---
title: 图论学习笔记
date: 2021-08-04 16:31:06
tags: Tulun
---

<!-- more -->

# 关竣佑图论学习笔记



[TOC]



## 三种存图方法：

### 1.邻接矩阵  2.邻接表  3.链式前向星

### 链式前向星：空间效率最高，没有浪费

```c++
const int num=1000005;
struct edge{
    int to,w,next;//表示该边的下一个结点，权重，该边的上一个结点为起点所连的上一条边
}edge[num];
int head[num];//记录每个结点所连的最后一条边
int cnt;
void init(){
    for(int i=0;i<num;i++)
    {
        edge[i].next=-1;
        head[i]=-1;
    }
    cnt=0;
}
void addedge(int u,int v,int w)
{
    edge[cnt].to=v;
    edge[cnt].w=w;
    edge[cnt].next=head[u];
    head[u]=cnt++;
}
for(int i=head[u];i;i=edge[i].next)
{
    //遍历点i的所有邻居
}
```

**遍历所有边时，先找到该结点为起点的最后一条边，再往前找，直至遍历完该节点的所有边**

## 拓扑排序：

### ***\*类型1：确定字典序最小的拓扑排序：\**** （邻接矩阵存图）

```c++
#include<bits/stdc++.h>//图论拓扑排序+优先队列hdu1285 
using namespace std;
//hdu1285 DFS拓扑排序加优先队列
bool mp[517][517];
int in[517];//记录入度 
priority_queue<int,vector<int>,greater<int> > q;//每次弹出最小值的优先队列 ,得到队伍编号最小的排名 
void tuopu(int n)
{
	for(int i=1;i<=n;i++)
	{
		if(in[i]==0)
			q.push(i);//把入度为0的压入队列 
	}
	int c=1;
	while(!q.empty())
	{
		int v=q.top();
		q.pop();
		if(c!=n)//没到尾 
		{
			cout<<v<<" ";
			c++;
		}
		else
			cout<<v<<endl;
		for(int i=1;i<=n;i++)
		{
			if(!mp[v][i])
				continue;
			in[i]--;//入度减一 
			if(!in[i])
				q.push(i);//把入度为0的压入队列 
		}
	}
}
int main()
{
	int n,m,i,j;
	while(cin>>n>>m)
	{
		int k=0;
		memset(mp,0,sizeof(mp));
		memset(in,0,sizeof(in));
		while(m--)
		{
			cin>>i>>j;
			if(mp[i][j])
			continue;
			mp[i][j]=1;
			in[j]++;	
		}	
		tuopu(n);
	}	
}
```

### ***\*类型2：按字典序从小到大输出所有拓扑排序（重要）\****

```c++
#include<iostream>//poj1270拓扑排序，邻接矩阵dfs实现，重要 
#include<algorithm>
#include<cstring>
using namespace std;

int mp[30][30],in[30],tp[30],n;
char s[30];
int getit(int ch)
{
	for(int i=1;i<n;i++)
		if(s[i]==ch) return i;
}
bool init()//输入 
{
	char ch;
	n=0;
	while((ch=getchar())!='\n')
	{
		if(ch==EOF) return false;
		if(ch!=' ') s[++n]=ch;
	}
	s[++n]='\0';
	sort(s+1,s+n);
	memset(in,0,sizeof(in));
	memset(mp,0,sizeof(mp));
	while(true)//输入约束对 
	{
		while((ch=getchar())==' ');
		if(ch==EOF) return false;
		if(ch=='\n') break;
		int u=getit(ch);
		while((ch=getchar())==' ');
		int v=getit(ch);
		mp[u][v]=1;//邻接矩阵储存 
		in[v]++;//入度增加	
	} 
	return true;
}
void dfs(int now)
{
	if(now==n) //到结尾 
	{
		for(int i=1;i<n;i++)
		{
			cout<<s[tp[i]];//输出拓扑序 
		}
		cout<<endl;
		return;
	}
	for(int i=1;i<n;i++)
	{
		if(in[i]==0)
		{
			in[i]=-1;
			tp[now]=1;//入度为0的在第一位 
			for(int j=1;j<n;j++)
				if(mp[i][j]) in[j]--;
				//把该结点的子节点入度递减 
			dfs(now+1);
			in[i]=0;//恢复现场 
			for(int j=1;j<n;j++)
				if(mp[i][j]) in[j]++;
		}
	}
}
void work()
{
	while(init())
	{
		dfs(1);
		cout<<endl;
	}
}
int main()
{
	work();
	return 0;
} 
```

### ***\*类型3：拓扑序找环，判断是否有环\****

