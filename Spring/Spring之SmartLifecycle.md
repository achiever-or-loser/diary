# Spring之SmartLifecycle

如果业务上需要在spring容器启动和关闭的时候做一些操作，可以自定义SmartLifecycle接口的实现类来扩展。



![image-20201125144355429](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201125144355429.png)

如上图所示，在继承了Lifecycle和Phased两个接口后，SmartLifecycle一共定义了六个方法，为了便于后面的源码分析，先做个简介：

| 方法            | 作用                                                         |
| --------------- | ------------------------------------------------------------ |
| start()         | bean初始化完毕后，该方法会被执行                             |
| stop()          | 容器关闭后： spring容器发现当前对象实现了SmartLifecycle，就调用stop(Runnable)， 如果只是实现了Lifecycle，就调用stop() |
| isRunning()     | 当前状态                                                     |
| getPhase()      | 返回值决定start方法在众多Lifecycle实现类中的执行顺序(stop也是) |
| isAutoStartup() | start方法被执行前先看此方法返回值，返回false就不执行start方法了 |
| stop(Runnable)  | 容器关闭后： spring容器发现当前对象实现了SmartLifecycle，就调用stop(Runnable)， 如果只是实现了Lifecycle，就调用stop() |

从上述列举中可以看出，感知容器变化的能力最终来自Lifecycle，而SmartLifecycle只是Lifecycle的增强版，可以自定义优先级（getPhase），自主决定是否随容器启动（isAutoStartup），以及停止时能接受一个runnable对象（stop(Runnable)）；



# 实例

~~~
@Component
public class SmartLifeCycleDemo implements SmartLifecycle {

    private boolean isRunning = false;

    /**
     * 1. 我们主要在该方法中启动任务或者其他异步服务，比如开启MQ接收消息<br/>
     * 2. 当上下文被刷新（所有对象已被实例化和初始化之后）时，将调用该方法，
     * 默认生命周期处理器将检查每个SmartLifecycle对象的isAutoStartup()方法返回的布尔值。
     * 如果为“true”，则该方法会被调用，而不是等待显式调用自己的start()方法。
     */
    @Override
    public void start() {
        System.out.println("start");
        // 执行完其他业务后，可以修改 isRunning = true
        isRunning = true;
    }

    /**
     * 如果工程中有多个实现接口SmartLifecycle的类，则这些类的start的执行顺序按getPhase方法返回值从小到大执行。<br/>
     * 例如：1比2先执行，-1比0先执行。 stop方法的执行顺序则相反，getPhase返回值较大类的stop方法先被调用，小的后被调用。
     */
    @Override
    public int getPhase() {
        // 默认为0
        return 0;
    }

    /**
     * 根据该方法的返回值决定是否执行start方法。<br/>
     * 返回true时start方法会被自动执行，返回false则不会。
     */
    @Override
    public boolean isAutoStartup() {
        // 默认为false
        return true;
    }

    /**
     * 1. 只有该方法返回false时，start方法才会被执行。<br/>
     * 2. 只有该方法返回true时，stop(Runnable callback)或stop()方法才会被执行。
     */
    @Override
    public boolean isRunning() {
        // 默认返回false
        return isRunning;
    }

    /**
     * SmartLifecycle子类的才有的方法，当isRunning方法返回true时，该方法才会被调用。
     */
    @Override
    public void stop(Runnable callback) {
        System.out.println("stop(Runnable)");
        // 如果你让isRunning返回true，就需要执行stop这个方法，就要调用callback.run();
        // 否则在你程序退出时，Spring的DefaultLifecycleProcessor会认为你这个TestSmartLifecycle没有stop完成，
        // 程序会一直卡着结束不了，等待一定时间（默认超时时间30秒）后才会自动结束。
        //callback中有个CountDownLatch实例，总数是SmartLifecycle对象的数量，
        //此方法被回调时CountDownLatch实例才会减一，初始化容器的线程一直在wait中.

        // PS：修改这个默认超时时间，可以定义DefaultLifecycleProcessor设置超时时间
//        @Bean("lifecycleProcessor")  //需指定bean的id：lifecycleProcessor
//        public DefaultLifecycleProcessor initDefaultLifeCycleProcessor(){
//            DefaultLifecycleProcessor defaultLifecycleProcessor=new DefaultLifecycleProcessor();
//            defaultLifecycleProcessor.setTimeoutPerShutdownPhase(20000L);
//
//            return defaultLifecycleProcessor;
//        }

        callback.run();
        isRunning = false;
    }

    /**
     * 接口Lifecycle的子类的方法，只有非SmartLifecycle的子类才会执行该方法。<br/>
     * 1. 该方法只对直接实现接口Lifecycle的类才起作用，对实现SmartLifecycle接口的类无效。<br/>
     * 2. 方法stop()和方法stop(Runnable callback)的区别只在于，后者是SmartLifecycle子类的专属。
     */
    @Override
    public void stop() {
        System.out.println("stop");
        isRunning = false;
    }
}
~~~



https://blog.csdn.net/boling_cavalry/article/details/82051356