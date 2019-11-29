### 1. @AspectJ 的由来

提到AspectJ,其实很多人是有误解的，很多人只知道在Spring中使用Aspect那一套注解，以为是Spring开发的这一套注解，这里我觉得有责任和大家澄清一下。 **AspectJ是一个AOP框架，它能够对java代码进行AOP编译（一般在编译期进行），让java代码具有AspectJ的AOP功能（当然需要特殊的编译器），可以这样说AspectJ是目前实现AOP框架中最成熟，功能最丰富的语言，更幸运的是，AspectJ与java程序完全兼容，几乎是无缝关联，因此对于有java编程基础的工程师，上手和使用都非常容易.** 其实AspectJ单独就是一门语言，它需要专门的编译器(ajc编译器). Spring AOP 与ApectJ的目的一致，都是为了统一处理横切业务，但与AspectJ不同的是，Spring AOP并不尝试提供完整的AOP功能(即使它完全可以实现)，Spring AOP 更注重的是与Spring IOC容器的结合，并结合该优势来解决横切业务的问题，因此在AOP的功能完善方面，相对来说AspectJ具有更大的优势，同时,Spring注意到AspectJ在AOP的实现方式上依赖于特殊编译器(ajc编译器)，因此Spring很机智回避了这点，**转向采用动态代理技术的实现原理来构建Spring AOP的内部机制（动态织入），这是与AspectJ（静态织入）最根本的区别**。在AspectJ 1.5后，引入@Aspect形式的注解风格的开发，Spring也非常快地跟进了这种方式，因此Spring 2.0后便使用了与AspectJ一样的注解。请注意，**Spring 只是使用了与 AspectJ 5 一样的注解，但仍然没有使用 AspectJ 的编译器，底层依是动态代理技术的实现，因此并不依赖于 AspectJ 的编译器**。 所以，大家要明白，Spring AOP虽然是使用了那一套注解，其实实现AOP的底层是使用了动态代理(JDK或者CGLib)来动态植入。至于AspectJ的静态植入，不是本文重点，所以只提一提。

### 2. Spring AOP - AspectJ注解

谈到Spring AOP,老程序员应该比较清楚,之前的Spring AOP没有使用@Aspect这一套注解和aop:config这一套XML解决方案，而是开发者自己定义一些类实现一些接口，而且配置贼恶心。本文就不再演示了，后来Spring痛下决心,把AspectJ"整合"进了Spring当中，并开启了aop命名空间。现在的Sping AOP可以算是朗朗乾坤。上例子之前，还是把AOP的概念都提一提:

- 切点:定位到具体方法的一个表达式
- 切面: 切点+建言
- 建言(增强):定位到方法后干什么事

- **前置通知@Before**: 前置通知通过@Before注解进行标注，并可直接传入切点表达式的值，该通知在目标函数执行前执行，注意JoinPoint，是Spring提供的静态变量，通过joinPoint 参数，可以获取目标对象的信息,如类名称,方法参数,方法名称等，该参数是可选的。

```
@Before("execution(...)")
public void before(JoinPoint joinPoint){
    System.out.println("...");
}
复制代码
```

- **后置通知@AfterReturning**: 通过@AfterReturning注解进行标注，该函数在目标函数执行完成后执行，并可以获取到目标函数最终的返回值returnVal，当目标函数没有返回值时，returnVal将返回null，必须通过returning = “returnVal”注明参数的名称而且必须与通知函数的参数名称相同。请注意，在任何通知中这些参数都是可选的，需要使用时直接填写即可，不需要使用时，可以完成不用声明出来。

```
@AfterReturning(value="execution(...)",returning = "returnVal")
public void AfterReturning(JoinPoint joinPoint,Object returnVal){
   System.out.println("我是后置通知...returnVal+"+returnVal);
}
复制代码
```

- **异常通知 @AfterThrowing**:该通知只有在异常时才会被触发，并由throwing来声明一个接收异常信息的变量，同样异常通知也用于Joinpoint参数，需要时加上即可.

```
@AfterThrowing(value="execution(....)",throwing = "e")
public void afterThrowable(Throwable e){
  System.out.println("出现异常:msg="+e.getMessage());
}
复制代码
```

- **最终通知 @After**:该通知有点类似于finally代码块，只要应用了无论什么情况下都会执行.

```
@After("execution(...)")
public void after(JoinPoint joinPoint) {
    System.out.println("最终通知....");
}
复制代码
```

- **环绕通知 @Around**: 环绕通知既可以在目标方法前执行也可在目标方法之后执行，更重要的是环绕通知可以控制目标方法是否指向执行，但即使如此，我们应该尽量以最简单的方式满足需求，在仅需在目标方法前执行时，应该采用前置通知而非环绕通知。案例代码如下第一个参数必须是ProceedingJoinPoint，通过该对象的proceed()方法来执行目标函数，proceed()的返回值就是环绕通知的返回值。同样的，ProceedingJoinPoint对象也是可以获取目标对象的信息,如类名称,方法参数,方法名称等等

```
@Around("execution(...)")
public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
    System.out.println("我是环绕通知前....");
    //执行目标函数
    Object obj= (Object) joinPoint.proceed();
    System.out.println("我是环绕通知后....");
    return obj;
}
复制代码
```

然后说下一直用"..."忽略掉的切入点表达式，这个表达式可以不是exection(..)，还有其他的一些，我就不说了，说最常用的execution:

```
//scope ：方法作用域，如public,private,protect
//returnt-type：方法返回值类型
//fully-qualified-class-name：方法所在类的完全限定名称
//parameters 方法参数
execution(<scope> <return-type> <fully-qualified-class-name>.*(parameters))
复制代码
<fully-qualified-class-name>.*(parameters)
复制代码
```

注意这一块，如果没有精确到class-name，而是到包名就停止了，要用两个".."来表示包下的任意类:

- execution(* com.zdy..*(..))：com.zdy包下所有类的所有方法.

- execution(* com.zdy.Dog.*(..)): 

Dog类下的所有方法.使用切入点时，还可以抽出来一个@Pointcut来供使用:

  ```
  /**
   * 使用Pointcut定义切点
   */
  @Pointcut("execution(...)")
  private void myPointcut(){}
  
  /**
   * 应用切入点函数
   */
  @After(value="myPointcut()")
  public void afterDemo(){
      System.out.println("最终通知....");
  }
  ```

 三、Spring中对AOP的支持

首先AOP思想的实现一般都是基于代理模式，在JAVA中一般采用JDK动态代理模式，但是我们都知道，JDK动态代理模式只能代理接口，如果要代理类那么就不行了。因此，Spring AOP 会这样子来进行切换，因为Spring AOP 同时支持 CGLIB、ASPECTJ、JDK动态代理，当你的真实对象有实现接口时，Spring AOP会默认采用JDK动态代理，否则采用cglib代理。

- 如果目标对象的实现类实现了接口，Spring AOP 将会采用 JDK 动态代理来生成 AOP 代理类；
- 如果目标对象的实现类没有实现接口，Spring AOP 将会采用 CGLIB 来生成 AOP 代理类——不过这个选择过程对开发者完全透明、开发者也无需关心。

