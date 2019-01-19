# JNU_ACM Day1下午 题解

## Problem1 DP入门题(数塔)
由下往上推，最终到一个终点，只需要算出由下往上得到的最大价值即可
```cpp
#include <iostream>
#include <cstring>
#include <string>
#include <cstdio>
#include <map>
#include <queue>
#include <algorithm>  
using namespace std;
typedef long long ll;
const int maxn = 100+5;
int dp[maxn][maxn];
int main()
{
	int t;
	cin >> t;
	while (t--)
	{
		int n;
		cin >> n;
		for (int i = 1; i <= n; i++)
			for (int j = 1; j <= i; j++)
				cin >> dp[i][j];
		for (int i = n-1; i >= 1; i--)
			for (int j = 1; j <= n-1; j++)
				dp[i][j] += max(dp[i + 1][j], dp[i + 1][j + 1]);
		printf("%d\n", dp[1][1]);
	}
	return 0;
}
```


## Problem2  从里向外推区间(区间DP)
 * 给一串数字,每次可以取最左边或者最右边,然后乘以对应取的次序,
 * 问和最大是多少
 * 状态  dp[i,j]: 剩下第i个至第j个物品时，取掉剩下的所有物品能获得的最大值 
 * 转移  dp[i,j] = `max(dp[i+1,j]+v[i]*(n-(j-i+1)+1), dp[i,j-1]+v[j]*(n-(j-i+1)+1));`
 * 边界  dp[i][i]=a[i]*n
```cpp
#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <string>
#include <cmath>
#include <algorithm>
#include <map>
#include <vector>
#include <queue>
#include <stack>
#include <set>
using namespace std;
const int maxn = 2005;
int a[maxn];
int dp[maxn][maxn];
/*
 * 给一个序列,每次可以从头取或者从尾巴取,
 * sum += k*a[i] ; k是第几次取,问sum的最大
 *
 * 同样的,不能按照一个固定的方向来进行DP,这里序列是线性的,
 * 则按照区间来进行DP
 * dp[i,j]: i到j的最优解, 长度j-i+1
 * dp[i,j] = max(dp[i+1][j]+a[i]*(n-len+1), dp[i][j-1]+a[j]*(n-len+1));
 */
int main()
{
    freopen("in","r",stdin);
    int n;cin>>n;
    for (int i=0;i<n;i++)
    {
        scanf("%d",&a[i]);
        dp[i][i]=a[i]*n;
    }
    //从距离小扩展到距离大
    for (int len=1;len<n;len++)
    {
        for (int i=0;i+len<n;i++)
        {
            int j = i+len;
            dp[i][j] = max(dp[i+1][j]+a[i]*(n-len), dp[i][j-1]+a[j]*(n-len));
        }
    }
    cout << dp[0][n-1];
    return 0;
}
```

## Problem3  简单二维DP
```cpp
#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <string>
#include <cmath>
#include <algorithm>
#include <map>
#include <vector>
#include <queue>
#include <stack>
#include <set>
using namespace std;
/*
 * 给n*n矩阵,问最大对称子矩阵是多大(沿着左下角到右上角对称)
 *
 * 先转换为主对角线矩阵
 * 然后从右下角开始往左上角
 * dp[i][j] = dp[i][j]+cnt;  cnt是dp[i][j]沿着对角线向右下角能走多远.
 * 当超过dp[i+1][j+1]或者超过矩阵,就退出,
 */
const int maxn = 1005;
char matrix[maxn][maxn];
int dp[maxn][maxn];
int main()
{
    freopen("in","r",stdin);
    int n;
    while(scanf("%d",&n) && n)
    {
        for (int i=n-1;i>=0;i--) //换成主对角.方便些
            scanf("%s",matrix[i]);
        for (int i=0;i<=n;i++)
            for (int j=0;j<=n;j++)
                dp[i][j]=1;

        for (int i=n-1;i>=0;i--)
        {
            for (int j=n-1;j>=0;j--)
            {
                bool flag=true;
                int cnt=0;
                for (int rr=1;rr<=dp[i+1][j+1];rr++)
                {
                    if (i+rr>=n || j+rr>=n)
                        break;
                    if (matrix[i][j+rr] != matrix[i+rr][j])
                    {
                        break;
                    }
                    cnt++;
                }
                dp[i][j]=dp[i][j]+cnt;
            }
        }
        int ans=0;
        for (int i=0;i<n;i++)
            for (int j=0;j<n;j++)
                ans = max(ans,dp[i][j]);
        cout << ans << endl;
    }
    return 0;
}
```

