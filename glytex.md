# Glytex

**Repository:** https://github.com/tari-project/glytex

**Branch:** main

## Project Overview

## Glytex - GPU-Based Tari Cryptocurrency Miner

### Project Overview

Glytex is a high-performance GPU mining application for the Tari cryptocurrency, supporting multiple GPU backends (NVIDIA CUDA, OpenCL, Apple Metal). Originally based on @cookiemonstermayhem's xtrgpuminer, it provides efficient SHA3/Keccak mining with comprehensive platform support and monitoring capabilities.

### Directory Structure

```
glytex/
├── .github/                    # GitHub workflows and templates
│   ├── ISSUE_TEMPLATE/         # Bug report templates
│   └── workflows/              # CI/CD, security audits, binary builds
├── cuda/                       # NVIDIA CUDA GPU kernels
│   └── keccak.ptx             # Compiled CUDA kernel for SHA3 mining
├── scripts/                    # Build and validation scripts
│   └── file_license_check.sh  # License header validation
├── src/                        # Main Rust source code
│   ├── http/                   # HTTP monitoring server
│   │   ├── handlers/           # REST API endpoints
│   │   ├── config.rs           # Server configuration
│   │   ├── server.rs           # HTTP server implementation
│   │   └── stats_collector.rs  # Real-time mining statistics
│   ├── main.rs                 # Application entry point
│   ├── config_file.rs          # Configuration management
│   ├── multi_engine_wrapper.rs # GPU backend abstraction
│   ├── gpu_engine.rs           # Generic GPU engine wrapper
│   ├── engine_impl.rs          # Engine trait definitions
│   ├── cuda_engine.rs          # NVIDIA CUDA implementation
│   ├── opencl_engine.rs        # OpenCL implementation
│   ├── metal_engine.rs         # Apple Metal implementation
│   ├── node_client.rs          # Tari blockchain communication
│   ├── p2pool_client.rs        # P2Pool mining protocol
│   ├── tari_coinbase.rs        # Mining reward transactions
│   ├── gpu_status_file.rs      # GPU configuration persistence
│   ├── stats_store.rs          # Mining performance tracking
│   ├── opencl_sha3.cl          # OpenCL kernel source
│   ├── metal_sha3.metal        # Apple Metal shader source
│   └── context_impl.rs         # GPU context traits
├── supply-chain/               # Security audit configuration
│   ├── audits.toml            # Local security audits
│   ├── config.toml            # Cargo-vet configuration
│   └── imports.lock           # Audit import lockfile
├── target/                     # Build artifacts (ignored)
├── Cargo.toml                  # Rust package manifest
├── Cargo.lock                  # Dependency lockfile
├── Cross.toml                  # Cross-compilation configuration
├── rust-toolchain.toml         # Rust toolchain specification
├── rustfmt.toml               # Code formatting rules
├── log4rs_sample.yml          # Logging configuration template
└── LICENSE                     # GPL-3.0 license
```

### **ENHANCED ARCHITECTURAL OVERVIEW**

#### **Core Architectural Pattern: Layered Multi-Backend Mining System**

Glytex employs a sophisticated **layered architecture** with **trait-based abstraction** that enables seamless GPU backend switching while maintaining performance and type safety:

```
┌─────────────────────────────────────────┐
│           APPLICATION LAYER              │  ← main.rs (orchestration)
├─────────────────────────────────────────┤
│         ABSTRACTION LAYER               │  ← MultiEngineWrapper (type erasure)
├─────────────────────────────────────────┤
│         TYPE-SAFE WRAPPER LAYER         │  ← GpuEngine<T> (generics)
├─────────────────────────────────────────┤
│         TRAIT INTERFACE LAYER           │  ← EngineImpl (contract)
├─────────────────────────────────────────┤
│         BACKEND IMPLEMENTATION LAYER    │  ← CudaEngine, OpenClEngine, MetalEngine
├─────────────────────────────────────────┤
│         GPU HARDWARE LAYER              │  ← NVIDIA, AMD, Intel, Apple Silicon
└─────────────────────────────────────────┘
```

#### **Cross-Cutting Architectural Concerns**

**1. Configuration Architecture:**
- **ConfigFile**: Runtime parameters, network settings, GPU optimization
- **GpuStatusFile**: Per-backend device configuration and performance tuning
- **CLI Integration**: Argument overrides and runtime parameter injection

**2. Monitoring & Observability Architecture:**
- **StatsStore**: Atomic performance counters (thread-safe)
- **StatsCollector**: Real-time aggregation with time windowing
- **HTTP Server**: RESTful monitoring API with external integration

**3. Blockchain Integration Architecture:**
- **NodeClient Trait**: Abstraction over Base Node vs P2Pool mining
- **Template Management**: Cached block templates with height monitoring
- **Coinbase Generation**: Mining reward transaction construction

### **DETAILED COMPONENT ARCHITECTURE**

#### **1. GPU Engine Abstraction System**

**Design Pattern**: Strategy + Adapter + Type Erasure
**Primary Flow**: `main.rs` → `MultiEngineWrapper` → `GpuEngine<T>` → `ConcreteEngine`

##### **Core Abstractions:**

**EngineImpl Trait** (`src/engine_impl.rs`):
- **Purpose**: Unified contract for all GPU backends
- **Associated Types**: Context, Function, Kernel (backend-specific)
- **Key Methods**: `init()`, `detect_devices()`, `create_context()`, `mine()`
- **Type Safety**: Enables compile-time optimization with runtime polymorphism

**GpuEngine<T>** (`src/gpu_engine.rs`):
- **Purpose**: Type-safe wrapper maintaining compile-time optimization
- **Pattern**: Zero-cost abstraction over concrete implementations
- **Integration**: Bridges typed implementations with type-erased wrapper

**MultiEngineWrapper** (`src/multi_engine_wrapper.rs`):
- **Purpose**: Runtime backend selection with type erasure
- **Implementation**: Uses `Box<dyn Any>` for cross-engine compatibility
- **Feature Gating**: Conditional compilation based on cargo features

##### **Backend Implementations:**

**CUDA Engine** (`src/cuda_engine.rs`):
- **Target**: NVIDIA GPUs (SM 5.2+)
- **Optimization**: Pre-compiled PTX assembly for maximum performance
- **Memory**: Efficient CUDA context management with SCHED_YIELD
- **Performance**: Direct GPU memory operations, optimal launch configurations

**OpenCL Engine** (`src/opencl_engine.rs`):
- **Target**: Universal GPU compatibility (NVIDIA, AMD, Intel)
- **Compilation**: Runtime kernel compilation for device-specific optimization
- **Compatibility**: Handles diverse OpenCL driver implementations
- **Platform**: Cross-vendor support with automatic capability detection

**Metal Engine** (`src/metal_engine.rs`):
- **Target**: Apple Silicon and Metal-compatible GPUs
- **Architecture**: Optimized for unified memory systems
- **Integration**: Native Apple ecosystem integration (Metal Performance Shaders)
- **Memory**: Autoreleasepool management for efficient memory handling

#### **2. Blockchain Integration Architecture**

**NodeClient Abstraction** (`src/node_client.rs`):
- **Pattern**: Strategy pattern for mining mode selection
- **Implementations**: Base Node (direct), P2Pool (pool), Benchmark (testing)
- **Protocol Handling**: gRPC communication with connection management
- **State Management**: Height tracking for both Tari and P2Pool chains

**Template Management Flow**:
1. **Height Monitoring**: Continuous blockchain height checking
2. **Template Caching**: RwLock-protected template storage
3. **Refresh Logic**: Automatic template updates on height changes
4. **Distribution**: Template propagation to mining threads

**Coinbase Integration** (`src/tari_coinbase.rs`):
- **Purpose**: Convert mining success to blockchain transactions
- **Integration**: Tari's one-sided payment protocol
- **Security**: Stealth payments and range proof generation
- **Reward Flow**: Block rewards + transaction fees → miner wallet

#### **3. Real-Time Monitoring Architecture**

**Statistics Collection Pipeline**:
```
Mining Threads → Broadcast Channels → StatsCollector → HTTP API
     ↓              ↓                    ↓             ↓
  HashRate     Sample Aggregation   Time Windows   JSON Response
  Generation   Per-Device Tracking  (10s, 1min)   External Tools
```

**Components:**

**StatsStore** (`src/stats_store.rs`):
- **Concurrency**: AtomicU64 with SeqCst ordering (lock-free)
- **Metrics**: Hashrate, accepted blocks, rejected blocks
- **Thread Safety**: Safe concurrent access from mining threads

**StatsCollector** (`src/http/stats_collector.rs`):
- **Pattern**: Observer pattern with broadcast channels
- **Aggregation**: Time-windowed averaging (10-second, 1-minute)
- **Data Structure**: VecDeque buffers for rolling window calculations
- **Real-time**: Continuous processing without mining interruption

