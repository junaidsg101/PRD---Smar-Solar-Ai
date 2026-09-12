# 📄 Product Requirements Document (PRD)
## Project: Smart Solar AI ☀️🔋

| Document Details | |
| :--- | :--- |
| **Product Name** | Smart Solar AI |
| **Document Version** | 1.0 |
| **Date** | September 12, 2026 |
| **Status** | Draft / In Progress |
| **Product Type** | AI/ML & Generative AI Web Application |

---

PRD sections, organized sequentially in a table format:

| Seq | PRD Section Name | Brief Overview |
| :--- | :--- | :--- |
| **1** | **Executive Summary** | High-level product vision, purpose, and core value proposition. |
| **2** | **Problem Statement & Solution** | The core user pain points and the AI-driven solution addressing them. |
| **3** | **Target Audience** | Primary (homeowners) and secondary (solar installers) user personas. |
| **4** | **Functional Requirements** | Core features including inputs, engineering sizing, financials, and data handling. |
| **5** | **AI, ML & GenAI Requirements** | Specifics on predictive models (forecasting/anomalies) and LLM narrative generation. |
| **6** | **Technical Architecture & Stack** | Technology stack, frameworks, and code structure/separation of concerns. |
| **7** | **User Flow / User Journey** | Step-by-step user experience from onboarding to final AI summary export. |
| **8** | **Non-Functional Requirements** | Performance, reliability, usability, and security standards (e.g., API key handling). |
| **9** | **Success Metrics (KPIs)** | Quantifiable goals to measure product success and user satisfaction. |
| **10** | **Risks & Mitigations** | Potential pitfalls (e.g., LLM hallucinations) and specific fallback strategies. |

---


## 1. Executive Summary
**Smart Solar AI** is an intelligent solar and battery sizing tool designed to democratize solar energy planning. By combining deterministic electrical-engineering sizing formulas with predictive Machine Learning (ML) and Generative AI (GenAI), the application provides homeowners with personalized, data-driven solar recommendations. The tool eliminates the need for an engineering background, transforming complex energy data into actionable, financially sound, and easily understandable solar designs.

## 2. Problem Statement & Solution
### The Problem
*   **Complexity:** Sizing a solar + battery system requires complex electrical engineering calculations.
*   **Lack of Personalization:** Generic calculators don't account for specific household anomalies, future consumption trends, or specific backup needs.
*   **Data Overload:** Homeowners are presented with raw technical jargon (kW, kWh, peak sun hours) without context on what it means for their specific wallet and lifestyle.

### The Solution
An automated, AI-driven platform that:
1.  Uses **physics-based math** for accurate, fail-safe system sizing.
2.  Uses **Predictive ML** to forecast future energy use, detect past anomalies, and predict hourly solar generation.
3.  Uses **Generative AI** to translate technical outputs into plain-English, personalized narrative summaries.

---

## 3. Target Audience
*   **Primary:** Homeowners considering solar installation (non-technical).
*   **Secondary:** Solar sales representatives and installers looking for a rapid, AI-assisted preliminary design and client-facing explanation tool.

---

## 4. Functional Requirements

### 4.1. System Configuration (Input Module)
*   **FR 1.1:** The system shall accept user inputs for: Location (zip/code), average monthly electricity bill, available roof area, desired backup hours, and critical load percentage.
*   **FR 1.2:** The system shall allow optional toggles/inputs for high-draw appliances (EV charger, AC unit, pool pump).

### 4.2. Recommended System Design (Engineering Module)
*   **FR 2.1:** The system shall calculate and display: Solar array size (kW + panel count), battery storage capacity (kWh), inverter size (kW), and estimated total investment.
*   **FR 2.2:** **Guarded Formulas:** All engineering calculations must include strict boundary checking and fallback values to prevent `NaN`, `Infinity`, or negative results (e.g., if roof area is 0, default to a standard ground-mount assumption or prompt user).

### 4.3. Financial & Environmental Insights
*   **FR 3.1:** Calculate and display estimated monthly savings, payback period (years), and estimated CO₂ avoided per year.

### 4.4. Consumption Analytics & Predictive ML
*   **FR 4.1:** Display a historical monthly usage chart.
*   **FR 4.2:** Generate a next-month consumption forecast using a Scikit-Learn Linear Regression model.
*   **FR 4.3:** Run Anomaly Detection using an Isolation Forest model on historical data. Flag anomalous dates on the chart with a severity indicator (Low/Med/High).

### 4.5. Predicted Solar Generation
*   **FR 5.1:** Generate an hourly solar generation curve for a representative day.
*   **FR 5.2:** Use a lightweight TensorFlow model for prediction if the library is installed.
*   **FR 5.3:** **Fallback:** Automatically revert to a deterministic physics-based calculation (using location irradiance and array size) if TensorFlow is unavailable or fails.

### 4.6. Generative AI Insights (Narrative Summary)
*   **FR 6.1:** Generate a natural-language summary of the entire system design, financial ROI, and ML insights.
*   **FR 6.2:** Integrate with any OpenAI-compatible API. Default configuration: Groq endpoint using Llama 3.3.
*   **FR 6.3:** The GenAI output must explicitly reference the user's specific inputs and the calculated engineering outputs.

### 4.7. Data Management
*   **FR 7.1:** Allow users to upload historical daily consumption data via CSV.
*   **FR 7.2:** Provide a default `data/datasets.csv` for demonstration purposes.
*   **FR 7.3:** Allow users to download the processed dataset and final system design parameters as a CSV/PDF.

---

## 5. AI, ML & GenAI Specific Requirements

