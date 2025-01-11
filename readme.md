```
/ros2_ws
    /src
    /Bulnabi_DBF2025_CustomSim_v1
        /Bulnabi_DBF2025_CustomSim_v1
            __init__.py
        /config
            zephyr_controllers.yaml
        /models
            /standard_vtol
                ~
            /zephyr_delta_wing
                ~
        /resource
            ~
        /test
            ~
        package.xml
        readme.md
        setup.cfg
        setup.py
```



```
cd ~/ros2_ws/src

ros2 pkg create --build-type ament_python Bulnabi_DBF2025_CustomSim_v1

colcon build

source install/setup.bash
```



- Gazebo Classic (zephyr_delta_wing)
```
ros2 launch gazebo_ros gazebo.launch.py
ros2 run gazebo_ros spawn_entity.py -entity zephyr_delta_wing -file ~/ros2_ws/src/Bulnabi_DBF2025_CustomSim_v1/models/zephyr_delta_wing/model.sdf


ros2 launch gazebo_ros gazebo.launch.py parameters_file:=~/ros2_ws/src/Bulnabi_DBF2025_CustomSim_v1/config/zephyr_controllers.yaml
ros2 run gazebo_ros spawn_entity.py -entity zephyr_delta_wing -file ~/ros2_ws/src/Bulnabi_DBF2025_CustomSim_v1/models/zephyr_delta_wing/model.sdf
ros2 control load_controller velocity_controllers --set-state active
ros2 topic pub /velocity_controllers/commands std_msgs/msg/Float64MultiArray "data: [1.0]"

ros2 launch gazebo_ros gazebo.launch.py parameters_file:=~/ros2_ws/src/Bulnabi_DBF2025_CustomSim_v1/config/zephyr_controllers.yaml
ros2 run gazebo_ros spawn_entity.py -entity zephyr_delta_wing -file ~/ros2_ws/src/Bulnabi_DBF2025_CustomSim_v1/models/zephyr_delta_wing/model.sdf
ros2 service list | grep controller_manager

ros2 service call /controller_manager/list_controllers controller_manager_msgs/srv/ListControllers "{}"
```



```
ros2 launch gazebo_ros gazebo.launch.py parameters_file:=~/ros2_ws/src/Bulnabi_DBF2025_CustomSim_v1/config/zephyr_controllers.yaml
ros2 run gazebo_ros spawn_entity.py -entity zephyr_delta_wing -file ~/ros2_ws/src/Bulnabi_DBF2025_CustomSim_v1/models/zephyr_delta_wing/model.sdf
ros2 control load_controller velocity_controllers --set-state active
ros2 topic pub /velocity_controllers/commands std_msgs/msg/Float64MultiArray "data: [1.0]"  # 1.0 rad/s로 제어
```



```
insert below code block to model.sdf

    <plugin name="gazebo_ros2_control" filename="libgazebo_ros2_control.so">
      <robotNamespace>/zephyr_delta_wing</robotNamespace>
      <control_period>0.01</control_period>
      <robotParam>robot_description</robotParam> <!-- URDF 매핑 -->
    </plugin>



ros2 launch gazebo_ros gazebo.launch.py parameters_file:=/home/tykim/ros2_ws/src/Bulnabi_DBF2025_CustomSim_v1/config/zephyr_controllers.yaml

ros2 run robot_state_publisher robot_state_publisher   --ros-args   -p robot_description:="$(cat /home/tykim/ros2_ws/src/Bulnabi_DBF2025_CustomSim_v1/models/zephyr_delta_wing/urdf/zephyr_delta_wing.urdf)"

ros2 run gazebo_ros spawn_entity.py -entity zephyr_delta_wing -file /home/tykim/ros2_ws/src/Bulnabi_DBF2025_CustomSim_v1/models/zephyr_delta_wing/model.sdf

ros2 control list_controllers

ros2 control load_controller joint_state_controller --set-state active
ros2 control load_controller velocity_controllers --set-state active
```
