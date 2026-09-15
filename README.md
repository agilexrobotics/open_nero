# Open Nero

Open Nero 是一个基于 ROS 2 的双臂机器人描述项目，提供整机 URDF/Xacro、网格模型和 RViz2 可视化配置，用于检查机器人结构、关节运动和 TF 关系。

本仓库只提供机器人模型与可视化资源，不包含真实机械臂驱动、通信协议或整机运动规划配置。

## 机器人组成

Open Nero 整机模型包含：

- 一个机器人底座
- 左右两台 7 自由度 Nero 机械臂
- 左右两个双指电动夹爪
- 两台末端 RealSense D435 相机
- 一套可旋转的顶部相机支架
- 一台顶部 RealSense D435 相机

左右机械臂分别使用 `left_` 和 `right_` 前缀，避免关节名和连杆名冲突。

## 目录结构

```text
open_nero/
├── open_nero_description/
│   ├── launch/
│   │   └── display.launch.py       # RViz2 可视化入口
│   ├── meshes/                     # 底座和相机支架网格
│   ├── rviz/
│   │   └── display.rviz            # RViz2 配置
│   └── urdf/
│       ├── dual_nero.xacro         # Open Nero 整机 Xacro 入口
│       ├── open_nero_description.urdf
│       ├── nero_gripper_d435.urdf  # Nero、夹爪与 D435 组合模型
│       ├── dual_nero.urdf           # 已生成的双臂 URDF
│       └── dual_nero_isaac.urdf     # Isaac Sim 使用的双臂 URDF
└── agx_arm_sim/                    # Nero 和 D435 模型依赖子模块
```

## 环境要求

仓库现有配置面向 ROS 2，相关子模块文档使用 ROS 2 Humble。

安装模型显示所需依赖：

```bash
sudo apt install \
  ros-humble-robot-state-publisher \
  ros-humble-joint-state-publisher \
  ros-humble-joint-state-publisher-gui \
  ros-humble-rviz2 \
  ros-humble-xacro
```

## 获取源码

建议将仓库克隆到 ROS 2 工作空间的 `src` 目录，并递归初始化子模块：

```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws/src
git clone --recurse-submodules https://github.com/agilexrobotics/open_nero.git
cd open_nero
git submodule update --init --recursive
```

子模块包含 Nero 机械臂和 RealSense D435 的基础模型。未初始化子模块时，Open Nero 整机模型无法完整加载。

## 构建

```bash
cd ~/ros2_ws
source /opt/ros/humble/setup.bash
rosdep install --from-paths src --ignore-src -r -y
colcon build
source install/setup.bash
```

## RViz2 可视化

启动 Open Nero 双臂整机模型：

```bash
ros2 launch open_nero_description display.launch.py
```

![](./open_nero.png)

启动后会运行：

- `robot_state_publisher`：发布机器人 TF
- `joint_state_publisher_gui`：通过滑条调整关节角度
- `rviz2`：显示整机模型、坐标系和运动状态

### Launch 参数

| 参数 | 默认值 | 说明 |
| --- | --- | --- |
| `gui` | `true` | 是否启动关节滑条界面 |
| `model` | `dual_nero.xacro` | 要加载的 URDF/Xacro 绝对路径 |
| `rvizconfig` | `display.rviz` | RViz2 配置文件绝对路径 |

不启动关节滑条界面：

```bash
ros2 launch open_nero_description display.launch.py gui:=false
```



