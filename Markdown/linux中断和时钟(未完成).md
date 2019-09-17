# Linux中断与时钟笔记

### 中断与定时器
##### 中断分类
按中断来源

* 内部中断
* 外部中断

按中断入口跳转方法

* 向量中断
* 非向量中断
	非向量中断即，多个中断共用一个入口，再由软件判断具体执行中断服务程序
		

##### 定时器
&emsp;定时器也是依赖中断实现，内部计数比较，达到预定值时，产生中断
##### ARM常用中断控制器
&emsp;ARM常用中断控制器是GIC，支持软件中断、某cpu的私有外设中断、共享外设中断
### Linux中断处理程序架构
##### 顶半部和底半部机制
&emsp;设备的中断会打断内核进程中的正常调度和运行，降低系统的吞吐率。所以内核中在不影响外部响应的同时，应该尽可能的减少中断的时间。Linux中采用了底半部加顶半部的机制来解决这个问题。
&emsp;将耗时少的，紧急的硬件操作放置与顶半部中，将耗时长的、不紧急的操作放置于底半部。顶半部往往只是简单的读取寄存器中的中断状态，并清除中断状态，登记中断，大部分的中断处理工作都在底半部完成。

### Linux中断编程

##### 申请IRQ

int request_irq()

int devm_request()

##### 使能和屏蔽中断

- void disable_irq(int irq) 

  关闭指定中断，关闭中断前会等待中断完成

- void disable_irq_nosync(int irq)

  关闭指定中断，关闭中断并立即返回

- void enable_irq(int irq)

  使能指定中断

- local_irq_save(unsigned long flags)

  关闭本cpu上所有中断

- local_irq_restore(unsigned long flags)

##### tasklet

​	tasklet运行与软中断上下文。使用模板如下：

```c
void xxx_do_tasklet(unsigned long);
DECLARE_TASKLET(xxx_task, xxx_do_tasklet);

void xxx_do_tasklet(unsigned long)
{
    ...
}

顶半部()
{
    tasklet_schedule(&xxx_tasklet);
}
```

