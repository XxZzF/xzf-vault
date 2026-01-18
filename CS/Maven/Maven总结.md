Maven 的依赖版本决定机制被称为 **“依赖调解 (Dependency Mediation)”**。它并不是随机选择的，而是遵循一套严格的优先级层级。

为了让你彻底理解，我们将这套机制拆解为 **“四大法则”**。

---

## 法则一：路径最近者优先 (Nearest Wins)

这是 Maven 最核心的自动仲裁规则。Maven 会计算从当前项目到依赖包在“依赖树”中的距离（层级数），距离越短，版本胜出。

- **场景演示：**
    
    - 路径 A：`Project -> Log4j (2.0)` （距离为 1）
        
    - 路径 B：`Project -> RPC-SDK -> Log4j (1.2)` （距离为 2）
        
- **结果：** Maven 选择 **2.0**，因为它离项目更近。
    

---

## 法则二：第一声明者优先 (First Declaration Wins)

如果两条路径的距离**完全一样**，那么 Maven 就不再看版本号的高低，而是看你在 `pom.xml` 中书写 `<dependency>` 的 **先后顺序**。

- **场景演示：**
    
    - 路径 A：`Project -> Service-X -> Spring-Core (5.1)`
        
    - 路径 B：`Project -> Service-Y -> Spring-Core (5.2)`
        
- **结果：** 如果你在 `pom.xml` 里先写了 `Service-X` 的依赖，那么最终版本就是 **5.1**。
    

---

## 法则三：上帝模式 (Dependency Management)

如果在祖先 POM 或当前 POM 的 **`<dependencyManagement>`** 中明确指定了版本，那么**法则一和法则二全部失效**。（距离当前POM最近的祖先POM中的`<dependencyManagement>`中指定的版本生效）

- **核心逻辑：** 它相当于一份“版本白名单”。无论依赖树多深、路径多远，只要依赖的 `groupId` 和 `artifactId` 匹配，Maven 就会强行将其锁定为指定的版本。
    
- **适用场景：** 多模块项目（父子工程）统一控制版本，防止不同子模块引入冲突的 Jar 包。
    

---

## 法则四：当前项目直接依赖 (Direct Dependency)

这是最高级别的优先级。如果你在当前项目的 `<dependencies>` 标签下直接写了某个依赖及其版本，它会覆盖上述所有规则。

- **逻辑：** 它的路径距离是 0，根据“路径最近者优先”，它永远是赢家。


---


maven项目使用maven进行一系列构建时，依赖的是本地仓库的jar包，不是idea里的源码。

* 把项目打成可执行jar包

```xml
<build>
      <plugins>
      <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-shade-plugin</artifactId>
          <version>1.2.1</version>
          <executions>
              <execution>
                  <phase>package</phase>
                  <goals>
                      <goal>shade</goal>
                  </goals>
                  <configuration>
                      <transformers>
                          <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                              <mainClass>全类名</mainClass>
                          </transformer>
                      </transformers>
                  </configuration>
              </execution>
          </executions>
      </plugin>
      </plugins>
  </build>
```
然后mvn clean package。
原理是把main类信息`Main-Class: com.xzf.maven.HelloWorld`写到jar包中的META-INF/MANIFEST.MF文件里

* `<packaging>jar</packaging>`标签的值与打出来的文件的扩展名不一定相同
* 依赖原理
编译项目时把依赖在maven仓库中的路径加入到classpath中
* 依赖范围
maven在编译、测试、运行时有不同的classpath，依赖的依赖范围决定了在某一环境下该依赖会不会被加入到classpath中。

![image](images/98e608c3-8566-43d0-98b6-fcfc839e67af.jpg)

* 依赖调解
一个项目的依赖中，不可能出现groupId和artifactId相同，但是version不同的两个依赖。
如果有这样两个依赖，则根据依赖调解的原则选择一个：
   1. 路径最近者优先（A->B->C->X1.0, A->D->X2.0,选X2.0）
   2. 若路径长度相同则选择先声明的依赖
* 项目中直接用到的依赖应该显示声明，不应该使用直接依赖的传递来隐式获取该依赖。
* 从仓库中解析依赖的机制

![image](images/011b427d-2ff3-49b1-b954-83b7479d475b.jpg)