```c++
#include<bits/stdc++.h>//hdu3342拓扑序找环 
using namespace std;
int n;
bool m[105][105];//邻接矩阵储存
int master[105];//指向的徒弟的师傅数量（入度）
int main()
{
	int n,M,u,v;
	while(scanf("%d%d",&n,&M)!=EOF and n)	
	{
		memset(master,0,sizeof(master));
		memset(m,false,sizeof(m));
		while(M--)
		{
			scanf("%d%d",&u,&v);
			if(!m[u][v])
			{
				m[u][v]=true;
				++master[v];
			}
		}
		bool flag;
		int num=0;//人数
		for(;;)
		{
			flag=true;
			for(int u=0;u<n;u++)//找出master数量为0的人 
			{
				if(master[u]==0){
					num++;
					master[u]=-1;
					flag=false;
					for(int v=0;v<n;v++)
					{
						if(m[u][v]) master[v]--;//入度递减			 
					}
				}
			}
			if(flag) break;	
		} 
		if(num==n) cout<<"YES"<<endl;
		else cout<<"NO"<<endl;
	}
	return 0;
} 
```

### ***\*变式：判断有无环+反向建图\****

```c++
#include<bits/stdc++.h>//拓扑排序+反向建图hdu2647
using namespace std;
const int M=10005;
vector<int> g[M]; //邻接表储存 
int num[M];
int n,ans;
int out[M];//出度
int toposort()
{
	queue<int> q;
	int cnt=0;
	ans=0;
	for(int i=1;i<=n;i++)
	{
		if(out[i]==0)
			q.push(i);
	}
	while(!q.empty())
	{
		cnt++;
		int u=q.front();
		ans+=num[u];
		q.pop();
		for(int i=0;i<g[u].size();i++)
		{
			int v=g[u][i];
			if(--out[v]==0)//如果出度为0则放入队列且 钱数增加1 
			{
				q.push(v);
				num[v]=num[u]+1;	
			}	
		} 
	}
	if(cnt!=n)//判断有无环，-1表示有环，1表示无环 
		ans-=1;
	return ans;
}
int main()
{
	int m;
	while(cin>>n>>m)
	{
		for(int i=0;i<=n;i++)
		{
			g[i].clear();
			out[i]=0;
			num[i]=888;	
		}	
		while(m--)
		{
			int u,v;
			cin>>u>>v;
			g[v].push_back(u);
			out[u]++;
		}
		cout<<toposort()<<endl;
	}	
	return 0;
} 

```

------



## 欧拉路：

| 存在欧拉路的充要条件 | ***\*存在欧拉回路\****       | ***\*存在欧拉路\****                                         |
| -------------------- | ---------------------------- | ------------------------------------------------------------ |
| ***\*无向图\****     | ***\*每个结点都是偶点\****   | ***\*只有2个奇点或每个结点都是偶点\****                      |
| ***\*有向图\****     | ***\*每个点出度等于入度\**** | ***\*只有两个点入度不等于出度且其中一个点入度-出度=1，另一个点出度-入度=1或每个点出度等于入度\**** |

### 类型1：无向图求欧拉回路  (如果是有向图输出的是一个逆序的路径，可以用栈把逆序按正序打印出来）

```c++
#include<bits/stdc++.h>//uva10054 无向图求欧拉回路  
using namespace std;
int n;
int g[55][55];//邻接矩阵储存 
int d[55];
bool check()
{
	for(int i=1;i<=n;i++)
	{
		if(d[i]%2==1) return 0;
	}
	return 1;
}
void dfs(int u)//深搜打印欧拉回路模板 
{
	int v;
	for(int v=1;v<=50;v++)//深搜u的所有邻居 
	{
		if(g[u][v])
		{
			g[u][v]--;g[v][u]--;//可能有重边 
			dfs(v);
			cout<<v<<" "<<u<<end;//思考为什么在后面打印？ 
		}
	}
}
int main()
{
	cin>>n;
	memset(g,0,sizeof(g));
	int u,v;
	for(int i=1;i<=n;i++)
	{

		cin>>u>>v;
		g[u][v]++;
		g[v][u]++;
		d[v]++;d[u]++;//度数 
	}
	if(!check())
		cout<<"some beads may be lost"<<endl;
	else
		dfs(u);	
	return 0;
} 
```

------

## 判断负环：

### 1.SPFA—BFS（会超时）  2.SPFA—DFS（高效）

### SPFA—DFS　（基于链式前向星）：

```c++
//dfs-spfa判断负环
int t;
int inf=0x7f7f7f;
int n,m,k,u,v,w;
int dis[2600];
bool vis[2600];
int head[2600];
bool flag;
int cnt;
struct edge
{
	int to,w,next;
}edge[2600];
void init()
{
	for(int i=0;i<=n;i++)
	{
		dis[i]=inf;
	}
	dis[1]=0;
	
	for(int i=0;i<=n;i++)
	{
		edge[i].next=-1;
		head[i]=-1;
	}
	cnt=0;
	memset(vis,false,sizeof(vis));
}
void addedge(int u,int v,int w)
{
	edge[cnt].to=v;
	edge[cnt].w=w;
	edge[cnt].next=head[u];
	head[u]=cnt++;
}
void spfa(int u)
{
	vis[u]=true;
	for(int i=head[u];i!=-1;i=edge[i].next)
	{
		int v=edge[i].to;
		if(dis[u]+edge[i].w<dis[v])
		{
			if(vis[v]){//存在一个点再一条路上出现多次 
				flag=true;
				return;
			}
			else
			{
				dis[v]=dis[u]+edge[i].w;
				spfa(v);
			}
		}
	}
	vis[u]=false; 
}
int main()
{
	cin>>t;
	while(t--)
	{
		flag=false;
		cin>>n>>m>>k;
		init();
		while(m--)
		{
			cin>>u>>v>>w;
			addedge(u,v,w);
			addedge(v,u,w);
		}
		while(k--)
		{
			cin>>u>>v>>w;
			addedge(u,v,-w);
		}
		
		spfa(1);
		if(flag) cout<<"YES"<<endl;
		else cout<<"NO"<<endl;
	}
	return 0;
}
```

