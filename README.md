## Assets Repo for RoboSim


本仓库包含了不同模拟器后端的资源目录。按照模拟器类型区分存放目录，`gazebo-11/assets` 存放 Gazebo Classic 的资源，`mujoco/assets` 存放 MuJoCo 的资源，`pybullet/assets` 存放 PyBullet 的资源。

现在支持的机器人：

- `gazebo/assets/robots/diffdrive_car`
- `mujoco/assets/robots/franka_panda`
- `mujoco/assets/robots/robot_vacuum`
- `mujoco/assets/robots/vx300s_cohesive`
- `mujoco/assets/robots/unitree_g1`
- `pybullet/assets/robots/simple_arm`

现在支持的场景：

- `gazebo/assets/worlds/sdf/aws_house.world`
- `gazebo/assets/worlds/sdf/standard_room.world`
- `mujoco/assets/worlds/bedroom`
- `mujoco/assets/worlds/cafe`
- `mujoco/assets/worlds/oldroom`
- `mujoco/assets/worlds/two_bedroom_apartment`



### MuJoCo 资产规约

- `worlds/`：存放的场景入口与场景本地 mesh / texture 资源。默认场景文件名（入口）为 `scene.xml`；

- `robots/`：存放被场景直接引用的机器人模型资源（MJCF 格式）；


> [!NOTE]
>
> 如果您需要为 MuJoCo 后端添加新的机器人，除了需要准备 XML 文件（MJCF）以外，还需要提供一个语义化的 SRDF 文件，用于描述机器人的关节模型组（Joint Model Group）。
> 
> “关节模型组”（JointModelGroup）是完全由用户定义的关节分组（在用户的 SRDF 文件中指定）。每个 JointModelGroup 可以包含 ≥ 0 个末端执行器和 ≥ 0 个固定姿态（组状态）。
> 
> 使用 JointModelGroup 抽象概念，可以非常方便地选择一系列关节和末端执行器进行驱动。这是基于 Moveit2 JointModelGroup 定义的概念。
> 
> 您可以自行手写一份 SRDF 文件，模仿 [`mujoco/assets/robots/franka_panda/panda.srdf`](./mujoco/assets/robots/franka_panda/panda.srdf) 的内容，您可以自由规定哪些关节在一个组里面最方便您之后的操纵。如不提供，robosim 会将所有位于一个 kinematic tree 上的关节放在一个 JointModelGroup 中。
> 
> 您也可以借助 ROS2 Moveit2 官方提供的 wizard（`ros2 run moveit_setup_assistant moveit_set
up_assistant`），导入您的机器人模型，一步步地生成，拿到其中的 `*.srdf` 即可；


### PyBullet 资产规约

- `robots/`：存放 PyBullet 后端可直接加载的 URDF 机器人资源。v1 默认入口文件名为 `robot.urdf`；

- `--scene`：PyBullet 后端 v1 要求传入 URDF 文件路径，例如 `pybullet/assets/robots/simple_arm/robot.urdf`；

- `*.srdf`：可选。若 URDF 同目录下存在同名 SRDF（例如 `robot.srdf`），robosim 会读取其中的 JointModelGroup、group state 和 end-effector 定义；若没有提供，后端会提供默认的 `all` 关节组；若提供了 SRDF，则 PyBullet 后端只暴露 SRDF 中声明的关节模型组，与 MuJoCo 后端保持一致；

- `URDF <robosim>`：可选。PyBullet 后端会读取 URDF 根节点下的 `<robosim>` 块来注册后端专用传感器。当前支持 `<camera>` 和 `<force_torque>`：

  ```xml
  <robosim>
    <camera
      name="world_camera"
      link="world"
      xyz="1.2 -1.2 0.8"
      rpy="0.4 0 2.35"
      width="320"
      height="240"
      fov="60"
      near="0.01"
      far="10.0"/>

    <force_torque
      name="ft_sensor"
      joint="elbow_joint"/>
  </robosim>
  ```

  `camera` 的 `link` 默认为 `world`，也可以填写机器人 link 名；`xyz/rpy` 表示相对于该 link 的局部位姿；相机本地 `+X` 为 forward，`+Z` 为 up。`force_torque` 会暴露 `<name>_force` 和 `<name>_torque` 两个传感器；

- 当前 PyBullet 后端以固定基座方式加载 URDF，适合机械臂和桌面测试资产；移动底盘和多机器人场景后续应通过 manifest 明确描述；

- 当前 PyBullet 后端将传入 URDF 加载出的单个 body 作为机器人主体，暂不定义多机器人/多 body 场景 manifest。后续如需复杂 PyBullet 场景，应增加显式 manifest 来描述主机器人、传感器和附加物体；
