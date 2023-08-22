## 栈
栈是一种具有【先进后出】特性的数据结构，通常于辅助实现非递归算法，如DFS。

### 两个栈实现队列
```cpp
class MyQueue {
    stack<int> s1, s2;
public:
    /** Initialize your data structure here. */
    MyQueue() {

    }

    /** Push element x to the back of queue. */
    void push(int x) {
        s1.push(x);
    }

    /** Removes the element from in front of queue and returns that element. */
    int pop() {
        int ans = peek();
        s2.pop();
        return ans;
    }

    /** Get the front element. */
    int peek() {
        if (s2.empty()) {
            while (!s1.empty()) {
                s2.push(s1.top());
                s1.pop();
            }
        }
        return s2.top();
    }

    /** Returns whether the queue is empty. */
    bool empty() {
        return s1.empty() && s2.empty();
    }
};

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue* obj = new MyQueue();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->peek();
 * bool param_4 = obj->empty();
 */
```
时间复杂度：push 和 empty 为 $O(1)$，pop 和 peak 的均摊为 $O(1)$，空间复杂度：$O(n)$。


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