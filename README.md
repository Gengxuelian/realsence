# Voxposer Franka复现  
## 项目介绍
在franka机械臂上复现VoxPoser方法，搭建两个摄像头获取场景信息，利用LLM(ChatGPT-4)和视觉语言模型(owlv2)结合生成3D map，规划路径，后采用ik获取机械臂自由度，控制执行动作。  
## 场景搭建  
### franka机械臂连接  
**1、基本环境安装**
* 创建cuda环境
```python
conda create -n voxposer-env python=3.9
conda activate voxposer-env
```
* 安装基本依赖
```python
pip install -r requirements.txt
```
* 安装panda_py
```python
pip install panda-python
```
* 安装对应的libfranka python版本
下载'panda_py_0.7.5_libfranka_0.9.2.zip', 解压后运行：
```python
pip install panda_python-0.7.5+libfranka.0.9.2-cp39-cp39-manylinux_2_17_x86_64.manylinux2014_x86_64.whl
```
* 获取API key替换至文件src/lmp_main.py中
**2、设置网络**  
在电脑主机和机器人控制器之间用网线连接，使用静态网络将控制器和工作站配置在同一网段上。  

|   | 电脑主机 | 控制器 |
| --- | --- | --- |  
| IP地址 | 172.16.0.1 | 172.16.0.2 |  
| 网络掩码 | 24 | 24 |  

详情：[Franka入门指南](https://franka.cn/FCI/getting_started.html " ")  
**3、服务器主机连接到Frank机械臂**  
 * 网页端输入ip地址：172.16.0.2  
 * 解锁机械臂关节  
 * 激活FCI接口（点击一次使窗口弹出激活态）
### 摄像头处理
**1、安装librealsense SDK**
```python
git clone https://github.com/IntelRealSense/librealsense.git
cd librealsense
```
* 安装依赖
```python
sudo apt-get install libudev-dev pkg-config libgtk-3-dev
sudo apt-get install libusb-1.0-0-dev pkg-config
sudo apt-get install libglfw3-dev
sudo apt-get install libssl-dev
```
* 进入到librealsense文件夹目录下，运行Intel Realsense 的许可脚本
```python
./scripts/setup_udev_rules.sh
```
* 下载并编译内核模块
```python
./scripts/patch-realsense-ubuntu-lts.sh
```
* 编译SDK2.0
```python
cd librealsense
mkdir build
cd build
cmake ../ -DBUILD_EXAMPLES=true
make
sudo make install
```
* 在终端运行realsense-viewer测试
* 安装pyrealsense2
```python
pip install pyrealsense2    
```
**2、内参获取**  
```python
python get_intrinsics/intrinsics.py
```
**3、外参标定**
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
将获取的内参外参替换到项目文件src/envs/real_env.py中的相应位置
## 项目运行
```python
python src/lmp_main.py
```
