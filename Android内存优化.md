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
### 4.常见的内存泄漏原因
	1、慎重使用static变量
	2、长周期内部类、匿名内部类长时间持有外部类引用导致相关资源无法释放（Handler或者内部线程等）
	3、BitMap导致内存溢出
	4、数据库、文件流等没有关闭
	5、监听器、广播注册后没有及时注销
	6、Adapter没有使用convertView
	7、字符串拼接尽量使用StringBuilder或者StringBuffer
	8、避免内存抖动，例如不要在onDraw中创建对象。
	9、界面不可见时，停止动画和相关线程
	10、资源文件需要选择合适的文件夹进行存放
	我们知道hdpi/xhdpi/xxhdpi等等不同dpi的文件夹下的图片在不同的设备上会经过scale的处理。例如我们只在hdpi的目录下放置了一张100100的图片，那么根据换算关系，xxhdpi的手机去引用那张图片就会被拉伸到200200。需要注意到在这种情况下，内存占用是会显著提高的。对于不希望被拉伸的图片，需要放到assets或者nodpi的目录下
	11、优化布局层次，减少内存消耗越扁平化的视图布局，占用的内存就越少，效率越高。我们需要尽量保证布局足够扁平化，当使用系统提供的View无法实现足够扁平的时候考虑使用自定义View来达到目的


