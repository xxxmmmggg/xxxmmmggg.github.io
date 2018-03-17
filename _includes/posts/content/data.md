### 排序
#### 1.插入、冒泡、快排

```js  
void maopao(int[] array){     //冒泡排序
	int len=array.length;
	for(int i=0;i<len;i++){
		for(int j=len-1;j>i;j--){
			if(array[j]<array[j-1])
				swap(array[j],array[j-1]);
		}
	}
}

void quikSort(int[] array,int start,int end){    // 快速排序     
	int start=0;
	int end=array.length;
	int index=start;
	while(start<end){
		while(array[index]<=array[end])end--;
		if(array[index]>array[end]){
			swap(array,index,end);
			index=end;
		}
		while(array[index]>=array[start])start++;
		if(array[index]<array[start]){
			swap(array,index,start);
			index=start;
		}
	}
	if(sta<(index-1))go(array,start,index-1);
	if(end>(index+1))go(array,index+1,end);	
}
```
---

### 树      
#### 1.二叉树的前中后三种递归和非递归遍历
//前序递归+非递归
```js
void go(Node* t){ 
	if(t==null)return;
	visit(t);
	go(t->left);
	go(t->right);
}
void go(Node* t){   
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
```
//中序递归+非递归
```js
void go(Node* t){   
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
```
后序递归+非递归
```js
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
#### 2.判断一颗二叉树是否是平衡树
```js
boolean go(Node* t, int *l){
	if(t==null){
		l=0;
		return true;
	}
	int ll=0;
	int rl=0;
	boolean lb=go(t->left,&ll);
	boolean rb=go(l-right,&rl);
	if(lb&&rn){
		if(|rl-ll|<=1){
			l=max(ll,rl)+1;
			return true;
		}else{
			return false;
		}
	}
}
```
---
### 图


---