# Tube-detection
Zeon Assignment 


# Problem Statement
We are given set of overhead images of tube we need to detect the tube position and their orientation as well


## DATA FORMAT

Images
`images/` contains 70 PNG images (640x480, RGB).

Each image is an overhead photo of a surface with microcentrifuge tubes. The number of tubes per image varies (3-6). Backgrounds include desks, white surfaces, black surfaces, and mixed-color surfaces.

Annotations
`annotations.csv` contains ground truth for all 70 images (371 total tubes).

| Column | Type | Description |
|-------------|--------|------------------------------------------------------------|
| `image` | string | Image filename (e.g. `2659ffa5-color.png`) |
| `center_x` | float | Tube lid center, x-coordinate in pixels |
| `center_y` | float | Tube lid center, y-coordinate in pixels |
| `bbox_x` | float | Bounding box top-left x |
| `bbox_y` | float | Bounding box top-left y |
| `bbox_w` | float | Bounding box width |
| `bbox_h` | float | Bounding box height |
| `bbox_rotation` | float | Bounding box rotation in degrees (clockwise) |
| `angle_deg` | float | Tube lid rotation angle in degrees, range [0, 360), defined by joint-to-tab direction |


Coordinate System
- Origin is the top-left corner of the image.
- X increases rightward, Y increases downward.
- Angle 0 degrees points along the positive X-axis (rightward).
- Angles increase counter-clockwise.
- Rotation angle of the tube is defined by the direction of the joint to the tab.


## APPROACH

Finding out several YOLO model ,Standard object detection models output Axis-Aligned Bounding Boxes (AABB), which do not account for the object's rotational orientation. YOLO-OBB (Oriented Bounding Box) generates tight, rotated boundaries, while YOLO-Pose identifies specific points of interest (keypoints) which might be point of interest in our case we need to find joint and tab points to find the orientation angle of joint to tab. The problem is that there exist no single model which predicts both.


My first approach was to use YOLO pose-model to find out 4 corner points + 2(joint and tab) points make arrow based on joint and tab , and joining the 4 corner bounding box points to make rectangle. While joining the 4 corner points it did not look like rectangle more of polygon/triangle  and was not giving good result with this approach as compared to ground truth.

![First Approach](<Screenshot%202026-05-14%20175905.png>)


2nd Approach was to use both model YOLO pose as well YOLO OBB ; one for bounding box , one for joint to tab angle and merge their result.11 Validation images ground truth and prediction done in the end of notebook.

![Second Approach](<Screenshot%202026-05-14%20175710.png>)

## IMPORTANT OBSERVATION

Selection of confidence score was higly important it was taken to be 0.25 and the images above are based on that if model is confident more than 25% then it would detect it. We can see that there is one image it manages to detect the box but misses out the arrow , if the confidence score is set to be lower it could also detect the arrows.


The bounding box predicted by the OBB model and the keypoint vector predicted by the Pose model must have center points within 20 pixels of each other. If they disagree, the prediction is discarded.

Once the ensemble prediction is formed, it is compared to the Ground Truth. It is only counted as a True Positive if the predicted bounding box achieves (IoU) > 0.45 against the actual label.

Based on confidence score=0.25 IoU >0.45

## Metrics

Precision:       1.0000
Recall:          0.9672
F1-Score:        0.9833
Mean Ang. Error: 8.34°

## Analysis and Next Step
For robotics application it is quite important to have the orientation of the tube as well as information about tab and joint to open tab. Standard bounding box would not solve the purpose and pose estimation is highly essential for finding the orientation of tube and location point of tab and joint. Precision 1 means that it does not detect any background as tube that is false positive whereas recall 0.9672 it misses some tube (i.e 3.28%) as we can see in the image that it misses 2 tubes joint to tab dirn(but not bounding box). Mean ang error shows that it deviates around 8 degrees, we need to improve this parameter according to precision and tolerance of our robot.

This was done for very small dataset that was around 70 images. Deep learning model requires large amount of data for giving good result , if we are limited with data then we can augment images(i.e flip rotate crop etc) hence increasing size of dataset for training . Another very popular method which is going on is MetaLearning which could be looked upon.

When the model is final , a integration is to be done with the robot to functionally work and detect and open and pick.

## USE OF AI

Code syntax,code debugging,looking for various YOLO Model




