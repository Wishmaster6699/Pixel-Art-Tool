# Pixel Art Grid Quantizer - Complete Project Plan

## 🎯 Project Overview

A comprehensive web-based tool for converting AI-generated images into production-ready pixel art assets for 2D retro-style games.

### Core Problem Statement
AI-generated images (especially from Gemini 3 Pro) often look good from a distance but have critical issues for pixel art game development:
- Blurry pixels with anti-aliasing artifacts
- Inconsistent color palettes (49 similar colors instead of 1)
- Wrong resolutions (2-3x larger than reference with alignment issues)
- Fake transparency backgrounds (white or checkerboard patterns)

### Solution
A single-file web application that processes AI-generated images through multiple stages to produce clean, game-ready pixel art sprites.

---

## ✅ Current Implementation (v1.0)

### Features Completed
- **4x4 Grid Quantization** with solidity threshold algorithm
- **Majority Vote System** - analyzes each block, fills with dominant color or clears as transparent
- **Adjustable Grid Size** (1-16 pixels)
- **Solidity Threshold Slider** (1-16, controls strictness of block filling)
- **Side-by-side comparison** (Original vs Processed)
- **Download functionality** (PNG export)
- **Processing statistics** display
- **Dark mode UI** with responsive design

### Algorithm
```
For each NxN grid block:
  1. Count solid (non-transparent) pixels
  2. IF (solid pixels >= threshold):
     → Find most common color in block
     → Fill entire block with that color
  3. ELSE:
     → Make entire block transparent
```

---

## 🚀 Planned Features (Roadmap)

### **PHASE 1: Image Analysis & Import** ⭐ HIGH PRIORITY

#### 1.1 Automatic Image Analysis
**Purpose:** Provide intelligent suggestions when user uploads an image

**Features:**
- Detect image resolution on upload
- Analyze pixel patterns to suggest optimal grid size
- Detect if resolution is a clean multiple (2x, 3x, 4x) of common sprite sizes
- Display analysis results in UI panel

**Implementation Strategy:**
```javascript
function analyzeImage(imageData) {
  const resolution = { width: imageData.width, height: imageData.height };

  // Detect common factors
  const commonSpriteSizes = [16, 32, 48, 64, 96, 128, 192, 256];
  const suggestedGridSizes = [];

  // Find patterns - sample the image for repeating color blocks
  // Suggest grid size based on detected block patterns

  // Check if resolution is clean multiple of reference sizes
  // e.g., 192x256 is exactly 3x of 64x64 sprites

  return {
    resolution,
    suggestedGridSize: 4,
    suggestedDownscale: "3x → 1x (192x256 → 64x64)",
    detectedPatterns: "4x4 color blocks detected"
  };
}
```

**UI Elements:**
- Analysis panel showing:
  - Current resolution
  - Detected grid pattern
  - Suggested grid size with reasoning
  - Recommended downscale ratio (if needed)

---

#### 1.2 Resolution Alignment Solver
**Purpose:** Fix the "missing 2-3 pixels" problem when Gemini generates larger images

**Problem:**
- User provides 64x64 reference to Gemini
- Gemini generates 192x256 (roughly 3x larger)
- Scaling down in Aseprite results in 2-3 pixel rows/columns missing

**Root Cause:**
- Non-integer scaling ratios (e.g., 192/64 = 3.0, but 256/64 = 4.0)
- Uneven dimensions that don't divide cleanly

**Solution Approach:**

**Method 1: Smart Crop/Pad**
```javascript
function alignToTarget(sourceImage, targetWidth, targetHeight) {
  // Calculate how much to crop or pad
  const currentRatio = sourceImage.width / sourceImage.height;
  const targetRatio = targetWidth / targetHeight;

  // If source is wider than target ratio, crop width
  // If source is taller than target ratio, crop height

  // Crop from center to maintain subject
  // Then scale to exact target dimensions
}
```

