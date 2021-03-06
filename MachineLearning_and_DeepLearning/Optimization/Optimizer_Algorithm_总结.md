在深度学习中，需要不停地迭代学习更新网络的参数，参数的更新过程是基于梯度的反向传播。但是关于又如何计算每层的参数的梯度，就有多种方法和算法。

## 0. 梯度下降的理解
首先要明确梯度下降的目标是优化模型？到底什么是所谓的优化模型？
* 模型是由一层一层的、相互递进的参数表示的。如果用`theta_i`表示第i层的参数，则有`f1=f1(theta_1, x)`, `f2=f2(theta_2, f1)`...。
即每一层的输出作为下一层的输入。
* 模型的好坏如何度量？如果模型越好，则其输出的结果越接近我们想要的，也就是越接近真实的结果，i.e. `prediction`与`truth`越接近。
* 那么又如何度量所谓的预测与真实的**接近程度**呢？这就是需要`loss function`来量化**接近程度**。如果`prediction`和`truth`大部分都是
匹配的，也就预测结果接近真实结果，`loss`就小；如果预测结果与真是结果相差很远，则`loss`就很大。

损失函数(`loss`)是每个样本损失函数的叠加求平均。那么影响`loss`的因素都有哪些呢？
* 训练集合中每个sample肯定会影响最终的`loss`，这是由dataset决定的，我们暂时不多考虑。
* 如果`loss`是批量sample的`loss`的叠加球平均，则`Batch`的大小和选择都会影响`loss`。

那么**梯度下降**又是干什么的呢？
* 既然模型有了，度量模型好坏的方式(loss)也有了。那么优化模型就是尽可能的减小`loss`。
* `loss`既然是一些参数(i.e. thetas)的函数，那么`loss`就可以表示为一个参数空间上的曲面，则相应的就存在极小值点，优化的目标就是找到能让
`loss`达到最小点的一系列参数`thetas`。
* 既然想让`loss`移动到最小值点，就要让`loss`沿着梯度、向着`loss`下降的方向移动，这就是所谓的梯度下降。

## 1. 基本的梯度下降算法
首先梯度下降的最常见三种基本算法是：BGD，SGD和MBGD。这三种的差别取决于用多少个sample来计算`loss`，然后用该`loss`去计算梯度并更新参数`thetas`。

### 1.1 Batch Gradient Descent(BGD)
BGD是基于整个dataset中的所有sample的loss叠加然后求平均表示最终的`loss`，也就是基于整个dataset来更新参数`thetas`。       

|  BGD update equation  |
|:---------------------:|
| ![1-1](./images/BGD_equation.png) |

缺点：
每一次的更新都要对整个dataset计算loss，然后计算梯度，则计算会非常缓慢；
遇到规模比较大的dataset，则会很慢；
不能添加新的sample，也就不能及时更新哦模型。

BGD对于凸函数可以收敛到全集极小值，对于非凸函数也可以收敛到局部极小值。

定义迭代次数`epoch`，即对整个dataset进行几次的全部计算，也就是更新几次`thetas`参数。

### 1.2 Stochastic Gradient Descent(SGD)
SGD是基于dataset中的每个sample的loss进行参数更新，也就是整体数据集是个循环，其中对每个样本进行一次参数更新。SGD每次只对一个sample进行计算，更新速度快；并且可以随时新增样本。

|  SGD update equation  |
|:---------------------:|
| ![1-2](./images/SGD_equation.png) |

冗余：对于很大的dataset而言，其中可能有相似、甚至一样的sample，那么相似的或一样的sample的loss就重复出现，也就是冗余。[有疑问？对于相同的dataset，SGD不也有吗？]

缺点：
SGD更新频繁，经常会有较严比较大的波动；
BGD 可以收敛到局部极小值，当然 SGD 的震荡可能会跳到更好的局部极小值处；
SGD相对与BGD而言，噪声会比较多，因为在BGD中少量的noise会被整体淹没掉，这就使得DGD每次的更新并不一定都朝向最优化的方向；
相比与BGD虽然训练速度快了，但是准确度会有所下降；
如果是非凸函数，则不一定能找到全局最优。

SGD虽然包含一定的随机性，但是从整体期望上来看，基本是正确的梯度，也就是整体上是朝正确方向下降的。

### 1.3 Mini-Batch Gradient Descent
SGD 和 BGD 是两个极端，SGD 每次是用一个sample，BGD 每次是用整个dataset。MBGD 则是介于两者之间，每次使用一小批量的samples，也就是n个样本进行计算。
每次更新都是基于n个samples，降低了噪声的影响，收敛更稳定；另一方面可以充分地利用深度学习库中高度优化的矩阵操作来进行更有效的梯度计算。

