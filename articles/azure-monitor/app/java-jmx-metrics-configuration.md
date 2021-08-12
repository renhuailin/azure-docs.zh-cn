---
title: 如何配置 JMX 指标 - 适用于 Java 的 Azure Monitor Application Insights
description: 为 Azure Monitor Application Insights Java 代理配置其他 JMX 指标收集
ms.topic: conceptual
ms.date: 03/16/2021
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 8b5db7861a3488fb71e709d17679e8d54911b0c9
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110069177"
---
# <a name="configuring-jmx-metrics"></a>配置 JMX 指标

Application Insights Java 3.x 默认会收集一些 JMX 指标，但在许多情况下并不充分。 本文档详细介绍了 JMX 配置选项。

## <a name="how-do-i-collect-additional-jmx-metrics"></a>如何收集其他 JMX 指标？

可以向 applicationinsights.json 文件中添加 ```"jmxMetrics"``` 部分，以配置 JMX 指标收集。 可以按照希望在 Azure 门户中 Application Insights 资源中显示的方式来指定指标名称。 必须为每个要收集的指标定义对象名称和属性。

## <a name="how-do-i-know-what-metrics-are-available-to-configure"></a>如何知道哪些指标可以配置？

说得对 - 必须知道对象名称和属性，对于不同的库、框架和应用程序服务器，这些属性各有区别，并且通常得不到良好记录。 若要获取对象名称和属性，需要查看 MBean 树。 MBean 是一种托管的 Java 对象，可以表示设备、应用程序或资源，并具有一组属性。 

若要查看并浏览可用指标，建议使用 [Java Mission Control](https://www.oracle.com/java/technologies/jdk-mission-control.html)。

### <a name="how-to-navigate-the-java-mission-control-to-get-to-the-right-metrics"></a>如何导航 Java Mission Control 以获得正确的指标？

当运行 Java Mission Control 工具时，可以从左侧选择可用的 JVM，单击“JVM 浏览器”选项卡下的相关进程。等待 JMC 加载进程的仪表板后，选择底部的“MBean 浏览器”选项卡（见下图）。 JMC 必须与 JVM 位于同一文件夹中，且进程/应用必须正常运行。

![JMC MBean 浏览器的屏幕截图](media/java-ipa/jmx/jmc-mbean-browser.png)

### <a name="how-to-get-to-the-metrics-i-want-and-the-necessary-attributes"></a>如何获取所需的指标以及必要的属性？

MBean 浏览器会打开 MBean 树，其中包含了可展开的类别列表。 从左侧选择一个类别，将在右侧打开属性列表。 下图是指标、其对象名称和属性的示例。 属性可能是嵌套的，如以下示例中所示。

![JMC MBean 树的屏幕截图](media/java-ipa/jmx/jmc-metric-sample.png)

### <a name="configuration-example"></a>配置示例

根据上图所示的选择，配置一些指标。 第一个是嵌套指标示例 - `LastGcInfo`，其具有多个属性，想要捕获 `GcThreadCount`。

```json
"jmxMetrics": [
      {
        "name": "Demo - GC Thread Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "LastGcInfo.GcThreadCount"
      },
      {
        "name": "Demo - GC Collection Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "CollectionCount"
      },
      {
        "name": "Demo - Thread Count",
        "objectName": "java.lang:type=Threading",
        "attribute": "ThreadCount"
      }
],
```

### <a name="types-of-collected-metrics-and-available-configuration-options"></a>已收集指标的类型和可用的配置选项？

我们支持数值和布尔型 JMX 指标，其他类型不受支持，将被忽略。 

当前不支持通配符和聚合属性，所以必须单独配置每个属性的“对象名称”/“属性”对。 


## <a name="where-do-i-find-the-jmx-metrics-in-application-insights"></a>在哪里能找到 Application Insights 中的 JMX 指标？

当应用程序正常运行并收集到 JMX 指标时，可以转到 Azure 门户进行查看，并导航到 Application Insights 资源。 在“指标”选项卡下，按如下所示选择下拉菜单以查看指标。

![门户中指标的屏幕截图](media/java-ipa/jmx/jmx-portal.png)
