# Medical Policy Knowledge Graph System: Documentation

## 1. Executive Summary

Modern healthcare delivery is burdened by the manual, inconsistent interpretation of complex medical coverage policies. This system provides a scalable solution by automatically translating unstructured PDF medical policies into computable SQL rules and visual Knowledge Graphs. The Policy Automation component effectively transforms a manual review process into an automated pipeline, allowing for reduced claim denials by visually pinpointing missing requirements prior to submission.

## 2. Problem Statement
* **Administrative Overhead**: Medical and operational policies are complex, frequently updated, and interpreted differently across teams, making consistent decisions difficult.
* **Scalability & Delays**: Manual interpretation limits scalability, introduces significant delays, and lacks a transparent audit trail for why a specific coverage decision was made.
* **Financial Impact**: Hospitals face an estimated $17.4 billion in unplanned costs annually due to fragmented care and policy interpretation gaps. The administrative cost to rework a single denied claim is estimated between $25 and $118.

## 3. System Architecture

The architecture is built upon a dual-stream pipeline that processes medical policies and patient records in parallel before converging at a decision engine.

![High-Level System Architecture](./Figures/high_level_arch.png)

### 3.1 Policy Processing Stream

This stream transforms narrative policy text into executable logic through four stages:
1. **OCR (Text Extraction)**: Converts PDF documents into machine-readable text using a dual-technology approach (PyMuPDF and Tesseract) to handle complex layouts and tables.
2. **Agent 1: Data Field Extraction**: Constructs a Policy Data Dictionary by identifying all clinical data fields referenced in the policy (e.g., `patient_bmi`, `patient_age`).
3. **Agent 2: Policy Extraction**: Decomposes narrative statements into atomic logical rules (e.g., `((BMI >= 40) OR (BMI >= 35 AND Comorbidity = True))`).
4. **Agent 3: SQL Conversion**: Translates structured rules into an executable SQL WHERE clause.

![Policy Extraction Architecture](./Figures/policy_extraction_agents1.png)

### 3.2 Patient Record Processing Stream
* **Medical Record Parser**: Guided by the Data Dictionary, this agent interprets clinical context from unstructured physician notes to populate required fields.
* **Normalization**: Handles variability in medical terminology (e.g., mapping "HTN" to "Hypertension" or "I10" to an ICD code).

## 4. Compliance Evaluation & Visualization

The Decision Engine brings together outputs from both streams to systematically evaluate eligibility.
* **Condition-Level Evaluation**: Compares clinical values with policy thresholds using data-type-aware logic.
* **Knowledge Graph Generation**: Color-coded visualization (Green for satisfied, Red for unmet) provides immediate interpretability and rapid clinical review.

![Patient Rule Knowledge Graph](./Figures/patient_rule_kg.png)

## 5. Technical Validation

Validation utilized a structured "LLM-as-a-Judge" methodology to assess reasoning capabilities across extraction phases. Diverse models (Claude 4.5 Sonnet and Gemini 3 Pro) and strategies (Zero-shot and Chain-of-Thought) were used to ensure objective scoring.

### 5.1 Phase 1: Data Dictionary Extraction Performance
**Objective**: Evaluate how accurately the system identifies structured variable definitions (CPT codes, coverage booleans, etc.) from policy text.

| Metric | Score | Assessment |
| :--- | :--- | :--- |
| **Precision** | **0.974** | Near Perfect: The system rarely "hallucinated" fields. Almost every extracted variable was supported by the text. |
| **Recall** | **0.676** | Moderate: The system missed some administrative nuances, such as specific provider specialty requirements. |
| **F1-Score** | **0.793** | Strong Baseline: A solid foundation for building the knowledge graph. |
*Table 1: Phase 1 Average Performance (PNS Policy)*

### 5.2 Phase 2: Policy Condition Extraction Performance
**Objective**: Evaluate the system's ability to convert complex natural language clauses into computable logical rules.

| Metric | Score | Assessment |
| :--- | :--- | :--- |
| **Precision** | **0.969** | High Reliability: The system accurately captured the logic for core rules. |
| **Recall** | **0.894** | High Completeness: The system successfully extracted the vast majority of eligibility criteria. |
| **F1-Score** | **0.928** | Excellent: This is the highest-performing phase, indicating the system is highly effective at structuring clinical policy logic. |
*Table 2: Phase 2 Average Performance (PNS Policy)*

### 5.3 Phase 3: Patient Condition Extraction Performance
**Objective**: Evaluate the accuracy of extracting clinical facts (contraindications, prior failures) from unstructured records.

| Metric | Score | Assessment |
| :--- | :--- | :--- |
| **Precision** | **0.917** | Safe Extraction: The system did not infer clinical facts that were not present in the text. |
| **Recall** | **0.609** | Conservative: The system missed some implicit concepts, such as calculating durations from text or inferring unlisted codes. |
| **F1-Score** | **0.729** | Good: The extraction is reliable for explicit facts but requires refinement for inferential reasoning. |
*Table 3: Phase 3 Average Performance (Patient Record L37360_001)*

### 5.4 Validation Logs

For the full, verbatim transcripts of the LLM prompts and raw JSON outputs used to derive these consensus metrics, please refer to the Technical Appendix.

**Figure 4. Summary of System Performance across Extraction Phases**

![Validation Metrics Summary](./Figures/validation_chart.png)

## 6. Limitations & Future Roadmap
* **Unstable Extraction**: LLM-based extraction may fail on rare edge cases or deeply nested logical structures.
* **Lack of Oversight**: The current pipeline lacks a Human-in-the-Loop (HITL) validation mechanism for low-confidence or high-risk decisions.
* **Interoperability**: Medical code coverage is currently limited to CPT and ICD-10; future work includes UMLS and HL7/FHIR integration.

---

If you want, I can also **reformat the tables and figure captions** to match professional publication style, so the Markdown looks fully polished and consistent. Do you want me to do that next?
