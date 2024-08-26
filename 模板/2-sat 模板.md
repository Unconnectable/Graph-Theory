# 2-Sat

$$
\begin{align*}
&\LARGE\color{Red}大意:\\
&有n个数a_i,m个约束条件都需要满足\\
&条件形如(i,a,j,b) \quad a_i=a \or a_j=b\\\\\\
&\LARGE\color{Red}思路:\\
&让a_i表示0,a_{i+n}表示1\\
&转换条件表达式成:\\
&a_i =a \ \or a_j=b   \\
&case1:a_i=a,a_j\not=b\\
&case2:a_j=b,a_i\not=a\\
&case1:\ 连接(i+a*n,j+!b*n)\\
&case2:\ 连接(j+b*n,a+!a*n)\\
&然后跑Tarjan判断就好 
\end{align*}
$$





如果i和i+n在一个scc里面肯定不行  因为a_i只能为1或者0，不能同时为1或0

```cpp

/**/
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
const int INF = 0x7FFFFFFF; //2147483647

const int inf1 = 0x3f3f3f3f; //1061109567
const int inf2 = 0x7f7f7f7f; //2139062143 memset赋值用

using namespace std;
int head[N],idx=0;
struct node{
    int to,val,next;
}e[N<<1];
int scc[N],scc_cnt;
int n,m;
bool vis[N];
int low[N],dfn[N];
void add(int u,int v,int val){
    e[idx] = {v,val,head[u]};
    head[u] = idx++;
}
int time__=0;
stack<int>st;
void tarjan(int u){
    dfn[u] = low[u] = ++time__;
    vis[u] = 1;
    st.push(u);
    for(int i=head[u] ; i!=-1 ; i=e[i].next){
        int v = e[i].to;
        if(!dfn[v]){
            tarjan(v);
            low[u] = min(low[u],low[v]);
        }
        else if(vis[v]){
            low[u] = min(low[u] , dfn[v]);
        }
    }
    if(low[u] ==dfn[u]){
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
void bd(){
    cin>>n>>m;
    memset(head,-1,sizeof head);
    for(int k=1 ; k<=m ; ++k){
        int i,a,j,b;
        cin>>i>>a>>j>>b;
        //a_i:0
        //a_i:1
        add(i + a * n, j + !b * n, 0);       
        add(j + b * n, i + !a * n, 0);
    }
}
signed main(){
    ios;

    bd();
    for(int i=1 ; i<=2*n ; ++i)
        if(!dfn[i])
            tarjan(i);
    bool ok =1;//是否有解
    for(int i=1 ; i<=n ; ++i){
        if(scc[i] == scc[i+n]){
            ok=0;
            break;
        }
    }
    //如果 i和i+n在同一个scc里面 就无解 
    //否则输出方案
    if(ok){
        cout<<"POSSIBLE"<<"\n";
        for(int i=1 ; i<=n;++i){
           cout << (scc[i] > scc[i + n] ? 0 : 1) << " ";
        }
    }
    else {
        cout<<"IMPOSSIBLE";
    }
    return 0;
}

```

