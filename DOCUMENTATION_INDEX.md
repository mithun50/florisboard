# FlorisBoard Documentation Index

Complete guide to all documentation for FlorisBoard with Kannada font support.

## 📋 Table of Contents

- [Quick Links](#quick-links)
- [User Documentation](#user-documentation)
- [Developer Documentation](#developer-documentation)
- [GitHub Workflows](#github-workflows)
- [Font Resources](#font-resources)
- [Getting Help](#getting-help)

## 🔗 Quick Links

| Document | Purpose | Audience |
|----------|---------|----------|
| [README_KANNADA.md](README_KANNADA.md) | Main project overview | Everyone |
| [KANNADA_FONTS.md](docs/KANNADA_FONTS.md) | Font usage & customization | Users & Developers |
| [BUILD_INSTRUCTIONS.md](docs/BUILD_INSTRUCTIONS.md) | Build from source | Developers |
| [GitHub Workflows](.github/workflows/) | CI/CD automation | DevOps |

## 👥 User Documentation

### Getting Started

**[README_KANNADA.md](README_KANNADA.md)**
- Project overview
- Quick start guide
- Installation instructions
- Basic usage

### Font Customization

**[docs/KANNADA_FONTS.md](docs/KANNADA_FONTS.md)**

**Contents**:
- Overview & architecture
- Font installation guide
- Theme customization
- Advanced usage
- Troubleshooting
- Performance optimization

**Topics Covered**:
- Adding new fonts
- Creating custom themes
- Font properties reference
- Element targeting
- State-based styling
- Multi-font themes

### Troubleshooting

Located in: [docs/KANNADA_FONTS.md#troubleshooting](docs/KANNADA_FONTS.md#troubleshooting)

**Common Issues**:
- Font not rendering
- Font loads but looks wrong
- Performance issues
- Theme not appearing
- Debugging font loading

## 👨‍💻 Developer Documentation

### Building the App

**[docs/BUILD_INSTRUCTIONS.md](docs/BUILD_INSTRUCTIONS.md)**

**Contents**:
- Prerequisites & setup
- Local development
- Build commands
- GitHub Actions CI/CD
- Release process
- Troubleshooting

**Build Variants**:
- Debug builds
- Release builds
- Beta builds
- Custom variants

### Testing

Located in: [docs/BUILD_INSTRUCTIONS.md#testing](docs/BUILD_INSTRUCTIONS.md#testing)

**Test Types**:
- Unit tests
- Instrumentation tests
- Font-specific tests
- Code quality checks

### Contributing

**Guidelines**:
- Code style (ktlint)
- Testing requirements
- Documentation updates
- Pull request process
- Commit message format

See [README_KANNADA.md#contributing](README_KANNADA.md#contributing)

## 🤖 GitHub Workflows

### Workflow Files

Located in: `.github/workflows/`

| Workflow | File | Trigger | Purpose |
|----------|------|---------|---------|
| **Build** | `build.yml` | Push/PR to main | Compile & validate |
| **Test** | `test.yml` | Push/PR to main | Run test suites |
| **Release** | `release.yml` | Tag push (v*) | Create releases |

### Build Workflow

**[.github/workflows/build.yml](.github/workflows/build.yml)**

**Features**:
- Lint and code quality checks
- Build debug & release APKs
- Upload artifacts
- PR comments with build info
- Parallel matrix builds

**Triggers**:
- Push to `main` or `develop`
- Pull requests
- Manual dispatch

**Outputs**:
- Debug APK artifact
- Release APK artifact
- Lint reports

### Test Workflow

**[.github/workflows/test.yml](.github/workflows/test.yml)**

**Features**:
- Unit tests with coverage
- Instrumentation tests (API 28, 31, 34)
- Font-specific validation
- Test result reports
- Codecov integration

**Test Suites**:
1. **Unit Tests**: Fast, isolated tests
2. **Instrumentation Tests**: On-device tests
3. **Font Tests**: Font file & theme validation

**Outputs**:
- Test results (JUnit XML)
- Coverage reports (JaCoCo)
- Test artifacts

### Release Workflow

**[.github/workflows/release.yml](.github/workflows/release.yml)**

**Features**:
- Signed release APK
- Automated changelog
- GitHub Release creation
- Asset uploads (APK + checksums)
- Post-release notifications

**Process**:
1. Tag commit: `git tag v1.0.0`
2. Push tag: `git push origin v1.0.0`
3. Workflow runs automatically
4. Release published on GitHub

**Secrets Required**:
- `RELEASE_KEYSTORE` (base64 keystore)
- `KEYSTORE_PASSWORD`
- `KEY_ALIAS`
- `KEY_PASSWORD`

## 🎨 Font Resources

### Included Fonts

**Noto Sans Kannada** (Google Fonts)
- License: OFL 1.1
- Weights: Light (300), Regular (400), Medium (500), Bold (700)
- Source: https://github.com/notofonts/kannada
- Total Size: ~1.2 MB

### Additional Recommended Fonts

| Font | Style | License | Source |
|------|-------|---------|--------|
| **Kedage** | Modern, rounded | OFL | [fonts-kedage](https://github.com/santhoshtr/fonts-kedage) |
| **Tunga** | Traditional | Proprietary | Windows fonts |
| **Mallige** | Elegant | GPL | [fonts-mallige](https://github.com/santhoshtr/fonts-mallige) |
| **Lohit Kannada** | Technical | OFL | [lohit-fonts](https://github.com/lohit-fonts/lohit-kannada) |

### Font File Locations

```
app/src/main/assets/ime/
└── fonts/
    └── kannada/
        ├── NotoSansKannada-Regular.ttf  (287 KB)
        ├── NotoSansKannada-Bold.ttf     (287 KB)
        ├── NotoSansKannada-Light.ttf    (287 KB)
        └── NotoSansKannada-Medium.ttf   (287 KB)
```

### Theme File Location

```
app/src/main/assets/ime/
└── theme/
    └── org.florisboard.themes/
        ├── extension.json           (Theme registry)
        └── stylesheets/
            └── kannada_custom.json  (Kannada theme)
```

## 📚 Code Documentation

### Font Architecture

**Core Files**:

| File | Purpose |
|------|---------|
| `SnyggFontValue.kt` | Font value types |
| `SnyggTheme.kt` | Theme compilation & font loading |
| `SnyggPropertySet.kt` | Font property extraction |
| `SnyggUi.kt` | Font preloading & caching |
| `SnyggText.kt` | Text rendering with fonts |

**Key Classes**:
- `SnyggGenericFontFamilyValue`: System fonts
- `SnyggCustomFontFamilyValue`: Custom fonts
- `SnyggFontStyleValue`: Font styles
- `SnyggFontWeightValue`: Font weights
- `CompiledFontFamilyData`: Preloaded fonts map

### Font Loading Pipeline

```
1. Theme Definition (JSON)
   └─> @font rules + element styles

2. Theme Compilation (SnyggTheme)
   ├─> Parse @font rules
   ├─> Resolve file paths
   └─> Create FontFamily objects

3. Font Preloading (ProvideSnyggTheme)
   ├─> FontFamilyResolver.preload()
   └─> Cache in CompositionLocal

4. Style Query (rememberQuery)
   ├─> Match element + attributes
   └─> Extract font properties

5. Rendering (SnyggText)
   ├─> Lookup custom fonts
   └─> Apply to Compose Text
```

## 🛠️ Development Tools

### Required Software

| Tool | Version | Purpose |
|------|---------|---------|
| JDK | 17 | Compile code |
| Android SDK | API 31+ | Android development |
| Gradle | 8.4+ | Build automation |
| Git | 2.x | Version control |

### Optional Tools

- Android Studio (IDE)
- adb (Device testing)
- Python 3 (Font tools)
- Node.js (Automation)

### Build Commands Reference

```bash
# Development
./gradlew assembleDebug           # Debug build
./gradlew test                    # Unit tests
./gradlew lint                    # Lint checks
./gradlew ktlintFormat            # Fix style

# Release
./gradlew assembleRelease         # Release build
./gradlew check                   # All checks

# Cleanup
./gradlew clean                   # Clean build
rm -rf ~/.gradle/caches/          # Clear cache

# Installation
adb install -r app/build/outputs/apk/debug/app-debug.apk
```

## 📞 Getting Help

### Support Channels

| Channel | Use For | Link |
|---------|---------|------|
| **GitHub Issues** | Bug reports, feature requests | [Issues](https://github.com/florisboard/florisboard/issues) |
| **GitHub Discussions** | Questions, ideas | [Discussions](https://github.com/florisboard/florisboard/discussions) |
| **Matrix Chat** | Real-time help | [#florisboard:matrix.org](https://matrix.to/#/#florisboard:matrix.org) |
| **Reddit** | Community discussions | [r/FlorisBoard](https://reddit.com/r/FlorisBoard) |

### Reporting Issues

**Include**:
1. FlorisBoard version
2. Android version & device
3. Font name (if font-related)
4. Steps to reproduce
5. Logcat output
6. Screenshots (if UI issue)

**Template**:
```markdown
### Bug Description
[Clear description]

### Steps to Reproduce
1. Step one
2. Step two
3. ...

### Expected Behavior
[What should happen]

### Actual Behavior
[What actually happens]

### Environment
- FlorisBoard: v1.0.0
- Android: 13 (API 33)
- Device: Pixel 6

### Logs
```
[Logcat output]
```

### Screenshots
[Attach images]
```

### Feature Requests

**Template**:
```markdown
### Feature Description
[Clear description of feature]

### Use Case
[Why this feature is needed]

### Proposed Solution
[How you envision it working]

### Alternatives Considered
[Other approaches]

### Additional Context
[Any other relevant info]
```

## 🔄 Keeping Documentation Updated

### When to Update Docs

- **New features**: Add to relevant docs
- **Bug fixes**: Update troubleshooting
- **API changes**: Update code docs
- **Build changes**: Update BUILD_INSTRUCTIONS.md
- **Font changes**: Update KANNADA_FONTS.md

### Documentation Standards

- **Clear headings**: Use descriptive titles
- **Code examples**: Include working examples
- **Screenshots**: Add visual aids where helpful
- **Links**: Keep internal links working
- **Versions**: Note version-specific info

## 📊 Documentation Statistics

- **Total Documents**: 5
- **Total Words**: ~20,000
- **Code Examples**: 100+
- **Workflows**: 3
- **Test Cases**: 15+
- **Troubleshooting Items**: 20+

## 🗂️ Document Change Log

### 2025-10-29
- ✅ Created README_KANNADA.md
- ✅ Created docs/KANNADA_FONTS.md
- ✅ Created docs/BUILD_INSTRUCTIONS.md
- ✅ Created .github/workflows/build.yml
- ✅ Created .github/workflows/test.yml
- ✅ Created .github/workflows/release.yml
- ✅ Created DOCUMENTATION_INDEX.md (this file)

## 🎯 Quick Navigation

**For Users**:
1. Start here: [README_KANNADA.md](README_KANNADA.md)
2. Font guide: [docs/KANNADA_FONTS.md](docs/KANNADA_FONTS.md)
3. Need help? [Getting Help](#getting-help)

**For Developers**:
1. Start here: [docs/BUILD_INSTRUCTIONS.md](docs/BUILD_INSTRUCTIONS.md)
2. Workflows: [.github/workflows/](.github/workflows/)
3. Contributing: [README_KANNADA.md#contributing](README_KANNADA.md#contributing)

**For DevOps**:
1. CI/CD: [GitHub Workflows](#github-workflows)
2. Release: [docs/BUILD_INSTRUCTIONS.md#release-process](docs/BUILD_INSTRUCTIONS.md#release-process)
3. Secrets: [Release Workflow](#release-workflow)

---

**Maintained by**: FlorisBoard Community
**Last Updated**: 2025-10-29
**Version**: 1.0.0

**Need help finding something?** Open an issue or ask in Matrix chat!
