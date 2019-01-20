###  problem A
简单的贪心问题，每一步合并两个质量最小的即可，类似于哈夫曼树
```cpp
#include <cstdio>
#include <cmath>
#include <algorithm>
using namespace std;
int main()
{
    int n;
    int a[117];
    while(~scanf("%d",&n))
    {
        for(int i = 0; i < n; i++)
        {
            scanf("%d",&a[i]);
        }
        sort(a,a+n);
        double ans = a[n-1];
        for(int i = n-2; i >= 0; i--)
        {
            ans = 2*sqrt(ans*a[i]);
        }
        printf("%.3lf\n",ans);
    }
    return 0;
}
```
### Problem B
最简单的数塔问题 这里就不讲了

### Problem C
01背包入门题：(取或不取思想）
转态转移方程：
f[i][v] = max ( f[i-1][v],f[i-1][v-c[i] ]+w[i] )
f[i][v]:前 i 件物品放入容量为v的背包获得最大价值。
c[i]: 第 i 件物品的体积。
w[i] :第 i 件物品的体积价值。
```cpp
#include <bits/stdc++.h>
using namespace std;
const int maxn=1050;
int w[maxn];
int val[maxn];
int dp[maxn][maxn];
int main()
{
    int T,n,V;
    cin>>T;
    while(T--)
    {
        cin>>n>>V;
        memset(dp,0,sizeof(dp));
        for(int i=1;i<=n;i++)
            cin>>val[i];
        for(int i=1;i<=n;i++)
            cin>>w[i];
 
        for(int i=1;i<=n;i++)
        {
            for(int j=0;j<=V;j++)
            {
                if(j>=w[i])
                    dp[i][j]=max(dp[i-1][j],dp[i-1][j-w[i]]+val[i]);
                else
                    dp[i][j]=dp[i-1][j];
            }
        }
        cout<<dp[n][V]<<endl;
 
    }
    return 0;
}
```
### Problem D
LCS最长公共子序列问题<br>
转移方程: <br>
dp[i+1][j+1] = max(dp[i][j]+1,dp[i][j+1]) 相同的时候
dp[i+1][j+1] = max(dp[i][j+1],dp[i+1][j]) 不同的时候
```cpp
#include<bits/stdc++.h>
using namespace std;
#define MAXN 10010
int dp[MAXN][MAXN];
int main()
{
    char a[MAXN],b[MAXN];
    while(cin>>a>>b)
    {
        int i,j;
        int n=strlen(a),m=strlen(b);
        for(i=0; i<n; ++i)
            for(j=0; j<m; ++j)
                if(a[i]==b[j])
                    dp[i+1][j+1]=dp[i][j]+1;
                else
                    dp[i+1][j+1]=max(dp[i][j+1],dp[i+1][j]);
        cout<<dp[n][m]<<endl;
    }
    return 0;
}

```