Because this is an AI-centric application, specific guardrails and architectural decisions are required:

### 5.1. Predictive Machine Learning
| Model | Framework | Purpose | Fallback / Guardrail |
| :--- | :--- | :--- | :--- |
| **Consumption Forecast** | Scikit-Learn (Linear Regression) | Predict next month's kWh usage based on historical trend. | If insufficient data (<3 months), fallback to simple moving average. |
| **Anomaly Detection** | Scikit-Learn (Isolation Forest) | Identify unusual spikes/drops in daily usage. | If data is too sparse, disable anomaly detection and show a warning. |
| **Generation Curve** | TensorFlow (Tiny Dense/Sequential) | Predict hourly kW output based on time of day/weather proxies. | **Strict Fallback:** Physics-based sine-wave/trapezoid model based on peak sun hours. |

### 5.2. Generative AI (LLM) Integration
*   **Model:** Llama 3.3 (via Groq) for low-latency inference.
*   **Context Window Management:** The prompt must dynamically inject the user's inputs, the engineering outputs, and the ML anomaly findings into the system prompt.
*   **Prompt Engineering Strategy:**
    *   *Role:* "You are an expert, friendly solar energy consultant."
    *   *Task:* "Explain the recommended system, the financial benefits, and any weird energy usage anomalies in plain English."
    *   *Constraints:* "Do not invent numbers. Only use the provided data. Keep it under 300 words."
*   **Hallucination Guardrails:** The UI must clearly label the GenAI text as "AI-Generated Summary" and provide a "View Raw Data" toggle so users can verify the math.
*   **Error Handling:** If the LLM API times out or returns an error, the UI must gracefully degrade, showing a structured, template-based text summary instead of breaking the app.

---

## 6. Technical Architecture & Stack

Based on the provided project structure, the technical stack is defined as follows:

*   **Frontend / UI:** Python-based web framework (Streamlit, Dash, or Gradio) utilizing custom CSS (`support/ui/styling.py`) and modular components (`support/ui/components.py`).
*   **Backend / Core Logic:** Python 3.10+
*   **Engineering Math:** Pure Python / NumPy / Pandas (`support/engineering.py`).
*   **Predictive ML:** Scikit-Learn, TensorFlow/Keras (`support/ml.py`).
*   **Generative AI:** `openai` Python SDK configured for Groq API (`support/ai_insights.py`).
*   **Data Visualization:** Matplotlib, Seaborn (`support/visualize/charts.py`).
*   **Data Handling:** Pandas for CSV parsing and manipulation.

### Directory Structure Enforcement
*   **Separation of Concerns:** UI logic must *never* contain math or ML code. `main_app.py` acts strictly as the orchestrator, passing data between `ui/`, `engineering.py`, `ml.py`, and `ai_insights.py`.

---

## 7. User Flow / User Journey

1.  **Onboarding & Input:** User lands on the app, reads a brief welcome message, and fills out the System Configuration form.
2.  **Data Loading (Optional):** User uploads their utility CSV, or uses the default dataset.
3.  **Processing (Background):**
    *   App calculates engineering sizing (guarded).
    *   App runs ML models (forecast, anomalies, generation).
4.  **Dashboard Rendering:**
    *   Tab 1: System Design & Financials (Cards with kW, kWh, Payback).
    *   Tab 2: Consumption Analytics (Charts with forecast and anomaly flags).
    *   Tab 3: Generation Prediction (Hourly curve).
5.  **GenAI Synthesis:** User clicks "Generate AI Insights". A loading spinner appears while Groq/Llama 3.3 generates the narrative. The text streams or appears in a dedicated "Consultant Summary" card.
6.  **Export:** User downloads their custom report.

---

## 8. Non-Functional Requirements

*   **Performance:** The engineering and ML calculations must complete in < 2 seconds. The GenAI narrative generation should take < 5 seconds (leveraging Groq's fast inference).
*   **Reliability:** The application must not crash if external APIs (LLM) fail or if optional libraries (TensorFlow) are missing. Fallbacks are mandatory.
*   **Usability:** The UI must score highly on accessibility. Tooltips must be present on all technical inputs (e.g., explaining what "Critical Load" means).
*   **Security:** API keys for the LLM (Groq/OpenAI) must be loaded via environment variables (`.env`), never hardcoded.

---

## 9. Success Metrics (KPIs)

*   **Time-to-Value:** Average time from user landing on the page to viewing their final AI summary (Target: < 60 seconds).
*   **Engineering Accuracy:** System sizing matches manual calculations by a certified solar engineer within a 5% margin of error (validated via testing).
*   **GenAI Comprehension:** User feedback/rating on the helpfulness of the AI-generated summary (Target: > 4.5/5 stars).
*   **System Stability:** 99.9% uptime for the core app; graceful degradation rate of 100% for LLM/ML fallbacks (no hard crashes).

---

## 10. Risks & Mitigations

| Risk | Impact | Mitigation Strategy |
| :--- | :--- | :--- |
| **LLM Hallucination** | High | Strict prompt engineering; inject only verified variables; add UI disclaimers; provide "View Raw Data" option. |
| **ML Model Overfitting** | Medium | Use simple models (Linear Regression, Isolation Forest) rather than deep learning for small datasets. Implement strict fallbacks. |
| **Missing User Data** | Medium | If user doesn't upload a CSV, use the default `datasets.csv` and clearly state "Using regional average data for analytics." |
| **API Rate Limits/Costs** | Low | Use Groq (currently highly cost-effective/free tier); cache LLM responses for identical system configurations. |

---