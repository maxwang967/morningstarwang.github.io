---
layout: post
title:  "【和我一起Flutter系列视频】01 Notes"
date:   2020-03-07
categories: coding
---
> 本期视频主要对Flutter进行了简单描述并对开发的准备工作进行了简要说明。

## 资源列表
- Flutter 官网: [https://flutter.dev/](https://flutter.dev/)
- Visual Studio Code: [https://code.visualstudio.com/](https://code.visualstudio.com/)
- Android Studio: [https://developer.android.google.cn/studio](https://developer.android.google.cn/studio)
- Windows环境配置流程：[https://flutter.dev/docs/get-started/install/windows](https://flutter.dev/docs/get-started/install/windows)
- MacOS环境配置流程：[https://flutter.dev/docs/get-started/install/macos](https://flutter.dev/docs/get-started/install/macos)

## 特别提示

### Windows环境

1. 配置环境变量的方式：Win+Q键调出搜索栏，输入env回车进入环境变量配置。
2. 需要配置的环境变量：
   - PATH：在用户变量中，双击PATH，添加下载解压好的Flutter Engine路径下的bin目录。建议配置到C:\src\flutter\bin下。检查的方式是到该目录下查找是否存在flutter.bat文件。
   - PUB_HOSTED_URL: 在用户变量中，新建该变量设置值为https://pub.flutter-io.cn
   - FLUTTER_STORAGE_BASE_URL: 在用户变量中，新建该变量设置值为https://storage.flutter-io.cn 
3. 检测配置是否成功的方式：Win+R键调出运行对话框，输入cmd回车。输入flutter doctor回车查看是否能够输入flutter检测信息。


### MacOS环境

Cmd+Space输入terminal回车，输入下述命令：

```zsh
open ~/.zshrc
```

在文件尾端添加如下代码并保存：

```zsh
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
export PATH="/Users/morningstarwang/flutter/bin:$PATH"
```


其中/Users/morningstarwang/flutter/bin需要修改为你实际解压Flutter的bin目录的路径。

输入下述命令：

   
```zsh
source ~/.zshrc
flutter doctor
```


查看是否输出了类似下述内容：



```zsh
Doctor summary (to see all details, run
flutter doctor -v):
[✓] Flutter (Channel stable, v1.12.13+hotfix.9-pre.1,
    on Mac OS X 10.15.3 19D76, locale en-CN)

[!] Android toolchain - develop for Android devices (Android SDK version 29.0.1)
    ! Some Android licenses not accepted.  To resolve this, run: flutter doctor 
[✓] Xcode - develop for iOS and macOS (Xcode 11.3.1)
[✓] Android Studio (version 3.5)
[!] IntelliJ IDEA Ultimate Edition (version 2020.1 EAP)
    ✗ Flutter plugin not installed; this adds Flutter specific functionality.
    ✗ Dart plugin not installed; this adds Dart specific functionality.
[✓] VS Code (version 1.42.1)
[!] Connected device
    ! No devices available

! Doctor found issues in 3 categories.
```



## 问题解答

1. 方式一：B站站内信私信我
2. 方式二：发邮件至morningstarwang@outlook.com
3. 在视频下方评论



感谢大家支持！