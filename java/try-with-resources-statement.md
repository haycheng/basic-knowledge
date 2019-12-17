# Java SE 7 中带资源声明的 try 语句（try with resources statement）


**try-with-resources statement** 是指声明了一个或多个资源的`try`语句（statement，声明），其中的资源一般是指在程序执行完毕後需要关闭（close）的对象，而 try-with-resources 语句中声明的资源会在`try`块执行完毕後自动关闭。try-with-resources 语句中的资源只能是实现了`java.lang.AutoCloseable`接口的类实例，但是 Java SE 7 之後的所有实现了java.io.Closeable的类都实现了`java.lang.AutoCloseable`接口（该接口是在Java SE 7中才引入的），故都可以作为这里的资源。

try-with-resources 语句用法：在`try`关键字後面伴随一对小括号，并在括号中声明`try`块中要用到的资源。下例中利用 `BufferedReader` 来读取文件中的第一行，采用了 try-with-resources 的语法：
```java
static String readFirstLineFromFile(String path) throws IOException {

    try (BufferedReader br = new BufferedReader(new FileReader(path))) {
        return br.readLine();
    }
}
```

这里的 `BufferedReader` 对象不必调用其`close()`方法进行关闭，而是会在退出`try`块（不论正常或异常退出）後自动关闭（後台会自动调用`close()`），释放资源。按 Java SE 7 之前的写法，`BufferedReader` 对象一般需要在`try`块後面的`finally`块中调用其`close()`方法关闭对象：
```java
static String readFirstLineFromFileWithFinallyBlock(String path) throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(path));
    try {
        return br.readLine();
    } finally {
        if (br != null)
            br.close();
    }
}
```

在上例中，如果`readLine()`与`close()`方法先後都产生了异常，那么在用`finally`块关闭`BufferedReader`对象时，**由`readLine()`方法产生的（第一个）异常会被抑制（suppressed），只有`close()`产生的（第二个）异常会被向上传递**；相反，若用 try-with-resources 语句的话，**则只有`try`块中产生的异常被向上传递（这个异常的信息通常更有用），try-with-resources 语句中产生的异常则会被抑制**。当然，在Java SE 7之後，被抑制的异常可以通过调用 `Throwable.getSuppressed()` 方法获取。

在 try-with-resources 语句中，也可以声明多个资源，各资源声明语句用分号隔开：
```java
try (
    ZipFile zf = new ZipFile(zipFileName);
    BufferedWriter writer = java.nio.file.Files.newBufferedWriter(outputFilePath, charset)
    ) {
        // Enumerate each entry
    }
```

当从`try`块中（以正常或异常方式）退出时，在 try-with-resources 语句中所声明资源的关闭方法都会被自动调用，并且是**按与资源声明序相反的顺序**调用其`close()`方法的。

最後，try-with-resources 语句可以像Java SE 7 之前的普通`try`语句一样带有`catch`块和`finally`块，但**在执行`catch`块（若有必要）和`finally`块中的代码前**，会先关闭 try-with-resources 语句中声明的资源。

**参考资料：**
* [The try-with-resources Statement (The Java&trade; Tutorials &gt; Essential Classes &gt; Exceptions)](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html)
* 本文首次贴在[这里](https://blog.csdn.net/clxjoseph/article/details/50637426)
