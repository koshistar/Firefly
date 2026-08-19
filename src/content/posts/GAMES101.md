---
title: GAMES101课程笔记
published: 2026-07-14
pinned: false
description: 计算机图形学基础
tags: [学习记录]
category: 基础知识
draft: false
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

subdivision  
Loop Subdivision  
Catmull-Clark Subdivision(General Mesh)  

simplification  
Collapsing An Edge  

Shadows  
Shadow Mapping  
# Ray Tracing
Ray Equation  
- Ray is defined by its origin and a direction vector  
- $ \bold r(t) = \bold o + t\bold d  , 0 \le t \lt \infty $
- Sphere: $ \bold p: {(\bold p- \bold c)}^2 - R^2 = 0 $  
- General implicit surface: $ \bold p: f(\bold p) = 0, f(\bold o + t\bold d) = 0 $  

Plane Equation  
- Plane is defined by normal vector and a point on plane  
- $ \bold p: (\bold p - \bold p')\cdot \bold N = 0 $  

Moller Trumbore Algorithm  
- $ \bold O + t \bold D = (1-b_1-b_2) \bold P_0 + b_1 \bold P_1 + b_2 \bold P_2 $  
- $ \begin{pmatrix} t \\ b_1 \\ b_2 \end{pmatrix} = \frac{1}{\bold S_1 \cdot \bold E_1} \begin{pmatrix} \bold S_2 \cdot \bold E_2 \\ \bold S_1 \cdot \bold S \\ \bold S_2 \cdot \bold D \end{pmatrix}, Where \bold E_1 = \bold P_1 - \bold P_0, \bold E_2 = \bold P_2 - \bold P_0, \bold S = \bold O - \bold P_0, \bold S_1 = \bold D \times \bold E_2, \bold S_2 = \bold S \times \bold E_1 $  

Accelerating Ray-Surface Intersection  
Bounding Volumes  
- AABB(Axis-Aligned Bounding Box/轴对齐包围盒)  

Spatial Partitioning  
- Octree  
- KD-Tree  
- BSP-Tree  

Bounding Volume Hierarchy(BVH)  
``` 
Intersect(Ray ray, BVH node)
{
    if(ray misses node.bbox) return;

    if(node is a leaf node)
        test intersection with all objs;
        return closest intersection;

    hit1=Intersect(ray, node.child1);
    hit2=Intersect(ray, node.child2);
    return the closer of hit1, hit2;
}
```

## Basic Radiometry(辐射度量学)  
Radiant Energy and Flux(Power)  
- Definition: Radiant energy: $ Q[J=Joule] $; Radiant flux(Power): $ \Phi \equiv \frac{dQ}{dt}[W=Watt][lm=lumen] $

Radiant Intensity  
- $ I(\omega) \equiv \frac{d\Phi}{d\omega}; [\frac{W}{sr}][\frac{lm}{sr}=cd=candela] $
- power per solid angle  
- Angles and Solid Angles: Angle: $ \theta = \frac{l}{r} $; Solid angle: $ \Omega = \frac{A}{r^2} $
- Isotropic Point Source: $ \Phi = \int_{S^2} I d\omega = 4\pi I; I = \frac{\Phi}{4\pi}$

Irradiance  
- $ E(x) = \frac{d\Phi(x)}{dA};[\frac{W}{m^2}][\frac{lm}{m^2} = lux] $  
- power per projected unit area  

Radiance  
- $ L(p,\omega) = \frac{d^2\Phi(p,\omega)}{d\omega dA\cos \theta};[\frac{W}{sr.m^2}][\frac{cd}{m^2} = \frac{lm}{sr.m^2} = nit] $
- Irradiance per solid angle / Intensity per projected unit area
- Incident Radiance: $ L(p,\omega) = \frac{dE(p)}{d\omega \cos\theta} $ ; Exiting Radiance: $ L(p,\omega) = \frac{dI(p,\omega)}{dA\cos\theta} $
- $ dE(p,\omega)=L_i(p,\omega)cos\theta d\omega $; $ E(p) = \int_{H^2} L_i(p,\omega)cos\theta d\omega $  

## Bidirectional Refletance Distribution Function (BRDF)  
Reflection at a Point  
- Differential irradiance incoming: $ dE(\omega_i) = L(\omega_i)\cos \theta_i d\omega_i $
- Differential radiance exiting (due to $dE(\omega_i)$): $dL_r(\omega_r)$  
- BRDF：$ f_r(\omega_i \rightarrow \omega_r) = \frac{dL_r(\omega_r)}{dE_i(\omega_i)}=\frac{dL_r(\omega_r)}{L_i(\omega_i)\cos\theta_i d\omega_i};[\frac{l}{sr}] $  

The Reflection Equation  
- $ L_r(p,\omega_r)=\int_{H^2} f_r(p,\omega_i \rightarrow \omega_r)L_i(p,\omega_i)cos\theta_i d\omega_i $  

The Rendering Equation  
- $ L_o(p,\omega_o) = L_e(p,\omega_o) + \int_{\Omega+} L_i(p,\omega_i)f_r(p,\omega_i, \omega_o)(n\cdot\omega_i)d\omega_i $

## Monte Carlo Integration  
- $ \int f(x)dx = \frac{1}{N}\sum_{i=1}^N \frac{f(X_i)}{p(X_i)}, X_i \sim p(x) $  

## Path Tracing  
- A Simple Monte Carlo Solution:$ L_o(p,\omega_o) = \int_{\Omega+} L_i(p,\omega_i)f_r(p,\omega_i, \omega_o)(n\cdot\omega_i)d\omega_i \approx \frac{1}{N}\sum_{i=1}^N \frac{L_i(p,\omega_i)f_r(p,\omega_i, \omega_o)(n\cdot\omega_i)}{p(\omega_i)}$
```
shade(p,wo)
    Randomly choose N directions wi~pdf
    Lo=0.0
    For each wi
        Trace a ray r(p,wi)
        If ray r hit the light
            Lo+=(1/N)*L_i*f_r*cosine/pdf(wi)
        Elese If ray r hit an object at q
            Lo+=(1/N)*shade(q,-wi)*f_r*cosine/pdf(wi)
    Return Lo
```
```
shade(p,wo)
    Randomly choose ONE direction wi~pdf(w)
    Trace a ray r(p,wi)
    If ray r hit the light
        Return L_i*f_r*cosine/pdf(wi)
    Elese If ray r hit an object at q
        Return shade(q,-wi)*f_r*cosine/pdf(wi)
```

Ray Generation
```
ray_generation(camPos,pixel)
    Uniformly choose N sample positions within the pixel
    pixel_radiance=0.0
    For each sample in the pixel 
        Shoot a ray r(camPos,cam_to_sample)
        If ray r hit the scene at p
            pixel_radiance+=1/N*shade(p,sample_to_cam)
    Return pixel_radiance
```
Russian Roulette(RR)  
- $ E=P*(Lo/P)+(1-P)*0=Lo $
```
shade(p,wo)
    Manually specify a probability P_RR 
    Randomly select ksi in a uniform dist. in [0,1]
    If (ksi>P_RR) return 0.0;
    Randomly choose ONE direction wi~pdf(w)
    Trace a ray r(p,wi)
    If ray r hit the light
        Return L_i*f_r*cosine/pdf(wi)/P_RR
    Elese If ray r hit an object at q
        Return shade(q,-wi)*f_r*cosine/pdf(wi)/P_RR
```
Sampling the light  
```
shade(p,wo)
    # Contribute from the light source 
    Uniformly sample the light ar x' (pdf_light=1/A)
    L_dir = L_i*f_r*cos theta * cos theta' / |x'-p|^2 / pdf_light

    #Contribution from other refletors  
    L_indir=0.0
    Test Russian Roulette with probability P_RR
    Uniformly sample the hemisphere toward wi (pdf_hemi = 1/2pi)
    Trace a ray r(p,wi)
    If ray r hit a non-emitting object at q
        L_indir=shade(q,-wi)*f_r*cos theta / pdf_hemi / P_RR
    Return L_dir+L_indir
```

## Materials and Apperances  
Materials=BRDF  
Diffuse/Lambertian material  
- $ L_o(\omega_o)=\int_{H^2}f_r L_i(\omega_i)\cos \theta_i d\omega_i=f_r L_i \int_{H^2}\cos \theta_i d\omega_i = \pi f_r L_i$
- $ f_r=\frac{\rho}{\pi}, \rho - albedo(color) $  

Glossy material  
Ideal reflective/refractive material  
Fresnel Reflection / Term  
Microfacet Material  
- $ f(i,o)=\frac{F(i,h)G(i,o,h)D(h)}{4(n,i)(n,o)} $
- F(i,h)- Fresnel term; G(i,o,h)- shadowing-masking term; D(h)- distribution of normals  

Isotropic/Anisotropic materials  
Properties of BRDFS  
- Non-negetivity $ f_r(\omega_i \rightarrow \omega_r) \geq 0 $
- Linearity $ L_r(p,\omega_r)=\int_{H^2}f_r(p,\omega_i \rightarrow \omega_r)L_i(p,\omega_i)d\omega_i $ 
- Reciprocity principle $ f_r(\omega_i \rightarrow \omega_r) = f_r(\omega_r \rightarrow \omega_i) $
- Energy conservation $ \forall \omega_r \int_{H_2}f_r(\omega_i \rightarrow \omega_r)\cos \theta_i d\omega_i \leq 1  $ 
- Isotropic vs. Anisotropic  
-- If isotropic, $ f_r(\theta_i,\Phi_i;\theta_r,\Phi_r)=f_r(\theta_i,\theta_r,\Phi_r-\Phi_i) $
-- Then, from reciprocity, $ f_r(\theta_i,\theta_r,\Phi_r-\Phi_i)=f_r(\theta_r,\theta_i,\Phi_i-\Phi_r)=f_r(\theta_i,\theta_r,|\Phi_r-\Phi_i|) $

Measuring BRDFS

## Advanced Tropics in Rendering
Advanced Light Transport  
- Unbiased light transport methods  
-- Bidirectional path tracing(DSPT)  
-- Metropolis light transport(MLT)  
- Biased light transport methods  
-- Photon mapping  
-- Vertex connection and merging(VCM)  
- Instant radiosity(VPL/many light methods)  

Advanced Appearance Modeling  
- Non-surface models  
-- Prticipating Media  
-- Hair Appearance: Kajiya-Kay Model; Marschner Model  
-- Fur Appearance: Double Cylinder Model  
-- Granular Material  
- Surface Models  
-- Translucent Material: BSSRDF  
-- Cloth  
-- Detailed Appearance: Motivation  

## Others  
Pinhole Image Formation  
Filed of View(FOV)  
Exposure: Aperture, Shutter, Gain(ISO)  
Thin Lens Approximation  
Defocus Blur  
Ray Tracing Ideal Thin Lenses  
Light Field/Lumigraph  
Physical Basis of Color  
Metamerism  
Color Space  
# Animation / Simulation
Keyframe Animation  
Physical Simulation(质点弹簧系统)  
Particle systems  
Forward Kinematics  
Inverse Kinematics  
Rigging  
Motion Capture  
Single particle simulation  
Rigid Body Simulation  
Fluid Simulation  
