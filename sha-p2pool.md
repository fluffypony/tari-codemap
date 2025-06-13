# Sha-P2Pool

**Repository:** https://github.com/tari-project/sha-p2pool

**Branch:** development

## Project Overview

## SHA-P2Pool Codebase Overview

### Project Introduction

SHA-P2Pool is a sophisticated decentralized pool mining software for the Tari network implementing SHA-3 algorithm mining. It's a Rust implementation of Bitcoin's original p2pool concept with modifications for the Tari ecosystem, providing instant decentralized payouts through an in-memory side chain called a "share chain." The system enables miners to participate in a decentralized mining pool without central authority, maintaining the decentralized principles of cryptocurrency while providing the benefits of pool mining.

### Architectural Philosophy

SHA-P2Pool implements a **service-oriented architecture with domain-driven design principles**, creating clear boundaries between concerns while maintaining high cohesion within domains. The architecture emphasizes **seamless miner integration** (miners connect to P2Pool exactly as they would to a Tari base node), **consensus reliability** (sophisticated share chain consensus with uncle blocks and LWMA difficulty adjustment), and **network resilience** (comprehensive P2P networking with NAT traversal and intelligent peer management).

### Directory Structure

```
sha-p2pool/
├── .cargo/                          # Cargo-specific configuration
│   ├── audit.toml                   # Security audit configuration with ignored advisories
│   └── config.toml                  # Cross-compilation linkers and CI command aliases
├── .config/                         # Project-specific configuration
│   └── nextest.toml                 # Test runner configuration with CI and IntelliJ profiles
├── .github/                         # GitHub CI/CD and project templates
│   ├── workflows/                   # GitHub Actions workflows
│   │   ├── audit.yml                # Daily security audit automation
│   │   ├── build_binaries.json      # Multi-platform build matrix configuration
│   │   ├── build_binaries.yml       # Cross-platform binary builds with signing
│   │   ├── build_docker.yml         # Multi-arch Docker image builds and publishing
│   │   ├── ci.yml                   # Main CI pipeline (format, clippy, test)
│   │   ├── coverage.yml             # Source code coverage analysis
│   │   ├── integration_tests.yml    # Cucumber-based integration testing
│   │   ├── pr_signed_commits_check.yml # GPG signature validation for PRs
│   │   ├── pr_title.yml             # Conventional Commits enforcement
│   │   └── [others]                 # Additional workflow automation
│   ├── ISSUE_TEMPLATE/              # GitHub issue templates
│   ├── PULL_REQUEST_TEMPLATE.md     # PR template for structured reviews
│   └── dependabot.yml               # Dependency update automation
├── integration_tests/               # End-to-end testing framework
│   ├── src/                         # Test infrastructure
│   │   ├── lib.rs                   # Test utilities and helpers
│   │   ├── p2pool_process.rs        # P2Pool node process management
│   │   ├── base_node_process.rs     # Tari base node process management
│   │   ├── world.rs                 # Cucumber World for test coordination
│   │   └── miner.rs                 # Mining simulation utilities
│   ├── tests/                       # Cucumber behavior tests
│   │   ├── features/                # Gherkin feature files
│   │   │   └── Sync.feature         # P2Pool synchronization test scenarios
│   │   ├── steps/                   # Test step implementations
│   │   │   ├── mod.rs               # Common step definitions and utilities
│   │   │   ├── base_node_steps.rs   # Tari base node test steps
│   │   │   └── p2pool_steps.rs      # P2Pool node test steps
│   │   └── cucumber.rs              # Main Cucumber test runner
│   ├── log4rs/                      # Test logging configuration
│   │   └── cucumber.yml             # Comprehensive logging setup for integration tests
│   └── .husky/                      # Git hooks for test automation
│       └── _/husky.sh              # Husky Git hooks initialization script
├── p2pool/                          # Main application source code
│   ├── src/
│   │   ├── main.rs                  # Application entry point with panic handling
│   │   ├── lib.rs                   # Library interface and public API exports
│   │   ├── cli/                     # Command-line interface
│   │   │   ├── args.rs              # CLI argument parsing and validation
│   │   │   ├── util.rs              # CLI styling and validation utilities
│   │   │   └── commands/            # Command implementations
│   │   │       ├── mod.rs           # Command module organization
│   │   │       ├── start.rs         # Start command delegation
│   │   │       ├── diagnostics.rs  # Network diagnostics mode
│   │   │       ├── generate_identity.rs # Peer identity generation
│   │   │       ├── list_squads.rs  # Squad discovery (placeholder)
│   │   │       └── util.rs          # **BOOTSTRAP ORCHESTRATOR** - Complete server initialization
│   │   └── server/                  # Core server implementation
│   │       ├── server.rs            # **ARCHITECTURAL BACKBONE** - Service orchestration and lifecycle
│   │       ├── config.rs            # Configuration management with builder pattern
│   │       ├── grpc/                # gRPC service layer
│   │       │   ├── mod.rs           # gRPC module organization and constants
│   │       │   ├── base_node.rs     # **MINER INTEGRATION BRIDGE** - Complete base node API passthrough
│   │       │   ├── p2pool.rs        # **MINING INTERFACE** - P2Pool-specific mining operations
│   │       │   ├── util.rs          # Connection utilities and coinbase handling
│   │       │   └── error.rs         # gRPC error handling
│   │       ├── http/                # HTTP service layer
│   │       │   ├── mod.rs           # HTTP module organization
│   │       │   ├── server.rs        # HTTP server implementation with Axum
│   │       │   ├── stats_collector.rs # **OPERATIONAL INTELLIGENCE** - Comprehensive statistics collection
│   │       │   ├── health.rs        # Health check endpoint
│   │       │   ├── version.rs       # Version endpoint
│   │       │   └── stats/           # Statistics endpoints and models
│   │       │       ├── mod.rs       # Stats module with timeout constants
│   │       │       ├── handlers.rs  # HTTP request handlers for stats
│   │       │       └── models.rs    # Data structures for stats responses
│   │       ├── p2p/                 # Peer-to-peer networking
│   │       │   ├── mod.rs           # P2P module organization and exports
│   │       │   ├── network.rs       # **DECENTRALIZED NERVOUS SYSTEM** - Core P2P service with libp2p
│   │       │   ├── messages.rs      # P2P message definitions with CBOR serialization
│   │       │   ├── peer_store.rs    # **NETWORK INTELLIGENCE** - Comprehensive peer management
│   │       │   ├── relay_store.rs   # Relay server management for NAT traversal
│   │       │   ├── setup.rs         # libp2p network stack initialization
│   │       │   ├── client.rs        # P2P service client for block broadcasting
│   │       │   └── util.rs          # P2P utilities and identity management
│   │       ├── diagnostics/         # Network diagnostics and monitoring
│   │       │   └── mod.rs           # Diagnostics module organization
│   │       └── sharechain/          # **CONSENSUS CORE** - P2Pool share chain implementation
│   │           ├── mod.rs           # Sharechain module interface and constants
│   │           ├── error.rs         # Comprehensive sharechain error handling
│   │           ├── p2block.rs       # Core P2Pool block structure and builder
│   │           ├── p2chain.rs       # **CONSENSUS HEART** - Share chain logic with validation
│   │           ├── p2chain_level.rs # Block level management for height organization
│   │           ├── in_memory.rs     # **DOMAIN SERVICE** - ShareChain trait implementation
│   │           └── lmdb_block_storage.rs # LMDB-based persistent storage
│   └── Cargo.toml                   # Package configuration with dependencies
├── scripts/                         # Build and deployment scripts
│   ├── cross_compile_tooling.sh     # Cross-compilation environment setup
│   ├── cross_compile_ubuntu_18-pre-build.sh # Ubuntu 18.04 cross-compilation setup
│   ├── file_license_check.sh        # License compliance verification
│   ├── install_ubuntu_dependencies.sh # System dependency installation
│   ├── install_ubuntu_dependencies-cross_compile.sh # Cross-compilation dependencies
│   ├── install_ubuntu_dependencies-rust-arm64.sh # ARM64 Rust toolchain setup
│   ├── install_ubuntu_dependencies-rust.sh # Rust installation automation
│   ├── multinet_envs.sh             # Multi-network environment configuration
│   └── [cross-compilation scripts]  # Platform-specific build scripts
├── supply-chain/                    # Supply chain security configuration
│   ├── config.toml                  # Cargo-vet security exemptions and policies
│   ├── audits.toml                  # Security audit results (currently empty)
│   └── imports.lock                 # Imported audit data (currently empty)
├── target/                          # Build artifacts (ignored by git)
├── Cargo.toml                       # Workspace configuration
├── Cargo.lock                       # Dependency lock file
├── Dockerfile                       # Standard container build
├── Dockerfile.cross-compile         # Multi-platform cross-compilation container
├── Cross.toml                       # Cross-compilation configuration
├── log4rs_sample.yml                # Sample logging configuration
├── log4rs_detailed.yml              # Detailed logging for diagnostics
└── [configuration files]            # Linting, formatting, toolchain configs
```

### Overall Architecture

#### **Layered Service-Oriented Architecture with Domain-Driven Design**

SHA-P2Pool implements a sophisticated layered architecture with clear separation of concerns and strong domain boundaries:

#### 1. **CLI Interface Layer** (`cli/`)
- **Command Processing**: Comprehensive command-line tool with subcommands for node management, diagnostics, identity generation, and squad discovery
- **Configuration Management**: Builder pattern for flexible server configuration with extensive customization options and environment variable integration
- **Bootstrap Orchestration**: `commands/util.rs` serves as the dependency injection container, coordinating complex service initialization sequences

#### 2. **Service Orchestration Layer** (`server/`)
- **Service Coordination**: `server.rs` acts as the architectural backbone, managing service lifecycle and dependency injection
- **API Gateway Patterns**: Dual-purpose services providing both complete Tari base node proxy functionality and P2Pool-specific mining interfaces
- **Cross-Cutting Concerns**: Logging, monitoring, configuration, and graceful shutdown coordination

