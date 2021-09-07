## 栈
栈是一种具有【先进后出】特性的数据结构，通常于辅助实现非递归算法，如DFS。

## 队列
队列是一种具有【先进先出】特性的数据结构，用于实现BFS算法。

### 用队列实现栈
只用一个队列实现栈的功能：压入、弹出、取栈顶元素

```cpp
class MyStack {
    queue<int> q;
public:
    /** Initialize your data structure here. */
    MyStack() {

    }
    
    /** Push element x onto stack. */
    void push(int x) {
        int n = (int)q.size();
        q.push(x);
        for (int i = 0; i < n; i++) {
            q.push(q.front());
            q.pop();
        }
    }
    
    /** Removes the element on top of the stack and returns that element. */
    int pop() {
        int ans = q.front();
        q.pop();
        return ans;
    }
    
    /** Get the top element. */
    int top() {
        return q.front();
    }
    
    /** Returns whether the stack is empty. */
    bool empty() {
        return q.empty();
    }
};
```
时间复杂度：push 是 $O(n)$，top 和 pop 都是 $O(1)$，空间复杂度：$O(n)$