**Method 2: Integer Scale Detection**
```javascript
function findBestIntegerScale(sourceWidth, sourceHeight, targetWidth, targetHeight) {
  // Try scale factors: 2x, 3x, 4x, 5x
  // Find which gives closest match
  // Suggest pre-crop dimensions that will scale perfectly

  // Example: 192x256 → suggest crop to 192x192 → scale to 64x64
}
```

**UI Features:**
- **"Target Resolution" input** - user enters desired final size
- **Alignment Preview** - shows what will be cropped/padded
- **Center/Top/Bottom/Left/Right crop options**
- **"Fix Alignment" button** - applies smart crop before processing

**Reference Research:**
- [Integer scaling for pixel-perfect results](https://tanalin.com/en/articles/integer-scaling/)
- [Pixel-perfect scaling tool](https://yal.cc/tools/upscale/)

---

#### 1.3 Background Removal Tool
**Purpose:** Remove fake transparency (white or checkerboard backgrounds) that Gemini adds

**Problem:**
- Gemini ignores transparency requests
- Adds white backgrounds or gray/white checkerboard patterns
- User needs to manually remove these in other tools

**Solution Strategies:**

**A. Chroma Key Removal (for solid backgrounds)**
```javascript
function removeBackgroundColor(imageData, targetColor, threshold) {
  // For each pixel:
  //   Calculate color distance from targetColor
  //   If distance < threshold:
  //     Set alpha to 0 (transparent)

  // Use Euclidean distance in RGB space:
  // distance = sqrt((R1-R2)² + (G1-G2)² + (B1-B2)²)
}
```

**B. Checkerboard Pattern Detection**
```javascript
function detectAndRemoveCheckerboard(imageData) {
  // Common checkerboard patterns:
  // - Light gray (#C0C0C0) + Dark gray (#808080)
  // - White (#FFFFFF) + Light gray (#E0E0E0)

  // Algorithm:
  // 1. Sample corners to detect checkerboard colors
  // 2. Check for alternating pattern (pixel[x,y] != pixel[x+1,y])
  // 3. If pattern detected, remove both colors

  // More robust: Use flood fill from corners
}
```

**C. Magic Cut (AI-like edge detection)**
```javascript
function magicCut(imageData) {
  // 1. Detect subject edges using Sobel/Canny edge detection
  // 2. Flood fill from corners (assume corners are background)
  // 3. Everything outside edges becomes transparent
  // 4. Optionally: expand/contract mask by N pixels for cleanup
}
```

**UI Features:**
- **Background Removal Mode:**
  - "Solid Color" (chroma key)
  - "Checkerboard Pattern"
  - "Magic Cut" (auto-detect)
- **Color Picker** for chroma key
- **Threshold Slider** for color tolerance (0-100)
- **Preview toggle** to show/hide transparency checkerboard
- **"Remove Background" button**

**Reference Research:**
- [Remove PNG Chroma Key](https://onlinepngtools.com/remove-png-chroma-key)
- [Remove Fake Transparent Background](https://www.photopea.com/tuts/remove-fake-transparency-online/)
- [Python checkerboard removal](https://stackoverflow.com/questions/74134195/how-to-replace-a-checked-pattern-in-a-png-image-with-transparent-in-python)

---

### **PHASE 2: Color Palette Management** ⭐ HIGH PRIORITY

#### 2.1 K-Means Color Quantization
**Purpose:** Reduce "49 similar colors" to single consistent colors

**Algorithm:**
```javascript
function kMeansQuantize(imageData, numColors, maxIterations = 10) {
  // 1. Extract all unique colors from image
  // 2. Initialize K random centroids (or use K-means++)
  // 3. Iterate:
  //    a. Assign each pixel to nearest centroid
  //    b. Recalculate centroids as average of assigned pixels
  //    c. Repeat until convergence or max iterations
  // 4. Remap every pixel to its nearest centroid color

  // Return: { processedImage, palette }
}
```

**Color Distance Calculation:**
```javascript
function colorDistance(color1, color2, colorSpace = 'RGB') {
  if (colorSpace === 'RGB') {
    // Euclidean distance
    return Math.sqrt(
      Math.pow(color1.r - color2.r, 2) +
      Math.pow(color1.g - color2.g, 2) +
      Math.pow(color1.b - color2.b, 2)
    );
  } else if (colorSpace === 'LAB') {
    // Delta-E (perceptually uniform)
    // Convert RGB → LAB first, then calculate
    // More accurate for human perception
  }
}
```

**UI Features:**
- **"Target Colors" slider** (2-256 colors)
- **Color Space selector** (RGB vs LAB)
- **Dithering options:**
  - None (flat colors)
  - Floyd-Steinberg
  - Ordered/Bayer
- **Live palette preview** showing final colors
- **Before/After comparison**

**Reference Research:**
- K-means implementation: ~200-300 lines of JavaScript
- Alternative: Median Cut algorithm (faster, simpler)
- Reference: [Image Quantization Guide](https://www.numberanalytics.com/blog/art-image-quantization)

---

#### 2.2 Palette Import/Export
**Purpose:** Maintain consistency across sprite sheet frames

**Features:**

**Export Palette:**
- Extract palette from processed image
- Sort by frequency or hue
- Export formats:
  - `.pal` (JASC-PAL format)
  - `.gpl` (GIMP Palette)
  - `.hex` (text file with hex codes)
  - `.json` (RGB values array)
  - `.ase` (Adobe Swatch Exchange)

**Import Palette:**
- Load existing palette file
- Force all future images to use only these colors
- Useful workflow: Process frame 1 → extract palette → apply to frames 2-N

**Lock Palette Mode:**
- Checkbox: "Lock to current palette"
- All subsequent processing uses same colors
- Ensures perfect consistency across sprite sheet

**Implementation:**
```javascript
function exportPalette(palette, format) {
  switch(format) {
    case 'hex':
      return palette.map(c => rgbToHex(c)).join('\n');
    case 'json':
      return JSON.stringify(palette, null, 2);
    case 'pal':
      return generateJASCPAL(palette);
    // etc.
  }
}

function importPalette(fileContent, format) {
  // Parse format and extract RGB values
  return paletteArray;
}
```

---

#### 2.3 Preset Retro Palettes
**Purpose:** Quick application of classic system palettes

**Included Palettes:**
- NES (54 colors)
- Game Boy (4 shades of green)
- Game Boy Color (32 colors)
- Commodore 64 (16 colors)
- Amstrad CPC (27 colors)
- PICO-8 (16 colors)
- Atari 2600
- Sega Genesis
- SNES
- Custom user palettes

**UI:**
- Dropdown selector
- Palette preview swatch
- "Apply Palette" button
- Option to save custom palettes

**Reference:**
- [Lospec Palette List](https://lospec.com/palette-list) - database of retro palettes

---

### **PHASE 3: Batch Processing & Sprite Sheets** ⭐ HIGH PRIORITY

#### 3.1 Multi-Image Upload
**Purpose:** Process entire sprite sheets at once

**Features:**
- Drag-and-drop multiple images
- Or upload entire sprite sheet (auto-split into frames)
- Apply same settings to all frames
- Maintain color palette consistency

**Implementation:**
```javascript
function batchProcess(images, settings) {
  let globalPalette = null;

  const results = images.map((img, index) => {
    // Process with grid quantization
    let processed = processImage(img, settings);

    // If first image, extract palette
    if (index === 0 && settings.maintainPalette) {
      globalPalette = extractPalette(processed, settings.targetColors);
    }

    // Apply global palette to subsequent images
    if (globalPalette) {
      processed = applyPalette(processed, globalPalette);
    }

    return processed;
  });

  return results;
}
```

**UI Features:**
- **Image list** showing all uploaded frames
- **Thumbnail previews**
- **Process All button**
- **Progress bar** during batch processing
- **Export options:**
  - Download as ZIP (individual frames)
  - Download as sprite sheet (combined)
  - Download palette file

---

#### 3.2 Sprite Sheet Auto-Split
**Purpose:** Upload one sprite sheet, auto-detect frames

**Algorithm:**
```javascript
function autoDetectFrames(spriteSheet, frameWidth, frameHeight) {
  // Divide sprite sheet into grid
  const cols = Math.floor(spriteSheet.width / frameWidth);
  const rows = Math.floor(spriteSheet.height / frameHeight);

  const frames = [];
  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      // Extract frame at [col, row]
      const frame = extractRegion(
        spriteSheet,
        col * frameWidth,
        row * frameHeight,
        frameWidth,
        frameHeight
      );
      frames.push(frame);
    }
  }

  return frames;
}
```

**UI:**
- **"Upload Sprite Sheet" button**
- **Frame dimensions input** (width x height)
- **Auto-detect button** (tries to guess frame size)
- **Grid overlay preview** showing detected frames
- **Manual adjustment** if auto-detect fails

**Reference:**
- [Top Down Sprite Maker crop/padding](https://flinkerflitzer.itch.io/tdsm)

---

#### 3.3 Sprite Sheet Reassembly
**Purpose:** Combine processed frames back into sprite sheet

**Features:**
- **Layout options:**
  - Horizontal strip
  - Vertical strip
  - Grid (NxM)
  - Custom arrangement
- **Padding options:**
  - No padding
  - 1px border
  - 2px border
  - Custom padding
- **Background:**
  - Transparent
  - Custom color

**Implementation:**
```javascript
function reassembleSpriteSheet(frames, layout, padding) {
  const frameWidth = frames[0].width;
  const frameHeight = frames[0].height;

  const cols = layout.cols;
  const rows = Math.ceil(frames.length / cols);

  const sheetWidth = cols * (frameWidth + padding) - padding;
  const sheetHeight = rows * (frameHeight + padding) - padding;

  // Create canvas and draw frames with padding
  // ...
}
```

---

### **PHASE 4: Advanced Processing Tools**

#### 4.1 Auto-Crop to Content
**Purpose:** Remove empty space around sprites

**Algorithm:**
```javascript
function autoCrop(imageData) {
  // Find bounding box of non-transparent pixels
  let minX = imageData.width, maxX = 0;
  let minY = imageData.height, maxY = 0;

  for (let y = 0; y < imageData.height; y++) {
    for (let x = 0; x < imageData.width; x++) {
      const alpha = getPixelAlpha(imageData, x, y);
      if (alpha > 0) {
        minX = Math.min(minX, x);
        maxX = Math.max(maxX, x);
        minY = Math.min(minY, y);
        maxY = Math.max(maxY, y);
      }
    }
  }

  // Crop to bounding box
  return cropRegion(imageData, minX, minY, maxX - minX + 1, maxY - minY + 1);
}
```

**UI:**
- **"Auto-Crop" button**
- **Option to add padding** after crop (1-8 pixels)
- **Preview showing crop bounds**

---

#### 4.2 Canvas Padding & Alignment
**Purpose:** Add padding to align sprite to grid or target dimensions

**Features:**
- **Target dimensions input** (e.g., "make it 64x64")
- **Padding distribution:**
  - Center (equal padding on all sides)
  - Top-left align
  - Bottom-center (for character sprites)
  - Custom offsets
- **Grid alignment** (ensure dimensions are multiples of grid size)

**Use Case:**
Gemini generates 58x62 sprite → Auto-crop → Pad to 64x64 → Process

---

#### 4.3 Dithering Engine
**Purpose:** Optional dithering when reducing to very limited palettes

**Algorithms:**

**Floyd-Steinberg:**
```javascript
function floydSteinbergDither(imageData, palette) {
  // For each pixel (left to right, top to bottom):
  //   1. Find nearest palette color
  //   2. Calculate quantization error
  //   3. Distribute error to neighboring pixels:
  //      - Right pixel: 7/16 of error
  //      - Bottom-left: 3/16
  //      - Bottom: 5/16
  //      - Bottom-right: 1/16
}
```

**Ordered (Bayer) Dithering:**
```javascript
function bayerDither(imageData, palette) {
  // Use Bayer matrix (4x4 or 8x8)
  // Add threshold pattern to image before quantization
  // Results in retro-style patterned dithering
}
```

**UI:**
- **Dithering toggle**
- **Algorithm selector** (None / Floyd-Steinberg / Bayer)
- **Strength slider** (0-100%)

---

#### 4.4 Edge-Aware Processing
**Purpose:** Preserve important edges during quantization

**Algorithm:**
```javascript
function edgeAwareQuantization(imageData, gridSize, threshold) {
  // 1. Run edge detection (Sobel or Canny)
  const edges = detectEdges(imageData);

  // 2. For each grid block:
  //    - If block contains edge pixels, use lower threshold
  //    - If block is flat color, use higher threshold
  //    - Preserves detail where it matters

  // 3. Process blocks accordingly
}
```

**Reference:**
- [Adaptive Downscaling with Edge Detection](https://hiivelabs.com/blog/gamedev/graphics/2025/01/19/adaptive-downscaling-pixel-art/)

---

### **PHASE 5: User Experience Enhancements**

#### 5.1 Processing Pipeline Visualization
**Purpose:** Show user each step of transformation

**UI:**
- Multi-stage view:
  1. Original
  2. After Background Removal
  3. After Grid Quantization
  4. After Color Reduction
  5. Final Result
- **Toggle to show/hide stages**
- **Edit settings for each stage independently**

---

#### 5.2 Before/After Comparison Slider
**Purpose:** Interactive comparison of results

**Implementation:**
```javascript
// Draggable slider overlay
// Left side: original
// Right side: processed
// Drag to reveal more of either side
```

**Reference:**
- Standard image comparison slider pattern

---

#### 5.3 Preset System
**Purpose:** Save and load processing configurations

**Features:**
- **Save current settings** as named preset
- **Load preset** for quick application
- **Built-in presets:**
  - "Gemini Cleanup (4x4 grid, high threshold)"
  - "16-Color Game Boy Style"
  - "32-Color SNES Style"
  - "Sprite Sheet Batch (maintain palette)"

**Storage:**
- localStorage for browser persistence
- Export/import preset files (.json)

---

#### 5.4 Undo/Redo System
**Purpose:** Allow experimentation without losing work

**Implementation:**
```javascript
class HistoryManager {
  constructor() {
    this.history = [];
    this.currentIndex = -1;
  }

  addState(imageData, settings) {
    // Remove future history if user went back then made change
    this.history = this.history.slice(0, this.currentIndex + 1);

    // Add new state
    this.history.push({ imageData, settings });
    this.currentIndex++;

    // Limit history depth (memory management)
    if (this.history.length > 20) {
      this.history.shift();
      this.currentIndex--;
    }
  }

  undo() { /* ... */ }
  redo() { /* ... */ }
}
```

**UI:**
- Undo/Redo buttons
- Keyboard shortcuts (Ctrl+Z, Ctrl+Y)
- History panel showing previous states

---

#### 5.5 Zoom & Pan
**Purpose:** Inspect results at pixel level

**Features:**
- **Zoom levels:** 100%, 200%, 400%, 800%
- **Pixel grid overlay** at high zoom
- **Pan with mouse drag**
- **Zoom to cursor position**

---

#### 5.6 Export Options
**Purpose:** Flexible output formats

**Options:**
- **File format:** PNG, GIF (for animations)
- **Bit depth:** 24-bit, 8-bit indexed
- **Compression:** None, Best
- **Metadata:** Include palette info
- **Batch export naming:**
  - `sprite_001.png`, `sprite_002.png`, etc.
  - `walk_001.png`, `walk_002.png`, etc.

---

### **PHASE 6: Advanced Features (Future)**

#### 6.1 Animation Preview
**Purpose:** Preview sprite sheet as animation

**Features:**
- Set FPS
- Play/pause/loop
- Onion skinning (show previous/next frames)

---

#### 6.2 Outline/Shadow Generation
**Purpose:** Add consistent outlines to sprites

**Algorithm:**
```javascript
function generateOutline(imageData, outlineColor, thickness) {
  // For each non-transparent pixel:
  //   Check 8 surrounding pixels
  //   If any are transparent, mark as edge
  //   Draw outline pixel in outlineColor

  // Can expand outline by running multiple passes
}
```

---

#### 6.3 Color Palette Shift
**Purpose:** Recolor sprites (e.g., for different characters)

**Features:**
- Hue shift slider
- Saturation/brightness adjustment
- Color replacement (swap specific colors)

---

#### 6.4 AI Enhancement Integration
**Purpose:** Optional AI cleanup before processing

**Potential:**
- Integrate with AI upscaling APIs (if user wants)
- Not a priority - manual tools are more controllable

---

## 🏗️ Technical Architecture

### Single-File HTML Application
- **No external dependencies**
- **Vanilla JavaScript** (ES6+)
- **HTML5 Canvas API** for image processing
- **Modern CSS** with CSS Grid and Flexbox
- **localStorage** for settings persistence

### Code Structure
```
pixel-art-quantizer.html
├── <head>
│   ├── <style> (CSS)
│   └── <meta>
├── <body>
│   ├── UI Components (HTML)
│   └── <script>
│       ├── Image I/O
│       ├── Analysis Tools
│       ├── Background Removal
│       ├── Grid Quantization (existing)
│       ├── Color Quantization (k-means)
│       ├── Palette Management
│       ├── Batch Processing
│       ├── UI Controllers
│       └── Export Functions
```

### Performance Considerations
- **Web Workers** for heavy processing (keep UI responsive)
- **Progressive rendering** for batch operations
- **Canvas pooling** to avoid memory leaks
- **Throttled preview updates** during slider adjustments

---

## 📊 Feature Priority Matrix

### Phase 1 - Must Have (v2.0)
| Feature | Priority | Effort | Value |
|---------|----------|--------|-------|
| Image Analysis | HIGH | Medium | High |
| Resolution Alignment | HIGH | Medium | High |
| Background Removal | HIGH | Medium | Critical |
| K-Means Quantization | HIGH | High | Critical |
| Batch Processing | HIGH | Medium | High |

### Phase 2 - Should Have (v2.5)
| Feature | Priority | Effort | Value |
|---------|----------|--------|-------|
| Palette Import/Export | MEDIUM | Low | High |
| Preset Palettes | MEDIUM | Low | Medium |
| Auto-Crop | MEDIUM | Low | Medium |
| Sprite Sheet Split/Join | MEDIUM | Medium | High |

### Phase 3 - Nice to Have (v3.0)
| Feature | Priority | Effort | Value |
|---------|----------|--------|-------|
| Dithering | LOW | Medium | Medium |
| Edge-Aware Processing | LOW | High | Medium |
| Undo/Redo | MEDIUM | Medium | High |
| Zoom/Pan | LOW | Medium | Medium |
| Animation Preview | LOW | Medium | Low |

---

## 🎨 UI/UX Design

### Layout Concept
```
┌─────────────────────────────────────────────────────────┐
│                 Pixel Art Grid Quantizer                │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  UPLOAD & ANALYSIS                              │   │
│  │  • Upload Image                                 │   │
│  │  • Resolution: 192x256                          │   │
│  │  • Suggested Grid: 4x4                          │   │
│  │  • Suggested Scale: 3x → 1x (192x256 → 64x64)  │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  STEP 1: BACKGROUND REMOVAL                     │   │
│  │  Mode: [Solid Color ▼] Color: [⬜] Tol: 10     │   │
│  │  [Remove Background]                            │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  STEP 2: RESOLUTION ALIGNMENT                   │   │
│  │  Target Size: 64 x 64  Crop: [Center ▼]        │   │
│  │  [Fix Alignment]                                │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  STEP 3: GRID QUANTIZATION                      │   │
│  │  Grid Size: 4  Threshold: [████████░░] 8       │   │
│  │  [Process Grid]                                 │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  STEP 4: COLOR PALETTE                          │   │
│  │  Target Colors: 32  Dither: [None ▼]           │   │
│  │  [Reduce Colors] [Export Palette]               │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  [Process All Steps] [Download Result]                 │
│                                                         │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌────────────────────┐  ┌────────────────────┐        │
│  │    ORIGINAL        │  │    PROCESSED       │        │
│  │                    │  │                    │        │
│  │   [Image Preview]  │  │   [Image Preview]  │        │
│  │                    │  │                    │        │
│  └────────────────────┘  └────────────────────┘        │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### Design Principles
- **Progressive Disclosure** - Show basic options first, advanced in expandable sections
- **Visual Feedback** - Show what each step does with before/after
- **Smart Defaults** - Auto-detect settings, user can override
- **Batch-Friendly** - Easy to apply same settings to multiple images
- **Non-Destructive** - Keep original, allow going back

---

## 📚 Implementation Resources

### Algorithms to Implement
1. **K-Means Clustering** (~300 lines)
2. **Floyd-Steinberg Dithering** (~100 lines)
3. **Chroma Key / Flood Fill** (~150 lines)
4. **Edge Detection (Sobel)** (~100 lines)
5. **Integer Scaling Calculator** (~50 lines)

### Color Science Resources
- RGB to LAB conversion for perceptual color distance
- Delta-E color difference formula
- Gamma correction for accurate color math

### External Tool References (for algorithm ideas)
- **Free Tools:**
  - [Lospec Palette Quantizer](https://lospec.com/palette-quantizer/)
  - [YourImageKit Color Quantizer](https://yourimagekit.com/quantize)
  - [ImaginaryCatLab Downscaler](https://imaginarycatlab.com/free-pixel-art-downscaler.html)
  - [pixeldetector (GitHub)](https://github.com/Astropulse/pixeldetector)
  - [pixel-artify (GitHub)](https://github.com/marcusrprojects/pixel-artify)

- **Research Papers:**
  - [Adaptive Downscaling of Pixel Art](https://hiivelabs.com/blog/gamedev/graphics/2025/01/19/adaptive-downscaling-pixel-art/)
  - [Image Quantization Guide](https://www.numberanalytics.com/blog/art-image-quantization)

### JavaScript Libraries (for reference only - we won't use them)
- image-q (quantization algorithms)
- quantize.js (Leptonica median cut)
- chroma.js (color space conversions)

**Note:** We're implementing from scratch to maintain zero dependencies.

---

## 🧪 Testing Strategy

### Test Cases to Build
1. **Grid Quantization:**
   - 4x4 grid on 64x64 image
   - 8x8 grid on 128x128 image
   - Non-divisible image size (67x67 with 4x4 grid)

2. **Background Removal:**
   - Pure white background
   - Checkerboard pattern (light/dark gray)
   - Gradient background
   - Already transparent image (should not change)

3. **Color Quantization:**
   - High color count (1000+) → 16 colors
   - Already limited palette (should detect and skip)
   - Gradient test (smooth vs banded)

4. **Batch Processing:**
   - 10 frames with different colors
   - Ensure palette consistency across all

5. **Resolution Alignment:**
   - 192x256 → 64x64 (3x downscale)
   - 67x67 → 64x64 (needs crop)
   - 60x60 → 64x64 (needs padding)

### Sample Images Needed
- AI-generated sprite with white background
- AI-generated sprite with checkerboard
- Clean reference sprite (64x64)
- Sprite sheet (8 frames)
- Blurry AI sprite with many similar colors

---

## 🚀 Development Roadmap

### Version 1.0 (Current) ✅
- Basic grid quantization
- Solidity threshold algorithm
- Single image processing
- Download functionality

### Version 2.0 (Next Major Release)
**Target Features:**
- Image analysis on import
- Resolution alignment solver
- Background removal (chroma key + checkerboard)
- K-means color quantization
- Batch processing (basic)

**Estimated Effort:** 3-4 weeks of development

### Version 2.5
**Target Features:**
- Palette import/export
- Preset retro palettes
- Sprite sheet split/join
- Auto-crop to content

**Estimated Effort:** 2 weeks

### Version 3.0
**Target Features:**
- Dithering options
- Undo/Redo
- Zoom/Pan
- Processing pipeline visualization
- Edge-aware processing

**Estimated Effort:** 3 weeks

---

## 🎯 Success Metrics

### Tool Effectiveness
- **Time Savings:** Process sprite in <30 seconds vs 5-10 min in Aseprite
- **Quality:** Consistent colors across sprite sheet (0 manual fixes needed)
- **Workflow:** Single tool vs switching between 3-4 different tools

### User Experience Goals
- **Ease of Use:** New user can process first sprite in <2 minutes
- **Smart Defaults:** 80% of users don't need to change auto-detected settings
- **Batch Efficiency:** Process 10-frame sprite sheet in <1 minute

---

## 📝 Notes & Ideas

### Workflow Integration
This tool is designed to fit into this pipeline:
```
Gemini 3 Pro → This Tool → Game Engine
     ↓              ↓            ↓
  Generate      Clean up      Import
   Images       & Reduce      Sprites
```

### Future Enhancements (v4.0+)
- WebGL acceleration for faster processing
- Export to sprite sheet formats (.aseprite, .pyxel)
- Integration with game engine plugins
- Cloud storage/sharing of presets
- Collaborative editing (multiple users)

### Community Features
- Share presets/palettes with community
- Gallery of before/after results
- Tutorial mode (guided workflow)

---

## 🔧 Technical Debt & Considerations

### Current Code Quality
- Single file = easy to distribute, harder to maintain at scale
- Consider modularization for v3.0+

### Performance Bottlenecks
- K-means can be slow on large images
  - Solution: Downsample for clustering, upsample for application
- Batch processing large sprite sheets
  - Solution: Web Workers + progressive rendering

### Browser Compatibility
- Target modern browsers (Chrome, Firefox, Safari, Edge)
- Minimum: ES6 support, Canvas API, File API
- Optional: Web Workers, IndexedDB (for history)

---

## 📖 Documentation Plan

### User Documentation Needed
1. **Quick Start Guide** - 5-minute tutorial
2. **Feature Guide** - Detailed explanation of each tool
3. **Workflow Examples** - Common use cases
4. **Troubleshooting** - Common issues and solutions
5. **FAQ** - Gemini-specific tips

### Developer Documentation
1. **Architecture Overview** - Code structure
2. **Algorithm Explanations** - How each algorithm works
3. **Adding New Features** - Extension guide
4. **Performance Optimization** - Best practices

---

## 🎉 Conclusion

This project aims to solve a real workflow problem: converting AI-generated images (especially from Gemini 3 Pro) into production-ready pixel art for game development.

**Core Value Proposition:**
- ✅ One tool instead of 5
- ✅ Automatic detection and smart defaults
- ✅ Batch processing for efficiency
- ✅ Consistent color palettes across sprite sheets
- ✅ Free, open-source, works offline

**Next Steps:**
1. Implement Phase 1 features (v2.0)
2. Test with real AI-generated sprites
3. Gather feedback from game developers
4. Iterate based on actual usage patterns

---

*Last Updated: 2025-11-23*
*Version: Planning Document v1.0*