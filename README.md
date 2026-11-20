# RiskShield_Booking

```markdown
# Hotel Revenue Protection Engine 🏨💰

An end-to-end Machine Learning system designed to optimize hotel pricing strategy and proactively manage cancellation-associated revenue risks. 

This project features a **dual-model ML system** that predicts: 
1. **Fair Room Pricing (Regression)**: Predicts the Average Daily Rate (ADR) using **XGBoost**.
2. **Cancellation Risk Probability (Classification)**: Assesses booking reliability using a **Random Forest** classifier.

Combined, these models power a **Live Business Intelligence Layer** that calculates key metrics like *Expected Revenue*, *Expected Revenue at Risk*, and *Secured Revenue* for any booking at the point of creation.

---

## 🚀 Project Overview & Business Value

Hotels struggle with two major revenue management issues:
* **Inconsistent Pricing:** Pricing rooms too high leads to empty rooms; pricing too low leaves money on the table.
* **Unmitigated Cancellation Loss:** Cancellations cause last-minute inventory gaps. Knowing cancellation risk upfront allows hotels to target high-risk bookings with customized deposit policies or overbooking strategies.

### Business Metric Output Demo:
```text
=== LIVE BOOKING ANALYSIS ===
Hotel: City Hotel | Lead Time: 120 days | Stay: 3 nights for 2 guests
----------------------------------------------
Predicted Fair ADR (Price/Night):  €97.16
Predicted Cancellation Risk:        38.0%
----------------------------------------------
Max Contract Value (Revenue):       €291.49
Expected Revenue at Risk:           €110.74
Expected Secured Revenue:           €180.75
```

---

## 🛠️ System Design & Architecture

### 1. Data Integrity & Leakage Prevention
* **Feature Minimization**: Scaled down to **exactly 8 high-impact features** available strictly at booking creation. No post-booking data (like `reservation_status` or post-arrival adjustments) is used to avoid target leakage.
* **Temporal Data Splitting**: Avoided standard random splits which leak future behavior into the past. Instead, the model trains on data from **2015–2016** and evaluates on unseen future data (**2017**).

### 2. Feature Engineering & Preprocessing Pipeline
Using `scikit-learn` `ColumnTransformer` and `Pipeline` architectures to ensure zero data leakage during cross-validation:
* **Numerical Features** (Passthrough/Scaled): `lead_time`, `nights`, `people`, `is_repeated_guest`.
* **Categorical Features** (One-Hot Encoded): `hotel`, `arrival_date_month`, `deposit_type`, `customer_type`.

### 3. Machine Learning Pipelines
* **Model 1: XGBoost Regressor (ADR € Predictor)**
  * **Metric:** Mean Absolute Error (MAE) & $R^2$ Score.
  * **Performance on Unseen 2017 Data:** $R^2 = 0.538$, $MAE = \text{€}25.26$.
* **Model 2: Random Forest Classifier (Cancellation Risk % Predictor)**
  * **Metric:** Area Under ROC (ROC-AUC) & F1-Score.
  * **Performance on Unseen 2017 Data:** $ROC\text{-}AUC = 0.770$.

---

## 📂 Repository Structure

```bash
├── data/
│   └── hotels.csv               # Dataset with 119,390 hotel booking records
├── notebooks/
│   └── revenue_engine.ipynb     # Complete execution notebook with pipeline steps
├── README.md                    # Project Documentation
└── requirements.txt             # Dependency requirements file
```

---

## 💻 Installation & Usage

1. **Clone the repository**:
   ```bash
   git clone https://github.com/your-username/hotel-revenue-protection-engine.git
   cd hotel-revenue-protection-engine
   ```

2. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

3. **Run the Live Booking Tracker**:
   ```python
   # From within your script or notebook:
   from engine import check_booking
   check_booking(hotel='City Hotel', lead_time=45, month='October', nights=4, people=2)
   ```

---

## 🎯 Key Takeaways for Interviewers
* **Production-Grade Pipelines:** Fully wrapped preprocessing steps within scikit-learn pipelines, ensuring clean, repeatable inference APIs.
* **Leakage-Free Validation:** Strict temporal validation split guarantees the models are ready for deployment against true prospective hotel traffic.
* **Concrete Business Impact:** Translates raw statistical predictions ($R^2$, $AUC$) directly into actionable monetary impact metrics (€ expected losses and secured revenues).
```
