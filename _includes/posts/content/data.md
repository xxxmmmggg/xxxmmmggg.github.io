### 链表
#### 1. 线程与进程区别
       

---
### 树      
#### 1.二叉树的前中后三种递归和非递归遍历
```js
void go(Node* t){ //前序递归
	if(t==null)return;
	visit(t);
	go(t->left);
	go(t->right);
}
void go(Node* t){ //前序非递归：   
	Stack<Node*> stack;
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
void go(Node* t){   //中序非递归
	if(t==null)return;
	go(t->left);
	visit(t);
	go(t->right);
}
void go(Node* t){   //中序非递归  
	Stack<Node*> stack
	if(t==null)return;
	while(t||!stack.empty){
		while(t.left){
			stack.push(t);
			t=t.left;
		}
		t=t.pop();
		visit(t);
		t=t.right;
	}
}
void go(Node* t){   //后序递归
	if(t==null)return;
	go(t->left);
	go(t->right);
	visit(t);
}
void go(Node* t){	//后序非递归
	Stack<Node*> stack;
	if(t==null) return;
	Node* cur;
	Node* pre;
	stack.push(t);
	while(!stack.empty()){
		cur=stack.top()；
		if((cur->left==null&&cur->right==null)||(pre!=null&&(pre==cur->left||pre==cur-right))){
			visit(cur);
			stack.pop();
			pre=cur;
		}else{
			if(cur->right!=null)
				stack.push(cur);
			if(cur->left!=null)
				stack.push(cur);	
		}
	}
}  
```
---
### 图


---