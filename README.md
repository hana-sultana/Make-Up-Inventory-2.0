# ✨ Glam Vault — Your Makeup Inventory

A full-stack makeup inventory app powered by computational facial mapping models and real ML libraries.

---

## How the Technology Works

### Facial Mapping with MediaPipe

When you upload a selfie, the app uses **MediaPipe Face Mesh** — a computational model developed by Google — to detect and map **468 precise landmark points** across your face in real time. These landmarks cover every major facial region:

- **Eyes** — 32 points per eye mapping the eyelid contours, inner and outer corners, upper and lower lash lines, and the iris boundary. Used to classify eye shape (almond, hooded, round, monolid, upturned, downturned).
- **Mouth & Lips** — 40 points mapping the outer lip boundary, cupid's bow, lip corners, and philtrum. Used to classify lip shape (full, thin, cupid's bow, wide, heart-shaped).
- **Nose** — landmark points across the nose tip, bridge, and nostrils.
- **Eyebrows** — 10 points per brow mapping the inner start, arch peak, and outer tail. Used to classify brow shape (high arched, softly arched, straight, flat).
- **Face Oval & Jaw** — points tracing the full face contour from hairline to chin. Used to compute face shape geometry.
- **Cheekbones** — landmark points across both cheekbones used to measure cheekbone prominence.

---

### Geometric Analysis (Face Shape Classification)

Once the 468 landmarks are detected, custom geometric algorithms compute precise measurements:

- **Face height** — distance from forehead (point 10) to chin tip (point 152)
- **Face width** — distance between cheekbone landmarks (93 → 361)
- **Jaw width** — distance between lower jaw landmarks (172 → 397)
- **Forehead width** — distance between brow landmarks (70 → 300)
- **Height-to-width ratio** — used to distinguish oval vs oblong vs round
- **Jaw ratio** — jaw width relative to face width, used to identify square vs heart
- **Forehead ratio** — used to identify heart vs diamond shapes

These measurements are compared against classification thresholds to determine your face shape: **oval, round, square, heart, oblong, or diamond**.

Eye shape is determined by computing:
- Eye width (inner corner → outer corner)
- Eye height (upper lid → lower lid)
- Tilt angle (whether the outer corner sits higher or lower than the inner corner)
- Lid overhang (whether the upper lid obscures the crease — indicating hooded eyes)

Lip shape is determined by:
- Lip width vs lip height ratio
- Cupid's bow peak displacement — measuring whether the upper lip peaks create a defined bow shape
- Lip width relative to total face width

---

### Skin Undertone Detection with OpenCV

**OpenCV** is used to sample the actual pixel colors from your cheek regions — specifically the landmarks that map to your left and right cheekbones. The sampled pixels are converted from RGB to the **HSV (Hue, Saturation, Value)** color space, and then analyzed:

- **Warm undertone** — high red-to-green ratio, strong yellow/peach tones
- **Cool undertone** — elevated blue channel, pink/rosy dominance, hue above 165°
- **Neutral undertone** — balanced red-to-green ratio within a narrow threshold
- **Olive undertone** — elevated green channel relative to both red and blue

This gives your detected **skin hex color** — the actual average color of your skin sampled directly from your face.

---

### Dominant Color Extraction with scikit-learn

**scikit-learn's KMeans clustering** algorithm extracts the dominant colors from makeup product photos. The image pixels are sampled (up to 5,000 points for performance), then grouped into 5 color clusters. The largest cluster represents the dominant product color.

---

### Delta E Color Matching

Product identification uses the **Delta E CIEDE2000** algorithm — the industry standard for perceptual color distance used by cosmetics labs, Pantone, and paint manufacturers. It converts colors from RGB to the **CIELAB** perceptual color space where distances correspond to how the human eye actually perceives color differences:

- Delta E < 1 → imperceptible difference
- Delta E 1–2 → very close match
- Delta E 2–10 → same color family
- Delta E > 10 → visually different colors

The extracted dominant colors from your photo are compared against 200+ real products in the database and the closest Delta E match is returned.

---

### OCR Label Reading with Tesseract

**Tesseract OCR** reads the text printed on makeup packaging. The image is preprocessed in 4 different ways (threshold, inverted, sharpened, upscaled) to maximize text detection accuracy. Any brand name, product name, or shade name detected is matched against the product database — making text-based identification more accurate than color matching alone.

---

## ML Stack Summary

| Library | What it does |
|---|---|
| **MediaPipe Face Mesh** | 468-point facial landmark detection — maps eyes, mouth, nose, brows, jaw |
| **OpenCV** | Image processing, pixel color sampling, HSV conversion for undertone detection |
| **scikit-learn KMeans** | Dominant color clustering from product photos |
| **Delta E CIEDE2000** | Industry-standard perceptual color distance for product matching |
| **Tesseract OCR** | Reads brand and shade text directly from packaging |
| **NumPy** | Geometric distance calculations and array operations |
| **Pillow** | Image format decoding and conversion |

---

## Setup

### Requirements

```bash
pip install -r requirements.txt
```

### Run locally

```bash
python app.py
```

Then open **http://localhost:5000**

### View your users

```
http://localhost:5000/api/users
```

---

## File Structure

```
glamvault-app/
├── app.py                  ← Flask backend + all ML logic
├── Procfile                ← Tells Railway how to start the app
├── runtime.txt             ← Python version (3.11)
├── requirements.txt        ← Python dependencies
├── .gitignore              ← Keeps secrets off GitHub
├── .env.example            ← Template for environment variables
├── users_tracking.json     ← Auto-created — stores user signups
├── community_products.json ← Auto-created — stores user-submitted products
└── templates/
    └── index.html          ← Full frontend (HTML + CSS + JavaScript)
```

---

## Deploying

**Backend** → Railway (runs the Python Flask + ML server)

**Frontend** → Netlify (serves the HTML to users)

Set publish directory to `templates` in Netlify settings.
