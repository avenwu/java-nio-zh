# 16. Java NIO Files
> 原文链接：[http://tutorials.jenkov.com/java-nio/files.html](http://tutorials.jenkov.com/java-nio/files.html)

<!-- toc -->

Java NIO中的Files类（java.nio.file.Files）提供了多种操作文件系统中文件的方法。本节教程将覆盖大部分方法。Files类包含了很多方法，所以如果本文没有提到的你也可以直接查询JavaDoc文档。
java.nio.file.Files类是和java.nio.file.Path相结合使用的，所以在用Files之前确保你已经理解了Path类。
## Files.exists()
Files.exits()方法用来检查给定的Path在文件系统中是否存在。
在文件系统中创建一个原本不存在的Payh是可行的。例如，你想新建一个目录，那么闲创建对应的Path实例，然后创建目录。
由于Path实例可能指向文件系统中的不存在的路径，所以需要用Files.exists()来确认。
下面是一个使用Files.exists()的示例：
```
Path path = Paths.get("data/logging.properties");

boolean pathExists =
        Files.exists(path,
            new LinkOption[]{ LinkOption.NOFOLLOW_LINKS});
```
这个示例中，我们首先创建了一个Path对象，然后利用Files.exists()来检查这个路径是否真实存在。
注意Files.exists()的的第二个参数。他是一个数组，这个参数直接影响到Files.exists()如何确定一个路径是否存在。在本例中，这个数组内包含了LinkOptions.NOFOLLOW_LINKS，表示检测时不包含符号链接文件。
## Files.createDirectory()
Files.createDirectory()会创建Path表示的路径，下面是一个示例：
```
Path path = Paths.get("data/subdir");

try {
    Path newDir = Files.createDirectory(path);
} catch(FileAlreadyExistsException e){
    // the directory already exists.
} catch (IOException e) {
    //something else went wrong
    e.printStackTrace();
}
```
第一行创建了一个Path实例，表示需要创建的目录。接着用try-catch把Files.createDirectory()的调用捕获住。如果创建成功，那么返回值就是新创建的路径。
如果目录已经存在了，那么会抛出java.nio.file.FileAlreadyExistException异常。如果出现其他问题，会抛出一个IOException。比如说，要创建的目录的父目录不存在，那么就会抛出IOException。父目录指的是你要创建的目录所在的位置。也就是新创建的目录的上一级父目录。
## Files.copy()
Files.copy()方法可以吧一个文件从一个地址复制到另一个位置。例如：
```
Path sourcePath      = Paths.get("data/logging.properties");
Path destinationPath = Paths.get("data/logging-copy.properties");

try {
    Files.copy(sourcePath, destinationPath);
} catch(FileAlreadyExistsException e) {
    //destination file already exists
} catch (IOException e) {
    //something else went wrong
    e.printStackTrace();
}
```
这个例子当中，首先创建了原文件和目标文件的Path实例。然后把它们作为参数，传递给Files.copy(),接着就会进行文件拷贝。
如果目标文件已经存在，就会抛出java.nio.file.FileAlreadyExistsException异常。类似的吐过中间出错了，也会抛出IOException。
### Overwriting Existing Files

## Files.move()

## Files.delete()

## Files.walkFileTree()

### Searching For Files
### Deleting Directies Recursively

## Additional Methods in the Files Class