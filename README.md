# ultralytics_ros
### Introduction
ROS/ROS 2 package for real-time object detection and segmentation using the Ultralytics YOLO, enabling flexible integration with various robotics applications.

|  `tracker_node`  |  `tracker_with_cloud_node`  |
| :------------: | :-----------------------: |
| <img src="https://github.com/Alpaca-zip/ultralytics_ros/assets/84959376/7ccefee5-1bf9-48de-97e0-a61000bba822" width="450px"> | <img src="https://github.com/Alpaca-zip/ultralytics_ros/assets/84959376/674f352f-5171-4fcf-beb5-394aa3dfe320" height="160px"> |

- The `tracker_node` provides real-time object detection on incoming ROS/ROS 2 image messages using the Ultralytics YOLO model.
- The `tracker_with_cloud_node` provides functionality for 3D object detection by integrating 2D detections, mask image, LiDAR data, and camera information.

### Status
| ROS distro | Industrial CI | Docker |
| :--------: | :-----------: | :----: |
|  ROS Melodic | [![ROS-melodic Industrial CI](https://github.com/Alpaca-zip/ultralytics_ros/actions/workflows/melodic-ci.yml/badge.svg)](https://github.com/Alpaca-zip/ultralytics_ros/actions/workflows/melodic-ci.yml) | [![ROS-melodic Docker Build Check](https://github.com/Alpaca-zip/ultralytics_ros/actions/workflows/melodic-docker-build-check.yml/badge.svg)](https://github.com/Alpaca-zip/ultralytics_ros/actions/workflows/melodic-docker-build-check.yml)
|  ROS Noetic | [![ROS-noetic Industrial CI](https://github.com/Alpaca-zip/ultralytics_ros/actions/workflows/noetic-ci.yml/badge.svg)](https://github.com/Alpaca-zip/ultralytics_ros/actions/workflows/noetic-ci.yml) | [![ROS-noetic Docker Build Check](https://github.com/Alpaca-zip/ultralytics_ros/actions/workflows/noetic-docker-build-check.yml/badge.svg)](https://github.com/Alpaca-zip/ultralytics_ros/actions/workflows/noetic-docker-build-check.yml)
|  ROS 2 Humble | [![ROS2-humble Industrial CI](https://github.com/Alpaca-zip/ultralytics_ros/actions/workflows/humble-ci.yml/badge.svg)](https://github.com/Alpaca-zip/ultralytics_ros/actions/workflows/humble-ci.yml) | [![ROS2-humble Docker Build Check](https://github.com/Alpaca-zip/ultralytics_ros/actions/workflows/humble-docker-build-check.yml/badge.svg)](https://github.com/Alpaca-zip/ultralytics_ros/actions/workflows/humble-docker-build-check.yml)

## Setup ⚙
### ROS Melodic
```bash
$ cd ~/{ROS_WORKSPACE}/src
$ GIT_LFS_SKIP_SMUDGE=1 git clone -b melodic-devel https://github.com/Alpaca-zip/ultralytics_ros.git
$ rosdep install -r -y -i --from-paths .
$ pip install pipenv
$ cd ultralytics_ros
$ pipenv install
$ pipenv shell
$ cd ~/{ROS_WORKSPACE} && catkin build
```
### ROS Noetic
```bash
$ cd ~/{ROS_WORKSPACE}/src
$ GIT_LFS_SKIP_SMUDGE=1 git clone -b noetic-devel https://github.com/Alpaca-zip/ultralytics_ros.git
$ rosdep install -r -y -i --from-paths .
$ python3 -m pip install -r ultralytics_ros/requirements.txt
$ cd ~/{ROS_WORKSPACE} && catkin build
```
### ROS 2 Humble
```bash
$ cd ~/{ROS2_WORKSPACE}/src
$ GIT_LFS_SKIP_SMUDGE=1 git clone -b humble-devel https://github.com/Alpaca-zip/ultralytics_ros.git
$ rosdep install -r -y -i --from-paths .
$ python3 -m pip install -r ultralytics_ros/requirements.txt
$ cd ~/{ROS2_WORKSPACE} && $ colcon build
```
**NOTE**: If you want to download KITTI datasets, remove `GIT_LFS_SKIP_SMUDGE=1` from the command line.

## Run 🚀
### ROS Melodic & ROS Noetic
**`tracker_node`**
```bash
$ roslaunch ultralytics_ros tracker.launch debug:=true
```
**`tracker_node` & `tracker_with_cloud_node`**
```bash
$ roslaunch ultralytics_ros tracker_with_cloud.launch debug:=true
```
### ROS 2 Humble
**`tracker_node`**
```bash
$ ros2 launch ultralytics_ros tracker.launch.xml debug:=true
```
**`tracker_node` & `tracker_with_cloud_node`**
```bash
$ ros2 launch ultralytics_ros tracker_with_cloud.launch.xml debug:=true
```
**NOTE**: If the 3D bounding box is not displayed correctly, please consider using a lighter yolo model(`yolov8n.pt`) or increasing the `voxel_leaf_size`.

## `tracker_node`
### Params
- `yolo_model`: Pre-trained Weights.
For yolov8, you can choose `yolov8*.pt`, `yolov8*-seg.pt`.

  |  YOLOv8  |  <img src="https://github.com/Alpaca-zip/ultralytics_ros/assets/84959376/08770080-bf20-470b-8269-eee7a7c41acc" width="350px">  |
  | :-------------: | :-------------: |
  |  **YOLOv8-seg**  |  <img src="https://github.com/Alpaca-zip/ultralytics_ros/assets/84959376/7bb6650c-769d-41c1-86f7-39fcbf01bc7c" width="350px">  |

  See also: https://docs.ultralytics.com/models/
- `input_topic`: Topic name for input image.
- `result_topic`: Topic name of the custom message containing the 2D bounding box and the mask image.
- `result_image_topic`: Topic name of the image on which the detection and segmentation results are plotted.
- `conf_thres`: Confidence threshold below which boxes will be filtered out.
- `iou_thres`: IoU threshold below which boxes will be filtered out during NMS.
- `max_det`: Maximum number of boxes to keep after NMS.
- `tracker`: Tracking algorithms.
- `device`: Device to run the model on(e.g. cpu or cuda:0).
  ```xml
  <arg name="device" default="cpu"/>
  ```
  ```xml
  <arg name="device" default="cuda:0"/>
  ```
- `classes`: List of class indices to consider.
  ```xml
  <param name="classes" value="0, 1" value-sep=", "/> <!-- person, bicycle -->
  ```
  See also: https://github.com/ultralytics/ultralytics/blob/main/ultralytics/cfg/datasets/coco128.yaml
- `result_conf`:  Whether to plot the detection confidence score.
- `result_line_width`: Line width of the bounding boxes.
- `result_font_size`: Font size of the text.
- `result_labels`: Font to use for the text.
- `result_font`: Whether to plot the label of bounding boxes.
- `result_boxes`: Whether to plot the bounding boxes.
### Topics
- Subscribed Topics:
  - Image data from `input_topic` parameter. ([sensor_msgs/Image](https://github.com/ros2/common_interfaces/blob/humble/sensor_msgs/msg/Image.msg))
- Published Topics:
  - Plotted images to `result_image_topic` parameter. ([sensor_msgs/Image](https://github.com/ros2/common_interfaces/blob/humble/sensor_msgs/msg/Image.msg))
  - Detected objects(2D bounding box, mask image) to `result_topic` parameter. (ultralytics_ros/YoloResult)
    ```
    std_msgs/Header header
    vision_msgs/Detection2DArray detections
    sensor_msgs/Image[] masks
    ```
