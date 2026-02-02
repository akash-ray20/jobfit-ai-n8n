# Workflow Overview – Automated Resume–Job Matching & Cover Letter Generator

This document provides a **complete, end-to-end explanation** of the workflow, describing **what each node does**, **how data flows**, and **why specific design choices were made**.  
The goal of this automation is to reliably discover relevant jobs, enrich them using AI, and store results **without duplication**, even across repeated runs.

---

## 🎯 Objective

To build a **restart-safe, configuration-driven automation** that:
- Searches LinkedIn jobs dynamically
- Evaluates job relevance against a resume using an LLM
- Generates personalized cover letters
- Stores results in Google Sheets
- Avoids redundant entries across executions

---

## 🧠 High-Level Architecture

The workflow is composed of five logical layers:

1. **Configuration & Inputs**
2. **Resume Ingestion**
3. **Job Discovery**
4. **Per-Job Processing & AI Enrichment**
5. **Deduplication & Persistence**

Each layer is intentionally isolated to keep the workflow scalable, debuggable, and safe to re-run.

---

## 1️⃣ Configuration & Control Layer

### 🔹 Google Sheets – Filter Sheet
This sheet acts as the **control panel** for the entire workflow.

It defines:
- Keywords
- Location
- Experience level
- Job type
- Remote preference
- Easy Apply filter

**Why this matters:**  
Search behavior can be modified without touching the workflow or code, making the system flexible and user-friendly.

---

## 2️⃣ Resume Ingestion Layer

### 🔹 Download File (Google Drive)
- Downloads the candidate’s resume (PDF) from Google Drive at runtime
- Decouples resume content from the workflow itself

### 🔹 Extract from File
- Converts the PDF resume into plain text
- Prepares the resume for semantic analysis by the LLM

**Why this matters:**  
The resume is treated as dynamic input, allowing easy replacement or reuse across different runs or candidates.

---

## 3️⃣ Job Discovery Layer

### 🔹 JavaScript – LinkedIn Search URL Builder
- Transforms filter-sheet values into valid LinkedIn search query parameters
- Handles mapping of:
  - Experience levels
  - Job types
  - Remote preferences

**Why this matters:**  
LinkedIn’s query syntax is not user-friendly. This node acts as a translation layer between human-readable filters and machine-readable URLs.

---

### 🔹 HTTP Request – LinkedIn Search Page
- Fetches raw HTML for LinkedIn job search results

### 🔹 HTML – Extract Job Links
- Parses the search page
- Extracts individual job posting URLs

### 🔹 Split Out
- Converts a list of job URLs into **individual items**

---

## 4️⃣ Job Processing & AI Enrichment Layer

### 🔹 Loop Over Items
- Iterates over each job URL independently
- Ensures:
  - Isolation between jobs
  - Retry safety
  - Scalability

**Why this matters:**  
Each job is processed as a standalone unit, preventing partial failures from affecting other jobs.

---

### 🔹 HTTP Request – Job Detail Page
- Fetches HTML for a single job posting

### 🔹 HTML – Extract Job Attributes
Extracts structured job data:
- Job title
- Company
- Location
- Job description
- Apply link

### 🔹 Edit Fields
- Normalizes extracted fields
- Aligns key names and formats

---

### 🔹 AI Agent (LLM)
**Inputs:**
- Resume text (from Google Drive)
- Job description (from LinkedIn)

**Outputs:**
- Resume–job match score
- Personalized cover letter

**Why this matters:**  
This replaces keyword matching with **semantic reasoning**, enabling more meaningful job relevance scoring and tailored content generation.

---

### 🔹 Edit Fields (Final Assembly)
- Combines:
  - Job metadata
  - Match score
  - Generated cover letter
- Aligns the final data structure with the output sheet schema

---

## 5️⃣ Deduplication & Persistence Layer (Key Feature)

### 🔹 Get Existing Results (Google Sheets – Result Sheet)
- Reads previously processed job entries
- Configured with **“Always Output Data”** to prevent execution blocking when the sheet is empty
- Used strictly as **reference data**, not as a driver of execution

**Why this matters:**  
This enables the workflow to be **idempotent** — safe to re-run without duplicating data.

---

### 🔹 Deduplicate Job (Code Node)
- Normalizes Apply Links
- Compares the current job against historical entries
- Drops duplicates silently
- Allows only genuinely new jobs to proceed

**Design decision:**  
Deduplication is done via a Code node instead of an IF node to:
- Avoid execution-order issues
- Handle empty reference data safely
- Normalize URLs reliably

---

### 🔹 Append Row (Google Sheets – Result Sheet)
- Writes only new, enriched job entries
- Stores:
  - Title
  - Company
  - Location
  - Apply Link
  - Match score
  - Job description
  - AI-generated cover letter

---

## 🔁 Execution Guarantees

- **Restart-safe**: Workflow can be stopped and re-run without side effects
- **Duplicate-proof**: Previously processed jobs are never re-added
- **Config-driven**: Changing inputs requires no workflow edits
- **Scalable**: Handles multiple jobs per execution via looping

---

## 🧠 Why This Workflow Stands Out

- Treats Google Sheets as both **control plane and datastore**
- Separates **reference data** from **execution flow**
- Uses AI as a reasoning layer, not just text generation
- Designed with real-world automation constraints in mind

---

## 🏁 Summary

This workflow demonstrates how n8n can be used to orchestrate a robust, AI-powered automation that combines web scraping, document processing, semantic analysis, and stateful deduplication into a single cohesive system.

It is intentionally designed to behave like a production pipeline rather than a one-off script.
