### 链表
#### 1. 线程与进程区别
       

---
### 树      
#### 1.二叉树的前中后三种非递归遍历
思路： 非递归版本考虑用栈和堆实现
前序：
```js
void go(Node* t){
	Stack<Node* > stack;
    if(t==null)return;
	stack.push(t); 
	while(!stack.empty()){
    	temp=stack.pop();
    	visit(temp);
    	if(temp.right)
    		stack.push(temp.right);
    	if(temp.left)
    		stack.push(temp.left);
    }
}
```
---
### 图


---