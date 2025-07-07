# HealthPay Backend Developer Assignment

A simplified, real-world agentic backend pipeline that processes medical insurance claim documents using AI tools and agent orchestration frameworks.

## 🎯 Overview

This application provides a Flask endpoint `/process-claim` that:
- Accepts multiple PDF files (bills, ID cards, discharge summaries)
- Classifies each PDF (currently based on filename/content using rule-based logic due to environment limitations)
- Extracts text from PDFs using PyPDF
- Processes extracted text using specialized agents (BillAgent, DischargeAgent, IDCardAgent)
- Structures data into a defined JSON schema
- Validates output for missing data or inconsistencies
- Returns a final claim decision (approve/reject) with reasons

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
```

## 🔧 Tech Stack

- **Backend**: Flask
- **PDF Processing**: PyPDF for text extraction
- **File Handling**: Python multipart/form-data support
- **Frontend**: Simple HTML/CSS/JavaScript interface

## 📋 Setup Instructions

### Prerequisites
- Python 3.11+

### Installation

1. **Clone and navigate to the project**:
   ```bash
   cd healthpay_backend
   ```

2. **Activate virtual environment**:
   ```bash
   source venv/bin/activate
   ```

3. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

4. **Run the application**:
   ```bash
   python src/main.py
   ```

The application will be available at `http://localhost:5000`

## 🚀 API Endpoint Details

### POST `/api/process-claim`

**Description**: Process multiple PDF claim documents and return structured analysis

**Request**:
- Method: `POST`
- Content-Type: `multipart/form-data`
- Body: Multiple PDF files with key `files`

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

## 🤖 AI Tool Usage

This project was developed with the assistance of a large language model (myself) for code scaffolding, debugging, architectural decisions, and generating explanations. While the design incorporates LLM-based agents, the current implementation relies on rule-based fallbacks due to sandbox environment limitations regarding direct LLM API integration.

### Prompt Examples (Conceptual, for LLM integration):

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
- hospital_name: Name of the hospital or medical facility
- total_amount: Total amount charged (as a number)
- date_of_service: Date of service (in YYYY-MM-DD format)

Text: [extracted text content]

Return the information in JSON format:
{
    "hospital_name": "...",
    "total_amount": 0,
    "date_of_service": "YYYY-MM-DD"
}
```

#### 3. Text Enhancement Prompt:
```
Clean and enhance the following extracted text from a bill document.
Remove any OCR errors, fix formatting, and make it more readable:

[raw extracted text]

Return the cleaned text.
```

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

## 🧪 Testing

### Manual Testing
1. Access the web interface at `http://localhost:5000`
2. Upload sample PDF files (bills, ID cards, discharge summaries)
3. Click "Process Claim" to see results

### API Testing with curl
```bash
curl -X POST http://localhost:5000/api/process-claim \
  -F "files=@sample_bill.pdf" \
  -F "files=@sample_discharge.pdf"
```

## 📁 Project Structure

```
healthpay_backend/
├── src/
│   ├── routes/
│   │   ├── claim.py          # Main claim processing logic
│   │   └── user.py           # Template user routes
│   ├── models/
│   │   └── user.py           # Database models
│   ├── static/
│   │   └── index.html        # Frontend interface
│   └── main.py               # Flask application entry point
├── venv/                     # Virtual environment
├── requirements.txt          # Python dependencies
└── README.md                 # This file
```

## 🔍 Key Features

- ✅ Multi-file PDF upload support
- ✅ Document classification (rule-based fallback)
- ✅ Specialized processing agents for different document types
- ✅ Data validation and consistency checking
- ✅ Automated claim decision making
- ✅ Web interface for easy testing
- ✅ CORS support for frontend integration
- ✅ Error handling and fallback mechanisms

## 🚀 Deployment Ready

The application is configured for deployment with:
- CORS enabled for cross-origin requests
- Host binding to `0.0.0.0` for external access
- Static file serving for frontend
- Production-ready Flask configuration

## 📝 Notes

- The current implementation uses rule-based classification and extraction due to sandbox environment limitations with direct LLM API integration.
- In a production environment with full LLM API access, the `DocumentClassifier` and `TextExtractor` would leverage LLMs for more advanced and accurate processing.
- The application uses placeholder API keys for demonstration.
- In production, proper API key management and security measures should be implemented.
- The current implementation focuses on core functionality and can be extended with additional features like authentication, logging, and monitoring.



