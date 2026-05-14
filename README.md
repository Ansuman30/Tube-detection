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

Finding out several YOLO model , generic YOLO model only makes bounding box in a particular way it does not account how object is placed and its orientation, YOLO OBB(Oriented Bounding Box) makes bounding box in the way a object is oriented, YOLO pose helps to find out certain points which might be point of interest in our case we need to find joint and tab points to find the orientation angle of joint to tab. The problem is that there exist no single model which predicts both.


My first approach was to use YOLO pose-model to find out 4 corner points + 2(joint and tab) points make arrow based on joint and tab , and joining the 4 corner bounding box points to make rectangle. While joining the 4 corner points it did not look like rectangle more of polygon/triangle  and was not giving good result with this approach as compared to ground truth.


2nd Approach was to use both model YOLO pose as well YOLO OBB ; one for bounding box , one for joint to tab angle and merge their result.




