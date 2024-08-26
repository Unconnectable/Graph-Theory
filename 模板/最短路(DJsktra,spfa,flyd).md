# 最短路

## **弗洛伊德：全源最短路:**

$$
\Large DP方程:\\
dp[i][j] = min(dp[i] [j],dp[i][k]+dp[k] [j])
$$

```cpp
#include <cstdio>
#include <algorithm>
#include <iostream>
#include <cstring>
#define int long long
#define ios std::ios::sync_with_stdio(false);std::cin.tie(0); 

const int INF = 1e18; // Use a large value suitable for long long
const int N = 505;    // Define N according to problem constraints

using namespace std;

int n, m,src;
int dp[N][N];

void bd() {
    cin >> n >> m;
    for (int i = 1; i <= n; ++i)
        for (int j = 1; j <= n; ++j)
            dp[i][j] = INF,dp[i][i]=0; // Initialize self-loops to 0

            
    for (int i = 1; i <= m; ++i) {
        int u, v, val;
        cin >> u >> v >> val;
        dp[u][v] = val;
        dp[v][u] = val; // Assuming undirected graph
    }
}

void flyd() {
    for (int i = 1; i <= n; ++i) {
        for (int k = 1; k <= n; ++k) {
            for (int j = 1; j <= n; ++j) {
                if (dp[i][k] != INF && dp[k][j] != INF) {
                    dp[i][j] = min(dp[i][j], dp[i][k] + dp[k][j]);
                }
            }
        }
    }
}

bool check() {
    //检查负环
    int src=1;
    for (int i = 1; i <= n; ++i) {
        if (dp[src][i] < 0) 
            return true; // Negative cycle detected
    }
    return false;
}

signed main() {
    ios;
    bd();
    flyd();
    
    if (check()) 
        cout << "Negative cycle";
    else 
        cout << "No negative cycle" << endl;
    
    return 0;
}
```



## 2.Bellman-fold

```cpp
#include <cstdio>
#include <queue>
#include <algorithm>
#include <iostream>
#include <cstring>
#include <vector>
#define ep emplace_back 

#define int long long 
#define ios std::ios::sync_with_stdio(false);std::cin.tie(0); 
const int N = 2e5+9;
const int inf2 = 0x7f7f7f7f; //2139062143 memset赋值用

using namespace std;


int n, m, src;
struct node {
    int to, val, next;
};
node e[N];
int head[N], idx = 0;
int dist[N];
bool vis[N];  // Added vis array
queue<int> q;

void add(int u, int v, int val) {
    e[idx] = {v, val, head[u]};
    head[u] = idx++;
}

void bd() {
    cin >> n >> m >> src;
    memset(head, -1, sizeof(head));
    memset(dist,inf2,sizeof dist);
    for(int i = 1; i <= m; ++i) {  // Changed from N to m
        int u, v, val;
        cin >> u >> v >> val;
        add(u, v, val);
    }
}
void bellman_Ford(){
    dist[src]=0;
    for(int i=1;i<=n-1;++i){
        for(int u=1;u<=n;++u){
            for(int j=head[u] ; ~j ; j=e[j].next){
                int v = e[j].to;
                int val = e[j].val;
                if(dist[v] > dist[u] + val){
                    dist[v] = dist[u]+val;
                }
            }
        }
    }
}
bool check(){
    for(int u=1 ; u<=n ; ++u){
        for(int i =head[u] ; i!=-1 ; i=e[i].next){
            int v =e[i].to;
            int val = e[i].val;
            if(dist[v] > dist[u] + val){
                return true;
               //存在负权环 
            }
        }
    }
    return false;
}
signed main(){
    ios;
    bd();
    bellman_Ford();
    for(int i=1 ; i<=n ;++i) cout<<dist[i]<<" ";
    return 0;
}
```



## 3.spfa

