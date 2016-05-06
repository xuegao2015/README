README
---------------
****
###　　　　　　　　　　　　Author:雪糕
###　　　　　　　　　 E-mail:yanxuegao@weyao.com
----------------
##Cat 监控集成<br>
###1）maven dependency
	<dependency>
		<groupId>com.weyao</groupId>
		<artifactId>cat-integration</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</dependency>
###2）项目文件中添加以下文件
	srv/main/resources<br>
	|
	|...META-INF
	|   	  |
	|    	  |..client.xml
	|
	|....app.properties
###3）Client.xml 内容（domain ID 改为项目名）
	<config mode="client">
   		<domain id="Test"/>
	</config>
###4）app.properties 内容如下（app.name=${项目名}）
	app.name=Test
###5）Spring Aop 配置 示例如下
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xsi:schemaLocation="     
          http://www.springframework.org/schema/beans     
          http://www.springframework.org/schema/beans/spring-beans-3.0.xsd     
          http://www.springframework.org/schema/context     
          http://www.springframework.org/schema/context/spring-context-3.0.xsd 
          http://www.springframework.org/schema/aop     
          http://www.springframework.org/schema/aop/spring-aop-3.0.xsd"
	default-autowire="byName">

	<!-- ==============================利用spring aspectj 来配置AOP================================ -->

	<!-- 声明通知类 -->
	<bean id="rpcAdvice" class="com.weyao.cat.aop.DobboRpcCatLogAspectAdvice" />
	<bean id="dbAdvice" class="com.weyao.cat.aop.MyBatisCatLogAspectAdvice" />
	<bean id="redisAdvice" class="com.weyao.cat.aop.RedisCatLogAspectAdvice" />

	<aop:config>
		<aop:aspect id="serviceAspect" ref="rpcAdvice">
			<!-- 配置指定切入的对象 -->

			<!-- 匹配 所有Srv的所有方法 包括 Provider、Consumer -->
			<aop:pointcut id="point_cut"
				expression="execution(* com.weyao.srv..*Srv.*(..))" />
			<!-- 前置通知  
			<aop:before method="doBefore" pointcut-ref="point_cut" />
			-->
			<!-- 后置通知 returning指定返回参数
			<aop:after-returning method="doAfter" pointcut-ref="point_cut" returning="result" />
			  -->
			<!-- 环绕通知 -->
			<aop:around method="doAround" pointcut-ref="point_cut" />
			<aop:after-throwing method="doThrow" pointcut-ref="point_cut" throwing="e" />
		</aop:aspect>
		<aop:aspect id="mybatiesAspect" ref="dbAdvice">
			<!-- 配置指定切入的对象 -->

			<!-- 匹配 所有Srv的所有方法 包括 Provider、Consumer -->
			<aop:pointcut id="db_point_cut" expression="execution(* com.weyao.dao..*Mapper.*(..))" />
			<!-- 前置通知  
			<aop:before method="doBefore" pointcut-ref="point_cut" />
			-->
			<!-- 后置通知 returning指定返回参数
			<aop:after-returning method="doAfter" pointcut-ref="point_cut" returning="result" />
			  -->
			<!-- 环绕通知 -->
			<aop:around method="doAround" pointcut-ref="db_point_cut" />
			<aop:after-throwing method="doThrow" pointcut-ref="db_point_cut" throwing="e" />
		</aop:aspect>
		
		<aop:aspect id="redisAspect" ref="redisAdvice">
			<!-- 配置指定切入的对象 -->

			<!-- 匹配 所有Srv的所有方法 包括 Provider、Consumer -->
			<aop:pointcut id="redis_point_cut" expression="execution(* com.weyao.redis.RedisService.*(..))" />
			
			<!-- 前置通知  -->
			<aop:before method="doBefore" pointcut-ref="redis_point_cut" />
			
			<!-- 后置通知 returning指定返回参数
			<aop:after-returning method="doAfter" pointcut-ref="point_cut" returning="result" />
			  -->
			<!-- 环绕通知 -->
			<aop:around method="doAround" pointcut-ref="redis_point_cut" />
			<aop:after-throwing method="doThrow" pointcut-ref="redis_point_cut" throwing="e" />
		</aop:aspect>
		
		
	</aop:config>
</beans>