## 强连通问题：求强连通分量（SCC）

**两个点可互相到达**

### Kosaraju算法：运用反图技术

#### 裸题：hdu1269

```c++
#include<bits/stdc++.h>
using namespace std;
//求强联通分量的Kosaraju算法
//hdu1269
const  int num=10005;
int n,m;
vector<int>G[num],rG[num];
vector<int> S;//存第一次dfs1的结果，标记点的顺序
int vis[num],sccno[num],cnt;//cnt记录强连通分量的个数
void dfs1(int u)
{
	if(vis[u]) return;
	vis[u]=1;
	for(int i=0;i<G[u].size();i++)
	{
		dfs1(G[u][i]);
	}
	S.push_back(u);
}
void dfs2(int u)
{
	if(sccno[u]) return;
	sccno[u]=cnt;
	for(int i=0;i<rG[u].size();i++) dfs2(rG[u][i]);
}
void kosaraju(int n)
{
	cnt=0;
	S.clear();
	memset(vis,0,sizeof(vis));
	memset(sccno,0,sizeof(sccno));
	for(int i=1;i<=n;i++) dfs1(i);
	for(int i=n-1;i>=0;i--)
	{
		if(!sccno[S[i]])
		{cnt++;dfs2(S[i]);}
	}
}
int main()
{
	while(cin>>n>>m)
	{	int u,v;
		if(n==0&&m==0) break;
		for(int i=0;i<n;i++) {
			G[i].clear();rG[i].clear();
		}
		for(int i=0;i<m;i++)
		{

			cin>>u>>v;
			G[u].push_back(v);
			rG[v].push_back(u);//存反图 
		}
		kosaraju(n);
		if(cnt==1) cout<<"Yes"<<endl;
		else cout<<"No"<<endl;
	}
	return 0;
} 
```



### Tarjan算法：一次性把所有SCC都挖出来  用栈来处理

#### 裸题：hdu1269

```c++
#include<bits/stdc++.h>//Tarjan算法求强连通分量 
using namespace std;
const int N=10005;
int cnt;
int low[N],num[N],dfn;
int sccno[N];//表示该点处于的SCC 
stack<int> S;
vector<int> G[N];
void dfs(int u)
{
	S.push(u);
	low[u]=num[u]=++dfn;
	for(int i=0;i<G[u].size();i++)
	{
		int v=G[u][i];
		if(!num[v]){
			dfs(v);
			low[u]=min(low[v],low[u]);
		}
		else if(!sccno[v])//若v还在栈里
			low[u]=min(low[u],num[v]);
	}
	if(low[u]==num[u]){//栈底的点是SCC的祖先它的low=num 
        //遍历完后若还没能通过子孙返回更早的时间点则u为SCC的祖先
		cnt++;
		while(1){
			int v=S.top();
			S.pop();
			sccno[v]=cnt;
			if(u==v) break;//栈底的点是SCC的祖先 
		}
	}
}
void Tarjan(int n)
{
	cnt=dfn=0;
	memset(sccno,0,sizeof(sccno));
	memset(num,0,sizeof(num));
	memset(low,0,sizeof(low));
	for(int i=1;i<=n;i++)
		if(!num[i]) dfs(i);
}
int main()
{
	int n,m,u,v;
	while(scanf("%d%d",&n,&m),n!=0||m!=0)
	{
		for(int i=1;i<=n;i++) G[i].clear();
		for(int i=0;i<m;i++)
		{
			scanf("%d%d",&u,&v);
			G[u].push_back(v);
		}
		Tarjan(n);
		printf("%s\n",cnt==1?"Yes":"No");
	}
	return 0;
}
```

#### 变式一：Tarjan缩点，求到每个缩点的最小代价和（非AC代码）

