
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

![Policy Extraction Agents](https://github.com/sijiasiga/Capstone_KG_VoiceAgents/blob/main/KG/Figures/policy_extraction_agents.png)

### 4.2 Data Field Extraction Agent

Identifies and types clinical variables from raw policy text to build a structured **Data Dictionary**.

#### Input

1. **Bariatric Surgery Policy**: https://www.anthem.com/medpolicies/abc/active/gl_pw_d085821.html
2. **Initial Data Dictionary**: [Data_dictionary.json](https://github.com/sijiasiga/Capstone_KG_VoiceAgents/blob/main/KG/test1/Data_dictionary.json)

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

**Data Dictionary JSON**: [Data_dictionary_CGSURG83.json](https://github.com/sijiasiga/Capstone_KG_VoiceAgents/blob/main/KG/test1/Policy_CGSURG83/Data_dictionary_CGSURG83.json)

---

### 4.3 Policy Extraction Agent

Decomposes narrative policy clauses into **atomic logical rules**.

#### Input

1. **Bariatric Surgery Policy**: https://www.anthem.com/medpolicies/abc/active/gl_pw_d085821.html
2. **Data Dictionary JSON**: [Data_dictionary_CGSURG83.json](https://github.com/sijiasiga/Capstone_KG_VoiceAgents/blob/main/KG/test1/Policy_CGSURG83/Data_dictionary_CGSURG83.json)

#### Output

**Policy Condition JSON**: [Policy_CGSURG83.json](https://github.com/sijiasiga/Capstone_KG_VoiceAgents/blob/main/KG/test1/Policy_CGSURG83/Policy_CGSURG83.json)

---

### 4.4 SQL Conversion Agent

Translates policy logic into **executable SQL WHERE clauses** for database filtering.

#### Input

**Policy Condition JSON**: [Policy_CGSURG83.json](https://github.com/sijiasiga/Capstone_KG_VoiceAgents/blob/main/KG/test1/Policy_CGSURG83/Policy_CGSURG83.json)

#### Output

**SQL**: [SQL_CGSURG83.txt](https://github.com/sijiasiga/Capstone_KG_VoiceAgents/blob/main/KG/test1/Policy_CGSURG83/SQL_CGSURG83.txt)

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

**Bariatric Surgery Policy KG**

![Bariatric Surgery Policy KG](https://github.com/sijiasiga/Capstone_KG_VoiceAgents/blob/main/KG/test1/Policy_CGSURG83/policy_rule_kg.png)

---

## 6. 📊 Patient–Policy Compliance

The system merges patient records with policy logic to produce an eligibility determination and visual explanation.

### 6.1 ✅ Patient 8472202544 — ELIGIBLE

* **Clinical Data**: BMI 42.4, Comorbidity present, Pre-op education completed
* **Outcome**: All required conditions satisfied
* **Visualization**: Green nodes in the Knowledge Graph indicate compliance

#### Patient Knowledge Graph

![Patient 8472202544 KG](https://github.com/sijiasiga/Capstone_KG_VoiceAgents/blob/main/KG/patient_data/patient_8472202544/patient_kg.png)

#### Patient-Policy Compliance Graph

![Patient 8472202544 - Bariatric KG](https://github.com/sijiasiga/Capstone_KG_VoiceAgents/blob/main/KG/patient_data/patient_8472202544/patient_rule_kg.png)

---

### 6.2 ❌ Patient 9384202577 — NOT ELIGIBLE

* **Failure Reasons**:
  * BMI below threshold (27.1)
  * Pre-operative education not completed
* **Visualization**: Red nodes highlight unmet conditions

#### Patient Knowledge Graph

![Patient 9384202577 KG](https://github.com/sijiasiga/Capstone_KG_VoiceAgents/blob/main/KG/patient_data/patient_9384202577/patient_kg.png)

#### Patient-Policy Compliance Graph

![Patient 9384202577 - Bariatric KG](https://github.com/sijiasiga/Capstone_KG_VoiceAgents/blob/main/KG/patient_data/patient_9384202577/patient_rule_kg.png)

---

## 7. 🌐 Streamlit Web Application

### Screenshots:

#### Medical Records Processing Page

![Streamlit Medical Records Page](https://github.com/sijiasiga/Capstone_KG_VoiceAgents/blob/main/KG/Figures/streamlit1.jpg)

#### SQL Queries & Database Management Page

![Streamlit SQL Queries Page](https://github.com/sijiasiga/Capstone_KG_VoiceAgents/blob/main/KG/Figures/streamlit2.jpg)

---

## 8. 🤖 Technical Validation (LLM-as-a-Judge)

Validation was performed using **Gemini 1.5 Pro** and **Claude 3.5 Sonnet**.
Scores were averaged across **Zero-Shot** and **Chain-of-Thought (CoT)** prompting strategies.

| Phase                  | Metric    | Score     | Assessment                                |
| ---------------------- | --------- | --------- | ----------------------------------------- |
| **Data Dictionary**    | Precision | **0.974** | Rare hallucinated fields                  |
| **Policy Logic**       | F1-Score  | **0.928** | Strong clinical logic structuring         |
| **Patient Extraction** | Precision | **0.917** | Safe extraction; no unsupported inference |

---

## 9. 🎓 Project Team

This project was developed as part of the **CMU × Zyter Capstone Project**.

---
