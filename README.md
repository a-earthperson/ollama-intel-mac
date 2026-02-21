# Ollama (MoltenVK macOS Fork)

This repository is a manually maintained fork of `ollama/ollama`,
based on work by [@jamfor999](https://github.com/jamfor999/ollama), with one primary goal:
enable and maintain a Vulkan/MoltenVK path for Intel Macs (especially Intel + AMD dGPU
systems) where the upstream default Metal-centric path is not always ideal.

If you want stock Ollama behavior, use upstream:
- https://github.com/ollama/ollama
- https://ollama.com

## Why this fork exists

Upstream moves quickly and optimizes for broad product goals. This fork intentionally
prioritizes:

1. A reproducible macOS build path with Vulkan enabled through MoltenVK.
2. Practical compatibility for Intel Mac hardware, including AMD discrete GPUs.
3. Tight control over backend and build-system changes that affect this target.

This is not a drop-in replacement for every upstream workflow. It is a focused
engineering fork with explicit trade-offs.

## Scope and support policy

- **Primary target**: macOS builds that include Vulkan via MoltenVK.
- **Best-effort target**: Apple Silicon (works, but this fork is not tuned for it).
- **Non-goal**: mirroring every upstream feature/documentation section in this README.
- **Support model**: manual maintenance; no SLA; expect occasional divergence.

## High-level differences from upstream

- Adds/maintains Vulkan-related build and runtime guards needed for macOS/MoltenVK.
- Maintains a dedicated Darwin Vulkan build script: `scripts/build_darwin_vulkan.sh`.
- Produces `Ollama.app` bundles with Vulkan backend assets integrated.
- Installs the built app to `/Applications/Ollama.app` in the Vulkan macOS app build flow.

## Prerequisites (macOS build host)

Install required toolchain packages:

```bash
brew install cmake go shaderc glslang libomp typescript
```

You also need:
- Xcode Command Line Tools
- Node.js + npm

Verify critical commands:

```bash
cmake --version
go version
glslc --version
npm --version
```

## Build the forked macOS app (MoltenVK path)

From repository root:

```bash
./scripts/build_darwin_vulkan.sh
```

This pipeline performs the following:

1. Builds Ollama binaries for Darwin targets.
2. Builds/installs Vulkan backend components via MoltenVK presets.
3. Produces `dist/Ollama.app` with backend resources embedded.
4. Attempts installation to `/Applications/Ollama.app` (with `sudo` fallback if needed).

Expected artifacts include:
- `dist/Ollama.app`
- `dist/ollama-darwin.tgz`
- `dist/*.zip` app bundle archives

## Build script entrypoints

The script supports subcommands:

```bash
./scripts/build_darwin_vulkan.sh build
./scripts/build_darwin_vulkan.sh sign
./scripts/build_darwin_vulkan.sh app
```

No arguments runs `build + sign + app` in sequence.

## Runtime notes

- On Intel + AMD systems, this fork is intended to favor Vulkan/MoltenVK viability.
- On Apple Silicon, performance may be worse than upstream Metal-first distributions.
- Vulkan capability depends on driver/runtime feature availability and shader toolchain
  compatibility; support is not binary across all devices.

## Manual maintenance model

This branch is maintained by selectively integrating upstream changes and carrying
fork-specific patches where necessary.

Suggested maintenance discipline:

1. Rebase/merge upstream regularly.
2. Re-validate Vulkan compile-time guards and feature probes after upstream ggml changes.
3. Re-run full macOS app build after each sync.
4. Keep fork-specific README/build notes current when scripts or backend behavior changes.

## Quick sanity test after build

After app build/install:

```bash
/Applications/Ollama.app/Contents/Resources/ollama --version
```

Then launch the app and run a basic inference smoke test from CLI:

```bash
ollama run gemma3
```

## Upstream docs you still want

For general Ollama API/CLI/model usage, prefer upstream docs:
- https://docs.ollama.com

For upstream source/development reference:
- https://github.com/ollama/ollama
