# Awesome Open-Source Sandbox [![Awesome](https://awesome.re/badge.svg)](https://awesome.re) [![License: CC0-1.0](https://img.shields.io/badge/License-CC0_1.0-lightgrey.svg)](http://creativecommons.org/publicdomain/zero/1.0/)

**English** | **[简体中文](./README_zh.md)**

A curated, full-stack directory of **open-source sandboxes, isolated execution platforms, and underlying virtualization/containment runtimes** for AI Agents, Code Interpreters, Reinforcement Learning (RL) environments, and untrusted workload execution.

> **Inclusion Principle**: Every repository listed here is **actively maintained (unarchived)** and ships **real open-source runtime or platform code** that you can inspect, audit, and self-host. Purely closed-source SaaS products without open-source core runtimes are intentionally excluded unless their self-hostable engine or SDK runtime is open-sourced.

---

## Contents

- [Understanding the Sandbox Isolation Stack](#understanding-the-sandbox-isolation-stack)
- [Cloud-Native & Distributed AI Agent Sandbox Platforms](#cloud-native--distributed-ai-agent-sandbox-platforms)
- [Local-First & Embeddable Agent MicroVM Sandboxes](#local-first--embeddable-agent-microvm-sandboxes)
- [Browser, Desktop GUI & Computer-Use Sandboxes](#browser-desktop-gui--computer-use-sandboxes)
- [Code Interpreters, Multi-Language Judges & RL Execution Engines](#code-interpreters-multi-language-judges--rl-execution-engines)
- [WebAssembly & Language-Level Sandboxes](#webassembly--language-level-sandboxes)
- [MicroVM Hypervisors, Unikernels & VM Runtimes](#microvm-hypervisors-unikernels--vm-runtimes)
- [Application Kernels, System Containers & OS Process Sandboxes](#application-kernels-system-containers--os-process-sandboxes)
- [MCP Sandbox Servers, Security & Taxonomy](#mcp-sandbox-servers-security--taxonomy)
- [Architecture Selection Guide](#architecture-selection-guide)
- [Contributing](#contributing)

---

## Understanding the Sandbox Isolation Stack

Choosing a sandbox involves balancing **security boundary strength**, **cold-start latency**, **POSIX/Linux compatibility**, and **state persistence**:

| Isolation Layer | Representative Engines | Security Boundary | Cold Start | POSIX / OS Compatibility | Typical Workload |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Hardware MicroVM** | Firecracker, Kata Containers, Cloud Hypervisor, `libkrun` | Dedicated Guest Kernel via KVM / Hypervisor.framework (**Strongest**) | `~50ms–1s` (`<50ms` via WarmPool / Snapshots) | Full Linux OS (`apt`, `systemd`, Docker, raw block) | Multi-tenant AI agents, autonomous SWE/DevOps, full-OS persistence |
| **User-Space App Kernel** | gVisor (`runsc`) | User-space syscall interception (`Sentry`), no host syscall passthrough | `~100–300ms` | High Linux syscall coverage (some `ioctl`/perf limits) | Kubernetes pods without bare-metal KVM / nested virtualization |
| **Hardened / System Containers** | Sysbox, Youki, OCI + Seccomp | Shared host Linux kernel + User/PID/Net namespaces + Seccomp-BPF | `~50–200ms` | Native Linux binaries & container images | Trusted-tenant CI/CD, single-tenant RL rollouts, Docker-in-Docker |
| **WebAssembly / Language VM** | Wasmtime, Wasmer, Pyodide, Monty, QuickJS | Capability-based linear memory & instruction sandbox | `<1ms–20ms` | WASI subset or single-language stdlib | High-density tool calls, inline Python/JS snippets, edge workers |
| **OS Process Sandbox** | Anthropic `srt`, Bubblewrap, NsJail, Firejail, Landlock | Host OS process wrapper (`seccomp`, `namespaces`, macOS Seatbelt) | `<5ms` | Host-native CLI tools without container daemon | Wrapping local coding agents (Claude Code, Codex) on developer laptops |

---

## Cloud-Native & Distributed AI Agent Sandbox Platforms

*Full-lifecycle orchestration platforms providing APIs, SDKs, warm pools, persistence, networking, and cluster scheduling for AI agents.*

- **[OpenSandbox](https://github.com/opensandbox-group/OpenSandbox)** `Go` `Apache-2.0` — General-purpose sandbox platform for AI applications offering multi-language SDKs, unified sandbox APIs, and pluggable Docker / Kubernetes / secure container runtimes.
- **[E2B](https://github.com/e2b-dev/E2B)** & **[E2B Runtime](https://github.com/e2b-dev/runtime)** `Go / Python / TS` `Apache-2.0` — Open-source Firecracker MicroVM infrastructure, orchestrator, and Python/JS [Code Interpreter SDKs](https://github.com/e2b-dev/code-interpreter) for running AI-generated code and enterprise agents with snapshot-based fast resume.
- **[CubeSandbox](https://github.com/TencentCloud/CubeSandbox)** `Go` `Custom OSS` — Instant, high-concurrency, lightweight sandbox runtime for AI agents engineered by Tencent Cloud with sub-second startup and multi-tenant isolation.
- **[CubePod](https://github.com/cubeplexai/cubepod)** `Go / Rust` `Apache-2.0` — Persistent Writable RootFS MicroVM execution platform on Kubernetes (Kata Containers + Dual CSI Raw Block PVCs). Decouples persistent OS `upperdir` (`apt`/`pip`/`npm`) from `/workspace`, supports `<50ms` `PodWarmPool` claims, zero-export suspend (`fsfreeze` to 0 CPU/RAM) with `~1s` cross-node resume, `<1s` CoW sandbox forking, zero-trust `cpodref_` mTLS egress secret substitution, and 100% E2B wire compatibility.
- **[kubernetes-sigs/agent-sandbox](https://github.com/kubernetes-sigs/agent-sandbox)** `Go` `Apache-2.0` — Official Kubernetes SIG project providing declarative CRDs and controllers (`Sandbox`, `SandboxWarmPool`, `SandboxClaim`) to manage isolated, stateful, singleton workloads for AI agent runtimes and RL on Kubernetes.
- **[Sandstorm](https://github.com/deeplethe/sandstorm)** `Rust` `Apache-2.0` — High-density sandbox orchestrator built in Rust designed to pack and run thousands of isolated AI agent sandboxes on commodity hardware.
- **[SPACE](https://github.com/perplexityai/SPACE)** `Rust` `Apache-2.0` — Perplexity AI's *Sandboxed Platform for Agentic Code Execution*, providing pluggable-runtime sandboxes for executing untrusted agent and user code in strict isolation.
- **[Beta9](https://github.com/beam-cloud/beta9)** `Go` `AGPL-3.0` — Self-hostable serverless compute engine powering Beam Cloud, featuring ultrafast container/sandbox cold starts, distributed storage, background queues, and GPU inference.
- **[Cloudflare Sandbox SDK](https://github.com/cloudflare/sandbox-sdk)** `TypeScript` `Apache-2.0` — Official SDK and Durable Objects orchestration layer for running isolated container sandboxes, shell commands, code interpreters, and preview URLs on Cloudflare's edge.
- **[Vercel Sandbox](https://github.com/vercel/sandbox)** `TypeScript` `Apache-2.0` — Open-source SDK and ephemeral compute primitive interface designed to safely run untrusted or LLM-generated code in isolated environments.

---

## Local-First & Embeddable Agent MicroVM Sandboxes

*Developer-friendly MicroVM tools and embeddable libraries designed to isolate coding agents (Claude Code, Codex, SWE-agent) on laptops or single hosts without complex cluster setup.*

- **[Microsandbox](https://github.com/superradcompany/microsandbox)** `Rust` `Apache-2.0` — Programmable, local-first MicroVM runtime and library (`libkrun`-backed) offering OCI image compatibility, instant startup, and hardware-virtualized isolation with zero cloud lock-in.
- **[smolvm](https://github.com/smol-machines/smolvm)** `Rust` `Apache-2.0` — Embeddable, portable, and branchable lightweight virtual machine designed specifically to run and checkpoint local AI agents safely.
- **[ArcBox](https://github.com/arcboxlabs/arcbox)** `Rust` `Apache-2.0` — Pure-Rust, local-first machine runtime that boots OCI-compatible isolated VMs (dedicated kernel, filesystem, and network) in `<100ms` for AI agents.
- **[BoxLite](https://github.com/boxlite-ai/boxlite)** `Rust / TypeScript` `Apache-2.0` — Embeddable MicroVM engine for AI agents—lightweight enough to embed directly inside desktop apps and elastic enough for cloud deployment.
- **[Gondolin](https://github.com/earendil-works/gondolin)** `Rust / TypeScript` `Apache-2.0` — Linux MicroVM agent sandbox featuring a programmable TypeScript control plane, virtualized network stack, and transparent HTTP/TLS egress interception so credentials never enter the guest VM.
- **[Clawk](https://github.com/clawkwork/clawk)** `Go` `Apache-2.0` — CLI and daemon that gives terminal coding agents a disposable, isolated Linux VM rather than unrestricted access to your host laptop.
- **[Shuru](https://github.com/superhq-ai/shuru)** `Rust` `Apache-2.0` — Local-first MicroVM sandbox for running autonomous AI coding agents safely across macOS and Linux.
- **[Arrakis](https://github.com/abshkbh/arrakis)** `Go / Python` `AGPL-3.0` — Self-hosted MicroVM sandbox for AI code execution and computer use with built-in snapshot backtracking for multi-step agent tree search, REST API, and automatic port forwarding.
- **[Microsoft NVX](https://github.com/microsoft/nvx)** `Rust` `MIT` — Cross-platform MicroVM sandbox engineered in Rust for isolating agentic workloads across host operating systems.
- **[Coop](https://github.com/trailofbits/coop)** `Rust` `Apache-2.0` — Security-hardened isolated VM environment by Trail of Bits purpose-built for running autonomous coding agents like Claude Code and Codex.

---

## Browser, Desktop GUI & Computer-Use Sandboxes

*Isolated headless browsers, WebRTC virtual desktops, and cross-OS GUI environments enabling web automation and human-like computer use.*

- **[Cua (Computer-Use Agents)](https://github.com/trycua/cua)** `Rust / Python` `MIT` — Open-source computer-use infrastructure providing high-performance macOS (`Lume` / Apple Virtualization.framework), Linux, and Windows sandboxes, unified GUI drivers, and evaluation benchmarks.
- **[Neko](https://github.com/m1k1o/neko)** `Go / TypeScript` `Apache-2.0` — Self-hosted virtual browser and desktop environment running inside Docker and streamed with ultra-low latency via WebRTC—widely used for human-in-the-loop agent takeover.
- **[Steel Browser](https://github.com/steel-dev/steel-browser)** `TypeScript` `Apache-2.0` — Batteries-included open-source browser sandbox API for AI agents featuring session management, Playwright/Puppeteer/CDP attachment, proxy rotation, and anti-bot resilience.
- **[AIO Sandbox (`agent-infra/sandbox`)](https://github.com/agent-infra/sandbox)** `Python / Go` `Apache-2.0` — All-in-One agent sandbox container uniting Chromium Browser (CDP + VNC), Shell terminal, File API, VSCode Server, and MCP tools behind a single endpoint.
- **[Playwriter](https://github.com/remorses/playwriter)** `TypeScript` `MIT` — CLI and MCP server that executes Playwright automation snippets inside a stateful sandboxed environment for browser-controlling agents.
- **[Selkies](https://github.com/selkies-project/selkies)** `Rust / Python` `MPL-2.0` — Low-latency, hardware-accelerated WebRTC remote desktop streaming platform built for containerized and Kubernetes GUI sandboxes.
- **[Open Computer Use](https://github.com/e2b-dev/open-computer-use)** `Python` `Apache-2.0` — Reference desktop agent environment combining open-source vision-language models with isolated Linux desktop sandboxes (X11/VNC/mouse/keyboard control).
- **[Kernel Images](https://github.com/kernel/kernel-images)** `Go` `Apache-2.0` — Open-source unikernel/container browser runtime images powering sub-second Browsers-as-a-Service for web agents and Playwright automations.

---

## Code Interpreters, Multi-Language Judges & RL Execution Engines

*High-throughput execution sandboxes specialized for untrusted code evaluation, competitive programming judges, LLM RLVR training rollouts, and SWE-bench harnesses.*

- **[Judge0](https://github.com/judge0/judge0)** `Ruby / C` `GPL-3.0` — Battle-tested, scalable open-source online code execution system supporting 60+ programming languages (`isolate`-backed) for coding platforms and AI evaluators.
- **[Piston](https://github.com/engineer-man/piston)** `JavaScript / Rust` `MIT` — High-performance general-purpose multi-language code execution engine designed for rapid stateless untrusted snippet evaluation.
- **[Dify Sandbox](https://github.com/langgenius/dify-sandbox)** `Go` `Apache-2.0` — Lightweight, fast seccomp-enforced code execution service powering workflow code nodes (Python / Node.js) in Dify and LLM pipelines.
- **[LLM Sandbox](https://github.com/vndee/llm-sandbox)** `Python` `MIT` — Portable Python code-interpreter library supporting pluggable Docker, Kubernetes, Podman, and MicroVM backends with LangChain / LlamaIndex / MCP integrations.
- **[SandboxFusion](https://github.com/bytedance/SandboxFusion)** `Python` `Apache-2.0` — ByteDance's multi-language code sandbox tailored for LLM code generation benchmarks, RLVR reward verification, and massive parallel evaluation across 20+ languages and datasets.
- **[CodeRunner](https://github.com/instavm/coderunner)** `Python` `Apache-2.0` — Local multi-language execution sandbox for AI agents combining lightweight isolation with MCP compatibility (macOS Apple Containers / Docker).
- **[SWE-ReX](https://github.com/SWE-agent/SWE-ReX)** `Python` `MIT` — Massively parallel sandboxed runtime engine powering `SWE-agent`, enabling interactive shell sessions across local Docker, AWS Modal, or remote clusters with unified async APIs.

---

## WebAssembly & Language-Level Sandboxes

*Capability-based runtimes and memory-safe interpreters offering microsecond-to-millisecond cold starts without booting an operating system.*

- **[Wasmer](https://github.com/wasmerio/wasmer)** `Rust` `MIT` — High-performance WebAssembly runtime with WASIX POSIX extensions capable of running compiled apps, web servers, and lightweight AI agent sandboxes.
- **[Wasmtime](https://github.com/bytecodealliance/wasmtime)** `Rust` `Apache-2.0` — Bytecode Alliance's standards-compliant, security-first WebAssembly and WASI Component Model runtime with Cranelift JIT compilation and epoch/fuel interruption.
- **[Pyodide](https://github.com/pyodide/pyodide)** `Python / C` `MPL-2.0` — CPython compiled to WebAssembly/Emscripten with NumPy, Pandas, SciPy, and Matplotlib, enabling zero-OS Python data-science sandboxes in Node.js, Deno, Cloudflare Workers, or browsers.
- **[WasmEdge](https://github.com/WasmEdge/WasmEdge)** `C++` `Apache-2.0` — CNCF cloud-native WebAssembly runtime optimized for edge serverless functions, WASI-NN inference, and OCI container (`crun`/`containerd`) integration.
- **[Pydantic Monty](https://github.com/pydantic/monty)** `Rust` `MIT` — Minimal, ultra-fast secure Python subset interpreter written in Rust specifically to execute LLM-generated Python tool/glue code with microsecond startup, state snapshotting, and zero host access by default.
- **[Extism](https://github.com/extism/extism)** `Rust` `BSD-3-Clause` — Cross-language plug-in and sandboxing framework built over Wasmtime/Wasmer for safely executing untrusted modules inside host applications across 15+ host languages.
- **[AgentOS (`rivet-dev/agentos`)](https://github.com/rivet-dev/agentos)** `Rust` `Apache-2.0` — Tiny, fast, Linux-compatible virtualized sandboxes for AI agents powered by WebAssembly.
- **[QuickJS-NG](https://github.com/quickjs-ng/quickjs)** `C` `MIT` — Actively developed fork of Fabrice Bellard's small, embeddable ES2023+ JavaScript engine—frequently compiled to Wasm (e.g., [`@sebastianwessel/quickjs`](https://github.com/sebastianwessel/quickjs)) for sub-millisecond JS/TS code sandboxing.

---

## MicroVM Hypervisors, Unikernels & VM Runtimes

*Foundational Virtual Machine Monitors (VMMs), container-native hypervisors, and unikernels that power cloud and local MicroVM sandboxes.*

- **[Apple `container`](https://github.com/apple/container)** & **[`containerization`](https://github.com/apple/containerization)** `Swift` `Apache-2.0` — Apple's official CLI and Swift framework for running OCI Linux containers inside dedicated lightweight MicroVMs on Apple Silicon with sub-second boot times.
- **[Firecracker](https://github.com/firecracker-microvm/firecracker)** `Rust` `Apache-2.0` — AWS's battle-tested KVM Virtual Machine Monitor powering AWS Lambda, Fargate, and E2B—launching minimalist Linux MicroVMs in `~125ms` with `<5 MiB` memory overhead and jailer isolation.
- **[Lima](https://github.com/lima-vm/lima)** `Go` `Apache-2.0` — Linux virtual machines on macOS and Linux with automatic file sharing and port forwarding, widely used to host containerd/Docker/nerdctl isolation environments.
- **[Kata Containers](https://github.com/kata-containers/kata-containers)** `Rust / Go` `Apache-2.0` — CNCF standard OCI runtime (`containerd-shim-kata-v2`) wrapping Kubernetes Pods inside lightweight hardware-isolated VMs (QEMU, Cloud Hypervisor, Firecracker, Dragonball) with `virtio-fs` and direct `virtio-blk` CSI attachment.
- **[Cloud Hypervisor](https://github.com/cloud-hypervisor/cloud-hypervisor)** `Rust` `Apache-2.0 / BSD-3-Clause` — Rust-based KVM/MSHV Virtual Machine Monitor built on `rust-vmm` for modern cloud workloads, supporting CPU/memory/`virtio-blk` hotplug and `vhost-user` offload.
- **[Unikraft](https://github.com/unikraft/unikraft)** `C` `BSD-3-Clause` — Cloud-native unikernel SDK and build system achieving millisecond boot times and minimal memory footprints (`<2–10 MiB`) by compiling applications into specialized single-address-space kernels.
- **[libkrun](https://github.com/libkrun/libkrun)** `Rust / C` `Apache-2.0` — Dynamic C/Rust library providing KVM (Linux) and `Hypervisor.framework` (macOS) process-level MicroVM isolation (`crun --krun`, Microsandbox, Podman Machine).
- **[Kuasar](https://github.com/kuasar-io/kuasar)** `Rust` `Apache-2.0` — CNCF Sandbox unified multi-sandbox `containerd` runtime supporting MicroVMs (Cloud Hypervisor, QEMU, StratoVirt), App Kernels (Quark), and Wasm in a single low-overhead Rust daemon.
- **[NanoVMs Ops](https://github.com/nanovms/ops)** `Go / C` `MIT` — Tooling to package arbitrary Linux binaries into Nanos unikernel images with reduced attack surface (no shell, no `fork`/`exec` exploit chains).

---

## Application Kernels, System Containers & OS Process Sandboxes

*User-space kernels, hardened OCI runtimes, and zero-container OS sandboxes utilizing `seccomp-bpf`, Linux namespaces, Landlock LSM, and macOS Seatbelt.*

- **[gVisor](https://github.com/gvisor/gvisor)** `Go` `Apache-2.0` — Google's OCI-compatible application kernel (`runsc`) implementing a substantial portion of the Linux syscall surface in memory-safe Go user space—providing strong multi-tenant container isolation without requiring hardware KVM.
- **[Bubblewrap (`bwrap`)](https://github.com/containers/bubblewrap)** `C` `LGPL-2.0+` — Unprivileged low-level Linux sandboxing tool utilizing user/mount/network/PID namespaces and `seccomp` filters; powers Flatpak, Anthropic `srt` on Linux, and Codex CLI Linux sandboxing.
- **[Firejail](https://github.com/netblue30/firejail)** `C` `GPL-2.0` — SUID Linux sandbox program reducing the risk of security breaches by restricting untrusted processes via namespaces, AppArmor, and `seccomp-bpf` profiles.
- **[Youki](https://github.com/youki-dev/youki)** `Rust` `Apache-2.0` — Fast, memory-safe OCI container runtime (`runc` alternative) written in Rust, eliminating `CVE-2019-5736`-style C memory safety pitfalls in container lifecycle bootstrap.
- **[Anthropic Sandbox Runtime (`srt`)](https://github.com/anthropics/sandbox-runtime)** `TypeScript` `Apache-2.0` — Lightweight OS-level process sandboxing CLI & library (`sandbox-exec` on macOS, `bubblewrap` on Linux) enforcing fine-grained filesystem read/write boundaries and proxy-mediated domain allowlists without Docker.
- **[Nono](https://github.com/nolabs-ai/nono)** `Rust` `Apache-2.0` — Zero-setup, zero-latency OS-level runtime sandbox (Landlock LSM on Linux + Seatbelt on macOS) enforcing capability-based filesystem, network, and credential guards for AI agents.
- **[NsJail](https://github.com/google/nsjail)** `C++` `Apache-2.0` — Google's lightweight Linux process isolation utility combining namespaces, `cgroups` v1/v2, `rlimits`, and expressive Kafel `seccomp-bpf` policy DSLs.
- **[Sysbox](https://github.com/nestybox/sysbox)** `Go / C` `Apache-2.0` — Next-generation OCI runtime (`runc` fork acquired by Docker) enabling rootless system containers to safely run `systemd`, Docker, and Kubernetes inside containers via user-namespace and `/proc`/`/sys` virtualization.
- **[Isolate](https://github.com/ioi/isolate)** `C` `GPL-2.0` — Linux `cgroups` and namespace sandbox built for the International Olympiad in Informatics (IOI) to reliably measure CPU/memory usage and prevent side-channel leaks in untrusted code evaluation.
- **[libseccomp](https://github.com/seccomp/libseccomp)** `C` `LGPL-2.1` & **[go-landlock](https://github.com/landlock-lsm/go-landlock)** `Go` `MIT` — Foundational libraries for building custom Linux syscall filters (`seccomp-bpf`) and unprivileged filesystem/network access-control sandboxes (`Landlock LSM`).

---

## MCP Sandbox Servers, Security & Taxonomy

*Model Context Protocol (MCP) sandbox adapters, governance frameworks, and security evaluation suites.*

- **[Microsoft Agent Governance Toolkit](https://github.com/microsoft/agent-governance-toolkit)** `Python` `MIT` — Policy enforcement, zero-trust agentic identity, execution sandboxing, and OWASP Agentic Top 10 security controls for autonomous AI agents.
- **[Windows Sandbox MCP](https://github.com/shanselman/sandbox-mcp)** `JavaScript` `MIT` — MCP server and CLI automating disposable Windows Sandbox VMs for testing Windows binaries and desktop apps safely from AI agents.
- **[Code Sandbox MCP](https://github.com/Automata-Labs-team/code-sandbox-mcp)** `Go` `MIT` — MCP server provisioning isolated container environments to execute arbitrary LLM-generated code, manage files, and install packages on demand.
- **[The Agent Sandbox Taxonomy](https://github.com/kajogo777/the-agent-sandbox-taxonomy)** `Go` `Open` — Open evaluation taxonomy and scoring framework analyzing AI agent sandboxes across 7 defense layers, 7 threat categories, and 3 operational dimensions.

---

## Architecture Selection Guide

1. **Long-lived, stateful autonomous agents (Kubernetes / Multi-tenant Cloud)**
   - Need full Linux compatibility (`apt`, compilers, headless browser), hardware kernel isolation, persistent OS + `/workspace` across sleep/wakeup, and zero-trust secret protection? $\rightarrow$ **[CubePod](https://github.com/cubeplexai/cubepod)**, **[OpenSandbox](https://github.com/opensandbox-group/OpenSandbox)**, **[E2B](https://github.com/e2b-dev/E2B)**, or **[kubernetes-sigs/agent-sandbox](https://github.com/kubernetes-sigs/agent-sandbox)** + **Kata / gVisor**.
2. **Isolating local CLI coding agents (Claude Code / Codex) on macOS or Linux laptops**
   - Want `<5ms` zero-VM process confinement? $\rightarrow$ **[Anthropic `sandbox-runtime`](https://github.com/anthropics/sandbox-runtime)** or **[Nono](https://github.com/nolabs-ai/nono)**.
   - Want full disposable guest kernel VM isolation locally? $\rightarrow$ **[Microsandbox](https://github.com/superradcompany/microsandbox)**, **[ArcBox](https://github.com/arcboxlabs/arcbox)**, **[Apple `container`](https://github.com/apple/container)**, **[Gondolin](https://github.com/earendil-works/gondolin)**, or **[Coop](https://github.com/trailofbits/coop)**.
3. **High-throughput stateless code evaluation / RLVR training / SWE-bench**
   - Running thousands of short-lived test verifications per minute? $\rightarrow$ **[SandboxFusion](https://github.com/bytedance/SandboxFusion)**, **[SWE-ReX](https://github.com/SWE-agent/SWE-ReX)**, **[Judge0](https://github.com/judge0/judge0)**, or **[Piston](https://github.com/engineer-man/piston)**.
4. **Sub-millisecond inline tool orchestration & pure data transformation**
   - Executing LLM glue logic or untrusted Python/JS snippets at edge scale without POSIX process spawning? $\rightarrow$ **[Pydantic Monty](https://github.com/pydantic/monty)**, **[Pyodide](https://github.com/pyodide/pyodide)**, **[Wasmtime](https://github.com/bytecodealliance/wasmtime)**, or **[QuickJS-NG](https://github.com/quickjs-ng/quickjs)**.
5. **Interactive Web Browsing & GUI Computer Use**
   - Need anti-bot Playwright/CDP sessions, WebRTC human takeover, or full macOS/Linux desktop GUI control? $\rightarrow$ **[Cua](https://github.com/trycua/cua)**, **[Steel Browser](https://github.com/steel-dev/steel-browser)**, **[AIO Sandbox](https://github.com/agent-infra/sandbox)**, or **[Neko](https://github.com/m1k1o/neko)**.

---

## Contributing

Contributions, new open-source sandbox submissions, and architectural corrections are warmly welcome! Please read **[CONTRIBUTING.md](./CONTRIBUTING.md)** before opening an issue or pull request.

## License

[![CC0](https://mirrors.creativecommons.org/presskit/buttons/88x31/svg/cc-zero.svg)](https://creativecommons.org/publicdomain/zero/1.0/)

To the extent possible under law, the maintainers have waived all copyright and related or neighboring rights to this work under **[CC0 1.0 Universal](./LICENSE)**.