#### 3. **API Layer** (`grpc/`, `http/`)
- **gRPC Services**: 
  - `base_node.rs` - **Miner Integration Bridge**: Complete Tari base node API passthrough enabling seamless miner integration
  - `p2pool.rs` - **Mining Interface**: P2Pool-specific mining operations with dual-algorithm support
- **HTTP Services**: RESTful API for statistics, health monitoring, peer information, and external integrations
- **Service Integration**: Clean interfaces between external clients and internal business logic

#### 4. **Networking Layer** (`p2p/`)
- **P2P Networking**: `network.rs` serves as the **Decentralized Nervous System** using libp2p with gossipsub for broadcasting and request-response for synchronization
- **Peer Management**: `peer_store.rs` provides **Network Intelligence** with sophisticated peer categorization and reputation management
- **NAT Traversal**: Relay store management enabling nodes behind firewalls to participate via relay connections

#### 5. **Core Business Logic** (`sharechain/`)
- **Share Chain Consensus**: `p2chain.rs` is the **Consensus Heart** managing in-memory side chain with sophisticated validation and LWMA difficulty adjustment
- **Domain Services**: `in_memory.rs` implements the ShareChain trait as the primary domain service interface
- **Block Management**: Comprehensive validation including difficulty, timestamps, uncles, and proportional reward distribution

#### 6. **Storage Layer**
- **LMDB Persistence**: Lightning Memory-Mapped Database for durable block storage with automatic resizing and migration support
- **In-Memory Caching**: Performance-optimized caching for active chain operations, peer information, and template management
- **Statistics Collection**: Real-time metrics aggregation via `stats_collector.rs` providing **Operational Intelligence**

#### 7. **Testing Infrastructure** (`integration_tests/`)
- **End-to-End Testing**: Comprehensive Cucumber BDD framework with realistic multi-node network simulation
- **Process Management**: Real process testing with actual binaries and network communication
- **Test Orchestration**: `world.rs` coordinates complex integration testing scenarios

### Core Business Logic and Workflows

#### **Architectural Pattern: Event-Driven Domain Services with Consensus Coordination**

#### Mining Pool Operation Workflow
1. **Miner Connection**: Miners connect via `grpc/base_node.rs` proxy that mimics complete Tari base node API for seamless integration
2. **Template Generation**: `grpc/p2pool.rs` generates block templates via `sharechain/in_memory.rs` including share chain coinbases with proportional reward distribution
3. **Block Submission**: Miners submit blocks which undergo comprehensive validation via `sharechain/p2chain.rs` consensus logic and are added to the share chain
4. **Reward Distribution**: Automatic payout calculation based on recent share contributions with configurable share windows
5. **Main Chain Submission**: Valid blocks are forwarded to actual Tari base node for inclusion in the main blockchain

#### Share Chain Management (Core Consensus)
1. **Block Addition**: New blocks undergo multi-stage validation including difficulty verification, timestamp checks, output validation, and uncle verification via `sharechain/p2chain.rs`
2. **Uncle Rewards**: Side-chain blocks receive reduced rewards (4 vs 5 shares) to encourage decentralization while providing incentives
3. **Chain Reorganization**: Automatic reorg detection and handling when higher difficulty chains are discovered
4. **Difficulty Adjustment**: Linear Weighted Moving Average (LWMA) algorithm for stable block times and network stability
5. **Chain Pruning**: Configurable retention windows for memory management via `lmdb_block_storage.rs`

#### P2P Network Operations (Decentralized Coordination)
1. **Peer Discovery**: mDNS for local network discovery and seed peers for bootstrap connectivity via `p2p/setup.rs`
2. **Block Broadcasting**: Gossipsub topics for efficient block propagation across the network via `p2p/network.rs`
3. **Chain Synchronization**: Request-response protocols for missing block retrieval and catch-up synchronization
4. **Peer Management**: Sophisticated peer categorization via `p2p/peer_store.rs` with reputation tracking
5. **NAT Traversal**: Relay store management via `p2p/relay_store.rs` for enabling nodes behind NAT/firewalls

### Key Technical Patterns and Architectural Decisions

#### 1. **Domain-Driven Design (DDD) Patterns**
- **Aggregates**: Share chain as aggregate root with rich business logic in `sharechain/p2chain.rs`
- **Domain Services**: `sharechain/in_memory.rs` as main domain service coordinating consensus operations
- **Repository Pattern**: Abstracts storage via `lmdb_block_storage.rs` with clean domain interfaces
- **Value Objects**: P2Block, P2BlockHeader with domain-specific validation and invariants

#### 2. **Service-Oriented Architecture (SOA) Patterns**
- **Service Orchestration**: `server/server.rs` coordinates multiple async services with dependency injection
- **Proxy Pattern**: `grpc/base_node.rs` provides complete API passthrough for seamless miner integration
- **Facade Pattern**: Simplified interfaces hiding complex subsystem interactions
- **Strategy Pattern**: Dual-algorithm support (RandomX/SHA3x) with pluggable implementations

#### 3. **Event-Driven Architecture Patterns**
- **Publisher-Subscriber**: `stats_collector.rs` broadcasts statistics to multiple consumers
- **Event Sourcing**: P2P network events drive peer management and synchronization decisions
- **Observer Pattern**: Real-time statistics collection and distribution across components

#### 4. **Async/Await Concurrency Patterns**
- **Actor-like Concurrency**: Heavy use of tokio runtime for async operations
- **Channel-based Communication**: Inter-service coordination with proper error handling
- **Semaphore Coordination**: Sync operations use semaphores to prevent race conditions
- **Graceful Shutdown**: Coordinated shutdown using tari_shutdown for clean resource cleanup

#### 5. **Network Resilience Patterns**
- **Circuit Breaker**: Connection management with backoff and retry logic
- **Bulkhead**: Isolated connection pools for different peer types
- **Timeout Management**: Configurable timeouts with proper error propagation
- **Peer Reputation**: Sophisticated scoring system for network reliability

#### 6. **Performance Optimization Patterns**
- **LRU Caching**: Template and tip info caching for reduced latency
- **Memory Mapping**: LMDB storage for high-performance persistent data access
- **Batch Processing**: Efficient block validation and network message processing
- **Connection Pooling**: Reusable connections with automatic lifecycle management

### Important Dependencies and Integrations

#### **Tari Ecosystem Integration** (Seamless Blockchain Integration)
- **minotari_app_grpc**: Complete base node gRPC interface compatibility for seamless miner integration
- **minotari_node_grpc_client**: Client for actual base node communication and blockchain interaction
- **tari_core**: Consensus rules, block structures, proof-of-work validation, and blockchain primitives
- **tari_crypto**: Cryptographic primitives, address handling, and key management
- **tari_common_types**: Shared type definitions across the Tari ecosystem

#### **Networking and Communication** (Decentralized P2P Foundation)
- **libp2p**: Core P2P networking with gossipsub, mDNS, relay support, and NAT traversal capabilities
- **tonic**: High-performance gRPC server and client implementation for mining interfaces
- **axum**: Modern HTTP server framework for REST APIs with middleware support
- **tokio**: Async runtime providing the foundation for concurrent operations

#### **Storage and Serialization** (Performance and Persistence)
- **rkv/lmdb**: High-performance persistent storage with memory-mapped access
- **serde**: Comprehensive serialization framework with CBOR and JSON support
- **bincode**: Efficient binary serialization for storage operations

#### **Cryptography and Mining** (Blockchain Security)
- **blake2**: Domain-separated hashing for blocks and security operations
- **randomx**: RandomX proof-of-work algorithm support for Tari compatibility
- **hex**: Hexadecimal encoding/decoding utilities for data representation

### Database Schema and Storage Architecture

#### **LMDB Block Storage** (High-Performance Persistence)
- **Key-Value Structure**: Block hash → Serialized P2Block for efficient retrieval
- **Migration Support**: Schema versioning for seamless upgrades and compatibility
- **Automatic Resizing**: Dynamic storage expansion to handle growing chain data
- **Performance Optimization**: Memory-mapped access patterns for high throughput

#### **In-Memory Data Structures** (Performance-Critical Operations)
- **Share Chain**: Height-indexed block levels with main/side chain tracking for fast reorganization
- **Peer Store**: Categorized peer management with health metrics, reputation scoring, and connection tracking
- **Template Cache**: LRU caches for mining template optimization and reduced latency
- **Statistics**: Real-time metrics collection and aggregation for monitoring and analysis

### API Structure and Service Interfaces

#### **gRPC Interface (Port 18145)** - Primary Mining Interface
- **get_new_block_template_with_coinbases**: Mining template generation with share-based rewards
- **submit_block**: Block submission with comprehensive validation and network propagation
- **get_tip_info**: Chain tip information with caching for performance
- **Base Node Proxy**: Complete passthrough of Tari base node APIs for seamless miner integration

#### **HTTP REST API (Configurable Port)** - Monitoring and Management
- **GET /health**: Service health check for monitoring systems
- **GET /version**: Version information for compatibility verification
- **GET /stats**: Comprehensive mining and network statistics
- **GET /miners**: Active miner information and share distribution
- **GET /chain**: Share chain status and synchronization information
- **GET /peer**: Peer connection status and network health
- **GET /connections**: Detailed network connection information

### Testing Architecture and Quality Assurance

#### **Multi-Layer Testing Strategy**
- **Unit Testing**: Module-level testing for core logic validation with comprehensive coverage
- **Integration Testing**: Cucumber BDD framework with Gherkin scenarios for behavior specification
- **End-to-End Testing**: Multi-node network simulation via `integration_tests/world.rs` for realistic testing environments
- **Performance Testing**: Load testing scenarios for production readiness validation

#### **Behavior-Driven Development (BDD)**
- **Feature Files**: Gherkin scenarios in `integration_tests/tests/features/`
- **Step Definitions**: Reusable test steps in `integration_tests/tests/steps/`
- **World Coordination**: `world.rs` manages complex multi-node test scenarios
- **Process Testing**: Real binary execution and network communication validation

#### **Continuous Integration Pipeline**
- **Multi-Platform Builds**: Linux, macOS, Windows with cross-compilation verification
- **Code Quality**: Automated clippy, rustfmt, and security audit checks
- **Test Execution**: Comprehensive unit and integration test runs with proper reporting
- **Supply Chain Security**: cargo-vet framework for dependency verification

