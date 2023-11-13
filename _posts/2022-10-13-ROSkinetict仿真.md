---
title: ROSkinetict仿真
tags:
  - ROS
---

kinetic传感器？？
![](https://cdn.jsdelivr.net/gh/YangSongL1n/img_bed/9fb4b5db4480e6ec63c83a6ad9ffb6ab.jpeg)

## 3D深度相机 

RGBD深度摄像头简介
深度相机就是可以获取场景中物体距离摄像头物理距离的相机
目前RGBD深度摄像头获取深度图像的方法主要有立体视觉，激光雷达测距和结构光三大类。

### 立体视觉

立体视觉。获取深度信息，指的是通过获取同一场景不同视角的多张图像，利用图像的匹配和一定的三维重建算法来计算场景对象的深度信息，如利用处同一轴线上的两个摄像头获取场景对象的两张视差图以及相机的内参和外参数计算深度信息的双目摄像头。
双目立体视觉法的原理和人眼类似，通过计算空间中同一个物体在两个相机成像的视差就可以根据如下三角关系计算得到物体离相机的距离。
![](https://cdn.jsdelivr.net/gh/YangSongL1n/img_bed/8b5f3b76dcaff89b03d7a624cd590e7c.png)

### 
 激光雷达测距
激光雷达测距则采用TOF 技术（Timeof flight，飞行时间），它通过记录光源投射到每个像素点的光线发射与反射间的相位变化来计算光线飞行时间，进而计算光源到每个像素点的距离飞行时间是从Time of Flight直译过来的，简称TOF。其测距原理是通过连续发射经过调制的特定频率的光脉冲（一般为不可见光）到被观测物体上，然后接收从物体反射回去的光脉冲，通过探测光脉冲的飞行（往返）时间来计算被测物体离相机的距离。
![](https://cdn.jsdelivr.net/gh/YangSongL1n/img_bed/037ac003e05a210089d842cf51606d5e.png)
                                                             激光雷达测距原理图

### 结构光

结构光法就是使用提前设计好的具有特殊结构的图案（比如离散光斑、条纹光、编码结构光等），将图案投影到三维空间物体表面上，使用另外一个相机观察在三维物理表面成像的畸变情况。如果结构光图案投影在该物体表面是一个平面，那么观察到的成像中结构光的图案就和投影的图案类似，没有变形，只是根据距离远近产生一定的尺度变化。但是，如果物体表面不是平面，那么观察到的结构光图案就会因为物体表面不同的几何形状而产生不同的扭曲变形，而且根据距离的不同而不同，根据已知的结构光图案及观察到的变形，就能根据算法计算被测物的三维形状及深度信息

![image.png](https://cdn.jsdelivr.net/gh/YangSongL1n/img_bed/c45927149c4ea22e6d056fe3f7618300.png)

## kinetic传感器仿真实验

### 1.1 新建 Xacro 文件，配置 kinetic传感器信息

####                  2  19  27 行

```xml
<robot name="my_sensors" xmlns:xacro="http://wiki.ros.org/xacro">
    <gazebo reference="kinect link名称">  
      <sensor type="depth" name="camera">
        <always_on>true</always_on>
        <update_rate>20.0</update_rate>
        <camera>
          <horizontal_fov>${60.0*PI/180.0}</horizontal_fov>
          <image>
            <format>R8G8B8</format>
            <width>640</width>
            <height>480</height>
          </image>
          <clip>
            <near>0.05</near>
            <far>8.0</far>
          </clip>
        </camera>
        <plugin name="kinect_camera_controller" filename="libgazebo_ros_openni_kinect.so">
          <cameraName>camera</cameraName>
          <alwaysOn>true</alwaysOn>
          <updateRate>10</updateRate>
          <imageTopicName>rgb/image_raw</imageTopicName>
          <depthImageTopicName>depth/image_raw</depthImageTopicName>
          <pointCloudTopicName>depth/points</pointCloudTopicName>
          <cameraInfoTopicName>rgb/camera_info</cameraInfoTopicName>
          <depthImageCameraInfoTopicName>depth/camera_info</depthImageCameraInfoTopicName>
          <frameName>kinect link名称</frameName>
          <baseline>0.1</baseline>
          <distortion_k1>0.0</distortion_k1>
          <distortion_k2>0.0</distortion_k2>
          <distortion_k3>0.0</distortion_k3>
          <distortion_t1>0.0</distortion_t1>
          <distortion_t2>0.0</distortion_t2>
          <pointCloudCutoff>0.4</pointCloudCutoff>
        </plugin>
      </sensor>
    </gazebo>

</robot>
```

####                     借用小车雷达上的连杆   还有一系列修改

```xml
    <gazebo reference="support">  
 
<plugin name="kinect_camera_controller" filename="libgazebo_ros_openni_kinect.so">
      <cameraName>kinect_camera</cameraName


```

![image.png](https://cdn.jsdelivr.net/gh/YangSongL1n/img_bed/1638283001643-61ac1869-f713-4b4a-90e6-9be1796d94fe.png)

### 1.2 xacro 文件集成

![image.png](https://cdn.jsdelivr.net/gh/YangSongL1n/img_bed/1638335371099-01805395-e0ef-4eff-98f2-11d6c25d0d59.png)

```xml
<robot name="mycar" xmlns:xacro="http://wiki.ros.org/xacro">
    <xacro:include filename="head.xacro" />
    <xacro:include filename="demo05_car_base.urdf.xacro" />
    <xacro:include filename="demo06_car_camera.urdf.xacro" /> 
    <xacro:include filename="demo07_car_laser.urdf.xacro" />
    <!-- 运动-->
    <xacro:include filename="gazebo/move.xacro" />

    <xacro:include filename="gazebo/Kinect.xacro" />
    <!-- <xacro:include filename="gazebo/camera.xacro" /> -->
    <!-- <xacro:include filename="gazebo/leida.xacro" /> -->

</robot>
```

####             demo03_env.launch

```xml
<launch>

    <!-- 将 Urdf 文件的内容加载到参数服务器 -->
     <param name="robot_description" command="$(find xacro)/xacro $(find urdf02_gazebo)/urdf/car.urdf.xacro" />
    <!-- 启动 gazebo -->
    <include file="$(find gazebo_ros)/launch/empty_world.launch">
        <arg name="world_name" value="$(find urdf02_gazebo)/worlds/box_house.world" />
    </include>

    <!-- 在 gazebo 中显示机器人模型 -->
    <node pkg="gazebo_ros" type="spawn_model" name="spawn_model" args="-urdf -model car -param robot_description"  />
</launch>
```

####         demo04_sensor.launch

```xml
<launch>
    <!-- 启动 rviz -->
    <node pkg="rviz" type="rviz" name="rviz" args="-d $(find urdf01_rviz)/config/show_mycar.rviz" />

    <!-- 关节以及机器人状态发布节点 -->
    <node name="joint_state_publisher" pkg="joint_state_publisher" type="joint_state_publisher" />
    <node name="robot_state_publisher" pkg="robot_state_publisher" type="robot_state_publisher" />

</launch>
```

####           运行launch 文件

```shell
roslaunch urdf02_gazebo demo03_env.launch 
roslaunch 包名 .launch  
roslaunch urdf02_gazebo demo04_sensor.launch 
```

sudo apt-get install ros-melodic-teleop-twist-keyboard

## [演示](https://www.bilibili.com/video/BV1eF41187Fc/)

        ？？？？？？？

![image.png](https://cdn.jsdelivr.net/gh/YangSongL1n/img_bed/1a38866c3dea4606932231d29e3dc28e.png)
![image.png](https://cdn.jsdelivr.net/gh/YangSongL1n/img_bed/aa20fdb27f3bcaeab7044894765bf9b3.png)
在kinect中图像数据与点云数据使用了两套坐标系统，且两套坐标系统位姿并不一致。

### 补充:kinect 点云数据显示

在kinect中也可以以点云的方式显示感知周围环境，在 rviz 中操作如下:![](https://cdn.jsdelivr.net/gh/YangSongL1n/img_bed/168a89b6a04f31fbac9790396e9906d8.png)
1.在插件中为kinect设置坐标系，修改配置文件的<frameName>标签内容：
<frameName>support_depth</frameName> 
2.发布新设置的坐标系到kinect连杆的坐标变换关系，在启动rviz的launch中，添加:

3.启动rviz，重新显示
![](https://cdn.jsdelivr.net/gh/YangSongL1n/img_bed/0306264b279a65d7eb90c1a15f6a19f7.png)

```
`<node pkg="tf2_ros" type="static_transform_publisher" name="static_transform_publisher" args="0 0 0 -1.57 0 -1.57 /support /support_depth" /> `
```

3.启动rviz，重新显示
![](https://cdn.jsdelivr.net/gh/YangSongL1n/img_bed/0306264b279a65d7eb90c1a15f6a19f7.png)