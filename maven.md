# Maven学习



## maven学习

#### maven坐标

| 字段       | 说明       | 示例              |
| ---------- | ---------- | ----------------- |
| groupId    | 公司项目ID | com.springboot    |
| artifactId | 模块ID     | Start-Spring-Boot |
| version    | 版本号     | 0.0.1-SNAPSHOT    |



#### maven的常用命令

| 命令 | 说明 |      |      |      |
| ---- | ---- | ---- | ---- | ---- |
|      |      |      |      |      |
|      |      |      |      |      |
|      |      |      |      |      |
|      |      |      |      |      |
|      |      |      |      |      |



#### maven的scope

scope为添加依赖包的时候，需要注明scope，默认为compile

![image-20211120233516741](/Users/zfyy04/studySummary/maven.assets/image-20211120233516741.png)

#### maven的依赖

1. 依赖的传递

   通过引入其他项目，会将其他项目依赖的包全部导入

   依赖的原则

   - 作用：解决jar包冲突

   - 依赖路径最短者优先

     ![image-20211120233540734](/Users/zfyy04/studySummary/maven.assets/image-20211120233540734.png)

   - 路径相同时，先申明着优先。dependency

   - 统一管理依赖的版本：

     ```xml
     <!--定义properties-->
     <properties>
     		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
     		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
     		<java.version>1.7</java.version>
     </properties>
     ```

   - 如何让依赖不传递

     ```xml
     <dependency>
       	<groupId>org.springframework.boot</groupId>
       	<artifactId>spring-boot-devtools</artifactId>
       	<optional>true</optional><!--依赖将不传递-->
     </dependency>
     ```

2. 依赖的排除，如下：

   ```xml
   <exclusions>
   	<exclusion>
       <groupId>
       	xxx
       </groupId>
       <artifactId>
       	xxx
       </artifactId>
     </exclusion>
   </exclusions>
   ```

3. 继承

   - 子工程继承父工程，父工程统一版本号

   - **在parent中严禁直接使用depandencys预定义依赖**

   - 父工程为maven工程，打包方式为pom，packaging为pom。

     添加依赖包管理，dependencyManagement里面的包被子工程引用

     ```xml
     <dependencyManagement>
             <dependencies>
                 <dependency>
                     <groupId>org.springframework.cloud</groupId>
                     <artifactId>spring-cloud-dependencies</artifactId>
                     <version>Edgware.SR3</version>
                     <type>pom</type>
                     <scope>import</scope>
                 </dependency>
             </dependencies>
      </dependencyManagement>
     ```

   - 注意：dependencyManagement的说明

     - 在Maven中dependencyManagement的作用其实相当于一个对所依赖jar包进行版本管理的管理器。

     - pom.xml文件中，jar的版本判断的两种途径

       1）如果dependencies里的dependency自己没有声明version元素，那么maven就会到dependencyManagement里面去找有没有对该artifactId和groupId进行过版本声明，如果有，就继承它，如果没有就会报错，告诉你必须为dependency声明一个version。

       2）如果dependencies中的dependency声明了version，那么无论dependencyManagement中有无对该jar的version声明，都以dependency里的version为准。

     - **dependencyManagement**里只是声明依赖，并不实现引入，因此子项目需要显示的声明需要用的依赖。如果不在子项目中声明依赖，是不会从父项目中继承下来的；只有在子项目中写了该依赖项，并且没有指定具体版本，才会从父项目中继承该项，并且version和scope都读取自父pom;另外如果子项目中指定了版本号，那么会使用子项目中指定的jar版本。

     - 示例

       ```xml
       //只是对版本进行管理，不会实际引入jar 
       <dependencyManagement> 
          <dependencies> 
             <dependency> 
               <groupId>org.springframework</groupId> 
               <artifactId>spring-core</artifactId> 
               <version>3.2.7</version> 
             </dependency> 
         </dependencies> 
       </dependencyManagement> 
        
       //会实际下载jar包，由于没有写版本，所以从父工程获取version
       <dependencies> 
           <dependency> 
               <groupId>org.springframework</groupId> 
               <artifactId>spring-core</artifactId> 
           </dependency> 
       </dependencies>
       ```

   - 在子工程中申明对父工程的引用

     ```xml
     <parent>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-starter-parent</artifactId>
             <version>1.5.13.RELEASE</version>
       			<relativePath>../pom.xml</relativePath> <!-- 需要写明父工程pom.xml的相对路径，如果不写则从repository获取 -->
         </parent>
     ```

     

   - 将子工程坐标与父工程中坐标重复的删除

   - 安装install时必须要安装父工程

   - scope为import时的说明：

     - 实现多继承，子工程写了parent以后，可以使用scope为import去引用另外父工程的jar包

     - 参照https://www.cnblogs.com/gyjx2016/p/6794893.html

     - 示例

       ```xml
       工程一：
       <project>
           <modelVersion>4.0.0</modelVersion>
           <groupId>com.test.sample</groupId>
           <artifactId>base-parent1</artifactId>
           <packaging>pom</packaging>
           <version>1.0.0-SNAPSHOT</version>
           <dependencyManagement>
               <dependencies>
                   <dependency>
                       <groupId>junit</groupId>
                       <artifactid>junit</artifactId>
                       <version>4.8.2</version>
                   </dependency>
                   <dependency>
                       <groupId>log4j</groupId>
                       <artifactid>log4j</artifactId>
                       <version>1.2.16</version>
                   </dependency>
               </dependencies>
           </dependencyManagement>
       </project>
       
       工程二，然后我就可以通过非继承的方式来引入这段依赖管理配置：
       <dependencyManagement>
           <dependencies>
               <dependency>
                   <groupId>com.test.sample</groupId>
                   <artifactid>base-parent1</artifactId>
                   <version>1.0.0-SNAPSHOT</version>
                   <type>pom</type>
                   <scope>import</scope>
               </dependency>
           </dependencies>
       </dependencyManagement>
       
       <dependency>
           <groupId>junit</groupId>
           <artifactid>junit</artifactId>
       </dependency>
       <dependency>
           <groupId>log4j</groupId>
           <artifactid>log4j</artifactId>
       </dependency>
       注意：import scope只能用在dependencyManagement里面
       
       这样，父模块的pom就会非常干净，由专门的packaging为pom来管理依赖，也契合的面向对象设计中的单一职责原则。此外，我们还能够创建多个这样的依赖管理pom，以更细化的方式管理依赖。这种做法与面向对象设计中使用组合而非继承也有点相似的味道。
       
       
       springcloud就是这样用的：
       <dependencyManagement>
         <dependencies>
           <dependency>
             <groupId>org.springframework.cloud</groupId>
             <artifactId>spring-cloud-dependencies</artifactId>
             <version>Edgware.SR3</version>
             <type>pom</type>
             <scope>import</scope>
           </dependency>
         </dependencies>
       </dependencyManagement>
       
       <dependencies>
         <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter</artifactId>
         </dependency>
         <dependency>
           <groupId>org.springframework.cloud</groupId>
           <artifactId>spring-cloud-config-server</artifactId>
         </dependency>
         <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-security</artifactId>
         </dependency>
       </dependencies>
       ```

       

4. 聚合

   - 父工程中配置子工程的模块

   ```xml
   <!--指定各工程的路径-->
   <modules>
       <module>provider</module>
       <module>consumer</module>
   </modules>
   ```

   - 父工程打包，可以将子工程全部执行