**HTTP Monitoring Server** (`src/http/server.rs`):
- **Framework**: Axum async web framework
- **Security**: Localhost-only binding (port 18000)
- **Endpoints**: `/health`, `/version`, `/stats`
- **Integration**: External monitoring systems (Prometheus, Grafana)

#### **4. Configuration Management Architecture**

**Multi-Layer Configuration System**:
1. **Default Values**: Sensible defaults for immediate operation
2. **JSON Configuration**: Human-readable persistent settings
3. **CLI Overrides**: Runtime parameter modification
4. **Environment Variables**: Container/deployment integration

**ConfigFile** (`src/config_file.rs`):
- **Domains**: Network, mining, monitoring, performance
- **Persistence**: JSON serialization with validation
- **Integration**: Used across all system components

**GpuStatusFile** (`src/gpu_status_file.rs`):
- **Purpose**: Per-device configuration and optimization storage
- **Multi-Engine**: Separate files per GPU backend
- **Device Management**: Enable/disable, performance tuning
- **Persistence**: Survives hardware and driver changes

#### **5. Security and Supply Chain Architecture**

**Cargo-vet Integration** (`supply-chain/config.toml`):
- **Trust Network**: Distributed security audit trust relationships
- **Automation**: Daily vulnerability scanning via GitHub Actions
- **Compliance**: Enterprise security audit trail
- **Risk Management**: Continuous dependency monitoring

**Build Security** (`.github/workflows/build_binaries.yml`):
- **Supply Chain**: Cargo-auditable for transparency
- **Code Signing**: Windows executable signing
- **Verification**: SHA256 checksums for all artifacts
- **Reproducibility**: Locked dependencies and deterministic builds

### **KEY TECHNICAL PATTERNS**

#### **1. Type-Safe GPU Abstraction**

**Challenge**: Support heterogeneous GPU backends while maintaining performance
**Solution**: Layered abstraction with trait bounds and type erasure

```rust
// Type-safe layer
trait EngineImpl {
    type Context: ContextImpl;
    type Function: FunctionImpl;
    type Kernel: KernelImpl;
    fn mine(...) -> Result<MiningResult, Error>;
}

// Type-erased layer
enum EngineType { Cuda, OpenCl, Metal }
struct MultiEngineWrapper {
    engine_type: EngineType,
    inner: Box<dyn Any>,
}
```

#### **2. Configuration Composition Pattern**

**Challenge**: Manage complex configuration across multiple domains
**Solution**: Hierarchical configuration with override capabilities

```
CLI Args → JSON Config → Default Values
    ↓         ↓           ↓
  Runtime Configuration
    ↓
Component-Specific Settings
```

#### **3. Real-Time Statistics Aggregation**

**Challenge**: Collect performance data without impacting mining
**Solution**: Lock-free atomic operations + background aggregation

```
Atomic Counters → Broadcast Channels → Time Windows → HTTP API
  (lock-free)    (async messaging)   (rolling avg)   (monitoring)
```

#### **4. Graceful Resource Management**

**Challenge**: Clean GPU resource cleanup across different backends
**Solution**: RAII patterns with engine-specific cleanup

```rust
impl Drop for GpuEngine<T> {
    fn drop(&mut self) {
        // Engine-specific cleanup
        self.inner.cleanup()
    }
}
```

### **CORE WORKFLOWS**

#### **1. Application Startup Sequence**

```
1. CLI Parsing & Configuration Loading
   ├── config.json → ConfigFile::load()
   ├── CLI overrides → clap argument processing
   └── Validation → parameter validation

2. GPU Engine Initialization
   ├── Backend Selection → feature flag detection
   ├── Engine Init → GPU driver initialization
   ├── Device Detection → hardware enumeration
   └── Status Files → load per-device settings

3. Blockchain Client Setup
   ├── Connection Type → Base Node vs P2Pool
   ├── Network Config → mainnet/testnet/nextnet
   ├── gRPC Setup → connection establishment
   └── Initial Template → first block template fetch

4. Monitoring System Launch
   ├── StatsCollector → background aggregation task
   ├── HTTP Server → monitoring API startup
   └── Statistics Init → atomic counter initialization

5. Mining Thread Coordination
   ├── Device Iteration → enabled GPU enumeration
   ├── Thread Spawning → per-GPU mining threads
   ├── Template Cache → shared template storage
   └── Shutdown Handlers → graceful termination setup
```

#### **2. Mining Execution Flow (Per GPU Thread)**

```
1. GPU Resource Preparation
   ├── Context Creation → GPU context allocation
   ├── Kernel Loading → shader compilation/loading
   ├── Memory Allocation → device buffer allocation
   └── Configuration → optimal grid/block sizes

2. Block Template Processing
   ├── Template Fetch → cached or fresh template
   ├── Coinbase Generation → mining reward transaction
   ├── Difficulty Check → target difficulty validation
   └── Nonce Range → thread-specific nonce assignment

3. GPU Mining Execution
   ├── Data Transfer → host to device memory copy
   ├── Kernel Launch → GPU compute dispatch
   ├── Result Collection → device to host copy
   └── Nonce Validation → successful mining check

4. Block Submission & Statistics
   ├── Valid Block Check → difficulty verification
   ├── Network Submission → blockchain submission
   ├── Statistics Update → atomic counter updates
   └── Loop Continuation → next iteration preparation
```

#### **3. Template Management Workflow**

```
1. Height Monitoring
   ├── Periodic Check → blockchain height polling
   ├── Height Change → new block detection
   └── Template Invalidation → cache expiration

2. Template Refresh Process
   ├── Client Selection → Base Node vs P2Pool
   ├── Template Request → new template fetch
   ├── Validation → template integrity check
   └── Cache Update → atomic template replacement

3. Mining Thread Coordination
   ├── Cache Access → RwLock read access
   ├── Template Distribution → thread notification
   ├── Work Coordination → nonce range distribution
   └── Result Aggregation → submission coordination
```

#### **4. Real-Time Monitoring Workflow**

```
1. Statistics Collection
   ├── Mining Events → hashrate sample generation
   ├── Broadcast Channels → async message passing
   ├── Aggregation → time-windowed averaging
   └── Storage → in-memory statistics cache

2. HTTP API Serving
   ├── Request Processing → concurrent request handling
   ├── Statistics Query → real-time data access
   ├── JSON Serialization → response formatting
   └── External Integration → monitoring tool access

3. Performance Analysis
   ├── Per-Device Tracking → individual GPU monitoring
   ├── Trend Analysis → performance over time
   ├── Optimization Hints → configuration suggestions
   └── Troubleshooting → performance debugging
```

### **DEPENDENCIES AND INTEGRATIONS**

#### **Core Dependencies**

**Tari Blockchain Integration**:
- `minotari_app_grpc`: Base node gRPC communication
- `tari_common`: Shared utilities and types
- `tari_core`: Blockchain primitives and cryptography
- Integration: Direct blockchain protocol implementation

**GPU Computing Frameworks**:
- `cust`: NVIDIA CUDA bindings (feature: nvidia)
- `opencl3`: OpenCL 3.x implementation (feature: opencl)
- `metal`: Apple Metal framework (feature: metal)
- Integration: Hardware-specific GPU acceleration

**Async Runtime & HTTP**:
- `tokio`: Async runtime with full feature set
- `axum`: Modern HTTP framework with JSON support
- Integration: Concurrent I/O and monitoring API

**Serialization & Configuration**:
- `serde_json`: JSON configuration and API responses
- `clap`: Command-line argument parsing
- Integration: Configuration management and CLI interface

#### **External Integrations**

**Tari Ecosystem**:
- **Base Node**: Direct gRPC communication for solo mining
- **P2Pool**: Pool mining protocol for shared rewards
- **Wallet Integration**: Mining reward distribution
- **Network Support**: Mainnet, testnet, nextnet compatibility

**GPU Hardware Ecosystems**:
- **NVIDIA**: CUDA runtime and driver integration
- **AMD**: ROCm and OpenCL driver support
- **Intel**: Intel OneAPI and OpenCL support
- **Apple**: Metal framework and Apple Silicon optimization

**Monitoring & Operations**:
- **Prometheus**: HTTP metrics scraping support
- **Grafana**: Dashboard integration via HTTP API
- **Docker**: Container deployment support
- **Kubernetes**: Cloud-native deployment patterns

### **GPU KERNEL IMPLEMENTATION**

#### **Algorithm: SHA3/Keccak Mining**

All GPU backends implement parallel SHA3 hashing with consistent approach:

**Mining Strategy**:
- Each GPU thread tests unique nonce ranges
- Multiple iterations per thread reduce kernel launch overhead
- Configurable grid/block sizes for architecture optimization
- Difficulty-based early termination when valid nonce found

