# SpringBoot之Transactional

Spring 事务管理分为编程式和声明式的两种方式。
编程式事务指的是通过编码方式实现事务；声明式事务基于 AOP,将具体业务逻辑与事务处理解耦。
声明式事务管理使业务代码逻辑不受污染, 因此在实际使用中声明式事务用的比较多。

声明式事务的实现有两种方式，一种是在配置文件（xml）中做相关的事务规则声明，另一种是基于@Transactional注解的方式。



# @Transactional注解属性

| 属性名        | 说明                                                         |
| :------------ | :----------------------------------------------------------- |
| value         | 当在配置文件中有多个 TransactionManager , 可以用该属性指定选择哪个事务管理器。 |
| propagation   | 事务的传播行为，默认值为 REQUIRED。                          |
| isolation     | 事务的隔离度，默认值采用 DEFAULT。                           |
| timeout       | 事务的超时时间，默认值为-1。如果超过该时间限制但事务还没有完成，则自动回滚事务。 |
| readOnly      | 指定事务是否为只读事务，默认值为 false；为了忽略那些不需要事务的方法，比如读取数据，可以设置 readOnly 为 true。 |
| rollbackFor   | 用于指定能够触发事务回滚的异常类型，如果有多个异常类型需要指定，各类型之间可以通过逗号分隔。 |
| noRollbackFor | 抛出 no-rollback-for 指定的异常类型，不回滚事务。            |

- value和transactionManager属性,它们两个是一样的意思。当配置了多个事务管理器时，可以使用该属性指定选择哪个事务管理器。


- propagation属性,事务的传播行为，默认值为Propagation.REQUIRED。可选的值有：

	- Propagation.REQUIRED 如果当前存在事务，则加入该事务，如果当前不存在事务，则创建一个新的事务。如a方法和b方法都添加了注解，使用默认传播模式，则a方法内部调用b方法，会把两个方法的事务合并为一个事务。这里就会存在问题，如果b方法内部抛了异常，而a方法catch了b方法的异常，那这个事务还能正常运行吗？答案是不行！会抛出异常：
org.springframework.transaction.UnexpectedRollbackException: Transaction rolled back because it has been marked as rollback-only，因为当ServiceB中抛出了一个异常以后，ServiceB会把当前的transaction标记为需要rollback。但是ServiceA中捕获了这个异常，并进行了处理，认为当前transaction应该正常commit。此时就出现了前后不一致，也就是因为这样，抛出了前面的UnexpectedRollbackException。Propagation.SUPPORTS
如果当前存在事务，则加入该事务；如果当前不存在事务，则以非事务的方式继续运行。
	- Propagation.MANDATORY 如果当前存在事务，则加入该事务；如果当前不存在事务，则抛出异常。
	- Propagation.REQUIRES_NEW 重新创建一个新的事务，如果当前存在事务，暂停当前的事务。这个属性可以实现：
类A中的a方法加上默认注解@Transactional(propagation = Propagation.REQUIRED)，类B中的b方法加上注解@Transactional(propagation = Propagation.REQUIRES_NEW)，然后在a方法中调用b方法操作数据库，再在a方法最后抛出异常，会发现a方法中的b方法对数据库的操作没有回滚，因为Propagation.REQUIRES_NEW会暂停a方法的事务。
	- Propagation.NOT_SUPPORTED 以非事务的方式运行，如果当前存在事务，暂停当前的事务。
	- Propagation.NEVER 以非事务的方式运行，如果当前存在事务，则抛出异常。
	- Propagation.NESTED 和 Propagation.REQUIRED 效果一样。

- isolation属性 事务的隔离级别，默认值为 Isolation.DEFAULT。

可选的值有： Isolation.DEFAULT 使用底层数据库默认的隔离级别。 Isolation.READ_UNCOMMITTED ,Isolation.READ_COMMITTED ,Isolation.REPEATABLE_READ,Isolation.SERIALIZABLE

- timeout属性：事务的超时时间，默认值为-1。如果超过该时间限制但事务还没有完成，则自动回滚事务。
- readOnly属性：指定事务是否为只读事务，默认值为 false；为了忽略那些不需要事务的方法，比如读取数据，可以设置 read-only 为 true。
- rollbackFor属性：用于指定能够触发事务回滚的异常类型，可以指定多个异常类型，（包括未检异常之外的异常）。
- noRollbackFor属性：抛出指定的异常类型，不回滚事务，也可以指定多个异常类型



# 位置

@Transactional可以作用于接口、接口方法、类以及类方法上。当作用于类上时，该类的所有public方法将都具有该类型的事务属性；同时，我们也可以在方法级别使用该标注来覆盖类级别的定义，但是方法级别的事务属性信息会覆盖类级别的相关配置信息。

虽然@Transactional注解可以作用于接口、接口方法、类以及类方法上，但是Spring建议不要在接口或者接口方法上使用该注解，因为只有在使用基于接口的代理时它才会生效。

