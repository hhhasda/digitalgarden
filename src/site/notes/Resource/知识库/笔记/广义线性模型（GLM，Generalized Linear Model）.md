---
{"dg-publish":true,"permalink":"/Resource/知识库/笔记/广义线性模型（GLM，Generalized Linear Model）/"}
---






# 1 理论介绍
## 1.1 引言

当对数据进行回归分析时，经常会用到两类回归模型： 

一类是针对连续型数据进行的线性回归，如下图（图片来源：维基百科）所示

![Pasted image 20231114154050.png](/img/user/Resource/%E5%9B%BE%E7%89%87%E5%BA%93/Pasted%20image%2020231114154050.png)

这种回归模型的公式是：  

$$y=a_0+a_1x_1+a_2x_2+\cdots+a_nx_n$$
{ #31f292}


还有一类是针对分类数据进行的 logistic 回归，如下图（图片来源）  
  
![Pasted image 20231114155246.png](/img/user/Resource/%E5%9B%BE%E7%89%87%E5%BA%93/Pasted%20image%2020231114155246.png)

$$y=\frac1{1+e^{-(a_0+a_1x_1+a_2x_2+\cdots+a_nx_n)}}$$

其中

$$y=\frac1{1+e^{-x}}$$

是 [[Resource/知识库/笔记/sigmoid 函数\|sigmoid 函数]]，**可以用来做二分类**，其图像如下

![Pasted image 20231114155927.png](/img/user/Resource/%E5%9B%BE%E7%89%87%E5%BA%93/Pasted%20image%2020231114155927.png)



> [!note] Note:数学伎俩——拼凑推广
> 这两种回归模型，是否有可能统一到一种更加抽象的模型中？这两种形式完全不一样的模型，是不是只是某种更加广义的模型的特例？  


## 1.2 广义线性模型的定义  
  
> [!Define] Define 1 (广义线性模型(Generalized Linear Model)，简称 GLM)
> 广义线性模型建立在三个定义的基础上，分别为： 
> 1. 定义线性预测算子
> 	$$\eta=\theta^Tx$$
> 2. 定义 y 的估计值 
>    $$h(x,\theta)=E(y|x,\theta)$$
> 3. 定义 y 的估值概率分布属于某种指数分布族
>    $$f(y|x,\theta)=b(y)exp(\eta^TT(y)-a(\eta))$$


石老师可能有些看不懂？别着急，接下来详细解释各个定义  

### 1.2.1 定义一 ：线性预测算子

广义线性模型的名字中有『线性』两个字，自然它包含了线性计算的过程，也就是它的假设之一，定义线性预测算子(linear predictor)为：
$$\eta=\theta^Tx$$
这里的 $\theta$ 和 $x$ 都是向量，写成标量形式就是：
$$\eta=\theta_0x_0+\theta_1x_1+\cdots+\theta_nx_n$$
如果取 $x_0 = 1$。可以发现和 
<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/resource///glm-generalized-linear-model/#31f292" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



$y=a_0+a_1x_1+a_2x_2+\cdots+a_nx_n$ 

</div></div>
 是一样的，只是换了一下字母。

### 1.2.2 定义二：期望估计

先讲一下什么是估计，通常我们需要研究自变量 $x_0,x_1,\cdots,x_n$ 与因变量 $y$ 的关系，这些数据可以通过做实验、爬虫获得。可以把通过给定的自变量 $x$ 以及关于研究对象的参数 $\theta_{0},\theta_{1},\cdots,\theta_{m}$（可能是先验的，也可能需要通过研究得出）估计因变量 $y$ 的过程，称为<mark style="background: #FFF3A3A6;">“估计”</mark>。

> [!example] Example 1（估计）
> 现有身高体重数据若干: 比如 （180,60），（190,70），（170,80），……
> 估计身高 200 的人体重是多少
> 已知身高 $x$ 与体重 $y$ 的关系满足以下分布
> $$\large f(y | \mu, \sigma) = \frac{1}{\sigma \sqrt{2\pi}} e^{-\frac{(y - \mu)^2}{2\sigma^2}}$$
> 其中根据世卫组织 $\mu=(x－80)×0.7=0.7x-56$（自变量 $x$，参数 $\theta_{0}=-56,\theta_{1}=0.7$）
> 将 $y$ 的期望作为对 y 的估计，即
> $$
> h(x,\theta)=E(y|x,\theta)=\mu=0.7x-56=0.7*200-56=84
> $$
> 所以估计身高 200 的人体重是 84 kg
{ #fa0c06}


> [!tip]
> 将均值作为估计值是常见的，即
> $$h(x,\theta)=E(y|x,\theta)$$
> 关于估计的理论，之后感兴趣再讲


### 1.2.3 定义三：指数分布族

可能你已经注意到了，在 [[Resource/知识库/笔记/广义线性模型（GLM，Generalized Linear Model）#^fa0c06\|example 1]] 里面，已知某分布
$$\large f(y | \mu, \sigma) = \frac{1}{\sigma \sqrt{2\pi}} e^{-\frac{(y - \mu)^2}{2\sigma^2}}=\frac{1}{\sigma \sqrt{2\pi}} e^{-\frac{y^{2} - 2\mu y+\mu^{2}}{2\sigma^2}}$$
而满足分布
$$
f(y|x,\theta)=b(y)exp(\eta^TT(y)-a(\eta))
$$
的，就满足广义线性分布。

显然 [[Resource/知识库/笔记/广义线性模型（GLM，Generalized Linear Model）#^fa0c06\|example 1]] 是满足的， $b(y)=\frac{1}{\sigma \sqrt{2\pi}} e^{-\frac{y
{ #2}
}{2\sigma^2}}$，线性预测算子 $\eta^T=\mu=0.7x-56$ ， $T(y)=\frac{y}{\sigma^{2}}$ , $a(\mu)=\frac{\mu^{2}}{2\sigma^{2}}$

指数分布族的性质，见 [[Resource/知识库/笔记/指数分布族\|指数分布族]]。

