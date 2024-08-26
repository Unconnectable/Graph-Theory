# 两种算法Prim and Kruskal

## Prim: 使用链式前项星

从根节点root开始加点直到加完所有点

dist[u] 表示从已经包含在 MST 中的节点到节点 u 的最小边权值

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
#define lld long long 
#define ios std::ios::sync_with_stdio(false);std::cin.tie(0); 
#define vec vector 

const int N = 2e6+9;
const int INF = 0x7FFFFFFFFFFFFFFF; // Larger value for long long

const int inf1 = 0x3f3f3f3f3f3f3f3f; // Larger value for long long
const int inf2 = 0x7f7f7f7f7f7f7f7f; // Larger value for long long memset赋值用

using namespace std;

int head[N], idx = 0;
int dist[N], vis[N]; // Declare dist and vis arrays
struct node {
    int to, val, next;
} e[N << 1];
void add(int u, int v, int val) {
    e[idx] = {v, val, head[u]};
    head[u] = idx++;
}
int sum = 0;
int n, m;
int cnt = 0;
void bd() {
    cin >> n >> m;
    memset(head, -1, sizeof head); // Initialize head array
    fill(dist, dist + N, inf2); // Initialize distances
    for (int i = 1; i <= m; ++i) {
        int u, v, val;
        cin >> u >> v >> val;
        add(u, v, val);
        add(v, u, val);
    }
}
struct edge {
    int u, v, dis;
};
bool operator <(edge a, edge b) {
    return a.dis > b.dis; // Descending order for priority queue
}
struct mst {
    int u, v, val;
};

vec<mst> TT;

void Prim() {
    //dist[u] 表示从已经包含在 MST 中的节点到节点 u 的最小边权值
    int src = 1;
    priority_queue<edge> pq;
    dist[src] = 0; 
    pq.push({src, src, 0}); 
    while (!pq.empty()) {
        int u = pq.top().u;
        int dis = pq.top().dis;
        int v = pq.top().v;
        pq.pop();

        if (vis[u]) continue;

        vis[u] = 1;
        cnt++;
        sum += dis;

        if (dis != 0) {
            TT.push_back({u, v , dis});
        }

        for (int i = head[u]; i != -1; i = e[i].next) { 
            int v = e[i].to;
            int val = e[i].val;
            if (!vis[v] && val < dist[v]) {
                dist[v] = val;
                pq.push({v, u, val}); 
                //下一次就要以v为顶点而不是u是顶点
            }
        }
    }
}
void print(){
    for (auto edge : TT) {
            cout << edge.u << " " << edge.v << " " << edge.val << "\n";
        }
}
signed main() {
    ios;

    bd();
    Prim();
    //刚好n个点
    if (cnt == n) {
        cout << sum << "\n";
    } 
    else 
        cout << "NO MST";
    return 0;
}
```

## Kruskal:边集数组and Sort

排序后加边 直到n-1条边

通过并查集合并边,无所谓是否按照秩合并

```cpp
#include <cstdio>
#include <queue>
#include <iostream>
#include <cstring>
#include <vector>
#include <algorithm>
#define ep emplace_back 

#define int long long  
#define ios std::ios::sync_with_stdio(false);std::cin.tie(0); 
#define vec vector 
const int N = 2e6+9;
using namespace std;
struct node{
    int u,v,val;
};
int n,m;
int cnt=0;
int ans=0;
node e[N];
int fa[N];
vec<node>mst;
void bd(){
    cin>>n>>m;
    for(int i=1;  i<=n ;++i ) fa[i] = i;
    for(int i=1 ; i<=m ;  ++i){
        int u,v,val;
        cin>>u>>v>>val;
        e[i] = {u,v,val};
    }
}
int find(int x){
    while(x!=fa[x]){
        x=fa[x];
    }
    return x;
}
bool cmp(node a,node b){
    return a.val<b.val;
}
void Kruskal(){
    sort(e+1,e+1+m,cmp);
    for(int i=1 ; i<=m;++i){
        int u = find(e[i].u);
        int v = find(e[i].v);
        if(u!=v){
            //u是v的父亲
            fa[v]= u;
            ans+=e[i].val;
            ++cnt;
            mst.push_back(e[i]);
            if(cnt==n-1) break;
        }
    }
}
void print(){
    for(auto x:mst){
        cout<<x.u<<" "<<x.v<<" "<<x.val;
        cout<<"\n";
    }
}
signed main(){
    ios;
    bd();
    Kruskal();
    if(cnt==n-1) cout<<ans;
    else cout<<"orz";
    return 0;
}
```

