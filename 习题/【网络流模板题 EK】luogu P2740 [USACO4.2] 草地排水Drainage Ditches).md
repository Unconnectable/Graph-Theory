# [P2740 [USACO4.2\] 草地排水Drainage Ditches)](https://www.luogu.com.cn/problem/P2740)

## 大意:网络流模板

## 做法:EK增广路

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

#define int long long 
#define ios std::ios::sync_with_stdio(false);std::cin.tie(0); 
#define vec vector 
const int N = 2e6+9;
const int INF = 0x7FFFFFFF; //2147483647

const int inf1 = 0x3f3f3f3f; //1061109567
const int inf2 = 0x7f7f7f7f; //2139062143 memset赋值用

using namespace std;
int n,m,src=1,sink;
int head[N],idx=1;
bool vis[N];
int dist[N];
int pre[N];
int ans=0;
int flag[2552][2502];
struct node{
    int to,capacity,next;
}e[N];
void add(int u,int v,int val){
    e[++idx] = {v,val,head[u]};
    head[u] = idx;
    e[++idx] = {u,0,head[v]};
    head[v] = idx;
}
bool bfs(){
    memset(vis,0,sizeof vis);
    dist[src] = inf2;
    queue<int>q;
    q.push(src);
    vis[src]=1;

    while(q.size()){
        int u = q.front();
        q.pop();
        for(int i=head[u] ; i!=0 ; i=e[i].next){
            int v = e[i].to;
            int val = e[i].capacity;
            if(vis[v] || e[i].capacity==0) continue;
            vis[v]=1;
            pre[v] = i;
            dist[v] = min(dist[u],val);
            q.push(v);
            if(v==sink)
                return 1;
        }
    }
    return false;
}
void EK(){
    int u = sink;
    while(u != src){
        int mmin_stream = dist[sink];
        int last = pre[u];
        e[last].capacity -= mmin_stream;
        e[last ^ 1].capacity += mmin_stream;
        u = e[last ^ 1].to;
    }
    ans+=dist[sink];
}
void bd(){
    cin>>m>>n;
    sink=n;
    for(int i=1; i<=m; ++i){
        int u,v,val;
        cin>>u>>v>>val;
        if(flag[u][v]==0){
            add(u,v,val);
            flag[u][v] = idx-1;
        }
        else 
            e[flag[u][v]].capacity+=val;
    }
}
signed main(){
    ios;
    bd();
    while(bfs()){
        EK();
    }
    cout<<ans;
    return 0;
}
```

