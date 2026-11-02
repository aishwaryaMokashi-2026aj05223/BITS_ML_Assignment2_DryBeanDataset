# Phishing Website Detection

## a. Problem statement
Classify a website as **phishing** or **legitimate** from 30 features extracted from its URL, page source and domain/third-party information (for example IP address in the URL, SSL state, URL length, anchor behaviour, domain age, web traffic and Google index status). Detecting phishing sites automatically helps browsers, mail filters and security teams block credential-theft attacks. Five machine learning classifiers are trained on the same dataset and compared using six evaluation metrics, and the results are explored in an interactive Streamlit app.

## b. Dataset description
- **Source:** Phishing Websites Dataset, UCI Machine Learning Repository (id 327; Mohammad, Thabtah & McCluskey, 2012), https://archive.ics.uci.edu/dataset/327/phishing+websites
- **Task:** binary classification
- **Instances:** 11,055 raw rows; <N> after removing exact duplicates (printed by `train.py`)
- **Features:** 30 integer features, every value in {-1, 0, 1} (1 = legitimate, 0 = suspicious, -1 = phishing indicator). Groups:
  - *Address bar:* having_IP_Address, URL_Length, Shortining_Service, having_At_Symbol, double_slash_redirecting, Prefix_Suffix, having_Sub_Domain, SSLfinal_State, Domain_registeration_length, Favicon, port, HTTPS_token
  - *Abnormal / page content:* Request_URL, URL_of_Anchor, Links_in_tags, SFH, Submitting_to_email, Abnormal_URL
  - *HTML & JavaScript:* Redirect, on_mouseover, RightClick, popUpWidnow, Iframe
  - *Domain:* age_of_domain, DNSRecord, web_traffic, Page_Rank, Google_Index, Links_pointing_to_page, Statistical_report
- **Target:** `Result` (`-1` = phishing, `1` = legitimate). Phishing is encoded as the positive class (1) internally.
- **Preprocessing:** dropped missing values and duplicate rows (so identical rows cannot appear in both train and test), standardized features with `StandardScaler` (inside each model pipeline), stratified 80/20 train/test split (`random_state=42`).

## c. GitHub repository link
<PASTE YOUR GITHUB REPO URL HERE>

Live Streamlit app: <PASTE YOUR STREAMLIT URL HERE>

## d. Models used
All metrics are computed on the held-out test set (`test_data.csv`). It is a binary problem, so Precision, Recall and F1 are for the positive class (**Phishing**), and AUC is computed from the predicted probability of phishing.

### Comparison table

<PASTE THE TABLE FROM results_table.md (written by train.py) HERE, same format as below>

| ML Model Name | Accuracy | AUC | Precision | Recall | F1 | MCC |
|---|---|---|---|---|---|---|
| Logistic Regression | | | | | | |
| Decision Tree | | | | | | |
| KNN | | | | | | |
| Naive Bayes | | | | | | |
| Support Vector Machine | | | | | | |

### Observations

| ML Model Name | Observation about model performance |
|---|---|
| Logistic Regression | <e.g. linear baseline; how close to the best model; coefficient/feature insight> |
| Decision Tree | <e.g. interpretability, depth limit (max_depth=12), over/under-fitting vs. the others> |
| KNN | <e.g. effect of k=7 and feature scaling on ternary features, any speed trade-off> |
| Naive Bayes | <e.g. Gaussian assumption on {-1,0,1} features, correlated features, AUC vs. accuracy> |
| Support Vector Machine | <e.g. RBF kernel (C=10) capturing non-linear interactions, training cost> |

**Overall winner for this dataset:** <model name and a one-line reason, referring to MCC / F1 / AUC>

## Project structure
```
app.py              Streamlit app (loads saved models, no retraining)
train.py            Trains and saves the five models, writes train/test CSVs and results_table.md
utils.py            Target encoding and metric computation
requirements.txt    Dependencies
test_data.csv       Held-out test data for the app (30 features + Result)
train_data.csv      Training split
model/              Saved .joblib pipelines (scaler + classifier) and metrics_table.csv
```

## How to run
```bash
pip install -r requirements.txt
# put the dataset next to train.py (e.g. "Training Dataset.arff" or phishing.csv),
# or pass a path:  python train.py path/to/file.csv
# with no file present, train.py downloads it from UCI via ucimlrepo
python train.py
streamlit run app.py
```
Then upload `test_data.csv` in the app sidebar, pick a model, and view metrics, the confusion matrix and the classification report. The "All models" tab compares all five on the uploaded file.

**Deployment note:** the `.joblib` files must be loaded with the same scikit-learn version that trained them. After running `train.py`, pin that exact version in `requirements.txt` (check with `python -c "import sklearn; print(sklearn.__version__)"`, e.g. `scikit-learn==1.5.2`) before pushing to GitHub.
