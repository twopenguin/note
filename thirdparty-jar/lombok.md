# 1.Lombok 简介

在写Java程序的时候经常会遇到如下情形：新建了一个Class类，然后在其中设置了几个字段，最后还需要花费很多时间来建立getter，setter方法还有构造函数等 。lombok项目的产生就是为了省去我们手动创建getter和setter方法的麻烦，它能够在我们编译源码的时候自动帮我们生成getter和setter方法。即它最终能够达到的效果是：在源码中没有getter和setter方法，但是在编译生成的字节码文件中有getter和setter方法。

# 2.安装

### 2.1 Maven添加依赖，lombok也就是一个普通的jar包

```xml
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
  <version>1.16.14</version>
  <scope>provided</scope>
</dependency>
```

### 2.2 eclipse安装

1.从官网下载[lombok.jar](https://link.jianshu.com?t=https%3A%2F%2Fprojectlombok.org%2Fdownloads%2Flombok.jar)，放入到eclipse所在安装目录（也可以放到其他目录，不过不能出现中文路径）：

![img](http://upload-images.jianshu.io/upload_images/3362699-8cc96dd8999015de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

eclipse根目录

```
-javaagent:D:\Program Files (x86)\eclipse-jee-neon-1a-win32-x86_64\eclipse\lombok.jar

```

![img](http://upload-images.jianshu.io/upload_images/3362699-e2070ff7aed40f7e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

eclipse.ini

重启eclipse，lombok即可生效

### 2.3  Intellij idea安装

idea中开发的话需要安装Lombok plugin，同时设置 Setting -> Compiler -> Annotation Processors -> Enable annotation processing勾选。

![img](http://upload-images.jianshu.io/upload_images/3362699-1c40c2bd626fe3b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/842/format/webp)

lombok plugin

# 3. lombok使用

## 3.1 `@NonNull`: 可以帮助我们避免空指针。

```java
package io.fredia.femicro.gate.ratelimit.config;

import io.micrometer.core.lang.NonNull;

/**
 * LombokDemo
 * 
 * @author : Fredia
 * @since : 2018年4月11日
 * @version : v1.0.0
 */
public class LombokDemoClass {

    public void NonNullDemo(@NonNull Object obj) {

    }
}


```

## 3.2 `@Cleanup`: 自动帮我们调用close()方法。

```java
package io.fredia.femicro.gate.ratelimit.config;

import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;

import lombok.Cleanup;

/**
 * LombokDemo
 * 
 * @author : Fredia
 * @since : 2018年4月11日
 * @version : v1.0.0
 */
public class LombokDemoClass {

    public static void main(String[] args) throws IOException {
        @Cleanup
        InputStream in = new FileInputStream(args[0]);
        @Cleanup
        OutputStream out = new FileOutputStream(args[1]);
        byte[] b = new byte[10000];
        while (true) {
            int r = in.read(b);
            if (r == -1)
                break;
            out.write(b, 0, r);
        }
    }
}

```

我们都知道io操作完必须关闭，所以此注解帮我们完成了如下的操作。

```
  in.close();
  out.close();

```

## 3.3 `@Getter / @Setter`: 自动生成Getter/Setter方法

```java
package io.fredia.femicro.gate.ratelimit.config;

import java.util.Date;

import lombok.Getter;
import lombok.Setter;

/**
 * LombokDemo
 * 
 * @author : Fredia
 * @since : 2018年4月11日
 * @version : v1.0.0
 */
public class LombokDemo {

    @Getter
    @Setter
    private String key;
    @Getter
    @Setter
    private Date expiration;

}

```

## 3.4 `@NoArgsConstructor`: 自动生成无参数构造函数。

```java
import lombok.NoArgsConstructor;

/**
 * LombokDemo
 * 
 * @author : Fredia
 * @since : 2018年4月11日
 * @version : v1.0.0
 */
@NoArgsConstructor
public class LombokDemo {

    private String key;
    private Long remaining;
    private Long reset;
    private Date expiration;

    public static void main(String[] args) {
            LombokDemo lom2 = new LombokDemo();
    }
}

```

## 3.5 `@AllArgsConstructor`: 自动生成全参数构造函数。

```java
import lombok.AllArgsConstructor;
/**
 * LombokDemo
 * 
 * @author : Fredia
 * @since : 2018年4月11日
 * @version : v1.0.0
 */
@AllArgsConstructor
public class LombokDemo {

    private String key;
    private Long remaining;
    private Long reset;
    private Date expiration;

    public static void main(String[] args) {
        LombokDemo lom = new LombokDemo(key, remaining, reset, expiration);
    }
}

```

## 3.6 `@Data`: 自动为所有字段

添加@ToString, @EqualsAndHashCode, @Getter方法，为非final字段添加@Setter,和@RequiredArgsConstructor@RequiredArgsConstructor

源码：

```java
@Data
public class AppNameProperties {
    private String name;
}
```

字节码反编译：

```java
public class AppNameProperties {
    private String name;

    public AppNameProperties() {
    }

    public String getName() {
        return this.name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public boolean equals(Object o) {
        if(o == this) {
            return true;
        } else if(!(o instanceof AppNameProperties)) {
            return false;
        } else {
            AppNameProperties other = (AppNameProperties)o;
            if(!other.canEqual(this)) {
                return false;
            } else {
                Object this$name = this.getName();
                Object other$name = other.getName();
                if(this$name == null) {
                    if(other$name != null) {
                        return false;
                    }
                } else if(!this$name.equals(other$name)) {
                    return false;
                }

                return true;
            }
        }
    }

    protected boolean canEqual(Object other) {
        return other instanceof AppNameProperties;
    }

    public int hashCode() {
        int PRIME = true;
        int result = 1;
        Object $name = this.getName();
        int result = result * 59 + ($name == null?43:$name.hashCode());
        return result;
    }

    public String toString() {
        return "AppNameProperties(name=" + this.getName() + ")";
    }
}
```

## 3.7 `@Accessors(chain = true) ` 生成链式的setter方法

# 4. lombok实现原理分析

接下来进行lombok能够工作的原理分析，以Oracle的javac编译工具为例。
自从Java 6起，javac就支持**“JSR 269 Pluggable Annotation Processing API”**规范，只要程序实现了该API，就能在javac运行的时候得到调用。
举例来说，现在有一个实现了**”JSR 269 API”**的程序A,那么使用javac编译源码的时候具体流程如下:

> 1. javac对源代码进行分析，生成一棵**抽象语法树(AST) **
> 2. 运行过程中调用实现了**”JSR 269 API”**的A程序
> 3. 此时A程序就可以完成它自己的逻辑，包括修改第一步骤得到的抽象语法树(AST)
> 4. javac使用修改后的抽象语法树(AST)生成字节码文件

所以lombok本质上就是这样的一个实现了**”JSR 269 API”**的程序。在使用javac的过程中，结合官方说明，它编译的流程如下：

> 1. javac对源代码进行分析，生成一棵**抽象语法树(AST)****
> 2. 运行过程中调用实现了**”JSR 269 API”**的lombok程序
> 3. 此时lombok就对第一步骤得到的**AST**进行处理，找到**@Data**注解所在类对应的**语法树(AST)**，然后修改该**语法树(AST)**，增加**getter和setter**方法定义的相应树节点
> 4. javac使用修改后的**抽象语法树(AST)**生成字节码文件

# 5. 总结

lombok的实现：就是元注解出现后注解运行生命周期里面的编译周期，这个就是 **JSR 269 Pluggable Annotation Processing API**，就是源代码在编译成字节码的时候修改了语法树的节点规则进行了加强生成，
以后可以更加快捷的敲代码了。