## C++17

### clamp

將給定值限制在指定的範圍內。

```cpp
#include <algorithm>
using namespace std;

//val = [min, max]
clamp(val, min, max);
```

## C++20

### split

空白切割字串

```cpp
for (auto token : s | views::split(' ')) {
    //token 是 subrange iterator ，不是 string
    if (token.begin() == token.end()) continue;//自動忽略多個空白 -> 否則會產生空字串
    string_view sv(token.begin(), token.end());
}
```

* 切「任意空白」（space / tab / newline）

```cpp
for (auto token : s | views::split_if(isspace)) {
    ...
}
```