# [732. My Calendar III](https://leetcode.com/problems/my-calendar-iii)

[中文文档](/solution/0700-0799/0732.My%20Calendar%20III/README.md)

## Description

<p>A <code>k</code>-booking happens when <code>k</code> events have some non-empty intersection (i.e., there is some time that is common to all <code>k</code> events.)</p>

<p>You are given some events <code>[start, end)</code>, after each given event, return an integer <code>k</code> representing the maximum <code>k</code>-booking between all the previous events.</p>

<p>Implement the <code>MyCalendarThree</code> class:</p>

<ul>
	<li><code>MyCalendarThree()</code> Initializes the object.</li>
	<li><code>int book(int start, int end)</code> Returns an integer <code>k</code> representing the largest integer such that there exists a <code>k</code>-booking in the calendar.</li>
</ul>

<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>

<pre>
<strong>Input</strong>
[&quot;MyCalendarThree&quot;, &quot;book&quot;, &quot;book&quot;, &quot;book&quot;, &quot;book&quot;, &quot;book&quot;, &quot;book&quot;]
[[], [10, 20], [50, 60], [10, 40], [5, 15], [5, 10], [25, 55]]
<strong>Output</strong>
[null, 1, 1, 2, 3, 3, 3]

<strong>Explanation</strong>
MyCalendarThree myCalendarThree = new MyCalendarThree();
myCalendarThree.book(10, 20); // return 1, The first event can be booked and is disjoint, so the maximum k-booking is a 1-booking.
myCalendarThree.book(50, 60); // return 1, The second event can be booked and is disjoint, so the maximum k-booking is a 1-booking.
myCalendarThree.book(10, 40); // return 2, The third event [10, 40) intersects the first event, and the maximum k-booking is a 2-booking.
myCalendarThree.book(5, 15); // return 3, The remaining events cause the maximum K-booking to be only a 3-booking.
myCalendarThree.book(5, 10); // return 3
myCalendarThree.book(25, 55); // return 3
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>0 &lt;= start &lt; end &lt;= 10<sup>9</sup></code></li>
	<li>At most <code>400</code> calls will be made to <code>book</code>.</li>
</ul>

## Solutions

<!-- tabs:start -->

### **Python3**

```python
class Node:
    def __init__(self, l, r):
        self.left = None
        self.right = None
        self.l = l
        self.r = r
        self.mid = (l + r) >> 1
        self.v = 0
        self.add = 0


class SegmentTree:
    def __init__(self):
        self.root = Node(1, int(1e9 + 1))

    def modify(self, l, r, v, node=None):
        if l > r:
            return
        if node is None:
            node = self.root
        if node.l >= l and node.r <= r:
            node.v += v
            node.add += v
            return
        self.pushdown(node)
        if l <= node.mid:
            self.modify(l, r, v, node.left)
        if r > node.mid:
            self.modify(l, r, v, node.right)
        self.pushup(node)

    def query(self, l, r, node=None):
        if l > r:
            return 0
        if node is None:
            node = self.root
        if node.l >= l and node.r <= r:
            return node.v
        self.pushdown(node)
        v = 0
        if l <= node.mid:
            v = max(v, self.query(l, r, node.left))
        if r > node.mid:
            v = max(v, self.query(l, r, node.right))
        return v

    def pushup(self, node):
        node.v = max(node.left.v, node.right.v)

    def pushdown(self, node):
        if node.left is None:
            node.left = Node(node.l, node.mid)
        if node.right is None:
            node.right = Node(node.mid + 1, node.r)
        if node.add:
            node.left.v += node.add
            node.right.v += node.add
            node.left.add += node.add
            node.right.add += node.add
            node.add = 0


class MyCalendarThree:

    def __init__(self):
        self.tree = SegmentTree()

    def book(self, start: int, end: int) -> int:
        self.tree.modify(start + 1, end, 1)
        return self.tree.query(1, int(1e9 + 1))


# Your MyCalendarThree object will be instantiated and called as such:
# obj = MyCalendarThree()
# param_1 = obj.book(start,end)
```

### **Java**

