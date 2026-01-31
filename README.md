# Face Locking Module

## Overview

This project builds on an existing **CPU-based face recognition pipeline** (ArcFace ONNX with 5-point landmark alignment) by introducing a **Face Locking mechanism**.

The goal of face locking is to move beyond frame-by-frame recognition and instead **bind the system to a single chosen identity**, allowing consistent tracking and behavior analysis over time.

In simple terms, once the correct person is identified, the system:

* Fixes its attention on that identity
* Follows the same person across consecutive frames
* Continues tracking even during short recognition interruptions
* Monitors and records basic facial actions
* Stores all detected actions with timestamps for later review

---

## Core Functionality

### Identity Locking

* A **specific enrolled identity** is selected manually (currently configured as `"Gabriel"`).
* The face lock is activated only after **high-confidence recognition**.
* Once locked, the pipeline ignores all other detected faces in the scene.

### Robust Tracking

* Identity continuity is maintained using a combination of:

  * IOU-based bounding box tracking
  * Embedding similarity checks
  * Time-based unlocking when the face is lost for too long
* Temporary occlusions or recognition drops do **not** immediately release the lock.

### Action Detection (Post-Lock Only)

Action analysis is performed **exclusively while the face is locked** to ensure correctness. The following movements are detected:

* Horizontal face movement (left / right)
* Eye blinking (via Eye Aspect Ratio)
* Smile detection (increased mouth width)
* Laugh detection (wide and vertically open mouth)

### Action History Logging

* All detected actions are written to a log file named using the pattern:

  ```
  <identity>_history_<timestamp>.txt
  ```

  Example:

  ```
  gabriel_history_1738339200123.txt
  ```
* Each entry in the log includes:

  * Timestamp
  * Detected action type
  * Short descriptive note

---

## Design Constraints

* **CPU-only execution** (no GPU acceleration)
* No model retraining required
* Emphasis on **clear, explainable logic** rather than production-level accuracy

This implementation demonstrates how face recognition can be extended into a stable, identity-aware tracking system capable of contextual action analysis.
