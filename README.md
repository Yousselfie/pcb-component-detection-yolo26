# pcb-component-detection-yolo26
Fine-tuned yolo26 nano object-detection model on images of printed-circuit-boards and their labeled components.

## Problem
PCB electrical components can come in very small sizes which makes PCB inspection and quality assurance a more challenging and tedious task. 

## Approach
From pretrained-COCO weights, fine-tuned **YOLO26-nano** on a **672-image** custom dataset (Roboflow) trained on Colab **T4 GPU** for 50 epochs at 640px.

## Results
Evaluated on the held-out validation split:

| Metric | Score |
|--------|-------|
| mAP50  | 0.088      |
| mAP50-95 |   0.052   |
| Precision | 0.141     |
| Recall |  0.164     |

These are baseline numbers from an initial, deliberately short training run. They are low, and the analysis below explains why and how to improve them — reading a model's own metrics to diagnose its failure mode is part of the point of this project.

**Training curves** 

![Training results](results/results.png)

**Confusion matrix** 

![Confusion matrix](results/confusion_matrix.png)

## Next steps
The baseline scored 0.088 mAP50 on a dense ~233-objects-per-image dataset. Two factors, both visible from the setup and the metrics, account for most of that and are the priority fixes:

- **Insufficient training (undertraining)**. Dense small-object detection needs far more epochs to converge than a standard fine-tune. The low precision (0.141) and recall (0.164) are consistent with a model that stopped well before learning the task. **Fix**: retrain for 100–150 epochs with early stopping (patience=30) so training runs until the validation mAP genuinely plateaus.

- **Resolution** too low for the object size. At imgsz=640, each of the ~230 components per board occupies only a handful of pixels, leaving too little detail for tight localization — which is why mAP50-95 (0.052) is especially low. **Fix**: train at imgsz=1280, the standard remedy for small-object detection.

- **Further improvements**: expand the dataset for the weakest classes, and export to ONNX served behind a FastAPI endpoint (see deployment repo) to complete the train → deploy pipeline.

## How to reproduce
This project runs in **Google Colab** (free T4 GPU).

1. Open the notebook in Colab:
   [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1X6RskWSDTkcrkjov5aO498YneDImfPyj?usp=sharing)
2. Set the runtime to GPU: **Runtime → Change runtime type → T4 GPU**.
3. Dataset: PCB component detection dataset (Roboflow, YOLO format). Add your
   Roboflow API key in the dataset cell, or point the notebook at your own copy.
4. Run all cells top to bottom. Training outputs (weights, results.png,
   confusion_matrix.png) are written to the session's `runs/` folder — mount
   Drive first if you want them to persist.
5. Baseline run: `epochs=50, imgsz=640`. Improved run: `epochs=150, imgsz=1280`.
