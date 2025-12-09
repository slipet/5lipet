# Introductory Problems

### 1. Weird Algorithm

簡單模擬

* <span style="color:red"> 沒有注意到乘 3 可能導致的 overflow </span>

```cpp
void solve() {
    int n;
    cin >> n;
    cout<<n;
    if(n != 1) cout<<' ';
    while(n != 1) {
        if(n & 1) {
            n *= 3;
            n++;
        } else {
            n >>= 1;
        }
        cout<<n;
        if(n != 1) cout<<' ';
    }
}
```

### 2. Missing Number

原本想用排序或是hash table 但是感覺有點醜，想了一下發現可以用 need - sum = x 找不在的元素。

題目 n 的上限是 $2 * n^5$ 有可能超過 int 索性直接開 long long 算。



```cpp
void solve() {
    int n, x;
    cin >> n;
    long long sum = 0;
    long long need = 1LL * n * (n + 1) / 2;
    for(int i = 1; i <= n - 1; ++i) {
        cin >> x;
        sum += x;
    }
    cout<< need - sum <<endl;
}
```

#### xor solution

不用開 long long ，避免 overflow 。

```cpp
void solve() {
    int n, x;
    cin >> n;
    long long xor = 0, all = 0;
    for(int i = 1; i <= n - 1; ++i) {
        cin >> x;
        xor ^= x;
        all ^= i;
    }
    cout<< all ^ xor ^ n <<endl;
}
```

