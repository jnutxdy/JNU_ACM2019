
# Day1_tutorial1上午场题解
### 1000 Weiming's easy problem
任意相邻的两个数一定是互质的数，但是有一种情况就是当输入的数是2的时候，<br>
因为1是它的一个因子，不能减，所以其他的情况直接输出1当为2时输出2就可以了。

```cpp
#include<iostream>
#include<cstdio>
using namespace std;
int main()
{
    int n;
    cin>>n;
    if(n==2) printf("2\n");
    else printf("1\n");
    return 0;
}
```

### 1001
典型的签到题，n个气球分给k个人，要使得每个人都没有相同的气球，很简单，只要拥有的每种气球颜色数量不超过k就行了
```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
#define N 100050
int n,k;
int num[N];  
char str[N];  
bool flag=true;  
int main()
{  
    scanf("%d%d",&n,&k);  
    scanf("%s",str);  
    for(int i=0;i<strlen(st);i++) num[st[i]-'a']++;    
    for(int i=0;i<26;++i) if(num[i]>k) flag=false;    
    if(!flag) printf("NO");
    else printf("YES");
    return 0;  
}
```

### 1002 Weiming's balloons
分析：给你连续一段数字，一开始玩家1可以取一段连续的子串，他们的和要为奇数，然后玩家2取连续的子串，他们的和要为偶数，谁不能取谁就输了。输出赢家。<br>
思路：稍作思考就会发现，如果一开始全部和为奇数，那么肯定是玩家1赢。<br>
如果是偶数的话，玩家1就要看一下这么多个数字里面有没有奇数，有奇数肯定是玩家1赢，
因为如果和是偶数，且存在奇数的话，那么肯定存在偶数个奇数，玩家1只要取走其中一个，他下次肯定还能取。所以玩家1肯定赢。如果没有奇数那么玩家2赢.
```cpp
#include<iostream>
#include<cstdio> 
using namespace std;
int main()
{
	int n,x;
	while(~scanf("%d",&n))
	{
	  	int flag=0;
		for(int i=0;i<n;i++)
		{
			scanf("%d",&x);
			if(x%2)
			  flag=1;
		}
      	if(flag) printf("First\n");
        else printf("Second\n");
	}
	return 0;
} 
```
### 1003 Weiming like XOR
题目大概说两个正整数a、b，已知s=a+b以及x=a xor b的值，问有几种a、b这样的数对。<br>
我知道异或相当于无进位的加法，s-x就是其各个位置的进位，比如s-x=1010，那就表示a和b的第1位和第3位发生的进位。<br>
这样，对于某些位其值就能确定，对于有些位其值不能确定（该位xor和为1且没有发生进位），这时a和b的该位都能选择0或者1，对于不确定的就是乘法原理答案累乘2。<br>
另外还有一些情况是不可能的，首先s<x不可能，s-x是奇数不可能，某一位xor和是1且发生了进位这不可能。<br>
最后注意是正整数，而出现0只有s=x的情况，这时答案要减去2。<br>

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long LL;
int main()
{
    LL s, x; 
    scanf("%lld %lld",&s,&x);
    LL ans = 0;
    if((s - x) >= 0 && (s - x) % 2 == 0) {
        int cnt = 0; 
        bool flag = true;
        while(x) {
            cnt = cnt + (x&1);
            x >>= 1;
        }
        ans = (1LL<<cnt);
        if(s == x) ans = -2;
    }
    cout << ans << endl;
    return 0;
}
```


