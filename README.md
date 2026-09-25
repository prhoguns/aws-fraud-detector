# Amazon Fraud Detector registration example

This repository reconstructs [How I Created a fraud detection system on AWS](https://rhoguns.hashnode.dev/how-i-created-a-fraud-detection-system-on-aws), published **March 15, 2023**. The original repository was lost; this runbook and configuration were recreated on September 24, 2026 from the article. They are not the original deployment or training data. Git commits use their actual creation date.

**Availability:** [AWS stopped accepting new Amazon Fraud Detector customers on November 7, 2025](https://docs.aws.amazon.com/frauddetector/latest/ug/frauddetector-availability-change.html). Existing customers can still use the service. This repo preserves the historical project and will only be executable in an eligible AWS account.

## Project configuration

| Resource | Value documented in the article |
| --- | --- |
| Entity | `customer` |
| Event type | `registration` |
| Labels | `fraud`, `legit` |
| Model | `fraud_detection_model`, Online Fraud Insights |
| Detector | `detector-getting-started` |
| High-risk rule | `$<fraud_detection_model>_insightscore >= 900` → `high_risk` |
| Additional outcomes | `medium_risk`, `low_risk` |

The source does not provide the sample dataset, variable mapping, or the numerical thresholds for the medium and low rules. `config/project.json` records those gaps rather than inventing historical values.

## Recreate in an eligible AWS account

1. Obtain a suitable historical-event CSV and upload it to a private S3 bucket. Use the data format and event variables required by Amazon Fraud Detector for Online Fraud Insights. Keep sensitive records out of this repo.
2. In the Fraud Detector console, create entity `customer`; labels `fraud` and `legit`; and event type `registration` with a data access IAM role for the S3 location.
3. Create `fraud_detection_model` as an Online Fraud Insights model for `registration`. Set the two label mappings, train it, review score distribution, confusion matrix, false-positive rate, true-positive rate and precision, then deploy a chosen version.
4. Create outcomes `high_risk`, `medium_risk`, and `low_risk`. Create detector `detector-getting-started`, attach the model, and add the documented high-risk rule. Select medium and low thresholds from the trained model's observed score distribution before publishing the detector version.
5. Use the console's test feature with sample registration events and inspect the returned score and outcome. The original article's tests were screenshots only; no machine-readable test events were published.

The service creates billable resources. No AWS resources were created during this reconstruction. For a new account, AWS recommends an [AutoGluon-based alternative](https://docs.aws.amazon.com/frauddetector/latest/ug/frauddetector-availability-change.html).
