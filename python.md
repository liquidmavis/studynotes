# 人工智能



## python基础

### super

通过super来调用父类方法，再涉及到两个类同时继承一个父类的时候，可以保证菱形体系的共同超类只初始化一次

```python
class MyBeseClass:
    def __init__(self,val):
        self.val = val
class TimesSeven(MyBeseClass):
    def __init__(self,val):
        super().__init__(val)
        self.val *= 7
class PlusSeven(MyBeseClass):
    def __init__(self,val):
        super().__init__(val)
        self.val += 7
class Combine(TimesSeven,PlusSeven):
    def __init__(self,val):
        super().__init__(val)
print(Combine(1).val)


```



### 列表

python的负索引是从倒数第一个开始逆序

如：computer_choice=['rock','paper','scissors']    computer_choice[-1]就为scissors



#### 列表相加

append和extend区别

如果向一个列表加入另外一个列表

append

```python
mystery=['secret']
mystery.append(['BBQ','DADA'])
结果
['secret', ['BBQ', 'DADA']]
```

显然append是向原列表添加了一个列表元素



extend

```python
mystery.extend(['BBQ','DADA'])
结果
['secret', 'BBQ', 'DADA']
```

显然extend是把新列表合并进原列表



### 字符串

列表和字符串都是序列，但是列表可变，字符串不可变



### 字典

dictionary={} 字典类似于hashmap



#### defaultdict

当要管理的字典可能需要添加任意键的时候，可以用defaultdict来解决键缺失的情况

```python
from collections import defaultdict

class Visitors:
    def __init__(self):
        self.data = defaultdict(set)
    def add(self,country,city):
        self.data[country].add(city)

vistors = Visitors()
vistors.add('china','成都')
vistors.add('china','背景')

print(vistors.data)
```



### for循环增强

```python
x=[1,2,3]
y=[-1,-2,-3]   zip(x,y)
#结果为 [(1,-1),(2,-2),(3,-3)]
for (i,j) in zip(x,y)
 print(i,j)
#结果为
(1,-1)
(2,-2)
(3,-3)
```



------

## 机器学习

![1601384906696](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1601384906696.png)

向量化使得代码更简洁和高效

### 监督学习

回归问题（连续）和分类问题（离散）



##### 代价函数

就是求出所有样本预测值和实际值之间的平均误差值，并获得参数对应最小的代价函数



#### K类邻接算法

通过训练数据集，对新数据进行预测，算法在新数据位置附近查找最近的K个邻接数据（K自己设置），通过比较某一类别数量最多来预测其属于那一类别。



------



#### 回归

过拟合：过度关注训练集，得到训练集表现好，而在测试集差的模型，**方差过大**

欠拟合：模型过于简单，在测试集甚至在训练集上效果差，**偏差过大**



------



#### 分类

##### Logistic回归

一端趋向于1，一端趋向于0



![1601386411289](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1601386411289.png)

linear_model_LogisticRegression

重要参数：C决定正则化强度，C越大，对应正则越弱

------



##### 线性支持向量机（大间隔分类器）

svm.linearSVC

重要参数：C决定正则化强度，C越大，对应正则越弱

依赖于原理大间隔(点在theta向量映射大，theta向量范数小)分类

下图中的p(i)为第i个点对应的在theta上的映射。如果对于i是正样本，但是p(i)太小的话，就会造成theta向量的范数要过于大，过于大就会使得损失函数过大，所以向量机不能选择该决策边界。而应选择使得p(i)较大的决策边界，使得损失函数较小

![1603074195325](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1603074195325.png)

------

###### 核函数（相似度函数）

下面所用的是高斯核函数

![1603177155143](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1603177155143.png)

原理：通过标记点和相似度函数来定义新的特征值，从而训练复杂的非线性边界

如何选择标记点？ 把训练集或交叉验证集上的每个点都作为标记点

注意：在使用高斯核函数时候，需要对特征做缩放处理，不然会对范围数据较小的点进行抹去



下图成功构造出了决策边界

![1603177846573](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1603177846573.png)

###### Logsitic回归和向量机

当特征远大于样本数时，选择Logistic回归或者没有核的支持向量机

当样本数大小是合适时候，选择高斯核支持向量机，典型二分类问题



##### 朴素贝叶斯分类器

**概念:**通过查看每个特征来学习参数，并从每个特征中收集简单的类别统计数据。



