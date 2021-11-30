react diff算法：

react16版本，加入了fiber进行优化算法。

react使用fober之前存在的问题： react是调用内置的堆栈去执行。这样react会一直执行，执行到把所有的堆栈任务执行完才会停止。

为了解决这个问题， 就是想要可以中断堆栈的执行，并且调整堆栈帧。然而fiber就是为了达成这个效果。



fiber的本质就是协调过程分成一小块。一次执行一块，把运算结果保存，最后组合成map\reduce。然后时间允许，会继续执行下一块，否则中断，然后让浏览器的主线程休息，然后执行优先级高的代码。



react diff算法。（只做同级比较、元素类型不同会生成不同的节点数、开发者可以使用key\props来是否复用这个元素）

它有currentChild nextChild jsx（newChild）他们都是fiber结构。主要就是对比newChild和oldChild.  



fiber其实就是一个链表结构， 它包含 return\child\sibilng\state node , 分别代表的是： 父节点、子节点、兄弟节点、当前的节点。



fiber是将更新、渲染的过程分割成小块， diff主要是计算、递归。主要是diff算法的时候进行切片。

如何切片？按照fiber节点来进行执行一个循环。一个fiber节点其实就是一个虚拟dom。执行完一次之后，检查是否还有时间，执行下一次循环（子节点的循环、兄弟节点的循环）。没有则结束。



中断执行，当时间不够、或者有其他优先级比较高的任务被中断之后，会有一个tag标记，是被更新的。并将结果缓存起来。下次执行完优先级高的。判断是执行被中断，则继续执行。



父节点最终拿到的是一个完整的effect list。所以被中断并不会影响页面的渲染。



commit阶段是同步任务， 也没有必要将其进行切片，中断。这不利于页面的渲染，还可能导致页面的dom与内部的dom不一致。体验不好。





diff算法是在哪个步骤： 一共有三个步骤， 1、日程  2、协调、调和  3、渲染

diff算法一般在第二个步骤，也就是调和的过程。



存在三种情况：

1、newChild执行完，但是oldChild没有执行完。 那么剩下 的oldChild，都会被删除

2、oldChild执行完，但是newChild没有执行完。 剩下的newChild，就会当作是新增的节点

3、两个都没有执行完， 会遍历oldChild的key作为key, value作为value. 然后遍历newChild的key, 看看是否有相同的oldChild的key，有那就进行diff去比对。



diff最终结果： 就是会在reactDom的fiber的flags上，打上对应的增删改的标签。然后根据fiber的flag形成一个effectList链表。最后渲染的时候，就是通过effectList去执行渲染的更新dom的。



