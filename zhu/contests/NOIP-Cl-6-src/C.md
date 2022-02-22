```cpp
// 参考了xuanyi的写法！
// sto xuanyi orz
#include <bits/stdc++.h>
using namespace std;
#define N   400005
#define int long long
#define F(i,l,r) for(int i=l;i<=r;++i)
#define D(i,r,l) for(int i=r;i>=l;--i)
#define MEM(a,x) memset(a,x,sizeof(a))
#define FK(x)    MEM(x,0)
#define p_b push_back
int I() {char c=getchar(); int x=0; int f=1; while(c<'0' or c>'9') f=(c=='-')?-1:1,c=getchar(); while(c>='0' and c<='9') x=(x<<1)+(x<<3)+(c^48),c=getchar(); return ((f==1)?x:-x);}

int n,ATK;
int a[N],lf[N],tp[N];
struct segtree // 线段树，区间加，区间求活人和，单点死人
{
int s[N<<2],sz[N<<2]; int ad[N<<2];
void clear() {FK(s); FK(sz); FK(ad);}
#define ls ix<<1
#define rs ix<<1|1
#define lson ls,L,mid
#define rson rs,mid+1,R
void up(int ix)
{
    s[ix]=s[ls]+s[rs];
    sz[ix]=sz[ls]+sz[rs];
}
void build(int ix=1,int L=1,int R=n)
{
    ad[ix]=0;
    if (L==R)
    {
        sz[ix]=1;
        s[ix]=a[L];
        return;
    }
    int mid=(L+R)>>1;
    build(ls,L,mid); build(rs,mid+1,R); 
    up(ix);
    assert(sz[ix]==R-L+1);
}
void addone(int x,int ix)
{
    s[ix]+=x*sz[ix];
    ad[ix]+=x;
}
void down(int ix)
{
    if (ad[ix])
    {
        addone(ad[ix],ls);
        addone(ad[ix],rs);
        ad[ix]=0;
    }
}
void add(int l,int r,int x,int ix=1,int L=1,int R=n)
{
    if (l>R or L>r or l>r) return;
    if (l<=L and R<=r) return addone(x,ix);
    int mid=(L+R)>>1;
    down(ix);
    add(l,r,x,lson),add(l,r,x,rson);
    up(ix);
}
void set_dead(int pos,int ix=1,int L=1,int R=n)
{
    if (L==R) {sz[ix]=0; s[ix]=0; return;}
    int mid=(L+R)>>1;
    down(ix);
    if (pos<=mid) set_dead(pos,lson);
    else          set_dead(pos,rson);
    up(ix);
}
int  qsum(int l,int r,int ix=1,int L=1,int R=n)
{
    if (l<=L and R<=r) return s[ix];
    int mid=(L+R)>>1;
    down(ix);
    if (r<=mid) return qsum(l,r,lson);
    if (mid<l)  return qsum(l,r,rson);
    return qsum(l,r,lson)+qsum(l,r,rson);
}
}T;
int ans=0;
namespace moni // 暴力模拟
{
    int buff=0,add=0,adds[N];
    // buff: 不受翻倍影响的增益
    // add: 受翻倍影响的增益
    // adds: 已经处理过的add，为了不重复贡献，我们会把它减去
    void clear() {FK(adds); add=buff=0;}
    int moni()
    {
        auto dead=[&](int i)
        {
            if ((tp[i]&2)==2) buff-=2;
            if ((tp[i]&4)==4) add+=2;
        };
        int tmp=0;
        F(i,1,n) if (lf[i]>0)
        {
            a[i]=a[i]+add-adds[i]; adds[i]=add;
            if ((tp[i]&1)==1)
            {
                lf[i]>>=1; a[i]<<=1;
                if (lf[i]<=0) {dead(i); continue;}
            }
            tmp+=(a[i]+buff); lf[i]-=ATK;
            if (lf[i]<=0) dead(i);
        }
        ans+=tmp; return tmp;
    }
    void sakuya()
    {
        F(i,1,n) if ((tp[i]&2)==2) buff+=2;
        F(_,1,20) moni();
        F(i,1,n) a[i]=a[i]+add-adds[i],adds[i]=add; // 做完之后加上
    }
    void change()
    {
        F(i,1,n) if (lf[i]>0) a[i]+=buff; // 由于不受翻倍影响，buff和add等价了，也加上去
    }
}
#define pii pair<int,int>
vector<pii> vec;
void clear()
{
    vec.clear(); ans=0;
    T.clear(); moni::clear();
    FK(a); FK(lf); FK(tp);
}
void flandre()
{
    clear();
    n=I(); ATK=I();
    F(i,1,n) a[i]=I(),lf[i]=I(),tp[i]=I();

    if (ATK==0) // 特殊处理
    {
        bool no_die=0,add=0;
        F(i,1,n)
        {
            if (tp[i]&1) add|=((tp[i]&4)==4);
            else
            {
                add|=((tp[i]&2)==2); no_die=1;
                if (a[i]>0)
                {
                    puts("defeat");
                    return;
                }
            }
        }
        if (add and no_die)
        {
            puts("defeat");
            return;
        }
    }

    moni::sakuya();

    {
        int nn=0;
        F(i,1,n) if (lf[i]>0)
        {
            ++nn;
            a[nn]=a[i];
            lf[nn]=lf[i]; tp[nn]=tp[i];
        }
        F(i,nn+1,n) a[i]=lf[i]=tp[i]=0;
        n=nn;
        if (!n or !ATK)
        {
            printf("%lld\n",ans);
            return;
        }
    } // 删去死人，并讨论ATK==0的另一种情况，就是人活着，但是不再打出更多伤害
    // “有的人活着，但它已经死了”
    moni::change();
    F(i,1,n) lf[i]=(lf[i]+ATK-1)/ATK; ATK=1;
    F(i,1,n) vec.p_b(pii(lf[i],i));
    sort(vec.begin(),vec.end());
    T.build();
    int las_id=1,las_tm=1;
    for(auto it:vec)
    {
        int tm,id; tie(tm,id)=it;
        if (id<las_id) // 转一圈回来
        {
            ans+=T.qsum(las_id,n); 
            ++las_tm; las_id=1;
        }
        if (las_id<=id) // 直接过来
        {
            ans+=T.qsum(las_id,id);
            las_id=id;
        }
        ans+=T.qsum(1,n)*(tm-las_tm); las_tm=tm;
        T.set_dead(id);
        int d=0;
        if ((tp[id]&2)==2) d-=2;
        if ((tp[id]&4)==4) d+=2;
        T.add(1,n,d);
    }
    printf("%lld\n",ans);
}
#undef int
int main()
{
    int _=I();
    F(i,1,_) flandre();
    return 0;
}
```