|  MBGD update equation  |
|:---------------------:|
| ![1-3](./images/MBGD_equation.png) |

每个batch中样本的个数n，是个超参数，一般取值在50～256。

缺点：
1. 不过 Mini-batch gradient descent 不能保证很好的收敛性，learning rate 如果选择的太小，收敛速度会很慢，如果太大，loss function 就会在极小值处不停地震荡甚至偏离。
对于非凸函数，还要避免陷于局部极小值处，或者鞍点处，因为鞍点周围的error是一样的，所有维度的梯度都接近于0，SGD 很容易被困在这里。
2. SGD对所有参数更新时应用同样的 learning rate，如果我们的数据是稀疏的，我们更希望对出现频率低的特征进行大一点的更新。LR会随着更新的次数逐渐变小。[???]

* 鞍点就是：一个光滑函数的鞍点邻域的曲线，曲面，或超曲面，都位于这点的切线的不同边。


## 2. Momentum
|  SGD with and without momentum  |
|:-------------------------------:|
| ![2-1](./images/SGD_without_and_with_momentum.png) |

SGD 在 ravines 的情况下容易被困住， ravines 就是曲面的一个方向比另一个方向更陡，这时 SGD 会发生震荡而迟迟不能接近极小值。
从上图中可以看出，沿着椭圆长轴的方向，梯度下降比较慢；而短轴方向梯度下降比较快，也就是梯度更陡。

|  Momentum Equation  |
|:-------------------------------:|
| ![2-2](./images/momentum_equation.png) |

Momentum 通过引入`v_t-1`，既可以加速SGD，也可以一直震荡。
因为这一项，1.可以使得梯度方向不变的方向上加速（正加正会越快），2.可以使梯度方向改变的方向更新速度变慢，从而抑制震荡。从而既可以加快收敛，也可以减小震荡。

其中，超参数`gama`一般取值为`0.9`。

缺点：


## 3. Nesterov Accelerated Gradient (NAG)
在计算梯度的时候，不是在**当前的**位置`J(theta)`，而是在**未来的**位置上`J(theta-\game x v_t-1)`。

|  Nesterov Accelaerated Equation  |
|:-------------------------------:|
| ![3](./images/Nesterov_Accelerated_Gradient_equation.png) |

其中，超参数`gama`一般取值为`0.9`。

* 在 SGD 的基础上，考虑调整速度，对 SGD 进行加速和调整，也就是`Momentum`和`Nesterov Acceleraed Gradient(NAG)`。
* 如果想要根据**参数重要性**而对不同的参数进行不同程度的更新呢？该怎么做？


## 4. Adaptive Graident Algorithm (Adagrad)
该算法可以做到：
* 对低频的参数做较大的更新；对高频的参数做较小的更新。因此对稀疏的数据的表现会更好，也因此提高了 SGD 的鲁棒性。

| #  | Adagrad Equation  |
|:---:|:----------------------------:|
| 4-1 | ![4-1](./images/Adagrad_1.png) |
| 4-2 | ![4-2](./images/Adagrad_2.png) |
| 4-3 | ![4-3](./images/Adagrad_3.png) |
| 4-4 | ![4-4](./images/Adagrad_4.png) |

* `g_t,i` 是t时刻，参数`theta_i`的梯度；
* `4-3` 普通 SGD 的`theta`更新方式；
* 其中 G_t 是个对角矩阵， (i,i) 元素就是 t 时刻参数 θ_i 的梯度平方和。

优点：
Adagrad 的优点是减少了对learning rate的手动调节。

缺点：
分母会不断积累，最终learning rate会收缩并变得非常小。

超参数设定值：一般η选取0.01


## 5. Adadelta
这个算法是对 Adagrad 的改进。和 Adagrad 相比，就是分母的 G 换成了过去的梯度平方的衰减平均值，指数**衰减平均值**

| #  | Adedelta Equation  |
|:---:|:----------------------------:|
| 5-1 | ![5-1](./images/Adedelta_1.png) |
| 5-2 | ![5-2](./images/Adedelta_2.png) |
| 5-3 | ![5-3](./images/Adedelta_3.png) |
| 5-4 | ![5-4](./images/Adedelta_4.png) |

