# CloudV RISC-V Builds

This repository provides automated RISC-V builds of popular open-source tools and software.

The goal of this project is to make precompiled RISC-V binaries easily accessible for developers who want to use these tools without building everything from source.

---

## What This Repository Does

- Builds multiple open-source tools for RISC-V
- Uses GitHub Actions and/or self-hosted runners
- Publishes compiled binaries through GitHub Releases
- Keeps each project isolated in its own folder

Each folder contains:
- license for the build
- 
---

## Available Projects

This repository includes automated builds for tools such as:

- GCC (RISC-V)
- Binutils (RISC-V)
- Coreutils (RISC-V)
- Kubernetes
- Node.js
- OpenJDK
- PyTorch
- TensorFlow
- Bazelisk
- ONNX Runtime
- GitHub Runner (RISC-V)
- And more...

Check the **Releases** section for available binaries.

---

## Repository Structure

Each tool has its own directory:

tool-name/
├── .github/workflows/
├── build scripts
└── related files

This allows independent builds and easier maintenance.

---

## Licensing

This repository contains:

- Build scripts and CI configuration authored by this project.
- Redistributed binaries of third-party open-source software.

Each software project retains its original license.

We do not claim ownership of the third-party tools included here.

Please refer to the respective upstream projects for their license details.

---

## Disclaimer

This project redistributes compiled binaries of open-source software.

All rights belong to their respective original authors and maintainers.

If you are a project maintainer and have any concerns, please open an issue.

---

## Contributions

Contributions are welcome.

You can:
- Improve workflows
- Add new RISC-V build targets
- Optimize build performance
- Report issues

---

## Project Goal

To strengthen the RISC-V ecosystem by providing easy access to essential development tools.
