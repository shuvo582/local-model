# local-model
Yes—you can absolutely build a lightweight, local AI model to parse and classify your scraped rows into the eight target fields. Below is:

1. **A high-level step-by-step guide** (from environment setup through deployment)  
2. **Rough data-volume guidelines** for initial vs. production-grade accuracy  
3. **A project outline** with milestones, deliverables and estimated timelines  

---

## 1. Step-by-Step Guide

1. **Define Scope & Requirements**  
   - 8 output fields: Website Name, Website Link, Contact Number(s), Email Address(es), Instagram Link, Facebook Link, Category, Country  
   - Input: one free-text CSV row (all columns concatenated)  
   - Output: structured JSON or CSV line  

2. **Choose Your Tech Stack**  
   - Python 3.9+  
   - **spaCy** (fast NER-based pipeline) _or_ **Hugging Face Transformers** (DistilBERT for token-classification)  
   - Annotation tool: [Label Studio](https://github.com/heartexlabs/label-studio) or [Prodigy] if licensed  

3. **Set Up Your Environment**  
   ```bash
   python -m venv venv
   source venv/bin/activate
   pip install spacy sklearn pandas label-studio
   # If using Transformers:
   pip install transformers datasets torch
   ```  

4. **Data Collection & Annotation**  
   - **Seed dataset**: grab 500–1,000 real scraped rows.  
   - **Annotation**: mark spans for each field in each row.  
     - Aim for at least **200 annotations per field type** (i.e. ~1,600 total span tags).  
     - Export in spaCy’s JSON format or Hugging Face’s token-classification format.  

5. **Model Training**  
   - **spaCy route**:  
     ```bash
     python -m spacy init config config.cfg --lang en --pipeline ner
     python -m spacy train config.cfg --paths.train train.spacy --paths.dev dev.spacy
     ```  
   - **Transformers route**:  
     ```python
     from datasets import load_dataset
     from transformers import AutoTokenizer, AutoModelForTokenClassification, Trainer, TrainingArguments

     # load, tokenize, train...
     ```  

6. **Evaluation & Iteration**  
   - Measure **precision/recall** on held-out dev set.  
   - Identify under-performing classes (e.g. Country names vs. category).  
   - Expand annotation by another 500–1,000 rows in weak spots.  

7. **Integration into Your Pipeline**  
   - Wrap model in a small Python script that reads raw CSV rows, runs NLP predict(), then writes your eight-column CSV.  
   - Optionally bundle into a CLI or minimal GUI with Tkinter (as you already have).  

8. **Deployment & Maintenance**  
   - Run locally on CPU (spaCy models ~50 MB, DistilBERT ~250 MB).  
   - Periodically re-annotate new edge cases and retrain every 2–3 months.  

---

## 2. Data-Volume Guidelines

| Phase              | Approx. Rows | Span-tags per field | Total span-tags | Expected Accuracy |
|--------------------|-------------:|--------------------:|---------------:|------------------:|
| **Prototype**      |        500   |  ≥50                |      ~400      |   ~75 % F1        |
| **Production-Lite**|      1,000   | ≥100                |    ~800–1,000  |   ~85 % F1        |
| **Robust Model**   |      2,000+  | ≥200                |    ~1,600+     |   ~90 %+ F1       |

> *Tip:* focus extra annotation on the trickiest fields (e.g. mixed-text “Category” or “Country” mentions).

---

## 3. Project Outline

| Milestone                     | Tasks                                                                 | Deliverables                            | ETA         |
|-------------------------------|-----------------------------------------------------------------------|-----------------------------------------|-------------|
| **1. Kick-off & Design**      | • Finalize fields & annotation spec<br>• Select tech (spaCy vs HF)    | Project spec doc                        | Week 1      |
| **2. Data Collection**        | • Export 500 raw rows from Instant Scraper<br>• Set up Label Studio    | Annotated seed dataset (JSON/CSV)       | Week 2      |
| **3. Prototype Model**        | • Train initial NER model<br>• Quick-eval on small dev set             | Prototype model + evaluation report     | Week 3      |
| **4. Enhanced Annotation**    | • Add 500 more rows focusing on errors<br>• Re-train                   | Expanded dataset + refined model        | Week 5      |
| **5. Integration & Testing**  | • Wrap into your CSV-processor script<br>• End-to-end tests           | CLI/GUI script, test logs               | Week 6      |
| **6. Deployment & Handover**  | • Documentation (setup, retraining steps)<br>• Code repository         | User guide, Git repo URL                | Week 7      |
| **7. Ongoing Maintenance**    | • Quarterly data review<br>• Retrain with new edge-cases               | Maintenance plan                        | Month 3+    |

---

With this roadmap you’ll have a **fully local, small-footprint AI** that reliably structures your scraped data—scaling from an MVP to a production-grade pipeline. Let me know if you’d like more detail on any specific step!
