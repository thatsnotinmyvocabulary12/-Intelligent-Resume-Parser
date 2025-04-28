# Intelligent Resume Parser
# Python NLP OCR

An AI-powered pipeline to extract structured data from resumes (PDFs/images) using OCR (for scanned resumes) and Mistral LLM via Ollama for intelligent field extraction. Outputs JSON with confidence scores.

# 🚀 Key Features: 

Multi-Format Support: Parses text-based PDFs, image-based PDFs, and images (JPG/PNG).

LLM-Powered Extraction: Uses Mistral via Ollama for context-aware parsing (names, skills, dates).

Confidence Scoring: Manual confidence metrics for transparency.

OCR Fallback: Auto-switches to Tesseract if text extraction fails.

Structured Output: Clean JSON with normalized fields (e.g., dates).

# 🛠️ How I Approached This Project
## 1. Problem Analysis
Challenge: Resumes vary wildly in formatting (text vs. scanned, inconsistent layouts).

Goal: Extract structured data (name, skills, education, etc.) reliably.

## 2. Technical Design
   code:
   flowchart TD
    A[Input PDF/Image] --> B{Is PDF?}
    B -->|Yes| C[Extract Text via PyMuPDF]
    B -->|No| D[OCR via Tesseract]
    C --> E{Text Found?}
    E -->|No| D
    E -->|Yes| F[LLM Processing]
    D --> F
    F --> G[Mistral via Ollama]
    G --> H[JSON Output + Confidence Scores]

## 3. Key Components
Text Extraction:

PyMuPDF: First attempt to extract text from PDFs.

Tesseract OCR: Fallback for image-based PDFs/JPG/PNG.

LLM Processing:

Mistral via Ollama: Handles unstructured text → structured JSON.

Prompt Engineering: Strict JSON output requirements (no extra text).

Post-Processing:

Manual confidence scores (simple heuristics based on presence/absence).

Error handling for malformed JSON.

## 4. Challenges & Solutions
Challenge	Solution
Scanned PDFs	Hybrid PyMuPDF + Tesseract fallback
LLM output consistency	Rigid prompt constraints + JSON validation
Date normalization	LLM prompt explicitly requests normalized formats
Missing fields	null handling + confidence scores

# ⚙️ Installation
## Prerequisites
Python 3.8+

Ollama installed and running (for Mistral)

Tesseract OCR:

bash
# Ubuntu
sudo apt install tesseract-ocr

# MacOS
brew install tesseract

# Windows (update path in code)
choco install tesseract

# Install dependencies:

bash
pip install pymupdf pytesseract pdf2image pillow ollama
Start Ollama (in a separate terminal):

bash
ollama pull mistral
ollama serve

# 📌 Usage
Command Line
bash
python resume_parser.py --file path/to/resume.pdf --output result.json
Python API
python
from resume_parser import parse_resume
result = parse_resume("resume.pdf")
print(result)
Output Example
json
{
  "name": "John Doe",
  "email": "john@example.com",
  "phone": "+1234567890",
  "education": [
    {
      "degree": "BSc Computer Science",
      "institution": "MIT",
      "year": "2020"
    }
  ],
  "confidence": {
    "name": 0.95,
    "email": 1.0,
    "phone": 0.9
  }
}

# 🧩 Code Highlights
1. Hybrid Text Extraction
python
def extract_text(file_path):
    text = ""
    if file_path.endswith(".pdf"):
        doc = fitz.open(file_path)
        for page in doc:
            text += page.get_text()  # PyMuPDF extraction
        if not text.strip():  # Fallback to OCR
            images = convert_from_path(file_path)
            text = " ".join(pytesseract.image_to_string(img) for img in images)
    else:  # Direct OCR for images
        text = pytesseract.image_to_string(Image.open(file_path))
    return text
2. LLM Prompt Engineering
python
prompt = f"""
You are a professional resume parser. Extract JSON with fields:
{{
  "name": "", 
  "email": "",
  ...
}}
Important:
- Return null for missing fields.
- Only output valid JSON (no extra text).
"""
response = ollama.chat(model="mistral", messages=[{"role": "user", "content": prompt}])

# 📈 Future Improvements
Validation Layer: Cross-check email/phone formats with regex.

Batch Processing: Support folder inputs for bulk parsing.

Enhanced Confidence Scoring: Use LLM self-evaluation.

# 📜 License
MIT © Mugesh surya pradeep

# 🤝 Contributing
PRs welcome! Focus areas:

Better OCR preprocessing (deskewing, contrast adjustment).

Alternative LLM backends (e.g., OpenAI, LiteLLM).

