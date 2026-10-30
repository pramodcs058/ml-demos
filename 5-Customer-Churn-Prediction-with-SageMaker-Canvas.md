# Instructor Guide: Customer Churn Prediction with Amazon SageMaker Canvas

## Demo at a glance

- **Service:** Amazon SageMaker AI – SageMaker Canvas (launched from SageMaker Studio)
- **Level:** Beginner to intermediate; no Python coding required
- **Duration:** 45–60 minutes (excluding any AWS account/domain setup delays)
- **ML task:** Binary classification / two-category prediction
- **Business scenario:** Predict whether a telecom customer is likely to churn (`Churn = Yes` or `No`)
- **Training dataset:** `telecom_customer_churn_training.csv` (900 synthetic customer rows)
- **Batch prediction dataset:** `new_customers_for_batch_prediction.csv` (15 unlabeled customer rows)

This is a fully synthetic teaching dataset. It contains no real customer records or personal information. Do not present model metrics from it as production performance.

## Learning outcomes

By the end, learners should be able to:

1. Open SageMaker Canvas from the SageMaker Studio environment.
2. Import and preview a CSV tabular dataset.
3. Identify the target column and distinguish it from input features.
4. Create a two-category predictive model using the visual Canvas workflow.
5. Review classification metrics, the confusion matrix, and feature importance/impact.
6. Run a single prediction and a batch prediction.
7. Explain why model quality, data quality, business cost, and responsible use all matter before deployment.

## Before the class: prerequisites and access

1. An Amazon SageMaker AI domain and user profile must already exist. SageMaker Canvas is launched within that environment.
2. The Studio environment must meet the current SageMaker Canvas requirement (Studio version 3.19.0 or later, as stated in the AWS getting-started guide).
3. The user profile needs **Run Studio Applications** and **Canvas Core Access** permissions. These cover starting Canvas and its core dataset/model-building functions.
4. For **local file upload**, the administrator must enable local file upload in the domain's Canvas configuration. If it is disabled, upload the CSV files to an approved S3 bucket and import them from Amazon S3 instead.
5. Model deployment is **not required** for this exercise. If showing deployment as an optional extension, the user profile needs Canvas MLOps/deployment permissions.
6. Check the AWS Region, account quotas, and any organization-specific restrictions before class.

AWS setup reference: https://docs.aws.amazon.com/sagemaker/latest/dg/canvas-getting-started.html

## Files to use

- `telecom_customer_churn_training.csv` — import this first; it includes the target column `Churn`.
- `new_customers_for_batch_prediction.csv` — import this after the model is ready; it intentionally has no `Churn` column because the model must predict it.

The training file contains 499 `No` and 401 `Yes` examples. There are more than 500 rows, meeting the documented minimum for a Quick build of a two-category prediction model.

## Live demo walkthrough

### Part 1 — Launch Canvas (5 minutes)

1. Sign in to the AWS Management Console and open **Amazon SageMaker AI**.
2. Open **Studio** and choose the configured domain/user profile if prompted.
3. In the Studio navigation, choose **Canvas** / **SageMaker Canvas**, then launch the Canvas application. Depending on the console layout and account configuration, Canvas may also be launched from the SageMaker AI console's Canvas entry.
4. Point out the main Canvas areas: Home, Datasets, My models, and (where enabled) Data preparation, Ready-to-use models, and other integrations.
5. Explain that Canvas is a visual, low-code/no-code ML experience. It manages much of the data preparation and model-building workflow, while the user still makes important data and business decisions.

### Part 2 — Import and inspect the training data (8–10 minutes)

1. In Canvas, open **Datasets** and choose **Import data**.
2. Choose **Tabular**.
3. Name the dataset `telecom_customer_churn_training`.
4. Choose **Local upload** and upload `telecom_customer_churn_training.csv`.
   - If local upload is unavailable, ask the administrator to enable it, or upload the CSV to an approved S3 bucket and select the S3 source instead.
5. Wait for the import to complete, then open the dataset to preview the rows and columns.
6. Explain the key columns:

