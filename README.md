# Reproduction log
**11.15 ~ 11.19： 材料集备（硬件）**

**11.20 ~ 11.22： 结构设计 + 3D 打印 + 装配**
![FastLivo_machine](https://github.com/user-attachments/assets/55a5412a-9e3b-4a83-9e41-2575b2f2ee87)

**11.23：STM32代码（发送PWM脉冲）**

**11.24：Fast-LIO跑通（外接imu）**

**11.25：外参标定测试**
![FastLivo_ext](https://github.com/user-attachments/assets/6d235952-a04b-47a1-9366-d137048dc46b)

**11.26 ~ 11.27：相机硬同步 + Lidar硬同步(PPS触发)**

**11.28 : 软同步（共享内存）**
![image](https://github.com/user-attachments/assets/e73cfc0c-e672-48ba-99f0-6254dc8904d8)
![image](https://github.com/user-attachments/assets/49e0e73f-a432-4131-896c-2c4c046967e3)
时间戳同步了，但是发布时间仍然没有同步，但是保证了没有时延应该就行。
![image](https://github.com/user-attachments/assets/650e31cc-ce40-4284-936e-0ba588563ef9)


**11.29 : 出图**

![image](https://github.com/user-attachments/assets/c03b83b0-e263-45d5-8bec-0efd5d74a8c6)
![image](https://github.com/user-attachments/assets/57f512bb-dde1-42ee-a207-da8803bb650c)
![image](https://github.com/user-attachments/assets/0c8cc620-9675-41d4-aebd-7e698fef3dbe)
![image](https://github.com/user-attachments/assets/4e86f256-e939-41d7-ac42-d9d3ff2c516a)
![image](https://github.com/user-attachments/assets/d8d4765b-1f53-44a6-a495-69dbda8869bf)

启动顺序：雷达（赋能串口:Stm32->GPS)->相机->IMU（赋能串口sudo chmod 777 /dev/ttyUSB*）->bag包检查时间戳和发布频率->FastLivo

根据Add 0 3D Points，回到源程序，判断可能是点未在方框内，推测是外参矩阵问题，IMU-Lidar的之前在Fast-LIO中试过是可以的。
推测可能是Lidar-Camera的问题，之前标外参是转置了，现在转回来尝试一下，就ok了。
![image](https://github.com/user-attachments/assets/a8bd62b9-804d-4126-bccd-855f9d3f9ee5)
会出现漂移

**11.30 ： 调优-> 外参标定**
![image](https://github.com/user-attachments/assets/225c5ade-f95b-49aa-af31-235a2eb5aa26)
4个场景，两个室内，两个室外。
4个一起做multi_calib的效果不好
![image](https://github.com/user-attachments/assets/7042e7fb-b2a4-4867-89a7-52b4415cb539)
单独试试，选最优的
![image](https://github.com/user-attachments/assets/413b1d09-d7b7-491c-a1f1-628462070265)
效果不好
![image](https://github.com/user-attachments/assets/a4ae1506-4ec8-4f58-b86c-345aad257f43)
效果不好
![image](https://github.com/user-attachments/assets/87b477d0-3e0c-4644-9cde-ca47f284e0b4)
效果稍微好点
尝试降采样，缩短时间38s->20s
![image](https://github.com/user-attachments/assets/7fcb44dd-7897-49a5-ba5e-a146d75079e7)
效果更差了
![image](https://github.com/user-attachments/assets/b7b25708-8970-4c99-b3c1-422b149b12ff)
效果不好
打算重新标定内参
ROS自带的标定
![image](https://github.com/user-attachments/assets/5a8d026a-6d94-446b-b6f7-26e54b6e7c42)
两次标定的结果相差有点大
![image](https://github.com/user-attachments/assets/f315bfab-063b-4237-a14e-378703e7e71e)

找其他的标定方法

**12.1~12.2 ：结构设计重构**

![image](https://github.com/user-attachments/assets/245e42ed-99d5-4eea-88f4-d5890433b632)

**12.3 ： 内外参标定**

内参尝试两套方案：
1、ROS内自带标定
![image](https://github.com/user-attachments/assets/3d9877a4-4718-4d36-91e3-2d169812de22)
2、Kalibr仓库标定
标定焦距方差大
![image](https://github.com/user-attachments/assets/f07b2108-024e-4b0c-a36b-c072a56049e9)

