# ROS_Wheelchair
This repository is for the same goal of the prototype edited to be suitable for our Smart Wheelchair final product; Indoor Navigation using ROS. It also contains the description, mapping, localization, and navigation code for the wheelchair which takes the movement order from the Gaze Controller.


## The final application assumptions of `The Indoor Navigation`:
1. The patient's house is scanned, a complete grid-based map is created and saved, then all final destinations are defined on the map through coordinates.
2. The desired rooms/final goals location are mapped to i.e. letters A, B, C, and D which is sent to ROS system to move the wheelchair to the target place through a path free of obstacles to ensure safety.
3. While ROS System is run in this mode, the user is asked to the next destination.



## Package Descriptions

* *slam*: wheelchair description `URDF`, simulation `Gazebo`, visualization `RViz`, and mapping ` gmapping launch file`. </br>
* *move_robot*: sending goals to the ROS navigation Stack `wheelchair navigation` AMCL and Navigation parameters. </br>
* *odom_pub*: taking `odometry information` from encoder ticks on Arduino. </br>
* *teleop_twist_keyboard*: keyboard control of the wheelchair


## Build
Steps to build "each in a new terminal tab"
```
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/
catkin_make
source devel/setup.bash
cd src/
git clone https://github.com/RoboBrain23/ROS_Wheelchair.git
cd ~/catkin_ws/
catkin_make
```

## Run

### Mapping
To launch the urdf `Wheelchair description` model in Gazebo 
, start to view the simulated model and take information from the robot's camera in `RViz` 
, and moving the wheelchair in gazebo by keyboard <br />

```
roslaunch slam gazebo.launch
cd catkin_ws/src/slam/rviz 
rviz -d map.rviz
rosrun teleop_twist_keyboard teleop_twist_keyboard.py
```
* After setup, run SLAM: <br />
```
roslaunch slam gmapping.launch
cd catkin_ws/src/slam/rviz
rviz -d map.rviz
```

To create a map of your environment, drive the wheelchair around the environment you want to map.




https://github.com/RoboBrain23/ROS_Wheelchair/assets/87612394/a79694cb-047b-4ec1-8668-8dfc9a9f828a





Once you completed mapping the whole environment,
save the map:
```
rosrun map_server map_saver ~/catkin_ws/src/slam/maps/name_of_map
```

The final scanned in previous demo be like:


![final_map](https://github.com/RoboBrain23/ROS_Wheelchair/assets/87612394/e9b836d4-8078-4aa0-a03b-901498fc349d)



### Autonomous Navigation
Autonomous Navigation can be done using the following -- giving the final destination:
#### 1- Graphical User Interface (GUI).
launch the urdf model in Gazebo, and RViz visualization using the saved map <br />

```
roslaunch slam gazebo.launch
cd catkin_ws/src/slam/rviz 
rviz -d navigate.rviz
```
Using amcl package for localization, `Run amcl.launch`. The amcl algorithm implements Monte Carlo localization for state estimation.
We will use amcl with a map built in the previous step. In the second part, we will create our map using the gmapping package and then use the resulting map for localization. <br />

The move_base package implements an action that will attempt to reach it with the wheelchair. This node links a global and local planner to accomplish its global navigation task. The move_base node also maintains two costmaps, one for the global planner and another for a local planner that are used to accomplish navigation tasks. `Run move_base.launch`.<br />
```
roslaunch slam amcl.launch map:='name_of_map'
roslaunch slam move_base.launch 
```





https://github.com/RoboBrain23/ROS_Wheelchair/assets/87612394/706ae370-578b-4db2-966d-5786f716490d







* Red arrows, in the beginning, indicate the wheelchair probable location in RViz.
* Set a goal for the wheelchair in RVIZ (Click "2D Nav goal" and pinpoint the desired location and direction on the map).
* Green line indicates the path planned.
* The wheelchair avoids obstacles "according to the pre-saved map" and also `Dynamic obstacles` that are added when it navigates.

#### 2- Sending the goal location coordinates through the move_robot node.


launch the wheelchair model in Gazebo and simulate in RViz <br />
```
roslaunch slam gazebo.launch
cd catkin_ws/src/slam/rviz 
rviz -d navigate.rviz
```

Then run `amcl.launch`, giving the name of the map you have created and saved. Run `move_base.launch` to move the wheelchair though the path planned by the global planner to accomplish the global navigation task. Finally send the location coordinates of the gaol to navigation-stack using ` move_robot node`: <br />
```
roslaunch slam amcl.launch map:='name_of_map'
roslaunch slam move_base.launch 
rosrun move_robot move_robot
```


https://github.com/RoboBrain23/ROS_Wheelchair/assets/87612394/93d3f4ab-45fd-462c-895a-714dedc669b3