```c++
#include<bits/stdc++.h>//hdu1827
using namespace std;
const int N=1010;
int cnt;
int low[N],num[N],dfn,w[N];//dfn是记录进入递归的顺序 
int M[N];
int sccno[N];
int in[N];
stack<int> S;
vector<int> G[N];
void dfs(int u)
{
	S.push(u);
	low[u]=num[u]=++dfn;
	for(int i=0;i<G[u].size();i++)
	{
		int v=G[u][i];
		if(!num[v])
		{
			dfs(v);
			low[u]=min(low[u],low[v]);	
		} 
		else if(!sccno[v])
		{
			low[u]=min(low[u],num[v]);
		}
	}
	if(low[u]==num[u])
	{
		cnt++;
		M[cnt]=0x7f7f7f;//记录每个新的缩点的最小花费 
		while(1)
		{
			int v=S.top();
			S.pop();
			sccno[v]=cnt;
			M[cnt]=min(M[cnt],w[v]);
			if(v==u) break;
		}
	}
}
void Tarjan(int n)
{
	cnt=dfn=0;
	memset(sccno,0,sizeof(sccno));
	memset(num,0,sizeof(num));
	memset(low,0,sizeof(low));
	memset(in,0,sizeof(in));
	for(int i=1;i<=n;i++)
	{
		if(!num[i]) dfs(i);
	}
}
int main()
{
	int n,m;
	cin>>n>>m;
	int u,v;
	for(int i=1;i<=n;i++) cin>>w[i];
	for(int i=1;i<=m;i++)
	{
		cin>>u>>v;
		G[u].push_back(v);
	}
	Tarjan(n);
	for(int i=1;i<=n;i++)
		for(int j=0;j<G[i].size();j++){
			int v=G[i][j];
			if(sccno[i]!=sccno[v])
				in[sccno[v]]++;//该缩点入度不为0 
		}
	int sum1=0,sum2=0;
	for(int i=1;i<=cnt;i++)
	{
		if(in[i]==0){
			sum1++;
			sum2+=M[i];
		}
	}
	cout<<sum1<<" "<<sum2<<endl;
	return 0;
}
```

#### 变式二：Tarjan+贪心 求每个缩点的最小入边权值和（TLE）

```c++
#include<iostream>
#include<cstring>
#include<algorithm>
#include<vector>
#include<stack>
using namespace std;
//hdu3072 Tarjan算法求最小代价
int cnt,dfn;
const int N=100010;
int num[N],low[N],M[N],sccno[N];
int W[100010];
vector<int>G[N];
stack<int> S;
bool flag[100010];
bool Flag[100010]; 
void dfs(int u)
{
	S.push(u);
	low[u]=num[u]=++dfn;
	for(int i=0;i<G[u].size();i++)
	{
		int v=G[u][i];
		if(!num[v])
		{
			dfs(v);
			low[u]=min(low[u],low[v]);
		}
		else if(!sccno[v])
		{
			low[u]=min(low[u],num[v]);
		}
	}
	if(low[u]==num[u]){
		cnt++;
		while(1)
		{
			int v=S.top();
			S.pop();
			sccno[v]=cnt;
			if(v==u) break;
		}
	}
}
void Tarjan(int n)
{
	cnt=0;
	memset(sccno,0,sizeof(sccno));
	memset(low,0,sizeof(low));
	memset(num,0,sizeof(num));
	for(int i=0;i<n;i++)
	{
		if(!num[i])	
		dfs(i);
	}	
}
int main()
{
	int n,m;
	while(cin>>n>>m)
	{
		for (int i=0;i<n;i++) G[i].clear();//不要忘
		memset(W,0x7f7f7f,sizeof(W));
		memset(M,0x7f7f7f,sizeof(M));
		memset(flag,0,sizeof(flag));//防止有些点没有入度标志为0
		memset(Flag,0,sizeof(Flag));
		for(int i=1;i<=m;i++)
		{
			int u,v,w;
			cin>>u>>v>>w;
			G[u].push_back(v);
			M[v]=min(M[v],w);//记录每个点最小入边权值
			flag[v]=1;
		}
		Tarjan(n);
		//for(int i=0;i<n;i++)
			//cout<<flag[i]<<" "<<sccno[i]<<endl;
	    for(int i=0;i<n;i++)
			for(int j=0;j<G[i].size();j++)
			{
				int v=G[i][j];
				if(sccno[i]!=sccno[v])//位于两个不同的缩点 
				{
					//cout<<i<<" "<<v<<endl;
					if(flag[v]==1){
					W[sccno[v]]=min(M[v],W[sccno[v]]);//求每个缩点最小入边权值 
					Flag[sccno[v]]=1;}
				}
			}
		int sum=0;
		for(int i=1;i<=cnt;i++)
		{
			if(Flag[i]==1)
			sum+=W[i];////求每个缩点最小入边权值和 
			
		}
		cout<<sum<<endl;
	}
	return 0;
}

```

#### 变式三：给定有向图至少添加多少条边才能成为强连通图？（非AC）

**思路：缩点之后MAX（出度为0的点个数，入度为0的点个数）**

