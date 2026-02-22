# Quran-Ai-Prototype

Title: Quran Recitation AI Web App - Prototype Guide

---

## 1. Prototype Overview

**Goal:** Build a simple web prototype that records Quran recitation, checks it against the correct ayah, and shows mistakes.

**Scope:**

* Records 5–10 seconds of recitation
* Converts speech to text using Whisper tiny/base
* Compares text to 3–5 Surahs
* Shows mistakes after ~30 seconds
* Word-level comparison only, no tajweed detection yet

**Important:**

* Focus on working logic first
* Accuracy must be reasonable for prototype
* Privacy: audio is temporary

---

## 2. Prototype Architecture

```
Browser (Frontend)
   ↓
Record audio (MediaRecorder)
   ↓
Send audio → Flask backend
   ↓
Whisper converts speech → text
   ↓
Compare with correct ayah
   ↓
Return JSON with mistakes
   ↓
Display on frontend
```

---

## 3. Frontend (Browser)

**Purpose:**

* Record audio from microphone
* Send audio to backend
* Display mistakes

**HTML Skeleton:**

```html
<button id="recordBtn">Record Recitation</button>
<div id="results"></div>
<script src="script.js"></script>
```

**JS Concept:**

* Use `MediaRecorder` API to capture audio
* Send audio to `/analyze` endpoint via POST
* Display JSON results (mistakes) in `results` div

**Future Enhancements:**

* Dropdown to select Surah/Ayah
* Highlight correct vs incorrect words
* User progress tracking

---

## 4. Backend (Python + Flask)

**Purpose:**

* Receive audio from frontend
* Convert speech to text using Whisper tiny
* Compare spoken text with correct ayah
* Return mistakes as JSON

**Python Skeleton:**

```python
from flask import Flask, request, jsonify
import whisper

app = Flask(__name__)
model = whisper.load_model("tiny")  # CPU-friendly

CORRECT_AYAH = "الحمد لله رب العالمين"

@app.route("/analyze", methods=["POST"])
def analyze():
    audio = request.files["audio"]
    audio.save("recitation.wav")

    result = model.transcribe("recitation.wav", language="ar")
    spoken_text = result["text"]

    mistakes = compare(spoken_text, CORRECT_AYAH)

    return jsonify({
        "spoken": spoken_text,
        "mistakes": mistakes
    })

def compare(spoken, correct):
    spoken_words = spoken.split()
    correct_words = correct.split()
    errors = []
    for i in range(len(correct_words)):
        if i >= len(spoken_words):
            errors.append(f"Missing: {correct_words[i]}")
        elif spoken_words[i] != correct_words[i]:
            errors.append(f"Wrong: {spoken_words[i]}")
    return errors

if __name__ == "__main__":
    app.run(debug=True)
```

**Notes:**

* Tiny model is fast for CPU
* Audio can be deleted after processing
* Flask handles JSON communication with frontend

---

## 5. Workflow

1. User selects a Surah/Ayah (for now just 1–3)
2. User clicks **Record**
3. Browser captures audio and sends to backend
4. Backend transcribes using Whisper
5. Text is compared to correct ayah
6. JSON with mistakes is returned
7. Frontend displays mistakes after ~30s

---

## 6. Testing Prototype

* Test with short recordings (5–10s)
* Check mistakes are detected correctly
* Ensure browser communicates with Flask backend
* Adjust word-level comparison if needed

---

## 7. Prototype Limitations

* Word-level only (no tajweed rules yet)
* Only 3–5 Surahs available
* Slower on CPU-only machines (~10–20s per transcription)
* Accuracy is basic, sufficient for demonstration

---

End of Prototype Guide