| Column | Meaning | Role in demo |
|---|---|---|
| `CustomerID` | Synthetic customer key | Identifier; exclude from model features |
| `TenureMonths` | Number of months as a customer | Input feature |
| `ContractType` | Month-to-month, one-year, or two-year plan | Input feature |
| `InternetService` | Fiber, DSL, or no internet | Input feature |
| `MonthlyCharges` | Monthly bill amount | Input feature |
| `TotalCharges` | Approximate cumulative bill amount | Input feature |
| `TechSupport` | Whether support is included | Input feature |
| `OnlineSecurity` | Whether online security is included | Input feature |
| `PaymentMethod` | Customer payment method | Input feature |
| `PaperlessBilling` | Paperless billing enabled | Input feature |
| `AutoPay` | Automatic payment enabled | Input feature |
| `SupportTickets90d` | Recent support-ticket count | Input feature |
| `AvgMonthlyDataGB` | Average monthly data use | Input feature |
| `Churn` | Whether the customer left | **Target to predict** |

7. Ask learners: “Which column should the model predict?” Expected answer: `Churn`.
8. Ask: “Should CustomerID be a predictor?” Expected answer: No. It is an identifier and has no intended predictive meaning. Exclude it from the model build if the interface offers column selection.
9. Check that `Churn` has two labels (`Yes` and `No`) and that numerical and categorical columns have sensible data types.

### Part 3 — Create the predictive model (10–15 minutes)

1. Open **My models** and choose **New model**.
2. Name it `telecom-customer-churn-canvas-demo`.
3. Choose the **Predictive analysis** problem type and create the model.
4. Select the `telecom_customer_churn_training` dataset and choose **Select dataset**.
5. On the **Build** tab, set **Target column** to `Churn`.
6. Confirm that Canvas identifies this as a two-category/categorical prediction problem.
7. Inspect the columns selected for the build. Exclude `CustomerID` from the predictor columns. Keep the other relevant columns.
8. For this class demo, choose **Quick build**. The training data has 900 rows and satisfies the documented 500-row minimum for a Quick build with two-category prediction.
9. Explain that Canvas automates candidate model training and selection. Learners are using a visual AutoML workflow, not manually choosing an algorithm such as logistic regression or random forest.
10. Wait until the model status is **Ready**. The build can take time; if class time is limited, launch the build early and use the wait period to explain the model workflow.

**Quick build vs Standard build:** Quick build is suitable for a guided demonstration. Standard build is a more extensive model-building option and can take longer / consume more resources. Use Standard build only when there is sufficient time and the instructor has checked the relevant account quotas and cost implications.

### Part 4 — Analyze and interpret the model (8–10 minutes)

1. Open the completed model and select **Analyze**.
2. Review the model score/overview and the classification metrics available for the model.
3. Open the confusion matrix, if shown, and explain the four outcomes:
   - True positive: a customer who churned and was predicted as `Yes`.
   - True negative: a customer who stayed and was predicted as `No`.
   - False positive: a customer predicted to churn who actually stayed.
   - False negative: a customer predicted to stay who actually churned.
4. Discuss the metrics:
   - **Accuracy:** Overall proportion of correct predictions.
   - **Precision for Churn = Yes:** Of customers predicted to churn, how many actually churned?
   - **Recall for Churn = Yes:** Of customers who actually churned, how many did the model identify?
   - **F1-score:** Balance between precision and recall.
5. Review feature importance/impact or column impact if available. Ask which factors appear influential in this particular trained model. The exact ranking is data- and build-dependent; do not promise a fixed order.
6. Teaching point: a high accuracy score alone is not enough. In churn prevention, missing a customer who is about to leave (false negative) may have a different business cost from contacting a customer who was not going to leave (false positive).

### Part 5 — Make a single prediction (5 minutes)

1. Open the model's **Predict** tab.
2. Choose **Single prediction**.
3. Try an example with higher-risk characteristics:
   - `TenureMonths`: 3
   - `ContractType`: Month-to-month
   - `InternetService`: Fiber optic
   - `MonthlyCharges`: 129
   - `TotalCharges`: 380
   - `TechSupport`: No
   - `OnlineSecurity`: No
   - `PaymentMethod`: Electronic check
   - `PaperlessBilling`: Yes
   - `AutoPay`: No
   - `SupportTickets90d`: 5
   - `AvgMonthlyDataGB`: 230
4. Choose **Update** / generate the prediction as shown in the UI, and review the predicted class and confidence/probability.
5. Change the values to a lower-risk profile (for example, long tenure, two-year contract, tech support enabled, autopay enabled, and fewer support tickets). Generate the prediction again.
6. Explain that changing input values is a useful way to explore the model, but the output is not a guarantee that a customer will or will not churn.

