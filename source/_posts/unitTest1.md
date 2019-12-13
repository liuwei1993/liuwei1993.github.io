---
title: 单元测试入门
date: 2017-03-04
categories: Android
tag: 单元测试
---

## 单元测试准备工作

### 一、单测工具的选择：JUnit + Robolectric + Jacoco

   AndroidTest 在前期做过尝试，需要依赖真机或模拟器，还要经历编译打包安装过程，开发效率低下
   JUnit 是在 PC 端执行测试代码，编译速度快便于调试，但无法使用 Android api
   Robolectric 可以模拟 Android 的 Context 环境，与 JUnit 相结合很好的解决了上述问题，不过也存在一些坑
  Jacoco 是用来评估单元测试覆盖率的

### 二、代码配置：

 1、**引入官方默认依赖**

 Android Studio 默认会添加一些依赖，给代码添加依赖就跟给普通的 flavor 添加依赖一样

```groovy
dependencies {
     // 使用 junit 进行本地单元测试
     testImplementation 'junit:junit:4.12'
     // 使用 android test runner 和 espresso 进行仪器测试 但我们不使用
     // androidTestImplementation 'com.android.support.test:runner:1.0.2'
     // androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
     //使用 Robolectric
     testImplementation 'org.robolectric:robolectric:4.3'
}
//根目录配置 jacoco
zhihu {
    jacoco {
        useDefaultCoverage true
    }
}
```



**2、允许 robolectric 读取 assets、resources 和 manifests，在 build.gradle 中添加**

```groovy
android {
    testOptions {
        unitTests {
            includeAndroidResources = true
        }
    }
}
```



**3、在 gradle.properties 中添加**

```
android.enableUnitTestBinaryResources = true
```



### 三、编写测试代码

使用 Robolectric 与 junit 基本一致，唯一不同的地方在于要声明使用 `RobolectricTestRunner` 以及做一些配置：

使用 `RobolectricTestRunner` ，在单元测试类上添加注解即可，示例：

```java
@RunWith(RobolectricTestRunner.class)
public class MyClassTest {
 
    @Test
    public void testContext() {
        //正常情况下，Context 可以使用 RuntimeEnvironment.application
        Assert.assertNotNull(RuntimeEnvironment.application);
    }
 
}
```

