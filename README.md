# ICUAS25_finals
This is the repo for the final stage of the ICUAS competition 2025. that is in Charlotte.
It is based  on the one for the [simulation stage](https://github.com/larics/icuas25_competition), so for the basic instructions on Docker installation please check that repo. This one is adapted for flying with real UAVs, therefore, before starting it, please follow the prerequisites instructions:

### Motion Capture tracking - prerequisites: 
To obtain data from motion capture system, please install locally [NatNetSDK](https://github.com/whoenig/NatNetSDKCrossplatform) as given in README instructions.

### USB permissions - prerequisites: 
Copy file: `to_copy/99-bitcraze.rules` to  `/etc/udev/rules.d` directory. Please check these links on USB permissions: [crazyradio](https://www.bitcraze.io/documentation/repository/crazyflie-lib-python/master/installation/usb_permissions/). This enables to use the USB Radio and Crazyflie 2 over USB without being root.

## Run Crazyflies in the Docker container
### Setting up
Clone the [repository]([https://github.com/larics/icuas25_finals]):
```
git clone git@github.com:larics/icuas25_finals.git

```
Add  to  `~/.bashrc` and source it, or type in the current terminal: 
```
export DOCKER_BUILDKIT=1
```
Run Dockerfile from the project root directory using the following commands:
```bash
# Build a Dockerfile
docker build -t icuas_competition_img . 

# Run the crazyswarm2_img container for the first time
./first_run.sh


# This will create docker container icuas_competition container and position you into the container
```
When in docker build the ros2 workspace: 
```
cd ros2_ws
colcon build --symlink-install --merge-install 
```
For future runs, you can use the following commands:
```
# Start the crazyflies_cont:
docker start -i icuas_competition_cont

# Open the crazyflies_cont in another terminal, while it is already started:
docker exec -it icuas_competition_cont bash

# Stop the conatainer
docker stop icuas_competition_cont

# Delete the container
docker rm icuas_competition_cont
```
## Connecting to crazyflies
To connect to crazyflie you must plug in the Crazyradio [flashed USB dongle](https://www.bitcraze.io/documentation/tutorials/getting-started-with-crazyradio-2-0/) into the laptop. 

The general application [cfclient](https://www.bitcraze.io/documentation/repository/crazyflie-clients-python/master/userguides/userguide_client/)to connect to crazyflie can be started with (this shouldn't be running while crazyswarm2 packages are launched):

```
cfclient
```
For connecting with ROS2 crazyswarm2 package follow [instructions] (https://imrclab.github.io/crazyswarm2/usage.html)

## Start:

To connect with crazyflies with crazyswarm2 navigate to `/root/ros2_ws/src/icuas25_competition/startup`. and run `./start.sh`. This will start node to connect the camera to the wifi, crazyflie_server_reconnect (with reconnection property), octomap_server and the emergency script. Mentioned programms are obligated to run the whole time during your run! You can start your code only after all crazyflies are fully connected. Once they are fully connected, the topic `/mission start` will publish `True` message.


### Battery Monitoring
Information about the battery is available on topic `/cf_x/status`, where `cf_x` is the namespace of a specific Crazyflie. Message type is [`Status`](https://github.com/IMRCLab/crazyswarm2/blob/main/crazyflie_interfaces/msg/Status.msg) defined in `crazyflie_interfaces` of `Crazyswarm2` repo. The field `battery_voltage` indicates battery level in Volts. You can either follow its status on your own, or/and  you can subscribe to topic `/return_to_base`. This topic publishes `Bool` message. If `True` it signals that battery is at critical level and you have enough charge to safely return to base. Battery is considered fully charged if its voltage level is above 4V.


## INFO part
If you are working in the group and you are all using the same network, please check [ROS_DOMAIN_ID](https://docs.ros.org/en/eloquent/Tutorials/Configuring-ROS2-Environment.html#the-ros-domain-id-variable) in .bashrc in container. Random number should be set during the build, however it is not impossible that some of you got the same number. If that is the situatation please change it, so that your simulations do  not crash.

General information about Crazyflies can be found [here](https://www.bitcraze.io/products/crazyflie-2-1/).

Tutorials on Crazyflies are [here](https://www.bitcraze.io/documentation/start/).

General information about Cfclient can be found [here](https://www.bitcraze.io/documentation/repository/crazyflie-clients-python/master/userguides/userguide_client/).

More info about Crazyswarm2 is described in [here](https://imrclab.github.io/crazyswarm2/).

The provided Docker image comes with a few preinstalled tools and configs which may simplify your life.
**Tmuxinator** is a tool that allows you to start a tmux session with a complex layout and automatically run commands by configuring a simple yaml configuration file. Tmux is a terminal multiplexer - it can run multiple terminal windows inside a single window. This approach is simpler than having to do `docker exec` every time you need a new terminal. You can move between terminal panes by holding down `Ctrl` key and navigating with arrow keys. Switching between tabs is done with `Shift` and arrow keys. If you have a lot of open panes and tabs in your tmux, you can simply kill everything and exit by pressing `Ctrl+b` and then `k`.
Here are some links: [Tmuxinator](https://github.com/tmuxinator/tmuxinator), [Getting starded with Tmux](https://linuxize.com/post/getting-started-with-tmux/), [Tmux Cheat Sheet](https://tmuxcheatsheet.com/)
