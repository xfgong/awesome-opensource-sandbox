# Contributing to Awesome Open-Source Sandbox

Thank you for helping keep **Awesome Open-Source Sandbox** accurate, neutral, and high-signal!

## Inclusion Criteria

Before submitting a project, please verify that it meets **all** of the following requirements:

1. **Genuinely Open Source**: The repository must contain the actual sandbox runtime, hypervisor, isolation wrapper, or orchestration engine under an open-source license—not merely a thin SDK wrapper or marketing landing page for a closed-source SaaS.
2. **Actively Maintained**: Archived repositories (`isArchived: true`) or abandoned proof-of-concepts without working builds are not eligible.
3. **Clear Isolation Mechanism**: The project documentation should clearly explain its isolation boundary (e.g., KVM MicroVM, User-Space Kernel, WebAssembly linear memory, Linux `namespaces` + `seccomp-bpf`, Landlock LSM, or macOS Seatbelt/Virtualization.framework).
4. **Bilingual Parity**: Please add your entry to **both** [`README.md`](./README.md) (English) and [`README_zh.md`](./README_zh.md) (Simplified Chinese) under the matching category.

## Entry Format

Use the following concise, vendor-neutral format (avoid marketing superlatives or emoji spam):

```markdown
- **[Project Name](https://github.com/org/repo)** `Primary Language` `SPDX-License` — Concise 1–2 sentence technical description explaining the isolation mechanism and primary use case.
```

## Pull Request Checklist

- [ ] Verified repository URL returns HTTP 200 and is not a stale redirect or archived repo.
- [ ] Verified primary language and SPDX license match the target repository.
- [ ] Updated both `README.md` and `README_zh.md`.
- [ ] Description focuses on concrete architecture rather than promotional slogans.