```c++
#include<bits/stdc++.h>
using namespace std;
//hdu3836 一个有向图要添加多少条边才能变成强连通图
//Tarjan缩点 
int cnt,dfn;
const int N=20010;
int sccno[N], num[N],low[N];
int IN[50010],OUT[50010];//缩点后每个点的出度和入度 
vector<int> G[N];
stack<int> S;
void dfs(int u)
{
	S.push(u);
	low[u]=num[u]=++dfn;
	for(int i=0;i<G[u].size();i++)
	{
		int v=G[u][i];
		if(!num[v]){
			dfs(v);
			low[u]=min(low[u],low[v]);
		}
		else if(!sccno[v]){
			low[u]=min(low[u],num[v]);
		}
	}
	if(low[u]==num[u]){
		cnt++;
		while(1)
		{
			int v=S.top();
			S.pop();
			sccno[v]=cnt;
			if(v==u) break;
		}
	}
}
void Tarjan(int n)
{
	cnt=dfn=0; 
	memset(num,0,sizeof(num));
	memset(low,0,sizeof(low));
	memset(sccno,0,sizeof(sccno));
	for(int i=1;i<=n;i++){
		if(!num[i])
		dfs(i);
	}
}
int main()
{
	int n,m;
	while(cin>>n>>m)
	{
		int u,v;
		memset(IN,0,sizeof(IN));
		memset(OUT,0,sizeof(OUT));
		for(int i=1;i<=m;i++)
		{
			cin>>u>>v;
			G[u].push_back(v);
		}
		Tarjan(n);
		for(int i=1;i<=n;i++)
			for(int j=0;j<G[i].size();j++)
			{
				int v=G[i][j];
				if(sccno[i]!=sccno[v])
				{
					IN[sccno[v]]++;OUT[sccno[i]]++;
				}
			}
		int sum1=0,sum2=0;//记录出度为0和入度为0的点分别有多少个 
		for(int i=1;i<=cnt;i++)
		{
			if(IN[i]==0) sum1++;
			if(OUT[i]==0) sum2++;
		}
		if(cnt==1) cout<<"0"<<endl;
		else
		cout<<max(sum1,sum2)<<endl;
	}
	return 0;
}
```



## 有向图dfs判断连通性且判断是否存在

## 欧拉路：

## 最短路：

### ***类型1：基于链式前向星的SPFA+判断负环：***

**适合大图使用，但是不稳定，可能有极端数据卡时间复杂度，求最短路首选dijkstra**

```c++
#include<bits/stdc++.h>
using namespace std;
//基于链式前向星的bfs-SPFA 判断负环+最短路
const int inf=0x7f7f7f;
const int num=1000005;
int n,m,cnt;
struct edge
{
	int to,w,next;//边的相邻结点，边的权重，边的from点为起点的上一条边 
}edge[num];
int head[num];
int dis[num];//记录所有结点到起点的距离
bool inq[num];//表示结点是否在队列中
int neg[num];//判断负环
int pre[num];//记录前驱结点
void print_path(int s,int t){
	if(s==t) {cout<<s; return;}
	print_path(s,pre[t]);
	cout<<t;
} 
void init(){
	for(int i=0;i<num;i++){
		edge[i].next=-1;
		head[i]=-1;
	}
	cnt=0;
}
void addedge(int u,int v,int w)
{
	edge[cnt].to=v;
	edge[cnt].w=w;
	edge[cnt].next=head[u];
	head[u]=cnt;
	cnt++;
}
int spfa(int s)
{
	memset(neg,0,sizeof(neg));
	neg[s]=1;
	for(int i=1;i<=n;i++)
	{
		dis[s]=inf;inq[i]=false;
	}
	dis[s]=0;
	queue<int> Q;
	Q.push(s);
	inq[s]=true;
	while(!Q.empty())
	{
		int u=Q.front();Q.pop();
		inq[u]=false;
		for(int i=head[u];i!=-1;i=edge[i].next)
		{
			int v=edge[i].to,w=edge[i].w;
			if(dis[u]+w<dis[v])
			{
				dis[v]=dis[v]+w;
				pre[v]=u;
				if(!inq[v]){
					inq[v]=true;
					Q.push(v);
					neg[v]++;
					if(neg[v]>n) return 1;//出现负环 
				}
			}
		}
	}
	cout<<dis[n];
	return 0;
}
int main()
{
	while(cin>>n>>m){
	init();
	if(n==0&&m==0) return 0;
	while(m--){
		int u,v,w;
		addedge(u,v,w);
		addedge(v,u,w);
	}
	spfa(1);
    }
	return 0;
}
```



### ***\*类型2：Bellman-Ford算法：\****

```c++
using namespace std;
//bellman-ford最短路
const int inf=1e6;
const int num=105;
struct edge{
	int u,v,w;//边：起点，终点，权值 
}e[10005];
int n,m,cnt;
int pre[num];
//记录前驱结点pre[x]=y，x的前一个结点是y
void print_path(int s,int t)
{
	if(s==t) {cout<<s;return;}
	print_path(s,pre[t]);
	cout<<t;
}
void bellman()
{
	int s=1;//定义起点
	int d[num];
	for(int i=1;i<=n;i++)//一共有n轮操作
		d[i]=inf;
	d[s]=0;
	for(int k=1;k<=n;k++)
		for(int i=0;i<cnt;i++){
			int x=e[i].u,y=e[i].v;
			if(d[x]>d[y]+e[i].w)
			{
				d[x]=d[y]+e[i].w;
				pre[x]=y;
			}
		} 
	cout<<d[n]<<endl;
}
int main()
{
	while(~scanf("%d%d",&n,&m))
	{
		if(n==0&&m==0) return 0;
		cnt=0;//记录边的数量
		while(m--)
		{
			int a,b,c;
			scanf("%d%d%d",&a,&b,&c);
			e[cnt].u=a;e[cnt].v=b;e[cnt].w=c;cnt++;
			e[cnt].u=b;e[cnt].v=a;e[cnt].w=c;cnt++;	
		}	
		bellman();
	}	
	return 0;
} 
```

