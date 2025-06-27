# Wallet-Ios

## Project Overview

## Tari Aurora iOS Wallet

### System Summary
Cryptocurrency wallet app for Tari blockchain with privacy-first architecture. Hybrid Swift/UIKit and SwiftUI app with Rust core via FFI, using Tor networking and MVVM pattern. Supports transactions, contacts, backups, and mining integration.

### Directory Structure
```
MobileWallet/
├── Libraries/TariLib/    # Core wallet integration (FFI, services, Tor)
├── Screens/             # UI screens following MVVM (Home, Send, Settings)
├── UIElements/          # Reusable components (buttons, labels, navigation)
├── SwiftUI/             # SwiftUI design system (buttons, typography, list items)
├── Common/              # Shared utilities (extensions, managers, theme)
├── Backup/              # Backup services (iCloud, Dropbox managers)
├── Assets.xcassets/     # Images, colors, icons (light/dark themes)
├── Colors.xcassets/     # SwiftUI design system colors
├── AppDelegate.swift    # App lifecycle and initialization
└── Info.plist          # App configuration

UnitTests/               # Test suite for core functionality
fastlane/               # Deployment automation
```

### Architecture
MVVM with reactive Combine framework. 4-layer structure: UI → Business Logic → Service → Core (FFI). Constructor-based dependency injection for all screens. All network traffic through Tor for privacy. Hybrid UI approach with UIKit screens and SwiftUI design system components.

### Key Files
- Entry: `AppDelegate.swift` (app initialization and Tor setup)
- Core: `Libraries/TariLib/Core/Tari.swift` (main wallet singleton)
- Config: `env.json` (API keys), `Info.plist` (app settings)
- FFI: `Libraries/TariLib/Core/FFI/` (Swift-Rust bridge)
- Services: `Libraries/TariLib/Core/Services/` (high-level wallet APIs)
- SwiftUI: `SwiftUI/` (design system components and typography)

### Conventions
- Screen constructors: `ScreenConstructor.buildScene(dependency:)` pattern
- ViewModels use `@Published` properties for reactive data
- All ViewControllers extend base classes with theming
- SwiftUI components follow design system patterns with Poppins typography
- Error handling via `CoreError` protocol with localized messages
- Security: `SecureViewController` prevents screenshots

### Important Notes
- **Setup**: Run `./update_dependencies.sh [mainnet|nextnet]` before building
- **FFI Memory**: Pointer management critical - follow existing patterns
- **Tor Required**: App fails without Tor connectivity
- **Theme System**: Colors defined in Assets.xcassets and Colors.xcassets, components auto-adapt
- **SwiftUI Integration**: New design system with reusable components and typography
- **Backup Encryption**: All backups encrypted before cloud storage
- **Deep Links**: Support `tari://` URLs for transactions and contacts

## Codebase Structure

### Root Directory

| File | Description |
|------|-------------|
| `.gitattributes` | Git attributes configuration for line ending and diff handling. Specifies that .strings files (iOS localization files) should use diff algorithm for better change tracking. Ensures consistent handling of internationalization files across different operating systems and development environments. Critical for maintaining localization file integrity in collaborative development. |
| `.gitignore` | Git ignore configuration for iOS development environment. Excludes: Xcode build artifacts (build/, DerivedData/), user-specific files (xcuserdata/, .vscode), compiled outputs (*.ipa, *.dSYM), CocoaPods cache, Swift Package Manager build files (.build/), fastlane reports, environment files, and Firebase configuration (GoogleService-Info.plist). Ensures clean repository without build artifacts, personal settings, or sensitive configuration files in version control. |
| `.swiftlint.yml` | SwiftLint configuration file defining code style rules and quality standards for the Tari wallet iOS codebase. Sets relaxed limits: line length (230/250), type body length (380/400), function body length (90/110), file length (675/1000). Disables rules: identifier_name, multiple_closures_with_trailing_closure, function_parameter_count, todo, opening_brace. Excludes: Pods directory, UnitTests, and specific files scheduled for refactor (SettingsViewController.swift, AddAmountViewController.swift, EmojiIdView.swift, AnimatedBalanceLabel.swift). Used by CI/CD and development tools for automated code quality enforcement. |
| `Gemfile` | Ruby dependency specification for iOS build automation tools. Defines gems: fastlane (iOS app deployment automation), cocoapods (dependency management). Includes fastlane plugin loading mechanism for extended build capabilities. Used by CI/CD pipeline and local development for automating app store distribution, certificate management, and build processes. Required for project setup and deployment workflows. |
| `LICENSE` | BSD 3-Clause License for the Tari Project wallet iOS application. Copyright 2019 The Tari Project. Permits redistribution and use in source and binary forms with modification under specific conditions: retain copyright notice, reproduce copyright notice in documentation, no endorsement without permission. Disclaims warranties and limits liability for damages. Standard open-source license allowing commercial and non-commercial use while protecting project contributors from legal liability. |
| `Podfile` | CocoaPods dependency specification for MobileWallet iOS app targeting iOS 15.0+. Defines third-party framework dependencies including Tor (network proxy), lottie-ios (animations), Firebase (messaging), Sentry (error tracking - updated to 8.52.0), Giphy (GIF support), cryptographic libraries (Base58Swift), and YAT/TariCommon libraries. Includes post-install hooks to configure deployment targets and fix toolchain references for Xcode compatibility. |
| `Podfile.lock` | [GENERATED] CocoaPods lockfile specifying exact versions of all dependencies and their transitive dependencies. Updated with Sentry 8.52.0 and CocoaPods 1.16.2. Ensures reproducible builds by locking specific versions. Contains pod specifications for frameworks like SwiftEntryKit, GiphyUISDK, YatLib, DropboxSDK, Tor integration, and testing frameworks. Generated by 'pod install' command and should be committed to version control. |
| `README.md` | Project documentation for Aurora - a reference-design mobile wallet app for the Tari digital currency. Contains build instructions using Podfile dependencies, Swift style guide following GitHub standards, version management process, and git branching strategy. Describes nextnet/mainnet setup processes, third-party dependencies (Tor, Firebase, Giphy, etc.), and deployment workflow with develop/feature/release/master branch structure. |
| `dependencies.env` | [CONFIDENTIAL] Environment configuration file containing dependency versions and build settings. Updated FFI_MAINNET_VERSION to 4.5.0. Contains potentially sensitive configuration values for external dependencies and build parameters. Should not be directly accessed - refer to env-example.json for template structure. Used by build system and dependency management tools. |
| `env-example.json` | Environment configuration template defining required API keys and credentials for the Tari wallet app. Contains placeholders for pushServerApiKey (push notifications), sentryPublicDSN (error tracking), appleTeamID (code signing), and giphyApiKey (GIF integration). Developers copy this to env.json and populate with actual values. Used by build system to inject sensitive configuration without committing secrets to repository. |
| `pre_commit.sh` | Git pre-commit hook script ensuring security compliance before code commits. Sanitizes Info.plist by removing sensitive Dropbox API keys from CFBundleURLSchemes configurations, preventing accidental exposure of credentials in version control. Automatically configured by update_dependencies.sh as part of development environment setup. Essential security measure for protecting API keys. |
| `update_dependencies.sh` | Automated build environment setup script for iOS wallet development. Downloads platform-specific Tari FFI library (mainnet vs nextnet) from GitHub releases, configures Xcode preprocessor macros (MAINNET), installs CocoaPods dependencies, updates Info.plist with Dropbox API keys, and sets up git hooks. Reads dependencies.env for version management and env.json for API configuration. Handles network selection and ensures consistent development environment across team. |

### .github/

| File | Description |
|------|-------------|
| `PULL_REQUEST_TEMPLATE.md` | GitHub pull request template for standardized code review process. Structured sections: description, motivation/context, testing details, screenshots/videos for UI changes, change types (UI fix, bug fix, new feature, breaking change, refactor, tests, documentation), checklist (multi-device testing, development branch, squashed commits, tests run, documentation updates). Ensures comprehensive PR documentation and review quality for iOS wallet development workflow. |

#### .github/ISSUE_TEMPLATE/

| File | Description |
|------|-------------|
| `bug_report.md` | GitHub issue template for standardized bug reports. Provides structured format for: bug description, reproduction steps, expected behavior, screenshots, device information (iOS device, OS version, browser), and additional context. Automatically labels issues as 'bug-report' and includes title placeholder. Ensures consistent bug reporting format for easier triaging and debugging by development team. |

### MobileWallet/

| File | Description |
|------|-------------|
| `AppDelegate.swift` | Main application delegate for the Tari iOS wallet app. Handles app lifecycle events, initialization of core services, and deep link processing. Key responsibilities: configures Firebase for push notifications, initializes Giphy SDK for transaction GIFs, sets up background task management, configures audio session for media playback, handles tari:// URL scheme deep links through DeeplinkHandler, manages APNS device token registration, and prevents keyboard extension usage for security. Dependencies: Firebase (notifications), GiphyUISDK (GIFs), BackgroundTaskManager (tasks), ShortcutsManager (iOS shortcuts), NotificationManager (push handling), BackupManager (wallet backup), AppConfigurator (app setup), DeeplinkHandler (URL processing), TariCommon framework. Includes TariView typealias for SwiftUI integration. Critical entry point that orchestrates app initialization and external integrations. |
| `Info.plist` | iOS app configuration plist defining bundle information, permissions, and capabilities for the Tari wallet app. Registers custom URL schemes ('tari', 'dbapi-8-emm', 'dbapi-2'), Dropbox integration, iCloud container support, and background tasks. Declares privacy-sensitive permissions for camera (QR scanning), contacts, Face ID authentication, and photo library access. Includes streamlined Poppins font declarations (6 weights: Black, Bold, Light, Medium, Regular, SemiBold) with standardized naming convention, plus DrukWideTrial font, background notification support, and scene delegate configuration. |
| `MobileWallet-bridging-header.h` | Objective-C bridging header exposing C/Objective-C APIs to Swift. Imports: libminotari_wallet_ffi_ios.xcframework headers for Rust FFI bindings, NetworkTools.h for low-level network utilities. Used by: Swift code requiring access to C FFI functions and Objective-C utilities. Essential for Tari wallet FFI integration and network interface detection. Enables Swift access to Rust wallet core functionality and system network APIs. |
| `SceneDelegate.swift` | Scene delegate managing iOS 13+ scene lifecycle and app state transitions. Handles window setup with custom TariWindow, deep link processing from app launch/notification/shortcuts, Yat integration configuration, and biometric authentication flow. Key features: processes notification responses when app launches, handles deep links during app startup with retry logic for airdrop auth, manages home screen shortcuts, configures Yat SDK with organization settings, sets up theme coordinator, manages foreground/background transitions with authentication requirements, automatically shows LocalAuthViewController when app returns to foreground if wallet exists, clears badge count and cleans up logs on foreground entry. Dependencies: YatLib (human addresses), DeeplinkHandler (URL processing), ShortcutsManager (iOS shortcuts), ThemeCoordinator (theming), AppRouter (navigation), LocalAuthViewController (biometrics), BackupManager (iCloud integration), TariWindow (custom window), Tari wallet core. Critical for app state management and external integration handling. |
| `Tari.entitlements` | iOS app entitlements defining security capabilities and service access. Enables push notifications (development environment), iCloud integration with CloudDocuments and CloudKit for backup synchronization, shared keychain access for secure credential storage across app group, and app groups for data sharing. Configures iCloud container 'iCloud.com.tari.wallet' for cross-device wallet synchronization and backup storage. Essential for security features, cloud backup functionality, and proper iOS system integration. Required for App Store distribution and production iCloud services. |

### MobileWallet.xcodeproj/

| File | Description |
|------|-------------|
| `project.pbxproj` | [GENERATED] Xcode project file containing build configuration, target definitions, file references, and build phases for MobileWallet iOS app. Defines main app target, notification service extension, test targets. Includes build settings for debug/release configurations, signing, deployment targets, framework linking, asset compilation. Contains file system organization mirroring source structure. Generated and maintained by Xcode IDE. Critical for app compilation, dependency management, and project structure. Includes CocoaPods integration, custom build phases, platform-specific settings, SwiftUI design system components, transaction detail views, new color assets, font resources, and payment reference functionality. Updated for iOS 16.0 minimum deployment target and version 1.1.2. |

#### MobileWallet.xcodeproj/project.xcworkspace/

| File | Description |
|------|-------------|
| `contents.xcworkspacedata` | [GENERATED] Xcode workspace configuration file defining project structure. Specifies MobileWallet.xcodeproj as the primary project reference. XML format used by Xcode to organize project files and dependencies. Automatically maintained by Xcode when project structure changes. Required for proper Xcode workspace functionality and project building. |

##### MobileWallet.xcodeproj/project.xcworkspace/xcshareddata/

| File | Description |
|------|-------------|
| `IDEWorkspaceChecks.plist` | [GENERATED] Xcode workspace integrity check configuration. Tracks Mac 32-bit compatibility warnings and other IDE validation settings. Automatically maintained by Xcode during project builds and compatibility checks. Used by Xcode build system for validation and warning management. Standard file for Xcode project configuration. |

##### MobileWallet.xcodeproj/xcshareddata/xcschemes/

| File | Description |
|------|-------------|
| `MobileWallet.xcscheme` | [GENERATED] Xcode build scheme configuration for MobileWallet main app target. Defines build settings, run configuration, test targets, archive settings, and debugging options. Automatically maintained by Xcode during scheme configuration. Controls build behavior, deployment settings, and development workflow for the primary app target. Essential for consistent builds across different environments. |
| `MobileWalletNotificationService.xcscheme` | [GENERATED] Xcode build scheme configuration for MobileWalletNotificationService app extension target. Defines build settings for notification service extension including background processing capabilities. Automatically maintained by Xcode during scheme configuration. Controls push notification handling, background updates, and extension-specific build behavior. Required for notification extension functionality. |

### MobileWallet.xcworkspace/

| File | Description |
|------|-------------|
| `contents.xcworkspacedata` | [GENERATED] Xcode workspace configuration file defining project and dependency structure. References MobileWallet.xcodeproj main project and Pods.xcodeproj for CocoaPods dependencies. XML format automatically maintained by Xcode and CocoaPods. Essential for workspace organization, dependency management, and build system integration. Required for proper Xcode workspace functionality with external dependencies. |

#### MobileWallet.xcworkspace/xcshareddata/

| File | Description |
|------|-------------|
| `IDETemplateMacros.plist` | [CONFIGURATION] Xcode file template customization defining standard file headers for new Swift files. Configures FILEHEADER macro with Tari Project copyright notice, BSD license terms, and auto-populated metadata (filename, author, date, Swift version, macOS version). Ensures consistent licensing and attribution across all project files. Used by Xcode when creating new files from templates. |
| `IDEWorkspaceChecks.plist` | [GENERATED] Xcode workspace integrity check configuration for workspace-level validation. Tracks compatibility warnings and IDE validation settings at workspace level. Automatically maintained by Xcode during workspace operations. Used by Xcode build system for workspace-wide validation and warning management. Standard file for Xcode workspace configuration. |
| `WorkspaceSettings.xcsettings` | [GENERATED] Xcode workspace settings configuration. Currently disables SwiftUI Previews (PreviewsEnabled: false) which is appropriate for this UIKit-based project. Automatically managed by Xcode IDE preferences and workspace configuration. Controls workspace-level development features and build system behavior. |

#### MobileWallet/Assets.xcassets/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Root Xcode asset catalog configuration file. Defines asset catalog version and metadata for Aurora wallet's complete asset management system including icons, colors, and images. Essential for iOS asset compilation and runtime resource loading. |

##### MobileWallet/Assets.xcassets/AppIcon.appiconset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for app icon configuration. Defines universal iOS app icon at 1024x1024 resolution using universe-icon.jpg file. Standard iOS asset catalog format with author metadata. Required for App Store submission and determines app icon displayed on device home screens, App Store listings, and system notifications. |

##### MobileWallet/Assets.xcassets/Assets/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog root configuration metadata for Assets folder. Standard Xcode-generated file containing author and version information for asset catalog management. Provides catalog structure metadata for iOS build system and asset compilation pipeline. |

###### MobileWallet/Assets.xcassets/Assets/AttentionIcon.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for attention/warning icon image set. Defines universal image with multiple scales for different screen densities. Used in error states, warning dialogs, and alert notifications throughout the wallet app. Auto-scaling ensures consistent visual feedback across all iOS devices. |

###### MobileWallet/Assets.xcassets/Assets/BackArrow.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for back navigation arrow icon. Universal image with multiple scales for navigation back buttons and screen transitions. Used in navigation bars and modal dismissal throughout the wallet app for consistent iOS navigation patterns. |

###### MobileWallet/Assets.xcassets/Assets/Close.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for close/dismiss icon. Universal image for modal dismissal buttons, popup close actions, and overlay dismissal. Used throughout the app for consistent modal interaction patterns and user interface closure actions. |

###### MobileWallet/Assets.xcassets/Assets/ContactsTabBar.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for contacts tab bar icon in unselected state. Universal image for bottom tab navigation representing contact book functionality. Used in main navigation bar to access contact management, address book, and social features of the wallet app. |

###### MobileWallet/Assets.xcassets/Assets/ForwardArrow.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for forward navigation arrow icon. Universal image for forward navigation actions, list item disclosure indicators, and progression flows. Used in navigation sequences and list views for indicating actionable items and forward movement through wallet workflows. |

###### MobileWallet/Assets.xcassets/Assets/Gem.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for gem/jewel icon representing Tari currency. Universal image used for currency indicators, balance displays, and Tari token representations throughout the wallet interface. Key branding element for Tari cryptocurrency visual identity. |

###### MobileWallet/Assets.xcassets/Assets/GemBlackSmall.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for small black variant of gem icon. Universal image for compact currency indicators and subtle Tari token representations in lists, smaller UI elements, and secondary contexts where space is limited or visual hierarchy requires less prominence. |

###### MobileWallet/Assets.xcassets/Assets/HandWave.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for hand wave greeting icon. Universal image used in welcome screens, onboarding flows, and friendly user interface elements. Provides welcoming visual cue for new user experiences and positive interaction feedback throughout the wallet app. |

###### MobileWallet/Assets.xcassets/Assets/HomeTabBar.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for home tab bar icon in unselected state. Universal image for main navigation representing home/dashboard functionality. Primary navigation element providing access to wallet balance, transactions, and main wallet interface. |

###### MobileWallet/Assets.xcassets/Assets/HomeTabBarSelected.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for home tab bar icon in selected state. Universal image showing active/highlighted state when home tab is currently selected. Provides visual feedback for current navigation context and user location within the app. |

###### MobileWallet/Assets.xcassets/Assets/LoginBanner.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for login screen banner image. Universal image displayed during authentication flows, app launch, and security verification screens. Provides branding and visual identity during user authentication and onboarding processes. |

###### MobileWallet/Assets.xcassets/Assets/Mined.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for mining completion/mined transaction icon. Universal image representing successfully mined transactions, confirmed mining rewards, and completed mining operations. Used in transaction history and mining status displays. |

###### MobileWallet/Assets.xcassets/Assets/MinersIcon.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for mining/miners icon representing mining functionality. Universal image for mining operations, miner status, and mining-related features. Used in mining dashboard, reward tracking, and network participation interfaces within the wallet app. |

###### MobileWallet/Assets.xcassets/Assets/RewardsTabBar.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for rewards tab bar icon in unselected state. Universal image for navigation to rewards, mining, and earning functionality. Provides access to reward tracking, mining status, and earning-related features within the wallet application. |

###### MobileWallet/Assets.xcassets/Assets/ScanQR.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for QR code scanner icon. Universal image for QR code scanning functionality buttons and camera access actions. Used in contact import, payment address scanning, and deep link QR code interactions throughout the wallet app. |

###### MobileWallet/Assets.xcassets/Assets/ScheduledIcon.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for scheduled transaction/pending operation icon. Universal image representing pending transactions, scheduled operations, and future-dated activities. Used in transaction lists to indicate timing and status of pending wallet operations. |

###### MobileWallet/Assets.xcassets/Assets/SearchIcon.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for search functionality icon. Universal image for search buttons, search field indicators, and lookup functionality. Used in contact search, transaction filtering, and general search interfaces throughout the wallet application. |

###### MobileWallet/Assets.xcassets/Assets/SelectedContactsTabBar.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for contacts tab bar icon in selected state. Universal image showing active/highlighted state when contacts tab is currently selected. Indicates current navigation position and provides visual feedback for contact-related functionality access. |

###### MobileWallet/Assets.xcassets/Assets/SelectedRewardsTabBar.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for rewards tab bar icon in selected state. Universal image showing active state for rewards/mining functionality tab. Indicates current navigation position for rewards, mining, and earning-related features within the wallet app. |

###### MobileWallet/Assets.xcassets/Assets/SelectedSettingsTabBar.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for settings tab bar icon in selected state. Universal image showing active state when settings tab is currently selected. Provides navigation feedback for app configuration, preferences, and administrative functionality access. |

###### MobileWallet/Assets.xcassets/Assets/SelectedStoreTabBar.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for store tab bar icon in selected state. Universal image showing active state for store/marketplace functionality. Indicates navigation position for commerce, store, and marketplace-related features within the wallet ecosystem. |

###### MobileWallet/Assets.xcassets/Assets/SendCopy.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for send/copy action icon. Universal image for send transaction actions and copy to clipboard functionality. Used in transaction sending flows and address copying interactions throughout the wallet interface. |

###### MobileWallet/Assets.xcassets/Assets/SendFundsSeparator.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset catalog configuration for send funds separator graphic. Defines visual divider element used in transaction sending flow interface to separate different sections or steps in the payment process. |

###### MobileWallet/Assets.xcassets/Assets/SendTariIcon.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset configuration for SendTariIcon image set. Defines multi-scale support (1x, 2x, 3x) for universal iOS idiom. Used in the send Tari flow interface, likely for the send button or transaction sending visual indicators. Core element of the transaction sending user experience. |

###### MobileWallet/Assets.xcassets/Assets/SettingsTabBar.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for settings tab bar icon in unselected state. Universal image for navigation to app settings, preferences, and configuration options. Provides access to user preferences, security settings, network configuration, and app customization features. |

###### MobileWallet/Assets.xcassets/Assets/StoreTabBar.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for store tab bar icon in unselected state. Universal image for navigation to marketplace and store functionality. Represents commerce features, digital goods, and marketplace integration within the Tari wallet ecosystem. |

###### MobileWallet/Assets.xcassets/Assets/SuccessIcon.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset configuration for SuccessIcon image set. Defines multi-scale support (1x, 2x, 3x) for universal iOS idiom. Used to indicate successful operations throughout the app, particularly for transaction confirmations, wallet creation success, and other positive feedback scenarios. |

###### MobileWallet/Assets.xcassets/Assets/TabBar/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for tab bar icon folder organization. Standard catalog structure file for grouping tab bar related icons and maintaining asset organization. Used by Xcode for asset management and build-time optimization of tab bar navigation icons. |

###### MobileWallet/Assets.xcassets/Assets/TabBar/navHome.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset configuration for navHome tabbar icon. Defines multi-scale support (1x, 2x, 3x) for universal iOS idiom. Image filenames: navHome.png variants. Used for the home tab icon in the bottom navigation bar. Core navigation element for accessing main wallet interface. |

###### MobileWallet/Assets.xcassets/Assets/TabBar/navSettings.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset catalog configuration for settings navigation icon in tab bar. Defines multi-resolution image set (1x, 2x, 3x scales) for settings tab navigation element. Part of main app navigation system with universal idiom support. |

###### MobileWallet/Assets.xcassets/Assets/TabBar/navTtl.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset catalog configuration for TTL (time-to-live) navigation icon in tab bar. Defines multi-resolution image set for specialized wallet feature tab related to transaction timing and expiry. Part of advanced wallet functionality navigation. |

###### MobileWallet/Assets.xcassets/Assets/TariIcon.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog metadata for Tari brand icon image set. Defines universal image with three scales: 1x (group15.png), 2x (group15@2x.png), 3x (group15@3x.png) for different screen densities. Used throughout app UI for Tari branding and visual identity. Auto-selected by iOS based on device screen resolution and ensures crisp rendering across all device types. |

###### MobileWallet/Assets.xcassets/Assets/WalletCard.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset configuration for WalletCard image set. Defines multi-scale support (1x, 2x, 3x) for universal iOS idiom. Image filenames: Group 660236415.png variants. Used for wallet card visual representation in the main interface, likely as background or decorative element for balance display area. |

###### MobileWallet/Assets.xcassets/Assets/Wave.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset configuration for Wave animation graphic. Defines multi-scale support (1x, 2x, 3x) for universal iOS idiom. Used for animated wave effects in the UI, likely on the home screen or transaction animations. Part of Tari's distinctive visual design language. |

###### MobileWallet/Assets.xcassets/Assets/Welcome_graphic.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset catalog configuration for welcome screen graphic. Defines multi-resolution image set for onboarding or welcome interface branding element. Used in initial user experience and app introduction flows. |

###### MobileWallet/Assets.xcassets/Assets/cancelGiphy.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset configuration for cancel Giphy icon. Defines multi-scale support (1x, 2x, 3x) for universal iOS idiom. Used to cancel or remove GIF selection in transaction message composition. Part of the Giphy integration UI controls. |

###### MobileWallet/Assets.xcassets/Assets/copy.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset configuration for copy icon image set. Defines multi-scale support (1x, 2x, 3x) for universal iOS idiom. Used for copy-to-clipboard functionality throughout the app, particularly for wallet addresses, transaction IDs, and other copyable text elements. Essential UX element for address sharing. |

###### MobileWallet/Assets.xcassets/Assets/discloseHide.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset catalog configuration for disclose/hide toggle icon. Defines multi-resolution image set for UI elements that show/hide content such as expandable sections, password fields, or collapsible information panels. |

###### MobileWallet/Assets.xcassets/Assets/edit-contact.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset catalog configuration for edit contact icon. Defines multi-resolution image set for contact editing interface action button, used in contact book management and address book modification workflows. |

###### MobileWallet/Assets.xcassets/Assets/elipse.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog configuration file for the 'elipse' image asset. Contains metadata for multi-resolution image variants (1x, 2x, 3x) used for different screen densities. References physical image files: Ellipse 3002.png, Ellipse 3002@2x.png, Ellipse 3002@3x.png. Part of the app's visual asset system for universal iOS device support. Auto-generated by Xcode with standard asset catalog structure. |

###### MobileWallet/Assets.xcassets/Assets/emojiAddress.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset configuration for emojiAddress icon image set. Defines multi-scale support (1x, 2x, 3x) for universal iOS idiom. Used to represent emoji-encoded Tari addresses, indicating when an address is displayed in emoji format rather than hex. Key visual cue for Tari's user-friendly address system. |

###### MobileWallet/Assets.xcassets/Assets/gems.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset configuration for gems image set. Defines multi-scale support (1x, 2x, 3x) for universal iOS idiom. Image filenames: gems.png variants. Used for Tari gem graphics throughout the application, likely for rewards, decorative elements, or transaction success indicators. Part of Tari's distinctive visual identity. |

###### MobileWallet/Assets.xcassets/Assets/nerdEmoji.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog configuration file for the 'nerdEmoji' image asset. Contains metadata for multi-resolution image variants (1x, 2x, 3x) used for different screen densities. This emoji asset is likely used in the wallet's UI for user interactions or transaction messaging. Auto-generated by Xcode with standard asset catalog structure. |

###### MobileWallet/Assets.xcassets/Assets/notch_down.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog configuration for the 'notch_down' image asset. Multi-resolution UI icon for downward-facing notch or arrow interface element. Used in navigation or user interaction flows within the Tari wallet app. Contains 1x, 2x, 3x variants for different screen densities. |

###### MobileWallet/Assets.xcassets/Assets/notificationsGraphic.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog configuration for 'notificationsGraphic' image asset. Visual graphic used in notification-related UI screens, likely for permission requests or notification settings. Multi-resolution support (1x, 2x, 3x) for optimal display across device types. Part of the wallet's notification management interface. |

###### MobileWallet/Assets.xcassets/Assets/numpad-delete.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog configuration for 'numpad-delete' icon asset. Delete/backspace button icon used in custom numeric keypads for amount entry during transactions. Multi-resolution variants (1x, 2x, 3x) for universal device support. Critical UI component for transaction input validation and editing. |

###### MobileWallet/Assets.xcassets/Assets/numpad.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog configuration for 'numpad' icon asset. Numeric keypad interface icon used for transaction amount entry and PIN input screens. Multi-resolution support ensures crisp display across all iOS devices. Essential UI component for secure financial data entry. |

###### MobileWallet/Assets.xcassets/Assets/openExplorer.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog configuration for 'openExplorer' icon asset. External link icon used to open blockchain explorer for transaction verification. References Group 1321314665.png variants. Enables users to view transaction details on external Tari blockchain explorer. Multi-resolution support for universal device compatibility. |

###### MobileWallet/Assets.xcassets/Assets/poweredByGiphy.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset configuration for 'Powered by Giphy' attribution image. Defines multi-scale support (1x, 2x, 3x) for universal iOS idiom. Required branding attribution for Giphy GIF integration in transaction messages. Ensures compliance with Giphy API terms of service. |

###### MobileWallet/Assets.xcassets/Assets/qr.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS imageset configuration for QR code scanning and generation icon. Used in interfaces related to QR code functionality including scanning for payments, displaying wallet addresses, and sharing transaction data. Contains multi-resolution image assets with template rendering for consistent theming. Essential component of the payment and address sharing systems. |

###### MobileWallet/Assets.xcassets/Assets/share.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset configuration for share icon image set. Defines multi-scale support (1x, 2x, 3x) for universal iOS idiom. Used for sharing functionality throughout the app, enabling users to share wallet addresses, transaction details, QR codes, and profile information via iOS native sharing interface. |

###### MobileWallet/Assets.xcassets/Assets/staticSplash.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset catalog configuration for static splash screen image. Defines multi-resolution image set for app launch screen graphic, providing branding and visual continuity during app initialization and loading processes. |

###### MobileWallet/Assets.xcassets/Assets/textAddress.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset configuration for textAddress icon. Defines multi-scale support (1x, 2x, 3x) for universal iOS idiom. Used to indicate when a Tari address is displayed in hexadecimal text format rather than emoji format. Provides visual distinction between address display modes. |

###### MobileWallet/Assets.xcassets/Assets/turtle.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Asset configuration for turtle graphic image set. Defines multi-scale support (1x, 2x, 3x) for universal iOS idiom. Used as decorative or mascot element in the Tari wallet interface, potentially for slow connection states or as part of the brand personality. |

###### MobileWallet/Assets.xcassets/Assets/unknownUser.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog configuration for 'unknownUser' placeholder image. Default avatar/profile image used when contact has no custom image set. Used in contact list, transaction details, and profile screens. Multi-resolution variants ensure consistent appearance across device types. Part of the contact management UI system. |

##### MobileWallet/Assets.xcassets/Colors/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog root metadata file for the Colors asset group. Contains Xcode authoring information and version metadata for the entire color system. Organizes the app's complete color design system including Color Palettes (Dark/Light themes) and Colors (adaptive theming). Used by Xcode build system for color asset compilation and runtime access. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/

| File | Description |
|------|-------------|
| `Contents.json` | Asset catalog folder configuration for Color Palettes directory. Standard Xcode asset catalog configuration file. Contains theme-based color organization for the application's design system. Parent directory for Dark and Light theme color definitions. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/

| File | Description |
|------|-------------|
| `Contents.json` | Asset catalog namespace configuration for Dark theme color system. Root configuration for all dark theme color categories including backgrounds, brand colors, buttons, components, icons, neutral colors, shadows, system colors, and text colors. Central organizational structure for dark mode design system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Backgrounds/

| File | Description |
|------|-------------|
| `Contents.json` | Asset catalog namespace configuration for Dark theme backgrounds. Sets provides-namespace property to true, allowing organization of background color assets. Contains color definitions for dark theme background variations used throughout the application interface. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Backgrounds/Primary.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Dark theme primary background color definition in iOS color asset format. Specifies sRGB color space with RGB values (0x11, 0x11, 0x11) creating a near-black background (#111111) with full alpha transparency. Part of the Dark theme color palette used for main background surfaces throughout the app. Essential component of the design system's dark mode implementation ensuring consistent visual appearance across all screens and components. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Backgrounds/Secondary.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Asset catalog color definition for dark theme secondary background color. Defines sRGB color (#17181F) used as secondary background surfaces in dark mode. Part of the dark theme color palette system. Used by: Secondary UI surfaces, card backgrounds, and overlay elements in dark mode theme. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Brand/

| File | Description |
|------|-------------|
| `Contents.json` | Asset catalog namespace configuration for Dark theme brand colors. Contains Tari brand color definitions optimized for dark mode interface. Includes primary brand colors like Dark Blue, Pink, and Purple used for branding elements throughout the dark theme. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Brand/Dark Blue.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Color asset definition for Dark Blue brand color in dark theme. RGB values: #40388A (64, 56, 138). Part of the Tari brand color palette optimized for dark mode interface. Used for primary brand elements, buttons, and accent colors in dark theme throughout the application. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Brand/Pink.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode color asset definition for brand pink color in dark theme. Defines sRGB color with RGB values (0xE3, 0x20, 0xBC) and full alpha (1.0), creating a bright magenta/pink brand color. Universal color for all iOS devices. Part of design system color palette, used throughout app for brand elements, buttons, and accents. Auto-generated by Xcode asset catalog when color is defined. Regenerated when color values are modified in asset catalog. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Brand/Purple.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Asset catalog color definition for dark theme brand purple color. Defines sRGB color (#9330FF) used for Tari brand elements in dark mode. Part of the dark theme brand color palette. Used by: Brand elements, primary accent colors, and Tari-specific UI components in dark mode. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Buttons/

| File | Description |
|------|-------------|
| `Contents.json` | Asset catalog namespace configuration for Dark theme button colors. Contains color definitions for all button states in dark mode including primary, secondary, disabled states and their text colors. Essential for consistent button styling across the dark theme interface. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Buttons/Disabled Text.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Color asset definition for disabled button text in dark theme. RGB values: #545454 (84, 84, 84) - medium gray. Part of the dark theme accessibility design system. Used for text on disabled buttons and interactive elements to provide visual feedback and comply with accessibility guidelines. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Buttons/Disabled.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Dark theme color definition for disabled button state. Defines RGB color (#222222) with full opacity for button elements that are inactive or non-interactive. Part of comprehensive dark mode color palette ensuring accessibility and visual hierarchy. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Buttons/Primary Background.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Asset catalog color definition for dark theme primary button background color. Defines sRGB color (#FEFEFE) used as the background color for primary buttons in dark mode. Part of the dark theme button color system. Used by: Primary action buttons, submit buttons, and main interactive elements in dark mode. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Buttons/Primary Border.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for dark theme primary button border color. Defines RGB color values (R:0x00, G:0x00, B:0x00, Alpha:1.0) for consistent button styling. Part of the dark theme design system ensuring uniform appearance across button components. Used by Theme.swift and button classes for UI consistency. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Buttons/Primary Text.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for dark theme primary button text color. Defines RGB color values for optimal text contrast on primary buttons in dark mode. Part of the comprehensive dark theme color system ensuring accessibility and visual consistency. Referenced by button UI components and Theme.swift. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Buttons/Secondary Background.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for dark theme secondary button background color. Defines RGB values for secondary button styling in dark mode interface. Part of the hierarchical button color system enabling clear visual distinction between primary and secondary actions. Used throughout the wallet's UI component library. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Buttons/Secondary Border.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS color asset definition for secondary button border in dark theme. Defines light gray color (RGB: 254,254,254, alpha: 1.0) used for button outlines in dark mode UI. Part of the dark theme design system's button styling hierarchy. Used by secondary buttons to provide visual separation and contrast against dark backgrounds. Maintained by Xcode Asset Catalog system for automatic dark/light theme switching. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Buttons/Secondary Text.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS color asset definition for secondary button text in dark theme. Defines white color (RGB: 255,255,255, alpha: 1.0) for secondary button text labels in dark mode. Part of the design system ensuring proper text contrast and readability on dark backgrounds. Used by secondary button components throughout the app to maintain consistent text appearance in dark theme. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Components/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog namespace container for dark theme component colors. Provides organizational structure for component-specific colors in dark mode (overlays, QR backgrounds, etc.). Contains provides-namespace property indicating this is a folder grouping related color assets. Used by Xcode to organize the design system's dark theme component color definitions. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Components/Overlay.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS color asset definition for overlay backgrounds in dark theme. Defines dark gray color (RGB: 40,41,45, alpha: 1.0) used for modal overlays, popup backgrounds, and semi-transparent layers in dark mode. Part of the component color system ensuring proper visual hierarchy and content separation. Used by modal views, popups, and overlay components to create depth and focus. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Components/QR Background.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS color asset definition for QR code background in dark theme. Defines white color (RGB: 255,255,255, alpha: 1.0) used as background for QR code display areas in dark mode. Part of the component color system ensuring QR codes remain scannable and visible against dark interfaces. Used by QR code generation and display components to provide proper contrast for machine readability. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Icons/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog namespace configuration for dark theme icon colors. Provides namespace organization for icon color assets in dark mode interface. Part of the design system's dark theme color palette supporting icon tinting and visual states. Used by Theme.swift for dynamic theming. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Icons/Active.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS color asset definition for active icon state in dark theme. Defines purple/magenta color (RGB: 147,48,255, alpha: 1.0) used for active, selected, or highlighted icons in dark mode. Part of the icon color system indicating interactive state and user focus. Used by tab bar icons, navigation elements, and interactive controls to show active selection in dark theme. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Icons/Default.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset definition for default icon color in dark theme. Defines white (#FFFFFF) as the standard icon color for dark mode UI elements. Part of the comprehensive Aurora wallet design system supporting dark/light theme adaptability. Used throughout the app for icon tinting and navigation elements in dark mode. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Icons/Inactive.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset definition for inactive icon color in dark theme. Defines muted gray (#646B84) for disabled or inactive UI elements in dark mode. Part of the Aurora design system accessibility features ensuring proper contrast and visual hierarchy for inactive states. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Neutral/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog configuration defining the Neutral namespace for dark theme color palette. Creates organizational structure for neutral/gray colors in the dark mode design system. Provides color hierarchy for backgrounds, borders, and subtle UI elements. Referenced by Theme.swift for semantic color mapping. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Neutral/Inactive.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for inactive neutral color in dark theme. Provides muted gray tone for disabled states and inactive UI elements in dark mode. Essential for accessibility compliance and visual hierarchy. Used by form controls, buttons, and text fields in disabled states. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Neutral/Primary.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for primary neutral color in dark theme. Defines main neutral/gray color used for text, borders, and primary UI elements in dark mode. Essential for text readability and contrast compliance. Referenced by Theme.primaryNeutralColor throughout the app. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Neutral/Secondary.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for secondary neutral color in dark theme. Provides lighter neutral tone for secondary text, subtle borders, and background elements in dark mode. Used for visual hierarchy and content separation. Referenced by labels and secondary UI components. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Neutral/Tertiary.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for tertiary neutral color in dark theme. Defines subtle neutral tone for least prominent UI elements, dividers, and background accents in dark mode. Used for maximum visual hierarchy differentiation and minimal contrast elements. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Shadows/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog configuration defining the Shadows namespace for dark theme color palette. Creates organizational structure for shadow-related colors in the dark mode design system. Provides color hierarchy for UI depth and elevation effects. Referenced by Theme.swift for shadow styling. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Shadows/Box.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for box shadow color in dark theme. Specifies shadow color used for card components, modals, and elevated UI elements in dark mode. Essential for depth perception and layered interface design. Referenced by card views and popup components. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/System/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog configuration defining the System namespace for dark theme color palette. Creates organizational structure for system-level colors (success, error, warning, info) in dark mode design system. Provides semantic color hierarchy for status indicators and system feedback. Referenced by Theme.swift for state-based styling. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/System/Blue.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for system blue color in dark theme. Defines blue accent color for informational elements, links, and primary actions in dark mode. Used for interactive elements, navigation highlights, and information states. Part of semantic color system for consistent user feedback. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/System/Green.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for system green color in dark theme. Defines green color for success states, positive balance indicators, and confirmed transactions in dark mode. Essential for transaction status feedback and positive user actions. Used by transaction list and balance displays. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/System/Light Blue.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for light blue system color in dark theme. Provides lighter variant of system blue for subtle informational elements and secondary actions in dark mode. Used for secondary links, info backgrounds, and less prominent interactive elements. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/System/Light Green.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for light green system color in dark theme. Provides lighter variant of system green for subtle success indicators and positive feedback in dark mode. Used for success backgrounds, positive change indicators, and confirmed action feedback. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/System/Light Orange.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for light orange system color in dark theme. Provides lighter variant of system orange for subtle warning indicators and moderate alerts in dark mode. Used for warning backgrounds, moderate priority badges, and attention-requesting elements. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/System/Light Red.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for light red system color in dark theme. Provides lighter variant of system red for subtle error indicators and negative feedback in dark mode. Used for error backgrounds, failed action indicators, and destructive action warnings. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/System/Light Yellow.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for light yellow system color in dark theme. Provides lighter variant of system yellow for subtle warning indicators and pending states in dark mode. Used for pending transaction backgrounds, caution highlights, and temporary status indicators. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/System/Orange.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for system orange color in dark theme. Defines orange color for general warnings, moderate alerts, and neutral status indicators in dark mode. Used for balance-neutral states and moderate priority notifications. Referenced by notification badges and status indicators. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/System/Red.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for system red color in dark theme. Defines red color for error states, negative balance indicators, and failed transactions in dark mode. Critical for error feedback and destructive actions. Used by error messages, failed transaction states, and warning dialogs. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/System/Yellow.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for system yellow color in dark theme. Defines yellow color for warning states, pending transactions, and caution indicators in dark mode. Used for temporary states and attention-requiring elements. Referenced by pending transaction indicators and warning messages. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Text/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog configuration defining the Text namespace for dark theme color palette. Creates organizational structure for text-related colors in the dark mode design system. Provides color hierarchy for all text elements, headings, body text, and links. Referenced by Theme.swift for text styling. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Text/Body.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for body text color in dark theme. Defines primary text color for main content, descriptions, and readable text in dark mode interface. Essential for accessibility and readability compliance. Used by labels, paragraphs, and content throughout the app. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Text/Heading.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for heading text color in dark theme. Defines high-contrast text color for headers, titles, and emphasized text in dark mode interface. Used for maximum readability and visual hierarchy. Referenced by title labels and navigation headers. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Text/Light Text.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for light text color in dark theme. Provides lower-contrast text color for secondary information, captions, and subtle text in dark mode. Used for metadata, timestamps, and less prominent textual content. Essential for visual hierarchy. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Text/Links.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for link text color in dark theme. Defines clickable text color for hyperlinks, navigation links, and interactive text in dark mode interface. Essential for user interaction feedback and accessibility. Used by clickable labels and navigation elements. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Text/Primary.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS color asset definition for primary text in dark theme. Defines light gray color (RGB: 182,183,195, alpha: 1.0) used for main body text and primary content in dark mode. Part of the text color hierarchy ensuring optimal readability on dark backgrounds. Used by labels, headings, and primary text content throughout the dark theme interface. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Dark/Text/Title.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for title text color in dark theme. Defines maximum-contrast text color for main titles, screen headers, and primary text elements in dark mode. Used for highest visual hierarchy and maximum readability. Referenced by main navigation titles. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog configuration defining the Light namespace for the complete light theme color palette. Creates organizational structure for all light mode colors including backgrounds, text, buttons, icons, and system colors. Root container for light theme design system referenced by Theme.swift. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Backgrounds/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog configuration defining the Backgrounds namespace for light theme color palette. Creates organizational structure for background-related colors in the light mode design system. Provides color hierarchy for primary and secondary backgrounds. Referenced by Theme.swift for background styling. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Backgrounds/Primary.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Light theme primary background color definition. Defines white background color (#FFFFFF) with full opacity for main content areas in light mode. Foundation color for app's visual hierarchy and readability in standard theme. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Backgrounds/Secondary.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for secondary background color in light theme. Defines subtle background color for cards, modals, and elevated content in light mode interface. Used for layered content and visual depth. Referenced by card components and popup backgrounds. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Brand/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog configuration defining the Brand namespace for light theme color palette. Creates organizational structure for Tari brand colors in light mode design system. Contains brand identity colors including Tari purple/pink gradient colors. Referenced by Theme.swift for brand consistency. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Brand/Dark Blue.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode Asset Catalog color definition for dark blue brand color in light theme. Defines Tari's secondary brand color (dark blue) for use in light mode interface. Used for brand accents, secondary actions, and brand identity elements. Part of official Tari brand color system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Brand/Pink.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Brand pink color definition (RGB: #E320BC) for light theme. Part of Tari's brand color palette used for accent elements, highlights, and brand consistency across the interface. Color space: sRGB, alpha: 1.0. Referenced by Theme.swift and brand-specific UI components. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Brand/Purple.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS color asset definition for brand purple in light theme. Defines Tari brand purple color (RGB: 147,48,255, alpha: 1.0) used for branding elements and primary accents in light mode. Part of the brand color system maintaining visual identity consistency. Used by brand elements, primary buttons, highlights, and Tari-specific UI components in light theme. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Buttons/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset namespace container for light theme button colors. Provides organizational structure for button-related color definitions in the Aurora design system. Contains disabled, primary, secondary button color specifications for consistent light mode button styling across the wallet interface. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Buttons/Disabled Text.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for disabled button text in light theme. Defines muted brown-gray (#7F8599) ensuring accessibility compliance for disabled button states. Critical for user experience indicating non-interactive elements in the Aurora wallet interface. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Buttons/Disabled.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for disabled button background in light theme. Defines light gray (#ECECEC) providing visual indication of non-interactive button states. Essential for Aurora wallet UX consistency and accessibility standards compliance. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Buttons/Primary Background.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Primary button background color for light theme (RGB: #000000 - black). Core component of the design system's button styling for primary actions like Send, Confirm, and key user interactions. Used by BaseButton and action button components throughout the app. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Buttons/Primary Border.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for primary button border in light theme. Defines off-white (#F9F9F9) for primary button borders providing subtle definition. Part of Aurora design system ensuring consistent primary action button styling across wallet interface. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Buttons/Primary Text.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for primary button text color in light theme. Used for text and icons on primary action buttons throughout the Aurora wallet interface. Ensures readable contrast and brand consistency for critical user actions like sending Tari. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Buttons/Secondary Background.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for secondary button background in light theme. Defines background color for secondary action buttons providing visual hierarchy below primary actions. Essential for Aurora wallet UI organization and user flow guidance. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Buttons/Secondary Border.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for secondary button border in light theme. Provides border definition for secondary action buttons in Aurora wallet interface. Maintains visual consistency while establishing clear button hierarchy. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Buttons/Secondary Text.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for secondary button text color in light theme. Used for text and icons on secondary action buttons throughout Aurora wallet. Ensures proper contrast and readability for secondary user actions. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Components/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset namespace container for light theme component colors. Organizational structure for component-specific color definitions including overlays, QR backgrounds, and special UI elements in Aurora design system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Components/Overlay.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for overlay backgrounds in light theme. Used for modal overlays, popup backgrounds, and semi-transparent layers in Aurora wallet interface. Critical for maintaining visual hierarchy and focus management. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Components/QR Background.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for QR code background in light theme. Provides optimal contrast background for QR code display ensuring reliable scanning. Used in receive payment screens and contact sharing features throughout Aurora wallet. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Icons/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset namespace container for light theme icon colors. Organizational structure for icon color definitions including active, default, and inactive states in Aurora design system light mode. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Icons/Active.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for active icon color in light theme. Defines the primary color for active/selected icons in light mode interface. Used for navigation highlights, selected states, and active UI elements in Aurora wallet. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Icons/Default.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for default icon color in light theme. Standard icon color for light mode UI elements providing consistent icon tinting throughout Aurora wallet interface. Used for navigation, toolbar, and content icons in light mode. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Icons/Inactive.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for inactive icon color in light theme. Muted color for disabled or inactive UI elements in light mode ensuring proper accessibility contrast while indicating non-interactive states in Aurora wallet. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Neutral/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset namespace container for light theme neutral colors. Organizational structure for neutral color palette including primary, secondary, tertiary, and inactive neutral tones in Aurora design system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Neutral/Inactive.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for inactive neutral color in light theme. Used for disabled states, placeholders, and inactive UI elements requiring neutral, accessible coloring throughout Aurora wallet interface. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Neutral/Primary.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for primary neutral color in light theme. Main neutral color used for borders, dividers, and subtle UI elements providing structure without visual emphasis in Aurora wallet design. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Neutral/Secondary.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for secondary neutral color in light theme. Used for secondary borders, dividers, and subtle structural elements providing visual organization without emphasis in Aurora wallet interface. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Neutral/Tertiary.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for tertiary neutral color in light theme. Lightest neutral tone used for subtle backgrounds, secondary separators, and minimal emphasis elements in Aurora design system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Shadows/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset namespace container for light theme shadow colors. Organizational structure for shadow effect color definitions including box shadows and elevation effects in Aurora design system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Shadows/Box.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for box shadow color in light theme. Used for drop shadows, elevations, and depth effects on cards, modals, and elevated UI components in Aurora wallet design system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/System/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset namespace container for light theme system colors. Organizational structure for system color palette including semantic colors for success, error, warning, and informational states in Aurora wallet interface. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/System/Blue.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | System blue color for light theme (RGB: #2399BE). Used for system elements, informational UI components, and iOS integration. Part of the semantic color system for status indicators, links, and informational elements. Complements iOS design guidelines. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/System/Green.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for the light theme system green color (#23BE90). Part of the iOS light color palette system used for semantic green UI elements like success states, positive indicators, and accent colors. RGB values: Red=0x23, Green=0xBE, Blue=0x90, Alpha=1.0. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/System/Light Blue.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for the light theme system light blue color (#A8E5F8). Part of the iOS light color palette system used for subtle blue backgrounds, information states, and secondary accent colors. RGB values: Red=0xA8, Green=0xE5, Blue=0xF8, Alpha=1.0. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/System/Light Green.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for the light theme system light green color (#C8F5E4). Part of the iOS light color palette system used for subtle green backgrounds, success message tints, and positive feedback UI elements. RGB values: Red=0xC8, Green=0xF5, Blue=0xE4, Alpha=1.0. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/System/Light Orange.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for the light theme system light orange color (#FFDCCD). Part of the iOS light color palette system used for subtle orange backgrounds, warning message tints, and attention-drawing UI elements. RGB values: Red=0xFF, Green=0xDC, Blue=0xCD, Alpha=1.0. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/System/Light Red.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for the light theme system light red color (#F9E1E4). Part of the iOS light color palette system used for subtle red backgrounds, error message tints, and negative feedback UI elements. RGB values: Red=0xF9, Green=0xE1, Blue=0xE4, Alpha=1.0. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/System/Light Yellow.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for the light theme system light yellow color (#FBE7C0). Part of the iOS light color palette system used for subtle yellow backgrounds, warning message tints, and informational UI elements. RGB values: Red=0xFB, Green=0xE7, Blue=0xC0, Alpha=1.0. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/System/Orange.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for the light theme system orange color (#FF631F). Part of the iOS light color palette system used for vibrant orange accents, warning states, and attention-grabbing UI elements. RGB values: Red=0xFF, Green=0x63, Blue=0x1F, Alpha=1.0. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/System/Red.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for the light theme system red color (#D32C44). Part of the iOS light color palette system used for error states, destructive actions, negative indicators, and critical alerts. RGB values: Red=0xD3, Green=0x2C, Blue=0x44, Alpha=1.0. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/System/Yellow.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for the light theme system yellow color (#FCAA2F). Part of the iOS light color palette system used for warning states, pending actions, caution indicators, and informational highlights. RGB values: Red=0xFC, Green=0xAA, Blue=0x2F, Alpha=1.0. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Text/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset namespace container for light theme text colors. Organizational structure for typography color definitions including body text, headings, links, and light text variants in Aurora design system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Text/Body.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for the light theme body text color (#111111). Part of the iOS light color palette text system used for primary body text, main content text, and standard readable text throughout the app. Dark gray color optimized for readability on light backgrounds. RGB values: Red=0x11, Green=0x11, Blue=0x11, Alpha=1.0. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Text/Heading.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for the light theme heading text color (#111111). Part of the iOS light color palette text system used for headings, titles, section headers, and emphasized text elements. Same color as body text for consistent hierarchy. RGB values: Red=0x11, Green=0x11, Blue=0x11, Alpha=1.0. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Text/Light Text.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for the light theme light text color (#7F8599). Part of the iOS light color palette text system used for secondary text, subtitles, placeholder text, and less emphasized content. Medium gray color for reduced visual weight on light backgrounds. RGB values: Red=0x7F, Green=0x85, Blue=0x99, Alpha=1.0. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Text/Links.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for the light theme link text color (#7F8599). Part of the iOS light color palette text system used for hyperlinks, interactive text elements, and clickable text content. Same gray color as light text for subtle link appearance. RGB values: Red=0x7F, Green=0x85, Blue=0x99, Alpha=1.0. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Text/Primary.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for primary text color in light theme. Main text color providing high contrast readability for body text, labels, and primary content throughout Aurora wallet interface. |

###### MobileWallet/Assets.xcassets/Colors/Color Palettes/Light/Text/Title.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for the light theme title text color (#000000). Part of the iOS light color palette text system used for page titles, section titles, and highest-priority text elements. Pure black for maximum contrast and emphasis on light backgrounds. RGB values: Red=0x00, Green=0x00, Blue=0x00, Alpha=1.0. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Colors/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog metadata file for the Colors folder. Contains Xcode authoring information and version metadata for the color asset organization. Defines the structure for the app's comprehensive color system including action, background, button, and common colors. Used by Xcode for managing the color asset hierarchy and build process. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Action/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset namespace container for action state colors. Organizational structure for interactive element colors including focus, hover, disabled, and selected states in Aurora design system. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Action/active.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog adaptive color definition for active UI elements. Supports both light and dark appearances with different RGB values for each theme. Light mode: RGB(0x7F, 0x7F, 0x7F), Dark mode: RGB(0x8B, 0x8B, 0x8B). Used for interactive elements in their active state. Ensures consistent user experience across theme changes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Action/disabled.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS color asset with adaptive theming for disabled action elements. Defines light gray (RGB: 153,153,153) for light mode and darker gray (RGB: 115,115,116) for dark mode. Supports automatic appearance switching based on device theme. Used by disabled buttons, inactive controls, and non-interactive UI elements to indicate unavailable state. Part of the action color system ensuring accessibility and visual feedback. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Action/disabledBackground.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for disabled button/action backgrounds with dark/light mode support. Light mode: #F2F2F2 (light gray), Dark mode: #2E2E2F (dark gray). Used throughout the app for inactive buttons, disabled form elements, and non-interactive UI components. Supports iOS automatic appearance switching. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Action/focus.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for action focus states with dark/light mode support. Light mode: #E5E5E5 (light gray), Dark mode: #2E2E2F (dark gray). Used for focused interactive elements, keyboard navigation highlights, and accessibility focus indicators throughout the app. Supports iOS automatic appearance switching. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Action/hover.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode Asset Catalog color definition for action element hover state. Defines light theme color (#F2F2F2 - light gray) and dark theme color (#2E2E2F - dark gray). Part of design system providing consistent interaction feedback colors across UI components. Used for button hover states and interactive element highlighting. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Action/selected.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode Asset Catalog color definition for action element selected state. Provides adaptive color for selected interactive elements with light and dark theme variants. Part of comprehensive design system ensuring consistent selection feedback across buttons, navigation elements, and interactive components. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Background/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset namespace container for background colors. Organizational structure for surface colors including primary, secondary, accent, and popup backgrounds with dark/light mode support. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Background/accent.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode Asset Catalog color definition for accent background elements. Provides adaptive background color for highlighted content areas, feature callouts, and accent containers. Supports light and dark theme variants for consistent visual hierarchy and brand identity across the wallet interface. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Background/popup.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode Asset Catalog color definition for popup and modal background surfaces. Defines light theme color (#F8F8F9 - off-white) and dark theme color (#1D1D1D - dark gray). Used for modal dialogs, overlays, and popup containers ensuring proper contrast and readability in both themes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Background/primary.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS color asset with adaptive theming for primary background. Defines white (RGB: 255,255,255) for light mode and very dark gray (RGB: 22,22,23) for dark mode. Supports automatic appearance switching for main app backgrounds. Used as the primary background color throughout the app's main interface, screens, and container views. Core component of the background color system ensuring proper theme consistency. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Background/secondary.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode Asset Catalog color definition for secondary background surfaces. Provides adaptive color for content areas, cards, and secondary surfaces with proper contrast against primary background. Essential for information hierarchy and visual separation in the wallet interface. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Button/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog namespace definition for button-related colors. Provides organizational structure for button color assets including primary, secondary, outlined, and text button variations. Contains no direct color data but enables hierarchical organization of button color assets within the design system. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Button/outlined.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for outlined button borders/text. Contains adaptive color pair: black (#000000) in light mode and white (#FFFFFF) in dark mode. Part of the Button semantic color group supporting design system consistency for outlined button styling with automatic dark/light theme adaptation. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Button/primary-bg.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS color asset with adaptive theming for primary button background. Defines black (RGB: 0,0,0) for light mode and very light gray (RGB: 249,249,249) for dark mode. Supports automatic appearance switching for primary button styling. Used by main action buttons throughout the app to provide clear visual hierarchy and user interaction feedback. Core component of the button color system. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Button/primary-text.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for primary button text color. Contains adaptive color pair: white (#FFFFFF) in light mode and black (#000000) in dark mode. Ensures optimal text contrast on primary colored buttons across light and dark appearance modes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Common/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog namespace definition for common color assets. Provides organizational structure for shared color assets including blackmain, whitemain, and other common color variations used throughout the design system. Contains no direct color data but enables hierarchical organization of common color assets. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Common/blackmain.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for the main black color (#111111) with consistent appearance across light/dark modes. Same dark gray color in both appearances for consistent black elements throughout the design system. Used for borders, icons, and elements requiring consistent dark appearance. RGB values: Red=0x11, Green=0x11, Blue=0x11, Alpha=1.0. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Common/whitemain.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for main white color used throughout the app. Contains static white color (#FFFFFF) for both light and dark modes. Part of the Common color group for consistent white color reference across UI components and themes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Components/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog namespace definition for UI component-specific colors. Provides logical grouping and organization for colors used in specific UI components like chips, navigation bars, and ratings. Contains no color data itself but enables hierarchical color organization in the asset catalog. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Components/chip-default-background.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for default chip component background. Contains adaptive color pair: black (#000000) in light mode and near-white (#F9F9F9) in dark mode. Supports chip UI elements with appropriate background contrast for both appearance modes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Components/chip-default-text.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for default chip component text color. Contains adaptive color that adjusts between appearance modes to ensure proper text contrast on chip backgrounds. Part of Components color group for UI element-specific styling. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Components/navbar-background.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for navigation bar background color. Contains adaptive color values that provide appropriate navigation bar styling for both light and dark appearance modes. Ensures visual hierarchy and proper contrast in navigation areas. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Components/navbar-icons.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for navigation bar icon tinting. Contains adaptive color values that ensure navigation bar icons maintain proper visibility and contrast across light and dark themes. Applied to back buttons, close buttons, and other navigation controls. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Components/rating-active-fill.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for active rating component fill color. Used for filled stars or rating indicators in active/selected state. Contains adaptive color values to maintain visual prominence across appearance modes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Elevation/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog namespace definition for elevation-related colors. Provides logical grouping for colors related to visual depth, shadows, and layered UI elements. Contains no color data itself but organizes elevation styling colors within the asset catalog hierarchy. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Elevation/outlined.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for outlined elements with elevation. Contains adaptive color values used for borders and outlines on elevated UI components like cards, modals, and overlays. Adjusts between appearance modes to maintain visual hierarchy. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Error/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset namespace container for error state colors. Organizational structure for error and warning colors including main, light, dark, and contrast variations for error messages and validation feedback. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Error/contrast.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for high-contrast error color. Used for error text and icons that require maximum visibility and accessibility compliance. Part of Error semantic color group ensuring clear error communication across appearance modes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Error/dark.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for dark variant of error color. Used for error backgrounds, borders, and secondary error indicators. Provides softer error styling while maintaining semantic meaning across light and dark appearance modes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Error/light.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for light variant of error color. Used for subtle error backgrounds, tinted areas, and non-critical error indicators. Provides gentle error styling while maintaining accessibility and semantic clarity. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Error/main.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for error state color with dark mode support. Red error color (#FF3332) used for error messages, validation failures, transaction errors, and critical alerts in Aurora wallet interface. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Icons/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog namespace definition for icon-specific colors. Provides logical grouping for colors used specifically for icon tinting and styling throughout the app. Contains no color data itself but organizes icon color assets within the catalog hierarchy. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Icons/default.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for default icon tint color. Contains adaptive color values used as the standard icon tinting color across the app. Ensures consistent icon visibility and theming across light and dark appearance modes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Info/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog namespace definition for informational colors. Provides logical grouping for colors used in informational UI elements like tips, help text, and informational alerts. Organizes info-related color variants within the asset catalog hierarchy. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Info/contrast.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for high-contrast informational color. Used for informational text and icons requiring maximum visibility and accessibility compliance. Ensures clear information communication across light and dark appearance modes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Info/dark.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [DESIGN SYSTEM] Info color asset for dark mode appearance. Defines information/status color with light mode variant (#195CDC - blue) and dark mode variant (#91CEFF - light blue). Part of semantic color system for informational UI elements like alerts, status messages, and info badges. Used throughout the app for consistent information status indication. Supports iOS appearance system for automatic light/dark mode switching. sRGB color space with full alpha opacity. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Info/light.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [DESIGN SYSTEM] Info color asset for light mode appearance. Defines information/status color optimized for light backgrounds. Companion to dark info color, providing semantic color for informational UI elements in light theme. Part of design system's adaptive color framework supporting automatic appearance changes. Used for info alerts, status indicators, and informational badges throughout the app interface. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Info/main.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for main informational color. Primary color for informational elements like help tips, notices, and informational callouts. Contains adaptive color values that maintain readability across appearance modes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Primary/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset namespace container for primary brand colors. Organizational structure for Tari brand color variants including main, dark, light, contrast, focus, hover, and outline variations with dark mode support. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Primary/contrast.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for primary color contrast (#FFFFFF). Pure white color consistent across light/dark modes for high contrast text and elements on primary colored backgrounds. Used for text on primary buttons, labels on brand colors, and maximum contrast elements. RGB values: Red=0xFF, Green=0xFF, Blue=0xFF, Alpha=1.0. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Primary/dark.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for dark variant of primary brand color. Used for primary color shadows, pressed states, and darker primary accents. Provides visual depth while maintaining brand consistency across appearance modes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Primary/focus.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [DESIGN SYSTEM] Primary focus color asset with light/dark mode variants. Light mode (#F2F6E0 - pale green) and dark mode (#2F3118 - dark green) for focused interface elements. Used for button focus states, active form fields, and selected interface components. Provides accessibility-compliant focus indication and visual feedback for user interactions. Part of primary color system ensuring consistent focus visualization across app. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Primary/focusVisible.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [DESIGN SYSTEM] Primary focus visible color asset for enhanced accessibility focus indication. Provides high-contrast focus visibility for keyboard navigation and accessibility tools. Part of primary color system with light/dark mode support. Used for visible focus states on interactive elements to meet WCAG accessibility guidelines. Ensures proper focus indication for users relying on keyboard navigation or assistive technologies. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Primary/hover.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [DESIGN SYSTEM] Primary hover color asset for interactive element hover states. Provides visual feedback when users hover over buttons, links, and interactive components. Part of primary color system with adaptive light/dark mode variants. Used throughout the app for consistent hover state indication on touchable elements. Enhances user experience by providing clear interaction feedback. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Primary/light.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode color asset configuration defining the primary light theme color for the Tari wallet. Specifies hex color #DFFB20 for light mode and #95B500 for dark mode with sRGB color space and full alpha opacity. Part of the primary color palette used throughout the UI for brand consistency. Supports automatic dark mode switching through iOS appearance system. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Primary/main.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for primary brand color. Contains Tari's signature purple color (#CAEB01) used consistently across both appearance modes. Core brand color for buttons, highlights, and primary interactive elements throughout the wallet app. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Primary/outlinedBorder.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode color asset configuration for primary outlined border color in the Tari wallet design system. Defines theme-aware border colors for buttons, input fields, and UI components with outlined styling. Supports light and dark mode variations for consistent border appearance across iOS interface themes. Used primarily for primary action elements requiring outlined visual treatment. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Primary/selected.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode color asset configuration for primary selected state color in the Tari wallet UI. Defines the color used when primary interface elements are in a selected or active state, such as selected buttons, active navigation items, or highlighted primary actions. Includes automatic dark mode color variants for consistent selected state appearance across iOS appearance modes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Secondary/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode color namespace configuration providing a container for secondary color variants in the Tari wallet design system. Acts as an organizational folder for secondary theme colors including contrast, dark, light, and main variants. Enables structured color management and consistent secondary color usage throughout the app interface. Does not contain colors directly but serves as a namespace provider for child color assets. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Secondary/contrast.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode color asset configuration for secondary contrast color in the Tari wallet design system. Defines high-contrast color variants for secondary UI elements requiring accessibility compliance and improved readability. Includes automatic light and dark mode support with appropriate contrast ratios for secondary interface components like secondary buttons, labels, and supporting text elements. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Secondary/dark.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode color asset configuration for secondary dark variant color in the Tari wallet design system. Provides darker shade of secondary colors for UI elements requiring subtle visual hierarchy or depth indication. Used for secondary buttons in pressed states, background overlays, or secondary interface elements needing darker appearance. Supports automatic iOS dark mode adaptation. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Secondary/light.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode color asset configuration for secondary light variant color in the Tari wallet design system. Provides lighter shade of secondary colors for subtle background treatments, disabled states, or reduced emphasis UI elements. Used for secondary interface components requiring muted visual presence while maintaining secondary brand color consistency. Includes automatic dark mode color switching. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Secondary/main.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode color asset configuration for the main secondary brand color in the Tari wallet design system. Defines the primary secondary color used for supporting UI elements, secondary buttons, accent details, and complementary interface components. Provides automatic light and dark mode color variants ensuring consistent secondary brand color application across iOS appearance themes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Success/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset namespace container for success state colors. Organizational structure for positive state colors including main, light, dark, and contrast variations for successful transactions and positive feedback in Aurora wallet. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Success/contrast.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode color asset configuration for high-contrast success state color in the Tari wallet design system. Defines accessibility-compliant contrast colors for success indicators, completed transaction states, positive feedback messages, and confirmation UI elements. Ensures WCAG compliance for success state communication while maintaining visual consistency across light and dark iOS interface modes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Success/dark.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [DESIGN SYSTEM] Success color asset for dark mode appearance. Green-based color palette for successful operations, completed transactions, and positive status indicators. Part of semantic color system with light/dark mode variants. Used for transaction success states, completion badges, and positive feedback throughout the app interface. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Success/light.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode color asset configuration for light variant success color in the Tari wallet design system. Provides subtle success state indication for background treatments, success message backgrounds, or reduced emphasis positive feedback elements. Used in success banners, positive balance indicators, and completed transaction confirmations requiring gentle visual treatment. Supports automatic dark mode adaptation. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Success/main.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for success state color. Contains adaptive green colors: darker green (#00C047) in light mode and brighter green (#09FE65) in dark mode. Used for successful transactions, completed states, and positive feedback throughout the app. |

###### MobileWallet/Assets.xcassets/Colors/Colors/System/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset namespace container for system semantic colors. Organizational structure for system colors including green, red, yellow variants and secondary alternatives for system-level feedback and status indication. |

###### MobileWallet/Assets.xcassets/Colors/Colors/System/green.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for system green color with dark mode support. Bright green (#09FE65) used for positive states, successful transactions, confirmation messages, and positive indicators throughout Aurora wallet interface. |

###### MobileWallet/Assets.xcassets/Colors/Colors/System/red.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for system red color supporting dark/light modes. Used for destructive actions, error states, failed transactions, and warning indicators throughout Aurora wallet interface. Maintains consistent error communication. |

###### MobileWallet/Assets.xcassets/Colors/Colors/System/secondaryGreen.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for secondary green system color. Lighter or alternative green tone used for secondary positive states, success background colors, and subtle positive indicators in Aurora wallet UI hierarchy. |

###### MobileWallet/Assets.xcassets/Colors/Colors/System/secondaryRed.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode colorset for secondary red system color supporting dark/light modes. Lighter red tone used for secondary error states, warning backgrounds, and subtle negative indicators in Aurora wallet UI hierarchy. |

###### MobileWallet/Assets.xcassets/Colors/Colors/System/secondaryYellow.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode color asset configuration for secondary yellow system color in the Tari wallet design system. Defines yellow color variants for warning indicators, attention-getting elements, and secondary accent highlights that require yellow brand association. Provides automatic light and dark mode color variants for consistent secondary yellow usage across iOS interface themes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/System/yellow.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode color asset configuration for main yellow system color in the Tari wallet design system. Primary yellow color used for warning states, caution indicators, pending transaction status, and attention-grabbing interface elements. Supports automatic dark mode adaptation with appropriate yellow tones for both light and dark interface themes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Text/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog namespace definition for text colors. Provides logical grouping for all text-related colors including primary, secondary, body, and disabled text variations. Organizes text styling colors within the asset catalog hierarchy for design system consistency. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Text/body.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [DESIGN SYSTEM] Body text color asset with adaptive light/dark mode support. Light mode (#7F8599 - medium gray) and dark mode (#C3C7D7 - light gray) for body text content. Primary text color for content, descriptions, and secondary information throughout the app. Provides optimal readability contrast ratios for body text across both appearance modes. Core typography color ensuring consistent text hierarchy and accessibility compliance. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Text/disabled.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [DESIGN SYSTEM] Disabled text color asset for inactive UI elements. Provides reduced opacity/contrast text color for disabled buttons, form fields, and inactive interface components. Light/dark mode adaptive color ensuring accessibility compliance for disabled states. Part of semantic color system maintaining consistent visual hierarchy for interactive element states. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Text/primary.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for primary text color. Contains adaptive color values for the main text color used in headers, titles, and primary content. Ensures optimal readability and contrast across light and dark appearance modes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Text/secondary.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for secondary text color. Contains adaptive color values for supporting text like descriptions, captions, and less prominent content. Provides appropriate visual hierarchy while maintaining readability. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Token/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode color namespace configuration for token-specific colors in the Tari wallet design system. Provides organizational structure for cryptocurrency token-related color assets including dividers and token-specific UI elements. Serves as a namespace provider for child color assets used in token display, transaction interfaces, and balance representations. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Token/divider.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode color asset configuration for token divider lines in the Tari wallet interface. Defines subtle separator colors used between different token entries, balance sections, and transaction list items. Provides theme-aware colors for visual separation in token-related interfaces while maintaining clean, minimal appearance across light and dark modes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Warning/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog namespace definition for warning colors. Provides logical grouping for all warning-related color variants including main, light, dark, and contrast colors. Organizes warning state colors for design system consistency. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Warning/Color.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode color asset configuration for main warning color in the Tari wallet design system. Primary warning color used for error states, failed transaction indicators, security alerts, and critical user attention elements. Provides automatic light and dark mode color adaptation with appropriate warning color intensity for different interface themes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Warning/contrast.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog color set definition for warning contrast color. Contains SRGB color definitions for both light and dark appearance modes. Light mode: #FEFFFF (near white), Dark mode: #FEFFFF (same near white). Part of the app's warning color palette system. Auto-generated by Xcode asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Warning/dark.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [DESIGN SYSTEM] Warning color asset for dark mode appearance. Orange/amber-based color for warning messages, caution states, and attention-grabbing UI elements. Part of semantic color system ensuring consistent warning indication across light/dark themes. Used for form validation warnings, transaction alerts, and cautionary interface feedback. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Warning/light.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog color set definition for warning light accent color. Contains SRGB color definitions for both appearance modes. Light mode: #F6D4B3 (light orange/yellow), Dark mode: #E98F4F (darker orange). Used for lighter warning UI elements and backgrounds. Part of the app's warning color system. |

###### MobileWallet/Assets.xcassets/Colors/Colors/Warning/main.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for main warning color. Contains adaptive color values for warning alerts, caution indicators, and important notices. Provides appropriate visual prominence for warning states across appearance modes. |

###### MobileWallet/Assets.xcassets/Colors/Colors/divider.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CONFIG] Color asset definition for UI divider elements. Defines semi-transparent divider colors: light mode (black 8% alpha) and dark mode (white 12% alpha). Used for separating content sections, table view dividers, and UI boundaries. Part of the design system's structural color palette. Dependencies: None. Used by: Table views, card components, section separators, layout elements. |

###### MobileWallet/Assets.xcassets/Colors/Static/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CONFIG] Asset catalog namespace configuration for static colors. Defines the Static color namespace with provides-namespace property. Contains colors that don't adapt to dark/light mode. Dependencies: None. Used by: Static color assets (MediumGrey, PopupOverlay), asset catalog organization. |

###### MobileWallet/Assets.xcassets/Colors/Static/Black.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog color definition for static black color (#000000). Pure black color that remains constant regardless of light/dark mode settings. Used for high-contrast elements, borders, text that must remain black, and design elements requiring consistent black appearance. RGB values: Red=0.0, Green=0.0, Blue=0.0, Alpha=1.0. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Colors/Static/MediumGrey.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog color set definition for static medium grey color. Single SRGB color definition: #7F8599 (medium grey-blue). Does not vary with appearance mode (static color). Used for secondary text, borders, or neutral UI elements throughout the app. |

###### MobileWallet/Assets.xcassets/Colors/Static/PopupOverlay.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog color set definition for popup overlay background. Single SRGB color: black (#000000) with 70% alpha transparency. Static color used for modal popup overlays to dim the background content behind dialogs and modal presentations. |

###### MobileWallet/Assets.xcassets/Colors/Static/Purple.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog color definition for static purple color. Contains fixed purple color (RGB: 147, 48, 255) that does not adapt to appearance modes. Used for specific brand elements or UI components that require consistent purple styling regardless of theme. |

###### MobileWallet/Assets.xcassets/Colors/Static/Red.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [DESIGN SYSTEM] Static red color asset for error states and critical actions. Fixed red color independent of appearance mode for consistent error indication, delete buttons, and critical warning states. Used for error messages, destructive actions, and high-attention alerts requiring unmistakable visual identification. |

###### MobileWallet/Assets.xcassets/Colors/Static/White.colorset/

| File | Description |
|------|-------------|
| `Contents.json` | [DESIGN SYSTEM] Static white color asset for consistent white elements. Pure white color independent of appearance mode for elements requiring absolute white (overlays, certain backgrounds, icons on dark surfaces). Used for modal overlays, popup backgrounds, and interface elements needing consistent white regardless of theme. |

##### MobileWallet/Assets.xcassets/Icons/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog namespace definition for main Icons folder. Contains 'provides-namespace' property organizing all app icon assets into subcategories like Contact Types, Fees, General, Network, Settings, TabBar, UTXO, and Yat. Root container for the app's icon asset organization system. |

###### MobileWallet/Assets.xcassets/Icons/Connection Details/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog namespace definition for connection status icons. Provides organizational structure for network connectivity icons including Internet, Sync, and Tor connection indicators used in network status displays and settings screens. Contains no direct image data but enables hierarchical organization of connection-related icons. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Icons/Connection Details/Internet.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog image definition for the internet connection status icon (icon-internet.pdf). Template-rendered vector icon used to indicate general internet connectivity status in network diagnostics and connection status screens. Supports all device scales through vector PDF format. Used by: TorManager, ConnectionHandler, network status UI components. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Icons/Connection Details/Sync.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog image definition for the blockchain synchronization status icon (icon-sync.pdf). Template-rendered vector icon used to indicate wallet sync status, blockchain synchronization progress, and data refresh operations in network status displays. Supports all device scales through vector PDF format. Used by: TariConnectionService, sync status UI, blockchain sync indicators. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Icons/Connection Details/Tor.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog image definition for the Tor network connection status icon (icon-tor.pdf). Template-rendered vector icon used to indicate Tor anonymity network connectivity status in privacy settings and network diagnostics. Critical privacy indicator for wallet's anonymity features. Supports all device scales through vector PDF format. Used by: TorManager, privacy status UI, connection status screens. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Icons/Contact Types/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog namespace definition for Contact Types icons folder. Contains 'provides-namespace' property indicating this folder organizes icon assets for different contact types. Acts as a container for contact-related icon assets like Linked contacts. |

###### MobileWallet/Assets.xcassets/Icons/Contact Types/External.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog image definition for external contact type icon (ico-cb-nopadding.pdf). Template-rendered vector icon with preserved vector representation used to indicate external contacts or non-wallet contacts in the contact book. Used by: contact management UI, contact type indicators. Supports all device scales through vector PDF format. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Icons/Contact Types/Internal.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog image definition for internal contact type icon. References 'ico-tari-nopadding.pdf' vector asset rendered as template for dynamic tinting. Used to identify internal Tari wallet contacts in the contact book, preserving vector quality with template rendering. |

###### MobileWallet/Assets.xcassets/Icons/Contact Types/Linked.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog imageset definition for linked contact icon. References ico-link-nopadding.pdf vector image. Configured as template rendering for tinting, preserves vector representation for scalability. Used to indicate contacts linked via deep links or QR codes in contact management UI. |

###### MobileWallet/Assets.xcassets/Icons/Fees/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog namespace definition for Fees icons folder. Contains 'provides-namespace' property organizing fee-related icon assets like Speedometer icons for transaction fee level indicators. Part of the app's transaction fee UI system. |

###### MobileWallet/Assets.xcassets/Icons/Fees/Speedometer/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog namespace definition for Speedometer fee icons folder. Contains 'provides-namespace' property organizing speedometer-style icons used to visually represent different transaction fee levels (low, mid, high). Part of the fee selection UI system. |

###### MobileWallet/Assets.xcassets/Icons/Fees/Speedometer/high.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode image asset configuration for the high-speed transaction fee indicator icon. References ico-speedometer-3.pdf vector file with vector preservation and template rendering. Used in transaction fee selection interfaces to indicate high-priority, fast transaction processing with higher fees. Preserves vector representation for sharp display across all screen sizes and densities. |

###### MobileWallet/Assets.xcassets/Icons/Fees/Speedometer/low.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog imageset definition for low fee speedometer icon. Vector PDF icon configured as template for tinting, preserves vector representation for scalability. Used in transaction fee selection UI to indicate low/slow fee option with visual speedometer metaphor. |

###### MobileWallet/Assets.xcassets/Icons/Fees/Speedometer/mid.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog imageset definition for medium fee speedometer icon. Vector PDF icon configured as template for tinting, preserves vector representation for scalability. Used in transaction fee selection UI to indicate medium/standard fee option with visual speedometer metaphor. |

###### MobileWallet/Assets.xcassets/Icons/General/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog namespace definition for general-purpose icons. Provides logical grouping for commonly used icons throughout the app including checkmarks, arrows, info, QR codes, and other utility icons. Organizes general UI icons within the asset catalog hierarchy. |

###### MobileWallet/Assets.xcassets/Icons/General/Analytics.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog image definition for the analytics/statistics icon (Analytics.pdf). Template-rendered vector icon with preserved vector representation used in debugging screens, performance monitoring, and data analysis interfaces. Supports all device scales through vector PDF format. Used by: Debug screens, performance monitoring UI, analytics components. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Icons/General/Bluetooth.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog image definition for Bluetooth connectivity icon (ico-ble.pdf). Template-rendered vector icon with preserved vector representation used for Bluetooth Low Energy (BLE) connectivity features, device pairing interfaces, and wireless connection status. Used by: connectivity settings, device management UI. Supports all device scales through vector PDF format. Auto-generated by Xcode's asset catalog system. |

###### MobileWallet/Assets.xcassets/Icons/General/CellArrow.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog imageset definition for cell arrow disclosure indicator. References CellArrow.pdf vector image. Configured as template rendering for tinting, preserves vector representation. Used in table view cells to indicate navigable items and show disclosure chevron arrows. |

###### MobileWallet/Assets.xcassets/Icons/General/Checkmark.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog imageset definition for checkmark/tick icon. Vector PDF icon configured as template for tinting, preserves vector representation for scalability. Used throughout the app to indicate completion, success states, selection confirmations, and validation indicators. |

###### MobileWallet/Assets.xcassets/Icons/General/Close.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode image asset configuration for the close button icon in the Tari wallet interface. References Close.pdf vector file with template rendering for consistent theming across light and dark modes. Used in modal dialogs, pop-ups, overlays, and navigation interfaces requiring close/dismiss functionality. Preserves vector representation for sharp display at all sizes. |

###### MobileWallet/Assets.xcassets/Icons/General/Faucet.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog imageset definition for faucet/testnet funding icon. Vector PDF icon configured as template for tinting, preserves vector representation. Used to indicate testnet faucet functionality for obtaining test Tari tokens during development and testing. |

###### MobileWallet/Assets.xcassets/Icons/General/Info.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode asset catalog imageset definition for information/help icon. Vector PDF icon configured as template for tinting, preserves vector representation. Used throughout the app to indicate informational content, help sections, and detail disclosure buttons. |

###### MobileWallet/Assets.xcassets/Icons/General/Link.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog metadata for link icon (ico-link.pdf). Universal template image with vector preservation. Used for link-related UI elements, external URL indicators, and sharing functionality throughout the wallet interface. Part of the general icons asset group. |

###### MobileWallet/Assets.xcassets/Icons/General/Magnifying Glass.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog metadata for magnifying glass search icon. Universal template image with vector preservation. Used for search functionality, QR code scanning UI, and contact lookup features throughout the wallet. Part of the general icons asset group. |

###### MobileWallet/Assets.xcassets/Icons/General/QR.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog image definition for QR code icon. References 'ico-qr.pdf' vector asset rendered as template for dynamic tinting. Used throughout the app for QR code scanning features, address sharing, and payment receiving interfaces. Preserves vector quality with template rendering. |

###### MobileWallet/Assets.xcassets/Icons/General/RoundedCancelButton.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog metadata for rounded cancel button icon. Universal template image with vector preservation. Used for modal dismissal, operation cancellation, and close button functionality throughout the wallet interface. Part of the general icons asset group. |

###### MobileWallet/Assets.xcassets/Icons/General/RoundedQuestionMark.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CATALOG] Xcode asset catalog metadata for rounded question mark icon. Vector-based PDF asset configured as template image for dynamic tinting. Used across the app for help/info UI elements. Properties: template-rendering-intent: template, preserves-vector-representation: true. Dependencies: QuestionMark.pdf file. Used by: Help dialogs, information buttons throughout the UI. |

###### MobileWallet/Assets.xcassets/Icons/General/Shield Checkmark.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog metadata for shield with checkmark security icon. Universal template image with vector preservation. Used for security confirmations, encryption status, verification indicators, and secure operation success states throughout the wallet interface. Part of the general icons asset group. |

###### MobileWallet/Assets.xcassets/Icons/General/Star.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog metadata for filled star icon (ico-star-filled.pdf). Universal template image with vector preservation. Used for favorites functionality, contact marking, and rating/importance indicators throughout the wallet interface. Part of the general icons asset group. |

###### MobileWallet/Assets.xcassets/Icons/General/Tari Gem.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode image asset configuration for the Tari Gem brand icon used throughout the wallet interface. References vector PDF file with template rendering intent for consistent brand representation across themes. Core brand element used in wallet balance displays, loading screens, transaction confirmations, and brand identity elements. Preserves vector format for crisp rendering at all display densities. |

###### MobileWallet/Assets.xcassets/Icons/General/Tick.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog metadata for checkmark/tick icon. Universal template image with vector preservation. Used for confirmation states, completed transactions, successful operations, and validation indicators throughout the wallet interface. Part of the general icons asset group. |

###### MobileWallet/Assets.xcassets/Icons/General/Wallet.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog metadata for wallet icon. Universal template image with vector preservation. Used for wallet-related UI elements, balance displays, and primary navigation indicators throughout the wallet interface. Part of the general icons asset group. |

###### MobileWallet/Assets.xcassets/Icons/Network/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog namespace definition for network status icons. Provides logical grouping for network connectivity indicators including full, limited, and offline states. Organizes network status UI icons for connection monitoring throughout the app. |

###### MobileWallet/Assets.xcassets/Icons/Network/Full.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog metadata for full network signal icon (network-full.pdf). Universal template image with vector preservation. Used for displaying strong network connectivity status, Tor connection quality, and base node communication indicators. Part of the network status icons asset group. |

###### MobileWallet/Assets.xcassets/Icons/Network/Limited.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog metadata for limited network signal icon. Universal template image with vector preservation. Used for displaying weak network connectivity status, limited Tor connection, and degraded base node communication indicators. Part of the network status icons asset group. |

###### MobileWallet/Assets.xcassets/Icons/Network/Off.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog metadata for network offline icon. Universal template image with vector preservation. Used for displaying disconnected network status, failed Tor connections, and offline base node communication indicators. Part of the network status icons asset group. |

###### MobileWallet/Assets.xcassets/Icons/Seed Words List/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CATALOG] Xcode asset catalog folder metadata for seed words list icons. Container for icons used in the wallet recovery/backup seed phrase interface. Used by: Seed phrase display screens, wallet backup functionality. |

###### MobileWallet/Assets.xcassets/Icons/Seed Words List/ExpandButtonArrow.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CATALOG] Xcode asset catalog metadata for expand button arrow icon used in seed phrase interface. Vector-based PDF asset for expand/collapse controls in seed word list views. Used by: Seed phrase backup screens, expandable sections in wallet recovery. |

###### MobileWallet/Assets.xcassets/Icons/Settings/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog namespace definition for settings screen icons. Provides logical grouping for all settings-related icons including about, network, backup, security, and configuration options. Organizes settings UI icons within the asset catalog hierarchy. |

###### MobileWallet/Assets.xcassets/Icons/Settings/About.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | iOS Asset Catalog image definition for about settings icon. References 'ico-about.pdf' vector asset rendered as template for dynamic tinting. Used in settings menu to identify the about/information section. Supports template rendering for theme consistency. |

###### MobileWallet/Assets.xcassets/Icons/Settings/BaseNode.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode image asset configuration for the base node settings icon. References ico-base-node.pdf vector file with template rendering for consistent theming. Used in advanced settings screens for base node configuration, network settings, and blockchain connectivity options. Template rendering allows icon to adapt to current theme colors automatically. |

###### MobileWallet/Assets.xcassets/Icons/Settings/BlockExplorer.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CATALOG] Xcode asset catalog metadata for block explorer icon in settings. Vector-based PDF asset for blockchain explorer functionality access. Used by: Settings screen block explorer option. |

###### MobileWallet/Assets.xcassets/Icons/Settings/Bluetooth.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog metadata for Bluetooth icon used in settings. Universal template image with vector preservation. Used for BLE (Bluetooth Low Energy) contact sharing settings, device discovery UI, and wireless contact exchange functionality. Part of the settings icons asset group. |

###### MobileWallet/Assets.xcassets/Icons/Settings/BridgeConfig.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CATALOG] Xcode asset catalog metadata for bridge configuration icon in settings. Vector-based PDF asset for bridge/network configuration interface. Used by: Settings screen bridge configuration option. |

###### MobileWallet/Assets.xcassets/Icons/Settings/Camera.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CATALOG] Xcode asset catalog metadata for camera icon in settings. Vector-based PDF asset for camera/QR scanning functionality. Used by: Settings screen camera access, QR scanning features. |

###### MobileWallet/Assets.xcassets/Icons/Settings/Contribute.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CATALOG] Settings contribute icon asset catalog metadata. Vector PDF for contributing to Tari project functionality. Used by: Settings contribute option. |

###### MobileWallet/Assets.xcassets/Icons/Settings/Delete.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CATALOG] Xcode asset catalog metadata for delete icon in settings. Vector-based PDF asset for destructive delete actions. Used by: Settings screen wallet deletion, data clearing options. |

###### MobileWallet/Assets.xcassets/Icons/Settings/Disclaimer.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode image set configuration for disclaimer settings icon. Template rendering intent for theme-aware icon display. Contains ico-disclaimer.pdf vector asset. Used by: settings screen disclaimer section. |

###### MobileWallet/Assets.xcassets/Icons/Settings/Network.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CATALOG] Settings network configuration icon asset catalog metadata. Vector PDF for network switching interface. Used by: Settings network selection. |

###### MobileWallet/Assets.xcassets/Icons/Settings/PrivacyPolicy.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode image set configuration for privacy policy settings icon. Template rendering for theme adaptation. Used by: settings screen privacy policy section. |

###### MobileWallet/Assets.xcassets/Icons/Settings/ReportBug.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode image set configuration for bug report settings icon. Template rendering for theme adaptation. Used by: settings screen bug report section. |

###### MobileWallet/Assets.xcassets/Icons/Settings/Theme.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CATALOG] Xcode asset catalog metadata for theme selection icon in settings. Vector-based PDF asset for theme switching functionality. Used by: Settings screen theme selection (light/dark/purple themes). |

###### MobileWallet/Assets.xcassets/Icons/Settings/UserAgreement.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode image set configuration for user agreement settings icon. Template rendering for theme adaptation. Used by: settings screen user agreement section. |

###### MobileWallet/Assets.xcassets/Icons/Settings/VisitTari.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode image set configuration for visit Tari website settings icon. Template rendering for theme adaptation. Used by: settings screen website link section. |

###### MobileWallet/Assets.xcassets/Icons/Settings/WalletBackups.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CATALOG] Settings wallet backup icon asset catalog metadata. Vector PDF for backup management functionality. Used by: Settings backup/restore options. |

###### MobileWallet/Assets.xcassets/Icons/TabBar/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CATALOG] Tab bar icons folder metadata. Container for main navigation tab bar icon assets. Used by: Main app navigation tabs. |

###### MobileWallet/Assets.xcassets/Icons/TabBar/ContactBook.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode image asset configuration for the contact book tab bar icon. Vector PDF asset with template rendering for consistent tab bar appearance across iOS themes. Used in main navigation tab bar for accessing the wallet's contact book, address book, and saved recipient management features. Template rendering ensures proper theming for selected and unselected states. |

###### MobileWallet/Assets.xcassets/Icons/UTXO/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CATALOG] Xcode asset catalog folder metadata for UTXO management icons. Container for icons used in unspent transaction output management interface. Used by: UTXO screens, coin control functionality. |

###### MobileWallet/Assets.xcassets/Icons/UTXO/Hourglass.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode image asset configuration for the hourglass icon used in UTXO management interfaces. References ico-hourglass.pdf vector file with template rendering. Indicates time-locked transactions, pending UTXOs, or time-based operations in the wallet's UTXO management system. Template rendering ensures consistent icon theming across interface modes. |

###### MobileWallet/Assets.xcassets/Icons/UTXO/Join.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog metadata for UTXO join operation icon. Universal template image with vector preservation. Used for coin joining operations, UTXO consolidation UI, and transaction input merging indicators in the wallet's advanced transaction management features. Part of the UTXO management icons asset group. |

###### MobileWallet/Assets.xcassets/Icons/UTXO/JoinSplit.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CATALOG] Xcode asset catalog metadata for UTXO join/split operation icon. Vector-based PDF asset for UTXO combining and splitting actions. Used by: UTXO management screens, coin control operations. |

###### MobileWallet/Assets.xcassets/Icons/UTXO/Split.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog metadata for UTXO split operation icon. Universal template image with vector preservation. Used for coin splitting operations, UTXO division UI, and transaction output creation indicators in the wallet's advanced transaction management features. Part of the UTXO management icons asset group. |

###### MobileWallet/Assets.xcassets/Icons/UTXO/TextListView.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode image set for UTXO text list view toggle icon. Template rendering for theme adaptation. Used by: UTXO management screen view switching controls. |

###### MobileWallet/Assets.xcassets/Icons/UTXO/TileListView.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode image set for UTXO tile list view toggle icon. Template rendering for theme adaptation. Used by: UTXO management screen view switching controls. |

###### MobileWallet/Assets.xcassets/Icons/UTXO/ValuePickerMinus.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode image set for UTXO value picker minus button icon. Template rendering for theme adaptation. Used by: UTXO value selection and amount adjustment controls. |

###### MobileWallet/Assets.xcassets/Icons/UTXO/ValuePickerPlus.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode image set for UTXO value picker plus button icon. Template rendering for theme adaptation. Used by: UTXO value selection and amount adjustment controls. |

###### MobileWallet/Assets.xcassets/Icons/Yat/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CATALOG] Yat integration icons folder metadata. Container for Yat emoji address system icon assets. Used by: Yat emoji address features, profile integration. |

###### MobileWallet/Assets.xcassets/Icons/Yat/ButtonOff.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode image set for Yat integration toggle button off state. Template rendering for theme adaptation. Used by: Yat profile settings and integration toggles. |

###### MobileWallet/Assets.xcassets/Icons/Yat/ButtonOn.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode image set for Yat integration toggle button on state. Template rendering for theme adaptation. Used by: Yat profile settings and integration toggles. |

###### MobileWallet/Assets.xcassets/Icons/Yat/Logo.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode image asset configuration for the Yat service logo used in Yat integration features. References Yat.pdf vector file with template rendering for consistent branding. Used in Yat-related interface elements, human-readable address features, and Yat ecosystem integration components within the wallet. Template rendering allows logo to adapt to current interface theme. |

##### MobileWallet/Assets.xcassets/Images/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Top-level asset catalog group for app images. Organizes all non-icon image assets by category. |

###### MobileWallet/Assets.xcassets/Images/Contact Book/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CATALOG] Xcode asset catalog folder metadata for contact book images. Container for images used in contact management interface including buttons and placeholders. Used by: Contact screens, address book functionality. |

###### MobileWallet/Assets.xcassets/Images/Contact Book/BLE Dialog/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode asset catalog group container for BLE dialog images. Organizes Bluetooth Low Energy pairing dialog assets including success, failure, and icon states. |

###### MobileWallet/Assets.xcassets/Images/Contact Book/BLE Dialog/Failure.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode image set for BLE contact sharing failure illustration. Used by: contact sharing failure dialogs during Bluetooth pairing errors. |

###### MobileWallet/Assets.xcassets/Images/Contact Book/BLE Dialog/Icon.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode image set for BLE contact sharing icon. Used by: contact sharing dialogs and Bluetooth pairing interface indicators. |

###### MobileWallet/Assets.xcassets/Images/Contact Book/BLE Dialog/Success.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode image set for BLE contact sharing success illustration. Used by: contact sharing success dialogs after successful Bluetooth pairing. |

###### MobileWallet/Assets.xcassets/Images/Contact Book/Buttons/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode asset catalog group container for contact book button images. Organizes contact management action button illustrations. |

###### MobileWallet/Assets.xcassets/Images/Contact Book/Buttons/AddContact.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode image set for add contact button illustration. Used by: contact book empty state and add contact action buttons. |

###### MobileWallet/Assets.xcassets/Images/Contact Book/Buttons/Share.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode image set for share contact button illustration. Used by: contact sharing and export action buttons. |

###### MobileWallet/Assets.xcassets/Images/Contact Book/Placeholders/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Xcode asset catalog group container for contact book placeholder images. Organizes empty state illustrations for different contact book sections. |

###### MobileWallet/Assets.xcassets/Images/Contact Book/Placeholders/Favourites.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Empty state placeholder for favorites contact list. Used when no favorite contacts exist. |

###### MobileWallet/Assets.xcassets/Images/Contact Book/Placeholders/LinkList.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Empty state placeholder for linked contacts list. Used when no linked contacts exist. |

###### MobileWallet/Assets.xcassets/Images/Contact Book/Placeholders/List.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | Xcode image asset configuration for the contact book list placeholder illustration. References ico-placeholder1.pdf vector file with vector preservation and template rendering. Used as empty state illustration when the contact book has no saved contacts, providing visual guidance for users to add their first contact. Maintains vector quality for crisp display. |

###### MobileWallet/Assets.xcassets/Images/Contact Book/Placeholders/TransactionList.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Empty state placeholder for contact transaction history. Used when contact has no transaction history. |

###### MobileWallet/Assets.xcassets/Images/Security/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Asset catalog group for security-related images. Organizes onboarding and security feature illustrations. |

###### MobileWallet/Assets.xcassets/Images/Security/Onboarding/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CATALOG] Xcode asset catalog folder metadata for security onboarding images. Container for onboarding flow illustrations explaining security features. Used by: Security onboarding screens, wallet setup flow. |

###### MobileWallet/Assets.xcassets/Images/Security/Onboarding/Background.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Background illustration for security onboarding screens. Used throughout 4-page onboarding flow. |

###### MobileWallet/Assets.xcassets/Images/Security/Onboarding/Page1.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Security onboarding page 1 illustration about seed phrase backup. Used by first onboarding step. |

###### MobileWallet/Assets.xcassets/Images/Security/Onboarding/Page2.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Security onboarding page 2 illustration about backup settings. Used by second onboarding step. |

###### MobileWallet/Assets.xcassets/Images/Security/Onboarding/Page3.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Security onboarding page 3 illustration about password protection. Used by third onboarding step. |

###### MobileWallet/Assets.xcassets/Images/Security/Onboarding/Page4.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Security onboarding page 4 illustration about wallet security completion. Used by final onboarding step. |

###### MobileWallet/Assets.xcassets/Images/TabBar/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Asset catalog group for tab bar related images. Organizes tab bar icons and illustrations. |

###### MobileWallet/Assets.xcassets/Images/TabBar/Send.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Tab bar send transaction illustration. Used by send transaction tab and related UI elements. |

###### MobileWallet/Assets.xcassets/Images/Themes/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET CATALOG] Xcode asset catalog folder metadata for theme preview images. Container for theme selection preview images (light, dark, purple, system). Used by: Settings theme selection screen. |

###### MobileWallet/Assets.xcassets/Images/Themes/Dark.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [ASSET] Dark theme preview image for theme selection. Used by theme settings screen. |

###### MobileWallet/Assets.xcassets/Images/Themes/Light.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog configuration for light theme preview image. References theme-light.pdf vector asset used in theme selection interface. Part of the dynamic theming system's visual previews that allow users to preview appearance before selection. Universal idiom ensures compatibility across all iOS devices and screen densities. |

###### MobileWallet/Assets.xcassets/Images/Themes/Purple.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog configuration for purple theme preview image. References theme-purple.pdf vector asset used in theme selection interface. Part of the dynamic theming system's visual previews. Universal idiom ensures proper rendering across iPhone and iPad platforms with automatic scaling for different screen densities. |

###### MobileWallet/Assets.xcassets/Images/Themes/System.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog configuration for system theme preview image. References theme-system.pdf vector asset for automatic light/dark appearance selection. Part of the theme selection UI showing system-controlled appearance option. Universal idiom provides consistent rendering across all device types and orientations. |

###### MobileWallet/Assets.xcassets/Images/UTXO/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog folder configuration for UTXO management interface images. Contains visual assets used in the unspent transaction output management screens, including placeholder graphics and success state indicators. Part of the UTXO tile and list view modes that provide visual feedback for transaction output operations. |

###### MobileWallet/Assets.xcassets/Images/UTXO/Placeholder.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog configuration for UTXO placeholder image. Used in UTXO management interface when no specific visualization is available for an unspent output. Provides consistent visual representation during loading states or when UTXO data is incomplete. Universal idiom ensures proper display across all device types. |

###### MobileWallet/Assets.xcassets/Images/UTXO/Success.imageset/

| File | Description |
|------|-------------|
| `Contents.json` | [GENERATED] Xcode asset catalog configuration for UTXO success state image. Used in UTXO management interface to indicate successful operations like splitting, joining, or selecting outputs. Provides visual feedback when UTXO operations complete successfully. Universal idiom ensures consistent success indication across all iOS devices. |

#### MobileWallet/Backup/

| File | Description |
|------|-------------|
| `DropboxBackupService.swift` | Dropbox cloud backup service implementing BackupServicable protocol. Handles encrypted/unencrypted wallet backups with automatic sync. Features: OAuth authentication flow, large file chunked uploads (>12MB), background task management, backup metadata checking, restore functionality with password handling. Key methods: createBackup(), restoreBackup(), turnOn/Off(). Manages sync status (enabled/disabled/progress/failed) via Combine publishers. Integrates with DropboxSDK, handles authentication redirects, creates/manages remote backup folder. Critical for wallet recovery and data protection. Dependencies: SwiftyDropbox, BackupFilesManager, TariSettings. |
| `Error+BackupErrors.swift` | Error message localization extensions for backup service error types. Provides user-friendly error messages for DropboxBackupError and ICloudBackupService.ICloudBackupError enums. Maps technical backup error codes to localized strings for display in UI error dialogs and notifications. Handles errors like authentication failures, upload/download failures, missing backups, and iCloud container issues. Used by: backup services for consistent error messaging across the app. Dependencies: localization system for message lookup. |

##### MobileWallet/Backup/Dropbox/

| File | Description |
|------|-------------|
| `DropboxBackupService.swift` | Complete Dropbox cloud backup service implementation for Tari wallet data. Manages encrypted and unencrypted wallet backups to Dropbox using SwiftyDropbox SDK. Key features: OAuth authentication flow, background task management, progress tracking, automatic backup creation, wallet restoration from backup. Implements BackupServicable protocol with reactive status updates. Supports both small file and large file upload strategies. Handles backup encryption/decryption with user passwords. Used by: BackupManager for coordinated multi-provider backup solution. Dependencies: SwiftyDropbox, BackupFilesManager, TariSettings. Error handling with typed DropboxBackupError enum. |

##### MobileWallet/Backup/ICloud/

| File | Description |
|------|-------------|
| `ICloudBackupMetadataQuery.swift` | NSMetadataQuery subclass for querying iCloud backup files by filename prefix. Configures metadata query to search iCloud ubiquitous data scope using NSPredicate for filename matching. Operates on main queue for UI updates. Dependencies: Foundation. Used by: ICloudDocsDownloadService, ICloudDocsUploadService. Part of iCloud backup infrastructure for discovering and monitoring backup files in iCloud Drive. |
| `ICloudBackupService.swift` | iCloud backup service implementation for Tari wallet data using CloudKit Documents. Manages encrypted wallet backups to iCloud Drive with network-specific folder organization. Key features: ubiquity container management, automatic backup creation/restoration, progress tracking, password-protected encryption, metadata query handling. Implements BackupServicable protocol with reactive status updates. Coordinates with ICloudDocsUploadService and ICloudDocsDownloadService for file operations. Used by: BackupManager for multi-provider backup solution. Dependencies: CloudKit, BackupFilesManager, TariSettings. Error handling with typed ICloudBackupError enum for iCloud-specific issues. |
| `ICloudDocsDownloadService.swift` | Service for downloading wallet backup files from iCloud Drive. Provides async downloadBackup() method with Combine-based status tracking (inProgress, failed, finished). Uses ICloudBackupMetadataQuery to monitor download progress and NSMetadataQuery notifications. Handles file discovery, download initiation via FileManager.startDownloadingUbiquitousItem, and completion detection. Dependencies: ICloudBackupMetadataQuery, Combine. Used by: BackupManager. Critical for wallet recovery from iCloud backups. |
| `ICloudDocsUploadService.swift` | Service for uploading wallet backup files to iCloud Drive with progress tracking. Monitors upload progress using ICloudBackupMetadataQuery and NSMetadataQuery notifications. Tracks upload status (idle, inProgress with progress percentage, finished) via @Published properties. Detects completion using NSMetadataUbiquitousItemIsUploadedKey attribute. Dependencies: ICloudBackupMetadataQuery, BackupFilesManager, Combine. Used by: BackupManager. Essential for automatic wallet backup synchronization to iCloud. |

##### MobileWallet/Backup/Manager/

| File | Description |
|------|-------------|
| `BackupFilesManager.swift` | Core backup file management system for Tari wallet data. Handles wallet database backup creation, encryption, compression, and restoration. Key features: full wallet backup (encrypted ZIP with database + passphrase), partial backup (JSON with UTXOs only), network-specific filenames, AES encryption with user passwords, ZIP compression using Zip library. Exports: BackupFilesManager enum with static methods for backup/restore operations. Dependencies: AES encryption, Zip library, Tari wallet core. Used by: DropboxBackupService, ICloudBackupService for backup file preparation and restoration. Manages temporary working directories and file cleanup for secure backup operations. |
| `BackupManager.swift` | Centralized wallet backup management supporting iCloud and Dropbox with automatic synchronization. Manages backup state, password storage, and error handling. Key features: automatic backup triggering on balance/transaction changes with throttling, scheduled backup retry mechanism, sync state tracking (disabled/outOfSync/inProgress/synced), unified error handling with popup/notification display, password management through keychain, background task notification on app termination. Coordinates ICloudBackupService and DropboxBackupService with unified BackupServicable interface. Dependencies: Combine (reactive programming), AppKeychainWrapper (secure storage), Tari wallet (data monitoring), PopUpPresenter (error display), NotificationManager (background alerts). Critical for wallet data protection and recovery. |
| `BackupServicable.swift` | Protocol definition for wallet backup services in the Tari wallet architecture. Defines common interface for backup providers (Dropbox, iCloud) with reactive patterns using Combine. Exports: BackupServicable protocol, BackupError enum. Required functionality: enable/disable backup, password management, backup status publishing, last backup timestamp tracking, forced backup execution, backup restoration. Implemented by: DropboxBackupService, ICloudBackupService. Used by: BackupManager for coordinated multi-provider backup management. Enables polymorphic backup service handling and consistent backup behavior across providers. |
| `BackupStatus.swift` | Enumeration defining backup operation states and error handling. Cases: disabled, enabled, inProgress(progress: Double), failed(error: Error?). Implements Equatable protocol for state comparison. Provides isFailed computed property for error detection. Dependencies: None. Used by: BackupManager, backup UI components, status tracking throughout backup system. Central status model for coordinating backup operations and UI feedback. |
| `PartialBackupModel.swift` | Codable data model for partial wallet backup metadata. Contains source identifier and array of UTXO strings for incremental backup functionality. Used for storing subset of wallet data in backup files rather than full wallet state. Dependencies: None. Used by: BackupManager, backup/restore operations. Enables efficient incremental backups by tracking specific UTXOs that need backing up. |

##### MobileWallet/Boards/Base.lproj/

| File | Description |
|------|-------------|
| `LaunchScreen.storyboard` | [UI STORYBOARD] App launch screen interface definition. Contains centered GemBig image (60x60) on SplashBackground color. Configured for iPhone devices with auto layout constraints and safe area support. Uses named colors for theming. Dependencies: GemBig image asset, SplashBackground color asset. Used by: App launch sequence, initial app presentation. Provides branded loading experience before main app interface loads. |

#### MobileWallet/Common/

| File | Description |
|------|-------------|
| `AESEncryption.swift` | AES-256 encryption/decryption implementation for Tari wallet data security. Provides symmetric encryption using CommonCrypto framework with PBKDF2 key derivation, random salt generation, and PKCS7 padding. Key features: AES-256-CBC encryption, secure random IV generation, PBKDF2 password-based key derivation with 10,000 iterations, comprehensive error handling with typed AESError enum. Exports: AESEncryption struct, AESError enum. Used by: BackupFilesManager for wallet backup encryption, potentially other security-sensitive operations. Critical for protecting wallet backup data with user-provided passwords. Dependencies: CommonCrypto framework, Foundation. |
| `APIService.swift` | HTTP API service for external Tari services communication (airdrop.tari.com). Implements generic request method with automatic JSON decoding, Bearer token authentication, and automatic token refresh on 401 responses. Handles OAuth-style refresh token flow with automatic token updates via UserManager integration. Provides error handling for network issues, authentication failures, and invalid responses. Critical for user authentication, airdrop functionality, and external Tari service integration. Dependencies: Combine for reactive networking, UserManager for token management. Used by UserManager for profile fetching and authentication. |
| `AppRouter.swift` | Central navigation coordinator managing app-wide screen transitions and modal presentations. Provides static methods for transitioning between major app states: splash screen, onboarding, and home screen with custom animation types. Key features: smooth screen transitions with snapshot-based animations (moveDown, crossDissolve), wallet state management for new/restored/synced wallets, tab bar navigation shortcuts, modal presentation helpers for various screens (profile, backup settings, send transaction, QR scanner), presentation hierarchy management finding top view controller, address poisoning protection in send flow, external app integration (Settings). Uses AlwaysPoppableNavigationController for consistent navigation behavior. Dependencies: MenuTabBarController (main UI), various screen constructors, PaymentInfo/QRCodeData models, PopUpPresenter (error handling), UIApplication extensions. Critical for maintaining consistent navigation patterns and managing complex presentation hierarchies throughout the app. |
| `AppValues.swift` | Global application constants and utility values container. Provides namespaced access to general app values through GeneralValues enum. Currently defines simulator detection functionality using compiler directives to distinguish between device and simulator environments. Designed as extensible structure for organizing app-wide configuration values and constants. |
| `CommandLineArgs.swift` | Development utility for processing command line arguments during app launch. Currently implements '-disable-animations' flag to disable UIView animations for automated testing and development workflows. Called from AppDelegate.didFinishLaunchingWithOptions. Provides extensible framework for adding additional developer command line options and testing configurations. |
| `Localization.swift` | Simple localization utilities providing string localization functions for internationalization support. Exports localized() function for basic string lookup from NSLocalizedString and localized() with arguments for formatted string interpolation with CVarArg parameters. Minimal but essential infrastructure for multi-language support throughout the application. Used extensively across UI components for displaying user-facing text in appropriate languages. Foundation for future internationalization expansion and accessibility compliance. |
| `NotificationManager.swift` | Comprehensive push notification manager handling both local and remote notifications for the Tari wallet app. Integrates with Firebase Cloud Messaging (FCM) and implements cryptographic signing for secure server communication. Manages notification authorization, device token registration with push server, background reminder notifications, and wallet-specific push notifications. Includes server request handling with signature validation, timeout mechanisms for wallet state, and supports both foreground and background notification scenarios. Implements MessagingDelegate for FCM token updates. |
| `PointerHandler.swift` | Low-level pointer manipulation utilities for FFI integration. Exports: PointerHandler enum with pointer() and rawPointer() methods, UnsafeMutableRawPointer extension. Functionality: Safe pointer creation for values and objects, raw pointer conversion for FFI calls, object retrieval from raw pointers. Dependencies: Foundation Unmanaged types. Used by: Rust FFI bindings, TariLib integration, low-level wallet operations. Security: Critical for safe memory management in FFI boundary, handles pointer lifetime correctly. |
| `Theme.swift` | Central design system providing app-wide visual assets and styling. Singleton structure containing Images, Fonts, and Sizes collections for consistent UI appearance. Defines navigation icons (home, settings), currency symbols (Gem), arrows, transaction status icons, wallet assets, and QR code styling. Referenced throughout the app for theme consistency and asset management. Key collections: Images (icons, backgrounds, UI elements), Fonts (typography system), Sizes (layout constants). Dependencies: UIKit. Used by all UI components for consistent branding and visual design. Critical for maintaining design system integrity. |
| `UserManager.swift` | User authentication and profile management system handling access tokens, refresh tokens, and user details. Manages JWT token validation, automatic retry logic for network failures, and reactive user state updates via @Published properties. Integrates with APIService for user profile fetching with rank information (gems, shells, hammers, total score). Provides secure token storage in UserDefaults and user session management. Supports user login state transitions (LoggedOut, Ok with UserDetails, Error states). Critical for user account functionality and authenticated API access. Dependencies: Combine for reactive programming, APIService for network requests. |
| `WebBrowserPresenter.swift` | In-app web browser presentation utility for external link handling. Provides static open method for presenting WebBrowserViewController modally with device-specific presentation styles (popover for iPad, automatic for iPhone). Ensures presentation happens on main thread and gets top view controller for proper modal presentation. Used throughout the app for opening external links while keeping users within the wallet application context rather than switching to Safari. Simple utility for consistent web content presentation. |

##### MobileWallet/Common/AlwaysPoppableNavigationController/

| File | Description |
|------|-------------|
| `AlwaysPoppableNavigationController.swift` | Custom UINavigationController subclass that ensures proper status bar style delegation. Overrides childForStatusBarStyle to delegate status bar styling to child view controllers or top view controller. Used throughout app for consistent navigation behavior and status bar appearance. Dependencies: UIKit. Used by: Navigation stack management, view controller presentation. Ensures child controllers can control status bar appearance in navigation hierarchy. |

##### MobileWallet/Common/BackgroundTasks/

| File | Description |
|------|-------------|
| `BackgroundTaskManager.swift` | Manages iOS background task registration and scheduling for reminder notifications. Singleton that handles BGTaskScheduler registration, scheduling background app refresh tasks every 15 minutes, and coordinating with ScheduleReminderNotificationsOperation. Includes debug simulation support. Dependencies: BackgroundTasks, ScheduleReminderNotificationsOperation, Logger. Used by: AppDelegate during app initialization. Critical for scheduling wallet-related reminder notifications when app is backgrounded. |
| `ReminderNotifications.swift` | Background task notification scheduler for user engagement reminders. Exports: ReminderNotifications singleton class, ScheduledReminder struct. Manages local push notifications to remind users to check the app for received Tari tokens. Implements time-delayed notifications (24h/48h for production, 2h/4h for debug). Uses UserDefaults for state persistence and supports different messaging based on network ticker symbol. Dependencies: Foundation, TariSettings, NetworkManager. |
| `ScheduleReminderNotificationsOperation.swift` | NSOperation subclass for scheduling wallet reminder notifications in background tasks. Validates timing constraints, cancels existing notifications, and schedules new reminders based on ReminderNotifications configuration. Handles timing validation to ensure notifications are scheduled appropriately relative to trigger events. Dependencies: ReminderNotifications, NotificationManager, Logger. Used by: BackgroundTaskManager. Critical for maintaining user engagement through timely wallet-related notifications. |

##### MobileWallet/Common/Data Models/

| File | Description |
|------|-------------|
| `PaymentInfo.swift` | Payment information data model encapsulating all transaction details for send operations. Contains recipient address components, optional alias for display, Yat ID for human-readable addresses, optional amount and fee specifications, and transaction note. Essential data structure passed through the send transaction workflow from recipient selection through confirmation. Used by AddressPoisoningDataHandler for security validation, transaction creation services, and UI components for payment preview and confirmation. Simple but critical model ensuring consistent payment data structure across the entire transaction creation process. |
| `TransactionDynamicModel.swift` | Transaction data model with dynamic content support. Exports: TransactionDynamicModel class with GifDataState enum. Manages transaction timestamps with automatic updates via timer, and handles Giphy GIF loading/caching for transaction attachments. Published properties for UI binding: formattedTimestamp, gif state. Dependencies: GiphyUISDK, TxGifManager. Used by transaction list views to display dynamic content like GIFs and relative timestamps. |

###### MobileWallet/Common/Data Models/Publishers/

| File | Description |
|------|-------------|
| `Publishable.swift` | Protocol defining types that can be used with reactive publishing patterns. Requires default initializer for creation of initial/default values. Extended to Bool for basic reactive state management. Dependencies: None. Used by: StaticPublisherWrapper, reactive programming utilities, state management. Enables type-safe reactive programming with required initialization semantics. |
| `StaticPublisherWrapper.swift` | Generic wrapper class for converting Published publishers to static properties. Takes a Published<T>.Publisher and assigns it to internal @Published value property. Enables clean encapsulation of reactive data streams with type constraint requiring Publishable protocol. Dependencies: Publishable protocol, Combine. Used by: Data model wrappers, reactive state management. Facilitates conversion between publisher patterns and static property access. |

##### MobileWallet/Common/Deep Links/

| File | Description |
|------|-------------|
| `DeepLinkDefaultActionsHandler.swift` | Deep link action handler implementing default behaviors for all deep link types. Exports: DeepLinkDefaultActionsHandler enum with ActionType, notification extensions. Handles base node addition, contact imports, transaction sending, paper wallet recovery, and login flows. Supports direct actions, pop-up confirmations, and notification displays. Manages UI navigation, wallet state updates, and user consent flows. Dependencies: UIKit, Tari wallet, ContactsManager, PopUpPresenter, AppRouter, UserManager, notification system. |
| `DeepLinkHandler.swift` | Central deep link processing engine for tari:// URL scheme handling. Exports: DeeplinkHandler enum with static deeplink() and handle() methods. Processes URL types: base node addition, contacts import/export, user profile sharing, transaction sending, paper wallet import, authentication/login. Key features: URL parsing with DeepLinkFormatter, type-safe routing to specific handlers, retry mechanism with exponential backoff (3 retries), error notification broadcasting, action type differentiation (import vs export), validation and security checks. Dependencies: DeepLinkFormatter (URL parsing), DeepLinkDefaultActionsHandler (action execution), various DeepLinkable protocols. Critical for external app integration and wallet interoperability through standardized URL schemes. |
| `DeeplinkHandler.swift` | Central deep link processing system handling tari:// URL scheme with comprehensive error handling and retry logic. Parses URLs into typed deep link models and routes to appropriate action handlers. Supports multiple deep link types: transaction send, base node addition, contacts, user profile, paper wallet, and login. Key features: DeepLinkFormatter for URL parameter parsing, action type detection (popup/notification/direct) based on app state, wallet readiness validation before processing, retry mechanism with exponential backoff for failed handling, notification posting for permanent failures. Handles edge cases like app backgrounding, wallet not running, and navigation not ready. Dependencies: DeepLinkFormatter (URL parsing), DeepLinkDefaultActionsHandler (action execution), AppRouter (navigation state), Tari wallet (running status), various deeplink models. Critical for external app integration and QR code-based interactions. |

###### MobileWallet/Common/Deep Links/Data Handling/

| File | Description |
|------|-------------|
| `DeepLinkFormatter.swift` | Core deep link URL formatting and parsing system. Exports: DeepLinkFormatter enum, DeepLinkable protocol, DeeplinkType enum, DeepLinkError. Handles conversion between deep link URLs and strongly-typed Swift models. Supports transaction send, base node add, contacts, profile, paper wallet, and login deep links. Uses 'tari' URL scheme with network-specific hosts. Integrates with DeepLinkEncoder/Decoder for serialization. Dependencies: NetworkManager for network validation. |

###### MobileWallet/Common/Deep Links/Data Handling/Decoder/

| File | Description |
|------|-------------|
| `DeepLinkDecoder.swift` | Custom Decoder implementation for parsing deep link URL parameters into Codable objects. Converts URL query parameters and path components into nested dictionary structure, then provides Decoder interface for automatic object parsing. Implements container methods for keyed and unkeyed decoding. Dependencies: Foundation (Decoder protocol). Used by: Deep link handling system, URL parameter parsing. Enables type-safe conversion of deep link URLs to structured data models. |
| `DeepLinkKeyedDecodingContainter.swift` | Deep link URL parameter keyed decoding container implementing KeyedDecodingContainerProtocol. Exports: DeepLinkKeyedDecodingContainter struct. Handles decoding of deep link URL parameters into typed Swift values for all primitive types (Bool, String, Int variants, UInt variants, Double, Float). Uses DeeplinkDataSource for parameter access. Part of custom Codable implementation for deep link parameter parsing. Dependencies: DeeplinkDataSource. |
| `DeeplinkDataSource.swift` | Deep link parameter data source and type conversion utility. Exports: DeeplinkDataSource struct. Converts URL parameter strings to typed Swift values for all primitive types. Handles nested parameter objects by creating recursive DeepLinkDecoder instances. Provides type-safe parameter access with error handling for parsing failures. Used by DeepLinkKeyedDecodingContainter for parameter decoding. Dependencies: DeepLinkDecoder, DeepLinkError. |
| `DeeplinkUnkeyedDecodingContainer.swift` | Deep link URL parameter unkeyed decoding container implementing UnkeyedDecodingContainer protocol. Exports: DeeplinkUnkeyedDecodingContainer struct. Handles sequential decoding of indexed deep link parameters into typed Swift values. Manages decoding state with currentIndex tracking and count-based iteration. Uses DeeplinkDataSource for parameter access with string-based indexing. Part of custom Codable implementation for deep link array parameter parsing. Dependencies: DeeplinkDataSource. |

###### MobileWallet/Common/Deep Links/Data Handling/Encoder/

| File | Description |
|------|-------------|
| `DeepLinkEncoder.swift` | Core deep link encoder implementing Swift's Encoder protocol to convert deep link model objects into URL query strings. Exports: DeepLinkEncoder struct with result property providing encoded query string. Dependencies: DeepLinkEncoderStorage, DeepLinkKeyedEncodingContainer, DeepLinkUnkeyedEncodingContainer. Part of the deep링크 system that enables tari:// URL generation for wallet operations like sending transactions, adding contacts, and base node configuration. Used by DeepLinkFormatter for URL creation. |
| `DeepLinkEncoderStorage.swift` | Storage backend for deep link URL query string generation. Exports: DeepLinkEncoderStorage class with query property and methods add(key:value:), add(keysHierarchy:value:), add(subquery:). Manages hierarchical key-value pair accumulation and URL query formatting with proper key nesting (e.g., 'key[subkey]=value'). Used by DeepLinkEncoder infrastructure to build complex URL parameter strings from nested object structures. |
| `DeepLinkKeyedEncodingContainer.swift` | Keyed encoding container for deep link URL generation implementing Swift's KeyedEncodingContainerProtocol. Exports: DeepLinkKeyedEncodingContainer struct supporting all primitive types and nested Encodable objects. Dependencies: DeepLinkEncoderStorage, DeepLinkEncoder. Handles object-to-URL-query conversion with automatic type serialization and nested structure support. Essential component of the deep link encoding pipeline. |
| `DeepLinkUnkeyedEncodingContainer.swift` | Unkeyed encoding container for deep link array serialization implementing Swift's UnkeyedEncodingContainer protocol. Exports: DeepLinkUnkeyedEncodingContainer struct with automatic array indexing and primitive type support. Dependencies: DeepLinkEncoderStorage, DeepLinkEncoder. Handles array-to-URL-query conversion with indexed key generation (e.g., 'list[0]=value'). Used for encoding array properties in deep link models. |

###### MobileWallet/Common/Deep Links/Models/

| File | Description |
|------|-------------|
| `BaseNodesAddDeeplink.swift` | Deep link model for adding custom base nodes via tari://network/base_nodes/add URLs. Exports: BaseNodesAddDeeplink struct with name and peer address properties. Implements DeepLinkable protocol with .baseNodesAdd type. Enables users to share custom base node configurations for network connectivity, supporting testnet and mainnet environments. Used by network settings to configure alternative node connections. |
| `ContactListDeeplink.swift` | Deep link model for batch contact import via tari://network/contacts URLs. Exports: ContactListDeeplink struct containing array of Contact objects with alias and tariAddress properties. Implements DeepLinkable protocol with .contacts type. Enables sharing and importing multiple contacts simultaneously through QR codes or URL sharing. Used by contact management system for bulk contact operations. |
| `LoginDeeplink.swift` | Deep link model for external service authentication via tari://network/airdrop/auth URLs. Exports: LoginDeeplink struct with token (required) and refreshToken (optional) properties. Implements DeepLinkable protocol with .login type. Handles OAuth-style authentication for third-party services and airdrops, enabling secure token-based authentication flow for external integrations. |
| `PaperWalletDeeplink.swift` | Deep link model for paper wallet import via tari://network/paper_wallet URLs. Exports: PaperWalletDeeplink struct with privateKey (required), appId (optional), and balance (optional) properties. Implements DeepLinkable protocol with .paperWallet type. Enables importing paper wallet private keys for wallet restoration or fund recovery. Handles secure private key parsing with anonymous ID tracking. |
| `TransactionsSendDeeplink.swift` | Deep link model for transaction sending operations via tari://network/transactions/send URLs. Exports: TransactionsSendDeeplink struct with receiverAddress (required), amount (optional), and note (optional) properties. Implements DeepLinkable protocol with .transactionSend type. Used by deep link handler to pre-populate send transaction screen when opened via external URLs. Essential for inter-app wallet functionality and sharing payment requests. |
| `UserProfileDeeplink.swift` | Deep link model for user profile sharing via tari://network/profile URLs. Exports: UserProfileDeeplink struct with alias and tariAddress properties. Implements DeepLinkable protocol with .profile type. Enables sharing user wallet information through QR codes and URLs for easy contact addition. Used by profile sharing and contact exchange features. |

##### MobileWallet/Common/Errors/

| File | Description |
|------|-------------|
| `CoreError.swift` | Base error protocol defining standardized error handling across the Tari wallet application. Requires code and domain properties for error categorization and provides signature generation combining domain and code for debugging. Implements Equatable comparison based on error codes. Foundation for all application-specific errors including WalletError, ensuring consistent error identification and debugging capabilities. Used throughout error management system for uniform error handling and technical support. |
| `PosixError.swift` | POSIX error handling and conversion utilities. Exports: PosixError struct conforming to CoreError protocol, Error extension. Provides structured error handling for POSIX system errors with code mapping and domain identification. Includes common error constants like connectionRefused (code 61). Converts NSError instances with NSPOSIXErrorDomain to PosixError for consistent error handling across the app. |
| `WalletError.swift` | Tari wallet-specific error definitions conforming to CoreError protocol. Defines common wallet errors with specific codes: insufficient funds (101), pending funds (115), invalid passphrase (428), recovery failure (702), and unknown errors (-1). Provides pattern matching operator for error type checking and FFI domain identification. Essential for wallet operation error handling and user feedback with precise error categorization. Used by ErrorMessageManager for localized error presentation and throughout wallet operations for error identification. |

##### MobileWallet/Common/Extensions/

| File | Description |
|------|-------------|
| `Animation+EnumInit.swift` | Lottie Animation extension providing enumerated animation asset management. Exports: Animation.LottieAnimation enum with predefined animation names (splash, checkMark, faceID, touchID, notification, emojiWheel, etc.) and Animation.named(_:) convenience method. Dependencies: Lottie framework. Used throughout the app for consistent animation loading and type-safe animation references. Essential for the wallet's animated UI elements and transitions. |
| `CChar+Helpers.swift` | UnsafePointer extension for C string to Swift String conversion. Exports: UnsafePointer<CChar>.string computed property for convenient C string conversion. Used for FFI (Foreign Function Interface) operations with the Rust TariLib, converting C-style strings returned from Rust functions to Swift String objects. Critical for wallet core integration. |
| `CharacterSet+CustomSets.swift` | CharacterSet extension providing custom character sets for Tari-specific input validation. Exports: CharacterSet.digitsAndDecimalSeparator (for MicroTari currency input) and CharacterSet.hexadecimal (for hexadecimal string validation). Dependencies: MicroTari for decimal separator. Used in text input validation, amount formatting, and cryptographic key validation throughout the wallet interface. |
| `ContactsManager+Utils.swift` | ContactsManager.Model extension providing UI view model conversion for contact display. Exports: contactBookCellAddressViewModel computed property returning AddressView.ViewModel with appropriate display formatting. Dependencies: ContactsManager, AddressView. Handles alias display vs. truncated address presentation for contact list cells. Used by contact book UI components for consistent address representation. |
| `Data+Utlis.swift` | Data extension providing utility methods for data manipulation and conversion. Exports: Data.string computed property (UTF-8 string conversion), Data.split(chunkSize:) method for data chunking, and Data.appending(byte:) for byte appending. Dependencies: Foundation. Used for binary data processing, file operations, cryptographic data handling, and Tari blockchain data manipulation throughout the wallet. |
| `Date.swift` | Date formatting extensions for user-friendly time displays. Exports: Date extensions with relativeDayFromToday() and formattedDisplay() methods. Provides intelligent relative date formatting showing 'now', minutes/hours ago for today, 'yesterday', or full date format for older dates. Supports localized time display for transaction lists and timestamps. Uses Calendar and DateFormatter for proper locale and timezone handling. Dependencies: Foundation for date operations. |
| `DateFormatter+Formats.swift` | DateFormatter extension providing predefined date formatting configurations for wallet operations. Exports: DateFormatter.backupTimestamp (readable backup dates) and DateFormatter.logTimestamp (file-safe log timestamps). Uses en_US_POSIX locale for consistency. Used for wallet backup timestamps, log file naming, and consistent date display throughout the app. |
| `Dictionary+Tools.swift` | Dictionary extension that provides nested merge functionality for [String: Any] dictionaries. Exports: nestedMerge(with:) method that recursively merges nested dictionaries, preserving sub-dictionary structure while merging overlapping keys. Used throughout the app for configuration merging and data processing. Handles deep dictionary merging scenarios where simple merge() would overwrite entire nested objects. |
| `FileManager+SecureCopy.swift` | FileManager extension for secure file operations with date-based sorting. Exports: FileManager.contentsOfDirectory(atURL:sortedBy:ascending:options:) method and ContentDateType enum for creation/modification/access date sorting. Dependencies: Foundation. Used for wallet backup file management, log file organization, and secure document handling. Provides enhanced directory enumeration with built-in date sorting capabilities. |
| `LAContext.swift` | LocalAuthentication extension providing biometric authentication utilities. Exports: BiometricType enum (none, touchID, faceID, pin), AuthenticateUserReason enum, biometricType computed property, authenticateUser() methods. Key features: Device biometric capability detection, user authentication with Face ID/Touch ID/PIN, fallback authentication options, error handling with localized messages. Dependencies: LocalAuthentication framework, UIKit for alerts, AppValues for simulator detection, Logger for error tracking. Used by: Security screens, wallet unlock functionality, transaction confirmation flows. Special considerations: Bypasses authentication on simulator for development convenience. |
| `NSAttributedString+Format.swift` | NSAttributedString extension for string formatting with attributed strings. Exports: NSAttributedString convenience initializer for format-based construction. Functionality: String formatting using %@ placeholders, preserves text attributes during substitution, supports multiple attributed string arguments. Dependencies: Foundation NSAttributedString/NSMutableAttributedString. Used by: UI text formatting throughout the app, attributed string construction with dynamic content. Pattern: Similar to String.format but maintains text styling and attributes. |
| `Publisher+Binding.swift` | Combine Publisher extensions for reactive binding utilities. Exports: assignPublisher(to:on:) method for weak reference binding, onChangePublisher() method for change detection. Functionality: Safe binding to object properties with automatic weak references, change event transformation for reactive chains. Dependencies: Combine framework. Used by: MVVM reactive bindings throughout app, view model to view connections, reactive UI updates. Pattern: Extension-based utilities for Combine reactive programming. |
| `Result+Void.swift` | Swift Result type extension for Void success cases. Exports: static success property for Result<Void, Error> types. Functionality: Convenient creation of successful Void results, eliminates boilerplate for completion-only operations. Dependencies: Swift standard library Result type. Used by: Async operations with no return value, completion handlers, validation methods throughout the app. Pattern: Type extension for common result patterns. |
| `String+Emoji.swift` | String and Character extensions for emoji detection and text similarity analysis. Exports: Character.isSimpleEmoji, Character.isCombinedIntoEmoji, Character.isEmoji, String.containsOnlyEmoji, String.isSimilar(to:minSameCharacters:usedPrefixSuffixCharacters:). Dependencies: Foundation. Used throughout the app for validating emoji-based wallet addresses and detecting similar text patterns for security validation. Critical for Tari's emoji ID system. |
| `String+Hex.swift` | String extension providing hexadecimal conversion utilities for the Tari wallet app. Implements hex() method to convert Unicode scalars to uppercase hexadecimal representation, and initializer for converting C hex tuples to hexadecimal strings using unsafe pointer operations. Used for cryptographic data formatting, transaction identifiers, and low-level data representation in wallet operations. Part of common utilities supporting blockchain and cryptographic functionality. |
| `String+Tools.swift` | String utility extensions for base node validation and text processing. Provides isBaseNodeAddress static method validating hex keys (64-character lowercase hex) and addresses (onion3 or IPv4 format) with iOS version-aware regex implementation. Includes splitElementsInBrackets method for parsing bracket-delimited content with iOS 16+ support. Essential for network configuration validation, ensuring proper base node addresses and peer string formatting. Used by connection services for validating user-entered node configurations and parsing network addresses. |
| `String.swift` | String utility extensions for common text operations. Exports: String extensions with separator insertion, bridge config parsing, random string generation, tokenization, currency symbol formatting, height calculation. StringProtocol extensions for index distance calculations. Collection and String.Index extensions for distance operations. Array extensions for SubSequence handling. Used throughout app for text processing, UI formatting, and data parsing. Dependencies: UIKit for font calculations, Theme for currency symbols. |
| `Task+Utils.swift` | Task extension providing convenient async sleep functionality and delayed task execution. Exports: Task.sleep(seconds:) static method for cleaner async delay code, Task.init(after:operation:) convenience initializers for delayed execution on main actor or async contexts. Dependencies: Swift concurrency. Used throughout async operations for timing delays, animations, and rate limiting. Simplifies common async/await patterns in wallet operations with improved API using modern Swift concurrency features. |
| `UIApplication+Utils.swift` | UIApplication extension providing app-wide utility methods for view controller management and navigation. Exports: firstWindow, menuTabBarController, topController properties, hideKeyboard() method, and private helper methods. Dependencies: UIKit, MenuTabBarController. Used for global navigation, keyboard dismissal, and finding specific view controllers throughout the app lifecycle. |
| `UICollectionViewDiffableDataSource+Animation.swift` | UICollectionViewDiffableDataSource extension for improved animation handling. Exports: apply(delayedSnapshot:animatingDifferences:completion:) method. Functionality: Delayed snapshot application to fix self-sizing cell animation issues, prevents animation glitches during size estimation. Dependencies: UIKit, uses DispatchQueue for timing. Used by: Collection views with dynamic cell sizes, animated list updates throughout the app. Performance: Fixes visual glitches in collection view animations by delaying updates by 0.01 seconds. |
| `UIColor+Utils.swift` | UIColor extension providing dynamic color variant generation based on text input. Exports: UIColor.colorVariant(text:) method that creates deterministic color variations using HSB color space manipulation. Dependencies: UIKit. Used for generating unique but consistent avatar colors and contact visual identifiers based on wallet addresses or contact names. Implements hash-based color generation with saturation and brightness adjustments. |
| `UIFont+FontStyle.swift` | UIFont extension providing custom font family access with updated font naming convention. Exports: Poppins enum (Bold, Medium, Light, Black, SemiBold, Regular), DrukWideTrial enum (Bold). Functionality: Type-safe access to custom fonts with size specification, font factory methods for consistent typography using hyphenated font names (Poppins-Bold vs Poppins Bold). Dependencies: UIKit, Poppins and DrukWideTrial font files. Used by: UI typography throughout the app, theme system, consistent font styling. Pattern: Enum-based font factory with size specification methods. |
| `UIImage+Utils.swift` | UIImage utility extensions for Core Image integration. Exports: makeCIImage() method. Functionality: Converts UIImage to CIImage with fallback handling, supports both CIImage and CGImage backing stores. Dependencies: UIKit, Core Image framework. Used by: Image processing operations, filter applications, QR code generation workflows. Pattern: Safe conversion utility with multiple backing store support. |
| `UILabel.swift` | UILabel extension for text styling utilities. Exports: interlineSpacing(spacingValue:), letterSpacing(value:) methods. Functionality: Line spacing adjustment using NSMutableParagraphStyle, letter spacing (kerning) adjustment using NSAttributedString. Dependencies: UIKit Foundation. Used by: Text styling throughout the app, typography consistency, improved readability. Pattern: Extension methods for attributed text manipulation. |
| `UINavigationController+Common.swift` | UINavigationController extension providing convenient navigation stack manipulation. Exports: remove(controller:) method for safely removing specific view controllers from the navigation stack. Dependencies: UIKit. Used for custom navigation flow management, modal dismissal, and navigation stack cleanup throughout the wallet's screen transitions. |
| `UIScreen+Tools.swift` | UIScreen extension for device screen size detection utilities. Exports: isSmallScreen static property. Functionality: Detects small screen devices (iPhone SE, iPhone 8 and smaller) using native bounds height threshold of 1334.0 points. Dependencies: UIKit. Used by: Layout adjustments, responsive UI design, conditional layouts throughout the app. Pattern: Static utility property for device-specific UI adaptations. |
| `UIScrollView+RefreshControl.swift` | UIScrollView extension for programmatic scrolling utilities. Exports: scrollToBottom(animated:), scrollToTop(animated:) methods. Functionality: Smooth scrolling to top and bottom positions, handles content insets, prevents negative offsets. Dependencies: UIKit. Used by: Chat interfaces, long lists, programmatic navigation in scroll views throughout the app. Pattern: Convenience methods for common scroll operations. |
| `UIStackView+Common.swift` | UIStackView extension providing common utility methods. Exports: removeAllViews() method that removes all arranged subviews and removes them from the superview. Dependencies: UIKit framework. Used by: UI screens that dynamically manage stack view contents. Provides clean removal of subviews with proper view hierarchy cleanup. |
| `UITableView+Common.swift` | UITableView extension providing header/footer frame management and cell resizing utilities. Exports: updateHeaderFrame(), updateFooterFrame(), resizeCellsWithoutAnimation() methods. Dependencies: UIKit framework. Used by: Screens with dynamic table headers/footers that need proper sizing. Provides auto-layout-based sizing for table view headers and footers, and smooth cell height updates without animation. |
| `UITextField+Combine.swift` | UITextField extension for Combine reactive programming integration. Exports: bind(withSubject:storeIn:), textPublisher(), isEditingPublisher() methods. Functionality: Two-way binding with CurrentValueSubject, text change publisher, editing state publisher using NotificationCenter. Dependencies: UIKit, Combine framework. Used by: Reactive form inputs, MVVM text field binding, real-time validation throughout the app. Pattern: Publisher-based reactive UI components with bidirectional data flow. |
| `UIView+Content.swift` | UIView extension for shadow styling utilities. Exports: Shadow struct (color, opacity, radius, offset), apply(shadow:) method. Functionality: Configurable shadow application and removal, encapsulated shadow configuration, layer-based shadow rendering. Dependencies: UIKit Core Animation. Used by: UI styling throughout app, card views, elevated components, visual depth effects. Pattern: Configuration struct with application method for view styling. |
| `UIView+GlobalFrame.swift` | UIView extension for global coordinate system utilities. Exports: globalFrame computed property. Functionality: Converts view frame to global/window coordinates, useful for positioning popups and overlays relative to views. Dependencies: UIKit. Used by: Popup positioning, overlay calculations, coordinate transformations throughout the app. Pattern: Computed property for coordinate system conversion. |
| `UIView+Utils.swift` | UIView animation utility extension providing async/await wrapper for UIView.animate. Enables modern Swift concurrency patterns for UI animations, replacing completion-based animation API with structured concurrency. Returns Bool indicating animation completion status. Simplifies animation code by enabling await syntax for sequential animations and animation coordination. Used throughout the app for smooth UI transitions and interactive feedback. |
| `UIViewController+Common.swift` | UIViewController extension for common presentation utilities. Exports: isModal computed property. Functionality: Detects if view controller is presented modally, supports both direct presentation and navigation controller presentation. Dependencies: UIKit, AlwaysPoppableNavigationController. Used by: Navigation logic, presentation handling, modal dismissal throughout the app. Pattern: Utility property for presentation context detection. |
| `UIViewController+Content.swift` | UIViewController extension providing content management and keyboard handling utilities. Exports: hideKeyboardWhenTappedAroundOrSwipedDown(), dismissKeyboard(), add(childController:containerView:), presentOnFullScreen() methods, hasNotch global property. Dependencies: UIKit framework. Used by: All view controllers needing keyboard management or child controller embedding. Provides keyboard dismissal on tap/swipe, child view controller embedding with auto-layout, and full-screen presentation modes. Includes device notch detection utility. |
| `UInt8+Utils.swift` | UInt8 extension providing Tari-specific utilities for emoji mapping and bit manipulation. Exports: tariEmoji computed property, flag(bitmask:) method. Dependencies: TariEmojis class. Used by: Tari address display, transaction metadata, and flag-based feature detection. Converts UInt8 values to corresponding Tari emoji characters and provides bitwise flag checking functionality for feature flags and status indicators. |
| `URL+Tools.swift` | URL extension providing deep link and query parameter parsing utilities. Exports: keysValueComponents computed property that parses URL query parameters into nested key-value dictionaries. Dependencies: String extension for bracket splitting. Used by: Deep link handlers and URL parameter extraction throughout the app. Supports complex nested parameter structures with bracket notation for deep link processing. |

##### MobileWallet/Common/Factories/

| File | Description |
|------|-------------|
| `QRCodeFactory.swift` | QR code generation factory utility. Exports: QRCodeFactory.makeQrCode(data:) async method. Functionality: Generates QR codes from Data using Core Image, scales output to screen width, uses Q error correction level, returns UIImage asynchronously. Dependencies: UIKit for screen dimensions and image handling, Core Image for QR generation. Used by: Contact sharing, wallet address display, transaction deep links, paper wallet export. Pattern: Async/await factory method for image generation. |

##### MobileWallet/Common/Formatters/

| File | Description |
|------|-------------|
| `AmountNumberFormatter.swift` | Reactive number formatter specialized for Tari currency amount input and display. Exports: AmountNumberFormatter class with @Published properties (amount, amountValue), append(string:), removeLast() methods. Dependencies: Combine, MicroTari. Handles decimal input validation, max 6 fraction digits, grouping separators, and real-time formatting. Critical for all transaction amount input throughout the wallet. |
| `AppVersionFormatter.swift` | Application version formatter utility. Exports: AppVersionFormatter enum with static version property. Functionality: Formats app version display string including network name, version number, and build number. Format: "NETWORKNAME vX.X.X (bXXX)". Dependencies: AppInfo for version data, NetworkManager for network context. Used by: Settings screen, about dialog, debug information display. Pattern: Static utility formatter for consistent version display. |
| `TransactionFormatter.swift` | Transaction data formatter for UI display with updated contact matching and payment terminology. Exports: TransactionFormatter class with Model struct. Functionality: Formats transactions for UI display including contact names, amounts, status, notes, payment IDs. Features: Contact name resolution using TariAddressComponents equality, transaction filtering, title component styling with 'Paid' terminology, amount formatting, coinbase transaction handling, emoji address truncation fallback. Dependencies: ContactsManager, StylizedLabel, AmountBadge, TariAddress. Used by: Transaction lists, transaction history screens, transaction details. Pattern: Data formatter with async contact updates and filtering support. |

##### MobileWallet/Common/Managers/

| File | Description |
|------|-------------|
| `AddressPoisoningManager.swift` | Security manager for detecting and preventing address poisoning attacks in cryptocurrency transactions with improved address comparison logic. Analyzes wallet addresses to identify potentially malicious addresses that share similar character patterns with legitimate contacts. Key features: configurable similarity detection (3+ matching characters in prefix/suffix), contact database comparison using TariAddressComponents equality instead of unique identifiers, transaction history analysis for suspicious addresses, Tari-only contact filtering. Exports: AddressPoisoningManager singleton, SimilarAddressData struct. Used by: transaction sending flows, address validation UI components. Dependencies: ContactsManager for address comparison, TariAddress for cryptographic operations. Critical security component protecting users from clipboard-based address poisoning scams common in cryptocurrency wallets. |
| `AnimationHandler.swift` | Animation completion handler manager for Core Animation. Exports: AnimationHandler class with onCompletion callback, CAAnimation extension for completion handling. Functionality: Provides completion callbacks for CAAnimation objects, manages animation delegate lifecycle, extends CAAnimation with convenient onCompletion property. Dependencies: QuartzCore framework. Used by: UI animations throughout the app, view transitions, custom animation sequences. Pattern: Delegate pattern implementation for animation completion callbacks. |
| `BugReportService.swift` | Bug reporting system integrating with Sentry for crash reporting and user feedback collection. Compresses recent log files (last 5) into ZIP attachments, creates temporary working directories for log processing, and submits bug reports with user details and log files to Sentry. Provides prepareLogsURL method for manual log export and automatic cleanup of temporary files. Essential for debugging and user support by collecting detailed crash information with relevant log context. Dependencies: Zip framework for compression, Sentry SDK for crash reporting, Tari.shared for log file access. |
| `DataFlowManager.swift` | Centralized data coordination manager implementing app-wide data flow orchestration. Singleton pattern managing data synchronization, state coordination between services, and reactive data flow using Combine framework. Handles configuration and setup of data flow relationships between different app components. Maintains cancellables for subscription management and coordinates data consistency across wallet services. Used for centralizing data flow patterns and ensuring proper data synchronization throughout the application lifecycle. |
| `ErrorMessageManager.swift` | Centralized error handling and localization manager that converts various error types into user-friendly MessageModel objects. Handles WalletError, FFIWalletHandler.GeneralError, SeedWords.InternalError, and ContactsManager.InternalError with specific localized messages. Provides fallback to generic error messages when specific translations are unavailable. Includes error signature appending for debugging support and maintains consistent error presentation across the app. |
| `LocalNotificationsManager.swift` | Local push notification management system extending NSObject and handling UserNotifications framework integration. Manages notification categories (contactReceived, contactsReceived), custom actions, and notification scheduling for wallet events. Implements UNUserNotificationCenterDelegate for notification handling and user interaction processing. Handles transaction notifications, contact updates, and other wallet-related local alerts. Provides centralized notification coordination ensuring users receive timely updates about wallet activity and important events. |
| `LogFilesManager.swift` | Log files cleanup manager for maintaining log storage. Exports: LogFilesManager enum with cleanupLogs() static method. Functionality: Automatic log file cleanup based on age and count limits, maintains minimum 5 files, removes files older than 7 days, sorted by modification date. Dependencies: Tari logging system, FileManager. Used by: App maintenance, log rotation system, storage management. Performance: Prevents unlimited log accumulation, maintains app storage footprint. |
| `MigrationManager.swift` | Wallet database version validation and migration management system with improved async handling. Validates wallet database version against minimum required version for network compatibility, provides user dialog for outdated wallet deletion with retry logic for version fetching using modern Task.sleep API. Prevents app crashes from incompatible database schemas and ensures wallet compatibility with current network protocol. Integrates with NetworkManager for version requirements and Tari wallet for version checking. Essential for maintaining backward compatibility and preventing data corruption during app updates. Shows destructive confirmation dialog for wallet deletion when migration is required. |
| `PendingDataManager.swift` | Manager for handling data that needs to be processed when wallet becomes available. Exports: PendingDataManager singleton class. Functionality: Stores contacts temporarily until wallet is running, automatically processes pending contacts when wallet becomes available, reactive wallet state monitoring. Dependencies: Combine for reactive updates, SwiftEntryKit, Tari wallet core. Used by: Contact import flows, wallet initialization sequences, data synchronization. Pattern: Singleton manager with reactive state monitoring. |
| `SecurityManager.swift` | Singleton security manager controlling app-wide security settings using Combine framework. Manages screenshot prevention functionality through areScreenshotsDisabled published property, automatically syncing with GroupUserDefaults for persistence. Provides centralized security configuration with reactive updates across the app. Designed to expand with additional security features like screen recording protection, jailbreak detection, etc. |
| `SeedWordsWalletRecoveryManager.swift` | Wallet recovery manager using seed phrase restoration. Exports: SeedWordsWalletRecoveryManager class with recovery methods. Functionality: Wallet recovery from seed phrases, custom base node configuration, wallet deletion, welcome overlay management, error handling for recovery failures. Key methods: recover(wallet:cipher:passphrase:), recover(wallet:seedWords:), deleteWallet(). Dependencies: Tari wallet core, SeedWords utility, UserDefaults. Used by: Wallet restore flows, seed phrase import screens. Security: Handles sensitive seed phrase data for wallet recovery. |
| `ShortcutsManager.swift` | iOS home screen shortcuts management for quick wallet actions. Configures dynamic shortcuts for showing QR code and sending Tari with localized titles and network-specific ticker symbols. Handles shortcut execution with queuing system for delayed execution when app navigation isn't ready. Routes shortcuts to appropriate app screens: QR display (Profile) and send transaction (Contact Book). Provides quick access to common wallet operations from iOS home screen with proper navigation coordination. Dependencies: UIKit for shortcuts API, AppRouter for navigation, NetworkManager for currency symbols. |
| `StagedWalletSecurityManager.swift` | Staged wallet security manager implementing progressive security measures based on wallet balance thresholds. Exports: StagedWalletSecurityManager singleton with start(), stop() methods. Dependencies: Combine, UIKit, TariSettings, wallet balance publisher, AppRouter, PopUpPresenter. Used by: App lifecycle to monitor security stages. Implements 4-stage security: 1A (seed phrase verification), 1B (backup setup), 2 (backup password), 3 (cold storage recommendation). Shows timed security prompts with user education and navigation to appropriate settings. |
| `TrackingConsentManager.swift` | Analytics tracking consent manager handling user privacy preferences for crash logging and telemetry. Exports: TrackingConsentManager enum with handleTrackingConsent() method. Dependencies: GroupUserDefaults, PopUpPresenter, AppConfigurator. Used by: App initialization to request tracking consent. Shows one-time consent dialog for crash reporting and analytics, updates system-wide tracking settings based on user choice. Manages privacy compliance for data collection. |
| `VideoCaptureManager.swift` | QR code scanning manager using AVFoundation for camera capture. Handles video session setup and QR code detection. Exports: ScanResult enum, setupSession(), startSession(), stopSession(). Dependencies: AVFoundation, DeeplinkHandler. Used by: QR scanning screens. Supports Tari deep links, Tor bridges, and Base58 addresses. |
| `WalletTransactionsManager.swift` | Core transaction management service handling Tari blockchain transaction execution with updated payment ID handling. Exports: WalletTransactionsManager class with TransactionError enum, State enum, and performTransactionPublisher(address:amount:feePerGram:paymentID:isOneSidedPayment:) method. Dependencies: Combine, TariLib, AppConnectionHandler, NotificationManager. Manages connection validation, transaction broadcasting with separate payment ID parameter instead of message field, result monitoring, and recipient notifications. Central to all wallet send operations with improved parameter separation for payment identification. |

###### MobileWallet/Common/Managers/Connection Monitor/

| File | Description |
|------|-------------|
| `AppConnectionHandler.swift` | Application-level connection monitoring coordinator. Exports: AppConnectionHandler singleton class with ConnectionMonitor integration. Functionality: Manages wallet connection status monitoring, coordinates Tor and base node connections, updates connection configuration when wallet is created, uses Combine for reactive updates. Dependencies: Combine framework, Tari wallet core, ConnectionMonitor. Used by: Network status monitoring throughout app, connection health indicators. Pattern: Singleton coordinator with reactive setup via Combine publishers. |

##### MobileWallet/Common/Onboarding/

| File | Description |
|------|-------------|
| `OnboardingPageView.swift` | Individual onboarding page view displaying security stages with images, styled text, and action buttons. Extends DynamicThemeView. Exports: OnboardingPageView class, ViewModel struct. Dependencies: TariCommon, StylizedLabel. Used by: OnboardingPageViewController. Contains static page data for 4 security stages and dynamic height calculation for responsive layouts. |
| `OnboardingPageViewController.swift` | View controller wrapper for individual onboarding pages managing content and footer height. Exports: OnboardingPageViewController class. Dependencies: OnboardingPageView. Used by: OnboardingViewController for each of 4 onboarding steps. Handles action button callbacks and view model configuration with dismissal logic. |
| `OnboardingPagerElementView.swift` | Individual progress element within onboarding pagination indicator providing smooth progress animation. Represents a single step in the multi-step onboarding flow with offset-based animation. Key features: (1) Offset property defining element position in sequence for animation calculations (2) Progress property (0.0-1.0) triggering visual width updates (3) Theme-aware background and progress colors (inactive/active icon colors) (4) Fixed dimensions (50x5 points) for consistent visual appearance (5) Dynamic width constraint updates based on progress calculation (6) Automatic progress view updates during layout changes. Animation: Calculates progress difference from offset to determine fill percentage with proper bounds clamping. Dependencies: DynamicThemeView, TariCommon, AppTheme. Used by: OnboardingPagerView for creating multiple progress elements in sequence. Layout: Leading-aligned progress view with dynamic width constraint for smooth fill animation. Critical for: Visual feedback during onboarding progression and user guidance through multi-step security setup process. |
| `OnboardingPagerView.swift` | Onboarding progress indicator showing visual page progression through security setup flow. Displays horizontal progress elements with smooth animation. Key features: (1) Dynamic element count configuration during initialization (2) Progress property (0.0-1.0) controlling visual state across all elements (3) UIStackView-based layout with equal distribution and consistent spacing (4) OnboardingPagerElementView integration for individual progress indicators (5) Automatic element creation and offset configuration for proper animation sequencing. Layout: Horizontal stack with 10pt spacing, fill equally distribution. Dependencies: OnboardingPagerElementView, TariCommon framework. Used by: Onboarding flow screens, security setup progression, staged wallet security introduction. Features: Smooth progress animation, scalable element count, consistent visual spacing. Critical for: User guidance through multi-step onboarding process and visual feedback for completion progress. |
| `OnboardingView.swift` | Main onboarding UI view component. Exports: OnboardingView class extending BaseNavigationContentView. Components: Navigation bar with close/next buttons, content view container, onboarding pager with progress indicator. Features: Progress tracking (0-1), customizable content view, navigation callbacks, theme-aware styling. Dependencies: UIKit, TariCommon base classes, OnboardingPagerView component. Used by: App onboarding flow, new user setup screens. Pattern: MVVM view component with progress indication and navigation integration. |
| `OnboardingViewController.swift` | Main onboarding flow controller managing four-page security introduction sequence. Extends SecureViewController with OnboardingView. Exports: OnboardingViewController. Dependencies: UIKit, Combine, PageViewController, OnboardingPageViewController. Used by: app startup flow when first launching. Manages page transitions, progress indicators, and navigation controls. Creates dynamic page view models for each onboarding step and handles footer height calculations for responsive layout. |

##### MobileWallet/Common/Persistant Data/

| File | Description |
|------|-------------|
| `AppKeychainWrapper.swift` | Secure keychain wrapper for sensitive app data storage. Exports: AppKeychainWrapper enum with static properties for backupPassword and dbPassphrase. Functionality: Secure storage/retrieval of backup passwords and database passphrases using iOS Keychain, shared keychain group access, automatic cleanup on nil assignment. Dependencies: TariSettings keychain wrapper. Used by: Wallet backup/restore operations, database encryption, secure credential management. Security considerations: Stores sensitive wallet credentials, uses shared keychain for app extensions. |
| `UserDefaults.swift` | App-wide UserDefaults configuration with grouped storage and cleanup utilities. Exports: GroupUserDefaults, TorManagerUserDefaults enums. Dependencies: UserDefault property wrapper. Defines all persistent storage keys for network settings, wallet config, user preferences, and Tor settings. Supports app group sharing and bulk removal. |

###### MobileWallet/Common/Persistant Data/User Settings/

| File | Description |
|------|-------------|
| `UserSettings.swift` | User preferences data model defining app-wide settings structure. Exports: UserSettings struct, ColorScheme enum (system, light, dark), BLEAdvertisementMode enum (turnedOff, onlyOnForeground, alwaysOn). Properties: name (optional user name), colorScheme (theme preference), bleAdvertismentMode (Bluetooth advertising behavior). Used by: UserSettingsManager, ThemeCoordinator. Provides default configuration with system color scheme and foreground-only BLE advertising. Codable for persistent storage. |
| `UserSettingsManager.swift` | Static manager providing type-safe access to user settings with automatic persistence. Exports: UserSettingsManager enum with name, colorScheme, bleAdvertisementMode properties. Dependencies: GroupUserDefaults, UserSettings. Used by: ThemeCoordinator, settings screens. Handles lazy loading and automatic saving of settings changes. |

##### MobileWallet/Common/Pop-up/

| File | Description |
|------|-------------|
| `PopUpComponentsFactory.swift` | Factory for creating standardized popup UI components. Exports: PopUpComponentsFactory enum with makeHeaderView(), makeContentView(), makeButtonsView() methods. Dependencies: PopUpHeaderView, PopUpDescriptionContentView, PopUpButtonsView. Used by: popup creation throughout app. Provides consistent styling and automatic dismissal handling. |
| `PopUpPresenter+CommonPopUps.swift` | Extension to PopUpPresenter providing standardized popup components and common dialog patterns. Offers high-level popup creation methods for typical app scenarios. Key features: (1) PopUpDialogModel for structured dialog configuration with titles, messages, buttons, and haptic feedback (2) PopUpDialogButtonModel supporting normal, destructive, text, and dimmed button types (3) MessageModel for error and normal message display with customizable close actions (4) BLEDialogType enum for Bluetooth contact sharing workflows with specialized UI (5) QR code dialog creation with customizable headers and additional buttons (6) Address poisoning dialog for security validation with option selection. Methods: show(message:), showMessageWithCloseButton, showQRCodeDialog, showBLEDialog, showAddressPoisoningPopUp. Dependencies: TariPopUp, PopUpComponentsFactory, localization system. Used by: Throughout app for user notifications, confirmations, QR code display, BLE operations. Provides: Consistent popup styling, haptic feedback integration, and standardized button behaviors across the application. |
| `PopUpPresenter.swift` | Central modal presentation system using SwiftEntryKit for app-wide popup management. Provides consistent bottom-float popup display with configurable duration, dismissal behavior, and haptic feedback. Key features: background overlay, touch handling, tag-based management, keyboard dismissal. Uses TariPopUp container and supports success/error/none haptic types. Dependencies: SwiftEntryKit, TariPopUp, UIApplication. |
| `PopUpTag.swift` | Popup identification system providing unique tags for popup management and dismissal operations. Enables targeted popup control in complex UI scenarios. Key features: (1) String-based enum for type-safe popup identification (2) bleScanContactSharingDialog tag for Bluetooth contact sharing workflows (3) bleScanTransactionDataDialog tag for Bluetooth transaction data operations (4) Raw string values for integration with popup presentation system. Usage: Allows selective popup dismissal, prevents popup conflicts, and enables proper popup state management. Dependencies: None (pure Swift enum). Used by: PopUpPresenter system, BLE dialog management, popup lifecycle control. Integration: Works with PopUpPresenter.dismissPopup(tag:) for targeted dismissal operations. Purpose: Ensures proper popup management in scenarios where multiple popups might be active, particularly for Bluetooth operations that require specific dismissal behavior and state coordination. |
| `TariPopUp.swift` | Custom popup view container with secure content wrapper and rounded background. Extends DynamicThemeView. Exports: TariPopUp class. Dependencies: TariCommon, SecureWrapperView. Used by: PopUpPresenter. Manages header, content, and button sections with responsive layout and security features. |

###### MobileWallet/Common/Pop-up/Components/

| File | Description |
|------|-------------|
| `CustomDeeplinkPopUpContentView.swift` | Specialized popup content view for displaying custom deep link actions, specifically for base node addition confirmations. Features expandable/collapsible design with name and peer address display, show/hide details functionality, and smooth animations. Used by PopUpPresenter for deep link validation workflows. Integrates with TariCommon theme system and provides localized content for base node overlay operations. |
| `PopUpAddressPoisoningContentView.swift` | Comprehensive address poisoning prevention popup for contact disambiguation. Features selectable contact list with emoji IDs, transaction history, trust indicators, and confirmation checkbox. Implements PopUpAddressPoisoningContentCell for individual contact display with trust shields and transaction counts. Used for security validation when multiple similar contacts exist, preventing address poisoning attacks through visual contact verification. |
| `PopUpButtonsTableView.swift` | Table-based button selection component for popup interfaces. Features customizable button options with optional footer text, row selection callbacks, and theme-aware styling. Implements PopUpButtonCell for individual button rendering with centered text and proper spacing. Used for presenting multiple action choices within popups like settings selection and confirmation dialogs. |
| `PopUpButtonsView.swift` | Popup buttons container managing vertical stack of styled action buttons. Exports: PopUpButtonsView class, addButton() method. Dependencies: TariCommon, StylisedButton. Used by: popup dialogs, confirmation screens. Supports multiple button types: normal, destructive, text, and dimmed text with appropriate styling. |
| `PopUpCircleImageHeaderView.swift` | Circular icon header component for popup presentations. Features centered circular background with icon, title text below, and configurable tint colors (purple, red). Provides visual hierarchy for popup headers with consistent spacing, shadow effects, and theme integration. Used for status dialogs, confirmation popups, and informational overlays requiring visual emphasis. |
| `PopUpDescriptionContentView.swift` | Popup content component displaying descriptive text with rich text formatting support. Provides standardized message display for popup dialogs with theme integration. Key features: (1) StylizedLabel for rich text rendering with mixed font weights and styles (2) Poppins font family integration (Medium 12pt normal, Bold 14pt emphasis) (3) Center text alignment for balanced popup appearance (4) Multi-line text support with automatic line wrapping (5) Theme-aware text color using Text.primary for consistent appearance (6) Full constraint layout filling parent container. Typography: Supports normal and bold text mixing with space separators for stylized content. Dependencies: DynamicThemeView, StylizedLabel, TariCommon, AppTheme. Used by: PopUp system for message content, dialog descriptions, informational text display. Configuration: Exposed label property allows external text content and styling setup. Critical for: User communication, popup dialog content display, and consistent text presentation throughout the popup system interface. |
| `PopUpHeaderView.swift` | Simple popup header component featuring StylizedLabel for text presentation. Provides centered, multi-line text display with consistent typography and theme integration. Uses SemiBold Poppins font with customizable separator handling. Essential building block for popup title presentation throughout the app's modal interfaces and informational dialogs. |
| `PopUpHeaderWithSubtitle.swift` | Two-tier popup header component featuring title and subtitle labels. Provides hierarchical text presentation with proper typography scaling (Light 18pt title, Medium 14pt subtitle) and vertical spacing. Used for popup dialogs requiring additional context or explanatory text below the main title. Integrates with dynamic theming system for consistent appearance. |
| `PopUpImageHeaderView.swift` | Image-centered popup header component with configurable image height and title text. Features aspect-fit image scaling, Theme.shared font integration, and customizable image dimensions. Used for popup presentations requiring visual illustration above descriptive text. Provides centered layout with consistent spacing between image and label elements. |
| `PopUpProfileShareContentView.swift` | Profile sharing popup content with link and Bluetooth Low Energy sharing options. Features network-aware messaging, RoundedLabeledButton components for sharing methods, and callback handlers for user interaction. Integrates with NetworkManager for dynamic ticker symbol display. Used for contact/profile sharing workflows with multiple transmission options including deep links and BLE proximity sharing. |
| `PopUpQRContentView.swift` | QR code display popup component with loading states and configurable padding. Features QRCodeView integration with square aspect ratio constraints, Lottie loading animations, and shadow effects. Automatically manages view states based on QR code availability, providing loading indication when QR data is unavailable. Used for address sharing, deep link generation, and contact exchange interfaces. |
| `PopUpSelectionView.swift` | Single-selection popup component for option lists with checkmark indicators. Features PopUpSelectionCell with tick icons, selected index tracking, and automatic visual state management. Implements table view data source pattern for dynamic option lists with immediate selection feedback. Used for settings selection, network choice, and other single-choice popup interfaces throughout the app. |
| `PopUpStagedWalletSecurityHeaderView.swift` | Specialized header component for staged wallet security onboarding popups. Features large title text, subtitle description, and help button with question mark icon. Used in progressive security feature introduction workflow, providing contextual help access and clear visual hierarchy. Integrates with theme system and supports custom title/subtitle configuration for different security stages. |
| `PopUpTableView.swift` | Specialized UITableView subclass optimized for popup presentation with auto-sizing behavior. Features dynamic height constraints based on content size, custom separator styling, and row selection callbacks. Extends DynamicThemeTableView for consistent theming. Used as foundation for all table-based popup components, ensuring proper sizing within modal presentations. |

###### MobileWallet/Common/Pop-up/Components/PopUpAddressDetailsContentView/

| File | Description |
|------|-------------|
| `PopUpAddressDetailsContentSectionView.swift` | Generic popup section container component using Swift generics to wrap any UIView with a titled section layout. Features rounded background styling, consistent padding, and dynamic theme integration. Used as building block for complex popup layouts requiring multiple information sections. Provides reusable section pattern for PopUpAddressDetailsContentView and other detail popups with title-content structure. |
| `PopUpAddressDetailsContentView.swift` | Comprehensive address details popup displaying Tari address components including network, features, view key, core address, and checksum information. Features scrollable content with sectioned layout, copy functionality for both raw and emoji address formats, and conditional view key display. Integrates with TariCommon theming and provides detailed address breakdown for user understanding and verification purposes. |
| `PopUpAddressViewDoubleLabel.swift` | Simple dual-label view component for displaying label-value pairs in address details popup. Features leading and trailing labels with horizontal stack layout, responsive typography, and theme integration. Used within PopUpAddressDetailsContentSectionView for network and features information display. Provides consistent formatting for key-value content presentation in popup contexts. |

###### MobileWallet/Common/Pop-up/Handlers/

| File | Description |
|------|-------------|
| `ScreenshotPopUpHandler.swift` | System-wide screenshot detection and security popup handler using Combine framework. Monitors UIApplication.userDidTakeScreenshotNotification and displays security warnings when screenshots are disabled. Features view controller filtering for sensitive screens and navigation to screenshot settings. Singleton pattern ensures consistent security policy enforcement across the application. |

##### MobileWallet/Common/Property Wrappers/

| File | Description |
|------|-------------|
| `UserDefault.swift` | Property wrapper for type-safe UserDefaults storage with Codable support. Exports: UserDefault property wrapper. Uses JSONEncoder/Decoder for object serialization. Used throughout app for persistent settings storage. Supports optional suite names for grouped preferences. |

##### MobileWallet/Common/Protocols/

| File | Description |
|------|-------------|
| `AddressPoisoningDataHandler.swift` | Address poisoning protection system preventing malicious address substitution attacks with improved address comparison. Detects similar-looking addresses during transaction creation, shows disambiguation dialog when multiple similar addresses exist, manages trusted address whitelist via GroupUserDefaults. Integrates with AddressPoisoningManager for address similarity analysis and provides user confirmation flow for address selection using TariAddressComponents equality instead of unique identifier comparison. Critical security feature protecting users from address-based attacks where malicious actors create visually similar addresses to steal funds. Handles address trust management and popup presentations for user address verification. |
| `ViewIdentifiable.swift` | Protocol and extensions providing type-safe cell registration and dequeuing for UITableView and UICollectionView. Automatically generates string identifiers from class names, eliminating hardcoded identifier strings and reducing runtime errors. Extends UITableViewCell, UICollectionViewCell, and UITableViewHeaderFooterView with convenient registration and dequeue methods. Essential utility for maintaining clean, type-safe collection view patterns throughout the app. |

##### MobileWallet/Common/Theme/

| File | Description |
|------|-------------|
| `AppTheme.swift` | Comprehensive theme system defining light and dark color schemes throughout the app. Exports: AppTheme struct, Colors struct with nested color categories. Provides consistent brand colors, backgrounds, text, icons, buttons, and system colors. Static light and dark theme configurations with proper semantic color mapping. |
| `DynamicThemeView.swift` | Dynamic theme system providing automatic theme updates for UI components. Implements ThemeViewProtocol for consistent theming across all UI elements. Key components: (1) DynamicThemeView, DynamicThemeTabBar, DynamicThemeTableView, DynamicThemeCell, etc. - theme-aware base classes (2) ThemeViewManager handling theme state, enforcement, and Combine-based updates (3) ThemeViewProtocol defining theme interface with automatic callback setup (4) Animated theme transitions with 0.3s duration for smooth visual updates (5) Enforced theme capability for overriding global theme settings (6) Initial vs. ongoing update differentiation for optimal performance. Classes provided: View, TabBar, TableView, CollectionCell, Cell, HeaderFooterView, BaseButton, Toolbar, TextField, TextView, ViewController. Dependencies: ThemeCoordinator, AppTheme, Combine framework. Used by: All themed UI components throughout the app. Features: Automatic theme subscription, animated transitions, and flexible theme enforcement for specific components or screens. |
| `ThemeCoordinator.swift` | Centralized theme management coordinator providing dynamic theme switching with system integration. Manages light/dark/system color schemes with real-time updates and Yat SDK integration. Key responsibilities: publishes AppTheme changes for UI components, synchronizes with iOS system dark mode, manages user preference persistence through UserSettingsManager, updates UIApplication interface style, integrates with YatLib theming, handles TariWindow callbacks for UI updates. Color schemes: .system (follows iOS), .light (always light), .dark (always dark). Dependencies: AppTheme (theme definitions), YatLib (external theming), TariWindow (UI callbacks), UserSettingsManager (persistence), UIApplication (system integration). Critical for consistent theming across the app and third-party integrations. |

##### MobileWallet/Common/Toasts/

| File | Description |
|------|-------------|
| `SuccessToast.swift` | Simple success toast notification view displaying positive feedback messages. Features centered white text on themed background with rounded corners and padding. Used by ToastPresenter for temporary success confirmations like copy operations, transaction completion, and settings updates. Integrates with Theme.shared fonts for consistent typography across feedback popups. |
| `ToastPresenter.swift` | Static toast notification presenter using SwiftEntryKit for temporary success messages. Features purple-branded styling, haptic feedback, configurable duration, and automatic keyboard dismissal. Provides centralized success toast functionality with consistent appearance and logging integration. Used throughout app for positive user feedback on successful actions like copying addresses and completing transactions. |

##### MobileWallet/Common/Tools/

| File | Description |
|------|-------------|
| `AddressViewDefaultActions.swift` | Utility enum providing standardized address detail popup actions for address views. Features address component breakdown display, copy functionality for both raw and emoji formats, and complete popup presentation workflow. Integrates PopUpAddressDetailsContentView with header, content, and button sections. Used by address views throughout the app to provide consistent address information and copy capabilities. |
| `CommonActions.swift` | Utility class providing common wallet operations and cleanup actions. Contains static methods for wallet lifecycle management. **Key exports:** deleteWalletAndMoveToSplashScreen() for complete wallet deletion and cleanup. **Dependencies:** Tari.shared, BackupManager, UserManager, NotificationManager, AppRouter. **Usage:** Called when user needs to reset wallet, recover from paper wallet, or during error recovery. Handles automatic reconnection disabling, backup cleanup, access token clearing, and navigation to splash screen with optional paper wallet recovery data. |
| `VersionValidator.swift` | Semantic version comparison utility for wallet database version validation. Implements Version struct conforming to Comparable protocol with support for semantic versioning (major.minor.patch) and pre-release suffixes (pre, rc). Provides static compare method for version compatibility checking with suffix priority ordering (pre < rc < release). Handles variable-length version components with zero-padding for comparison. Essential for MigrationManager to determine wallet database compatibility and prevent data corruption from version mismatches. Used for validating minimum required wallet versions against current database version. |

##### MobileWallet/Common/View Controllers/

| File | Description |
|------|-------------|
| `OverlayPresentable.swift` | Protocol extension providing standardized overlay presentation functionality for view controllers. Implements show and hide overlay methods with consistent modal presentation style (overFullScreen with crossDissolve transition). Ensures proper overlay management by checking for existing presented view controllers and providing unified overlay presentation patterns. Essential for consistent modal presentation throughout the app including popups, alerts, and temporary UI overlays. Used by controllers requiring overlay functionality like splash screens, authentication prompts, and modal dialogs. Simple but critical for maintaining consistent user experience across modal presentations. |
| `PageViewController.swift` | Generic UIPageViewController wrapper providing horizontal paging functionality with real-time progress tracking. **Key exports:** PageViewController class with move(toIndex:) method and @Published pageIndex property. **Dependencies:** UIKit. **Features:** Programmatic page navigation, scroll gesture handling, smooth animation with completion callbacks, published page index for binding to progress indicators. **Usage:** Used throughout the app for onboarding flows, settings screens, and multi-step processes requiring horizontal navigation. |

##### MobileWallet/Common/Views/

| File | Description |
|------|-------------|
| `AmountBadge.swift` | Themed UI component displaying transaction amounts with color-coded status indicators. Provides visual feedback for different transaction states and value types. Key features: (1) ValueType enum defining four states (positive/green, negative/red, waiting/yellow, invalidated/grey) (2) ViewModel structure with amount text and value type for clean data binding (3) Dynamic theme integration with automatic color updates (4) Compact design with 3pt corner radius and 5pt padding (5) Poppins Black font at 12pt for consistent typography (6) Content hugging and compression resistance for proper layout behavior. Color mapping: Positive (light green bg/green text), negative (light red bg/red text), waiting (light yellow bg/yellow text), invalidated (secondary bg/light text). Dependencies: DynamicThemeView, TariCommon, AppTheme. Used by: Transaction lists, amount displays, balance indicators throughout the app. Updates: Responds to both theme changes and view model updates for consistent appearance across different contexts. |
| `BaseToolbar.swift` | Base toolbar component with dynamic background transparency and theme support. **Key exports:** BaseToolbar class with backgroundAlpha property. **Dependencies:** DynamicThemeView. **Features:** Configurable background alpha blending, automatic theme color application, real-time transparency updates. **Usage:** Base class for app toolbars requiring dynamic opacity effects, particularly for overlays and navigation bars that fade in/out based on scroll position or user interaction. |
| `ContactSearchView.swift` | Specialized search input field for contact/address entry with QR code scanning support and preview functionality. **Key exports:** ContactSearchView class with textField, qrButton properties and preview capabilities. **Dependencies:** TariCommon, PulseButton, ScrollableLabel. **Features:** Built-in QR code button, Yat logo support, scrollable address preview, theme-aware styling with border and background. **Usage:** Used in send transaction flow and contact management for entering recipient addresses, with integrated QR scanning and address validation preview. |
| `ContentScrollView.swift` | Pre-configured UIScrollView with embedded content view and automatic constraint setup. **Key exports:** ContentScrollView class with contentView property. **Dependencies:** UIKit. **Features:** Automatic content view setup with proper scroll constraints, low-priority height constraint for flexible sizing, no additional configuration required. **Usage:** Base component for scrollable content areas throughout the app, providing consistent scroll behavior and content management with automatic layout handling. |
| `FormTextField.swift` | Form input field with emoji support and Combine integration. Extends DynamicThemeView. Exports: FormTextField class with text publisher. Dependencies: TariCommon, EmojiTextField, Combine. Used by: contact forms, send transaction forms. Features custom separator line and return key handling. |
| `KeyboardAvoidingContentView.swift` | Smart container view that automatically adjusts layout when keyboard appears/disappears. **Key exports:** KeyboardAvoidingContentView class with contentView property. **Dependencies:** UIKit, Combine, ContentScrollView, DynamicThemeView. **Features:** Automatic keyboard notification handling, smooth animated layout adjustments, embedded scroll view with content management, Combine-based reactive updates. **Usage:** Used in forms and input screens to prevent keyboard overlap, ensuring content remains visible and accessible during text input. |
| `LoadingImageView.swift` | Dual-state view component that switches between loading animation and image display with smooth transitions. **Key exports:** LoadingImageView class with State enum (.loading/.image). **Dependencies:** TariCommon, Lottie framework. **Features:** Lottie loading animation (pending circle), smooth alpha transitions, automatic state management, scale-to-fit image display. **Usage:** Used in transaction screens, contact displays, and any UI requiring loading states before showing dynamic content like contact avatars or transaction attachments. |
| `RoundedGlassContentView.swift` | Generic container view with glass morphism styling and configurable content. **Key exports:** RoundedGlassContentView<Subview> with borderWidth property. **Dependencies:** TariCommon. **Features:** Semi-transparent white background (40% opacity), white border (60% opacity), automatic circular corner radius, generic subview type support, configurable border width. **Usage:** Used for overlay content, modal backgrounds, and special UI elements requiring glass/frosted glass effects with contained child views. |
| `ScrollableLabel.swift` | Theme-aware label with horizontal scrolling capability and gradient edge masking. **Key exports:** ScrollableLabel class with label and scrollView properties, configurable margin. **Dependencies:** TariCommon. **Features:** Horizontal scroll for long text, gradient fade mask at edges, Poppins SemiBold font, configurable margins, automatic scroll indicator hiding. **Usage:** Used for displaying long addresses, transaction IDs, and other text that may exceed available width while maintaining visual consistency with fade-out edges. |
| `SearchField.swift` | Custom search text field with magnifying glass icon and themed styling. Extends DynamicThemeTextField. Exports: SearchField class. Dependencies: TariCommon. Used by: contact book, transaction search, any searchable lists. Features rounded corners, custom icon placement, and theme-aware styling. |
| `SearchTextField.swift` | Themed search text field with integrated search icon and consistent styling. Extends DynamicThemeTextField with search-specific visual design and layout. Key features: (1) Fixed 46pt height with 6pt corner radius for consistent appearance (2) Search icon positioned in right view with centered alignment (3) Left padding view (20pt) for balanced text input positioning (4) Right padding view (44pt) containing search icon with proper spacing (5) Poppins Medium 14pt font for text input consistency (6) Theme-aware styling (background, text, border, icon tint colors) (7) 1pt border width with theme-based border color. Visual design: Primary background, heading text color, link-colored search icon, tertiary border. Dependencies: DynamicThemeTextField, Theme system for search icon. Used by: Contact search, transaction filtering, any search functionality throughout the app. Icon: 16x16pt search icon with scale aspect fill content mode. Critical for: Search interfaces, user input collection, and consistent search experience across the application. |
| `SelectableCell.swift` | Theme-aware table view cell for selection lists with tick indicator. **Key exports:** SelectableCell class with ViewModel struct containing id, title, isSelected properties. **Dependencies:** TariCommon, DynamicThemeCell. **Features:** UUID-based identification, tick mark visibility control, consistent 63px height, theme-responsive colors, no selection styling. **Usage:** Used in settings screens, base node selection, network selection, and other option lists requiring single or multiple selection with visual feedback. |
| `TickButton.swift` | Checkbox-style button with tick icon for selection states. Extends DynamicThemeBaseButton. Exports: TickButton class. Shows tick icon when selected, empty border when unselected. Used by: settings toggles, selection lists, confirmation dialogs. Non-interactive button controlled programmatically. |

###### MobileWallet/Common/Views/Address View/

| File | Description |
|------|-------------|
| `AddressView.swift` | Reusable address display component with truncation and details button. Extends DynamicThemeView. Exports: AddressView class, TextType enum, ViewModel struct. Dependencies: TariCommon. Used by: transaction screens, contact screens. Supports compact/full display modes and optional prefix/suffix truncation. |
| `RoundedAddressView.swift` | Theme-aware view wrapper for displaying wallet addresses with rounded background styling. **Key exports:** RoundedAddressView class with isCompact property and onViewDetailsButtonTap callback. **Dependencies:** TariCommon framework, AddressView. **Features:** 10px corner radius container, theme-responsive background, compact/expanded display modes, address details button handling. **Usage:** Used in transaction screens, contact displays, and wallet address sharing to provide consistent address presentation with visual hierarchy. |

###### MobileWallet/Common/Views/Secure View/

| File | Description |
|------|-------------|
| `SecureViewController.swift` | Generic secure view controller base class providing screenshot prevention and privacy protection features. Wraps any UIView subtype in security-enhanced container. Key features: (1) Generic MainView type constraint allowing type-safe view access (2) SecureWrapperView integration for automatic privacy protection (3) Screenshot prevention status management with configurable prevention levels (4) Automatic security wrapper application during view loading (5) Type-safe mainView property providing direct access to wrapped view content. Security features: Screen recording detection, background content hiding, screenshot prevention. Dependencies: SecureWrapperView, ScreenshotPreventionStatus. Used by: Security-sensitive screens throughout the app including wallet balance, transaction details, seed phrase display. Inheritance: Base class for DynamicThemeViewController and other secure screen implementations. Critical for: Privacy protection, sensitive data display, and security compliance across the application. |
| `SecureWrapperView.swift` | Advanced privacy protection view wrapper using iOS secure text entry mechanism for screenshot prevention. Wraps any UIView subtype in security-enhanced container with configurable protection levels. Key features: (1) ScreenshotPreventionStatus enum (enforced, turnedOff, automatic) for flexible security control (2) Generic MainView constraint enabling type-safe wrapper for any UIView subclass (3) Hidden UITextField with secure text entry providing the security layer (4) CanvasView extraction for iOS screen recording protection (5) SecurityManager integration for global security settings (6) Combine-based reactive security status updates (7) iOS version compatibility handling (iOS 16+ optimizations). Security mechanisms: Leverages iOS secure text entry to prevent screenshots and screen recording. Dependencies: TariCommon, Combine, SecurityManager, Logger. Used by: SecureViewController and all security-sensitive screens (wallet balance, seed phrases, transaction details). Configuration: Automatic mode follows global settings, manual modes override for specific screens. Critical for: Privacy protection, regulatory compliance, and sensitive data display security throughout the application. |

#### MobileWallet/Fonts/

| File | Description |
|------|-------------|
| `fontExporter.sh` | [SCRIPT] Font processing utility script for converting OTF fonts to editable XML and back. Contains commented commands for ftxdumperfuser tool. Used for: font modification and generation during development. Processes all .otf files in current directory. |

###### MobileWallet/Libraries/TariLib/Core/

| File | Description |
|------|-------------|
| `FFIWalletHandler.swift` | Core FFI bridge handler providing direct interface to Rust wallet library through C FFI calls. Manages wallet lifecycle (connect/disconnect), publishes wallet running status, and wraps all wallet operations with comprehensive error handling. Key operations: wallet connection with CommsConfig and logging setup, balance and transaction queries (completed/pending/cancelled), contact management (add/remove/list), transaction operations (send/cancel/validate), UTXO management (list/split/join), fee estimation and network stats, base node configuration, wallet recovery with enhanced logging, message signing, seed phrase access, key-value storage, payment reference lookup. Enhanced transaction sending without message parameter, standardized error checking with helper method, and payment reference retrieval for transaction metadata. All methods use standardized error handling with error code pointers and throw WalletError exceptions. Critical for safely bridging Swift UI layer to Rust blockchain core while maintaining type safety and proper memory management. Includes background task management for safe wallet disconnection during app backgrounding. Dependencies: Rust FFI functions, PointerHandler, WalletError, various wrapper types (Balance, Transactions, Contacts, PaymentReference, etc.). |
| `Tari.swift` | Core Tari wallet manager singleton orchestrating all wallet operations, Tor connectivity, and app lifecycle. Central coordinator managing wallet containers, Tor connection status, and automated connection/disconnection based on app state. Key responsibilities: maintains WalletContainer instances per network, manages Tor connectivity through TorManager with bootstrap progress tracking, handles secure passphrase generation and storage via keychain, provides wallet lifecycle management (start/stop/restore/delete), implements automatic reconnection on foreground entry, manages log file creation with timestamps, coordinates network switching between mainnet/testnet, provides published properties for UI binding (torConnectionStatus, torBootstrapProcess, torError). Enhanced with static mainWallet accessor for convenient access to main network wallet instance. Dependencies: TorManager (network privacy), WalletContainer (wallet instances), AppKeychainWrapper (secure storage), NetworkManager (network selection), UIKit (app state). Exports: shared singleton instance, mainWallet static accessor, wallet access methods, connection management, error handling. Critical foundation class that all wallet operations depend on for Tor-routed blockchain connectivity and wallet management. |
| `TorManager.swift` | Complete Tor network management for privacy-first cryptocurrency operations. Handles Tor daemon lifecycle (connect/disconnect actions), bridge configuration for censorship circumvention, IPv6 connectivity testing via IPtProxy, and connection status monitoring. Integrates with Tor library for onion routing and Combine framework for reactive state updates. Enhanced with improved async/await sleep handling for better thread management. Provides network anonymization layer ensuring all wallet communications are routed through Tor network. Supports custom bridge configuration, connection retry logic, and network status reporting. Dependencies: Tor, Combine, IPtProxy. Essential component for wallet privacy and network security with optimized connection handling. |

###### MobileWallet/Libraries/TariLib/Core/App Setup/

| File | Description |
|------|-------------|
| `AppConfigurator.swift` | Central application configuration coordinator managing system-wide initialization. Configures environment-specific logging (Console, File, Crash loggers), initializes core managers (BackupManager, DataFlowManager, LocalNotificationsManager, ScreenshotPopUpHandler), and sets up reactive callbacks for Tor error handling. Provides crash logging control interface and handles Tor connection errors with user feedback via ToastPresenter. Singleton pattern ensuring consistent app-wide configuration and error handling strategies. |

###### MobileWallet/Libraries/TariLib/Core/Data Models/

| File | Description |
|------|-------------|
| `MessageMetadata.swift` | Cryptographic message metadata container for digital signatures and authentication. Contains hex-encoded signature data and cryptographic nonce for message verification. Used in secure communication protocols, push notification authentication, and wallet message signing operations. Essential for NotificationManager's cryptographically signed push notifications and secure API communications. Simple but critical data structure ensuring message integrity and authenticity in blockchain and external service communications. |
| `WalletBalance.swift` | Core wallet balance data model representing all balance categories in Tari wallet. Defines available (spendable), incoming (pending receipts), outgoing (pending sends), and timeLocked (mining rewards) balance components as UInt64 values. Provides static zero balance for initialization and total computed property combining available, incoming, and timeLocked funds. Essential data structure used throughout the app for balance display, transaction validation, and spending calculations. Conforms to Equatable for comparison operations and reactive UI updates via TariBalanceService. |

###### MobileWallet/Libraries/TariLib/Core/FFI/

| File | Description |
|------|-------------|
| `Balance.swift` | FFI wrapper for wallet balance operations from Rust core. Exports: Balance class with available, incoming, outgoing, timelocked properties. Dependencies: Rust FFI functions (balance_get_*). Used by: wallet balance display throughout app. Manages opaque pointer to Rust balance object with automatic cleanup. |
| `ByteVector.swift` | Low-level byte array wrapper providing safe access to binary data through FFI interface. Handles raw byte operations for cryptographic data, addresses, and binary content. Key features: (1) FFI pointer-based byte vector management with automatic cleanup (2) Data constructor converting Swift Data to FFI byte vector with error handling (3) Individual byte access by index with bounds checking and error propagation (4) Count property providing vector length with error handling (5) Uppercase hex string conversion for debugging and display purposes (6) Byte array extraction for Swift interoperability (7) Data property for seamless Swift Data integration. Conversions: Supports uppercase hex string output, byte array access, and Data conversion. Dependencies: Foundation Data, PointerHandler, WalletError. Used by: Address operations, cryptographic key handling, transaction data, Tor cookie management. Extensions: Convenient hex, bytes, and data properties for various data format needs. Critical for: All binary data operations, cryptographic operations, and data format conversions throughout the Tari library. |
| `RestoreWalletStatus.swift` | Enumeration representing different states of wallet restoration process from seed phrase. **Key exports:** RestoreWalletStatus enum with cases for connection, progress, completion, and failure states. **Dependencies:** None (native Swift). **Features:** Status code mapping from FFI, progress tracking with UTXO counts, retry attempt tracking, custom equality comparison. **Usage:** Used during wallet recovery flow to provide real-time feedback to users about restoration progress, connection status, and error handling. |
| `TariEmojis.swift` | FFI wrapper for emoji address encoding/decoding functionality from Rust core. Manages emoji set collection with automatic memory cleanup, provides access to individual emojis by index, and offers convenient all property for complete emoji list retrieval. Used for converting Tari addresses to/from human-readable emoji format, essential for user-friendly address display and QR code generation. |
| `TariFeePerGramStats.swift` | FFI wrapper for transaction fee statistics and estimation from network analysis. **Key exports:** TariFeePerGramStats class with fee calculation methods (min, avg, max fee per gram). **Dependencies:** Core FFI functions (fee_per_gram_stats_*), WalletError. **Features:** Statistical fee analysis, indexed access to fee data, order-based fee tiers, automatic memory management. **Usage:** Used in transaction creation flow to provide users with fee estimation options (slow, normal, fast) based on current network conditions. |
| `TariVectorWrapper.swift` | FFI wrapper for managing dynamic arrays of various Tari data types from Rust core. **Key exports:** TariVectorWrapper class with add() methods and UnsafeMutablePointer extension for array conversion. **Dependencies:** Core FFI functions (create_tari_vector, tari_vector_push_string, destroy_tari_vector), TariTypeTag, WalletError. **Features:** Type-safe vector creation, string commitment addition, batch operations, automatic memory management, array conversion utilities. **Usage:** Used for passing collections of data (commitments, keys, addresses) between Swift and Rust FFI layers. |
| `TransactionValidationData.swift` | Simple data structure for transaction validation results. **Key exports:** TransactionValidationData struct with identifier and status properties. **Dependencies:** TransactionValidationStatus enum. **Features:** Transaction identification with validation status pairing. **Usage:** Used for communicating validation results between Rust FFI layer and Swift UI components, particularly during transaction verification and processing. |
| `TransactionValidationStatus.swift` | Enumeration representing the result of transaction validation operations. **Key exports:** TransactionValidationStatus enum with success, alreadyBusy, internalFailure, communicationFailure cases. **Dependencies:** None (native Swift). **Features:** UInt64-based raw values for FFI compatibility. **Usage:** Used to communicate transaction validation results from Rust core to Swift UI, helping determine next steps in transaction processing workflows. |
| `Wallet.swift` | Core FFI wrapper for Rust wallet implementation providing comprehensive Tari blockchain operations. Manages low-level wallet functionality including transaction processing, balance management, contact operations, and network communication. Key features: extensive callback system for real-time events (received transactions, mining confirmations, network status), secure initialization with comms config and seed words, comprehensive transaction lifecycle management, UTXO operations (split, join, selection), contact and address management, fee estimation and network connectivity. Critical callbacks: receivedTransaction, transactionBroadcast, transactionMined, connectionStatus, balanceUpdated. Dependencies: Rust FFI functions, CommsConfig, SeedWords, WalletCallbacks. Core component bridging Swift UI layer with Rust blockchain implementation, ensuring type-safe wallet operations and real-time blockchain event handling. |
| `WalletCallbacks.swift` | Reactive callback system bridging Rust FFI wallet events to Swift Combine publishers. Provides type-safe event handling for all wallet operations and state changes. Key features: (1) WalletCallbacksReadable protocol defining 16 event publishers for different wallet events (2) PassthroughSubject instances for each event type enabling immediate event propagation (3) Dedicated callbacks queue (com.tari.events) for thread-safe event processing (4) Transaction lifecycle events (received, reply, finalized, broadcast, mined, cancelled) (5) Validation events (output validation, transaction validation) for security checking (6) Network events (connectivity status, scanned height, base node state) (7) Recovery events for wallet restoration operations. Event types: Transaction events, balance updates, connectivity status, validation data, recovery status. Dependencies: Combine framework, transaction and validation data types. Used by: WalletContainer, all wallet services, UI components needing real-time wallet updates. Thread safety: All publishers receive events on dedicated queue. Critical for: Real-time wallet state updates and event-driven UI updates throughout the application. |
| `WalletContainer.swift` | High-level wallet service container implementing WalletInteractable protocol. Orchestrates all wallet operations and manages service dependencies. Key components: (1) WalletInteractable protocol defining wallet interface with address, version, and service access (2) WalletConnectionCallbacks publishing connection status, scan height, and block height updates (3) WalletContainer managing 11 specialized services (connection, contacts, fees, transactions, etc.) (4) Configuration for wallet database, networking, and Tor integration (5) Wallet lifecycle management (start/stop) with seed word handling (6) Directory and database file management with network-specific paths. Dependencies: FFIWalletHandler, TariLib services, NetworkManager, TariSettings. Used by: Main app wallet management and all transaction operations. Service delegation: Routes operations to specialized service classes while maintaining centralized configuration and state management. Critical for: Complete wallet functionality and service coordination. |

###### MobileWallet/Libraries/TariLib/Core/FFI/Base Node/

| File | Description |
|------|-------------|
| `BaseNodeConnectivityStatus.swift` | FFI enum representing base node connection states from Rust core. Exports: BaseNodeConnectivityStatus enum (connecting, online, offline). Raw value UInt64 matching Rust enum values. Used by: network status indicators, connection managers. Maps FFI values to Swift enum cases for type safety. |
| `BaseNodeState.swift` | FFI wrapper for base node connectivity state information from Rust core. Provides access to blockchain height data through heightOfTheLongestChain property with proper error handling and pointer management. Used for network synchronization status monitoring and blockchain height validation. Essential for determining wallet sync progress and connection health. |

###### MobileWallet/Libraries/TariLib/Core/FFI/Configs/

| File | Description |
|------|-------------|
| `CommsConfig.swift` | Communications configuration wrapper for wallet networking setup through FFI interface. Defines essential networking parameters for wallet connectivity and peer discovery. Key features: (1) Public address configuration for wallet accessibility (2) TransportConfig integration for underlying network transport (Tor) (3) Database configuration (name and folder path) for local data storage (4) Discovery timeout settings for peer discovery operations (5) SAF (Store and Forward) message duration configuration (6) Error handling for configuration validation and creation. Configuration parameters: Public address, transport config, database settings, timeouts, message duration. Dependencies: TransportConfig, PointerHandler, WalletError. Used by: WalletContainer during wallet initialization and startup. Network integration: Works with Tor transport layer for privacy-focused networking. Critical for: Wallet network connectivity, peer discovery, and secure communications setup. Automatic cleanup: Properly destroys FFI pointers during deinitialization. |
| `TransportConfig.swift` | FFI wrapper for Tor transport configuration in Tari wallet connections. **Key exports:** TransportConfig class with Tor-specific initialization. **Dependencies:** Core FFI functions (transport_tor_create, transport_type_destroy), ByteVector, WalletError. **Features:** Tor control server configuration, port and authentication setup, SOCKS proxy credentials, automatic memory management. **Usage:** Essential for establishing secure Tor connections to Tari network, used during wallet initialization and base node connectivity setup. |

###### MobileWallet/Libraries/TariLib/Core/FFI/Contacts/

| File | Description |
|------|-------------|
| `Contact.swift` | FFI wrapper for individual contact management providing core contact data access and manipulation. Represents a single contact entry with address, alias, and favorite status. Key features: (1) TariAddress property for contact's wallet address with error-handled FFI calls (2) Alias string property for user-assigned contact name with UTF-8 validation (3) isFavorite boolean property for priority contact marking (4) Pointer-based initialization for FFI integration (5) Convenience initializer for creating new contacts with validation (6) Automatic memory management with proper FFI pointer cleanup. Contact properties: address (TariAddress), alias (String), isFavorite (Bool). Dependencies: TariAddress, PointerHandler, WalletError. Used by: Contact management services, address book functionality, transaction recipient selection. Creation: Supports creation from existing FFI pointer or new contact data with address pointer. Critical for: Contact storage, address book operations, and transaction recipient management throughout the contact system. |
| `Contacts.swift` | FFI wrapper for managing collections of wallet contacts from Rust core. **Key exports:** Contacts class with count property, contact(index:) method, and all computed property. **Dependencies:** Core FFI functions (contacts_get_length, contacts_get_at, contacts_destroy), Contact, WalletError. **Features:** Thread-safe contact enumeration, indexed access, automatic memory cleanup, error handling for all operations. **Usage:** Core component for contact book functionality, provides access to all stored contacts for display, search, and transaction recipient selection. |

###### MobileWallet/Libraries/TariLib/Core/FFI/Errors/

| File | Description |
|------|-------------|
| `BaseNodeConnectionError.swift` | Swift error enumeration for base node connectivity issues. **Key exports:** BaseNodeConnectionError enum with noSelectedBaseNode and noBaseNodeToSwitch cases. **Dependencies:** None (native Swift). **Features:** Specific error cases for base node selection and switching scenarios. **Usage:** Thrown by network management components when base node operations fail, caught by UI components to display appropriate error messages and recovery options to users. |

###### MobileWallet/Libraries/TariLib/Core/FFI/Keys/

| File | Description |
|------|-------------|
| `PublicKey.swift` | Cryptographic public key wrapper providing secure key operations through FFI interface. Handles public key data access, creation, and validation for wallet operations. Key features: (1) ByteVector property for raw key data access with error handling (2) Emoji encoding for user-friendly key representation and display (3) Hex string initialization for key creation from string data (4) Pointer-based initialization for FFI integration (5) Equatable implementation for key comparison operations (6) Automatic memory management with proper FFI cleanup. Key operations: Creation from hex strings, byte vector access, emoji encoding for display. Dependencies: ByteVector, PointerHandler, WalletError. Used by: TariAddress components (view key, spend key), cryptographic operations, key validation. Equality: Compares keys using hex representation of byte vectors. Security: Handles cryptographic material with proper error propagation and memory management. Critical for: Address validation, cryptographic operations, transaction verification, and key management throughout the wallet system. |
| `PublicKeys.swift` | FFI wrapper for managing collections of cryptographic public keys from Rust core. **Key exports:** PublicKeys class with count property, publicKey(index:) method, and all computed property. **Dependencies:** Core FFI functions (public_keys_get_length, public_keys_get_at), PublicKey, WalletError. **Features:** Safe iteration over public key collections, indexed access with bounds checking, automatic memory management, error handling for all operations. **Usage:** Used in transaction validation, contact management, and cryptographic operations requiring multiple public keys. |
| `TariAddress.swift` | Tari blockchain address representation with comprehensive FFI integration. Provides complete address functionality including creation, validation, and component extraction. Key features: (1) Multiple initialization methods (base58, emoji ID, raw pointer) with error handling (2) Address component access (network, features, view key, spend key, checksum) (3) Emoji ID representation for user-friendly address display (4) Network and feature flag decoding with bitmask operations including payment ID support (5) Address validation and format conversion utilities (6) TariAddressComponents integration for structured address data. Supports: One-sided, interactive, and payment ID features with feature detection methods. Dependencies: ByteVector, PublicKey, TariAddressComponents. Used by: Contact management, transaction creation, address validation throughout app. Extensions: Deprecated publicKey getter, utility makeTariAddress function, network name resolution, comprehensive feature flag checking (oneSided, interactive, paymentId). Critical for: All address handling, transaction routing, and contact management operations. |
| `TariAddressComponents.swift` | Data structure for decomposed Tari address components with emoji and Base58 representations. **Key exports:** TariAddressComponents struct with network, features, keys, checksums and various address type flags including payment ID support. **Dependencies:** Base58Swift, TariAddress, emoji conversion extensions. **Features:** Network and feature extraction, Base58 encoding, emoji representation, address type detection (oneSided, interactive, paymentID), formatted display properties, equality comparison supporting payment ID addresses. **Usage:** Used for address validation, display formatting, QR code generation, and transaction recipient analysis throughout the app. **Equality:** Custom equality implementation that compares full raw addresses for payment ID addresses, otherwise uses unique identifier. |

###### MobileWallet/Libraries/TariLib/Core/FFI/Seed Words/

| File | Description |
|------|-------------|
| `SeedWords.swift` | Mnemonic seed phrase management for wallet creation and recovery with comprehensive validation. Handles BIP39-compatible seed word operations through FFI interface. Key features: (1) Multiple initialization methods (from wallet, cipher, empty, word array) with validation (2) PushWordResult enum tracking word addition status (invalid, successful, complete, invalid phrase) (3) Word-by-word building with real-time validation and length checking (4) Comprehensive error handling (invalid word, invalid phrase, length validation) (5) Individual word access by index with bounds checking (6) Automatic pointer management and cleanup. Validation: Checks word validity, phrase completeness, and proper sequence length. Dependencies: FFI wallet operations, PointerHandler, WalletError. Used by: Wallet creation, backup/restore operations, seed phrase entry screens. Extensions: Convenient array-based initialization and complete word list access. Critical for: Wallet security, backup/restore functionality, and cryptographic key derivation across the entire wallet system. |
| `SeedWordsMnemonicWordList.swift` | FFI wrapper for accessing BIP39 mnemonic word lists in different languages. **Key exports:** SeedWordsMnemonicWordList class with Language enum (English/Spanish) and seedWords property. **Dependencies:** Core FFI functions (seed_words_*), WalletError. **Features:** Language-specific word list loading, indexed word access, automatic memory management, convenience initializers. **Usage:** Used during wallet creation and recovery for seed phrase validation, word suggestion, and mnemonic phrase generation in supported languages. |

###### MobileWallet/Libraries/TariLib/Core/FFI/Transactions/

| File | Description |
|------|-------------|
| `PaymentReference.swift` | Payment reference wrapper for transaction payment record data through FFI interface. Provides access to payment reference information including reference string, transaction direction, and block height. Key features: (1) Payment reference string extraction from C hex tuple (2) Direction detection (inbound/outbound) based on FFI direction flag (3) Block height access for transaction confirmation tracking (4) Automatic memory management with FFI pointer cleanup. Dependencies: FFI payment record functions, String hex tuple conversion. Used by: Transaction payment reference lookup, payment tracking, transaction metadata display. Critical for: Payment reference display and transaction payment information throughout the wallet. |
| `PaymentReferences.swift` | Payment references collection wrapper managing multiple payment reference records through FFI interface. Provides indexed access to payment reference data with automatic memory management. Key features: (1) Collection count access with error handling (2) Indexed payment reference retrieval with bounds checking (3) PaymentReference wrapper creation for individual records (4) Automatic FFI pointer cleanup and memory management. Dependencies: PaymentReference, FFI payment records functions, PointerHandler, WalletError. Used by: Transaction payment reference lookup, payment history access, transaction metadata services. Critical for: Managing collections of payment references and transaction payment data throughout the wallet. |
| `Transaction.swift` | Transaction protocol and status enumeration defining the core transaction interface and lifecycle states. Provides unified transaction handling across different transaction types with enhanced payment ID support. Key components: (1) TransactionStatus enum with 16 states covering pending, completed, broadcast, mined, coinbase, and error states (2) Transaction protocol defining common interface (identifier, amount, direction, status, paymentId, timestamp, address, cancellation state, pointer access) (3) Protocol extensions for transaction type checking (one-sided payments, coinbase transactions) (4) Timestamp formatting utilities for relative date display (5) Array extensions for duplicate filtering by transaction identifier (6) Enhanced paymentId method supporting multiple FFI extraction strategies. Transaction states: Tracks complete lifecycle from pending through broadcast, mining, and confirmation. Dependencies: TariAddress, Date extensions, ByteVector. Used by: All transaction-related services, UI components, and transaction history management. Extensions: Provides computed properties for transaction classification, display formatting, and comprehensive payment ID extraction. Critical for: Transaction categorization, status tracking, and unified transaction handling across the app. |
| `TransactionSendResult.swift` | FFI wrapper for transaction sending operation results with detailed status information. **Key exports:** TransactionSendResult class with Status enum (queued, directSend, safSend variants) and success checking methods. **Dependencies:** Core FFI functions (transaction_send_status_*), WalletError. **Features:** Transaction send status decoding, success determination logic, automatic memory management, detailed send method tracking. **Usage:** Used in send transaction flow to provide users with immediate feedback on transaction submission success and delivery method. |

###### MobileWallet/Libraries/TariLib/Core/FFI/Transactions/Completed/

| File | Description |
|------|-------------|
| `CompletedTransaction.swift` | Completed transaction wrapper implementing Transaction protocol for finalized blockchain transactions. Represents fully processed transactions with comprehensive status and metadata access. Key features: (1) RejectionReason enum handling 8 cancellation states (timeout, double spend, orphan, invalid, etc.) (2) Transaction protocol implementation with identifier, amount, fee, paymentId, timestamp access (3) Confirmation count tracking for transaction security verification (4) Source and destination address extraction for transaction routing (5) Transaction kernel access for cryptographic proof verification (6) Mining block height for blockchain confirmation tracking (7) Directional address property based on transaction direction (8) Enhanced payment ID extraction supporting multiple FFI methods. Status handling: Maps to TransactionStatus enum with comprehensive error checking. Dependencies: TariAddress, CompletedTransactionKernel, ByteVector for message decoding. Used by: Transaction history, balance calculations, transaction status displays. Lifecycle: Represents final state of successful transactions with complete metadata. Critical for: Transaction confirmation, balance updates, and user transaction history throughout the application. |
| `CompletedTransactionKernel.swift` | FFI wrapper for accessing cryptographic kernel data of completed transactions. **Key exports:** CompletedTransactionKernel class with excessHex, excessPublicNonceHex, excessSignatureHex properties. **Dependencies:** Core FFI functions (transaction_kernel_*), WalletError. **Features:** Hexadecimal cryptographic data access, automatic memory management, error handling for all kernel operations. **Usage:** Used for transaction verification, audit trails, and cryptographic proof validation in completed transactions. |
| `CompletedTransactions.swift` | FFI wrapper for managing collections of completed transactions from Rust core. **Key exports:** CompletedTransactions class with count property and transaction(at:isCancelled:) method. **Dependencies:** Core FFI functions (completed_transactions_*), CompletedTransaction, WalletError. **Features:** Safe indexed access to completed transactions, cancellation status tracking, automatic memory management. **Usage:** Core component for transaction history display, providing access to all completed transactions for history screens and reporting. |

###### MobileWallet/Libraries/TariLib/Core/FFI/Transactions/Pending Inbound/

| File | Description |
|------|-------------|
| `PendingInboundTransaction.swift` | FFI wrapper for pending inbound transactions conforming to Transaction protocol. **Key exports:** PendingInboundTransaction class with identifier, amount, message, timestamp, address, status properties. **Dependencies:** Core FFI functions (pending_inbound_transaction_*), Transaction protocol, TariAddress, TransactionStatus. **Features:** Inbound transaction details access, sender address information, payment ID/message retrieval, status tracking. **Usage:** Used for displaying incoming transactions awaiting confirmation, notification handling, and transaction monitoring in real-time. |
| `PendingInboundTransactions.swift` | FFI wrapper for managing collections of pending inbound transactions from Rust core. **Key exports:** PendingInboundTransactions class with count property and transaction(at:) method. **Dependencies:** Core FFI functions (pending_inbound_transactions_*), PendingInboundTransaction, WalletError. **Features:** Safe indexed access to pending inbound transactions, automatic memory management, error handling. **Usage:** Core component for displaying incoming transactions awaiting confirmation in transaction lists and notification systems. |

###### MobileWallet/Libraries/TariLib/Core/FFI/Transactions/Pending Outbound/

| File | Description |
|------|-------------|
| `PendingOutboundTransaction.swift` | Pending outbound transaction wrapper for tracking outgoing transactions awaiting completion. Implements Transaction protocol for transactions in progress from this wallet to external recipients. Key features: (1) Fixed outbound transaction properties (isOutboundTransaction: true, isCancelled: false, isPending: true) (2) Transaction identifier for tracking pending operations (3) Amount and fee access for transaction cost calculation (4) Message extraction for transaction metadata and notes (5) Timestamp for transaction creation time tracking (6) Destination address for recipient identification (7) Transaction status monitoring for completion tracking. Transaction lifecycle: Represents intermediate state between initiation and completion. Dependencies: TariAddress, TransactionStatus, PointerHandler. Used by: Send transaction flow, pending transaction lists, transaction status monitoring. Status tracking: Monitors transaction progression through network processing. Critical for: Transaction sending operations, user feedback on pending transactions, and outbound transaction management throughout the wallet interface. |
| `PendingOutboundTransactions.swift` | FFI wrapper for managing collections of pending outbound transactions from Rust core. **Key exports:** PendingOutboundTransactions class with count property and transaction(at:) method. **Dependencies:** Core FFI functions (pending_outbound_transactions_*), PendingOutboundTransaction, WalletError. **Features:** Safe indexed access to pending outbound transactions, automatic memory management, error handling. **Usage:** Core component for displaying outgoing transactions awaiting confirmation in transaction lists and status monitoring. |

###### MobileWallet/Libraries/TariLib/Core/FFI/Unblinded Outputs/

| File | Description |
|------|-------------|
| `UnblindedOutput.swift` | FFI wrapper for individual unblinded transaction outputs with JSON serialization support. **Key exports:** UnblindedOutput class with json property and JSON-based initialization. **Dependencies:** Core FFI functions (tari_unblinded_output_*), WalletError. **Features:** JSON serialization/deserialization, automatic memory management, output data preservation. **Usage:** Used for UTXO management, transaction reconstruction, and data exchange between wallet instances, particularly during backup/restore operations. |
| `UnblindedOutputs.swift` | FFI wrapper for managing collections of unblinded transaction outputs from Rust core. **Key exports:** UnblindedOutputs class with count property, unblindedOutput(index:) method, and all computed property. **Dependencies:** Core FFI functions (unblinded_outputs_*), UnblindedOutput, WalletError. **Features:** Safe indexed access to unblinded outputs, automatic memory management, collection enumeration. **Usage:** Core component for UTXO management, transaction building, and wallet state reconstruction during recovery and synchronization. |

###### MobileWallet/Libraries/TariLib/Core/Services/

| File | Description |
|------|-------------|
| `CoreTariService.swift` | Base service class for all Tari blockchain services providing shared infrastructure. Defines MainServiceable protocol interface for core wallet services (connection, validation, balance). Maintains unowned references to FFIWalletHandler, WalletCallbacks, and MainServiceable to prevent retain cycles. Abstract base class establishing common patterns for TariBalanceService, TariTransactionsService, TariContactsService, and other blockchain services. Essential architectural component ensuring consistent service design and resource management across the Tari integration layer. |
| `TariBalanceService.swift` | Reactive service managing wallet balance state and real-time updates. Extends CoreTariService with @Published properties for balance tracking (WalletBalance) and FFI balance coordination. Implements Combine framework for reactive UI updates and maintains cancellables set for subscription management. Provides interface between wallet balance queries and UI layer, handling available, pending, and locked fund calculations. Used by home screen, transaction flows, and balance-dependent UI components for real-time wallet state reflection. |
| `TariConnectionService.swift` | Network connectivity and base node management service extending CoreTariService. Handles base node selection, peer connections, and network validation with InternalError enum for invalid peer string scenarios. Coordinates with validation service for connection verification and manages blockchain node connectivity. Provides interface for selecting base nodes, managing peer connections, and monitoring network health. Critical for wallet's blockchain connectivity and ensuring reliable network communication for transaction broadcasting and blockchain synchronization. |
| `TariContactsService.swift` | Address book management service extending CoreTariService for contact operations. Provides interface for wallet contact management including allContacts property access and upsert operations for contact creation/updates. Enhanced contact lookup using TariAddressComponents equality comparison supporting payment ID addresses. Handles contact persistence, retrieval, and synchronization with underlying wallet contact storage. Interfaces with wallet manager for contact operations and maintains contact consistency. Used by contact book screens, transaction recipient selection, and address management features for comprehensive contact management throughout the wallet. |
| `TariFeesService.swift` | Transaction fee calculation and network statistics service. Extends CoreTariService providing fee estimation with customizable parameters: amount, feePerGram (defaults to TariConstants.defaultFeePerGram), kernelsCount, and outputsCount. Includes feePerGramStats functionality for network fee analysis. Bridges wallet manager fee calculation capabilities to UI layer, enabling accurate transaction cost estimation and network fee monitoring for optimal transaction pricing. |
| `TariKeyValueService.swift` | Simple key-value storage service for wallet configuration and settings persistence. Provides strongly-typed key access via Key enum (currently supporting network configuration) with get/set/clear operations through FFI wallet storage. Enables persistent storage of wallet-specific configuration data with atomic operations and error handling. Used for storing network settings and other wallet configuration that persists across app launches. Minimal but essential service for wallet state persistence and configuration management through the underlying Tari wallet database. |
| `TariMessageSignService.swift` | Cryptographic message signing service for digital signature operations using wallet private keys. Provides message signing functionality returning MessageMetadata with hex signature and nonce components. Implements signature parsing and validation with error handling for malformed signature strings. Essential for secure communication protocols, authentication tokens, and push notification verification. Used by NotificationManager for cryptographically signed push notifications and other services requiring message authentication. Critical security service ensuring message integrity and authenticity in wallet communications. Dependencies: FFIWalletHandler for signing operations, MessageMetadata for result structure. |
| `TariRecoveryService.swift` | Wallet recovery and seed word management service providing wallet restoration capabilities. Manages recovery status updates via @Published properties for UI feedback during wallet restoration process. Provides access to wallet seed words for backup purposes and implements recovery initiation with recovery output messages. Supports multilingual seed word lists for internationalized wallet recovery and mnemonic validation. Essential for wallet backup and recovery functionality including seed phrase backup and wallet restoration from existing seed phrases. Integrates with WalletCallbacks for recovery status updates and FFIWalletHandler for recovery operations. |
| `TariTransactionsService.swift` | Comprehensive transaction lifecycle management service extending CoreTariService. Handles transaction creation, sending, receiving, monitoring, and status tracking with InternalError enum for insufficient funds scenarios. Implements Combine framework for reactive transaction state updates and real-time status monitoring with main actor updates. Manages pending inbound/outbound transactions, completed transactions, and transaction validation. Enhanced transaction sending without message parameter and payment reference lookup functionality. Provides interface for sending payments, monitoring transaction progress, coordinating with wallet balance updates, and retrieving payment references for transaction metadata. Dependencies: Combine, PaymentReference. Central service for all wallet transaction operations, state management, and payment reference tracking. |
| `TariUTXOsService.swift` | Unspent Transaction Output (UTXO) management service for advanced wallet operations. Provides access to all wallet UTXOs and implements coin control features: coin splitting for privacy and coin joining for consolidation. Offers preview functionality for fee estimation before executing coin operations with configurable fee-per-gram rates. Essential for advanced users managing UTXO sets for privacy optimization and wallet maintenance. Integrates with FFIWalletHandler for UTXO operations and uses TariConstants for default fee parameters. Used by advanced settings and UTXO management interfaces. |
| `TariUnspentOutputsService.swift` | UTXO management service providing access to unspent transaction outputs. Inherits from CoreTariService and wraps wallet manager UTXO operations. Exports: unspentOutputs() for retrieving UnblindedOutputs, store() for importing external UTXOs. Dependencies: CoreTariService base class, WalletManager FFI wrapper. Used by: UTXO management screens and transaction sending logic. Handles both getting existing UTXOs and storing imported ones with source address and message metadata. |
| `TariValidationService.swift` | Blockchain synchronization and transaction validation service managing wallet sync status. Tracks transaction output (TXO) and transaction (TX) validation progress with reactive status updates (idle, syncing, synced, failed). Coordinates dual validation processes ensuring transaction and output integrity against blockchain. Provides reset functionality for resync operations and handles validation callbacks for status tracking. Essential for wallet synchronization UI feedback and ensuring transaction data consistency. Used by sync indicators and wallet state management for user feedback during blockchain synchronization. |

###### MobileWallet/Libraries/TariLib/Core/Tor/

| File | Description |
|------|-------------|
| `TorConnectionStatus.swift` | Tor connection state enumeration for privacy network management. Exports: TorConnectionStatus enum with states: disconnected, connecting, waitingForAuthorization, portsOpen, connected, disconnecting. Used by: ConnectionMonitor, TorService, and UI components displaying network status. Represents the complete Tor connection lifecycle from initial disconnection through authorization, port opening, and final connection establishment. Essential for providing user feedback about privacy network connectivity. |
| `TorError.swift` | Tor error handling enumeration with associated values for detailed error context. Exports: TorError enum conforming to Error protocol with cases: connectionFailed(error), authenticationFailed, missingController, missingCookie(error), unknown(error). Used by: Tor connection management code for error handling and user feedback. Provides structured error representation for Tor network issues including connection failures, authentication problems, missing controllers, cookie authentication issues, and unknown errors with underlying error details. |

###### MobileWallet/Libraries/TariLib/Core/Tor/Helpers/

| File | Description |
|------|-------------|
| `Ipv6Tester.swift` | IPv6 network capability detection for Tor bridge configuration. Exports: Ipv6Status enum (torIpv6ConnFalse, torIpv6ConnDual, torIpv6ConnOnly, torIpv6ConnUnknown), ipv6_status() static method. Dependencies: Foundation, Reachability framework, NetworkTools Objective-C helper. Used by: Tor configuration logic to determine optimal bridge settings. Analyzes network interfaces (en*, pdp_ip*) to detect IPv4/IPv6 availability, filtering out local addresses (127.*, 169.254.*, fe80:) to determine actual internet connectivity capabilities. |
| `NetworkTools.h` | Objective-C header for low-level network interface inspection. Exports: NetworkTools class with addressesForProtocol(int) method. Used by: Ipv6Tester.swift for IPv6 capability detection and Tor bridge configuration. Provides foundation for iOS network interface enumeration using BSD socket APIs. Essential for determining device network capabilities before configuring Tor bridges. |
| `NetworkTools.m` | Objective-C implementation for network interface enumeration. Implements: addressesForProtocol(int) using getifaddrs() BSD system call. Dependencies: ifaddrs.h, arpa/inet.h, net/if.h, netdb.h system headers. Used by: Ipv6Tester for network capability detection. Enumerates active network interfaces, extracts IPv4/IPv6 addresses using inet_ntop(), filters by protocol version, and returns dictionary mapping interface names to IP addresses. Critical for Tor bridge selection and network privacy configuration. |

###### MobileWallet/Libraries/TariLib/Core/Variables/

| File | Description |
|------|-------------|
| `TariConstants.swift` | Core Tari protocol constants for wallet operations. Exports: TariConstants enum with static values: defaultFeePerGram (10 MicroTari), defaultKernelCount (1), defaultOutputCount (2), torPort (18101). Used by: Transaction fee calculation, UTXO management, and Tor network configuration. Centralizes critical protocol parameters ensuring consistency across wallet operations. Essential for transaction construction with proper fees and Tor privacy network configuration. |
| `WalletTag.swift` | Wallet instance identifier enumeration for multi-wallet support. Exports: WalletTag enum conforming to String with case: main. Used by: Wallet management systems to distinguish between different wallet instances. Provides type-safe wallet identification ensuring proper wallet context in operations. Currently supports single main wallet but designed for future multi-wallet functionality. |

###### MobileWallet/Libraries/TariLib/Wrappers/Utils/

| File | Description |
|------|-------------|
| `AppInfo.swift` | Application version and build information utilities. Exports: AppInfo enum with static computed properties: appVersion (CFBundleShortVersionString), buildVestion [sic] (CFBundleVersion). Dependencies: Foundation Bundle class. Used by: Debug screens, crash reporting, and version-dependent features. Provides type-safe access to app bundle information for version display and compatibility checks. Note: Contains typo in buildVestion property name. |
| `MicroTari.swift` | Precise currency amount handling for Tari cryptocurrency with comprehensive formatting and validation. Manages conversion between raw µTari values and user-facing decimal representation. Key features: (1) Currency conversion (1 Tari = 1,000,000 µTari) with precise arithmetic (2) Multiple formatters for different use cases (default, precise, edit, with operators) (3) Formatting options including rounded (2 decimals), precise (6 decimals), and small value handling (4) String parsing and validation with overflow protection (5) Operator formatting for transaction displays (+/- prefixes) (6) Locale-aware number formatting with grouping and decimal separators. Formatters: defaultFormatter (standard display), preciseFormatter (full precision), editFormatter (user input), withOperatorFormatter (transaction display). Dependencies: Foundation NumberFormatter. Used by: Transaction amounts, balance display, user input validation, fee calculations throughout the app. Critical for: All monetary calculations and display formatting with proper precision handling. |

###### MobileWallet/Libraries/TariLib/Wrappers/Utils/Connection Monitor/

| File | Description |
|------|-------------|
| `ConnectionMonitor.swift` | Centralized network connectivity monitoring using Combine publishers. Exports: ConnectionMonitor class with @Published properties for networkConnection, torConnection, torBootstrapProgress, baseNodeConnection, walletScannedHeight, chainTip, syncStatus. Dependencies: UIKit, Combine framework, NetworkMonitor, TorConnectionStatus, BaseNodeConnectivityStatus. Used by: UI components displaying connection status and network-dependent features. Aggregates multiple network status streams into reactive properties with UI helpers for status display, popup dialogs, and localized status messages. Includes showDetailsPopup() for comprehensive connection status modal. |
| `NetworkMonitor.swift` | Low-level network interface monitoring using iOS Network framework. Exports: NetworkMonitor class with Status enum (disconnected, connected(interface)), @Published status property. Dependencies: Network framework, Combine. Used by: ConnectionMonitor for basic network connectivity detection. Monitors NWPathMonitor for network status changes, identifies interface types (WiFi, Cellular, Ethernet, Loopback), and publishes connectivity status updates. Handles automatic resource cleanup on deinitialization. |

###### MobileWallet/Libraries/TariLib/Wrappers/Utils/Loggers/

| File | Description |
|------|-------------|
| `ConsoleLogger.swift` | Console output logging implementation conforming to Logable protocol. Exports: ConsoleLogger class implementing log() method. Dependencies: os framework, LogFormatter, Logger.Domain/Level. Used by: Logger system for development debugging and console output. Outputs formatted log messages to system console using os_log with timestamp, domain, and level information. Part of multi-destination logging architecture for debugging and monitoring. |
| `CrashLogger.swift` | Sentry crash reporting and analytics logging implementation. Exports: CrashLogger class with isEnabled property, configure() method, conforming to Logable protocol. Dependencies: Sentry SDK, TariSettings, GroupUserDefaults, Logger system. Used by: App-wide crash monitoring and error reporting. Manages Sentry SDK initialization based on user tracking preferences, converts logger levels to Sentry breadcrumbs, and handles opt-in/opt-out crash reporting. Includes environment-specific configuration and SDK lifecycle management. |
| `FileLogger.swift` | File-based logging implementation conforming to Logable protocol. Exports: FileLogger class implementing log() method. Dependencies: LogFormatter, Tari wallet FFI, WalletTag. Used by: Logger system for persistent log storage and debugging. Routes formatted log messages through Tari wallet FFI for file-based persistence, enabling log export and debugging capabilities. Part of multi-destination logging infrastructure. |
| `LogFormatter.swift` | Centralized log message formatting utilities for consistent logging output. Exports: LogFormatter enum with formattedMessage() and formattedDomainName() static methods. Dependencies: Logger.Domain/Level enums. Used by: ConsoleLogger, FileLogger, and other logging components for uniform message formatting. Provides fixed-width domain and level formatting, '[Aurora]' app prefix, standardized separators, and string padding utilities. Ensures consistent log format across all logging destinations. |
| `Logger.swift` | Centralized logging system with domain-based categorization and multiple logger support. Provides flexible logging infrastructure for debugging and monitoring. Key features: (1) Level enumeration (verbose, info, warning, error) for message prioritization (2) Domain enumeration (general, connection, navigation, UI, security, BLE, tor, debug) for categorization (3) Logable protocol for implementing custom logger backends (4) Multiple logger attachment system allowing concurrent logging destinations (5) Tag-based filtering for selective logging to specific loggers (6) Domain-specific logging for organized debug output. Domains: Covers all major app subsystems including network, UI, security, and Bluetooth operations. Dependencies: None (pure Swift). Used by: All app components for debug output, error reporting, user action tracking. Logger types: Console, file, crash, status loggers can be attached. Critical for: Debugging, error tracking, user behavior analysis, and system monitoring across the entire application. |
| `StatusLoggerManager.swift` | Reactive logging manager for system status monitoring. Exports: StatusLoggerManager singleton with configure() method. Dependencies: Combine framework, AppConnectionHandler, Logger system. Used by: Application setup for comprehensive status logging. Monitors ConnectionMonitor @Published properties (baseNodeConnection, syncStatus, torConnection, networkConnection, torBootstrapProgress) and automatically logs state changes to the connection domain. Provides centralized status change logging for debugging and monitoring purposes. |

###### MobileWallet/Libraries/TariLib/Wrappers/Utils/Network/

| File | Description |
|------|-------------|
| `BaseNode.swift` | Tari base node configuration data model for network connectivity. Exports: BaseNode struct conforming to Equatable, Codable with properties: name, hex identifier, optional address. Methods: isCustomBaseNode computed property, peer identifier, makePublicKey(). Dependencies: PublicKey FFI wrapper. Used by: Network settings, base node selection screens, wallet initialization. Represents both default and custom base nodes with peer identification, public key generation, and network connectivity configuration. Essential for wallet-to-network communication setup. |
| `NetworkManager.swift` | Network configuration manager handling blockchain network selection and base node connectivity. Provides singleton access to network state, base node management, and network-specific settings. Features: (1) TariNetwork selection with mainnet default (2) Default and custom base node management (3) Block height tracking for sync status (4) NetworkSettings persistence via GroupUserDefaults (5) Random base node selection for failover (6) Currency symbol access for display. Dependencies: TariNetwork, BaseNode, NetworkSettings, Combine. Used by: Wallet initialization, connectivity management. Critical for: Network operations and blockchain synchronization. |
| `NetworkSettings.swift` | Network configuration container for custom base nodes and blockchain state. Exports: NetworkSettings struct conforming to Codable, Equatable with properties: name, customBaseNodes array, blockHeight. Methods: update() for customBaseNodes and blockHeight. Used by: Network management, settings persistence, blockchain synchronization. Provides immutable update pattern for network configuration changes while maintaining type safety and data consistency. |
| `TariNetwork.swift` | Tari blockchain network configuration definitions and utilities. Exports: TariNetwork struct with properties for name, presentedName, tickerSymbol, isRecommended, dnsPeer, blockExplorerURL, currencySymbol, version info. Static networks: mainnet (XTM), nextnet (tXTM), esmeralda (tXTM). Methods: fullPresentedName, blockExplorerKernelURL() for transaction lookup. Updated mainnet version to 4.5.0 for FFI compatibility. Used by: Network selection screens, wallet initialization, blockchain connectivity. Centralizes network-specific configuration including DNS peers, block explorers, currency symbols, and version compatibility requirements. |

###### MobileWallet/Libraries/TariLib/Wrappers/Utils/Settings/

| File | Description |
|------|-------------|
| `TariSettings.swift` | Centralized application configuration and environment management singleton. Exports: TariSettings shared instance, AppEnvironment enum (debug, testflight, production), URLs for external services, API keys from env.json. Dependencies: SwiftKeychainWrapper, WalletSettingsManager. Used by: App-wide configuration access, environment detection, external service integration. Loads environment variables, manages shared keychain access, storage directories, and provides URLs for Tari services, Tor bridges, privacy policies, and third-party integrations (Sentry, Giphy, Yat, Dropbox). Handles debug/testflight/production environment detection. |

###### MobileWallet/Libraries/TariLib/Wrappers/Utils/Settings/Wallet/

| File | Description |
|------|-------------|
| `WalletSettings.swift` | Wallet configuration state and backup settings data model. Exports: WalletSettings struct conforming to Codable/Equatable with enums: WalletConfigurationState (notConfigured, initialized, authorized, ready), BackupStatus (disabled, enabled with sync date), WalletSecurityStage (stage1A-3 for progressive security features). Properties: networkName, configurationState, iCloudDocsBackupStatus, dropboxBackupStatus, hasVerifiedSeedPhrase, delayedWalletSecurityStagesTimestamps, yat. Used by: WalletSettingsManager, security onboarding, backup management. Tracks wallet setup progress, backup configurations, and staged security feature implementation. |
| `WalletSettingsManager.swift` | Wallet settings persistence manager with network-specific configuration handling. Exports: WalletSettingsManager class with computed properties for configurationState, backup statuses, hasVerifiedSeedPhrase, security stage timestamps, connectedYat. Dependencies: NetworkManager, GroupUserDefaults, WalletSettings. Used by: App-wide wallet configuration management and settings persistence. Manages per-network wallet settings, automatic creation of default settings, and atomic updates to GroupUserDefaults. Provides type-safe access to wallet configuration with automatic persistence. |

###### MobileWallet/Screens/AdvancedSettings/Bridges/Custom Tor Bridges/

| File | Description |
|------|-------------|
| `CustomTorBridgesConstructor.swift` | Factory constructor for the Custom Tor Bridges configuration screen. Exports: buildScene(bridges:) -> CustomTorBridgesViewController. Dependencies: CustomTorBridgesModel, CustomTorBridgesViewController. Used by: Advanced Settings navigation flow for custom Tor bridge configuration. Follows MVVM dependency injection pattern with pre-filled bridge values. |
| `CustomTorBridgesModel.swift` | Business logic model for custom Tor bridges configuration screen. Exports: CustomTorBridgesModel class with @Published properties (isConnectionPossible, torBridges, endFlow). Dependencies: Tari.shared (Tor bridge management), Combine framework. Used by: CustomTorBridgesViewController. Manages bridge validation, connection status, and Tor bridge updates with reactive state management. |
| `CustomTorBridgesView.swift` | UI view for custom Tor bridges configuration screen with table-based interface. Exports: CustomTorBridgesView class extending BaseNavigationContentView, Row enum (input, requestBridges, scanQRCode, uploadQRCode). Dependencies: TariCommon, CustomTorBridgesInputCell, CustomTorBridgesHeaderView, MenuCell. Used by: CustomTorBridgesViewController. Features diffable data source, navigation bar with connect button, and multiple bridge input methods (text, QR scan, image upload). |
| `CustomTorBridgesViewController.swift` | Main view controller for custom Tor bridges configuration screen with QR code and image processing capabilities. Exports: CustomTorBridgesViewController extending SecureViewController. Dependencies: UIKit, Combine, CustomTorBridgesModel, CustomTorBridgesView, CIDetector (QR code), AppRouter, WebBrowserPresenter, PopUpPresenter. Used by: Advanced Settings navigation. Implements UIImagePickerControllerDelegate for image-based QR code scanning, QR scanner integration, and Tor Project website navigation for bridge requests. |

###### MobileWallet/Screens/AdvancedSettings/Bridges/Custom Tor Bridges/Views/

| File | Description |
|------|-------------|
| `CustomTorBridgesHeaderView.swift` | Header view component for custom Tor bridges input section displaying instructional text. Exports: CustomTorBridgesHeaderView extending DynamicThemeHeaderFooterView. Dependencies: TariCommon, localized strings. Used by: CustomTorBridgesView as table section header. Features themed label with "paste bridges" instruction text and proper spacing constraints. |
| `CustomTorBridgesInputCell.swift` | Table view cell for Tor bridges text input with multi-line text view and format examples. Exports: CustomTorBridgesInputCell extending UITableViewCell with text property and onTextUpdate callback. Dependencies: TariCommon, UITextViewDelegate. Used by: CustomTorBridgesView in input section. Features 250pt height constraint, placeholder with obfs4 and IP:PORT format examples, animated placeholder visibility, and real-time text change callbacks. |

###### MobileWallet/Screens/AdvancedSettings/Bridges/Tor Bridges List/

| File | Description |
|------|-------------|
| `TorBridgesConstructor.swift` | Factory constructor for the Tor Bridges list configuration screen. Exports: buildScene() -> TorBridgesViewController. Dependencies: TorBridgesModel, TorBridgesViewController. Used by: Advanced Settings navigation flow for Tor bridge type selection. Follows MVVM dependency injection pattern without parameters. |
| `TorBridgesModel.swift` | Business logic model for Tor bridges selection screen managing bridge types and connection states. Exports: TorBridgesModel class with BridgesType enum (noBridges, customBridges), Action enum (setupCustomBridges), @Published properties (isConnectionPossible, areCustomBridgesSelected, selectedBridgesType, action). Dependencies: Tari.shared (Tor configuration). Used by: TorBridgesViewController. Manages bridge type selection, connection validation, and navigation to custom bridge setup. |
| `TorBridgesView.swift` | UI view for Tor bridges selection screen with table-based interface and accessory images. Exports: TorBridgesView extending BaseNavigationContentView, Row enum (noBridges, customBridges). Dependencies: TariCommon, AccessoryImageMenuCell, TorBridgesFooterView. Used by: TorBridgesViewController. Features diffable data source, connect button, selection indicators with scheduled icons, footer view, and row-specific arrow visibility configuration. |
| `TorBridgesViewController.swift` | Main view controller for Tor bridges selection with reactive state management and navigation to custom bridge setup. Exports: TorBridgesViewController extending SecureViewController. Dependencies: UIKit, Combine, TorBridgesModel, TorBridgesView, CustomTorBridgesConstructor. Used by: Advanced Settings flow. Features reactive bridge type selection, connection button management, automatic status updates on appear, and navigation to custom bridge configuration screen. |

###### MobileWallet/Screens/AdvancedSettings/Bridges/Tor Bridges List/Views/

| File | Description |
|------|-------------|
| `TorBridgesFooterView.swift` | Footer view for Tor bridges configuration screen. Extends DynamicThemeHeaderFooterView to provide theme-aware footer content with descriptive text about Tor bridges functionality. Contains a multi-line UILabel that displays localized help text about bridges configuration. Uses TariCommon framework for theming and positioning. Auto-layout with 8pt vertical padding and 25pt horizontal margins. Theme-aware text color updates via update(theme:) method using lightText color. |

###### MobileWallet/Screens/AdvancedSettings/Screen Recoding Settings/

| File | Description |
|------|-------------|
| `ScreenRecordingSettingsConstructor.swift` | Factory constructor for the Screen Recording Settings configuration screen. Exports: buildScene(backButtonType:) -> ScreenRecordingSettingsViewController. Dependencies: ScreenRecordingSettingsModel, ScreenRecordingSettingsViewController, NavigationBar.BackButtonType. Used by: Advanced Settings navigation flow for screen recording privacy configuration. Follows MVVM dependency injection pattern with configurable navigation back button type. |
| `ScreenRecordingSettingsModel.swift` | Simple business logic model for screen recording privacy settings management. Exports: ScreenRecordingSettingsModel class with @Published areScreenshotsEnabled property. Dependencies: Combine, SecurityManager. Used by: ScreenRecordingSettingsViewController. Features bidirectional binding with SecurityManager for screenshot blocking configuration with automatic security state updates. |
| `ScreenRecordingSettingsView.swift` | UI view for screen recording privacy settings with description and toggle switch. Exports: ScreenRecordingSettingsView extending BaseNavigationContentView with switch value change callback. Dependencies: TariCommon, Combine, SwitchMenuCell. Used by: ScreenRecordingSettingsViewController. Features description label explaining privacy options, table view with switch cell, diffable data source, reactive switch value updates, and proper theming support. |
| `ScreenRecordingSettingsViewController.swift` | Screen recording security settings view controller. Extends SecureViewController<ScreenRecordingSettingsView> to manage user preferences for screenshot/screen recording permissions. Contains ScreenRecordingSettingsModel for state management and uses Combine framework for reactive UI updates. Features: Toggle switch for enabling/disabling screenshots, confirmation dialog when enabling (security warning), reactive UI updates via Published properties, custom navigation bar with back button type configuration. Implements security-first approach requiring explicit user confirmation before enabling potentially dangerous screenshot functionality. |

###### MobileWallet/Screens/AdvancedSettings/Select Network/

| File | Description |
|------|-------------|
| `SelectNetworkModel.swift` | Model for network selection functionality. Manages switching between different Tari networks (mainnet/testnet/localnet). Contains NetworkModel struct for display data and ViewModel with Published properties for reactive UI updates. Features: Tracks selected network index and available networks, refreshData() updates current network selection state, update(selectedIndex:) switches to new network and triggers app restart via AppRouter.transitionToSplashScreen(). Integrates with NetworkManager.shared and Tari.shared for network management. Note: Network switching requires app restart and disables automatic wallet reconnection. |
| `SelectNetworkViewController.swift` | View controller for network selection screen. Extends SettingsParentTableViewController to provide table-based interface for switching between Tari networks. Uses UITableViewDiffableDataSource with SystemMenuTableViewCellItem for clean list presentation. Features: Combines framework for reactive UI updates, confirmation dialog before network switching (warns about app restart), SettingsHeaderView for section headers with descriptive text, selection marks for current network, automatic data refresh on viewWillAppear. Integrates with SelectNetworkModel for business logic and PopUpPresenter for confirmation dialogs. |

###### MobileWallet/Screens/AdvancedSettings/SelectBaseNode/

| File | Description |
|------|-------------|
| `SelectBaseNodeModel.swift` | Model for base node selection and management functionality. Manages available Tari base nodes including predefined default nodes and custom user-added nodes. Contains NodeModel struct with Identifiable/Hashable conformance for unique identification and UI display. Features: Published properties for reactive UI updates, refreshData() loads current node list and selection state, selectNode() switches active base node with error handling, addNode() validates and adds custom base nodes, deleteNode() removes custom nodes (protects predefined ones). Integrates with NetworkManager for node lists and Tari.shared.wallet for node selection. Error handling via ErrorMessageManager. |
| `SelectBaseNodeViewController.swift` | View controller for base node selection and management screen. Extends SettingsParentTableViewController to provide interface for selecting active Tari base node and managing custom nodes. Uses UITableViewDiffableDataSource with SelectBaseNodeModel.NodeModel for efficient list updates. Features: Navigation bar with plus button for adding custom nodes, SelectBaseNodeCell with tick marks for selected nodes and delete buttons for removable custom nodes, FormOverlayPresenter integration for add base node form, reactive UI updates via Combine framework, error message handling via PopUpPresenter. Integrates with SelectBaseNodeModel for business logic and state management. |

###### MobileWallet/Screens/AdvancedSettings/SelectBaseNode/Views/

| File | Description |
|------|-------------|
| `SelectBaseNodeCell.swift` | Custom table view cell for base node selection list. Extends DynamicThemeCell with three accessory types: none, tick (selected), and delete button (for custom removable nodes). Contains title label (node name), subtitle label (peer address), and conditional accessory views. Features: Vertical stack view layout with 4pt spacing, theme-aware text colors and button styling, highlight animation with alpha transitions, delete button callback mechanism via onDeleteButtonTap closure, auto-layout with 65pt minimum height and proper margins. Used specifically in SelectBaseNodeViewController for displaying available base nodes with appropriate visual indicators. |

###### MobileWallet/Screens/AdvancedSettings/Views/

| File | Description |
|------|-------------|
| `SettingsHeaderView.swift` | Reusable header view for settings sections. Extends DynamicThemeHeaderFooterView to provide consistent header styling across settings screens. Contains single UILabel with multi-line support and proper font styling for settings descriptions. Features: Text property for easy content setting, 25pt horizontal margins and vertical padding, theme-aware text color using body text color, translatesAutoresizingMaskIntoConstraints disabled for proper auto-layout. Uses TariCommon for theming and Theme.shared.fonts.settingsSeedPhraseDescription for typography. Generic component used throughout advanced settings screens for section headers. |

###### MobileWallet/Screens/AppEntry/Splash/

| File | Description |
|------|-------------|
| `LocalAuthViewController.swift` | Dedicated local authentication controller for wallet security verification. Presents over existing content with full-screen modal for biometric (Face ID/Touch ID) or passcode authentication. Provides authentication success/failure callbacks for app resume scenarios and manual authentication triggers. Skips authentication on simulator for development convenience. Essential security component ensuring wallet access protection when app returns from background. Used by SceneDelegate and other security-sensitive areas requiring user verification. Dependencies: LAContext for biometric authentication, SplashView for UI consistency. |
| `SplashView.swift` | Main splash screen UI with wallet creation/import interface. Exports: SplashView class extending DynamicThemeView with subviews: iconView, staticSplashView, titleLabel, importWallet/createWallet buttons, disclaimerTextView. Dependencies: Lottie, TariCommon, UIKit, Typography. Used by: SplashViewController for app entry screen. Features animated layout transitions, theme-aware styling, version display, legal disclaimer with clickable privacy/user agreement links, and button callbacks for wallet creation/import flows. Includes commented Lottie animation support. |
| `SplashViewConstructor.swift` | Dependency injection constructor for splash screen using MVVM pattern. Exports: SplashViewConstructor enum with buildScene() static method accepting isWalletConnected, paperWalletRecoveryData, recoveryMode parameters. Dependencies: SplashViewController, SplashViewModel, PaperWalletRecoveryData. Used by: App navigation flow for initial splash screen setup. Implements constructor pattern for clean dependency injection and testable component creation with support for wallet recovery scenarios. |
| `SplashViewController.swift` | Primary application entry screen managing wallet initialization and authentication flows. Orchestrates transitions between wallet creation, restoration, and existing wallet authentication. Implements reactive UI with Combine framework for status updates, network selection, and error handling. Enhanced authentication flow with conditional button visibility - initially hides wallet creation/import buttons, shows them only for new users or provides continue option for existing users with authentication failure. Handles biometric authentication via LocalAuthentication framework with improved state management. Manages wallet recovery progress display and coordinates with SplashViewModel for wallet operations. Central coordinator for app entry determining user path based on wallet existence and configuration state with improved UX flow. |
| `SplashViewModel.swift` | Splash screen MVVM business logic handling wallet creation, recovery, and connection flows. Exports: SplashViewModel class with PaperWalletRecoveryData struct, Status/RecoveryMode enums, @Published properties for status, networkName, appVersion, isWalletExist, errorMessage. Dependencies: Combine, SeedWordsWalletRecoveryManager, NetworkManager, Tari wallet FFI. Used by: SplashViewController for app entry logic. Manages wallet lifecycle (create, open, delete, recover), network validation, Tor connection status, version migration, and error handling. Supports paper wallet and seed phrase recovery modes with comprehensive async/await operations. |
| `WalletCreationViewController.swift` | Comprehensive wallet creation and onboarding flow controller with multi-state management and biometric authentication. Exports: WalletCreationViewController extending DynamicThemeViewController, WalletCreationState enum (initial, createEmojiId, showEmojiId, localAuthentication, enableNotifications). Dependencies: Lottie, LocalAuthentication, AVFoundation, various UI components, TariLib (wallet address), AppRouter. Used by: App entry flow for new wallet setup. Features state machine with Lottie animations (checkMark, emojiWheel, faceID, touchID, nerdEmoji), emoji ID display, biometric setup, and configuration state management with TariSettings. |

###### MobileWallet/Screens/AppEntry/Splash/CheckMarkAnimation/

| File | Description |
|------|-------------|
| `CheckMark.json` | [GENERATED] Lottie animation file for checkmark completion animation used in splash and onboarding screens. Contains vector-based checkmark drawing animation with 206 frames at 60fps, featuring trim paths animation from 0-60 frames (drawing) and 120-180 frames (completion). Dependencies: Lottie animation framework. Used by: splash screen, wallet creation completion, transaction confirmations. Animation size: 79x63px with black stroke and smooth easing curves. |

###### MobileWallet/Screens/AppEntry/Splash/EmojiWheelAnimation/

| File | Description |
|------|-------------|
| `EmojiWheel.json` | [GENERATED] Lottie animation file for emoji wheel spinning animation used during wallet creation. Large animation file with very long lines (>5000 chars). Dependencies: Lottie animation framework. Used by: WalletCreationViewController during emoji ID generation phase. Features emoji wheel rotation animation for visual feedback during address creation process. |

###### MobileWallet/Screens/AppEntry/Splash/FaceIdAnimation/

| File | Description |
|------|-------------|
| `FaceID.json` | [GENERATED] Lottie animation file for Face ID authentication visual feedback. Contains vector-based animation data for Face ID scanning interface with 360 frames at 60fps (6 second duration). Features face outline with scanning elements, position animations, scale transformations from 5% to 14%, opacity transitions, and white fill color. Used during biometric authentication process to provide visual feedback while Face ID is being processed. Animation includes face boundary, eye indicators, and scanning visual effects. Regenerated when updating Face ID animation assets or when animation parameters change. |

###### MobileWallet/Screens/AppEntry/Splash/NerdEmojiAnimation/

| File | Description |
|------|-------------|
| `NerdEmojiAnimation.json` | [GENERATED] Lottie animation file for nerd emoji feedback animation. Contains vector-based animation data for displaying nerd face emoji with glasses and other animated elements. Used for positive user feedback or achievement notifications in the app. File exceeds 5000 characters per line due to compressed animation data format. Regenerated when updating emoji animations or feedback visual elements. Performance-optimized for mobile playback during user interaction feedback scenarios. |

###### MobileWallet/Screens/AppEntry/Splash/NotificationAnimation/

| File | Description |
|------|-------------|
| `NotificationAnimation.json` | [GENERATED] Lottie animation file for notification indicator animation. Contains vector-based animation data for notification bell, badge, or alert visual feedback elements. Used in notification permission flows, alerts, or status indicators throughout the app. Regenerated when updating notification-related animations or permission flows. Performance-optimized for mobile playback during notification scenarios. |

###### MobileWallet/Screens/AppEntry/Splash/PendingCircleAnimation/

| File | Description |
|------|-------------|
| `PendingCircleAnimation.json` | [GENERATED] Lottie animation file for pending transaction circle loader. Contains vector-based animation data for circular progress or loading indicator specifically for pending transaction states. Used during transaction processing, network synchronization, or other pending operations. Features smooth circular motion and loading states. Regenerated when updating transaction status animations or loading indicators. Performance-optimized for continuous playback during long-running operations. |

###### MobileWallet/Screens/AppEntry/Splash/SplashAnimation/

| File | Description |
|------|-------------|
| `SplashAnimation.json` | [GENERATED] Large Lottie animation file for app splash screen. Contains complex vector-based animation data with multiple layers, shapes, and transitions for the main Tari wallet loading animation. File exceeds 5000 characters per line due to compressed animation data. Used during app startup and network transitions to provide engaging visual feedback while app initializes. Includes Tari branding elements, loading indicators, and smooth transitions. Regenerated when updating splash screen animation or branding elements. Performance-optimized for mobile playback during critical app startup phase. |

###### MobileWallet/Screens/AppEntry/Splash/TouchIdAnimation/

| File | Description |
|------|-------------|
| `TouchIdAnimation.json` | [GENERATED] Lottie animation file for Touch ID authentication visual feedback. Contains vector-based animation data for Touch ID scanning interface with fingerprint elements and scanning animations. Used during biometric authentication process to provide visual feedback while Touch ID is being processed. Features fingerprint outline, scanning elements, and authentication state transitions. Regenerated when updating Touch ID animation assets or authentication flow elements. |

##### MobileWallet/Screens/Contact Book/

| File | Description |
|------|-------------|
| `PopPresenter+ContactBook.swift` | PopUpPresenter extension providing contact-specific dialog functionality for unlink confirmation and success notifications. Features standardized styling and localized messaging for contact unlinking workflows. |

###### MobileWallet/Screens/Contact Book/Add Contact/

| File | Description |
|------|-------------|
| `AddContactConstructor.swift` | Factory constructor for adding new contacts to the contact book with optional pre-filled address. Exports: buildScene(onSuccess:address:) and buildScene(onSuccess:) methods returning AddContactViewController. Dependencies: AddContactModel, AddContactViewController, TariAddress, NavigationActionType. Used by: Contact book navigation flow. Follows MVVM dependency injection pattern with configurable success navigation actions and optional address parameter for QR/deep link imports. |
| `AddContactModel.swift` | Business logic model for adding contacts with comprehensive validation and QR code handling. Exports: AddContactModel class with Action enum (showDetails, popBack), DataValidationError enum, @Published properties (isDataValid, action, errorText, errorMessage), CurrentValueSubject properties (emojiIDSubject, nameSubject). Dependencies: Combine, ContactsManager, TariAddress, QRCodeData, ErrorMessageManager, DeepLink protocols. Used by: AddContactViewController. Features reactive validation, emoji ID validation, contact creation, QR code import support, and deep link handling for user profiles and transaction addresses. |
| `AddContactView.swift` | UI view for adding contacts with search field, name input, and QR code scanning integration. Exports: AddContactView extending BaseNavigationContentView with searchView, nameTextField properties, error handling, and callback actions. Dependencies: TariCommon, ContactSearchView. Used by: AddContactViewController. Features themed input fields with validation error display, QR code button integration, done button state management, and responsive layout with proper spacing and constraints. |
| `AddContactViewController.swift` | Main view controller for adding contacts with comprehensive form handling and QR code integration. Exports: AddContactViewController extending SecureViewController, NavigationActionType enum (moveToDetails, moveBack). Dependencies: UIKit, Combine, AddContactModel, AddContactView, AppRouter, ContactDetailsConstructor, PopUpPresenter. Used by: Contact book navigation flow. Features reactive form validation, QR code scanning for contact import, navigation management with configurable success actions, error display, and automatic text binding. |

###### MobileWallet/Screens/Contact Book/Contact Details/

| File | Description |
|------|-------------|
| `ContactDetailsConstructor.swift` | Factory constructor for contact details screen displaying individual contact information and actions. Exports: buildScene(model:) -> ContactDetailsViewController. Dependencies: ContactDetailsModel, ContactDetailsViewController, ContactsManager.Model. Used by: Contact book navigation flow. Follows MVVM dependency injection pattern with contact model parameter for detailed view presentation. |
| `ContactDetailsModel.swift` | Comprehensive business logic model for contact details with extensive menu management and external wallet integration. Exports: ContactDetailsModel class with MenuSection struct, MenuItem enum (send, favorites, link/unlink, transactions, remove, external wallets), Action enum, ViewModel struct, @Published properties. Dependencies: Combine, YatLib, ContactsManager, TariAddress, PaymentInfo, ErrorMessageManager. Used by: ContactDetailsViewController. Features contact operations, Yat integration for external wallets (BTC/ETH/XMR), transaction management, favorites handling, and URL scheme launching for external wallets. |
| `ContactDetailsView.swift` | Complex UI view for displaying contact details with address/Yat toggle functionality and menu table. Exports: ContactDetailsView extending BaseNavigationContentView, IDElementsState enum, ContactDetailsViewBottomView class. Dependencies: UIKit, TariCommon, RoundedAddressView, MenuTableView, BaseButton. Used by: ContactDetailsViewController. Features name label, address view with details button, Yat label with toggle button, menu table with sections, footer view, state management for ID elements visibility, and comprehensive layout constraints with dynamic theming. |
| `ContactDetailsViewController.swift` | Main view controller for contact details with comprehensive action handling and navigation management. Exports: ContactDetailsViewController extending SecureViewController with menu action handling. Dependencies: UIKit, Combine, ContactDetailsModel, ContactDetailsView, various navigation constructors, AppRouter, PopUpPresenter, FormOverlayPresenter. Used by: Contact book flow. Features reactive UI updates, menu action dispatching, edit form presentation, confirmation dialogs for contact operations, transaction navigation, link/unlink functionality, and external wallet integration with proper lifecycle management. |

###### MobileWallet/Screens/Contact Book/Contact Details/Edit Form/

| File | Description |
|------|-------------|
| `ContactBookFormView.swift` | Generic contact editing form view with secure content wrapper and dynamic text field configuration. Exports: ContactBookFormView extending DynamicThemeView, implementing FormShowable protocol, TextFieldViewModel struct. Dependencies: UIKit, TariCommon, Combine, FormTextField, SecureWrapperView, NavigationBar. Used by: FormOverlayPresenter for contact editing. Features configurable text fields with emoji keyboard support, secure content wrapping, return key navigation, reactive text binding, and form lifecycle management with proper constraints. |

###### MobileWallet/Screens/Contact Book/Contact Transactions List/

| File | Description |
|------|-------------|
| `ContactTransactionListConstructor.swift` | Factory constructor for contact transaction list screen. Implements dependency injection pattern to build ContactTransactionListViewController with properly configured ContactTransactionListModel. Takes ContactsManager.Model as input to initialize transaction filtering for specific contact. Single static method buildScene() encapsulates view controller creation and dependency wiring. Used by contact book navigation flow to show transaction history for selected contact. |
| `ContactTransactionListModel.swift` | Model for contact-specific transaction list functionality. Manages filtering and displaying transactions associated with a specific contact using ContactsManager.Model. Features: Published properties for reactive UI updates (name, viewModels, action, errorModel), real-time transaction filtering based on contact address hex comparison, transaction sorting by timestamp (newest first), TxTableViewModel conversion for UI display, navigation actions for transaction details and send flows. Integrates with Tari.shared.wallet transactions stream using Combine for automatic updates. Error handling via ErrorMessageManager and PaymentInfo integration for send flows. |
| `ContactTransactionListView.swift` | View for displaying contact-specific transaction history. Extends BaseNavigationContentView with table view for transaction list and placeholder for empty states. Features: ContactTransactionListHeaderView with contact name display, UITableView with TxTableViewCell registration and automatic row height, ContactTransactionListPlaceholder for empty state with send button, UITableViewDiffableDataSource for efficient updates, GIF download support for transaction cells with reload callbacks. Layout includes navigation bar integration and placeholder/table view switching based on content availability. Callbacks for row selection and send button interaction. |
| `ContactTransactionListViewController.swift` | View controller for contact transaction history screen. Extends SecureViewController to provide secure transaction history viewing for specific contacts. Features: ContactTransactionListModel integration for business logic, Combine framework for reactive UI updates, automatic view updates from model changes, navigation to transaction details and send transaction flow, error handling and action routing. Implements MVVM pattern with clear separation of concerns. |

###### MobileWallet/Screens/Contact Book/Contact Transactions List/Legacy/

| File | Description |
|------|-------------|
| `TxGifManager.swift` | Legacy GIF download manager for transaction attachments. Singleton class managing GIF downloads from Giphy SDK with caching, concurrent operations, and timeout handling. Features: NSCache for media storage, operation management with cancellation support, completion callback queuing for multiple requests, 10-second download timeout with automatic cleanup, error handling for network failures. Uses GiphyCore.shared.gifByID() for downloads. Cache management includes automatic cleanup on operation cancellation. Thread-safe operation handling with weak references for memory safety. |
| `TxTableViewCell.swift` | Legacy transaction table view cell with GIF attachment support. Extends DynamicThemeCell to display transaction information including title, time, status, note, value, and optional Giphy attachments. Features: GPHMediaView for GIF display, LoadingGIFButton for download states, KVO observation for real-time updates, complex layout with value container and label positioning, theme-aware styling with conditional colors based on transaction type, automatic cell size updates for dynamic content. Integrates with TxTableViewModel for data binding and GIF management. |
| `TxTableViewModel.swift` | Legacy view model for transaction table cells with Giphy integration. NSObject subclass providing KVO-observable properties for transaction display including title formatting, status updates, and GIF management. Features: Transaction data transformation (amount, direction, status), contact name attribution with bold styling, Giphy ID extraction from transaction messages, real-time status updates based on confirmation counts, GIF download coordination via TxGifManager, automatic time updates every 60 seconds. Handles one-sided payments, pending states, and cancelled transactions with appropriate messaging. |

###### MobileWallet/Screens/Contact Book/Contact Transactions List/Views/

| File | Description |
|------|-------------|
| `ContactTransactionListHeaderView.swift` | Header view for contact transaction list screen. Extends DynamicThemeView with stylized label displaying localized text about transaction history for specific contact. Features: StylizedLabel with mixed font weights (normal/bold), centered text alignment with contact name highlighting, dynamic text updates when contact name changes, 20pt margins and spacing, theme-aware background colors (secondary/primary), placeholder view for spacing. Uses TariCommon framework for theming and component styling. |
| `ContactTransactionListPlaceholder.swift` | Placeholder view for empty contact transaction lists. Extends DynamicThemeView with illustration, text, and send button for empty state. Features: PaintBackgroundImageView with themed illustration, title and message labels with contact name personalization, TextButton for sending first transaction, responsive layout for different screen sizes, StylizedLabel with bold/normal text mixing for emphasis. Includes PaintBackgroundImageView subclass providing layered image display with background paint effect and theme-aware tinting. Callback mechanism for send button interactions. |

###### MobileWallet/Screens/Contact Book/Extensions/

| File | Description |
|------|-------------|
| `ContactType+Data.swift` | UI data extensions for contact type representation. Exports: ContactsManager.ContactType extension with computed properties: image (contact type icons), text (localized contact type labels). Dependencies: UIKit, localization system. Used by: Contact book UI components for contact type display. Provides visual representation mapping for contact types: internal/emoji ID contacts get specific icons and labels, empty contacts return nil values. Essential for consistent contact type visualization across the contact management interface. |

###### MobileWallet/Screens/Contact Book/Link Contacts/

| File | Description |
|------|-------------|
| `LinkContactsConstructor.swift` | Contact linking screen constructor following dependency injection pattern. Creates LinkContactsViewController configured with ContactSelectionModel for linking transactions to existing contacts. Part of contact book linking workflow. |
| `LinkContactsModel.swift` | Business logic model for contact selection and linking functionality. Manages contact search, filtering, and linking actions with Combine-based reactive state management. Handles contact validation, selection confirmation, and contact metadata updates through ContactsManager integration. |
| `LinkContactsView.swift` | UI view for contact linking interface with search functionality and contact list. Features navigation bar, search field, table view with ContactBookCell components, and placeholder handling. Supports real-time search filtering and contact selection with theme-aware styling. |
| `LinkContactsViewController.swift` | View controller coordinating contact linking workflow with reactive UI updates and popup dialogs. Handles contact selection confirmation, success notifications, and navigation to add contact screen. Integrates model state changes with view updates using Combine publishers. |

###### MobileWallet/Screens/Contact Book/List/

| File | Description |
|------|-------------|
| `ContactBookConstructor.swift` | Main contact book screen constructor implementing dependency injection pattern. Creates ContactBookViewController with ContactBookModel for managing contact list, search, and sharing functionality. |
| `ContactBookModel.swift` | Comprehensive contact book business logic with search, filtering, selection, and sharing capabilities. Manages normal and sharing modes, contact data fetching, deep link generation for contact sharing via QR codes or links. Features reactive state management with Combine publishers. |
| `ContactBookView.swift` | Main contact book view with navigation bar, search field, share bar, and pager container. Handles selection mode transitions, share button state management, and navigation button updates. Integrates ContactBookShareBar for contact sharing options. |
| `ContactBookViewController.swift` | Main contact book view controller orchestrating contact list display with pager navigation between all contacts and favorites tabs. Handles contact selection, sharing modes, QR code generation, and integration with ContactBookModel for business logic. Features overlay presentation and secure view controller inheritance. |

###### MobileWallet/Screens/Contact Book/List/Contact List/

| File | Description |
|------|-------------|
| `ContactBookContactListView.swift` | Main contact list view component with sectioned table view, selection mode, and placeholder support. Manages contact display, editing mode for sharing, selection state tracking, and footer actions. Features diffable data source, header sections, and reactive selected row updates. |
| `ContactBookContactListViewController.swift` | Lightweight view controller wrapper for ContactBookContactListView providing property pass-through and view lifecycle management. Acts as intermediate layer between parent controllers and contact list view. |
| `ContactBookListPlaceholder.swift` | Reusable placeholder view for empty contact list states with background image, styled labels, and action button. Features responsive layout, theme integration, and configurable content through ViewModel pattern. Used across contact book sections for consistent empty state presentation. |

###### MobileWallet/Screens/Contact Book/List/Views/

| File | Description |
|------|-------------|
| `ContactBookCell.swift` | Reusable table view cell for displaying contact information with address view, favorite indicator, contact type image, and selection support. Features dynamic theme support, edit mode transitions, and tick button for multi-selection during sharing workflows. |
| `ContactBookShareBar.swift` | Horizontal button bar for selecting contact sharing options (QR code or link). Features equally distributed rounded labeled buttons with selection state management and identifier tracking for share type selection. |

###### MobileWallet/Screens/Contact Book/Managers/

| File | Description |
|------|-------------|
| `ContactsManager.swift` | High-level contact management facade coordinating internal contact operations. Exports: ContactsManager class with ContactType enum, Model struct, InternalError enum. Properties: isPermissionGranted, tariContactModels array. Methods: fetchModels(), update(), remove(), createInternalModel(). Dependencies: InternalContactsManager, TariAddress, PaymentInfo. Used by: Contact book screens and transaction flows. Provides abstraction layer over internal contact storage with CRUD operations, permission handling, and contact type classification. Bridges UI models with internal persistence while supporting favorites, aliases, and address components. |
| `InternalContactsManager.swift` | Core contact data management handling persistent storage, wallet contact synchronization, and transaction history address extraction. Manages contact CRUD operations with UserDefaults persistence, deduplication, and sorting. Integrates with TariLib for address validation and transaction data. |

##### MobileWallet/Screens/Debug/

| File | Description |
|------|-------------|
| `DesignSystemViewController.swift` | Design system showcase and testing interface displaying all styled UI components including buttons (primary, secondary, outlined, inherit, text) in various sizes and states, plus typography samples. Features scrollable layout with comprehensive component library for design validation and development reference. Updated to use @TariView property wrapper for view management and improved layout constraints. |
| `UIViewController+DebugMenu.swift` | UIViewController extension adding debug menu functionality triggered by device shake gesture. Provides motion detection override to show debug popup with options: Design System, New Design System (SwiftUI), Logs, Bug Report, Connection Status. Uses PopUpPresenter with TariPopUp components for consistent debug interface. Handles navigation to LogsListConstructor, DesignSystemViewController, new SwiftUI design system, BugReportingConstructor, and AppConnectionHandler connection status. Includes app version display in popup footer. Essential debug tool for developers and testers to access diagnostic features from any screen in the app. |

###### MobileWallet/Screens/Debug/Logs/List/

| File | Description |
|------|-------------|
| `LogViewController.swift` | Individual log file viewer with filtering and search capabilities. Displays log entries in table view with diffable data source, provides filter popup with switch list for log level selection, and handles real-time log content updates with spinner loading states. |
| `LogsListCell.swift` | Table view cell for displaying log file entries in logs list with title label and forward arrow indicator. Features theme-aware styling and consistent layout for navigation to individual log file details. |
| `LogsListConstructor.swift` | Constructor for debug logs list screen following dependency injection pattern. Creates LogsListViewController with LogsListModel for browsing available log files and sharing functionality. |
| `LogsListModel.swift` | Business logic for debug logs management handling log file discovery, selection, sharing, and temporary file cleanup. Integrates with TariLib for log access and BugReportService for log packaging and export functionality. |
| `LogsListView.swift` | Debug screen view for displaying a list of application log files. Extends BaseNavigationContentView with a table view to show available log files and an export button. Key features: title "Logs", close button navigation, right-side export functionality, automatic row height table view with clear background. Used by LogsListViewController for debug log management. Provides onExportButtonTap closure for handling export actions. Part of the debug tools accessible via shake gesture. |
| `LogsListViewController.swift` | Debug screen controller for managing application log files list and export functionality. Extends SecureViewController with LogsListView. Uses Combine for reactive data binding and UITableViewDiffableDataSource for dynamic table updates. Key functions: displays log titles from LogsListModel, handles row selection to show log details, exports logs via share dialog, manages temporary files cleanup. Integrates with LogConstructor for navigation to individual log views. Handles error states via popup presentation. Part of the debug system accessed through device shake gesture. |

###### MobileWallet/Screens/Debug/Logs/Log/

| File | Description |
|------|-------------|
| `LogCell.swift` | Table view cell for displaying individual log entries with multi-line text support and theme styling. |
| `LogConstructor.swift` | Constructor for individual log file viewer screen creating LogViewController with LogModel for specific log file URL. |
| `LogModel.swift` | Business logic for log file processing with filtering, parsing and data management. Handles file reading, keyword filtering by log level and domain with async processing. |
| `LogView.swift` | Debug screen view for displaying individual log file contents. Extends BaseNavigationContentView with table view for log entries, overlay background with Lottie spinner animation for loading states. Key features: dynamic title support, filter button (faucet icon), loading overlay with pendingCircleAnimation, theme-aware styling. Provides onFilterButtonTap closure for filtering functionality. Used by log detail controllers to show specific log file contents with search/filter capabilities. Includes spinner visibility control with fade animations. |

###### MobileWallet/Screens/Home/Home/

| File | Description |
|------|-------------|
| `HomeConstructor.swift` | Simple constructor factory for the main home screen using MVVM pattern. Creates HomeModel instance and injects it into HomeViewController using dependency injection. Part of the consistent constructor pattern used throughout the app for screen creation. Minimal implementation following single responsibility principle for home screen instantiation. |
| `HomeModel.swift` | Home screen business logic model managing wallet dashboard data and state. Provides @Published properties for reactive UI updates: connection status, balances, mining statistics, transactions, and sync progress. Integrates multiple systems: Tari wallet for balance/transactions, APIService for mining data, ContactsManager for transaction formatting, and AppConnectionHandler for connectivity status. Implements sophisticated connection status logic combining network, Tor, base node, and sync states. Manages mining status timer and user avatar generation from wallet address. Critical coordination layer between blockchain services and home UI presentation. |
| `HomeView.swift` | Main home screen interface with wallet card, balance display, active miners view, transaction list and navigation controls. Features balance hiding/showing, sync status, send/receive buttons, QR scanner and dynamic transaction table with placeholder support. |
| `HomeViewController.swift` | Main home screen view controller for the Tari wallet app. Manages wallet dashboard displaying balance, recent transactions, and navigation to core features. Key responsibilities: manages HomeModel for data operations, handles overlay presentations (welcome, restored wallet, synced wallet), coordinates mining status display, manages transaction history integration, handles secure view inheritance, triggers data updates on appearance, executes queued shortcuts, starts UI animations. Dependencies: HomeModel (business logic), HomeView (UI presentation), SecureViewController (security base), OverlayViewController (modal overlays), Combine (reactive updates). Central navigation hub providing access to all wallet functionality and real-time status monitoring. |
| `MinersGradientView.swift` | Custom gradient view displaying mining status and active miners count on the home screen. Features dark green gradient background (0x0E1510 to 0x07160B), rounded corners, miners count with icon, and dynamic mining status indicators. Shows different states based on desktop sync: start mining button for non-synced users, or status labels (green "You're mining" / red "You're not mining") for synced users. Includes StylisedButton for mining initiation and onStartMiningTap closure. Used on main wallet screen to show mining participation and network statistics. Part of the home screen mining interface integration. |
| `VersionBadgeView.swift` | Home screen widget displaying network status and app version information. Extends DynamicThemeView with status circle (red/yellow/green), vertical separator, and version label showing network name and version. Features NetworkStatus enum (connected/connectedWithIssues/disconnected) with corresponding colors, tap gesture to show connection details popup via AppConnectionHandler. Monitors network connection, Tor status, base node connectivity, and sync status. Formats version using AppVersionFormatter and NetworkManager. Provides real-time network health visualization on main wallet screen. |

###### MobileWallet/Screens/Home/Home/Views/

| File | Description |
|------|-------------|
| `HomeBackgroundView.swift` | Home screen background view with gradient top section and animated wave bottom section. Manages background animations through TariGradientView and WaveView components. |
| `HomeGlassView.swift` | Frosted glass visual effect component for home screen UI elements. Simple UIView subclass with semi-transparent white background (20% opacity), rounded corners (10px radius), and subtle white border (40% opacity). Creates modern glassmorphism design effect commonly used in overlay UI elements. Lightweight component focused purely on visual styling with no interactive functionality. Used throughout home screen for creating layered, translucent visual hierarchy. |
| `HomeTransactionsPlaceholderCell.swift` | Table view cell displaying placeholder content when no transactions exist. Extends DynamicThemeCell with centered title and body text (different fonts), "Start Mining Tari" button, and onStartMiningButtonTap closure. Creates formatted attributed string combining localized title and body text with currency symbol from NetworkManager. Fixed height of 200pt with centered layout, clear background, no selection. Used in transaction list to encourage user engagement and mining participation. Theme-aware text coloring and clear visual hierarchy. |
| `HomeTransactionsPlaceholderView.swift` | Standalone view component displaying placeholder content when transaction list is empty. Extends DynamicThemeView with customizable text property, centered title label, and "Start Mining Tari" button. Uses localized placeholder text with currency symbol formatting. Features onStartMiningButtonTap closure for mining initiation. Simpler version of HomeTransactionsPlaceholderCell for non-table contexts. Clear background, centered layout with 10pt spacing between elements. Theme-aware styling for consistent visual integration with app design system. |
| `HomeViewTransactionCell.swift` | Transaction cell for home screen with glass background, icon, title, timestamp and amount display. Features dynamic timestamp updates, theme support and formatted amount display with positive/negative indicators. |
| `MinersGradientView.swift` | Custom gradient view component for displaying mining status and controls on the home screen. Features a green-to-dark gradient background (0x0E1510 to 0x07160B) with 16px corner radius. Displays active miner count with icon, mining status indicator, and start mining button. Conditionally shows different UI states based on desktop synchronization status: when synced to desktop shows mining status labels ('You're mining' in green or 'You're not mining' in red), otherwise shows start mining button. Uses Poppins font family with white text on gradient. Exports: setActiveMiners(), setMiningActive(), onStartMiningTap closure. Dependencies: TariCommon, NotificationManager, StylisedButton. Used by: Home screen for mining integration display. |
| `PopUpNetworkStatusContentView.swift` | Popup content view showing detailed network connection status information. Extends DynamicThemeView with 2x2 grid of StatusView components: network status (internet icon), Tor status (tor icon), base node connection (baseNode icon), and sync status (sync icon). Includes chain tip information with StylizedLabel. Each StatusView has icon background, status dot (red/orange/green), and text label. Provides update methods for each status type and chain tip suffix. Used by AppConnectionHandler for detailed connection diagnostics popup. Features responsive layout with stack views and proper spacing. |
| `PulseLayer.swift` | Core Animation layer creating pulsing circle effects for home screen animations. Extends CAShapeLayer with customizable radius, white stroke color, clear fill. Features combined scale (0.5 to 1.0) and opacity (1.0 to 0.0) animations over 1 second duration, repeating infinitely every 20 seconds. Provides startAnimation() and stopAnimation() methods for control. Used for visual feedback and attention-drawing effects on home screen components. Essential for creating dynamic, engaging user interface animations that enhance the wallet's visual appeal. |
| `PulseView.swift` | Animated pulse view using custom PulseLayer for visual effects. Simple wrapper view managing pulse animation start/stop with configurable radius. |
| `SyncStatusView.swift` | Sync progress indicator view displayed during wallet synchronization. Shows sync status message with theme-aware styling and informs users about disabled actions during sync. |
| `VersionBadgeView 2.swift` | Advanced version badge with dynamic network status monitoring. Extends basic VersionBadgeView with NetworkStatus enum (connected/connectedWithIssues/disconnected) and real-time status updates. Status circle changes color based on network conditions: green for connected, yellow for issues, red for disconnected. Monitors network connection, Tor status, base node connectivity, and sync status through updateNetworkStatus() method. Uses comprehensive status checking logic combining NetworkMonitor, TorConnectionStatus, BaseNodeConnectivityStatus, and TariValidationService.SyncStatus. Similar layout to basic version but with enhanced functionality. Dependencies: TariCommon, TariLib, NetworkMonitor, TorConnectionStatus. Used by: Home screen for comprehensive network monitoring. |
| `VersionBadgeView.swift` | Simple version badge UI component displaying network status indicator and version information. Shows green status circle (8x8px), vertical separator, and formatted version label with network name in primary text color and version number in secondary text color. Uses 10px corner radius with outlined border styling. Styled with Poppins.SemiBold 11pt font. Retrieves version from AppVersionFormatter and network info from NetworkManager.shared.selectedNetwork. Fixed layout with padding and spacing. Dependencies: TariCommon, AppVersionFormatter, NetworkManager. Used by: Home screen for network status display. |
| `WaveView.swift` | Animated wave effect view for home screen background graphics. Uses CAShapeLayer mask with TariGradientView to create flowing wave animation. Features configurable wave width scale (2.5x), amplitude (6% of height), and mathematical sine wave generation. Provides continuous horizontal scrolling animation over 10 seconds with automatic restart. Includes startAnimation() and stopAnimation() controls with animation state tracking. Creates immersive, dynamic background effects that enhance the wallet's visual experience and provide subtle movement feedback. |

###### MobileWallet/Screens/Home/Overlays/

| File | Description |
|------|-------------|
| `DisclaimerView.swift` | Home screen overlay displaying balance explanation and disclaimer information. Shows total balance with currency symbol, separators, and detailed explanations for "Total Balance" vs "Available to spend" concepts. Features rounded container (30px radius), balance properties for dynamic updates, visual separators, and comprehensive balance breakdown. Includes touch gesture handling for dismissal via onCloseButtonTap closure. Educational component helping users understand locked vs available funds, mining rewards, and confirmation states. Essential for user onboarding and financial transparency. |
| `MiningView.swift` | Home screen overlay promoting Tari Universe desktop mining application. Features welcome graphic (402x518), title "Start mining Tari!", description text about downloading desktop app, and "Open Download Link" button. Rounded container design (30px radius) with proper spacing and centered layout. Includes onSendMiningLinkTap closure for handling download link actions. Theme-aware with automatic updates on appearance changes. Encourages mining participation by directing users to desktop mining solution, crucial for network growth and user engagement. |
| `NotificationsView.swift` | Home screen overlay requesting notification permissions from users. Features notifications graphic (387x133), title "Never miss a moment!", description about mining updates and wins, "Allow notifications" primary button, and "Maybe later" text button. Rounded container design (30px radius) with proper spacing. Includes onPromptButtonTap and onCloseButtonTap closures for handling user choice. Theme-aware with automatic appearance updates. Essential for improving user engagement by enabling push notifications for mining rewards and important wallet events. |
| `OverlayView.swift` | Central overlay management view supporting multiple overlay types: notifications, restored, synced, startMining, disclaimer. Features dark blur background, gesture handling for dismissal, and coordinate access to WelcomeView, NotificationsView, MiningView, and DisclaimerView components. Provides showOverlay(for:animated:) method for switching between overlays with proper visibility management. Includes auto-dismissal timer for synced/restored overlays (5 seconds). Implements UIGestureRecognizerDelegate to prevent accidental dismissal on interactive elements. Essential overlay system for user onboarding and important notifications. |
| `OverlayViewController.swift` | Controller managing overlay presentation with custom fade animations. Extends SecureViewController with OverlayView. Features FadeInAnimator and FadeOutAnimator for smooth transitions (0.45s duration). Manages overlay lifecycle including disclaimer UserDefaults tracking, notification prompting logic, and automatic overlay chaining (restored/synced → notifications). Implements UIViewControllerTransitioningDelegate for custom animations. Handles balance data for disclaimer overlay and coordinates with NotificationManager for permission requests. Essential for guided user experience and feature introduction. |
| `WelcomeView.swift` | Welcome overlay for wallet restoration and sync completion. Features welcome graphic (402x518), dynamic title and description based on isPaperWalletRestored flag. Shows either "Welcome back!" with restoration message or "Wallet Synced! 🎉" with balance availability message including currency symbol. Rounded container design (30px radius) with theme-aware styling. Essential for user feedback during onboarding, restoration, and sync processes. Provides positive reinforcement and clarity about wallet state to improve user confidence. |

###### MobileWallet/Screens/Home/Transaction Details/

| File | Description |
|------|-------------|
| `TransactionDetailsCell.swift` | Table view cell for transaction details rows implementing DynamicThemeCell with comprehensive configuration options. Wrapper around DetailView component that handles all transaction detail display logic. Supports multiple cell types through properties: address cells with emoji/raw format toggling, contact cells with add/edit buttons, block explorer cells with external links, and standard value cells with copy functionality. Manages callbacks for various actions: copy operations, contact management, address format switching, and block explorer navigation. Fixed height of 74px with 22px horizontal margins. Implements proper cell reuse cleanup with prepareForReuse. Dependencies: TariCommon, DetailView component, DynamicThemeCell protocol. Used by: TransactionDetailsViewController for displaying transaction information rows. |
| `TransactionDetailsConstructor.swift` | Factory class for creating transaction detail views with SwiftUI migration support. Currently builds UIHostingController wrapping SwiftUI TransactionDetails view instead of legacy UIKit TransactionDetailsViewController. Includes commented legacy code for rollback capability during SwiftUI transition testing phase. |
| `TransactionDetailsModel.swift` | Enhanced transaction details business logic model with payment reference support and improved contact management. Features payment reference confirmation tracking, wallet block height monitoring, raw transaction details generation, and streamlined contact alias handling. Adds confirmation count calculations, block height tracking via Combine publishers, and comprehensive transaction data formatting for both UIKit and SwiftUI implementations. |
| `TransactionDetailsView.swift` | View component for transaction details screen implementing DynamicThemeView protocol. Simple container view with navigation bar and theme-aware background. Manages title property through NavigationBar component and handles theme updates by setting background to secondary color. Minimal view structure serving as container for TransactionDetailsViewController's table view content. Exports: title property getter/setter. Dependencies: TariCommon, NavigationBar component, DynamicThemeView protocol. Used by: TransactionDetailsViewController as main view container. |
| `TransactionDetailsViewController.swift` | Enhanced UIKit transaction details controller with payment reference support and raw details copying. Features additional table view cells for payment reference display, confirmation status tracking, copy raw details button, and improved address/note handling. Implements dynamic cell counting based on transaction type, payment reference confirmation states, and comprehensive copy functionality with proper truncation and validation. |
| `TransactionTotalCell.swift` | Transaction details table view cell that displays the total transaction amount. Extends DynamicThemeCell with automatic theme support. Contains a "Total" label on the left and a configurable total value label on the right with semibold Poppins font. Uses Auto Layout constraints with 22pt horizontal margins and fixed 48pt height. Exports: TransactionTotalCell class, totalText property. Dependencies: TariCommon, UIKit. Used by: Transaction details screen table view. Updates text color based on current theme. |

###### MobileWallet/Screens/Home/Transaction Details/SwiftUI/

| File | Description |
|------|-------------|
| `CopyRawDetailsButton.swift` | SwiftUI button component for copying raw transaction details with visual feedback. Features TariButton with outlined style, dynamic icon switching between copy and checkmark states, and implements Copying protocol for feedback animation. Uses @State for copy status tracking and provides action callback for raw transaction data copying functionality. |
| `PaymentReferenceInfoSheet.swift` | SwiftUI modal sheet explaining payment reference functionality. Features modal title, descriptive text about payment ID privacy protection, and close button. Uses presentationDetents for fixed height presentation and provides user education about payment reference lookup capabilities while maintaining privacy. |
| `TransactionDetails+Actions.swift` | SwiftUI transaction details business logic extension with comprehensive transaction data processing. Features transaction status determination, confirmation counting, payment reference handling, contact management, and raw details generation. Implements real-time transaction updates via Combine publishers, address formatting, block explorer integration, and transaction lifecycle tracking with proper error handling. |
| `TransactionDetails.swift` | SwiftUI transaction details view with comprehensive transaction information display. Features scrollable transaction details, copy functionality, contact editing, payment reference info, and real-time updates. Uses NavigationStack with toolbar, sheet presentations for editing, and reactive state management. Displays transaction amounts, addresses, payment references, contact names, dates, block heights, and status with proper formatting and user interactions. |

###### MobileWallet/Screens/Home/Transaction Details/Views/

| File | Description |
|------|-------------|
| `DetailView.swift` | Reusable transaction detail view component for displaying key-value pairs with interactive buttons. Extends DynamicThemeView supporting title/value display, copy functionality, add contact, edit contact, block explorer navigation, and address format toggle. Features configurable action buttons (copy, add contact, edit, block explorer), emoji/text address format switching with animated button interactions. Exports: DetailView class, action callbacks, format properties. Dependencies: TariCommon, UIKit. Used by: Transaction details screen, address display components. Includes proper memory management with cleanup method. |
| `TransactionDetailsBlockExplorerView.swift` | Block explorer navigation button component for transaction details. Extends DynamicThemeBaseButton with localized "View in Block Explorer" text and forward arrow icon. Uses consistent 22pt horizontal margins and 11pt vertical padding. Follows Tari copyright headers and licensing. Exports: TransactionDetailsBlockExplorerView class. Dependencies: UIKit, TariCommon, Theme system. Used by: Transaction details screen for external block explorer navigation. Theme-aware colors for text and icon tinting. |
| `TransactionDetailsContactView.swift` | Specialized view for editing contact names in transaction details. Contains disabled text field with contact name placeholder and edit button for enabling contact editing. Text field has 40 character limit, disabled autocorrection, and uses theme fonts. Simple horizontal layout with text field on left and edit button on right. Theme-aware text color updates. Exports: textField and editButton for external configuration. Dependencies: TariCommon, DynamicThemeView, TextButton component, localized strings. Used by: Transaction details views for contact name editing functionality. |
| `TransactionDetailsEmojiView.swift` | Specialized view component for displaying transaction address information with emoji representation. Contains AddressView component for emoji address display and TextButton for adding contacts. Fixed height of 85px with structured layout - address view at top, add contact button below with 10px spacing. Manages addressViewModel property that updates AddressView content, and forwards onViewDetailsButtonTap events from AddressView. Uses localized strings for button labels. Designed for transaction details screens where address information needs prominent emoji display. Dependencies: TariCommon, AddressView component, TextButton component. Used by: Transaction details views for address presentation with contact management integration. |
| `TransactionDetailsNoteView.swift` | Transaction note display component supporting both text and GIF content. Extends DynamicThemeView with multi-line note label and GPHMediaView for GIF attachments. Features dynamic height adjustment for GIF aspect ratio, corner radius styling, and automatic layout constraints. Exports: TransactionDetailsNoteView class, note/gifMedia properties. Dependencies: UIKit, TariCommon, GiphyUISDK. Used by: Transaction details screen for displaying transaction messages and GIF attachments. Includes proper constraint management for dynamic content sizing. |
| `TransactionDetailsSectionView.swift` | Generic section container view for transaction details using Swift generics. Extends DynamicThemeView with title label and generic content view of type T. Features 20pt top padding for section spacing and 22pt horizontal margins for title. Exports: TransactionDetailsSectionView<T> class, title property, contentView accessor. Dependencies: UIKit, TariCommon, Theme system. Used by: Transaction details screen for organizing different detail sections with consistent styling. Provides flexible content container pattern for various detail view types. |
| `TransactionDetailsSeparatorView.swift` | Simple visual separator view for transaction details sections. Extends DynamicThemeView with a 1pt height divider using theme neutral tertiary color. Features 22pt horizontal margins matching other detail views. Follows Tari copyright headers and licensing. Exports: TransactionDetailsSeparatorView class. Dependencies: UIKit, TariCommon. Used by: Transaction details screen for visual section separation. Theme-aware background color updates automatically. |
| `TransactionDetailsValueView.swift` | Value display view for transaction amounts with optional fee information. Shows currency symbol icon, large transaction value label with font scaling (minimum 0.2x), and conditional fee label with info button. Uses dynamic layout constraints - when fee is present, shows fee label and button below value; when fee is null, hides fee elements and adjusts spacing. Currency symbol positioned left of value, all elements centered horizontally. Theme-aware colors for background (secondary), icons, and text (heading color). Exports: valueLabel, feeButton, and fee property. Dependencies: TariCommon, DynamicThemeView, TextButton, theme fonts and images. Used by: Transaction details for prominent amount display with fee breakdown. |

###### MobileWallet/Screens/Home/Transaction Details/WaveEmojiAnimation/

| File | Description |
|------|-------------|
| `WaveEmojiAnimation.json` | [GENERATED] Lottie animation file defining wave emoji animation for transaction details. 60fps animation lasting 231 frames (3.85 seconds) with 140x139px dimensions. Contains opacity fade-in from 0-100% over frames 0-57, rotation animation from 0° to 15° to -15° to 15° to 0° over frames 30-230, and fade-out from 100-0% over frames 549-599. References external wave image asset (img_0.png). Uses 50% scale and positioned at coordinates [91.5, 97.5]. Configured with standard easing curves for smooth transitions. Used by: Transaction details screens for celebratory wave animation effects during transaction completion or success states. |

###### MobileWallet/Screens/Home/Transaction History/

| File | Description |
|------|-------------|
| `TransactionHistoryConstructor.swift` | Dependency injection constructor for transaction history screen using factory pattern. Creates TransactionHistoryViewController with TransactionHistoryModel dependency. Follows MVVM architecture pattern with constructor injection. Exports: TransactionHistoryConstructor enum, buildScene() static method. Dependencies: TransactionHistoryModel, TransactionHistoryViewController. Used by: Navigation flow for creating transaction history screen. Follows Tari copyright headers and clean architecture patterns. |
| `TransactionHistoryModel.swift` | MVVM model for transaction history with Combine-based reactive data flow. Manages pending/completed transaction lists with search functionality and real-time updates. Features TransactionsSection structure, transaction formatting via TransactionFormatter, and automatic sorting by timestamp. Exports: TransactionHistoryModel class, published properties, TransactionsSection struct. Dependencies: Combine, Tari wallet system, TransactionFormatter. Used by: TransactionHistoryViewController. Includes contact data updates and transaction filtering with search text. |
| `TransactionHistoryView.swift` | Transaction history view with search functionality and sectioned table display. Extends BaseNavigationContentView with search field, table view using UITableViewDiffableDataSource, and section headers. Features keyboard dismissal, automatic row sizing, and interactive cell tapping. Exports: TransactionHistoryView class, ViewModel struct, onCellTap callback. Dependencies: TariCommon, Combine, TransactionHistoryCell, MenuTableHeaderView. Used by: TransactionHistoryViewController. Includes search text binding and dynamic content updates with animations. |
| `TransactionHistoryViewController.swift` | Main view controller for transaction history screen using MVVM pattern. Extends SecureViewController with TransactionHistoryView and Combine-based data binding. Handles navigation to transaction details, search text synchronization, and automatic data refresh on view appearance. Exports: TransactionHistoryViewController class. Dependencies: UIKit, Combine, TransactionHistoryModel, TransactionHistoryView, TransactionDetailsConstructor. Used by: Main navigation flow. Maps model data to view models and coordinates user interactions with business logic. |

###### MobileWallet/Screens/Home/Transaction History/Views/

| File | Description |
|------|-------------|
| `TransactionHistoryCell.swift` | Complex table view cell for displaying transaction history items with dynamic content. Extends DynamicThemeCell with ViewModel struct containing transaction data (ID, title components, timestamp, info, note, GIF ID, amount). Features StylizedLabel for formatted titles, timestamp display with TransactionDynamicModel, optional info/note labels, AmountBadge for amounts, and GIF support via GPHMediaView with loading states. Exports: TransactionHistoryCell class, ViewModel struct, onContentChange callback. Dependencies: TariCommon, GiphyUISDK, Combine, TransactionDynamicModel, AmountBadge. Used by: Transaction history table view. Includes proper cell reuse preparation and memory management. |

###### MobileWallet/Screens/Home/Transaction List/

| File | Description |
|------|-------------|
| `TxTableViewCell.swift` | Transaction table view cell fragment containing amount label update logic. Single function updateAmountLabel() that formats transaction amount with currency symbol using NetworkManager. Exports: updateAmountLabel() method. Dependencies: NetworkManager currency system. Used by: Transaction list table view cells. Appears to be a code fragment rather than complete file - may be part of larger cell implementation. |

###### MobileWallet/Screens/Home/UTXOs Wallet/

| File | Description |
|------|-------------|
| `UTXOsWalletConstructor.swift` | Dependency injection constructor for UTXO wallet screen using factory pattern. Creates UTXOsWalletViewController with UTXOsWalletModel dependency following MVVM architecture. Simple buildScene() static method for clean dependency injection. Exports: UTXOsWalletConstructor enum, buildScene() static method. Dependencies: UTXOsWalletModel, UTXOsWalletViewController. Used by: Navigation flow for creating UTXO management screen. Follows Tari copyright headers and clean architecture patterns. |
| `UTXOsWalletModel.swift` | Comprehensive MVVM model for UTXO wallet management with reactive data binding. Manages UTXO selection, sorting (4 methods), breaking/combining operations with fee preview, and real-time FFI integration. Features UtxoModel with amount/height calculations, BreakPreviewData for transaction previews, and UtxoStatus enum with UI properties. Handles complex UTXO operations like coin breaking/combining, dynamic tile height calculations (100-300pt range), and network block height validation. Exports: UTXOsWalletModel class, SortMethod enum, UtxoModel/BreakPreviewData structs. Dependencies: UIKit, Combine, Tari wallet FFI, MicroTari formatting. Used by: UTXOsWalletViewController. Includes comprehensive error handling and validation logic. |
| `UTXOsWalletView.swift` | Main view controller for UTXO wallet management with dual display modes (tiles/text). Extends BaseNavigationContentView with comprehensive UTXO visualization, selection controls, contextual actions, and view state management. Features ListType/VisibleContentType/ActionType enums, multiple view controllers (tile list, text list, placeholder, loading), and ContextualButtonsOverlay integration. Supports break/combine operations with editing modes and animated transitions. Exports: UTXOsWalletView class, various enums, published properties for state management. Dependencies: TariCommon, Combine, multiple UTXO view components. Used by: UTXOsWalletViewController. Includes sophisticated state management and user interaction handling. |
| `UTXOsWalletViewController.swift` | Comprehensive UTXO management view controller implementing MVVM with Combine bindings. Manages UTXO tile and text list views, handles sorting (amount/date ascending/descending), selection states, and UTXO operations (split/combine/combine-split). Features complex popup system for confirmations, details, and operation configurations. Supports dual display modes: visual tiles and text list with real-time data binding. Handles UTXO operations: breaking (splitting) single UTXOs into multiple pieces, combining multiple UTXOs into one, and hybrid combine-break operations. Comprehensive popup dialogs for operation confirmation, progress tracking, and success feedback. Value picker integration for split count selection with fee estimation preview. Dependencies: UTXOsWalletModel, Combine, PopUpPresenter, various specialized popup components. Used by: Home screen for advanced UTXO coin control management. |

###### MobileWallet/Screens/Home/UTXOs Wallet/Other/

| File | Description |
|------|-------------|
| `UTXOsWalletTileListLayout.swift` | Custom UICollectionViewLayout for UTXO tile display with dynamic column-based masonry layout. Implements waterfall layout algorithm with configurable columns, balanced height distribution, and custom tile sizing. Features ColumnData structure for tracking heights and attributes, onCheckHeightAtIndex callback for dynamic sizing, and consistent margins (30pt horizontal, 12pt vertical/internal). Exports: UTXOsWalletTileListLayout class, columnsCount property, height callback. Dependencies: UIKit. Used by: UTXO wallet tile collection view. Optimizes visual space usage for varying UTXO amounts and tile heights. |

###### MobileWallet/Screens/Home/UTXOs Wallet/Views/

| File | Description |
|------|-------------|
| `ContextualButton.swift` | Expandable contextual action button with text label and icon. Extends BaseButton with collapsible layout, right-aligned text (Poppins Medium 17pt), and 20x20pt icon. Features isExpanded property controlling label visibility and layout constraints, automatic tint color propagation to subviews. Exports: ContextualButton class, isExpanded property, update() method. Dependencies: UIKit, TariCommon. Used by: ContextualButtonsOverlay for UTXO wallet actions. Supports smooth expand/collapse animations and consistent 15pt margins. |
| `ContextualButtonsOverlay.swift` | Floating overlay displaying contextual UTXO action buttons with auto-collapse functionality. Extends DynamicThemeView with ButtonModel structure, vertical stack layout, 10pt corner radius container, and 3-second auto-collapse timer. Features async hide/show animations (0.3s duration), spring-damped collapse animation, separator views between buttons, and theme-aware styling with 90% opacity overlay. Exports: ContextualButtonsOverlay class, ButtonModel struct, setup() method. Dependencies: UIKit, TariCommon, ContextualButton. Used by: UTXO wallet for break/combine action display. Includes sophisticated timing and animation management. |
| `PopUpCombineUTXOsConfirmationContentView.swift` | UTXO combine operation confirmation popup content view. Extends DynamicThemeView with center-aligned confirmation message (Poppins Medium 14pt, multi-line) and UTXOsEstimationLabel for fee display with currency symbol formatting. Features localized fee text with attributed string formatting including currency symbols with specific image bounds (8x8pt). Exports: PopUpCombineUTXOsConfirmationContentView class, messageText/feeText properties. Dependencies: UIKit, TariCommon, UTXOsEstimationLabel. Used by: UTXO wallet combine confirmation popups. Includes automatic currency symbol integration and theme-aware text coloring. |
| `PopUpUTXOsBreakContentView.swift` | UTXO break operation popup content with value picker and estimation display. Extends DynamicThemeView with Combine-based reactive UI, ValuePickerView and UISlider (range 2-50) for break count selection, synchronized value updates between picker/slider, and UTXOsEstimationLabel for cost preview. Features localized description, currency symbol formatting with 8x8pt image bounds, and automatic estimation updates. Exports: PopUpUTXOsBreakContentView class, published value property, update() method. Dependencies: UIKit, TariCommon, Combine, ValuePickerView, UTXOsEstimationLabel. Used by: UTXO wallet break operation popups. Includes purple-themed slider and proper value synchronization. |
| `PopUpUtxoDetailsContentView.swift` | UTXO details popup displaying comprehensive information about individual UTXOs. Extends DynamicThemeView with Model struct containing amount, status, commitment, block height, and date. Features prominent CurrencyLabelView amount display (Poppins Bold 26pt, 13pt icon), vertical stack of detail rows with PopUpUtxoContentRowView components, conditional visibility for optional fields, and status color indicators. Includes private PopUpUtxoContentRowView class with title/value display, status indicator dot (10x10pt, 5pt corner radius), and multi-line value support. Exports: PopUpUtxoDetailsContentView class, Model struct, update() method. Dependencies: UIKit, TariCommon, CurrencyLabelView, UtxoStatus. Used by: UTXO wallet detail popups. |
| `UTXOTileView.swift` | Visual tile representation of individual UTXOs for collection view display. Features dynamic height tiles with color-coded backgrounds based on UTXO hash, currency amount display with large font (Poppins Black 30pt), status icons, date labels, and selection tick buttons. Implements hash-based color generation for unique visual identification. Complex custom shape layer for bottom-right corner status indicator using CAShapeLayer with circular arc. Supports selection states with shadow effects and tick button animations. Gesture handling for tap and long-press interactions. Theme-aware styling with status-specific coloring. Fixed 10px corner radius with 2px margins. Model includes: UUID, amount text, hash, height, status, date, selectability. Dependencies: TariCommon, DynamicThemeCollectionCell, CurrencyLabelView, TickButton. Used by: UTXO wallet for visual UTXO grid display. |
| `UTXOsEstimationLabel.swift` | Styled label component for displaying UTXO operation cost estimations. Extends DynamicThemeView with center-aligned, multi-line UILabel (Poppins Medium 12pt), 5pt corner radius container, and 10pt vertical padding with 2pt horizontal margins. Features attributed text support for rich formatting including currency symbols and formatted estimation data. Exports: UTXOsEstimationLabel class, attributedText property. Dependencies: UIKit, TariCommon. Used by: UTXO break/combine popup content views for fee and cost estimation display. Provides consistent styling for financial information with theme-aware background and text colors. |
| `UTXOsWalletLoadingView.swift` | Loading state view for UTXO wallet with animated spinner. Extends DynamicThemeView with vertical stack layout containing Lottie AnimationView (pendingCircleAnimation, 42pt height, loop mode, auto-play) and localized title label (Poppins SemiBold 14pt, center-aligned). Features background behavior pause/restore for animation lifecycle management and centered layout. Exports: UTXOsWalletLoadingView class. Dependencies: UIKit, TariCommon, Lottie framework. Used by: UTXO wallet during data loading states. Provides smooth animation feedback with theme-aware text styling. |
| `UTXOsWalletPlaceholderView.swift` | Empty state placeholder view for UTXO wallet when no UTXOs are available. Extends DynamicThemeView with vertical stack layout containing placeholder image (120pt height), localized title (Poppins Light 18pt), and subtitle (Poppins Medium 14pt, multi-line). Features centered layout with 67% max width/height constraints and consistent 10pt spacing. Uses .Images.UTXO.placeholder image with scale aspect fit. Exports: UTXOsWalletPlaceholderView class. Dependencies: UIKit, TariCommon. Used by: UTXO wallet for empty state display. Includes theme-aware image tinting and text coloring with proper contrast hierarchy. |
| `UTXOsWalletTextListView.swift` | UTXO table view display with editing capabilities and selection management. Extends DynamicThemeView with UITableView using UITableViewDiffableDataSource, Combine-based reactive properties for models/editing/selection, and scroll tracking. Features UTXOsWalletTextListViewCell registration, content inset management, selection state synchronization, and animated editing transitions with semi-transparent disabled states. Exports: UTXOsWalletTextListView class, published properties, onTapOnCell callback. Dependencies: UIKit, TariCommon, Combine, UTXOsWalletTextListViewCell. Used by: UTXO wallet text list mode. Includes proper cell reuse handling and 30pt separator insets with fade animations. |
| `UTXOsWalletTextListViewCell.swift` | Table view cell for displaying UTXO information in text list format. Extends DynamicThemeCell with Model struct containing ID, amount, status, status text, hash, and selectability. Features amount label (Poppins Bold 15pt), hash display (Poppins SemiBold 12pt), status indicator with colored circle (8pt diameter) and text, TickButton for selection, and animated editing mode transitions. Includes constraint switching between normal/editing layouts with 30pt margins and proper status color theming. Exports: UTXOsWalletTextListViewCell class, Model struct, update methods. Dependencies: UIKit, TariCommon, TickButton, UtxoStatus. Used by: UTXOsWalletTextListView. Supports semi-transparent disabled states and smooth selection animations. |
| `UTXOsWalletTileListView.swift` | UTXO collection view display using masonry tile layout with editing capabilities. Features UICollectionView with UTXOsWalletTileListLayout (2 columns mobile, 4 iPad), UICollectionViewDiffableDataSource, and Combine-based reactive properties. Manages UTXOTileView cells with selection/editing states, tap/long press handlers, and automatic tile height calculation via layout callbacks. Includes dynamic column configuration, content inset management, and proper selection state synchronization. Exports: UTXOsWalletTileListView class, published properties, tile interaction callbacks. Dependencies: UIKit, TariCommon, Combine, UTXOsWalletTileListLayout, UTXOTileView. Used by: UTXO wallet tile display mode. Features smooth scroll tracking and animated tile updates. |
| `UTXOsWalletTopBar.swift` | Top toolbar for UTXO wallet with filter and edit controls. Extends BaseToolbar with LeftImageButton for filtering (faucet icon, Poppins SemiBold 14pt) and BaseButton for edit mode toggling. Features dynamic height constraint management, edit mode state tracking with localized button text switching (Select/Cancel), and 32pt horizontal margins with 44pt button heights. Exports: UTXOsWalletTopBar class, filter title property, interaction callbacks, isEditingEnabled state. Dependencies: TariCommon, LeftImageButton, BaseButton. Used by: UTXO wallet top navigation area. Includes theme-aware icon/text coloring and proper callback handling for filter/edit actions. |
| `ValuePickerView.swift` | Integer value picker control with increment/decrement buttons for UTXO splitting operations. Features minus/plus buttons with 9px corner radius background circles, center value label (Poppins Bold 22pt), and enforced min/max value constraints. Button states change color based on boundaries: purple when active, inactive gray when at limits. Fixed width layout (70px for value label) with 8px button spacing. Value updates trigger onValueChanged callback for real-time fee estimation. Fixed height of 34px. Theme-aware button and text coloring. Prevents values outside defined range through max/min enforcement. Dependencies: TariCommon, DynamicThemeView, BaseButton. Used by: UTXO splitting dialogs for selecting number of output pieces. |

##### MobileWallet/Screens/PasswordVerification/

| File | Description |
|------|-------------|
| `PasswordVerificationViewController.swift` | Password verification view controller for secure wallet operations. Supports two modes: changing backup password (.change) and restoring wallet from backup (.restore). Features adaptive UI with scroll view, password field with validation, theme-aware styling, and keyboard management. Uses PasswordField with confirmation validation and SecureBackupViewController navigation for password changes. Includes proper constraint handling for keyboard appearance and dynamic text styling based on verification mode. Implements PasswordFieldDelegate for real-time validation and UIScrollViewDelegate for modal presentation control. |

##### MobileWallet/Screens/Profile/

| File | Description |
|------|-------------|
| `NewProfileModel.swift` | Business logic model for new profile screen with Yat integration. Manages user authentication states (Initial, Loading, LoggedOut, Profile, Error) using Combine publishers. Integrates with UserManager for Yat authentication and handles deep link authentication failures. Features error handling with localized MessageModel responses, automatic state updates via Combine subscriptions, and comprehensive notification handling for logout and authentication failures. Supports profile name validation and user info updates through YatLib integration. |
| `NewProfileView.swift` | Comprehensive profile view with Yat Universe integration and mining statistics. Contains InviteView with gradient background and referral link sharing, GaugeView for displaying mining stats and gems earned, and main NewProfileView with login/logout functionality. Features responsive layout with username display, mining/gems gauges, invite component, and loading states. Uses @View property wrapper pattern, custom fonts (Poppins family), and dynamic theming. Supports airdrop account connection, profile data binding, and share functionality for referral codes. Includes both authenticated and unauthenticated states with appropriate UI transitions. |
| `ProfileViewController.swift` | User profile view controller managing Tari airdrop integration and user authentication state. Implements WKNavigationDelegate for web view integration and handles login/logout functionality with token management. Features real-time wallet balance display, referral code sharing via UIActivityViewController, and external airdrop website integration. State management includes LoggedOut, Error, Initial, Loading, and Profile states with corresponding UI updates. Handles UserManager token clearing on logout and supports profile data updates. Integrates with YatLib and wallet balance subscriptions. Dependencies: Combine, YatLib, WebKit, NewProfileModel, NewProfileView, UserManager, Tari wallet. Used by: Main navigation for user profile and airdrop functionality. |

###### MobileWallet/Screens/Profile/RequestTari/

| File | Description |
|------|-------------|
| `RequestTariAmountModel.swift` | Business logic for requesting Tari payments with QR code and deep link generation. Uses AmountNumberFormatter for real-time amount validation and formatting. Creates TransactionsSendDeeplink with user's wallet address and requested amount. Features QR code generation via QRCodeFactory, share dialog preparation with localized messages, and deep link URL creation. Publishes amount changes, validation state, and generated content through Combine. Integrates with Tari wallet FFI for address retrieval and MicroTari conversion for amount handling. |
| `RequestTariAmountView.swift` | Custom view for Tari payment request interface with animated amount display and custom keyboard. Features AnimatedBalanceLabel with typing animation, AmountKeyboardView for numeric input, and action buttons for QR generation and sharing. Uses dynamic theming, custom currency symbol attachment, and responsive layout with different constraints for iPad vs iPhone. Implements button state management based on amount validation and proper Safe Area layout guides. Includes gem currency symbol integration and theme-aware text coloring for optimal user experience. |
| `RequestTariAmountViewController.swift` | View controller coordinator for Tari payment request flow. Binds RequestTariAmountModel and RequestTariAmountView using Combine framework. Handles keyboard input delegation, QR code popup presentation via PopUpPresenter, and native share dialog with UIActivityViewController. Features reactive UI updates for amount changes, button state management, and completion handlers for QR generation and sharing actions. Implements comprehensive error handling and user interaction flow for payment request generation. |

##### MobileWallet/Screens/QR Code Scanner/

| File | Description |
|------|-------------|
| `QRCodeScannerConstructor.swift` | Factory constructor for QR code scanner with dependency injection. Creates QRCodeScannerViewController with configured VideoCaptureManager, expected data types, and disabled data types. Handles camera session setup and error propagation. Supports multiple QR code data types including deep links, Tor bridges, and base64 addresses. Provides clean separation of concerns by handling all dependency creation and configuration in a single factory method. |
| `QRCodeScannerModel.swift` | Comprehensive QR code scanning business logic with multi-format support. Handles deep links (transactions, base nodes, contacts, profiles), Tor bridges, and base64 addresses. Features expected/disabled data type filtering, transaction formatting with contact lookup, and completion action management. Integrates with VideoCaptureManager for camera input, TransactionFormatter for contact resolution, and DeeplinkHandler for action routing. Supports validation, error handling, and user confirmation flows for unexpected QR code types. Publishes action models and completion events through Combine framework. |
| `QRCodeScannerView.swift` | Full-screen QR code scanner interface with camera preview and action management. Features AVCaptureVideoPreviewLayer for live camera feed, animated QRCodeScannerBoxView for scan target, and dynamic action presentation with approve/cancel buttons. Implements overlay UI with title content, bottom action area, and semi-transparent backgrounds. Supports different action types (normal/error) with appropriate styling and button visibility. Uses comprehensive Auto Layout with safe area constraints and proper camera session integration. Includes close button, action labels, and smooth animation transitions for user feedback. |
| `QRCodeScannerViewController.swift` | QR code scanner view controller implementing AVFoundation video capture for multi-format QR code scanning. Handles expected data types (deep links, profile data, base64 addresses) with validation and error handling. Features scanning confirmation dialogs with approve/cancel actions, automatic video session management (start on appear, stop on disappear), and completion callback handling for both expected and unexpected data types. Integrates with QRCodeScannerModel for business logic and QRCodeScannerView for UI presentation. Supports secure video capture session injection for testing. Exports onExpectedDataScan callback for successful scans. Dependencies: AVFoundation, Combine, QRCodeScannerModel, QRCodeScannerView, QRCodeData types. Used by: Transaction sending, contact importing, address input workflows. |

###### MobileWallet/Screens/QR Code Scanner/Views/

| File | Description |
|------|-------------|
| `QRCodeScannerBoxView.swift` | Custom visual QR code scanning target with animated corner borders. Draws white rounded corner brackets using CAShapeLayer with bezier path calculations. Features responsive corner length based on view dimensions (25% of smaller dimension) and smooth line caps/joins. Automatically updates border path on layout changes with proper bounds calculation. Provides visual feedback for QR code scanning area with professional appearance and clear scanning boundaries. |

##### MobileWallet/Screens/Receive/

| File | Description |
|------|-------------|
| `ReceiveConstructor.swift` | Simple factory constructor for receive screen. Creates ReceiveViewController instance with basic dependency injection pattern. Provides centralized object creation following the constructor pattern used throughout the app for view controller instantiation and dependency management. |
| `ReceiveModel.swift` | Business logic model for receive payment functionality providing QR code generation and address management. Generates payment request QR codes containing deep links, manages wallet address formats (base64 and emoji), and handles share functionality for payment requests. Creates deep links using TransactionsSendDeeplink model and QRCodeFactory for QR generation. Provides @Published properties for reactive UI updates and integrates with Tari wallet for address retrieval. Essential for payment request workflow enabling users to share payment information via QR codes or direct links. Dependencies: TariCommon, QRCodeFactory, DeepLinkFormatter. |
| `ReceiveView.swift` | Comprehensive wallet receive interface with QR code display and address sharing. Features QRCodeView with gem icon overlay, address container with emoji and base58 address formats, copy buttons for both address types, and share functionality. Uses responsive layout with dark/light mode theme support, dynamic network labeling, and custom shadows. Integrates with NetworkManager for currency symbols and network information. Supports QR code loading states and proper visual feedback with custom corner radius and shadow effects. |
| `ReceiveViewController.swift` | Receive screen controller for displaying wallet payment request interface with QR codes and address sharing. Extends SecureViewController for screenshot protection of wallet addresses. Manages QR code generation, address display (both base64 and emoji formats), and share functionality for payment requests. Provides copy-to-clipboard functionality with user feedback and integrates with ReceiveModel for business logic. Handles deep link sharing for payment requests and coordinates address format presentation. Essential for receiving Tari payments with user-friendly QR codes and address sharing. Dependencies: ReceiveView for UI, ReceiveModel for data management, TariCommon for utilities. |

##### MobileWallet/Screens/RestoreWallet/

| File | Description |
|------|-------------|
| `RestoreWalletModel.swift` | Business logic for wallet restoration from various sources. Manages paper wallet recovery flow with confirmation, password entry, and progress tracking. Integrates with SeedWordsWalletRecoveryManager for wallet operations and handles PaperWalletDeeplink processing. Features action-driven state management (confirmation, password form, progress), NotificationManager integration for app ID setting, and comprehensive error handling through Combine publishers. Supports wallet deletion and cipher management for secure recovery processes. |
| `RestoreWalletViewController.swift` | Main wallet restoration controller with multiple restore options and comprehensive error handling. Supports desktop sync, iCloud restore, Dropbox restore, and seed phrase restore. Features table view interface, QR scanner integration for paper wallets, biometric authentication, and PendingView overlay for long operations. Implements backup service integration, password verification flows, recovery progress tracking, and navigation to onboarding screens. Handles various error types (BackupError, DropboxBackupError, ICloudBackupError) with appropriate user feedback and recovery flows. |

##### MobileWallet/Screens/RestoreWalletFromSeeds/

| File | Description |
|------|-------------|
| `RestoreWalletFromSeedsModel.swift` | Advanced seed phrase wallet restoration with intelligent autocompletion and validation. Features TokenViewModel for UI representation, comprehensive input validation, and real-time autocompletion from Tari seed word list. Supports custom base node configuration, multi-token input parsing, and dynamic state management (valid/invalid/editing). Integrates with SeedWordsWalletRecoveryManager for wallet recovery operations. Uses Combine for reactive UI updates and complex business logic including text tokenization, autocomplete filtering, and error handling for base node validation. |
| `RestoreWalletFromSeedsView.swift` | User interface for seed phrase wallet restoration with keyboard avoidance and custom base node selection. Features KeyboardAvoidingContentView for proper keyboard handling, TokenCollectionView for seed word input, and optional base node configuration. Uses responsive layout with dynamic button state management and comprehensive constraint system. Supports first responder management and custom base node feature toggling through TariSettings flags. |
| `RestoreWalletFromSeedsViewController.swift` | Wallet restoration view controller implementing seed phrase input and validation using MVVM with Combine. Features interactive token collection view with autocomplete, custom base node selection, and progress overlay integration. Handles seed word input validation, autocompletion suggestions, and removal operations. Supports custom base node configuration via FormOverlayPresenter and wallet restoration progress through SeedWordsRecoveryProgressViewController. Implements OverlayPresentable protocol for progress display. Features real-time validation of seed phrase completeness and automatic navigation to onboarding on successful restoration. Dependencies: Combine, RestoreWalletFromSeedsModel, RestoreWalletFromSeedsView, FormOverlayPresenter, SeedWordsRecoveryProgressViewController, AppRouter. Used by: Wallet setup flow for recovering existing wallets from seed phrases. |

###### MobileWallet/Screens/RestoreWalletFromSeeds/Views/

| File | Description |
|------|-------------|
| `TokenCollectionView.swift` | Advanced collection view for seed word input with intelligent autocompletion and validation. Features SeedWordModel with state management (valid/invalid/editing), TokenViewFlowLayout for custom layout, and dual cell types (TokenView/TokenInputView). Implements comprehensive gesture handling, toolbar integration for autocomplete, and dynamic content sizing. Uses UICollectionViewDiffableDataSource for smooth animations and supports text input delegation, first responder management, and intelligent token removal with backspace handling at first position. |
| `TokenInputView.swift` | Interactive text input cell for seed word entry with toolbar integration and advanced text handling. Features ObservableTextField with custom backspace detection, TokensToolbar for autocompletion, and comprehensive text field delegation. Supports autocomplete token selection, system text menu integration, and special backspace handling at cursor position. Uses proper first responder management and coordinates with parent collection view for seamless token input experience. Includes custom UITextField subclass for advanced cursor position detection and text manipulation. |
| `TokenView.swift` | Visual seed word token display with validation states and delete functionality. Features dynamic corner radius calculation, state-based styling (valid/invalid), and optional delete icon visibility. Uses theme-aware colors for border, text, and icon tinting based on validation state. Supports interactive delete functionality and provides clear visual feedback for seed word validation status with proper rounded pill appearance and consistent spacing. |
| `TokensToolbar.swift` | Keyboard accessory toolbar for seed word autocompletion with horizontal scrolling. Features UICollectionView with TokensToolbarFlowLayout for horizontal token display, animated label/collection view transitions, and tap-to-complete functionality. Uses UICollectionViewDiffableDataSource for smooth updates and supports dynamic content switching between autocomplete tokens and informational messages. Provides essential UX for seed phrase input with intelligent suggestion display and smooth animations. |

##### MobileWallet/Screens/RestoreWalletFromSeedsProgress/

| File | Description |
|------|-------------|
| `SeedWordsRecoveryProgressModel.swift` | Business logic for wallet restoration progress tracking with real-time status updates. Manages RestoreWalletStatus monitoring through Tari FFI integration, handles app lifecycle events for restoration continuity, and provides comprehensive progress reporting. Features status tracking (connecting, connected, scanning, progress, completed, failed), automatic recovery restart on app activation, percentage progress calculation for UTXO restoration, and localized error handling. Uses Combine framework for reactive UI updates and integrates with recovery process messaging for restored outputs. |
| `SeedWordsRecoveryProgressViewController.swift` | View controller for wallet restoration progress overlay with automatic timeout handling. Extends SecureViewController for sensitive operation protection, manages idle timer prevention during restoration, and provides success/failure callback handling. Features delayed description text update after 3 minutes, real-time status and progress binding, error popup presentation, and proper cleanup on view disappearance. Integrates with SeedWordsRecoveryProgressModel for business logic and PopUpPresenter for error display. |

###### MobileWallet/Screens/RestoreWalletFromSeedsProgress/Views/

| File | Description |
|------|-------------|
| `SeedWordsRecoveryProgressView.swift` | Progress overlay view for wallet restoration from seed words. Extends DynamicThemeView with comprehensive theme support. Components: Tari logo (55x55), title label "Restoring your wallet", description text explaining the process, dynamic status label for current operation, progress percentage label. Uses Poppins fonts with responsive sizing. Auto Layout with centered content and theme-aware colors. Dependencies: UIKit, Lottie. Used during wallet recovery process to show restoration progress. Theme colors update automatically for title, description, status and progress text. |

##### MobileWallet/Screens/Send/

| File | Description |
|------|-------------|
| `TransactionViewControllable.swift` | Protocol for transaction view controllers with completion handling. Single property: onCompletion((WalletTransactionsManager.TransactionError?) -> Void)? for success/failure callbacks. Extends UIViewController. Provides consistent interface for transaction progress screens. Dependencies: UIKit, WalletTransactionsManager. Implemented by SendingTariViewController and other transaction flow controllers. Enables standardized error handling and completion flow management across transaction screens. |

###### MobileWallet/Screens/Send/AddAmount/

| File | Description |
|------|-------------|
| `AddAmountViewController.swift` | Send transaction amount entry screen in the transaction flow. Manages amount input with custom AmountKeyboardView, real-time balance validation, network fee calculation with TransactionFeesManager, and currency conversion. Displays recipient address, wallet balance, fee options (slow/medium/fast), and network traffic indicators. Implements form validation, balance checking, pending transaction warnings, and fee modification popup. Extends DynamicThemeViewController for theme support. Dependencies: TariCommon, Combine, PaymentInfo model. Navigates to AddNoteViewController on continue. Key UI: AnimatedBalanceLabel, AddressView, ActionButton, fee selection controls. |
| `TransactionFeesManager.swift` | Transaction fee calculation manager for the send flow. Manages network traffic analysis (low/medium/high), fee estimation with three tiers (slow/medium/fast), and real-time fee calculation based on transaction amount. Uses TariFeesService and TariValidationService for fee per gram statistics and transaction validation. Implements reactive patterns with @Published properties for real-time UI updates. Provides timeout-based fee fetching, error handling, and automatic recalculation when amount changes. Key components: NetworkTraffic enum, FeeOptions struct, FeesData model. Dependencies: Combine, TariCommon, TariLib services. Used by AddAmountViewController for dynamic fee display. |

###### MobileWallet/Screens/Send/AddAmount/Views/

| File | Description |
|------|-------------|
| `AddAmountSpinnerView.swift` | Loading spinner view for transaction amount calculations with Lottie animation. Features looping pendingCircleAnimation with automatic play/pause behavior, centered layout with descriptive label, and theme-aware styling. Uses TariCommon framework and implements DynamicThemeView for consistent appearance. Provides visual feedback during fee calculations and amount validation processes with professional spinner animation and localized messaging. |
| `CurrencyLabelView.swift` | Custom currency display view combining Tari symbol and amount text. Properties: text (main content), textColor (applies to both icon and text), font/secondaryFont for dual-style formatting, separator for font switching point, iconHeight for custom sizing. Features dynamic font sizing with baseline alignment, currency icon scales with text height or fixed size, attributed string support for mixed fonts. Dependencies: UIKit, TariCommon. Used in amount entry screens and transaction displays. @View property wrapper for Auto Layout setup. Supports theme-aware tinting and responsive layout. |
| `NetworkTrafficView.swift` | Network traffic indicator with dynamic speedometer icons for transaction fee estimation. Features three traffic variants (low/medium/high) with corresponding speedometer icons, theme-aware styling, and compact horizontal layout. Uses custom icon system with proper content mode and tint color management. Provides users with visual feedback about network congestion affecting transaction fees and processing times. |
| `PopUpModifyFeeContentView.swift` | Fee adjustment popup content view for transaction sending. Components: TariSegmentedControl with three fee speed options (low/mid/high speedometer icons), estimated fee title label, CurrencyLabelView for fee amount display. Properties: estimatedFee (string) for fee value updates. Extends DynamicThemeView with automatic theme updates. Dependencies: UIKit, TariCommon. Used in add amount screen when users want to modify transaction fees. Centered layout with consistent 14px and 26px font sizing, theme-aware text colors. |
| `TariSegmentedControl.swift` | Custom segmented control with animated selection indicator and spring animations. Features dynamic icon-based segments, smooth selection view transitions, and comprehensive gesture handling. Uses Combine for reactive index selection, shadow effects for visual depth, and spring-damped animations for natural feel. Supports custom element sizing, theme-aware styling, and proper constraint management for selection view positioning. Provides enhanced UX for fee selection and other multi-option choices. |

###### MobileWallet/Screens/Send/AddNote/

| File | Description |
|------|-------------|
| `AddNoteViewController.swift` | Transaction note and GIF attachment screen in the send flow. Extends DynamicThemeViewController with Giphy SDK integration for adding animated GIFs to transaction messages. Features text note input with placeholder, GIF carousel, search functionality, and powered-by-Giphy branding. Manages PaymentInfo with note/attachment data, keyboard handling, and scroll view interactions. Implements attachment preview, cancel functionality, and send button state management. Key features: GIF search with rotating keywords, attachment container, slide-to-send button. Dependencies: GiphyUISDK, TariCommon, PaymentInfo model. Final step before transaction confirmation screen. |

###### MobileWallet/Screens/Send/AddRecipient/

| File | Description |
|------|-------------|
| `AddRecipientConstructor.swift` | Constructor for AddRecipient screen in send transaction flow. Factory pattern implementation providing buildScene() static method. Creates and configures AddRecipientModel and AddRecipientViewController with proper dependency injection. Minimal enum-based constructor following app's MVVM architecture pattern. Dependencies: none (imports handled by components). Used by navigation system to instantiate add recipient screen. Part of transaction sending workflow for selecting transaction recipients. |
| `AddRecipientModel.swift` | Business logic model for recipient selection in send transaction flow. Features: Yat ID lookup with emoji validation (1-5 characters), contact search and filtering, TariAddress generation and validation, recent transaction contacts, QR code parsing for addresses/deeplinks. Published properties: listSections, action, isYatFound, isAddressPreviewAvaiable, canMoveToNextStep, errorMessage. Combines ContactsManager, YatLib API, TariAddress validation. Supports TransactionsSendDeeplink and UserProfileDeeplink. Uses Combine for reactive updates. Error handling for invalid addresses, self-sending prevention, network-specific validation. Dependencies: Combine, YatLib, ContactsManager, Tari wallet FFI. |
| `AddRecipientView.swift` | UI view for recipient selection in transaction sending flow. Components: ContactSearchView with QR scanner button, ErrorView for validation messages, UITableView for contact list with sections. Features table view data source with diffable snapshots, contact filtering, row selection handling. Properties: viewModels for sections, isYatLogoVisible/isPreviewButtonVisible for Yat integration, errorMessage display. Callbacks: onQrCodeScannerButtonTap, onYatPreviewButtonTap, onRowTap. Extends BaseNavigationContentView with theme support. Dependencies: TariCommon, ContactBookCell, MenuTableHeaderView. Used in send transaction workflow for recipient selection. |
| `AddRecipientViewController.swift` | Add recipient step in transaction sending flow implementing MVVM with Combine bindings. Manages contact selection, address input, QR code scanning, and Yat integration for recipient selection. Features search functionality with real-time filtering, address preview with validation, QR scanner integration, and contact list display. Handles Yat lookup with preview toggling, wallet address validation with error messaging, and automatic progression to next transaction step. Integrates with search text binding, contact selection callbacks, and payment info generation. Dependencies: Combine, AddRecipientModel, AddRecipientView, AppRouter, PopUpPresenter. Used by: Transaction sending workflow for recipient selection and validation. |

###### MobileWallet/Screens/Send/AddRecipient/Views/

| File | Description |
|------|-------------|
| `ErrorView.swift` | Styled error message display component for recipient validation. Features rounded border container (4px radius), padding 14px, red border and text from theme system. Properties: message (get/set for label text). Extends DynamicThemeView with automatic theme updates for red coloring. Dependencies: TariCommon. Used in AddRecipientView to show validation errors for invalid addresses, emoji IDs, or self-sending attempts. Responsive layout with multi-line text support and bold font styling. |

###### MobileWallet/Screens/Send/Confirmation/

| File | Description |
|------|-------------|
| `ConfirmationView.swift` | Transaction confirmation screen UI showing send details before final submission. Components: title label, amount/address containers with shadows, Tari/user icons, DetailView for fee/recipient, total amount display, send/cancel buttons. Properties: amountText, totalAmountText, feeText, addressText, userText, isEmojiFormat toggle. Callbacks: onSendButonTap, onCancelButonTap, onCopyButonTap, onAddressFormatToggle. Styled containers (356x82) with rounded corners and theme-dependent shadows. Dependencies: TariCommon, DetailView, StylisedButton. Theme-aware layout with light/dark mode shadow handling. Recent addition (April 2025) to send transaction workflow. |
| `ConfirmationViewController.swift` | Transaction confirmation screen controller for the sending flow. Extends SecureViewController with ConfirmationView, manages navigation bar and AddressView display. Handles PaymentInfo display logic - shows contact alias if available, otherwise displays shortened emoji address. Manages address component display with full raw address showing, emoji format detection, and user text assignment. Final step before transaction broadcast in the send transaction workflow, providing user verification of recipient and amount details. |

###### MobileWallet/Screens/Send/SendingTari/

| File | Description |
|------|-------------|
| `SendingTariConstructor.swift` | Constructor for SendingTari transaction progress screen. Factory method buildScene() takes InputData (address, amount, fee, message, isOneSidedPayment) and returns configured SendingTariViewController. Creates SendingTariModel with transaction data and SendingTariView.InputModel with appropriate section count (2 for one-sided, 3 for standard transactions). Minimal enum-based constructor following app's MVVM pattern. Dependencies: none. Used by transaction flow to instantiate sending progress screen with proper configuration. |
| `SendingTariModel.swift` | Business logic model for transaction sending progress tracking. InputData: address, amount, feePerGram, message, isOneSidedPayment. State progression: connectionCheck → discovery → sent. Published properties: stateModel (firstText/secondText/stepIndex), onCompletion (success/failure). Features WalletTransactionsManager integration, state queue management, transaction ID tracking. Methods: start(), moveToNextStep(). Uses Combine for reactive state updates and transaction publisher. Localizable state messages for UI display. Dependencies: Combine, WalletTransactionsManager. Handles transaction lifecycle with error reporting and completion callbacks. |
| `SendingTariView.swift` | UI view for transaction sending progress with animated background and progress indicators. Components: VideoView background (sending-background.mp4), Lottie animation logo (sendingTariAnimation), two SendingTariLabel instances for status text, SendingTariProgressBar for step tracking. Methods: playInitialAnimation(), playSuccessAnimation(), playFailureAnimation(), hideAllComponents(). Text updates with animated transitions and completion callbacks. InputModel specifies numberOfSections for progress bar. Dependencies: UIKit, TariCommon, Lottie. Used in transaction sending flow to show real-time progress with video background and coordinated animations. |
| `SendingTariViewController.swift` | Transaction sending progress controller implementing TransactionViewControllable protocol. Manages SendingTariModel state changes, view animations, and transaction completion handling. Features: video background animation management, progress bar state updates, success/failure flow coordination, navigation gesture control. Binds model stateModel to view updates via Combine. Methods: endFlowWithSuccess() (3.7s delay), endFlow(withError). Handles app foreground notifications for background video restart. Dependencies: UIKit, Combine, SecureViewController. Coordinates complex transaction sending UI flow with multiple animated components and error handling. |

###### MobileWallet/Screens/Send/SendingTari/Animation/

| File | Description |
|------|-------------|
| `sendingTariAnimation.json` | [GENERATED] Lottie animation file for SendingTari transaction progress screen. Contains keyframe animation data for the Tari logo animation during transaction sending. Used by SendingTariView AnimationView component. Features initial animation (0.0-0.2 progress) for startup, success animation (0.2-1.0 progress) for completion, failure animation (0.2-0.0 progress) for errors. Generated by animation tools and integrated into app bundle. Part of transaction sending visual feedback system with coordinated timing for progress states. |

###### MobileWallet/Screens/Send/SendingTari/Views/

| File | Description |
|------|-------------|
| `ProgressBar.swift` | Animated progress bar component for transaction sending steps. States: disabled (inactive gray), off (purple 50% alpha), on (full purple). Features animated bar expansion from leading to trailing edge with 0.85s duration and 0.1s delay. Intrinsic content size 55x4 points. Auto Layout constraints switch between off/on positions for smooth animation. Theme-aware colors with brand purple from theme system. Dependencies: UIKit, TariCommon. Used in SendingTariProgressBar for multi-step transaction progress visualization. Completion callbacks for chaining animations. |
| `SendingTariLabel.swift` | Animated text label for transaction sending status updates. Features slide-in animation from bottom with fade effect. Update method: hideLabel() (1.0s fade out) → setText → showLabel() (0.5s slide up). Properties: font/textColor forwarded to internal UILabel. Always sets textColor to black during animations. Clipped bounds container with height-based constraints that animate between top/bottom positions. Dependencies: UIKit, TariCommon. Used in SendingTariView for animated status text transitions during transaction progress. Completion callback support for coordinated UI updates. |
| `SendingTariProgressBar.swift` | Multi-section progress bar container for transaction sending steps. Uses horizontal UIStackView with 6pt spacing and equal distribution. Dynamically creates ProgressBar instances based on update(sections:) call. Methods: update(state:forSection:completion:), state(forSection:). Tracks currentStep property. Helper progressBar(forSection:) safely accesses stack subviews. Dependencies: UIKit, TariCommon. Used in SendingTariView to show multi-step transaction progress (connection, discovery, sent). Completion callbacks forwarded to individual progress bars for coordinated animations. |

###### MobileWallet/Screens/Send/YatTransaction/

| File | Description |
|------|-------------|
| `YatTransactionConstructor.swift` | Constructor for YatTransaction immersive animation screen. Factory method buildScene() takes InputData (address, amount, feePerGram, message, yatID, isOneSidedPayment) and returns configured YatTransactionViewController. Creates YatTransactionModel with transaction data for Yat-powered transactions. Minimal enum-based constructor following app's MVVM pattern. Dependencies: none. Used when sending transactions to Yat recipients to display custom Yat visualizations during transaction processing. |
| `YatTransactionModel.swift` | Business logic model for Yat transaction processing with custom visualization. YatTransactionViewState enum: idle, initial, playVideo, completion, failed. InputData includes yatID for Yat integration. Features: Yat API visualization fetching from 'VisualizerFileLocations', video download/caching via YatCacheManager, parallel transaction sending, vertical video detection (scaleToFill). Published properties: viewState, error. 30s connection timeout. Methods coordinate transaction blockchain submission with Yat visualization display. Dependencies: Combine, YatLib, WalletTransactionsManager, YatCacheManager. Creates immersive transaction experience. |
| `YatTransactionViewController.swift` | Controller for immersive Yat transaction experience implementing TransactionViewControllable. Manages YatTransactionModel view state binding to YatTransactionView. Full-screen modal presentation (.overFullScreen). Features: model state binding via Combine, view refresh on app activation, completion callback forwarding. Dismisses automatically on completion with error passthrough. Dependencies: UIKit, Combine, SecureViewController. Coordinates complex Yat animation with blockchain transaction processing for enhanced user experience during Yat-powered transactions. |

###### MobileWallet/Screens/Send/YatTransaction/Managers/

| File | Description |
|------|-------------|
| `YatCacheManager.swift` | File caching manager for Yat visualization videos in YatVisualisations cache directory. FileIdentifier enum: verticalVideo (.vert), normalVideo (empty). FileData struct with URL and identifier. Methods: fetchFileData() checks hash validity and orientation compatibility, save() stores data with obsolete file cleanup. String.Components extension parses filenames (assetName-hash-identifier.extension). Cache directory creation, hash validation, orientation matching for vertical videos. Dependencies: Foundation, Logger. Used for Yat transaction visualization video caching with versioning and file management. |

###### MobileWallet/Screens/Send/YatTransaction/Views/

| File | Description |
|------|-------------|
| `VideoView.swift` | Video playback view using AVPlayerLayer with looping support. Properties: url (triggers startPlayer on set), videoGravity (AVLayerVideoGravity passthrough). Features AVPlayerLooper for seamless video loops, AVQueuePlayer for continuous playback. Video layer automatically sized to view bounds in layoutSubviews. Method startPlayer() creates new player/looper instances and starts playback. Dependencies: UIKit, AVFoundation. Used in Yat transaction animations and SendingTari background videos. Simple video container with automatic loop functionality. |
| `YatTransactionView.swift` | Immersive Yat transaction visualization view with animated scene transitions. Components: transaction label with styled text, VideoView for Yat animations, Lottie spinner, completion message label, gradient mask for video alpha. State-driven: idle, initial (with text/yatID), playVideo (with URL/scaleToFill), completion, failed. Features circular mask animations (hiddenScenePath ↔ visibleScenePath), attributed text styling, video aspect ratio handling, bottom alpha gradient mask. Constraint switching between center/bottom positioning. Dependencies: TariCommon, Lottie. Creates cinematic Yat transaction experience with coordinated animations and scene management. |

##### MobileWallet/Screens/Settings/

| File | Description |
|------|-------------|
| `SettingsParentTableViewController.swift` | Base table view controller for settings screens with consistent navigation and layout. Extends UITableViewController with standardized navigation bar setup, theme integration, and common settings screen patterns. Provides foundation for settings screens requiring table-based UI with consistent styling and behavior. Used as parent class for settings screens like DeleteWalletViewController. Dependencies: UIKit. Ensures consistent user experience across all settings table views. |
| `SettingsParentViewController.swift` | Base view controller for settings screens with consistent navigation and layout. Extends UIViewController with standardized navigation bar setup, theme integration, modal detection, and common settings patterns. Provides foundation for settings screens requiring custom view layouts beyond table views. Used as parent class for complex settings screens. Dependencies: UIKit. Ensures consistent user experience and navigation behavior across all settings screens. |
| `SettingsViewController.swift` | Main settings screen controller providing comprehensive wallet configuration interface. Organizes settings into Security (backup, data collection), More (about, support, legal), and Advanced Settings (theme, network, base node, wallet deletion) sections. Implements table view interface with section headers and navigation to specialized settings screens. Integrates LocalAuthentication for security-sensitive operations and provides access to backup settings, network configuration, Tor bridge setup, and theme selection. Essential configuration hub for wallet customization and advanced user preferences. Dependencies: SettingsParentTableViewController, LocalAuthentication, TariCommon for theming and localization. |

###### MobileWallet/Screens/Settings/AdvancedSettings/

| File | Description |
|------|-------------|
| `DeleteWalletViewController.swift` | Settings screen for wallet deletion with safety warnings. Extends SettingsParentTableViewController with single destructive menu item. Features: warning dialog with confirm/cancel options, PopUpPresenter for safety confirmation, CommonActions.deleteWalletAndMoveToSplashScreen() for deletion. DeleteWalletHeaderView provides warning text with theme support. SystemMenuTableViewCell with isDestructive styling. Table view setup with delegate/dataSource, custom header, zero margins. Dependencies: UIKit, TariCommon, PopUpPresenter. Critical wallet management function with multiple safety confirmations. |

###### MobileWallet/Screens/Settings/BackUpSettings/

| File | Description |
|------|-------------|
| `SecureBackupViewController.swift` | Secure wallet backup screen with password protection. Components: scroll view with stack layout, password entry/confirmation fields, PendingView for backup progress. Features: keyboard management with constraint animation, password validation via PasswordFieldDelegate, BackupManager integration with syncState monitoring. Methods: performBackup() triggers BackupManager.backupNow(), completion handling returns to BackupWalletSettingsViewController. Two-part description text with attributed styling, redacted password fields for security. Dependencies: UIKit, Combine, BackupManager, PasswordField, PendingView. Critical security feature for wallet data protection. |

###### MobileWallet/Screens/Settings/BackUpSettings/Seed Words List/

| File | Description |
|------|-------------|
| `SeedWordsListConstructor.swift` | Constructor for SeedWordsList screen in backup settings flow. Factory method buildScene() creates and configures SeedWordsListModel and SeedWordsListViewController with proper dependency injection. Follows app's MVVM architecture pattern with minimal enum-based constructor. Dependencies: none. Used by backup settings navigation to instantiate seed words display screen for wallet backup verification and review. Part of secure wallet backup workflow. |
| `SeedWordsListModel.swift` | Business logic model for seed words display and management in backup flow. Handles wallet seed phrase retrieval, word list formatting, reveal/hide functionality, and security state management. Published properties for word list, visibility state, and completion status. Methods for accessing secure seed storage, formatting for display, and managing user interaction states. Dependencies: wallet FFI, secure storage. Used in backup verification and seed phrase review processes with security considerations. |
| `SeedWordsListView.swift` | UI view for displaying wallet seed words in secure backup flow. Features expandable/collapsible word list, security warnings, copy protection, and structured layout. Components include word grid, reveal/hide controls, security notices, and navigation elements. Theme-aware with security-focused styling and accessibility support. Used for seed phrase display during backup creation and verification. Dependencies: custom seed word views, expand button. Provides secure seed phrase interface. |
| `SeedWordsListViewController.swift` | Controller for seed words display with security and interaction management. Coordinates model updates with view presentation, handles user interactions, manages security warnings, and controls navigation flow. Features screen recording protection, copy prevention, and secure display handling. Implements backup workflow progression and completion callbacks. Dependencies: SeedWordsListModel, SeedWordsListView. Provides secure seed phrase presentation with comprehensive protection measures. |

###### MobileWallet/Screens/Settings/BackUpSettings/Seed Words List/Views/

| File | Description |
|------|-------------|
| `ExpandButton.swift` | Custom button for expanding/collapsing seed words list with security considerations. Features toggle state visualization, animated transitions, and security-aware styling. Shows appropriate icons and text for reveal/hide states with warning indicators. Theme-aware design with accessibility support. Used in seed words interface to control sensitive content visibility. Dependencies: UIKit. Provides secure toggle interface for sensitive seed phrase content. |
| `SeedWordListElementView.swift` | Individual seed word display component with number and word text. Features secure text rendering, copy protection, selection prevention, and styled presentation. Shows word index and value with consistent formatting and spacing. Theme-aware with security-focused design preventing screenshots or text selection. Used in seed words grid layout for backup display. Dependencies: UIKit. Provides secure individual word presentation with anti-copying measures. |

###### MobileWallet/Screens/Settings/BackUpSettings/Settings/

| File | Description |
|------|-------------|
| `BackupWalletSettingsConstructor.swift` | Constructor for BackupWalletSettings screen in settings navigation. Factory method buildScene() creates and configures BackupWalletSettingsModel and BackupWalletSettingsViewController with proper dependency injection. Follows app's MVVM architecture pattern with minimal enum-based constructor. Dependencies: none. Used by settings navigation to instantiate backup settings management screen for wallet security configuration. Central hub for backup-related settings. |
| `BackupWalletSettingsModel.swift` | Business logic model for backup settings management and configuration. Handles backup status monitoring, cloud storage preferences, backup frequency settings, and security options. Published properties for backup state, last backup date, settings values, and error states. Methods for triggering backups, configuring storage, and managing backup lifecycle. Dependencies: BackupManager, cloud storage services. Central coordinator for wallet backup configuration and management. |
| `BackupWalletSettingsView.swift` | UI view for backup settings configuration and status display. Components include backup status indicators, cloud storage toggles, backup frequency controls, manual backup triggers, and last backup information. Features settings sections, progress indicators, and action buttons. Theme-aware with status-based styling and accessibility support. Used as main backup configuration interface. Dependencies: settings controls, status views. Provides comprehensive backup management interface. |
| `BackupWalletSettingsViewController.swift` | Controller for backup settings with configuration management and user interaction handling. Coordinates model state with view updates, handles settings changes, manages backup operations, and provides user feedback. Features navigation to related backup screens, progress monitoring, and error handling. Dependencies: BackupWalletSettingsModel, BackupWalletSettingsView. Orchestrates backup configuration workflow and provides centralized backup management interface. |

###### MobileWallet/Screens/Settings/BackUpSettings/Settings/Views/

| File | Description |
|------|-------------|
| `BackupWalletSettingsHeaderView.swift` | Header view component for the backup wallet settings screen. Displays a title and descriptive text explaining wallet backup functionality. Exports: BackupWalletSettingsHeaderView (UIView subclass). Dependencies: UIKit, TariCommon, Theme system, NetworkManager for currency symbol. Features dynamic theming support with automatic color updates, localized text content, and responsive layout with fixed margins and spacing. |

###### MobileWallet/Screens/Settings/BackUpSettings/VerifySeedWords/

| File | Description |
|------|-------------|
| `VerifySeedWordsConstructor.swift` | Constructor for VerifySeedWords screen in backup verification flow. Factory method buildScene() creates and configures VerifySeedWordsModel and VerifySeedWordsViewController with proper dependency injection. Follows app's MVVM architecture pattern with minimal enum-based constructor. Dependencies: none. Used by backup workflow to instantiate seed words verification screen for confirming user has correctly recorded backup phrases. Critical security step. |
| `VerifySeedWordsModel.swift` | Business logic model for seed phrase verification flow. Manages interactive token selection to verify wallet backup seed phrase. Exports: VerifySeedWordsModel class with InputData struct. Dependencies: Combine framework, SeedWordModel, TariSettings. Key features: randomized word ordering, drag-and-drop token verification, real-time validation feedback, success/error state management, persistent verification status storage. Implements MVVM pattern with @Published properties for reactive UI updates. |
| `VerifySeedWordsView.swift` | User interface for seed phrase verification screen. Interactive word token selection interface with drag-and-drop functionality. Exports: VerifySeedWordsView (BaseNavigationContentView). Dependencies: UIKit, TariCommon, Theme system, TokenCollectionView. Features: scrollable content layout, animated error/success indicators, dual token collection views (selected and available), responsive typography, dynamic theme support, content hiding and scaling animations. |
| `VerifySeedWordsViewController.swift` | View controller for seed phrase verification flow. Coordinates between model and view using Combine reactive bindings. Exports: VerifySeedWordsViewController (SecureViewController). Dependencies: UIKit, Combine, VerifySeedWordsModel, VerifySeedWordsView. Features: secure content protection, token selection callbacks, navigation flow management with modal/push detection, automatic flow completion on successful verification. Part of wallet backup security verification system. |

###### MobileWallet/Screens/Settings/Bug Reporting/

| File | Description |
|------|-------------|
| `BugReportingConstructor.swift` | Constructor for BugReporting screen in settings navigation. Factory method buildScene() creates and configures BugReportingModel and BugReportingViewController with proper dependency injection. Follows app's MVVM architecture pattern with minimal enum-based constructor. Dependencies: none. Used by settings navigation to instantiate bug reporting interface for user feedback and issue submission. Part of app support and quality assurance system. |
| `BugReportingModel.swift` | Business logic model for bug reporting functionality. Manages crash log consent and report submission. Exports: BugReportingModel class with Action enum. Dependencies: BugReportService, AppConfigurator, ErrorMessageManager. Key features: data collection consent validation, async bug report submission, error handling with user-friendly messages, integration with crash logging configuration. Uses Task-based async/await for network operations. |
| `BugReportingView.swift` | User interface for bug reporting screen. Form-based interface for user feedback submission. Exports: BugReportingView (BaseNavigationContentView). Dependencies: UIKit, TariCommon, KeyboardAvoidingContentView. Features: name/email text fields, expandable message text view, keyboard management, form validation, submission state indicators, logs viewing button, dynamic theming, localized content, responsive layout with proper spacing and constraints. |
| `BugReportingViewController.swift` | View controller for bug reporting functionality. Coordinates form submission and data collection consent flows. Exports: BugReportingViewController (SecureViewController). Dependencies: UIKit, Combine, BugReportingModel, PopUpPresenter, LogsListConstructor. Features: secure content protection, data collection consent popup, form data binding, error message handling, logs navigation, modal presentation management, reactive state updates using Combine framework. |

###### MobileWallet/Screens/Settings/Data Collection Settings/

| File | Description |
|------|-------------|
| `DataCollectionSettingsConstructor.swift` | Constructor for DataCollectionSettings screen in privacy settings. Factory method buildScene() creates and configures DataCollectionSettingsModel and DataCollectionSettingsViewController with proper dependency injection. Follows app's MVVM architecture pattern with minimal enum-based constructor. Dependencies: none. Used by settings navigation to instantiate data collection preferences screen for user privacy control and analytics opt-in/out management. |
| `DataCollectionSettingsModel.swift` | Business logic model for data collection consent settings. Manages crash logging and analytics opt-in preferences. Exports: DataCollectionSettingsModel class. Dependencies: Combine framework, AppConfigurator. Features: reactive state management with @Published property, automatic AppConfigurator synchronization, duplicate value filtering. Simple toggle model that binds UI state to global crash logger configuration. Part of privacy settings infrastructure. |
| `DataCollectionSettingsView.swift` | User interface for data collection consent settings screen. Single switch toggle for enabling/disabling crash reporting and analytics. Exports: DataCollectionSettingsView class. Dependencies: TariCommon, Combine, SwitchMenuCell. Features: descriptive header text, table view with switch cell, dynamic data source with diffable snapshots, reactive switch value binding, dynamic theming support. Simple privacy settings interface for user control over data collection. |
| `DataCollectionSettingsViewController.swift` | View controller for data collection consent settings screen. Coordinates model and view for crash reporting preferences. Exports: DataCollectionSettingsViewController (SecureViewController). Dependencies: UIKit, Combine, DataCollectionSettingsModel, DataCollectionSettingsView. Features: secure content protection, reactive switch state binding, model-view coordination, navigation integration. Part of privacy settings allowing users to control crash logging and analytics collection. |

###### MobileWallet/Screens/Settings/MoreSettings/

| File | Description |
|------|-------------|
| `AboutModel.swift` | Business logic model for About/More settings screen. Manages app information, version details, and additional settings navigation. Exports: AboutModel class. Dependencies: App configuration, version management utilities. Features: app version retrieval, build information management, settings item configuration, navigation state management. Provides data for about screen display and settings navigation. |
| `AboutView.swift` | User interface for About/More settings screen displaying app information and additional settings options. Exports: AboutView (BaseNavigationContentView). Dependencies: UIKit, TariCommon, AboutViewCell, AboutViewHeader. Features: table view layout, app version display, settings item list, section headers, dynamic theming support. Presents app information and navigation to additional settings screens. |
| `AboutViewController.swift` | View controller for About/More settings screen coordination. Manages app information display and settings navigation. Exports: AboutViewController (SecureViewController). Dependencies: UIKit, Combine, AboutModel, AboutView. Features: secure content protection, table view management, settings navigation handling, model-view coordination. Entry point for additional app settings and information. |

###### MobileWallet/Screens/Settings/MoreSettings/Views/

| File | Description |
|------|-------------|
| `AboutViewCell.swift` | Table view cell for About screen settings items. Displays individual setting options with consistent styling. Exports: AboutViewCell class with ViewModel struct. Dependencies: UIKit, Theme system. Features: title and description labels, optional disclosure indicator, consistent cell height, dynamic theming support. Used in About screen for listing various app settings and information items. |
| `AboutViewHeader.swift` | Header view component for About screen displaying app logo and version information. Exports: AboutViewHeader class. Dependencies: UIKit, Theme system, app version utilities. Features: Tari logo display, app version and build number, centered layout, responsive typography, dynamic theming support. Provides visual branding and version identification at top of About screen. |

###### MobileWallet/Screens/Settings/Theme Settings/

| File | Description |
|------|-------------|
| `ThemeSettingsConstructor.swift` | Constructor for ThemeSettings screen in appearance settings. Factory method buildScene() creates and configures ThemeSettingsModel and ThemeSettingsViewController with proper dependency injection. Follows app's MVVM architecture pattern with minimal enum-based constructor. Dependencies: none. Used by settings navigation to instantiate theme selection interface for light/dark/purple theme management and dynamic theme switching. |
| `ThemeSettingsModel.swift` | Business logic model for theme selection functionality. Manages light/dark/system theme preferences. Exports: ThemeSettingsModel class with Element enum and ElementModel struct. Dependencies: ThemeCoordinator. Features: three theme options (system, light, dark), selection state management, ThemeCoordinator integration for theme persistence, reactive UI updates with @Published properties. Core component of dynamic theming system allowing user customization. |
| `ThemeSettingsView.swift` | User interface for theme selection screen with collection view of theme options. Exports: ThemeSettingsView (BaseNavigationContentView). Dependencies: UIKit, TariCommon, ThemeSettingsCollectionCell. Features: collection view layout, theme preview cells, selection state management, responsive grid layout, dynamic theming support. Allows users to choose between system/light/dark theme preferences with visual previews. |
| `ThemeSettingsViewController.swift` | View controller for theme selection functionality. Coordinates theme selection between model and view using reactive bindings. Exports: ThemeSettingsViewController (SecureViewController). Dependencies: UIKit, Combine, ThemeSettingsModel, ThemeSettingsView. Features: secure content protection, collection view management, theme selection handling, real-time theme application, model-view coordination. |

###### MobileWallet/Screens/Settings/Theme Settings/Views/

| File | Description |
|------|-------------|
| `RadioButtonView.swift` | Custom radio button component for theme selection with selection state indicator. Exports: RadioButtonView class. Dependencies: UIKit, dynamic theming system. Features: circular selection indicator, animated state changes, configurable colors, selection state management, accessibility support. Used in theme settings for indicating currently selected theme option. |
| `ThemeSettingsCollectionCell.swift` | Collection view cell for theme selection displaying theme preview with radio button selection. Exports: ThemeSettingsCollectionCell class with ViewModel struct. Dependencies: UIKit, RadioButtonView, Theme system. Features: theme preview display, embedded radio button, selection state management, theme-specific styling, responsive layout. Provides visual theme selection interface in collection view. |

###### MobileWallet/Screens/Settings/Views/

| File | Description |
|------|-------------|
| `BaseMenuTableView.swift` | Base table view component for settings screens with consistent styling and behavior. Exports: BaseMenuTableView class. Dependencies: UIKit, Theme system. Features: standardized appearance, section styling, separator configuration, dynamic theming support, consistent spacing and margins. Foundation component for menu-style settings interfaces throughout the app. |
| `SettingsProfileCell.swift` | Table view cell for settings profile section displaying user avatar and wallet information. Exports: SettingsProfileCell class with ViewModel struct. Dependencies: UIKit, Theme system, avatar components. Features: circular avatar display, wallet name and details, profile editing access, dynamic theming support, responsive layout. Top section of settings screen showing user profile summary. |
| `SettingsViewFooter.swift` | Footer view component for settings screen displaying app version and additional information. Exports: SettingsViewFooter class. Dependencies: UIKit, Theme system, app version utilities. Features: app version display, centered text layout, dynamic theming support, subtle typography. Provides version information at bottom of settings screen for reference and debugging. |

##### MobileWallet/SwiftUI/Buttons/

| File | Description |
|------|-------------|
| `CopyButton.swift` | SwiftUI copy button component with visual feedback for copying text to clipboard. Features animated state changes between copy and checkmark icons, implements Copying protocol for reusable feedback behavior. Uses IconButton with template styling and UIPasteboard integration. Includes 1-second feedback animation showing success state. Part of SwiftUI design system for transaction details and text copying functionality. |
| `EmojiToggle.swift` | SwiftUI toggle button for switching between emoji and text address display modes. Features animated state transitions and template-styled icons. Uses binding for reactive state management and withAnimation for smooth transitions. Part of SwiftUI design system for address format selection in transaction interfaces. |
| `IconButton.swift` | SwiftUI icon button component for displaying UIImage icons with tap actions. Features template styling for theme-aware icon coloring and customizable action callbacks. Simple wrapper around SwiftUI Button with consistent icon presentation. Part of SwiftUI design system for toolbar and interface actions. |
| `TariButton.swift` | Comprehensive SwiftUI button component with multiple styles and sizes for the Tari design system. Supports primary, secondary, outlined, and text styles with large, medium, and small sizes. Features optional leading icons, disabled states, and theme-aware styling. Includes capsule background shapes, stroke borders, and responsive typography. Core UI component for consistent button appearance across SwiftUI interfaces. |

##### MobileWallet/SwiftUI/Extensions/

| File | Description |
|------|-------------|
| `Image+Style.swift` | SwiftUI Image extension for template-style rendering with custom colors. Provides templateStyle modifier for theme-aware icon coloring throughout the app. Simple utility for consistent image styling in SwiftUI components. |
| `Toolbar+Items.swift` | SwiftUI toolbar extension providing standardized back button item for navigation bars. Features consistent back arrow styling with theme-aware colors and customizable action callbacks. Part of SwiftUI design system for navigation consistency. |
| `View+Backgrount.swift` | SwiftUI View extension for applying scene-wide background colors that extend beyond safe areas. Provides sceneBackground modifier for consistent full-screen background styling. Part of SwiftUI design system utilities. |

##### MobileWallet/SwiftUI/ListItems/

| File | Description |
|------|-------------|
| `SettingsItem.swift` | SwiftUI list item component for settings menu entries with icons and titles. Features consistent spacing, dividers, and theme-aware styling. Uses HStack layout with trailing spacer and integrated divider. Part of SwiftUI design system for settings interfaces. |
| `TransactionDetailItem.swift` | SwiftUI component for displaying transaction detail rows with labels, values, and optional actions. Features customizable value colors, tap actions, and trailing action buttons. Includes divider styling and consistent spacing. Supports both simple text display and interactive elements. Part of SwiftUI design system for transaction detail screens. |

##### MobileWallet/SwiftUI/Typography/

| File | Description |
|------|-------------|
| `Font+Poppins.swift` | SwiftUI Font extension providing Poppins font family integration with weight variants. Defines PoppinsWeight enum for regular, medium, semiBold, and bold styles. Core typography foundation for SwiftUI design system ensuring consistent font usage across the app. |
| `Text+Typography.swift` | Comprehensive SwiftUI typography system with predefined text styles and modifiers. Defines heading, body, button, and modal text styles using Poppins fonts. Provides both Font extensions and View/Text modifiers for consistent typography throughout SwiftUI interfaces. Core design system component for text styling and hierarchy. |

#### MobileWallet/UIElements/

| File | Description |
|------|-------------|
| `AmountKeyboardView.swift` | Custom numerical keyboard view for entering cryptocurrency amounts in the wallet app. Features dynamic theme support, configurable key layout (0-9, decimal separator, delete), and callback-based key press handling. Uses stack view layout with equal distribution, supports customizable spacing, and integrates with BaseButton components. Includes pre-configured keyboard layout for Tari amount entry with proper decimal separator handling. Theme-aware with automatic color updates for buttons and text. |
| `AnimatedRefreshingView.swift` | Animated status indicator view for wallet sync and transaction states. Displays loading states with emoji, spinner, and text labels for: wallet syncing (.loading), receiving transactions (.receiving), completing broadcasts (.completing), updating cancelled transactions (.updating), success state, and transaction-specific states (waiting for sender/recipient, confirmation progress). Features smooth slide-up animations between states, themed colors (purple for loading, green for success, yellow for transaction states), automatic hide/show with fade transitions. Used in wallet dashboard and transaction views. Dependencies: UIKit, Theme system. |
| `BaseNavigationContentView.swift` | Base view component with integrated navigation bar for consistent screen layouts. Extends DynamicThemeView with pre-configured NavigationBar component, automatic constraint setup, and theme-aware background styling. Provides foundation for screens requiring navigation bar integration with consistent spacing and theming. Used as parent class for content views throughout the app to maintain visual consistency and reduce code duplication. |
| `CircularProgressView.swift` | Animated circular progress indicator with theme-aware styling and smooth animations. Features dual CAShapeLayer design with background circle and animated progress stroke, customizable line widths, and rounded line caps. Implements percentage-based progress updates with CABasicAnimation for smooth transitions. Uses theme system for color management and provides visual feedback for loading states, download progress, and operation completion across the wallet interface. |
| `EmojiTextField.swift` | Specialized text field for emoji ID input with validation and formatting. Features emoji-only input filtering, character limits, real-time validation, and Tari address formatting. Custom keyboard handling for emoji selection, paste validation, and input sanitization. Theme-aware styling with error states and visual feedback. Used for Yat emoji ID entry and Tari address input throughout the app. Dependencies: UIKit. Provides specialized input handling for cryptocurrency address formats and emoji-based identifiers. |
| `GradientView.swift` | Flexible gradient view component with multiple orientation support and location-based color mapping. Features GradientLocationData struct for precise color positioning, three orientation modes (horizontal, vertical, diagonal), and CAGradientLayer-based rendering. Supports dynamic gradient updates, automatic bounds management, and complex color transitions. Used for background effects, visual enhancements, and brand-consistent gradient applications throughout the wallet interface. |
| `NavigationBar.swift` | Custom navigation bar component providing consistent navigation UI across the wallet application. Supports multiple back button types (back arrow, close, custom text, none), configurable right-side action buttons with images or text, and dynamic theming. Implements ButtonModel structure for flexible button configuration with callbacks. Provides title customization and content view access for additional UI elements. Essential UI component ensuring consistent navigation patterns and branding throughout the app. Extends DynamicThemeView for automatic theme adaptation and uses Poppins font for typography consistency. |
| `QRCodeView.swift` | Custom UIView component for displaying QR codes with loading states using TariCommon framework. Wraps LoadingImageView with white background and rounded corners (7.0 radius). Provides state management for loading, success, and error states. Includes 17-point padding around the inner image view. Used throughout the app for receiving payments, contact sharing, and wallet address display. |
| `RoundedAvatarView.swift` | Circular avatar view component for user profile display. Exports: RoundedAvatarView class. Dependencies: UIKit, Theme system. Features: circular image masking, placeholder support, automatic sizing, border styling, image loading with error handling, dynamic theming support. Used for user profile images in settings and contact displays throughout the app. |
| `TariGradientView.swift` | Tari-branded gradient view component with theme integration. Creates diagonal gradients using theme-defined primary button colors. Exports: TariGradientView class. Dependencies: UIKit, DynamicThemeView, AppTheme. Features: CAGradientLayer with top-left to bottom-right direction, automatic theme color integration (primaryStart to primaryEnd), responsive layer bounds management, neutral primary background fallback. Part of Tari's visual identity system for consistent gradient styling. |
| `TariWindow.swift` | Custom UIWindow subclass providing dark/light theme change detection and coordination. Implements traitCollectionDidChange callback to notify theme system of user interface style changes for dynamic theme adaptation. Simple but essential component enabling responsive theme switching based on system appearance settings. Used by SceneDelegate for window initialization and ThemeCoordinator for theme management. Provides onUpdateUIStyle callback for real-time theme coordination throughout the application interface. |
| `UnselectableTextView.swift` | Text view component with disabled text selection for display-only content. Exports: UnselectableTextView class. Dependencies: UIKit. Features: disabled text selection, scroll support, copy prevention, read-only display, consistent styling. Used for displaying sensitive information like seed phrases where text selection must be prevented for security reasons. |

##### MobileWallet/UIElements/Buttons/

| File | Description |
|------|-------------|
| `ActionButton.swift` | Primary action button component with multiple styles and animated loading states. Supports normal, destructive, and loading styles with Lottie animation integration for pending states. Extends DynamicThemeBaseButton for automatic theme adaptation and provides customizable animation behavior. Implements sophisticated button styling with background colors, text formatting, and loading spinner integration. Essential UI component used throughout the app for primary actions like sending transactions, confirming operations, and navigation. Provides consistent button behavior and visual feedback across all major user interactions. Dependencies: TariCommon for theming, Lottie for animations. |
| `BaseButton.swift` | Foundation button class providing consistent behavior and styling across all app buttons. Extends UIButton with closure-based tap handling through onTap property, automatic tint color management for enabled/disabled states, and standardized callback mechanism. Serves as base class for specialized buttons (RoundedButton, ActionButton, etc.). Dependencies: UIKit. Used by: all custom button implementations, form controls, navigation elements. Key component of reusable UI element architecture ensuring consistent interaction patterns. |
| `LeftImageButton.swift` | Custom button component with image positioned on the left side of text label. Configurable image and title with automatic layout and spacing. Theme-aware styling with consistent typography and colors. Used throughout the app for buttons requiring icon + text combinations. Dependencies: UIKit. Provides standardized button appearance with left-aligned imagery for navigation, actions, and interactive elements requiring visual indicators. |
| `LoadingGIFButton.swift` | Button component with integrated GIF animation for loading states. Shows animated GIF during processing with disabled interaction. Switches between normal button state and loading animation. Theme-aware styling with configurable GIF assets. Used for operations requiring visual loading feedback like transaction submission, network requests, or processing operations. Dependencies: UIKit, possibly GIF animation framework. Provides enhanced user feedback during async operations. |
| `PulseButton.swift` | Button component with pulsing animation effect for emphasis. Features continuous or triggered pulse animations to draw user attention. Configurable pulse intensity, duration, and colors. Theme-aware styling with animated visual feedback. Used for primary actions, call-to-action buttons, or time-sensitive operations requiring user attention. Dependencies: UIKit, Core Animation. Provides engaging visual feedback to guide user interaction. |
| `RoundedButton.swift` | Standard button component with rounded corner styling. Configurable corner radius, background colors, text styling, and border options. Theme-aware with consistent appearance across the app. Used for primary and secondary actions throughout the interface. Dependencies: UIKit. Provides standardized button appearance following app design system with accessibility support and touch feedback. |
| `RoundedLabeledButton.swift` | Button component combining rounded styling with additional label text. Features main button action with supplementary descriptive label. Configurable styling for both button and label elements. Theme-aware with coordinated colors and typography. Used for complex actions requiring additional context or explanation. Dependencies: UIKit. Provides enhanced button functionality with integrated labeling for better user understanding. |
| `SecondaryActionButton.swift` | Button component styled for secondary actions with subtle appearance. Features muted colors, thinner borders, or alternate styling to distinguish from primary actions. Theme-aware with consistent secondary action styling. Used for cancel, back, or alternative action buttons throughout the interface. Dependencies: UIKit. Provides visual hierarchy for button importance and maintains design consistency for secondary interactions. |
| `SlideView.swift` | Interactive slide-to-action component for critical operations. Features horizontal sliding gesture to confirm actions, preventing accidental triggers. Configurable slide distance, text, colors, and completion feedback. Used for destructive or irreversible actions like transaction confirmation, wallet deletion, or sensitive operations. Dependencies: UIKit, gesture recognizers. Provides additional safety layer for critical user actions through deliberate interaction. |
| `TextButton.swift` | Lightweight text-based button component with multiple style variants (primary, secondary, warning). Supports customizable fonts, images with configurable spacing, and dynamic theme adaptation. Provides flexible button configuration for secondary actions, links, and auxiliary controls throughout the wallet interface. Implements consistent styling patterns while maintaining lightweight footprint for frequent use. Essential for navigation aids, secondary actions, and inline controls. Extends DynamicThemeBaseButton for automatic theme support and uses Poppins font family for typography consistency. |

##### MobileWallet/UIElements/Checkbox/

| File | Description |
|------|-------------|
| `Checkbox.swift` | Custom checkbox component with animated state transitions. Features smooth check/uncheck animations using Lottie (CheckboxSelectAnimation.json, CheckboxDeselectAnimation.json). Configurable styling, colors, and sizes with theme integration. State management with delegate pattern for value changes. Used in forms, settings, and selection interfaces throughout the app. Dependencies: UIKit, Lottie. Provides enhanced checkbox experience with fluid animations and consistent styling. |
| `CheckboxDeselectAnimation.json` | [GENERATED] Lottie animation file for checkbox deselection transition. Contains keyframe animation data for unchecking checkbox UI elements. Used by Checkbox UIElement component for smooth state transitions. Provides visual feedback when checkboxes change from selected to unselected state. Generated by animation tools and integrated into app bundle. Part of form interaction system with coordinated checkbox animations for better user experience. |
| `CheckboxSelectAnimation.json` | [GENERATED] Lottie animation file for checkbox selection transition. Contains keyframe animation data for checking checkbox UI elements. Used by Checkbox UIElement component for smooth state transitions. Provides visual feedback when checkboxes change from unselected to selected state. Generated by animation tools and integrated into app bundle. Part of form interaction system with coordinated checkbox animations for better user experience. |

##### MobileWallet/UIElements/Keyboard Form/

| File | Description |
|------|-------------|
| `FormOverlay.swift` | Overlay component for keyboard-based form interactions. Provides additional UI elements, toolbar, or contextual controls when keyboard is displayed. Features keyboard appearance/dismissal handling, dynamic content updates, and form navigation controls. Theme-aware styling with responsive layout adjustments. Used in forms requiring enhanced keyboard interaction or additional input controls. Dependencies: UIKit. Enhances form user experience with contextual keyboard enhancements. |
| `FormOverlayPresenter.swift` | Presenter/coordinator for FormOverlay keyboard enhancements. Manages overlay display logic, keyboard state monitoring, and coordination between form controls and overlay content. Handles presentation timing, animation coordination, and cleanup. Used to orchestrate complex form keyboard interactions and maintain overlay state consistency. Dependencies: UIKit, FormOverlay. Provides centralized management for form overlay lifecycle and interaction coordination. |
| `FormOverlayView.swift` | View component for keyboard form overlay UI. Contains the actual UI elements displayed above keyboard including buttons, controls, or additional input options. Features responsive layout, theme integration, and interaction handling. Coordinates with FormOverlayPresenter for state management. Used to provide contextual form controls and enhanced input options during keyboard interaction. Dependencies: UIKit. Implements the visual overlay interface for enhanced form interactions. |

##### MobileWallet/UIElements/Labels/

| File | Description |
|------|-------------|
| `AnimatedBalanceLabel.swift` | Advanced animated label component for displaying changing balance values with smooth character-by-character animations. Exports: AnimatedBalanceLabel class with Alignment and Animation enums. Dependencies: UIKit, CAAnimation. Features: push transition animations, configurable animation speeds (slow/fast), text alignment options (left/right/center), automatic font size adjustment, character-level animation delays, slide animations for layout changes, supports both value updates and typing animations. Complex implementation with label recycling and constraint management for performance. |
| `StylizedLabel.swift` | Custom label component for rendering text with mixed styles (normal, bold, highlighted) in a single label. Exports: StylizedLabel class with Style enum and StylizedText struct. Dependencies: UIKit. Features: attributed string composition, separate font support for normal/bold text, configurable separators between text components, highlighted text color support, dynamic style updates. Useful for displaying formatted text with different emphases in transaction displays and informational content. |
| `UILabelWithPadding.swift` | UILabel subclass with configurable internal padding. Supports custom edge insets around text content. Exports: UILabelWithPadding class. Dependencies: UIKit, Objective-C runtime for associated objects. Features: configurable UIEdgeInsets padding, automatic intrinsic content size calculation with padding, custom draw(rect:) implementation for inset text rendering, associated object storage for padding property. Useful for labels requiring internal spacing without additional container views. |

##### MobileWallet/UIElements/Menu/

| File | Description |
|------|-------------|
| `AccessoryImageMenuCell.swift` | Menu cell variant with custom accessory image. Extends MenuCell with image display capability. Exports: AccessoryImageMenuCell class with ViewModel struct. Dependencies: TariCommon, MenuCell base class. Features: 22x22pt accessory image view, configurable image content, inherits all MenuCell functionality, scale aspect fit image mode, replaces standard arrow accessory. Used for menu items requiring custom icons or status indicators. |
| `MenuCell.swift` | Reusable table view cell for menu-style lists with title and optional arrow accessory. Exports: MenuCell class with ViewModel struct. Dependencies: TariCommon, Theme system. Features: configurable arrow visibility, destructive action styling with red tint, dynamic theme support, accessory view management with stack view, standard 63pt height, proper spacing and margins. Includes helper methods for replacing and removing accessory items. Used throughout settings and navigation screens. |
| `MenuTableHeaderView.swift` | Table view header component for menu sections with title and optional description. Exports: MenuTableHeaderView class. Dependencies: UIKit, Theme system. Features: section title display, optional description text, consistent typography, proper spacing and margins, dynamic theming support. Used as section headers in menu-style table views throughout settings and navigation screens. |
| `MenuTableView.swift` | Specialized table view component for menu-style interfaces with consistent styling and behavior. Exports: MenuTableView class. Dependencies: UIKit, MenuCell, MenuTableHeaderView, Theme system. Features: standardized cell registration, header view support, consistent styling, separator configuration, dynamic theming. Foundation component for menu-based navigation and settings screens. |
| `SwitchMenuCell.swift` | Menu cell variant with UISwitch toggle functionality. Extends MenuCell with reactive switch state management. Exports: SwitchMenuCell class with DynamicModel helper class. Dependencies: TariCommon, Combine, MenuCell base class. Features: reactive state binding with @Published property, automatic switch value synchronization, Combine pipeline for duplicate filtering, target-action integration. DynamicModel enables external control of switch state through reactive programming. Used in settings screens for toggle preferences. |

##### MobileWallet/UIElements/Navigation/

| File | Description |
|------|-------------|
| `ContentNavigationViewController.swift` | Base view controller for screens with navigation bar and content area. Exports: ContentNavigationViewController class. Dependencies: UIKit, TariCommon, BaseNavigationContentView. Features: embedded navigation bar, content view container, layout change callbacks, automatic constraint management. Provides consistent navigation structure across screens while allowing custom content. Used as parent class for screens requiring standard navigation bar layout. Part of navigation system architecture. |

##### MobileWallet/UIElements/New/

| File | Description |
|------|-------------|
| `PrimaryButton 2.swift` | Modern primary button component with multiple styles and sizes. Exports: PrimaryButton class with Style and Size enums. Dependencies: TariCommon, Lottie. Features: three styles (primary, secondary, destructive), three sizes (large, medium, small), animated press feedback, disabled state support, configurable content insets, accessibility support, async animation handling with Task/await. Uses UIButton.Configuration for modern iOS styling. Replaces older button implementations with consistent design system integration. |
| `SeparatorView.swift` | Simple separator view component for UI divisions. Exports: SeparatorView class. Dependencies: TariCommon, Lottie. Features: pixel-perfect 1px height using screen scale, dynamic theme support, automatic height constraint, token-based color system. Uses .Token.divider color for consistent theming. Lightweight component for creating visual separation between UI elements in lists and forms. Part of the new design system components. |
| `StylisedButton.swift` | Comprehensive button component with multiple styles and sizes, animations, and theme integration. Features 8 style variants (primary, secondary, outlined, text, destructive, mining, etc.) and 5 size options (large, medium, small, subSmall, xsmall). Implements tap animations with scale/alpha transitions, disabled state handling, and Typography integration. Uses UIButton.Configuration for modern styling approach with custom typography, corner radius, and color management. Supports content insets, border styling, and accessibility features with comprehensive theming system. |
| `StylisedLabel.swift` | Modern styled label component with enhanced typography and theming support. Exports: StylisedLabel class. Dependencies: UIKit, Theme system. Features: pre-configured font styles, dynamic theming integration, responsive typography, accessibility support, consistent styling across app. Part of new design system components for unified visual language. |
| `Typography.swift` | Centralized typography system defining consistent font styles across the app. Features comprehensive Poppins font family usage with specific weights (SemiBold, Medium, Regular, Bold) and sizes for different UI elements. Includes heading hierarchy (2XL to SM), body text variants, modal titles, buttons, menu items, and special app title using DrukWideTrial font. Provides type-safe font access and maintains design system consistency throughout the application. |

##### MobileWallet/UIElements/Pager/

| File | Description |
|------|-------------|
| `PageToolbarView.swift` | Horizontal tab toolbar component for pager navigation with animated selection indicator. Features: tab button generation from string array, animated selector line positioning, equal distribution layout, tap handling with callback. Key components: UIStackView for tab layout, animated selector line view, BaseButton for tab items. Supports dynamic tab updates and smooth animated transitions between tabs via indexPosition property. Used as navigation component in TariPagerViewController for multi-page views. Provides visual feedback for current page selection with purple brand-colored indicator line. Dependencies: TariCommon, UIKit. |
| `TariPagerView.swift` | Container view for pager interface with integrated toolbar and content area. Provides structured layout for PageToolbarView and content container managed by TariPagerViewController. Simple but essential UI component that establishes the visual framework for tabbed/paged interfaces throughout the app. Used as the main view in TariPagerViewController to contain both navigation toolbar and page content. Supports theme-aware styling with proper constraint-based layout. Dependencies: TariCommon, UIKit. |
| `TariPagerViewController.swift` | Custom tabbed pager view controller for multi-page interfaces. Manages page navigation using PageViewController with smooth transitions and reactive page index tracking via Combine publishers. Contains Page struct defining title and controller pairs. Exports pageIndex publisher for external subscription. Used throughout the app for tabbed interfaces like transaction history, settings sections, and multi-step flows. Integrates with TariPagerView for visual presentation and user interaction handling. |

##### MobileWallet/UIElements/PasswordField/

| File | Description |
|------|-------------|
| `PasswordField.swift` | Secure password input field with visibility toggle and validation. Exports: PasswordField class. Dependencies: UIKit, security utilities, Theme system. Features: password visibility toggle, secure text entry, input validation, character masking, security indicators, dynamic theming support. Used for sensitive input like PIN codes and password entry throughout the app. |

##### MobileWallet/UIElements/PendingView/

| File | Description |
|------|-------------|
| `PendingView.swift` | Full-screen overlay for long-running operations like wallet restoration. Displays Tari logo, title, and descriptive text during processing. Exports: PendingView class. Dependencies: UIKit, Lottie, DynamicThemeView. Features: fade-in/out animations, idle timer prevention, automatic window overlay, progressive description updates (switches to longDefinition after 10 seconds), centered stack layout with responsive constraints, theme support. Used for wallet backup/restore operations requiring user patience. |

##### MobileWallet/UIElements/RadialGradientView/

| File | Description |
|------|-------------|
| `RadialGradientView.swift` | Circular radial gradient view component. Creates radial gradients from center to edge with automatic circular masking. Exports: RadialGradientView class. Dependencies: UIKit, Core Animation. Features: CAGradientLayer.radial type, configurable inside/outside colors, automatic corner radius for circular appearance, center-to-edge gradient direction. Simple utility for creating circular gradient backgrounds and visual effects throughout the app. |

##### MobileWallet/UIElements/SystemMenuTableViewCell/

| File | Description |
|------|-------------|
| `SystemMenuTableViewCell.swift` | Standard system-style table view cell for menu interfaces with iOS native appearance. Exports: SystemMenuTableViewCell class. Dependencies: UIKit. Features: native iOS styling, disclosure indicators, standard layouts, accessibility support, system color integration. Used for settings and menu items requiring native iOS appearance and behavior. |

##### MobileWallet/UIElements/TabBar/

| File | Description |
|------|-------------|
| `CustomTabBar.swift` | Custom tab bar implementation with rounded shadow design and security features. Exports: CustomTabBar class and RoundedShadowView helper class. Dependencies: TariCommon, SecureWrapperView. Features: rounded corners with shadow, secure content wrapping, custom sizing (59pt + safe area), dynamic theming, appearance customization for icons and background. RoundedShadowView creates drop shadow effects. Overrides addSubview to route content through secure wrapper. Core navigation component with enhanced visual design. |
| `MenuTabBarController.swift` | Main application tab bar controller managing three-tab navigation: Home, Profile (Contact Book), and Settings. Features custom tab bar with animated transitions and state management for wallet sync/restore presentation. Handles wallet state changes to trigger welcome overlays for newly created or restored wallets. Implements TabBarTransition class for smooth slide animations between tabs. Manages tab bar appearance including icon insets for notch/non-notch devices. Coordinates with HomeConstructor, ProfileViewController, and SettingsViewController. Central navigation hub after user authentication. |

##### MobileWallet/UIElements/Transaction/

| File | Description |
|------|-------------|
| `TransactionProgressPresenter.swift` | Coordinator for transaction progress display and status updates. Manages transaction state visualization and user feedback. Exports: TransactionProgressPresenter class. Dependencies: Transaction models, UI components, animation utilities. Features: progress tracking, status animations, error handling, completion callbacks, real-time updates. Presents transaction progress across different screens and states. |

##### MobileWallet/UIElements/TransitionUILabel/

| File | Description |
|------|-------------|
| `TransitionLabel.swift` | Animated label component with smooth text transitions and state changes. Exports: TransitionLabel class. Dependencies: UIKit, Core Animation. Features: fade transitions, slide animations, configurable animation duration, text state management, smooth visual updates. Used for dynamic text displays where content changes need smooth visual transitions like balance updates and status changes. |

##### MobileWallet/UIElements/WebBrowserView/

| File | Description |
|------|-------------|
| `WebBrowserViewController.swift` | In-app web browser view controller for displaying web content within the app. Exports: WebBrowserViewController class. Dependencies: UIKit, WebKit, WKWebView. Features: web page loading, navigation controls, URL validation, security policies, loading indicators, error handling. Used for displaying external web content like terms of service, privacy policies, and help documentation without leaving the app. |

#### MobileWallet/en.lproj/

| File | Description |
|------|-------------|
| `Localizable.strings` | English localization file containing all user-facing text strings for the iOS wallet app. Key-value pairs for UI labels, error messages, screen titles, button text, dialog content, onboarding text, transaction messages, and settings descriptions. Used by localized() function throughout the app for internationalization support. Enables text updates without code changes and future localization to other languages. Critical for user interface text management and localization infrastructure. |

### UnitTests/

| File | Description |
|------|-------------|
| `AmountNumberFormatterTests.swift` | Unit tests for AmountNumberFormatter class covering decimal number input validation and formatting. Tests: character appending, string appending, decimal separator handling, precision limits (6 decimal places), invalid character rejection, multiple decimal separator prevention, character removal operations. Dependencies: XCTest framework, Tari_Aurora module. Validates edge cases like leading decimal points, character limits, invalid strings, and complete value removal. Essential for ensuring robust amount input validation in transaction flows. |
| `DeepLinkFormatterTests.swift` | Comprehensive unit tests for DeepLinkFormatter functionality covering URL parsing, validation, and generation. Tests multiple deep link types: TransactionsSendDeeplink, BaseNodesAddDeeplink, ContactListDeeplink. Dependencies: XCTest, Tari_Aurora, NetworkManager. Key test scenarios: network name validation, command validation, parameter parsing/encoding, error handling for missing/invalid data, URL encoding/decoding, array parameter support for contact lists. Ensures robust deep link system security and reliability. |
| `ErrorMessageManagerTests.swift` | Unit test suite for ErrorMessageManager class that handles user-facing error messages. Tests error message generation for wallet errors, generic errors, and edge cases. Validates localized error message formatting, error code display, and fallback behavior for unknown errors. Critical for ensuring users receive meaningful error feedback throughout the wallet application. Tests custom messages for specific wallet errors vs generic error handling. |
| `MobileWalletTests.swift` | General unit tests for mobile wallet date utilities. Tests relative date formatting functionality with edge cases. Dependencies: XCTest, Tari_Aurora, Pods_MobileWallet. Key test: testRelativeDayValue() validates Date.relativeDayFromToday() extension for displaying transaction timestamps as "2m ago", "2h ago", "Yesterday", or explicit dates. Tests time boundary conditions and calendar integration. Important for transaction history display accuracy. |
| `NetworkManagerTests.swift` | Unit tests for NetworkManager class covering network configuration and base node management. Dependencies: XCTest, Tari_Aurora, GroupUserDefaults. Tests: network initialization, persistent storage, base node selection, custom base node management, settings persistence across network changes. Currently limited to single network support (Esmeralda) with commented multi-network tests. Validates UserDefaults integration and network settings consistency. Critical for network connectivity reliability. |
| `StringBaseNodeTests.swift` | Unit tests for base node address validation functionality. Tests String.isBaseNodeAddress() method with various hex/address combinations. Dependencies: XCTest, Tari_Aurora. Test scenarios: valid 64-character hex strings, onion3 addresses with proper format, IPv4 addresses with TCP ports, invalid hex lengths/characters, malformed onion/IP addresses, invalid port ranges. Ensures robust base node address validation for network connectivity and security. Critical for preventing invalid node connections. |
| `StringSimilarityTests.swift` | Unit test suite for string similarity algorithms used in contact and address book functionality. Tests string comparison methods for identifying similar contact names, addresses, or transaction descriptions. Validates minimum character matching thresholds and similarity scoring. Critical for features like contact search, duplicate detection, and fuzzy matching in the wallet's contact management system. Ensures reliable string matching for user experience improvements. |
| `StringTokenizationTests.swift` | Unit tests for string tokenization functionality. Tests String.tokenize() extension method for word separation and whitespace handling. Dependencies: XCTest, Tari_Aurora. Test scenarios: single words, multiple words with various spacing, trailing spaces, empty strings, multiple consecutive spaces. Validates tokenization logic used in seed phrase input parsing and text processing. Critical for ensuring accurate word separation in security-sensitive contexts like seed phrase entry. |
| `VersionValidatorTests.swift` | Unit test suite for VersionValidator class functionality. Tests semantic version comparison logic including major.minor.patch format, pre-release versions (pre, rc), and version precedence rules. Validates version string parsing, component comparison, and edge cases like extra components and different string lengths. Critical for ensuring proper app update validation and version checking throughout the wallet application. Part of comprehensive test coverage for utility functions. |
| `YatCacheManagerTests.swift` | Unit tests for YatCacheManager file caching functionality. Tests video file caching with hash-based naming and video orientation handling. Dependencies: XCTest, Tari_Aurora, FileManager. Test scenarios: file saving/loading, cache replacement, hash validation, vertical vs square video fallback logic, cache invalidation. Validates Yat emoji video caching system ensuring proper file management and orientation-specific caching behavior. Critical for Yat integration performance and storage management. |

#### UnitTests/Mocks/

| File | Description |
|------|-------------|
| `TariNetwork+Mocks.swift` | Mock extension for TariNetwork class used in unit testing. Provides test network configuration for isolated testing. Exports: TariNetwork.testNetwork static property. Dependencies: Tari_Aurora test target. Creates test network instance with safe test parameters (test_network name, Test Network display name, Test Symbol ticker). Essential for network-related unit tests requiring predictable network configuration without affecting production networks. |

#### UnitTests/WalletLib/

| File | Description |
|------|-------------|
| `SeedWordsTests.swift` | Unit tests for SeedWords class validation and initialization. Tests BIP39 mnemonic phrase validation with proper error handling. Dependencies: XCTest, Tari_Aurora. Test scenarios: valid 24-word phrases, invalid words detection, phrase length validation (too short/long), proper error type verification. Validates critical wallet security functionality ensuring only valid BIP39 mnemonics are accepted. Essential for wallet backup/restore security and preventing invalid seed phrase acceptance. |

### fastlane/

| File | Description |
|------|-------------|
| `Fastfile` | Fastlane automation configuration for iOS deployment and debugging. Implements dsym lane for uploading debug symbols to Sentry crash reporting service with configurable authentication, organization, project parameters, and dSYM file paths. Essential for automated crash reporting setup and release management. Enables continuous integration and deployment workflows with proper crash analysis capabilities. Used by CI/CD systems for automated App Store deployment and debug symbol management. |
| `Pluginfile` | [GENERATED] Fastlane plugin configuration file specifying required plugins for build automation. Currently includes fastlane-plugin-sentry for crash reporting integration during builds. Autogenerated and maintained by Fastlane gem management system. Essential for build pipeline functionality and Sentry integration for release monitoring. |

---
