single convolutional neural network (CNN) that simultaneously predicts multiple bounding boxes and class probabilities for each bounding box

1. ****Grid Division****: The input image is divided into an S x S grid. Each grid cell is responsible for detecting objects whose center falls within it.
2. ****Bounding Box Prediction****: Each grid cell predicts B bounding boxes, each with a confidence score. The confidence score reflects the probability that a bounding box contains an object and the accuracy of the bounding box.
3. ****Class Prediction****: Each grid cell also predicts the class probabilities C for the object, given that an object exists in that grid cell.
4. ****Final Predictions****: YOLO combines these predictions to generate the final detected objects, applying non-max suppression to eliminate redundant bounding boxes.


![[Pasted image 20260118173012.png]]

1. [**Backbone**](https://blog.roboflow.com/glossary/#:~:text=backbone): A convolutional neural network that aggregates and forms image features at different granularities.
2. [**Neck**](https://blog.roboflow.com/glossary/#:~:text=neck%20-): A series of layers to mix and combine image features to pass them forward to prediction.
3. [**Head**](https://blog.roboflow.com/glossary/#:~:text=head%20-): Consumes features from the neck and takes box and class prediction steps.

- **Built on a base network (Backbone)**, typically a CNN such as CSPDarknet in YOLOv5, which extracts hierarchical feature maps from the input image.
- **Additional convolutional layers (Neck)** are stacked on top of the backbone to fuse feature maps at different resolutions using feature pyramid structures, enabling detection of objects at multiple scales.
- **Detection heads operate on these multi-scale feature maps**, predicting multiple bounding boxes at each spatial location, with each box associated with an objectness score and class probabilities.
- **Uses anchor boxes (pre-defined bounding boxes with different scales and aspect ratios)** at each feature map location, which are refined during training to accurately localize objects.

### Flow


![[Pasted image 20260118175346.png]]

1. Start Load Image
2. Preporcessing , Normalize Image
3. Extract Features , Generate Feature Maps
4. Perform , Non Maximum Suppression 
5. Apply Convolutional Layers
6. Define Anchors, Predefined Aspect Ratios
7. Output Bounding Boxes and Class 
8. End

# 
1. **Start – Load Input Image**
2. **Preprocessing**
    - Resize image (e.g. 640×640)
    - Normalize pixel values
    - Convert to tensor
3. **Feature Extraction (Backbone CNN)**
    - Apply convolutional layers
    - Extract hierarchical feature maps
4. **Feature Fusion (Neck: FPN + PAN)**
    - Combine feature maps at multiple resolutions
    - Prepare multi-scale features for detection
5. **Anchor Boxes Initialization**
    - Use predefined anchor boxes with different scales and aspect ratios
    - Anchors are fixed before prediction (learned during training)
6. **Detection Head Predictions**
    - Predict bounding box offsets (x, y, w, h)
    - Predict objectness score
    - Predict class probabilities
7. **Post-processing**
    - Compute final confidence scores
    - Apply **Non-Maximum Suppression (NMS)**
8. **Output**
    - Final bounding boxes
    - Class labels
    - Confidence scores
9. **End**
    

---