### Build and Deployment Architecture

#### **Development and Cross-Platform Support**
```bash
## Development Build
cargo build --release
./target/release/sha_p2pool start

## Cross-Platform Compilation
cross build --target aarch64-unknown-linux-gnu --release
```

#### **Container Deployment Strategy**
- **Multi-Stage Builds**: Docker with cargo-chef optimization for efficient caching
- **Cross-Compilation**: Support for x86_64, ARM64, RISC-V on Linux; universal macOS; Windows
- **Security**: Non-root execution with tini init system for proper signal handling
- **Configuration**: Environment variable and volume mount support for flexible deployment

#### **Release and Distribution**
- **Automated Builds**: GitHub Actions for all supported platforms with proper caching
- **Code Signing**: macOS notarization and Windows signing for distribution trust
- **Artifacts**: Signed binaries, checksums, and installer packages for easy deployment
- **Multi-Network Support**: Automatic network targeting based on deployment context

### Security Architecture and Considerations

#### **Network Security**
- **Peer Authentication**: Identity-based peer verification via `p2p/util.rs` and reputation management
- **Message Validation**: Comprehensive input validation and sanitization for all network messages
- **DoS Protection**: Rate limiting, connection management, and resource usage monitoring

#### **Cryptographic Security**
- **Secure Hashing**: Domain-separated Blake2b hashing for all security-critical operations
- **Key Management**: Secure identity generation, storage, and rotation capabilities via `cli/commands/generate_identity.rs`
- **Consensus Validation**: Full proof-of-work verification and blockchain validation

#### **Code Security and Supply Chain**
- **Memory Safety**: Rust's ownership system preventing memory vulnerabilities and data races
- **Dependency Auditing**: Automated security scanning with cargo-audit and supply chain verification via `supply-chain/`
- **Input Validation**: Strict validation of all external inputs with proper error handling
- **License Compliance**: Automated checking via `scripts/file_license_check.sh` for legal compliance

### Operational Architecture and Monitoring

#### **Observability and Monitoring**
- **Statistics Collection**: `stats_collector.rs` provides comprehensive operational intelligence for mining performance, network health, and system operation
- **Diagnostic Mode**: Specialized configuration via `cli/commands/diagnostics.rs` for network analysis and troubleshooting
- **Health Endpoints**: HTTP endpoints for service monitoring and alerting integration
- **Structured Logging**: Detailed logging with configurable levels and multiple output destinations

#### **Deployment and Operations**
- **Resource Requirements**: Moderate CPU and memory requirements with configurable limits
- **Network Connectivity**: Requires stable internet connection for P2P networking and base node communication
- **Storage Management**: Configurable storage retention with automatic cleanup capabilities
- **High Availability**: Support for redundant deployments and failover scenarios

#### **Multi-Network Operations**
- **Environment Configuration**: Automated network targeting via `scripts/multinet_envs.sh` based on deployment context
- **Network Isolation**: Support for mainnet, testnet, and development networks
- **Configuration Management**: Environment-specific settings and parameters

### Development Practices and Quality Standards

#### **Code Quality and Standards**
- **Linting**: Comprehensive clippy rules via `clippy.toml` for code quality and best practices
- **Formatting**: Consistent code formatting with rustfmt configuration via `rustfmt.toml`
- **Documentation**: Inline documentation and comprehensive API documentation
- **Code Review**: Structured pull request process with automated checks

#### **Commit and Branch Management**
- **Conventional Commits**: Enforced commit message standards via `.github/workflows/pr_title.yml` for better changelog generation
- **Signed Commits**: GPG signature verification via `.github/workflows/pr_signed_commits_check.yml` for commit authenticity
- **Branch Protection**: Automated checks preventing direct pushes to main branches

#### **Dependency and Security Management**
- **Security Scanning**: Automated vulnerability detection via `.github/workflows/audit.yml`
- **License Compliance**: Verification of dependency license compatibility via `scripts/file_license_check.sh`
- **Supply Chain Verification**: Cryptographic verification of dependency integrity via `supply-chain/config.toml`

### Key Architectural Insights from Holistic Analysis

#### **Central Integration Patterns**
1. **Service Orchestration Hub**: `server/server.rs` serves as the central coordination point, implementing dependency injection and service lifecycle management
2. **Domain Service Bridge**: `sharechain/in_memory.rs` acts as the primary interface between networking/API layers and core consensus logic
3. **Miner Integration Proxy**: `grpc/base_node.rs` enables seamless miner adoption by providing complete Tari base node API compatibility
4. **Network Intelligence Center**: `p2p/peer_store.rs` and `p2p/network.rs` work together to provide sophisticated P2P networking with resilience patterns

#### **Performance-Critical Paths**
1. **Mining Template Generation**: `grpc/p2pool.rs` → `sharechain/in_memory.rs` → `sharechain/p2chain.rs` with LRU caching
2. **Block Submission Pipeline**: Validation → `sharechain/p2chain.rs` → `p2p/network.rs` broadcasting → `lmdb_block_storage.rs` persistence
3. **Chain Synchronization**: `p2p/network.rs` → `peer_store.rs` → `sharechain/in_memory.rs` with semaphore coordination

#### **Consensus and Network Coordination**
- **Dual-Algorithm Support**: Sophisticated coordination between RandomX and SHA3x chains with shared infrastructure
- **Uncle Block Management**: Incentivized decentralization through reduced but significant uncle rewards
- **Peer Reputation System**: Advanced networking with categorized peers and intelligent selection strategies

This codebase represents a sophisticated implementation of decentralized mining pool technology, demonstrating advanced Rust programming patterns, comprehensive networking protocols, rigorous testing methodologies, and integration with blockchain consensus mechanisms. The architecture balances performance, security, and maintainability while providing a robust foundation for decentralized mining operations in the Tari ecosystem. The holistic analysis reveals a well-architected system with clear separation of concerns, sophisticated networking capabilities, and comprehensive operational tooling.

## Codebase Structure

### Root Directory

| File | Description |
|------|-------------|
| `.dockerignore` | Docker build context exclusion configuration. Excludes Docker-related files, Node.js artifacts (node_modules, npm files), Git repository (.git, .gitignore, .github), and Rust build artifacts (target/). Optimizes Docker build performance by reducing build context size and avoiding unnecessary file transfers to Docker daemon. |
| `.gitignore` | Git ignore configuration for SHA-P2Pool project. Excludes Rust build artifacts (debug/, target/, data/), backup files (*.rs.bk), debug symbols (*.pdb), OS files (.DS_Store), IDE files (.idea/), and other temporary files. Standard Rust project exclusions with additional data directory for application runtime files. |
| `.license.ignore` | License header check exclusion list. Specifies files that should not be checked for license headers by automated license verification scripts. Currently excludes Dockerfile.cross-compile from license header requirements, likely due to special formatting or generated content constraints. |
| `CODEOWNERS` | GitHub CODEOWNERS configuration defining code review requirements. Assigns @tari-project/devops team ownership of CI/CD files (.github/**/*) and CODEOWNERS file itself, ensuring DevOps team reviews all infrastructure and deployment-related changes. |
| `Cargo.lock` | [GENERATED] Cargo dependency lock file ensuring reproducible builds. Contains exact versions and checksums for all dependencies including cryptography (aes, blake2, digest), networking (libp2p, tokio, tonic), Tari ecosystem libraries, and testing frameworks. Auto-generated by Cargo and should not be manually edited. Critical for consistent builds across environments and deployments. |
| `Cargo.toml` | Root workspace configuration for SHA-P2Pool project. Defines workspace members (p2pool, integration_tests), package metadata (authors: Tari Development Community, BSD-3-Clause license, version 1.0.3), edition 2021 with resolver 2. Configures release profile with overflow checks, optimization level 3, and debug symbols support for heap profiling with dhat feature. |
| `Cross.toml` | Cross-compilation configuration for multiple architectures (aarch64, x86_64, riscv64gc Linux). Defines environment variable passthrough for build flags, networking configs, and Tari-specific variables. Uses Ubuntu 18.04/22.04 base images with pre-build scripts for dependency installation. Enables cross-compilation from any host to target architectures with proper toolchain setup. |
| `Dockerfile` | Multi-stage Docker build for SHA-P2Pool binary. Uses cargo-chef for dependency caching, builds with configurable profile/features/RUSTFLAGS, installs system dependencies (libclang, protobuf, cmake, libudev), and creates Ubuntu runtime image with tini init system. Supports build arguments for customization and properly handles binary extraction from target directory. |
| `Dockerfile.cross-compile` | Multi-stage Docker build configuration for cross-platform compilation of SHA-P2Pool. Supports multiple architectures (x86_64, ARM64, RISC-V) using platform-specific build arguments, configurable Rust toolchains, custom build profiles and features, cross-compilation tooling setup, and runtime optimization. Creates minimal production image with tini init system, non-root user execution, and proper security practices. Enables efficient containerized deployment across different hardware platforms. |
| `LICENSE` | BSD 3-Clause License for SHA-P2Pool project. Copyright 2024 The Tari Project. Permits redistribution and use with conditions: retain copyright notice, include disclaimer in binaries, no endorsement using names. Standard open-source license with disclaimer of warranties and liability limitations. |
| `README.md` | Main project documentation explaining SHA-P2Pool: a decentralized pool mining software for Tari network using SHA-3 algorithm. Describes how it implements Bitcoin's original p2pool with modifications, using in-memory side chain (share chain) to track miners and provide instant decentralized payouts. Includes prerequisites (Tor proxy, running Tari Base Node, wallet address), setup instructions, and usage commands. Entry point documentation for users and developers. |
| `ci_all.sh` | Comprehensive CI pipeline script for local development. Runs complete validation suite: cargo +nightly ci-fmt (formatting), cargo machete (unused dependencies), cargo ci-check (compilation), cargo ci-test (unit tests), and cargo ci-cucumber (integration tests). Provides single command for full project validation before committing changes. |
| `clippy.toml` | Clippy linter configuration. Sets cognitive complexity threshold to 15, allows up to 12 function arguments, and sets enum variant size threshold to 216 bytes (sized for RistrettoPublicKey). Customizes Clippy analysis for cryptocurrency/blockchain development patterns with appropriate complexity limits. |
| `lints.toml` | Comprehensive lint configuration using cargo-lints. Denies critical issues (correctness, unused code, common mistakes), style violations, casting issues, and performance problems. Allows certain patterns common in blockchain development (too_many_arguments, bool_assert_comparison). Specifically configures lints for cryptocurrency code patterns and debugging practices. |
| `log4rs_detailed.yml` | Detailed log4rs configuration with extensive logging categories and higher file size limits (100MB vs 10MB). Includes appenders for sync operations, P2P networking, and other system components with debug-level logging for troubleshooting. Provides more granular logging control for development and diagnostic purposes compared to the sample configuration. |
| `log4rs_sample.yml` | Comprehensive log4rs logging configuration template for P2Pool operations. Defines multiple rolling file appenders for different components (p2pool, sync, peers, p2p, grpc, messages) with 10MB rotation and 5-file retention. Includes detailed pattern formatting with timestamps, log levels, and source file information. Configures specific loggers for various P2Pool modules with appropriate log levels and output destinations. Supports debugging, monitoring, and troubleshooting of P2Pool network operations. |
| `rust-toolchain.toml` | Rust toolchain specification pinning project to nightly-2024-09-05. Ensures consistent compilation environment across development and CI/CD. Required for unstable features used in the project (likely async, const generics, or advanced trait features). |
| `rustfmt.toml` | Rust code formatting configuration for edition 2021. Defines formatting rules: 120 character line width, trailing commas, import reordering, grouping by StdExternalCrate, single-line structs, comment formatting, and various nightly formatting features. Enforces consistent code style across the project with comprehensive formatting rules. |