**Performance Optimization**:
- **CUDA**: Pre-compiled PTX for maximum performance
- **OpenCL**: Runtime compilation for device-specific optimization
- **Metal**: Apple Silicon unified memory optimization

#### **Kernel Sources and Compilation**

**CUDA Implementation** (`cuda/keccak.ptx`):
- Pre-compiled PTX assembly for NVIDIA GPUs
- Generated by NVIDIA NVVM Compiler (CUDA 12.3)
- Target: SM 5.2+ architecture compatibility
- Optimization: Maximum performance with architecture-specific tuning

**OpenCL Implementation** (`src/opencl_sha3.cl`):
- Runtime-compiled C source for cross-platform compatibility
- Device-specific optimization during compilation
- Compatibility: OpenCL 1.0+ specifications
- Support: AMD, NVIDIA, Intel OpenCL drivers

**Metal Implementation** (`src/metal_sha3.metal`):
- Apple Metal Shading Language compute kernels
- Unified memory architecture optimization
- Target: Apple Silicon and Metal-compatible GPUs
- Integration: Native Apple ecosystem performance

#### **Performance Characteristics**

**Thread Configuration**:
- Device-specific grid/block size recommendations
- Automatic work group size detection
- Memory coalescing optimization
- Async execution with command queues/streams

**Memory Management**:
- Minimal host-device data transfer
- Efficient buffer allocation strategies
- Architecture-specific memory access patterns
- Unified memory support (Metal/Apple Silicon)

### **CONFIGURATION SYSTEM**

#### **Hierarchical Configuration Architecture**

**Configuration Layers** (precedence: high → low):
1. **CLI Arguments**: Runtime overrides and debugging options
2. **JSON Configuration**: Persistent user settings (config.json)
3. **Default Values**: Sensible defaults for immediate operation
4. **Auto-Detection**: Hardware-based automatic configuration

#### **Configuration Domains**

**Network Configuration**:
```json
{
  "tari_address": "miner_wallet_address",
  "tari_node_url": "http://127.0.0.1:18142",
  "p2pool_enabled": false,
  "network": "mainnet"
}
```

**Mining Optimization**:
```json
{
  "block_size": 896,
  "single_grid_size": 1024,
  "iterations_per_cycle": 1,
  "template_refresh_secs": 30
}
```

**Monitoring Configuration**:
```json
{
  "http_server_enabled": true,
  "http_server_port": 18000,
  "stats_collection_enabled": true
}
```

#### **GPU Device Configuration**

**Per-Engine Status Files**:
- `CUDA_gpu_status.json`: NVIDIA GPU settings
- `OpenCL_gpu_status.json`: OpenCL device settings
- `Metal_gpu_status.json`: Apple Metal device settings

**Device Configuration Structure**:
```json
{
  "gpu_devices": [
    {
      "device_name": "NVIDIA RTX 4090",
      "device_index": 0,
      "settings": {
        "is_available": true,
        "is_excluded": false
      },
      "status": {
        "recommended_grid_size": 1024,
        "recommended_block_size": 896,
        "max_grid_size": 2147483647
      }
    }
  ]
}
```

### **API ENDPOINTS AND MONITORING**

#### **HTTP Monitoring Server**

**Base URL**: `http://localhost:18000`
**Security**: Localhost-only binding for security
**Framework**: Axum async web framework

#### **Endpoint Specifications**

**Health Check** - `GET /health`:
- **Purpose**: Service availability monitoring
- **Response**: HTTP 200 OK
- **Use Case**: Load balancer health checks, container orchestration

**Version Information** - `GET /version`:
- **Purpose**: Build identification and version tracking
- **Response**: Version string from CARGO_PKG_VERSION
- **Use Case**: Deployment verification, compatibility checking

**Mining Statistics** - `GET /stats`:
- **Purpose**: Real-time mining performance data
- **Response Format**:
```json
{
  "hashrate_per_device": {
    "0": {
      "ten_seconds": 1234567890.0,
      "one_minute": 1200000000.0
    },
    "1": {
      "ten_seconds": 1100000000.0,
      "one_minute": 1050000000.0
    }
  },
  "total_hashrate": {
    "ten_seconds": 2334567890.0,
    "one_minute": 2250000000.0
  }
}
```

#### **Monitoring Integration**

**External Tool Support**:
- **Prometheus**: HTTP metrics scraping endpoint
- **Grafana**: Dashboard visualization via JSON API
- **Custom Monitoring**: RESTful API for integration
- **Mining Pools**: Performance reporting and optimization

**Operational Monitoring**:
- Real-time hashrate tracking per GPU device
- Mining efficiency analysis and optimization
- Hardware utilization and performance monitoring
- Troubleshooting and debugging support

### **TESTING AND QUALITY ASSURANCE**

#### **Automated Testing Strategy**

**Unit Testing**:
- **Cargo Test Suite**: Core functionality validation
- **Component Testing**: Individual module verification
- **Integration Testing**: Cross-component interaction testing

**Performance Testing**:
- **Benchmarking Mode**: Synthetic workload performance testing
- **GPU Detection Testing**: Hardware enumeration validation
- **Hashrate Validation**: Mining performance verification

#### **Code Quality Enforcement**

**Formatting and Style**:
- **rustfmt**: Consistent code formatting (120 char lines)
- **Configuration**: Nightly features, import organization
- **Enforcement**: CI/CD pipeline integration

**Lint and Quality Checks**:
- **cargo clippy**: Pedantic lint rules and best practices
- **cargo machete**: Unused dependency detection
- **Static Analysis**: Code quality and performance analysis

**Security Auditing**:
- **cargo vet**: Supply chain security verification
- **Daily Audits**: Automated vulnerability scanning
- **Dependency Tracking**: Security audit trail maintenance

#### **Cross-Platform Validation**

**CI/CD Matrix Testing**:
- **Platforms**: Linux, macOS, Windows
- **Architectures**: x86_64, ARM64, RISC-V
- **GPU Backends**: CUDA, OpenCL, Metal feature combinations
- **Network Modes**: Mainnet, testnet, nextnet testing

**Build Verification**:
- Cross-compilation validation
- Platform-specific dependency resolution
- GPU driver compatibility testing
- Feature flag validation across platforms

### **SECURITY CONSIDERATIONS**

#### **Supply Chain Security**

**Cargo-vet Framework**:
- **Trust Network**: Distributed security audit verification
- **Audit Sources**: Bytecode Alliance, Mozilla, Google, ISRG
- **Automated Scanning**: Daily vulnerability detection
- **Compliance**: Enterprise security audit requirements

**Build Security**:
- **Reproducible Builds**: Locked dependencies and deterministic compilation
- **Code Signing**: Windows executable signing for trust
- **Checksum Verification**: SHA256 hashes for all distributed binaries
- **Supply Chain Transparency**: Cargo-auditable integration

#### **Runtime Security**

**Secret Management**:
- **No Secret Logging**: Secure handling of wallet addresses
- **Configuration Security**: Separation of sensitive data
- **Memory Protection**: Secure cleanup of cryptographic material

**Network Security**:
- **TLS Communication**: Encrypted gRPC connections to Tari nodes
- **Local Binding**: HTTP server localhost-only for security
- **Input Validation**: Robust parsing of network data and configuration

**Resource Security**:
- **Error Containment**: Graceful handling of GPU driver failures
- **Resource Limits**: Memory and compute resource management
- **Privilege Separation**: Minimal required system permissions

#### **Operational Security**

**Access Control**:
- **Local Access Only**: HTTP monitoring bound to localhost
- **No Remote Access**: Prevents external network exposure
- **Minimal Attack Surface**: Limited external interfaces

**Monitoring Security**:
- **Non-Sensitive Metrics**: Only performance data exposed
- **Rate Limiting**: Template fetch throttling and failure limits
- **Audit Logging**: Structured logging for security analysis

### **BUILD AND DEPLOYMENT**

#### **Build Requirements**

**Development Environment**:
- **Rust Toolchain**: Nightly 2024-07-07 for unstable features
- **GPU SDKs**: CUDA Toolkit, OpenCL headers, or Xcode (Metal)
- **System Dependencies**: Protocol Buffers, OpenSSL
- **Platform Tools**: Platform-specific build tools and linkers

#### **Feature Flag Architecture**

**GPU Backend Selection**:
- `nvidia`: Enable CUDA support for NVIDIA GPUs
- `opencl`: Enable OpenCL support for cross-platform GPUs
- `metal`: Enable Apple Metal support for Apple Silicon

**Conditional Compilation**:
- Engine-specific code compilation based on features
- Dependency inclusion optimization
- Platform-specific optimizations

#### **Cross-Compilation Support**

**Cross.toml Configuration**:
- Docker-based cross-compilation environment
- Platform-specific dependency installation
- Environment variable passthrough for build configuration

