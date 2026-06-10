# Open Generative AI Studio

An open-source, feature-complete generative AI workspace supporting both local offline inference and cloud APIs. It provides dedicated studios for Image Generation, Video Creation, Lip-Syncing, Cinema-style camera controls, and custom visual Workflows.

---

## 🚀 Key Features

* **Image Studio (Dual Mode):**
  * **Text-to-Image:** Support for state-of-the-art models (Flux, Stable Diffusion, etc.).
  * **Image-to-Image / Editing:** Support for image-conditioned generations and multi-image inputs (up to 14 reference images for compatible models).
* **Video Studio (Dual Mode):**
  * Generate high-quality clips from text prompts or animate a start-frame image.
* **Lip Sync Studio:**
  * Animate portrait images or sync lips on existing videos using audio files (with models like LatentSync, LTX Lipsync, and Infinite Talk).
* **Cinema Studio:**
  * Precise virtual camera controls including Lens Type, Focal Length, and Aperture to generate cinematic shots.
* **Workflow Studio:**
  * A node-based visual editor to build, save, and run multi-step AI pipelines.
* **Local Offline Inference:**
  * Run models locally on your GPU/CPU using the bundled C++ engine (`sd.cpp`) or connect to a custom remote Gradio server (`Wan2GP`).

---

## 📦 Project Structure

The project is structured as a Next.js monorepo using `npm` workspaces.

```
Open-Generative-AI/
├── app/                        # Next.js App Router (Web App)
│   ├── layout.js               # Root layout & global styling
│   └── studio/                 # Studio interface shell
├── electron/                   # Electron main & preload scripts (Desktop App)
├── packages/
│   └── studio/                 # Shared core React components & assets
│       ├── src/
│       │   ├── models.js       # Single source of truth for 200+ model schemas
│       │   ├── muapi.js        # Cloud API client wrapper
│       │   └── components/     # Studio views (Image, Video, LipSync, etc.)
├── next.config.mjs             # Next.js bundler configurations
├── tailwind.config.js          # Shared utility styles
└── package.json                # Workspaces setup
```

---

## 🛠️ Getting Started

### Prerequisites
* [Node.js](https://nodejs.org/) (v18 or higher)
* (Optional) An API key from [Muapi.ai](https://muapi.ai) to run cloud models.

### Setup & Installation

1. Clone the repository along with its submodules (required for internal package workspaces):
   ```bash
   git clone --recurse-submodules https://github.com/SOURABREDDY394/higsfield.git
   cd higsfield
   ```
   *If you already cloned without submodules, run:*
   ```bash
   git submodule update --init --recursive
   ```

2. Install dependencies and bootstrap the packages:
   ```bash
   npm run setup
   ```

3. Run the development environment:
   * **Desktop App (Electron + Vite):**
     ```bash
     npm run electron:dev
     ```
   * **Web App (Next.js):**
     ```bash
     npm run dev
     ```

### Production Build

* **Web Application:**
  ```bash
  npm run build
  npm run start
  ```
* **Desktop Application installers:**
  ```bash
  # Build for your active platform:
  npm run electron:build
  
  # Cross-platform builds:
  npm run electron:build:win      # Windows (NSIS)
  npm run electron:build:linux    # Linux (AppImage + DEB)
  ```

---

## ⚡ Local Model Inference (Desktop App Only)

The desktop client integrates support for running model workloads directly on local hardware.

### 1. Bundled C++ Engine (`sd.cpp`)
Uses [stable-diffusion.cpp](https://github.com/leejet/stable-diffusion.cpp) for lightweight offline generation.
* **Compatibility:** CPU (all platforms) and Apple Silicon Metal GPU.
* **Storage Path:** Model weights and binaries are saved under:
  * macOS: `~/Library/Application Support/open-generative-ai/local-ai`
  * Windows: `%APPDATA%\open-generative-ai\local-ai`
  * Linux: `~/.config/open-generative-ai/local-ai`
  * *Override this path by setting the `OPEN_GENERATIVE_AI_LOCAL_AI_DIR` environment variable before launch.*

### 2. Gradio Backend (`Wan2GP`)
Allows offloading heavy video model inference (e.g., Wan 2.2, Hunyuan Video) to a separate GPU machine.
1. Run [Wan2GP](https://github.com/deepbeepmeep/Wan2GP) on the host machine:
   ```bash
   git clone https://github.com/deepbeepmeep/Wan2GP
   cd Wan2GP
   ./install.sh
   python wgp.py --listen --server-name 0.0.0.0
   ```
2. In the desktop application under **Settings → Local Models**, enter your server's URL (e.g., `http://192.168.1.42:7860`).

---

## 🔌 API Integration

Cloud model generation leverages the [Muapi.ai](https://muapi.ai) gateway. The integration operates using a asynchronous two-step pattern:
1. **Submit:** Sends a `POST` request containing generation parameters to the corresponding model endpoint. The API returns a `request_id`.
2. **Poll:** Periodically polls `GET /api/v1/predictions/{request_id}/result` until the processing status resolves to `completed` or `failed`.

Authentication keys are safely persisted in the browser's `localStorage` and sent directly to the API endpoints.

---

## 📄 License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
