Baxter SSH - Run Inside Baxter
======

A C++ version of the Baxter SDK that works along side the Rethink SDK. This is the base package that contains a ros_control implementation of the Baxter controllers, and it is intended to run on Baxter itself

### Features

 * Baxter ros_control position, velocity, or torque trajectory controllers
   * Uses the ros_control [joint_trajectory_controller](https://github.com/ros-controls/ros_controllers/tree/hydro-devel/joint_trajectory_controller) instead of the python trajectory controller that comes with the SDK

## Prerequisites

 * A Baxter with dual parallel electric grippers with SDK v1.0.0 installed
    
## Installation

* Create a catkin workspace if you don't already have one (we recommend a separate one for Baxter) 

    ```
    mkdir -p ~/ros/ws_baxter/src
    cd ~/ros/ws_baxter/src
    ```

* Install the following on Baxter:

   * baxter_common
   * cmake_modules
   * control_toolbox
   * realtime_tools
   * ros_control -b all_dev_combined
   * ros_controllers -b indigo_to_groovy_backport
   * xacro
   * control_msgs -b indigo-devel


## Bringup Baxter

### Hardware

 * Power on baxter

 * Ensure you have the correct ROS_MASTER_URI exported, this depends on your robot serial number. Mine is:
   ```
   export ROS_MASTER_URI=http://011305P0009.local:11311
   ```

   You might also need to set the ROS hostname environment variable if you have not already done so and you have communication issues:
   ```
   export ROS_HOSTNAME=128.138.244.72  # REPLACE WITH YOUR COMPUTER'S IP ADDRESS
   ```

 * Bringup ros_control controllers - starts a position-based trajectory controller. See [Hardware Control Modes](#hardware-control-modes) for other control modes
   ```
   roslaunch baxter_control baxter_hardware.launch
   ```

## Hardware Control Modes

This Baxter repository uses [ros_control](http://wiki.ros.org/ros_control) to send trajectories to Baxter via the joint_trajectory_controller. Trajectories can be executed on Baxter in either position mode or velocity mode. You can easily switch between the two - both are loaded at startup but position is started by default:

 * Position Control

   Load the position controllers (not loaded by default)
   ```
   rosrun controller_manager spawner --stopped position_joint_mode_controller left_position_trajectory_controller right_position_trajectory_controller --namespace /robot &
   ```
   Start the position controllers and stop the velocity controllers
   ```
   rosservice call /robot/controller_manager/switch_controller "{start_controllers: ['position_joint_mode_controller','left_position_trajectory_controller','right_position_trajectory_controller'], stop_controllers: ['velocity_joint_mode_controller','left_velocity_trajectory_controller','right_velocity_trajectory_controller'], strictness: 2}"
   ```
   Plot position error of position-based trajectory controller 
   ```
   roslaunch baxter_control joint_position_left_trajectory_controller.launch
   roslaunch baxter_control joint_position_right_trajectory_controller.launch
   ```

 * Velocity Control
   ```
   rosservice call /robot/controller_manager/switch_controller "{start_controllers: ['velocity_joint_mode_controller','left_velocity_trajectory_controller','right_velocity_trajectory_controller'], stop_controllers: ['position_joint_mode_controller','left_position_trajectory_controller','right_position_trajectory_controller'], strictness: 2}"
   ```
   Plot *position* error of velocity-based trajectory controller 
   ```
   roslaunch baxter_control joint_velocity_left_trajectory_controller.launch
   roslaunch baxter_control joint_velocity_right_trajectory_controller.launch
   ```

 * Torque Control

   TODO

## Programmed Buttons

### End Effector Cuff

 * Rectangular button: close end effector
 * Circular button: open end effector

### Back of Robot Shoulders

 * Left shoulder button: enable Baxter
 * Right shoulder button: disable Baxter

## Installing Other Software on Baxter

Some helpful notes for setting up your Baxter to be more useful. Its suggested you place all software in a folder ``software``

    cd ~
    mkdir software

### Install emacs

    wget SOME-MIRROR/emacs-24.3.tar.xz                                                                                                                                                                                                                                            
    tar xvfJ emacs-24.3.tar.xz                                                                                                                                                                                                                                                    
    cd emacs-24.3                                                                                                                                                                                                                                                                 
    ./configure --with-gif=no                                                                                                                                                                                                                                                     
    make                                                                                                                                                                                                                                                                          

Add to PATH:

    export PATH=$PATH:/home/ruser/software/emacs-24.3/lib-src/
    export PATH=$PATH:/home/ruser/software/emacs-24.3/src/

### Install colordiff

    wget http://www.colordiff.org/colordiff-1.0.13.tar.gz
    tar xvfz colordiff-1.0.13.tar.gz
    cd colordiff-1.0.13
    mkdir ~/bin ~/man ~/etc

Edit Makefile to say first three lines:

     INSTALL_DIR=~/bin
     MAN_DIR=~/man/man1
     ETC_DIR=~/etc

Install - The script will complain about changing ownership of colordiffrc - ignore the error.
    
     make install

Add to PATH in .bashrc

     export PATH=$PATH:/home/ruser/bin

### Install catkin_tools

First edit your .bashrc and add:

     export PYTHONPATH="/home/ruser/bin/catkin_tools/lib/python2.7/site-packages:$PYTHONPATH"

Source your .bashrc then

     cd ~/ros
     git clone https://github.com/catkin/catkin_tools.git
     cd catkin_tools/
     python setup.py install --prefix ~/bin/catkin_tools


## License

BSD (New BSD License)

## Contributors

Please help - see [Contribute](https://github.com/davetcoleman/baxter_cpp/blob/master/CONTRIBUTING.md) page.

