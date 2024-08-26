$$
\Large给出一个图，求出SCC后缩点得到新的图
$$

# 做法:Tarjan 记录scc 然后根据SCC去重新建图

```cpp
#include <cstdio>
#include <stack>
#include <algorithm>
#include <iostream>
#include <cstring>
#include <vector>
#include <queue>
#define ep emplace_back 

#define lld long long 
#define ios std::ios::sync_with_stdio(false);std::cin.tie(0); 
#define vec vector 
const int N = 4e5+9;

const int INF = 0x7FFFFFFF; //2147483647

const int inf1 = 0x3f3f3f3f; //1061109567
const int inf2 = 0x7f7f7f7f; //2139062143 memset赋值用

using namespace std;

int head[N], idx = 0;
bool vis[N];
struct node {
    int to, val, next;
};
node e[N];
int n, m;
int time_ = 0;
stack<int> st;
int low[N], dfn[N];
int scc[N], scc_cnt = 0;  // 用于标记强连通分量的总数,也是每个点的scc编号
int a[N], p[N], in[N];
lld ans = 0,dist[N]; // 新增：一些需要的变量
int din[N];
void add(int u, int v, int val) {
    e[idx] = {v, val, head[u]};
    head[u] = idx++;
}
node ee[N];
int idx__=0,head__[N];
void add__(int u,int v,int val){
    ee[idx__] = {v,val,head__[u] };
    head__[u] = idx__++;
}
void bd() {
    cin >> n >> m;
    memset(head, -1, sizeof(head));
    memset(head__, -1, sizeof(head__));
    //for (int i = 1; i <= n; ++i) cin >> a[i];
    //如果每个点有权值的话
    for (int i = 1; i <= m; ++i) {
        int u, v;
        cin >> u >> v;
        add(u, v,0);
    }
}

void tarjan(int u) {
    low[u] = dfn[u] = ++time_;
    vis[u] = true;
    st.push(u);
    for (int i = head[u]; i != -1; i = e[i].next) {
        int v = e[i].to;
        if (dfn[v] == 0) {
            tarjan(v);
            low[u] = min(low[u], low[v]); // low[u] 和 low[v] 比较
        } 
        else if (vis[v]) {
            //如果在stack中
            low[u] = min(low[u], dfn[v]);
            //low(u)和他字节点的时间戳比较
        }
    }

    if (low[u] == dfn[u]) {
        //每个scc的根节点
        ++scc_cnt;//scc编号
        while (true) {
            int v = st.top();
            st.pop();
            vis[v] = false;//出栈
            scc[v] = scc_cnt; // 给每个点分配一个 scc 编号
            if (v == u) break;
        }
    }
}

void debug(){
    //检查tarjan后的scc值
    for(int i=1 ; i<=n ; ++i)
        cout<<scc[i]<<"\n";
    
}
void bd__() {
    for (int i = 1; i <= n; ++i) {
        for (int u = head[i]; u != -1; u = e[u].next) {  // 注意循环变量的使用
            int v = e[u].to;
            // 每个点对应的 SCC
            int scc_u = scc[i];  // 注意，这里应该是 i 对应的 SCC
            int scc_v = scc[v];
            if (scc_u != scc_v) {
                // 不在同一个 SCC 里面，而且 u 连接了 v，说明 scc_u 连接了 scc_v
                add__(scc_u, scc_v, 0);
                din[scc_v]++;
            }
        }
    }
}
int main() {
    ios;
    bd();
    for (int i = 1; i <= n; ++i)
        if (!dfn[i]) tarjan(i);
        //如果没有访问过 那么就访问  否则跳过  time:O(n+m)
    bd__();

    return 0;
}
```

