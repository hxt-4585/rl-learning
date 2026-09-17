# 第 3 课：贝尔曼最优公式

## 3.1 Motivation examples

![状态转移示例](assets/001.png)

贝尔曼方程：

$$
\begin{aligned}
v_\pi(s_1) &= -1+\gamma v_\pi(s_2), \\
v_\pi(s_2) &= 1+\gamma v_\pi(s_4), \\
v_\pi(s_3) &= 1+\gamma v_\pi(s_4), \\
v_\pi(s_4) &= 1+\gamma v_\pi(s_4).
\end{aligned}
$$

状态价值：令 $\gamma=0.9$，则可以计算得到：

$$
v_\pi(s_4)
{}=
v_\pi(s_3)
{}=
v_\pi(s_2)
{}=
10,
\qquad
v_\pi(s_1)=8.
$$
动作价值：考虑状态 $s_1$
$$
\begin{aligned}
q_\pi(s_1,a_1) &= -1+\gamma v_\pi(s_1)=6.2, \\
q_\pi(s_1,a_2) &= -1+\gamma v_\pi(s_2)=8, \\
q_\pi(s_1,a_3) &= 0+\gamma v_\pi(s_3)=9, \\
q_\pi(s_1,a_4) &= -1+\gamma v_\pi(s_1)=6.2, \\
q_\pi(s_1,a_5) &= 0+\gamma v_\pi(s_1)=7.2.
\end{aligned}
$$


<span style="color:red">问题：当前策略并不理想，如何改进它？</span>

<span style="color:blue">答案：利用动作价值。</span>

当前策略 $\pi(a\mid s_1)$ 为：

$$
\pi(a\mid s_1)
{}=
\begin{cases}
1, & a=a_2, \\
0, & a\ne a_2.
\end{cases}
$$
观察刚刚计算得到的动作价值：

$$
\begin{aligned}
q_\pi(s_1,a_1)&=6.2, &
q_\pi(s_1,a_2)&=8, &
q_\pi(s_1,a_3)&=9, \\
q_\pi(s_1,a_4)&=6.2, &
q_\pi(s_1,a_5)&=7.2.
\end{aligned}
$$

如果选择动作价值最大的动作，就可以得到一个新策略：

$$
\pi_{\text{new}}(a\mid s_1)
{}=
\begin{cases}
1, & a=a^*, \\
0, & a\ne a^*.
\end{cases}
$$

其中：

$$
a^*
{}=
\arg\max_a q_\pi(s_1,a)
{}=
a_3.
$$
**为什么选择动作价值最大的动作可以改进策略？**

直觉上，动作价值 $q_\pi(s,a)$ 衡量的是：

> 当前处于状态 $s$ 时，先执行动作 $a$，之后继续按照原策略 $\pi$ 行动，能够获得的期望累计回报。

因此，在同一个状态 $s$ 下，动作价值越大，说明“第一步选择该动作”带来的长期回报越高。

原策略的状态价值，本质上是各个动作价值按照策略概率的加权平均：

$$
v_\pi(s)
{}=
\sum_a\pi(a\mid s)q_\pi(s,a).
$$

而一组数的加权平均不可能大于其中的最大值，因此：

$$
v_\pi(s)
\le
\max_a q_\pi(s,a).
$$

所以，如果我们在每个状态都选择动作价值最大的动作：

$$
\pi_{\text{new}}(s)
{}=
\arg\max_a q_\pi(s,a),
$$

那么新策略在当前状态下的“一步决策”至少不会比原策略更差。

需要注意的是，$q_\pi(s,a)$ 的含义是“先执行 $a$，之后仍按照旧策略 $\pi$ 行动”的回报。因此，贪心地选择最大动作价值，保证新策略不会变差；当存在更优动作时，策略通常会得到改进。

更严格地，策略改进定理指出：

$$
v_{\pi_{\text{new}}}(s)
\ge
v_\pi(s),
\qquad
\forall s\in\mathcal{S}.
$$

这就是策略改进的核心思想：先评估当前策略，再根据动作价值选择更好的动作，然后重复这一过程。



## 3.2 Definition of optimal policy

状态价值可以用于判断一个策略是否更好。

如果对于所有状态 $s\in\mathcal{S}$，都有：

$$
v_{\pi_1}(s)\ge v_{\pi_2}(s),
$$

那么可以认为策略 $\pi_1$ 至少不比策略 $\pi_2$ 差；当某些状态上严格大于时，$\pi_1$ 可以认为优于 $\pi_2$。

> 定义
>
> 如果对于任意其他策略 $\pi$ 和任意状态 $s$，都有：
>
> $$
> v_{\pi^*}(s)\ge v_\pi(s),
> $$
>
> 则称策略 $\pi^*$ 为**最优策略**（optimal policy）。

这个定义会带来一些问题：

- 最优策略是否一定存在？
- 最优策略是否唯一？
- 最优策略是随机策略，还是确定性策略？
- 如何求得最优策略？

