#SPH理论

###1. 狄拉克函数与核函数（第一次近似）
$\qquad$对于质点、点电荷等体积趋于无限小，然而密度或电荷密度在空间中的积分却又是有限值的模型，常引入狄拉克$\delta$函数来进行描述。

$$\delta(\mathbf{r})=\left \{ \begin{aligned} &\infin \quad |\mathbf{r}|=0 \\ &0  \ \ \quad other \\ \end{aligned} \right.$$


$\qquad$狄拉克函数为广义函数，无法进行数值积分。我们可以定义一个连续核函数来近似狄拉克函数的过程。一个自然的想法是利用$\sigma$很小的高斯函数，但由于高斯函数需要积分整个R空间，也不适合进行数值积分。我们将光滑核函数定义为$W(\mathbf{r},h)$，其中$\mathbf{r}$为向量差，$h$为平滑半径。

$$\begin{aligned}A(\mathbf{x})&\approx(A*W)(\mathbf{x})\\ &=\int{A(\mathbf{x'})W(\mathbf{x}-\mathbf{x'}, h)}dv' \end{aligned}$$


$\qquad$光滑核函数满足如下条件：
$\qquad$1. 归一化；
$\qquad$2. 狄拉克条件，即 $lim_{h \to 0}W(\mathbf{x},h)=\delta(\mathbf{r})$；
$\qquad$3. 非负性；
$\qquad$4. 对称性；
$\qquad$5. 有界性，即 $W(\mathbf{x},h)=0 \quad\ for\ |\mathbf{r}|>=\hbar$。

$\qquad$将$A(\mathbf{x})$在$\mathbf{x}$处泰勒展开，可以利用第一个和第四个条件进行简化以保证一阶精度。

###2. 离散化（第二次近似）

$$\big<A(\mathbf{x}_i)\big>=\sum_{j=1}^N\frac{m_j}{\rho_j}A(\mathbf{x}_j)W(\mathbf{x}_i-\mathbf{x}_j, h)$$

$\qquad$一阶导的离散化需要利用<font color=#FF0000>差分公式（Difference Formula）</font>或<font color=#FF0000>对称公式（Symmetric Formula）</font>。差分拥有更高精度，对称公式保证动量守恒，建议对直接影响粒子轨迹的物理量进行离散化时使用对称公式。

$\qquad$二阶导离散化也有更好的形式，具体见论文。

###3. 状态方程

$$\rho\overset{\to}{a}=\rho\overset{\to}{g}-\nabla p+\mu{{\nabla}^2} \overset{\to}{u} $$

$\qquad$相关内容复习《计算流体力学基础及其应用》

###4. 时间积分

$\qquad$CFL条件：

$$\Delta t \le \lambda \frac{\tilde{h}}{||\mathbf{v}^{max}||}$$

###5. 基本流程

$\qquad$给定一个连续体$\mathbf{x}(t)$的当前几何形状与$t$时刻的速度场$\mathbf{v}(t)$，将问题分解成如下的子问题序列：

$\qquad$① 解 $\frac{D\mathbf{v}}{Dt}=\nu\nabla^2\mathbf{v}+\frac{1}{\rho}\mathbf{f}_{ext}$，更新$\mathbf{v}$;

$\qquad$② 通过 $\frac{D\rho}{Dt}=0$ 来确定 $\nabla p$;

$\qquad$③ 解 $\frac{D\mathbf{v}}{Dt}=-\frac{1}{\rho}\nabla p$，更新$\mathbf{v}$;

$\qquad$④ 解 $\frac{D\mathbf{x}}{Dt}=\mathbf{v}$，更新$\mathbf{x}$。