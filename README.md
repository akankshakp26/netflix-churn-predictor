# 📺 Netflix Customer Churn Prediction
<img width="1892" height="910" alt="Screenshot 2026-05-28 121408" src="https://github.com/user-attachments/assets/a968b6d7-59f5-4503-b03b-9feea6bf69b2" />

A full-stack **Django web application** that predicts Netflix customer churn using machine learning. The app walks through the complete ML pipeline — from raw data ingestion and preprocessing to model training, prediction, and detailed evaluation — entirely through a browser-based UI.

---

## 🚀 Demo Flow

```
Home → Data Upload & Preprocessing → Model Training → Prediction → Evaluation
```

---

<img width="1909" height="910" alt="Screenshot 2026-05-28 121418" src="https://github.com/user-attachments/assets/000d8987-3508-4920-8b16-1d08bf1f7b69" />
<img width="1900" height="913" alt="Screenshot 2026-05-28 121428" src="https://github.com/user-attachments/assets/ca93d184-1147-422a-8aeb-aba0f220e562" />
<img width="1908" height="918" alt="Screenshot 2026-05-28 121436" src="https://github.com/user-attachments/assets/1834f123-7565-4ea4-a6b9-9fd2e888edc1" />


<img width="1912" height="913" alt="Screenshot 2026-05-28 121444" src="https://github.com/user-attachments/assets/95bb3718-302a-4b95-a3af-de19a7a94200" />

## 🧠 What It Does

| Step | Route | Description |
|------|-------|-------------|
| Home | `/` | Landing page with project overview |
| Data Upload | `/dataupload` | Loads CSV, drops nulls, encodes categoricals, scales features, splits data |
| Model Training | `/modeltraining` | Trains an XGBoost classifier and serialises model artifacts |
| Prediction | `/prediction` | Runs inference on the test set, reports accuracy |
| Evaluation | `/evaluation` | Full metrics — precision, recall, F1, confusion matrix, classification report |

---

## 📁 Project Structure

```
netflix/
├── manage.py                        # Django management entrypoint
├── netflix_customer_churn.csv       # Dataset (5,000 customer records, 14 features)
│
├── churn_model.pkl                  # Serialised XGBoost model
├── churn_scaler.pkl                 # Fitted StandardScaler
├── churn_encoders.pkl               # Fitted LabelEncoders (per categorical column)
├── feature_columns.pkl              # Ordered feature list for inference
├── y_pred.pkl                       # Cached predictions
├── y_test.pkl                       # Cached ground-truth labels
│
├── netflix/                         # Django project config
│   ├── settings.py                  # App settings (Django 4.2)
│   ├── urls.py                      # Root URL dispatcher
│   ├── asgi.py / wsgi.py            # Deployment entrypoints
│   └── __init__.py
│
└── myapp/                           # Core application
    ├── views.py                     # All ML logic + view handlers
    ├── urls.py                      # App-level URL patterns
    ├── models.py                    # Django ORM models
    ├── admin.py                     # Admin site registration
    ├── apps.py                      # App config
    ├── tests.py                     # Test stubs
    ├── migrations/                  # DB migration files
    └── templates/
        └── myapp/
            ├── index.html           # Landing page
            ├── dataupload.html      # Data preview & stats
            ├── modeltraining.html   # Training parameters display
            ├── prediction.html      # Accuracy result
            └── evaluation.html     # Full evaluation dashboard
```

---

## 📊 Dataset

**`netflix_customer_churn.csv`** — 5,000 rows × 14 columns

| Feature | Type | Description |
|---------|------|-------------|
| `customer_id` | String | Unique identifier (dropped before training) |
| `age` | Integer | Customer age |
| `gender` | Categorical | Female / Male / Other |
| `subscription_type` | Categorical | Basic / Standard / Premium |
| `watch_hours` | Float | Total hours watched |
| `last_login_days` | Integer | Days since last login |
| `region` | Categorical | Africa / Asia / Europe / Oceania / etc. |
| `device` | Categorical | TV / Mobile / Tablet / etc. |
| `monthly_fee` | Float | Monthly subscription cost (USD) |
| `payment_method` | Categorical | Gift Card / Crypto / etc. |
| `number_of_profiles` | Integer | Profiles on the account |
| `avg_watch_time_per_day` | Float | Average daily watch time (hours) |
| `favorite_genre` | Categorical | Action / Drama / Sci-Fi / Horror / etc. |
| `churned` ⭐ | Binary (0/1) | **Target variable** |

---

## 🤖 ML Pipeline

### 1. Preprocessing (`/dataupload`)
- Drops rows with missing values
- Auto-detects and removes ID columns (`customer_id`, `Customer_ID`, etc.)
- Auto-detects target column from a priority list (`churned`, `Churn`, `Exited`, …)
- Encodes all categorical columns with `LabelEncoder`
- Scales all features with `StandardScaler`
- 80/20 stratified train/test split (`random_state=42`)

### 2. Model (`/modeltraining`)
```python
XGBClassifier(
    n_estimators     = 500,
    learning_rate    = 0.05,
    max_depth        = 8,
    subsample        = 0.8,
    colsample_bytree = 0.8,
    random_state     = 42
)
```
Trained artifacts are persisted to disk as `.pkl` files for reuse across requests.

### 3. Evaluation (`/evaluation`)
Reports accuracy, precision, recall, F1-score, confusion matrix, and a full `classification_report`.

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|-----------|
| Web Framework | Django 4.2 |
| ML Model | XGBoost |
| Data Processing | Pandas, NumPy |
| Preprocessing | scikit-learn (`LabelEncoder`, `StandardScaler`, `train_test_split`) |
| Metrics | scikit-learn (`accuracy_score`, `precision_score`, `f1_score`, `confusion_matrix`) |
| Database | SQLite3 (`db.sqlite3`) |
| Templating | Django Templates (HTML5) |
| Serialisation | Python `pickle` |
| Python Version | 3.8 |

---

## ⚙️ Getting Started

### Prerequisites
```bash
python >= 3.8
pip
```

### Installation
```bash
# 1. Clone the repository
git clone https://github.com/<your-username>/netflix-churn-prediction.git
cd netflix-churn-prediction

# 2. Create and activate a virtual environment
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

# 3. Install dependencies
pip install django xgboost scikit-learn pandas numpy

# 4. Apply migrations
python manage.py migrate

# 5. Run the development server
python manage.py runserver
```

### Usage
Open `http://127.0.0.1:8000/` and follow the on-screen steps:

1. **Home** — project introduction
2. **Data Upload** → loads and preprocesses `netflix_customer_churn.csv`
3. **Model Training** → trains XGBoost and saves `.pkl` artifacts
4. **Prediction** → test-set accuracy
5. **Evaluation** → full metrics dashboard

> ⚠️ Steps must be followed in order — each view depends on global state set by the previous one.

---

## 📌 Known Limitations & Improvement Ideas

- **Global state** — model and data are stored in Python module-level globals; not suitable for multi-worker or multi-user deployments. Migrate state to the database or a cache layer (Redis).
- **Hard-coded CSV path** — `pd.read_csv("netflix_customer_churn.csv")` uses a relative path; add a file upload form for flexibility.
- **No hyperparameter tuning UI** — XGBoost params are hard-coded; expose them as form inputs.
- **Secret key** — the Django `SECRET_KEY` in `settings.py` should be moved to an environment variable before any deployment.
- **Add `requirements.txt`** — pin exact dependency versions for reproducibility.

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).
