vue react 虚拟dom的 diff 算法：

1、react  从左往右依次 去判断。（vue却会从两端往中间去判断）

如果节点元素类型相同，className不一样，react会认为是同类型节点，直接改变类名。（vue却认为是不同类型的元素，会重新渲染dom）



2、如果是一个集合，只是把最后一个移到第一个，react 会把前面的节点依次移动，而vue只会把最后一个节点移动到第一个。





react  整个过程分为4个阶段：

1. 第一遍遍历新fiber 如果相同  就可以复用节点，找到不可复用的直接退出循环
2. 第一遍 新节点已经遍历完成，如果还剩老节点，直接删除
3. 如果还有 老节点 没有了 新节点还有 或者 初次渲染 就直接插入
4. 如果新旧节点的位置 有移动，把oldFiber 按照key  或者 index 放到Map 里，然后遍历新的Fiber 看看有匹配的直接复用。





