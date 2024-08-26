# [P2746 [USACO5.3\] 校园网Network of Schools](https://www.luogu.com.cn/problem/P2746)

## 大意:一个图 可能有环  a：求deg入度为0的点的个数   b：至少加多少条边让图所有点可以互相到达

## 思路：看代码

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
int head__[N],idx__;
bool vis[N];
struct node{
    int to,val,next;
};
node e[N],ee[N];
int din[N],dout[N];
int n,m,time__=0;
int low[N],dfn[N];
int scc[N],scc_cnt=0;
stack<int>st;
void add(int u,int v,int val){
    e[idx] = {v,val,head[u]};
    head[u] = idx++;
}
void add__(int u,int v,int val){
    ee[idx__] = {v,val,head__[u]};
    head__[u] = idx__++;
} 

void bd(){
    cin>>n;
    memset(head,-1,sizeof(head));
    for(int i=1 ; i<=n ; ++i){
        int x;
        cin>>x;
        while(x!=0){
            add(i,x,0);
            cin>>x;
        }
    }
}
void tarjan(int u){
    dfn[u] = low[u] = ++time__;
    st.push(u);
    vis[u] = 1;
    for(int i=head[u] ; i!=-1 ; i=e[i].next){
        int v = e[i].to;
        if(!dfn[v]){
            tarjan(v);
            low[u] = min(low[u] , low[v]);
        }
        else if(vis[v])
            low[u] = min(low[u] , dfn[v]);
    }
    if(low[u] == dfn[u]){
        ++scc_cnt;
        while(1){
            int v = st.top();
            st.pop();
            vis[v] = false;
            scc[v] = scc_cnt;
            if(v==u) break;
        }
    }
}
void bd__(){
    for(int i=1 ; i<=n ; ++i){
        for(int u = head[i] ; u!=-1 ; u=e[u].next){
            int v = e[u].to;
            int scc_u = scc[i];  // 获取节点 i 的 SCC 编号
            int scc_v = scc[v];  // 获取节点 v 的 SCC 编号
            if(scc_u != scc_v){
                add__(scc_u,scc_v,0);  // 在新图中添加边
                din[scc_v]++;
                dout[scc_u]++;
            }
        }
    }
}
void debug(){
    for(int i=1 ; i<=n ; ++i){
        //printf("%d\n",scc[i]);
    }
}
int main(){
    ios;
    bd();
    for(int i=1 ; i<=n ; ++i)
        if(!dfn[i]) tarjan(i);
    bd__();
    //debug();

    
    int ans1=0,ans2=0;
    for(int i=1 ; i<=scc_cnt ; ++i){
        //printf("din==%d dout==%d\n",din[i],dout[i]);
        if(din[i]==0)  ans1++;
        if(dout[i]==0) ans2++;
    }
    int ans = max(ans1,ans2);
    //取max
    if(scc_cnt==1) ans=0;
    cout<<ans1<<"\n"<<ans;
    
    return 0;
}

```