为了回答这些问题，接下来需要学习**贝尔曼最优方程**（Bellman optimality equation）。





## 3.3 BOE: Introduction

### 3.3.1 逐元素形式

$$
\begin{aligned}
v^*(s)
&=
\max_\pi
\sum_a\pi(a\mid s)
\left[
\sum_r p(r\mid s,a)\,r
{}+
\gamma\sum_{s'}p(s'\mid s,a)v^*(s')
\right] \\
&=
\max_\pi
\sum_a\pi(a\mid s)q^*(s,a),
\qquad
\forall s\in\mathcal{S}.
\end{aligned}
$$

我们需要先解决优化问题

备注：

- $p(r\mid s,a)$ 和 $p(s'\mid s,a)$ 是已知的环境模型。

- $v^*(s)$ 和 $v^*(s')$ 是未知量，需要计算。

- 在贝尔曼最优方程中，策略 $\pi$ 不再是预先给定的策略，而是需要通过 $\max_\pi$ 优化的对象。





### 3.3.2 矩阵向量形式

$$
\mathbf{v}
{}=
\max_\pi
\left(
\mathbf{r}_\pi
{}+
\gamma\mathbf{P}_\pi\mathbf{v}
\right).
$$

其中，与状态 $s$ 或下一状态 $s'$ 对应的向量、矩阵元素为：

$$
[\mathbf{r}_\pi]_s
\triangleq
\sum_a\pi(a\mid s)
\sum_r p(r\mid s,a)\,r.
$$

$$
[\mathbf{P}_\pi]_{s,s'}
{}=
p_\pi(s'\mid s)
\triangleq
\sum_a\pi(a\mid s)p(s'\mid s,a).
$$

这里的 $\max_\pi$ 是逐元素进行的：对于每一个状态 $s$，都选择能够使该状态价值最大的动作或策略。



贝尔曼最优方程（BOE）既精妙，又具有一定难度。

- 为什么说它精妙？

  它用一个紧凑的方程，同时描述了最优策略和最优状态价值。

- 为什么说它难？

  方程右侧包含最大化操作，因此不像普通线性方程那样可以直接求解。

- 仍有许多问题需要回答：

  - **算法问题**：如何求解这个方程？
  - **存在性问题**：这个方程是否存在解？
  - **唯一性问题**：这个方程的解是否唯一？
  - **最优性问题**：该方程的解与最优策略之间有什么关系？



## 3.4 BOE: Maximization on right-hand side

**贝尔曼最优方程：逐元素形式**
$$
v^*(s)
{}=
\max_\pi
\sum_a\pi(a\mid s)
\left(
\sum_r p(r\mid s,a)\,r
{}+
\gamma\sum_{s'}p(s'\mid s,a)v^*(s')
\right),
\qquad
\forall s\in\mathcal{S}.
$$

**贝尔曼最优方程：矩阵—向量形式**
$$
\mathbf{v}
{}=
\max_\pi
\left(
\mathbf{r}_\pi
{}+
\gamma\mathbf{P}_\pi\mathbf{v}
\right).
$$

> 示例：如何从一个方程中求解两个未知量
>
> 考虑两个实数变量 $x,a\in\mathbb{R}$。假设它们满足：
>
> $$
> x
> {}=
> \max_a
> \left(
> 2x-1-a^2
> \right).
> $$
>
> 该方程中包含两个未知量。为求解它，先观察右侧。
>
> 无论 $x$ 取何值，由于 $-a^2\le 0$，都有：
>
> $$
> \max_a(2x-1-a^2)
> {}=
> 2x-1.
> $$
>
> 当 $a=0$ 时，上述最大值取得。
>
> 将 $a=0$ 代回原方程：
>
> $$
> x=2x-1.
> $$
>
> 因此：
>
> $$
> x=1,
> \qquad
> a=0.
> $$
>
> 所以，该方程的解为 $x=1$ 和 $a=0$。

<span style="color:red">第一个例子说明：即使一个方程里同时有“未知价值”和“最大化操作”，也可以先求右侧的最大化，再代回去求价值。</span>

先固定下一状态价值 $v(s')$，再求使右侧最大的策略 $\pi$：
$$
\begin{aligned}
v(s)
&=
\max_\pi
\sum_a\pi(a\mid s)
\left(
\sum_r p(r\mid s,a)\,r
{}+
\gamma\sum_{s'}p(s'\mid s,a)v(s')
\right) \\
&=
\max_\pi
\sum_a\pi(a\mid s)q(s,a),
\qquad
\forall s\in\mathcal{S}.
\end{aligned}
$$

> 示例：如何求解 $\max_\pi\sum_a\pi(a\mid s)q(s,a)$
>
> 假设 $q_1,q_2,q_3\in\mathbb{R}$ 已知。求解：
>
> $$
> \max_{c_1,c_2,c_3}
> \left(
> c_1q_1+c_2q_2+c_3q_3
> \right),
> $$
>
> 其中：
>
> $$
> c_1+c_2+c_3=1,
> \qquad
> c_1,c_2,c_3\ge 0.
> $$
>
> 这里的 $c_1,c_2,c_3$ 可以理解为选择三个动作的概率。
>
> 不失一般性，假设：
>
> $$
> q_3\ge q_1,
> \qquad
> q_3\ge q_2.
> $$
>
> 那么最优解为：
>
> $$
> c_3^*=1,
> \qquad
> c_1^*=c_2^*=0.
> $$
>
> 原因是，对于任意满足概率约束的 $c_1,c_2,c_3$，都有：
>
> $$
> \begin{aligned}
> q_3
> &=
> (c_1+c_2+c_3)q_3 \\
> &=
> c_1q_3+c_2q_3+c_3q_3 \\
> &\ge
> c_1q_1+c_2q_2+c_3q_3.
> \end{aligned}
> $$
>
> 因此，为了使动作价值的加权平均最大，应将全部概率分配给动作价值最大的动作。

<span style="color:red">第二个例子说明：策略概率的加权平均想取得最大值，就应该把全部概率分给动作价值最大的动作。</span>

<span style="color:red">虽然在 $q(s,a)$ 已知时，可以直接选择动作价值最大的动作：</span>
$$
a^*
{}=
\arg\max_a q(s,a),
$$

<span style="color:red">但在实际问题中，动作价值通常并不事先已知。</span>

<span style="color:red">原因是，动作价值依赖于下一状态的最优状态价值：</span>
$$
q^*(s,a)
{}=
\sum_r p(r\mid s,a)\,r
{}+
\gamma\sum_{s'}p(s'\mid s,a)v^*(s').
$$

<span style="color:red">而下一状态价值 $v^*(s')$ 本身也是未知量。因此，状态价值和动作价值之间形成了相互依赖：</span>
$$
v^*(s)
\longrightarrow
q^*(s,a)
\longrightarrow
v^*(s').
$$

<span style="color:red">这正是贝尔曼最优方程的核心难点：我们想通过动作价值求最优状态价值，但动作价值又依赖于尚未求出的最优状态价值。</span>

<span style="color:red">解决方法不是一次性直接求出答案，而是从一个初始估计开始反复更新。</span>
$$
v_{k+1}(s)
{}=
\max_a
\left[
\sum_r p(r\mid s,a)\,r
{}+
\gamma\sum_{s'}p(s'\mid s,a)v_k(s')
\right].
$$

<span style="color:red">每一轮先使用旧估计 $v_k(s')$ 计算动作价值，再取最大值更新为 $v_{k+1}(s)$。不断迭代后，价值估计会逐渐收敛到最优状态价值 $v^*(s)$。</span>

具体求解方式会在下面几个小节详细讲解



## 3.5 BOE: Rewrite as $v=f(v)$

贝尔曼最优方程（BOE）为：

$$
\mathbf{v}
{}=
\max_\pi
\left(
\mathbf{r}_\pi
{}+
\gamma\mathbf{P}_\pi\mathbf{v}
\right).
$$

定义函数 $f$：

$$
f(\mathbf{v})
\triangleq
\max_\pi
\left(
\mathbf{r}_\pi
{}+
\gamma\mathbf{P}_\pi\mathbf{v}
\right).
$$

于是，贝尔曼最优方程可以改写为：

$$
\mathbf{v}
{}=
f(\mathbf{v}).
$$

其中，向量 $f(\mathbf{v})$ 在状态 $s$ 对应的元素为：

$$
[f(\mathbf{v})]_s
{}=
\max_\pi
\sum_a\pi(a\mid s)q(s,a),
\qquad
s\in\mathcal{S}.
$$

接下来，需要解决的问题是：如何求解方程 $\mathbf{v}=f(\mathbf{v})$？



## 3.6 Contraction mapping theorem

- **不动点（fixed point）**：若 $x\in X$ 满足

$$
f(x)=x,
$$

则称 $x$ 是函数 $f:X\to X$ 的一个不动点。

- **压缩映射（contraction mapping，也称压缩函数）**：若存在一个常数 $\gamma\in(0,1)$，使得对任意 $x_1,x_2\in X$ 都有

$$
\left\lVert f(x_1)-f(x_2)\right\rVert
\le
\gamma\left\lVert x_1-x_2\right\rVert,
$$

则称 $f$ 是一个压缩映射。

这里的 $\lVert\cdot\rVert$ 可以是任意一种向量范数。

> 直观理解：经过 $f$ 映射后，任意两点之间的距离至多变为原来的 $\gamma$ 倍。因为 $0<\gamma<1$，距离会不断缩小。

- $\gamma$ 必须严格小于 $1$。这样经过不断迭代后，误差项 $\gamma^k$ 才会在 $k\to\infty$ 时趋近于 $0$：

$$
\gamma^k\to0,\qquad k\to\infty.
$$
