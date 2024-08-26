> # 题目链接https://www.luogu.com.cn/problem/P5908

### 题目大意:

$$
\begin{align*}
& 给定n个点构成一颗树 每条边val=1\\
& 求从根节点Root=1开始 \quad 其它所有点v到Root的距离\mathrm{dis(v,Root)} <=\mathrm{d}的点的数量\\
\end{align*}
$$



# 思路：

##### 1.bfs 队列跑一遍 记录每个点的父亲  遇到父亲就跳过

##### 2.dfs 同上 不过dfs使用递归，用另一个参数记录父亲即可 无需开数组father[N]

```cpp
#include <cstdio>
#include <queue>
#include <deque>
#include <stack>
#include <map>
#include <cmath>
#include <algorithm>
#include <iostream>
#include <cstring>
#include <vector>
#define ep emplace_back 

#define lld long long 
#define ios std::ios::sync_with_stdio(false);std::cin.tie(0); 
#define vec vector 
const int N = 2e5+9;
const int INF = 0x7FFFFFFF; //2147483647

const int inf1 = 0x3f3f3f3f; //1061109567
const int inf2 = 0x7f7f7f7f; //2139062143 memset赋值用

using namespace std;

vec<int>adj[N];
struct node{
    /* data */
    int to,val,next;
};
node e[N];
int idx=0,head[N];
void add(int u,int v,int val){
    e[idx] = {v,val,head[u]};
    head[u]  = idx++;
}
int n,d;//n 个点
int ans=0;
int dist[N];//所有节点到根节点1的距离
void bd(){
    cin>>n>>d;
    for(int i=1;i<=n;++i) head[i] = -1;
    for(int i=1;i<=n-1;++i){
        int u,v;
        cin>>u>>v;
        add(u,v,0);//双向
        add(v,u,0);
    }
}
void dfs(int u,int father){
    if( dist[u] <= d) ans++;
    for(int i=head[u] ; i!=-1 ; i = e[i].next){
        int v = e[i].to;
        if( v == father) continue;
        dist[v] = dist[u]+1;
        dfs(v,u);
    }
}

bool vis[N];
int fa[N];
void bfs(){
    queue<int>q;
    q.push(1);
    while(!q.empty()){
        int u = q.front();
        q.pop();
        if(dist[u] <= d) 
            ans++;
        for(int i = head[u] ; i!=-1 ; i=e[i].next){
            int v = e[i].to;
            if(v == fa[u]) continue;
            fa[v] = u;
            dist[v] = dist[u] + 1;
            q.push(v);
        }
    }
}
int main(){
    ios;
    bd();
    //dfs(1,0);
    bfs();
    cout<<ans-1;
    //第一次会判定 dist[1] <=d ? 答案为真  ans+= 因此ans要-1
    return 0;
}
```