* 对某个仓库配置镜像后，对该仓库的所有访问会被转至该镜像。配置镜像时`<mirrorOf>`标签指明了镜像匹配的仓库。
* 每个构建步骤都可以绑定一个或多个插件行为，并且Maven为大多数构建步骤编写并绑定了默认插件
* Maven分三套生命周期：clean、default、site。`mvn clean install`命令执行的是clean生命周期的pre-clean、clean阶段和default生命周期的install之前的阶段。每套生命周期互不影响，执行`mvn install`命令不会执行clean生命周期的阶段。
* 每个插件都有多个插件目标，生命周期的阶段和插件的目标绑定。
* 在POM中配置插件时，如果该插件是Maven的官方插件（即如果其groupId为org.apache.maven.plugins)，就可以省略groupId配置。
* 超级POM：
   1. 是所有Maven项目的父POM，所有项目都会继承这个超级POM的配置。
   2. 为核心插件设定了版本
   3. 把插件目标默认绑定到生命周期阶段上
   4. 配置了中央仓库
   5. 定义了Maven项目结构的约定



## 聚合和继承
### 1\. 聚合
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.juvenxu.mvnbook.account</groupId>
    <artifactId>account-aggregator</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>
    <name>Account Aggregator</name>
    <modules>
        <module>account-email</module>
        <module>account-persist</module>
        <module>account-parent</module>
    </modules>
</project>
```
`<module>`是子模块的目录的相对父模块的路径

### 2\. 继承
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.juvenxu.mvnbook.account</groupId>
        <artifactId>account-parent</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../account-parent/pom.xml</relativePath>
    </parent>
    <artifactId>account-email</artifactId>
    <name>Account Email</name>

    ...

</project>
```
1. `<relativePath>`表示父模块pom的相对路径，默认值是`../pom.xml`。

> The relative path of the parent pom.xml file within the check out.  If not specified, it defaults to ../pom.xml.  Maven looks for the parent POM first in this location on the filesystem, then the local repository, and lastly in the remote repo.  relativePath allows you to select a different location, for example when your structure is flat, or deeper without an intermediate parent POM.  However, the group ID, artifact ID and version are still required, and must match the file in the location given or it will revert to the repository for the POM.  This feature is only for enhancing the development in a local checkout of that project.  **Set the value to an empty string in case you want to disable the feature and always resolve the parent POM from the repositories**

2. 子模块可以不声明groupId和version，实际上，子模块隐式地从父模块继承了这两个元素。
3. 子模块继承父模块的`<dependencies>`下的依赖

### 3\. 依赖管理
parent的pom：

```xml
<dependencyManagement>
 <dependencies>
     <dependency>
         <groupId>junit</groupId>
         <artifactId>junit</artifactId>
         <version>${junit.version}</version>
         <scope>test</scope>
     </dependency>
 </dependencies>
 <dependencies>
     <dependency>
         <groupId>xxx</groupId>
         <artifactId>xxx</artifactId>
         <version>xxx</version>
         <type>pom</type>
         <scope>import</scope>
     </dependency>
 </dependencies>
</dependencyManagement>
```
child的pom：

```xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
    </dependency>
</dependencies>
```
1. 父模块中在`<dependencyManagement>`中声明的依赖项，子模块中只需声明该依赖的**groupId**和**artifactId**即可继承该依赖的其他属性，若子模块中声明其他属性如**version**、**scope**，则不继承父模块中的依赖项。
2. 父模块的`<dependencyManagement>`中若存在scope为**import**的依赖项，scope为**import**的依赖一般指向一个pom，作用是将目标pom的`<dependencyManagement>`配置导入合并到当前pom的`<dependencyManagement>`元素中。

### 反应堆与裁剪
1. maven构建时，先构建当前模块所依赖的模块，再构建当前模块。
2. 可以选择部分模块进行构建

### maven仓库的搜索顺序
* 本地仓库（Local Repository）： Maven首先会检查本地仓库。如果在本地仓库中找到所需的依赖，Maven会直接使用它。如果没有找到，它会进入下一步。
* 远程仓库（Remote Repositories）： Maven会按照以下顺序检查远程仓库：
* 中央仓库（Central Repository）： 中央仓库是 Maven 的默认仓库，包含了大量的开源库。如果依赖在中央仓库中找到，Maven会下载并缓存到本地仓库，然后使用。
* 其他配置的远程仓库： 如果中央仓库中没有找到依赖，Maven会继续检查用户在 pom.xml 文件或 Maven 的 settings.xml 文件中配置的其他远程仓库。这些仓库按照配置的顺序逐一检查。
* 仓库间的优先级： 当依赖在多个仓库中都存在时，Maven会根据仓库的声明顺序选择第一个找到的依赖。这也就是为什么仓库的声明顺序在一些情况下会影响 Maven 解析依赖的结果。

注意：

1. 如果在搜索过程中发现仓库有镜像设置，则用镜像的地址代替，例如现在需要在repository A仓库中查找某个依赖，但A仓库配置了mirror，则会转到从A的mirror中查找该依赖，不会再从A中查找。
2. settings.xml中配置的profile（激活的）下的repository优先级高于项目中pom文件配置的repository。
3. 如果仓库的id设置成“central”，则该仓库会覆盖maven默认的中央仓库配置。