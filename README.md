# Voxposer Franka复现  
## 项目介绍
在franka机械臂上复现VoxPoser方法，搭建两个摄像头获取场景信息，利用LLM(ChatGPT-4)和视觉语言模型(owlv2)结合生成3D map，规划路径，采用ik获取机械臂自由度，控制执行动作。  
## 场景搭建  
### franka机械臂连接  
**1、设置网络**  
在电脑主机和机器人控制器之间用网线连接，使用静态网络将控制器和工作站配置在同一网段上。  

|   | 电脑主机 | 控制器 |
| --- | --- | --- |  
| IP地址 | 172.16.0.1 | 172.16.0.2 |  
| 网络掩码 | 24 | 24 |  

详情：[Alt](https://franka.cn/FCI/getting_started.html "title")
