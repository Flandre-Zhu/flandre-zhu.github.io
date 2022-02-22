# 虚树 笔记

见到了一种比较简单的写法，不容易写错。这里简单讲一下。

## 啥是虚树

我们要考虑一个树上问题，但是只有一些我们在意的关键点。

我们可以把这些关键点提取出来考虑，但是这样丢掉了树结构。

如果想要保留树结构，一个方法是，把每个点到根的链都拿出来。

如下图:

![](https://flandre-zhu.github.io/zhu/algo/vtree-1.png)

在这个图中，如果咱要考虑 $4,11$ 这两个点，以及和路径有关的问题，其实只需要保留下图中红色的这些边即可。其它的点，可以证明，无论如何也不会用到。

![](https://flandre-zhu.github.io/zhu/algo/vtree-2.png)

但是红色的链太长了，有时候很多点只有一个儿子。我们把中间这些点缩掉，在这里，直接从 $1$ 点连到 $4,11$ 两个点即可。

这样建出来的树就叫做 **虚树**。

虚树并不严格，在保证复杂度的情况下，你多一两个点其实影响不大，因为我们只要把关心的信息都抽出来即可。不关心的信息，如果也抽出来了，不管就是了。

假设我们研究的点集合为 $S$，是整棵树点集的子集。我们关心的信息，即，任意两个点间路径的信息，想要保留下来，这些点是不可或缺的：
- $S$ 中的点
- $S$ 中的点两两组成的LCA，即，$\{LCA(u,v)|u\in S,v\in S\}$

注意到我们很多时候需要研究到根的路径问题，因此我们通常会把根也加入虚树。就算可能用不到这个信息，它也能帮助我们建虚树，一般也作为虚树的根。

## 如何维护

传统做法：单调栈维护。这个看 oi-wiki 即可。

新颖做法：首先把根和关键点按dfn排序，然后把排序后相邻两个点的LCA都加入到虚树中，然后去个重。容易证明，这些就是虚树中的点。

然后我们就得到了虚树的点集。如果要建出树，也是单调栈，不过简单很多，也很容易想：每次加入一个元素，弹栈直到当前栈顶是这个点的祖先（dfn判断），然后把栈顶和当前点连边，当前点再入栈。

## 如何做题

通常虚树只是作为一个工具，真正灵活的地方在于树的问题。

对于比较板子的虚树题，就是先考虑一整颗树怎么做（通常是树形dp，或者一遍dfs算一下），会了这个之后就没难度，单纯是打个板子了。

对于比较复杂的题，建虚树也可能作为转化问题，解决问题的一步。比如WC2018 通道。

## 例题

来点题捏。

### SDOI2011 消耗战

首先考虑整个树咋做，很显然一个dp。设 $mn(u)$ 表示 $u$ 到根的最短的边权（预处理），$f(u)$ 表示要切掉 $u$ 子树里面所有关键点的最小代价。

如果 $u$ 本身是关键点，直接 $f(u)=mn(u)$。否则 $f(u)=\min\{mn(u),\sum\limits_{v} f(v)\}$ ，$v$ 是 $u$ 的儿子。

然后写个虚树就过了。

可以把本题当做板子题，来参考一下虚树的写法

<details>

<summary> 点击展开代码 </summary>

```cpp
#include <bits/stdc++.h>
using namespace std;
#define F(i,l,r) for(int i=l;i<=r;++i)
#define D(i,r,l) for(int i=r;i>=l;--i)
#define T(G,i,u) for(int i=G.h[u];~i;i=G.nx[i])
#define MEM(a,x) memset(a,x,sizeof(a))
int I() {char c=getchar(); int x=0; int f=1; while(c<'0' or c>'9') f=(c=='-')?-1:1,c=getchar(); while(c>='0' and c<='9') x=(x<<1)+(x<<3)+(c^48),c=getchar(); return ((f==1)?x:-x);}
#define int long long
#define N   500005
#define INF 0x3f3f3f3f3f3f3f3fll

struct graph // 图
{
    #define M N<<1
    int to[M],nx[M],wei[M],ec; int h[N];
    void clear() {MEM(to,-1); MEM(nx,-1); MEM(h,-1); ec=-1;}
    void add(int u,int v,int w=1) {++ec; tie(to[ec],wei[ec],nx[ec])=tie(v,w,h[u]); h[u]=ec;}
    void ad2(int u,int v,int w=1) {add(u,v,w),add(v,u,w);}
}G;

int n,m;
int dep[N],fa[N],sz[N],sk[N];
int dfn[N],top[N],tick=0;
int mn[N];
namespace treecut // 树剖，得到dfn，并求LCA
{
    void d(int u,int f)
    {
        if (u==f) mn[u]=INF;
        dep[u]=dep[f]+1; fa[u]=f; sz[u]=1; sk[u]=-1;
        T(G,i,u) 
        {
            int v=G.to[i]; if (v==f) continue;
            mn[v]=min(mn[u],G.wei[i]);
            d(v,u);
            sz[u]+=sz[v];
            if (sk[u]==-1 or sz[v]>sz[sk[u]]) sk[u]=v;
        }
    }
    void d2(int u,int t)
    {
        dfn[u]=++tick; top[u]=t;
        if (~sk[u]) d2(sk[u],t); 
        T(G,i,u)
        {
            int v=G.to[i];
            if (v!=fa[u] and v!=sk[u]) d2(v,v);
        }
    }
    void init()
    {
        tick=0;
        d(1,1); d2(1,1);
    }
}
inline int insub(int f,int u) {return dfn[f]<=dfn[u] and dfn[u]<=dfn[f]+sz[f]-1;}
inline int LCA(int u,int v)
{
    while(top[u]!=top[v])
    {
        if (dep[top[u]]<dep[top[v]]) swap(u,v);
        u=fa[top[u]];
    }
    if (dep[u]>dep[v]) swap(u,v); return u;
}
int pt[N],pc;
namespace vtree
{
    int vp[N],vc=0;
    bool cmp_dfn(int a,int b) {return dfn[a]<dfn[b];}
    graph vG;
    int st[N],top=0;
    void build()
    {
        vp[1]=1; vc=1;
        sort(pt+1,pt+pc+1,cmp_dfn);
        F(i,1,pc) vp[++vc]=pt[i];
        F(i,1,pc-1) vp[++vc]=LCA(pt[i],pt[i+1]);
        sort(vp+1,vp+vc+1,cmp_dfn);
        vc=unique(vp+1,vp+vc+1)-vp-1;
        // 加入根，点集，相邻的LCA，然后去重

        vG.ec=-1;
        F(i,1,vc) vG.h[vp[i]]=-1; // 手动清图
        F(i,1,vc)
        {
            int x=vp[i];
            while(top and !insub(st[top],x)) --top;
            if (top) vG.add(st[top],x);
            st[++top]=x;
        }
    }
    bool mark[N]; // 是否是关键点
    int dp[N];
    void DFS(int u) // 和树上一样的dp
    {
        dp[u]=mn[u];
        if (mark[u]) return;
        int sum=0;
        T(vG,i,u)
        {
            int v=vG.to[i];
            DFS(v);
            sum+=dp[v];
        }
        dp[u]=min(dp[u],sum);
    }
    void sakuya()
    {
        top=0; vc=0;
        build();
        F(i,1,vc)
        {
            int x=vp[i];
            dp[x]=0; mark[x]=0; // 注意清空
        }
        F(i,1,pc) mark[pt[i]]=1;
        DFS(1);
        printf("%lld\n",dp[1]);
    }
}
void flandre()
{
    n=I(); G.clear();
    F(i,1,n-1)
    {
        int u=I(),v=I(),w=I();
        G.ad2(u,v,w);
    }
    treecut::init();
    m=I();
    F(i,1,m)
    {
        pc=I();
        F(j,1,pc)
        {
            pt[j]=I();
        }
        vtree::sakuya();
    }
}
#undef int
int main()
{
    int t=1;
    while(t-->0) {flandre();}
}
```
</details>