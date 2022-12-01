# Command-Line Interface Tools
## Configuring Environment

#### installation:
 
https://docs.ros.org/en/foxy/Installation/Ubuntu-Install-Debians.html 

set locale

setup sources

install ROS2 packages

#### switching between ROS1 and ROS2:

https://automaticaddison.com/how-to-switch-between-ros-1-and-ros-2-in-ubuntu-linux/ 

#### using ROS1 bridge:

https://github.com/ros2/ros1_bridge/blob/master/README.md 

skip this for now (we will get back to this after understanding/practicing more ROS2)

#### sourcing the setup script:

$ gedit ~/.bashrc

`source /opt/ros/foxy/setup.bash` add this line at the bottom of the script

#### check environment variables:

$ printenv | grep -i ROS

#### ROS2 demo test:

$ ros2 run demo_nodes_cpp talker

$ ros2 run demo_nodes_py listener

#### terminologies:

core workspace = “underlay”

local workspaces = “overlay”

#### `ROS_DOMAIN_ID` variable:

$ gedit ~/.bashrc

`export ROS_DOMAIN_ID=<your_domain_id>` add this line at the bottom of the script

this should already be defaulted with ROS1 installation (at least for me)

more info: https://docs.ros.org/en/foxy/Concepts/About-Domain-ID.html 

### Using `turtlesim` and `rqt`

#### terminologies:

turtlesim = lightweight simulator for learning ROS2 (ROS1 has its own too!)

rqt = graphical user interface tool for ROS2 (same with ROS1) to manipulate ROS2 elements

#### core concepts:

nodes

topics

services

#### install turtlesim:

$ sudo apt update

$ sudo apt install ros-foxy-turtlesim

#### run turtlesim:

$ ros2 run turtlesim turtlesim_node

$ ros2 run turtlesim turtle_teleop_key

notice how we don’t need a `roscore` or `ros master` client!

use up and down arrow keys to move forward and backward (velocity command)

use right and left arrow keys to rotate clockwise and counterclockwise

use:

e r t

d _ g

c v b

use f to stop rotation (while turtle is rotating)

#### another useful `list` command to see nodes and their associated services, topics, actions:

$ ros2 node list

$ ros2 topic list

$ ros2 service list

$ ros2 action list

more on these concepts later…

#### install rqt:

$ sudo apt update

$ sudo apt install ~nros-foxy-rqt*

#### run rqt:

$ rqt

select Plugins > Services > Service Caller

`turtlesim_node` and `turtle_teleop_key` should be running

refresh service and drop down to `/spawn`

#### try the spawn service:

set x to 1.0

set y to 1.0

set name to ‘turtle2’ (with the quotes)

click on `call`

check back on turtlesim window and you can see a new turtle spawning!

refresh service list in rqt and you can also see turtle2 service `/turtle2/…`

#### try the set_pen service:

select the service `/turtle1/set_pen` and change:

r to 255

width to 5

don’t forget to `call`

how do we control turtle2? we can remap turtle1’s `cmd_vel` topic onto turtle2

#### remapping:

new terminal (and source ROS2 if it’s not yet in ~/.bashrc)

$ ros2 run turtlesim turtle_teleop_key --ros-args --remap turtle1/cmd_vel:=turtle2/cmd_vel

this terminal becomes a separate, but identical, controller for turtle2

#### close turtlesim:

`ctrl+c` in `turtlesim_node`

`q` in `turtle_teleop_key` (i guess you can `ctrl+c` too but the documentation didn’t say this)

`ctrl+c` in `rqt`

## Nodes

#### visualizing a robotic system made up of nodes (ROS2 graph):

<img src="https://docs.ros.org/en/foxy/_images/Nodes-TopicandService.gif"/>

`ROS2 graph` is a network of ROS2 elements processing data together at one time

this encompasses all executables and connections between them

each node in ROS2 should be responsible for a single, module purpose (e.g. one node for controlling wheel motors, one node for controlling a laser range-
finder, etc.)

each node can send and receive data to other nodes via topics, services, actions, parameters

a full robotic system is compromised of many nodes working in concert

in ROS2, a single executable program (c++, python, etc.) can contain one or more nodes.

#### ros2 run:

`$ ros2 run <package_name> <executable_name>` launches an executable from a package

e.g.: `$ ros2 run turtlesim turtlesim_node`

`turtlesim` = package name

`turtlesim_node` = executable name

how do we find node names? we can use `$ ros2 node list`

#### ros2 node list:

if you run `turtlesim_node` and `turtle_teleop_key`

`$ ros2 node list` will return:

/turtlesim

/teleop_turtle

these are the names of the two active nodes

#### remapping revisited (to duplicate and change node name):

remapping allows us to reassign default node properties, like node name, topic names, service names, etc. to custom values

last time we remapped `turtle_teleop_key` to change the default turtle being controlled

https://design.ros2.org/articles/ros_command_line_arguments.html#name-remapping-rules 

#### to reassign the name of our `/turtlesim` node:

$ ros2 run turtlesim turtlesim_node --ros-args --remap __node:=my_turtle

this should open a new turtlesim window

now, if we open a new terminal and write `$ ros2 node list`, we should see a new node name `/my_turtle`

#### ros2 node info:

`$ ros2 node info <node_name>` allows us to access more information about that node

e.g.: `ros2 node info /my_turtle` returns a list of subscribers, publishers, services, and actions (the ROS2 graph connections) that interact with that node. notice the node name includes the `/`

