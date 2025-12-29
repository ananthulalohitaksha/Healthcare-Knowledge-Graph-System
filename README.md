
---

# 🏥 Medical Policy Knowledge Graph Generator (Bariatric Surgery)

A professional **multi-agent AI system** for transforming unstructured medical policies and patient records into **computable SQL logic** and **interactive Knowledge Graphs**.
This toolkit provides end-to-end validation of **Bariatric Surgery eligibility (CG-SURG-83)** with full auditability.

---

## 1. Problem Statement

* **Administrative Overhead**
  Medical and operational policies are complex, frequently updated, and interpreted inconsistently across teams, leading to unreliable decisions.

* **Scalability & Delays**
  Manual interpretation does not scale, introduces delays, and lacks a transparent audit trail explaining coverage decisions.

* **Financial Impact**
  Hospitals face billions in unplanned costs annually due to fragmented care and policy interpretation gaps.

---

## 2. 🚀 Quick Start

### 2.1 Install Dependencies

```bash
cd KG
pip install -r requirements.txt
```

### 2.2 Run the Web Application

Before running, create `api.json` in the **project root**:

```json
{
  "gemini": "your-gemini-api-key-here"
}
```

Run the interactive Streamlit app:

```bash
streamlit run app/streamlit_final.py
```

---

## 3. 📁 Project Structure (Restructured)

```text
├── agents/                                # THE ENGINE: Agent Orchestration & Extraction
│   ├── process_policy.py                  # Chained agents for policy extraction
│   ├── process_patient_record.py          # Patient data extraction agents
│   ├── DataField_Valid_Agent.py            # Field accuracy validation
│   ├── OCR/                               # OCR & record processing modules
│   └── scripts/                           # Automation & execution scripts
├── app/                                   # THE UI: Streamlit & Visualizations
│   ├── streamlit_final.py                 # Final interactive web application
│   ├── patient_kg.py                      # Patient KG visualization
│   ├── patient_rule_kg.py                 # Patient vs. policy evaluator
│   ├── patient_rule_kg_interactive.py     # Interactive patient-policy KG
│   ├── policy_rule_kg.py                  # Policy rule KG generator
│   └── policy_rule_kg_interactive.py      # Interactive policy KG
├── db_service/                            # THE BACKEND: Databases & Clinical Mapping
│   ├── Database/                          # Database management system
│   ├── code_mapping.db                    # Clinical code mapping database
│   └── *.csv                              # CPT / ICD-10 clinical datasets
├── documentation/                         # THE EVIDENCE: Reports & Architecture
│   ├── Documentation.md                   # Full technical report
│   ├── Technical_Appendix.md              # Verbatim validation logs & transcripts
│   └── Figures/                           # System diagrams & KG screenshots
├── knowledge_base/                        # THE LIBRARY: Source Data & Runtime Outputs
│   ├── source_data/
│   │   └── NCD_LCD_Syn_data/              # Test policies (L34106, etc.)
│   └── runtime_outputs/
│       ├── Run_Time_Policy/               # Policy extraction (JSON / SQL / PNG)
│       └── Run_Time_Patient/              # Patient compliance outputs
└── benchmarks/                            # THE EVALUATION: Performance & Prompts
    ├── prompts/                           # System prompts for all agents
    └── Evaluation/                        # LLM-as-a-Judge benchmark results
```

---

## 4. 📋 Policy Rule Extraction: Bariatric Surgery

The system transforms the **Anthem Bariatric Surgery Policy (CG-SURG-83)** through a three-agent pipeline.

### 4.1 Policy Extraction Agents Workflow

![Policy Extraction Agents](documentation/Figures/policy_extraction_agents.png)

### 4.2 Data Field Extraction Agent

Identifies and types clinical variables from raw policy text to build a structured **Data Dictionary**.

#### Input

1. **Bariatric Surgery Policy**: https://www.anthem.com/medpolicies/abc/active/gl_pw_d085821.html
2. **Initial Data Dictionary**: [Data_dictionary.json](benchmarks/test1/Data_dictionary.json)
   
```json
[
    {
      "name": "patient_id",
      "type": "string",
      "description": "Unique patient identifier",
      "section": "Demographics"
    }
]
```

#### Output

