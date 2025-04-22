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
    libeigen3-dev \
    cmake \
    build-essential \
    unzip \
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
    ros-noetic-image-geometry \
    ros-noetic-pcl-ros \
    ros-noetic-cv-bridge \
    libtbb-dev \
    ros-noetic-cmake-modules \
    ros-noetic-grpc \
    libgoogle-glog-dev \ 
    ros-noetic-image-proc \
    libtool \
    ros-noetic-depth-image-proc \

    && rm -rf /var/lib/apt/lists/*

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


RUN cd ~/kimera_ws/src && \
    git clone https://github.com/MIT-SPARK/Kimera-Semantics.git 


# Import dependencies using vcs
WORKDIR /root/kimera_ws/src
RUN vcs import . < ~/kimera_ws/src/Kimera-Semantics/install/kimera_semantics_https.rosinstall

RUN sed -i '/project(kimera_semantics_ros)/a set(CMAKE_CXX_STANDARD 14)' /root/kimera_ws/src/Kimera-Semantics/kimera_semantics_ros/CMakeLists.txt

#RUN cat /root/kimera_ws/src/Kimera-Semantics/kimera_semantics_ros/CMakeLists.txt && exit 1

WORKDIR /root/kimera_ws
RUN rosdep update &&  rosdep fix-permissions && \
          rosdep install --from-paths src --ignore-src -r -y

#RUN rm -rf ~/kimera_ws/src/gtsam && apt update && apt install ros-noetic-gtsam

# Install dependencies using rosdep
RUN cd ~/kimera_ws && \
    /bin/bash -c "source /opt/ros/noetic/setup.bash &&  catkin build kimera_semantics_ros"  

# Add environment setup to bashrc for automatic sourcing
RUN echo "source /opt/ros/noetic/setup.bash" >> /root/.bashrc
RUN echo "source ~/kimera_ws/devel/setup.bash" >> /root/.bashrc
# Set the default entrypoint to bash
CMD ["/bin/bash"]


