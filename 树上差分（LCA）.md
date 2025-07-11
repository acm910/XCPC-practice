# 题目

松鼠的新家是一棵树，前几天刚刚装修了新家，新家有 *n* 个房间，并且有 *n*−1 根树枝连接，每个房间都可以相互到达，且俩个房间之间的路线都是唯一的。天哪，他居然真的住在“树”上。

松鼠想邀请小熊维尼前来参观，并且还指定一份参观指南，他希望维尼能够按照他的指南顺序，先去 *a*1，再去 *a*2，……，最后到 *a**n*，去参观新家。可是这样会导致重复走很多房间，懒惰的维尼不停地推辞。可是松鼠告诉他，每走到一个房间，他就可以从房间拿一块糖果吃。

维尼是个馋家伙，立马就答应了。现在松鼠希望知道为了保证维尼有糖果吃，他需要在每一个房间各放至少多少个糖果。

因为松鼠参观指南上的最后一个房间 *a**n* 是餐厅，餐厅里他准备了丰盛的大餐，所以当维尼在参观的最后到达餐厅时就不需要再拿糖果吃了。

## 输入格式

第一行一个正整数 *n*，表示房间个数第二行 *n* 个正整数，依次描述 *a*1,*a*2,⋯,*a**n*。

接下来 *n*−1 行，每行两个正整数 *x*,*y*，表示标号 *x* 和 *y* 的两个房间之间有树枝相连。

## 输出格式

一共 *n* 行，第 *i* 行输出标号为 *i* 的房间至少需要放多少个糖果，才能让维尼有糖果吃。

## 输入输出样例

**输入 #1**

```
5
1 4 5 3 2
1 2
2 4
2 3
4 5
```

**输出 #1**

```
1
2
1
2
1
```

## 说明/提示

对于全部的数据，2≤*n*≤3×105，1≤*a**i*≤*n*。

```` C++

#include <bits/stdc++.h>


using namespace std;

typedef long long LL;
typedef pair<int, int> PII;
typedef unsigned long long ULL;
typedef pair<double, double> PDD;

const int MAX = (int) 3e5 + 5;
const int LOG = 23;

vector<int> graph[MAX];
int arr[MAX];
int pa[MAX][LOG];
int depth[MAX];
int suf[MAX];
int ans[MAX];

void dfs(int x, int fa, int dep) {

    depth[x] = dep;
    pa[x][0] = fa;
    auto children = graph[x];
    for (int y: children) {
        if (fa == y) continue;
        dfs(y, x, dep + 1);
    }

}

int dfs2(int x,int fa){
    vector<int> children = graph[x];
    if(children.size() == 1 && children[0] == fa){
        return ans[x] = suf[x];
    }
    int res =0 ;
    for(int y:children){

        if(fa == y) continue;
        res += dfs2(y,x);
    }
    return ans[x] = res + suf[x];
}

void solve() {
    int n;
    cin >> n;
    for (int i = 1; i <= n; i++) {
        graph[i].clear();
        arr[i] = 0;
        depth[i] = 0;
        ans[i] = 0;
        suf[i] = 0;
        for (int j = 0; j < LOG; j++) {
            pa[i][j] = -1;
        }
    }
    for (int i = 1; i <= n; i++) {
        cin >> arr[i];
    }
    for (int i = 0; i < n - 1; i++) {
        int x, y;
        cin >> x >> y;
        graph[x].push_back(y);
        graph[y].push_back(x);
    }

    dfs(1, -1, 0);
    for (int j = 1; j < LOG; j++) {
        for (int i = 1; i <= n; i++) {
            if(pa[i][j-1] != -1)
                pa[i][j] = pa[pa[i][j - 1]][j - 1];
        }
    }
    auto lca = [&](int x, int y) -> int {

        if (depth[x] > depth[y]) {
            int temp = x;
            x = y;
            y = temp;
        }
        for (int i = LOG - 1; i >= 0; i--) {
            if (depth[x] == depth[y]) break;
            if (pa[y][i] == -1) continue;
            if (depth[pa[y][i]] < depth[x]) continue;
            y = pa[y][i];
        }
        if(x == y) return x;
        for (int i = LOG-1; i >= 0; i--) {
            if(pa[x][i] == -1) continue;
            if(pa[x][i] == pa[y][i]) continue;
            x = pa[x][i];
            y = pa[y][i];
        }
        return pa[x][0];
    };


   for(int i = 2;i<=n;i++){
       int from = arr[i-1];
       int to = arr[i];

       int com = lca(from,to);
       suf[from]++;
       suf[to]++;
       suf[com]--;
       if(pa[com][0] != -1 ) suf[pa[com][0]]--;
   }
   dfs2(1,-1);
   for(int i = 2;i<=n;i++){
       ans[arr[i]]--;
   }
   for(int i =1; i <= n ;i++){
       cout<<ans[i]<<"\n";
   }


}

int main() {
    solve();
}
````

