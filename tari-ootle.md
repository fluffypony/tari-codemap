# Tari-Ootle

## Project Overview

## System Summary

Tari Ootle is a Layer 2 sharded blockchain platform built in Rust that implements a distributed network of validator nodes running HotStuff Byzantine Fault Tolerant consensus. The system provides smart contract execution through WASM-based templates, comprehensive wallet services with multiple authentication methods, and privacy-preserving digital asset management. Core architecture includes validator nodes for consensus, wallet daemons for user operations, indexers for blockchain data access, and a development swarm daemon for local testing. All services expose JSON-RPC 2.0 APIs with React-based web UIs for administration and monitoring. The platform supports confidential transactions, NFTs, template-based smart contracts, and multi-shard scaling with automatic epoch management and validator committee formation.

## Directory Structure

```
applications/
├── tari_validator_node/         # Core consensus validator with HotStuff BFT, JSON-RPC API, React admin UI
├── tari_walletd/                # Wallet daemon with account management, JSON-RPC API, Material-UI web interface
├── tari_wallet_cli/             # Command-line wallet for terminal-based operations and automation
├── tari_validator_node_cli/     # CLI for validator operations, node management, consensus participation
├── tari_swarm_daemon/           # Development network orchestrator with process management and monitoring
├── tari_indexer/                # Blockchain data indexer with GraphQL/JSON-RPC APIs and React UI
└── tari_signaling_server/       # WebRTC signaling for wallet P2P communication

bindings/                        # Auto-generated TypeScript types from Rust via ts-rs
├── src/types/                   # All API request/response types and data structures
├── src/wallet-daemon-client.ts  # Wallet API client types
├── src/validator-node-client.ts # Validator API client types
└── src/index.ts                 # Consolidated type exports

crates/                          # Core Rust libraries and blockchain logic
├── consensus/                   # HotStuff BFT consensus protocol implementation
├── consensus_types/             # Consensus-specific data structures and certificates
├── engine/                      # Smart contract execution engine with WASM support
├── engine_types/                # Core types for smart contract system
├── template_lib/                # Smart contract template framework and runtime
├── template_macros/             # Procedural macros for template development
├── storage/                     # Blockchain state storage abstractions
├── storage_sqlite/              # SQLite backend implementation with Diesel ORM
├── wallet/                      # Wallet SDK and account management
├── epoch_oracles/               # Base layer monitoring and epoch management
├── indexer_lib/                 # Blockchain scanning and data indexing
├── networking/                  # P2P networking with libp2p integration
└── common_types/                # Shared data structures across the system

clients/                         # Client libraries for external integration
├── wallet_daemon_client/        # Rust client for wallet daemon JSON-RPC API
├── validator_node_client/       # Rust client for validator node operations
└── tari_indexer_client/         # GraphQL and JSON-RPC clients for indexer

config/                          # Network configuration templates
├── presets/                     # Pre-configured network settings (localnet, testnet)
└── envs/                        # Environment-specific configurations

networking/                      # P2P networking infrastructure
├── core/                        # Core networking abstractions and libp2p integration
├── swarm/                       # Tari-specific libp2p swarm implementation
└── proto_builder/               # Protocol buffer compilation utilities

integration_tests/               # End-to-end testing with Cucumber BDD framework
├── tests/                       # Cucumber feature files and step definitions
├── src/                         # Test utilities and process management
└── fixtures/                    # Test templates and configuration files

utilities/                       # Development and maintenance tools
├── db_inspector/                # Database inspection tool with web UI
└── stress_test/                 # Network load testing utilities

docker/                          # Container orchestration and deployment
prometheus/                      # Monitoring and metrics configuration
scripts/                         # Build automation and development tools
target/                          # Rust compilation output (build artifacts)
```

## Architecture

The system implements a sophisticated multi-layered architecture combining distributed consensus, P2P networking, and modern web technologies. At the core, HotStuff Byzantine Fault Tolerant consensus ensures safety and liveness across validator nodes, with automatic view changes and leader election. The networking layer uses libp2p for peer-to-peer communication, supporting gossipsub for consensus message propagation, rendezvous for peer discovery, and relay nodes for NAT traversal. Smart contracts execute in sandboxed WASM environments through template-based deployment, enabling secure user-defined logic with resource metering and deterministic execution.

Each major service (validator node, wallet daemon, indexer) follows a consistent pattern: Rust backend with JSON-RPC 2.0 API, React TypeScript frontend served via embedded HTTP server, and auto-generated type bindings via ts-rs for type safety. The validator nodes form sharded committees based on VRF-based shard assignment, with epoch management handled by base layer oracles that monitor Tari Layer-1 blockchain for validator registrations and stake changes. State synchronization occurs through Merkle tree-based proofs, while transaction execution uses a UTXO-style substate model supporting confidential amounts and privacy-preserving operations.

Authentication supports multiple modes including JWT for web sessions, WebAuthn for hardware security keys, and plain password authentication. The wallet system manages hierarchical deterministic key derivation, account creation, and multi-signature operations. All components integrate with Prometheus for metrics collection and provide comprehensive logging through structured tracing.

## Key Files

**Entry Points:**
- [`applications/tari_validator_node/src/main.rs`](file:///Users/ric/Desktop/working/tari-ootle-work/applications/tari_validator_node/src/main.rs) - Validator node daemon startup with consensus initialization
- [`applications/tari_walletd/src/main.rs`](file:///Users/ric/Desktop/working/tari-ootle-work/applications/tari_walletd/src/main.rs) - Wallet daemon with account management services
- [`applications/tari_indexer/src/main.rs`](file:///Users/ric/Desktop/working/tari-ootle-work/applications/tari_indexer/src/main.rs) - Blockchain data indexer and query service
- [`applications/tari_swarm_daemon/src/main.rs`](file:///Users/ric/Desktop/working/tari-ootle-work/applications/tari_swarm_daemon/src/main.rs) - Development network orchestrator

**Core Configuration:**
- [`Cargo.toml`](file:///Users/ric/Desktop/working/tari-ootle-work/Cargo.toml) - Workspace definition with all crates and applications
- [`config/presets/`](file:///Users/ric/Desktop/working/tari-ootle-work/config/presets/) - Network-specific configuration templates
- [`clippy.toml`](file:///Users/ric/Desktop/working/tari-ootle-work/clippy.toml) - Rust linting configuration
- [`rust-toolchain.toml`](file:///Users/ric/Desktop/working/tari-ootle-work/rust-toolchain.toml) - Rust compiler version specification

**Consensus Implementation:**
- [`crates/consensus/src/hotstuff/worker.rs`](file:///Users/ric/Desktop/working/tari-ootle-work/crates/consensus/src/hotstuff/worker.rs) - Main HotStuff consensus protocol implementation
- [`applications/tari_validator_node/src/consensus/mod.rs`](file:///Users/ric/Desktop/working/tari-ootle-work/applications/tari_validator_node/src/consensus/mod.rs) - Consensus module with spawn_consensus() entry point
- [`crates/consensus_types/src/lib.rs`](file:///Users/ric/Desktop/working/tari-ootle-work/crates/consensus_types/src/lib.rs) - Certificate and voting data structures

**API Definitions:**
- [`applications/tari_validator_node/src/json_rpc/handlers.rs`](file:///Users/ric/Desktop/working/tari-ootle-work/applications/tari_validator_node/src/json_rpc/handlers.rs) - 25+ validator JSON-RPC endpoints
- [`applications/tari_walletd/src/handlers/`](file:///Users/ric/Desktop/working/tari-ootle-work/applications/tari_walletd/src/handlers/) - Wallet daemon RPC handlers by category
- [`applications/tari_indexer/src/json_rpc/handlers.rs`](file:///Users/ric/Desktop/working/tari-ootle-work/applications/tari_indexer/src/json_rpc/handlers.rs) - Indexer API endpoints

**Smart Contract System:**
- [`crates/engine/src/wasm/mod.rs`](file:///Users/ric/Desktop/working/tari-ootle-work/crates/engine/src/wasm/mod.rs) - WASM template compilation and execution  
- [`crates/template_lib/src/template/manager.rs`](file:///Users/ric/Desktop/working/tari-ootle-work/crates/template_lib/src/template/manager.rs) - Template lifecycle management
- [`crates/engine_types/src/lib.rs`](file:///Users/ric/Desktop/working/tari-ootle-work/crates/engine_types/src/lib.rs) - Core smart contract type system

**Web Interfaces:**
- [`applications/*/web_ui/src/main.tsx`](file:///Users/ric/Desktop/working/tari-ootle-work/applications/tari_validator_node/web_ui/src/main.tsx) - React application entry points
- [`applications/*/web_ui/package.json`](file:///Users/ric/Desktop/working/tari-ootle-work/applications/tari_validator_node/web_ui/package.json) - Frontend dependencies and build scripts
- [`bindings/src/index.ts`](file:///Users/ric/Desktop/working/tari-ootle-work/bindings/src/index.ts) - TypeScript type definitions

**Networking and P2P:**
- [`networking/core/src/lib.rs`](file:///Users/ric/Desktop/working/tari-ootle-work/networking/core/src/lib.rs) - Core P2P networking abstractions
- [`networking/swarm/src/behaviour.rs`](file:///Users/ric/Desktop/working/tari-ootle-work/networking/swarm/src/behaviour.rs) - libp2p behavior implementation
- [`applications/tari_validator_node/src/p2p/services/consensus_gossip/`](file:///Users/ric/Desktop/working/tari-ootle-work/applications/tari_validator_node/src/p2p/services/consensus_gossip/) - Consensus message gossip

## Essential Conventions

**JSON-RPC API Standards:**
All services use JSON-RPC 2.0 protocol with consistent patterns: `{method: "endpoint_name", jsonrpc: "2.0", id: request_id, params: {...}}`. Response format: `{jsonrpc: "2.0", id: request_id, result?: any, error?: {code, message, data?}}`. Method names use snake_case and are organized by domain (accounts_, templates_, validator_, etc.).

**Rust Code Organization:**
- Services use `src/handlers/` directories for JSON-RPC endpoint implementations grouped by functionality
- `src/main.rs` files handle CLI parsing, configuration loading, service initialization, and graceful shutdown
- Error types use `thiserror` crate with custom error enums and Display implementations  
- Configuration structures use `serde` for JSON/TOML serialization with environment variable overrides
- Database models in `src/models/` or `storage/src/models/` with Diesel ORM for SQLite backend

**React Frontend Patterns:**
- TypeScript with strict type checking and ESNext target compilation
- Material-UI (MUI) component library for consistent design system
- TanStack Query for server state management and caching with automatic refetching
- React Router v6+ for client-side navigation with nested route structures
- Vite build system for fast development and optimized production builds

**TypeScript Type Generation:**
- All API types auto-generated from Rust using ts-rs crate with `#[derive(Serialize, Deserialize, TS)]`
- Generated files marked with `[GENERATED]` comments to prevent manual editing
- Types organized by service: `wallet-daemon-client/`, `validator-node-client/`, etc.
- Export barrel files (index.ts) provide centralized access to all generated types

**Authentication and Security:**
- JWT tokens for web session management with configurable expiration
- WebAuthn integration for hardware security key authentication
- Password-based authentication with secure hashing (Argon2)
- Environment variables for sensitive configuration (never hardcoded secrets)
- CORS headers configured for cross-origin web UI access

**Build and Development:**
- Workspace Cargo.toml defines all crates with shared dependencies and feature flags
- Conditional compilation features: `web_ui`, `metrics`, `test_vectors`, `desktop_ui`
- Build scripts (`build.rs`) handle React compilation and asset embedding in release mode
- Integration tests use Cucumber BDD framework with process lifecycle management

## Important Notes

**TypeScript Binding Regeneration:**
The `bindings/` directory contains auto-generated TypeScript types that automatically regenerate when Rust types change. Never manually edit files marked `[GENERATED]` as changes will be overwritten. When adding new API endpoints or modifying request/response types, ensure Rust structs have `#[derive(TS)]` and run `cargo build` to update TypeScript types.

**Web UI Development vs Production:**
In development mode, React applications run on separate Vite dev servers (typically port 5173+ for hot reload). In production/release builds, the build.rs scripts compile React apps and embed static assets into Rust binaries. Web UIs are served by their respective daemon HTTP servers on configured ports (validator: 5000, wallet: 3000, indexer: 3001).

**Consensus and Networking Interdependencies:**
Validator nodes must be properly networked via libp2p for consensus to function. The consensus gossip service (`consensus_gossip/`) propagates HotStuff messages based on shard assignments and epoch information. Epoch changes trigger networking topology updates, validator committee reassignment, and state synchronization. Base layer oracle monitoring is critical for validator set management and network liveness.

**Template and Smart Contract Deployment:**
Smart contracts deploy as WASM templates through a two-phase process: template publication to the network via validator nodes, then component instantiation via wallet transactions. Templates require specific ABI definitions and must implement required traits. Template addresses are deterministically generated from WASM binary hashes, ensuring consistency across the network.

**Database Schema and Migrations:**
SQLite databases use Diesel ORM with migration files in `migrations/` directories. Schema changes require new migration files and must be backward compatible. Database models live in `storage/src/models/` or service-specific model directories. The storage layer abstracts database operations through trait interfaces enabling different backend implementations.

**Authentication Flow Complexity:**
The wallet daemon supports multiple authentication methods that can be configured per deployment. WebAuthn requires HTTPS in production and proper origin configuration. JWT tokens have configurable expiration and require secure secret management. Some operations require elevated authentication (template publishing, validator registration) while others work with basic auth.

**Sharding and Committee Assignment:**
Validator nodes participate in multiple shards based on VRF-derived shard keys. Committee assignment happens at epoch boundaries based on stake weight and randomness from base layer. Shard assignment affects network topology, consensus participation, and state synchronization requirements. Cross-shard transactions require special handling through atomic commitment protocols.

**Integration Testing and Process Management:**
The integration test framework spawns real processes (validator nodes, wallets, indexers) in isolated test environments. Process management utilities handle configuration generation, log capture, and cleanup. Cucumber steps coordinate multi-process test scenarios with proper synchronization and state verification.

**Metrics and Observability:**
All services integrate Prometheus metrics collection with optional HTTP endpoints (/metrics). Structured logging uses tracing crate with configurable log levels and output formats. Performance metrics include consensus timing, transaction throughput, network latency, and resource utilization. Web UIs display real-time metrics and system status information.

**Development Network Orchestration:**
The swarm daemon provides comprehensive development environment management including Minotari base layer nodes, miners, validator nodes, wallets, and indexers. Process definitions handle configuration templating, port allocation, log aggregation, and inter-service communication setup. Essential for local development and testing scenarios.

## Codebase Structure

### Root Directory

| File | Description |
|------|-------------|
| `.dockerignore` | Docker build context ignore file excluding unnecessary files from Docker builds. Filters out build artifacts (target), development files (.env, IDE configs), OS files (.DS_Store), documentation (book), data files (*.sqlite3, *.mdb), and keys.json. Optimizes Docker build performance by reducing context size and preventing sensitive files from being included in container images. |
| `.gitignore` | Git ignore file excluding build artifacts (target, Cargo.lock), development files (.env, IDE configs, VS Code, IntelliJ), OS files (.DS_Store), data files (*.sqlite3, *.mdb, data/, logs), and code coverage reports. Includes specific exclusions for client package files, temporary databases, and test output. Standard Rust project gitignore with Tari-specific additions for database files and local development data. |
| `.license.ignore` | License checker exclusion list specifying files to ignore during license compliance validation. Contains auto-generated schema files from Diesel ORM (SQLite schema definitions) that shouldn't be subject to license header requirements: indexer schemas, storage schemas, wallet schemas, and message logger schemas. Used by cargo-deny or similar license auditing tools to skip generated code files. |
| `.nvmrc` | Node Version Manager (nvm) configuration file specifying Node.js version v23.11.0 for consistent development environment across team members. Used by nvm to automatically switch to the correct Node.js version when entering the project directory. Matches NODE_VERSION environment variable in GitHub Actions workflows for consistent CI/CD builds. |
| `.prettierrc.mjs` | Prettier code formatting configuration for JavaScript/TypeScript files. Specifies formatting rules: 120 character line width, 2-space indentation, semicolons enabled, double quotes, consistent quote properties, trailing commas, bracket spacing, arrow function parentheses, and LF line endings. Used by the indexer web UI and TypeScript bindings to maintain consistent code style across the project. |
| `CHANGELOG.md` | Project changelog following standard-version format, documenting notable changes from v0.1.1 to v0.3.0. Records breaking changes like libp2p migration, new features including fee estimation UI, peer-sync protocol, foreign block requests, transaction validation improvements, and WebAuthn integration. Bug fixes cover UI issues, P2P messaging improvements, and propagation fixes. Provides comprehensive release history with GitHub issue/PR links and commit references for tracking development progress. |
| `CHECKLISTS.md` | Release management checklists providing standardized procedures for version bumps and releases. Includes preparation steps (version decisions, Cargo.toml updates, PR creation) and release steps (signed tag creation, CI/CD binary builds, GitHub release creation). Essential documentation for maintaining consistent release processes and ensuring proper version management across the workspace. |
| `CODEOWNERS` | GitHub CODEOWNERS file defining review requirements for critical files. DevOps team reviews required for CI/CD workflows (.github/**), scripts, and CODEOWNERS itself. Lists key maintainers @stringhandler, @CjS77, and @sdbondi as default reviewers. Ensures proper oversight of infrastructure changes and maintains code quality through mandatory reviews. |
| `Cargo.lock` | [GENERATED] Cargo dependency lock file automatically generated and maintained by Cargo package manager. Contains exact versions and checksums of all dependencies and transitive dependencies used by the Tari Ootle workspace. Ensures reproducible builds across different environments by locking specific dependency versions. Should not be manually edited - updated automatically when dependencies change in Cargo.toml files. |
| `Cargo.toml` | Workspace root Cargo manifest defining 67 member crates across applications, clients, and core libraries. Configures shared dependencies, Rust toolchain settings, common build configurations, and workspace-wide package metadata. Central build configuration for the entire Tari Ootle blockchain ecosystem. |
| `Contributing.md` | Brief contribution guide redirecting to the main Tari project repository (https://github.com/tari-project/tari) for development guidelines and contribution processes. Simple reference file linking developers to the primary contribution documentation. |
| `Cross.toml` | Cross-compilation configuration for building Tari binaries on multiple architectures. Defines Docker images, toolchains, and environment variables for aarch64, x86_64, and RISC-V targets. Features pre-build scripts, PKG_CONFIG settings, and architecture-specific compiler configurations. Used by: CI/CD pipeline and developers for creating multi-platform Tari releases with consistent build environments across different target architectures. |
| `LICENSE` | BSD 3-Clause License for the Tari Ootle project, copyright 2019 The Tari Developer Community. Permits redistribution and use in source and binary forms with conditions requiring copyright notice retention, disclaimer reproduction, and prohibition of unauthorized endorsements. Standard open-source license providing clear usage rights and liability disclaimers for the project. |
| `README.md` | Main project documentation for Tari Ootle smart contract blockchain layer. Provides setup instructions, development workflows, testing procedures, and usage examples. Documents integration with tari_swarm_daemon for local development and links to additional resources and tools. |
| `bors.toml` | Bors merge bot configuration for Tari project automated testing and merging. Defines required status checks including clippy, nightly/stable builds, tests, and CircleCI integration/FFI tests. Sets 2-hour timeout for CI completion. Used by: GitHub merge automation to ensure all required checks pass before merging pull requests, maintaining code quality and preventing broken builds in main branch. |
| `ci_all.bat` | Windows batch script for running comprehensive continuous integration tests and builds. Executes full test suite, builds all components, and performs validation checks on Windows platforms. Used in CI/CD pipelines and local development for complete project validation on Windows systems. |
| `clippy.toml` | Clippy linter configuration adjusting default thresholds for Tari codebase. Sets cognitive complexity threshold to 15, allows up to 7 function arguments, and sets enum variant size threshold to 216 bytes (sized for RistrettoPublicKey). Customizes Rust linting rules to accommodate the cryptographic and networking components common in blockchain projects. |
| `deny.toml` | Cargo-deny configuration for security, licensing, and dependency management in Tari project. Defines security vulnerability checking, license compliance (allows MIT, BSD, Apache, etc.), dependency banning rules, and source repository validation. Features CVSS-based vulnerability thresholds, copyleft license warnings, and multiple dependency version detection. Used by: CI/CD security scans and dependency auditing to ensure compliance and security standards. |
| `lints.toml` | Rust linting configuration for Tari project code quality enforcement. Defines deny list of critical lints (clippy::correctness, unused imports/variables, dead code), allow list for acceptable patterns (too_many_arguments, mutable_key_type for RistrettoPublicKey), and warn list for new lints. Features comprehensive clippy rules covering correctness, style, performance, and common mistakes. Used by: CI/CD pipeline and development builds for consistent code quality standards across the Tari codebase. |
| `package.json` | Node.js package configuration for Tari project. Defines project metadata, dependencies, and scripts for JavaScript/TypeScript components. Includes workspace configuration for managing multiple packages and development dependencies. Used for frontend development, build tooling, and JavaScript-based utilities in the Tari ecosystem. |
| `pnpm-lock.yaml` | PNPM package manager lock file. Contains exact dependency versions and resolution information for reproducible JavaScript/TypeScript builds. [GENERATED] file created by PNPM to ensure consistent dependency installation across environments. Used to lock dependency versions for frontend and JavaScript tooling components. |
| `pnpm-workspace.yaml` | PNPM workspace configuration for Tari project. Defines workspace structure and package organization for multi-package JavaScript/TypeScript development. Configures package discovery patterns and workspace-level settings. Used to manage multiple frontend packages and development tooling in a unified workspace. |
| `rust-toolchain.toml` | Rust toolchain specification for Tari project build consistency. Defines stable channel requirement with detailed update instructions referencing Docker images (rust_tari-build-with-deps, rust-ndk), CI files, and Makefile dependencies. Includes developer notes about toolchain update complexity and multiple codebase references. Used by: all Rust compilation to ensure consistent compiler version across development and production environments. |
| `rustfmt.toml` | Rust code formatting configuration using edition 2021 with 120-character line width. Enables features like grouped imports (StdExternalCrate), comment formatting, trailing commas, and shorthand syntax. Uses nightly formatting features for enhanced code organization including impl item reordering and delimited expression overflow handling. Enforces consistent code style across the entire Tari Ootle codebase. |

### .cargo/

| File | Description |
|------|-------------|
| `config.toml` | Cargo configuration defining custom profiles, cross-compilation linkers, and CI command aliases. Provides release-with-debug profile, linkers for aarch64 and riscv64 targets, and convenient aliases for CI operations (fmt, clippy, test, cucumber). Streamlines development and CI workflows with pre-configured commands for code quality checks and testing. |

### .config/

| File | Description |
|------|-------------|
| `nextest.toml` | Nextest configuration for test execution with default and CI profiles. Excludes integration_tests package and export_bindings tests from default runs. CI profile includes 60-second slow timeout with terminate-after limit, and JUnit XML output for CI integration. Optimizes test execution for both development and automated CI environments. |

### .github/

| File | Description |
|------|-------------|
| `PULL_REQUEST_TEMPLATE.md` | GitHub pull request template providing standardized structure for PR submissions. Contains sections for: Description of changes, Motivation and Context, Testing methodology, Review process guidance, and Breaking Changes checklist (data directory deletion, other breaking changes). Ensures consistent PR documentation and helps reviewers understand the scope and impact of proposed changes. |
| `dependabot.yml` | Dependabot configuration for automated dependency updates. Monitors GitHub Actions in the repository root with weekly update schedules. Helps maintain up-to-date CI/CD workflows and reduces security vulnerabilities through automated dependency management. |

#### .github/ISSUE_TEMPLATE/

| File | Description |
|------|-------------|
| `bug_report.md` | GitHub issue template for bug reports. Provides structured format for users to report bugs with sections for: bug description, reproduction steps, expected behavior, screenshots, desktop/mobile environment details, and additional context. Uses GitHub issue template YAML front matter with automatic 'bug-report' label assignment. Essential for maintaining consistent bug reporting across the Tari Ootle project. |

#### .github/workflows/

| File | Description |
|------|-------------|
| `audit.yml` | Security audit workflow using cargo-audit to check for vulnerabilities in dependencies. Runs daily at 5:43 AM via cron schedule and supports manual dispatch. Uses rustsec/audit-check action to scan for known security advisories in Rust dependencies, ensuring supply chain security for the project. |
| `build_binaries.json` | CI/CD build matrix configuration defining target platforms for cross-compilation of Tari Ootle binaries. Specifies build environments for: Linux x86_64/ARM64/RISC-V, macOS x86_64/ARM64, Windows x64/ARM64. Each target includes platform runner, Rust toolchain, compilation target, cross-compilation flags, and build enablement settings. ARM64 and RISC-V targets marked as 'best effort' builds. Used by GitHub Actions workflows for automated binary release generation. |
| `build_binaries.yml` | Comprehensive binary build workflow creating cross-platform releases for tari_ootle_walletd, tari_indexer, tari_validator_node, and tari_swarm_daemon. Triggers on version tags, scheduled builds, and manual dispatch. Supports multiple architectures, platforms, and includes packaging, signing, and release artifact management. Central CI/CD component for distributing production binaries. |
| `build_dockers.yml` | GitHub Actions workflow for building and publishing Docker images. Triggers on: version tags, development branch pushes, scheduled daily/weekly builds, and manual dispatch. Supports multi-architecture builds (linux/amd64, linux/arm64) with configurable platforms and version tagging. Includes environment setup job that determines build parameters based on trigger type (tagged releases build everything, scheduled builds are limited, manual builds are selective). Delegates actual building to build_dockers_workflow.yml with inherited secrets and computed parameters. |
| `build_dockers_workflow.yml` | Reusable GitHub Actions workflow for Docker image building and publishing. Implements multi-architecture builds (linux/amd64, linux/arm64), handles version tagging, registry authentication, and platform-specific compilation. Called by build_dockers.yml with configurable parameters for platforms, versions, and tag aliases. Supports automated Docker Hub publishing with proper versioning and architecture support. |
| `ci.yml` | GitHub Actions CI/CD workflow for automated testing, building, and validation. Includes code formatting, comprehensive test suites, cross-platform builds, security audits, and deployment automation. Comprehensive quality assurance pipeline for Rust blockchain development. |
| `coverage.yml` | Code coverage analysis workflow using LLVM tools for generating test coverage reports. Runs on manual dispatch with nightly Rust toolchain, installs dependencies, executes tests with coverage instrumentation, and generates coverage reports. Essential for maintaining code quality and ensuring comprehensive test coverage across the codebase. |
| `npm_publish.yml` | GitHub Actions workflow for publishing TypeScript bindings and wallet client to NPM registry. Runs on development branch pushes with Node.js 23.11.0 and pnpm 10. Contains two jobs: publish-typescript-bindings (builds and publishes core TypeScript bindings from bindings/ directory) and publish-wallet-daemon-client (publishes wallet daemon client from clients/javascript/wallet_daemon_client/ with dependency on TypeScript bindings). Uses NPM_TOKEN secret for authentication and includes duplicate publish protection. |
| `pr_signed_commits_check.yml` | GitHub workflow for verifying signed commits in pull requests. Uses 1Password's check-signed-commits-action to ensure all commits in PRs are cryptographically signed. Security workflow enforcing commit signing requirements to maintain code integrity and authorship verification in the repository. |
| `pr_title.yml` | GitHub workflow for validating pull request titles against Conventional Commits specification. Uses commitlint with conventional config to ensure PR titles follow standardized format (feat:, fix:, docs:, etc.). Maintains consistent commit message standards and facilitates automated changelog generation. |
| `test_results.yml` | GitHub Actions workflow for collecting and publishing test results. Processes test outputs from CI runs, generates test reports, and publishes results for visibility. Handles test result aggregation, failure analysis, and integration with GitHub's test reporting features for comprehensive test coverage monitoring. |

### .run/

| File | Description |
|------|-------------|
| `Run tariswap_bench.run.xml` | IntelliJ IDEA/CLion run configuration for executing Tariswap benchmark tests. Contains IDE-specific settings for running performance benchmarks, test parameters, and execution environment configuration. Used for development and performance testing of smart contract operations in the IDE environment. |

#### applications/tari_app_utilities/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for tari_ootle_app_utilities crate providing shared application infrastructure. Dependencies include core Tari crates (common, crypto, engine, storage), networking components, epoch management, template handling, and external crates (tokio, serde, config, etc.). Serves as the foundation library for all Tari applications with common utilities for configuration, networking, transaction processing, and storage management. |

##### applications/tari_app_utilities/config_presets/

| File | Description |
|------|-------------|
| `a_common.toml` | Common configuration preset defining shared settings across Tari applications. Includes auto-update configuration with DNS-based update checking, stagenet-specific update URIs, metrics server bindings, and common paths. Provides foundation configuration inherited by validator nodes, indexers, and wallets. Part of the configuration preset system allowing environment-specific overrides while maintaining consistent base settings. |
| `b_peer_seeds.toml` | Peer seeds configuration presets for different Tari networks (nextnet, stagenet, esmeralda, igor). Defines DNS seed hosts and static peer addresses for network bootstrapping. Each network section contains dns_seeds arrays for dynamic peer discovery and peer_seeds arrays for hardcoded peer addresses. DNS seeds use domain names (seeds.nextnet.tari.com, etc.) while peer_seeds specify node public keys with network addresses (IP4/IP6/Onion). Igor network includes IPv6 peer configurations. Used by P2P networking layer for initial network discovery and connectivity. |
| `c_validator_node.toml` | Validator node configuration preset defining identity files, networking settings, data directories, API endpoints, and database options. Configures JSON-RPC (port 18200), web UI (port 5000), P2P networking, auto-registration, and storage backend selection (SQLite/RocksDB). Central configuration template for validator node deployment across different environments with sensible defaults. |
| `d_indexer.toml` | Indexer configuration preset defining identity management, networking, API endpoints, and event filtering. Configures JSON-RPC (port 18300), web UI (port 15000), scanning intervals, and flexible event filters by topic, entity_id, substate_id, or template_address. Default empty filter persists all network events. Essential configuration for blockchain data indexing and API services. |
| `e_wallet_daemon.toml` | Wallet daemon configuration preset defining JSON-RPC listener address, signaling server settings, and network-specific indexer endpoints. Provides default configuration for local development (port 9000) and Igor testnet with specific indexer URLs. Essential configuration for wallet daemon deployment across different network environments. |
| `f_epoch_oracle.toml` | Configuration template for Epoch Oracle component that determines epoch timing and validator sets. Supports three oracle types: BaseLayer (uses Tari base node for epoch determination and L2 UTXO scanning), Configured (uses pre-defined config file for epochs/validators), and Hybrid (combines base layer epoch determination with configured validators). Base layer section configures Minotari node gRPC URL and scanning intervals. Configured section specifies JSON/TOML config file path with epoch timing, validators, public keys, claim keys, shard groups, and registration epochs. Used by indexer and validator nodes for epoch management. |

##### applications/tari_app_utilities/src/

| File | Description |
|------|-------------|
| `common.rs` | Common utility functions shared across Tari applications. Provides network verification functionality to ensure base node client connection matches configured network (localnet, stagenet, mainnet). Prevents configuration mismatches that could lead to incorrect network operations. Simple but critical utility for maintaining network consistency across the ecosystem. |
| `configuration.rs` | Configuration loading and management system for Tari applications. Provides functions to load configurations from file paths, apply overrides, handle network-specific settings, and create default configurations from embedded presets. Supports automatic config file creation, environment variable overrides, and network switching (localnet, stagenet, mainnet). Central configuration system used across all Tari applications for consistent settings management. |
| `epoch_oracle_config.rs` | Epoch Oracle configuration structures and loading logic for determining network epochs and validator sets. Defines EpochOracleConfig with three oracle types: BaseLayer (scans Minotari base layer), Configured (uses static config file), and Hybrid (combines both). BaseLayerOracleConfig specifies base node gRPC URL and scanning intervals. ConfiguredOracleConfig handles loading TOML/JSON epoch configuration files with async load() method supporting both formats. Used by epoch manager to determine current epoch, validator sets, and timing for consensus operations. Implements SubConfigPath trait for configuration hierarchy. |
| `keypair.rs` | Cryptographic keypair management utilities for Tari applications. Provides RistrettoKeypair wrapper with secure file I/O, identity loading/saving, and permissions validation. Includes setup prompts for interactive key generation and secure storage with proper file permissions. Core security component used across all Tari applications for identity management and cryptographic operations. |
| `lib.rs` | Shared application utilities used across Tari Ootle applications. Provides common configuration management, keypair utilities, P2P configuration, seed peer handling, substate caching, template management, and transaction execution. Foundation library for application infrastructure. |
| `p2p_config.rs` | P2P networking configuration utilities for Tari applications. Defines P2pConfig with mDNS, rendezvous, listener port, and reachability mode settings. Provides ReachabilityMode enum (Auto/Private) with conversions to tari_networking types. Essential configuration component for libp2p-based networking across validator nodes, indexers, and other network participants. |
| `seed_peer.rs` | Seed peer parsing and management utilities. Defines SeedPeer struct containing public key and multiaddr for network peer discovery. Provides parsing from string format, peer ID conversion, and libp2p integration. Critical component for bootstrap peer connectivity and network formation in the Tari P2P ecosystem. |
| `substate_file_cache.rs` | File-based substate caching implementation using Tari BOR encoding. Provides SubstateFileCache for persistent storage of substate data with read/write operations and directory management. Used by indexers and validators for efficient substate lookup and caching to improve query performance and reduce network overhead. |
| `template_download_queue.rs` | Template download queue implementation to resolve circular dependencies between epoch manager and template manager services. Provides asynchronous queuing mechanism for template downloads with epoch awareness, author verification, and WASM binary hash validation. Critical component for coordinating template management across network epochs. |
| `transaction_executor.rs` | Transaction execution framework providing unified interface for executing transactions across the Tari ecosystem. Defines TransactionExecutor trait, ExecutionOutput with result finalization, and input lock resolution for substate management. Central component enabling consistent transaction processing across validator nodes, indexers, and testing environments. |
| `utxo_store.rs` | UTXO store implementation for managing burnt UTXOs from Layer-1 Minotari claims. Provides StateUtxoStore for tracking unclaimed confidential outputs across epochs, handling duplicate detection, and integration with state storage. Essential component for burn-claim bridge functionality between L1 and L2 networks. |

#### applications/tari_generate/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for tari_generate application - a code generation tool for Tari smart contract templates. Dependencies include: tari_engine for template processing, liquid/liquid-core for template rendering, clap for CLI interface, convert_case for string transformations, reqwest for HTTP requests, and standard serialization libraries. Designed to generate client code (like Rust CLIs) from smart contract template definitions using liquid templating engine. |

###### applications/tari_generate/liquid_templates/rust_cli/

| File | Description |
|------|-------------|
| `.gitignore.liquid` | Liquid template for generating .gitignore file in generated Rust CLI projects. Simple template that excludes the 'target' directory (Cargo build artifacts). Used by tari_generate when creating Rust CLI client code from smart contract templates to ensure generated projects have proper Git ignore patterns for Rust development. |
| `Cargo.toml.liquid` | Liquid template for generating Cargo.toml manifest in Rust CLI projects created from smart contract templates. Dynamically generates package name using template_name converted to snake_case with '_cli' suffix. Conditionally includes dependencies from local crate paths (if crates_root variable set) or Git repository (development branch). Dependencies include: tari_wallet_daemon_client, tari_engine_types, tari_template_lib, tari_transaction for blockchain interaction; clap for CLI, reqwest for HTTP, serde_json and tokio for async operations. Used by tari_generate to create complete Cargo projects for smart contract interaction. |

###### applications/tari_generate/liquid_templates/rust_cli/src/

| File | Description |
|------|-------------|
| `cli.rs.liquid` | Comprehensive Liquid template for generating CLI interface for smart contract interaction. Creates clap-based CLI with global options (daemon endpoint, auth token, template address, fee settings, account) and dynamic subcommands for each contract method/function. Includes login module for wallet daemon authentication. Each command module handles method/function calls with parameter parsing, bucket handling for resource transfers, instruction building, and transaction submission. Supports both component methods (require component_address) and template functions. Generates complete CLI applications for interacting with deployed smart contracts via wallet daemon. |
| `daemon_client.rs.liquid` | Liquid template for generating wallet daemon client wrapper in Rust CLI projects. Creates DaemonClient struct with authentication flow (login/grant), instruction submission (single/multiple), and transaction result waiting. Handles authentication token management, transaction fee configuration, bucket dumping for resource outputs, dry run capabilities, and other inputs for complex transactions. Uses WalletDaemonClient for underlying wallet daemon communication via JSON-RPC. Provides abstraction layer for smart contract CLI interactions with proper error handling and transaction lifecycle management. |
| `main.rs.liquid` | Liquid template for generating Rust CLI applications for smart contract interaction. Provides template-based main.rs with CLI parsing, daemon client initialization, authentication, and command routing. Part of the code generation system enabling automatic creation of CLI tools for smart contract templates with standardized patterns. |

##### applications/tari_generate/src/

| File | Description |
|------|-------------|
| `main.rs` | Code generation tool for creating template bindings and client libraries. Supports generating clients from WASM templates, building templates, publishing to networks, and scaffolding new projects. Development utility for smart contract development workflow automation. |

###### applications/tari_generate/src/generators/

| File | Description |
|------|-------------|
| `mod.rs` | Code generation module defining traits and types for generating client code from Tari smart contract templates. Key components: TemplateDefinition struct wrapping template name and TemplateDef, CodeGenerator trait for generation implementations, GeneratorOpts for configuration with output paths and liquid template options, GeneratorType enum supporting RustTemplateCli generation. Converts LoadedTemplate into TemplateDefinition for processing. Supports liquid templating with variables and formatting options. Used by tari_generate application to create Rust CLI clients from smart contract templates. |

###### applications/tari_generate/src/generators/liquid/

| File | Description |
|------|-------------|
| `mod.rs` | Liquid template generator implementation for code generation. Provides RustCli template support with embedded template files (Cargo.toml, .gitignore, src files), ABI processing, and dynamic code generation based on smart contract definitions. Core component of the template generation system enabling automated project scaffolding. |
| `snake_case.rs` | Custom Liquid template filter for converting strings to snake_case format. Implements liquid Filter trait using convert_case crate for case conversion. Registers as 'snake_case' filter in liquid templating engine, used in code generation templates to transform template names and identifiers into proper Rust naming conventions. Essential for generating valid Rust code from smart contract template definitions that may use different naming conventions. |

#### applications/tari_indexer/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the Tari indexer application. Dependencies include core Tari crates (consensus, engine, storage), external libraries (diesel, axum, GraphQL), and optional web UI feature. Implements blockchain data indexing, GraphQL API server, JSON-RPC endpoints, and SQLite storage. Key application for providing searchable access to blockchain state and transaction history. |
| `build.rs` | Build script for Tari Indexer application that conditionally builds the React TypeScript web UI. Checks for web_ui cargo feature and debug mode flags before building. In release mode with web_ui feature enabled, runs 'pnpm install' and 'pnpm run build' in the web_ui directory to compile the React frontend. Creates web_ui/dist directory and sets cargo rerun triggers for UI source files. Handles cross-platform pnpm command differences (pnpm.cmd on Windows). Provides warning messages when web UI build is skipped due to missing features or debug mode. |
| `log4rs_sample.yml` | Comprehensive log4rs configuration template for Tari Indexer with segregated logging by component. Defines multiple appenders: stdout (console INFO+), network.log (P2P communication), ootle.log (core application), json_rpc.log (API calls), libp2p.log (P2P library), other.log (third-party errors). Uses rolling file policies with 10MB rotation and 5 file retention. Configures specific loggers for different modules: tari::indexer (debug), networking (info), libp2p components (debug/info), external crates (error/info). Supports templated log_dir variable and includes node identification in log patterns for distributed debugging. |
| `openrpc.json` | OpenRPC specification for Tari Indexer JSON-RPC API. Defines service metadata, server endpoints, method signatures, parameter schemas, and example requests/responses for indexer API methods like get_connections. Provides standardized API documentation for client integration and automated tooling generation. |

##### applications/tari_indexer/src/

| File | Description |
|------|-------------|
| `block_data.rs` | Block data structure for the Tari indexer containing block information and associated substate updates. Simple data container coupling blocks with their state changes for efficient processing and storage in the indexer pipeline. Essential component for maintaining blockchain state coherence during indexing operations. |
| `bootstrap.rs` | Bootstrap initialization for the Tari indexer service. Handles identity management, base layer client setup, epoch oracle configuration, networking initialization, and service coordination. Provides comprehensive startup sequence including network verification, P2P setup, and integration with the broader Ootle ecosystem for reliable indexer deployment. |
| `cli.rs` | Command-line interface (CLI) parser for the Tari Indexer application using clap. Defines Cli struct with options for: substate addresses to monitor, scanning interval, JSON-RPC bind address, epoch oracle configuration, networking settings (peer seeds, listener port, reachability mode, mDNS), and web UI public URLs. Implements ConfigOverrideProvider trait to convert CLI arguments into configuration overrides for different networks. Supports environment variable configuration (TARI_INDEXER_MINOTARI_NODE_GRPC_URL, TARI_INDEXER_WEB_UI_PUBLIC_JSON_RPC_URL, TARI_INDEXER_WEB_UI_PUBLIC_GRAPHQL_URL). Used by main.rs to parse command-line arguments and configure the indexer service. |
| `config.rs` | Configuration management for the Tari indexer application. Defines ApplicationConfig, IndexerConfig with network settings, API endpoints, scanning intervals, event filters, and template management configuration. Provides structured configuration loading with defaults and validation for indexer deployment across different network environments. |
| `event_data.rs` | Simple data structure for coupling blockchain events with their associated substate data. EventData struct contains Event (from engine_types) and optional Substate for event context. Used throughout the indexer for maintaining relationships between events and the substrate state changes they represent. Serializable via serde for storage and API responses. |
| `event_manager.rs` | Event management system for the Tari indexer handling blockchain event processing, filtering, and storage. Manages event detection, substate scanning, metadata extraction, and database persistence. Core component coordinating between validator nodes and indexer storage for comprehensive event tracking and searchable blockchain history. |
| `event_scanner.rs` | Core blockchain event scanning engine for Tari Indexer. EventScanner monitors validator network for new blocks, transactions, and state changes, filtering events based on configured criteria. Key functionality: syncs blocks from validator committees using epoch manager, processes substate updates and events, applies event filters, stores events and substates in SQLite database, handles template changes for smart contracts. Implements comprehensive error handling for network failures and provides detailed logging. Critical component for maintaining real-time blockchain state index and supporting GraphQL/JSON-RPC queries. |
| `lib.rs` | Tari Indexer application library providing blockchain data indexing and querying services. Includes GraphQL/JSON-RPC APIs, event scanning, block processing, web UI, and database management. Core service for making blockchain data accessible to applications and explorers. |
| `main.rs` | Indexer application entry point providing blockchain data indexing and querying services. Handles CLI parsing, configuration loading, panic handling, and indexer service initialization. Provides database indexing of blockchain state, events, and transactions for efficient querying. Essential service for blockchain data access, analytics, and historical state queries with comprehensive error handling and graceful shutdown. |
| `network_client.rs` | Network client for Tari indexer providing validator node communication and transaction submission. Handles epoch management, shard-based routing, transaction distribution, and RPC client coordination. Essential component enabling indexer integration with the validator network for real-time blockchain data synchronization and transaction processing. |
| `substate_manager.rs` | Substate management service providing high-level access to blockchain state for GraphQL/JSON-RPC APIs. SubstateManager orchestrates substate queries across validator network and local storage cache. Key features: retrieves substates by ID/version from network or database, lists substates with filtering and pagination, handles non-fungible token queries with metadata, caches substates using SubstateFileCache for performance. Response types include SubstateResponse and NonFungibleResponse for API serialization. Abstracts complexity of distributed state retrieval for client applications. |

###### applications/tari_indexer/src/dry_run/

| File | Description |
|------|-------------|
| `error.rs` | Error types for the dry run transaction processor in Tari Indexer. Defines DryRunTransactionProcessorError enum with variants for: substate down/unavailable errors, epoch manager failures, RPC communication errors, transaction processor errors, validator committee failures (with detailed failure counts), indexer errors, state store errors, and non-dry-run transaction validation. Includes comprehensive error context with epoch, substate requirements, committee sizes, and failure thresholds for debugging validator network issues during transaction simulation. |
| `mod.rs` | Module definition for dry run transaction processing functionality in Tari Indexer. Exposes error types (error.rs) and processor implementation (processor.rs) for simulating transaction execution without committing to the blockchain. Used by indexer's JSON-RPC API to provide transaction preview and validation capabilities, allowing clients to test transactions before actual submission to the network. |
| `processor.rs` | Dry run transaction processor for simulating transaction execution without committing to blockchain state. Implements DryRunTransactionProcessor that uses TariTransactionProcessor with in-memory state store for transaction validation and preview. Key features: loads substates from validator network via committee consensus, handles virtual substates, validates transaction logic without permanent effects, provides detailed execution results including fees and outputs. Uses epoch manager for validator discovery, template manager for WASM modules, and state store abstractions. Essential for JSON-RPC API dry run capabilities allowing clients to test transactions before submission. |

###### applications/tari_indexer/src/graphql/

| File | Description |
|------|-------------|
| `mod.rs` | GraphQL API module for Tari Indexer providing flexible blockchain data queries. Exports GraphQL schema models and server implementation. Enables rich, typed queries for blockchain data exploration, analytics, and application integration with custom query capabilities. |
| `server.rs` | GraphQL server implementation for the Tari indexer providing flexible blockchain data queries. Implements async-graphql schema with event queries, GraphQL playground interface, CORS support, and HTTP routing. Enables powerful blockchain data exploration with complex queries, filtering, and real-time access to network state and transaction history. |

###### applications/tari_indexer/src/graphql/model/

| File | Description |
|------|-------------|
| `events.rs` | GraphQL event model and schema definitions for blockchain event queries. Defines Event struct with fields: substate_id, template_address, tx_hash, topic, payload (key-value map). Implements conversion from engine event types and provides GraphQL query resolvers via async-graphql. Supports flexible event filtering and retrieval through GraphQL interface. Integrates with EventManager for data access and provides structured event data for GraphQL API consumers. |
| `mod.rs` | GraphQL model definitions module organizing data structures for GraphQL API. Contains event models and other GraphQL schema types for blockchain data queries. Provides structured data representations for GraphQL resolvers and client applications. |

###### applications/tari_indexer/src/http_ui/

| File | Description |
|------|-------------|
| `mod.rs` | HTTP web UI module definition for Tari Indexer React-based administration interface. Exposes server component for serving the compiled React application and providing web-based access to indexer functionality. Used when web_ui feature is enabled to provide graphical interface for monitoring and managing the indexer. |
| `server.rs` | HTTP server implementation for serving the React-based web UI of Tari Indexer. Provides static file serving for compiled React application, API proxy configuration, and web server setup. Handles routing between UI routes and backend API endpoints (JSON-RPC, GraphQL). Enables web-based administration and monitoring interface when web_ui feature is enabled. |

###### applications/tari_indexer/src/json_rpc/

| File | Description |
|------|-------------|
| `error.rs` | JSON-RPC error handling utilities for Tari Indexer API. Provides internal_error() function that creates standardized JsonRpcResponse error messages. Includes debug-mode error details in development/CI environments while sanitizing error messages in production for security. Uses axum_jrpc error types for consistent JSON-RPC error responses. |
| `handlers.rs` | Comprehensive JSON-RPC API handlers for Tari Indexer providing blockchain data access and network operations. JsonRpcHandlers implements methods for: substate queries (get_substate, list_substates), template management (add_template, get_template), transaction operations (submit_transaction, get_transaction), network diagnostics (get_connections, get_comms_stats), epoch information, non-fungible queries, and peer management. Supports dry run transaction execution, template installation, network peer addition/management. Integrates with SubstateManager, TransactionManager, and networking services. Central API layer for wallet daemons and external applications to interact with Tari blockchain. |
| `mod.rs` | JSON-RPC API module for Tari Indexer providing structured blockchain data access. Exports RPC handlers, error handling, and server spawning functionality. Complementary to GraphQL API with standardized RPC interface for blockchain data retrieval and system integration. |
| `server.rs` | JSON-RPC server implementation for the Tari indexer providing standardized API access to blockchain data. Implements axum-based HTTP server with JSON-RPC 2.0 protocol, CORS support, request logging, and configurable body limits for large transactions. Provides compatible API interface for existing blockchain tooling and applications. |

###### applications/tari_indexer/src/storage_sqlite/

| File | Description |
|------|-------------|
| `README.md` | Documentation for SQLite storage implementation in Tari Indexer. Explains database schema, migration procedures, and storage layer architecture. Provides guidance for database operations, schema changes, and maintenance procedures for the indexer's persistent storage system. |
| `diesel.toml` | Diesel ORM configuration file specifying database schema location and migration directory for SQLite database. Configures Diesel code generation and migration management for the indexer's persistent storage layer. |
| `mod.rs` | SQLite storage module definition for Tari Indexer database layer. Organizes storage components: models/ (data structures), schema (Diesel-generated schema), serialization (data conversion), store_factory (database operations). Provides abstraction layer for SQLite-based persistence of substates, events, transactions, and NFT indexes. Central module for database access throughout the indexer application. |
| `schema.rs` | [GENERATED] Diesel-generated database schema definitions for SQLite tables. Contains table! macros defining database structure for events, substates, non_fungible_indexes, scanned_block_ids, and other tables. Automatically generated from migrations and should not be manually edited. Used by Diesel ORM for type-safe database queries and operations. |
| `serialization.rs` | Serialization utilities for SQLite storage operations. Provides JSON serialization/deserialization functions with error handling (serialize_json, deserialize_json) and hex encoding for binary data. Includes proper error mapping to StorageError with type information for debugging. Used throughout storage layer for data persistence and retrieval operations. |
| `store_factory.rs` | Comprehensive database store factory providing all storage operations for Tari Indexer. Implements IndexerStore trait with methods for: substate storage/retrieval, event management, NFT indexing, transaction recording, block scanning progress tracking. Handles SQLite connections, embedded migrations, transactions, and error handling. Key traits: IndexerStoreReadTransaction for queries, IndexerStoreWriteTransaction for data modifications. Central component for all database operations in the indexer. |

###### applications/tari_indexer/src/storage_sqlite/migrations/

| File | Description |
|------|-------------|
| `.gitkeep` | Git keep file ensuring the migrations directory is preserved in version control even when empty. Required for Diesel migration system to function properly. |

###### applications/tari_indexer/src/storage_sqlite/migrations/2023-02-16-145719_create_substates/

| File | Description |
|------|-------------|
| `down.sql` | [MIGRATION] Diesel migration rollback script for dropping the 'substates' table and its associated indexes. Reverts the create_substates migration to restore database to previous state. |
| `up.sql` | [MIGRATION] Diesel migration creating the core 'substates' table for storing blockchain substate data. Table structure: id (autoincrement primary key), address (text substate identifier), version (integer version number), data (text serialized substate content). Creates unique index on address field for fast lookups. Foundation table for indexer's substate storage and retrieval functionality. |

###### applications/tari_indexer/src/storage_sqlite/migrations/2023-03-02-162407_create_nft_indexes/

| File | Description |
|------|-------------|
| `down.sql` | [MIGRATION] Diesel migration rollback script for dropping the non_fungible_indexes table and its indexes. Reverts the create_nft_indexes migration. |
| `up.sql` | [MIGRATION] Diesel migration creating non_fungible_indexes table for NFT resource indexing. Table structure: id (autoincrement), resource_address (NFT collection), idx (position in collection), non_fungible_address (individual NFT). Foreign key constraints to substates table ensure referential integrity. Unique index on (resource_address, idx) ensures no duplicate positions. Performance index for efficient NFT collection queries and enumeration. |

###### applications/tari_indexer/src/storage_sqlite/migrations/2023-04-28-153806_create_events_table/

| File | Description |
|------|-------------|
| `down.sql` | [MIGRATION] Diesel migration rollback script for dropping the 'events' table and its associated indexes. Reverts the create_events_table migration. |
| `up.sql` | [MIGRATION] Diesel migration creating the 'events' table for storing blockchain events emitted by smart contracts. Table structure: id (autoincrement), template_address (contract template), tx_hash (transaction hash), topic (event type), payload (event data). Creates unique index on (template_address, tx_hash, topic) and performance index on (template_address, tx_hash) for efficient event queries. |

###### applications/tari_indexer/src/storage_sqlite/migrations/2023-05-03-054827_add_transaction_hash_to_substate/

| File | Description |
|------|-------------|
| `down.sql` | [MIGRATION] Diesel migration rollback script removing transaction_hash column from substates table. Reverts the add_transaction_hash_to_substate migration. |
| `up.sql` | [MIGRATION] Diesel migration adding transaction_hash column to substates table for tracking which transaction created each substate. Enables linking substates to their originating transactions for audit trails and enhanced query capabilities. |

###### applications/tari_indexer/src/storage_sqlite/migrations/2023-05-19-165832_add_version_to_events_table/

| File | Description |
|------|-------------|
| `down.sql` | [MIGRATION] Diesel migration rollback script removing version column from events table. Reverts the add_version_to_events_table migration. |
| `up.sql` | [MIGRATION] Diesel migration adding version column to events table for tracking event versioning and evolution. Enables support for multiple event versions and backward compatibility in event processing. |

###### applications/tari_indexer/src/storage_sqlite/migrations/2024-03-19-114926_refactor_event_payloads/

| File | Description |
|------|-------------|
| `down.sql` | [MIGRATION] Diesel migration rollback script reverting event payload refactoring changes. Restores previous event payload storage structure. |
| `up.sql` | [MIGRATION] Diesel migration refactoring event payload storage structure for improved performance and flexibility. Updates event data schema to support enhanced payload formats and more efficient querying capabilities. |

###### applications/tari_indexer/src/storage_sqlite/migrations/2024-03-21-212928_refactor_event_substate/

| File | Description |
|------|-------------|
| `down.sql` | [MIGRATION] Diesel migration rollback script reverting event-substate refactoring changes. Restores previous event-substate relationship storage structure. |
| `up.sql` | [MIGRATION] Diesel migration refactoring event-substate relationship storage for improved performance and data integrity. Updates schema to better link events with their associated substates and enable more efficient queries. |

###### applications/tari_indexer/src/storage_sqlite/migrations/2024-04-04-201211_create_scanned_block_ids/

| File | Description |
|------|-------------|
| `down.sql` | [MIGRATION] Diesel migration rollback script for dropping the scanned_block_ids table and its indexes. Reverts the create_scanned_block_ids migration. |
| `up.sql` | [MIGRATION] Diesel migration creating scanned_block_ids table for tracking event scanning progress across validator committees. Table structure: id (autoincrement), epoch (consensus epoch), shard_group (validator shard), last_block_id (latest scanned block). Unique index on (epoch, shard_group) ensures one scan position per committee. Enables efficient resumption of event scanning and prevents duplicate event processing across network shards. |

###### applications/tari_indexer/src/storage_sqlite/migrations/2024-06-18-095000_add_metadata_to_events_and_substates/

| File | Description |
|------|-------------|
| `down.sql` | [MIGRATION] Diesel migration rollback script removing metadata columns from events and substates tables. Reverts the add_metadata_to_events_and_substates migration. |
| `up.sql` | [MIGRATION] Diesel migration adding metadata columns to both events and substates tables. Enhances data storage with additional contextual information for improved querying and analysis capabilities. |

###### applications/tari_indexer/src/storage_sqlite/migrations/2025-06-25-111041_create_transactions/

| File | Description |
|------|-------------|
| `down.sql` | [MIGRATION] Diesel migration rollback script for dropping the 'transactions' table and its indexes. Reverts the create_transactions migration. |
| `up.sql` | [MIGRATION] Recent Diesel migration creating the 'transactions' table for storing transaction records. Table structure: id (autoincrement), transaction_id (unique text identifier), body (transaction data), created_at (timestamp with default). Creates unique index on transaction_id for fast transaction lookups. Supports transaction history and retrieval functionality in the indexer. |

###### applications/tari_indexer/src/storage_sqlite/models/

| File | Description |
|------|-------------|
| `events.rs` | Diesel ORM models for event storage in SQLite database. Defines Event struct with Queryable/Identifiable traits for database queries, and NewEvent for insertions. Additional models include NewEventPayloadField for structured event data, ScannedBlockId for tracking scan progress, and conversion utilities. Handles event serialization/deserialization, template address mapping, and transaction hash associations. Used by store_factory for event database operations. |
| `mod.rs` | Database model definitions module for SQLite storage. Exposes data models for events (event storage structures) and substate (substate storage structures). Contains Rust structs mapping to database tables with Diesel ORM annotations for serialization/deserialization and query operations. |
| `substate.rs` | Diesel ORM models for substate storage in SQLite database. Defines Substate struct with fields: id, address, version, data, tx_hash, template_address, module_name. Includes NewSubstate for insertions and conversion utilities for SubstateResponse. Provides database mapping for blockchain state storage with template associations and transaction tracking. Used by store_factory for substate database operations. |

###### applications/tari_indexer/src/transaction_manager/

| File | Description |
|------|-------------|
| `error.rs` | Error types for transaction management operations in Tari Indexer. Defines TransactionManagerError enum covering network communication failures, validator node errors, storage errors, and transaction processing issues. Provides comprehensive error context for transaction submission, retrieval, and monitoring operations. |
| `mod.rs` | Transaction management service for tracking and retrieving blockchain transactions in Tari Indexer. TransactionManager provides high-level operations: submit_transaction() to network validators, get_transaction() for retrieval with status, wait_for_transaction_result() for monitoring completion. Integrates with validator network via TariNetworkClient and stores transaction records in IndexerStore. Handles transaction lifecycle from submission through completion with comprehensive error handling. |

##### applications/tari_indexer/web_ui/

| File | Description |
|------|-------------|
| `.gitignore` | Git ignore configuration for the Tari Indexer web UI. Excludes common Node.js artifacts including logs, node_modules, build outputs (dist/), and editor files. Standard React/TypeScript frontend gitignore patterns. |
| `.nvmrc` | Node Version Manager configuration specifying LTS Node.js version 'lts/jod' for consistent development environment across the Tari Indexer web UI project. |
| `README.md` | Standard Create React App documentation for the Tari Indexer web UI. React-based administration interface for monitoring indexer status, viewing blockchain data, and managing connections. Available scripts include npm start (development), npm test (testing), npm run build (production build). Built using standard React development workflow and served via indexer's HTTP server when web_ui feature is enabled. |
| `index.html` | Main HTML entry point for the Tari Indexer web UI. Sets up the React root element and loads the TypeScript module from main.tsx. Simple Vite-style HTML template for single-page application. |
| `package.json` | Package configuration for Tari indexer React web UI. Modern React 19 application using Vite, TypeScript, Material-UI components, React Router, TanStack Query, and Tari TypeScript bindings. Provides web-based interface for exploring indexed blockchain data, viewing transactions, substates, events, and validator node connections. |
| `tsconfig.json` | TypeScript configuration for Tari Indexer web UI React app. Targets ESNext with strict type checking, includes DOM libraries, and references bindings for TypeScript definitions. Configured for Vite with React JSX transform. |
| `tsconfig.node.json` | TypeScript configuration for Node.js build tools in the Tari Indexer web UI. Configures TypeScript compiler options for Vite build process with ESNext module support, Node.js module resolution, and synthetic default imports. Includes vite.config.ts file and enables composite project structure for build tooling. |
| `vite.config.ts` | Vite build configuration for Tari Indexer web UI. Simple setup using React SWC plugin for fast development and builds. Standard React development server configuration. |

###### applications/tari_indexer/web_ui/public/

| File | Description |
|------|-------------|
| `.gitkeep` | Git placeholder file to maintain the public directory structure. Used to ensure the public folder is included in version control even when empty. |
| `index.html` | HTML template for the Tari Indexer web UI when built for production. Contains manifest linking, PWA configuration, and React root element. Includes build-time placeholder variables for public URL resolution. |
| `manifest.json` | PWA manifest for Tari Indexer web UI, configuring it as a Progressive Web App. Defines app name as 'Validator node web GUI', standalone display mode, and theme colors. Missing icons configuration. |
| `robots.txt` | Search engine robots configuration file allowing all web crawlers to index the Tari Indexer web UI without restrictions. |

###### applications/tari_indexer/web_ui/src/

| File | Description |
|------|-------------|
| `App.tsx` | Main React application component for Tari indexer web UI. Implements routing for connections, transactions, resources, events, substates, and validator node views. Provides breadcrumb navigation and comprehensive interface for exploring indexed blockchain data. Central application component enabling user-friendly access to all indexer functionality and blockchain exploration tools. |
| `main.tsx` | React application entry point for Tari Indexer web UI. Sets up React Router with routes for connections, transactions, events, substates, and resources. Uses Material-UI theme and renders the main App component with error handling. |
| `vite-env.d.ts` | TypeScript declaration file for Vite environment types in the Tari Indexer web UI. Contains a single reference directive to include Vite client types, enabling TypeScript support for Vite-specific features like import.meta.env and HMR (Hot Module Replacement). Standard configuration file for Vite-based React applications. |

###### applications/tari_indexer/web_ui/src/Components/

| File | Description |
|------|-------------|
| `AlertDialog.tsx` | Reusable confirmation dialog component for Tari Indexer web UI. Exports ConfirmDialog with customizable title, description, and action buttons. Uses Material-UI Dialog components with confirm/cancel actions. |
| `Breadcrumbs.tsx` | Navigation breadcrumbs component using react-router-breadcrumbs and Material-UI. Dynamically generates breadcrumb navigation based on current route, with support for static and dynamic breadcrumb labels. |
| `CopyToClipboard.tsx` | Copy-to-clipboard utility component with visual feedback. Displays a copy icon button with tooltip, copies text to clipboard on click, and shows confirmation snackbar. Used throughout the UI for copying blockchain data. |
| `Copyright.tsx` | Footer copyright component displaying Tari Labs copyright notice with current year and link to tari.com. Simple Material-UI typography component used in page footers. |
| `JsonDialog.tsx` | Full-screen dialog component for displaying JSON data in formatted view. Uses renderJson helper utility and Material-UI dialog with close button. Used for detailed inspection of blockchain objects and transaction data. |
| `JsonTooltip.tsx` | JSON tooltip component displaying formatted JSON data on hover. Uses renderJson helper to format object data and converts 32-byte arrays to hex strings. Used throughout the UI for blockchain data inspection. |
| `MenuItems.tsx` | Navigation menu items configuration for the main sidebar. Defines menu structure with icons (Ionic icons) for Home, Transactions, Events, Substates, and Connections. Exports mainListItems with active/inactive state styling. |
| `PageHeading.tsx` | Styled page heading component with centered H1 and themed underline decoration. Provides consistent page header styling using primary theme color throughout the indexer web UI. |
| `SecondaryHeading.tsx` | React component for displaying secondary page headings in the Tari Indexer web UI. Exports SecondaryHeading component that renders a centered heading with purple underline bar. Dependencies: theme from "../theme/theme". Used by: various page layouts for section headers. Props: children (string) for heading text. Styling uses Material-UI theme palette primary color. |
| `StyledComponents.ts` | Styled Material-UI components with custom theming for the Indexer web UI. Exports AccordionIconButton, StyledPaper, DataTableCell (monospace), CodeBlock, BoxHeading variants, and SubHeading. Provides consistent styling patterns across the application. |
| `Title.tsx` | Simple Material-UI Typography wrapper for section titles. Renders H2 variant with primary color and bottom margin. Used for consistent section headings across the application. |

###### applications/tari_indexer/web_ui/src/assets/

| File | Description |
|------|-------------|
| `Logo.tsx` | React SVG component for the Tari Indexer logo. Exports Logo component with configurable width, height, and fill color props. Contains SVG path definitions for "TARI INDEXER" text logo with geometric shapes. Used by: header/navigation components for branding. Default props: width="100%", height="auto", fill="black". Vector-based scalable logo implementation. |

###### applications/tari_indexer/web_ui/src/routes/

| File | Description |
|------|-------------|
| `ErrorPage.tsx` | React Router error boundary page displaying friendly error messages. Uses Material-UI components to show formatted error information when navigation or component errors occur. Distinguishes between route errors and unexpected exceptions. |

###### applications/tari_indexer/web_ui/src/routes/Connections/

| File | Description |
|------|-------------|
| `Connections.tsx` | Layout component for the Connections page in Tari Indexer web UI. Exports ConnectionsLayout component that wraps the VN/Components/Connections component in Material-UI Grid layout with PageHeading and StyledPaper. Dependencies: PageHeading, Grid, StyledPaper, Connections from VN components. Used by: routing system for displaying peer connections. Simple wrapper providing consistent page layout structure. |

###### applications/tari_indexer/web_ui/src/routes/Events/

| File | Description |
|------|-------------|
| `Events.tsx` | React component for displaying blockchain events with filtering and pagination in Tari Indexer web UI. Exports EventsLayout with Material-UI Table displaying topic, transaction hash, substate ID, template address, and payload. Features: GraphQL filtering by topic/substate_id, pagination with PAGE_SIZE=10, copy-to-clipboard functionality, JSON payload download/view via JsonDialog. Dependencies: Material-UI components, helpers for hex conversion, graphql utils, file-saver. Fetches events via GraphQL API with real-time data display. |

###### applications/tari_indexer/web_ui/src/routes/RecentTransactions/

| File | Description |
|------|-------------|
| `RecentTransactions.tsx` | Layout component for Recent Transactions page in Tari Indexer web UI. Exports RecentTransactionsLayout that wraps the VN/Components/RecentTransactions component in Material-UI Grid layout with PageHeading and StyledPaper. Dependencies: PageHeading, Grid, StyledPaper, RecentTransactions from VN components. Used by: routing system for displaying recent blockchain transactions. Simple wrapper providing consistent page layout structure. |

###### applications/tari_indexer/web_ui/src/routes/Resources/

| File | Description |
|------|-------------|
| `Resources.tsx` | Layout component for Resources page in Tari Indexer web UI. Exports ResourcesLayout that wraps the VN/Components/Resources component in Material-UI Grid layout with PageHeading ("Resource") and StyledPaper. Dependencies: PageHeading, Grid, StyledPaper, Resources from VN components. Used by: routing system for displaying blockchain resources. Simple wrapper providing consistent page layout structure. |

###### applications/tari_indexer/web_ui/src/routes/Substates/

| File | Description |
|------|-------------|
| `Substates.tsx` | React component for displaying blockchain substates with filtering and pagination in Tari Indexer web UI. Exports SubstatesLayout with Material-UI Table showing address, version, template, timestamp, and content. Features: filtering by template/type (Component, Resource, Vault, etc.), pagination with PAGE_SIZE=10, copy-to-clipboard, JSON content download/view via JsonDialog. Dependencies: Material-UI components, tari-project/typescript-bindings, json_rpc utils, react-router-dom Link. Uses listSubstates and getSubstate JSON-RPC calls for data fetching. |

###### applications/tari_indexer/web_ui/src/routes/VN/

| File | Description |
|------|-------------|
| `ValidatorNode.css` | CSS styles for the Validator Node interface in the Tari Indexer web UI. Defines comprehensive styling for validator node components including: section layouts with padding and borders, validator node grid display, committee wrapper and range layouts, button states (normal, disabled, hover, active) with shadow effects, error message styling, table formatting, tooltip functionality with hover states, JSON syntax highlighting (string in green, other in red), and responsive design patterns. Includes monospace font styling for cryptographic keys and addresses, grid-based layouts for committee and member displays, and interactive button styling with shadow effects. |
| `ValidatorNode.tsx` | Main React component for the Validator Node page in the Tari Indexer web UI. Orchestrates display of validator node information through three main sections: Info (node identity), Connections (peer connections), and Recent Transactions. Fetches validator node identity data via getIdentity JSON-RPC call on component mount. Uses Material-UI Grid layout with StyledPaper containers and SecondaryHeading components for structured presentation. Implements error handling and loading states for identity data. Imports and renders child components (Info, Connections, RecentTransactions) with proper data flow and styling integration. |

###### applications/tari_indexer/web_ui/src/routes/VN/Components/

| File | Description |
|------|-------------|
| `Connections.tsx` | React component for managing peer connections in the Tari Indexer web UI. Displays a table of active network connections with peer information (peer ID, address, age, direction, latency, user agent). Features include: auto-refresh connection list every 5 seconds, peer addition dialog with public key and multiaddr input fields, connection management through addPeer and getConnections JSON-RPC calls. Uses Material-UI components (Table, Button, TextField, Form) with custom styling (DataTableCell, BoxHeading2). Implements custom useInterval hook for periodic data fetching and state management for connection list and add peer dialog visibility. |
| `Error.tsx` | Simple React error display component for the Tari Indexer web UI. Takes component name and error message as props and renders them in a styled container with error CSS classes. Used for displaying component-specific error states in the validator node interface. Minimal functional component with no dependencies beyond React. |
| `Info.css` | CSS styles for the Info component table in the Tari Indexer web UI. Sets left text alignment for info-table class and its table cells. Simple styling to ensure consistent left-aligned display of validator node information data. |
| `Info.tsx` | React component displaying validator node identity information in the Tari Indexer web UI. Shows peer ID, listen addresses, and public key in a Material-UI table format. Takes IndexerGetIdentityResponse as props containing node identity data. Uses custom DataTableCell components for styling consistency. Displays network identity information retrieved from the indexer's JSON-RPC API. |
| `RecentTransactions.tsx` | React component displaying recent transactions in the Tari Indexer web UI. Features collapsible table rows showing transaction IDs with expandable sections for fee instructions and transaction instructions. Uses Material-UI components (Table, Collapse, IconButton) with custom accordion functionality. Fetches up to 50 recent transactions via listRecentTransactions JSON-RPC call on component mount. Each transaction row displays transaction_id and provides expand/collapse buttons to view detailed fee_instructions and instructions data formatted as JSON code blocks. Implements RowData sub-component for individual transaction row management with state for controlling accordion expansion. |
| `Resources.tsx` | React component for displaying resource details in the Tari Indexer web UI. Shows resource information including token symbol, resource type, and total supply. For NonFungible resources, displays NFT gallery with images using Material-UI ImageList components. Fetches resource data via getSubstate and getNonFungibles JSON-RPC calls based on resourceAddress URL parameter. Handles CBOR data conversion for NFT metadata (image_url, name) and displays first 10 NFTs with image thumbnails, titles, and addresses. Uses convertCborValue utility for data parsing and substateIdToString for address formatting. Features responsive grid layout for resource information and NFT display. |
| `helpers.tsx` | Utility functions and classes for Tari Indexer web UI. Exports U256 class for 256-bit unsigned integer arithmetic with plus(), minus(), inc(), dec(), comparison operators (gt, ge, lt, le, eq). Also exports compare() for generic comparison, toHexString()/fromHexString() for byte array conversion, and shortenString() for text truncation. Used by: blockchain data display components for address formatting and large number calculations. Includes overflow handling and hex string manipulation. |

###### applications/tari_indexer/web_ui/src/theme/

| File | Description |
|------|-------------|
| `LayoutMain.tsx` | Main layout component for the Tari Indexer web UI using Material-UI components. Implements responsive drawer navigation with collapsible sidebar, app bar with Tari logo, and main content area. Features: collapsible drawer (300px wide) with navigation menu items, top app bar with hamburger menu toggle, breadcrumb navigation, and main content area with responsive grid layout. Uses Material-UI styling system with custom theme integration, responsive breakpoints, and smooth transitions for drawer open/close states. Manages drawer state with React hooks and provides ThemeProvider context for consistent styling throughout the application. Exports as default Layout component for application-wide use. |
| `theme.css` | Global CSS styles and font definitions for the Tari Indexer web UI. Defines custom Avenir font family imports (Regular, Medium, Heavy) from local OTF files, sets global body styling with Avenir font and background color (#f5f5f7), and provides utility classes for form layouts. Includes responsive design classes: flex-container for flexible layouts, add-confirm-form for form styling, heading-menu for header elements, and media queries for mobile (max-width: 767px) and desktop (min-width: 768px) breakpoints. Uses modern CSS with font smoothing and sans-serif fallbacks. |
| `theme.ts` | Material-UI theme configuration for Tari Indexer web UI. Exports createTheme with Tari brand colors (primary: #9330FF purple, secondary: #40388A), AvenirMedium font family, custom typography scale (h1-h6), rounded corners (borderRadius: 10), and component overrides for MuiButton and MuiTableCell. Used by: all UI components for consistent styling. Defines transitions, button styling (no ripple, custom height/fonts), and table cell borders. |

###### applications/tari_indexer/web_ui/src/utils/

| File | Description |
|------|-------------|
| `cbor.ts` | CBOR (Concise Binary Object Representation) data processing utilities for Tari blockchain data. Exports getValueByPath() for traversing CBOR object paths, convertCborValue() for converting CBOR types to JavaScript values, and convertTaggedValue() for handling binary tags (VaultId, ComponentAddress, ResourceAddress, etc.). Handles Maps, Arrays, Text, Bytes, Integers, Booleans, and tagged values. Used by: blockchain data display components for parsing smart contract state and transaction data. |
| `graphql.tsx` | GraphQL endpoint configuration utility for Tari Indexer web UI. Exports getGraphQLAddress() function that dynamically determines the GraphQL server URL by first trying /graphql_address endpoint, then falling back to environment variables (VITE_INDEXER_GRAPHQL_ADDRESS, VITE_GRAPHQL_ADDRESS) or localhost:18301. Used by: components that query the GraphQL API for blockchain data. Handles URL validation and error fallback for flexible deployment configurations. |
| `helpers.tsx` | UI utility functions for the indexer web interface. Exports renderJson for recursive JSON formatting, displayDuration for time formatting, and truncateText for string truncation. Handles special cases like 32-byte arrays (converts to hex) and nested objects. |
| `json_rpc.tsx` | JSON-RPC client for Tari Indexer API communication. Exports typed functions for all indexer endpoints: getIdentity(), addPeer(), getSubstate(), listSubstates(), getConnections(), getNonFungibles(), submitTransaction(), getTransactionResult(), etc. Dependencies: @tari-project/typescript-bindings for type definitions. Features dynamic endpoint discovery via /json_rpc_address or environment variables (VITE_INDEXER_JRPC_ADDRESS). Used by: all UI components for blockchain data fetching and transaction submission. |

#### applications/tari_scaffolder/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for tari_scaffolder application - a development tool for generating project scaffolding from smart contract templates. Dependencies include: tari_engine for template processing, liquid/liquid-core for templating, clap for CLI, convert_case for naming conversions, and serialization libraries. Used to bootstrap new projects and generate client code from existing template definitions. |
| `README.md` | Documentation for Tari Scaffolder development tool. Explains basic usage for generating project scaffolding from smart contract templates using cargo run command with config.json and generator options. Currently supports rust-template-cli generator for creating Rust CLI applications. Planned future support for react-admin-web generator for React-based admin interfaces. Essential tool for smart contract development workflow. |

###### applications/tari_scaffolder/liquid_templates/rust_cli/

| File | Description |
|------|-------------|
| `.gitignore.liquid` | Liquid template for .gitignore file in Rust CLI projects generated by tari_scaffolder. Simple template that ignores the 'target' directory where Rust build artifacts are stored. Part of the scaffolding system for generating new Rust CLI applications with standard project structure. |
| `Cargo.toml.liquid` | Liquid template for generating Rust CLI application Cargo.toml files. Configures dependencies for Tari wallet daemon client, engine types, template library, and transaction handling. Supports both local path and git dependencies based on crates_root variable. |

###### applications/tari_scaffolder/liquid_templates/rust_cli/src/

| File | Description |
|------|-------------|
| `cli.rs.liquid` | Liquid template for generating Rust CLI applications that interact with Tari smart contracts. Creates a comprehensive command-line interface using clap with support for: daemon JSON-RPC endpoints, authentication tokens, template addresses, fee management, and dry run functionality. Generates dynamic command modules from template function definitions, supporting both function calls and method calls on components. Handles bucket management for resource transfers, workspace variables, and transaction submission. Includes login command for authentication token generation and grant permissions. Template variables allow customization of commands, arguments, and component interactions during scaffolding. |
| `daemon_client.rs.liquid` | Liquid template for generating Tari wallet daemon client functionality in scaffolded Rust CLI applications. Provides DaemonClient struct for interacting with wallet daemon JSON-RPC API including: authentication flow (login, grant permissions), instruction submission (single and batch), transaction waiting and result retrieval. Supports advanced features like fee management, bucket dumping, dry run execution, and substate requirements. Uses tari_wallet_daemon_client for typed API communication and handles transaction lifecycle from submission to result retrieval. Template enables generated CLI tools to seamlessly interact with Tari network through wallet daemon. |
| `main.rs.liquid` | Liquid template for generating main.rs entry point of Tari CLI applications. Sets up daemon client connection with authentication, parses template addresses, and routes commands to appropriate handlers. Supports both method and function-based commands. |

##### applications/tari_scaffolder/src/

| File | Description |
|------|-------------|
| `cli.rs` | Command-line interface for Tari Scaffolder application. Defines Cli struct with options for: base directory, output path, generator type, clean operation, custom data (key-value pairs), and generator config file. Includes parse_hashmap utility for processing key-value command line arguments. Supports configurable project generation with flexible data injection for template customization. |
| `command.rs` | Command execution module for Tari Scaffolder defining the scaffold command and its operations. Handles template loading, generator execution, and project creation workflow. Orchestrates the scaffolding process from template input to generated project output. |
| `main.rs` | Project scaffolding tool for generating Rust CLI applications and smart contract templates. Supports Liquid templating for code generation, WASM template compilation, and project structure creation. Development tool for bootstrapping new Tari Ootle projects and applications. |

###### applications/tari_scaffolder/src/generators/

| File | Description |
|------|-------------|
| `mod.rs` | Generator module definitions for Tari Scaffolder. Organizes code generation implementations including liquid templating system. Defines generator traits and types for extensible project scaffolding. Supports multiple output formats and templating engines. |

###### applications/tari_scaffolder/src/generators/liquid/

| File | Description |
|------|-------------|
| `mod.rs` | Liquid templating engine integration for Tari Scaffolder. Implements liquid-based code generation with custom filters and template processing. Enables flexible template-driven project scaffolding with dynamic content generation. |
| `snake_case.rs` | Snake case filter implementation for liquid templating in Tari Scaffolder. Provides string case conversion functionality for template generation, ensuring proper naming conventions in generated code. Similar to tari_generate snake_case filter but specific to scaffolder templates. |

#### applications/tari_signaling_server/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for tari_signaling_server application - a coordination service for Tari network communication. Provides signaling and coordination capabilities for peer discovery and network bootstrapping. Essential infrastructure component for network initialization and peer connectivity. |
| `log4rs_sample.yml` | Log4rs configuration template for Tari Signaling Server. Defines logging levels, appenders, and output formats for signaling server operations. Includes console and file logging with rotation policies for production deployment and debugging of network coordination services. |

##### applications/tari_signaling_server/src/

| File | Description |
|------|-------------|
| `cli.rs` | Command-line interface for Tari Signaling Server. Defines CLI options for listen address (defaults to 127.0.0.1:9100) and base directory configuration. Supports environment variable configuration via JRPC_ENDPOINT. Used for network coordination and peer discovery services. Essential infrastructure component for Tari network bootstrapping and communication. |
| `data.rs` | Data structures and models for Tari Signaling Server. Defines signaling message types, peer information, and coordination data structures used for network discovery and peer communication. Handles serialization/deserialization of signaling protocol messages. |
| `jrpc_server.rs` | JSON-RPC server implementation for Tari Signaling Server. Provides API endpoints for peer registration, discovery, and signaling operations. Handles JSON-RPC requests for network coordination services and maintains peer state information for network bootstrapping. |
| `main.rs` | WebRTC signaling server for Tari wallet peer-to-peer communication. Provides JSON-RPC server for coordinating WebRTC connections between wallet instances, handles session data with expiration, and enables direct wallet-to-wallet communication. Essential component for wallet connectivity and peer discovery in decentralized scenarios. |
| `permissions.rs` | Permission management system for Tari Signaling Server. Defines access control and authorization mechanisms for signaling operations. Handles permission validation for peer registration and network coordination requests. |

#### applications/tari_swarm_daemon/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the Tari swarm daemon development tool. Dependencies include Minotari and validator node gRPC clients, core Tari types, web server components (axum), and process management utilities. Orchestrates complete test networks with multiple components, providing web-based administration interface for development and testing scenarios. |
| `build.rs` | Build script for Tari Swarm Daemon that handles compilation-time web UI building. Similar to indexer build script, conditionally compiles React/TypeScript web interface for swarm management. Includes build triggers, feature checks, and npm/pnpm integration for UI compilation. |

##### applications/tari_swarm_daemon/docker/

| File | Description |
|------|-------------|
| `README.md` | Docker deployment documentation for Tari Swarm Daemon. Explains containerization setup, cross-compilation procedures, and Docker-based development environment deployment. Includes instructions for building multi-architecture images and container orchestration for blockchain development networks. |
| `cross-compile-aarch64.sh` | Cross-compilation script for building ARM64 (aarch64) binaries of Tari Swarm Daemon. Handles toolchain setup, cross-compilation environment configuration, and binary generation for ARM64 targets. Used for multi-architecture Docker image building and deployment. |
| `tari_swarm.Dockerfile` | Dockerfile for containerizing Tari Swarm Daemon. Defines multi-stage build process, runtime environment setup, and container configuration for deploying swarm daemon in containerized environments. Includes dependency management and optimization for production deployments. |

##### applications/tari_swarm_daemon/src/

| File | Description |
|------|-------------|
| `cli.rs` | Command-line interface for Tari Swarm Daemon - the development orchestration tool for managing local blockchain networks. Defines CLI commands for network management: start/stop instances, status checking, configuration, template operations, account management, mining control, and transaction handling. Supports multiple instance types (Minotari node, Validator node, Indexer, Wallet daemon) with flexible network configuration. Essential for local development and testing of Tari blockchain applications. |
| `config.rs` | Configuration management for Tari Swarm Daemon orchestration system. Defines Config struct with network settings, port allocation, process configurations, web server setup, and blockchain network parameters. Key features: supports multiple networks (localnet, testnet), automatic port allocation starting from start_port, settings injection for process customization, process enable/disable flags, public IP configuration for network binding. Handles config file loading, validation, and merging with CLI parameters for flexible development environment setup. |
| `logger.rs` | Logging configuration and management for Tari Swarm Daemon. Sets up structured logging for process management, web server, and orchestration operations. Handles log level configuration, output formatting, and log file management for development environment monitoring and debugging. |
| `main.rs` | Swarm orchestration daemon for managing development and testing environments. Provides process management for validator nodes, indexers, wallets, and supporting services. Includes web UI for monitoring and controlling distributed test networks and development clusters. |

###### applications/tari_swarm_daemon/src/layer_one_transactions/

| File | Description |
|------|-------------|
| `mod.rs` | Layer-one transaction integration module for Tari Swarm Daemon. Provides service and submitter components for handling Minotari (Layer-1) blockchain transactions within development environments. Enables burn-claim operations and layer-one integration testing in local development networks. |
| `service.rs` | Layer-one transaction service implementation for Tari Swarm Daemon. Provides LayerOneTransactionService for managing Minotari blockchain interactions in development environments. Handles transaction submission, monitoring, and integration between Layer-1 and Layer-2 networks for local testing scenarios. |
| `submitter.rs` | Layer-one transaction submitter implementation for Tari Swarm Daemon. Handles actual submission of transactions to Minotari blockchain network from development environment. Manages transaction formatting, submission, and confirmation monitoring for layer-one integration testing. |

###### applications/tari_swarm_daemon/src/process_definitions/

| File | Description |
|------|-------------|
| `context.rs` | Process definition context management for Tari Swarm Daemon. Provides shared context and configuration data for process definitions including network settings, port allocations, and inter-process dependencies. Used by all process definitions for consistent configuration and coordination. |
| `definition.rs` | Base process definition trait and implementation for Tari Swarm Daemon. Defines ProcessDefinition trait providing common interface for all managed process types. Includes configuration generation, startup parameters, and lifecycle management methods shared across validator nodes, indexers, wallets, and other components. |
| `indexer.rs` | Indexer process definition for Tari Swarm Daemon. Configures and manages indexer instances within development networks including database setup, API configuration, web UI settings, and network connectivity. Handles indexer lifecycle and integration with validator nodes for event scanning. |
| `minotari_miner.rs` | Minotari miner process definition for Tari Swarm Daemon. Configures and manages mining nodes for Layer-1 blockchain in development environments. Handles mining configuration, difficulty adjustment, block production, and integration with Minotari nodes for local blockchain testing. |
| `minotari_node.rs` | Minotari base layer node process definition for Tari Swarm Daemon. Configures and manages Minotari (Layer-1) blockchain nodes in development environments. Handles base layer configuration, peer discovery, blockchain synchronization, and integration with Layer-2 components for local testing scenarios. |
| `minotari_wallet.rs` | Minotari wallet process definition for Tari Swarm Daemon. Configures and manages Layer-1 wallet instances in development environments. Handles wallet configuration, key management, transaction capabilities, and integration with Minotari nodes for base layer operations and testing. |
| `mod.rs` | Process definition module for Tari Swarm Daemon defining all managed process types. Organizes definitions for: indexer, validator nodes, wallet daemons, Minotari nodes/miners, signaling server, and wallet daemon key creation. Provides abstractions for process lifecycle management, configuration, and inter-process coordination in development environments. |
| `signaling_server.rs` | Signaling server process definition for Tari Swarm Daemon. Configures and manages signaling server instances for network coordination in development environments. Handles peer discovery configuration, signaling protocol setup, and integration with other network components for local testing scenarios. |
| `validator_node.rs` | Validator node process definition for Tari Swarm Daemon. Defines configuration, startup parameters, networking setup, and lifecycle management for validator node instances in development networks. Handles consensus configuration, peer discovery, and integration with other swarm components. |
| `wallet_daemon.rs` | Wallet daemon process definition for Tari Swarm Daemon. Manages wallet daemon instances in development networks with configuration for accounts, authentication, networking, and integration with validator nodes. Handles wallet lifecycle, JSON-RPC API setup, and test account creation for development workflows. |
| `wallet_daemon_create_key.rs` | Wallet daemon key creation process definition for Tari Swarm Daemon. Handles automated creation of wallet keys and accounts for development environments. Configures key generation, account setup, and initial wallet state for testing workflows. Essential for automated wallet initialization in local networks. |

###### applications/tari_swarm_daemon/src/process_manager/

| File | Description |
|------|-------------|
| `handle.rs` | ProcessManagerHandle implementation providing communication interface to ProcessManager from external components. Defines message-passing protocol for process lifecycle operations: start/stop instances, status queries, configuration updates. Used by web server and CLI to control development network processes. |
| `manager.rs` | Core ProcessManager implementation for Tari Swarm Daemon. Manages lifecycle of all blockchain node types: validator nodes, indexers, wallet daemons, Minotari nodes/miners, and signaling servers. Key features: executable discovery and management, instance lifecycle control, template management and automatic registration, layer-one transaction integration, health monitoring, port allocation, peer connectivity management. Handles complex orchestration workflows including template deployment and network initialization for development environments. |
| `mod.rs` | Process manager module for Tari Swarm Daemon orchestrating all managed processes. Organizes process management components: executables (executable discovery), instances (process instance management), handles (communication interfaces), port allocation, and process definitions. Exports spawn() function to initialize ProcessManager with shutdown handling. Central coordination system for development environment management. |
| `port_allocator.rs` | Port allocation management for Tari Swarm Daemon ensuring non-conflicting network ports across all managed processes. Implements PortAllocator for systematic port assignment starting from configured base port. Prevents port conflicts between validator nodes, indexers, wallet daemons, and other services in development networks. |

###### applications/tari_swarm_daemon/src/process_manager/executables/

| File | Description |
|------|-------------|
| `executable.rs` | Executable definition and management for Tari Swarm Daemon. Defines Executable struct representing individual blockchain binaries with metadata, version information, and execution parameters. Handles executable discovery, validation, and launch configuration for different node types. |
| `manager.rs` | Executable collection manager for Tari Swarm Daemon. Implements ExecutableManager for discovering, tracking, and managing blockchain binaries across the system. Handles automatic binary discovery, version management, and provides executable instances for process launching. |
| `mod.rs` | Executable management module for Tari Swarm Daemon. Organizes executable discovery and management components: individual executable definitions and executable manager for collection operations. Handles binary discovery, version management, and executable validation for blockchain components. |

###### applications/tari_swarm_daemon/src/process_manager/instances/

| File | Description |
|------|-------------|
| `instance.rs` | Individual process instance implementation for Tari Swarm Daemon. Defines ProcessInstance struct managing single running process with state tracking, configuration, health monitoring, and lifecycle control. Handles process startup, shutdown, status monitoring, and resource management for individual blockchain components. |
| `manager.rs` | Instance collection manager for Tari Swarm Daemon. Implements InstanceManager for coordinating multiple process instances, tracking instance states, managing instance lifecycle operations, and providing collection-level operations. Handles batch operations and inter-instance coordination for development network management. |
| `mod.rs` | Process instance management module for Tari Swarm Daemon. Organizes instance lifecycle components: individual instance definitions, instance manager for collection management, and instance tracking. Provides abstractions for managing running process instances with state tracking and lifecycle control. |

###### applications/tari_swarm_daemon/src/process_manager/processes/

| File | Description |
|------|-------------|
| `indexer.rs` | Indexer process implementation for Tari Swarm Daemon. Manages indexer process lifecycle, database initialization, API configuration, and integration with validator nodes. Handles indexer-specific operations including event scanning coordination and web UI management. |
| `minotari_miner.rs` | Minotari miner process implementation for Tari Swarm Daemon. Manages mining process lifecycle, difficulty configuration, and integration with Minotari nodes. Handles mining-specific operations including block production coordination and mining pool management for development testing. |
| `minotari_node.rs` | Minotari node process implementation for Tari Swarm Daemon. Manages Layer-1 blockchain node lifecycle, synchronization, and integration with Layer-2 components. Handles base layer operations including block validation and network participation for development environments. |
| `minotari_wallet.rs` | Minotari wallet process implementation for Tari Swarm Daemon. Manages Layer-1 wallet process lifecycle, key management, transaction capabilities, and integration with Minotari nodes. Handles wallet-specific operations including base layer transactions, balance management, and coordination with Layer-2 wallet services for development testing. |
| `mod.rs` | Process type implementations module for Tari Swarm Daemon. Organizes specific process implementations for each node type: indexer, validator nodes, wallet daemons, Minotari nodes/miners, and signaling servers. Provides concrete process handling for different blockchain components in development environments. |
| `signaling_server.rs` | Signaling server process implementation for Tari Swarm Daemon. Manages signaling server lifecycle, peer coordination, and network discovery services. Handles signaling-specific operations including peer registration and network bootstrap coordination for development environments. |
| `validator_node.rs` | Validator node process implementation for Tari Swarm Daemon. Handles specific validator node process lifecycle, configuration management, health monitoring, and inter-node coordination. Provides validator-specific process operations including consensus participation and network integration. |
| `wallet_daemon.rs` | Wallet daemon process implementation for Tari Swarm Daemon. Manages wallet daemon lifecycle, account initialization, authentication setup, and network integration. Handles wallet-specific operations including key management and transaction processing coordination. |

###### applications/tari_swarm_daemon/src/webserver/

| File | Description |
|------|-------------|
| `context.rs` | Web server context management for Tari Swarm Daemon. Provides HandlerContext for HTTP request handling with access to configuration and ProcessManagerHandle. Enables web interface to control and monitor development network through structured context passing. |
| `error.rs` | Error handling for Tari Swarm Daemon web server. Defines error types for HTTP operations, process management failures, and web interface issues. Provides structured error responses and proper HTTP status code mapping for web API operations. |
| `handler.rs` | HTTP request handlers for Tari Swarm Daemon web server. Implements request routing and processing for web interface operations including process control, status monitoring, configuration management, and development network administration. Provides REST API endpoints for swarm management. |
| `mod.rs` | Web server module for Tari Swarm Daemon providing HTTP API and web interface. Organizes web components: context (request context), error handling, handlers (request processing), RPC endpoints, server implementation, and template management. Spawns web server with ProcessManagerHandle integration for managing development network through web interface. Enables browser-based control of local blockchain networks. |
| `server.rs` | HTTP server implementation for Tari Swarm Daemon web interface. Provides axum-based web server with JSON-RPC API handling, static file serving for web UI, CORS support, and request routing. Includes authentication, template rendering, and API proxy functionality. Serves embedded web UI assets and provides REST/JSON-RPC endpoints for development network administration and monitoring. |
| `templates.rs` | Template management for Tari Swarm Daemon web server. Handles smart contract template operations including registration, deployment, and management across development networks. Provides template lifecycle management and integration with validator nodes for template distribution and validation. |

###### applications/tari_swarm_daemon/src/webserver/rpc/

| File | Description |
|------|-------------|
| `indexers.rs` | JSON-RPC endpoints for indexer management in Tari Swarm Daemon web server. Provides API methods for indexer process control, status monitoring, configuration, and integration with development networks. Handles indexer-specific operations including database management and event scanning coordination. |
| `instances.rs` | JSON-RPC endpoints for general instance management in Tari Swarm Daemon web server. Provides API methods for process lifecycle control, status queries, configuration updates, and cross-instance operations. Handles generic instance management operations applicable to all node types in development networks. |
| `logs.rs` | JSON-RPC endpoints for log management in Tari Swarm Daemon web server. Provides API methods for retrieving, filtering, and monitoring logs from all managed processes. Enables centralized log access and debugging capabilities for development network administration. |
| `miners.rs` | JSON-RPC endpoints for miner management in Tari Swarm Daemon web server. Provides API methods for miner process control, mining configuration, difficulty adjustment, and integration with Minotari nodes. Handles mining-specific operations including block production coordination and mining statistics. |
| `minotari_nodes.rs` | JSON-RPC endpoints for Minotari node management in Tari Swarm Daemon web server. Provides API methods for Layer-1 node control, blockchain synchronization, peer management, and integration with Layer-2 components. Handles base layer operations including block validation and network participation. |
| `minotari_wallets.rs` | JSON-RPC endpoints for Minotari wallet management in Tari Swarm Daemon web server. Provides API methods for Layer-1 wallet control, account management, transaction processing, and integration with Minotari nodes. Handles base layer wallet operations including balance queries and transaction signing. |
| `mod.rs` | JSON-RPC API module for Tari Swarm Daemon web server. Organizes RPC endpoints for different node types: indexers, instances (general management), logs, miners, Minotari nodes/wallets, Tari wallets, and validator nodes. Provides structured API access for development network management through HTTP-based JSON-RPC interface. |
| `tari_wallets.rs` | JSON-RPC endpoints for Tari wallet daemon management in Tari Swarm Daemon web server. Provides API methods for Layer-2 wallet control, account management, transaction processing, and smart contract interactions. Handles Ootle wallet operations including burn-claim functionality and template interactions. |
| `validator_nodes.rs` | JSON-RPC endpoints for validator node management in Tari Swarm Daemon web server. Provides API methods for validator control, consensus monitoring, committee management, and network coordination. Handles validator-specific operations including registration, shard management, and consensus participation. |

##### applications/tari_swarm_daemon/webui/

| File | Description |
|------|-------------|
| `.eslintrc.cjs` | ESLint configuration for the Tari Swarm Daemon web UI. Configures browser environment with ES2020 support, extends recommended configurations for ESLint, TypeScript, and React hooks. Uses TypeScript parser with latest ECMAScript version and module source type. Includes react-refresh plugin for development hot reloading with component export warnings. |
| `.gitignore` | Git ignore file for the Tari Swarm Daemon web UI. Excludes development artifacts including: logs (npm, yarn, pnpm, lerna), node_modules directory, build outputs (dist, dist-ssr), local environment files, editor configurations (.vscode, .idea), system files (.DS_Store), and .env environment variables. Preserves .gitkeep files in dist directory. |
| `index.html` | HTML entry point for the Tari Swarm Daemon web UI. Minimal HTML5 document with UTF-8 charset, responsive viewport meta tag, root div element for React mounting, and module script loading the main TypeScript entry point. Standard single-page application structure for Vite-based React development. |
| `package.json` | Package.json for Tari Swarm Daemon web UI. React application using TypeScript, React Router v7, and async-mutex for concurrency control. Includes Vite build system and ESLint for development tooling. |
| `tsconfig.json` | TypeScript configuration for the Tari Swarm Daemon web UI. Configures ES2020 target with modern bundler mode, React JSX support, strict type checking, and linting rules. Includes DOM libraries, allows TypeScript extensions importing, JSON module resolution, and isolated modules for Vite bundling. References tsconfig.node.json for build tools configuration. Enables strict unused variable/parameter checking and switch statement fall-through protection. |
| `tsconfig.node.json` | TypeScript configuration for Node.js build tools in the Tari Swarm Daemon web UI. Configures composite project with bundler module resolution, ESNext modules, and synthetic default imports. Includes vite.config.ts file for build tooling configuration. Used by the main tsconfig.json for build process type checking. |
| `vite.config.ts` | Vite build configuration for the Tari Swarm Daemon web UI. Configures React plugin for JSX support and preserves .gitkeep files by disabling emptyOutDir during build process. Simple Vite configuration for React development with build output preservation. Uses @vitejs/plugin-react for React integration and hot module replacement. |
| `vite.config.ts.timestamp-1712311434695-a58d35a3bc145.mjs` | [GENERATED] Vite build timestamp cache file generated during the swarm daemon web UI build process. Contains pre-compiled configuration with timestamp for build optimization. Generated by Vite bundler, not directly modified. |

###### applications/tari_swarm_daemon/webui/src/

| File | Description |
|------|-------------|
| `App.css` | CSS styles for the Tari Swarm Daemon web UI application. Defines layout styles including: root container with left-aligned text and padding, main flexbox layout, info card styling with borders and border-radius, templates centered layout, log level color coding (TRACE: green, DEBUG: orange, INFO: blue, WARN: orange-red, ERROR: dark red), file path styling with user-select capability, and infos flex row layout. Provides visual hierarchy for application components and log display. |
| `App.tsx` | Main React application component for Tari Swarm Daemon web UI. Sets up routing with React Router for main dashboard and log viewing functionality. Includes 404 error handling for unknown routes. |
| `Types.ts` | TypeScript type definitions for Tari Swarm Daemon web UI. Exports ExecutedTransaction interface with transaction, abort_details, final_decision fields, and basic Transaction interface with id field. Used by: swarm daemon UI components for type safety when displaying transaction execution results and status information. Simple type definitions for transaction management interface. |
| `index.css` | Global CSS styles for the Tari Swarm Daemon web UI. Defines root variables with Inter font family, dark theme color scheme (dark background #242424, light text), optimized font rendering settings, and responsive design patterns. Includes button styling with rounded corners, hover effects, and focus states. Features light/dark mode media query support with automatic color scheme adaptation. Standard modern web application styling foundation. |
| `main.tsx` | React application entry point for the Tari Swarm Daemon web UI. Sets up React Router with browser routing, renders App component in React StrictMode, and loads global theme CSS. Creates router configuration with catch-all path routing to App component. Standard Vite + React + TypeScript application bootstrap with router provider for single-page application navigation. |
| `vite-env.d.ts` | TypeScript declaration file for Vite environment types in the Tari Swarm Daemon web UI. Single reference directive to include Vite client types, enabling TypeScript support for Vite-specific features like import.meta.env and development tooling. |

###### applications/tari_swarm_daemon/webui/src/components/

| File | Description |
|------|-------------|
| `MinotariNodes.tsx` | React component for managing Minotari base layer nodes in the Tari Swarm Daemon web UI. Fetches and displays MinoTariNode instances via JSON-RPC calls, showing node information including name, GRPC port, and current blockchain height. Features: auto-refresh capability (1-second intervals), node control buttons (start/stop/delete data), loading states, and error handling. Includes NodeControls component for instance management. Provides real-time monitoring of base layer node status and height synchronization for development environments. |
| `MinotariWallet.tsx` | React component for managing Minotari wallets in the Tari Swarm Daemon web UI. Manages both MinoTariConsoleWallet and TariWalletDaemon instances, displaying wallet information and providing burn functionality for cross-layer transfers. Features: wallet instance control (start/stop/delete), burn funds interface with amount and account name inputs, claim URL generation for Layer-1 to Layer-2 transfers, and automatic mining (10 blocks) during burn operations. Integrates with NodeControls for standard instance management and provides bridge functionality between base layer and Layer-2. |
| `NodeControls.tsx` | Reusable React component providing standard node control buttons for the Tari Swarm Daemon web UI. Provides Start, Stop, and Delete Data buttons with conditional enabling based on node running state. Used across different node types (validator nodes, indexers, wallets, base layer nodes) for consistent instance management interface. Simple functional component with onClick handlers and disabled state management. |

###### applications/tari_swarm_daemon/webui/src/routes/

| File | Description |
|------|-------------|
| `Log.tsx` | React component for displaying log files in the Tari Swarm Daemon web UI. Fetches and displays log content via get_file JSON-RPC call using base64-encoded file names from URL parameters. Features: syntax highlighting for timestamps, log levels (INFO, ERROR, WARN, DEBUG), target modules, file paths, and different formats (normal vs stdout). Applies regex-based text processing to colorize log output with CSS classes. Handles loading states and empty file display. Renders formatted logs in preformatted text with dangerouslySetInnerHTML for HTML markup. |
| `Main.tsx` | Main dashboard component for the Tari Swarm Daemon web UI. Comprehensive development environment interface managing multiple Tari network components: validator nodes, indexers, wallets, base layer nodes, miners, and templates. Features: real-time monitoring with auto-refresh, transaction pool tracking across validator nodes, consensus status display, epoch manager statistics, node registration/exit management, mining controls (manual and periodic), template upload functionality, and cross-node transaction state comparison. Includes detailed validator node information display (shard groups, committee membership, public keys, peer IDs), missing transaction analysis between nodes, and unified instance management. Provides complete development network orchestration with log viewing, instance control, and network coordination capabilities. |

###### applications/tari_swarm_daemon/webui/src/theme/

| File | Description |
|------|-------------|
| `colors.ts` | Color palette definitions for the Tari Swarm Daemon web UI theme system. Exports comprehensive color scales including: grey (50-950 shades), tariPurple (Tari brand colors), teal, gothic, blue, orange, green, red color variants, and alpha transparency utilities (lightAlpha, darkAlpha). Each color family provides 10+ shade variations for consistent design system implementation. Used for Material-UI theme customization and component styling throughout the application. |
| `theme.css` | Theme CSS for the Tari Swarm Daemon web UI with Avenir font integration. Defines custom font faces (AvenirRegular, AvenirMedium, AvenirHeavy), global body styling, flexible container layouts, responsive design patterns, and specialized classes for dialogs and Material-UI components. Includes purple-flash animation keyframes for visual feedback and responsive breakpoints for mobile/desktop layouts. Provides consistent typography and layout foundation for the swarm daemon interface. |

###### applications/tari_swarm_daemon/webui/src/utils/

| File | Description |
|------|-------------|
| `json_rpc.tsx` | JSON-RPC client utility for the Tari Swarm Daemon web UI. Provides jsonRpc function for making JSON-RPC 2.0 calls to the swarm daemon backend. Features: automatic request ID generation, environment variable address configuration (VITE_JSON_RPC_ADDRESS, VITE_JRPC_ADDRESS), error handling with console logging, and promise-based async interface. Uses fetch API with JSON content type and standard JSON-RPC 2.0 protocol format. Central communication layer for all frontend-to-daemon interactions. |

#### applications/tari_validator_node/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the Tari validator node application. Features include metrics (Prometheus), tokio debugging, and web UI. Dependencies span consensus, networking, storage (SQLite/RocksDB), P2P communication, template management, and epoch handling. Central application for network validation, consensus participation, and transaction processing in the Ootle network. |
| `README.md` | Documentation for Tari Validator Node - the core consensus participant in the Ootle network. Explains web GUI features (pub keys, shard key, comms state, epoch management, committees, VN lists, registration), JSON-RPC API endpoints (transaction submission, template registration, identity/stats, validator registration), installation requirements, and deployment options. Key reference for validator node setup, operation, and administration with React-based web interface on port 5000 and JSON-RPC on port 18145. |
| `build.rs` | Build script for Tari Validator Node handling compilation-time web UI building. Similar to indexer and swarm daemon build scripts, conditionally compiles React/TypeScript web interface for validator node administration. Manages UI asset compilation, feature detection, and integration with validator node binary for embedded web interface serving. |
| `log4rs_sample.yml` | Log4rs configuration template for Tari Validator Node with consensus-specific logging. Defines logging levels, appenders, and output formats for validator operations including: consensus protocol messages, transaction processing, P2P communication, epoch management, and template operations. Includes segregated logging for consensus, networking, and application layers with rotation policies for production deployment. |
| `openrpc.json` | OpenRPC specification for Tari Validator Node JSON-RPC API. Defines API schema, method signatures, parameter types, and return values for all validator node endpoints including transaction submission, template registration, validator management, consensus operations, and network queries. Used for API documentation generation and client library development. |

##### applications/tari_validator_node/src/

| File | Description |
|------|-------------|
| `bootstrap.rs` | Bootstrap initialization for Tari validator node service. Handles comprehensive startup including identity management, base layer client setup, epoch oracle configuration, consensus initialization, networking setup, RPC services, and state management. Core bootstrap component orchestrating all validator node subsystems for reliable network participation and consensus operation. |
| `cli.rs` | Command-line interface for Tari Validator Node application. Defines CLI options for: JSON-RPC server binding, web UI configuration, epoch oracle settings, networking (peer seeds, listener ports, reachability), auto-registration flags, committee shard configuration, and tracing capabilities. Supports environment variable overrides and implements ConfigOverrideProvider for flexible validator node configuration across different network environments and deployment scenarios. |
| `config.rs` | Configuration management for Tari validator node application. Defines ApplicationConfig, ValidatorNodeConfig with network settings, API endpoints, database options, auto-registration settings, and epoch oracle configuration. Provides structured configuration loading with validation for validator node deployment across different network environments and operational scenarios. |
| `dry_run_transaction_processor.rs` | Dry-run transaction processor for validating transactions without committing state changes. Resolves local and foreign substates, executes transactions in temporary memory store, and returns execution results with fee calculations. Used for transaction preview and validation before actual submission. |
| `event_subscription.rs` | Event subscription wrapper for broadcast channels providing read-only access to event streams. Wraps broadcast::Sender to prevent buffer overflow while allowing multiple subscribers. Used throughout validator node for distributing internal events. |
| `file_l1_submitter.rs` | File-based Layer-1 transaction submitter writing validator registration/exit transactions to JSON files. Implements LayerOneTransactionSubmitter trait for development/testing environments where direct base layer submission isn't available. Generates unique filenames with random IDs. |
| `lib.rs` | Validator node library providing comprehensive consensus and validation functionality. Exports bootstrap, configuration, consensus implementation, transaction processing, event subscriptions, JSON-RPC services, HTTP UI, and runtime management. Core library implementing the complete validator node functionality for the Tari Ootle consensus network. |
| `main.rs` | Validator node application entry point providing distributed consensus and transaction validation services. Handles CLI parsing, configuration loading, keypair setup, panic handling, and validator node initialization. Integrates logging, shutdown management, and optional tokio debugging. Core service for network consensus, transaction processing, and blockchain state management with comprehensive error handling and graceful shutdown. |
| `metrics.rs` | Prometheus metrics registration helper trait for validator node. Provides CollectorRegister trait with register_at method for registering metrics with name, help text, and registry. Used to expose validator node performance and operational metrics. |
| `node.rs` | Main validator node orchestrator handling consensus events, epoch changes, and template management. Coordinates between HotStuff consensus, epoch manager, and networking services. Processes block commits to finalize transactions and update template manager with newly published smart contracts. |
| `state_bootstrap.rs` | Genesis state initialization for new validator nodes. Creates essential system substates including public identity resource, confidential XTR token, and testnet faucets (XTR and NFT). Bootstraps the initial blockchain state with predefined addresses and resources required for network operation. |
| `substate_resolver.rs` | Substate resolution service for transaction validation. Resolves local substates from state store and fetches remote substates from other validator nodes via SubstateScanner. Validates substate versions, handles DOWN states, and provides comprehensive error handling for missing or invalid substates. |
| `validator.rs` | Generic validator trait and combinators for composable validation logic. Provides Validator trait with chaining operations (and_then), context mapping, and boxed validators. Used throughout the system for transaction validation, consensus validation, and other validation workflows. |

###### applications/tari_validator_node/src/consensus/

| File | Description |
|------|-------------|
| `block_transaction_executor.rs` | Block transaction executor for HotStuff consensus in Tari Validator Node. Implements transaction execution logic within consensus blocks, handling WASM template execution, state transitions, fee processing, and result validation. Integrates with transaction processor for deterministic block execution and state commitment in the consensus protocol. |
| `handle.rs` | Consensus handle providing communication interface to HotStuff consensus engine from other validator node components. Defines message-passing protocol for consensus operations: transaction submission, block proposals, voting, and consensus state queries. Used by JSON-RPC handlers and other services to interact with consensus layer. |
| `leader_selection.rs` | Leader selection algorithm for HotStuff consensus in Tari Validator Node. Implements RoundRobinLeaderStrategy and other leader selection mechanisms for determining which validator proposes blocks in each consensus round. Essential for consensus protocol operation, ensuring fair leader rotation and Byzantine fault tolerance. |
| `metrics.rs` | Consensus metrics collection and reporting for Tari Validator Node. Implements Prometheus metrics for consensus operations including block proposals, voting, timing, network latency, and consensus health monitoring. Provides observability for consensus performance and debugging in production deployments. |
| `mod.rs` | Consensus module for Tari Validator Node implementing HotStuff BFT consensus protocol. Organizes consensus components: block transaction execution, leader selection, metrics, signer service, and consensus specifications. Exports spawn_consensus() function to initialize HotStuff consensus worker with transaction processing, epoch management, P2P messaging, and state synchronization. Central component for distributed consensus among validator nodes in the Ootle network. |
| `signer_service.rs` | Cryptographic signing service for HotStuff consensus in Tari Validator Node. Handles consensus message signing, signature verification, and cryptographic operations for consensus protocol. Manages validator private keys and provides secure signing for block proposals, votes, and other consensus messages. |
| `spec.rs` | Consensus specification implementation (TariConsensusSpec) for Tari Validator Node. Defines consensus protocol parameters, transaction types, validator behavior, and protocol compliance for HotStuff BFT consensus. Implements ConsensusSpec trait providing consensus algorithm customization and network-specific protocol behavior. |

###### applications/tari_validator_node/src/http_ui/

| File | Description |
|------|-------------|
| `mod.rs` | HTTP UI module declaration for the Tari Validator Node web interface. Simple module file that exports the server submodule containing HTTP server implementation for the validator node's web UI. Provides module structure for web-based administrative interface and monitoring dashboard. |
| `server.rs` | HTTP web UI server for Tari Validator Node administrative interface. Exports run_http_ui_server() function that serves static files from included web_ui/dist directory using axum framework. Features SPA routing with fallback to index.html, dynamic JSON-RPC endpoint configuration, MIME type detection, and graceful port binding with OS assignment fallback. Dependencies: axum, include_dir for static assets. Provides web-based validator node management and monitoring interface. |

###### applications/tari_validator_node/src/json_rpc/

| File | Description |
|------|-------------|
| `handlers.rs` | Comprehensive JSON-RPC API handlers for validator node operations. Implements 25+ RPC methods for transaction submission, state queries, block operations, template management, networking, epoch management, and Layer-1 registration/exit. Core API interface for external clients and wallets. |
| `jrpc_errors.rs` | JSON-RPC error handling utilities for Tari Validator Node. Exports error constructor functions: internal_error() for generic errors (logs details in debug mode), not_found() for 404 errors, general_error() for 500 errors, invalid_operation() for 400 errors. Uses axum_jrpc for JSON-RPC error types. Features conditional error message exposure based on debug builds/CI environment. Used by: JSON-RPC handlers for consistent error responses. |
| `metrics.rs` | Prometheus metrics HTTP handler for the Tari Validator Node JSON-RPC server. Implements MetricsHandler that serves Prometheus metrics via HTTP GET requests using axum framework. Features: OpenMetrics text format encoding, async registry access with Mutex protection, proper Content-Type headers (application/openmetrics-text), and method validation (GET only). Integrates with prometheus_client for metrics collection and encoding. Used for monitoring validator node performance and operational metrics. |
| `mod.rs` | JSON-RPC service module for Tari validator node providing API endpoints for external interactions. Exports handlers, error definitions, server implementation, and optional metrics. Enables standardized JSON-RPC 2.0 communication for transaction submission, state queries, and validator node administration through HTTP-based API interfaces. |
| `server.rs` | JSON-RPC server setup using Axum web framework for validator node API. Spawns HTTP server with CORS support, routes requests to handler methods, and includes optional Prometheus metrics endpoint. Handles method routing and error logging for all JSON-RPC calls. |

###### applications/tari_validator_node/src/p2p/

| File | Description |
|------|-------------|
| `logging.rs` | P2P message logging trait and implementations for the Tari Validator Node. Defines MessageLogger trait for logging inbound and outbound P2P messages with source/destination, message type, and tag information. Provides SqliteMessageLogger implementation for persistent message logging and NopLogger for no-op logging during testing. Supports generic serializable message types for comprehensive network communication auditing and debugging. Used throughout the P2P networking stack for message tracing and analysis. |
| `mod.rs` | P2P networking module for the Tari Validator Node. Exports create_tari_validator_node_rpc_service factory function for RPC service creation, MessageLogger traits and implementations for network message logging, and networking services module. Provides core P2P infrastructure including RPC services, message logging, and service management for validator node networking operations. |

###### applications/tari_validator_node/src/p2p/rpc/

| File | Description |
|------|-------------|
| `block_sync_task.rs` | Blockchain synchronization task for streaming blocks between Tari Validator nodes. Exports BlockSyncTask implementing block-by-block streaming with configurable options (QCs, substates, transactions). Features buffered streaming (BLOCK_BUFFER_SIZE=15), epoch filtering, substate update selection, and transaction receipt handling. Dependencies: StateStore, consensus models, RPC framework. Used by: P2P RPC service for sync_blocks operations. Handles both intra-epoch and inter-epoch block streaming with proper validation. |
| `mod.rs` | RPC service factory for the Tari Validator Node. Exports ValidatorNodeRpcServiceImpl and create_tari_validator_node_rpc_service factory function. Creates configured ValidatorNodeRpcServer with epoch manager, template manager, state store, and mempool dependencies. Provides the main entry point for setting up validator node RPC services including block sync, state sync, and template sync tasks. Central configuration point for all RPC-based validator node operations. |
| `service_impl.rs` | RPC service implementation for Tari Validator Node P2P communication. Exports ValidatorNodeRpcServiceImpl providing submit_transaction(), get_substate(), get_transaction_result(), sync_blocks(), get_checkpoint(), sync_state(), sync_templates() methods. Dependencies: EpochManagerHandle, TemplateManagerHandle, StateStore, MempoolHandle. Handles blockchain synchronization, transaction submission, state queries with committee/shard validation. Features streaming RPC for bulk sync operations and CBOR encoding for execution results. |
| `state_sync_task.rs` | State synchronization task for streaming state transitions between Tari Validator nodes. Exports StateSyncTask implementing batched state transition streaming with BATCH_SIZE=100. Features epoch-aware synchronization, incremental state transition fetching, and efficient buffer management. Dependencies: StateStore, consensus models, RPC framework. Used by: P2P RPC service for sync_state operations. Handles state tree synchronization between validator nodes with proper ordering and batching. |
| `template_sync_task.rs` | Template synchronization task for distributing smart contract templates between Tari Validator nodes. Exports TemplateSyncTask implementing batched template streaming with configurable batch_size. Features WASM binary and manifest template support, error handling for unavailable/missing templates, and efficient template manager integration. Dependencies: TemplateManagerHandle, TemplateQueryResult. Used by: P2P RPC service for sync_templates operations. Handles smart contract code distribution across validator network. |

###### applications/tari_validator_node/src/p2p/services/

| File | Description |
|------|-------------|
| `mod.rs` | P2P services module for the Tari Validator Node. Exports consensus_gossip, mempool, and messaging service modules. Provides the organizational structure for all P2P networking services including HotStuff consensus gossip, transaction mempool management, and consensus message routing. Central module for validator node networking services. |

###### applications/tari_validator_node/src/p2p/services/consensus_gossip/

| File | Description |
|------|-------------|
| `error.rs` | Error types for the consensus gossip service in the Tari Validator Node. Defines ConsensusGossipError enum covering invalid messages, epoch manager errors, request cancellation, and networking errors. Uses thiserror for error derivation and provides From implementations for error propagation from underlying services. Includes oneshot cancellation error handling for service request lifecycle management. |
| `handle.rs` | Consensus gossip handle for broadcasting HotStuff consensus messages in Tari Validator Node. Exports ConsensusGossipHandle with publish() method for sending HotstuffMessage to specific shard groups via P2P gossip. Dependencies: NetworkingHandle, ProstCodec for message encoding. Used by: consensus engine for distributing proposals, votes, and certificates. Converts internal consensus messages to protobuf format and publishes to topic-based gossip network. |
| `initializer.rs` | Consensus gossip service initializer for the Tari Validator Node. Spawns ConsensusGossipService with epoch manager and consensus event streams, networking handle, and gossip message receiver. Returns ConsensusGossipHandle for external communication, JoinHandle for task management, and channel receiver for consensus messages. Bridges HotStuff consensus events with libp2p gossipsub networking for distributed consensus message propagation across validator nodes. |
| `mod.rs` | Consensus gossip module for the Tari Validator Node P2P services. Exports ConsensusGossipError, ConsensusGossipHandle for service interaction, spawn initializer function, and TOPIC_PREFIX constant for gossip topic configuration. Provides module structure for HotStuff consensus message propagation over libp2p gossipsub networking. Includes error handling, service lifecycle management, and protocol definitions. |
| `service.rs` | Consensus gossip service for propagating HotStuff consensus messages across validator network. Manages gossip subscriptions based on shard groups, handles epoch changes, and forwards consensus messages between validators. Critical for consensus protocol communication. |

###### applications/tari_validator_node/src/p2p/services/mempool/

| File | Description |
|------|-------------|
| `error.rs` | Mempool error types for Tari Validator Node transaction pool management. Exports MempoolError enum covering invalid messages, epoch manager failures, request cancellation, consensus channel closure, dry run processor errors, storage errors, transaction validation errors, and networking errors. Implements is_transaction_validator_error() helper and channel error conversions. Used by: mempool service for comprehensive error handling across transaction validation, networking, and storage operations. |
| `gossip.rs` | Mempool gossip service for transaction propagation in Tari Validator Node P2P network. Exports MempoolGossip with subscribe/unsubscribe topic management, forward_to_local_replicas(), forward_to_foreign_replicas() for transaction routing based on shard groups. Dependencies: EpochManagerHandle, NetworkingHandle, libp2p gossipsub. Features codec for TariMessage encoding/decoding, smart routing to relevant committees based on transaction inputs, and global vs local transaction handling. |
| `handle.rs` | Asynchronous handle for mempool service providing remote access to mempool operations. Supports transaction submission, batch removal, and size queries through mpsc channels. Used by JSON-RPC handlers and other components to interact with mempool service. |
| `initializer.rs` | Mempool service initializer for the Tari Validator Node. Spawns MempoolService with epoch manager, transaction validator, state store, consensus handle, networking, and gossip receiver. Conditionally includes Prometheus metrics when metrics feature is enabled. Returns MempoolHandle for external requests and JoinHandle for task management. Creates single-buffered channel for mempool requests to ensure proper request-response ordering. Central factory function for mempool service lifecycle management. |
| `metrics.rs` | Prometheus metrics for the mempool service in the Tari Validator Node. Defines PrometheusMempoolMetrics with counters for transactions received and validation errors. Registers metrics under 'mempool' prefix in Prometheus registry with descriptive help text. Provides callback methods for tracking transaction receipt and validation failures. Used for monitoring mempool performance and transaction processing statistics. |
| `mod.rs` | Mempool module for the Tari Validator Node P2P services. Exports MempoolHandle and MempoolRequest for external communication, spawn initializer function, error types, gossip functionality with TOPIC_PREFIX, conditionally available metrics, and service traits. Provides complete mempool infrastructure including transaction validation, gossip propagation, metrics collection, and service lifecycle management. |
| `service.rs` | Core mempool service managing transaction pool for validator nodes. Handles transaction submission, validation, gossip propagation, and removal. Integrates with consensus for transaction finalization and maintains transaction state with comprehensive validation pipeline. |
| `traits.rs` | Mempool substate resolution traits for Tari Validator Node transaction processing. Exports SubstateResolver trait with try_resolve_local() and try_resolve_foreign() methods for fetching transaction dependencies. Defines ResolvedSubstates struct containing local substates and unresolved foreign requirements. Used by: mempool for resolving transaction inputs from local storage and remote committees. Essential for distributed transaction validation and committee-based state resolution. |

###### applications/tari_validator_node/src/p2p/services/messaging/

| File | Description |
|------|-------------|
| `inbound.rs` | Inbound consensus messaging implementation for the Tari Validator Node. ConsensusInboundMessaging receives HotStuff consensus messages from direct messaging, gossip, and loopback channels. Implements InboundMessaging trait with biased message priority (loopback first, then direct/gossip). Features message logging, protocol buffer conversion, error handling for invalid messages, and proper message routing. Core component for consensus message reception in the HotStuff Byzantine Fault Tolerant consensus protocol. |
| `mod.rs` | Messaging module for consensus P2P services in the Tari Validator Node. Exports inbound and outbound messaging components for HotStuff consensus communication. Provides ConsensusInboundMessaging and ConsensusOutboundMessaging implementations that handle message routing, protocol conversion, and logging for the Byzantine Fault Tolerant consensus protocol. |
| `outbound.rs` | Outbound consensus messaging implementation for the Tari Validator Node. ConsensusOutboundMessaging handles sending HotStuff consensus messages via direct messaging, multicast, and gossip broadcast. Implements OutboundMessaging trait with loopback handling, message logging, protocol buffer conversion, and error handling. Supports send_self for local messages, point-to-point send, multicast to multiple peers, and shard group broadcasts via gossip. Core component for consensus message transmission in the HotStuff Byzantine Fault Tolerant consensus protocol. |

###### applications/tari_validator_node/src/transaction_validators/

| File | Description |
|------|-------------|
| `dry_run.rs` | Transaction validator for rejecting dry run transactions in Tari Validator Node mempool. Exports TransactionDryRunValidator implementing Validator trait that checks Transaction.is_dry_run() and returns DryRunNotAllowed error. Used by: mempool validation pipeline to prevent dry run transactions from being included in consensus. Includes unit tests for both dry run and normal transaction validation scenarios. |
| `epoch_range.rs` | Transaction validator for checking epoch range constraints in Tari Validator Node mempool. Exports EpochRangeValidator implementing Validator trait that validates transactions have min_epoch <= current_epoch <= max_epoch. Returns CurrentEpochLessThanMinimum or CurrentEpochGreaterThanMaximum errors when constraints violated. Used by: mempool validation pipeline to ensure transactions are submitted within valid epoch windows. Essential for preventing stale or premature transaction processing. |
| `error.rs` | Comprehensive error types for transaction validation failures. Defines TransactionValidationError enum covering storage errors, signature failures, fee validation, template errors, epoch validation, network mismatches, and other validation scenarios. Used throughout validation pipeline. |
| `fee.rs` | Fee transaction validator ensuring all transactions include required fee instructions. Validates that transactions have non-empty fee instructions to prevent fee-less transactions from being processed. Essential for network economics and spam prevention. |
| `has_inputs.rs` | Transaction validator for ensuring transactions have required inputs in Tari Validator Node mempool. Exports HasInputs validator implementing Validator trait that checks transactions have at least one input via all_inputs_iter(). Returns NoInputs error for transactions without inputs. Used by: mempool validation pipeline to prevent empty transactions from being processed. Makes exception for CreateFreeTestCoins transactions that legitimately have no inputs. |
| `mod.rs` | Transaction validation module aggregator. Exports all transaction validators including signature validation, fee validation, template existence, epoch range, network validation, dry run, and error handling. Provides comprehensive validation pipeline for incoming transactions. |
| `network.rs` | Transaction validator for verifying network compatibility in Tari Validator Node mempool. Exports TransactionNetworkValidator implementing Validator trait that checks transaction network byte matches validator's configured network (LocalNet, MainNet, etc.). Returns NetworkMismatch or UnknownNetwork errors for invalid networks. Used by: mempool validation pipeline to prevent cross-network transaction acceptance. Essential for network isolation and transaction routing. Includes comprehensive unit tests. |
| `signature.rs` | Transaction signature validator ensuring all transaction signatures are cryptographically valid. Implements Validator trait to verify transaction seal signatures using verify_all_signatures(). Critical security component preventing invalid transactions from entering mempool. |
| `template_exists.rs` | Template existence validator ensuring referenced smart contract templates are available and valid. Checks template manager for template availability and validates template status (not invalid). Prevents execution of transactions referencing missing or corrupted templates. |
| `with_context.rs` | Generic transaction validator wrapper providing context support for Tari Validator Node. Exports WithContext struct implementing Validator trait with generic context, type, and error parameters. Always returns Ok(()) as a no-op validator. Used by: validation pipeline as a base validator or wrapper for adding context to other validators. Provides type-safe context passing in validator composition patterns. |

##### applications/tari_validator_node/web_ui/

| File | Description |
|------|-------------|
| `.gitignore` | Git ignore rules for the Tari validator node web UI. Excludes Node.js dependencies, build artifacts, development files, and environment configurations from version control. |
| `.nvmrc` | Node.js version specification for the validator web UI project. Ensures consistent Node.js version across development environments when using nvm (Node Version Manager). |
| `README.md` | Standard Create React App documentation for the Tari validator node web UI. Provides basic npm/yarn commands for development (start, test, build, eject) and links to React documentation. Generated template README. |
| `index.html` | Main HTML entry point for the Tari validator node web UI. Serves as the root template for the React application, containing the basic HTML structure and root div where React components mount. |
| `package.json` | Package configuration for Tari validator node React web UI. Uses Vite for development, TypeScript compilation, Material-UI components, React Router, TanStack Query for data fetching, and charts. Includes Tari TypeScript bindings for type-safe API communication. Modern React application providing comprehensive validator node administration interface. |
| `tsconfig.json` | TypeScript configuration for the validator web UI project. Configures ESNext target, React JSX, strict type checking, and module resolution for the React application. References tsconfig.node.json for build tooling. |
| `tsconfig.node.json` | TypeScript configuration for Node.js tooling and build scripts in the validator web UI project. Separate config for build tools like Vite with Node.js-specific settings and module resolution. |
| `vite.config.ts` | Vite build configuration for the Tari validator node web UI. Configures React SWC plugin for fast compilation and development server settings. Uses Vite for modern frontend tooling with React support. |

###### applications/tari_validator_node/web_ui/public/

| File | Description |
|------|-------------|
| `.gitkeep` | Git keep file to preserve the public directory structure in version control when the directory might otherwise be empty. Ensures public folder exists for static assets. |
| `index.html` | Public HTML template for the validator node web UI. Alternative or backup HTML file in the public directory, typically used during build process for static assets and SEO meta tags. |
| `manifest.json` | Progressive Web App manifest for the validator node web UI. Defines app metadata including name, icons, theme colors, and display properties for when the app is installed on mobile devices or desktops. |
| `robots.txt` | Search engine crawler instructions for the validator node web UI. Defines which parts of the site search engines can access and index, typically allowing all content for this admin interface. |

###### applications/tari_validator_node/web_ui/src/

| File | Description |
|------|-------------|
| `App.tsx` | Main React application component for Tari validator node web UI. Implements routing for mempool, committees, connections, fees, blocks, templates, and transaction views. Provides context management for epoch stats, identity, and shard key data. Central application orchestrating the complete validator node administration interface with real-time data updates. |
| `main.tsx` | Main application entry point for the Tari validator node web UI. Sets up React Router with routes for blocks, transactions, committees, templates, validator nodes, connections, fees, and mempool. Dependencies: React Router DOM, Material-UI theme. Renders application with router configuration and error boundaries. |
| `vite-env.d.ts` | Vite environment type definitions for TypeScript support. Provides type declarations for Vite-specific imports, environment variables, and module resolution in the development environment. |

###### applications/tari_validator_node/web_ui/src/Components/

| File | Description |
|------|-------------|
| `Accordion.tsx` | Reusable accordion/collapsible UI component for the validator web interface. Provides expandable sections for organizing complex data display, likely used for transaction details, block information, or configuration sections. |
| `AlertDialog.tsx` | Alert dialog component for user notifications and confirmations in the validator web UI. Uses Material-UI dialog system to display warnings, errors, or confirmation messages with customizable actions and styling. |
| `Breadcrumbs.tsx` | Navigation breadcrumb component showing the current page hierarchy. Uses react-router-dom breadcrumbs to display navigational context and allow users to navigate back to parent pages in the validator UI. |
| `CodeBlock.tsx` | Code display component for formatting and presenting code snippets, JSON data, or configuration text. Provides syntax highlighting and copy functionality for technical data displayed in the validator interface. |
| `CopyToClipboard.tsx` | Utility component for copying text content to the user's clipboard. Provides copy button with visual feedback, commonly used for copying transaction hashes, addresses, and other blockchain identifiers. |
| `Copyright.tsx` | Footer copyright component displaying Tari Project legal information and licensing details. Shows copyright notice and version information for the validator web interface. |
| `Filter.tsx` | Generic filtering component for data tables and lists. Provides input controls and filter logic for searching and filtering blockchain data like transactions, blocks, and validator information. |
| `HeadingMenu.tsx` | Navigation menu component for page headers in the validator interface. Provides dropdown or contextual menu options for actions related to the current page or section. |
| `JsonTooltip.tsx` | Tooltip component for displaying formatted JSON data on hover. Used to show detailed blockchain data structures like transaction details or block contents without cluttering the main interface. |
| `Loading.tsx` | Loading spinner component using Material-UI CircularProgress. Displays a centered loading indicator while data is being fetched from the validator node or during page transitions. Exports: Loading component. |
| `MenuItems.tsx` | Menu items definition and configuration for the validator web UI navigation. Contains menu structure, routing links, and permissions for different sections like blocks, transactions, committees, and validator management. |
| `PageHeading.tsx` | Page title heading component with consistent styling across the validator web interface. Provides standardized typography and spacing for main page titles using Material-UI theme. |
| `SearchFilter.tsx` | Search input component with filtering capabilities for blockchain data. Provides text search functionality with debouncing and filter controls for tables and lists in the validator interface. |
| `SecondaryHeading.tsx` | Secondary heading component for section titles and subsection headers. Provides consistent styling for hierarchical content organization below main page headings. |
| `StatusChip.tsx` | Status indicator chip component for displaying entity states like transaction status, validator status, or connection states. Uses Material-UI Chip with color coding for different statuses. |
| `StyledComponents.ts` | Collection of styled Material-UI components with custom theming for the validator web interface. Exports: AccordionIconButton, StyledPaper, DataTableCell, CodeBlock, BoxHeading variants, SubHeading. Dependencies: Material-UI styled components, custom theme. Provides consistent design system across the application. |
| `Title.tsx` | Main title component for the application header and branding. Displays the Tari validator node title with consistent styling and potentially includes logo or navigation elements. |

###### applications/tari_validator_node/web_ui/src/assets/

| File | Description |
|------|-------------|
| `Logo.tsx` | Tari logo SVG component for branding in the validator web interface. Exports React component version of the Tari project logo with customizable sizing and theming properties. |

###### applications/tari_validator_node/web_ui/src/routes/

| File | Description |
|------|-------------|
| `ErrorPage.tsx` | Error boundary and 404 page component for handling routing errors and application exceptions. Displays user-friendly error messages and navigation options when pages are not found or errors occur. |

###### applications/tari_validator_node/web_ui/src/routes/Blocks/

| File | Description |
|------|-------------|
| `BlockDetails.tsx` | Block details page component displaying comprehensive information about a specific blockchain block. Shows block header, transactions, validator information, and technical details using parameterized routing (/blocks/:blockId). |
| `Blocks.tsx` | Blocks page layout component providing the main blocks listing interface. Uses PageHeading and StyledPaper components to wrap the blocks data grid, showing recent blocks with pagination and filtering capabilities. |
| `Transactions.tsx` | Transactions within blocks display component. Shows the list of transactions contained within a specific block, with transaction details, status, and links to individual transaction pages. |

###### applications/tari_validator_node/web_ui/src/routes/Committees/

| File | Description |
|------|-------------|
| `Committee.tsx` | Individual committee display component showing committee details, members, and status. Displays committee composition, active/inactive members, and committee-specific metrics and governance information. |
| `CommitteeMembers.tsx` | Committee members page component displaying detailed member information for a specific committee. Shows member addresses, status, voting power, and participation statistics using route parameter (/committees/:address). |
| `CommitteeSingle.tsx` | Single committee details page showing comprehensive information about one committee instance. Displays committee metadata, member details, decision history, and current voting status. |
| `Committees.css` | Stylesheet for committee-related components providing custom CSS styles for committee visualizations, charts, tables, and layout specific to the committees section of the validator interface. |
| `Committees.tsx` | Main committees listing page component showing overview of all committees in the Tari network. Displays committee status, members count, and provides navigation to individual committee details. |
| `CommitteesLayout.tsx` | Layout wrapper component for all committee-related pages. Provides consistent page structure, navigation, and styling for the committees section with shared header and navigation elements. |
| `CommitteesPieChart.tsx` | Pie chart visualization component for committee data using ECharts. Shows distribution of committee members, voting power allocation, or committee status breakdown with interactive chart features. |
| `CommitteesRadial.tsx` | Radial chart visualization component for committee metrics using ECharts. Displays committee relationships, hierarchical structure, or circular data patterns for network governance visualization. |
| `CommitteesWaterfall.tsx` | Waterfall chart component for visualizing committee progression or sequential committee data using ECharts. Shows cumulative changes in committee composition or voting patterns over time. |

###### applications/tari_validator_node/web_ui/src/routes/Connections/

| File | Description |
|------|-------------|
| `Connections.tsx` | Network connections page displaying validator node peer connections, communication statistics, and network topology. Shows active connections, peer information, and network health metrics. |

###### applications/tari_validator_node/web_ui/src/routes/Fees/

| File | Description |
|------|-------------|
| `Fees.tsx` | Validator fees page displaying fee structures, earnings, and fee-related statistics for the validator node. Shows current fee rates, historical earnings, and fee collection metrics. |

###### applications/tari_validator_node/web_ui/src/routes/Mempool/

| File | Description |
|------|-------------|
| `Mempool.tsx` | Memory pool page showing pending transactions awaiting inclusion in blocks. Displays transaction queue, priorities, fees, and mempool statistics with real-time updates. |

###### applications/tari_validator_node/web_ui/src/routes/Templates/

| File | Description |
|------|-------------|
| `Templates.tsx` | Smart contract templates page displaying available templates, their functions, and deployment status. Shows template metadata, function signatures, and provides template management interface. |

###### applications/tari_validator_node/web_ui/src/routes/Transactions/

| File | Description |
|------|-------------|
| `Events.tsx` | Transaction events component displaying event logs and smart contract events triggered by transactions. Shows event data, parameters, and execution details. |
| `Instructions.tsx` | Transaction instructions component showing the execution steps and smart contract instructions within a transaction. Displays instruction details, parameters, and execution flow. |
| `Logs.tsx` | Transaction execution logs component displaying runtime logs, debug information, and execution traces for transactions. Shows detailed execution information for debugging and monitoring. |
| `Substates.tsx` | Transaction substates component showing the state changes and substate modifications caused by transaction execution. Displays before/after state values and substate metadata. |
| `TransactionDetails.tsx` | Transaction details page component displaying comprehensive transaction information. Shows transaction hash, inputs, outputs, fees, execution status, and related events using route parameter (/transactions/:transactionHash). |

###### applications/tari_validator_node/web_ui/src/routes/VN/

| File | Description |
|------|-------------|
| `ValidatorNode.css` | Main CSS stylesheet for the validator node interface. Provides styling for the overall validator node layout, dashboard components, navigation, and VN-specific UI elements. |
| `ValidatorNode.tsx` | Main validator node dashboard component providing overview and navigation for validator-specific functionality. Displays validator status, statistics, and provides access to all VN management features. |

###### applications/tari_validator_node/web_ui/src/routes/VN/Components/

| File | Description |
|------|-------------|
| `AllVNs.tsx` | All validator nodes component displaying network-wide validator information. Shows list of all active validators, their status, stake amounts, and performance metrics across the entire Tari network. |
| `Blocks.tsx` | Validator node blocks component showing blocks specific to this validator. Displays blocks proposed, validated, or relevant to the current validator node with block details and statistics. |
| `Connections.tsx` | Validator node connections component displaying peer connections specific to this validator. Shows connected peers, connection quality, communication statistics, and network topology from this node's perspective. |
| `Error.tsx` | Error handling component for validator node specific errors. Displays error messages, diagnostic information, and recovery options when validator operations fail or encounter issues. |
| `Fees.tsx` | Validator node fees component showing fee collection and earnings for this specific validator. Displays fee rates, collected fees, earnings history, and fee-related performance metrics. |
| `Info.css` | CSS stylesheet for validator node information components. Provides styling for validator info displays, statistics tables, status indicators, and layout specific to the VN info section. |
| `Info.tsx` | Validator node information component displaying detailed node metadata, configuration, and status. Shows node identity, version, configuration parameters, and operational statistics. |
| `Mempool.tsx` | Validator node mempool component showing the local mempool state for this validator. Displays pending transactions in this node's mempool, transaction priorities, and mempool management controls. |
| `TemplateFunctions.tsx` | Template functions component displaying smart contract template functions and methods. Shows available template functions, their signatures, parameters, and provides interface for template interaction using route parameter (/templates/:address). |
| `Templates.css` | CSS stylesheet for template-related components in the validator interface. Provides styling for template listings, function displays, parameter forms, and template management interface elements. |
| `Templates.tsx` | Validator node templates component showing smart contract templates known to this validator. Displays template registry, template details, deployment status, and provides template management functionality. |
| `helpers.tsx` | Helper utility functions for validator node components. Contains common functions for data formatting, state management, API calls, and shared logic used across VN-specific components. |

###### applications/tari_validator_node/web_ui/src/routes/ValidatorNodes/

| File | Description |
|------|-------------|
| `ValidatorNodes.tsx` | Validator nodes listing page showing all validators in the network. Displays validator registry, status, performance metrics, and provides navigation to individual validator details. |

###### applications/tari_validator_node/web_ui/src/theme/

| File | Description |
|------|-------------|
| `LayoutMain.tsx` | Main layout component defining the overall application structure for the validator web UI. Provides navigation, sidebar, header, and content area layout with responsive design and routing integration. |
| `echarts.css` | CSS styling for ECharts visualization components used throughout the validator interface. Provides custom styling for charts, graphs, and data visualization elements with Tari theme integration. |
| `theme.css` | Global CSS theme for the validator web UI. Defines base styles, typography, colors, layouts, and CSS custom properties that complement the Material-UI theme system. |
| `theme.ts` | Material-UI theme configuration for the validator web interface. Defines color palette (primary #9330FF, secondary #40388A), typography (AvenirMedium font), component styles, and Material-UI component overrides. Exports: default theme object. Used by: styled components, Material-UI components. |

###### applications/tari_validator_node/web_ui/src/utils/

| File | Description |
|------|-------------|
| `helpers.tsx` | General utility helper functions for the validator web UI. Contains common functions for data formatting, date/time handling, number formatting, and other shared utilities used across multiple components. |
| `interfaces.tsx` | TypeScript interfaces and type definitions for the validator web UI. Exports: ICommitteeChart interface for committee visualization data. Defines data structures for API responses, component props, and shared types across the application. |
| `json_rpc.tsx` | JSON-RPC client for communicating with the Tari validator node backend. Exports: API functions for transactions, blocks, templates, validator operations, and network management. Dependencies: Tari TypeScript bindings. Provides typed interfaces for all validator node API endpoints with automatic client address detection. |

#### applications/tari_validator_node_cli/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the Tari Validator Node CLI application. Defines dependencies including Tari workspace crates (common types, crypto, engine, transaction handling, validator node client), external crates (clap for CLI, reqwest for HTTP, tokio for async, serde for serialization), and development tools (anyhow, log, dirs). Enables clap derive and env features for command-line argument parsing and environment variable support. Standard Rust application manifest for the validator node command-line interface. |

##### applications/tari_validator_node_cli/src/

| File | Description |
|------|-------------|
| `cli.rs` | Command-line interface definition for the Tari Validator Node CLI. Defines Cli struct using clap derive macros with validator node daemon JSON-RPC endpoint configuration, base directory option, and command subcommands. Supports environment variable configuration (JRPC_ENDPOINT) and provides init() factory method for CLI parsing. Entry point for validator node command-line tool configuration and argument processing. |
| `cli_range.rs` | CLI range parsing utilities for the Tari Validator Node CLI. Defines CliRange<T> wrapper for RangeInclusive with string parsing support for epoch ranges and numeric ranges. Supports various range formats including single values (treated as inclusive range), open ranges (1.., ..10), exclusive ranges (1..10), and inclusive ranges (1..=10). Provides specialized parsing for Epoch types using u64 conversion. Used for command-line arguments that specify block heights, epoch ranges, and other numeric intervals. |
| `component_manager.rs` | Component and substate management for the Tari Validator Node CLI. Provides ComponentManager using JSON file storage (JFS) to track smart contract components and their substate dependencies. Features add_root_substate for storing component versions with child substates, commit_diff for processing SubstateDiff changes, and get_root_substate for queries. SubstateMetadata tracks component addresses, versions, and child SubstateRequirements. Essential for managing component state and dependencies during CLI transaction operations and component lifecycle tracking. |
| `from_hex.rs` | Hexadecimal string parsing utility for the Tari Validator Node CLI. Provides FromHex<T> wrapper type with FromStr implementation for parsing hex strings into any type that implements TryFrom<&[u8]>. Used throughout CLI for parsing addresses, hashes, and other cryptographic data from command-line arguments. Simplifies hex input handling with automatic error conversion to anyhow::Error and Display trait support for output formatting. |
| `key_manager.rs` | Cryptographic key management for the Tari Validator Node CLI. Provides KeyManager for persistent Ed25519 key pair storage and management with create (generate new keys), all (list all keys), get_active_key, and set_active_key operations. Uses JSON file storage in base_path/keys directory with automatic active key tracking. KeyPair struct includes secret_key, public_key, and is_active status with conversion methods to RistrettoPublicKeyBytes and NonFungibleAddress for ownership tokens. Essential for CLI identity management and transaction signing operations. |
| `lib.rs` | Main library module for the Tari Validator Node CLI application. Exports all CLI functionality including cli for command-line interface, command for command implementations, from_hex for hex parsing, key_manager for cryptographic key management, prompt for user interaction, table for output formatting, cli_range for range parsing, and component_manager for smart contract component tracking. Central entry point for all CLI library functionality. |
| `main.rs` | Validator node CLI application entry point providing command-line interface for validator operations including node management, consensus participation, and daemon interaction. Handles CLI parsing, validator node connection via JSON-RPC, key management, endpoint resolution, and authentication. Provides terminal-based validator functionality for node operations, consensus management, and validator administration with comprehensive error handling. |
| `prompt.rs` | Interactive prompt utilities for the validator node CLI. Provides user input handling, command completion, and interactive session management for the validator node command-line interface. |
| `table.rs` | ASCII table formatting utility for the Tari Validator Node CLI. Provides Table struct for creating formatted console output with automatic column width calculation, title headers, row separators, and optional row counting. Features set_titles, add_row, render methods with proper padding and alignment. Includes table_row! macro for easy row construction. Used throughout CLI commands for displaying transaction results, template information, and other tabular data in a readable spreadsheet-compatible format. |

###### applications/tari_validator_node_cli/src/command/

| File | Description |
|------|-------------|
| `account.rs` | Account management commands for the Tari Validator Node CLI. Provides AccountsSubcommand with Create command for creating new accounts on the Tari network. Handles account creation with public key integration using KeyManager, instruction building for CreateAccount operations, and transaction submission with dry-run support. Integrates with ValidatorNodeClient for transaction submission and result handling. Core functionality for account lifecycle management in the CLI tool. |
| `key.rs` | Cryptographic key management commands for the Tari Validator Node CLI. Provides KeysSubcommand with New (create), List, and Use (set active) operations for managing key pairs. Integrates with KeyManager for persistent key storage, creation, enumeration, and activation. Handles key lifecycle including creation of new Ed25519 key pairs, listing all stored keys with active status indication, and setting active keys for transaction signing. Essential for CLI cryptographic operations and identity management. |
| `manifest.rs` | Transaction manifest commands for the Tari Validator Node CLI. Provides ManifestSubcommand with New and Check operations for transaction manifest management. New command creates manifest templates from embedded template file, Check command parses and validates manifest syntax with global variable support. Handles manifest file I/O, stdin input for piped content, manifest parsing with tari_transaction_manifest, and global variable parsing for parameterized manifests. Essential for transaction construction and validation workflows. |
| `manifest.template.rs` | Rust module containing transaction manifest templates for the validator node CLI. Provides template generators for common transaction patterns and smart contract interactions in the Tari ecosystem. |
| `mod.rs` | Main command module for the Tari Validator Node CLI. Defines the top-level Command enum with subcommands for Templates, Keys, Transactions, Accounts, Manifests, and Peers. Exports all command-related types and functionality including TemplateSubcommand, KeysSubcommand, and imports for account, manifest, peer, and transaction commands. Provides the central command dispatch structure for the CLI application with alias support for shorter command names. |
| `peer.rs` | Peer management commands for the Tari Validator Node CLI. Provides PeersSubcommand with Connect operation for adding peers to the validator node network. Handles peer connection with public key parsing, multiaddr addresses, and wait-for-dial functionality. Integrates with ValidatorNodeClient AddPeerRequest for establishing P2P connections. Essential for network topology management and peer discovery in the validator node infrastructure. |
| `template.rs` | Smart contract template management commands for the Tari Validator Node CLI. Provides TemplateSubcommand with Get and List operations for template inspection. Get command retrieves template details including ABI functions, arguments, and return types with formatted table output. List command displays active templates with address and status information. Integrates with ValidatorNodeClient for template queries and uses Table utility for formatted console output. Essential for smart contract development and template exploration. |
| `transaction.rs` | Transaction management commands for the Tari Validator Node CLI. Provides comprehensive transaction operations including Get (retrieve transaction results), Submit (execute individual instructions), and SubmitManifest (execute transaction manifests). Features extensive CLI instruction support for smart contract operations (CallFunction, CallMethod, CreateAccount, etc.), transaction building with inputs/outputs, dry-run execution, result waiting with timeout, and detailed output formatting. Integrates with KeyManager for signing, ComponentManager for component tracking, and ValidatorNodeClient for transaction submission. Supports manifest parsing, global variables, fee management, and comprehensive transaction lifecycle management. Core functionality for interacting with the Tari smart contract platform. |
| `vn.rs` | Validator node command implementations for the CLI tool. Contains command handlers for validator operations, node management, and validator-specific functionality in the Tari network. |

#### applications/tari_wallet_cli/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the Tari wallet CLI application. Dependencies: tari engine, utilities, wallet daemon client, transaction libraries, clap for CLI parsing, and networking libraries. Provides command-line wallet functionality for the Tari ecosystem. |

##### applications/tari_wallet_cli/src/

| File | Description |
|------|-------------|
| `cli.rs` | Command-line interface configuration for the Tari wallet CLI. Exports: Cli struct. Dependencies: clap parser, multiaddr. Defines CLI arguments for daemon endpoint (JRPC_ENDPOINT), JWT token authentication, and command routing. Used by: main application entry point. |
| `from_base64.rs` | Base64 decoding utility module for the wallet CLI. Provides custom argument parsing for Base64-encoded inputs, commonly used for handling encoded transaction data, keys, and other binary data in CLI commands. |
| `from_hex.rs` | Hexadecimal decoding utility module for the wallet CLI. Provides custom argument parsing for hex-encoded inputs, commonly used for handling transaction hashes, addresses, and cryptographic data in CLI commands. |
| `lib.rs` | Main library module for the Tari wallet CLI. Exports: cli, command modules, utility modules (from_base64, from_hex, prompt, table). Central module organization and public API for the wallet command-line interface library. |
| `main.rs` | Wallet CLI application entry point providing command-line interface for wallet operations including account management, transactions, and daemon interaction. Handles CLI parsing, wallet daemon connection via JSON-RPC, multiaddr endpoint resolution, and authentication. Provides terminal-based wallet functionality for account operations, transaction submission, and wallet management with comprehensive error handling. |
| `prompt.rs` | Interactive prompt utilities for the wallet CLI. Provides user input handling, password prompts, confirmation dialogs, and interactive command-line session management for secure wallet operations. |
| `table.rs` | Table formatting utilities for the wallet CLI. Provides macros and functions for displaying tabular data like account balances, transaction history, and other structured wallet information in the terminal. |

###### applications/tari_wallet_cli/src/command/

| File | Description |
|------|-------------|
| `account.rs` | Account management commands for the wallet CLI. Handles account creation, listing, balance queries, and account-specific operations. Provides command implementations for managing Tari wallet accounts via the command line. |
| `auth.rs` | Authentication commands for the wallet CLI. Handles login, logout, token management, and authentication-related operations for connecting to the wallet daemon. Manages JWT authentication flow. |
| `key.rs` | Key management commands for the wallet CLI. Handles key generation, import, export, and cryptographic key operations. Provides command implementations for managing wallet keys and cryptographic material. |
| `mod.rs` | Command module aggregator for the wallet CLI. Exports all command modules and defines the main Command enum that routes CLI subcommands to their respective handlers. Central command dispatch system. |
| `nfts.rs` | NFT (Non-Fungible Token) management commands for the wallet CLI. Handles NFT creation, transfer, listing, and metadata operations. Provides command implementations for interacting with NFTs on the Tari network. |
| `proof.rs` | Proof generation and verification commands for the wallet CLI. Handles cryptographic proof creation, validation, and proof-related operations for privacy and validation features in the Tari ecosystem. |
| `transaction.rs` | Transaction management commands for the wallet CLI. Handles transaction creation, submission, status checking, and transaction history queries. Provides comprehensive transaction lifecycle management from the command line. |
| `validator.rs` | Validator-related commands for the wallet CLI. Handles validator node interactions, staking operations, delegation management, and validator-specific transactions from the wallet perspective. |
| `webrtc.rs` | WebRTC connection commands for the wallet CLI. Handles peer-to-peer WebRTC connections, communication setup, and WebRTC-based wallet interactions for decentralized wallet connectivity. |

#### applications/tari_walletd/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the Tari wallet daemon. Dependencies include wallet SDK, crypto libraries, template management, indexer client, key management, and WebAuthn support. Optional web UI feature with embedded assets. Provides wallet services, account management, transaction signing, and burn-claim functionality for the Ootle layer-2 network. |
| `build.rs` | Build script for the Tari wallet daemon. Handles build-time code generation, compilation flags, environment setup, and build configuration for the wallet daemon binary. May include version generation and platform-specific configuration. |
| `log4rs_sample.yml` | Sample logging configuration for the wallet daemon using log4rs. Defines log levels, output formats, file rotation, and logging destinations for wallet daemon operations. Template for production logging setup. |
| `openrpc.json` | OpenRPC specification for the wallet daemon JSON-RPC API. Defines API endpoints, request/response schemas, error codes, and method documentation for wallet daemon RPC interface. Used for API documentation and client generation. |

##### applications/tari_walletd/src/

| File | Description |
|------|-------------|
| `cli.rs` | Command-line interface configuration for the Tari wallet daemon. Exports: Cli struct, WalletRestoreArgs, Subcommand enum. Dependencies: clap, common CLI args, configuration providers. Defines CLI arguments for JSON-RPC endpoints, WebUI URLs, indexer connections, keyring password override, and wallet restoration options. Implements ConfigOverrideProvider for runtime configuration. |
| `config.rs` | Configuration management for Tari wallet daemon application. Defines ApplicationConfig and WalletDaemonConfig with network settings, indexer endpoints, signaling server configuration, authentication options, and password handling. Provides structured configuration loading with validation for wallet daemon deployment across different network environments. |
| `indexer_jrpc_impl.rs` | Indexer JSON-RPC implementation for the wallet daemon. Provides RPC client implementation for communicating with blockchain indexer services, handling indexer-specific operations and data synchronization. |
| `jrpc_server.rs` | JSON-RPC server implementation for the wallet daemon. Provides the main RPC server infrastructure, request handling, method dispatch, and server configuration for wallet daemon API endpoints. |
| `lib.rs` | Wallet daemon application library providing Layer-2 wallet services. Includes JSON-RPC server, account management, transaction handling, indexer integration, and web UI. Comprehensive wallet backend enabling user applications to interact with Tari Ootle network for digital asset management. |
| `main.rs` | Wallet daemon application entry point providing wallet services, account management, and transaction submission capabilities. Handles CLI parsing, configuration loading, wallet store initialization, SDK initialization, and daemon startup. Includes key management, panic handling, and comprehensive error management. Core service for user wallet operations, asset management, and transaction creation with JSON-RPC API. |
| `notify.rs` | Notification system for the wallet daemon. Implements event notifications, webhook handling, and notification delivery for wallet events like transaction confirmations, account updates, and system events. |
| `webrtc.rs` | WebRTC implementation for the wallet daemon. Provides peer-to-peer WebRTC communication, connection management, and WebRTC-based networking for decentralized wallet connectivity and communication. |

###### applications/tari_walletd/src/handlers/

| File | Description |
|------|-------------|
| `accounts.rs` | Account management handlers for the wallet daemon JSON-RPC API. Implements RPC endpoints for account creation, listing, balance queries, and account operations. Handles account-related requests from wallet clients. |
| `confidential.rs` | Confidential transaction handlers for the wallet daemon. Implements privacy-preserving transaction operations, confidential balance management, and related cryptographic operations for enhanced transaction privacy. |
| `context.rs` | Request context and session management for wallet daemon handlers. Provides context objects, session state, authentication context, and shared data structures used across RPC handler implementations. |
| `error.rs` | Error handling and error types for wallet daemon RPC handlers. Defines error enums, error conversions, and standardized error responses for JSON-RPC API operations. Centralizes error management across handlers. |
| `helpers.rs` | Helper utilities and common functions for wallet daemon RPC handlers. Provides shared utility functions, data conversion helpers, validation routines, and common operations used across multiple handler modules. |
| `keys.rs` | Cryptographic key management handlers for the wallet daemon. Implements RPC endpoints for key generation, import, export, derivation, and key lifecycle management. Handles wallet cryptographic material securely. |
| `mod.rs` | Wallet daemon request handlers module aggregating all API endpoint implementations. Exports handlers for accounts, authentication, confidential transactions, templates, substates, WebAuthn, and WebRTC. Core API layer for wallet operations with comprehensive endpoint coverage. |
| `nfts.rs` | NFT (Non-Fungible Token) handlers for the wallet daemon. Implements RPC endpoints for NFT creation, transfer, metadata management, and NFT collection operations. Handles digital asset management within the wallet. |
| `rpc.rs` | Main RPC server and handler aggregation for the wallet daemon. Coordinates all RPC handlers, implements the JSON-RPC server interface, and provides the central RPC dispatch system for wallet operations. |
| `settings.rs` | Settings and configuration handlers for the wallet daemon. Implements RPC endpoints for wallet configuration management, preference settings, and configuration updates. Handles runtime configuration changes. |
| `substates.rs` | Substate management handlers for the wallet daemon. Implements RPC endpoints for querying, monitoring, and managing blockchain substates relevant to wallet accounts and transactions. |
| `templates.rs` | Smart contract template handlers for the wallet daemon. Implements RPC endpoints for template registration, querying, deployment, and interaction with smart contract templates from the wallet perspective. |
| `transaction.rs` | Transaction handlers for the wallet daemon. Implements RPC endpoints for transaction creation, submission, status monitoring, and transaction history management. Core transaction lifecycle management for the wallet. |
| `validator.rs` | Validator interaction handlers for the wallet daemon. Implements RPC endpoints for validator operations, staking, delegation, and validator-related transactions from the wallet's perspective. |
| `wallet.rs` | Core wallet management handlers for the wallet daemon. Implements RPC endpoints for wallet creation, backup, restore, status, and fundamental wallet operations. Primary wallet lifecycle management. |
| `wasm_optimizer.rs` | WebAssembly optimization handlers for the wallet daemon. Implements WASM code optimization, compilation, and optimization services for smart contract templates and WebAssembly-based operations. |
| `webauthn.rs` | WebAuthn service handlers for the wallet daemon. Implements WebAuthn credential management, registration, and authentication operations for hardware security key and biometric authentication support. |
| `webrtc.rs` | WebRTC handlers for the wallet daemon. Implements peer-to-peer WebRTC communication endpoints, connection management, and WebRTC-based wallet connectivity for decentralized communication. |

###### applications/tari_walletd/src/handlers/auth/

| File | Description |
|------|-------------|
| `anonym.rs` | Anonymous authentication handler for the wallet daemon. Implements authentication bypass for development or testing scenarios where no authentication is required. Part of the modular authentication system. |
| `jwt.rs` | JWT (JSON Web Token) authentication handler for the wallet daemon. Implements token-based authentication with JWT creation, validation, and refresh mechanisms for secure wallet access. |
| `mod.rs` | Authentication module aggregator for the wallet daemon. Exports authentication methods including anonymous, JWT, and WebAuthn handlers. Provides central authentication system coordination and method selection. |
| `webauthn.rs` | WebAuthn authentication handler for the wallet daemon. Implements FIDO2/WebAuthn authentication using hardware security keys, biometrics, or platform authenticators for enhanced wallet security. |

###### applications/tari_walletd/src/http_ui/

| File | Description |
|------|-------------|
| `mod.rs` | HTTP UI module aggregator for the wallet daemon. Exports HTTP server components for serving the wallet web interface. Provides module organization for web UI serving functionality. |
| `server.rs` | HTTP server implementation for serving the wallet web UI. Handles static file serving, routing, and HTTP server configuration for the wallet daemon's web interface. Serves the React-based wallet UI. |

###### applications/tari_walletd/src/services/

| File | Description |
|------|-------------|
| `account_monitor.rs` | Account monitoring service for the wallet daemon. Implements background monitoring of account balances, state changes, and account-related events. Provides real-time account status tracking. |
| `events.rs` | Event handling service for the wallet daemon. Implements event bus, event processing, and event distribution system for wallet-related events and system notifications. Coordinates event flow across services. |
| `mod.rs` | Wallet daemon services module providing background processes and service orchestration. Manages account monitoring, transaction service, template monitoring, WebAuthn services, and session management. Core service layer coordinating wallet operations and event notifications. |
| `recovery_service.rs` | Wallet recovery service for the wallet daemon. Implements wallet restoration from seed phrases, backup recovery, and wallet reconstruction operations. Handles wallet recovery workflows and state restoration. |
| `session_store.rs` | Session management and storage service for the wallet daemon. Implements session persistence, session lifecycle management, and session data storage for maintaining authenticated wallet sessions. |
| `template_monitor.rs` | Template monitoring service for the wallet daemon. Implements background monitoring of smart contract templates, template updates, and template-related events. Provides real-time template registry tracking. |
| `webauthn.rs` | WebAuthn service implementation for the wallet daemon. Provides WebAuthn credential management, authentication flows, and integration with FIDO2/WebAuthn standards for enhanced wallet security. |

###### applications/tari_walletd/src/services/transaction_service/

| File | Description |
|------|-------------|
| `error.rs` | Error handling for the transaction service. Defines transaction-specific error types, error conversions, and error handling for transaction processing, validation, and execution operations. |
| `handle.rs` | Transaction service handle implementation. Provides the main interface for transaction service operations, handles transaction requests, and coordinates transaction processing workflows. |
| `mod.rs` | Transaction service module aggregator. Exports transaction service components including handle, service implementation, and error types. Central module organization for transaction processing. |
| `service.rs` | Core transaction service implementation. Implements transaction creation, validation, submission, monitoring, and lifecycle management. Central transaction processing engine for the wallet daemon. |

##### applications/tari_walletd/web_ui/

| File | Description |
|------|-------------|
| `.env.example` | Environment variable template for the wallet daemon web UI. Provides example configuration for API endpoints, authentication settings, and environment-specific variables needed for the wallet web interface. |
| `.gitignore` | Git ignore rules for the wallet daemon web UI. Excludes Node.js dependencies, build artifacts, environment files, and development artifacts from version control for the wallet web interface. |
| `.prettierrc.js` | Prettier code formatting configuration for the wallet web UI. Defines code style rules, formatting options, and formatting preferences for maintaining consistent code style across the wallet interface. |
| `README.md` | Documentation for the Tari wallet daemon web UI. Provides setup instructions, development guidelines, build processes, and usage information for the React-based wallet web interface. |
| `index.html` | Main HTML entry point for the Tari wallet daemon web UI. Serves as the root template for the React wallet application, containing the basic HTML structure and root div where the wallet components mount. |
| `package.json` | Node.js package configuration for the Tari wallet daemon web UI. Dependencies: React, Material-UI, TanStack Query, WalletConnect, Zustand state management, TypeScript bindings. Development setup for the wallet interface with Vite build tooling. |
| `tsconfig.json` | TypeScript configuration for the wallet daemon web UI project. Configures ESNext target, React JSX, strict type checking, and module resolution for the wallet React application. |
| `tsconfig.node.json` | TypeScript configuration for Node.js tooling and build scripts in the wallet web UI project. Separate config for build tools like Vite with Node.js-specific settings. |
| `vite.config.ts` | Vite build configuration for the Tari wallet daemon web UI. Configures React SWC plugin for fast compilation, development server settings, and build optimization for the wallet interface. |

###### applications/tari_walletd/web_ui/public/

| File | Description |
|------|-------------|
| `.gitkeep` | Git keep file to preserve the public directory structure in version control when the directory might otherwise be empty. Ensures public folder exists for static assets in the wallet web UI. |
| `index.html` | Public HTML template for the wallet daemon web UI. Alternative or backup HTML file in the public directory, typically used during build process for static assets and SEO meta tags for the wallet interface. |
| `manifest.json` | Progressive Web App manifest for the wallet daemon web UI. Defines app metadata including name, icons, theme colors, and display properties for when the wallet app is installed on mobile devices or desktops. |
| `robots.txt` | Search engine crawler instructions for the wallet daemon web UI. Defines which parts of the wallet interface search engines can access and index, typically restricting access for this private wallet application. |

###### applications/tari_walletd/web_ui/src/

| File | Description |
|------|-------------|
| `App.css` | Main CSS stylesheet for the wallet daemon web UI. Provides global styles, component styles, and layout definitions for the wallet interface. Complements Material-UI theme system with custom styling. |
| `App.tsx` | Main application component for the Tari wallet daemon web UI. Exports: breadcrumbRoutes, GuardedRoute component, App component. Dependencies: React Router, auth hooks, JWT handling. Defines routing for accounts, transactions, keys, templates, settings, and authentication. Implements authentication guards and token management with automatic token expiration handling. |
| `main.tsx` | Main application entry point for the Tari wallet daemon web UI. Sets up React application with query client, theme provider, and router configuration. Renders the root App component with TanStack Query for API state management. |
| `vite-env.d.ts` | Vite environment type definitions for TypeScript support in the wallet UI. Provides type declarations for Vite-specific imports, environment variables, and module resolution. |

###### applications/tari_walletd/web_ui/src/Components/

| File | Description |
|------|-------------|
| `Accordion.tsx` | Reusable accordion/collapsible UI component for the wallet web interface. Provides expandable sections for organizing wallet data like transaction details, account information, or settings sections. |
| `AlertDialog.tsx` | Alert dialog component for user notifications and confirmations in the wallet web UI. Uses Material-UI dialog system to display warnings, errors, confirmations, and important wallet operation messages. |
| `Breadcrumbs.tsx` | Navigation breadcrumb component for the wallet web UI. Uses react-router-dom breadcrumbs to display navigational context and allow users to navigate back to parent pages within the wallet interface. |
| `CodeBlock.tsx` | Code display component for formatting and presenting code snippets, transaction data, or JSON responses. Provides syntax highlighting and copy functionality for technical data in the wallet interface. |
| `CopyAddress.tsx` | Address copying component with specialized formatting for wallet addresses. Provides one-click copying of account addresses, public keys, and wallet identifiers with visual feedback. |
| `CopyToClipboard.tsx` | Generic clipboard copy utility component for the wallet UI. Provides copy functionality with visual feedback for transaction hashes, addresses, and other wallet data. |
| `Copyright.tsx` | Footer copyright component displaying Tari Project legal information and licensing details. Shows copyright notice and version information for the wallet web interface. |
| `Error.tsx` | Error display component for handling and presenting error states in the wallet UI. Shows user-friendly error messages, error details, and recovery actions for wallet operations. |
| `FetchStatusCheck.tsx` | API fetch status monitoring component for the wallet UI. Provides real-time status checking for API connections, displays connection health, and handles connectivity issues. |
| `HeadingMenu.tsx` | Navigation menu component for page headers in the wallet interface. Provides dropdown or contextual menu options for actions related to wallet operations and navigation. |
| `JsonTooltip.tsx` | JSON data tooltip component for displaying formatted JSON on hover. Used to show detailed transaction data, account information, or technical details without cluttering the wallet interface. |
| `Loading.tsx` | Loading spinner component for the wallet UI. Displays loading indicators during API calls, transaction processing, and data fetching operations in the wallet interface. |
| `MenuItems.tsx` | Menu items definition and configuration for the wallet web UI navigation. Contains menu structure, routing links, and permissions for wallet sections like accounts, transactions, keys, and settings. |
| `NFTList.tsx` | NFT listing component for displaying Non-Fungible Tokens in the wallet. Shows NFT collection, metadata, ownership details, and provides actions for NFT transfer and management. |
| `PageHeading.tsx` | Page title heading component with consistent styling across the wallet web interface. Provides standardized typography and spacing for main page titles using Material-UI theme. |
| `SearchFilter.tsx` | Search input component with filtering capabilities for wallet data. Provides text search functionality with debouncing and filter controls for transactions, accounts, and other wallet content. |
| `SecondaryHeading.tsx` | Secondary heading component for section titles and subsection headers in the wallet UI. Provides consistent styling for hierarchical content organization below main page headings. |
| `StatusChip.tsx` | Status indicator chip component for displaying wallet operation states. Uses Material-UI Chip with color coding for transaction status, account status, or connection states. |
| `Stepper.tsx` | Step-by-step process component for multi-step wallet operations. Provides visual progress indication for complex workflows like transaction creation, account setup, or template deployment. |
| `StyledComponents.ts` | Collection of styled Material-UI components with custom theming for the wallet web interface. Provides consistent design system components with Tari branding and styling patterns used across the wallet application. |
| `ThemeSwitcher.tsx` | Theme switching component for toggling between light and dark modes in the wallet UI. Provides user preference controls for visual theme selection with persistent storage. |
| `Title.tsx` | Main title component for the wallet application header and branding. Displays the Tari wallet title with consistent styling and potentially includes logo or navigation elements. |

###### applications/tari_walletd/web_ui/src/Components/ConnectorLink/

| File | Description |
|------|-------------|
| `CheckMark.css` | CSS styling for checkmark component used in connector link flows. Provides visual styling for success states, confirmations, and completion indicators in external wallet connector interactions. |
| `CheckMark.tsx` | Checkmark visual component for connector link success states. Displays confirmation visual feedback when external wallet connections or operations complete successfully. |
| `ConfirmTransaction.tsx` | Transaction confirmation component for external connector flows. Handles transaction confirmation UI when external applications request transaction approvals through wallet connector protocols. |
| `ConnectorLink.css` | CSS styling for connector link components. Provides styling for external wallet connector interface elements, connection flows, and connector-specific UI components. |
| `ConnectorLink.tsx` | Main connector link component for external wallet integrations. Handles connection establishment, protocol negotiation, and communication with external applications requesting wallet services. |
| `ConnectorLogo.tsx` | Logo display component for external connector applications. Shows the logo and branding of external applications requesting wallet connections or services. |
| `Permissions.css` | CSS styling for permissions component in connector flows. Provides styling for permission request dialogs, permission lists, and access control UI elements for external connections. |
| `Permissions.tsx` | Permissions management component for connector links. Displays and manages permissions requested by external applications, allowing users to approve or deny specific wallet access permissions. |
| `index.ts` | Index module for connector link components. Exports all connector-related components for external wallet integration functionality. Provides centralized access to connector UI components. |

###### applications/tari_walletd/web_ui/src/Components/WalletConnectLink/

| File | Description |
|------|-------------|
| `CheckMark.css` | CSS styling for checkmark component used in WalletConnect integration flows. Provides visual styling for success states and confirmations in WalletConnect protocol interactions. |
| `CheckMark.tsx` | Checkmark visual component for WalletConnect success states. Displays confirmation visual feedback when WalletConnect protocol operations complete successfully. |
| `ConfirmTransaction.tsx` | Transaction confirmation component for WalletConnect flows. Handles transaction confirmation UI when external DApps request transaction approvals through WalletConnect protocol. |
| `ConnectorLink.css` | CSS styling for WalletConnect connector link components. Provides styling for WalletConnect interface elements, connection flows, and protocol-specific UI components. |
| `ConnectorLogo.tsx` | Logo display component for WalletConnect DApps. Shows the logo and branding of external decentralized applications requesting wallet connections through WalletConnect. |
| `Permissions.css` | CSS styling for permissions component in WalletConnect flows. Provides styling for WalletConnect permission request dialogs, permission lists, and access control UI elements. |
| `Permissions.tsx` | Permissions management component for WalletConnect integrations. Displays and manages permissions requested by DApps, allowing users to approve or deny specific wallet access permissions. |
| `WalletConnectLink.tsx` | Main WalletConnect integration component. Handles WalletConnect protocol implementation, session management, and communication with external DApps requesting wallet services. |
| `index.ts` | Index module for WalletConnect link components. Exports all WalletConnect-related components for external DApp integration functionality. Provides centralized access to WalletConnect UI components. |

###### applications/tari_walletd/web_ui/src/api/

| File | Description |
|------|-------------|
| `queryClient.ts` | TanStack Query client configuration for the wallet API. Sets up query client with caching strategies, retry policies, and global query settings for wallet API operations. |

###### applications/tari_walletd/web_ui/src/api/helpers/

| File | Description |
|------|-------------|
| `types.ts` | Type definitions and helper types for the wallet API layer. Defines common types, interfaces, and utility types used across API hooks and RPC communication in the wallet frontend. |

###### applications/tari_walletd/web_ui/src/api/hooks/

| File | Description |
|------|-------------|
| `useAccounts.tsx` | React hooks for account management API operations. Provides hooks for fetching account lists, account details, balance queries, and account creation using TanStack Query for state management. |
| `useAuth.tsx` | React hooks for authentication API operations. Provides hooks for login, logout, token refresh, authentication method detection, and authentication state management using TanStack Query. |
| `useKeys.tsx` | React hooks for cryptographic key management API operations. Provides hooks for key generation, import, export, key listing, and key derivation operations using TanStack Query. |
| `useNfts.tsx` | React hooks for NFT (Non-Fungible Token) API operations. Provides hooks for NFT creation, transfer, listing, metadata management, and NFT collection queries using TanStack Query. |
| `useTemplatesAuthored.tsx` | React hooks for authored smart contract templates API operations. Provides hooks for querying templates created by the user, template authoring status, and template management using TanStack Query. |
| `useTokens.tsx` | React hooks for access token and API token management. Provides hooks for token creation, listing, revocation, and token-based authentication management using TanStack Query. |
| `useTransactions.tsx` | React hooks for transaction API operations. Provides hooks for transaction creation, submission, status monitoring, transaction history, and transaction details using TanStack Query. |
| `useWebauthn.tsx` | React hooks for WebAuthn API operations. Provides hooks for WebAuthn registration, authentication, credential management, and FIDO2 hardware security key integration using TanStack Query. |

###### applications/tari_walletd/web_ui/src/assets/

| File | Description |
|------|-------------|
| `Logo.tsx` | Tari logo SVG component for branding in the wallet web interface. Exports React component version of the Tari project logo with customizable sizing and theming properties. |
| `TariGem.tsx` | Tari gem icon SVG component for the wallet interface. Exports React component for the Tari gem symbol used in branding, status indicators, and decorative elements. |
| `TariLogo.tsx` | Tari logo SVG component with full branding for the wallet interface. Exports React component for the complete Tari logo used in headers, splash screens, and branding elements throughout the wallet application. |

###### applications/tari_walletd/web_ui/src/assets/images/

| File | Description |
|------|-------------|
| `FailureIcon.tsx` | Failure/error icon SVG component for the wallet UI. Displays error states, failed transactions, and negative status indicators with consistent styling throughout the wallet interface. |
| `SuccessIcon.tsx` | Success/checkmark icon SVG component for the wallet UI. Displays success states, completed transactions, and positive status indicators with consistent styling throughout the wallet interface. |

###### applications/tari_walletd/web_ui/src/routes/

| File | Description |
|------|-------------|
| `ErrorPage.tsx` | Error boundary and 404 page component for handling routing errors and application exceptions in the wallet UI. Displays user-friendly error messages and navigation options when pages are not found. |

###### applications/tari_walletd/web_ui/src/routes/AccessToken/

| File | Description |
|------|-------------|
| `AccessToken.tsx` | Access token generation page for the wallet UI. Provides interface for creating API access tokens, displaying token details, and managing token permissions for external application integration. |

###### applications/tari_walletd/web_ui/src/routes/AccessTokens/

| File | Description |
|------|-------------|
| `AccessTokens.tsx` | Access tokens management page showing list of API tokens. Displays existing tokens, creation dates, permissions, and provides controls for token revocation and management. |

###### applications/tari_walletd/web_ui/src/routes/AccountDetails/

| File | Description |
|------|-------------|
| `AccountDetails.tsx` | Account details page displaying comprehensive account information. Shows account balance, transaction history, account metadata, and provides account management actions using route parameter for account ID. |

###### applications/tari_walletd/web_ui/src/routes/Accounts/

| File | Description |
|------|-------------|
| `Accounts.tsx` | Accounts listing page showing all wallet accounts. Displays account list with balances, names, and provides navigation to account details and account creation functionality. |

###### applications/tari_walletd/web_ui/src/routes/AssetVault/

| File | Description |
|------|-------------|
| `AssetVault.tsx` | Main asset vault page serving as the dashboard for wallet assets. Displays asset overview, account summaries, recent transactions, and provides access to key wallet operations like sending, receiving, and asset management. |

###### applications/tari_walletd/web_ui/src/routes/AssetVault/Components/

| File | Description |
|------|-------------|
| `AccountBalance.tsx` | Account balance display component for the asset vault. Shows detailed balance information, available funds, pending transactions, and balance history with formatted currency display. |
| `AccountDetails.tsx` | Account details component for the asset vault displaying comprehensive account information. Shows account metadata, address, balances, and account-specific settings within the asset vault context. |
| `ActionMenu.tsx` | Action menu component providing quick access to common wallet operations in the asset vault. Includes actions for sending funds, receiving payments, creating accounts, and other primary wallet functions. |
| `AddAccount.tsx` | Account creation component for adding new wallet accounts. Provides form interface for account creation with name input, account type selection, and account setup configuration. |
| `Assets.tsx` | Assets display component showing all wallet assets including tokens, NFTs, and digital assets. Provides asset overview, value calculation, and navigation to individual asset details. |
| `ClaimBurn.tsx` | Claim burn component for handling burned asset claims in the wallet. Provides interface for claiming assets from burn transactions and managing burn-related operations. |
| `ClaimFees.tsx` | Fee claiming component for collecting validator fees and rewards. Provides interface for claiming accumulated fees from validator operations and staking rewards. |
| `MyAssets.tsx` | Personal assets component displaying user-owned digital assets. Shows owned tokens, NFTs, and other digital assets with management capabilities and portfolio overview. |
| `PublishTemplate.tsx` | Template publishing component for deploying smart contract templates. Provides interface for template code upload, metadata input, and template registration on the Tari network. |
| `SelectAccount.tsx` | Account selection component for choosing source/destination accounts in transactions. Provides dropdown or list interface for account selection with balance display and account filtering. |
| `SendMoney.tsx` | Send money component for creating outgoing transactions. Provides form interface for recipient selection, amount input, fee configuration, and transaction confirmation for sending funds. |
| `TransferNft.tsx` | NFT transfer component for sending Non-Fungible Tokens to other addresses. Provides interface for NFT selection, recipient input, and transfer confirmation with metadata display. |

###### applications/tari_walletd/web_ui/src/routes/Auth/

| File | Description |
|------|-------------|
| `Auth.tsx` | Authentication page for wallet login and access control. Provides authentication forms, method selection (JWT, WebAuthn, etc.), and handles authentication flow with redirect support. |

###### applications/tari_walletd/web_ui/src/routes/Keys/

| File | Description |
|------|-------------|
| `Keys.tsx` | Cryptographic keys management page for the wallet. Displays key pairs, public keys, key derivation information, and provides key import/export functionality for wallet cryptographic material. |

###### applications/tari_walletd/web_ui/src/routes/Manifest/

| File | Description |
|------|-------------|
| `Manifest.tsx` | Transaction manifest creation and management page. Provides interface for creating complex transaction manifests, multi-step operations, and smart contract interaction sequences. |

###### applications/tari_walletd/web_ui/src/routes/Onboarding/

| File | Description |
|------|-------------|
| `Onboarding.tsx` | Wallet onboarding page for new user setup and initial configuration. Guides users through wallet creation, backup, security setup, and initial account configuration. |

###### applications/tari_walletd/web_ui/src/routes/Settings/

| File | Description |
|------|-------------|
| `Settings.tsx` | Main settings page for wallet configuration and preferences. Provides organized settings interface with tabs for general, security, network, and advanced wallet configuration options. |

###### applications/tari_walletd/web_ui/src/routes/Settings/Components/

| File | Description |
|------|-------------|
| `GeneralSettings.tsx` | General settings component for basic wallet configuration. Provides controls for wallet name, default account, currency preferences, and general wallet behavior settings. |
| `IndexerSettings.tsx` | Indexer configuration settings component for blockchain data indexing. Provides controls for indexer endpoint configuration, sync settings, and indexer-related preferences. |
| `SettingsTabs.tsx` | Settings navigation tabs component for organizing settings categories. Provides tabbed interface for different settings sections like general, security, network, and advanced settings. |
| `ViewVaultBalance.tsx` | Vault balance viewing component for confidential balance revelation. Provides interface for viewing confidential balances using view keys and cryptographic proofs for privacy-preserving balance display. |

###### applications/tari_walletd/web_ui/src/routes/Templates/

| File | Description |
|------|-------------|
| `Templates.tsx` | Smart contract templates page for template management and deployment. Displays available templates, authored templates, and provides interface for template creation and deployment. |

###### applications/tari_walletd/web_ui/src/routes/Transactions/

| File | Description |
|------|-------------|
| `Events.tsx` | Transaction events component displaying event logs and smart contract events triggered by transactions. Shows event data, parameters, and execution details for transaction analysis. |
| `FeeInstructions.tsx` | Fee instructions component showing transaction fee breakdown and fee calculation details. Displays fee structure, fee instructions, and fee estimation for transaction cost analysis. |
| `Instructions.tsx` | Transaction instructions component showing execution steps and smart contract instructions within a transaction. Displays instruction details, parameters, and execution flow for transaction analysis. |
| `Logs.tsx` | Transaction execution logs component displaying runtime logs, debug information, and execution traces. Shows detailed execution information for transaction debugging and monitoring. |
| `Substates.tsx` | Transaction substates component showing state changes and substate modifications caused by transaction execution. Displays before/after state values and substate metadata for state analysis. |
| `TransactionDetails.tsx` | Transaction details page displaying comprehensive transaction information. Shows transaction hash, inputs, outputs, fees, execution status, and related events using route parameter for transaction ID. |
| `Transactions.tsx` | Transactions listing component showing transaction history and status. Displays transaction list with filters, search functionality, and navigation to transaction details. |
| `TransactionsLayout.tsx` | Transactions page layout component providing structure for transaction-related pages. Implements shared layout, navigation, and context for all transaction views and details. |

###### applications/tari_walletd/web_ui/src/routes/Wallet/

| File | Description |
|------|-------------|
| `Wallet.css` | CSS stylesheet for the main wallet dashboard and wallet-specific components. Provides styling for wallet layout, dashboard elements, and wallet-specific UI components. |
| `Wallet.tsx` | Main wallet dashboard page providing overview of wallet status and operations. Displays wallet summary, recent activity, quick actions, and navigation to detailed wallet functions. |

###### applications/tari_walletd/web_ui/src/routes/Wallet/Components/

| File | Description |
|------|-------------|
| `AccessTokens.tsx` | Access tokens management component for the wallet overview. Displays API tokens within the wallet context, showing token status and providing quick access to token management functionality. |
| `Accounts.tsx` | Accounts overview component for the wallet dashboard. Displays account summaries, balances, and account management within the main wallet view with quick access to account actions. |
| `Keys.tsx` | Keys overview component for the wallet dashboard. Displays key management summary, key status, and provides quick access to key operations within the main wallet interface. |

###### applications/tari_walletd/web_ui/src/routes/WebauthnRegistration/

| File | Description |
|------|-------------|
| `Webauthn.tsx` | WebAuthn registration page coordinating the security key enrollment process. Manages registration flow, credential management, and provides unified interface for WebAuthn authentication setup. |

###### applications/tari_walletd/web_ui/src/routes/WebauthnRegistration/Components/

| File | Description |
|------|-------------|
| `Login.tsx` | WebAuthn login component for hardware security key authentication. Provides interface for WebAuthn authentication flow, credential selection, and biometric/hardware key login process. |
| `Registration.tsx` | WebAuthn registration component for enrolling new security keys or biometric credentials. Provides interface for WebAuthn credential creation, device registration, and security key setup. |

###### applications/tari_walletd/web_ui/src/store/

| File | Description |
|------|-------------|
| `accountStore.ts` | Account state management store using Zustand. Manages account selection, active account state, account preferences, and account-related UI state persistence across the wallet application. |
| `authStore.ts` | Authentication state management store using Zustand. Manages auth tokens, login status, authentication method, and session state with persistent storage for wallet authentication. |
| `manifestStore.ts` | Transaction manifest state management store using Zustand. Manages manifest creation state, manifest templates, and manifest-related data for complex transaction building. |
| `themeStore.ts` | Theme preference state management store using Zustand. Manages dark/light mode selection, theme preferences, and UI theme state with persistent storage across sessions. |

###### applications/tari_walletd/web_ui/src/theme/

| File | Description |
|------|-------------|
| `LayoutMain.tsx` | Main layout component defining the overall application structure for the wallet web UI. Provides navigation, sidebar, header, and content area layout with responsive design and routing integration. |
| `colors.ts` | Color palette definitions for the wallet UI theme. Defines primary colors, secondary colors, semantic colors (success, error, warning), and color variations for consistent theming. |
| `theme.css` | Global CSS theme for the wallet web UI. Defines base styles, typography, colors, layouts, and CSS custom properties that complement the Material-UI theme system. |
| `tokens.ts` | Design system tokens for the wallet UI. Defines spacing, typography scale, border radius, shadows, and other design tokens for consistent styling across the wallet application. |

###### applications/tari_walletd/web_ui/src/utils/

| File | Description |
|------|-------------|
| `cbor.ts` | CBOR (Concise Binary Object Representation) utilities for the wallet. Provides encoding/decoding functions for CBOR data format used in blockchain transactions and cryptographic operations. |
| `helpers.tsx` | General utility helper functions for the wallet web UI. Contains common functions for data formatting, date/time handling, number formatting, validation, and other shared utilities used across wallet components. |
| `json_rpc.tsx` | JSON-RPC client utility for the wallet daemon web UI. Provides typed API functions for all wallet operations including accounts, transactions, keys, templates, and authentication. Uses wallet JRPC client with automatic error handling and type safety. |
| `serialize.ts` | Serialization utilities for the wallet UI. Provides functions for serializing/deserializing wallet data, transaction data, and other complex objects for storage, transmission, or API communication. |
| `tari_permissions.tsx` | Tari-specific permissions utilities for the wallet UI. Handles permission management, access control validation, and permission-related operations for wallet features and external integrations. |

#### applications/tari_watcher/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the Tari Watcher network monitoring application. Dependencies: tokio for async operations, logging frameworks, configuration management, and networking libraries. Provides network monitoring and alerting functionality for Tari infrastructure. |
| `README.md` | Documentation for the Tari Watcher network monitoring application. Provides setup instructions, configuration guide, monitoring capabilities, and usage information for the network monitoring and alerting system. |

##### applications/tari_watcher/src/

| File | Description |
|------|-------------|
| `alerting.rs` | Alerting system for the Tari Watcher. Implements alert generation, notification delivery, alert threshold management, and integration with external monitoring systems for network health alerts. |
| `cli.rs` | Command-line interface for the Tari Watcher monitoring application. Defines CLI arguments for monitoring configuration, alert settings, network parameters, and watcher operation modes. |
| `config.rs` | Configuration management for the Tari Watcher application. Handles configuration loading, validation, monitoring parameters, alert thresholds, and watcher-specific settings for network monitoring. |
| `constants.rs` | Constants and static values for the Tari Watcher application. Defines monitoring thresholds, timeout values, default configuration parameters, and other compile-time constants used throughout the monitoring system. |
| `helpers.rs` | Helper utility functions for the Tari Watcher monitoring system. Provides common functions for data processing, network utilities, formatting, and shared operations used across watcher components. |
| `logger.rs` | Logging configuration and setup for the Tari Watcher application. Implements structured logging, log level management, log output formatting, and logging infrastructure for monitoring operations. |
| `main.rs` | Network monitoring and alerting application for Tari infrastructure. Provides process management, monitoring capabilities, alerting systems, and transaction worker functionality. Monitors network health, validator node status, and system performance with configurable alerts and automated response mechanisms for operational management. |
| `manager.rs` | Main manager for coordinating Tari Watcher operations. Orchestrates monitoring tasks, manages worker processes, handles system lifecycle, and coordinates between different monitoring components. |
| `minotari.rs` | Minotari-specific monitoring integration for the Tari Watcher. Implements monitoring hooks, Minotari node communication, and Minotari-specific metrics collection and alerting. |
| `monitoring.rs` | Core monitoring functionality for the Tari Watcher. Implements metrics collection, performance monitoring, health checks, and monitoring data aggregation for network components. |
| `process.rs` | Process management for the Tari Watcher system. Handles process monitoring, process lifecycle management, process health checks, and coordination of monitored system processes. |
| `shutdown.rs` | Graceful shutdown handling for the Tari Watcher application. Implements shutdown signals, cleanup procedures, resource deallocation, and coordinated system shutdown for monitoring components. |
| `transaction_worker.rs` | Transaction monitoring worker for the Tari Watcher. Implements background transaction monitoring, transaction pool analysis, transaction performance metrics, and transaction-related alerting. |

### bindings/

| File | Description |
|------|-------------|
| `.gitignore` | Git ignore rules for the TypeScript bindings generation. Excludes generated files, build artifacts, and temporary files from version control for the TypeScript API bindings project. |
| `README.md` | Documentation for the TypeScript bindings generation system. Provides setup instructions, build process, usage guide, and information about auto-generated TypeScript types from Rust structures for API client integration. |
| `build.sh` | Build script for generating TypeScript bindings from Rust types. Automates the process of extracting Rust type definitions and generating corresponding TypeScript interfaces for seamless frontend-backend integration. |
| `package.json` | Node.js package configuration for the TypeScript bindings library. Defines package metadata, dependencies, build scripts, and publishing configuration for the auto-generated TypeScript API types package. |
| `replace-backslash.ps1` | PowerShell script for Windows path normalization in TypeScript bindings generation. Handles path separator conversion and file path processing for cross-platform compatibility during bindings generation. |
| `tsconfig.json` | TypeScript configuration file for the bindings module. Configures TypeScript compiler settings for generating type-safe bindings from Rust types. Includes compilation targets, module resolution, and type checking options optimized for auto-generated type definitions and client library distribution. |
| `tsconfig.prod.json` | Production TypeScript configuration file for the bindings module. Extends base TypeScript config with production-optimized settings including stricter type checking, optimized output, and distribution-ready compilation options. Used for building production bindings packages and client library releases. |

#### bindings/src/

| File | Description |
|------|-------------|
| `base-node-client.ts` | TypeScript client for Tari base node API communication. Provides typed interfaces and client methods for interacting with base node functionality, blockchain queries, and node management operations. |
| `index.ts` | TypeScript type definitions and bindings for Tari Ootle blockchain data structures. Exports comprehensive type system covering transactions, components, substates, confidential outputs, committees, and protocol messages. Enables type-safe interaction with blockchain APIs from TypeScript applications. |
| `tari-indexer-client.ts` | TypeScript client for Tari indexer API communication. Provides typed interfaces and client methods for blockchain data indexing, querying indexed data, and indexer-specific operations. |
| `validator-node-client.ts` | [GENERATED] TypeScript client library barrel file exporting all validator node client types and interfaces. Auto-generated from Rust types via ts-rs. Provides centralized access to all validator node API types including requests, responses, and data structures. Part of the bindings module for validator node integration and API consumption in TypeScript applications. |
| `wallet-daemon-client.ts` | [GENERATED] TypeScript client library barrel file exporting all wallet daemon client types and interfaces. Auto-generated from Rust types via ts-rs. Provides centralized access to all wallet daemon API types including account management, transactions, authentication, and WebAuthn flows. Part of the bindings module for wallet integration and API consumption in TypeScript applications. |

##### bindings/src/helpers/

| File | Description |
|------|-------------|
| `helpers.ts` | Helper utilities for TypeScript bindings and API clients. Provides common functions for data conversion, type checking, API response handling, and utility functions used across binding clients. |

##### bindings/src/types/

| File | Description |
|------|-------------|
| `AbortReason.ts` | [GENERATED] TypeScript type definition for AbortReason enum. Auto-generated from Rust enum defining reasons for transaction or operation abortion. Used for error handling and transaction failure classification. |
| `AccessRule.ts` | [GENERATED] TypeScript type definition for AccessRule structure. Auto-generated from Rust struct defining access control rules for smart contracts and component permissions. Used for authorization and access management. |
| `Account.ts` | [GENERATED] TypeScript type definition for Account structure. Auto-generated from Rust struct defining wallet account data including balance, address, metadata, and account configuration. Core type for wallet account management. |
| `AllocatableAddressType.ts` | [GENERATED] TypeScript type definition for AllocatableAddressType enum. Auto-generated from Rust enum defining types of addresses that can be allocated in the Tari ecosystem. Used for address management and allocation. |
| `Amount.ts` | [GENERATED] TypeScript type definition for Amount structure. Auto-generated from Rust type representing monetary amounts in the Tari ecosystem. Handles precision, currency representation, and amount calculations. |
| `Arg.ts` | [GENERATED] TypeScript type definition for Arg structure. Auto-generated from Rust struct representing function arguments and parameters. Used for smart contract function calls and template interactions. |
| `ArgDef.ts` | [GENERATED] TypeScript type definition for ArgDef structure. Auto-generated from Rust struct defining function argument definitions including types, constraints, and metadata. Used for template function signatures. |
| `AuthHook.ts` | [GENERATED] TypeScript type definition for AuthHook structure. Auto-generated from Rust struct defining authentication hooks and authorization mechanisms. Used for access control and authentication flows. |
| `Block.ts` | [GENERATED] TypeScript type definition for Block structure. Auto-generated from Rust struct representing blockchain blocks including transactions, headers, and metadata. Core type for blockchain data representation. |
| `BlockHeader.ts` | [GENERATED] TypeScript type definition for BlockHeader structure. Auto-generated from Rust struct representing block headers with hash, timestamp, and consensus metadata. Used for block validation and chain navigation. |
| `BucketId.ts` | [GENERATED] TypeScript type definition for BucketId structure. Auto-generated from Rust type representing bucket identifiers in the Tari storage system. Used for data organization and storage management. |
| `Claims.ts` | [GENERATED] TypeScript type definition for Claims structure. Auto-generated from Rust struct representing cryptographic claims and proofs. Used for asset claims, fee claims, and proof verification. |
| `Command.ts` | [GENERATED] TypeScript type definition for Command structure. Auto-generated from Rust enum representing system commands and operations. Used for transaction instructions and system control. |
| `Committee.ts` | [GENERATED] TypeScript interface for blockchain committee structure. Exports: Committee<TAddr> interface representing a collection of committee members. Used by: consensus and validator node operations. Auto-generated from Rust types via ts-rs. |
| `CommitteeInfo.ts` | [GENERATED] TypeScript interface for committee configuration metadata. Exports: CommitteeInfo interface with shard count, group members, epoch info, and voting power. Used by: validator node consensus logic. Auto-generated from Rust types via ts-rs. |
| `CommitteeMember.ts` | [GENERATED] TypeScript interface for individual committee member representation. Exports: CommitteeMember<TAddr> interface. Used by: Committee interface and consensus operations. Auto-generated from Rust types via ts-rs. |
| `ComponentAccessRules.ts` | [GENERATED] TypeScript interface for smart contract component access control rules. Exports: ComponentAccessRules interface defining permission requirements. Used by: smart contract deployment and execution. Auto-generated from Rust types via ts-rs. |
| `ComponentAddress.ts` | [GENERATED] TypeScript type alias for smart contract component addressing. Exports: ComponentAddress as string type. Used by: smart contract operations and addressing throughout the system. Auto-generated from Rust types via ts-rs. |
| `ComponentBody.ts` | [GENERATED] TypeScript interface for smart contract component body definition. Exports: ComponentBody interface containing component implementation details. Used by: smart contract deployment and template system. Auto-generated from Rust types via ts-rs. |
| `ComponentCall.ts` | [GENERATED] TypeScript interface for smart contract component method invocation. Exports: ComponentCall interface defining method calls with arguments. Used by: transaction building and smart contract execution. Auto-generated from Rust types via ts-rs. |
| `ComponentHeader.ts` | [GENERATED] TypeScript interface for smart contract component metadata header. Exports: ComponentHeader interface with component identification and metadata. Used by: smart contract deployment and component management. Auto-generated from Rust types via ts-rs. |
| `ComponentKey.ts` | [GENERATED] TypeScript type for smart contract component unique identification key. Exports: ComponentKey type for component referencing. Used by: component lookup and smart contract operations. Auto-generated from Rust types via ts-rs. |
| `CompressedElgamalVerifiableBalance.ts` | [GENERATED] TypeScript interface for compressed ElGamal cryptographic balance proofs. Exports: CompressedElgamalVerifiableBalance interface for privacy-preserving balance verification. Used by: confidential transactions and privacy features. Auto-generated from Rust types via ts-rs. |
| `ConfidentialClaim.ts` | [GENERATED] TypeScript interface for confidential asset claim operations. Exports: ConfidentialClaim interface for privacy-preserving asset claims. Used by: confidential transactions and privacy protocols. Auto-generated from Rust types via ts-rs. |
| `ConfidentialOutput.ts` | [GENERATED] TypeScript interface for confidential transaction outputs with privacy features. Exports: ConfidentialOutput interface hiding transaction amounts. Used by: private transactions and confidential asset transfers. Auto-generated from Rust types via ts-rs. |
| `ConfidentialOutputStatement.ts` | [GENERATED] TypeScript interface for confidential output cryptographic statements. Exports: ConfidentialOutputStatement interface for zero-knowledge proofs. Used by: confidential transaction validation. Auto-generated from Rust types via ts-rs. |
| `ConfidentialStatement.ts` | [GENERATED] TypeScript interface for general confidential cryptographic statements. Exports: ConfidentialStatement interface for privacy proof declarations. Used by: confidential transaction protocols. Auto-generated from Rust types via ts-rs. |
| `ConfidentialTransferInputSelection.ts` | [GENERATED] TypeScript interface for selecting inputs in confidential transfers. Exports: ConfidentialTransferInputSelection interface for input selection algorithms. Used by: wallet transaction building and confidential transfers. Auto-generated from Rust types via ts-rs. |
| `ConfidentialWithdrawProof.ts` | [GENERATED] TypeScript interface for confidential withdrawal cryptographic proofs. Exports: ConfidentialWithdrawProof interface for privacy-preserving withdrawals. Used by: confidential asset withdrawal operations. Auto-generated from Rust types via ts-rs. |
| `Decision.ts` | [GENERATED] TypeScript type for consensus decision representation. Exports: Decision type for validator consensus outcomes. Used by: consensus protocol and validator node decision making. Auto-generated from Rust types via ts-rs. |
| `ElgamalVerifiableBalance.ts` | [GENERATED] TypeScript interface for ElGamal cryptographic balance verification. Exports: ElgamalVerifiableBalance interface for homomorphic balance proofs. Used by: confidential balance verification and privacy protocols. Auto-generated from Rust types via ts-rs. |
| `EntityId.ts` | [GENERATED] TypeScript type for unique entity identification. Exports: EntityId type for identifying blockchain entities. Used by: entity references throughout the system. Auto-generated from Rust types via ts-rs. |
| `Epoch.ts` | [GENERATED] TypeScript type for blockchain epoch numbering. Exports: Epoch type representing consensus time periods. Used by: consensus protocol, validator scheduling, and committee rotation. Auto-generated from Rust types via ts-rs. |
| `Era.ts` | [GENERATED] TypeScript type for blockchain era numbering. Exports: Era type representing major blockchain periods. Used by: consensus protocol, epoch management, and blockchain versioning. Auto-generated from Rust types via ts-rs. |
| `Event.ts` | [GENERATED] TypeScript type for blockchain event representation. Exports: Event type for system and smart contract events. Used by: event indexing, transaction logs, and application notifications. Auto-generated from Rust types via ts-rs. |
| `EvictNodeAtom.ts` | [GENERATED] TypeScript interface for node eviction atomic operations. Exports: EvictNodeAtom interface for consensus node removal. Used by: consensus protocol and validator node management. Auto-generated from Rust types via ts-rs. |
| `Evidence.ts` | [GENERATED] TypeScript interface for consensus evidence of misbehavior. Exports: Evidence interface for byzantine fault detection. Used by: consensus protocol and validator slashing. Auto-generated from Rust types via ts-rs. |
| `EvidenceInputLockData.ts` | [GENERATED] TypeScript interface for evidence-based input locking. Exports: EvidenceInputLockData interface for transaction input locks with evidence. Used by: consensus and transaction validation. Auto-generated from Rust types via ts-rs. |
| `ExecuteResult.ts` | [GENERATED] TypeScript interface for transaction execution results. Exports: ExecuteResult interface containing execution outcomes and state changes. Used by: transaction processing and smart contract execution. Auto-generated from Rust types via ts-rs. |
| `ExecutedTransaction.ts` | [GENERATED] TypeScript interface for completed transaction execution. Exports: ExecutedTransaction interface with execution results and fees. Used by: transaction history, block building, and fee calculation. Auto-generated from Rust types via ts-rs. |
| `ExtraData.ts` | [GENERATED] TypeScript interface for additional transaction metadata. Exports: ExtraData interface for optional transaction data. Used by: transaction construction and metadata storage. Auto-generated from Rust types via ts-rs. |
| `FeeBreakdown.ts` | [GENERATED] TypeScript interface for transaction fee breakdown by source. Exports: FeeBreakdown interface mapping fee sources to amounts. Used by: fee calculation, transaction cost analysis, and wallet fee display. Auto-generated from Rust types via ts-rs. |
| `FeeClaim.ts` | [GENERATED] TypeScript interface for fee claiming operations. Exports: FeeClaim interface for validator fee collection. Used by: validator rewards, fee distribution, and consensus rewards. Auto-generated from Rust types via ts-rs. |
| `FeeClaimAddress.ts` | [GENERATED] TypeScript type for fee claim addressing. Exports: FeeClaimAddress type for locating fee claim recipients. Used by: fee distribution and validator rewards. Auto-generated from Rust types via ts-rs. |
| `FeeCostBreakdown.ts` | [GENERATED] TypeScript interface for detailed fee cost analysis. Exports: FeeCostBreakdown interface with granular fee components. Used by: fee estimation, transaction cost planning, and wallet fee display. Auto-generated from Rust types via ts-rs. |
| `FeeReceipt.ts` | [GENERATED] TypeScript interface for fee payment receipts. Exports: FeeReceipt interface documenting fee payments. Used by: transaction receipts, audit trails, and fee tracking. Auto-generated from Rust types via ts-rs. |
| `FeeSource.ts` | [GENERATED] TypeScript enum for fee source classification. Exports: FeeSource type categorizing fee origins (execution, network, storage). Used by: fee breakdown, cost accounting, and fee analysis. Auto-generated from Rust types via ts-rs. |
| `FinalizeResult.ts` | [GENERATED] TypeScript interface for transaction finalization results. Exports: FinalizeResult interface with finalization outcomes. Used by: transaction completion, consensus finalization, and state commitment. Auto-generated from Rust types via ts-rs. |
| `ForeignProposalAtom.ts` | [GENERATED] TypeScript interface for foreign consensus proposal atoms. Exports: ForeignProposalAtom interface for cross-shard proposals. Used by: consensus protocol and inter-shard communication. Auto-generated from Rust types via ts-rs. |
| `FunctionDef.ts` | [GENERATED] TypeScript interface for smart contract function definitions. Exports: FunctionDef interface describing callable functions. Used by: smart contract templates and function invocation. Auto-generated from Rust types via ts-rs. |
| `IndexedValue.ts` | [GENERATED] TypeScript interface for indexed value storage. Exports: IndexedValue interface for searchable data structures. Used by: indexing services and data retrieval. Auto-generated from Rust types via ts-rs. |
| `IndexedWellKnownTypes.ts` | [GENERATED] TypeScript interface for indexed well-known type definitions. Exports: IndexedWellKnownTypes interface for standard type lookups. Used by: type system and smart contract compilation. Auto-generated from Rust types via ts-rs. |
| `Instruction.ts` | [GENERATED] TypeScript interface for blockchain instruction representation. Exports: Instruction interface for transaction operations. Used by: transaction building, execution engine, and smart contracts. Auto-generated from Rust types via ts-rs. |
| `InstructionArg.ts` | [GENERATED] TypeScript interface for instruction arguments. Exports: InstructionArg interface for operation parameters. Used by: instruction building, execution, and smart contract calls. Auto-generated from Rust types via ts-rs. |
| `InstructionResult.ts` | [GENERATED] TypeScript interface for instruction execution results. Exports: InstructionResult interface with execution outcomes. Used by: transaction processing and execution result handling. Auto-generated from Rust types via ts-rs. |
| `JrpcPermission.ts` | [GENERATED] TypeScript interface for JSON-RPC permission definitions. Exports: JrpcPermission interface for API access control. Used by: authentication system and API authorization. Auto-generated from Rust types via ts-rs. |
| `JrpcPermissions.ts` | [GENERATED] TypeScript interface for JSON-RPC permission collections. Exports: JrpcPermissions interface for grouped API permissions. Used by: authentication system and role-based access control. Auto-generated from Rust types via ts-rs. |
| `LeaderFee.ts` | [GENERATED] TypeScript interface for consensus leader fee structure. Exports: LeaderFee interface for leader reward calculations. Used by: consensus protocol and validator incentives. Auto-generated from Rust types via ts-rs. |
| `LockFlag.ts` | [GENERATED] TypeScript enum for transaction input lock flags. Exports: LockFlag type for input locking modes. Used by: transaction building and input management. Auto-generated from Rust types via ts-rs. |
| `LogEntry.ts` | [GENERATED] TypeScript interface for system log entries. Exports: LogEntry interface for structured logging. Used by: logging system and audit trails. Auto-generated from Rust types via ts-rs. |
| `LogLevel.ts` | [GENERATED] TypeScript enum for logging severity levels. Exports: LogLevel type for log filtering (error, warn, info, debug, trace). Used by: logging configuration and log filtering. Auto-generated from Rust types via ts-rs. |
| `Metadata.ts` | [GENERATED] TypeScript interface for general metadata storage. Exports: Metadata interface for key-value metadata. Used by: resources, components, and general data annotation. Auto-generated from Rust types via ts-rs. |
| `MintConfidentialOutputAtom.ts` | [GENERATED] TypeScript interface for confidential minting atomic operations. Exports: MintConfidentialOutputAtom interface for private asset creation. Used by: confidential asset minting and privacy protocols. Auto-generated from Rust types via ts-rs. |
| `NewAccountInfo.ts` | [GENERATED] TypeScript interface for new account creation data. Exports: NewAccountInfo interface with account setup parameters. Used by: account creation and wallet initialization. Auto-generated from Rust types via ts-rs. |
| `NodeHeight.ts` | [GENERATED] TypeScript type for blockchain node height tracking. Exports: NodeHeight type for block height numbers. Used by: consensus, synchronization, and blockchain indexing. Auto-generated from Rust types via ts-rs. |
| `NonFungible.ts` | [GENERATED] TypeScript interface for non-fungible token (NFT) representation. Exports: NonFungible interface with unique token data. Used by: NFT operations, collections, and metadata management. Auto-generated from Rust types via ts-rs. |
| `NonFungibleAddress.ts` | [GENERATED] TypeScript type for NFT addressing system. Exports: NonFungibleAddress type for unique NFT identification. Used by: NFT transactions, ownership tracking, and asset management. Auto-generated from Rust types via ts-rs. |
| `NonFungibleAddressContents.ts` | [GENERATED] TypeScript interface for NFT address content details. Exports: NonFungibleAddressContents interface with address components. Used by: NFT address parsing and construction. Auto-generated from Rust types via ts-rs. |
| `NonFungibleContainer.ts` | [GENERATED] TypeScript interface for NFT container structure. Exports: NonFungibleContainer interface for organizing NFT collections and metadata. Used by: NFT management and collection operations. Auto-generated from Rust types via ts-rs. |
| `NonFungibleId.ts` | [GENERATED] TypeScript type for unique NFT identification. Exports: NonFungibleId type for individual NFT identifier within collections. Used by: NFT tracking and ownership management. Auto-generated from Rust types via ts-rs. |
| `NonFungibleToken.ts` | [GENERATED] TypeScript interface for complete NFT representation. Exports: NonFungibleToken interface with vault ID, NFT ID, resource address, data, mutable data, and burn status. Used by: NFT operations and metadata management. Auto-generated from Rust types via ts-rs. |
| `NumPreshards.ts` | [GENERATED] TypeScript type for pre-shard count specification. Exports: NumPreshards type for defining shard quantity in consensus committees. Used by: consensus protocol and shard group management. Auto-generated from Rust types via ts-rs. |
| `Ordering.ts` | [GENERATED] TypeScript enum for sort ordering specification. Exports: Ordering type for ascending/descending sort order in queries. Used by: database queries and data sorting operations. Auto-generated from Rust types via ts-rs. |
| `OwnerRule.ts` | [GENERATED] TypeScript interface for ownership rule definitions. Exports: OwnerRule interface for smart contract ownership and access control. Used by: component ownership and authorization systems. Auto-generated from Rust types via ts-rs. |
| `PeerAddress.ts` | [GENERATED] TypeScript type for peer network addressing. Exports: PeerAddress type for P2P network peer identification. Used by: networking and peer discovery systems. Auto-generated from Rust types via ts-rs. |
| `ProofId.ts` | [GENERATED] TypeScript type for cryptographic proof identification. Exports: ProofId type for tracking zero-knowledge proofs and cryptographic evidence. Used by: proof systems and validation. Auto-generated from Rust types via ts-rs. |
| `ProposalCertificate.ts` | [GENERATED] TypeScript interface for consensus proposal certificates. Exports: ProposalCertificate interface for block proposal validation. Used by: HotStuff consensus and proposal verification. Auto-generated from Rust types via ts-rs. |
| `PublishedTemplate.ts` | [GENERATED] TypeScript interface for published smart contract templates. Exports: PublishedTemplate interface containing template metadata and deployment information. Used by: template registry and smart contract deployment. Auto-generated from Rust types via ts-rs. |
| `PublishedTemplateAddress.ts` | [GENERATED] TypeScript type alias for PublishedTemplateAddress as string. Auto-generated from Rust types via ts-rs. Represents addresses of published smart contract templates on the Tari network. Used by template management APIs and template deployment workflows. Part of the core type system for template addressing. |
| `QuorumCertificate.ts` | [GENERATED] TypeScript interface for QuorumCertificate representing consensus proofs in the Tari validator network. Auto-generated from Rust types via ts-rs. Contains block metadata (qc_id, block_id, header_hash, parent_id), consensus data (block_height, epoch, shard_group), validator signatures array, decision outcome, and share processing status. Used in consensus protocols, block validation, and quorum decision verification. Dependencies: Epoch, NodeHeight, ShardGroup, ValidatorSignature types. |
| `QuorumDecision.ts` | [GENERATED] TypeScript type for QuorumDecision representing validator consensus decisions. Auto-generated from Rust types via ts-rs. Used in consensus protocols to represent decision outcomes from validator quorums. Part of the consensus mechanism for transaction validation and block acceptance in the Tari network. |
| `RejectReason.ts` | [GENERATED] TypeScript type for RejectReason representing reasons why transactions or operations are rejected. Auto-generated from Rust types via ts-rs. Used in transaction validation, consensus protocols, and error handling to provide detailed rejection explanations. Part of the validation and error reporting system. |
| `RequireRule.ts` | [GENERATED] TypeScript type for RequireRule representing access control requirements. Auto-generated from Rust types via ts-rs. Used in resource access control systems and permission validation. Part of the authorization and access control framework for smart contracts and resources. |
| `Resource.ts` | [GENERATED] TypeScript interface for Resource type exported from Rust engine_types. Defines resource structure with resource_type, owner_rule, owner_key, access_rules, metadata, total_supply, view_key, and auth_hook fields. Auto-generated for frontend integration providing type-safe resource management and digital asset handling in web applications. |
| `ResourceAccessRules.ts` | [GENERATED] TypeScript type for ResourceAccessRules defining access control policies for digital resources. Auto-generated from Rust types via ts-rs. Used in resource permission systems to define who can access, modify, or transfer resources. Part of the access control and authorization framework for digital assets. |
| `ResourceAddress.ts` | [GENERATED] TypeScript type alias for ResourceAddress as string. Auto-generated from Rust types via ts-rs. Represents unique addresses of resources (tokens, NFTs) on the Tari network. Used throughout the system for resource identification, transactions, and asset management. Core addressing component for digital assets. |
| `ResourceContainer.ts` | [GENERATED] TypeScript type for ResourceContainer representing containers that hold digital resources. Auto-generated from Rust types via ts-rs. Used in asset management and transaction processing to represent grouped or contained resources. Part of the resource organization and management system. |
| `ResourceType.ts` | [GENERATED] TypeScript type for ResourceType enum representing different categories of digital resources. Auto-generated from Rust types via ts-rs. Used to categorize resources as fungible tokens, non-fungible tokens, or other resource types. Essential for resource classification and type-specific operations in the digital asset system. |
| `RestrictedAccessRule.ts` | [GENERATED] TypeScript type for RestrictedAccessRule representing restricted access control policies. Auto-generated from Rust types via ts-rs. Used in permission systems to define restricted access patterns for resources and operations. Part of the access control framework for enhanced security. |
| `RuleRequirement.ts` | [GENERATED] TypeScript type for RuleRequirement representing access rule requirements. Auto-generated from Rust types via ts-rs. Used in authorization systems to define what requirements must be met for access. Part of the rule-based access control and permission validation framework. |
| `Shard.ts` | [GENERATED] TypeScript type for Shard representing network sharding components. Auto-generated from Rust types via ts-rs. Used in the distributed validator network architecture for scalability and load distribution. Part of the sharding system for network partitioning and parallel processing. |
| `ShardGroup.ts` | [GENERATED] TypeScript type for ShardGroup representing groups of network shards. Auto-generated from Rust types via ts-rs. Used in validator network organization to manage shard collections for consensus and transaction processing. Core component of the distributed network architecture and consensus mechanisms. |
| `ShardGroupEvidence.ts` | [GENERATED] TypeScript type for ShardGroupEvidence representing proof of shard group operations. Auto-generated from Rust types via ts-rs. Used in consensus protocols to provide evidence of shard group activities and decisions. Part of the validation and audit trail system for distributed consensus. |
| `Substate.ts` | [GENERATED] TypeScript type for Substate representing individual state components in the Tari network. Auto-generated from Rust types via ts-rs. Core data structure for storing application state, resource data, and smart contract state. Used throughout transaction processing, state management, and consensus protocols. Fundamental building block of the Tari state system. |
| `SubstateAddress.ts` | [GENERATED] TypeScript type alias for SubstateAddress as string. Auto-generated from Rust types via ts-rs. Represents unique addresses of state components in the Tari network. Used for state identification, state queries, and transaction targeting. Essential for state management and referencing. |
| `SubstateDestroyed.ts` | [GENERATED] TypeScript type for SubstateDestroyed representing destroyed state records. Auto-generated from Rust types via ts-rs. Used in state lifecycle management to track destroyed substates for cleanup and garbage collection. Part of the state management and transaction finalization system. |
| `SubstateDiff.ts` | [GENERATED] TypeScript type for SubstateDiff representing state changes between versions. Auto-generated from Rust types via ts-rs. Used in transaction processing and state management to track changes to substates. Essential for state versioning, rollbacks, and transaction validation. |
| `SubstateId.ts` | [GENERATED] TypeScript type for SubstateId representing unique identifiers for state components. Auto-generated from Rust types via ts-rs. Used throughout the system for state identification, queries, and references. Core identifier type for the state management system and transaction processing. |
| `SubstateLockType.ts` | [GENERATED] TypeScript type for SubstateLockType enum representing state locking mechanisms. Auto-generated from Rust types via ts-rs. Used in transaction processing to define how substates are locked during operations. Part of the concurrency control and state management system for preventing conflicts. |
| `SubstateRecord.ts` | [GENERATED] TypeScript type for SubstateRecord representing complete state records with metadata. Auto-generated from Rust types via ts-rs. Used in state storage and retrieval operations to encapsulate substate data with versioning and metadata. Core data structure for state persistence and querying. |
| `SubstateRequirement.ts` | [GENERATED] TypeScript type for SubstateRequirement representing transaction requirements for state access. Auto-generated from Rust types via ts-rs. Used in transaction validation to specify required substates and access patterns. Part of the transaction dependency and validation system. |
| `SubstateType.ts` | [GENERATED] TypeScript type for SubstateType enum representing different categories of state components. Auto-generated from Rust types via ts-rs. Used for state categorization and type-specific operations throughout the system. Essential for state management, validation, and processing logic based on substate types. |
| `SubstateValue.ts` | [GENERATED] TypeScript type for SubstateValue representing the actual data content of state components. Auto-generated from Rust types via ts-rs. Used throughout the system to represent and transport state data. Core value container for all state-related operations and transactions. |
| `TemplateDef.ts` | [GENERATED] TypeScript type for TemplateDef representing smart contract template definitions. Auto-generated from Rust types via ts-rs. Used in template management and smart contract deployment to define template structure and capabilities. Core component of the template system for smart contract development. |
| `TemplateDefV1.ts` | [GENERATED] TypeScript type for TemplateDefV1 representing version 1 of smart contract template definitions. Auto-generated from Rust types via ts-rs. Used in template versioning and backward compatibility for smart contract definitions. Part of the versioned template system. |
| `TimeoutCertificate.ts` | [GENERATED] TypeScript type for TimeoutCertificate representing consensus timeout proof certificates. Auto-generated from Rust types via ts-rs. Used in consensus protocols to handle timeout scenarios and maintain network liveness. Part of the fault tolerance and timeout handling mechanisms in the validator network. |
| `Transaction.ts` | [GENERATED] TypeScript type for Transaction as versioned union type containing TransactionV1. Auto-generated from Rust types via ts-rs. Core transaction wrapper supporting version evolution. Used throughout the system for transaction processing, validation, and execution. Dependencies: TransactionV1. Central component of the transaction system. |
| `TransactionAtom.ts` | [GENERATED] TypeScript type for TransactionAtom representing atomic transaction components. Auto-generated from Rust types via ts-rs. Used in transaction composition and execution to ensure atomicity of operations. Part of the atomic transaction processing and rollback system. |
| `TransactionExecution.ts` | [GENERATED] TypeScript type for TransactionExecution representing transaction execution results and state. Auto-generated from Rust types via ts-rs. Used in transaction processing to track execution progress, results, and state changes. Core component of the transaction execution and monitoring system. |
| `TransactionPoolRecord.ts` | [GENERATED] TypeScript type for TransactionPoolRecord representing transactions in the mempool. Auto-generated from Rust types via ts-rs. Used in transaction pool management to track pending transactions with metadata and status. Part of the mempool and transaction scheduling system. |
| `TransactionPoolStage.ts` | [GENERATED] TypeScript type for TransactionPoolStage enum representing transaction processing stages. Auto-generated from Rust types via ts-rs. Used in transaction pool management to track transaction progression through validation, preparation, and execution stages. Essential for transaction lifecycle management. |
| `TransactionReceipt.ts` | [GENERATED] TypeScript type for TransactionReceipt representing transaction execution receipts with results and fees. Auto-generated from Rust types via ts-rs. Used to provide transaction execution confirmation, fee information, and result details. Core component for transaction result reporting and auditing. |
| `TransactionReceiptAddress.ts` | [GENERATED] TypeScript type alias for TransactionReceiptAddress as string. Auto-generated from Rust types via ts-rs. Represents unique addresses of transaction receipts for storage and retrieval. Used in receipt management and transaction result queries. |
| `TransactionResult.ts` | [GENERATED] TypeScript type definition for transaction execution results exported from Rust. Union type supporting Accept (full success with SubstateDiff), AcceptFeeRejectRest (fee-only success with SubstateDiff and RejectReason), and Reject (failure with RejectReason). Auto-generated from engine_types for frontend integration providing type-safe transaction result handling. |
| `TransactionSealSignature.ts` | [GENERATED] TypeScript type for TransactionSealSignature representing transaction finalization signatures. Auto-generated from Rust types via ts-rs. Used in transaction sealing and finalization to provide cryptographic proof of completion. Part of the transaction security and finalization system. |
| `TransactionSignature.ts` | [GENERATED] TypeScript type for TransactionSignature representing cryptographic signatures for transactions. Auto-generated from Rust types via ts-rs. Used throughout the system for transaction authentication and authorization. Essential for transaction security and identity verification. |
| `TransactionStatus.ts` | [GENERATED] TypeScript type for TransactionStatus enum representing transaction processing states. Auto-generated from Rust types via ts-rs. Used throughout the system to track transaction progression from submission to completion. Core status tracking for transaction lifecycle management and user interfaces. |
| `TransactionV1.ts` | [GENERATED] TypeScript type for TransactionV1 representing version 1 transaction structure. Auto-generated from Rust types via ts-rs. Contains transaction details, inputs, outputs, instructions, and signatures. Core transaction implementation used by the Transaction wrapper type. Essential for all transaction operations and processing. |
| `Type.ts` | [GENERATED] TypeScript type for Type representing type system definitions. Auto-generated from Rust types via ts-rs. Used in the type system for defining and validating data types throughout the Tari ecosystem. Core component of the type system and validation framework. |
| `UnclaimedConfidentialOutput.ts` | [GENERATED] TypeScript type for UnclaimedConfidentialOutput representing private transaction outputs awaiting claim. Auto-generated from Rust types via ts-rs. Used in confidential transaction processing for privacy-preserving value transfers. Part of the privacy and confidential transaction system. |
| `UnclaimedConfidentialOutputAddress.ts` | [GENERATED] TypeScript type alias for UnclaimedConfidentialOutputAddress as string. Auto-generated from Rust types via ts-rs. Represents addresses of unclaimed confidential outputs for privacy-preserving transactions. Used in confidential transaction management and output claiming. |
| `UnsealedTransactionV1.ts` | [GENERATED] TypeScript type for UnsealedTransactionV1 representing unsealed version 1 transactions. Auto-generated from Rust types via ts-rs. Used in transaction processing before final sealing and commitment. Part of the transaction construction and preparation pipeline. |
| `UnsignedTransaction.ts` | [GENERATED] TypeScript type for UnsignedTransaction representing transactions before signing. Auto-generated from Rust types via ts-rs. Used in transaction construction and multi-step signing workflows. Core component of the transaction preparation and signing process. |
| `UnsignedTransactionV1.ts` | [GENERATED] TypeScript type for UnsignedTransactionV1 representing version 1 unsigned transactions. Auto-generated from Rust types via ts-rs. Used in transaction construction before signature application. Part of the versioned transaction building and signing workflow. |
| `ValidatorFeePool.ts` | [GENERATED] TypeScript type for ValidatorFeePool representing fee collection pools for validators. Auto-generated from Rust types via ts-rs. Used in validator economics and fee distribution systems. Part of the validator incentive and fee management framework. |
| `ValidatorFeePoolAddress.ts` | [GENERATED] TypeScript type alias for ValidatorFeePoolAddress as string. Auto-generated from Rust types via ts-rs. Represents addresses of validator fee pools for fee collection and distribution. Used in validator economics and fee management operations. |
| `ValidatorFeeWithdrawal.ts` | [GENERATED] TypeScript type for ValidatorFeeWithdrawal representing validator fee withdrawal operations. Auto-generated from Rust types via ts-rs. Used in validator fee claiming and withdrawal processes. Part of the validator economics and incentive distribution system. |
| `ValidatorSignature.ts` | [GENERATED] TypeScript interface for ValidatorSignature containing public_key (string) and signature object with public_nonce and signature fields. Auto-generated from Rust types via ts-rs. Used throughout consensus protocols for validator authentication and block signing. Core cryptographic component for validator identity and consensus security. |
| `ValidatorSignatureBytes.ts` | [GENERATED] TypeScript type for ValidatorSignatureBytes representing validator signatures in byte format. Auto-generated from Rust types via ts-rs. Used for serialized validator signatures in consensus protocols and network communication. Part of the signature serialization and validation system. |
| `Vault.ts` | [GENERATED] TypeScript type for Vault representing secure storage containers for digital assets. Auto-generated from Rust types via ts-rs. Used in asset management for secure storage and access control of resources. Core component of the asset custody and vault management system. |
| `VaultId.ts` | [GENERATED] TypeScript type for VaultId representing unique identifiers for vault instances. Auto-generated from Rust types via ts-rs. Used throughout the system for vault identification and reference. Essential for vault management and asset custody operations. |
| `VersionedSubstateId.ts` | [GENERATED] TypeScript type for VersionedSubstateId representing versioned state identifiers. Auto-generated from Rust types via ts-rs. Used in state versioning and history tracking for substates. Core component of the state versioning and rollback system. |
| `VersionedSubstateIdLockIntent.ts` | [GENERATED] TypeScript type for VersionedSubstateIdLockIntent representing locking intentions for versioned state components. Auto-generated from Rust types via ts-rs. Used in concurrency control to specify locking requirements for versioned substates. Part of the transaction locking and conflict resolution system. |
| `ViewableBalanceProof.ts` | [GENERATED] TypeScript type for ViewableBalanceProof representing balance verification proofs for confidential transactions. Auto-generated from Rust types via ts-rs. Used in privacy-preserving balance verification and auditing. Part of the confidential transaction and privacy system. |
| `VotePower.ts` | [GENERATED] TypeScript type for VotePower representing voting strength in consensus mechanisms. Auto-generated from Rust types via ts-rs. Used in consensus protocols and governance systems to represent validator voting power. Core component of the consensus and governance frameworks. |
| `WalletTransaction.ts` | [GENERATED] TypeScript type for WalletTransaction representing wallet-specific transaction data. Auto-generated from Rust types via ts-rs. Used in wallet applications to track and manage transaction information from the wallet perspective. Part of the wallet transaction management and history system. |
| `WorkspaceOffsetId.ts` | [GENERATED] TypeScript type for WorkspaceOffsetId representing workspace offset identifiers. Auto-generated from Rust types via ts-rs. Used in workspace management and transaction ordering within workspaces. Part of the workspace organization and transaction sequencing system. |

###### bindings/src/types/base-node-client/

| File | Description |
|------|-------------|
| `BaseLayerValidatorNode.ts` | [GENERATED] TypeScript type for BaseLayerValidatorNode representing base layer validator node information. Auto-generated from Rust types via ts-rs. Used in base layer integration and validator node communication. Part of the base layer bridge and validator node management system for the base-node-client module. |

###### bindings/src/types/tari-indexer-client/

| File | Description |
|------|-------------|
| `GetNonFungibleCollectionsResponse.ts` | [GENERATED] TypeScript interface for GetNonFungibleCollectionsResponse containing collections array of [string, number] tuples. Auto-generated from Rust types via ts-rs. Used in indexer API responses to return NFT collection information with counts. Part of the tari-indexer-client module for NFT collection querying and management. |
| `GetNonFungibleCountRequest.ts` | [GENERATED] TypeScript type for GetNonFungibleCountRequest representing requests for NFT count queries. Auto-generated from Rust types via ts-rs. Used in indexer API requests to query non-fungible token counts. Part of the tari-indexer-client module for NFT statistics and counting operations. |
| `GetNonFungibleCountResponse.ts` | [GENERATED] TypeScript type for GetNonFungibleCountResponse representing NFT count query results. Auto-generated from Rust types via ts-rs. Used in indexer API responses to return non-fungible token count information. Part of the tari-indexer-client module for NFT statistics and analytics. |
| `GetNonFungiblesRequest.ts` | [GENERATED] TypeScript type for GetNonFungiblesRequest representing requests for NFT data queries. Auto-generated from Rust types via ts-rs. Used in indexer API requests to retrieve non-fungible token information and metadata. Part of the tari-indexer-client module for NFT data retrieval and browsing. |
| `GetNonFungiblesResponse.ts` | [GENERATED] TypeScript type for GetNonFungiblesResponse representing NFT data query results. Auto-generated from Rust types via ts-rs. Used in indexer API responses to return non-fungible token data and metadata. Part of the tari-indexer-client module for NFT data presentation and management. |
| `GetRelatedTransactionsRequest.ts` | [GENERATED] TypeScript type for GetRelatedTransactionsRequest representing requests for related transaction queries. Auto-generated from Rust types via ts-rs. Used in indexer API requests to find transactions related to specific addresses or resources. Part of the tari-indexer-client module for transaction relationship analysis. |
| `GetRelatedTransactionsResponse.ts` | [GENERATED] TypeScript type for GetRelatedTransactionsResponse representing related transaction query results. Auto-generated from Rust types via ts-rs. Used in indexer API responses to return related transaction information. Part of the tari-indexer-client module for transaction graph analysis and exploration. |
| `GetTemplateDefinitionRequest.ts` | [GENERATED] TypeScript type for GetTemplateDefinitionRequest representing requests for template definition queries. Auto-generated from Rust types via ts-rs. Used in indexer API requests to retrieve smart contract template definitions. Part of the tari-indexer-client module for template discovery and analysis. |
| `GetTemplateDefinitionResponse.ts` | [GENERATED] TypeScript type for GetTemplateDefinitionResponse representing template definition query results. Auto-generated from Rust types via ts-rs. Used in indexer API responses to return smart contract template definitions and metadata. Part of the tari-indexer-client module for template management and introspection. |
| `IndexerAddPeerRequest.ts` | [GENERATED] TypeScript type for IndexerAddPeerRequest representing requests to add network peers to the indexer. Auto-generated from Rust types via ts-rs. Used in indexer API requests for peer management and network connectivity. Part of the tari-indexer-client module for network administration and peer management. |
| `IndexerAddPeerResponse.ts` | [GENERATED] TypeScript type for IndexerAddPeerResponse representing results of peer addition operations. Auto-generated from Rust types via ts-rs. Used in indexer API responses for peer management operations. Part of the tari-indexer-client module for network administration and connectivity management. |
| `IndexerConnection.ts` | [GENERATED] TypeScript interface for IndexerConnection containing connection metadata including connection_id, peer_id, address, direction, age, optional ping_latency, and optional user_agent. Auto-generated from Rust types via ts-rs. Used to represent network connections in the indexer service. Dependencies: IndexerConnectionDirection. Part of the tari-indexer-client module for connection monitoring and management. |
| `IndexerConnectionDirection.ts` | [GENERATED] TypeScript type for IndexerConnectionDirection enum representing network connection directions. Auto-generated from Rust types via ts-rs. Used to categorize connections as inbound or outbound in the indexer network monitoring. Part of the tari-indexer-client module for connection direction classification. |
| `IndexerGetAllVnsRequest.ts` | [GENERATED] TypeScript type for IndexerGetAllVnsRequest representing requests for VNS (Virtual Name Service) data queries. Auto-generated from Rust types via ts-rs. Used in indexer API requests to retrieve all VNS entries. Part of the tari-indexer-client module for VNS management and name resolution services. |
| `IndexerGetAllVnsResponse.ts` | [GENERATED] TypeScript type for IndexerGetAllVnsResponse representing VNS data query results. Auto-generated from Rust types via ts-rs. Used in indexer API responses to return all Virtual Name Service entries. Part of the tari-indexer-client module for VNS browsing and name service management. |
| `IndexerGetCommsStatsResponse.ts` | [GENERATED] TypeScript type for IndexerGetCommsStatsResponse representing communication statistics from the indexer. Auto-generated from Rust types via ts-rs. Used in indexer API responses to return network communication metrics and statistics. Part of the tari-indexer-client module for network monitoring and performance analysis. |
| `IndexerGetConnectionsResponse.ts` | [GENERATED] TypeScript type for IndexerGetConnectionsResponse representing network connection information from the indexer. Auto-generated from Rust types via ts-rs. Used in indexer API responses to return current network connection details. Part of the tari-indexer-client module for connection monitoring and network status reporting. |
| `IndexerGetEpochManagerStatsResponse.ts` | [GENERATED] TypeScript type for IndexerGetEpochManagerStatsResponse representing epoch management statistics from the indexer. Auto-generated from Rust types via ts-rs. Used in indexer API responses to return epoch-related metrics and information. Part of the tari-indexer-client module for epoch monitoring and consensus tracking. |
| `IndexerGetIdentityResponse.ts` | [GENERATED] TypeScript type for IndexerGetIdentityResponse representing indexer identity information. Auto-generated from Rust types via ts-rs. Used in indexer API responses to return node identity and identification details. Part of the tari-indexer-client module for node identification and network discovery. |
| `IndexerGetSubstateRequest.ts` | [GENERATED] TypeScript type for IndexerGetSubstateRequest representing requests for substate data queries. Auto-generated from Rust types via ts-rs. Used in indexer API requests to retrieve specific substate information. Part of the tari-indexer-client module for state querying and substate inspection. |
| `IndexerGetSubstateResponse.ts` | [GENERATED] TypeScript type for IndexerGetSubstateResponse representing substate data query results. Auto-generated from Rust types via ts-rs. Used in indexer API responses to return specific substate information and metadata. Part of the tari-indexer-client module for state data retrieval and inspection. |
| `IndexerGetTransactionResultRequest.ts` | [GENERATED] TypeScript type for IndexerGetTransactionResultRequest representing requests for transaction result queries. Auto-generated from Rust types via ts-rs. Used in indexer API requests to retrieve transaction execution results. Part of the tari-indexer-client module for transaction result tracking and analysis. |
| `IndexerGetTransactionResultResponse.ts` | [GENERATED] TypeScript type for IndexerGetTransactionResultResponse representing transaction result query responses. Auto-generated from Rust types via ts-rs. Used in indexer API responses to return transaction execution results and status. Part of the tari-indexer-client module for transaction monitoring and result verification. |
| `IndexerReadyResponse.ts` | [GENERATED] TypeScript type for IndexerReadyResponse representing indexer readiness status. Auto-generated from Rust types via ts-rs. Used in indexer API responses to indicate service readiness and health status. Part of the tari-indexer-client module for health checking and service monitoring. |
| `IndexerSubmitTransactionRequest.ts` | [GENERATED] TypeScript type for IndexerSubmitTransactionRequest representing transaction submission requests. Auto-generated from Rust types via ts-rs. Used in indexer API requests to submit transactions for processing. Part of the tari-indexer-client module for transaction submission and network broadcasting. |
| `IndexerSubmitTransactionResponse.ts` | [GENERATED] TypeScript type for IndexerSubmitTransactionResponse representing transaction submission results. Auto-generated from Rust types via ts-rs. Used in indexer API responses to confirm transaction submission and provide submission status. Part of the tari-indexer-client module for transaction submission confirmation. |
| `IndexerTransactionFinalizedResult.ts` | [GENERATED] TypeScript type for IndexerTransactionFinalizedResult representing finalized transaction results from the indexer. Auto-generated from Rust types via ts-rs. Used in indexer responses to provide final transaction outcomes and status. Part of the tari-indexer-client module for transaction finalization and result tracking. |
| `InspectSubstateRequest.ts` | [GENERATED] TypeScript type for InspectSubstateRequest representing requests for detailed substate inspection. Auto-generated from Rust types via ts-rs. Used in indexer API requests to perform deep inspection of substate components. Part of the tari-indexer-client module for state debugging and detailed analysis. |
| `InspectSubstateResponse.ts` | [GENERATED] TypeScript type for InspectSubstateResponse representing detailed substate inspection results. Auto-generated from Rust types via ts-rs. Used in indexer API responses to return comprehensive substate analysis and metadata. Part of the tari-indexer-client module for state introspection and debugging. |
| `ListRecentTransactionsRequest.ts` | [GENERATED] TypeScript type for ListRecentTransactionsRequest representing requests for recent transaction lists. Auto-generated from Rust types via ts-rs. Used in indexer API requests to retrieve recently processed transactions. Part of the tari-indexer-client module for transaction history and activity monitoring. |
| `ListRecentTransactionsResponse.ts` | [GENERATED] TypeScript type for ListRecentTransactionsResponse representing recent transaction list results. Auto-generated from Rust types via ts-rs. Used in indexer API responses to return lists of recently processed transactions. Part of the tari-indexer-client module for transaction history browsing and activity feeds. |
| `ListSubstateItem.ts` | [GENERATED] TypeScript type for ListSubstateItem representing individual items in substate lists. Auto-generated from Rust types via ts-rs. Used in indexer responses to represent substate entries in list operations. Part of the tari-indexer-client module for substate enumeration and listing. |
| `ListSubstatesRequest.ts` | [GENERATED] TypeScript type for ListSubstatesRequest representing requests for substate listing operations. Auto-generated from Rust types via ts-rs. Used in indexer API requests to retrieve lists of substates with filtering options. Part of the tari-indexer-client module for substate discovery and browsing. |
| `ListSubstatesResponse.ts` | [GENERATED] TypeScript type for ListSubstatesResponse representing substate listing results. Auto-generated from Rust types via ts-rs. Used in indexer API responses to return filtered lists of substates. Part of the tari-indexer-client module for substate search and enumeration. |
| `ListTemplatesRequest.ts` | [GENERATED] TypeScript type for ListTemplatesRequest representing requests for template listing operations. Auto-generated from Rust types via ts-rs. Used in indexer API requests to retrieve lists of available smart contract templates. Part of the tari-indexer-client module for template discovery and browsing. |
| `ListTemplatesResponse.ts` | [GENERATED] TypeScript type for ListTemplatesResponse representing template listing results. Auto-generated from Rust types via ts-rs. Used in indexer API responses to return lists of available smart contract templates. Part of the tari-indexer-client module for template catalog and discovery. |
| `NonFungibleSubstate.ts` | [GENERATED] TypeScript type for NonFungibleSubstate representing NFT-specific substate data. Auto-generated from Rust types via ts-rs. Used to represent non-fungible token state information in the indexer. Part of the tari-indexer-client module for NFT state management and token tracking. |
| `TransactionEntry.ts` | [GENERATED] TypeScript type for TransactionEntry representing transaction entries in indexer lists and results. Auto-generated from Rust types via ts-rs. Used to represent transaction information in indexer responses and transaction listings. Part of the tari-indexer-client module for transaction data presentation and management. |

###### bindings/src/types/validator-node-client/

| File | Description |
|------|-------------|
| `DryRunTransactionFinalizeResult.ts` | [GENERATED] TypeScript interface for DryRunTransactionFinalizeResult containing decision, finalize, and optional fee_breakdown fields. Auto-generated from Rust types via ts-rs. Used in validator API responses for transaction dry-run operations to preview execution results without committing. Dependencies: FeeCostBreakdown, FinalizeResult. Part of the validator-node-client module for transaction testing and simulation. |
| `GetBaseLayerEpochChangesRequest.ts` | [GENERATED] TypeScript type for GetBaseLayerEpochChangesRequest representing requests for base layer epoch change information. Auto-generated from Rust types via ts-rs. Used in validator API requests to query base layer epoch transitions and changes. Part of the validator-node-client module for base layer integration and epoch management. |
| `GetBaseLayerEpochChangesResponse.ts` | [GENERATED] TypeScript type for GetBaseLayerEpochChangesResponse representing base layer epoch change query results. Auto-generated from Rust types via ts-rs. Used in validator API responses to return base layer epoch transition information. Part of the validator-node-client module for base layer integration and epoch tracking. |
| `GetBlockRequest.ts` | [GENERATED] TypeScript type for GetBlockRequest representing requests for specific block data. Auto-generated from Rust types via ts-rs. Used in validator API requests to retrieve individual block information by identifier. Part of the validator-node-client module for block querying and blockchain exploration. |
| `GetBlockResponse.ts` | [GENERATED] TypeScript type for GetBlockResponse representing block data query results. Auto-generated from Rust types via ts-rs. Used in validator API responses to return specific block information and metadata. Part of the validator-node-client module for block data retrieval and blockchain browsing. |
| `GetBlocksCountResponse.ts` | [GENERATED] TypeScript type for GetBlocksCountResponse representing block count query results. Auto-generated from Rust types via ts-rs. Used in validator API responses to return blockchain height and block count statistics. Part of the validator-node-client module for blockchain metrics and statistics. |
| `GetBlocksRequest.ts` | [GENERATED] TypeScript type for GetBlocksRequest representing requests for multiple block data. Auto-generated from Rust types via ts-rs. Used in validator API requests to retrieve ranges or lists of blocks with filtering options. Part of the validator-node-client module for bulk block querying and pagination. |
| `GetBlocksResponse.ts` | [GENERATED] TypeScript type for GetBlocksResponse representing multiple block data query results. Auto-generated from Rust types via ts-rs. Used in validator API responses to return lists of blocks with metadata. Part of the validator-node-client module for bulk block data retrieval and blockchain browsing. |
| `GetCommitteeRequest.ts` | [GENERATED] TypeScript type for GetCommitteeRequest representing requests for committee information. Auto-generated from Rust types via ts-rs. Used in validator API requests to retrieve validator committee data and membership. Part of the validator-node-client module for consensus committee management and monitoring. |
| `GetCommitteeResponse.ts` | [GENERATED] TypeScript type for GetCommitteeResponse representing committee information query results. Auto-generated from Rust types via ts-rs. Used in validator API responses to return validator committee data, membership, and shard assignments. Part of the validator-node-client module for consensus committee inspection and governance. |
| `GetConsensusStatusResponse.ts` | [GENERATED] TypeScript type for GetConsensusStatusResponse representing consensus status information. Auto-generated from Rust types via ts-rs. Used in validator API responses to return current consensus state, progress, and health metrics. Part of the validator-node-client module for consensus monitoring and status reporting. |
| `GetEpochManagerStatsResponse.ts` | [GENERATED] TypeScript type for GetEpochManagerStatsResponse representing epoch management statistics. Auto-generated from Rust types via ts-rs. Used in validator API responses to return epoch manager metrics, transitions, and performance data. Part of the validator-node-client module for epoch management monitoring and analysis. |
| `GetFilteredBlocksCountRequest.ts` | [GENERATED] TypeScript type for GetFilteredBlocksCountRequest representing requests for filtered block count queries. Auto-generated from Rust types via ts-rs. Used in validator API requests to count blocks with specific filtering criteria. Part of the validator-node-client module for blockchain statistics and analytics. |
| `GetMempoolStatsResponse.ts` | [GENERATED] TypeScript type for GetMempoolStatsResponse representing mempool statistics from the validator. Auto-generated from Rust types via ts-rs. Used in validator API responses to return transaction pool metrics, pending counts, and memory usage. Part of the validator-node-client module for mempool monitoring and performance analysis. |
| `GetNetworkCommitteeResponse.ts` | [GENERATED] TypeScript type for GetNetworkCommitteeResponse representing network-wide committee information. Auto-generated from Rust types via ts-rs. Used in validator API responses to return entire network committee structure and validator distribution. Part of the validator-node-client module for network governance and committee oversight. |
| `GetRecentTransactionsRequest.ts` | [GENERATED] TypeScript type for GetRecentTransactionsRequest representing requests for recent transaction data. Auto-generated from Rust types via ts-rs. Used in validator API requests to retrieve recently processed transactions with filtering options. Part of the validator-node-client module for transaction history and activity monitoring. |
| `GetRecentTransactionsResponse.ts` | [GENERATED] TypeScript type for GetRecentTransactionsResponse representing recent transaction query results. Auto-generated from Rust types via ts-rs. Used in validator API responses to return lists of recently processed transactions. Part of the validator-node-client module for transaction activity feeds and recent transaction browsing. |
| `GetShardKeyRequest.ts` | [GENERATED] TypeScript type for GetShardKeyRequest representing requests for shard key information. Auto-generated from Rust types via ts-rs. Used in validator API requests to retrieve shard key data for network partitioning. Part of the validator-node-client module for shard management and network topology queries. |
| `GetShardKeyResponse.ts` | [GENERATED] TypeScript type for GetShardKeyResponse representing shard key query results. Auto-generated from Rust types via ts-rs. Used in validator API responses to return shard key information and network partitioning data. Part of the validator-node-client module for shard topology and distributed network management. |
| `GetStateRequest.ts` | [GENERATED] TypeScript type for GetStateRequest representing requests for blockchain state queries. Auto-generated from Rust types via ts-rs. Used in validator API requests to retrieve specific blockchain state information. Part of the validator-node-client module for state inspection and blockchain data queries. |
| `GetStateResponse.ts` | [GENERATED] TypeScript type for GetStateResponse representing blockchain state query results. Auto-generated from Rust types via ts-rs. Used in validator API responses to return blockchain state data and metadata. Part of the validator-node-client module for state retrieval and blockchain inspection tools. |
| `GetSubstatesByTransactionRequest.ts` | [GENERATED] TypeScript type for GetSubstatesByTransactionRequest representing requests for transaction-related substates. Auto-generated from Rust types via ts-rs. Used in validator API requests to retrieve all substates affected by a specific transaction. Part of the validator-node-client module for transaction analysis and state change tracking. |
| `GetSubstatesByTransactionResponse.ts` | [GENERATED] TypeScript type for GetSubstatesByTransactionResponse representing transaction substate query results. Auto-generated from Rust types via ts-rs. Used in validator API responses to return lists of substates affected by transactions. Part of the validator-node-client module for transaction impact analysis and state auditing. |
| `GetTemplateRequest.ts` | [GENERATED] TypeScript type for GetTemplateRequest representing requests for smart contract template information. Auto-generated from Rust types via ts-rs. Used in validator API requests to retrieve specific template definitions and metadata. Part of the validator-node-client module for template discovery and smart contract management. |
| `GetTemplateResponse.ts` | [GENERATED] TypeScript type for GetTemplateResponse representing smart contract template query results. Auto-generated from Rust types via ts-rs. Used in validator API responses to return template definitions, ABI information, and metadata. Part of the validator-node-client module for template introspection and smart contract development tools. |
| `GetTemplatesRequest.ts` | [GENERATED] TypeScript type for GetTemplatesRequest representing requests for multiple template listings. Auto-generated from Rust types via ts-rs. Used in validator API requests to retrieve lists of available smart contract templates with filtering options. Part of the validator-node-client module for template browsing and catalog management. |
| `GetTemplatesResponse.ts` | [GENERATED] TypeScript type for GetTemplatesResponse representing multiple template listing results. Auto-generated from Rust types via ts-rs. Used in validator API responses to return lists of available smart contract templates with metadata. Part of the validator-node-client module for template discovery and smart contract ecosystem browsing. |
| `GetTransactionRequest.ts` | [GENERATED] TypeScript type for GetTransactionRequest representing requests for specific transaction data. Auto-generated from Rust types via ts-rs. Used in validator API requests to retrieve individual transaction information by hash or ID. Part of the validator-node-client module for transaction querying and blockchain exploration. |
| `GetTransactionResponse.ts` | [GENERATED] TypeScript type for GetTransactionResponse representing transaction query results. Auto-generated from Rust types via ts-rs. Used in validator API responses to return complete transaction data, status, and execution details. Part of the validator-node-client module for transaction inspection and analysis tools. |
| `GetTxPoolResponse.ts` | [GENERATED] TypeScript type for GetTxPoolResponse representing transaction pool status and contents. Auto-generated from Rust types via ts-rs. Used in validator API responses to return mempool information, pending transactions, and pool statistics. Part of the validator-node-client module for mempool monitoring and transaction pool management. |
| `LayerOneTransactionParams.ts` | [GENERATED] TypeScript type for LayerOneTransactionParams representing base layer transaction parameters. Auto-generated from Rust types via ts-rs. Used in validator operations for base layer integration and cross-layer transaction coordination. Part of the validator-node-client module for layer 1 bridge operations and base layer communication. |
| `ListBlocksRequest.ts` | [GENERATED] TypeScript type for ListBlocksRequest representing requests for block listing operations. Auto-generated from Rust types via ts-rs. Used in validator API requests to retrieve lists of blocks with pagination and filtering options. Part of the validator-node-client module for blockchain browsing and block discovery. |
| `ListBlocksResponse.ts` | [GENERATED] TypeScript type for ListBlocksResponse representing block listing results. Auto-generated from Rust types via ts-rs. Used in validator API responses to return paginated lists of blocks with metadata and summary information. Part of the validator-node-client module for blockchain navigation and block catalog browsing. |
| `PrepareLayerOneTransactionRequest.ts` | [GENERATED] TypeScript type for PrepareLayerOneTransactionRequest representing requests for base layer transaction preparation. Auto-generated from Rust types via ts-rs. Used in validator API requests to prepare transactions for base layer submission. Part of the validator-node-client module for cross-layer transaction coordination and layer 1 integration. |
| `PrepareLayerOneTransactionResponse.ts` | [GENERATED] TypeScript type for PrepareLayerOneTransactionResponse representing base layer transaction preparation results. Auto-generated from Rust types via ts-rs. Used in validator API responses to return prepared base layer transactions and coordination data. Part of the validator-node-client module for layer 1 bridge operations and cross-layer transaction management. |
| `SubstateStatus.ts` | [GENERATED] TypeScript type for SubstateStatus enum representing the lifecycle status of substates. Auto-generated from Rust types via ts-rs. Used throughout the validator system to track substate states (active, locked, destroyed, etc.). Part of the validator-node-client module for state management and lifecycle tracking. |
| `TemplateAbi.ts` | [GENERATED] TypeScript type for TemplateAbi representing smart contract template Application Binary Interface definitions. Auto-generated from Rust types via ts-rs. Used to define template function signatures, arguments, and return types for smart contract interaction. Part of the validator-node-client module for template introspection and contract development tools. |
| `TemplateMetadata.ts` | [GENERATED] TypeScript type for TemplateMetadata representing smart contract template metadata and descriptive information. Auto-generated from Rust types via ts-rs. Used to store template descriptions, version information, author details, and other template-related metadata. Part of the validator-node-client module for template discovery and smart contract ecosystem management. |
| `VNAddPeerRequest.ts` | [GENERATED] TypeScript type for VNAddPeerRequest representing validator node peer addition requests. Auto-generated from Rust types via ts-rs. Used in validator API requests to add new network peers for connectivity and network expansion. Part of the validator-node-client module for peer management and network topology control. |
| `VNAddPeerResponse.ts` | [GENERATED] TypeScript type for VNAddPeerResponse representing validator node peer addition results. Auto-generated from Rust types via ts-rs. Used in validator API responses to confirm peer addition operations and provide connection status. Part of the validator-node-client module for network management and peer connectivity confirmation. |
| `VNArgDef.ts` | [GENERATED] TypeScript type for VNArgDef representing validator node function argument definitions. Auto-generated from Rust types via ts-rs. Used to define argument specifications for smart contract functions and API methods. Part of the validator-node-client module for function signature definitions and type validation. |
| `VNCommitteeShardInfo.ts` | [GENERATED] TypeScript type for VNCommitteeShardInfo representing validator committee shard assignment information. Auto-generated from Rust types via ts-rs. Used to track validator assignments to specific shard groups and committee membership data. Part of the validator-node-client module for consensus committee management and shard coordination. |
| `VNConnection.ts` | [GENERATED] TypeScript type for VNConnection representing validator node network connection information. Auto-generated from Rust types via ts-rs. Used to track active network connections, peer relationships, and connection metadata for validator nodes. Part of the validator-node-client module for network monitoring and connection management. |
| `VNConnectionDirection.ts` | [GENERATED] TypeScript type for VNConnectionDirection enum representing validator node connection directions. Auto-generated from Rust types via ts-rs. Used to categorize network connections as inbound or outbound for validator nodes. Part of the validator-node-client module for connection direction classification and network topology analysis. |
| `VNFunctionDef.ts` | [GENERATED] TypeScript type for VNFunctionDef representing validator node function definitions. Auto-generated from Rust types via ts-rs. Used to define smart contract function signatures, parameters, and return types for template interfaces. Part of the validator-node-client module for function introspection and contract interaction. |
| `VNGetAllVnsRequest.ts` | [GENERATED] TypeScript type for VNGetAllVnsRequest representing validator node VNS (Virtual Name Service) query requests. Auto-generated from Rust types via ts-rs. Used in validator API requests to retrieve all VNS entries and name resolution data. Part of the validator-node-client module for name service management and resolution queries. |
| `VNGetAllVnsResponse.ts` | [GENERATED] TypeScript type for VNGetAllVnsResponse representing validator node VNS query results. Auto-generated from Rust types via ts-rs. Used in validator API responses to return all Virtual Name Service entries and name mapping data. Part of the validator-node-client module for VNS browsing and name resolution services. |
| `VNGetCommsStatsResponse.ts` | [GENERATED] TypeScript type for VNGetCommsStatsResponse representing validator node communication statistics. Auto-generated from Rust types via ts-rs. Used in validator API responses to return network communication metrics, bandwidth usage, and performance data. Part of the validator-node-client module for network performance monitoring and diagnostics. |
| `VNGetConnectionsResponse.ts` | [GENERATED] TypeScript type for VNGetConnectionsResponse representing validator node active connections information. Auto-generated from Rust types via ts-rs. Used in validator API responses to return current network connections, peer lists, and connection status data. Part of the validator-node-client module for network monitoring and connection management. |
| `VNGetIdentityResponse.ts` | [GENERATED] TypeScript type for VNGetIdentityResponse representing validator node identity information. Auto-generated from Rust types via ts-rs. Used in validator API responses to return node identity, public keys, and identification data. Part of the validator-node-client module for node identification and network discovery services. |
| `VNGetSubstateRequest.ts` | [GENERATED] TypeScript type for VNGetSubstateRequest representing validator node substate query requests. Auto-generated from Rust types via ts-rs. Used in validator API requests to retrieve specific substate information and state data. Part of the validator-node-client module for state querying and blockchain data access. |
| `VNGetSubstateResponse.ts` | [GENERATED] TypeScript type for VNGetSubstateResponse representing validator node substate query results. Auto-generated from Rust types via ts-rs. Used in validator API responses to return substate data, metadata, and state information. Part of the validator-node-client module for state inspection and blockchain data retrieval. |
| `VNGetTransactionResultRequest.ts` | [GENERATED] TypeScript type for VNGetTransactionResultRequest representing validator node transaction result query requests. Auto-generated from Rust types via ts-rs. Used in validator API requests to retrieve transaction execution results and outcomes. Part of the validator-node-client module for transaction result tracking and analysis. |
| `VNGetTransactionResultResponse.ts` | [GENERATED] TypeScript type for VNGetTransactionResultResponse representing validator node transaction result query results. Auto-generated from Rust types via ts-rs. Used in validator API responses to return transaction execution results, status, and detailed outcome information. Part of the validator-node-client module for transaction monitoring and result verification. |
| `VNGetValidatorFeesRequest.ts` | [GENERATED] TypeScript type for VNGetValidatorFeesRequest representing validator fee query requests. Auto-generated from Rust types via ts-rs. Used in validator API requests to retrieve validator fee information, earnings, and fee pool data. Part of the validator-node-client module for validator economics and fee management. |
| `VNGetValidatorFeesResponse.ts` | [GENERATED] TypeScript type for VNGetValidatorFeesResponse representing validator fee query results. Auto-generated from Rust types via ts-rs. Used in validator API responses to return validator fee data, earnings information, and fee pool statistics. Part of the validator-node-client module for validator economics monitoring and fee tracking. |
| `VNLogEntry.ts` | [GENERATED] TypeScript type for VNLogEntry representing validator node log entries. Auto-generated from Rust types via ts-rs. Used to represent structured log messages from validator nodes with timestamps, levels, and message content. Part of the validator-node-client module for log management and debugging tools. |
| `VNLogLevel.ts` | [GENERATED] TypeScript type for VNLogLevel enum representing validator node logging levels. Auto-generated from Rust types via ts-rs. Used to categorize log messages by severity (error, warn, info, debug, trace). Part of the validator-node-client module for log filtering and level-based logging control. |
| `VNSubmitTransactionRequest.ts` | [GENERATED] TypeScript type for VNSubmitTransactionRequest representing validator node transaction submission requests. Auto-generated from Rust types via ts-rs. Used in validator API requests to submit transactions for processing and validation. Part of the validator-node-client module for transaction submission and network broadcasting. |
| `VNSubmitTransactionResponse.ts` | [GENERATED] TypeScript type for VNSubmitTransactionResponse representing validator node transaction submission results. Auto-generated from Rust types via ts-rs. Used in validator API responses to confirm transaction submission and provide submission status. Part of the validator-node-client module for transaction submission confirmation and tracking. |
| `ValidatorFee.ts` | [GENERATED] TypeScript type for ValidatorFee representing individual validator fee entries. Auto-generated from Rust types via ts-rs. Used to represent validator earnings, fee amounts, and fee-related metadata. Part of the validator-node-client module for validator economics and fee calculation systems. |
| `ValidatorNode.ts` | [GENERATED] TypeScript type for ValidatorNode representing validator node information and metadata. Auto-generated from Rust types via ts-rs. Used to represent validator node details, status, and configuration data. Part of the validator-node-client module for validator registry and node management systems. |
| `ValidatorNodeChange.ts` | [GENERATED] TypeScript type for ValidatorNodeChange representing changes to validator node state or configuration. Auto-generated from Rust types via ts-rs. Used to track validator node updates, status changes, and configuration modifications. Part of the validator-node-client module for validator lifecycle management and change tracking. |

###### bindings/src/types/wallet-daemon-client/

| File | Description |
|------|-------------|
| `AccountGetDefaultRequest.ts` | [GENERATED] TypeScript type for AccountGetDefaultRequest representing requests to retrieve the default wallet account. Auto-generated from Rust types via ts-rs. Used in wallet API requests to get the currently selected default account information. Part of the wallet-daemon-client module for account management and default account operations. |
| `AccountGetRequest.ts` | [GENERATED] TypeScript type for AccountGetRequest representing requests to retrieve specific wallet account information. Auto-generated from Rust types via ts-rs. Used in wallet API requests to get account details by account ID or name. Part of the wallet-daemon-client module for account querying and account data retrieval. |
| `AccountGetResponse.ts` | [GENERATED] TypeScript type for AccountGetResponse representing wallet account query results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return specific account information, metadata, and configuration. Part of the wallet-daemon-client module for account data presentation and account management tools. |
| `AccountInfo.ts` | [GENERATED] TypeScript type for AccountInfo representing wallet account information and metadata. Auto-generated from Rust types via ts-rs. Used to store account details including name, address, balance information, and account settings. Part of the wallet-daemon-client module for account representation and account management systems. |
| `AccountOrKeyIndex.ts` | [GENERATED] TypeScript type for AccountOrKeyIndex representing account identification by either account reference or key index. Auto-generated from Rust types via ts-rs. Used in wallet operations to specify target accounts using flexible identification methods. Part of the wallet-daemon-client module for account addressing and identification systems. |
| `AccountSetDefaultRequest.ts` | [GENERATED] TypeScript type for AccountSetDefaultRequest representing requests to set the default wallet account. Auto-generated from Rust types via ts-rs. Used in wallet API requests to change the currently selected default account. Part of the wallet-daemon-client module for account preference management and default account configuration. |
| `AccountSetDefaultResponse.ts` | [GENERATED] TypeScript type for AccountSetDefaultResponse representing results of setting the default wallet account. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm default account changes. Part of the wallet-daemon-client module for account preference confirmation and default account management. |
| `AccountsCreateFreeTestCoinsRequest.ts` | [GENERATED] TypeScript type for AccountsCreateFreeTestCoinsRequest representing requests to create free test coins for accounts. Auto-generated from Rust types via ts-rs. Used in wallet API requests to generate test tokens for development and testing purposes. Part of the wallet-daemon-client module for test environment setup and development utilities. |
| `AccountsCreateFreeTestCoinsResponse.ts` | [GENERATED] TypeScript type for AccountsCreateFreeTestCoinsResponse representing results of test coin creation operations. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm test token generation and provide transaction details. Part of the wallet-daemon-client module for development testing and test token management. |
| `AccountsCreateRequest.ts` | [GENERATED] TypeScript type for AccountsCreateRequest representing requests to create new wallet accounts. Auto-generated from Rust types via ts-rs. Used in wallet API requests to create new accounts with specified names and configuration. Part of the wallet-daemon-client module for account creation and multi-account wallet management. |
| `AccountsCreateResponse.ts` | [GENERATED] TypeScript type for AccountsCreateResponse representing results of wallet account creation operations. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm account creation and provide new account details. Part of the wallet-daemon-client module for account creation confirmation and account registration. |
| `AccountsGetBalancesRequest.ts` | [GENERATED] TypeScript type for AccountsGetBalancesRequest representing requests to retrieve account balances. Auto-generated from Rust types via ts-rs. Used in wallet API requests to get balance information for one or more accounts. Part of the wallet-daemon-client module for balance querying and financial data retrieval. |
| `AccountsGetBalancesResponse.ts` | [GENERATED] TypeScript type for AccountsGetBalancesResponse representing account balance query results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return balance information for requested accounts including available, pending, and locked balances. Part of the wallet-daemon-client module for balance display and financial reporting. |
| `AccountsInvokeRequest.ts` | [GENERATED] TypeScript type for AccountsInvokeRequest representing requests to invoke smart contract functions from accounts. Auto-generated from Rust types via ts-rs. Used in wallet API requests to call smart contract methods and execute template functions. Part of the wallet-daemon-client module for smart contract interaction and method invocation. |
| `AccountsInvokeResponse.ts` | [GENERATED] TypeScript type for AccountsInvokeResponse representing smart contract invocation results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return smart contract execution results, transaction details, and method outputs. Part of the wallet-daemon-client module for contract interaction results and transaction confirmation. |
| `AccountsListRequest.ts` | [GENERATED] TypeScript type for AccountsListRequest representing requests to list wallet accounts. Auto-generated from Rust types via ts-rs. Used in wallet API requests to retrieve lists of all accounts with optional filtering and pagination. Part of the wallet-daemon-client module for account discovery and multi-account management. |
| `AccountsListResponse.ts` | [GENERATED] TypeScript type for AccountsListResponse representing account listing results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return lists of accounts with basic information and metadata. Part of the wallet-daemon-client module for account browsing and account management interfaces. |
| `AccountsTransferRequest.ts` | [GENERATED] TypeScript type for AccountsTransferRequest representing requests for account-to-account transfers. Auto-generated from Rust types via ts-rs. Used in wallet API requests to transfer assets between accounts within the same wallet or to external addresses. Part of the wallet-daemon-client module for asset transfer operations and transaction creation. |
| `AccountsTransferResponse.ts` | [GENERATED] TypeScript type for AccountsTransferResponse representing account transfer operation results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm transfer operations and provide transaction details. Part of the wallet-daemon-client module for transfer confirmation and transaction tracking. |
| `AuthGetAllJwtRequest.ts` | [GENERATED] TypeScript type for AuthGetAllJwtRequest representing requests to retrieve all active JWT tokens. Auto-generated from Rust types via ts-rs. Used in wallet API requests to get lists of all active authentication tokens for session management. Part of the wallet-daemon-client module for authentication token management and security auditing. |
| `AuthGetAllJwtResponse.ts` | [GENERATED] TypeScript type for AuthGetAllJwtResponse representing JWT token listing results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return lists of active authentication tokens with metadata and expiration information. Part of the wallet-daemon-client module for token session management and security monitoring. |
| `AuthGetMethodRequest.ts` | [GENERATED] TypeScript type for AuthGetMethodRequest representing requests to retrieve current authentication method configuration. Auto-generated from Rust types via ts-rs. Used in wallet API requests to get information about active authentication methods and settings. Part of the wallet-daemon-client module for authentication configuration and method management. |
| `AuthGetMethodResponse.ts` | [GENERATED] TypeScript type for AuthGetMethodResponse representing authentication method query results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return current authentication method settings and configuration data. Part of the wallet-daemon-client module for authentication method inspection and configuration management. |
| `AuthLoginAcceptRequest.ts` | [GENERATED] TypeScript type for AuthLoginAcceptRequest representing requests to accept login attempts. Auto-generated from Rust types via ts-rs. Used in wallet API requests to approve pending authentication requests and complete login flows. Part of the wallet-daemon-client module for multi-step authentication and login approval workflows. |
| `AuthLoginAcceptResponse.ts` | [GENERATED] TypeScript type for AuthLoginAcceptResponse representing login acceptance results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm login approval and provide authentication tokens. Part of the wallet-daemon-client module for login confirmation and token issuance. |
| `AuthLoginDenyRequest.ts` | [GENERATED] TypeScript type for AuthLoginDenyRequest representing requests to deny login attempts. Auto-generated from Rust types via ts-rs. Used in wallet API requests to reject pending authentication requests and deny access. Part of the wallet-daemon-client module for login rejection and security protection workflows. |
| `AuthLoginDenyResponse.ts` | [GENERATED] TypeScript type for AuthLoginDenyResponse representing login denial results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm login rejection and provide denial reasons. Part of the wallet-daemon-client module for access control and security audit logging. |
| `AuthLoginRequest.ts` | [GENERATED] TypeScript type for AuthLoginRequest representing authentication login requests. Auto-generated from Rust types via ts-rs. Used in wallet API requests to initiate authentication flows with credentials or authentication methods. Part of the wallet-daemon-client module for user authentication and login process initiation. |
| `AuthLoginResponse.ts` | [GENERATED] TypeScript type for AuthLoginResponse representing authentication login results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return login status, authentication tokens, and session information. Part of the wallet-daemon-client module for login completion and session establishment. |
| `AuthMethod.ts` | [GENERATED] TypeScript type for AuthMethod enum representing available authentication methods. Auto-generated from Rust types via ts-rs. Used to specify authentication modes including JWT, WebAuthn, password, and anonymous authentication. Part of the wallet-daemon-client module for authentication method selection and multi-modal authentication configuration. |
| `AuthRevokeTokenRequest.ts` | [GENERATED] TypeScript type for AuthRevokeTokenRequest representing requests to revoke authentication tokens. Auto-generated from Rust types via ts-rs. Used in wallet API requests to invalidate specific JWT tokens and terminate sessions. Part of the wallet-daemon-client module for token lifecycle management and security logout operations. |
| `AuthRevokeTokenResponse.ts` | [GENERATED] TypeScript type for AuthRevokeTokenResponse representing token revocation results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm token revocation and session termination. Part of the wallet-daemon-client module for logout confirmation and security audit logging. |
| `AuthoredTemplate.ts` | [GENERATED] TypeScript type for AuthoredTemplate representing smart contract templates created by the wallet user. Auto-generated from Rust types via ts-rs. Used to track templates authored by the current wallet with metadata and authorship information. Part of the wallet-daemon-client module for template authorship tracking and intellectual property management. |
| `BalanceEntry.ts` | [GENERATED] TypeScript type for BalanceEntry representing individual balance information for accounts and resources. Auto-generated from Rust types via ts-rs. Used to display balance details including available, pending, and locked amounts for different asset types. Part of the wallet-daemon-client module for balance reporting and financial data presentation. |
| `CallInstructionRequest.ts` | [GENERATED] TypeScript type for CallInstructionRequest representing requests to execute smart contract method calls. Auto-generated from Rust types via ts-rs. Used in wallet API requests to invoke specific smart contract functions with parameters. Part of the wallet-daemon-client module for smart contract interaction and method execution. |
| `ClaimBurnRequest.ts` | [GENERATED] TypeScript type for ClaimBurnRequest representing requests to claim burned assets from the base layer. Auto-generated from Rust types via ts-rs. Used in wallet API requests to claim assets that were burned on layer 1 for layer 2 issuance. Part of the wallet-daemon-client module for cross-layer asset claiming and burn-to-mint operations. |
| `ClaimBurnResponse.ts` | [GENERATED] TypeScript type for ClaimBurnResponse representing burn claim operation results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm burned asset claims and provide transaction details. Part of the wallet-daemon-client module for cross-layer asset transfer confirmation and burn claim tracking. |
| `ClaimValidatorFeesRequest.ts` | [GENERATED] TypeScript type for ClaimValidatorFeesRequest representing requests to claim validator fees and rewards. Auto-generated from Rust types via ts-rs. Used in wallet API requests to claim accumulated validator fees from fee pools. Part of the wallet-daemon-client module for validator economics and fee claiming operations. |
| `ClaimValidatorFeesResponse.ts` | [GENERATED] TypeScript type for ClaimValidatorFeesResponse representing validator fee claim results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm validator fee claims and provide transaction details. Part of the wallet-daemon-client module for validator reward processing and fee claim confirmation. |
| `ComponentAddressOrName.ts` | [GENERATED] TypeScript type for ComponentAddressOrName representing flexible component identification by address or name. Auto-generated from Rust types via ts-rs. Used in wallet operations to reference smart contract components using either direct addresses or human-readable names. Part of the wallet-daemon-client module for component addressing and name resolution. |
| `ConfidentialCreateOutputProofRequest.ts` | [GENERATED] TypeScript type for ConfidentialCreateOutputProofRequest representing requests to create confidential output proofs. Auto-generated from Rust types via ts-rs. Used in wallet API requests to generate cryptographic proofs for privacy-preserving transaction outputs. Part of the wallet-daemon-client module for confidential transaction construction and privacy features. |
| `ConfidentialCreateOutputProofResponse.ts` | [GENERATED] TypeScript type for ConfidentialCreateOutputProofResponse representing confidential output proof creation results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return generated cryptographic proofs and proof metadata. Part of the wallet-daemon-client module for privacy proof generation and confidential transaction support. |
| `ConfidentialTransferRequest.ts` | [GENERATED] TypeScript type for ConfidentialTransferRequest representing requests for privacy-preserving asset transfers. Auto-generated from Rust types via ts-rs. Used in wallet API requests to initiate confidential transactions with hidden amounts and recipients. Part of the wallet-daemon-client module for private transaction creation and confidential asset management. |
| `ConfidentialTransferResponse.ts` | [GENERATED] TypeScript type for ConfidentialTransferResponse representing confidential transfer operation results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm privacy-preserving transfers and provide transaction details. Part of the wallet-daemon-client module for confidential transaction confirmation and private transfer tracking. |
| `ConfidentialViewVaultBalanceRequest.ts` | [GENERATED] TypeScript type for ConfidentialViewVaultBalanceRequest representing requests to view confidential vault balances. Auto-generated from Rust types via ts-rs. Used in wallet API requests to view private asset balances in confidential vaults with view keys. Part of the wallet-daemon-client module for private balance viewing and confidential asset inspection. |
| `ConfidentialViewVaultBalanceResponse.ts` | [GENERATED] TypeScript type for ConfidentialViewVaultBalanceResponse representing confidential vault balance query results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return private balance information for confidential vaults. Part of the wallet-daemon-client module for private balance display and confidential asset reporting. |
| `FeePoolDetails.ts` | [GENERATED] TypeScript type for FeePoolDetails representing validator fee pool information and statistics. Auto-generated from Rust types via ts-rs. Used to display fee pool details including accumulated fees, distribution metrics, and pool status. Part of the wallet-daemon-client module for validator economics and fee pool monitoring. |
| `GetAccountNftRequest.ts` | [GENERATED] TypeScript type for GetAccountNftRequest representing requests to retrieve specific NFT information from accounts. Auto-generated from Rust types via ts-rs. Used in wallet API requests to get detailed NFT data and metadata for account-owned non-fungible tokens. Part of the wallet-daemon-client module for NFT inspection and digital collectible management. |
| `GetValidatorFeesRequest.ts` | [GENERATED] TypeScript type for GetValidatorFeesRequest representing requests to retrieve validator fee information. Auto-generated from Rust types via ts-rs. Used in wallet API requests to query validator fee data, earnings, and fee pool status. Part of the wallet-daemon-client module for validator economics monitoring and fee tracking. |
| `GetValidatorFeesResponse.ts` | [GENERATED] TypeScript type for GetValidatorFeesResponse representing validator fee query results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return validator fee information, earnings data, and fee pool statistics. Part of the wallet-daemon-client module for validator economics display and fee analytics. |
| `KeyBranch.ts` | [GENERATED] TypeScript type for KeyBranch enum representing hierarchical deterministic key derivation branches. Auto-generated from Rust types via ts-rs. Used in wallet key management to specify different key derivation paths and purposes. Part of the wallet-daemon-client module for HD wallet key derivation and cryptographic key organization. |
| `KeysCreateRequest.ts` | [GENERATED] TypeScript type for KeysCreateRequest representing requests to create new cryptographic keys. Auto-generated from Rust types via ts-rs. Used in wallet API requests to generate new key pairs for accounts, transactions, or other cryptographic operations. Part of the wallet-daemon-client module for key generation and cryptographic key management. |
| `KeysCreateResponse.ts` | [GENERATED] TypeScript type for KeysCreateResponse representing key creation results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return newly generated keys, addresses, and key metadata. Part of the wallet-daemon-client module for key generation confirmation and cryptographic key provisioning. |
| `KeysListRequest.ts` | [GENERATED] TypeScript type for KeysListRequest representing requests to list available cryptographic keys. Auto-generated from Rust types via ts-rs. Used in wallet API requests to retrieve lists of managed keys with filtering and pagination options. Part of the wallet-daemon-client module for key inventory management and key discovery. |
| `KeysListResponse.ts` | [GENERATED] TypeScript type for KeysListResponse representing key listing results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return lists of managed keys with metadata and usage information. Part of the wallet-daemon-client module for key browsing and cryptographic key inventory display. |
| `KeysSetActiveRequest.ts` | [GENERATED] TypeScript type for KeysSetActiveRequest representing requests to set active cryptographic keys. Auto-generated from Rust types via ts-rs. Used in wallet API requests to designate specific keys as active for transactions and operations. Part of the wallet-daemon-client module for key activation and active key management. |
| `KeysSetActiveResponse.ts` | [GENERATED] TypeScript type for KeysSetActiveResponse representing active key setting results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm key activation and provide active key status. Part of the wallet-daemon-client module for key activation confirmation and active key state management. |
| `ListAccountNftRequest.ts` | [GENERATED] TypeScript type for ListAccountNftRequest representing requests to list NFTs owned by accounts. Auto-generated from Rust types via ts-rs. Used in wallet API requests to retrieve lists of non-fungible tokens associated with specific accounts. Part of the wallet-daemon-client module for NFT collection browsing and digital asset inventory. |
| `ListAccountNftResponse.ts` | [GENERATED] TypeScript type for ListAccountNftResponse representing NFT listing results for accounts. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return lists of account-owned NFTs with metadata and ownership information. Part of the wallet-daemon-client module for NFT collection display and digital collectible management. |
| `MintAccountNftRequest.ts` | [GENERATED] TypeScript type for MintAccountNftRequest representing requests to mint NFTs for accounts. Auto-generated from Rust types via ts-rs. Used in wallet API requests to create new non-fungible tokens and assign them to specific accounts. Part of the wallet-daemon-client module for NFT creation and digital asset minting operations. |
| `MintAccountNftResponse.ts` | [GENERATED] TypeScript type for MintAccountNftResponse representing NFT minting results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm NFT creation and provide new token details and transaction information. Part of the wallet-daemon-client module for NFT minting confirmation and digital asset creation tracking. |
| `MintFaucetNftRequest.ts` | [GENERATED] TypeScript type for MintFaucetNftRequest representing requests to mint NFTs from faucet services. Auto-generated from Rust types via ts-rs. Used in wallet API requests to create test NFTs from development faucets for testing and development purposes. Part of the wallet-daemon-client module for test NFT generation and development utilities. |
| `MintFaucetNftResponse.ts` | [GENERATED] TypeScript type for MintFaucetNftResponse representing faucet NFT minting results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm test NFT creation from faucet services. Part of the wallet-daemon-client module for development NFT provisioning and test asset management. |
| `NetworkInfo.ts` | [GENERATED] TypeScript type for NetworkInfo representing network status and configuration information. Auto-generated from Rust types via ts-rs. Used to display current network details including network name, version, connectivity status, and network-specific settings. Part of the wallet-daemon-client module for network monitoring and connection status display. |
| `ProofsCancelRequest.ts` | [GENERATED] TypeScript type for ProofsCancelRequest representing requests to cancel pending cryptographic proofs. Auto-generated from Rust types via ts-rs. Used in wallet API requests to cancel in-progress proof generation or proof operations. Part of the wallet-daemon-client module for proof lifecycle management and cryptographic operation control. |
| `ProofsCancelResponse.ts` | [GENERATED] TypeScript type for ProofsCancelResponse representing proof cancellation results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm proof operation cancellation and cleanup status. Part of the wallet-daemon-client module for proof operation management and cryptographic process control. |
| `ProofsFinalizeRequest.ts` | [GENERATED] TypeScript type for ProofsFinalizeRequest representing requests to finalize cryptographic proofs. Auto-generated from Rust types via ts-rs. Used in wallet API requests to complete proof generation processes and commit proof data. Part of the wallet-daemon-client module for proof finalization and cryptographic proof completion. |
| `ProofsFinalizeResponse.ts` | [GENERATED] TypeScript type for ProofsFinalizeResponse representing proof finalization results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm proof completion and provide finalized proof data. Part of the wallet-daemon-client module for proof completion confirmation and cryptographic proof delivery. |
| `ProofsGenerateRequest.ts` | [GENERATED] TypeScript type for ProofsGenerateRequest representing requests to generate cryptographic proofs. Auto-generated from Rust types via ts-rs. Used in wallet API requests to initiate proof generation for confidential transactions and privacy operations. Part of the wallet-daemon-client module for proof generation initiation and cryptographic proof creation. |
| `ProofsGenerateResponse.ts` | [GENERATED] TypeScript type for ProofsGenerateResponse representing proof generation results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return generated cryptographic proofs and proof metadata. Part of the wallet-daemon-client module for proof generation confirmation and cryptographic proof delivery. |
| `PublishTemplateRequest.ts` | [GENERATED] TypeScript type for PublishTemplateRequest representing requests to publish smart contract templates. Auto-generated from Rust types via ts-rs. Used in wallet API requests to deploy new smart contract templates to the network. Part of the wallet-daemon-client module for template publishing and smart contract deployment operations. |
| `PublishTemplateResponse.ts` | [GENERATED] TypeScript type for PublishTemplateResponse representing template publishing results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm template deployment and provide published template details. Part of the wallet-daemon-client module for template publishing confirmation and deployment tracking. |
| `RevealFundsRequest.ts` | [GENERATED] TypeScript type for RevealFundsRequest representing requests to reveal confidential funds. Auto-generated from Rust types via ts-rs. Used in wallet API requests to reveal hidden assets in confidential transactions for auditing or transparency purposes. Part of the wallet-daemon-client module for confidential asset revelation and privacy control. |
| `RevealFundsResponse.ts` | [GENERATED] TypeScript type for RevealFundsResponse representing fund revelation results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to provide revealed fund information and transparency data. Part of the wallet-daemon-client module for confidential asset disclosure and privacy management. |
| `SettingsGetResponse.ts` | [GENERATED] TypeScript type for SettingsGetResponse representing wallet settings retrieval results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return current wallet configuration and user preferences. Part of the wallet-daemon-client module for settings management and configuration display. |
| `SettingsSetRequest.ts` | [GENERATED] TypeScript type for SettingsSetRequest representing requests to update wallet settings. Auto-generated from Rust types via ts-rs. Used in wallet API requests to modify wallet configuration and user preferences. Part of the wallet-daemon-client module for settings modification and configuration management. |
| `SettingsSetResponse.ts` | [GENERATED] TypeScript type for SettingsSetResponse representing wallet settings update results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm settings changes and provide updated configuration status. Part of the wallet-daemon-client module for settings update confirmation and configuration change tracking. |
| `SubstatesGetRequest.ts` | [GENERATED] TypeScript type for SubstatesGetRequest representing requests to retrieve specific substate information. Auto-generated from Rust types via ts-rs. Used in wallet API requests to get detailed substate data and state information. Part of the wallet-daemon-client module for state querying and blockchain data access. |
| `SubstatesGetResponse.ts` | [GENERATED] TypeScript type for SubstatesGetResponse representing substate retrieval results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return requested substate data and metadata. Part of the wallet-daemon-client module for state data display and blockchain state inspection. |
| `SubstatesListRequest.ts` | [GENERATED] TypeScript type for SubstatesListRequest representing requests to list substates with filtering options. Auto-generated from Rust types via ts-rs. Used in wallet API requests to retrieve lists of substates with pagination and filtering capabilities. Part of the wallet-daemon-client module for substate discovery and state browsing. |
| `SubstatesListResponse.ts` | [GENERATED] TypeScript type for SubstatesListResponse representing substate listing results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return filtered lists of substates with metadata and pagination information. Part of the wallet-daemon-client module for substate enumeration and state management interfaces. |
| `TemplatesGetRequest.ts` | [GENERATED] TypeScript type for TemplatesGetRequest representing requests to retrieve specific smart contract template information. Auto-generated from Rust types via ts-rs. Used in wallet API requests to get detailed template data and metadata. Part of the wallet-daemon-client module for template inspection and smart contract development tools. |
| `TemplatesGetResponse.ts` | [GENERATED] TypeScript type for TemplatesGetResponse representing template information query results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return detailed template data, ABI information, and template metadata. Part of the wallet-daemon-client module for template introspection and smart contract interaction. |
| `TemplatesListAuthoredRequest.ts` | [GENERATED] TypeScript type for TemplatesListAuthoredRequest representing requests to list user-authored smart contract templates. Auto-generated from Rust types via ts-rs. Used in wallet API requests to retrieve templates created by the current wallet user. Part of the wallet-daemon-client module for authorship tracking and intellectual property management. |
| `TemplatesListAuthoredResponse.ts` | [GENERATED] TypeScript type for TemplatesListAuthoredResponse representing authored template listing results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return lists of user-authored templates with authorship metadata. Part of the wallet-daemon-client module for template portfolio management and creator analytics. |
| `TransactionClaimBurnResponse.ts` | [GENERATED] TypeScript type for TransactionClaimBurnResponse representing transaction results for burn claim operations. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm burn claim transactions and provide cross-layer transfer details. Part of the wallet-daemon-client module for layer 1 bridge transaction tracking. |
| `TransactionGetAllRequest.ts` | [GENERATED] TypeScript type for TransactionGetAllRequest representing requests to retrieve all wallet transactions. Auto-generated from Rust types via ts-rs. Used in wallet API requests to get comprehensive transaction history with filtering and pagination options. Part of the wallet-daemon-client module for transaction history management and activity tracking. |
| `TransactionGetAllResponse.ts` | [GENERATED] TypeScript type for TransactionGetAllResponse representing comprehensive transaction listing results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return complete transaction history with metadata and status information. Part of the wallet-daemon-client module for transaction history display and activity reporting. |
| `TransactionGetRequest.ts` | [GENERATED] TypeScript type for TransactionGetRequest representing requests to retrieve specific transaction information. Auto-generated from Rust types via ts-rs. Used in wallet API requests to get detailed transaction data by transaction ID or hash. Part of the wallet-daemon-client module for transaction inspection and detailed transaction analysis. |
| `TransactionGetResponse.ts` | [GENERATED] TypeScript type for TransactionGetResponse representing specific transaction query results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return detailed transaction information including status, amounts, and execution details. Part of the wallet-daemon-client module for transaction detail display and transaction analysis tools. |
| `TransactionGetResultRequest.ts` | [GENERATED] TypeScript type for TransactionGetResultRequest representing requests to retrieve transaction execution results. Auto-generated from Rust types via ts-rs. Used in wallet API requests to get transaction outcome information and execution status. Part of the wallet-daemon-client module for transaction result tracking and execution monitoring. |
| `TransactionGetResultResponse.ts` | [GENERATED] TypeScript type for TransactionGetResultResponse representing transaction result query responses. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return transaction execution results, final status, and execution details. Part of the wallet-daemon-client module for transaction result display and execution outcome reporting. |
| `TransactionSubmitDryRunRequest.ts` | [GENERATED] TypeScript type for TransactionSubmitDryRunRequest representing requests for transaction dry-run simulations. Auto-generated from Rust types via ts-rs. Used in wallet API requests to simulate transaction execution without committing to the blockchain. Part of the wallet-daemon-client module for transaction testing and execution preview. |
| `TransactionSubmitDryRunResponse.ts` | [GENERATED] TypeScript type for TransactionSubmitDryRunResponse representing dry-run simulation results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return transaction simulation outcomes including estimated fees and execution results. Part of the wallet-daemon-client module for transaction preview and execution estimation. |
| `TransactionSubmitManifestRequest.ts` | [GENERATED] TypeScript type for TransactionSubmitManifestRequest representing requests to submit transaction manifests. Auto-generated from Rust types via ts-rs. Used in wallet API requests to submit complex multi-step transaction manifests for execution. Part of the wallet-daemon-client module for advanced transaction composition and manifest-based operations. |
| `TransactionSubmitManifestResponse.ts` | [GENERATED] TypeScript type for TransactionSubmitManifestResponse representing manifest submission results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm manifest transaction submission and provide execution tracking information. Part of the wallet-daemon-client module for complex transaction execution and manifest processing. |
| `TransactionSubmitRequest.ts` | [GENERATED] TypeScript type for TransactionSubmitRequest representing general transaction submission requests. Auto-generated from Rust types via ts-rs. Used in wallet API requests to submit transactions for blockchain execution and validation. Part of the wallet-daemon-client module for transaction submission and network broadcasting. |
| `TransactionSubmitResponse.ts` | [GENERATED] TypeScript type for TransactionSubmitResponse representing transaction submission results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm transaction submission and provide transaction hash and tracking information. Part of the wallet-daemon-client module for transaction submission confirmation and tracking. |
| `TransactionWaitResultRequest.ts` | [GENERATED] TypeScript type for TransactionWaitResultRequest representing requests to wait for transaction completion. Auto-generated from Rust types via ts-rs. Used in wallet API requests to wait for transaction finalization and get final execution results. Part of the wallet-daemon-client module for transaction completion monitoring and result waiting. |
| `TransactionWaitResultResponse.ts` | [GENERATED] TypeScript type for TransactionWaitResultResponse representing transaction completion wait results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return final transaction results after completion waiting. Part of the wallet-daemon-client module for transaction finalization confirmation and completion tracking. |
| `TransferNftRequest.ts` | [GENERATED] TypeScript type for TransferNftRequest representing requests to transfer NFTs between accounts or addresses. Auto-generated from Rust types via ts-rs. Used in wallet API requests to initiate non-fungible token transfers with recipient and token specifications. Part of the wallet-daemon-client module for NFT transfer operations and digital asset mobility. |
| `TransferNftResponse.ts` | [GENERATED] TypeScript type for TransferNftResponse representing NFT transfer operation results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm NFT transfers and provide transaction details. Part of the wallet-daemon-client module for NFT transfer confirmation and digital asset transaction tracking. |
| `WalletGetInfoRequest.ts` | [GENERATED] TypeScript type for WalletGetInfoRequest representing requests to retrieve wallet information and metadata. Auto-generated from Rust types via ts-rs. Used in wallet API requests to get wallet status, configuration, and general information. Part of the wallet-daemon-client module for wallet information display and status reporting. |
| `WalletGetInfoResponse.ts` | [GENERATED] TypeScript type for WalletGetInfoResponse representing wallet information query results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to return wallet status, version, configuration, and metadata. Part of the wallet-daemon-client module for wallet status display and information dashboard. |
| `WalletSubstateRecord.ts` | [GENERATED] TypeScript type for WalletSubstateRecord representing wallet-specific substate records with ownership and metadata. Auto-generated from Rust types via ts-rs. Used to track substates owned or managed by the wallet with wallet-specific information. Part of the wallet-daemon-client module for wallet state management and owned asset tracking. |
| `WebRtcStart.ts` | [GENERATED] TypeScript type for WebRtcStart representing WebRTC connection initiation data. Auto-generated from Rust types via ts-rs. Used in peer-to-peer WebRTC connection establishment for direct wallet communication. Part of the wallet-daemon-client module for decentralized communication and offline transaction capabilities. |
| `WebRtcStartRequest.ts` | [GENERATED] TypeScript type for WebRtcStartRequest representing requests to initiate WebRTC connections. Auto-generated from Rust types via ts-rs. Used in wallet API requests to start peer-to-peer connections for direct wallet communication. Part of the wallet-daemon-client module for decentralized networking and peer-to-peer wallet connectivity. |
| `WebRtcStartResponse.ts` | [GENERATED] TypeScript type for WebRtcStartResponse representing WebRTC connection initiation results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to provide WebRTC connection details and peer communication information. Part of the wallet-daemon-client module for P2P connection establishment and decentralized networking. |
| `WebauthnAlreadyRegisteredRequest.ts` | [GENERATED] TypeScript type for WebauthnAlreadyRegisteredRequest representing requests to check WebAuthn device registration status. Auto-generated from Rust types via ts-rs. Used in wallet API requests to verify if hardware security keys are already registered. Part of the wallet-daemon-client module for WebAuthn device management and hardware authentication. |
| `WebauthnAlreadyRegisteredResponse.ts` | [GENERATED] TypeScript type for WebauthnAlreadyRegisteredResponse representing WebAuthn device registration check results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to indicate hardware security key registration status. Part of the wallet-daemon-client module for WebAuthn device verification and hardware authentication management. |
| `WebauthnFinishAuthRequest.ts` | [GENERATED] TypeScript type for WebauthnFinishAuthRequest representing requests to complete WebAuthn authentication flows. Auto-generated from Rust types via ts-rs. Used in wallet API requests to finalize hardware security key authentication with signed challenges. Part of the wallet-daemon-client module for WebAuthn authentication completion and hardware key verification. |
| `WebauthnFinishRegisterRequest.ts` | [GENERATED] TypeScript type for WebauthnFinishRegisterRequest representing requests to complete WebAuthn device registration. Auto-generated from Rust types via ts-rs. Used in wallet API requests to finalize hardware security key registration with device credentials. Part of the wallet-daemon-client module for WebAuthn device enrollment and hardware key registration. |
| `WebauthnFinishRegisterResponse.ts` | [GENERATED] TypeScript type for WebauthnFinishRegisterResponse representing WebAuthn device registration completion results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to confirm hardware security key registration and provide device information. Part of the wallet-daemon-client module for WebAuthn registration confirmation and device management. |
| `WebauthnStartAuthRequest.ts` | [GENERATED] TypeScript type for WebauthnStartAuthRequest representing requests to initiate WebAuthn authentication flows. Auto-generated from Rust types via ts-rs. Used in wallet API requests to start hardware security key authentication challenges. Part of the wallet-daemon-client module for WebAuthn authentication initiation and hardware key login flows. |
| `WebauthnStartAuthResponse.ts` | [GENERATED] TypeScript type for WebauthnStartAuthResponse representing WebAuthn authentication initiation results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to provide authentication challenges and credential options for hardware security keys. Part of the wallet-daemon-client module for WebAuthn challenge delivery and authentication flow coordination. |
| `WebauthnStartRegisterRequest.ts` | [GENERATED] TypeScript type for WebauthnStartRegisterRequest representing requests to initiate WebAuthn device registration. Auto-generated from Rust types via ts-rs. Used in wallet API requests to start hardware security key registration processes. Part of the wallet-daemon-client module for WebAuthn device enrollment initiation and registration flow coordination. |
| `WebauthnStartRegisterResponse.ts` | [GENERATED] TypeScript type for WebauthnStartRegisterResponse representing WebAuthn device registration initiation results. Auto-generated from Rust types via ts-rs. Used in wallet API responses to provide registration challenges and options for hardware security key enrollment. Part of the wallet-daemon-client module for WebAuthn registration challenge delivery and enrollment flow management. |

### buildtools/

| File | Description |
|------|-------------|
| `build-notes.md` | Documentation file containing build system notes, procedures, and guidelines for the Tari project. Includes build process documentation, toolchain requirements, platform-specific instructions, and development workflow guidance. Essential reference for developers working on build system improvements and release processes. |
| `check_signatures.ps1` | PowerShell script for verifying cryptographic signatures and code signing validation. Used in the build process to ensure integrity of released binaries and packages. Performs signature verification for security compliance and tamper detection in the Tari build and release pipeline. |

#### buildtools/vagrant/

| File | Description |
|------|-------------|
| `.gitignore` | Git ignore configuration file for Vagrant development environment artifacts. Excludes Vagrant-specific files like VM images, logs, and temporary files from version control. Ensures clean repository state while using Vagrant for consistent development environments across different platforms. |
| `Vagrantfile` | Vagrant configuration file defining virtual machine setup for consistent development environments. Specifies VM configuration, provisioning scripts, network settings, and shared folders for Tari development. Enables standardized development environments across different host operating systems and developer machines. |

#### clients/base_node_client/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the base node client library. Defines dependencies, metadata, and build configuration for the Rust client library that interfaces with Tari base layer nodes. Includes gRPC dependencies, async runtime support, and cryptographic libraries for secure base layer communication. |

##### clients/base_node_client/src/

| File | Description |
|------|-------------|
| `error.rs` | Error handling module for the base node client library. Defines custom error types, error conversion implementations, and error propagation patterns for base layer communication failures. Includes network errors, gRPC failures, authentication errors, and base layer protocol violations. |
| `grpc.rs` | gRPC client implementation for base layer node communication. Provides async gRPC client methods for interacting with Tari base layer nodes including transaction submission, block queries, and network status operations. Handles connection management, request/response serialization, and gRPC-specific error handling. |
| `lib.rs` | Client library for communicating with Minotari base layer nodes via gRPC. Provides error handling, gRPC communication, type definitions, and traits for base node interaction. Essential component for Layer-1/Layer-2 bridge functionality, enabling Ootle network to interact with the Minotari blockchain for burn-claim operations and network coordination. |
| `traits.rs` | Trait definitions for base node client abstractions. Defines common interfaces for base layer operations, client behavior contracts, and pluggable client implementations. Enables testability, modularity, and alternative base layer client implementations while maintaining consistent API contracts. |
| `types.rs` | Type definitions and data structures for base node client operations. Defines request/response types, client configuration structures, and base layer specific data models. Includes serialization/deserialization implementations and type conversions for base layer protocol communication. |

##### clients/javascript/wallet_daemon_client/

| File | Description |
|------|-------------|
| `.gitignore` | Git ignore configuration for the JavaScript wallet daemon client library. Excludes Node.js artifacts, build outputs, dependency directories, and TypeScript compilation artifacts. Ensures clean repository state for the JavaScript/TypeScript client library distribution. |
| `.prettierrc` | Prettier code formatting configuration for the JavaScript wallet daemon client. Defines code style rules, formatting preferences, and consistency standards for TypeScript/JavaScript code in the client library. Ensures uniform code formatting across the JavaScript client codebase. |
| `README.md` | Documentation for the JavaScript wallet daemon client library. Provides installation instructions, usage examples, API documentation, and integration guidelines for developers using the TypeScript/JavaScript client to interact with Tari wallet daemon services. Includes authentication setup and common usage patterns. |
| `package.json` | NPM package configuration for the JavaScript wallet daemon client library. Defines package metadata, dependencies, build scripts, and distribution settings for the TypeScript client library. Includes development dependencies, testing configuration, and publishing information for NPM distribution. |
| `tsconfig.json` | TypeScript configuration for the JavaScript wallet daemon client library. Configures TypeScript compiler settings, module resolution, type checking, and output options for the client library build process. Optimized for library distribution and browser/Node.js compatibility. |
| `tsconfig.prod.json` | Production TypeScript configuration for the JavaScript wallet daemon client library. Extends base config with production optimizations including stricter type checking, minification settings, and distribution-ready compilation options for NPM package publishing. |

###### clients/javascript/wallet_daemon_client/src/

| File | Description |
|------|-------------|
| `index.ts` | Main entry point for the JavaScript wallet daemon client library. Exports primary client classes, configuration options, and public API interfaces for TypeScript/JavaScript applications. Provides centralized access to wallet daemon functionality including authentication, account management, and transaction operations. |

###### clients/javascript/wallet_daemon_client/src/transports/

| File | Description |
|------|-------------|
| `fetch.ts` | Fetch-based HTTP transport implementation for the JavaScript wallet daemon client. Provides JSON-RPC communication over HTTP using the Fetch API for browser and Node.js environments. Handles request/response serialization, error handling, and authentication token management for wallet daemon API calls. |
| `index.ts` | Transport abstraction module for the JavaScript wallet daemon client. Exports transport interfaces, implementations, and factory functions for different communication protocols. Provides pluggable transport layer supporting HTTP, WebSocket, and other communication methods for wallet daemon connectivity. |

#### clients/tari_indexer_client/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the Tari indexer client library. Defines dependencies, metadata, and build configuration for the Rust client library that interfaces with Tari indexer services. Includes JSON-RPC, GraphQL, async runtime, and serialization dependencies for comprehensive indexer API access. |

##### clients/tari_indexer_client/src/

| File | Description |
|------|-------------|
| `error.rs` | Error handling module for the Tari indexer client library. Defines custom error types, error conversion implementations, and error propagation patterns for indexer service communication failures. Includes network errors, JSON-RPC failures, GraphQL errors, and indexer-specific protocol violations. |
| `graphql_client.rs` | GraphQL client implementation for Tari indexer services. Provides async GraphQL client methods for complex blockchain data queries, transaction analysis, and state exploration. Handles GraphQL query construction, variable binding, response parsing, and subscription management for real-time indexer data access. |
| `json_rpc_client.rs` | JSON-RPC client implementation for Tari indexer services. Provides async JSON-RPC 2.0 client methods for indexer API operations including transaction queries, substate inspection, and blockchain data retrieval. Handles request/response serialization, method routing, and RPC-specific error handling. |
| `lib.rs` | Client library for Tari Indexer providing both GraphQL and JSON-RPC client implementations. Includes error handling, type definitions, and dual API access patterns. Enables applications to query blockchain data via indexer's GraphQL or JSON-RPC interfaces. |
| `types.rs` | Type definitions and data structures for Tari indexer client operations. Defines request/response types, client configuration structures, and indexer-specific data models. Includes serialization/deserialization implementations for both JSON-RPC and GraphQL communication protocols. |

#### clients/validator_node_client/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the validator node client library. Defines dependencies, metadata, and build configuration for the Rust client library that interfaces with Tari validator nodes. Includes JSON-RPC, async runtime, cryptographic, and consensus-related dependencies for validator communication. |

##### clients/validator_node_client/src/

| File | Description |
|------|-------------|
| `error.rs` | Error handling module for the validator node client library. Defines custom error types, error conversion implementations, and error propagation patterns for validator node communication failures. Includes network errors, consensus errors, JSON-RPC failures, and validator-specific protocol violations. |
| `lib.rs` | HTTP JSON-RPC client library for communicating with validator nodes. Provides ValidatorNodeClient for remote procedure calls, request/response types, and error handling. Enables external applications and services to interact with validator node APIs for queries and transactions. |
| `types.rs` | Type definitions and data structures for validator node client operations. Defines request/response types, client configuration structures, and validator-specific data models. Includes serialization/deserialization implementations and type conversions for consensus protocol communication and validator node interactions. |

#### clients/wallet_daemon_client/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the wallet daemon client library. Defines dependencies, metadata, and build configuration for the Rust client library that interfaces with Tari wallet daemon services. Includes JSON-RPC, async runtime, cryptographic, and wallet-specific dependencies for comprehensive wallet API access. |

##### clients/wallet_daemon_client/src/

| File | Description |
|------|-------------|
| `component_address.rs` | Component address handling module for the wallet daemon client. Implements component address parsing, validation, and conversion utilities for smart contract component references. Provides type-safe component addressing with support for both direct addresses and name-based resolution in wallet operations. |
| `error.rs` | Error handling module for the wallet daemon client library. Defines custom error types, error conversion implementations, and error propagation patterns for wallet daemon communication failures. Includes network errors, authentication errors, JSON-RPC failures, and wallet-specific operation errors. |
| `lib.rs` | Client library for communicating with Tari wallet daemon. Provides component address management, error handling, permissions, serialization utilities, and comprehensive wallet API methods. Enables applications to perform wallet operations like account management, transaction creation, balance queries, and authentication through standardized client interfaces. |
| `permissions.rs` | Permission management module for the wallet daemon client. Implements permission checking, access control validation, and authorization utilities for wallet operations. Handles role-based access control, operation permissions, and security policy enforcement for wallet daemon API access. |
| `serialize.rs` | Serialization utilities module for the wallet daemon client. Implements custom serialization/deserialization logic, JSON-RPC message formatting, and data type conversions for wallet daemon communication. Handles complex type transformations and ensures compatibility between client and server data formats. |
| `types.rs` | Type definitions and data structures for wallet daemon client operations. Defines request/response types, client configuration structures, and wallet-specific data models. Includes serialization/deserialization implementations and type conversions for wallet daemon protocol communication and account management operations. |

### config/

| File | Description |
|------|-------------|
| `logs.yml` | Log4rs logging configuration defining console and file-based logging with rotation policies. Configures structured logging for different components with separate log files for network traffic, application messages, and error handling. Production-ready logging setup for blockchain operations. |

#### crates/common_types/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the common types crate. Defines dependencies, metadata, and build configuration for shared type definitions used across the Tari ecosystem. Includes serialization, cryptographic, and consensus-related dependencies for fundamental blockchain data structures and common utilities. |

##### crates/common_types/src/

| File | Description |
|------|-------------|
| `borsh.rs` | Borsh serialization implementations for common types. Provides efficient binary serialization using the Borsh format for blockchain data structures including transactions, addresses, and consensus types. Optimized for performance and deterministic serialization in consensus-critical operations. |
| `bytes.rs` | Byte array utilities and abstractions for common types. Provides byte manipulation functions, safe byte array handling, and conversion utilities for binary data processing. Includes fixed-size byte arrays, byte vector operations, and endianness-aware byte conversions for blockchain data structures. |
| `committee.rs` | Committee management types and utilities for validator governance. Defines committee structures, member management, and committee selection algorithms for consensus operations. Includes committee composition, validator assignment, and governance protocols for distributed validator network coordination. |
| `consensus_constants.rs` | Consensus protocol constants and configuration parameters. Defines immutable consensus rules, protocol limits, timing parameters, and network-specific constants for blockchain operation. Includes block time intervals, transaction limits, fee parameters, and consensus protocol configuration values. |
| `crypto.rs` | Cryptographic primitives and utilities for common types. Provides cryptographic functions, key management, signature operations, and hash functions used throughout the Tari ecosystem. Includes public/private key handling, digital signatures, and cryptographic proof utilities for blockchain security. |
| `displayable.rs` | Display formatting traits and implementations for common types. Provides human-readable formatting for blockchain data structures including addresses, hashes, amounts, and transaction details. Includes pretty-printing utilities and developer-friendly display formats for debugging and user interfaces. |
| `epoch.rs` | Epoch management types and utilities for consensus protocol. Defines epoch structures, epoch transitions, and epoch-based validator set changes. Includes epoch numbering, epoch boundaries, and epoch-specific consensus parameters for time-based protocol upgrades and validator rotation. |
| `era.rs` | Era management types for long-term protocol evolution. Defines era structures, era transitions, and era-based protocol upgrades for major blockchain evolution phases. Includes era numbering, backward compatibility handling, and protocol versioning for network upgrades and feature rollouts. |
| `extra_data.rs` | Extra data handling utilities for extensible blockchain structures. Provides mechanisms for attaching additional metadata, custom fields, and extensible data to blockchain transactions and blocks. Includes forward compatibility, versioning, and optional data handling for protocol extensibility. |
| `fee_pool.rs` | Fee pool management types and utilities for validator economics. Defines fee collection, distribution, and pool management structures for validator incentives. Includes fee accumulation, validator reward calculation, and fee distribution algorithms for sustainable validator economics. |
| `hashing.rs` | Hashing algorithms and utilities for blockchain operations. Provides cryptographic hash functions, Merkle tree operations, and hash-based data structures used throughout the Tari protocol. Includes content addressing, integrity verification, and deterministic hashing for consensus-critical operations. |
| `layer_one_transaction.rs` | Layer 1 transaction types and utilities for base layer integration. Defines structures and operations for Tari base layer transactions, cross-layer communication, and layer 1 bridge functionality. Includes base layer transaction formatting, validation, and integration utilities for multi-layer blockchain operations. |
| `lib.rs` | Common type definitions shared across Tari Ootle components. Provides fundamental blockchain types including epochs, committees, shards, node addressing, substate addressing, cryptographic utilities, and validator metadata. Foundation types for consensus and networking layers. |
| `lock_intent.rs` | Lock intent types and utilities for transaction concurrency control. Defines locking mechanisms, intent declarations, and conflict resolution for concurrent transaction processing. Includes read/write locks, intent validation, and deadlock prevention for safe parallel transaction execution. |
| `node_addressable.rs` | Node addressing utilities and traits for network node identification. Provides addressable node abstractions, node ID management, and network addressing for distributed validator nodes. Includes node discovery, addressing schemes, and network topology management for peer-to-peer communication. |
| `node_height.rs` | Node height types and utilities for blockchain height tracking. Defines block height structures, height comparison operations, and height-based validation for consensus protocols. Includes height arithmetic, height intervals, and chain height management for blockchain synchronization. |
| `num_preshards.rs` | Pre-shard configuration types and utilities for network sharding. Defines pre-shard count management, shard allocation parameters, and sharding configuration for scalable validator networks. Includes shard capacity planning, pre-shard validation, and dynamic shard allocation for network scalability. |
| `optional.rs` | Optional value utilities and extensions for common types. Provides enhanced Option handling, optional field management, and nullable value utilities for blockchain data structures. Includes optional value validation, default handling, and type-safe optional field operations. |
| `peer_address.rs` | Peer address types and utilities for network peer management. Defines peer addressing structures, address validation, and peer discovery mechanisms for distributed network operations. Includes peer identification, address resolution, and network peer management for validator communication. |
| `shard.rs` | Shard types and utilities for network sharding implementation. Defines shard structures, shard identification, and shard management for scalable validator networks. Includes shard assignment, shard boundaries, and cross-shard communication protocols for distributed transaction processing. |
| `shard_group.rs` | Shard group type implementation for representing inclusive ranges of shards in the Tari blockchain. Exports ShardGroup struct with creation methods (new, new_checked, new_unchecked), range operations (contains, overlaps_shard_group, intersection), iterator support, and conversion utilities for shards and substate addresses. Used by consensus system for organizing validators into committees and mapping blockchain state to shard ranges. Includes comprehensive encoding/decoding for network transmission and string parsing for configuration. Key for sharded consensus protocol allowing distributed validation across multiple shard groups. |
| `substate_address.rs` | Core substate addressing system combining ObjectKey and version for unique blockchain state identification. Exports SubstateAddress struct with creation from SubstateId/ObjectKey, conversion to/from U256, byte serialization, and shard mapping for distributed consensus. Provides ToSubstateAddress trait for type conversions. Key methods include to_shard() for consensus routing, to_shard_group() for committee assignment, and comprehensive address range calculations. Used throughout consensus, storage, and state management for addressing substates in sharded blockchain architecture. |
| `substate_type.rs` | Substate type enumeration for categorizing blockchain state entries. Exports SubstateType enum with variants: Component, Resource, Vault, UnclaimedConfidentialOutput, NonFungible, TransactionReceipt, ValidatorFeePool, Template. Provides type checking with matches() method, string prefixes for identification, and conversions from SubstateValue, SubstateId, and Substate. Used by indexing, querying, and type validation systems to categorize and filter different kinds of blockchain state objects across the Tari ecosystem. |
| `uint.rs` | 256-bit unsigned integer type aliases and constants for blockchain operations. Re-exports U256 from ethnum crate and provides constants U256_ZERO and U256_ONE. Used throughout the codebase for large integer arithmetic in cryptographic operations, address calculations, shard mapping, and state tree operations. Essential for handling hash values, addresses, and large numeric computations in the distributed blockchain system. |
| `validator_metadata.rs` | Validator node metadata structure for network registration and identification. Exports ValidatorMetadata struct containing RistrettoPublicKeyBytes public key, SubstateAddress shard key, and SchnorrSignatureBytes signature for validator authentication. Includes vn_node_hash() function for generating network-specific validator hashes using TariBaseLayerHasher32. Used by validator registration, committee formation, and network consensus for identifying and authenticating validator nodes in the distributed consensus network. |
| `versioned_substate_id.rs` | Versioned substate identification system for blockchain state management with optional and required versioning. Exports SubstateRequirement (optional version), VersionedSubstateId (required version), and reference variants for memory-efficient operations. Provides conversion between versioned/unversioned forms, shard mapping, substate address generation, and string parsing. Used by transaction processing, consensus, and state management for tracking substate versions and dependencies throughout the blockchain lifecycle. |
| `vote_power.rs` | Vote power type for consensus voting weight representation. Exports VotePower newtype wrapper around u64 with arithmetic operations (add, sub, mul, div), checked variants for overflow protection, and utility methods (zero, max, is_zero). Used by consensus protocols for validator voting weight, quorum calculations, and Byzantine fault tolerance thresholds. Essential for weighted voting in the distributed consensus mechanism where validators have different voting power based on stake or other factors. |

###### crates/common_types/src/services/

| File | Description |
|------|-------------|
| `mod.rs` | Services module for common service abstractions and interfaces. Provides service trait definitions, service lifecycle management, and common service patterns used across the Tari ecosystem. Includes service registration, dependency injection, and service composition utilities. |
| `template_provider.rs` | Template provider service abstractions for smart contract template management. Defines service interfaces for template storage, retrieval, and management across the Tari ecosystem. Includes template caching, template validation, and template distribution services for smart contract infrastructure. |

#### crates/consensus/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for tari_consensus crate providing distributed consensus protocol implementation. Dependencies include core Tari blockchain types, storage, transaction processing, epoch management, state tree, and cryptographic libraries. External dependencies for async operations (tokio), data structures (indexmap), error handling (thiserror), and serialization (serde). Used to build the HotStuff consensus protocol implementation for the Tari Layer 2 blockchain with sharded validator network support. |

##### crates/consensus/src/

| File | Description |
|------|-------------|
| `consensus_constants.rs` | Consensus configuration constants for the Tari Ootle network. Defines ConsensusConstants struct with parameters including base layer confirmations (3), committee size (7), pacemaker block time (10s), node suspension/eviction thresholds (5/10 missed proposals), maximum commands per block (500), fee burning rate (5%), epochs per era (10), and template binary size limits (5MB). Provides network-specific configurations with devnet implementation and Network trait integration. Critical for consensus behavior and network operation parameters. |
| `lib.rs` | Consensus library module providing HotStuff consensus protocol implementation for distributed validator network. Exports consensus constants, HotStuff protocol implementation, consensus messages, tracing utilities, traits for consensus behavior, and validation logic. Core component for achieving Byzantine fault tolerant consensus among validator nodes with proper message handling and validation. |
| `tracing.rs` | Performance tracing utilities for consensus operations. Provides TraceTimer struct for measuring execution time with configurable log levels, excessive duration warnings (default 1s threshold), iteration counting for batch operations, and automatic logging on drop. Supports info/debug convenience constructors and detailed performance analysis with average timing calculations. Essential for monitoring consensus performance bottlenecks and optimizing HotStuff protocol operations. |

###### crates/consensus/src/hotstuff/

| File | Description |
|------|-------------|
| `block_change_set.rs` | Block change set management for HotStuff consensus with memory-optimized change tracking. Exports BlockDecision for committed blocks and quorum decisions, ProposedBlockChangeSet for tracking all changes during block proposal/validation. Manages substate changes, state tree diffs, transaction executions, locks, foreign proposals, UTXO mints, and validator evictions. Provides save() method for persisting changes to storage and memory management with configurable limits. Core component for atomic block processing in the distributed consensus protocol. |
| `commit_proofs.rs` | Sidechain commit proof generation for validator evictions and epoch transitions. Exports functions for generating eviction proofs, end-of-epoch commit proofs, and block commit proofs with full chain validation. Converts between internal consensus types and sidechain proof formats including QuorumCertificates, ChainLinks, and ValidatorBlockSignatures. Used by the consensus system to create cryptographic proofs of committed blocks for base layer integration and validator network management. |
| `common.rs` | Common utilities for HotStuff consensus protocol including dummy block generation, state tree calculations, and transaction processing. Exports functions for calculating dummy blocks between heights, computing state merkle roots from substate changes, generating epoch checkpoints, and processing newly justified blocks. Provides leader selection with eviction handling, fee pool management, and committee filtering for distributed consensus operations. Core shared functionality used across the consensus state machine for block validation and chain extension. |
| `config.rs` | HotStuff consensus configuration parameters including network settings, sidechain ID, consensus constants, and cleanup intervals. Essential configuration for BFT consensus protocol operation with network-specific parameters and timing controls. |
| `current_view.rs` | Thread-safe current view tracking for HotStuff consensus pacemaker. Exports CurrentView struct with atomic epoch and height counters, providing enter() for monotonic updates, reset() for forced synchronization, and get methods for current state. Used by consensus state machine to track progress through blockchain heights and epochs across multiple threads. Essential for coordinating view changes and ensuring consensus participants stay synchronized on current blockchain position. |
| `epoch_gc.rs` | Epoch garbage collection task for cleaning up old consensus data. Exports EpochGc struct implementing PeriodicTask trait for automated cleanup of outdated epoch information from storage. Runs epoch_cleanup() operations in background to maintain storage efficiency and prevent unbounded growth of consensus state. Part of the consensus maintenance system ensuring long-running validator nodes don't accumulate excessive historical data while preserving necessary consensus checkpoints. |
| `epoch_state.rs` | Epoch state management for consensus committee information and validator configuration. Exports EpochState struct containing current epoch, epoch hash, local committee details, and committee information for the validator's shard group. Provides update_from_epoch_manager() for refreshing committee membership and epoch transitions. Used by consensus system to maintain current validator set, shard assignments, and committee structure for the active epoch in distributed consensus operations. |
| `error.rs` | Comprehensive error types for HotStuff consensus protocol operations. Exports HotStuffError enum covering storage errors, validation failures, messaging issues, epoch management, and transaction processing errors. Includes ProposalValidationError for detailed block and QC validation failures. Used throughout consensus system for error handling in block validation, vote processing, leader selection, state management, and network communication. Essential for proper error propagation and debugging in the distributed consensus protocol. |
| `event.rs` | HotStuff consensus event types for communicating consensus state changes. Defines events for block commits, failures, leader timeouts, proposed block parking, and epoch transitions. Core event system enabling reactive programming patterns in consensus protocol. |
| `foreign_proposal_processor.rs` | Foreign proposal processing for cross-shard communication in distributed consensus. Exports process_foreign_block() function handling LocalPrepare and LocalAccept commands from other shard groups, validating foreign pledges, detecting transaction conflicts, and updating transaction pool state. Manages cross-shard transaction coordination, abort decisions based on foreign commitments, and pledge validation for multi-shard transactions. Critical component enabling the sharded consensus protocol to coordinate transaction execution across multiple validator committees. |
| `mod.rs` | HotStuff Byzantine Fault Tolerant consensus implementation module aggregator. Exports consensus state machine, voting mechanisms, leader election, proposal processing, transaction management, and synchronization. Core BFT consensus protocol for validator node coordination. |
| `on_beat.rs` | Consensus heartbeat mechanism for coordinating view changes and timing events. Provides watch-based notification system for consensus timing coordination. Simple but essential component for synchronizing consensus state transitions. |
| `on_catch_up_sync.rs` | Catch-up synchronization initiation for HotStuff consensus when validators fall behind. Exports OnCatchUpSync struct handling sync request creation and dispatch to other validators. Provides request_sync() method for requesting missing blocks from a specific validator based on current high QC position. Used by consensus state machine when detecting that local validator is behind network progress and needs to synchronize missing blocks and transactions to rejoin consensus. |
| `on_catch_up_sync_request.rs` | Catch-up synchronization response handling for providing blocks to lagging validators. Exports OnSyncRequest struct processing incoming sync requests and responding with missing blocks and votes. Validates epoch compatibility, fetches blocks between requested and current heights, includes foreign proposals, and sends last vote information. Essential for helping validators that have fallen behind to catch up to current blockchain state and rejoin consensus protocol operations. |
| `on_force_beat.rs` | Force beat mechanism for triggering consensus pacemaker timeouts and view changes. Exports OnForceBeat struct with watch channel for signaling immediate pacemaker beats at specific heights. Provides wait() for async timeout listening and beat() for triggering forced timeouts. Used by consensus system to manually advance views when needed, handle leader failures, or force view changes during network partitions or synchronization scenarios. |
| `on_inbound_message.rs` | Inbound message processing and buffering for HotStuff consensus protocol. Exports OnInboundMessage struct managing message reception, temporal buffering, and view-relative filtering. Includes MessageBuffer for queuing future messages and discarding outdated ones based on epoch/height. Handles proposals, votes, new view messages, foreign proposals, and sync requests with proper timing validation. Core message routing component ensuring consensus processes messages in correct order and buffers early arrivals. |
| `on_leader_timeout.rs` | Leader timeout handling for HotStuff consensus pacemaker and view changes. Processes leader timeout events, triggers view changes when current leader fails to propose blocks within timeout period, updates pacemaker state, and coordinates transition to next leader in rotation. Essential for Byzantine fault tolerance ensuring consensus progress continues when leaders become unresponsive, partitioned, or malicious. Used by consensus state machine to maintain liveness properties. |
| `on_message_validate.rs` | Message validation logic for HotStuff consensus protocol ensuring message authenticity and correctness. Validates signatures, checks epoch compatibility, verifies committee membership, validates block structure, and ensures message ordering constraints. Critical security component preventing malicious or malformed messages from disrupting consensus operations. Used by message processing pipeline to filter valid consensus messages before state machine processing. |
| `on_next_sync_view.rs` | Next view synchronization handling for coordinating view changes across consensus participants. Manages transition to next consensus view, synchronizes validator states, handles new view messages, and ensures consistent view progression across the network. Part of HotStuff view-change protocol enabling validators to coordinate on current consensus height and leader rotation during normal operation and recovery scenarios. |
| `on_propose.rs` | HotStuff proposal handling logic for creating and processing block proposals. Manages transaction selection, foreign proposal integration, block construction, state transitions, and proposal validation. Core component for consensus block production and validation workflow. |
| `on_ready_to_vote_on_local_block.rs` | Local block voting readiness detection and vote casting for HotStuff consensus. Determines when validator is ready to vote on locally proposed blocks, validates block contents, checks safety conditions, creates vote messages, and broadcasts votes to committee members. Core component of consensus voting protocol ensuring validators only vote on valid blocks that maintain safety and liveness properties of the blockchain. |
| `on_receive_foreign_proposal.rs` | Foreign proposal reception and processing for cross-shard consensus coordination. Handles incoming proposals from other shard groups, validates foreign block content, processes cross-shard transaction evidence, updates local transaction state, and coordinates multi-shard transaction execution. Essential for sharded consensus protocol enabling validators to receive and process blocks from other committees for distributed transaction coordination. |
| `on_receive_local_proposal.rs` | Local proposal reception and validation for HotStuff consensus within the same committee. Handles incoming block proposals from committee leaders, validates block structure and content, checks safety predicates, processes transaction executions, updates local state, and determines voting decisions. Core consensus component ensuring validators properly evaluate and respond to block proposals from other committee members. |
| `on_receive_new_transaction.rs` | New transaction reception and pool management for consensus transaction processing. Handles incoming transactions from network, validates transaction format and signatures, determines shard routing, sequences transactions into pool, and manages transaction lifecycle states. Used by consensus system to receive user transactions and foreign transaction notifications for inclusion in upcoming blocks and cross-shard coordination. |
| `on_receive_new_view.rs` | New view message reception and processing for HotStuff view change protocol. Handles incoming new view messages from validators, validates view change conditions, processes high QC and timeout certificates, coordinates view synchronization, and triggers leader selection for new views. Critical component of consensus view-change mechanism ensuring validators can recover from leader failures and maintain consensus progress. |
| `on_receive_request_missing_transactions.rs` | Missing transaction request handling for consensus transaction synchronization. Processes requests for missing transactions from other validators, validates request authenticity, retrieves requested transactions from local store, and responds with transaction data. Used during block validation when validators discover they lack transactions referenced in block proposals, enabling transaction sharing for complete block validation. |
| `on_receive_vote.rs` | HotStuff vote processing handler for collecting and validating consensus votes. Manages vote collection, leader verification, certificate generation, and consensus advancement. Essential component for BFT agreement protocol with vote aggregation and verification. |
| `pacemaker.rs` | HotStuff consensus pacemaker for controlling consensus timing and leader rotation. Manages view transitions, block proposal timing, timeout handling, and view synchronization. Critical component for maintaining consensus liveness with adaptive timing based on network conditions. |
| `pacemaker_handle.rs` | Pacemaker handle for controlling consensus timing and view progression. Provides interface for interacting with consensus pacemaker including view updates, timeout management, leader rotation, and forced view changes. Used by consensus state machine to coordinate timing aspects of HotStuff protocol, handle leader timeouts, and manage view synchronization across validator network for proper consensus operation. |
| `state_tree_gc.rs` | State tree garbage collection for cleaning up old merkle tree data. Manages cleanup of outdated state tree versions, removes obsolete merkle tree nodes, maintains storage efficiency, and preserves necessary state tree history for consensus operations. Background maintenance task preventing unbounded growth of state tree storage while preserving required blockchain state. |
| `worker.rs` | Main HotStuff consensus worker implementing the core consensus protocol. Orchestrates consensus operations including block proposal, vote processing, view changes, transaction management, foreign proposal handling, and state machine coordination. Primary entry point for consensus protocol execution managing all aspects of validator participation in distributed consensus. |

###### crates/consensus/src/hotstuff/state_machine/

| File | Description |
|------|-------------|
| `check_sync.rs` | Synchronization state checking for HotStuff consensus state machine. Validates whether validator is synchronized with network, checks for missing blocks or falling behind, determines if catch-up sync is needed, and coordinates transition between consensus states. Used by state machine to ensure validator maintains synchronization with network before participating in consensus operations. |
| `event.rs` | Event definitions for HotStuff consensus state machine transitions. Defines events triggering state changes including message reception, timeouts, sync completion, proposal validation, vote collection, and view changes. Used by state machine to process asynchronous events and coordinate transitions between Idle, Running, and Syncing states in the consensus protocol. |
| `idle.rs` | Idle state implementation for HotStuff consensus state machine. Handles initial state where validator is not actively participating in consensus, processes registration events, waits for epoch activation, manages committee membership updates, and transitions to Running state when ready to participate in consensus operations. |
| `mod.rs` | State machine module organization for HotStuff consensus protocol. Re-exports state machine components including State enum, Event definitions, Worker implementation, and state-specific handlers for Idle, Running, and Syncing states. Provides the main state machine coordination for consensus protocol execution and state transitions. |
| `running.rs` | Running state implementation for active HotStuff consensus participation. Handles main consensus operations including block proposal, vote processing, message validation, leader rotation, transaction execution, and foreign proposal coordination. Core state for validators actively participating in consensus protocol when committee is properly formed and synchronized. |
| `state.rs` | State enumeration and management for HotStuff consensus state machine. Defines State enum with Idle, Running, and Syncing variants, provides state transition logic, handles state-specific event processing, and maintains state machine invariants. Central coordination point for consensus state management and proper state transitions. |
| `syncing.rs` | Syncing state implementation for HotStuff consensus catch-up operations. Handles synchronization with network when validator falls behind, processes sync responses, validates received blocks, updates local state, and transitions back to Running state once synchronized. Critical for maintaining network participation after temporary disconnections or startup. |
| `worker.rs` | State machine worker implementation for HotStuff consensus protocol execution. Manages the main consensus event loop, coordinates state transitions, processes incoming events, handles background tasks, and maintains consensus state across Idle, Running, and Syncing states. Primary orchestrator for consensus protocol execution and state management. |

###### crates/consensus/src/hotstuff/substate_store/

| File | Description |
|------|-------------|
| `error.rs` | Error types for substate store operations in HotStuff consensus. Defines error variants for storage failures, state tree errors, substate conflicts, version mismatches, and access violations. Used throughout substate storage system for proper error handling and debugging during consensus state management and transaction execution. |
| `mod.rs` | Substate store module organization for consensus state management. Re-exports substate storage components including PendingSubstateStore, ShardedStateTree, ShardScopedTreeStoreReader, and error types. Provides unified interface for managing blockchain state during consensus operations, transaction execution, and state tree updates across sharded validator network. |
| `pending_store.rs` | Pending substate store for managing uncommitted state changes during consensus. Provides staging area for substate modifications before block commitment, handles read/write operations on pending state, manages state isolation between concurrent proposals, and coordinates state transitions during block execution. Critical for maintaining consistent state during consensus protocol execution. |
| `shard_state_store.rs` | Shard-specific state store implementation for distributed consensus state management. Provides storage interface for individual shard state, handles shard-scoped read/write operations, manages state tree operations within shard boundaries, and coordinates state synchronization between shards. Used by consensus system to maintain state consistency within validator committee's assigned shard group. |
| `sharded_state_tree.rs` | Sharded state tree implementation for distributed blockchain state management. Manages merkle tree operations across multiple shards, coordinates state tree updates, handles cross-shard state consistency, and provides unified state root calculation. Core component enabling scalable state management in sharded consensus protocol by partitioning state tree operations across validator committees. |

###### crates/consensus/src/hotstuff/transaction_manager/

| File | Description |
|------|-------------|
| `lock_deps.rs` | Transaction lock dependency management for preventing conflicts in consensus. Tracks substate locks, manages lock dependencies between transactions, detects lock conflicts, and coordinates lock resolution during transaction execution. Used by transaction manager to ensure safe concurrent transaction processing and prevent double-spending or conflicting state modifications. |
| `manager.rs` | Main transaction manager for consensus transaction lifecycle coordination. Manages transaction pool, coordinates transaction execution phases, handles transaction state transitions, manages pledges and commitments, and coordinates cross-shard transaction processing. Central component orchestrating transaction flow through prepare, accept, and commit phases in the distributed consensus protocol. |
| `mod.rs` | Transaction manager module organization for consensus transaction processing. Re-exports transaction management components including Manager, LockDeps, Pledged, Prepared states, and transaction coordination utilities. Provides unified interface for managing transaction lifecycle, lock dependencies, and state transitions during consensus operations. |
| `pledged.rs` | Pledged transaction state management for consensus transaction processing. Handles transactions in pledged state where validators have committed to specific substate values, manages pledge validation, coordinates pledge resolution, and transitions to prepared or aborted states. Part of multi-phase transaction protocol ensuring atomic transaction execution across distributed validator network. |
| `prepared.rs` | Prepared transaction state management for consensus transaction processing. Handles transactions in prepared state where all inputs are locked and validated, manages transaction readiness for acceptance phase, coordinates cross-shard preparation, and transitions to accept or abort states. Critical component of two-phase commit protocol in distributed consensus. |

###### crates/consensus/src/hotstuff/vote_collector/

| File | Description |
|------|-------------|
| `collector.rs` | Vote collection and aggregation for HotStuff consensus protocol. Collects votes from validators, validates vote signatures and eligibility, tracks voting progress, detects quorum achievement, and creates quorum certificates. Core component enabling consensus decisions by aggregating validator votes into proposal certificates and timeout certificates for blockchain progression. |
| `helpers.rs` | Helper utilities for vote collection and validation in HotStuff consensus. Provides utility functions for vote aggregation, signature verification, quorum calculation, voting power computation, and certificate creation. Used by vote collector to perform common operations during vote processing and quorum certificate generation. |
| `mod.rs` | Vote collector module organization for HotStuff consensus voting. Re-exports vote collection components including Collector, ProposalCollector, TimeoutCollector, and helper utilities. Provides unified interface for managing vote aggregation, quorum detection, and certificate creation during consensus protocol execution. |
| `proposal_collector.rs` | Proposal vote collection for HotStuff consensus block proposals. Collects and validates votes for block proposals, tracks voting progress per proposal, detects proposal quorum achievement, and creates proposal certificates. Used during consensus rounds to aggregate validator votes on block proposals and determine consensus acceptance or rejection. |
| `timeout_collector.rs` | Timeout vote collection for HotStuff consensus leader failure handling. Collects timeout votes from validators when leaders fail to propose, tracks timeout voting progress, detects timeout quorum achievement, and creates timeout certificates. Essential for view change protocol enabling consensus to progress when current leader becomes unresponsive or malicious. |

###### crates/consensus/src/messages/

| File | Description |
|------|-------------|
| `foreign_proposal.rs` | Foreign proposal message types for cross-shard consensus communication. Defines message structures for foreign block proposals, validation logic, serialization formats, and cross-shard coordination protocols. Used by consensus messaging system to communicate block proposals and transaction evidence between different validator committees in sharded consensus network. |
| `message.rs` | Core message types and routing for HotStuff consensus communication. Defines HotstuffMessage enum encompassing all consensus message types including proposals, votes, sync requests, foreign proposals, and view changes. Provides message serialization, routing logic, and common message handling functionality for consensus network communication. |
| `mod.rs` | Consensus messaging module organization providing all message types for HotStuff protocol. Re-exports message types including HotstuffMessage, ProposalMessage, VoteMessage, NewViewMessage, SyncRequestMessage, ForeignProposalMessage, and transaction request messages. Central messaging interface for consensus communication. |
| `new_view.rs` | New view message types for HotStuff consensus view change protocol. Defines new view message structure, validation logic, high QC and timeout certificate handling, and view synchronization protocols. Used during leader failures and view changes to coordinate validators on transitioning to new consensus rounds with updated leader selection. |
| `proposal.rs` | Block proposal message types for HotStuff consensus protocol. Defines proposal message structure containing block data, foreign proposals, validation logic, and proposal routing. Used by leaders to broadcast block proposals to committee members and coordinate consensus rounds during normal consensus operation. |
| `request_missing_transaction.rs` | Missing transaction request message types for consensus transaction synchronization. Defines request message structure for validators to request missing transactions from peers, handles transaction lookup coordination, and manages response routing. Used when validators need transactions referenced in block proposals to complete validation and maintain transaction pool consistency. |
| `requested_transaction.rs` | Transaction response message types for providing missing transactions to requesting validators. Defines response message structure containing requested transaction data, handles transaction sharing protocols, and manages peer-to-peer transaction distribution. Used to respond to missing transaction requests and maintain network-wide transaction availability. |
| `sync.rs` | Synchronization message types for HotStuff consensus catch-up protocol. Defines sync request and response message structures, handles block synchronization coordination, and manages catch-up communication between validators. Used when validators fall behind and need to synchronize missing blocks to rejoin consensus operations. |
| `vote.rs` | Vote message types for HotStuff consensus voting protocol. Defines vote message structure containing validator signatures, block references, and voting decisions. Handles vote validation, serialization, and routing for consensus participation. Core message type enabling validators to express approval or rejection of block proposals during consensus rounds. |

###### crates/consensus/src/traits/

| File | Description |
|------|-------------|
| `block_store.rs` | Block storage trait defining interface for consensus block persistence and retrieval. Provides abstract interface for storing, querying, and managing blockchain blocks across different storage implementations. Used by consensus system to abstract storage operations and enable pluggable storage backends for block data management. |
| `certificate.rs` | Certificate trait for consensus quorum certificate management. Defines interface for creating, validating, and managing proposal certificates and timeout certificates in HotStuff consensus. Provides abstraction for certificate operations enabling different certificate implementations and validation strategies. |
| `hooks.rs` | Consensus hooks trait for extensible event handling during consensus operations. Defines interface for custom event handlers, logging, metrics collection, and consensus monitoring. Enables pluggable observability and custom logic injection into consensus protocol execution without modifying core consensus code. |
| `leader_strategy.rs` | Leader selection strategy trait for HotStuff consensus leader rotation. Defines interface for different leader selection algorithms, round-robin rotation, reputation-based selection, and leader failure handling. Enables pluggable leader selection strategies to optimize consensus performance and Byzantine fault tolerance. |
| `messaging.rs` | Messaging traits for consensus network communication abstraction. Defines InboundMessaging and OutboundMessaging interfaces for receiving and sending consensus messages. Provides network layer abstraction enabling different transport protocols and message routing strategies for consensus communication. |
| `mod.rs` | Consensus traits module organization providing all abstract interfaces for consensus components. Re-exports traits for messaging, block storage, leader strategy, hooks, signing service, transaction execution, and consensus specification. Central trait collection enabling pluggable implementations and dependency injection in consensus system. |
| `signing_service.rs` | Signing service trait for consensus cryptographic operations. Defines interface for block signing, vote signing, certificate validation, and cryptographic authentication. Provides abstraction for different signing implementations enabling hardware security modules, key management systems, and cryptographic backends for consensus security. |
| `substate_store.rs` | Substate store trait for consensus state management abstraction. Defines interface for storing, retrieving, and managing blockchain substates across different storage implementations. Provides state access abstraction enabling pluggable storage backends for substate data management during consensus operations. |
| `sync.rs` | Synchronization trait for consensus catch-up and recovery operations. Defines interface for requesting missing blocks, handling sync responses, and coordinating catch-up protocols. Provides abstraction for different sync strategies enabling efficient recovery mechanisms when validators fall behind network progress. |
| `tasks.rs` | Task traits for consensus background operations and periodic maintenance. Defines PeriodicTask interface for garbage collection, state cleanup, metrics reporting, and maintenance operations. Enables structured background task management in consensus system ensuring proper resource management and system health. |
| `transaction_executor.rs` | Transaction executor trait for consensus transaction processing abstraction. Defines interface for executing transactions, validating transaction results, managing transaction state transitions, and coordinating transaction lifecycle. Provides execution abstraction enabling different transaction execution engines and validation strategies. |

###### crates/consensus/src/validations/

| File | Description |
|------|-------------|
| `block.rs` | Block validation logic for HotStuff consensus protocol. Validates block structure, signatures, merkle roots, transaction consistency, height progression, and safety predicates. Critical security component ensuring only valid blocks are accepted by consensus protocol and malformed or malicious blocks are rejected. |
| `common.rs` | Common validation utilities shared across consensus validation components. Provides reusable validation functions for signatures, timestamps, epochs, committee membership, and data integrity checks. Used by block, vote, and proposal validators to perform consistent validation operations. |
| `foreign_proposal.rs` | Foreign proposal validation for cross-shard consensus communication. Validates foreign block proposals, commit proofs, transaction evidence, and cross-shard consistency. Ensures foreign proposals from other committees are authentic, properly formed, and safe for local consensus processing in sharded blockchain network. |
| `mod.rs` | Validation module organization for consensus protocol security. Re-exports validation components for blocks, votes, foreign proposals, and common validation utilities. Provides centralized validation interface ensuring consistent security checks across all consensus message types and operations. |
| `signed_vote.rs` | Signed vote validation for HotStuff consensus voting protocol. Validates vote signatures, voter eligibility, vote structure, epoch compatibility, and voting power. Critical security component ensuring only authentic votes from eligible validators are accepted and processed during consensus rounds. |

#### crates/consensus_tests/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for consensus testing crate providing comprehensive test infrastructure for HotStuff consensus protocol. Dependencies include consensus implementation, storage mocks, networking stubs, cryptographic utilities, and testing frameworks. Used to build integration tests, unit tests, and consensus simulation environments for validating consensus protocol correctness and Byzantine fault tolerance. |

##### crates/consensus_tests/fixtures/

| File | Description |
|------|-------------|
| `block.json` | [GENERATED] Test fixture containing sample block data for consensus testing. JSON representation of valid block structure used in consensus protocol tests for validation, serialization, and state machine testing. Provides consistent test data for block processing, vote collection, and consensus round simulation. |
| `block_with_dummies.json` | [GENERATED] Test fixture containing block data with dummy blocks for consensus testing. JSON representation of block chain including dummy blocks used for testing view changes, leader failures, and catch-up scenarios. Provides test data for consensus protocol edge cases and recovery mechanisms. |
| `committee.json` | [GENERATED] Test fixture containing committee configuration data for consensus testing. JSON representation of validator committee structure including public keys, shard assignments, and voting power. Used in consensus tests for simulating multi-validator scenarios, Byzantine fault tolerance, and committee-based validation. |
| `state.wasm` | [GENERATED] Test fixture containing compiled WASM state for consensus state tree testing. WebAssembly bytecode representing blockchain state used for testing state tree operations, merkle root calculations, and state validation during consensus. Provides deterministic state data for consensus state management tests. |

##### crates/consensus_tests/src/

| File | Description |
|------|-------------|
| `consensus.rs` | Integration tests for HotStuff consensus protocol implementation. Tests consensus rounds, block proposal and voting, quorum formation, view changes, and Byzantine fault tolerance scenarios. Validates consensus safety and liveness properties under various network conditions including partition tolerance and malicious validator behavior. |
| `dummy_blocks.rs` | Tests for dummy block generation and handling in consensus protocol. Validates dummy block creation during view changes, leader timeouts, and catch-up scenarios. Tests dummy block validation, chain extension with dummies, and proper consensus progression when leaders fail or network partitions occur. |
| `eviction_proof.rs` | Tests for validator eviction proof generation and validation. Validates eviction proof creation for misbehaving validators, proof verification, and sidechain commit proof generation. Tests validator network management ensuring Byzantine validators can be properly evicted with cryptographic proof for base layer integration. |
| `leader_failure.rs` | Tests for leader failure scenarios and recovery mechanisms in consensus. Validates leader timeout handling, view change protocols, leader rotation, and consensus continuation under leader failures. Tests Byzantine fault tolerance ensuring consensus progresses when leaders become unresponsive, partitioned, or malicious. |
| `lib.rs` | Consensus testing library providing test infrastructure and utilities for HotStuff consensus protocol validation. Exports test harnesses, mock implementations, fixture loading, and common testing utilities. Central module organizing consensus test suite for protocol validation, Byzantine fault tolerance testing, and performance benchmarking. |
| `state_tree.rs` | Tests for state tree operations in consensus protocol. Validates merkle tree updates, state root calculations, shard state management, and state tree consistency during consensus operations. Tests state tree performance, concurrent access, and proper state evolution as blocks are committed to the blockchain. |
| `substate_store.rs` | Tests for substate store operations during consensus processing. Validates substate storage, retrieval, locking, conflict detection, and transaction state management. Tests store consistency, concurrent access patterns, and proper isolation between pending and committed substates during consensus protocol execution. |

###### crates/consensus_tests/src/support/

| File | Description |
|------|-------------|
| `address.rs` | Test support utilities for address generation and management in consensus tests. Provides address creation helpers, validator address generation, committee address assignment, and address-related test utilities. Used across consensus test suite for creating consistent test data and validator identities. |
| `epoch_manager.rs` | Mock epoch manager implementation for consensus testing. Provides test epoch management, committee configuration, validator registration simulation, and epoch transition testing. Used in consensus tests to simulate epoch changes, committee updates, and validator network evolution without external dependencies. |
| `executions_store.rs` | Mock execution store implementation for consensus testing. Provides transaction execution simulation, execution result storage, and execution state management for testing. Used in consensus tests to validate transaction processing, execution ordering, and state transitions without requiring full execution engine. |
| `fixtures.rs` | Test fixture loading and management utilities for consensus testing. Provides fixture file loading, test data generation, block creation helpers, and committee setup utilities. Used across consensus test suite for creating consistent test scenarios, validator networks, and blockchain state. |
| `harness.rs` | Consensus test harness providing comprehensive testing infrastructure for HotStuff protocol validation. Orchestrates multi-validator test networks, simulates Byzantine behavior, manages test scenarios, and provides consensus simulation environment. Central testing framework for validating consensus correctness, safety, and liveness properties. |
| `helpers.rs` | Helper utilities for consensus testing providing common test operations and assertions. Includes test assertion helpers, consensus state validation, message creation utilities, and test synchronization primitives. Used throughout consensus test suite for reducing test code duplication and ensuring consistent test patterns. |
| `leader_strategy.rs` | Mock leader selection strategy implementation for consensus testing. Provides deterministic leader selection, leader failure simulation, and leader rotation testing. Used in consensus tests to control leader behavior, test leader failure scenarios, and validate leader selection algorithms under various conditions. |
| `logging.rs` | Test logging configuration and utilities for consensus testing. Provides test-specific logging setup, log level management, test output filtering, and debugging utilities. Used to configure appropriate logging during consensus tests for debugging test failures and understanding consensus behavior. |
| `messaging_impls.rs` | Test messaging implementation for consensus protocol testing. Exports: TestOutboundMessaging (handles leader communication, broadcasts, and multicast with TestAddress), TestInboundMessaging (receives messages from network and loopback). Dependencies: HotstuffMessage, InboundMessaging/OutboundMessaging traits, TestEpochManager, TestAddress. Core components for simulating network communication in consensus tests, providing channels for leader messages, broadcasts, and self-messages with proper error handling. |
| `mod.rs` | Module aggregator for consensus test support utilities. Exports: all test support components including address, epoch_manager, fixtures, harness, helpers, network, spec, validator, and transaction utilities. Dependencies: NumPreshards from tari_ootle_common_types. Central hub for test infrastructure enabling consensus protocol testing with 256 preshards as default configuration. |
| `network.rs` | Test network simulation for consensus testing. Exports: spawn_network(), TestNetwork (controls network state and offline nodes), TestVnDestination (routing target enum), TestNetworkWorker (message routing engine). Dependencies: ValidatorChannels, HotstuffMessage, transaction handling. Implements complete network simulation with message filtering, offline node support, broadcast/leader message routing, and transaction mempool behavior for comprehensive consensus testing scenarios. |
| `signing_service.rs` | Test signature service for consensus validation. Exports: TestVoteSignatureService implementing ValidatorSignerService and ValidatorSignatureVerifierService traits. Dependencies: RistrettoPublicKey, PrivateKey, ValidatorSchnorrSignature, ToSignatureMessage. Provides cryptographic signing and verification capabilities for consensus messages using Ristretto keys derived from test addresses, essential for validator authentication in test scenarios. |
| `spec.rs` | Test consensus specification implementation. Exports: TestConsensusSpec implementing ConsensusSpec trait, TestStore type alias for RocksDbStateStore. Dependencies: TestAddress, TestEpochManager, TestInboundMessaging/TestOutboundMessaging, TestVoteSignatureService, AlwaysSyncedSyncManager, RoundRobinLeaderStrategy, TestBlockTransactionProcessor. Defines the complete consensus protocol specification for testing with all required components and NoopHooks for simplified test execution. |
| `sync.rs` | Simple synchronization manager for testing. Exports: AlwaysSyncedSyncManager implementing SyncManager trait. Dependencies: HotStuffError, SyncStatus. Always returns SyncStatus::UpToDate and succeeds sync operations immediately, eliminating synchronization complexity from consensus tests to focus on core consensus logic. |
| `table.rs` | ASCII table rendering utility for test output formatting. Exports: Table struct with title and row support, table_row! macro for row creation. Dependencies: std::io::Write. Features: configurable delimiters, column width auto-sizing, row counting, and stdout printing. Used for displaying test results and consensus state information in readable tabular format during test execution and debugging. |
| `transaction.rs` | Transaction creation and execution utilities for consensus testing. Exports: build_transaction_from(), create_execution_result_for_transaction(), build_substate_id_for_committee(), random_substates_ids_for_committee_generator(), build_transaction(). Dependencies: TransactionRecord, ExecuteResult, SubstateDiff, ValidatorFeeWithdrawal. Creates realistic transaction execution results with proper substate handling, fee calculations, and committee-specific substate ID generation for comprehensive consensus testing scenarios. |
| `transaction_executor.rs` | Test transaction execution processor for consensus testing. Exports: TestBlockTransactionProcessor implementing BlockTransactionExecutor trait. Dependencies: TestExecutionSpecStore, MemoryStateStore, TransactionExecution, VirtualSubstates. Validates and executes transactions using predefined execution specifications, manages state transitions, and handles substate dependencies. Central component for simulating realistic transaction processing behavior in consensus protocol tests. |

###### crates/consensus_tests/src/support/validator/

| File | Description |
|------|-------------|
| `builder.rs` | Builder pattern for creating test validators in consensus tests. Exports: ValidatorBuilder with fluent configuration methods for address, shard group, leader strategy, epoch manager, and RocksDB path. Dependencies: TestAddress, TestEpochManager, TestOutboundMessaging/TestInboundMessaging, HotstuffWorker, ConsensusWorker. Constructs complete validator instances with all required channels, messaging, state storage, and consensus components for comprehensive distributed consensus testing scenarios. |
| `instance.rs` | Validator instance and channel structures for consensus testing. Exports: ValidatorChannels (communication channels for messaging), Validator (complete validator instance with state, events, and handles). Dependencies: TestAddress, TestStore, TestEpochManager, HotstuffMessage, HotstuffEvent, ConsensusCurrentState. Provides validator lifecycle management, state access, transaction pool monitoring, and blockchain state querying capabilities for distributed consensus testing scenarios. |
| `mod.rs` | Module aggregator for validator testing components. Exports: builder and instance modules for validator construction and management. Dependencies: ValidatorBuilder, ValidatorChannels, Validator structs. Central module providing complete validator testing infrastructure for consensus protocol testing scenarios. |

#### crates/consensus_types/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo configuration for consensus types crate. Dependencies: tari_common_types, tari_ootle_common_types, tari_sidechain, tari_hashing, tari_template_lib, tari_engine_types, tari_crypto, borsh, serde. Features: optional TypeScript bindings via ts-rs. Provides core data structures and types used throughout the Tari consensus protocol implementation. |

##### crates/consensus_types/src/

| File | Description |
|------|-------------|
| `decision.rs` | Consensus decision types for Tari transaction outcomes. Exports Decision enum (Commit/Abort) with AbortReason details, providing is_commit(), is_abort(), abort_reason(), and(), is_same_outcome() methods. Features BorshSerialize, TypeScript bindings, and conversion from TransactionResult. Used by: consensus engine for representing transaction execution decisions and abort reasons. Core type for HotStuff consensus voting and transaction finalization. |
| `lib.rs` | Consensus-specific type definitions for HotStuff BFT implementation. Provides bookkeeping structures, certificates, decision types, identifier management, consensus traits, and validator signatures. Essential types for consensus protocol implementation, enabling proper message handling and state tracking in the distributed consensus system. |
| `traits.rs` | Consensus protocol trait definitions for Tari HotStuff implementation. Exports ToSignatureMessage for cryptographic signing, SignedMessage for signature verification, and Vote trait for consensus voting with key, epoch, height, and decision. Dependencies: FixedHash, SchnorrSignatureBytes, QuorumDecision. Used by: consensus engine for standardized message handling, signature verification, and vote processing across different consensus message types. |
| `validator_signature.rs` | Validator signature types for consensus authentication. Exports: ValidatorSchnorrSignature type alias, ValidatorSignatureBytes struct containing public_key and signature. Dependencies: RistrettoPublicKey, SchnorrSignature, ValidatorNodeHashDomain, crypto types. Used throughout consensus protocol for validator authentication in votes and certificates. Provides TypeScript bindings and serialization for cross-platform compatibility in consensus operations. |

###### crates/consensus_types/src/bookkeeping/

| File | Description |
|------|-------------|
| `high_pc.rs` | High Prepare Certificate (PC) bookkeeping structure for consensus. Exports: HighPc struct containing block_id, block_height, epoch, and qc_id. Dependencies: BlockId, QcId, NodeHeight, Epoch. Tracks the highest prepare certificate seen by a validator, essential for HotStuff consensus safety properties. Implements Display trait for debugging and logging consensus state progression. |
| `high_tc.rs` | High Timeout Certificate (TC) bookkeeping structure for consensus. Exports: HighTc struct containing epoch, height, and tc_id. Dependencies: TcId, NodeHeight, Epoch. Tracks the highest timeout certificate observed by a validator, crucial for HotStuff consensus liveness properties and view synchronization. Implements Display trait for debugging consensus timeout scenarios and state progression. |
| `last_executed.rs` | Last executed block bookkeeping structure for consensus. Exports: LastExecuted struct containing height, block_id, and epoch. Dependencies: BlockId, NodeHeight, Epoch. Tracks the most recently executed block by a validator, ensuring proper transaction execution ordering and state consistency in the HotStuff consensus protocol. |
| `last_proposed.rs` | Last proposed block bookkeeping structure for consensus. Exports: LastProposed struct with height, block_id, epoch, shard_group and as_leaf_block() method. Dependencies: BlockId, LeafBlock, NodeHeight, Epoch, ShardGroup. Tracks the most recent block proposed by a validator, essential for leader rotation and proposal validation in sharded HotStuff consensus. Implements Display trait for debugging. |
| `last_seen_block.rs` | Highest seen block bookkeeping structure for consensus. Exports: HighestSeenBlock struct containing block_id, height, epoch, shard_group with as_leaf() conversion. Dependencies: BlockId, LeafBlock, NodeHeight, Epoch, ShardGroup. Tracks the highest block height observed by a validator across all chains, essential for chain synchronization and view progression in sharded HotStuff consensus. Implements Display trait for consensus state monitoring. |
| `last_sent_newview.rs` | Last sent new view message bookkeeping structure for consensus. Exports: LastSentNewView struct containing epoch and height with accessor methods. Dependencies: Epoch, NodeHeight. Tracks the most recent new view message sent by a validator during view changes, preventing duplicate new view messages and ensuring proper view change protocol behavior in HotStuff consensus. Implements Display trait for debugging. |
| `last_sent_vote.rs` | Last sent vote bookkeeping structure for consensus. Exports: LastSentVote struct wrapping ProposalVote with accessor methods. Dependencies: BlockId, ProposalVote, Epoch, NodeHeight. Tracks the most recent vote sent by a validator, essential for preventing duplicate voting and maintaining HotStuff consensus safety properties. Provides conversion from ProposalVote and Display trait for vote tracking and debugging. |
| `last_voted.rs` | Last voted block bookkeeping structure for consensus. Exports: LastVoted struct containing block_id, height, epoch with accessor methods. Dependencies: BlockId, NodeHeight, Epoch. Tracks the most recent block a validator voted for, crucial for preventing double voting and maintaining HotStuff consensus safety properties. Implements Display trait for logging and debugging. |
| `leaf_block.rs` | Leaf block bookkeeping structure for consensus. Exports: LeafBlock struct containing block_id, height, epoch, shard_group with as_highest_seen_block() conversion. Dependencies: BlockId, HighestSeenBlock, NodeHeight, Epoch, ShardGroup. Represents the current tip of the blockchain from a validator's perspective, fundamental for HotStuff consensus progress tracking and chain extension decisions. Implements Display trait for debugging consensus state. |
| `locked_block.rs` | Locked block bookkeeping structure for consensus. Exports: LockedBlock struct containing height, block_id, epoch with accessor methods. Dependencies: BlockId, NodeHeight, Epoch. Tracks the block that a validator has locked in the HotStuff consensus protocol, essential for safety guarantees and preventing conflicting commits. Implements Display trait for consensus state debugging and logging. |
| `mod.rs` | Module aggregator for consensus bookkeeping types. Exports: all bookkeeping structures including HighPc, HighTc, LastExecuted, LastProposed, LastSeenBlock, LastSentNewview, LastSentVote, LastVoted, LeafBlock, LockedBlock. Dependencies: individual bookkeeping modules. Central hub for HotStuff consensus state tracking structures used to maintain protocol safety and liveness properties. |

###### crates/consensus_types/src/certificates/

| File | Description |
|------|-------------|
| `mod.rs` | Module aggregator for consensus certificate types. Exports: proposal_certificate, proposal_vote, timeout_certificate, timeout_vote modules. Dependencies: individual certificate implementations. Central hub for HotStuff consensus certificate structures including proposals and timeouts with their corresponding vote types essential for consensus protocol operations. |
| `proposal_certificate.rs` | Proposal certificate (Quorum Certificate) for HotStuff consensus. Exports: ProposalCertificate struct with header_hash, parent_id, height, epoch, shard_group, signatures, decision. Dependencies: BlockId, QcId, ValidatorSignatureBytes, QuorumDecision, HighPc, LeafBlock. Core consensus artifact proving quorum agreement on a block proposal. Provides ID calculation, conversion to bookkeeping structures, and TypeScript bindings. Essential for HotStuff safety and chain progression. |
| `proposal_vote.rs` | Proposal vote for HotStuff consensus. Exports: ProposalVote struct implementing Vote, ToSignatureMessage, SignedMessage traits. Dependencies: BlockId, ValidatorSignatureBytes, QuorumDecision, Epoch, NodeHeight. Individual validator vote on a block proposal that gets aggregated into ProposalCertificates. Provides cryptographic signature verification, message hashing, and vote key identification for secure consensus participation. Implements Display trait for vote tracking. |
| `timeout_certificate.rs` | Timeout certificate for HotStuff consensus liveness. Exports: TimeoutCertificate struct with epoch, height, signatures and as_high_tc() conversion. Dependencies: TcId, ValidatorSignatureBytes, HighTc, Epoch, NodeHeight. Aggregated timeout votes proving quorum agreement to advance views during network delays or failures. Provides ID calculation, signature verification, and TypeScript bindings. Essential for HotStuff liveness and view synchronization in asynchronous networks. |
| `timeout_vote.rs` | Timeout vote for HotStuff consensus view changes. Exports: TimeoutVote struct implementing Vote, ToSignatureMessage, SignedMessage traits, TimeoutVoteMessage helper. Dependencies: ValidatorSignatureBytes, Epoch, NodeHeight, QuorumDecision. Individual validator vote for timeout/view change that gets aggregated into TimeoutCertificates. Provides cryptographic signature verification and message hashing for secure consensus liveness. Always uses Accept decision for timeout scenarios. |

###### crates/consensus_types/src/ids/

| File | Description |
|------|-------------|
| `block_id.rs` | Block identifier for Tari consensus protocol. Exports: BlockId struct using create_hash_type! macro with from_parent_and_header_hash() method. Dependencies: FixedHash, hashing utilities. Provides strongly-typed block identification using cryptographic hashes derived from parent block ID and header hash. Handles special zero block case and ensures deterministic block ID calculation for consensus integrity. |
| `macros.rs` | Macro for creating strongly-typed hash-based identifiers. Exports: create_hash_type! macro generating ID types with FixedHash wrapper. Dependencies: FixedHash, serde, borsh serialization. Generates consistent ID types with zero values, conversions, display formatting, and byte access methods. Used throughout consensus types to create BlockId, QcId, TcId ensuring type safety and preventing hash confusion in protocol operations. |
| `mod.rs` | Consensus identifier types and macros. Exports: block_id module, QcId (Proposal Certificate ID), TcId (Timeout Certificate ID) via create_hash_type! macro. Dependencies: internal macros module. Provides strongly-typed hash-based identifiers for consensus components, ensuring type safety in HotStuff protocol operations and certificate management. |
| `qc_id.rs` | Quorum Certificate identifier for consensus protocol. Exports: QcId struct using create_hash_type! macro. Dependencies: create_hash_type! macro. Provides strongly-typed identifier for Quorum Certificates (proposal certificates) in HotStuff consensus, ensuring type safety when referencing and storing QCs across the protocol implementation. |
| `tc_id.rs` | Timeout Certificate identifier for consensus protocol. Exports: TcId struct with hash wrapper, zero value, conversions, and byte access methods. Dependencies: FixedHash, FixedHashSizeError, serde, borsh. Provides strongly-typed identifier for Timeout Certificates in HotStuff consensus, ensuring type safety when referencing and storing TCs. Includes comment about representing unsigned initial QC (likely copy-paste error from QC implementation). |

#### crates/engine/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo configuration for Tari template runtime engine. Dependencies: tari_* crates for core functionality, wasmer for WASM runtime, wasmer-middlewares, crypto libraries, serialization (borsh, serde), utilities. Dev dependencies: template test tooling and transaction manifest. Core engine responsible for executing smart contract templates in secure WASM environment with resource management and state handling. |

##### crates/engine/src/

| File | Description |
|------|-------------|
| `lib.rs` | Main library module for the Tari smart contract execution engine. Exports core engine modules including executables for WASM execution, fees for transaction fee calculation, function_definitions for template function handling, runtime for execution environment, state_store for state management, template for smart contract templates, transaction for transaction processing, and wasm for WASM runtime integration. Re-exports tari_template_abi as abi and includes base layer hash functions for cryptographic operations. Central entry point for smart contract execution infrastructure. |
| `traits.rs` | Core traits for the Tari smart contract execution engine. Defines Invokable trait for objects that can execute smart contract functions with generic store parameter, error handling, and function definition support. The invoke method takes a mutable store reference, FunctionDef for function metadata, CBOR-encoded arguments, and returns InstructionResult. Foundation trait for template execution and function invocation in the WASM runtime environment. |

###### crates/engine/src/executables/

| File | Description |
|------|-------------|
| `mod.rs` | Executable abstraction for engine transactions and instructions. Exports: Instructions struct (fee/main instruction sets), Executable trait (ID, inputs, signer, instructions), WeightedExecutable trait (weight calculation). Dependencies: Instruction, SubstateRequirementRef, TransactionId, TransactionWeight. Core abstraction enabling the engine to execute different types of transactions with consistent interfaces for resource tracking and fee calculation. |
| `transaction.rs` | Transaction implementation of Executable traits for engine execution. Exports: Executable and WeightedExecutable implementations for Transaction. Dependencies: Transaction, TransactionId, TransactionWeight, SubstateRequirementRef, Instructions. Provides concrete implementation enabling transactions to be executed by the engine runtime, handling signer determination, input iteration, instruction separation, and weight calculation for fee processing. |

###### crates/engine/src/fees/

| File | Description |
|------|-------------|
| `fee_module.rs` | Fee calculation module for engine runtime execution. Exports: FeeModule implementing RuntimeModule trait, ByteCounter utility. Dependencies: FeeTable, StateTracker, FeeSource, RuntimeModule. Calculates fees for transaction initialization, weight, runtime calls, storage, logs, and events during execution. Tracks resource usage and applies fee table rates with storage cost reduction factor. Essential for economic security and resource management in the template engine. |
| `fee_table.rs` | Fee table configuration for engine resource pricing. Exports: FeeTable struct with per-unit costs for transaction weight, module calls, storage bytes, events, and logs. Dependencies: none. Provides zero_rated() constructor and accessor methods for all fee types. Used by FeeModule to calculate actual fees based on resource usage during transaction execution. Essential for economic security and spam prevention in the template engine. |
| `mod.rs` | Fee calculation and management system for smart contract execution. Provides fee tables for resource pricing and fee modules for calculating transaction costs. Essential component for network economics and spam prevention through usage-based fees. |

###### crates/engine/src/function_definitions/

| File | Description |
|------|-------------|
| `function_arg_definition.rs` | Function argument definitions for template function signatures. Exports: FunctionArgDefinition struct with name and arg_type, ArgType enum (String, Bytes). Dependencies: tari_template_abi::Type, serde. Defines argument metadata for template functions enabling type checking and validation during template invocation. Converts to template ABI types for WASM interface compatibility. |
| `mod.rs` | Module aggregator for WASM function definitions and argument handling. Exports: FunctionArgDefinition, ArgType from function_arg_definition, WasmFunctionDefinition from wasm_function_definition. Dependencies: individual definition modules. Central hub for managing template function signatures, argument types, and WASM function metadata used by the engine for template invocation and validation. |
| `wasm_function_definition.rs` | WASM function definition metadata for template functions. Exports: WasmFunctionDefinition struct containing name, args, and in_module fields. Dependencies: FunctionArgDefinition, serde. Represents template function metadata including function name, argument specifications, and module context. Used by engine to validate and invoke WASM template functions with proper argument handling. |

###### crates/engine/src/runtime/

| File | Description |
|------|-------------|
| `actions.rs` | Action identification and categorization system for smart contract runtime operations. Defines action types for components, resources, vaults, and native operations. Used for authorization, tracking, and auditing of template execution actions. |
| `address_allocation.rs` | Engine runtime address allocation system for managing substate addressing. Exports AllocatedAddress struct which tracks allocated substate addresses along with their optional template addresses. Used by the runtime to manage address allocation and referencing during transaction execution. The AllocatedAddress provides immutable access to substate_id and template_address fields for address resolution within the engine runtime. |
| `auth.rs` | Authorization and authentication framework for runtime execution. Exports: AuthParams (initial ownership proofs), AuthorizationScope (virtual and resource-based proofs). Dependencies: NonFungibleAddress, ProofId. Manages authorization context during template execution with virtual proofs (system-issued) and resource proofs. Supports proof addition/removal and scope inheritance for secure component access control and resource authorization. |
| `engine_args.rs` | Engine argument handling for runtime function calls. Exports: EngineArgs struct wrapping Vec<Vec<u8>> with type-safe deserialization methods. Dependencies: tari_bor decode, RuntimeError. Provides get(), get_opt(), assert_one_arg(), assert_n_args(), assert_no_args() for argument validation and extraction. Essential for safe parameter passing between runtime and template functions with proper error handling. |
| `error.rs` | Comprehensive error types for engine runtime execution. Exports: RuntimeError (main error enum), AssertError, TransactionCommitError. Dependencies: engine types, template lib, state store errors. Covers all possible runtime failures including substate not found, access denied, fee validation, lock errors, validation failures, and commit errors. Implements conversion to reject reasons and not-found error detection for consensus integration. |
| `fee_state.rs` | Fee state management for transaction processing in the engine runtime. Exports FeeState struct that tracks fee payments (ResourceContainer with VaultId pairs) and fee charges (FeeBreakdown). Provides methods to calculate total charges and payments for fee validation and processing. Used during transaction execution to accumulate fees and ensure proper fee collection from transaction participants. |
| `impl.rs` | Main RuntimeInterface implementation for template engine execution. Exports: RuntimeInterfaceImpl struct implementing RuntimeInterface trait. Dependencies: StateTracker, template provider, component operations, resource management, WASM execution, virtual substates. Massive implementation (2500+ lines) handling all runtime operations including component creation, resource management, consensus actions, confidential operations, fee processing, and cross-template calls. Core engine implementation enabling secure template execution. |
| `limits.rs` | Engine execution limits and constants for resource control. Exports: EngineLimits struct, ENGINE_LIMITS constant. Dependencies: none. Defines maximum number of call arguments (100) to prevent resource exhaustion attacks. TODO comment indicates future expansion for function name length limits. Used by runtime to enforce execution boundaries and maintain system stability. |
| `locking.rs` | Substate locking system for the engine runtime implementing readers-writer locks for concurrent access control. Exports LockedSubstates manager, LockState enum (Read/Write), LockedSubstate wrapper, and LockError types. Provides thread-safe locking mechanisms with lock ID tracking, access validation, and automatic lock cleanup. Supports multiple concurrent readers or single writer per substate, with comprehensive error handling for lock conflicts and access violations. |
| `mod.rs` | Core runtime engine interface and implementation for template execution. Exports: RuntimeInterface trait, Runtime struct, AuthParams, RuntimeModule, RuntimeError, StateTracker, various action types. Dependencies: template ABI, engine types, component management, substate handling. Central abstraction for WASM template execution providing component invocation, resource management, consensus interactions, and state finalization capabilities with comprehensive error handling and security controls. |
| `module.rs` | Runtime module trait for engine lifecycle hooks. Exports: RuntimeModule trait with on_initialize(), on_runtime_call(), on_before_finalize() hooks, RuntimeModuleError. Dependencies: StateTracker, BorError. Enables modular engine functionality with fee modules, logging, validation, etc. All methods have default no-op implementations for optional behavior injection during transaction execution phases. |
| `scope.rs` | Call scope management for engine runtime execution context. Exports CallScope (tracking owned/referenced substates, locks, proofs, buckets, auth scope), CallFrame (template context with scope), and PushCallFrame enum for frame creation. Manages deterministic substate ownership, authorization scopes, and resource tracking during template execution. Handles scope inheritance between parent/child calls and enforces access control for buckets, proofs, and locked substates. |
| `state_store.rs` | Working state store implementation for runtime substate management during transaction execution. Exports WorkingStateStore which provides cached access to substates with locking, mutation tracking, and change detection. Manages new substates (IndexMap for deterministic ordering), loaded substates cache, and locked substates coordination. Handles component loading, vault enumeration, and substate diff generation for transaction processing. |
| `tracker.rs` | Core state tracking for engine runtime execution. Exports: StateTracker managing WorkingState, events, logs, fees, call frames, and substates. Dependencies: WorkingState, Workspace, indexed values, virtual substates, locking. Central coordinator for all runtime state mutations including component creation, substate locking, fee tracking, event emission, and transaction finalization. Provides thread-safe access patterns and fee checkpoint/rollback functionality for atomic execution. |
| `tracker_auth.rs` | Authorization and access control system for engine runtime operations. Exports Authorization struct providing access rule validation for component methods and resource operations. Implements ownership checking (OwnedBySigner, ByAccessRule, ByPublicKey), restricted access rule evaluation (Require, AnyOf, AllOf), and requirement validation (Resource, NonFungibleAddress, ScopedToComponent/Template). Enforces fine-grained access control using proofs, virtual proofs, and authorization scopes. |
| `working_state.rs` | Core working state management for engine runtime transaction execution. Exports WorkingState managing transaction context including events, logs, buckets, proofs, address allocations, virtual substates, fee state, and call frame stack. Provides comprehensive resource management (mint, burn, recall), vault operations, component state validation, authorization integration, and transaction finalization. Central coordinator for all runtime state changes during template execution with extensive validation and error handling. |
| `workspace.rs` | Workspace management for template execution context providing indexed value storage and retrieval. Exports Workspace struct managing WorkspaceId-indexed values with offset support for array/map access. Handles proof ID tracking and provides tuple/array element access for complex data structures. Used during template execution to store intermediate values and function parameters with type-safe indexed access patterns. |

###### crates/engine/src/state_store/

| File | Description |
|------|-------------|
| `bootstrap.rs` | State store bootstrapping utilities for initializing engine state with global resources. Exports new_memory_store() function creating MemoryStateStore with essential system resources: PUBLIC_IDENTITY_RESOURCE (non-fungible ID tokens) and CONFIDENTIAL_TARI_RESOURCE (XTR confidential tokens). These global resources are implicitly available in every transaction without explicit pledging, providing foundation for identity and fee payment systems. |
| `memory.rs` | In-memory state store implementation for engine runtime. Exports: MemoryStateStore, ReadOnlyMemoryStateStore implementing StateReader/StateWriter traits. Dependencies: Substate, SubstateId, state store traits. Provides fast HashMap-based storage for substates during transaction execution. Used for temporary state during processing and read-only views for execution contexts. Essential for engine's transactional state management and testing infrastructure. |
| `mod.rs` | State storage abstractions for smart contract execution engine. Provides StateReader and StateWriter traits for accessing blockchain state during template execution. Includes memory-based implementations and error handling for deterministic state management. |

###### crates/engine/src/template/

| File | Description |
|------|-------------|
| `error.rs` | Template loading error types for engine template system. Exports: TemplateLoaderError enum covering WASM module, compile, instantiation, export, and runtime errors. Dependencies: wasmer errors, WasmExecutionError. Comprehensive error handling for template loading pipeline from WASM compilation through instantiation and execution. Enables proper error propagation and debugging in template management. |
| `loaded_template.rs` | Template loading abstraction for engine template management. Exports LoadedTemplate enum currently supporting WASM templates through LoadedWasmTemplate wrapper. Provides template metadata access (name, definition, code size) and serves as extensible interface for future template types. Used by template registry and execution engine to manage loaded template instances with consistent API regardless of underlying template implementation. |
| `mod.rs` | Template loading and management system for smart contract WASM modules. Provides template loading infrastructure, loaded template abstractions, and error handling for WASM template lifecycle management. Core component for dynamic smart contract loading in the execution engine. |
| `template_loader.rs` | Template loading trait definition for engine modularity. Exports: TemplateModuleLoader trait with load_template() method. Dependencies: LoadedTemplate, TemplateLoaderError. Simple abstraction enabling different template loading strategies and dependency injection for testing. Used by engine components to load templates from various sources (filesystem, network, database). |

###### crates/engine/src/transaction/

| File | Description |
|------|-------------|
| `error.rs` | Transaction processing error types for engine execution pipeline. Exports: TransactionError enum covering WASM, runtime, template loading, and validation errors. Dependencies: WasmExecutionError, RuntimeError, TemplateLoaderError, IndexedValueError. Handles template not found, binary size limits, function resolution, and conversion errors. Comprehensive error propagation for transaction execution debugging and error handling. |
| `mod.rs` | Transaction processing engine for executing smart contract instructions. Provides transaction processor with configurable parameters, error handling, and call depth limits. Core component for deterministic transaction execution with state validation and fee calculation. |
| `processor.rs` | Transaction processor for engine execution pipeline. Exports: TransactionProcessor, TransactionProcessorConfig with network settings and binary size limits (5MB default). Dependencies: StateTracker, WASM execution, template loading, instruction processing. Main entry point for transaction execution handling instruction processing, state management, template invocation, and result finalization. Includes account creation, workspace management, and comprehensive error handling for atomic transaction execution. |

###### crates/engine/src/wasm/

| File | Description |
|------|-------------|
| `compile.rs` | WASM template compilation utilities for building smart contract templates from Rust source code. Provides compile-time tooling for converting template source into deployable WASM modules. Development utility for template creation and testing workflows. |
| `environment.rs` | WASM execution environment for template engine providing memory management and ABI integration. Exports WasmEnv wrapper managing WASM memory, allocator functions, error tracking (panics, engine errors), and template ABI loading. Provides memory reading/writing utilities, pointer management via AllocPtr, and error propagation mechanisms. Core component for safe WASM template execution with proper resource management and debugging support. |
| `error.rs` | WASM execution error types for template engine. Exports: WasmError (argument errors), WasmExecutionError (comprehensive WASM runtime errors). Dependencies: wasmer errors, RuntimeError, BorError, IndexedValueError. Covers instantiation failures, memory access violations, function export issues, encoding problems, version mismatches, and panic handling. Essential for WASM security and debugging in template execution. |
| `limiting_tunable.rs` | WASM memory limiting tunables for secure template execution. Exports: LimitingTunables struct wrapping base tunables with memory limits. Dependencies: wasmer Tunables, VMMemory, VMTable types. Enforces maximum memory usage (Pages limit) by adjusting and validating MemoryType requests. Delegates table operations to base tunables. Critical security component preventing memory exhaustion attacks in WASM templates. |
| `mem_writer.rs` | WASM memory writer utility implementing tari_bor::Write trait for writing data to WASM linear memory. Exports MemWriter struct providing sequential memory writing with automatic pointer advancement. Used for serializing data into WASM template memory space during template execution and function parameter passing. |
| `metering.rs` | WASM execution metering for DoS protection and fair resource usage. Exports: middleware() function creating Metering middleware with cost_function. Dependencies: wasmer-middlewares::Metering, wasmparser::Operator. Assigns execution costs to different WASM operations (arithmetic: 1-4, memory: 1-4, calls: 4, floating point: 4-10). Critical for preventing infinite loops and ensuring bounded execution time in templates. |
| `mod.rs` | WASM compilation and execution module for smart contract templates. Provides WASM module loading, compilation tools, execution environment, metering for resource limits, and versioning. Enables secure sandboxed execution of user-provided smart contract code. |
| `module.rs` | WASM module loading and execution for template runtime. Exports: WasmModule, MainFunction, LoadedWasmTemplate types. Dependencies: wasmer runtime, template ABI, LimitingTunables, metering middleware. Handles WASM compilation with Cranelift backend, memory limits (2MB), execution metering (100M ops), and template ABI loading. Creates secure sandboxed environment for template execution with resource controls and debugging support. |
| `process.rs` | WASM process execution engine providing secure template runtime environment. Implements ABI communication, function invocation, memory management, and sandbox isolation. Core component for executing smart contract templates with deterministic behavior and resource limits. |
| `version.rs` | Semantic version compatibility checking for WASM templates using Cargo-style semver rules. Exports are_versions_compatible function and SemverCompatibility enum. Implements compatibility logic where versions are compatible if their left-most non-zero component (major/minor/patch) matches. Used for template version validation and upgrade compatibility during template loading. |

##### crates/engine/tests/

| File | Description |
|------|-------------|
| `access_rules.rs` | Engine integration tests for access control and authorization rules covering component and resource access patterns. Tests component ownership, method access rules (AllowAll, DenyAll, Restricted), resource operations (withdraw, deposit, recall), and authorization scope validation. Validates proper access control enforcement, error handling for unauthorized operations, and complex rule compositions. Critical for security validation of the access control system. |
| `account.rs` | Engine integration tests for account component functionality including basic faucet operations, transfers, and access control. Tests account creation, XTR token transfers, authorization validation, and component interaction patterns. Validates account component security, proper access control enforcement, and transaction processing. Essential for testing core account functionality used throughout the system. |
| `address_allocation.rs` | Engine integration tests for address allocation system validating pre-allocated addresses, address usage tracking, and allocation error handling. Tests address allocation within templates, allocation reuse prevention, allocation lifecycle management, and proper address generation. Validates address uniqueness, allocation cleanup, and error scenarios. Critical for ensuring proper address management and preventing address conflicts. |
| `airdrop.rs` | Engine integration tests for NFT airdrop functionality including multi-recipient distribution, batch operations, and supply management. Tests airdrop template creation, token distribution to multiple accounts, total supply tracking, and batch account creation. Validates proper NFT minting, distribution mechanisms, and supply constraints. Essential for testing mass distribution features. |
| `asserts.rs` | Engine integration tests for assertion and validation mechanisms including runtime assertions, error propagation, and validation checks. Tests assert operations, error handling, account operations, faucet functionality, and runtime validation. Validates proper assertion behavior, error reporting, and system integrity checks. Critical for testing debugging and validation tools within templates. |
| `composability.rs` | Engine integration tests for template composability and inter-component communication including call depth limits, component interaction patterns, and cross-template functionality. Tests template-to-template calls, state sharing, access control across components, and maximum call depth enforcement. Validates proper component isolation, interaction security, and call stack management. Critical for template ecosystem functionality. |
| `confidential.rs` | Engine integration tests for confidential transaction functionality including ElGamal encryption, range proofs, and privacy-preserving asset operations. Tests confidential output generation, proof validation, withdraw operations, commitment verification, and balance recovery using lookup tables. Validates cryptographic correctness, proof verification, and proper error handling for confidential transaction systems. Critical for privacy feature validation. |
| `events.rs` | Engine integration tests for event emission and handling covering basic event emission, event payload validation, template-specific events, and component-scoped events. Tests event topic generation, payload data handling, template address association, and substate ID linkage. Validates proper event system functionality, data integrity, and event filtering capabilities. Essential for monitoring and debugging template execution. |
| `fees.rs` | Engine integration tests for fee management and payment processing covering fee deduction, refunding, payment validation, and insufficient fee handling. Tests fee collection from accounts, refund mechanisms, transaction execution with fees enabled/disabled, and fee receipt validation. Validates proper fee payment flows, error handling for insufficient fees, and accurate fee accounting. Essential for transaction economics validation. |
| `publish_template.rs` | Engine integration tests for template publication process including template compilation, publishing validation, address generation, and fee handling. Tests successful template publication, hash calculation, template address derivation, and publication failure scenarios. Validates template compilation pipeline, publishing mechanics, fee payment, and error handling for template deployment. Critical for testing template lifecycle management. |
| `recall.rs` | Engine integration tests for resource recall functionality covering fungible, non-fungible, and confidential resource recovery operations. Tests recall operations across all resource types, vault interactions, confidential proof handling, and resource retrieval mechanisms. Validates proper resource recall implementation, access control, and asset recovery capabilities. Essential for testing emergency recovery and admin operations. |
| `reentrancy.rs` | Engine integration tests for reentrancy attack prevention and locking mechanisms including reentrant withdraw protection, lock state validation, and cross-component call security. Tests reentrancy prevention, lock validation, component interaction security, and proper locking behavior. Validates system security against reentrancy attacks and ensures proper lock management. Critical for security validation of the engine's protection mechanisms. |
| `resource.rs` | Engine integration tests for resource management operations including fungible/non-fungible/confidential resource joining operations. Tests resource combination, bucket operations, confidential resource handling with proof generation, and resource container management. Validates proper resource aggregation, type safety, and proof handling for different resource types. Core validation for resource management functionality. |
| `shenanigans.rs` | Engine integration tests for malicious and edge case behaviors including dangling vault detection, orphaned substate handling, invalid resource operations, and security violation attempts. Tests detection of improper resource management, invalid operations, malicious template behavior, and system security mechanisms. Validates engine robustness against malicious templates and improper resource handling. Critical for security validation. |
| `tariswap.rs` | Engine integration tests for TariSwap decentralized exchange functionality including liquidity pool creation, token swapping, fee handling, and liquidity provision. Tests AMM (Automated Market Maker) functionality, swap operations, liquidity pool management, and DEX mechanics. Validates proper exchange implementation, slippage handling, and liquidity incentives. Essential for DeFi functionality validation. |
| `test.rs` | Engine integration test suite covering core template execution, WASM compilation, and runtime behavior. Tests hello world template, NFT operations, component instantiation, virtual substates, and error handling. Validates template compilation, execution pipeline, substate management, and transaction processing. Comprehensive test coverage for engine functionality including builtin templates and custom template behavior. |

###### crates/engine/tests/templates/access_rules/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo configuration for AccessRules test template demonstrating comprehensive authorization patterns. Template project with tari_template_lib dependency and CDYLIB/lib crate types for WASM compilation. Used for testing access control mechanisms, badge-based authentication, and permission systems in the engine. |

###### crates/engine/tests/templates/access_rules/src/

| File | Description |
|------|-------------|
| `lib.rs` | Access rules test template demonstrating comprehensive authorization and access control patterns including badge-based authentication, resource access rules, component access control, and owner rules. Exports AccessRulesTest component with configurable access rules, badge management, and authorization testing. Essential template for validating access control systems, permission management, and security mechanisms. |

###### crates/engine/tests/templates/address_allocation/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo configuration for AddressAllocation test template demonstrating address pre-allocation and management functionality. Template project with tari_template_lib dependency and CDYLIB/lib crate types for WASM compilation. Used for testing address allocation mechanisms, address reuse prevention, and allocation lifecycle management in the engine. |

###### crates/engine/tests/templates/address_allocation/src/

| File | Description |
|------|-------------|
| `lib.rs` | Test template for testing component and resource address allocation functionality. The AddressAllocationTest component demonstrates pre-allocation of addresses before resource/component creation, showing how to use CallerContext::allocate_component_address() and CallerContext::allocate_resource_address(). Key exports: create(), create_from_allocations(), get_resource_address(), get_component_allocation_address(), get_resource_allocation_address(), drop_resource_allocation(), drop_component_allocation(). Creates non-fungible resources with multiple token types (u32, u64, string, u256) and validates that allocated addresses match actual addresses after creation. Dependencies: tari_template_lib. Used in engine integration tests for address allocation validation. |

###### crates/engine/tests/templates/buggy/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for intentionally buggy template test module used to validate error handling in the template engine. Configures minimal dependencies (tari_template_abi, lol_alloc) with 'alloc' feature for no-std environments. Defines three conditional features for testing different ABI error scenarios: return_null_abi (returns empty ABI), return_empty_abi (returns minimal ABI), unexpected_export_function (exports unexpected functions). Compiles as both cdylib for WASM and lib for native testing. Used in engine tests to verify proper error handling for malformed templates. |

###### crates/engine/tests/templates/buggy/src/

| File | Description |
|------|-------------|
| `lib.rs` | Intentionally buggy template implementation for testing engine error handling capabilities. Uses lol_alloc memory allocator for WASM environments with single-threaded assumptions. Contains hardcoded ABI definition bytes that can be conditionally replaced with invalid data via features (return_null_abi, return_empty_abi). Exports minimal C-compatible functions: Buggy_main() that returns null pointer, and optional i_shouldnt_be_here() for testing unexpected exports. Includes external function declarations for engine integration (tari_engine, debug, on_panic). Used to verify template validation, ABI parsing, and error recovery in the execution engine. |

###### crates/engine/tests/templates/builtin_templates/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for builtin templates test module that validates access to system-provided template addresses. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native environments. Used in engine integration tests to verify that builtin template addresses (Account, AccountNft) are correctly accessible and properly typed within user templates. |

###### crates/engine/tests/templates/builtin_templates/src/

| File | Description |
|------|-------------|
| `lib.rs` | Test template for validating access to builtin template addresses provided by the Tari engine. The BuiltinTest component provides static methods to retrieve system template addresses for core functionality: get_account_template_address() returns the standard account template address, get_account_nft_template_address() returns the account NFT template address. Uses BuiltinTemplate enum to access pre-deployed system templates. Dependencies: tari_template_lib. Used in engine tests to verify builtin template accessibility and address consistency across the network. |

###### crates/engine/tests/templates/caller_context/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for caller context test template that validates transaction signer identity retrieval. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify CallerContext functionality and transaction signer public key accessibility within template execution. |

###### crates/engine/tests/templates/caller_context/src/

| File | Description |
|------|-------------|
| `lib.rs` | Test template for validating CallerContext functionality, specifically transaction signer identity retrieval. The CallerContextTest component stores the signer's public key obtained via CallerContext::transaction_signer_public_key() during component creation. Key exports: create() for component instantiation, caller_pub_key() for retrieving stored signer identity. Uses RistrettoPublicKeyBytes for cryptographic key storage. Dependencies: tari_template_lib. Used in engine tests to verify that templates can correctly identify and access transaction signer information for authentication and authorization purposes. |

###### crates/engine/tests/templates/component_manager/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for component manager test template (note: incorrectly named 'tuples' in package name). Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify ComponentManager functionality for introspecting component metadata and template relationships. |

###### crates/engine/tests/templates/component_manager/src/

| File | Description |
|------|-------------|
| `lib.rs` | Test template for validating ComponentManager functionality for component introspection. The ComponentManagerTest provides static method get_template_address_for_component() that uses ComponentManager::get() to retrieve a component's template address. This validates the engine's ability to provide metadata about deployed components, including their originating template. Dependencies: tari_template_lib. Used in engine tests to verify component metadata accessibility and template-component relationship tracking. |

###### crates/engine/tests/templates/composability/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo configuration for Composability test template demonstrating inter-component communication and template interaction patterns. Template project with tari_template_lib dependency and CDYLIB/lib crate types for WASM compilation. Used for testing template composability, cross-component calls, and interaction security in the engine. |

###### crates/engine/tests/templates/composability/src/

| File | Description |
|------|-------------|
| `lib.rs` | Comprehensive test template for validating component composability and cross-component interactions. The Composability component wraps a 'State' component and demonstrates function-to-function, function-to-component, and component-to-component calls via TemplateManager and ComponentManager. Key exports: new(), new_from_component(), get_state_component_address(), set_nested_composability(), increase_inner_state_component(), replace_state_component(), malicious_withdraw(), get_nested_value(), call_component_with_args(). Tests include recursive calls, invalid method calls, and security validation for cross-component access. Dependencies: tari_template_lib. Used in engine tests to verify composability patterns, call security, and recursion limits. |

###### crates/engine/tests/templates/confidential/faucet/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for confidential faucet template used to test confidential (privacy-preserving) transactions. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify confidential asset creation, minting, and withdrawal with zero-knowledge proofs. |

###### crates/engine/tests/templates/confidential/faucet/src/

| File | Description |
|------|-------------|
| `lib.rs` | Test template for confidential (privacy-preserving) asset faucet functionality. The ConfidentialFaucet component manages a vault of confidential assets and provides minting/withdrawal operations with zero-knowledge proofs. Key exports: mint(), mint_with_view_key(), mint_revealed(), mint_revealed_with_range_proof(), mint_more(), take_free_coins(), total_supply(), split_coins(), vault_balance(). Supports ConfidentialOutputStatement for mint proofs, ConfidentialWithdrawProof for withdrawals, and RistrettoPublicKeyBytes for view keys. Dependencies: tari_template_lib. Used in engine tests to verify confidential transaction mechanics and cryptographic proof validation. |

###### crates/engine/tests/templates/confidential/utilities/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for confidential utilities template providing helper functions for confidential asset operations. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify utility functions for confidential bucket operations and asset management. |

###### crates/engine/tests/templates/confidential/utilities/src/

| File | Description |
|------|-------------|
| `lib.rs` | Test template providing utility functions for confidential asset bucket operations. The ConfidentialUtilities struct offers static methods for common confidential asset manipulations: take_from_bucket() for withdrawing confidential amounts using ConfidentialWithdrawProof, and join_buckets() for combining buckets of the same resource. These utilities demonstrate proper handling of confidential assets and zero-knowledge proof integration. Dependencies: tari_template_lib. Used in engine tests to verify bucket manipulation mechanics in confidential transactions. |

###### crates/engine/tests/templates/consensus/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for consensus test template that validates access to consensus layer information. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify that templates can access consensus-level data such as current epoch information. |

###### crates/engine/tests/templates/consensus/src/

| File | Description |
|------|-------------|
| `lib.rs` | Test template for validating access to consensus layer information within template execution. The TestConsensus struct provides static method current_epoch() that uses Consensus::current_epoch() to retrieve the current blockchain epoch. This validates that templates can access consensus-level data and time-based information for time-sensitive operations. Dependencies: tari_template_lib. Used in engine tests to verify consensus data accessibility and epoch management integration. |

###### crates/engine/tests/templates/errors/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for error handling test template (note: incorrectly named 'account' in package name). Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify proper error handling, panic behavior, and invalid operation detection within template execution. |

###### crates/engine/tests/templates/errors/src/

| File | Description |
|------|-------------|
| `lib.rs` | Test template for validating error handling and panic behavior within template execution. The Errors struct provides static methods to test different failure scenarios: panic() for intentional panics with message capture, please_pass_invalid_args() for argument validation testing, and invalid_engine_call() for testing invalid resource operations (creating vault for non-existent resources). Used to verify that the engine properly captures and handles template errors, panics, and invalid operations. Dependencies: tari_template_lib. Critical for engine robustness testing. |

###### crates/engine/tests/templates/events/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for event emission test template (note: incorrectly named 'event' in package name). Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify event emission functionality and event logging within template execution. |

###### crates/engine/tests/templates/events/src/

| File | Description |
|------|-------------|
| `lib.rs` | Test template for validating event emission functionality within template execution. The EventEmitter struct provides test_function() that demonstrates how to emit custom events using emit_event() with topic strings and key-value payload data. Uses HashMap for event payload structure and std::collections for data organization. Dependencies: tari_template_lib, std::collections. Used in engine tests to verify event emission mechanics, event logging, and template-to-engine communication via events. |

###### crates/engine/tests/templates/hello_world/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo configuration for HelloWorld test template - basic example template for engine testing. Minimal template project with tari_template_lib dependency and CDYLIB/lib crate types for WASM compilation. Demonstrates fundamental template structure and serves as reference implementation for template development and testing framework validation. |

###### crates/engine/tests/templates/hello_world/src/

| File | Description |
|------|-------------|
| `lib.rs` | HelloWorld test template demonstrating basic template structure, component creation, and method invocation. Exports HelloWorld component with greet() static function and new() constructor with greeting field. Simplest possible template for testing template compilation, instantiation, and execution pipeline. Reference implementation for template development and engine testing. |

###### crates/engine/tests/templates/infinity_loop/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for infinite loop test template used to validate engine timeout and execution limit handling. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify execution timeout enforcement and runaway loop detection capabilities. |

###### crates/engine/tests/templates/infinity_loop/src/

| File | Description |
|------|-------------|
| `lib.rs` | Test template for validating infinite loop detection and execution timeout handling. The InfinityLoopTest component provides infinity_loop() method that enters an intentional infinite loop without any termination condition. Used to test engine's ability to detect and handle runaway execution, timeout enforcement, and resource consumption limits within template execution. Dependencies: tari_template_lib. Critical for testing engine safety mechanisms and execution bounds. |

###### crates/engine/tests/templates/nft/airdrop/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for NFT airdrop test template that validates token distribution and allow list mechanics. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify NFT distribution patterns, recipient management, and airdrop state control. |

###### crates/engine/tests/templates/nft/airdrop/src/

| File | Description |
|------|-------------|
| `lib.rs` | Comprehensive NFT airdrop template for testing token distribution mechanics. The Airdrop component manages an allow list and distributes non-fungible tokens with symbol 'AIR' to eligible recipients. Key exports: new(), add_recipient(), open_airdrop(), claim_any(), claim_specific(), total_supply(), num_claimed(), vault_balance(). Features allow list management with 100-address limit, airdrop state control, and both random and specific token claiming. Uses HashSet for efficient address lookup and NFT ID management. Dependencies: tari_template_lib, tari_template_abi::rust::collections::HashSet. Used in engine tests to verify NFT distribution patterns and access control mechanisms. |

###### crates/engine/tests/templates/nft/basic_nft/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo configuration for BasicNFT test template demonstrating non-fungible token functionality. Template project with tari_template_lib dependency and CDYLIB/lib crate types for WASM compilation. Used for testing NFT creation, minting, data structures, and resource management in the engine. |

###### crates/engine/tests/templates/nft/basic_nft/src/

| File | Description |
|------|-------------|
| `lib.rs` | Basic NFT test template demonstrating non-fungible token creation, minting, and management functionality. Exports SparkleNft component with NFT resource creation, token minting with custom data (Sparkle struct), vault management, and NFT operations. Reference implementation for NFT functionality testing including data structures, minting operations, and resource management. |

###### crates/engine/tests/templates/nft/emoji_id/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for emoji-based NFT test template that validates custom identifier systems and paid minting mechanics. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify custom NFT ID generation, payment validation, and Unicode emoji handling in blockchain assets. |

###### crates/engine/tests/templates/nft/emoji_id/src/

| File | Description |
|------|-------------|
| `lib.rs` | NFT minting template demonstrating emoji-based unique identifier system and paid minting mechanics. The EmojiIdMinter component creates a constant-price NFT drop with custom emoji IDs using Unicode emoji characters (Smile, Sweat, Laugh, Wink). Key exports: new(), mint(), total_supply(). Features payment processing with exact amount validation, emoji ID length constraints, uniqueness enforcement via string-based NonFungibleId derivation, and metadata storage. Uses custom Emoji enum with Display trait for Unicode rendering and EmojiId wrapper for emoji sequences. Dependencies: tari_template_lib, std collections. Used in engine tests to verify custom NFT ID systems, payment validation, and metadata handling. |

###### crates/engine/tests/templates/nft/nft_list/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for NFT list test template that validates random ID generation and mutable data handling. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify random NonFungibleId generation, mutable NFT data structures, and basic minting patterns. |

###### crates/engine/tests/templates/nft/nft_list/src/

| File | Description |
|------|-------------|
| `lib.rs` | Simple NFT template for testing random token ID generation and mutable data handling. The SparkleNft component demonstrates basic NFT minting with random IDs and mutable Sparkle data structure containing brightness properties. Key exports: new(), mint(), total_supply(). Features resource creation with 'sparkle' symbol, random NonFungibleId generation, and mutable data attachment for post-mint modifications. Uses custom Sparkle struct with serde serialization. Dependencies: tari_template_lib. Used in engine tests to verify random ID generation, mutable NFT data, and basic minting patterns. |

###### crates/engine/tests/templates/nft/tickets/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for event ticketing NFT test template that validates real-world use cases with payment processing and redemption tracking. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify complex NFT business logic, payment validation, and mutable state management. |

###### crates/engine/tests/templates/nft/tickets/src/

| File | Description |
|------|-------------|
| `lib.rs` | Comprehensive event ticketing system template demonstrating real-world NFT use cases with supply management and redemption tracking. The TicketSeller component manages ticket sales with XTR payments, redemption status tracking, and owner-based minting controls. Key exports: new(), mint_more_tickets(), buy_ticket(), redeem_ticket(), total_supply(). Features initial batch minting, event metadata, exact payment validation, ticket redemption with mutable data updates, and supply chain tracking capabilities. Uses Ticket struct with redemption status and owner-based access rules. Dependencies: tari_template_lib. Used in engine tests to verify complex NFT business logic, payment processing, and state management. |

###### crates/engine/tests/templates/private_function/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for private function test template that validates method visibility and encapsulation controls. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify private method access restrictions and template interface boundaries. |

###### crates/engine/tests/templates/private_function/src/

| File | Description |
|------|-------------|
| `lib.rs` | Test template for validating private method access and visibility controls within template execution. The PrivateCounter component demonstrates proper encapsulation with private methods some_private_method() and some_private_function() that are not exposed through the template interface. Key exports: new(), get(), increase(). Tests that private methods can only be called internally and are not accessible from external template invocations. Dependencies: tari_template_lib. Used in engine tests to verify method visibility enforcement and template interface boundaries. |

###### crates/engine/tests/templates/random/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for random number generation test template that validates deterministic randomness in template execution. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify random number generation, entropy sources, and consistent randomness across executions. |

###### crates/engine/tests/templates/random/src/

| File | Description |
|------|-------------|
| `lib.rs` | Test template for validating random number generation capabilities within template execution. The RandomTest component generates and stores various types of random data during creation: random u32 integers, 32-byte random arrays, and 300-byte long random arrays. Key exports: create(), get_random(), get_random_bytes(), get_random_long_bytes(). Uses rand::random_u32() and rand::random_bytes() functions provided by the template runtime. Dependencies: tari_template_lib. Used in engine tests to verify deterministic randomness, entropy sources, and consistent random generation across template executions. |

###### crates/engine/tests/templates/recall/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for resource recall test template that validates administrative control over deployed assets. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify resource recall functionality across fungible, non-fungible, and confidential asset types. |

###### crates/engine/tests/templates/recall/src/

| File | Description |
|------|-------------|
| `lib.rs` | Comprehensive test template for resource recall functionality across all asset types (fungible, non-fungible, confidential). The Recall component demonstrates creation and management of recallable resources with rule!(allow_all) recall permissions. Key exports: new(), withdraw_some(), recall_fungible_all(), recall_fungible(), recall_non_fungibles(), recall_confidential(), get_balances(). Tests recall operations with vault IDs, specific amounts/IDs, commitment sets, and revealed amounts. Uses BTreeSet for efficient ID/commitment management. Dependencies: tari_template_lib, std::collections::BTreeSet. Used in engine tests to verify resource recall mechanics and administrative control over deployed assets. |

###### crates/engine/tests/templates/reentrancy/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for reentrancy attack test template that validates protection against recursive method calls. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify reentrancy protection mechanisms and component method call security. |

###### crates/engine/tests/templates/reentrancy/src/

| File | Description |
|------|-------------|
| `lib.rs` | Test template for validating reentrancy attack prevention in component method calls. The Reentrancy component provides methods that attempt to call themselves recursively via ComponentManager::current().call() to test engine protection mechanisms. Key exports: with_bucket(), new(), withdraw(), deposit(), get_balance(), reentrant_withdraw(), reentrant_access(), reentrant_access_mut(), reentrant_access_immutable(), assert_is_allowed(). Tests both state-changing and immutable reentrancy scenarios to ensure engine prevents unauthorized recursive calls and state corruption. Dependencies: tari_template_lib. Critical for validating template execution security and reentrancy protection. |

###### crates/engine/tests/templates/resource/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for resource management test template that validates multi-type asset operations. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify resource creation, bucket operations, and asset type handling across fungible, non-fungible, and confidential resources. |

###### crates/engine/tests/templates/resource/src/

| File | Description |
|------|-------------|
| `lib.rs` | Test template for validating resource management operations across all asset types (fungible, non-fungible, confidential). The ResourceTest component demonstrates resource creation, bucket joining, and balance management for different resource types. Key exports: new(), fungible_join(), non_fungible_join(), confidential_join(). Tests bucket operations including withdrawal, joining multiple buckets, minting confidential assets with ConfidentialOutputStatement, and balance assertions. Validates resource type compatibility and commitment counting for confidential assets. Dependencies: tari_template_lib. Used in engine tests to verify core resource manipulation mechanics and multi-type asset handling. |

###### crates/engine/tests/templates/resource_shenanigans/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for resource type mismatch test template that validates engine protection against invalid operations. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify resource type safety and operation validation against incompatible resource operations. |

###### crates/engine/tests/templates/resource_shenanigans/src/

| File | Description |
|------|-------------|
| `lib.rs` | Test template for validating engine protection against resource type mismatches and invalid operations. The Shenanigans component demonstrates attempting to mint fungible tokens from a non-fungible resource, which should be caught by the engine's type validation. Key exports: new(), mint_different_resource_type(). Tests resource type enforcement by creating a non-fungible resource and then attempting to mint fungible tokens from it, validating that the engine properly rejects incompatible operations. Dependencies: tari_template_lib. Used in engine tests to verify resource type safety and operation validation. |

###### crates/engine/tests/templates/shenanigans/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for comprehensive security testing template that validates engine protection against various attack vectors. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify security mechanisms against vault theft, unauthorized access, state manipulation, and cross-template exploitation attempts. |

###### crates/engine/tests/templates/shenanigans/src/

| File | Description |
|------|-------------|
| `lib.rs` | Comprehensive security testing template for validating engine protection against various attack vectors and unauthorized operations. The Shenanigans component tests dangling resources/components, vault theft attempts, cross-template exploitation, hardcoded vault access, state manipulation, and bucket/proof theft. Key exports: dangling_vault(), return_vault(), with_stolen_vault(), attempt_to_steal_funds_using_cross_template_call(), take_from_hardcoded_vault(), empty_state_on_component(), deposit(). Tests include Vault::for_test() misuse, compile-time vault ID injection, unauthorized ComponentManager calls, and various security boundary violations. Dependencies: tari_template_lib. Critical for validating engine security mechanisms and attack prevention. |

###### crates/engine/tests/templates/state/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for state management test template that validates component state persistence and access control. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify state transitions, access rule enforcement, and component initialization patterns. |

###### crates/engine/tests/templates/state/src/

| File | Description |
|------|-------------|
| `lib.rs` | Test template for validating state management and access control patterns. The State enum represents three variants (Zero, One, Other(u32)) with component creation methods demonstrating different access rule configurations. Key exports: new(), create_multiple(), restricted(), set(), get(). Features include default state creation with allow_all access, batch component creation, and restricted components with method-specific access rules. Implements From<u32> conversion trait for state transitions. Dependencies: tari_template_lib. Used in engine tests to verify state persistence, access control enforcement, and component initialization patterns. |

###### crates/engine/tests/templates/tariswap/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo configuration for TariSwap test template implementing automated market maker (AMM) functionality. Template project with tari_template_lib dependency and CDYLIB/lib crate types for WASM compilation. Used for testing DeFi functionality, liquidity pools, token swapping, and decentralized exchange operations in the engine. |

###### crates/engine/tests/templates/tariswap/src/

| File | Description |
|------|-------------|
| `lib.rs` | TariSwap test template implementing automated market maker (AMM) functionality with constant product formula for decentralized exchange operations. Exports TariSwapPool component with liquidity pool management, token swapping, fee collection, liquidity provision/removal, and price calculation. Comprehensive DeFi template for testing AMM mechanics, liquidity operations, and exchange functionality. |

###### crates/engine/tests/templates/tuples/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for tuple data type test template that validates complex return values and parameter passing. Simple configuration with tari_template_lib dependency and cdylib/lib compilation for both WASM and native testing environments. Used in engine integration tests to verify tuple type support, multi-value returns, and structured data handling in template execution. |

###### crates/engine/tests/templates/tuples/src/

| File | Description |
|------|-------------|
| `lib.rs` | Test template for validating tuple data type handling and return value support in template execution. The Tuple component demonstrates storing and manipulating tuple data with both String and u32 components. Key exports: new(), get(), set(). Tests tuple return values from component creation (returning both Component and String), tuple parameter passing, and tuple state management. Validates that complex data structures can be properly serialized, passed between template boundaries, and maintained in component state. Dependencies: tari_template_lib. Used in engine tests to verify tuple type support and multi-value return functionality. |

#### crates/engine_types/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo configuration for tari_engine_types crate providing shared engine data types. Dependencies include cryptographic libraries (tari_crypto, blake2), serialization (borsh, serde), template system (tari_template_lib/abi), and optional TypeScript bindings generation (ts-rs). Features include default, ts (TypeScript export), and ts-rs-temporary-fix for test compatibility issues. Core types library used across the engine subsystem. |

##### crates/engine_types/src/

| File | Description |
|------|-------------|
| `argument_parser.rs` | Argument parsing utilities for template instruction arguments supporting JSON and custom string syntax. Exports json_deserialize function for serde integration, parse_arg for special string parsing (Amount(123), component addresses, template addresses). Handles complex type conversion including CBOR encoding, workspace references, and typed substate addresses. Enables flexible argument specification in both JSON-RPC and CLI contexts. |
| `base_layer_hashing.rs` | Base layer hashing utilities for Tari blockchain integration providing domain-separated hashers. Exports Blake2b-based hashers for 32-byte and 64-byte outputs with network-specific labels. Includes specialized hashers for confidential outputs, ownership proofs, and wallet encryption keys. Used for cryptographic operations requiring deterministic, network-aware hashing compatible with Tari base layer. |
| `bucket.rs` | Bucket resource container for transient resource management during template execution. Exports Bucket struct wrapping ResourceContainer with additional bucket-specific operations including take/join, locking for proofs, and non-fungible token management. Provides resource isolation and transfer mechanisms between template functions while maintaining resource type safety and ownership tracking. |
| `byte_types.rs` | Type conversion utilities between cryptographic types and their lightweight byte representations. Exports ToByteType and FromByteType traits for safe conversion between RistrettoPublicKey, PedersenCommitment, SchnorrSignature, CommitmentSignature and their corresponding byte array types. Enables efficient serialization and type-safe cryptographic operations with proper error handling for malformed byte data. |
| `commit_result.rs` | Transaction execution and commit result types for engine processing outcomes. Exports ExecuteResult (finalization result with timing), FinalizeResult (events, logs, execution results), TransactionResult enum (Accept/AcceptFeeRejectRest/Reject), RejectReason enum, and AbortReason. Provides comprehensive error handling, fee management, and result inspection utilities for transaction processing pipeline with TypeScript bindings support. |
| `component.rs` | Component type definitions for template instance management including headers and bodies. Exports ComponentHeader (template_address, access_rules, owner_key, entity_id, state), ComponentBody (CBOR state), and component address derivation utilities. Provides ownership checking, access rule management, and substate containment validation. Core component infrastructure for template-based smart contracts with comprehensive metadata and security controls. |
| `entity_id_provider.rs` | Thread-safe entity ID generation provider for unique entity identification within transaction contexts. The EntityIdProvider manages atomic ID allocation with configurable limits and generates deterministic EntityId values from transaction hashes. Key exports: EntityIdProvider, EntityIdProviderError, new(), next_entity_id(), transaction_hash(). Uses atomic counters for thread safety and cryptographic hashing with engine-specific domain labels for ID generation. Features max ID allocation enforcement and locking error handling. Dependencies: tari_template_lib, crate::hashing. Used throughout engine for creating unique identifiers for components, resources, and other blockchain entities within transaction boundaries. |
| `events.rs` | Event system for template execution providing structured event emission with metadata and topic-based organization. Exports Event struct with template address, transaction hash, topic naming, substate association, and payload management. Supports standard engine events (std. prefix) and custom template events with comprehensive metadata storage. Essential for monitoring, debugging, and external system integration with TypeScript bindings support. |
| `fees.rs` | Fee management system providing fee calculation, payment tracking, and cost breakdown for transaction processing. Exports FeeReceipt with payment totals, cost breakdowns, and refund calculations. Includes FeeBreakdown for detailed cost analysis and FeeCostBreakdown for comprehensive fee reporting. Core component for transaction economics with proper fee allocation, refunding, and accounting mechanisms. |
| `hashing.rs` | Cryptographic hashing infrastructure providing domain-separated Blake2b hashers for engine operations. Exports TariHasher64, hasher functions with domain labels, and template code hashing utilities. Supports secure hash generation for addresses, state validation, and cryptographic operations with proper domain separation. Essential for maintaining cryptographic integrity across the system with specialized hashers for different use cases. |
| `id_provider.rs` | Comprehensive ID generation system for creating unique blockchain entity identifiers within transaction boundaries. The IdProvider manages deterministic generation of resource addresses, component addresses, vault IDs, bucket IDs, proof IDs, and random bytes using cryptographic hashing with domain separation. Features ObjectIds for thread-safe atomic counters and maximum allocation limits. Key exports: IdProvider, ObjectIds, IdProviderError, new_resource_address(), new_component_address(), new_vault_id(), new_bucket_id(), new_proof_id(), new_uuid(), get_random_bytes(). Uses domain-separated hashing for security and includes comprehensive test coverage. Dependencies: tari_template_lib, crate::hashing, crate::component. Essential for blockchain entity creation and transaction isolation. |
| `indexed_value.rs` | Value indexing system for tracking referenced substates and well-known types within template execution. Exports IndexedValue and IndexedWellKnownTypes for automatically extracting substate references, bucket/proof IDs, vault references, and component addresses from CBOR values. Essential for scope management, dependency tracking, and ensuring proper resource management during template execution with comprehensive type analysis. |
| `instruction.rs` | Instruction types for transaction execution defining all possible operations in the Tari engine. Exports Instruction enum covering account creation, function/method calls, workspace operations, logging, fee payments, resource claiming, address allocation, and assertions. Comprehensive instruction set for template execution with argument parsing, access control, and workspace management. Core component for transaction composition and execution flow. |
| `instruction_call.rs` | Core type definitions for component call routing in the Tari template engine. Defines ComponentCall enum with Address(ComponentAddress) and Workspace(WorkspaceId) variants for routing template calls to either specific component addresses or workspace contexts. Implements From traits for convenient conversion from ComponentAddress and WorkspaceId. Features TypeScript bindings generation via ts-rs for frontend integration. Exports: ComponentCall enum, From implementations, Display trait for debugging. Dependencies: tari_bor, tari_template_lib. Used throughout engine to distinguish between direct component calls and workspace-scoped operations. |
| `instruction_result.rs` | Template instruction execution result wrapper with indexed value access and type information. The InstructionResult struct contains an IndexedValue for efficient data access and Type information for result validation. Key exports: InstructionResult struct, empty(), decode(), get_value(). Provides BOR-based deserialization for extracting typed results and path-based value access for complex nested data structures. Features TypeScript bindings generation for frontend integration. Dependencies: tari_bor, tari_template_abi, serde. Used throughout engine to wrap and access template execution results with strong typing. |
| `json_cbor.rs` | JSON to CBOR conversion utility with depth protection for safe data transformation. Provides convert_json_to_cbor() function that converts serde JSON values to tari_bor CBOR values with 50-level maximum depth protection against stack overflow attacks. Handles all JSON types (null, bool, number, string, array, object) with appropriate CBOR mappings. Features MaxDepthExceeded error for security protection. Exports: convert_json_to_cbor(), MaxDepthExceeded. Dependencies: serde_json, tari_bor. Used for secure JSON-to-CBOR data transformation in API processing and data serialization pipelines. |
| `lib.rs` | Core type definitions for smart contract execution engine. Exports comprehensive type system including substates, components, resources, instructions, proofs, transactions, and confidential operations. Fundamental types shared across the entire smart contract execution stack. |
| `lock.rs` | Locking system types providing read/write lock flags and lock ID management for substate concurrency control. Exports LockFlag enum (Read/Write), LockId type alias, and lock validation utilities. Core component for the engine's concurrency control system ensuring proper read/write access patterns and preventing data races during template execution. |
| `logs.rs` | Logging system for template execution providing structured log entry management with configurable log levels. Exports LogEntry struct with message content and log level (Debug, Info, Warn, Error). Used for debugging and monitoring template execution with proper log level filtering and structured output. Essential for development, debugging, and production monitoring with TypeScript bindings support. |
| `non_fungible.rs` | Non-fungible token container providing data storage, mutability management, and burning functionality for NFTs. Exports NonFungibleContainer and NonFungible types with immutable data, mutable data fields, burning state tracking, and typed data access. Essential for NFT functionality supporting complex data structures, ownership tracking, and lifecycle management including burn operations. |
| `proof.rs` | Proof system for authorization and resource access providing cryptographic proofs of resource ownership. Exports Proof and LockedResource types with container references (bucket/vault), resource validation, and access authorization. Core component for security model enabling proof-based authorization, resource locking, and access control verification throughout the template execution system. |
| `published_template.rs` | Published template address system for uniquely identifying deployed smart contract templates. Defines PublishedTemplateAddress with 'template_' prefix format and PublishedTemplate struct containing author and binary hash. Uses deterministic address generation from author public key and binary hash via domain-separated cryptographic hashing. Key exports: PublishedTemplateAddress, PublishedTemplate, from_author_and_binary_hash(), as_hash(), from_hex(). Features TypeScript bindings, borsh serialization, and comprehensive string/parsing support. Dependencies: tari_bor, tari_template_lib, crate::hashing. Critical for template deployment, identification, and verification in the smart contract system. |
| `resource.rs` | Resource definition system providing fungible, non-fungible, and confidential resource types with access control and metadata management. Exports Resource struct with resource type, ownership rules, access rules, metadata, supply tracking, and view key support. Core component for digital asset definition with comprehensive authorization, supply management, and confidential asset support including view key validation. |
| `resource_container.rs` | Resource container implementation providing unified storage for fungible, non-fungible, and confidential resources with comprehensive operations. Exports ResourceContainer enum with deposit, withdraw, join, lock operations, and confidential proof validation. Core component for resource management supporting all asset types with proper validation, proof handling, and confidential transaction integration including commitment verification and output statements. |
| `substate.rs` | Substate management system providing the core state representation for blockchain entities including components, resources, vaults, NFTs, templates, and receipts. Exports Substate, SubstateId, SubstateValue, and SubstateDiff types with comprehensive address derivation, hashing, serialization, and difference tracking. Foundation for all blockchain state management with versioning, validation, and efficient storage mechanisms. |
| `substate_serde.rs` | Custom serde implementation for SubstateId enum supporting both binary and human-readable serialization formats. Provides manual Serialize/Deserialize implementations that use string representation for JSON (enabling SubstateId as map keys) and standard enum serialization for binary formats. Handles all substate variants: Component, Resource, Vault, UnclaimedConfidentialOutput, NonFungible, TransactionReceipt, Template, ValidatorFeePool. Includes comprehensive test coverage for JSON and bincode round-trip validation. Exports: Serialize/Deserialize implementations for SubstateId. Dependencies: tari_template_lib, crate types. Critical for API serialization and database storage of blockchain state identifiers. |
| `template.rs` | Template address parsing and binary hash calculation utilities for the Tari template system. Provides parse_template_address() for parsing 'template_' prefixed address strings and calculate_template_binary_hash() for generating deterministic hashes of WASM template binaries. Uses engine-specific hash domain labels for cryptographic separation and FixedHash for consistent address representation. Exports: parse_template_address(), calculate_template_binary_hash(). Dependencies: tari_common_types, tari_template_lib, crate::hashing. Used for template deployment validation and address generation from WASM bytecode. |
| `transaction_receipt.rs` | Transaction receipt system providing comprehensive transaction execution results including events, logs, and fees. Exports TransactionReceiptAddress, TransactionReceipt with event collection, log entries, fee receipts, and transaction hash. Essential for transaction result tracking, monitoring, debugging, and external system integration with complete execution context and outcome data. |
| `validator_fee.rs` | Validator fee pool management system for validator node economics in the Tari network. Defines ValidatorFeePoolAddress with 'vnfp_' prefix for unique fee pool identification and ValidatorFeePool struct for managing validator rewards with RistrettoPublicKeyBytes ownership. Key exports: ValidatorFeePoolAddress, ValidatorFeePool, ValidatorFeeWithdrawal, withdraw_direct(), deposit_direct(), withdraw_all(), as_ownership(). Features XTR token integration, ownership-based access control, and ResourceContainer creation for bucket operations. Includes TypeScript bindings and borsh serialization. Dependencies: tari_template_lib, tari_bor, crate::resource_container. Critical for validator economics and fee distribution mechanisms. |
| `vault.rs` | Vault implementation for secure resource storage providing deposit, withdraw, and locking mechanisms for all resource types. Exports Vault struct with ResourceContainer wrapper, supporting fungible/non-fungible/confidential operations, proof generation, and resource locking. Essential component for persistent resource storage with proper access control, proof mechanisms, and transaction safety including confidential asset support. |
| `virtual_substate.rs` | Virtual substate system providing engine-managed global state like current epoch without persistent storage. Exports VirtualSubstateId, VirtualSubstate enum, and VirtualSubstates collection for accessing system-wide information. Currently supports CurrentEpoch tracking with extensible design for additional virtual state. Essential for consensus and time-based operations requiring global context. |

###### crates/engine_types/src/confidential/

| File | Description |
|------|-------------|
| `claim.rs` | Confidential output claiming mechanism for privacy-preserving asset redemption. Exports ConfidentialClaim struct containing public key, output address, range proof, proof of knowledge (CommitmentSignature), and optional withdraw proof. Used to claim ownership of confidential outputs with cryptographic proof of knowledge while maintaining transaction privacy. Includes TypeScript bindings for frontend integration. |
| `elgamal.rs` | ElGamal encryption implementation for verifiable confidential balances enabling brute-force balance recovery. Exports CompressedElgamalVerifiableBalance (serializable form) and ElgamalVerifiableBalance (cryptographic operations) with conversions between byte and cryptographic types. Implements batched brute force decryption using value lookup tables for efficient balance recovery within specified ranges. Core component for confidential transaction verification. |
| `mod.rs` | Module aggregating confidential transaction and privacy features for the engine. Exports claim handling, ElGamal encryption, proof systems, unclaimed outputs, validation utilities, value lookup tables, and withdraw operations. Provides core cryptographic infrastructure for confidential asset transfers, balance proofs, and privacy-preserving transactions with comprehensive proof validation and encrypted output management. |
| `proof.rs` | Range proof and commitment factory services for confidential transactions using Bulletproofs+. Exports global static instances of CommitmentFactory and BulletproofsPlusService with aggregation factors 1 and 2. Provides message generation utilities for confidential withdraws and viewable balance proof challenges. Core cryptographic infrastructure for zero-knowledge proofs in confidential transactions with domain-separated hashing. |
| `unclaimed.rs` | Unclaimed confidential output representation for privacy-preserving asset storage. Exports UnclaimedConfidentialOutput struct containing Pedersen commitment and encrypted data. Provides address derivation from commitment for output identification. Used to represent confidential outputs that have not yet been claimed by their recipients, maintaining privacy until redemption. Includes TypeScript bindings for frontend integration. |
| `validation.rs` | Confidential proof validation for verifying range proofs and commitment statements in privacy-preserving transactions. Exports ValidatedConfidentialProof and validate_confidential_proof function handling Bulletproofs+ verification, viewable balance proof validation, and commitment verification. Validates output statements, change statements, and revealed amounts while ensuring cryptographic correctness. Core component for confidential transaction security validation. |
| `value_lookup_table.rs` | Simple trait definition for confidential value lookup functionality in zero-knowledge proof systems. The ValueLookupTable trait provides lookup() method for retrieving 32-byte values by u64 identifiers with associated error handling. Used as abstraction for external systems that provide value mappings for confidential transaction validation. Exports: ValueLookupTable trait. No dependencies beyond standard library. Used in confidential transaction processing for value resolution in zero-knowledge proof validation. |
| `withdraw.rs` | Comprehensive confidential withdrawal validation system for privacy-preserving transactions. Implements ValidatedConfidentialWithdrawProof for validating zero-knowledge proofs in confidential asset withdrawals, ConfidentialOutput for encrypted transaction outputs, and validate_confidential_withdraw() function for cryptographic proof verification. Uses Bulletproofs for range proofs, Pedersen commitments for value hiding, and Schnorr signatures for balance proofs. Key exports: ValidatedConfidentialWithdrawProof, ConfidentialOutput, ValidatedConfidentialOutput, validate_confidential_withdraw(). Dependencies: tari_crypto, tari_template_lib, tari_common_types. Critical for confidential transaction security and privacy guarantees. |

###### crates/engine_types/src/serde_with/

| File | Description |
|------|-------------|
| `base64.rs` | Base64 serialization utilities for human-readable and binary format support. Provides serialize() and deserialize() functions that automatically choose base64 encoding for human-readable formats (JSON) and raw bytes for binary formats (bincode). Uses standard base64 encoding with STANDARD engine for consistent cross-platform compatibility. Key exports: serialize(), deserialize(). Dependencies: base64, serde. Used throughout the codebase for encoding binary data in JSON APIs while maintaining efficient binary serialization for internal storage and network protocols. |
| `cbor_value.rs` | CBOR value serialization utilities supporting both human-readable JSON and efficient binary formats. Provides serialize()/deserialize() functions that automatically choose JSON representation for human-readable formats and binary CBOR encoding for non-human-readable formats like bincode. Uses tari_bor wrappers for JSON compatibility and includes comprehensive test coverage for round-trip validation. Key exports: serialize(), deserialize(). Dependencies: tari_bor, serde. Used throughout the system for efficient CBOR data handling in APIs (JSON) and internal storage/networking (binary). |
| `duration.rs` | Duration serialization utilities for configuration values supporting seconds-based representation. Provides seconds module for converting Duration to/from u64 seconds and optional_seconds module for Option<Duration> handling. Key exports: seconds::serialize(), seconds::deserialize(), optional_seconds::serialize(), optional_seconds::deserialize(). Dependencies: std::time::Duration, serde. Used in configuration files and APIs where time durations need human-readable seconds representation while maintaining strong typing with Duration objects. |
| `hex.rs` | Hexadecimal serialization utilities for human-readable and binary format support with vector and option variants. Provides serialize()/deserialize() for single values, vec module for collections, and option module for optional values. Automatically chooses hex encoding for human-readable formats (JSON) and raw bytes for binary formats (bincode). Includes comprehensive test coverage for round-trip serialization validation. Key exports: serialize(), deserialize(), vec::serialize(), vec::deserialize(), option::serialize(), option::deserialize(). Dependencies: hex, serde. Used extensively for encoding cryptographic data, hashes, and binary identifiers in JSON APIs. |
| `mod.rs` | Module declaration file for custom serde serialization utilities providing specialized encoding/decoding functionality. Exports submodules: base64 for base64 encoding, cbor_value for CBOR value handling, duration for time duration serialization, hex for hexadecimal encoding, string for string utilities, and vec for vector serialization. Used throughout the codebase to provide consistent, efficient, and human-readable serialization formats for various data types in both binary and text formats. |
| `string.rs` | String-based serialization utilities for types implementing ToString/FromStr traits with option variant support. Provides serialize()/deserialize() functions that automatically choose string representation for human-readable formats and native serialization for binary formats. Includes option module for Option<T> handling. Key exports: serialize(), deserialize(), option::serialize(), option::deserialize(). Dependencies: serde, std::str::FromStr. Used throughout the system for types that have natural string representations (addresses, hashes, IDs) but need efficient binary serialization. |
| `vec.rs` | Vector serialization utilities for collections supporting both human-readable and binary formats. Provides serialize()/deserialize() functions that convert iterators with key-value pairs to vector tuples for JSON compatibility (circumventing JSON's string-only key requirement) while maintaining native serialization for binary formats. Key exports: serialize(), deserialize(). Dependencies: serde. Used for serializing maps and collections where JSON compatibility requires tuple-based representation but binary formats can use efficient native encoding. |

#### crates/epoch_manager/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo configuration for tari_epoch_manager crate providing epoch lifecycle management and validator committee coordination. Dependencies include storage layers (SQLite optional), cryptographic libraries, common types, and shutdown handling. Service feature enables logging and SQLite storage for production deployment. Core infrastructure for managing blockchain epochs, validator registration, and committee transitions. |

##### crates/epoch_manager/src/

| File | Description |
|------|-------------|
| `error.rs` | Error types for the Tari epoch manager. Defines EpochManagerError enum covering channel communication errors, epoch/committee lookup failures, validator node registration issues, public key validation errors, integer overflow protection, sidechain ID mismatches, and Layer-1 transaction submission failures. Implements IsNotFoundError trait for optional query handling and provides specialized methods for checking registration errors. Comprehensive error handling for validator set management and epoch transitions. |
| `event.rs` | Epoch manager event system providing notifications for epoch changes and shard group assignments. Exports EpochManagerEvent enum with EpochChanged variant containing epoch information and optional registered shard group for local validator. Used for notifying other services about epoch transitions and validator assignment changes affecting consensus participation. |
| `lib.rs` | Main library for epoch and validator set management in Tari Ootle. Exports EpochManagerReader trait for epoch queries, EpochManagerError for error handling, epoch_event_oracle for epoch event monitoring, service module (when service feature enabled) for epoch management operations, and event types for epoch lifecycle management. Central component for managing validator committee rotation, epoch transitions, and validator set consensus in the Byzantine Fault Tolerant network. |
| `traits.rs` | Comprehensive trait system defining the epoch management architecture for validator consensus and committee coordination. Defines EpochManagerSpec for type configuration, EpochManagerWriter for state mutations (validator registration/deactivation, epoch activation), EpochManagerReader for queries (committees, validator nodes, epoch info), and supporting traits (EpochUtxoStore, LayerOneTransactionSubmitter, TemplateDownloader). Features async interfaces, committee management, validator lifecycle, shard-based organization, and template downloading. Key exports: EpochManagerSpec, EpochManagerWriter, EpochManagerReader, EpochUtxoStore, LayerOneTransactionSubmitter, TemplateDownloader. Dependencies: tari_ootle_common_types, tari_ootle_storage, tari_sidechain, tokio. Central to validator network coordination and consensus mechanisms. |

###### crates/epoch_manager/src/epoch_event_oracle/

| File | Description |
|------|-------------|
| `event.rs` | Core event types for epoch management system defining all possible blockchain events that trigger epoch transitions and validator network changes. The EpochEvent enum covers validator lifecycle events (registration, exit, set changes), template downloads, confidential outputs, eviction proofs, and epoch transitions. ValidatorNodeChange enum defines Add/Remove operations with comprehensive validator metadata including shard keys, activation epochs, and vote power. Features extensive Display implementations for debugging and logging. Key exports: EpochEvent, ValidatorNodeChange, error(). Dependencies: tari_common_types, tari_engine_types, tari_ootle_common_types, tari_sidechain, tari_template_lib_types. Used throughout epoch management for event-driven validator network coordination. |
| `mod.rs` | Module declaration file for epoch event oracle system providing event-driven epoch management. Re-exports all types and traits from event and traits modules for unified access to EpochEvent types and EpochEventOracle trait definitions. Used as central entry point for epoch event handling across the validator network coordination system. |
| `traits.rs` | Core trait definition for epoch event generation providing cancel-safe async event streaming. The EpochEventOracle trait defines next_epoch_event() method that returns Future<Option<EpochEvent>> with Send bounds for safe async operation. Implementations must ensure cancel safety and return None when no further events are available. Used as abstraction layer for different epoch event sources (base layer monitoring, configured intervals, hybrid approaches). Exports: EpochEventOracle trait. Dependencies: crate::epoch_event_oracle::EpochEvent. Central interface for epoch transition detection in validator networks. |

###### crates/epoch_manager/src/service/

| File | Description |
|------|-------------|
| `config.rs` | Configuration structure for epoch manager service parameters controlling validator network behavior. The EpochManagerConfig defines base_layer_confirmations for blockchain finality requirements, committee_size for consensus group sizing, validator_node_sidechain_id for optional sidechain integration, num_preshards for network sharding configuration, and fee_claim_public_key for validator reward claiming. Exports: EpochManagerConfig struct. Dependencies: tari_ootle_common_types, tari_template_lib_types, std::num::NonZeroU32. Used to configure epoch management behavior, committee formation, and validator economic parameters across the network. |
| `epoch_manager.rs` | Epoch manager service implementation providing validator committee management, epoch transitions, and shard group coordination. Manages validator registration, committee formation, epoch progression, layer one transaction monitoring, and validator set updates. Core service for coordinating distributed consensus across epochs with proper validator lifecycle management, committee membership tracking, and shard group assignment. |
| `epoch_manager_service.rs` | Main epoch manager service implementing the core event loop for validator network coordination. The EpochManagerService orchestrates epoch events, validator lifecycle management, template downloads, and request handling through async select loops. Key features include validator registration/deactivation, epoch activation, committee management, template downloading, and comprehensive error handling. Provides spawn() method for service initialization and run() method for main event processing. Handles both epoch events and service requests concurrently with shutdown signal support. Dependencies: tari_ootle_storage, tokio, log. Central service for epoch-based validator network management and coordination. |
| `error.rs` | Error conversion implementation for integrating SQLite storage errors into the epoch manager error system. Provides From trait implementation to convert SqliteStorageError into EpochManagerError::StorageError variant. Simple error mapping layer that maintains error context while integrating database storage errors into the broader epoch management error hierarchy. Exports: From<SqliteStorageError> for EpochManagerError. Dependencies: tari_ootle_storage_sqlite, crate::EpochManagerError. Used for consistent error handling across storage operations in epoch management functionality. |
| `handle.rs` | Handle implementation for epoch manager service providing async API access through message passing. The EpochManagerHandle implements both EpochManagerReader and EpochManagerWriter traits via oneshot channels for request-response communication. Key features include validator node management, committee queries, epoch operations, fee claim operations, and eviction handling. Provides both async trait methods and direct access methods like get_current_epoch() for non-blocking operations. Uses atomic operations for current epoch tracking and broadcast channels for event subscriptions. Dependencies: tokio::sync, tari_ootle_common_types. Essential client interface for epoch management operations across the validator network. |
| `initializer.rs` | Service initializer providing factory function for spawning epoch manager service instances. The spawn_service() function creates EpochManagerService with all required dependencies including global database, epoch event oracle, UTXO store, template downloader, and layer one transaction submitter. Returns both EpochManagerHandle for client access and JoinHandle for task management. Exports: spawn_service(). Dependencies: tari_ootle_storage, tari_shutdown, tokio. Used to bootstrap epoch manager services in validator node initialization. |
| `mod.rs` | Module declaration file for epoch manager service components providing unified access to all service types and utilities. Re-exports EpochManagerConfig, EpochManager, EpochManagerHandle, spawn_service, and request/response types for comprehensive epoch management functionality. Used as central entry point for epoch manager service initialization and interaction across the validator network. |
| `types.rs` | Request/response type definitions for epoch manager service communication using oneshot channels. The EpochManagerRequest enum defines all possible service operations including validator management, committee queries, epoch operations, scanning control, fee operations, and eviction handling. Uses type alias Reply<T> for consistent oneshot response channel typing. Key operations include CurrentEpoch, GetValidatorNodeByPublicKey, AddValidatorNodeRegistration, GetCommittees, ActivateEpoch, and GetRandomCommitteeMember. Dependencies: tari_ootle_common_types, tari_ootle_storage, tari_sidechain, tokio::sync. Used for type-safe message passing between epoch manager clients and service implementation. |

#### crates/epoch_oracles/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for epoch oracles providing different mechanisms for epoch event generation and blockchain synchronization. Dependencies include base layer integration (tari_base_node_client, tari_core), storage systems (tari_ootle_storage_sqlite), and epoch management components. Features 'base_layer' for optional base layer blockchain integration. Dev dependencies include tokio for async testing. Used to build oracle systems that determine epoch transitions through configured intervals, base layer monitoring, or hybrid approaches for validator network coordination. |

##### crates/epoch_oracles/src/

| File | Description |
|------|-------------|
| `lib.rs` | Main module for epoch oracle implementations providing different strategies for epoch event generation. Defines EpochOracle enum with BaseLayer (base layer blockchain monitoring), Configured (interval-based epochs), and Hybrid (combined approach) variants. Implements EpochEventOracle trait for unified epoch event handling across different oracle types. Conditional compilation features enable base layer integration when needed. Exports: EpochOracle enum, EpochEventOracle implementation. Dependencies: tari_epoch_manager. Used as central abstraction for epoch transition detection in validator networks. |
| `store.rs` | Storage abstraction and key definitions for epoch oracle persistence. EpochOracleStore trait provides get/set operations for serializable data. StoreKey enum defines all oracle storage keys including base layer scanning state (tip, height, hashes) and configured oracle state (current epoch, initialization). Implements store trait for GlobalDb with SqliteGlobalDbAdapter. Used by both base layer and configured oracles for state persistence across restarts. |

###### crates/epoch_oracles/src/base_layer/

| File | Description |
|------|-------------|
| `mod.rs` | Base layer blockchain scanner and oracle implementation for epoch management. Implements BaseLayerOracle which monitors Tari base layer blockchain for sidechain UTXOs, validator registrations, template registrations, burned outputs, and eviction proofs. Key components: BaseLayerOracle (async scanner with configurable intervals), BaseLayerOracleInner (state management and blockchain interaction), scan_blockchain() (main scanning logic), sync_blockchain() (synchronizes with base layer), and EpochEvent generation. Supports reorg detection, sidechain ID filtering, height lag for stability, and persistent state storage. Used by validator nodes to track epoch changes and validator set updates from base layer transactions. |

###### crates/epoch_oracles/src/configured/

| File | Description |
|------|-------------|
| `config.rs` | Configuration types for configured epoch oracles with static validator sets. Defines Config struct with epoch_time (optional duration), initial_epoch, and validators list. Validator struct contains public_key, claim_key, shard_group, and registration_epoch. Includes calculate_shard_key() method that generates deterministic shard keys within the validator's assigned ShardGroup using hash of validator properties. Used for testing and networks with predefined validator sets rather than dynamic base layer registration. |
| `epoch_ticker.rs` | Epoch progression timing mechanism for configured epoch oracles. Defines EpochTicker trait for polling epoch transitions and EpochTickerData for epoch information. IntervalEpochTicker implements time-based epoch progression with tokio::time::Interval, supporting burst tick behavior for missed ticks. Provides rapid initial progression to catch up to initial_epoch, then follows configured interval timing. Generates static epoch hashes based on epoch numbers. Used by ConfiguredEpochOracle for deterministic epoch timing in testing and static validator scenarios. |
| `mod.rs` | Module exports for configured epoch oracle components. Re-exports config types (Config, Validator), epoch timing (EpochTicker, IntervalEpochTicker, EpochTickerData), and oracle implementation (ConfiguredEpochOracle). Provides clean interface for configured epoch oracle functionality used in testing and static validator network configurations. |
| `oracle.rs` | Configured epoch oracle implementation using static validator sets and time-based epochs. ConfiguredEpochOracle manages pre-configured validators, queues validator activations by epoch, and generates epoch events on timer intervals. Key features: initialize() loads queued validators from config, prepare_next_epoch() activates validators and emits registration events, poll() manages async event generation. Supports EpochEventOracle trait for integration with epoch manager. Uses persistent store for state recovery and custom ticker for epoch progression timing. Primarily used for testing and development environments. |

###### crates/epoch_oracles/src/hybrid/

| File | Description |
|------|-------------|
| `mod.rs` | Module exports for hybrid epoch oracle components. Re-exports oracle implementation and watch ticker functionality. Hybrid oracles combine multiple epoch information sources for redundancy and enhanced reliability in validator network epoch management. |
| `oracle.rs` | Hybrid epoch oracle implementation combining configured and base layer oracles. HybridEpochOracle coordinates between ConfiguredEpochOracle (for static validator events) and BaseLayerOracle (for blockchain events), using watch channels to synchronize epoch changes. Key logic: should_emit_configured() and should_emit_base_layer() methods determine event routing, EpochChanged events from base layer trigger WatchEpochTicker updates, and biased tokio::select! prioritizes configured oracle events. Provides unified epoch event stream from multiple sources. |
| `watch_ticker.rs` | Watch-based epoch ticker for hybrid epoch oracles. WatchEpochTicker implements EpochTicker trait using tokio::sync::watch channels for epoch progression. watch_ticker() factory creates connected ticker and sender pair. Provides async poll-based interface that waits for epoch changes via watch receiver, properly handles watch channel closure, and marks initial dummy data as unchanged. Used by HybridEpochOracle to coordinate epoch timing between base layer and configured sources. |

#### crates/indexer_lib/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for network scanning and indexing library providing blockchain state monitoring and data retrieval functionality. Dependencies include epoch management (tari_epoch_manager), validator node RPC communication (tari_validator_node_rpc), and engine types (tari_engine_types). Uses async-trait for async trait definitions and thiserror for error handling. Used to build indexing services that scan validator networks, cache blockchain state, and provide efficient queries for blockchain data and substate information. |

##### crates/indexer_lib/src/

| File | Description |
|------|-------------|
| `error.rs` | Comprehensive error types for indexing operations and validator network interactions. The IndexerError enum covers epoch management errors, validator node communication failures, substate retrieval issues, cache operation errors, and transaction parsing problems. Key variants include AllRequestsFailed for network failures, InvalidSubstateState/Value for data corruption, NotFoundTransaction for missing data, and SubstateCacheError for cache operations. Exports: IndexerError enum with From implementations. Dependencies: tari_engine_types, tari_epoch_manager, crate::substate_cache. Used throughout indexing services for robust error handling and network resilience. |
| `lib.rs` | Library for Tari blockchain indexing functionality. Exports error handling, substate_cache for caching substate data, substate_decoder for parsing blockchain state, substate_scanner for scanning blockchain events with Byzantine fault tolerance, and common types. Provides core infrastructure for the Tari Indexer application to efficiently scan, decode, and cache blockchain data for GraphQL and JSON-RPC API access. |
| `substate_cache.rs` | Async trait definition and types for substate caching system providing efficient blockchain state storage and retrieval. The SubstateCache trait defines read()/write() operations for SubstateCacheEntry containing version and SubstateResult data. SubstateCacheError provides unified error handling for cache operations. Uses async-trait for async method support and string-based addressing for cache keys. Key exports: SubstateCache trait, SubstateCacheEntry, SubstateCacheError. Dependencies: async-trait, serde, tari_validator_node_rpc. Used in indexing services for efficient substate data caching and retrieval optimization. |
| `substate_decoder.rs` | Substate analysis utility for discovering cross-references and dependencies between blockchain substates. The find_related_substates() function recursively scans Component, NonFungible, Vault, and Resource substates to extract references to other substates using IndexedWellKnownTypes for structured data analysis. Handles component state inspection, NFT data/mutable data scanning, vault-to-resource relationships, and resource auth hook references. Returns Vec<SubstateId> for dependency tracking. Key exports: find_related_substates(). Dependencies: tari_engine_types, log, std::collections::HashSet. Used in indexing services for building substate dependency graphs and relationship mapping. |
| `substate_scanner.rs` | Substate scanning service for Tari Indexer to retrieve blockchain state from validator committees. Exports SubstateScanner with get_substate() and get_specific_substate_from_committee() methods. Features Byzantine fault tolerance (f+1 validator queries), substate caching, committee shuffling, and automatic failover. Dependencies: EpochManagerReader, ValidatorNodeClientFactory, SubstateCache. Used by: indexer to reliably fetch substate data from distributed validator network with consensus validation. |
| `types.rs` | Core data types for the Tari indexer service. Defines NonFungibleSubstate struct containing index (u64), address (SubstateId), and substate (Substate) for indexing non-fungible token data. Provides serialization support for storing and querying NFT substate information in the indexer database. Used by indexer services to structure and persist blockchain substate data for efficient querying. |

#### crates/p2p/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for peer-to-peer networking and message types in the Tari Ootle distributed validator network. Dependencies include comprehensive Tari ecosystem components (consensus, crypto, networking, storage), protobuf for message serialization (prost), and BOR encoding. Build dependencies include proto_builder for generating Rust code from protobuf definitions. Used to build P2P communication infrastructure for validator nodes, consensus messaging, transaction propagation, and network coordination across the distributed blockchain network. |
| `build.rs` | Build script for P2P crate protobuf compilation. Uses proto_builder::ProtobufCompiler to compile protocol buffer definitions from the proto/ directory. Generates Rust types from .proto files for P2P communication protocols. Includes rerun directives to recompile when protobuf files change. Essential for building P2P networking layer with type-safe protocol buffer message serialization. |

##### crates/p2p/proto/

| File | Description |
|------|-------------|
| `common.proto` | Common protocol buffer message definitions for Tari P2P communication. Defines fundamental types: Signature (public_nonce, signature), SignatureAndPublicKey (adds public_key), SubstateAddress (bytes), and Epoch (uint64). These base types are used across all P2P protocols for cryptographic signatures, addressing, and epoch management. Part of tari.dan.common package providing shared message types for the distributed application network. |
| `consensus.proto` | Protocol buffer definition for HotStuff consensus messaging in Tari Ootle. Defines consensus message types including NewView, Proposal, ForeignProposal, Vote, timeout handling, and sync requests. Specifies QuorumCertificate, Block, and Committee structures. Core protocol specification enabling Byzantine Fault Tolerant consensus communication between validator nodes. |
| `network.proto` | Network-level protocol buffer definitions for P2P communication. Defines TariMessage wrapper with oneof message types and message_tag for debugging. NetworkAnnounce contains identity and peer claim data. IdentitySignature provides versioned signatures with timestamps. PeerIdentityClaim includes network addresses and identity signatures for peer discovery and authentication. Imports common, transaction, and consensus protobuf definitions. Part of tari.dan.network package supporting peer-to-peer networking protocols. |
| `rpc.proto` | RPC protocol buffer definitions for validator node communication. Defines request/response pairs for contract method invocation (InvokeReadMethod, InvokeMethod), transaction submission, peer discovery, state synchronization (VnStateSync, SyncBlocks, SyncState), substate queries, checkpoint retrieval, and template synchronization. Includes status enums (Status, SubstateStatus, PayloadResultStatus) and complex types like StateTransition, QuorumCertificates, and EpochCheckpoint. Supports streaming protocols for large data sets. Part of tari.dan.rpc package for validator node API. |
| `transaction.proto` | Protocol buffer definition for Tari transaction messaging and instruction types. Defines Transaction, Instruction types (function calls, methods, template publishing, account creation), commitment signatures, and allocatable address types. Comprehensive specification for smart contract execution instructions and transaction serialization in the Ootle network. |

##### crates/p2p/src/

| File | Description |
|------|-------------|
| `block_sync.rs` | Block synchronization helper methods for SyncBlocksResponse protobuf messages. Provides convenience methods to extract specific data types from the sync_data oneof field: into_block(), into_quorum_certificates(), substate_count(), transaction_count(), into_substate_update(), into_transaction(), transaction_receipt_count(), and into_transaction_receipt(). Simplifies handling of streaming block synchronization responses where different message types are sent sequentially. Used by validator nodes during blockchain synchronization. |
| `encoding.rs` | Binary encoding and decoding utilities for P2P message serialization. Provides encode_to_vec() for serializing any Serialize type to bytes using tari_bor, and decode_from_slice() for deserializing any DeserializeOwned type from bytes. Includes error context with type information for debugging. Used throughout P2P conversion layer for binary data marshaling in protobuf messages. |
| `lib.rs` | Peer-to-peer networking library providing block synchronization, message handling, and protocol buffer communication. Exports message types, message specifications, encoding utilities, conversions, and network protocol implementations. Core networking infrastructure for validator node communication, consensus message propagation, and blockchain data synchronization between peers. |
| `message.rs` | P2P message types and implementations for network communication. Defines TariMessage enum with NewTransaction variant, provides type identification, message tagging based on transaction IDs, and display formatting. NewTransactionMessage struct wraps Transaction for network transmission. Used as the primary message envelope for P2P gossip and communication protocols. |
| `message_spec.rs` | Message specification for Tari networking layer. Defines TariMessagingSpec implementing MessageSpec trait with type mappings for consensus gossip messages, general messages, and transaction gossip messages using protobuf types. Provides the networking layer with standardized message type definitions for P2P communication protocols. |
| `proto.rs` | [GENERATED] Protobuf module inclusions for P2P communication. Includes compiled protobuf modules for transaction, consensus, network, RPC, and common message types from the build process. Suppresses clippy warnings for large enum variants in network and RPC modules. Auto-generated by build.rs during compilation. |
| `utils.rs` | Utility functions for P2P message handling. Provides checked_copy_fixed() function for safely copying byte slices to fixed-size arrays with length validation. Used in type conversions to ensure byte array integrity when converting between protobuf and Rust types, particularly for cryptographic data structures. |

###### crates/p2p/src/conversions/

| File | Description |
|------|-------------|
| `common.rs` | Type conversion implementations between protobuf and Rust types for common P2P data structures. Provides TryFrom/From implementations for: Signature/SchnorrSignatureBytes conversions, SignatureAndPublicKey/ValidatorSignatureBytes and TransactionSignature conversions, SubstateAddress conversions, and Epoch conversions. Handles byte array validation and error handling for cryptographic types. Essential for marshaling/unmarshaling protobuf messages to strongly-typed Rust structs in P2P communication. |
| `consensus.rs` | Comprehensive type conversions between protobuf and Rust types for consensus-related P2P communication. Implements TryFrom/From for: HotstuffMessage variants (NewView, Proposal, Vote, etc.), consensus data structures (Block, QuorumCertificate, TimeoutCertificate), transaction atoms and commands, evidence and decisions, validator metadata, substate records, and state transitions. Handles complex nested conversions with proper error handling, validation, and encoding/decoding of binary data. Critical for consensus protocol serialization in validator node communication. |
| `mod.rs` | Module declarations for P2P protocol buffer type conversions. Re-exports conversion modules for common types, consensus types, network types, RPC types, and transaction types. Provides centralized organization of all protobuf to Rust type conversion implementations used throughout the P2P communication layer. |
| `network.rs` | Type conversions for network-level P2P messages. Implements TryFrom/From conversions between TariMessage enum and proto::network::TariMessage protobuf. Handles message tag propagation and conversion of NewTransaction messages. Provides bidirectional conversion with error handling for network message serialization/deserialization in P2P communication layer. |
| `rpc.rs` | Type conversions for RPC-related P2P message types. Implements TryFrom/From conversions for: SubstateCreatedProof, SubstateDestroyedProof, SubstateUpdate, SubstateData, SubstateValueOrHash, StateTransition, StateTransitionId, and EpochCheckpoint. Handles binary encoding/decoding using tari_bor for complex data structures. Includes validation and DoS protection (shard root limits). Used by validator nodes for RPC communication and state synchronization. |
| `transaction.rs` | Extensive type conversions for transaction-related P2P message types. Implements TryFrom/From conversions for: NewTransactionMessage, Transaction (using binary encoding), Instruction variants (CreateAccount, CallFunction, CallMethod, etc.), InstructionArg, ComponentCall, SubstateRequirement, VersionedSubstateId, cryptographic types (CommitmentSignature, ConfidentialWithdrawProof), confidential transaction components, access control types (OwnerRule, AccessRules), and workspace management types. Critical for transaction serialization in P2P communication. |

#### crates/rpc_state_sync/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for RPC-based state synchronization service. Dependencies include consensus, storage, P2P, epoch management, RPC framework, state tree, template manager, and validator node RPC components. Provides state synchronization functionality for validator nodes to catch up with network state across epochs and shard boundaries. |

##### crates/rpc_state_sync/src/

| File | Description |
|------|-------------|
| `error.rs` | Error types for RPC state synchronization operations. Defines RpcStateSyncError with variants for epoch manager errors, RPC errors, storage errors, validator node client errors, transaction pool errors, invalid responses, block safety failures, sync failures, proposal validation errors, state tree errors, state root mismatches, template manager errors, task join failures, template sync failures, and missing committees. Includes error conversion traits and remote error handling. |
| `lib.rs` | P2P RPC state synchronization protocol for validator nodes. Provides error handling, state sync implementation, and statistics tracking for efficient state synchronization between validator nodes. Critical component enabling new validators to catch up with network state and maintain consensus across the distributed network. |
| `manager_old.rs` | Legacy RPC-based consensus synchronization manager. RpcStateSyncManager implements SyncManager trait for syncing blocks, transactions, and state between validator nodes. Features: sync_blocks() streams block data from peers, processes dummy blocks for leader failures, validates state merkle trees, commits state updates after 3-chain confirmation, and handles cross-epoch synchronization. Includes committee management, peer connection handling, and comprehensive validation. Marked as 'old' indicating this is likely superseded by newer state sync implementations. |
| `state_sync.rs` | RPC-based state synchronization implementation for validator nodes. RpcStateSyncClientProtocol manages state sync across epochs and shards by: fetching and validating epoch checkpoints, streaming state transitions from peer validators, applying substate updates to local state tree, syncing templates, validating state roots, and coordinating multi-shard synchronization. Implements SyncManager trait with comprehensive error handling and recovery. Critical for validator nodes joining committees or catching up after downtime. |
| `stats.rs` | Statistics tracking for state synchronization operations. StateSyncStats struct tracks total_transitions, total_time, and total_requests during sync operations. Provides Display implementation for formatted logging and monitoring of sync performance. Used by RpcStateSyncClientProtocol to measure and report synchronization metrics. |

#### crates/state_store_rocksdb/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for RocksDB-based state store implementation. Dependencies include Tari storage interfaces, common types, transactions, engine types, state tree, template types, consensus types, plus RocksDB, serialization libraries (borsh, bincode), and utility crates. Provides persistent storage backend for validator node state, substates, blocks, and consensus data using RocksDB database engine. |

##### crates/state_store_rocksdb/src/

| File | Description |
|------|-------------|
| `cf_api.rs` | Column family API for RocksDB operations with type-safe codecs. Provides DbContext for database access and CfContext for column family operations. Key features: get/put/delete operations with automatic key/value encoding/decoding, iterators with ordering and range support, multi-get operations, prefix operations, existence checks, count operations, and query-specific methods. Supports both regular and custom codec operations for flexible data access patterns. Central abstraction for type-safe RocksDB column family interactions. |
| `error.rs` | Error types for RocksDB storage operations. RocksDbStorageError enum covers: RocksDbError (low-level database errors), NotFound (missing keys), EncodeError/DecodeError (serialization failures), MalformedData (data corruption), ConflictingInsert (duplicate key attempts), QueryError (query failures), and ColumnFamilyNotFound (schema issues). Implements conversion to StorageError trait and IsNotFoundError for error handling patterns. Provides detailed error context including operation names and key information. |
| `info.rs` | RocksDB column family information structure for database introspection and monitoring. ColumnFamilyInfo contains metadata including name (column family identifier), num_entries (record count), and total_entries_bytes (storage size). Used for database monitoring, performance analysis, storage optimization, and administrative operations. Essential for blockchain database maintenance, enabling operators to monitor storage usage, optimize performance, and make informed decisions about database configuration and resource allocation in the distributed consensus system. |
| `key.rs` | Composite key implementation for efficient RocksDB key construction and manipulation. CompositeKey<L> uses SmallBytes for fixed or dynamic storage, enabling zero-copy key building from multiple parts. Provides push(), try_push(), section_iter(), and conversion methods for flexible key composition. SectionIter enables parsing composite keys back into components with next_be_u64() for numeric fields. Includes comprehensive error handling and testing. Essential for RocksDB key space organization, enabling efficient prefix queries, multi-part keys for indexing, and performance optimization in the blockchain state store through careful key layout design. |
| `lib.rs` | RocksDB-based state store implementation for production validator nodes. Provides column family management, codecs, error handling, reader/writer abstractions, and utility functions. High-performance persistent storage backend optimized for consensus state, transaction history, and substate management in production environments. |
| `options.rs` | RocksDB database configuration options for state store management. DatabaseOptions contains state_history_length (default 100 versions) for state tree historical preservation and epoch_history_length (default 1 epoch) for blockchain data retention. Controls garbage collection behavior: state_history_length limits preserved versions of state tree stale nodes, while epoch_history_length determines how many previous epochs of blocks and foreign proposals to retain. Essential for balancing storage efficiency with data availability requirements, enabling configurable retention policies for different deployment scenarios in the distributed blockchain network. |
| `read_only.rs` | Generic read-only wrapper providing type-safe read-only access to any RocksReader implementation. ReadOnly<TX> wraps inner RocksReader types and delegates all read operations (get_pinned_cf, iterator_cf, multi_get_cf) while preventing write access through type system. Enables creating read-only views of transactions, snapshots, or database instances without runtime overhead. Essential for enforcing read-only access patterns, creating safe concurrent readers, and providing immutable database views for operations that should not modify state in the blockchain consensus system. |
| `read_only_ctx.rs` | Read-only context wrapper for database access providing column family creation with type safety. ReadOnlyContext wraps ReadOnlyDb and provides cf() method for creating typed CfContext instances with error handling for missing column families. Includes detailed error reporting with column family type names and CF names for debugging. Essential for structured database access patterns, enabling type-safe column family operations in read-only scenarios while maintaining comprehensive error reporting for operational diagnostics in the blockchain state store. |
| `reader.rs` | Comprehensive read-only state store implementation providing all blockchain data access operations. RocksDbStateStoreReadTransaction implements StateStoreReadTransaction trait with 1900+ lines of functionality including: consensus state queries (epochs, votes, proposals), block operations (retrieval, pagination, ancestry checks), transaction management (executions, pool operations), substate operations (locks, records, state trees), and specialized queries for validators, certificates, and foreign proposals. Includes transaction count, pagination with filtering/sorting, and comprehensive error handling. Essential core component enabling all blockchain data reads across the distributed consensus system. |
| `snapshot.rs` | Snapshot context wrapper providing consistent point-in-time database access with column family support. SnapshotContext wraps database and snapshot instances, providing cf() method for creating typed CfContext instances from snapshots. Enables consistent multi-operation reads without interference from concurrent writes. Includes comprehensive error handling with type information for missing column families. Essential for blockchain state consistency guarantees, enabling atomic multi-key reads, block validation, and state tree operations requiring consistent data views across the distributed consensus system. |
| `store.rs` | Main RocksDB state store implementation providing complete blockchain storage functionality. RocksDbStateStore manages database lifecycle including opening, migration, column family management, and both read-only and transactional access patterns. Defines all_column_families_iter() listing 50+ column families for comprehensive blockchain data storage. Implements StateStore trait with create_read_tx() and create_write_tx() methods. Includes database migration support, column family statistics, and administrative operations like compact_all(). Essential core component providing persistent storage foundation for the entire distributed consensus system with support for both read-only nodes and full validator functionality. |
| `tests.rs` | RocksDB testing utilities and integration tests for database operations. Provides create_rocksdb() helper for creating temporary test databases with specified column families, ctx() helper for database context creation, and test ID generation functions (block_id_from_seed, transaction_id_from_seed). Defines test column families TwoBlocksCf and EpochHeightBlock with corresponding query types for testing prefix queries and iteration patterns. Includes integration tests validating put/get operations, transactions, and database consistency. Essential for ensuring database functionality correctness across all storage operations in the blockchain consensus system. |
| `utils.rs` | RocksDB storage utilities providing serialization, deserialization, and I/O helper functions. Provides bincode_encode() and bincode_decode() for consistent binary serialization with comprehensive error handling and type information. Includes read_to_fixed() and read_n_bytes() for binary data parsing from byte streams. Contains now() function for timestamp creation using UtcDateTime conversion. Essential utility layer enabling consistent data serialization across all RocksDB operations, providing standardized encoding/decoding with detailed error reporting and supporting binary protocol operations in the blockchain state store. |
| `writer.rs` | Comprehensive write transaction implementation for blockchain state modifications. RocksDbStateStoreWriteTransaction implements StateStoreWriteTransaction trait with 1900+ lines providing all blockchain write operations including: block operations (insertion, commits, diffs), consensus state updates (votes, proposals, certificates), transaction management (pool updates, execution tracking), substate operations (locks, state transitions, tree updates), and epoch management (checkpoints, validator stats). Includes comprehensive foreign proposal handling, transaction pool management, and state tree operations. Essential core component enabling all blockchain state modifications with ACID transaction guarantees in the distributed consensus system. |

###### crates/state_store_rocksdb/src/codecs/

| File | Description |
|------|-------------|
| `bincode.rs` | Bincode-based database codec for RocksDB serialization. Implements DbCodec trait using bincode for encoding/decoding serializable types. Provides Bincode codec for full encode/decode operations and BincodeRef codec for encode-only operations (decode panics). Uses phantom types for type safety and provides default implementations. Used throughout RocksDB storage layer for serializing Rust types to database-compatible byte arrays. |
| `block_diff.rs` | RocksDB encoding/decoding implementation for BlockDiffKey used in block diff storage. Provides two codec variants: BlockIdSeqSubstateIdVersion (encodes in block_id, sequence, substate_id, version order) and SubstateIdBlockIdVersionSeq (encodes in substate_id, block_id, version, sequence order). Each codec implements DbCodec trait with encode() and decode() methods for serializing BlockDiffKey structures containing block_id, substate_id, version, is_up flag, and sequence number. Uses big-endian byte encoding for numeric values and delegates substate ID encoding to SubstateIdCodec. Essential for blockchain state diff storage in RocksDB with different indexing patterns for efficient querying. |
| `bytes.rs` | Byte array codecs for RocksDB serialization. BytesCodec provides encoding for types implementing AsRef<[u8]> and TryFrom<&[u8]>. FixedBytesCodec<LEN> handles fixed-length byte arrays with compile-time length checking. Includes type aliases: FixedBytesCodec32, TransactionIdCodec, and BlockIdCodec for common 32-byte identifiers. Note: BytesCodec not suitable for prefix operations unless target type handles excess bytes gracefully. |
| `column.rs` | Column differentiation codecs for RocksDB key prefixing. Column<COL> provides 32-bit column identifiers encoded as big-endian bytes for separating logical columns within shared column families. ByteColumn<COL> uses single-byte column identifiers for more compact keys. ColumnCodec implements encoding/decoding with validation to ensure correct column bytes. Warning: Not recommended for prefix lookups due to potential key collision issues between <foo> and <column><foo> patterns. |
| `misc.rs` | Miscellaneous database codecs for common types. UnitCodec encodes/decodes unit type () as empty bytes. BoolCodec encodes booleans as single bytes (0/1). NumberCodec<T> provides big-endian encoding for numeric types: u8, u32, u64, Epoch, NodeHeight, and Shard. Includes type aliases: EpochCodec, ShardCodec, NodeHeightCodec. All use big-endian byte order for consistent lexicographic sorting in RocksDB keys. |
| `mod.rs` | Database codec system for RocksDB type serialization. Defines DbCodec trait for encode/decode operations and EncodeVec type alias for efficient small buffer allocation (stack for <100 bytes, heap otherwise). Re-exports all codec implementations: bincode, bytes, column, misc, public_key, state_tree, substate_id, substate_lock, tuple, and block_diff codecs. Provides DefaultCodec and DefaultCodecRef type aliases using Bincode. Central coordination for type-safe database serialization. |
| `public_key.rs` | Database codec for RistrettoPublicKeyBytes serialization. PublicKeyCodec implements DbCodec trait for encoding/decoding Ristretto public keys as byte arrays. Uses RistrettoPublicKeyBytes::from_bytes() for validation during decoding. Provides type-safe conversion between cryptographic public key types and database byte representation in RocksDB storage. |
| `small_bytes.rs` | Efficient immutable byte buffer with stack/heap allocation optimization. SmallBytes<L> uses SmallVec to store buffers on stack if under L bytes, otherwise heap-allocates. Provides constructors from slices, arrays, vectors, and multiple slices. Includes utility methods for length, emptiness, and allocation type checking. Used as EncodeVec in database codecs to minimize allocations for small keys/values while supporting larger data when needed. |
| `state_tree.rs` | RocksDB encoding/decoding implementation for NodeKey from the Jellyfish Merkle Tree state storage system. NodeKeyCodec encodes NodeKey structures containing version and nibble path into byte arrays for RocksDB storage. Handles both even and odd nibble paths correctly, encoding version as big-endian u64, number of nibbles as u64, and the nibble path bytes. Includes comprehensive tests with JellyfishMerkleTree integration showing batch operations, stale node tracking, and multi-version state management. Critical component for persistent state tree storage enabling efficient state proofs and historical state queries in the Tari blockchain. |
| `substate_id.rs` | Specialized codec for SubstateId and VersionedSubstateIdRef database encoding. SubstateIdCodec uses Borsh serialization with stack/heap optimization based on encoded size. Supports prefix operations via deserialize_reader for partial decoding. Handles SubstateId references and VersionedSubstateIdRef encoding (concatenates substate ID + version bytes). Includes comprehensive tests for encoding validation and prefix compatibility with compound keys. |
| `substate_lock.rs` | RocksDB encoding/decoding implementation for SubstateLockKey supporting multiple key ordering variants. Provides three codec implementations with different tuple orderings: (TransactionId, SubstateId, BlockId, NodeHeight), (BlockId, SubstateId, TransactionId, NodeHeight), and (SubstateId, TransactionId, BlockId, NodeHeight). Each variant enables different query patterns for substate lock management in consensus. SubstateLockKeyCodec implements DbCodec trait with encode/decode methods, using helper functions for consistent field decoding. Essential for tracking substate locks across transactions and blocks, enabling conflict detection and resolution in the distributed validator network. |
| `substate_transaction_index.rs` | RocksDB encoding/decoding implementation for SubstateTransactionIndexKey used for tracking substate changes within transactions. SubstateTransactionKeyCodec encodes keys containing transaction_id, versioned_substate_id, and is_up boolean flag. The codec serializes transaction ID as fixed hash bytes, substate ID using SubstateIdCodec, version as big-endian u32, and is_up as single byte boolean. Includes helper methods for decoding individual field types with comprehensive error handling. Contains unit tests validating encode/decode roundtrip correctness. Critical for indexing which substates are modified by specific transactions, enabling transaction rollback and state transition tracking. |
| `tuple.rs` | Tuple encoding codecs for composite database keys. TupleBytesCodec handles 2-tuple and 3-tuple encoding for types implementing AsRef<[u8]> with fixed byte lengths. Provides tuple codec implementations using individual codecs for each element. StateTransitionIdCodec supports different field ordering (Shard,Seq,Epoch) and (Epoch,Shard,Seq) for different query patterns. FixedByteLength trait defines byte sizes for various types (BlockId, Epoch, TransactionId, etc.). Essential for compound key encoding in RocksDB column families. |

###### crates/state_store_rocksdb/src/column_families/

| File | Description |
|------|-------------|
| `block.rs` | RocksDB column family definitions for block storage and indexing. BlockCf stores blocks keyed by BlockId using default bincode serialization. EpochHeightIndex provides secondary index for blocks by (Epoch, NodeHeight, BlockId) for efficient range queries. ByEpochHeightQuery enables queries by epoch and height prefix. ByEpochQuery enables queries by epoch prefix only. Supports efficient block retrieval by height, epoch-based iteration, and temporal block queries. |
| `block_diff.rs` | RocksDB column family definitions for storing block state differences and related indexes. Defines BlockDiffCf storing ordered substate transitions with schema (BlockId, Seq, SubstateId, Version, IsUp) -> SubstateChange, enabling block-ordered iteration of state changes. Includes ByBlockIdQuery for querying diffs by block ID and SubstateIdIndex secondary index with schema (SubstateId, BlockId, Version, Seq) -> () for substate-based queries. BlockDiffModelRef provides reference-based insertion to avoid cloning SubstateChange values. BlockDiffKey contains block_id, substate_id, version, is_up flag, and sequence number (limits max changes per block to u32::MAX). Essential for blockchain state diff storage and efficient querying of state transitions. |
| `block_transaction_execution.rs` | RocksDB column family definitions for storing block transaction execution records and related indexes. BlockTransactionExecutionCf stores execution data with schema (TransactionId, BlockId, NodeHeight) -> BlockTransactionExecution, including node height for height-based filtering. Provides ByTransactionIdQuery for querying executions by transaction. Includes BlockIndex secondary index with schema (BlockId, TransactionId, NodeHeight) -> () enabling ByBlockQuery and ByBlockAndTransactionQuery for flexible execution lookup patterns. Essential for tracking transaction execution status across blocks and enabling efficient queries for pending transaction executions in the consensus system. |
| `bookkeeping.rs` | RocksDB column family definitions for consensus bookkeeping state storage. Defines single-value column families for tracking critical consensus state: DatabaseMigrationVersion, LastVotedCf, LastExecutedCf, LastProposedCf, LastSentVoteCf, CommitBlockCf, LeafBlockCf, LockedBlockCf, HighPcCf (proposal certificates), HighTcCf (timeout certificates), PreviousEpochStateRootCf, HighestSeenBlockCf, and LastSentNewViewCf. Each uses ByteColumn keys with unique byte identifiers and appropriate value codecs. CommitBlock struct contains height, block_id, and parent_id for commit tracking. Essential for maintaining consensus protocol state persistence across validator node restarts and ensuring protocol safety properties. |
| `burnt_utxo.rs` | RocksDB column family definitions for storing burnt UTXO (Unspent Transaction Output) records and related indexes. BurntUtxoCf stores burnt UTXOs with schema UnclaimedConfidentialOutputAddress -> UnclaimedConfidentialOutput, tracking confidential outputs that have been burnt/destroyed. Includes ProposedInBlockIndex secondary index with schema (BlockId, UnclaimedConfidentialOutputAddress) -> () for tracking which block proposed burning specific UTXOs. ByProposedInBlockIdQuery enables querying burnt UTXOs by the block that proposed their burning. Essential for confidential transaction privacy features and preventing double-spending of burnt outputs in the Tari privacy-preserving ecosystem. |
| `certificates.rs` | RocksDB column family definitions for storing consensus certificates organized into proposal and timeout certificate modules. proposal::ProposalCertificateCf stores proposal certificates with schema (Epoch, QcId) -> ProposalCertificate, including ByEpochQuery for epoch-based queries. timeout::TimeoutCertificateCf stores timeout certificates with schema (Epoch, TcId) -> TimeoutCertificate, also with ByEpochQuery support. Uses EpochCodec and FixedBytesCodec32 for key encoding and DefaultCodec for certificate value encoding. Essential for consensus protocol certificate storage, enabling validators to query and verify proposal and timeout certificates by epoch for safety and liveness guarantees in the distributed consensus algorithm. |
| `chain.rs` | RocksDB column family definitions for blockchain chain indexing and parent-child block relationships. PendingChainIndex stores pending block chain with schema BlockId (child) -> BlockId (parent). PendingParentChildIndex provides secondary index with schema (Parent, Child) -> () enabling ByParentIdQuery for finding children of specific parents. CommittedParentChildChainIndex stores committed block chain relationships with schema BlockId (parent) -> BlockId (child), used primarily for block sync operations. Essential for maintaining blockchain structure, enabling efficient chain traversal, and supporting block synchronization by tracking both pending and committed parent-child relationships in the validator network. |
| `epoch_checkpoint.rs` | RocksDB column family definition for storing epoch checkpoints in the consensus system. EpochCheckpointCf uses simple schema Epoch -> EpochCheckpoint, mapping epoch numbers to their corresponding checkpoint data. Uses EpochCodec for key encoding and DefaultCodec for EpochCheckpoint value serialization. Essential for epoch boundary management in the consensus protocol, enabling validators to store and retrieve critical state snapshots at epoch transitions for recovery, synchronization, and ensuring consensus safety across epoch boundaries in the distributed validator network. |
| `evicted_node.rs` | RocksDB column family definition for tracking evicted validator nodes in the consensus system. EvictedNodeCf stores evicted node data with schema (RistrettoPublicKeyBytes, BlockId) -> EvictedNodeData, mapping validator public keys and block IDs to eviction details. EvictedNodeData contains epoch and is_committed flag indicating eviction status. Includes ByPublicKeyQuery for querying evictions by validator public key. Essential for consensus fault tolerance, enabling tracking of misbehaving or non-responsive validators that have been removed from the active validator set, and managing validator set changes across epochs. |
| `finalized_transaction.rs` | RocksDB column family definition for tracking finalized transactions in the consensus system. FinalizedTransactionLinkCf uses simple schema TransactionId -> FinalizedTransactionLinkData, storing finalization timestamps for completed transactions. FinalizedTransactionLinkData contains finalized_at PrimitiveDateTime field indicating when the transaction was finalized. Essential for transaction lifecycle management, enabling queries of transaction finalization status and providing audit trails for completed transactions in the distributed consensus system. Used for transaction cleanup, status reporting, and ensuring transaction completeness guarantees. |
| `foreign_parked_blocks.rs` | RocksDB column family definitions for managing foreign parked blocks and their missing transactions. ForeignParkedBlockCf stores parked proposals with schema BlockId -> ForeignParkedProposal for blocks from foreign shards awaiting processing. MissingTransactionsModel tracks missing transactions with schema (TransactionId, BlockId) -> () identifying transactions referenced but not yet received. Includes ByTransactionIdQuery for querying missing transactions by ID. Essential for cross-shard coordination in the sharded consensus system, enabling proper sequencing of inter-shard transactions and ensuring all dependencies are satisfied before block processing. |
| `foreign_proposal.rs` | RocksDB column family definitions for managing foreign proposals in cross-shard consensus with multiple indexing strategies. ForeignProposalCf stores proposals with schema BlockId -> ForeignProposalRecord. EpochIndex maps (Epoch, BlockId) -> ForeignProposalEpochIndexData for epoch-based queries via ByEpochQuery. ProposedInBlockIndex tracks proposal relationships with schema (proposed_in_block, block_id) -> () enabling ByProposedInBlockIndexQuery. UnconfirmedIndex manages unconfirmed proposals with schema (Epoch, BlockId) -> () via UnconfirmedIndexEpochQuery. Essential for inter-shard proposal management, enabling efficient querying by epoch, proposer block, and confirmation status in the distributed sharded consensus protocol. |
| `foreign_substate_pledge.rs` | RocksDB column family definition for tracking foreign substate pledges in cross-shard consensus. ForeignSubstatePledgeCf stores pledges with schema (TransactionId, SubstateAddress) -> SubstatePledge, mapping transaction and substate address pairs to pledge data. Includes ByTransactionIdQuery for querying pledges by transaction ID. Essential for managing substate locks and commitments across shard boundaries in the distributed consensus system, enabling proper coordination of cross-shard transactions and ensuring atomic execution of multi-shard operations while preventing double-spending and maintaining state consistency. |
| `lock_conflict.rs` | RocksDB column family definitions for tracking transaction lock conflicts in the consensus system. LockConflictCf stores conflicts with schema (TransactionId, BlockId, depends_on_tx_id) -> LockConflict, tracking dependencies between conflicting transactions. LockConflictBlockIdIndex provides secondary index with schema (BlockId, TransactionId, depends_on_tx_id) -> () for block-based queries. Includes ByTransactionIdQuery and ByBlockIdQuery for flexible conflict resolution queries. Essential for transaction ordering and conflict resolution, enabling proper sequencing of dependent transactions and preventing deadlocks in concurrent transaction processing within consensus blocks. |
| `missing_transactions.rs` | RocksDB column family definitions for tracking missing transactions in the consensus system. MissingTransactionCf stores missing transaction records with schema (TransactionId, BlockId) -> (), tracking transactions referenced but not yet received. MissingTransactionBlockIdIndex provides secondary index with schema (BlockId, TransactionId) -> () sharing the same column family name (collision unlikely between BlockId/TransactionId). Includes ByTransactionIdQuery and ByBlockIdQuery for flexible missing transaction queries. Essential for block processing dependencies, ensuring all required transactions are available before block execution and enabling proper synchronization in distributed consensus. |
| `mod.rs` | Module declarations for RocksDB column family implementations. Organizes column families by category: consensus data (block, certificates, chain), transaction management (transaction, transaction_pool), state management (substate, state_tree, state_transition), synchronization (foreign_proposal, foreign_parked_blocks), and system tracking (bookkeeping, validator_node_epoch_stats). Each module defines column family structure, keys, values, and operations for specific data types in the validator node storage system. |
| `parked_block.rs` | RocksDB column family definitions for storing parked blocks awaiting processing in the consensus system. ParkedBlockCf stores parked block data with schema BlockId -> ParkedBlockData, where ParkedBlockData contains the block and associated foreign proposals. ParkedBlockModelRef provides reference-based insertion using ParkedBlockDataRef to avoid cloning during storage operations. Essential for consensus block buffering, enabling temporary storage of blocks that cannot be immediately processed due to missing dependencies or out-of-order arrival, ensuring proper sequencing and processing of all consensus blocks in the distributed validator network. |
| `pending_state_tree_diff.rs` | RocksDB column family definition for tracking pending state tree differences across shards in the consensus system. PendingStateTreeDiffCf stores pending diffs with schema (BlockId, Shard) -> PendingShardStateTreeDiff, mapping block and shard pairs to their state tree changes. Includes ByBlockIdQuery for querying pending diffs by block ID across all shards. Essential for sharded state management, enabling tracking of state changes that are pending application across different shards and ensuring consistent state tree updates in the distributed consensus system with cross-shard coordination. |
| `state_transition.rs` | RocksDB column family definitions for tracking state transitions in the sharded consensus system. StateTransitionCf stores transitions with schema StateTransitionId -> StateTransitionModelData, where StateTransitionModelData contains substate_address and StateTransitionType (Up/Down). Includes ByShardQuery and ByShardAndIdQuery for shard-based queries. ShardSeqIndex tracks sequence numbers with schema Shard -> u64 for ordering state transitions within shards. Essential for managing ordered state changes across shards, enabling proper sequencing of substate modifications and ensuring consistency in the distributed consensus system with cross-shard state coordination. |
| `state_tree.rs` | RocksDB column family definitions for Jellyfish Merkle Tree state storage across shards. StateTreeCf stores tree nodes with schema (Shard, NodeKey) -> Node<Version> for shard-partitioned state trees. StateTreeCfRef provides reference-based key operations to avoid cloning during storage. StateTreeStaleNodesModel tracks garbage collection with schema (Shard, Version) -> Vec<StaleTreeNode> for cleanup of outdated tree nodes. Includes ByShardQuery and ByStateTreeStaleShardQuery for shard-based queries. Essential for persistent authenticated state storage, enabling efficient state proofs, historical queries, and garbage collection in the sharded blockchain architecture. |
| `state_tree_shard_versions.rs` | RocksDB column family definition for tracking state tree versions per shard in the consensus system. StateTreeShardVersionCf uses simple schema Shard -> Version, mapping shard identifiers to their current state tree version numbers. Uses ShardCodec for shard encoding and NumberCodec for version values. Essential for shard state versioning, enabling proper coordination of state tree updates across different shards and ensuring consistency when synchronizing or validating state tree modifications in the distributed sharded consensus architecture. |
| `substate.rs` | RocksDB column family definitions for substate storage and indexing in the consensus system. SubstateCf stores substates with schema SubstateAddress -> SubstateRecord using fixed-length address encoding. HeadIndex tracks current versions with schema SubstateId -> SubstateHeadData containing version and is_up status. UnprunedDownedValuesIndex manages garbage collection with schema (Epoch, Shard, u64) -> SubstateAddress for tracking unpruned downed substates. Includes UnprunedDownedValuesEpochQuery for epoch-based cleanup queries. Essential for blockchain state management, enabling efficient substate lookups, version tracking, and automated cleanup of outdated state data across shards. |
| `substate_locks.rs` | RocksDB column family definitions for comprehensive substate lock management in the consensus system. SubstateLockModel stores locks with custom SubstateLockKey containing block_id, block_height, substate_id, and transaction_id. Provides multiple indexes: HeadIndex (SubstateId -> SubstateLockKey), BlockIdIndex with ByBlockIdQuery and ByBlockIdSubstateIdQuery, and SubstateIdIndex (SubstateId -> SubstateLockType). Includes ByTransactionIdQuery for transaction-based lock queries. Essential for preventing double-spending and managing concurrent access to substates, enabling proper lock ordering and conflict resolution in the distributed consensus system with fine-grained concurrency control. |
| `transaction.rs` | RocksDB column family definition for storing complete transaction records in the consensus system. TransactionCf uses simple schema TransactionId -> TransactionRecord, mapping transaction identifiers to their full transaction data. Uses TransactionIdCodec for key encoding and DefaultCodec for TransactionRecord value serialization. Essential for transaction storage and retrieval, enabling validator nodes to access complete transaction details during consensus processing, block building, and transaction pool management in the distributed blockchain network. |
| `transaction_pool.rs` | RocksDB column family definition for transaction pool management in the consensus system. TransactionPoolCf stores pool records with schema TransactionId -> TransactionPoolRecord, tracking transaction status, validation results, and pool metadata. Uses TransactionIdCodec for key encoding and DefaultCodec for TransactionPoolRecord serialization. Essential for transaction pool operations including validation, ordering, selection for block proposals, and lifecycle management of pending transactions awaiting inclusion in consensus blocks across the distributed validator network. |
| `transaction_pool_state_update.rs` | RocksDB column family definitions for tracking transaction pool state updates and debugging history. TransactionPoolStateUpdateCf stores updates with schema (BlockId, TransactionId) -> TransactionPoolStateUpdateData containing evidence, fees, decisions, and readiness status. TransactionPoolStateUpdateData provides merge_into() method for applying updates to existing records. TransactionPoolStateUpdateDebugHistoryCf tracks historical updates with schema (Epoch, NodeHeight, TransactionId) -> TransactionPoolStateUpdateData. Includes ByBlockIdQuery for block-based queries. Essential for transaction pool state management, consensus decision tracking, and debugging transaction lifecycle issues across validator nodes. |
| `validator_node_epoch_stats.rs` | RocksDB column family definition for tracking validator node performance statistics per epoch. ValidatorNodeEpochStatsCf stores statistics with schema (Epoch, RistrettoPublicKeyBytes) -> ValidatorConsensusStats, mapping epoch and validator public key pairs to consensus performance metrics. Includes ByEpochQuery for querying all validator stats within an epoch. Essential for validator performance monitoring, reward calculations, reputation tracking, and network health assessment, enabling measurement of validator participation, block proposals, votes, and overall consensus contribution across epochs in the distributed validator network. |

###### crates/state_store_rocksdb/src/dbs/

| File | Description |
|------|-------------|
| `mod.rs` | Module definition for RocksDB database wrappers and implementations. Exports read_only module publicly and includes private snapshot and transaction modules. Organizes different database access patterns: read-only access for queries, snapshot-based consistent reads, and transactional access for atomic writes. Essential for structuring the database layer architecture in the state store, providing type-safe abstractions over RocksDB functionality while maintaining performance and consistency guarantees required by the distributed consensus system. |
| `read_only.rs` | RocksDB read-only database wrapper implementing RocksDatabase and RocksReader traits. ReadOnlyDb wraps a RocksDB instance providing read-only access through get_pinned_cf(), iterator operations, and multi_get_cf() methods. Implements column family handle access and all standard read operations without write capabilities. Essential for safe concurrent read access to the blockchain state store, enabling multiple reader threads to access data without affecting write operations or requiring transaction overhead, critical for high-performance blockchain data queries and synchronization processes. |
| `snapshot.rs` | RocksDB snapshot implementation providing consistent point-in-time read access. Implements RocksReader trait for SnapshotWithThreadMode, enabling consistent reads across multiple operations without being affected by concurrent writes. Provides get_pinned_cf(), iterator, and multi_get_cf() operations on a consistent snapshot view of the database. Essential for blockchain state consistency, enabling atomic multi-key reads, block validation operations, and state tree operations that require a consistent view of data across the distributed consensus system without write contention. |
| `transaction.rs` | RocksDB transaction database implementation providing atomic read-write operations. Implements RocksDatabase for TransactionDB and both RocksReader and RocksWriter traits for Transaction instances. Enables atomic multi-key operations including puts, deletes, gets, and iteration within ACID transaction boundaries. Essential for blockchain state modifications requiring atomicity, such as block application, state transitions, and consensus operations that must either complete entirely or rollback completely, ensuring data consistency in the distributed consensus system. |

###### crates/state_store_rocksdb/src/traits/

| File | Description |
|------|-------------|
| `column_family.rs` | Core traits defining column family type system for RocksDB integration. Cf trait defines column family schema with Key/Value types and their corresponding codecs, providing name() and codec factory methods. QueryCf trait enables prefix query operations on column families with separate key types. Implements blanket Cf implementation for QueryCf types to enable query types to be used as column families. Essential type system foundation enabling compile-time safety for all database operations, ensuring consistent encoding/decoding and providing the basis for all column family definitions in the blockchain state store. |
| `database.rs` | RocksDB database access traits defining unified interface for different database access patterns. RocksDatabase provides column family handle access. RocksReader defines read operations (get_pinned_cf, iterator methods, multi_get_cf) with lifetime management for database iterators. RocksWriter extends RocksReader with write operations (put_cf, delete_cf). These traits enable polymorphic database access across transactions, snapshots, and read-only instances while maintaining type safety and performance. Essential abstraction layer enabling unified database operations across different RocksDB access patterns in the blockchain consensus system. |
| `mod.rs` | Traits module organization for RocksDB abstractions exporting column family and database traits. Re-exports all types from column_family and database modules including Cf, QueryCf, RocksDatabase, RocksReader, and RocksWriter traits. Provides clean public API for database abstraction layer, enabling type-safe and polymorphic database access patterns. Essential module structure organizing the trait system that underpins all RocksDB operations in the blockchain state store, ensuring consistent interfaces across different database access patterns and column family operations. |

#### crates/state_store_tests/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo configuration for state store integration tests crate. Defines workspace-based package configuration with dev-dependencies including tari_ootle_storage, tari_state_store_rocksdb, and all core blockchain types (consensus, engine, transaction). Includes testing utilities like tempfile for temporary databases, rand for test data generation, and env_logger for test logging. This crate contains comprehensive integration tests validating state store functionality across different implementations, ensuring database operations work correctly for blocks, transactions, substates, and consensus operations. |

##### crates/state_store_tests/src/

| File | Description |
|------|-------------|
| `block_diffs.rs` | Integration tests for block diff storage and retrieval operations in the state store. Tests block diff insertion, querying by block ID, substate change tracking, and diff ordering within blocks. Validates that state changes are correctly recorded and can be retrieved for blockchain synchronization and state reconstruction. Essential for ensuring block diff functionality works correctly across state store implementations. |
| `blocks.rs` | Integration tests for blockchain block operations covering basic block CRUD, parent-child relationships, and block queries. Tests include block insertion/retrieval, existence checks, QC setting, commit flag management, deletion operations, and complex querying scenarios like blocks_get_all_between with filtering and pagination. Validates block parent chain operations, pending ancestor checks, and committed block relationships. Essential test suite ensuring block storage and retrieval operations work correctly across different state store implementations, providing confidence in blockchain data persistence and query functionality. |
| `foreign_parked_proposals.rs` | Integration tests for foreign parked proposal storage and management in cross-shard consensus scenarios. Tests storage, retrieval, and lifecycle management of proposals from foreign shards that are temporarily parked awaiting dependencies. Validates proper handling of cross-shard proposal coordination and ensures foreign proposal operations work correctly across state store implementations. |
| `foreign_proposals.rs` | Integration tests for foreign proposal storage, indexing, and query operations in the sharded consensus system. Tests foreign proposal insertion, retrieval by various indexes (epoch, proposer block), status management (confirmed/unconfirmed), and cleanup operations. Validates cross-shard proposal coordination functionality and ensures foreign proposal operations work correctly across state store implementations. |
| `foreign_substate_pledges.rs` | Integration tests for foreign substate pledge storage and management in cross-shard transactions. Tests pledge creation, retrieval, and coordination mechanisms for substates across shard boundaries. Validates proper handling of cross-shard substate commitments and ensures pledge operations work correctly for maintaining consistency in distributed transactions across state store implementations. |
| `helpers.rs` | Comprehensive test utilities and factory functions for state store testing. Provides create_rocksdb() for temporary RocksDB instances, test data generators (transaction_id_from_seed, substate creation functions, random data generators), blockchain builders (create_block, create_chain, commit_chain), and assertion helpers. Includes factory functions for SubstateRecord, SubstateValue, ComponentHeaders, ForeignProposalRecord, and complete blockchain structures. Essential testing infrastructure enabling consistent test data generation and database setup across all state store integration tests, supporting both unit and integration testing scenarios. |
| `lib.rs` | Comprehensive test suite for Tari state store implementations. Provides test modules covering block diffs, blocks, foreign proposals, state transitions, state trees, quorum certificates, and various storage scenarios. Essential testing framework ensuring state store correctness across different storage backends and complex state management operations. |
| `misc.rs` | Miscellaneous integration tests for various state store operations not covered in other specific test modules. Contains utility tests, edge case validations, and integration scenarios that test interactions between different state store components. Provides additional test coverage for ensuring comprehensive state store functionality across implementations. |
| `missing_transactions.rs` | Integration tests for missing transaction tracking and dependency management in the consensus system. Tests storage and retrieval of missing transaction records, dependency resolution, and block processing coordination when transactions are not yet available. Validates proper handling of transaction dependencies and ensures missing transaction operations work correctly across state store implementations. |
| `quorum_certificates.rs` | Integration tests for quorum certificate storage, validation, and retrieval operations in the consensus system. Tests certificate insertion, querying by various criteria, epoch-based organization, and certificate lifecycle management. Validates proper handling of consensus certificates (both proposal and timeout certificates) and ensures certificate operations work correctly across state store implementations. |
| `state_transitions.rs` | Integration tests for state transition tracking and management in the sharded consensus system. Tests state transition creation, retrieval by shard, sequencing operations, and transition lifecycle management. Validates proper ordering and tracking of state changes across shards and ensures state transition operations work correctly across state store implementations. |
| `state_tree.rs` | Integration tests for Jellyfish Merkle Tree state tree operations including node storage, retrieval, versioning, and garbage collection. Tests state tree node insertion, querying by shard and version, stale node tracking, and tree integrity operations. Validates proper handling of authenticated state storage and ensures state tree operations work correctly across state store implementations. |
| `state_tree_diff.rs` | Integration tests for state tree difference tracking and management across shards in the consensus system. Tests pending state tree diff storage, retrieval by block and shard, and diff application operations. Validates proper handling of state tree changes across shard boundaries and ensures state tree diff operations work correctly across state store implementations. |
| `substate_locks.rs` | Integration tests for substate lock management and concurrency control in the consensus system. Tests lock creation, retrieval by various indexes, conflict detection, and lock lifecycle management. Validates proper handling of substate locking for preventing double-spending and ensuring transaction isolation, confirming lock operations work correctly across state store implementations. |
| `substates.rs` | Integration tests for substate storage operations including creation, versioning, retrieval, and lifecycle management. Tests substate creation with multiple versions, substate_get operations, batch retrieval with substates_get_any, maximum version queries, and substate state transitions (up/down operations). Validates version tracking, historical data access, and substate address management. Essential test suite ensuring substate storage operations work correctly across state store implementations, providing confidence in blockchain state management and substate versioning functionality in the consensus system. |
| `transactions.rs` | Integration tests for transaction pool operations and transaction execution lifecycle management. Tests transaction pool insertion, stage transitions (confirmation stages), transaction execution tracking, block transaction execution records, and finalized transaction management. Validates transaction pool status updates, evidence handling, fee management, and transaction lifecycle from pool insertion through finalization. Essential test suite ensuring transaction management operations work correctly across state store implementations, providing confidence in transaction pool functionality and execution tracking in the consensus system. |

#### crates/state_tree/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the tari_state_tree crate - a core component implementing Jellyfish Merkle Tree-based state storage for the Tari DAN. Dependencies include tari_common_types, tari_ootle_common_types, tari_engine_types, tari_jellyfish, and tari_template_lib for blockchain state management. Key dev dependency: indexmap for testing. This crate provides state tree abstractions, key mapping, and memory/staged store implementations for efficient state tracking and proofs. |

##### crates/state_tree/src/

| File | Description |
|------|-------------|
| `error.rs` | Error handling module for the state tree crate. Defines StateTreeError enum that wraps JmtStorageError from the Jellyfish Merkle Tree library. Implements IsNotFoundError trait to provide consistent error handling for missing state entries. Used throughout the state tree implementation for error propagation and handling of storage-level errors. |
| `key_mapper.rs` | Key mapping abstractions for the state tree. Defines DbKeyMapper trait for converting different key types to LeafKey for the Jellyfish Merkle Tree. Implements SpreadPrefixKeyMapper for VersionedSubstateId (hashes the ID for distribution) and HashIdentityKeyMapper for TreeHash (identity mapping). Used by StateTree to map application-level keys to tree-level keys for efficient storage and retrieval. |
| `lib.rs` | State tree implementation using Tari Jellyfish merkle tree structures. Provides key mapping, memory store, staged store operations, and tree management for cryptographically verified state storage. Essential component for maintaining verifiable blockchain state with efficient merkle proofs and state transitions in the Ootle network. |
| `memory_store.rs` | In-memory implementation of tree storage for the Jellyfish Merkle Tree. Provides MemoryTreeStore struct with HashMap-based node storage and stale node tracking. Implements TreeStoreReader for node retrieval and TreeStoreWriter for node insertion and stale node recording. Includes clear_stale_nodes() method for cleanup. Primarily used for testing and temporary state operations. Display implementation provides debugging output of stored nodes. |
| `staged_store.rs` | Staged storage implementation that layers pending changes over a base store. StagedTreeStore provides a read-write interface that combines a readable base store with pending state changes and new modifications. Key features: apply_pending_diff() for layering pending changes, automatic pruning of stale nodes, and into_diff() for extracting the final changeset. Used in consensus to build up state changes before committing them to the persistent store. |
| `traits.rs` | Additional traits for tree storage operations. Defines TreeStoreBatchWriter trait for batch operations: batch_insert_nodes() for efficient bulk node insertion and record_stale_tree_nodes() for marking nodes for pruning. Extends the base TreeStore interface to support high-performance batch operations needed for consensus state updates. |
| `tree.rs` | Core StateTree implementation using Jellyfish Merkle Tree for blockchain state management. Provides SpreadPrefixStateTree and RootStateTree type aliases. Key functionality: put_substate_changes() for state updates, get_proof() for Merkle proofs, get_root_hash() for state roots, batch operations for performance. Includes SubstateTreeChange enum for Up/Down state transitions, StateHashTreeDiff for change tracking, and utility functions for Merkle root computation and proof generation. Central component for DAN state management. |

##### crates/state_tree/tests/

| File | Description |
|------|-------------|
| `support.rs` | Test utilities and support functions for state tree testing. Provides make_value() for creating test VersionedSubstateId instances, change() and change_exact() for creating SubstateTreeChange test data, hash_value_from_seed() for deterministic value hashing. Includes HashTreeTester struct for systematic testing of tree operations and TestMapper for test-specific key mapping. Used across state tree test suite for consistent test data generation. |
| `test.rs` | Comprehensive test suite for state tree functionality. Tests hash consistency across state changes, version handling, Merkle proof generation/verification, stale node tracking, and key serialization ordering. Covers edge cases like empty states, repeated writes, entry addition/removal, and state restoration. Includes tests for proof inclusion/exclusion verification and tree behavior under various state transitions. Adapted from Radix Engine store tests, ensuring compatibility with established patterns. |

#### crates/storage/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for tari_ootle_storage crate - the main storage layer for Tari DAN consensus and state management. Dependencies include Tari core libraries (common, consensus_types, sidechain), state management (state_tree, engine_types, transaction), and serialization libraries (borsh, serde). Supports shard store operations with optional TypeScript bindings via ts-rs feature. Central storage abstraction for consensus models, global state, and transaction data. |

##### crates/storage/src/

| File | Description |
|------|-------------|
| `atomic.rs` | Database transaction abstraction trait for atomic operations. Defines AtomicDb trait with associated types for Error and DbTransaction, providing create_transaction(), commit(), and rollback() methods. Core interface for database implementations to ensure ACID properties. Used throughout the storage layer to enable transactional operations across different backend implementations (SQLite, etc.). |
| `error.rs` | Storage layer error types for Tari blockchain persistence. Exports StorageError enum covering connection, query, migration, encoding/decoding, file system, and data consistency errors. Implements IsNotFoundError trait for optional query handling. Features thiserror integration and detailed error context for debugging. Used by: all storage implementations (SQLite, RocksDB) for standardized error handling across database operations. Essential for storage abstraction layer error propagation. |
| `lib.rs` | Storage abstraction library providing atomic database operations, global storage management, and error handling. Exports AtomicDb for transactional operations, StorageError types, and global storage utilities. Foundation for all persistent data storage including blockchain state, transactions, and metadata with comprehensive error handling and transactional guarantees. |

###### crates/storage/src/consensus_models/

| File | Description |
|------|-------------|
| `block.rs` | Blockchain block data model with comprehensive consensus information. Implements block structure, validation, chain operations, state proof generation, and transaction execution tracking. Core data structure for blockchain persistence and consensus protocol state management. |
| `block_diff.rs` | BlockDiff model representing changes made by a specific block. Contains block_id and a vector of SubstateChange entries. Key methods: into_filtered() for committee-specific filtering, insert()/remove() for database operations, get_last_change_for_substate() and get_for_versioned_substate() for querying changes. Used in consensus to track state modifications per block and enable efficient state synchronization and rollback operations. |
| `block_header.rs` | Core BlockHeader implementation for Tari DAN blocks. Contains comprehensive block metadata: network, parent/child relationships, QC justification, height, epoch, shard group, proposer info, Merkle roots (state and command), leader fees, signatures, timestamps, and extra data. Key functionality: create(), genesis(), dummy_block() constructors, hash calculation, signature verification, and conversions to consensus types (LockedBlock, LastExecuted, etc.). Implements ToSignatureMessage and SignedMessage traits. Central to consensus protocol with TypeScript bindings support. |
| `block_pledges.rs` | Block pledge system for DAN consensus managing substate commitments. BlockPledge contains a HashMap of SubstateId to Substate mappings. SubstatePledge enum handles Input (read/write with substate values) and Output pledges. Key functionality: get_all_pledges_for_evidence(), validation methods for checking input availability, pledge creation and satisfaction. Used in consensus to track which substates are pledged for transaction execution and validate that all required inputs are available before processing. |
| `bookkeeping.rs` | Consensus bookkeeping model trait and implementations for tracking HotStuff-style consensus state. BookkeepingModel trait provides get/set interface for epoch-based storage. Implementations for key consensus types: HighPc, HighTc, LastExecuted, LastVoted, LeafBlock, HighestSeenBlock, LockedBlock, LastProposed, LastSentVote, LastSentNewView. Each type implements the trait to provide consistent storage/retrieval interface for consensus state management across epochs. |
| `burnt_utxo.rs` | Burnt UTXO management for confidential outputs in DAN. BurntUtxo struct tracks UnclaimedConfidentialOutput with commitment, output data, proposal block, and epoch. MintConfidentialOutputAtom handles atomic operations for minting. Key operations: insert(), set_proposed_in_block(), get_all_unproposed(), has_unproposed(). Used for bridging between base layer (Tari) and DAN layer, allowing burnt UTXOs to be minted as confidential outputs in the sidechain. Includes TypeScript bindings support. |
| `command.rs` | Core command system for DAN consensus operations. Command enum defines transaction lifecycle: LocalOnly, LocalPrepare, LocalAccept, AllAccept, SomeAccept for multi-phase consensus, plus ForeignProposal, MintConfidentialOutput, EvictNode, EndEpoch. TransactionAtom contains transaction ID, decision, evidence, fees. Implements ordering for deterministic block processing, command extraction methods, and hash computation. Central to the consensus protocol for coordinating transaction execution across shards. Includes TypeScript bindings. |
| `commands_commit_proof.rs` | Sidechain commit proof validation for foreign proposal commands. CommandsCommitProof enum (currently V1) contains commands and sidechain proof. CommandOrHash enum allows mixing full commands with command hashes for efficiency. Key validation: header signatures, command Merkle root verification, quorum threshold validation. Used to validate that foreign shard groups have committed to specific commands before accepting them locally. Includes comprehensive error handling for malformed proofs and signature verification failures. |
| `epoch_checkpoint.rs` | Epoch checkpoint validation and management for cross-shard consensus. EpochCheckpoint contains CommandCommitProof with EndOfEpochCommand and shard state roots. Key validation: commit proof verification, quorum thresholds, 3-chain rule, state Merkle root matching. Computes aggregate state root from individual shard roots. Used at epoch boundaries to ensure consistent state across all shards before transitioning to next epoch. Includes comprehensive validation errors and storage operations. |
| `epoch_state_root.rs` | State root tracking across epochs for consensus continuity. EpochStateRoot stores epoch, shard group, and state root hash for a specific committee's state at epoch end. Used to maintain state continuity between epochs and enable state validation when epochs transition. Provides set() and get() methods for storage persistence. Essential for cross-epoch validation and ensuring state consistency in sharded consensus. |
| `evidence.rs` | Comprehensive evidence system for transaction execution across shard groups. Evidence contains ShardGroupEvidence for inputs/outputs, prepare/accept QCs. Tracks lock intents, substate pledges, and justification status. Key methods: from_inputs_and_outputs(), merge(), validation checks for prepare/accept phases. ShardGroupEvidence manages individual shard's input/output locks and QC tracking. Central to multi-shard transaction coordination, ensuring all required substates are properly locked before execution. Includes TypeScript bindings. |
| `foreign_parked_proposal.rs` | Temporary storage for foreign proposals awaiting processing. ForeignParkedProposal contains CommandsCommitProof and BlockPledge for proposals that cannot immediately be processed (e.g., missing dependencies). Provides save(), insert(), exists() operations and methods for managing missing transactions. Can be converted to ForeignProposal when ready for processing. Used to handle proposals that arrive out-of-order or depend on incomplete local state. |
| `foreign_proposal.rs` | Foreign proposal system for cross-shard block coordination. ForeignProposalRecord tracks proposals with status (New, Proposed, Confirmed, Invalid) and storage operations. ForeignProposal contains CommandsCommitProof and BlockPledge for proposals from other shards. ForeignProposalAtom provides lightweight reference. Key functionality: status management, validation, transaction filtering by committee, storage operations. Central to cross-shard consensus allowing committees to coordinate block execution and maintain consistency across the network. Includes TypeScript bindings. |
| `leader_fee.rs` | Leader fee calculation model for DAN consensus rewards. LeaderFee struct contains fee amount and global_exhaust_burn value. calculate_leader_fee() function implements fee distribution algorithm across involved shards with exhaust burning mechanism to prevent fee accumulation. Handles remainder distribution and burn adjustments to ensure fair reward distribution while maintaining economic balance. Includes TypeScript bindings for web interface integration. |
| `lock_conflict.rs` | Lock conflict detection and resolution for concurrent substate access. Manages conflicts when multiple transactions attempt to access the same substates with incompatible lock types (read vs write). Used in consensus to detect and resolve contention, ensuring transaction isolation and preventing race conditions in multi-shard execution. |
| `lock_intent.rs` | Lock intent models for substate locking in transaction execution. Defines structures and traits for expressing intent to lock specific substates with read/write/output permissions. Central to the multi-phase consensus protocol where transactions declare their locking requirements before execution, enabling conflict detection and proper ordering. |
| `mod.rs` | Consensus data models aggregator for blockchain storage layer. Exports comprehensive set of models including blocks, transactions, substates, locks, votes, epochs, and state transitions. Core data structures for persisting consensus state and transaction execution results. |
| `no_vote.rs` | No-vote tracking for HotStuff consensus protocol. Manages scenarios where validators choose not to vote on specific proposals, tracking abstentions and ensuring proper consensus progress. Used to distinguish between absent validators and intentional abstentions in the voting process. |
| `state_transition.rs` | State transition models for tracking changes in DAN state. Manages transitions between different states in the consensus protocol, including block states, transaction states, and validator states. Provides structured representation of state changes for validation and rollback operations. |
| `state_tree_diff.rs` | State tree difference tracking for incremental state updates. Manages diffs between state tree versions, tracking additions, modifications, and deletions of state entries. Used to efficiently propagate state changes across the network and enable incremental synchronization without requiring full state transfers. |
| `substate.rs` | Substate data model representing blockchain state elements with versioning and lifecycle tracking. Manages substate creation, destruction, ownership, and state transitions. Fundamental data structure for blockchain state management and smart contract execution. |
| `substate_change.rs` | Substate change tracking for transaction effects. Models individual changes to substates during transaction execution, including creation, updates, and deletions. Provides structured representation of how transactions modify the global state, enabling proper state tree updates and validation of transaction effects. |
| `substate_lock.rs` | Substate locking mechanisms for transaction isolation. Implements locking protocols to ensure transaction ACID properties in the distributed environment. Manages read/write locks on substates, preventing conflicts and ensuring consistent state access across concurrent transactions. |
| `transaction.rs` | Transaction data model for blockchain storage with execution tracking and pledge management. Handles transaction records, execution state, substate pledges, and evidence collection. Core persistence layer for transaction lifecycle and consensus validation. |
| `transaction_execution.rs` | Transaction execution result models for DAN consensus. TransactionExecution contains ExecuteResult with resolved inputs/outputs and execution metadata. BlockTransactionExecution links execution to specific blocks. Key functionality: decision determination, fee calculation, evidence generation, abort handling. Used to track transaction outcomes, execution time, and state changes throughout the consensus process. Includes TypeScript bindings and comprehensive storage operations. |
| `transaction_pool.rs` | Transaction pool management for pending transactions. Manages the collection of transactions awaiting execution, providing queueing, prioritization, and selection mechanisms. Handles transaction lifecycle from submission to execution, including validation, ordering, and removal of processed transactions. |
| `transaction_pool_status_update.rs` | Transaction pool status update tracking for transaction lifecycle management. Manages status changes of transactions in the pool (pending, executing, completed, failed). Provides notifications and updates for transaction state transitions, enabling proper coordination between consensus and application layers. |
| `validated_block.rs` | Validated block models for consensus-approved blocks. Represents blocks that have passed validation and are ready for execution or commitment. Contains validation metadata, approval status, and links to block content. Used in the consensus pipeline after block proposal validation and before final commitment. |
| `validator_stats.rs` | Validator statistics tracking for performance and reliability metrics. Maintains counters for validator behavior including proposals, votes, blocks produced, uptime metrics. Used for validator reputation tracking, rewards calculation, and network health monitoring. |

###### crates/storage/src/global/

| File | Description |
|------|-------------|
| `backend_adapter.rs` | GlobalDbAdapter trait defining the interface for global database operations. Extends AtomicDb with methods for metadata management, template operations (status, CRUD), validator node management (insert, deactivate, queries), committee formation, epoch management, and BMT (Balanced Merkle Tree) operations. Generic over NodeAddressable types. Core abstraction enabling different database backends (SQLite, etc.) while maintaining consistent API for global state operations. |
| `base_layer_db.rs` | Base layer database interface for Tari blockchain integration. Manages connections and operations with the main Tari blockchain (layer 1), including block synchronization, transaction validation, and checkpoint management. Provides bridge between DAN (layer 2) and main chain for security and finality. |
| `bmt_db.rs` | Balanced Merkle Tree database operations for validator node commitments. Manages BMT structures for validator registration, committee formation, and proof generation. Provides efficient storage and retrieval of validator node balanced Merkle trees used in consensus and committee selection processes. |
| `epoch_db.rs` | Epoch database management for consensus epoch tracking. Handles storage and retrieval of epoch information including epoch transitions, validator sets, and epoch-specific configuration. Manages epoch lifecycle and provides historical epoch data for consensus validation and state management. |
| `global_db.rs` | GlobalDb struct providing high-level interface to global database operations. Wraps GlobalDbAdapter implementations with transaction management (with_write_tx, with_read_tx) and provides accessor methods for specialized database interfaces: templates(), metadata(), validator_nodes(), epochs(), base_layer(), and bmt(). Includes DbFactory trait for database instantiation and migration. Central coordination point for all global state database operations with proper transaction lifecycle management. |
| `metadata_db.rs` | Global metadata database for system configuration and state. Stores key-value metadata including network parameters, configuration settings, and system state information. Provides generic storage for metadata that needs to persist across system restarts and be accessible globally. |
| `mod.rs` | Global database interface aggregating specialized database components. Exports template database, validator node database, epoch database, metadata storage, and base layer integration. Provides unified access to cross-cutting blockchain data storage needs. |
| `template_db.rs` | Template database operations for smart contract template management. Handles storage, retrieval, and status tracking of Tari templates including pending, active, and deprecated templates. Manages template metadata, binary data, and lifecycle state changes. |
| `validator_node_db.rs` | Validator node database operations for validator management. Handles CRUD operations for validator registration, deactivation, committee assignment, and statistics. Provides queries for validator selection, shard group formation, and validator node lifecycle management. |

###### crates/storage/src/global/models/

| File | Description |
|------|-------------|
| `mod.rs` | Module definition file for global storage models. Exports data models used in global database operations including validator nodes, epochs, templates, and metadata structures. Organizes model definitions for global state management. |
| `validator_node.rs` | Validator node data model for global state. Defines ValidatorNode structure containing public key, shard assignment, address, vote power, epoch information, and fee claim keys. Used throughout the system for validator management, committee formation, and consensus operations. |

###### crates/storage/src/state_store/

| File | Description |
|------|-------------|
| `mod.rs` | State store abstractions providing comprehensive database interface for blockchain state management. Defines read/write transaction traits, consensus operations, substate queries, block management, and state tree operations. Core storage interface supporting both SQLite and RocksDB backends. |

#### crates/storage_sqlite/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for tari_ootle_storage_sqlite crate - SQLite implementation of the storage layer. Dependencies include Diesel ORM for database operations, tari_ootle_storage for traits, and core Tari types. Provides SQLite backend for global database operations including validator nodes, templates, epochs, and metadata storage. Used as the primary persistence layer for DAN nodes. |
| `README.md` | Documentation for the SQLite storage implementation. Explains the SQLite backend for Tari DAN storage, including setup instructions, database schema information, migration procedures, and usage examples. Covers the Diesel ORM integration and database configuration options. |
| `diesel.toml` | Diesel ORM configuration file for SQLite database operations. Specifies database schema location, migration directories, and Diesel-specific settings for the storage_sqlite crate. Configures code generation and database connection parameters for the SQLite backend. |

###### crates/storage_sqlite/migrations/2022-06-20-091532_initial/

| File | Description |
|------|-------------|
| `down.sql` | Initial database migration rollback script. SQL commands to drop tables and reverse the initial database schema setup. Used by Diesel migrations to rollback the initial storage schema if needed during development or maintenance. |
| `up.sql` | Initial database migration script. SQL DDL statements to create the foundational database schema for Tari DAN storage including tables for validator nodes, templates, epochs, metadata, and other global state. Establishes the base schema structure for SQLite storage backend. |

##### crates/storage_sqlite/src/

| File | Description |
|------|-------------|
| `error.rs` | Error handling for SQLite storage backend. Defines SqliteStorageError enum covering database connection issues, query failures, serialization errors, and Diesel-specific errors. Provides error mapping from low-level database errors to application-level storage errors with proper context and error chains. |
| `lib.rs` | SQLite storage backend implementation using Diesel ORM for blockchain data persistence. Provides database transactions, migration support, and global database factory. Primary storage backend for development and testing environments with comprehensive SQL-based state management. |
| `sqlite_db_factory.rs` | SQLite database factory implementation. Implements DbFactory trait to provide SQLite database instances with proper configuration, connection pooling, and migration management. Handles database creation, migration execution, and connection lifecycle for the SQLite storage backend. |
| `sqlite_transaction.rs` | SQLite transaction implementation. Provides transaction management for SQLite database operations including transaction creation, commit, rollback, and proper resource cleanup. Implements database transaction traits with SQLite-specific behavior and connection management. |

###### crates/storage_sqlite/src/global/

| File | Description |
|------|-------------|
| `backend_adapter.rs` | SQLite implementation of the GlobalDbAdapter trait. Provides concrete SQLite-backed implementations for all global database operations including validator node management, template storage, epoch tracking, and metadata operations. Bridges the abstract storage interface with SQLite-specific Diesel operations. |
| `mod.rs` | Module organization for SQLite global storage implementation. Exports the backend adapter, models, schema, and serialization components. Organizes the SQLite-specific implementations of global database functionality. |
| `schema.rs` | [GENERATED] Diesel-generated database schema definitions for SQLite global storage. Contains table definitions, column mappings, and relationships for all global database entities. Auto-generated from database migrations and should not be manually edited. |
| `serialization.rs` | Serialization utilities for SQLite storage backend. Provides conversion functions between in-memory data structures and database-compatible formats. Handles JSON serialization, binary encoding, and type conversions for complex data types stored in SQLite. |

###### crates/storage_sqlite/src/global/models/

| File | Description |
|------|-------------|
| `bmt.rs` | SQLite Diesel model for Balanced Merkle Tree storage. Defines database schema mappings and serialization for BMT data structures. Handles conversion between in-memory BMT representations and SQLite storage format for validator node commitment trees. |
| `committee.rs` | SQLite Diesel model for committee storage. Defines database schema mappings for committee information including validator memberships, shard group assignments, and committee metadata. Handles serialization of committee structures for persistent storage. |
| `epoch.rs` | SQLite Diesel model for epoch data storage. Defines database schema mappings for epoch information including epoch numbers, transition timestamps, validator sets, and epoch-specific configuration. Manages epoch lifecycle data persistence. |
| `metadata.rs` | SQLite Diesel model for metadata key-value storage. Provides database schema mappings for storing arbitrary metadata as key-value pairs with proper serialization. Used for system configuration, network parameters, and persistent application state. |
| `mod.rs` | Module organization for SQLite storage models. Exports all Diesel model structures for global database entities including validator nodes, templates, epochs, committees, BMT, and metadata. Centralizes model definitions for SQLite backend. |
| `template.rs` | SQLite Diesel model for template storage. Defines database schema mappings for smart contract templates including template addresses, binary data, status information, and metadata. Handles serialization of template data for persistent storage and retrieval. |
| `validator_node.rs` | SQLite Diesel model for validator node storage. Defines database schema mappings for validator node information including public keys, addresses, shard assignments, vote power, epochs, and fee claim keys. Core model for validator management in the SQLite backend. |

##### crates/storage_sqlite/tests/

| File | Description |
|------|-------------|
| `global_db.rs` | Integration tests for SQLite global database operations. Tests validator node management, template storage, epoch handling, metadata operations, and transaction behavior. Validates SQLite backend functionality against the storage interface contracts and ensures data integrity. |

#### crates/tari_bor/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for tari_bor crate - Binary Object Representation library. Provides CBOR-based binary encoding for template and engine data types. Dependencies include ciborium for CBOR operations with std/alloc feature support. Optional json_encoding feature for development/debugging. Used throughout the system for efficient serialization of complex data structures. |
| `README.md` | Documentation for the Binary Object Representation (BOR) crate. Explains the CBOR-based encoding system for Tari templates and engine data types. Covers encoding/decoding operations, supported data types, feature flags, and integration with the template system. |

##### crates/tari_bor/src/

| File | Description |
|------|-------------|
| `error.rs` | Error handling for BOR encoding/decoding operations. Defines BorError enum covering CBOR encoding failures, type mismatches, unsupported operations, and serialization issues. Provides error context and conversion utilities for debugging binary encoding problems. |
| `json_encoding.rs` | JSON encoding utilities for BOR data types. Provides JSON serialization/deserialization for BOR-encoded data, primarily used for debugging, development tools, and human-readable representation of binary data. Optional feature for development environments. |
| `lib.rs` | Tari Binary Object Representation (BOR) encoding library based on CBOR (Concise Binary Object Representation). Provides efficient serialization/deserialization with length prefixes, JSON encoding features, error handling, and no-std support. Core serialization library used throughout Tari for data persistence, network communication, and state storage. |
| `tag.rs` | CBOR tag definitions and handling for BOR encoding. Defines semantic tags used to identify different data types in the binary representation. Manages tag allocation, validation, and provides type safety for tagged CBOR data structures. |
| `walker.rs` | CBOR data structure walker for traversing BOR-encoded data. Provides utilities for walking through complex nested CBOR structures, extracting values, and performing transformations. Used for data inspection, validation, and processing of encoded template data. |

#### crates/template_abi/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for tari_template_abi crate - low-level Tari engine ABI for WASM targets. Defines the interface between templates and the Tari engine. Dependencies include tari_bor for encoding and optional hashbrown/ts-rs. Features support std/alloc environments and TypeScript generation. Core component for template-engine communication. |
| `README.md` | Documentation for the template ABI crate. Explains the low-level Application Binary Interface for Tari templates, including WASM integration, function signatures, data types, and calling conventions. Covers template-engine communication protocols and ABI versioning. |

##### crates/template_abi/src/

| File | Description |
|------|-------------|
| `call_info.rs` | Template call information and metadata. Defines structures for tracking template function calls, including call context, parameters, return values, and execution metadata. Used for debugging, profiling, and auditing template execution. |
| `lib.rs` | WASM Application Binary Interface (ABI) for smart contract templates. Defines low-level communication protocols between WASM runtime and templates, including function calls, engine operations, type encoding, and cross-compilation support. Foundation for secure template execution. |
| `ops.rs` | Template operation definitions for the ABI. Defines low-level operations that templates can perform through the engine interface including state access, cryptographic operations, and system calls. Core operation set for template-engine interaction. |
| `rust.rs` | Rust-specific template ABI bindings and utilities. Provides Rust language bindings for template development, including macros, type definitions, and helper functions for creating Rust-based Tari templates with proper ABI compliance. |
| `types.rs` | Type definitions for template ABI. Defines data types used in template-engine communication including primitive types, complex structures, and serialization formats. Ensures type safety and compatibility across the template execution boundary. |

###### crates/template_abi/src/abi/

| File | Description |
|------|-------------|
| `mod.rs` | ABI module organization. Exports WASM and non-WASM ABI implementations, providing a unified interface for template-engine communication across different execution environments. Organizes platform-specific ABI code. |
| `non_wasm.rs` | Non-WASM ABI implementation for native template execution. Provides direct function call interface for templates running in native environments without WASM runtime overhead. Used for development, testing, and high-performance scenarios where WASM isolation is not required. |
| `wasm.rs` | WASM ABI implementation for sandboxed template execution. Provides WebAssembly interface for secure template execution with proper isolation and resource limits. Handles WASM memory management, function imports/exports, and cross-boundary data marshalling for production template environments. |

#### crates/template_builtin/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for built-in Tari templates. Contains account, faucet, and NFT faucet templates that are included with the Tari DAN. Includes build script for template compilation and dependencies for template functionality. Provides essential templates for basic DAN operations. |
| `build.rs` | Build script for compiling built-in templates. Handles compilation of template source code into WASM binaries, template registration, and artifact management. Ensures built-in templates are properly compiled and included in the binary distribution. |

##### crates/template_builtin/src/

| File | Description |
|------|-------------|
| `account.rs` | Built-in Account template model for Tari smart contracts. Exports Account struct representing a multi-vault account mapped by ResourceAddress to Vault. Provides from_value() for BOR deserialization, vaults() accessor, and get_vault_by_resource() for vault lookup by resource type. Dependencies: BOR serialization, template_lib models. Used by: smart contracts for managing multiple resource vaults in a single account. Mirrors the Account built-in template state structure. |
| `lib.rs` | Built-in smart contract templates for core Tari Ootle functionality. Provides account template, NFT faucet template, and essential system templates with predefined addresses. Core system templates that are automatically available in all Ootle networks, providing fundamental account management and system utilities for users and applications. |

##### crates/template_builtin/templates/

| File | Description |
|------|-------------|
| `.gitignore` | Git ignore rules for template build artifacts. Excludes generated WASM files, compilation artifacts, and other build outputs from version control while preserving source template code. |

###### crates/template_builtin/templates/account/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the built-in account template. Basic account template providing fundamental account functionality including balance management, transfers, and access control. Core template for user account management in the Tari DAN. |

###### crates/template_builtin/templates/account/src/

| File | Description |
|------|-------------|
| `lib.rs` | Built-in account template implementation. Provides fundamental account functionality including balance management, transfer operations, access control, and multi-asset support. Core template for user account management in Tari DAN with basic wallet operations, authorization checks, and state management. |

###### crates/template_builtin/templates/faucet/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the built-in faucet template. Token distribution template for test networks and development environments. Provides controlled token dispensing with rate limiting and access controls for testing and development purposes. |

###### crates/template_builtin/templates/faucet/src/

| File | Description |
|------|-------------|
| `lib.rs` | Faucet template implementation for token distribution. Provides controlled token dispensing functionality with rate limiting, cooldown periods, and access controls. Used in test networks for distributing tokens to developers and testers with configurable limits and restrictions. |

###### crates/template_builtin/templates/nft_faucet/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the NFT faucet template. Non-fungible token distribution template for testing NFT functionality. Provides controlled NFT minting and distribution for development and testing environments. |

###### crates/template_builtin/templates/nft_faucet/src/

| File | Description |
|------|-------------|
| `lib.rs` | NFT faucet template implementation for non-fungible token distribution. Provides controlled NFT minting and distribution functionality with access controls and rate limiting. Used for testing NFT functionality and providing sample NFTs for development purposes. |

##### crates/template_builtin/tests/

| File | Description |
|------|-------------|
| `nft_faucet.rs` | Integration tests for the NFT faucet template. Tests NFT minting, distribution, access controls, rate limiting, and error conditions. Validates the NFT faucet template functionality and ensures proper behavior under various scenarios. |

#### crates/template_lib/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for tari_template_lib - the main template development library. Provides high-level APIs, types, and utilities for building Tari templates. Dependencies include engine types, BOR encoding, crypto libraries, and template ABI. Core library for template developers with comprehensive functionality for state management, resource handling, and DAN interaction. |
| `README.md` | Documentation for the template library. Comprehensive guide for template development including API reference, examples, best practices, and integration patterns. Covers state management, resource handling, authentication, and interaction with the Tari DAN. |

##### crates/template_lib/src/

| File | Description |
|------|-------------|
| `caller_context.rs` | Caller context information for template execution. Provides context about the caller including identity, permissions, transaction details, and execution environment. Used for authentication, authorization, and auditing within template methods. |
| `consensus.rs` | Consensus module for smart contract access to blockchain state. Exports Consensus struct with current_epoch() method for retrieving current chain epoch from validator node. Uses template_abi engine calls via EngineOp::ConsensusInvoke and ConsensusAction::GetCurrentEpoch. Used by: smart contracts needing epoch-aware functionality for time-based logic and consensus-dependent operations. Provides read-only access to chain consensus state. |
| `constants.rs` | Template library constants and configuration values. Defines system-wide constants including limits, default values, protocol parameters, and configuration settings used throughout the template system. |
| `context.rs` | Template execution context management for WASM smart contracts. Exports init_context() for setup, get_context() for access, and Context struct for template execution state. Features thread-local storage on WASM targets and panic on non-WASM environments. Dependencies: template_abi CallInfo for context initialization. Used by: template engine for maintaining execution context during smart contract function calls. Essential for template ABI context handling. |
| `engine.rs` | TariEngine interface for smart contract template interaction with the Tari execution engine. Provides engine() factory function returning TariEngine instance with methods for create_component (creating new components with state, owner rules, access rules), emit_log (logging with level support), and component_manager (accessing ComponentManager for component operations). Uses EngineOp calls to communicate with the underlying WASM execution environment via ABI. Core abstraction for template-to-engine communication in smart contract execution. |
| `events.rs` | Event system for template execution. Defines event types, emission mechanisms, and event handling for template operations. Provides observability and notification system for template state changes and method calls. |
| `lib.rs` | Template library providing ergonomic abstractions for WASM templates to interact with the Tari Ootle engine. Primary library for template authors including models, ResourceBuilder, ComponentBuilder, ABI functions, and macros. Contains comprehensive prelude, template examples, and fundamental types for building smart contracts. Essential dependency for all Rust-based Ootle templates with extensive documentation and examples. |
| `macros.rs` | Utility macros for template development. Provides convenience macros for common template patterns, code generation, and boilerplate reduction. Simplifies template development with declarative syntax for common operations. |
| `newtype_serde_macros.rs` | Newtype serialization macros for template types. Provides macros for generating serialization/deserialization code for newtype wrappers. Simplifies implementation of custom types with proper serde support and validation. |
| `panic_hook.rs` | Panic handling for template execution. Provides custom panic hooks for template runtime, error reporting, and graceful failure handling. Ensures template panics are properly caught and reported without crashing the host system. |
| `prelude.rs` | Prelude module providing commonly used types and functions for Tari smart contract development. Exports comprehensive template development toolkit including Value from tari_bor, cryptographic types (RistrettoPublicKeyBytes, SchnorrSignatureBytes, PedersenCommitmentBytes), template macros, authentication and access rules, component and resource management, consensus access, constants (CONFIDENTIAL_TARI_RESOURCE_ADDRESS, XTR), models (Amount, Bucket, ComponentAddress, etc.), resource building utilities, and logging functions. Single import point for most template development needs via 'use tari_template_lib::prelude::*;'. |
| `rand.rs` | Random number generation utilities for templates. Provides cryptographically secure random number generation for template operations including deterministic randomness where required. Ensures proper entropy and security for template randomness needs. |
| `template_dependencies.rs` | Template dependency management and resolution. Handles template dependencies, version compatibility, and dependency loading. Provides dependency resolution system for complex template interactions and composition. |

###### crates/template_lib/src/args/

| File | Description |
|------|-------------|
| `arg.rs` | Template argument handling and validation. Defines argument types, validation rules, and conversion utilities for template function parameters. Provides type-safe argument parsing and validation for template method calls with proper error handling. |
| `mod.rs` | Module organization for template argument handling. Exports argument types, validation utilities, and conversion functions. Organizes the argument processing system for template method calls and parameter validation. |
| `result.rs` | Template argument result handling. Defines result types for argument processing including success and error cases. Provides structured error reporting for argument validation failures with detailed context for debugging template calls. |
| `types.rs` | Template argument type definitions. Defines supported data types for template arguments including primitives, complex structures, and serialization formats. Ensures type safety and compatibility for template method parameters. |
| `value.rs` | Template argument value representation and conversion. Handles dynamic value types, serialization, and conversion between different argument formats. Provides runtime value handling for template method calls with proper type coercion. |

###### crates/template_lib/src/auth/

| File | Description |
|------|-------------|
| `access_rules.rs` | Access control rules for template authentication. Defines permission systems, access policies, and authorization rules for template methods and resources. Provides fine-grained access control with role-based permissions and conditional access. |
| `auth_hook.rs` | Authentication hooks for template execution. Provides callback mechanisms for custom authentication logic, authorization checks, and security policies. Enables templates to implement custom authentication flows and access control mechanisms. |
| `mod.rs` | Authentication module organization. Exports access rules, authentication hooks, and owner rules for template security. Organizes the authentication and authorization system for template access control. |
| `owner_rule.rs` | Ownership rules for template resources and components. Defines owner-based access control, ownership transfer mechanisms, and owner privilege validation. Manages resource ownership and provides owner-based authorization for sensitive operations. |

###### crates/template_lib/src/component/

| File | Description |
|------|-------------|
| `instance.rs` | Component instance management for template objects. Handles component instantiation, lifecycle management, and state tracking. Provides runtime management of template component instances with proper initialization and cleanup. |
| `manager.rs` | Component manager for template component lifecycle. Manages component creation, destruction, state persistence, and inter-component communication. Provides centralized component management with resource tracking and lifecycle coordination. |
| `mod.rs` | Component module organization. Exports component instance and manager types for template component system. Organizes the component management infrastructure for template object lifecycle and state management. |

###### crates/template_lib/src/models/

| File | Description |
|------|-------------|
| `address_allocation.rs` | Address allocation models for template resources. Manages allocation and tracking of addresses for components, resources, and other template objects. Provides deterministic address generation and allocation strategies. |
| `amount.rs` | Amount and quantity models for template operations. Defines Amount type for representing fungible quantities with proper arithmetic operations, precision handling, and overflow protection. Core type for financial and resource calculations. |
| `binary_tag.rs` | Binary tag models for resource classification. Defines binary tagging system for categorizing and identifying different types of resources and components. Provides metadata and classification capabilities for template objects. |
| `bucket.rs` | Bucket model for temporary resource holding. Represents containers for holding resources during template execution before depositing into vaults. Provides temporary resource management with automatic cleanup and validation. |
| `component.rs` | Component model definitions for template objects. Defines component structure, addressing, state management, and lifecycle. Core abstraction for stateful template objects with persistent state and method interfaces. |
| `confidential_proof.rs` | Confidential proof models for privacy-preserving operations. Handles zero-knowledge proofs, range proofs, and other cryptographic proofs for confidential transactions and resource operations. Provides privacy features while maintaining auditability. |
| `layer_one_commitment.rs` | Layer one commitment models for base layer integration. Handles commitments and proofs that bridge between the Tari base layer and DAN layer. Provides cryptographic linking and validation for cross-layer operations. |
| `metadata.rs` | Metadata models for template resources and components. Defines metadata structures, key-value storage, and annotation systems for template objects. Provides extensible metadata capabilities for resource description and classification. |
| `mod.rs` | Template models module organization. Exports all model types including components, resources, amounts, proofs, and other template data structures. Central module for template type definitions and core abstractions. |
| `non_fungible.rs` | Non-fungible token models and operations. Defines NFT structure, unique identifiers, metadata handling, and NFT-specific operations. Provides comprehensive NFT functionality including minting, transfer, and metadata management. |
| `proof.rs` | Proof models for cryptographic validation. Defines various proof types including ownership proofs, execution proofs, and validation proofs. Provides cryptographic verification capabilities for template operations and resource access. |
| `resource.rs` | Resource models for fungible and non-fungible assets. Defines resource types, properties, and management operations. Core abstraction for all digital assets in the template system with comprehensive resource lifecycle management. |
| `system.rs` | System models for template runtime environment. Defines system-level types, environment information, and runtime context. Provides access to system capabilities and environment state for template execution. |
| `vault.rs` | Vault models for secure resource storage. Defines vault structure, access controls, and resource management operations. Provides secure long-term storage for resources with authorization and audit capabilities. |

###### crates/template_lib/src/resource/

| File | Description |
|------|-------------|
| `manager.rs` | Resource manager for resource lifecycle operations. Handles resource creation, minting, burning, transfer, and metadata management. Provides centralized resource management with proper authorization and validation. |
| `mod.rs` | Resource module organization. Exports resource builders and managers for comprehensive resource management functionality. Organizes the resource system including creation, management, and lifecycle operations. |

###### crates/template_lib/src/resource/builder/

| File | Description |
|------|-------------|
| `confidential.rs` | Confidential resource builder for privacy-preserving assets. Provides builder pattern for creating confidential resources with zero-knowledge proofs, range proofs, and privacy features. Enables private asset creation and management. |
| `fungible.rs` | Fungible resource builder for creating standard tokens. Provides builder pattern for fungible token creation with configurable properties including supply, divisibility, metadata, and access controls. Core builder for token creation. |
| `mod.rs` | Resource builder module organization. Exports builders for different resource types including fungible, non-fungible, and confidential resources. Provides unified interface for resource creation with type-specific configurations. |
| `non_fungible.rs` | Non-fungible resource builder for creating NFTs. Provides builder pattern for NFT creation with configurable properties including unique identifiers, metadata schemas, and access controls. Specialized builder for NFT creation and management. |

###### crates/template_lib/src/template/

| File | Description |
|------|-------------|
| `builtin.rs` | Built-in template definitions and registration. Provides access to system-provided templates including account, faucet, and NFT templates. Handles built-in template loading and integration with the template system. |
| `manager.rs` | Template manager for template lifecycle operations. Handles template loading, compilation, execution, and state management. Provides centralized template management with proper isolation and resource control. |
| `mod.rs` | Template module organization. Exports template manager and built-in template functionality. Organizes the template execution and management system for template lifecycle operations. |

#### crates/template_lib_types/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for tari_template_lib_types - core type definitions for template system. Provides fundamental types, cryptographic primitives, and serialization support for template operations. Shared type library used across template components with minimal dependencies. |

##### crates/template_lib_types/src/

| File | Description |
|------|-------------|
| `entity_id.rs` | Entity identifier types for template objects. Defines unique identifiers for template entities including components, resources, and other template objects. Provides type-safe identification and addressing within the template system. |
| `error.rs` | Error types for template lib operations. Defines comprehensive error handling for template execution including runtime errors, validation failures, and system errors. Provides structured error reporting with proper context and error chains. |
| `hash.rs` | Hash utilities and types for template operations. Provides hash functions, digest types, and hashing utilities for template data structures. Ensures consistent hashing across the template system with proper cryptographic security. |
| `hex.rs` | Hexadecimal encoding/decoding utilities for template types. Provides hex string conversion for binary data, keys, and hashes. Used for serialization, debugging, and human-readable representation of binary template data. |
| `lib.rs` | Fundamental type definitions for Tari smart contract templates. Provides crypto types, entity IDs, error handling, hash utilities, hex encoding, and serialization helpers. Defines TemplateAddress type and core primitives used across the template system for type safety and consistent data handling in smart contract development. |
| `serde_helpers.rs` | Serialization helper utilities for template types. Provides custom serde serializers/deserializers for complex template types including cryptographic types and specialized formats. Ensures consistent serialization across the template system. |

###### crates/template_lib_types/src/crypto/

| File | Description |
|------|-------------|
| `balance_proof.rs` | Balance proof cryptographic primitives for confidential transactions. Implements zero-knowledge proofs for balance validation without revealing actual amounts. Provides privacy-preserving balance verification for confidential assets. |
| `commitment.rs` | Cryptographic commitment primitives for template operations. Implements Pedersen commitments and related cryptographic constructs for confidential transactions and privacy-preserving operations. Core cryptographic building blocks for private assets. |
| `commitment_signature.rs` | Commitment signature schemes for cryptographic authorization. Implements signature schemes that work with commitments for privacy-preserving authentication and authorization. Enables signing while maintaining commitment privacy. |
| `mod.rs` | Cryptographic module organization for template types. Exports cryptographic primitives including commitments, signatures, proofs, and key types. Organizes the cryptographic foundation for template security and privacy features. |
| `ristretto.rs` | Ristretto cryptographic group operations for template system. Implements Ristretto255 elliptic curve operations, point arithmetic, and key management. Provides the cryptographic foundation for template security and privacy operations. |
| `scalar.rs` | Scalar arithmetic for cryptographic operations. Implements scalar field operations for Ristretto255 including arithmetic, serialization, and validation. Core component for cryptographic computations in template operations. |
| `schnorr.rs` | Schnorr signature implementation for template authentication. Provides Schnorr signature creation, verification, and key management for template security. Standard signature scheme for template authorization and validation. |

###### crates/template_lib_types/src/models/

| File | Description |
|------|-------------|
| `binary_tag.rs` | Binary tag type definitions for resource classification. Defines type-safe binary tags for categorizing and identifying template resources and components. Provides metadata and classification system for template objects. |
| `component.rs` | Component type definitions for template objects. Defines component addresses, state structures, and component-related types. Core types for template component system including addressing and state management. |
| `mod.rs` | Template lib types model organization. Exports component and binary tag types for template system. Organizes core type definitions used across the template infrastructure. |

#### crates/template_macros/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for template procedural macros. Provides compile-time code generation for template development including ABI generation, template definition macros, and boilerplate reduction. Essential tooling for template developers to simplify template creation. |

##### crates/template_macros/src/

| File | Description |
|------|-------------|
| `lib.rs` | Procedural macros for Tari smart contract template development. Provides the #[template] macro that generates template definition and dispatcher code from annotated Rust code. Essential macro system enabling declarative smart contract development with automatic code generation for WASM compilation and Ootle integration. |

###### crates/template_macros/src/template/

| File | Description |
|------|-------------|
| `abi.rs` | ABI generation macros for template interfaces. Provides procedural macros for automatically generating ABI definitions from template code. Ensures type safety and consistency between template implementations and their interfaces. |
| `ast.rs` | Abstract syntax tree utilities for template macro processing. Provides AST parsing, manipulation, and code generation utilities for template macros. Core infrastructure for compile-time template code analysis and generation. |
| `definition.rs` | Template definition macros for declarative template creation. Provides macros for defining template structure, methods, and metadata in a declarative syntax. Simplifies template development with automatic code generation and validation. |
| `dispatcher.rs` | Template method dispatcher generation macros. Provides automatic generation of method dispatch code for template method calls. Handles method routing, parameter validation, and result handling for template execution. |
| `mod.rs` | Template macro module organization. Exports template macros including ABI generation, AST utilities, definition macros, and dispatcher generation. Organizes the template compile-time code generation system. |

#### crates/template_manager/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for template manager - handles template lifecycle and synchronization. Manages template loading, compilation, caching, and distribution across the network. Key component for template deployment and management in DAN nodes with networking and storage dependencies. |

##### crates/template_manager/src/

| File | Description |
|------|-------------|
| `lib.rs` | Template management service providing storage, retrieval, and lifecycle management for smart contract templates. Includes implementation and interface layers for template registration, validation, and deployment across the validator network. |

###### crates/template_manager/src/implementation/

| File | Description |
|------|-------------|
| `cmap_semaphore.rs` | Concurrent map semaphore for template resource management. Provides synchronized access to template resources with proper concurrency control. Manages resource limits and access coordination for template operations. |
| `downloader.rs` | Template downloader for fetching templates from network peers. Handles template discovery, download, verification, and caching from other network nodes. Provides reliable template distribution across the DAN network. |
| `initializer.rs` | Template manager initialization and setup. Handles template manager startup, configuration loading, and initial template discovery. Manages the bootstrap process for template management services. |
| `manager.rs` | Core template manager implementation. Handles template lifecycle management including loading, compilation, caching, versioning, and execution. Central coordinator for all template operations with proper resource management and synchronization. |
| `mod.rs` | Template manager implementation module organization. Exports all implementation components including manager, downloader, sync workers, and services. Organizes the template management system implementation. |
| `service.rs` | Template manager service layer implementation. Provides service interface for template operations including RPC endpoints, request handling, and service lifecycle management. Bridges external requests with internal template management. |
| `sync_worker.rs` | Template synchronization worker for network-wide template distribution. Handles background synchronization of templates across network nodes including polling, conflict resolution, and consistency maintenance. Ensures all nodes have up-to-date template versions. |
| `template_config.rs` | Template configuration management for template manager. Handles template-specific configuration including compilation settings, runtime parameters, and deployment options. Provides configuration persistence and validation for template management. |
| `template_sync_task.rs` | Template synchronization task implementation. Manages individual template sync operations including download scheduling, verification, and installation. Handles template update propagation and version management across the network. |

###### crates/template_manager/src/interface/

| File | Description |
|------|-------------|
| `error.rs` | Template manager error types and handling. Defines comprehensive error categories for template management operations including download failures, compilation errors, and synchronization issues. Provides structured error reporting for template operations. |
| `handle.rs` | Template manager handle and client interface. Provides high-level API for interacting with the template manager including template operations, status queries, and lifecycle management. Client-facing interface for template management operations. |
| `mod.rs` | Template manager interface module organization. Exports client interfaces, error types, and handle types for template manager interactions. Organizes the public API for template management services. |
| `types.rs` | Template manager interface type definitions. Defines request/response types, status enums, and configuration structures for template manager API. Provides type safety for template management operations and client interactions. |

#### crates/template_test_tooling/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for template testing utilities and tooling. Provides comprehensive testing framework for template development including test harnesses, mock environments, and assertion utilities. Essential tooling for template developers to validate template functionality. |

##### crates/template_test_tooling/src/

| File | Description |
|------|-------------|
| `builtin_component_state.rs` | Built-in component state utilities for template testing. Provides predefined component states and fixtures for testing template interactions with system components. Simplifies testing setup for complex template scenarios. |
| `lib.rs` | Testing framework and utilities for Tari smart contract templates. Provides TemplateTest framework, Package builder, read-only state store, component state management, and cryptographic utilities. Essential tooling for unit testing smart contracts with mock environments and test harnesses before deployment. |
| `package_builder.rs` | Template package builder for test scenarios. Provides utilities for creating and assembling template packages for testing including dependencies, metadata, and configuration. Enables comprehensive integration testing of template deployments. |
| `read_only_state_store.rs` | Read-only state store for template testing. Provides immutable state access for testing template behavior without side effects. Enables deterministic testing by providing consistent state snapshots for template execution validation. |
| `template_test.rs` | Core template testing framework and harness. Provides comprehensive testing infrastructure for template execution including test setup, execution environment, and validation utilities. Central framework for template unit and integration testing. |
| `track_calls.rs` | Template call tracking utilities for testing and debugging. Provides instrumentation for tracking template method calls, parameters, and return values during testing. Enables detailed analysis of template execution behavior. |
| `wrapped_transaction.rs` | Transaction wrapper utilities for template testing. Provides wrapped transaction types with additional testing context and debugging information. Enables detailed transaction testing with enhanced observability and validation. |

###### crates/template_test_tooling/src/support/

| File | Description |
|------|-------------|
| `assert_error.rs` | Error assertion utilities for template testing. Provides specialized assertion macros and utilities for validating error conditions in template execution. Enables comprehensive error handling testing for template development. |
| `confidential.rs` | Confidential transaction testing utilities. Provides tools for testing confidential transactions, privacy features, and zero-knowledge proof validation in templates. Enables comprehensive testing of privacy-preserving template functionality. |
| `mod.rs` | Template testing support module organization. Exports testing utilities including error assertions, confidential transaction support, and other testing helpers. Organizes comprehensive testing support infrastructure for template development. |

###### crates/template_test_tooling/templates/faucet/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for test faucet template used in testing infrastructure. Testing version of the faucet template specifically designed for template testing scenarios with additional debugging and validation features. |

###### crates/template_test_tooling/templates/faucet/src/

| File | Description |
|------|-------------|
| `lib.rs` | Test faucet template implementation for testing framework. Enhanced faucet template with additional testing hooks, debugging features, and validation capabilities. Used for comprehensive template testing scenarios and framework validation. |

#### crates/transaction/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for tari_transaction crate - core transaction handling for Tari DAN. Provides transaction types, validation, serialization, and execution logic. Dependencies include engine types, crypto libraries, and template system. Fundamental component for transaction processing and validation in the DAN. |

##### crates/transaction/src/

| File | Description |
|------|-------------|
| `lib.rs` | Transaction system library providing transaction building, signing, validation, and execution types. Exports TransactionBuilder for transaction construction, transaction types, transaction ID management, unsigned transaction handling, versioning support, and weight calculation. Core infrastructure for all transaction operations including instruction sequences, fee management, and cryptographic signatures. |
| `transaction.rs` | Core transaction wrapper enum providing unified interface for all transaction versions. Exports: Transaction enum (currently V1 only), transaction builder, ID calculation, weight computation. Key methods: calculate_id(), verify_all_signatures(), inputs(), instructions(), network(). Dependencies: engine_types, template_lib, ootle_common_types. Used by: all transaction processing, validation, and execution workflows. Provides version-agnostic transaction interface. |
| `transaction_id.rs` | Transaction identifier type with 32-byte hash representation. Exports: TransactionId struct with creation, conversion, and validation methods. Key methods: new(), as_bytes(), from_hex(), into_receipt_address(). Dependencies: tari_crypto for hex utilities, engine_types for receipt addresses. Used by: transaction indexing, receipt generation, substate addressing. Provides unique transaction identification with various conversion utilities. |
| `unique.rs` | Unique constraint enforcement for transaction elements ensuring no duplicate components or addresses in transaction execution. Exports: uniqueness validation functions and types. Dependencies: core transaction types. Used by: transaction validation pipeline to prevent duplicate resource usage and ensure transaction integrity. |
| `unsigned_transaction.rs` | Unsigned transaction representation containing instructions and metadata before signing. Exports: UnsignedTransaction enum with instruction management, input handling, and builder integration. Key methods: instructions(), fee_instructions(), build(), add_input(). Dependencies: engine instruction types, common types. Used by: transaction construction before signature application, transaction builder output. |
| `weight.rs` | Transaction weight calculation for fee determination and resource usage estimation. Exports: TransactionWeight struct with calculation methods for different transaction components. Dependencies: transaction instruction types. Used by: transaction fee calculation, network resource planning. Provides deterministic weight calculation based on transaction complexity and resource usage. |

###### crates/transaction/src/builder/

| File | Description |
|------|-------------|
| `mod.rs` | Transaction builder module providing fluent API for constructing transactions. Exports TransactionBuilder struct with methods for building complex transactions including: account creation, function/method calls, fee instructions, input management, and workspace allocation. Key exports: TransactionBuilder, NamedComponentCall. Dependencies: tari_engine_types, tari_template_lib, tari_ootle_common_types. Used by: transaction creation workflows, wallet operations. Supports both simple and complex transaction patterns with named arguments and workspace management. |
| `named_args.rs` | Named argument system for transaction builder with workspace and literal value support. Exports: NamedArg enum, BuilderWorkspaceKey, ParsedBuilderWorkspaceKey, parse_workspace_key function, arg! and args! macros. Dependencies: tari_bor for serialization. Used by: TransactionBuilder for flexible argument passing. Provides macro-based DSL for constructing instruction arguments supporting both workspace references and literal values. |
| `named_component_call.rs` | Component call abstraction supporting both direct addresses and workspace references. Exports: NamedComponentCall enum, CallFromWorkspace struct. Dependencies: tari_template_lib for ComponentAddress. Used by: TransactionBuilder for flexible component method calls. Enables calling methods on components either by direct address or by workspace reference name. |
| `tests.rs` | Test module for transaction builder functionality. Contains unit tests for TransactionBuilder methods, argument handling, workspace management, and transaction construction. Dependencies: transaction builder components, test utilities. Used for: validating transaction builder API correctness and ensuring proper transaction construction workflows. |
| `workspace_ids.rs` | Workspace ID management for transaction builder providing mapping between named keys and numeric workspace IDs. Exports: WorkspaceIds struct with insert/get methods for mapping BuilderWorkspaceKey to WorkspaceOffsetId. Dependencies: template_lib workspace types. Used by: TransactionBuilder for managing workspace references and resolving named workspace keys to engine-compatible workspace IDs. |

###### crates/transaction/src/v1/

| File | Description |
|------|-------------|
| `mod.rs` | Version 1 transaction implementation module. Exports: TransactionV1, UnsealedTransactionV1, transaction signature types. Dependencies: v1-specific signature and transaction components. Used by: transaction enum wrapper for v1-specific functionality. Contains v1 transaction format with sealing, signature verification, and execution context. |
| `signature.rs` | Transaction signature implementation for v1 transactions using Ristretto cryptography. Exports: TransactionSignature, signature creation and verification methods. Dependencies: tari_crypto for Ristretto operations, private key types. Used by: TransactionV1 for signature management and verification. Provides cryptographic signature functionality for transaction authentication. |
| `transaction.rs` | Version 1 transaction implementation with sealing, signature verification, and execution context. Exports: TransactionV1 struct with signature management, component reference extraction, weight calculation. Dependencies: v1 signature types, unsealed transaction. Used by: Transaction enum for v1-specific functionality. Core implementation of v1 transaction format with full signature verification. |
| `unsealed.rs` | Unsealed transaction v1 implementation containing instructions before final sealing. Exports: UnsealedTransactionV1 with instruction access, component iteration, and sealing capability. Dependencies: unsigned transaction, signature types. Used by: transaction builder output before sealing, transaction construction pipeline. Intermediate state between unsigned and sealed transactions. |
| `unsigned.rs` | Unsigned transaction v1 implementation with instruction and input management. Exports: UnsignedTransactionV1 struct with instruction manipulation, input handling, epoch constraints. Dependencies: engine instruction types, common substate types. Used by: transaction builder for pre-signature transaction construction. Contains transaction content before any cryptographic signatures are applied. |

#### crates/transaction_manifest/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for tari_transaction_manifest crate providing transaction manifest parsing and AST generation. Dependencies: tari_template_lib, tari_engine_types, tari_template_builtin, tari_bor, proc-macro2, syn, thiserror. Used for: parsing transaction manifests into executable instructions. Enables declarative transaction construction through manifest files. |

##### crates/transaction_manifest/src/

| File | Description |
|------|-------------|
| `ast.rs` | Abstract Syntax Tree representation for transaction manifests. Exports: ManifestAst struct implementing syn::Parse for parsing manifest syntax into structured representation. Dependencies: syn for parsing, ManifestParser. Used by: manifest compilation pipeline to convert manifest text into executable transaction instructions. Provides structured representation of manifest content. |
| `error.rs` | Error types for transaction manifest parsing and compilation. Exports: manifest-specific error enums with detailed error information for parsing failures, validation errors, and compilation issues. Dependencies: standard error traits, syn error types. Used by: manifest parser, AST generation, transaction compilation pipeline. Provides comprehensive error reporting for manifest processing. |
| `generator.rs` | Transaction manifest instruction generator for converting high-level transaction descriptions to executable instructions. Exports ManifestInstructionGenerator that processes ManifestAst into ManifestInstructions, handling template imports, variable resolution, workspace management, and argument processing. Features support for template/component invocation, input assignment, logging, and type-safe literal conversion. Dependencies: engine types, template_lib. Used by: transaction builder for converting user-friendly manifests to low-level engine instructions. |
| `lib.rs` | Transaction manifest parsing and compilation system. Provides AST parsing, instruction generation, and manifest compilation for creating structured transactions from high-level descriptions. Enables declarative transaction construction with template addressing and instruction sequencing for complex multi-step operations. |
| `parser.rs` | Transaction manifest parser converting text manifests into ParsedManifest structures. Exports: ManifestParser, ParsedManifest with parsing methods for manifest syntax. Dependencies: syn for Rust syntax parsing, AST types. Used by: ManifestAst for parsing manifest files into structured data. Core parser implementation for declarative transaction manifest language. |
| `value.rs` | Value representation and conversion for transaction manifest data types. Exports: value types, conversion functions between manifest values and engine types. Dependencies: template_lib types, engine types for value representation. Used by: manifest parser for converting parsed values into transaction instruction arguments. Handles type conversion between manifest syntax and executable values. |

##### crates/transaction_manifest/tests/

| File | Description |
|------|-------------|
| `parser.rs` | Parser tests for transaction manifest syntax validation and parsing correctness. Contains test cases for manifest parsing, AST generation, error handling, and edge cases. Dependencies: manifest parser, test utilities. Used for: ensuring robust manifest parsing and comprehensive error reporting. Validates parser behavior across various manifest syntax scenarios. |

###### crates/transaction_manifest/tests/examples/

| File | Description |
|------|-------------|
| `picture_seller.rs` | Example test demonstrating picture seller marketplace scenario using transaction manifests. Contains test case for multi-party transaction involving picture sales, payments, and component interactions. Dependencies: manifest testing framework, example templates. Used for: validating manifest functionality with real-world use case. Demonstrates complex transaction composition through declarative manifests. |

#### crates/validator_node_rpc/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for validator node RPC implementation providing peer-to-peer RPC services. Dependencies: tari_networking, tari_rpc_framework, tari_ootle_p2p, tari_ootle_storage, async-trait, prost for protobuf. Build dependencies: proto_builder for gRPC code generation. Used for: validator node communication, consensus protocol implementation. Enables RPC communication between validator nodes. |
| `README.md` | Documentation for validator node RPC crate explaining peer-to-peer RPC implementation, service definitions, and usage patterns. Contains: service descriptions, RPC method documentation, integration examples. Used for: developer guidance on validator node RPC integration and understanding RPC service architecture. |

##### crates/validator_node_rpc/src/

| File | Description |
|------|-------------|
| `client.rs` | RPC client implementation for connecting to validator nodes with async communication capabilities. Exports: RPC client struct, connection management, method call wrappers. Dependencies: networking framework, RPC framework, protobuf types. Used by: other validator nodes, indexer services for peer communication. Provides typed client interface for validator node RPC services. |
| `error.rs` | Validator node RPC client error types for Tari P2P communication. Exports ValidatorNodeRpcClientError enum covering protocol violations, networking, RPC framework, status errors, invalid responses, and BOR serialization errors. Implements IsNotFoundError trait for query handling. Dependencies: NetworkingError, RpcError, RpcStatus, BorError. Used by: validator node clients for handling distributed consensus communication failures and network-level errors. |
| `lib.rs` | RPC service definitions and client implementations for validator node communication. Provides RPC client, error handling, and service interfaces for inter-validator communication, consensus messaging, and network coordination. Essential component enabling structured communication between validator nodes in the distributed network. |
| `rpc_service.rs` | RPC service implementation for validator node providing consensus-related RPC endpoints. Exports: RPC service trait implementations, request handlers for consensus protocols. Dependencies: storage layer, consensus types, networking framework. Used by: validator node main process to expose RPC endpoints. Implements core validator node RPC service interface for peer communication. |

##### crates/wallet/crypto/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for wallet cryptographic operations providing confidential transaction support. Dependencies: tari_engine_types, tari_template_lib, tari_crypto, blake2, chacha20poly1305, digest, rand for cryptographic operations. Dev dependencies: tari_template_test_tooling, serde_json. Used for: confidential proofs, encrypted outputs, privacy-preserving wallet operations. |

###### crates/wallet/crypto/src/

| File | Description |
|------|-------------|
| `api.rs` | Wallet crypto API providing confidential transaction operations including withdraw proofs, value encryption/decryption, and output unblinding. Exports: create_withdraw_proof(), encrypt_value_and_mask(), extract_value_and_mask(), unblind_output(), WalletCryptoError. Dependencies: chacha20poly1305, tari_crypto, confidential proof system. Used by: wallet SDK for privacy-preserving transactions. Core cryptographic operations for confidential asset management. |
| `confidential_output.rs` | Confidential output data structures for encrypted asset values and commitments. Exports: ConfidentialOutputMaskAndValue struct with value and mask pairs for confidential transactions. Dependencies: crypto primitives for confidential commitments. Used by: confidential transaction construction, proof generation. Represents confidential output state with cryptographic binding between value and commitment. |
| `confidential_statement.rs` | Confidential transaction statement generation for range proofs and output statements. Exports: ConfidentialProofStatement with mask, revealed amounts, and range proof components. Dependencies: confidential proof system, crypto primitives. Used by: confidential transaction construction, withdraw proof generation. Creates cryptographic statements proving confidential transaction validity. |
| `error.rs` | Error types for wallet cryptographic operations including confidential proof errors, encryption failures, and commitment validation errors. Exports: crypto-specific error enums with detailed error information. Dependencies: standard error traits, cryptographic error types. Used by: wallet crypto API, proof generation, encryption operations. Provides comprehensive error handling for cryptographic wallet operations. |
| `kdfs.rs` | Key derivation functions for wallet cryptographic operations including AEAD key derivation and encryption key generation. Exports: KDF functions for various cryptographic contexts, Diffie-Hellman key derivation. Dependencies: cryptographic primitives, hash functions. Used by: confidential output encryption, value encryption/decryption. Provides secure key derivation for wallet cryptographic protocols. |
| `lib.rs` | Cryptographic library for Tari wallet operations providing confidential transactions, zero-knowledge proofs, and value privacy. Exports error handling, key derivation functions (KDFs), confidential proofs, output handling, statements, and value lookup utilities. Core cryptographic foundation enabling private and secure wallet operations in the Ootle ecosystem. |
| `proof.rs` | Confidential proof generation and verification for private transactions including range proofs and balance proofs. Exports: proof creation functions, statement generation, encryption/decryption utilities. Dependencies: confidential proof system, cryptographic primitives. Used by: wallet API for confidential transaction construction. Core implementation of privacy-preserving cryptographic proofs. |

###### crates/wallet/crypto/src/value_lookup/

| File | Description |
|------|-------------|
| `header.rs` | Header structures for value lookup operations in confidential transactions. Exports: header types for value lookup tables, metadata structures. Dependencies: crypto primitives for header validation. Used by: value lookup operations, confidential output scanning. Provides header format for efficient confidential value scanning and lookup. |
| `io_reader_value_lookup.rs` | I/O reader implementation for value lookup operations enabling efficient scanning of confidential outputs. Exports: reader implementation for value lookup, streaming operations. Dependencies: I/O traits, crypto primitives, value lookup headers. Used by: wallet scanning operations, confidential output discovery. Provides efficient streaming interface for confidential value scanning. |
| `mod.rs` | Value lookup module for confidential output scanning and discovery providing efficient search through encrypted values. Exports: value lookup traits, implementation types, header and I/O components. Dependencies: crypto value lookup system. Used by: wallet scanning, confidential output discovery. Enables efficient scanning of confidential outputs without full decryption. |

###### crates/wallet/crypto/tests/

| File | Description |
|------|-------------|
| `output_statement.rs` | Tests for confidential output statement generation and validation ensuring correct proof construction. Contains test cases for output statement creation, range proof validation, commitment verification. Dependencies: wallet crypto components, test utilities. Used for: validating confidential output statement correctness and security properties. |
| `viewable_balance_proof.rs` | Tests for viewable balance proof functionality enabling selective balance disclosure. Contains test cases for balance proof generation, verification, and viewability constraints. Dependencies: crypto proof system, test framework. Used for: ensuring correct implementation of viewable balance proofs and privacy controls. |

##### crates/wallet/sdk/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for wallet SDK providing high-level wallet functionality and API layers. Dependencies: tari components, key management, storage, networking, async-trait, serde, anyhow. Dev dependencies: testing utilities, tempfile. Used for: wallet application development, integration with Tari ecosystem. Main SDK interface for wallet operations. |

###### crates/wallet/sdk/src/

| File | Description |
|------|-------------|
| `lib.rs` | Wallet SDK providing high-level wallet functionality for Layer-2 operations. Includes storage abstractions, API clients, wallet models, configuration, and key management. Enables wallet applications to interact with Tari Ootle network for account management and transactions. |
| `network.rs` | Network interface abstraction for wallet SDK providing blockchain communication layer. Exports: WalletNetworkInterface trait with methods for substate queries, transaction submission. Dependencies: network types, async traits. Used by: wallet APIs for blockchain interaction. Abstraction layer enabling different network implementations. |
| `sdk.rs` | Main wallet SDK implementation providing centralized access to all wallet functionality including initialization, configuration, and API access. Exports: WalletSdk struct, WalletSdkConfig, initialization methods, API getters. Dependencies: all wallet APIs, key management, storage, networking. Used by: wallet applications as main entry point. Core SDK orchestrating all wallet operations with lifecycle management. |
| `storage.rs` | Storage abstraction layer for wallet SDK defining storage traits and error types. Exports: WalletStore trait, WalletStoreReader/Writer traits, WalletStorageError. Dependencies: storage operation types, async traits. Used by: all wallet APIs for data persistence. Provides abstraction enabling different storage backend implementations. |

###### crates/wallet/sdk/src/apis/

| File | Description |
|------|-------------|
| `accounts.rs` | Accounts API for wallet SDK providing account management, balance queries, and vault operations. Exports: AccountsApi with methods for account creation, balance retrieval, vault management. Dependencies: storage layer, engine types, template lib. Used by: wallet SDK for account operations, balance management. Core account management functionality with storage integration. |
| `confidential_crypto.rs` | Confidential cryptography API providing high-level interface for confidential transaction operations. Exports: ConfidentialCryptoApi with methods for proof generation, encryption, and confidential operations. Dependencies: wallet crypto layer, cryptographic primitives. Used by: wallet SDK for privacy-preserving operations. Bridges high-level wallet operations with low-level cryptographic functions. |
| `confidential_outputs.rs` | Confidential outputs API for managing encrypted transaction outputs and commitments. Exports: ConfidentialOutputsApi with output scanning, creation, and management methods. Dependencies: storage layer, crypto API, key management. Used by: wallet SDK for confidential output operations. Manages lifecycle of confidential outputs from creation to spending. |
| `confidential_transfer.rs` | Confidential transfer API orchestrating private transactions with encrypted inputs and outputs. Exports: ConfidentialTransferApi with transfer construction, proof generation, and submission methods. Dependencies: key manager, accounts, confidential outputs, crypto API. Used by: wallet SDK for private transaction flows. High-level API for confidential asset transfers. |
| `config.rs` | Configuration API for wallet SDK providing settings management and configuration storage. Exports: ConfigApi with get/set methods, ConfigKey enum, configuration errors. Dependencies: storage layer, encryption support. Used by: wallet SDK for configuration management, network settings, user preferences. Centralized configuration management with encryption support. |
| `key_manager.rs` | Key management API providing cryptographic key derivation and management for wallet operations. Exports: KeyManagerApi with key derivation, address generation, signing methods. Dependencies: tari_key_manager, cipher seed, storage layer. Used by: wallet SDK for all cryptographic key operations. Core key management functionality with hierarchical deterministic key derivation. |
| `mod.rs` | Wallet SDK API module aggregating all wallet functionality interfaces. Exports APIs for accounts, confidential crypto, non-fungible tokens, substates, templates, transactions, and configuration. Core API layer providing structured access to wallet operations. |
| `non_fungible_tokens.rs` | Non-fungible tokens API for managing NFT operations including creation, transfer, and metadata management. Exports: NonFungibleTokensApi with NFT lifecycle management methods. Dependencies: storage layer, template lib for NFT types. Used by: wallet SDK for NFT operations. Provides comprehensive NFT management functionality. |
| `substate.rs` | Substate API for querying and managing blockchain state including component and resource states. Exports: SubstatesApi with substate querying, caching, and synchronization methods. Dependencies: storage layer, network interface, substate types. Used by: wallet SDK for blockchain state access. Provides abstraction layer for substate operations with caching. |
| `template.rs` | Template API for managing authored templates and template registration operations. Exports: TemplateApi with template storage, retrieval, and registration methods. Dependencies: storage layer, template types. Used by: wallet SDK for template development and deployment. Manages lifecycle of user-authored templates. |
| `transaction.rs` | Transaction API for wallet SDK providing transaction construction, submission, and monitoring. Exports: TransactionApi with transaction building, execution, and status tracking methods. Dependencies: storage layer, network interface, transaction types. Used by: wallet SDK for all transaction operations. High-level transaction management with network integration. |

###### crates/wallet/sdk/src/models/

| File | Description |
|------|-------------|
| `account.rs` | Account model definitions for wallet SDK representing user accounts and their metadata. Exports: Account struct with address, keys, balance information, vault associations. Dependencies: engine types, template lib for addresses. Used by: accounts API, storage layer. Core account representation with rich metadata and vault tracking. |
| `authored_template.rs` | Authored template model representing user-created templates with metadata and function definitions. Exports: AuthoredTemplate struct with template address, author information, function metadata. Dependencies: template types, crypto keys. Used by: template API, storage layer. Represents compiled user templates with deployment information. |
| `confidential_output.rs` | Confidential output model representing encrypted transaction outputs with commitments and proofs. Exports: ConfidentialOutput struct with encrypted data, commitments, claim information. Dependencies: crypto types, engine types. Used by: confidential outputs API, storage layer. Core model for private transaction outputs with cryptographic binding. |
| `config.rs` | Configuration model for wallet settings and parameters with encryption support. Exports: Config struct with key-value storage, encryption flags, timestamps. Dependencies: storage types, encryption utilities. Used by: config API, wallet initialization. Persistent configuration storage with selective encryption. |
| `mod.rs` | Wallet SDK data models representing wallet state and operations. Exports account models, wallet transactions, proofs, confidential outputs, substates, vaults, NFTs, and WebAuthn registrations. Core data structures for wallet functionality and state management. |
| `non_fungible_tokens.rs` | Non-fungible token model representing NFT metadata, ownership, and attributes. Exports: NonFungibleToken struct with token ID, metadata, owner information. Dependencies: template lib NFT types, engine types. Used by: NFT API, storage layer. Comprehensive NFT representation with metadata and ownership tracking. |
| `proof.rs` | Proof model for storing cryptographic proofs and verification data. Exports: Proof struct with proof data, verification parameters, metadata. Dependencies: crypto proof types, storage types. Used by: confidential operations, proof storage and retrieval. Persistent storage model for cryptographic proofs. |
| `substate.rs` | Substate model representing cached blockchain state information. Exports: Substate struct with state data, versioning, synchronization metadata. Dependencies: engine substate types, storage types. Used by: substate API, state caching. Local representation of blockchain substates with caching support. |
| `vault.rs` | Vault model representing resource containers with balance tracking and metadata. Exports: Vault struct with vault ID, resource information, balance data. Dependencies: template lib vault types, resource types. Used by: accounts API, balance management. Resource container model with balance and metadata tracking. |
| `wallet_transaction.rs` | Wallet transaction model representing transaction history and metadata. Exports: WalletTransaction struct with transaction data, status, execution information. Dependencies: transaction types, engine types. Used by: transaction API, transaction history. Comprehensive transaction record with execution details and status tracking. |
| `webauthn_registration.rs` | WebAuthn registration model for biometric and hardware authentication support. Exports: WebAuthnRegistration struct with credential data, device information. Dependencies: webauthn types, crypto primitives. Used by: authentication flows, security features. Stores WebAuthn credential registration data for enhanced security. |

###### crates/wallet/sdk/tests/

| File | Description |
|------|-------------|
| `confidential_output_api.rs` | Integration tests for confidential output API validating encrypted output operations, scanning, and management. Contains test cases for output creation, scanning, unblinding, and lifecycle management. Dependencies: wallet SDK, test utilities, crypto components. Used for: ensuring confidential output API correctness and integration with storage layer. |

##### crates/wallet/storage_sqlite/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for SQLite storage implementation providing persistent wallet data storage. Dependencies: diesel for ORM, sqlite3, chrono for timestamps, serde, storage trait implementations. Dev dependencies: testing utilities, tempfile. Build script: diesel.toml for migrations. Used for: production wallet storage with SQLite backend. |
| `README.md` | Documentation for SQLite wallet storage implementation explaining database schema, migrations, and usage patterns. Contains: setup instructions, schema documentation, migration procedures, integration examples. Used for: developer guidance on wallet storage integration and database management. |
| `diesel.toml` | [GENERATED] Diesel configuration file specifying database schema location, migration directory, and code generation settings. Contains: schema file path, migration directory configuration, generated code settings. Used by: diesel CLI for database migrations and schema management. |

###### crates/wallet/storage_sqlite/migrations/2023-02-08-122514_initial/

| File | Description |
|------|-------------|
| `down.sql` | [GENERATED] Database migration rollback script for initial wallet schema. Contains: SQL statements to drop initial database tables and indexes. Generated by: diesel migration system. Used for: rolling back initial database schema during development or recovery. |
| `up.sql` | [GENERATED] Database migration script creating initial wallet schema with tables for accounts, transactions, outputs, and configuration. Contains: CREATE TABLE statements for wallet data structures, indexes, constraints. Generated by: diesel migration system. Used for: setting up initial wallet database schema. |

###### crates/wallet/storage_sqlite/src/

| File | Description |
|------|-------------|
| `lib.rs` | SQLite-based storage implementation for Tari wallets providing persistent data management. Implements WalletStore trait with models, schema definitions, serialization, and read/write transactions using Diesel ORM. Includes database migrations and connection management for secure wallet data persistence and recovery. |
| `reader.rs` | SQLite storage reader implementation providing read-only database operations for wallet data access. Exports: WalletStoreReader trait implementation with query methods for all wallet data types. Dependencies: diesel, schema, models. Used by: wallet APIs for data retrieval operations. Read-optimized database access layer with transaction support. |
| `schema.rs` | [GENERATED] Diesel-generated database schema definitions for all wallet tables including primary keys, foreign keys, and indexes. Contains: table schema macros, column definitions, relationship mappings. Generated by: diesel CLI from migrations. Used by: all SQLite storage models and operations. Database schema representation for compile-time safety. |
| `serialization.rs` | Serialization utilities for converting between wallet SDK types and database-compatible formats. Exports: serialization/deserialization functions for complex types, binary data handling. Dependencies: serde, tari_bor, wallet SDK types. Used by: storage reader/writer for type conversion. Handles complex type serialization for database storage. |
| `writer.rs` | SQLite storage writer implementation providing write operations for wallet data persistence. Exports: WalletStoreWriter trait implementation with insert/update/delete methods for all wallet data types. Dependencies: diesel, schema, models, serialization. Used by: wallet APIs for data modification operations. Write-optimized database access layer with transaction support. |

###### crates/wallet/storage_sqlite/src/models/

| File | Description |
|------|-------------|
| `account.rs` | SQLite account model with Diesel ORM mapping for persistent account storage. Exports: Account struct with database field mappings, timestamps, serialization. Dependencies: diesel, chrono, wallet SDK account types. Used by: storage implementation for account persistence. Database representation of wallet accounts with ORM integration. |
| `authored_template.rs` | SQLite authored template model with Diesel ORM mapping for template storage. Exports: AuthoredTemplate struct with database fields, serialization, conversion methods. Dependencies: diesel, wallet SDK template types. Used by: storage implementation for template persistence. Database representation of user-authored templates with metadata. |
| `config.rs` | SQLite configuration model with Diesel ORM mapping for wallet settings storage. Exports: Config struct with key-value pairs, encryption flags, timestamps. Dependencies: diesel, chrono, wallet SDK config types. Used by: storage implementation for configuration persistence. Database representation of wallet configuration with encryption support. |
| `mod.rs` | SQLite storage models module exporting all database model structs with Diesel ORM mappings. Exports: Account, Config, ConfidentialOutput, Substate, Transaction, Vault, NonFungibleToken, AuthoredTemplate, Proof, WebAuthnRegistration models. Dependencies: individual model modules. Used by: storage implementation as unified model interface. Central module organizing all database model types. |
| `non_fungible_tokens.rs` | SQLite non-fungible token model with Diesel ORM mapping for NFT storage. Exports: NonFungibleToken struct with token metadata, ownership, database mappings. Dependencies: diesel, wallet SDK NFT types. Used by: storage implementation for NFT persistence. Database representation of NFTs with comprehensive metadata storage. |
| `output.rs` | SQLite confidential output model with Diesel ORM mapping for encrypted output storage. Exports: ConfidentialOutput struct with commitment data, encryption details, database fields. Dependencies: diesel, wallet SDK output types. Used by: storage implementation for confidential output persistence. Database representation of encrypted transaction outputs. |
| `proof.rs` | SQLite proof model with Diesel ORM mapping for cryptographic proof storage. Exports: Proof struct with proof data, verification parameters, database mappings. Dependencies: diesel, wallet SDK proof types. Used by: storage implementation for proof persistence. Database representation of cryptographic proofs with metadata. |
| `substate.rs` | SQLite substate model with Diesel ORM mapping for cached blockchain state storage. Exports: Substate struct with state data, versioning, synchronization metadata, database fields. Dependencies: diesel, wallet SDK substate types. Used by: storage implementation for substate caching. Database representation of cached blockchain substates. |
| `transaction.rs` | SQLite transaction model with Diesel ORM mapping for transaction history storage. Exports: Transaction struct with transaction data, status, execution details, database mappings. Dependencies: diesel, wallet SDK transaction types. Used by: storage implementation for transaction persistence. Database representation of wallet transaction history. |
| `vault.rs` | SQLite vault model with Diesel ORM mapping for resource container storage. Exports: Vault struct with vault metadata, balance information, database fields. Dependencies: diesel, wallet SDK vault types. Used by: storage implementation for vault persistence. Database representation of resource containers with balance tracking. |
| `webauthn_registrations.rs` | SQLite WebAuthn registration model with Diesel ORM mapping for authentication credential storage. Exports: WebAuthnRegistration struct with credential data, device information, database mappings. Dependencies: diesel, wallet SDK WebAuthn types. Used by: storage implementation for authentication persistence. Database representation of WebAuthn credentials. |

###### crates/wallet/storage_sqlite/tests/

| File | Description |
|------|-------------|
| `accounts.rs` | Integration tests for account storage operations validating database CRUD operations for wallet accounts. Contains test cases for account creation, retrieval, updates, and deletion with database constraints. Dependencies: storage implementation, test utilities. Used for: ensuring account storage correctness and database integrity. |
| `config.rs` | Integration tests for configuration storage operations validating encrypted and plain configuration data persistence. Contains test cases for config key-value operations, encryption handling, timestamps. Dependencies: storage implementation, encryption utilities. Used for: ensuring configuration storage security and functionality. |
| `key_manager_state.rs` | Integration tests for key manager state storage validating cryptographic key persistence and recovery. Contains test cases for key derivation state, seed storage, key manager initialization. Dependencies: storage implementation, key manager. Used for: ensuring key manager state persistence and cryptographic key security. |
| `substates.rs` | Integration tests for substate storage operations validating cached blockchain state persistence and synchronization. Contains test cases for substate caching, versioning, cache invalidation. Dependencies: storage implementation, substate types. Used for: ensuring substate caching correctness and synchronization integrity. |
| `transaction.rs` | Integration tests for transaction storage operations validating transaction history persistence and querying. Contains test cases for transaction storage, status updates, history retrieval. Dependencies: storage implementation, transaction types. Used for: ensuring transaction history storage correctness and query performance. |

### docker/

| File | Description |
|------|-------------|
| `README.md` | Docker build documentation providing instructions for containerizing Tari Ootle applications. Contains: build commands, directory structure setup, Docker image creation process. Used for: deployment and development environment setup. Standardized containerization approach for Tari Ootle components. |
| `cross_compile_tooling.sh` | Cross-compilation tooling script for building Tari Ootle across different target architectures. Contains: cross-compilation setup, toolchain configuration, build automation. Dependencies: cross-compilation tools, Rust toolchains. Used for: multi-platform binary generation and deployment automation. |
| `ootle.Dockerfile` | Dockerfile for building Tari Ootle applications in containerized environments with multi-stage build optimization. Contains: base image setup, dependency installation, build stages, runtime configuration. Used for: production deployment, development environments, CI/CD pipelines. Optimized container build for Tari Ootle applications. |

### docs/

| File | Description |
|------|-------------|
| `consensus_state_machine.md` | Documentation of consensus state machine architecture with Mermaid diagram showing state transitions between Idle, Sync, Ready, Leader, and voting phases. Contains: state machine flow, transition conditions, pacemaker integration. Used for: understanding consensus protocol implementation, validator node behavior. Visual representation of consensus algorithm state transitions. |
| `wallet_sequence_diagram.md` | Wallet sequence diagram documentation illustrating transaction flows and component interactions in wallet operations. Contains: sequence diagrams, interaction patterns, API call flows. Used for: understanding wallet architecture, transaction processing flows, integration patterns. Visual documentation of wallet component interactions. |

### integration_tests/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for integration tests providing end-to-end testing infrastructure for Tari Ootle components. Dependencies: test utilities, network components, validator nodes, base nodes, indexer. Build dependencies: build script support. Used for: comprehensive system testing, network simulation, component integration validation. |
| `build.rs` | Build script for integration tests handling test environment setup, resource compilation, and test infrastructure preparation. Contains: build-time configuration, resource copying, environment setup. Used by: Cargo build system for integration test preparation. Ensures proper test environment initialization. |

#### integration_tests/src/

| File | Description |
|------|-------------|
| `base_node.rs` | Base node process management for integration testing providing spawning, configuration, and management of base nodes in test environments. Exports: BaseNodeProcess, spawn_base_node(), get_base_node_client(). Dependencies: minotari_node, networking, configuration. Used by: integration tests for blockchain network simulation. Test infrastructure for base node operations. |
| `helpers.rs` | Integration test helper utilities for Tari system testing. Exports port assignment helpers (get_os_assigned_port), async service startup waiters (wait_listener_on_local_port), component lookup functions (get_address_from_output, get_component_from_namespace), and process monitoring (check_join_handle). Features TCP socket testing for service readiness, panic-safe process monitoring, and output reference resolution. Used by: integration tests for reliable test orchestration, service coordination, and result verification across distributed Tari components. |
| `http_server.rs` | HTTP server utilities for integration testing providing web server setup and management for test scenarios. Exports: HTTP server configuration, request handling, test endpoint setup. Dependencies: HTTP framework, async runtime. Used by: integration tests requiring HTTP interfaces. Test infrastructure for web-based component testing. |
| `indexer.rs` | Indexer process management for integration testing providing spawning, configuration, and client interfaces for indexer components. Exports: IndexerProcess, spawn_indexer(), indexer client management. Dependencies: tari_indexer, GraphQL/JSON-RPC clients. Used by: integration tests for blockchain indexing and querying. Test infrastructure for indexer operations. |
| `lib.rs` | Integration testing framework using Cucumber BDD for end-to-end blockchain testing. Provides test world management, process orchestration (validator nodes, indexers, wallets), network setup, and scenario execution. Comprehensive testing infrastructure for validating complete system behavior. |
| `logging.rs` | Logging configuration and utilities for integration tests providing structured logging setup and test scenario organization. Exports: logging initialization, test directory management, log formatting. Dependencies: logging framework, file system utilities. Used by: integration test infrastructure for debugging and monitoring. Centralized logging setup for test environments. |
| `miner.rs` | Miner process management for integration testing providing mining simulation and block production in test environments. Exports: Miner configuration, mining process control, block generation utilities. Dependencies: mining components, block production. Used by: integration tests requiring block production and mining simulation. Test infrastructure for consensus and mining operations. |
| `template.rs` | Integration test utilities for template management and deployment in Tari system. Exports template publishing functions (publish_template, send_template_registration), WASM compilation utilities (compile_wasm_template), and path helpers (get_template_wasm_path). Features wallet daemon integration for template deployment, template registration with Minotari base layer, mock server for template distribution, and comprehensive error handling. Dependencies: tari_engine, wallet_daemon_client, template compilation tools. Used by: integration tests for smart contract deployment and distribution testing. |
| `util.rs` | Utility functions for integration tests. Provides helper functions for test setup and common operations. Contains transaction_builder() function that creates a TransactionBuilder configured for LocalNet network. Used across integration tests to create properly configured transaction builders with network-specific settings. |
| `validator_node.rs` | Validator node integration test utilities providing process management, configuration, and client interaction. Handles validator node registration, Layer-1 transaction creation, keypair management, and network configuration for integration testing. Essential component enabling comprehensive end-to-end testing of validator node functionality in test environments. |
| `validator_node_cli.rs` | Integration test utilities for Tari Validator Node CLI operations. Exports account/component creation functions (create_account, create_component), transaction submission helpers (submit_manifest, call_method), key management (create_key), and substate tracking (add_substate_ids). Features manifest parsing with globals, concurrent transaction testing, output reference management, and comprehensive transaction validation. Dependencies: validator_node_cli, transaction_manifest, template_builtin. Used by: integration tests for end-to-end transaction flow testing and validator node interaction. |
| `wallet.rs` | Wallet process management for integration tests. Implements WalletProcess struct and functions for spawning and managing console wallet instances during cucumber tests. Handles wallet configuration, GRPC client setup, base node connections, and process lifecycle. Provides spawn_wallet() for creating wallet instances and run_wallet() for executing wallet runtime. Manages wallet connectivity, authentication, and inter-process communication for test scenarios. Used by integration test framework to create isolated wallet environments. |
| `wallet_daemon.rs` | Wallet daemon process management for integration tests. Implements TariWalletDaemonProcess struct and spawn_wallet_daemon() function for creating wallet daemon instances during cucumber tests. Handles wallet daemon configuration, JSON-RPC client setup, indexer connections, and process lifecycle management. Manages authentication flow and provides utilities for interacting with wallet daemon services. Used by integration test framework to create isolated wallet daemon environments for testing DAN wallet functionality. |
| `wallet_daemon_cli.rs` | Wallet daemon CLI integration functions for cucumber tests. Provides high-level functions for interacting with wallet daemon through JSON-RPC API. Implements operations for: burn claims, validator fee claims, account management, NFT operations, confidential transfers, transaction submission, and template publishing. Contains helper functions for authentication, transaction building, and response handling. Used by cucumber step definitions to perform wallet daemon operations during integration testing. |

###### integration_tests/src/templates/basic_nft/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the basic_nft template used in integration tests. This template demonstrates NFT (Non-Fungible Token) functionality in the Tari DAN. Configured to build as both a cdylib (for WebAssembly compilation) and library. Dependencies include tari_template_lib for template functionality and serde for serialization. Optimized for size with release profile settings suitable for WASM deployment. |

###### integration_tests/src/templates/basic_nft/src/

| File | Description |
|------|-------------|
| `lib.rs` | Basic NFT template implementation demonstrating non-fungible token operations in the Tari DAN. Implements SparkleNft component with functionality for: NFT creation/minting with metadata (name, image_url), token burning, mutable data updates (brightness), and collection management. The template creates NFTs with "SPKL" symbol and includes both immutable metadata and mutable Sparkle data structure. Used in integration tests to validate NFT template functionality. Exports: SparkleNft component with methods new(), mint(), burn(), inc_brightness(). |

###### integration_tests/src/templates/counter/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the counter template used in integration tests. This simple template demonstrates basic state management and method invocation in the Tari DAN. Configured to build as both cdylib and library for WebAssembly compatibility. Uses tari_template_lib for template framework functionality. Optimized for size with aggressive release profile settings. |

###### integration_tests/src/templates/counter/src/

| File | Description |
|------|-------------|
| `lib.rs` | Simple counter template implementation for Tari DAN integration tests. Provides a basic component that maintains a u32 counter value with increment functionality. Used to test template deployment, component instantiation, and method invocation in the DAN network. Exports Counter component with methods: new() (creates component with value 0), value() (returns current count), increase() (increments by 1). Implements allow_all access rules for testing purposes. |

###### integration_tests/src/templates/faucet/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the faucet template used in integration tests. This template implements a token faucet for distributing test tokens in the Tari DAN. Configured for WebAssembly compilation with size-optimized release settings. Dependencies include tari_template_lib for template framework functionality. |

###### integration_tests/src/templates/faucet/src/

| File | Description |
|------|-------------|
| `lib.rs` | Faucet template implementation for distributing test tokens in Tari DAN integration tests. Implements TestFaucet component that creates and manages a vault of fungible tokens with "FAUCET" symbol. Provides methods to distribute free tokens for testing purposes. Exports: TestFaucet component with methods mint() (creates faucet with initial supply), take_free_coins() (withdraws 1000 tokens), take_amount_of_free_coins() (withdraws specified amount). Uses allow_all access rules for testing. |

###### integration_tests/src/templates/fees/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for the fees template used in integration tests. This template is used to test fee-related functionality in the Tari DAN. Configured for WebAssembly compilation with tari_template_lib dependency for template framework support. Includes size-optimized release profile settings. |

###### integration_tests/src/templates/fees/src/

| File | Description |
|------|-------------|
| `lib.rs` | Minimal fees template implementation for testing fee-related functionality in the Tari DAN. Contains a basic FeesTest component with empty struct and simple constructor. This template appears to be a placeholder or minimal implementation used to test fee calculations and template deployment costs. Exports: FeesTest component with new() method. |

#### integration_tests/tests/

| File | Description |
|------|-------------|
| `cucumber.rs` | Cucumber BDD (Behavior-Driven Development) test runner for Tari Ootle integration tests. Implements step definitions for given/when/then scenarios, network process management, transaction validation, consensus testing, and comprehensive end-to-end test execution. Provides human-readable test specifications with automated validation of network behavior. |

##### integration_tests/tests/features/

| File | Description |
|------|-------------|
| `Indexer.feature` | Cucumber BDD feature file testing indexer functionality with wallet daemon integration. Tests complete workflow including base node setup, validator node registration, account creation, template publishing (counter, basic_nft), component creation, transaction processing, and data verification. Comprehensive integration test ensuring indexer properly captures and processes blockchain events. |
| `claim_burn.feature` | Cucumber feature file testing base layer burn and claim functionality. Tests the process of burning Tari coins on the base layer and claiming them on the DAN layer through the wallet daemon. Includes scenarios for: successful burn claim operations, double-spend prevention, base node and validator node coordination, and wallet daemon burn claim integration. Tests the full flow from base layer burn to DAN layer claim including commitment generation, proof validation, and fund recovery. |
| `claim_fees.feature` | Cucumber feature file for testing validator fee claiming functionality. Tests the process where validators can claim accumulated fees from processing transactions on the DAN. Includes scenarios for fee collection, validator registration, epoch management, and fee distribution. Tests integration between validator nodes, wallet daemon, and base layer for fee claim transactions. |
| `committee.feature` | Cucumber feature file testing committee management and consensus functionality in the DAN. Tests validator committee formation, consensus protocols, leader election, and committee management across epochs. Includes scenarios for: committee registration, consensus participation, epoch transitions, and validator committee interactions. Tests the core consensus and governance mechanisms of the DAN network. |
| `concurrency.feature` | Cucumber feature file testing concurrent transaction processing and parallel execution in the DAN. Tests the network's ability to handle multiple simultaneous transactions, component interactions, and resource contention. Includes scenarios for: concurrent template calls, parallel account operations, transaction ordering, and conflict resolution. Tests DAN's concurrency control and parallel processing capabilities. |
| `counter.feature` | Cucumber feature file testing the counter template functionality. Tests basic template deployment, component instantiation, and method invocation using the simple counter template. Includes scenarios for: template publishing, component creation, state management, and method calls. Tests fundamental DAN template operations including single and multiple increment operations. Used as a basic sanity test for DAN template functionality. |
| `epoch_change.feature` | Cucumber feature file testing epoch transitions and validator set changes in the DAN. Tests the process of epoch changes, validator committee rotation, and state transitions between epochs. Includes scenarios for: epoch boundary handling, validator registration across epochs, state persistence during transitions, and consensus continuity. Tests critical DAN network governance and validator management functionality. |
| `eviction.feature` | Cucumber feature file testing state eviction functionality in the DAN. Tests the network's ability to manage state storage by evicting old or unused state data. Includes scenarios for: state cleanup, storage management, substate eviction policies, and state recovery. Tests DAN's state management and storage optimization mechanisms. |
| `fungible.feature` | Cucumber feature file testing fungible token operations in the DAN. Tests creation, transfer, and management of fungible tokens through templates and components. Includes scenarios for: token creation, transfers between accounts, balance tracking, and token supply management. Tests fundamental DAN fungible token functionality including account-to-account transfers and balance verification. |
| `indexer.feature` | Cucumber feature file testing indexer functionality in the DAN ecosystem. Tests the indexer's ability to track and index blockchain state, transactions, and substate changes. Includes scenarios for: state indexing, query operations, blockchain scanning, and data synchronization. Tests integration between indexer and other DAN components including base nodes and validator nodes. |
| `leader_failure.feature.ignore` | Ignored cucumber feature file for testing leader failure scenarios in DAN consensus. Tests the network's resilience to leader node failures and consensus recovery mechanisms. Currently disabled (.ignore extension) indicating potential test instability or incomplete implementation. Would test: leader election, failure detection, consensus recovery, and network partition handling. |
| `nft.feature` | Cucumber feature file testing NFT (Non-Fungible Token) operations using the basic_nft template. Tests NFT creation, minting, transfer, metadata management, and burning operations. Includes scenarios for: NFT template deployment, token minting with metadata, mutable data updates, multi-account transfers, and transaction manifests. Tests comprehensive NFT functionality in the DAN including account interactions and state management. |
| `state_sync.feature` | Cucumber feature file testing state synchronization between DAN network nodes. Tests the ability of nodes to synchronize state data, catch up after being offline, and maintain consensus state consistency. Includes scenarios for: state replication, node resynchronization, consensus state management, and network partition recovery. Tests critical DAN network reliability and data consistency mechanisms. |
| `substates.feature` | Cucumber feature file testing substate management in the DAN. Tests creation, reading, updating, and deletion of substates which form the fundamental state units in the DAN. Includes scenarios for: substate lifecycle management, state transitions, conflict resolution, and state queries. Tests core DAN state management functionality and substate storage mechanisms. |
| `transfer.feature` | Cucumber feature file testing transfer operations between accounts in the DAN. Tests movement of fungible tokens, NFTs, and resources between different accounts using wallet daemon operations. Includes scenarios for: account-to-account transfers, balance verification, transfer validation, and multi-asset transfers. Tests fundamental DAN transfer functionality and account management. |
| `wallet_daemon.feature` | Cucumber feature file testing wallet daemon core functionality. Tests wallet daemon operations including account creation, authentication, balance queries, and transaction management. Includes scenarios for: wallet daemon startup, account management, balance tracking, and integration with indexer and validator nodes. Tests the primary wallet daemon interface and JSON-RPC API functionality. |

##### integration_tests/tests/log4rs/

| File | Description |
|------|-------------|
| `cucumber.yml` | Log4rs logging configuration for cucumber integration tests. Configures structured logging for different components: dan_layer (DAN-specific logs), network (P2P and communication logs), base_layer (base node logs), console_wallet (wallet logs), wallet_daemon (wallet daemon logs), and other (third-party crate logs). Uses rolling file appenders with size-based rotation. Separates concerns with component-specific log files for easier debugging during integration tests. |

##### integration_tests/tests/steps/

| File | Description |
|------|-------------|
| `base_node.rs` | Cucumber step definitions for base node operations in integration tests. Implements Given/When/Then steps for: spawning base nodes, checking mempool transaction counts, and verifying base node state. Provides helper functions for base node lifecycle management and state verification during cucumber test execution. Integrates with TariWorld test context for base node process management. |
| `common.rs` | Common cucumber step definitions shared across integration tests. Contains utility step implementations for: commitment conversion to addresses, common test operations, and shared test state management. Provides reusable step functions that are used by multiple feature files. Integrates with TariWorld test context for managing shared test state and operations. |
| `indexer.rs` | Cucumber step definitions for indexer operations in integration tests. Implements Given/When/Then steps for: indexer spawning, validator connections, block height tracking, state verification, and substate queries. Provides helper functions for indexer lifecycle management, peer connections, and state synchronization verification during cucumber test execution. Integrates with TariWorld test context for indexer process management and state tracking. |
| `miner.rs` | Cucumber step definitions for miner operations in integration tests. Implements Given/When/Then steps for: miner spawning, block mining, difficulty management, and mining verification. Provides helper functions for miner lifecycle management and block production during cucumber test execution. Integrates with TariWorld test context for miner process management and blockchain state progression. |
| `mod.rs` | Module declaration file for integration test step definitions. Organizes and exports all cucumber step definition modules including: base_node, common, indexer, miner, network, validator_node, wallet, and wallet_daemon. Provides centralized access to all step implementations used by cucumber feature files during integration testing. |
| `network.rs` | Cucumber step definitions for network operations in integration tests. Implements Given/When/Then steps for: network connectivity, peer management, protocol testing, and network state verification. Provides helper functions for network topology setup and communication testing during cucumber test execution. Integrates with TariWorld test context for network configuration and peer relationship management. |
| `validator_node.rs` | Cucumber step definitions for validator node operations in integration tests. Implements Given/When/Then steps for: validator node spawning, registration, consensus participation, epoch management, and state verification. Provides helper functions for validator lifecycle management, consensus testing, and DAN network operations during cucumber test execution. Integrates with TariWorld test context for validator process management and consensus state tracking. |
| `wallet.rs` | Cucumber step definitions for wallet operations in integration tests. Implements Given/When/Then steps for: wallet spawning, balance checking, transaction creation, burn operations, and wallet state verification. Provides helper functions for wallet lifecycle management and transaction testing during cucumber test execution. Integrates with TariWorld test context for wallet process management and balance tracking. |
| `wallet_daemon.rs` | Cucumber step definitions for wallet daemon operations in integration tests. Implements Given/When/Then steps for: wallet daemon spawning, account management, template operations, transaction submission, NFT operations, and balance verification. Provides comprehensive helper functions for wallet daemon JSON-RPC API testing during cucumber test execution. Integrates with TariWorld test context for wallet daemon process management and DAN operation testing. |

### meta/

| File | Description |
|------|-------------|
| `hashes.txt` | SHA-256 checksums for Tari binary releases across different platforms. Contains hash values for base node and console wallet executables for macOS (.pkg), Linux (.zip), and Windows (.exe) platforms. Used for integrity verification of downloaded release binaries to ensure they haven't been tampered with. Organized by component type (base node, console wallet) and platform. |
| `hashes.txt.bad.sig` | Invalid or corrupted GPG signature file for hashes.txt. This appears to be a test file containing a deliberately bad signature, likely used for testing signature verification functionality or demonstrating what happens with invalid signatures. Part of the security and verification testing infrastructure. |
| `hashes.txt.sig` | GPG signature file for hashes.txt. Contains the cryptographic signature that verifies the authenticity and integrity of the hashes.txt file. Used to ensure that the hash checksums for release binaries haven't been tampered with. Part of the security verification chain for validating Tari release packages. |

#### meta/assets/

| File | Description |
|------|-------------|
| `rustdoc-include-js-header.html` | HTML header fragment for including JavaScript resources in generated Rust documentation. Contains script tags and JavaScript code to enhance rustdoc-generated documentation with additional functionality. Used during documentation build process to inject custom JavaScript behavior into API documentation pages. |
| `rustdoc-include-katex-header.html` | HTML header fragment for including KaTeX mathematical rendering support in generated Rust documentation. Contains KaTeX CSS and JavaScript includes to enable mathematical formula rendering in rustdoc-generated documentation. Used during documentation build process to support mathematical notation in API documentation and code comments. |

#### meta/gpg_keys/

| File | Description |
|------|-------------|
| `CjS77.asc` | GPG public key for developer CjS77 (Cayle Sharrock). ASCII-armored PGP public key block used for verifying commits and signed releases from this developer. Part of the Tari project's code signing and verification infrastructure to ensure commit authenticity and maintain code integrity. |
| `README.md` | Documentation for Tari developer GPG public keys management. Explains the purpose of GPG keys for code signing, provides instructions for creating, importing, and using GPG keys for commit signing. Includes guidance on key generation, keyring management, commit signing setup, and procedures for submitting public keys to the project. Used to maintain developer identity verification and code integrity through signed commits. |
| `delta1.asc` | GPG public key for developer delta1. ASCII-armored PGP public key block used for verifying commits and signed releases from this developer. Part of the Tari project's code signing and verification infrastructure to ensure commit authenticity and maintain code integrity. |
| `hansieodendaal.asc` | GPG public key for developer hansieodendaal. ASCII-armored PGP public key block used for verifying commits and signed releases from this developer. Part of the Tari project's code signing and verification infrastructure to ensure commit authenticity and maintain code integrity. |
| `neonknight64.asc` | GPG public key for developer neonknight64. ASCII-armored PGP public key block used for verifying commits and signed releases from this developer. Part of the Tari project's code signing and verification infrastructure to ensure commit authenticity and maintain code integrity. |
| `philipr-za.asc` | GPG public key for developer philipr-za. ASCII-armored PGP public key block used for verifying commits and signed releases from this developer. Part of the Tari project's code signing and verification infrastructure to ensure commit authenticity and maintain code integrity. |
| `sdbondi.asc` | GPG public key for developer sdbondi. ASCII-armored PGP public key block used for verifying commits and signed releases from this developer. Part of the Tari project's code signing and verification infrastructure to ensure commit authenticity and maintain code integrity. |
| `swvheerden.asc` | GPG public key for developer swvheerden. ASCII-armored PGP public key block used for verifying commits and signed releases from this developer. Part of the Tari project's code signing and verification infrastructure to ensure commit authenticity and maintain code integrity. |
| `tari-bot.asc` | GPG public key for tari-bot automation account. ASCII-armored PGP public key block used for verifying automated commits and releases from the Tari project's continuous integration and release automation systems. Part of the infrastructure for maintaining automated code signing and verification. |

#### networking/core/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for tari_networking core crate. Provides core networking functionality built on libp2p for Tari DAN communication. Dependencies include libp2p with extensive features (tokio, noise, ping, tcp, identify, yamux, relay, quic, gossipsub), tari_swarm for swarm management, and tari_rpc_framework for RPC communication. Optional metrics feature adds Prometheus monitoring support. Enables P2P networking, relay functionality, and gossip protocols for DAN node communication. |

##### networking/core/src/

| File | Description |
|------|-------------|
| `builder.rs` | Builder pattern implementation for constructing networking components in tari_networking. Provides Builder struct for configuring and spawning NetworkingWorker with customizable messaging modes, peer configurations, rendezvous servers, and metrics. Exports: Builder struct with methods for configuration chaining (with_messaging_mode, with_config, with_seed_peers, with_rendezvous_server, spawn). Handles identity management, P2P configuration, and worker lifecycle setup for DAN networking. |
| `config.rs` | Configuration structures for tari_networking core. Defines Config struct with swarm configuration, listener addresses, reachability modes, connection settings, and rendezvous service parameters. Includes ReachabilityMode enum for controlling network reachability (Auto/Private). Provides default configurations with TCP and QUIC listeners. Used to configure P2P networking behavior, peer discovery, and connection management for DAN nodes. |
| `connection.rs` | Connection management functionality for tari_networking core. Handles P2P connection lifecycle, connection state tracking, and connection quality management. Provides utilities for managing libp2p connections, handling connection events, and maintaining connection health. Used by networking worker to manage peer connections and network topology in the DAN. |
| `error.rs` | Error types and handling for tari_networking core. Defines networking-specific error types using thiserror for structured error handling. Includes errors for connection failures, configuration issues, protocol errors, and swarm management problems. Provides centralized error handling for all networking operations in the DAN networking stack. |
| `event.rs` | Event system for tari_networking core. Defines networking events and event handling for P2P operations including connection events, peer discovery events, protocol events, and message events. Provides event structures for network state changes, peer lifecycle events, and protocol-specific notifications. Used by networking components to communicate state changes and trigger appropriate responses. |
| `global_ip.rs` | Global IP address detection and management for tari_networking core. Handles discovery and validation of public IP addresses for P2P networking. Provides functionality for determining external IP addresses, handling NAT traversal scenarios, and managing address announcements. Used to establish proper peer connectivity and address advertisement in P2P networks. |
| `handle.rs` | Handle abstraction for tari_networking core operations. Provides NetworkingHandle struct for interacting with the networking worker from external components. Exports: NetworkingHandle with methods for sending messages, managing peers, and controlling network behavior. Enables async communication with the networking subsystem through channel-based message passing. Used by DAN components to perform networking operations. |
| `lib.rs` | Core networking abstractions for peer-to-peer communication in Tari Ootle. Provides networking builder, configuration, connection management, message handling, and libp2p integration. Foundation layer for validator node P2P networking with relay and rendezvous support. |
| `message.rs` | Message definitions and handling for tari_networking core. Defines MessageSpec trait and message structures for P2P communication. Includes message types for consensus, transactions, and general networking protocols. Provides message serialization, routing, and handling infrastructure. Used to define the communication protocols between DAN nodes and coordinate distributed operations. |
| `message_mode.rs` | Message mode configuration for tari_networking core. Defines different messaging modes and their behaviors for P2P communication. Includes configuration for message handling patterns, routing strategies, and protocol selection. Provides MessagingMode enum and related types for configuring how messages are processed and routed through the network. Used to customize networking behavior for different DAN components. |
| `notify.rs` | Notification system for tari_networking core. Provides notification mechanisms for networking events and state changes. Handles async notification delivery to interested parties and event subscription management. Used to propagate networking events to other system components and coordinate responses to network state changes. |
| `peer.rs` | Peer management functionality for tari_networking core. Defines peer structures, peer state tracking, and peer lifecycle management. Handles peer discovery, connection establishment, and peer quality assessment. Provides utilities for managing peer relationships, peer scoring, and peer selection for network operations in the DAN. |
| `relay_state.rs` | Relay state management for tari_networking core. Handles state tracking for libp2p relay functionality including relay connections, relay reservations, and relay circuit management. Manages relay server and client state for NAT traversal and connectivity improvement. Used to enable connectivity for nodes behind NATs and firewalls in the DAN network. |
| `rendezvous_state.rs` | Rendezvous state management for tari_networking core. Handles state tracking for libp2p rendezvous protocol including peer registration, discovery, and rendezvous server interactions. Manages client and server state for peer discovery through rendezvous points. Used to enable peer discovery and bootstrap connectivity in the DAN network topology. |
| `worker.rs` | Main networking worker implementation for tari_networking core. Implements NetworkingWorker that orchestrates all networking operations including swarm management, message routing, peer management, and protocol handling. Exports: NetworkingWorker struct with core networking loop and event processing. Coordinates between different networking components and provides the main networking runtime for DAN nodes. |

#### networking/libp2p-messaging/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for libp2p-messaging crate. Provides messaging protocol implementation for libp2p networks. Dependencies include libp2p for networking, async-trait for async trait support, and optional prost for Protocol Buffer serialization. Features include optional prost codec support and Prometheus metrics integration. Used to implement custom messaging protocols on top of libp2p for structured communication between DAN nodes. |

##### networking/libp2p-messaging/src/

| File | Description |
|------|-------------|
| `behaviour.rs` | libp2p messaging network behavior implementing peer-to-peer message exchange protocols. Manages connection handling, message routing, stream management, and protocol negotiation. Core component for validator node P2P communication with connection lifecycle management and message queuing. |
| `config.rs` | Configuration structures for libp2p-messaging. Defines configuration options for messaging protocols including timeouts, buffer sizes, connection limits, and protocol-specific settings. Provides default configurations and validation for messaging protocol parameters. Used to configure messaging behavior and performance characteristics in P2P communication channels. |
| `error.rs` | Error types and handling for libp2p-messaging. Defines messaging-specific error types using thiserror for structured error handling. Includes errors for codec failures, protocol violations, connection issues, and serialization problems. Provides centralized error handling for messaging protocol operations and stream management. |
| `event.rs` | Event system for libp2p-messaging. Defines messaging protocol events including message received events, connection events, and protocol state changes. Provides event structures for stream lifecycle, protocol negotiations, and error conditions. Used to communicate messaging protocol state and trigger appropriate responses in P2P communication layers. |
| `handler.rs` | libp2p connection handler for message streams and protocol upgrades. Manages inbound/outbound stream negotiation, message codec handling, and connection state management. Essential component for establishing and maintaining P2P communication channels between validator nodes. |
| `lib.rs` | libp2p messaging behavior implementation for peer-to-peer communication. Provides message codecs, configuration, event handling, stream management, and metrics for P2P messaging protocol. Core component enabling validator nodes to exchange messages over libp2p networks. |
| `message.rs` | Message definitions and utilities for libp2p-messaging. Defines message structures, message traits, and message handling utilities for P2P communication. Provides message routing, validation, and processing infrastructure. Used to define the structure and behavior of messages exchanged between DAN nodes through libp2p protocols. |
| `metrics.rs` | Metrics collection for libp2p-messaging. Provides Prometheus metrics for messaging protocol performance including message counts, latency measurements, error rates, and connection statistics. Exports metrics for monitoring messaging protocol health and performance in production deployments. Optional feature that enhances observability of P2P communication. |
| `stream.rs` | Stream management for libp2p-messaging. Handles libp2p stream lifecycle, stream multiplexing, and stream-based message exchange. Provides utilities for managing bidirectional communication channels and stream state. Used to implement reliable message exchange over libp2p connections with proper flow control and error handling. |

###### networking/libp2p-messaging/src/codec/

| File | Description |
|------|-------------|
| `mod.rs` | Codec module for libp2p-messaging. Organizes message encoding and decoding functionality for different serialization formats. Provides module organization for various codec implementations including Protocol Buffer support. Used to abstract message serialization and provide pluggable codec support for different message formats in P2P communication. |
| `prost.rs` | Protocol Buffer codec implementation for libp2p-messaging. Provides ProstCodec for encoding and decoding messages using Protocol Buffers through the prost library. Implements libp2p stream codec traits for efficient binary message serialization. Used to enable high-performance, compact message serialization for DAN node communication protocols. |

#### networking/libp2p-peersync/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for libp2p-peersync crate. Implements peer synchronization protocol for libp2p networks enabling efficient peer discovery and state synchronization. Dependencies include libp2p for networking and protocol support. Includes build script for Protocol Buffer compilation. Used to maintain peer state consistency and enable distributed peer discovery in P2P networks. |
| `build.rs` | Build script for libp2p-peersync. Handles Protocol Buffer compilation and code generation for peer synchronization messages. Configures protobuf compilation and generates Rust code from .proto definitions. Used during build process to create message types and serialization code for peer synchronization protocols. |

##### networking/libp2p-peersync/proto/

| File | Description |
|------|-------------|
| `messages.proto` | Protocol buffer definition for libp2p peer synchronization messages. Defines peer record exchange protocols including SignedPeerRecord with addresses and timestamps, WantPeers requests, and PeerSignature verification. Core protocol enabling secure peer discovery and record sharing in the distributed Tari validator network. |

##### networking/libp2p-peersync/src/

| File | Description |
|------|-------------|
| `behaviour.rs` | libp2p network behavior implementation for peer synchronization. Implements NetworkBehaviour trait to provide peer sync functionality including peer discovery, state synchronization, and protocol coordination. Handles peer communication patterns and integrates with libp2p's behavior system. Core component that enables distributed peer state management across the network. |
| `config.rs` | Configuration structures for libp2p-peersync. Defines peer synchronization protocol configuration including sync intervals, retry policies, connection limits, and protocol parameters. Provides default configurations and validation for peer sync behavior. Used to tune peer synchronization performance and reliability characteristics. |
| `epoch_time.rs` | Epoch time management for peer synchronization. Handles time-based coordination and synchronization across distributed peers. Provides utilities for managing time epochs, time-based state transitions, and temporal consistency in peer synchronization protocols. Used to coordinate time-sensitive operations and maintain temporal ordering in distributed peer state. |
| `error.rs` | Error types for libp2p-peersync. Defines peer synchronization specific error types using thiserror for structured error handling. Includes errors for sync failures, protocol violations, timeout conditions, and state inconsistencies. Provides centralized error handling for peer synchronization operations and protocol management. |
| `event.rs` | Event system for libp2p-peersync. Defines peer synchronization events including sync completion, peer discovery events, state change notifications, and protocol failures. Provides event structures for coordinating peer sync operations and communicating sync status to higher layers. Used to track synchronization progress and trigger appropriate responses to sync events. |
| `handler.rs` | Connection handler for libp2p-peersync protocol. Implements ConnectionHandler trait to manage peer sync protocol lifecycle on individual connections. Handles protocol negotiation, message exchange, and connection-specific sync state. Coordinates between inbound and outbound sync tasks and manages per-connection protocol state. |
| `inbound_task.rs` | Inbound task management for peer synchronization. Handles incoming peer sync requests and coordinates inbound sync operations. Manages peer state reception, validation, and integration into local peer store. Processes sync requests from remote peers and ensures proper state consistency during inbound synchronization. |
| `lib.rs` | libp2p peer synchronization protocol for validator node peer discovery and record sharing. Implements signed peer record exchange, gossipsub integration, want-list management, and epoch-aware peer synchronization. Critical protocol enabling efficient peer discovery and maintaining up-to-date validator network topology in the distributed Tari consensus system. |
| `metrics.rs` | Metrics collection for libp2p-peersync. Provides Prometheus metrics for peer synchronization performance including sync success rates, latency measurements, peer counts, and error statistics. Exports metrics for monitoring peer sync health and network topology changes. Used for observability and performance tuning of peer discovery and synchronization. |
| `outbound_task.rs` | Outbound task management for peer synchronization. Handles outgoing peer sync requests and coordinates outbound sync operations. Manages local peer state transmission, request scheduling, and sync completion tracking. Initiates sync operations with remote peers and ensures efficient state distribution across the network. |
| `peer_record.rs` | Peer record definitions and management for peer synchronization. Defines peer record structures, peer metadata, and peer state information. Provides utilities for peer record serialization, validation, and lifecycle management. Used to represent peer information that is synchronized across the network for discovery and connectivity. |
| `proto.rs` | Protocol Buffer definitions and generated code for libp2p-peersync. Contains generated Rust code from .proto files defining peer sync message formats and protocol structures. Includes message types for peer records, sync requests, and protocol coordination. [GENERATED] file created from Protocol Buffer definitions during build process. |
| `store.rs` | Peer store implementation for peer synchronization. Manages local storage of peer records, peer state persistence, and peer information queries. Provides efficient storage and retrieval of peer data with support for filtering, searching, and state updates. Core component for maintaining peer database and enabling peer discovery operations. |

#### networking/libp2p-substream/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for libp2p-substream crate. Implements substream management and multiplexing for libp2p networks. Provides utilities for managing bidirectional streams, stream lifecycle, and stream-based communication patterns. Dependencies include libp2p for networking infrastructure. Used to enable fine-grained stream control and efficient multiplexed communication channels. |

##### networking/libp2p-substream/src/

| File | Description |
|------|-------------|
| `behaviour.rs` | libp2p network behavior implementation for substream management. Implements NetworkBehaviour trait to provide substream multiplexing and management functionality. Handles stream creation, lifecycle management, and coordination between multiple concurrent streams. Enables efficient bidirectional communication channels over libp2p connections. |
| `config.rs` | Configuration for libp2p-substream management. Defines substream configuration including timeout settings, buffer sizes, concurrency limits, and stream lifecycle parameters. Provides default configurations for efficient stream multiplexing and resource management. Used to tune substream performance and control resource usage in P2P communication channels. |
| `error.rs` | Error types for libp2p-substream management. Defines substream-specific error types using thiserror for structured error handling. Includes errors for stream creation failures, protocol violations, timeout conditions, and resource exhaustion. Provides centralized error handling for substream operations and multiplexing management. |
| `event.rs` | Event system for libp2p-substream management. Defines substream events including stream creation, completion, failure, and state change notifications. Provides event structures for coordinating stream operations and communicating stream status to higher protocol layers. Used to track stream lifecycle and trigger appropriate responses to stream events. |
| `handler.rs` | Connection handler for libp2p-substream protocol. Implements ConnectionHandler trait to manage substream protocol lifecycle on individual connections. Handles stream creation, multiplexing, and per-connection stream state management. Coordinates between multiple concurrent streams and manages connection-specific resources and limits. |
| `lib.rs` | libp2p substream protocol implementation for Tari network communication. Provides behavior management, configuration, error handling, event processing, stream handling, notification systems, and optional metrics. Enables efficient substream multiplexing and protocol handling for complex P2P communication patterns in the Tari ecosystem. |
| `metrics.rs` | Metrics collection for libp2p-substream management. Provides Prometheus metrics for substream performance including active stream counts, creation rates, failure rates, and resource usage statistics. Exports metrics for monitoring stream multiplexing efficiency and resource utilization. Used for observability and performance optimization of stream-based communication. |
| `notify.rs` | Notification system for libp2p-substream events. Provides notification mechanisms for substream state changes and lifecycle events. Handles async notification delivery and event subscription management for stream operations. Used to coordinate between different components that need to respond to stream events and state changes. |
| `stream.rs` | Core stream implementation for libp2p-substream management. Provides stream abstractions, stream state management, and stream I/O operations. Implements bidirectional stream functionality with proper flow control and error handling. Core component that enables efficient multiplexed communication channels over libp2p connections. |

#### networking/proto_builder/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for proto_builder utility crate. Provides build utilities and helper functions for Protocol Buffer compilation across networking crates. Contains shared build logic for generating Rust code from .proto files. Used by other networking crates' build scripts to standardize Protocol Buffer compilation and code generation processes. |

##### networking/proto_builder/src/

| File | Description |
|------|-------------|
| `lib.rs` | Protocol buffer build utilities for Tari networking components. Provides build-time code generation for protobuf definitions, rustfmt integration, file system utilities, and hash-based caching. Essential build infrastructure for generating type-safe gRPC and protocol buffer bindings used throughout the Tari networking stack. |

#### networking/rpc_framework/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for tari RPC framework. Provides high-level RPC abstraction built on libp2p for structured request-response communication. Dependencies include libp2p, tokio for async runtime, and Protocol Buffer support. Includes build script for protobuf compilation. Used to implement type-safe RPC communication between DAN components with automatic serialization and routing. |
| `build.rs` | Build script for tari RPC framework. Handles Protocol Buffer compilation and code generation for RPC message definitions. Configures protobuf compilation and generates Rust types for RPC requests, responses, and service definitions. Used during build process to create type-safe RPC interfaces from .proto files. |

##### networking/rpc_framework/proto/

| File | Description |
|------|-------------|
| `rpc.proto` | Protocol buffer definition for Tari RPC framework communication. Defines RpcRequest, RpcResponse, RpcSession, and RpcSessionReply messages with request/response identification, method routing, status codes, payload handling, and session negotiation. Foundation protocol specification enabling type-safe RPC communication across the Tari networking stack. |

##### networking/rpc_framework/src/

| File | Description |
|------|-------------|
| `body.rs` | RPC message body handling for tari RPC framework. Provides utilities for RPC request and response body serialization, deserialization, and validation. Handles different content types and message formats with efficient streaming support. Used to manage RPC message payloads and ensure proper data encoding/decoding in client-server communication. |
| `bounded_executor.rs` | Bounded executor implementation for RPC framework. Provides bounded task execution with concurrency limits to prevent resource exhaustion. Manages async task scheduling with backpressure and resource controls. Used to ensure RPC server stability under high load by limiting concurrent request processing and managing resource usage. |
| `either.rs` | Either type utilities for RPC framework. Provides Either enum and related utilities for handling alternative types and branching logic in RPC operations. Used for error handling, response variants, and conditional RPC processing where operations can result in different outcome types. |
| `error.rs` | Error types and handling for tari RPC framework. Defines RPC-specific error types using thiserror for structured error handling. Includes errors for connection failures, protocol violations, serialization issues, and service errors. Provides centralized error handling for all RPC framework operations with proper error propagation and context. |
| `event.rs` | Event system for RPC framework operations. Defines RPC events including connection events, request/response events, and service lifecycle notifications. Provides event structures for coordinating RPC operations and communicating state changes to interested components. Used to enable reactive programming patterns and event-driven RPC processing. |
| `framing.rs` | Message framing implementation for RPC framework. Handles message boundary detection, frame encoding/decoding, and stream parsing for RPC communication. Provides reliable message framing over stream-based transports with length prefixing and integrity checking. Used to ensure proper message boundaries and enable reliable RPC message exchange over network streams. |
| `handshake.rs` | RPC connection handshake implementation. Handles initial connection negotiation, protocol version agreement, and capability exchange between RPC client and server. Manages authentication and connection establishment procedures. Used to ensure compatible RPC communication and establish secure, authenticated connections for service calls. |
| `lib.rs` | RPC protocol framework providing request/response messaging with streaming support. Defines frame size limits, bidirectional communication patterns, and protocol abstractions. Used by validator nodes for internal RPC communication and service coordination. |
| `message.rs` | RPC message definitions and utilities. Defines RPC message structures, request/response types, and message validation logic. Provides message routing, serialization, and processing infrastructure for RPC communication. Used to define the structure and behavior of RPC messages exchanged between clients and servers in the DAN. |
| `not_found.rs` | Not found error handling for RPC framework. Provides specialized error handling for missing services, unknown methods, and unavailable endpoints. Implements standard RPC error responses for resource not found scenarios. Used to provide consistent error responses when requested RPC services or methods are not available or recognized. |
| `notify.rs` | Notification system for RPC framework events. Provides notification mechanisms for RPC state changes, connection events, and service lifecycle notifications. Handles async notification delivery and event subscription management. Used to coordinate between different RPC components and enable reactive responses to RPC system events. |
| `optional.rs` | Optional value utilities for RPC framework. Provides utilities for handling optional parameters, nullable fields, and missing values in RPC messages. Includes serialization support for optional types and validation logic. Used to manage optional data in RPC requests and responses with proper null handling and type safety. |
| `proto.rs` | Protocol Buffer definitions and generated code for RPC framework. Contains generated Rust code from .proto files defining RPC service interfaces and message formats. Includes service definitions, request/response types, and RPC method signatures. [GENERATED] file created from Protocol Buffer definitions during build process. |
| `status.rs` | RPC status codes and status handling for tari RPC framework. Defines standard RPC status codes, status messages, and status propagation utilities. Provides consistent status reporting for RPC operations including success, error, and intermediate states. Used to standardize RPC response status and enable proper error categorization across services. |

###### networking/rpc_framework/src/client/

| File | Description |
|------|-------------|
| `metrics.rs` | Client metrics collection for tari RPC framework. Provides Prometheus metrics for RPC client performance including request latency, success rates, error counts, and connection statistics. Exports metrics for monitoring RPC client health and performance characteristics. Used for observability and performance tuning of RPC client operations. |
| `mod.rs` | RPC client module for tari RPC framework. Implements RPC client functionality including connection management, request routing, and response handling. Provides high-level client API for making RPC calls with automatic serialization and error handling. Exports client types and utilities for building type-safe RPC clients. |
| `pool.rs` | Connection pool management for RPC clients. Implements connection pooling to enable efficient reuse of RPC connections and manage connection lifecycle. Provides connection pooling with health checks, load balancing, and resource management. Used to optimize RPC client performance and resource utilization through connection sharing and management. |
| `tests.rs` | Unit tests for RPC client functionality. Contains test cases for RPC client operations including connection management, request/response handling, error scenarios, and connection pooling. Provides comprehensive test coverage for client-side RPC functionality and edge cases. Used to ensure RPC client reliability and correctness. |

###### networking/rpc_framework/src/server/

| File | Description |
|------|-------------|
| `early_close.rs` | Early connection close handling for RPC server. Manages premature connection termination scenarios and cleanup procedures for interrupted RPC sessions. Provides graceful handling of client disconnections and resource cleanup. Used to ensure proper resource management when RPC connections are closed unexpectedly or during request processing. |
| `error.rs` | Server-specific error types for RPC framework. Defines RPC server error types including service errors, handler failures, and server-side protocol violations. Provides structured error handling for server-side RPC operations with proper error categorization and response generation. Used to manage and report RPC service errors consistently. |
| `handle.rs` | RPC server handle implementation. Provides server control interface for managing RPC server lifecycle, service registration, and server configuration. Exports server handle types for external server management and control operations. Used to control RPC server behavior and manage running server instances from application code. |
| `metrics.rs` | Server metrics collection for tari RPC framework. Provides Prometheus metrics for RPC server performance including request counts, response times, error rates, and active connection statistics. Exports metrics for monitoring RPC server health and performance characteristics. Used for observability and performance tuning of RPC server operations and service endpoints. |
| `mock.rs` | Mock server utilities for RPC framework testing. Provides mock RPC server implementations for unit testing and integration testing. Includes test doubles, stub services, and mock response generation. Used to enable isolated testing of RPC clients and service integration without requiring full server deployment. |
| `mod.rs` | RPC server module for tari RPC framework. Implements RPC server functionality including service registration, request routing, and response handling. Provides high-level server API for hosting RPC services with automatic deserialization and error handling. Exports server types and utilities for building type-safe RPC servers. |
| `router.rs` | Request routing implementation for RPC server. Handles RPC request routing to appropriate service handlers based on method names and service paths. Provides efficient request dispatching with middleware support and error handling. Used to route incoming RPC requests to the correct service implementations and manage request processing pipeline. |

###### networking/rpc_framework/src/test/

| File | Description |
|------|-------------|
| `client_pool.rs` | Client pool tests for RPC framework. Contains integration tests for RPC client connection pooling functionality including pool management, connection reuse, and load balancing. Tests connection pool behavior under various conditions and validates pool performance characteristics. Used to ensure RPC client pool reliability and efficiency. |
| `greeting_service.rs` | Greeting service test implementation for RPC framework. Provides a simple test service used for RPC framework testing and validation. Implements basic service methods for testing RPC communication patterns, serialization, and error handling. Used as a reference implementation for RPC service development and framework testing. |
| `handshake.rs` | RPC handshake tests for connection negotiation. Contains test cases for RPC handshake procedures including protocol negotiation, version compatibility, and authentication flows. Tests various handshake scenarios and error conditions. Used to ensure reliable RPC connection establishment and protocol compatibility. |
| `mock.rs` | Mock framework tests for RPC testing utilities. Contains test cases for mock RPC services, test doubles, and testing infrastructure. Validates mock service behavior and testing utility functionality. Used to ensure comprehensive testing capabilities for RPC framework development and service validation. |
| `mod.rs` | Test module organization for RPC framework. Organizes and exports test utilities, mock services, and integration tests for RPC framework validation. Provides centralized access to testing infrastructure and test helper functions. Used to structure RPC framework testing and provide reusable testing components. |
| `smoke.rs` | Smoke tests for RPC framework functionality. Contains basic end-to-end tests that validate core RPC framework operations including client-server communication, service invocation, and error handling. Provides quick validation of framework health and basic functionality. Used as initial validation for RPC framework changes and deployments. |

#### networking/rpc_macros/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for RPC procedural macros crate. Provides procedural macros for code generation in the tari RPC framework. Dependencies include syn, quote, and proc-macro2 for macro development. Used to generate RPC service traits, client implementations, and server boilerplate code from service definitions. |

##### networking/rpc_macros/src/

| File | Description |
|------|-------------|
| `expand.rs` | Macro expansion logic for RPC procedural macros. Implements the core macro expansion functionality that generates RPC service code from attributes and traits. Handles code generation for service implementations, client stubs, and server routing. Used to transform high-level service definitions into concrete RPC implementation code. |
| `generator.rs` | Code generation utilities for RPC macros. Provides code generation helpers for creating RPC service implementations, client code, and server routing logic. Includes template-based code generation and syntax tree manipulation utilities. Used to generate type-safe RPC code with proper error handling and serialization support. |
| `lib.rs` | Procedural macros for Tari RPC framework code generation. Provides #[tari_rpc] macro for generating RPC server and client code from trait definitions with method routing, protocol handling, and type safety. Essential macro system enabling declarative RPC service development with automatic harness code generation for the Tari networking stack. |
| `macros.rs` | Macro definitions for RPC code generation. Defines procedural macros for RPC service traits, client generation, and server implementation. Provides derive macros and attribute macros for automating RPC code generation. Used to enable declarative RPC service definition with automatic implementation generation. |
| `method_info.rs` | Method information extraction for RPC macro generation. Provides utilities for analyzing RPC service method signatures, extracting parameter information, and building method metadata. Used by macro expansion to understand service method structure and generate appropriate client/server code with proper type safety. |
| `options.rs` | Configuration options for RPC macro generation. Defines macro configuration options, generation preferences, and customization settings for RPC code generation. Provides flexibility in controlling generated code behavior and output characteristics. Used to customize RPC macro expansion based on specific requirements. |

##### networking/rpc_macros/tests/

| File | Description |
|------|-------------|
| `macro.rs` | Integration tests for RPC procedural macros. Contains test cases validating macro expansion behavior, generated code correctness, and macro functionality. Tests various macro usage patterns and edge cases. Used to ensure RPC macro reliability and correctness of generated code. |

#### networking/sqlite_message_logger/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for SQLite message logging crate. Provides SQLite-based logging for network messages and communication debugging. Dependencies include diesel for database ORM and SQLite support. Used for debugging and analyzing network message flows in development and testing environments. |
| `README.md` | Documentation for SQLite message logger. Explains usage, configuration, and setup for SQLite-based network message logging. Provides guidance on database schema, query patterns, and debugging workflows. Used to understand and configure message logging for network debugging and analysis. |
| `diesel.toml` | Diesel ORM configuration for SQLite message logger. Configures database connection settings, migration paths, and schema generation for message logging database. Used by diesel CLI and ORM for database management and code generation in the message logging system. |

##### networking/sqlite_message_logger/migrations/

| File | Description |
|------|-------------|
| `.gitkeep` | Git placeholder file to maintain migrations directory structure. Ensures the migrations directory is preserved in version control even when empty. Used by diesel migration system to store database migration files for message logger schema management. |

###### networking/sqlite_message_logger/migrations/2022-10-20-121212_create_table/

| File | Description |
|------|-------------|
| `down.sql` | Database migration rollback script for message logger table creation. SQL script to revert the initial table creation migration for message logging database. Used by diesel migration system to rollback database schema changes when needed. |
| `up.sql` | Database migration script for message logger table creation. SQL script to create initial tables for storing network message logs including message content, timestamps, and metadata. Used by diesel migration system to set up message logging database schema. |

###### networking/sqlite_message_logger/migrations/2022-10-20-121213_create_inbound/

| File | Description |
|------|-------------|
| `down.sql` | Database migration rollback script for inbound message table creation. SQL script to revert the inbound message table migration for message logging database. Used by diesel migration system to rollback inbound message schema changes when needed. |
| `up.sql` | Database migration script for inbound message table creation. SQL script to create tables for storing inbound network message logs including message direction, content, and processing metadata. Used by diesel migration system to set up inbound message logging schema for debugging and analysis. |

##### networking/sqlite_message_logger/src/

| File | Description |
|------|-------------|
| `lib.rs` | Main library module for SQLite message logger. Provides SQLite-based logging functionality for network messages with database persistence and querying capabilities. Exports logger types, database interfaces, and message storage utilities. Used for debugging network communication and analyzing message flows in development environments. |
| `schema.rs` | Database schema definitions for SQLite message logger. Contains diesel-generated schema definitions for message logging tables including column types, relationships, and constraints. [GENERATED] file created by diesel CLI from database migrations. Used by diesel ORM for type-safe database operations and query generation. |
| `sqlite_message_log.rs` | SQLite message logging implementation. Provides concrete implementation of message logging with SQLite backend including message insertion, querying, and storage management. Implements message logger traits and database operations for persistent network message storage and analysis. |

#### networking/swarm/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for tari_swarm crate. Provides libp2p swarm management and networking utilities for Tari DAN nodes. Dependencies include libp2p with extensive feature support for various protocols and transports. Includes optional metrics support for monitoring. Used to manage P2P network swarms and coordinate peer connections in the DAN. |

##### networking/swarm/src/

| File | Description |
|------|-------------|
| `behaviour.rs` | libp2p network behavior implementation for tari_swarm. Implements NetworkBehaviour trait to provide comprehensive P2P networking functionality including peer discovery, protocol handling, and swarm coordination. Manages multiple libp2p protocols and coordinates network operations. Core component that orchestrates all P2P networking behavior for DAN nodes. |
| `config.rs` | Configuration structures for tari_swarm. Defines swarm configuration including protocol settings, connection limits, timeouts, and network behavior parameters. Provides default configurations and validation for swarm operations. Used to configure P2P networking behavior, connection management, and protocol coordination in DAN nodes. |
| `error.rs` | Error types for tari_swarm operations. Defines swarm-specific error types using thiserror for structured error handling. Includes errors for swarm initialization, protocol failures, connection issues, and network coordination problems. Provides centralized error handling for all swarm operations and P2P networking functionality. |
| `lib.rs` | Tari-specific libp2p swarm implementation providing node behavior, configuration, error handling, protocol versioning, and metrics. Defines TariSwarm and TariNodeBehaviour for P2P networking with identity management, rendezvous, messaging, and substream protocols. Core networking layer for all Tari P2P communication. |
| `metrics.rs` | Metrics collection for tari_swarm networking. Provides Prometheus metrics for swarm performance including connection counts, protocol statistics, message rates, and network health indicators. Exports metrics for monitoring P2P network performance and troubleshooting connectivity issues. Optional feature for production monitoring and observability. |
| `protocol_ids.rs` | Protocol identifier definitions for tari_swarm. Defines unique protocol identifiers for different libp2p protocols used in the Tari network. Provides protocol naming conventions and identifier management for network protocol negotiation. Used to ensure protocol compatibility and proper protocol routing in P2P communication. |
| `protocol_version.rs` | Protocol version management for tari_swarm. Handles protocol versioning, compatibility checking, and version negotiation between peers. Provides version comparison utilities and backward compatibility management. Used to ensure proper protocol version negotiation and maintain network compatibility across different node versions. |

### prometheus/

| File | Description |
|------|-------------|
| `README.md` | Documentation for Prometheus monitoring setup. Explains how to configure and deploy Prometheus monitoring for Tari nodes including metrics collection, dashboard configuration, and alerting setup. Provides guidance on monitoring DAN network health, performance metrics, and operational dashboards. |
| `dashboard.json` | Grafana dashboard configuration for Tari monitoring. JSON configuration defining dashboard panels, queries, and visualizations for monitoring Tari node performance and network health. Includes metrics for DAN operations, networking statistics, and system performance. Used to create comprehensive monitoring dashboards for Tari infrastructure. |
| `docker-compose.yml` | Docker Compose configuration for Prometheus monitoring stack. Defines services for Prometheus, Grafana, and related monitoring components for Tari nodes. Includes volume mounts, network configuration, and service dependencies. Used to deploy complete monitoring infrastructure for Tari development and production environments. |
| `prometheus.yml` | Prometheus server configuration for Tari monitoring. Defines scrape targets, collection intervals, and metric collection rules for Tari nodes and services. Configures service discovery, alerting rules, and data retention policies. Used to configure Prometheus to collect metrics from DAN nodes, base nodes, and supporting infrastructure. |

### scripts/

| File | Description |
|------|-------------|
| `build_dists_tarball.sh` | Build script for creating distribution tarballs. Shell script that compiles Tari binaries and packages them into distribution archives for various platforms. Handles cross-compilation, binary optimization, and archive creation for release distribution. Used in CI/CD pipeline to create downloadable release packages. |
| `build_webuis.sh` | Build script for web user interfaces. Shell script that builds frontend applications and web UIs for Tari components including wallet UIs and management dashboards. Handles JavaScript/TypeScript compilation, asset bundling, and production optimization. Used to create production-ready web interfaces for Tari applications. |
| `bundle-app-bins.sh` | Application bundling script for Tari binaries. Shell script that bundles compiled applications with their dependencies and configuration files for distribution. Handles binary collection, dependency resolution, and packaging for various deployment scenarios. Used to create self-contained application bundles for deployment and distribution. |
| `code_coverage.sh` | Code coverage analysis script. Shell script that runs test suites with coverage instrumentation and generates coverage reports for Tari codebase. Handles test execution, coverage data collection, and report generation in various formats. Used in CI/CD to monitor test coverage and ensure code quality standards. |
| `create_bundle.sh` | Bundle creation script for Tari packages. Shell script that creates comprehensive packages containing binaries, documentation, configuration templates, and supporting files. Handles file organization, permission setting, and bundle validation. Used to create complete installation packages for various deployment environments. |
| `cross_compile_ubuntu_18-pre-build.sh` | Cross-compilation preparation script for Ubuntu 18.04. Shell script that sets up build environment and dependencies for cross-compiling Tari binaries targeting Ubuntu 18.04. Handles toolchain installation, environment configuration, and pre-build setup. Used to ensure compatibility with older Ubuntu LTS releases. |
| `env_sample` | Sample environment configuration file. Template file showing required and optional environment variables for Tari development and deployment. Includes configuration examples for different environments (development, testing, production) with documentation and default values. Used as reference for setting up Tari node environments. |
| `file_license_check.sh` | License header validation script. Shell script that checks source files for proper license headers and copyright notices. Validates license compliance across the codebase and identifies files missing required headers. Used in CI/CD to ensure all source files have appropriate license information and maintain legal compliance. |
| `install_tor.sh` | Tor installation script for privacy features. Shell script that installs and configures Tor for providing privacy and anonymity features in Tari nodes. Handles Tor service setup, configuration, and integration with Tari networking. Used to enable privacy-preserving networking capabilities in Tari deployments. |
| `install_ubuntu_dependencies.sh` | Ubuntu dependency installation script. Shell script that installs required system dependencies and development tools for building Tari on Ubuntu systems. Handles package manager updates, dependency resolution, and build tool setup. Used to prepare Ubuntu development environments for Tari compilation and development. |
| `publish_crates.sh` | Crate publishing script for cargo crates. Shell script that automates the publishing of Tari crates to crates.io registry. Handles version management, dependency ordering, and publication sequencing for multiple crates. Used in release process to publish Tari libraries and components to the Rust package registry. |
| `sign-hashes.sh` | Hash signing script for release verification. Shell script that generates cryptographic signatures for release hashes to ensure download integrity. Handles GPG key management, hash calculation, and signature generation for binary releases. Used to provide cryptographic verification of release packages and maintain security in the distribution process. |
| `test_in_docker.sh` | Docker testing script for containerized test execution. Shell script that runs Tari test suites inside Docker containers to ensure consistent testing environments. Handles container setup, test execution, and result collection in isolated environments. Used for CI/CD testing and ensuring reproducible test results across different platforms. |

#### utilities/db_inspector/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for database inspector utility. Provides web-based database inspection and debugging tools for Tari node databases. Dependencies include web server frameworks, database access libraries, and serialization support. Used for debugging database state, analyzing storage issues, and providing operational insights into node database content. |
| `build.rs` | Build script for database inspector utility. Handles asset compilation, template processing, and static resource preparation for the web-based database inspector interface. Configures build-time generation of web assets and database schema information. Used during compilation to prepare web interface resources. |

##### utilities/db_inspector/src/

| File | Description |
|------|-------------|
| `cli.rs` | Command-line interface for database inspector utility. Defines CLI arguments, options, and commands for launching and configuring the database inspector web server. Handles database path specification, server configuration, and operational parameters. Used to configure and launch the database inspection web interface from command line. |
| `config.rs` | Configuration structures for database inspector utility. Defines configuration options for database access, web server settings, and inspection parameters. Provides validation and default configurations for the database inspector service. Used to configure database connections, server ports, and inspection behavior. |
| `helpers.rs` | Helper utilities for database inspector. Provides utility functions for database access, data formatting, serialization, and web response generation. Includes common operations for database queries, data transformation, and presentation formatting. Used across the inspector to standardize database operations and data handling. |
| `main.rs` | Database inspection utility providing web-based interface for examining RocksDB and SQLite databases used by Tari components. Offers database exploration, schema inspection, and data visualization through a web server interface. Essential debugging and administration tool for database analysis and troubleshooting in development and production environments. |

###### utilities/db_inspector/src/webserver/

| File | Description |
|------|-------------|
| `context.rs` | Web server context management for database inspector. Provides request context, database connection management, and shared state for web handlers. Manages per-request database access and maintains connection pooling for efficient database operations. Used to coordinate database access across web requests and maintain consistent state. |
| `either_cf.rs` | Column family abstraction for database inspector. Provides unified interface for accessing different database column families and storage backends. Handles type-safe access to various database storage structures with consistent API. Used to abstract database column family differences and provide uniform access patterns for web handlers. |
| `error.rs` | Error handling for database inspector web server. Defines web-specific error types, HTTP error responses, and error conversion utilities. Provides structured error handling for database access failures, web request errors, and response generation issues. Used to ensure proper error reporting and user-friendly error messages in the web interface. |
| `handler.rs` | Main web request handler for database inspector. Coordinates request routing, handler dispatch, and response generation for the database inspector web interface. Manages middleware, authentication, and request processing pipeline. Core component that orchestrates web request handling and coordinates with specific database inspection handlers. |
| `mod.rs` | Web server module for database inspector. Organizes and exports web server components including handlers, context management, and server infrastructure. Provides centralized access to web server functionality and coordinates web service initialization. Used to structure web server organization and manage web service components. |
| `server.rs` | Web server implementation for database inspector. Implements HTTP server for hosting database inspection web interface including route configuration, middleware setup, and server lifecycle management. Provides REST API endpoints and serves static web UI assets. Core server component that hosts the database inspection web application. |

###### utilities/db_inspector/src/webserver/handlers/

| File | Description |
|------|-------------|
| `block_diff.rs` | Block difference analysis handler for database inspector. Provides web endpoints for comparing blocks, analyzing block differences, and examining block chain evolution. Handles block comparison queries and presents block diff information through web interface. Used for debugging blockchain state transitions and analyzing block content differences. |
| `blocks.rs` | Block inspection handler for database inspector. Provides web endpoints for viewing block details, block headers, and block chain information. Handles block queries, pagination, and detailed block data presentation. Used for examining blockchain state, debugging block processing, and analyzing block chain structure through web interface. |
| `bookkeeping.rs` | Bookkeeping data handler for database inspector. Provides web endpoints for examining accounting data, transaction records, and financial state information. Handles queries for balance tracking, transaction history, and audit trail data. Used for debugging financial operations and analyzing transaction processing through web interface. |
| `column_families.rs` | Column family inspection handler for database inspector. Provides web endpoints for examining database column families, their schemas, and data organization. Handles column family enumeration, data browsing, and storage structure analysis. Used for debugging database organization and analyzing storage layout through web interface. |
| `databases.rs` | Database overview handler for database inspector. Provides web endpoints for listing available databases, database statistics, and high-level database information. Handles database enumeration, size reporting, and metadata presentation. Used for getting overview of database instances and selecting databases for detailed inspection. |
| `foreign_substate_pledges.rs` | Foreign substate pledge handler for database inspector. Provides web endpoints for examining cross-shard substate pledges and foreign state references in the DAN. Handles pledge queries, state dependencies, and inter-shard relationship analysis. Used for debugging DAN cross-shard operations and analyzing distributed state management. |
| `mod.rs` | Handler module organization for database inspector web server. Organizes and exports all web request handlers for different database inspection features. Provides centralized access to handler implementations and coordinates handler registration. Used to structure web handler organization and provide unified access to inspection functionality. |
| `state_transitions.rs` | State transition analysis handler for database inspector. Provides web endpoints for examining DAN state transitions, substate changes, and state evolution. Handles state transition queries, change tracking, and transition history analysis. Used for debugging DAN state management and analyzing component state changes through web interface. |
| `tables.rs` | Database table inspection handler for database inspector. Provides web endpoints for examining database tables, table schemas, and table content browsing. Handles table enumeration, data pagination, and table structure analysis. Used for detailed examination of database table content and debugging data storage issues. |
| `types.rs` | Type definitions for database inspector web handlers. Defines common types, response structures, and data models used across web handlers. Provides type safety and consistent data representations for web API responses. Used to standardize data structures and ensure type consistency across database inspection endpoints. |

##### utilities/db_inspector/web_ui/

| File | Description |
|------|-------------|
| `.gitignore` | Git ignore file for database inspector web UI. Specifies files and directories to exclude from version control including build artifacts, dependency directories, and generated files. Used to maintain clean version control for the React-based web interface development. |
| `README.md` | Documentation for database inspector web UI. Explains the React-based web interface for database inspection including setup instructions, development workflow, and usage guidance. Provides information about the frontend architecture and development practices for the database inspector interface. |
| `eslint.config.js` | ESLint configuration for database inspector web UI. Defines code quality rules, style guidelines, and linting preferences for TypeScript/React development. Includes rules for code formatting, error detection, and development best practices. Used to maintain code quality and consistency in the web interface development. |
| `index.html` | Main HTML template for database inspector web UI. Provides the base HTML structure for the React application including meta tags, title, and root element. Serves as the entry point for the single-page application that provides the database inspection interface. |
| `package.json` | Package configuration for database inspector web UI. Defines dependencies, scripts, and project metadata for the React-based frontend application. Includes build tools, UI libraries, and development dependencies for creating the database inspection web interface. Used for dependency management and build configuration. |
| `tsconfig.app.json` | TypeScript configuration for database inspector web UI application code. Extends base TypeScript configuration with application-specific settings for React development. Configures compilation targets, module resolution, and build settings for the main application code. Used for TypeScript compilation of React application components. |
| `tsconfig.json` | Base TypeScript configuration for database inspector web UI. Defines project-wide TypeScript compilation settings including compiler options, path mapping, and project references. Provides shared configuration used by application and build tool configurations. Main TypeScript project configuration file. |
| `tsconfig.node.json` | TypeScript configuration for database inspector web UI build tools. Extends base TypeScript configuration with Node.js-specific settings for build scripts and tooling. Configures compilation for Vite configuration and build-time scripts. Used for TypeScript compilation of build and development tooling. |
| `vite.config.ts` | Vite build configuration for database inspector web UI. Configures Vite build tool including React plugin setup, build optimization, development server settings, and asset handling. Defines build targets, output directories, and development workflow configuration. Used to configure the frontend build and development environment. |

###### utilities/db_inspector/web_ui/src/

| File | Description |
|------|-------------|
| `App.css` | Main stylesheet for database inspector web UI. Defines global styles, layout rules, and visual design for the React application. Includes CSS for database inspection interface components, responsive design, and visual theming. Used to style the database inspection web interface and provide consistent visual design. |
| `App.tsx` | Main React application component for database inspector web UI. Implements the root application component including routing, layout, and top-level state management. Coordinates navigation, theme management, and overall application structure. Core component that orchestrates the database inspection web interface functionality. |
| `client.ts` | API client for database inspector web UI. Implements HTTP client functionality for communicating with the database inspector backend API. Provides typed API calls, request handling, and response processing for database inspection operations. Used to bridge frontend interface with backend database inspection services. |
| `main.tsx` | Main entry point for database inspector web UI. React application bootstrap file that initializes the application, sets up routing, and renders the root component. Configures React DOM, router, and application-level providers. Entry point that starts the database inspection web interface application. |
| `vite-env.d.ts` | Vite environment type definitions for database inspector web UI. TypeScript declaration file providing type definitions for Vite build tool and environment variables. [GENERATED] file that enables TypeScript support for Vite-specific features and environment configuration in the React application. |

###### utilities/db_inspector/web_ui/src/components/

| File | Description |
|------|-------------|
| `MenuItems.tsx` | Menu navigation component for database inspector web UI. React component that provides navigation menu with links to different database inspection features. Handles menu state, active item highlighting, and navigation between inspection views. Used to provide consistent navigation interface across the database inspection application. |
| `StyledPaper.tsx` | Styled paper component for database inspector web UI. React component providing consistent styled containers with Material-UI Paper component. Implements reusable styling patterns, theming, and layout consistency across the database inspection interface. Used to maintain visual consistency and design system compliance. |
| `ThemeSwitcher.tsx` | Theme switcher component for database inspector web UI. React component that allows users to toggle between light and dark themes in the database inspection interface. Manages theme state, persistence, and visual theme transitions. Used to provide user-controlled theme customization and improved user experience. |

###### utilities/db_inspector/web_ui/src/routes/

| File | Description |
|------|-------------|
| `ErrorPage.tsx` | Error page component for database inspector web UI. React component that displays user-friendly error messages and provides error recovery options. Handles application errors, API failures, and routing errors with appropriate messaging. Used to provide graceful error handling and user guidance when issues occur. |
| `Home.tsx` | Home page component for database inspector web UI. React component that provides the main dashboard and entry point for database inspection features. Displays database overview, navigation options, and system status. Main landing page that introduces users to database inspection capabilities and provides navigation to specific features. |
| `InspectCf.tsx` | Column family inspection page for database inspector web UI. React component that provides detailed view of database column family content including data browsing, filtering, and analysis. Handles pagination, search functionality, and data presentation for specific column families. Used for detailed examination of database column family data. |
| `ListColumnFamilies.tsx` | Column families listing page for database inspector web UI. React component that displays available database column families with metadata and navigation links. Provides overview of database structure and entry points to detailed column family inspection. Used to browse database organization and select column families for detailed analysis. |

###### utilities/db_inspector/web_ui/src/store/

| File | Description |
|------|-------------|
| `databases.ts` | Database state management for database inspector web UI. TypeScript module that manages database-related state including available databases, active database selection, and database metadata. Provides state management hooks and utilities for database information across the React application. Used to coordinate database state across UI components. |
| `theme.ts` | Theme state management for database inspector web UI. TypeScript module that manages UI theme state including theme selection, persistence, and theme-related utilities. Provides theme management hooks and state coordination for consistent theming across the React application. Used to maintain theme consistency and user preferences. |

###### utilities/db_inspector/web_ui/src/theme/

| File | Description |
|------|-------------|
| `LayoutMain.tsx` | Main layout component for database inspector web UI. React component that provides the primary application layout including header, navigation, and content areas. Implements responsive design, theme integration, and consistent layout structure. Used as the main layout wrapper for all database inspection pages and features. |
| `colors.ts` | Color definitions for database inspector web UI theme. TypeScript module defining color palettes, theme colors, and visual design tokens for consistent styling. Provides color constants for light and dark themes including primary, secondary, and semantic colors. Used to maintain visual consistency and enable theme switching functionality. |
| `theme.css` | Global theme stylesheet for database inspector web UI. CSS file defining theme-specific styles, CSS variables, and global design system rules. Includes styles for theme transitions, component overrides, and responsive design patterns. Used to apply consistent theming and visual design across the database inspection interface. |
| `theme.ts` | Theme configuration for database inspector web UI. TypeScript module that configures Material-UI theme including typography, spacing, breakpoints, and component styling. Provides theme objects for light and dark modes with consistent design tokens. Used to configure the Material-UI theme system and maintain design consistency. |

#### utilities/generate_ristretto_value_lookup/

| File | Description |
|------|-------------|
| `.gitignore` | Git ignore file for Ristretto value lookup generator utility. Specifies files and directories to exclude from version control including build artifacts and generated lookup tables. Used to maintain clean version control for the cryptographic value generation utility. |
| `Cargo.toml` | Cargo manifest for Ristretto value lookup generator utility. Provides tools for generating cryptographic lookup tables and precomputed values for Ristretto operations. Dependencies include cryptographic libraries and generation utilities. Used for optimizing cryptographic operations by precomputing common values and lookup tables. |

##### utilities/generate_ristretto_value_lookup/src/

| File | Description |
|------|-------------|
| `cli.rs` | Command-line interface for Ristretto value lookup generator. Defines CLI arguments and options for configuring cryptographic value generation including table sizes, output formats, and generation parameters. Used to control the generation of optimized cryptographic lookup tables for Ristretto operations. |
| `main.rs` | Ristretto value lookup table generator utility for cryptographic operations. Generates precomputed lookup tables for Ristretto public key values within specified ranges, optimizing cryptographic performance in wallet and validation operations. Performance optimization tool reducing computational overhead for frequently used cryptographic operations in the Tari ecosystem. |

#### utilities/tariswap_test_bench/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for TariSwap test bench utility. Provides load testing and benchmarking tools for TariSwap decentralized exchange functionality. Dependencies include testing frameworks, metrics collection, and DAN interaction libraries. Used for performance testing, stress testing, and benchmarking TariSwap operations. |

##### utilities/tariswap_test_bench/src/

| File | Description |
|------|-------------|
| `accounts.rs` | Account management for TariSwap test bench. Handles creation, management, and coordination of test accounts for TariSwap benchmarking. Provides account setup, funding, and state management for load testing scenarios. Used to manage test account lifecycle and coordinate multi-account testing operations. |
| `cli.rs` | Command-line interface for TariSwap test bench. Defines CLI arguments and options for configuring load tests including test duration, concurrency levels, swap frequencies, and reporting options. Used to control TariSwap performance testing and benchmarking scenarios from command line. |
| `faucet.rs` | Faucet management for TariSwap test bench. Handles test token distribution and account funding for TariSwap load testing. Provides automated token dispensing and balance management for test scenarios. Used to ensure test accounts have sufficient tokens for sustained TariSwap benchmarking operations. |
| `main.rs` | Performance benchmarking tool for TariSwap decentralized exchange operations. Creates multiple accounts, executes swaps, measures transaction throughput, and generates performance statistics. Essential utility for load testing the network, validating DEX functionality, and measuring system performance under various transaction loads. |
| `runner.rs` | Test runner for TariSwap test bench. Orchestrates load testing execution including test scheduling, concurrency management, and result coordination. Handles test lifecycle, worker coordination, and performance measurement. Core component that manages TariSwap benchmark execution and coordinates testing operations across multiple workers. |
| `stats.rs` | Statistics collection and reporting for TariSwap test bench. Handles performance metrics, timing data, and test result aggregation. Provides statistical analysis, reporting utilities, and performance measurement tools. Used to collect, analyze, and report TariSwap performance benchmarking results and system performance metrics. |
| `tariswap.rs` | TariSwap interaction module for test bench. Provides API for interacting with TariSwap decentralized exchange including swap operations, liquidity management, and state queries. Handles swap execution, transaction management, and result processing. Used to perform actual TariSwap operations during load testing and benchmarking. |
| `templates.rs` | Template management for TariSwap test bench. Handles deployment and management of test templates including TariSwap contracts and faucet templates. Provides template compilation, deployment, and version management for testing environments. Used to ensure consistent template versions and manage test template lifecycle. |
| `timer.rs` | Timing utilities for TariSwap test bench. Provides high-precision timing, scheduling, and performance measurement tools for benchmarking operations. Handles test timing coordination, latency measurement, and timing statistics collection. Used to ensure accurate performance measurement and timing coordination in TariSwap load testing scenarios. |

###### utilities/tariswap_test_bench/templates/faucet/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for TariSwap test bench faucet template. Provides test faucet template for distributing tokens during TariSwap benchmarking. Configured for WebAssembly compilation with tari_template_lib dependency. Used to create token distribution infrastructure for sustained load testing scenarios. |

###### utilities/tariswap_test_bench/templates/faucet/src/

| File | Description |
|------|-------------|
| `lib.rs` | Faucet template implementation for TariSwap test bench. Implements token distribution functionality specifically designed for load testing scenarios. Provides high-throughput token dispensing with configurable distribution rates and balance management. Used to maintain token liquidity during sustained TariSwap benchmarking operations. |

###### utilities/tariswap_test_bench/templates/tariswap/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for TariSwap test bench swap template. Provides TariSwap template optimized for load testing and benchmarking scenarios. Configured for WebAssembly compilation with enhanced performance characteristics for high-throughput testing. Used to deploy TariSwap functionality optimized for benchmark testing environments. |

###### utilities/tariswap_test_bench/templates/tariswap/src/

| File | Description |
|------|-------------|
| `lib.rs` | TariSwap template implementation for test bench. Implements decentralized exchange functionality optimized for load testing with enhanced performance characteristics and monitoring capabilities. Provides automated market maker, liquidity management, and swap execution optimized for high-throughput benchmarking scenarios. |

#### utilities/transaction_generator/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for transaction generator utility. Provides tools for generating DAN transactions, manifests, and test scenarios. Dependencies include transaction building libraries, serialization support, and DAN interaction utilities. Used for creating transaction workloads, testing scenarios, and automated transaction generation for DAN testing. |

##### utilities/transaction_generator/manifests/

| File | Description |
|------|-------------|
| `create_free_coins.rs` | Free coins creation manifest for transaction generator. Provides transaction manifest template for creating free test coins in DAN testing environments. Includes transaction structure, parameter configuration, and execution logic for automated coin generation. Used to bootstrap test accounts with initial token balances for testing scenarios. |
| `tariswap_do_swap.rs` | TariSwap transaction manifest for transaction generator. Provides transaction manifest template for performing TariSwap operations including token swaps, liquidity provision, and exchange interactions. Includes parameterized transaction structure for automated TariSwap testing and load generation scenarios. |

##### utilities/transaction_generator/src/

| File | Description |
|------|-------------|
| `cli.rs` | Command-line interface for transaction generator utility. Defines CLI arguments and options for configuring transaction generation including transaction types, generation rates, output formats, and execution parameters. Used to control automated transaction generation and testing scenario execution from command line. |
| `lib.rs` | Transaction generation utility for testing and benchmarking. Provides transaction builders, readers, and writers for creating synthetic transaction workloads. Used in performance testing and load generation for blockchain stress testing. |
| `main.rs` | Main entry point for transaction generator utility. Orchestrates transaction generation workflow including CLI processing, configuration loading, transaction building, and output management. Coordinates between different transaction types and generation strategies. Primary executable for automated DAN transaction generation and testing. |
| `transaction_reader.rs` | Transaction reading utilities for transaction generator. Provides functionality for reading, parsing, and validating existing transactions from various sources. Handles transaction deserialization, format detection, and validation. Used to analyze existing transactions, extract patterns, and create transaction templates for generation scenarios. |
| `transaction_writer.rs` | Transaction writing utilities for transaction generator. Provides functionality for serializing, formatting, and outputting generated transactions to various destinations. Handles transaction serialization, file output, and format conversion. Used to export generated transactions for submission, analysis, or storage in testing workflows. |

###### utilities/transaction_generator/src/transaction_builders/

| File | Description |
|------|-------------|
| `free_coins.rs` | Free coins transaction builder for transaction generator. Implements transaction building logic for creating free test coins including account setup, balance initialization, and coin distribution. Provides configurable coin amounts and recipient accounts for testing scenarios. Used to create initial token balances for DAN testing and development environments. |
| `manifest.rs` | Manifest transaction builder for transaction generator. Implements transaction building from manifest templates including manifest parsing, parameter substitution, and transaction construction. Provides flexible transaction generation from declarative manifest definitions. Used to generate complex transactions from reusable manifest templates for automated testing. |
| `mod.rs` | Transaction builder module organization for transaction generator. Organizes and exports different transaction building strategies including free coins, manifest-based, and custom transaction builders. Provides centralized access to transaction building functionality and coordinates builder implementations. Used to structure transaction generation capabilities. |

#### utilities/transaction_submitter/

| File | Description |
|------|-------------|
| `Cargo.toml` | Cargo manifest for transaction submitter utility. Provides tools for submitting transactions to DAN nodes with high throughput and concurrency control. Dependencies include async runtime, HTTP client libraries, and DAN interaction utilities. Used for load testing, transaction submission automation, and high-volume transaction processing scenarios. |

##### utilities/transaction_submitter/src/

| File | Description |
|------|-------------|
| `bounded_spawn.rs` | Bounded task spawning utilities for transaction submitter. Provides controlled task spawning with concurrency limits to prevent resource exhaustion during high-volume transaction submission. Implements backpressure control and resource management for async task execution. Used to manage submission concurrency and maintain system stability under load. |
| `cli.rs` | Command-line interface for transaction submitter utility. Defines CLI arguments and options for configuring transaction submission including target nodes, submission rates, concurrency limits, and monitoring options. Used to control automated transaction submission and load testing scenarios from command line. |
| `main.rs` | Transaction stress testing utility for load testing Tari validator nodes. Submits batches of transactions to multiple validator nodes, tracks success/failure rates, measures response times, and generates performance statistics. Essential tool for network performance validation, capacity planning, and identifying bottlenecks in transaction processing pipelines. |

---
