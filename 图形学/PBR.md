# PBR

## 1.基本概念
> &emsp;&emsp;基于物理的渲染，包括三个方面：基于物理的材质、基于物理的相机、基于物理的光照。

## 2.BRDF
> &emsp;&emsp;PBR中的BRDF一般用Cook-Torrance BRDF模型。
> $$f_r=k_df_{lambert}+k_sf_{cook-torrance}$$
> &emsp;&emsp;$k_d$和$k_s$分别表示入射光线漫反射和镜面反射的比率，漫反射项$f_{lambert}=\frac{c}{\pi}$（以均匀入射光的假设推导），其中$c$表示颜色。
> &emsp;&emsp;镜面反射部分形式如下：
> $$f_{cook-torrance}=\frac{D(h)G(l,v)F(v,h)}{4(w_o\cdot n)(w_i\cdot n)}$$
> + &emsp;&emsp;$D(h)$为法线分布函数（Normal Distribution Function），估算在表面粗糙度的影响下，法线朝向与半程向量一致的微表面比率。
> + &emsp;&emsp;$G(l, v)$为几何函数(Geometry Function)，描述微平面自遮挡的情况。
> + &emsp;&emsp;$F(v, h)$为菲涅尔方程(Fresnel Rquation)，表示不同角度反射光所占比率。一般用Schlick近似求解：
> $$F_{Schlick}(h, v, F_0)=F_0+(1-F_0)(1-(h\cdot v))^5  $$

## 3.IBL
> &emsp;&emsp;基于图像的光照（Image based lighting）是一种基于预处理加速图像光照渲染的方法。
> &emsp;&emsp;首先列出反射方程：
> $$L_o(p,w_o)=\int_{\Omega}(k_d\frac{c}{\pi}+k_s(F)\frac{DG}{4(n\cdot w_i)(n\cdot w_o)})L_i(p,w_i)(n\cdot w_i)\mathrm{d}w_i$$
> + #### 漫反射部分
> &emsp;&emsp;对于反射方程中的漫反射项，可以将常数项移出积分：$L_o(p,w_o)=k_d\frac{c}{\pi}\int_{\Omega}L_i(p,w_i)(n\cdot w_i)\mathrm{d}w_i$。当处理天空盒时，默认处于中心，可以忽略位置信息，因此，只需要考虑$L(n)=\int_{\Omega}L_i(w_i)(n\cdot w_i)\mathrm{d}w_i$。于是，我们保存一张cubemap，记录法线对应的$L(n)$，使用时直接查询即可。
> &emsp;&emsp;利用蒙特卡洛采样计算积分，假设均匀漫反射，则$L(n)=\int_{\Omega}L_i(w_i)(n\cdot w_i)\mathrm{d}w_i=\frac{1}{n1\cdot n2}\sum_{m=0}^{n1}\sum_{n=0}^{n2}L_i(\phi_m,\theta_n)sin\theta_n cos\theta_n$
> + #### 镜面反射部分
> &emsp;&emsp;对于镜面反射，需要先利用split sum近似将$L_i$项移出积分:
> $$L_o(w_o)\approx\frac{\int_\Omega L_i(w_i)dw_i}{\int_\Omega dw_i}\int_\Omega f_s(w_i)(n\cdot w_i)\mathrm{d}w_i$$
> &emsp;&emsp;该近似成立的条件是支撑域较小且某一被积项光滑。
> &emsp;&emsp;在镜面反射中，计算积分不能用均匀的蒙特卡洛采样，而要考虑重要性采样，可以用法线分布函数（Normal Distribution Function）作为分布函数。又考虑到复杂度，假设观察视角方向等于法线方向$(w_o=n)$。由于这个假设，我们又需要为不同角度设置不同权重$W(w_i)$。再经过一些近似，最终式子如下：
> $$L_o(w_o)\approx\frac{\sum_k^N L_i(w_i^k)W(w_i^k)}{\sum_k^NW(w_i^k)}\int_\Omega f_s(w_i)(n\cdot w_i)\mathrm{d}w_i$$
> &emsp;&emsp;这样，左半部分就可以算出来了。右半部分可以考虑把菲涅尔方程中的$F_0$移出积分：
> $$\begin{aligned}\int_\Omega f_s(w_i)(n\cdot w_i)\mathrm{d}w_i&=\int_\Omega \frac{f_s(w_i)}{F}(F_0+(1-F_0)(1-w_o\cdot h)^5)(n\cdot w_i)\mathrm{d}w_i\\ &=F_0\int_\Omega \frac{f_s(w_i)}{F}(1-(1-w_o\cdot h)^5)(n\cdot w_i)\mathrm{d}w_i\\ &\quad+ \int_\Omega \frac{f_s(w_i)}{F}(1-w_o\cdot h)^5(n\cdot w_i)\mathrm{d}w_i\\ &=F_0\ast scale + bias \end{aligned}$$
> &emsp;&emsp;$scale$与$bias$同样根据以法线分布函数为分布的重要性采样来计算，过程中可以约去D项，只需要记录角度和粗糙度为维度的LUT二维贴图即可。