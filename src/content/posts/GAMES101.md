---
title: GAMES101课程笔记
published: 2026-07-14
pinned: false
description: 计算机图形学基础
tags: [学习记录]
category: 基础知识
draft: true
---

# Rasterization
## Transform
Scale Transform  
- $ \begin{pmatrix}
x' \\
y' 
\end{pmatrix} = \begin{pmatrix} s_x & 0 \\ 0 & s_y \end{pmatrix} \begin{pmatrix} x \\ y \end{pmatrix}$
- Reflection Matrix $ \begin{pmatrix} -1 & 0 \\ 0 & 1 \end{pmatrix} $
- Shear Matrix $ \begin{pmatrix} 1 & a \\ 0 & 1 \end{pmatrix} $  

Rotate transform
- Homogeneous Coordinate: 2D point $ \begin{pmatrix} x \\ y \\ 1 \end{pmatrix} $ 2D 
- Rotation Matrix $ \begin{pmatrix} \cos \theta & -\sin \theta \\ \sin \theta & \cos \theta \end{pmatrix} $ 

Translate Transform
- Translation Matrix $ \begin{pmatrix} 1 & 0 & t_x \\ 0 & 1 & t_y \\ 0 & 0 & 1 \end{pmatrix} $

Affine Transform
- using affine map = linear map + translation $\begin{pmatrix}x' \\ y \end{pmatrix} = \begin{pmatrix} a_{11} & a_{12} \\ a_{21} & a_{22} \end{pmatrix} \begin{pmatrix} x \\ y \end{pmatrix} + \begin{pmatrix} t_x \\ t_y \end{pmatrix}$
- using homogenous coordinates $\begin{pmatrix} x' \\ y' \\ 1 \end{pmatrix} = \begin{pmatrix} a_{11} & a_{12} & a_{13} \\ a_{21} & a_{22} & a_{23} \\ 0 & 0 & 1 \end{pmatrix} \begin{pmatrix} x \\ y \\ 1 \end{pmatrix}$

Inverse Transform
- $\bold M^{-1}$

Decomposing of Complex Transforms
 
3D Transform
- Rotation around x-,y-, or z-axis $ \begin{pmatrix} 1 & 0 & 0 & 0 \\ 0 & \cos \theta & -\sin \theta & 0 \\ 0 & \sin \theta & \cos \theta  & 0 \\ 0 & 0 & 0 & 1 \end{pmatrix} $ $ \begin{pmatrix} \cos \theta & 0 & \sin \theta & 0 \\ 0 & 1 & 0 & 0 \\ -\sin \theta & 1 & \cos \theta  & 0 \\ 0 & 0 & 0 & 1 \end{pmatrix} $ $ \begin{pmatrix} \cos \theta & -\sin \theta & 0 & 0 \\ \sin \theta & \cos \theta & 0 & 0 \\ 0 & 0 & 1 & 0 \\ 0 & 0 & 0 & 1 \end{pmatrix} $
- $ R_{xyz}(\alpha,\beta,\gamma) = R_x(\alpha)R_y(\beta)R_z(\gamma)$， $ \alpha,\beta,\gamma $ are called Euler angles

Rodrigues' Rotation Formula
- $ R(\theta) = \cos \alpha \bold I + (1-\cos \theta) \bold n \bold n \top + \sin \theta N $
- $ N = \begin{pmatrix} 0 & -n_z & n_y \\ n_z & 0 & -n_x \\ -n_y & n_x & 0 \end{pmatrix} $

View/Camera Transformation
- MVP(Model View Projection)
- Define camera: Position $ \overrightarrow e $, Look-at/gaze direction $ \overrightarrow g $, Up direction $ \overrightarrow t $
- $ M_{view} = R_{view}T_{view} $
- $ T_{view}= \begin{pmatrix} 1 & 0 & 0 & -x_e \\ 0 & 1 & 0 & -y_e \\ 0 & 0 & 1 & -z_e \\ 0 & 0 & 0 & 1 \end{pmatrix} $
- $ R_{view}^{-1} = \begin{pmatrix} x_{\hat g \times \hat t} & x_t & x_{-g} & 0 \\ y_{\hat g \times \hat t} & y_t & y_{-g} & 0 \\ z_{\hat g \times \hat t} & z_t & z_{-g} & 0 \\ 0 & 0 & 0 & 1 \end{pmatrix} $ -> $ R_{view} = \begin{pmatrix} x_{\hat g \times \hat t} & y_{\hat g \times \hat t} & z_{\hat g \times \hat t} & 0 \\ x_t & y_t & z_t & 0 \\ x_{-g} & y_{-g} & z_{-g} & 0 \\ 0 & 0 & 0 & 1 \end{pmatrix} $

