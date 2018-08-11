# 探索 Android Studio
1. 选择项目的 Problems 视图会显示指向包含任何已识别编码和语法错误（例如布局文件中缺失一个 XML 元素结束标记）的源文件的链接。
2. 您可以随时通过按两下 Shift 键或点击 Android Studio 窗口右上角的放大镜搜索源代码、数据库、操作和用户界面的元素等。此功能非常实用，例如在您忘记如何触发特定 IDE 操作时，可以利用此功能进行查找。
3. compileSdkVersion：是告诉gradle 用哪个SDK版本来编译，和运行时要求的版本号没有关系；使用任何新添加的 API 就需要使用对应 Level 的 Android SDK。
4. buildToolsVersion：构建工具的版本，其中包括了打包工具aapt、dx等等。这个工具的目录位于sdk_path/build-tools。

# 管理项目
1. New Module之库模块  
1)Android 库：这种类型的库可以包含 Android 项目中支持的所有文件类型，包括源代码、资源和清单文件。构建结果是一个 Android 归档 (AAR) 文件，您可以将其作为 Android 应用模块的依赖项添加。  
2）Java 库：此类型的库只能包含 Java 源文件。构建结果是一个 Java 归档 (JAR) 文件，您可以将其作为 Andriod 应用模块或其他 Java 项目的依赖项添加。  
2. APK 分析器  
1)选择 Build > Analyze APK。  
2)从 app/build/outputs/apk/ 目录中选择 APK 并点击 OK。  
3. 将应用模块转换为库模块  
1)打开现有应用模块的 build.gradle 文件
       apply plugin: 'com.android.application'
2)修改为
       apply plugin: 'com.android.library'
4. 应用以依赖项形式添加您的库  
1）Import Module  
2）settings.gradle 文件
       include ':app', ':my-library-module'
3）打开应用模块的 build.gradle
       dependencies {
         compile project(":my-library-module")
       }
4）点击 Sync Project with Gradle Files

# 编写您的应用
1. 编码工作效率  
1)要插入实时模板，请输入模板缩写并按 Tab 键。如需查看支持的实时模板列表并对其进行自定义，请点击 File > Settings > Editor > Live Templates。  
2）将光标放在方法/成员/类名称上并按 F1 可查看 API 相关文档。也可查看图像和主题背景等其他资源的信息。 例如，如果您将光标放在 Android 清单文件中的主题背景名称上，并按 F1，您可以查看主题背景继承层次结构以及各种属性的颜色或图像。  
2. 使用资源  
1）Android Studio 包含一个名为 Vector Asset Studio 的工具，可帮助您创建支持各种屏幕密度的图像。 您可以上传自己的 SVG 文件进行编辑，或从 Google 提供的众多 Material Design 图标中选择一个。单击 File > New > Vector Asset 开始创建。  
2）预览图像和颜色，在代码中引用图像和图标时，左侧边缘将显示图像预览以帮助您验证图像或图标引用。如需查看完整尺寸的图像，请点击左侧边缘中的缩略图。 或者，将光标放在资源的内联引用上，然后按 F1，以查看图像的详细信息，包括所有替代尺寸。  
3）翻译 UI 字符串：要开始使用，右键点击 strings.xml 文件的任意副本，然后点击 Open Translations Editor。
3. 从模板添加代码:new->activity
