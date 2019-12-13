---
title: 踩坑记录
date: 2017-03-05
categories: Android
tag: 单元测试
---

## 踩坑记录

## 1、首先第一个坑，可能其他组件也会遇到，就是编译时，stetho 拦截器找不到，导致编译不通过，gradle 中需要加上：

```groovy
//测试用例中 stetho 找不到 原因未知
testImplementation 'com.facebook.stetho:stetho-okhttp3:1.5.1'
```



2、有些基础库，比如 JsonUtils，Net等，会使用 BaseApplication.INSTANCE ，会出现空指针，不过找到了完美解决方案

```java
@RunWith(RobolectricTestRunner.class)
@Config(
    // 指定 Application 为 BaseApplication，可以不写
    application = BaseApplication.class
)
public class MyClassTest {
 
    @Test
    public void testContext() {
        //正常情况下，Context 可以使用 RuntimeEnvironment.application
        Assert.assertNotNull(BaseApplication.INSTANCE);
    }
 
}
```

3、有一些基础库需要先初始化才能使用，如果多个测试方法使用该库，建议在 before 中进行初始化

4、kotlin 中使用单元测试有个小坑：

```java
@RunWith(RobolectricTestRunner::class)
@Config(
    // 指定 Application 为 BaseApplication，可以不写
    application = BaseApplication::class
)
class PresetManagerUnitTest {
 
    @Test
    fun testDataFilter() {
        SearchPresetManager.mooc()
    }
}
```

在 kotlin 中，注解引用 java class 时，使用 ${className}::class ，而不是 ${className}.class 