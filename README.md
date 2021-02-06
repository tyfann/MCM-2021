# MCM-2021

# 数据处理

## MySql数据库的处理：

1. 将Excel表格中的数据导入到MySql数据库中，首先在MySql中检视了不干净的数据，其中有格式为"30/8/1899"此类的以及年份中出现了"<Null>"的数据行，同时发现这些样本的globalId在映射表中没有对应的FileName，并且此类数据行数量较少，因此直接做删除处理
2. 将两张Excel表格中的globalId作为关联键，将两表格合为一张没有globalId只剩下其他有效信息的新表格作为后续的启动数据表格

## 图片视频数据集的处理：

对于给定的数据集中存在的".MOV"类型文件，使用python opencv工具提取视频中的关键帧并输出成新的图片文件放入数据集中，将原有的".MOV"类型文件进行删除，获取一个只有图片文件的数据集

## 卷积神经网络训练模型前图像预处理：

1. 将所有的图片都resize成200 $\times$ 200的标准化输入图像大小
2. 随机对图像样本进行镜像、模糊等手段进行处理

# CNN（卷积神经网络的使用）

我们参考了过往的关于AlexNet论文[1]，在对比了LeNet[2]后，我们选择使用层数更多，同时具有更多连接的AlexNet，在搭建了具有5层卷积层和3层连接层的神经网络模型之后，我们对样本进行8:2的划分（8为训练集，2为测试集），其中测试集内具有820张negativeID的图片和2张positiveID的图片，最终经过5个epoch的迭代，我们获得的测试集准确率为0.9975，然而我们通过计算发现，假设有一个朴素分类器，将所有的图片均判定为negativeID，即同时将测试集中的两张positiveID的图片均判定为negativeID，获得的测试准确率820/822=0.9975，与神经网络的训练结果基本一致，由此可见使用卷积神经网络AlexNet来对图片进行分类是不够的，也是不准确的，因为它永远不会将图片判定为positiveID。

因此我们提出了新的基于时间-经纬度地理信息位置测算模型TLLC（Time-Longitude-Latitude Classifier），通过基于reporter所拍摄图片的经纬度位置和实际的检测时间，对reporter提供的信息进行初步筛查，以便为决策者提供更加精准的资源分配方案

| layer_name | kernel_size | kernel_num | padding | stride |
| ---------- | ----------- | ---------- | ------- | ------ |
| Conv1      | 11          | 96         | [1,2]   | 4      |
| Maxpool1   | 3           | None       | 0       | 2      |
| Conv2      | 5           | 256        | [2,2]   | 1      |
| Maxpool2   | 3           | None       | 0       | 2      |
| Conv3      | 3           | 384        | [1,1]   | 1      |
| Conv4      | 3           | 384        | [1,1]   | 1      |
| Conv5      | 3           | 256        | [1,1]   | 1      |
| Maxpool3   | 3           | None       | 0       | 2      |
| FC1        | 2048        | None       | None    | None   |
| FC2        | 2048        | None       | None    | None   |
| FC3        | 1000        | None       | None    | None   |





[1] Krizhevsky A, Sutskever I, Hinton G E. Imagenet classification with deep convolutional neural networks[J]. Advances in neural information processing systems, 2012, 25: 1097-1105.

[2] LeCun Y, Bottou L, Bengio Y, et al. Gradient-based learning applied to document recognition[J]. Proceedings of the IEEE, 1998, 86(11): 2278-2324.

