---
title: AVL树
tags: [DataStructure , Tree]
---

### 一.AVL树

- AVL树：平衡二叉搜索树

- 平衡因子：某节点的左右子树的高度差
- 特点：
  - 每个节点的平衡因子只可能是1,0，-1(绝对值<=1)
  - 搜索，添加，删除的时间复杂度是O(logn)
  - 可继承自BST

- 需要在节点类里添加height属性

### 二.添加导致的失衡

- 最坏情况：所有祖先节点都失衡
- 父节点，非祖先节点不会失衡
- g节点：失衡的高度最小的节点
- p节点：g的左右孩子节点中高度较大的节点
- n节点：p的左右孩子节点中高度较大的节点

#### 1.LL(右单旋)

- LL：Left-Left，失衡节点左侧再左侧的节点的子树添加节点导致的失衡

![LL](D:\blog\source\_posts\AVL树\LL.png)

- g.left = p.right且p.right = g
- T2，p，g的parent属性需要维护
- 更新g，p的高度

```java
    private void rotateRight(Node<E> grand){
        Node<E> parent = grand.left;
        Node<E> child = parent.right;
        grand.left = child;
        parent.right = grand;
        afterRotate(grand,parent,child);
    }
```



#### 2.RR(左单旋)

- RR：Right-Right，失衡节点右侧再右侧的节点的子树添加节点导致的失衡

![RR](D:\blog\source\_posts\AVL树\RR.png)

- g.right = p.left且p.left = g
- T1，p，g的parent属性需要维护
- 更新g，p的高度

```java
	private void rotateLeft(Node<E> grand){
        Node<E> parent = grand.right;
        Node<E> child = parent.left;
        grand.right = parent.left;
        parent.left = grand;

        afterRotate(grand,parent,child);
    }
```



#### 3.LR(左右双旋)

- LR：Left-Right，失衡节点左侧再右侧的节点的子树添加节点导致的失衡

![LR](D:\blog\source\_posts\AVL树\LR.png)

- 先对p做左单旋，再对g做右单旋

#### 4.RL(右左双旋)

- RL：Right-Left，失衡节点右侧再左侧的节点的子树添加节点导致的失衡

![RL](D:\blog\source\_posts\AVL树\RL.png)

- 先对p做右单旋，再对g做左单旋

#### 5.更新节点数据

- 在添加节点后需要更新每个节点的高度，如果添加完节点后平衡，则直接更新；如果添加完节点后失衡，先旋转恢复平衡，然后更新节点高度

```java
    @Override
    protected void afterAdd(Node<E> node) {
        while ((node = node.parent)!=null){
            if (isBalanced(node)){
                //更新高度
                updateHeight(node);
            }else {
                //恢复平衡
                rebalance(node);
                break;
            }
        }
    }
```

```java
	private void afterRotate(Node<E> grand, Node<E> parent, Node<E> child){
        //更新父节点,使parent成为子树的根节点
        parent.parent = grand.parent;
        if (grand.isLeftChild()){
            grand.parent.left = parent;
        }else if (grand.isRightChild()){
            grand.parent.right = parent;
        }else {
            //grand根节点
            root = parent;
        }
        //更新child的parent
        if (child!=null){
            child.parent = grand;
        }
        //更新grand的parent
        grand.parent = parent;
        //更新高度
        updateHeight(grand);
        updateHeight(parent);
    }
```



### 三.删除导致的失衡

- 只会导致父节点或祖先节点失衡(只有一个节点失衡)
- 让父节点恢复平衡之后，可能会导致更高层的祖先节点失衡

#### 1.LL(右单旋)

- 如果绿色节点不存在，则g的父节点或某个祖先节点可能会因此失衡。而这种失衡可能进一步导致再往上的祖先节点以相同的原因失衡

![LL](D:\blog\source\_posts\AVL树\RemoveLL.png)

#### 2.RR(左单旋)

![RR](D:\blog\source\_posts\AVL树\RemoveRR.png)

#### 3.LR(左右双旋)

![LR](D:\blog\source\_posts\AVL树\RemoveLR.png)

#### 4.RL(右左双旋)

![RL](D:\blog\source\_posts\AVL树\RemoveRL.png)

#### 5.核心逻辑

```java
	@Override
    protected void afterRemove(Node<E> node) {
        //删除逻辑是覆盖，即改变被删除节点父节点的左右指针指向，所以node.parent不会是空
        //被删除的节点所占用的内存空间会因为没有对象指向它而被GC回收
        while ((node = node.parent)!=null){
            if (isBalanced(node)){
                //更新高度
                updateHeight(node);
            }else {
                //恢复平衡
                //和添加的唯一区别就是调整平衡后不需要break，因为后面还可能有失衡的节点
                rebalance(node);
            }
        }
    }
```



### 四.时间复杂度

- 平均时间复杂度
  - 搜索：O(logn)
  - 添加：O(logn)，仅需O(1)次旋转操作
  - 删除：O(logn)，最多需要O(logn)次的旋转操作