# Universe

**Repository:** https://github.com/tari-project/universe

**Branch:** main

## Project Overview

## Tari Universe Codebase Overview

### Directory Structure

```
tari-universe/
├── .github/                    # GitHub configuration and CI/CD workflows
│   ├── ISSUE_TEMPLATE/         # Bug report and issue templates
│   ├── workflows/              # CI/CD pipelines (build, test, release)
│   └── dependabot.yml          # Automated dependency updates
├── ci/                         # Build and deployment utilities
│   ├── build-notes.md          # Cross-platform build documentation
│   └── scripts/                # Build automation scripts
├── dist-isolation/             # Tauri isolation layer security
├── docs/                       # Technical documentation
│   ├── containerization/       # Docker setup guides
│   ├── sleep_mode/             # System sleep/wake behavior
│   └── tauri_config/           # Security configuration
├── public/                     
│   └── locales/               # i18n translations (12 languages)
│       ├── en/                # English (base language)
│       ├── cn/                # Chinese
│       ├── de/                # German
│       └── ...                # 9 more languages
├── scripts/                   # Development and build scripts
│   ├── i18n_checker.py       # Translation validation
│   └── translator.js         # Translation management
├── src/                      # React frontend application
│   ├── App/                  # Main application container
│   ├── components/           # Reusable UI components
│   ├── containers/           # Feature containers
│   │   ├── main/            # Dashboard, mining, wallet
│   │   ├── navigation/      # Sidebar navigation
│   │   └── floating/        # Modals and overlays
│   ├── hooks/               # Custom React hooks
│   ├── store/               # Zustand state management
│   ├── theme/               # Styled-components theming
│   ├── types/               # TypeScript definitions
│   └── utils/               # Utility functions
├── src-tauri/               # Rust backend application
│   ├── src/                 # Rust source code
│   │   ├── binaries/        # External binary management
│   │   ├── configs/         # Configuration management
│   │   ├── gpu_miner/       # GPU mining implementation
│   │   ├── node/            # Blockchain node integration
│   │   ├── p2pool/          # P2Pool mining support
│   │   ├── setup/           # Application setup phases
│   │   ├── tapplets/        # Tapplet (mini-app) system
│   │   ├── utils/           # Backend utilities
│   │   └── main.rs          # Backend entry point
│   ├── binaries-versions/   # Binary version configs
│   ├── capabilities/        # Tauri security permissions
│   └── tauri.conf.json      # Tauri configuration
├── tari-win-bundler/        # Windows installer configuration
├── package.json             # Frontend dependencies
├── Cargo.toml              # Backend dependencies
└── README.md               # Project documentation
```

### Architecture Overview

Tari Universe is a desktop cryptocurrency mining application built with a modern web stack packaged as a native desktop application:

#### Technology Stack

**Frontend:**
- React 19.1.0 with TypeScript
- Vite build system with HMR
- Zustand for state management
- styled-components for theming
- i18next for internationalization (12 languages)
- @tari-project/tari-tower for 3D blockchain visualization
- Framer Motion for animations

**Backend:**
- Rust with Tauri 2.x framework
- Tokio async runtime
- Tari blockchain libraries
- External binary management (XMRig, GPU miners, Tor)
- SQLite for local storage

**Desktop Integration:**
- Native system tray
- Auto-launch on boot
- Hardware monitoring (CPU/GPU)
- Cross-platform: Windows, macOS, Linux

#### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        User Interface                        │
│  ┌─────────────┐  ┌──────────────┐  ┌──────────────────┐  │
│  │   Mining    │  │    Wallet    │  │    Settings      │  │
│  │  Dashboard  │  │  Management  │  │  Configuration   │  │
│  └─────────────┘  └──────────────┘  └──────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                               │
                    ┌──────────▼──────────┐
                    │   Tauri IPC Bridge  │
                    │  (Command Handlers) │
                    └──────────┬──────────┘
                               │
┌─────────────────────────────────────────────────────────────┐
│                      Rust Backend                            │
│  ┌─────────────┐  ┌──────────────┐  ┌──────────────────┐  │
│  │   Mining    │  │   Wallet     │  │     Node         │  │
│  │  Managers   │  │   Adapter    │  │   Management     │  │
│  └─────────────┘  └──────────────┘  └──────────────────┘  │
│  ┌─────────────┐  ┌──────────────┐  ┌──────────────────┐  │
│  │  External   │  │   Config     │  │    Process       │  │
│  │  Binaries   │  │  Management  │  │   Management     │  │
│  └─────────────┘  └──────────────┘  └──────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                               │
                    ┌──────────▼──────────┐
                    │  External Services  │
                    │  - Tari Blockchain  │
                    │  - Mining Pools     │
                    │  - Airdrop API      │
                    └─────────────────────┘
```

### Core Business Logic and Main Workflows

#### 1. Application Initialization Flow

The application follows a multi-phase setup process:

```
1. Splashscreen Phase
   └─> Load application configuration
   └─> Initialize logging and telemetry
   └─> Check system requirements

2. Core Setup Phase
   └─> Platform prerequisites check
   └─> Network connectivity test  
   └─> Auto-launcher configuration
   └─> System tray initialization

3. Hardware Setup Phase
   └─> Download mining binaries (XMRig, GPU miners)
   └─> Detect GPU capabilities
   └─> Run CPU benchmarking
   └─> Initialize hardware monitoring

4. Node Setup Phase
   └─> Initialize Tor (optional)
   └─> Start Tari base node
   └─> Synchronize blockchain
   └─> Monitor for orphan chains

5. Wallet Setup Phase
   └─> Initialize wallet binary
   └─> Create/import wallet
   └─> Setup spending wallet
   └─> Configure bridge tapplet

6. Mining Setup Phase
   └─> Configure P2Pool connection
   └─> Setup merge mining proxy
   └─> Initialize mining pools
   └─> Start auto-mining if configured
```

#### 2. Mining Workflow

The application supports both CPU and GPU mining:

```
User clicks "Start Mining"
    │
    ├─> CPU Mining Path
    │   ├─> Launch XMRig process
    │   ├─> Connect to mining pool/proxy
    │   ├─> Monitor hashrate and temperature
    │   └─> Update UI with mining statistics
    │
    └─> GPU Mining Path
        ├─> Launch GPU miner binary
        ├─> Configure mining algorithm (SHA3, RandomX)
        ├─> Allocate GPU resources
        └─> Track performance metrics
```

Mining modes:
- **Eco Mode**: 30% CPU utilization
- **Ludicrous Mode**: 100% resources
- **Custom Mode**: User-defined thread allocation

#### 3. Wallet Operations

Key wallet functionality:

```
Transaction Flow:
1. User initiates send transaction
2. Validate Tari address
3. Check sufficient balance
4. Create transaction
5. Broadcast to network
6. Update transaction history

Bridge Operations:
1. Connect to WXTM bridge
2. Swap between XTM ↔ WXTM
3. Monitor bridge transactions
4. Update unified balance
```

#### 4. Airdrop Game Integration

The application includes gamification elements:

```
Airdrop System:
├─> User Authentication (OAuth)
├─> Mining Rewards (gems)
├─> Referral System
├─> Community Messages
└─> Shell of Secrets (special events)
```

### Key Technical Patterns and Conventions

#### Frontend Patterns

1. **Component Organization**
   ```typescript
   // Presentational components in /components
   // Container components in /containers
   // Shared UI elements in /components/elements
   ```

2. **State Management with Zustand**
   ```typescript
   // Store per domain
   useWalletStore()    // Wallet state
   useMiningStore()    // Mining state
   useUIStore()        // UI preferences
   
   // Actions separated from stores
   import { startMining } from '@app/store/actions'
   ```

3. **Styled Components Theming**
   ```typescript
   // Theme-aware components
   const Button = styled.button`
     background: ${({ theme }) => theme.palette.primary};
   `;
   ```

4. **Custom Hooks Pattern**
   ```typescript
   // Business logic in hooks
   const { balance, isLoading } = useTariBalance();
   ```

#### Backend Patterns

1. **Singleton Pattern for Managers**
   ```rust
   // Global state managers
   HardwareStatusMonitor::instance()
   MiningStatusManager::instance()
   ```

2. **Process Adapter Pattern**
   ```rust
   // Unified interface for external processes
   trait ProcessAdapter {
       fn spawn() -> Result<ProcessInstance>;
       fn health_check() -> HealthStatus;
   }
   ```

3. **Event-Driven Communication**
   ```rust
   // Backend to frontend events
   events_emitter.emit::<MiningStatusUpdate>(status);
   ```

4. **Configuration Management**
   ```rust
   // Layered configuration
   ConfigCore → Application settings
   ConfigMining → Mining parameters
   ConfigWallet → Wallet preferences
   ```

### Important Dependencies and Integrations

#### Core Dependencies

**Frontend:**
- React ecosystem (React Query, React Hook Form)
- Tauri plugins (shell, clipboard, process)
- Animation libraries (Framer Motion, Lottie)
- Crypto utilities (@noble/hashes)

**Backend:**
- Tari libraries (tari_core, tari_crypto)
- Mining integration (nvml-wrapper for NVIDIA)
- Networking (reqwest, tokio-tungstenite)
- Security (ring, keyring)

#### External Integrations

1. **Mining Pools**
   - P2Pool decentralized mining
   - Traditional mining pools
   - Merge mining with Monero

2. **Blockchain Services**
   - Tari base node (local or remote)
   - Block explorers
   - Network status APIs

3. **Exchange Integration**
   - WXTM bridge for wrapped tokens
   - Exchange-specific branding
   - Swap functionality

4. **External Binaries**
   - XMRig (CPU mining)
   - GPU miners (multiple algorithms)
   - Tor daemon (privacy)
   - Tari node and wallet

### Database Schema Overview

The application uses SQLite for local storage with the following key areas:

1. **Configuration Storage**
   - User preferences
   - Mining settings
   - Network configuration

2. **Wallet Data**
   - Transaction history
   - Address book
   - Balance cache

3. **Mining Statistics**
   - Historical hashrates
   - Earnings tracking
   - Hardware performance

### API Structure

#### Tauri Commands (Frontend → Backend)

```typescript
// Mining control
invoke('start_mining', { mode: 'Eco' })
invoke('stop_mining')
invoke('get_mining_status')

// Wallet operations  
invoke('send_transaction', { address, amount, message })
invoke('get_wallet_balance')
invoke('import_seed_words', { seedWords })

// Configuration
invoke('set_config_mining', { config })
invoke('get_app_config')
```

#### Backend Events (Backend → Frontend)

```typescript
// Mining updates
listen('mining_status_update', (status) => {})
listen('new_block_found', (block) => {})

// Wallet events
listen('wallet_balance_update', (balance) => {})
listen('transaction_update', (tx) => {})
```

#### External APIs

1. **Airdrop API**
   ```
   GET /api/user/details
   POST /api/referral/claim
   WebSocket /ws/mining-status
   ```

2. **Bridge API**
   ```
   GET /api/wxtm/balance
   POST /api/wxtm/swap
   ```

### Testing Approach

#### Frontend Testing
- Unit tests for utilities and hooks
- Component testing with React Testing Library
- Integration tests for critical workflows

#### Backend Testing
- Unit tests for business logic
- Integration tests for external processes
- Performance benchmarks for mining

#### E2E Testing
- Tauri-driver for desktop automation
- Critical user journey testing
- Cross-platform validation

### Build and Deployment Notes

#### Development Setup

```bash
## Install dependencies
npm install
cd src-tauri && cargo build

## Development mode
npm run tauri dev

## Production build
npm run tauri build
```

#### Platform-Specific Builds

**Windows:**
- Requires Visual Studio Build Tools
- WiX toolset for MSI creation
- Code signing with Azure Key Vault

**macOS:**
- Xcode Command Line Tools
- Apple Developer certificate
- Notarization for distribution

**Linux:**
- Build for multiple package formats (.deb, .AppImage)
- Handle different GPU driver requirements

#### Release Process

1. **Version Bump**
   - Update package.json
   - Update Cargo.toml
   - Update tauri.conf.json

2. **Build Artifacts**
   ```bash
   # Triggered by GitHub Actions
   .github/workflows/release.yml
   ```

3. **Distribution**
   - GitHub Releases
   - Auto-updater manifests
   - Exchange-specific builds

#### Environment Configuration

```env
## Development
VITE_TARI_UNIVERSE_VERSION=1.2.9
VITE_USE_LOCAL_CONFIG=true

## Production
TAURI_SIGNING_PRIVATE_KEY=***
TAURI_SIGNING_PUBLIC_KEY=***
```

#### Deployment Considerations

1. **Auto-Updates**
   - Differential updates to minimize bandwidth
   - Signature verification
   - Rollback capability

2. **Telemetry**
   - Anonymous usage statistics
   - Opt-in crash reporting
   - Performance monitoring

3. **Security**
   - CSP policies for web content
   - Process isolation
   - Secure credential storage

This comprehensive overview provides the foundation for understanding and contributing to the Tari Universe codebase. The modular architecture, clear separation of concerns, and extensive documentation make it accessible for new developers while maintaining the flexibility needed for a complex desktop application.

## Codebase Structure

### Root Directory

| File | Description |
|------|-------------|
| `.coderabbit.yml` | CodeRabbit AI code review configuration file. Disables review status and collapses walkthrough sections. Enables auto review for non-draft PRs. Contains special path instructions for localization files (public/locales/**/*.json) to consolidate translation comments and prevent suggesting English-to-other-language translations, as the workflow adds English keys across all languages first, then batch updates translations later. |
| `.env` | [SENSITIVE FILE] Environment variables configuration containing API keys, secrets, and sensitive configuration values for Tari Universe. Likely includes external service credentials, build-time secrets, and development environment settings. Used by build processes and runtime configuration but excluded from version control for security. Contains confidential information that should not be exposed publicly. |
| `.gitignore` | Git ignore configuration for Tari Universe project. Excludes standard Node.js artifacts (logs, node_modules, dist folders), editor files (.vscode except extensions.json, .idea, .DS_Store), and development files (*.local, vite-profile*). Includes Tauri-specific exclusions: src-tauri/gpu_status.json for GPU status tracking, .env.sentry-build-plugin for Sentry configuration, and .updater/*.json for auto-updater files. Also excludes Windows-specific bundler artifacts from tari-win-bundler directory (logs, .vs, bin folders). Essential for maintaining clean repository without build artifacts, temporary files, or sensitive configuration data. |
| `.license.ignore` | License scanning exclusion configuration for Tari Universe. Excludes Windows bundler files (Bundle.wxs, Tari Universe.wixproj, .wixproj.user, Theme.wxl, Theme.xml) from license compliance checks. These Windows Installer XML (WiX) files are build artifacts and configuration files that don't require license headers. Used by license scanning tools to avoid false positives during automated license compliance verification. |
| `.nvmrc` | Node Version Manager configuration specifying Node.js v20.17.0 for Tari Universe development. Ensures consistent Node.js version across development environments when using nvm. Used by development tools and CI/CD systems to automatically switch to the correct Node.js version for building and running the application. |
| `.prettierignore` | Prettier code formatting exclusion configuration for Tari Universe. Ignores build artifacts (build, coverage, dist, dist-isolation), all JSON files in locales directories (to preserve translation file formatting), and log files (logs, *.log, npm/yarn/pnpm debug logs). Prevents Prettier from reformatting generated files, translation files that may have specific formatting requirements, and temporary files that don't need formatting. Used in conjunction with .prettierrc configuration to maintain code style consistency while excluding appropriate file types. |
| `.prettierrc` | Prettier code formatting configuration for Tari Universe. Uses ES5 trailing commas, 4-space indentation, semicolons, single quotes, bracket spacing, and 120-character line width. Sets automatic line ending detection for cross-platform compatibility. Includes special override for YAML files to use 2-space indentation instead of 4. Integrated with ESLint via eslint-plugin-prettier for consistent code formatting across the project. Used by development tools and CI/CD for automatic code formatting. |
| `CHANGELOG.md` | Release notes and version history for Tari Universe application. Documents v1.2.9 'Colonies of Light' (June 12, 2025) with link to supported exchanges, revamped transaction history including bridge transactions, Linux Tor support, improved download handling for node setup, stability improvements for lower-end machines, and airdrop game bug fixes. Previous versions include v1.2.8 'The Feast of Light' adding native wXTM purchase with ETH/USDT/USDC and bridge status improvements, and v1.2.7 'The Crown of Every Heart' with message rendering fixes, transaction error handling, faster balance updates, and clearer transaction status display. Comprehensive changelog spanning multiple releases with feature additions, bug fixes, and user experience improvements for the Tari mining and wallet application. |
| `CODEOWNERS` | GitHub CODEOWNERS configuration defining review requirements for Tari Universe repository. Requires @tari-project/devops team approval for all .github/ directory changes (CI/CD workflows, issue templates, PR templates) and the CODEOWNERS file itself. Ensures DevOps team oversight for critical infrastructure and automation changes. Used by GitHub to automatically request reviews from appropriate teams when protected files are modified. |
| `LICENSE.md` | Tari Universe licensing document under Enhanced Common Public Attribution License Version 1.0, based on Common Public Attribution License Version 1.0. Defines comprehensive terms including Commercial Use, Contributor, Covered Code, Electronic Distribution Mechanism, Executable, Initial Developer, Larger Work, and License definitions. References user agreement and provides detailed legal framework for software distribution, modification, and attribution requirements. Critical legal document governing the open-source nature of Tari Universe while ensuring proper attribution and compliance with licensing terms. |
| `README.md` | Project documentation for Tari Universe v1, a desktop mining application for Tari blockchain (XTM tokens). Includes installation instructions for Windows (.msi), macOS (.dmg), and Linux (.deb/.AppImage). Details build requirements: Node.js, Rust, Tauri CLI, platform-specific dependencies. Describes development workflow with npm/cargo commands for linting (ESLint, Clippy), formatting (Prettier, rustfmt), and building. Configuration stored in platform-specific app data directories. Links to community Discord, GitHub issues, and RFC documentation. |
| `SECURITY.md` | Comprehensive security vulnerability disclosure policy for Tari Universe and related Tari Labs projects. Defines scope including tari-project GitHub repositories, Tari RFCs, academic publications, and Tari Labs infrastructure. Excludes archived repositories, documentation sites, and third-party dependencies. Outlines responsible disclosure process with security@tarilabs.com contact, vulnerability assessment procedures, and coordinated disclosure timelines. Includes bounty program details, disclosure guidelines, and legal safe harbor provisions for security researchers. Critical document establishing security research framework and protecting both researchers and the project through clear vulnerability reporting protocols. |
| `eslint.config.js` | ESLint configuration for Tari Universe frontend with comprehensive linting rules. Integrates React, TypeScript, React Hooks, i18next, React Compiler, and Prettier. Configured for browser globals with TypeScript parser and JSX support. Targets src/ and scripts/ directories while ignoring config files. Key rules: no-console warnings (allows info/warn/error), TypeScript unused variables with underscore prefix allowance, disabled React prop-types (TypeScript used), internationalization literal string enforcement (markupOnly), and React Compiler recommendations. Combines recommended configurations from multiple plugins for robust code quality enforcement. |
| `index.html` | Main HTML entry point for Tari Universe desktop application. Preloads Poppins, Druk, and IBM Plex Mono font families for optimal typography performance. Includes CSS-in-HTML font-face declarations with font-display: swap for better loading. Preloads Cloudflare Stream video content for block bubble and sync animations. Sets up responsive viewport, Tari SVG favicon, and basic styling for light/dark theme support. React app mounts to #root div via src/main.tsx module script. |
| `knip.ts` | Knip configuration for detecting unused files, dependencies, and exports in Tari Universe. Scans src/ and scripts/ directories for TypeScript/JavaScript files. Enforces strict rules: errors for unused files, unlisted dependencies, and duplicates; warnings for unused dependencies, exports, and types. Ignores specific binaries (commitlint) and Babel plugins needed by build process but not directly imported. Enables ignoreExportsUsedInFile to avoid false positives for exports used within the same file. Used by npm run lint and npm run knip commands for maintaining clean codebase and dependency management. |
| `package.json` | Frontend package configuration for Tari Universe v1.2.9. Defines scripts: dev (Vite dev server), build (TypeScript + Vite production build), lint (Knip + ESLint), translate (i18n script). Key dependencies: React 19.1.0, Zustand (state), styled-components (styles), i18next (i18n), @tari-project/tari-tower (3D visualization), Tauri plugins. Dev dependencies include React Compiler, ESLint with React/i18next rules, Knip for unused code detection. Module type: ESM. |
| `tsconfig.json` | TypeScript configuration for Tari Universe frontend targeting ES2020 with modern bundler setup. Enables strict type checking, React JSX transform, DOM libraries, and Vite-compatible module resolution. Configured for bundler mode with TypeScript extension imports, synthetic defaults, and JSON module resolution. Defines @app/* path mapping to ./src/* for clean imports. Includes src directory with project references to tsconfig.node.json for build tooling. Optimized for React development with proper type safety and modern JavaScript features. |
| `tsconfig.node.json` | TypeScript configuration for Node.js build tools and configuration files. Enables composite project structure with ESNext modules, bundler resolution, and synthetic default imports. Specifically includes vite.config.ts for proper TypeScript support in build configuration. Separated from main tsconfig.json to isolate build-time dependencies from application code. |
| `vite.config.ts` | Vite build configuration for React frontend. Plugins: React with Babel (styled-components, React Compiler), tsconfigPaths, ESLint. React Compiler configured for src/App files only. Alias @app -> ./src. Dev server: port 1420, ignores src-tauri changes. Production: sourcemap enabled. Conditional config based on command (serve vs build). ReactCompilerConfig enables optimization for App components. LogLevel: error for cleaner output. |

### .github/

| File | Description |
|------|-------------|
| `PULL_REQUEST_TEMPLATE.md` | GitHub pull request template providing structured format for code contributions to Tari Universe. Includes sections for description, motivation and context, testing procedures, and reviewer guidance. Features breaking changes checklist covering data directory deletion, hard fork requirements, and other changes. Includes conventional commit guidance for CHANGELOG generation and footer instructions for breaking changes. Ensures consistent PR documentation, proper testing verification, and clear communication about potential breaking changes to maintainers and users. |
| `dependabot.yml` | GitHub Dependabot configuration for automated dependency updates in Tari Universe repository. Monitors GitHub Actions dependencies with weekly update schedule to ensure CI/CD workflows use latest action versions. Helps maintain security and stability by automatically creating pull requests for outdated GitHub Actions. Part of repository maintenance automation reducing manual dependency management overhead. |

#### .github/ISSUE_TEMPLATE/

| File | Description |
|------|-------------|
| `bug_report.md` | GitHub issue template for structured bug reporting in Tari Universe repository. Provides standardized format including bug description, reproduction steps, expected behavior, screenshots, and system information (OS, browser, device details). Includes clear instructions directing users to Discord and Telegram for general support rather than GitHub issues. Features automatic bug-report label assignment and emphasizes development-related bugs only. Essential for consistent bug reporting, faster issue triage, and separating development issues from user support requests. |
| `config.yml` | GitHub issue template configuration that disables blank issues and provides contact links for community support. Directs users to Tari Community Discord, Tari Project Telegram for immediate help, and HackerOne Bug Bounty program for security vulnerabilities. Part of GitHub's issue management system to route users to appropriate support channels. |

#### .github/workflows/

| File | Description |
|------|-------------|
| `ci-changelog.yml` | GitHub Actions workflow for syncing CHANGELOG.md to S3 storage and invalidating CloudFlare cache. Triggers on pushes to main/ci-changelog-* branches when CHANGELOG.md changes. Uses Docker with AWS CLI to upload changelog to S3 bucket with proper content-type headers, then purges CloudFlare cache for immediate distribution. Requires AWS and CloudFlare secrets for authentication. |
| `ci.yml` | GitHub Actions CI workflow for Tari Universe performing comprehensive code quality checks and testing. Triggered on push to ci-* branches, pull requests, and merge groups with concurrency control to prevent duplicate runs. Runs cargo checks (fmt, clippy, check) on Ubuntu with Linux dependencies (webkit2gtk, protobuf, OpenCL headers). Includes frontend linting with knip and ESLint, cross-platform builds for Linux x64/ARM and Windows x64, and Tauri application building. Features dependency caching, Node.js v20 setup, Rust toolchain installation, and automated testing pipeline. Critical for maintaining code quality, preventing regressions, and ensuring cross-platform compatibility before merging changes. |
| `lint.yml` | GitHub Actions linting workflow for Tari Universe code quality enforcement. Triggered on pull requests to ensure code standards before merging. Runs on Ubuntu with Node.js LTS, installs dependencies via npm ci, and executes comprehensive linting checks: npm run lint (ESLint + Knip for unused exports), and npm run lint:taplo for TOML file formatting. Uses npm cache for faster builds and ensures consistent code formatting across the codebase. Critical for maintaining code quality and preventing style inconsistencies in the repository. |
| `pr.yml` | GitHub Actions workflow for Pull Request validation. Runs on PR open/reopen/edit/sync events. Performs three key checks: 1) Validates commits are signed using 1Password's action, 2) Checks PR title follows conventional commit format using commitlint, 3) Validates i18n JSON files with jsonlint and runs Python scripts to compare locales and find unused translations. Uploads i18n check logs as artifacts for review. |
| `release-exchange.yml` | Complex GitHub Actions workflow for building and releasing exchange-specific versions of Tari Universe. Builds for multiple platforms (Ubuntu x64/ARM, Windows, macOS universal) and supports mainnet/nextnet/esmeralda networks. Features include: exchange-specific branding via secrets, cross-platform Rust/Node.js builds, code signing (Azure for Windows, Apple for macOS), WiX installer bundling, Sentry debug symbol upload, and artifact distribution via GitHub or AWS S3. Creates exchange-specific updater manifests and handles cache invalidation. |
| `release.yml` | GitHub Actions release workflow for building and distributing Tari Universe across multiple platforms. Triggered on release/beta branches, scheduled daily builds, or manual dispatch. Builds for Ubuntu (deb, appimage), Windows (msi), and macOS (dmg, app) with platform-specific configurations. Uses release-ci feature flags and handles beta builds with special labeling. Includes cross-platform matrix strategy with best-effort ARM builds and automated artifact uploads. Features code signing, notarization for macOS, and release asset management. Critical for delivering production-ready binaries to users across all supported platforms with proper security certificates and update mechanisms. |

### ci/

| File | Description |
|------|-------------|
| `build-notes.md` | Comprehensive build documentation for cross-platform compilation of Tari Universe on Apple Silicon for Linux ARM64 using Docker. Provides step-by-step instructions including Docker container setup with SSH auth sock, Ubuntu 20.04 ARM64 platform, and volume mounting for persistent storage. Details dependency installation: basic utilities (curl, wget, ca-certificates), build tools (build-essential), Tauri dependencies (webkit2gtk, protobuf, librsvg2), RandomX build requirements (cmake, git, make), and OpenSSL libraries. Includes complete Rust toolchain installation, Node.js 22.x setup from NodeSource, npm build process, Tauri CLI installation, and final application building. Critical for cross-platform development and CI/CD pipeline setup enabling developers to build ARM64 Linux binaries from macOS development machines. |
| `windows-dev-environment-notes.md` | Documentation file containing development environment setup notes specifically for Windows development. Provides guidance for developers working on Tari Universe on Windows platforms, likely including toolchain setup, dependencies, and Windows-specific build considerations. |

### dist-isolation/

| File | Description |
|------|-------------|
| `index.html` | Tauri isolation layer security script. Contains the __TAURI_ISOLATION_HOOK__ function that intercepts and potentially filters Tauri commands for security. Currently has placeholder TODOs for preventing command execution and whitelisting commands. This isolation layer helps protect against malicious command injection by providing a security boundary between the frontend and backend. |

#### docs/containerization/

| File | Description |
|------|-------------|
| `Dockerfile` | Docker container definition for running Tari Universe in containerized environment. Based on Ubuntu 22.04 with essential GUI and WebKit dependencies: libfontconfig, libharfbuzz, libx11-6, libgbm-dev, webkit2gtk, dbus-x11, OpenGL/EGL libraries for graphics rendering. Copies and executes entrypoint.sh script for application startup. Designed for testing Tari Universe across different platforms and architectures while supporting GUI rendering through X11 forwarding. Part of containerization strategy for cross-platform development and testing workflows. |
| `containerization.md` | Docker containerization documentation for Tari Universe proving proof-of-concept for cross-platform testing. Describes running Tari Universe from AppImage in containerized environment for testing across different operating systems and architectures. Documents limitations: GPU mining only works on Linux hosts with NVIDIA drivers, recommends rocker for GPU access, suggests cloud instances for other GPU architectures. Provides Linux-specific testing instructions: X11 server access configuration (xhost +local:root), AppImage copying and renaming, Docker image building, and container execution script. Designed for development and testing scenarios rather than production deployment. Enables developers to test Tari Universe behavior across multiple platforms from single development machine. |
| `docker_script.sh` | Docker script for running Tari Universe in a containerized environment on Linux with X11 GUI support. Configures environment variables for GUI accessibility bridge, display forwarding, and sets TARI_NETWORK to esmeralda testnet. Mounts X11 socket, machine IDs, SSL certificates, and application data directory. Enables GPU access via /dev/dri and runs in privileged mode with host networking for development/testing purposes. |
| `entrypoint.sh` | Docker container entrypoint script that automatically finds and executes the Tari Universe AppImage file. Searches for .AppImage files in /app/AppImage directory, verifies executability, and launches with --appimage-extract-and-run flag for containerized environments. Provides error handling if no AppImage is found or if the file is not executable. |

#### docs/sleep_mode/

| File | Description |
|------|-------------|
| `sleep_mode.md` | Technical documentation analyzing Tari Universe behavior during system sleep/wake cycles. Documents base node behavior: broken communication pipes on wake-up due to peer disconnections, automatic re-dialing to establish new connections, and successful peer reconnection. Details networking issues: remote nodes marking local node offline, TTL timeouts, connection failures, and forced closures. Includes real log examples showing error patterns, connection recovery processes, and timeout scenarios. Notes XMRig miner behavior on laptop battery power with potential pause-on-battery configuration. Important for troubleshooting connectivity issues, understanding node resilience, and optimizing power management settings for mining operations. |

#### docs/tauri_config/

| File | Description |
|------|-------------|
| `tauri_config.md` | Comprehensive documentation for Tauri configuration security and CSP (Content Security Policy) considerations in Tari Universe. Details CSP sources required for @tari-project/tari-tower 3D animations including worker-src, blob: sources, unsafe-eval for WebGL, and CDN access. Documents WebSocket connections for airdrop functionality, styled-components requirements with unsafe-inline and dangerousDisableAssetCspModification. Covers profile image sources for Twitter and Google integration. Critical reference for security configuration, CSP troubleshooting, and understanding Tauri security model for desktop applications with web content. |

##### public/locales/af/

| File | Description |
|------|-------------|
| `airdrop.json` | i18n localization file for Afrikaans (af) translation of airdrop/referral system UI. Contains translated text for airdrop game interface, gem claiming, friend invitation system, bonus rewards, and user permission dialogs. Key sections: gem claiming UI, referral code system, friend invitation mechanics, bonus tier calculations, analytics permissions, login/logout flows. Used by React components via i18next for Afrikaans language support. Part of 12-language internationalization system supporting testnet mining rewards and social features. |
| `bridge.json` | i18n localization file for Afrikaans (af) translation of blockchain bridge functionality. Contains translated UI text for bridge transactions, cross-chain operations, and token swapping features. Used by React components via i18next for Afrikaans language support in bridge-related user interfaces. Part of 12-language internationalization system supporting cross-chain transaction flows. |
| `common.json` | i18n localization file for Afrikaans (af) translation of common/shared UI elements. Contains translated text for frequently used interface elements, buttons, dialogs, error messages, and general application terms used across multiple components. Used by React components via i18next for Afrikaans language support of shared UI elements. Part of 12-language internationalization system providing common vocabulary for the Tari Universe application. |
| `components.json` | i18n localization file for Afrikaans (af) translation of reusable UI components. Contains translated text for component-specific labels, tooltips, placeholders, and messages used in shared React components throughout the application. Used by React components via i18next for Afrikaans language support of reusable UI elements. Part of 12-language internationalization system supporting component-based architecture. |
| `exchange.json` | i18n localization file for Afrikaans (af) translation of cryptocurrency exchange integration features. Contains translated UI text for exchange listings, trading interfaces, token swapping, and external exchange connections. Used by React components via i18next for Afrikaans language support in exchange-related user interfaces. Part of 12-language internationalization system supporting crypto trading functionality. |
| `external-dependency-dialog.json` | i18n localization file for Afrikaans (af) translation of external dependency management dialogs. Contains translated text for dependency installation, update prompts, binary download dialogs, and external tool configuration interfaces. Used by React components via i18next for Afrikaans language support in dependency management flows. Part of 12-language internationalization system supporting mining software and external tool management. |
| `info.json` | i18n localization file for Afrikaans (af) translation of informational content and help text. Contains translated text for tooltips, help dialogs, information panels, and explanatory content throughout the application. Used by React components via i18next for Afrikaans language support of informational UI elements. Part of 12-language internationalization system providing contextual help and guidance. |
| `mining-view.json` | i18n localization file for Afrikaans (af) translation of mining interface components. Contains translated text for mining dashboard, hashrate displays, earnings calculations, mining controls, power level settings, and mining status indicators. Used by React components via i18next for Afrikaans language support in mining-related user interfaces. Part of 12-language internationalization system supporting CPU/GPU mining operations. |
| `p2p.json` | i18n localization file for Afrikaans (af) translation of P2Pool mining interface. Contains translated text for pool mining controls, squad statistics, pool connections, hashrate sharing, and collaborative mining features. Used by React components via i18next for Afrikaans language support in P2Pool mining interfaces. Part of 12-language internationalization system supporting decentralized pool mining operations. |
| `paper-wallet.json` | i18n localization file for Afrikaans (af) translation of paper wallet functionality. Contains translated text for wallet backup, seed phrase display, cold storage options, QR code generation, and secure wallet printing features. Used by React components via i18next for Afrikaans language support in wallet security interfaces. Part of 12-language internationalization system supporting wallet backup and recovery flows. |
| `reconnect.json` | i18n localization file for Afrikaans (af) translation of network reconnection interfaces. Contains translated text for connection recovery, network status messages, reconnection prompts, and connectivity troubleshooting. Used by React components via i18next for Afrikaans language support in network management interfaces. Part of 12-language internationalization system supporting network resilience features. |
| `settings.json` | i18n localization file for Afrikaans (af) translation of application settings interface. Contains translated text for configuration options, mining preferences, network settings, UI themes, language selection, and system preferences. Used by React components via i18next for Afrikaans language support in settings panels. Part of 12-language internationalization system supporting comprehensive application configuration. |
| `setup-progresses.json` | i18n localization file for Afrikaans (af) translation of setup progress indicators. Contains translated text for initialization steps, progress bars, setup status messages, and onboarding flow indicators. Used by React components via i18next for Afrikaans language support in application setup interfaces. Part of 12-language internationalization system supporting first-time user onboarding. |
| `setup-view.json` | i18n localization file for Afrikaans (af) translation of setup wizard interface. Contains translated text for initial configuration, wallet creation, network selection, and first-time setup workflows. Used by React components via i18next for Afrikaans language support in setup wizard components. Part of 12-language internationalization system supporting application initialization flows. |
| `sidebar.json` | i18n localization file for Afrikaans (af) translation of sidebar navigation elements. Contains translated text for navigation menu items, sidebar labels, navigation tooltips, and main application section names. Used by React components via i18next for Afrikaans language support in navigation interfaces. Part of 12-language internationalization system supporting application navigation structure. |
| `sos.json` | i18n localization file for Afrikaans (af) translation of Shell of Secrets (SoS) game interface. Contains translated text for the integrated mini-game, game instructions, rewards, progress tracking, and game-specific UI elements. Used by React components via i18next for Afrikaans language support in SoS game components. Part of 12-language internationalization system supporting gamification features. |
| `staged-security.json` | i18n localization file for Afrikaans (af) translation of staged security features. Contains translated text for security wizards, authentication flows, wallet protection, backup procedures, and multi-stage security verification processes. Used by React components via i18next for Afrikaans language support in security interfaces. Part of 12-language internationalization system supporting wallet security and protection flows. |
| `tribes-view.json` | i18n localization file for Afrikaans (af) translation of tribes/squad mining interface. Contains translated text for group mining features, squad statistics, collaborative mining rewards, team formation, and social mining aspects. Used by React components via i18next for Afrikaans language support in tribes/squad interfaces. Part of 12-language internationalization system supporting social mining features. |
| `wallet.json` | i18n localization file for Afrikaans (af) translation of wallet interface components. Contains translated text for wallet operations, transaction history, balance displays, send/receive flows, transaction details, and wallet management features. Used by React components via i18next for Afrikaans language support in wallet interfaces. Part of 12-language internationalization system supporting comprehensive wallet functionality. |

##### public/locales/cn/

| File | Description |
|------|-------------|
| `airdrop.json` | i18n localization file for Chinese (cn) translation of airdrop/referral system UI. Contains translated text for airdrop game interface, gem claiming, friend invitation system, bonus rewards, and user permission dialogs. Key sections: gem claiming UI, referral code system, friend invitation mechanics, bonus tier calculations, analytics permissions, login/logout flows. Used by React components via i18next for Chinese language support. Part of 12-language internationalization system supporting testnet mining rewards and social features. |
| `bridge.json` | i18n localization file for Chinese (cn) translation of blockchain bridge functionality. Contains translated UI text for bridge transactions, cross-chain operations, and token swapping features. Used by React components via i18next for Chinese language support in bridge-related user interfaces. Part of 12-language internationalization system supporting cross-chain transaction flows. |
| `common.json` | Chinese (Simplified) localization file for common UI strings in Tari Universe. Contains shared terminology used across multiple components: navigation controls (返回, 取消, 关闭), status labels (完成, 失败, 已连接), time units (天, 月, 年), mining terms (哈希率, 温度), and universal actions. Chinese translation of the English common.json base template. |
| `components.json` | i18n localization file for Chinese (cn) translation of reusable UI components. Contains translated text for component-specific labels, tooltips, placeholders, and messages used in shared React components throughout the application. Used by React components via i18next for Chinese language support of reusable UI elements. Part of 12-language internationalization system supporting component-based architecture. |
| `exchange.json` | i18n localization file for Chinese (cn) translation of cryptocurrency exchange integration features. Contains translated UI text for exchange listings, trading interfaces, token swapping, and external exchange connections. Used by React components via i18next for Chinese language support in exchange-related user interfaces. Part of 12-language internationalization system supporting crypto trading functionality. |
| `external-dependency-dialog.json` | Chinese (CN) localization file for external dependency dialog interface. Contains 4 translation keys: title, description, and download-and-install button text. Used by external dependency modal that prompts users to download required dependencies for proper application functionality. Part of i18next internationalization system supporting 12 languages. Dependencies: i18next translation framework. Used by: external dependency dialog components, setup process components. |
| `info.json` | Chinese (CN) localization file for information/onboarding view. Contains structured content and headings for 6-step welcome tutorial explaining Tari Universe, mining, testnet status, and user onboarding. Includes content.step-1 through step-6 and heading.step-1 through step-6 translation keys with emoji placeholders. Used by info dialog, welcome screens, and help sections. Dependencies: i18next translation framework. Used by: onboarding components, help dialogs, info containers. |
| `mining-view.json` | Chinese (Simplified) translations for mining interface and dashboard. Localized mining controls, performance metrics, visual elements, connection status, troubleshooting steps, P2Pool integration, and reward notifications adapted for Chinese-speaking miners. Includes culturally appropriate technical terminology for cryptocurrency mining concepts and blockchain operations. |
| `p2p.json` | Chinese (CN) localization file for P2P/pool mining interface. Contains translation keys for P2Pool squad-based mining features, pool statistics, chain information, and peer-to-peer mining functionality. Used by P2Pool components, squad earnings displays, and collaborative mining features. Dependencies: i18next translation framework. Used by: P2Pool containers, mining statistics, squad management components. |
| `paper-wallet.json` | Chinese (CN) localization file for paper wallet functionality. Contains translation keys for wallet QR code generation, mobile app connection, passphrase management, and Tari Aurora wallet linking features. Used by paper wallet components, mobile integration, and wallet export features. Dependencies: i18next translation framework. Used by: wallet containers, QR code generators, mobile connection dialogs. |
| `reconnect.json` | Chinese (CN) localization file for network reconnection interface. Contains translation keys for connection retry logic, network status messages, reconnection prompts, and connectivity troubleshooting. Used by network status components, connection management, and automatic retry mechanisms. Dependencies: i18next translation framework. Used by: network containers, connection status indicators, retry dialogs. |
| `settings.json` | Chinese (Simplified) translations for Tari Universe settings interface. Comprehensive localization of mining configuration, network settings, wallet functionality, application preferences, P2Pool mining interface, hardware monitoring, node configuration, debug features, and experimental settings. Adapted for Chinese regulatory and cultural context with appropriate technical terminology. |
| `setup-progresses.json` | Chinese (CN) localization file for setup progress indicators. Contains translation keys for installation steps, progress messages, dependency download status, and setup completion feedback. Used by setup wizard, progress bars, and installation status displays. Dependencies: i18next translation framework. Used by: setup containers, progress components, installation dialogs. |
| `setup-view.json` | Chinese (CN) localization file for setup view interface. Contains translation keys for initial setup wizard, configuration steps, welcome messages, and setup completion screens. Used by main setup flow, configuration dialogs, and first-run experience. Dependencies: i18next translation framework. Used by: setup containers, wizard components, configuration forms. |
| `sidebar.json` | Chinese (CN) localization file for sidebar navigation interface. Contains translation keys for navigation menu items, sidebar labels, menu tooltips, and navigation actions. Used by main application sidebar, navigation components, and menu systems. Dependencies: i18next translation framework. Used by: sidebar containers, navigation components, menu items. |
| `sos.json` | Chinese (CN) localization file for SOS/emergency help interface. Contains translation keys for error messages, troubleshooting guides, emergency actions, and crisis support. Used by error dialogs, help systems, and emergency recovery features. Dependencies: i18next translation framework. Used by: error containers, help dialogs, troubleshooting components. |
| `staged-security.json` | Chinese (CN) localization file for staged security setup interface. Contains translation keys for security wizard, authentication steps, wallet protection, and security configuration. Used by security setup flow, authentication dialogs, and wallet protection features. Dependencies: i18next translation framework. Used by: security containers, authentication components, protection dialogs. |
| `tribes-view.json` | Chinese (CN) localization file for tribes/squads view interface. Contains translation keys for squad management, tribal mining features, group statistics, and collaborative mining displays. Used by tribes/squad views, team mining components, and group collaboration features. Dependencies: i18next translation framework. Used by: tribes containers, squad components, collaborative mining interfaces. |
| `wallet.json` | Chinese (Simplified) translations for wallet functionality including transaction management, send/receive operations, and balance display. Localized wallet interface with culturally appropriate financial terminology, transaction flow descriptions, address management, and error messages for Chinese-speaking users. |

##### public/locales/de/

| File | Description |
|------|-------------|
| `airdrop.json` | German (DE) localization file for airdrop/rewards interface. Contains 58 translation keys for testnet rewards, gem earning, friend invitations, analytics permissions, Twitter integration, referral codes, and airdrop participation. Includes modal dialogs, tooltips, permission requests, and success/error messages. Used by airdrop components, rewards tracking, and social features. Dependencies: i18next translation framework. Used by: airdrop containers, rewards components, social integration dialogs. |
| `bridge.json` | German (DE) localization file for bridge functionality interface. Contains translation keys for blockchain bridge operations, cross-chain transfers, bridge status, and token bridging features. Used by bridge components, cross-chain transaction dialogs, and interoperability features. Dependencies: i18next translation framework. Used by: bridge containers, cross-chain components, token transfer dialogs. |
| `common.json` | German localization file for common UI strings in Tari Universe. Contains shared terminology used across multiple components: navigation controls (zurück, abbrechen, schließen), status labels (vollständig, fehlgeschlagen, verbunden), time units (Tag, Monat, Jahr), mining terms (Hashrate, Temperatur), and universal actions. German translation of the English common.json base template. |
| `components.json` | German (DE) localization file for common UI components. Contains translation keys for reusable interface elements, buttons, forms, notifications, and shared component text. Used across multiple components for consistent German language support. Dependencies: i18next translation framework. Used by: all UI components, form elements, notification systems, shared interface elements. |
| `exchange.json` | German (DE) localization file for exchange/trading interface. Contains translation keys for cryptocurrency exchange features, trading operations, market data, and swap functionality. Used by exchange components, trading dialogs, and market interfaces. Dependencies: i18next translation framework. Used by: exchange containers, trading components, swap dialogs. |
| `external-dependency-dialog.json` | German (DE) localization file for external dependency dialog interface. Contains translation keys for dependency installation prompts, download buttons, and external binary requirement messages. Used by dependency management dialogs, installation wizards, and setup requirements. Dependencies: i18next translation framework. Used by: dependency components, installation dialogs, setup containers. |
| `info.json` | German (DE) localization file for information/onboarding view. Contains structured content and headings for 6-step welcome tutorial explaining Tari Universe, mining, testnet status, and user onboarding. Mirrors content structure of English version with German translations. Dependencies: i18next translation framework. Used by: info dialogs, onboarding components, help containers. |
| `mining-view.json` | German translations for mining interface and dashboard. Localized mining controls, performance metrics, visual elements, connection status, troubleshooting steps, P2Pool integration, and reward notifications adapted for German-speaking miners. Includes technically accurate German terminology for cryptocurrency mining concepts and blockchain operations. |
| `p2p.json` | German (DE) localization file for P2P/pool mining interface. Contains translation keys for P2Pool squad-based mining, pool statistics, collaborative mining features, and peer-to-peer functionality. Dependencies: i18next translation framework. Used by: P2Pool containers, squad management, mining statistics components. |
| `paper-wallet.json` | German (DE) localization file for paper wallet functionality. Contains translation keys for wallet QR code generation, mobile app connections, passphrase management, and Tari Aurora integration. Dependencies: i18next translation framework. Used by: wallet components, mobile integration, QR code dialogs. |
| `reconnect.json` | German (DE) localization file for network reconnection interface. Contains translation keys for connection retry, network troubleshooting, reconnection prompts, and connectivity status messages. Dependencies: i18next translation framework. Used by: network components, connection management, retry mechanisms. |
| `settings.json` | German translations for Tari Universe settings interface. Comprehensive localization of mining configuration, network settings, wallet functionality, application preferences, P2Pool mining interface, hardware monitoring, node configuration, debug features, and experimental settings. Adapted for German regulatory context with precise technical terminology. |
| `setup-progresses.json` | German (DE) localization file for setup progress indicators. Contains translation keys for installation steps, progress tracking, dependency downloads, and setup completion status. Dependencies: i18next translation framework. Used by: setup wizards, progress bars, installation components. |
| `setup-view.json` | German (DE) localization file for setup view interface. Contains translation keys for setup wizard, configuration steps, welcome screens, and first-run experience. Dependencies: i18next translation framework. Used by: setup containers, configuration dialogs, wizard components. |
| `sidebar.json` | German (DE) localization file for sidebar navigation interface. Contains translation keys for navigation menu, sidebar labels, menu tooltips, and navigation actions. Dependencies: i18next translation framework. Used by: sidebar containers, navigation components, menu systems. |
| `sos.json` | German (DE) localization file for SOS/emergency help interface. Contains translation keys for error handling, troubleshooting guides, emergency actions, and crisis support features. Dependencies: i18next translation framework. Used by: error dialogs, help systems, troubleshooting components. |
| `staged-security.json` | German (DE) localization file for staged security setup interface. Contains translation keys for security wizard, authentication flow, wallet protection, and security configuration steps. Dependencies: i18next translation framework. Used by: security containers, authentication dialogs, protection setup. |
| `tribes-view.json` | German (DE) localization file for tribes/squads view interface. Contains translation keys for squad management, tribal mining, group statistics, and collaborative features. Dependencies: i18next translation framework. Used by: tribes containers, squad components, group collaboration interfaces. |
| `wallet.json` | German translations for wallet functionality including transaction management, send/receive operations, and balance display. Localized wallet interface with proper German financial terminology, transaction flow descriptions, address management, and error messages for German-speaking users. |

##### public/locales/en/

| File | Description |
|------|-------------|
| `airdrop.json` | English localization file for airdrop/rewards system functionality. Contains strings for gem-based reward system including claiming gems, referral codes, friend invitations, analytics permissions, and airdrop participation. Covers user authentication (Twitter login), username management, bonus calculations, and notification settings. Includes detailed modal dialogs for explaining reward mechanics and privacy implications of analytics. |
| `bridge.json` | English localization file for bridge functionality. Contains minimal strings for cross-chain bridge operations, specifically for buying Tari (XTM) tokens. Part of the broader wallet ecosystem for cross-chain token swaps and exchanges. |
| `common.json` | English common UI translations (base language). Contains shared interface labels: navigation (back, continue, close), actions (cancel, submit, restart), states (pending, failed, complete), mining terms (hashrate, temperature, utilization), network terms (mainnet, testnet, connection), system messages (webgl-not-supported, shutting-down). Key phrases for error handling, confirmations, and general app interactions. Referenced across all UI components. Part of i18next internationalization system. |
| `components.json` | English localization file for reusable UI components. Contains strings for form validation errors (required fields, invalid data types), confirmation dialogs (ludicrous mining mode warning), release notes modal, app resume process status messages, network warm-up phase information banner, and system status notifications. Covers shared component text used across multiple parts of the application. |
| `exchange.json` | English localization file for exchange integration strings. Contains modal title for starting mining directly to supported exchange wallets. Used by i18next for internationalization of exchange selection UI. |
| `external-dependency-dialog.json` | English localization file for external dependency dialog strings. Contains title, description, and action button text for informing users about required external dependencies. Used by i18next for internationalization of dependency installation prompts. |
| `info.json` | English localization file for onboarding and information content strings. Contains step-by-step welcome content explaining Tari Universe, blockchain concepts, mining process, testnet status, and user empowerment messaging. Includes headings and detailed explanations for each onboarding step. Used by i18next for internationalization of welcome screens and educational content. |
| `mining-view.json` | English localization for mining interface components in Tari Universe. Contains strings for mining controls, status indicators, hardware monitoring, performance metrics, earnings display, and mining mode selection. Includes text for CPU/GPU mining toggles, hashrate displays, temperature warnings, and mining pool connections. |
| `p2p.json` | English localization file for P2P pool mining interface strings. Contains connection status indicators, network statistics, peer information, mining rewards tracking, and pool performance metrics. Includes specialized terms for RandomX/SHA3 stats, chain tip data, and reward thresholds with tooltips. Used by i18next for internationalization of pool mining dashboard and connection details. |
| `paper-wallet.json` | English localization file for mobile wallet synchronization strings. Contains text for QR code generation and scanning workflow to sync Tari Universe desktop wallet with Tari Universe Mobile app, including connection prompts, app download links, security warnings about QR code sharing, identification codes, and help messages. Used by i18next for internationalization of the paper wallet/mobile sync interface. |
| `reconnect.json` | English localization file for network reconnection interface strings. Contains error messages, status updates, and action buttons for when the application loses connection to the Tari network, including auto-reconnect countdown timers, severe disconnect scenarios with troubleshooting suggestions, and support options via Telegram. Used by i18next for internationalization of the network reconnection UI. |
| `settings.json` | English translations for Tari Universe settings interface. Comprehensive localization covering: mining configuration (CPU/GPU settings, power levels, custom modes), network settings (Tor configuration, bridges, entry guards), wallet functionality (seed words, addresses, import/export), application preferences (themes, languages, auto-start), P2Pool squad mining interface, hardware status monitoring, node configuration (local/remote), debug and logging features, update management, experimental features warnings, and user feedback systems. Includes technical terminology for blockchain operations, mining terminology, and user-friendly explanations for complex features. Supports dynamic content with interpolation ({{current}}/{{max}}) and HTML formatting. |
| `setup-progresses.json` | English localization file for application setup progress indicators. Contains detailed status messages for all setup phases including core, hardware, node, wallet, and mining setup stages, binary preparation steps (CPU miner, GPU miner, node, wallet, etc.), sync progress with dynamic values, error handling messages for critical failures, and time remaining calculations. Used by i18next for internationalization of the comprehensive setup progress display. |
| `setup-view.json` | English localization for setup and onboarding flow in Tari Universe. Contains strings for initial configuration steps, dependency installation, network selection, wallet creation, security setup, mining configuration wizard, and progress indicators. Guides users through first-time application setup and configuration validation. |
| `sidebar.json` | English localization file for sidebar navigation and mining interface strings. Contains labels for auto-miner controls with idle detection, mining schedules setup, wallet balance display, recent mining wins, reward milestones, social sharing functionality with "flex and invite" features for celebrating mining achievements, paper wallet sync buttons with tooltips, and mining history toggles. Used by i18next for internationalization of the main application sidebar. |
| `sos.json` | English localization file for SOS (Season of Swag) promotional widget strings. Contains text for upcoming reward displays (MacBook Pro 16'), countdown timer elements with days/hours indicators, friend invitation functionality to reduce wait times, crew building features, and link copying confirmations. Used by i18next for internationalization of the special promotional widget with countdown and social invitation mechanics. |
| `staged-security.json` | English localization file for wallet security backup workflow strings. Contains comprehensive text for multi-stage seed phrase backup process including introduction screens with wallet balance display, security warnings, seed phrase display with clipboard functionality, verification steps requiring word selection in correct order, and completion confirmations with settings access reminders. Used by i18next for internationalization of the critical staged security/backup interface. |
| `tribes-view.json` | English localization file for squad/team view strings. Contains minimal content with only a title for the squad view interface. Used by i18next for internationalization of the tribes/squad functionality display. |
| `wallet.json` | English localization for wallet functionality in Tari Universe. Includes strings for balance display, transaction history, address management, send/receive operations, wallet import/export, scanning progress, and security features. Contains text for Tari address formats (base58/emoji), transaction types (coinbase, standard), and wallet status messages. |

##### public/locales/fr/

| File | Description |
|------|-------------|
| `airdrop.json` | French (FR) localization file for airdrop/rewards interface. Contains translation keys for testnet rewards, gem earning, friend invitations, analytics permissions, Twitter integration, referral codes, and airdrop participation. Mirrors English airdrop functionality with French translations. Dependencies: i18next translation framework. Used by: airdrop containers, rewards tracking, social integration components. |
| `bridge.json` | French (FR) localization file for bridge functionality interface. Contains translation keys for blockchain bridge operations, cross-chain transfers, bridge status, and token bridging features. Dependencies: i18next translation framework. Used by: bridge containers, cross-chain components, interoperability features. |
| `common.json` | French localization file for common UI strings in Tari Universe. Contains shared terminology used across multiple components: navigation controls (retour, annuler, fermer), status labels (terminé, échec, connecté), time units (jour, mois, année), mining terms (taux de hachage, température), and universal actions. French translation of the English common.json base template. |
| `components.json` | French (FR) localization file for common UI components. Contains translation keys for reusable interface elements, buttons, forms, notifications, and shared component text. Used across multiple components for consistent French language support. Dependencies: i18next translation framework. Used by: all UI components, form elements, notification systems. |
| `exchange.json` | French (FR) localization file for exchange/trading interface. Contains translation keys for cryptocurrency exchange features, trading operations, market data, and swap functionality. Dependencies: i18next translation framework. Used by: exchange containers, trading components, swap dialogs. |
| `external-dependency-dialog.json` | French (FR) localization file for external dependency dialog interface. Contains translation keys for dependency installation prompts, download buttons, and external binary requirement messages. Dependencies: i18next translation framework. Used by: dependency components, installation dialogs, setup requirements. |
| `info.json` | French (FR) localization file for information/onboarding view. Contains structured content and headings for 6-step welcome tutorial explaining Tari Universe, mining concepts, testnet status, and user onboarding flow. Dependencies: i18next translation framework. Used by: info dialogs, onboarding components, help systems. |
| `mining-view.json` | French (FR) localization file for mining view interface. Contains 47 translation keys for mining controls, hashrate display, earnings estimation, mining button states, orphan chain warnings, pool statistics, and mining rewards. Includes bubbles.* keys for mining activity, mining-button-text.* for control states, pool.* for statistics, and block reward notifications. Dependencies: i18next translation framework. Used by: mining containers, mining controls, statistics displays, mining status components. |
| `p2p.json` | French (FR) localization file for P2P/pool mining interface. Contains translation keys for P2Pool squad-based mining, pool statistics, collaborative mining features, and peer-to-peer functionality. Dependencies: i18next translation framework. Used by: P2Pool containers, squad management, mining statistics. |
| `paper-wallet.json` | French (FR) localization file for paper wallet functionality. Contains translation keys for wallet QR code generation, mobile connections, passphrase management, and Tari Aurora integration. Dependencies: i18next translation framework. Used by: wallet components, mobile integration, QR code features. |
| `reconnect.json` | French (FR) localization file for network reconnection interface. Contains translation keys for connection retry logic, network troubleshooting, reconnection prompts, and connectivity status messages. Dependencies: i18next translation framework. Used by: network components, connection management, retry mechanisms. |
| `settings.json` | French (FR) localization file for settings interface. Contains extensive translation keys for application settings, mining configuration, network options, wallet management, security settings, Tor configuration, hardware controls, and system preferences. Includes tabs, error messages, confirmation dialogs, and feature descriptions. Based on Hindi version structure with 174 translation keys. Dependencies: i18next translation framework. Used by: settings containers, configuration dialogs, preference components. |
| `setup-progresses.json` | French (FR) localization file for setup progress indicators. Contains translation keys for installation steps, progress tracking, dependency downloads, and setup completion status. Dependencies: i18next translation framework. Used by: setup wizards, progress bars, installation components. |
| `setup-view.json` | French (FR) localization file for setup view interface. Contains translation keys for setup wizard, configuration steps, welcome screens, and first-run experience. Dependencies: i18next translation framework. Used by: setup containers, configuration dialogs, wizard components. |
| `sidebar.json` | French (FR) localization file for sidebar navigation interface. Contains translation keys for navigation menu, sidebar labels, menu tooltips, and navigation actions. Dependencies: i18next translation framework. Used by: sidebar containers, navigation components, menu systems. |
| `sos.json` | French (FR) localization file for SOS/emergency help interface. Contains translation keys for error handling, troubleshooting guides, emergency actions, and crisis support features. Dependencies: i18next translation framework. Used by: error dialogs, help systems, troubleshooting components. |
| `staged-security.json` | French (FR) localization file for staged security setup interface. Contains translation keys for security wizard, authentication flow, wallet protection, and security configuration steps. Dependencies: i18next translation framework. Used by: security containers, authentication dialogs, protection setup. |
| `tribes-view.json` | French (FR) localization file for tribes/squads view interface. Contains translation keys for squad management, tribal mining, group statistics, and collaborative features. Dependencies: i18next translation framework. Used by: tribes containers, squad components, group collaboration interfaces. |
| `wallet.json` | French (FR) localization file for wallet interface. Contains translation keys for wallet operations, balance display, transaction history, send/receive functionality, address management, and wallet security features. Dependencies: i18next translation framework. Used by: wallet containers, transaction components, balance displays, address management. |

##### public/locales/hi/

| File | Description |
|------|-------------|
| `airdrop.json` | Hindi translations for airdrop/rewards system. Contains translations for: gem claiming system (claimGems, claimReferralCode), friend invitation flow (inviteFriends, referral codes), modal dialogs (claimModalText with referral mechanics), analytics permissions (permission setup), social features (Twitter login, logout), bonus tier system (nextTierBonus), and status messages. Includes HTML-embedded content with gem counts and bonus calculations. Key for the gamification and social aspects of the app in Hindi language. |
| `bridge.json` | Hindi translations for bridge/exchange functionality. Currently contains single entry for buying Tari cryptocurrency (buy-tari: Tari (XTM) खरीदें). Part of the exchange/swap system translations that would handle cross-chain or exchange operations. Minimal content suggests this feature may be in development or simplified in current version. Used by exchange/bridge UI components. |
| `common.json` | Hindi translations for common UI elements. Mirrors English common.json structure with Hindi translations: navigation (back: वापस, continue: जारी रखें), actions (cancel: रद्द करें, submit: प्रस्तुत करें), states (failed: विफल, complete: पूर्ण), technical terms (hashrate: हैश दर, temperature: तापमान), application-specific (tari-universe: तारी यूनिवर्स, mainnet: मेननेट). Some entries like 'copied' and 'not-available' remain in English, indicating partial localization status. Core vocabulary for Hindi interface. |
| `components.json` | Hindi translations for UI components and dialogs. Contains: banner messages (network stability warnings, 'Tari is brand new' announcement), field validation errors (integers-only, required fields, email validation), specialized dialogs (ludicrousConfirmationDialog for mining mode warnings, releaseNotesDialog, resume-app-modal for process restart), warmupDialog with extensive network explanation text. Includes detailed technical explanations about Tari network warm-up phase, mining behavior, and user experience expectations. Critical for user guidance in Hindi. |
| `exchange.json` | Hindi translations for exchange functionality. Currently contains only English text for exchange modal (modal-title about mining Tari directly to exchange wallet). Indicates incomplete localization - content not yet translated to Hindi. Part of the exchange/swap system that integrates with supported cryptocurrency exchanges. Used by exchange selection UI components. |
| `external-dependency-dialog.json` | Hindi translations for external dependency dialogs. Would contain translations for system dependency check dialogs, binary download confirmations, and external tool installation prompts. Missing description indicates this file doesn't exist yet or contains untranslated content. Part of the app initialization and setup flow that handles required external binaries (XMRig, GPU miners, Tor) in Hindi language. |
| `info.json` | Hindi translations for informational content and help sections. Would contain translations for tooltips, help dialogs, educational content about mining, blockchain concepts, and system explanations. Missing description indicates this file doesn't exist yet or contains untranslated content. Part of the user education and onboarding system in Hindi language. |
| `mining-view.json` | Hindi translations for mining interface and status displays. Would contain translations for: mining controls (start/stop mining buttons), performance metrics (hashrate, temperature, utilization), mining status bubbles (active miners, block solving), pool statistics (accepted shares, earnings), error messages (connection lost), and mining rewards notifications. Missing description indicates file doesn't exist yet or contains untranslated content. Critical for mining UI in Hindi. |
| `p2p.json` | Hindi translations for P2Pool mining functionality. Would contain translations for: pool connection status, squad/pool statistics, shared mining metrics, collaborative mining explanations, and P2Pool-specific terminology. Missing description indicates file doesn't exist yet or contains untranslated content. Part of the collaborative mining system interface in Hindi language. |
| `paper-wallet.json` | Hindi translations for paper wallet and mobile integration functionality. Would contain translations for: wallet backup/recovery instructions, seed word management, QR code generation for mobile sync, passphrase entry, security warnings about wallet sharing, and mobile app linking. Missing description indicates file doesn't exist yet or contains untranslated content. Part of wallet security and mobile integration system in Hindi language. |
| `reconnect.json` | Hindi translations for network reconnection and connectivity management. Would contain translations for: connection retry messages, network status indicators, reconnection progress updates, connectivity troubleshooting instructions, and offline/online state notifications. Missing description indicates file doesn't exist yet or contains untranslated content. Part of network management and reliability system in Hindi language. |
| `settings.json` | Hindi translations for application settings and configuration. Would contain translations for: mining power controls (CPU/GPU settings), network configuration (Tor, bridges, nodes), wallet management (import, reset, paper wallet), application preferences (theme, language, auto-start), experimental features, logging and debug options, and system information displays. Missing description indicates file doesn't exist yet or contains untranslated content. Critical for app configuration in Hindi. |
| `setup-progresses.json` | Hindi translations for application setup and initialization progress indicators. Would contain translations for: setup step descriptions, progress messages during initial configuration, dependency installation status, wallet creation flow, network connection establishment, and onboarding completion messages. Missing description indicates file doesn't exist yet or contains untranslated content. Part of first-time setup experience in Hindi language. |
| `setup-view.json` | Hindi translations for setup view interface and onboarding screens. Would contain translations for: welcome messages, setup wizard steps, configuration options, user agreements, initial mining setup, security setup instructions, and setup completion notifications. Missing description indicates file doesn't exist yet or contains untranslated content. Part of user onboarding and initial configuration system in Hindi language. |
| `sidebar.json` | Hindi translations for sidebar navigation and menu items. Would contain translations for: main navigation labels (mining, wallet, settings, airdrop), menu items, navigation tooltips, section headers, and sidebar status indicators. Missing description indicates file doesn't exist yet or contains untranslated content. Part of the main navigation system in Hindi language. |
| `sos.json` | Hindi translations for SOS/help and emergency support functionality. Would contain translations for: help requests, emergency contact information, critical error messages, support ticket submission, troubleshooting guidance, and emergency recovery instructions. Missing description indicates file doesn't exist yet or contains untranslated content. Part of user support and emergency assistance system in Hindi language. |
| `staged-security.json` | Hindi translations for staged security and wallet protection features. Would contain translations for: multi-stage security setup, wallet encryption options, security warnings, backup verification steps, recovery procedures, and security best practices. Missing description indicates file doesn't exist yet or contains untranslated content. Part of the wallet security and protection system in Hindi language. |
| `tribes-view.json` | Hindi translations for tribes/squad view and community mining features. Would contain translations for: squad formation, group mining interface, community statistics, team earnings displays, collaborative mining explanations, and social mining features. Missing description indicates file doesn't exist yet or contains untranslated content. Part of the social and collaborative mining system in Hindi language. |
| `wallet.json` | Hindi translations for wallet functionality and transaction management. Would contain translations for: wallet balance displays, transaction history, send/receive operations, transaction details, wallet sync status, address management, fee calculations, and swap/exchange operations. Missing description indicates file doesn't exist yet or contains untranslated content. Critical for wallet interface in Hindi language. |

##### public/locales/id/

| File | Description |
|------|-------------|
| `airdrop.json` | Indonesian translations for airdrop/rewards system. Contains translations for: gem claiming system, friend invitation mechanics, referral code functionality, modal dialogs for rewards, analytics permissions setup, social features (Twitter integration), bonus tier calculations, and gamification elements. Missing description indicates file doesn't exist yet or contains untranslated content. Part of the social mining and rewards system in Indonesian language. |
| `bridge.json` | Indonesian translations for bridge/exchange functionality. Contains translations for cross-chain operations, token swapping, exchange integrations, and cryptocurrency purchasing features. Missing description indicates file doesn't exist yet or contains untranslated content. Part of the exchange and cross-chain bridge system in Indonesian language. |
| `common.json` | Indonesian translations for common UI elements and shared vocabulary. Contains translations for: navigation controls (back, continue, close), action buttons (cancel, submit, restart), status indicators (pending, failed, complete), technical terms (hashrate, temperature, utilization), network terms (mainnet, testnet), and application-specific terminology. Missing description indicates file doesn't exist yet or contains untranslated content. Core interface vocabulary for Indonesian language. |
| `components.json` | Indonesian translations for UI components and system dialogs. Contains translations for: informational banners (network status, warnings), form validation messages, confirmation dialogs (mining mode changes), system modals (release notes, app resume), and detailed explanatory content (network warm-up explanations). Missing description indicates file doesn't exist yet or contains untranslated content. Essential for component-level interactions in Indonesian language. |
| `exchange.json` | Indonesian translations for exchange functionality and third-party integrations. Contains translations for: exchange selection modals, supported exchange information, mining-to-exchange workflows, and integration setup instructions. Missing description indicates file doesn't exist yet or contains untranslated content. Part of the cryptocurrency exchange integration system in Indonesian language. |
| `external-dependency-dialog.json` | Indonesian translations for external dependency management dialogs. Contains translations for: system dependency checks, binary download confirmations, external tool installation prompts, dependency status updates, and installation error messages. Missing description indicates file doesn't exist yet or contains untranslated content. Part of app initialization and dependency management system in Indonesian language. |
| `info.json` | Indonesian translations for informational content and help documentation. Contains translations for: help tooltips, explanatory dialogs, educational content about mining and blockchain, system information displays, and user guidance materials. Missing description indicates file doesn't exist yet or contains untranslated content. Part of user education and help system in Indonesian language. |
| `mining-view.json` | Indonesian translations for mining interface and performance monitoring. Contains translations for: mining control buttons (start/stop mining), performance metrics displays (hashrate, temperature, utilization), mining status indicators, pool statistics, error messages for mining issues, and earnings calculations. Missing description indicates file doesn't exist yet or contains untranslated content. Critical for mining operations interface in Indonesian language. |
| `p2p.json` | Indonesian translations for P2Pool collaborative mining functionality. Contains translations for: pool connection management, shared mining statistics, squad/team formation, collaborative mining explanations, and P2Pool-specific terminology. Missing description indicates file doesn't exist yet or contains untranslated content. Part of the collaborative mining system in Indonesian language. |
| `paper-wallet.json` | Indonesian translations for paper wallet and mobile integration features. Contains translations for: wallet backup instructions, seed word management, QR code generation for mobile sync, security warnings, passphrase handling, and mobile app linking procedures. Missing description indicates file doesn't exist yet or contains untranslated content. Part of wallet security and mobile integration in Indonesian language. |
| `reconnect.json` | Indonesian translations for network reconnection and connectivity management. Contains translations for: connection retry mechanisms, network status indicators, reconnection progress updates, connectivity troubleshooting instructions, and offline/online state notifications. Missing description indicates file doesn't exist yet or contains untranslated content. Part of network reliability and connection management in Indonesian language. |
| `settings.json` | Indonesian translations for application settings and configuration management. Contains translations for: mining power controls (CPU/GPU settings), network configuration (Tor, bridges, connection), wallet management (import, reset, security), application preferences (theme, language, startup), experimental features, system information, and debug options. Missing description indicates file doesn't exist yet or contains untranslated content. Core configuration interface in Indonesian language. |
| `setup-progresses.json` | Indonesian translations for application setup progress indicators and initialization steps. Contains translations for: setup step descriptions, progress messages during configuration, dependency installation status, wallet creation flow, network connection establishment, and onboarding completion messages. Missing description indicates file doesn't exist yet or contains untranslated content. Part of initial setup and onboarding experience in Indonesian language. |
| `setup-view.json` | Indonesian translations for setup view interface and onboarding screens. Contains translations for: welcome messages, setup wizard navigation, configuration options, user agreements, initial mining configuration, security setup instructions, and setup completion notifications. Missing description indicates file doesn't exist yet or contains untranslated content. Part of user onboarding and initial configuration system in Indonesian language. |
| `sidebar.json` | Indonesian translations for sidebar navigation and menu system. Contains translations for: main navigation labels (mining, wallet, settings, airdrop), menu items, navigation tooltips, section headers, status indicators, and sidebar interaction elements. Missing description indicates file doesn't exist yet or contains untranslated content. Part of the main navigation interface in Indonesian language. |
| `sos.json` | Indonesian translations for SOS/emergency help and support functionality. Contains translations for: help requests, emergency contact information, critical error messages, support ticket submission, troubleshooting guidance, emergency recovery instructions, and user assistance features. Missing description indicates file doesn't exist yet or contains untranslated content. Part of emergency support and user assistance system in Indonesian language. |
| `staged-security.json` | Indonesian translations for staged security and wallet protection features. Contains translations for: multi-stage security setup, wallet encryption options, security warnings, backup verification steps, recovery procedures, security best practices, and protection protocols. Missing description indicates file doesn't exist yet or contains untranslated content. Part of wallet security and protection system in Indonesian language. |
| `tribes-view.json` | Indonesian translations for tribes/squad view and community mining features. Contains translations for: squad formation interface, group mining controls, community statistics displays, team earnings tracking, collaborative mining explanations, and social mining interactions. Missing description indicates file doesn't exist yet or contains untranslated content. Part of social and collaborative mining system in Indonesian language. |
| `wallet.json` | Indonesian translations for wallet functionality and transaction management. Contains translations for: wallet balance displays, transaction history, send/receive operations, transaction details, wallet sync status, address management, fee calculations, swap/exchange operations, and payment confirmations. Missing description indicates file doesn't exist yet or contains untranslated content. Critical for wallet interface in Indonesian language. |

##### public/locales/ja/

| File | Description |
|------|-------------|
| `airdrop.json` | Japanese translations for airdrop/rewards system. Contains translations for: gem claiming system, friend invitation mechanics, referral code functionality, modal dialogs for rewards, analytics permissions setup, social features (Twitter integration), bonus tier calculations, and gamification elements. Missing description indicates file doesn't exist yet or contains untranslated content. Part of the social mining and rewards system in Japanese language. |
| `bridge.json` | Japanese translations for bridge/exchange functionality. Contains translations for cross-chain operations, token swapping, exchange integrations, and cryptocurrency purchasing features. Missing description indicates file doesn't exist yet or contains untranslated content. Part of the exchange and cross-chain bridge system in Japanese language. |
| `common.json` | Japanese translations for common UI elements and shared vocabulary. Contains translations for: navigation controls (back, continue, close), action buttons (cancel, submit, restart), status indicators (pending, failed, complete), technical terms (hashrate, temperature, utilization), network terms (mainnet, testnet), and application-specific terminology. Missing description indicates file doesn't exist yet or contains untranslated content. Core interface vocabulary for Japanese language. |
| `components.json` | Japanese translations for UI components and system dialogs. Contains translations for: informational banners (network status, warnings), form validation messages, confirmation dialogs (mining mode changes), system modals (release notes, app resume), and detailed explanatory content (network warm-up explanations). Missing description indicates file doesn't exist yet or contains untranslated content. Essential for component-level interactions in Japanese language. |
| `exchange.json` | Japanese translations for exchange functionality and third-party integrations. Contains translations for: exchange selection modals, supported exchange information, mining-to-exchange workflows, and integration setup instructions. Missing description indicates file doesn't exist yet or contains untranslated content. Part of the cryptocurrency exchange integration system in Japanese language. |
| `external-dependency-dialog.json` | Japanese localization file for external dependency dialog UI strings. Contains translation keys for the external dependencies dialog including title, description, and download/install button text. Part of the i18n system supporting Japanese language users. Related to external dependency management workflow. |
| `info.json` | Japanese localization file for the info/onboarding flow. Contains multi-step tutorial content explaining Tari Universe welcome, what is Tari, mining concepts, testnet status, next steps, and community philosophy. Includes step headings and detailed explanations with emoji placeholders. Used in info modal/flow component. |
| `mining-view.json` | Japanese translations for mining interface and performance monitoring. Contains translations for: mining control buttons (start/stop mining), performance metrics displays (hashrate, temperature, utilization), mining status indicators, pool statistics, error messages for mining issues, and earnings calculations. Missing description indicates file doesn't exist yet or contains untranslated content. Critical for mining operations interface in Japanese language. |
| `p2p.json` | Japanese localization file for P2P networking and mining pool UI components. Contains translations for network connection status, P2Pool statistics, mining peer information, reward tracking, and network sync status. Includes mining stats display and connection details for P2Pool mining operations. |
| `paper-wallet.json` | Japanese localization file for paper wallet and mobile app connection features. Contains translations for QR code scanning, mobile app connection flow, wallet import instructions, and verification processes. Used for connecting Tari Universe desktop to mobile app via QR codes. |
| `reconnect.json` | Japanese localization file for network reconnection and connection issues UI. Contains translations for reconnection dialogs, network error messages, retry flows, and connection status indicators. Used when handling network disconnections and recovery. |
| `settings.json` | Japanese translations for application settings and configuration management. Contains translations for: mining power controls (CPU/GPU settings), network configuration (Tor, bridges, connection), wallet management (import, reset, security), application preferences (theme, language, startup), experimental features, system information, and debug options. Missing description indicates file doesn't exist yet or contains untranslated content. Core configuration interface in Japanese language. |
| `setup-progresses.json` | Japanese localization file for setup progress indicators and status messages. Contains translations for various setup stages, progress descriptions, and completion states during application initialization and configuration setup. |
| `setup-view.json` | Japanese localization file for application setup and initialization flow. Contains translations for setup progress states, network synchronization, binary downloads, dependency checking, auto-update flows, and initialization messages. Used during app startup and setup wizard. |
| `sidebar.json` | Japanese (ja) localization file for sidebar component translations. Contains 27 translation keys for sidebar UI elements including: auto-miner settings, mining schedules, wallet balance, block rewards, recent wins, and paper wallet synchronization. Includes nested 'share' object with social sharing functionality translations. Used by sidebar components for Japanese language support in the Tari Universe application. |
| `sos.json` | Japanese (ja) localization file for SOS (emergency/help) component translations. Contains translations for help documentation, emergency contacts, troubleshooting guides, and support resources. Used by SOS/help components for Japanese language support in the Tari Universe application. |
| `staged-security.json` | Japanese (ja) localization file for staged security component translations. Contains translations for security setup wizards, password requirements, backup procedures, and security validation steps. Used by security setup components for Japanese language support in the Tari Universe application. |
| `tribes-view.json` | Japanese (ja) localization file for tribes/community view component translations. Contains translations for community features, tribe management, social interactions, and group mining functionality. Used by tribes/community components for Japanese language support in the Tari Universe application. |
| `wallet.json` | Japanese localization file for wallet UI components. Contains translations for wallet tabs (send, receive, history), transaction details, balance displays, swap/exchange functionality, and transaction status messages. Includes complex transaction flow text and error messages for wallet operations. |

##### public/locales/ko/

| File | Description |
|------|-------------|
| `airdrop.json` | Korean (ko) localization file for airdrop game component translations. Contains 58 translation keys for airdrop features including: gem rewards system, referral codes, friend invitations, login/logout functionality, analytics permissions, and bonus tier rewards. Includes nested 'permission' and 'push-notifications' objects. Used by airdrop game components for Korean language support in the Tari Universe application. |
| `bridge.json` | Korean (ko) localization file for bridge component translations. Contains translations for cross-chain bridge functionality, token transfers, network switching, and bridge transaction statuses. Used by bridge/swap components for Korean language support in the Tari Universe application. |
| `common.json` | Korean (ko) localization file for common/shared component translations. Contains translations for frequently used UI elements, buttons, labels, error messages, and general application terms used across multiple components. Base translation file for Korean language support in the Tari Universe application. |
| `components.json` | Korean (ko) localization file for generic component translations. Contains translations for reusable UI components, form elements, modals, dialogs, and shared interface elements. Used by various components for Korean language support in the Tari Universe application. |
| `exchange.json` | Korean (ko) localization file for exchange/trading component translations. Contains translations for token exchange interfaces, trading pairs, market data, and transaction confirmations. Used by exchange/trading components for Korean language support in the Tari Universe application. |
| `external-dependency-dialog.json` | Korean (ko) localization file for external dependency dialog translations. Contains translations for dependency installation prompts, binary downloads, system requirements checks, and external tool setup dialogs. Used by dependency management components for Korean language support in the Tari Universe application. |
| `info.json` | Korean (ko) localization file for info/about component translations. Contains translations for application information, version details, credits, license information, and help documentation. Used by info/about components for Korean language support in the Tari Universe application. |
| `mining-view.json` | Korean (ko) localization file for mining view component translations. Contains translations for mining interface, hashrate displays, pool connections, GPU/CPU mining controls, and mining statistics. Used by mining view components for Korean language support in the Tari Universe application. |
| `p2p.json` | Korean (ko) localization file for P2P/peer-to-peer component translations. Contains translations for P2Pool mining, network connections, peer discovery, and distributed mining functionality. Used by P2P components for Korean language support in the Tari Universe application. |
| `paper-wallet.json` | Korean (ko) localization file for paper wallet component translations. Contains translations for paper wallet generation, mobile sync, QR codes, wallet backup procedures, and mobile app integration. Used by paper wallet components for Korean language support in the Tari Universe application. |
| `reconnect.json` | Korean (ko) localization file for reconnect component translations. Contains translations for network reconnection dialogs, connection error messages, retry mechanisms, and network status indicators. Used by reconnection components for Korean language support in the Tari Universe application. |
| `settings.json` | Korean (ko) localization file for settings component translations. Contains translations for application settings, preferences, configuration options, mining parameters, and system configurations. Used by settings components for Korean language support in the Tari Universe application. |
| `setup-progresses.json` | Korean (ko) localization file for setup progress component translations. Contains translations for installation progress indicators, setup steps, progress bars, and completion messages. Used by setup progress components for Korean language support in the Tari Universe application. |
| `setup-view.json` | Korean (ko) localization file for setup view component translations. Contains translations for initial application setup, onboarding wizard, configuration forms, and first-run experience. Used by setup view components for Korean language support in the Tari Universe application. |
| `sidebar.json` | Korean (ko) localization file for sidebar component translations. Contains translations for sidebar UI elements including auto-miner settings, mining schedules, wallet balance, block rewards, recent wins, and paper wallet synchronization. Used by sidebar components for Korean language support in the Tari Universe application. |
| `sos.json` | Korean (ko) localization file for SOS (emergency/help) component translations. Contains translations for help documentation, emergency contacts, troubleshooting guides, and support resources. Used by SOS/help components for Korean language support in the Tari Universe application. |
| `staged-security.json` | Korean (ko) localization file for staged security component translations. Contains translations for security setup wizards, password requirements, backup procedures, and security validation steps. Used by security setup components for Korean language support in the Tari Universe application. |
| `tribes-view.json` | Korean (ko) localization file for tribes/community view component translations. Contains translations for community features, tribe management, social interactions, and group mining functionality. Used by tribes/community components for Korean language support in the Tari Universe application. |
| `wallet.json` | Korean (ko) localization file for wallet component translations. Contains translations for wallet operations, balance displays, transaction history, send/receive forms, and wallet management features. Used by wallet components for Korean language support in the Tari Universe application. |

##### public/locales/pl/

| File | Description |
|------|-------------|
| `airdrop.json` | Polish (pl) localization file for airdrop game component translations. Contains translations for gem rewards system, referral codes, friend invitations, login/logout functionality, analytics permissions, and bonus tier rewards. Used by airdrop game components for Polish language support in the Tari Universe application. |
| `bridge.json` | Polish (pl) localization file for bridge component translations. Contains translations for cross-chain bridge functionality, token transfers, network switching, and bridge transaction statuses. Used by bridge/swap components for Polish language support in the Tari Universe application. |
| `common.json` | Polish (pl) localization file for common/shared component translations. Contains translations for frequently used UI elements, buttons, labels, error messages, and general application terms used across multiple components. Base translation file for Polish language support in the Tari Universe application. |
| `components.json` | Polish (pl) localization file for generic component translations. Contains translations for reusable UI components, form elements, modals, dialogs, and shared interface elements. Used by various components for Polish language support in the Tari Universe application. |
| `exchange.json` | Polish (pl) localization file for exchange/trading component translations. Contains translations for token exchange interfaces, trading pairs, market data, and transaction confirmations. Used by exchange/trading components for Polish language support in the Tari Universe application. |
| `external-dependency-dialog.json` | Polish (pl) localization file for external dependency dialog translations. Contains translations for dependency installation prompts, binary downloads, system requirements checks, and external tool setup dialogs. Used by dependency management components for Polish language support in the Tari Universe application. |
| `info.json` | Polish (pl) localization file for info/about component translations. Contains translations for application information, version details, credits, license information, and help documentation. Used by info/about components for Polish language support in the Tari Universe application. |
| `mining-view.json` | Polish (pl) localization file for mining view component translations. Contains translations for mining interface, hashrate displays, pool connections, GPU/CPU mining controls, and mining statistics. Used by mining view components for Polish language support in the Tari Universe application. |
| `p2p.json` | Polish (pl) localization file for P2P/peer-to-peer component translations. Contains translations for P2Pool mining, network connections, peer discovery, and distributed mining functionality. Used by P2P components for Polish language support in the Tari Universe application. |
| `paper-wallet.json` | Polish (pl) localization file for paper wallet component translations. Contains translations for paper wallet generation, mobile sync, QR codes, wallet backup procedures, and mobile app integration. Used by paper wallet components for Polish language support in the Tari Universe application. |
| `reconnect.json` | Polish (pl) localization file for reconnect component translations. Contains translations for network reconnection dialogs, connection error messages, retry mechanisms, and network status indicators. Used by reconnection components for Polish language support in the Tari Universe application. |
| `settings.json` | Polish (pl) localization file for settings component translations. Contains translations for application settings, preferences, configuration options, mining parameters, and system configurations. Used by settings components for Polish language support in the Tari Universe application. |
| `setup-progresses.json` | Polish (pl) localization file for setup progress component translations. Contains translations for installation progress indicators, setup steps, progress bars, and completion messages. Used by setup progress components for Polish language support in the Tari Universe application. |
| `setup-view.json` | Polish (pl) localization file for setup view component translations. Contains translations for initial application setup, onboarding wizard, configuration forms, and first-run experience. Used by setup view components for Polish language support in the Tari Universe application. |
| `sidebar.json` | Polish (pl) localization file for sidebar component translations. Contains translations for sidebar UI elements including auto-miner settings, mining schedules, wallet balance, block rewards, recent wins, and paper wallet synchronization. Used by sidebar components for Polish language support in the Tari Universe application. |
| `sos.json` | Polish (pl) localization file for SOS (emergency/help) component translations. Contains translations for help documentation, emergency contacts, troubleshooting guides, and support resources. Used by SOS/help components for Polish language support in the Tari Universe application. |
| `staged-security.json` | Polish (pl) localization file for staged security component translations. Contains translations for security setup wizards, password requirements, backup procedures, and security validation steps. Used by security setup components for Polish language support in the Tari Universe application. |
| `tribes-view.json` | Polish (pl) localization file for tribes/community view component translations. Contains translations for community features, tribe management, social interactions, and group mining functionality. Used by tribes/community components for Polish language support in the Tari Universe application. |
| `wallet.json` | Polish (pl) localization file for wallet component translations. Contains translations for wallet operations, balance displays, transaction history, send/receive forms, and wallet management features. Used by wallet components for Polish language support in the Tari Universe application. |

##### public/locales/ru/

| File | Description |
|------|-------------|
| `airdrop.json` | Russian (ru) localization file for airdrop game component translations. Contains translations for gem rewards system, referral codes, friend invitations, login/logout functionality, analytics permissions, and bonus tier rewards. Used by airdrop game components for Russian language support in the Tari Universe application. |
| `bridge.json` | Russian (ru) localization file for bridge component translations. Contains translations for cross-chain bridge functionality, token transfers, network switching, and bridge transaction statuses. Used by bridge/swap components for Russian language support in the Tari Universe application. |
| `common.json` | Russian (ru) localization file for common/shared component translations. Contains translations for frequently used UI elements, buttons, labels, error messages, and general application terms used across multiple components. Base translation file for Russian language support in the Tari Universe application. |
| `components.json` | Russian (ru) localization file for generic component translations. Contains translations for reusable UI components, form elements, modals, dialogs, and shared interface elements. Used by various components for Russian language support in the Tari Universe application. |
| `exchange.json` | Russian (ru) localization file for exchange/trading component translations. Contains translations for token exchange interfaces, trading pairs, market data, and transaction confirmations. Used by exchange/trading components for Russian language support in the Tari Universe application. |
| `external-dependency-dialog.json` | Russian (ru) localization file for external dependency dialog translations. Contains translations for dependency installation prompts, binary downloads, system requirements checks, and external tool setup dialogs. Used by dependency management components for Russian language support in the Tari Universe application. |
| `info.json` | Russian (ru) localization file for info/about component translations. Contains translations for application information, version details, credits, license information, and help documentation. Used by info/about components for Russian language support in the Tari Universe application. |
| `mining-view.json` | Russian (ru) localization file for mining view component translations. Contains translations for mining interface, hashrate displays, pool connections, GPU/CPU mining controls, and mining statistics. Used by mining view components for Russian language support in the Tari Universe application. |
| `p2p.json` | Russian (ru) localization file for P2P/peer-to-peer component translations. Contains translations for P2Pool mining, network connections, peer discovery, and distributed mining functionality. Used by P2P components for Russian language support in the Tari Universe application. |
| `paper-wallet.json` | Russian (ru) localization file for paper wallet component translations. Contains translations for paper wallet generation, mobile sync, QR codes, wallet backup procedures, and mobile app integration. Used by paper wallet components for Russian language support in the Tari Universe application. |
| `reconnect.json` | Russian (ru) localization file for reconnect component translations. Contains translations for network reconnection dialogs, connection error messages, retry mechanisms, and network status indicators. Used by reconnection components for Russian language support in the Tari Universe application. |
| `settings.json` | Russian (ru) localization file for settings component translations. Contains translations for application settings, preferences, configuration options, mining parameters, and system configurations. Used by settings components for Russian language support in the Tari Universe application. |
| `setup-progresses.json` | Russian (ru) localization file for setup progress component translations. Contains translations for installation progress indicators, setup steps, progress bars, and completion messages. Used by setup progress components for Russian language support in the Tari Universe application. |
| `setup-view.json` | Russian (ru) localization file for setup view component translations. Contains translations for initial application setup, onboarding wizard, configuration forms, and first-run experience. Used by setup view components for Russian language support in the Tari Universe application. |
| `sidebar.json` | Russian (ru) localization file for sidebar component translations. Contains translations for sidebar UI elements including auto-miner settings, mining schedules, wallet balance, block rewards, recent wins, and paper wallet synchronization. Used by sidebar components for Russian language support in the Tari Universe application. |
| `sos.json` | Russian (ru) localization file for SOS (emergency/help) component translations. Contains translations for help documentation, emergency contacts, troubleshooting guides, and support resources. Used by SOS/help components for Russian language support in the Tari Universe application. |
| `staged-security.json` | Russian (ru) localization file for staged security component translations. Contains translations for security setup wizards, password requirements, backup procedures, and security validation steps. Used by security setup components for Russian language support in the Tari Universe application. |
| `tribes-view.json` | Russian (ru) localization file for tribes/community view component translations. Contains translations for community features, tribe management, social interactions, and group mining functionality. Used by tribes/community components for Russian language support in the Tari Universe application. |
| `wallet.json` | Russian (ru) localization file for wallet component translations. Contains translations for wallet operations, balance displays, transaction history, send/receive forms, and wallet management features. Used by wallet components for Russian language support in the Tari Universe application. |

##### public/locales/tr/

| File | Description |
|------|-------------|
| `airdrop.json` | Turkish (tr) localization file for airdrop game component translations. Contains translations for gem rewards system, referral codes, friend invitations, login/logout functionality, analytics permissions, and bonus tier rewards. Used by airdrop game components for Turkish language support in the Tari Universe application. |
| `bridge.json` | Turkish (tr) localization file for bridge component translations. Contains translations for cross-chain bridge functionality, token transfers, network switching, and bridge transaction statuses. Used by bridge/swap components for Turkish language support in the Tari Universe application. |
| `common.json` | Turkish (tr) localization file for common/shared component translations. Contains translations for frequently used UI elements, buttons, labels, error messages, and general application terms used across multiple components. Base translation file for Turkish language support in the Tari Universe application. |
| `components.json` | Turkish (tr) localization file for generic component translations. Contains translations for reusable UI components, form elements, modals, dialogs, and shared interface elements. Used by various components for Turkish language support in the Tari Universe application. |
| `exchange.json` | Turkish (tr) localization file for exchange/trading component translations. Contains translations for token exchange interfaces, trading pairs, market data, and transaction confirmations. Used by exchange/trading components for Turkish language support in the Tari Universe application. |
| `external-dependency-dialog.json` | Turkish (tr) localization file for external dependency dialog translations. Contains translations for dependency installation prompts, binary downloads, system requirements checks, and external tool setup dialogs. Used by dependency management components for Turkish language support in the Tari Universe application. |
| `info.json` | Turkish (tr) localization file for info/about component translations. Contains translations for application information, version details, credits, license information, and help documentation. Used by info/about components for Turkish language support in the Tari Universe application. |
| `mining-view.json` | Turkish (tr) localization file for mining view component translations. Contains translations for mining interface, hashrate displays, pool connections, GPU/CPU mining controls, and mining statistics. Used by mining view components for Turkish language support in the Tari Universe application. |
| `p2p.json` | Turkish (tr) localization file for P2P/peer-to-peer component translations. Contains translations for P2Pool mining, network connections, peer discovery, and distributed mining functionality. Used by P2P components for Turkish language support in the Tari Universe application. |
| `paper-wallet.json` | Turkish (tr) localization file for paper wallet component translations. Contains translations for paper wallet generation, mobile sync, QR codes, wallet backup procedures, and mobile app integration. Used by paper wallet components for Turkish language support in the Tari Universe application. |
| `reconnect.json` | Turkish (tr) localization file for reconnect component translations. Contains translations for network reconnection dialogs, connection error messages, retry mechanisms, and network status indicators. Used by reconnection components for Turkish language support in the Tari Universe application. |
| `settings.json` | Turkish (tr) localization file for settings component translations. Contains translations for application settings, preferences, configuration options, mining parameters, and system configurations. Used by settings components for Turkish language support in the Tari Universe application. |
| `setup-progresses.json` | Turkish (tr) localization file for setup progress component translations. Contains translations for installation progress indicators, setup steps, progress bars, and completion messages. Used by setup progress components for Turkish language support in the Tari Universe application. |
| `setup-view.json` | Turkish (tr) localization file for setup view component translations. Contains translations for initial application setup, onboarding wizard, configuration forms, and first-run experience. Used by setup view components for Turkish language support in the Tari Universe application. |
| `sidebar.json` | Turkish (tr) localization file for sidebar component translations. Contains translations for sidebar UI elements including auto-miner settings, mining schedules, wallet balance, block rewards, recent wins, and paper wallet synchronization. Used by sidebar components for Turkish language support in the Tari Universe application. |
| `sos.json` | Turkish (tr) localization file for SOS (emergency/help) component translations. Contains translations for help documentation, emergency contacts, troubleshooting guides, and support resources. Used by SOS/help components for Turkish language support in the Tari Universe application. |
| `staged-security.json` | Turkish (tr) localization file for staged security component translations. Contains translations for security setup wizards, password requirements, backup procedures, and security validation steps. Used by security setup components for Turkish language support in the Tari Universe application. |
| `tribes-view.json` | Turkish (tr) localization file for tribes/community view component translations. Contains translations for community features, tribe management, social interactions, and group mining functionality. Used by tribes/community components for Turkish language support in the Tari Universe application. |
| `wallet.json` | Turkish (tr) localization file for wallet component translations. Contains translations for wallet operations, balance displays, transaction history, send/receive forms, and wallet management features. Used by wallet components for Turkish language support in the Tari Universe application. |

### scripts/

| File | Description |
|------|-------------|
| `check-get-yq.sh` | Bash script for automatic yq installation and dependency management. Checks if yq (YAML processor) is installed, and if not, downloads and installs the specified version (4.44.3) for the target platform (amd64/arm64) and OS (linux). Downloads from GitHub releases and installs to /usr/local/bin. Uses environment variables for platform/version configuration. Dependencies: wget, tar. Used by: CI/CD workflows, YAML processing tasks in build pipeline. |
| `create-latest-json.sh` | Comprehensive release automation script for GitHub workflows. Creates latest.json file with platform-specific download information, signatures, and URLs for multi-platform Tauri app distribution. Processes artifacts for linux-x86_64, linux-aarch64, darwin-aarch64, darwin-x86_64, and windows-x86_64 platforms. Extracts archives, validates signatures, generates updater manifest with version, publication date, platform-specific URLs and signatures. Used by: .github/workflows/release-exchange.yml, auto-updater system. Dependencies: jq, unzip, tar, find. Essential for app distribution and auto-update functionality. |
| `file_license_check.sh` | License compliance checker script for copyright validation. Uses ripgrep to find files without 'Copyright.*The Tari Project' headers, compares against .license.ignore whitelist, and reports licensing violations. Excludes specific file types (Dockerfile, config files, documentation, etc.) and validates copyright compliance across the codebase. Ensures all source files have proper Tari Project copyright notices. Dependencies: ripgrep (rg), diff. Used by: CI/CD license validation, development workflows, legal compliance checks. |
| `i18n_checker.py` | Python internationalization validation tool for locale management. Provides 'compare' and 'unused' modes: compares translation keys across locales to find missing/extraneous keys, and identifies unused translation keys by searching source code. Generates CSV reports for translation status, missing keys per locale, and unused keys. Supports recursive file scanning and UTF-8 encoding. Essential for maintaining translation completeness across 12 supported languages. Dependencies: json, csv, argparse, os modules. Used by: translation workflows, locale validation, i18n maintenance. |
| `inject_license.sh` | Automated license header injection script for Rust source files. Recursively processes src-tauri/src/ directory and prepends BSD-3-Clause copyright notice to files missing license headers. Checks for existing license blocks before injection to avoid duplication. Ensures all Rust backend files comply with Tari Project licensing requirements. Dependencies: grep, bash file operations. Used by: license compliance, development setup, source file preparation. |
| `translator.js` | Node.js translation management tool for i18next locale files. Provides two modes: cleanup/formatting of translation files, and key-value updates across all locales. Merges locale files with English defaults, sorts keys alphabetically, handles nested object updates via dot notation, and ensures translation consistency across 12 languages. Supports nested value setting and deep object merging. Essential for maintaining i18next translation infrastructure. Dependencies: fs, path, process modules. Used by: npm run translate scripts, translation workflows, locale maintenance. Example: 'npm run translate common greeting.hello=\"Hello!\"' |

### src/

| File | Description |
|------|-------------|
| `i18initializer.ts` | i18next internationalization setup for Tari Universe. Supports 12 languages: English (default), Polish, Afrikaans, Turkish, Chinese, Hindi, Indonesian, Japanese, Korean, Russian, French, German. Includes Language enum, regional variant resolution function (e.g., zh-CN → cn), and LanguageList with native language names. Configures HttpBackend for loading translations from /locales/{{lng}}/{{ns}}.json pattern, with browser language detection and fallback to English/common namespace. |
| `main.tsx` | Frontend application entry point. Imports i18n initialization, renders React app with StrictMode and Suspense. Performs WebGL support detection on DOMContentLoaded (logs error if unsupported). Creates root element and renders AppWrapper component. Dependencies: React DOM createRoot, AppWrapper component, i18initializer. Critical path: DOM ready -> WebGL check -> React render. |
| `styled.d.ts` | TypeScript declaration file extending styled-components DefaultTheme interface for Tari Universe theming system. Integrates ThemePalette and ThemeComponents types to provide comprehensive theme typing including mode (light/dark), color palette, alpha colors, and gradients. Enables TypeScript intellisense and type safety for styled-components throughout the application. Critical for maintaining type safety in theme-aware components and ensuring consistent theming API across all styled components in the React application. |
| `vite-env-override.d.ts` | TypeScript module declaration overrides for Vite environment in Tari Universe. Declares SVG imports as React functional components with SVG properties, enabling TypeScript recognition of SVG files as React components. Allows importing SVG files directly as components for use in React interface. Part of Vite TypeScript configuration enabling proper SVG handling and component integration throughout the application. |
| `vite-env.d.ts` | Vite environment types declaration for Tari Universe frontend. References vite-env-override.d.ts for custom environment overrides and vite/client for standard Vite TypeScript support. Enables TypeScript recognition of Vite-specific features including import.meta.env, import.meta.hot, and asset imports. Essential for TypeScript integration with Vite development server and build system. |

### src-tauri/

| File | Description |
|------|-------------|
| `.gitignore` | Git ignore file for the Rust/Tauri backend project. Excludes compiled Rust artifacts (/target/) and Tauri-generated schema files (/gen/schemas) used for capability auto-completion. Minimal configuration focusing on build outputs and generated code. |
| `Cargo.lock` | [GENERATED] Cargo dependency lock file automatically generated by Rust's package manager. Contains exact versions of all dependencies and transitive dependencies for reproducible builds. Over 600 crates including cryptography (aes, ring), async runtime (tokio), GUI framework (tauri), and Tari blockchain libraries. Critical for ensuring consistent builds across environments. |
| `Cargo.toml` | Rust backend configuration for Tauri app v1.2.9. Key dependencies: Tauri 2.x with isolation/tray/devtools features, Tari blockchain libraries (v4.4.0-rc.0), mining (nvml-wrapper for GPU), networking (axum, tokio, tor), security (keyring, ring), data (sqlite, serde). Platform-specific: Windows (planif, winreg), all (bundled OpenSSL). Features: airdrop-env, telemetry-env, custom-protocol for production. Release profile: debug symbols enabled. Build deps: tauri-build with isolation. |
| `build.rs` | Tauri build script for platform-specific configuration and Windows privilege elevation. Dependencies: tauri_build for build process integration. Used by: Cargo build system during compilation. Configures Windows manifest to require Administrator privileges for handling firewall prompts and system-level mining operations. Enables tokio_unstable features and sets up Windows Common Controls dependency. Critical for proper Windows installation and mining binary execution with elevated permissions. Handles cross-platform build differences between Windows (with admin requirements) and Unix systems. |
| `config.json` | GPU mining configuration template for Tari mining operations. Specifies default Tari address, node URL (localhost:18142), coinbase extra data (tari_gpu_miner), template refresh interval (30s), P2Pool settings, HTTP server configuration (port 18000), and GPU utilization (100%). Used by: GPU mining components for initial setup and configuration. Provides baseline settings for local mining node integration and mining parameter defaults. Modified during application setup based on user preferences and hardware detection. |
| `lints.toml` | Clippy linting configuration for Rust code quality enforcement. Defines strict linting rules including: correctness checks, style enforcement, performance optimizations, and security guidelines. Denies: unwrap_used, dbg_macro, dead_code, unused imports, and casting issues. Allows: too_many_arguments, default_trait_access for practical development. Used by: Cargo build system and CI/CD pipelines for code quality validation. Critical for maintaining high code quality standards, preventing common Rust pitfalls, and ensuring consistent coding practices across the project. |
| `rust-toolchain` | Rust toolchain specification file defining stable channel as default compiler version. Ensures consistent Rust version across development environments and CI/CD builds. Critical for maintaining compatibility with Tauri framework and Tari blockchain dependencies. |
| `tauri.conf.json` | Main Tauri application configuration file defining app metadata, build settings, security policies, and bundling options. Configures product name 'Tari Universe (Alpha)', bundle identifier, icon resources, and platform-specific settings. Defines security capabilities, CSP policies allowing connections to Tari services and WalletConnect. Includes updater configuration with GitHub endpoints, window settings (1300x731 minimum size), and CLI argument support for wallet import. Critical configuration file for Tauri app compilation and runtime behavior. |

#### src-tauri/beta-icons/

| File | Description |
|------|-------------|
| `icon.icns` | macOS application icon for beta releases. ICNS format bundle containing multiple icon sizes (16x16 to 1024x1024) optimized for Retina displays. Used during beta testing phases to distinguish from production releases. Contains Tari Universe branding with beta indicators for development and testing builds. |
| `systray_icon.icns` | macOS system tray icon for beta releases. ICNS format for menu bar display in various sizes and color modes (light/dark theme). Provides visual distinction for beta builds in system tray area. Used by system tray manager for beta testing identification and status indication. |

#### src-tauri/binaries-versions/

| File | Description |
|------|-------------|
| `binaries_versions_mainnet.json` | [GENERATED] Binary version constraints for mainnet network operations. Specifies exact versions for all external dependencies: XMRig (6.22.0), MMProxy (4.4.1), Minotari Node (4.4.1), Wallet (4.4.1), SHA-P2Pool (1.0.3), Glytex GPU miner (0.2.26), and Tor (14.5.1). Used by: BinaryManager for version validation and compatibility checking. Critical for network stability and security - ensures all miners use compatible binary versions. Generated by release engineering process and updated with each network upgrade. |
| `binaries_versions_nextnet.json` | [GENERATED] Binary version constraints for nextnet (development) network operations. Specifies versions for testing: XMRig (6.22.0), MMProxy (4.4.1-rc.0), Minotari Node (4.4.1-rc.0), Wallet (4.4.1-rc.0), SHA-P2Pool (1.0.0), Glytex (0.2.26), and Tor (14.5.1). Used by: BinaryManager for pre-release version validation. Allows release candidate versions for testing new features before mainnet deployment. Critical for development network stability and feature validation. Updated independently from mainnet versions. |
| `binaries_versions_testnets.json` | External binary version configuration for testnet deployments. Specifies exact versions for mining components (xmrig=6.22.0, glytex=0.2.26), Tari network infrastructure (minotari_node, wallet, mmproxy=4.4.1-pre.0), P2Pool mining (sha-p2pool=1.0.0), and privacy networking (tor=14.5.1). Used by binaries resolver to download compatible versions for testnet environments. |

#### src-tauri/capabilities/

| File | Description |
|------|-------------|
| `default.json` | Tauri security capabilities configuration for main and splash screen windows. Grants core permissions (filesystem, events, window management, resources, menus, system tray), shell access for external links, clipboard management, process controls, and HTTP requests to localhost. Includes Sentry error reporting permissions. Central security boundary defining what the frontend can access. |
| `desktop.json` | Cross-platform desktop capability configuration for Tauri app. Enables auto-updater functionality across macOS, Windows, and Linux platforms for main and splash screen windows. Minimal focused configuration specifically for software update mechanisms. |
| `migrated.json` | Tauri v1 to v2 migration capability configuration. Local-only permissions including core defaults, auto-updater, and OS-level access for main and splash screen windows. Maintains compatibility during framework version transition while providing essential system integration capabilities. |

##### src-tauri/gen/schemas/

| File | Description |
|------|-------------|
| `acl-manifests.json` | [GENERATED] Tauri v2 Access Control List (ACL) manifests schema file. Auto-generated by Tauri build process to define permission manifests for app capabilities and security boundaries. Contains JSON schema definitions for application permissions, command access controls, and IPC layer security policies. Used by Tauri's security system to validate and enforce capability-based permissions during runtime. Should not be manually edited as it gets regenerated during builds. |
| `capabilities.json` | [GENERATED] Tauri v2 capabilities configuration file defining application permissions and window access controls. Contains three main capabilities: 'default' (basic app functionality), 'desktop-capability' (platform-specific permissions), and 'migrated' (legacy v1 permissions). Specifies which windows can access core functionalities like path operations, events, HTTP requests, shell commands, clipboard access, and system tray. Auto-generated during build process to ensure proper IPC security boundaries. |
| `desktop-schema.json` | [GENERATED] JSON Schema definition for Tauri v2 capability files format. Defines the structure and validation rules for capability configuration files including permissions, scopes, identifiers, and platform targeting. Contains comprehensive schema definitions for HTTP, shell, and other plugin permission systems. Used by development tools and build process to validate capability files and ensure proper security configuration. Part of Tauri's capability-based security model. |
| `macOS-schema.json` | [GENERATED] Platform-specific JSON Schema for Tauri v2 capabilities on macOS. Identical to desktop-schema.json but targeted for macOS platform builds. Defines capability file format with permission identifiers, scope definitions, and validation rules for HTTP, shell, and core Tauri plugins. Used during macOS-specific build processes to ensure proper permission configuration and security boundaries for Apple platform deployment. |

#### src-tauri/icons/

| File | Description |
|------|-------------|
| `icon.icns` | macOS application icon for production releases. ICNS format bundle with full resolution range (16x16 to 1024x1024) optimized for all macOS display types. Primary application icon used for production builds, app bundles, and distribution. Contains official Tari Universe branding for release versions. |
| `systray_icon.icns` | macOS system tray icon for production releases. ICNS format optimized for menu bar display with theme-adaptive design. Used by system tray manager for status indication, background mining alerts, and quick access menu. Critical for user experience when application runs in background mining mode. |

#### src-tauri/log4rs/

| File | Description |
|------|-------------|
| `base_node_sample.yml` | Log4rs configuration template for Minotari base node logging. Defines structured logging with multiple appenders: stdout (WARN+ to console), network.log (P2P/comms traffic), base_layer.log (application logic), grpc.log (gRPC communication), messages.log (message logging), and other.log (third-party crates). Uses rolling file policies with 2MB size limits and retention of 2 files. Configures separate loggers for different Tari components (minotari, comms, p2p, wallet) with appropriate log levels. Template uses {{log_dir}} placeholder replaced at runtime. |
| `proxy_sample.yml` | Log4rs configuration template for merge mining proxy logging. Defines dual logging to stdout (WARN+ level) and rolling file proxy.log (DEBUG+ level) with 5MB size limit and 3 file retention. Includes specific loggers for HTTP components (h2, hyper) and HTML parsing (html5ever, selectors) with WARN/ERROR levels to reduce noise. Simple configuration compared to base node logging, focusing on proxy-specific operations and external service communications. Template uses {{log_dir}} placeholder. |
| `spend_wallet_sample.yml` | Log4rs configuration template for Tari spend wallet logging. Configures logging output for wallet operations including transaction processing, key management, and wallet state changes. Similar structure to other Tari component logging configs with rolling file appenders and level-based filtering. Used by wallet processes to maintain audit trails and debug information for spending operations and wallet management functions. |
| `universe_sample.yml` | Sample logging configuration for Universe application. YAML configuration defining log levels, appenders, and output formats for the main application components. Includes console and file outputs with rotation policies. Template for customizing logging behavior during development and production deployment. Used by logging_utils for setup_logging() initialization. |
| `wallet_sample.yml` | Log4rs configuration template for standard Tari wallet logging. Defines logging structure for wallet operations including balance queries, transaction creation, address generation, and wallet synchronization. Uses rolling file appenders with size-based rotation to manage log files. Configures appropriate log levels for wallet components, gRPC communication, and underlying Tari libraries. Template file with {{log_dir}} placeholder for runtime path substitution. |

#### src-tauri/src/

| File | Description |
|------|-------------|
| `ab_test_selector.rs` | A/B testing framework selector for experimental features in Tari Universe. Exports: ABTestSelector enum with GroupA and GroupB variants. Dependencies: serde for serialization, std::fmt for display formatting. Used by: Configuration system and feature flag management to enable controlled rollouts of new features. Implements Display trait for user-friendly group naming. Part of the experimental features system for testing UI changes, mining optimizations, or other functionality with subset of users. |
| `airdrop.rs` | Airdrop system for tracking mining rewards and communicating with airdrop API services. Exports: AirdropAccessToken, AirdropMinedBlockMessage structs, JWT validation functions, and mined block notification system. Dependencies: jsonwebtoken for JWT handling, sha2 for hashing, reqwest for HTTP requests. Used by: Mining system to report newly mined blocks to airdrop service. Handles JWT token validation with expiration checking, wallet view key hashing for privacy, and async communication with external airdrop API endpoints. Critical for reward distribution and mining incentive programs. |
| `app_in_memory_config.rs` | Dynamic application configuration management for Tari Universe with exchange-specific settings. Manages runtime configuration including airdrop API endpoints, telemetry URLs, and bridge backend integration. Features DER-encoded public key generation for secure authentication and ExchangeMiner configuration for different mining environments. Handles environment-specific settings with feature-flag controlled compilation for different deployment targets (airdrop-env, telemetry-env). Provides DEFAULT_EXCHANGE_ID and EXCHANGE_ID constants for multi-exchange support. Critical for runtime configuration management, secure API authentication, and environment-specific deployment strategies. |
| `auto_launcher.rs` | Cross-platform auto-launcher service for starting Tari Universe on system boot. Exports: AutoLauncher singleton with platform-specific startup management. Dependencies: auto_launch crate, planif for Windows Task Scheduler, dunce for path handling. Used by: App configuration system to enable/disable startup behavior. Implements different strategies per OS: Launch Agent on macOS, startup entries on Linux, and Task Scheduler with admin privileges on Windows. Handles auto-launch state synchronization, permission management, and platform-specific edge cases like macOS dual instance prevention. |
| `commands.rs` | Tauri command handlers for frontend-backend communication in Tari Universe. Exports 100+ functions for mining control (CPU/GPU), wallet operations, configuration management, external dependencies, setup phases, and system utilities. Key commands include start/stop mining, wallet balance retrieval, transaction sending, tapplet management, and hardware detection. Uses MAX_ACCEPTABLE_COMMAND_TIME for performance monitoring. Handles Tari address validation, Monero integration, and credential management. Central interface between React frontend and Rust backend. |
| `consts.rs` | Application constants for Tari Universe backend. Defines platform-specific constants including PROCESS_CREATION_NO_WINDOW for Windows process creation without console windows. Contains DEFAULT_MONERO_ADDRESS constant for Monero mining pool integration and fallback scenarios. Provides centralized constant definitions used throughout the Rust backend for consistent behavior across different platforms and mining configurations. Important for maintaining application-wide constants and platform-specific optimizations. |
| `cpu_miner.rs` | CPU mining implementation using XMRig for Tari Universe. Manages XMRig process lifecycle, configuration generation, performance monitoring, and earnings estimation. Supports connection types: BuiltInProxy, Pool, MergeMinedPool. Handles eco mode (30% CPU usage), automatic restart on failure, and integration with mining status manager. Communicates with P2Pool/MM Proxy via XmrigAdapter and processes mining statistics. Includes process stats collection and graceful shutdown handling. |
| `credential_manager.rs` | Secure credential storage using platform keyring integration. Manages Credential struct with SafePassword for Tari seed phrases and Monero seed bytes. Provides store_credentials(), retrieve_credentials(), and remove_credentials() with OS-native keyring storage (Keychain on macOS, Windows Credential Manager, Secret Service on Linux). Handles fallback file storage and migration. Critical security component for wallet secrets. |
| `download_utils.rs` | File extraction and validation utilities for binary downloads and archive handling. Exports: extract() for multi-format support (zip, tar.gz, tgz), set_permissions() for Unix executable rights, validate_checksum() for SHA256 verification. Dependencies: async-zip for ZIP handling, flate2/tar for gzip archives, sha2 for checksums. Used by: BinaryManager for secure binary installation and XMRig/GPU miner setup. Implements path sanitization, async extraction, and cross-platform permission management. Critical for secure binary deployment and installation integrity. |
| `events.rs` | Event system definitions for Tari Universe backend-frontend communication. Defines comprehensive EventType enum covering wallet updates (address, balance), blockchain events (base node, new blocks), mining status (CPU/GPU updates, pool status), hardware detection (GPU devices, engines), and application lifecycle (setup phases, splash screen, errors). Includes event payload structures for data transmission with proper serialization support. Provides connection status, transaction updates, and system monitoring events. Critical for real-time communication between Rust backend and React frontend enabling responsive UI updates and status synchronization throughout the application. |
| `events_emitter.rs` | Central event emission system for real-time communication between Rust backend and React frontend. Exports: EventsEmitter singleton with 40+ event emission methods for mining status, wallet updates, configuration changes, setup phases, and system alerts. Dependencies: Tauri event system, frontend ready channel for synchronization. Used by: All backend modules to notify frontend of state changes. Implements type-safe event emission with payload validation, frontend readiness checking, and error handling. Critical for reactive UI updates and real-time mining/wallet status display. |
| `events_manager.rs` | Central event handling coordinator for blockchain and application events. Manages handle_new_block_height() for blockchain synchronization, processes mining rewards, coordinates airdrop notifications, and updates node status. Integrates with wallet manager, task trackers, and EventsEmitter for real-time updates. Handles cross-component communication and state synchronization during mining operations. |
| `external_dependencies.rs` | External dependency detection and management for Tari Universe. Identifies and validates system-installed software required for optimal mining performance including Graphics drivers (NVIDIA, AMD), C++ redistributables, GPU utilities, and system libraries. Features platform-specific detection logic: Windows registry scanning, binary path resolution, and version checking. Provides ExternalDependencyStatus tracking (Installed, NotInstalled, Unknown) and dependency download capabilities for missing components. Includes manufacturer information tracking and required dependency definitions. Critical for ensuring users have proper drivers and libraries for GPU mining, preventing mining failures due to missing dependencies, and providing guided installation recommendations. |
| `feedback.rs` | User feedback and crash report submission system. Creates ZIP archives of log files, configuration data, and system information for technical support. Implements collect_feedback() for gathering diagnostic data and send_compressed_logs() for secure upload to support endpoints. Includes log file sanitization, file size limits, and privacy-preserving data collection. Used for troubleshooting and issue reporting. |
| `gpu_miner.rs` | GPU mining implementation for Tari Universe supporting multiple engines (Sha3x, RandomX, Blake3). Manages GPU miner binary lifecycle, device detection via GpuStatusFile, and configuration based on GpuThreads settings. Integrates with GpuMinerAdapter for process control and performance monitoring. Handles earnings estimation, automatic restarts, and graceful shutdown. Supports various mining modes and node connection sources for flexible deployment scenarios. |
| `gpu_miner_adapter.rs` | GPU mining adapter implementation for Tari Universe managing GPU mining software lifecycle. Handles different GPU node sources (local MMProxy, P2Pool, remote pools), engine type selection, and GPU device configuration. Implements ProcessAdapter for GPU miner process management with health monitoring and status tracking. Features GpuMinerStatus tracking including hashrate, accepted shares, and device-specific metrics. Supports multiple mining modes (Eco, Ludicrous, Custom) with configurable GPU thread allocation per device. Includes Windows-specific firewall rule management for mining software. Critical for GPU mining functionality enabling high-performance cryptocurrency mining with proper hardware utilization and monitoring. |
| `gpu_status_file.rs` | GPU configuration and status persistence for mining optimization. Defines GpuStatus struct with CUDA grid/block size recommendations and GpuSettings for device availability tracking. Provides file-based storage for GPU-specific parameters, device exclusion settings, and mining optimization hints. Used by GPU mining adapter to maintain consistent device configurations across sessions. |
| `internal_wallet.rs` | Internal wallet implementation for Tari address generation, seed management, and paper wallet creation. Exports: InternalWallet struct with cryptographic key management, WalletConfig for persistence, PaperWalletConfig for backup creation. Dependencies: Tari cryptography libraries, key management, mnemonic handling, credential storage. Used by: Wallet setup and address generation throughout the application. Implements seed encryption, address derivation, network-specific storage, and secure credential management. Critical for wallet security, backup creation, and Tari address generation with proper key isolation. |
| `main.rs` | Main Rust entry point for Tari Universe Tauri backend. Initializes logging, telemetry (Sentry), manages mining state (CPU/GPU), P2Pool, Tor, wallet, and node connections. Registers Tauri command handlers for frontend communication. Manages system tray, auto-launching, external dependencies, and websocket events. Uses Arc<Mutex> for shared state management across mining managers, wallet adapters, and network components. Handles app lifecycle events and graceful shutdown procedures. |
| `mining_status_manager.rs` | Mining status reporting service for airdrop and analytics integration. Exports: MiningStatusManager with periodic status reporting, MiningStatusMessage for API communication. Dependencies: CPU/GPU miner status watches, node status monitoring, JWT token management, WebSocket data signing. Used by: Airdrop system to track mining participation and reward distribution. Implements 15-second interval reporting, cryptographic message signing, and authenticated API communication. Critical for mining reward tracking, user analytics, and participation verification in the Tari ecosystem. |
| `mm_proxy_adapter.rs` | Merge Mining Proxy adapter for connecting XMRig to Tari network. Implements ProcessAdapter trait with MergeMiningProxyConfig for port, pool, and base node configuration. Handles proxy process lifecycle, health monitoring, and P2Pool integration. Provides merge mining coordination between Monero mining (XMRig) and Tari blockchain through proxy service. Critical component for dual-chain mining operations. |
| `mm_proxy_manager.rs` | Merge Mining Proxy Manager for coordinating Monero-Tari merge mining operations. Manages MergeMiningProxyAdapter lifecycle including startup, health monitoring, and configuration. Exports: MmProxyManager struct, StartConfig for proxy configuration. Dependencies: MergeMiningProxyAdapter, ProcessWatcher, PortAllocator, TasksTrackers. Handles proxy startup with 90-second timeout, port allocation, P2Pool integration, and Monero node configuration. Used by mining system to bridge Monero mining pools with Tari blockchain. Implements process management with health checks and automatic restart capabilities. |
| `network_utils.rs` | Network utility functions for Tari blockchain integration. Provides get_best_block_from_block_scan() for querying network block height via Tari text explorer API. Handles mainnet and testnet URL construction, network-specific explorer endpoints, and blockchain synchronization helpers. Used for node sync validation and network connectivity verification across different Tari networks. |
| `p2pool_adapter.rs` | P2Pool process adapter for decentralized mining pool integration. Implements ProcessAdapter trait with P2poolConfig management, health monitoring, and statistics collection. Handles P2Pool binary lifecycle, connection management, and performance tracking. Provides decentralized mining alternative to traditional pools with reduced centralization risks. Includes Windows firewall configuration for P2Pool networking. |
| `p2pool_manager.rs` | P2Pool mining pool management for decentralized mining in Tari Universe. Manages P2poolAdapter process lifecycle, handles configuration for GRPC/stats ports, base node connections, and RandomX settings. Monitors pool statistics, connections, and performance metrics. Coordinates with PortAllocator for dynamic port assignment and ProcessWatcher for health monitoring. Supports CPU benchmarking and squad overrides for pool optimization. |
| `pool_status_watcher.rs` | Generic pool status monitoring system for mining pools. Exports: PoolStatus, PoolApiAdapter trait, PoolStatusWatcher, SupportXmrStyleAdapter. Dependencies: reqwest for HTTP requests, serde for JSON handling. Provides extensible framework for monitoring different mining pool APIs with adapter pattern. Includes SupportXMR-style adapter for pools using similar API format. Tracks accepted shares, unpaid balance, total balance, and minimum payout thresholds. Used for monitoring external mining pool statistics and user earnings. |
| `port_allocator.rs` | Dynamic port allocation utility for Tari Universe services. Exports: PortAllocator struct. Dependencies: std::net::TcpListener for port binding. Automatically finds available ports using OS port 0 binding with fallback to manual range 49152-65535. Includes port availability verification and retry logic (10 attempts max). Prevents port conflicts between multiple services (node, proxy, wallet, gRPC endpoints). Critical utility used throughout the application for service startup and network configuration management. |
| `process_adapter.rs` | Core process management abstraction layer for Tari Universe backend. Defines ProcessAdapter trait for unified external process handling including mining software, blockchain nodes, and system utilities. Provides ProcessInstance and StatusMonitor traits for process lifecycle management, health checking, and startup specifications. Features process statistics tracking, permission management, PID file handling, and graceful shutdown mechanisms. Implements process launching with proper working directory setup and cross-platform process management. Critical infrastructure enabling consistent process management across all external binaries including XMRig, GPU miners, Tor daemon, and Tari node processes. |
| `process_adapter_utils.rs` | Utility functions for process adapter initialization and cleanup. Provides setup_working_directory() for creating network-specific data directories and peer database cleanup. Handles file system preparation for Tari node processes with network isolation and cleanup routines. Used by various process adapters for consistent directory management and startup preparation. |
| `process_killer.rs` | Cross-platform process termination utility for killing child processes. Exports: kill_process async function. Dependencies: Windows taskkill, Unix SIGTERM signals. Uses platform-specific methods: Windows CMD with taskkill /F /PID for forceful termination, Unix nix library with SIGTERM signal for graceful shutdown. Includes PROCESS_CREATION_NO_WINDOW flag on Windows to prevent console windows. Essential utility for clean shutdown of managed mining processes, nodes, and external binaries when stopping services or during application exit. |
| `process_stats_collector.rs` | Centralized process statistics collection system for all Tari Universe services. Exports: ProcessStatsCollectorBuilder, ProcessStatsCollector. Dependencies: tokio watch channels, ProcessWatcherStats. Uses builder pattern with take methods to distribute stat senders to process watchers (CPU miner, GPU miner, MM proxy, node, P2Pool, Tor, wallet). Collector provides read access to all process statistics for monitoring dashboard, performance tracking, and health status reporting across the entire application. |
| `process_utils.rs` | Process management utility functions for launching and managing child processes. Exports: launch_child_process, retry_with_backoff, write_pid_file functions. Dependencies: tokio::process, ProcessStartupSpec. Provides cross-platform process launching with stdio control, environment variables, and Windows-specific PROCESS_CREATION_NO_WINDOW flag. Includes exponential backoff retry mechanism for resilient operations and PID file management for process tracking. Core utilities used throughout the application for reliable process lifecycle management. |
| `process_watcher.rs` | Process monitoring and health checking implementation for Tari Universe. Manages ProcessWatcher instances with comprehensive statistics tracking including uptime, health checks, warnings, failures, and restart counts. Features automatic restart capabilities, health status monitoring, and process failure detection. Implements ProcessWatcherStats for performance metrics and process reliability monitoring. Provides binary resolution, graceful shutdown handling, and task tracking integration. Critical for maintaining system reliability by monitoring external processes like mining software and blockchain nodes, automatically recovering from failures, and providing detailed operational statistics for troubleshooting. |
| `progress_tracker_old.rs` | Legacy progress tracking system for Tauri Universe setup and operations. Exports: ProgressTracker, ProgressTrackerInner structs. Dependencies: Tauri AppHandle, tokio watch channels. Provides progress reporting with title, parameters, and percentage completion for long-running tasks. Uses internal min/max range tracking for nested progress operations. Sends progress updates through channels to frontend components. Marked as 'old' indicating newer progress tracking implementation exists (likely progress_trackers module). Used for setup phases, mining initialization, and other async operations requiring user feedback. |
| `release_notes.rs` | Release notes management system for Tari Universe with caching and version checking. Exports: ReleaseNotes singleton, ReleaseNotesFile struct. Dependencies: reqwest with retry middleware, semver, ConfigCore. Fetches CHANGELOG.md from CDN with ETag-based caching, compares app versions, and automatically shows release notes for new versions. Uses 1-hour cache expiration and exponential backoff retry policy. Integrates with config system to track last shown version. Emits events to frontend for release notes dialog display and update notifications. |
| `spend_wallet_adapter.rs` | Spending wallet adapter for advanced transaction operations including one-sided transactions to stealth addresses. Provides command-line interface to Tari wallet binary for specialized spending operations. Exports: SpendWalletAdapter (main adapter), DummyStatusMonitor (health monitor), ExecutionCommand (command builder). Implements ProcessAdapter trait for wallet binary management. Dependencies: process adapter framework, port allocator, internal wallet for seed words, UniverseAppState for integration. Key functionality: (1) send_one_sided_to_stealth_address() - executes complex transaction flow including wallet recovery, sync, transaction creation, and export to main wallet, (2) Wallet recovery using seed words from internal wallet with MINOTARI_WALLET_SEED_WORDS environment variable, (3) Transaction sync against base node for accurate balance state, (4) One-sided transaction creation with optional payment ID and stealth address support, (5) Transaction export to JSON format for import into main view wallet, (6) Process management with proper argument construction, environment variables, and exit code handling. Features intentional base node disconnection during transaction creation to prevent broadcast (transactions are exported instead). Includes wallet birthday optimization, network-specific DNS seed configuration, and comprehensive error reporting with Sentry integration. Used by: SpendWalletManager for advanced transaction operations. Critical for: stealth address transactions, one-sided payments, and transaction export workflows. Enables sophisticated transaction types not available through standard wallet operations. |
| `spend_wallet_manager.rs` | High-level manager for spending wallet operations with automatic cleanup and lifecycle management. Orchestrates SpendWalletAdapter operations with data cleanup scheduling based on blockchain height. Exports: SpendWalletManager (main manager struct). Dependencies: SpendWalletAdapter, NodeManager, BinaryResolver, task tracker framework. Key functionality: (1) init() method sets up spending wallet with binary resolution, data cleanup, and adapter initialization, (2) send_one_sided_to_stealth_address() coordinates node readiness, connection details setup, transaction execution, and cleanup scheduling, (3) Automatic data cleanup system that monitors blockchain height and erases wallet data after BLOCKS_THRESHOLD (5 blocks) to maintain privacy, (4) Block height monitoring task that watches BaseNodeStatus and triggers cleanup when threshold is reached, (5) Temporary cleanup suspension during active transaction processing to prevent data loss. Features erase_related_data() for secure data removal and next_wallet_data_erasure_block tracking with mutex protection. Includes comprehensive logging for cleanup operations and error handling for poisoned locks. Used by: wallet setup phase and frontend commands for advanced transaction operations. Critical for: transaction privacy through data cleanup, spending wallet lifecycle management, and coordination between wallet operations and blockchain state. Ensures sensitive transaction data is automatically purged while maintaining operational capability for active transactions. |
| `systemtray_manager.rs` | System tray management for Tari Universe with mining statistics display. Exports: SystemTrayManager, SystemTrayData, SystrayItemId enum. Dependencies: Tauri tray/menu APIs, formatting utilities. Provides system tray with real-time mining information: CPU/GPU hash rates, estimated earnings, and minimize/restore functionality. Includes platform-specific behavior (macOS About menu, Linux tooltip limitations) and dynamic menu item updates. Integrates with mining system to display current performance metrics and provides quick access to main window controls. Essential for monitoring mining status when application is minimized. |
| `tasks_tracker.rs` | Global task coordination and lifecycle management for Tari Universe. Provides TasksTrackers singleton with organized task tracking for different application phases: common, node_phase, and mining_phase operations. Implements TaskTrackerUtil with shutdown signal coordination, task spawning, and graceful termination. Features structured task management enabling proper cleanup during application shutdown and phase transitions. Critical for coordinating async operations across mining, blockchain synchronization, and application lifecycle ensuring proper resource cleanup and preventing orphaned processes during shutdown. |
| `telemetry_manager.rs` | Comprehensive telemetry and analytics management for Tari Universe. Collects and reports mining performance metrics, hardware status, node synchronization data, GPU/CPU mining statistics, and application usage patterns. Implements privacy-preserving analytics with user consent, anonymous ID generation, and secure data transmission. Features JWT-based authentication for telemetry services, hardware monitoring integration, and airdrop game data collection. Handles mining time tracking, earnings estimation, node status reporting, and system performance metrics. Supports configurable telemetry levels and opt-out mechanisms respecting user privacy preferences. Critical for product improvement, performance optimization, and user experience enhancement while maintaining strict privacy standards and compliance with data protection regulations. |
| `telemetry_service.rs` | Telemetry data collection service for Tari Universe analytics and debugging. Exports: TelemetryService, TelemetryData, FullTelemetryData structures. Dependencies: reqwest, hardware monitor, ConfigCore. Collects application events with system information (OS, CPU, GPU, version) and user consent checking. Uses async channels for non-blocking telemetry sending with retry logic and rate limiting handling. Includes user anonymization with anon_id and respects privacy settings. Essential for product analytics, crash reporting, and user experience optimization while maintaining privacy compliance. |
| `tor_adapter.rs` | Tor network adapter for privacy-enhanced Tari node connections. Exports: TorAdapter, TorConfig, TorStatusMonitor. Dependencies: TorControlClient, PortAllocator, process management. Manages Tor daemon with SOCKS proxy, bridge support (obfs4), control port configuration, and entry guard information. Includes platform-specific libevent handling for Linux, configuration persistence, and health monitoring with bootstrap status checking. Provides privacy layer for node communications and enables operation in restrictive network environments. Critical for user privacy and network censorship resistance. |
| `tor_control_client.rs` | Tor control client for monitoring Tor daemon status and network connectivity through control port communication. Provides real-time Tor status information for privacy networking features. Exports: TorControlClient (main client), TorStatus (status struct). Dependencies: tokio for async TCP communication, regex for response parsing, serde for serialization. Key functionality: (1) get_info() method connecting to Tor control port on localhost with authentication via AUTHENTICATE command, (2) Bootstrap phase monitoring through GETINFO status/bootstrap-phase command with regex parsing of progress percentage and completion status, (3) Circuit establishment monitoring via GETINFO status/circuit-established to verify Tor circuit availability, (4) Network liveness detection through GETINFO network-liveness command for connectivity validation, (5) Comprehensive status aggregation in TorStatus struct with bootstrap_phase (0-100), is_bootstrapped boolean, network_liveness, and circuit_ok flags. Features robust response parsing handling Tor's text-based control protocol and error handling for authentication failures. Used by: Tor manager for monitoring Tor daemon health and readiness before enabling privacy features. Critical for: privacy networking verification, Tor readiness detection, and user feedback during Tor initialization process. |
| `tor_manager.rs` | Tor network management for privacy networking in Tari Universe. Manages TorAdapter process lifecycle with 3-minute startup timeout, monitors Tor connection status, and handles control client communications. Provides privacy layer for wallet and node connections via Tor proxy. Integrates with ProcessWatcher for health monitoring and TasksTrackers for resource management. Supports both hidden services and client proxy modes. |
| `updates_manager.rs` | Application auto-update management for Tari Universe using Tauri updater plugin. Handles update checking, downloading, and installation with progress tracking and user notifications. Features exchange-specific update endpoints, pre-release channel support, and download progress monitoring with DownloadProgressPayload events. Integrates with system status monitoring for appropriate update timing and frontend ready channel for UI coordination. Provides update availability checking, manual update triggering, and graceful update installation. Includes error handling for update failures and network issues. Critical for keeping application current with latest features, security patches, and mining optimizations while providing seamless user experience during updates. |
| `wallet_adapter.rs` | Tari wallet integration adapter implementing ProcessAdapter for wallet daemon management. Handles wallet process lifecycle, GRPC client connectivity, and comprehensive wallet operations including balance queries, transaction history, address generation, and transaction sending. Implements WalletBalance, TransactionInfo structures and WalletState tracking for frontend synchronization. Features wallet synchronization monitoring, network status checking, and transaction import capabilities. Provides WalletStatusMonitor for health checking and automatic wallet recovery. Critical component enabling all wallet functionality including balance display, transaction processing, and blockchain interaction through the Tari wallet daemon. |
| `wallet_manager.rs` | Wallet management system handling Tari wallet lifecycle, transactions, and balance monitoring. Manages wallet startup/shutdown, transaction processing, balance tracking, and integration with node manager. Includes wallet state monitoring, error handling, and event emission for frontend updates. |
| `websocket_events_manager.rs` | WebSocket event management system for real-time mining status broadcasting to external services with cryptographic authentication. Orchestrates periodic mining status updates with JWT token validation and message signing. Exports: WebsocketEventsManager. Dependencies: websocket manager, JWT decoding, command signing, status monitoring, task tracking. Key functionality: (1) emit_interval_ws_events() spawns background task sending mining status every 15 seconds to WebSocket connections, (2) Real-time status aggregation combining CPU mining, GPU mining, and node status with network information, (3) JWT token integration with airdrop claims for user identification and authentication, (4) Cryptographic message signing using sign_ws_data command for message integrity and authenticity, (5) Mining activity detection combining CPU and GPU hashrate monitoring for accurate status reporting, (6) Graceful shutdown handling with close channel coordination and task cancellation. Features singleton pattern with started state tracking, comprehensive error handling, and structured JSON payload with signature verification. Used by: external integrations, mining pool communications, and real-time status broadcasting. Critical for: authenticated mining status reporting, external service integration, real-time mining analytics, and secure communication with mining infrastructure services. |
| `websocket_manager.rs` | WebSocket management for real-time communication with Tari airdrop service. Exports: WebsocketManager, WebsocketMessage, WebsocketManagerStatusMessage. Dependencies: tokio-tungstenite, Tauri event system. Provides bi-directional WebSocket communication with JWT authentication, automatic reconnection, status broadcasting, and graceful shutdown handling. Connects to airdrop API with app_id and optional token authentication. Used for real-time airdrop notifications, balance updates, and interactive features requiring live backend communication. Essential for airdrop system and real-time user engagement. |
| `xmrig_adapter.rs` | XMRig CPU miner adapter implementation for Tari Universe. Manages XMRig binary execution, configuration, and monitoring with support for different connection types: LocalMmproxy (local mining proxy), Pool (external pool), and P2Pool (decentralized pool). Implements ProcessAdapter trait with health monitoring, startup specifications, and status tracking. Features XMRig HTTP API integration for real-time statistics, hashrate monitoring, and process health checking. Handles XMRig configuration generation, port allocation, and connection management. Provides XmrigSummary data structures for mining metrics and performance tracking. Critical component enabling CPU mining functionality through XMRig integration with proper process management and monitoring capabilities. |

##### src-tauri/src/binaries/

| File | Description |
|------|-------------|
| `adapter_github.rs` | GitHub releases API adapter for downloading external binaries. Implements LatestVersionApiAdapter trait with fetch_releases_list() and specific_version_info() methods. Handles GitHub rate limiting, asset filtering via regex patterns, version resolution, and platform-specific binary selection. Uses RequestClient for HTTP operations and supports both latest and specific version queries. Core component of binary management system. |
| `adapter_tor.rs` | Tor release adapter for downloading Tor expert bundles. Implements LatestVersionApiAdapter with CDN fallback strategy (tari.com CDN → torproject.org). Handles platform-specific bundle selection (Linux, Windows x64, macOS), version 14.5.1 hardcoded for stability. Provides download links for tor binary integration with privacy networking features. Includes platform detection utilities. |
| `adapter_xmrig.rs` | XMRig binary adapter implementing platform-specific download and validation logic for CPU mining. Exports: XmrigVersionApiAdapter struct implementing LatestVersionApiAdapter trait. Dependencies: github API client, regex for platform matching, async-trait for interface implementation. Used by: CPU mining manager for XMRig binary lifecycle management. Handles platform detection (Windows/macOS/Linux/FreeBSD), binary path resolution, checksum validation from SHA256SUMS files, and network-specific binary folders. Essential for secure XMRig installation and RandomX mining operations. |
| `binaries_list.rs` | Core binary enumeration and management definitions. Defines Binaries enum (Xmrig, MergeMiningProxy, MinotariNode, Wallet, ShaP2pool, GpuMiner, Tor) with name mapping, path resolution, and version management. Provides binary-specific configuration including expected executables, folder structures, and network compatibility. Essential registry for all external dependencies. |
| `binaries_manager.rs` | Comprehensive binary management system for downloading, validating, and maintaining external mining binaries (XMRig, GPU miners, Tor). Exports: BinaryManager struct with version selection, download orchestration, and checksum validation. Dependencies: semver for version handling, tari_common for network config, github client for release fetching. Used by: CPU/GPU mining modules to ensure correct binary versions. Implements network-specific version requirements, retry logic for downloads, extraction handling, and local/online version comparison. Critical for secure and reliable mining operations with proper binary verification. |
| `binaries_resolver.rs` | Binary version resolution and download orchestration system. Implements BinariesResolver with adaptive version checking (6-hour intervals), multiple download sources (GitHub, Tor, Xmrig), progress tracking, and caching. Manages version constraints from config files, handles network timeouts, provides progress updates via watch channels. Core binary management coordinator handling download, verification, and installation of external mining and networking components. |
| `mod.rs` | Binary management module for Tari Universe organizing third-party executable handling. Exports Binaries enum and BinaryResolver for cross-platform binary management. Contains adapters for different binary types: GitHub releases (adapter_github), Tor daemon (adapter_tor), and XMRig CPU miner (adapter_xmrig). Includes binaries_manager for lifecycle management and binaries_list for supported binary definitions. Critical for downloading, updating, and managing external mining software, Tor daemon, and other required executables across Windows, macOS, and Linux platforms. Enables automatic binary updates and platform-specific binary selection for optimal performance. |

##### src-tauri/src/configs/

| File | Description |
|------|-------------|
| `config_core.rs` | Core application configuration managing system-wide settings and user preferences. Exports: ConfigCore singleton, ConfigCoreContent struct with app settings, AirdropTokens for reward system. Dependencies: getset for property access, semver for versioning, tari_common for network config. Used by: All application modules for accessing global settings. Manages P2Pool preferences, Tor usage, telemetry consent, auto-launch settings, A/B testing groups, airdrop tokens, and node connection details. Implements persistent configuration with network-specific defaults and migration support. |
| `config_mining.rs` | Mining configuration management with singleton pattern. Defines MiningMode enum (Eco=30% CPU, Ludicrous=100%, Custom), ConfigMiningContent with CPU/GPU settings, thread counts, pool configurations. Implements ConfigImpl trait for persistence, provides reactive updates via EventsEmitter. Manages mining behavior, performance profiles, and hardware optimization settings. Used by mining adapters and UI preferences. |
| `config_ui.rs` | UI configuration management with DisplayMode (System/Dark/Light theme), language settings with automatic locale detection, and visual preferences. Implements ConfigImpl trait for persistence and EventsEmitter for reactive updates. Manages appearance settings, internationalization preferences, and user interface customization. Used by frontend theme system and i18n framework. |
| `config_wallet.rs` | Wallet configuration management with ConfigWalletContent containing migration tracking, Monero address generation, and wallet preferences. Implements ConfigImpl trait for persistence and integrates with EventsEmitter for state updates. Manages wallet-specific settings, cross-chain bridge configurations, and wallet creation workflows. Used by wallet manager and transaction processing systems. |
| `mod.rs` | Configuration module organizing all application configuration structures and traits. Exports: config_core (app-wide settings), config_mining (mining parameters), config_ui (interface preferences), config_wallet (wallet settings), trait_config (configuration interface). Used by: All application modules requiring persistent configuration. Provides centralized configuration management with type-safe access patterns and consistent serialization. Critical module for app settings, user preferences, and system configuration across frontend and backend components. |
| `trait_config.rs` | Configuration trait definition providing consistent interface for all config modules in Tari Universe. Defines ConfigImpl trait with read_config() and write_config() methods for serialization/deserialization. Ensures type-safe configuration management across mining, wallet, UI, and core settings. Used by config_mining.rs, config_wallet.rs, config_ui.rs, and config_core.rs for unified config handling. |
| `trait_config_test.rs` | Test implementation and validation module for ConfigImpl trait. Defines TestConfig struct with NotFullConfigContent for testing configuration persistence patterns. Implements ConfigContentImpl and ConfigImpl traits with getters/setters using getset macros. Provides test utilities for validating configuration read/write operations and ensuring trait implementations work correctly across all config modules. |

##### src-tauri/src/github/

| File | Description |
|------|-------------|
| `cache.rs` | GitHub API response caching system with ETag-based cache invalidation. Implements CacheJsonFile with repository tracking, file-based persistence, and cache entry management. Supports both GitHub API and CDN mirror responses, prevents redundant downloads, and provides cache cleanup functionality. Optimizes binary download performance by avoiding unnecessary GitHub API calls during binary resolution. |
| `mod.rs` | GitHub API integration module coordinator. Exports request_client and cache modules, defines Release and Asset structs for GitHub API responses, and ReleaseSource enum for source tracking. Implements list_releases() for fetching GitHub releases with caching support. Central module for GitHub-based binary discovery and download functionality used throughout the binary management system. |
| `request_client.rs` | HTTP client for GitHub API and file downloads with resilience features. Implements RequestClient with retry policies, exponential backoff, CloudFlare cache status handling, and progress tracking. Provides download_file() with resume capability, rate limiting protection, and error handling. Supports both GitHub API requests and binary asset downloads with middleware for reliability and performance optimization. |

##### src-tauri/src/hardware/

| File | Description |
|------|-------------|
| `hardware_status_monitor.rs` | Hardware monitoring and detection system for Tari Universe optimizing mining performance. Implements singleton HardwareStatusMonitor with specialized CPU and GPU parameter readers for different manufacturers (Intel, AMD, Apple, NVIDIA). Features real-time hardware monitoring including temperature, utilization, and performance metrics using sysinfo integration. Provides manufacturer-specific optimization strategies and hardware capability detection for mining configuration. Includes comprehensive hardware profiling for CPU and GPU resources enabling intelligent mining mode selection and thermal management. Critical for hardware-aware mining optimization and preventing overheating during mining operations. |
| `mod.rs` | Hardware monitoring module coordinator. Exports cpu_readers and gpu_readers submodules for vendor-specific hardware monitoring implementations. Provides hardware_status_monitor for system performance tracking. Central module for hardware abstraction layer supporting AMD, Intel, Apple, and NVIDIA devices across different platforms. |

###### src-tauri/src/hardware/cpu_readers/

| File | Description |
|------|-------------|
| `amd_cpu_reader.rs` | AMD CPU hardware monitoring implementation. Implements CpuParametersReader trait with sysinfo-based temperature and usage tracking. Platform-specific support (Linux/macOS enabled, Windows disabled). Provides get_device_parameters() for AMD processor monitoring including thermal sensors and performance metrics. Used by hardware status monitor for AMD Ryzen and older AMD processors. |
| `apple_cpu_reader.rs` | Apple Silicon CPU monitoring implementation. Implements CpuParametersReader trait with macOS-only support for M1/M2/M3 processors. Uses sysinfo for Apple Silicon-specific temperature and performance monitoring with thermal sensor integration. Provides hardware monitoring optimized for Apple's ARM-based processors including efficiency and performance core tracking. |
| `intel_cpu_reader.rs` | Intel CPU hardware monitoring implementation. Implements CpuParametersReader trait with cross-platform support (Windows/Linux/macOS enabled). Uses sysinfo for CPU temperature and usage monitoring with component-based thermal tracking. Provides comprehensive Intel processor monitoring including performance counters and thermal management for optimal mining performance. |
| `mod.rs` | CPU monitoring interface module defining CpuParametersReader trait for vendor-specific implementations. Exports amd_cpu_reader, apple_cpu_reader, and intel_cpu_reader modules. Provides async trait for temperature, usage, and performance monitoring with DefaultCpuParametersReader fallback. Foundation for cross-platform CPU monitoring supporting vendor-specific optimizations and thermal management. |

###### src-tauri/src/hardware/gpu_readers/

| File | Description |
|------|-------------|
| `amd_gpu_reader.rs` | AMD GPU monitoring implementation. Implements GpuParametersReader trait with placeholder for future AMD GPU monitoring. Contains commented-out references to ADLX SDK and libamdgpu_top for AMD Radeon GPU monitoring. Currently not fully implemented but provides foundation for AMD graphics card temperature and utilization tracking for mining optimization. |
| `apple_gpu_reader.rs` | GPU parameter reader implementation for Apple GPUs (Metal/Apple Silicon). Implements GpuParametersReader trait with platform detection for macOS systems. Currently returns stub implementation with placeholder values (0.0 for usage, temperature) as Apple GPU monitoring is not yet implemented. Part of hardware monitoring system for GPU mining operations. Uses async_trait for asynchronous device parameter reading. Designed for future integration with Metal Performance Shaders or system APIs for real GPU metrics. |
| `intel_gpu_reader.rs` | GPU parameter reader implementation for Intel integrated graphics. Implements GpuParametersReader trait with cross-platform detection but currently returns stub implementation with zero values for usage and temperature metrics. Designed for monitoring Intel GPU performance during mining operations. Uses async_trait for consistent interface with other GPU readers. Future implementation would integrate with Intel GPU monitoring APIs or system performance counters for real metrics. |
| `mod.rs` | GPU monitoring interface module defining GpuParametersReader trait for vendor-specific implementations. Exports amd_gpu_reader, apple_gpu_reader, intel_gpu_reader, and nvidia_gpu_reader modules. Provides async trait for GPU temperature, usage, and performance monitoring with DefaultGpuParametersReader fallback. Foundation for cross-platform GPU monitoring supporting NVIDIA, AMD, Intel, and Apple Silicon graphics cards. |
| `nvidia_gpu_reader.rs` | NVIDIA GPU monitoring implementation using NVML (NVIDIA Management Library). Implements GpuParametersReader with GPU temperature, utilization, and power monitoring through nvml-wrapper. Provides real-time metrics for NVIDIA graphics cards including thermal sensors and performance counters. Essential for GPU mining optimization and thermal protection on NVIDIA hardware. |

##### src-tauri/src/node/

| File | Description |
|------|-------------|
| `local_node_adapter.rs` | Local Minotari base node adapter implementing NodeAdapter and ProcessAdapter traits. Manages local blockchain node with automatic port allocation, gRPC configuration, and platform-specific optimizations. Exports: LocalNodeAdapter struct. Dependencies: NodeAdapterService, ProcessWatcher, PortAllocator, Tor integration. Handles node initialization, migration logic, peer configuration, and AB testing for node performance. Configures extensive command-line arguments for mining, P2P networking, Tor support, and pruning options. Includes Windows firewall integration and platform-specific optimizations for connectivity and performance. |
| `mod.rs` | Node management module coordinator. Exports local_node_adapter for embedded Tari node, node_adapter for generic node interface, node_manager for lifecycle management, and remote_node_adapter for external node connections. Central module for Tari blockchain node integration supporting both local and remote node configurations. |
| `node_adapter.rs` | Generic Tari node adapter providing blockchain interaction interface. Implements gRPC client for Tari base node communication including block queries, network state monitoring, and sync status tracking. Provides BaseNodeConfig, NodeStatusSyncInfo, and BaseNodeStatus structures. Handles both local and remote node connections with health monitoring and automatic reconnection. Core blockchain integration component. |
| `node_manager.rs` | Blockchain node management handling both local and remote Tari node connections. Manages node type selection (Local, Remote, RemoteUntilLocal, LocalAfterRemote), process lifecycle for local node operations, and connection switching logic. Coordinates node startup configuration, blockchain synchronization monitoring, peer connection management, and database cleanup operations. Implements health monitoring, error recovery, and automatic node type transitions. Provides blockchain status updates, network information, and manages node identity/configuration persistence. Central component for all blockchain connectivity in Tari Universe with seamless switching between local and remote operations. |
| `remote_node_adapter.rs` | Remote Minotari base node adapter for connecting to external Tari nodes. Implements NodeAdapter and ProcessAdapter traits with NullProcessInstance for no-op process management. Exports: RemoteNodeAdapter, NullProcessInstance. Dependencies: NodeAdapterService, BaseNodeStatus broadcast. Handles gRPC address parsing with HTTP/HTTPS scheme detection, port 443 HTTPS assumption. Provides connection details retrieval with IPv4/IPv6 preference. Used when users choose external node instead of running local blockchain node, reducing resource usage and startup time. |

##### src-tauri/src/p2pool/

| File | Description |
|------|-------------|
| `mod.rs` | P2Pool mining module coordinator. Exports models for P2Pool data structures and stats_client for pool statistics gathering. Provides interface for decentralized pool mining integration with Tari blockchain. Central module for P2Pool functionality enabling distributed mining coordination. |
| `models.rs` | Data models for P2Pool mining statistics and network information. Exports: StatsBlock, SquadDetails, ConnectionInfo, P2poolStats, ChainStats, BlockStats, EstimatedEarnings, PeerInfo. Dependencies: serde for JSON serialization, tari_core for MicroMinotari amounts. Defines structures for blockchain statistics, network connection counters, peer information, and estimated mining earnings across time periods (1min to 30days). Used by P2Pool integration to track mining performance, network health, and calculate rewards for decentralized mining pool operations. |
| `stats_client.rs` | HTTP client for P2Pool statistics server API. Exports: Client struct. Dependencies: reqwest for HTTP requests, P2pool models for data structures. Provides methods to fetch P2Pool stats (connection info, chain statistics, peer details) and network connections from P2Pool stats server endpoints. Uses JSON deserialization with error logging for failed requests. Used by mining system to monitor P2Pool network health, performance metrics, and connected peer information for user dashboard display. |

##### src-tauri/src/progress_trackers/

| File | Description |
|------|-------------|
| `mod.rs` | Module definition for modern progress tracking system in Tari Universe. Exports: ProgressSetupCorePlan, ProgressStepper from sub-modules. This is the newer progress tracking implementation replacing progress_tracker_old.rs. Contains structured progress plans for setup phases and stepped progress reporting. Used for setup wizard, mining initialization, node synchronization, and other long-running operations requiring user progress feedback. Provides more sophisticated progress management than the legacy system. |
| `progress_plans.rs` | Structured progress tracking plans for Tari Universe setup phases. Exports: ProgressEvent, ProgressStep traits, ProgressSetupCorePlan, ProgressSetupNodePlan, ProgressSetupHardwarePlan, ProgressSetupWalletPlan, ProgressSetupMiningPlan enums, ProgressPlans wrapper. Dependencies: ProgressEvents enum. Defines weighted progress steps for each setup phase with percentage calculations, titles, and event types. Core phase: platform prerequisites, modules, network test. Node phase: binaries, Tor, sync stages. Hardware: GPU detection, benchmarks. Wallet: binaries, initialization, bridge setup. Mining: P2Pool, proxy setup. Used by ProgressStepper for structured setup progress reporting. |
| `progress_stepper.rs` | Advanced progress tracking engine for Tari Universe setup phases. Exports: ProgressStepper, ProgressStepperBuilder, ChanneledStepUpdate. Dependencies: ProgressPlans, EventsEmitter, telemetry service. Provides weighted step progression with percentage calculations, channeled updates for long-running operations, and timeout monitoring integration. Emits progress events to frontend and sends telemetry data. Includes builder pattern for constructing phase-specific progress sequences. Used by setup system to provide real-time progress feedback with accurate percentage completion and step-by-step guidance. |

##### src-tauri/src/setup/

| File | Description |
|------|-------------|
| `mod.rs` | Application setup and onboarding module coordinator. Exports setup_manager for overall setup orchestration, and setup phases (core, hardware, mining, node, wallet) for step-by-step initialization. Includes trait_setup_phase interface and utility functions for guided application setup and configuration. Central module for first-time user onboarding and system preparation. |
| `phase_core.rs` | Core setup phase implementation for Tari Universe initialization. Exports: CoreSetupPhase, CoreSetupPhaseOutput, CoreSetupPhaseAppConfiguration. Dependencies: SetupPhaseImpl trait, ProgressStepper, ConfigCore, AutoLauncher, PlatformUtils, NetworkStatus. Handles platform prerequisites, application module initialization, auto-launcher setup, system tray initialization, mining status manager configuration, and network speed testing. First phase in setup sequence, essential for preparing the application environment before node, wallet, and mining configuration. Emits completion event to trigger next setup phases. |
| `phase_hardware.rs` | Hardware setup phase implementation for Tari Universe application initialization. Manages hardware detection, binary initialization, and mining capability benchmarking during application setup. Exports: HardwareSetupPhase (main phase struct), HardwareSetupPhaseOutput, HardwareSetupPhaseAppConfiguration. Implements SetupPhaseImpl trait with complete hardware setup lifecycle. Dependencies: binary resolver for GPU/CPU miners, hardware status monitor, progress tracking system, task management framework. Key functionality: (1) Downloads and initializes GPU miner and XMRig CPU miner binaries with timeout handling, (2) Detects GPU capabilities and configures mining engines (Sha3x, RandomX, Blake3), (3) Performs 30-second CPU benchmarking to determine optimal mining parameters, (4) Initializes hardware status monitoring for temperature and performance tracking, (5) Sets up continuous monitoring tasks for GPU/CPU mining status updates and system tray data synchronization. Features progress tracking through 5 distinct steps (BinariesGpuMiner, BinariesCpuMiner, DetectGPU, RunCpuBenchmark, Done) with timeout protection and error recovery. Emits events for mining status updates and systemtray integration. Used by: setup manager during application initialization sequence. Critical for: mining capability assessment and hardware resource preparation. |
| `phase_mining.rs` | Mining setup phase implementation for configuring mining infrastructure during application initialization. Orchestrates P2Pool and merge mining proxy setup for decentralized mining operations. Exports: MiningSetupPhase (main phase struct), MiningSetupPhaseOutput, MiningSetupPhaseAppConfiguration, MiningSetupPhaseSessionConfiguration. Implements SetupPhaseImpl trait for mining infrastructure setup. Dependencies: binary resolver for mining proxies, P2Pool manager, MM proxy manager, configuration system for mining/core settings. Key functionality: (1) Downloads and initializes MergeMiningProxy and ShaP2pool binaries with timeout handling, (2) Configures P2Pool connection with base node GRPC address, squad overrides, and stats server, (3) Sets up merge mining proxy with P2Pool integration, Monero node connectivity, and failover support, (4) Handles centralized vs decentralized mining pool configuration based on setup features, (5) Integrates CPU benchmark hashrate data for optimal P2Pool configuration. Configuration includes: p2pool_enabled, p2pool_stats_server_port, mmproxy_monero_nodes array, mmproxy_use_monero_fail boolean, squad_override. Features conditional setup based on CentralizedPool feature flag - skips P2Pool if centralized, skips MM proxy if decentralized. Progress tracking through 5 steps (BinariesMergeMiningProxy, BinariesP2pool, P2Pool, MMProxy, Done). Used by: setup manager after hardware phase completion. Critical for: mining pool connectivity and decentralized mining infrastructure. |
| `phase_node.rs` | Node setup phase implementation for Tari blockchain node initialization and synchronization during application startup. Manages Tor integration, node binary setup, and blockchain synchronization monitoring. Exports: NodeSetupPhase (main phase struct), NodeSetupPhaseOutput, NodeSetupPhaseAppConfiguration. Implements SetupPhaseImpl trait for node lifecycle management. Dependencies: binary resolver for Tor/node binaries, node manager, Tor manager, events manager for node type updates, configuration system. Key functionality: (1) Conditionally downloads and initializes Tor binary (skipped on macOS), configures Tor networking with control port setup, (2) Downloads and initializes MinotariNode binary with timeout handling, (3) Starts Tari node with Tor integration, base node address configuration, and database corruption recovery logic, (4) Monitors blockchain synchronization through three phases - initial sync, header sync, and block sync with real-time progress tracking, (5) Implements orphan chain detection with 30-second periodic checks to prevent mining on stale chains. Configuration includes: use_tor boolean, base_node_grpc_address string. Features sophisticated error recovery with database cleanup on specific exit codes (STOP_ON_ERROR_CODES). Progress tracking through 7 detailed steps including sync phases with percentage updates. Spawns continuous monitoring task for orphan chain detection. Used by: setup manager as foundational phase before wallet/mining setup. Critical for: blockchain connectivity, network synchronization, and mining chain validation. |
| `phase_wallet.rs` | Wallet setup phase implementation for Tari wallet initialization and spending wallet integration during application startup. Manages wallet binary setup, spending wallet initialization, and bridge tapplet configuration. Exports: WalletSetupPhase (main phase struct), WalletSetupPhaseOutput, WalletSetupPhaseAppConfiguration. Implements SetupPhaseImpl trait for wallet lifecycle management. Dependencies: binary resolver for wallet binary, tapplet resolver for bridge tapplet, wallet manager, spend wallet manager, configuration system for core/UI/wallet settings. Key functionality: (1) Downloads and initializes wallet binary with timeout handling, (2) Handles wallet migration with version nonce tracking (WALLET_MIGRATION_NONCE=1) and data cleanup for breaking changes, (3) Starts main wallet with Tor integration and local/remote node connectivity based on node manager status, (4) Initializes spending wallet manager for advanced transaction operations and stealth address support, (5) Sets up bridge tapplet for cross-chain functionality and external integrations, (6) Implements staged security modal logic - monitors wallet balance and shows security warnings for funded wallets on first discovery. Configuration includes: use_tor boolean, was_staged_security_modal_shown boolean. Features wallet birthday optimization to avoid full blockchain scan from genesis. Spawns background task to monitor wallet balance and trigger security modal for non-zero balances during initial scan completion. Progress tracking through 5 steps (BinariesWallet, StartWallet, InitializeSpendingWallet, SetupBridge, Done). Used by: setup manager as final phase before application ready state. Critical for: transaction capability, wallet security, and bridge functionality. |
| `setup_manager.rs` | Comprehensive application setup orchestration for Tari Universe. Manages multi-phase setup process including core configuration, hardware detection, node setup, wallet initialization, and mining configuration. Coordinates setup phases: CoreSetupPhase (config initialization), HardwareSetupPhase (system capabilities), NodeSetupPhase (blockchain connectivity), WalletSetupPhase (wallet creation/import), and MiningSetupPhase (mining configuration). Features sleep mode handling, exchange-specific configurations, and setup state management with frontend event emission. Handles dynamic memory configuration loading, release notes management, and WebSocket integration. Provides singleton pattern for global setup coordination and includes proper error handling and recovery mechanisms. Critical for first-time user onboarding and application initialization workflow. |
| `trait_setup_phase.rs` | Core trait definition for Tari Universe setup phase system. Exports: SetupPhaseImpl trait, SetupConfiguration struct. Dependencies: ProgressStepper, TimeoutWatcher, PhaseStatus. Defines common interface for all setup phases including progress tracking, app directory access, timeout handling, shutdown signals, and task tracking. Provides standardized lifecycle methods (new, setup, setup_inner, finalize_setup) and dependency management. Used by all setup phases (Core, Node, Hardware, Wallet, Mining) to ensure consistent behavior and integration with the setup manager system. |

###### src-tauri/src/setup/utils/

| File | Description |
|------|-------------|
| `mod.rs` | Module declaration file for setup utilities providing helper components for setup phase implementations. Exports utility modules: phase_builder (for constructing setup phases with configuration), setup_default_adapter (for standardized setup execution patterns), timeout_watcher (for timeout management during setup operations). Used by: setup phase implementations to access common utilities for configuration building, execution management, and timeout handling. This module provides the foundational utilities that enable consistent setup phase behavior across hardware, node, mining, and wallet setup phases. |
| `phase_builder.rs` | Builder pattern implementation for constructing setup phases with standardized configuration. Provides fluent API for creating SetupConfiguration objects with phase dependencies and timeout settings. Exports: PhaseBuilder (main builder struct). Dependencies: setup phase trait and configuration types. Key functionality: (1) Fluent builder pattern with new() constructor creating empty configuration, (2) with_listeners_for_required_phases_statuses() method for setting phase dependency listeners, (3) with_setup_timeout_duration() method for configuring phase timeout behavior, (4) Generic build() method that constructs any SetupPhaseImpl with the configured settings. Used by: setup manager and phase implementations to standardize phase creation with consistent dependency management and timeout configuration. Critical for: ensuring phases wait for their dependencies and have appropriate timeout protection during setup operations. Enables type-safe construction of hardware, node, mining, and wallet setup phases with common configuration patterns. |
| `setup_default_adapter.rs` | Default adapter implementation providing standardized setup execution pattern for all setup phases. Implements common setup lifecycle with dependency waiting, timeout handling, and error management. Exports: SetupDefaultAdapter with static setup() method. Dependencies: setup phase trait, events emitter for critical problems, Sentry error reporting. Key functionality: (1) Generic setup execution that works with any SetupPhaseImpl, waits for all phase dependencies to reach success status before proceeding, (2) Timeout protection using phase's timeout watcher with critical problem emission on timeout, (3) Error handling with Sentry reporting and critical problem events for setup failures, (4) Graceful shutdown handling during setup operations, (5) Spawns setup operations in task tracker for proper lifecycle management. Features comprehensive logging at info/warn/error levels with phase identification. Emits CriticalProblemPayload with phase-specific titles and descriptions for user notification. Used by: all setup phases (hardware, node, mining, wallet) as their primary execution adapter. Critical for: consistent setup behavior, error reporting, timeout management, and user feedback during application initialization. Ensures all setup phases follow the same execution pattern with proper error handling and dependency management. |
| `timeout_watcher.rs` | Timeout management utility for setup phases providing dynamic timeout behavior with hash-based change detection. Enables setup operations to extend timeouts when progress is detected through hash value changes. Exports: TimeoutWatcher (main timeout manager), hash_value() utility function, conditional_sleeper() async utility. Dependencies: tokio for async operations and watch channels. Key functionality: (1) TimeoutWatcher tracks progress through hash value changes sent via watch channel, resets timeout when different hash values are received indicating progress, (2) conditional_sleeper() utility for optional timeouts - sleeps for duration if provided, waits indefinitely if None (useful in tokio::select! blocks), (3) hash_value() utility function for computing hash of any hashable type using DefaultHasher, (4) resolve_timeout() method waits for either hash change (progress) or timeout duration, loops until timeout actually expires without progress. Configuration includes optional Duration for timeout behavior. Features detailed logging for timeout resolution and debugging support with count_sleep_duration() for development. Used by: setup phases through SetupPhaseImpl to provide timeout protection during long-running operations like binary downloads, node synchronization, and hardware detection. Critical for: preventing setup deadlocks, providing progress-aware timeouts, and enabling graceful timeout handling in setup operations. Integrates with progress tracking systems to extend timeouts when meaningful progress is detected. |

##### src-tauri/src/tapplets/

| File | Description |
|------|-------------|
| `bridge_adapter.rs` | Bridge tapplet adapter implementing download and version management for the WXTM bridge frontend tapplet. Provides platform-specific asset resolution and checksum validation for bridge tapplet releases. Exports: BridgeTappletAdapter struct. Implements LatestVersionApiAdapter trait for tapplet resolution framework. Dependencies: GitHub API integration, binary resolver framework, request client for downloads, network configuration. Key functionality: (1) fetch_releases_list() retrieves GitHub releases from tari-project/wxtm-bridge-frontend repository, (2) Platform-specific asset selection with regex matching for OS/architecture combinations (currently simplified with empty suffix), (3) Checksum validation through SHA256 file download and verification against asset names, (4) Network-aware tapplet folder management in cache directory with network-specific subdirectories, (5) Fallback URL support for download resilience with automatic retry on primary URL failure. Features network-specific path organization ({cache}/tari-universe/tapplets/bridge/{network}) and comprehensive error handling. Includes placeholder logic for future platform-specific binary selection (Windows, macOS x86_64/ARM64, Linux). Used by: TappletResolver during bridge tapplet initialization and updates. Critical for: cross-chain bridge functionality, tapplet version management, and secure tapplet distribution with integrity verification. |
| `error.rs` | Error types and handling for tapplet system operations including server management and I/O operations. Provides comprehensive error taxonomy for tapplet functionality with serde serialization support. Exports: Error (main error enum), TappletServerError (server-specific errors), IOError (I/O specific errors). Dependencies: thiserror for error derivation, serde for serialization, standard library types. Key functionality: (1) Error enum with transparent error forwarding for IOError, TappletServerError, TauriError, and JsonParsingError, (2) Serde serialization support for Error to enable JSON error responses in Tauri commands, (3) TappletServerError variants for FailedToObtainLocalAddress, FailedToStart, and BindPortError with port information, (4) IOError for parse int errors during port/configuration handling. Features structured error messages with context preservation and automatic error conversion through From trait implementations. Used by: tapplet server, tapplet manager, and tapplet resolution system for consistent error handling and user feedback. Critical for: debugging tapplet issues, providing meaningful error messages to users, and maintaining system stability during tapplet operations. |
| `mod.rs` | Tapplets (Tari Applications) ecosystem module coordinator. Exports bridge_adapter for external app integration, tapplets_manager for lifecycle management, and interface definitions. Provides tapplet_server for hosting third-party applications, tapplets_list registry, and tapplets_resolver for discovery. Central module for Tari ecosystem app extensibility and third-party integration. |
| `tapplet_server.rs` | HTTP server implementation for serving tapplet web applications locally with graceful shutdown support. Provides local web server functionality for tapplet frontend hosting using Axum framework. Exports: start_tapplet() function, using_serve_dir() router builder, serve() server function, shutdown signal handling. Dependencies: Axum web framework, tower-http for static file serving, tokio for async runtime, cancellation token for shutdown. Key functionality: (1) start_tapplet() convenience function that serves a tapplet directory on a dynamic port, (2) using_serve_dir() creates Axum router with ServeDir service for static file serving, (3) serve() function binds TCP listener on localhost with port allocation, starts Axum server with graceful shutdown, (4) Cancellation token system for clean server shutdown when tapplet is stopped or application exits, (5) Error handling for port binding failures and address resolution issues. Features automatic port assignment (port 0) with local address discovery and comprehensive error reporting. Used by: tapplet manager to host individual tapplet frontends as local web applications. Critical for: tapplet web interface accessibility, local development server functionality, and clean resource management during tapplet lifecycle operations. |
| `tapplets_list.rs` | Tapplet enumeration and metadata management providing centralized registry of available tapplets with version-specific file path generation. Defines available tapplets and their filesystem organization. Exports: Tapplets enum. Dependencies: semver for version handling, standard library types. Key functionality: (1) Tapplets enum currently containing Bridge variant for cross-chain bridge functionality, (2) name() method returns string identifier for each tapplet type, (3) from_name() method for string-to-enum conversion with panic on unknown tapplet names, (4) tapplet_file_name() method generates version-specific directory paths for tapplet organization (e.g., bridge-{version}/bridge). Used by: tapplet resolver, tapplet manager, and file system organization for tapplet version management. Critical for: tapplet discovery, filesystem organization, version management, and extensible tapplet registry. Provides foundation for adding new tapplet types with consistent naming and path conventions. |
| `tapplets_manager.rs` | Core tapplet lifecycle management including version resolution, download orchestration, and local file management. Handles complete tapplet download, validation, and version selection logic with network-specific version requirements. Exports: TappletManager struct, TappletVersionsJsonContent configuration. Dependencies: semver for version handling, GitHub integration, download utilities, progress tracking, checksum validation. Key functionality: (1) Network-specific version requirements from embedded JSON files (nextnet, testnets, mainnet configurations), (2) Online and local version discovery with semver requirement matching and prerelease filtering, (3) Download orchestration with retry logic, fallback URL support, and progress tracking, (4) Checksum validation for security with SHA256 verification against asset checksums, (5) Filesystem management including in-progress folder handling, extraction, and cleanup operations, (6) Version selection algorithm choosing highest compatible version from local and online sources. Features comprehensive error handling with Sentry integration, progress reporting throughout download process, and robust cleanup on failures. Includes sophisticated version filtering based on network prerelease prefixes and semantic version requirements. Used by: TappletResolver for all tapplet lifecycle operations including initialization, updates, and version management. Critical for: secure tapplet distribution, version compatibility management, and reliable tapplet installation with integrity verification. |
| `tapplets_resolver.rs` | High-level tapplet resolution and coordination system managing tapplet initialization, updates, and version tracking with timeout protection. Provides centralized tapplet management with network-specific configuration and adaptive download strategies. Exports: TappletResolver (main resolver), LatestVersionApiAdapter trait. Dependencies: tapplet managers, bridge adapter, configuration system, timeout handling, Sentry error reporting. Key functionality: (1) Singleton TappletResolver with lazy initialization managing HashMap of tapplet managers, (2) Network-specific regex patterns for tapplet selection (nextnet, testnet, mainnet variants with platform-specific handling for macOS ARM64), (3) Automatic update checking with 6-hour intervals based on configuration timestamps, (4) initialize_tapplet_timeout() provides 5-minute timeout protection for tapplet setup operations, (5) Complete tapplet initialization workflow including local version discovery, online update checking, download coordination, and file validation, (6) Path resolution for serving tapplet files with subfolder and version-specific organization. Features comprehensive error handling with timeout detection, version management with highest version selection, and fallback download strategies. Includes bridge tapplet configuration with tari-project/wxtm-bridge-frontend repository integration. Used by: setup phases, frontend commands, and tapplet lifecycle management. Critical for: tapplet availability, version management, timeout protection during setup, and coordinated tapplet ecosystem management. |

###### src-tauri/src/tapplets/interface/

| File | Description |
|------|-------------|
| `mod.rs` | Module declaration file for tapplet interface definitions providing data structures for tapplet metadata and management. Exports all types from the tapplet module for use throughout the tapplet system. Dependencies: internal tapplet module. Used by: tapplet management system, frontend integration, and tapplet metadata handling. Provides access to Tapplet, TappletVersion, and ActiveTapplet data structures for tapplet registry and lifecycle management. |
| `tapplet.rs` | Data structures for tapplet metadata and registry information providing typed interfaces for tapplet management. Defines serializable structs for tapplet description, versioning, and runtime state. Exports: Tapplet (metadata struct), TappletVersion (version info struct), ActiveTapplet (runtime state struct). Dependencies: serde for JSON serialization. Key functionality: (1) Tapplet struct contains comprehensive metadata including id, registry_id, package_name, display_name, logo_url, background_url, author information, and description fields, (2) TappletVersion struct tracks version information with id, tapplet_id relationship, version string, integrity hash, and registry_url, (3) ActiveTapplet struct represents running tapplet state with tapplet_id, display_name, source, and version. All structs implement Debug, Clone, and serde::Serialize for API integration. Used by: tapplet registry system, frontend tapplet display, version management, and tapplet lifecycle tracking. Critical for: tapplet discovery, metadata presentation, version tracking, and runtime state management in the tapplet ecosystem. |

##### src-tauri/src/tests/

| File | Description |
|------|-------------|
| `mod.rs` | Test module declaration file providing organization for Rust unit and integration tests. Currently contains only copyright header without test implementations. Used by: Rust test framework for organizing test modules. Future tests for the Tari Universe backend would be organized under this module structure. |

##### src-tauri/src/utils/

| File | Description |
|------|-------------|
| `address_utils.rs` | Tari address manipulation and validation utilities. Provides functions for Tari address parsing, validation, formatting, and conversion between different address formats. Includes emoji ID support, network-specific address handling, and checksum validation. Used throughout the application for wallet address operations, transaction validation, and user input processing. Essential for ensuring proper address formats and preventing user errors when handling Tari addresses. |
| `app_flow_utils.rs` | Application flow and navigation utilities for Tari Universe. Provides helper functions for managing application state transitions, navigation flows, and user journey orchestration. Includes utilities for determining next steps in setup processes, handling conditional navigation based on user configuration, and managing application phase transitions. Used by setup system and main application to provide smooth user experience through complex workflows. |
| `file_utils.rs` | File system utility functions for Tari Universe. Exports: make_relative_path, path_as_string, convert_to_string functions. Dependencies: std::path for path manipulation. Provides cross-platform path operations including relative path calculation between two paths, path component extraction with Unix-style separators, and safe PathBuf to String conversion with error handling. Used throughout the application for file system operations, configuration file paths, and logging directory management. Essential utilities for portable file handling across Windows, macOS, and Linux platforms. |
| `formatting_utils.rs` | String formatting utilities for Tari Universe user interface. Exports: format_currency, format_hashrate, and other display formatting functions. Provides consistent number formatting, currency display with proper precision, hash rate formatting with appropriate units (KH/s, MH/s, GH/s), and localized number representation. Used throughout the UI for displaying mining statistics, wallet balances, earnings estimates, and system performance metrics. Essential for providing professional, user-friendly data presentation. |
| `locks_utils.rs` | Thread synchronization and locking utilities for Tari Universe. Provides helper functions for managing async locks, mutex operations, and concurrent access patterns. Includes utilities for timeout-aware locking, deadlock prevention, and safe concurrent access to shared resources. Used throughout the application for protecting shared state in mining operations, configuration management, and process coordination. Essential for thread safety in the multi-threaded Tauri application environment. |
| `logging_utils.rs` | Log4rs configuration setup utility for Tari Universe components. Exports: setup_logging function. Dependencies: std::fs for file operations. Creates log4rs configuration files from templates with dynamic {{log_dir}} placeholder replacement. Ensures parent directories exist, converts Windows paths to Unix format for log4rs compatibility, and writes customized configuration files. Used by all Tari components (base node, wallet, proxy, P2Pool) to initialize structured logging with appropriate file rotation and level filtering. Critical for debugging and monitoring application behavior. |
| `macos_utils.rs` | macOS-specific utility functions for application environment detection and platform-specific behavior handling. Provides macOS-only functionality with conditional compilation. Exports: is_app_in_applications_folder() function (macOS only). Dependencies: standard library env and path modules with conditional macOS compilation. Key functionality: (1) is_app_in_applications_folder() determines if the current executable is running from the /Applications directory, useful for detecting proper installation vs development/testing environments, (2) Uses env::current_exe() to get the current executable path and checks if it starts with /Applications path prefix. Features conditional compilation ensuring the code only exists on macOS builds. Used by: application startup logic, installation detection, and macOS-specific behavior configuration. Critical for: detecting proper macOS installation, enabling macOS-specific features, and providing platform-appropriate user experience on macOS systems. |
| `math_utils.rs` | Mining earnings calculation utilities for Tari Universe. Exports: estimate_earning function. Dependencies: tari_core for MicroMinotari amounts. Calculates estimated daily earnings based on network hash rate, individual hash rate, and block rewards. Uses 360 blocks per day constant (both RandomX and SHA3 algorithms produce 360 blocks each = 720 total). Includes zero network hash rate protection and maximum earnings cap validation. Essential for providing users with realistic earning estimates and mining profitability calculations in the dashboard. |
| `mod.rs` | Utility modules declaration for Tari Universe helper functions. Exports modules: address_utils, app_flow_utils, file_utils, formatting_utils, locks_utils, logging_utils, macos_utils, math_utils, network_status, platform_utils, wallet_utils, system_status, windows_setup_utils (Windows only). Organizes cross-cutting utility functions used throughout the application for common operations like file handling, string formatting, network operations, platform detection, logging setup, and OS-specific functionality. Central hub for accessing utility functions from any part of the codebase. |
| `network_status.rs` | Network speed testing and monitoring system providing bandwidth analysis and connectivity validation with user feedback. Implements comprehensive network performance measurement using Cloudflare's speedtest infrastructure. Exports: NetworkStatus (singleton manager). Dependencies: cfspeedtest crate for speed testing, tokio for async operations, events emitter for UI updates. Key functionality: (1) Singleton NetworkStatus manager with watch channel for real-time speed updates, (2) perform_speed_test() conducts download (10MB), upload (10MB), and latency tests using Cloudflare's speedtest infrastructure, (3) Bandwidth threshold validation with configurable minimums (1.5 MB/s download/upload) for mining suitability assessment, (4) Event emission system sending network status updates to frontend with speed results and low bandwidth warnings, (5) Timeout protection (60 seconds) for speed tests with comprehensive error handling and logging. Features spawn_blocking for non-async speedtest library integration and detailed result reporting. Constants define test payload sizes (10MB) and minimum speeds for optimal mining performance. Used by: system monitoring, mining readiness assessment, and user network quality feedback. Critical for: network performance validation, mining capability assessment, and user guidance on connection quality for optimal mining experience. |
| `platform_utils.rs` | Platform detection and OS-specific initialization utilities. Exports: CurrentOperatingSystem enum, PlatformUtils struct. Dependencies: platform-specific modules (macos_utils, ExternalDependencies). Provides OS detection (Windows, Linux, macOS) and platform-specific prerequisites validation. macOS: enforces Applications folder installation in production. Windows: checks Visual C++ Redistributable and required dependencies via registry. Linux: minimal prerequisite checking. Used during application startup to ensure proper platform configuration and dependency satisfaction before proceeding with setup phases. |
| `system_status.rs` | System power state monitoring for handling suspend/resume events and sleep mode detection. Provides cross-platform power management event handling for mining operation coordination. Exports: SystemStatus (singleton manager). Dependencies: psp crate for power monitoring, tokio for async operations and watch channels. Key functionality: (1) Singleton SystemStatus manager with power state monitoring infrastructure, (2) start_listener() initializes PowerMonitor for system power event detection, (3) receive_power_event() processes power state changes including Suspend, Resume, and other power events, (4) Watch channel system broadcasting sleep mode state changes to subscribers throughout the application, (5) get_sleep_mode_watcher() provides receivers for components needing power state awareness. Features comprehensive error handling for power monitor initialization and event processing. Used by: mining managers, process managers, and system components requiring power state awareness. Critical for: gracefully handling system sleep/wake cycles, preventing mining operations during system suspend, and maintaining system stability during power state transitions. |
| `wallet_utils.rs` | Wallet utility functions for Monero address generation and seed management supporting cross-chain mining operations. Provides Monero wallet integration for merge mining capabilities. Exports: create_monereo_address() function. Dependencies: monero_address_creator for address generation, credential manager for seed storage, configuration system. Key functionality: (1) create_monereo_address() generates or retrieves Monero addresses for merge mining operations, (2) Credential management integration storing and retrieving Monero seeds securely in system keychain, (3) Automatic Monero seed generation using cryptographically secure random generation when no existing seed is found, (4) Mainnet Monero address generation from seeds with fallback to default address constant, (5) Configuration directory management with fallback to temp directory for credential storage. Features secure seed storage in platform-specific credential manager and deterministic address generation from seeds. Used by: merge mining setup, cross-chain operations, and Monero integration features. Critical for: Monero address management in merge mining workflows, secure seed storage, and cross-chain mining functionality enabling Tari/Monero dual mining operations. |
| `windows_setup_utils.rs` | Windows-specific setup utilities for firewall configuration and security management during mining software installation. Provides Windows firewall integration for mining binary network access. Exports: add_firewall_rule() function. Dependencies: Windows-specific process creation, netsh command integration. Key functionality: (1) check_netsh_rule_exists() verifies if a firewall rule already exists by parsing netsh command output for Allow action status, (2) add_firewall_rule() creates Windows Firewall inbound rules for mining binaries using netsh advfirewall commands, (3) Command execution with PROCESS_CREATION_NO_WINDOW flag to hide console windows during rule creation, (4) Comprehensive error handling with stderr output capture and logging for firewall operations, (5) Public profile firewall rule creation enabling network access for mining operations. Features conditional execution to avoid duplicate rule creation and proper Windows process handling. Used by: Windows installation processes, mining software setup, and network configuration during application initialization. Critical for: enabling mining software network connectivity on Windows, firewall exception management, and seamless Windows installation experience without manual firewall configuration. |

##### src-tauri/src/xmrig/

| File | Description |
|------|-------------|
| `mod.rs` | XMRig CPU miner integration module for Tari Universe. Exports: http_api sub-module. Central module organizing XMRig miner functionality including HTTP API communication, configuration management, and performance monitoring. XMRig is the primary CPU mining engine used for RandomX algorithm mining in Tari Universe. This module structure provides the foundation for XMRig process management, statistics collection, and configuration control. Used by CPU mining system for hash rate monitoring and miner control. |

###### src-tauri/src/xmrig/http_api/

| File | Description |
|------|-------------|
| `mod.rs` | XMRig HTTP API client for monitoring CPU mining performance and statistics through XMRig's REST API interface. Provides authenticated communication with XMRig mining software for real-time mining data. Exports: XmrigHttpApiClient. Dependencies: reqwest for HTTP communication, JSON parsing, retry logic. Key functionality: (1) XmrigHttpApiClient with URL and access token configuration for secure API communication, (2) get() method with Bearer token authentication for all API requests to XMRig daemon, (3) summary() method retrieving comprehensive mining statistics via /2/summary endpoint, (4) Retry logic (3 attempts) handling XMRig's occasional JSON parsing bugs documented in GitHub issue #3363, (5) Comprehensive error handling for network failures, authentication issues, and malformed responses. Features robust parsing with fallback for XMRig's known JSON corruption issues and detailed error logging. Used by: CPU miner management, hashrate monitoring, and mining performance tracking. Critical for: real-time mining statistics, CPU miner health monitoring, and performance optimization based on XMRig's internal metrics including hashrate, uptime, and connection status. |
| `models.rs` | Data models for XMRig HTTP API response deserialization providing type-safe access to XMRig mining statistics. Defines structures matching XMRig's JSON API format with optional field handling. Exports: Summary, Connection, Hashrate, Resources, Memory, Results, Cpu structs. Dependencies: serde for JSON deserialization. Key functionality: (1) Summary struct containing connection status and hashrate information as main API response, (2) Connection struct with uptime tracking and optional error log fields, (3) Hashrate struct with total hashrate array supporting multi-thread mining statistics, (4) Optional field handling for XMRig version compatibility including commented out fields that may not exist in all versions, (5) Robust deserialization supporting XMRig v6.21.0 and other versions with varying field availability. Features defensive field definitions accommodating XMRig's API evolution and version differences. Used by: XMRig HTTP API client for structured access to mining data, hashrate calculations, and connection monitoring. Critical for: type-safe XMRig data access, mining performance tracking, connection status monitoring, and hashrate calculations across different XMRig versions and configurations. |

#### src-tauri/tapplets-versions/

| File | Description |
|------|-------------|
| `tapplets_versions_mainnet.json` | Tapplet version configuration for Tari mainnet network. Defines approved tapplet versions for production mainnet deployment. Currently specifies bridge tapplet version "=0.1.7" with exact version matching. Used by tapplet resolver system to ensure compatible and secure tapplet versions are loaded for mainnet operations. Part of security system preventing incompatible or malicious tapplet execution in production environment. Critical for maintaining network security and tapplet ecosystem integrity on mainnet. |
| `tapplets_versions_nextnet.json` | Tapplet version configuration for Tari nextnet network. Defines approved tapplet versions for nextnet deployment (development/testing network). Similar structure to mainnet version file, specifying bridge tapplet version requirements. Used by tapplet resolver to ensure compatible tapplet versions for nextnet operations. Part of multi-network tapplet version management system allowing different version requirements per network. Essential for testing new tapplet versions before mainnet deployment. |
| `tapplets_versions_testnets.json` | Tapplet version configuration for Tari testnet networks. Defines approved tapplet versions for testing network deployment. Used for experimental features and testing new tapplet functionality before production release. Part of multi-network tapplet ecosystem allowing version flexibility for testing environments. Critical for tapplet development workflow and ensuring network-specific compatibility requirements are met across Tari's various test networks. |

#### src/App/

| File | Description |
|------|-------------|
| `App.styles.ts` | Styled-components configuration for main App container with motion animations. Defines transition variants (hidden, visible, dashboardInitial, exit) using motion/react-m for smooth app-level animations. Creates AppContentContainer with full viewport dimensions, custom z-index layering, and pointer event management for proper user interaction. Implements cubic-bezier easing curves and duration-based transitions for polished app entrance/exit animations. Dependencies: styled-components, motion/react-m. Used by: App.tsx main container, app-level transitions, layout management. |
| `App.tsx` | Main React application component managing app phases and layout. Exports default App function. Creates QueryClient for TanStack React Query. Manages three app states: splashscreen, main view, shutdown via CurrentAppSection component. Provides global context: ThemeProvider, QueryClientProvider, GlobalStyle, LazyMotion (animations). Includes WebGL detection with error handling. Renders FloatingElements, phase-specific content, and Tower canvas. Dependencies: motion/react, theme system, UI store, hooks. Core app orchestration component. |
| `AppEffects.tsx` | Side effects component for Tari Universe app initialization. Sets up shared logger and runs async initialization: fetches backend in-memory config, mining network settings, airdrop setup, and bridge cold wallet address. Registers custom hooks for mode detection, refresh prevention, Tauri event listening, and progress event handling. Separated from main App to prevent unwanted re-renders and maintain clean separation of concerns. |
| `AppWrapper.tsx` | Top-level React component wrapper that renders AppEffects (for side effects and event handling) alongside the main App component. Separates side effects from UI rendering concerns. Essential for proper application initialization order. |
| `sentryIgnore.ts` | Sentry error monitoring configuration with ignored error messages. Defines ALREADY_FETCHING constants for common duplicate request scenarios: transaction history, transfer history, miner metrics, and wallet balance fetching. Prevents noise in error reporting by filtering out expected 'already fetching' states that occur during normal operation. Used to improve error monitoring quality by ignoring benign concurrent request conflicts. Dependencies: none. Used by: Sentry error filtering, error monitoring configuration, request deduplication logic. |

##### src/assets/icons/

| File | Description |
|------|-------------|
| `LinkIcon.tsx` | SVG React icon component for external link indicator. Renders 14x13 viewBox link symbol with currentColor fill for theme compatibility. Single path element creating classic chain link icon for external URL navigation. Used in buttons, links, and navigation elements requiring external link indication. Dependencies: React. Used by: link buttons, external navigation, URL indicators. |
| `bridge-outline.tsx` | SVG React icon component for blockchain bridge functionality. Renders bridge outline symbol for cross-chain token transfers and interoperability features. Uses currentColor for theme integration. Dependencies: React. Used by: bridge components, cross-chain interfaces, token transfer dialogs. |
| `chevron.tsx` | SVG React icon component for chevron/arrow indicator. Directional arrow icon for dropdowns, navigation, expansion controls, and UI state indicators. Themeable with currentColor. Dependencies: React. Used by: dropdown menus, accordion controls, navigation arrows, expansion indicators. |
| `cube-outline.tsx` | SVG React icon component for 3D cube outline representation. Geometric cube icon for blockchain blocks, mining visualization, or structural elements. Theme-compatible with currentColor fill. Dependencies: React. Used by: mining interfaces, blockchain visualizations, structural UI elements. |
| `external-link.tsx` | SVG React icon component for external link indication. Alternative external link icon with different visual style from LinkIcon. Theme-compatible currentColor implementation. Dependencies: React. Used by: external navigation, URL links, new tab/window indicators. |
| `receive.tsx` | SVG React icon component for receive/incoming transaction indicator. Arrow or symbol representing incoming payments, token receipts, or receive operations. Theme-compatible design. Dependencies: React. Used by: wallet interfaces, transaction history, receive dialogs, payment flows. |
| `replay.tsx` | SVG React icon component for replay/refresh functionality. Circular arrow or refresh symbol for retry operations, page refresh, or restart actions. Theme-compatible currentColor implementation. Dependencies: React. Used by: refresh buttons, retry mechanisms, reload interfaces, restart controls. |
| `send.tsx` | SVG React icon component for send/outgoing transaction indicator. Arrow or symbol representing outgoing payments, token sends, or transfer operations. Theme-compatible design for wallet interfaces. Dependencies: React. Used by: wallet send dialogs, transaction history, payment flows, transfer buttons. |
| `tari-outline.tsx` | React SVG icon component for Tari logo outline variant. Exports: TariOutlineSVG functional component returning SVG element with 38x39 viewBox. Used for branding, navigation elements, and logo displays throughout the application. Dependencies: React (implicit). Contains vector path data for Tari geometric logo design. Used by: header components, navigation, branding elements. Scalable SVG optimized for various display contexts with currentColor fill for theme compatibility. |
| `x-icon.tsx` | SVG React icon component for close/dismiss functionality. X or cross symbol for closing dialogs, dismissing notifications, or cancel operations. Standard close button icon with theme compatibility. Dependencies: React. Used by: close buttons, dialog dismissal, notification close, modal cancel actions. |

##### src/assets/lotties/

| File | Description |
|------|-------------|
| `VECTOR_SOON.json` | Lottie animation data file for 'VECTOR_SOON' animation sequence. Contains complex vector animation data with keyframes, shapes, transforms, and timing information for animated UI elements. JSON format with extensive animation properties including opacity, position, scale, and path morphing data. Used for loading states, feature announcements, or visual feedback in the application interface. Dependencies: Lottie animation library, @tari-project/tari-tower rendering engine. Used by: Lottie animation components, loading screens, visual transitions. |

##### src/components/AdminUI/

| File | Description |
|------|-------------|
| `AdminUI.tsx` | Developer admin UI panel component for application debugging and testing. Floating dropdown menu with theme controls, dialog testing, modal management, and UI component toggles. Uses @floating-ui for intelligent positioning, motion animations for smooth interactions, and modular group organization (ThemeGroup, DialogsGroup, GreenModalsGroup, OtherUIGroup). Provides developers with quick access to UI state manipulation and component testing. Dependencies: @floating-ui/react, motion/react, styled-components. Used by: development builds, UI testing, component debugging. eslint-disable for literal strings since it's dev-only. |
| `styles.ts` | Styled-components definitions for AdminUI development interface. Exports ToggleButton (fixed position overlay), MenuWrapper, MenuContent (floating menu), Button (with active state), CategoryLabel, and ButtonGroup components. Uses motion/react-m for animations. Provides dark theme styling for development tools overlay. Dependencies: styled-components, motion/react-m. Used by AdminUI components. |

###### src/components/AdminUI/groups/

| File | Description |
|------|-------------|
| `DialogsGroup.tsx` | Admin UI component for testing dialog functionality during development. Provides buttons to trigger various dialogs including Critical Problem, Auto Update, and External Dependencies dialogs. Uses app state and UI stores to manage dialog visibility. Part of the AdminUI debugging system for developers. |
| `GreenModalsGroup.tsx` | Admin UI component for testing green modal functionality during development. Provides toggle buttons for Paper Wallet, Staged Security, and Share Reward modals. Uses respective store hooks to manage modal visibility states. Part of the AdminUI debugging system for developers to test modal interactions. |
| `OtherUIGroup.tsx` | Admin UI component for testing miscellaneous UI functionality during development. Provides buttons to cycle through connection status states (connected, disconnected, disconnected-severe) and toggle universal exchange modal. Uses UI store and exchange store hooks. Part of the AdminUI debugging system for developers. |
| `ThemeGroup.tsx` | React component for admin UI theme switching. Exports ThemeGroup component with light/dark theme toggle buttons. Uses useUIStore hook for theme state and setUITheme action for theme changes. Includes styled buttons with active state indicators. Dependencies: @app/store/useUIStore, @app/store. Used by AdminUI for development theme switching. |

##### src/components/CharSpinner/

| File | Description |
|------|-------------|
| `CharSpinner.styles.ts` | Styled components for CharSpinner component variants (DrukWide/Poppins fonts). Defines wrapper, character, and motion components with responsive font sizes, spacing, and alignment. Supports different variants (simple vs default) and handles decimal/unit display for cryptocurrency amounts. Uses motion/react for animations. |
| `CharSpinner.tsx` | Animated character spinner component for numeric value displays with smooth digit transitions. Creates slot-machine style animations for numbers using spring transitions with stagger effects. Supports 'simple' and 'large' variants, customizable font sizing, XTM unit display, and decimal/comma handling. Renders digits 0-9 vertically and animates Y-position to show target value. Perfect for balance displays, earnings counters, and animated statistics. Dependencies: motion/react, CharSpinner.styles. Used by: wallet balance, mining earnings, statistics displays, animated counters. |

##### src/components/GreenModal/

| File | Description |
|------|-------------|
| `GreenModal.tsx` | Reusable modal component with green-themed styling and smooth animations. Features customizable width, padding, close button with icon, backdrop cover, and motion animations (slide-up entrance, fade exit). Provides clean modal interface for dialogs, forms, and content overlays. Uses motion/react for enter/exit transitions and styled-components for theming. Dependencies: motion/react, styled-components, CloseIcon. Used by: dialogs, forms, content overlays, confirmation modals, settings panels. |
| `styles.ts` | Styled-components definitions for GreenModal component. Contains styling for modal overlay, content containers, and green-themed modal elements. Dependencies: styled-components. Used by GreenModal component for consistent green-themed modal styling throughout the application. |

###### src/components/GreenModal/icons/

| File | Description |
|------|-------------|
| `CloseIcon.tsx` | SVG React component for close button icon used in GreenModal. Renders a circular close (X) icon with hover states. Uses currentColor for theme-aware coloring. Pure functional component with no dependencies. |

##### src/components/ToastStack/

| File | Description |
|------|-------------|
| `ToastStack.tsx` | Toast notification system component managing stack of toast messages. Handles toast display order (reversed), hover state management, and positioning during setup vs normal mode. Uses motion/react for animations and integrates with toast store for state management. Shows notifications with different types and timeouts. |
| `styles.tsx` | Styled-components definitions for ToastStack container component. Contains styling for toast stack positioning, container layout, z-index management, and toast spacing. Handles fixed positioning for notification overlay. Dependencies: styled-components. Used by ToastStack component for managing multiple toast notifications. |
| `useToastStore.tsx` | Zustand store for managing toast notifications. Exports useToastStore with toasts array, addToast, and removeToast actions. Includes ToastType interface (default, error, warning, success, info) and Toast interface with id, title, text, timeout properties. Auto-removes old toasts (max 3), filters loading toasts. Provides addToast/removeToast helper functions. Dependencies: zustand. Used throughout app for notifications. |

###### src/components/ToastStack/Toast/

| File | Description |
|------|-------------|
| `Toast.tsx` | Individual toast notification component with progress bar and auto-dismiss functionality. Handles positioning animations, hover state management, progress tracking, and timeout-based removal. Uses motion/react for animations and includes circular progress indicator. Supports different toast types and can be manually dismissed. |
| `motion.ts` | Motion configuration for Toast component animations. Defines framer-motion variants for toast enter/exit animations including slide-in effects, opacity transitions, and stagger delays. Dependencies: motion/react or framer-motion. Used by ToastStack/Toast components for smooth notification animations. |
| `styles.tsx` | Styled-components definitions for individual Toast component. Contains styling for toast container, content areas, close buttons, and toast type variants (error, warning, success, info). Includes responsive design and theme-based coloring. Dependencies: styled-components. Used by Toast component within ToastStack. |

##### src/components/TransactionModal/

| File | Description |
|------|-------------|
| `TransactionModal.tsx` | Reusable modal component for transaction-related dialogs. Exports TransactionModal with props: show, title, children, handleBack, handleClose, noClose, noHeader. Features animated overlay, back/close buttons, and flexible content area. Uses AnimatePresence for smooth transitions. Dependencies: motion/react, react-icons/io5, ./icons/CloseIcon, ./styles. Used by wallet and transaction components. |
| `styles.ts` | Styled-components definitions for TransactionModal component. Contains styling for modal wrapper, box wrapper, cover overlay, title, top wrapper, and top buttons. Includes animation support and responsive design. Dependencies: styled-components. Used by TransactionModal for consistent modal styling. |

###### src/components/TransactionModal/icons/

| File | Description |
|------|-------------|
| `CloseIcon.tsx` | Close icon component for TransactionModal. Exports CloseIcon as React component rendering X/close symbol. Provides consistent close button styling for modal dialogs. Dependencies: React. Used by TransactionModal component for close button functionality. |

##### src/components/VideoStream/

| File | Description |
|------|-------------|
| `VideoStream.tsx` | HLS video streaming component for Tari Universe supporting Cloudflare Stream integration. Uses HLS.js for HTTP Live Streaming with poster image fallback and comprehensive event handling. Features automatic HLS support detection, poster overlay management, and error recovery mechanisms. Provides play/pause controls, loading state management, and cleanup on unmount. Used for setup phase animations and blockchain visualization videos with smooth transitions and responsive design. Critical for engaging user experience during setup and providing visual feedback during application initialization. |
| `styles.ts` | Styled-components definitions for VideoStream component. Contains styling for video containers, overlays, controls, and responsive video layouts. Handles video player styling and media queries. Dependencies: styled-components. Used by VideoStream component for video display and controls. |

##### src/components/dialogs/

| File | Description |
|------|-------------|
| `ConfirmationDialog.tsx` | Reusable confirmation dialog component with confirm/cancel actions. Uses Dialog element wrapper, supports custom title and description text, includes close button and theme-aware styling. Uses i18n for localization, responsive layout with Stack components, and styled Button/TextButton elements. |
| `RestartDialog.tsx` | React dialog component for application restart confirmation. Exports RestartDialog component with confirmation message and restart/cancel actions. Handles app restart flow with user confirmation. Dependencies: React, dialog components. Used by app lifecycle management for restart functionality. |
| `SendLogsDialog.tsx` | React dialog component for sending application logs to support. Exports SendLogsDialog with log collection, privacy options, and submission functionality. Handles diagnostic data transmission with user consent. Dependencies: React, dialog components. Used by support/troubleshooting features. |

##### src/components/elements/

| File | Description |
|------|-------------|
| `Chip.tsx` | Reusable Chip UI component for displaying labels/tags. Exports Chip component with props: children, size (small/medium/large), background, color. Features responsive sizing, theme integration, and customizable styling. Uses styled-components with Poppins font. Dependencies: styled-components, React. Used throughout app for status indicators and labels. |
| `CircularProgress.tsx` | Animated circular progress indicator component for Tari Universe loading states. Features Motion (Framer Motion) animation with rotating circular path, dynamic stroke progression, and opacity transitions. Uses styled-components theming with success color palette and responsive sizing. Implements infinite rotation animation with realistic loading progression (pathLength, rotate, opacity). Provides consistent loading indicator for async operations throughout the application including mining startup, blockchain synchronization, and wallet operations. |
| `Divider.tsx` | Simple Divider UI component for visual separation. Exports Divider component creating horizontal or vertical separators with theme-based styling. Provides consistent spacing and visual breaks between UI sections. Dependencies: styled-components, React. Used throughout app for layout organization. |
| `LinearProgress.tsx` | LinearProgress UI component for progress indicators. Exports LinearProgress with value, max, color, and animation options. Features smooth animations, theme integration, and customizable styling. Used for loading states, download progress, and mining status indicators. Dependencies: styled-components, React. Used by setup, mining, and download components. |
| `Stack.tsx` | Flexible Stack layout component for arranging UI elements. Exports Stack component with direction (row/column), spacing, alignment, and flex properties. Provides consistent spacing and layout patterns throughout the application. Dependencies: styled-components, React. Used extensively for component layout and organization. |
| `ToggleSwitch.tsx` | Advanced ToggleSwitch UI component with custom styling. Exports ToggleSwitch with props: label, customDecorators, variant (solid/gradient), disabled, isLoading. Features keyboard navigation, custom decorators, theme integration, and accessibility support. Complex styled-components with state-based styling. Dependencies: styled-components, React, @app/components/elements/Typography. Used throughout app for settings and controls. |
| `Typography.tsx` | Reusable Typography component for consistent text rendering across Tari Universe. Supports HTML tag variants (h1-h6, p, span) with custom font family configuration and CSS properties pass-through. Implements memoization for performance optimization and uses DynamicTypography styled component for theming integration. Provides flexible text rendering with TypeScript type safety and consistent styling patterns. Core UI element used throughout the application for headings, body text, and specialized text display with proper semantic HTML structure and accessibility support. |
| `styled.ts` | Common styled-components and utility styles for UI elements. Contains shared styled components, theme utilities, and reusable style patterns used across multiple UI elements. Provides consistent styling foundation for the design system. Dependencies: styled-components. Used by various UI element components. |

###### src/components/elements/buttons/

| File | Description |
|------|-------------|
| `BaseButton.styles.ts` | Base styled-components definitions for button components. Contains foundational button styling including states (hover, active, disabled), theme integration, and common button variants. Provides consistent button styling patterns across the application. Dependencies: styled-components. Used by all button components as base styling. |
| `Button.tsx` | Reusable Button component for Tari Universe UI implementing comprehensive button functionality. Features variant system (primary, secondary, etc.), customizable sizing, icon positioning (start/end), loading states with loader support, and fluid width option. Includes color customization, background color override, and disable color option for special cases. Uses memoization for performance optimization and styled-components for theming integration. Implements accessibility standards and consistent design patterns across the application. Core UI element used throughout mining interface, wallet operations, settings, and navigation components. |
| `ExtendedButton.styles.ts` | Extended styled-components definitions for enhanced button variants. Contains advanced button styling including gradients, animations, size variants, and special button types. Builds upon BaseButton styles with additional features. Dependencies: styled-components. Used by specialized button components requiring advanced styling. |
| `IconButton.tsx` | Styled icon button component with size variants (small, medium, large) and active states. Features circular design with hover effects, gradient backgrounds, theme-aware colors, and disabled state handling. Supports primary/secondary variants with different dimensions and styling. |
| `SquaredButton.tsx` | Memoized SquaredButton component extending base button functionality. Exports SquaredButton using ButtonSquared styled component with props: children, variant, color (default 'grey'), size (default 'medium'). Uses ExtendedButtonProps interface and ExtendedButton.styles. Dependencies: React.memo, ./ExtendedButton.styles, ./button.types. Used for square-shaped buttons throughout app. |
| `TextButton.tsx` | TextButton component for text-only buttons. Exports TextButton using StyledTextButton styled component with props: children, variant, color (default 'tariPurple'), colorIntensity, size (default 'medium'). Uses ExtendedButtonProps interface and ExtendedButton.styles. Dependencies: ./ExtendedButton.styles, ./button.types. Used for text-style buttons throughout app. |
| `button.types.ts` | TypeScript type definitions for button components. Exports ButtonVariant, ButtonSize, IconPosition, CommonButtonProps, ButtonStyleProps, ExtendedButtonProps, and ExtendedButtonStyleProps interfaces. Defines button colors, variants (primary, secondary, outlined, gradient, etc.), sizes (xs to xlarge), and styling properties. Dependencies: React types, @app/theme/palettes/colors. Used by all button components for type safety. |

###### src/components/elements/dialog/

| File | Description |
|------|-------------|
| `Dialog.styles.ts` | Styled-components definitions for Dialog component system. Contains styling for dialog overlay, content wrapper, backdrop effects, and dialog positioning. Includes ContentWrapperProps interface for customization options like padding, overflow, border radius, and background transparency. Dependencies: styled-components. Used by Dialog.tsx for modal styling. |
| `Dialog.tsx` | Advanced Dialog system using Floating UI. Exports Dialog and DialogContent components with features: focus management, click-outside dismissal, portal rendering, accessibility support, and error state handling. Uses useDialog hook for state management and FloatingUI for positioning. Includes DialogContext for component communication. Dependencies: @floating-ui/react, @app/store/appStateStore, React. Used throughout app for modal dialogs. |

###### src/components/elements/gradientText/

| File | Description |
|------|-------------|
| `GradientText.tsx` | Animated gradient text component for visual effects. Exports GradientText with props: children, colors (default Tari theme colors), animationSpeed (default 8s), showBorder. Creates animated gradient text effects using CSS animations and linear gradients. Dependencies: React, ./styles. Used for branded text effects and visual highlights throughout the app. |
| `styles.ts` | Styled-components definitions for GradientText component. Contains AnimatedGradientText wrapper and TextContent with animated gradient effects, keyframe animations, and gradient text styling. Dependencies: styled-components. Used by GradientText.tsx for animated gradient text effects. |

###### src/components/elements/inputs/

| File | Description |
|------|-------------|
| `Input.styles.ts` | Styled-components definitions for Input component system. Contains InputWrapper, StyledInput, and StyledInputLabel with error states, focus styling, and theme integration. Handles input validation styling and accessibility features. Dependencies: styled-components. Used by Input.tsx for form input styling. |
| `Input.tsx` | Forwardable Input component with validation and adornments. Exports Input with props: labelText, endAdornment, hasError, subIcon, plus standard input attributes. Features number input validation, controlled value state, and error handling. Uses forwardRef for parent access. Dependencies: React, ./Input.styles. Used throughout app for form inputs and data entry. |
| `RadioButton.styles.ts` | Styled-components definitions for RadioButton component. Contains radio button styling including custom radio appearance, checked states, disabled states, and theme integration. Provides consistent radio button design across the application. Dependencies: styled-components. Used by RadioButton.tsx. |
| `RadioButton.tsx` | RadioButton component for single-choice selections. Exports RadioButton with standard radio input props plus custom styling. Features controlled state management, accessibility support, and theme integration. Dependencies: React, ./RadioButton.styles. Used in forms and option selection interfaces throughout the app. |
| `Select.styles.ts` | Styled-components definitions for Select component. Contains select dropdown styling including custom appearance, option styling, focus states, and disabled states. Provides consistent select input design across the application. Dependencies: styled-components. Used by Select.tsx for dropdown styling. |
| `Select.tsx` | Select dropdown component for option selection. Exports Select with custom dropdown functionality, option rendering, and theme integration. Features controlled state management, keyboard navigation, and accessibility support. Dependencies: React, ./Select.styles. Used throughout app for dropdown selections and option lists. |
| `TextArea.tsx` | TextArea component for multi-line text input. Exports TextArea with enhanced textarea functionality, auto-resize capabilities, character limits, and validation support. Features controlled state management and theme integration. Dependencies: React, styled-components. Used throughout app for longer text inputs and descriptions. |

###### src/components/elements/loaders/

| File | Description |
|------|-------------|
| `LoadingDots.tsx` | Animated loading dots component for indicating loading states. Exports LoadingDots as React.FC with SVG-based three-dot animation using staggered timing. Features keyframe animation with opacity effects and currentColor theming. Dependencies: React, styled-components. Used throughout app for loading indicators and progress states. |
| `SpinnerIcon.tsx` | Spinning loader icon component for loading states. Exports SpinnerIcon with rotating animation and theme support. Features smooth rotation animation and customizable size/color properties. Dependencies: React, styled-components. Used throughout app for loading spinners and busy indicators. |

##### src/components/exchanges/

| File | Description |
|------|-------------|
| `commonStyles.ts` | Shared styled-components for exchange-related components. Contains common styling patterns, layouts, and theme utilities used across all exchange interfaces. Provides consistent design language for trading and exchange features. Dependencies: styled-components. Used by various exchange components. |

###### src/components/exchanges/LinkModal/

| File | Description |
|------|-------------|
| `LinkModal.tsx` | Modal component for exchange linking functionality. Exports LinkModal providing interface for connecting external exchanges, wallet linking, and cross-platform integrations. Features modal dialog, validation, and connection flow management. Dependencies: React, modal components. Used by exchange integration features. |
| `styles.ts` | Styled-components definitions for LinkModal component. Contains modal styling for exchange linking interfaces, form layouts, connection status indicators, and responsive design. Dependencies: styled-components. Used by LinkModal.tsx for exchange connection modal styling. |

###### src/components/exchanges/connect/

| File | Description |
|------|-------------|
| `Connect.tsx` | Exchange connection component that handles user address submission and telemetry opt-in for connecting external exchanges to Tari Universe. Renders a form with address input validation, real-time Tari address verification, telemetry toggle, and submission handling. Exports: Connect component. Dependencies: react-hook-form, @tauri-apps/api/core for address verification, useExchangeStore for exchange data, debounced input handling, Typography, ToggleSwitch, LoadingDots. Used by: Exchange modal flows. Features address truncation display, dynamic CTA styling based on exchange branding, form validation with error states, and seedless UI activation upon successful connection. |
| `connect.styles.ts` | Styled components for the exchange connection form. Exports: Wrapper (container with subtle background), CTA (customizable button with exchange branding support), CTAText (white button text), ConnectForm (form layout), AddressInputWrapper (input container), AddressInputLabel (input label styling), AddressInput (input field with error states), OptInWrapper (telemetry toggle container). Uses theme system for colors, RGBA conversion utilities, and includes disabled states, error highlighting, and focus styles. Supports dynamic background colors from exchange branding data. |

###### src/components/exchanges/display/

| File | Description |
|------|-------------|
| `WalletDisplay.tsx` | Wallet display component for showing connected exchange information in collapsible format. Renders exchange logo, name, and user's Tari address with expand/collapse functionality. Exports: WalletDisplay (default). Dependencies: useExchangeStore for exchange data, useWalletStore for wallet address, truncateMiddle utility, ChevronSVG icon, Typography component. Uses Framer Motion for smooth height animations. Displays truncated address (6 characters middle) and includes proper ARIA accessibility attributes for the toggle button. Conditionally renders only when exchange data is available. |
| `wallet.styles.ts` | Styled components for the wallet display interface. Exports: WalletDisplayWrapper (main container with paper background and rounded corners), XCInfo (flex container for exchange logo and name), HeaderSection (header layout with space between elements). Uses theme palette for consistent background colors and spacing. Provides clean, card-like appearance with proper padding and flex layouts for the collapsible wallet information display. |

###### src/components/exchanges/universal/option/

| File | Description |
|------|-------------|
| `XCOption.tsx` | Individual exchange option component for the universal exchange selection interface. Renders clickable exchange option with logo, name, and selection functionality. Exports: XCOption component. Dependencies: ExchangeMinerAssets and ExchangeMiner types, styled components, ChevronSVG icon, exchange store actions, Tauri invoke for user selection handling. Props: content (exchange data), isCurrent (current selection indicator). Handles exchange miner selection by invoking 'user_selected_exchange' command and managing modal transitions between universal and specific exchange modals. |
| `styles.ts` | Styled components for individual exchange option cards. Exports: Wrapper (main container with conditional styling for current selection), Heading (typography for exchange names), ContentWrapper (layout for content and action button), XCContent (logo and name container). Features conditional styling based on selection state using green border and background for current selection, theme-based colors, and proper spacing. Uses convertHexToRGBA utility for subtle background colors and theme system integration. |

###### src/components/exchanges/universal/options/

| File | Description |
|------|-------------|
| `Options.tsx` | Universal exchange options list component that displays available exchange miners with Tari Universe as the default option. Exports: XCOptions component. Dependencies: useExchangeStore for exchange miners data, XCOption for individual options, Divider component, styled ListWrapper. Maps through available exchange miners to render option cards, includes Tari Universe as a selected current option, and conditionally shows divider when external exchanges are available. Provides the main interface for users to choose between local mining and external exchange connections. |
| `styles.ts` | Styled components for the exchange options list container. Exports: ListWrapper (main container for exchange option cards). Provides vertical flex layout with consistent spacing between options, rounded corners, paper background from theme, and proper padding. Creates a clean card-like container that holds all the exchange options in the universal selection interface. |

###### src/components/mining/timer/

| File | Description |
|------|-------------|
| `MiningTime.tsx` | Memoized mining timer component that displays elapsed mining time in various formats. Exports: MiningTime component with memo optimization. Dependencies: TimeSince utility type, styled components, i18next for translations. Props: variant ('primary' or 'mini'), timing (TimeSince object with time units). Features include tabular-nums font for consistent digit spacing, conditional display of days/hours/minutes/seconds, different layouts for primary vs mini variants, and proper zero-padding for mini format. Includes green dot indicator and translatable 'time-mining' heading. Handles edge cases for zero values and empty states. |
| `styles.ts` | Styled components for mining timer display with support for multiple variants. Exports: MiningTimeVariant type ('primary' \| 'mini'), Wrapper (main container), HeadingSection (header with dot indicator), Heading (secondary text label), MiniWrapper (compact timer variant), TimerDot (green status indicator), TimerUnitWrapper (time unit labels), TimerTextWrapper (timer digits container), SpacedNum (monospaced digit styling). Features variant-specific styling, tabular-nums font for consistent digit alignment, theme integration, and compact mini variant with background and different sizing. Includes proper spacing and alignment for professional timer appearance. |

##### src/components/svgs/

| File | Description |
|------|-------------|
| `CheckSvg.tsx` | Check mark SVG icon component. Exports CheckSvg rendering a checkmark symbol with currentColor theming. Used for success states, completed tasks, and validation indicators throughout the application. Dependencies: React. Used by various components for success/completion feedback. |
| `CubeSvg.tsx` | Cube SVG icon component for 3D/mining related interfaces. Exports CubeSvg rendering a geometric cube symbol with currentColor theming. Used in mining interfaces, 3D visualizations, and block-related components. Dependencies: React. Used by mining and visualization components. |
| `LoadingSvg.tsx` | Loading SVG icon component with animation support. Exports LoadingSvg rendering a loading/spinner symbol with rotation animation and currentColor theming. Used for loading states and progress indicators. Dependencies: React. Used throughout app for loading feedback. |
| `QuestionMarkSvg.tsx` | Question mark SVG icon component for help interfaces. Exports QuestionMarkSvg rendering a question mark symbol with currentColor theming. Used for help buttons, tooltips, and unclear state indicators. Dependencies: React. Used by help and info components throughout the app. |
| `XSpaceSvg.tsx` | X Space SVG icon component for social/space related interfaces. Exports XSpaceSvg rendering an X/Twitter Space symbol with currentColor theming. Used for social media integrations and space-related features. Dependencies: React. Used by social and communication components. |

##### src/components/tapplets/

| File | Description |
|------|-------------|
| `Tapplet.tsx` | Tapplet component for mini-application widgets. Exports Tapplet providing embedded application functionality within the main Tari Universe interface. Features iframe-like widget embedding, state management, and communication bridges. Dependencies: React. Used for extending app functionality with micro-applications. |

##### src/components/transactions/

| File | Description |
|------|-------------|
| `WalletSidebarContent.styles.ts` | Styled components for wallet sidebar layout. Exports: WalletSections (main container with padding and positioning), WalletGreyBox (themed container with dark/light mode support). Features responsive padding, full height layout, and theme-aware background colors - dark mode uses #2E2E2E and light mode uses #E9E9E9. Provides rounded corners, proper padding, and centered flex layout for wallet content. Supports optional absolute positioning via $absolute prop. |
| `WalletSidebarContent.tsx` | Main wallet sidebar content container that orchestrates transaction-related components and modals. Exports: WalletSidebarContent (memoized). Dependencies: Receive component, Wallet component, TransactionModal, SendModal, i18next translations, styled containers. Manages section state for navigation between 'history', 'send', and 'receive' views. Renders wallet interface in grey container, handles send modal visibility, and shows receive modal with translation support. Provides centralized state management for wallet section navigation and modal coordination. |
| `types.ts` | Core TypeScript interfaces for transaction UI components. Exports: TransationType ('mined'\|'sent'\|'received'\|'unknown'), HistoryListItemProps (for transaction history items), BridgeHistoryListItemProps (for bridge transactions), BaseItemProps/BridgeBaseItemProps (shared item properties). Dependencies: TransactionInfo, TransactionDirection, UserTransactionDTO from bridge API. Used by: transaction history components, bridge transaction displays. Provides type safety for transaction rendering. |

###### src/components/transactions/components/

| File | Description |
|------|-------------|
| `CheckIcon.tsx` | SVG check icon component for transaction and validation success states. Exports: CheckIcon (default). Renders a 24x25px circular green checkmark icon with #009E54 fill color. Used throughout the transaction flow to indicate successful operations, validated inputs, and completed states. Designed with professional styling for form validation feedback and status indicators across the wallet interface. |
| `TxInput.style.ts` | Comprehensive styled components for transaction input fields with advanced features. Exports: Wrapper (main container with error/disabled states), AccentWrapper (floating accent element), StyledInput (input field with multiple variants), ContentWrapper (input content container), IconWrapper (positioned icon container), Label (input label), CheckIconWrapper (animated validation icon), ErrorText (animated error messages), LabelWrapper (label and action container), SecondaryText (helper text). Features dark/light mode support, error state styling with red backgrounds, disabled state handling, secondary input variant, icon positioning, validation feedback, and smooth animations using Motion React. Supports various input sizes and includes proper accessibility attributes. |
| `TxInput.tsx` | Advanced transaction input component with validation, truncation, and accessibility features. Exports: TxInput component, TxInputProps interface. Dependencies: styled components, CheckIcon, AnimatePresence from Motion React. Props include value, onChange, validation states, icons, labels, error handling, focus management, and truncation options. Features include focus state management, automatic text truncation on blur, validation icon animations, error message animations, secondary field support, accessibility attributes, and support for both input and textarea modes. Used throughout wallet and transaction flows for address input, amount input, and form fields with comprehensive validation feedback. |

###### src/components/transactions/components/StatusHero/

| File | Description |
|------|-------------|
| `StatusHero.tsx` | Reusable hero section component for displaying status information with icon and text. Exports: StatusHero component. Props: icon (ReactNode), title (string), children (ReactNode for description text). Dependencies: styled components from local styles. Provides centered layout with icon, title, and descriptive content. Used across transaction flows to show status messages, success states, error states, and informational content with consistent visual hierarchy and spacing. |
| `styles.ts` | Styled components for StatusHero layout and typography. Exports: Wrapper (main flex container), IconWrapper (50px square icon container), TextWrapper (text content container), Title (18px bold heading), Text (12px descriptive text). Features centered column layout with proper spacing, responsive icon sizing, consistent typography hierarchy, and proper alignment. Provides professional status display with clear visual separation between icon and text content. |

###### src/components/transactions/components/StatusList/

| File | Description |
|------|-------------|
| `StatusList.tsx` | Reusable status list component for displaying key-value pairs with optional external links and status indicators. Exports: StatusList component, StatusListEntry interface. Dependencies: styled components, SendStatus type, ExternalLinkIcon, Tauri shell plugin for external links. Props: entries array with label, value, valueRight, status, externalLink properties. Features status-based styling (processing/completed colors), external link handling with icon, value truncation, and automatic filtering of empty entries. Used throughout transaction flows for displaying transaction details, fees, addresses, and other status information. |
| `styles.ts` | Styled components for StatusList layout and typography. Exports: Wrapper (main container), Entry (individual status entry with border), Label (12px secondary text), Value (14px primary text with status colors), ValueLeft (left-aligned value container), ValueRight (right-aligned secondary info), ExternalLink (clickable link styling). Features status-specific colors for processing (#ff7700) and completed (#36c475) states, proper text wrapping and overflow handling, consistent spacing with alpha-based borders, and responsive layout with space-between alignment. |

###### src/components/transactions/components/StatusList/icons/

| File | Description |
|------|-------------|
| `ExternalLinkIcon.tsx` | SVG external link icon component for status list entries. Exports: ExternalLinkIcon (default). Renders a 15px external link icon with arrow and box design using currentColor for theme integration. Features 25% stroke opacity, rounded line caps and joins, and proper stroke width. Used in StatusList component to indicate clickable external links such as blockchain explorers, transaction details, and external service links. |

###### src/components/transactions/components/Tabs/

| File | Description |
|------|-------------|
| `tab.styles.ts` | Styled components for transaction tab navigation and headers. Exports: HeaderLabel (secondary text), TabHeader (section header with border), BottomNavWrapper (navigation container), NavButtonContent (button content layout), NavButton (outlined navigation button), StyledIconButton (circular icon button), AddressWrapper (address display container). Features conditional styling for hidden states, theme-aware colors with RGBA conversion, proper spacing and transitions, capitalized text transform, and responsive button states with opacity changes. Used across wallet and transaction interfaces for consistent navigation styling. |

###### src/components/transactions/history/

| File | Description |
|------|-------------|
| `BridgeHoveredItem.tsx` | Memoized hover overlay component for bridge transaction list items. Exports: BridgeItemHover (memoized). Dependencies: styled components from ListItem.styles, Motion React for animations. Props: button (optional ReactNode). Features smooth fade-in animations for hover state with opacity and x-axis translations. Used by BridgeListItem to show action buttons (like view details) when user hovers over bridge transaction entries. Provides consistent hover interaction patterns across bridge transaction history. |
| `BridgeListItem.tsx` | Bridge transaction list item component with hover interactions and detail viewing. Exports: BridgeHistoryListItem (memoized). Dependencies: formatting utilities, bridge transaction types, hover component, styled components, wallet store, i18next, Button component, timestamp helpers. Features include XTM-to-WXTM bridge transaction display, balance hiding support, hover state management, view details functionality, transaction amount formatting, timestamp handling, and 'new' badge for recent transactions. Uses BaseItem for consistent layout and provides detailed transaction information with proper event handling for modal interactions. |
| `HistoryList.tsx` | Main transaction history list component with infinite scrolling and mixed transaction types. Exports: HistoryList (memoized), TransactionDetailsItem type. Dependencies: InfiniteScroll, wallet stores, transaction types, list components, bridge API types, helper functions. Features include combined transaction and bridge transaction display, infinite scroll pagination, transaction merging logic for bridge operations, new transaction badges, wallet scanning progress, empty state handling, transaction details modal, and address parsing for emoji display. Handles complex transaction sorting, filtering, and state management for both regular Tari transactions and bridge transactions with WXTM. |
| `HoveredItem.tsx` | Memoized hover overlay component for regular transaction list items with sharing and replay features. Exports: ItemHover (memoized). Dependencies: transaction types, airdrop store, share reward store, blockchain visualization store, styled components, gem image asset, ReplaySVG icon. Props: item (TransactionInfo), button (optional ReactNode). Features sharing functionality with gem rewards, blockchain visualization replay, conditional rendering based on login status and sharing settings, smooth animations, and proper event handling. Provides interactive transaction actions with gamification elements and educational blockchain visualization features. |
| `ListItem.styles.ts` | Comprehensive styled components for transaction history list items with hover interactions and animations. Exports: ItemWrapper (main animated container), HoverWrapper (overlay with backdrop blur), ContentWrapper (layout container), Content (flex content areas), BlockInfoWrapper (transaction info container), TitleWrapper/TimeWrapper (typography components), ValueWrapper (amount display), Chip (status badge), CurrencyText (XTM label), ValueChangeWrapper (positive/negative indicators), ReplayButton (circular action button), ButtonWrapper (animated button container), FlexButton (gradient action button), GemPill/GemImage (reward display), PlaceholderItem (loading skeleton). Features dark/light mode support, Motion React animations, theme integration, status-based colors, hover effects, and responsive design. |
| `ListItem.tsx` | Memoized transaction history list item component with hover interactions and transaction details. Exports: HistoryListItem (memoized). Dependencies: formatting utilities, transaction types, hover component, styled components, UI store, translation, Button, transaction status utilities. Features include BaseItem for consistent layout, transaction type detection, balance hiding support, hover state management, view details functionality for non-mined transactions, transaction title and amount formatting, timestamp handling, and 'new' badge for recent transactions. Handles inbound/outbound direction indicators and proper event handling for modal interactions. |
| `TxHistory.styles.ts` | Styled components for transaction history list layout and scrolling. Exports: ListWrapper (main scrollable container with mask gradient), ListItemWrapper (item container with spacing). Features vertical scrolling with hidden overflow, mask-image for fade-out edges, proper flex growth, relative positioning, and consistent gap spacing. The mask creates smooth fade effects at top and bottom edges for professional scrolling appearance. |
| `helpers.ts` | Utility functions for transaction history data processing and type checking. Exports: formatTimeStamp (locale-aware time formatting), findFirstNonBridgeTransaction, findByTransactionId, isBridgeTransaction/isTransactionInfo (type guards), getTimestampFromTransaction. Features include configuration-aware date formatting using user's language preferences, transaction type differentiation between regular Tari transactions and bridge transactions, timestamp conversion for bridge transaction dates, transaction filtering utilities, and type safety for mixed transaction arrays. Uses config store for internationalization and provides consistent time display across the application. |

###### src/components/transactions/history/details/

| File | Description |
|------|-------------|
| `TransactionDetails.tsx` | Transaction details modal component with comprehensive transaction information display and raw data copy functionality. Exports: TransactionDetails component. Dependencies: TransactionInfo and BackendBridgeTransaction types, TransactionModal, StatusList, copy to clipboard hook, getListEntries utility. Features include expandable transaction details view, hidden information toggle via triple-click, raw transaction data copy with icon feedback, comprehensive status list display, and support for both regular and bridge transactions. Provides detailed transaction inspection capabilities with user-friendly formatting and accessibility features. |
| `getListEntries.tsx` | Complex transaction details parsing utility for converting transaction data into display-ready status list entries. Exports: getListEntries function. Dependencies: i18next, helper functions, formatting utilities, StatusListEntry type, network utilities, stores. Features include TypeScript type mapping for transaction fields, key translation system, value parsing for amounts/fees/timestamps, payment reference calculation with confirmation logic, bridge transaction handling, external link generation for blockchain explorer, emoji address formatting, and conditional field display based on transaction type. Supports both regular Tari transactions and bridge transactions with different field handling and formatting. |
| `styles.ts` | Styled components for transaction details modal content. Exports: Wrapper (main container with max height and scrolling), EmojiAddressWrapper (emoji address display with spacing). Features responsive height constraints (80vh max), vertical scrolling for long content, proper line height and letter spacing for emoji addresses, and consistent typography sizing for emoji display components. |

###### src/components/transactions/receive/

| File | Description |
|------|-------------|
| `Address.style.ts` | Comprehensive styled components for receive address display with QR code and tooltip functionality. Exports: QRContainer (dark container), QROutside/QRSizer (QR code layout), AddressContainer (white address container), ContentWrapper (layout), EmojiAddressWrapper (emoji styling), AddressWrapper (address text), ToggleWrapper (emoji toggle), ImgOption/TextOption (toggle decorators), Label (field labels), CopyAddressButton (action button with states), Tooltip components (animated tooltip with arrow). Features dark theme for QR section, responsive design with media queries, emoji address formatting with letter spacing, animated tooltip positioning, button state transitions, and proper aspect ratios for QR codes. |
| `Address.tsx` | Address display component with emoji/text toggle and informational tooltip. Exports: Address component. Dependencies: wallet store, styled components, truncate utility, Typography, ToggleSwitch, emoji-regex, AnimatePresence. Props: useEmoji (boolean), setUseEmoji (setter). Features include dual address display modes (Base58 and emoji), emoji parsing with truncation to show first/last 4 emojis, toggle switch with custom decorators (Tx icon and Yat hand image), animated tooltip explaining emoji IDs, address truncation for readability, and internationalized tooltips. Provides user-friendly address sharing with educational context about emoji addressing. |
| `AddressQRCode.tsx` | QR code generator component for Tari wallet addresses with embedded logo and address display. Exports: AddressQRCode component. Dependencies: styled components, wallet/mining stores, react-qrcode-logo, Address component. Props: useEmoji (boolean), setUseEmoji (setter). Features include network-aware QR code generation with tari:// protocol scheme, high error correction level (H), Tari logo embedding with circular padding, dots style QR code, responsive sizing (400px with 100% scaling), quiet zone padding, and integrated address display component. Generates scannable QR codes for easy address sharing with professional Tari branding. |
| `CopyAddress.tsx` | Copy address button component for receive interface with visual feedback. Exports: CopyAddress component. Dependencies: wallet store, copy to clipboard hook, i18next, styled button component. Props: useEmoji (boolean to determine which address format to copy). Features include dual address format support (Base58 or emoji), visual feedback with color change on successful copy, internationalized button text with success states, and automatic clipboard copying. Provides user-friendly address sharing functionality with clear visual confirmation. |
| `Receive.tsx` | Main receive transaction component orchestrating QR code display and address copying. Exports: Receive component. Dependencies: styled wrapper, AddressQRCode, CopyAddress components. Manages emoji toggle state shared between QR code and copy functionality. Features include centralized state management for address display format (emoji vs Base58), integration of QR code generation with address display, and copy address functionality. Provides complete receive transaction interface with coordinated components. |
| `receive.styles.ts` | Minimal styled components for receive transaction layout. Exports: Wrapper (main container). Provides simple vertical flex layout with 8px gap between QR code and copy button components. Ensures consistent spacing and alignment for the receive transaction interface. |

###### src/components/transactions/send/

| File | Description |
|------|-------------|
| `FormField.tsx` | Form field wrapper component for send transaction inputs using React Hook Form. Exports: FormField component. Dependencies: react-hook-form Controller, TxInput component, i18next, send types. Props include control, name, validation, event handlers, icons, and styling options. Features include form validation with field-specific rules (numeric validation for amounts), internationalized labels and placeholders, error handling with custom and validation messages, change event processing with NaN prevention for amount fields, and integration with TxInput for consistent styling. Provides standardized form field behavior across send transaction forms. |
| `Send.styles.ts` | Styled components for send transaction form layout with loading and error states. Exports: Wrapper (main container with conditional states), StyledForm (form layout), FormFieldsWrapper (field container), BottomWrapper (action area). Features loading state with opacity reduction, error state with colored backgrounds (dark/light mode support), full-height flex layout with proper spacing, form field grouping with consistent gaps, and bottom section for action buttons. Provides responsive layout for send transaction interface with visual state feedback. |
| `SendForm.tsx` | Transaction send form component with address and amount validation. Handles form input management using react-hook-form, validates Tari addresses and amounts via Tauri commands, includes debounced address validation, balance checking, and max amount functionality. Features i18n support and responsive form layout. |
| `SendModal.tsx` | Main send transaction modal component with multi-step form flow and transaction processing. Exports: SendModal component, SendStatus type. Dependencies: React Hook Form, TransactionModal, SendForm, SendReview, Tauri invoke, store actions. Features include step-by-step transaction flow (fields → reviewing → processing → completed), form validation and error handling, Tauri backend integration for sending transactions, modal state management with dynamic titles, form reset functionality, and comprehensive error handling with store integration. Manages the complete send transaction lifecycle from form input to completion confirmation. |
| `types.ts` | TypeScript type definitions for transaction sending functionality. Exports: SendInputs interface (message, address, amount fields), InputName type (keyof SendInputs). Used by: send transaction components for form validation and data structure. Defines structure for user input during transaction creation. |

###### src/components/transactions/send/SendReview/

| File | Description |
|------|-------------|
| `SendReview.tsx` | Send transaction review and status component with step-specific UI rendering. Exports: SendReview component. Dependencies: Button, status icons (Processing, Completed), TariPurpleLogo, StatusHero, StatusList, formatting utilities. Props include status, amount, address, message, fees, and handlers. Features include review screen with amount display and transaction details, processing state with animated icon and status information, completed state with success confirmation and formatted amount display, conditional status list entries based on transaction phase, and dynamic button states. Provides comprehensive transaction confirmation and status tracking interface. |
| `styles.ts` | Styled-components for the SendReview component in transaction sending flow. Exports: Wrapper (main container with flex column layout), WhiteBox (themed background container), WhiteBoxLabel/WhiteBoxValue (label-value pairs), Amount (large number display with custom typography), Currency (small currency text with opacity). Used by: SendReview component for displaying transaction details before confirmation. Features theme-aware styling and consistent spacing. |

###### src/components/transactions/send/SendReview/icons/

| File | Description |
|------|-------------|
| `CompletedIcon.tsx` | SVG completed transaction icon component for send review success state. Exports: CompletedIcon (React.FC). Renders a 49x50px green checkmark in circle icon (#17CB9B fill) with detailed path geometry for professional success indication. Used in SendReview component to indicate successful transaction completion. Features clean geometric design with proper fill rules and clip paths for crisp display. |
| `ProcessingIcon.tsx` | SVG processing transaction icon component for send review loading state. Exports: ProcessingIcon (React.FC). Renders a 63x64px orange (#FF7700) circular progress indicator with partial stroke and center clock/timer design. Features opacity-reduced circular progress arc, central clock face with hour hand, professional loading animation appearance. Used in SendReview component to indicate transaction is being processed by the network. |
| `TariPurpleLogo.tsx` | SVG Tari logo icon component in purple branding colors for transaction review. Exports: TariPurpleLogo (default). Renders a 26x26px Tari geometric logo with purple background (#614CFF) and white foreground design. Features the distinctive Tari crystal/gem design with clip path masking, professional branding appearance. Used in SendReview component to represent Tari currency and provide brand recognition during transaction review process. |

###### src/components/transactions/wallet/

| File | Description |
|------|-------------|
| `ArrowRight.tsx` | Simple SVG arrow icon component pointing right. Exports: ArrowRight functional component rendering a 6x10 chevron SVG with currentColor stroke. Used by: wallet sync buttons and navigation elements requiring right-pointing indicator. Responsive to parent text color via currentColor. |
| `Wallet.tsx` | Main wallet component managing transaction interface, balance display, and navigation. Exports: Wallet component with section-based navigation. Dependencies: WalletBalanceMarkup, HistoryList, Swap component, ArrowRight icon. Features: wallet address display with copy functionality, send/receive navigation, swap integration with AnimatePresence transitions, paper wallet sync, conditional UI based on airdrop permissions. Used by: main transaction interface. Integrates balance, history, and transaction actions in unified interface. |
| `transitions.ts` | Motion/Framer Motion transition configurations for wallet and swap components. Exports: walletTransition (slide animation for wallet view), swapTransition (slide animation for swap view). Features smooth opacity and x-axis transitions with 0.2s duration and easeInOut easing. Used by: Wallet component's AnimatePresence for smooth view transitions between wallet and swap modes. |
| `wallet.styles.ts` | Styled-components for wallet interface layout and buttons. Exports: Wrapper (main container with flex column and height constraints), BuyTariButton (green CTA button for purchasing), TabsWrapper/TabsTitle (header section with balance display), SyncButton (phone sync button with hover effects), WalletWrapper/SwapsWrapper (motion.div containers for animations). Dependencies: convertHexToRGBA utility, motion/react. Features theme-aware colors, hover transitions, and responsive design for wallet navigation. |

###### src/components/transactions/wallet/Exchanges/

| File | Description |
|------|-------------|
| `ExchangesUrls.tsx` | Component for displaying exchange platform URLs and buy buttons in wallet interface. Exports: ExchangesUrls component showing exchange logos and "Buy Tari" CTA. Dependencies: useExchangeStore for exchange data, ChevronSVG icon, i18n translations. Used by: Wallet component for purchasing options. Features logo stacking, conditional rendering based on exchange availability, opens XC modal on click. |
| `styles.ts` | Styled-components for ExchangesUrls component. Exports: XCWrapper (container), SectionDivider (separator with "or" text and lines), XCButton (clickable exchange button), XCLogos/XCLogo (stacked exchange logos with positioning), CopyWrapper (text container), Title/Subtitle (typography components), ChevronCTA (rotated chevron). Features theme-aware colors, hover states, logo stacking with z-index, and dynamic background colors from exchange data. |

###### src/components/transactions/wallet/Swap/

| File | Description |
|------|-------------|
| `Swap.styles.ts` | Styled-components for the Swap component interface. Exports: SwapsContainer (main container), BackButton (styled back button with border), SectionHeaderWrapper (header layout), SwapsIframe (responsive iframe with dynamic height), IframeContainer (iframe wrapper with centering). Features conditional styling based on wallet connect state, dynamic height adjustment, rounded corners, and responsive design for embedded swap interface. |
| `Swap.tsx` | Main swap component for cryptocurrency exchange functionality within wallet. Exports: Swap component managing iframe-based swap interface. Dependencies: useIframeMessage hook, SwapConfirmation/ProcessingTransaction dialogs, useIframeUrl. Features: theme synchronization with embedded iframe, message handling for swap lifecycle (confirm, approve, process), wallet connect integration, fullscreen support, error handling. Used by: Wallet component when swapping is enabled. Handles complex iframe communication for external swap providers. |

###### src/components/wallet/seedwords/

| File | Description |
|------|-------------|
| `SeedWords.tsx` | Controller component for viewing and editing wallet seed words (both Tari and Monero). Exports: SeedWords component with edit/view modes. Dependencies: Display/Edit components, useGetSeedWords hook, react-hook-form, wallet store actions. Features: toggle between display and edit modes, copy functionality, seed word import with confirmation dialog, form validation, loading states. Used by: wallet settings for seed phrase management. Supports both viewing existing seeds and importing new ones. |
| `styles.ts` | Main styled-components container for SeedWords component. Exports: Wrapper component providing base layout and styling for the seed words controller. Used by: SeedWords component for consistent layout structure. |

###### src/components/wallet/seedwords/components/

| File | Description |
|------|-------------|
| `Display.tsx` | Display component for showing seed words in a grid format with show/hide functionality. Used by: SeedWords controller component for viewing seed phrases. Features masking/unmasking of seed words, loading states, and different layouts for Tari vs Monero seeds. Provides secure display with user-controlled visibility. |
| `Edit.tsx` | Edit component for entering/modifying seed words in wallet import flow. Used by: SeedWords controller for seed phrase input. Features textarea input with word count validation, form integration with react-hook-form, and real-time validation feedback. Enables users to import or modify existing seed phrases. |
| `display.styles.ts` | Styled-components for seed words Display component. Provides styling for seed word grid layout, individual word cells, masking overlays, and loading states. Features responsive grid design, hover effects, and theme-aware colors for secure seed phrase presentation. |
| `edit.styles.ts` | Styled-components for seed words Edit component. Exports: Form container and textarea styling for seed phrase input. Features proper form layout, consistent spacing, and theme integration for seed word editing interface. |

##### src/containers/floating/

| File | Description |
|------|-------------|
| `FloatingElements.tsx` | Central floating UI elements container for Tari Universe managing all modal dialogs and overlay components. Uses FloatingTree for proper modal layering and includes comprehensive dialog system: settings modal, staged security, auto-update dialog, critical error/problem dialogs, external dependencies, paper wallet, share rewards, and Shell of Secrets. Features development-only AdminUI, toast notifications, and specialized dialogs for ludicrous mode confirmation, warmup guidance, and exchange integration. Provides centralized modal management ensuring proper z-index ordering, focus management, and accessibility for all overlay interfaces throughout the application. |

###### src/containers/floating/AutoUpdateDialog/

| File | Description |
|------|-------------|
| `AutoUpdateDialog.styles.ts` | Styled-components for AutoUpdateDialog layout. Exports: ButtonsWrapper for dialog action buttons. Provides consistent button spacing and layout for update confirmation dialog. |
| `AutoUpdateDialog.tsx` | Dialog component for handling automatic application updates. Exports: AutoUpdateDialog component managing update flow. Dependencies: Tauri event system, Dialog components, SquaredButton. Features: listens for update events from backend, shows download progress, handles user consent for updates, error handling for failed updates. Used by: main app for update notifications. Integrates with Tauri's auto-updater system for seamless updates. |
| `UpdatedStatus.tsx` | Component for displaying download progress during auto-updates. Used by: AutoUpdateDialog to show download status with progress bar and percentage. Provides visual feedback during update download process. |

###### src/containers/floating/CriticalErrorDialog/

| File | Description |
|------|-------------|
| `CriticalErrorDialog.tsx` | Dialog component for handling critical application errors that require app termination. Exports: CriticalErrorDialog component. Dependencies: Tauri invoke API, app state store. Features: displays critical error message (primarily for macOS installation location errors), exit application button, loading state during exit. Used by: main app when critical errors occur that prevent normal operation. Note: specifically designed for scenarios where app must close (e.g., incorrect installation directory). |

###### src/containers/floating/CriticalProblemDialog/

| File | Description |
|------|-------------|
| `CriticalProblemDialog.tsx` | Dialog component for handling critical problems with recovery options. Exports: CriticalProblemDialog component. Dependencies: Tauri invoke API, useCopyToClipboard hook, app state store. Features: displays critical problem details, send feedback with logs functionality, restart/close options, copy logs submission ID. Used by: main app for recoverable critical issues. Provides user options to send logs, restart, or close when serious problems occur. |

###### src/containers/floating/EXModal/

| File | Description |
|------|-------------|
| `EXModal.tsx` | Modal component for displaying exchange information and URLs. Exports: EXModal component. Dependencies: useExchangeStore for exchange data, Hero/Content components, LoadingDots. Features: displays exchange details with hero image and content sections, loading state while fetching data, custom border radius styling. Used by: exchange URL interfaces when user clicks on exchange CTAs. Shows detailed exchange information for purchasing cryptocurrency. |
| `RingsSVG.tsx` | SVG component rendering decorative rings graphic for exchange modal. Used by: EXModal components for visual enhancement. Provides animated or static ring graphics for exchange interface branding. |
| `styles.ts` | Main styled-components for EXModal container. Exports: Wrapper providing overall layout and styling for exchange modal. Features modal structure, spacing, and container styling for hero and content sections. |

###### src/containers/floating/EXModal/components/

| File | Description |
|------|-------------|
| `Content.tsx` | Content section component for exchange modal displaying exchange details, features, and action buttons. Used by: EXModal for main content area. Shows exchange information, supported features, and purchase/navigation options. |
| `Hero.tsx` | Hero section component for exchange modal displaying branded header with logo and colors. Used by: EXModal for top section. Features exchange branding with hero image, primary/secondary color theming from exchange data. |
| `content.styles.ts` | Styled-components for exchange modal Content component. Provides styling for content layout, text sections, buttons, and spacing within exchange modal. Features responsive design and theme integration. |
| `hero.styles.ts` | Styled-components for exchange modal Hero component. Provides styling for hero section layout, background colors, logo positioning, and branding elements. Features dynamic theming based on exchange color data. |

###### src/containers/floating/ExternalDependenciesDialog/

| File | Description |
|------|-------------|
| `ExternalDependenciesDialog.tsx` | Dialog component for managing external dependencies (XMRig, GPU miners, etc.) required for mining operations. Features dependency status checking, download/install options, progress tracking, and dependency validation. Used by: mining setup flow to ensure all required external binaries are available. |
| `ExternalDependenciesDialog.utils.ts` | Utility functions for ExternalDependenciesDialog component. Contains helper functions for dependency validation, status checking, download logic, and dependency management operations. Used by: ExternalDependenciesDialog for external binary operations. |
| `ExternalDependencyCard.tsx` | Card component for displaying individual external dependency status and actions. Shows dependency name, status, download progress, install/update buttons. Used by: ExternalDependenciesDialog to render each dependency item with appropriate actions and status indicators. |

###### src/containers/floating/LudicrousCofirmationDialog/

| File | Description |
|------|-------------|
| `LudicrousCofirmationDialog.tsx` | Confirmation dialog for enabling "Ludicrous" mining mode with performance warnings. Features warning messages about potential system stress, confirmation buttons, and user consent handling. Used by: mining settings when user attempts to enable maximum performance mining mode. |
| `styles.ts` | Styled-components for LudicrousCofirmationDialog. Provides styling for confirmation dialog layout, warning text, buttons, and visual emphasis for the ludicrous mode warning interface. |

###### src/containers/floating/PaperWalletModal/

| File | Description |
|------|-------------|
| `PaperWalletModal.tsx` | Modal component for paper wallet (mobile app) synchronization flow. Exports: PaperWalletModal with section-based navigation. Dependencies: ConnectSection, QRCodeSection, usePaperWalletStore. Features: multi-section interface (Connect/QRCode), dynamic modal width, AnimatePresence transitions. Used by: wallet sync functionality to connect with Tari mobile app. Guides users through QR code scanning process. |
| `styles.ts` | Main styled-components for PaperWalletModal container. Provides overall modal styling, layout structure, and theme integration for paper wallet synchronization interface. |

###### src/containers/floating/PaperWalletModal/icons/

| File | Description |
|------|-------------|
| `AppleStoreIcon.tsx` | SVG icon component for Apple App Store download link. Used by: PaperWalletModal to display App Store download option for Tari mobile app. |
| `GoogleStoreIcon.tsx` | SVG icon component for Google Play Store download link. Used by: PaperWalletModal to display Play Store download option for Tari mobile app. |
| `HideIcon.tsx` | SVG icon component for hiding/masking sensitive information. Used by: PaperWalletModal to toggle visibility of QR codes or sensitive data during wallet sync process. |
| `ShowIcon.tsx` | SVG icon component for showing/revealing sensitive information. Used by: PaperWalletModal to toggle visibility of QR codes or sensitive data during wallet sync process. |

###### src/containers/floating/PaperWalletModal/sections/ConnectSection/

| File | Description |
|------|-------------|
| `ConnectSection.tsx` | First section of paper wallet modal explaining mobile app connection process. Features: introduction text, mobile app download links, instructions for proceeding to QR code section. Used by: PaperWalletModal as initial connection screen before QR code display. |
| `styles.ts` | Styled-components for ConnectSection component. Provides layout, spacing, buttons, and visual design for the initial connection screen in paper wallet modal. |

###### src/containers/floating/PaperWalletModal/sections/ConnectSection/QRTooltip/

| File | Description |
|------|-------------|
| `QRTooltip.tsx` | Tooltip component explaining QR code functionality in paper wallet connection flow. Provides contextual help and instructions for QR code scanning process. Used by: ConnectSection for user guidance. |
| `styles.ts` | Styled-components for QRTooltip component. Provides tooltip styling, positioning, and visual design for QR code help information. |

###### src/containers/floating/PaperWalletModal/sections/QRCodeSection/

| File | Description |
|------|-------------|
| `QRCodeSection.tsx` | Second section of paper wallet modal displaying QR code for mobile app scanning. Features: QR code generation, wallet sync data, completion handling. Used by: PaperWalletModal for actual wallet synchronization via QR scanning. |
| `styles.ts` | Styled-components for QRCodeSection component. Provides styling for QR code display, container layout, and visual elements for the QR scanning interface. |

###### src/containers/floating/ReleaseNotesDialog/

| File | Description |
|------|-------------|
| `ReleaseNotesDialog.tsx` | Dialog component for displaying application release notes and changelog information. Features markdown content rendering, version history, and new features highlights. Used by: settings and update flows to show release information. |
| `styles.ts` | Styled-components for ReleaseNotesDialog. Provides layout and styling for release notes content, version headers, markdown rendering, and dialog structure. |

###### src/containers/floating/ResumeApplicationModal/

| File | Description |
|------|-------------|
| `ResumeApplicationModal.tsx` | Modal component for resuming application after it was paused or minimized. Features application state restoration, resume confirmation, and state management. Used by: app lifecycle management when returning from suspended state. |
| `styles.ts` | Styled-components for ResumeApplicationModal. Provides modal layout, button styling, and visual design for application resume interface. |

###### src/containers/floating/Settings/

| File | Description |
|------|-------------|
| `SettingsModal.styles.ts` | Styled-components for main SettingsModal layout. Exports: Container (main modal layout), ContentContainer (content area), HeaderContainer (header with title/close), SectionWrapper (section content), EndContainer (header end actions). Features responsive layout, navigation structure, and theme integration. |
| `SettingsModal.tsx` | Main settings modal component with navigation and section management. Exports: SettingsModal component. Dependencies: settings sections, Navigation component, RestartDialog. Features: tabbed navigation between settings categories (general, mining, connections, etc.), section state management, version display, restart dialog integration. Used by: main app for comprehensive settings management. Central hub for all application configuration. |
| `types.ts` | TypeScript type definitions for settings sections and navigation. Exports: SettingsType, SETTINGS_TYPES array defining available settings sections (general, mining, connections, wallet, etc.). Used by: SettingsModal for type-safe section management. |

###### src/containers/floating/Settings/components/

| File | Description |
|------|-------------|
| `Card.component.tsx` | Reusable card component for settings sections. Provides consistent container styling and layout for settings groups. Used throughout settings sections for organized content presentation. |
| `LanguageDropdown.tsx` | Dropdown component for language selection in settings. Features: language list with flags, i18n integration, language switching functionality. Used by: GeneralSettings for internationalization configuration. |
| `Navigation.styles.ts` | Styled-components for settings navigation sidebar. Provides styling for navigation tabs, active states, hover effects, and navigation layout structure. |
| `Navigation.tsx` | Navigation sidebar component for settings modal. Features: tabbed navigation between settings sections, active state management, section icons. Used by: SettingsModal for section switching between general, mining, connections, wallet, etc. |
| `OpenSettingsButton.tsx` | Button component for opening settings modal from other parts of the application. Provides consistent trigger for settings access throughout the UI. |
| `Settings.styles.tsx` | Global styled-components for settings interface. Provides base styling, common components, and shared design elements used across all settings sections. |
| `SettingsGroup.styles.ts` | Styled-components for settings group containers. Provides styling for grouped settings sections, dividers, advanced settings sections, and consistent spacing between related settings. |
| `ThemeSelector.tsx` | Component for theme selection (light/dark mode) in settings. Features: theme preview, toggle functionality, theme persistence. Used by: GeneralSettings for application theme configuration. |

###### src/containers/floating/Settings/sections/

| File | Description |
|------|-------------|
| `index.ts` | Export index file for settings sections. Re-exports all settings section components (GeneralSettings, MiningSettings, ConnectionsSettings, etc.) for simplified imports. Standard module barrel export pattern. |

###### src/containers/floating/Settings/sections/airdrop/

| File | Description |
|------|-------------|
| `AirdropSettings.tsx` | Settings section for airdrop-related configuration. Features: airdrop permissions, notification settings, eligibility status. Used by: SettingsModal for managing airdrop participation and related features. |
| `ApplyInviteCode.tsx` | Component for applying airdrop invite codes. Features: invite code input, validation, submission, and status feedback. Used by: AirdropSettings for invite code redemption functionality. |

###### src/containers/floating/Settings/sections/connections/

| File | Description |
|------|-------------|
| `ConnectionStatus.tsx` | Component displaying connection status for various network components (node, P2Pool, Tor). Shows real-time connection health, peer counts, and network diagnostics. Used by: ConnectionsSettings for network status monitoring. |
| `ConnectionsSettings.tsx` | Main settings section for network connections configuration. Exports: ConnectionsSettings component combining connection status, node configuration, peer management. Used by: SettingsModal for comprehensive network settings management. |
| `Network.tsx` | Component for network selection and configuration (Mainnet/Testnet/LocalNet). Features network switching, network-specific settings, and validation. Used by: ConnectionsSettings for blockchain network management. |
| `Node.tsx` | Component for Tari node connection settings. Features: local vs remote node selection, node URL configuration, connection testing. Used by: ConnectionsSettings for blockchain node management. |
| `NodeTypeConfiguration.tsx` | Component for configuring node type and connection parameters. Features: node type selection, connection string configuration, advanced node settings. Used by: Node component for detailed node configuration. |
| `Peers.tsx` | Component for managing network peers and peer connections. Features: peer list display, peer status monitoring, peer management actions. Used by: ConnectionsSettings for P2P network peer management. |

###### src/containers/floating/Settings/sections/experimental/

| File | Description |
|------|-------------|
| `AppVersions.tsx` | Component displaying application version information and update status. Shows current version, available updates, and version history. Used by: ExperimentalSettings for version tracking and update management. |
| `DebugSettings.tsx` | Component for debug and development settings. Features: debug mode toggle, logging level configuration, development features. Used by: ExperimentalSettings for debugging and development tools. |
| `ExperimentalSettings.tsx` | Main experimental settings section combining debug features, app versions, and advanced/experimental functionality. Exports: ExperimentalSettings component. Used by: SettingsModal for experimental and debug features management. |
| `ExperimentalWarning.tsx` | Warning component for experimental features section. Displays caution message about experimental features, potential risks, and user acknowledgment. Used by: ExperimentalSettings to warn users about experimental functionality. |
| `P2poolMarkup.tsx` | Component for P2Pool mining configuration and status. Features: P2Pool connection settings, pool status monitoring, mining pool configuration. Used by: ExperimentalSettings for P2Pool mining management. |

###### src/containers/floating/Settings/sections/experimental/MonerodMarkup/

| File | Description |
|------|-------------|
| `MonerodMarkup.styles.ts` | Styled-components for MonerodMarkup component. Provides styling for Monero daemon configuration interface, including status indicators, connection settings, and debug information display. |
| `MonerodMarkup.tsx` | Component for Monero daemon (monerod) configuration and status display. Features: monerod connection settings, status monitoring, configuration options. Used by: ExperimentalSettings for Monero blockchain integration management. |
| `index.ts` | Export index file for MonerodMarkup component. Re-exports MonerodMarkup component for simplified imports. Standard module barrel export pattern. |

###### src/containers/floating/Settings/sections/experimental/TorMarkup/

| File | Description |
|------|-------------|
| `TorDebug.tsx` | Debug component for Tor network connectivity diagnostics. Features: Tor connection testing, circuit information, bridge configuration debugging. Used by: TorMarkup for Tor troubleshooting and diagnostics. |
| `TorMarkup.styles.ts` | Styled-components for TorMarkup component. Provides styling for Tor configuration interface, status indicators, connection settings, and debug information display. |
| `TorMarkup.tsx` | Component for Tor network configuration and status management. Features: Tor enabling/disabling, bridge configuration, connection status, debug tools. Used by: ExperimentalSettings for privacy networking management. |
| `index.ts` | Export index file for TorMarkup component. Re-exports TorMarkup component for simplified imports. Standard module barrel export pattern. |

###### src/containers/floating/Settings/sections/general/

| File | Description |
|------|-------------|
| `AirdropNotificationSettings.tsx` | Component for configuring airdrop notification preferences. Features: notification enable/disable, notification types, delivery preferences. Used by: GeneralSettings for airdrop notification management. |
| `AirdropPermissionSettings.tsx` | Component for managing airdrop participation permissions and eligibility settings. Features: permission toggles, eligibility status, participation consent. Used by: GeneralSettings for airdrop participation management. |
| `AppDataSettings.tsx` | Component for managing application data and storage settings. Features: data directory display, cleanup options, storage usage information. Used by: GeneralSettings for application data management. |
| `AutoUpdate.tsx` | Component for auto-update configuration settings. Features: auto-update enable/disable, update channel selection, update preferences. Used by: GeneralSettings for application update management. |
| `GeneralSettings.tsx` | Main general settings section combining core application preferences. Exports: GeneralSettings component including LocalNodeSync, StartApplicationOnBoot, AutoUpdate, language/theme settings, and advanced options. Used by: SettingsModal for primary application configuration. |
| `LanguageSettings.tsx` | Component for language and localization settings. Features: language selection dropdown, region preferences, language switching. Used by: GeneralSettings for internationalization configuration. |
| `LocalNodeSync.tsx` | Component for local node synchronization settings. Features: sync preferences, blockchain sync options, local node management. Used by: GeneralSettings for blockchain synchronization configuration. |
| `LogsSettings.tsx` | Component for logging configuration and log management. Features: log level settings, log file access, log cleanup options. Used by: GeneralSettings for application logging management. |
| `PreReleaseSettings.tsx` | Component for pre-release and beta features configuration. Features: beta channel opt-in, pre-release updates, experimental features access. Used by: GeneralSettings for beta/pre-release participation. |
| `ResetSettingsButton.tsx` | Component providing reset functionality for application settings. Features: confirmation dialog, selective reset options, backup creation. Used by: GeneralSettings for settings reset functionality. |
| `ResetSettingsDialog.tsx` | Confirmation dialog for settings reset functionality. Features: reset confirmation, options selection, data backup warnings. Used by: ResetSettingsButton for user confirmation before resetting settings. |
| `StartApplicationOnBootSettings.tsx` | Component for configuring application startup behavior. Features: auto-start on boot toggle, startup preferences, boot configuration. Used by: GeneralSettings for system startup management. |
| `ThemeSettings.tsx` | Component for theme and visual appearance settings. Features: light/dark theme selection, theme preferences, visual mode configuration. Used by: GeneralSettings for application theme management. |

###### src/containers/floating/Settings/sections/mining/

| File | Description |
|------|-------------|
| `CpuMiningMarkup.tsx` | Component for CPU mining configuration settings. Features: CPU thread count, eco mode settings, CPU mining optimization. Used by: MiningSettings for CPU mining configuration. |
| `GpuDevices.tsx` | Component for GPU device selection and configuration in mining settings. Features: GPU device list, device selection, device-specific settings. Used by: MiningSettings for GPU device management. |
| `GpuEngine.tsx` | Component for GPU mining engine selection and configuration. Features: mining algorithm selection, engine optimization, performance settings. Used by: MiningSettings for GPU engine management. |
| `GpuMiningMarkup.tsx` | Component for GPU mining configuration settings. Features: GPU mining enable/disable, power settings, GPU optimization options. Used by: MiningSettings for GPU mining configuration. |
| `MineOnStartMarkup.tsx` | Component for auto-start mining configuration. Features: mine on application start toggle, startup mining preferences, delayed start options. Used by: MiningSettings for automatic mining startup configuration. |
| `MiningSettings.tsx` | Main mining settings section combining CPU and GPU mining configuration. Exports: MiningSettings component including CpuMiningMarkup, GpuMiningMarkup, GpuEngine, GpuDevices, and MineOnStartMarkup. Used by: SettingsModal for comprehensive mining configuration management. |

###### src/containers/floating/Settings/sections/poolMining/

| File | Description |
|------|-------------|
| `P2PConnectionData.tsx` | Component displaying P2P connection data and statistics for pool mining. Features: connection status, peer information, network statistics. Used by: PoolMiningSettings for P2P network monitoring in pool mining context. |
| `P2PoolStats.styles.ts` | Styled-components for P2PoolStats component. Provides styling for pool statistics display, performance metrics, charts, and statistical data visualization. |
| `P2PoolStats.tsx` | Component displaying P2Pool mining statistics and performance metrics. Features: hashrate statistics, pool share information, earnings data, performance charts. Used by: PoolMiningSettings for pool mining monitoring. |
| `P2pMarkup.tsx` | Component for P2P pool mining configuration and markup display. Features: P2P settings configuration, pool connection management, peer-to-peer mining setup. Used by: PoolMiningSettings for P2P mining configuration. |
| `PeerTable.tsx` | Table component displaying peer information in pool mining context. Features: peer list, connection status, peer statistics, peer management. Used by: PoolMiningSettings for peer network visualization. |
| `PoolMiningSettings.tsx` | Main settings section for pool mining configuration. Exports: PoolMiningSettings component combining P2P connection data, pool stats, peer management. Used by: SettingsModal for comprehensive pool mining management. |
| `PoolMiningStats.tsx` | Component displaying comprehensive pool mining statistics and analytics. Features: mining performance metrics, pool contribution statistics, reward tracking. Used by: PoolMiningSettings for pool mining performance monitoring. |

###### src/containers/floating/Settings/sections/releaseNotes/

| File | Description |
|------|-------------|
| `ReleaseNotes.tsx` | Settings section for displaying application release notes and changelog. Features: version history display, release note accordion, markdown content rendering. Used by: SettingsModal for release information management. |
| `styles.ts` | Styled-components for ReleaseNotes section. Provides styling for release notes container, version headers, content layout, and markdown rendering within settings interface. |

###### src/containers/floating/Settings/sections/releaseNotes/AccordionItem/

| File | Description |
|------|-------------|
| `AccordionItem.tsx` | Accordion item component for displaying individual release notes. Features: collapsible content, version headers, release details expansion/collapse. Used by: ReleaseNotes for organized release information display. |
| `styles.ts` | Styled-components for AccordionItem component. Provides styling for accordion headers, content containers, expand/collapse animations, and visual states. |

###### src/containers/floating/Settings/sections/wallet/

| File | Description |
|------|-------------|
| `RefreshWalletHistory.tsx` | Component for refreshing wallet transaction history. Features: manual refresh trigger, sync status display, history update functionality. Used by: WalletSettings for wallet data synchronization. |
| `WalletSettings.tsx` | Main wallet settings section combining wallet address management, seed words, and wallet operations. Features: Tari/Monero address display, seed word management, wallet refresh, address editing. Used by: SettingsModal for comprehensive wallet configuration. |
| `styles.ts` | Styled-components for wallet settings sections. Provides styling for wallet grid layout, input areas, CTA buttons, form containers, and consistent spacing across wallet settings components. |

###### src/containers/floating/Settings/sections/wallet/MoneroAddressMarkup/

| File | Description |
|------|-------------|
| `MoneroAddressMarkup.tsx` | Component for displaying and managing Monero wallet address information. Features: Monero address display, QR code generation, copy functionality. Used by: WalletSettings for Monero address management. |
| `index.ts` | Export index file for MoneroAddressMarkup component. Re-exports MoneroAddressMarkup component for simplified imports. Standard module barrel export pattern. |

###### src/containers/floating/Settings/sections/wallet/MoneroSeedWords/

| File | Description |
|------|-------------|
| `MoneroSeedWordSettings.tsx` | Component for Monero seed word management in wallet settings. Features: Monero seed phrase display, backup options, recovery functionality. Used by: WalletSettings for Monero wallet seed management. |

###### src/containers/floating/Settings/sections/wallet/SeedWordsMarkup/

| File | Description |
|------|-------------|
| `useGetSeedWords.ts` | Custom hook for fetching and managing seed words from wallet. Features: secure seed word retrieval, caching, error handling, both Tari and Monero seed support. Used by: SeedWords components for seed phrase operations. |

###### src/containers/floating/Settings/sections/wallet/TariSeedWords/

| File | Description |
|------|-------------|
| `TariSeedWords.tsx` | Component for Tari seed word management in wallet settings. Features: Tari seed phrase display, backup options, recovery functionality. Used by: WalletSettings for Tari wallet seed management. |

###### src/containers/floating/Settings/sections/wallet/WalletAddressMarkup/

| File | Description |
|------|-------------|
| `WalletAddressMarkup.tsx` | Component for displaying and managing Tari wallet address information. Features: Tari address display, QR code generation, copy functionality, address validation. Used by: WalletSettings for Tari address management. |
| `index.ts` | Export index file for WalletAddressMarkup component. Re-exports WalletAddressMarkup component for simplified imports. Standard module barrel export pattern. |

###### src/containers/floating/Settings/sections/wallet/components/

| File | Description |
|------|-------------|
| `AddressEditor.tsx` | Component for editing wallet addresses with validation and formatting. Features: address input field, validation feedback, format checking, save/cancel actions. Used by: WalletSettings for wallet address modification. |

###### src/containers/floating/ShareRewardModal/

| File | Description |
|------|-------------|
| `ShareRewardModal.tsx` | Modal component for sharing mining rewards and referral functionality. Features: reward sharing interface, referral link generation, social sharing options. Used by: mining interface for reward sharing campaigns. |
| `styles.ts` | Styled-components for ShareRewardModal. Provides styling for reward sharing interface, social media buttons, referral links, and modal layout structure. |

###### src/containers/floating/StagedSecurity/

| File | Description |
|------|-------------|
| `StagedSecurity.tsx` | Multi-stage security setup wizard for wallet protection. Features: staged security flow, seed phrase backup, verification steps, security recommendations. Used by: wallet setup for comprehensive security configuration. |
| `styles.ts` | Main styled-components for StagedSecurity wizard. Provides overall layout, step navigation, progress indicators, and container styling for multi-stage security setup flow. |

###### src/containers/floating/StagedSecurity/components/HelpTip/

| File | Description |
|------|-------------|
| `HelpTip.tsx` | Help tip component for security setup guidance. Features: contextual help information, security tips, user guidance during security setup process. Used by: StagedSecurity sections for user assistance. |
| `styles.ts` | Styled-components for HelpTip component. Provides styling for help tooltips, tip containers, positioning, and visual design for security setup guidance. |

###### src/containers/floating/StagedSecurity/icons/

| File | Description |
|------|-------------|
| `CheckIcon.tsx` | SVG check icon component for security setup completion states. Used by: StagedSecurity components to indicate completed steps and successful validation. |
| `CopyIcon.tsx` | SVG copy icon component for copying security information. Used by: StagedSecurity components for copying seed phrases, addresses, and security data. |
| `PillCloseIcon.tsx` | SVG close/dismiss icon component for security setup interface. Used by: StagedSecurity components for dismissing pills, warnings, or completing actions. |

###### src/containers/floating/StagedSecurity/sections/ProtectIntro/

| File | Description |
|------|-------------|
| `ProtectIntro.tsx` | Introduction section for staged security setup explaining wallet protection importance. Features: security benefits explanation, setup overview, user motivation. Used by: StagedSecurity as initial step in security setup flow. |
| `styles.ts` | Styled-components for ProtectIntro section. Provides styling for introduction layout, explanatory text, security icons, and motivational content presentation. |

###### src/containers/floating/StagedSecurity/sections/SeedPhrase/

| File | Description |
|------|-------------|
| `SeedPhrase.tsx` | Seed phrase backup section in staged security setup. Features: seed phrase display, backup instructions, security warnings, copy functionality. Used by: StagedSecurity for seed phrase backup step. |
| `styles.ts` | Styled-components for SeedPhrase section. Provides styling for seed phrase grid, word display, security warnings, and backup interface layout. |

###### src/containers/floating/StagedSecurity/sections/VerifySeedPhrase/

| File | Description |
|------|-------------|
| `VerifySeedPhrase.tsx` | Seed phrase verification section for confirming backup completion. Features: word selection verification, validation feedback, completion confirmation. Used by: StagedSecurity for seed phrase verification step. |
| `styles.ts` | Styled-components for VerifySeedPhrase section. Provides styling for verification interface, word selection buttons, validation states, and completion feedback. |

###### src/containers/floating/SwapDialogs/components/WalletButton/

| File | Description |
|------|-------------|
| `WalletButton.styles.ts` | Styled-components for WalletButton component in swap dialogs. Provides styling for wallet selection buttons, connection states, icons, and interactive elements. |
| `WalletButton.tsx` | Wallet connection button component for swap dialogs. Features: wallet provider selection, connection status, wallet icons, connect/disconnect functionality. Used by: swap dialogs for wallet integration. |

###### src/containers/floating/SwapDialogs/helpers/

| File | Description |
|------|-------------|
| `getIcon.tsx` | Helper function for retrieving appropriate icons for different tokens and chains in swap interface. Returns icon components based on token/chain type. Used by: swap dialog components for dynamic icon display. |

###### src/containers/floating/SwapDialogs/icons/

| File | Description |
|------|-------------|
| `copyIcon.tsx` | SVG copy icon component for swap dialog interfaces. Used by: swap dialogs for copying transaction hashes, addresses, and other swap-related data. |
| `mm-fox.tsx` | SVG MetaMask fox logo icon component. Used by: swap dialogs to represent MetaMask wallet connection and Web3 wallet integration. |

###### src/containers/floating/SwapDialogs/icons/chains/

| File | Description |
|------|-------------|
| `ethereumIcon.tsx` | SVG icon component for Ethereum blockchain. Used by: swap dialogs to represent Ethereum network and ETH token in exchange interfaces. |
| `polygonIcon.tsx` | SVG icon component for Polygon blockchain. Used by: swap dialogs to represent Polygon network and MATIC token in exchange interfaces. |
| `tariIcon.tsx` | SVG icon component for Tari blockchain. Used by: swap dialogs to represent Tari network and XTM token in exchange interfaces. |
| `usdcIcon.tsx` | SVG icon component for USDC stablecoin. Used by: swap dialogs to represent USDC token in exchange interfaces and token selection. |
| `usdtIcon.tsx` | SVG icon component for USDT stablecoin. Used by: swap dialogs to represent USDT token in exchange interfaces and token selection. |

###### src/containers/floating/SwapDialogs/icons/elements/

| File | Description |
|------|-------------|
| `ArrowIcon.tsx` | SVG arrow icon component for swap direction indication. Used by: swap dialogs to show swap direction between tokens and navigation elements. |

###### src/containers/floating/SwapDialogs/sections/ProcessingTransaction/

| File | Description |
|------|-------------|
| `ProcessingTransaction.styles.ts` | Styled-components for ProcessingTransaction dialog section. Provides styling for transaction processing interface, status indicators, progress displays, and confirmation layouts. |
| `ProcessingTransaction.tsx` | Dialog section for displaying transaction processing status during swaps. Features: processing status display, transaction confirmation, fee information, error handling, completion states. Used by: Swap component for transaction lifecycle feedback. |

###### src/containers/floating/SwapDialogs/sections/SignMessage/

| File | Description |
|------|-------------|
| `SignApprovalMessage.styles.ts` | Styled-components for SignApprovalMessage dialog section. Provides styling for message signing interface, approval buttons, wallet connection states, and signature layouts. |
| `SignApprovalMessage.tsx` | Dialog section for signing approval messages during swap transactions. Features: message signing interface, wallet approval prompts, user confirmation. Used by: Swap component for transaction approval workflow. |

###### src/containers/floating/SwapDialogs/sections/SwapConfirmation/

| File | Description |
|------|-------------|
| `SwapConfirmation.styles.ts` | Styled-components for SwapConfirmation dialog section. Provides styling for swap confirmation interface, transaction details, fee displays, and confirmation buttons. |
| `SwapConfirmation.tsx` | Dialog section for confirming swap transactions before execution. Features: transaction details display, fee breakdown, confirmation/cancel actions, swap parameters review. Used by: Swap component for final transaction confirmation. |

###### src/containers/floating/UniversalEXSelectorModal/

| File | Description |
|------|-------------|
| `UniversalEXSelectorModal.tsx` | Modal component for selecting universal exchange providers. Features: exchange provider list, selection interface, provider comparison. Used by: exchange interfaces for provider selection and comparison. |
| `styles.ts` | Styled-components for UniversalEXSelectorModal. Provides styling for exchange selector interface, provider cards, selection states, and modal layout structure. |

###### src/containers/floating/Warmup/

| File | Description |
|------|-------------|
| `BlocksSVG.tsx` | SVG component rendering blocks graphic for warmup/initialization screens. Used by: WarmupDialog to provide visual representation during application or mining startup phases. |
| `WarmupDialog.tsx` | Dialog component for application warmup and initialization phases. Features: startup progress display, system initialization feedback, loading states. Used by: main app during startup and mining initialization processes. |
| `styles.ts` | Styled-components for WarmupDialog. Provides styling for warmup interface, progress indicators, loading animations, and initialization feedback layout. |

###### src/containers/floating/XSpaceBanner/

| File | Description |
|------|-------------|
| `XSpaceBanner.style.ts` | Styled-components for XSpaceBanner component. Provides styling for Twitter/X Space promotional banner, event information display, CTA buttons, and banner layout. |
| `XSpaceBanner.tsx` | Banner component for promoting Twitter/X Space events and community activities. Features: event information display, join links, scheduling details. Used by: main interface for community event promotion. |
| `formatDate.ts` | Utility function for formatting dates in XSpace banner display. Handles date formatting for event scheduling and time display. Used by: XSpaceBanner for consistent date presentation. |

##### src/containers/main/

| File | Description |
|------|-------------|
| `MainView.tsx` | Main application view container for Tari Universe. Conditionally renders either the setup flow (Sync component) or the main Dashboard based on appUnlocked status from useSetupStore. Includes SidebarNavigation and optional Background based on visual_mode setting. Acts as the primary router between onboarding and main application phases. |

###### src/containers/main/Airdrop/Settings/

| File | Description |
|------|-------------|
| `Logout.tsx` | Component for airdrop account logout functionality. Features: logout confirmation, session termination, account disconnection. Used by: airdrop settings for user account management. |
| `styles.ts` | Styled-components for airdrop settings interface. Provides styling for airdrop configuration, account management, logout buttons, and settings layout structure. |

###### src/containers/main/Airdrop/sidebar/

| File | Description |
|------|-------------|
| `AirdropSidebarItems.tsx` | Component rendering sidebar navigation items for airdrop section. Features: navigation menu, airdrop-specific items, user status display. Used by: airdrop interface for section navigation. |
| `Gems.tsx` | Component displaying gems/rewards information in airdrop sidebar. Features: gem count display, rewards tracking, achievement indicators. Used by: airdrop sidebar for rewards visualization. |
| `Invite.tsx` | Component for airdrop invitation and referral functionality. Features: invite link generation, referral tracking, social sharing options. Used by: airdrop sidebar for user referral management. |
| `LogIn.tsx` | Component for airdrop account login functionality. Features: authentication interface, login forms, account connection. Used by: airdrop sidebar for user authentication. |
| `User.tsx` | Component displaying user information and profile in airdrop sidebar. Features: user avatar, profile info, account status, user stats. Used by: airdrop sidebar for user profile display. |
| `items.style.ts` | Styled-components for airdrop sidebar items collection. Provides styling for sidebar item grouping, spacing, layout structure, and overall sidebar design. |

###### src/containers/main/Airdrop/sidebar/components/

| File | Description |
|------|-------------|
| `SidebarItem.tsx` | Reusable sidebar item component for airdrop navigation. Features: navigation item styling, active states, icons, click handling. Used by: airdrop sidebar for consistent navigation items. |
| `item.style.ts` | Styled-components for individual sidebar items in airdrop interface. Provides styling for sidebar item layout, hover states, active indicators, and visual design. |

###### src/containers/main/Airdrop/sidebar/components/CommunityMessages/

| File | Description |
|------|-------------|
| `CommunityMessages.styles.ts` | Styled-components for CommunityMessages component in airdrop sidebar. Provides styling for community message display, message cards, timestamps, and messaging interface layout. |
| `CommunityMessages.tsx` | Component for displaying community messages and announcements in airdrop sidebar. Features: message feed, community updates, notification display. Used by: airdrop sidebar for community communication. |

###### src/containers/main/Dashboard/

| File | Description |
|------|-------------|
| `Dashboard.tsx` | Main dashboard container handling connection status and view routing in Tari Universe. Displays DisconnectWrapper when offline, conditionally shows Tapplet component for bridge swaps or defaults to MiningView. Uses mining states synchronization hook and integrates with UI, airdrop, and tapplets stores for state management. Central hub for authenticated user interface. |
| `index.ts` | Export index file for Dashboard container. Re-exports Dashboard components and related functionality for simplified imports. Standard module barrel export pattern. |
| `styles.ts` | Main styled-components for Dashboard container. Provides overall dashboard layout, grid structure, responsive design, and main dashboard visual styling. |

###### src/containers/main/Dashboard/MiningView/

| File | Description |
|------|-------------|
| `MiningView.styles.ts` | Styled-components for MiningView dashboard interface. Provides styling for mining dashboard layout, stats display, control panels, and mining visualization elements. |
| `MiningView.tsx` | Main mining interface component for Tari Universe dashboard. Displays core mining information including earnings, block height accent, ruler visualization, and block time. Conditionally renders between standard mining view (BlockHeightAccent, Ruler, BlockTime) and block explorer mini view based on blockBubblesEnabled setting. Integrates UI store for configuration state and provides primary mining dashboard interface. Central component for mining status visualization and user interaction with mining operations. |

###### src/containers/main/Dashboard/MiningView/components/

| File | Description |
|------|-------------|
| `BlockHeightAccent.styles.ts` | Styled-components for BlockHeightAccent component. Provides styling for block height display accent, highlighting current block information in mining interface. |
| `BlockHeightAccent.tsx` | Component for highlighting current block height in mining dashboard. Features: block height display, visual emphasis, real-time updates. Used by: MiningView for blockchain progress indication. |
| `BlockTime.styles.ts` | Styled-components for BlockTime component. Provides styling for block time display, timing information, and temporal indicators in mining interface. |
| `BlockTime.tsx` | Component for displaying block timing information in mining dashboard. Features: block time tracking, timing displays, temporal statistics. Used by: MiningView for blockchain timing metrics. |
| `Earnings.styles.ts` | Styled-components for Earnings component. Provides styling for earnings display, profit calculations, currency formatting, and financial metrics visualization in mining dashboard. |
| `Earnings.tsx` | Component for displaying mining earnings and profit calculations. Features: earnings tracking, profit metrics, currency conversion, performance indicators. Used by: MiningView for financial mining statistics. |
| `Ruler.styles.ts` | Styled-components for Ruler component. Provides styling for measurement ruler, scale indicators, and visual guidelines in mining dashboard interface. |
| `Ruler.tsx` | Component for displaying measurement ruler and scale guides in mining dashboard. Features: visual scale indicators, measurement guides, alignment helpers. Used by: MiningView for interface measurements and visual alignment. |

###### src/containers/main/Dashboard/components/

| File | Description |
|------|-------------|
| `VisualMode.tsx` | Component for toggling visual mode settings in dashboard. Features: visual mode selection, 3D/2D toggle, visualization preferences. Used by: Dashboard and settings for visual display configuration. |

###### src/containers/main/Dashboard/components/BlockExplorerMini/

| File | Description |
|------|-------------|
| `BlockExplorerMini.tsx` | Mini block explorer component for dashboard display. Features: recent block list, block status overview, mining activity visualization, navigation to full explorer. Used by: Dashboard for blockchain activity monitoring. |
| `styles.ts` | Main styled-components for BlockExplorerMini component. Provides overall layout, container styling, block list structure, and mini explorer visual design. |
| `useBlocks.ts` | Custom hook for managing block data in mini block explorer. Features: block fetching, state management, real-time updates, block filtering. Used by: BlockExplorerMini for block data operations. |

###### src/containers/main/Dashboard/components/BlockExplorerMini/BlockEntry/

| File | Description |
|------|-------------|
| `BlockEntry.tsx` | Component for individual block entries in mini block explorer. Features: block information display, block status, mining details, navigation to full block view. Used by: BlockExplorerMini for block list representation. |

###### src/containers/main/Dashboard/components/BlockExplorerMini/BlockEntry/BlockSolved/

| File | Description |
|------|-------------|
| `BlockSolved.tsx` | Component for displaying solved block information and status. Features: solved block details, completion time, miner information, block statistics. Used by: BlockEntry for completed block representation. |
| `PeopleIcon.tsx` | SVG people/users icon component for displaying miner count information. Used by: BlockSolved to represent number of miners or participants in block solving. |
| `styles.ts` | Styled-components for BlockSolved component. Provides styling for solved block display, completion status, block information layout, and solved block visual indicators. |

###### src/containers/main/Dashboard/components/BlockExplorerMini/BlockEntry/BlockSolved/BlockProgress/

| File | Description |
|------|-------------|
| `BlockProgress.tsx` | Component for displaying block solving progress indicator. Features: progress visualization, completion status, solving statistics. Used by: BlockSolved for block completion tracking. |
| `CubeIcon.tsx` | SVG cube icon component for block progress visualization. Used by: BlockProgress to represent blockchain blocks and mining progress indicators. |
| `styles.ts` | Styled-components for BlockProgress component. Provides styling for progress bars, completion indicators, progress animations, and visual progress feedback. |

###### src/containers/main/Dashboard/components/BlockExplorerMini/BlockEntry/BlockSolving/

| File | Description |
|------|-------------|
| `BlockSolving.tsx` | Component for displaying currently solving block information and progress. Features: solving status, progress indicators, time elapsed, current mining activity. Used by: BlockEntry for blocks in progress. |
| `styles.ts` | Styled-components for BlockSolving component. Provides styling for solving block display, progress indicators, active mining status, and solving block visual design. |

###### src/containers/main/Dashboard/components/BlockExplorerMini/BlockEntry/BlockSolving/BlockTimer/

| File | Description |
|------|-------------|
| `BlockTimer.tsx` | Timer component for tracking block solving duration. Features: elapsed time display, real-time updates, timing calculations. Used by: BlockSolving for duration tracking. |
| `styles.ts` | Styled-components for BlockTimer component. Provides styling for timer display, time formatting, countdown animations, and temporal indicators. |

###### src/containers/main/Dashboard/components/BlockExplorerMini/BlockEntry/BlockSolving/BlockVideo/

| File | Description |
|------|-------------|
| `BlockVideo.tsx` | Video component for animated block solving visualization. Features: mining animation, visual feedback, block solving representation. Used by: BlockSolving for visual mining activity indication. |

###### src/containers/main/Dashboard/components/BlockExplorerMini/BlockScrollList/

| File | Description |
|------|-------------|
| `BlockScrollList.tsx` | Scrollable list component for displaying blocks in mini block explorer. Features: virtualized scrolling, block list management, efficient rendering. Used by: BlockExplorerMini for block list display. |
| `styles.ts` | Styled-components for BlockScrollList component. Provides styling for scrollable block list, list container, scroll behavior, and block list layout structure. |

###### src/containers/main/Dashboard/components/BlockExplorerMini/MinerCount/

| File | Description |
|------|-------------|
| `MinerCount.tsx` | Component for displaying current active miner count. Features: miner statistics, active miners display, network participation metrics. Used by: BlockExplorerMini for mining network status. |
| `styles.ts` | Styled-components for MinerCount component. Provides styling for miner count display, statistics visualization, number formatting, and network participation indicators. |

###### src/containers/main/Dashboard/components/BlockExplorerMini/utils/

| File | Description |
|------|-------------|
| `formatting.ts` | Utility functions for formatting block data in mini block explorer. Contains formatters for block numbers, timestamps, hash values, and other blockchain data. Used by: BlockExplorerMini components for consistent data presentation. |

###### src/containers/main/Reconnect/

| File | Description |
|------|-------------|
| `DisconnectWrapper.tsx` | Wrapper component for managing disconnection states and reconnection flow. Features: connection monitoring, reconnection attempts, network status handling. Used by: main app for network connectivity management. |
| `Disconnected.tsx` | Component for displaying standard disconnection state. Features: disconnection message, reconnection options, network status display. Used by: DisconnectWrapper for recoverable disconnection scenarios. |
| `DisconnectedSevere.tsx` | Component for displaying severe disconnection state requiring user intervention. Features: critical disconnect message, manual reconnection options, troubleshooting guidance. Used by: DisconnectWrapper for severe connectivity issues. |
| `styles.ts` | Styled-components for Reconnect container components. Provides styling for disconnection screens, reconnection interfaces, network status displays, and connection error layouts. |

###### src/containers/main/ShellOfSecrets/

| File | Description |
|------|-------------|
| `ShellOfSecrets.tsx` | Main container for Shell of Secrets feature - special promotional/gamification interface. Features: interactive widget display, special events, prize mechanics. Used by: main app for special Shell of Secrets campaigns. |

###### src/containers/main/ShellOfSecrets/SoSWidget/

| File | Description |
|------|-------------|
| `SoSWidget.tsx` | Shell of Secrets widget component containing game interface. Features: timer display, friends section, prize information, interactive game elements. Used by: ShellOfSecrets for game UI presentation. |
| `styles.ts` | Styled-components for Shell of Secrets widget creating retro TV/CRT aesthetic. Exports: Wrapper (fixed motion div positioned top-right with transition variants), BlackBox (dark container with inner/outer shadow layers creating depth effect), JewelImage/GateImage (absolutely positioned decorative images with precise pixel placement), ContentLayer (z-index 3 flex container for content layering), TopGroup (flex layout with 20px gap for header elements). Features dark theme (#181e2c background), rounded corners (24px), complex box-shadow system with multiple layers for CRT screen effect, and strategic z-indexing for proper element stacking. Implements motion variants for smooth show/hide transitions. |

###### src/containers/main/ShellOfSecrets/SoSWidget/segments/Friends/

| File | Description |
|------|-------------|
| `Friends.tsx` | Friends section of Shell of Secrets widget showing friend participation and referrals. Features: friend list, referral tracking, social elements. Used by: SoSWidget for social/friend functionality. |
| `styles.ts` | Styled-components for Friends segment. Provides styling for friend list, social interactions, referral displays, and friend-related UI elements in Shell of Secrets. |

###### src/containers/main/ShellOfSecrets/SoSWidget/segments/Friends/AnimatedArrows/

| File | Description |
|------|-------------|
| `AnimatedArrows.tsx` | Animated arrow component for Shell of Secrets friends section. Features: directional animations, friend connection indicators. Used by: Friends segment for visual direction cues. |

###### src/containers/main/ShellOfSecrets/SoSWidget/segments/Prize/

| File | Description |
|------|-------------|
| `Prize.tsx` | Prize section of Shell of Secrets widget displaying available rewards and prizes. Features: prize information, reward tracking, prize eligibility. Used by: SoSWidget for prize/reward display. |
| `styles.ts` | Styled-components for Prize segment. Provides styling for prize display, reward visualization, prize information layout, and prize-related UI elements. |

###### src/containers/main/ShellOfSecrets/SoSWidget/segments/Timer/

| File | Description |
|------|-------------|
| `Timer.tsx` | Timer section of Shell of Secrets widget showing countdown and timing information. Features: countdown display, time tracking, event timing. Used by: SoSWidget for time-based game mechanics. |
| `styles.ts` | Styled-components for Timer segment. Provides styling for countdown display, timer visualization, time formatting, and timer-related UI elements in Shell of Secrets. |

###### src/containers/main/ShellOfSecrets/components/Scanlines/

| File | Description |
|------|-------------|
| `Scanlines.tsx` | React component implementing retro CRT TV effect with animated scanlines and static noise. Exports: Scanlines (default). Dependencies: React hooks, styled components. Creates canvas-based animation using requestAnimationFrame to render TV static (random noise) and moving horizontal scanlines every 3 pixels. Features: 8% opacity noise overlay, white scanlines with 8% opacity, continuous animation loop with 0.1px offset increment, mix-blend-mode screen for visual effect. Used by: Shell of Secrets retro UI theming. |
| `styles.ts` | Styled components for CRT scanlines visual effect. Exports: Canvas (absolute positioned canvas with pointer-events disabled), Vignette (radial gradient overlay). Dependencies: styled-components. Canvas covers full container (inset: 0) with z-index 2, no pointer events for performance. Vignette creates darkened edge effect using radial gradient from transparent center to 50% black edges with multiply blend mode. Used by: Scanlines component for retro TV aesthetic. |

###### src/containers/main/Sync/

| File | Description |
|------|-------------|
| `Sync.tsx` | Blockchain synchronization interface for Tari Universe initial setup and sync phases. Features theme-aware video streams from Cloudflare with coin animation posters (light/dark variants). Includes setup components: Progress tracking, AirdropInvite, AirdropLogin, and ModeSelection. Uses internationalization for setup-view translations and styled-components theming. Provides visual feedback during blockchain sync, wallet setup, and airdrop game registration. Critical component for onboarding new users, displaying sync progress, and guiding through initial application configuration steps. |
| `sync.styles.ts` | Styled components for sync screen layout and typography. Exports: Wrapper (full-width motion container), Content (responsive grid layout), HeaderContent (centered flex column), Heading/SubHeading (responsive typography), HeaderGraphic (video container), ActionContent (action card grid), FooterContent (bottom section). Dependencies: motion/react-m, styled-components, Typography component. Features responsive design with vw/vh units, grid layouts, viewport-based scaling, height-based media queries (@max-height: 955px), consistent spacing and typography hierarchy. |

###### src/containers/main/Sync/actions/

| File | Description |
|------|-------------|
| `AirdropInvite.tsx` | React component for airdrop friend invitation action during sync phase. Exports: AirdropInvite (default). Dependencies: useAirdropStore, SyncActionCard, useCopyToClipboard, react-i18next, styled action components. Generates shareable invitation URL using airdrop URL and user's referral code. Features copy-to-clipboard functionality with visual feedback (LinkIcon → CheckSvg), internationalized text, conditional rendering based on referral code existence. Used by: Sync screen action cards for user referral program and gem earning. |
| `AirdropLogin.tsx` | React component for airdrop authentication action during sync phase. Exports: AirdropLogin (default). Dependencies: useAirdropStore, SyncActionCard, XIconSVG, useAirdropAuth, react-i18next, styled action components. Provides login button to authenticate with airdrop system for earning gems. Features X/Twitter icon, auth handler integration, conditional rendering (only shows when no airdrop token exists), internationalized text for CTA and copy. Used by: Sync screen to prompt users to connect social accounts for rewards. |
| `ModeSelection.tsx` | React component for mining mode selection action during sync phase. Exports: ModeSelection (default). Dependencies: SyncActionCard, ModeSelect, styled components, react-i18next. Renders mode selection dropdown within SyncActionCard wrapper with minimal variant styling. Uses SelectWrapper for consistent padding and layout. Features internationalized title/subtitle via setup-view translations. Used by: Sync screen action cards to allow users to choose mining mode (eco/performance) during initial setup. |
| `actions.style.ts` | Styled-components for sync phase action UI elements. Exports: ActionContentWrapper (flex container with centered gap for button content), ActionButton (full-width button with tari purple background and hover states), ButtonIconWrapper (30px circular icon container with theme-based background gradient), SelectWrapper (padded flex container for dropdown components). Features consistent theming via palette variables, hover effects with opacity transitions, flexible layouts for various action types, and proper spacing/padding for touch targets. Used by sync phase action cards for consistent styling across AirdropInvite, AirdropLogin, and ModeSelection components. |

###### src/containers/main/Sync/components/

| File | Description |
|------|-------------|
| `Progress.tsx` | React component displaying setup progress with linear progress bar, percentage, and countdown timer. Exports: Progress (default), Timer (styled typography). Dependencies: LinearProgress, Typography, styled-components, react-i18next, custom hooks (useCurrentPhaseDetails, useProgressCountdown), useNodeStore. Features dynamic progress display with phase titles, percentage values, countdown text, conditional rendering based on node type (Local vs Remote). Combines setup phase info with countdown estimates for user feedback during initial setup and sync process. |
| `SyncActionCard.tsx` | Reusable card component for sync phase actions. Exports: SyncActionCard function. Dependencies: styled components from syncAction.style.ts, React. Interface: SyncActionCardProps (title: string, action?: ReactNode, subtitle?: string). Features consistent card layout with title, subtitle, and action area for buttons/controls. Used by: AirdropInvite, AirdropLogin, ModeSelection components to provide unified card-based UI for setup actions during sync phase. |
| `syncAction.style.ts` | Styled components for sync action cards. Exports: Card (main card container), CardTitle (h1 typography), CardSubtitle (smaller text), CardActionWrapper (bottom action area), ActionWrapper (styled action container). Dependencies: styled-components, convertHexToRGBA utility, Typography component. Features: 30px padding, 20px border radius, theme-based background/colors, box shadow with RGBA conversion, flex layouts, responsive action area sizing (min 240px, 70% width). Used by: SyncActionCard component for consistent card-based layout styling. |
| `useCurrentPhaseDetails.ts` | Custom React hook providing current setup phase details during sync process. Exports: useCurrentPhaseDetails. Dependencies: useSetupStore, React useMemo. Returns object with setupPhaseTitle, setupTitle, setupProgress, setupParams from store payloads. Implements phase priority logic: mining > hardware > node > core phases. Memoized calculation determines which phase to show based on completion status. Used by: Progress component to display current setup phase information and progress percentage. |
| `useProgressCountdown.ts` | Custom React hook managing setup progress countdown timer with local/remote node estimates. Exports: useProgressCountdown. Dependencies: React hooks, useNodeStore, useSetupStore, useExchangeStore, react-i18next. Features: Local node sync estimation (MIN_PER_BLOCK_SYNC rate), remote node 120s default, header/block sync calculations, real-time countdown updates, formatted time display (HH:MM:SS), internationalized status messages. Returns countdown, countdownText, formattedTime. Handles Universal modal awaiting state and various completion scenarios. |

##### src/containers/navigation/

| File | Description |
|------|-------------|
| `SidebarNavigation.styles.ts` | Styled components for sidebar navigation wrapper. Exports: SidebarNavigationWrapper. Dependencies: motion/react-m, styled-components, SB_SPACING theme constant. Simple motion div with full height, flex display, gap spacing from theme constants, pointer events enabled, z-index 10 for proper layering. Used by: SidebarNavigation container to wrap sidebar components with consistent spacing and layout. |
| `SidebarNavigation.tsx` | Navigation sidebar container managing mini and full sidebar states for Tari Universe. Features memoized component with automatic sidebar opening after setup completion. Renders SidebarMini (always visible) and conditionally displays full Sidebar with Motion AnimatePresence for smooth transitions. Hides full sidebar when tapplets are active to maximize tapplet display area. Integrates with setup store to control sidebar behavior during application initialization. Provides primary navigation interface for switching between mining and wallet views, accessing settings, and application features. Critical UI component for application navigation and user experience. |

###### src/containers/navigation/Sidebars/

| File | Description |
|------|-------------|
| `BridgeNavigationButton.tsx` | React component for bridge tapplet navigation button with hover effects and animation integration. Exports: BridgeNavigationButton (default). Dependencies: React, react-icons, tari-tower, UI/Tapplets stores, styled components, motion/react. Features: NavButton memo component with hover arrow animation, bridge icon (BridgeOutlineSVG), tapplet activation (BRIDGE_TAPPLET_ID), sidebar state management, camera offset animation via setAnimationProperties, wallet scanning state checks. Integrates with 3D tower visualization by adjusting camera offset when sidebar opens/closes. |
| `NavigationButton.tsx` | React component for main navigation button with 3D tower integration and connection status. Exports: NavigationButton (default). Dependencies: React, react-icons (IoChevronDown), tari-tower, UI/mining stores, styled components, motion/react. Features: NavButton memo component with rotating chevron animation on hover (0→180 degrees), cube icon (CubeOutlineSVG), connection pulse indicator for isNodeConnected status, sidebar/tapplet state management via toggleSidebarOpen(), tower camera offset animation via setAnimationProperties based on sidebar state. Handles toggle between sidebar and tapplet states, adjusting 3D visualization camera when UI changes. Primary navigation control for opening/closing main sidebar with visual feedback. |
| `Sidebar.styles.ts` | Styled components for main sidebar layout with motion animations. Exports: SidebarWrapper (motion div with variants), WrapperGrid (flex column container), GridAreaTop/Bottom (section containers). Dependencies: styled-components, motion/react-m, SB_WIDTH theme constant. Features: open/closed animation variants (opacity/left transitions), box shadow, rounded corners, pointer events management. SidebarWrapper uses 320px width, 15px/10px padding, background theme colors. Grid areas provide top/bottom layout separation. |
| `Sidebar.tsx` | Main sidebar container component organizing mining and wallet sections. Exports: Sidebar (default memo). Dependencies: WalletSection, MiningSection, styled components. Simple layout wrapper with MiningSection in top grid area and WalletSection in bottom grid area. Uses SidebarWrapper for styling and animation, WrapperGrid for layout structure. Core sidebar displaying mining controls at top and wallet information at bottom. |
| `SidebarMini.styles.ts` | Styled components for mini sidebar layout with grid areas and button styling. Exports: MiniWrapper (grid container), GridTop/Center/Bottom (grid areas), NavIconWrapper/HoverIconWrapper (motion divs), LogoWrapper/ConnectionWrapper/NavigationWrapper (utility containers), StyledIconButton (themed icon button). Dependencies: motion/react-m, styled-components, IconButton, SB_MINI_WIDTH, convertHexToRGBA. Features: 3-area grid layout, 20px padding, 60px width, box shadow, responsive gap adjustment (@max-height: 680px), hover/nav icon positioning. |
| `SidebarMini.tsx` | Compact sidebar component with logo, navigation, and action buttons. Exports: SidebarMini (default memo). Dependencies: TariOutlineSVG, OpenSettingsButton, AirdropSidebarItems, NavigationButton, BridgeNavigationButton, useTappletsStore, styled grid components. Layout: Tari logo at top, navigation buttons in center (main nav + conditional bridge), airdrop items and settings at bottom. Bridge navigation conditionally rendered based on uiBridgeSwapsEnabled flag. Provides minimal interface when main sidebar is closed. |

###### src/containers/navigation/Sidebars/sections/

| File | Description |
|------|-------------|
| `Mining.tsx` | Mining section container for sidebar organizing mining-related components. Exports: MiningSection (default function). Dependencies: MiningButton, LostConnectionAlert, Miner components. Simple wrapper component rendering mining controls in order: main mining button, connection alert if disconnected, and detailed miner statistics/controls. Used by: Sidebar component as top grid area content for mining functionality access. |
| `Wallet.tsx` | Wallet section container with conditional UI based on seedless mode. Exports: WalletSection (default memo). Dependencies: WalletSidebarContent, WalletDisplay, useUIStore. Features conditional rendering: displays WalletDisplay for seedless UI mode, otherwise shows WalletSidebarContent for standard wallet interface. Used by: Sidebar component as bottom grid area content. Adapts wallet UI based on user's setup preferences (seedless vs traditional). |

###### src/containers/navigation/components/

| File | Description |
|------|-------------|
| `LostConnectionAlert.tsx` | Alert component displaying network connection loss warning. Exports: LostConnectionAlert (default). Dependencies: react-icons IoAlertCircleSharp, Stack, Typography, react-i18next, styled-components, useSetupStore. Features warning icon with theme colors, horizontal stack layout, conditional rendering (shows when not connected to Tari and not setting up). Currently hardcoded to connected state with hotfix comment. Styled warning icon uses theme palette warning colors. Used by: MiningSection to alert users of connectivity issues. |

###### src/containers/navigation/components/Miner/

| File | Description |
|------|-------------|
| `Miner.tsx` | Main miner dashboard displaying CPU/GPU hashrate stats and mining controls. Exports: Miner (default function). Dependencies: react-i18next, mining stores (useMiningStore, useMiningMetricsStore, useConfigMiningStore), formatHashrate utility, ModeSelect, Tile, PoolStatsTile components. Features: Real-time hashrate display for CPU/GPU, loading states for mining initiation, conditional stats based on mining status, mining mode selection, pool statistics. Complex state logic determining loading states and hashrate visibility. Core mining interface in sidebar. |
| `styles.ts` | Styled components for miner layout and tiles. Exports: MinerContainer (flex column wrapper), TileContainer (responsive grid), TileItem (individual tile styling), TileTop (header section), StatWrapper (statistic display), LoaderWrapper (loading container), Unit (unit text). Dependencies: styled-components. Features: 6px gaps, responsive tile sizing (33% width), 60px tile height, theme-based colors, box shadows, conditional text transform, paper background with rounded borders. Provides consistent layout for mining statistics dashboard. |

###### src/containers/navigation/components/Miner/components/

| File | Description |
|------|-------------|
| `ButtonOrbitAnimation.styles.ts` | Styled components for orbital animation around mining button. Exports: OrbitWrapper (absolute positioned container), Orbit (motion circular border), CubeWrapper (motion cube positioning). Dependencies: styled-components, motion/react, convertHexToRGBA utility. Features: 300px circular orbit container, centered positioning, theme-based border colors with alpha transparency, cube icon wrapper (22px), pointer-events disabled, z-index layering. Used by: ButtonOrbitAnimation component for visual effects around mining controls. |
| `ButtonOrbitAnimation.tsx` | React component creating animated orbital effects around mining button. Exports: ButtonOrbitAnimation (default). Dependencies: motion animations (useTime, useTransform), CubeSvg, styled components. Features: 4 orbital tracks (200px-260px radius), 6 cubes per track, alternating rotation directions, opacity/scale animations, even/odd track styling (solid/dashed borders), time-based transforms (55s rotation cycle), pulse/fade effects. Complex mathematical positioning using track size, PI calculations, and modulo operations for cube placement. |
| `ModeSelect.tsx` | React component for mining mode selection with Eco/Ludicrous/Custom options. Exports: ModeSelect (default memo). Dependencies: react-i18next, mining stores, Select component, styled components, emoji icons (eco.png, fire.png, custom.png). Features: primary/minimal variants, loading states, disabled during mining, custom power levels dialog trigger, ludicrous confirmation dialog, mode change handling. Interface: ModeSelectProps with variant prop. Conditional rendering based on hardware phase completion and feature flags. |
| `Tile.tsx` | Reusable tile component for displaying mining statistics with optional chip indicators. Exports: Tile (default function). Dependencies: styled components, truncateString utility, Typography, Chip, color palette, formatters, LoadingDots. Interface: TileProps (title, stats, unit, chipValue, isLoading, useLowerCase). Features: Color-coded chip based on value ranges (0-100% mapped to color ramp), loading state with dots animation, stat truncation to 8 characters, case transformation options. Used by: Miner component for CPU/GPU hashrate display. |

###### src/containers/navigation/components/Miner/components/CustomPowerLevels/

| File | Description |
|------|-------------|
| `CustomPowerLevelsDialog.styles.ts` | Styled-components definitions for the Custom Power Levels dialog interface. Exports CustomLevelsContent (main container with 700px width, flex column layout), CustomLevelsHeader (title bar with bottom border), SuccessContainer (green success message with fade animation), TopRightContainer (right-aligned button container). Uses conditional styling based on $visible props for animation states. Part of mining power level configuration UI. |
| `CustomPowerLevelsDialog.tsx` | React component for configuring custom CPU and GPU mining power levels. Allows users to set specific thread counts for mining hardware via range sliders. Exports CustomPowerLevelsDialog component. Dependencies: react-hook-form for form management, useMiningStore for mining state, useConfigMiningStore for configuration. Handles form submission to change mining mode to 'Custom' with user-defined levels. Includes success feedback and loading states during mode changes. Used within CustomPowerLevelsDialogContainer. |
| `CustomPowerLevelsDialogContainer.tsx` | Container component that wraps CustomPowerLevelsDialog in a modal Dialog. Manages dialog open/close state via useMiningStore and handles hardware detection requirements. Fetches max available threads when dialog opens if not already loaded. Shows loading spinner while hardware detection is in progress. Prevents dialog closure during mode changes. Used to provide modal interface for custom mining power level configuration. |
| `RangeInput.styles.ts` | Styled-components for custom range input slider used in mining power level configuration. Defines styled components including RangeInputHolder (slider track), RangeInput (webkit slider with custom thumb positioning), InputVal (progress fill), PerformanceMarker (eco/fire indicators), RangeValueHolder (popup value display), WarningContainer (conditional warning display). Uses 570px slider width, 30px thumb size constants. Includes webkit-specific styling for cross-browser slider appearance. Theme-aware styling for dark/light modes. |
| `RangeInput.tsx` | React component for custom range input slider with visual indicators and warning system. Exports RangeInputComponent with props for label, max level, current value, step size, loading state, and percentage display option. Features performance markers at 15% (eco) and 75% (fire) positions, hover value display, warning threshold at 75% of max value. Uses i18next for internationalization. Includes step value validation and change debouncing. Part of mining power level configuration interface. |

###### src/containers/navigation/components/Miner/components/PoolStatsTile/

| File | Description |
|------|-------------|
| `PoolStatsTile.tsx` | Pool statistics display tile component for mining dashboard. Shows unpaid mining earnings with animated progress indicators and success celebrations. Exports PoolStatsTile component. Dependencies: NumberFlow for animated number transitions, Floating UI for tooltips, Lottie animations for visual feedback. Features automatic 2 XTM reward threshold detection, mining timer display, expandable tooltip with detailed pool information. Integrates with Tari Tower 3D visualization for success animations. Only renders when CPU mining is enabled. |
| `styles.ts` | Comprehensive styled-components for PoolStatsTile component. Defines Wrapper (container), Border/Inside (tile structure with theme-aware gradients), AnimatedGradient (rotating conic gradient for mining state), LeftContent/RightContent (tile layout), Values, Title, BalanceVal (typography components), TriggerWrapper (question mark icon), ExpandedBox (floating tooltip), TooltipChip components (chip-style info displays). Includes loading, mining, and idle states with appropriate color schemes. Features spinning animation keyframe for active mining border effects. |

###### src/containers/navigation/components/Miner/components/PoolStatsTile/ProgressAnimation/

| File | Description |
|------|-------------|
| `ProgressAnimation.tsx` | Progress animation component using Lottie for pool statistics updates. Displays animated coin progress when mining earnings increase. Auto-hides after 4 seconds and calls optional onComplete callback. Uses DotLottieReact with AnimatePresence for smooth transitions. Loads coins_progress_url Lottie animation. Used within PoolStatsTile to provide visual feedback for earning progress. |
| `styles.ts` | Styled-components for ProgressAnimation Lottie overlay. Defines Wrapper component as absolute positioned overlay with theme-aware background gradients (light green for light mode, dark green for dark mode). Scales Lottie animation to 1.1x for visual effect. Uses motion/react-m for animation support. Positioned to cover entire parent tile with proper z-index layering. |

###### src/containers/navigation/components/Miner/components/PoolStatsTile/SuccessAnimation/

| File | Description |
|------|-------------|
| `SuccessAnimation.tsx` | Success celebration animation component for reaching mining reward thresholds. Displays victory Lottie animation with customizable reward text when mining milestones are achieved. Auto-hides after 4 seconds with optional completion callback. Uses DotLottieReact with coins_victory_url animation. Includes scale and opacity transitions via motion/react. Props include reward threshold amount and copy text. Triggered when users reach 2 XTM mining rewards in PoolStatsTile. |
| `styles.ts` | Styled-components for SuccessAnimation celebration overlay. Defines Wrapper with green gradient background (#5fea9d to #b3d891), Text component with Poppins font styling for reward message display, and LottieWrapper for positioning victory animation. Uses absolute positioning with high z-index for overlay effect. Scales Lottie animation to 1.1x with border radius matching parent tile. Motion-enabled components for animation support. |

###### src/containers/navigation/components/Miner/components/PoolStatsTile/lotties/

| File | Description |
|------|-------------|
| `Coins_Progress_Lottie.json` | [GENERATED] Lottie animation JSON for pool mining progress visualization. Large single-line JSON file containing Lottie animation data for coin/progress animation. Used by: ProgressAnimation component to display animated progress feedback during pool mining operations. Contains vector animation data with paths, colors, keyframes for smooth progress indicators. |
| `Coins_Victory_Lottie.json` | [GENERATED] Lottie animation JSON for pool mining success/victory visualization. Large single-line JSON file containing Lottie animation data for coin/victory celebration animation. Used by: SuccessAnimation component to display success feedback when mining achieves milestones or completes blocks. Contains vector animation data for celebratory coin animations. |

###### src/containers/navigation/components/MiningButton/

| File | Description |
|------|-------------|
| `MiningButton.styles.ts` | Styled components for mining button with dynamic states and gradients. Exports: IconWrapper (circular icon container), ButtonWrapper (button container), StyledButton (main button with states). Dependencies: styled-components, Button component, convertHexToRGBA utility. Features: State-based gradients (started/stopped), hover effects, disabled styling, loading state with transparent text, transition animations (0.7s cubic-bezier), conditional background images, box shadows. Loading state removes pointer events and changes styling completely. |
| `MiningButton.tsx` | Primary mining control button with state management and visual feedback. Exports: MiningButton (default function). Dependencies: react-icons (pause/chevron), react-i18next, mining stores, startMining/stopMining actions, loaders, ButtonOrbitAnimation. Features: Start/stop mining toggle, disabled states during setup/loading, conditional icons (play/pause), orbital animation when mining, spinner loading, state text enum. Complex state logic considering CPU/GPU mining, setup phases, and control permissions. Central mining interface component. |

###### src/containers/navigation/components/VersionChip/

| File | Description |
|------|-------------|
| `VersionChip.tsx` | React component displaying app version and network info with connection status. Exports: VersionChip (default function). Dependencies: react-i18next, ConnectedPulse, styled components, network utility. Features: Version display from VITE_TARI_UNIVERSE_VERSION environment variable, testnet label for non-mainnet, connection pulse indicator, divider styling. Layout: pulse → divider → network label → version. Used by: Navigation components to show app status and version information. |
| `styles.ts` | Styled components for version chip layout and styling. Exports: Wrapper (main chip container), Divider (separator line). Dependencies: styled-components. Features: Horizontal flex layout with 4px gap, 21px height, 10px/8px padding, rounded corners (20px), theme-based background/colors, max-content width, 11px font with 600 weight, white-space nowrap, slight vertical transform (1px). Divider is 1x12px with 20% white opacity. Used by: VersionChip for consistent styling. |

###### src/containers/navigation/components/VersionChip/ConnectedPulse/

| File | Description |
|------|-------------|
| `ConnectedPulse.tsx` | React component displaying network connection status with animated pulse effect. Exports: ConnectedPulse (default function). Dependencies: styled components (Dot, Pulse, Wrapper), useMiningMetricsStore. Features: Visual connection indicator with colored dot (green=connected, yellow=disconnected), animated pulse ring with 2s infinite animation. Reads isNodeConnected state from mining metrics store. Used by: VersionChip to show Tari network connectivity status with visual feedback. |
| `styles.ts` | Styled components for connection pulse animation with color states. Exports: Wrapper (9x9px container), Dot (5px connection indicator), Pulse (animated ring). Dependencies: styled-components with keyframes. Features: Pulse keyframe animation (scale 0→2, opacity 1→0), conditional colors based on connection state (green=#31eeaa connected, yellow=#f3c11c disconnected), centered positioning, 2s infinite animation timing. Provides visual feedback for network connectivity status. |

###### src/containers/navigation/components/Wallet/

| File | Description |
|------|-------------|
| `Wallet.styles.ts` | Styled-components for wallet balance display components. Exports WalletBalance (balance value container), WalletBalanceWrapper (46px height alignment container), WalletBalanceContainer (space-between layout container), BalanceVisibilityButton (circular 22px icon button for toggle balance visibility). Uses motion/react-m for animations and theme-aware colors. Designed for wallet balance display with visibility toggle functionality. |
| `WalletBalanceMarkup.tsx` | Wallet balance display component with animated number transitions and visibility controls. Features compressed/full balance format toggle on hover, balance hiding functionality, dynamic font sizing based on balance length, and animated character spinner for number updates. Exports memoized WalletBalanceMarkup component. Uses useTariBalance hook for balance logic, NumbersLoadingAnimation for loading states, and CharSpinner for smooth number transitions. Includes eye icon toggle for balance privacy. |

###### src/containers/navigation/components/Wallet/ListLoadingAnimation/

| File | Description |
|------|-------------|
| `ListLoadingAnimation.tsx` | List loading animation component for wallet interface. Creates staggered rectangle animation effect with 6 sequential elements. Uses opacity transitions with 0.15s stagger delay, automatically loops every cycle. Exports default ListLoadingAnimation with optional loadingText prop. Manages animation state via useEffect with cleanup for unmounting. Used to indicate loading state in wallet transaction lists and similar UI contexts. |
| `styles.ts` | Styled-components for ListLoadingAnimation. Defines Wrapper (full container), ListItemsWrapper (centered column layout), Rectangle (48px height loading bars with theme-aware backgrounds), LoadingText (centered overlay text with shadow and rounded background). Uses motion/react for animations. Theme-aware styling with dark/light mode support for loading indicators. |

###### src/containers/navigation/components/Wallet/NumbersLoadingAnimation/

| File | Description |
|------|-------------|
| `NumbersLoadingAnimation.tsx` | Numbers loading animation component for wallet balance displays. Creates 7 sequential square elements with scale and opacity animation effects. Uses 0.15s stagger delay with automatic looping cycle. Includes additional circular element for visual balance. Exports default NumbersLoadingAnimation with no props required. Used to indicate loading state for numerical wallet data like balances and transaction amounts. |
| `styles.ts` | Styled-components for NumbersLoadingAnimation. Defines Wrapper (31px height flex container), SquareWrapper (grouped layout for squares), Square (24x31px rectangles with rounded corners), Circle (22px circular element). Uses motion/react for animations and theme-aware backgrounds. Designed to mimic numerical digit placeholders during loading states. |

###### src/containers/navigation/components/Wallet/SyncTooltip/

| File | Description |
|------|-------------|
| `SyncTooltip.tsx` | Reusable tooltip component for wallet sync status information. Uses Floating UI for positioning with hover interactions and safe polygon hover detection. Exports default SyncTooltip with trigger, title, and text props. Features top placement with offset and shift middleware, automatic layout updates, and AnimatePresence for smooth fade transitions. Used to display sync-related information when hovering over wallet status indicators. |
| `styles.ts` | Styled-components for SyncTooltip. Defines Wrapper (relative container), Trigger (clickable full-width trigger), Menu (216px floating card with shadow and rounded corners), Title (primary text styling), Text (secondary text styling). Uses motion/react-m for animations and theme-aware background and text colors. Provides card-style tooltip appearance with proper spacing and typography hierarchy. |

###### src/containers/phase/ShuttingDownScreen/

| File | Description |
|------|-------------|
| `ShuttingDownScreen.styles.tsx` | Styled-components for application shutdown screen. Defines ShuttingDownScreenContainer as full-screen overlay (100vh height, z-index 100) with centered flex layout. Uses theme-aware background color and positions content in the center during application shutdown process. Provides overlay styling for graceful app termination. |
| `ShuttingDownScreen.tsx` | Application shutdown screen component displayed during app termination. Shows circular progress indicator and internationalized "shutting down" message. Exports default ShuttingDownScreen component. Uses i18next for localization and styled container for full-screen overlay. Provides user feedback during graceful shutdown process when the application is closing. |

###### src/containers/phase/Splashscreen/

| File | Description |
|------|-------------|
| `SplashScreenContainer.styles.tsx` | Styled-components for splash screen container during app initialization. Defines SplashScreenContainer (full viewport centering with 100vh/100vw dimensions) and LottieWrapper (300x300px container for Lottie animations). Provides centered layout for loading animation display during application startup phase. |
| `Splashscreen.tsx` | Splashscreen component displaying animated Tari Universe logo during app initialization. Uses DotLottieReact for smooth animations with theme-aware logo selection (black for light mode, white for dark mode). Imports Lottie JSON animations as URL assets and renders them in a centered container. |
| `Tari_Universe_Black_JSON.json` | [GENERATED] Lottie animation JSON for Tari Universe dark theme splashscreen. Large single-line JSON file containing Lottie animation data for app startup animation in dark mode. Used by: SplashScreen component during app initialization. Contains vector-based Tari Universe logo animation with dark theme colors, providing branded startup experience. |
| `Tari_Universe_White_JSON.json` | [GENERATED] Lottie animation JSON for Tari Universe light theme splashscreen. Large single-line JSON file containing Lottie animation data for app startup animation in light mode. Used by: SplashScreen component during app initialization. Contains vector-based Tari Universe logo animation with light theme colors, providing branded startup experience. |

#### src/hooks/

| File | Description |
|------|-------------|
| `index.ts` | Central exports for custom React hooks in Tari Universe. Re-exports hook collections from app-specific hooks, helper utilities, and mining-related hooks. Provides single import point for accessing all custom hooks across the application. |

###### src/hooks/airdrop/stateHelpers/

| File | Description |
|------|-------------|
| `useAirdropPolling.ts` | Hook for managing airdrop data polling and feature flag fetching. Implements debounced polling for community messages, user data, and X Space events every 60 seconds when enabled. Fetches feature flags (orphan chain UI, polling, send/recv UI, warmup, block bubbles) on app focus and periodic intervals. Uses Tauri event listeners for app focus detection. Includes cleanup for timeouts and intervals. |
| `useAirdropSetTokens.ts` | Hook for generating and setting airdrop authentication UUID tokens. Exports useAirdropSetTokenToUuid function that generates a UUID v4 token and sends it to the airdrop API for authentication setup. Returns encoded URI of generated UUID on success. Used for establishing airdrop authentication session when user is logged in. |
| `useAirdropTokensRefresh.ts` | Utility functions for refreshing airdrop authentication tokens. Exports handleRefreshAirdropTokens function that checks token expiration (5 hours ahead) and refreshes tokens via API call if needed. Includes refreshAirdropTokens function for making refresh API requests. Updates airdrop store with new tokens. Handles token lifecycle management to maintain authenticated sessions. |
| `useFetchAirdropToken.ts` | Hook for fetching airdrop tokens using auth UUID polling mechanism. Polls API every 2 seconds for token retrieval when authUuid is available and listening is enabled. Auto-clears interval on successful token fetch, triggers user data fetching, and sets install reward animations. Includes 5-minute timeout for auth attempts. Used during airdrop authentication flow to retrieve tokens after UUID generation. |

###### src/hooks/airdrop/utils/

| File | Description |
|------|-------------|
| `useAirdropAuth.ts` | Airdrop authentication hook managing OAuth flow for airdrop access. Exports useAirdropAuth() returning handleAuth() and authUrlCopied flag. Generates UUID tokens, enables telemetry consent, builds auth URLs with referral codes, opens URLs via Tauri shell API with clipboard fallback. Dependencies: uuid, Tauri shell plugin, useConfigCoreStore, useAirdropStore, useFetchAirdropToken. Used by airdrop authentication components for secure token-based access to airdrop services. |
| `useAvatarGradient.ts` | Avatar gradient generator hook creating deterministic gradients from usernames. Exports useAvatarGradient({ username, fallback, image }) returning style object with backgroundColor and backgroundImage. Implements seeded random number generation using string hash for consistent colors. Supports image URLs, username-based gradients, and fallback colors. No external dependencies. Used by user profile components and avatar displays for consistent visual identity generation. |
| `useHandleRequest.ts` | Airdrop API request handler with automatic token refresh and retry logic. Exports handleAirdropRequest<T>() async function handling GET/POST requests with Bearer token authentication. Implements token expiration detection, automatic refresh with 3-retry limit, error handling with exponential backoff (250ms intervals). Dependencies: useAirdropStore, handleRefreshAirdropTokens. Used throughout airdrop features for authenticated API communication with automatic token management. |

###### src/hooks/airdrop/ws/

| File | Description |
|------|-------------|
| `useAirdropWebsocket.ts` | Simple wrapper hook for airdrop WebSocket functionality. Exports useAirdropWebsocket() that initializes useRustWebsocket. Dependencies: useRustWebsocket. Used as convenient entry point for airdrop WebSocket features. Acts as facade pattern over more complex WebSocket implementation. |
| `useHandleWsGlobalEvent.ts` | WebSocket global event handler for airdrop system. Exports useHandleWsGlobalEvent() returning callback function processing WebsocketGlobalEvent types. Handles X_SPACE_EVENT by calling setXSpaceEvent action. Dependencies: airdropStoreActions, WebSocket types. Used by WebSocket system to process broadcast events affecting all users. Implements pattern matching for extensible global event handling. |
| `useHandleWsUserIdEvent.ts` | WebSocket user-specific event handler for airdrop notifications. Exports useHandleWsUserIdEvent() returning callback processing WebsocketUserEvent types. Handles REFERRAL_INSTALL_REWARD (sets flare animation), USER_SCORE_UPDATE and COMPLETED_QUEST (updates user points), X_SPACE_EVENT (sets latest event). Dependencies: WebSocket types, store actions. Used by WebSocket system to process user-targeted events. Implements event-driven user interface updates. |
| `useRustWebsocket.ts` | Core WebSocket connection manager for airdrop real-time communication. Exports useAirdropWebsocket() and WebsocketEventType interface. Manages WebSocket lifecycle, connects when tokens available, listens for Tauri 'ws-status-change' and 'ws-rx' events. Routes messages to global/user event handlers based on event names. Dependencies: Tauri event API, useAirdropStore, event handlers, socket utils. Used as central WebSocket coordinator enabling real-time airdrop features and notifications. |
| `useSendWsMessage.ts` | WebSocket message sending hook for airdrop communication. Exports useSendWsMessage() returning callback function and WebsocketMessageType interface. Uses Tauri webview API to emit 'ws-tx' events with message payload to main window. Dependencies: Tauri webview API. Used by airdrop components to send WebSocket messages through Tauri's event system. Provides type-safe outbound message interface. |

##### src/hooks/app/

| File | Description |
|------|-------------|
| `index.ts` | Export barrel for app-level hooks. Exports: useDisableRefresh, useEnvironment, useShuttingDown. Dependencies: Individual hook files. Simple re-export module providing centralized access to core application hooks used throughout the app for environment detection, refresh prevention, and shutdown handling. Used by: Components needing app-level functionality without direct imports. |
| `useDisableRefresh.ts` | React hook preventing page refresh and context menu in production builds. Exports: useDisableRefresh. Dependencies: React useEffect. Features: F5, Ctrl+R, Cmd+R key prevention, right-click context menu blocking (except on input/textarea elements), development mode bypass, automatic event listener cleanup. Security/UX measure to prevent accidental app exits in desktop environment. Production-only functionality preserving development workflow. |
| `useEnvironment.ts` | React hook detecting application environment based on hostname. Exports: useEnvironment, Environment enum. Dependencies: React useMemo. Returns Environment.Development for localhost hosts, Environment.Production otherwise. Memoized for performance. Used throughout app for environment-specific behavior, feature flags, and debugging controls. Simple but critical infrastructure hook. |
| `useProgressEventsListener.ts` | React hook listening to setup progress events from Tauri backend. Exports: useProgressEventsListener, types (ProgressStateUpdateEvent, ProgressTrackerUpdatePayload). Dependencies: Tauri event API, setup store actions, deepEqual utility. Features: Core/Node/Hardware/Mining/Wallet phase tracking, progress percentage updates, title parameters, completion status, deduplication via deepEqual, conditional logging. Critical hook for setup phase coordination between frontend and backend. |
| `useShuttingDown.ts` | React hook managing application shutdown sequence for Tari Universe. Handles window close events with proper state cleanup and Tauri integration. Prevents immediate window closure, sets shutdown state, resets all Zustand stores, and invokes backend exit_application command after 250ms delay. Ensures graceful application termination with proper cleanup of mining processes, wallet connections, and application state. Uses Tauri window API for close request interception and provides coordinated shutdown between frontend and backend systems. Critical for data integrity and proper resource cleanup during application exit. |
| `useTauriEventsListener.ts` | Comprehensive React hook managing all Tauri backend event communications. Exports: useTauriEventsListener (default). Dependencies: Tauri API, extensive store actions, backend state types. Features: 40+ event type handlers, mining status updates, wallet updates, config loading, phase unlocking, error handling, network status, device detection. Includes frontend_ready signal to backend. Core hook establishing bidirectional communication between React frontend and Rust backend. Essential for app state synchronization. |

##### src/hooks/helpers/

| File | Description |
|------|-------------|
| `formatting.ts` | Time formatting utility function. Exports: formatSecondsToMmSs. Dependencies: None. Converts total seconds to MM:SS format with zero-padding, handles floating point inputs gracefully using Math.floor. Returns formatted string like "03:45" for 225 seconds. Used throughout app for duration displays, countdown timers, and time-based UI components. |
| `index.ts` | Export barrel for utility helper hooks. Exports: useCopyToClipboard, useDetectMode, useInterval, useCountdown, formatting utilities. Dependencies: Individual helper hook files. Centralized access point for commonly used utility hooks throughout the application. Used by: Components needing clipboard, timing, theming, and formatting functionality without direct file imports. |
| `useCopyToClipboard.ts` | React hook providing clipboard copy functionality with feedback state. Exports: useCopyToClipboard. Dependencies: Tauri clipboard plugin, React hooks. Features: Text copying via writeText API, isCopied state management, 1.5s automatic reset timer, optional onCopied callback, error handling with console logging. Returns copyToClipboard function and isCopied boolean. Used throughout app for address copying, sharing links, and user-friendly clipboard interactions. |
| `useCountdown.ts` | React hook managing countdown timer with reconnection functionality. Exports: useCountdown. Dependencies: Tauri API invoke, React hooks. Interface: CountdownResult (seconds, start, stop). Features: Dual timeout management (connection retry + countdown display), automatic Tauri reconnect invoke, 1-second interval updates, cleanup on completion, stop functionality. Used for connection retry mechanisms with user feedback on reconnection timing. |
| `useDebounce.ts` | React hook providing debounced value updates with configurable delay. Exports: useDebouncedValue (default). Dependencies: React hooks. Parameters: initialValue, delay (default 250ms). Features: Timeout-based debouncing, automatic cleanup, delay configuration, state synchronization. Returns debounced value that updates only after delay period without new changes. Used for search inputs, form validation, and performance optimization of frequent updates. |
| `useDetectMode.ts` | React hook detecting and responding to system theme changes. Exports: useDetectMode. Dependencies: Tauri event API, Theme types, UI store actions. Features: Listens to 'tauri://theme-changed' events, automatic theme switching when config is set to 'system', setUITheme action dispatch, conditional activation based on display_mode config. Enables automatic dark/light mode switching based on OS preferences when user selects system theme mode. |
| `useInterval.ts` | React hook for managing setInterval with cleanup and dynamic delay. Exports: useInterval. Dependencies: React hooks (useEffect, useRef). Parameters: callback function, delay in milliseconds (null to disable). Features: Ref-stored callback to prevent stale closures, automatic cleanup on unmount, delay change handling, null delay support for disabling interval. Used for periodic updates, polling, and time-based functionality throughout the app. |

##### src/hooks/mining/

| File | Description |
|------|-------------|
| `index.ts` | Mining-related React hooks exports module for Tari Universe. Organizes mining-specific hooks including useBlockInfo for blockchain data, useEarningsRecap for mining rewards tracking, useMiningStatesSync for state synchronization, and useMiningUiStateMachine for UI state management. Provides centralized access to mining functionality hooks enabling consistent mining-related logic across components. Part of organized hook architecture separating mining concerns from general application hooks. |
| `useBlockInfo.ts` | Mining block information timer hook for blockchain visualization. Exports useBlockInfo() managing display and debug timers for block time tracking. Calculates time since last block using block_time from mining metrics, updates counters every 1000ms via useInterval. Dependencies: useBlockchainVisualisationStore, useMiningMetricsStore, calculateTimeSince, useInterval. Used by mining dashboard to show real-time block timing information and debug metrics. |
| `useEarningsRecap.ts` | Mining earnings recap hook for missed rewards detection. Exports useEarningsRecap() monitoring app focus events to check for missed coinbase transactions. Calculates total earnings from missed transactions matching recapIds, triggers win recap with count and totalEarnings. Dependencies: useBlockchainVisualisationStore, useWalletStore, Tauri event API. Used by mining interface to notify users of earnings accumulated while app was unfocused, enhancing user engagement. |
| `useMinerCount.ts` | Mining statistics hook for total miner count from airdrop API. Exports useMinerStats() and KEY_MINER_STATS constant. Uses React Query for caching and auto-refresh (30-second intervals, refetch on focus). Returns totalMiners count with fallback to 0. Dependencies: @tanstack/react-query, useAirdropStore. Used by mining dashboard to display community miner participation statistics. Implements error handling for failed API requests. |
| `useMiningStatesSync.ts` | Composite React hook orchestrating mining state synchronization for Tari Universe. Coordinates multiple mining-related hooks: useBlockInfo for blockchain data updates, useUiMiningStateMachine for UI state management, and useEarningsRecap for mining rewards tracking. Provides single hook for components to synchronize all mining-related state without managing multiple hook dependencies. Critical for ensuring consistent mining state across the dashboard and mining interface components. |
| `useMiningTime.ts` | Mining session timer hook tracking total and session mining duration. Exports useMiningTime() returning TimeSince object with formatted time components. Uses useInterval for 1000ms updates, tracks either session or total mining time via USE_SESSION_TIME_ONLY flag. Updates mining store state while mining active. Dependencies: useInterval, useMiningMetricsStore, useMiningStore, calculateTimeSince. Used by mining interface to display real-time mining duration with proper time formatting. |
| `useMiningUiStateMachine.ts` | Mining UI state machine controlling Tari Tower 3D animation states. Exports useUiMiningStateMachine() managing start/stop animation logic based on mining status. Implements retry mechanism (15 attempts, 2-second intervals) for animation stop operations. Coordinates with visual mode settings and setup completion state. Dependencies: @tari-project/tari-tower, mining/UI stores. Used by mining visualization to synchronize 3D animations with actual mining state. Handles edge cases and timeout management. |

##### src/hooks/swap/

| File | Description |
|------|-------------|
| `useIframeMessage.ts` | Iframe message handling hook for cryptocurrency swap interface communication. Exports useIframeMessage() hook and IframeMessage union type with MessageType enum. Handles swap flow messages: approval requests, confirmations, success/error states, height changes, fullscreen toggles. Defines comprehensive message payloads for transaction details, fees, slippage, price impact. Dependencies: swap types. Used by swap iframe components for parent-child window communication. Implements type-safe message passing for swap operations. |
| `useIframeUrl.ts` | Swap iframe URL management hook with automatic fetching and fallback. Exports useIframeUrl() returning swap interface URL. Fetches URL from airdrop API via handleAirdropRequest, implements 1-second retry interval until URL obtained, provides fallback to 'https://tari.com/swaps'. Uses ref for URL persistence and interval cleanup. Dependencies: useHandleRequest. Used by swap components to dynamically load exchange interface URL with resilient fallback mechanism. |

###### src/hooks/swap/lib/

| File | Description |
|------|-------------|
| `types.ts` | Type definitions for cryptocurrency swap functionality. Exports SwapDirection ('fromXtm' \| 'toXtm'), SwapStatus (processing/success/error states), and SelectableTokenInfo interface with token metadata (symbol, address, balance, USD value, decimals). Defines comprehensive token structure for swap operations. No dependencies. Used throughout swap components for type safety and consistent token representation. Includes raw balance (bigint) and formatted balance strings. |

##### src/hooks/wallet/

| File | Description |
|------|-------------|
| `useTariBalance.ts` | Tari wallet balance display hook with formatting and privacy controls. Exports useTariBalance() returning formatted balance values, display toggles, and animation state. Handles balance privacy (****), long/compact formats, wallet scanning states, hover interactions. Supports both calculated and available balance display with different formatting presets. Dependencies: formatNumber utils, useWalletStore, useUIStore. Used by wallet components for consistent balance presentation with user experience enhancements. |

#### src/store/

| File | Description |
|------|-------------|
| `appStateStore.ts` | Application state Zustand store for Tari Universe global application status. Manages error states (general errors, critical problems), settings dialog visibility, external dependencies tracking, missing dependencies detection, issue references, application versions, release notes, and update availability. Includes network status monitoring and orphan chain detection for blockchain health. Provides centralized state for application-wide status information, error handling, and system health monitoring. Critical for application lifecycle management and user notification systems. |
| `consts.ts` | Application feature flags and constants definition. Exports: FEATURES object, BRIDGE_TAPPLET_ID. Features: Feature flag strings for UI components (orphan-chain-ui-disabled, ui-send-recv, ui-warmup, swaps-enabled, ui-bridge-swaps, ui-block-bubbles), polling flag, bridge tapplet identifier (0). Used throughout app for conditional feature rendering, A/B testing, and configuration management. Central location for enabling/disabling experimental features. |
| `create.ts` | Enhanced Zustand store creation utility with global reset functionality for Tari Universe. Wraps zustand/traditional createWithEqualityFn to add store reset capability. Maintains set of reset functions for each created store, enabling resetAllStores() to reset all application state to initial values. Used throughout the application for creating type-safe Zustand stores with consistent reset behavior. Critical for testing, state cleanup, and application restart scenarios. Follows Zustand v5 migration patterns for stable selector outputs. |
| `index.ts` | Central store exports for Tari Universe React state management. Re-exports all store modules including useUIStore, useMiningStore, useWalletStore, useAirdropStore, and other specialized stores. Also exports shared actions, types, and utility functions. Serves as the main entry point for importing store functionality across the application. |
| `types.ts` | Core TypeScript type definitions for Tari Universe store system. Defines fundamental types: modeType for mining modes (Eco, Ludicrous, Custom) and displayMode for theme settings (system, dark, light). These types ensure type safety across the application for mining configuration and UI theming. Used throughout store actions, components, and configuration management for consistent typing and preventing invalid state values. |
| `useAirdropStore.ts` | Zustand store for airdrop/referral system state management. Exports: useAirdropStore hook, BonusTier interface, UserPoints interface, User interface for airdrop participants, ReferralCount tracking. Manages airdrop game state, gem rewards system, user referral codes, bonus tier calculations, and social gaming features. Dependencies: Zustand create utility, WebSocket event types, config types. Used by: airdrop components, referral UI, gaming interfaces. Handles testnet reward distribution and friend invitation mechanics with 5000 gem rewards per referral. |
| `useAppConfigStore.ts` | Centralized Zustand store for application configuration management in Tari Universe. Manages four main configuration domains: Core (telemetry, notifications, auto-update, P2Pool, Tor settings), Wallet (Monero addresses, keyring access), Mining (CPU/GPU settings, modes, thread counts, engine selection), and UI (theme, language, display modes). Includes backend in-memory configuration for dynamic settings and visual mode toggle loading state. Defines initial state values for all configuration categories and provides typed interfaces for configuration management. Critical for persisting user preferences, mining configuration, and application behavior settings across sessions. Integrates with backend configuration system for seamless settings synchronization. |
| `useBlockchainVisualisationStore.ts` | Blockchain visualization store managing mining success/failure animations, block timing, earnings display, and UI coordination. Handles win/fail scenarios with Tari Tower animations, implements debounced block processing, manages recap functionality for missed earnings, transaction refreshing, and wallet balance updates. Coordinates with mining controls enablement, visual mode settings, window visibility detection. Dependencies: Tari Tower animations, multiple stores, wallet actions. Central orchestrator for mining visualization feedback and block event processing with sophisticated timing controls. |
| `useExchangeStore.ts` | Zustand store managing exchange integration and universal miner functionality. Exports: useExchangeStore, setShowExchangeModal, setExchangeContent, setShowUniversalModal, setExchangeMiners, fetchExchangeMiners, fetchExchangeContent. Dependencies: exchange types, wallet store, config store. State: content, showExchangeAddressModal, exchangeMiners, showUniversalModal. Features: API fetching for exchange assets, content management, modal state control, seedless UI activation. Handles exchange platform integration for seamless mining-to-exchange workflows. |
| `useMiningMetricsStore.ts` | Zustand store for real-time mining metrics and base node status. Exports: useMiningMetricsStore. Dependencies: create wrapper, app-status types. State: isNodeConnected, base_node_status (block height/time, sync status, hashrates), connected_peers, gpu_devices, gpu_mining_status, cpu_mining_status. Initial state with sensible defaults (all false/0). Core metrics store updated by Tauri events, providing live mining statistics for UI components. |
| `useMiningStore.ts` | Mining state management store for CPU/GPU mining controls in Tari Universe. Tracks mining initiation status, hashrate readiness, mode changes, session timing, thread management via MaxConsumptionLevels, and network configuration. Manages custom mining levels dialog, GPU device exclusion, and available engines (Sha3x, RandomX, Blake3). Coordinates with mining controls and backend mining managers for performance optimization. |
| `useNodeStore.ts` | Blockchain node state management store for Tari Universe. Manages NodeType configuration (Local, Remote, RemoteUntilLocal, LocalAfterRemote), node identity including public key and addresses, and connection details. Tracks background node synchronization updates with deep equality checking to prevent unnecessary re-renders. Provides utility functions for node type updates and background sync state management. Critical for blockchain connectivity configuration, node switching logic, and real-time node synchronization status tracking throughout the application. |
| `useP2poolStatsStore.ts` | P2Pool mining statistics store managing connection info, peer data, RandomX/SHA3X statistics. Exports fetchP2poolStats() and fetchP2poolConnections() functions with store state. Tracks connected peers, network info, connection counters (pending/established), mining algorithm statistics. Implements error handling for backend communication failures. Dependencies: Tauri invoke API, P2Pool types. Used by mining dashboard to display P2Pool network health, peer connections, and mining pool performance metrics. |
| `usePaperWalletStore.ts` | Paper wallet store managing modal display and QR code generation for offline wallet backups. Exports store with modal visibility, QR code value, and identification code state. Provides setters for modal control, QR code content, and identification code management. No external dependencies. Used by wallet backup components to generate and display paper wallet QR codes for secure offline storage. Simple state management for wallet backup workflow. |
| `useSetupStore.ts` | Setup phase state management store for Tari Universe application initialization. Tracks setup progress through multiple phases: CPU/GPU mining unlock, wallet unlock, hardware phase completion, and overall setup completion. Manages setup payloads for each phase (core, hardware, node, wallet, mining) and disabled phases configuration. Controls app unlock state determining when user can access main application features. Critical for guiding users through first-time setup, ensuring proper initialization sequence, and managing setup phase transitions during application onboarding. |
| `useShareRewardStore.ts` | Modal state management store for sharing reward/transaction information. Exports: useShareRewardStore hook with setShowModal(), setItemData() actions. Dependencies: TransactionInfo type from app-status, store/create wrapper. Used by: reward sharing components and transaction displays. Manages showModal boolean and item data (TransactionInfo \| null) for displaying reward details in modal dialogs. |
| `useShellOfSecretsStore.ts` | Shell of Secrets (SOS) game state management store for referral program and countdown timer. Exports: useShellOfSecretsStore hook, getSOSTimeRemaining() utility function. Dependencies: CrewMember type from ws.ts, store/create wrapper. Key features: referral tracking, widget visibility, bonus time calculation, WebSocket connection monitoring, countdown to SOS_GAME_ENDING_DATE (2025-01-30). Used by: Shell of Secrets game components, referral system. |
| `useStagedSecurityStore.ts` | Security setup flow modal state management store. Exports: useStagedSecurityStore hook with setShowModal(), setShowReminderTip(), setShowCompletedTip() actions. Dependencies: store/create wrapper. Used by: security setup components, onboarding flow. Manages three boolean states for coordinating security setup UI: main modal visibility, reminder tooltip display, and completion confirmation display. |
| `useTappletSignerStore.ts` | Tapplet (Tari application) signer state management store for blockchain transactions. Exports: useTappletSignerStore hook with initTappletSigner(), setTappletSigner(), runTransaction() actions. Dependencies: TappletSigner, TransactionEvent, TappletSignerParams types, store error handling. Used by: tapplet components, transaction processing. Manages TappletSigner initialization, provider switching, and message-based transaction execution with error handling. |
| `useTappletsStore.ts` | Tapplets (Tari applications) management store for running decentralized applications. Exports: useTappletsStore hook with setActiveTapp(), setActiveTappById(), deactivateTapplet(), bridge swap management actions. Dependencies: ActiveTapplet, BridgeTxDetails types, useTappletSignerStore, Tauri invoke, feature flags. Used by: tapplet browser, bridge transactions. Manages active tapplet state, built-in vs external tapplets, bridge swap feature flags, and ongoing bridge transactions. |
| `useUIStore.ts` | UI state management store using Zustand for Tari Universe interface controls. Manages theme (light/dark), sidebar state (mining/wallet), dialog visibility (logs, restart, update, etc.), connection status, WebGL support detection, admin sections (setup/main/shutdown), and visual preferences. Includes Tower canvas positioning, block bubbles toggle, and responsive layout calculations. Detects system color scheme preference and handles splashscreen transitions. |
| `useWalletStore.ts` | Wallet state management store handling Tari address (base58/emoji), balance tracking, transaction history (coinbase and regular), and WXTM bridge integration. Manages wallet scanning progress, import status, reward history loading, and transaction pagination. Includes cold wallet bridge transactions via BackendBridgeTransaction interface. Integrates with refreshTransactions action for updating transaction data from backend services. |

##### src/store/actions/

| File | Description |
|------|-------------|
| `airdropStoreActions.ts` | Comprehensive airdrop store actions managing authentication, tokens, user data, and feature flags. Exports 25+ functions including airdropSetup(), token management, user data fetching, JWT parsing, feature flag controls. Handles OAuth flow, token refresh, WebSocket initialization, bonus tiers, community messages, X Space events. Implements automatic token refresh, error handling, logout procedures. Dependencies: Tauri invoke API, multiple stores, WebSocket utils. Central coordination point for all airdrop-related operations and state management. |
| `appConfigStoreActions.ts` | Application configuration store actions managing all app settings and preferences. Exports 35+ functions handling config loading, mining settings (CPU/GPU), UI preferences (theme, language), network settings (Tor, P2Pool), wallet configuration, auto-launch, telemetry, and experimental features. Implements error handling with state rollback, async Tauri backend communication, mining restart logic. Dependencies: Tauri invoke API, i18next, multiple config stores, mining actions. Central configuration management coordinating frontend-backend setting synchronization. |
| `appStateStoreActions.ts` | Application state management actions handling app lifecycle, dependencies, errors, and setup phases. Exports functions for version fetching with retry logic, external dependency management, critical error handling, network status updates, release notes display, setup phase coordination. Implements setup restart logic affecting multiple phases (Core, Node, Hardware, Mining, Wallet). Dependencies: Tauri invoke API, toast notifications, multiple stores, setup actions. Used for application health monitoring and state coordination during startup and runtime. |
| `index.ts` | Central exports module for all Zustand store actions in Tari Universe. Organizes actions by domain: airdrop operations (setup, logout, tokens, user details), app configuration (telemetry, mining settings, language, auto-launch), application state (versions, dependencies, errors, updates), mining operations (mode changes, device management, start/stop), UI state (theme, dialogs, WebGL support), wallet operations (transactions, seed import, balance), and mining metrics (GPU devices, status updates, peer connections). Provides single import point for all store actions enabling clean imports and organized state management architecture throughout the application. |
| `miningMetricsStoreActions.ts` | Mining metrics store actions managing GPU/CPU mining status and node connectivity. Exports functions for GPU device management, mining status updates, peer connection handling, base node status processing. Implements automatic GPU mining enablement based on device availability, mining animation coordination with connection state, blockchain visualization height updates. Dependencies: mining types, config stores, Tari Tower animation. Used by mining system to coordinate hardware detection, status updates, and UI synchronization with mining operations. |
| `miningStoreActions.ts` | Mining store actions managing CPU/GPU mining operations and state. Exports: changeMiningMode, startMining, stopMining, startCpuMining, startGpuMining, stopCpuMining, stopGpuMining, restartMining, getMaxAvailableThreads, setEngine, toggleDeviceExclusion, and utility functions. Dependencies: Tauri invoke API, mining stores, blockchain visualization. Features: Mode switching (Eco/Ludicrous/Custom), device exclusion, engine selection, thread management, error handling with rollback, blockchain visualization integration. Core mining control layer coordinating frontend actions with backend Rust mining processes. |
| `setupStoreActions.ts` | Application setup phase store actions managing unlock states and initialization sequence. Exports functions handling app unlock (Tower animation loading, bridge transactions), wallet/mining unlocks with auto-start logic, setup phase progress tracking, disabled phase management. Coordinates mining startup based on configuration, manages visual mode initialization, handles setup completion events. Dependencies: Tari Tower, multiple stores, setup types, exchange integration. Central coordinator for application startup sequence and feature unlocking based on backend readiness. |
| `stagedSecurityActions.ts` | Staged security store actions for managing security modal display. Exports handleShowStagedSecurityModal() function that triggers security modal visibility. Minimal action file focused on modal state management. Dependencies: useStagedSecurityStore. Used by security components to display staged security features or prompts. Part of security workflow coordination system. |
| `uiStoreActions.ts` | UI store actions managing theme, dialogs, sidebar, and 3D tower animation. Exports: setUITheme, setDialogToShow, setSidebarOpen, enableTowerAnimation, handleConnectionStatusChanged, toggleHideWalletBalance, and more. Dependencies: tari-tower 3D engine, UI store, theme types. Features: Light/dark theme switching with animation property updates, tower animation loading/cleanup, WebGL support detection, sidebar state with tower offset calculation, connection status management, dialog management. Critical UI coordination layer integrating React state with 3D visualization. |
| `walletStoreActions.ts` | Wallet store actions managing transaction history, bridge operations, seed import, address management, and balance calculations. Exports functions for transaction fetching with pagination, bridge transaction/address retrieval, seed word import, wallet address setting, balance calculations including pending transactions. Implements error handling, loading states, transaction deduplication. Dependencies: Tauri invoke API, WXTM bridge API, wallet types, error handling. Central wallet operation coordinator handling both Tari and bridge wallet functionality. |

##### src/store/types/

| File | Description |
|------|-------------|
| `setup.ts` | TypeScript interface definitions for application setup state management. Exports SetupState interface defining unlock flags for different app phases (CPU/GPU mining, wallet, hardware, app), setup completion status, progress tracking payloads for each setup phase, and disabled phases array. Dependencies: progress event types, backend state types. Used by setup store and related components for type-safe setup state management and progress tracking throughout application initialization. |

#### src/theme/

| File | Description |
|------|-------------|
| `GlobalStyle.ts` | Global CSS reset and styling for Tari Universe application. Implements comprehensive element resets for buttons, inputs, fieldsets with accessibility-focused design including focus-visible outlines and proper keyboard navigation. Removes default browser styling while maintaining semantic behavior. Includes platform-specific input number styling (WebKit/Firefox), cross-browser compatibility fixes, and consistent focus indicators. Defines GlobalStyle with tower canvas visibility control and responsive design foundations. Critical for consistent cross-platform styling, accessibility compliance, and providing clean foundation for styled-components theming system. |
| `ThemeProvider.tsx` | Theme provider component for Tari Universe styled-components theming system. Manages theme selection between light and dark modes based on user preferences or system settings. Integrates with UI store for theme state management and provides fallback to preferred theme when stored theme is invalid or set to 'system'. Wraps application with styled-components ThemeProvider making theme context available to all styled components. Enables consistent theming across entire application including mining interface, wallet views, and all UI components. Essential for responsive design and user experience customization. |
| `components.ts` | Theme component settings defining design system constants for UI consistency. Exports componentSettings with shape (border radius values for app, dialog, buttons) and typography (Poppins font family, font sizes, weights, line heights, letter spacing for h1-h6, p, span elements). Includes ThemeComponents type export. No dependencies. Used throughout styled components for consistent visual design. Defines comprehensive typography scale and border radius system for the entire application UI. |
| `gradients.ts` | Theme gradient definitions for dark and light mode color schemes. Exports darkGradients and lightGradients objects with CSS gradient strings for primary/secondary colors, radial backgrounds, setup backgrounds, mining button states (started, hover). Provides consistent gradient styling across theme modes. No dependencies. Used by styled components and theme system for gradient-based visual elements like backgrounds, buttons, and overlay effects. Supports theme switching with mode-appropriate gradients. |
| `styles.ts` | Global styled-components definitions and layout constants for main dashboard. Exports: SB_MINI_WIDTH (78px), SB_WIDTH (356px), SB_SPACING (15px) constants, DashboardContainer, DashboardContent, Background styled components. Dependencies: styled-components, clouds background image, convertHexToRGBA utility. Used by: main dashboard layout components. Defines responsive sidebar dimensions, dashboard flex layout with gradient overlays, and background image with theme-aware brightness filtering. |
| `themes.ts` | Theme system aggregator combining palettes and component settings into complete styled-components themes. Exports: lightTheme, darkTheme (DefaultTheme objects). Dependencies: lightPalette, darkPalette, componentSettings. Used by: styled-components ThemeProvider, theme context providers. Creates final theme objects by merging color palettes with component-specific styling configurations for consistent theming across the entire application. |
| `types.ts` | TypeScript type definitions for theme system structure and color variants. Exports: Theme ('light'\|'dark'), ThemePalette interface, Palette interface, color type utilities. Dependencies: Colours, ColoursAlpha from color palettes. Used by: all theme files, styled-components. Defines comprehensive type safety for theme modes, color properties (main/dark/light/accent/etc), gradients, and semantic color groups (primary/secondary/success/warning/error). |

##### src/theme/palettes/

| File | Description |
|------|-------------|
| `colors.ts` | Core color palette definitions for Tari Universe theme system. Exports: colors object with blue, orange, green, red, brightRed, teal, gothic, tariPurple, grey, greyscale, success, info, warning, error, brightGreen color groups. colorsAll combines all colors and ramp. Dependencies: colorsAlpha for transparency variants. Used by: light.ts, dark.ts theme palettes. Provides comprehensive color scales (50-950) for UI components, semantic colors (success/error/warning), and Tari brand colors (tariPurple). |
| `colorsAlpha.ts` | Alpha transparency color definitions for theme system overlays and translucent elements. Exports: colorsAlpha object with lightAlpha, darkAlpha, tariPurpleAlpha, warningDarkAlpha, errorDarkAlpha, greyscaleAlpha variants. Used by: colors.ts, light.ts, dark.ts theme palettes. Provides percentage-based transparency (5%-90%) for white/black overlays, brand color transparencies, and semantic state overlays for hover effects, shadows, and background blending. |
| `dark.ts` | Dark theme palette configuration for Tari Universe. Exports: darkPalette (ThemePalette) with mode: 'dark'. Dependencies: colors.ts, colorsAlpha.ts, darkGradients, ThemePalette type. Used by: theme system for dark mode. Defines complete dark mode color scheme including primary (tariPurple[900]), backgrounds (grey[900/700]), text colors, semantic states, and component colors optimized for dark UI with proper contrast ratios. |
| `light.ts` | Light theme palette configuration for Tari Universe. Exports: lightPalette (ThemePalette) with mode: 'light'. Dependencies: colors.ts, colorsAlpha.ts, lightGradients, ThemePalette type. Used by: theme system for light mode. Defines complete light mode color scheme including primary (tariPurple[600]), backgrounds (grey[50]/white), text colors, semantic states, and component colors optimized for light UI with accessibility-compliant contrast ratios. |

#### src/types/

| File | Description |
|------|-------------|
| `app-status.ts` | Core TypeScript type definitions for Tari Universe application status and configuration. Defines interfaces for TorConfig (control port, bridges), ExternalDependencyStatus enum, mining-related types including transaction information, wallet balance, CPU/GPU mining status, and hardware configuration. Contains comprehensive type definitions for application state including mining metrics, node status, P2Pool statistics, connection information, and mining mode configurations. Includes types for external dependencies, system requirements, GPU device information, and transaction history structures. Critical for TypeScript type safety across the entire application ensuring consistent data structures between frontend and backend communication via Tauri commands. |
| `backend-state.ts` | Backend event system type definitions for Tauri frontend-backend communication. Exports: SetupPhase enum, BACKEND_STATE_UPDATE constant, BackendStateUpdateEvent union type with 30+ event variants. Dependencies: event payloads, app-status types, config types. Used by: event listeners, state management stores. Defines comprehensive event schema for wallet updates, mining status, node synchronization, setup phases, and application lifecycle events. |
| `configs.ts` | TypeScript interfaces for Tari Universe configuration management. Defines comprehensive configuration structures: ConfigCore (P2Pool, Tor, telemetry, auto-launch, node settings), ConfigWallet (Monero addresses, keyring access), ConfigUI (display modes, language, experimental features), and ConfigMining (mining modes, CPU/GPU settings, power levels). Includes mining mode types, thread configurations, and custom mining parameters. Critical for type-safe configuration management across frontend and backend, ensuring consistent data structures for application settings, mining parameters, and user preferences. |
| `events-payloads.ts` | Event payload type definitions for backend-to-frontend event data structures. Exports: WalletAddressUpdatePayload, NewBlockHeightPayload, DetectedDevicesPayload, CriticalProblemPayload, NodeTypeUpdatePayload, BackgroundNodeSyncUpdatePayload, AppInMemoryConfigChangedPayload, etc. Dependencies: app-status types. Used by: backend-state.ts, event handlers. Defines structured data formats for wallet addresses, block heights, GPU devices, node connections, sync progress, and configuration updates. |
| `exchange.ts` | Exchange platform and mining partner type definitions for branded mining experiences. Exports: ExchangeContent, ExchangeMiner, ExchangeMinerAssets interfaces. Used by: exchange integrations, branded mining campaigns. Defines exchange branding (colors, logos, campaign content), miner identification (id/name/slug), and asset combinations for customizable mining UI based on partner exchanges with reward percentages and branding elements. |
| `invoke.d.ts` | Tauri command interface type definitions extending @tauri-apps/api/core invoke function. Exports: 80+ typed function overloads for backend commands. Dependencies: app-status types, language types, store types, tapplet types. Used by: frontend components calling backend. Provides type safety for all Tauri invoke calls including mining control, wallet operations, settings management, external dependencies, telemetry, and tapplet integration. |
| `mining.ts` | Mining-related time calculation type definitions. Exports: BlockTimeData interface with optional days, hours, minutes, seconds fields and string variants. Used by: mining statistics components, block time calculations. Simple interface for displaying time-based mining metrics like estimated block discovery time and mining duration with both numeric and formatted string representations. |
| `transactions.ts` | Tari blockchain transaction type definitions matching tari.rpc.rs enums. Exports: TransactionDirection enum (Inbound=1, Outbound=2), TransactionStatus enum with 15 states from Completed to CoinbaseNotInBlockChain. Used by: wallet components, transaction history, status displays. Provides type safety for transaction processing states and direction indicators, ensuring frontend-backend consistency for transaction status tracking. |
| `ws.ts` | WebSocket event system type definitions for real-time game features and social interactions. Exports: WebsocketEventNames enum, SignData, CrewMember, UserScoreUpdate, XSpaceEvent, WebsocketUserEvent/GlobalEvent union types. Dependencies: XSpaceEventType utility. Used by: Shell of Secrets game, referral system, crew features. Defines event structure for quest completion, mining status updates, crew member tracking, user scoring, and X Space social events. |

##### src/types/tapplets/

| File | Description |
|------|-------------|
| `TappletSigner.ts` | Tapplet signer class providing blockchain interaction interface for Tari applications. Exports: TappletSigner class with account management, wallet operations, bridge transaction handling. Dependencies: store hooks, Tauri invoke, app-status types. Used by: tapplet execution environment, useTappletSignerStore. Implements window messaging, account access, balance queries, one-sided transactions, bridge operations, and language/environment utilities for embedded tapplets. |
| `tapplet.types.ts` | Core type definitions for Tapplet (Tari application) system interfaces. Exports: SupportedChain type, WindowSize, TappletSignerParams, AccountData, ActiveTapplet, SendOneSidedRequest, BridgeTxDetails interfaces. Used by: TappletSigner, tapplet stores, tapplet components. Defines structure for blockchain network support, window management, account identification, transaction requests, and bridge transaction details for decentralized applications running within Universe. |
| `transaction.ts` | Transaction event type definitions for tapplet-to-signer communication. Exports: TransactionEvent interface with methodName, args, id fields. Dependencies: TappletSigner class. Used by: useTappletSignerStore, tapplet message handling. Defines structure for method invocation events between tapplets and the signer, excluding the runOne method and providing type-safe parameter passing for blockchain operations. |

#### src/utils/

| File | Description |
|------|-------------|
| `XSpaceEventType.ts` | Enumeration for X Space (Twitter Spaces) event type classification. Exports: XSpaceEventType enum with 'link' and 'event' values. Used by: ws.ts WebSocket events, Shell of Secrets features. Simple utility for categorizing social media events as either direct links or scheduled events within the gamified social features of Universe. |
| `calculateTimeSince.ts` | Time difference calculation utility for elapsed time formatting. Exports: TimeSince interface, calculateTimeSince() default function. Used by: time-based UI components, mining statistics. Converts timestamp differences into human-readable format with days, hours, minutes, seconds, including both numeric values and formatted strings with proper pluralization for displaying time elapsed since events. |
| `convertHex.ts` | Hex color to RGBA conversion utility for theme and styling operations. Exports: convertHexToRGBA() function with opacity support. Used by: theme system, styled-components, background overlays. Converts hex color codes (3 or 6 digit) to RGBA format with configurable opacity, handles backward compatibility for percentage-based opacity values, commonly used for transparent color variants. |
| `formatters.ts` | Comprehensive number and value formatting utilities with i18n support. Exports: FormatPreset enum, formatNumber(), formatHashrate() functions, helper utilities. Dependencies: i18next for localization. Used by: mining statistics, wallet balances, performance metrics. Provides formatted display for XTM cryptocurrency amounts, percentages, hashrates with unit scaling (H/s to PH/s), compact notation, and locale-aware decimal formatting. |
| `getTimeAgo.ts` | Relative time formatting utility for human-readable timestamps. Exports: timeAgo() function. Used by: transaction history, activity feeds, status displays. Converts Unix timestamps to relative time strings ('X secs ago', 'X mins ago', 'X hrs ago', 'X days ago') with proper pluralization for displaying when events occurred relative to current time. |
| `getTxStatus.ts` | Transaction status analysis and formatting utilities with i18n support. Exports: getTxTypeByStatus(), getTxStatusTitleKey(), getTxTitle() functions. Dependencies: transaction types, i18next, transaction components. Used by: transaction displays, wallet history. Categorizes transactions by type (mined/received/sent), determines status states (pending/failed/complete), and generates localized display titles with block heights and payment messages. |
| `index.ts` | Utility functions barrel export file for centralized access to common utilities. Exports: all functions from calculateTimeSince, convertHex, formatters, shared-logger, truncateString modules. Used by: components importing utilities via @app/utils path. Provides single import point for time calculations, color conversions, number formatting, logging, and string manipulation utilities. |
| `network.ts` | Network utility functions for Tari Universe blockchain network management. Defines Network enum including MainNet, StageNet, NextNet, LocalNet, Igor, and Esmeralda networks. Groups networks into categories (testnet, stagenet, mainnet) for logical organization and configuration. Provides utility functions: isMainNet() for mainnet detection, getNetworkGroup() for network categorization, and getExplorerUrl() for blockchain explorer URL generation with network-specific subdomain mapping. Integrates with mining store for network state management. Critical for network-aware features, explorer integration, and ensuring correct blockchain connectivity based on selected network configuration. |
| `objectDeepEqual.ts` | Deep equality comparison utility for objects and primitive values. Exports: deepEqual() function that recursively compares two values by traversing object properties and checking strict equality. Used throughout the React frontend for state comparisons, prop diffing, and memoization dependencies. Handles null/undefined values, type mismatches, and nested object structures. Essential for preventing unnecessary re-renders and optimizing performance in React components. |
| `shared-logger.ts` | Shared logging system that bridges frontend console logs to the Tauri backend logging infrastructure. Exports: setupLogger() function that overrides console.info, console.warn, and console.error. Dependencies: @tauri-apps/api/core for invoke() calls. Used by: App initialization to establish unified logging across frontend and backend. Includes universe version in log messages and JSON serialization of log arguments. Enables centralized log collection and analysis in the Rust backend via log_web_message command. |
| `socket.ts` | WebSocket connection management for real-time communication between frontend and Tauri backend. Exports: socketInitialised flag, initialiseSocket(), removeSocket() functions. Dependencies: @tauri-apps/api/core and @tauri-apps/api/event. Used by: App components for real-time mining status updates, wallet events, and system notifications. Manages connection state, auto-reconnection, and proper cleanup. Listens for 'ws-status-change' events to track connection health and provides connection lifecycle management. |
| `truncateString.ts` | String truncation utilities with emoji support for displaying long text in constrained UI spaces. Exports: truncateString() for simple end truncation, truncateMiddle() for middle truncation preserving start/end. Dependencies: emoji-regex for proper emoji handling. Used by: Wallet address displays, transaction hashes, file names, and other UI components. Handles Unicode emoji characters correctly to prevent breaking emoji sequences when truncating. Essential for responsive design and user-friendly text display. |

### tari-win-bundler/

| File | Description |
|------|-------------|
| `Bundle.wxs` | WiX Toolset bundle configuration for Windows installer creation. Defines Windows installer bundle with Visual C++ Redistributable 2015-2022 (x64) as prerequisite and main Tari Universe MSI package. Uses environment variables for dynamic configuration (TARI_UNIVERSE_EXECUTABLE_AND_FOLDER_NAME, TARI_UNIVERSE_APP_VERSION, TARI_UNIVERSE_UPGRADE_CODE). Includes custom bootstrapper application with logo, theme, and localization files. Configures architecture detection, prerequisite checking, and conditional installation based on system requirements. Essential for Windows distribution and automated installation experience. |
| `Tari Universe.wixproj` | WiX project file for building Windows installer. Defines MSBuild project configuration for compiling WiX source files into Windows installer bundles. Contains build targets, compiler settings, and project dependencies for generating the final Windows installer package. Used by build pipeline to create distributable Windows installation files with proper versioning and signing. |
| `Tari Universe.wixproj.user` | User-specific WiX project settings file for Visual Studio/MSBuild. Contains local development environment configurations and build preferences specific to individual developers. Typically includes local paths, debug settings, and personal build configurations. Should not be committed to version control as it contains user-specific preferences, but appears to be included for reference or shared development environment setup. |
| `Theme.wxl` | WiX localization file defining English (en-us) text strings for Windows installer UI. Contains localized strings for all installer dialogs including welcome messages, progress indicators, button labels, and error messages. Defines user-facing text for installation, modification, repair, uninstall, and failure scenarios. Uses WiX bundle variables for dynamic content like [WixBundleName] and [WixBundleVersion]. Essential for providing professional, localized installer experience and potential future multi-language support. |
| `Theme.xml` | WiX theme definition file for Windows installer UI layout and styling. Defines visual appearance including fonts (Segoe UI), window dimensions (632x476), background image (clouds.bmp), and UI element positioning. Contains page definitions for Help, Loading, Install, Options, Progress, Modify, Success, and Failure states with buttons, labels, and hypertext controls. Specifies installation workflow, progress bars, license agreement UI, and user interaction elements. Creates professional installer experience matching Windows design standards. |

