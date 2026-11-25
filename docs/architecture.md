# CeramicBough — proposed AWS architecture

CeramicBough is designed on AWS so image storage, model training, classification and reporting scale with your inspection volume — and your images stay in your account region.

## Request flow

1. Photograph
2. Amazon S3
3. AWS Lambda
4. SageMaker AI
5. DynamoDB
6. Inspector review
7. Bedrock summary

## Services

### Amazon SageMaker AI

**Role:** Train and host the defect classifier

Trains the computer vision model on inspector-labelled photographs, evaluates every new version against a held-out set, and serves real-time classifications from a managed endpoint.

### Amazon S3

**Role:** Store product photographs

Holds original images and model crops with encryption at rest, lifecycle rules for older batches, and per-factory prefixes so data never mixes.

### AWS Lambda

**Role:** Run the inspection pipeline

Triggers on each upload: validates the image, calls the SageMaker endpoint, stores the result and notifies the review queue — no servers to manage.

### Amazon DynamoDB

**Role:** Keep batches, results and decisions

Stores batch histories, classifications, reviewer decisions and audit events with single-digit-millisecond reads for the dashboards.

### Amazon Bedrock

**Role:** Draft quality summaries

Turns confirmed defect data into plain-language weekly summaries and answers questions about batches — grounded in your records and always edited by a person.

## Principles

- **Your images, your region** — Photographs and results stay in the AWS region you choose, separated per customer.
- **Measured before it ships** — Each model version is evaluated against inspector-labelled images before it serves predictions.
- **Pay for what you inspect** — Serverless processing keeps costs in line with inspection volume.

## Safeguards

- **The inspector decides** — CeramicBough suggests. A qualified inspector confirms every classification before it counts in a report.
- **Visible reasoning** — Each suggestion shows the image region and confidence it was based on, so reviewers can challenge it.
- **Visible defects only** — We classify what a camera can see. Strength, water absorption and other lab tests stay with your laboratory.
- **Encrypted and separated** — Images and results are encrypted in transit and at rest, and each factory's data is isolated.

## MVP scope

We start with one line and the defects that matter most to you. Inspectors upload or capture photographs, review every suggestion, and the confirmed results build the first batch histories and trend reports.

- Photograph upload and fixed-camera capture
- Six starter defect classes plus your own
- Inspector review queue with corrections
- Batch histories and weekly defect trends
- Exportable inspection reports
