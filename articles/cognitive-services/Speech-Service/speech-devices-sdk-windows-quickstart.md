---
title: 快速入门：在 Windows 上运行语音设备 SDK - 语音服务
titleSuffix: Azure Cognitive Services
description: Windows 语音设备 SDK 入门的先决条件和说明。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
origin.date: 11/13/2019
ms.date: 01/27/2020
ms.author: v-tawe
ms.openlocfilehash: c6b2fecfda6d955ce61f66775e8d6a478c9c10d2
ms.sourcegitcommit: 94e1c9621b8f81a7078f1412b3a73281d0a8668b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76123446"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-windows"></a>快速入门：在 Windows 上运行语音设备 SDK 示例应用

本快速入门介绍如何使用适用于 Windows 的语音设备 SDK 来生成支持语音的产品。 目前，只有 [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) 受支持。 对于其他语音，支持使用提供麦克风阵列几何的线性麦克风阵列。

该应用程序是使用语音 SDK 程序包和 Eclipse Java IDE (v4) 在 64 位 Windows 上构建的。 它在 64 位 Java 8 运行时环境 (JRE) 中运行。

本指南需要一个包含语音服务资源的 [Azure 认知服务](get-started.md)帐户。 如果没有帐户，可以使用[试用帐户](https://www.azure.cn/pricing/1rmb-trial/)获取订阅密钥。

[示例应用程序](https://aka.ms/sdsdk-download-JRE)的源代码随附在语音设备 SDK 中， 也可在 [GitHub 上获取](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)。

## <a name="prerequisites"></a>必备条件

本快速入门需要：

* 操作系统：64 位 Windows
* 麦克风阵列，如 [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* 仅限 [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) 或 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)。
* [Microsoft Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* 语音服务的 Azure 订阅密钥。 [免费获得一个](get-started.md)。
* 下载用于 Java 的[语音设备 SDK](https://aka.ms/sdsdk-download-JRE) 的最新版本，并将 .zip 提取到工作目录。
   > [!NOTE]
   > JRE-Sample-Release.zip 文件包含 JRE 示例应用，本快速入门假设该应用提取到 C:\SDSDK\JRE-Sample-Release

<!-- Conversation Transcription is currently only available for "en-US" and "zh-CN", in the “chinaeast2” region. You must have a speech key in one of those regions to use Conversation Transcription. -->

如果计划使用意向，则将需要[语音理解服务 (LUIS)](https://docs.azure.cn/cognitive-services/LUIS/luis-how-to-azure-subscription) 订阅。 [示例 LUIS 模型](https://csspeechstorage.blob.core.windows.net/azure-kinect-dk/drop/LUIS-example.json)适用于此应用。

## <a name="create-and-configure-the-project"></a>创建和配置项目

1. 启动 Eclipse。

1. 在 Eclipse IDE Launcher 的“工作区”字段中，输入新工作区目录的名称   。 然后选择“启动”。 

   ![Eclipse Launcher 的屏幕截图](media/speech-devices-sdk/eclipse-launcher.png)

1. 片刻之后，Eclipse IDE 的主窗口将会显示。 如果出现了欢迎屏幕，请将其关闭。

1. 从 Eclipse 菜单栏上，依次选择“文件”   > “新建”   > “Java 项目”以新建一个项目  。 如果不可用，请依次选择“项目”  和“Java 项目”  。

1. 此时将启动“新建 Java 项目”向导  。 **浏览**示例项目所在的位置。 选择“完成”。 

   ![“新建 Java 项目”向导的屏幕截图](media/speech-devices-sdk/eclipse-new-java-project.png)

1. 在“包资源管理器”  中，右键单击你的项目。 从上下文菜单中选择“配置” > “转换为 Maven 项目”。   选择“完成”。 

   ![包资源管理器的屏幕截图](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. 打开 pom.xml 文件并对其进行编辑。

    在文件末尾，在结束标记 `</project>` 之前，创建 `repositories` 和 `dependencies` 元素（如此处所示），并确保 `version` 与当前版本匹配：
    ```xml    
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.7.0</version>
        </dependency>
    </dependencies>
   ```

1. 将 Windows-x64  的内容复制到 Java 项目位置，如 **C:\SDSDK\JRE-Sample-Release**

1. 将 `kws.table`、`participants.properties`、`Microsoft.CognitiveServices.Speech.extension.pma.dll` 复制到项目文件夹 **target\classes**

## <a name="configure-the-sample-application"></a>配置示例应用程序

1. 将语音订阅密钥添加到源代码。

   对于语音和 LUIS，你的信息将进入 `FunctionsList.java`：

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "chinaeast2";
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "chinaeast2";
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   <!-- If you are using conversation transcription, your speech key and region information are also needed in `Cts.java`: -->

1. 默认关键字为“Computer”。 还可以尝试所提供的其他关键字之一，例如“Machine”或“Assistant”。 这些备用关键字的资源文件位于语音设备 SDK 的 keyword 文件夹中。 例如，`C:\SDSDK\JRE-Sample-Release\keyword\Computer` 包含用于关键字“Computer”的文件。

    <!-- > [!TIP] -->
    <!-- > You can also [create a custom keyword](speech-devices-sdk-create-kws.md). -->

    要使用新的关键字，请更新 `FunctionsList.java` 中的下面一行，并将关键字复制到应用。 例如，要使用关键字包 `machine.zip` 中的关键字“Machine”，请执行以下操作：

   * 将 `kws.table` 文件从 zip 包复制到项目文件夹“target/classes”中  。
   * 使用关键字名称更新 `FunctionsList.java`：

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>从 Eclipse 运行示例应用程序

1. 从 Eclipse 菜单栏中，依次选择“运行”   > “运行方式”   >   “Java 应用程序”。 然后选择“FunctionsList”和“确定”。  

   ![选择 Java 应用程序的屏幕截图](media/speech-devices-sdk/eclipse-run-sample.png)

1. 语音设备 SDK 示例应用程序将会启动，并显示以下选项：

   ![示例语音设备 SDK 的示例应用程序和选项](media/speech-devices-sdk/java-sample-app-windows.png)

<!-- 1. Try the new **Conversation Transcription** demo. Start transcribing with **Session** > **Start**. By default everyone is a guest. However, if you have participant’s voice signatures they can be put into a file `participants.properties` in the project folder **target/classes**. To generate the voice signature, look at [Transcribe conversations (SDK)](how-to-use-conversation-transcription-service.md). -->

<!-- ![Demo Conversation Transcription application](media/speech-devices-sdk/cts-sample-app-windows.png) -->

## <a name="create-and-run-a-standalone-application"></a>创建并运行独立应用程序

1. 在“包资源管理器”  中，右键单击你的项目。 选择“导出”  。

1. 此时将显示“导出”  窗口。 展开“Java”  ，选择“可运行的 JAR 文件”  ，然后选择“下一步”  。

   ![“导出”窗口的屏幕截图](media/speech-devices-sdk/eclipse-export-windows.png)

1. 此时将显示“可运行的 JAR 文件导出”  窗口。 为应用程序选择“导出目标”  ，然后选择“完成”  。

   ![“可运行的 JAR 文件导出”的屏幕截图](media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. 请将 `kws.table`、`participants.properties`、`unimic_runtime.dll`、`pma.dll` 和 `Microsoft.CognitiveServices.Speech.extension.pma.dll` 放入上面选择的目标文件夹中，因为该应用程序需要这些文件。

1. 运行独立的应用程序

   ```powershell
   java -jar SpeechDemo.jar
   ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [查看发行说明](devices-sdk-release-notes.md)