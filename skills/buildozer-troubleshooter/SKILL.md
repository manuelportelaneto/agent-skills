# Buildozer Troubleshooting Expert Skill

This skill provides advanced strategies for resolving Kivy/Buildozer compilation errors, specifically in heterogeneous environments (e.g., building for Android ARM64 on an ARM64 Linux host like an Oracle VM).

## 1. Environment Architecture Mismatch (ARM64 Host)
- **Problem**: Android NDK binaries (clang, ld) are typically x86_64 for Linux. Running them on aarch64 fails with `cannot open shared object file` or `exec format error`.
- **Solution**: 
    - Use a native `linux-aarch64` NDK (e.g., from community builds like SnowNF).
    - Substitute the `linux-x86_64` folder inside the NDK with a symlink to `linux-aarch64`.
    - Set `android.ndk_path` in `buildozer.spec`.

## 2. Autotools / Libffi Macro Failures
- **Problem**: `possibly undefined macro: AC_PROG_LIBTOOL` or `LT_SYS_SYMBOL_USCORE`.
- **Solution**:
    - Build and install `autoconf`, `automake`, and `libtool` into a local path.
    - Export `ACLOCAL_PATH` to include standard snap or system m4 directories (e.g., `/usr/share/aclocal`).
    - Patch the `libffi` recipe in P4A to skip `autoreconf` if `configure` already exists.

## 3. Java and SDK Issues
- **Problem**: `JAVA_HOME` not found or SDK download hangs.
- **Solution**:
    - Explicitly set `JAVA_HOME` to the openjdk path (e.g., `/usr/lib/jvm/java-21-openjdk-arm64`).
    - Ensure `android.api` matches a version already downloaded or accessible.

## 4. Pygame / Performance (Native vs Web)
- **Problem**: Pygbag uses an `async` loop, while native Android via Buildozer uses a sync loop.
- **Solution**:
    - Use a dual-entry point in `main.py`.
    - Reserved `asyncio.run(main())` for `__name__ == "__main__"` (Web/Pygbag).
    - Explicitly call `Jogo().run_sync()` for native builds.

## 5. UI Assets (Icons & Splash)
- **Problem**: Black borders or stretching.
- **Solution**:
    - Icon should be a full 1024x1024 square with no transparency padding.
    - Splash screen should be vertical (e.g., 1080x1920) for standard mobile orientation.
