# [P3388 【模板】割点](https://www.luogu.com.cn/problem/P3388)

## Note:图可能不联通，因此每次tarjan都要更新root

```cpp
#include <cstdio>
#include <stack>
#include <cmath>
#include <algorithm>
#include <iostream>
#include <cstring>
#include <vector>

#define int long long 
#define ios std::ios::sync_with_stdio(false);std::cin.tie(0); 
const int N = 2e6+9;

using namespace std;


int n,m;
int idx=0,head[N];
int dfn[N],low[N];
int time__=0;
struct node{
    int to,val,next;
}e[N<<1];
int cut[N];
int root;
vector<int>cut_vertex;
void add(int u,int v,int val){
    e[idx] = {v,val,head[u]};
    head[u] = idx++;
}
void bd(){
    cin>>n>>m;
    memset(head,-1,sizeof head);
    for(int i=1;  i<=m ;++i){
        int u,v;
        cin>>u>>v;
        add(u,v,0);
        add(v,u,0);
    }
}
void tarjan(int u) {
    low[u] = dfn[u] = ++time__;
    int child = 0;
    for(int i = head[u]; i != -1; i = e[i].next) {
        int v = e[i].to;
        if (!dfn[v]) {
            tarjan(v);
            low[u] = min(low[u], low[v]);
            if(low[v] >= dfn[u]){
                child++;
                if(u!=root || child > 1){
                    cut[u]=1;
                }
            }
        } 
        else 
            low[u] = min(low[u], dfn[v]);
    }
}
signed main(){
    ios;
    bd();
    for(int i=1 ; i<=n ;++i)
        if(!dfn[i]){
            root=i;
            tarjan(i);
        }
    int ans=0;
    for(int i=1 ; i<=n ;++i)
        if(cut[i]==1)
            ans++;
    cout<<ans<<"\n";
    for(int i=1 ; i<=n ;++i)
        if(cut[i]==1)
            cout<<i<<" ";
    return 0;
}
```

# [T103481 【模板】割边](https://www.luogu.com.cn/problem/T103481)

这次要找边，每次dfs的时候记录他的父节点然后判断一下，不要往回跑

```cpp
#include <cstdio>
#include <algorithm>
#include <iostream>
#include <cstring>
#include <vector>
#define ep emplace_back 

#define int long long  
#define ios std::ios::sync_with_stdio(false);std::cin.tie(0);  
const int N = 2e6+9;
using namespace std;

int n,m;
struct node{
    int to,val,next;
}e[N<<1];
int head[N],idx=0;
int dfn[N],low[N];
int time__=0;
bool vis[N];

void add(int u,int v,int val=0){
    e[idx] = {v,val,head[u]};
    head[u] = idx++; 
}
void bd(){
    cin>>n>>m;
    memset(head,-1,sizeof head);
    for(int i=1 ; i<=m ; ++i){
        int u,v;
        cin>>u>>v;
        add(u,v);
        add(v,u);
    }
}
struct edge{
    int edge,from,to;
};
vector<edge>ee;
void tarjan(int u,int fa){
    low[u] = dfn[u] = ++time__;
    for(int i=head[u] ; i!=-1 ; i=e[i].next){
        int v = e[i].to;
        if(!dfn[v]){
            tarjan(v,u);
            low[u] = min(low[u],low[v]);
            if (low[v] > dfn[u]) {
                ee.push_back(edge{i/2+1,u,v});//加边
            }
        }
        else if(v!=fa)
            low[u] = min(low[u],dfn[v]);
    }
}
signed main(){
    ios;
    bd();
    for(int i=1 ; i<=n ;++i)
        if(!dfn[i])
            tarjan(i,i);

    cout<<ee.size()<<"\n";
    for(auto x:ee){
        //printf("%lld %lld %lld",x.edge,x.from,x.to);
    }
    return 0;
}
```

