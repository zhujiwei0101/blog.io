---
layout: post
title: 2018.7.28学习记录
date: 2018-7-28
categories: blog
tags: 
---

### 今日的学习记录
######  ac自动机
    
   <span style="color:blue;"> [HDU - 3341](https://cn.vjudge.net/problem/15628/origin) </span>
   
   
   题意：现给定一堆数论基因序列，以及一段DNA序列，重排该DNA序列使基因数目最多
   
   
   思路：ac自动机+状压DP

   本是一个很常见的匹配问题，但自己之前没有写过状压DP的题目，对这些操作 
   不是很了解，故写在记录里
   贴上代码 （代码也参考了网上别人的，姑且算是学习吧）
``` c++
#include<bits/stdc++.h>
using namespace std;
int n;
char s[100];
char sub[100];
struct Trie
{
    int next[1000+10][4],fail[1000+10],end[1000+10];
    int root,L;
    int newnode()
    {
        for(int i=0;i<4;i++)
            next[L][i]=-1;
        end[L++]=0;
        return L-1;
    }
    void init()
    {
        L=0;
        root=newnode();
    }
    int getid(char t)
    {
        if(t=='A')return 0;
        if(t=='G')return 1;
        if(t=='C')return 2;
        if(t=='T')return 3;
    }
    void insert(char str[])
    {
        int len=strlen(str);
        int now=root;
        for(int i=0;i<len;i++)
        {
            int id=getid(str[i]);
            if(next[now][id]==-1)
                next[now][id]=newnode();
            now=next[now][id];
        }
        end[now]++;
    }
    void build()
    {
        queue<int>q;
        fail[root]=root;
        for(int i=0;i<4;i++)
            if(next[root][i]==-1)
            next[root][i]=root;
            else
            {
                fail[next[root][i]]=root;
                q.push(next[root][i]);
            }
        while(!q.empty())
        {
            int now=q.front();
            q.pop();
            end[now]+=end[fail[now]];
            for(int i=0;i<4;i++)
            {
                if(next[now][i]==-1)
                    next[now][i]=next[fail[now]][i];
                else
                {
                    fail[next[now][i]]=next[fail[now]][i];
                    q.push(next[now][i]);
                }
            }
        }
    }
    int dp[550][11*11*11*11+1];
    int num[4];
    int bit[4];
    void solve(char str[])
    {
        memset(dp,-1,sizeof(dp));
        int  len=strlen(str);
        memset(num,0,sizeof(num));
        memset(bit,0,sizeof(bit));
        for(int i=0;i<len;i++)
        {
            num[getid(str[i])]++;
        }
        dp[0][0]=0;
        bit[0]=1;
        bit[1]=bit[0]*(num[0]+1);
        bit[2]=bit[1]*(num[1]+1);
        bit[3]=bit[2]*(num[2]+1);
        int last=bit[0]*num[0]+bit[1]*num[1]+bit[2]*num[2]+bit[3]*num[3];
        for(int A=0;A<=num[0];A++)
        for(int G=0;G<=num[1];G++)
        for(int C=0;C<=num[2];C++)
        for(int T=0;T<=num[3];T++)
        {
            int i=A*bit[0]+G*bit[1]+C*bit[2]+T*bit[3];
            for(int j=0;j<L;j++)
            {
                if(dp[j][i]==-1)continue;
                for(int k=0;k<4;k++)
                {
                    if(k==0&&A==num[0])continue;
                    if(k==1&&G==num[1])continue;
                    if(k==2&&C==num[2])continue;
                    if(k==3&&T==num[3])continue;
                    int now=next[j][k];
                    dp[now][i+bit[k]]=max(dp[now][i+bit[k]],dp[j][i]+end[now]);
                }
            }
        }
        int ans=0;
        for(int i=0;i<L;i++)
            ans=max(ans,dp[i][last]);
        printf("%d\n",ans);
    }
}ac;
int main()
{
    int tot=0;
    while(scanf("%d",&n),n)
    {
        ac.init();
        for(int i=0;i<n;i++)
        {
            scanf("%s",sub);
            ac.insert(sub);
        }
        ac.build();
        scanf("%s",s);
        printf("Case %d: ",++tot);
        ac.solve(s);
    }
    return 0;
}
```
###### 数论 Bézout's identity
> **Bézout's identity** — Let *a* and *b* be [integers](https://en.wikipedia.org/wiki/Integer "Integer") with [greatest common divisor](https://en.wikipedia.org/wiki/Greatest_common_divisor "Greatest common divisor") *d*. Then, there exist integers *x* and *y* such that *ax* + *by* = *d*. More generally, the integers of the form *ax* + *by* are exactly the multiples of *d*. (引用自维基百科)



<span style="color:blue;"> [Codeforces 1010C](http://codeforces.com/problemset/problem/1010/C) </span>

题意：给定一堆十进制的数以及d,将这些数转换成d进制后，可以随意相加，问能最后一位的数字能得到多少种


思路：令a1,a2..an为转换后的序列，k=a1x1+a2x2+...+anxn;问题转换成k%d能得到多少种
根据Bézout's identity 可知k为gcd(a1,a2.....an)的倍数，故求出所有数的gcd即可得到答案
```c++
#include<bits/stdc++.h>
using namespace std;
int gcd(int a,int b)
{
    if(a%b==0)return b;
    return gcd(b,a%b);
}
int n,k;
int main()
{
    while(cin>>n>>k)
    {
        int g=0;
        for(int i=0;i<n;i++){
           int t;
           cin>>t;
           g=gcd(g,t);
        }
        set<int>ans;
        long long s=0;
        for(int i=0;i<k;i++){
            ans.insert(s%k);
            s+=g;
        }
        cout<<ans.size()<<endl;
        for(set<int>::iterator it=ans.begin();it!=ans.end();it++)
        cout<<*it<<" ";cout<<endl;
    }
    return 0;
}
```
  （今天第一次拿markdown写东西，还有很多不太熟练QAQ）












