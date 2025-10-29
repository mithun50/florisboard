# Kannada Fonts Implementation Guide

## Overview

FlorisBoard now supports custom Kannada fonts through the Snygg theming system. This guide covers installation, customization, and troubleshooting of Kannada fonts.

## Table of Contents

- [Quick Start](#quick-start)
- [Font Architecture](#font-architecture)
- [Available Fonts](#available-fonts)
- [Customization](#customization)
- [Advanced Usage](#advanced-usage)
- [Troubleshooting](#troubleshooting)
- [Performance Optimization](#performance-optimization)

## Quick Start

### Activate Kannada Theme

1. **Open FlorisBoard Settings**
2. Navigate to **Appearance → Theme**
3. Select **"Kannada Custom (Noto Sans)"**
4. The keyboard will reload with custom Kannada fonts

### Test Kannada Input

1. Enable Kannada keyboard layout (if not already enabled)
2. Switch to Kannada input
3. Type Kannada characters - they will render with Noto Sans Kannada font

## Font Architecture

### Directory Structure

```
app/src/main/assets/ime/
├── fonts/
│   └── kannada/
│       ├── NotoSansKannada-Regular.ttf
│       ├── NotoSansKannada-Bold.ttf
│       ├── NotoSansKannada-Light.ttf
│       └── NotoSansKannada-Medium.ttf
└── theme/
    └── org.florisboard.themes/
        ├── extension.json
        └── stylesheets/
            └── kannada_custom.json
```

### Font Loading Process

1. **Theme Compilation**: `SnyggTheme.compileFrom()` parses `@font` rules
2. **Font Resolution**: Resolves `ime:///` URIs to actual file paths
3. **Font Creation**: Creates `Font` objects with specified weight/style
4. **Font Preloading**: `FontFamilyResolver.preload()` loads fonts at initialization
5. **Caching**: Fonts cached in `CompiledFontFamilyData` map
6. **Rendering**: Applied via `SnyggText` composables throughout UI

### Font Fallback Chain

```
Custom Font → Generic Family → System Default
     ↓              ↓                ↓
NotoSansKannada → sans-serif → Roboto
```

## Available Fonts

### Noto Sans Kannada (Included)

**License**: OFL (Open Font License)
**Source**: Google Fonts
**Coverage**: Complete Kannada Unicode range

| Weight | File | Use Case |
|--------|------|----------|
| Light (300) | `NotoSansKannada-Light.ttf` | Hints, secondary text |
| Regular (400) | `NotoSansKannada-Regular.ttf` | Primary keyboard text |
| Medium (500) | `NotoSansKannada-Medium.ttf` | Popups, emphasized text |
| Bold (700) | `NotoSansKannada-Bold.ttf` | Action keys, headers |

### Additional Recommended Fonts

#### Kedage
- **Style**: Modern, rounded
- **Best For**: Casual, friendly interface
- **Download**: https://github.com/santhoshtr/fonts-kedage

#### Tunga
- **Style**: Traditional, professional
- **Best For**: Formal, traditional appearance
- **Included**: Windows fonts (license check required)

#### Mallige
- **Style**: Elegant, refined
- **Best For**: High-end, stylish interfaces
- **Download**: https://github.com/santhoshtr/fonts-mallige

#### Lohit Kannada
- **Style**: Clean, technical
- **Best For**: Technical documentation, code
- **Download**: https://github.com/lohit-fonts/lohit-kannada

## Customization

### Adding New Fonts

#### 1. Download Font Files

```bash
# Example: Adding Kedage font
cd app/src/main/assets/ime/fonts/kannada/
curl -L "https://github.com/santhoshtr/fonts-kedage/raw/master/Kedage-Regular.ttf" -o Kedage-Regular.ttf
curl -L "https://github.com/santhoshtr/fonts-kedage/raw/master/Kedage-Bold.ttf" -o Kedage-Bold.ttf
```

#### 2. Define Font in Theme

Edit `stylesheets/kannada_custom.json`:

```json
{
  "@font `Kedage`": [
    {
      "src": "ime:///fonts/kannada/Kedage-Regular.ttf",
      "font-weight": "normal",
      "font-style": "normal"
    },
    {
      "src": "ime:///fonts/kannada/Kedage-Bold.ttf",
      "font-weight": "bold",
      "font-style": "normal"
    }
  ]
}
```

#### 3. Apply Font to Elements

```json
{
  "key": {
    "font-family": "`Kedage`",
    "font-size": "24sp",
    "font-weight": "normal"
  }
}
```

#### 4. Rebuild and Test

```bash
./gradlew clean assembleDebug
adb install -r app/build/outputs/apk/debug/app-debug.apk
```

### Creating Custom Themes

#### Minimal Theme Example

```json
{
  "$schema": "https://schemas.florisboard.org/snygg/v2/stylesheet",

  "@font `MyKannadaFont`": [
    {
      "src": "ime:///fonts/kannada/MyFont.ttf",
      "font-weight": "normal"
    }
  ],

  "key": {
    "font-family": "`MyKannadaFont`",
    "font-size": "24sp"
  }
}
```

#### Multi-Font Theme Example

```json
{
  "@font `PrimaryFont`": [
    { "src": "ime:///fonts/kannada/Primary.ttf" }
  ],
  "@font `SecondaryFont`": [
    { "src": "ime:///fonts/kannada/Secondary.ttf" }
  ],
  "@font `AccentFont`": [
    { "src": "ime:///fonts/kannada/Accent.ttf" }
  ],

  "key": {
    "font-family": "`PrimaryFont`"
  },
  "key-hint": {
    "font-family": "`SecondaryFont`"
  },
  "smartbar": {
    "font-family": "`AccentFont`"
  }
}
```

### Font Properties Reference

#### Font Family

```json
// Generic families
"font-family": "system"         // System default
"font-family": "sans-serif"     // Sans-serif fallback
"font-family": "serif"          // Serif fallback
"font-family": "monospace"      // Monospace
"font-family": "cursive"        // Cursive

// Custom families (backticks required!)
"font-family": "`NotoSansKannada`"
```

#### Font Weight

```json
// Named weights
"font-weight": "thin"           // 100
"font-weight": "extra-light"    // 200
"font-weight": "light"          // 300
"font-weight": "normal"         // 400 (default)
"font-weight": "medium"         // 500
"font-weight": "semi-bold"      // 600
"font-weight": "bold"           // 700
"font-weight": "extra-bold"     // 800
"font-weight": "black"          // 900

// Numeric weights
"font-weight": "400"
```

#### Font Style

```json
"font-style": "normal"          // Default
"font-style": "italic"          // Italic
```

#### Font Size

```json
"font-size": "24sp"             // Scaled pixels (recommended)
"font-size": "16dp"             // Device pixels
```

#### Letter Spacing

```json
"letter-spacing": "0.5sp"       // Add spacing
"letter-spacing": "-0.2sp"      // Reduce spacing
```

#### Line Height

```json
"line-height": "28sp"           // Explicit height
"line-height": "1.5em"          // Relative to font-size
```

### Element Targeting

#### Keyboard Keys

```json
{
  "key": {},                    // All keys
  "key:pressed": {},           // Pressed state
  "key[code=10]": {},          // Enter key
  "key[code=32]": {},          // Spacebar
  "key[code=-11]": {},         // Shift key
  "key-hint": {},              // Hint text
  "key-popup-box": {},         // Long-press popup
}
```

#### Smartbar

```json
{
  "smartbar": {},                      // Entire smartbar
  "smartbar-candidate-word": {},       // Suggestions
  "smartbar-action-key": {},           // Action buttons
  "smartbar-action-tile": {}           // Action tiles
}
```

#### Clipboard

```json
{
  "clipboard-item": {},                // Clipboard items
  "clipboard-header": {},              // Header text
  "clipboard-filter-chip": {}          // Filter chips
}
```

#### Emoji Panel

```json
{
  "media-emoji-key": {},               // Emoji keys
  "media-emoji-subheader": {}          // Category headers
}
```

## Advanced Usage

### Conditional Font Loading

```json
{
  "@font `HeavyFont`": [
    {
      "src": "ime:///fonts/kannada/Heavy.ttf",
      "font-weight": "bold"
    }
  ],

  "key": {
    "font-family": "`NotoSansKannada`",
    "font-weight": "normal"
  },

  "key:pressed": {
    "font-family": "`HeavyFont`",
    "font-weight": "bold"
  }
}
```

### State-Based Font Styles

```json
{
  "key": {
    "font-family": "`NotoSansKannada`",
    "font-weight": "normal"
  },

  "key:pressed": {
    "font-weight": "bold"
  },

  "key[code=-11][shiftstate=`caps_lock`]": {
    "font-weight": "extra-bold",
    "font-style": "italic"
  }
}
```

### Attribute-Based Styling

```json
{
  "key[code=-201,-202,-203]": {
    "font-size": "20sp",
    "font-weight": "semi-bold"
  }
}
```

## Troubleshooting

### Font Not Rendering

**Symptom**: Keys show default font instead of custom Kannada font

**Causes & Solutions**:

1. **Incorrect Path**
   ```json
   // ❌ Wrong
   "src": "fonts/kannada/Font.ttf"

   // ✅ Correct
   "src": "ime:///fonts/kannada/Font.ttf"
   ```

2. **Missing Backticks**
   ```json
   // ❌ Wrong
   "font-family": "NotoSansKannada"

   // ✅ Correct
   "font-family": "`NotoSansKannada`"
   ```

3. **File Not Exists**
   ```bash
   # Verify file exists
   ls -la app/src/main/assets/ime/fonts/kannada/
   ```

4. **Case Sensitivity**
   - File names are case-sensitive
   - Ensure exact match between `src` path and actual filename

### Font Loads But Looks Wrong

**Symptom**: Font displays but characters appear broken or incorrect

**Causes & Solutions**:

1. **Wrong Font Format**
   - Only `.ttf` and `.otf` supported
   - Convert other formats to TTF

2. **Corrupted Font File**
   ```bash
   # Re-download font
   curl -L "URL" -o FontName.ttf
   ```

3. **Missing Glyphs**
   - Font doesn't support Kannada script
   - Use fonts with Kannada Unicode coverage

4. **Font Weight Mismatch**
   ```json
   // Ensure weight matches file
   {
     "src": "NotoSansKannada-Bold.ttf",
     "font-weight": "bold"  // Must match!
   }
   ```

### Performance Issues

**Symptom**: Keyboard lags or slow to load

**Causes & Solutions**:

1. **Too Many Fonts**
   - Limit to 3-5 font families per theme
   - Remove unused fonts

2. **Large Font Files**
   ```bash
   # Check font sizes
   ls -lh app/src/main/assets/ime/fonts/kannada/

   # Aim for < 500KB per file
   # Consider font subsetting for Kannada-only glyphs
   ```

3. **Blocking Preload**
   - Font preloading happens at theme init
   - Large fonts delay keyboard appearance
   - **Fix**: Use smaller font files or lazy loading

### Theme Not Appearing

**Symptom**: Custom theme doesn't show in theme selector

**Causes & Solutions**:

1. **Not Registered**
   - Check `extension.json` includes theme entry

2. **Invalid JSON**
   ```bash
   # Validate JSON
   cat stylesheets/kannada_custom.json | python -m json.tool
   ```

3. **Missing Required Fields**
   ```json
   {
     "id": "required",
     "label": "required",
     "authors": ["required"],
     "isNight": false  // required
   }
   ```

4. **Rebuild Required**
   ```bash
   ./gradlew clean assembleDebug
   ```

### Debugging Font Loading

#### Check Logs

```bash
# Filter for font-related logs
adb logcat | grep -i "font\|snygg\|typeface"
```

#### Common Error Messages

```
Error: Font file not found: ime:///fonts/kannada/Font.ttf
→ File doesn't exist or path incorrect

Error: Failed to preload font family: NotoSansKannada
→ Font file corrupted or invalid format

Warning: Font weight 'bold' not found, using default
→ Bold weight file missing or not defined
```

## Performance Optimization

### Font File Optimization

#### 1. Font Subsetting

Extract only Kannada glyphs:

```bash
# Using pyftsubset (pip install fonttools)
pyftsubset NotoSansKannada-Regular.ttf \
  --unicodes=U+0C80-U+0CFF \
  --output-file=NotoSansKannada-Regular-Subset.ttf

# Reduces file size by 50-80%
```

#### 2. Font Compression

```bash
# WOFF2 compression (web fonts)
# Note: Android needs TTF/OTF, but can compress TTF internally

# Optimize TTF
ttfautohint NotoSansKannada-Regular.ttf \
  NotoSansKannada-Regular-Hinted.ttf
```

#### 3. Limit Font Weights

```json
// Instead of 9 weights:
"@font `Heavy`": [
  { "src": "...-Thin.ttf", "font-weight": "100" },
  { "src": "...-Light.ttf", "font-weight": "300" },
  // ... 7 more weights
]

// Use only essential weights:
"@font `Optimized`": [
  { "src": "...-Regular.ttf", "font-weight": "normal" },
  { "src": "...-Bold.ttf", "font-weight": "bold" }
]
```

### Memory Management

#### Font Caching Strategy

- **Preload**: Fonts loaded once at theme init
- **Cache**: Stored in `CompiledFontFamilyData` map
- **Reuse**: Same font instance across all text elements
- **Lifecycle**: Cached until theme change

#### Memory Footprint

```
Font File Sizes (Approximate):
- Light:  250-300 KB
- Regular: 280-350 KB
- Bold:   290-360 KB
- Full family (4 weights): ~1.2 MB

In-Memory (After Preload):
- Compressed: ~500-700 KB per family
- Total overhead: Minimal (< 2 MB for typical theme)
```

### Best Practices

1. **Lazy Font Loading**: Load fonts only when Kannada layout active (future enhancement)
2. **Font Pooling**: Reuse font instances across themes
3. **Async Preload**: Move preloading off UI thread (future enhancement)
4. **Smart Fallbacks**: Use generic families as fallbacks
5. **Test on Low-End Devices**: Verify performance on older Android devices

## Contributing

### Adding New Fonts

1. Verify font license (OFL, GPL, Apache 2.0 preferred)
2. Test Kannada script coverage
3. Optimize file size (< 500 KB ideal)
4. Create theme JSON
5. Test on multiple devices
6. Submit pull request with:
   - Font files
   - Theme definition
   - Documentation
   - Screenshots

### Reporting Issues

Include:
- FlorisBoard version
- Android version
- Device model
- Font name and version
- Steps to reproduce
- Logcat output

## Resources

### Font Sources

- **Google Fonts**: https://fonts.google.com/?subset=kannada
- **Font Library**: https://fontlibrary.org/
- **GitHub Kannada Fonts**: Search "kannada font" on GitHub

### Documentation

- **Snygg Spec**: `lib/snygg/README.md`
- **Theme Guide**: https://florisboard.org/docs/themes
- **Font Technical Details**: `docs/FONT_ARCHITECTURE.md`

### Community

- **GitHub Issues**: https://github.com/florisboard/florisboard/issues
- **Matrix Chat**: #florisboard:matrix.org
- **Reddit**: r/FlorisBoard

## License

Font files included in this distribution:
- **Noto Sans Kannada**: OFL 1.1 (Google)

FlorisBoard code: Apache 2.0

---

**Last Updated**: 2025-10-29
**Version**: 1.0.0
**Maintainers**: FlorisBoard Community
