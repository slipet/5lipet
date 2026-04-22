#動態開點

```cpp
#include <iostream>
#include <algorithm>
#include <climits>

using namespace std;

// 設定預設值，若求最大值且包含負數，可設為 INT_MIN
const int ST_NODE_DEFAULT_VAL = 0;

struct stNode {
    stNode *lo, *ro;
    int l, r;
    int val;

    // 靜態哨兵節點，類似 Go 的 emptyStNode
    static stNode* empty;

    stNode(int _l, int _r, int _val = ST_NODE_DEFAULT_VAL) 
        : l(_l), r(_r), val(_val) {
        lo = ro = empty;
    }

    // 這裡為了簡化，提供一個靜態初始化方法
    static void initEmpty() {
        if (!empty) {
            empty = new stNode(0, 0, ST_NODE_DEFAULT_VAL);
            empty->lo = empty->ro = empty;
        }
    }

    int mergeInfo(int a, int b) {
        return max(a, b);
    }

    void maintain() {
        // 因為有哨兵節點，不需要判斷 lo/ro 是否為空
        val = mergeInfo(lo->val, ro->val);
    }

    void update(int i, int v) {
        if (l == r) {
            val = mergeInfo(val, v);
            return;
        }
        
        int m = l + (r - l) / 2; // 安全的下取整中點計算
        if (i <= m) {
            if (lo == empty) {
                lo = new stNode(l, m);
            }
            lo->update(i, v);
        } else {
            if (ro == empty) {
                ro = new stNode(m + 1, r);
            }
            ro->update(i, v);
        }
        maintain();
    }

    int query(int ql, int qr) {
        // 如果目前是哨兵節點，或者區間不交疊
        if (this == empty || ql > r || qr < l) {
            return ST_NODE_DEFAULT_VAL;
        }
        // 完全包含
        if (ql <= l && r <= qr) {
            return val;
        }
        return mergeInfo(lo->query(ql, qr), ro->query(ql, qr));
    }
};

// 初始化靜態成員
stNode* stNode::empty = nullptr;

// 測試範例
int main() {
    stNode::initEmpty();
    
    // 假設範圍是 [1, 1e9]
    stNode* root = new stNode(1, 1000000000);
    
    root->update(5, 10);
    root->update(10, 20);
    
    cout << "Query [1, 7]: " << root->query(1, 7) << endl;   // 10
    cout << "Query [7, 12]: " << root->query(7, 12) << endl; // 20
    cout << "Query [1, 100]: " << root->query(1, 100) << endl; // 20

    return 0;
}
```
```
ref: https://github.com/EndlessCheng/codeforces-go/blob/master/copypasta/segment_tree.go
var emptyStNode = &stNode{val: stNodeDefaultVal}

func init() {
	emptyStNode.lo = emptyStNode
	emptyStNode.ro = emptyStNode
}

const stNodeDefaultVal = 0 // 如果求最大值并且有负数，改成 math.MinInt

type stNode struct {
	lo, ro *stNode
	l, r   int
	val    int
}

func (stNode) mergeInfo(a, b int) int {
	return max(a, b)
}

func (o *stNode) maintain() {
	// 注意这里没有判断 o.lo 和 o.ro 是空的，因为用的 emptyStNode
	o.val = o.mergeInfo(o.lo.val, o.ro.val)
}

func (o *stNode) update(i, val int) {
	if o.l == o.r {
		o.val = o.mergeInfo(o.val, val)
		return
	}
	m := (o.l + o.r) >> 1 // 使用右移而不是除法，这样保证负数也是下取整     或者 o.l + (o.r - o.l) / 2
	if i <= m {
		if o.lo == emptyStNode {
			o.lo = &stNode{lo: emptyStNode, ro: emptyStNode, l: o.l, r: m, val: stNodeDefaultVal}
		}
		o.lo.update(i, val)
	} else {
		if o.ro == emptyStNode {
			o.ro = &stNode{lo: emptyStNode, ro: emptyStNode, l: m + 1, r: o.r, val: stNodeDefaultVal}
		}
		o.ro.update(i, val)
	}
	o.maintain()
}

func (o *stNode) query(l, r int) int {
	if o == emptyStNode || l > o.r || r < o.l {
		return stNodeDefaultVal
	}
	if l <= o.l && o.r <= r {
		return o.val
	}
	return o.mergeInfo(o.lo.query(l, r), o.ro.query(l, r))
}

// 询问范围的最小值、最大值
// 0 1e9
// -2e9 2e9
func newStRoot(l, r int) *stNode {
	return &stNode{lo: emptyStNode, ro: emptyStNode, l: l, r: r, val: stNodeDefaultVal}
}

// 动态开点线段树·其二·延迟标记（区间修改）
// https://codeforces.com/problemset/problem/1557/D 2200（内存受限）
// https://codeforces.com/problemset/problem/803/G 2300
// https://codeforces.com/problemset/problem/915/E 2300（注：此题有多种解法）
// https://www.luogu.com.cn/problem/P5848 https://codeforces.com/edu/course/2/lesson/5/4/practice/contest/280801/problem/F
var emptyLazyNode = &lazyNode{sum: lazyNodeDefaultVal, todo: lazyNodeDefaultTodoVal}

func init() {
	emptyLazyNode.lo = emptyLazyNode
	emptyLazyNode.ro = emptyLazyNode
}

const lazyNodeDefaultVal = 0
const lazyNodeDefaultTodoVal = 0

type lazyNode struct {
	lo, ro *lazyNode
	l, r   int
	sum    int // info
	todo   int
}

func (lazyNode) mergeInfo(a, b int) int {
	return a + b // max(a, b)
}

func (lazyNode) mergeTodo(a, b int) int {
	return a + b // max(a, b)
}

func (o *lazyNode) apply(f int) {
	o.sum += (o.r - o.l + 1) * f    // % mod
	o.todo = o.mergeTodo(f, o.todo) // % mod
}

func (o *lazyNode) maintain() {
	o.sum = o.mergeInfo(o.lo.sum, o.ro.sum)
}

func (o *lazyNode) spread() {
	m := (o.l + o.r) >> 1
	if o.lo == emptyLazyNode {
		o.lo = &lazyNode{lo: emptyLazyNode, ro: emptyLazyNode, l: o.l, r: m, sum: lazyNodeDefaultVal, todo: lazyNodeDefaultTodoVal}
	}
	if o.ro == emptyLazyNode {
		o.ro = &lazyNode{lo: emptyLazyNode, ro: emptyLazyNode, l: m + 1, r: o.r, sum: lazyNodeDefaultVal, todo: lazyNodeDefaultTodoVal}
	}
	if f := o.todo; f != lazyNodeDefaultTodoVal {
		o.lo.apply(f)
		o.ro.apply(f)
		o.todo = lazyNodeDefaultTodoVal
	}
}

func (o *lazyNode) update(l, r int, add int) {
	if l <= o.l && o.r <= r {
		o.apply(add)
		return
	}
	o.spread()
	m := (o.l + o.r) >> 1
	if l <= m {
		o.lo.update(l, r, add)
	}
	if m < r {
		o.ro.update(l, r, add)
	}
	o.maintain()
}

func (o *lazyNode) query(l, r int) int {
	if o == emptyLazyNode || l > o.r || r < o.l {
		return lazyNodeDefaultVal
	}
	if l <= o.l && o.r <= r {
		return o.sum
	}
	o.spread()
	lRes := o.lo.query(l, r)
	rRes := o.ro.query(l, r)
	return o.mergeInfo(lRes, rRes)
}

// 询问范围的最小值、最大值
// 0 1e9
// -2e9 2e9
func newLazyRoot(l, r int) *lazyNode {
	return &lazyNode{lo: emptyLazyNode, ro: emptyLazyNode, l: l, r: r, sum: lazyNodeDefaultVal, todo: lazyNodeDefaultTodoVal}
}
```