### ***\*类型3：Dijkstra算法最短路\****

```c++
#include<bits/stdc++.h>
using namespace std;//hdu2544 Dijkstra最短路径 邻接表+优先队列
const int  inf=1e6;
const int num=105;
struct edge{
	int from,to,w;
	edge(int a,int b,int c){
		from=a;to=b;w=c;
	}
};
vector<edge>e[num];//用于存图
struct s_node{
	int id,n_dis;
	s_node(int b,int c)
	{
		id=b;n_dis=c;//id:结点，n_dis:这个结点到起点的距离 
	}
}; 
bool operator<(s_node a,s_node b)  
{
	return a.n_dis>b.n_dis; //注意这里是输出小的在队首（判断和sort相反！） 
}
int n,m;
int pre[num];
void print_path(int s,int t)//打印从s到t的路径
{
	if(s==t) {cout<<s; return;}
	print_path(s,pre[t]);
	cout<<t;	
} 
void dijkstra()
{
	int s=1;
	int dis[num];//记录所有结点到起点的距离
	bool done[num];//表示到结点i的最短路径已经找到了 
	for(int i=1;i<=n;i++)
	{
		dis[i]=inf;done[i]=false;	
	} 
	dis[s]=0;
	priority_queue<s_node> Q;//优先队列，存结点信息
	Q.push(s_node(s,dis[s]));
	while(!Q.empty())
	{
		s_node u=Q.top();
		Q.pop();
		if(done[u.id])
			continue;//丢弃已经找到最短路径的结点，即集合A中的点
		done[u.id]=true;
		for(int i=0;i<e[u.id].size();i++)//检查结点u的所有邻居边
		{
			edge y=e[u.id][i];//u.id 的第i个邻居是y.to
			if(done[y.to]) continue;
			if(dis[y.to]>y.w+u.n_dis){
				dis[y.to]=y.w+u.n_dis;
				Q.push(s_node(y.to,dis[y.to]));
				pre[y.to]=u.id;
			} 
		}	
	} 
	cout<<dis[n]<<endl;
	//print_path(s,n);
}
int main()
{
	while(~scanf("%d%d",&n,&m))
	{
		if(n==0&&m==0) return 0;
		for(int i=1;i<=n;i+
		{
			e[i].clear();
		}
		while(m--)
		{
			int a,b,c;
			scanf("%d%d%d",&a,&b,&c);
			e[a].push_back(edge(a,b,c));
			e[b].push_back(edge(b,a,c)); 
		}
		dijkstra();
	}
}


```

## 最小生成树问题

### 最小生成树模板：

kruskal用于稀疏图  prim用于稠密图

#### Kruskal算法：加并查集判环

对边进行排序，依次把最短的边加入到T中并判环

```c++
#include<iostream>
#include<cstdio>
#include<algorithm>//hdu1102right 
using namespace std;
 
struct e{
	int x, y, w;
}edge[5055];
int parent[105];
int c[105][105];
int n, m;
 
bool comp(e a, e b){
	return a.w<b.w;
}
 
void initSet(){
	for(int i = 0; i <= n; i++){
		parent[i] = i;
	}
}
int find(int x){
	int r = x;
	while(r!=parent[r]){
		r = parent[r];
	}//一直找到根节点 
 
	int temp;
	while(x!=r){
		temp = x;
		x = parent[x];//x改为x的父结点 
		parent[temp] = r;//把当前结点的集改为根节点 
	}//把路径上元素的集改为根节点 
	return r;
}//并查集路径压缩非递归代码 
void kruskal(){
	int sum = 0, count = 0;
	initSet();
	
	for(int i = 0; i < m; i++){
		int x = find(edge[i].x);
		int y = find(edge[i].y);
		if(x!=y){
			count++;
			parent[x] = y;
			sum+= edge[i].w;
			//if(count==n-1){
			//	break;
			//}n个点有n-1条边
		}
	}
	cout<<sum<<endl;
}
 
int main(){
	int i , j , t, weight;
	while(cin>>n){
		m = 0;
		for(i = 1; i<=n; i++){
			for(j = 1; j <= n; j++){
				scanf("%d", &weight);
				if(i<j){
					edge[m].x = i;
					edge[m].y = j;
					edge[m].w = weight;
					c[i][j] = m++;//m是边的个数 
				}
			}
		}
		cin>>t;
		//m=n*(n-1)/2;也是一样的 
		while(t--){
			int a, b;
			scanf("%d %d", &a, &b);
			if(a>b){
				swap(a, b);
			}
			edge[c[a][b]].w= 0;
		}
		sort(edge, edge+m, comp);
		kruskal();
		
	}
	return 0;
}
```

