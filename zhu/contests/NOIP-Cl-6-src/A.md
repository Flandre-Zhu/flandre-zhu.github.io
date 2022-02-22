```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long
#define F(i,l,r) for(int i=l;i<=r;++i)
#define D(i,r,l) for(int i=r;i>=l;--i)
#define MEM(a,x) memset(a,x,sizeof(a))
int I() {char c=getchar(); int x=0; int f=1; while(c<'0' or c>'9') f=(c=='-')?-1:1,c=getchar(); while(c>='0' and c<='9') x=(x<<1)+(x<<3)+(c^48),c=getchar(); return ((f==1)?x:-x);}

int L,v1,v2,T;
int gcd(int a,int b) {while(b)swap(a,b),b%=a; return a;}
int lcm(int a,int b) {return a/gcd(a,b)*b;}
void flandre()
{
    L=I(); v1=I(); v2=I(); T=I();
    if (v1>v2) swap(v1,v2);

    int ans=0;
    int cnt1=(T*(v1+v2)+L)/(2*L); // 第一种情况，这个就是 (M+1)/2
    ans+=cnt1;
    if (v1!=0 and v2!=v1)
    {
        int cnt2=(T*(v2-v1)+L)/(2*L); // 第二种情况，同理
        ans+=cnt2;

        int g=gcd(v1+v2,v2-v1);
        int A=(v1+v2)/g,B=(v2-v1)/g;

        if ((lcm(A,L)/L)&1 and (lcm(B,L)/L)&1)
        {
            int X=lcm(A,L)/A,Y=lcm(B,L)/B;
            int Z=lcm(X,Y);
            if ((Z/X)&1 and (Z/Y)&1)
            {
                // p=奇数*Z, p<=T*g
                int lim=T*g;
                ans-=(lim/Z+1)/2;
            }
        }
    }
    printf("%lld\n",ans);
}

#undef int
int main()
{
    int tt=I();
    while(tt-->0)
    {
        flandre();
    }
}
```