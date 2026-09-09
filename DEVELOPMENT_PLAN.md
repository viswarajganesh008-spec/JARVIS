# JarvisAssistant Development Plan

## 1. Audio Model Requirements
To enable voice functionality, the following models must be placed in `core-voice/src/main/assets/`:

- **VAD (Voice Activity Detection):** 
  - Source: MediaPipe Audio Classifier.
  - File: `.task` or `.tflite`.
- **Speaker Embedding:** 
  - Source: ECAPA-TDNN TFLite (GitHub).
  - Requirements: Follow their `export.py` to get the `.tflite` file.
- **Keyword Spotting:** 
  - Source: TensorFlow Simple Audio Recognition Tutorial.
  - Requirements: Train on custom audio samples, download the final `.tflite`.

### Model Preparation Rules
- **Use Python for Model Prep:** Train/convert models externally (Colab/GPU).
- **Verify Shapes:** Document input/output shapes for each model. The Kotlin `EmbeddingEngine` must strictly match these shapes.

## 2. Technical Roadmap
1. [ ] **Environment Setup:** Ensure Android Studio is configured (API 34).
2. [ ] **Model Integration:** Place models in `core-voice` and verify loading.
3. [ ] **Module Implementation:**
    - `core-auth`: Set up user authentication.
    - `core-data`: Configure local database (Room) or API integration.
    - `core-nlu`: Implement natural language understanding logic.
    - `core-voice`: Integrate VAD, Embedding, and Keyword Spotting engines.
    - `core-actions`: Define executable commands/intent handling.
4. [ ] **UI Integration:** Connect Compose UI in `app` to modules.
5. [ ] **Build & Test:** Verify functional integrity on an Android device/emulator.