**Data Dictionary JSON**: [Data_dictionary_CGSURG83.json](https://github.com/ananthulalohitaksha/Healthcare-Knowledge-Graph-System/blob/main/KG/test1/Policy_CGSURG83/Data_dictionary_CGSURG83.json)

---

### 4.3 Policy Extraction Agent

Decomposes narrative policy clauses into **atomic logical rules**.

#### Input

1. **Bariatric Surgery Policy**: https://www.anthem.com/medpolicies/abc/active/gl_pw_d085821.html
2. **Data Dictionary JSON**: [Data_dictionary_CGSURG83.json](https://github.com/ananthulalohitaksha/Healthcare-Knowledge-Graph-System/blob/main/KG/test1/Policy_CGSURG83/Data_dictionary_CGSURG83.json)

#### Output

**Policy Condition JSON**: [Policy_CGSURG83.json](https://github.com/ananthulalohitaksha/Healthcare-Knowledge-Graph-System/blob/main/KG/test1/Policy_CGSURG83/Policy_CGSURG83.json)

---

### 4.4 SQL Conversion Agent

Translates policy logic into **executable SQL WHERE clauses** for database filtering.

#### Input

**Policy Condition JSON**: [Policy_CGSURG83.json](https://github.com/ananthulalohitaksha/Healthcare-Knowledge-Graph-System/blob/main/KG/test1/Policy_CGSURG83/Policy_CGSURG83.json)

#### Output

**SQL**: [SQL_CGSURG83.txt](https://github.com/ananthulalohitaksha/Healthcare-Knowledge-Graph-System/blob/main/KG/test1/Policy_CGSURG83/SQL_CGSURG83.txt)

```sql
WHERE patient_age >= 18 
  AND (patient_bmi >= 40.0 OR (patient_bmi >= 35.0 AND comorbidity_flag = TRUE))
  AND weight_loss_program_history = TRUE
  AND conservative_therapy_attempt = TRUE
  AND preop_medical_clearance = TRUE
  AND preop_psych_clearance = TRUE
  AND preop_education_completed = TRUE
  AND treatment_plan_documented = TRUE
```

---

## 5. 📊 Policy Knowledge Graph

**Bariatric Surgery Policy KG** (`Policy_CGSURG83/policy_rule_kg.png`)

![Bariatric Surgery Policy KG](https://github.com/ananthulalohitaksha/Healthcare-Knowledge-Graph-System/blob/main/knowledge_base/runtime_outputs/Run_Time_Policy/CGSURG_83/policy_rule_kg_CGSURG_83.png)

---

## 6. 📊 Patient–Policy Compliance

The system processes medical policies and patient data through three main phases:

```
Phase 1: Policy Analysis
Input: Policy rules (policy json/sql)
Output: Policy knowledge graph

Phase 2: Patient Analysis  
Input: Patient data (patient record json)
Output: Patient knowledge graph

Phase 3: Compliance Evaluation
Input: Patient data + Policy rules
Output: Compliance report + Visualization
```

### 6.1 Generated Outputs

**1. Policy Knowledge Graph**

**2. Patient Knowledge Graphs**

**3. Compliance Reports**

### 6.2 ✅ Patient 84722025445 — ELIGIBLE

[PDF](knowledge_base/source_data/NCD_LCD_Syn_data/CGSURG_83/CGSURG_83_Record_001.pdf) $\rightarrow$ [OCR](agents/OCR/pdf_ocr.py) $\rightarrow$ [Parser](agents/OCR/medical_record_parser.py) $\rightarrow$ [JSON](knowledge_base/runtime_outputs/Run_Time_Patient/Patient_84722025445_Policy_CGSURG_83/Patient_data_8472-2025-445.json)

```json
{
  "patient_id": "8472-2025-445",
  "patient_age": 47,
  "patient_bmi": 42.4,
  "comorbidity_flag": true,
  "weight_loss_program_history": true,
  "conservative_therapy_attempt": true,
  "preop_medical_clearance": true,
  "preop_psych_clearance": true,
  "preop_education_completed": true,
  "treatment_plan_documented": true,
  "procedure_code_CPT": null,
  "procedure_code_ICD10PCS": null,
  "diagnosis_code_ICD10": null
}
```

* **Clinical Data**: BMI 42.4, Comorbidity present, Pre-op education completed
* **Outcome**: All required conditions satisfied
* **Visualization**: Green nodes in the Knowledge Graph indicate compliance

#### Patient Knowledge Graph

![Patient 8472202544 KG](./knowledge_base/runtime_outputs/Run_Time_Patient/Patient_84722025445_Policy_CGSURG_83/patient_kg_interactive.html)

#### Patient-Policy Compliance Graph

![Patient 8472202544 - Bariatric KG](https://github.com/ananthulalohitaksha/Healthcare-Knowledge-Graph-System/blob/main/KG/patient_data/patient_8472202544/patient_rule_kg.png)

**Compliance Report** (`patient_data/patient_8472202544/pat_8472202544_pol_CGSURG83.json`):

```json
{
  "patient_id": "8472202544",
  "policy_id": "CGSURG83", 
  "patient_met_policy": true,
  "conditions": [
    {
      "condition": "Age requirement: Individual is 18 years or older.",
      "rule": "patient_age >= 18",
      "logic": "AND",
      "is_met": true,
      "logically_met": true,
      "logical_status": "met"
    },
    {
      "condition": "BMI 40 or greater.",
      "rule": "patient_bmi >= 40.0",
      "logic": "OR",
      "is_met": true,
      "logically_met": true,
      "logical_status": "met"
    }
  ]
}
```

---

### 6.3 ❌ Patient 9384202577 — NOT ELIGIBLE

[PDF](https://github.com/ananthulalohitaksha/Healthcare-Knowledge-Graph-System/blob/main/KG/patient_data/patient_9384202577/MR_3.pdf) -- [OCR](https://github.com/ananthulalohitaksha/Healthcare-Knowledge-Graph-System/blob/main/KG/OCR/pdf_ocr.py) -- [Parser](https://github.com/ananthulalohitaksha/Healthcare-Knowledge-Graph-System/blob/main/KG/OCR/medical_record_parser.py) --> [JSON](https://github.com/ananthulalohitaksha/Healthcare-Knowledge-Graph-System/blob/main/KG/patient_data/patient_9384202577/Patient_data_dictionary_9384202577.json)

```json
{
  "patient_id": "9384202577",
  "patient_age": 40,
  "patient_bmi": 27.1,
  "comorbidity_flag": true,
  "weight_loss_program_history": true,
  "conservative_therapy_attempt": true,
  "preop_medical_clearance": true,
  "preop_psych_clearance": true,
  "preop_education_completed": false,
  "treatment_plan_documented": true,
  "procedure_code_CPT": "43775",
  "procedure_code_ICD10PCS": "0DB64Z3",
  "diagnosis_code_ICD10": "E66.01"
}
```

* **Failure Reasons**:
  * BMI below threshold (27.1)
  * Pre-operative education not completed
* **Visualization**: Red nodes highlight unmet conditions

#### Patient Knowledge Graph

![Patient 9384202577 KG](https://github.com/ananthulalohitaksha/Healthcare-Knowledge-Graph-System/blob/main/KG/patient_data/patient_9384202577/patient_kg.png)

#### Patient-Policy Compliance Graph

![Patient 9384202577 - Bariatric KG](https://github.com/ananthulalohitaksha/Healthcare-Knowledge-Graph-System/blob/main/KG/patient_data/patient_9384202577/patient_rule_kg.png)

**Compliance Report** (`patient_data/patient_9384202577/pat_9384202577_pol_CGSURG83.json`):

```json
{
  "patient_id": "9384202577",
  "policy_id": "CGSURG83",
  "patient_met_policy": false,
  "conditions": [
    {
      "condition": "Age requirement: Individual is 18 years or older.",
      "rule": "patient_age >= 18",
      "logic": "AND",
      "is_met": true,
      "logically_met": true,
      "logical_status": "met"
    },
    {
      "condition": "BMI 40 or greater.",
      "rule": "patient_bmi >= 40.0",
      "logic": "OR",
      "is_met": false,
      "logically_met": true,
      "logical_status": "logically_met_by_other_or"
    },
    {
      "condition": "Pre-operative education completed (risks, benefits, expectations, need for long-term follow-up, adherence to behavioral modifications).",
      "rule": "preop_education_completed = TRUE",
      "logic": "AND",
      "is_met": false,
      "logically_met": false,
      "logical_status": "not_met"
    }
  ]
}
```

---

## 7. 🚀 LLM Evaluation

We provided both zero-shot and chain-of-thought prompts for LLM-based Evaluation in [benchmarks/prompts/Evaluation](https://github.com/ananthulalohitaksha/Healthcare-Knowledge-Graph-System/tree/main/benchmarks/prompts/Evaluation)

We replaced the PLACEHOLDER in the prompt with the original OCRed text and generated files, then let LLMs to generate scores.

Generally we pick 2 models (e.g. Gemini and Claude) and 2 prompts for each stage:
1. Data field extraction
2. Policy condition extraction  
3. Patient data extraction

So we have 4 evaluation results for each stage, then we take the average to compute the final recall, precision and accuracy.

---

## 8. 🌐 Streamlit Web Application

The `streamlit_app.py` provides an interactive web interface for the complete workflow:

### Features:

- **📄 Medical Records Page**: Upload PDFs, extract text, parse patient data, generate knowledge graphs
- **🗄️ SQL Queries Page**: View database, run policy filters, manage patient records

### Usage:

```bash
streamlit run streamlit_app.py
```

### Screenshots:

**Medical Records Processing Page:**

![Streamlit Medical Records Page](https://github.com/ananthulalohitaksha/Healthcare-Knowledge-Graph-System/blob/main/KG/Figures/streamlit1.jpg)

**SQL Queries & Database Management Page:**

![Streamlit SQL Queries Page](https://github.com/ananthulalohitaksha/Healthcare-Knowledge-Graph-System/blob/main/KG/Figures/streamlit2.jpg)

---

## 9. 🤖 Technical Validation (LLM-as-a-Judge)

Validation was performed using **Gemini 1.5 Pro** and **Claude 3.5 Sonnet**.
Scores were averaged across **Zero-Shot** and **Chain-of-Thought (CoT)** prompting strategies.

| Phase                  | Metric    | Score     | Assessment                                |
| ---------------------- | --------- | --------- | ----------------------------------------- |
| **Data Dictionary**    | Precision | **0.974** | Rare hallucinated fields                  |
| **Policy Logic**       | F1-Score  | **0.928** | Strong clinical logic structuring         |
| **Patient Extraction** | Precision | **0.917** | Safe extraction; no unsupported inference |

---

## 10. 🎓 Project Team

This project was developed as part of the **CMU × Zyter Capstone Project**.

---
