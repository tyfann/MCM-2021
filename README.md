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


# TTP Model

## 符号定义：

| symbol     | meaning                                            |
| ---------- | -------------------------------------------------- |
| R          | all resource department owns                       |
| t          | time effect factor                                 |
| To         | topographic                                        |
| lo         | longitude                                          |
| la         | latitude                                           |
| Po         | population                                         |
| $t_d$      | Detect Date                                        |
| $t_l$      | the latest Date of positive ID detect in this area |
| p          | possibility of unverified becoming possitive ID    |
| $\alpha$   | the enhanced weight of positive ID                 |
| $\Delta d$ | the distance of two area(space effect factor)      |
| $\Delta t$ | time bias(should be positive)                      |
| $r_i$      | the amount of resource each area will get          |
| $w_i$      | the weight of i area(importance)                   |
| isP        | if id is positive, then isP=1, else isP=0          |
| $\beta$    | base weight for time and space                     |
|            |                                                    |
|            |                                                    |
|            |                                                    |

## 模型所需要解决的主要问题：

"What strategies can we use to prioritize these public reports for additional investigation given the limited resources of government agencies?"

## 模型定义：

TTP model is based on variables including time, topographic and population.

topographic depends on location, which is related to longitude and latitude.

First, we concern about the evolution of the positive ID's distribution.

<img src=https://tva1.sinaimg.cn/large/008eGmZEgy1gneyf517cjj30rd0elnhb.jpg width=85%>

<center>Figure 1 positive ID distribution before 2020</center>

<img src=https://tva1.sinaimg.cn/large/008eGmZEgy1gneyfexgprj30rd0elwzh.jpg width=85%>

<center>Figure 2 positive ID distribution after 2020</center>

<img src=https://tva1.sinaimg.cn/large/008eGmZEgy1gneyfothx0j30a907cmx0.jpg width=85%>

<center>Figure 3 Detect Date of positive ID</center>

we can find from the heat map that positive ID's location is being changed from time to time, so we give time as a factor t, if the latest positive ID detect time is far from current, then t will become less and if the latest positive ID detect time is close to now, then t will become greater which means this place has higher priority to allocate resouce.

Secondly, we find from the paper that Vespa mandarinia typically build their nests inside of natural cavities such as hollow trees and sometimes inside the walls of buildings, so we divide terrain types into four terrains: plateau, mountain, plain and valley. And we give these four types different weights "To".

Finally, due to the risk of human being attacked by Vespa mandarinia, we concentrate on the population density of Washington state, and we give population as a factor 'Po', 'Po' becomes greater if the place is densely populated.

<img src=https://tva1.sinaimg.cn/large/008eGmZEgy1gne3ou9hz0j30jg0b4n0t.jpg width=85%>



<img src=https://tva1.sinaimg.cn/large/008eGmZEgy1gne3jkf272j30rd0eldj1.jpg width=85%>

## Model formula

As mentioned above, there are three impact factors, and then we give the interpretation formula(which is the resource distribution formula as well):

when the new reports come, we need to use TTP model to evaluate the priority of each report base on their location and the time when the information detect.

For a new coming report, assume we have the DetectDate $t_d$, latitude $la$ and longitude $lo$ of this report, we can find a postivie ID happened place which is nearest to this location, we calculate the distance $\Delta d$ and DetectDate bias $\Delta t$, 

$p_i =  \frac{\beta}{\Delta t_i * \Delta d_i}$ (when isP=0)

$w_i = isP * (\alpha * \frac{\beta}{\Delta t}) + (1-isP)*(p_i * \alpha) \\= isP * (\alpha * \frac{\beta}{\Delta t}) + (1-isP)*(\frac{\beta}{\Delta t_i * \Delta d_i} * \alpha)$

$\Large r_i = \frac{w_i}{\sum_{i=1}^{n} w_i}$

And then we set a loss function which will evaluate the model.Loss function is a finance evaluation model which will evaluate the economics loss of Washington State (contains the agricultural loss and society loss)

$Loss = E * (w_b-w_i)$

The resource allocation strategy:

We give the higher weight($w_i$) the prior schedule to give out to the lab to figure out the status.

## 主要策略：

1. 被实验室判定为Negative ID的直接忽略
2. 被实验室判定为Positive ID的需要重点调配资源(相同时间detect则平均分配，如果时间发生的离现在)
3. 被判定为Unprocessed的也先忽略，但如果在后续模型判定中Unprocessed的所处的地区是可能需要调配资源的地区的话，需要被纳入考虑范围
4. 被判定为Unverified的区域，根据模型所给出的权重进行排序，根据排序将剩余的可用资源进行依次调配



resource website:

https://commons.wikimedia.org/wiki/File:Washington_topographic_map-fr.svg