```c++
#include<bits/stdc++.h>
using namespace std;
//hdu1102最小生成树  WA代码 
int n,q,m;
const int num=110;
int c[num][num];
int S[num];
struct edge{
	int u,v,w;
}e[num*num];
bool cmp(edge a,edge b)
{
	return a.w<b.w;
}
int find(int u)//并查集判环 没有路径压缩
{
	return S[u]==u?u:find(S[u]);
}
int kruskal()
{
	int ans=0;
	for(int i=1;i<=n;i++) S[i]=i;
	sort(e+1,e+1+m,cmp);
	for(int i=1;i<=m;i++)
	{
		int b=find(e[i].u);
		int c=find(e[i].v);
		if(b==c) continue;
		S[c]=b;
		ans+=e[i].w;
	}
	return ans;
}
int main()
{
	while(cin>>n)
	{
	m=1;
	int w;
	for(int i=1;i<=n;i++)
	{
		for(int j=1;j<=n;j++)
		{
			cin>>w;
			if(i<j){
				e[m].u=i;
				e[m].v=j;
				e[m].w=w;
				c[i][j]=m++;
			}
		}
	}
	//m=n*(n-1)/2;
	cin>>q;
	for(int i=1;i<=q;i++){
		int u,v;
		cin>>u>>v;
		if(u>v){
			swap(u,v);
		}
		e[c[u][v]].w=0;//已经开通 
	}
	cout<<kruskal()<<endl;
	}
	return 0;
}
```

#### prim算法：邻接矩阵存图

```c++
#include<bits/stdc++.h>
using namespace std;
//hdu1102最小生成树prim算法 邻接表存图 
const int num=110;
int n,inf=0x7f7f7f;
int cost[num][num];//uv边的权值
int mincost[num];//从集合X出发的边到每个顶点的最小权值 
bool used[num];//顶点i是否包含在X中 
int prim()
{
	mincost[1]=0;
	int res=0;
	while(1)
	{
		int v=-1;
		for(int u=1;u<=n;u++)
		{//从不属于X的顶点中选取X到其权值最小的顶点 
			if(!used[u]&&(v==-1||mincost[u]<mincost[v])) 
				v=u;
		}
		if(v==-1) break;
		used[v]=true;
		res+=mincost[v];
		for(int u=1;u<=n;u++){
			mincost[u]=min(mincost[u],cost[v][u]);//更新 
		}
	}
	return res;
}
int main()
{
	while(cin>>n)
	{
		memset(cost,inf,sizeof(cost));
		memset(used,false,sizeof(used));
		memset(mincost,inf,sizeof(mincost));
		if(n==0) break;
		for(int i=1;i<=n*(n-1)/2;i++)
		{
			int u,v,w;
			cin>>u>>v>>w;
			cost[u][v]=w;
			cost[v][u]=w;
		}
		cout<<prim()<<endl;
	}
	return 0;
}
```

### 最大生成树问题：Kruskal算法

```c++
#include<iostream>
#include<algorithm>
using namespace std;
//最大生成树poj2377  AC代码 
int n,m;
const int num=1010;
int S[num];
struct edge{
	int u,v,w;
}e[40010];
bool cmp(edge a,edge b)
{
	return a.w>b.w;
}
int find(int u)
{
	return S[u]==u?u:find(S[u]);
}
int kruskal()
{
	int ans=0,cnt=0;
	for(int i=0;i<n;i++)
	{
		S[i]=i;
	}
	sort(e,e+m,cmp);
	for(int i=0;i<m;i++)
	{
		int b=find(e[i].u);
		int c=find(e[i].v);
		if(b==c) continue;
		S[c]=b;
		cnt++;
		ans+=e[i].w;
		if(cnt==n-1) break;
	 } 
	if(cnt==n-1) return ans;//重点 
	return -1;
}
int main()
{
    while(cin>>n>>m)
    {
	    for(int i=0;i<m;i++){
		int u,v,w;
		cin>>u>>v>>w;
		e[i].u=u;e[i].v=v;e[i].w=w;
	    }
        cout<<kruskal()<<endl;
	}
    return 0;
}
```

## 网络流问题：

### 最大流问题：Ford-Fullkerson方法

#### Edmonds-Karp算法（EK）：

由于适用规模小所以用邻接矩阵存图

１.找到一条增广路２.找到瓶颈正向路径减少ｆｌｏｗ，反向路径增加ｆｌｏｗ３.直到找不到增广路

```c++
#include<bits/stdc++.h>
using namespace std;
//Edmonds-Karp算法 最大流问题
//hdu1532
const int inf=1e9;
const int maxn=300;
int n,m,G[maxn][maxn],pre[maxn];
int bfs(int s,int t)
{
	int flow[maxn];
	memset(pre,-1,sizeof(pre));
	flow[s]=inf;pre[s]=0;
	queue<int> Q;
	Q.push(s);
	while(!Q.empty())
	{
		int u=Q.front();
		Q.pop();
		if(u==t) break;
		for(int i=1;i<=m;i++){
			if(i!=s&&G[u][i]>0&&pre[i]==-1){
				pre[i]=u;
				Q.push(i);
				flow[i]=min(flow[u],G[u][i]);//更新结点流量 
			}
		}
	}
	if(pre[t]==-1) return -1;//没有找到新的增广路 
	return flow[t];
}
int maxflow(int s,int t)
{
	int Maxflow=0;
	while(1)
	{
		int flow=bfs(s,t);
		//执行一次BFS找到一条路径返回路径的流量
		if(flow==-1) break;//没有找到新的增广路
		int cur=t;//
		while(cur!=s)//一直沿路径回溯到起点 
		{
			int father=pre[cur];//pre记录路径上前一个点 
			G[father][cur]-=flow;//更新残留网络，正向减 
			G[cur][father]+=flow;
			cur=father;
		} 
		Maxflow+=flow; 
	}
	return Maxflow;
}
int main()
{
	while(cin>>n>>m)
    {
    	memset(G,0,sizeof(G));
		for(int i=0;i<n;i++){
			int u,v,w;
			cin>>u>>v>>w;
			G[u][v]+=w;//可能有重边 
		}	
		cout<<maxflow(1,m)<<endl;
	}	
	return 0;
} 
```

