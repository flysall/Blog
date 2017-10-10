---
title: 【读书笔记】红黑树--删除
date: 2017-10-10 13:53:45
tags: Algorithm
---
红黑树以一种特殊的二叉搜索树，它很好地解决了普通二叉搜索树可能出现树的深度过大的问题，这主要是通过以下五点限制来实现的。
<!-- more -->
## 红黑树性质
1. 节点是红色或者黑色。
2. 根节点是黑色。
3. 所有的叶节点（NIL）都是黑色。
4. 如果一个节点是红色的，则它的两个子节点都是黑色。
5. 从任一节点到其每个叶节点的所有简单路径都包含相同数目的黑色节点。

一棵典型的红黑树如下所示：
{% asset_img 1.png 红黑树(摘自维基百科) %}

## 红黑树常见操作
与普通的二叉搜索树一样，红黑树支持旋转、插入、删除等操作，因为要保持红黑树的性质，所以会复杂不少，其中最复杂的就是红黑树的删除操作，明白了删除操作，插入自然不在话下。本篇文章主要讨论红黑树的删除操作，因为会涉及到旋转操作，所以先来看看旋转操作，过程非常简单，一图明了：
{% asset_img 2.PNG 树的旋转(摘自算法导论) %}
**左旋伪代码实现**
{% codeblock %}
LEFT-ROTATE(T, x)
    y = x.right
    x.right = y.left
    if y.left != T.nil
        y.left.p = x
    y.p = x.p
    if x.p == T.nil
        T.root = y
    elseif x == x.p.left
        x.p.left = y
    else 
        x.p.right = y
        y.left = x
        y.left = x
        x.p = y
{% endcodeblock %}

## 红黑树的删除
与普通的二叉搜索树不同的是，删除节点可能会导致其违背红黑树的性质，我们可以通过一定的辅助操作对其抢救一下，先来看看红黑树的替换操作。
### 删除操作伪代码代码实现
{% codeblock %}
RB-TRANSPLANT(T, u, v)
    if u.p == T.nil
        T.root = v
    elseif u == u.p.left
        u.p.left = v
    else
        u.p.right = v
    v.p = u.p
{% endcodeblock %}
即用树v替换T的子树u，下面是红黑树的删除代码：
{% codeblock %}
R-B-DELETE(T, z)
    y = z
    y-original-color = y.color
    if z.left == T.nil
        x = z.right
        RB-TRANSPLANT(T, z, z.right)
    elseif z.right == T.nil
        x = z.left
        RB-TRANSPLANT(T, z, z.left)
    else
        y = TREE-MINIMUM(z.right)   //用于找到z的后继y
        y-original-color = y.color
        x = y.right
        if y.p == z
            x.p = y
        else
            RB-TRANSPLANT(T, y, y.right)
            y.right = z.right
            y.right.p = y
        RB-TRANSPLANT(T, z, y)
        y.left = z.left
        y.left.p = y
        y.color = z.color
    if y-original-color == BLACK
        RB-DELETE-FIRXUP(T, x)
{% endcodeblock %}
### 删除操作代码解析
我们用y-original-color来保存y原来的颜色，`R-B-DELETE(T, z)`的`4-9`行代码处理的是
节点`z`只有一个孩子的情形。`11-23`行代码处理的是`z`有两个孩子的情况：`11-13`行代码用于找到`z`的后继`y`,并用`y-original-color`保存其原有颜色，`x`用于指向`y`的右孩子（**可能为`NIL`**)，注意的是`y`作为`z`的后继，`y`的左孩子**必为`NIL`**。

如果`y.p == z`,则说明`y`为`z`的右孩子，否则`y`为`z`的右子树中序遍历的第一个元素。

