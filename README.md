# it_monitoring_anomaly_detection

## Overview:
This project focuses on detecting anomalies in server performance metrics to identify potential incidents before they escalate. The dataset simulates 5-minute interval logs from servers, containing metrics such as CPU usage, memory usage, network I/O.

The goal is to build an AI-based pipeline that flags anomalies using two different models, alongside manual threshold-based detection, to compare model learning with rule-based thresholds.

## Dataset Description:

Columns- Column	Description:

timestamp- Date and time of metric collection
server_id- Unique server identifier
cpu_usage (%)-	CPU utilization per process
memory_usage (%)-	Memory utilization per process
network_in- Incoming network traffic (bytes)
network_out-	Outgoing network traffic (bytes)
Frequency: every 5 minutes per server
Period Covered: ~15 days
Number of records: 100,800

## Manually set Anomaly Types:

High CPU usage (>90%) – Sustained spikes (average over 25 mins)

Memory Leak Trend – Gradual increase over time (10% increase over 30 minutes)

Network Data Spikes – Sudden increase in network traffic (over 10,000 bytes)

 ## Workflow
1. Data Preprocessing

Loaded dataset using pandas and converted timestamp to datetime.

Checked and handled missing or irregular timestamps.

Resampled data at 5-minute intervals per server.

Normalized metrics using StandardScaler for model training.

2. Manual Rule-Based Detection

Defined threshold-based anomalies:

CPU anomaly: sustained usage > 90% for 5 consecutive intervals

Memory anomaly: >10% increase over 30 minutes

Network anomaly: traffic spike >10,000 bytes

Each condition sets a Boolean anomaly flag.

3. Model 1 – Isolation Forest

Algorithm: IsolationForest (unsupervised anomaly detector)

Features: CPU, memory, network_in, network_out

Contamination: 0.04 (approx. 4% expected anomalies)

Outputs anomaly label: -1 = anomaly, 1 = normal

4. Model 2 – One-Class SVM

Algorithm: OneClassSVM (kernel-based anomaly detector)

Kernel: RBF

Gamma: auto

Nu: 0.04 (approx. proportion of anomalies)

Outputs anomaly label: -1 = anomaly, 1 = normal

5. Combined Detection

All anomaly flags (manual + model outputs) were merged:

final_anomaly = manual OR model-based anomalies


This approach improves overall recall, since real incidents may be caught by either rule-based logic or ML models.

6. Visualization

Visual plots generated using matplotlib:

Time-series plots for each metric per server

Anomalies highlighted in red/orange/yellow

Separate plots for manual, Isolation Forest, and One-Class SVM anomalies

## Why These Models Were Chosen

### Isolation Forest:
Chosen for its efficiency and interpretability in high-dimensional, unsupervised anomaly detection tasks. It isolates anomalies by randomly partitioning data — making it ideal for server metrics where anomalies are rare and randomly distributed.

### One-Class SVM:
Selected because it can capture nonlinear anomaly boundaries using kernel functions. It’s more sensitive to subtle deviations in patterns, complementing the Isolation Forest’s broader detection.

Together, they provide both statistical isolation and boundary-based learning, giving a balanced view of anomalies.

## Evaluation
Performance Metrics (vs manually set ground truth)
Model	Precision	Recall	F1-Score	Accuracy
Isolation Forest	0.23	0.23	0.23	0.94
One-Class SVM	0.20	0.20	0.20	0.94

## Observations:

Both models detect anomalies close in count to manually defined anomalies (≈ 800 anomalies).

However, precision and recall are low since model anomalies differ from rule-based “ground truth.”

Indicates statistical learning captures different anomaly patterns unseen in threshold-based methods.

Combining manual + model anomalies improves reliability and F1-score.

## Key Insights

Isolation Forest and One-Class SVM detect contextual anomalies (based on correlations), not just threshold breaches.

Manual thresholds detect only known conditions (e.g., CPU>90%).

Combining both methods creates a hybrid anomaly detection framework.

Visualization highlights overlaps and differences between manual and model-predicted anomalies.

## How to Run

### Requirements

Install dependencies:

pip install pandas numpy matplotlib scikit-learn

### Steps to Execute

Place your dataset file as it_monitoring_dummy_dataset.csv in the working directory.

Run the notebook or script:

python anomaly_detection.py


The script:

Loads and preprocesses data

Runs both models

Generates anomaly detection plots

Prints evaluation reports to console



### Outputs

Classification reports for both models

Visual plots for CPU, memory, and network anomalies

Comparison of true vs. model-predicted anomalies

## Conclusion

This project demonstrates both rule-based and AI-driven anomaly detection for server performance monitoring. While manual thresholds provide clear rules, machine learning models uncover deeper, unseen patterns—together forming a robust, explainable IT monitoring system.