### .cargo/

| File | Description |
|------|-------------|
| `audit.toml` | Cargo security audit configuration specifying ignored security advisories. Ignores RUSTSEC-2021-0145 (special allocator issue not applicable) and RUSTSEC-2023-0071 (RSA vulnerability not used). Used by cargo-audit to filter known non-applicable security warnings during dependency scanning in CI/CD pipelines. |
| `config.toml` | Cargo build configuration defining cross-compilation linkers and CI aliases. Sets linkers for ARM64 (aarch64-linux-gnu-gcc) and RISC-V (riscv64-linux-gnu-gcc) targets. Defines CI command aliases: ci-fmt (format check), ci-clippy (lint), ci-test (nextest), ci-cucumber (integration tests), ci-check (compilation check). Streamlines development and CI workflows with consistent commands. |

### .config/

| File | Description |
|------|-------------|
| `nextest.toml` | Nextest (modern Rust test runner) configuration with CI and IntelliJ profiles. CI profile: 60s slow test timeout, JUnit XML output for CI integration. IntelliJ profile: 30s timeout, immediate failure output, no retries, optimized for IDE development. Improves test execution performance and CI/CD integration with better reporting and timeout handling. |

### .github/

| File | Description |
|------|-------------|
| `PULL_REQUEST_TEMPLATE.md` | GitHub pull request template ensuring consistent PR format. Includes sections for description, motivation/context, testing details, reviewer verification process, and breaking changes checklist. Emphasizes proper titles for release notes and includes conventional commit guidelines. Helps maintain code quality and streamlines review process with structured information gathering. |
| `dependabot.yml` | Dependabot configuration for automated dependency updates. Monitors GitHub Actions ecosystem in root directory with weekly update schedule. Ensures CI/CD workflows stay current with latest action versions for security and feature updates. |

#### .github/ISSUE_TEMPLATE/

| File | Description |
|------|-------------|
| `bug_report.md` | GitHub issue template for bug reports with structured format. Includes sections for bug description, reproduction steps, expected behavior, screenshots, desktop/mobile environment details. Auto-labels issues as 'bug-report' and provides consistent format for easier triage and debugging. Helps maintainers gather necessary information for effective bug resolution. |

#### .github/workflows/

