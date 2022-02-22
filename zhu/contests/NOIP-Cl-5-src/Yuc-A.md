```cpp
#include<bits/stdc++.h>
namespace io{
const int D=1<<21;
char buf[D],*s,*t;
inline char get(){return s==t&&(t=(s=buf)+fread(buf,1,D,stdin),s==t)?-1:*s++;}
inline int read(){
	int a=0;char c=get();
	for(;c< '0'||c> '9';c=get());
	for(;c>='0'&&c<='9';a=a*10+c-'0',c=get());
	return a;
}
}
using io::read;
const int N=1e6+3,M=2e7+3,D=6330;
int n,m,x[M],y[M],deg[N],w,p[N],f[N],q[N],r[D],s[D],t,e[D][D],vis[D],ans[N],ansk;
void Dfs(int u){
	ans[++ansk]=r[u];
	if(r[u]==w)for(int v=1;v<=n;v++)if(!f[v])ans[++ansk]=v;
	for(int v=1;v<=t;v++)if(u!=v&&e[u][v]<s[u]*s[v]&&!vis[v])vis[v]=1,Dfs(v);
}
int main(){
	int u,v;
	n=read(),m=read();
	for(int i=1;i<=m;i++){
		u=read(),v=read();
		x[i]=u,y[i]=v;
		++deg[u],++deg[v];
	}
	for(u=1;u<=n;u++){
		p[u]=u;
		w=!w||deg[u]<deg[w]?u:w;
	}
	f[w]=1;
	for(int i=1;i<=m;i++){
		if(x[i]==w)f[y[i]]=1;
		if(y[i]==w)f[x[i]]=1;
	}
	for(u=1;u<=n;u++)
		if(!f[u])p[u]=w;
		else ++t,q[u]=t,r[t]=u;
	for(int i=1;i<=t;i++)++s[i];
	for(u=1;u<=n;u++)if(!f[u])++s[q[w]];
	for(int i=1;i<=m;i++){
		u=x[i],v=y[i];
		++e[q[p[u]]][q[p[v]]],++e[q[p[v]]][q[p[u]]];
	}
	vis[1]=1,Dfs(1);
	if(ansk==n)return 0*puts("-1");
	printf("%d\n",ansk);
	for(int i=1;i<=ansk;i++)printf("%d ",ans[i]);puts("");
	return 0;
} 
```