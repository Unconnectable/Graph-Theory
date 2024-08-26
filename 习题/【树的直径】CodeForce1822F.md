# [CF1822F-Problem - F - Codeforces](https://codeforces.com/contest/1822/problem/F)

## 题目大意:无根树的每条边为k,定义操作：移动根节点为把当前的根ROOT移动到相邻节点,每次代价为c，

### 定义成本=从ROOT出发到达的最长的路径的长度，利润=成本-代价，求利润最大值

$$
\begin{align}
&\huge\color{red}记得开\text{longlong}\\\\\\
& \huge思路\\

& 1.建图 默认根\text{Root}=1,跑一遍dfs 记录每个点到\text{Root}的距离\text{dist}_1{i}\\
& 距离\text{Root}最远的点是c_1 \\
& 如果k\le c,不移动边，因为移动后ans减小 \\
& 否则:以c_1为\text{Root}跑dfs,记\text{dist}_2{i}是到c_1的距离，距离c1最远的点是c_2\\
& 以c_2为\text{Root}跑dfs，记\text{dist}_3{i}是到c_2的距离 \\
& 此时c_1,c_2就是树的直径\\
& 如果可以移动，必然是移动到某个直径\\
&此时的的成本W=\text{max}(\text{dist}_2{i},\text{dist}_3{i}) \times k,代价就是从\text{root}=1到现在的节点的偏移量\text{dist}_1{i}\\
&因此答案Ans = max(Ans,W-c\times\text{dist}_1{i})\\\\\\
&\huge\color{red}记得开\text{longlong}
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
const int N = 2e5+9;
const int INF = 0x7FFFFFFF; //2147483647

const int inf1 = 0x3f3f3f3f; //1061109567
const int inf2 = 0x7f7f7f7f; //2139062143 memset赋值用

using namespace std;
int head[N],idx=0;
bool vis[N];
struct node{
    int to,val,next;
};
node e[N<<1];
int n,m,k,c;
int c1,c2,c3,c4;
int dist1[N],dist2[N],dist3[N];
void add(int u,int v,int val){
    e[idx] = {v,val,head[u]};
    head[u] = idx++;
}

void clear9(){
    memset(head,-1,sizeof(head));
    memset(dist1,0,sizeof(dist1));
    memset(dist2,0,sizeof(dist2));
    memset(dist3,0,sizeof(dist3));
    idx=0;
    c1=c2=c3=c=0;
}
void bd(){
    cin>>n>>k>>c;
    for(int i=1 ; i<=n-1 ; ++i){
        int u,v;
        cin>>u>>v;
        add(u,v,1);
        add(v,u,1);
    }
}

void dfs1(int u,int fa){
    for(int i=head[u]; i!=-1 ; i=e[i].next){
        int v = e[i].to;
        if(v!=fa){
            dist1[v] = dist1[u] +1;
            if(dist1[v] > dist1[c1])
                c1=v;
                dfs1(v,u);
        }
    }
}
void dfs2(int u,int fa){
    for(int i=head[u]; i!=-1 ; i=e[i].next){
        int v = e[i].to;
        if(v!=fa){
            dist2[v] = dist2[u] +1;
            if(dist2[v] > dist2[c1])
                c2=v;
                dfs2(v,u);
        }
    }
}
void dfs3(int u,int fa){
    for(int i=head[u]; i!=-1 ; i=e[i].next){
        int v =e[i].to;
        if(v!=fa){
            dist3[v] = dist3[u] +1;
            if(dist3[v] > dist3[c1])
                c3=v;
                dfs3(v,u);
        }
    }
}
void solve(){
    clear9();
    bd();
    dfs1(1,0);
    dfs2(c1,0);
    dfs3(c2,0);
    //三次dfs
    //开longlong可以用 ans去乘以1ll
    lld ans=0;
    if(k<c){
        ans=(lld)dist1[c1]*k;
    }
    else{
        for(int i=1;  i<=n;++i){
            int dis=max(dist2[i],dist3[i]);
            ans = max(ans,(lld)k*(dis)-(lld)c*dist1[i]);
    }
    }
    cout<<ans<<"\n";
};


int main(){
    ios;
    int T=1;
    cin>>T;
    while(T--){
        solve();
    }
    return 0;
}
```