# 回滚机制
Spring的事务管理默认是针对**unchecked exception**回滚，也就是默认对Error异常和RuntimeException异常以及其子类进行事务回滚，且必须抛出异常。若使用try-catch对其异常捕获则不会进行回滚！

（Error异常和RuntimeException异常抛出时不需要方法调用throws或try-catch语句）。而**checked exception** 则必须用try语句块进行处理或者把异常交给上级方法处理总之就是必须写代码处理它，所以必须在service捕获异常，然后再次抛出，这样事务方才起效。

![Transactional](./Transactional.jpg)

另外，@Transactional注解应该只被应用到public方法上，这是由Spring AOP的本质决定的。如果你在protected、private或者默认可见性的方法上使用@Transactional注解，这将被忽略，也不会抛出任何异常。

因为在使用 Spring AOP 代理时，Spring 在调用在图 1 中的 TransactionInterceptor 在目标方法执行前后进行拦截之前，DynamicAdvisedInterceptor（CglibAopProxy 的内部类）的 intercept 方法或 JdkDynamicAopProxy 的 invoke 方法会间接调用 AbstractFallbackTransactionAttributeSource（Spring 通过这个类获取@Transactional 注解的事务属性配置属性信息）的 computeTransactionAttribute 方法。

这个方法会检查目标方法的修饰符是不是 public，若不是 public，就不会获取@Transactional 的属性配置信息，最终会造成不会用 TransactionInterceptor 来拦截该目标方法进行事务管理。

# AOP 的自调用问题

在 Spring 的 AOP 代理下，只有目标方法由外部调用，目标方法才由 Spring 生成的代理对象来管理。也就是说，同一类中的其他没有@Transactional 注解的方法内部调用有@Transactional 注解的方法，有@Transactional 注解的方法的事务被忽略，不会发生回滚。这会造成自调用问题。

解决

使用 AspectJ 取代 Spring AOP 代理。待验证



https://www.cnblogs.com/goslingwu/p/13690434.html

一个类中的方法调用另一个事物传播性为创建事物的方法，调用的方法事物失效？
SpringAOP 代理的Service对象调用了其方法，这个方法再去调用这个Service中的其他方法是没有使用AOP代理的对象去调用的所以也不会创建新的事物。

1.再创建一个Service，用一个Service去调用另一个Service,这样就是代理对象调用.(如:用批量插入UserBatchService去调用单个插入UserService);不要在同一个类中调用。（它们都是AOP代理的对象，都会创建新的事物）。
2.集成ApplicationContextAware接口,手动获取AOP代理的Service对象,再由这个代理对象去调用方法。也可以成功创建事物。从IoC容器取出的对象是一个代理对象,通过它克服自调用的问题.

```java
// 1.实现ApplicationContextAware
private AplicationContext applicationContext = null;
// 2.从IoC容器中取出代理对象
UserService useService = applicationContext.getBean(UserService.class) // 当前类的实现接口
// 3.设置隔离级别和传播行为.
```

# 注意

@Transactional事务使用时需要注意：
A. 一个功能是否要事务，必须纳入设计、编码考虑。不能仅仅完成了基本功能就ok。
B. 如果加了事务，必须做好开发环境测试（测试环境也尽量触发异常、测试回滚），确保事务生效。
C. 以下列了事务使用过程的注意事项，请大家留意。
1.不要在接口上声明@Transactional，而要在具体类的方法上使用@Transactional注解，否则注解可能无效。
2.不要将@Transactional放置在类级的声明中，放在类声明，会使得所有方法都有事务。故@Transactional应该放在方法级别，不需要使用事务的方法，就不要放置事务，比如查询方法。否则对性能是有影响的。
3.使用了@Transactional的方法，对同一个类里面的方法调用，@Transactional无效。比如有一个类Test，它的一个方法A，A再调用Test本类的方法B（不管B是否public还是private），但A没有声明注解事务，而B有。则外部调用A之后，B的事务是不会起作用的。（经常在这里出错）
4.使用了@Transactional的方法，只能是public，@Transactional注解的方法都是被外部其他类调用才有效，故只能是public。道理和上面的有关联。故在protected、private或者package-visible的方法上使用 @Transactional 注解，它也不会报错，但事务无效。
5.spring的事务在抛异常的时候会回滚，如果是catch捕获了，事务无效。可以在catch里面加throw new RuntimeException();
6.最后有个关键的一点：和锁同时使用需要注意：由于Spring事务是通过AOP实现的，所以在方法执行之前会有开启事务，之后会有提交事务逻辑。而synchronized代码块执行是在事务之内执行的，可以推断在synchronized代码块执行完时，事务还未提交，其他线程进入synchronized代码块后，读取的数据不是最新的。
所以必须使synchronized锁的范围大于事务控制的范围，把synchronized加到Controller层或者大于事务边界的调用层！



https://www.cnblogs.com/clar/p/13633697.html

https://developer.ibm.com/zh/articles/j-master-spring-transactional-use/