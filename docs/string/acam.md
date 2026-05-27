# Aho-Corasick Automation

核心思想是 KMP ，簡單來說就是在 Trie 上做 KMP。

```cpp
const int MAXN = 1'000'000;
struct Node {
    int ch[26];
    int fail;
    int end;
} trie[MAXN];
int root = 1;
int dummy = 0;
int cnt = 1;
void insert(string &s) {
    int pos = root;
    for(auto &c: s) {
        if(!trie[pos].ch[c - 'a']) {
            trie[pos].ch[c - 'a'] = ++cnt;
        }
        pos = trie[pos].ch[c - 'a'];
    }
    trie[pos].end++;
}

void build() {
    queue<int> q;
    for(int i = 0; i < 26; ++i) trie[dummy].ch[i] = root;
    trie[root].fail = dummy;
    q.push(root);
    while(!q.empty()) {
        int cur = q.front();
        q.pop();
        for(int i = 0; i < 26; ++i) {
            if(!trie[cur].ch[i]) {
                trie[cur].ch[i] = trie[trie[cur].fail].ch[i];
                continue;
            }
            trie[trie[cur].ch[i]].fail = trie[trie[cur].fail].ch[i];
            q.push(trie[cur].ch[i]);
        }
    }
}

int query(string &s) {
    int res = 0;
    int pos = root;
    for(auto &c: s) {
        pos = trie[pos].ch[c - 'a'];
        int k = pos;
        while(!vis[k]) {//跳fail
            res += trie[k].end;
            vis[k] = 1;
            k = trie[k].fail;
        }        
    }
    return res;
}

```

* [P3808](https://www.luogu.com.cn/problem/P3808)
