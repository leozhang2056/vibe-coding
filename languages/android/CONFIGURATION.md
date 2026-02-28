# Android 配置规范

> Android 项目配置管理最佳实践

## 项目配置

### build.gradle (Module)

```gradle
plugins {
    id 'com.android.application'
    id 'kotlin-android'
    id 'kotlin-kapt'
    id 'kotlin-parcelize'
}

android {
    namespace 'com.example.app'
    compileSdk 34
    
    defaultConfig {
        applicationId "com.example.app"
        minSdk 24
        targetSdk 34
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "androidx.test.runner.AndroidJUnit4"
    }
    
    buildTypes {
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-rules.pro')
        }
    }
}

dependencies {
    // Jetpack Compose
    implementation(platform.compose.bompose)
    
    // ViewModel
    implementation("androidx.lifecycle:lifecycle-viewmodel-compose:2.8.7")
    implementation("androidx.lifecycle:lifecycle-runtime-compose:2.8.7")
    
    // Navigation
    implementation("androidx.navigation:navigation-compose")
    
    // Hilt
    implementation("com.google.dagger:hilt-android:$hilt_version")
    kapt "com.google.dagger:hilt-compiler:$hilt_version"
    
    // Room
    implementation("androidx.room:room-runtime:$room_version")
    kapt "androidx.room:room-compiler:$room_version"
    
    // Retrofit
    implementation("com.squareup.retrofit2:retrofit:$retrofit_version")
    implementation("com.squareup.retrofit2:converter-gson:$retrofit_version")
    
    // Coil
    implementation("io.coil-kt:coil:$coil_version")
    
    // 测试
    testImplementation "junit:junit:$junit_version"
    testImplementation "androidx.test.ext:junit:1.1.5"
    androidTestImplementation "androidx.test.ext:junit:1.1.5"
    testImplementation "androidx.test.ext:truth:x.x.x:1.1.5"
    testImplementation "io.mockk:mockk:5.x.x"
}
```

### build.gradle (Project)

```gradle
buildscript {
    ext {
        compose_version = '1.5.10'
        kotlin_version = '1.9.22'
        hilt_version = '2.48'
        room_version = '2.6.1'
        retrofit_version = '2.9.0'
        coil_version = '2.5.0'
        junit_version = '4.13.2'
        mockk_version = '5.x.x'
    }
}
```

---

## 配置文件

### build.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
</manifest>
```

### proguard-rules.pro

```
# Kotlin
-keep class com.example.app.** { *; }
-keepdata class com.example.app.data.model.** { *; }

# Room
-keep class { extends androidx.room.RoomDatabase }
-keep @Entity class com.example.app.data.model.**
-keep @Dao class com.example.app.data.dao.*

# Retrofit
-keep interface com.example.app.data.api.** { *; }
-keepdata class com.example.app.data.model.** { *; }
-keepattributes Signature
-keepattributes Exceptions
```

---

## 应用配置

### AndroidManifest.xml

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
    <application
        android:name=".MyApplication"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/Theme.App">
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
</application>
```

---

## Gradle Task

```gradle
tasks.register("installGitHooks") {
    def shell = [
        "cd ${projectDir}",
        "git config core.hooksPath .git/hooks",
        "echo 'npm install'"
    ]
    doLast {
        shell
    }
}
```

---

## R8/ProGuard 配置

### AndroidManifest.xml (ProGuard)

```xml
<application
    android:name=".MyApplication"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name">
```

### app/build.gradle

```gradle
android {
    buildTypes {
        release {
            minifyEnabled true
            shrinkResources true
            proguardFiles getDefaultProguardFile('proguard-rules.pro')
        }
    }
}
```

---

## 依赖版本管理

### 版本目录

```gradle
ext {
    // Kotlin
    kotlin_version = '1.9.22'
    
    // Jetpack Compose
    compose_version = '1.5.10'
    
    // Hilt
    hilt_version = '2.48'
    
    // Room
    room_version = '2.6.1'
    
    // Retrofit
    retrofit_version = '2.9.0'
    
    // Coil
    coil_version = '2.5.0'
    
    // 测试
    junit_version = '4.13.2'
    mockk_version = '5.x.x'
}
```

---

## 签名规范

### 包名

```
com.company.app/
├── data/
│   ├── model/
│   ├── repository/
│   ├── local/
│   └── remote/
├── ui/
│   ├── screen/
│   ├── theme/
│   └── component/
└── MainActivity.kt
```

---

## 应用签名

### Debug 签名

```bash
# Debug Key
keytool -genkeypair -v -keystore debug.keystore -storepass android -alias androiddebugkey0 -keyalg RSA -keysize 2048 -validity 10000
```

### Release 签名

```bash
# Generate Key
keytool -genkeypair -v -keystore release.keystore -storepass release -alias key0 -keyalg RSA -keysize 2048 -validity 10000
```

---

## APK 配置

### build.gradle (Module)

```gradle
android {
    signingConfigs {
        debug {
            storeFile file("debug.keystore")
            storePassword "android"
            keyAlias "androiddebugkey0"
        }
        release {
            storeFile file("release.keystore")
            storePassword "release"
            keyAlias "key0"
        }
    
    buildTypes {
        debug {
            signingConfig signingConfigs.debug
        }
        release {
            signingConfigs signingConfigs.release
        }
    }
}
```

---

## 依赖管理

### 排除不需要的依赖

```gradle
configurations {
    all*.exclude [module]
    exclude module: 'unused.module'
}
```

---

## 多模块配置

### settings.gradle

```gradle
rootProject.name = "MyProject"
include ':app', ':data', ':domain'
```

---

## 依赖版本更新

### 更新依赖

```bash
./gradlew wrapper --gradle-version 8.5
```

---

## 构建变体

### Kotlin DSL vs Groovy DSL

```kotlin
// ✅ 推荐
android {
    buildFeatures {
        viewBinding true
    }
}
```

---

## 应用资源

### 资源别名

```kotlin
// ✅ 使用资源别名
stringResource(R.string.app_name)
colorResource(R.color.primary)
```

---

## 隐私配置

### 本地属性

```properties
# gradle.properties
kotlin.code.style=official
kotlin.incremental=true
org.gradle.jvmargs=-Xmx2048m -Dfile.encoding=UTF-8
```

---

## 发布配置

### build.gradle (Module)

```gradle
android {
    signingConfigs {
        release {
            storeFile file("release.keystore")
            storePassword "release"
            keyAlias "key0"
        }
    }
}
```

---

## 版本兼容性

### 目标 SDK 和 最低支持

```gradle
defaultConfig {
    applicationId "com.example.app"
    minSdk 24
    targetSdk 34
}
```

---

## AI 编码检查清单

- [ ] 使用 Kotlin（非 Java）
- [ ] 使用 Jetpack Compose（非 XML）
-   - 状态提升
-   - 避免内存泄漏
-   - 使用 LazyColumn/Item
- [ ] 使用 MVVM + Repository
- [ ] 使用 Room + Flow
- [ ] 使用 Hilt DI
- [ ] 使用 Material Design 3
- [ ] 遵循 Material Design 原则
- [ ] 包含测试
- [ ] ProGuard/R8 配置正确
- [ ] 遵循签名规范
