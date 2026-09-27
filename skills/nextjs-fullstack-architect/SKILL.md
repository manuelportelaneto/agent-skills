---
name: nextjs-fullstack-architect
description: Master modern Next.js 15+ App Router, React Server Components (RSC), Server Actions, Suspense streaming, Core Web Vitals, TanStack Query, and scalable frontend architectures.
metadata:
  model: inherit
---

## Use this skill when

- Architecting, building, or refactoring fullstack applications with Next.js 15+ (App Router).
- Designing React Server Components (RSC) and Client Component boundaries.
- Implementing secure Server Actions with validation, authentication, and optimistic updates.
- Optimizing Core Web Vitals (LCP, INP, CLS) and frontend performance.
- Integrating TanStack Query, Zustand, or URL state for complex client-side interactions.
- Building accessible, scalable UI design systems with Tailwind CSS and Radix UI / shadcn/ui.

## Do not use this skill when

- The project uses legacy Next.js Pages Router (use standard React skills unless migrating).
- The project is pure static HTML/Vanilla JS without modern React ecosystems.

## Instructions

- Default to React Server Components (RSC); only add `'use client'` at leaf interactive components.
- Never trust client inputs in Server Actions: validate payloads with Zod and verify authorization on every invocation.
- Implement progressive enhancement and streaming with Suspense boundaries to maximize Core Web Vitals.

---

## 1. App Router Architecture & Component Boundaries

Structure directory layers to keep server logic separated and component boundaries clean:

```
src/
├── app/
│   ├── (auth)/             # Route groups (clean URLs)
│   ├── (dashboard)/
│   │   ├── layout.tsx      # Persistent layout with server-fetched user data
│   │   ├── loading.tsx     # Instant loading skeleton
│   │   ├── error.tsx       # Error boundary (must be 'use client')
│   │   └── page.tsx        # RSC page fetching data in parallel
│   ├── api/                # Route handlers only for external webhooks/REST
│   ├── globals.css
│   └── layout.tsx          # Root layout with fonts, metadata, providers
├── components/
│   ├── ui/                 # Reusable atomic UI (shadcn / Radix)
│   └── features/           # Feature-bound composite components
├── actions/                # Server Actions (colocated or centralized)
├── lib/
│   ├── auth.ts             # Auth utilities (Auth.js / Supabase / Firebase)
│   ├── db.ts               # Database clients
│   └── validations.ts      # Shared Zod schemas
└── hooks/                  # Custom client hooks
```

### Server vs. Client Component Rules
- **Server Components (Default)**: Use for data fetching, accessing backend resources directly, keeping large dependencies/tokens on the server, and reducing client bundle size.
- **Client Components (`'use client'`)**: Push as deep as possible in the component tree. Use only for interactivity (`onClick`, `onChange`), browser APIs (`window`, `localStorage`), state (`useState`, `useReducer`), and effects (`useEffect`).

---

## 2. Secure Server Actions Pattern

Never treat Server Actions as simple internal functions; they expose public POST endpoints.

```typescript
// src/actions/project-actions.ts
'use server'

import { z } from 'zod'
import { revalidatePath } from 'next/cache'
import { auth } from '@/lib/auth'
import { db } from '@/lib/db'

const CreateProjectSchema = z.object({
  name: z.string().min(3).max(50),
  description: z.string().max(250).optional(),
})

export type ActionResponse<T> = {
  data?: T
  error?: string
}

export async function createProjectAction(
  prevState: unknown,
  formData: FormData
): Promise<ActionResponse<{ id: string }>> {
  // 1. Authenticate user
  const session = await auth()
  if (!session?.user?.id) {
    return { error: 'Unauthorized: You must be logged in' }
  }

  // 2. Validate input
  const parsed = CreateProjectSchema.safeParse({
    name: formData.get('name'),
    description: formData.get('description'),
  })

  if (!parsed.success) {
    return { error: parsed.error.issues[0].message }
  }

  try {
    // 3. Perform mutation
    const project = await db.project.create({
      data: {
        ...parsed.data,
        userId: session.user.id,
      },
    })

    // 4. Revalidate cache
    revalidatePath('/dashboard/projects')
    return { data: { id: project.id } }
  } catch (err) {
    return { error: 'Failed to create project. Please try again.' }
  }
}
```

---

## 3. Streaming & Suspense Data Fetching

Avoid blocking the entire page on slow queries. Stream slow parts via Suspense:

```tsx
// src/app/(dashboard)/analytics/page.tsx
import { Suspense } from 'react'
import { MetricsOverview } from '@/components/features/metrics-overview'
import { SlowDetailedChart } from '@/components/features/slow-chart'
import { MetricsSkeleton, ChartSkeleton } from '@/components/ui/skeletons'

export default async function AnalyticsPage() {
  return (
    <div className="space-y-6 p-8">
      <h1 className="text-3xl font-bold tracking-tight">Analytics Dashboard</h1>
      
      {/* Fast Component */}
      <Suspense fallback={<MetricsSkeleton />}>
        <MetricsOverview />
      </Suspense>

      {/* Slower Component streamed independently */}
      <Suspense fallback={<ChartSkeleton />}>
        <SlowDetailedChart />
      </Suspense>
    </div>
  )
}
```

---

## 4. Core Web Vitals & Performance Optimization

| Metric | Target | Next.js Implementation Strategy |
| :--- | :--- | :--- |
| **LCP** (Largest Contentful Paint) | $\le 2.5\text{s}$ | Use `next/image` with `priority` for above-the-fold hero images; use `next/font/google` with `display: 'swap'`; stream non-critical UI. |
| **INP** (Interaction to Next Paint) | $\le 200\text{ms}$ | Minimize client-side bundle size; avoid synchronous blocking tasks on main thread; use `useTransition` for non-urgent UI transitions. |
| **CLS** (Cumulative Layout Shift) | $\le 0.1$ | Always specify `width` and `height` (or aspect ratio) on images; avoid inserting dynamic banners without reserving layout space. |

---

## 5. Anti-Patterns to Avoid

- **No `'use client'` at the top of pages**: Adding `'use client'` to `page.tsx` or `layout.tsx` disables server-side streaming and forces the entire sub-tree into the client bundle.
- **No unauthenticated Server Actions**: Assuming Server Actions are protected because they are called from an authenticated view.
- **No `useEffect` for initial data fetching**: Use RSC async fetches directly or TanStack Query with prefetching in RSC.
- **No Waterfall data loading**: Fetch independent queries in parallel using `Promise.all([fetchA(), fetchB()])`.
