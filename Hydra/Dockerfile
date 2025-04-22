# Use the official ROS Noetic base image
FROM osrf/ros:noetic-desktop

# Set environment variables
ENV DEBIAN_FRONTEND=noninteractive

# Install necessary dependencies
RUN apt-get update && apt-get install -y \
    python3-rosdep \
    python3-catkin-tools \
    python3-vcstool \
    git \
    nano \
    xauth \
    x11-apps \
    lsb-release \
    pkg-config \
    libqt5core5a \
    libqt5gui5 \
    libqt5widgets5 \
    qtbase5-dev \
    libopencv-dev \
    libpcl-dev \
    ros-noetic-gtsam \
    ros-noetic-cv-bridge \
    ros-noetic-image-transport \
    ros-noetic-tf2-eigen \
    ros-noetic-image-proc \
    ros-noetic-depth-image-proc \
    ros-noetic-interactive-markers \
    ros-noetic-rviz \
    libgoogle-glog-dev \
    libzmqpp-dev \
    nlohmann-json3-dev \
    libeigen3-dev \
    && rm -rf /var/lib/apt/lists/*

# Install OpenGL dependencies

RUN apt-get update && apt-get install -y \
    libglvnd0 \
    libx11-dev \
    libxrandr-dev \
    libxinerama-dev \
    libxi-dev \
    libopengl0 \
    x11-utils \
    libxrandr2 \
    libxinerama1

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
RUN mkdir -p ~/hydra_ws/src
WORKDIR /root/hydra_ws

# Initialize Catkin workspace with configuration
RUN /bin/bash -c "source /opt/ros/noetic/setup.bash && \
    catkin init && \
    catkin config -DCMAKE_BUILD_TYPE=Release"

# Clone Hydra repository
RUN cd ~/hydra_ws/src && \
    git clone https://github.com/MIT-SPARK/Hydra.git hydra

# Verify that Hydra repo was cloned successfully
# RUN ls -la ~/hydra_ws/src/hydra

# Replace SSH URLs with HTTPS in hydra.rosinstall
RUN sed -i 's|git@github.com:|https://github.com/|g' ~/hydra_ws/src/hydra/install/hydra.rosinstall

# Import dependencies using vcs
WORKDIR /root/hydra_ws/src
# RUN ls -la ~/hydra_ws/src/hydra/install
RUN vcs import . < ~/hydra_ws/src/hydra/install/hydra.rosinstall

# Install dependencies using rosdep
# RUN ls -la ~/hydra_ws/src
WORKDIR /root/hydra_ws
RUN rosdep update &&  rosdep fix-permissions && \
    rosdep install --from-paths src --ignore-src -r -y

# Install dependencies using rosdep
RUN cd ~/hydra_ws && \
    /bin/bash -c "source /opt/ros/noetic/setup.bash && \
    catkin build"

# Add environment setup to bashrc for automatic sourcing
RUN echo "source /opt/ros/noetic/setup.bash" >> /root/.bashrc
RUN echo "source ~/hydra_ws/devel/setup.bash" >> /root/.bashrc

# Set the default entrypoint to bash
CMD ["/bin/bash"]

