# 堆 && 优先级队列

## binary heap

binary heap本质是complete binary tree，没有漏洞，紧凑数据结构，使用vector存储节点

- make_heap：构造堆，将一个可迭代容器变成一个堆，第三个参数缺省为less<>（大项堆），可配置greater<>（小项堆）
- push_heap：插入操作，新插入的元素在数组尾部（堆的最底层的最右侧），以小项堆为例，将新元素与父节点比较，若大于父节点则交换，一直到小于父节点或者到达根节点为止，这个过程叫做上溯。
- pop_heap：删除操作，以小项堆为例，将根节点与底部节点交换，然后为这个底部节点找到一个合适的位置，使这个二叉树满足原来堆的结构。
- sort_heap：将堆排序，排序后，序列将失去原先的特性，大项堆排序后是递增序列，小项堆排序后是递减序列。第三个参数要求与make_heap的第三个参数保持一致。

```c++
void test9() {
    vector<int> v = { 0,1,2,3,4,5,6 };
    make_heap(v.begin(), v.end()); //以vector为底层容器
    for (auto i : v)
    {
        cout << i << " "; // 6 4 5 3 1 0 2
    }
    cout << endl;
    v.push_back(7);
    push_heap(v.begin(), v.end());
    for (auto i : v)
    {
        cout << i << " "; // 7 6 5 4 1 0 2 3
    }
    cout << endl;
    pop_heap(v.begin(), v.end());
    cout << v.back() << endl; // 7 
    v.pop_back();
    for (auto i : v)
    {
        cout << i << " "; // 6 4 5 3 1 0 2
    }
    cout << endl;
    sort_heap(v.begin(), v.end());
    for (auto i : v)
    {
        cout << i << " "; // 0 1 2 3 4 5 6
    }
}
```

## priority_queue

优先级队列，一个有权值的queue，顶部入口，底部出口，元素顺序不按照插入顺序排列，按照堆的性质（大项堆、小项堆），缺省情况下为max_heap，底层容器是vector，按照heap的规则来管理底层容器实现。

```c++
template<class T, class Squence = vector<T>, 
class Compare = less<typename Sequence::value_tyoe>
class priority_queue{
    //……
protected:
	Sequence c;   // 底层容器
    Compare comp; // 元素大小比较标准
public:
    bool empty() const {
        return c.empty();
    }
    size_type size() const {
        return c.size();
    }
    const_reference top() const {
        return c.front();
    }
    void push(const value_type& x) {
        c.push_heap(x);
        push_heap(c.begin(), c.end(),comp);
    }
    void pop() {
        pop_heap(c.begin(), c.end(),comp);
        c.pop_back();
    }
};
```



```c++
void priority_queue_test() {
    vector<int> ia = { 0,4,1,2,3,6,5,8,7 };
    priority_queue<int> pq(ia.begin(), ia.end());
    cout << pq.size() << endl;   // 9
    for (int i = 0; i < pq.size(); i++) {
        cout << pq.top() << " "; // 8 8 8 8 8 8 8 8 8
    }
    cout << endl;
    while (!pq.empty()) {
        cout << pq.top() << ' '; // 8 7 6 5 4 3 2 1 0
        pq.pop();
    }
}
```

priority_queue的所有元素，进出都有一定的规则，只有queue顶端的元素（权值最高者），才有机会被外界取用，它没有遍历功能，也不提供迭代器。

