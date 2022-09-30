[ROS2]
Beginner: Command-Line Interface Tools
Configuring Environment

installation:
https://docs.ros.org/en/foxy/Installation/Ubuntu-Install-Debians.html 
set locale
setup sources
install ROS2 packages

switching between ROS1 and ROS2:
https://automaticaddison.com/how-to-switch-between-ros-1-and-ros-2-in-ubuntu-linux/ 

using ROS1 bridge:
https://github.com/ros2/ros1_bridge/blob/master/README.md 
skip this for now (we will get back to this after understanding/practicing more ROS2)

sourcing the setup script:
$ gedit ~/.bashrc
`source /opt/ros/foxy/setup.bash` add this line at the bottom of the script

check environment variables:
$ printenv | grep -i ROS

ROS2 demo test:
$ ros2 run demo_nodes_cpp talker
$ ros2 run demo_nodes_py listener

terminologies:
core workspace = “underlay”
local workspaces = “overlay”

`ROS_DOMAIN_ID` variable:
$ gedit ~/.bashrc
`export ROS_DOMAIN_ID=<your_domain_id>` add this line at the bottom of the script
this should already be defaulted with ROS1 installation (at least for me)
more info: https://docs.ros.org/en/foxy/Concepts/About-Domain-ID.html 

Using `turtlesim` and `rqt`

terminologies:
turtlesim = lightweight simulator for learning ROS2 (ROS1 has its own too!)
rqt = graphical user interface tool for ROS2 (same with ROS1) to manipulate ROS2 elements

core concepts:
nodes
topics
services

install turtlesim:
$ sudo apt update
$ sudo apt install ros-foxy-turtlesim

run turtlesim:
$ ros2 run turtlesim turtlesim_node
$ ros2 run turtlesim turtle_teleop_key
notice how we don’t need a `roscore` or `ros master` client!
use up and down arrow keys to move forward and backward (velocity command)
use right and left arrow keys to rotate clockwise and counterclockwise
use:	e r t
d   g
c v b
use f to stop rotation (while turtle is rotating)
another useful `list` command to see nodes and their associated services, topics, actions:
$ ros2 node list
$ ros2 topic list
$ ros2 service list
$ ros2 action list
more on these concepts later…

install rqt:
$ sudo apt update
$ sudo apt install ~nros-foxy-rqt*

run rqt:
$ rqt
select Plugins > Services > Service Caller
`turtlesim_node` and `turtle_teleop_key` should be running
refresh service and drop down to `/spawn`

try the spawn service:
set x to 1.0
set y to 1.0
set name to ‘turtle2’ (with the quotes)
click on `call`
check back on turtlesim window and you can see a new turtle spawning!
refresh service list in rqt and you can also see turtle2 service `/turtle2/…`

try the set_pen service:
select the service `/turtle1/set_pen` and change:
r to 255
width to 5
don’t forget to `call`
how do we control turtle2? we can remap turtle1’s `cmd_vel` topic onto turtle2

remapping:
new terminal (and source ROS2 if it’s not yet in ~/.bashrc)
$ ros2 run turtlesim turtle_teleop_key --ros-args --remap turtle1/cmd_vel:=turtle2/cmd_vel
this terminal becomes a separate, but identical, controller for turtle2

close turtlesim:
`ctrl+c` in `turtlesim_node`
`q` in `turtle_teleop_key` (i guess you can `ctrl+c` too but the documentation didn’t say this)
`ctrl+c` in `rqt`
Nodes

visualizing a robotic system made up of nodes (ROS2 graph):
`ROS2 graph` is a network of ROS2 elements processing data together at one time
this encompasses all executables and connections between them
each node in ROS2 should be responsible for a single, module purpose (e.g. one node for controlling wheel motors, one node for controlling a laser range-finder, etc.)
each node can send and receive data to other nodes via topics, services, actions, parameters
a full robotic system is compromised of many nodes working in concert
in ROS2, a single executable program (c++, python, etc.) can contain one or more nodes.

ros2 run:
`$ ros2 run <package_name> <executable_name>` launches an executable from a package
e.g.: `$ ros2 run turtlesim turtlesim_node`
`turtlesim` = package name
`turtlesim_node` = executable name
how do we find node names? we can use `$ ros2 node list`

ros2 node list:
if you run `turtlesim_node` and `turtle_teleop_key`
`$ ros2 node list` will return:
/turtlesim
/teleop_turtle
these are the names of the two active nodes

remapping revisited (to duplicate and change node name):
remapping allows us to reassign default node properties, like node name, topic names, service names, etc. to custom values
last time we remapped `turtle_teleop_key` to change the default turtle being controlled
https://design.ros2.org/articles/ros_command_line_arguments.html#name-remapping-rules 
to reassign the name of our `/turtlesim` node:
$ ros2 run turtlesim turtlesim_node --ros-args --remap __node:=my_turtle
this should open a new turtlesim window
now, if we open a new terminal and write `$ ros2 node list`, we should see a new node name `/my_turtle`

ros2 node info:
`$ ros2 node info <node_name>` allows us to access more information about that node
e.g.: `ros2 node info /my_turtle` returns a list of subscribers, publishers, services, and actions (the ROS2 graph connections) that interact with that node. notice the node name includes the `/`
try finding node info for `/teleop_turtle` and see how its connections differ from `my_turtle`

summary:
a node is a fundamental ROS2 element that serves a single, modular purpose in a robotics system
we utilized nodes created from the `turtlesim` package by running the executables `turtlesim_node` and `turtle_teleop_key`
we learned to use tools like `ros2 node list` to discover active node names and `ros2 node info` to introspect on a single node
these tools are vital to understanding the flow of data in a complex, real-world robot system
next, we will learn about topics – one of the communication types that connects nodes
Topics

