# LCA最近公共祖先	

$$
\begin{align}
要求 \ 给出一个树和他的根节点\text{root} \quad给出Q个询问 回答\text {LCA}(a,b)
\end{align}
$$

## 给出一个表格

| 最近公共祖先 | 朴素算法     | 倍增算法           | Tarjan算法               | 树链剖分                                |
| ------------ | ------------ | ------------------ | ------------------------ | :-------------------------------------- |
| 数据结构     | fa[u],dep[u] | fa[u] [v], dep[u]  | query[u], ans[i], vis[u] | fa[u], dep[u], size_[u], son[u], top[u] |
| 算法         | 暴力         | 倍增法             | 并查集                   | 重链剖分                                |
| 做法         | 暴力一次跳跃 | 深搜打表，跳跃查询 | 深搜，回时指父，离时搜根 | 两遍深搜打表，跳跃查询                  |
| 时间复杂度   | O(n^2)       | O(n + mlogn)       | O(n+m)                   | O(n + mlogn)                            |



## 朴素版本


$$
\begin{align*}
& \mathrm{LCA}朴素版\quad查询复杂度:O(n)  \\
& 一次一次往上跳\\
& 让u是更深的节点\quad然后把u跳到和v一样的高度\\
& 然后u,v同时往上跳 当u==v时就是答案
\end{align*}
$$


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
const int N = 2e6+9;
const int INF = 0x7FFFFFFF; //2147483647

const int inf1 = 0x3f3f3f3f; //1061109567
const int inf2 = 0x7f7f7f7f; //2139062143 memset赋值用

using namespace std;
int head[N],idx=0;
struct node{
    int to,val,next;
};
node e[N<<1];
int fa[N];
void add(int u ,int v, int val){
    e[idx] = {v,val,head[u]};
    head[u]  = idx++;
}
int a[N],b[N];
int n,root,Q;
int depth[N],max_dep=-1;
int pa[N];
vec<int>child[N];
void bd(){
    cin>>n>>Q>>root;
    memset(head,-1,sizeof(head));
    for(int i=1;i<=n;++i){
        int u,v;
        cin>>u>>v;
        add(u,v,0);
        add(v,u,0);
        //树是双向边
    }
}
void dfs(int u){
    for(int i=head[u] ; i!=-1 ; i=e[i].next){
        int v = e[i].to;
        if(v == fa[u]) continue;
        fa[v] = u;
        depth[v] = depth[u]+1;
        max_dep = max(max_dep,depth[v]);
        dfs(v);
    }
}
int LCA(int u,int v){
    //朴素版LCA  一次一次向上跳
    if(depth[u] < depth[v])
        swap(u,v);
    //默认最深的点是u


    while(depth[u] != depth[v]){
        u = fa[u];
        //先把u往上跳
        //u的深度和v一样
    }
    while( u != v){
        u=fa[u];
        v=fa[v];
        //u,v深度一样之后只需要把u v一起往上跳跃
        //当u==v 时 这个时候的u==v ==LCA(u,v)
    }
    return u;
}
int main(){
    ios;
    bd();
    depth[root] = 0;
    fa[root] = -1;
    dfs(root);
    for(int i=1;i<=Q;++i){
        int a,b;
        cin>>a>>b;
        cout<<LCA(a,b)<<"\n";
    }
    return 0;
}

