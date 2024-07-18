# Voxposer Franka复现  
## 项目介绍
在franka机械臂上复现VoxPoser方法，搭建两个摄像头获取场景信息，利用LLM(ChatGPT-4)和视觉语言模型(owlv2)结合生成3D map，规划路径，采用ik获取机械臂自由度，控制执行动作。  
### 环境配置  
* 创建cuda环境
```python
conda create -n voxposer-env python=3.9
conda activate voxposer-env
```
* 安装依赖
```python
pip install -r requirements.txt
```
* 获取API key
## 场景搭建  
### franka机械臂连接  
**1、设置网络**  
在电脑主机和机器人控制器之间用网线连接，使用静态网络将控制器和工作站配置在同一网段上。  

|   | 电脑主机 | 控制器 |
| --- | --- | --- |  
| IP地址 | 172.16.0.1 | 172.16.0.2 |  
| 网络掩码 | 24 | 24 |  

详情：[Franka入门指南](https://franka.cn/FCI/getting_started.html " ")  
**2、服务器主机连接到Frank机械臂**  
 * 网页端输入ip地址：172.16.0.2  
 * 解锁机械臂关节  
 * 激活FCI接口（点击一次使窗口弹出激活态）
### 摄像头参数处理
**1、内参获取**  
```python
python get_intrinsics/intrinsics.py
```
**2、外参标定**
* 获取标定板（可以打印``Data/ChArUco_DICT_6X6_250_4_3_50_5_new.png``）
* 用机械臂抓手抓取标定板用摄像头拍摄图片保存在Data目录下
* 保存不同图片下的机械臂姿态，与图片一一对应
```python
python get_extrinsics_charuco/arm_pose.py
```
* 运行脚本文件获取相机外参
```python
python get_extrinsics_charuco/head_cam_calibration_func.py
```
将获取的内参外参替换到项目文件中
## 项目运行
```python
python src/lmp_main.py
```
