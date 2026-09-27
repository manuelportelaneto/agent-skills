---
name: firebase-fullstack-pro
description: Production Firebase development covering Firestore data modeling, complex Security Rules, Cloud Functions v2, Auth custom claims, Storage, and Emulator Suite testing.
metadata:
  model: inherit
---

## Use this skill when

- Designing or optimizing Firestore NoSQL data schemas, indexes, and queries.
- Writing, testing, or auditing Firebase Security Rules for Firestore and Storage.
- Developing Firebase Cloud Functions v2 (HTTP callable, background triggers, blocking auth functions).
- Managing Firebase Authentication, custom claims, and multi-tenant user access control.
- Setting up Firebase Local Emulator Suite for deterministic offline testing.
- Integrating Firebase with frontend frameworks (React, Next.js, Flutter) or Node.js backends.

## Do not use this skill when

- The project relies purely on relational SQL databases (PostgreSQL, MySQL) without Firebase.
- Simple client-only apps that do not utilize Firebase services.

## Instructions

- Never rely solely on client-side validation; enforce constraints rigorously in Firebase Security Rules.
- Avoid unbounded Firestore queries; always specify limits and design schemas for shallow reads.
- Use Cloud Functions v2 with explicit concurrency and memory allocations to minimize cold starts.

---

## 1. Firestore Data Modeling Best Practices

Firestore is a document-oriented database optimized for scale and reads.

### Subcollections vs. Root Collections
- **Use Subcollections** when data is strictly owned by the parent document and deleted when the parent is deleted (e.g. `/users/{userId}/notifications/{notifId}`).
- **Use Root Collections with Foreign Keys** when data must be queried across multiple parents without collection-group query limitations (e.g. `/orders` with field `userId: "xyz"`).

### High-Write Rate Distributed Counter
Firestore limits write throughput on a single document to $\sim 1\text{ write/second}$. For high-velocity counters (e.g. post likes, live views), use distributed shards:

```typescript
// Incrementing a distributed counter across 10 shards
import { doc, updateDoc, increment } from 'firebase/firestore'

export async function incrementDistributedCounter(db: any, postId: string) {
  const shardId = Math.floor(Math.random() * 10).toString()
  const shardRef = doc(db, 'posts', postId, 'shards', shardId)
  await updateDoc(shardRef, { count: increment(1) })
}
```

---

## 2. Production Firebase Security Rules

Write robust, maintainable rules using helper functions:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    // Helper functions
    function isAuthenticated() {
      return request.auth != null;
    }
    
    function isOwner(userId) {
      return isAuthenticated() && request.auth.uid == userId;
    }
    
    function hasRole(role) {
      return isAuthenticated() && request.auth.token.role == role;
    }

    function isValidUserPayload() {
      let data = request.resource.data;
      return data.keys().hasAll(['displayName', 'email', 'updatedAt'])
        && data.displayName is string
        && data.displayName.size() >= 2
        && data.email is string;
    }

    // Collection rules
    match /users/{userId} {
      allow read: if isAuthenticated();
      allow create: if isOwner(userId) && isValidUserPayload();
      allow update: if (isOwner(userId) || hasRole('admin')) && isValidUserPayload();
      allow delete: if hasRole('admin');
    }

    match /organizations/{orgId}/projects/{projectId} {
      allow read, write: if isAuthenticated() && 
        exists(/databases/$(database)/documents/organizations/$(orgId)/members/$(request.auth.uid));
    }
  }
}
```

---

## 3. Cloud Functions v2 & Blocking Triggers

Cloud Functions v2 run on Cloud Run, offering request concurrency and granular configuration:

```typescript
// functions/src/index.ts
import { onCall, HttpsError } from 'firebase-functions/v2/https'
import { beforeUserCreated } from 'firebase-functions/v2/identity'
import * as admin from 'firebase-admin'

admin.initializeApp()

// 1. Identity Platform Blocking Trigger
export const checkSignupAllowed = beforeUserCreated((event) => {
  const user = event.data
  if (user.email && !user.email.endsWith('@company.com')) {
    throw new HttpsError('invalid-argument', 'Only internal company emails are permitted.')
  }
  // Automatically assign custom claims
  return {
    customClaims: { role: 'employee' }
  }
})

// 2. High-Performance Callable Function with Concurrency
export const processOrder = onCall(
  {
    concurrency: 80,
    memory: '512MiB',
    timeoutSeconds: 30,
    cors: ['https://app.company.com']
  },
  async (request) => {
    if (!request.auth) {
      throw new HttpsError('unauthenticated', 'User must be authenticated')
    }

    const { orderId } = request.data
    const orderRef = admin.firestore().doc(`orders/${orderId}`)
    
    return await admin.firestore().runTransaction(async (transaction) => {
      const snap = await transaction.get(orderRef)
      if (!snap.exists) throw new HttpsError('not-found', 'Order does not exist')
      
      transaction.update(orderRef, { status: 'CONFIRMED', processedAt: admin.firestore.FieldValue.serverTimestamp() })
      return { success: true }
    })
  }
)
```

---

## 4. Local Emulator Suite Setup

Run tests completely offline with zero cloud charges:

```bash
# Start emulators with saved seed data
firebase emulators:start --import=./emulator-data --export-on-exit

# Run unit tests against the emulator
FIRESTORE_EMULATOR_HOST="127.0.0.1:8080" \
FIREBASE_AUTH_EMULATOR_HOST="127.0.0.1:9099" \
npm test
```

---

## 5. Anti-Patterns to Avoid

- **No Secrets in Client Builds**: Never commit Firebase service account keys to frontend repositories. Only client configuration (API keys, project IDs) are public.
- **No Unindexed Large Queries**: Always deploy composite index definitions in `firestore.indexes.json`.
- **No Direct Document Overwrite**: Use `updateDoc` instead of `setDoc` without merge, unless an entire document replacement is intentionally desired.