Note: Single prediction in Canvas uses an asynchronous inference endpoint that is automatically shut down after two hours of inactivity. It may still incur charges while active; keep the session short and review AWS pricing.

### Part 6 — Run a batch prediction (5–8 minutes)

1. Go to **Datasets** → **Import data** → **Tabular**.
2. Name the dataset `new_customers_for_batch_prediction`.
3. Upload `new_customers_for_batch_prediction.csv` (or select it from S3 if using S3-based import).
4. Confirm the prediction dataset has the same input feature columns as the training dataset, but does not include the target `Churn` column.
5. Return to the model's **Predict** tab and choose **Batch prediction**.
6. Choose **Select dataset**, select `new_customers_for_batch_prediction`, and start predictions.
7. When the job completes, preview the output. Show the predicted churn label and confidence/probability alongside the customer input columns.
8. Download the prediction results if desired.

### Part 7 — Wrap up (2 minutes)

Summarize the ML workflow the learners completed:

**Business question → CSV dataset → target selection → model build → model analysis → single prediction → batch prediction.**

Ask learners to identify one way the business could act on a high-risk prediction (for example, proactive support or a service review). Stress that predictions should support, not automatically replace, human decisions.

## Optional extension: deploy the model

Deployment is not necessary for the main demo. If time and permissions allow:

1. Open the model's **Deploy** tab.
2. Create a deployment and review the suggested hosting instance configuration before confirming.
3. Explain that an endpoint allows an application to send inference requests to a hosted model.
4. **Immediately delete the deployment/endpoint after the demonstration.** An active endpoint incurs hosting charges. Deployment requires Canvas MLOps / direct-deployment permissions.

AWS deployment reference: https://docs.aws.amazon.com/sagemaker/latest/dg/canvas-deploy-model.html

## Cost and cleanup checklist

- [ ] Avoid Standard build unless needed; use Quick build for the main demo.
- [ ] Do not deploy a hosted endpoint for the core exercise.
- [ ] If you test single predictions, close the activity and allow the asynchronous inference endpoint to shut down; check the AWS account's resource and billing views.
- [ ] If you performed the optional deployment, delete the deployment/endpoint after the demo.
- [ ] Keep or delete the imported datasets/model according to your training-lab cleanup policy. Persistent Canvas storage/S3 artifacts may remain after the UI session ends.
- [ ] Review AWS pricing for the selected Region and enabled features before a paid workshop.

## Suggested questions for learners

1. What is the target column in this problem, and why?
2. Why should `CustomerID` be excluded from the predictor features?
3. What is the difference between precision and recall for the `Yes` class?
4. Why can a model with high accuracy still miss valuable churn cases?
5. What does a feature-importance score tell us—and what does it not prove?
6. What is the difference between a single prediction and a batch prediction?
7. What would you validate before using this model with real customer data?

## Expected answers / teaching notes

- Target: `Churn`, because the business goal is to predict whether the customer leaves.
- `CustomerID` is a unique identifier, not a meaningful behavioral signal; it can encourage spurious patterns.
- Precision is about the reliability of positive predictions; recall is about the fraction of actual positive cases detected.
- If churn is a minority class, a model can score well on accuracy by mostly predicting the majority class. The confusion matrix and class-specific metrics expose this.
- Feature importance shows model-level association/usefulness in the trained model, not causation.
- Single prediction is one record; batch prediction applies the model to a dataset of records.
- Real-world validation should consider representativeness, drift, class balance, leakage, fairness, privacy, deployment monitoring, and business costs.

## Official AWS references

- Getting started and prerequisites: https://docs.aws.amazon.com/sagemaker/latest/dg/canvas-getting-started.html
- Import a tabular dataset: https://docs.aws.amazon.com/sagemaker/latest/dg/canvas-import-dataset.html
- Build a categorical prediction model: https://docs.aws.amazon.com/sagemaker/latest/dg/canvas-build-model-how-to.html
- Evaluate a model: https://docs.aws.amazon.com/sagemaker/latest/dg/canvas-evaluate-model.html
- Single predictions: https://docs.aws.amazon.com/sagemaker/latest/dg/canvas-make-predictions-single.html
- Batch predictions: https://docs.aws.amazon.com/sagemaker/latest/dg/canvas-make-predictions-batch-manual.html
- Deploy a model: https://docs.aws.amazon.com/sagemaker/latest/dg/canvas-deploy-model.html
