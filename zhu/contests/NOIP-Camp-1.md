# NOIP Camp #1 比赛记录(现场)

某机构举办的noip/csp冲刺营。

## 题意&题解

### A

定义函数 $f(a_1,a_2...a_n)$ 为：
$$
\begin{cases}
a_1 & (n=1)\\
f(a_1\oplus a_2,a_2\oplus a_3...a_{n-1}\oplus a_n) & (n>1)
\end{cases}
$$
其中 $\oplus$ 表示二进制按位异或运算。

（注意仔细看题，并不是 $a_1...a_n$ 的异或和！）

多组询问。每次给定 $L,R$，求 $\max \{f(a_l,a_{l+1}...a_{r}),\ L\le l\le r\le R\}$

$n\le 1000,m\le 10^6$。

题解：注意到 $n\le 1000$，考虑处理每对 $F(l,r)$ 表示 $[l,r]$ 区间的答案。

设 $f(l,r)$ 表示 $f(a_l...a_r)$。注意到它等于 $f(l,r-1)\oplus f(l+1,r)$。

然后两维求个前缀max就可以得到 $F$。

复杂度是 $O(n^2) - O(1)$ 的。

### B

给定 $n,m$，求有多少 $x$ 满足：

- $x\in [0,n]$
- $\forall k, x\mod 10^k\ge m\mod 10^k$

$n,m\le 10^{50}$

题解：很明显它说的就是每一位都 $\ge m$ 对应的那一位。

然后类似数位dp，处理一下卡界就行了。

### C

有 $n$ 盏灯排成一列，初始所有灯均为熄灭状态。

你需要对这 $n$ 盏灯进行 $n$ 次操作。

对于第 $i$ 次操作，选取 $i$ 的所有质数倍标号的灯，将它们的状态取反（点亮变为熄灭，熄灭变为点亮）。

你需要回答最终有多少盏灯为开启状态。 

90pts: $n\le 5e7$

时限2s。

题解：这题我只会90pts

首先线性筛出所有质数，然后模拟它的过程，直接就是 $n\log\log n$ 的，可以获得 $1e6$ 的部分分。然而，过 $1e7$ 需要少量的乱搞。尽管是乱搞，它对思路的启发也很有意义。

分析一波, 它其实求的是 $[1,n]$ 种有多少个数有奇数 **种** 质因数。

对于 $5e7$ 的范围，线性筛写好看点是能很快跑出来的。只是 $\log\log n$ 有点卡而已，甚至把它常数卡一卡也就过了。

考虑 $> \sqrt{n}$ 的那些质因数，只会出现一种, 且次数为 $1$。可以从筛好的质数种枚举，设它为 $p$。那剩下的部分就 $\le n/p$，要求由偶数种质因数构成 （注意到我们先选了一个，所以剩下那些就是偶数个）。这个可以预处理出来，设 $g(n,0/1)$ 表示由偶数/奇数个质因数构成的方案数，可以用原题筛出来的那个东西求前缀和轻松得到。

然后就解决了包含一个 $> sqrt{n}$ 的质因数的情况，复杂度 $O(n)$。接下来考虑那些 $\le sqrt{n}$ 的质因数。

我们发现它们很少，由这些小质因子构成的数，也在 $0.75n$ 左右。于是我们考虑用 DFS，就可以精确枚举到所有剩下的数，略小于 $n$。这个DFS写出来可以发现，它显然是O(答案)的，于是它是 $O(n)$ 的，且常数小。

然后就可以卡过 $5e7$ 了。

据说剩下10pts是留给出题人切的，$n\le 10^{13}$。目测需要min-25，或者是powerful number等神秘筛法来做。

### D

根本不会。再见~

## 总结

A题看错题意，分数-100

B题没看清范围，1e50用的long long，分数-50

C,D算是正常发挥了。

暴露的一个问题是，做题过于求快，而不稳，导致题意读错了。也许是受 CF/AT 比赛的影响，也有我个人的原因。

对于NOIP/CSP赛制的比赛，考场策略与节奏还需要不断摸索和练习。