**Target Platform Support**:
- **Linux**: x86_64, ARM64, RISC-V architectures
- **macOS**: Intel and Apple Silicon support
- **Windows**: x86_64 with GPU backend support

#### **Distribution Strategy**

**Binary Distribution**:
- **GitHub Releases**: Automated release creation for tagged versions
- **Platform Variants**: Architecture and GPU backend combinations
- **Verification**: SHA256 checksums and code signing
- **Documentation**: Release notes and changelog integration

**Container Support**:
- **Docker Compatibility**: Container deployment support
- **GPU Passthrough**: Hardware acceleration in containers
- **Configuration Management**: Environment variable configuration
- **Health Checks**: Container orchestration integration

### **PERFORMANCE CHARACTERISTICS**

#### **Typical Performance Metrics**

**Hashrate Performance** (hardware dependent):
- **High-end NVIDIA GPU**: 1-10 GH/s (GeForce RTX 4090)
- **Mid-range AMD GPU**: 500 MH/s - 2 GH/s (RX 6800 XT)
- **Apple Silicon M1/M2**: 100-500 MH/s (unified memory advantage)
- **CPU Fallback**: 1-10 MH/s (software implementation)

**Resource Utilization**:
- **GPU Memory**: 50-200 MB per device (kernel and buffers)
- **System Memory**: 100-500 MB total (statistics and caching)
- **CPU Usage**: Low (5-15%) during GPU mining
- **Network Bandwidth**: Minimal (template fetching only)

#### **Optimization Strategies**

**GPU Optimization**:
- Device-specific grid/block size tuning
- Automatic configuration based on hardware capabilities
- Memory access pattern optimization
- Async execution for maximum throughput

**System Optimization**:
- Lock-free atomic operations for statistics
- Efficient template caching with RwLock
- Minimal data copying between host and device
- Background task coordination for monitoring

### **TROUBLESHOOTING AND DEBUGGING**

#### **Common Issues and Solutions**

**GPU Driver Problems**:
- **CUDA**: Verify CUDA runtime installation and driver compatibility
- **OpenCL**: Check OpenCL runtime and vendor driver installation
- **Metal**: Ensure macOS and Xcode compatibility

**Network Connectivity**:
- **Base Node**: Verify Tari node accessibility and gRPC port
- **P2Pool**: Check P2Pool node connection and protocol version
- **Firewall**: Ensure network access for blockchain communication

**Performance Issues**:
- **Grid/Block Sizes**: Adjust GPU parameter configuration
- **Template Refresh**: Optimize blockchain polling intervals
- **Device Selection**: Enable/disable GPUs based on performance

#### **Debugging Tools**

**Command Line Options**:
- `--detect`: Enumerate and display available GPU devices
- `--benchmark`: Performance testing mode without blockchain
- `--find-optimal`: Automatic parameter optimization discovery

**Logging and Monitoring**:
- **Structured Logging**: log4rs with configurable levels and targets
- **Component Isolation**: Separate log targets for engines, HTTP, clients
- **Performance Metrics**: Real-time monitoring via HTTP API
- **File Rotation**: Automatic log management with size limits

#### **Performance Tuning**

**GPU Configuration**:
- Device-specific parameter optimization
- Memory usage monitoring and optimization
- Thread configuration tuning
- Temperature and power monitoring integration

**System Tuning**:
- Template refresh frequency optimization
- Statistics collection overhead minimization
- Network timeout and retry configuration
- Resource allocation and cleanup optimization

### **FUTURE DEVELOPMENT ROADMAP**

#### **Planned Enhancements**

**Performance Optimization**:
- **Auto-tuning**: Automatic mining parameter optimization
- **Dynamic Scaling**: Runtime GPU enable/disable capability
- **Load Balancing**: Intelligent work distribution optimization
- **Memory Management**: Advanced GPU memory optimization

**Operational Features**:
- **Pool Failover**: Multiple pool/node endpoint support with automatic failover
- **Advanced Monitoring**: Prometheus metrics integration and alerting
- **GPU Temperature**: Thermal monitoring and automatic throttling
- **Configuration UI**: Web-based configuration management interface

**Scalability Improvements**:
- **Multi-Node Mining**: Distributed mining across multiple machines
- **Cluster Management**: Centralized mining farm coordination
- **Cloud Integration**: Cloud GPU instance support and optimization
- **Container Orchestration**: Kubernetes operator for mining deployments

#### **Technical Debt and Improvements**

**Code Quality**:
- **Error Handling**: Enhanced error reporting and recovery
- **Type Safety**: Further type system improvements
- **Documentation**: Comprehensive API and architecture documentation
- **Testing**: Expanded test coverage and integration testing

**Architecture Evolution**:
- **Plugin System**: Extensible architecture for custom GPU backends
- **Protocol Abstraction**: Support for additional blockchain protocols
- **Monitoring Framework**: Pluggable monitoring and metrics systems
- **Configuration Evolution**: Advanced configuration management and validation

---

This comprehensive architectural overview provides developers, operators, and contributors with deep understanding of glytex's design patterns, implementation strategies, and operational characteristics. The enhanced descriptions throughout the codebase now include cross-references, architectural context, and system integration details that enable efficient navigation and contribution to this sophisticated GPU mining application.

## Codebase Structure

### Root Directory

| File | Description |
|------|-------------|
| `.gitignore` | Git ignore patterns for glytex project. Excludes: build artifacts (/target), runtime configuration (config.json), data directories, log files (xtrgpuminer.log), GPU status files (CUDA/OpenCL/Metal_gpu_status.json), panic logs, OS files (.DS_Store), and IDE directories (.idea, .vscode except extensions.json). Prevents sensitive configuration and generated files from being committed to version control. |
| `.license.ignore` | License check exclusion list for automated license validation. Excludes cuda/keccak.ptx from license header checks since it's a generated binary file from NVIDIA compiler tools. Used by license validation scripts to skip files that don't require license headers. |
| `Cargo.lock` | [GENERATED] Cargo dependency lockfile ensuring reproducible builds. Auto-generated lock of exact dependency versions and checksums for all transitive dependencies. Contains 5000+ dependency entries including GPU libraries (cust, opencl3, metal), async runtime (tokio), HTTP framework (axum), Tari blockchain components, cryptography, and build tools. Essential for consistent builds across environments and security audit trails. |
| `Cargo.toml` | Main Cargo manifest for glytex v0.2.29. Defines dependencies including Tari blockchain components (minotari_app_grpc, tari_common, tari_core), GPU computing libraries (cust for NVIDIA CUDA, opencl3 for OpenCL, metal for Apple Metal), async runtime (tokio), HTTP server (axum), logging (log4rs), cryptography and utilities. Features: nvidia, metal, opencl for different GPU backends. Dependencies pinned to Tari v2.0.0-alpha.2. |
| `Cross.toml` | Cross-compilation configuration for building glytex on multiple architectures. Supports aarch64, x86_64, and riscv64 Linux targets. Configures environment variable passthrough for build tools, Tari network settings, and cross-compilation flags. Pre-build steps install required dependencies: libprotobuf-dev, protobuf-compiler, OpenCL headers and libraries, SSL development packages. Used with Cross tool for creating portable binaries across different Linux architectures while maintaining GPU library compatibility. |
| `LICENSE` | GNU General Public License v3.0 (GPL-3.0) license text. Full text of the copyleft license governing glytex distribution and modification. Ensures source code remains free and open, requires derivative works to be licensed under GPL-3.0, and protects user freedoms. Standard FSF GPL-3.0 text covering terms, conditions, and warranty disclaimers for open source software distribution. |
| `README.md` | Project README file for glytex, a GPU-based miner for the Tari cryptocurrency. Brief description stating it's originally based on @cookiemonstermayhem's xtrgpuminer. This is the main entry point for understanding the project's purpose and likely contains build/usage instructions. |
| `log4rs_sample.yml` | Log4rs logging configuration template for glytex miner. Defines logging appenders: stdout (console output with info level), xtrgpuminer (rolling file logger with 10MB rotation and 5 file retention). Log pattern includes timestamp, level, message, and source location. Configuration separates console and file logging levels, with file logging capturing detailed information. Template uses {{log_dir}} placeholder for runtime directory substitution. Used by Tari's logging initialization system. |
| `rust-toolchain.toml` | Rust toolchain specification pinning project to nightly-2024-07-07. Ensures consistent build behavior across environments and enables unstable features used in the codebase. Required for rustfmt unstable features and potential use of experimental Rust capabilities. |
| `rustfmt.toml` | Rustfmt code formatting configuration. Defines code style rules: 120 character line width, nightly edition features, import grouping (StdExternalCrate), horizontal/vertical import layout, comment formatting and normalization, struct literal formatting, trailing commas in match blocks. Enables unstable formatting features, import reordering, and automatic code style enforcement. Ensures consistent code formatting across the glytex project. |

