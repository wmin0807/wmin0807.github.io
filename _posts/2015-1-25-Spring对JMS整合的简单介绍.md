---
layout: post
title: spring对JMS整合的简单介绍

---

JMS是Java Message Service 的简写，也就是Java消息服务。它是面向异步消息而制定的标准API，一种异步通信的机制，当异步发送消息的时候，客户端不需要等待服务器处理消息，甚至不需要等待消息被投递，就可以继续执行的程序。异步消息是一个应用程序向另一个应用程序间接发送消息的一种方式，这种方式无需等待对方的响应。

首先需要了解JMS中存在两个主要概念：

* 消息代理(message broker)，当客户端发送异步消息就必然会有一个消息服务器（broker）来帮助它完成消息的转发，而这个服务器就是消息服务器。
* 目的地(destination)

JMS中目的地有两种：

* 队列，队列也称为点对点模型
* 主题，主题也称为发布/订阅模型

点对点的消息模型：

每条消息都有一个发送者和一个接收者。当消息代理得到消息时，它将消息放入一个队列中，接收者从这个队列接受消息，接受后该条消息从队列中删除，这样就可以保证消息只能投递给一个接收者。

发布-订阅消息模型：

消息会发送给一个主题，多个接收者都可以订阅这个主题，但是消息发送者并不知道。

---

下面是一个基于ActiveMQ的消息服务器，同时使用Spring对JMS的整合来发送和接受消息。

首先使用Maven建立工程，pom需要引入以下几个依赖：
	
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-context</artifactId>
		<version>4.1.3.RELEASE</version>
	</dependency>
	<dependency>
		<groupId>org.apache.activemq</groupId>
		<artifactId>activemq-core</artifactId>
		<version>5.7.0</version>
	</dependency>
	<dependency>
		<groupId>org.springframework</groupId>
		<artifactId>spring-jms</artifactId>
		<version>4.1.4.RELEASE</version>
	</dependency>

然后需要在Spring的配置文件中创建activemq的链接工厂，如下所示：

	<bean id="connectionFactory" 
		"class="org.apache.activemq.spring.ActiveMQConnectionFactory">
		<property name="brokerURL" value="tcp://localhost:61616" />
	</bean>
	
这里的brokerURL值就是代理服务器（activemq）的地址，由于在本地启动所以设置成localhost,61616为默认的占用端口。

启动activemq方式根据不同的操作系统平台是不一样的，在Mac下启动需要切换到activemq安装目录的bin目录下，然后再切换到macosx目录下，使用./activemq start启动，如果需要停止activemq就使用./activemq stop停止。

然后可以启动一个程序来发送消息，这里使用Spring已经封装好的JmsTemplate来进行消息的发送。以下是一个例子：

	public class JmsSendMessage {
		private JmsTemplate jmsTemplate;
		public void setJmsTemplate(JmsTemplate jmsTemplate) {
			this.jmsTemplate = jmsTemplate;
		}
		public void sendStudent(final Student student) {
			jmsTemplate.send("student", new MessageCreator(){
				public Message createMessage(Session session) throws JMSException {
					return session.createObjectMessage(student);
				}
			});
		}
	}

这个程序里面Student是自定义的一个对象，在发送信息的时候就是通过sendStudent(final Student student)将一个student对象作为消息发送。同时在调用jmsTemplate 的时候使用student 作为目的地，这种方式是直接在程序里面指定，不建议这么做。所以在接受消息的时候也需要从student 这个目的地接受。

程序中使用到了jmsTemplate，所以需要对jmsTemplate进行配置，由于jmsTemplate需要连接jms服务器，所以需要把connectionFactory注入到jmsTemplate中，配置如下：

	<bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
		<property name="connectionFactory" ref="connectionFactory" />
	</bean>

下面是接受消息的简单例子：

	public class JmsReceiveMessage {
		private JmsTemplate jmsTemplate;
		public void setJmsTemplate(JmsTemplate jmsTemplate) {
			this.jmsTemplate = jmsTemplate;
		}
		public Student getStudent() {
			ObjectMessage receivedMessage = (ObjectMessage) jmsTemplate.receive("student");
			try {
				Student student = (Student)receivedMessage.getObject();
				return student;
			} catch (JMSException e) {
				throw JmsUtils.convertJmsAccessException(e);
			}
		}
	}

接收消息和发送消息都需要到spring的配置文件中进行配置，这里省略。

启动activemq消息服务器后就可以调用JmsSendMessage 的sendStudent来发送消息，使用JmsReceiveMessage来接受消息，但是这里有一个问题需要注意，使用JmsReceiveMessage接收消息时，只要当有消息发送到student这个目的后才能接收到，如果没有消息会一直等待。这种方式对异步通信来说没有什么作用，所以下面是一个简单地消息监听器配置，通过消息监听器就可以让消息到来后系统自动帮你执行你指定的程序获取数据。

spring提倡基于POJO的低侵入编程，所以这里在实现监听器的时候没有在程序中实现MessageListener这个监听器接口，而是在spring配置文件中通过配置的方式完成消息的监听。

这个时候消息接受类就变成这样了：

	public class JmsReceiveMessage {
		// 当消息到达后自动将消息放入student参数中（因为我们知道发送方就是发送的一个student，所以接受时消息能转成Student对象传入）
		public void getStudent(Student student) {
			...
		}
	}

在spring的配置文件中的配置需要增加以下几行：

	<jms:listener-container connection-factory="connectionFactory">
		<jms:listener destination="student" ref="jmsReceiveMessage"
			method="getStudent" />
	</jms:listener-container>	

这样在消息达到后系统就会自动帮你执行jmsReceiveMessage 的getStudent方法。