```java
class Node {
    Node left;
    Node right;
    int l;
    int r;
    int mid;
    int v;
    int add;
    public Node(int l, int r) {
        this.l = l;
        this.r = r;
        this.mid = (l + r) >> 1;
    }
}

class SegmentTree {
    private Node root = new Node(1, (int) 1e9 + 1);

    public SegmentTree() {

    }

    public void modify(int l, int r, int v) {
        modify(l, r, v, root);
    }

    public void modify(int l, int r, int v, Node node) {
        if (l > r) {
            return;
        }
        if (node.l >= l && node.r <= r) {
            node.v += v;
            node.add += v;
            return;
        }
        pushdown(node);
        if (l <= node.mid) {
            modify(l, r, v, node.left);
        }
        if (r > node.mid) {
            modify(l, r, v, node.right);
        }
        pushup(node);
    }

    public int query(int l, int r) {
        return query(l, r, root);
    }

    public int query(int l, int r, Node node) {
        if (l > r) {
            return 0;
        }
        if (node.l >= l && node.r <= r) {
            return node.v;
        }
        pushdown(node);
        int v = 0;
        if (l <= node.mid) {
            v = Math.max(v, query(l, r, node.left));
        }
        if (r > node.mid) {
            v = Math.max(v, query(l, r, node.right));
        }
        return v;
    }

    public void pushup(Node node) {
        node.v = Math.max(node.left.v, node.right.v);
    }

    public void pushdown(Node node) {
        if (node.left == null) {
            node.left = new Node(node.l, node.mid);
        }
        if (node.right == null) {
            node.right = new Node(node.mid + 1, node.r);
        }
        if (node.add != 0) {
            Node left = node.left, right = node.right;
            left.add += node.add;
            right.add += node.add;
            left.v += node.add;
            right.v += node.add;
            node.add = 0;
        }
    }
}

class MyCalendarThree {
    private SegmentTree tree = new SegmentTree();

    public MyCalendarThree() {

    }
    
    public int book(int start, int end) {
        tree.modify(start + 1, end, 1);
        return tree.query(1, (int) 1e9 +1);
    }
}

/**
 * Your MyCalendarThree object will be instantiated and called as such:
 * MyCalendarThree obj = new MyCalendarThree();
 * int param_1 = obj.book(start,end);
 */
```

### **C++**

```cpp
class Node {
public:
    Node* left;
    Node* right;
    int l;
    int r;
    int mid;
    int v;
    int add;

    Node(int l, int r) {
        this->l = l;
        this->r = r;
        this->mid = (l + r) >> 1;
        this->left = this->right = nullptr;
        v = add = 0;
    }
};

class SegmentTree {
private:
    Node* root;

public:
    SegmentTree() {
        root = new Node(1, 1e9 + 1);
    }

    void modify(int l, int r, int v) {
        modify(l, r, v, root);
    }

    void modify(int l, int r,int v, Node* node) {
        if (l > r) return;
        if (node->l >= l && node->r <= r)
        {
            node->v += v;
            node->add += v;
            return;
        }
        pushdown(node);
        if (l <= node->mid) modify(l, r, v, node->left);
        if (r > node->mid) modify(l, r, v, node->right);
        pushup(node);
    }

    int query(int l, int r) {
        return query(l, r, root); 
    }

    int query(int l, int r, Node* node) {
        if (l > r) return 0;
        if (node->l >= l && node-> r <= r) return node->v;
        pushdown(node);
        int v = 0;
        if (l <= node->mid) v = max(v, query(l, r, node->left));
        if (r > node->mid) v = max(v, query(l, r, node->right));
        return v; 
    }

    void pushup(Node* node) {
        node->v = max(node->left->v, node->right->v);
    }

    void pushdown(Node* node) {
        if (!node->left) node->left = new Node(node->l, node->mid);
        if (!node->right) node->right = new Node(node->mid + 1, node->r);
        if (node->add)
        {
            Node* left = node->left;
            Node* right = node->right;
            left->v += node->add;
            right->v += node->add;
            left->add += node->add;
            right->add += node->add;
            node->add = 0;
        }
    }
};

class MyCalendarThree {
public:
    SegmentTree* tree;

    MyCalendarThree() {
        tree = new SegmentTree();
    }
    
    int book(int start, int end) {
        tree->modify(start + 1, end, 1);
        return tree->query(1, 1e9 + 1);
    }
};

/**
 * Your MyCalendarThree object will be instantiated and called as such:
 * MyCalendarThree* obj = new MyCalendarThree();
 * int param_1 = obj->book(start,end);
 */
```

### **...**

```

```

<!-- tabs:end -->
