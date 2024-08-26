# [P2341 [USACO03FALL / HAOI2006\] 受欢迎的牛 G ](https://www.luogu.com.cn/problem/P2341)

## 大意:其他所有点通过其他的scc到达图中存在的某个scc，那么输出这个scc的点的数量

## 思路:Tarjan求SCC,然后看每个scc的出度dout,如果有两个scc的dout=0，说明这两个scc不能到达，不满足要求，否则求出那个scc中有多少个点就行了

```cpp
#include <cstdio>
#include <stack>
#include <iostream>
#include <cstring>
#include <vector>
#define ios std::ios::sync_with_stdio(false);std::cin.tie(0); 

using namespace std;

const int N = 2e5+9;

int head[N], idx = 0;
int head__[N], idx__ = 0;
bool vis[N];
struct node {
    int to, val, next;
} e[N], ee[N];
int din[N], dout[N];
int n, m;
int time__ = 0;
int dfn[N], low[N], scc[N], scc_cnt = 0,scc_count[N];
stack<int> st;

void add(int u, int v, int val) {
    e[idx] = {v, val, head[u]};
    head[u] = idx++;
}

void add__(int u, int v, int val) {
    ee[idx__] = {v, val, head__[u]};
    head__[u] = idx__++;
}

void bd() {
    cin >> n >> m;
    memset(head, -1, sizeof(head));
    memset(head__, -1, sizeof(head__));
    for (int i = 1; i <= m; ++i) {
        int u, v;
        cin >> u >> v;
        add(u, v, 0);
    }
}

void tarjan(int u) {
    low[u] = dfn[u] = ++time__;
    vis[u] = 1;
    st.push(u);
    for (int i = head[u]; i != -1; i = e[i].next) {
        int v = e[i].to;
        if (!dfn[v]) {
            tarjan(v);
            low[u] = min(low[u], low[v]);
        } else if (vis[v]) {
            low[u] = min(low[u], dfn[v]);
        }
    }
    if (low[u] == dfn[u]) {
        scc_cnt++;
        while (1) {
            int v = st.top();
            st.pop();
            vis[v] = 0;
            scc[v] = scc_cnt;
            scc_count[scc_cnt]++;
            if (v == u) break;
        }
    }
}

void bd__() {
    for (int i = 1; i <= n; ++i) {
        for (int u = head[i]; u != -1; u = e[u].next) {
            int v = e[u].to;
            int scc_u = scc[i];
            int scc_v = scc[v];
            if (scc_u != scc_v) {
                add__(scc_u, scc_v, 0);
                din[scc_v]++;
                dout[scc_u]++;
            }
        }
    }
}
void debug(){
    for(int i=1 ; i<=n; ++i) printf(" i=%d scc=%d\n",i,scc[i]);
}
int main() {
    ios;
    bd();
    for (int i = 1; i <= n; ++i)
        if (!dfn[i])
            tarjan(i);
    bd__();
    int ans=0;
    int pos=0;
    //debug();
    for(int i=1 ; i<=scc_cnt ; ++i){
        if(dout[i]==0){
            ans++;
            pos=i;
        }
        if(ans==2){
            //判定是否两个scc的dout是0
            cout<<"0";
            return 0;
        }
    }
    cout << scc_count[pos];
    return 0;
}

```

