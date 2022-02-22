本篇所有内容可以在yhx的 [这篇博客](https://yhx-12243.github.io/OI-transit/records/cf600F.html) 中看到。

# 二分图边染色

对于一个二分图，我们给它的每一条边染一个颜色，使得任意相邻的两条边颜色不一样。

1. 最少多少个颜色 2. 给出构造方案

## 前置知识

正则二分图：每个点度数都一样的二分图

**引理1**：正则二分图一定有一个完美匹配

> 由Hall定理可以推得

**引理2**：正则二分图一定可以分解成若干完美匹配

> 匹配一次后，把匹配边删去，每个点度数减少 $1$，发现它还是个正则二分图，因此可以不断的做下去。

## 正片

问题1：设点度数为 $d_i$。那么我们只需要 $\max\ d_i$ 种颜色。

首先，如果我们能把手上的这个二分图分解成若干个匹配，那同一个匹配分配同一种颜色，匹配之间颜色不同。这样一定满足，相邻的边颜色不同。

可以证明，我们一定可以把这个图补成一个 $\max\ d_i$ 度数的正则二分图。然后跑一遍匹配分解，再染色即可。发现答案不可能再小，因此答案就是 $max d_i$。

问题2：如何构造？

可以补成正则二分图后，不断的跑匹配。

复杂度 $O(\max\ d_i \sqrt{n} m$。实际上每次跑完之后可以把边删了，实际跑出来速度还行。

这里有一个 $O(nm)$ 的算法。类似匈牙利，动态的维护染色，每次加边进来时调整一次。

设当前加入了 $u,v$。$u$ 最小的没用过的颜色是 $c_u$，$v$ 最小的没用过的颜色是 $c_v$。

如果 $c_u=c_v$，那显然，咱们就用这种颜色，不会有问题。

否则，咱们直接先用上 $c_u$ 这个颜色。如果发现 $v$ 那边用过了 $c_u$ 这个颜色，递归过去调整。把 $v$ 和用 $c_u$ 颜色的那个点，用的颜色换成 $c_v$。否则，如果 $v$ 那边没用过 $c_v$，啥事没有，直接返回。

<details>
<summary> 点击展开代码实现 </summary>
```cpp
#include <bits/stdc++.h>
using namespace std;
#define F(i,l,r) for(int i=l;i<=r;++i)
#define D(i,r,l) for(int i=r;i>=l;--i)
#define MEM(a,x) memset(a,x,sizeof(a))
int I() {char c=getchar(); int x=0; int f=1; while(c<'0' or c>'9') f=(c=='-')?-1:1,c=getchar(); while(c>='0' and c<='9') x=(x<<1)+(x<<3)+(c^48),c=getchar(); return ((f==1)?x:-x);}
#define N 2003
#define M 2000006

int a,b,m; int g[N][N];
int col[2][N][N];
int deg[N];
void DFS(int a,int b,int u,int v,int cu,int cv)
{
    int tmp=col[b][v][cu];
    col[a][u][cu]=v; col[b][v][cu]=u;
    if (cu==cv) return;
    if (tmp) DFS(b,a,v,tmp,cv,cu);
    else col[b][v][cv]=0;
}
int ans[M];
void flandre()
{
    a=I(); b=I(); m=I();
    F(i,1,m)
    {
        // printf("i=%d\n",i);
        int u=I(),v=I();
        g[u][v]=i;
        ++deg[u]; ++deg[v+a];
        int cu; for(cu=1;col[0][u][cu];++cu);
        int cv; for(cv=1;col[1][v][cv];++cv);
        DFS(0,1,u,v,cu,cv);
    }

    int mx=*max_element(deg+1,deg+a+b+1);
    printf("%d\n",mx);
    F(u,1,a)
    {
        F(i,1,mx) if (col[0][u][i])
        {
            ans[g[u][col[0][u][i]]]=i;
        }
    }
    F(i,1,m) printf("%d ",ans[i]); puts("");
}
int main()
{
    int t=1;
    while(t-->0) {flandre();}
}
```
</details>
