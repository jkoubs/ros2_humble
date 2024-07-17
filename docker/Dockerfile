# FROM osrf/ros:humble-desktop
FROM osrf/ros:humble-desktop
ENV DEBIAN_FRONTEND=noninteractive
SHELL ["/bin/bash", "-c"]

# dependencies
RUN apt-get update --fix-missing && \
    apt-get install -y git && \
    apt-get install -y build-essential \
                       terminator \
                       nano \
                       vim \
                       python3-pip \
                       libeigen3-dev \
                       tree \
                       wget \
                       bash-completion \
                       gedit \
                       ros-humble-turtlebot3-gazebo \
                       ros-humble-nav2-simple-commander \
                       ros-humble-turtlebot3 \
                       ros-humble-xacro \
                       ros-humble-joint-state-publisher \
                       ros-humble-joint-state-publisher-gui \
                       ros-humble-gazebo-ros-pkgs \
                       ros-humble-rviz2 \
                       ros-humble-rmw-cyclonedds-cpp \
                       ros-humble-robot-localization \
                       ros-humble-mapviz \
                       ros-humble-mapviz-plugins \
                       ros-humble-tile-map \
                       qtbase5-dev \
                       libqt5svg5-dev \
                       libzmq3-dev \
                       libdw-dev
                       
RUN apt-get -y dist-upgrade
RUN pip3 install transforms3d

# Create env_ws containing the turtlebot3 pkgs
RUN mkdir -p /env_ws/src/turtlebot3_pkgs

# Source and Git clone turtlebot3_simulations, turtlebot3_msgs and turtlebot3 pkgs 
RUN source /opt/ros/humble/setup.bash && \
    apt-get update --fix-missing && \
    cd /env_ws/src/turtlebot3_pkgs  && \
    git clone -b humble-devel https://github.com/ROBOTIS-GIT/turtlebot3_simulations.git && \
    git clone -b humble-devel https://github.com/ROBOTIS-GIT/turtlebot3_msgs.git && \
    git clone -b humble-devel https://github.com/ROBOTIS-GIT/turtlebot3.git && \
    cd /env_ws/src  && \
    git clone https://github.com/BehaviorTree/Groot.git && \
    cd Groot && \
    git submodule update --init --recursive && \
    mkdir build; cd build && \
    cmake .. && \
    make && \
    cd /env_ws && \
    # This installs dependencies declared in package.xml from all pkgs in the src folder for ROS 2 Humble
    rosdep install -i --from-path src --rosdistro humble -y && \
    # Build env_ws
    colcon build --symlink-install

# Source automatically the underlay (all necessary setup to run ROS2) (add it to .bashrc file)
RUN echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
# Source automatically the overlay (workspace -> env_ws) (add it to .bashrc file)
RUN echo "source /env_ws/install/setup.bash" >> ~/.bashrc
RUN echo "source /usr/share/gazebo/setup.sh" >> ~/.bashrc

COPY ros_entrypoint.sh /ros_entrypoint.sh
RUN chmod +x /ros_entrypoint.sh

# Set Working directory
WORKDIR '/'
ENTRYPOINT ["/ros_entrypoint.sh"]
CMD ["bash"]