```

## 倍增加速算法版本

$$
\begin{align}
& 在线版本\to倍增法\quad n = \sum_{k=0} ^{\log_2n}2^{k}\\
& 不断分解查询的步骤\\
& 即2^k = 2^{k-1}+2^{k-1}\quad \\
&令u=\mathrm{anc}(i,j-1) ,\mathrm{anc}(i,j) = \mathrm{anc}(u,j-1)\\
&然后通过\text{dfs}记录每个点的\text{depth}和他的父亲节点\\
&需要注意的问题是可能会跳过头，因此需要跳到那个最离v最近的地方\\
&然后同时把u,v向上跳 u,v的\text{anc}(u,0)就是\text{LCA}(u,v)
\end{align}
$$

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
const int N = 5e5+9;
const int INF = 0x7FFFFFFF; //2147483647
const int LOG = log2(N)+1;
const int inf1 = 0x3f3f3f3f; //1061109567
const int inf2 = 0x7f7f7f7f; //2139062143 memset赋值用

using namespace std;

int n,Q,root;
int logn;
int anc[N][LOG];
//anc(i,j):i号节点的第2^(j-1)个祖先节点 
int depth[N],max_depth=0;
int head[N],idx=0;
bool vis[N];
struct node{
    int to,val,next;
};
node e[N<<1];
void add(int u,int v,int val){
    e[idx] = {v,val,head[u]};
    head[u] = idx++;
}
void bd(){
    cin>>n>>Q>>root;
    logn = log2(n);
    memset(head,-1,sizeof head);
    for(int i=1;  i<=n-1 ;++i){
        int u,v,val;
        cin>>u>>v;
        add(u,v,0);
        add(v,u,0);
    }
}
void dfs(int u,int fa){
    anc[u][0] = fa;
    //2^(0)=1   v的一号祖先就是父亲
    for(int i=head[u] ; i!=-1 ; i=e[i].next){
        int v = e[i].to;
        if( v==fa ) continue;
        //双向边会跑到父亲 故跳过
        depth[v] = depth[u]+1;
        dfs(v,u);
    }
}
void init(){
    //预处理dfs后的所有祖先
    //pow(2,k) = pow(2,k-1) + pow(2,k-1)
    //需要用到j-1 故j从1开始
    for(int j = 1 ; j<=logn ;++j){
        for(int i=1 ; i<=n ; ++i){
            int v = anc[i][j-1];
            anc[i][j]= anc[v][j-1];
        }
    }
}
int LCA(int u,int v){
    if(depth[v] > depth[u])
        swap(u,v);
        //让u是最深的节点
    /*
    for(int i=logn ; i>=0 ; --i){
        if(depth[anc[u][i]] >= depth[v]){
            u = anc[u][i];
        }
    }
    */
    for(int i=logn ; i>=0 ; --i ){
        if( depth[u]-(1<<i) >= depth[v] ){
            u = anc[u][i];
        }
    }
    
    if(u == v) return u;//提前判定一次

    for(int i=logn ; i>=0 ; --i ){
        if(anc[u][i] != anc[v][i]){
            u= anc[u][i];
            v= anc[v][i];
        }
    }
    return anc[u][0];
}
int main(){
    ios;
    bd();
    dfs(root,-1);
    init();
    for(int i=1 ; i<=Q ; ++i){
        int u,v;
        cin>>u>>v;
        cout<<LCA(u,v)<<"\n";
    }
    return 0;
}
```

## Tarjan算法

```cpp
sssxxx
```



## 这是树链剖分算法

```cpp
#include <cmath>
#include <algorithm>
#include <iostream>
#include <cstring>
#include <vector>

#define lld long long 
#define ios std::ios::sync_with_stdio(false);std::cin.tie(0); 

using namespace std;
const int N =5e5+9;
int head[N],idx=0;
bool vis[N];
int n,m,Q,root;
int dep[N],fa[N],top[N],size_[N],son[N];
vector<int>e[N<<1];
void bd(){
    cin>>n>>Q>>root;
    memset(head,-1,sizeof(head));
    for(int i=1 ; i<=n-1 ; ++i){
        int u,v;
        cin>>u>>v;
        e[u].push_back(v);
        e[v].push_back(u);
    }
}
void dfs1(int u,int fa_){
    fa[u] = fa_;
    dep[u] = dep[fa_]+1;
    size_[u] = 1;
    for(int v:e[u]){
        if(v==fa_) continue;
        dfs1(v,u);
        size_[u] = size_[u] + size_[v];
        if(size_[son[u]] < size_[v]){
            son[u] = v;
        }
    }
}
void dfs2(int u,int topu){
    //topu:u这条链上的顶点
    top[u] = topu;
    //没有重儿子说明是叶子节点 结束dfs
    if(son[u]==0) return;
    dfs2(son[u],topu);
    
    //先记录每个点的topu 再更新
    for(int v:e[u]){
        //上面的dfs搜的就是重儿子son[u] 这里不重复搜了跳过
        if(v==fa[u] || v==son[u])  continue;
        //剩下的就是轻儿子 轻儿子都是叶子节点  单独成链  这时候端点就是自己
        dfs2(v,v);
        
    }
    
}
int LCA(int u,int v){
    while(top[u] != top[v]){
        if(dep[top[u]] < dep[top[v]])
            swap(u,v);
        u = fa[top[u]];
    }
    //当他们在同一条重链上时结束循环
    //深度小的哪个点就是LCA
    return dep[u]<dep[v]?u:v;
    
}
int main(){
    ios;
    bd();
    dfs1(root,-1);
    dfs2(root,root);
    while(Q--){
        int a,b;
        cin>>a>>b;
        cout<<LCA(a,b);
        cout<<"\n";
    }
    
    return 0;
}
```

