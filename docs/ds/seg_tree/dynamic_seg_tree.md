#動態開點


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