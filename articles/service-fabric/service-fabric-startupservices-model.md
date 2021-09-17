---
title: 在 StartupServices.xml 中为 Service Fabric 应用程序定义服务配置
description: 了解如何使用 StartupServices.xml 从 ApplicationManifest.xml 中隔离服务级别配置。
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: 69ec795bab910f8f2b030ab5758698d3fdbae824
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681375"
---
# <a name="introducing-startupservicesxml-in-service-fabric-application"></a>在 Service Fabric 应用程序中引入 StartupServices.xml
此功能在 Service Fabric 应用程序设计中引入了 StartupServices.xml 文件。 此文件承载 ApplicationManifest.xml 的 DefaultServices 节。 使用此实现时，DefaultServices 和服务定义相关的参数将从现有 ApplicationManifest.xml 移动到这个名为 StartupServices.xml 的新文件。 此文件用在 Visual Studio 的每个功能（生成/重新生成/F5/Ctrl+F5/发布）中。

注 - StartupServices.xml 仅适用于 Visual Studio 部署，这种安排是为了确保使用 Visual Studio（使用 StartupServices.xml）部署的包不会与 ARM 部署的服务发生冲突。 StartupServices.xml 未打包为应用程序包的一部分。 DevOps 管道不支持 StartupServices.xml，客户应通过 ARM 或通过 cmdlet 使用所需配置在应用程序中部署单个服务。

## <a name="existing-service-fabric-application-design"></a>现有的 Service Fabric 应用程序设计
对于每个 Service Fabric 应用程序，ApplicationManifest.xml 是应用程序的所有服务相关信息的源。 ApplicationManifest.xml 包含所有 Parameters、ServiceManifestImport 和 DefaultServices。 将在 ApplicationParameters 下的 Cloud.xml/Local1Node.xml/Local5Node.xml 文件中提到配置参数。

在应用程序中添加新服务后，对于此新服务，将在 ApplicationManifest.xml 中添加 Parameters、ServiceManifestImport 和 DefaultServices 节。 将在 ApplicationParameters 下的 Cloud.xml/Local1Node.xml/Local5Node.xml 文件中添加配置参数。

当用户在 Visual Studio 中单击“生成/重新生成”功能时，ServiceManifestImport、Parameters 和 DefaultServices 节的修改将在 ApplicationManifest.xml 中将发生。 还会在 ApplicationParameters 下的 Cloud.xml/Local1Node.xml/Local5Node.xml 文件中编辑配置参数。

当用户触发 F5/Ctrl+F5/发布时，将根据 ApplictionManifest.xml 中的信息部署或发布应用程序和服务。  将使用 ApplicationParameters 下的任何 Cloud.xml/Local1Node.xml/Local5Node.xml 文件中的配置参数。

![Service Fabric 应用程序的现有设计][exisiting-design-diagram]

ApplicationManifest.xml 示例 

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="SampleAppType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Web1_ASPNETCORE_ENVIRONMENT" DefaultValue="" />
    <Parameter Name="Web1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Web1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Web1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <EnvironmentOverrides CodePackageRef="code">
      <EnvironmentVariable Name="ASPNETCORE_ENVIRONMENT" Value="[Web1_ASPNETCORE_ENVIRONMENT]" />
    </EnvironmentOverrides>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatefulService ServiceTypeName="Web1Type" TargetReplicaSetSize="[Web1_TargetReplicaSetSize]" MinReplicaSetSize="[Web1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Web1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="new-service-fabric-application-design-with-startupservicesxml"></a>使用 StartupServices.xml 的新 Service Fabric 应用程序设计