[ref](https://hackmd.io/p53aGB5DQBGRwSCBd9Fc-Q?view#%E6%83%B3%E6%B3%95-3-2%EF%BC%9AXOR-%E7%89%88)


### 3. Repetitions

基本的技巧

1. 如何處理結尾情況

2. 如何計算不同元素

```cpp
void solve() {
    string s;
    cin >> s;
    const int n = s.length();
    int cnt = 0;
    int ans = 0;
    for(int i = 0; i < n; ++i) {
        cnt++;
        if(i == n - 1 || s[i] != s[i + 1]) {
            ans = max(ans, cnt);
            cnt = 0;
        };
    }
    cout<<ans<<endl;
}
```

### 4. Increasing Array

1. 要注意 overflow 的情況

2. max 的技巧可以避免 x > pre 的情況。

```cpp
void solve() {
    int n, x, pre = 0;
    long long ans = 0;
    cin >> n;
    for(int i = 0; i < n; ++i) {
        cin >> x;
        if(i > 0)
            ans += max(0, pre - x);
        if(pre <= x) pre = x;
    }
    cout<< ans << endl;
}
```

### 5. Permutations

* 注意 1 是合法的答案

```cpp
void solve() {
    int n;
    cin >> n;
    if(n > 1 && n <= 3) {
        cout<<"NO SOLUTION";
    } else {
        int odd = n - ((n & 1) == 0);
        int even = n - (n & 1);

        for(int i = odd; i > 0; i -= 2) {
            if(i != odd) cout<<' ';
            cout<<i;
        }
        for(int i = even; i > 0; i -= 2) {
            cout<<' ';
            cout<<i;
        }
    }
    cout<<endl;
}
```

### 6. Number Spiral

```cpp
void solve() {
    long long x, y;
    cin >> y >> x;
    long long mx = max(x, y);
    long long base = mx * mx - (mx - 1);
    long long ans;
    if(mx == y) {
        ans = base + (mx & 1 ? -1 : 1) * (mx - x);
    } else {
        ans = base + (mx & 1 ? 1 : -1) * (mx - y);
    }
    cout<<ans<<endl;
}
```

### 7. Two Knights

* <span style="color:red"> 沒有注意到 square * (square - 1) 可能導致的 overflow </span>

```cpp
void solve() {
    long long n;
    cin >> n;
    for(int k = 1; k <= n; ++k) {
        long long square = k * k;
        long long w = k - 1;
        long long h = k - 2;
        cout << square * (square - 1) / 2 - w * h * 2 * 2 << endl;
    }
}
```

### 8. Two Sets

* <span style="color:red"> 沒有注意到正確時要輸出 "YES" </span>

```cpp
void solve() {
    long long n;
    cin >> n;
    long long sum = (n + 1) * n / 2;
    if(sum & 1) {
        cout<<"NO";
    } else {
        vector<int> left, right;
        long long half = sum / 2;
        for(int i = n; i; --i) {
            if(half >= i) {
                left.push_back(i);
                half -= i;
            } else{
                right.push_back(i);
            }
        }
        cout<<"YES"<<endl;
        cout<<left.size()<<endl;
        for(int i = 0; i < left.size(); ++i) {
            if(i) cout<<' ';
            cout<<left[i];
        }
        cout<<endl;
        cout<<right.size()<<endl;
        for(int i = 0; i < right.size(); ++i) {
            if(i) cout<<' ';
            cout<<right[i];
        }
    }
    cout<<endl;
}
```

### 9. Bit Strings

```cpp
const int MOD = 1'000'000'000 + 7;
void solve() {
    int n;
    cin >> n;
    int ans = 1;
    for(int i = 0; i < n; ++i) {
        ans = (ans << 1) % MOD;
    }
    cout<< ans << endl;
}
```
### 10. Trailing Zeros

直覺地想到是要找階乘中 2 和 5 的因子個數。

* 錯誤的想法﹕

一開始直覺的思考計算　n / 10 * 2 + (n % 10 >= 5) 計算。但是這個方法會漏掉 $5^2$, $5^3$  這種數。

* Solution:

接著思考如何得到階乘中 2 和 5 的因子個數。
對於 5 的倍數的個數可以由 $\lfloor \frac{n}{5} \rfloor$ 得到，而 $5^2$ 可以由 $\lfloor \frac{n}{25} \rfloor$ 得到。
所以對於 $n!$ 中 5 的因子個數 $cnt_5(n!)$ 可以由 $cnt_5(n!) = \lfloor \frac{n}{5} \rfloor + \lfloor \frac{n}{5^2} \rfloor + \lfloor \frac{n}{5^3} \rfloor ...$

同理，所以對於 $n!$ 中 2 的因子個數 $cnt_2(n!)$ 可以由 $cnt_2(n!) = \lfloor \frac{n}{2} \rfloor + \lfloor \frac{n}{2^2} \rfloor + \lfloor \frac{n}{2^3} \rfloor ...$

我們要找的是 2 * 5 的個數為 $min(cnt_5(n!), cnt_2(n!))$

```cpp
void solve() {
    int n;
    cin >> n;
    long long pow5 = 1, pow2 = 1;
    int cnt5 = 0, cnt2 = 0;
    while(pow5 * 5 <= n) {
        pow5 *= 5;
        cnt5 += (n / pow5);
    }
    while(pow2 * 2 <= n) {
        pow2 *= 2;
        cnt2 += (n / pow2);
    }
    cout << min(cnt2, cnt5) << endl;
}
```

由其他人的寫法發現直接計算 $cnt_5(n!)$ 就可以，一開始我也直覺地想到 $n!$ 中 5 的因子個數 $cnt_5(n!)$ 直覺上會少於 $cnt_2(n!)$，但是寫題目的時候懶得仔細去思考證明，所以同時計算 2 和 5 並且取最小值。

證明:

由上述可知:

$cnt_5(n!) = \lfloor \frac{n}{5} \rfloor + \lfloor \frac{n}{5^2} \rfloor + \lfloor \frac{n}{5^3} \rfloor + ... + \lfloor \frac{n}{5^k} \rfloor, \quad k \le \log_5{(n)}$

$cnt_2(n!) = \lfloor \frac{n}{2} \rfloor + \lfloor \frac{n}{2^2} \rfloor + \lfloor \frac{n}{2^3} \rfloor + ... + \lfloor \frac{n}{2^p} \rfloor, \quad p \le \log_2{(n)}$

可以寫成:

\[
\left\{ 
\begin{array}{ll}
    cnt_5(n!) = n\sum_{i = 1}^{k} \lfloor \frac{1}{5^i} \rfloor\\
    cnt_2(n!) = n\sum_{j = 1}^{p} \lfloor \frac{1}{2^j} \rfloor
\end{array}
\right.
\]

觀察當項數 i = j = 1, 2, ... 時，$\frac{1}{5} \le \frac{1}{2}$, $\frac{1}{5^2} \le \frac{1}{2^2}$ ...。

且 $k \le p$，表示 $cnt_2(n!)$ 的項數會比 $cnt_5(n!)$ 多，也就可以知道 $cnt_5(n!) \le cnt_2(n!)$。


```cpp
void solve() {
    int n;
    cin >> n;
    int cnt = 0;
    while(n >= 5) {
        cnt += (n / 5);
        n /= 5;
    }
    cout << cnt << endl;
}
```

### 11. Coin Piles

解聯立方程即可。

```cpp
void solve() {
    int a, b;
    cin >> a >> b;
    
    int x = 2 * a - b;
    int y = 2 * b - a;
    if(x < 0 || y < 0 || x % 3 != 0 || y % 3 != 0)
        cout<<"NO";
    else
        cout<<"YES";

    cout<<endl;
}
```

### 12. Palindrome Reorder


```cpp
void solve() {
    string s;
    cin >> s;
    unordered_map<char, int> fq;
    for(auto &c: s) fq[c]++;
    int odd = 0;
    char mid = 0;
    for(auto &[x, c]: fq) {
        if(c & 1) {
            odd++;
            mid = x;
        }
    }
    if(odd > 1) {
        cout<<"NO SOLUTION"<<endl;
        return;
    }
    string half = "";
    for(auto &[x, c]: fq) {
        if(c / 2)
            half += string(c / 2, x);
    }
    string rev = half;
    ranges::reverse(rev);
    if(odd) {
        cout<<half + mid + rev <<endl;
    } else {
        cout<<half + rev <<endl;
    }
}
```

### 13. Gray Code

* gray code 特殊寫法 i ^ (i >> 1)

```cpp
void solve() {
    int n;
    cin >> n;
    int limit = 1 << n;
    int x = 0;
    for(int i = 0; i < limit; ++i) {
        x = i ^ (i >> 1);
        vector<int> d(n);
        int j = n - 1;
        while(x) {
            d[j--] = x & 1;
            x >>= 1;
        }
        for(auto &x: d) cout<<x;
        cout<<endl;
    }
}
```
### 14. Tower of Hanoi

* 河內塔，結果反而不會寫了。

* 主要思考的方向應該是要看做一個整體，並且使用反向思考。

* 對於最後一個元素一定是由 1 -> 3，則代表 2 會有所有其他的元素。

* 所以可以得到:

    1. 將 n - 1 個元素由 1 -> 2(繼續遞迴，使用3作為輔助)

    2. 將第 n 個元素 1 -> 3

    3. 將 n - 1 個元素由 2 -> 3

```cpp
void solve() {
    int n;
    cin >> n;
    vector<vector<int>> ans;
    auto dfs = [&](auto &&dfs, int n, char from, char to, char mid) -> void {
        if(n == 1)  {
            ans.push_back({from, to});
            return;
        } else {
            dfs(dfs, n - 1, from, mid, to);
            ans.push_back({from, to});
            dfs(dfs, n - 1, mid, to, from);
        }
    };
    dfs(dfs, n, '1', '3', '2');
    cout<<ans.size()<<endl;
    for(auto &p: ans) cout<<(char)p[0]<<' '<<(char)p[1]<<endl;
}
```

### 15. Creating Strings

兩種寫法，注意兩種寫法都需要使用 sort

1. next_permutation

```cpp
void solve() {
    string s;
    cin>>s;
    
    ranges::sort(s);
    vector<string> ans;
    
    do {
        ans.push_back(s);
    } while(next_permutation(s.begin(), s.end()));
    
    cout<<ans.size()<<endl;
    for(auto &a: ans) cout<<a<<endl;
}
```

2. 暴力枚舉，遞迴

```cpp
void solve() {
    string s;
    cin>>s;
    unordered_map<char, int> cnt;
    for(auto &c: s) cnt[c]++;
    string cur = "";
    int n = s.length();
    vector<string> ans;
    auto dfs = [&](auto&& dfs, int i) -> void {
        if(i == n) {
            ans.push_back(cur);
            return;
        }
        for(auto &[ch, c]: cnt) {
            if(c == 0) continue;
            c--;
            cur += ch;
            dfs(dfs, i + 1);
            cur.pop_back();
            c++;
        }
    };
    dfs(dfs, 0);
    cout<<ans.size()<<endl;
    ranges::sort(ans);
    for(auto &a: ans) cout<<a<<endl;
}
```

### 16. Apple Division

二進制枚舉

我使用的是比較好寫的枚舉的方式會比較慢

1. $O(n2^n)$
比較慢

```cpp
void solve() {
    int n;
    cin>>n;
    vector<int> a(n);
    long long tot = 0;
    for(int i = 0; i < n; ++i) {
        cin >> a[i];
        tot += a[i];
    }
    int u = 1 << n;
    long long ans = INT_MAX;
    for(int s = 0; s < u; ++s) {
        long long sum = 0;
        for(int i = 0; i < n; ++i) {
            if(s >> i & 1) sum += a[i];
        }
        ans = min(ans, abs(sum * 2 - tot));
    }
    cout<<ans<<endl;
}
```

2. 遞迴枚舉 $O(2^n)$

```cpp
void solve() {
    int n;
    cin>>n;
    vector<int> a(n);
    long long tot = 0;
    for(int i = 0; i < n; ++i) {
        cin >> a[i];
        tot += a[i];
    }
    auto dfs = [&](auto&& dfs, int i, long long l, long long r) -> long long {
        if(i == n) {
            return abs(l - r);
        }
        return min(dfs(dfs, i + 1, l + a[i], r), dfs(dfs, i + 1, l, r + a[i]));
    };
    cout<<dfs(dfs, 0, 0, 0)<<endl;
}
```

### 17. Chessboard and Queens

用遞迴暴力枚舉所有可能

```cpp
void solve() {
    const int n = 8;
    vector<string> board(n);
    for(int i = 0; i < n; ++i) {
        cin>>board[i];
    }
    int col = 0, diag = 0, inv_diag = 0;
    int ans = 0;
    auto set = [&](int r, int c) -> int {
        if(col >> c & 1) return 0;
        //diag: k = r - c + (n - 1)
        int k = r - c + (n - 1);
        if(diag >> k & 1) return 0;
        //inv_diag: r + c = k
        int inv_k = r + c;
        if(inv_diag >> inv_k & 1) return 0;
        diag |= (1 << k);
        col |= (1 << c);
        inv_diag |= (1 << inv_k);
        return 1;
    };
    auto unset = [&](int r, int c) -> void {
        col &= ~(1 << c);
        //diag: k = r - c + (n - 1)
        int k = r - c + (n - 1);
        diag &= ~(1 << k);
        //inv_diag: r + c = k
        int inv_k = r + c;
        inv_diag &= ~(1 << inv_k);
    };
    auto dfs = [&](auto&& dfs, int i) -> void {
        if(i == n) {
            ans++;
            return;
        }
        for(int j = 0; j < n; ++j) {
            if(board[i][j] == '*') continue;
            if(set(i, j)) {
                dfs(dfs, i + 1);
                unset(i, j);
            }
        }
    };
    dfs(dfs, 0);
    cout<<ans<<endl;
}
```

### 18. Raab Game I

重點在找到不會變動的元素個數

```cpp
void solve() {
    int n, a, b;
    cin >> n >> a >> b;
    vector<int> arr(n);
    int exchange = 0;
    if(a < b) {
        swap(a, b);
        exchange = 1;
    }
    if(a + b > n || a >= n || b >= n || (b == 0 && a != 0)) {
        cout<<"NO"<<endl;
        return;
    }
    iota(arr.begin(), arr.end() , 1);
    int rest = n - a - b;
    int end = n - rest;
    int cur = 1;
    for(int i = b; i < end; ++i) {
        arr[i] = cur++;
    }
    for(int i = 0; i < b; ++i) {
        arr[i] = cur++;
    }

    cout<<"YES"<<endl;
    auto printa = [&]() -> void {
        for(int i = 0; i < n; ++i) {
            if(i > 0) cout<<' ';
            cout<<i + 1;
        }
        cout<<endl;
    };
    auto printb = [&]() -> void {
        for(int i = 0; i < n; ++i) {
            if(i > 0) cout<<' ';
            cout<<arr[i];
        }
        cout<<endl;
    };
    if(exchange) {
        printb();
        printa();
    } else {
        printa();
        printb();
    }
    
}
```

### 19. Mex Grid Construction

1. 樸素解法，維護一個有序結構。

```cpp
void solve() {
    int n;
    cin >> n;
    
    vii mat(n, vi(n, 0));
    
    int mx = 0;
    for(int i = 0; i < n; ++i) {
        set<int> available, row;
        for(int x = 0; x < mx; ++x) {
            available.insert(x);
        }
        for(int j = 0; j < n; ++j) {
            //delete
            for(int k = i - 1; k >= 0; --k) {
                int &x = mat[k][j];
                available.erase(x);
            }
            if(available.empty()) {
                available.insert(mx);
                mx++;
            }
            int x = *available.begin();
            mat[i][j] = x;
            available.erase(x);
            row.insert(x);
            //add
            for(int k = i - 1; k >= 0; --k) {
                int &x = mat[k][j];
                if(!row.contains(x))
                    available.insert(x);
            }
        }
    }
    for(int i = 0; i < n; ++i) {
        for(int j = 0; j < n; ++j) {
            if(j > 0) cout<<' ';
            cout<<mat[i][j];
        }
        cout<<endl;
    }
 
}
```

2. 當 n > 10^9  時，上面的方法不再適用，簡單講一下結論 i ^ j

這題的詳細解釋先跳過，目前程度還沒有辦法深入理解

key words: grandy number, nimber, game theory


[source](https://nthu-cp.github.io/NTHU-CPP/misc/nim_and_sg_value.html)

[video](https://www.youtube.com/watch?v=cRHe4Ig3JnE&t=6446s)

[gpt](https://chatgpt.com/share/6936bd46-4264-8010-8c93-40c7d3d00cc6)

```cpp
void solve() {
    int n;
    cin>>n;
    
    for(int i = 0; i < n; ++i) {
        for(int j = 0; j < n; ++j) {
            if(j > 0) cout<<' ';
            cout<<(i ^ j);
        }
        cout<<endl;
    }
}
```

### 20. Knight Moves Grid

bfs 把整個棋盤走完。

證明 knight 可以從任何點抵達左上角，也就是反過來說證明從左上角原點可以抵達 n * n 棋盤的任何位置

1. 首先構造出 4 x 4 的棋盤

| 0 | 3 | 2 | 5 |
|:-:|:-:|:-:|:-:|
| 3 | 4 | 1 | 2 |
| 2 | 1 | 4 | 3 |
| 5 | 2 | 3 | 2 |

2. 接著由 4 x 4 構造出 (4 + 1) x (4 + 1) 的棋盤

| 0 | 3 | 2 | 5 | 2 |
|:-:|:-:|:-:|:-:|:-:|
| 3 | 4 | 1 | 2 | 3 |
| 2 | 1 | 4 | 3 | 2 |
| 5 | 2 | 3 | 2 | 3 |
| 2 | 3 | 3 | 3 | 4 |

這不是 optimal 的棋盤但是透過這種方式可以構造出 n x n 的棋盤且每個位置都可以遍歷到。

```cpp
vector<pii> dirs = {{2, 1}, {1, 2}, {2, -1}, {1, -2}, {-2, 1}, {-1, 2}, {-2, -1}, {-1, -2}};

void solve() {
    int n;
    cin >> n;
    vii board(n, vi(n, -1));
    board[0][0] = 0;
    vector<pii> q;
    q.emplace_back(0, 0);
    while(!q.empty()) {
        vector<pii> nxt;
        for(auto &[r, c]: q) {
            for(auto &[dr, dc]: dirs) {
                int nr = r + dr, nc = c + dc;
                if(nr < 0 || nc < 0 || nr >= n || nc >= n) continue;
                if(board[nr][nc] >= 0) continue;
                board[nr][nc] = board[r][c] + 1;
                nxt.emplace_back(nr, nc);
            }
        }
        q = move(nxt);
    }
    for(int i = 0; i < n; ++i) {
        for(int j = 0; j < n; ++j) {
            if(j > 0) cout<<' ';
            cout<<board[i][j];
        }
        cout<<endl;
    }
}
```

### 21. Grid Coloring I

一開始想的是 bfs, dfs ，結果做了一個dfs然後超時。

接著用網格順序遍歷單純的判斷可以選的字母就好了。真正會限制的只有自己，左，上三種，所以一定有可以選的字母

```cpp
vector<pii> dirs = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};

void solve() {
    int n, m;
    cin >> n >> m;
    vs board;
    for(int i = 0; i < n; ++i) {
        string s;
        cin >> s;
        board.pb(s);
    }
    vector<vector<char>> ans(n, vector<char>(m, 0));
    
    for(int i = 0; i < n; ++i) {
        for(int j = 0; j < m; ++j) {
            int used = 1 << (board[i][j] - 'A');
            if(i == 0 && j == 0) {
                for(int k = 0; k < 4; ++k) {
                    if((used >> k & 1) == 0) {
                        ans[i][j] = 'A' + k;
                        cout<<(char)ans[i][j];
                        break;
                    }
                }
                continue;
            }
            if(i - 1 >= 0) {
                used |= 1 << (ans[i - 1][j] - 'A');
            }
            if(j - 1 >= 0) {
                used |= 1 << (ans[i][j - 1] - 'A');
            }
            for(int k = 0; k < 4; ++k) {
                if((used >> k & 1) == 0) {
                    ans[i][j] = 'A' + k;
                    cout<<(char)ans[i][j];
                    break;
                }
            }
        }
        cout<<endl;
    } 
}
```

### 22. Digit Queries

```cpp
void solve() {
    long long k;
    cin >> k;
    long long pow10 = 1LL;
    long long range = 0;
    int len = 1;
    for(; ;len++) {//find start
        range = 9 * pow10 * len;
        if(k > range) {
            k -= range;
        } else break;
        pow10 *= 10;
    }
    long long start = pow10;
    long long idx = (k - 1) / len;
    long long num = start + idx;
    k -= len * idx;//map to num
    k--;//0-indexed
    string s = to_string(num);
    cout<< s[k] <<endl;
}
```