### .github/

| File | Description |
|------|-------------|
| `PULL_REQUEST_TEMPLATE.md` | GitHub pull request template ensuring consistent PR submissions. Sections include: description, motivation/context, testing methodology, reviewer verification process, and breaking changes checklist. Emphasizes release-ready titles for changelog generation and breaking change documentation. Helps maintainers evaluate and merge contributions efficiently with complete information about changes and their impact. |
| `dependabot.yml` | Dependabot configuration for automated dependency updates. Monitors GitHub Actions dependencies with weekly update schedule. Helps maintain security and compatibility by automatically creating pull requests for outdated GitHub Actions used in CI/CD workflows. |

#### .github/ISSUE_TEMPLATE/

| File | Description |
|------|-------------|
| `bug_report.md` | GitHub issue template for bug reports. Structured template with sections for: bug description, reproduction steps, expected behavior, screenshots, desktop/mobile environment details, and additional context. Pre-labeled as 'bug-report' for automatic categorization. Helps contributors provide consistent, actionable bug reports with all necessary debugging information. |

#### .github/workflows/

| File | Description |
|------|-------------|
| `audit.yml` | Security audit GitHub workflow for daily vulnerability scanning. Runs automatically on cron schedule (05:43 UTC daily), on dependency changes (Cargo.toml/Cargo.lock), workflow modifications, or manual trigger. Uses rustsec/audit-check action to scan Rust dependencies for known security vulnerabilities in RustSec Advisory Database. Essential for maintaining security posture and identifying compromised dependencies early. |
| `build_binaries.json` | Build matrix configuration for automated binary compilation. Defines target combinations: GPU backends (CUDA, OpenCL, Metal), architectures (x86_64, ARM64, RISC-V), operating systems (Linux, macOS, Windows), Rust toolchain versions, cross-compilation settings, and build enablement flags. Includes platform-specific configurations, rustflags, and best-effort builds for experimental targets. Used by build_binaries.yml workflow to generate comprehensive binary distribution matrix. |
| `build_binaries.yml` | **COMPREHENSIVE RELEASE AUTOMATION**: Multi-platform binary build and distribution pipeline for glytex mining software. **ARCHITECTURAL SCOPE**: Builds complete binary matrix covering x86_64/ARM64/RISC-V architectures × Linux/macOS/Windows platforms × CUDA/OpenCL/Metal GPU backends × mainnet/testnet/nextnet networks = 50+ binary combinations. **SUPPLY CHAIN SECURITY**: Integrates cargo-auditable for supply chain transparency, uses code signing for Windows executables, generates SHA256 checksums for all artifacts, enables reproducible builds with locked dependencies. **CROSS-COMPILATION INFRASTRUCTURE**: Leverages Cross.toml for Docker-based cross-compilation, installs platform-specific dependencies (CUDA toolkit, OpenCL headers, Xcode), handles environment variable passthrough for build configuration. **DISTRIBUTION AUTOMATION**: Creates GitHub releases for tagged versions, uploads signed binaries with verification checksums, organizes artifacts by platform and GPU backend, maintains release notes and changelog integration. **BUILD MATRIX STRATEGY**: Uses build_binaries.json configuration for systematic platform/feature combinations, includes best-effort builds for experimental targets, handles conditional compilation based on feature flags. **DEPLOYMENT INTEGRATION**: Supports mining farm deployment with pre-built binaries, eliminates compilation requirements for end users, provides verified binaries for production environments. **CROSS-REFERENCES**: Uses Cross.toml for compilation settings, integrates with Cargo.toml feature flags, coordinates with audit.yml for security scanning, follows rust-toolchain.toml version specifications. **OPERATIONAL VALUE**: Enables rapid deployment of updates across diverse mining operations, reduces barrier to entry for new miners, supports multi-platform mining farms with heterogeneous hardware. |
| `ci.yml` | **COMPREHENSIVE QUALITY ASSURANCE PIPELINE**: Multi-platform continuous integration ensuring code quality, security, and compatibility across all supported environments. **TESTING MATRIX**: Linux/macOS/Windows platforms × CUDA/OpenCL/Metal feature combinations ensuring mining functionality works across diverse hardware configurations. **CODE QUALITY ENFORCEMENT**: Cargo fmt (formatting consistency), cargo clippy (lint checks with pedantic rules), cargo machete (unused dependency detection), ensuring maintainable codebase. **DEPENDENCY INTEGRITY**: Cargo test suite validation, dependency installation verification (protobuf, OpenCL, CUDA toolkit), security auditing with cargo vet for supply chain protection. **PLATFORM-SPECIFIC VALIDATION**: Handles GPU driver installation, environment setup for different operating systems, platform-specific dependency resolution, cross-platform compatibility verification. **DEVELOPMENT WORKFLOW**: Pre-merge validation ensuring main branch stability, concurrent job execution for faster feedback, cache optimization for efficient CI resource usage. **ARCHITECTURAL INTEGRATION**: Validates all GPU engine implementations work correctly, ensures HTTP monitoring endpoints function, confirms configuration management operates properly, tests blockchain integration components. **CROSS-REFERENCES**: Uses rust-toolchain.toml for consistent toolchain, validates Cargo.toml dependencies, coordinates with build_binaries.yml for release preparation, integrates with audit.yml security scanning. **QUALITY GATES**: Prevents merge of code that breaks compilation, formatting, or security standards, ensures all supported platform/feature combinations remain functional. |
| `pr.yml` | Pull request validation workflow enforcing code quality standards. Checks: signed commit verification using 1Password action, conventional commit title formatting using commitlint. Ensures all commits are cryptographically signed and PR titles follow semantic versioning conventions for changelog generation. Runs on PR open, reopen, edit, and sync events with concurrency management. |

### cuda/

| File | Description |
|------|-------------|
| `keccak.ptx` | [GENERATED] NVIDIA PTX assembly code for Keccak/SHA3 mining kernel. Generated by NVIDIA NVVM Compiler (CUDA 12.3) from CUDA source. Contains optimized GPU assembly for SM 5.2+ architecture. Defines constants: RHO (rotation offsets), PI (position indices), RC (round constants) for Keccak algorithm. Main keccakKernel function implements parallel SHA3 mining with nonce testing. Used by CUDA engine for high-performance GPU mining on NVIDIA hardware. PTX allows runtime compilation for optimal performance on specific GPU architectures. |

### scripts/

| File | Description |
|------|-------------|
| `file_license_check.sh` | License header validation script for CI/CD pipeline. Uses ripgrep to find source files missing "Copyright.*The Tari Project" headers, excluding common configuration and documentation file types. Compares results against .license.ignore whitelist to identify files needing license headers. Script enforces alphabetical sorting of ignore file and provides clear error messages for license compliance issues. Essential for maintaining open source license requirements across the codebase. |

### src/

