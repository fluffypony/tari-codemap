# Tari Codebase Maps

📖 **Comprehensive technical documentation for Tari ecosystem projects**

This repository contains detailed codebase analyses and architectural overviews for key projects in the Tari ecosystem. Each codemap provides developers with essential insights into project structure, core components, and implementation patterns.

## Available Codemaps

### 🌐 [Tari Core Protocol](tari.md)
*Main Tari blockchain implementation*
- **Repository**: [tari-project/tari](https://github.com/tari-project/tari)
- **Focus**: Core blockchain protocol, P2P communications, wallet infrastructure
- **Architecture**: Modular Rust workspace with layered design

### ⚡ [Glytex GPU Miner](glytex.md)
*High-performance GPU mining application*
- **Repository**: [tari-project/glytex](https://github.com/tari-project/glytex)  
- **Focus**: Multi-platform GPU mining (CUDA, OpenCL, Metal)
- **Architecture**: Performance-optimized mining kernels with HTTP monitoring

### 🏊 [SHA-P2Pool](sha-p2pool.md)
*Decentralized pool mining software*
- **Repository**: [tari-project/sha-p2pool](https://github.com/tari-project/sha-p2pool)
- **Focus**: Decentralized mining pool with share chain consensus
- **Architecture**: Service-oriented design with domain-driven principles

### 📱 [Tari Android Wallet](wallet-android.md)
*Native Android wallet application*
- **Repository**: [tari-project/wallet-android](https://github.com/tari-project/wallet-android)
- **Focus**: Mobile wallet with Tor integration and FFI bindings
- **Architecture**: Android MVP with Dagger DI and Rust FFI integration

### 🍎 [Tari iOS Wallet](wallet-ios.md)
*Native iOS wallet application*
- **Repository**: [tari-project/wallet-ios](https://github.com/tari-project/wallet-ios)
- **Focus**: iOS wallet with SwiftUI and native Tari library integration
- **Architecture**: SwiftUI with MVVM and Swift FFI bindings

## Usage

Each codemap provides:
- 🏗️ **Directory structure** with detailed component explanations
- 🔧 **Core architecture** and design patterns
- 🔄 **Key workflows** and data flows
- 📚 **Development guidelines** and best practices

Perfect for developers onboarding to Tari projects or contributing to the ecosystem.

---

*Part of the [Tari Project](https://www.tari.com) ecosystem*
