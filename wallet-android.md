# Wallet-Android

## Project Overview

## Tari Wallet Android - Codebase Overview

### System Summary
Cryptocurrency wallet Android app with native Rust core accessed via FFI. MVVM architecture with mixed XML Views + Jetpack Compose UI, Tor privacy integration, and biometric security.

### Directory Structure
```
app/src/main/java/com/tari/android/wallet/
├─ application/     # App lifecycle, WalletManager setup
├─ data/           # Repositories, SharedPreferences access
├─ di/             # Dagger 2 dependency injection modules
├─ ffi/            # Native Rust FFI wrapper classes
├─ infrastructure/ # Services, networking, Tor proxy
├─ model/          # Data classes and entities
├─ navigation/     # Custom navigation system
├─ notification/   # Firebase push notifications
├─ tor/            # Tor proxy integration
├─ ui/             # Activities, fragments, ViewModels
└─ util/           # Extensions and helper utilities

buildSrc/src/main/kotlin/
├─ Dependencies.kt    # Centralized version management
├─ TariBuildConfig.kt # Build configuration
└─ download-libwallet.gradle.kts # Native library management

libwallet/           # Native Rust libraries (arm64/x86_64)
yatlib/             # YAT emoji address integration
```

### Architecture
MVVM + Clean Architecture. Native Rust wallet via FFI → JNI C++ bridge → Kotlin wrappers. Dagger 2 for DI. StateFlow/LiveData for reactive UI. Custom navigation on top of Navigation Component.

### Key Files
- Entry: `WalletApplication.kt` (app initialization)
- Core: `WalletManager.kt` (wallet lifecycle coordinator)
- FFI: `app/src/main/cpp/jniWallet.cpp` (native bridge)
- Config: `buildSrc/Dependencies.kt` (versions)
- Native: `libwallet/` directory (core Rust implementation)

### Conventions
- ViewModels named `[Feature]ViewModel`
- Repositories follow pattern: `[Entity]Repository`
- FFI classes extend `FFIBase` for memory management
- All async operations use Coroutines with proper scope
- UI state via StateFlow, events via custom EffectFlow
- Modular dialogs via `ModularDialog` builder pattern

### Important Rules
- **FFI Memory**: Always use FFIBase.dispose() to prevent leaks
- **Native Threads**: FFI calls must be on background threads
- **ProGuard**: Keep all FFI classes, models, and YAT integration
- **Security**: Never log/expose private keys or seed phrases
- **Architecture**: Place business logic in ViewModels, not fragments
- **State**: Use StateHandlers for complex reactive state management

### Critical Dependencies
- **Native**: libminotari_wallet_ffi (core wallet), OpenSSL, SQLite
- **Android**: AndroidX, Compose, Coroutines, Dagger 2
- **Network**: Retrofit, OkHttp, Tor proxy
- **Security**: Biometric, encrypted SharedPreferences
- **External**: Sentry (crashes), Firebase (push), YAT (emoji addresses)

### Build Gotchas
- Native libraries downloaded via Gradle task before build
- Requires `secret.properties`, `sentry.properties`, `yat.properties`
- ProGuard rules critical for FFI layer preservation
- Different product flavors: regular vs privacy (no analytics)
- Version code auto-generated from git commit count

### Database
- Core data in native SQLite via Rust FFI (transactions, UTXOs, contacts)
- Android SharedPreferences for settings and config
- No Room or direct SQLite usage - all through native layer

### Testing
- Unit tests for utilities and repositories
- Instrumented tests for FFI integration
- Mock data available via DebugConfig for UI development
- Test classes follow pattern: `[Feature]Tests`

## Codebase Structure

### Root Directory

| File | Description |
|------|-------------|
| `.gitignore` | Git ignore configuration for Android project. Excludes build artifacts (*.apk, *.dex, build/, .gradle/), IDE files (.idea/, *.iml), native build outputs (.cxx/, .externalNativeBuild), secret configuration files (secret.properties, sentry.properties, yat.properties), Firebase config (google-services.json), native libraries (libwallet/ files), log files, keystores. Includes app-specific native libs directory. |
| `LICENSE` | BSD 3-Clause License for The Tari Project (2019). Standard open source license permitting redistribution and modification with attribution requirements. Disclaims warranties and limits liability for copyright holders and contributors. |
| `README.md` | Primary project documentation providing setup instructions, architecture overview, and contribution guidelines. Contains comprehensive build instructions requiring Android Studio 4.0+, NDK setup, and configuration details. Explains Tari Universe Wallet as reference implementation for Tari digital currency ecosystem. Includes Play Store/App Store links, build variants (regular/privacy), and optional configuration for Sentry crash reporting. Essential onboarding documentation for developers. |
| `build.gradle.kts` | Root Gradle build script using Kotlin DSL. Configures buildscript dependencies: Android Gradle Plugin, Kotlin, Sentry, Firebase. Sets up repositories (Google, Maven Central, Gradle Plugin Portal). Applies KSP plugin for annotation processing. Defines clean task to delete build directories. Uses Dependencies object for centralized dependency management. |
| `gradle.properties` | Global Gradle build configuration defining JVM arguments, AndroidX settings, and build optimizations. Sets heap size to 2536m, enables AndroidX and Jetifier, configures ignore lists for problematic libraries. Includes Kotlin code style settings and resource ID configuration. Critical for consistent build environment across development machines and CI/CD systems. |
| `gradlew` | Gradle wrapper script for Unix/Linux systems. Executable script that downloads and runs the specified Gradle version, ensuring consistent build environment across different development setups. |
| `gradlew.bat` | Gradle wrapper script for Windows systems. Batch script that downloads and runs the specified Gradle version, ensuring consistent build environment on Windows development machines. |
| `secret-example.properties` | Example configuration file for sensitive API keys and secrets. Template showing required secret configuration format without actual values. Developers copy and populate with real credentials. |
| `sentry-example.properties` | Example configuration file for Sentry crash reporting integration. Template for Sentry API keys and project configuration. Enables error tracking and performance monitoring setup. |
| `settings.gradle.kts` | Root Gradle settings configuration for multi-module Android project. Includes app and yatlib modules with dependency resolution management. Configures repository sources including Google, Maven Central, Guardian Project, and JitPack for comprehensive dependency access. Sets strict repository mode and project naming. Used by Gradle build system for module discovery and dependency resolution. |
| `yat-example.properties` | Example configuration file for YAT (Your Alias Token) service integration. Template for YAT API credentials and service configuration enabling alias token functionality. |

### app/

| File | Description |
|------|-------------|
| `.gitignore` | App module specific git ignore rules. Excludes app build directory and release output folder to prevent build artifacts from being committed. |
| `build.gradle.kts` | Main Android app module build configuration with comprehensive plugin setup and dependency management. Configures Android application with Kotlin, Sentry, Compose, and native build integration. Includes build variants (debug/release), product flavors (regular/privacy), and native library integration via CMake. Manages version code from git commits, external configuration files (secrets, Sentry, YAT), and ProGuard optimization. Includes comprehensive dependency declarations for AndroidX, testing, UI, networking, and third-party integrations. |
| `proguard-rules.pro` | ProGuard/R8 code obfuscation rules for release builds. Preserves source file line numbers for debugging stack traces. Keeps entire FFI layer classes, model classes, enums, fragments, ViewModels. Protects YAT integration, wallet callbacks, data layer classes. Suppresses warnings for JGSS, naming APIs. Includes Google Drive SDK compatibility rules. Critical for maintaining JNI interface integrity in release builds. |

###### app/src/androidTest/java/com/tari/android/wallet/

| File | Description |
|------|-------------|
| `FFIByteVectorTests.kt` | Android instrumented tests for FFI ByteVector data type. Tests byte array operations, serialization/deserialization, memory management for native byte vector objects. Validates conversion between Java byte arrays and native FFI ByteVector structures. Critical for ensuring data integrity in JNI layer communications. |
| `FFICommsConfigTests.kt` | Android instrumented tests for FFI communications configuration. Tests network communication settings, transport protocols, connection parameters for Tari wallet network layer. Validates configuration objects used for peer-to-peer communication setup. Ensures proper FFI integration for network configuration management. |
| `FFITariContactTests.kt` | Android instrumented tests for FFI Tari Contact data structures. Tests contact creation, public key handling, emoji ID conversion, contact serialization. Validates FFI integration for contact management system. Ensures proper data exchange between native contact structures and Android contact objects. |
| `FFITestSuite.kt` | JUnit test suite runner for FFI (Foreign Function Interface) instrumented tests. Aggregates multiple test classes: FFIByteVectorTests, FFICommsConfigTests, FFITariContactTests, FFITransportTypeTest, FFIWalletAddressTests, FFIWalletTests, HexStringTests, NetAddressStringTests. Provides comprehensive testing of native library integration and data conversion utilities. Uses AndroidJUnit4 test runner for Android environment testing. |
| `FFITestUtil.kt` | Utility functions for FFI testing infrastructure. Provides helper methods for test data generation, mock object creation, test environment setup. Used across FFI test classes for consistent test configuration and data preparation. Supports testing of native library integration with standardized test utilities. |
| `FFITransportTypeTest.kt` | Android instrumented tests for FFI transport type enumeration. Tests network transport protocol definitions (TCP, TOR, etc.), transport configuration validation, protocol selection logic. Ensures correct FFI integration for network transport layer configuration in Tari wallet communications. |
| `FFIWalletAddressTests.kt` | Android instrumented tests for FFI wallet address functionality. Tests Tari wallet address creation, validation, conversion between different address formats, emoji ID generation. Validates FFI integration for wallet addressing system. Critical for ensuring address compatibility and data integrity in wallet operations. |
| `FFIWalletTests.kt` | Android instrumented tests for core FFI wallet functionality. Tests wallet creation, startup, shutdown, transaction operations, balance queries, contact management through native FFI layer. Comprehensive validation of wallet core operations and FFI integration stability. Primary test suite for wallet functionality. |
| `HexStringTests.kt` | Android instrumented tests for hexadecimal string utilities. Tests hex string validation, conversion to/from byte arrays, string formatting, encoding/decoding operations. Validates utility functions used throughout wallet for cryptographic data representation and display. |
| `NetAddressStringTests.kt` | Android instrumented tests for network address string parsing and validation. Tests IP address parsing, port extraction, network address formatting, address type detection. Validates network address utilities used in peer discovery and communication setup. |

##### app/src/main/

