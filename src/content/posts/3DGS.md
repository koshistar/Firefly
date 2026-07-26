---
title: 3DGS解析
published: 2026-07-01
pinned: false
description: 3d gaussian-splatting原理解析
tags: [科研，学习记录]
category: 科研
draft: false
---

# 3DGS基础理解

## 定义 
一种体渲染方法：从3D物体渲染到2D平面  
Ray-cast是被动的（NeRF） -计算出每个相随但受到发光例子的影响来生成图像  
Splatting是主动的（3DGS） - 计算出每个发光粒子如何影响像素点  

# Splatting

## Splatting基本理解
Splat: 拟声词，【啪唧一声】  
抛雪球法、足迹法，翻译成喷溅  
核心：选择【雪球】，抛掷雪球（从3D投影到3D），加以合成，形成最后的图像  

## 选择核——高斯  
很好地数学性质：  
- 仿射变换后高斯核仍然闭合  
- 3D降维到2D后（沿着某一个轴积分），依然为高斯  

定义：  
- 椭球高斯
$$
G(\mathbf{x})=\frac{1}{\sqrt{(2\pi)^{k} |\boldsymbol{\Sigma}|}} e^{( -\frac{1}{2} (\mathbf{x} - \boldsymbol{\mu})^\top \boldsymbol{\Sigma}^{-1} (\mathbf{x} - \boldsymbol{\mu}) )}
$$
- $\boldsymbol{\Sigma}$表示协方差矩阵，半正定，$|\boldsymbol{\Sigma}|$是其行列式；$\boldsymbol{\mu}$表示均值  
- $( (\mathbf{x} - \boldsymbol{\mu})^\top \boldsymbol{\Sigma}^{-1} (\mathbf{x} - \boldsymbol{\mu}) ) = constant$，定义了一个椭球面，$G(\mathbf{x;{\mu},{\Sigma}}) = [0,1]$表示大椭球壳套小椭球壳，即三维高斯是一个实心的椭球  

### 协方差矩阵
高斯分布：
- 一维：均值&方差
- 二维：均值&协方差矩阵  

协方差矩阵：
- 是一个对称矩形，巨鼎高斯分布形状  
- 对角线上元素为x轴/y轴/z轴的方差
- 反斜对角线上的值为协方差，表示x和y，x和z，y和z的线性相关程度  
$$
\boldsymbol{\Sigma}=\begin{bmatrix}\sigma_{x}^{2} & \sigma_{xy} & \sigma_{xz} \\ \sigma_{yx} & \sigma_{y}^{2} & \sigma_{yz} \\ \sigma_{zx} & \sigma_{zy} & \sigma_{z}^{2} \end{bmatrix}
$$

### 各向同性&各向异性
各向同性(Isotropic)：
- 在所有方向具有相同的扩散程度（梯度）
- 球
- 3d高斯分布：协方差举证是对角矩阵
$$
\boldsymbol{\Sigma}=\begin{bmatrix}\sigma^{2} & 0 & 0 \\ 0 & \sigma^{2} & 0 \\ 0 & 0 & \sigma^{2} \end{bmatrix}
$$

各向异性(Anisotropic)：
- 在不同方向具有不同的扩散程度（梯度）
- 椭球
- 3d高斯分布：协方差矩阵是对称矩阵
$$
\boldsymbol{\Sigma}=\begin{bmatrix}\sigma_{x}^{2} & \sigma_{xy} & \sigma_{xz} \\ \sigma_{yx} & \sigma_{y}^{2} & \sigma_{yz} \\ \sigma_{zx} & \sigma_{zy} & \sigma_{z}^{2} \end{bmatrix}
$$

### 仿射变换
$$
\boldsymbol{w}=A\boldsymbol{x}+b
$$

$$
\boldsymbol{w} \sim N(A\boldsymbol{\mu}+b,A\boldsymbol{\Sigma}A^\top)
$$

标准高斯分布：
- $\boldsymbol{w} \sim N(\overrightarrow{0},1)$
- 协方差矩阵$\begin{bmatrix}1 & 0 &0 \\ 0 & 1 & 0 \\ 0 & 0 & 1 \end{bmatrix}$
- $\boldsymbol{\Sigma} = A \cdot I \cdot A^\top $

任意高斯可以看做是标准高斯通过仿射变换得到

### 用旋转和缩放矩阵表达协方差矩阵
$$
A = RS
$$ 
$$
\boldsymbol{\Sigma}= A \cdot I \cdot A^\top = R \cdot S \cdot I \cdot (R \cdot S)^\top = R \cdot S \cdot S^\top \cdot R^\top
$$

