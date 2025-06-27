# Tari Codebase Maps

📖 **Comprehensive technical documentation for Tari ecosystem projects**

This repository contains detailed codebase analyses and architectural overviews for key projects in the Tari ecosystem. Each codemap provides developers with essential insights into project structure, core components, and implementation patterns.

## Available Codemaps

### 🌐 [Tari Core Protocol](tari.md)
*Privacy-focused Rust cryptocurrency implementing Mimblewimble protocol*
- **Repository**: [tari-project/tari](https://github.com/tari-project/tari)
- **Focus**: Mimblewimble protocol, merge-mining with Monero, and modular architecture
- **Architecture**: Service-oriented async architecture with layered design and encrypted P2P communication

### ⚡ [Glytex GPU Miner](glytex.md)
*High-performance GPU mining application for Tari cryptocurrency*
- **Repository**: [tari-project/glytex](https://github.com/tari-project/glytex)  
- **Focus**: Multi-backend GPU mining (CUDA, OpenCL, Metal) with comprehensive platform support
- **Architecture**: Layered multi-backend mining system with trait-based abstraction and HTTP monitoring

### 🏊 [SHA-P2Pool](sha-p2pool.md)
*Decentralized pool mining software implementing SHA-3 algorithm mining*
- **Repository**: [tari-project/sha-p2pool](https://github.com/tari-project/sha-p2pool)
- **Focus**: Decentralized mining pool with in-memory share chain and instant payouts
- **Architecture**: Service-oriented architecture with domain-driven design and HotStuff BFT consensus

### 📱 [Tari Android Wallet](wallet-android.md)
*Cryptocurrency wallet Android app with native Rust core*
- **Repository**: [tari-project/wallet-android](https://github.com/tari-project/wallet-android)
- **Focus**: MVVM architecture with FFI integration, Tor privacy, and biometric security
- **Architecture**: Mixed XML Views + Jetpack Compose UI with Dagger 2 DI and C++/Rust FFI bridge

### 🍎 [Tari iOS Wallet](wallet-ios.md)
*Cryptocurrency wallet app for Tari blockchain with privacy-first architecture*
- **Repository**: [tari-project/wallet-ios](https://github.com/tari-project/wallet-ios)
- **Focus**: Hybrid Swift/UIKit and SwiftUI app with Rust core via FFI and Tor networking
- **Architecture**: MVVM with reactive Combine framework and 4-layer structure: UI → Business Logic → Service → Core

### 🌌 [Tari Universe](universe.md)
*Desktop cryptocurrency mining application built with modern web stack*
- **Repository**: [tari-project/universe](https://github.com/tari-project/universe)
- **Focus**: Cross-platform desktop mining with React frontend, Rust backend, and native integration
- **Architecture**: Tauri 2.x framework with React 19.1.0/TypeScript UI and Tokio async runtime

### 🔧 [Tari-Ootle](tari-ootle.md)
*Layer 2 sharded blockchain platform with smart contract execution*
- **Repository**: [tari-project/tari-ootle](https://github.com/tari-project/tari-ootle)
- **Focus**: Distributed validator nodes with HotStuff BFT consensus and WASM-based templates
- **Architecture**: Multi-layered architecture with JSON-RPC 2.0 APIs and React-based web UIs

## Usage

Each codemap provides:
- 🏗️ **Directory structure** with detailed component explanations
- 🔧 **Core architecture** and design patterns
- 🔄 **Key workflows** and data flows
- 📚 **Development guidelines** and best practices

Perfect for developers onboarding to Tari projects or contributing to the ecosystem.

---

*Part of the [Tari Project](https://www.tari.com) ecosystem*
