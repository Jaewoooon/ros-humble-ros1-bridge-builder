# ROS1 Docker to ROS2 Docker communication

### 1. Build the ROS1 Bridge Builder (if not already built)
```bash
# Clone the repository
git clone https://github.com/Jaewoooon/ros-humble-ros1-bridge-builder.git
cd ros-humble-ros1-bridge-builder

# Build the Docker image
docker build . -t ros-humble-ros1-bridge-builder
```

### 2. Extract the ROS1 Bridge Package
```bash
# Extract the pre-compiled ROS1 bridge package to your home directory
cd ~/
docker run --rm ros-humble-ros1-bridge-builder | tar xvzf -
```

### 3. Start ROS1 (Noetic) Container
```bash
# Run ROS1 Noetic in a Docker container
docker run -it --rm --privileged --network=host -v /dev/shm:/dev/shm --name ros1_noetic osrf/ros:noetic-desktop-full-focal bash

# Start roscore
source /opt/ros/noetic/setup.bash
roscore
```

### 4. Start ROS2 (Humble) Container
```bash
# Run ROS2 Humble in a Docker container, mounting the ROS1 bridge
docker run -it --rm --privileged --network=host -v /dev/shm:/dev/shm -v ~/ros-humble-ros1-bridge:/root/ros-humble-ros1-bridge --name ros2_humble osrf/ros:humble-desktop-jammy bash


# Start the ROS1 bridge
source /opt/ros/humble/setup.bash
source /root/ros-humble-ros1-bridge/install/local_setup.bash
ros2 run ros1_bridge dynamic_bridge
```

### 5. Run ROS1 Talker
```bash
# In a new terminal, run a ROS1 talker in the ROS1 container
docker exec -it ros1_noetic bash -c "source /opt/ros/noetic/setup.bash && rosrun rospy_tutorials talker"
```

### 6. Run ROS2 Listener
```bash
# In a new terminal, run a ROS2 listener in the ROS2 container
docker exec -it ros2_humble bash -c "source /opt/ros/humble/setup.bash && ros2 run demo_nodes_cpp listener"
```

The ROS2 listener should now receive messages from the ROS1 talker through the bridge.
