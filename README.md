# pcb-component-detection-yolo26
Fine-tuned yolo26 nano object-detection model on images of printed-circuit-boards and their labeled components.

## Problem
PCB electrical components can come in very small sizes which makes PCB inspection and quality assurance a more challenging and tedious task. 

## Approach
From pretrained-COCO weights, fine-tuned **YOLO26-nano** on a **672-image** custom dataset (Roboflow) trained on Colab **T4 GPU** for <N> epochs at 640px.

## Results
Evaluated on the held-out validation split:

| Metric | Score |
|--------|-------|
| mAP50  |       |
| mAP50-95 |      |
| Precision |      |
| Recall |       |

**Training curves** - 

![Training results](results/results.png)

**Confusion matrix** - 

![Confusion matrix](results/confusion_matrix.png)

## Next steps

## How to reproduce
1. Open the training notebook: ['notebooks/train_yolo26_pcb.ipynb'](notebooks/PCB_YOLO.ipynb)
2. Dataset: [Roboflow - 
