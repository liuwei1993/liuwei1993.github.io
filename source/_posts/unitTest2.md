---
title: 查看单测代码覆盖率
date: 2017-03-05
categories: Android
tag: 单元测试
---

## 查看单测代码覆盖率

### 查看单测代码覆盖率

**工程根目录执行命令：**

```bash
./gradlew :${moduleName}:jacocoTestReport
```

漫长的等待后，在指定目录 **${module}/build/reports/jacoco/jacocoTestReport/html/** 中找到 **index.html** 文件，使用浏览器打开就可以查看覆盖率：

![image1](unitTest2-2.png)

![image1](unitTest2-1.png)

页面末尾可以看到对应覆盖率