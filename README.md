# Android Kernel Intelligence Platform (AKIP)

Android Kernel Intelligence Platform (AKIP) is an automated system designed for precise, surgical patching, testing, building, and tracking of Android Linux kernels. The platform is modularized into several components linked as Git submodules in this repository:

- [akip-core](https://github.com/Android-Kernel-Intelligence/akip-core): Python-based kernel patching engine (Anchor matching, token context fallback, and atomic rollback).
- [akip-api](https://github.com/Android-Kernel-Intelligence/akip-api): Serverless REST API built with Hono.js running on Cloudflare Workers, integrated with Supabase and Cloudflare KV.
- [akip-worker](https://github.com/Android-Kernel-Intelligence/akip-worker): GitHub Actions workflow automation for kernel compilations with Clang/LLVM and AnyKernel3 packaging.
- [akip-web](https://github.com/Android-Kernel-Intelligence/akip-web): React, TypeScript, and Vite frontend dashboard for submission and real-time build tracking.

---

## Getting Started

### 1. Cloning the Repository
Since this project uses Git submodules to pull all the platform components together, you must clone the repository recursively:

```bash
git clone --recursive <repository-url>
```

If you have already cloned the repository without submodules, run the following command to initialize and update them:

```bash
git submodule update --init --recursive
```

### 2. Component Layout & Documentation

| Component | Path | Description |
| --- | --- | --- |
| **Core Engine** | [akip-core](https://github.com/Android-Kernel-Intelligence/akip-core) | Python CLI and core engine executing AST-like and anchor-based regex patching on Linux kernel source trees. |
| **Serverless API** | [akip-api](https://github.com/Android-Kernel-Intelligence/akip-api) | Cloudflare Worker microservice tracking manifests, compatibility analysis, and build statuses. |
| **Worker / CI** | [akip-worker](https://github.com/Android-Kernel-Intelligence/akip-worker) | CI/CD build scripts designed to automate target cloning, patching with `akip-core`, compilation, and release packaging. |
| **Frontend Web** | [akip-web](https://github.com/Android-Kernel-Intelligence/akip-web) | Web interface offering real-time build status tracking, manifest viewing, and patch application forms. |

---

## Component Workflows

### Patching Engine (`akip-core`)
The patching engine uses precise signature-based matching for inserting code blocks:
- **Anchor Matcher**: Targets functions (`signature_pattern`) and positions injections (e.g., `after_open_brace`, `before_close_brace`).
- **Context Matcher**: Uses Jaccard token similarity over sliding windows to apply patches as fallback.
- **Rollback System**: Creates atomic LIFO backups of modified files and rolls back all patches if a single stage fails.

### CI Build Pipeline (`akip-worker`)
Build workers run via GitHub Actions:
- Triggered by `workflow_dispatch` containing the build metadata (`MANIFEST_ID`, `API_URL`).
- Pulls kernel source, applies patches via `akip-core`, compiles with Clang/LLVM, packages boot images using `AnyKernel3`, and publishes results back to the Cloudflare Worker API.

### Dashboard & API (`akip-web` & `akip-api`)
- Users configure patches and submit target repositories through the frontend dashboard.
- The Hono.js API persists builds and reports via Supabase and caches hot statuses in Cloudflare KV.
- Real-time build status updates are pushed from CI to the API and streamed to the dashboard.

---

## License

This project is licensed under the MIT License. See the [LICENSE](file:///Users/mubasharhussain/Developer/Custom%20Tools/akip/LICENSE) file for details.
