```cpp
// 参考了 Yuc 同学的实现，见这里: http://noi.ac/submission/141332
#include <bits/stdc++.h>
using namespace std;
namespace Flandre_Scarlet
{
    #define N   400005
    #define M   20000007
    #define ull unsigned long long
    #define F(i,l,r) for(int i=l;i<=r;++i)
    #define D(i,r,l) for(int i=r;i>=l;--i)
    #define Tra(i,u) for(int i=G.h[u],v=G.to(i);~i;i=G.nx(i),v=G.to(i)) if (i>=0)
    #define MEM(a,x) memset(a,x,sizeof(a))
    #define FK(a) MEM(a,0)
    #define sz(x) ((int)x.size())
    #define all(x) x.begin(),x.end()
    #define p_b push_back
    #define pii pair<int,int>
    #define fir first
    #define sec second
    char buf[100000],*p1=buf,*p2=buf,ch;
    int result;
    #define getc() p1==p2&&(p2=(p1=buf)+fread(buf,1,100000,stdin),p1==p2)?EOF:*p1++;
    inline int I()
    {
        char c=getc(); int x=0;
        while(c<'0' or c>'9') c=getc(); 
        while(c>='0' and c<='9') x=(x<<1)+(x<<3)+(c^48),c=getc(); 
        return x;
    }

    int n,m;
    int d[N]; int uu[M],vv[M];
    void Input()
    {
        n=I(),m=I();
        F(i,1,m)
        {
            int u=I(),v=I();
            uu[i]=u,vv[i]=v;
            ++d[u],++d[v];
        }
    }

    int ver[N],tot=0; int id[N];
    bool g[6500][6500]; int tmp[6500][6500];
    void set2(int u,int v,int x) {g[u][v]=g[v][u]=x;}
    bool vis[6500];
    void DFS(int u)
    {
        vis[u]=1;
        F(i,1,tot+1) if (!g[u][i] and !vis[i]) DFS(i);
    }
    vector<int>ans;
    void Sakuya()
    {
        int st=min_element(d+1,d+n+1)-d;
        FK(id);
        F(i,1,m) if (uu[i]==st or vv[i]==st)
        {
            int v=(uu[i]^vv[i]^st);
            ++tot; ver[tot]=v; id[v]=tot;
        }

        FK(g);
        F(i,1,m)
        {
            int u=id[uu[i]],v=id[vv[i]];
            if (!u) u=tot+1; if (!v) v=tot+1;
            ++tmp[u][v]; ++tmp[v][u];
        }
        F(i,1,tot+1) F(j,i+1,tot+1)
        {
            if (i<=tot and j<=tot) set2(i,j,(tmp[i][j]>0));
            else set2(i,j,(tmp[i][j]==n-tot));
        }
        DFS(tot+1);

        int cnt=0;
        F(i,1,tot) if (!vis[i]) ans.p_b(i);
        if (sz(ans))
        {
            printf("%d\n",sz(ans));
            for(auto x:ans) printf("%d ",ver[x]); puts("");
        }
        else
        {
            puts("-1");
        }
    }
    void IsMyWife()
    {
        int t=1;
        while(t-->0){Input(); Sakuya();}
    }
}
#undef int //long long
int main()
{
    Flandre_Scarlet::IsMyWife();
    return 0;
}
```