scikit-learn实现三个朴素分类器：GasussianNB、BernoullliNB、MultinomialNB



其中GasussianNB用于连续数据，BernoullliNB（输入数据为二分类）和MultinomialNB(为计数数据)用于处理文本数据分类



##### 决策树

**概念：**

本质是从一层层的if/else中进行学习得出结论。每一次对一个特征进行划分，之后反复划分直到每个区域只包含单个目标值。

纯叶节点：如果某个叶节点包含数据目标值相同，则成为纯的



问题：构造决策树容易过拟合。有两种方法解决：

1. 预剪枝：及时停止树的生长（限制树高和叶结点数目）
2. 后剪枝：先构造树，随后删除或折叠信息量少的结点

优点：容易可视化，每个特征单独处理，不用对特征进行缩放处理

缺点：容易过拟合（通过限制高度可以防止过拟合），泛化能力弱，每次选择的特征划分是随机的，每次划分的图像都可能不一样

DecisionTreeClassifier和DecisionTreeRegressor



实现：

Classification and Regression Tree (CART)算法来实现二分树



##### 决策树集成

**强化学习**：用一群预测器各自训练训练集一部分数据，然后预测数据是大家共同推选出来票数最多的

下面图片是采用了svm，随机森林，k最近邻等多中预测器集合的强化模型

一般情况下，强化模型的准确度要优于每个单一的预测器

![1606027632549](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1606027632549.png)

软投票和硬投票：

软投票是取各个分类器对于某个类别概率的平均值，最高的被投票

硬投票是取各个分类器最高的类别，少数服从多数原则



取样方式：Bagging和Pasting

每次放回取样叫做Bagging，不放回取样叫做Pasting



集成：合并多个模型生成更强大的模型

代表：随机森林和梯度提升决策树



###### 随机森林

概念：本质是许多决策树的集合，每棵树与其他树都有所不同。每颗树都过拟合，取他们的平均值，来降低预测结果的过拟合



RandomForestRegressor和RandomForestClassifier



重要参数

1. n_estimators设置更多的树，降低过拟合

2. max_features:决定随机性大小

   

###### 梯度提升回归树

采用连续的方式构造树，每棵树都试图纠正前一棵树的错误

重要参数

1. n_estimators设置更多的树，降低过拟合
2. learning_rate：对前一棵树的纠正强度，过高会提高过拟合



------



##### 梯度下降

1、普通梯度下降

一次使用全部的数据来算出梯度

就是为了从红色的最高点不断降低到蓝色的位置。也就是降低代价函数 ，得到局部最优解

![1601259379823](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1601259379823.png)

2、随机梯度下降

每次随机取一个数据来算梯度，相对于正常的梯度下降，该梯度下降会有很大的波动性。配合每次混洗数据能很好地快速找到全局最优解。

3、小批量梯度下降

每次随机选取一个batch的数据来计算梯度。会出现算出局部最优解的问题。配合GPU加速使用





###### 一般公式

![1601259950698](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1601259950698.png)

- derivative(导数)：就是求该点的斜率
- learning late（学习率）：表示下降的步伐速度

###### MINI-batch

每次选取一个batch进行向量化，然后计算损失函数和梯度，对训练集很大的数据很有用 

###### 动量梯度下降法

以指数加权平均为基础

当B越大，平均的数目越多

好处：占内存小，只需要一行列式

![1608518494888](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1608518494888.png)
$$
Vdw2 = BVdw1 + (1-B)dw
$$

$$
Vdb2 = BVdb1 + (1-B)db
$$

$$
	W = W - aVdw
	
$$

$$
b = b - aVdb
$$

![1608520622368](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1608520622368.png)

动量梯度下降法减小了小批量数据造成的下降振动，从而使下降更好的收敛

###### RMSprop

![1608528718573](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1608528718573.png)



###### Adam

结合了动量和RMSprop

![1608529015607](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1608529015607.png)

###### 梯度问题

梯度消失和梯度爆炸

梯度消失：在神经网络中梯度在一层层减少后，到最后一层的时候梯度下降很慢或者几乎不变，这会使得结果不会收敛。

梯度爆炸：梯度增加非常快，权重等都在增加。

解决办法：合理的初始化权重参数



##### 特征缩放（与归一化一样都是为了提高梯度下降质量）

