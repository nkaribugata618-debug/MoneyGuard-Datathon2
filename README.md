MoneyGuard — AI-Assisted Business Intelligence & Money Leak Detection

VIT-AP DATATHON XI · Track 4: The Money Leak
A business intelligence dashboard that helps teams investigate unusual transactions, understand operational risks, and prioritize where to look for potential revenue leakage.







Table of contents

Overview

Problem

Our solution

Key features

Machine-learning approach

Current model results

Technology stack

Application flow

Project structure

Run locally

Train the model

Deployment

Responsible interpretation

Current limitations and next steps

Team / project information

License

Overview

MoneyGuard combines a web-based business intelligence interface with machine-learning-assisted transaction screening. It is designed to help users move from raw business data to a focused review queue: find unusual records, inspect the supporting transaction details, and decide which cases deserve further investigation.

The project combines business-rule analysis and exploratory analytics with an unsupervised Isolation Forest anomaly-detection model. The model highlights records that look unusual relative to the data it was fitted on. It does not independently prove fraud, waste, or a financial loss.

Prototype transparency: The Money Leak Finder includes a snapshot of exported Colab model results. Some other dashboard charts may still contain illustrative demo data. The CSV uploader currently previews uploaded files; it does not automatically recompute every dashboard KPI or retrain the model.

Problem

Businesses can lose money or miss opportunities through patterns that are difficult to spot in large tables, such as:

Unusual transaction values, quantities, or discounts

Returns and cancellations that warrant further review

Low or inconsistent estimated margins

Inventory levels that may create stockout risk

Campaign spend that may not translate into conversions

Regional or product-category performance differences

Reviewing every record manually is time-consuming. MoneyGuard is intended to help prioritize the investigation—not replace financial controls or human judgement.

Our solution

MoneyGuard provides a single dashboard experience for exploring potential money leaks across transactions, regions, product categories, marketing campaigns, and inventory. Its ML review queue ranks unusual transactions so a reviewer can start with records that stand out most from the dataset.

The intended workflow is:

Load business datasets.

Inspect and prepare the data, checking types, missing values, and relevant joins.

Analyze business measures and identify patterns that merit review.

Score transaction records with Isolation Forest.

Review the highest-ranked records alongside their transaction context.

Validate flagged records against source documents and business rules before deciding on action.

Key features

Business intelligence dashboard built with Next.js and React.

Money Leak Finder with a model summary and a transaction review table.

Anomaly risk ranking to help prioritize unusual records.

Region and category filters for the bundled ML review snapshot.

Regional, category, marketing, and inventory workspaces for exploring business performance.

CSV Data Explorer for inspecting uploaded CSV structure in the browser.

Responsive layout for desktop and smaller screens.

Exportable model artifacts from the training script for reproducibility.

Feature status

Capability

Current status

Next.js dashboard

Implemented

Colab Isolation Forest run

Completed on 18,000 transaction records

ML summary in Money Leak Finder

Integrated as a fixed exported snapshot

Highest-risk flagged-record table

Integrated; the bundled summary contains up to 100 flagged records

Region/category filtering of bundled review records

Implemented in the corrected project version

CSV upload

Preview only

Automatically retrain model when a CSV is uploaded

Not implemented

Live Python inference endpoint

Not implemented

Every dashboard KPI connected to verified source calculations

Still requires integration/verification

Machine-learning approach

Algorithm: Isolation Forest

MoneyGuard uses Isolation Forest, an unsupervised anomaly-detection algorithm available in scikit-learn. It is useful when there is no reliable labelled dataset indicating which transactions are confirmed losses.

The Colab model run used these features:

gross_value

discount_pct

quantity

unit_price

The model produces an anomaly flag and a relative risk ranking. A higher risk score means the record is more unusual relative to the records scored in that run. It is not a calibrated probability of fraud or loss.

Reproduce model training locally

The repository includes ml/train_moneyguard.py. Use a transaction CSV that you are authorized to process.

Requirements: Python 3.10+ is recommended, along with the packages listed in requirements-ml.txt.

python -m pip install -r requirements-ml.txt
python ml/train_moneyguard.py path/to/transactions.csv

The script writes these artifacts to outputs/:

scored_transactions.csv — input records with anomaly flags, relative risk scores, and review-priority labels

model_metrics.json — row count, feature list, anomaly count/rate, and limitations

isolation_forest.joblib — saved fitted pipeline and feature-column metadata

