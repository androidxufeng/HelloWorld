## 内存优化
### 1.垃圾回收的职责
* 分配内存
* 确保任何被引用的对象保留在内存
* 回收不能通过引用找到对象的内存
#### 1.2垃圾回收
 垃圾回收器中有一个进程来做上面的这些事情, 这个进程查找我们的对象引用的关系并释放其内存, 这个进程就是garbage collection(垃圾回收), 也就是我们常说的GC，主要是针对Heap区域。stack是随时销毁的
#### 1.3 活对象、垃圾
如果这个对象是引用可达的, 则称之为**活的(live)**, 反之, 如果这个对象引用不可达, 则称之为死的(dead), 也可以称之为**垃圾(garbage)**.

### 2.java的内存管理机制
#### 2.1 关于JVM
* Young Generation
 新生代.
 所有new的对象.
 该区域的内存管理使用minor garbage collection(小GC).
 更进一步分成Eden space, Survivor 0 和 Survivor 1 三个部分.
* Old Generation
    老年区.
    新生代中执行小粒度的GC幸存下来的"老"对象.
    该区域的内存管理使用major garbage collection(大GC).
* Permanent Generation
 持久代.
 包含应用的类/方法信息, 以及JRE库的类和方法信息.

### 3.Android中的内存管理机制
	VSS - Virtual Set Size 虚拟耗用内存（包含共享库占用的内存）
	RSS - Resident Set Size 实际使用物理内存（包含共享库占用的内存）
	PSS - Proportional Set Size 实际使用的物理内存（比例分配共享库占用的内存）
	USS - Unique Set Size 进程独自占用的物理内存（不包含共享库占用的内存）
	一般来说内存占用大小有如下规律：VSS >= RSS >= PSS >= USS
* 
