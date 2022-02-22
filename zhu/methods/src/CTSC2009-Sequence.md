```cpp
#include <bits/stdc++.h>
using namespace std;
#define N 1000006
#define int long long
#define F(i,l,r) for(int i=l;i<=r;++i)
#define D(i,r,l) for(int i=r;i>=l;--i)
#define MEM(a,x) memset(a,x,sizeof(a))
int I() {char c=getchar(); int x=0; int f=1; while(c<'0' or c>'9') f=(c=='-')?-1:1,c=getchar(); while(c>='0' and c<='9') x=(x<<1)+(x<<3)+(c^48),c=getchar(); return ((f==1)?x:-x);}

int n,V,l,r;
int a[N];
priority_queue<int> L;
priority_queue<int,vector<int>,greater<int> > R;
int b[N];
void flandre()
{
    n=I(); V=I(); l=I(),r=I();
    F(i,1,n) a[i]=I();
    int base=0;
    F(i,1,n) if (a[i]<(i-1)*l+1)
    {
        base+=(i-1)*l+1-a[i]; // 先加一波，这肯定要有
        a[i]=(i-1)*l+1;
    }
    F(i,1,n) a[i]-=(i-1)*l+1;

    int K=r-l;
    // 然后问题变成 0<=b[i+1]-b[i]<=K
    // i时刻插入R的元素x, 插一个 x-i*K; i'时刻取，取x+(i')*K; 这样就实现了每一轮+K
    F(i,1,n)
    {
        L.push(a[i]);
        R.push(a[i]-i*K);
        while(1)
        {
            int x=L.top(),y=R.top(); y+=i*K;
            if (x<=y) break;
            L.pop(); R.pop(); R.push(x-i*K); L.push(y);
            // 维护对顶平衡
        }
        b[i]=L.top();
        // 取平台上的点，是任取的
        // 你可以试一试，如果改成 R.top()+i*K，照样能过
    }
    b[n]=min(b[n],V-(n-1)*l-1); // 注意V的限制
    D(i,n-1,1) // 反推一遍回去
    {
        if (b[i]>b[i+1]) b[i]=b[i+1];
        if (b[i]<b[i+1]-K) b[i]=b[i+1]-K;
    }
    int ans=0;
    F(i,1,n) ans+=abs(a[i]-b[i]);
    printf("%lld\n",base+ans);
}
#undef int
int main()
{
    flandre();
    return 0;
}
```