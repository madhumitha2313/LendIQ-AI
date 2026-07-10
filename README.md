                                                                      LendIQ

Smarter Lending Decisions Powered by Explainable AI
A production-grade, enterprise-style FinTech SaaS loan-approval platform. Predicts loan approvals with an XGBoost model, explains every decision with SHAP, authenticates with Firebase, and works end-to-end with zero backend thanks to an in-browser scoring engine.

React + Vite · XGBoost + SHAP · Firebase Auth/Firestore · FastAPI · Recharts

What LendIQ does : 
Premium FinTech UI — dark glassmorphism theme, animated KPIs, gauges, charts, fully responsive. Not your typical dashboard.
Manual & batch predictions — score one applicant or upload an .xlsx/.csv dataset for bulk decisions.
Explainable AI — real, additive SHAP feature attributions with local (per-applicant) and global views, waterfall + contribution charts.
AI Loan Advisor — plain-language reasons + prioritised recommended actions for every decision.
Risk intelligence — 0–100 risk score, LOW/MEDIUM/HIGH bands, animated gauge, portfolio risk.
One-click exports — PDF, Excel and CSV for single predictions and batches, including the SHAP summary and AI recommendation text.
Prediction history — every decision stored with user ownership; search, filter, delete and download.
Analytics suite — approval distribution, trends, income/loan/credit/area distributions, confidence, feature importance.
Two prediction backends, one shape — calls a FastAPI XGBoost+SHAP service when configured, otherwise the in-browser Edge Engine. The app never breaks.

The scoring engine : 
The LendIQ Edge Engine (frontend/src/engine) lets the whole product run with no Python at all. It's a calibrated logistic scorecard in standardised feature space. Because SHAP values for a linear model are exactly wᵢ·(xᵢ − E[xᵢ]), the explanations are a genuine additive decomposition, not a cosmetic mock-up — the per-feature contributions plus the intercept reconstruct the model logit precisely (verified in tests). Credit history, EMI-to-income and loan-to-income dominate, consistent with sound underwriting.

When VITE_API_BASE_URL is set, predictions instead come from the FastAPI service, which serves a real GridSearch-tuned XGBoost model with SHAP TreeExplainer values — and returns the identical response shape.

Architecture : 
LendIQ/
├── frontend/                     # React + Vite SaaS app
│   └── src/
│       ├── engine/               # Edge Engine: preprocessing, scoring (SHAP),
│       │                         #   risk engine, recommendation engine
│       ├── services/             # FastAPI client, Firestore history, exports, file parsing
│       ├── context/              # Auth, Theme, Toast, Prediction providers
│       ├── components/           # Layout, UI primitives, charts, PredictionResult
│       └── pages/                # Splash, Login, Dashboard, Batch, Manual,
│                                 #   ExplainAI, Analytics, History, Reports, Settings
└── backend/                      # FastAPI prediction service (optional)
    ├── pipeline.py               # Feature pipeline mirroring the frontend
    ├── train_model.py            # XGBoost + GridSearchCV training → best_xgb.pkl
    └── main.py                   # /predict, /predict/batch with SHAP + API key

Quick start :

Option A — full stack, one command (UI + real XGBoost backend)
npm run setup     # installs deps, trains the model, writes frontend/.env
npm run dev       # runs FastAPI (:8000) + React (:5173) together
Frontend → http://localhost:5173
API → http://127.0.0.1:8000 (interactive docs at /docs)
The React app is wired to the FastAPI service, so predictions and SHAP values come from the real trained XGBoost model. Sign up with any email.

Option B — frontend only (works on its own — demo auth + Edge Engine)
cd frontend
npm install
npm run dev          # http://localhost:5173
Open the app, sign up with any email (stored locally in demo mode) and explore. Visit Analytics → Generate demo data to populate the dashboards. With no backend configured, predictions use the in-browser Edge Engine.

Option C — zero-setup single-file preview
cd frontend && npm install && npm run build:single
Produces frontend/dist-single/index.html — the entire app inlined into one file. Double-click it to run LendIQ in any browser with no server and no install (uses the Edge Engine, hash-routed).

Optional: real Firebase + FastAPI
cd frontend
cp .env.example .env      # fill in Firebase + VITE_API_BASE_URL / VITE_API_KEY
cd backend
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
python train_model.py     # trains XGBoost → model/best_xgb.pkl
uvicorn main:app --port 8000
See backend/README.md for full API details.

⚙️ Configuration : 
Everything is optional — LendIQ degrades gracefully.

Variable	Effect when set	When unset
VITE_FIREBASE_*	Firebase Auth + Firestore history	Local demo auth + localStorage history
VITE_API_BASE_URL	Predictions via FastAPI/XGBoost	In-browser Edge Engine
VITE_API_KEY	Sent as X-API-Key to the service	—
LENDIQ_API_KEY (backend)	Requires X-API-Key header	Auth disabled
🎨 Design system
Dark glassmorphism, soft shadows, gradient backdrops, smooth animations.

Token	Value : 
Background	#081120
Primary	#2563EB
Secondary	#14B8A6
Success / Danger / Warning	#22C55E / #EF4444 / #F59E0B
Cards	rgba(255,255,255,0.06)

Tech stack : 
. React  
. Vite 
· React Router 
· Firebase 
· Recharts 
· Framer Motion 
· lucide-react 
· jsPDF 
· SheetJS (xlsx) 
· PapaParse 
· FastAPI 
· XGBoost 
· SHAP 
· scikit-learn 
· pandas 
· numpy

Model : 
XGBoost Classifier · reported accuracy 88.62% · 10 engineered features · trained offline with GridSearchCV. The app loads the model — no training in the UI.
