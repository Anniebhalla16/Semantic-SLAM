# Use the official ROS Noetic base image
FROM osrf/ros:noetic-desktop

# Set environment variables
ENV DEBIAN_FRONTEND=noninteractive

# Install necessary dependencies
RUN apt-get update && apt-get install -y \
    python3-rosdep \
    python3-vcstool \
    git \
    nano \
    xauth \
    x11-apps \
    lsb-release \
    libqt5core5a \
    libqt5gui5 \
    libqt5widgets5 \
    qtbase5-dev \
    libopencv-dev \
    libpcl-dev \
    # ros-noetic-gtsam \
    ros-noetic-cv-bridge \
    ros-noetic-image-transport \
    ros-noetic-tf2-eigen \
    ros-noetic-image-proc \
    ros-noetic-depth-image-proc \
    ros-noetic-interactive-markers \
    ros-noetic-rviz \
    libgoogle-glog-dev \
    ros-noetic-image-geometry \
    ros-noetic-pcl-ros \
    ros-noetic-cv-bridge \
    libzmqpp-dev \
    nlohmann-json3-dev \
    libeigen3-dev \
    && rm -rf /var/lib/apt/lists/*


 # Update and install necessary packages
RUN apt-get update && apt-get install -y --no-install-recommends \
    apt-utils \
    cmake \
    build-essential \
    unzip \
    pkg-config \
    autoconf \
    libboost-all-dev \
    libjpeg-dev \
    libpng-dev \
    libtiff-dev \
    libvtk7-dev \
    libgtk-3-dev \
    libatlas-base-dev \
    gfortran \
    libparmetis-dev \
    python3-wstool \
    ros-noetic-camera-info-manager \
    python3-catkin-tools \
    && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y \
    libopencv-dev

# Ensure .Xauthority exists and set correct permissions
RUN touch ~/.Xauthority && \
    ls -la ~/.Xauthority && \
    chmod 644 ~/.Xauthority

# Initialize rosdep if not already initialized
RUN if [ ! -f /etc/ros/rosdep/sources.list.d/20-default.list ]; then \
    rosdep init && rosdep fix-permissions &&  rosdep update; \
    else \
    rosdep fix-permissions && rosdep update; \
    fi


# Create and initialize the Hydra workspace
RUN mkdir -p ~/kimera_ws/src
WORKDIR /root/kimera_ws

# Initialize Catkin workspace with configuration
RUN /bin/bash -c "source /opt/ros/noetic/setup.bash && \
    catkin init && \
    catkin config -DCMAKE_BUILD_TYPE=Release"

RUN catkin clean

RUN cd ~/kimera_ws/src && \
    git clone https://github.com/MIT-SPARK/Kimera-VIO-ROS.git Kimera-VIO-ROS


# Import dependencies using vcs
WORKDIR /root/kimera_ws/src
# RUN ls -la ~/hydra_ws/src/hydra/install
RUN vcs import . < ~/kimera_ws/src/Kimera-VIO-ROS/install/kimera_vio_ros_https.rosinstall

WORKDIR /root/kimera_ws
RUN rosdep update &&  rosdep fix-permissions && \
          rosdep install --from-paths src --ignore-src -r -y

RUN rm -rf ~/kimera_ws/src/gtsam && apt update && apt install ros-noetic-gtsam

# Install dependencies using rosdep
RUN cd ~/kimera_ws && \
    /bin/bash -c "source /opt/ros/noetic/setup.bash &&    catkin build"

# Add environment setup to bashrc for automatic sourcing
RUN echo "source /opt/ros/noetic/setup.bash" >> /root/.bashrc
RUN echo "source ~/kimera_ws/devel/setup.bash" >> /root/.bashrc

# Set the default entrypoint to bash
CMD ["/bin/bash"]

