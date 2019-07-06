# Bottleneck Guided Rapidly Exploring Random Tree Star

<div align='right'>
  <img src='images/Flow chart 2 (1).png' width='400px' align='right'>
</div>


The aim is to solve the problem of no 'one size fits all' heuristic for different motion planning problems and the inherent 'narrow passage' problem for uniform sampling based planners. 

The motion planner is implemented in C++ whereas the 3D CNN is implemented in Python using Tensorflow. The 3D CNN is a modified form of the [VoxNet](https://www.ri.cmu.edu/pub_files/2015/9/voxnet_maturana_scherer_iros15.pdf) architecture. 

### Modifications :

* Multi Input Single Output CNN 
  * 1st Input : Voxelized Environment
  * 2nd Input : Start and End points selected by user in Rviz
  * Output : Relevant Bottleneck Points
* No softmax non linearity in the last layer since ours is a prediction task (not classification)
* 9 units in the last dense layer to predict 3 sets of x,y,z coordinates of relevant bottleneck points

Video demo of this work is available at : https://www.youtube.com/watch?v=28sW9mFheOY&feature=youtu.be
### Training :

* Since data was limited for our prediction task, we have used transfer learning from a pre trained VoxNet trained on the Sydney Urban Objects Dataset.
* Our manually labelled dataset in the labelled_data folder consists of 200 data samples collected using a 10% Goal Biased RRT* on different hand engineered environments, where inputs are the voxelized environments, start and end points are 1D arrays of 3D points generated using rviz and outputs are the 1D arrays of three bottleneck points each.

## RRT* Implementation Details 
* [FCL](https://github.com/flexible-collision-library/fcl) : Collision Checking with environment
* [Octomap](https://github.com/OctoMap/octomap) : Data Structure for the 3D Environment
* [Mersenne Twister PRNG](https://github.com/cslarsen/mersenne-twister) : Sampling Random Points in Workspace of Manipulator
* [OpenRAVE](https://github.com/rdiankov/openrave) :Kinematic Reachability Module used to find sampling domain of the planner.
* [libNABO](https://github.com/ethz-asl/libnabo) :Library for nearest neighbour searches to find multiple parent candidates
* [H5Easy](https://github.com/stevenwalton/H5Easy): To convert hdf5 file created by openrave using C++ vectors
* [ROS](https://github.com/ros): rviz visualization and sensor data acquisition
* [moveit cartesian plan plugin](https://github.com/ros-industrial-consortium/fermi/tree/hydro-devel): For taking user input for 3D start and end points in rviz
*  [abb_driver](https://github.com/ros-industrial/abb/tree/kinetic-devel/abb_driver): ROS industrial abb package for downloading joint space trajectories to IRC5 controller 

## Instructions 
* The Robot, motion planning markers and the Octomap environment can be visualized in rviz and moveit_cartesian_plan_plugin can be used to select the start and endpoints by using :
```bash
roslaunch abb_1410_moveit_config moveit_planning_execution.launch
```
* The main ROS package is called abb_manipulator since all experiments were conducted on the ABB 1410 welding robot, all the above listed packages in RRT* implementation details appear as dependencies in the CMakeList.txt file and must be compiled in your workspace
* The bottleneck guided RRT* and other variations of RRT can be found in abb_manipulator/src folder and can be executed using
```bash
rosrun abb_manipulator rrt_star_milestones
```
But our motion planner will not start searching until it has received the start, end points from rviz, octomap environment and the milestone points from the 3D CNN


## Comparison of traditional fixed heuristic RRT planners 
<div align='centre'>
  <img src='images/random exploration.png' width='800px' align='centre'>
</div>

## Performance of Bottleneck Guided RRT*
<div align='centre'>
  <img src='images/Intelligent Exploration.png' width='800px' align='centre'>
</div>

## References 
* [Execution Extended RRT](https://link.springer.com/content/pdf/10.1007/978-3-540-45135-8_23.pdf)
* [Synchronized Greedy Biased RRT](https://link.springer.com/content/pdf/10.1007/s12555-011-0417-7.pdf)