第`24`判断`y`原来的颜色`y-original-color`, 此时`y`取代了`z`地位置，`x`取代了`y`的位置，我们以原来的树中`y`为`z`的右子树中序遍历的第一个元素为这种情况来分析，参考如下图片：
{% asset_img 3.png 红黑树删除操作 %}
先说结论：**删除操作后，红黑树性质被破坏的唯一可能区域是上图中的矩形区域**，这是因为第`23`行代码执行了`y.color = z.color`, 所以除去矩形区域之外的所有节点的颜色都未曾改变。如果`y-original-color`为红，则整棵红黑树的性质没有被破坏，因为`y`为红，则`x`和`p`的颜色必定为黑（原来的树满足红黑树的性质）；如果`y-original-color`为黑，则矩形区域会因为`y`的离去而丢失一个黑色节点，直接后果是通过矩形区域的简单路径黑色节点的数目减一，从而破坏红黑树性质5，此外还有可能`p`和`x`均为红色节点，从而导致红色节点的孩子为也为红色节点，破坏性质4。此时我们可以通过第`25`行的`RB-DELETE-FIXUP(T, x)`对矩形区域进行修复以维持红黑树性质。

## 修复
从上面可以看到，红黑树性质遭到破坏，我们通过`RB-DELETE-FIXUP(T, x)`操作对其修复。`RB-DELETE-FIXUP(T, x)`**使得通过`x`的简单路径上的黑色节点数增一，同时维持红色节点的两个孩子节点必须为黑色这一性质**。
### 修复操作伪代码
{% codeblock %}
RB-DELETE-FIXUP(T, x)
    while x ！= T.root and x.color == BLACK
        if x == x.p.left
            w = x.p.right
            if w.color == RED
                w.color = BLACKK            // case1
                x.p.color = RED             // case1
                LEFT-ROTATE(T, x.p)         // case1
                w = x.p.right               // case1
            if w.left.color == BLACK and w.right.color == BLACK
                w.color = RED               // case2
                x = x.p                     // case2
            else 
                if w.right.color == BLACK   // case3
                    w.left.color = BLACK    // case3
                    w.color = RED           // case3
                    RIGHT-ROTATE(T, w)      // case3
                    w = x.p.right           // case3
                w.color = x.p.color         // case4
                x.p.color = BLACK           // case4
                w.right.color = BLACK       // case4
                LEFT-ROTATE(T, x.p)         // case4
                x = T.root                  // case4
        else(same as then clause with "right" and "left" exchangeed)
    x.color = BLACK
{% endcodeblock %}
### 修复代码解析
如果`x`节点的颜色为红，这种情况非常简单，跳过循环，执行第`25`行代码直接将其涂黑即可使得通过矩形区域的简单路径黑色节点数增一，从而满足红黑性质5，而且不会出现`x`和`x`的父节点均为红色的情形。
如果`x`节点的颜色为黑，则不会出现`x`和`x`的父节点均为红色的情形，我们只需要想办法让树满足红黑性质5即可，分为下图的四种情况，图中，**通过用矩形画出的区域的简单路径的黑色节点数比其他简单路径少1**：
{% asset_img 4.jpg  红黑树修复(摘自算法导论) %}
1. **`x`的兄弟节点为红色节点**：则`x`的父节点必然不为红色，通过一次重新着色、左旋可以将其转换为`x`的兄弟转换为黑色节点（可能对应情况2、3、4中的任意一种），此时矩形区域外的简单路径上的黑色节点不变，通过矩形区域的简单路径上的黑色节点仍然比其他简单路径少1。
2. **`x`的兄弟节点`w`是黑色，且`w`的两个孩子为黑色**：将`w`涂红，`x`指向它原来的父节点，此时通过`x`孩子的简单路径（大矩形区域）黑色节点比其他简单路径少1，若此时`x`为红色节点，则循环不能继续，执行第`25`行代码，将`x`涂黑，通过矩形区域的所有简单路径黑色节点数增1，满足红黑性质5； 若`x`此时为黑色，满足循环条件，**继续执行循环**。
3. **`x`的兄弟节点`w`为黑色，且`w`的左孩子为红色，右孩子为黑色**：交换`w`和`w.left`的颜色，并对`w`进行一次右旋，此时变为情况4，通过矩形区域的简单路径上的黑色节点数比其他简单路径少1。
4. **`x`的兄弟节点`w`为黑色，且`w`的右孩子为红色**： 通过如图中所示颜色改变，以及一次左旋，使得通过矩形区域的简单路径上的黑色节点数目增1，而其他简单路径上黑色节点数目不变，从而满足红黑性质5，通过第`23`行代码，使得`x`指向根节点来终止循环，`x`本来就为黑色，第`25`行将`x`涂为黑色不改变任何红黑树性质。

通过以上步骤，一棵红黑树再一次出现在了我们面前。

            


