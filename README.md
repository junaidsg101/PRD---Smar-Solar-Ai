# 📄 Product Requirements Document (PRD)
## **Product Name:** Smart Solar AI  
**Version:** 2.0  
**Date:** October 3, 2026  

---
| Seq | PRD Section Name | Brief Overview|
| :--  | :-- | :-- |
| 1 | **Executive Summary** | **Smart Solar AI Multi-Agent Technical Issue Solver** |
| 2 | **Problem & Solution** | **(4)Technical Issue & Solution** |
| 3 | **Scope** | - **MPPT failure/tracking error <br> - Sensor drift/calibration error <br> - Communication protocol failure (Modbus/CAN) <br> - Inverter overheat** |
| 4 | **Functional Requirements** | Feature <br> - Description <br> - Priority |
| 5 | **Non-Functional Requirements** | Performance <br> - Reliability <br> - Security <br> - Scalability <br> - Maintainability  |
| 6 | **Technology Stack & Tools** | Category, Technology / Tool, Purpose |
| 7 | **Cost Estimate (Production)** | - A. Monthly Operational Costs <br> - B. Overall First-Year Cost Estimate |
| 8 | **Project Timeline & Milestones (6-Week Plan)** | Phase, Duration, Key Deliverables |
| 9 | **Risks & Mitigation Strategies** | Risk <br> - Impact <br> - Probability <br> - Mitigation Strategy |
| 10 | **Project Structure** | Folder/ Files |
| 11 | **System Architecture** | Wrokflow in text formate |
---

### **1. Executive Summary**
Solar energy systems suffer from costly downtime due to undetected or misdiagnosed technical faults (e.g., wiring degradation, grid voltage fluctuations, MPPT failures, communication drops). The **Smart Solar AI Multi-Agent Technical Issue Solver** is an AI-driven, automated diagnostic platform. It utilizes a hybrid multi-agent architecture (Sequence, Parallel, and Magnetic patterns) combined with Machine Learning (Scikit-learn) to ingest sensor data, classify faults with >90% accuracy, prescribe actionable, step-by-step repair steps with time estimates, and generate comprehensive reports, reducing Mean Time To Resolution (MTTR) by up to 60%.

---

### **2. Problem & Solution**
| # | Technical Issue | Solution |
| --- | --- |--- |
| 1 | Loose or corroded wiring | Tighten connections and replace damaged cables. |
| 2 | Monitoring system offline | Check internet, power, reset gateway, update firmware. |
| 3 | Wiring/grounding fault | Tighten connections, test ground, repair damaged cables. |
| 4 | Grid voltage fluctuation | Use voltage stabilizer, check grid code, adjust inverter settings. |

---

### **3. Scope**
- Detection of 8 specific issues: Wiring/grounding faults, Grid voltage fluctuation, Monitoring system offline, Loose/corroded wiring, **MPPT failure/tracking error, Sensor drift/calibration error, Communication protocol failure (Modbus/CAN), Inverter overheat**.
- Data ingestion from CSV files (20-row historical samples) and simulated real-time API payloads.
- Multi-Agent orchestration using Sequence, Parallel, and Magnetic patterns with live step-by-step logging.
- ML-based fault classification (Scikit-learn RandomForest) with automatic in-memory fallback.
- Web-based UI dashboard (Streamlit) with **Light/Dark mode toggle**, responsive Plotly/Matplotlib charts, and interactive sensor sliders.
- Logging, audit trails, and severity/financial impact scoring of all agent decisions.
---

### **5. Functional Requirements**
| ID | Feature | Description | Priority |
|----|---------|-------------|----------|
| FR-1 | **Data Ingestion** | System shall read and preprocess `solar_data.csv`, `inverter_logs.csv`, `grid_voltage.csv`, etc., and accept real-time API payloads via sliders/inputs. | High |
| FR-2 | **ML Fault Detection** | System shall use a trained Scikit-learn model to classify incoming data into one of the 8 fault categories or "Normal", returning a confidence score. | High |
| FR-3 | **Magnetic Agent Orchestration** | Manager agent shall analyze the fault type and dynamically delegate sub-tasks to specialist workflows (Data, Diagnostic, Repair, Report). | High |
| FR-4 | **Parallel Data Checking** | For grid voltage or MPPT issues, the system shall simultaneously query voltage sensors, grid APIs, and inverter logs to save time. | Medium |
| FR-5 | **Sequence Repair Flow** | For monitoring offline or comms failures, the system shall execute a strict step-by-step reboot and verification sequence. | Medium |
| FR-6 | **Reporting & UI** | System shall generate a plain-English fault report with severity, financial impact, and recommended actions, displayed via a Streamlit dashboard. | High |
| FR-7 | **MLOps & Fallback** | System shall log model predictions. If the `.pkl` model is missing, it shall gracefully degrade to a safe, in-memory dummy model to prevent crashes. | High |

