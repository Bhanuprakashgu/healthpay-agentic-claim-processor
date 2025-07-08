```markdown
# HealthPay Backend Developer Assignment

A simplified, real-world agentic backend pipeline that processes medical insurance claim documents using AI tools and agent orchestration frameworks.

## 🎯 Overview

This application provides a **FastAPI** endpoint `/process-claim` that:
- Accepts multiple PDF files (bills, ID cards, discharge summaries)
- Classifies each PDF (currently based on filename/content using rule-based logic due to environment limitations)
- Extracts text from PDFs using PyPDF
- Processes extracted text using specialized agents (BillAgent, DischargeAgent, IDCardAgent)
- Structures data into a defined JSON schema
- Validates output for missing data or inconsistencies
- Returns a final claim decision (approve/reject) with reasons

---

## 🏗️ Architecture

### Core Components

1. **DocumentClassifier**: Classifies uploaded PDFs based on content and filename. Designed for LLM integration, but currently uses rule-based logic.
2. **TextExtractor**: Extracts text from PDFs using PyPDF. Designed for LLM enhancement, but currently extracts raw text.
3. **Specialized Agents**:
   - **BillAgent**: Processes medical bills to extract hospital name, amount, and service date
   - **DischargeAgent**: Processes discharge summaries to extract patient info, diagnosis, and dates
   - **IDCardAgent**: Processes ID cards to extract patient name, ID number, and insurance provider
4. **ClaimValidator**: Validates processed documents for completeness and consistency
5. **ClaimDecisionEngine**: Makes final approval/rejection decisions based on validation results

### Data Flow

```

PDF Upload → Classification (Rule-based) → Text Extraction (PyPDF) → Agent Processing → Validation → Decision

````

---

## 🔧 Tech Stack

- **Backend**: FastAPI
- **PDF Processing**: PyPDF for text extraction
- **File Handling**: Python multipart/form-data support
- **Frontend**: Simple HTML/CSS/JavaScript interface

---

## 📋 Setup Instructions

### Prerequisites
- Python 3.11+
- `pip`, `uvicorn`, and virtual environment tools

### Installation

1. **Clone and navigate to the project**:
   ```bash
   git clone https://github.com/Bhanuprakashgu/healthpay-agentic-claim-processor.git
   cd healthpay-agentic-claim-processor
````

2. **Create and activate virtual environment**:

   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies**:

   ```bash
   pip install -r requirements.txt
   ```

4. **Run the application using FastAPI**:

   ```bash
   uvicorn app.main:app --host 0.0.0.0 --port 8000
   ```

The application will be available at:
`http://localhost:8000`

---

## 🚀 API Endpoint Details

### POST `/process-claim`

**Description**: Process multiple PDF claim documents and return structured analysis

**Request**:

* Method: `POST`
* Content-Type: `multipart/form-data`
* Body: Multiple PDF files with key `files`

**Response**:

```json
{
  "documents": [
    {
      "type": "bill",
      "hospital_name": "ABC Hospital",
      "total_amount": 12500,
      "date_of_service": "2024-04-10"
    },
    {
      "type": "discharge_summary",
      "patient_name": "John Doe",
      "diagnosis": "Fracture",
      "admission_date": "2024-04-01",
      "discharge_date": "2024-04-10"
    }
  ],
  "validation": {
    "missing_documents": [],
    "discrepancies": []
  },
  "claim_decision": {
    "status": "approved",
    "reason": "All required documents received and validated."
  }
}
```

---

## 🤖 AI Tool Usage

This project was developed with the assistance of the following AI tools:

### 🛠️ Tools Used:

* **Cursor.ai** – used extensively to scaffold project modules, generate validation logic, and improve code structure.
* **ChatGPT** – used for LLM prompt brainstorming, JSON schema generation, and refining fallback extraction rules.
* **Google Gemini (planned)** – Integration is architected for Gemini API calls, though currently simulated via rule-based logic.

---

### 📌 Prompt Examples (Conceptual for LLM use):

#### 1. Document Classification Prompt:

```
Analyze the following document and classify it as one of: 'bill', 'id_card', 'discharge_summary'

Filename: medical_bill_abc_hospital.pdf
Content preview: MEDICAL BILL
ABC Hospital
Patient: John Doe
Date of Service: 2024-04-10
Total Amount: $12,500...

Return only the classification type (bill, id_card, or discharge_summary).
```

#### 2. Bill Processing Prompt:

```
Extract the following information from this medical bill document:
- hospital_name
- total_amount
- date_of_service

Text: [extracted text content]

Return the information in JSON format.
```

#### 3. Text Enhancement Prompt:

```
Clean and enhance the following extracted text from a bill document.
Fix OCR issues, remove garbage characters, and reformat:

[raw extracted text]
```

---

## 🏛️ Design Choices

### 1. Agent-Based Architecture
- **Rationale**: Separates concerns by having specialized agents for different document types
- **Benefits**: Easier to maintain, test, and extend with new document types

### 2. LLM Integration (Planned)
- **Rationale**: Provides robust LLM capabilities for classification and extraction
- **Implementation**: The code is structured to easily integrate LLMs (e.g., Gemini) for classification and structured data extraction once an API key is provided and the environment supports it.

### 3. Fallback Mechanisms
- **Rationale**: Ensures system reliability when AI calls fail or are unavailable
- **Implementation**: Filename-based classification and rule-based extraction provide functional fallbacks.

### 4. Modular Validation
- **Rationale**: Separates validation logic from processing logic
- **Benefits**: Easy to modify validation rules without affecting core processing

### 5. Simple Frontend Interface
- **Rationale**: Provides easy testing and demonstration capabilities
- **Implementation**: Single-page application with file upload and results display


---

## 🧪 Testing

### Manual Testing

1. Visit: `http://localhost:8000`
2. Upload a bill, ID card, and discharge summary PDF
3. Click "Process Claim"
4. View the structured response

### API Testing with cURL

```bash
curl -X POST http://localhost:8000/process-claim \
  -F "files=@sample_bill.pdf" \
  -F "files=@sample_discharge.pdf"
```

---

## 📁 Project Structure

```
healthpay-agentic-claim-processor/
├── app/
│   ├── agents/             # BillAgent, DischargeAgent, etc.
│   ├── services/           # Extraction & validation logic
│   ├── routes/             # Route handlers
│   ├── schemas.py          # Pydantic models
│   └── main.py             # FastAPI entrypoint
├── static/                 # Frontend interface (optional)
├── requirements.txt        # Project dependencies
└── README.md               # You're reading it
```

---

## ✅ Key Features

* ✅ Multi-PDF file support
* ✅ Agent-based document processing
* ✅ Modular architecture
* ✅ Validation + decision engine
* ✅ Rule-based fallback logic
* ✅ Prompt-ready LLM integration
* ✅ Ready for production extension

---

## 🚀 Deployment Notes

* To deploy on platforms like Render:

  * Use the `uvicorn` start command as shown
  * Bind to `0.0.0.0`
  * Set environment variables securely if LLM API keys are added

---

## 📝 Final Notes

* The current version uses **rule-based logic** due to sandbox limitations.
* LLM-based extraction and classification is **planned and designed for future integration**.
* This app is modular, testable, and follows HealthPay’s JSON schema and functional workflow expectations.

---

**Thank you for reviewing this submission!**

```

---

Let me know if you want:
- A `render.yaml` for one-click Render deployment
- A Loom recording outline based on your script
- A ZIP-ready version of this project

You're ready to go 🚀
```
