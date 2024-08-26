# [1583Problem - B - Codeforces](https://codeforces.com/contest/1583/problem/B)

### 题目大意:n个点的无根树 给出m个限制条件  (a,c,b)在a到b路径上不能存在c点,求任意一种可能的树的所有边

#### ***注意数据范围:1<m<n<1e5***

#### 这说明了***最多有n-1个限制条件*** 这说明至少有一个点不存在限制条件  即这个点可以作为根节点root连接其他所有点形成边

#### 思路:记录可以作为根节点root的点  然后for一遍 输出即可

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
int tag[N];
int n,m;
void solve(){
    
    memset(tag,0,sizeof(tag));
    cin>>n>>m;
    for(int i=1 ; i<=m ; ++i){
        int a,b,c;
        cin>>a>>c>>b;
        tag[c]=1;
    }
    int tag__=0;
    //tag__就是根节点root
    for(int i = 1; i <= n; ++i){
        if( tag[i] ==0 ){
            tag__=i;
            break;
        }
    }
    for(int i = 1; i <= n; ++i){
        if(i != tag__)
            printf("%d %d \n",i,tag__);
    }
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

