# Scenic-Text-Detection-Recognition-for-Mixed-Orientations-with-PaddleOCR

A compact solution for detecting and recognizing text on shipping containers where the text can appear at various angles (horizontal, vertical, diagonal) and in different shapes or fonts.

---

## Introduction
Detecting and recognizing text on shipping containers can be challenging when the text is oriented in vertical, diagonal, or horizontal layouts, especially in “scenic” or cluttered environments. **PaddleOCR** provides powerful models and tools to handle such complexities. This repository demonstrates how to train, preprocess, and run inference with a **two-stage pipeline** for:
![image](https://github.com/user-attachments/assets/99552e16-0505-4e89-a7a2-a7c8e66628ce)

- **Text Detection** (segmentation-based)  
- **Text Recognition** (attention-based)

---

## Detection Model

**Model chosen**: `det_mv3_db`

### Key Components
- **Feature Extraction**: [MobileNetV3](https://arxiv.org/abs/1905.02244) extracts features from the entire input image.  
- **Text Detection**: [DB (Differentiable Binarization)](https://arxiv.org/abs/1911.08947) predicts text regions by producing a segmentation map.  
- **Refinement**: The DB algorithm refines these regions into precise text boundaries.  
- **Flexible Orientation Support**: This model excels at detecting text in arbitrary orientations.

---

### Why Traditional Detection Falls Short
In tasks where text is strictly horizontal or only slightly tilted, a typical bounding-box-based object detector (like standard Faster R-CNN or YOLO approaches) might suffice. However, when text can be vertical, diagonal, or even curvy, relying purely on aligned bounding boxes can fail:

1. **Inaccurate bounding**: Standard detectors struggle with text that doesn’t align to the x-y axes.  
2. **Varied shapes**: Letters can follow curved baselines or appear stacked in vertical columns.

---

### Why Segmentation (DB) Works Better
1. **Pixel-level precision**: DB segments text at the pixel level, adapting to any shape or orientation naturally.  
2. **Consistent detection**: Even with highly skewed text, DB produces consistent results by focusing on the *text region itself* rather than fitting rectangles.  
3. **Robust post-processing**: Differentiable Binarization refines the segmentation boundaries, enhancing the detection of diagonal and curved text.

---

## Recognition Model

**Model chosen**: `rec_r50_vd_srn`

### Key Components
- **Backbone**: [ResNet50](https://arxiv.org/abs/1512.03385) extracts visual features from the cropped text regions.  
- **Transformer Encoders**: Convert the visual feature map into a sequence representation.  
- **Semantic Reasoning Module (SRN)**: Captures context dependencies within the text.  
- **Decoder**: Attention-based mechanism that predicts character sequences.

---

### Preprocessing
1. **Rotation**: Cropped text images are rotated 90° counterclockwise if needed to ensure consistent orientation.
2. **Grayscale**: Simplifies input, focusing the model on text shapes.  
3. **Resizing**: Standardized to width **256** × height **64** for uniform batching and model expectations.
  ![image](https://github.com/user-attachments/assets/283d034c-0d61-41fa-b65d-242de309306c)

--- 

### Why `rec_r50_vd_srn` Is Suitable
1. **Handles Complex Layouts**: An attention-based sequence decoder can handle irregular text spacing and shapes better than purely convolutional methods.  
2. **Contextual Understanding**: By leveraging the Semantic Reasoning Module, the model captures character dependencies, improving accuracy on multi-orientation or partially occluded text.  
3. **Robust to Noise**: The ResNet50 backbone is proven robust, and the transformer encoder is adept at focusing on relevant text features, even with background clutter.

# Results
Both models were trained on a relatively small amount of examples(3000) but still managed impressive results

### Detection Model:
Precision 0.97, Recall 0.98
### Recognition Model:
98% Accuracy 

Total time for pipeline is under 200ms in C++

System is in production