try finding node info for `/teleop_turtle` and see how its connections differ from `my_turtle`

#### summary:

a node is a fundamental ROS2 element that serves a single, modular purpose in a robotics system

we utilized nodes created from the `turtlesim` package by running the executables `turtlesim_node` and `turtle_teleop_key`

we learned to use tools like `ros2 node list` to discover active node names and `ros2 node info` to introspect on a single node

these tools are vital to understanding the flow of data in a complex, real-world robot system

next, we will learn about topics – one of the communication types that connects nodes

## Topics

<img src="https://docs.ros.org/en/foxy/_images/Topic-MultiplePublisherandMultipleSubscriber.gif"/>

visualizing a robotic system made up of nodes (ROS2 graph):

topics acts as a bus for nodes to exchange messages. they don’t have to only be one-to-one communication but can also be one-to-many or many-to-many

### startup turtlesim:

$ ros2 run turtlesim turtlesim_node

$ ros2 run turtlesim turtle_teleop_key

node names are:

/turtlesim

/teleop_turtle

### rqt_graph:

$ rqt_graph

visualize all changing nodes and topics as well as the connections between them

i set group to 0 and view to nodes/topics (active)

nodes are in circles

topics are in squares

actions are in the periphery with the name `_action` in its path

`/teleop_turtle` is publishing data (the keystrokes you enter to move the turtle around) to the `/turtle1/cmd_vel` topic, and the `/turtlesim` node is subscribed to that topic to receive the data

hovering with a mouse cursor highlights how each element is interacting with the system

### ros2 topic list:

$ ros2 topic list

returns a list of all the topics currently active in the system

$ ros2 topic list -t

returns the same list of all the topics but with the topic `type` appended in brackets

these `type` or attributes are how nodes know they are talking about the same information as it moves over topics

you can uncheck all the boxes under hide to see where all these topics are in rqt_graph but leave those options checked for now to avoid confusion

### ros2 topic echo:
$ ros2 topic echo <topic_name>
use `echo` to introspect `/turtle1/cmd_vel` which takes `/teleop_turtle` published data to `/turtlesim`
$ ros2 topic echo /turtle1/cmd_vel
returns nothing right now because it is waiting for `/teleop_turtle` to publish something
going back to terminal with `turtle_teleop_key`, use arrow keys to move the turtle around and watch the terminal with `echo` returning position data being published

uncheck `debug`
`/_ros2cli_…` is the node created by the `echo`
now we can see the publisher is publishing data over the `cmd_vel` topic, and the two subscribers are subscribed

### ros2 topic info:
counting the number of publisher and subscription of a topic
$ ros2 topic info /turtle1/cmd_vel

### ros2 interface show:
publishers and subscribers must send and receive the `same type` of message to communicate
`ros2 topic list -t` where the `cmd_vel` topic has the type `geometry_msgs/msg/Twist`
this means that the package `geometry_msgs` has a `msg` called `Twist`
$ ros2 interface show geometry_msgs/msg/Twist
`/turtlesim` node is expecting (subscribed to) a message with two vectors `linear` and `angular` of three elements each `x` `y` `z`
this is what `Vector3` refers to

### ros2 topic pub:
$ ros2 topic pub <topic_name> <msg_type> ‘<args>’
can publish data onto a topic directly from the command line
the `’<args>’` argument is the actual data you’ll pass to the topic in the 2 vectors of 3 elements structure
this argument must be input YAML syntax like so:
$ ros2 topic pub --once /turtle1/cmd_vel geometry_msgs/msg/Twist “{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.8}}”
`--once` is an optional argument meaning “publish one message then “exit”

simulation and real robots require a steady stream of commands to operate continuously
$ ros2 topic pub --rate 1 /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.8}}"
instead of `--once` option and using `--rate 1` option, `ros2 topic pub` publishes the command in a stead stream at 1 Hz
refresh rqt_graph to see what’s happening graphically. you will see now a new node `/_ros2cli_…` is publishing over the `/turtle1/cmd_vel` topic and is being received by the `/turtlesim` and `echo` node

$ ros2 topic echo /turtle1/pose
returns x, y, theta, linear_velocity, angular_velocity instantaneously
refresh rqt_graph
now `/turtlesim` node is also publishing to the `pose` topic, which the new `echo` node is subscribed to


### ros2 topic hz:
view the rate at which data is published
$ ros2 topic hz /turtle1/pose
returns data on the rate at which the /turtlesim node is publishing data to the `pose` topic
we set the rate of `turtle1/cmd_vel` to publish at a steady 1 Hz using `ros2 topic pub --rate 1`
if we run the above command with
$ ros2 topic hz /turtle1/cmd_vel
we will see an average reflecting that rate and also see a new cli node in rqt_graph

### clean up:
ctrl+c in each terminal to terminate all the many nodes currently running

### summary:
nodes publish information over topics, which allows any number of other nodes to subscribe to and access that information. we saw the connections between several nodes over topics using rqt_graph and command line tools to have a good idea of how data moves around ROS2

# Using Fast Data-Distribution-Service (DDS) Discovery Server as discovery protocol

TODO

https://docs.ros.org/en/foxy/Tutorials/Advanced/Discovery-Server/Discovery-Server.html

https://fast-dds.docs.eprosima.com/en/v2.1.0/index.html

https://docs.ros.org/en/foxy/How-To-Guides/DDS-tuning.html

# Using Quality of Service (QoS) for lossy networks

TODO

https://docs.ros.org/en/foxy/Tutorials/Demos/Quality-of-Service.html