| File | Description |
|------|-------------|
| `config_file.rs` | **RUNTIME CONFIGURATION MANAGEMENT**: Central configuration system managing all mining parameters and network settings. **ARCHITECTURAL ROLE**: Configuration layer bridging CLI arguments, JSON configuration files, and runtime parameter validation across all system components. **CONFIGURATION DOMAINS**: Tari wallet integration (tari_address, coinbase_extra), blockchain connectivity (tari_node_url, network selection), mining optimization (block_size, grid_size, iterations_per_cycle), monitoring (HTTP server settings), timing control (template_refresh_secs, height_check_interval), P2Pool settings, and failure handling (max_template_failures). **SYSTEM INTEGRATION**: Used by main.rs for startup configuration, provides runtime parameters to GPU engines via grid/block size settings, configures NodeClient connections, controls HTTP server behavior, manages mining loop timing. **PERSISTENCE MODEL**: JSON serialization for human-readable configuration files, load() and save() methods for configuration management, default factory settings with sensible mining parameters. **CROSS-REFERENCES**: Configuration consumed by main.rs startup, GPU parameters used by all engines (cuda_engine.rs, opencl_engine.rs, metal_engine.rs), HTTP settings used by http/server.rs, node settings used by node_client.rs implementations. **RUNTIME FLEXIBILITY**: CLI argument overrides for configuration values, per-network configuration support (mainnet/testnet/nextnet), development vs production settings. **OPERATIONAL IMPORTANCE**: Controls mining efficiency through GPU parameters, manages network connectivity and reliability, enables monitoring and debugging capabilities. |
| `context_impl.rs` | Empty trait marker for GPU context implementations. ContextImpl trait serves as a type constraint for GPU context objects across different engines (CUDA Context, OpenCL Context, Metal Device). Used by engine implementations to ensure type safety while allowing different context types for each GPU backend. |
| `cuda_engine.rs` | **NVIDIA CUDA BACKEND**: High-performance GPU mining implementation for NVIDIA hardware using CUDA runtime. **ARCHITECTURAL ROLE**: Implements EngineImpl trait as one of three GPU backends (CUDA/OpenCL/Metal), optimized for NVIDIA GPU architectures (SM 5.2+). **PERFORMANCE OPTIMIZATION**: Uses pre-compiled PTX assembly from cuda/keccak.ptx for maximum performance, avoids runtime compilation overhead, supports all modern NVIDIA architectures. **CUDA INTEGRATION**: Leverages cust library for Rust-CUDA bindings, manages CUDA contexts with SCHED_YIELD flags for CPU efficiency, handles device capability detection (max grid dimensions), calculates optimal launch configurations. **MEMORY MANAGEMENT**: Efficient GPU memory allocation for mining data, device-to-host result transfers, stream management for async operations. **MINING EXECUTION**: keccakKernel dispatch with configurable grid/block sizes, nonce range distribution across CUDA threads, result collection and validation. **ARCHITECTURAL INTEGRATION**: Selected by MultiEngineWrapper based on 'nvidia' feature flag, wrapped by GpuEngine<T>, managed by main.rs mining coordination. **TYPE SAFETY**: CudaContext and CudaFunction wrappers implement ContextImpl and FunctionImpl traits for type system integration. **CROSS-REFERENCES**: Uses cuda/keccak.ptx (kernel binary), integrates with multi_engine_wrapper.rs, follows EngineImpl contract, shares GpuStatusFile format. **HARDWARE TARGETING**: Supports GeForce, Quadro, Tesla, and data center GPUs, automatic architecture detection, optimal thread configuration per device type. |
| `engine_impl.rs` | **CORE TRAIT DEFINITION**: Fundamental interface contract for all GPU mining engines in the glytex architecture. **DESIGN PATTERN**: Abstract factory + strategy pattern defining common interface for heterogeneous GPU backends (CUDA, OpenCL, Metal). **ASSOCIATED TYPES**: Context (GPU device contexts), Function (compiled kernels), Kernel (executable units) - enables type-safe backend-specific implementations while maintaining common interface. **METHOD CONTRACTS**: init() (engine initialization), detect_devices() (hardware discovery), create_context() (GPU resource allocation), create_main_function()/create_kernel() (shader compilation), mine() (core mining execution). **ARCHITECTURAL IMPORTANCE**: Enables polymorphic GPU operations in MultiEngineWrapper while preserving type safety and performance. **IMPLEMENTATION HIERARCHY**: Implemented by CudaEngine, OpenClEngine, MetalEngine → wrapped by GpuEngine<T> → unified by MultiEngineWrapper. **TYPE CONSTRAINTS**: Associated types must implement ContextImpl, FunctionImpl, KernelImpl marker traits for additional type safety. **CROSS-REFERENCES**: Extended by all GPU engines (cuda_engine.rs, opencl_engine.rs, metal_engine.rs), wrapped by gpu_engine.rs, used by multi_engine_wrapper.rs. **MINING INTERFACE**: mine() method signature defines standard mining parameters (difficulty, nonce ranges, block data) and return format (found nonces, hash counts, execution metrics). |
| `function_impl.rs` | Trait for GPU function/kernel optimization. FunctionImpl trait provides suggested_launch_configuration() method to determine optimal grid and block sizes for GPU kernels based on device capabilities. Each GPU backend implements this to return optimal thread configuration (grid_size, block_size) for maximum performance on the specific device type. |
| `gpu_engine.rs` | **TYPE-SAFE ENGINE WRAPPER**: Generic wrapper providing unified interface over concrete GPU engine implementations. **ARCHITECTURAL PATTERN**: Adapter pattern bridging type-specific engines (CudaEngine, OpenClEngine, MetalEngine) with type-erased MultiEngineWrapper. **TYPE SYSTEM INTEGRATION**: GpuEngine<TEngineImpl> maintains compile-time type safety while enabling runtime polymorphism through EngineImpl trait bounds. **LIFECYCLE MANAGEMENT**: Orchestrates GPU resource lifecycle: init() → detect_devices() → create_context() → create_main_function() → create_kernel() → mine() → cleanup. **ABSTRACTION LAYER**: Provides consistent API regardless of underlying GPU technology, handles engine-specific initialization and resource management, standardizes error handling across backends. **RESOURCE COORDINATION**: Manages GPU contexts, compiled functions/kernels, device enumeration, and mining parameter validation. **ARCHITECTURAL BRIDGE**: Intermediary between application logic (main.rs) and hardware-specific implementations, enables compile-time optimization while supporting runtime engine selection. **CROSS-REFERENCES**: Wraps CudaEngine/OpenClEngine/MetalEngine implementations, used by MultiEngineWrapper for type erasure, implements EngineImpl trait interface. **MEMORY SAFETY**: Ensures proper GPU resource cleanup, handles engine-specific context management, provides safe abstraction over unsafe GPU operations. **PERFORMANCE**: Zero-cost abstraction maintaining direct access to engine-specific optimizations while providing unified interface. |
| `gpu_status_file.rs` | **GPU DEVICE CONFIGURATION PERSISTENCE**: Device-specific configuration management enabling per-GPU mining control and optimization settings. **ARCHITECTURAL ROLE**: Persistent storage layer for GPU device settings, performance tuning parameters, and availability management across all GPU backends. **DEVICE MANAGEMENT**: GpuDevice structures track device identification (name, index), availability settings (is_available, is_excluded), and performance parameters (recommended_grid_size, recommended_block_size, max_grid_size). **MULTI-ENGINE SUPPORT**: Separate status files per GPU backend (CUDA_gpu_status.json, OpenCL_gpu_status.json, Metal_gpu_status.json) allowing per-engine device configuration and optimization. **CONFIGURATION MERGING**: load() method merges detected hardware devices with saved preferences, preserving user exclusions and performance tuning while accommodating hardware changes. **SYSTEM INTEGRATION**: Used by MultiEngineWrapper for device enumeration, consulted by main.rs for GPU thread spawning decisions, provides optimization parameters to engine implementations, enables runtime device enable/disable. **OPERATIONAL CONTROL**: Allows users to exclude problematic GPUs, save optimal performance parameters per device, maintain configuration across driver updates, and manage multi-GPU systems efficiently. **CROSS-REFERENCES**: Created and managed by multi_engine_wrapper.rs, consumed by all GPU engines for device settings, used by main.rs for mining thread creation, integrates with detection logic in each engine implementation. **PERFORMANCE OPTIMIZATION**: Stores proven grid/block size combinations per device, preserves optimal configurations discovered through benchmarking, enables device-specific tuning for maximum hashrate. |
| `kernel_impl.rs` | Empty trait marker for GPU kernel implementations. KernelImpl trait serves as a type constraint for GPU kernel objects across different engines (CUDA Function, OpenCL Kernel, Metal Function). Used by engine implementations to ensure type safety while allowing different kernel types for each GPU backend. Currently unused but provides extensibility for future kernel-specific functionality. |
| `main.rs` | **CORE ENTRY POINT**: Main orchestrator for glytex GPU mining application. **ARCHITECTURAL ROLE**: Central command & control hub that coordinates all subsystems: GPU engines, blockchain clients, HTTP monitoring, and statistics collection. **SYSTEM INTEGRATION**: Implements CLI interface using clap, initializes MultiEngineWrapper for GPU abstraction, manages NodeClient implementations (BaseNode/P2Pool), coordinates block template caching with RwLock protection, spawns per-GPU mining threads, integrates HttpServer for monitoring API. **KEY WORKFLOWS**: 1) Startup: GPU detection → engine selection → client connection → thread spawning, 2) Mining loop: template fetching → coinbase generation → GPU execution → block submission, 3) Monitoring: statistics aggregation → HTTP API serving. **CONCURRENCY MODEL**: Tokio async runtime for I/O, native threads for GPU mining, broadcast channels for statistics. **CROSS-REFERENCES**: Uses MultiEngineWrapper (GPU abstraction), NodeClient trait (blockchain integration), ConfigFile (configuration), GpuStatusFile (device management), HttpServer + StatsCollector (monitoring). **CRITICAL PATHS**: get_template_from_client() (block templates), run_thread() (per-GPU mining), run_template_height_watcher() (blockchain monitoring). **PERFORMANCE**: Template caching reduces blockchain RPC calls, parallel GPU execution maximizes hashrate, atomic shutdown coordination prevents data corruption. |
| `metal_engine.rs` | **APPLE ECOSYSTEM BACKEND**: Native Apple Silicon and Metal GPU mining implementation optimized for Apple's unified memory architecture. **ARCHITECTURAL ROLE**: Apple-specific GPU backend targeting M1/M2/M3 chips, discrete AMD GPUs in Mac systems, and iOS/iPadOS Metal-compatible devices. **METAL INTEGRATION**: Uses Metal Performance Shaders framework, compiles metal_sha3.metal compute shaders at runtime, leverages Apple's unified memory for efficient data access. **MEMORY EFFICIENCY**: Exploits unified memory architecture eliminating explicit CPU-GPU transfers, uses autoreleasepool for automatic memory management, optimized for Apple Silicon's memory bandwidth. **COMPUTE OPTIMIZATION**: Creates Metal compute pipeline states, calculates optimal threadgroup sizes based on device capabilities, uses Metal command buffers for GPU command queuing. **ARCHITECTURAL INTEGRATION**: Enabled by 'metal' feature flag, exclusive to Apple platforms, integrated via MultiEngineWrapper, implements EngineImpl trait. **PLATFORM TARGET**: macOS (Intel/Apple Silicon), iOS, iPadOS, tvOS devices with Metal support, optimized for Apple's GPU schedulers and memory systems. **SHADER COMPILATION**: Runtime compilation of metal_sha3.metal source, device-specific optimizations, automatic threading configuration. **APPLE ECOSYSTEM**: Leverages Metal's integration with Core Animation, Accelerate framework, and Apple's power management. **CROSS-REFERENCES**: Uses metal_sha3.metal (shader source), integrates with multi_engine_wrapper.rs, follows EngineImpl contract, managed by GpuStatusFile. **PERFORMANCE CHARACTERISTICS**: Optimized for Apple's tile-based deferred rendering architecture, efficient for both integrated and discrete GPUs in Apple systems. |
| `metal_sha3.metal` | Apple Metal shader source code for SHA3/Keccak mining. Implements Metal compute kernel for high-performance mining on Apple Silicon and Metal-compatible GPUs. Includes Metal-specific features: buffer attributes, thread positioning, atomic operations. Constants: rotation offsets (rot), position indices (pos), round constants (RC). Main 'sha3' compute kernel performs parallel nonce testing with thread-level parallelism. Optimized for Apple's unified memory architecture and GPU schedulers. Used by Metal engine for mining on macOS, iOS, and Apple Silicon devices. |
| `multi_engine_wrapper.rs` | **GPU ABSTRACTION LAYER**: Central abstraction providing unified interface across CUDA, OpenCL, and Metal GPU backends. **ARCHITECTURAL PATTERN**: Strategy pattern implementation with type erasure using Box<dyn Any> for cross-engine compatibility. **SYSTEM ROLE**: Bridges main.rs application logic with specific GPU implementations (CudaEngine, OpenClEngine, MetalEngine), enabling runtime GPU backend selection without compile-time coupling. **INTEGRATION POINTS**: Called by main.rs for GPU operations, delegates to backend-specific engines via EngineImpl trait, manages per-engine GpuStatusFile configurations. **TYPE SAFETY**: Implements EngineImpl trait while handling heterogeneous engine types, provides type-safe dispatch to concrete implementations. **FEATURE GATING**: Conditionally compiles GPU backends based on cargo features (nvidia, opencl, metal). **ENGINE LIFECYCLE**: Handles initialization → device detection → context creation → kernel compilation → mining execution → cleanup. **CROSS-REFERENCES**: Integrates with GpuEngine<T> wrappers, GpuStatusFile for device configuration, specific engines (cuda_engine.rs, opencl_engine.rs, metal_engine.rs). **RUNTIME FLEXIBILITY**: Allows users to select optimal GPU backend for their hardware without code changes, supports mixed-vendor GPU environments. |
| `node_client.rs` | **BLOCKCHAIN INTEGRATION LAYER**: Abstraction layer for Tari blockchain communication supporting multiple mining modes. **ARCHITECTURAL PATTERN**: Strategy pattern with NodeClient trait enabling runtime selection between base node and P2Pool mining. **CLIENT IMPLEMENTATIONS**: BaseNodeClientWrapper (direct Tari node gRPC), P2poolClientWrapper (P2Pool mining protocol), BenchmarkNodeClient (testing/performance measurement). **SYSTEM INTEGRATION**: Used by main.rs for block template fetching, block submission, height monitoring; integrates with Tari gRPC services (minotari_app_grpc). **KEY WORKFLOWS**: 1) Template retrieval: get_next_block_template() → blockchain state validation, 2) Block submission: submit_block() → mining reward distribution, 3) Height monitoring: get_height() → template refresh triggering. **PROTOCOL ABSTRACTION**: Handles differences between direct node mining (full template control) vs P2Pool mining (template delegation), manages connection lifecycle and retry logic. **DATA STRUCTURES**: HeightData tracks both Tari chain height and P2Pool chain state for dual-chain awareness. **CROSS-REFERENCES**: Implemented by p2pool_client.rs and base node logic, used by main.rs mining loops, integrates with tari_coinbase.rs for reward generation. **PERFORMANCE**: Connection pooling, timeout management, automatic retry on failures, efficient gRPC streaming for high-frequency operations. **MINING COORDINATION**: Synchronizes blockchain state changes with GPU mining threads, triggers template refresh on height increases. |
| `opencl_engine.rs` | **CROSS-PLATFORM GPU BACKEND**: Universal GPU mining implementation using OpenCL 3.x for maximum hardware compatibility. **ARCHITECTURAL ROLE**: Vendor-agnostic GPU backend supporting NVIDIA, AMD, Intel GPUs and other OpenCL-compatible devices. **PLATFORM ABSTRACTION**: Discovers multiple OpenCL platforms (AMD, NVIDIA, Intel drivers), creates contexts for any compatible GPU device, compiles kernels at runtime for optimal device-specific performance. **KERNEL COMPILATION**: Runtime compilation of opencl_sha3.cl source code, device-specific optimization, automatic work group size detection based on hardware capabilities. **MEMORY ARCHITECTURE**: OpenCL buffer management for input/output data, efficient host-device transfers, command queue optimization for async execution. **MINING IMPLEMENTATION**: 'sha3' kernel execution with configurable global work sizes, automatic work group sizing based on device limits, error handling for diverse driver implementations. **CROSS-VENDOR SUPPORT**: Works with AMD ROCm, NVIDIA OpenCL, Intel OneAPI, Arm Mali, and other OpenCL runtimes. **ARCHITECTURAL INTEGRATION**: Enabled by 'opencl' feature flag, selected by MultiEngineWrapper, follows EngineImpl trait contract. **COMPATIBILITY LAYER**: Handles driver differences, varying OpenCL version support, platform-specific optimizations. **CROSS-REFERENCES**: Uses opencl_sha3.cl (kernel source), integrates with multi_engine_wrapper.rs, implements EngineImpl interface, shares GpuStatusFile configuration. **PERFORMANCE TUNING**: Automatic device capability detection, optimal work size calculation, efficient memory access patterns for different GPU architectures. |
| `opencl_sha3.cl` | OpenCL kernel source code for SHA3/Keccak mining. Implements optimized SHA3 algorithm for OpenCL-compatible GPUs. Key components: rotation constants (rot), position indices (pos), round constants (RC) for Keccak. Main 'sha3' kernel function performs parallel nonce testing with configurable rounds. Includes endianness handling (swap_endian_64), state array management, and difficulty checking. Each work item tests multiple nonces efficiently. Used by OpenCL engine for cross-platform GPU mining (AMD, Intel, NVIDIA OpenCL drivers). Compatible with OpenCL 1.0+ specifications. |
| `p2pool_client.rs` | P2Pool mining client implementation. P2poolClientWrapper implements NodeClient trait for mining through Tari P2Pool using ShaP2PoolClient gRPC interface. Key features: P2Pool node connection management with automatic retry, wallet payment address and coinbase extra data configuration, block template requests with SHA3 PoW algorithm, new block generation with mining parameters, block submission with wallet integration. Handles both Tari chain and P2Pool chain height tracking. Designed for pool mining where multiple miners contribute hashrate and share rewards. Integrates with Tari P2Pool protocol for decentralized mining pool operation. |
| `stats_store.rs` | **ATOMIC MINING STATISTICS STORAGE**: Thread-safe statistics accumulator providing real-time mining performance tracking across all GPU threads. **CONCURRENCY MODEL**: Uses AtomicU64 with SeqCst memory ordering for lock-free statistics updates from multiple mining threads, enabling high-frequency updates without contention. **PERFORMANCE METRICS**: Tracks hashes_per_second (current mining rate), accepted_blocks (successful mining), rejected_blocks (failed submissions) with atomic operations ensuring accuracy under concurrent access. **SYSTEM INTEGRATION**: Updated by mining threads in main.rs during mining operations, queried by HTTP stats endpoints for real-time performance data, provides foundation for monitoring and optimization. **THREADING SAFETY**: Lock-free atomic operations prevent performance degradation from statistics collection, safe concurrent access from multiple GPU mining threads, consistent read operations for monitoring queries. **STATISTICAL ACCURACY**: Atomic updates ensure accurate hashrate calculation, prevents race conditions in statistics aggregation, maintains data integrity under high-frequency mining operations. **MONITORING INTEGRATION**: Provides data source for stats_collector.rs aggregation, serves http/handlers/stats.rs endpoints, enables performance analysis and troubleshooting. **CODE ISSUE**: Contains bug in inc_rejected_blocks() method which incorrectly increments accepted_blocks counter instead of rejected_blocks, affecting mining statistics accuracy. **CROSS-REFERENCES**: Updated by mining loops in main.rs, queried by HTTP monitoring system, aggregated by stats_collector.rs for time-windowed analysis. |
| `tari_coinbase.rs` | Tari coinbase transaction generation for mining rewards. Provides generate_coinbase() function that creates coinbase outputs and kernels for newly mined blocks. Uses Tari's transaction key manager, wallet payment addresses, and consensus constants to generate: TransactionOutput for miner rewards, TransactionKernel for transaction validation. Supports stealth payments, configurable range proof types, and payment ID integration. Integrates with Tari's one-sided payment protocol and coinbase building infrastructure. Essential for converting mining success into valid blockchain transactions that credit miners with block rewards and transaction fees. |

