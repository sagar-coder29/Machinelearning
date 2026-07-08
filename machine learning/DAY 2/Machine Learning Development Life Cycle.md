# Machine Learning Development Life Cycle | MLDLC

---

## What is MLDLC?

**MLDLC (Machine Learning Development Life Cycle)** is the step-by-step process of taking an ML project from **idea to production**. It is also called the **Data Science Life Cycle**.

```mermaid
graph LR
    A["Idea / Problem"] --> B["MLDLC Process"]
    B --> C["Deployed Model"]
```

---

## The 9 Steps of MLDLC

```mermaid
graph TD
    S1["1️⃣ Frame the Problem"] --> S2["2️⃣ Gather Data"]
    S2 --> S3["3️⃣ Data Preprocessing"]
    S3 --> S4["4️⃣ Exploratory Data Analysis"]
    S4 --> S5["5️⃣ Feature Engineering"]
    S5 --> S6["6️⃣ Model Training"]
    S6 --> S7["7️⃣ Model Evaluation"]
    S7 --> S8["8️⃣ Model Deployment"]
    S8 --> S9["9️⃣ Monitor & Optimize"]
    
    S9 -.->|"Feedback Loop"| S2
```

---

## Step 1: Frame the Problem

**Goal:** Clearly define what we want to achieve.

| Question | Why It Matters |
|----------|---------------|
| What is the business problem? | Defines project direction |
| Who are the stakeholders? | Knows who needs the results |
| What type of ML problem? | Supervised / Unsupervised / RL? |
| What is the success metric? | How do we measure "good"? |
| What data is needed? | Plan data collection |
| What is the budget/timeline? | Resource planning |
| How will it be deployed? | Batch / Online? |

**Examples:**
- ❌ Bad: "Let's use ML on our data"
- ✅ Good: "We want to predict which customers will churn next month so we can offer them discounts"

---

## Step 2: Gather Data

**Goal:** Collect data from all relevant sources.

### Data Sources

```mermaid
graph LR
    DS["Data Sources"] --> CSV["CSV / Excel Files"]
    DS --> DB["Databases (SQL)"]
    DS --> API["APIs"]
    DS --> WS["Web Scraping"]
    DS --> SS["Sensors / IoT"]
    DS --> LOG["Log Files"]
```

| Source | Example |
|--------|---------|
| **CSV Files** | Kaggle datasets, company exports |
| **Databases** | Customer data from SQL database |
| **APIs** | Twitter API, weather API |
| **Web Scraping** | Scraping product prices from websites |
| **Sensors** | Temperature, pressure sensors |
| **Logs** | Server logs, user activity logs |

---

## Step 3: Data Preprocessing

**Goal:** Clean and prepare raw data for analysis.

### Common Tasks

```mermaid
graph TD
    DP["Data Preprocessing"] --> MV["Handle Missing Values"]
    DP --> OL["Handle Outliers"]
    DP --> SC["Feature Scaling"]
    DP --> EN["Encoding Categorical Data"]
    DP --> SPL["Train/Test Split"]
    
    MV --> MV1["Remove rows / Impute"]
    OL --> OL1["Cap values / Remove"]
    SC --> SC1["Standardization / Normalization"]
    EN --> EN1["One-Hot / Label Encoding"]
    SPL --> SPL1["70-80% Train, 20-30% Test"]
```

| Task | Description |
|------|-------------|
| **Missing Values** | Fill with mean/median/mode or drop rows |
| **Outliers** | Detect and handle extreme values |
| **Feature Scaling** | Bring all features to similar range |
| **Encoding** | Convert categories to numbers |
| **Train/Test Split** | Separate data for training and evaluation |

---

## Step 4: Exploratory Data Analysis (EDA)

**Goal:** Understand the data through visualizations and statistics.

### What We Look For

```mermaid
graph LR
    EDA["EDA"] --> DIST["Distribution of Data"]
    EDA --> CORR["Correlations"]
    EDA --> PAT["Patterns & Trends"]
    EDA --> BIAS["Biases & Anomalies"]
```

| Technique | Purpose |
|-----------|---------|
| **Histograms** | See distribution of each feature |
| **Box Plots** | Detect outliers |
| **Correlation Matrix** | Find relationships between features |
| **Pair Plots** | Visualize feature interactions |
| **Missing Value Maps** | See where data is missing |

---

## Step 5: Feature Engineering & Selection

**Goal:** Create better features and select the most important ones.

### Feature Engineering
- Create new features from existing ones
- Example: From "date" → extract "day of week", "month", "is_weekend"
- Example: From "height" and "width" → create "area"