## Problem4 DP与记忆化搜索
```cpp
/*
 * n*n矩阵,吃奶酪
 * 走四个方向,每次最多走k步
 * 且要求走的地方的奶酪是递增的,问最多能迟到多少奶酪
 * 
 * 明显,直接DP,虽然子问题重复,但是不满足无后效性,因为每次移动的方向不是固定的,而且四个方向,所以用记忆花搜搜
 * 这题要DP的话,先将所有点按权值排列,这样找到一个方向后,可以进行DP
 */
const int maxn = 100+5;
int mp[maxn][maxn],dp[maxn][maxn];
int n,k;
struct Node{
    int i,j;
    int val;
    bool operator <(const Node& mm) const{
        return val<mm.val;
    }
}node[maxn*maxn];
int main()
{
    freopen("in","r",stdin);
    while(scanf("%d%d",&n,&k) && n!=-1)
    {
        int tot=0;
        for (int i=0;i<n;i++)
        {
            for (int j=0;j<n;j++)
            {
                scanf("%d",&mp[i][j]);
                if (i==0 && j==0)
                    continue;
                node[tot].i=i; node[tot].j=j; node[tot++].val=mp[i][j];
            }
        }
        sort(node,node+tot);
        memset(dp,-1,sizeof(dp));
        dp[0][0]=mp[0][0];
        int ans = dp[0][0];
        for (int tt=0;tt<tot;tt++) //找到范围内比他小的点
        {
            //自底向上更新点(i,j)
            int i=node[tt].i, j=node[tt].j;
            for (int rr=max(0,i-k);rr<=min(n-1,i+k);rr++)
            {
                if (mp[rr][j]>=mp[i][j]) continue;
                if (dp[rr][j]==-1) continue;
                dp[i][j] = max(dp[i][j], dp[rr][j]+mp[i][j]);
            }
            for (int cc=max(0,j-k);cc<=min(n-1,j+k);cc++)
            {
                if (mp[i][cc]>=mp[i][j]) continue;
                if (dp[i][cc]==-1) continue;
                dp[i][j] = max(dp[i][j], dp[i][cc]+mp[i][j]);
            }
            ans = max(ans,dp[i][j]);
        }
        cout << ans << endl;
    }
    return 0;
}
```

## Problem5  3*nDP
```cpp
#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <string>
#include <algorithm>
using namespace std;
const int maxn = 2000+5;
const int INF = 0x3f3f3f3f;
int dp[maxn];
/*
 * dp[i]:前i个人最小时间(i{1..2000})
 * dp[1]=s[1];  dp[2]=min(s[1]+s[2], d[1]); dp[i]=INF
 * dp[i]=min(dp[i-2]+d[i-1],dp[i-1]+s[i])
 *
 */
int s[maxn],d[maxn];//single,double

void out(int tt)
{
    int hh=8,mm=0,ss=0;
    ss = tt;
    mm = ss/60; ss%=60;
    hh += mm/60; mm%=60;
    hh%=12;

    if (hh<10) printf("0%d:",hh);
    else printf("%d:",hh);
    if (mm<10) printf("0%d:",mm);
    else printf("%d:",mm);
    if (ss<10) printf("0%d",ss);
    else printf("%d",ss);

    if (hh>=12)
        printf(" pm\n");
    else printf(" am\n");
}
int main()
{
    freopen("in","r",stdin);
    int t;
    cin >> t;
    while(t--)
    {
        int n;
        scanf("%d",&n);
        for (int i=1;i<=n;i++)
        {
            scanf("%d",&s[i]);
        }
        for (int i=1;i<=n-1;i++)
        {
            scanf("%d",&d[i]);
        }
        memset(dp,INF,sizeof(dp));
        dp[1]=s[1]; dp[2]=min(s[1]+s[2], d[1]);
        for (int i=3;i<=n;i++)
            dp[i]=min(dp[i-1]+s[i],dp[i-2]+d[i-1]);
        out(dp[n]);
    }
    return 0;
}
```