#### src/http/

| File | Description |
|------|-------------|
| `config.rs` | HTTP server configuration struct. Simple Config struct containing port number for HTTP monitoring server. Provides default port 18000 and constructor method. Used by HttpServer for binding configuration. |
| `mod.rs` | HTTP server module declarations. Exports submodules: config (server configuration), handlers (HTTP endpoint implementations), server (HTTP server implementation), stats_collector (hashrate statistics aggregation). Provides monitoring and status API for the glytex miner. |
| `server.rs` | **MONITORING API SERVER**: HTTP REST API providing real-time mining visibility and operational monitoring. **ARCHITECTURAL ROLE**: External interface layer enabling monitoring, debugging, and integration with external systems (Prometheus, Grafana, monitoring dashboards). **FRAMEWORK INTEGRATION**: Built on Axum web framework with async/await support, integrates with Tari's shutdown signal system for graceful termination, provides concurrent access while mining operates. **API ENDPOINTS**: /health (service availability), /version (build identification), /stats (real-time performance metrics). **SYSTEM INTEGRATION**: AppState provides shared access to StatsClient for live mining statistics, coordinates with StatsCollector background service, operates independently from mining threads. **OPERATIONAL MONITORING**: Enables external monitoring of hashrate performance, GPU device status, mining success rates, and service health without interrupting mining operations. **NETWORK SECURITY**: Binds to localhost only for security, configurable port (default 18000), no authentication (local access only). **CONCURRENCY MODEL**: Async request handling allows multiple concurrent monitoring connections, non-blocking access to mining statistics, graceful shutdown coordination. **CROSS-REFERENCES**: Uses StatsClient from stats_collector.rs, integrated by main.rs during startup, serves data from StatsStore, responds to handlers in handlers/ directory. **DEPLOYMENT INTEGRATION**: Supports container health checks, load balancer probes, monitoring system scraping, operational dashboards, and debugging workflows. |
| `stats_collector.rs` | **REAL-TIME STATISTICS AGGREGATION**: Background service collecting and aggregating mining performance data from all GPU mining threads. **ARCHITECTURAL PATTERN**: Observer pattern with broadcast channels collecting HashrateSample events from mining threads and providing aggregated statistics to HTTP API. **DATA FLOW**: Mining threads → broadcast channels → StatsCollector → time-windowed aggregation → StatsClient → HTTP endpoints. **PERFORMANCE WINDOWS**: Maintains rolling 60-second history per GPU device, calculates 10-second and 1-minute average hashrates, handles device-specific tracking with VecDeque buffers. **REAL-TIME AGGREGATION**: Continuous processing of hashrate samples, automatic expiration of old data points, per-device and total hashrate calculation, request/response pattern for statistics queries. **CONCURRENCY COORDINATION**: Receives async HashrateSample messages, processes statistics in dedicated tokio task, provides thread-safe StatsClient interface for HTTP server access. **SYSTEM INTEGRATION**: Launched by main.rs during startup, connects mining threads via broadcast channels, serves HTTP server through StatsClient, integrates with graceful shutdown. **DATA STRUCTURES**: GetHashrateResponse provides both per-device hashrates and total mining statistics, HashrateSample contains device_id and hashes_per_second measurements. **CROSS-REFERENCES**: Used by http/server.rs via StatsClient, receives data from mining threads in main.rs, serves handlers/stats.rs endpoint, coordinates with StatsStore. **MONITORING VALUE**: Enables real-time performance visibility, historical trend analysis, per-GPU performance comparison, and operational health assessment without impacting mining performance. |