在此设计中，服务级别信息（例如服务定义和服务参数）与应用程序级别信息（ServiceManifestImport 和 ApplicationParameters）之间有明显的差别。 StartupServices.xml 包含所有服务级别信息，而 ApplicationManifest.xml 包含所有应用程序级别信息。 引入的另一项更改是在 StartupServiceParameters 下添加了 Cloud.xml/Local1Node.xml/Local5Node.xml，其中仅包含服务参数的配置。 ApplicationParameters 下的现有 Cloud.xml/Local1Node.xml/Local5Node.xml 仅包含应用程序级别参数配置。

在应用程序中添加新服务后，将在 ApplicationManifest.xml 中添加应用程序级别参数和 ServiceManifestImport。 将在 ApplicationParameters 下的 Cloud.xml/Local1Node.xml/Local5Node.xml 文件中添加应用程序参数的配置。 将在 StartupServiceParameters 下的 StartupServices.xml 中添加服务信息和服务参数，并在 Cloud.xml/Local1Node.xml/Local5Node.xml 中添加服务参数的配置。

在生成/重新生成项目期间，ServiceManifestImport 和应用程序参数的修改将在 ApplicationManifest.xml 中发生。 还会在 ApplicationParameters 下的 Cloud.xml/Local1Node.xml/Local5Node.xml 文件中编辑应用程序参数的配置。 将在 StartupServiceParameters 下的 StartupServices.xml 中编辑服务相关的信息，并在 Cloud.xml/Local1Node.xml/Local5Node.xml 中编辑服务参数。

在 Visual Studio 中触发 F5/Ctrl+F5/发布时，将会根据 ApplictionManifest.xml 中的信息以及 ApplicationParameters 下的任何 Cloud.xml/Local1Node.xml/Local5Node.xml 文件中的应用程序参数部署/发布应用程序。 每个服务将根据 StartupServices.xml 中的服务信息以及 StartupServiceParameters 下的任何 Cloud.xml/Local1Node.xml/Local5Node.xml 文件中的服务参数配置单独启动。

![使用 StartupServices.xml 的新 Service Fabric 应用程序设计][new-design-diagram]

在发布应用程序（单击右键并选择“发布”）之前，可按图中所示编辑这些服务参数和应用程序参数。

![新设计中的“发布”选项][publish-application]

新设计中的示例 ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="SampleAppType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Web1_ASPNETCORE_ENVIRONMENT" DefaultValue="" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <EnvironmentOverrides CodePackageRef="code">
      <EnvironmentVariable Name="ASPNETCORE_ENVIRONMENT" Value="[Web1_ASPNETCORE_ENVIRONMENT]" />
    </EnvironmentOverrides>
  </ServiceManifestImport>
</ApplicationManifest>
```

StartupServices.xml 文件示例
```xml
<?xml version="1.0" encoding="utf-8"?>
<StartupServicesManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                         xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="Web1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <Services>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Web1" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </Services>
</StartupServicesManifest>
```

在 SDK 5.0.516.9590 和更高版本中，将为所有新项目启用 startupServices.xml 功能。 对于执行组件服务，Microsoft.ServiceFabric.Actors NuGet 5.0.516 和更高版本中将启用此功能。 使用旧版 SDK 创建的项目可完全与最新 SDK 后向兼容。 不支持将旧项目迁移到新设计。 如果用户想要在较新版本的 SDK 中创建不使用 StartupServices.xml 的 Service Fabric 应用程序，则应如下图所示单击“帮助我选择项目模板”链接。

![新设计中的“创建新应用程序”选项][create-new-project]


## <a name="next-steps"></a>后续步骤
- 了解 [Service Fabric 应用程序模型](service-fabric-application-model.md)。
- 了解 [Service Fabric 应用程序和服务清单](service-fabric-application-and-service-manifests.md)。

<!--Image references-->
[exisiting-design-diagram]: ./media/service-fabric-startupservices/existing-design.png
[new-design-diagram]: ./media/service-fabric-startupservices/new-design.png
[publish-application]: ./media/service-fabric-startupservices/publish-application.png
[create-new-project]: ./media/service-fabric-startupservices/create-new-project.png