**EK算法效率不高，竞赛中一般用ISAP算法**

#### 最大流建模：

**一  ， 无向图转换为有向图  二，多个源点s和汇点t：添加超级源点和超级汇点，从s到每个源点都有一条边，从每个汇点都连一个边到t**

### 最小割问题：

### 最小费用最大流：

**从零流开始，每次增加一个最小费用路径，经过多次增广，直到无法再增加路径。**

**最大流问题和最短路径问题结合。**

**由于存在负边（不是负环）所以只能用Ford-Fulkerson方法+SPFA算法**

下面这道题是poj2135 把每条边的流量设为1每条边只能用一次，在图中添加一个超级源点和超级汇点

无向图变成四个边：首先分成有向边(u,v),(v,u);

有向边(u,v)变成一个正向的费用为cost ,容量为capacity的边，以及一个反向的费用为-cost,容量为0的边。

```c++
#include<iostream>
#include<cstring>
#include<queue>
#include<algorithm>
#include<stdio.h>
using namespace std;
//poj2135最小费用最大流
const int inf=0x3f3f3f3f;
const int N=1010;
int dis[N],pre[N],preve[N];//dis[i]记录起点到i的最短距离
int n,m;
struct edge{
	int to,cost,capacity,rev;
	//表示下一个点，费用，容量，前驱结点 
	edge(int a,int b,int c,int d){
		to=a;cost=b;capacity=c;rev=d;
	}
}; //邻接表存图
vector<edge> e[N];//存第i个结点连接的所有边 
void addedge(int from,int to,int cost,int capacity){
	e[from].push_back(edge(to,cost,capacity,e[to].size()));
	e[to].push_back(edge(from,-cost,0,e[from].size()-1));//把一个有向边再分为两个 
}
bool spfa(int s,int t,int cnt)
{
	bool inq[N];
	memset(pre,-1,sizeof(pre));
	for(int i=1;i<=cnt;i++){
		dis[i]=inf;inq[i]=false;
	}
	dis[s]=0;
	queue<int> Q;
	Q.push(s);
	inq[s]=true;
	while(!Q.empty())
	{
		int u=Q.front();
		Q.pop();
		inq[u]=false;
		for(int i=0;i<e[u].size();i++)
			if(e[u][i].capacity>0){
				int v=e[u][i].to,cost=e[u][i].cost;
				if(dis[u]+cost<dis[v]){
					dis[v]=dis[u]+cost;
					pre[v]=u;//v的前驱结点是u 
					preve[v]=i;//u的第i个边连接v点
					if(!inq[v]){
						inq[v]=true;
						Q.push(v);
					} 
				}
			}
	}
	return dis[t]!=inf;
}
int mincost(int s,int t,int cnt)
{
	int cost=0;
	while(spfa(s,t,cnt))
	{
		int v=t,flow=inf;//每次增加的流量 
		while(pre[v]!=-1){//回溯整个路径计算路径的流 
			int u=pre[v],i=preve[v];
			//u是v的前驱结点，u的第i个边连接v
			flow=min(flow,e[u][i].capacity);
			//所有边的最小容量是这条路的流
			v=u; 
		}
		v=t;
		while(pre[v]!=-1)
		{
			int u=pre[v],i=preve[v];
			e[u][i].capacity-=flow;
			e[v][e[u][i].rev].capacity+=flow;
			v=u;
		}
		cost+=dis[t]*flow;//费用累加，如果程序需要输出最大流， 
	}
	return cost; 
}
int main()
{
	while(~scanf("%d%d",&n,&m))
	{
		for(int i=0;i<N;i++) e[i].clear();
		for(int i=1;i<=m;i++)
		{
			int u,v,w;
			scanf("%d%d%d",&u,&v,&w);
			addedge(u,v,w,1);
			addedge(v,u,w,1);
		}
		int s=n+1,t=n+2;
		addedge(s,1,0,2);
		addedge(n,t,0,2);
		printf("%d\n",mincost(s,t,n+2));
	}
	return 0;
}
```

### 最大流问题变式：

#### 变式一：多源点多汇点

**添加超级源点t和超级汇点s，从s向每个源点连一个容量为对应最大流出容量的边，从每个汇点向t连一条容量为对应最大容量的边。**

#### 变式二：无向图

**当作有向图两个方向的边容量相同**

#### 变式三：顶点上也有容量限制的情况

**把每个顶点拆成两个，从入顶点到出顶点连一条顶点容量的边
