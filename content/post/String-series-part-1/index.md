---
title: "String series: Part 1"
date: 2021-07-21T00:25:58+06:00
draft: false
image: "pexels-fiona-art-5186869.jpg"
categories: ["String"]
tags: ["Suffix Structures"]
---

String related problems used to scare the shit out of me. Then I was introduced to some string suffix structures and it absolutely blew my mind. I don&#39;t claim to be an expert on suffix structures, but I hope my approach to solve the following problems will help you get some insight.

Please keep in mind that this article is **in no way an introduction** to these data structures. I&#39;ll attach some links at the end. Read them to understand how these data structures work.

Let&#39;s start then.

## 1. **Codeforces GYM 101991E- Exciting Menus**

Problem statement:

![Problem Statement](101991E.png)

Problem link: [Exciting Menus - Codeforces](https://codeforces.com/gym/101991/problem/E)

Let&#39;s think about the popularity of a substring first. If this was the only thing that we were asked to find, then a **trie** would suffice. Length of the prefix is also easy to maintain in a trie. The only thing remaining is the _joy level_.

It&#39;s pretty apparent that if we have multiple occurrences of a substring, then we should take the highest _joy level_ corresponding to that substring. I don&#39;t think we can do that with a normal trie. Here&#39;s where **Aho-Corasick** comes into play.

Think what the _fail link_ of an Aho-Corasick node means. It points to the node with the maximum prefix match with the suffix of the original node. Suppose we have a prefix &quot;aab&quot;. Then the node corresponding to this prefix must be a _fail link/suffix link_ to a prefix ending with &quot;aab&quot;, right? Then that node will be a _suffix link_ to another node ending with &quot;aab&quot; and so on.

Now, we will make another graph where we will add a directed edge from the suffix link node to the original node. For example, if the suffix link of a prefix &quot;bccaab&quot; goes to the prefix &quot;aab&quot;, then we will add a directed edge from &quot;aab&quot; to &quot;bccaab&quot;. This graph will be a rooted tree. In this newly formed graph, the descendants of &quot;aab&quot; will be the prefixes which have &quot;aab&quot; as their suffix. So, if we know the maximum _joy level_ of the descendants of &quot;aab&quot;, we can assign that value to &quot;aab&quot;; pretty cool right?

Now, we&#39;ll just traverse each node of the **Aho-Corasick** automaton and use the formula on each of them to find out the answer.

Here&#39;s my code:
```c++
#include <bits/stdc++.h>
#define N 100007
#define ll long long
#define pii pair<int,int>
#define ff first
#define ss second
using namespace std;

std::vector<int> adj[N];
string s[N];
int node[N][27];
int vis[N];
int val[N];
int backnode[N];
int cnt[N];
bool ending[N];
int id;
int ln[N];
int arr[N];
ll ans;
int depth[N];
inline void init()
{
    id = 0;
    for (int i = 0; i < 26; i++)
        node[id][i] = 0;
}

inline int newnode()
{
    id++;
    for (int i = 0; i < 26; i++)
    {
        node[id][i] = 0;
    }
    backnode[id] = 0;
    cnt[id] = 0;
    val[id] = 0;
    ending[id] = 0;
    depth[id] = 0;
    return id;
}
inline void Insert(string &st)
{
    int u = 0;
    int n = st.size();
    for (int i = 0; i < n; i++)
    {
        int x = st[i] - 'a';
        if (!node[u][x]) node[u][x] = newnode();
        u = node[u][x];
        val[u] = max(val[u], arr[i]);
        cnt[u]++;
        depth[u] = i + 1;
    }

    ending[u] = 1;
}

inline void AhoCorasik()
{
    queue<int>q;
    for (int i = 0; i < 26; i++)
    {
        if (node[0][i])
        {
            q.push(node[0][i]);
            backnode[node[0][i]] = 0;
        }
    }
    while (!q.empty())
    {
        int u = q.front();
        int w = backnode[u];
        adj[w].push_back(u);
        q.pop();

        for (int i = 0; i < 26; i++)
        {
            int v = node[u][i];
            if (v)
            {
                q.push(v);
                backnode[v] = node[backnode[u]][i];
            }
            else
            {
                node[u][i] = node[backnode[u]][i];
            }
        }
    }
}

void dfs(int x)
{
    ans = max( ans, 1LL * depth[x] * val[x] * cnt[x] );

    for ( auto v : adj[x] )
    {
        dfs(v);
    }
}

int traverse(int x)
{
    for ( auto v : adj[x] )
    {
        val[x] = max(val[x], traverse(v));
    }

    return val[x];
}

int main()
{
    ios_base::sync_with_stdio(0);
    cin.tie(0);

    freopen("exciting.in", "r", stdin);

    int tc;
    cin >> tc;

    while (tc--)
    {
        init();

        ans = 0;
        int n;
        cin >> n;

        for ( int i = 0; i < n; i++ )
        {
            cin >> s[i];
            ln[i] = s[i].size();
        }

        for ( int i = 0; i < n; i++ )
        {
            for ( int j = 0; j < ln[i]; j++ )
                cin >> arr[j];

            Insert(s[i]);
        }

        AhoCorasik();
        int k = traverse(0);
        dfs( 0 );

        for( int i=0;i<=id;i++ )
            adj[i].clear();

        cout << ans << "\n";
    }
}
```

