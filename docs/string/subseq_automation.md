# Sequence Automaton

## 子序列自動機

```cpp
string s;
const int invalid = -1;
vector nxt(n + 1, vector<int>(26, invalid));
for(int i = n - 1; i >= 0; --i) {
	nxt[i] = nxt[i + 1];
	nxt[i][s[i] - 'a'] = i + 1;
}
for(auto &w: words) {
	int pos = 0;
	for(auto &c: w) {
		pos = nxt[pos][c - 'a'];
		if(pos < 0) break;
	}
	if(pos < 0) ...
	else ....
}
```