| File | Description |
|------|-------------|
| `audit.yml` | GitHub Actions workflow configuration for daily security audits. Automatically runs RustSec security audit using rustsec/audit-check action on multiple triggers: when dependencies change (Cargo.toml/Cargo.lock), when audit configuration changes, daily at 5:43 AM via cron schedule, and on manual dispatch. Uses cargo-audit to scan dependencies for known security vulnerabilities and generates security reports. Essential for maintaining supply chain security and early detection of vulnerable dependencies in the project. |
| `build_binaries.json` | Build matrix configuration JSON file defining cross-platform compilation targets for GitHub Actions binary builds. Specifies 7 target platforms: Linux x86_64 (with nightly Rust and metric builds), Linux ARM64, Linux RISC-V64, macOS x86_64, macOS ARM64, Windows x64, and Windows ARM64. Each entry includes platform name, runner OS, Rust toolchain version, target triple, cross-compilation settings, and build flags. Used by build_binaries.yml workflow to generate release artifacts for multiple architectures and operating systems. |
| `build_binaries.yml` | Comprehensive binary build workflow for multiple platforms (Linux, macOS, Windows) and architectures (x86_64, arm64, RISC-V). Triggers on version tags, build branches, schedule, and manual dispatch. Features matrix-based builds from JSON config, cross-compilation support, platform-specific dependency installation, code signing (macOS/Windows), notarization, installer creation, checksum verification, artifact uploading, and S3 synchronization. Creates GitHub releases with signed binaries and universal macOS packages. Includes extensive environment configuration and build optimization. |
| `build_docker.yml` | Comprehensive GitHub Actions workflow for building multi-architecture Docker images of sha-p2pool. Features sophisticated build pipeline with environment setup, QEMU for cross-compilation, multi-registry publishing (GitHub Container Registry and Quay.io), platform-specific builds (amd64/arm64), automatic image tagging with metadata, expiration policies for non-release builds, multi-arch manifest creation, and network-specific configuration support via multinet_envs.sh. Supports scheduled builds, manual dispatch with custom versioning, and automatic builds on tags/branches. Creates optimized Docker images using cross-compilation Dockerfile with proper security labels and annotations. |
| `ci.yml` | Main CI/CD workflow for GitHub Actions. Runs on push to ci-* branches, PRs, and merge groups. Uses nightly Rust toolchain (2024-09-19). Jobs: ci (format, clippy, machete, vet), build-stable (stable toolchain compatibility), licenses (file license verification), test (nextest with network matrix), artifacts (PR number upload), event_file (test results). Features caching strategies, Ubuntu dependencies installation, and test result publishing. Excludes integration_tests from main test runs. |
| `coverage.yml` | GitHub Actions workflow for source code coverage analysis. Runs on development and ci-coverage branches using self-hosted Ubuntu runners with high memory. Installs system dependencies, uses nightly Rust toolchain, executes tests with coverage instrumentation via source_coverage.sh script, and uploads results to Coveralls for coverage reporting. Configured to continue on test errors to ensure coverage data is always generated. |
| `integration_tests.yml` | GitHub Actions workflow for Cucumber-based integration testing. Runs on PRs, merge groups, and manual dispatch using self-hosted Ubuntu high-CPU runners. Builds sha_p2pool binary, executes Cucumber tests with @critical tag filter excluding @long-running and @broken tests, supports 5 concurrent scenarios with 2 retries, and uploads JUnit XML test results as artifacts. Uses local caching for performance and includes environment setup for test execution. |
| `pr_signed_commits_check.yml` | GitHub Actions workflow for validating commit signature verification in pull requests. Uses 1Password's check-signed-commits-action to ensure all commits in PRs are properly signed with GPG keys, enforcing security best practices for code integrity. Triggers on pull_request_target events with proper concurrency management to prevent conflicts. Essential for maintaining authenticated commit history and preventing unauthorized code modifications in the repository. |
| `pr_title.yml` | GitHub Actions workflow enforcing Conventional Commits standard for pull request titles. Automatically validates PR title format using @commitlint/cli and @commitlint/config-conventional npm packages when PRs are opened, reopened, edited, or synchronized. Ensures consistent commit message formatting following the Conventional Commits specification (https://www.conventionalcommits.org/) which improves changelog generation, semantic versioning, and project maintainability through standardized commit message structure. |

### integration_tests/

| File | Description |
|------|-------------|
| `Cargo.toml` | Integration test suite package configuration. Depends on Tari ecosystem libraries (minotari_app_grpc, minotari_node, tari_core, etc.) pinned to specific git revision, sha_p2pool main package, and testing frameworks (cucumber with libtest/output-junit features). Uses same networking libraries as main package (libp2p, tokio, tonic). Configures cucumber test target with harness disabled for proper test output formatting. Used for end-to-end behavior-driven testing. |
| `README.md` | Integration test documentation for Tari cucumber tests. Explains test execution commands (cargo test --test cucumber), filtering by test name (-n), tags (@critical, @long-running, @broken), and input files (-i). Documents CI strategy: PR runs @critical tests, nightly runs non-@long-running tests, weekly runs @long-running tests. Notes work-in-progress status with @broken scenarios and commented-out tests requiring implementation. |

##### integration_tests/.husky/_/

| File | Description |
|------|-------------|
| `husky.sh` | Husky Git hooks initialization script for automated testing and code quality enforcement. Provides core Git hook infrastructure with debug support, configuration loading from ~/.huskyrc, environment variable control (HUSKY=0 to skip), and proper error handling with exit codes. This script bootstraps Git hooks to run integration tests, linting, formatting, or other validation steps automatically on Git operations like commits and pushes, ensuring code quality and preventing broken commits from entering the repository. |

#### integration_tests/log4rs/

| File | Description |
|------|-------------|
| `cucumber.yml` | Comprehensive logging configuration for Cucumber BDD integration tests using log4rs framework. Defines multiple rolling file appenders (stdout, cucumber, base_node, other) with 100MB size limits and automatic log rotation. Configures structured logging with timestamps, thread IDs, log levels, and file/line information. Separates log outputs by component (cucumber test framework, base node operations, P2P communications) with appropriate log levels and filtering. Essential for debugging integration test failures and monitoring system behavior during end-to-end testing scenarios. |

#### integration_tests/src/

| File | Description |
|------|-------------|
| `base_node_process.rs` | Tari base node process management for P2Pool integration testing. Spawns and configures Tari base nodes with LocalNet settings, manages node identity and peer connections, handles gRPC service setup, implements proper shutdown procedures with port cleanup, provides base node client connectivity, and manages temporary directories. Essential for creating realistic test environments where P2Pool nodes can connect to actual Tari base nodes during integration testing. |
| `lib.rs` | Integration test suite library providing shared utilities for cucumber testing. Exports test process management (P2PoolProcess, BaseNodeProcess, TariWorld), port allocation with conflict avoidance, service startup waiting, and peer address resolution. Includes get_port() for random port selection avoiding conflicts, wait_for_service() for async service startup, and base directory management for test isolation. Core testing infrastructure for end-to-end P2Pool functionality verification. |
| `miner.rs` | Mining simulation utilities for integration testing P2Pool functionality. Provides mine_and_submit_tari_blocks() for automated block generation and submission to P2Pool nodes, new_random_dual_tari_address() for creating test wallet addresses, find_sha3x_header_with_achieved_difficulty() for SHA3x proof-of-work mining simulation, and verify_block_height() for validating chain synchronization via HTTP stats endpoints. Includes comprehensive error handling, timeout management, and statistical verification for end-to-end testing of mining operations, block propagation, and network consensus in multi-node P2Pool environments. |
| `p2pool_process.rs` | P2Pool process management for integration testing. Spawns and manages P2Pool instances with configurable parameters (seed nodes, diagnostic mode, squads, ports), handles process lifecycle (start, stop, restart), implements gRPC client connectivity, supports peer verification through HTTP API, manages temporary directories and configuration files, provides command-line argument conversion, and includes libp2p identity management. Critical for end-to-end testing of P2Pool network behavior and process interactions. |
| `world.rs` | **Cucumber World implementation - integration test orchestration coordinator enabling end-to-end P2Pool validation.** Manages comprehensive test environment state including multiple P2Pool nodes via `p2pool_process.rs`, multiple Tari base nodes via `base_node_process.rs`, dynamic seed node lists for network topology simulation, and isolated test artifacts for scenario separation. Provides sophisticated node management utilities with lifecycle coordination, gRPC client access for service interaction, process lifecycle tracking with proper cleanup, and test isolation through scenario-specific directories preventing test interference.  **Testing Architecture**: Central test coordination component implementing the World pattern for Cucumber BDD testing. Enables complex multi-node integration testing scenarios including network partition simulation, chain synchronization validation, peer discovery testing, and consensus behavior verification across diverse network topologies and failure scenarios.  **Integration Test Support**: Core testing infrastructure enabling end-to-end validation of P2Pool functionality through realistic network simulation. Connects to actual `sha_p2pool` binaries via `p2pool_process.rs`, real Tari base nodes for blockchain integration, HTTP APIs for statistics validation via `miner.rs`, and P2P networking for peer behavior testing.  **Scenario Management**: Provides proper test cleanup and debugging support for complex integration scenarios, manages temporary directories and configuration files, coordinates test timing and synchronization, and enables realistic failure simulation for robustness testing. Critical for validating P2Pool's consensus behavior, network resilience, and mining pool operations.  **Quality Assurance**: Essential for ensuring P2Pool reliability by enabling comprehensive end-to-end testing of mining operations, chain synchronization, peer discovery, network partitions, and consensus correctness. Validates integration between `sharechain/`, `p2p/`, `grpc/`, and external Tari components through realistic multi-node scenarios. |

#### integration_tests/tests/

| File | Description |
|------|-------------|
| `cucumber.rs` | Main Cucumber BDD test runner for sha-p2pool integration tests. Configures test environment with LocalNet network settings, handles automatic sha_p2pool binary building in release mode with conditional rebuild logic, initializes structured logging with stdout capture, and sets up comprehensive test execution pipeline. Configures single-threaded scenario execution for mDNS compatibility, implements scenario lifecycle management with before/after hooks, generates JUnit XML reports for CI integration, and provides detailed test output with proper error handling and logging for debugging test failures. |

##### integration_tests/tests/features/

| File | Description |
|------|-------------|
| `Sync.feature` | Comprehensive Gherkin BDD feature file testing P2Pool node synchronization scenarios. Defines critical test cases for: new node sync with existing peers on startup, block loading from persistent storage on restart, offline nodes catching up after reconnection, block propagation participation across multiple nodes, squad isolation ensuring different squads maintain separate chains, and diagnostic node functionality for network analysis. Each scenario validates proper peer connections, block height synchronization, chain integrity, and network behavior in various operational conditions including node failures and network partitions. |

##### integration_tests/tests/steps/

| File | Description |
|------|-------------|
| `base_node_steps.rs` | Cucumber step definitions for Tari base node management in integration tests. Implements Gherkin step handlers for spawning different types of base nodes: seed nodes (with bootstrap capabilities), regular nodes, and nodes connected to all seed nodes. Uses cucumber macros for natural language test step mapping, includes comprehensive error handling with panic on failures, integrates with TariWorld test context for node lifecycle management, and provides configurable delays for proper node initialization and network stabilization during test execution. |
| `mod.rs` | Integration test step definitions module providing common Cucumber step implementations and test utilities. Exports base_node_steps and p2pool_steps modules, defines timing constants for test synchronization (CONFIRMATION_PERIOD, TWO_MINUTES_WITH_HALF_SECOND_SLEEP), and implements generic wait steps for test coordination including 'I wait X seconds' for test continuation and 'I wait X seconds and stop' for test completion. Also provides error validation steps for testing expected failure scenarios and error message verification in integration tests. |
| `p2pool_steps.rs` | Comprehensive Cucumber step definitions for P2Pool node operations in integration tests. Implements Gherkin step handlers for: spawning P2Pool seed nodes, regular nodes, and diagnostic nodes with squad/base node associations; mining and submitting blocks to specific nodes; verifying block heights and peer connections via HTTP stats endpoints; node lifecycle management including shutdown and restart operations; and diagnostic file generation validation with timeout support. Provides robust error handling, logging, and test synchronization delays for reliable end-to-end P2Pool network testing scenarios. |

### p2pool/

| File | Description |
|------|-------------|
| `Cargo.toml` | Main sha_p2pool package configuration. Defines dependencies: async/networking (tokio, libp2p, axum), Tari ecosystem (minotari_app_grpc, tari_core, tari_crypto), cryptography (blake2, digest), serialization (serde, bincode, cbor), storage (rkv/lmdb), logging (log4rs), and utilities. Configures both library (src/lib.rs) and binary (src/main.rs) targets. Includes dhat-heap feature for memory profiling. Key dependencies pinned to specific Tari git revision. |

#### p2pool/src/

| File | Description |
|------|-------------|
| `lib.rs` | Main library interface for SHA-P2Pool. Exports key public APIs: CLI (handle_start, run_with_cli, Cli, Commands, StartArgs), server configuration (ShaP2PoolConfig), P2P utilities (generate_identity), and sharechain components (LmdbBlockStorage, P2BlockBuilder, P2Chain). Defines profiling log target constant and utility error creation function. Serves as main entry point for external consumers of the library. |
| `main.rs` | Application entry point for SHA-P2Pool binary. Sets up multi-threaded tokio runtime, configures DHAT heap profiler when enabled, implements panic handling with custom logging and file output (panic.log), installs Ctrl-C signal handlers, and delegates to CLI command handling. Uses tari_shutdown for graceful shutdown coordination. Includes system time formatting utilities and proper cleanup of profiling resources. |

##### p2pool/src/cli/

| File | Description |
|------|-------------|
| `args.rs` | Command-line interface definitions using clap. Defines StartArgs with extensive configuration options (ports, addresses, seed peers, squads, relay settings, diagnostics), Commands enum (Start, Diagnostics, GenerateIdentity, ListSquads), and main Cli struct. Implements command routing in handle_command() and run_with_cli(). Provides base directory resolution and comprehensive mining pool configuration through CLI arguments. Includes validation, custom styling, and detailed help text. |
| `mod.rs` | CLI module interface. Re-exports command-line argument definitions (args: Cli, Commands, StartArgs, run_with_cli), command handlers (commands: handle_start), utilities, and LibP2pInfo type. Organizes CLI functionality across args, commands, and util submodules. |
| `util.rs` | CLI utility functions for sha-p2pool command-line interface styling and validation. Provides cli_styles() function creating consistent ANSI color scheme for clap CLI output with bright yellow headers/usage, bright green literals/valid values, bright cyan placeholders, and bright red errors/invalid values. Also implements validate_squad() function ensuring squad names are non-empty strings with proper error messaging. Essential for maintaining professional CLI appearance and input validation across all command-line interactions. |

###### p2pool/src/cli/commands/

| File | Description |
|------|-------------|
| `diagnostics.rs` | Diagnostics command implementation for P2Pool network debugging. Configures diagnostic mode by disabling mining algorithms, enabling HTTP server, setting peer publish intervals, and forcing network silence delay. Provides specialized configuration for network analysis and troubleshooting without active mining participation. Used for monitoring peer connections, network behavior, and system health in diagnostic environments. |
| `generate_identity.rs` | Identity generation command for creating P2Pool peer identities. Generates cryptographic keys and peer identity information, outputting results in JSON format. Used for establishing unique peer identities in the P2Pool network, enabling secure peer-to-peer communication and network participation. |
| `list_squads.rs` | Squad discovery command (currently unimplemented) intended for discovering available mining squads in the P2Pool network. Contains placeholder implementation with commented-out logic for squad enumeration, timeout handling, and JSON output formatting. Would enable miners to discover and join different mining groups within the P2Pool ecosystem. |
| `mod.rs` | CLI commands module organization for sha-p2pool application. Re-exports all command implementations: generate_identity for peer identity creation, list_squads for squad discovery (placeholder), handle_start for main server startup, handle_diagnostics for network diagnostic mode, and LibP2pInfo utility for libp2p information handling. Provides clean modular organization of command-line interface functionality with proper separation of concerns for different operational modes and utilities. |
| `start.rs` | Start command implementation for SHA-P2Pool node. Handles the main 'start' subcommand by delegating to util::server() for server initialization and then calling start() to begin operation. Simple wrapper that connects CLI argument parsing to server startup with proper shutdown handling. Entry point for normal P2Pool mining operations. |
| `util.rs` | **Core server initialization utilities - comprehensive bootstrap orchestrator for P2Pool services.** Contains the critical `server()` function (260+ lines) that performs complete P2Pool server bootstrap: initializes structured logging with `log4rs`, builds comprehensive configuration from CLI arguments via `config.rs`, sets up Tari consensus manager integration, creates and configures libp2p swarm via `p2p/setup.rs`, initializes dual-algorithm share chains (SHA3x and RandomX) via `sharechain/in_memory.rs`, configures statistics collection via `http/stats_collector.rs`, enables diagnostics collection, and manages sophisticated peer management systems.  **Bootstrap Architecture**: Central service bootstrap component implementing the Builder pattern for complex system initialization. Provides `LibP2pInfo` structure for peer identity export/import enabling identity persistence, coordinates service dependency injection ensuring proper initialization order, manages configuration validation and error handling, and provides foundation for all CLI command implementations through shared bootstrap logic.  **Integration Orchestration**: Critical integration point connecting CLI layer to service layer - bridges `cli/args.rs` configuration to `server/server.rs` service execution. Manages complex dependency relationships between networking (`p2p/`), consensus (`sharechain/`), storage (`lmdb_block_storage.rs`), and monitoring (`http/`) components. Ensures proper initialization sequence preventing race conditions and initialization failures.  **Service Lifecycle**: Used by all CLI commands (`start.rs`, `diagnostics.rs`) for consistent server initialization, provides error handling and logging configuration for operational reliability, manages graceful shutdown coordination, and enables testing infrastructure through controlled initialization patterns.  **Architectural Significance**: Serves as the dependency injection container for P2Pool's service-oriented architecture, ensuring all components receive properly configured dependencies and maintaining separation of concerns between CLI interface and service implementation. Critical for system reliability and maintainability. |

##### p2pool/src/server/

| File | Description |
|------|-------------|
| `config.rs` | Server configuration management using builder pattern. Defines Config struct with comprehensive server settings: ports (P2P, gRPC), connection limits, timeouts, mining algorithms (SHA3/RandomX), relay circuits, share windows, and cache settings. ConfigBuilder provides fluent API for configuration construction with 30+ builder methods. Integrates P2P and HTTP server configurations. Used throughout server initialization and component setup. |
| `mod.rs` | Server module interface and common types. Re-exports configuration types, defines PROTOCOL_VERSION constant (37), and PeerType enum for classifying peers (SeedPeer, RelayPeer, PrivatePeer, NonSquadPeer, Unknown). Organizes server functionality across config, server, diagnostics, grpc, http, and p2p submodules. Provides Display implementation for PeerType for logging and debugging. |
| `server.rs` | **Central server orchestration managing P2Pool's complete service stack and architectural backbone.** Coordinates async execution of gRPC services (base node proxy via `grpc/base_node.rs` and P2Pool mining interface via `grpc/p2pool.rs`), HTTP REST API (`http/server.rs`), P2P networking (`p2p/network.rs`), statistics collection (`http/stats_collector.rs`), and diagnostics (`diagnostics/`).   **Core Architecture Role**: Service orchestrator implementing dependency injection pattern - injects shared state (share chains, statistics, peer store) into all service components. Handles server lifecycle from initialization through graceful shutdown using tari_shutdown coordination. Manages dual-algorithm support (RandomX/SHA3x) maintaining separate but coordinated share chains, maintains sync status flags for network coordination, and provides high-level error handling with proper resource cleanup.  **Integration Patterns**: Serves as the main integration point connecting all P2Pool components into a cohesive mining pool service. Uses channel-based communication for inter-service coordination, implements the orchestrator pattern for managing complex async service dependencies, and provides centralized error handling and logging coordination. Works closely with `config.rs` for service configuration and `cli/commands/util.rs` for server bootstrap. |

###### p2pool/src/server/diagnostics/

| File | Description |
|------|-------------|
| `diagnostics_collector.rs` | Network diagnostics collection and reporting system for P2Pool peer analysis. Tracks peer lifecycle events (discovery, connection, requests, responses), categorizes peers by type (seed, private, relay, non-squad, unknown), measures response times and peer exchange metrics. Provides comprehensive diagnostic data for network health monitoring, peer behavior analysis, and troubleshooting connectivity issues. Includes JSON serialization with custom time formatting and real-time diagnostic reporting. |
| `mod.rs` | Diagnostics module for P2Pool network analysis and monitoring. Re-exports diagnostics_collector module providing comprehensive network diagnostic capabilities including peer discovery analysis, connection health monitoring, network topology mapping, and performance metrics collection. Used by diagnostic mode to gather intelligence about P2Pool network structure, peer behaviors, and operational characteristics for troubleshooting and network optimization purposes. |

###### p2pool/src/server/grpc/

| File | Description |
|------|-------------|
| `base_node.rs` | **Base node gRPC proxy service - critical bridge enabling seamless miner integration with P2Pool.** Provides complete passthrough of Tari base node API to miners, implementing the Proxy pattern to make P2Pool appear as a standard Tari base node while adding mining pool functionality behind the scenes. Implements all BaseNode service methods with sophisticated connection management, timeout handling, error propagation, and streaming response support for real-time blockchain data.  **Architectural Significance**: Core component of the service layer implementing the Facade pattern - miners connect to P2Pool exactly as they would to a regular Tari base node, requiring zero miner-side configuration changes. Uses macros for efficient method proxying, automatic retry logic for connection failures via `util.rs`, and performance monitoring for production reliability.  **Integration Architecture**: Works with `util.rs` for base node connectivity and connection management, `p2pool.rs` for P2Pool-specific mining operations, and `error.rs` for structured error handling. Connected to actual Tari base node via `minotari_node_grpc_client` while serving miners through `tonic` gRPC server. The proxy maintains API compatibility while `grpc/p2pool.rs` adds share chain functionality.  **Performance Patterns**: Implements connection pooling, request caching where appropriate, timeout management with configurable limits, and proper resource cleanup. Essential for maintaining mining pool decentralization by enabling any Tari-compatible miner to participate without modification, making P2Pool adoption frictionless for existing miners. |
| `error.rs` | gRPC error handling definitions for P2Pool server operations. Defines structured error types for shutdown scenarios, Tonic transport errors, and basic authentication failures. Uses thiserror for automatic trait implementations and proper error propagation throughout the gRPC service layer. |
| `mod.rs` | gRPC services module providing complete Tari base node interface emulation and P2Pool-specific functionality. Exports base_node (complete Tari base node API passthrough), p2pool (mining-specific endpoints), util (connection utilities and coinbase handling), and error (gRPC error management) modules. Defines MAX_ACCEPTABLE_GRPC_TIMEOUT constant (5 seconds) for request timeout management. Enables seamless miner integration by mimicking real Tari base node gRPC interface while adding P2Pool share chain capabilities for decentralized mining pool operations. |
| `p2pool.rs` | **P2Pool-specific gRPC service - mining interface bridging miners to share chain consensus.** Implements ShaP2Pool trait providing the core mining operations: `get_new_block_template` for mining template generation with share chain coinbases and `submit_block` for block validation and network propagation. Features sophisticated dual-algorithm support (RandomX/SHA3x) with algorithm-specific template caching (100 templates each), share chain integration for proportional reward calculation, and comprehensive difficulty validation.  **Mining Architecture**: Core mining service implementing the Strategy pattern for dual-algorithm support. Integrates deeply with `sharechain/in_memory.rs` for consensus operations, `sharechain/p2chain.rs` for share calculations and uncle rewards, and `p2p/client.rs` for block broadcasting across the network. Provides mining template generation with proper coinbase construction including share-based rewards distribution to active miners.  **Performance Optimizations**: Implements sophisticated caching strategies - template caching for reduced latency, tip info caching for frequently requested data, and optimized block submission pipeline with parallel validation and broadcasting. Uses LRU cache eviction and configurable cache sizes to balance memory usage with performance requirements.  **Integration Patterns**: Central mining interface connecting miners to P2Pool network. Works with `grpc/base_node.rs` (proxy services), `grpc/util.rs` (base node connectivity), `http/stats_collector.rs` (mining statistics), and `server/server.rs` (service coordination). Communicates with actual Tari base node via `grpc/util.rs` while providing P2Pool-enhanced mining functionality to connected miners.  **Consensus Role**: Critical bridge between individual miners and P2Pool consensus - validates submitted blocks against share chain rules, manages difficulty adjustment for fair mining, and ensures proper reward distribution through share tracking. Enables decentralized mining pool operations by maintaining mining compatibility while adding collaborative consensus layer. |
| `util.rs` | gRPC utility functions for base node connectivity and coinbase extra data handling. Provides connect_base_node() with infinite retry logic, graceful shutdown support, and configurable timeouts. Includes convert_coinbase_extra() for encoding squad information and custom messages using TLV (Type-Length-Value) format. Handles connection resilience, message size limits, and proper error propagation for robust base node integration. |

###### p2pool/src/server/http/

| File | Description |
|------|-------------|
| `health.rs` | Simple HTTP health check endpoint handler returning OK status. Provides basic service availability monitoring for load balancers and monitoring systems to verify P2Pool HTTP server responsiveness. |
| `mod.rs` | HTTP server module providing RESTful API endpoints for P2Pool monitoring and statistics. Exports health (health check endpoints), server (Axum HTTP server implementation), stats (statistics endpoints and models), stats_collector (comprehensive metrics aggregation), and version (version information) modules. Implements HTTP-based interface for external monitoring systems, dashboards, and operational tools to access P2Pool node status, mining statistics, peer information, and network health metrics. |
| `server.rs` | HTTP REST API server implementation using Axum framework. Provides mining pool statistics, health monitoring, version information, chain status, peer connections, and miner details through RESTful endpoints. Integrates with statistics collector and P2P service for real-time data. Supports graceful shutdown, configurable port binding, and structured error handling. Serves as the external monitoring and management interface for P2Pool operations, enabling web dashboards and monitoring tools. |
| `stats_collector.rs` | **Comprehensive statistics collection and reporting system - operational intelligence backbone for P2Pool monitoring.** Tracks comprehensive mining statistics (accepted blocks per algorithm with performance metrics), detailed chain status (height, length, difficulty, sync status), dynamic peer counts with categorization, connection statistics with health indicators, and real-time network activity monitoring. Provides sophisticated real-time performance monitoring using broadcast/subscription pattern for live updates, periodic console reporting with human-readable formatting and colored output, and complete HTTP API integration for external monitoring tools.  **Observability Architecture**: Central observability component implementing the Observer pattern for real-time statistics distribution and the Publisher-Subscriber pattern for multi-consumer metrics delivery. Integrates with all major system components: `sharechain/in_memory.rs` for chain metrics, `p2p/peer_store.rs` for network statistics, `grpc/p2pool.rs` for mining performance, and `server/server.rs` for service health indicators.  **Performance Monitoring**: Implements sophisticated metrics aggregation with configurable collection intervals, performance trend analysis for mining efficiency optimization, network health scoring based on peer connectivity and sync performance, and real-time alerting capabilities for operational issues. Provides detailed debugging information for network partitions, sync failures, and mining performance degradation.  **Integration Hub**: Used by `http/stats/handlers.rs` for REST API endpoints, `server/server.rs` for service health coordination, `p2p/network.rs` for network performance metrics, and `sharechain/` components for consensus statistics. Enables comprehensive monitoring dashboards, automated alerting systems, and operational troubleshooting through rich statistics APIs.  **Operational Value**: Essential for mining pool performance analysis, enabling operators to monitor mining efficiency, detect network issues, track peer health, optimize performance parameters, and troubleshoot connectivity problems. Provides the foundation for P2Pool observability enabling data-driven operational decisions and proactive issue resolution. |
| `version.rs` | HTTP version endpoint handler returning application version from Cargo metadata. Provides version information for monitoring, debugging, and compatibility checking. Uses compile-time version string from CARGO_PKG_VERSION environment variable. |

###### p2pool/src/server/http/stats/

| File | Description |
|------|-------------|
| `handlers.rs` | HTTP REST API handlers for P2Pool statistics and monitoring. Implements endpoints for connections (/connections), peer information (/peer), chain statistics (/chain), general stats (/stats), and miner shares (/miners). Provides real-time data on network health, mining performance, peer connections, share distributions, and chain synchronization status. Includes proper error handling, timeout monitoring, and structured JSON responses for external monitoring tools and dashboards. |
| `mod.rs` | HTTP statistics module for P2Pool metrics and monitoring endpoints. Exports handlers (HTTP request handlers for stats endpoints) and models (data structures for stats responses) modules. Defines MAX_ACCEPTABLE_HTTP_TIMEOUT constant (500ms) for fast HTTP response requirements. Provides structured API for accessing real-time P2Pool statistics including mining performance, network health, peer connections, share chain status, and operational metrics through RESTful endpoints for monitoring and dashboard integration. |
| `models.rs` | Data models for HTTP statistics API responses in P2Pool server. Defines Serde-serializable structures: StatsBlock (share chain block information with hash, height, timestamp, miner address), SquadDetails (squad identification), Stats (comprehensive node statistics including connection info, algorithm stats, peer ID, squad), and ChainStats (share chain statistics with squad details and chain metrics). Provides structured data representation for JSON API responses supporting monitoring dashboards, operational tools, and external integrations requiring P2Pool network insights. |

###### p2pool/src/server/p2p/

| File | Description |
|------|-------------|
| `client.rs` | P2P service client interface for block broadcasting operations. Provides lightweight wrapper around unbounded sender for triggering broadcast of new P2Pool blocks to the network. Simple but critical component enabling other services (like gRPC handlers) to initiate block propagation through the P2P network without direct coupling to networking implementation. |
| `messages.rs` | P2P message definitions and serialization for share chain synchronization. Defines comprehensive message types: PeerInfo (peer discovery), sync requests/responses (CatchUpSync, SyncMissingBlocks), tip notifications, and metadata exchange. Uses CBOR serialization for efficient network transmission, includes protocol versioning, peer reputation data, and proof-of-work information. Provides conversion macros for libp2p integration and display formatting for debugging. Critical for P2Pool's decentralized coordination and chain synchronization between peers. |
| `mod.rs` | Peer-to-peer networking module for P2Pool using libp2p framework. Re-exports network module and provides organized access to: client (P2P service client for block broadcasting), messages (P2P message definitions with CBOR serialization), peer_store (comprehensive peer management system), relay_store (relay server management for NAT traversal), setup (libp2p network stack initialization), and util (P2P utilities and identity management). Implements efficient gossipsub broadcasting, request-response protocols, mDNS discovery, and sophisticated peer categorization for robust decentralized P2Pool networking. |
| `network.rs` | **Core P2P networking service - the decentralized nervous system of P2Pool.** Implements comprehensive libp2p stack providing gossipsub for peer discovery and efficient block notifications, request-response protocols for chain synchronization and missing block retrieval, mDNS for local network discovery, relay circuits for NAT traversal enabling broader network participation, and sophisticated peer management with reputation tracking.  **Networking Architecture**: Central networking component (3800+ lines) implementing the Event-Driven architecture pattern with libp2p's async event system. Features automatic sync coordination using semaphores to prevent race conditions, peer categorization and ranking via `peer_store.rs`, diagnostics collection via `diagnostics/` for network health monitoring, and protocol version handling for backward compatibility during network upgrades.  **P2P Patterns**: Implements advanced P2P patterns including gossip protocols for efficient message propagation, DHT-like peer discovery, circuit relay for NAT traversal via `relay_store.rs`, request-response for direct peer communication, and sophisticated connection management with backoff and retry logic. Uses CBOR message serialization via `messages.rs` for efficient network transmission.  **Integration Points**: Core networking hub connecting to `p2chain.rs` for share chain synchronization, `grpc/p2pool.rs` for block broadcasting after submission, `peer_store.rs` for peer lifecycle management, `setup.rs` for network stack configuration, and `client.rs` for simplified service interfaces. Works with `server.rs` for service lifecycle coordination and `diagnostics/` for network monitoring and troubleshooting.  **Consensus Role**: Critical for maintaining P2Pool's decentralized consensus by enabling block propagation, peer synchronization, chain reorganization detection, and network partition recovery. Implements sophisticated timing and coordination to prevent network splits and ensure consistent view across all participants. |
| `peer_store.rs` | **Comprehensive peer management system - intelligent network topology coordinator for P2Pool operations.** Maintains sophisticated peer categorization (whitelist, greylist, blacklist, non-squad) with detailed tracking including sync attempts, grey listing reasons, catch-up attempts, ping history, and reputation scoring. Implements advanced peer reputation management enabling network health optimization, automatic peer discovery through network events, squad-based filtering for mining pool isolation, and intelligent peer selection strategies optimized for different algorithms (RandomX/SHA3x).  **Network Intelligence**: Core networking component implementing the Strategy pattern for peer selection and the State pattern for peer lifecycle management. Provides thread-safe async operations for peer lifecycle management, sophisticated ban/unban functionality with time-based expiration, smart peer selection algorithms considering network performance and reliability, and comprehensive statistics integration for monitoring and debugging network behavior.  **P2P Architecture**: Central peer coordination hub connecting to `network.rs` for event-driven peer updates, `setup.rs` for initial peer configuration, `relay_store.rs` for NAT traversal peer management, and `messages.rs` for peer information exchange. Implements sophisticated peer ranking algorithms considering connection quality, sync performance, and historical reliability for optimal network performance.  **Integration Patterns**: Used by `p2p/network.rs` for peer selection during sync operations, `diagnostics/` for network health analysis, `http/stats_collector.rs` for peer statistics reporting, and `server/server.rs` for network coordination. Provides the foundation for P2Pool's decentralized networking by ensuring optimal peer connections and network resilience.  **Consensus Support**: Critical for maintaining P2Pool consensus by ensuring reliable peer connections for block propagation, managing network partitions through intelligent peer selection, coordinating chain synchronization across diverse network topologies, and preventing network isolation through strategic peer management. Enables robust decentralized mining pool operations across unreliable internet connections. |
| `relay_store.rs` | Relay server management system for P2Pool P2P networking with NAT traversal capabilities. Manages relay reservations and potential relay peers for libp2p circuit relay functionality. Tracks possible relay nodes, active reservations with expiration times, pending reservation states, and provides APIs for adding relays, confirming reservations, discovering potential relays, and managing expiring reservations. Essential for enabling P2Pool nodes behind NAT/firewalls to participate in the network through relay connections, improving network connectivity and decentralization. |
| `setup.rs` | libp2p network stack initialization and configuration for P2Pool networking. Sets up gossipsub for efficient block broadcasting, mDNS for local peer discovery, relay services for NAT traversal, request-response protocols for synchronization, and identify/ping for peer management. Handles cryptographic key generation with optional stability, transport configuration (TCP/QUIC), and security (Noise protocol). Configures connection limits, timeouts, and relay circuit management. Foundation for P2Pool's decentralized networking capabilities. |
| `util.rs` | P2P networking utilities for libp2p identity generation and management. Provides generate_identity() async function creating new Ed25519 keypairs for libp2p peer identification, returning GenerateIdentityResult with PeerId and hex-encoded private key. Includes proper protobuf encoding for key serialization and error handling. Used by generate-identity CLI command and peer identity management system to create cryptographically secure peer identities for P2Pool network participation and authentication. |

##### p2pool/src/sharechain/

| File | Description |
|------|-------------|
| `error.rs` | Comprehensive error handling for sharechain operations. Defines ShareChainError with 15+ variants covering block validation, chain management, uncle blocks, difficulty calculations, and consensus errors. ValidationError enum provides 9 specialized validation failures. Integrates with Tari consensus, proof-of-work, and address systems. Uses thiserror for structured error messages and type conversions. Critical for mining pool error reporting and debugging. |
| `in_memory.rs` | **In-memory share chain implementation - primary interface between services and consensus logic.** Provides the ShareChain trait interface for P2Pool mining operations, acting as the main abstraction layer between the service layer and core consensus algorithms. Manages dual-algorithm chains (RandomX/SHA3x) with sophisticated switching logic, comprehensive block validation including PoW verification, uncle validation, timestamp checks, and coinbase validation following Tari consensus rules.  **Domain Architecture**: Core domain service implementing the Repository pattern - abstracts underlying storage (`lmdb_block_storage.rs`) and consensus logic (`p2chain.rs`) while providing clean service interfaces. Handles block submission with complete validation pipeline, chain synchronization with conflict resolution, tip management for mining template generation, and share generation for proportional payout calculations.  **Performance Architecture**: Integrates with LMDB storage for persistence via `lmdb_block_storage.rs`, implements sophisticated caching strategies for hot path optimizations (template generation, tip queries), and provides comprehensive testing utilities for end-to-end validation. Uses async/await patterns for non-blocking operations while maintaining data consistency.  **Service Integration**: Primary interface used by `grpc/p2pool.rs` for mining operations, `p2p/network.rs` for chain synchronization and broadcasting, `http/stats_collector.rs` for real-time metrics, and `server/server.rs` for dual-algorithm coordination. Serves as the bridge between P2Pool's networking layer and the underlying consensus implementation in `p2chain.rs`.  **Testing Role**: Provides comprehensive testing utilities enabling integration tests via `integration_tests/` to validate end-to-end mining flows, chain synchronization, and consensus behavior across multiple nodes. Critical for ensuring mining pool reliability and consensus correctness. |
| `lmdb_block_storage.rs` | LMDB-based persistent storage implementation for P2Pool blocks using Lightning Memory-Mapped Database. Provides BlockCache trait with CRUD operations (get, insert, delete, all_blocks), automatic database resizing when full, migration support for schema upgrades, and optimized serialization with bincode. Handles database lifecycle, error recovery, and includes test utilities with in-memory cache implementation. Critical for maintaining share chain persistence across restarts and providing high-performance block storage for mining operations. |
| `mod.rs` | Core sharechain module defining the P2Pool share chain functionality. Exports constants (CHAIN_ID: 2, reward shares, difficulty windows, minimum difficulties), BlockValidationParams for consensus validation, ShareChain trait for chain operations, and MinerShare struct. Defines async trait methods for block submission, chain synchronization, share generation, and chain querying. Organizes submodules: error, in_memory, lmdb_block_storage, p2block, p2chain, p2chain_level. Central interface for P2Pool's side-chain implementation. |
| `p2block.rs` | Core P2Pool block structure and builder implementation. P2Block contains 13 fields including headers, uncles, verification status, and mining data. Uses domain-separated Blake2b hashing, supports both RandomX and SHA3x algorithms, manages accumulated difficulty calculations, and tracks verification status via bitflags (5 verification types). P2BlockBuilder provides fluent API for block construction. VerifiedStatus tracks validation state with comprehensive display formatting. Central data structure for sharechain operations. |
| `p2chain.rs` | **Core P2Pool share chain implementation - the heart of decentralized mining pool consensus.** Manages the in-memory side chain that tracks miner contributions and enables instant decentralized payouts. Implements sophisticated multi-algorithm block validation (difficulty, timestamps, uncles, shares), chain reorganization logic with accumulated difficulty calculations, LWMA (Linear Weighted Moving Average) difficulty adjustment for network stability, and uncle block management (up to 3 blocks with 80% rewards to encourage decentralization).  **Consensus Architecture**: Central domain entity in DDD architecture containing 2700+ lines of complex consensus logic including median timestamp validation (prevents time-based attacks), accumulated difficulty calculations for reorg detection, comprehensive error handling via `error.rs`, and share calculation algorithms for proportional payouts. Integrates with Tari consensus rules through `tari_core` while maintaining P2Pool-specific consensus extensions.  **Performance Patterns**: Handles sync coordination using semaphores to prevent concurrent modifications, missing block tracking with sophisticated retry logic, chain pruning for memory management, and optimized block retrieval patterns. Works closely with `in_memory.rs` (ShareChain trait implementation), `p2chain_level.rs` (height-based organization), `p2block.rs` (block structures), and `lmdb_block_storage.rs` (persistence layer).  **Integration Points**: Used by `grpc/p2pool.rs` for mining template generation and block submission, `p2p/network.rs` for chain synchronization and broadcasting, `http/stats_collector.rs` for performance metrics, and `server/server.rs` for dual-algorithm coordination. Critical for maintaining network consensus and enabling trustless mining pool operations. |
| `p2chain_level.rs` | Share chain level management representing collections of blocks at the same height. Maintains block headers in-memory for fast access while delegating full block storage to BlockCache. Tracks main chain designation, provides parent/uncle relationship queries, verification status tracking, and supports chain reorganization. Includes P2BlockHeader structure with essential block metadata (difficulty, timestamp, wallet address, verification status). Core component for organizing blocks by height and managing chain state transitions. |

### scripts/

| File | Description |
|------|-------------|
| `cross_compile_tooling.sh` | Cross-compilation setup script for building sha-p2pool on multiple architectures. Configures build environment based on TARGETARCH environment variable for ARM64 (aarch64) and AMD64 (x86_64) targets. Sets up Rust cross-compilation toolchains, target-specific linkers, C/C++ compilers, OpenSSL paths, and Debian package architecture support. Installs necessary cross-compilation packages and configures build variables for successful multi-platform binary generation in CI/CD pipelines and Docker builds. |
| `cross_compile_ubuntu_18-pre-build.sh` | Comprehensive Ubuntu 18.04 cross-compilation pre-build setup script for multi-architecture sha-p2pool builds. Configures APT proxy support, validates build targets (x86_64, aarch64, riscv64), sets up timezone/locale, installs base development packages, downloads and configures Rust toolchain, and establishes cross-architecture APT repositories for arm64/riscv64 ports. Installs target-specific GCC toolchains, cross-compilation libraries (SSL, SQLite, HID API), and prepares complete build environment for successful cross-platform compilation in containerized build systems. |
| `file_license_check.sh` | License compliance verification script ensuring all source files contain proper Tari Project copyright headers. Uses ripgrep to scan for files missing 'Copyright.*The Tari Project' headers, excludes non-source file extensions (documentation, configs, assets), compares results against .license.ignore allowlist, and reports violations with diff output. Includes requirements checking for mktemp, ripgrep, and diff tools. Essential for maintaining legal compliance, intellectual property protection, and consistent licensing across the codebase in CI/CD pipelines. |
| `install_ubuntu_dependencies-cross_compile.sh` | Cross-compilation dependency installation script for Ubuntu systems. Installs architecture-specific development packages including pkg-config, GCC, and G++ cross-compilers for specified target ISA architecture (aarch64, x86-64, riscv64). Takes target architecture as first parameter and supports additional package specifications. Essential for setting up cross-compilation toolchains in CI/CD environments and development setups requiring multi-architecture build capabilities for sha-p2pool distribution. |
| `install_ubuntu_dependencies-rust-arm64.sh` | Rust ARM64 cross-compilation setup script for Ubuntu systems. Configures Rust toolchain for aarch64-unknown-linux-gnu target by adding the target architecture and installing the stable toolchain with --force-non-host flag. Updates PATH to include Cargo binaries for immediate availability. Simple, focused script for enabling ARM64 cross-compilation capabilities in development and CI environments requiring multi-architecture Rust builds. |
| `install_ubuntu_dependencies-rust.sh` | Automated Rust installation script for Ubuntu systems following official installation guidelines. Downloads and executes the rustup installer with unattended mode (-y flag), configures PATH environment variable, and sources Cargo environment for immediate availability. Simple, reliable script for bootstrapping Rust development environment in CI/CD pipelines, container builds, and automated development environment setup without user interaction. |
| `install_ubuntu_dependencies.sh` | Ubuntu system dependencies installation script. Installs required packages for building SHA-P2Pool: SSL/TLS libraries (openssl, libssl-dev), database (sqlite3), development tools (cmake, clang, g++), protocol buffers (protobuf-compiler), USB device support (libudev-dev, libhidapi-dev), terminal libraries (readline, ncurses), and build essentials. Used by CI/CD and development environment setup. |
| `multinet_envs.sh` | Multi-network environment configuration script for Tari network deployment targeting. Sets TARI_NETWORK, TARI_TARGET_NETWORK, and TARI_NETWORK_DIR environment variables based on git tag patterns: pre-release tags map to esme testnet, release candidates to nextnet, DAN tags to igor testnet, and default releases to mainnet. Used by CI/CD pipelines and Docker builds to automatically configure appropriate network settings for different deployment environments and testing scenarios. |

### supply-chain/

| File | Description |
|------|-------------|
| `audits.toml` | [GENERATED] Cargo-vet security audits configuration file for supply chain security verification. Currently empty [audits] section indicating no custom security audits have been performed yet. Part of cargo-vet framework for tracking and verifying the security status of Rust dependencies through cryptographically signed audit data. Will contain audit entries as security reviews of specific crate versions are completed by project maintainers or imported from trusted sources. |
| `config.toml` | Cargo-vet supply chain security configuration defining audit policies for dependencies. Configures security exemptions for hundreds of crates with safe-to-deploy criteria, specifies audit-as-crates-io policies for Tari ecosystem components, and manages dependency verification requirements. Critical for ensuring supply chain security and compliance with security standards for production deployments. |
| `imports.lock` | [GENERATED] Cargo-vet imports lock file for tracking imported security audit data from external sources. Currently empty, indicating no external audit data has been imported yet. Part of cargo-vet supply chain security framework that enables importing and verifying cryptographically signed audit data from trusted organizations and maintainers. Will contain locked import records when audit data is imported from external sources to enhance dependency security verification coverage. |