---

### **6. Non-Functional Requirements**
- **Performance:** Agent diagnosis and report generation must complete in **< 10 seconds** for standard payloads.
- **Reliability:** System uptime of **99.5%** during business hours; graceful degradation if LLM/ML services fail.
- **Security:** All configuration files (`config`) containing API keys must be stored in environment variables. Data at rest encrypted via AES-256.
- **Scalability:** Architecture must support scaling from 1 to 100 solar sites without rewriting agent logic (handled via config-driven site IDs).
- **Maintainability:** Code must adhere to PEP-8, with >80% unit test coverage for agent logic, patterns, and data pipelines.

---

### **7. Technology Stack & Tools**
| Category | Technology / Tool | Purpose |
|----------|-------------------|---------|
| **Language** | Python 3.10+ | Core application logic and agent orchestration. |
| **Data Processing** | Pandas, NumPy | Data cleaning, transformation, and statistical analysis. |
| **Visualization** | Plotly, Matplotlib | Interactive, responsive charts (Line, Bar, Pie, Histogram, Gauge, Topology) in the UI. |
| **Machine Learning** | Scikit-learn | Fault classification models (Random Forest) with Label Encoding. |
| **MLOps** | MLflow, Joblib | Model tracking, artifact saving (`solar_fault_model.pkl`), and deployment monitoring. |
| **Multi-Agent Framework** | Custom Python Classes | Orchestrating Sequence, Parallel, and Magnetic agent patterns with `ThreadPoolExecutor`. |
| **Frontend / UI** | Streamlit | Rapid development of the internal dashboard with dynamic Light/Dark CSS theming. |
| **Configuration** | TOML | Secure, readable storage of API keys, model paths, and site configs. |
| **Logging** | Python `logging` | Audit trails of agent actions, errors, and ML predictions. |

---

### **8. Cost Estimate (Production)**
*Assumptions: Mid-sized deployment managing 5–10 solar sites, processing ~10,000 data rows/day, moderate LLM token usage.*

#### **A. Monthly Operational Costs (Tooling & Infrastructure)**
| Item | Service / Provider | Estimated Monthly Cost | Notes |
|------|-------------------|------------------------|-------|
| **Cloud Compute** | AWS EC2 (t3.medium) or Render | $40 - $60 | Hosts Streamlit app, agent backend, and ML inference. |
| **Cloud Storage** | AWS S3 / Backblaze B2 | $10 - $15 | Stores CSV logs, model artifacts, and MLflow tracking data. |
| **LLM API Costs** | OpenAI / Anthropic | $50 - $150 | Based on ~50k–150k tokens/month for agent reasoning. |
| **MLOps / Monitoring** | MLflow (Self-hosted) + Sentry | $0 - $25 | Self-hosted MLflow is free; Sentry free tier for error tracking. |
| **Domain & SSL** | Namecheap / Cloudflare | $2 - $5 | Custom domain for the Streamlit dashboard. |
| **Total Monthly** | | **~$102 - $255 / month** | *Excludes human labor/salaries.* |

#### **B. Overall First-Year Cost Estimate**
| Category | Estimated Cost (Year 1) | Notes |
|----------|-------------------------|-------|
| **Infrastructure & Tools** | $1,500 - $3,000 | 12 months of operational costs. |
| **Development** | $15,000 - $40,000 | Assumes 1 Full-Stack/ML Engineer for 2–3 months (part-time or contract). |
| **Contingency (15%)** | $2,500 - $6,500 | Buffer for unexpected API price hikes or scope expansion. |
| **Total Year 1 Budget** | **$19,000 - $49,500** | Highly dependent on whether development is in-house or contracted. |

---

### **9. Project Timeline & Milestones (6-Week Plan)**
| Phase | Duration | Key Deliverables |
|-------|----------|------------------|
| **Week 1: Discovery & Design** | Days 1-7 | Finalized PRD, system architecture diagram, dataset collection (20-row CSVs), `requirements.txt` lock. |
| **Week 2: Core ML & Data** | Days 8-14 | Data pipelines built, Scikit-learn ML model trained & validated with auto-fallback logic. |
| **Week 3: Agent Orchestration** | Days 15-21 | Custom Multi-Agent system built (Manager, Diagnostic, Repair, Report) with Sequence/Parallel/Magnetic patterns. |
| **Week 4: UI & Integration** | Days 22-28 | Streamlit dashboard connected to agents, Light/Dark mode CSS enforced, 6+ Plotly/Matplotlib charts implemented. |
| **Week 5: Testing & QA** | Days 29-35 | Unit tests, integration tests, simulated fault injection (sliders), prompt/logic tuning to reduce hallucinations. |
| **Week 6: Deployment & Handoff** | Days 36-42 | Cloud deployment, MLflow tracking active, user documentation (`README.md`), and stakeholder demo. |