Projection Transformation  
Orthographic Projection(右手坐标系) 
- $ M_{ortho} = \begin{pmatrix} \frac{2}{r-l} & 0 & 0 & 0 \\ 0 & \frac{2}{t-b} & 0 & 0 \\ 0 & 0 & \frac{2}{n-f} & 0 \\ 0 & 0 & 0 & 1 \end{pmatrix} \begin{pmatrix} 1 & 0 & 0 & -\frac{r+l}{2} \\ 0 & 1 & 0 & -\frac{t+b}{2} \\ 0 & 0 & 1 & -\frac{n+f}{2} \\ 0 & 0 & 0 & 1 \end{pmatrix} $ 

Perspective Projection  
- $ M_{persp} = M_{ortho}M_{persp->ortho} $
- $ M_{persp->ortho} = \begin{pmatrix} n & 0 & 0 & 0 \\ 0 & n & 0 & 0 \\ 0 & 0 & n+f & -nf \\ 0 & 0 & 1 & 0 \end{pmatrix} $

## Rasterization
Sampling  
Inside: Three cross products  
Aliasing(Jaggies)  
  
Antialiasing  
Blurring(Pre-Filtering) Before Sampling  
Fourier Transform  
- $ F(\omega) = \int_{-\infty}^{\infty} f(x) e^{-2\pi i \omega x} dx $
- $ f(x) = \int_{-\infty}^{\infty} F(\omega) e^{2\pi i \omega x} d\omega $  
- $ e^{ix} = \cos x + i \sin x$  

Filter = Convolution  
Wider Filter Kernel = Lower Frequencies  
Sampling = Repeating Frequency Contents  
Aliasing = Mixed Frequency Contents  
Antialiasing = Limiting, then repeating  
Antialiasing By Supersampling(MSAA)  
FXAA(Fast Approximate AA)  
TAA(Temporal AA)  
  
Z-buffering  

## Shading  
### Blinn-Phong Reflectance Model  
- Inputs: Viewer direction(v), Surface normal(n), Light direction(l), Surface parameters(color, shininess, ...)

$ L = L_a + L_d + L_s = k_aI_a + k_d(\frac{I}{r^2})max(0,\bold n \cdot \bold l) + k_s(\frac{I}{r^2})max(0,\bold n \cdot \bold h)^p $  
  
Diffuse Reflection  
- Lamberts's cosine law  
- $ L_d = k_d(\frac{I}{r^2})max(0,\bold n \cdot \bold l) $
  
Specular Term  
- 半程向量 $ \bold h = bisector(\bold v, \bold l)= \frac{\bold l + \bold v}{\|\bold l + \bold v\|} $  
- $ L_s = k_s(\frac{I}{r^2})max(0,\cos \alpha)^p = k_s(\frac{I}{r^2})max(0,\bold n \cdot \bold h)^p $  

Ambient Term  
- $ L_a = k_aI_a $  

### Shading Frequency  
Shading freqncy: Face Vertex Pixel   
Shading type: Flat Gouraud Phong  
Defining Per-Vertex Normal Vectors  
- $ N_v = \frac{\varSigma_i N_i}{\| \varSigma_i \cdot N_i \|} $

Defining Per-Pixel Normal Vectors  

### Graphics(Real-time Rendering) Pipeline  
Application -> Vertex Proceessing -> Trangle Processing -> Rasterization -> Fragment Processing -> Framebuffer Operations -> Display  

### Texture Mapping  

Interpolation Across Triangles: Barycentric Coordinates(重心坐标)  
- $ (x,y) = \alpha A + \beta B + \gamma C $
- $ \alpha + \beta + \gamma = 1 $
- $ (\alpha,\beta,\gamma) = (\frac{1}{3},\frac{1}{3},\frac{1}{3})$

Texture Magnification  
Nearest Neighbor Interpolation  
Bilinear Interpolation  
Bicubic Interpolation  

Mipmap  
Trilinear Interpolation  

Anisotropic Filtering  
EWA filtering  

Applications of textures  
Environment Lighting  
Bump Mapping  
Displacement Mapping  
...  
# Curves and Mesh
## Geometry  
Constructive Solid Geometry(CSG)  
Distance Functions  
Fractals  

### Curves  
Bezier Curves  
de Casteljau Algorithm  
- $ b_0^1(t) = (1-t)b_0 + tb_1 $  
- $ b_1^1(t) = (1-t)b_1 + tb_2 $  
- $ b_0^2(t) = (1-t)b_0^1 + tb_1^1 $  
- $ b^n(t) = b_0^n(t) = \Sigma_{j=0}^n b_jB_j^n(t) $
- Bernstein polynomials $ B_i^n(t) = \binom{n}{i}t^i(1-t)^{n-i} $  

Properties of Bezier Curves  
- Interpolates endpoints
- Tangent to end segments  
- Affine transformation property  
- Convex hull property  

Other types of splines  
B-Splines  

### Surfaces  
Bezier Surfaces  
Mesh Operations: Geometry Processing  


# Ray Tracing

# Animation / Simulation
