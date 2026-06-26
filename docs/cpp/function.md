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

### sstream

[ref](https://home.gamer.com.tw/creationDetail.php?sn=4114818)

```cpp
#include <sstream>
#include <iostream>
using namespace std;
int main()
{
    ////////基本的初始化stringstream
    stringstream s1;
    s1.str("");
    s1.clear();
}
```

```cpp
istringstream ss(path);
string s;
while (getline(ss, s, '/')) {
    if (s.empty() || s == ".") {
        continue;
    }
    if (s != "..") {
        stk.push_back(s);
    } else if (!stk.empty()) {
        stk.pop_back();
    }
}
```

### 刪除重複元素
```cpp
v.erase(unique(v.begin(),v.end()),v.end());
s.erase(unique(s.begin(),s.end()),s.end(), \
				[](char a,char b){return a==' ' && a==b;});
				
//1.
sort( vec.begin(), vec.end() );
vec.erase( unique( vec.begin(), vec.end() ), vec.end() );
//2.
set<int> s( vec.begin(), vec.end() );
vec.assign( s.begin(), s.end() );
```

### nth_element

```cpp
vector<int> arr;
ranges::nth_element(arr, arr.end() - k); //k ~ end large
```

### binary search