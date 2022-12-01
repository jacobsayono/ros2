# ros1_bridge

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
