# Kotlin DSL Primer

## 概述

`kotlin`早已经在`Android`开发中普及了，不过目前大部分工程的`gradle`配置文件还是沿用之前的基于`groovy`的配置。是时候切换到`kotlin DSL`了，使用一种语言来进行开发和配置更为统一，也不需要经常去查`groovy`的语法。

相比`groovy`而言，`kotlin`的语法总体来讲有以下几个典型的不同

- `groovy`中字符串可以用单引号或双引号，`kotlin`只能使用双引号
- `groovy`方法调用可以省略括号，`kotlin`不能省略
- `groovy`变量赋值可以省略括号，`kotlin`不能省略

## 从`groovy DSL`切换到`kotlin DSL`

### 修改工程配置文件

将`settings.gradle`文件重命名为`settings.gradle.kts`文件，修复语法错误

```
rootProject.name = "KotlinDslPrimer"

include ":app"
```
```
rootProject.name = "KotlinDslPrimer"

include(":app")
```

### 修改全局配置

- 将根目录下的`build.gradle`文件重命名为`build.gradle.kts`文件
- 将全局变量的定义挪出`buildscript`目录，并使用 `kotlin`语法重新定义
```
ext.kotlin_version = "1.5.20"
```
```
extra["kotlinVersion"] = "1.5.20"
```
- 修复语法错误，主要是单引号改为双引号，方法调用和变量定义加括号
```
classpath "com.android.tools.build:gradle:4.2.2"
classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
```
```
classpath("com.android.tools.build:gradle:4.2.2")
classpath(::"org.jetbrains.kotlin:kotlin-gradle-plugin:1.5.20")
```
- `clean task`重新定义
```
task clean(type: Delete) {
    delete rootProject.buildDir
}
```
```
tasks {
    val clean by registering(Delete::class) {
        delete(buildDir)
    }
}
```

### 修改模块配置

- `plugins`定义
```
plugins {
    id 'com.android.application'
    id 'kotlin-android'
}
```
```
plugins {
    id("com.android.application")
    kotlin("android")
}
```

- `buildTypes`定义
```
buildTypes {
    release {
        minifyEnabled false
        proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
    }
}
```
```
buildTypes {
    getByName("release") {
        isMinifyEnabled = false
        proguardFiles(
            getDefaultProguardFile("proguard-android-optimize.txt"),
            "proguard-rules.pro"
        )
    }
}
```

- 语法错误修复（括号）

## 参考文档

- [Gradle Kotlin DSL Primer](https://docs.gradle.org/current/userguide/kotlin_dsl.html)
- [Migrating build logic from Groovy to Kotlin](https://docs.gradle.org/current/userguide/migrating_from_groovy_to_kotlin_dsl.html#migrating_groovy_kotlin)
- [配置构建](https://developer.android.com/studio/build/#kts)
- [gradle-tips](https://developer.android.com/studio/build/gradle-tips?hl=zh-cn)
