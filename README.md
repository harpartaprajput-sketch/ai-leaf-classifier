# ai-leaf-classifier

Leaf classification using two CNN models: an older MobileNetV2 model (INT8) running on an ESP32-CAM over USB, and a newer CNN model served through a secured API.

## Models

| File | Architecture | Notebook | Deployment |
|------|-------------|----------|------------|
| `leaf_classifier_full_int8.tflite` | MobileNetV2 (older, INT8) | `CNN (1) (2).ipynb` | ESP32-CAM over USB |
| `leaf_classifier__3_.tflite` | CNN (newer, float) | `CN1.ipynb` | Secured API |

## Repository Contents

| File | Description |
|------|-------------|
| `CNN (1) (2).ipynb` | MobileNetV2 training and INT8 export notebook |
| `CN1.ipynb` | CNN training and export notebook |
| `leaf_classifier_full_int8.tflite` | MobileNetV2 INT8 model used on ESP32-CAM |
| `leaf_classifier__3_.tflite` | CNN float model used by the API |
| `api/server.py` | Flask API that serves the CNN model |
| `api/.env` | Holds the API key (not committed) |

## Model Details

### MobileNetV2 INT8 (ESP32-CAM)
- **Input size:** [e.g., 96x96x3]
- **Quantization:** Full INT8
- **Classes:** [list leaf classes]
- **Connection:** USB from laptop to ESP32-CAM
- **Status:** Deployed and working

### CNN (API)
- **Input size:** [e.g., 224x224x3]
- **Preprocessing:** [copy from CN1.ipynb, e.g., /255]
- **Classes:** [list leaf classes]
- **Test accuracy:** [value]

## ESP32-CAM Setup (USB)
1. Open the ESP32-CAM sketch in the Arduino IDE.
2. Select the board and COM port under **Tools**.
3. Click **Upload**.
4. Connect the ESP32-CAM to the laptop by USB and open **Serial Monitor** at [baud rate].

The ESP32-CAM uses the INT8 model directly and does not call the API.

## API Setup (CNN Model)

### 1. Install dependencies
```bash
pip install tensorflow flask numpy pillow
```

### 2. Generate an API key
```bash
python -c "import secrets; print(secrets.token_hex(32))"
```

### 3. Set the key
Windows (PowerShell):
```powershell
$env:LEAF_API_KEY = "your_generated_key"
```
Linux/macOS:
```bash
export LEAF_API_KEY="your_generated_key"
```

### 4. Start the server
```bash
cd api
python server.py
```

### 5. Send a request
```bash
curl -X POST http://localhost:5000/predict \
  -H "X-API-Key: your_generated_key" \
  -F "image=@leaf.jpg"
```

Example response:
```json
{"label": "[class name]", "confidence": 0.93}
```

Requests without a valid key receive `401 Unauthorized`.

## Security Notes
- Never commit the API key. Keep `api/.env` and any key files in `.gitignore`.
- Rotate the key if it is ever shared or pushed to GitHub.
- If a key was ever committed, rotate it, because it stays in the Git history.

## Changelog
- [date]: Initial commit with CNN training notebooks
- [date]: MobileNetV2 INT8 model deployed on ESP32-CAM over USB
- [date]: CNN model API with key authentication added