## 3D到像素

观测变换 -> 投影变换 -> 视口变换 -> 光栅化  
### 观测变换
物理坐标系
- 高斯核中心：$ t_k = [t_0 ~ t_1 ~ t_2]\top $
- 高斯核：$r_k''t=G_{V_k''}(t-t_k)$
- $V_k''$是协方差矩阵  

相机坐标系
- 高斯核中心：$ \upsilon_k = [\upsilon_0 ~ \upsilon_1 ~ \upsilon_2]\top $ 
- 高斯核：$r_k't=G_{V_k'}(\upsilon-\upsilon_k)$
- 均值 $\upsilon_k= Wt_k+d$
- 协方差矩阵 $V_k' = WV_k''W^\top$  

### 投影变换
相机坐标系
- 高斯核中心：$ \upsilon_k = [\upsilon_0 ~ \upsilon_1 ~ \upsilon_2]\top $ 
- $V_k'$是协方差矩阵  

投影变换： 
- 高斯核中心 $x_k=[x_0 ~ x_1 ~ x_2] $
- 高斯核：$r_k(x)=G_{V_k}(x-x_k)$
- 均值 $x_k= m(\upsilon_k)$
- 不能直接使用投影变换：从透视投影到正交投影是非线性变换，非仿射——引入雅可比矩阵  
- 协方差矩阵 $V_k = J V_k' J\top=J W V_k'' W\top J^\top$ 
- $\boldsymbol{J} = \begin{bmatrix}\frac{df_1}{dx} & \frac{df_1}{dy} & \frac{df_1}{dz} \\ \frac{df_2}{dx} & \frac{df_2}{dy} & \frac{df_2}{dz} \\ \frac{df_3}{dx} & \frac{df_3}{dy} & \frac{df_3}{dz}  \end{bmatrix}=\begin{bmatrix} \frac{n}{z} & 0 & -\frac{nx}{z^2} \\ 0 & \frac{n}{z} & -\frac{ny}{z^2} \\ 0 & 0 & \frac{nf}{z^2} \end{bmatrix}$

雅可比矩阵：
- 坐标变换：f_1(x)=x+sin(y),f_2(y)=y+sin(x)  
- 雅可比矩阵：$J=\begin{bmatrix}\frac{\partial f_1}{\partial x} & \frac{\partial f_1}{\partial y} \\ \frac{\partial f_2}{\partial x} & \frac{\partial f_2}{\partial y} \end{bmatrix} = \begin{bmatrix}1 & cos(y) \\ cos(x) & 1 \end{bmatrix}$  

### 视口变换

投影变换后： 
- 高斯核中心 $x_k=[x_0 ~ x_1 ~ x_2] $
- 高斯核：$r_k(x)=G_{V_k}(x-x_k)$
- 足迹函数 

像素坐标系：
- 高斯核中心 $\mu_k=[\mu_1 ~ \mu_2 ~ \mu_3] $，平移+缩放  
- 足迹渲染（离散计算）：$G(\hat{x}) = e^{-\frac{1}{2} (\hat{x} - \mu_k)^\top V_k^{-1} (x - \mu)}$  

## 3D高斯球的颜色

基函数 
- 任何一个函数可以分解为正弦与余弦的线性组合
- $f(x)=a_0+\sum_{n=1}^{+\infty} a_n cos(\frac{n\pi}{l})x+b_n sin(\frac{n\pi}{l})x$
- $cos(\frac{n\pi}{l})x,sin(\frac{n\pi}{l})x$是基函数  

球谐函数
- 任何一个球面坐标的函数可以用多个球谐函数来近似  
- $f(t)\approx \sum_{l}^{} \sum_{m=-l}^{l} c_l^my_l^m(\theta,\Phi)$
- 其中，$c_l^m$各项系数 $y_l^m$是基函数  
![看不懂的球谐函数](.\images\SphericalHarmonics.png)
![像素的颜色](.\images\PixpelColor.png)

参考视频：[【较真系列】讲人话-3d gaussian splatting全解(原理+代码+公式)【4】 高性能渲染与机器学习](https://www.bilibili.com/video/BV1rJ4m1g7Un?vd_source=368035710b365f19a8d10ff50ad5857b)

原版高斯泼溅：[3D Gaussian Splatting
for Real-Time Radiance Field Rendering](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/)