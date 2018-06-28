#### AOP术语
- 通知：描述了切面要完成的工作，定义了何时执行这个工作，分为Before、After、Around、After-returning、After-throwing五种类型。  
- 连接点：可以所有可以应用通知的组件。  
- 切点：真正去应用通知的组件，定义了哪些连接点被通知。   
- 切面：切点+通知。  
- 织入：将通知应用到切点的过程。  
- 引入：将通知引入切面。
#### AOP支持
- 基于代理的经典AOP
- 纯POJO切面
- @AspectJ注解驱动的切面
- 注入式AspectJ切面（适用于spring各种版本）  
1. 第一种一般不用，第二种基于xml声明式实现，第三种基于注解实现  
2. 前三种都是SpringAop实现的变体，SpringAop基于动态代理实现，因此，Spring对AOP的支持局限于方法拦截  
3. Spring只支持方法级别的的连接点
#### 通过切点选择连接点（使用的是AspectJ的切点表达式）
- arg():限制连接点匹配参数为为指定类型的执行方法
- @args():限制连接点匹配参数为指定注解标注的执行方法
- execution():用于匹配是连接点的指定参数
- this
- target
- @target
- within
- @within
- @annotation
1. Spring只支持AspectJ切点指示器的一个子集
2. 在上面所有展示的指示器中，只有execution()方法是用来执行匹配的，其他的方法都用来限制匹配的
3. 编写切点语法：整体为执行匹配+限制匹配构成，【返回值类型（一般用通配符*代表任意的返回值类型）+全限定类名和方法名（也可以用通配符\*处理）+方法参数列表（可以用..去接收任意个参数）】（类似于去声明一个函数）+【限制指示器(相关参数)】
4. 切面代码（通知+切点）
```
package aopDemo;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Component
@Aspect//定义该POJO为切面（通知+切点）
public class Audience {

	@Pointcut("execution(* aopDemo.Performance.perform(..))")//如果有多个通知配置的切点表达式一至，可以用@Pointcut+方法实现重用
	public void performance() {}
	@Before("performance()")
	public void silenceCellPhoones() {//表演前
		System.out.println("Before：Silencing!!!");
	}
	@AfterReturning("performance()")
	public void applause() {//表演结束后
		System.out.println("AfterReturning:CLAP!!!");
	}
	@AfterThrowing("execution(* aopDemo.Performance.perform(..))")
	public void demandRefund() {//表演失败后
		System.out.println("AfterThrowing:Refund!!!");
	}
	@Around("performance()")
	public void aroundDemo(ProceedingJoinPoint proceed) {//环绕通知，将上述三种通知合并在       一起
		try {
			System.out.println("Before：Silencing!!!");
			proceed.proceed();//执行具体代理的方法
			System.out.println("AfterReturning:CLAP!!!");
		} catch (Throwable e) {
			System.out.println("AfterThrowing:Refund!!!");
		}
	}
}
```
2、配置切点的实习类
```
package aopDemo;

import org.springframework.stereotype.Component;

@Component
public class Dance implements Performance {

	public void perform() {
		System.out.println("run:dancing");
	}

}
```
3、JavaConfig配置
```
package aopDemo;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

import aopDemo.Performance;

@Configuration
@ComponentScan(basePackageClasses= {Performance.class})
@EnableAspectJAutoProxy//开启自动代理功能
public class PerformConfig {
	
}
```
4、测试类
```
package performance_test;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import aopDemo.PerformConfig;
import aopDemo.Performance;


@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes=PerformConfig.class)
public class PerformTest {
	@Autowired
	private Performance dance;
	
	@Test
	public void notice() {
		dance.perform();
	}
}
```