## Problem6 优先队列 + 贪心算法
```cpp
#include <iostream>
#include <cstring>
#include <cstdio>
#include <queue>
#include <functional> //greater
#include <algorithm>
using namespace std;
typedef long long ll;
const int maxn = 10000 + 5;
int n;  //加油站数
int l, p; //距离,已有油量
struct point {
	int x;
	int v;
}s[maxn];//油站地点和油量
bool cmp(point s1, point s2)
{
	return s1.x < s2.x;
}
void solve()
{
	//把最终的终点也当作油站
	s[n].x = l; 
	s[n].v = 0;
	n++;
	//按距离从小到大排
	sort(s, s + n, cmp);
	priority_queue<int> pque;  //值大先取出来

	//ans:加油次数, pos:现在的位置, tank:油箱的油
	int ans = 0, pos = 0, tank = p;
	for (int i = 0; i < n; i++)
	{
		//下来要前进的距离
		int d = s[i].x - pos;
		//加油加到够用
		while (tank - d < 0)
		{
			if (pque.empty())
			{
				puts("-1");
				return;
			}
			tank += pque.top();
			pque.pop();
			ans++;
		}
		tank -= d;
		pos = s[i].x;
		pque.push(s[i].v);
	}
	printf("%d\n", ans);
}
int main()
{
	scanf("%d", &n);
	for (int i = 0; i < n; i++)
		scanf("%d%d", &s[i].x, &s[i].v);
	scanf("%d%d", &l, &p);
	for (int i = 0; i < n; i++)
		s[i].x = l - s[i].x;
	solve();
	return 0;
}
```

## Problem7 图上DP
```cpp
#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <string>
#include <cmath>
#include <algorithm>
#include <map>
#include <vector>
#include <queue>
#include <stack>
#include <set>
using namespace std;

/*
 * 给n个点,n-1条边
 * 点权是物品价值,边权是油费
 *
 * 要求找两个点,从一个点购入物品,另一个点卖出物品
 * 问最大收益
 *
 * 注意:图论的题要注意点从1开始标还是0开始标
 */
const int maxn = 1e5+5;
int p[maxn];

struct edge{
    int u,v,w;
}E[maxn];

void addedge(int u,int v,int w,int tot)
{
    E[tot].u=u;E[tot].v=v;E[tot].w=w;
}
int dp[maxn];
int main()
{
    freopen("in8","r",stdin);
    int t;cin>>t;
    while(t--)
    {
        int n;scanf("%d",&n);
        for (int i=1;i<=n;i++) scanf("%d",&p[i]);
        int tot = 0;
        for (int i=0;i<n-1;i++)
        {
            int u,v,w;
            scanf("%d%d%d",&u,&v,&w);
            if (p[u]>p[v]) swap(u,v); //建双向边或者只建单向边,从小指向大的,另一个方向的边不影响DP最优解
            addedge(u,v,w,tot);tot++;
            //addedge(v,u,w,tot);tot++;
        }
        memset(dp,0,sizeof(dp));
        for (int i=0;i<tot;i++)
        {
            dp[E[i].v] = max(dp[E[i].v],dp[E[i].u]+(p[E[i].v]-p[E[i].u])-E[i].w);
        }
        int ans = dp[1];
        for (int i=2;i<=n;i++)
            ans = max(ans,dp[i]);
        printf("%d\n",ans);
    }
    return 0;
}
```
