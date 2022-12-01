# Building ros1_bridge

## Bridging ROS1 (Noetic) and ROS2 (Foxy)

### Main document:
https://github.com/ros2/ros1_bridge/blob/foxy/README.md

### How ros2-ros1 bridge works:
https://github.com/ros2/ros1_bridge/blob/foxy/doc/index.rst

okay, now we're ready to start!

### Clone repo from FOXY TREE:
https://github.com/ros2/ros1_bridge/tree/foxy

MAKE SURE YOU CLONE THE FOXY TREE!!! (I spent days trying to figure out the problem but you're welcome! <3)

`$ git clone -b foxy https://github.com/ros2/ros1_bridge.git`

`$ cd ros1_bridge/`

`$ colcon build --symlink-install --packages-skip ros1_bridge`

`$ source /opt/ros/noetic/setup.bash`

`$ source /opt/ros/foxy/setup.bash`

`$ colcon build --symlink-install --packages-select ros1_bridge --cmake-force-configure`

### Now we are ready to reserve 1 terminal for our ros1_bridge

The documentation does not say this, but ros1_bridge works like a `ros2 workspace folder`. this means that you have to source this folder!

In `.bashrc`, which you can edit by typing `gedit ~/.bashrc` I alias 3 things:

`alias noetic='source /opt/ros/noetic/setup.bash'`

`alias foxy='source /opt/ros/foxy/setup.bash'`

`alias bridge='source ~/ros1_bridge/install/setup.bash'`

That way, when you want to run ros1_bridge, which is a ros2 workspace, you can use 1 terminal for this. Your terminal command lines should be something like:

`$ noetic`

`$ foxy`

`$ bridge`

`$ ros2 run ros1_bridge dynamic_bridge`

IN THIS ORDER!!! (very very important)

### I hope this helps new beginners (like me) trying to get into ROS2 and ROS1!

So if you want one terminal for ROS1 stuff, just source it by typing `noetic` in the command line. And `foxy` for a ROS2 terminal.

ros1_bridge is more special so you type in `noetic` and then `foxy` and then `bridge` and then `ros2 run ros1_bridge dynamic_bridge`.

You can also alias that last command, but note that there are different types of bridges!

# Using multiple ros1_bridge

I have not seen any examples online, but if you need to run a SEPARATE ros1+ros2 workspaces from another ros+ros2 workspaces on the same computer (for whatever reason), I would suggest you do the exact same steps except create a new ros1_bridge folder with a different name, say `ros1_bridge_ws`. Then glone the repo and cd into `ros1_bridge` (default name from the github) and source install/setup.bash on that terminal and do whatever ros2-ros1 stuff you need to do.

# Different options of ros1_bridge

If you type `ros2 run ros1_bridge dynamic_bridge` you can actually end the line with a double dash for more options `--`.

Here are the options:

`--show-introspection: Print output of introspection of both sides of the bridge.`

`--print-pairs: Print a list of the supported ROS 2 <=> ROS 1 conversion pairs.`

`--bridge-all-topics: Bridge all topics in both directions, whether or not there is a matching subscriber.`

`--bridge-all-1to2-topics: Bridge all ROS 1 topics to ROS 2, whether or not there is a matching subscriber.`

`--bridge-all-2to1-topics: Bridge all ROS 2 topics to ROS 1, whether or not there is a matching subscriber.`

Honestly, I haven't yet known when I would use any of these options. I am also curious if within the `ros1_bridge` package, what the `dynamic_bridge` node mean and what the other nodes mean. Here are the other node options (I used `tab` to show the possible fills).

`dynamic_bridge`                  
`parameter_bridge`                
`--prefix`                        
`ros1_bridge_generate_factories`  
`simple_bridge`                   
`simple_bridge_1_to_2`
`simple_bridge_2_to_1`
`static_bridge`
`test_ros2_client_cpp`
`test_ros2_server_cpp`
