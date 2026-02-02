# 🤖 Automated Resume–Job Matching & Cover Letter Generator (n8n)

An end-to-end **n8n-based automation** that discovers LinkedIn job postings, evaluates resume–job fit using LLMs, generates tailored cover letters, and stores results in Google Sheets — **without duplicates**.

---

## 🚀 Overview

This project automates the repetitive job-application analysis workflow by:

- Fetching job listings dynamically from LinkedIn
- Parsing job details directly from HTML
- Analyzing resume–job fit using an LLM
- Generating customized cover letters
- Appending results to Google Sheets with **deduplication**
- Remaining safe to re-run without creating redundant entries

---

## 🧠 Key Features

- **Resume-aware scoring** (match score out of 100)
- **AI-generated cover letters** per job
- **Apply-link–based deduplication** (no repeated jobs)
- **Loop-based processing** for multiple job links
- **Google Sheets as control + output layer**
- **Restart-safe execution** (idempotent design)

---

## 🧩 Workflow Architecture

### 1. Input & Configuration
- **Google Sheet (Filter tab)**  
  Controls keywords, location, experience level, job type, remote preference, and Easy Apply flag.

### 2. Resume Ingestion
- **Download File (Google Drive)**  
  Fetches the resume dynamically for analysis and cover-letter generation.

### 3. Job Discovery
- **JavaScript Node**  
  Builds LinkedIn search URLs dynamically.
- **HTTP Request + HTML Parsing**  
  Extracts individual job links.

### 4. Job Processing Loop
- **Split Out + Loop Over Items**  
  Iterates over each job link independently.

### 5. Job Data Extraction
- Parses:
  - Job title  
  - Company  
  - Location  
  - Job description  
  - Apply link  

### 6. AI Evaluation
- **AI Agent (LLM)** generates:
  - Resume–job match score
  - Tailored cover letter

### 7. Deduplication (Critical Feature)
- **Get Existing Results**  
  Reads previously stored jobs from the Results sheet.
- **Deduplicate Job (Code Node)**  
  Compares normalized Apply Links to prevent duplicate entries.

### 8. Output Storage
- **Append Row (Google Sheets)**  
  Saves only new, unique job entries with full AI output.

---

## 📊 Output Format (Google Sheets)

| Title | Company | Location | Apply Link | Score | Description | Cover Letter |
|------|--------|----------|------------|-------|-------------|--------------|

---

## 🛠 Tech Stack

- **n8n** – Workflow automation
- **Google Sheets** – Input + output datastore
- **Google Drive** – Resume storage
- **JavaScript** – Data transformation & deduplication
- **LLM (via AI Agent)** – Scoring & cover-letter generation
- **HTML Parsing** – Job data extraction

---

## 🔁 Why This Design Works

- **Idempotent**: Safe to re-run without data corruption  
- **Scalable**: Handles multiple jobs per execution  
- **Config-driven**: No hardcoded filters  
- **Production-ready**: Handles empty states & edge cases  

---

## 📌 Use Cases

- Job seekers automating application research
- Resume–job matching analysis
- AI-powered career tooling
- Workflow automation portfolios

---

## 📄 License

MIT License
