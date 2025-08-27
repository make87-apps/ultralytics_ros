# ── Build stage ────────────────────────────────────────────────────────────────
ARG ROS_DISTRO=humble
FROM ros:${ROS_DISTRO}-ros-base AS builder

SHELL ["/bin/bash", "-c"]
ENV DEBIAN_FRONTEND=noninteractive

# Build essentials + colcon + python packages
RUN apt-get update && apt-get install -y --no-install-recommends \
    python3-colcon-common-extensions \
    python3-pip \
    ros-${ROS_DISTRO}-cv-bridge \
    ros-${ROS_DISTRO}-vision-msgs \
    && rm -rf /var/lib/apt/lists/*

# Workspace lives at /opt/ws; your Docker build context is the repo root (.)
WORKDIR /opt/ws
COPY . .

# Install Python requirements
RUN python3 -m pip install -r requirements.txt

# Build in Release, merge-install makes a single overlay dir
RUN source /opt/ros/${ROS_DISTRO}/setup.bash && \
    colcon build \
    --merge-install \
    --cmake-args -DCMAKE_BUILD_TYPE=Release

# ── Runtime stage ──────────────────────────────────────────────────────────────
FROM ros:${ROS_DISTRO}-ros-core
SHELL ["/bin/bash", "-c"]

# Install runtime dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    ros-${ROS_DISTRO}-rmw-zenoh-cpp \
    ros-${ROS_DISTRO}-cv-bridge \
    ros-${ROS_DISTRO}-vision-msgs \
    ros-${ROS_DISTRO}-image-view \
    python3-pip \
    jq \
    git \
    && rm -rf /var/lib/apt/lists/*

# Copy only the built install tree
COPY --from=builder /opt/ws/install /opt/ws/install

# Copy Python requirements and install them in runtime
COPY --from=builder /opt/ws/requirements.txt /opt/ws/requirements.txt
RUN python3 -m pip install -r /opt/ws/requirements.txt

# Copy the entrypoint script
COPY build_kit/entrypoint.sh /usr/local/bin/entrypoint.sh
RUN chmod +x /usr/local/bin/entrypoint.sh

# Optional: keep a stable domain
ENV ROS_DOMAIN_ID=87
ENV RMW_IMPLEMENTATION=rmw_zenoh_cpp

# Set production paths for the entrypoint script
ENV PROJECT_DIR=/opt/ws
ENV ROS_ENTRYPOINT=/ros_entrypoint.sh
ENV SETUP_SCRIPT=install/setup.bash
ENV PACKAGE_NAME=ultralytics_ros
ENV NODE_NAME=tracker_node

# Use our custom entrypoint script
ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
