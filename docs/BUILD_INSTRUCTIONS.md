# FlorisBoard Build Instructions

Complete guide for building FlorisBoard from source with GitHub Actions automation.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Local Development Setup](#local-development-setup)
- [Building Locally](#building-locally)
- [GitHub Actions CI/CD](#github-actions-cicd)
- [Release Process](#release-process)
- [Troubleshooting](#troubleshooting)

## Prerequisites

### Required Software

| Tool | Minimum Version | Recommended | Purpose |
|------|----------------|-------------|---------|
| **JDK** | 17 | 17 (LTS) | Compile Java/Kotlin code |
| **Android SDK** | API 31 | API 34 | Android development |
| **Gradle** | 8.4 | 8.6+ | Build automation |
| **Git** | 2.x | Latest | Version control |

### Optional Tools

- **Android Studio**: IDE with visual tools (recommended for beginners)
- **adb**: Android Debug Bridge for device testing
- **Python 3**: For font subsetting and optimization scripts
- **Node.js**: For pre-commit hooks and automation

## Local Development Setup

### 1. Clone Repository

```bash
# HTTPS
git clone https://github.com/florisboard/florisboard.git
cd florisboard

# Or SSH
git clone git@github.com:florisboard/florisboard.git
cd florisboard
```

### 2. Install Java Development Kit

#### Ubuntu/Debian

```bash
sudo apt update
sudo apt install openjdk-17-jdk
java -version  # Verify installation
```

#### macOS

```bash
brew install openjdk@17
echo 'export PATH="/opt/homebrew/opt/openjdk@17/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

#### Windows

Download from: https://adoptium.net/temurin/releases/?version=17

### 3. Install Android SDK

#### Option A: Android Studio (Easiest)

1. Download: https://developer.android.com/studio
2. Install Android SDK via SDK Manager
3. Set environment variables:

```bash
# Linux/macOS (~/.bashrc or ~/.zshrc)
export ANDROID_HOME=$HOME/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools

# Windows (System Environment Variables)
ANDROID_HOME=C:\Users\YourName\AppData\Local\Android\Sdk
PATH=%PATH%;%ANDROID_HOME%\tools;%ANDROID_HOME%\platform-tools
```

#### Option B: Command Line Tools

```bash
# Download SDK command line tools
wget https://dl.google.com/android/repository/commandlinetools-linux-9477386_latest.zip
unzip commandlinetools-linux-9477386_latest.zip -d $HOME/android-sdk
cd $HOME/android-sdk/cmdline-tools
mkdir latest
mv bin lib NOTICE.txt source.properties latest/

# Set environment
export ANDROID_HOME=$HOME/android-sdk
export PATH=$PATH:$ANDROID_HOME/cmdline-tools/latest/bin

# Install required components
sdkmanager "platform-tools" "platforms;android-34" "build-tools;34.0.0"
```

### 4. Verify Setup

```bash
# Check Java
java -version
# Expected: openjdk version "17.x.x"

# Check Android SDK
echo $ANDROID_HOME
# Expected: /path/to/Android/Sdk

# Check Gradle (uses wrapper)
./gradlew --version
# Expected: Gradle 8.x
```

## Building Locally

### Quick Build

```bash
# Clean and build debug APK
./gradlew clean assembleDebug

# Output location:
# app/build/outputs/apk/debug/app-debug.apk
```

### Build Variants

FlorisBoard supports multiple build variants:

#### Debug Build

```bash
# Standard debug build
./gradlew assembleDebug

# With specific features
./gradlew assembleDebug -PenableKannada=true
```

#### Release Build

```bash
# Unsigned release APK
./gradlew assembleRelease

# Signed release APK (requires keystore)
./gradlew assembleRelease \
  -Pandroid.injected.signing.store.file=keystore.jks \
  -Pandroid.injected.signing.store.password=<password> \
  -Pandroid.injected.signing.key.alias=<alias> \
  -Pandroid.injected.signing.key.password=<password>
```

#### Beta Build

```bash
# Beta variant for testing
./gradlew assembleBeta
```

### Build Options

```bash
# Parallel builds (faster)
./gradlew assembleDebug --parallel

# Offline mode (use cached dependencies)
./gradlew assembleDebug --offline

# With stack trace (debugging)
./gradlew assembleDebug --stacktrace

# Clean build (removes all artifacts)
./gradlew clean assembleDebug

# Build specific module
./gradlew :app:assembleDebug
```

### Running Tests

```bash
# Unit tests
./gradlew test

# Unit tests with coverage
./gradlew testDebugUnitTest jacocoTestReport

# Instrumentation tests (requires device/emulator)
./gradlew connectedAndroidTest

# Specific test class
./gradlew test --tests "SnyggFontValueTest"

# Run all checks (lint + tests)
./gradlew check
```

### Code Quality

```bash
# Lint checks
./gradlew lint

# Kotlin lint
./gradlew ktlintCheck

# Fix Kotlin style issues
./gradlew ktlintFormat

# Detekt (static analysis)
./gradlew detekt
```

### Installing on Device

```bash
# Install debug APK
adb install app/build/outputs/apk/debug/app-debug.apk

# Install and replace existing
adb install -r app/build/outputs/apk/debug/app-debug.apk

# Install to specific device
adb -s <device-id> install app/build/outputs/apk/debug/app-debug.apk

# Uninstall
adb uninstall org.florisboard.app
```

## GitHub Actions CI/CD

### Workflow Files

All GitHub Actions workflows are stored in `.github/workflows/`:

```
.github/workflows/
├── build.yml           # Main build workflow
├── test.yml            # Test suite
├── release.yml         # Release automation
├── lint.yml            # Code quality checks
└── deploy-beta.yml     # Beta deployment
```

### Main Build Workflow

See: `.github/workflows/build.yml`

**Triggers**:
- Push to `main` branch
- Pull requests to `main`
- Manual workflow dispatch

**Jobs**:
1. **Lint**: Code quality checks
2. **Test**: Unit and integration tests
3. **Build**: Compile APK for multiple variants
4. **Upload Artifacts**: Store APK files

**Usage**:
- Automatic on PR/push
- Manual: GitHub → Actions → Build → Run workflow

### Test Workflow

See: `.github/workflows/test.yml`

**Features**:
- Runs on multiple Android API levels (28, 31, 34)
- Generates test coverage reports
- Uploads coverage to Codecov
- Fails PR if tests fail

### Release Workflow

See: `.github/workflows/release.yml`

**Triggers**:
- Git tag push matching `v*` (e.g., `v1.0.0`)

**Process**:
1. Build release APK
2. Sign with release keystore
3. Create GitHub Release
4. Upload signed APK
5. Generate changelog
6. Publish to F-Droid (optional)

**Creating Release**:

```bash
# Tag commit
git tag v1.0.0
git push origin v1.0.0

# GitHub Actions will automatically:
# - Build release APK
# - Create GitHub Release
# - Upload APK assets
```

### Secrets Configuration

Required GitHub Secrets (Settings → Secrets → Actions):

| Secret Name | Description | Example |
|-------------|-------------|---------|
| `RELEASE_KEYSTORE` | Base64-encoded keystore file | `base64 keystore.jks` |
| `KEYSTORE_PASSWORD` | Keystore password | `mySecurePassword123` |
| `KEY_ALIAS` | Key alias in keystore | `florisboard-release` |
| `KEY_PASSWORD` | Key password | `myKeyPassword456` |
| `GITHUB_TOKEN` | Auto-provided by GitHub | (automatic) |

**Encoding Keystore**:

```bash
# Encode keystore to base64
base64 -i keystore.jks | pbcopy  # macOS
base64 -w 0 keystore.jks          # Linux

# Add to GitHub Secrets as RELEASE_KEYSTORE
```

### Status Badges

Add to README.md:

```markdown
![Build Status](https://github.com/florisboard/florisboard/actions/workflows/build.yml/badge.svg)
![Tests](https://github.com/florisboard/florisboard/actions/workflows/test.yml/badge.svg)
![Release](https://github.com/florisboard/florisboard/actions/workflows/release.yml/badge.svg)
```

## Release Process

### Version Numbering

FlorisBoard uses Semantic Versioning (semver):

```
MAJOR.MINOR.PATCH[-PRERELEASE]
  ↓     ↓      ↓        ↓
  1  .  2  .   3   -beta.1
```

- **MAJOR**: Breaking changes
- **MINOR**: New features (backward compatible)
- **PATCH**: Bug fixes
- **PRERELEASE**: alpha, beta, rc

### Release Checklist

#### 1. Pre-Release

- [ ] Update version in `build.gradle.kts`
- [ ] Update `CHANGELOG.md`
- [ ] Run full test suite: `./gradlew check`
- [ ] Test on multiple devices/API levels
- [ ] Update documentation
- [ ] Review open issues/PRs

#### 2. Version Bump

```kotlin
// app/build.gradle.kts
android {
    defaultConfig {
        versionCode = 100  // Increment by 1
        versionName = "1.0.0"  // Update version
    }
}
```

#### 3. Create Release Commit

```bash
git add app/build.gradle.kts CHANGELOG.md docs/
git commit -m "chore: bump version to 1.0.0"
git push origin main
```

#### 4. Create Git Tag

```bash
# Lightweight tag
git tag v1.0.0

# Annotated tag (recommended)
git tag -a v1.0.0 -m "Release version 1.0.0"

# Push tag
git push origin v1.0.0
```

#### 5. GitHub Actions Automation

- Workflow automatically triggered by tag
- Builds and signs release APK
- Creates GitHub Release with changelog
- Uploads APK as release asset

#### 6. Post-Release

- [ ] Verify release on GitHub
- [ ] Test downloaded APK
- [ ] Announce on social media/forums
- [ ] Close milestone
- [ ] Create next milestone

### Beta Releases

```bash
# Beta version example: 1.0.0-beta.1
./gradlew assembleBeta

# Tag beta
git tag v1.0.0-beta.1
git push origin v1.0.0-beta.1

# Mark as pre-release on GitHub
```

### Hotfix Process

```bash
# Create hotfix branch
git checkout -b hotfix/1.0.1 v1.0.0

# Fix bug
git add .
git commit -m "fix: critical bug in font loading"

# Merge to main
git checkout main
git merge hotfix/1.0.1

# Tag hotfix
git tag v1.0.1
git push origin main v1.0.1
```

## Troubleshooting

### Build Failures

#### "SDK location not found"

```bash
# Create local.properties
echo "sdk.dir=$ANDROID_HOME" > local.properties

# Or set environment variable
export ANDROID_SDK_ROOT=$HOME/Android/Sdk
```

#### "Unsupported Java version"

```bash
# Check Java version
java -version

# Install JDK 17
# See "Install Java Development Kit" section

# Set JAVA_HOME
export JAVA_HOME=/path/to/jdk-17
```

#### "Could not resolve dependencies"

```bash
# Clear Gradle cache
rm -rf ~/.gradle/caches/

# Re-download dependencies
./gradlew build --refresh-dependencies
```

#### "Execution failed for task ':app:mergeDebugResources'"

```bash
# Clean build
./gradlew clean

# Invalidate caches (Android Studio)
# File → Invalidate Caches → Invalidate and Restart
```

### Test Failures

#### "No connected devices"

```bash
# List devices
adb devices

# Start emulator
emulator -avd <avd-name>

# Or use Gradle managed devices (automatic)
./gradlew connectedCheck
```

#### "Font tests failing"

```bash
# Verify font files exist
ls -la app/src/main/assets/ime/fonts/kannada/

# Check file permissions
chmod 644 app/src/main/assets/ime/fonts/kannada/*.ttf

# Run specific test
./gradlew test --tests "SnyggFontValueTest" --info
```

### GitHub Actions Issues

#### "Workflow not triggering"

- Check workflow file syntax (YAML validator)
- Verify branch name matches trigger
- Check repository permissions
- Review Actions tab for errors

#### "Secrets not available"

- Verify secrets are set in repository settings
- Check secret names match workflow file
- Ensure workflow has access to secrets (not forked PRs)

#### "Build timeout"

```yaml
# Increase timeout in workflow
jobs:
  build:
    timeout-minutes: 60  # Default: 360
```

### Performance Issues

#### Slow Gradle builds

```bash
# Enable parallel builds
echo "org.gradle.parallel=true" >> gradle.properties

# Increase memory
echo "org.gradle.jvmargs=-Xmx4096m" >> gradle.properties

# Enable build cache
echo "org.gradle.caching=true" >> gradle.properties
```

#### Slow test execution

```bash
# Run tests in parallel
./gradlew test --parallel --max-workers=4

# Skip slow tests
./gradlew test -PexcludeSlowTests
```

## Advanced Topics

### Custom Build Variants

```kotlin
// app/build.gradle.kts
android {
    flavorDimensions += "version"
    productFlavors {
        create("kannada") {
            dimension = "version"
            applicationIdSuffix = ".kannada"
            versionNameSuffix = "-kannada"
        }
    }
}
```

Build:
```bash
./gradlew assembleKannadaDebug
```

### ProGuard/R8 Configuration

```kotlin
// app/build.gradle.kts
android {
    buildTypes {
        release {
            isMinifyEnabled = true
            isShrinkResources = true
            proguardFiles(
                getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro"
            )
        }
    }
}
```

### Signing Configuration

```kotlin
// app/build.gradle.kts
android {
    signingConfigs {
        create("release") {
            storeFile = file("keystore.jks")
            storePassword = System.getenv("KEYSTORE_PASSWORD")
            keyAlias = System.getenv("KEY_ALIAS")
            keyPassword = System.getenv("KEY_PASSWORD")
        }
    }
    buildTypes {
        release {
            signingConfig = signingConfigs.getByName("release")
        }
    }
}
```

## Resources

### Documentation

- **Android Developer Guide**: https://developer.android.com/guide
- **Gradle Docs**: https://docs.gradle.org/
- **Kotlin Docs**: https://kotlinlang.org/docs/home.html
- **Jetpack Compose**: https://developer.android.com/jetpack/compose

### Tools

- **Android Studio**: https://developer.android.com/studio
- **Gradle Build Tool**: https://gradle.org/
- **GitHub Actions**: https://docs.github.com/en/actions

### Community

- **GitHub Issues**: https://github.com/florisboard/florisboard/issues
- **Matrix Chat**: #florisboard:matrix.org
- **Discussions**: https://github.com/florisboard/florisboard/discussions

---

**Last Updated**: 2025-10-29
**Maintainers**: FlorisBoard Build Team
