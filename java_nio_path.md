# Java NIO Path

> 原文链接：[http://tutorials.jenkov.com/java-nio/path.html](http://tutorials.jenkov.com/java-nio/path.html)

<!-- toc -->

Java的path接口是作为Java NIO 2的一部分是Java6,7中NIO的升级增加部分。Path在Java 7新增的。相关接口位于java.nio.file包下，所以Javaz内Path接口的完整名称是java.nio.file.Path.

一个Path实例代表一个文件系统内的路径。path可以指向文件也可以指向目录。可以使相对路径也可以是绝对路径。绝对路径包含了从根目录到该文件（目录）的完整路径。相对路径包含该文件（目录）相对于其他路径的路径。相对路径听起来可能有点让人头晕。但是别急，稍后我们会详细介绍。

不要把文件系统中路径和环境变量的路径混淆。java.nio.file.Path和环境变量没有任何关系。

在很多情况下java.no.file.Path接口和java.io.File比较相似，但是他们之间存在一些细微的差异。尽管如此，在大多数情况下，我们都可以用File相关类来替换Path接口。

## 创建Path实例（Creating a Path Instance）
为了使用java.nio.file.Path实例我们必须创建Path对象。创建Path实例可以通过Paths的工厂方法get（）。下面是一个实例：
```
import java.nio.file.Path;
import java.nio.file.Paths;

public classs PathExample {
  public static void mian(String[] args) {
    Path = path = Paths.get("c:\\data\\myfile.txt");
  }
}
```
注意上面的两个import声明。需要使用Path和Paths的接口,毕现先把他们引入。
其次注意Paths.get("c:\\data\\myfile.txt")的调用。这个方法会创建一个Path实例，换句话说Paths.get()是Paths的一个工厂方法。

### 创建绝对路径（Creating an Absolute Path）

### 创建相对路径（Creating a Relative Path）

## Path.normalize()