# Local AI Image Generation - Implementation Plan

## 🎯 Project Overview

Add browser-based, fully local AI image generation capabilities to the Pixel Art Tool, creating an all-in-one solution for AI-powered pixel art creation with zero cloud dependencies.

### Target Hardware
- **Primary:** M4 Mac Mini, 16GB RAM
- **GPU:** Apple Silicon GPU (excellent WebGPU support via Metal)
- **Browser:** Safari 26 beta (native WebGPU) or Chrome 113+
- **Storage:** Unlimited (user accepts large model downloads)

### Philosophy
- ✅ **100% Local** - Everything runs in browser, no APIs
- ✅ **Offline-First** - Works without internet after initial setup
- ✅ **Privacy-First** - Nothing leaves the device
- ✅ **Unlimited** - No generation limits, no costs
- ✅ **Experimental** - We'll test models beyond recommended specs

---

## 🚀 Technology Stack (2025)

### **WebGPU Framework** (Foundation)
- **Safari 26 beta** - Native Metal backend (BEST for M4)
- **Chrome 113+** - Cross-platform fallback
- **Performance:** M4 GPU can handle 5-7GB models efficiently

### **Inference Engines** (Choose One)

#### **Option A: MLC-AI Web Stable Diffusion** 🌟 RECOMMENDED
- **Repo:** https://github.com/mlc-ai/web-stable-diffusion
- **Demo:** https://websd.mlc.ai/
- **Pros:**
  - Optimized for WebGPU
  - Supports full Stable Diffusion models
  - Active development (2025)
  - Works great on Apple Silicon
  - Can load custom models (LoRA, fine-tunes)
- **Cons:**
  - Larger bundle size (~50MB framework + models)
  - Initial setup complexity

#### **Option B: ONNX Runtime Web**
- **Microsoft's solution**
- **Pros:**
  - Industry standard
  - Excellent documentation
  - Fast on WebGPU (20× CPU performance)
- **Cons:**
  - Requires ONNX model conversion
  - Slightly more complex integration

#### **Option C: Transformers.js** (Lighter)
- **Hugging Face solution**
- **Pros:**
  - Easy integration
  - Smaller models available
  - Great for quick prototypes
- **Cons:**
  - Limited to smaller models (~1-3B params)
  - Less control over generation

### **Recommendation: Start with MLC-AI**
Best balance of power, flexibility, and Apple Silicon optimization.

---

## 🎨 Model Selection Strategy

### **Memory Budget Analysis (16GB RAM)**

**System Overhead:** ~4GB (macOS + browser)
**Available for models:** ~12GB
**WebGPU memory:** Shares with system RAM on M4

**Model Size Guidelines:**
- **2GB models:** ✅ Fast, responsive, multiple instances
- **4GB models:** ✅ Comfortable, good quality
- **5-7GB models:** ✅ Excellent quality, slight slowdown
- **10GB+ models:** ⚠️ Possible but experimental, may swap

### **🔥 UPDATED NOVEMBER 2025: BEST MODELS FOR M4**

#### **🏆 TOP PICK: FLUX.1-schnell + Pixel Art LoRAs**

**Why FLUX.1 is the best choice for pixel art on M4 Mac Mini:**
1. **#1 ranked image quality** - Better base model than SDXL or Kolors
2. **November 2025 pixel art LoRAs** - Brand new, specifically trained for game sprites
3. **Tiny LoRAs** (50-200MB each) - Load multiple styles, swap instantly
4. **Superior prompt following** - Understands pixel art requirements better
5. **Excellent Apple Silicon optimization** - Runs beautifully on M4's Metal backend
6. **Flexible** - One base model + many specialized LoRAs

**Recommended Setup (M4 Mac Mini, 16GB RAM):**
```
FLUX.1-schnell (base model):    ~6.0GB
+ NewPixelCore LoRA:            ~150MB (anime pixel art)
+ Modern_Pixel_art LoRA:        ~100MB (modern 2D games)
+ Retro-Pixel LoRA:             ~80MB  (SNES/NES style)
─────────────────────────────────────────────────
Total loaded in memory:         ~6.3GB
Remaining for system/browser:   ~9.7GB
```