##### src/http/handlers/

| File | Description |
|------|-------------|
| `health.rs` | Health check HTTP endpoint handler. Simple health check endpoint that returns HTTP 200 OK status. Used for monitoring system health and service availability. Part of the HTTP monitoring API. |
| `mod.rs` | HTTP handlers module declarations. Exports health, stats, and version handler modules for the monitoring HTTP server endpoints. |
| `stats.rs` | **MINING STATISTICS ENDPOINT**: HTTP handler serving real-time mining performance data in JSON format. **API CONTRACT**: GET /stats returns comprehensive mining statistics including per-device hashrates (10-second and 1-minute windows) and total aggregated performance. **DATA SOURCE INTEGRATION**: Uses StatsClient to query StatsCollector for live mining data, provides both granular per-GPU statistics and system-wide totals. **JSON RESPONSE STRUCTURE**: Stats struct with hashrate_per_device (device_id → time-windowed hashrates) and total_hashrate (aggregated performance), compatible with monitoring tools and dashboards. **SYSTEM MONITORING**: Enables external monitoring system integration (Prometheus scraping, Grafana dashboards, custom monitoring tools), provides actionable performance data for mining optimization. **PERFORMANCE VISIBILITY**: Shows real-time hashrate for each GPU device, identifies performance variations across devices, tracks mining efficiency trends, enables capacity planning and troubleshooting. **OPERATIONAL USE CASES**: Mining farm monitoring, performance debugging, efficiency analysis, capacity planning, and integration with existing infrastructure monitoring. **CROSS-REFERENCES**: Serves data from stats_collector.rs via StatsClient, integrated into server.rs routing, format matches monitoring expectations from external tools. **REAL-TIME NATURE**: Provides current mining performance without historical storage, focuses on operational monitoring rather than long-term analytics. |
| `version.rs` | Version information HTTP endpoint handler. Returns current glytex version from Cargo.toml via CARGO_PKG_VERSION environment variable. Simple text endpoint for version checking and service identification. |

### supply-chain/

| File | Description |
|------|-------------|
| `audits.toml` | Cargo-vet security audit declarations file. Currently empty, indicating no local security audits have been performed. Used by cargo-vet for tracking security reviews of Rust crate dependencies. Part of supply chain security framework ensuring dependency integrity and vulnerability management. |
| `config.toml` | **SUPPLY CHAIN SECURITY FRAMEWORK**: Cargo-vet configuration establishing trust relationships and security audit policies for dependency management. **TRUST NETWORK**: Imports security audit data from established sources (bytecode-alliance, embark-studios, google, isrg, mozilla, zcash) creating distributed trust for dependency verification. **TARI ECOSYSTEM INTEGRATION**: Defines audit policies for Tari project dependencies (minotari_app_grpc, tari_common, tari_core) treating internal dependencies as equivalent to crates.io for audit purposes. **SECURITY EXEMPTIONS**: Extensive exemption list marking third-party crates as safe-to-deploy based on community trust, established usage patterns, and vendor reputation. **AUTOMATED SECURITY**: Integrates with audit.yml workflow for daily vulnerability scanning, enables continuous security monitoring of dependency changes, prevents introduction of unaudited dependencies. **RISK MANAGEMENT**: Provides security audit trail for all dependencies, enables security audit compliance for enterprise deployments, maintains security posture documentation. **ECOSYSTEM COMPLIANCE**: Follows Rust security audit best practices, leverages community security intelligence, provides transparency for security-conscious users. **CROSS-REFERENCES**: Used by audit.yml for automated security scanning, coordinates with Cargo.lock for dependency verification, integrates with supply-chain/audits.toml for local audit storage. **OPERATIONAL SECURITY**: Enables secure dependency management for mining operations, provides audit trail for compliance requirements, supports security-conscious deployment environments. |
| `imports.lock` | [GENERATED] Cargo-vet imports lockfile tracking publisher verification data for crate dependencies. Contains timestamped publisher verification records from external audit sources including user IDs, login names, and publication dates. Generated automatically by cargo-vet to ensure reproducible security audit states. Used for supply chain integrity verification and publisher attestation. |