---

### **10. Risks & Mitigation Strategies**
| Risk | Impact | Probability | Mitigation Strategy |
|------|--------|-------------|---------------------|
| **LLM/Logic Hallucination** (Agent suggests dangerous repair) | High | Medium | Implement strict prompt engineering, require agent outputs to be validated against a predefined "Safe Repair Knowledge Base", and add a "Human-in-the-Loop" approval step. |
| **Data Quality Issues** (Missing/noisy sensor data) | High | High | Build robust data validation in the `DataAgent` to flag and impute missing values before ML inference. |
| **Model File Corruption/Missing** | Medium | Low | Implement the `SolarFaultPredictor` auto-fallback mechanism to instantly generate a safe, in-memory dummy model to prevent application crashes. |
| **Model Drift** (ML accuracy degrades over time) | Medium | Medium | Use MLflow to monitor prediction distributions. Set up automated alerts to retrain the model quarterly with new field data. |

---

### **11. Project Structure**
```text
solar-multi-agent/
│
├── app.py                      # Main CLI entry point & core agent/pattern logic
├── ui.py                       # Interactive Streamlit UI (Light/Dark mode)
├── config(file)                 # Configuration file (Theme, Server, LLM, App settings)
├── requirements.txt            # Python dependencies
├── README.md                   # Project structure and documentation
│
├── agents/                     # Multi-Agent System definitions
│   ├── __init__.py
│   ├── manager_agent.py        # Central orchestrator routing tasks based on fault type
│   ├── data_agent.py           # Ingests and preprocesses raw sensor data
│   ├── diagnostic_agent.py     # Uses ML model to classify the specific fault
│   ├── repair_agent.py         # Generates step-by-step repair recommendations
│   └── report_agent.py         # Compiles the final diagnostic report with impact scoring
│
├── patterns/                   # Agent orchestration patterns
│   ├── __init__.py
│   ├── sequence.py             # Strict step-by-step execution (e.g., reboot sequences)
│   ├── parallel.py             # Concurrent task execution (e.g., multi-source data checks)
│   └── magentic.py             # Manager delegates to specialist agents dynamically
│
├── data/                       # Sample and historical sensor data (CSV, 20 rows each)
│   ├── grid_voltage.csv        # 3-phase grid voltage and frequency
│   ├── inverter_logs.csv       # Inverter efficiency and error codes
│   ├── wiring_faults.csv       # Historical wiring/grounding fault logs
│   ├── battery_data.csv        # Battery State of Charge (SoC) and health
│   └── monitoring_status.csv   # Network ping, packet loss, and connection status
│
├── ml_models/                  # Directory for saved model artifacts (generated at runtime)
│   ├── solar_fault_model.pkl   # Trained Scikit-learn model
│   └── label_encoder.pkl       # Maps numeric predictions to fault names
│
└── utils/                      # Utility and helper functions
    ├── __init__.py
    ├── logger.py               # Configures rotating file and console logging
    └── helpers.py              # Config loading, timestamp formatting, path helpers
```

---

## Multi-Agent System Architecture

## Presentation Layer

- **[UI]** Streamlit Web UI / Dashboard
  - Light & Dark Mode
- **Flow:** Triggers Workflow & Sliders → Multi-Agent Orchestration Layer

---

## Multi-Agent Orchestration Layer

### Manager Agent

- **[Manager]** Manager Agent (Orchestrator)
- Delegates tasks to:

| Agent | Role | Responsibility |
|---|---|---|
| **[Data]** | Data Agent | Preproc |
| **[Diag]** | Diagnostic Agent | ML Class |
| **[Repair]** | Repair Agent | Planner |
| **[Report]** | Report Agent | Compiler |

---

## AI & ML Engine

| Component | Technology | Purpose |
|---|---|---|
| **[LLM]** | OpenAI / LLM API | Reasoning & NLP |
| **[MLModel]** | Scikit-Learn Fault Model | Random Forest + Fallback |
| **[Patterns]** | Pattern Engines | Sequence, Parallel, Magnetic |

---

## Data & Infrastructure

| Component | Description |
|---|---|
| **[CSVs]** | CSV Data Sources (20-row samples) |
| **[Config]** | config |
| **[Logs]** | MLflow & System Logs |

---
