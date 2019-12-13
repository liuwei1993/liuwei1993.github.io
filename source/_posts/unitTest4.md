---
title: 万能 Logger
date: 2017-03-05
categories: Android
tag: 单元测试
---

## 万能 Logger

### 可定位代码位置&Android环境与单测通用的 Logger

为了方便大家在开发环境和单元测试环境都能看到日志，开发了此工具

```kotlin
object Logger {
 
    //根据是否是 x86 架构来判断是不是手机（目前最取巧的方法）
    private val isAndroid: Boolean by lazy {
        try {
            !System.getProperty("os.arch")!!.contains("x86")
        } catch (e: ClassNotFoundException) {
            false
        }
    }
 
    private fun debug(tag:String, msg: Any?) {
        if (isAndroid) {
            Log.d(tag, msg.toString())
        } else{
            println(msg)
        }
    }
 
    private fun error(tag:String, msg: Any?, e:Throwable? = null) {
        if (isAndroid) {
            Log.e(tag, msg.toString(), e)
        } else{
            System.err.println(msg)
            e?.let {
                System.err.println(it)
            }
        }
    }
 
    private val dateFormat by lazy { FastDateFormat.getInstance("HH:mm:ss:SSS") }
    //使用 系统时间（Android log 自带时间所以不需要携带）+ 线程名字 + 代码行号 来构造日志前缀
    private val header = { stackTraceElement: StackTraceElement ->
        when (isAndroid) {
            true -> "[${Thread.currentThread().name}] "
            false -> "${dateFormat.format(Date(System.currentTimeMillis()))} [${Thread.currentThread().name}] "
        } + codeLine(stackTraceElement)
    }
 
    private fun codeLine(ste: StackTraceElement): String {
        val buf = StringBuilder()
        buf.append("${ste.className.split(".").last()}.${ste.methodName}")
        if (ste.isNativeMethod) {
            buf.append("(Native Method)")
        } else {
            val fName = ste.fileName
            if (fName == null) {
                buf.append("(Unknown Source)")
            } else {
                val lineNum = ste.lineNumber
                buf.append('(')
                buf.append(fName)
                if (lineNum >= 0) {
                    buf.append(':').append(lineNum)
                }
                buf.append("):")
            }
        }
        return buf.toString()
    }
 
 
    @JvmStatic
    fun d(tag:String, msg: Any?) {
        val stackTrace =
            Thread.currentThread().stackTrace.dropWhile { it.fileName != "Logger.kt" || it.methodName != "d" }
        val lineNum= if (stackTrace.size <= 1)  "" else header(stackTrace[1])
        debug(tag, "$lineNum $msg")
    }
 
    @JvmStatic
    fun e(tag:String, msg: Any?, e:Throwable? = null)  {
        val stackTrace =
            Thread.currentThread().stackTrace.dropWhile { it.fileName != "Logger.kt" || it.methodName != "e" }
        val lineNum= if (stackTrace.size <= 1)  "" else header(stackTrace[1])
        error(tag, "$lineNum $msg", e)
    }
}
```

使用方法：

跑在单元测试代码中：

```kotlin
@Test
fun test1() {
    Logger.d("test","hello test2，告诉我有异常吗？")
    test2()
}
 
fun test2() {
    Logger.e("test","hello test1", Exception("似乎出了点问题～"))
}
```

控制台输出结果：箭头处点击可以跳转到指定代码位置

![image1](/Users/simonliu/Documents/博客/技术/Android/单元测试/unittest4-1.png)

运行在手机端

```kotlin
class MainActivity : AppCompatActivity() {
 
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        test1()
    }
 
    fun test1() {
        Logger.d("test","hello test2，告诉我有异常吗？")
        test2()
    }
 
    fun test2() {
        Logger.e("test","hello test1", Exception("似乎出了点问题～"))
    }
 
}
```

Logcat 输出结果：箭头处点击可以跳转到指定代码位置

![image2](/Users/simonliu/Documents/博客/技术/Android/单元测试/unittest4-2.png)

如此依赖，无论是业务代码中还是单元测试代码中，都可以很方便的使用 Logger 来打印日志，在单测中也可以很方便的在控制台看到业务代码中打印的日志

代码量很小，大家可以根据需要进行一定的修改后使用，欢迎提供建议和指导

