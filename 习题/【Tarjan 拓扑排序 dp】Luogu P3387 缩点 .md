# [P3387 【模板】缩点 ](https://www.luogu.com.cn/problem/P3387)

## 大意：n个图，m条边，每个点都有值a[i]，求路径经过的点权值之和最大

## 思路:因为会出现环 Tarjan 求scc 记录din dout 然后拓扑排序，dp[v]是经过v点的最大值,每经过一个点就记录经过他的最大值  然后取max

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
int head[N],idx=0;
int head__[N],idx__=0;
bool vis[N];
struct node{
    int to,val,next;
}e[N],ee[N];
int n,m;
int a[N],scc_val[N];
//a[n] 每个点的权值
//scc_val[n] 每个scc的权值之和
int time__,low[N],dfn[N];
stack<int>st;
lld ans=0;
lld dp[N];
int din[N],dout[N];
int scc[N],scc_cnt,scc_count[N];
void add(int u,int v,int val){
    e[idx] = {v,val,head[u]};
    head[u] = idx++;
}
void add__(int u,int v,int val){
    ee[idx__] = {v,val,head__[u]};
    head__[u] = idx__++;
}
void bd(){
    cin>>n>>m;
    memset(head,-1,sizeof(head));
    memset(head__,-1,sizeof(head__));
    for(int i=1 ; i<=n ; ++i)
        cin>>a[i];
    for(int i=1 ; i<=m ; ++i){
        int u,v;
        cin>>u>>v;
        add(u,v,0);
    }
}
void tarjan(int u){
    dfn[u] = low[u] = ++time__;
    vis[u] = 1;
    st.push(u);
    for(int i=head[u] ; i!=-1 ; i=e[i].next){
        int v =e[i].to;
        if(!dfn[v]){
            tarjan(v);
            low[u] = min(low[u],low[v]);
        }
        else if(vis[v]){
            low[u] = min(low[u],dfn[v]);
        }
    }
    if(low[u] == dfn[u]){
        scc_cnt++;
        while(1){
            int v = st.top();
            st.pop();
            vis[v] = 0;
            scc[v] = scc_cnt;
            scc_val[scc_cnt]+=a[v];
            if(v== u) break;
        }
    }
}
void bd__(){
    for(int i=1 ; i<=n ; ++i){
        for(int u = head[i] ; u!=-1; u = e[u].next){
            int v = e[u].to;
            int scc_u = scc[i];
            int scc_v = scc[v];
            if(scc_u != scc_v){
                add__(scc_u,scc_v,0);
                din[scc_v]++;
                dout[scc_u]++;
            }
        }
    }
}

void topo(){
    queue<int>q;
    
    // 初始化队列，将所有入度为0的强连通分量入队
    for(int i=1 ; i<=scc_cnt ; ++i){
        if(din[i]==0){
            q.push(i);
            dp[i] = scc_val[i]; // 初始时，dp[i] 等于该强连通分量的权值之和
        }
    }
    
    // 进行拓扑排序
    while(!q.empty()){
        int u = q.front();
        q.pop();
        for(int i = head__[u] ; i != -1 ; i = ee[i].next){
            int v = ee[i].to;
            // 更新 dp[v]，确保其值为经过当前点u的最大路径和
            dp[v] = max(dp[v], dp[u] + scc_val[v]);
            --din[v];
            // 如果 v 的入度变为 0，则将 v 入队
            if(din[v] == 0){
                q.push(v);
            }
        }
    }
}

int main(){
    ios;
    bd();
    for(int i=1 ; i<=n ; ++i)
        if(!dfn[i])
            tarjan(i);
    bd__();
    topo();
    for(int i=1 ; i<=scc_cnt ; ++i)
        ans = max(ans,dp[i]);
    cout<<ans;
    return 0;
}
```

