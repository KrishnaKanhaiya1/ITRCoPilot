# ITRCoPilot – Agentic Tax Return Auto-Filer

A multi-agent tax filing engine that processes unstructured tax records, extracts key data, evaluates compliance, and automates filing decisions. Implemented in **Python** using state-machine orchestration logic to run 8 cooperative LLM agents.

---

## 🤖 System Architecture & Agents

`ITRCoPilot` routes filing actions through an orchestration graph (`backend/orchestrator/graph.py`) to manage task handoffs:

```
[User Documents] 
      │
      ▼
1. DocumentClassifier ──► 2. FieldExtraction
                               │
                               ▼
                        3. IncomeAggregator
                               │
                               ▼
4. FormSelector ◄─────── 5. DeductionClaimer
      │
      ▼
6. TaxComputation ──► 7. EVerification ──► [Filing Complete]
                               ▲
                               │
                        8. Supervisor (Re-evaluator)
```

### The 8 Agents:
1. **DocumentClassifier**: Classifies PDFs (Form 16, Bank Statements, Form 26AS, etc.) using zero-shot classification reasoning.
2. **FieldExtraction**: Extract numbers (salary, TDS, 80C, HRA exemptions).
3. **IncomeAggregator**: Aggregates salary and interest fields into standardized schedules.
4. **DeductionClaimer**: Evaluates deduction bounds (e.g., capping 80C to ₹1.5L, calculating HRA).
5. **FormSelector**: Selects appropriate form (ITR-1, ITR-2) based on income sources.
6. **TaxComputation**: Applies tax slab calculations across both old and new regimes to recommend the most optimal choice.
7. **EVerification**: Ensures data completeness and verifies filing criteria.
8. **Supervisor**: Monitors exceptions, handles loop-backs, and orchestrates user input correction when documents are missing or discrepant.

---

## 🛠️ Tech Stack

* **Core**: Python 3.9+
* **Orchestration**: Custom State-Graph Orchestrator (LangGraph-style)
* **Model Integration**: Google GenAI API (Gemini models)
* **Validation**: Pydantic
* **Frontend**: React + Vite (HMR, Tailwind)

---

## ⚙️ How to Setup

### Backend
1. Go to `backend/` directory:
   ```bash
   cd backend
   pip install -r requirements.txt
   ```
2. Configure `.env`:
   ```env
   GEMINI_API_KEY=your_gemini_api_key
   ```
3. Run tests/workflow:
   ```bash
   python test_agentic_final.py
   ```

### Frontend
1. Go to root:
   ```bash
   npm install
   npm run dev
   ```

---

## 💡 Key CS & Software Engineering Learnings

* **State Graphs in Production**: Learned how to design predictable multi-agent systems using structured state graphs, eliminating runaway agent loops.
* **Deterministic Verification**: Integrated Pydantic parsing alongside LLMs to guarantee that generated output conforms to precise API schema constraints.
