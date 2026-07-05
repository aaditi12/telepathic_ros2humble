# telepathic_ros2humble
Same project as just above — here are the run commands again.

## Setup

```bash
sudo apt update
sudo apt install -y ros-humble-gazebo-ros-pkgs ros-humble-xacro \
                     ros-humble-robot-state-publisher python3-numpy
```

## Build

```bash
cd telepathic_comm_ws
rosdep install --from-paths src --ignore-src -r -y
colcon build --symlink-install
source install/setup.bash
```

## Run — Option A: AI pipeline only, no Gazebo

```bash
ros2 launch telepathy_bringup pipeline_only.launch.py
```
Watch the terminal — the simulated subject switches intent every ~4s, and the broadcaster logs `>>> TELEPATHIC BROADCAST: "..."` when the stabilized intent changes.

## Run — Option B: full digital-twin swarm in Gazebo

```bash
ros2 launch telepathy_bringup digital_twin_swarm.launch.py num_robots:=3
```
Spawns 3 robots (`robot_1`, `robot_2`, `robot_3`) that all move in lockstep, since they all subscribe to the same unaddressed `/telepathy/broadcast` topic.

## Inspect topics live

```bash
ros2 topic echo /eeg/raw --field true_intent_label
ros2 topic echo /telepathy/intent
ros2 topic echo /telepathy/broadcast
ros2 topic echo /robot_1/cmd_vel
```

## Visualize in RViz

```bash
rviz2 -d src/telepathy_bringup/rviz/swarm.rviz
```

## Tunable parameters

| Node | Parameter | Default |
|---|---|---|
| `eeg_simulator_node` | `intent_switch_period_sec` | 4.0 |
| `eeg_simulator_node` | `noise_std` | 0.3 |
| `intent_decoder_node` | `calibration_windows_per_class` | 40 |
| `telepathic_broadcaster_node` | `smoothing_window` | 5 |
| `telepathic_broadcaster_node` | `min_confidence` | 0.3 |
| `telepathy_receiver` | `min_confidence` | 0.3 |

Override example:
```bash
ros2 launch telepathy_bringup pipeline_only.launch.py --ros-args -p eeg_simulator_node.intent_switch_period_sec:=2.0
```

Needs Ubuntu 22.04 + ROS 2 Humble (Gazebo only for Option B) — won't run in this sandbox.
