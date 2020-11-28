# Spring之ShutDownHook

我们的java程序运行在JVM上，有很多情况可能会突然崩溃掉，比如OOM、用户强制退出、业务其他报错。。。等一系列的问题可能导致我们的进程挂掉。如果我们的进程在运行一些很重要的内容，比如事务操作之类的，很有可能导致事务的不一致性问题。所以，实现应用的优雅关闭还是蛮重要的，起码我们可以在关闭之前做一些记录补救操作。

# Java程序中实现

在java程序中，可以通过添加关闭钩子函数，实现在程序退出时关闭资源、平滑退出的功能。

  主要就是通过Runtime.addShutDownHook(Thread hook)来实现的。下面我们来简单看一个示例

~~~java
// 创建HookTest，我们通过main方法来模拟应用程序
public class HookTest {
    public static void main(String[] args) {
        // 添加hook thread，重写其run方法
        Runtime.getRuntime().addShutdownHook(new Thread(() -> System.out.println("this is hook demo...")));

        throw new RuntimeException("some Exception");
    }
}
~~~



~~~
// res
Exception in thread "main" java.lang.ArithmeticException: / by zero
	at hook.HookTest.main(HookTest.java:23)
this is hook demo...
 
Process finished with exit code 1
~~~

总结：我们主动写了一个报错程序，在程序报错之后，钩子函数还是被执行了。经验证，我们是可以通过对Runtime添加钩子函数来做到优雅停机。

# 应用场景

  既然JDK提供的这个方法可以注册一个JVM关闭的钩子函数，那么这个函数在什么情况下会被调用呢？上述我们展示了在程序异常情况下会被调用，还有没有其他场景呢？

  \* 程序正常退出

  \* 使用System.exit()

  \* 终端使用Ctrl+C触发的中断

  \* 系统关闭

  \* OutofMemory宕机

  \* 使用Kill pid杀死进程（使用kill -9是不会被调用的）

# Spring添加钩子函数

  1）Spring添加钩子函数比较简单，如下

```java
// 通过这种方式来添加钩子函数
ApplicationContext.registerShutdownHook();
 
// 通过源码可以看到，
@Override
public void registerShutdownHook() {
    if (this.shutdownHook == null) {
        // No shutdown hook registered yet.
        this.shutdownHook = new Thread() {
            @Override
            public void run() {
                synchronized (startupShutdownMonitor) {
                    doClose();
                }
            }
        };
        // 也是通过这种方式来添加
        Runtime.getRuntime().addShutdownHook(this.shutdownHook);
    }
}
 
// 重点是这个doClose()方法
protected void doClose() {
    // Check whether an actual close attempt is necessary...
    if (this.active.get() && this.closed.compareAndSet(false, true)) {
        if (logger.isInfoEnabled()) {
            logger.info("Closing " + this);
        }
 
        LiveBeansView.unregisterApplicationContext(this);
 
        try {
            // Publish shutdown event.
            publishEvent(new ContextClosedEvent(this));
        }
        catch (Throwable ex) {
            logger.warn("Exception thrown from ApplicationListener handling ContextClosedEvent", ex);
        }
 
        // Stop all Lifecycle beans, to avoid delays during individual destruction.
        if (this.lifecycleProcessor != null) {
            try {
                this.lifecycleProcessor.onClose();
            }
            catch (Throwable ex) {
                logger.warn("Exception thrown from LifecycleProcessor on context close", ex);
            }
        }
 
        // Destroy all cached singletons in the context's BeanFactory.
        destroyBeans();
 
        // Close the state of this context itself.
        closeBeanFactory();
 
        // Let subclasses do some final clean-up if they wish...
        onClose();
 
        // Switch to inactive.
        this.active.set(false);
    }
}
```

  可以看到：doClose()方法会执行bean的destroy()，也会执行SmartLifeCycle的stop()方法，我们就可以通过重写这些方法来实现对象的关闭，生命周期的管理，实现平滑shutdown.

**测试**

~~~java
ConfigurableApplicationContext applicationContext = SpringApplication.run(Demo1Application.class, args);
applicationContext.registerShutdownHook();
~~~

SmartLifeCycleDemo参考：

~~~
// 我们可以对比下注册钩子与不注册的区别：
// 1）不注册，只创建容器，结果如下：
start // 只输出start，说明只执行了SmartLifeCycleDemo.start()方法

// 2）注册钩子
start
stop(Runnable) // 当main方法执行结束时，主动执行了SmartLifeCycleDemo.stop()方法
~~~



通过调用ApplicationContext.registerShutdownHook()来注册钩子函数，可以在程序停止前执行我们自定义的各种destroy()或者stop()方法，实现bean的destroy，Spring容器的关闭，通过实现这些方法来实现平滑关闭。

  注意：我们当前讨论的都是Spring非Web程序，如果是Web程序的话，不需要我们来注册钩子函数，Spring的Web程序已经有了相关的代码实现优雅关闭了。