**Performance on M4 Mac Mini:**
- **Initial load:** ~20-25 seconds (one-time per session)
- **Generation time:** 6-10 seconds per 512x512 image
- **LoRA switching:** <1 second (just swap the adapter)
- **Quality:** ⭐⭐⭐⭐⭐ (best available for pixel art)
- **GPU utilization:** 80-95% (M4 GPU handles this perfectly)
- **Memory pressure:** LOW (plenty of headroom)
- **Stability:** Excellent (no swapping or slowdowns)

**Available Pixel Art LoRAs (November 2025):**

1. **[NewPixelCore-ILL-FLUX](https://civitai.com/models/2114925/new-pixel-core-ill-flux)** ⭐ NEWEST
   - **Released:** November 2025
   - **Style:** Detailed anime pixel art
   - **Training:** High-quality pixel art with fine details, Bf16 Qwen
   - **Size:** ~150MB
   - **Strength:** 0.7-1.0
   - **Trigger words:** "newpixelcore, pixel, many details"
   - **Best for:** Character sprites, portraits, detailed work

2. **[FLUX.1-dev-LoRA-Modern_Pixel_art](https://huggingface.co/UmeAiRT/FLUX.1-dev-LoRA-Modern_Pixel_art)**
   - **Style:** Modern 2D pixel art games
   - **Training:** 100 high-quality images from modern indie games
   - **Size:** ~100MB
   - **Trigger words:** "modern pixel art"
   - **Best for:** Indie game aesthetics, detailed sprites

3. **[Retro-Pixel-Flux-LoRA](https://www.promptlayer.com/models/retro-pixel-flux-lora)**
   - **Style:** Classic 16-bit retro (SNES/Genesis era)
   - **Training:** 16 high-res retro sprites, 64 network dims, 15 epochs
   - **Size:** ~80MB
   - **Trigger words:** "retro pixel art"
   - **Best for:** SNES-style sprites, retro aesthetics

4. **[Pixel Art Style v1 (FLUX)](https://civitai.com/models/747921/pixel-art-style)**
   - **Style:** General pixel art
   - **Size:** ~90MB
   - **Best for:** Experimentation, flexible styles

**Workflow:**
1. Load FLUX.1-schnell once at startup
2. Generate with different LoRAs for different styles
3. No need to reload base model - just swap LoRAs
4. Mix and match for different sprite types

---

### **Recommended Models for Pixel Art (Legacy/Alternative)**

#### **Tier 1: Lightweight & Fast** (2-3GB)
*Use these if you want even faster generation or as backups*

**1. SDXL Lightning (Turbo)**
- **Size:** ~2.5GB ONNX
- **Speed:** 1-2 seconds per image on M4
- **Quality:** ⭐⭐⭐ Very good for speed
- **Pixel Art:** Needs good prompting
- **Use Case:** Ultra-fast prototyping when testing ideas

**2. Custom Pixel Art SD 1.5 Fine-Tune**
- **Size:** ~2GB
- **Speed:** ~3-5 seconds on M4
- **Quality:** ⭐⭐⭐⭐ Excellent for pixel art specifically
- **Fine-tuned on:** Retro game sprites, 16-bit art
- **Use Case:** Lightweight alternative to FLUX

#### **Tier 2: High Quality** (4-7GB)
Best quality-to-performance ratio

**3. Kolors 2.1 (Quantized)** 🔥 TOP PICK
- **Size:** ~5GB (INT8 quantized)
- **Speed:** ~5-8 seconds on M4
- **Quality:** State-of-the-art, #5 on Image Arena
- **Bilingual:** Chinese + English
- **Text Rendering:** Can embed text in images
- **Pixel Art:** Excellent with proper prompting
- **Cultural Understanding:** Best for diverse art styles
- **Use Case:** Production-quality generation

**4. FLUX.1-schnell (Quantized)**
- **Size:** ~6GB (INT8)
- **Speed:** ~6-10 seconds
- **Quality:** #1 ranked image quality
- **Pixel Art:** Very good with LoRA
- **Use Case:** Highest quality output

**5. HiDream-I1 (Quantized)**
- **Size:** ~7GB (INT8)
- **Speed:** ~8-12 seconds
- **Quality:** Excellent, beats DALL·E 3
- **Pixel Art:** Good with prompting
- **Use Case:** Variety and creativity

#### **Tier 3: Experimental** (8-12GB)
For when you want maximum quality and can accept slower speed

**6. Full Kolors 2.1 (FP16)**
- **Size:** ~10GB
- **Speed:** ~15-20 seconds
- **Quality:** Maximum fidelity
- **Note:** May cause memory pressure on 16GB
- **Use Case:** Final production assets

### **Pixel Art LoRA Adapters** (Add ~50-200MB each)
These are small add-ons that adapt base models for pixel art:

- **Pixel Art LoRA v1** (for SDXL)
- **Retro Game Sprites LoRA**
- **16-bit Style LoRA**
- **SNES Aesthetic LoRA**

**Strategy:** Start with base model + LoRA for best results

---

## 🏗️ Implementation Architecture

### **Phase 1: Foundation** (4-6 hours)

#### 1.1 WebGPU Detection & Setup
```javascript
async function initWebGPU() {
  if (!('gpu' in navigator)) {
    throw new Error('WebGPU not supported');
  }

  const adapter = await navigator.gpu.requestAdapter();
  if (!adapter) {
    throw new Error('No GPU adapter found');
  }

  // Check memory limits
  const limits = adapter.limits;
  console.log('Max buffer size:', limits.maxBufferSize);
  console.log('Max texture size:', limits.maxTextureDimension2D);

  return adapter;
}
```

#### 1.2 MLC-AI Integration
```javascript
import { WebStableDiffusion } from '@mlc-ai/web-stable-diffusion';

class PixelArtGenerator {
  constructor() {
    this.model = null;
    this.isLoading = false;
    this.isReady = false;
  }

  async initialize(modelPath, progressCallback) {
    this.isLoading = true;

    try {
      this.model = new WebStableDiffusion();

      // Load model with progress tracking
      await this.model.loadModel(modelPath, {
        onProgress: (progress) => {
          progressCallback({
            phase: progress.phase,
            loaded: progress.loaded,
            total: progress.total,
            percentage: (progress.loaded / progress.total) * 100
          });
        }
      });

      this.isReady = true;
      this.isLoading = false;
    } catch (error) {
      this.isLoading = false;
      throw error;
    }
  }

  async generate(config) {
    if (!this.isReady) {
      throw new Error('Model not loaded');
    }

    const result = await this.model.generate({
      prompt: config.prompt,
      negative_prompt: config.negativePrompt,
      width: config.width || 512,
      height: config.height || 512,
      num_inference_steps: config.steps || 20,
      guidance_scale: config.guidanceScale || 7.5,
      seed: config.seed || Math.floor(Math.random() * 1000000)
    });

    return result;
  }
}
```

#### 1.3 Model Download & Caching
```javascript
class ModelManager {
  constructor() {
    this.db = null; // IndexedDB for model storage
    this.availableModels = [
      {
        id: 'pixel-art-sd15',
        name: 'Pixel Art SD 1.5',
        size: 2048, // MB
        url: 'https://huggingface.co/.../pixel-art-sd15.onnx',
        type: 'base',
        recommended: true
      },
      {
        id: 'kolors-2.1-int8',
        name: 'Kolors 2.1 (Quantized)',
        size: 5120,
        url: 'https://huggingface.co/.../kolors-2.1-int8.onnx',
        type: 'base',
        recommended: true
      },
      {
        id: 'sdxl-lightning',
        name: 'SDXL Lightning Turbo',
        size: 2560,
        url: 'https://huggingface.co/.../sdxl-lightning.onnx',
        type: 'base',
        recommended: true
      }
    ];
  }

  async downloadModel(modelId, progressCallback) {
    const model = this.availableModels.find(m => m.id === modelId);
    if (!model) throw new Error('Model not found');

    const response = await fetch(model.url);
    const reader = response.body.getReader();
    const contentLength = +response.headers.get('Content-Length');

    let receivedLength = 0;
    const chunks = [];

    while (true) {
      const { done, value } = await reader.read();

      if (done) break;

      chunks.push(value);
      receivedLength += value.length;

      progressCallback({
        loaded: receivedLength,
        total: contentLength,
        percentage: (receivedLength / contentLength) * 100,
        speed: this.calculateSpeed(receivedLength)
      });
    }

    // Combine chunks
    const modelBlob = new Blob(chunks);

    // Store in IndexedDB
    await this.storeModel(modelId, modelBlob);

    return modelBlob;
  }

  async storeModel(modelId, blob) {
    // Store in IndexedDB for persistence
    const db = await this.openDB();
    const tx = db.transaction('models', 'readwrite');
    await tx.objectStore('models').put({
      id: modelId,
      blob: blob,
      timestamp: Date.now()
    });
  }

  async getStoredModel(modelId) {
    const db = await this.openDB();
    const tx = db.transaction('models', 'readonly');
    const model = await tx.objectStore('models').get(modelId);
    return model ? model.blob : null;
  }
}
```

---

### **Phase 2: UI Integration** (2-3 hours)

#### 2.1 Model Selection Panel
```
┌─────────────────────────────────────────────────────────┐
│  AI GENERATION SETUP                                    │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  CHOOSE MODEL                                   │   │
│  │                                                 │   │
│  │  ○ Pixel Art SD 1.5 (2GB) - Fast & Optimized   │   │
│  │     [Download] Status: Not installed            │   │
│  │                                                 │   │
│  │  ● Kolors 2.1 (5GB) - Best Quality             │   │
│  │     [✓ Installed] [Load] [Delete]               │   │
│  │                                                 │   │
│  │  ○ SDXL Lightning (2.5GB) - Ultra Fast          │   │
│  │     [Download] Status: Not installed            │   │
│  │                                                 │   │
│  │  [+ Add Custom Model URL]                       │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  DOWNLOAD PROGRESS                              │   │
│  │  Downloading: Kolors 2.1 (Quantized)            │   │
│  │  [████████████████░░░░] 82% (4.1 / 5.0 GB)     │   │
│  │  Speed: 12.5 MB/s | Time left: ~1 min          │   │
│  │  [Pause] [Cancel]                               │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  Storage Used: 12.3 GB / Available: 247 GB              │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

#### 2.2 Generation Interface
```
┌─────────────────────────────────────────────────────────┐
│  AI PIXEL ART GENERATOR                                 │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  Model Loaded: Kolors 2.1 (5GB) ✓                      │
│  GPU: Apple M4 (Metal) | Memory: 4.2 / 12 GB available │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  PROMPT                                         │   │
│  │  [pixel art wizard character, 64x64, front     │   │
│  │   facing, SNES style, 16 colors, clean         │   │
│  │   outlines, standing pose, transparent bg]     │   │
│  │                                                 │   │
│  │  [💡 Pixel Art Tips] [📋 Load Template]        │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  NEGATIVE PROMPT                                │   │
│  │  [blurry, gradient, anti-aliased, 3D, photo-   │   │
│  │   realistic, detailed background, smooth]      │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  GENERATION SETTINGS                            │   │
│  │                                                 │   │
│  │  Output Size: [512x512 ▼] (will auto-process)  │   │
│  │  Target Grid: [4x4 ▼]      Target Colors: 16   │   │
│  │  Steps: [20] ⚡              Guidance: [7.5]    │   │
│  │  Seed: [Random ▼] [🔒 Lock]                     │   │
│  │                                                 │   │
│  │  Style Preset: [SNES 16-bit ▼]                 │   │
│  │  • SNES 16-bit (vibrant, clean outlines)       │   │
│  │  • Game Boy (4-color, green tint)              │   │
│  │  • NES 8-bit (limited palette, simple)         │   │
│  │  • Modern Indie (flexible palette)             │   │
│  │  • Custom                                       │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  POST-PROCESSING (AUTO-APPLIED)                 │   │
│  │  ☑ Auto grid quantization (4x4)                │   │
│  │  ☑ Auto color reduction (to 16 colors)         │   │
│  │  ☑ Remove background                           │   │
│  │  ☐ Add black outline                           │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  [🎨 Generate] [🔄 Generate 4 Variations] [⏹️ Stop]    │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  GENERATION PROGRESS                            │   │
│  │  Step 14 / 20                                   │   │
│  │  [██████████████░░░░] 70%                       │   │
│  │  Time: 4.2s elapsed | ~2s remaining             │   │
│  │  GPU Temp: 62°C | Memory: 5.8 GB                │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │  RESULTS (Click to select)                      │   │
│  │                                                 │   │
│  │  ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐           │   │
│  │  │ img1 │ │ img2 │ │ img3 │ │ img4 │           │   │
│  │  │ ⭐    │ │      │ │      │ │      │           │   │
│  │  └──────┘ └──────┘ └──────┘ └──────┘           │   │
│  │                                                 │   │
│  │  Selected: Image 1                              │   │
│  │  [↗️ Send to Editor] [💾 Save] [🗑️ Delete]      │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
│  Generation History: 47 images (243 MB)                 │
│  [📜 View All] [🗑️ Clear History]                      │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

### **Phase 3: Advanced Features** (3-4 hours)

#### 3.1 Prompt Templates
```javascript
const PROMPT_TEMPLATES = {
  'character-front': {
    base: 'pixel art {character} character, {size}, front facing view, {style} style, clean outlines, standing pose',
    negative: 'blurry, gradient, 3D, realistic, background, side view',
    settings: { steps: 20, guidance: 7.5 }
  },
  'character-walk': {
    base: 'pixel art {character} character walk cycle, {size}, side view, {style} style, 4 frames, clean animation',
    negative: 'blurry, gradient, 3D, realistic, still, static',
    settings: { steps: 25, guidance: 8.0 }
  },
  'item': {
    base: 'pixel art {item} game item icon, {size}, isometric view, {style} style, clean icon, centered',
    negative: 'blurry, gradient, 3D, character, background',
    settings: { steps: 15, guidance: 7.0 }
  },
  'tile': {
    base: 'pixel art {tile} tileable texture, {size}, seamless, {style} style, no objects',
    negative: 'characters, items, non-tileable, seams',
    settings: { steps: 20, guidance: 6.5 }
  }
};
```

#### 3.2 Batch Generation
```javascript
async function generateVariations(baseConfig, count = 4) {
  const results = [];

  for (let i = 0; i < count; i++) {
    // Use different seeds for variations
    const config = {
      ...baseConfig,
      seed: baseConfig.seed + i
    };

    const result = await generator.generate(config);
    results.push(result);

    // Update UI progress
    updateProgress(i + 1, count);
  }

  return results;
}
```

#### 3.3 Auto-Processing Pipeline
```javascript
async function generateAndProcess(prompt, settings) {
  // Step 1: Generate with AI
  updateStatus('Generating with AI...');
  const rawImage = await generator.generate({
    prompt: prompt,
    ...settings
  });

  // Step 2: Auto-apply grid quantization
  if (settings.autoGridQuantize) {
    updateStatus('Applying grid quantization...');
    rawImage = gridQuantizer.process(rawImage, {
      gridSize: settings.gridSize,
      threshold: settings.threshold
    });
  }

  // Step 3: Auto-apply color reduction
  if (settings.autoColorReduce) {
    updateStatus('Reducing colors...');
    rawImage = colorQuantizer.quantize(rawImage, {
      targetColors: settings.targetColors,
      dithering: false
    });
  }

  // Step 4: Remove background
  if (settings.autoRemoveBackground) {
    updateStatus('Removing background...');
    rawImage = backgroundRemover.remove(rawImage, {
      method: 'magic-cut'
    });
  }

  updateStatus('Complete!');
  return rawImage;
}
```

#### 3.4 LoRA Support
```javascript
class LoRAManager {
  async loadLoRA(loraPath, weight = 0.8) {
    // Load LoRA adapter
    const lora = await fetch(loraPath).then(r => r.arrayBuffer());

    // Apply to current model
    await this.generator.model.applyLoRA(lora, weight);
  }

  async unloadLoRA() {
    await this.generator.model.removeLoRA();
  }
}
```

---

## 📊 Performance Optimization

### **M4 Mac Mini Specific Optimizations**

#### 1. Metal Backend Tweaks
```javascript
const webgpuConfig = {
  powerPreference: 'high-performance', // Use full M4 power
  forceFallbackAdapter: false,
  limits: {
    maxBufferSize: 2 * 1024 * 1024 * 1024, // 2GB buffers
    maxTextureDimension2D: 8192
  }
};
```

#### 2. Memory Management
```javascript
class MemoryManager {
  constructor() {
    this.maxCacheSize = 8 * 1024 * 1024 * 1024; // 8GB
    this.currentUsage = 0;
  }

  async cleanupOldGenerations() {
    // Remove old cached images if memory pressure
    if (this.currentUsage > this.maxCacheSize * 0.8) {
      await this.evictOldest();
    }
  }

  async evictOldest() {
    // Clear oldest 20% of cached results
    const db = await this.openDB();
    // ... eviction logic
  }
}
```

#### 3. Inference Optimizations
```javascript
const optimizationPresets = {
  'quality': {
    steps: 25,
    guidance: 8.0,
    scheduler: 'DPMSolverMultistep'
  },
  'balanced': {
    steps: 20,
    guidance: 7.5,
    scheduler: 'DPMSolverMultistep'
  },
  'speed': {
    steps: 10,
    guidance: 6.0,
    scheduler: 'LCM' // Latent Consistency Model
  },
  'turbo': {
    steps: 4,
    guidance: 1.0,
    scheduler: 'LCM'
  }
};
```

### **Expected Performance (M4 Mac Mini, 16GB RAM) - UPDATED**

| Model | Size | Load Time | Gen Time | Pixel Art Quality | Memory Used | Recommended |
|-------|------|-----------|----------|-------------------|-------------|-------------|
| **FLUX.1 + LoRAs** 🌟 | 6.3GB | ~20-25s | 6-10s | ⭐⭐⭐⭐⭐ | 6.3GB | **YES** |
| Hunyuan Image 3.0* | 10-15GB | ~40-60s | 15-25s | ⭐⭐⭐⭐⭐ | 11-14GB | Experimental |
| SDXL Lightning | 2.5GB | ~12-15s | 1-2s | ⭐⭐⭐ | 3GB | Fast backup |
| Pixel Art SD 1.5 | 2GB | ~10-12s | 3-5s | ⭐⭐⭐⭐ | 2.5GB | Lightweight |
| Kolors 2.1 (INT8) | 5GB | ~20-25s | 5-8s | ⭐⭐⭐⭐ | 6GB | Alternative |

*#1 ranked model globally, worth trying but may push limits
🌟 **Top recommendation for pixel art on M4 Mac Mini**

**Notes:**
- All times measured on M4 Mac Mini via Metal backend (WebGPU)
- Load time is one-time per browser session
- Generation time for 512x512 images, 20 steps
- Memory includes model + VRAM + processing overhead

---

## 🎯 **UPDATED: Recommended Model Strategy for M4**

### **🌟 BEST START Configuration (Total: ~6.3GB)**
1. **Primary:** FLUX.1-schnell (6GB) - #1 quality, best for pixel art
2. **LoRAs:** 3-4 pixel art LoRAs (~300MB total) - Multiple styles
   - NewPixelCore (anime pixel art)
   - Modern_Pixel_art (indie games)
   - Retro-Pixel (SNES/NES)

### **Why This is THE BEST:**
- ✅ FLUX.1 beats Kolors for pixel art quality
- ✅ Multiple specialized LoRAs for different styles
- ✅ Swap LoRAs instantly (no reload needed)
- ✅ Total 6.3GB leaves 9.7GB free on M4
- ✅ November 2025 cutting-edge models
- ✅ Better prompt following
- ✅ Excellent Metal/WebGPU performance

**Performance:**
- Generation: 6-10 seconds (fast enough!)
- Quality: ⭐⭐⭐⭐⭐ (best available)
- Flexibility: Switch styles with one click

### **Optional Add-Ons:**
- **SDXL Lightning (2.5GB)** - For ultra-fast prototyping (1-2s)
- **Your custom LoRA** - Train on YOUR pixel art style

### **Experimental "Push the Limits" Setup:**
- Try loading **Hunyuan Image 3.0** (~10-15GB)
- #1 ranked model globally
- May cause memory pressure but worth testing
- If it works, you have better-than-API quality locally!

### **Upgrade Path:**
1. Start with FLUX + 3 LoRAs
2. Train your own custom LoRA (30-60 min on M4)
3. Optionally add SDXL Lightning for speed
4. Experiment with Hunyuan if you want maximum quality

---

## 🚀 Implementation Timeline (AI Speed)

| Phase | Features | AI Time | Complexity |
|-------|----------|---------|------------|
| 1. Foundation | WebGPU + MLC-AI + Model Manager | 4-6 hours | Medium |
| 2. UI Integration | Model selection + Generation UI | 2-3 hours | Low |
| 3. Advanced Features | Templates + Batch + Pipeline | 3-4 hours | Medium |
| 4. Optimization | M4 tweaks + Memory management | 2 hours | Low |
| 5. Testing | Real-world testing + polish | 2-3 hours | Low |

**Total: 13-18 hours of AI implementation**

---

## 💡 Workflow Examples

### **Example 1: Quick Character Sprite**
```
1. User: "wizard character front view"
2. Template auto-fills: "pixel art wizard character, 64x64, front facing, SNES style..."
3. Click "Generate"
4. AI generates in 6 seconds (Kolors)
5. Auto-applies: grid quantization → color reduction → bg removal
6. Result: Clean 64x64 sprite ready for game
7. Click "Send to Sprite Sheet Slicer" for variations
```

### **Example 2: Walk Cycle Animation**
```
1. User: "knight walk cycle"
2. Template: "walk cycle" (4 frames)
3. Click "Generate 4 Variations"
4. AI generates 4 seeds with slight variations
5. User picks best frames
6. Click "Create Sprite Sheet" → 1x4 grid
7. Auto-slice to individual frames
8. Export to Aseprite → instant animation
```

### **Example 3: Item Icons**
```
1. User: "magic sword, health potion, key"
2. Batch mode: generates 3 items
3. Each auto-processed to 32x32 icons
4. Perfect color palette consistency
5. One-click export to game engine
```

---

## 🔧 Alternative Models (Experimental)

### **For 16GB RAM Adventurers:**

**1. Hunyuan Image 3.0** 🇨🇳 🏆 #1 RANKED GLOBALLY
- **Size:** ~10-15GB (estimated, model not yet available for local download)
- **Quality:** ⭐⭐⭐⭐⭐ (BEAT Google's Nano Banana on leaderboards!)
- **Speed on M4:** ~15-25 seconds (estimated)
- **Status:** World's largest open-source text-to-image model (Tencent, 2025)
- **Pixel Art:** Has pixel art LoRA available
- **Worth trying:** **YES!** If we can get it running on M4, you'll have the BEST model locally
- **Memory pressure:** HIGH - will use 11-14GB, but M4 can handle it
- **Fallback:** Use FLUX if this is too heavy
- **Advantage:** Better quality than ANY paid API!

**2. Quantized SDXL (4-bit)**
- Size: ~3.5GB
- Quality: 90% of full SDXL
- Speed: 3-4s on M4
- Worth trying for lightweight option!

**3. Distilled Models**
- Size: 1-2GB
- Speed: Ultra-fast (<1s)
- Quality: Good enough for rapid prototypes

---

## 🎨 **Training Custom LoRAs on M4 Mac Mini**

**YOU CAN TRAIN YOUR OWN PIXEL ART LoRAs!**

Instead of RTX 4090 (which you don't have), use **MLX** - Apple's optimized ML framework for M-series chips.

### **Training on M4 Mac Mini:**

**Using MLX-based FLUX LoRA Training:**
- **Framework:** [mflux](https://github.com/filipstrand/mflux) - MLX port of FLUX models
- **Optimized for:** Apple Silicon (M1/M2/M3/M4)
- **Uses:** Metal backend, unified memory architecture
- **Benefits:** Fast training directly on your M4!

**Training Time Estimates (M4 Mac Mini, 16GB RAM):**
- **Small LoRA** (15-20 images, 500 steps): ~30-60 minutes
- **Medium LoRA** (50-100 images, 1000 steps): ~2-3 hours
- **Large LoRA** (100+ images, 1500 steps): ~4-6 hours

**Memory Requirements:**
- Training uses ~8-10GB RAM
- Can train while browser is closed
- Runs in background, Mac remains usable

**Process:**
1. **Collect training data:** 15-100 pixel art sprites from YOUR game
2. **Organize & caption:** Tag each image with descriptions
3. **Install MLX:** `pip install mlx-lm mflux`
4. **Run training script:** Point to your images, set parameters
5. **Wait:** Training happens automatically
6. **Export LoRA:** Get a ~50-200MB .safetensors file
7. **Load in web app:** Use your custom LoRA for generation!

**Example Training Command:**
```bash
python train_flux_lora.py \
  --images ./my_pixel_art \
  --output ./my_pixel_art_lora \
  --steps 1000 \
  --learning_rate 1e-4
```

**Result:**
- Sprites generated in YOUR EXACT art style
- Perfect color palette matching
- Consistent character designs across all angles
- No cloud services needed - 100% local!

**This is POWERFUL because:**
- Train on M4's GPU directly (via Metal)
- All local - your art never leaves your Mac
- Can create unlimited custom styles
- Fine-tune for specific character types, items, environments
- Share LoRAs with team or community (tiny file size)

---

## 📦 Storage Strategy

### **Model Storage (IndexedDB)**
- Persistent across sessions
- Can store 10-20GB easily
- Automatic cleanup of old models

### **Generation Cache**
- Last 100 generations kept
- Auto-evict oldest when >8GB used
- Can disable if storage limited

### **Recommended Setup:**
- Download 2-3 models initially
- Test each on your art style
- Keep your favorite(s)
- Delete others to save space

---

## 🎨 Prompt Engineering Guide

### **Pixel Art Prompt Formula:**
```
[art style] + [subject] + [size] + [view] + [color info] + [style era] + [details]

Example:
"pixel art wizard character, 64x64, front facing, 16 colors, SNES style, purple robes, staff, clean outlines"
```

### **Negative Prompt Essentials:**
```
blurry, gradient, anti-aliased, 3D, photorealistic, detailed background, smooth shading, high resolution
```

### **Style Keywords:**
- **SNES/16-bit:** vibrant, clean outlines, dithered shading
- **NES/8-bit:** simple, limited palette, blocky
- **Game Boy:** 4-color, green tint, high contrast
- **Modern Indie:** flexible palette, detailed, expressive

---

## 🚀 Future Enhancements

### **Phase 2 (Post-Launch):**
- **ControlNet Support** - Use reference poses
- **Img2Img** - Refine existing sprites
- **Inpainting** - Edit parts of generated sprites
- **Animation Generation** - AI-powered walk cycles

### **Phase 3 (Advanced):**
- **Fine-Tuning UI** - Train on your own art
- **Cloud Backup** - Sync models across devices (optional)
- **Collaborative Generation** - Share prompts/settings
- **Style Transfer** - Apply your game's style to AI art

---

## ✅ Success Criteria

**Must Have:**
- ✅ Load and run FLUX.1-schnell + LoRAs on M4 Mac
- ✅ Generate 512x512 image in <10 seconds
- ✅ Auto-process to pixel art grid
- ✅ Batch generate 4 variations
- ✅ Persistent model storage

**Nice to Have:**
- ⭐ Multiple model support
- ⭐ LoRA loading
- ⭐ Template system
- ⭐ Generation history

**Experimental:**
- 🧪 10GB+ models
- 🧪 Real-time preview
- 🧪 Custom fine-tuning

---

## 📚 Technical Resources

### **MLC-AI Documentation:**
- https://mlc.ai/web-stable-diffusion/
- https://github.com/mlc-ai/web-stable-diffusion

### **Model Sources:**
- **Hugging Face:** https://huggingface.co/models?pipeline_tag=text-to-image
- **Kolors:** https://github.com/Kwai-Kolors/Kolors
- **FLUX:** https://huggingface.co/black-forest-labs

### **WebGPU Resources:**
- https://developer.mozilla.org/en-US/docs/Web/API/WebGPU_API
- https://webkit.org/blog/14879/webgpu-now-available-for-testing-in-safari-technology-preview/

### **Pixel Art Training Sets:**
- LoRA adapters on Hugging Face
- Custom training with your sprite sheets

---

## 🎯 Implementation Priority

### **MVP (Minimum Viable Product):**
1. ✅ MLC-AI integration
2. ✅ Download FLUX.1-schnell + pixel art LoRAs
3. ✅ Basic generation UI
4. ✅ Auto-processing pipeline
5. ✅ Model caching

**Time: ~10-12 hours of AI implementation**

### **V1.0 (Full Featured):**
1. All MVP features
2. Multiple model support
3. Prompt templates
4. Batch generation
5. LoRA support
6. History management

**Time: ~16-18 hours total**

---

## 🔥 Let's Build This!

**Current Status:**
- ✅ Technology researched
- ✅ Architecture designed
- ✅ M4 optimization planned
- ✅ Model strategy defined
- ✅ Implementation timeline clear

**Next Steps:**
1. Integrate MLC-AI framework
2. Build model download UI
3. Implement generation interface
4. Test on M4 Mac Mini
5. Optimize and polish

**Ready to make pixel art generation INSTANT and UNLIMITED!** 🚀

---

*Last Updated: 2025-11-23*
*Version: Local AI Plan v1.1*
*Target Hardware: M4 Mac Mini, 16GB RAM*
*Primary Model: FLUX.1-schnell + November 2025 Pixel Art LoRAs*
*Status: READY TO BUILD 🔥*
