# rotors_path_planning

启动仿真器
roslaunch rotors_gazebo mav_hovering_example_with_vi_sensor.launch  mav_name:=firefly world_name:=single_box
加载地图
roslaunch path_planning load_octomap.launch map_name:=single_box.bt
打开rviz
roslaunch path_planning rviz.launch
路径规划
rosrun path_planning path_planning_node
轨迹规划并执行
rosrun path_planning traj_gen
手动建图
roslaunch octomap_server octomap_tracking_server.launch
Gazebo仅加载world(用于生成octomap地图)
roslaunch path_planning visualize_world.launch world_name:=outdoor

Gazebo打开高程图world:
source /usr/share/gazebo/setup.sh
GAZEBO_RESOURCE_PATH="$GAZEBO_RESOURCE_PATH:/home/yzc/rotors_simulator_ws/src/rotors_simulator/rotors_gazebo/worlds" gazebo /home/yzc/rotors_simulator_ws/src/rotors_simulator/rotors_gazebo/worlds/volcano.world

获取高程地图:
https://heightmap.skydark.pl/
https://tangrams.github.io/heightmapper 可用，需将图片从rgba处理为grayscale


保存octomap地图(2种方法)
1.保存压缩的二进制存储格式地图：
rosrun octomap_server octomap_saver mapfile.bt
2.保存一个完整的概率八叉树地图：
rosrun octomap_server octomap_saver -f mapfile.ot
可视化octomap地图：
octovis xxx.ot[bt]

直接从world获取octomap文件，在.world文件中添加：
<plugin name='gazebo_octomap' filename='librotors_gazebo_octomap_plugin.so'>
  <octomapPubTopic>world/octomap</octomapPubTopic>
  <octomapServiceName>world/get_octomap</octomapServiceName>
</plugin>
运行以下命令直接得到octomap地图：
rosservice call /world/get_octomap '{bounding_box_origin: {x: 0, y: 0, z: 1.75}, bounding_box_lengths: {x: 20.8, y: 20.8, z: 4}, leaf_size: 0.1, filename: /home/yzc/map.bt}'
rosservice call /world/get_octomap '{bounding_box_origin: {x: 0, y: 0, z: 1.75}, bounding_box_lengths: {x: 20.8, y: 20.8, z: 4}, leaf_size: 0.1, filename: /home/yzc/rotors_simulator_ws/src/path_planning/map/single_box.bt}'

rosservice call /world/get_octomap '{bounding_box_origin: {x: 0, y: 0, z: 48}, bounding_box_lengths: {x: 499.2, y: 499.2, z: 116.8}, leaf_size: 1.6, filename: /home/yzc/rotors_simulator_ws/src/path_planning/map/outdoor.bt}'

rosservice call /world/get_octomap '{bounding_box_origin: {x: 0, y: 0, z: 0}, bounding_box_lengths: {x: 4006.4, y: 4006.4, z: 512.0}, leaf_size: 12.8, filename: /home/yzc/rotors_simulator_ws/src/path_planning/map/south_lake.bt}'

加载octomap：roslaunch path_planning load_octomap.launch map_name:=outdoor.bt
<arg name="path" default="/home/yzc/map.bt">
<node pkg="octomap_server" type="octomap_server_node" name="octomap_talker" output="screen" args="$(arg path)">
