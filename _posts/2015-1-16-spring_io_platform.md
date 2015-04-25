---
layout: post
title: spring io 平台介绍

---

Spring IO Platform reference对Spring IO的介绍如下：

> Spring IO Platform is primarily intended to be used with a dependency management system. It works well with both Maven and Gradle.

具体如何理解Spring IO Platform 的作用了？

以前在升级Spring项目的时候是手动的一个一个升级Spring模块的版本，并且一个模块与另一个模块之间的依赖适不适合你并不知道，你还需要测试或者找资料，所以比较麻烦。Spring IO Platform它能够结合Maven (或Gradle)管理每个模块的依赖，使得开发者不再花心思研究各个Java库相互依赖的版本，只需要引入Spring IO Platform即可，因为这些库的依赖关系Spring IO Platform已经帮你验证过了。

在Maven中的使用也比较简单，只需要在pom.xml文件中加入依赖管理就可：

	<dependencyManagement>
		<dependencies>
			<dependency> 
				<groupId>io.spring.platform</groupId>
				<artifactId>platform-bom</artifactId>
				<version>1.1.2.BUILD-SNAPSHOT</version> 
				<type>pom</type>
				<scope>import</scope>
			</dependency>
        </dependencies>
    </dependencyManagement>

当然也可以使用继承的方式：
	<parent> 
		<groupId>io.spring.platform</groupId>
		<artifactId>platform-bom</artifactId>
		<version>1.1.2.BUILD-SNAPSHOT</version> 
		<relativePath/>
	</parent>

---

参考：

http://docs.spring.io/platform/docs/1.1.2.BUILD-SNAPSHOT/reference/htmlsingle/





