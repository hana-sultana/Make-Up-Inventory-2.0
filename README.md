# ✨ Glam Vault — Full Stack Setup

## What's inside
- `app.py`           — Python Flask backend with real ML
- `templates/index.html` — Full frontend (served by Flask)
- `requirements.txt` — Python dependencies

## ML Stack
| Library       | What it does                                      |
|---------------|---------------------------------------------------|
| MediaPipe     | 468-point facial landmark detection               |
| OpenCV        | Image processing, color region sampling           |
| scikit-learn  | KMeans clustering for dominant color extraction   |
| Pillow        | Image decoding and format conversion              |
| Claude API    | Generates narratives, looks, product recs         |

---

## Setup (3 steps)

### 1. Install Python dependencies
```bash
pip install -r requirements.txt
```

### 2. Set your Anthropic API key
Option A — environment variable (recommended):
```bash
export ANTHROPIC_API_KEY=sk-ant-your-key-here
```

Option B — edit line 20 of app.py:
```python
ANTHROPIC_API_KEY = "sk-ant-your-key-here"
```

Get your API key at: https://console.anthropic.com

### 3. Run the app
```bash
python app.py
```

Then open **http://localhost:5000** in your browser. That's it!

---

## How Face Analysis Works

1. **You upload a selfie** (or use your webcam)
2. **MediaPipe Face Mesh** detects 468 facial landmarks in milliseconds
3. **Geometric algorithms** compute:
   - Face shape (height/width ratio, jaw ratio, forehead ratio)
   - Eye shape (tilt angle, height/width ratio, lid detection)
   - Lip shape (width/height ratio, cupid's bow detection)
   - Cheekbone prominence (cheek vs jaw width ratio)
   - Brow arch angle
4. **OpenCV color sampling** extracts skin tone from cheek regions
5. **scikit-learn KMeans** clusters pixel colors to find your undertone
6. **Claude AI** receives all measurements and generates:
   - Personalized narrative about your features
   - 6 curated makeup looks with step-by-step guidance
   - Pinterest links for each look

---

## API Endpoints

| Method | Route                  | Description                        |
|--------|------------------------|------------------------------------|
| GET    | `/`                    | Serves the frontend                |
| GET    | `/api/health`          | Backend health check               |
| POST   | `/api/analyze-face`    | ML face analysis + AI look gen     |
| POST   | `/api/identify-product`| Claude Vision product identification|
| POST   | `/api/recommendations` | AI product recommendations         |

---

## Troubleshooting

**"No face detected"** — Make sure the photo is well-lit, front-facing, and the face is clearly visible. Avoid heavy filters.

**"Backend Offline" badge** — The Flask server isn't running. Run `python app.py` first.

**ModuleNotFoundError** — Run `pip install -r requirements.txt` again.

**mediapipe install issues on Mac M1/M2** — Try:
```bash
pip install mediapipe-silicon
```

**Port already in use** — Change port in app.py last line: `app.run(port=5001)`
Then update `const API = 'http://localhost:5001'` in templates/index.html line ~4.
