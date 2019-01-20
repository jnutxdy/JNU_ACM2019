### Problem A
数位DP
理解常用的数位DP模版
```cpp
#include <cstdio>
#include <cstring>
#include <iostream>
#include <algorithm>
using namespace std;
#define LL __int64
LL n, dp[25][3];
//dp[i][j]:长度为i，状态为j
int digit[25];
//nstatus: 0：不含49, 1：不含49但末尾是4, 2 :含49
LL DFS(int pos, int status, int limit)
{
    if(pos <= 0) // 如果到了已经枚举了最后一位，并且在枚举的过程中有49序列出现
        return status==2;//注意是 ==
    if(!limit && dp[pos][status]!=-1)   //对于有限制的询问我们是不能够记忆化的
        return dp[pos][status];
    LL ans = 0;
    int End = limit?digit[pos]:9;   // 确定这一位的上限是多少
    for(int i = 0; i <= End; i++)   // 每一位有这么多的选择
    {
        int nstatus = status;       // 有点else s = statu 的意思
 
        if(status==0 && i==4)//高位不含49，并且末尾不是4 ，现在末尾添4返回1状态
            nstatus = 1;
        else if(status==1 && i!=4 && i!=9)//高位不含49，且末尾是4，现在末尾添加的不是4返回0状态
            nstatus = 0;
        else if(status==1 && i==9)//高位不含49，且末尾是4，现在末尾添加9返回2状态
            nstatus = 2;
        ans+=DFS(pos-1, nstatus, limit && i==End);
    }
    if(!limit)
        dp[pos][status]=ans;
    return ans;
}
 
int cal(LL x)
{
    int cnt = 0;
    while(x)
    {
        digit[++cnt] = x%10;
        x/=10;
    }
    digit[cnt+1] = 0;
    return cnt;
}
 
int main()
{
    int t;
    scanf("%d",&t);
    while(t--)
    {
        memset(dp,-1,sizeof(dp));
        scanf("%I64d",&n);
        int len = cal(n);
        LL ans = DFS(len, 0, 1);
        printf("%I64d\n",ans);
    }
    return 0;
}
```



### Problem B
完全背包的模版题目
```cpp
#include <string.h>
#include <stdio.h>
#include <algorithm>
using namespace std;
int dp[1000050];
const int INF = 10000000;
int main()
{
    int t;
    int wp,wt,W,n;
    int v[550],w[550];
    scanf("%d",&t);
    while(t--)
    {
        scanf("%d%d",&wt,&wp);
        W = wp-wt;//减去小猪本身重量
        scanf("%d",&n);
        for(int i = 0;i<n;i++)
        scanf("%d%d",&v[i],&w[i]);
        for(int i = 0;i<=W;i++)
            dp[i] = INF;
        dp[0] = 0;
        for(int i = 0;i<n;i++)
        {
            for(int j = w[i];j<=W;j++)
            {
                dp[j] = min(dp[j],dp[j-w[i]]+v[i]);
            }
        }
        if(dp[W] == INF)
        printf("This is impossible.\n");
        else
        printf("The minimum amount of money in the piggy-bank is %d.\n",dp[W]);
    }
 
    return 0;
}
```

### Problem C
题意：有n个人每个人都有一个权值v，根据他出场的顺序决定他的心情，比如第k个出场，坏的心情值就是（k-1）*v,有一个原理为栈的小黑屋，可以控制出场顺序。问怎么利用这个小黑屋使所有人心情值之和的最小是多少？ 
思路：首先贪心的想让权值最高的先出场一定最优的，但是这题人出场的顺序只能由一个栈来控制，不能贪心的决策。而栈起到的作用就是改变一定区间内人的出场顺序。 
所以这题就用区间dp来做，设dp[i][j]表示从第i个人到第j个人这段区间的最小花费（是只考虑这j-i+1个人，不需要考虑前面有多少人） 
那么对于dp[i][j]的第i个人，就有可能第1个上场，也可以第j-i+1个上场。 
考虑第K个上场，那么表示在i到j个人之间已经有k-1个人在第i个人之前先出场， 有状态dp[i+1][i+k-1],在第i个人后还有dp[i+k][j]这个转态，对于当前第i个人 a[i]（k-1）,对于第i个人后面上场的j-(i+k)个人来说每个人的都已经等待了k次,加上k(sum[j]-sum[i+k-1]);

```cpp
#include<bits/stdc++.h>
using namespace std;
const int inf=0x7fffffff;
int a[110],sum[110];
int dp[110][110];

int main(){
    int t;
    scanf("%d",&t);
    int cas=1;
    while(t--){
        int n;
        scanf("%d",&n);
        sum[0]=0;
        for(int i=1;i<=n;i++) {
            scanf("%d",&a[i]);
            sum[i]=sum[i-1]+a[i];
        }
        for(int l=1;l<n;l++){
            for(int i=1;i<=n;i++){
                int j=i+l;
                dp[i][j]=inf;
                for(int k=1;k<=j-i+1;k++){
                    dp[i][j]=min(dp[i][j],dp[i+1][i+k-1]+dp[i+k][j]+(k-1)*a[i]+k*(sum[j]-sum[i+k-1]));
                }
            }
        }
        printf("Case #%d: %d\n",cas++,dp[1][n]);
    }
    return 0;
}
```

