---
title: Maven
date: 2018-06-04 16:43:53
tags: java
---

Maven简单配置


<!--more-->


maven本地资源库配置（settings标签里）：

```xml
<localRepository>C:\ProgramFiles\Java\mavenRepo</localRepository>
```
maven阿里云仓库配置（mirrors标签里）：

```xml
<mirror>    
	<id>nexus-aliyun</id>    
	<mirrorOf>*</mirrorOf>    
	<name>Nexus aliyun</name>    
	<url>http://maven.aliyun.com/nexus/content/groups/public</url>    
</mirror> 
```
maven jdk1.8配置（profiles标签里）：

```xml
<profile>
	<id>development</id>
	<activation>
	  	<jdk>1.8</jdk>
	  	<activeByDefault>true</activeByDefault>
	</activation>
	<properties>
		<maven.compiler.source>1.8</maven.compiler.source>
		<maven.compiler.target>1.8</maven.compiler.target>
		<maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
	</properties>
</profile>
```