| File | Description |
|------|-------------|
| `AndroidManifest.xml` | Android application manifest for Tari Wallet. Declares permissions: camera, contacts, internet, biometric, NFC, notifications. Defines main application class (TariWalletApplication), file provider for log sharing. Configures Firebase Cloud Messaging service. Declares activities: StartActivity (launcher/splash), OnboardingFlowActivity, AuthActivity, HomeActivity with deep link support (tari:// scheme, NFC), QrScannerActivity, YatFinalizeSendTxActivity, DebugActivity, WalletRestoreActivity. Sets up Sentry crash reporting. |

###### app/src/main/assets/tor/

| File | Description |
|------|-------------|
| `bridges.txt` | Tor bridge configuration file containing bridge relay information for circumventing network censorship. Lists bridge addresses and connection parameters for accessing Tor network in restricted environments. Used when direct Tor connections are blocked or need additional privacy layers. |
| `geoip` | Tor geographic IP address database for IPv4 addresses. Maps IP address ranges to country codes for routing decisions and exit node selection. Used by Tor daemon for geographic routing policies and circuit selection in privacy mode operations. |
| `geoip6` | Tor geographic IP address database for IPv6 addresses. Maps IPv6 address ranges to country codes for routing decisions and exit node selection. Used by Tor daemon for IPv6 geographic routing policies and circuit selection in privacy mode operations. |
| `torrc` | Tor network configuration file defining routing behavior for privacy mode. Configures Tor daemon settings, bridge connections, circuit building, exit policies. Used when wallet operates in privacy mode to anonymize network communications through Tor network. Critical for privacy-preserving wallet operations. |

###### app/src/main/cpp/

| File | Description |
|------|-------------|
| `CMakeLists.txt` | CMake build configuration for native C++ library compilation and linking. Defines static library imports for OpenSSL (libssl, libcrypto), SQLite (libsqlite3), and Tari wallet core (libminotari_wallet_ffi). Compiles 20+ JNI bridge files into native-lib shared library. Configures C++11 standard and includes Android logging support. Critical build configuration linking Android app to native Rust wallet implementation. |
| `jniBalance.cpp` | JNI wrapper for Tari wallet balance operations. Implements native methods for balance queries, available/pending balance calculations, balance conversions between different units. Bridges Java/Kotlin balance management with native FFI balance structures. Used by BalanceStateHandler for real-time balance updates. |
| `jniByteVector.cpp` | JNI wrapper for FFI ByteVector data structure. Handles byte array operations, memory management, serialization/deserialization between Java byte arrays and native byte vectors. Critical for data exchange in FFI layer, used throughout wallet for cryptographic data and binary serialization. |
| `jniCollections.cpp` | JNI wrapper for FFI collection data structures. Implements native methods for handling lists, vectors, and arrays of wallet objects (transactions, contacts, UTXOs). Provides iteration, access, and management of native collections. Essential for bulk data operations in wallet. |
| `jniCommon.cpp` | Common JNI utilities and logging infrastructure used across all native bridge components. Contains logging macros (LOGE, LOGI, etc.), error handling utilities, and shared JNI helper functions. Provides foundation for all JNI implementations throughout the native layer. Includes Android logging integration and common C++ utilities for memory management and error handling. Essential infrastructure for native-Java communication. |
| `jniCommsConfig.cpp` | JNI wrapper for Tari communications configuration. Handles network communication settings, transport protocols, connection parameters for peer-to-peer networking. Configures base node connections, peer discovery, message routing. Used by WalletManager for network setup. |
| `jniCompletedTransaction.cpp` | JNI wrapper for completed transaction objects. Provides access to finalized transaction data: transaction ID, amount, fee, timestamp, status, message. Enables querying transaction history and displaying transaction details in UI. Core component for transaction management. |
| `jniCompletedTransactionKernel.cpp` | JNI wrapper for completed transaction kernel data structure. Provides access to transaction kernel information including excess, signature, and verification data. Used for transaction validation and blockchain proof verification. Essential for cryptographic transaction integrity checks in the native layer. |
| `jniContact.cpp` | JNI wrapper for contact management in native layer. Handles contact creation, public key operations, emoji ID conversion, contact persistence. Bridges Java contact objects with native FFI contact structures. Used by ContactsRepository for contact operations and address book management. |
| `jniEmojiSet.cpp` | JNI wrapper for emoji set operations used in Tari emoji ID generation. Handles emoji character mapping, validation, and conversion between public keys and emoji representations. Critical for Tari's human-readable address system using emoji sequences instead of cryptographic addresses. |
| `jniOutputFeatures.cpp` | JNI wrapper for transaction output features including maturity periods, output types, and metadata. Handles UTXO feature flags, time locks, and output constraints. Used for transaction output validation and feature enforcement in the native wallet layer. |
| `jniPendingInboundTransaction.cpp` | JNI wrapper for pending inbound transaction objects. Provides access to incoming transaction data before completion: sender information, amount, message, transaction ID. Enables monitoring and management of pending incoming transactions in the wallet UI. |
| `jniPendingOutboundTransaction.cpp` | JNI wrapper for pending outbound transaction objects. Handles outgoing transaction data before completion: recipient information, amount, fee, message, status. Used for tracking sent transactions through various stages (pending, broadcasting, mined) until completion. |
| `jniPrivateKey.cpp` | JNI wrapper for cryptographic private key operations. Handles private key generation, serialization, signing operations, and secure memory management. Critical for wallet security, transaction signing, and cryptographic operations. Implements secure key handling practices in native layer. |
| `jniPublicKey.cpp` | JNI wrapper for cryptographic public key operations. Handles public key generation, validation, serialization, verification operations. Used for address generation, transaction verification, contact identification, and cryptographic validation throughout the wallet system. |
| `jniSeedWords.cpp` | JNI wrapper for BIP39 seed phrase operations. Handles mnemonic seed phrase generation, validation, entropy conversion, and wallet recovery operations. Critical for wallet backup/restore functionality and secure key derivation from human-readable seed phrases. |
| `jniTariBaseNodeState.cpp` | JNI wrapper for base node state information. Provides access to base node connection status, sync state, chain height, and network information. Used for monitoring blockchain synchronization progress and base node connectivity health in the wallet. |
| `jniTariCoinPreview.cpp` | JNI wrapper for coin preview functionality in transaction preparation. Handles coin selection preview, UTXO analysis, fee estimation preview before transaction commitment. Used for transaction fee calculation and coin selection optimization in the wallet. |
| `jniTariFeePerGramStat.cpp` | JNI wrapper for individual fee-per-gram statistics. Provides access to single fee statistic data points including fee rate, order, and statistical measures. Used for granular fee analysis and transaction fee optimization in the native wallet layer. |
| `jniTariFeePerGramStats.cpp` | JNI wrapper for fee-per-gram statistics collection. Handles aggregated fee statistics, fee estimation algorithms, and historical fee data analysis. Critical for dynamic fee calculation and providing users with optimal transaction fee recommendations based on network conditions. |
| `jniTariPaymentRecord.cpp` | JNI C++ implementation for loading Tari payment record data from native wallet library. Exports: jniLoadData() native function. Dependencies: wallet.h (Tari core library), jniCommon.cpp utilities. Used by: FFITariPaymentRecord.jniLoadData(). Handles marshalling of payment record fields including payment reference (32-byte array), amount, block height, mined timestamp, and direction from native TariPaymentRecord struct to Java object fields. |
| `jniTariTransportConfig.cpp` | JNI wrapper for Tari network transport configuration. Handles transport protocol settings (TCP, Tor), connection parameters, encryption options, and network routing configuration. Essential for configuring peer-to-peer communication protocols in the wallet. |
| `jniTariUnblindedOutput.cpp` | JNI wrapper for unblinded output operations in transaction processing. Handles UTXO unblinding, value extraction, ownership verification. Critical for transaction privacy and confidential transaction handling in the Tari protocol. |
| `jniTariUtxo.cpp` | JNI wrapper for UTXO (Unspent Transaction Output) management. Handles UTXO creation, validation, spending, and tracking. Core component for transaction input/output handling and wallet balance management in the native layer. |
| `jniTariVector.cpp` | JNI wrapper for generic vector collections in the FFI layer. Provides dynamic array operations, iteration, and memory management for collections of native objects. Used throughout the FFI layer for handling lists of transactions, contacts, UTXOs, and other wallet objects. |
| `jniTariWalletAddress.cpp` | JNI wrapper for Tari wallet address operations. Handles wallet address generation, validation, encoding/decoding, and emoji ID conversion. Critical for wallet addressing, contact management, and transaction recipient identification in the native layer. |
| `jniTransactionSendStatus.cpp` | JNI wrapper for transaction send status tracking. Handles transaction state enumeration (pending, broadcasting, confirmed, rejected), status updates, and transaction lifecycle monitoring. Used for tracking outbound transaction progress through various network states. |
| `jniWallet.cpp` | C++ JNI bridge implementation connecting Android Kotlin code to native Rust wallet library. Main native implementation file providing complete wallet functionality through JNI interface. Exports: All native wallet operations accessible from Kotlin FFIWallet class. Dependencies: libminotari_wallet_ffi.a, jniCommon.cpp, Android JNI. Used by: FFIWallet.kt as native implementation. Features transaction management, balance operations, mining, UTXO handling, callback registration, and comprehensive wallet lifecycle management. Licensed under Tari Project BSD license. Critical native bridge enabling full wallet functionality. |

###### app/src/main/java/com/tari/android/wallet/application/

| File | Description |
|------|-------------|
| `ActivityLifecycleCallbacks.kt` | Application-wide activity lifecycle monitoring. Implements Android ActivityLifecycleCallbacks to track activity creation, start, resume, pause, stop, destroy events. Used by TariWalletApplication for app-wide lifecycle management, security checks, and resource cleanup. Enables centralized activity state tracking across the wallet application. |
| `AppStateHandler.kt` | Application lifecycle state management singleton using BroadcastEffectFlow. Tracks and broadcasts app state changes: AppForegrounded, AppBackgrounded, AppDestroyed. Used by TariWalletApplication.AppObserver to notify components of app lifecycle transitions. Enables reactive programming for components that need to respond to app state changes. All events dispatched on Main dispatcher. |
| `MigrationManager.kt` | Database and app state migration manager for version upgrades. Handles data schema changes, preference migrations, file system updates between app versions. Ensures smooth transitions when updating wallet software while preserving user data and wallet state. Critical for maintaining data integrity across version updates. |
| `Network.kt` | Tari network enumeration defining supported blockchain networks. Includes MAINNET, test networks (RINCEWIND, RIDCULLY, STIBBONS, IGOR, DIBBLER, ESMERALDA), NEXTNET, STAGENET. Each network has uriComponent for deep links and displayName for UI. Provides companion function to parse network from URI component string. Used throughout app for network-specific configurations and validations. |
| `TariWalletApplication.kt` | Main Android Application class coordinating app lifecycle, dependency injection, and core services initialization. Exports: TariWalletApplication class with 160+ lines of setup logic. Initializes DI container, notification channels, logging framework, network monitoring, security handlers, and YAT integration. Manages app lifecycle callbacks, background/foreground detection, and WebView debugging. Sets up Sentry crash reporting and Paper database. Critical entry point for entire application. |
| `YatAdapter.kt` | YAT (Your Address Token) integration adapter implementing YatIntegration.Delegate. Manages YAT emoji address connections, lookup services, payment address resolution. Handles YAT configuration with organization credentials from BuildConfig. Integrates with YatPrefRepository for persistence. Provides functionality to connect/disconnect YAT addresses, resolve emoji IDs to Tari addresses. Supports transaction sending via YAT addresses. Used for YAT-based addressing in Tari wallet. |

###### app/src/main/java/com/tari/android/wallet/application/baseNodes/

| File | Description |
|------|-------------|
| `BaseNodesManager.kt` | Singleton manager for Tari base node discovery and configuration. Loads base node lists from resources and preference storage, validates node addresses with regex patterns (IPv4, Onion). Manages current base node selection, node availability checking, network-specific node lists. Provides base node switching functionality and health monitoring. Critical for wallet blockchain connectivity. |

###### app/src/main/java/com/tari/android/wallet/application/deeplinks/

| File | Description |
|------|-------------|
| `DeepLink.kt` | Deep link data model representing wallet deep link requests. Defines structure for tari:// protocol links including network, action type, parameters (amounts, addresses, messages). Used for processing payment requests, wallet connections, transaction initiations from external sources. Supports multiple Tari networks and action types. |
| `DeeplinkManager.kt` | Deep link processing and routing manager. Handles incoming tari:// protocol links, validates deep link format, extracts parameters, routes to appropriate wallet actions. Manages deep link queue, handles app state transitions for deep link processing. Coordinates with UI layers for deep link action execution. |
| `DeeplinkParser.kt` | Deep link URL parsing utility. Parses tari:// protocol URLs, extracts network information, action types, transaction parameters. Validates URL format, handles malformed links, converts URL parameters to DeepLink objects. Used by DeeplinkManager for initial deep link processing and validation. |

###### app/src/main/java/com/tari/android/wallet/application/securityStage/

| File | Description |
|------|-------------|
| `StagedWalletSecurityManager.kt` | Security stage management for wallet onboarding and setup process. Manages progressive security feature enrollment: biometric setup, PIN creation, backup verification. Tracks security stage completion, enforces security requirements, guides users through security setup flow. Ensures proper wallet security configuration before full wallet access. |

###### app/src/main/java/com/tari/android/wallet/application/walletManager/

| File | Description |
|------|-------------|
| `MainFFIWalletListener.kt` | Primary FFI wallet event listener implementation. Handles native wallet callbacks, processes transaction events, balance updates, base node state changes. Integrates with state handlers (BalanceStateHandler, BaseNodeStateHandler) to update app state. Core bridge between native wallet events and Android application state management. |
| `WalletCallbacks.kt` | Singleton FFI wallet callback dispatcher supporting multiple wallet instances. Manages callback listeners mapped by wallet context ID, routes native FFI callbacks to appropriate listeners. Handles transaction events (received, sent, completed), balance updates, base node state changes, restoration progress. Critical for FFI-to-Android communication bridge. Must be protected in ProGuard rules due to native callback method names. |
| `WalletConfig.kt` | Wallet configuration data class containing initialization parameters. Defines wallet setup options including network selection, base node configuration, logging levels, transport settings. Used by WalletManager for wallet startup configuration and runtime parameter management. |
| `WalletManager.kt` | Core wallet coordination system managing entire wallet lifecycle, Tor integration, and blockchain operations. Exports: WalletManager singleton with 450+ lines orchestrating all wallet functionality. Handles wallet creation/restoration, Tor proxy coordination, transaction management, base node synchronization, and callback systems. Manages FFI wallet lifecycle, state transitions, and recovery operations. Critical component coordinating all wallet operations with comprehensive error handling and state management. |
| `WalletState.kt` | Wallet state enumeration and data classes representing wallet operational states. Defines states like STARTING, STARTED, STOPPING, STOPPED with associated data. Used throughout the wallet for state management, UI updates, and operation coordination based on current wallet status. |
| `WalletStateHandler.kt` | Reactive state manager for wallet operational states. Maintains MutableStateFlow with current WalletState, handles state transitions (starting, started, stopping, stopped), provides state observation for UI components. Central coordinator for wallet lifecycle state management and state-dependent operations. |
| `WalletValidator.kt` | Wallet validation utilities for data integrity and consistency checks. Validates transaction data, address formats, balance calculations, and wallet state consistency. Provides validation functions used throughout wallet operations to ensure data correctness and prevent invalid operations. |

###### app/src/main/java/com/tari/android/wallet/data/

| File | Description |
|------|-------------|
| `BalanceStateHandler.kt` | Singleton reactive state manager for wallet balance information. Maintains MutableStateFlow with BalanceInfo containing available, pending incoming/outgoing, and time-locked balances. Provides thread-safe balance state updates via Flow pattern. Central source of truth for balance data across the application. Used by UI components for real-time balance display. |
| `ConnectionStateHandler.kt` | Reactive connection state manager tracking wallet network connectivity. Maintains connection status to base nodes, peer network, and internet connectivity. Provides Flow-based connection state updates for UI components and wallet operations that depend on network availability. |

###### app/src/main/java/com/tari/android/wallet/data/airdrop/

| File | Description |
|------|-------------|
| `AirdropRepository.kt` | Airdrop system repository handling miner rewards and token management with backend API integration. Exports: AirdropRepository singleton. Manages airdrop authentication tokens, miner statistics, mining status, and user details via Retrofit API calls. Provides token persistence through CorePrefRepository and reactive StateFlow for token updates. Used by airdrop-related UI components and services. |
| `AirdropRetrofitService.kt` | Retrofit API service interface for airdrop backend communications. Defines REST endpoints for airdrop eligibility checks, claim submissions, campaign information retrieval. Used by AirdropRepository for network communications with airdrop distribution services. |

###### app/src/main/java/com/tari/android/wallet/data/baseNode/

| File | Description |
|------|-------------|
| `BaseNodeState.kt` | Data class representing base node connection and synchronization state. Contains connection status, sync progress, chain height information, last sync timestamp. Used for tracking blockchain synchronization progress and base node connectivity health throughout the wallet. |
| `BaseNodeStateHandler.kt` | Reactive state manager for base node connection and synchronization status. Maintains MutableStateFlow with BaseNodeState, handles state updates from FFI callbacks, provides connection monitoring for UI components. Central source for base node connectivity information. |
| `BaseNodeSyncState.kt` | Enumeration and data classes for base node synchronization states. Defines sync states (connecting, syncing, synced, offline) with associated progress information. Used for tracking and displaying blockchain synchronization progress in the wallet UI. |

###### app/src/main/java/com/tari/android/wallet/data/chat/

| File | Description |
|------|-------------|
| `ChatItemDto.kt` | Data transfer object for chat items in the wallet messaging system. Represents individual chat conversations with contacts, including metadata like last message, timestamp, unread count. Used for chat list display and conversation management in the wallet's messaging features. |
| `ChatMessageItemDto.kt` | Data transfer object for individual chat messages within conversations. Contains message content, sender information, timestamp, delivery status, message type. Used for displaying and managing individual messages in chat conversations within the wallet. |
| `ChatsRepository.kt` | Repository for chat and messaging functionality within the wallet. Manages chat conversations, message storage, contact messaging integration. Handles chat data persistence, message synchronization, and conversation management. Integrates with contacts system for peer-to-peer messaging capabilities. |

###### app/src/main/java/com/tari/android/wallet/data/contacts/

| File | Description |
|------|-------------|
| `Contact.kt` | Core contact data model representing wallet contacts. Contains contact information including public key, emoji ID, alias, favorite status. Provides contact identification and display data for transaction recipients and address book management. Central contact entity used throughout the wallet. |
| `ContactsDb.kt` | Contact database interface and data access layer. Provides database operations for contact storage, retrieval, and management. Handles contact persistence, search functionality, and database schema for contact-related data. Core data access component for contact management. |
| `ContactsRepository.kt` | Contacts repository managing wallet address book and contact information. Singleton providing contact CRUD operations with reactive state management. Exports: contactList StateFlow, updateContactInfo(), addContactList(), deleteContact(), findOrCreateContact(). Dependencies: ContactsDb for persistence, application scope coroutines. Used by: Contact management screens and transaction processing. Features reactive contact list updates, automatic refresh on database changes, alias management, and contact discovery for transactions. Bridges database layer with domain models. |

###### app/src/main/java/com/tari/android/wallet/data/contacts/obsolete/

| File | Description |
|------|-------------|
| `ContactAction.kt` | [OBSOLETE] Contact action enumeration for legacy contact management system. Defines contact operations like add, update, delete for older contact management implementation. Deprecated in favor of newer contact management architecture but maintained for migration compatibility. |
| `ContactsRepository.kt` | [OBSOLETE] Legacy contacts repository implementation for backward compatibility. Contains older contact management logic superseded by current ContactsRepository. Maintained for data migration and compatibility during transition to newer contact management system. |
| `FFIContactsRepositoryBridge.kt` | [OBSOLETE] Legacy bridge between FFI contacts and repository layer. Provided contact synchronization between native FFI contacts and Android contact storage in older architecture. Superseded by current FFI integration patterns but maintained for migration support. |
| `MergedContactsRepositoryBridge.kt` | [OBSOLETE] Legacy merged contacts repository combining multiple contact sources. Integrated FFI contacts with device contacts in older architecture. Replaced by current contact management system but maintained for compatibility and data migration purposes. |
| `PhoneBookRepositoryBridge.kt` | [OBSOLETE] Legacy phone book integration bridge for device contacts. Provided access to device contact book for contact import/export in older implementation. Superseded by current contact management but preserved for backward compatibility and migration. |

###### app/src/main/java/com/tari/android/wallet/data/contacts/obsolete/model/

| File | Description |
|------|-------------|
| `ContactDto.kt` | [OBSOLETE] Legacy contact data transfer object from older contact management system. Contains contact information structure used in previous contact implementation. Maintained for data migration and compatibility with legacy contact data formats. |
| `ContactInfo.kt` | [OBSOLETE] Legacy contact information data class from previous contact management implementation. Contains contact details and metadata used in older contact system. Preserved for backward compatibility and data migration from legacy contact storage. |

###### app/src/main/java/com/tari/android/wallet/data/network/

| File | Description |
|------|-------------|
| `NetworkConnectionState.kt` | Network connection state enumeration and data classes. Defines network connectivity states (connected, disconnected, limited) with associated metadata. Used for tracking internet connectivity and network availability throughout the wallet application. |
| `NetworkConnectionStateHandler.kt` | Reactive state manager for network connectivity monitoring. Maintains network connection state, provides Flow-based updates for network changes, handles connectivity transitions. Used by components that need to respond to network availability changes for offline/online behavior. |
| `NetworkConnectionStateReceiver.kt` | Android BroadcastReceiver for network connectivity changes. Monitors system network state changes, detects connectivity events, integrates with NetworkConnectionStateHandler. Registered in TariWalletApplication to provide real-time network state monitoring across the application. |

###### app/src/main/java/com/tari/android/wallet/data/push/

| File | Description |
|------|-------------|
| `PushRepository.kt` | Push notification registration repository handling FCM token management with cryptographic wallet integration. Exports: PushRepository singleton. Manages push token registration using wallet signatures for authentication, including public key derivation, message signing, and API integration. Uses wallet cryptographic operations for secure push notification setup with backend services. |
| `PushRetrofitService.kt` | Retrofit API service interface for push notification backend communications. Defines REST endpoints for notification registration, token management, and push notification configuration. Used by PushRepository for backend integration with notification services. |

###### app/src/main/java/com/tari/android/wallet/data/recovery/

| File | Description |
|------|-------------|
| `WalletRestorationState.kt` | Data classes and enumeration for wallet restoration progress tracking. Defines restoration states (scanning, restoring, completed) with progress indicators and metadata. Used during wallet recovery from seed phrases to track and display restoration progress to users. |
| `WalletRestorationStateHandler.kt` | Reactive state manager for wallet restoration progress tracking. Maintains restoration state during seed phrase recovery, provides progress updates, handles restoration completion. Integrates with FFI restoration callbacks to track wallet recovery operations. Central coordinator for restoration UI updates. |

###### app/src/main/java/com/tari/android/wallet/data/sharedPrefs/

| File | Description |
|------|-------------|
| `CommonPrefRepository.kt` | Common shared preferences repository providing shared preference utilities. Base class or interface for preference management with common functionality like preference delegates, serialization helpers, and shared preference operations used across specific preference repositories. |
| `CorePrefRepository.kt` | Main preferences repository aggregating all wallet settings and configuration. Singleton coordinating multiple specialized preference repositories. Exports: wallet address, emoji ID, alias, onboarding state, airdrop tokens, database passphrase generation, clear operations. Dependencies: All specialized preference repositories, shared preference delegates. Used by: Throughout app for centralized configuration access. Features wallet identity management, onboarding flow tracking, interrupted state detection, data clearing capabilities, and coordinated cleanup across all preference domains. Licensed under Tari Project BSD license. |
| `SimplePrefRepository.kt` | Simple shared preferences repository with basic key-value storage operations. Provides simplified preference access without complex data structures. Used for straightforward configuration settings and simple preference management throughout the wallet application. |

###### app/src/main/java/com/tari/android/wallet/data/sharedPrefs/addressPoisoning/

| File | Description |
|------|-------------|
| `AddressPoisoningPrefRepository.kt` | Shared preferences repository for address poisoning protection settings. Manages user preferences for address validation, suspicious address warnings, and poisoning attack detection. Stores configuration for address verification features that protect users from malicious address substitution attacks. |

###### app/src/main/java/com/tari/android/wallet/data/sharedPrefs/backup/

| File | Description |
|------|-------------|
| `BackupPrefRepository.kt` | Shared preferences repository for wallet backup settings and state. Manages backup preferences including automatic backup settings, backup locations, cloud storage configuration, backup completion tracking. Central storage for backup-related user preferences and state management. |

###### app/src/main/java/com/tari/android/wallet/data/sharedPrefs/baseNode/

| File | Description |
|------|-------------|
| `BaseNodeDto.kt` | Data transfer object for base node configuration information. Contains base node details including name, public key, network address, and connection parameters. Used for base node selection, storage, and display in base node management functionality. |
| `BaseNodePrefRepository.kt` | Shared preferences repository for base node selection and configuration. Manages current base node selection, custom base node additions, base node lists, and connection preferences. Central storage for base node configuration and user-selected connectivity preferences. |

###### app/src/main/java/com/tari/android/wallet/data/sharedPrefs/chat/

| File | Description |
|------|-------------|
| `ChatsPrefRepository.kt` | Shared preferences repository for chat and messaging preferences. Manages chat settings including message notifications, chat history preferences, conversation display options. Stores user preferences for chat functionality and messaging behavior within the wallet. |

###### app/src/main/java/com/tari/android/wallet/data/sharedPrefs/delegates/

| File | Description |
|------|-------------|
| `SerializableTime.kt` | Serializable time utility class for shared preference storage. Provides time-based data serialization for preference storage, handles time formatting and conversion for preference delegates. Used for storing time-related settings and timestamps in shared preferences. |
| `SharedPrefBooleanDelegate.kt` | Kotlin property delegate for boolean shared preferences. Provides automatic shared preference access for boolean values with getter/setter delegation. Simplifies boolean preference management with type-safe property delegation pattern used throughout preference repositories. |
| `SharedPrefBooleanNullableDelegate.kt` | Kotlin property delegate for nullable boolean shared preferences. Provides automatic shared preference access for nullable boolean values, handling null states and default values. Used for optional boolean settings in preference repositories. |
| `SharedPrefGsonDelegate.kt` | Kotlin property delegate for complex object shared preferences using Gson serialization. Automatically handles serialization/deserialization of complex objects to/from JSON in shared preferences. Enables storing custom data classes and complex structures in preferences. |
| `SharedPrefGsonNullableDelegate.kt` | Kotlin property delegate for nullable complex object shared preferences using Gson serialization. Handles nullable objects with automatic JSON serialization/deserialization, manages null states and default values. Used for optional complex data structures in preference repositories. |
| `SharedPrefStringDelegate.kt` | Kotlin property delegate for string shared preferences. Provides automatic shared preference access for string values with type-safe property delegation. Simplifies string preference management with getter/setter delegation pattern used throughout preference repositories. |
| `SharedPrefStringSecuredDelegate.kt` | Kotlin property delegate for encrypted string shared preferences. Provides automatic encryption/decryption for sensitive string data stored in shared preferences. Used for secure storage of sensitive configuration values and credentials within preference repositories. |
| `UriDeserializer.kt` | Custom Gson deserializer for Android Uri objects in shared preferences. Handles URI serialization/deserialization for complex preference data structures containing Uri references. Used by Gson preference delegates for URI-containing data classes. |

###### app/src/main/java/com/tari/android/wallet/data/sharedPrefs/network/

| File | Description |
|------|-------------|
| `NetworkPrefRepository.kt` | Shared preferences repository for network configuration and selection. Manages current network selection (mainnet, testnet, etc.), network switching, and network-specific preferences. Central storage for network configuration used throughout wallet for network-dependent operations. |
| `NetworkUtils.kt` | Network utility functions for network validation and operations. Provides helper functions for network configuration validation, network type detection, and network-related utility operations used by network preference management and validation. |
| `NoSupportedNetworkException.kt` | Exception class thrown when no supported network is available or configured. Used for network validation error handling when network configuration is invalid or unsupported networks are selected. Part of network configuration error handling system. |
| `TariNetwork.kt` | Tari network configuration data class with network-specific parameters. Contains network details including genesis block hash, network ID, and network-specific configuration values. Used for network-specific wallet configuration and validation throughout the application. |

###### app/src/main/java/com/tari/android/wallet/data/sharedPrefs/security/

| File | Description |
|------|-------------|
| `LoginAttemptDto.kt` | Data transfer object for tracking login attempts and authentication security. Contains login attempt metadata including timestamp, success status, and attempt details. Used for tracking authentication history and implementing security features like login attempt limits. |
| `SecurityPrefRepository.kt` | Security and authentication preferences repository managing PIN codes, biometrics, and login attempts. Singleton providing secure storage for authentication data. Exports: authentication state, PIN code, biometrics settings, database passphrase, login attempt tracking. Dependencies: Secured string delegates, GSON delegate for complex objects. Used by: Authentication system throughout the app. Features encrypted PIN storage, biometric authentication flags, login attempt tracking with automatic reset on success, and database passphrase management. Critical for wallet security infrastructure. |

###### app/src/main/java/com/tari/android/wallet/data/sharedPrefs/securityStages/

| File | Description |
|------|-------------|
| `SecurityStagesPrefRepository.kt` | Shared preferences repository for security stage management during wallet setup. Tracks completion of security setup stages including biometric enrollment, PIN creation, backup verification. Manages progressive security configuration flow and stage persistence during onboarding process. |
| `WalletSecurityStage.kt` | Enumeration of wallet security setup stages during onboarding. Defines security stages like biometric setup, PIN creation, seed phrase backup, security verification. Used by security stage management system to track and enforce progressive security configuration. |

###### app/src/main/java/com/tari/android/wallet/data/sharedPrefs/sentry/

| File | Description |
|------|-------------|
| `SentryPrefRepository.kt` | Shared preferences repository for Sentry crash reporting configuration. Manages Sentry settings including crash reporting enablement, user consent, error tracking preferences. Stores user preferences for crash analytics and error reporting functionality. |

###### app/src/main/java/com/tari/android/wallet/data/sharedPrefs/tariSettings/

| File | Description |
|------|-------------|
| `TariSettingsPrefRepository.kt` | Shared preferences repository for Tari-specific wallet settings and configurations. Manages wallet-specific preferences including fee settings, privacy options, display preferences, and advanced wallet configuration options. Central storage for user-customizable wallet behavior. |

###### app/src/main/java/com/tari/android/wallet/data/sharedPrefs/tor/

| File | Description |
|------|-------------|
| `TorBridgeConfiguration.kt` | Configuration data class for Tor bridge settings used in privacy mode. Contains bridge relay information, connection parameters, and Tor routing configuration. Used for configuring Tor network connectivity when privacy mode is enabled in the wallet. |
| `TorPrefRepository.kt` | Shared preferences repository for Tor network configuration and privacy settings. Manages Tor enablement, bridge configuration, privacy mode settings, and Tor connection preferences. Central storage for privacy-related network configuration and user privacy preferences. |

###### app/src/main/java/com/tari/android/wallet/data/sharedPrefs/yat/

| File | Description |
|------|-------------|
| `YatPrefRepository.kt` | Shared preferences repository for YAT (Your Address Token) integration settings. Manages YAT connection status, emoji address mappings, YAT service configuration, and user YAT preferences. Central storage for YAT-related user data and configuration settings. |

###### app/src/main/java/com/tari/android/wallet/data/tx/

| File | Description |
|------|-------------|
| `TxDto.kt` | Data transfer object for transaction information. Contains transaction data including amount, fee, recipient, status, timestamp, and transaction metadata. Base transaction data structure used throughout the wallet for transaction display and management operations. |
| `TxListData.kt` | Transaction list data structure for UI display. Contains grouped transaction data, list metadata, pagination information, and transaction categorization for transaction history display. Used by transaction list UI components for organized transaction presentation. |
| `TxRepository.kt` | Transaction repository managing wallet transaction data with reactive state management. Singleton providing transaction list operations and wallet event integration. Exports: txs StateFlow, txsInitialized StateFlow, findTxById(). Dependencies: ContactsRepository, WalletManager, FFI wallet. Used by: Transaction screens and balance management. Features automatic refresh on wallet events, contact integration for transaction display, comprehensive transaction type handling (completed/pending/cancelled), and reactive updates on contact changes. Critical bridge between FFI wallet data and UI layer. |

###### app/src/main/java/com/tari/android/wallet/di/

| File | Description |
|------|-------------|
| `ApplicationComponent.kt` | Primary Dagger dependency injection component defining all injection points for the application. Exports: ApplicationComponent interface with 190+ lines of injection declarations. Provides singleton scoped dependencies and defines injection methods for 50+ activities, fragments, services, and ViewModels. Integrates multiple DI modules (ApplicationModule, RetrofitModule, ViewModelModule) for comprehensive dependency graph management. |
| `ApplicationModule.kt` | Core Dagger module providing fundamental Android dependencies and system services. Exports: ApplicationModule with @Provides methods for Context, SharedPreferences, system services, and security components. Includes BiometricAuthenticationService, ClipboardManager, NotificationHelper, and ResourceManager providers. Defines singleton scoped core dependencies used throughout the application dependency graph. |
| `CoroutinesDispatchersModule.kt` | Dagger 2 module providing Kotlin Coroutines dispatchers for dependency injection. Configures IO, Main, Default, and custom dispatchers for coroutine operations. Enables proper coroutine context management and threading throughout the application. |
| `DiContainer.kt` | Dependency injection container managing Dagger 2 component lifecycle. Initializes application component, provides global access to dependency injection, manages component creation and destruction. Central DI coordination point used by TariWalletApplication. |
| `RetrofitModule.kt` | Dagger 2 module configuring Retrofit HTTP client dependencies. Provides Retrofit instances, OkHttp configuration, HTTP interceptors, and API service interfaces. Configures network communication layer for backend API integration. |
| `TorModule.kt` | Dagger 2 module providing Tor network integration dependencies. Configures Tor proxy settings, Tor service connections, privacy network components for anonymous networking. Enables privacy-focused network communication through Tor integration. |

###### app/src/main/java/com/tari/android/wallet/ffi/

| File | Description |
|------|-------------|
| `Base58String.kt` | Base58 string encoding/decoding utilities for cryptocurrency addresses and keys. Provides Base58 conversion functions used for wallet address representation, public key encoding, and cryptographic data display in human-readable format. |
| `FFIBalance.kt` | FFI wrapper for wallet balance operations. Provides Kotlin interface to native balance functions including available balance, pending balance, time-locked balance queries. Core component for balance management and display throughout the wallet. |
| `FFIBase.kt` | Base class for all FFI native peer entities providing memory management and pointer lifecycle. Abstract base with pointer tracking and automatic cleanup via finalize(). Exports: FFIBase abstract class, FFIIterableBase for collections, FFIPointer typealias, nullptr constant, isNull() extension. Dependencies: Native pointer management. Used by: All FFI wrapper classes throughout the application. Features automatic memory management, null pointer checking, and iterable collection support. Essential for preventing memory leaks in JNI bridge. Licensed under Tari Project BSD license. |
| `FFIByteVector.kt` | FFI wrapper for native byte vector operations. Provides Kotlin interface to native byte array functions, handles memory management for binary data exchange between Kotlin and native layers. Used throughout FFI for binary data serialization and cryptographic operations. |
| `FFICommsConfig.kt` | FFI wrapper for communications configuration objects. Provides Kotlin interface to native communication settings including transport protocols, network parameters, connection configuration. Used for configuring wallet network communication and peer connectivity. |
| `FFICompletedTx.kt` | FFI wrapper for completed transaction objects. Provides Kotlin interface to native completed transaction data including amount, fee, status, timestamps, transaction details. Core component for accessing finalized transaction information from native wallet layer. |
| `FFICompletedTxKernel.kt` | FFI wrapper for completed transaction kernel data. Provides access to transaction kernel cryptographic proof including excess, signature, and verification data. Used for transaction validation and blockchain proof verification in the native layer. |
| `FFICompletedTxs.kt` | FFI wrapper for completed transactions collection. Provides Kotlin interface to native completed transaction lists, handles iteration and access to multiple completed transactions. Used for transaction history retrieval and bulk transaction operations. |
| `FFIContact.kt` | FFI wrapper for contact objects in native wallet. Provides Kotlin interface to native contact data including public key, emoji ID, alias. Core component for contact management operations and integration between Kotlin contact system and native wallet contacts. |
| `FFIContacts.kt` | FFI wrapper for contacts collection in native wallet. Provides Kotlin interface to native contact lists, handles iteration and access to multiple contacts. Used for bulk contact operations and contact list management from native wallet layer. |
| `FFIEmojiSet.kt` | FFI wrapper for emoji set operations used in Tari emoji ID system. Provides Kotlin interface to native emoji character mapping and validation functions. Core component for converting between public keys and human-readable emoji addresses in Tari protocol. |
| `FFIError.kt` | FFI error handling and error code definitions for native layer communication. Defines error types, error codes, and error message handling for FFI operations. Provides standardized error handling across all FFI wrapper classes and native library integration. |
| `FFIException.kt` | FFI error handling and exception management for native library interactions. Provides error checking utilities and exception wrapping for FFI operations. Exports: FFIException class, throwIf() utility, runWithError() wrapper function. Dependencies: FFIError, WalletError enums. Used by: All FFI operations requiring error handling. Features automatic error code checking, exception throwing on errors, and safe execution wrapper. Critical for handling native library errors gracefully in Kotlin code. Licensed under Tari Project BSD license. |
| `FFIExtensions.kt` | Kotlin extension functions for FFI objects providing convenience methods and utility operations. Adds helper functions, conversion utilities, and enhanced functionality to FFI wrapper classes. Improves FFI object usability and provides Kotlin-idiomatic interfaces. |
| `FFIFeePerGramStat.kt` | FFI wrapper for individual fee-per-gram statistics from native wallet. Provides access to single fee statistic data points for transaction fee analysis. Used for fee estimation and transaction cost optimization in the wallet. |
| `FFIFeePerGramStats.kt` | FFI wrapper for fee-per-gram statistics collection from native wallet. Provides access to aggregated fee statistics for transaction fee estimation and optimization. Core component for dynamic fee calculation based on network conditions. |
| `FFIPendingInboundTx.kt` | FFI wrapper for pending inbound transaction objects from native wallet. Provides Kotlin interface to incoming transaction data before completion including sender information, amount, and transaction status. Used for monitoring and managing pending incoming transactions. |
| `FFIPendingInboundTxs.kt` | FFI wrapper for pending inbound transactions collection from native wallet. Provides Kotlin interface to native pending inbound transaction lists, handles iteration and access to multiple pending incoming transactions. Used for bulk pending transaction operations and monitoring. |
| `FFIPendingOutboundTx.kt` | FFI wrapper for pending outbound transaction objects from native wallet. Provides Kotlin interface to outgoing transaction data before completion including recipient information, amount, fee, and transaction status. Used for tracking sent transactions through completion stages. |
| `FFIPendingOutboundTxs.kt` | FFI wrapper for pending outbound transactions collection from native wallet. Provides Kotlin interface to native pending outbound transaction lists, handles iteration and access to multiple pending outgoing transactions. Used for bulk pending transaction management and status monitoring. |
| `FFIPrivateKey.kt` | FFI wrapper for cryptographic private key operations from native wallet. Provides Kotlin interface to native private key functions including key generation, serialization, signing operations, and secure memory management. Critical for wallet security and transaction signing. |
| `FFIPublicKey.kt` | FFI wrapper for cryptographic public key operations from native wallet. Provides Kotlin interface to native public key functions including key validation, serialization, verification operations, and address generation. Used for contact identification and transaction verification. |
| `FFIPublicKeys.kt` | FFI wrapper for public keys collection from native wallet. Provides Kotlin interface to native public key lists, handles iteration and access to multiple public keys. Used for bulk public key operations and contact management from native layer. |
| `FFISeedWords.kt` | FFI wrapper for BIP39 seed phrase operations from native wallet. Provides Kotlin interface to native seed phrase functions including mnemonic generation, validation, entropy conversion, and wallet recovery operations. Critical for wallet backup and restoration functionality. |
| `FFITariBaseNodeState.kt` | FFI wrapper for base node state information from native wallet. Provides Kotlin interface to native base node connection status, sync state, chain height, and network information. Used for monitoring blockchain synchronization progress and connectivity health. |
| `FFITariCoinPreview.kt` | FFI wrapper for coin preview functionality from native wallet. Provides Kotlin interface to coin selection preview, UTXO analysis, and fee estimation before transaction commitment. Used for transaction fee calculation and coin selection optimization. |
| `FFITariPaymentRecord.kt` | FFI wrapper class for Tari payment record data from native wallet library. Extends FFIBase for pointer management. Exports: FFITariPaymentRecord class with payment fields. Dependencies: FFIBase, FFIPointer for native interop. Used by: TariPaymentRecord model class constructor. Contains fields for payment reference (ByteArray), amount, block height, mined timestamp, and direction. Calls native jniLoadData() to populate fields from C++ layer. |
| `FFITariPaymentRecords.kt` | FFI wrapper class for a collection of Tari payment records from native wallet library. Extends FFIIterableBase for iterable collection functionality. Exports: FFITariPaymentRecords class with iteration methods. Dependencies: FFIIterableBase, FFITariPaymentRecord, FFIPointer, FFIError. Used by: Payment history retrieval and processing. Implements getLength() and getAt() methods for accessing individual payment records by index, with native JNI calls for data retrieval and memory management. |
| `FFITariTransportConfig.kt` | FFI wrapper for Tari transport configuration from native wallet. Provides Kotlin interface to native transport protocol settings, connection parameters, encryption options, and network routing configuration for peer-to-peer communication. |
| `FFITariTypeTag.kt` | FFI wrapper for Tari type tag enumeration from native wallet. Provides type identification and validation for various Tari protocol objects. Used for type safety and object categorization in native layer communications and validation operations. |
| `FFITariUnblindedOutput.kt` | FFI wrapper for unblinded output operations from native wallet. Provides Kotlin interface to UTXO unblinding, value extraction, and ownership verification. Critical for transaction privacy and confidential transaction handling in the Tari protocol. |
| `FFITariUnblindedOutputs.kt` | FFI wrapper for unblinded outputs collection from native wallet. Provides Kotlin interface to native unblinded output lists, handles iteration and access to multiple unblinded outputs. Used for bulk UTXO operations and transaction processing. |
| `FFITariUtxo.kt` | FFI wrapper for UTXO (Unspent Transaction Output) management from native wallet. Provides Kotlin interface to UTXO creation, validation, spending, and tracking operations. Core component for transaction input/output handling and balance management. |
| `FFITariVector.kt` | FFI wrapper for generic vector collections from native wallet. Provides Kotlin interface to dynamic array operations, iteration, and memory management for collections of native objects. Used throughout FFI layer for handling lists of various wallet objects. |
| `FFITariWalletAddress.kt` | FFI wrapper for Tari wallet address operations from native wallet. Provides Kotlin interface to wallet address generation, validation, encoding/decoding, and emoji ID conversion. Critical for wallet addressing and transaction recipient identification. |
| `FFITransactionSendStatus.kt` | FFI wrapper for transaction send status tracking from native wallet. Provides Kotlin interface to transaction state enumeration (pending, broadcasting, confirmed, rejected) and status monitoring. Used for tracking outbound transaction progress through network states. |
| `FFITxBase.kt` | FFI base class for transaction objects from native wallet. Provides common transaction properties and operations shared across different transaction types (pending, completed, cancelled). Foundation for transaction hierarchy in FFI layer. |
| `FFITxCancellationReason.kt` | FFI enumeration for transaction cancellation reasons from native wallet. Defines reasons for transaction cancellation including user cancellation, network errors, insufficient funds, timeout. Used for transaction failure analysis and error reporting. |
| `FFITxStatus.kt` | FFI enumeration for transaction status from native wallet. Defines transaction states including pending, broadcast, mined, confirmed, rejected, cancelled. Core component for transaction lifecycle tracking and status display in wallet UI. |
| `FFIWallet.kt` | Main FFI wallet wrapper providing Kotlin interface to native Rust wallet library. Comprehensive wallet functionality including transaction management, balance operations, mining, UTXO handling, and network communication. Exports: All core wallet operations (sendTari, getBalance, importUtxos, syncWallet, etc.), callback registration, and lifecycle management. Dependencies: Native libminotari_wallet_ffi.a, all transaction FFI types, WalletCallbacks. Used by: WalletManager as primary wallet interface. Features complete transaction lifecycle, balance tracking, contact management, sync status, and callback-based event handling. Licensed under Tari Project BSD license. Critical component bridging Android app to Rust wallet core. |
| `HexString.kt` | Hexadecimal string utility functions for cryptographic data representation. Provides hex string validation, conversion to/from byte arrays, string formatting for displaying cryptographic hashes, public keys, and transaction IDs. Used throughout wallet for data display and validation. |
| `NetAddressString.kt` | Network address string parsing and validation utilities. Provides IP address parsing, port extraction, network address formatting, address type detection for peer discovery and base node connections. Used for network configuration and peer connectivity validation. |
| `TransactionValidationStatus.kt` | Enumeration for transaction validation status from native wallet. Defines validation states including valid, invalid, timeout, orphaned for transaction verification processes. Used for transaction validation feedback and error handling in wallet operations. |

###### app/src/main/java/com/tari/android/wallet/infrastructure/

| File | Description |
|------|-------------|
| `ShareManager.kt` | Utility manager for sharing functionality including QR codes, wallet addresses, transaction details, and backup files. Handles Android sharing intents, file sharing, clipboard operations, and social media integration. Provides centralized sharing capabilities throughout the wallet application. |

###### app/src/main/java/com/tari/android/wallet/infrastructure/backup/

| File | Description |
|------|-------------|
| `BackupAndRestoreException.kt` | Exception class for backup and restore operation errors. Defines specific error types for backup failures, restore failures, file corruption, network issues, and validation errors. Used throughout backup system for structured error handling and user feedback. |
| `BackupFileProcessor.kt` | File processing utilities for wallet backup operations. Handles backup file creation, encryption, compression, validation, and integrity checking. Manages backup file formats, metadata, and security for wallet backup and restoration processes. |
| `BackupManager.kt` | Central manager for wallet backup and restoration operations. Orchestrates backup creation, cloud storage integration, automatic backup scheduling, backup validation, and restore processes. Coordinates with cloud storage services (Google Drive) and manages backup lifecycle and user preferences. |
| `BackupNamingPolicy.kt` | Naming policy and conventions for wallet backup files. Defines backup file naming patterns, timestamp formatting, version numbering, and file organization strategies. Ensures consistent backup file naming across different storage locations and backup types. |
| `BackupState.kt` | Data classes and enumeration for backup operation states. Defines backup states including idle, backing up, completed, failed with associated progress information and error details. Used for tracking backup progress and providing user feedback during backup operations. |
| `BackupStateHandler.kt` | Reactive state manager for backup operations progress tracking. Maintains backup state during backup and restore operations, provides progress updates, handles backup completion and error states. Integrates with backup services to track operation status and provide UI feedback. |
| `BackupStorage.kt` | Abstract interface for backup storage implementations. Defines backup storage operations including save, retrieve, delete, and list backups. Implemented by various storage providers (local storage, Google Drive) for pluggable backup storage architecture. |
| `BackupUtxos.kt` | UTXO backup functionality for wallet backup operations. Handles backup and restoration of unspent transaction outputs, manages UTXO serialization, encryption, and validation during backup processes. Critical for complete wallet state recovery. |

###### app/src/main/java/com/tari/android/wallet/infrastructure/backup/compress/

| File | Description |
|------|-------------|
| `CompressionMethod.kt` | Interface defining compression methods for backup file compression. Provides abstraction for different compression algorithms used in backup operations. Enables pluggable compression strategies for optimizing backup file size and performance. |
| `ZipCompression.kt` | ZIP compression implementation for backup file compression. Provides ZIP-based compression and decompression for wallet backup files. Handles compression settings, file organization within ZIP archives, and integrity validation for compressed backups. |

###### app/src/main/java/com/tari/android/wallet/infrastructure/backup/googleDrive/

| File | Description |
|------|-------------|
| `GoogleDriveBackupStorage.kt` | Google Drive cloud storage implementation for wallet backups. Provides Google Drive API integration for backup upload, download, synchronization, and management. Handles authentication, file organization, conflict resolution, and cloud backup lifecycle management. |

###### app/src/main/java/com/tari/android/wallet/infrastructure/backup/local/

| File | Description |
|------|-------------|
| `LocalBackupStorage.kt` | Local device storage implementation for wallet backups. Provides local file system storage for backup files including backup creation, retrieval, organization, and cleanup. Manages local backup directory structure and backup file lifecycle on device storage. |

###### app/src/main/java/com/tari/android/wallet/infrastructure/logging/

| File | Description |
|------|-------------|
| `BugReportingService.kt` | Bug reporting service integrating with Sentry for crash reporting and log collection. Singleton providing log aggregation and user feedback submission. Exports: share() method for bug report creation with logs. Dependencies: Sentry SDK, WalletConfig, CorePrefRepository. Used by: Bug reporting screens and error handling. Features automatic log file collection, zip compression for efficient transfer, Sentry event creation with attachments, user feedback integration, and comprehensive error handling for file operations. Licensed under Tari Project BSD license. Critical for debugging and user support. |
| `FFIFileAdapter.kt` | Logging adapter for FFI layer file-based logging. Routes native library log messages to file storage, manages log file rotation, formatting, and persistence. Bridges native wallet logging with Android file system logging for debugging FFI operations. |
| `LoggerAdapter.kt` | Central logging adapter managing multiple logging destinations. Coordinates between console logging, file logging, Sentry logging, and FFI logging. Provides unified logging interface and manages log level configuration throughout the wallet application. Used by TariWalletApplication for logging setup. |
| `LoggerTags.kt` | Centralized logger tag constants for consistent logging throughout the wallet. Defines standardized log tags for different components including FFI, network, security, UI, transactions. Enables structured logging and easy log filtering for debugging and monitoring. |
| `SentryException.kt` | Custom exception class for Sentry-specific error reporting. Wraps exceptions with additional context for crash analytics, provides structured error information for debugging. Used for capturing and reporting structured error data to Sentry monitoring service. |
| `SentryLogAdapter.kt` | Sentry logging adapter for crash reporting and error monitoring. Routes application logs and exceptions to Sentry service, manages crash context, handles error categorization. Integrates with LoggerAdapter for centralized error reporting and monitoring. |

###### app/src/main/java/com/tari/android/wallet/infrastructure/permission/

| File | Description |
|------|-------------|
| `PermissionManager.kt` | Android permission management utility for handling runtime permissions. Manages permission requests, permission status checking, permission rationale handling for camera, contacts, storage, and other wallet features. Provides centralized permission handling throughout the application. |

###### app/src/main/java/com/tari/android/wallet/infrastructure/security/biometric/

| File | Description |
|------|-------------|
| `BiometricAuthenticationException.kt` | Exception class for biometric authentication errors. Defines specific error types for biometric failures including hardware unavailable, user cancellation, authentication failed, lockout. Used for structured error handling in biometric authentication flows. |
| `BiometricAuthenticationService.kt` | Biometric authentication service providing fingerprint, face, and device lock authentication. Exports: authenticate() methods for Fragment/Activity, authentication type detection, device security checks. Dependencies: AndroidX Biometric, KeyguardManager. Used by: Authentication flows throughout the app. Features authentication type detection (biometric/PIN/none), device security validation, suspend function support for coroutines, BiometricManager integration with weak authenticator support, and comprehensive callback handling. Licensed under Tari Project BSD license. Essential for secure wallet access. |
| `BiometricAuthenticationType.kt` | Enumeration for biometric authentication types supported by the wallet. Defines fingerprint, face recognition, iris, and other biometric modalities. Used for biometric capability detection and authentication method selection in security settings. |
| `ContinuationAdapter.kt` | Adapter for integrating biometric authentication with Kotlin coroutines. Provides coroutine-based biometric authentication API, handles suspending function integration with biometric callbacks. Enables modern async programming patterns for authentication flows. |
| `SingleSuccessOrErrorAdapterDecorator.kt` | Decorator for biometric authentication adapters ensuring single success or error responses. Prevents multiple callback invocations, handles authentication state management, provides consistent authentication result handling. Used for reliable biometric authentication flow control. |

###### app/src/main/java/com/tari/android/wallet/infrastructure/security/encryption/

| File | Description |
|------|-------------|
| `SymmetricEncryptionAlgorithm.kt` | Symmetric encryption algorithm interface for wallet data encryption. Defines encryption and decryption operations for sensitive data including seed phrases, private keys, and backup files. Provides abstraction for different encryption algorithms used in wallet security. |

###### app/src/main/java/com/tari/android/wallet/model/

| File | Description |
|------|-------------|
| `BalanceInfo.kt` | Core data model representing wallet balance information. Contains available balance, pending incoming balance, pending outgoing balance, and time-locked balance in MicroTari units. Central balance data structure used throughout wallet for balance display and calculations. Used by BalanceStateHandler for reactive balance updates. |
| `CompletedTransactionKernel.kt` | Data model for completed transaction kernel containing cryptographic proof data. Includes transaction excess, signature, and verification information for blockchain validation. Represents the cryptographic kernel that proves transaction validity in the Tari protocol. |
| `CoreError.kt` | Core error model defining wallet error types and error handling structures. Provides standardized error representation for wallet operations including FFI errors, network errors, validation errors. Used throughout wallet for consistent error handling and user feedback. |
| `MicroTari.kt` | Fundamental data model representing Tari currency amounts in micro-Tari units. Provides value class for precise currency calculations, formatting, conversion between different Tari denominations. Core currency representation used throughout wallet for all amount calculations and display. Ensures precision and prevents rounding errors in financial calculations. |
| `PublicKey.kt` | Core data model representing cryptographic public keys in the wallet. Contains public key data, validation, serialization, and formatting functionality. Used for wallet addresses, contact identification, transaction verification, and emoji ID generation. Central cryptographic identifier throughout the Tari protocol. |
| `TariBaseNodeState.kt` | Data model representing the state of a Tari base node connection. Exports: TariBaseNodeState data class. Contains heightOfLongestChain (BigInteger) and nodeId (String) properties. Includes constructor from FFITariBaseNodeState for FFI bridge integration. Used by wallet connection status tracking and synchronization components. Implements Parcelable for intent serialization. |
| `TariCoinPreview.kt` | Data model for previewing Tari coin transaction details before execution. Exports: TariCoinPreview data class. Contains vector (TariVector) and feeValue (MicroTari) properties. Includes constructor from FFITariCoinPreview for native library integration. Used by transaction preview and fee estimation components. Implements Parcelable for activity state preservation. |
| `TariContact.kt` | Core contact data model representing a wallet user with alias. Exports: TariContact data class. Contains walletAddress (TariWalletAddress), alias (String), and isFavorite (Boolean) properties. Includes constructor from FFIContact for native library bridge. Used throughout contact management, transaction sending, and UI display components. Implements Parcelable for intent passing and state preservation. |
| `TariPaymentRecord.kt` | Data model class representing a Tari payment record with payment reference, amount, block height, and mined timestamp. Implements Parcelable for Android bundle serialization. Exports: TariPaymentRecord data class, Direction enum (Inbound/Outbound/Change). Dependencies: FFITariPaymentRecord for FFI interop, toHex extension function. Used by: Payment history UI components, transaction processing. Includes constructor to convert from FFI object and Direction enum with companion object for value conversion. |
| `TariUnblindedOutput.kt` | Data model for unblinded transaction outputs in the Tari protocol. Exports: TariUnblindedOutput data class. Contains json (String) property storing the serialized output data. Includes constructor from FFITariUnblindedOutput for native library integration. Used by transaction processing and UTXO management components. Implements Parcelable for cross-activity communication. |
| `TariUtxo.kt` | UTXO (Unspent Transaction Output) data model for the Tari blockchain. Exports: TariUtxo data class, UtxoStatus enum. Contains commitment, value, minedHeight, timestamp, lockHeight, and status properties. Includes comprehensive UtxoStatus enum with 12 different states (Unspent, Spent, Encumbered variants, etc.). Used by wallet balance calculations, transaction building, and UTXO management UI. Implements Parcelable and includes FFI constructor. |
| `TariVector.kt` | Vector data structure wrapper for collections of Tari blockchain objects from native library. Exports: TariVector data class. Contains len, cap, itemsList (List<TariUtxo>), and longs (List<Long>) properties. Maps FFITariVector collections to Kotlin collections. Used by transaction building, UTXO selection, and batch operations. Implements Parcelable for state persistence. |
| `TariWalletAddress.kt` | Core wallet address model representing Tari network addresses with comprehensive validation and formatting. Exports: TariWalletAddress data class, Network enum, Feature enum. Contains network, features, emoji components, base58 representation, and address type flags. Includes static factory methods for address creation from base58/emoji strings, validation utilities, and network detection. Used throughout the app for address display, validation, transaction targeting, and contact management. Implements advanced equality comparison for payment ID addresses. |
| `TransactionSendStatus.kt` | Transaction send result status model indicating how a transaction was transmitted. Exports: TransactionSendStatus class, Status enum. Contains status property and convenience methods isSuccess, isDirectSend, isSafSend, isQueued. Status enum includes Queued, DirectSendSafSend, DirectSend, SafSend, Invalid states. Used by transaction sending flow to determine delivery method success and provide user feedback. |
| `TxStatus.kt` | Comprehensive transaction status enumeration mapping all possible transaction states in the Tari protocol. Exports: TxStatus enum with 14 states including COMPLETED, BROADCAST, MINED_CONFIRMED, PENDING, COINBASE variants, ONE_SIDED variants, etc. Includes utility methods isOneSided() and isCoinbase() for status classification. Maps from FFITxStatus for native library integration. Used throughout transaction display, filtering, and state management components. |
| `TypeAlias.kt` | Type aliases for common Tari wallet data types to improve code readability and consistency. Exports: TxId (BigInteger), EmojiId (String), Base58 (String) type aliases. Used throughout the codebase to provide semantic meaning to primitive types and ensure consistent usage of transaction IDs, emoji addresses, and base58 encoded strings. |
| `WalletError.kt` | Comprehensive wallet error handling system with predefined error codes and exception management. Exports: WalletError data class, WalletException class, throwIf() function. Contains standard error codes (114-702) for database, transactions, contacts, passphrases, seed words, and recovery issues. Includes constructors for FFI errors, exceptions, and general throwables. Used throughout the app for consistent error handling, user feedback, and debugging. Extends CoreError and implements Parcelable. |

###### app/src/main/java/com/tari/android/wallet/model/seedPhrase/

| File | Description |
|------|-------------|
| `SeedPhrase.kt` | Seed phrase creation and validation utility for wallet recovery. Exports: SeedPhrase class, SeedPhraseCreationResult sealed class. Manages 24-word seed phrase validation with FFISeedWords integration. Includes detailed result types (Success, Failed, InvalidSeedPhrase, etc.) for word-by-word validation. Used by wallet restoration, backup creation, and seed phrase verification flows. Provides both strict and nullable validation methods. |
| `SeedWordsWordPushResult.kt` | Enumeration for seed phrase word validation results during restoration process. Exports: SeedWordsWordPushResult enum with 4 states. Contains InvalidSeedWord, SuccessfulPush, SeedPhraseComplete, and InvalidSeedPhrase values. Used by SeedPhrase class during word-by-word validation. Includes fromInt() companion method for FFI integration. |

###### app/src/main/java/com/tari/android/wallet/model/tx/

| File | Description |
|------|-------------|
| `CancelledTx.kt` | Transaction model for cancelled transactions with cancellation reason tracking. Exports: CancelledTx data class extending Tx. Contains fee and cancellationReason (FFITxCancellationReason) properties in addition to base transaction fields. Includes constructor from FFICompletedTx for native library integration. Used by transaction history display and cancellation handling. Implements Parcelable for activity state management. |
| `CompletedTx.kt` | Transaction model for successfully completed transactions with full blockchain confirmation details. Exports: CompletedTx data class extending Tx. Contains fee, confirmationCount, txKernel (CompletedTransactionKernel), minedTimestamp, and minedHeight properties. Includes comprehensive constructor from FFICompletedTx with transaction kernel extraction and error handling. Used by transaction history, transaction details, and confirmation tracking. Represents final state transactions with complete blockchain metadata. |
| `PendingInboundTx.kt` | Transaction model for incoming transactions awaiting confirmation. Exports: PendingInboundTx data class extending Tx. Contains standard transaction properties without fee (not applicable for received transactions). Includes dual constructors from FFICompletedTx and FFIPendingInboundTx for different FFI scenarios. Used by transaction monitoring and pending transaction displays. Implements Parcelable for cross-activity communication. |
| `PendingOutboundTx.kt` | Transaction model for outgoing transactions awaiting confirmation. Exports: PendingOutboundTx data class extending Tx. Contains fee property in addition to base transaction fields. Includes dual constructors from FFICompletedTx and FFIPendingOutboundTx for different FFI integration scenarios. Used by send transaction flow, pending transaction monitoring, and fee display. Implements Parcelable for state preservation. |
| `Tx.kt` | Abstract base class for all transaction types in the Tari wallet. Exports: Tx abstract class, Direction enum. Contains common transaction properties: id, direction, amount, timestamp, paymentId, status, and tariContact. Includes convenience properties for transaction classification (isOneSided, isCoinbase, isInbound, isOutbound) and DateTime conversion. Extended by CompletedTx, PendingInboundTx, PendingOutboundTx, and CancelledTx subclasses. Used throughout transaction display and management components. |

###### app/src/main/java/com/tari/android/wallet/navigation/

| File | Description |
|------|-------------|
| `Navigation.kt` | Navigation command sealed class hierarchy defining all possible navigation actions in the wallet. Exports: Navigation sealed class with nested sealed classes for each feature area (Auth, Restore, TxList, ContactBook, AllSettings, etc.). Contains 50+ navigation commands covering authentication, transactions, contacts, settings, backup, chat, and onboarding flows. Used by TariNavigator to route between activities and fragments. Includes data parameters for passing context between screens. |
| `TariNavigator.kt` | Central navigation controller implementing type-safe navigation throughout the wallet application. Exports: TariNavigator singleton class. Contains 270+ lines of navigation logic mapping Navigation commands to actual UI transitions. Handles activity launches, fragment transactions, external intents, and navigation parameters. Integrates with YatAdapter for external wallet connections. Used by all ViewModels to perform navigation actions. Maintains currentActivity reference for context-dependent navigation. |

###### app/src/main/java/com/tari/android/wallet/notification/

| File | Description |
|------|-------------|
| `FcmHelper.kt` | Firebase Cloud Messaging token management and registration helper. Exports: FcmHelper singleton class. Manages FCM token lifecycle including retrieval, registration with push repository, and error handling. Uses coroutines for async operations and integrates with PushRepository for backend registration. Handles token refresh events and wallet-specific token registration. Used by FCM service for push notification setup. |
| `NotificationHelper.kt` | Notification management helper providing standardized notification creation and channel setup. Singleton managing Android notification system integration. Exports: createNotificationChannels(), postNotification() methods. Dependencies: Android notification system, NotificationManagerCompat. Used by: Throughout the app for user notifications. Features notification channel creation with proper importance levels, grouped notifications for transactions, permission checking for Android 13+, comprehensive error handling, and branded notification styling. Licensed under Tari Project BSD license. TODO comment indicates code review needed for unused functionality. |
| `TariFcmService.kt` | Firebase Cloud Messaging service for push notifications and token management. Extends FirebaseMessagingService with wallet integration. Exports: FCM message handling, token registration. Dependencies: WalletManager, FcmHelper, Firebase Messaging. Used by: Firebase system for push notification delivery. Features automatic token refresh handling, wallet-integrated token registration, coroutine-based async operations, proper lifecycle management with job cancellation, and comprehensive logging. Critical for wallet notifications and user engagement. |

###### app/src/main/java/com/tari/android/wallet/tor/

| File | Description |
|------|-------------|
| `NativeLoader.kt` | Native library extraction and loading utility for Tor binary deployment from APK to filesystem. Exports: NativeLoader object. Handles extraction of native .so libraries from APK archive, manages architecture detection (x86, ARM), sets file permissions for execution, and provides fallback loading mechanisms. Used by Tor proxy system to deploy native Tor binary for privacy operations. Critical for Tor functionality initialization. |
| `TorBootstrapStatus.kt` | Tor bootstrap progress tracking model parsing status from Tor logs. Exports: TorBootstrapStatus data class. Contains progress (Int), summary (String), and optional warning (String) properties. Includes regex parsing for Tor log format with progress percentages and status messages. Used by TorProxyManager and UI components to display Tor connection progress. Companion object provides log parsing with MAX_PROGRESS constant. |
| `TorConfig.kt` | Tor proxy configuration data model containing connection parameters and authentication details. Exports: TorConfig data class. Contains proxyPort, controlHost, controlPort, connectionPort, cookieFilePath, sock5Username, and sock5Password properties. Used by TorProxyManager for Tor daemon configuration and connection setup. Manages authentication credentials and network routing parameters for privacy-focused communication. |
| `TorProxyControl.kt` | Tor proxy management and monitoring controller. Singleton service that establishes connection to Tor control interface, authenticates using cookie files, monitors bootstrap status, and maintains proxy state. Dependencies: TorConfig, TorProxyStateHandler, TorControlConnection, RxJava. Exports: startMonitoringTor(), shutdownTor(). Used by: Tor integration components. Features connection retry logic, timeout handling, and periodic status checking. Critical for privacy mode functionality. |
| `TorProxyManager.kt` | Core Tor proxy management system handling installation, configuration, and lifecycle of Tor daemon. Exports: TorProxyManager singleton class. Manages Tor resource installation, process control, bootstrap monitoring, and bridge configuration. Includes torrc file generation, process execution, and log parsing for status updates. Used throughout the app for privacy-focused network communication. Integrates with TorProxyStateHandler for state management and TorResourceInstaller for asset management. |
| `TorProxyState.kt` | Sealed class hierarchy representing all possible Tor proxy connection states. Exports: TorProxyState sealed class with 5 states. Contains NotReady, ReadyForWallet, Initializing, Running, and Failed states. Integrates TorBootstrapStatus for progress tracking during initialization and running phases. Used by TorProxyStateHandler for state management and UI components for status display. Provides comprehensive state modeling for Tor connection lifecycle. |
| `TorProxyStateHandler.kt` | Reactive state management for Tor proxy lifecycle with coroutine-based state waiting mechanisms. Exports: TorProxyStateHandler singleton. Manages TorProxyState flow with update methods and specialized waiting functions (doOnTorBootstrapped, doOnTorReadyForWallet, doOnTorFailed). Uses StateFlow for reactive state updates and provides async state-dependent actions. Critical component for coordinating wallet operations with Tor availability. |
| `TorResourceInstaller.kt` | Tor asset installation and configuration system extracting resources from APK to filesystem. Exports: TorResourceInstaller class with 200+ lines managing Tor binary setup. Handles extraction of GeoIP files, torrc configuration, and native Tor binary from app assets to private directories. Manages file permissions, directory creation, and version tracking. Critical component for preparing Tor environment for proxy operations. |

###### app/src/main/java/com/tari/android/wallet/ui/common/

| File | Description |
|------|-------------|
| `ClipboardArgs.kt` | Simple data class for clipboard operations with user feedback. Exports: ClipboardArgs class. Contains clipLabel (String), clipText (String), and toastMessage (String) properties. Used by clipboard management throughout the app for copying wallet addresses, transaction IDs, and other data with appropriate user notification messages. Provides standardized clipboard interaction pattern. |
| `CommonActivity.kt` | Abstract base activity class providing common functionality for all wallet activities. Exports: CommonActivity abstract class. Manages view model binding, theme application, fragment management, dialog handling, permission management, screen capture protection, and shake detection for debug menus. Includes activity lifecycle management, navigation coordination, and security features. Used as parent for all feature activities. Integrates with CommonViewModel, DialogManager, and TariNavigator. |
| `CommonFragment.kt` | Abstract base class for all fragments in the wallet application providing common functionality. Exports: CommonFragment abstract class. Manages view model binding, dialog handling, clipboard operations, QR scanning, permissions, screen recording protection, and fragment lifecycle. Includes animation management, back press handling, and fragment popped notifications. Used as parent for all feature fragments. Integrates with CommonViewModel and DialogManager. Handles deep link processing and navigation events. |
| `CommonViewModel.kt` | Abstract base ViewModel providing shared functionality across all wallet ViewModels. Exports: CommonViewModel abstract class with 380+ lines of common logic. Manages application state, navigation commands, dialog handling, deep links, permissions, connection status, and error handling. Includes wallet integration, clipboard operations, YAT integration, and theme management. Used as parent for all feature ViewModels. Integrates with TariNavigator, DialogManager, WalletManager, and all core repositories. |
| `CommonXmlActivity.kt` | Abstract activity base class for traditional View-based (XML) activities with ViewBinding support. Exports: CommonXmlActivity abstract class extending CommonActivity. Provides ui property for ViewBinding access while maintaining all CommonActivity functionality. Used by activities that haven't migrated to Jetpack Compose. Bridges traditional Android Views with modern architecture patterns. |
| `CommonXmlFragment.kt` | Abstract fragment base class for traditional View-based (XML) fragments with ViewBinding support. Exports: CommonXmlFragment abstract class extending CommonFragment. Provides ui property for ViewBinding access while maintaining all CommonFragment functionality. Used by fragments that haven't migrated to Jetpack Compose. Part of the migration strategy from traditional Views to Compose. |
| `DialogManager.kt` | Centralized dialog management system handling modal dialog queue and lifecycle. Exports: DialogManager singleton class. Manages ModularDialog queue with replace(), dismiss(), and dismissAll() methods. Provides dialog ID-based tracking and prevents duplicate dialogs. Includes convenience methods like showNotReadyYetDialog(). Used throughout the app for consistent dialog presentation and management. Handles dialog dismiss listeners and automatic cleanup. |
| `LiveDataExtension.kt` | LiveData extension providing debounce functionality for reactive UI interactions. Exports: debounce() extension function on LiveData. Uses MediatorLiveData with Handler to delay value emission by specified duration (default 1000ms). Prevents rapid UI updates and improves performance for search inputs, form validation, and user interaction handling. Essential utility for smooth user experience. |
| `SingleLiveEvent.kt` | LiveData variant ensuring single-time event consumption for UI interactions. Exports: SingleLiveEvent class extending MutableLiveData. Uses AtomicBoolean to track pending state and ensures events are only consumed once per emission. Prevents duplicate UI actions on configuration changes or multiple observers. Used for navigation commands, snackbar messages, and one-time UI events throughout the wallet. |

###### app/src/main/java/com/tari/android/wallet/ui/common/domain/

| File | Description |
|------|-------------|
| `PaletteManager.kt` | Legacy color palette management system for traditional View-based UI components. Exports: PaletteManager object with 30+ color accessor methods. Provides theme-aware color access through XML attributes for text, backgrounds, system colors, icons, and brand colors. Marked for migration to Compose color system. Used by legacy UI components that haven't migrated to TariColors. Bridges traditional theming with modern design tokens. |
| `ResourceManager.kt` | Centralized resource access manager providing type-safe resource retrieval with context encapsulation. Utility class wrapping Android resource access with convenient methods. Exports: getString(), getColor(), getDimen(), getDimenInPx(), getDrawable() methods. Dependencies: Android resources, ContextCompat. Used by: Throughout the app for resource access abstraction. Features string resource access with format arguments, color resource retrieval with proper theming, dimension access in both pixels and float values, drawable retrieval with proper compatibility, and consistent resource access patterns. Improves testability and resource management. |

###### app/src/main/java/com/tari/android/wallet/ui/common/recyclerView/

| File | Description |
|------|-------------|
| `AdapterFactory.kt` | Factory object for creating generic RecyclerView adapters. Exports: generate() method that takes ViewHolderBuilder varargs and returns CommonAdapter. Used by: RecyclerView implementations throughout the app. Provides type-safe adapter creation with multiple view holder types. Simplifies RecyclerView setup by abstracting adapter instantiation. |
| `CommonAdapter.kt` | Generic RecyclerView adapter framework supporting multiple view holder types with automatic binding. Exports: CommonAdapter abstract class extending ListAdapter. Uses ViewHolderBuilder pattern for type-safe view holder creation and dynamic view type mapping. Includes click/long-click listeners and DiffUtil integration. Foundation for all list-based UI components throughout the wallet. Supports complex heterogeneous lists with type safety. |
| `CommonViewHolder.kt` | Abstract base ViewHolder class for type-safe RecyclerView implementations with ViewBinding. Exports: CommonViewHolder abstract class. Provides ui property for ViewBinding access, item tracking, and clickView management. Includes bind() method for data binding with generic type safety. Used by all RecyclerView implementations throughout the app for consistent list item handling. |
| `CommonViewHolderItem.kt` | Abstract base class for all RecyclerView item data models with unique identification and comparison support. Exports: CommonViewHolderItem abstract class implementing Serializable. Requires viewHolderUUID for unique identification, includes equality comparison, and supports deep copying. Foundation for all list item data throughout the app enabling efficient DiffUtil operations and type-safe RecyclerView implementations. |
| `DefaultDiffCallback.kt` | Default DiffUtil.ItemCallback implementation for efficient RecyclerView updates with generic type support. Exports: DefaultDiffCallback class. Provides standard item comparison logic using viewHolderUUID for identity and equals() for content comparison. Enables smooth RecyclerView animations and optimal performance by detecting minimal changes. Used by CommonAdapter for automatic list diffing. |
| `ViewHolderBuilder.kt` | Configuration data class for dynamic ViewHolder creation in generic RecyclerView adapters. Exports: ViewHolderBuilder data class. Contains function references for view creation, item class mapping, and ViewHolder instantiation. Enables type-safe, dynamic ViewHolder factory pattern used by CommonAdapter for heterogeneous list support. Critical component for flexible RecyclerView architecture. |

###### app/src/main/java/com/tari/android/wallet/ui/common/recyclerView/items/

| File | Description |
|------|-------------|
| `DividerViewHolderItem.kt` | RecyclerView item model for visual dividers/separators. Extends CommonViewHolderItem with unique UUID identifier. Used by: Lists requiring visual separation between items. Simple data class with no properties, serves as a marker for divider rendering. Part of the common RecyclerView framework. |
| `SpaceVerticalViewHolderItem.kt` | RecyclerView item model for vertical spacing elements. Extends CommonViewHolderItem with space property (integer value for spacing amount). Exports: space property, unique UUID based on space value. Used by: Lists requiring variable vertical spacing between items. Partners with SpaceVerticalViewHolder for rendering. |
| `TitleViewHolderItem.kt` | RecyclerView item model for section titles/headers. Extends CommonViewHolderItem with title string and isFirst boolean flag. Exports: title text, isFirst positioning flag, UUID based on title. Used by: Lists requiring section headers with conditional first-item styling. Partners with TitleViewHolder for rendering. |

###### app/src/main/java/com/tari/android/wallet/ui/common/recyclerView/viewHolders/

| File | Description |
|------|-------------|
| `SpaceVerticalViewHolder.kt` | RecyclerView ViewHolder for rendering vertical spacing elements. Extends CommonViewHolder binding to ItemVerticalSpaceBinding. Exports: bind() method, getBuilder() factory method. Dependencies: SpaceVerticalViewHolderItem, dpToPx extension. Used by: Lists needing dynamic vertical spacing. Dynamically adjusts height based on item's space value in dp. Includes ViewHolderBuilder pattern for adapter integration. |
| `TitleViewHolder.kt` | RecyclerView ViewHolder for rendering section titles/headers. Extends CommonViewHolder binding to ItemTitleBinding. Exports: bind() method, getBuilder() factory method. Dependencies: TitleViewHolderItem, dimension resources, setTopMargin extension. Used by: Lists requiring section headers. Applies conditional top margin based on isFirst flag. Includes ViewHolderBuilder pattern for adapter integration. |

###### app/src/main/java/com/tari/android/wallet/ui/component/clipboardController/

| File | Description |
|------|-------------|
| `ClipboardController.kt` | Animated clipboard UI component for displaying and copying wallet addresses with visual feedback. Exports: ClipboardController class with 200+ lines of animation logic. Manages expand/collapse animations, clipboard operations, and visual states for wallet address display. Includes Easing interpolator animations and ConstraintLayout transitions. Used throughout the app for address sharing and QR code functionality. Integrates with TariWalletAddress display and user feedback systems. |
| `WalletAddressViewModel.kt` | ViewModel for discovering and validating Tari wallet addresses from clipboard and query strings. Extends CommonViewModel with ClipboardManager injection. Exports: discoveredWalletAddressFromClipboard/Query LiveEvents, tryToCheckClipboard(), checkQueryForValidEmojiId(). Dependencies: DeepLink parsing, TariWalletAddress validation. Used by: Components needing wallet address detection. Supports deep link parsing and emoji ID validation with async processing. |

###### app/src/main/java/com/tari/android/wallet/ui/component/common/

| File | Description |
|------|-------------|
| `CommonView.kt` | Abstract base class for custom view components with ViewModel integration. Generic base for views with ViewBinding and CommonViewModel. Exports: bindViewModel() method, abstract setup() and bindingInflate() methods. Dependencies: ModularDialog, lifecycle management. Used by: Custom view implementations throughout the app. Provides common patterns for dialog management, deep link handling, and lifecycle-aware ViewModel binding. |

###### app/src/main/java/com/tari/android/wallet/ui/component/loadingButton/

| File | Description |
|------|-------------|
| `LoadingButtonState.kt` | State model for loading button UI component tracking title, enable status, and loading state. Exports: LoadingButtonState data class. Contains title (String), isEnable (Boolean), and isLoading (Boolean) properties. Used by loading button components to manage visual states during async operations. Provides type-safe state management for button interactions throughout the app. |
| `LoadingSwitchView.kt` | Custom button view with integrated loading spinner functionality. Extends FrameLayout with ViewProgressButtonBinding. Exports: setState() method, setOnClickListener() override. Dependencies: LoadingButtonState, setVisible extension. Used by: Forms and actions requiring loading feedback. Manages button text, enabled state, and loading indicator visibility. Shows spinner during loading and hides button text. Licensed under Tari Project BSD license. |

###### app/src/main/java/com/tari/android/wallet/ui/component/loadingSwitch/

| File | Description |
|------|-------------|
| `TariLoadingSwitchState.kt` | Data class representing state for TariLoadingSwitchView component. Immutable state object with isChecked and isLoading boolean properties. Exports: startLoading() and stopLoading() state transition methods. Used by: TariLoadingSwitchView for state management. Follows immutable state pattern with copy-based mutations. Enables loading state overlay on switch components. |
| `TariLoadingSwitchView.kt` | Custom switch view with integrated loading spinner functionality. Extends FrameLayout with ViewProgressSwitchBinding. Exports: setState(), setOnCheckedChangeListener() methods. Dependencies: TariLoadingSwitchState, setVisible extension. Used by: Settings and toggle controls requiring loading feedback. Manages switch state, checked change events, and loading indicator visibility. Shows spinner during loading and temporarily hides switch. Licensed under Tari Project BSD license. |

###### app/src/main/java/com/tari/android/wallet/ui/component/tari/

| File | Description |
|------|-------------|
| `TariBackButton.kt` | Custom back navigation button component for Tari wallet UI. Extends FrameLayout with TariBackButtonBinding. Exports: backPressedAction customizable callback property. Dependencies: setOnThrottledClickListener extension. Used by: Navigation screens requiring back button functionality. Defaults to Activity.onBackPressed() but allows custom back navigation logic. Includes throttled click protection. |
| `TariButton.kt` | Custom button component with Tari brand typography. Extends AppCompatButton with custom font support. Dependencies: TariFont attribute parsing. Used by: Primary action buttons throughout the wallet app. Automatically applies Tari brand fonts based on XML attributes. Part of the Tari design system components. Licensed under Tari Project BSD license. |
| `TariCheckbox.kt` | Custom checkbox component with Tari branding and consistent typography. Exports: TariCheckbox class extending AppCompatCheckBox. Applies green color tint from PaletteManager and TariFont typography system. Used throughout the app for user selections and form inputs with consistent visual appearance. Integrates with Tari design system colors and fonts. |
| `TariDivider.kt` | Custom divider component with theme-aware neutral color styling. Exports: TariDivider class extending View. Automatically applies neutral secondary color from PaletteManager for consistent visual separation. Used throughout the app for content separation in lists, forms, and layout sections. Ensures consistent divider appearance across different themes. |
| `TariEditText.kt` | Custom EditText component with Tari-specific typography and styling. Exports: TariEditText class extending AppCompatEditText. Automatically applies TariFont typeface from XML attributes. Used throughout the app for consistent text input styling. Provides branded appearance for form fields, search boxes, and user input areas. Integrates with Tari design system fonts and themes. |
| `TariFont.kt` | Typography system managing Poppins font variants for consistent Tari branding. Exports: TariFont enum with 6 font weights. Maps to Poppins font family (Black/Heavy/Medium/Roman/Regular/Light) with resource file references. Includes XML attribute parsing via getFromAttributeSet() for layout integration. Used by all custom Tari UI components for consistent typography. Integrates with UIFont enum for font weight mapping. |
| `TariGradientButton.kt` | Advanced button component with gradient background and custom styling for prominent actions. Exports: TariGradientButton class extending FrameLayout. Uses ViewBinding (TariGradientButtonBinding) for complex button layout with gradient effects. Includes custom text property and styled attributes support. Used for primary call-to-action buttons throughout the wallet interface. Provides premium visual appearance for important user actions. |
| `TariIconView.kt` | Custom icon view component with Tari brand theming. Extends AppCompatImageView with automatic icon tinting. Dependencies: PaletteManager for theme colors. Used by: Icon elements throughout the wallet app. Automatically applies default icon tint color from the palette manager. Part of the Tari design system components ensuring consistent iconography. |
| `TariInput.kt` | Custom input field component with Tari brand styling and validation support. Extends FrameLayout with TariInputBinding. Exports: setText(), setIsInvalid(), setErrorText(), enabled state management, textChangedListener callback. Dependencies: PaletteManager, XML attribute parsing, setVisible extension. Used by: Forms throughout the wallet app. Features error state visualization, hint text, input type configuration, IME options, and single-line mode. Licensed under Tari Project BSD license. |
| `TariLetterSpacingSpan.kt` | Custom letter spacing span for text styling in Tari design system. Extends TypefaceSpan with letter spacing control for enhanced text presentation. Used by: Text styling throughout the app requiring custom letter spacing. Part of Tari's typography enhancement utilities for consistent text appearance. |
| `TariProgressBar.kt` | Custom progress bar component with Tari brand theming and color variants. Extends ProgressBar with automatic accent color application. Exports: setWhite(), setError() color variant methods. Dependencies: PaletteManager, setColor extension. Used by: Loading states throughout the wallet app. Provides accent, white, and error color variants for different UI contexts. Part of the Tari design system components. |
| `TariSecondaryButton.kt` | Custom secondary button component with Tari brand styling and XML attribute support. Extends FrameLayout with TariSecondaryButtonBinding. Exports: enabled state management, click listener delegation. Dependencies: XML attribute parsing, TariSecondaryButton styleable attributes. Used by: Secondary actions throughout the wallet app. Features XML-configurable title and enabled state with proper delegation to internal button. Licensed under Tari Project BSD license. |
| `TariSeekbar.kt` | Custom seekbar component with Tari brand theming. Extends AppCompatSeekBar with automatic color application. Dependencies: PaletteManager for theme colors. Used by: UI screens requiring slider/progress controls. Automatically applies brand colors to thumb, progress, and background elements using text heading color from palette. Part of the Tari design system components. |
| `TariTabLayout.kt` | Custom tab layout component with Tari brand typography. Extends Material TabLayout with automatic font application to all tabs. Exports: Tab management with font override methods. Dependencies: TariFont, Material TabLayout. Used by: Screens requiring tabbed navigation. Features automatic Tari Heavy font application to all tab text, overrides all addTab methods to ensure consistent typography, and direct TextView manipulation for font styling. Part of the Tari design system components. |
| `TariTertiaryDivider.kt` | Tertiary divider component for Tari design system with specific styling. Custom View providing consistent tertiary-level visual separation between UI elements. Used by: UI screens requiring tertiary visual separation. Part of the Tari design system hierarchy for dividers and separators. |
| `TariTextView.kt` | Custom text view component with Tari brand typography. Extends AppCompatTextView with automatic font application. Dependencies: TariFont attribute parsing. Used by: Text elements throughout the wallet app. Automatically applies Tari brand fonts based on XML attributes. Core component of the Tari design system ensuring consistent typography. Licensed under Tari Project BSD license. |
| `TariTypefaceSpan.kt` | Custom typeface span for creating partial bold/styled spannable strings with Tari fonts. Extends TypefaceSpan with custom font application for text styling. Exports: TariTypefaceSpan class with custom typeface support. Used by: Spannable text creation throughout the app. Features custom typeface application to text spans, fake bold and italic support when needed, proper paint state management for drawing and measuring, and comprehensive style preservation. Licensed under Tari Project BSD license. Essential for rich text formatting with brand fonts. |
| `UIFont.kt` | Font weight enumeration providing mapping between UI font declarations and TariFont resources. Exports: UIFont enum with 6 weight variants. Maps Black, Heavy, Medium, Regular, Light, and Roman weights to corresponding TariFont values. Includes toTariFont() conversion method. Used by font attribute parsing and UI component font assignment for consistent typography system. |

###### app/src/main/java/com/tari/android/wallet/ui/component/tari/background/

| File | Description |
|------|-------------|
| `TariAlphaBackground.kt` | Alpha background component with transparency support for Tari design system. Extends TariBackground with alpha/transparency styling capabilities. Dependencies: TariBackground base class, PaletteManager. Used by: UI elements requiring semi-transparent backgrounds. Features XML-configurable transparency levels and inheritance of TariBackground shape and elevation functionality. |
| `TariBackground.kt` | Abstract base class for Tari background components with radius, elevation, and color support. Extends FrameLayout providing common background styling functionality. Exports: setFullBack(), restoreFullBack(), reset() methods. Dependencies: PaletteManager, Android outline/shadow APIs. Used by: All Tari background component implementations. Features rounded corners with configurable radius, elevation with shadow support (Android P+), color theming, and proper parent clipping. Core component for consistent background styling across the design system. |
| `TariPrimaryBackground.kt` | Primary background component with Tari brand styling and XML attribute support. Extends TariBackground with automatic primary color application. Dependencies: TariBackground base class, PaletteManager, XML attributes. Used by: UI elements requiring primary background styling. Features XML-configurable corner radius and elevation, automatic primary background color application from palette, and inheritance of all TariBackground functionality including shadows and shape management. |
| `TariQrBackground.kt` | QR code specialized background component for Tari design system. Extends TariBackground with QR code display styling optimization. Dependencies: TariBackground base class, PaletteManager. Used by: QR code display screens and components. Features specialized styling for QR code presentation with appropriate contrast and visual treatment. |
| `TariRoundBackground.kt` | Round background component with circular styling for Tari design system. Extends TariBackground with circular shape optimization and rounded corner management. Dependencies: TariBackground base class, PaletteManager. Used by: Circular UI elements like profile pictures, buttons, badges. Features circular shape styling with automatic radius calculation and proper corner handling. |
| `TariSecondaryBackground.kt` | Secondary background component with secondary color theming for Tari design system. Extends TariBackground with automatic secondary color application. Dependencies: TariBackground base class, PaletteManager. Used by: UI elements requiring secondary background styling. Features XML-configurable properties and secondary color theming from palette manager. |
| `TariSwitchedBackground.kt` | Switchable background component supporting multiple state styling for Tari design system. Extends TariBackground with state-based background switching capabilities. Dependencies: TariBackground base class. Used by: Interactive UI elements requiring state-based background changes. Features dynamic background styling based on component state. |

###### app/src/main/java/com/tari/android/wallet/ui/component/tari/background/obsolete/

| File | Description |
|------|-------------|
| `TariAlphaBackgroundConstraint.kt` | [OBSOLETE] Legacy alpha background component with ConstraintLayout support for Tari design system. Deprecated background component maintained for backward compatibility. Dependencies: ConstraintLayout, legacy TariBackground patterns. Used by: Legacy screens during migration. Features ConstraintLayout-specific background styling, marked as obsolete for future removal during design system modernization. |
| `TariBackgroundConstraint.kt` | [OBSOLETE] Legacy base background component with ConstraintLayout support for Tari design system. Deprecated base background component with ConstraintLayout integration. Dependencies: ConstraintLayout, legacy background patterns. Used by: Legacy screens during migration. Features ConstraintLayout-specific background functionality, marked as obsolete for future removal during design system modernization. |
| `TariPrimaryBackgroundConstraint.kt` | [OBSOLETE] Legacy primary background component with ConstraintLayout support for Tari design system. Deprecated primary background with ConstraintLayout integration. Dependencies: ConstraintLayout, legacy TariBackground patterns. Used by: Legacy screens during migration. Features ConstraintLayout-specific primary background styling, marked as obsolete for future removal during design system modernization. |
| `TariQrBackgroundConstraint.kt` | [DEPRECATED] Legacy QR code background component extending ConstraintLayout. Creates a themed background with corner radius and elevation for QR code displays using PaletteManager.getBackgroundQr(). Superseded by TariQrBackground component. Uses TariQrBackground_cornerRadius and TariQrBackground_elevation attributes. Part of obsolete View-based design system being migrated to Compose. |
| `TariRoundBackgroundConstraint.kt` | [DEPRECATED] Legacy rounded background component extending ConstraintLayout with Tari copyright. Applies custom styling with configurable corner radius, elevation, and background color using TariRound styleable attributes. Superseded by TariRoundBackground component. Part of obsolete View-based design system being phased out in favor of Compose components. |
| `TariSecondaryBackgroundConstraint.kt` | [DEPRECATED] Legacy secondary background component extending ConstraintLayout. Creates secondary themed background using PaletteManager.getBackgroundSecondary() with configurable corner radius and elevation via TariSecondaryBackground styleable attributes. Superseded by TariSecondaryBackground component. Part of obsolete View-based design system migration to Compose. |

###### app/src/main/java/com/tari/android/wallet/ui/component/tari/toolbar/

| File | Description |
|------|-------------|
| `TariToolbar.kt` | Custom toolbar component with configurable left/right actions and title. Extends FrameLayout with TariToolbarBinding. Exports: setText(), setRightArgs(), setLeftArgs(), setOnBackPressedAction(), action visibility controls. Dependencies: TariToolbarActionArg, TariToolbarActionView, extension utilities. Used by: Screens requiring standardized toolbar functionality. Features dynamic action configuration, smart margin calculation, back button default, XML attribute support for title and root mode, and responsive layout adjustments based on action count. |
| `TariToolbarActionArg.kt` | Data class defining configuration for toolbar action elements. Supports multiple action types: icon resource, drawable resource, text title, or back navigation. Includes disabled state flag and optional click action callback. Used by TariToolbarActionView to determine rendering mode and behavior. Part of Tari's modular toolbar system. |
| `TariToolbarActionView.kt` | Custom toolbar action view component for Tari design system. FrameLayout-based view that renders toolbar actions with text, icons, or back navigation based on TariToolbarActionArg configuration. Supports disabled states, throttled click handling, and flexible rendering modes (title, drawable, icon, or back button). Uses ViewTariToolbarActionBinding for layout inflation. Part of View-based toolbar component system. |

###### app/src/main/java/com/tari/android/wallet/ui/compose/

| File | Description |
|------|-------------|
| `PreviewStub.kt` | Jetpack Compose preview utilities for Tari design system. Provides PreviewPrimarySurface and PreviewSecondarySurface composables that wrap content in themed surfaces with proper background colors. Accepts TariTheme parameter for theming and ColumnScope content builders. Used for consistent preview rendering during Compose UI development and testing. |
| `TariColors.kt` | Comprehensive Jetpack Compose color system defining Tari's design system palette. Exports: TariColors data class, LocalTariColors composition local, TariLightColorPalette, TariDarkColorPalette. Contains 50+ semantic color tokens covering text, primary/secondary, success/error/warning/info, backgrounds, actions, buttons, and system states. Includes transaction-specific colors (green/yellow/red) and component-specific colors. Used by Compose UI components for consistent theming across light and dark modes. |
| `TariDesignSystem.kt` | Jetpack Compose design system implementation providing comprehensive theming for Tari wallet UI. Exports: TariDesignSystem composable with theme management. Integrates TariColors, TariTextStyles, and MaterialTheme for complete design system. Handles light/dark theme switching and app-based theme detection. Contains comprehensive text style definitions with Poppins font family integration. Used as root composable for all Compose UI components to ensure consistent styling. |
| `TariShapes.kt` | Compose shape definitions for Tari design system. Immutable data class defining consistent corner radius and shape patterns. Exports: TariShapes data class, LocalTariShapes CompositionLocal. Used by: Compose components for consistent shape styling. Defines shapes for cards (16dp), buttons (circular), start mining button (10dp), bottom menu/sheet with top corners only, and chips (10dp). Part of the unified Tari design system. |
| `TariTextStyles.kt` | Compose text styles definition for Tari design system. Immutable data class defining typography hierarchy with heading styles, body text, modal titles, button text, and link styles. Exports: TariTextStyles data class, LocalTariTextStyles CompositionLocal, PoppinsFontFamily with complete font weight variants. Dependencies: Compose text system, Poppins font resources. Used by: Compose UI components for consistent typography. Includes complete Poppins font family with all weights and styles. |

###### app/src/main/java/com/tari/android/wallet/ui/compose/components/

| File | Description |
|------|-------------|
| `TariButtons.kt` | Compose button components for Tari design system. Complete button library including TariPrimaryButton, TariSecondaryButton, TariOutlinedButton, TariInheritTextButton, and TariTextButton. Exports: Button composables, TariButtonSize enum, WithRippleColor utility. Dependencies: Material3, TariDesignSystem colors/shapes/typography. Used by: Compose screens requiring standardized buttons. Features size variants (Small/Medium/Large), enabled/disabled states, warning colors, custom ripple effects, and comprehensive preview implementation. |
| `TariDivider.kt` | Compose divider components for Tari design system. Provides standardized horizontal and vertical dividers with consistent theming. Exports: TariHorizontalDivider, TariVerticalDivider composables. Dependencies: Material3 dividers, TariDesignSystem colors. Used by: Compose screens requiring visual separation. Both dividers use 1dp thickness and design system divider color for consistency across the app. |
| `TariGradient.kt` | Compose gradient utility component for creating vertical gradients with Tari design system. Exports: TariVerticalGradient composable with configurable from/to colors. Dependencies: Compose foundation, Brush API. Used by: Compose screens requiring gradient backgrounds. Simple wrapper around Compose gradient functionality providing consistent gradient implementation across the app. |
| `TariLoadingLayout.kt` | Comprehensive loading layout system for Compose with error, loading, and content states. Exports: TariLoadingLayout, TariErrorView, TariErrorWarningView, TariProgressView composables, TariLoadingLayoutState sealed interface. Dependencies: Material3, TariDesignSystem, TariPrimaryButton. Used by: Screens requiring state management for async operations. Features crossfade animations, customizable error layouts, progress indicators, try-again functionality, and comprehensive preview implementations. Includes content size animation and state-based UI switching. |
| `TariModalBottomSheet.kt` | Compose modal bottom sheet components with Tari design system styling and animation support. Exports: TariModalBottomSheet, TariModalImageBottomSheet composables with dismiss animations. Dependencies: Material3, TariDesignSystem, coroutines. Used by: Screens requiring modal bottom sheet interactions. Features animated dismiss with state management, custom theming with shadow and shape, transparent container with scrim overlay, image variant with top image overlay support, and comprehensive preview implementations. Includes proper coroutine scope handling for animations. |
| `TariPullToRefreshBox.kt` | Jetpack Compose pull-to-refresh component for Tari design system. Wraps Material3's PullToRefreshBox with automatic loading state management (1-second minimum duration). Uses ExperimentalMaterial3Api features with rememberPullToRefreshState for state management. Provides onPullToRefresh callback and BoxScope content builder. Part of Compose migration from View system. |
| `TariSearchField.kt` | Compose search field component with Tari design system styling. Exports: TariSearchField composable with search/clear functionality. Dependencies: Material3, TariDesignSystem. Used by: Screens requiring search functionality. Features search/clear icons, hint text, transparent indicators, custom theming, and comprehensive preview implementations. Includes query change callbacks and automatic clear button when text is present. |
| `TariTopBar.kt` | Compose top bar component for Tari design system with optional back navigation and shadow. Exports: TariTopBar composable with title, back action, and shadow configuration. Dependencies: Material3, TariDesignSystem theming. Used by: Compose screens requiring standardized top navigation. Features responsive padding based on back button presence, configurable shadow elevation, centered title text with smart horizontal padding, and comprehensive preview implementations including long title handling. |

###### app/src/main/java/com/tari/android/wallet/ui/compose/widgets/

| File | Description |
|------|-------------|
| `DotsAnimation.kt` | Animated loading dots widget for Compose with staggered fade animation. Exports: DotsAnimation composable with customizable appearance. Dependencies: Compose animation, infinite transition. Used by: Loading states in Compose screens. Features three dots with staggered opacity animation, infinite repeat with reverse mode, configurable timing and spacing, and comprehensive light/dark theme previews. Provides smooth loading indication for async operations. |
| `StartMiningButton.kt` | Specialized mining status button widget with state-based styling and gradient effects. Exports: StartMiningButton composable with mining state visualization. Dependencies: TariDesignSystem, Compose graphics, gradient brushes. Used by: Home screen for mining controls. Features state-based rendering (mining/not mining), custom gradient backgrounds with radial and horizontal gradients, border styling with themed colors (green for mining, red for not mining), and comprehensive preview implementations. Provides clear visual feedback for mining status. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/

| File | Description |
|------|-------------|
| `SharedPrefBigIntegerDelegate.kt` | Property delegate for value change observation with before/after callbacks. Generic delegate class supporting text and tile change listeners. Exports: ChangedPropertyDelegate class with configurable change listeners. Used by: Components requiring property change observation. Features separate before/after change callbacks for text and tile changes, allowing reactive UI updates when property values change. Useful for form validation and UI synchronization. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/confirm/

| File | Description |
|------|-------------|
| `ConfirmDialogArgs.kt` | Configuration class for confirmation dialogs with modular dialog integration. Provides builder pattern for confirmation dialogs with custom base node support. Exports: ConfirmDialogArgs class, getModular() factory methods. Dependencies: ModularDialogArgs, dialog modules, ResourceManager. Used by: Confirmation scenarios throughout the app. Features configurable titles/descriptions, button text customization, cancellation behavior, callback actions, and specialized base node configuration support. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/error/

| File | Description |
|------|-------------|
| `WalletErrorArgs.kt` | Wallet error dialog configuration class providing localized error messages and modular dialog integration. Exports: WalletErrorArgs with error mapping and dialog factory methods. Dependencies: CoreError, WalletError, ResourceManager, SimpleDialogArgs. Used by: Error handling throughout the app. Features comprehensive error code mapping to localized messages, constructor overload for exception handling, error signature extraction for titles, and automatic ModularDialog creation for consistent error presentation. Critical for user-friendly error reporting. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/

| File | Description |
|------|-------------|
| `DialogArgs.kt` | Basic dialog configuration data class for ModularDialog system. Exports: DialogArgs with cancellation behavior and dismiss callback. Used by: ModularDialogArgs and dialog creation throughout the app. Simple configuration object defining dialog cancelable state, touch-outside dismissal behavior, and dismiss action callback. Foundation for all modular dialog configurations. |
| `IDialogModule.kt` | Abstract base class for modular dialog components. Exports: dismissAction property for dialog dismissal. Used by: All dialog module implementations in the modular dialog system. Simple interface providing common dismiss functionality that gets injected by the ModularDialog when modules are added. Essential part of the flexible dialog architecture. |
| `InputModularDialog.kt` | Specialized modular dialog for input operations extending base ModularDialog. Configures dialog for keyboard input with SOFT_INPUT_STATE_VISIBLE, removes elevation and margins from root background, and clears focus flags. Inherits modular dialog architecture allowing flexible module composition. Used for forms and text input scenarios requiring keyboard interaction. |
| `ModularDialog.kt` | Flexible modular dialog system supporting composable UI modules. Manages dialog lifecycle, animations, and module rendering. Exports: show(), dismiss(), addDismissListener(), applyArgs() methods. Dependencies: All dialog module types, ValueAnimator, WeakReference for memory safety. Used by: Throughout the app for complex dialog scenarios. Features bottom slide animation, module type factory pattern, weak context references for memory safety, and supports 20+ different module types including buttons, inputs, images, custom content. Includes TODO comment about potential memory leaks from uncleaned listeners. |
| `ModularDialogArgs.kt` | Data class defining arguments for ModularDialog system with dialog identification. Exports: ModularDialogArgs data class, DialogId constants for predefined dialogs. Dependencies: DialogArgs, IDialogModule list. Used by: ModularDialog creation throughout the app. Features unique dialog ID system for preventing duplicate instances, predefined IDs for common dialogs (connection status, debug menu, screen recording, deeplink handling), and module list configuration. |
| `SimpleDialogArgs.kt` | Simple dialog configuration data class for basic dialogs with icon, title, description, and close button. Converts to ModularDialogArgs with ImageModule, HeadModule, BodyModule, and ButtonModule composition. Supports configurable close button text, cancelable behavior, and onClose callback. Used as shorthand for common dialog patterns without manual module assembly. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/modules/addressDetails/

| File | Description |
|------|-------------|
| `AddressDetailsModule.kt` | Dialog module for displaying detailed Tari wallet address information. Implements IDialogModule interface with TariWalletAddress data and copy action callbacks for Base58 and emoji formats. Part of modular dialog system for flexible address detail presentation. Used in address verification and sharing workflows. |
| `AddressDetailsModuleView.kt` | View implementation for AddressDetailsModule displaying comprehensive wallet address information. Shows network name with emoji, features (one-sided/interactive/payment ID), view key and spend key emojis, checksum emoji, and copy buttons for Base58/emoji formats. Handles network-specific display formatting and feature translation. Extends CommonView with DialogModuleAddressDetailsBinding. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/modules/addressPoisoning/

| File | Description |
|------|-------------|
| `AddressPoisoningModule.kt` | Dialog module for address poisoning protection displaying similar addresses for user selection. Extends IDialogModule with address validation functionality. Exports: address items list, selected address tracking, trust marking flag. Dependencies: SimilarAddressDto, SimilarAddressItem. Used by: ModularDialog for address poisoning warnings. Features automatic selection of first address, similarity mapping, trust flag for whitelisting, and address selection management. Critical for wallet security against address poisoning attacks. |
| `AddressPoisoningModuleView.kt` | View implementation for address poisoning detection module. Displays list of similar addresses using SimilarAddressListAdapter with LinearLayoutManager. Includes trusted checkbox for marking selected address as trusted and handles item selection/deselection logic. Shows warning about address poisoning with count of similar addresses. Extends CommonView for address security validation workflows. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/modules/addressPoisoning/adapter/

| File | Description |
|------|-------------|
| `SimilarAddressItem.kt` | RecyclerView item data class for similar address display in address poisoning detection. Wraps SimilarAddressDto with additional UI state (lastItem flag, selected state). Provides computed properties: contactName, numberOfTransaction, lastTransactionDate, and trusted status. Uses fullBase58 address as unique identifier for ViewHolder UUID. Part of address security validation UI. |
| `SimilarAddressItemViewHolder.kt` | RecyclerView ViewHolder for displaying similar addresses in address poisoning detection. Handles both selected and unselected states with different layouts, displays emoji ID with proper styling, contact name, transaction count, last transaction date (formatted as "dd MMM yyyy"), and trusted status icon. Uses EmojiUtil for emoji ID formatting with color theming. Extends CommonViewHolder with ViewHolderBuilder pattern. |
| `SimilarAddressListAdapter.kt` | RecyclerView adapter for similar address list in address poisoning detection module. Simple CommonAdapter implementation that uses SimilarAddressItemViewHolder for rendering items. Provides the adapter infrastructure for displaying potentially poisoned addresses during transaction validation workflows. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/modules/body/

| File | Description |
|------|-------------|
| `BodyModule.kt` | Dialog module for body text content supporting plain text and spannable strings. Extends IDialogModule with text content options. Exports: BodyModule with text/textSpannable properties. Used by: ModularDialog for body content display. Simple content module supporting both plain strings and formatted SpannableString for rich text display. Essential component for dialog content presentation. |
| `BodyModuleView.kt` | View implementation for BodyModule in modular dialog system. Simple view that displays body text content, supporting both plain text and SpannableString formats. Extends CommonView with DialogModuleBodyBinding for consistent dialog body rendering. Used for description text in dialog compositions throughout the app. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/modules/button/

| File | Description |
|------|-------------|
| `ButtonModule.kt` | Dialog module for button actions with styling and click handling. Extends IDialogModule with button configuration. Exports: ButtonModule with text, style, and action properties. Dependencies: ButtonStyle enum. Used by: ModularDialog for action buttons. Simple button module supporting different visual styles and click callbacks. Essential component for dialog user interactions and action triggers. |
| `ButtonModuleView.kt` | View implementation for ButtonModule in modular dialog system. Renders dialog buttons with three styles: Normal (gradient background), Warning (destructive red background), Close (transparent background). Supports throttled click handling, automatic dismiss for Close buttons, and themed styling using PaletteManager colors. Extends CommonView with multiple constructor overloads. |
| `ButtonStyle.kt` | Enum defining visual styles for dialog buttons in modular dialog system. Three styles: Normal (default gradient), Warning (destructive/danger actions), and Close (dismiss actions). Used by ButtonModuleView to apply appropriate theming and background styling. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/modules/checked/

| File | Description |
|------|-------------|
| `CheckedModule.kt` | Dialog module for checkbox/switch input in modular dialog system. Simple IDialogModule implementation with text label and boolean checked state. Used for toggleable options in dialogs like settings, confirmations, or feature flags. State is mutable and managed by the module instance. |
| `CheckedModuleView.kt` | View implementation for CheckedModule displaying a labeled switch/checkbox control. Binds module text to title label and manages switch state with change listener that updates the module's isChecked property. Extends CommonView with DialogModuleCheckedBinding for consistent dialog checkbox rendering. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/modules/connection/

| File | Description |
|------|-------------|
| `ConnectionStatusesModule.kt` | Dialog module for displaying comprehensive network connection status. Maps ConnectionState to UI resources for network, Tor proxy, base node, and sync states. Provides text and icon resources for each connection component with color-coded status indicators (green=connected, yellow=connecting, red=disconnected). Includes Tor bootstrap progress, chain tip synchronization status, and base node ID display. Used in connection status dialogs. |
| `ConnectionStatusesModuleView.kt` | View implementation for ConnectionStatusesModule displaying network diagnostics. Shows WiFi/network status, Tor proxy status with bootstrap percentage, base node connection, sync state, chain tip progress, and base node ID. Uses color-coded status icons and handles chain tip synchronization warnings with red highlighting when wallet height doesn't match chain tip. Extends CommonView with comprehensive connection monitoring UI. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/modules/customBaseNodeBody/

| File | Description |
|------|-------------|
| `CustomBaseNodeBodyModule.kt` | Dialog module for displaying custom base node information in modular dialogs. Simple IDialogModule wrapper for BaseNodeDto containing base node configuration details. Used in custom base node selection and configuration dialogs to present node information to users. |
| `CustomBaseNodeBodyModuleView.kt` | View implementation for CustomBaseNodeBodyModule displaying base node details. Shows formatted base node information with name and peer address (publicKeyHex::address format). Uses HtmlHelper for rich text formatting and home_custom_base_node_full_description string resource. Extends CommonView for consistent dialog presentation. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/modules/head/

| File | Description |
|------|-------------|
| `HeadBoldSpannableModule.kt` | Dialog module for header with bold text highlighting using string resources. Takes regularTitlePart and boldTitlePart as string resource IDs with optional subtitle. Used for dialog headers requiring emphasis on specific text portions. Part of spannable text formatting system in modular dialogs. |
| `HeadBoldSpannableModuleView.kt` | View implementation for HeadBoldSpannableModule creating styled header text. Combines regular and bold text parts using TariFont.LIGHT and TariFont.BLACK fonts with applyFontStyle extension. Creates SpannableString with font weight differences for emphasis. Extends CommonView with DialogModuleHeadBinding for consistent header rendering. |
| `HeadModule.kt` | Dialog module for header section with title, optional subtitle, and optional right action button/icon. Implements IDialogModule with configurable right button title, icon, and action callback. Computed property showRightAction determines if right button should be displayed. Used for dialog titles with optional action buttons like close, edit, or share. |
| `HeadModuleView.kt` | View implementation for HeadModule displaying dialog header with title and optional right action. Adjusts title margins when right action is present (76dp horizontal margins). Handles both text button and icon button variants with click listeners. Manages layout parameters and visibility for flexible header configurations. Extends CommonView with DialogModuleHeadBinding. |
| `HeadSpannableModule.kt` | Dialog module for header with spannable text highlighting using CharSequence. Takes regularTitlePart and highlightedTitlePart as CharSequence with optional subtitle. More flexible than HeadBoldSpannableModule as it accepts any CharSequence type. Used for dynamic text formatting in dialog headers. |
| `HeadSpannableModuleView.kt` | View implementation for HeadSpannableModule creating complex spannable header text. Uses SpannableStringBuilder with TariTypefaceSpan to combine regular and highlighted text parts. Applies TariFont.HEAVY typeface to highlighted portion with SPAN_EXCLUSIVE_EXCLUSIVE flags. Manually constructs spannable text with proper spacing and formatting. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/modules/icon/

| File | Description |
|------|-------------|
| `IconModule.kt` | Simple dialog module for displaying icons in modular dialogs. Takes drawable resource ID as parameter. Basic IDialogModule implementation for icon-only dialog content. Used for visual elements like status icons, illustrations, or decorative graphics in dialog compositions. |
| `IconModuleView.kt` | View implementation for IconModule displaying drawable icons in dialogs. Multiple constructor overloads for flexible instantiation, simple setImageResource implementation for icon display. Extends CommonView with DialogModuleIconBinding. Used for status indicators, illustrations, and visual elements in modular dialog compositions. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/modules/imageModule/

| File | Description |
|------|-------------|
| `ImageModule.kt` | Dialog module for displaying images in modular dialogs. Simple IDialogModule wrapper for drawable resource ID. Used for larger images, illustrations, or graphic content in dialog layouts. Similar to IconModule but typically for larger visual elements. |
| `ImageModuleView.kt` | View implementation for ImageModule displaying image resources in dialogs. Simple setImageResource implementation for image display using DialogModuleImageBinding. Extends CommonView for consistent dialog image rendering. Used for illustrations, graphics, and visual content in modular dialog compositions. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/modules/input/

| File | Description |
|------|-------------|
| `InputModule.kt` | Dialog module for text input in modular dialogs. Supports configurable hint text, value binding, first/last field flags for navigation, and onDoneAction callback. Open class allowing extension for specialized input types. Used for form inputs, search fields, and user text entry in dialog workflows. |
| `InputModuleView.kt` | View implementation for InputModule providing text input functionality. Handles keyboard IME actions (NEXT/DONE), automatic focus and keyboard display for first field, text change listeners, and cursor positioning. Manages input field navigation flow and keyboard interactions. Extends CommonView with DialogModuleInputBinding for dialog text input. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/modules/option/

| File | Description |
|------|-------------|
| `OptionModule.kt` | Simple dialog module for clickable option items in modular dialogs. Contains optional text and action callback. Basic building block for creating selectable options, menu items, or action choices in dialog layouts. Used for creating option lists and interactive menu systems. |
| `OptionModuleView.kt` | View implementation for OptionModule creating clickable option items. Simple text display with root view click listener that triggers module action. Extends CommonView with DialogModuleOptionBinding for consistent option item rendering in dialog lists and menus. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/modules/securityStages/

| File | Description |
|------|-------------|
| `SecurityStageHeadModule.kt` | Security stage header module for modular dialogs. Part of the modular dialog system. Exports: SecurityStageHeadModule class that extends IDialogModule. Contains emojiTitle, title strings and onboardingFlowAction callback. Used by security-related dialogs during onboarding and wallet setup stages. Provides structured header content for security progression dialogs with emoji icons and action buttons. |
| `SecurityStageHeadModuleView.kt` | View implementation for SecurityStageHeadModule in modular dialog system. Extends CommonView with DialogModuleSecurityStageTitleBinding. Sets up UI with emoji title, title text, and question mark button that triggers onboardingFlowAction. Dependencies: CommonView, SecurityStageHeadModule. Used in security-related dialogs to display stage progression headers with interactive help button. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/modules/shareOptions/

| File | Description |
|------|-------------|
| `ShareOptionsModule.kt` | Share options module for modular dialogs. Part of the modular dialog system. Exports: ShareOptionsModule class extending IDialogModule. Contains shareQr and shareDeeplink callback functions. Used in contact sharing dialogs to provide QR code and deep link sharing options. Enables flexible sharing mechanisms through the modular dialog framework. |
| `ShareOptionsModuleView.kt` | View implementation for ShareOptionsModule in modular dialog system. Extends CommonView with DialogModuleShareOptionsBinding. Creates QR code and link sharing options using ShareOptionView components. Dependencies: ShareOptionsModule, ShareOptionView, ShareType, ShareOptionArgs. Sets up two share options (QR code and link) with respective icons, titles and click handlers. Used in contact sharing dialogs. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/modules/shareQr/

| File | Description |
|------|-------------|
| `ShareQRCodeModuleView.kt` | View implementation for ShareQrCodeModule displaying QR codes in dialogs. Uses QrUtil to generate bitmap from deeplink string with wallet_info_img_qr_code_size dimensions. Displays generated QR code in ImageView for sharing workflows. Extends CommonView with DialogModuleShareQrCodeBinding for QR code presentation. |
| `ShareQrCodeModule.kt` | Dialog module for sharing QR codes in modular dialogs. Simple IDialogModule wrapper containing deeplink string for QR code generation. Used in sharing workflows where QR codes are displayed for wallet addresses, transactions, or other shareable content. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/modules/shortEmoji/

| File | Description |
|------|-------------|
| `ShortEmojiIdModule.kt` | Dialog module for displaying shortened emoji ID representation in modular dialogs. Simple IDialogModule wrapper for TariWalletAddress used to show compact emoji address format. Used in address display scenarios where space is limited or quick visual identification is needed. |
| `ShortEmojiModuleView.kt` | View implementation for ShortEmojiIdModule displaying compact emoji address format. Shows wallet address using prefix, first, and last emoji segments via addressPrefixEmojis(), addressFirstEmojis(), and addressLastEmojis() extension functions. Creates visually compact address representation for space-constrained UI scenarios. Extends CommonView with DialogModuleShortEmojiBinding. |

###### app/src/main/java/com/tari/android/wallet/ui/dialog/modular/modules/space/

| File | Description |
|------|-------------|
| `SpaceModule.kt` | Dialog module for adding vertical spacing in modular dialogs. Takes space parameter in density-independent pixels (dp). Simple IDialogModule for layout spacing and visual separation between dialog components. Used to control vertical spacing and improve dialog visual hierarchy. |
| `SpaceModuleView.kt` | View implementation for SpaceModule creating vertical spacing in dialogs. Converts dp space value to pixels and sets root view height accordingly using dpToPx and setLayoutHeight extensions. Simple spacing component for visual layout hierarchy in modular dialog compositions. Extends CommonView with DialogModuleSpaceBinding. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/

| File | Description |
|------|-------------|
| `StartActivity.kt` | Main entry point activity that routes users to appropriate screens based on app state. Extends AppCompatActivity with dependency injection. Exports: Activity lifecycle management and routing logic. Dependencies: WalletConfig, WalletManager, various preference repositories. Used by: Android OS as app launcher. Features wallet existence checks, network validation, interrupted restoration handling, and authentication state routing. Routes to OnboardingFlowActivity for new users or AuthActivity for returning users. Includes edge cases for data clearing and network changes. Licensed under Tari Project BSD license. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/auth/

| File | Description |
|------|-------------|
| `AuthActivity.kt` | Main authentication activity for wallet access control. Handles biometric and PIN authentication with EnterPinCodeFragment integration, device security checks, and fallback dialogs. Manages login attempt tracking, displays network version info, shows loading progress, and transitions to HomeActivity on successful authentication. Disables back button navigation. Extends CommonXmlActivity with AuthViewModel and handles deep link data forwarding. |
| `AuthViewModel.kt` | Authentication coordinator ViewModel managing wallet startup and version validation. Extends CommonViewModel with migration and biometric authentication support. Exports: Authentication flow coordination and error handling. Dependencies: BiometricAuthenticationService, MigrationManager. Used by: Authentication screens for wallet access. Features wallet version validation, migration handling, incompatible version dialogs, wallet deletion for corruption recovery, and automatic wallet manager startup on successful validation. Critical component for secure wallet access and version compatibility. |
| `FeatureAuthFragment.kt` | Feature-level authentication fragment for accessing protected functionality within the app. Similar to AuthActivity but designed as a fragment component. Manages biometric/PIN authentication for feature access, integrates EnterPinCodeFragment with FeatureAuth behavior, sets isFeatureAuthenticated flag on success, and displays network version info. Used for gating sensitive features without full app re-authentication. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/biometrics/

| File | Description |
|------|-------------|
| `ChangeBiometricsFragment.kt` | Fragment for enabling/disabling biometric authentication in settings. Features loading switch UI that triggers authentication flow when toggled. Handles device security validation, biometric authentication prompts, and updates biometric preferences on successful authentication. Uses TariLoadingSwitchState to show loading during authentication process. Extends CommonXmlFragment with ChangeBiometricsViewModel. |
| `ChangeBiometricsViewModel.kt` | ViewModel for biometric authentication toggle management. Maintains TariLoadingSwitchState with current biometric setting and loading status. Provides methods to start/stop authentication flow, update biometric preferences on successful authentication, and handle loading states. Injected with BiometricAuthenticationService for authentication operations. Extends CommonViewModel with dependency injection. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/chat/addChat/

| File | Description |
|------|-------------|
| `AddChatFragment.kt` | Fragment for adding new chat contacts. Extends ContactSelectionFragment with chat-specific configuration: hides first name input field, sets "Add Chat" toolbar title, and triggers AddChat effect on continue button. Simple specialization of contact selection flow for chat functionality. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/chat/chatDetails/

| File | Description |
|------|-------------|
| `ChatDetailsFragment.kt` | Chat detail screen for messaging with specific contact. Displays chat messages in RecyclerView using ChatMessageViewHolder, supports message input with send button, attach button for options menu, and shows empty state when no messages. Updates UI based on ViewModel state including contact display and message list. Factory method creates instance with TariWalletAddress parameter. |
| `ChatDetailsModel.kt` | Data model for chat details screen. Defines UiState containing wallet address, contact, and message list with computed showEmptyState property. Includes WALLET_ADDRESS constant for fragment arguments. Simple model supporting chat UI state management. |
| `ChatDetailsViewModel.kt` | ViewModel for chat details functionality. Manages chat state with contact and message list, supports sending messages (TODO: backend integration), provides options menu with Send Tari and Request Tari actions. Uses ChatsRepository and ContactsRepository for data operations. Handles navigation to send transaction screen. Maintains reactive UI state via StateFlow. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/chat/chatDetails/adapter/

| File | Description |
|------|-------------|
| `ChatMessageViewHolder.kt` | RecyclerView ViewHolder for displaying chat messages in chat details. Simple ViewHolder that binds message text from ChatMessageViewHolderItem to UI. Uses CommonViewHolder pattern with ViewHolderBuilder factory method. Part of chat message display system with ItemChatMessageItemBinding. |
| `ChatMessageViewHolderItem.kt` | RecyclerView item data class for chat messages in chat details. Contains message text and ownership flag (isMine) for display formatting. Includes constructor from ChatMessageItemDto for data conversion. Uses composite UUID with message content and ownership for ViewHolder identification. Part of chat message display system. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/chat/chatList/

| File | Description |
|------|-------------|
| `ChatListFragment.kt` | Fragment displaying list of chat conversations with contacts. Features RecyclerView with ChatItemViewHolder for chat items, empty state handling, add chat toolbar action, and chat selection handling. Uses lazy adapter initialization and CollectFlow for reactive UI updates. Navigates to individual chat details and add chat screens. |
| `ChatListModel.kt` | Data model for chat list screen. Defines UiState containing list of chat items with computed showEmpty property for empty state display. Simple model supporting chat list UI state management with reactive empty state handling. |
| `ChatListViewModel.kt` | ViewModel for chat list functionality. Manages chat list state sorted by last message date, integrates with ChatsRepository and ContactsRepository. Supports mock data for development via DebugConfig. Handles chat selection navigation and add chat functionality. Maintains reactive UI state via StateFlow with automatic chat list updates. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/chat/chatList/adapter/

| File | Description |
|------|-------------|
| `ChatItemViewHolder.kt` | RecyclerView ViewHolder for chat list items. Displays chat contact information with unread count badge, last message date, message preview, and contact alias or emoji ID. Handles contact name vs emoji ID display logic with visibility controls. Uses CommonViewHolder pattern with ViewHolderBuilder factory for chat list display. |
| `ChatItemViewHolderItem.kt` | RecyclerView item data class for chat list display. Contains wallet address, contact information, last message details, unread count, and online status. Constructor converts ChatItemDto and Contact to display-ready format with relative time formatting, message counting, and emoji ID extraction. Used for chat list UI representation. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/contactBook/add/

| File | Description |
|------|-------------|
| `AddContactFragment.kt` | Fragment for adding new contacts to address book. Extends ContactSelectionFragment with contact-specific configuration: shows first name input field, sets "Add Contact" toolbar title, filters for contacts without aliases, and triggers AddContact effect with entered name on continue. Specializes contact selection flow for address book management. |
| `SelectUserContactFragment.kt` | Fragment for selecting user contacts during transaction sending. Extends ContactSelectionFragment with customizations for send flow. Exports: SelectUserContactFragment with newInstance factory method. Overrides startQRCodeActivity to use TransactionSend source and goToNext for SelectUserContact effect. Configurable toolbar visibility and customized UI (hidden first name input). Used in send transaction flow for contact selection. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/contactBook/addressPoisoning/

| File | Description |
|------|-------------|
| `AddressPoisoningChecker.kt` | Address poisoning detection service for security validation. Compares wallet addresses by emoji patterns (prefix/suffix matching with MIN_SAME_CHARS threshold) to identify potentially poisoned addresses. Manages trusted contact list, analyzes transaction history for similar addresses, supports mock data for testing. Singleton service protecting against address spoofing attacks using emoji ID similarity detection. |
| `SimilarAddressDto.kt` | Data transfer object for similar address information in address poisoning detection. Contains contact details, transaction count, last transaction timestamp, and trusted status. Used by AddressPoisoningChecker for analyzing potentially poisoned addresses with transaction history and trust relationships. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/contactBook/contactSelection/

| File | Description |
|------|-------------|
| `ContactSelectionFragment.kt` | Complex contact selection fragment with emoji ID input masking, clipboard integration, and YAT support. Features sophisticated text input processing with emoji formatting, automatic separator insertion, QR scanning, clipboard monitoring, and contact search. Implements TextWatcher for real-time input validation and formatting. Supports multiple selection modes and keyboard interaction management. Base class for AddContactFragment and other contact selection workflows. |
| `ContactSelectionModel.kt` | Data model for contact selection functionality. Defines YatState with YatUser containing emoji ID and wallet address, eye toggle for showing/hiding YAT details, and computed visibility properties. Includes Effect sealed interface for UI effects: invalid emoji ID warnings, next button display, and navigation triggers. Core model supporting YAT integration and contact selection workflows. |
| `ContactSelectionViewModel.kt` | Comprehensive ViewModel for contact selection workflows with YAT integration, address validation, and poisoning detection. Handles emoji ID input processing, contact search, deeplink parsing, address poisoning checks, and multiple selection effects (AddContact, SelectUserContact, AddChat). Integrates YatAdapter for emoji-to-address resolution, clipboard monitoring, and sophisticated contact filtering. Central coordinator for contact-related user interactions. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/contactBook/contacts/

| File | Description |
|------|-------------|
| `ContactsFragment.kt` | Fragment displaying list of contacts with search functionality and pull-to-refresh. Features ContactListAdapter in RecyclerView with LinearLayoutManager, swipe refresh with themed colors, search capability, and item click handling. Open class allowing extension for specialized contact views. Observes reactive data via ViewModel with automatic UI updates and loading triggers. |
| `ContactsViewModel.kt` | ViewModel for contacts list management with search, filtering, and selection support. Manages contact display with debounced list updates, selection state tracking, and contact categorization (phone vs regular contacts). Supports both normal viewing and selection modes for sharing. Handles empty state configurations for favorites vs regular contacts. Integrates with ContactSelectionRepository for multi-contact operations. |
| `FavoritesFragment.kt` | Favorites-specific contact list fragment extending ContactsFragment. Sets favorite flag for appropriate empty state display and filtering. Simple specialization that configures the base contacts functionality for favorite contacts only. Currently has placeholder filter implementation (commented out) for favorite contact filtering. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/contactBook/contacts/adapter/

| File | Description |
|------|-------------|
| `ContactListAdapter.kt` | RecyclerView adapter for contact list display. Supports multiple ViewHolder types: TitleViewHolder, ContactItemViewHolder, SettingsTitleViewHolder, EmptyStateViewHolder, and SpaceVerticalViewHolder. Comprehensive adapter enabling flexible contact list layouts with headers, contacts, empty states, and spacing. Part of contact book infrastructure. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/contactBook/contacts/adapter/contact/

| File | Description |
|------|-------------|
| `ContactItemViewHolder.kt` | RecyclerView ViewHolder for individual contact display. Shows contact alias or emoji ID (prefix, first, last) with fallback logic, selection checkbox for multi-select mode, and star indicator for favorites. Handles address emoji formatting using extension functions. Includes commented legacy code for different contact types. Uses ViewHolderBuilder pattern for adapter integration. |
| `ContactItemViewHolderItem.kt` | RecyclerView item data class for contact list display. Contains Contact object with display flags for simple/complex view modes, selection state, and selected status. Implements proper equals/hashCode using HashcodeUtils for RecyclerView diffing. Uses wallet address fullBase58 as unique UUID. Mutable selection properties support multi-contact selection workflows. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/contactBook/contacts/adapter/emptyState/

| File | Description |
|------|-------------|
| `EmptyStateViewHolder.kt` | ViewHolder for empty state display in contact book lists. Extends CommonViewHolder with ItemContactEmptyStateBinding. Exports: EmptyStateViewHolder class with getBuilder factory method. Binds EmptyStateViewHolderItem data including title, body, image, button text and action callback. Sets up empty state UI with title, description, image, and optional action button. Used in contact lists when no contacts are available. |
| `EmptyStateViewHolderItem.kt` | Data class for empty state items in contact book recycler views. Extends CommonViewHolderItem. Exports: EmptyStateViewHolderItem data class. Contains title and body as SpannedString for rich text, image resource ID, button title string, and action callback. Uses title as viewHolderUUID for identification. Used by EmptyStateViewHolder to display empty state content in contact lists. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/contactBook/details/

| File | Description |
|------|-------------|
| `ContactDetailsFragment.kt` | Fragment for displaying detailed contact information with RecyclerView-based layout. Shows contact profile, available actions, and YAT connected wallets. Features ContactDetailsAdapter for flexible content display and reactive UI updates via StateFlow. Includes factory method for fragment creation with Contact parameter. Currently has placeholder toolbar edit functionality. |
| `ContactDetailsModel.kt` | Data model for contact details screen. Defines UiState containing Contact and optional list of YAT ConnectedWallet objects for displaying linked external wallets. Simple model supporting contact detail display with YAT wallet integration. Part of contact management system architecture. |
| `ContactDetailsViewModel.kt` | ViewModel for contact details functionality with comprehensive contact management. Handles contact editing, deletion, linking/unlinking, favorite toggling, and YAT integration. Features modular dialog system for edit/delete confirmations, YAT wallet discovery, and contact validation. Extensive commented code suggests rich feature set for contact actions. Manages state via reactive StateFlow with ContactDetailsModel. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/contactBook/details/adapter/

| File | Description |
|------|-------------|
| `ContactDetailsAdapter.kt` | RecyclerView adapter for contact details screen. Extends CommonAdapter with CommonViewHolderItem. Exports: ContactDetailsAdapter class. Configures viewHolderBuilders list with TitleViewHolder, ContactProfileViewHolder, SettingsDividerViewHolder, SettingsRowViewHolder, SettingsTitleViewHolder, SpaceVerticalViewHolder, and ContactTypeViewHolder. Used to display contact profile information and settings options in a unified adapter pattern. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/contactBook/details/adapter/contactType/

| File | Description |
|------|-------------|
| `ContactTypeViewHolder.kt` | ViewHolder for contact type display in contact details. Extends CommonViewHolder with ItemContactTypeBinding. Exports: ContactTypeViewHolder class with getBuilder factory method. Binds ContactTypeViewHolderItem data to display contact type icon and text. Used in contact details adapter to show contact type information (e.g., Tari contact, YAT contact). Part of contact book module. |
| `ContactTypeViewHolderItem.kt` | Data class for contact type items in contact details recycler view. Extends CommonViewHolderItem. Exports: ContactTypeViewHolderItem class. Contains type string and icon resource ID. Uses type as part of viewHolderUUID for identification. Used by ContactTypeViewHolder to display contact type information in contact details screens. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/contactBook/details/adapter/profile/

| File | Description |
|------|-------------|
| `ContactProfileViewHolder.kt` | ViewHolder for contact profile display in contact details. Extends CommonViewHolder with ItemContactProfileBinding. Exports: ContactProfileViewHolder class with getBuilder factory method. Displays contact alias, emoji ID, wallet address with prefix/first/last emoji parts. Includes YAT address toggling functionality (currently disabled). Handles address click events. Uses emoji address utility functions for formatting. |
| `ContactProfileViewHolderItem.kt` | Data class for contact profile items in contact details recycler view. Extends CommonViewHolderItem. Exports: ContactProfileViewHolderItem data class. Contains Contact object and onAddressClick callback. Uses contact wallet address fullBase58 as viewHolderUUID. Used by ContactProfileViewHolder to display contact profile information and handle address interaction. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/contactBook/link/

| File | Description |
|------|-------------|
| `ContactLinkFragment.kt` | Fragment for linking/managing contact connections. Extends CommonXmlFragment with FragmentContactsLinkBinding and ContactLinkViewModel. Exports: ContactLinkFragment with createFragment factory method. Uses LinkContactAdapter for recycler view, observes ViewModel uiState for UI updates. Handles contact item clicks via ContactItemViewHolderItem. Factory accepts Contact parameter for initialization. Part of contact book linking functionality. |
| `ContactLinkModel.kt` | Data model for contact linking functionality. Defines UiState with contact list and search query. Includes Effect sealed class for permission granting. Used in contact book to link Tari wallet contacts with phone contacts. Part of the contact management system. |
| `ContactLinkViewModel.kt` | ViewModel for linking Tari contacts with phone contacts. Manages UI state including contact lists and search functionality. Handles contact linking dialog flow, permission management, and success notifications. Uses ContactsRepository for contact operations. Implements search filtering and modular dialog presentation for link confirmation and success messages. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/contactBook/link/adapter/

| File | Description |
|------|-------------|
| `LinkContactAdapter.kt` | RecyclerView adapter for contact linking screen. Extends CommonAdapter to handle multiple view types: ContactItemViewHolder for contact items, ContactLinkHeaderViewHolder for search header, and EmptyStateViewHolder for empty states. Used in contact linking flow to display searchable contact list. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/contactBook/link/adapter/linkHeader/

| File | Description |
|------|-------------|
| `ContactLinkHeaderViewHolder.kt` | ViewHolder for contact link header containing search functionality and emoji ID display. Manages SearchView with query text listener, displays wallet address emoji components (prefix, first part, last part), and handles keyboard focus. Used at the top of contact linking list for search and wallet identification. |
| `ContactLinkHeaderViewHolderItem.kt` | Data item for ContactLinkHeaderViewHolder. Contains search action callback and TariWalletAddress for display. Implements CommonViewHolderItem with unique viewHolderUUID based on wallet address. Used to pass search functionality and wallet address data to the header view in contact linking. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/contactBook/root/

| File | Description |
|------|-------------|
| `ContactBookFragment.kt` | Root contact book fragment with tabbed interface and comprehensive contact management. Features ViewPager2 with Contacts/Favorites tabs, search functionality, QR scanner integration, clipboard monitoring for wallet addresses, contact selection/sharing system, and dynamic toolbar states. Manages ClipboardController for address pasting and keyboard interactions. Complex fragment coordinating multiple contact-related features. |
| `ContactBookViewModel.kt` | ViewModel for the main contact book screen. Manages contact sharing functionality with QR codes and links, handles contact selection repository, and provides wallet address sharing options. Contains share list configuration for different share types (QR code, link, BLE, NFC) and manages user interaction with contact book root features. |
| `ContactSelectionRepository.kt` | Singleton repository managing contact selection state. Tracks selected contacts, selection mode, and share availability. Provides toggle and clear functionality for multi-select operations. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/contactBook/root/share/

| File | Description |
|------|-------------|
| `ShareOptionArgs.kt` | Data class defining share option configuration. Contains share type, title, icon, selection state, and click action. Used for configuring contact sharing methods (QR, link, NFC, BLE). |
| `ShareOptionView.kt` | Custom view for share option buttons. Displays icon, title, and selection state with dynamic styling. Supports different sizes (Big/Medium) and handles click events for share functionality. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/debug/

| File | Description |
|------|-------------|
| `DebugNavigation.kt` | Enum defining navigation destinations for debug functionality. Four debug screens: Logs (log file listing), LogDetail (individual log viewing), BugReport (bug reporting interface), and SampleDesignSystem (design system showcase). Used by DebugActivity for navigation routing and development tooling workflows. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/debug/activity/

| File | Description |
|------|-------------|
| `DebugActivity.kt` | Debug activity for development and troubleshooting tools. Navigation-based activity supporting logs viewing, log detail analysis, bug reporting, and design system samples. Routes to appropriate fragments based on DebugNavigation parameter. Factory method for easy launching with navigation type. Essential debugging infrastructure for development workflow and issue investigation. |
| `DebugViewModel.kt` | Simple ViewModel for debug activity. Extends CommonViewModel with no additional functionality. Used as base ViewModel for debug screens and development tools within the application. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/debug/sampleDesign/

| File | Description |
|------|-------------|
| `SampleDesignSystemFragment.kt` | Fragment showcasing the Tari design system components. Uses Jetpack Compose to display design system elements in development/debug mode. Implements CommonFragment with SampleDesignSystemViewModel and renders SampleDesignSystemScreen within TariDesignSystem theme provider. |
| `SampleDesignSystemScreen.kt` | Compose screen showcasing Tari design system components. Displays typography samples and button variants with different sizes and states. Includes light/dark theme previews for development validation. |
| `SampleDesignSystemViewModel.kt` | ViewModel for design system showcase screen. Simple extension of CommonViewModel with no additional functionality. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/home/

| File | Description |
|------|-------------|
| `HomeActivity.kt` | Main authenticated home activity hosting the primary wallet interface. Extends CommonXmlActivity with Compose integration. Dependencies: HomeViewModel, TariDesignSystem, fragment management. Used by: Authenticated users as primary wallet interface. Features authentication checks, edge-to-edge display, Compose UI hosting with HomeScreen, deep link processing, notification permissions (Android 13+), and session validation on resume. Redirects to StartActivity if not authenticated. Manages fragment container and ViewModel lifecycle cleanup. |
| `HomeModel.kt` | Data model for main home screen navigation. Exports: HomeModel object with UiState data class and BottomMenuOption enum. UiState tracks selectedMenuItem and airdropLoggedIn status. BottomMenuOption enum defines navigation tabs: Home, Shop, Gem, Profile, Settings. Used by HomeScreen Compose UI for bottom navigation state management and tab selection. |
| `HomeScreen.kt` | Jetpack Compose screen for the main home interface. Integrates fragment container for legacy views with modern Compose UI. Handles navigation bar, status bar padding, and fragment management within Compose architecture. |
| `HomeViewModel.kt` | Main home screen coordinator ViewModel managing navigation and authentication state. Extends CommonViewModel with bottom menu navigation and authentication validation. Exports: UI state flow, authentication checks, menu item selection, navigation methods. Dependencies: ContactsRepository, AirdropRepository, Navigation system. Used by: HomeActivity for main navigation coordination. Features airdrop login tracking, wallet failure handling, authentication validation on lifecycle events, and bottom menu state management. Central coordinator for authenticated home experience. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/home/overview/

| File | Description |
|------|-------------|
| `HomeOverviewFragment.kt` | Home overview fragment displaying wallet balance, recent transactions, and primary actions. Extends CommonFragment with full Compose implementation. Dependencies: HomeOverviewViewModel, HomeOverviewScreen Compose component, TariDesignSystem. Used by: Home navigation as main dashboard screen. Features pull-to-refresh, mining controls, send/request actions, transaction list, connection status, sync dialogs, and balance information. Pure Compose UI with ViewModel binding and permission checking on view creation. |
| `HomeOverviewModel.kt` | Data model for home overview screen state. Exports: HomeOverviewModel class with UiState data class. Contains transaction list, balance information, network details (ticker, name, FFI version), connection state, mining status (active miners count, mining state), and dialog visibility flags. Includes default values for initial state. Used by HomeOverviewScreen Compose UI and related ViewModels for state management. |
| `HomeOverviewScreen.kt` | Jetpack Compose screen for home overview with wallet balance and transactions. Main composable: HomeOverviewScreen taking UiState and callback functions. Dependencies: HomeOverviewModel, various widget components (WalletBalanceCard, TxItem, ActiveMinersCard, etc.). Features pull-to-refresh, transaction list, balance cards, mining status, sync indicators, and modal dialogs. Includes three preview variants (normal, empty, loading). Primary home screen in Tari wallet app. |
| `HomeOverviewViewModel.kt` | Core home overview ViewModel managing wallet balance, transaction history, and mining functionality. Extends CommonViewModel with comprehensive wallet state management. Exports: UI state with balance info, transaction lists, mining status, connection dialogs. Dependencies: ContactsRepository, TxRepository, BalanceStateHandler, AirdropRepository, StagedWalletSecurityManager. Used by: HomeOverviewFragment for main dashboard. Features automatic data refresh (60s), transaction filtering (5 most recent), balance information dialogs, mining controls, connection status management, and permission checking. Central hub for primary wallet functionality display. |
| `StagedSecurityDelegate.kt` | Delegate managing staged wallet security prompts based on balance thresholds. Shows progressive security popups (Stage 1A, 1B, 2, 3) and navigates to appropriate backup/security settings. Handles modular dialogs for security education. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/home/overview/widget/

| File | Description |
|------|-------------|
| `ActiveMinersCard.kt` | Jetpack Compose card widget for displaying active miners information. Main composable: ActiveMinersCard. Features gradient background, loading states for miners count and mining status, error handling, TariLoadingLayout integration, and StartMiningButton component. Uses custom background with blurred ellipse effect. Dependencies: TariProgressView, TariErrorWarningView, StartMiningButton. Multiple preview variants for different states. Part of home overview screen. |
| `BalanceInfoModal.kt` | Compose modal displaying detailed balance information. Shows available, pending inbound/outbound balances with currency formatting. Includes close button and proper theming support. |
| `BlockSyncChip.kt` | Compose chip component showing blockchain synchronization status. Displays sync progress with progress bar, percentage, and status icon. Used in home overview to indicate wallet sync state. |
| `EmptyTxList.kt` | Compose widget for empty transaction list state. Shows welcome message with optional mining button. Used when user has no transactions to display in home overview. |
| `SyncSuccessModal.kt` | Compose modal showing successful wallet synchronization. Displays congratulations message with gradient background and dismiss button. Appears after blockchain sync completion. |
| `TxItem.kt` | Jetpack Compose component for displaying transaction list items. Main composable: TxItem. Features transaction direction indicators (+/-), formatted amounts with minimum rounding, time-based date formatting, contact alias or emoji ID display. Includes extension functions for DateTime formatting and TxDto message generation. Handles inbound/outbound styling with colors. Multiple preview variants. Part of home overview transaction list. |
| `VersionCodeChip.kt` | Compose chip displaying app version and connection status. Shows version number with colored connection indicator. Clickable for version details and network status information. |
| `WalletBalanceCard.kt` | Jetpack Compose card widget for displaying wallet balance. Main composable: WalletBalanceCard. Features balance visibility toggle, auto-sizing text for large amounts, total and available balance display with help button. Uses custom background image and white text styling. Dependencies: BalanceInfo, WalletConfig formatter. Includes preview variants with different balance amounts. Part of home overview screen. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/onboarding/activity/

| File | Description |
|------|-------------|
| `OnboardingFlowActivity.kt` | Main activity for wallet onboarding flow. Extends CommonXmlActivity with ActivityOnboardingFlowBinding and OnboardingFlowViewModel. Implements OnboardingFlowListener interface. Manages 3-step onboarding: Introduction, CreateWallet, LocalAuth. Handles paper wallet restoration, interrupted onboarding recovery, wallet startup, and fragment navigation with custom animations. Dependencies: WalletManager, CorePrefRepository. Entry point for new user onboarding experience. |
| `OnboardingFlowFragment.kt` | Base fragment classes for onboarding flow. Exports: OnboardingFlowXmlFragment and OnboardingFlowFragment abstract classes. Both provide onboardingListener property that safely casts activity to OnboardingFlowListener. OnboardingFlowXmlFragment extends CommonXmlFragment, OnboardingFlowFragment extends CommonFragment. Used as base classes for all onboarding-related fragments to ensure proper listener access. |
| `OnboardingFlowListener.kt` | Interface defining onboarding flow navigation callbacks. Handles transitions between authentication, wallet creation, and network selection steps. Used by OnboardingFlowActivity to coordinate flow progression. |
| `OnboardingFlowModel.kt` | Data model for onboarding flow activity. Exports: OnboardingFlowModel object with Effect sealed class. Contains ResetFlow effect for resetting onboarding progress. Simple model structure for coordinating onboarding flow state changes. Used by OnboardingFlowViewModel and OnboardingFlowActivity for flow control and reset functionality. |
| `OnboardingFlowViewModel.kt` | Onboarding flow coordinator ViewModel managing new user wallet setup process. Extends CommonViewModel with effect flow for complex navigation. Exports: Effect flow for flow control, dialog management, navigation methods. Dependencies: Navigation system, dialog modules. Used by: OnboardingFlowActivity for new user experience. Features reset flow confirmation dialogs, navigation coordination between onboarding steps, effect-based communication with UI, and final navigation to home screen upon completion. Essential for new user wallet creation journey. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/onboarding/createWallet/

| File | Description |
|------|-------------|
| `CreateWalletFragment.kt` | Fragment for wallet creation onboarding step. Handles complex animations, wallet generation progress, and user interaction. Contains extensive animation logic with easing interpolators and state management for smooth UX. |
| `CreateWalletModel.kt` | Data model for wallet creation process. Defines Effect sealed class with StartCheckmarkAnimation for coordinating creation completion animations. |
| `CreateWalletViewModel.kt` | ViewModel for wallet creation process. Manages onboarding completion state, handles continue button actions, and coordinates with wallet manager. Triggers checkmark animation effects upon successful creation. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/onboarding/inroduction/

| File | Description |
|------|-------------|
| `IntroductionFragment.kt` | Fragment for onboarding introduction screen. Uses Compose UI with state collection and effect handling. Presents wallet creation and import options to new users. |
| `IntroductionModel.kt` | Data model for introduction screen. Contains UiState with version info and network name. Defines Effect sealed class for navigation to wallet creation. |
| `IntroductionScreen.kt` | Compose screen for onboarding introduction. Features Tari branding, version display, and primary/secondary action buttons for creating or importing wallet. Includes gradient background and proper theming. |
| `IntroductionViewModel.kt` | ViewModel for introduction screen. Manages version info, network name display, and user actions. Handles wallet creation and restore navigation through effect flow pattern. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/onboarding/localAuth/

| File | Description |
|------|-------------|
| `LocalAuthFragment.kt` | Fragment for local authentication setup during onboarding. Handles biometric and PIN code configuration with animations. Manages security preferences and authentication flow completion. |
| `LocalAuthModel.kt` | Data model for local authentication setup. Defines SecureState with biometrics availability and security status flags. Contains Effect interface for authentication success events. |
| `LocalAuthViewModel.kt` | ViewModel for local authentication setup. Manages biometric service, backup manager, and secure state flow. Handles PIN creation, biometric enrollment, and onboarding completion navigation. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/pinCode/

| File | Description |
|------|-------------|
| `EnterPinCodeFragment.kt` | Fragment for PIN code entry with comprehensive security features. Handles PIN creation, confirmation, and authentication modes. Includes retry limits, timeout logic, and screen recording protection. |
| `EnterPinCodeViewModel.kt` | ViewModel for PIN code entry screen. Manages PIN creation, confirmation, and authentication flows. Handles retry attempts, timeout logic, and different PIN code behaviors with state management. |
| `PinCodeScreenBehavior.kt` | Enum defining PIN code screen behavior modes. Includes Create, CreateConfirm, ChangeNew, ChangeNewConfirm, Auth, and FeatureAuth states for different PIN code interaction scenarios. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/profile/login/

| File | Description |
|------|-------------|
| `ProfileLoginFragment.kt` | Fragment for profile login screen using Compose. Renders ProfileLoginScreen with authentication URL and connect action handling. Part of profile/airdrop connection flow. |
| `ProfileLoginScreen.kt` | Compose screen for profile authentication. Shows QR code and connect button for airdrop login. |
| `ProfileLoginViewModel.kt` | ViewModel for profile login with auth URL generation and airdrop connection handling. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/profile/profile/

| File | Description |
|------|-------------|
| `ProfileFragment.kt` | Fragment for user profile screen with Compose UI. Handles profile data display, invite link sharing, mining controls, pull-to-refresh, and connection management. Collects UI state for reactive updates. |
| `ProfileModel.kt` | Profile data model with UiState for ticker, mined Tari, user details and friends. |
| `ProfileScreen.kt` | Compose screen for user profile with mining stats, referral system and friends list. |
| `ProfileViewModel.kt` | ViewModel for profile screen managing airdrop data, mining stats and sharing functionality. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/profile/profile/widget/

| File | Description |
|------|-------------|
| `FriendListEmpty.kt` | Compose widget for empty friends list state with image and descriptive text. |
| `FriendListItem.kt` | Compose item component for displaying friend referral information with status and gems. |
| `InviteLinkCard.kt` | Compose card component for referral invite link display with sharing functionality. |
| `ProfileInfoCard.kt` | Compose card displaying profile information with user details and status. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/profile/walletInfo/

| File | Description |
|------|-------------|
| `RoundButtonWithIconView.kt` | Custom view component for round button with icon in wallet info screen. |
| `WalletInfoFragment.kt` | Fragment displaying wallet information including address, YAT, and wallet actions. |
| `WalletInfoModel.kt` | Wallet info data model with wallet address, YAT emoji ID, alias and YAT connection state. |
| `WalletInfoViewModel.kt` | ViewModel for wallet info screen managing address display, YAT integration and wallet actions. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/qr/

| File | Description |
|------|-------------|
| `QrScannerActivity.kt` | Activity for QR code scanning with camera integration and deep link processing. |
| `QrScannerModel.kt` | QR scanner data model with UiState for scan errors and Effect for scan results. |
| `QrScannerSource.kt` | Enum defining QR scanner invocation sources. Exports: QrScannerSource enum with values None, Home, AddContact, ContactBook, TransactionSend, TorBridges, PaperWallet. Used to track where QR scanning was initiated from and apply context-specific behavior. Helps QR scanner handle different data types and navigation flows based on source screen. |
| `QrScannerViewModel.kt` | ViewModel for QR scanner handling scan results, errors and deep link parsing. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/restore/activity/

| File | Description |
|------|-------------|
| `WalletRestoreActivity.kt` | Activity for wallet restoration flow with fragment navigation and error handling. |
| `WalletRestoreViewModel.kt` | ViewModel for wallet restore activity. Handles restore failures and wallet reset flow. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/restore/chooseRestoreOption/

| File | Description |
|------|-------------|
| `ChooseRestoreOptionFragment.kt` | Fragment for selecting wallet restore method from available backup options. |
| `ChooseRestoreOptionModel.kt` | Data model for wallet restore option selection with backup options and progress state. |
| `ChooseRestoreOptionViewModel.kt` | ViewModel managing restore option selection and backup method coordination. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/restore/chooseRestoreOption/option/

| File | Description |
|------|-------------|
| `RecoveryOptionView.kt` | Custom view for displaying individual recovery option with icon and description. |
| `RecoveryOptionViewModel.kt` | ViewModel for individual recovery option handling selection and state management. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/restore/enterRestorationPassword/

| File | Description |
|------|-------------|
| `EnterRestorationPasswordFragment.kt` | Fragment for entering password during wallet restoration with validation and progress. |
| `EnterRestorationPasswordState.kt` | Sealed class defining restoration password entry states: Init, RestoringInProgress, WrongPasswordError. |
| `EnterRestorationPasswordViewModel.kt` | ViewModel for restoration password entry with validation, state management and restore execution. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/restore/inputSeedWords/

| File | Description |
|------|-------------|
| `CustomBaseNodeState.kt` | State class for custom base node configuration during seed word restoration. |
| `InputSeedWordsFragment.kt` | Fragment for entering seed phrase words during wallet restoration with auto-completion. |
| `InputSeedWordsViewModel.kt` | ViewModel managing seed word input validation, suggestions and wallet restoration logic. |
| `WordItemViewModel.kt` | ViewModel for individual seed word item with validation and suggestion handling. |
| `WordTextView.kt` | Custom text view component for seed word input with validation styling. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/restore/inputSeedWords/suggestions/

| File | Description |
|------|-------------|
| `SuggestionState.kt` | State class for word suggestion system during seed phrase input. |
| `SuggestionViewHolder.kt` | ViewHolder for displaying word suggestions in RecyclerView during seed input. |
| `SuggestionViewHolderItem.kt` | Data item for suggestion ViewHolder containing word text and selection callback. |
| `SuggestionsAdapter.kt` | RecyclerView adapter for word suggestions list during seed phrase restoration. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/restore/walletRestoring/

| File | Description |
|------|-------------|
| `WalletRestoringFragment.kt` | Fragment displaying wallet restoration progress with status updates and animations. |
| `WalletRestoringViewModel.kt` | ViewModel managing wallet restoration process with progress tracking and error handling. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/send/addAmount/

| File | Description |
|------|-------------|
| `AddAmountFragment.kt` | Fragment for entering transaction amount with fee selection and validation. |
| `AddAmountModel.kt` | Data model for amount entry screen with transaction data and validation states. |
| `AddAmountViewModel.kt` | Transaction amount input ViewModel managing fee calculation and transaction preparation. Extends CommonViewModel with SavedStateHandle for navigation parameters. Exports: UI state with contact, amount, fee data, validation states. Dependencies: NetworkConnectionStateHandler, fee calculation systems. Used by: AddAmount screen in send transaction flow. Features fee calculation with multiple speed options (slow/medium/fast), amount validation, contact management, note handling, network connectivity checking, and transaction data preparation for confirmation step. Critical component for transaction creation workflow. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/send/addAmount/keyboard/

| File | Description |
|------|-------------|
| `KeyboardController.kt` | Controller for custom numeric keyboard in amount entry screens. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/send/addNote/

| File | Description |
|------|-------------|
| `AddNoteFragment.kt` | Fragment for adding optional note/message to transactions. |
| `AddNoteViewModel.kt` | ViewModel for transaction note entry with text validation and storage. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/send/amountView/

| File | Description |
|------|-------------|
| `AmountStyle.kt` | Style configuration class for amount display formatting and appearance. |
| `AmountView.kt` | Custom view component for displaying formatted transaction amounts. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/send/common/

| File | Description |
|------|-------------|
| `TransactionData.kt` | Data class holding transaction information for send flow coordination. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/send/confirm/

| File | Description |
|------|-------------|
| `ConfirmFragment.kt` | Fragment for transaction confirmation with final review before sending. |
| `ConfirmScreen.kt` | Compose screen for transaction confirmation UI with details display. |
| `ConfirmViewModel.kt` | ViewModel for transaction confirmation with validation and send execution. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/send/confirm/widget/

| File | Description |
|------|-------------|
| `SenderCard.kt` | Compose card widget displaying sender information in confirmation screen. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/send/finalize/

| File | Description |
|------|-------------|
| `FinalizeSendTxFragment.kt` | Fragment for transaction finalization with progress tracking and completion. |
| `FinalizeSendTxModel.kt` | Data model for transaction finalization with step tracking and effects. |
| `FinalizeSendTxViewModel.kt` | ViewModel managing transaction finalization process with step-by-step progress. |
| `FinalizingStepView.kt` | Custom view component displaying individual finalization steps with status. |
| `YatFinalizeSendTxActivity.kt` | YAT (Your Alias Token) integration activity for finalizing send transactions. Extends YatLibOutcomingTransactionActivity to handle YAT-specific transaction finalization. Dependencies: FinalizeSendTxViewModel, TransactionData, YAT library. Manages transaction state transitions (Complete/Failed) and handles lifecycle events including automatic cleanup on stop. Used when sending transactions through YAT service integration. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/send/receive/

| File | Description |
|------|-------------|
| `ReceiveFragment.kt` | Fragment for receiving Tari with QR code display and address sharing. |
| `ReceiveScreen.kt` | Compose screen for receiving payments with QR code and address display. |
| `ReceiveViewModel.kt` | ViewModel for receive screen with QR generation and address management. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/send/requestTari/

| File | Description |
|------|-------------|
| `RequestTariFragment.kt` | Fragment for requesting Tari payment from other users. Extends CommonXmlFragment with custom amount input keyboard and sharing functionality. Exports: newInstance(). Dependencies: RequestTariViewModel, KeyboardController, ModularDialog. Features deep link generation for payment requests, QR code sharing, and standard Android sharing. Implements amount validation and enables/disables sharing buttons based on amount input. Used in TransferFragment tab layout. |
| `RequestTariViewModel.kt` | ViewModel for RequestTariFragment handling deep link generation for payment requests. Extends CommonViewModel. Dependencies: CorePrefRepository for wallet address, DeepLink system. Exports: getDeepLink(). Generates payment request deep links containing wallet address and requested amount. Minimal logic focused on deep link creation. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/send/transfer/

| File | Description |
|------|-------------|
| `TransferFragment.kt` | Transfer fragment container with tab-based UI for Send and Request Tari operations. Uses ViewPager2 with TabLayoutMediator for tab navigation. Dependencies: TransferViewModel, SelectUserContactFragment, RequestTariFragment. Contains TransferPagerAdapter implementing FragmentStateAdapter. Tab 0: Send Tari (SelectUserContactFragment), Tab 1: Request Tari (RequestTariFragment). Updates toolbar title based on selected tab. |
| `TransferViewModel.kt` | Minimal ViewModel for TransferFragment. Extends CommonViewModel with basic dependency injection setup. Contains no additional logic or state management, serving as placeholder for potential future functionality. Used by TransferFragment as required by CommonXmlFragment architecture pattern. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/allSettings/

| File | Description |
|------|-------------|
| `AllSettingsFragment.kt` | Main settings screen fragment displaying all wallet configuration options. Extends CommonXmlFragment with AllSettingsOptionAdapter for RecyclerView. Dependencies: AllSettingsViewModel, YAT integration. Features screen recording protection, dynamic settings refresh on fragment return, YAT onboarding trigger. Observes allSettingsOptions flow for UI updates. Exports: newInstance(). Used as primary settings navigation hub. |
| `AllSettingsOptionAdapter.kt` | RecyclerView adapter for displaying various settings options in AllSettingsFragment. Extends CommonAdapter with multiple ViewHolder types: SettingsRowViewHolder, SettingsTitleViewHolder, SettingsDividerViewHolder, SettingsVersionViewHolder, MyProfileViewHolder, SettingsBackupOptionViewHolder, SpaceVerticalViewHolder. Supports heterogeneous list items for comprehensive settings display. |
| `AllSettingsViewModel.kt` | Comprehensive settings ViewModel managing all wallet configuration options. Extends CommonViewModel with extensive settings organization and navigation. Exports: Settings groups (security, backup, advanced, secondary), version info, navigation actions. Dependencies: Multiple preference repositories, BiometricAuthenticationService, Navigation system. Used by: AllSettings screen for configuration management. Features organized settings categories, backup status tracking, biometric availability detection, YAT integration status, version information with copy functionality, and navigation to specialized settings screens. Central configuration hub for wallet customization. |
| `PresentationBackupState.kt` | Data class representing wallet backup status for UI presentation. Contains BackupStateStatus enum (InProgress, Success, Warning) with optional text resource ID and color. Used by backup-related UI components to display current backup state with appropriate visual indicators and messaging. |
| `TariVersionModel.kt` | Model class for displaying Tari wallet version information. Dependencies: NetworkPrefRepository, BuildConfig. Exports versionInfo string combining network display name, version name, and build code. Format: '{Network} {VERSION_NAME} b{VERSION_CODE}'. Used in settings UI to show current wallet version and network. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/allSettings/about/

| File | Description |
|------|-------------|
| `TariAboutFragment.kt` | About screen fragment displaying Tari project information and icons. Extends CommonXmlFragment with TariIconsAdapter for displaying clickable icon list. Dependencies: TariAboutViewModel, HtmlHelper. Features HTML description with license link and recyclerview of Tari-related links/icons. Handles click navigation to external URLs and license information. |
| `TariAboutViewModel.kt` | ViewModel for TariAboutFragment managing list of Tari-related icons and URLs. Extends CommonViewModel. Exports: iconList LiveData, openAboutUrl(), openLicense(). Contains comprehensive list of TariIconViewHolderItems with icons, text, and URL resources for various Tari features (backup, privacy, themes, etc.). Handles external URL navigation for about links and license information. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/allSettings/about/list/

| File | Description |
|------|-------------|
| `TariIconViewHolder.kt` | ViewHolder for displaying Tari icon items in about screen RecyclerView. Extends CommonViewHolder with ItemTariIconBinding. Binds TariIconViewHolderItem data including icon drawable and text. Exports: getBuilder() for ViewHolderBuilder pattern. Used by TariIconsAdapter to display clickable icon/text combinations in about screen. |
| `TariIconViewHolderItem.kt` | Data model for Tari icon list items in about screen. Extends CommonViewHolderItem with icon resource, text resource, and iconLink string resource. Generates unique viewHolderUUID from combined values. Used by TariAboutViewModel and TariIconViewHolder to represent clickable icon entries with associated URLs. |
| `TariIconsAdapter.kt` | RecyclerView adapter specifically for displaying TariIconViewHolderItem list in about screen. Extends CommonAdapter with single ViewHolder type (TariIconViewHolder). Simple adapter implementation focused on displaying Tari-related icons and links. Used by TariAboutFragment. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/allSettings/backupOptions/

| File | Description |
|------|-------------|
| `SettingsBackupOptionViewHolder.kt` | ViewHolder for displaying backup option settings with dynamic status indicators. Extends CommonViewHolder with ItemSettingsBackupOptionBinding. Manages backup state visualization (InProgress, Success, Warning) with corresponding progress, success, and warning views. Dependencies: PresentationBackupState. Exports: getBuilder(). Features click handling and dynamic text/color styling based on backup state. |
| `SettingsBackupOptionViewHolderItem.kt` | Data model for backup option settings row items. Extends CommonViewHolderItem. Contains backup state (PresentationBackupState), left icon resource ID, and click action lambda. Used by SettingsBackupOptionViewHolder to represent backup configuration options with visual state indicators. UUID generated from backup state string representation. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/allSettings/divider/

| File | Description |
|------|-------------|
| `SettingsDividerViewHolder.kt` | ViewHolder for displaying divider elements in settings list. Extends CommonViewHolder with ItemSettingsDividerBinding and DividerViewHolderItem. Simple visual separator component used in AllSettingsOptionAdapter. Exports: getBuilder() for ViewHolderBuilder pattern. No binding logic required as dividers are static visual elements. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/allSettings/myProfile/

| File | Description |
|------|-------------|
| `MyProfileViewHolder.kt` | ViewHolder for displaying user profile information in settings. Extends CommonViewHolder with ItemMyProfileBinding. Displays alias, emoji ID (prefix/first/last parts), and YAT information with conditional visibility. Dependencies: MyProfileViewHolderItem, DebugConfig, address emoji utilities. Features click handling and YAT display control via debug config. Exports: getBuilder(). |
| `MyProfileViewHolderItem.kt` | Data class for user profile information display in settings. Extends CommonViewHolderItem. Contains TariWalletAddress, YAT string, alias, and click action. Used by MyProfileViewHolder to represent user's wallet identity including address, nickname, and YAT alias. UUID generated from wallet address full emoji ID. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/allSettings/row/

| File | Description |
|------|-------------|
| `SettingsRowStyle.kt` | Enum defining visual styles for settings row items. Values: Normal (standard appearance), Warning (alert/danger styling). Used by SettingsRowView and SettingsRowViewHolderItem to control text color and visual appearance of settings options based on their functional context. |
| `SettingsRowView.kt` | Custom view component for settings row items with configurable styling. Extends CommonView with ViewSettingsRowBinding. Features: left icon, title, right icon, warning indicator, style-based color theming. Dependencies: SettingsRowViewHolderItem, SettingsRowStyle, PaletteManager. Supports normal and warning styles with automatic color application. Handles click events via item action lambda. |
| `SettingsRowViewHolder.kt` | ViewHolder for settings row items using SettingsRowView component. Extends CommonViewHolder with ItemSettingsRowBinding. Delegates item binding to SettingsRowView.initDto() method. Exports: getBuilder() for ViewHolderBuilder pattern. Used by AllSettingsOptionAdapter to display configurable settings menu items. |
| `SettingsRowViewHolderItem.kt` | Data model for settings row configuration. Extends CommonViewHolderItem. Contains title, optional left/right icons, warning flag, SettingsRowStyle, and action lambda. Used by SettingsRowViewHolder and SettingsRowView to configure appearance and behavior of settings menu items. UUID generated from title string. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/allSettings/title/

| File | Description |
|------|-------------|
| `SettingsTitleView.kt` | Custom view component for displaying section titles in settings lists. Extends CommonView with ViewSettingsTitleBinding. Simple text display component for organizing settings into labeled sections. Dependencies: SettingsTitleViewHolderItem. Exports: initDto() method for title configuration. Used to create visual hierarchy in settings UI. |
| `SettingsTitleViewHolder.kt` | ViewHolder for settings section titles using SettingsTitleView component. Extends CommonViewHolder with ItemSettingsTitleBinding. Delegates item binding to SettingsTitleView.initDto() method. Exports: getBuilder() for ViewHolderBuilder pattern. Used by AllSettingsOptionAdapter to display section headers in settings list. |
| `SettingsTitleViewHolderItem.kt` | Data model for settings section title items. Extends CommonViewHolderItem with custom hashCode implementation. Contains title string used by SettingsTitleViewHolder. Dependencies: HashcodeUtils for hash generation. UUID includes item type and title. Used to create section headers in settings lists. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/allSettings/version/

| File | Description |
|------|-------------|
| `SettingsVersionViewHolder.kt` | ViewHolder for displaying wallet version information in settings. Extends CommonViewHolder with ItemSettingsVersionBinding. Displays version string and handles click events. Used by AllSettingsOptionAdapter to show app version, build number, and network information with clickable interaction. Exports: getBuilder(). |
| `SettingsVersionViewHolderItem.kt` | Data model for version display in settings. Extends CommonViewHolderItem with version string and click action. Used by SettingsVersionViewHolder to represent app version information with interactive functionality. UUID includes ViewHolder type and version string identifier. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/backup/backupSettings/

| File | Description |
|------|-------------|
| `BackupSettingsFragment.kt` | Main backup configuration screen fragment managing wallet backup options and settings. Extends CommonXmlFragment with BackupSettingsViewModel. Features: recovery phrase backup, cloud backup, password management, backup status indicators. Dependencies: BackupOptionView, backup state tracking. Handles activity results, seed word verification status, and backup option configuration. Exports: newInstance(). |
| `BackupSettingsViewModel.kt` | ViewModel managing backup settings logic and state. Extends CommonViewModel. Dependencies: BackupManager, BackupPrefRepository, BackupStateHandler. Exports: backup options, navigation actions, backup status. Handles cloud backup initiation, password management, recovery phrase backup, backup failure error handling. Manages backup state changes and option availability. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/backup/backupSettings/option/

| File | Description |
|------|-------------|
| `BackupOptionView.kt` | Custom view component for backup option configuration with toggle switch and status display. Extends CommonView with ViewBackupOptionBinding and BackupOptionViewModel. Features: backup enable/disable toggle, progress indication, folder selection triggering, last backup timestamp display. Dependencies: Fragment lifecycle, BackupManager. Used in BackupSettingsFragment to display backup options like Google Drive or local storage. |
| `BackupOptionViewModel.kt` | ViewModel managing individual backup option (Google/Local) configuration and state. Extends CommonViewModel. Dependencies: BackupPrefRepository, BackupManager, BackupStateHandler. Features: switch state management, storage setup handling, backup state transitions, error dialog display. Handles activity results for storage permissions, backup enable/disable flows, and comprehensive error handling with user feedback. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/backup/changeSecurePassword/

| File | Description |
|------|-------------|
| `ChangeSecurePasswordFragment.kt` | Fragment for changing backup password with validation and user feedback. Extends CommonXmlFragment with ChangeSecurePasswordViewModel. Features: dual password input with confirmation, length validation (6+ chars), real-time error display, keyboard management, backup progress indication. Dependencies: InputMethodManager, PaletteManager. Handles password validation, UI state transitions, and error dialogs for backup password updates. |
| `ChangeSecurePasswordViewModel.kt` | ViewModel managing backup password change operations and state. Extends CommonViewModel with Effect pattern. Dependencies: CorePrefRepository, BackupManager, BackupPrefRepository, BackupStateHandler. Features: password update with backup execution, backup state monitoring, error handling for network/storage issues. Provides navigation back to backup settings and comprehensive error reporting via Effect sealed class. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/backup/data/

| File | Description |
|------|-------------|
| `BackupOption.kt` | Enum defining available wallet backup storage options. Values: Google (Google Drive backup), Local (device storage), Dropbox (commented out - not yet supported). Used throughout backup system to determine storage destination and backup method configuration. |
| `BackupOptionDto.kt` | Data transfer object for backup option configuration and status. Implements Serializable. Contains BackupOption type, enable status, success/failure timestamps. Dependencies: SerializableTime for date handling. Used to transfer backup configuration data between components and persist backup status information. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/backup/enterCurrentPassword/

| File | Description |
|------|-------------|
| `EnterCurrentPasswordFragment.kt` | Fragment for verifying current backup password before allowing changes. Extends CommonXmlFragment with EnterCurrentPasswordViewModel. Features: password input validation, error state display for mismatches, temporary button disabling on failed attempts, automatic keyboard management. Dependencies: BackupPrefRepository for password verification. Navigates to ChangeSecurePasswordFragment on successful verification. |
| `EnterCurrentPasswordViewModel.kt` | Minimal ViewModel for EnterCurrentPasswordFragment handling password verification logic. Extends CommonViewModel with BackupPrefRepository dependency for accessing stored backup password. Provides access to backup settings repository for password comparison in the fragment. Simple supporting ViewModel with no additional business logic. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/backup/learnMore/

| File | Description |
|------|-------------|
| `BackupLearnMoreFragment.kt` | Educational onboarding fragment for wallet backup concepts using ViewPager2 flow. Extends CommonXmlFragment with BackupLearnMoreViewModel. Features 4-stage educational flow with tab indicators, next/close navigation, stage-specific navigation actions. Dependencies: BackupLearnMoreDataSource, BackupLearnMoreStageArgs. Uses BackupOnboardingFlowAdapter for fragment management and provides navigation to backup workflows. |
| `BackupLearnMoreViewModel.kt` | ViewModel supporting backup education flow with navigation actions. Extends CommonViewModel. Provides navigation methods to change password and wallet backup flows. Simple supporting ViewModel focused on navigation coordination between learn more stages and actual backup implementation. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/backup/learnMore/item/

| File | Description |
|------|-------------|
| `BackupLearnMoreDataSource.kt` | Static data source managing backup education stage configurations. Object providing storage and retrieval for BackupLearnMoreStageArgs list. Features position-based access and list management for ViewPager2 integration. Used by BackupLearnMoreFragment and related components to share stage configuration data. |
| `BackupLearnMoreItemFragment.kt` | Individual page fragment for backup education flow displaying stage-specific content. Extends CommonXmlFragment with BackupLearnMoreItemViewModel. Features image, title, description, button, and bottom text display based on BackupLearnMoreStageArgs. Dependencies: BackupLearnMoreDataSource for stage data. Exports: createInstance() factory method. |
| `BackupLearnMoreItemViewModel.kt` | Minimal ViewModel for BackupLearnMoreItemFragment. Extends CommonViewModel with basic dependency injection. No additional logic required as fragment handles display directly from stage arguments. Simple supporting ViewModel following architecture patterns. |
| `BackupLearnMoreStageArgs.kt` | Sealed class hierarchy defining 4-stage backup education flow with rich text formatting. Implements Serializable. Contains StageOne (seed words), StageTwo (cloud backup), StageThree (password protection), StageFour (completion). Features: SpannableString formatting, HTML processing, threshold balance display, typography styling. Dependencies: ResourceManager, ButtonModule, PaletteManager, HtmlHelper. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/backup/learnMore/module/

| File | Description |
|------|-------------|
| `BackupLearnMoreItemModule.kt` | Dialog module wrapper for backup education stage content. Extends IDialogModule containing BackupLearnMoreStageArgs. Simple container class for integrating backup education content into modal dialog system. Used by BackupLearnMoreItemModuleView for dialog presentation. |
| `BackupLearnMoreItemModuleView.kt` | Dialog view component for displaying backup education content in modal format. Extends CommonView with DialogModuleBackupLearnMoreItemBinding. Features image, title, description, button, and close functionality. Dependencies: BackupLearnMoreItemModule for stage data. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/backup/verifySeedPhrase/

| File | Description |
|------|-------------|
| `SeedPhrase.kt` | Wallet seed phrase management class implementing Iterable<String>. Manages seed word list, sorting, verification, and selection operations. Features: phrase consistency checking, sorted word generation, selection initialization. Dependencies: DebugConfig for mock sorting. Used for seed phrase verification flow where users must correctly reorder scrambled seed words. |
| `SelectableWordTextView.kt` | Custom view for selectable seed phrase words in verification UI. Extends FrameLayout with ViewConfirmWordBinding. Features configurable selection state, remove button visibility, and custom padding/background. Used in seed phrase verification flow to display individual words that users can select and deselect. |
| `SelectionSequence.kt` | Class managing user selection sequence during seed phrase verification. Tracks word selection order, validates selections, prevents duplicates. Features: selection state tracking, original phrase matching, sequence completion detection. Used with SeedPhrase to verify users can correctly reconstruct their wallet seed phrase from shuffled words. |
| `VerifySeedPhraseFragment.kt` | Interactive seed phrase verification fragment with advanced animation and selection logic. Extends CommonXmlFragment with VerifySeedPhraseViewModel. Features: screen recording protection, shuffled word selection with animations, real-time validation feedback, flexbox layouts for word organization. Dependencies: SeedPhrase, SelectionSequence, SelectableWordTextView. Implements sophisticated word selection/deselection with fade animations and completion validation. Exports: newInstance(). |
| `VerifySeedPhraseViewModel.kt` | ViewModel managing seed phrase verification state and logic. Extends CommonViewModel. Uses SeedPhrase and SelectionSequence for word management. Features phrase initialization, word selection/deselection tracking, completion detection, verification completion with settings update. Provides LiveData for UI state changes and handles navigation back to backup settings on successful verification. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/backup/writeDownSeedWords/

| File | Description |
|------|-------------|
| `WriteDownSeedPhraseFragment.kt` | Fragment for displaying wallet seed phrase with expandable grid layout and security features. Extends CommonXmlFragment with WriteDownSeedPhraseViewModel. Features screen recording protection, 2-column grid layout, expandable/collapsible view with animations, confirmation checkbox, debug copy functionality. Dependencies include PhraseWordsAdapter and ConstraintSet animations. |
| `WriteDownSeedPhraseViewModel.kt` | ViewModel managing seed phrase retrieval and display logic. Extends CommonViewModel. Features wallet seed word extraction, error handling for failed retrieval, clipboard functionality for debug builds. Provides LiveData for seed words list and handles wallet interaction with comprehensive error feedback via dialog display. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/backup/writeDownSeedWords/adapter/

| File | Description |
|------|-------------|
| `PhraseWordViewHolder.kt` | ViewHolder for individual seed phrase words with animated margin transitions. Extends RecyclerView.ViewHolder with custom layout handling. Features: word index and content display, animated margin adjustments for expanded/collapsed states. Uses ValueAnimator for smooth transitions between display modes. Used by PhraseWordsAdapter for seed phrase word presentation with visual state changes. |
| `PhraseWordsAdapter.kt` | RecyclerView adapter for displaying seed phrase words in column-based layout. Manages mutable seed words list with expandable/collapsible display modes. Features custom position indexing for column layout (splits into left/right columns), expansion state management. Uses PhraseWordViewHolder for individual word display with animated transitions. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/baseNodeConfig/changeBaseNode/

| File | Description |
|------|-------------|
| `ChangeBaseNodeFragment.kt` | Fragment for managing and selecting Tari network base nodes. Extends CommonXmlFragment with ChangeBaseNodeViewModel. Features base node list display, add custom node action, node selection and QR code sharing, delete functionality for custom nodes. Dependencies: ChangeBaseNodeAdapter, TariToolbarActionArg. Implements click and long-click handlers for node management and sharing. |
| `ChangeBaseNodeViewModel.kt` | ViewModel managing base node configuration and selection logic. Extends CommonViewModel. Dependencies: BaseNodesManager, BaseNodeStateHandler. Features: base node list management, custom node creation with validation, QR code sharing, node selection with wallet sync. Implements comprehensive base node operations including add/delete custom nodes, validation, and deep link generation for sharing. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/baseNodeConfig/changeBaseNode/adapter/

| File | Description |
|------|-------------|
| `BaseNodeViewHolder.kt` | ViewHolder for displaying base node information in selection list. Extends CommonViewHolder with ItemBaseNodeBinding. Displays base node name, public key hex, and onion address. Features: current node indication with done icon, delete button for custom nodes, conditional UI elements based on node type and selection state. Exports: getBuilder(). |
| `BaseNodeViewHolderItem.kt` | Data model for base node list items in selection interface. Extends CommonViewHolderItem. Contains BaseNodeDto, current node reference, and delete action lambda. Used by BaseNodeViewHolder to represent individual base nodes with selection state and interaction capabilities. UUID generated from node address for unique identification. |
| `ChangeBaseNodeAdapter.kt` | RecyclerView adapter for displaying base node selection list. Extends CommonAdapter with single ViewHolder type (BaseNodeViewHolder). Simple adapter implementation focused on base node display and selection functionality. Used by ChangeBaseNodeFragment for node management interface. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/bugReporting/

| File | Description |
|------|-------------|
| `BugsReportingFragment.kt` | Fragment for bug reporting with user input form and log access. Extends CommonXmlFragment with BugsReportingViewModel. Features name, email, and bug description input fields, send functionality, view logs button. Dependencies: DebugActivity integration for log viewing. Handles user input collection and submission to bug reporting service. |
| `BugsReportingViewModel.kt` | ViewModel managing bug report submission with comprehensive error handling. Extends CommonViewModel. Dependencies: BugReportingService. Features: asynchronous bug report sending with user feedback, error dialog display, success navigation, comprehensive logging. Handles name, email, and description data processing with robust error recovery. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/dataCollection/

| File | Description |
|------|-------------|
| `DataCollectionFragment.kt` | Fragment for managing data collection privacy settings with toggle switch interface. Extends CommonXmlFragment with DataCollectionViewModel. Features TariLoadingSwitchState for visual feedback, user preference updates, state observation. Simple privacy settings interface for controlling app data collection behavior. |
| `DataCollectionViewModel.kt` | ViewModel managing Sentry data collection preferences for privacy control. Extends CommonViewModel. Dependencies: SentryPrefRepository. Features state management for crash reporting and analytics toggle, real-time preference updates. Simple privacy control interface with boolean state management for user data collection consent. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/deleteWallet/

| File | Description |
|------|-------------|
| `DeleteWalletFragment.kt` | Critical wallet deletion fragment with confirmation flow and progress indication. Extends CommonXmlFragment with DeleteWalletViewModel. Features confirmation dialog, progress display, complete app restart flow. Dependencies: OnboardingFlowActivity for restart. Implements secure wallet deletion with full activity stack clearance and navigation to onboarding. |
| `DeleteWalletViewModel.kt` | ViewModel managing wallet deletion with confirmation dialog and secure deletion process. Extends CommonViewModel. Features: confirmation dialog with warning styling, asynchronous wallet deletion, progress indication, navigation to splash screen. Uses SingleLiveEvent for UI coordination and implements complete wallet cleanup with proper threading. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/logs/logFiles/

| File | Description |
|------|-------------|
| `LogFilesFragment.kt` | Fragment displaying list of available log files for debugging and support. Extends CommonXmlFragment with LogFilesViewModel. Features RecyclerView with LogFileListAdapter for file selection. Dependencies: DebugActivity integration for navigation. Provides access to application log files with navigation to detailed log viewing. |
| `LogFilesViewModel.kt` | ViewModel managing log file list display with file size formatting. Extends CommonViewModel. Dependencies: WalletConfig for file access. Features: log file discovery, human-readable file size calculation (B/KB/MB/GB/TB), navigation coordination. Implements file size formatting with decimal precision and proper unit scaling. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/logs/logFiles/adapter/

| File | Description |
|------|-------------|
| `LogFileListAdapter.kt` | RecyclerView adapter for displaying log file list with dividers. Extends CommonAdapter with two ViewHolder types: LogFileViewHolder and SettingsDividerViewHolder. Simple adapter implementation for log file selection interface. Used by LogFilesFragment for file navigation and management. |
| `LogFileViewHolder.kt` | ViewHolder for displaying individual log files in selection list. Extends CommonViewHolder with ItemSettingsRowBinding. Uses SettingsRowView component for consistent styling with filename display and click handling. Exports: getBuilder() for ViewHolderBuilder pattern. Integrates with settings row UI components for uniform appearance. |
| `LogFileViewHolderItem.kt` | Data model for log file list items. Extends CommonViewHolderItem. Contains filename, File reference, and click action lambda. Used by LogFileViewHolder to represent individual log files with navigation functionality. UUID generated from filename for unique identification in RecyclerView. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/logs/logs/

| File | Description |
|------|-------------|
| `LogsFragment.kt` | Fragment for detailed log viewing with filtering and clipboard functionality. Extends CommonXmlFragment with LogsViewModel. Features: toolbar with filter action, RecyclerView with LogListAdapter, long-click clipboard copying, loading state management. Dependencies: DebugActivity integration, TariToolbarActionArg. Exports: getInstance() factory with File parameter. |
| `LogsViewModel.kt` | Advanced ViewModel managing log file content with filtering and clipboard functionality. Uses SavedStateHandle for file parameter. Dependencies: LogLevelFilters, LogSourceFilters. Features: asynchronous file reading, MediatorLiveData for filtered results, dynamic filter dialog with CheckedModules, clipboard integration. Implements comprehensive log filtering with level and source-based criteria. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/logs/logs/adapter/

| File | Description |
|------|-------------|
| `DebugLog.kt` | Log parsing class for structured debug log analysis with regex pattern matching. Features timestamp, source, level, and log content extraction from FFI log format. Supports nested Aurora debug logs with recursive parsing. Uses regex pattern for log line structure: timestamp [source] [optional] LEVEL message. Enables sophisticated log filtering and analysis in debug interface. |
| `LogListAdapter.kt` | RecyclerView adapter for displaying parsed debug logs in detailed log viewer. Extends CommonAdapter with single ViewHolder type (LogViewHolder). Simple adapter implementation focused on log line display with structured content. Used by LogsFragment for filtered log presentation. |
| `LogViewHolder.kt` | ViewHolder for displaying individual debug log entries with intelligent content selection. Extends CommonViewHolder with ItemLogBinding. Features automatic Aurora debug log preference over raw FFI logs for better readability. Used by LogListAdapter to display parsed log content with proper formatting and trimming. Exports: getBuilder(). |
| `LogViewHolderItem.kt` | Data model for individual log entries in debug log viewer. Extends CommonViewHolderItem with DebugLog reference. Used by LogViewHolder to represent parsed log content with structured information including timestamp, source, level, and message. UUID generated from DebugLog object for unique identification. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/logs/logs/module/

| File | Description |
|------|-------------|
| `LogLevelCheckedModule.kt` | Dialog module wrapper for log level filter checkboxes. Extends IDialogModule with LogLevelFilters and CheckedModule integration. Used in log filtering dialog to manage level-based filter selection (Error, Warning, Info, Debug). Provides state management for checkbox filtering in log viewer interface. |
| `LogLevelFilters.kt` | Enum defining log level filtering criteria with matching logic. Values: Error, Warning, Info, Debug with case-insensitive level matching functions. Each entry contains title resource and lambda function for DebugLog matching. Used by LogsViewModel for dynamic log filtering based on severity levels. |
| `LogSourceCheckedModule.kt` | Dialog module wrapper for log source filter checkboxes. Extends IDialogModule with LogSourceFilters and CheckedModule integration. Used in log filtering dialog to manage source-based filter selection (FFI, Aurora General, UI, Navigation, Connection). Provides state management for source filtering in log viewer. |
| `LogSourceFilters.kt` | Enum defining log source filtering criteria with component-specific matching logic. Values: FFI, AuroraGeneral, AuroraUI, AuroraNavigation, AuroraConnection with lambda functions for source identification. Dependencies: LoggerTags for component matching. Used by LogsViewModel for filtering logs by system component origin. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/networkSelection/

| File | Description |
|------|-------------|
| `NetworkSelectionFragment.kt` | Fragment for selecting Tari network environment with app restart coordination. Extends CommonXmlFragment with NetworkSelectionViewModel. Features RecyclerView with NetworkAdapter for network options, click handling for network selection, complete app restart flow to StartActivity. Dependencies: NetworkViewHolderItem for network display. Critical settings affecting wallet network connectivity. |
| `NetworkSelectionViewModel.kt` | ViewModel managing Tari network selection with wallet recreation and dependency injection reset. Extends CommonViewModel. Dependencies: WalletConfig, TariNetwork, DiContainer. Features network list management, confirmation dialogs for existing wallets, complete app restart coordination, wallet stop/restart cycle. Critical for switching between mainnet/testnet environments. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/networkSelection/networkItem/

| File | Description |
|------|-------------|
| `NetworkAdapter.kt` | RecyclerView adapter for network selection items in settings. Extends CommonAdapter to handle display of network options in the network selection screen. Uses NetworkTariViewHolder to render individual network items. Part of the network configuration UI where users can select between different Tari network environments (testnet, mainnet, etc.). |
| `NetworkTariViewHolder.kt` | ViewHolder for individual network selection items in the network configuration settings. Binds NetworkViewHolderItem data to ItemNetworkBinding UI elements. Displays network name with optional "recommended" label and shows checkmark for currently selected network. Uses data binding pattern and supports the settings screen where users choose their Tari network environment. |
| `NetworkViewHolderItem.kt` | Data model for network selection list items in settings UI. Extends CommonViewHolderItem and holds TariNetwork configuration and current selected Network state. Used by NetworkAdapter and NetworkTariViewHolder to display available network options with current selection state. Part of the network configuration feature allowing users to switch between different Tari blockchain networks. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/screenRecording/

| File | Description |
|------|-------------|
| `ScreenRecordingSettingsFragment.kt` | Settings fragment for managing screen recording permissions and security. Extends CommonXmlFragment with FragmentScreenRecordingSettingsBinding and ScreenRecordingSettingsViewModel. Provides toggle switch UI to enable/disable screen recording with confirmation dialog for security. Uses TariLoadingSwitchView and observes ViewModel state changes. Part of privacy/security settings to protect sensitive wallet information from screen capture. |
| `ScreenRecordingSettingsViewModel.kt` | ViewModel for screen recording settings management. Extends CommonViewModel and manages TariLoadingSwitchState for enable/disable toggle. Shows confirmation dialog when enabling screen recording for security awareness. Persists setting via tariSettingsSharedRepository.screenRecordingTurnedOn. Critical security feature to prevent unauthorized capture of sensitive wallet data like seed phrases, balances, and transaction details. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/themeSelector/

| File | Description |
|------|-------------|
| `TariTheme.kt` | Enum defining available theme options for the Tari wallet application. Contains AppBased (system theme), Light, and Dark theme choices. Each enum value includes resource references for display text (string resource) and preview image (drawable resource). Used by theme selection UI to present and manage user's visual appearance preferences. |
| `ThemeSelectorFragment.kt` | Settings fragment for theme selection UI. Extends CommonXmlFragment with FragmentThemeChangeBinding and ThemeSelectorViewModel. Displays theme options in a 2-column GridLayoutManager using ThemesAdapter. Observes theme changes and triggers activity recreation to apply new theme. Handles user interaction for selecting between Light, Dark, and System-based themes. Part of app customization settings. |
| `ThemeSelectorViewModel.kt` | ViewModel for theme selection settings screen. Extends CommonViewModel and manages list of theme options with current selection state. Handles theme switching via tariSettingsSharedRepository and triggers UI refresh through newTheme SingleLiveEvent. Listens to base node state changes to refresh theme list. Coordinates with TariTheme enum and ThemeViewHolderItem for consistent theme management across the application. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/themeSelector/adapter/

| File | Description |
|------|-------------|
| `ThemeViewHolderItem.kt` | Data model for theme selection list items in RecyclerView. Extends CommonViewHolderItem and holds TariTheme configuration, current selected theme state, and checkbox selection callback. Used by ThemesAdapter to display theme options with selection state. Contains theme property, currentTheme for comparison, and checkboxSelection function for handling user theme changes in the theme selector UI. |
| `ThemesAdapter.kt` | RecyclerView adapter for theme selection list in settings. Extends CommonAdapter to handle ThemeViewHolderItem objects and uses ThemesViewHolder for rendering. Manages the list of available themes (Light, Dark, System-based) in the theme selection screen. Works with ThemeSelectorFragment and ThemeSelectorViewModel to provide theme switching functionality. |
| `ThemesViewHolder.kt` | ViewHolder for theme selection items in RecyclerView. Extends CommonViewHolder with ItemThemeBinding to display theme options with title, preview image, and selection checkbox. Handles theme selection state by comparing current theme with item theme and managing checkbox interactions. Disables checkbox interaction for currently selected theme. Used by ThemesAdapter in theme selection settings screen. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/torBridges/

| File | Description |
|------|-------------|
| `TorBridgesSelectionFragment.kt` | Settings fragment for Tor bridge configuration and selection. Extends CommonXmlFragment with FragmentTorBridgeSelectionBinding and TorBridgesSelectionViewModel. Displays available Tor bridges in LinearLayoutManager using TorBridgesAdapter. Provides bridge preselection, connection functionality, and QR code viewing for custom bridges. Critical for privacy features allowing users to configure Tor network access when regular Tor connection is blocked. |
| `TorBridgesSelectionViewModel.kt` | ViewModel for Tor bridge configuration and selection. Extends CommonViewModel and manages bridge selection, connection, and state management. Handles custom bridges, bridge preselection, QR code sharing, and Tor connection process with progress dialogs. Integrates with TorProxyManager, TorPrefRepository, and TorProxyStateHandler for complete Tor network configuration. Critical for privacy features when users need alternative Tor connection methods. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/torBridges/customBridges/

| File | Description |
|------|-------------|
| `CustomTorBridgesFragment.kt` | Fragment for custom Tor bridge configuration and input. Extends CommonXmlFragment with FragmentCustomTorBridgesBinding and CustomTorBridgesViewModel. Provides UI for manual bridge input, QR code scanning, and external bridge request functionality. Supports text input validation and bridge connection setup. Critical for users in restricted networks requiring custom Tor bridge configurations for privacy and accessibility. |
| `CustomTorBridgesViewModel.kt` | ViewModel for managing custom Tor bridge configurations in wallet settings. Handles parsing and validation of custom Tor bridge connection strings, manages bridge configuration persistence, and processes deeplinks for Tor bridges. Key exports: CustomTorBridgesViewModel class with methods for openRequestPage(), navigateToUploadQr(), connect(), and handleDeeplink(). Dependencies: TorPrefRepository for bridge storage, CommonViewModel for base functionality, DeepLink handlers. Used by: Custom Tor bridges settings screen. Implements comprehensive bridge string parsing supporting multiple formats (basic and advanced with certificates), validates IP:port format, and provides error handling for incorrect formats. Includes deeplink support for automated bridge configuration via QR codes or links. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/settings/torBridges/torItem/

| File | Description |
|------|-------------|
| `TorBridgeViewHolderItem.kt` | Sealed class hierarchy for Tor bridge list items in RecyclerView. Defines Empty (no bridges), Bridge (configured bridge with TorBridgeConfiguration), and CustomBridges (add custom bridges option) variants. Extends CommonViewHolderItem with selection state management and deep copying support. Provides type-safe abstraction for different bridge selection options in Tor configuration UI. |
| `TorBridgesAdapter.kt` | RecyclerView adapter for Tor bridge selection list. Extends CommonAdapter to handle TorBridgeViewHolderItem objects and uses TorBridgesViewHolder for rendering. Manages the display of available bridge options including no bridges, configured bridges, and custom bridge entry option in the Tor bridge selection interface. |
| `TorBridgesViewHolder.kt` | ViewHolder for Tor bridge selection items in RecyclerView. Extends CommonViewHolder with ItemBridgeBinding to display bridge names with appropriate selection indicators. Shows checkmark for selected bridges and arrow for custom bridge navigation option. Handles different bridge types (Empty, Bridge, CustomBridges) with appropriate UI state visualization for Tor bridge configuration interface. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/store/

| File | Description |
|------|-------------|
| `EventsPropagatingWebViewClient.kt` | Custom WebViewClient for store fragment with event propagation and URL override capabilities. Extends WebViewClient and maintains list of WebViewEventListener for page lifecycle events (started, finished, error). Provides URLOverrideStrategy pattern for handling external links, including ExternalSiteOverride for redirecting external URLs. Used by StoreFragment for comprehensive web browsing with proper event handling and logging. |
| `StoreFragment.kt` | WebView-based store fragment for Tari TTL (T-Shirt Transaction Layer) store integration. Extends Fragment with FragmentStoreBinding and implements full web browser functionality including navigation controls, JavaScript support, error handling, and scroll-based UI animations. Provides share functionality and external link handling. Features NavigationPanelAnimation for hiding/showing browser controls based on scroll direction. Part of e-commerce integration within the wallet application. |
| `WebViewEventListener.kt` | Interface for WebView event callbacks in store functionality. Defines lifecycle event methods: onPageStarted, onPageCommitVisible, onPageFinished, and onReceivedError. Used by EventsPropagatingWebViewClient to provide standardized event handling for WebView operations. Enables loose coupling between WebView implementation and event processing logic for the store WebView integration. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/tx/details/

| File | Description |
|------|-------------|
| `TxDetailsFragment.kt` | Transaction details screen fragment using Jetpack Compose. Extends CommonFragment with TxDetailsViewModel and displays comprehensive transaction information through TxDetailsScreen composable. Handles transaction actions like cancel, copy values, block explorer opening, contact editing, and address details. Receives transaction data and UI options via Bundle arguments. Part of transaction management workflow for viewing and interacting with transaction details. |
| `TxDetailsModel.kt` | Data models and business logic for transaction details screen. Contains UiState with comprehensive transaction information including fees, status, block explorer links, formatted dates, and cancellation reasons. Handles different transaction types (completed, cancelled, pending) with appropriate status text and UI states. Provides transaction-specific computed properties like totalAmount, showCancelButton, and blockExplorerLink for enhanced transaction detail presentation. |
| `TxDetailsScreen.kt` | Jetpack Compose screen for comprehensive transaction details display. Implements TxDetailsModel.UiState with adaptive layout for transaction information including amounts, fees, contacts, dates, status, and action buttons. Supports different transaction states with appropriate UI variations and multiple preview configurations. Provides copy functionality, block explorer integration, contact editing, and transaction cancellation with rich interaction patterns. |
| `TxDetailsViewModel.kt` | ViewModel for transaction details screen. Extends CommonViewModel with SavedStateHandle for transaction data. Manages TxDetailsModel.UiState with real-time transaction updates via wallet events. Handles contact editing, transaction cancellation, block explorer opening, address details, and fee information dialogs. Listens to wallet events for live transaction status updates and integrates with ContactsRepository for contact management. Critical for comprehensive transaction detail presentation and user interactions. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/tx/details/widget/

| File | Description |
|------|-------------|
| `TxDetailInfoItem.kt` | Jetpack Compose components for transaction detail information display. Provides TxDetailInfoItem base component, TxDetailInfoCopyItem with copy functionality, TxDetailInfoContactNameItem for contact editing, TxDetailInfoStatusItem with status-based coloring, and TxDetailInfoAddressItem with emoji/base58 toggle. Includes comprehensive preview configurations and action icon integration for interactive transaction detail elements. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/tx/history/

| File | Description |
|------|-------------|
| `TxHistoryFragment.kt` | Transaction history fragment using Jetpack Compose. Extends CommonFragment with TxHistoryViewModel and displays transaction list through TxHistoryScreen composable. Supports contact-specific transaction filtering when initialized with Contact parameter. Handles transaction navigation, search functionality, and back navigation. Part of transaction management workflow for viewing historical transaction data with search and filtering capabilities. |
| `TxHistoryScreen.kt` | Jetpack Compose screen for transaction history listing and search. Implements TxHistoryViewModel.UiState with adaptive layout for general history or contact-specific transactions. Features search functionality, empty state handling, and categorized transaction lists (pending/completed). Supports different preview modes and provides keyboard management for search interactions. Critical for transaction browsing and historical data access. |
| `TxHistoryViewModel.kt` | ViewModel for transaction history management and filtering. Extends CommonViewModel with SavedStateHandle for contact-specific filtering. Manages TxRepository integration, search query processing, and navigation to transaction details. Provides UiState with computed properties for transaction filtering, empty state detection, and sorted list generation. Supports both general transaction history and contact-specific transaction views with real-time data updates. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/utxos/list/

| File | Description |
|------|-------------|
| `UtxosListFragment.kt` | Fragment for UTXO (Unspent Transaction Output) management and visualization. Extends CommonXmlFragment with FragmentUtxosListBinding and UtxosListViewModel. Supports both text and tile list display modes with synchronized scrolling for dual-column tile view. Implements selection, join/split operations, sorting controls, and multi-adapter management. Critical for advanced wallet users to manage individual UTXOs for coin control and transaction optimization. |
| `UtxosListViewModel.kt` | Comprehensive ViewModel for UTXO management and operations. Extends CommonViewModel and handles UTXO loading, sorting, selection, joining, and splitting operations. Manages dual-column tile layout with height calculations, text/tile view modes, and complex selection states. Integrates with wallet FFI for UTXO operations and provides detailed dialogs for UTXO information. Critical for advanced users performing coin control and UTXO optimization with complete transaction output management. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/utxos/list/adapters/

| File | Description |
|------|-------------|
| `UtxosListAdapter.kt` | RecyclerView adapter for UTXO text list display. Extends CommonAdapter to handle UtxosViewHolderItem objects and uses UtxosTextListViewHolder for rendering. Part of UTXO management UI providing linear text-based list view of unspent transaction outputs. Works with UtxosListFragment and UtxosListViewModel for comprehensive UTXO management functionality. |
| `UtxosListTileAdapter.kt` | RecyclerView adapter for UTXO tile display mode. Extends CommonAdapter to handle UtxosViewHolderItem objects and uses UtxosTileListViewHolder for visual tile rendering. Part of dual-column tile layout system in UTXO management UI, providing height-based visual representation of UTXO values alongside UtxosListAdapter for comprehensive UTXO visualization options. |
| `UtxosStatus.kt` | Enum defining UTXO status types with associated UI resources. Contains Mined and Confirmed status values, each with corresponding icon, text icon, and string resource references. Used throughout UTXO list displays to show visual status indicators and localized status text. Essential for UTXO management UI to clearly communicate the confirmation state of unspent transaction outputs to users. |
| `UtxosTextListViewHolder.kt` | ViewHolder for UTXO text list display mode. Extends CommonViewHolder with ItemUtxosTextBinding to render UTXOs in linear text format with amounts, status icons, dates, and commitment hashes. Handles selection state management with checkbox controls and alpha-based enabling/disabling. Used by UtxosListAdapter for traditional list-based UTXO visualization with comprehensive UTXO metadata display. |
| `UtxosTileListViewHolder.kt` | ViewHolder for UTXO tile display mode with visual representation. Extends CommonViewHolder with ItemUtxosTileBinding to render UTXOs as colored tiles with dynamic heights based on value. Features color generation algorithms, elevation effects for selection, and visual status indicators. Used by UtxosListTileAdapter for innovative tile-based UTXO visualization that provides intuitive size-based value representation. |
| `UtxosViewHolderItem.kt` | Data model for UTXO list items with formatting and state management. Extends CommonViewHolderItem and wraps TariUtxo with UI-specific properties like formatted amounts, dates, status indicators, and selection states. Calculates maturity, visibility conditions, and provides height constants for tile layout. Critical component for UTXO visualization with comprehensive display logic and user interaction state tracking. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/utxos/list/controllers/

| File | Description |
|------|-------------|
| `BottomButtonsController.kt` | Controller for UTXO management bottom action buttons with animated states. Manages JoinSplitButtonsState transitions and UI visibility for join/split operations. Features automatic text hiding animation after 3 seconds and coroutine-based state management. Handles None (no actions), Break (single UTXO split), and JoinAndBreak (multiple UTXO operations) states with appropriate button configurations and animations. |
| `CheckedController.kt` | Controller for UTXO selection state management with TextView display. Manages atomic boolean selection state and provides toggle functionality with callback support. Updates TextView appearance between "Selecting" and "Cancel" states with appropriate color themes using PaletteManager. Used in UTXO management interface for consistent selection state indication and user interaction feedback. |
| `JoinSplitButtonsState.kt` | Enum defining button states for UTXO join/split operations. Contains None (no operations available), Break (single UTXO split), and JoinAndBreak (multiple UTXOs join and split). Used by UtxosListViewModel to manage bottom action button visibility and functionality based on UTXO selection state in the UTXO management interface. |
| `Ordering.kt` | Enum defining UTXO list sorting options with associated UI text resources. Contains ValueAnc/ValueDesc for amount-based sorting and DateAnc/DateDesc for timestamp-based sorting. Each entry includes value identifier and string resource for user-facing display. Used by UtxosListViewModel for UTXO list ordering and sorting dialog presentation. |
| `ScreenState.kt` | Enum defining screen states for UTXO list display. Contains Loading (data loading), Empty (no UTXOs available), and Data (UTXOs displayed). Used by UtxosListViewModel and UtxosListFragment to manage UI visibility states and user feedback during UTXO loading and display operations. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/utxos/list/controllers/listType/

| File | Description |
|------|-------------|
| `ListType.kt` | Enum defining display modes for UTXO list visualization. Contains Tile (dual-column tile layout with visual size representation) and Text (linear text-based list). Used by UtxosListViewModel and ListTypeSwitchController to toggle between different UTXO presentation modes based on user preference. |
| `ListTypeSwitchController.kt` | Controller for switching between UTXO list display modes via toolbar action. Manages ListType state transitions between Tile and Text modes with appropriate icon updates. Integrates with TariToolbar to provide toggle functionality and callback support. Updates toolbar icons dynamically based on current state and provides toggle callback for coordinating with UtxosListViewModel display mode changes. |

###### app/src/main/java/com/tari/android/wallet/ui/screen/utxos/list/module/

| File | Description |
|------|-------------|
| `DetailItemModule.kt` | Data model for UTXO detail display modules in modular dialogs. Simple dialog module containing title, description, and optional description icon for displaying UTXO-related information. Key exports: DetailItemModule class extending IDialogModule. Dependencies: IDialogModule base class from modular dialog framework. Used by: UTXO detail views and modular dialog system. Provides structured data container for displaying labeled information with optional iconography in UTXO management interfaces. Part of the modular dialog component system for flexible UI composition. |
| `DetailItemModuleView.kt` | View implementation for DetailItemModule in UTXO dialogs. Custom view that renders title, description, and optional icon for UTXO detail display. Key exports: DetailItemModuleView class extending CommonView. Dependencies: DetailItemModule for data, DialogModuleUtxosDetailItemBinding for layout, CommonView for base functionality. Used by: UTXO detail dialogs and modular dialog system. Implements view binding pattern with automatic text and icon setup from module data. Supports drawable icons positioned relative to description text using compound drawables. Part of the modular dialog architecture for displaying UTXO information in a consistent format. |
| `ListItemModule.kt` | Data model for UTXO list ordering/filtering options in modular dialogs. Contains ordering configuration, selection state, and interaction handlers for UTXO list management. Key exports: ListItemModule class extending IDialogModule with properties for ordering, click handler, selection state, and listener. Dependencies: IDialogModule base class, Ordering enum from UTXO controllers. Used by: UTXO list filtering and sorting dialogs. Implements reactive selection state with listener pattern for UI updates. Supports configurable click handlers and maintains selection state for ordering options in UTXO list views. Part of the modular dialog system for UTXO management functionality. |
| `ListItemModuleView.kt` | View implementation for ListItemModule in UTXO ordering dialogs. Custom view that renders ordering options with text and selection indicator for UTXO list management. Key exports: ListItemModuleView class extending CommonView. Dependencies: ListItemModule for data, DialogModuleUtxosOrderingItemBinding for layout, CommonView for base functionality. Used by: UTXO list sorting/filtering dialogs. Implements reactive UI updates through listener pattern, displays ordering text from resource IDs, manages selection indicator visibility, and handles click interactions. Part of the modular dialog architecture providing interactive ordering controls for UTXO list views. |
| `UtxoAmountModule.kt` | Data model for UTXO amount display modules in modular dialogs. Simple dialog module containing MicroTari amount value for displaying UTXO amounts in a structured format. Key exports: UtxoAmountModule class extending IDialogModule. Dependencies: MicroTari model for amount representation, IDialogModule base class. Used by: UTXO amount display views and modular dialog system. Provides structured container for UTXO amount values to be rendered in dialogs. Part of the modular dialog component system enabling flexible display of UTXO monetary values throughout the application. |
| `UtxoAmountModuleView.kt` | View implementation for UtxoAmountModule in UTXO dialogs. Custom view that renders formatted UTXO amount values using wallet configuration formatting. Key exports: UtxoAmountModuleView class extending CommonView. Dependencies: UtxoAmountModule for data, WalletConfig for amount formatting, DialogModuleUtxoAmountBinding for layout, CommonView for base functionality. Used by: UTXO amount display dialogs and modular dialog system. Automatically formats MicroTari values to display-friendly Tari amounts using the wallet's configured formatter. Part of the modular dialog architecture providing consistent amount display formatting across UTXO-related interfaces. |
| `UtxoSplitModule.kt` | Data model for UTXO splitting functionality in modular dialogs. Contains UTXO list, preview calculation function, and split count for dividing UTXOs into smaller outputs. Key exports: UtxoSplitModule class extending IDialogModule with properties for items list, previewMaker function, and count. Dependencies: TariUtxo model, TariCoinPreview model, IDialogModule base class. Used by: UTXO splitting dialogs and transaction building. Implements UTXO splitting logic where users can specify how many smaller UTXOs to create from larger ones, includes preview calculation capability for showing split results before execution. Part of the modular dialog system for advanced UTXO management and transaction optimization. |
| `UtxoSplitModuleView.kt` | View implementation for UtxoSplitModule providing interactive UTXO splitting interface. Custom view with seekbar controls for selecting split count and real-time preview of split results including fees and output amounts. Key exports: UtxoSplitModuleView class extending CommonView. Dependencies: UtxoSplitModule for data, WalletConfig for formatting, DialogModuleUtxoSplitBinding for layout, MicroTari for calculations. Used by: UTXO splitting dialogs. Implements seekbar with min/max constraints (2-50 splits), plus/minus buttons for fine control, real-time preview updates showing total amount, split count, fee calculations, and resulting coin values. Includes sophisticated amount formatting and preview calculation integration for optimal user experience in UTXO management. |

###### app/src/main/java/com/tari/android/wallet/util/

| File | Description |
|------|-------------|
| `Constants.kt` | Application-wide constants organized by functional categories. Object containing nested constant groups for UI animations, wallet configuration, contacts, and build variants. Exports: UI timing constants, animation durations, wallet parameters, contact limits, build flavor identifiers. Used by: Throughout the app for consistent timing and configuration values. Features organized structure (UI.Button, UI.CreateEmojiId, Wallet, Contacts, Build), animation timing definitions, wallet parameters like discovery timeout and default fees, and build configuration constants. Licensed under Tari Project BSD license. Critical for maintaining consistent app behavior and timing. |
| `ContactUtil.kt` | Singleton utility class for contact-related operations and alias management. Provides normalizeAlias() method to handle contact display names, falling back to emoji-based default aliases from wallet addresses when user-defined alias is missing. Uses ResourceManager for localized string formatting. Dependencies: TariWalletAddress model and resource management. Used throughout contact management features for consistent alias display. |
| `DebugConfig.kt` | Comprehensive debug configuration and mock data utilities for development and testing. Contains debug flags for enabling mock transactions, UTXOs, chat messages, network settings, and other features. MockDataStub object provides factory methods for creating test data including contacts, transactions, UTXOs, and chat messages. YatEnvironment defines sandbox vs production YAT service endpoints. Critical for development testing and debugging wallet functionality without real blockchain data. |
| `EffectFlow.kt` | Reactive flow utilities for handling one-time effects and events in MVVM architecture. EffectFlow provides single-collector effect delivery using Channel. BroadcastEffectFlow supports multiple collectors using SharedFlow. Both classes facilitate communication between ViewModels and UI components for actions like navigation, snackbar messages, or dialogs. Essential for maintaining clean separation between business logic and UI layers. |
| `EmojiUtil.kt` | Comprehensive emoji processing utilities for Tari's emoji ID system with validation, formatting, and styling capabilities. Extensive utility functions for emoji manipulation and display. Exports: containsNonEmoji(), formatting functions, SpannableString creation, emoji validation, chunking utilities. Dependencies: FFIEmojiSet, BreakIterator, SpannableString styling. Used by: Emoji ID processing throughout the app. Features emoji set validation against Tari's official emoji set, sophisticated text formatting with color and spacing styles, chunked emoji display with separators, non-emoji character detection, and comprehensive SpannableString creation. Licensed under Tari Project BSD license. Essential for Tari's emoji-based address system. |
| `HashcodeUtils.kt` | Utility object for generating hash codes from multiple objects. Provides generate() method that combines hashCodes of multiple arguments using the standard 31-prime multiplication approach. Used for creating composite hash codes in data classes and objects where multiple properties need to be combined for equality and hashing operations. Ensures consistent hashing behavior across the application. |
| `QrUtil.kt` | QR code generation utility using ZXing library for wallet address and data encoding. Exports: QrUtil object with bitmap generation methods. Provides getQrEncodedBitmap() for throwing version and getQrEncodedBitmapOrNull() for safe version with error handling. Uses UTF-8 encoding and configurable size parameters. Used throughout the app for sharing wallet addresses and transaction data via QR codes. |

###### app/src/main/java/com/tari/android/wallet/util/extension/

| File | Description |
|------|-------------|
| `ActivityExtensions.kt` | Activity extension functions for common UI operations and lifecycle management. Provides keyboard show/hide utilities, coroutine launch helpers with lifecycle awareness (launchOnIo, launchOnMain), and ActivityResult convenience methods. Extensions include hideKeyboard(), showKeyboard(), resultOk(), and dataIfOk() for cleaner activity code. Used throughout fragments and activities for consistent keyboard management and lifecycle-aware coroutine execution. |
| `AnyExtensions.kt` | Type casting extension functions for Any objects. Provides castTo() for unsafe casting, safeCastTo() for safe casting with null return, and takeIfIs() for conditional casting. Enables chained casting operations with better readability and type safety. Used throughout the codebase for safe type conversions, particularly when working with polymorphic objects and different transaction types. |
| `BooleanExtensions.kt` | Boolean extension functions for nullable Boolean handling. Provides isTrue(), isFalse(), isNotTrue(), and isNotFalse() methods for safe nullable Boolean operations. Enables cleaner conditional logic when working with Boolean? types throughout the application. Used for null-safe Boolean checks and conditional expressions where Boolean nullability needs explicit handling. |
| `CollectionExtensions.kt` | Collection manipulation extension functions for lists and mutable lists. Provides repopulate() for bulk replacement, replaceItem() for conditional replacement, replaceOrAddItem() for upsert operations, withItem()/withoutItem() for immutable list operations. Enables functional programming patterns and cleaner collection manipulation throughout the app, particularly for managing UI state and data transformations. |
| `ContextExtensions.kt` | Context extension functions for Android resource access and utilities. Provides convenient methods for string(), color(), dimen(), drawable() resource retrieval with type safety. Includes dimension conversion helpers (dpToPx, dimenPx) and activity lifecycle checks (isStillAlive). Simplifies resource access patterns throughout the app and reduces boilerplate code for common Context operations. |
| `DateExtensions.kt` | Date and time formatting extension functions. Provides txFormattedDate() for transaction date display with ordinal indicators, formatToRelativeTime() for human-readable time differences, isAfterNow() for calendar comparisons, and minusHours() for date arithmetic. Used across transaction lists, details screens, and chat features for consistent date/time presentation and user-friendly relative time display. |
| `DisposableExtension.kt` | RxJava Disposable extension functions for memory management. Provides addTo() method for convenient addition of Disposable objects to CompositeDisposable for batch disposal. Used throughout the app for managing RxJava subscriptions and preventing memory leaks by grouping disposables for lifecycle-aware cleanup in reactive programming patterns. |
| `FileExtensions.kt` | File utility extension functions for backup and security operations. Provides extension property for file extensions, getLastPathComponent() for filename extraction, and encrypt() method for file encryption using SymmetricEncryptionAlgorithm. Used primarily for wallet backup functionality and secure file operations. Critical for protecting sensitive wallet data during backup and restore processes. |
| `FlowExtensions.kt` | Kotlin Flow extension functions for lifecycle-aware coroutine collection. Provides collectFlow() methods for Activity, Fragment, ViewModel, and CoroutineScope with error handling. Includes collectNonNullFlow() for non-null filtering, combineToPair()/zipToPair() for flow combination, and lifecycle-aware collection with automatic cancellation. Essential for reactive UI updates and state management in MVVM architecture. |
| `FragmentExtensions.kt` | Fragment extension functions for resource access and Compose integration. Provides convenient methods for string(), color(), dimen(), drawable() resource retrieval and composeContent() for Jetpack Compose integration with proper lifecycle management. Simplifies resource access in fragments and enables seamless compose/fragment interoperability with ViewCompositionStrategy.DisposeOnLifecycleDestroyed for proper cleanup. |
| `NumericExtensions.kt` | Numeric conversion extension functions for Tari cryptocurrency operations. Provides toMicroTari() conversions from Int, Long, and BigInteger to MicroTari model objects. Includes parseToBigInteger() for safe string parsing and flag() for byte bitmask operations. Critical for handling Tari currency amounts, transaction values, and fee calculations throughout the wallet application with proper precision and type safety. |
| `ParcelableExtensions.kt` | Parcelable and Serializable extension functions for Android API compatibility. Provides type-safe parcelable() and serializable() methods for Intent, Bundle, and SavedStateHandle with proper API level handling for Android 33+ changes. Includes getOrThrow() for required SavedStateHandle parameters. Essential for safe data passing between activities, fragments, and ViewModels across different Android versions. |
| `StringExtensions.kt` | Comprehensive string processing extensions for typography, styling, and cryptographic operations. Exports: 160+ lines of string extension functions. Includes applyFontStyle(), applyColorStyle(), emoji processing, text chunking, validation utilities, and cryptographic functions (SHA256, hex conversion). Provides consistent text styling throughout the app with Tari typography system integration. Essential utilities for address display, form validation, and secure string operations. |
| `ViewExtensions.kt` | Comprehensive View extension functions providing common UI manipulation utilities. Collection of extension functions for View classes covering visibility, layout, animation, and styling. Exports: visibility helpers (visible, gone, invisible), layout manipulation (margins, dimensions), animation utilities, progress bar styling, scroll view helpers, throttled click listeners. Dependencies: Android animation framework, view system. Used by: Throughout the app for consistent UI manipulation. Features convenient visibility state management, layout parameter helpers, animation creation utilities, dp/px conversion functions, and safe UI operation wrappers. Licensed under Tari Project BSD license. Essential for clean and consistent UI code patterns. |
| `ViewHolderExtensions.kt` | RecyclerView ViewHolder extension functions for resource access. Provides convenient methods for dimen(), string(), color(), colorFromAttribute(), and drawable() resource retrieval through itemView context. Simplifies resource access within ViewHolder implementations and reduces boilerplate code for common resource operations in RecyclerView adapters and ViewHolders throughout the application. |
| `ViewModelExtension.kt` | ViewModel and LiveData extension functions for MVVM pattern support. Provides safe observe() methods for Fragment, Activity, and CommonView with exception handling. Includes debounce() for LiveData rate limiting, coroutine launch helpers (launchOnIo, launchOnMain), and context switching utilities (switchToIo, switchToMain). Critical for maintaining clean MVVM architecture and preventing UI crashes from observer exceptions. |
| `WalletExtension.kt` | FFIWallet extension functions for safe wallet operations and error handling. Provides getWithError() for wallet operations with custom error handling and getOrNull() for optional operations. Includes error mapping from FFIException to WalletError. Essential for safe interaction with the native Rust wallet library through FFI, preventing crashes and providing structured error handling throughout wallet operations. |

###### app/src/main/res/anim/

| File | Description |
|------|-------------|
| `enter_from_left.xml` | Animation resource for screen transition entering from left. Defines translate animation from -20% X position to 0% combined with alpha fade from 0.2 to 1.0 opacity. Uses medium animation duration with accelerate-decelerate interpolator for smooth transitions. Used in navigation framework for fragment/activity transitions and slide-in effects. |
| `enter_from_right.xml` | Animation resource for screen transition entering from right. Defines translate animation from 20% X position to 0% combined with alpha fade from 0.2 to 1.0 opacity. Uses medium animation duration with accelerate-decelerate interpolator for smooth transitions. Complements enter_from_left for bidirectional navigation animations. |
| `exit_to_left.xml` | Animation resource for screen transition exiting to left. Defines translate animation from 0% to -20% X position combined with alpha fade from 1.0 to 0.2 opacity. Uses medium animation duration with accelerate-decelerate interpolator. Pairs with enter_from_right for coordinated navigation transitions when moving backward through screens. |
| `exit_to_right.xml` | Animation resource for screen transition exiting to right. Defines translate animation from 0% to 20% X position combined with alpha fade from 1.0 to 0.2 opacity. Uses medium animation duration with accelerate-decelerate interpolator. Pairs with enter_from_left for coordinated navigation transitions when moving forward through screens. |
| `fade_in.xml` | Simple fade-in animation resource. Defines alpha animation from 0.0 to 1.0 opacity using medium animation duration with accelerate-decelerate interpolator. Used for smooth element appearances, dialog transitions, and general fade-in effects throughout the application UI. Provides consistent timing and easing for opacity transitions. |
| `fade_out.xml` | Simple fade-out animation resource. Defines alpha animation from 1.0 to 0.0 opacity using medium animation duration with accelerate-decelerate interpolator. Complements fade_in animation for element disappearances, dialog dismissals, and general fade-out effects. Provides consistent timing and easing for smooth UI transitions. |

###### app/src/main/res/color/

| File | Description |
|------|-------------|
| `button_text_color_selector.xml` | Color selector for button text states. Defines enabled state using palette_button_primary_text theme attribute and disabled state using palette_button_disabled_text. Provides consistent button text colors across different UI states and supports theme-based color schemes. Used throughout the app for maintaining button text accessibility and visual consistency. |
| `toolbar_text_color_selector.xml` | Color selector for toolbar text states. Defines enabled and disabled text colors for toolbar elements using theme attributes. Ensures consistent toolbar text appearance across different states and themes. Used by toolbar components, action items, and navigation elements to maintain proper contrast and accessibility in the app's navigation system. |

###### app/src/main/res/drawable/

| File | Description |
|------|-------------|
| `tari_active_miners_card_ellipse.xml` | Vector drawable for active miners card elliptical background element. Semi-transparent cyan (#38FFD1) elliptical shape with 44% opacity used as a decorative background element for mining-related UI cards. Dimensions: 370x76dp with elliptical path creating a wide oval shape. Used by: Active miners card components and mining-related UI screens. Provides subtle visual accent with Tari brand colors for highlighting mining activity status in the wallet interface. |
| `tari_empty_drawable.xml` | Empty vector drawable placeholder for UI components requiring drawable resources. Transparent drawable used as a placeholder or spacer in layouts where a drawable reference is required but no visual content is desired. Used by: Various UI components needing null drawable references. Provides clean way to satisfy drawable requirements without adding visual content. |
| `tari_sample_avatar.xml` | Vector drawable representing a sample avatar with gem/crystal design. White geometric shape (16x16dp) depicting a stylized gem or crystal structure, used as placeholder avatar or icon in profile contexts. Complex path creates faceted gem appearance. Used by: Avatar placeholders, profile images, user representation in UI. Provides default visual element for user identification when no custom avatar is available. |
| `tari_splash_screen.xml` | Vector drawable for app splash screen logo or branding element. Used during app launch and loading states to display Tari wallet branding. Provides consistent visual identity during app initialization. Used by: Splash screen activity, loading states, app launch sequence. Ensures branded experience during app startup and loading processes. |
| `tari_staged_question.xml` | Vector drawable icon for staged/pending question or help state. Used to indicate awaiting user input or pending question status in staged transaction flows. Provides visual feedback for interactive elements requiring user attention or decision. Used by: Transaction staging UI, help states, pending action indicators. Part of the transaction flow iconography for user guidance. |
| `tari_switch_compat_selector_thumb.xml` | Drawable selector for switch component thumb (toggle button) with state management. Provides different visual states for switch thumb element based on checked/unchecked and enabled/disabled states using theme colors. Used by: Switch components, toggle controls throughout the app. Ensures consistent switch styling with proper accessibility states and theme integration for toggle UI elements. |
| `tari_switch_compat_selector_track.xml` | Drawable selector for switch component track (background rail) with state management. Provides different visual states for switch track element based on checked/unchecked and enabled/disabled states using theme colors. Used by: Switch components, toggle controls throughout the app. Ensures consistent switch track styling with proper accessibility states and theme integration for toggle UI backgrounds. |
| `vector_add_contact.xml` | Vector drawable icon for adding new contacts functionality. Black icon (18x20dp) depicting a person silhouette with a plus symbol, used for add contact actions throughout the app. Complex path data creates detailed person icon with plus overlay indicating add/create action. Used by: Contact management screens, add contact buttons, contact-related UI elements. Provides clear visual indication for contact creation functionality in the wallet's contact book system. |
| `vector_add_note_and_send_slide_button_disabled_bg.xml` | Background drawable for disabled state of add note and send slide button. Provides visual styling for slide-to-send button when in disabled state, typically with muted colors and reduced opacity. Used by: Transaction send flow, slide button components. Ensures proper disabled state feedback for slide-to-send interactions in payment flows. |
| `vector_add_note_and_send_slide_button_enabled_bg.xml` | Background drawable for enabled state of add note and send slide button. Provides visual styling for slide-to-send button when active and ready for interaction, typically with vibrant colors and full opacity. Used by: Transaction send flow, slide button components. Ensures clear enabled state indication for slide-to-send interactions in payment flows. |
| `vector_add_note_slide_button_bg.xml` | Background drawable for add note slide button component. Provides visual styling for slide button specifically for adding notes to transactions. Used by: Transaction note flow, slide button components for note addition. Ensures consistent styling for slide-to-add-note interactions in transaction creation workflows. |
| `vector_all_settings_about_icon.xml` | Vector drawable icon for about/information section in settings. Black circular icon (22x22dp) with speech bubble design containing an info symbol (exclamation mark in circle), used for about and information menu items. Complex paths create detailed chat bubble with info indicator. Used by: Settings screen about section, information dialogs, help-related UI elements. Provides clear visual representation for accessing app information, version details, and general about content. |
| `vector_all_settings_background_service_icon.xml` | Vector drawable icon for background service settings option. Icon representing background service configuration in the settings menu, used for managing app background operations and service behavior. Used by: Settings screen background service section, service management UI. Provides clear visual representation for accessing background service preferences and configuration options. |
| `vector_all_settings_backup_options_icon.xml` | Vector drawable icon for backup options in settings menu. Icon representing wallet backup and recovery settings, used for accessing backup creation, restoration, and management features. Used by: Settings screen backup section, backup/recovery UI. Provides clear visual representation for accessing wallet backup options, seed phrase management, and recovery functionality. |
| `vector_all_settings_block_explorer_icon.xml` | Vector drawable icon for block explorer settings option. Icon representing blockchain explorer configuration in the settings menu, used for managing block explorer preferences and external blockchain browsing options. Used by: Settings screen block explorer section, blockchain browsing UI. Provides clear visual representation for accessing block explorer settings and blockchain inspection tools. |
| `vector_all_settings_bridge_configuration_icon.xml` | Vector drawable icon for Tor bridge configuration in settings menu. Icon representing Tor bridge setup and configuration options, used for managing privacy network connections and custom bridge settings. Used by: Settings screen Tor bridge section, privacy configuration UI. Provides clear visual representation for accessing Tor bridge configuration, custom bridge setup, and network privacy options. |
| `vector_all_settings_cart.xml` | Vector drawable icon for shopping cart functionality in settings or main UI. Icon representing shop, marketplace, or commerce features within the wallet application. Used by: Shopping features, marketplace integration, commerce-related UI elements. Provides clear visual representation for accessing shopping or marketplace functionality integrated with the wallet. |
| `vector_all_settings_contribute_to_tari_icon.xml` | Vector drawable icon for contribute to Tari option in settings menu. Icon representing community contribution, development participation, or support for the Tari project. Used by: Settings screen contribution section, community engagement UI. Provides clear visual representation for accessing ways to contribute to the Tari ecosystem, whether through development, testing, or community support. |
| `vector_all_settings_data_collection.xml` | Vector drawable icon for data collection settings option. Icon representing privacy settings, analytics preferences, and data collection management in the settings menu. Used by: Settings screen privacy section, data collection preferences UI. Provides clear visual representation for accessing data collection settings, analytics opt-in/out, and privacy control options. |
| `vector_all_settings_delete_button_icon.xml` | Vector drawable icon for delete/removal actions in settings. Destructive action icon used for wallet deletion, data removal, or other permanent deletion operations in settings. Used by: Settings destructive actions, wallet deletion UI, data removal confirmations. Provides clear visual warning for irreversible delete operations and data removal actions. |
| `vector_all_settings_disclaimer_icon.xml` | Vector drawable icon for disclaimer section in settings menu. Icon representing legal disclaimers, terms of use, or important notices that users should be aware of. Used by: Settings screen legal section, disclaimer display UI. Provides clear visual representation for accessing legal disclaimers, warnings, and important legal information related to wallet usage. |
| `vector_all_settings_passcode.xml` | Vector drawable icon for passcode/security settings option. Icon representing passcode configuration, PIN setup, and security authentication settings in the settings menu. Used by: Settings screen security section, passcode configuration UI. Provides clear visual representation for accessing passcode settings, PIN management, and authentication security options. |
| `vector_all_settings_privacy_policy_icon.xml` | Vector drawable icon for privacy policy section in settings menu. Icon representing privacy policy document access, privacy information, and data usage policies. Used by: Settings screen privacy section, privacy policy display UI. Provides clear visual representation for accessing privacy policy documents, data usage information, and privacy-related legal content. |
| `vector_all_settings_report_bug_icon.xml` | Vector drawable icon for bug reporting functionality in settings menu. Icon representing bug report submission, issue reporting, and feedback mechanisms for reporting problems to developers. Used by: Settings screen support section, bug reporting UI. Provides clear visual representation for accessing bug reporting features, issue submission, and developer feedback channels. |
| `vector_all_settings_screen_recording_icon.xml` | Vector drawable icon for screen recording protection settings. Icon representing screen recording prevention, screenshot blocking, and privacy protection features in the settings menu. Used by: Settings screen privacy section, screen recording prevention UI. Provides clear visual representation for accessing screen recording protection settings and privacy safeguards against unauthorized screen capture. |
| `vector_all_settings_select_base_node_icon.xml` | Vector drawable icon for base node selection in settings menu. Icon representing blockchain base node configuration, network node selection, and blockchain connectivity settings. Used by: Settings screen network section, base node configuration UI. Provides clear visual representation for accessing base node selection, blockchain network configuration, and node connectivity preferences. |
| `vector_all_settings_select_network_icon.xml` | Vector drawable icon for network selection in settings menu. Icon representing blockchain network configuration, testnet/mainnet selection, and network environment switching. Used by: Settings screen network section, network selection UI. Provides clear visual representation for accessing network selection options, switching between different blockchain networks, and network environment configuration. |
| `vector_all_settings_select_theme_icon.xml` | Vector drawable icon for theme selection in settings menu. Icon representing UI theme configuration, dark/light mode selection, and appearance customization settings. Used by: Settings screen appearance section, theme selection UI. Provides clear visual representation for accessing theme options, appearance customization, and UI styling preferences. |
| `vector_all_settings_user_agreement_icon.xml` | Vector drawable icon for user agreement section in settings menu. Icon representing terms of service, user agreements, and legal terms access in the settings. Used by: Settings screen legal section, user agreement display UI. Provides clear visual representation for accessing terms of service, user agreements, and legal documentation related to wallet usage. |
| `vector_all_settings_visit_tari_icon.xml` | Vector drawable icon for visiting Tari website/resources in settings menu. Icon representing external links to Tari project website, documentation, or official resources. Used by: Settings screen information section, external link UI. Provides clear visual representation for accessing official Tari resources, project website, and external documentation links. |
| `vector_all_settings_yat_icon.xml` | Vector drawable icon for YAT (Your Alias Token) settings in settings menu. Icon representing YAT integration, alias token configuration, and YAT-related features. Used by: Settings screen YAT section, YAT configuration UI. Provides clear visual representation for accessing YAT settings, alias token management, and YAT service integration options. |
| `vector_back_button.xml` | Vector drawable icon for back navigation button. Standard back arrow icon used throughout the app for returning to previous screens or closing dialogs. Used by: Navigation bars, toolbars, dialog headers, and back navigation throughout the app. Provides consistent navigation experience and clear affordance for returning to previous screens. |
| `vector_backup_onboarding_cloud.xml` | Vector drawable icon for cloud backup in onboarding flow. Cloud icon representing cloud storage backup options during wallet setup and onboarding process. Used by: Backup onboarding screens, cloud backup selection UI, wallet setup flow. Provides clear visual representation for cloud-based backup options like Google Drive or other cloud storage services during initial wallet configuration. |
| `vector_backup_onboarding_final.xml` | Vector drawable icon for final backup confirmation in onboarding flow. Icon representing completion or final step in the backup setup process during wallet onboarding. Used by: Backup onboarding completion screens, final backup confirmation UI, wallet setup completion. Provides visual confirmation that backup setup has been completed successfully during initial wallet configuration. |
| `vector_backup_onboarding_icons_back.xml` | Vector drawable back navigation icon for backup onboarding flow. Specialized back button icon used specifically within the backup setup and onboarding screens. Used by: Backup onboarding navigation, wallet setup flow navigation, backup configuration screens. Provides consistent back navigation within the backup setup process during initial wallet configuration. |
| `vector_backup_onboarding_password.xml` | Vector drawable icon for password/security step in backup onboarding flow. Icon representing password creation, security setup, or encryption configuration during backup setup process. Used by: Backup onboarding password screens, security configuration UI, wallet setup security steps. Provides clear visual representation for password and security configuration during backup setup in initial wallet onboarding. |
| `vector_backup_onboarding_seed_words.xml` | Vector drawable icon for seed words/recovery phrase in backup onboarding flow. Icon representing seed phrase creation, recovery words backup, or mnemonic phrase configuration during wallet setup. Used by: Backup onboarding seed phrase screens, recovery phrase UI, wallet setup recovery steps. Provides clear visual representation for seed phrase and recovery word configuration during backup setup in initial wallet onboarding. |
| `vector_block_synced.xml` | Vector drawable icon indicating blockchain synchronization completion. Icon representing successful blockchain sync, up-to-date blockchain state, or completed network synchronization. Used by: Network status indicators, sync status UI, blockchain state displays. Provides clear visual feedback when the wallet is fully synchronized with the blockchain network and ready for transactions. |
| `vector_chat_add.xml` | Vector drawable icon for adding new chat or message functionality. Plus or add icon specifically for chat/messaging features, used to initiate new conversations or add chat participants. Used by: Chat interface, messaging screens, contact communication UI. Provides clear visual indication for starting new conversations or adding messaging functionality within the wallet's communication features. |
| `vector_chat_empty_list.xml` | Vector drawable icon for empty chat list state. Illustration or icon displayed when there are no active chats or conversations in the messaging interface. Used by: Chat list empty states, messaging empty views, conversation list placeholders. Provides visual feedback when no conversations exist and encourages users to start new chats or conversations. |
| `vector_chat_empty_messages.xml` | Vector drawable icon for empty messages state within a chat. Illustration or icon displayed when a conversation exists but contains no messages yet. Used by: Chat conversation empty states, message thread empty views, conversation placeholders. Provides visual feedback when a conversation is open but no messages have been exchanged, encouraging users to start the conversation. |
| `vector_chat_input_attach.xml` | Vector drawable icon for attaching files or media in chat input. Attachment/paperclip icon used in messaging interface to indicate file attachment functionality. Used by: Chat input controls, messaging attachment UI, file sharing in conversations. Provides clear visual indication for adding attachments, files, or media to chat messages. |
| `vector_chat_input_send.xml` | Vector drawable icon for sending messages in chat input. Send/paper plane icon used in messaging interface to submit or send chat messages. Used by: Chat input controls, messaging send buttons, conversation UI. Provides clear visual indication for sending messages and completing message composition in chat interfaces. |
| `vector_chat_unread_count.xml` | Vector drawable background for unread message count indicators in chat. Badge or bubble background used to display unread message counts in chat lists and conversation indicators. Used by: Chat list items, unread badges, notification indicators, conversation list UI. Provides visual emphasis for unread message counts and new message notifications in messaging interface. |
| `vector_check_mark_bg.xml` | Vector drawable background for checkmark/confirmation indicators. Background element used behind checkmark icons to provide visual emphasis for successful actions, confirmations, and completed states. Used by: Success indicators, confirmation UI, completed transaction states, validation feedback. Provides consistent background styling for positive confirmation states throughout the app. |
| `vector_circle_done.xml` | Vector drawable circular checkmark icon indicating completion or success. Circular background with checkmark symbol used for completed actions, successful operations, and positive confirmations. Used by: Success states, completed transactions, confirmation dialogs, progress indicators. Provides clear visual feedback for successful completion of actions and processes throughout the application. |
| `vector_close.xml` | Vector drawable close/dismiss icon. Standard X or cross icon used for closing dialogs, dismissing overlays, canceling operations, and general close actions throughout the app. Used by: Dialog close buttons, modal dismissal, cancel actions, overlay close controls. Provides consistent close affordance across all dismissible UI elements and interactive overlays. |
| `vector_close_error.xml` | Vector drawable close icon specifically for error states. Close/X icon with error styling used for dismissing error messages, error dialogs, and failure notifications. Used by: Error dialog close buttons, error message dismissal, failure state UI, error notification controls. Provides contextually appropriate close affordance for error and failure scenarios with distinctive styling. |
| `vector_close_opposite.xml` | Vector drawable alternative close icon with inverted or opposite styling. Close/X icon with alternate visual treatment for use in different contexts or themes. Used by: Close buttons in dark themes, contrasting backgrounds, alternate UI states, inverted color schemes. Provides close affordance with styling that contrasts appropriately with different background contexts and color schemes. |
| `vector_closed_eye.xml` | Vector drawable closed eye icon for hiding/obscuring content. Eye icon with closed or crossed-out state used for hiding sensitive information, toggling password visibility, or concealing private data. Used by: Password visibility toggles, balance hiding, private information controls, sensitive data masking. Provides clear visual indication for hidden or concealed content states in privacy-sensitive contexts. |
| `vector_common_close.xml` | Vector drawable common close icon for general dismissal actions. Standard close/X icon used consistently across common UI elements for closing, dismissing, or canceling operations. Used by: General close buttons, common dismissal actions, standard modal close controls, generic cancel operations. Provides unified close affordance for standard UI interactions throughout the application. |
| `vector_contact_action_details.xml` | Vector drawable icon for viewing contact details action. Info or details icon used in contact management for accessing detailed contact information and contact profiles. Used by: Contact action menus, contact detail buttons, contact management UI, contact profile access. Provides clear visual indication for viewing comprehensive contact information and accessing contact detail screens. |
| `vector_contact_action_link.xml` | Vector drawable icon for linking contact action. Link or connection icon used in contact management for creating associations between wallet contacts and device contacts. Used by: Contact action menus, contact linking UI, contact association controls, phone contact integration. Provides clear visual indication for establishing links between different contact sources and creating contact associations. |
| `vector_contact_action_send.xml` | Vector drawable icon for sending transactions to contacts. Send or payment icon used in contact actions for initiating transactions or payments to specific contacts. Used by: Contact action menus, quick send buttons, contact transaction UI, payment initiation controls. Provides clear visual indication for sending Tari to contacts directly from contact management interface. |
| `vector_contact_action_to_favorites.xml` | Vector drawable icon for adding contacts to favorites. Star, heart, or favorite icon used in contact actions for marking contacts as favorites or frequently used contacts. Used by: Contact action menus, favorite buttons, contact preference controls, quick access management. Provides clear visual indication for adding contacts to favorites list for easy access and priority display. |
| `vector_contact_action_to_unfavorites.xml` | Vector drawable icon for removing contacts from favorites. Unfavorite, unstar, or remove favorite icon used in contact actions for removing contacts from favorites list. Used by: Contact action menus, unfavorite buttons, contact preference controls, favorites management. Provides clear visual indication for removing contacts from favorites and managing priority contact lists. |
| `vector_contact_action_unlink.xml` | Vector drawable icon for unlinking contact associations. Unlink or disconnect icon used in contact actions for breaking associations between wallet contacts and device contacts. Used by: Contact action menus, unlink buttons, contact association controls, phone contact separation. Provides clear visual indication for removing links between different contact sources and breaking contact associations. |
| `vector_contact_book_icon.xml` | Vector drawable icon representing contact book or address book functionality. Book or contacts icon used for accessing contact management features and contact lists throughout the app. Used by: Contact book navigation, contact management UI, address book access, contact-related screens. Provides clear visual representation for contact management and address book functionality in the wallet application. |
| `vector_contact_book_type.xml` | Vector drawable icon for contact book type classification. Icon used to distinguish different types of contact books or contact sources (device contacts, wallet contacts, etc.). Used by: Contact type indicators, contact source UI, contact classification, contact management categorization. Provides visual distinction between different contact types and sources in contact management interface. |
| `vector_contact_empty_state.xml` | Vector drawable illustration for empty contact list state. Empty state graphic displayed when no contacts are available in the contact list or contact book. Used by: Contact list empty views, contact book empty states, no contacts placeholders. Provides visual feedback when contact list is empty and encourages users to add contacts or import from device contacts. |
| `vector_contact_favorite_empty_state.xml` | Vector drawable illustration for empty favorites contact list state. Empty state graphic displayed when no favorite contacts are available in the favorites section. Used by: Favorite contacts empty views, favorites list empty states, no favorites placeholders. Provides visual feedback when favorites list is empty and encourages users to mark contacts as favorites. |
| `vector_contact_type_link.xml` | Vector drawable icon for linked contact type indicator. Link icon used to show when a wallet contact is linked or associated with a device contact. Used by: Contact type indicators, linked contact UI, contact association status, contact relationship indicators. Provides visual indication of contact linking status and associations between wallet and device contacts. |
| `vector_copy_paste_emoji_id_button_bg.xml` | Vector drawable background for copy/paste emoji ID button functionality. Background styling for buttons used to copy emoji IDs or paste emoji ID addresses in transaction and contact flows. Used by: Emoji ID copy buttons, paste buttons, address sharing UI, emoji ID input controls. Provides consistent button styling for emoji ID-related copy/paste operations throughout the wallet interface. |
| `vector_delete_button.xml` | Vector drawable icon for delete operations and destructive actions. Delete, trash, or remove icon used for permanent deletion operations throughout the app. Used by: Delete buttons, remove actions, destructive operation confirmations, data deletion UI. Provides clear visual warning for irreversible delete operations and data removal actions across the application. |
| `vector_destructive_action_button_bg.xml` | Drawable selector for destructive action button backgrounds with warning styling. Background for buttons performing dangerous or irreversible operations like deletion, with distinctive warning colors and styling. Used by: Delete buttons, destructive action confirmations, warning action buttons, dangerous operation UI. Provides appropriate visual emphasis for destructive actions with warning colors and proper accessibility states. |
| `vector_disable_able_gradient_button_bg.xml` | Drawable selector for gradient buttons with enable/disable state management. Advanced gradient button background that changes appearance based on enabled/disabled state with smooth gradient transitions. Used by: Primary gradient buttons, interactive action buttons, state-sensitive controls. Provides enhanced button styling with gradient effects and proper accessibility states for improved user experience. |
| `vector_emoji_id_bg.xml` | Vector drawable background for emoji ID display components. Background styling for emoji ID representations, used behind emoji ID text or visual displays throughout the app. Used by: Emoji ID displays, address representations, contact emoji IDs, transaction address UI. Provides consistent background styling for emoji ID components and address displays across the wallet interface. |
| `vector_empty_wallet.xml` | Vector drawable illustration for empty wallet state. Empty state graphic displayed when wallet has no balance, no transactions, or when wallet is newly created. Used by: Empty wallet views, zero balance states, new wallet placeholders, transaction list empty states. Provides visual feedback for empty wallet conditions and encourages users to receive their first Tari or explore wallet features. |
| `vector_fingerprint.xml` | Vector drawable fingerprint icon for biometric authentication. Fingerprint symbol used for biometric login, fingerprint unlock, and biometric security features throughout the app. Used by: Biometric authentication UI, fingerprint login screens, security settings, authentication prompts. Provides clear visual indication for biometric authentication options and fingerprint-based security features. |
| `vector_gem.xml` | Vector drawable gem/diamond icon representing rewards, gems, or special features. Geometric gem symbol used throughout the app for gems functionality, rewards system, or premium features. Used by: Gems navigation, rewards UI, special features, premium content indicators. Provides clear visual representation for gem-related features and reward systems in the wallet application. |
| `vector_gradient_button_bg.xml` | Drawable selector for primary gradient button backgrounds with state management. Responsive button background that changes appearance based on enabled/disabled state using theme colors. Enabled state uses primary button color, disabled state uses button disable color, both with 56dp corner radius for modern rounded button design. Used by: Primary action buttons, gradient buttons throughout the app. Provides consistent button styling with proper accessibility states and theme integration. |
| `vector_help_icon_24dp.xml` | Vector drawable help/support icon in 24dp size. Question mark or help symbol used for accessing help documentation, support resources, and assistance features. Used by: Help buttons, support UI, assistance features, documentation access, FAQ links. Provides clear visual indication for accessing help and support resources throughout the application. |
| `vector_home_nav_menu_gem.xml` | Vector drawable icon for gem/rewards section in home navigation menu. Outlined gem/diamond icon (26x26dp) with stroke design in dark blue (#141B34), featuring geometric gem shape with horizontal line detail. Used by: Bottom navigation bar, home screen navigation, rewards/gems section. Provides clear visual representation for accessing gem-related features, rewards, or special content in the wallet application. Part of navigation icon set for main app sections. |
| `vector_home_nav_menu_gem_filled.xml` | Vector drawable filled gem icon for active gems navigation tab. Filled version of gem icon used in bottom navigation when gems section is selected/active. Used by: Bottom navigation active state, gems section selected indicator, active tab highlighting. Provides clear visual feedback for active gems navigation state in the main app navigation system. |
| `vector_home_nav_menu_home.xml` | Vector drawable home icon for home navigation tab. Outlined home/house icon used in bottom navigation for accessing the main wallet home screen. Used by: Bottom navigation home tab, main screen navigation, wallet home access. Provides clear visual representation for returning to the main wallet home screen and primary app functionality. |
| `vector_home_nav_menu_home_filled.xml` | Vector drawable filled home icon for active home navigation tab. Filled version of home icon used in bottom navigation when home section is selected/active. Used by: Bottom navigation active state, home section selected indicator, active tab highlighting. Provides clear visual feedback for active home navigation state in the main app navigation system. |
| `vector_home_nav_menu_profile.xml` | Vector drawable profile icon for profile navigation tab. Outlined person/user icon used in bottom navigation for accessing profile, account, and user-related features. Used by: Bottom navigation profile tab, user profile access, account management navigation. Provides clear visual representation for accessing user profile and account management features. |
| `vector_home_nav_menu_profile_filled.xml` | Vector drawable filled profile icon for active profile navigation tab. Filled version of profile icon used in bottom navigation when profile section is selected/active. Used by: Bottom navigation active state, profile section selected indicator, active tab highlighting. Provides clear visual feedback for active profile navigation state in the main app navigation system. |
| `vector_home_nav_menu_settings.xml` | Vector drawable settings icon for settings navigation tab. Outlined gear/cog icon used in bottom navigation for accessing app settings, preferences, and configuration options. Used by: Bottom navigation settings tab, settings screen access, configuration navigation. Provides clear visual representation for accessing wallet settings and app configuration options. |
| `vector_home_nav_menu_settings_filled.xml` | Vector drawable filled settings icon for active settings navigation tab. Filled version of settings icon used in bottom navigation when settings section is selected/active. Used by: Bottom navigation active state, settings section selected indicator, active tab highlighting. Provides clear visual feedback for active settings navigation state in the main app navigation system. |
| `vector_home_nav_menu_shop.xml` | Vector drawable shop icon for shop navigation tab. Outlined shopping/store icon used in bottom navigation for accessing marketplace, shop, or commerce features. Used by: Bottom navigation shop tab, marketplace access, commerce navigation. Provides clear visual representation for accessing shopping and marketplace functionality integrated with the wallet. |
| `vector_home_nav_menu_shop_filled.xml` | Vector drawable filled shop icon for active shop navigation tab. Filled version of shop icon used in bottom navigation when shop section is selected/active. Used by: Bottom navigation active state, shop section selected indicator, active tab highlighting. Provides clear visual feedback for active shop navigation state in the main app navigation system. |
| `vector_home_overview_active_miners.xml` | Vector drawable icon for active miners display on home overview. Mining or blockchain icon used in home screen to show active mining status or mining-related information. Used by: Home screen mining indicators, mining status UI, blockchain activity displays. Provides visual representation of active mining operations and mining-related features in the wallet home interface. |
| `vector_home_overview_hide_balance.xml` | Vector drawable icon for hiding wallet balance on home overview. Eye with slash or hide icon used to conceal wallet balance from view for privacy. Used by: Home screen balance privacy, balance hiding toggle, privacy controls, sensitive information masking. Provides privacy control for hiding wallet balance and sensitive financial information on the main home screen. |
| `vector_icon_copy.xml` | Vector drawable copy icon for copying text, addresses, or data to clipboard. Copy/duplicate icon used throughout the app for copying wallet addresses, transaction IDs, emoji IDs, and other data. Used by: Copy buttons, address copying, clipboard operations, data sharing UI. Provides clear visual indication for copy-to-clipboard functionality across various data types in the wallet. |
| `vector_icon_edit_contact_pencil.xml` | Vector drawable pencil icon for editing contact information. Edit/pencil icon used for modifying contact details, names, and contact information. Used by: Contact edit buttons, contact modification UI, edit contact screens. Provides clear visual indication for editing and modifying contact information throughout the contact management system. |
| `vector_icon_open_url.xml` | Vector drawable icon for opening external URLs or links. External link or open icon used for navigating to external websites, documentation, or web resources. Used by: External link buttons, web navigation, documentation links, external resource access. Provides clear visual indication for opening external links and leaving the app to access web content. |
| `vector_icon_question_circle.xml` | Vector drawable question mark icon in circular background for help or information. Circular help icon used for contextual help, tooltips, and information displays throughout the app. Used by: Help buttons, information indicators, contextual help UI, tooltip triggers. Provides clear visual indication for accessing contextual help and additional information about features. |
| `vector_icon_send_tari.xml` | Vector drawable send icon specifically for Tari transactions. Send/payment arrow icon used for initiating Tari transfers and payments throughout the app. Used by: Send Tari buttons, payment initiation, transaction creation, quick send actions. Provides clear visual indication for sending Tari cryptocurrency and initiating payment transactions. |
| `vector_icon_smiley.xml` | Vector drawable smiley face icon for positive feedback or emoji ID context. Happy face icon used in emoji ID displays, positive feedback, or mood indicators. Used by: Emoji ID UI, positive state indicators, mood displays, satisfaction feedback. Provides visual element for positive emotions and emoji ID representation in wallet interface. |
| `vector_icon_smiley_strikethrough.xml` | Vector drawable smiley face with strikethrough for negative feedback or disabled emoji. Crossed-out happy face icon used for negative feedback, disabled emoji states, or unavailable features. Used by: Disabled emoji ID states, negative feedback UI, unavailable feature indicators. Provides visual element for negative states and disabled emoji functionality in wallet interface. |
| `vector_info_circle.xml` | Vector drawable information icon in circular background. Info 'i' symbol used for displaying additional information, help text, and informational messages throughout the app. Used by: Information buttons, help indicators, info dialogs, contextual information displays. Provides clear visual indication for accessing additional information and explanatory content. |
| `vector_introduction_tari_logo.xml` | Vector drawable Tari logo for introduction and onboarding screens. Official Tari project logo used in app introduction, onboarding flow, and branding elements. Used by: Introduction screens, onboarding flow, splash screens, branding displays. Provides official Tari brand identity and visual consistency in introductory and branding contexts throughout the app. |
| `vector_logs_filter.xml` | Vector drawable filter icon for log filtering functionality. Filter or funnel icon used in debug and logging screens to filter log entries by type, level, or content. Used by: Debug log screens, log filtering UI, developer tools, diagnostic interfaces. Provides clear visual indication for filtering and searching through application logs and debug information. |
| `vector_network_state_base_node.xml` | Vector drawable icon for base node network state indicator. Base node icon used to show blockchain base node connection status and network configuration. Used by: Network status indicators, base node connection UI, blockchain connectivity displays. Provides visual representation of base node connection state and blockchain network status. |
| `vector_network_state_full.xml` | Vector drawable icon for full network connectivity state. Full signal or complete connection icon used to show optimal network connectivity and complete synchronization. Used by: Network status indicators, connectivity displays, sync status UI, network health screens. Provides visual feedback for full network connectivity and complete blockchain synchronization. |
| `vector_network_state_limited.xml` | Vector drawable icon for limited network connectivity state. Limited signal or partial connection icon used to show restricted network connectivity or partial synchronization. Used by: Network status indicators, connectivity displays, sync status UI, network health screens. Provides visual feedback for limited network connectivity and partial blockchain synchronization. |
| `vector_network_state_off.xml` | Vector drawable icon for offline or disconnected network state. No signal or disconnected icon used to show complete network disconnection or offline status. Used by: Network status indicators, connectivity displays, offline mode UI, network health screens. Provides visual feedback for complete network disconnection and offline operating mode. |
| `vector_network_state_sync_status.xml` | Vector drawable icon for blockchain synchronization status display. Sync or refresh icon used to show blockchain synchronization progress and sync status. Used by: Sync status indicators, blockchain sync displays, network synchronization UI, progress indicators. Provides visual representation of blockchain synchronization process and sync progress status. |
| `vector_network_state_tor.xml` | Vector drawable icon for Tor network connectivity state. Tor onion icon used to show Tor network connection status and privacy network connectivity. Used by: Tor status indicators, privacy network displays, anonymity connection UI, network privacy screens. Provides visual representation of Tor network connection state and privacy network status. |
| `vector_network_state_wifi.xml` | Vector drawable icon for WiFi network connectivity state. WiFi signal icon used to show wireless network connection status and internet connectivity. Used by: Network status indicators, WiFi connectivity displays, internet connection UI, network type screens. Provides visual representation of WiFi network connection state and wireless internet connectivity. |
| `vector_network_status_dot_green.xml` | Vector drawable green status dot for positive network indicators. Green circular indicator used to show healthy network status, successful connections, or positive network states. Used by: Network status indicators, connection health displays, positive status UI, success state indicators. Provides visual feedback for healthy network conditions and successful connectivity states. |
| `vector_network_status_dot_red.xml` | Vector drawable red status dot for negative network indicators. Red circular indicator used to show network errors, failed connections, or problematic network states. Used by: Network status indicators, connection error displays, negative status UI, error state indicators. Provides visual feedback for network problems and failed connectivity states. |
| `vector_network_status_dot_yellow.xml` | Vector drawable yellow status dot for warning network indicators. Yellow circular indicator used to show network warnings, cautionary states, or moderate network issues. Used by: Network status indicators, connection warning displays, cautionary status UI, warning state indicators. Provides visual feedback for network warnings and moderate connectivity issues. |
| `vector_notification_icon.xml` | Vector drawable notification bell icon for push notifications and alerts. Bell or notification symbol used for notification features, alerts, and message indicators throughout the app. Used by: Notification buttons, alert indicators, push notification UI, message notifications. Provides clear visual indication for notifications, alerts, and communication features. |
| `vector_opened_eye.xml` | Vector drawable open eye icon for showing/revealing content. Open eye icon used for revealing hidden information, toggling password visibility, or showing private data. Used by: Password visibility toggles, balance showing, private information controls, sensitive data revealing. Provides clear visual indication for shown or revealed content states in privacy-sensitive contexts. |
| `vector_outline_done_24.xml` | Vector drawable outlined checkmark icon in 24dp size for completion indicators. Outlined check/tick symbol used for completed actions, success states, and confirmation feedback. Used by: Success indicators, completion confirmations, done states, validation feedback UI. Provides clear visual feedback for successful completion of actions and processes with outlined styling for better visibility. |
| `vector_pin_input_background.xml` | Vector drawable background for PIN input fields and authentication screens. Background styling for PIN entry interfaces, passcode inputs, and security authentication screens. Used by: PIN input screens, passcode entry UI, authentication backgrounds, security input interfaces. Provides consistent background styling for PIN and passcode input components throughout the security authentication system. |
| `vector_pin_input_dot_error.xml` | Vector drawable error state dot for PIN input visualization. Error-styled circular indicator used in PIN entry screens to show incorrect PIN input or authentication failures. Used by: PIN input error states, authentication failure UI, passcode error indicators, security input feedback. Provides visual feedback for incorrect PIN entries and authentication errors with distinctive error styling. |
| `vector_pin_input_dot_normal.xml` | Vector drawable normal state dot for PIN input visualization. Standard circular indicator used in PIN entry screens to represent PIN digit input states. Used by: PIN input displays, authentication UI, passcode entry indicators, security input visualization. Provides visual representation of PIN entry progress and digit input states in authentication interfaces. |
| `vector_plus.xml` | Vector drawable plus/add icon for general addition actions. Plus symbol used throughout the app for adding items, creating new entries, or positive actions. Used by: Add buttons, create actions, plus controls, addition operations throughout the app. Provides clear visual indication for adding or creating new content, items, or performing positive actions. |
| `vector_positive_check.xml` | Vector drawable positive checkmark icon for success and confirmation states. Positive check/tick symbol with success styling used for completed actions, verified states, and positive confirmations. Used by: Success confirmations, positive feedback UI, verification indicators, completion states. Provides clear visual feedback for successful actions and positive confirmation states with emphasis on success styling. |
| `vector_profile_invite_friend_copy.xml` | Vector drawable copy icon for inviting friends from profile screen. Copy symbol specifically used in profile screens for copying invitation links, referral codes, or friend invitation data. Used by: Profile invite features, friend invitation UI, referral copy buttons, social sharing from profile. Provides clear visual indication for copying invitation-related content from user profile screens. |
| `vector_profile_tari_mined_logo.xml` | Vector drawable Tari mined logo for profile mining achievements. Special Tari logo variant used in profile screens to indicate mining achievements, mined Tari, or mining-related accomplishments. Used by: Profile mining sections, mining achievement displays, mined Tari indicators, mining reward UI. Provides visual representation of mining activities and achievements in user profile context. |
| `vector_purple_button_bg.xml` | Drawable background for purple-themed buttons with state management. Purple button background with proper state handling for enabled/disabled states using purple color scheme. Used by: Purple-themed buttons, special action buttons, accent buttons, secondary actions. Provides distinctive purple button styling with proper accessibility states and consistent purple theming throughout the app. |
| `vector_recovery_collapse_button.xml` | Vector drawable collapse icon for wallet recovery interfaces. Collapse/minimize arrow icon used in recovery and backup screens to collapse expanded sections or hide detailed information. Used by: Recovery screens, backup interfaces, collapsible sections, wallet restoration UI. Provides clear visual indication for collapsing expanded content in recovery and backup workflows. |
| `vector_recovery_expand_icon.xml` | Vector drawable expand icon for wallet recovery interfaces. Expand/maximize arrow icon used in recovery and backup screens to expand collapsed sections or show detailed information. Used by: Recovery screens, backup interfaces, expandable sections, wallet restoration UI. Provides clear visual indication for expanding collapsed content in recovery and backup workflows. |
| `vector_restoring_seed_phrase_word_bg.xml` | Vector drawable background for seed phrase word inputs during wallet restoration. Background styling for individual seed phrase word input fields in wallet recovery and restoration flows. Used by: Seed phrase restoration UI, recovery word inputs, wallet restoration screens, backup recovery interfaces. Provides consistent background styling for seed phrase word entry components during wallet recovery processes. |
| `vector_restoring_seed_phrase_word_bg_error.xml` | Vector drawable error background for incorrect seed phrase words during restoration. Error-styled background for seed phrase word inputs when words are invalid or incorrect during wallet recovery. Used by: Seed phrase restoration error states, invalid recovery word inputs, wallet restoration error feedback. Provides visual error feedback for incorrect seed phrase entries during wallet recovery with distinctive error styling. |
| `vector_send_button_back.xml` | Vector drawable back button specifically for send transaction flows. Back navigation arrow used in send Tari transaction screens and payment workflows. Used by: Send transaction navigation, payment flow back buttons, transaction creation screens. Provides context-specific back navigation for send transaction flows and payment interfaces. |
| `vector_share.xml` | Vector drawable share icon for general sharing functionality. Share/export symbol used throughout the app for sharing addresses, transaction data, QR codes, and other wallet information. Used by: Share buttons, export actions, data sharing UI, social sharing features. Provides clear visual indication for sharing wallet data and information with external apps and contacts. |
| `vector_share_dots.xml` | Vector drawable share icon with dots styling for enhanced sharing options. Share symbol with dots decoration used for advanced sharing features, multiple sharing options, or share menu triggers. Used by: Advanced share buttons, share option menus, enhanced sharing UI, multi-option sharing interfaces. Provides visual indication for comprehensive sharing options and advanced sharing functionality. |
| `vector_share_link.xml` | Vector drawable share link icon for sharing URLs and links. Link share symbol used for sharing wallet addresses, transaction links, or web links related to wallet functionality. Used by: Link sharing buttons, URL sharing UI, address link sharing, web link export features. Provides clear visual indication for sharing links and URL-based wallet information. |
| `vector_share_qr_code.xml` | Vector drawable for QR code sharing functionality. 22x22dp white QR code pattern icon used in sharing interfaces and dialogs. Features detailed QR code visualization with corner markers and data pattern. Used in bridge sharing, address sharing, and general QR code generation contexts throughout the wallet application for easy sharing and scanning operations. |
| `vector_sharing_failed.xml` | Vector drawable icon indicating failed sharing operations. Error or failure symbol used to show unsuccessful sharing attempts, failed exports, or sharing errors. Used by: Sharing error states, failed export notifications, sharing failure feedback UI. Provides clear visual indication when sharing operations fail and need user attention or retry. |
| `vector_sharing_success.xml` | Vector drawable icon indicating successful sharing operations. Success checkmark or confirmation symbol used to show successful sharing, completed exports, or sharing confirmations. Used by: Sharing success states, successful export confirmations, sharing completion feedback UI. Provides positive visual feedback when sharing operations complete successfully. |
| `vector_shield_checkmark.xml` | Vector drawable shield with checkmark for security and protection indicators. Security shield icon with verification checkmark used to show secure states, verified security, or protection confirmations. Used by: Security indicators, protection status UI, verified security states, safety confirmations. Provides visual representation of security verification and protection status throughout security-related interfaces. |
| `vector_similar_contact_bg_stroke.xml` | Vector drawable stroke background for similar contact indicators. Border or outline styling used to highlight similar contacts, contact matches, or contact suggestions in search and contact management. Used by: Similar contact displays, contact matching UI, contact suggestion indicators, duplicate contact highlighting. Provides visual emphasis for contact similarity and matching functionality. |
| `vector_star.xml` | Vector drawable star icon for favorites, ratings, or special indicators. Star symbol used for marking favorites, rating features, special content, or premium indicators throughout the app. Used by: Favorite buttons, rating systems, special feature indicators, premium content markers. Provides clear visual indication for favorite items and special content designation. |
| `vector_store_reload.xml` | Vector drawable reload/refresh icon for store or marketplace functionality. Refresh or reload symbol used in store interfaces to reload content, refresh listings, or update marketplace data. Used by: Store refresh buttons, marketplace reload UI, content refresh actions, data update controls. Provides clear visual indication for refreshing store content and marketplace information. |
| `vector_store_share.xml` | Vector drawable share icon for store or marketplace content sharing. Share symbol specifically used in store interfaces for sharing products, marketplace items, or store-related content. Used by: Store sharing buttons, marketplace content sharing, product sharing UI, store social features. Provides clear visual indication for sharing store and marketplace content with others. |
| `vector_tari_yat_close.xml` | Vector drawable close icon for YAT (Your Alias Token) interfaces. Close or dismiss symbol specifically styled for YAT-related dialogs, YAT configuration screens, or YAT feature interfaces. Used by: YAT dialog close buttons, YAT screen dismissal, YAT configuration cancellation. Provides contextually appropriate close affordance for YAT-related UI components and flows. |
| `vector_tari_yat_open.xml` | Vector drawable open/expand icon for YAT (Your Alias Token) interfaces. Open, expand, or launch symbol specifically styled for YAT-related features, YAT configuration access, or YAT service opening. Used by: YAT feature access buttons, YAT configuration opening, YAT service launch controls. Provides contextually appropriate access affordance for YAT-related functionality and services. |
| `vector_tx_detail_arrow_down.xml` | Vector drawable downward arrow for transaction detail expansion. Down arrow icon used in transaction details to expand additional information, show more details, or access extended transaction data. Used by: Transaction detail expansion, transaction info reveals, extended data access buttons. Provides clear visual indication for expanding transaction details and accessing additional transaction information. |
| `vector_utxos_list_join.xml` | Vector drawable join icon for UTXO list operations. Join or combine symbol used in UTXO management to merge multiple UTXOs into larger outputs for transaction optimization. Used by: UTXO join operations, UTXO consolidation UI, transaction optimization controls, UTXO management tools. Provides clear visual indication for combining multiple UTXOs into fewer, larger outputs for improved transaction efficiency. |
| `vector_utxos_list_selected.xml` | Vector drawable selection indicator for UTXO list items. Selection checkmark or indicator used to show selected UTXOs in UTXO management interfaces for batch operations. Used by: UTXO selection UI, multi-UTXO operations, batch UTXO management, UTXO list selection controls. Provides visual feedback for UTXO selection state in multi-select UTXO management workflows. |
| `vector_utxos_list_split.xml` | Vector drawable for UTXO split operation visualization. 19x18dp icon showing arrows diverging from center point representing UTXO splitting functionality. Features dual arrow design with path elements indicating separation or breaking of single UTXO into multiple outputs. Used in UTXO management interface for split operation buttons and visual indicators in advanced coin control features. |
| `vector_utxos_list_tile_bg.xml` | Vector drawable background for UTXO list tile components. Background styling for individual UTXO items in list views, providing consistent visual presentation for UTXO data. Used by: UTXO list items, UTXO tile backgrounds, UTXO management UI, UTXO display components. Provides consistent background styling for UTXO list items and tile-based UTXO presentations throughout the management interface. |
| `vector_utxos_list_tile_outline_bg.xml` | Vector drawable outlined background for UTXO list tile components. Outlined border styling for UTXO items in list views, used for selection states, hover states, or emphasis. Used by: UTXO list item selection, UTXO tile highlighting, selected UTXO indicators, UTXO emphasis states. Provides outlined background styling for UTXO list items requiring visual emphasis or selection indication. |
| `vector_utxos_minus.xml` | Vector drawable minus icon for UTXO operations and controls. Minus or subtract symbol used in UTXO management for removing items, decreasing values, or negative operations. Used by: UTXO removal controls, UTXO quantity decreasing, UTXO subtraction operations, UTXO management negative actions. Provides clear visual indication for removing or decreasing UTXO-related values and operations. |
| `vector_utxos_plus.xml` | Vector drawable plus icon for UTXO operations and controls. Plus or add symbol used in UTXO management for adding items, increasing values, or positive operations. Used by: UTXO addition controls, UTXO quantity increasing, UTXO addition operations, UTXO management positive actions. Provides clear visual indication for adding or increasing UTXO-related values and operations. |
| `vector_utxos_status_confirmed.xml` | Vector drawable confirmed status indicator for UTXO states. Confirmation checkmark or verified symbol used to show UTXOs that have been confirmed on the blockchain. Used by: UTXO status indicators, confirmed UTXO displays, UTXO state visualization, blockchain confirmation UI. Provides visual representation of UTXO confirmation status and blockchain verification state. |
| `vector_utxos_status_mined.xml` | Vector drawable mined status indicator for UTXO states. Mining or mined symbol used to show UTXOs that have been mined and included in blockchain blocks. Used by: UTXO status indicators, mined UTXO displays, UTXO mining state visualization, blockchain mining UI. Provides visual representation of UTXO mining status and blockchain inclusion state. |
| `vector_utxos_status_text_confirmed.xml` | Vector drawable text-based confirmed status for UTXO displays. Text or label styling for confirmed UTXO status indicators, providing textual confirmation status representation. Used by: UTXO status text displays, confirmed UTXO labels, UTXO text status indicators, status text styling. Provides text-based visual representation of UTXO confirmation status with appropriate styling for confirmed states. |
| `vector_utxos_status_text_mined.xml` | Vector drawable text-based mined status for UTXO displays. Text or label styling for mined UTXO status indicators, providing textual mining status representation. Used by: UTXO status text displays, mined UTXO labels, UTXO text status indicators, mining status text styling. Provides text-based visual representation of UTXO mining status with appropriate styling for mined states. |
| `vector_validation_error_box_border_bg.xml` | Selector drawable for validation error box border. Rectangular background with transparent fill, 1dp red stroke (?attr/palette_system_red), and 4dp rounded corners. Used to highlight input fields or containers with validation errors. |
| `vector_view_elevation_bottom_gradient.xml` | Gradient drawable for bottom elevation effects. Linear gradient for creating shadow or elevation effects specifically at bottom edge of views. Provides visual depth and separation for UI components with smooth gradient transition from bottom. |
| `vector_view_elevation_gradient.xml` | Gradient drawable for view elevation effects. Linear gradient at 270° angle from gray to transparent, used to create shadow or elevation effects. Provides visual depth for UI components with smooth gradient transition. |
| `vector_wallet_filtering.xml` | Vector drawable for wallet filtering functionality. 12x14dp filter funnel icon in black used for filtering or sorting operations in wallet interfaces. Provides visual representation for filter controls in transaction lists, UTXO management, or other data filtering contexts. Part of wallet UI iconography for data organization and user control features. |
| `vector_wallet_group_cells.xml` | Vector drawable for wallet grid/cells view icon. Visual representation of grid or cells layout mode for wallet interface. Used in view mode switchers to allow users to change between list and grid presentations of wallet data. |
| `vector_wallet_group_list.xml` | Vector drawable for wallet list view icon. Visual representation of list layout mode for wallet interface. Used in view mode switchers to allow users to change between grid and list presentations of wallet transactions and data. |
| `vector_wallet_not_backed.xml` | Vector drawable for wallet not backed up warning. Warning icon indicating wallet has not been backed up, used in security and backup settings to alert users about unprotected wallet state. Critical security indicator. |
| `vector_wallet_utxos_empty.xml` | Vector drawable for empty UTXO state. Empty state illustration used when wallet has no unspent transaction outputs to display. Provides visual feedback for empty UTXO list with appropriate messaging. |
| `vector_wallet_wallet.xml` | Vector drawable for wallet icon. Primary wallet representation icon used throughout the app for wallet-related functionality, navigation, and branding. Core visual element representing the wallet concept. |
| `vector_wallet_yat.xml` | Vector drawable for wallet YAT integration icon. Icon representing YAT (Your Alias Token) functionality within wallet interface. Used for YAT-related features, settings, and integration points. |
| `vector_warning.xml` | Vector drawable for warning icon. Triangle warning symbol (24x24dp) with #D32C44 red color containing exclamation mark. Used throughout app for error states, warnings, and alert notifications with detailed path data for consistent warning visualization. |

###### app/src/main/res/layout/

| File | Description |
|------|-------------|
| `activity_auth.xml` | Layout for authentication activity. Contains ConstraintLayout with fragment container for auth flows, network info display at bottom, and loading overlay with progress bar. Used by AuthActivity for PIN, biometric, and password authentication screens. |
| `activity_debug.xml` | Layout for debug activity. Simple TariPrimaryBackground container with navigation FrameLayout for debug-related fragments. Used by DebugActivity for development tools, testing features, and diagnostic interfaces during app development. |
| `activity_home.xml` | Layout for main home activity. Simple FrameLayout containing ComposeView for Jetpack Compose UI and navigation container for fragment-based screens. Entry point for primary wallet interface mixing Compose and traditional Android views. |
| `activity_onboarding_flow.xml` | Layout for onboarding flow activity. Simple FrameLayout with black background serving as container for onboarding fragments. Entry point for new user setup flow including wallet creation, backup, and security configuration. |
| `activity_qr_scanner.xml` | Layout for QR code scanner activity. Contains CodeScannerView for camera functionality, close button, instruction text with TariAlphaBackground, and error handling container. Used for scanning QR codes for addresses, contact sharing, and transaction initiation. |
| `activity_wallet_backup.xml` | Layout for wallet backup activity. Contains navigation container for wallet backup flow fragments including seed phrase display, verification, and backup method selection. Critical security flow for wallet recovery setup. |
| `dialog_base.xml` | Base layout for dialog components. Foundation layout providing consistent structure for modular dialog system. Used as container for various dialog modules (head, body, buttons, etc.) with proper theming and spacing. |
| `dialog_module_address_details.xml` | Dialog module for address details display. Shows comprehensive address information including emoji ID, hex format, and address metadata. Used in dialogs for detailed address inspection and verification. |
| `dialog_module_address_poisoning.xml` | Dialog module for address poisoning warning. Security alert component warning users about potential address poisoning attacks with educational content and protective measures. Critical security feature. |
| `dialog_module_backup_learn_more_item.xml` | Dialog module for backup education item. Individual item in backup learning interface explaining specific backup concepts, benefits, or steps. Used in educational dialogs about wallet backup importance. |
| `dialog_module_body.xml` | Dialog module for body text display. LinearLayout containing TariTextView with custom font, centered alignment, specific margins, and theming. Used as reusable component in various dialog implementations throughout the app. |
| `dialog_module_button.xml` | Dialog module for button component. LinearLayout containing TariButton with gradient background, standard dimensions, and theming. Used as reusable button component in dialog implementations with consistent styling and behavior. |
| `dialog_module_checked.xml` | Dialog module for checked/selected state indicator. Visual component showing selected or confirmed states in dialogs with checkmark or selection indicators. Used for multi-step confirmations and selections. |
| `dialog_module_connection_statuses.xml` | Dialog module for connection status display. Shows network connection states including base node connectivity, Tor status, and sync progress. Used in network diagnostics and connection troubleshooting dialogs. |
| `dialog_module_head.xml` | Dialog module for header component. FrameLayout with centered heading text, optional secondary button, and optional icon button. Used for dialog titles with action buttons and proper spacing for modular dialog construction. |
| `dialog_module_icon.xml` | Dialog module for icon display. FrameLayout with circular TariPrimaryBackgroundConstraint container (80dp) holding centered icon ImageView. Used for prominent icon display in dialogs with elevation and proper spacing. |
| `dialog_module_image.xml` | Dialog module for image display. Component for showing images in dialogs including icons, illustrations, or user-generated content. Used for visual communication and enhanced dialog presentation. |
| `dialog_module_input.xml` | Dialog module for input field. LinearLayout containing TariInput with standard dimensions (50dp height) and proper padding. Used as reusable input component in dialog implementations for text entry with consistent styling. |
| `dialog_module_option.xml` | Dialog module for option selection. Reusable component for displaying selectable options in dialogs with radio buttons, checkboxes, or selection indicators. Used for choice dialogs and configuration options. |
| `dialog_module_security_stage_title.xml` | Dialog module for security stage title display. Shows current stage in multi-step security setup process with progress indicators and context. Used in security configuration workflows. |
| `dialog_module_share_options.xml` | Dialog module for sharing options display. Shows various sharing methods including QR code, NFC, Bluetooth, and social platforms. Central component for sharing functionality throughout the app. |
| `dialog_module_share_qr_code.xml` | Dialog module for QR code sharing. Displays QR codes for addresses, payment requests, or contact sharing with customization options. Central component for QR-based sharing functionality. |
| `dialog_module_short_emoji.xml` | Dialog module for short emoji ID display. Compact presentation of emoji IDs in dialogs with abbreviated format for quick identification. Used when full emoji ID display is not needed. |
| `dialog_module_space.xml` | Dialog module for spacing/empty space. Simple FrameLayout used as spacer component in modular dialog construction. Provides consistent spacing between dialog elements without visible content. |
| `dialog_module_utxo_amount.xml` | Dialog module for UTXO amount display and editing. Shows UTXO value with editing capabilities for coin control operations. Used in UTXO management and transaction construction dialogs. |
| `dialog_module_utxo_split.xml` | Dialog module for UTXO splitting operations. Interface for splitting large UTXOs into smaller denominations for privacy and transaction flexibility. Advanced coin control feature. |
| `dialog_module_utxos_detail_item.xml` | Dialog module for UTXO detail item display. Shows detailed information about individual UTXO including value, status, origin transaction, and metadata. Used in UTXO inspection dialogs. |
| `dialog_module_utxos_ordering_item.xml` | Dialog module for UTXO ordering option item. Individual option in UTXO sorting/ordering dialogs allowing users to choose sort criteria like amount, age, or status. Part of UTXO management interface. |
| `fragment_add_amount.xml` | Layout for amount entry screen in send transaction flow. Complex layout with back button, title, emoji ID display, amount input, balance warnings, fee calculation, network traffic indicators, and numpad. Includes custom TariPrimaryBackgroundConstraint and AmountView components. |
| `fragment_add_note.xml` | Layout for add note fragment in transaction flow. Interface for adding notes or messages to transactions during send process. Contains text input, character limits, and transaction context for user annotations. |
| `fragment_all_settings.xml` | Layout for main settings fragment. Primary settings interface containing RecyclerView or list of settings categories including security, network, backup, and app preferences. Central hub for wallet configuration. |
| `fragment_backup_learn_more.xml` | Layout for backup education fragment. Educational interface explaining wallet backup importance, methods, and security considerations. Used in onboarding and settings to educate users about backup necessity. |
| `fragment_backup_learn_more_item.xml` | Layout for backup education item in fragment. Component within backup learning interface showing specific backup tip, warning, or educational content. Used to build comprehensive backup education flows. |
| `fragment_base_node_change.xml` | Layout for base node configuration fragment. Interface for changing base node connection settings including custom node entry and connection testing. Critical for wallet network connectivity configuration. |
| `fragment_bugs_reporting.xml` | Layout for bug reporting fragment. Interface for reporting app issues including description input, log attachment, and device information collection. Helps developers improve app quality through user feedback. |
| `fragment_change_biometrics.xml` | Layout for biometric authentication settings fragment. Interface for enabling/disabling biometric authentication (fingerprint, face unlock) with security explanations and fallback options. Critical security configuration interface. |
| `fragment_change_secure_password.xml` | Layout for secure password change fragment. Interface for updating wallet security password with current password verification, new password entry, and confirmation. Essential security management functionality. |
| `fragment_chat.xml` | Layout for chat fragment. Messaging interface for communication with contacts including message display, input field, and file sharing. Enables private communication alongside transaction functionality. |
| `fragment_chat_list.xml` | Layout for chat list fragment. Shows list of active conversations and contacts available for messaging. Features search, contact status, and recent message previews for conversation management. |
| `fragment_choose_restore_option.xml` | Layout for wallet restoration option selection fragment. Interface for choosing restoration method including seed phrase, backup file, or cloud restore. Critical onboarding component for existing users. |
| `fragment_contact_book_root.xml` | Layout for contact book root fragment. Main container for contact management interface with navigation between contact list, search, and management features. Central hub for contact-related functionality in wallet. |
| `fragment_contacts.xml` | Layout for contacts list fragment. Simple RelativeLayout with SwipeRefreshLayout containing RecyclerView for displaying contact list. Supports pull-to-refresh functionality and proper background theming with palette attributes. |
| `fragment_contacts_details.xml` | Layout for contact details fragment. Detailed view of individual contact including emoji ID, alias, transaction history, and contact management actions (edit, delete, favorite). Used for comprehensive contact information display. |
| `fragment_contacts_link.xml` | Layout for contacts linking fragment. Interface for linking Tari wallet contacts with device phone contacts. Enables integration between wallet address book and device contact list for enhanced usability. |
| `fragment_contacts_selection.xml` | Layout for contact selection fragment. Contact picker interface used during transaction flows to select recipient from contact list. Features search, filtering, and recent contacts for efficient recipient selection. |
| `fragment_create_wallet.xml` | Layout for wallet creation fragment. Complex onboarding screen with emoji ID generation, Lottie animations (emoji wheel, checkmark, nerd emoji), multiple text states, progress indicators, and action buttons. Features emoji ID display with scroll and tap-to-expand functionality for wallet setup flow. |
| `fragment_custom_tor_bridges.xml` | Layout for custom Tor bridges configuration fragment. Advanced interface for manually configuring custom Tor bridges with text input and validation. Used for specialized privacy requirements and network conditions. |
| `fragment_data_collection.xml` | Layout for data collection settings fragment. Privacy settings for controlling app analytics, crash reporting, and data sharing preferences. Used for GDPR compliance and user privacy control. |
| `fragment_delete_wallet.xml` | Layout for wallet deletion fragment. Critical security interface for permanently deleting wallet with multiple confirmation steps, warnings, and backup verification. Used in wallet reset scenarios. |
| `fragment_enter_current_password.xml` | Layout for current password entry fragment. Secure interface for entering existing wallet password during password change or sensitive operations. Features masked input and validation. |
| `fragment_enter_pincode.xml` | Layout for PIN code entry fragment. PIN authentication interface with numeric keypad, PIN input indicators, and error handling. Used for wallet security authentication during app launch and sensitive operations. |
| `fragment_enter_restore_password.xml` | Layout for restore password entry fragment. Interface for entering password during wallet restoration from encrypted backup. Critical component of secure wallet recovery process. |
| `fragment_feature_auth.xml` | Layout for feature authentication fragment. Authentication gate for accessing specific wallet features requiring additional security verification. Used before sensitive operations like backup access or settings changes. |
| `fragment_finalize_send_tx.xml` | Layout for transaction finalization fragment. Simple centered layout with gem icon, informational text lines, and progress steps container. Used during send transaction process to show status, progress, and completion states to user. |
| `fragment_local_auth.xml` | Layout for local authentication fragment. Local device authentication interface using system biometrics or device security. Used for secure wallet access without exposing wallet-specific credentials to system. |
| `fragment_log_files.xml` | Layout for log files management fragment. Interface for viewing, sharing, and managing app log files for debugging and support purposes. Features log file listing and export functionality. |
| `fragment_logs.xml` | Layout for logs viewing fragment. Displays app logs with filtering, search, and export capabilities. Used for debugging, troubleshooting, and technical support scenarios. |
| `fragment_network_selection.xml` | Layout for network selection fragment. Interface for choosing blockchain network (mainnet, testnet) and base node configuration. Critical setup component for wallet network connectivity. |
| `fragment_request_tari.xml` | Layout for request Tari fragment. Interface for requesting Tari payments including amount specification, QR code generation, and sharing options. Enables users to request payments from others with various sharing methods. |
| `fragment_screen_recording_settings.xml` | Layout for screen recording protection settings. Security configuration for preventing screen recording and screenshots of sensitive wallet information. Important privacy and security feature. |
| `fragment_store.xml` | Layout for store fragment. Interface for browsing and purchasing digital goods, apps, or services using Tari. Features product listings, search, and payment integration for marketplace functionality. |
| `fragment_tari_about.xml` | Layout for about Tari fragment. Information page about Tari project including version info, team credits, links to resources, and legal information. Used in app settings and help sections. |
| `fragment_theme_change.xml` | Layout for theme selection fragment. Interface for changing app visual theme including light/dark mode selection and color scheme preferences. Part of personalization and accessibility settings. |
| `fragment_tor_bridge_selection.xml` | Layout for Tor bridge selection fragment. Interface for selecting and configuring Tor bridges for enhanced privacy and circumventing network restrictions. Part of advanced privacy configuration. |
| `fragment_transfer.xml` | Layout for transfer/transaction fragment. Main interface for sending transactions including recipient selection, amount entry, note addition, and confirmation. Central component of transaction workflow. |
| `fragment_utxos_list.xml` | Layout for UTXO list fragment. Displays unspent transaction outputs with status indicators, amounts, and management controls. Features filtering, sorting, and selection for UTXO coin control functionality. |
| `fragment_verify_seed_phrase.xml` | Layout for seed phrase verification fragment. Critical security interface for verifying user has correctly recorded seed phrase during backup process. Features word selection, verification prompts, and error handling for wallet recovery assurance. |
| `fragment_wallet_backup_settings.xml` | Layout for wallet backup settings fragment. Configuration interface for backup options including cloud storage, local backup, and backup frequency settings. Essential for wallet recovery management. |
| `fragment_wallet_info.xml` | Layout for wallet information fragment. Displays wallet details including balance, address, connection status, and wallet metadata. Used for wallet overview and diagnostic information. |
| `fragment_wallet_input_seed_words.xml` | Layout for seed phrase input fragment. Interface for entering seed words during wallet restoration process. Features word input fields, auto-completion, validation, and error handling for secure wallet recovery. |
| `fragment_wallet_restoring.xml` | Layout for wallet restoration progress fragment. Shows restoration progress, status updates, and loading indicators during wallet recovery process. Used while wallet is being restored from seed phrase or backup. |
| `fragment_write_down_seed_phrase.xml` | Layout for seed phrase recording fragment. Interface for displaying seed phrase for user to write down during backup process. Features secure display, copy protection, and verification prompts for wallet recovery setup. |
| `item_base_node.xml` | Layout for base node list item. Displays base node information including address, connection status, and performance metrics. Used in base node selection and configuration interfaces. |
| `item_bridge.xml` | Layout for Tor bridge list item. Displays Tor bridge configuration options with connection status and selection controls. Used in Tor bridge selection and configuration interfaces. |
| `item_chat_item.xml` | Layout for chat conversation item. RecyclerView item displaying individual conversations in chat list with contact info, last message preview, timestamp, and unread indicators. |
| `item_chat_message_item.xml` | Layout for individual chat message item. Displays single message in conversation with sender info, content, timestamp, and delivery status. Supports text messages and attachments. |
| `item_contact.xml` | Layout for contact list item. RecyclerView item displaying contact information including name, emoji ID, favorite status, and avatar. Used in contact lists with click handling for selection and detail navigation. |
| `item_contact_empty_state.xml` | Layout for empty contact list state. Empty state item shown when no contacts are available, featuring illustration, message, and action buttons to encourage contact addition or import. |
| `item_contact_link_header.xml` | Layout for contact link header item. Header section in contact linking interface showing instructions or categories for linking device contacts with wallet addresses. |
| `item_contact_profile.xml` | Layout for contact profile item. Detailed contact display with profile information, transaction history summary, and management actions. Used in contact details and profile views. |
| `item_contact_type.xml` | Layout for contact type selection item. Shows different contact categories or types for organization and filtering. Used in contact management and categorization interfaces. |
| `item_log.xml` | Layout for log entry item. Displays individual log entries with timestamp, level, and message content. Used in log viewing interfaces for debugging and troubleshooting. |
| `item_my_profile.xml` | Layout for user profile item. Shows current user's profile information including wallet address, emoji ID, and personal settings. Used in contact lists and profile management. |
| `item_network.xml` | Layout for network selection item. Displays available networks (mainnet, testnet) with status indicators and selection controls. Used in network configuration interfaces. |
| `item_phrase_word.xml` | Layout for seed phrase word item. Displays individual words in seed phrase with numbering and selection capabilities. Used in seed phrase verification and input interfaces for wallet backup/restore. |
| `item_settings_backup_option.xml` | Layout for backup option settings item. Shows different backup methods and their configuration in settings interface. Used for backup preference selection and management. |
| `item_settings_divider.xml` | Layout for settings divider item. Visual separator used in settings lists to group related options and improve organization. Simple divider line or space for settings sections. |
| `item_settings_row.xml` | Layout for settings row item. Standard settings list item with title, description, icon, and action controls. Core component for settings interface with consistent styling and interaction. |
| `item_settings_title.xml` | Layout for settings section title item. Header titles for different sections in settings interface providing clear organization and navigation. Used to separate setting categories. |
| `item_settings_version.xml` | Layout for settings version information item. Displays app version, build number, and version details in settings interface. Used for debugging and support identification. |
| `item_similar_address.xml` | Layout for similar address warning item. Shows potentially similar addresses that might cause confusion or indicate address poisoning attacks. Critical security feature for user protection. |
| `item_similar_address_content.xml` | Layout for similar address content display. Detailed view of similar address with comparison highlighting and risk warnings. Used in address poisoning detection and prevention. |
| `item_suggestion.xml` | Layout for search suggestion item. Displays search suggestions and autocomplete options in search interfaces. Used for contact search, address lookup, and general app search functionality. |
| `item_tari_icon.xml` | Layout for Tari icon display item. Shows Tari branding icons and logos in various contexts throughout the app. Used for consistent brand representation and visual identity. |
| `item_theme.xml` | Layout for theme selection item. Shows theme options with previews and selection controls for app appearance customization. Used in theme settings for personalization. |
| `item_title.xml` | Layout for title item display. Generic title component used in various lists and interfaces for section headers and organization. Provides consistent title styling across the app. |
| `item_utxos_text.xml` | Layout for UTXO text information item. Displays textual information about UTXOs including descriptions, metadata, and explanatory content. Used in UTXO management interfaces. |
| `item_utxos_tile.xml` | Layout for UTXO tile display item. Visual tile representation of individual UTXOs with amount, status, and selection controls. Used in grid view of UTXO management interface. |
| `item_vertical_space.xml` | Layout for vertical spacing item. Simple spacer component providing consistent vertical spacing between elements in RecyclerView lists. Used for visual organization and layout spacing. |
| `tari_back_button.xml` | Layout for Tari-branded back button component. Custom back navigation button with Tari styling, sizing, and interaction behaviors. Used throughout the app for consistent navigation experience. |
| `tari_gradient_button.xml` | Layout for Tari gradient button component. Primary button with gradient background and Tari branding. Used for main action buttons throughout the app with consistent theming. |
| `tari_input.xml` | Layout for Tari-branded input field component. Custom text input with Tari styling, validation, and interaction behaviors. Used for forms and text entry throughout the app. |
| `tari_secondary_button.xml` | Layout for Tari secondary button component. Alternative button style with different visual treatment for secondary actions. Used alongside primary buttons for action hierarchies. |
| `tari_toolbar.xml` | Layout for Tari-branded toolbar component. Custom toolbar with Tari styling, navigation controls, and action items. Used for consistent app bar appearance across screens. |
| `view_add_amount_element.xml` | Layout for add amount element component. UI element for amount addition functionality with input controls and validation. Used in transaction flows for adding multiple amounts or fees. |
| `view_address_short.xml` | Layout for short address display component. Abbreviated emoji ID display with truncation and expansion capabilities. Used for compact address representation in lists and cards. |
| `view_address_short_small.xml` | Layout for small short address display component. Compact emoji ID display with minimal size and truncation for use in small UI spaces like badges or inline text. |
| `view_amount.xml` | Layout for amount display component. Shows cryptocurrency amounts with proper formatting, currency symbols, and conversion display. Used throughout app for balance and transaction amount display. |
| `view_backup_option.xml` | Layout for backup option selection component. Individual backup method option with description, security level, and selection controls. Used in backup configuration interfaces. |
| `view_clipboard_wallet.xml` | Layout for clipboard wallet address component. Shows wallet address from clipboard with validation and import capabilities. Used for quick address entry from copied content. |
| `view_confirm_word.xml` | Layout for word confirmation component. Interface for confirming seed phrase words during backup verification with selection states and validation feedback. |
| `view_custom_base_node_full_description.xml` | Layout for custom base node full description component. Detailed description interface for custom base node configuration with technical details and connection information. |
| `view_finalizing_step.xml` | Layout for transaction finalizing step component. Step indicator and progress display during transaction finalization process showing current stage and progress. |
| `view_input_amount.xml` | Layout for amount input component. Specialized input field for cryptocurrency amounts with decimal handling, currency formatting, and validation. Core component for transaction flows. |
| `view_numpad.xml` | Layout for numeric keypad component. Custom numpad for amount entry, PIN input, and numeric data entry. Features custom styling and interaction behaviors for wallet-specific use cases. |
| `view_progress_button.xml` | Layout for progress button component. Button with integrated progress indicator for showing loading states during async operations. Used for actions with processing time. |
| `view_progress_switch.xml` | Layout for progress switch component. Switch control with integrated progress indicator for settings that require processing time to enable/disable features. |
| `view_recovery_word.xml` | Layout for seed phrase recovery word component. Individual word display in seed phrase interfaces with numbering and interaction capabilities. Used in backup and restore flows. |
| `view_restore_option.xml` | Layout for wallet restore option component. Individual restore method option with description, security information, and selection controls. Used in wallet recovery interfaces. |
| `view_settings_row.xml` | Layout for settings row component. Reusable settings item with title, description, icon, and action controls. Core component for building consistent settings interfaces throughout the app. |
| `view_settings_title.xml` | Layout for settings title component. Section header component for settings interfaces with proper typography and spacing. Used to organize settings into logical groups and categories. |
| `view_share_option.xml` | Layout for share option component. Individual sharing method option with icon, label, and selection capabilities. Used in sharing dialogs for contact and transaction sharing. |
| `view_tari_toolbar_action.xml` | Layout for Tari toolbar action component. Custom action button for toolbar with Tari branding and interaction behaviors. Used for toolbar-specific actions and navigation elements. |

###### app/src/main/res/mipmap-anydpi-v26/

| File | Description |
|------|-------------|
| `ic_launcher.xml` | Adaptive app launcher icon configuration for Android API 26+. Defines foreground and background elements for adaptive icon system allowing system to apply various visual effects and shapes. |
| `ic_launcher_round.xml` | Adaptive round app launcher icon configuration for Android API 26+. Defines foreground and background elements specifically for round icon format used by some Android launchers. |

###### app/src/main/res/raw/

| File | Description |
|------|-------------|
| `check_mark.json` | Lottie animation file for checkmark animation. JSON-based success animation used to indicate completed actions, successful transactions, or confirmation states throughout the app. |
| `emoji_wheel.json` | Lottie animation file for emoji wheel animation. JSON-based animation used during wallet creation showing rotating emoji wheel for address generation. Part of onboarding visual experience. |
| `nerd_emoji.json` | Lottie animation file for nerd emoji animation. JSON-based character animation used in educational or technical contexts within the app for visual engagement. |
| `nextnet_base_nodes.txt` | Configuration file containing list of NextNet (testnet) base node addresses. Used for connecting to test network infrastructure for development and testing purposes. |
| `onboarding_bottom_spinner.json` | Lottie animation file for onboarding progress spinner. JSON-based loading animation displayed at bottom of onboarding screens during wallet setup and initialization processes. |
| `stagenet_base_nodes.txt` | Configuration file containing list of StageNet base node addresses. Used for connecting to staging network infrastructure for pre-production testing and validation. |

###### app/src/main/res/values/

| File | Description |
|------|-------------|
| `colors.xml` | Color resources file defining app color palette including primary, secondary, accent colors, and theme-specific color definitions. Foundation for app visual theming and branding. |
| `colors_palette_dark.xml` | Dark theme color palette resources. Defines color values specifically for dark mode including background, text, accent, and UI element colors. Part of comprehensive theming system. |
| `colors_palette_light.xml` | Light theme color palette resources. Defines color values specifically for light mode including background, text, accent, and UI element colors. Complementary to dark theme palette. |
| `dimens.xml` | Dimension resources file defining spacing, sizes, margins, and layout dimensions used throughout the app. Ensures consistent spacing and responsive design across different screen sizes. |
| `palette_attrs.xml` | Theme palette attribute definitions. Defines custom attributes for dynamic theming system allowing colors to change based on selected theme. Foundation for adaptive UI theming. |
| `palette_styles.xml` | Palette-based style definitions. Combines palette attributes with style definitions for comprehensive theming. Used to create consistent visual appearance across different themes. |
| `strings.xml` | String resources file containing all app text content including UI labels, messages, errors, and multilingual support. Central location for app localization and text management. |
| `styles.xml` | Style resources file defining app themes, text styles, and component styling. Contains custom styles and theme definitions for consistent visual appearance across the application. |
| `tari_elements_attrs.xml` | Custom attribute definitions for Tari UI elements. Defines styleable attributes for custom Tari components enabling theming and customization of branded UI elements. |

###### app/src/main/res/values-h640dp/

| File | Description |
|------|-------------|
| `dimens.xml` | Height-specific dimension resources for 640dp screens. Device-specific spacing and sizing values optimized for medium height screens ensuring proper layout scaling and visual hierarchy. |

###### app/src/main/res/values-h720dp/

| File | Description |
|------|-------------|
| `dimens.xml` | Height-specific dimension resources for 720dp screens. Device-specific spacing and sizing values optimized for larger height screens ensuring proper layout scaling and improved user experience. |

###### app/src/main/res/xml/

| File | Description |
|------|-------------|
| `file_provider.xml` | FileProvider configuration for secure file sharing. Defines paths and permissions for sharing files between apps including log files, backups, and transaction data. Required for Android file sharing security. |

###### app/src/test/java/com/tari/android/wallet/model/

| File | Description |
|------|-------------|
| `MicroTariTest.kt` | Unit tests for MicroTari model class. Tests cryptocurrency amount handling, precision, conversion operations, and validation logic. Ensures correct handling of Tari currency amounts and calculations. |

###### app/src/test/java/com/tari/android/wallet/ui/screen/settings/backup/

| File | Description |
|------|-------------|
| `SelectionSequenceTest.kt` | Unit tests for backup selection sequence logic. Tests user interaction flows for seed phrase backup verification including word selection, sequencing, and validation logic. |

### buildSrc/

| File | Description |
|------|-------------|
| `build.gradle.kts` | Build configuration for buildSrc module containing custom Gradle plugins and build logic. Configures Kotlin DSL, applies necessary plugins for custom build tasks, manages dependencies for build scripts. Essential for project-specific build customization and plugin development. |

###### buildSrc/src/main/kotlin/

| File | Description |
|------|-------------|
| `Dependencies.kt` | Centralized dependency management for the Android project. Defines version constants for all external libraries: Kotlin (2.0.21), AndroidX components, Compose, Coroutines, Firebase, Google services, Retrofit, Sentry, testing frameworks. Organized by logical groups (AndroidX, Test, etc.) for maintainability. Used throughout build.gradle.kts files for consistent versioning. |
| `TariBuildConfig.kt` | Build configuration object defining app version, SDK versions, and native library management. Centralized configuration for build system and native library downloads. Exports: App version, SDK targets, LibWallet configuration with network variants (MAINNET/NEXTNET/ESMERALDA), library URLs and filenames. Dependencies: GitHub releases for native libraries. Used by: Build scripts and gradle configuration. Features network-specific library versions, automated library download URLs, and minimum version validation. Critical for managing native library dependencies and build consistency. |
| `download-libwallet.gradle.kts` | Gradle plugin for automatic native library downloading. Downloads Tari wallet native libraries (libminotari_wallet_ffi) from GitHub releases based on TariBuildConfig version settings. Handles architecture-specific downloads (arm64-v8a, x86_64), manages library versioning, and integrates with build process. Critical for native FFI library management. |

### docs/

| File | Description |
|------|-------------|
| `tari.md` | Documentation file about Tari cryptocurrency project. Contains technical documentation, API information, blockchain details, and developer resources related to Tari integration and usage. |

#### gradle/wrapper/

| File | Description |
|------|-------------|
| `gradle-wrapper.jar` | Gradle wrapper executable JAR file. Contains the bootstrap code needed to download and run the specified Gradle version ensuring consistent build environment across development setups. |
| `gradle-wrapper.properties` | Gradle wrapper configuration properties. Defines Gradle version, distribution URL, and wrapper settings for consistent build environment across development machines. |

#### libwallet/arm64-v8a/

| File | Description |
|------|-------------|
| `libcrypto.a` | OpenSSL cryptographic library for ARM64 architecture. Static library providing cryptographic functions for secure wallet operations including encryption, hashing, and digital signatures. |
| `libsqlite3.a` | SQLite database library for ARM64 architecture. Static library providing database functionality for wallet data storage, transaction history, and local data management. |
| `libssl.a` | OpenSSL secure socket layer library for ARM64 architecture. Static library providing TLS/SSL functionality for secure network communications in wallet operations. |

#### libwallet/x86_64/

| File | Description |
|------|-------------|
| `libcrypto.a` | OpenSSL cryptographic library for x86_64 architecture. Static library providing cryptographic functions for emulator and x86_64 device support in wallet operations. |
| `libsqlite3.a` | SQLite database library for x86_64 architecture. Static library providing database functionality for emulator support and x86_64 device compatibility. |
| `libssl.a` | OpenSSL secure socket layer library for x86_64 architecture. Static library providing TLS/SSL functionality for secure communications on emulator and x86_64 devices. |

### yatlib/

| File | Description |
|------|-------------|
| `build.gradle` | Build script for YAT library module. Gradle build configuration for YAT (Your Alias Token) integration module including dependencies, compilation settings, and library packaging. |
| `yat-lib-development-snapshot.aar` | YAT (Your Alias Token) library Android Archive. Pre-compiled library containing YAT SDK functionality for alias token integration, address resolution, and YAT ecosystem features. |

---
