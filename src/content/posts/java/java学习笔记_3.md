---

title: Java学习笔记（3）

published: 2025-03-09

description: 'Java学习之路'

image: './img/Alice_3.jpeg'

tags: [java, 学习]

category: 'java'

draft: false 

---

## try-with-resources

`try-with-resources`是Java 7引入的一种特性，用于自动管理资源，例如文件、网络连接等。这种机制确保了在`try`代码块执行完毕后，资源会被自动关闭，从而避免了资源泄露。
### 工作原理
1. **资源定义**：在`try`语句的括号内声明并初始化资源。这些资源必须实现`AutoCloseable`接口。
2. **资源使用**：在`try`代码块内使用这些资源。
3. **自动关闭**：无论`try`代码块是正常结束还是因为异常而结束，都会自动调用资源的`close()`方法来关闭资源。
### 语法
```java
try (ResourceType resource = resourceInitialization) {
    // 使用资源的代码
} catch (ExceptionType e) {
    // 处理异常的代码
}
```
### 示例
假设我们有一个文件`example.txt`，我们想要读取它的内容并打印到控制台。使用`try-with-resources`，我们可以确保文件在读取后会被正确关闭。
```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
public class TryWithResourcesExample {
    public static void main(String[] args) {
        // 使用try-with-resources自动管理BufferedReader资源
        try (BufferedReader br = new BufferedReader(new FileReader("example.txt"))) {
            String line;
            while ((line = br.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            System.err.println("发生IO异常: " + e.getMessage());
        }
        // 不需要显式关闭BufferedReader，try-with-resources会自动处理
    }
}
```
在这个例子中：
- `BufferedReader`是一个实现了`AutoCloseable`接口的资源。
- 我们在`try`的括号内初始化了这个资源。
- 在`try`代码块内，我们读取文件的内容。
- 无论读取过程中是否发生异常，`BufferedReader`都会在`try`代码块执行完毕后自动关闭。
这种方式简化了资源管理，使得代码更加简洁和健壮。

### ry-with-resources 处理多个资源
try-with-resources 语句中可以声明多个资源，方法是使用分号 **;** 分隔各个资源：

```java
import java.io.*;
import java.util.*;
class RunoobTest {
    public static void main(String[] args) throws IOException{
        try (Scanner scanner = new Scanner(new File("testRead.txt")); 
            PrintWriter writer = new PrintWriter(new File("testWrite.txt"))) {
            while (scanner.hasNext()) {
                writer.print(scanner.nextLine());
            }
        }
    }
}
```

以上实例使用 `Scanner` 对象从 `testRead.txt` 文件中读取一行并将其写入新的 `testWrite.txt` 文件中。

多个声明资源时，`try-with-resources` 语句以相反的顺序关闭这些资源。 在本例中，`PrintWriter` 对象先关闭，然后 `Scanner` 对象关闭。

## 类实例对象监测

要实时检测某个类在当前程序中的实例数量，我们可以使用一个静态变量来跟踪实例化的次数。每次创建新实例时，我们增加这个计数器；每次实例被垃圾回收时，我们减少这个计数器。为了实现后者，我们可以使用Java的`finalize()`方法，但是请注意，`finalize()`方法不保证被及时调用，并且它的使用通常是不推荐的。从Java 9开始，`finalize()`方法已被弃用。
一个更现代的方法是使用Java的`java.lang.ref.PhantomReference`和`java.lang.ref.ReferenceQueue`，这样可以更准确地跟踪对象的垃圾回收。
以下是一个使用`PhantomReference`和`ReferenceQueue`来实时检测类实例数量的示例：
```java
import java.lang.ref.PhantomReference;
import java.lang.ref.ReferenceQueue;
public class InstanceTracker {
    private static int instanceCount = 0;
    private static ReferenceQueue<InstanceTracker> referenceQueue = new ReferenceQueue<>();
    private static PhantomReference<InstanceTracker> phantomReference;
    public InstanceTracker() {
        instanceCount++;
        phantomReference = new PhantomReference<>(this, referenceQueue);
    }
    @Override
    protected void finalize() throws Throwable {
        try {
            super.finalize();
        } finally {
            instanceCount--;
        }
    }
    public static int getInstanceCount() {
        // 清理已经被回收的实例
        while ((phantomReference = (PhantomReference<InstanceTracker>) referenceQueue.poll()) != null) {
            instanceCount--;
        }
        return instanceCount;
    }
    public static void main(String[] args) throws InterruptedException {
        System.out.println("Initial instance count: " + InstanceTracker.getInstanceCount());
        InstanceTracker instance1 = new InstanceTracker();
        System.out.println("Instance count after creating instance1: " + InstanceTracker.getInstanceCount());
        InstanceTracker instance2 = new InstanceTracker();
        System.out.println("Instance count after creating instance2: " + InstanceTracker.getInstanceCount());
        // 强制垃圾回收
        instance1 = null;
        System.gc();
        Thread.sleep(100); // 等待垃圾回收完成
        System.out.println("Instance count after garbage collecting instance1: " + InstanceTracker.getInstanceCount());
        instance2 = null;
        System.gc();
        Thread.sleep(100); // 等待垃圾回收完成
        System.out.println("Instance count after garbage collecting instance2: " + InstanceTracker.getInstanceCount());
    }
}
```
在这个示例中，我们定义了一个`InstanceTracker`类，它有一个静态变量`instanceCount`来跟踪实例的数量。每次创建`InstanceTracker`的新实例时，`instanceCount`都会增加。我们使用`PhantomReference`和`ReferenceQueue`来检测对象何时被垃圾回收，并在`getInstanceCount`方法中更新`instanceCount`。
请注意，这个示例中的`System.gc()`和`Thread.sleep()`是为了演示目的，强制垃圾回收并等待其完成。在实际应用中，不推荐依赖这些方法来管理内存，因为垃圾回收的行为是不可预测的。
此外，这个示例仅用于演示如何跟踪实例数量，并不推荐作为生产环境中的解决方案。在生产环境中，更好的做法是使用现有的库或框架来管理对象的生命周期和资源。