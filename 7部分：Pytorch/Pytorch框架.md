

# 1.深度学习框架：PyTorch

2002年Torch（Lua） -- > 2016.19 发布0.1， 2018.12发布1.0（CAFFE2后端 facebook发布），

同类型框架：	TensorFlow2（静态图）， （PyTorch动态图）





## 环境搭建

[mac搭建参考：](https://juejin.cn/post/7199631784061452343) 

conda安装 ， conda list ， conda --version

1. 在终端中输入 `conda activate pytorch` 即可激活我们的运行环境，输入`conda deactivate`可退出已激活的环境。

2. 查看工具包 `conda list`





\# To activate this environment, use

\#

\#   $ conda activate torch-gpu

\#

\# To deactivate an active environment, use

\#

\#   $ conda deactivate

## 张量数据类型



## 创建Tensor



## 索引 与 切片





## 纬度变换

View/reshape，  shape 转变

Squeeze/unsqueeze ， 挤压：删减纬度 / 增加纬度。

矩阵的转制 ：Transpose  / permute :  Transpose(1,3) 两个纬度  , permute(0,2,3,1)

纬度的扩展： Expand / repeat 





## Broadcasting

