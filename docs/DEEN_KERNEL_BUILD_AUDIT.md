# DeenRev 4.19.325 build audit

Base ref: `4.19.325-WIP`

## Verified source facts

- Kernel Makefile declares Linux 4.19.325.
- Target configuration exists at `arch/arm64/configs/vendor/deen_defconfig`.
- `build.sh` compiles `deen` with the external Clang 18 toolchain.
- The script fetches BakaSU from `Baka-SU/BakaSU` when `-ksu` is passed.
- The companion installer is `SHANDZIN/AnyKernel3`, branch `deen`, with device identifiers `deen` and `deen_sprout`.

## Known blockers before a SUSFS-enabled build

- Current `build.sh` explicitly disables `KSU_SUSFS` with `-d KSU_SUSFS`.
- Current Deen defconfig does not contain SUSFS configuration options.
- Kernel tree does not currently include the SUSFS kernel-side files (`fs/susfs.c`, `include/linux/susfs.h`, `include/linux/susfs_def.h`).
- Current build script prompts interactively for ZIP upload and optional header build; the workflow responds `n` to keep CI non-interactive.
- BakaSU default is resolved from a configurable ref; use an immutable commit SHA for a repeatable final build.
- Current GitLab CI file is a generic upstream kernel pipeline, not a project-specific Android/Deen packaging workflow.

## Workflow scope

`.github/workflows/deen-kernel-build.yml` is a baseline build workflow. It verifies the 4.19.325 source, can build the current configuration with or without external BakaSU, packages via the existing Deen AnyKernel3 branch, and uploads artifacts. It does **not** claim SUSFS 2.3 or NoMount support.

## Next work items

1. Pin the desired BakaSU commit and matching manager APK/release.
2. Choose and port a SUSFS 2.3.x kernel patch set that explicitly supports the 4.19 codebase and BakaSU's expected Kconfig/hooks.
3. Review unmount/NoMount requirements separately; NoMount is a userspace metamodule and is not automatically implied by a kernel `umount` option.
4. Compile with SUSFS features enabled, inspect the final `.config`, and verify the resulting ZIP before device testing.