### Problem D
题意：给定n个点的坐标，先问这些点是否能组成一个凸包，如果是凸包，问用不相交的线来切这个凸包使得凸包只由三角形组成，根据costi, j = |xi + xj| * |yi + yj| % p算切线的费用，问最少的切割费用。


### Problem E
题意： 统计区间 [a,b] 中不含 4 和 62 的数字有多少个。

思路：
状态: 
dp[len][0 / 1] 表示长度为 len 的数字不含 4 和 62 ，首位是否为2的个数。
转移方程:
dp[i][0] = 8 * dp[i-1][0] + dp[i-1][1] ,  
dp[i][1] = 7 * dp[i-1][0] + dp[i-1][1]。

#include <bits/stdc++.h>
using namespace std;
int dp[8][2],digit[8];
int dfs(int len,bool state,bool fp)
{
    if(!len)
        return 1;
    if(!fp && dp[len][state] != -1)
        return dp[len][state];
    int ret = 0 , fpmax = fp ? digit[len] : 9;
    for(int i=0;i<=fpmax;i++)
    {
        if(i == 4 || state && i == 2)
            continue;
        ret += dfs(len-1,i == 6,fp && i == fpmax);
    }
    if(!fp)
        dp[len][state] = ret;
    return ret;
}
int f(int n)
{
    int len = 0;
    while(n)
    {
        digit[++len] = n % 10;
        n /= 10;
    }
    return dfs(len,false,true);
}
int main()
{
    int a,b;
    memset(dp,-1,sizeof(dp));
    while(scanf("%d%d",&a,&b),a||b)
    {
        printf("%d\n",f(b)-f(a-1));
    }
    return 0;
}


### Problem F
题解: 
In this problem one should answer the query: how many beautiful numbers are there in the interval from 1 to R.
Clearly, to check whether a number is divisible by all its digits, it is sufficient to know the remainder of a division by the lcm of all possible digits (call this lcm M), that is M = 8 * 9 * 5 * 7 = 2520. The standart dynamic solution is supposed to maintain such state parameters: the length of the number, "strictly less" flag, current remainder of a division by M and the mask of already taken digits.
The first note: we can maitnain the lcm of the digits already taken, not the mask. This will decrease the number of different values of the last parameter (from 256 to 4 * 3 * 2 * 2 = 48, where 4 is the number of different powers of 2 etc).
Then, it is a good idea to pre-count transitions to the new parameters. But we wanted and tried to set such a time limit restriction, so that this solution would not be enough to avoid TL.
The idea that will decrease the running time even more lies in number theory. If we add digits from the end of a number we may see that the remainder of a number after division by 5 depends only on the last digit. Therefore, we may maintain the flag "last digit = 5 or 0" and ban transitions to the digit 5 if the flag is set to "false". Such an idea reduces the number of states by 5 * 2 / 2 = 5. This solution is fast enough to pass in any language, though there are even more powerful optimizations (the trick mentioned above can be done with digit 2 also).


题意:  如果一个数能够被组成它的各个非0数字整除，则称它是完美数。
例如：1-9都是完美数，10,11,12,101都是完美数，但是13就不是完美数（因为13不能被数字3整除）。现在给定正整数x,y，求x和y之间（包含x和y的闭区间）共有多少完美数。

题解: 
数位DP经典好题
首先根据同余定理: (a*10 + b)%2520 =  (a%2520*10 + b) % 2520, 即1-9的最小公倍数不会超过2520 (或者根据上面那个英文题解)
 dp[i][j][k] 表示到第i位对2520取模后为j,各位的lcm为k的数字个数。
因为19＊2520＊2520太大， 会mle， 所以可以优化下第三维，因为最小公倍数都是离散的， 所以可以hash记录下。
 #include <bits/stdc++.h>
using namespace std;
int num[20];
long long dp[20][2522][49];
int __lcm(int a, int b){
    return a/__gcd(a, b)*b;
}
int Index[2522];
void init(){
    int te = 1;
    for(int i=1; i<=2520; i++)
        if(2520%i == 0)
            Index[i] = te++;
}
long long dfs(int i, int p, int lcm, bool e) {
    if (i==-1) return p%lcm == 0;
    if (!e && ~dp[i][p][Index[lcm]]) return dp[i][p][Index[lcm]];
    long long res = 0;
    int u = e?num[i]:9;
    for (int d = 0; d <= u; ++d){
        int nowlcm = lcm;
        if(d) nowlcm = __lcm(nowlcm, d);
        res += dfs(i-1, (p*10+d)%2520, nowlcm,e&&d==u);
    }
    return e?res:dp[i][p][Index[lcm]] = res;
}
long long solve(long long x){
    int t = 0;
    while(x){
        num[t++] = x%10;
        x /= 10;
    }
    return dfs(t-1, 0, 1, true);
}
int main() {
	long long a, b;
    while(cin>>a>>b) {
    	init();
    memset(dp, -1, sizeof(dp));
       cout<<solve(b) - solve(a-1)<<endl;
    }
    return 0;
}