The training script automatically derives a gross transaction value when quantity and unit_price are present, and it derives estimated discount/net values when a discount column is available. These derived values are estimates based on the input fields and assumptions in the script.

Note: Re-running the local training script may not reproduce the exact Colab count because the script's feature selection and model configuration can differ from the Colab experiment. Treat each run's metrics as specific to its data and configuration.

Current model results

The exported Colab run integrated into the Money Leak Finder reported:

Metric

Result

Transactions scored

18,000

Records flagged by Isolation Forest

897

Flag rate

4.98%

Algorithm

Isolation Forest

Features

Gross value, discount percentage, quantity, unit price

These numbers describe the exported model run, not verified financial losses. The interface shows a limited review snapshot, not every flagged row.

Technology stack

Layer

Technology

Frontend framework

Next.js App Router

UI

React + TypeScript

Styling

Tailwind CSS v4

Charts

Recharts

Icons

Lucide React

Machine learning

Python, pandas, NumPy, scikit-learn

Model persistence

joblib

Notebook analysis

Google Colab

Source control

Git + GitHub

Frontend deployment

Vercel

Application flow

Business transaction data
          |
          v
Data inspection and feature preparation
          |
          v
Isolation Forest anomaly detection
          |
          v
Anomaly flags + relative risk scores
          |
          v
Exported model summary and review records
          |
          v
MoneyGuard Next.js dashboard
          |
          v
Human review and validation against business evidence

The deployed frontend currently displays exported model results; it does not call a live Python inference service when a user uploads a new CSV.

Project structure

MoneyGuard-Datathon/
├── src/
│   ├── app/
│   │   ├── globals.css
│   │   ├── layout.tsx
│   │   └── page.tsx
│   └── data/
│       └── ml_summary.json
├── ml/
│   └── train_moneyguard.py
├── outputs/
│   ├── model_metrics.json
│   └── flagged_transactions_review.csv
├── requirements-ml.txt
├── package.json
├── next.config.ts
├── postcss.config.mjs
├── tsconfig.json
└── README.md

Generated files may vary by workflow. Do not commit raw or sensitive business data unless the event's data-sharing rules explicitly permit it.

Run locally

Prerequisites

Node.js LTS

npm

Python 3.10+ only if you plan to retrain the ML model

Install and run the frontend

npm install
npm run dev

Open http://localhost:3000 in your browser.

Verify a production build

npm run build
npm start

The production build should complete without errors before deployment.

Deployment

The frontend is designed for deployment on Vercel.

Push the project to a GitHub repository.

In Vercel, choose Add New → Project and import the repository.

Keep the detected Next.js framework and default build settings unless your project requires otherwise.

Deploy and test the resulting URL.

After pushing changes to the connected production branch, check the Vercel deployment status and smoke-test the live site.

The current frontend can display its bundled model snapshot without a Python server. If live scoring of newly uploaded transactions is added later, deploy the inference code as a separate Python API/service or another compatible backend and connect the frontend to it.

Responsible interpretation

MoneyGuard is a decision-support prototype. It should not be used as the sole basis for accusing a person, blocking a payment, or declaring fraud.

An anomaly is an unusual pattern, not proof of wrongdoing.

A relative risk score is not a probability.

A flagged transaction is not automatically a money leak.

Estimated margin or net value depends on the source columns and calculation assumptions.

Findings should be traced to source rows and validated against business evidence.

Review thresholds should be tested against domain knowledge and, when available, labelled historical outcomes.

Current limitations and next steps

Known limitations

The deployed ML review queue is based on a fixed export from Colab.

The dashboard's CSV uploader currently previews files rather than updating all analytics.

Some non-ML analytics charts may remain illustrative placeholders until connected to verified calculation exports.

No live inference API is currently connected to the deployed frontend.

The anomaly model is unsupervised and has not been evaluated against a labelled set of confirmed money-leak cases.

Planned improvements

Connect each analytics workspace to verified Colab calculation outputs.

Add dataset/schema validation and clear upload feedback.

Connect CSV upload to a controlled scoring workflow.

Add evidence details and explainable reasons for each flagged record.

Validate thresholds with business reviewers and labelled historical data if available.

Add automated tests for filters, calculations, and model-output parsing.

Add a backend inference service if real-time scoring is required.

Team / project information

Project: MoneyGuard
Event: VIT-AP DATATHON XI
Track: The Money Leak

Built as a datathon prototype to demonstrate how business analytics and unsupervised machine learning can help prioritize investigation of unusual transaction patterns.

License

See LICENSE for the project's license terms.# MoneyGuard-Datathon2
