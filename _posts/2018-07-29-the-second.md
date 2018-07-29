---
layout: post
title: 2018.7.29学习记录
date: 2018-7-29
tags:[字符串]
catalog: true
---

# 今日的学习记录
### ac自动机
- <span style="color:red;">[HDU - 4758]( https://vjudge.net/problem/46763/origin) </span>


题意： 给n个R，m个D，用这些字符组成一个字符串，再给两个用R、D组成的字符
串，问有多少种字符串可以包含这两个单词，单词可以重叠

思路：ac自动机+状压DP 

```c++
#include<bits/stdc++.h>
using namespace std;
const int mod=1000000007;
int n,m,t;
char s[120];
struct Trie
{
    int next[300][2],end[300],fail[300];
    int root,L;
    int newnode()
    {
        for(int i=0;i<2;i++)
            next[L][i]=-1;
        end[L++]=0;
        return L-1;
    }
    void init()
    {
        L=0;
        root=newnode();
    }
    void insert(char str[],int tag)
    {
        int len=strlen(str);
        int now=root;
        for(int i=0;i<len;i++)
        {
           int id= (str[i]=='R')?0 :1;
           if(next[now][id]==-1)
            next[now][id]=newnode();
           now=next[now][id];
        }
        end[now]|=(1<<tag);
    }
    void build()
    {
        queue<int>q;
        fail[root]=root;
        for(int i=0;i<2;i++)
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
            end[now]|=end[fail[now]];
            for(int i=0;i<2;i++)
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
    int dp[4][110][110][300];
    void solve()
    {
        for(int R=0;R<=n;R++)
            for(int D=0;D<=m;D++)
                for(int i=0;i<L;i++)
                    for(int j=0;j<4;j++)
                        dp[j][R][D][i]=0;
        dp[0][0][0][0]=1;
        for(int R=0;R<=n;R++)
            for(int D=0;D<=m;D++)
                for(int i=0;i<L;i++)
                    for(int j=0;j<4;j++)
                    {
                        //cout<<j<<" "<<R<<" "<<D<<" "<<i<<" "<<dp[j][R][D][i]<<endl;
                        
                        if(dp[j][R][D][i]==0)continue;
                        int now=next[i][0];
                        if(R<n)
                        dp[j|end[now]][R+1][D][now]= (dp[j|end[now]][R+1][D][now]%mod+ dp[j][R][D][i]%mod)%mod;
                        now=next[i][1];
                        if(D<m)
                        dp[j|end[now]][R][D+1][now]= (dp[j|end[now]][R][D+1][now]%mod+ dp[j][R][D][i]%mod)%mod;
                    }
        int ans=0;
        for(int i=0;i<L;i++)
        {
            ans=(ans%mod+dp[3][n][m][i]%mod)%mod;
        }
        printf("%d\n",ans);
    }
}ac;
int main()
{
    scanf("%d",&t);
    while(t--)
    {
        scanf("%d%d",&n,&m);
        ac.init();
        for(int i=0;i<2;i++)
        {
            scanf("%s",s);
            ac.insert(s,i);
        }
        ac.build();
        ac.solve();
    }
    return 0;
}
```
- [HDU - 4511](https://vjudge.net/problem/37609/origin)
 
 题意：给n个点，m条路径，要求从1到n不能经过这些路径，求要走的路程的最小值
 
 思路：本是一个ac自动机+DP的题目，但是自己不知道什么情况推了半天，之后还一直wa，最后发现是求距离的时候精度出了问题，以后还是要注意这样的细节啊
 ```c++
 #include<bits/stdc++.h>
using namespace std;
int n,m;
struct node
{
    int x,y;
}p[60];
int rut[10];
double dis(int x1,int y1,int x2,int y2)
{
    //double c1=1.0*(x1-x2)*(x1-x2);
    
    //double c2=1.0*(y1-y2)*(y1-y2);
    
    return sqrt((double)((1.0*x1-x2)*(1.0*x1-x2))+(double)((1.0*y1-y2)*(1.0*y1-y2)));
}
struct Trie
{
    int next[1000][60],fail[1000],end[1000];
    int root,L;
    int newnode()
    {
        for(int i=1;i<=n;i++)
            next[L][i]=-1;
        end[L++]=0;
        return L-1;
    }
    void init()
    {
        L=0;
        root=newnode();
    }
    void insert(int str[],int len)
    {
        int now=root;
        for(int i=0;i<len;i++)
        {
            int id=str[i];
            if(next[now][id]==-1)
                next[now][id]=newnode();
            now=next[now][id];
        }
        end[now]=1;
    }
    void build()
    {
        queue<int>q;
        fail[root]=root;
        for(int i=1;i<=n;i++)
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
            end[now]|=end[fail[now]];
            for(int i=1;i<=n;i++)
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
    double dp[60][1000];
    void solve()
    {
        for(int i=1;i<=n;i++)
        for(int j=0;j<L;j++)
        dp[i][j]=1e15;
        double inf=1e15;
        //cout<<(int)inf<<endl;
        
        dp[1][next[root][1]]=0;
        for(int i=1;i<n;i++)
        for(int j=0;j<L;j++)
        {
            if(dp[i][j]==inf)continue;
            for(int k=i+1;k<=n;k++)
            {
                int now=next[j][k];
                if(end[now]==0)
                {
                    double d=dis(p[i].x,p[i].y,p[k].x,p[k].y);
                    //cout<<flag[j].x<<" "<<flag[j].y<<" "<<p[k].x<<" "<<p[k].y<<" "<<d<<endl;
                    
                    //cout<<i<<" "<<j<<" "<<k<<" "<<dp[i][j]<<" "<<d<<endl;
                    
                    dp[k][now]=min(dp[k][now],dp[i][j]+d);
                }
            }
        }
        double ans=inf;
        for(int i=0;i<L;i++)
            if(dp[n][i]<inf)
            ans=min(ans,dp[n][i]);
        if(ans==inf)
        printf("Can not be reached!\n");
        else printf("%.2lf\n",ans);
    }
    void debug()
    {
        for(int i=0;i<L;i++)
        {
            printf("id=%3d,fail=%3d,end=%3d,chi=[",i,fail[i],end[i]);
            for(int j=1;j<=50;j++)
                printf("%2d",next[i][j]);
            printf("]\n");
        }
    }
}ac;
int main()
{
    while(scanf("%d%d",&n,&m))
    {
        if(n==0&&m==0)break;
        for(int i=1;i<=n;i++)
        {
         int x,y;
         scanf("%d%d",&x,&y);
            p[i].x=x;
            p[i].y=y;
        }
        ac.init();
        for(int i=0;i<m;i++)
        {
            int k;
            scanf("%d",&k);
            for(int j=0;j<k;j++)
            {
                scanf("%d",&rut[j]);
            }
            ac.insert(rut,k);
        }
        ac.build();
        //ac.debug();
        
        ac.solve();
    }
    return 0;
}
 ```