为了减少梯度下降中，特征值差距过大造成的下降震荡

![1601262165358](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1601262165358.png)

##### 均值归一化

下面减去的是均值

![1601262421087](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1601262421087.png)





##### 激活函数

MISH

```
Mish = x*tanh(ln(1+e^x))
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191113091329951.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwNjkyOA==,size_16,color_FFFFFF,t_70)

##### 召回率和精确率

召回率是指原来正确的被预测为正确的比例

精确率是指预测为正确的里面有多少原来就是正确的比例

![img](https://pic2.zhimg.com/80/d701da76199148837cfed83901cea99e_720w.jpg?source=1940ef5c)

##### 过拟合

###### 正则化

一般有L1和L2正则

使各个参数中大部分对损失函数没有影响，避免**过拟合**

但是如果让正则参数太大的话，那么对各个参数来说惩罚太大（几乎都等于0），就会出现**欠拟合**现象

![1602415611497](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1602415611497.png)



###### 数据扩增

对原始数据进行旋转裁剪等操作，来达到增加数据集，类似于正则化的作用

###### early stopping

顾名思义就是提前停止训练。

当它在训练过程中发现验证集表现下降的时候，就提前终止训练，避免训练进入过拟合阶段

###### 批归一化

Batch Normalization解决 Internal Covariate Shift问题

 Internal Covariate Shift的问题：

1、网络进行梯度下降时候不断调整来适应输入数据分布变化

2、容易陷入梯度饱和区，减缓网络收敛速度（如使用sigmoid和tanh）



如何解决？可以使用白化**使得输入特征分布具有相同的均值与方差**

但白化计算成本高，改变了网络每一层分布丢失了特征信息



引入了Batch Normalization，基于mini-batch批量训练，在每一层对批量数据每一个特征单独进行规范化均值为0，方差为1的分布，但也因此导致了数据丢失。

BN又引入了两个可学习（learnable）的参数 ![[公式]](https://www.zhihu.com/equation?tex=%5Cgamma) 与 ![[公式]](https://www.zhihu.com/equation?tex=%5Cbeta) 。这两个参数的引入是为了恢复数据本身的表达能力，对规范化后的数据进行线性变换，即 ![[公式]](https://www.zhihu.com/equation?tex=%5Ctilde%7BZ_j%7D%3D%5Cgamma_j+%5Chat%7BZ%7D_j%2B%5Cbeta_j) 。使得保留了原始特征

#### 神经网络

第一层为输入层，最后一层为输出层，中间的称为隐藏层

![1602470827357](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1602470827357.png)



合并三个简单的逻辑层来完成XNOR

![1602472341200](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1602472341200.png)



##### 反向传播

![1602987418410](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1602987418410.png)

表示第L层的j节点的误差

下面是第四层j结点的误差

![1602987468678](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1602987468678.png)



算法步骤

![1602987989446](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1602987989446.png)

###### 梯度检测

使用梯度检测来验证反向传播的导数是否正确。

通过对损失函数的每个参数求导之后与反向传播求出来的导数一一比对来验证反向传播正确性



### 无监督学习

分为数据集变换和聚类

#### 数据集变换

无监督变换是创建与原始数据相比较更加容易被理解的算法，最常的应用是**降纬**

目的：可视化、压缩数据和寻求进一步处理



##### 主成分分析法（PCA）

1、pca通过将数据变换旋转来让高维数据可视化和特征提取

2、pca通过低维来投影高维数据，并减少高维投影到低维的距离

3、选择保留方差最大的超平面



步骤：我们首先通过预处理（归一化和特征缩放），然后使用奇异值分解，选取前k个向量来对原数据进行降维投影



如何选择k（维度）？

但下图这种方法很耗时间

![1603788691637](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1603788691637.png)

但用下面这个方法要好得多，先用奇异值分解得到奇异值矩阵，然后算前多少个奇异值（对应奇异向量）保留了最多的数据

![1603788952848](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1603788952848.png)



正态分布函数 宽度是标准差，中心是平均值

![1603805378887](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1603805378887.png)

非负矩阵分解（NMF）

t-SNE

#### 聚类

把相似的数据分为一组

##### K-means

不断循环下面两个步骤直到聚类完成：

1. 族分配（某个点离哪个族最近，就属于哪个族）
2. 中心转移（算族中心下所有点的平均值，中心变为平均值位置）

![1603180614841](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1603180614841.png)

最优化函数也就对应了K-MEANS的第一步族分配步骤，每个点距离族的距离平方和最小

![1603181308407](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1603181308407.png)



注意：

对数据特征需要进行缩放，否则k-means可能运行很差劲

对k-means族的选择是个很难平衡的问题





### 模型评估

1、把数据分为训练集、交叉验证集和测试集（比例6:2:2）

用训练集拟合曲线，然后用交叉验证集来得到误差和误差函数，来评估和选择模型

2、偏差和方差

次项过于小的时候会出现欠拟合，导致高偏差，此项过于大的时候会出现过拟合，导致高方差

![1603071606792](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1603071606792.png) 

## 深度学习

### 卷积神经网络历史

​		AlexNet采用了Relu代替sigmoid，减少了梯度消失问题，并且在最后全连接包含参数数量最多时候，加入dropout来减少过拟合问题

​		Vgg遵循alexnet的relu和数据增强，主要新颖在于使用更小的过滤器3-3，而alexnet是11-11

​		googlenet通过使用inception模块来堆叠，该模块使用1-1的卷积核来降低通道数，第一个脱离卷积层和池化层的网络

​		Resnet建立残差学习，使用跳跃式网络可以克服反向传播中的梯度消失问题

​		densenet是把一个block里的前几层网络特征添加到最后一层中

​		

​		

















#### 卷积层

![1605754102583](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1605754102583.png)

##### 三维卷积

![1605754539950](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1605754539950.png)

多个过滤器卷积

![1605755098717](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1605755098717.png)

##### 构建一个简单卷积神经网络

![1605756712764](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1605756712764.png)

##### 与全连接的区别

参数共享和稀疏连接

参数共享：相对于全连接，卷积所用的参数要少得多

稀疏连接：一个输出只需要对应少数的输入，而全连接是所有输入一起计算出一个输出



###### 使用卷积代替全连接

使用1*1的卷积核达到全连接作用

![1608269575815](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1608269575815.png)

#### 池化层

提取关键信息，对特征位置要求不高

最大池化（选取过滤器中最大值）

![1605758019724](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1605758019724.png)

平均池化：选取过滤器中平均值

#### 全连接层



#### Intecept层

用户决定输入输出的大小，由intercept网络来决定使用池化还是卷积还是任何参数来组合出结果



#### YOLO系列

##### YOLO1



![1607589897193](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1607589897193.png)

yolo把每个输入图片分成了S*S个网格，当一个对象落在某个网格中心，这个网格就负责检测该图像

每个网格包含了B个边界框，每个边界框对应了x,y,w,h和一个置信值，xy代表了该边界框相对于网格的中心值；w,h代表了边界框相对于网格的相对位置。置信值的公式为![1607590181097](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1607590181097.png)

置信值代表了该边界框对于是否存在对象的信心程度。如果没有对象该值为0。其中IOU<sup>truth</sup><sub>pred</sub>是交并比，特别注意，因为一个网格有多个边框，我们在输出时候只会有一个边框，在训练时候我们每个网格选择交并比最高的作为预测对象的边框

同时每个网格还包含了C个分类概率Pr(Class<sub>i</sub>|Object),他们概率之和为1



所以最终我们一张图片的张量为SS*(B*5+C)

使用的激活函数时Leakrelu

![1607590760079](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1607590760079.png)





损失函数

![1607591474470](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1607591474470.png)

1<sup>obj</sup><sub>ij</sub>表示了对象出现在了i网格的j边框中
$$
\lambda_coord和\lambda_noobj，\lambda_noobj值一般都小于\lambda_coord。
$$
因为图片中大部分网格没有对象而存在的，所以梯度会特别大，加入这两个参数就会减小对于没有对象的网格的惩罚

损失函数

1、计算了边界框对象中心位置与真实值的差值

2、计算了边界框在图片的位置与真是值得差值

3和4、计算了边界框的置信度差值

5、计算了各个存在对象的网格的分类差值



注意到步骤二的宽和高都加了根号，如果正常处理大边框有小的差别和小边框也有同样的差别得到的损失函数是一样的，这是不对的，大边框应该容忍度更高。所以作者加了根号来处理

![https://pic2.zhimg.com/80/v2-bfac676d0f0db4a1d9f4f9aa782341dd_hd.jpg](https://pic2.zhimg.com/80/v2-bfac676d0f0db4a1d9f4f9aa782341dd_hd.jpg)



预测流程：因为有多个网格多个边框，输出时候我们从中提取最有可能的值。采用了NMS（非极大值抑制也叫取局部最大值）

网上建议是对每个类别设置NMS，我们计算下面的值，也就是每个类别在该框的可能性

![1607593692480](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\1607593692480.png)

1、设置一个score阀值来删除掉低于这个值的框

2、遍历对象类别

​	找到最大的score并设置为输出列表

​	对每一个不为0的score将其与最大的score的boudingbox计算IOU

​	根据预设的IOU来删除掉过大的边界框

​	如果边界框都在输出列表或者值为0那么该轮NMS完成



缺点：不能预测一个网格多对象的问题

​		  网格设置稀疏

##### YOLO2

基于yolo v1使用了

1、批归一化

2、使用高分辨率图像微调分类模型（具体是先用低分率训练，再用高分率图像微调模型）

3、采用先验框（移除了全连接层,可以使用不同尺寸的图片）

4、**聚类提取先验框尺度**：yolov2尝试使用统计方法聚类来对训练集标注边框进行分析，找到匹配的边框，使用欧式距离的话会导致大边框产生于yolov1相同的大误差，所以使用下面方法
$$
d(box,centroid) = 1 - IOU(box,centroid)
$$
![https://pic3.zhimg.com/80/v2-25c593e08140eb339b736d64de419712_720w.jpg](https://pic3.zhimg.com/80/v2-25c593e08140eb339b736d64de419712_720w.jpg)

5、约束边框位置

![img](https://pic3.zhimg.com/80/v2-ce407201d04f578e0f4bb54bcf46b6f2_720w.jpg)

6、使用passthrough层（为了更好提取小对象特征）

在最后一个池化层之前，将特征图1拆4，直接传递到池化后与池化后的特征进行叠加

![img](https://pic2.zhimg.com/80/v2-8faf5d343e3a396d0751666833039eb1_720w.jpg)





下图中25代表20个分类，先验框坐标4和置信度

![img](https://pic2.zhimg.com/80/v2-50b64ae9c7687be4f5d96bac597ffabd_720w.jpg)



损失函数：

![img](https://pic4.zhimg.com/80/v2-74511f262389468edd067b83d2dda637_720w.jpg)



YOLOv可以识别9000中种类，因为有些类别之间不是互斥的而是包含关系，所以不能直接使用softmax，下面使用的是worldTree结构

当在同一个节点子节点时候可以使用softmax

![img](https://pic4.zhimg.com/80/v2-97fee30bec4cc55415bdf324f6125b17_720w.jpg)

##### YOLO3

主要特点：

1、对象分类用Logistic代替softmax（logistics可以更好来适应worldTree结构）

2、使用多尺度特征进行对象检测

使用不同的感受野（卷积核）来检测对应尺寸大小的对象，在第79层、91等之后向上采样

79层下采样高，感受野大所以能识别大尺寸对象

![img](https://pic3.zhimg.com/80/v2-aa898cebbda0ba139300f4795d8df79a_720w.jpg)

3、新的网络结构（53层卷积）

结合了残差网络

![img](https://pic1.zhimg.com/80/v2-4d95ca9c05047f87787b966fa8673b98_720w.jpg)

左边1,2,8,4代表了多少个重复的残差组件（包含两个卷积层和一个残差层）

![img](https://pic2.zhimg.com/80/v2-efd4113a088031c17ec9b4ad66daea79_720w.jpg)



##### YOLO4

![img](https://pic4.zhimg.com/v2-88544afd1a5b01b17f53623a0fda01db_b.jpg)

**先整理下Yolov4的五个基本组件**：

1. **CBM：**Yolov4网络结构中的最小组件，由Conv+Bn（批量归一化）+Mish激活函数三者组成。
2. **CBL：**由Conv+Bn+Leaky_relu激活函数三者组成。
3. **Res unit：**借鉴Resnet网络中的残差结构，让网络可以构建的更深。
4. **CSPX：**借鉴CSPNet网络结构，由卷积层和X个Res unint模块Concate组成。
5. **SPP：**采用1×1，5×5，9×9，13×13的最大池化的方式，进行多尺度融合。