和 Adagrad 相比，就是分母的 G 换成了过去的梯度平方的衰减平均值，指数衰减平均值。
这个分母相当于梯度的均方根 root mean squared (RMS)，在数据统计分析中，将所有值平方求和，求其均值，再开平方，就得到均方根值，
所以可以用 RMS 简写 [equation 5-2]

超参数设定值:  γ 一般设定为 0.9


## 6. RMSProp
RMSprop 是 Geoff Hinton 提出的一种自适应学习率方法。 
**RMSprop 和 Adadelta 都是为了解决 Adagrad 学习率急剧下降问题的.**

| #  | Adedelta Equation  |
|:---:|:----------------------------:|
| 6-1 | ![6-1](./images/RMSProp_equation.png) |

* 与 Momentum 的处理方式类似，采用**指数加权平均**
* 某一维度的导数比较大，则指数加权平均就大，某一维度的导数比较小，则其指数加权平均就小，这样就保证了各维度导数都在一个量级，进而减少了摆动。
* 允许使用一个更大的学习率η

* 超参数设定值: Hinton 建议设定 γ 为 0.9, 学习率 η 为 0.001。


## 7. Adaptive Moment Estimation (Adam)
这个算法是另一种计算**每个参数**的自适应学习率的方法。相当于 RMSprop + Momentum。        
除了像 Adadelta 和 RMSprop 一样存储了过去梯度的平方 vt 的指数衰减平均值 ，也像 momentum 一样保持了过去梯度 mt 的指数衰减平均值：

| #  | Adam Equation  |
|:---:|:----------------------------:|
| 7-1 | ![7-1](./images/Adam_1.png) |
| 7-2 | ![7-2](./images/Adam_2.png) |
| 7-3 | ![7-3](./images/Adam_3.png) |

* 超参数设定值: 建议 β1 ＝ 0.9，β2 ＝ 0.999，ϵ ＝ 10e−8

实践表明，Adam 比其他适应性学习方法效果要好。

## 8. Performance Comparasion
![optimization on saddle point](https://images2018.cnblogs.com/blog/1192699/201803/1192699-20180311105558593-251578131.gif)
* 可以看出红色的点(SGD)最终停留在鞍点上；
* 绿色的(Momentum)和淡紫色的(NAG)在初期震荡比较厉害，但是最终也找到了正确的方向；
* 基于 Adaptive 的几种算法(Adagrad, Adedelta, RMSProp)都能很快找到正确方向并且收敛速度较快。

![optimization on loss surface contours](https://images2018.cnblogs.com/blog/1192699/201803/1192699-20180311110108768-2113908893.gif)

* 上面两种情况都可以看出，Adagrad, Adadelta, RMSprop 几乎很快就找到了正确的方向并前进，收敛速度也相当快，而其它方法要么很慢，要么走了很多弯路才找到。
* 由图可知自适应学习率方法即 Adagrad, Adadelta, RMSprop, Adam 在这种情景下会更合适而且收敛性更好。


## 9. How to choose optimization algorithm
* 如果数据是稀疏的，就用自适用方法，即 Adagrad, Adadelta, RMSprop, Adam。
* RMSprop, Adadelta, Adam 在很多情况下的效果是相似的。
* Adam 就是在 RMSprop 的基础上加了 bias-correction 和 momentum，
* 随着梯度变的稀疏，Adam 比 RMSprop 效果会好。
* 整体来讲，Adam 是最好的选择。
* 很多论文里都会用 SGD，没有 momentum 等。SGD 虽然能达到极小值，但是比其它算法用的时间长，而且可能会被困在鞍点。
* 如果需要更快的收敛，或者是训练更深更复杂的神经网络，需要用一种自适应的算法。


## References
1. [深度学习——优化器算法Optimizer详解（BGD、SGD、MBGD、Momentum、NAG、Adagrad、Adadelta、RMSprop、Adam）](https://www.cnblogs.com/guoyaohua/p/8542554.html)
2. [Sebastian Ruder](https://arxiv.org/pdf/1609.04747.pdf)
3. [为什么数学概念中，将凸起的函数称为凹函数？](https://www.zhihu.com/question/20014186)
4. [什么是指数加权平均、偏差修正？](https://www.cnblogs.com/guoyaohua/p/8544835.html)
5. [An overview of gradient descent optimization algorithms](http://sebastianruder.com/optimizing-gradient-descent/index.html#fn:24)
6. http://www.redcedartech.com/pdfs/Select_Optimization_Method.pdf
7. https://stats.stackexchange.com/questions/55247/how-to-choose-the-right-optimization-algorithm
