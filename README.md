# ultralytics_ros (make87 Fork)

**This is a make87-specific fork of the original [ultralytics_ros](https://github.com/Alpaca-zip/ultralytics_ros) package, tailored for deployment on the [make87 platform](https://make87.com).**

### Introduction
ROS 2 package for real-time object detection and segmentation using the Ultralytics YOLO, optimized for containerized deployment with make87's configuration management and Zenoh networking.

|  `tracker_node`  |
| :------------: |
| <img src="https://github.com/Alpaca-zip/ultralytics_ros/assets/84959376/7ccefee5-1bf9-48de-97e0-a61000bba822" width="450px"> |

- The `tracker_node` provides real-time object detection on incoming ROS 2 image messages using the Ultralytics YOLO model.
- Configured entirely through make87's `MAKE87_CONFIG` environment variable.
- Uses Zenoh middleware for efficient networking.

## make87 Deployment
This package is designed to run on the make87 platform. Configuration is handled automatically through:
- **MAKE87.yml**: Defines interfaces, publishers, subscribers, and configuration parameters
- **Entrypoint script**: Parses make87 configuration and launches the node with appropriate parameters
- **Containerized build**: Multi-stage Docker build optimized for production deployment

## Development Setup
For local development outside of make87:

```bash
$ cd ~/{ROS2_WORKSPACE}/src
$ git clone https://github.com/make87-apps/ultralytics_ros.git
$ cd ~/{ROS2_WORKSPACE}
$ rosdep install -r -y -i --from-paths .
$ python3 -m pip install -r ultralytics_ros/requirements.txt
$ colcon build
```

## Development Usage
Run the tracker node directly with parameters:

```bash
$ ros2 run ultralytics_ros tracker_node.py \
  --ros-args \
  -p input_topic:="/camera/image_raw" \
  -p result_image_topic:="/yolo/result_image" \
  -p result_topic:="/yolo/detections" \
  -p yolo_model:="yolov8n.pt" \
  -p confidence_threshold:=0.25 \
  -p iou_threshold:=0.45 \
  -p tracker_type:="bytetrack"
```

## `tracker_node`
### Params
- `yolo_model`: Pre-trained Weights.
For yolov8, you can choose `yolov8*.pt`, `yolov8*-seg.pt`.

  |  YOLOv8  |  YOLOv8-seg  |
  | :-------------: | :-------------: |
  | <img src="https://github.com/Alpaca-zip/ultralytics_ros/assets/84959376/08770080-bf20-470b-8269-eee7a7c41acc" width="350px"> | <img src="https://github.com/Alpaca-zip/ultralytics_ros/assets/84959376/7bb6650c-769d-41c1-86f7-39fcbf01bc7c" width="350px"> |

  See also: https://docs.ultralytics.com/models/
- `confidence_threshold`: Confidence threshold below which boxes will be filtered out (default: 0.25).
- `iou_threshold`: IoU threshold below which boxes will be filtered out during NMS (default: 0.45).
- `tracker_type`: Tracking algorithms (bytetrack, botsort) (default: "bytetrack").

**Note**: Topic names (`input_topic`, `result_topic`, `result_image_topic`) are configured through the make87 platform interface definitions.

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

## Changes from Original
This make87 fork includes the following modifications:
- **Python-only**: Removed C++ components (`tracker_with_cloud_node`)
- **make87 Configuration**: Replaced launch files with MAKE87_CONFIG parsing
- **Zenoh Networking**: Configured for make87's Zenoh middleware
- **Containerized**: Multi-stage Docker build for optimized deployment
- **Simplified Parameters**: Reduced to core detection parameters only

## Original Repository
For the original multi-language (Python/C++) version with launch files and additional features, see: https://github.com/Alpaca-zip/ultralytics_ros