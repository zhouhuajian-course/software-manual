# InteliJ IDEA

## 行断点 方法断点 字段断点 异常断点

https://www.jetbrains.com/help/idea/using-breakpoints.html#breakpoint-icons

![image](https://github.com/user-attachments/assets/de4bbd39-be98-4b74-a7fb-3be64bfa2ca3)


## Gradle 设置所有项目 使用 阿里镜像源

**创建目录**

GRADLE_USER_HOME （默认 C:\Users\zhouhuajian\.gradle） 下创建目录 init.d （没有时创建）

**创建文件**

build.gradle

```
allprojects {
    repositories {
        maven {
            url 'https://maven.aliyun.com/repository/public/'
        }
        maven {
            url 'https://maven.aliyun.com/repository/google/'
        }
    }
    buildscript {
        repositories {
            maven{ url 'https://maven.aliyun.com/repository/public/'}
            maven{ url 'https://maven.aliyun.com/repository/google/'}
        }
    }
}
```

## Gradle 下载 出现 Connect timed out

**原因**

gradle/wrapper/gradle-wrapper.properties 需要下载

`distributionUrl=https\://services.gradle.org/distributions/gradle-8.2.1-all.zip`

这个下载，会跳转到 Github https://github.com/gradle/gradle-distributions/releases/download/v8.2.1/gradle-8.2.1-all.zip

Gihub 连不上 导致 Connect timed out

**解决办法**

直接下载 https://github.com/gradle/gradle-distributions/releases/download/v8.2.1/gradle-8.2.1-all.zip

**zip包 放到下面目录里面**

有个版本一样的目录，那是未下载好的目录，放到里面，例如

`C:\Users\zhouhuajian\.gradle\wrapper\dists\gradle-8.2.1-all\d8pvvlun5bx6sdtwqhf8y9z4b`

`distributionBase=GRADLE_USER_HOME`  

`distributionPath=wrapper/dists`

**然后，重新 sync，成功**
