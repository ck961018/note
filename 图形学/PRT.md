# PRT

## 1.基本概念
> &emsp;&emsp;预计算辐射传输是一种应用球谐函数将实时渲染环境光照中的计算开销转移到离线预计算的技术。

## 2.推导
>  + #### diffuse材质
> $$L{o}=\int_\Omega L(i)V(i)\rho(i,o)max(0,n\cdot i)\,\mathrm{d}i$$
> &emsp;&emsp;假设对于任意点任意方向，BRDF为常数。
> $$\begin{aligned}L{o}&=\rho\int_\Omega L(i)V(i)max(0,n\cdot i)\,\mathrm{d}i\\ &=\rho\sum l_i\int_\Omega B_i(i)V(i)max(0, n\cdot i)\,\mathrm{d}i \end{aligned}$$
> &emsp;&emsp;上式应用了球谐函数，将$L(i)$看作光照项（lighting）、$V(i)max(0, n\cdot i)$看作传输项（light transport）。对于右式中传输项和基函数乘积的积分，可以应用球谐函数的性质，使$T_i=\int_\Omega B_i(i)V(i)max(0, n\cdot i)di$，$T_i$即传输项的球谐函数系数。最后用黎曼积分算出$l_i$和$T_i$。
> $$l_i=\sum_sB_i(w_i)L_i(w_i)\Delta w_i \\ T_i=\sum_sB_i(w_i)V(w_i)max(0, n\cdot w_i)\Delta w_i$$
> &emsp;&emsp;预计算完成，在实时渲染时计算$L_o=\rho \sum_i l_iT_i$即可。
>  + #### glossy材质
> &emsp;&emsp;由于镜面反射不仅与入射光有关，也跟观察方向有关，BRDF是四维的（入射两维、出射两维）。
> $$\begin{aligned}L{o}&=\int_\Omega L(i)V(i)\rho(i,o)max(0,n\cdot i)\,\mathrm{d}i\\ &=\sum l_iT_i(o) \\ &=\sum(\sum l_it_{ij})B_j(o) \end{aligned}$$
> &emsp;&emsp;计算和存储的开销都很大。