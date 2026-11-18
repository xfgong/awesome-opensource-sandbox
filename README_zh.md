# Awesome Open-Source Sandbox [![Awesome](https://awesome.re/badge.svg)](https://awesome.re) [![License: CC0-1.0](https://img.shields.io/badge/License-CC0_1.0-lightgrey.svg)](http://creativecommons.org/publicdomain/zero/1.0/)

**[English](./README.md)** | **简体中文**

一份面向 **AI Agent（智能体）、Code Interpreter（代码解释器）、强化学习（RL）训练环境与不可信工作负载执行**的全栈开源沙箱（Open-Source Sandbox）精选名录，涵盖云原生沙箱编排平台、本地轻量级沙箱、浏览器/桌面 Computer-Use 环境以及底层虚拟化与内核隔离运行时。

> **收录原则**：本列表仅收录**持续维护（未归档）**且**核心运行时或平台代码真正开源、可自托管与安全审计**的项目。纯闭源商业 SaaS（无开源核心引擎）不在收录范围内。

---

## 目录

- [沙箱隔离技术栈全景对比](#沙箱隔离技术栈全景对比)
- [云原生与分布式 AI Agent 沙箱平台](#云原生与分布式-ai-agent-沙箱平台)
- [本地优先与可嵌入式 MicroVM 沙箱](#本地优先与可嵌入式-microvm-沙箱)
- [浏览器、桌面 GUI 与 Computer-Use 沙箱](#浏览器桌面-gui-与-computer-use-沙箱)
- [代码解释器、多语言评测引擎与 RL 执行环境](#代码解释器多语言评测引擎与-rl-执行环境)
- [WebAssembly 与语言级轻量沙箱](#webassembly-与语言级轻量沙箱)
- [MicroVM 虚拟化监控器（VMM）、Unikernel 与虚拟机运行时](#microvm-虚拟化监控器vmmunikernel-与虚拟机运行时)
- [用户态应用内核、系统级容器与 OS 进程沙箱](#用户态应用内核系统级容器与-os-进程沙箱)
- [MCP 沙箱服务、安全治理与评测体系](#mcp-沙箱服务安全治理与评测体系)
- [技术选型指南](#技术选型指南)
- [参与贡献](#参与贡献)

---

## 沙箱隔离技术栈全景对比

在架构选型时，通常需要在**安全隔离边界强度**、**冷启动延迟**、**Linux/POSIX 兼容性**与**状态持久化能力**之间进行权衡：

| 隔离层级 | 代表性开源引擎 | 安全隔离边界 | 冷启动耗时 | POSIX / 系统兼容性 | 典型适用场景 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **硬件虚拟化 MicroVM** | Firecracker, Kata Containers, Cloud Hypervisor, `libkrun` | 基于 KVM / Hypervisor.framework 的独立 Guest 内核（**最强**） | `~50ms–1s`（配合预热池/快照可达 `<50ms`） | 完整 Linux 系统（支持 `apt`、`systemd`、裸块设备等） | 多租户 AI Agent 平台、自主软件工程/DevOps、全系统持久化 |
| **用户态应用内核** | gVisor (`runsc`) | 用户态拦截并实现 Linux 系统调用（`Sentry`），阻断宿主机直通调用 | `~100–300ms` | 覆盖绝大多数 Linux 系统调用（部分底层 `ioctl` 受限） | 无裸金属 KVM / 不支持嵌套虚拟化的公有云 Kubernetes 集群 |
| **加固型 / 系统级容器** | Sysbox, Youki, OCI + Seccomp | 共享宿主机内核 + User/PID/Net 命名空间 + Seccomp-BPF | `~50–200ms` | 原生运行标准 Linux 二进制与容器镜像 | 内部可信租户 CI/CD、单租户大规模 RL 训练采样、Docker-in-Docker |
| **WebAssembly / 语言级虚拟机** | Wasmtime, Wasmer, Pyodide, Monty, QuickJS | 基于能力（Capability-based）的线性内存与指令级沙箱 | `<1ms–20ms` | WASI 子集或单一编程语言标准库 | 超高密度工具编排调用、内联 Python/JS 代码片段执行、边缘计算函数 |
| **OS 进程级沙箱** | Anthropic `srt`, Bubblewrap, NsJail, Firejail, Landlock | 宿主机原生进程封装（`seccomp`、`namespaces`、macOS Seatbelt） | `<5ms` | 直接复用宿主机 CLI 工具链，无需守护进程 | 在开发者个人电脑上安全包裹本地编码 Agent（Claude Code、Codex） |

---

## 云原生与分布式 AI Agent 沙箱平台

*提供完整生命周期编排、多语言 SDK、预热池（Warm Pool）、持久化存储、网络管控与集群调度的全栈沙箱平台。*

- **[OpenSandbox](https://github.com/opensandbox-group/OpenSandbox)** `Go` `Apache-2.0` — 面向 AI 应用的通用开源沙箱平台，提供统一沙箱生命周期 API、多语言 SDK，并支持 Docker、Kubernetes 及安全容器等可插拔运行时后端。
- **[E2B](https://github.com/e2b-dev/E2B)** & **[E2B Runtime](https://github.com/e2b-dev/runtime)** `Go / Python / TS` `Apache-2.0` — 基于 Firecracker MicroVM 的开源云端 Agent 运行时基础设施与编排引擎，配套 Python/JS [Code Interpreter SDK](https://github.com/e2b-dev/code-interpreter)，支持基于内存快照的快速休眠与恢复。
- **[CubeSandbox](https://github.com/TencentCloud/CubeSandbox)** `Go` `Custom OSS` — 腾讯云开源的轻量级、高并发、瞬时启动 AI Agent 沙箱运行时，兼顾多租户安全隔离与低资源开销。
- **[CubePod](https://github.com/cubeplexai/cubepod)** `Go / Rust` `Apache-2.0` — 面向长生命周期自主 AI Agent 的云原生**持久可写全系统（Persistent Writable RootFS）MicroVM 执行平台**（基于 Kubernetes + Kata Containers + 双 CSI 裸块设备 PVC）。解耦系统层 `upperdir`（保留 `apt`/`pip`/`npm` 修改）与 `/workspace` 数据卷，支持 `<50ms` `PodWarmPool` 预挂载热池分配、零导出极速休眠（`fsfreeze` 后释放至 0 CPU/RAM）与 `~1s` 跨节点唤醒、`<1s` CoW 沙箱分支克隆、零信任 `cpodref_` mTLS 出口密钥置换，并 100% 兼容 E2B 协议与官方 SDK。
- **[kubernetes-sigs/agent-sandbox](https://github.com/kubernetes-sigs/agent-sandbox)** `Go` `Apache-2.0` — Kubernetes 官方 SIG 子项目，通过声明式 CRD（`Sandbox`、`SandboxWarmPool`、`SandboxClaim`）管理面向 AI Agent 运行时与强化学习的有状态、强隔离单例工作负载。
- **[Sandstorm](https://github.com/deeplethe/sandstorm)** `Rust` `Apache-2.0` — 使用 Rust 构建的高密度 AI Agent 沙箱编排系统，旨在单台低成本服务器上高密度并发运行数千个隔离沙箱。
- **[SPACE](https://github.com/perplexityai/SPACE)** `Rust` `Apache-2.0` — Perplexity AI 开源的 *Sandboxed Platform for Agentic Code Execution*，提供可插拔运行时后端以在严格隔离环境中执行 Agent 与用户代码。
- **[Beta9](https://github.com/beam-cloud/beta9)** `Go` `AGPL-3.0` — 驱动 Beam Cloud 的自托管 Serverless 计算引擎，支持极速容器/沙箱冷启动、分布式存储、后台任务队列及 GPU 推理编排。
- **[Cloudflare Sandbox SDK](https://github.com/cloudflare/sandbox-sdk)** `TypeScript` `Apache-2.0` — Cloudflare 官方开源的边缘沙箱 SDK 与 Durable Objects 编排层，支持在边缘网络运行隔离容器、Shell 命令、代码解释器与预览端口。
- **[Vercel Sandbox](https://github.com/vercel/sandbox)** `TypeScript` `Apache-2.0` — Vercel 开源的临时安全计算原语与 SDK，用于安全隔离地执行不可信代码或大模型生成的代码。

---

## 本地优先与可嵌入式 MicroVM 沙箱

*专为开发者笔记本或单机场景打造的轻量级 MicroVM 工具与嵌入式库，无需复杂集群即可安全隔离 Claude Code、Codex、SWE-agent 等本地智能体。*

- **[Microsandbox](https://github.com/superradcompany/microsandbox)** `Rust` `Apache-2.0` — 本地优先的可编程 MicroVM 运行时与库（基于 `libkrun`），兼容标准 OCI 容器镜像，兼具毫秒级启动体验与硬件级虚拟机隔离。
- **[smolvm](https://github.com/smol-machines/smolvm)** `Rust` `Apache-2.0` — 可嵌入、可移植、支持状态分支（Branchable）的轻量级虚拟机，专为在本地安全运行和检查点回滚 AI Agent 设计。
- **[ArcBox](https://github.com/arcboxlabs/arcbox)** `Rust` `Apache-2.0` — 纯 Rust 编写的本地优先虚拟机运行时，兼容 OCI 镜像，可在 `<100ms` 内启动拥有独立内核、文件系统与网络栈的隔离环境。
- **[BoxLite](https://github.com/boxlite-ai/boxlite)** `Rust / TypeScript` `Apache-2.0` — 面向 AI Agent 的嵌入式 MicroVM 引擎，既能直接嵌入桌面客户端运行，也能弹性扩展至云端集群。
- **[Gondolin](https://github.com/earendil-works/gondolin)** `Rust / TypeScript` `Apache-2.0` — 配备可编程 TypeScript 控制平面的 Linux MicroVM 沙箱，内置虚拟化网络栈与透明 HTTP/TLS 出口代理，使外部 API 凭据无需进入虚拟机内部。
- **[Clawk](https://github.com/clawkwork/clawk)** `Go` `Apache-2.0` — 为终端编程 Agent 提供即用即焚的隔离 Linux 虚拟机，避免 Agent 直接修改或破坏宿主机个人环境。
- **[Shuru](https://github.com/superhq-ai/shuru)** `Rust` `Apache-2.0` — 跨 macOS 与 Linux 的本地优先 MicroVM 沙箱，用于安全隔离自主编码智能体。
- **[Arrakis](https://github.com/abshkbh/arrakis)** `Go / Python` `AGPL-3.0` — 支持自托管的 AI 代码执行与 Computer-Use MicroVM 沙箱，原生内置快照回溯（Backtracking）能力以支持多步树搜索推演。
- **[Microsoft NVX](https://github.com/microsoft/nvx)** `Rust` `MIT` — 微软开源的跨平台 Rust MicroVM 沙箱，专为隔离 Agent 工作负载打造。
- **[Coop](https://github.com/trailofbits/coop)** `Rust` `Apache-2.0` — 顶级安全团队 Trail of Bits 开源的隔离虚拟机环境，专为高安全性运行 Claude Code 与 Codex 设计。

---

## 浏览器、桌面 GUI 与 Computer-Use 沙箱

*隔离的无头/有头浏览器、WebRTC 远程虚拟桌面与跨操作系统 GUI 环境，支撑网页自动化与类人桌面操作智能体。*

- **[Cua (Computer-Use Agents)](https://github.com/trycua/cua)** `Rust / Python` `MIT` — 开源 Computer-Use 基础设施，提供基于 Apple Virtualization.framework (`Lume`) 的 macOS、Linux 与 Windows 跨平台桌面虚拟机沙箱、统一 GUI 驱动及评测基准。
- **[Neko](https://github.com/m1k1o/neko)** `Go / TypeScript` `Apache-2.0` — 运行于 Docker 容器内并通过 WebRTC 低延迟推流的自托管虚拟浏览器与桌面环境，广泛用于 AI Agent 浏览器可视化与人工接管（Human-in-the-loop）。
- **[Steel Browser](https://github.com/steel-dev/steel-browser)** `TypeScript` `Apache-2.0` — 面向 AI Agent 开箱即用的开源浏览器沙箱 API，内置会话生命周期管理、Playwright/Puppeteer/CDP 接入、代理轮换与反爬对抗支持。
- **[AIO Sandbox (`agent-infra/sandbox`)](https://github.com/agent-infra/sandbox)** `Python / Go` `Apache-2.0` — 单容器 All-in-One 智能体沙箱，将 Chromium 浏览器（CDP + VNC）、Shell 终端、文件读写 API、VSCode Server 与 MCP 工具统一封装在单一端点下。
- **[Playwriter](https://github.com/remorses/playwriter)** `TypeScript` `MIT` — 支持 CLI 与 MCP 协议的浏览器控制工具，在有状态安全沙箱中执行 Playwright 脚本片段。
- **[Selkies](https://github.com/selkies-project/selkies)** `Rust / Python` `MPL-2.0` — 面向容器与 Kubernetes 云原生环境的超低延迟、支持硬件加速的 WebRTC 远程桌面流式传输平台。
- **[Open Computer Use](https://github.com/e2b-dev/open-computer-use)** `Python` `Apache-2.0` — 结合开源视觉多模态大模型与隔离 Linux 桌面沙箱（X11/VNC/键鼠控制）的开源桌面操作参考实现。
- **[Kernel Images](https://github.com/kernel/kernel-images)** `Go` `Apache-2.0` — 面向 Web Agent 与 Playwright 自动化的亚秒级启动浏览器容器/Unikernel 镜像套件。

---

## 代码解释器、多语言评测引擎与 RL 执行环境

*专注于高吞吐不可信代码判题、大模型强化学习（RLVR）奖励验证与 SWE-bench 大规模并行评测的执行引擎。*

- **[Judge0](https://github.com/judge0/judge0)** `Ruby / C` `GPL-3.0` — 成熟稳定的开源在线代码执行与评测系统（底层基于 `isolate`），支持 60+ 种编程语言，广泛用于在线判题与 AI 代码验证。
- **[Piston](https://github.com/engineer-man/piston)** `JavaScript / Rust` `MIT` — 高性能通用多语言无状态代码执行引擎，适合快速安全地运行不可信代码片段。
- **[Dify Sandbox](https://github.com/langgenius/dify-sandbox)** `Go` `Apache-2.0` — Dify 官方开源的轻量级多语言代码执行沙箱服务，基于 Seccomp 系统调用过滤保护工作流中的 Python / Node.js 代码节点。
- **[LLM Sandbox](https://github.com/vndee/llm-sandbox)** `Python` `MIT` — 轻量可移植的 Python 代码解释器沙箱库，支持 Docker、Kubernetes、Podman 等多种后端，无缝集成 LangChain、LlamaIndex 与 MCP。
- **[SandboxFusion](https://github.com/bytedance/SandboxFusion)** `Python` `Apache-2.0` — 字节跳动开源的多语言代码评测沙箱，专为大模型代码基准测试与强化学习（RLVR）大规模并发验证设计，内置 20+ 种语言与主流评测集支持。
- **[CodeRunner](https://github.com/instavm/coderunner)** `Python` `Apache-2.0` — 面向本地 AI Agent 的多语言代码沙箱，支持 Apple Containers 与 Docker 隔离及 MCP 接入。
- **[SWE-ReX](https://github.com/SWE-agent/SWE-ReX)** `Python` `MIT` — 驱动 `SWE-agent` 的大规模并行沙箱运行时引擎，通过统一异步接口在本地 Docker、云端集群或 Serverless 平台上管理交互式 Shell 会话。

---

## WebAssembly 与语言级轻量沙箱

*无需启动完整操作系统内核，基于线性内存边界与能力安全模型实现微秒至毫秒级冷启动的执行环境。*

- **[Wasmer](https://github.com/wasmerio/wasmer)** `Rust` `MIT` — 高性能 WebAssembly 运行时，通过 WASIX 扩展提供丰富的类 POSIX 能力，可运行编译型应用、Web 服务与轻量 Agent 沙箱。
- **[Wasmtime](https://github.com/bytecodealliance/wasmtime)** `Rust` `Apache-2.0` — Bytecode Alliance 旗下遵循最新 WASI Component Model 标准的安全性优先 WebAssembly 运行时，支持指令计费（Fuel）与超时中断。
- **[Pyodide](https://github.com/pyodide/pyodide)** `Python / C` `MPL-2.0` — 编译为 WebAssembly 的完整 CPython 发行版（含 NumPy、Pandas、SciPy、Matplotlib），可在浏览器、Node.js、Deno 或边缘 Worker 中实现零 OS 依赖的 Python 数据科学沙箱。
- **[WasmEdge](https://github.com/WasmEdge/WasmEdge)** `C++` `Apache-2.0` — CNCF 云原生高性能 WebAssembly 运行时，深度集成 OCI 容器生态（`crun`/`containerd`）与 WASI-NN 推理扩展。
- **[Pydantic Monty](https://github.com/pydantic/monty)** `Rust` `MIT` — Pydantic 团队用 Rust 从零编写的安全 Python 子集解释器沙箱，专为微秒级执行大模型生成的工具编排代码设计，支持执行状态快照序列化且默认完全阻断宿主机访问。
- **[Extism](https://github.com/extism/extism)** `Rust` `BSD-3-Clause` — 基于 Wasmtime/Wasmer 的跨语言安全插件与沙箱框架，支持在 15+ 种宿主语言中安全加载并调用不可信模块。
- **[AgentOS (`rivet-dev/agentos`)](https://github.com/rivet-dev/agentos)** `Rust` `Apache-2.0` — 基于 WebAssembly 驱动的超轻量、极速启动且兼容 Linux 工具生态的 AI Agent 沙箱。
- **[QuickJS-NG](https://github.com/quickjs-ng/quickjs)** `C` `MIT` — 社区持续活跃演进的轻量可嵌入 ES2023+ JavaScript 引擎（常配合 [`@sebastianwessel/quickjs`](https://github.com/sebastianwessel/quickjs) 编译为 Wasm），实现亚毫秒级 JS/TS 安全沙箱执行。

---

## MicroVM 虚拟化监控器（VMM）、Unikernel 与虚拟机运行时

*支撑各类云端与本地沙箱平台的底层硬件虚拟化引擎、容器原生 Hypervisor 与单地址空间 Unikernel。*

- **[Apple `container`](https://github.com/apple/container)** & **[`containerization`](https://github.com/apple/containerization)** `Swift` `Apache-2.0` — 苹果官方开源的命令行工具与 Swift 框架，专为 Apple Silicon 优化，将每个 OCI Linux 容器运行在独立的亚秒级启动轻量虚拟机中。
- **[Firecracker](https://github.com/firecracker-microvm/firecracker)** `Rust` `Apache-2.0` — AWS 开源并驱动 AWS Lambda、Fargate 与 E2B 的生产级 KVM 虚拟化监控器，约 `125ms` 启动极简 Linux MicroVM，单实例内存开销低于 `5 MiB`。
- **[Lima](https://github.com/lima-vm/lima)** `Go` `Apache-2.0` — 支持自动文件共享与端口转发的跨平台 Linux 虚拟机管理工具，广泛用于在 macOS/Linux 上承载隔离的 containerd 与 Docker 环境。
- **[Kata Containers](https://github.com/kata-containers/kata-containers)** `Rust / Go` `Apache-2.0` — CNCF 标准虚拟化容器运行时（`containerd-shim-kata-v2`），将 Kubernetes Pod 封装在轻量硬件虚拟机中（支持 QEMU、Cloud Hypervisor、Firecracker、Dragonball），兼具容器编排体验与独立内核隔离，支持 `virtio-fs` 与 CSI 裸块设备直通挂载。
- **[Cloud Hypervisor](https://github.com/cloud-hypervisor/cloud-hypervisor)** `Rust` `Apache-2.0 / BSD-3-Clause` — 基于 `rust-vmm` 构建的现代云工作负载虚拟化监控器（支持 KVM 与 MSHV），具备极小攻击面以及 CPU/内存/`virtio-blk` 热插拔能力。
- **[Unikraft](https://github.com/unikraft/unikraft)** `C` `BSD-3-Clause` — 高度模块化的云原生 Unikernel 构建系统与内核库，通过将应用裁剪编译为专用单地址空间内核实现毫秒级启动与数 MB 级内存占用。
- **[libkrun](https://github.com/libkrun/libkrun)** `Rust / C` `Apache-2.0` — 以动态链接库形态提供 KVM（Linux）与 `Hypervisor.framework`（macOS）进程级硬件虚拟化隔离的轻量引擎（驱动 `crun --krun` 与 Microsandbox）。
- **[Kuasar](https://github.com/kuasar-io/kuasar)** `Rust` `Apache-2.0` — CNCF Sandbox 多沙箱统一容器运行时，在单一低开销 Rust 守护进程中同时支持 MicroVM（Cloud Hypervisor/QEMU/StratoVirt）、应用内核（Quark）与 Wasm 沙箱。
- **[NanoVMs Ops](https://github.com/nanovms/ops)** `Go / C` `MIT` — 将常规 Linux 应用程序一键打包为 Nanos Unikernel 镜像的构建工具，彻底移除 Shell 与多进程提权攻击面。

---

## 用户态应用内核、系统级容器与 OS 进程沙箱

*不依赖硬件虚拟化，通过用户态系统调用拦截、Rootless 命名空间、`seccomp-bpf`、Landlock LSM 与 macOS Seatbelt 实现强隔离的运行时工具。*

- **[gVisor](https://github.com/gvisor/gvisor)** `Go` `Apache-2.0` — Google 开源的 OCI 兼容容器应用内核（`runsc`），使用内存安全的 Go 语言在用户态重写 Linux 系统调用栈，无需裸金属 KVM 即可在任意云主机上提供强多租户容器隔离。
- **[Bubblewrap (`bwrap`)](https://github.com/containers/bubblewrap)** `C` `LGPL-2.0+` — 非特权底层 Linux 沙箱工具，结合 User/Mount/Net/PID 命名空间与 `seccomp` 过滤器；是 Flatpak、Anthropic `srt`（Linux 端）以及 Codex CLI 沙箱的核心底座。
- **[Firejail](https://github.com/netblue30/firejail)** `C` `GPL-2.0` — 经典 Linux 进程安全沙箱，利用命名空间、AppArmor 与丰富的预设 `seccomp-bpf` 策略限制不可信程序的权限边界。
- **[Youki](https://github.com/youki-dev/youki)** `Rust` `Apache-2.0` — 使用 Rust 编写的高性能、内存安全标准 OCI 容器运行时（`runc` 替代方案），消除底层容器启动阶段的 C 内存安全隐患。
- **[Anthropic Sandbox Runtime (`srt`)](https://github.com/anthropics/sandbox-runtime)** `TypeScript` `Apache-2.0` — Anthropic 开源的无容器进程级沙箱工具（macOS 基于 `sandbox-exec`，Linux 基于 `bubblewrap`），对任意本地命令强制执行细粒度目录读写白名单与代理域名过滤。
- **[Nono](https://github.com/nolabs-ai/nono)** `Rust` `Apache-2.0` — 零配置、零延迟的操作系统级 Agent 运行时安全沙箱（基于 Linux Landlock LSM 与 macOS Seatbelt），提供基于能力模型的文件、网络与凭据防护。
- **[NsJail](https://github.com/google/nsjail)** `C++` `Apache-2.0` — Google 开源的轻量级 Linux 进程隔离工具，深度整合名字空间、`cgroups` v1/v2、资源配额与声明式 Kafel `seccomp-bpf` 策略语言。
- **[Sysbox](https://github.com/nestybox/sysbox)** `Go / C` `Apache-2.0` — 新一代系统容器 OCI 运行时（已被 Docker 收购并持续开源），通过 User Namespace 与 `/proc`、`/sys` 虚拟化让无特权容器能够像虚拟机一样安全运行 `systemd`、Docker 与 Kubernetes。
- **[Isolate](https://github.com/ioi/isolate)** `C` `GPL-2.0` — 国际信息学奥林匹克竞赛（IOI）打造的 Linux `cgroups` 与命名空间判题沙箱，专注于精准的 CPU/内存计量与防侧信道干扰。
- **[libseccomp](https://github.com/seccomp/libseccomp)** `C` `LGPL-2.1` & **[go-landlock](https://github.com/landlock-lsm/go-landlock)** `Go` `MIT` — 构建自定义 Linux 系统调用白名单过滤（`seccomp-bpf`）与非特权文件系统/网络访问控制（`Landlock LSM`）的核心基础库。

---

## MCP 沙箱服务、安全治理与评测体系

*Model Context Protocol（MCP）沙箱适配器、Agent 安全治理工具包与安全评估分类学框架。*

- **[Microsoft Agent Governance Toolkit](https://github.com/microsoft/agent-governance-toolkit)** `Python` `MIT` — 微软开源的自主 AI Agent 安全治理套件，覆盖策略强制执行、零信任身份认证、执行沙箱隔离以及 OWASP Agentic Top 10 全项防护。
- **[Windows Sandbox MCP](https://github.com/shanselman/sandbox-mcp)** `JavaScript` `MIT` — 自动化操控一次性 Windows Sandbox 虚拟机的 MCP Server 与 CLI，供智能体安全测试 Windows 应用与脚本。
- **[Code Sandbox MCP](https://github.com/Automata-Labs-team/code-sandbox-mcp)** `Go` `MIT` — 基于隔离容器环境的 MCP 代码沙箱服务，让支持 MCP 的大模型客户端安全执行任意代码、管理文件及按需安装依赖。
- **[The Agent Sandbox Taxonomy](https://github.com/kajogo777/the-agent-sandbox-taxonomy)** `Go` `Open` — 开源 AI Agent 沙箱安全分类学与量化评分框架，从 7 层纵深防御、7 类威胁向量与 3 个工程维度对主流沙箱架构进行系统评估。

---

## 技术选型指南

1. **长生命周期、有状态的生产级多租户自主智能体（Kubernetes / 私有云部署）**
   - 需要完整 Linux 生态（自由执行 `apt`/`pip`/`npm`、编译代码、启动浏览器）、硬件级独立内核隔离、休眠唤醒后保留全系统与 `/workspace` 状态，以及零信任密钥防泄漏？ $\rightarrow$ 推荐 **[CubePod](https://github.com/cubeplexai/cubepod)**、**[OpenSandbox](https://github.com/opensandbox-group/OpenSandbox)**、**[E2B](https://github.com/e2b-dev/E2B)** 或 **[kubernetes-sigs/agent-sandbox](https://github.com/kubernetes-sigs/agent-sandbox)** + **Kata / gVisor**。
2. **在个人开发机（macOS / Linux）上安全限制本地 CLI 编程智能体（如 Claude Code、Codex）**
   - 追求 `<5ms` 零虚拟机开销的进程边界管控？ $\rightarrow$ 推荐 **[Anthropic `sandbox-runtime`](https://github.com/anthropics/sandbox-runtime)** 或 **[Nono](https://github.com/nolabs-ai/nono)**。
   - 需要独立 Guest 内核的本地即用即焚虚拟机隔离？ $\rightarrow$ 推荐 **[Microsandbox](https://github.com/superradcompany/microsandbox)**、**[ArcBox](https://github.com/arcboxlabs/arcbox)**、**[Apple `container`](https://github.com/apple/container)**、**[Gondolin](https://github.com/earendil-works/gondolin)** 或 **[Coop](https://github.com/trailofbits/coop)**。
3. **高并发无状态代码评测 / 大模型强化学习（RLVR）训练采样 / SWE-bench 评测**
   - 每分钟需要并发拉起成千上万次短生命周期单元测试与判题？ $\rightarrow$ 推荐 **[SandboxFusion](https://github.com/bytedance/SandboxFusion)**、**[SWE-ReX](https://github.com/SWE-agent/SWE-ReX)**、**[Judge0](https://github.com/judge0/judge0)** 或 **[Piston](https://github.com/engineer-man/piston)**。
4. **微秒/亚毫秒级内联工具编排与纯数据计算片段执行**
   - 在边缘节点或宿主进程内高频运行 LLM 生成的 Python/JS 胶水逻辑，无需完整操作系统进程？ $\rightarrow$ 推荐 **[Pydantic Monty](https://github.com/pydantic/monty)**、**[Pyodide](https://github.com/pyodide/pyodide)**、**[Wasmtime](https://github.com/bytecodealliance/wasmtime)** 或 **[QuickJS-NG](https://github.com/quickjs-ng/quickjs)**。
5. **交互式网页浏览自动化与桌面 GUI 操作（Computer Use）**
   - 需要 Playwright/CDP 会话管理、WebRTC 实时画面推流与人工接管，或完整 macOS/Linux 桌面键鼠控制？ $\rightarrow$ 推荐 **[Cua](https://github.com/trycua/cua)**、**[Steel Browser](https://github.com/steel-dev/steel-browser)**、**[AIO Sandbox](https://github.com/agent-infra/sandbox)** 或 **[Neko](https://github.com/m1k1o/neko)**。

---

## 参与贡献

欢迎通过 Issue 或 Pull Request 补充优秀的开源沙箱项目、修正技术细节或完善分类！提交前请参阅 **[CONTRIBUTING.md](./CONTRIBUTING.md)**。

## 开源许可

[![CC0](https://mirrors.creativecommons.org/presskit/buttons/88x31/svg/cc-zero.svg)](https://creativecommons.org/publicdomain/zero/1.0/)

在法律允许的最大范围内，本项目维护者依据 **[CC0 1.0 通用公共领域奉献协议](./LICENSE)** 放弃本作品的所有版权及相关邻接权利。
