
## Final Presentation

1. **Identify the waste**
First we identified which are the type of waste we want to classify , eg: plastic, paper , leaves etc.

So we classified them into 2 categories 
- Biodegradable
- Non-Biodegradable

**Bio Degradable Waste:** 
They include leaves, vegetable scraps etc. 


2. **Data Collections**
We are using image processing to detect the type of waste , so we collected hundreads of images of different types of wastes. 

3. **Data Annotation**: 
- [RoboFlow](https://roboflow.com/) is used for annotating the images.

4. 

### Training Process
Developing a custom object detection model is an iterative process:

**Collect & Organize Images**: Gather images relevant to your specific task. High-quality, diverse data is crucial. See our guide on Data Collection and Annotation.

**Label Objects**: Annotate the objects of interest within your images accurately.

**Train a Model**: Use the labeled data to train your YOLOv5 model. Leverage transfer learning by starting with pretrained weights.
Deploy & Predict: Utilize the trained model for inference on new, unseen data.

**Collect Edge Cases**: Identify scenarios where the model performs poorly (edge cases) and add similar data to your dataset to improve robustness. Repeat the cycle.


## Battery

There are mainly 4 Units thats needs power

1. Raspberry PI (5 V)
2. Conveyor belt (12 V) ( Using Buck Booster )
3. Movement Motor (5 V)
4. Bins Base ( 5V )