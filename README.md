# Tari Codebase Maps

📖 **Comprehensive technical documentation for Tari ecosystem projects**

This repository contains detailed codebase analyses and architectural overviews for key projects in the Tari ecosystem. Each codemap provides developers with essential insights into project structure, core components, and implementation patterns.

## Available Codemaps

### 🌐 [Tari Core Protocol](tari.md)
*Decentralized privacy-preserving blockchain protocol*
- **Repository**: [tari-project/tari](https://github.com/tari-project/tari)
- **Focus**: Mimblewimble protocol, merge-mining with Monero, digital asset management
- **Architecture**: Rust monorepo with layered infrastructure and gRPC service communication

### ⚡ [Glytex GPU Miner](glytex.md)
*High-performance SHA3/Keccak GPU mining application*
- **Repository**: [tari-project/glytex](https://github.com/tari-project/glytex)  
- **Focus**: Multi-backend GPU mining (CUDA, OpenCL, Metal) with trait-based abstraction
- **Architecture**: Layered multi-backend system with HTTP monitoring and P2Pool integration

### 🏊 [SHA-P2Pool](sha-p2pool.md)
*Decentralized mining pool with share chain consensus*
- **Repository**: [tari-project/sha-p2pool](https://github.com/tari-project/sha-p2pool)
- **Focus**: P2Pool mining with libp2p networking and instant decentralized payouts
- **Architecture**: Service-oriented architecture with domain-driven design and LWMA difficulty adjustment

### 📱 [Tari Android Wallet](wallet-android.md)
*Android wallet with biometric authentication and Tor privacy*
- **Repository**: [tari-project/wallet-android](https://github.com/tari-project/wallet-android)
- **Focus**: MVVM architecture with JNI/FFI Rust integration and UTXO management
- **Architecture**: Layered Android app with Dagger 2 DI, Jetpack Compose, and C++/Rust FFI bridge

### 🍎 [Tari iOS Wallet](wallet-ios.md)
*iOS Aurora wallet with Tor integration and iCloud backup*
- **Repository**: [tari-project/wallet-ios](https://github.com/tari-project/wallet-ios)
- **Focus**: Privacy-first iOS wallet with Combine reactive programming and biometric security
- **Architecture**: MVVM with UIKit, Combine framework, and Swift FFI bindings to Rust core

## Usage

Each codemap provides:
- 🏗️ **Directory structure** with detailed component explanations
- 🔧 **Core architecture** and design patterns
- 🔄 **Key workflows** and data flows
- 📚 **Development guidelines** and best practices

Perfect for developers onboarding to Tari projects or contributing to the ecosystem.

---

*Part of the [Tari Project](https://www.tari.com) ecosystem*
