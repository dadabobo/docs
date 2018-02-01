
## JUnit

#### Java 单元测试如何断言(检查)控制台输出

关于在 JUnit 单元测试中如何断言某个函数的控制台输出已是我一个长久的问题. 虽然有控制台输出的函数有了副作用, 不能称之为一个纯函数, 在讲求函数式编程的今天, 纯函数是最好测试的, 所谓的 Data In, Data Out. 但总还是有这样的需求, 比如自己实现的某个日志框架的 Appender, 需要验证它向控制台的输出内容.

我先前在项目中的办法是, 先把把标准输出定向到一个 `ByteArrayOutputStream` 中去, 完后把这个流转成字符串来断言它的内容, 最后恢复标准输出为 `System.out` , 代码如下:

````Java
ByteArrayOutputStream output = new ByteArrayOutputStream();
System.setOut(new PrintStream(output));
System.out.print("Hello");
assertThat(output.toString(), is("Hello"));
System.setOut(System.out);
````

这样也能完成任务, 本质也是对的, 但稍显复杂了些. 今天读 `Spring in Action` 一书, 发现它用了 `StandardOutputStreamLog` 这个 JUnit 的 `@Rule` , 来自于 System Rules . 其实 `StandardOutputStreamLog` 类已不推荐使用, 取而代之的是 `SystemOutRule` , 所以应用 `SystemOutRule` 来断言控制台输出的测试方法就是

````java
@Rule
public final SystemOutRule log = new SystemOutRule().enableLog();

@Test
public void testConsoleOutput() {
	log.clearLog();
	System.out.print("Hello");
	assertThat(log.getLog(), is("Hello"));
}
````

上面的代码在控制台也能看到同样的输出, `log.getLog()` 也保存有标准输出的内容, 如果不想在控制台看到输出可以执行 `log.mute()` 但是再恢复让控制台的内容输出就有小麻烦了.
既然有 `SystemOutRule`, 自然就有 `SystemErrRule`, 用于捕获向标准错误输出的内容。

If your code under test writes the correct new line characters to `System.out` then the test output is different at different systems. `getLogWithNormalizedLineSeparator()` provides a log that always uses \n as line separator. This makes it easy to write appropriate assertions that work on all systems.
