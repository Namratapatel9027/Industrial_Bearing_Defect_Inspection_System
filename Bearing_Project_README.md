# Bearing Defect Inspection System

> **Documentation-only portfolio repository.** Source code, trained weights, client datasets, credentials, and proprietary implementation details are intentionally excluded.

## Project overview

An industrial computer-vision inspection system designed to identify visible defects on manufactured bearings and present an auditable **OK / NG** inspection result to an operator.

The remembered inspection classes are:

- Scratch
- Crack
- Rust
- Dent

The system uses a two-stage YOLOv8 detection design:

1. **Bearing localization — YOLOv8n:** detects the bearing in the full camera frame and defines the region of interest (ROI).
2. **Defect inspection — YOLOv8m:** analyzes the bearing crop for the four defect classes.
3. **Decision logic:** after confidence filtering and duplicate suppression, one or more qualifying defects produce **NG**; no qualifying defect produces **OK**.

This is a dashboard-based inspection workflow. An automatic PLC rejection mechanism was not part of the remembered project scope.

## High-level architecture

![System architecture](diagrams/01_system_architecture.png)

## Computer-vision pipeline

![Two-stage CV pipeline](diagrams/02_two_stage_cv_pipeline.png)

## Data and model development

![Dataset and training lifecycle](diagrams/03_dataset_training_lifecycle.png)

- Approximately **5,000 original images** were recalled.
- An approximate **70:20:10** train/validation/test split was used, with images grouped by bearing/session to reduce leakage.
- Training augmentation increased the training set to approximately **8,000 images**. Augmentation was applied to training data only.
- QuPath was used for bounding-box annotation.
- Stage 1 uses a `Bearing` class. Stage 2 uses `Scratch`, `Crack`, `Rust`, and `Dent`.
- Recalled augmentation operations include rotation, flipping, brightness/contrast changes, blur, and noise. Images were prepared at 640 × 640 for the YOLO workflow.

## Reported results

| Metric | Reported value |
|---|---:|
| Accuracy | **93.2%** |
| Precision | **90.44%** |
| Recall | **91.1%** |

These figures were supplied from project recollection. The exact evaluation report, metric averaging method, class-wise breakdown, test-set definition, and corresponding mAP values have not yet been recovered. They should not be interpreted as independently audited benchmarks.

## Technology stack

- **Camera / acquisition:** Baumer industrial camera and Camera SDK (exact model and SDK version not recovered)
- **Computer vision:** Python, OpenCV, Ultralytics YOLOv8
- **Models:** YOLOv8n for bearing localization; YOLOv8m for defect detection
- **Backend:** FastAPI
- **Dashboard:** Streamlit
- **Packaging / deployment:** Docker
- **Persistence:** a database was used; exact engine and schema not recovered

## Runtime and deployment flow

![Runtime deployment flow](diagrams/04_runtime_deployment_flow.png)

The original setup used a Baumer camera with its SDK. A separate simulation mode using recorded video is suitable for a public portfolio demonstration. The simulation is a documentation/demo adaptation and should not be described as the original production camera interface.

## Key implementation details recalled

| Area | Recalled detail |
|---|---|
| Camera | Baumer industrial camera |
| Acquisition interface | Camera SDK |
| Camera arrangement | Camera positioned above the bearing in a separate test setup |
| Stage 1 | YOLOv8n, `Bearing` |
| Stage 2 | YOLOv8m, `Scratch`, `Crack`, `Rust`, `Dent` |
| Input preparation | 640 × 640 |
| Training | Approximately 500 epochs; batch size 64 (recollection; verify training logs) |
| GPU | NVIDIA A100 on an IIT server (recollection; verify) |
| Confidence threshold | Approximately 0.75 |
| Data split | 70:20:10, bearing/session-wise |
| Augmentation | Training set only; approximately 8,000 augmented training images |
| Backend / UI | FastAPI / Streamlit |
| Deployment | Docker |
| Output | Dashboard with bearing crop, defect labels/boxes, confidence, timestamp and OK/NG |
| Automation | No PLC-based automatic rejection confirmed |

## Confidentiality boundary

This repository intentionally describes the project at an architectural and capability level. It does **not** contain:

- Source code or internal implementation files
- Model weights or proprietary training artifacts
- Client images, videos, annotations, or identifying information
- Internal camera credentials, network details, or deployment secrets
- Private API keys, database credentials, or production URLs

Only publish this README and the approved diagrams/documentation. Confirm that your former employer permits public disclosure of even high-level project details before publishing.

## Documentation

See [`docs/PROJECT_DOCUMENTATION.md`](docs/PROJECT_DOCUMENTATION.md) for the detailed end-to-end technical record, parameter register, assumptions, limitations, and interview-safe explanation.

## Parameter verification status

Some exact values are not available from the project recollection, including the Baumer camera model, lens, exposure/gain, frame rate, SDK version, Ultralytics version, optimizer, learning-rate schedule, early-stopping patience, NMS/duplicate-filter IoU threshold, database engine, API endpoint names, and Docker service topology. These are explicitly marked as **not recovered** in the detailed documentation rather than guessed.