```cpp
#include <cstdio>
#include <queue>
#include <iostream>
#include <cstring>
#include <algorithm>

#define ios std::ios::sync_with_stdio(false);std::cin.tie(0); 
using namespace std;

#define INF 0x7f7f7f7f
const int N = 2e6 + 9;

int n, m, src;
struct node {
    int to, val, next;
};
node e[N];
int head[N], idx = 0;
int dist[N];
bool vis[N];  // Added vis array
queue<int> q;

void add(int u, int v, int val) {
    e[idx] = {v, val, head[u]};
    head[u] = idx++;
}

void bd() {
    cin >> n >> m >> src;
    memset(head, -1, sizeof(head));
    memset(dist,INF,sizeof dist);
    for(int i = 1; i <= m; ++i) {  // Changed from N to m
        int u, v, val;
        cin >> u >> v >> val;
        add(u, v, val);
    }
}

void spfa() {
    dist[src] = 0;
    q.push(src);
    vis[src] = true;
    while (!q.empty()) {
        int u = q.front();
        q.pop();
        vis[u] = false;
        for (int i = head[u]; i != -1; i = e[i].next) {
            int v = e[i].to;
            int val = e[i].val;
            if (dist[u] + val < dist[v]) {
                dist[v] = dist[u] + val;
                if (!vis[v]) {
                    q.push(v);
                    vis[v] = true;
                }
            }
        }
    }
}
bool check(){
    
}
void print() {
    for (int i = 1; i <= n; ++i) {
        cout << dist[i] << " ";
    }
    cout << endl;  // Optionally add a newline at the end of the output
}

signed main() {
    ios;
    bd();
    spfa();
    print();  // Call the print function to display results
    return 0;  // Add return statement for main
}

```





## 4.Djstra

### 朴素版

```cpp
#include <cstdio>
#include <queue>
#include <deque>
#include <algorithm>
#include <iostream>
#include <cstring>
#include <vector>

#define int long long 
#define ios std::ios::sync_with_stdio(false);std::cin.tie(0); 
const int N = 2e6+9;
const int inf2 = 0x7f7f7f7f; //2139062143 memset赋值用

using namespace std;

int n,m,src;
int head[N],idx=0;
int dist[N];
bool vis[N];
struct node{
    int to,val,next;
}e[N<<1];
void add(int u,int v,int val){
    e[idx] = {v,val,head[u]};
    head[u] = idx++;
}
void bd(){
    cin>>n>>m>>src;
    memset(head,-1,sizeof head);
    memset(dist,inf2,sizeof dist);
    for(int i=1; i<=m; ++i){
        int u,v,val;
        cin>>u>>v>>val;
        add(u,v,val);
    }
}
void dj(){
    dist[src]=0;
    for (int i = 1; i <= n; ++i) {
        int u = -1;
        for (int j = 1; j <= n; ++j) {
            if (!vis[j] && (u == -1 || dist[j] < dist[u])) {
                u = j;
            }
        }
        
        if (dist[u] == inf2) break;
        vis[u] = true;

        for (int i = head[u]; i != -1; i = e[i].next) {
            int v = e[i].to;
            int value = e[i].val;
            dist[v] = min(dist[v],dist[u]+value);
        }
    }
}
signed main(){
    ios;
    bd();
    dj();
    for(int i=1 ;i<=n ;++i )
            cout<<dist[i]<<" ";

    return 0;
}
```



### 二叉堆优化

```cpp
#include <cstdio>
#include <queue>
#include <deque>
#include <algorithm>
#include <iostream>
#include <cstring>
#include <vector>

#define int long long 
#define ios std::ios::sync_with_stdio(false);std::cin.tie(0); 
const int N = 2e6+9;
const int inf2 = 0x7f7f7f7f; //2139062143 memset赋值用

using namespace std;

int n,m,src;
int head[N],idx=0;
int dist[N];
bool vis[N];
struct node{
    int to,val,next;
}e[N<<1];
void add(int u,int v,int val){
    e[idx] = {v,val,head[u]};
    head[u] = idx++;
}
struct edge{
    long long  u,dis;
};
bool operator <(edge a,edge b){
    return a.dis>b.dis;
}
priority_queue<edge>pq;
void bd(){
    cin>>n>>m>>src;
    memset(head,-1,sizeof head);
    memset(dist,inf2,sizeof dist);
    for(int i=1; i<=m; ++i){
        int u,v,val;
        cin>>u>>v>>val;
        add(u,v,val);
    }
}
void dj(){
    dist[src]=0;
    pq.push({src,dist[src]});
    while(pq.size()){
        int u = pq.top().u;
        pq.pop();
        if(vis[u]) continue;
        vis[u]=1;
        for(int i=head[u] ; i!=-1 ;i=e[i].next){
            int v = e[i].to;
            int val = e[i].val;
            if(dist[v] > dist[u] + val){
                dist[v] = dist[u] + val;
                pq.push(edge{v,dist[v]});
            }
        }
    }
}
signed main(){
    ios;
    bd();
    dj();
    for(int i=1 ;i<=n ;++i ) cout<<dist[i]<<" ";
    return 0;
}
```

