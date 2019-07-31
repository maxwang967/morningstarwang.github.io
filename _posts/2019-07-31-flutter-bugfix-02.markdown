---
layout: post
title:  "Flutter生产项目开发笔记02—配置AndroidX"
date:   2019-07-31 22:40:28 +0800
categories: solution
---
# Flutter侧配置
团队为了方便后续对Android端代码进行优化，直接使用了安装好Plugin的Android Studio进行项目创建。为避免不必要的兼容问题，采用了Java语言的Android+OC语言的iOS。团队后续准备将Java迁移至Kotlin，同时将OC迁移至Swift 5。

项目创建成功后，执行下面命令切换flutter至stable频道并完成版本更新：

```bash
flutter channel stable
flutter upgrade
```

并且获取最新packages并尝试进行编译:

```bash
flutter packages get
flutter run
```

之后，添加项目所需的全部dependency，然后再次执行上述命令完成flutter编译工作。

# Android侧配置
首先，在Android Studio中打开项目目录下的android文件夹，并等待Gradle完成编译。

待左侧Android视图中出现全部Plugin后，全选，点击菜单栏中的Refactor->Migrate to AndroidX...（如下图所示）
![](https://blog.morningstarwang.com/wp-content/uploads/2019/07/BD6C7B8F-7141-42C2-9684-F65E36DEDC08.jpg)

完成迁移后，将全部Plugin的build.gradle版本均进行调整，全部调整至：

```gradle
compileSdkVersion 28
```

然后Sync，等待Gradle完成编译。

之后，运行项目，此时Gradle进入Build阶段。通常来说，这里会出现错误。目前预期错误有以下几种：
1. 提示找不到AppCompat中的相关包：解决方案是进入提示的错误文件中，将import的红字删除，重新导入AndroidX命名空间下的包即可。此操作可能需要重复进行多次，修改不同包下的错误引用，直至Build通过为止。
2. 提示缺少androidx....包或者提示androidx....包版本应为...：解决方案是在app的build.gradle中加入对应要求的包和版本的依赖。此处一定使用implementation进行依赖引入，示例代码如下所示：

```gradle
dependencies {
    implementation 'com.amap.api:location:latest.integration'
    implementation 'org.apache.httpcomponents:httpcore:4.4.2'
    implementation 'androidx.media:media:1.0.0'
    implementation 'androidx.legacy:legacy-support-v4:1.0.0'
    implementation 'androidx.fragment:fragment:1.1.0-alpha04'
    implementation 'androidx.legacy:legacy-support-core-ui:1.0.0'
    implementation 'androidx.legacy:legacy-support-core-utils:1.0.0'
    implementation 'androidx.loader:loader:1.0.0'
    implementation 'androidx.viewpager:viewpager:1.0.0'
    implementation 'androidx.coordinatorlayout:coordinatorlayout:1.0.0'
    implementation 'androidx.drawerlayout:drawerlayout:1.0.0'
    implementation 'androidx.slidingpanelayout:slidingpanelayout:1.0.0'
    implementation 'androidx.customview:customview:1.0.0'
    implementation 'androidx.swiperefreshlayout:swiperefreshlayout:1.0.0'
    implementation 'androidx.asynclayoutinflater:asynclayoutinflater:1.0.0'
    implementation 'androidx.core:core:1.0.1'
    implementation 'androidx.versionedparcelable:versionedparcelable:1.1.0-alpha01'
    implementation 'androidx.collection:collection:1.0.0'
    implementation 'androidx.documentfile:documentfile:1.0.0'
    implementation 'androidx.localbroadcastmanager:localbroadcastmanager
}
```

完成此步骤后，Gradle会首先Sync一遍。Sync未出现错误后，依然需要运行项目尝试Build。

当Build不出现错误且项目成功运行时，则意味着AndroidX迁移工作已经完成。需要注意的是，后续引入任何Plugin或者第三方原生库均需要进行了以上步骤的版本检查。由于目前Flutter尚属快速迭代阶段，建议开发者使用stable channel同时严格按照本方案进行配置，以免遇到其他无法预期的问题。https://www.yejinmo.com/games/Mario/Mario.html "Play")