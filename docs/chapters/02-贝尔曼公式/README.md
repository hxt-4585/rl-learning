# 第 2 课：贝尔曼公式

## 2.1 Motivation Examples

### 2.1.1 return 为什么重要?

**return 能够告诉我们哪个策略好哪个策略坏**

请看下面一个例子，从起始点 $s_1$，哪个策略更好？

![image-20260917164120268](F:\rl-learning\docs\chapters\02-贝尔曼公式\assets\001.png)

直观上来看：

- 左边策略：从状态 $s_1$ 出发并不会进入禁区 $s_2$，轨迹为 $s_1 → s_3 → s_4 → s_4...$ ，回报最大，策略最好
- 中间策略：从状态 $s_1$ 出发一定会进入禁区 $s_2$，轨迹为 $s_1 → s_2 → s_4 → s_4...$ ，回报最小，策略最差
- 右边策略：从状态 $s_1$ 出发可能会进入禁区 $s_2$，存在两条轨迹，分别为 $s_1 → s_2 → s_4 → s_4...$ 和 $s_1 → s_3 → s_4 → s_4...$ ，回报一般



### 2.1.2 如何计算 return？

![image-20260917183410306](F:\rl-learning\docs\chapters\02-贝尔曼公式\assets\002.png)

我们通常使用 $v_i$ 来表示从 $s_i$ 出发所得到的 return

- **定义法：return 定义为沿轨迹收集的所有奖励的折扣总和。**

$$
v_1=r_1+\gamma r_2+\gamma^2 r_3+... \\
v_2=r_2+\gamma r_3+\gamma^2 r_4+... \\
v_3=r_3+\gamma r_4+\gamma^2 r_1+... \\
v_4=r_4+\gamma r_1+\gamma^2 r_2+... \\
$$

- **自举法（bootstrapping）：我们可以将定义法中的等式进行变换**

$$
v_1=r_1+\gamma( r_2+\gamma r_3+...)=r_1 + \gamma v_2 \\
v_2=r_2+\gamma( r_3+\gamma r_4+...)=r_2 + \gamma v_3 \\
v_3=r_3+\gamma( r_4+\gamma r_1+...)=r_3 + \gamma v_4 \\
v_4=r_4+\gamma( r_1+\gamma r_2+...)=r_4 + \gamma v_1 \\
$$

把它写成矩阵的形式
$$
\underbrace{
\begin{bmatrix}
v_1\\
v_2\\
v_3\\
v_4
\end{bmatrix}
}_{\mathbf v}
=
\underbrace{
\begin{bmatrix}
r_1\\
r_2\\
r_3\\
r_4
\end{bmatrix}
}_{\mathbf r}
+
\gamma
\underbrace{
\begin{bmatrix}
0&1&0&0\\
0&0&1&0\\
0&0&0&1\\
1&0&0&0
\end{bmatrix}
}_{\mathbf P}
\underbrace{
\begin{bmatrix}
v_1\\
v_2\\
v_3\\
v_4
\end{bmatrix}
}_{\mathbf v}
$$
最终可以写为
$$
\mathbf{v}=\mathbf{r}+\gamma\mathbf{P}\mathbf{v}
$$
这就是**贝尔曼方程**，其中 $\mathbf{P}$ 是状态转移矩阵，它描述的是在给定策略后，从当前状态转移至下一个状态的概率。由贝尔曼方程移项并提取 $\mathbf{v}$，可以求解得到：
$$
\boxed{
\mathbf v=(\mathbf I-\gamma\mathbf P)^{-1}\mathbf r
}
$$


### 2.1.3 Exercise

考虑下面一个例子，写出 returns 之间的关系

![image-20260917191034829](F:\rl-learning\docs\chapters\02-贝尔曼公式\assets\003.md)

**Answer**
$$
v_1=0 + \gamma v_3 \\
v_2=1 + \gamma v_4 \\
v_3=1 + \gamma v_4 \\
v_4=1 + \gamma v_4 \\
$$


## 2.2 State Value

考虑一个单步过程：
$$
S_t \xrightarrow{A_t} R_{t+1},\, S_{t+1}
$$

- $t,\, t+1$：离散时间点
- $S_t$：时间点 $t$ 的状态
- $A_t$：在状态 $S_t$ 下采取的动作
- $R_{t+1}$：采取动作 $A_t$ 后获得的奖励
- $S_{t+1}$：采取动作 $A_t$ 后转移到的状态

> $S_t$、$A_t$、$R_{t+1}$ 都是随机变量



这一步的演化由以下概率分布决定：

- 状态 $S_t$ 到动作 
