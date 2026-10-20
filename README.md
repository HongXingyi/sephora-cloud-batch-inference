# Sephora Poor Review Classification — Cloud Deployment

## 1. Project Overview

This project deploys a trained machine learning model to identify
Sephora skincare reviews likely to receive poor ratings (1–2 stars).
The deployment supports batch classification of customer feedback
for subsequent review and investigation.

## 2. Deployment Strategy

Inference mode: Batch Inference
Cloud platform: Databricks Free Edition
Compute: Serverless
Execution: Databricks Jobs (manually triggered)

Batch inference was selected because customer reviews can be
processed collectively without requiring immediate predictions
for individual submissions.

## 3. Model

Model: Linear Support Vector Machine (Linear SVM)

Features:
- Product attributes (numeric and categorical)
- Review text represented using TF-IDF

Model artifact: product_tfidf_LinearSVM.joblib

The saved artifact contains the preprocessing pipeline,
trained classifier, feature definitions and decision threshold.

Classification:
- 1 = Predicted poor feedback
- 0 = Predicted non-poor feedback

The model uses SVM decision scores rather than probabilities.

## 4. Deployment Architecture

1. Load the batch input CSV from Databricks Workspace.
2. Load the saved model artifact using joblib.
3. Validate the required input features.
4. Apply the saved preprocessing pipeline.
5. Generate decision scores using Linear SVM.
6. Apply the stored classification threshold.
7. Compare cloud predictions with local reference predictions.
8. Save the prediction results as a CSV file.

The workflow runs on Databricks Serverless compute.

## 5. Environment

Python: 3.12.3

Dependencies are recorded in requirements.txt.

The deployed model uses scikit-learn 1.5.1,
matching the version used to serialize the model.

## 6. Files

- Sephora_Final_Deployment: Main Databricks notebook
- product_tfidf_LinearSVM.joblib: Saved model pipeline
- sephora_batch_input_fixed.csv: Batch input data
- sephora_expected_predictions.csv: Local reference predictions
- sephora_final_predictions.csv: Cloud prediction output
- requirements.txt: Python dependencies

## 7. How to Run

1. Open the Databricks workspace.
2. Ensure the model and input files are available.
3. Open the Sephora_Final_Deployment notebook.
4. Confirm the notebook environment includes scikit-learn 1.5.1.
5. Run the notebook, or trigger its associated Databricks Job
   using the Run now button.
6. Inspect the execution results and generated prediction CSV.

## 8. Deployment Validation

The final cloud batch inference processed 100 held-out reviews.

Results:
- Total reviews: 100
- Predicted poor reviews: 9
- Predicted non-poor reviews: 91
- Cloud vs local label agreement: 100%
- Maximum absolute decision-score difference: 0.001915
- Mean absolute decision-score difference: 0.0000766
- Records with score difference greater than 0.01: 0

The Databricks Job completed successfully.

Label agreement measures consistency between cloud and local
predictions; it does not represent classification accuracy.

## 9. Limitations and Future Improvements

This deployment is a course-project prototype rather than
a production-ready service.

The demonstration uses a fixed batch of 100 held-out reviews.
The input required GB18030 decoding and correction of a known
brand-name encoding issue. A production workflow should use
consistent UTF-8 data transfer and automated data-quality checks.

Future improvements include automated scheduling, centralized
model versioning, data-drift monitoring, performance monitoring,
and alerting.

## 10. Business Value

Batch classification helps customer experience teams identify
potentially dissatisfied customers and prioritize feedback
for further investigation.

Predictions are intended to support human review rather than
automatically determine product quality or corrective actions.