### Feature Selection

```mermaid
graph TD
    FS["Feature Selection"] --> F1["Remove Irrelevant Features"]
    FS --> F2["Remove Redundant Features"]
    FS --> F3["Keep Only Important Features"]
    
    F1 -.-> R1["Reduces noise"]
    F2 -.-> R2["Reduces computation"]
    F3 -.-> R3["Improves accuracy"]
```

| Method | Description |
|--------|-------------|
| **Filter Methods** | Correlation, Chi-square test |
| **Wrapper Methods** | Forward/Backward selection |
| **Embedded Methods** | Lasso, Decision Tree importance |

---

## Step 6: Model Training

**Goal:** Train ML models on the prepared data.

### Activities
- Choose candidate algorithms (Linear Regression, Random Forest, SVM, etc.)
- Train models on training data
- Tune hyperparameters

```mermaid
graph LR
    TD["Training Data"] --> M1["Model A<br/>(Linear Regression)"]
    TD --> M2["Model B<br/>(Random Forest)"]
    TD --> M3["Model C<br/>(SVM)"]
    
    M1 --> E1["Evaluate"]
    M2 --> E2["Evaluate"]
    M3 --> E3["Evaluate"]
```

---

## Step 7: Model Evaluation

**Goal:** Check how well the model performs on unseen data.

### Evaluation Metrics

| Problem Type | Metrics |
|-------------|---------|
| **Regression** | MAE, MSE, RMSE, R² Score |
| **Classification** | Accuracy, Precision, Recall, F1-Score, ROC-AUC |
| **Clustering** | Silhouette Score, Inertia |

### Techniques
- **Cross-Validation** — more reliable evaluation
- **Test Set** — final check on unseen data
- **A/B Testing** — compare with existing solution

---

## Step 8: Model Deployment

**Goal:** Put the model into production.

```mermaid
graph LR
    Model["Trained Model"] --> API["Create API<br/>(Flask / FastAPI)"]
    Model --> App["Integrate into App"]
    Model --> Cloud["Deploy to Cloud<br/>(AWS / GCP / Azure)"]
```

| Deployment Type | Description |
|----------------|-------------|
| **Batch Prediction** | Run model periodically on new data |
| **Real-time API** | Model serves predictions on-demand |
| **Embedded** | Model runs on device (mobile, IoT) |

---

## Step 9: Monitor & Optimize

**Goal:** Track model performance and improve over time.

### Monitoring
- **Data Drift** — Is input data changing?
- **Concept Drift** — Is the relationship changing?
- **Performance Metrics** — Is accuracy dropping?
- **System Health** — Is the API responding fast enough?

### Optimization
- Retrain model with new data
- Try better algorithms
- Improve features
- Optimize infrastructure

---

## Complete MLDLC Flow

```mermaid
graph TD
    P["1️⃣ Frame Problem"] --> D["2️⃣ Gather Data"]
    D --> PP["3️⃣ Preprocess Data"]
    PP --> E["4️⃣ Exploratory Data Analysis"]
    E --> FE["5️⃣ Feature Engineering"]
    FE --> MT["6️⃣ Model Training"]
    MT --> ME["7️⃣ Model Evaluation"]
    
    ME -->|"❌ Not Good Enough"| FE
    ME -->|"✅ Good"| DEP["8️⃣ Deploy"]
    
    DEP --> MON["9️⃣ Monitor & Optimize"]
    MON -->|"Issue Detected"| D
```

---

## Analogy: Building a House 🏠

| MLDLC Step | House Building Analogy |
|------------|----------------------|
| Frame Problem | Decide what kind of house to build |
| Gather Data | Buy bricks, cement, wood |
| Preprocess | Clean bricks, cut wood to size |
| EDA | Check if materials are good quality |
| Feature Engineering | Design windows, doors layout |
| Model Training | Build the house |
| Model Evaluation | Inspect if house is safe |
| Deployment | Move into the house |
| Monitor | Maintain and repair over time |

---

## Summary

```
1️⃣ Frame Problem     → What to build?
2️⃣ Gather Data       → Collect raw materials
3️⃣ Preprocess        → Clean & prepare
4️⃣ EDA               → Understand data
5️⃣ Feature Engineering → Create better features
6️⃣ Model Training    → Train algorithms
7️⃣ Evaluation        → Check performance
8️⃣ Deployment        → Put in production
9️⃣ Monitor           → Maintain & improve
```

---

*Based on CampusX video: "Machine Learning Development Life Cycle | MLDLC in Data Science"*
