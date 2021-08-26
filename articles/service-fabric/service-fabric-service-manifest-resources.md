---
title: 指定 Service Fabric 服务终结点
description: 如何在服务清单中描述终结点资源，包括如何设置 HTTPS 终结点
ms.topic: conceptual
ms.date: 09/16/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 66474543834f97c409d7572d54c7850ddc57faa0
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778645"
---
# <a name="specify-resources-in-a-service-manifest"></a>在服务清单中指定资源
## <a name="overview"></a>概述
Service Fabric 应用程序和服务使用清单文件进行定义和版本控制。 有关 ServiceManifest.xml 和 ApplicationManifest.xml 的概括程度更高的概述，请参阅 [Service Fabric 应用程序和服务清单](service-fabric-application-and-service-manifests.md)。

服务清单允许声明或更改服务要使用的资源而无需更改已编译的代码。 Service Fabric 支持对服务的终结点资源进行配置。 可以通过应用程序清单中的 SecurityGroup 控制对服务清单中指定资源的访问。 资源的声明允许在部署时更改这些资源，这意味着服务不需要引入新的配置机制。 ServiceManifest.xml 文件的架构定义将随 Service Fabric SDK 和工具一起安装到 C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd，并记录到 [ServiceFabricServiceModel.xsd 架构文档](service-fabric-service-model-schema.md)中。

## <a name="endpoints"></a>终结点
在服务清单中定义了终结点资源时，如果未显式指定端口，则 Service Fabric 从保留的应用程序端口范围中分配端口。 例如，可以查看本段落后面提供的清单代码段中指定的终结点 *ServiceEndpoint1* 。 此外，服务还可以请求在资源中使用特定端口。 在不同群集节点上运行的服务副本可以分配不同的端口号，而运行在同一节点上的服务副本共享同一个端口。 之后服务副本可根据需要将这些端口用于复制和侦听客户端请求。

激活一项可指定 https 终结点的服务后，Service Fabric 会设置端口的访问控制项，将指定的服务器证书绑定到端口，还会向证书的私钥授予该服务作为权限运行的标识。 每次启动 Service Fabric 时，或通过升级更改应用程序的证书声明时，都会调用激活流。 还会监视终结点证书的更改/续订，并根据需要定期重新应用权限。

服务终止后，Service Fabric 会清理终结点访问控制条目，并删除证书绑定。 但是，任何应用于证书私钥的权限都不会被清除。

> [!WARNING] 
> 根据设计，静态端口不应与 ClusterManifest 中指定的应用程序端口范围重叠。 如果指定静态端口，请将其分配到应用程序端口范围外，否则会导致端口冲突。 对于版本 6.5CU2，当我们检测到此类冲突时，我们将发出 **运行状况警告**，但让部署继续与已发布的 6.5 行为同步。 但是，我们可能会在下一个主要版本中阻止应用程序部署。
>
> 在版本 7.0 中，当我们检测到应用程序端口范围使用率超过 HostingConfig::ApplicationPortExhaustThresholdPercentage（默认 80%）时，我们将发出 **运行状况警告**。
>

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

如果在单个服务包中有多个代码包，则还需要在“终结点”部分中引用代码包。  例如，如果 **ServiceEndpoint2a** 和 **ServiceEndpoint2b** 是同一个服务包中引用不同代码包的终结点，则对应于每个终结点的代码包按如下所示说明：

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint2a" Protocol="http" Port="802" CodePackageRef="Code1"/>
    <Endpoint Name="ServiceEndpoint2b" Protocol="http" Port="801" CodePackageRef="Code2"/>
  </Endpoints>
</Resources>
```

请参阅[配置有状态 Reliable Services](service-fabric-reliable-services-configuration.md)以了解有关从配置包设置文件 (settings.xml) 引用终结点的更多信息。

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>示例：为服务指定 HTTP 终结点
以下服务清单在 &lt;Resources&gt; 元素中定义了 1 个 TCP 终结点资源和 2 个 HTTP 终结点资源。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
      <Endpoint Name="ServiceEndpoint4" Protocol="https" Port="14023"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>示例：指定用于服务的 HTTPS 终结点
HTTPS 协议提供服务器身份验证，用于对客户端-服务器通信进行加密。 若要在 Service Fabric 服务上启用 HTTPS，请在服务清单的“*资源 -> 终结点 -> 终结点*”部分中指定该协议，如前面针对终结点 *ServiceEndpoint3* 的操作所示。

> [!NOTE]
> 在应用程序升级期间不能更改服务的协议。 如果在升级期间进行了更改，那会是一项重大的更改。
> 

> [!WARNING] 
> 使用 HTTPS 时，请勿将同一端口和证书用于已部署到同一节点的不同服务实例（独立于应用程序）。 在不同的应用程序实例中使用相同的端口升级两个不同的服务将导致升级失败。 有关详细信息，请参阅[使用 HTTPS 终结点升级多个应用程序](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints)。
>

下面是演示 HTTPS 终结点所需配置的示例 ApplicationManifest。 服务器/终结点证书可以通过指纹或主题公用名声明，且必须提供一个值。 EndpointRef 是对 ServiceManifest 中 EndpointResource 的引用，其协议必须设置为“https”协议。 可以添加多个 EndpointCertificate。  

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint3"/>
      <EndpointBindingPolicy CertificateRef="SslCertByCN" EndpointRef="ServiceEndpoint4"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="SslCertByTP" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
    <EndpointCertificate Name="SslCertByCN" X509FindType="FindBySubjectName" X509FindValue="ServiceFabric-EndpointCertificateBinding-Test" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

对于 Linux 群集，**MY** 存储默认为文件夹 **/var/lib/sfcerts**。

若要通过示例方式了解一个使用 HTTPS 终结点的完整应用程序，请参阅[使用 Kestrel 将 HTTPS 终结点添加到 ASP.NET Core Web API 前端服务](./service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)。

## <a name="port-acling-for-http-endpoints"></a>HTTP 终结点的端口 ACL 操作
Service Fabric 将对默认指定的 HTTP(S) 终结点自动执行 ACL。 如果某个终结点没有关联的 [SecurityAccessPolicy](service-fabric-assign-policy-to-endpoint.md)，并且 Service Fabric 配置为使用具有管理员权限的帐户运行，则它不会执行自动 ACL 操作。

## <a name="overriding-endpoints-in-servicemanifestxml"></a>重写 ServiceManifest.xml 中的终结点

在 ApplicationManifest 中添加一个 ResourceOverrides 节，作为 ConfigOverrides 节的同级。 在本部分中，可以为服务清单中指定的资源部分中的终结点部分指定替代。 运行时 5.7.217/SDK 2.7.217 及更高版本支持替代终结点。

若要使用 ApplicationParameter 替代 ServiceManifest 中的终结点，请更改 ApplicationManifest，如下所示：

在 ServiceManifestImport 部分添加一个新部分“ResourceOverrides”。

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <ResourceOverrides>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" Port="[Port]" Protocol="[Protocol]" Type="[Type]" />
        <Endpoint Name="ServiceEndpoint1" Port="[Port1]" Protocol="[Protocol1] "/>
      </Endpoints>
    </ResourceOverrides>
        <Policies>
           <EndpointBindingPolicy CertificateRef="SslCertByTP" EndpointRef="ServiceEndpoint"/>
        </Policies>
  </ServiceManifestImport>
```

在 Parameters 中添加以下内容：

```xml
  <Parameters>
    <Parameter Name="Port" DefaultValue="" />
    <Parameter Name="Protocol" DefaultValue="" />
    <Parameter Name="Type" DefaultValue="" />
    <Parameter Name="Port1" DefaultValue="" />
    <Parameter Name="Protocol1" DefaultValue="" />
  </Parameters>
```

部署应用程序时，可以传入这些值作为 ApplicationParameter。  例如： 。

```powershell
PS C:\> New-ServiceFabricApplication -ApplicationName fabric:/myapp -ApplicationTypeName "AppType" -ApplicationTypeVersion "1.0.0" -ApplicationParameter @{Port='1001'; Protocol='https'; Type='Input'; Port1='2001'; Protocol='http'}
```

注意：如果为给定 ApplicationParameter 提供的值为空，我们会回到 ServiceManifest 中为对应 EndPointName 提供的默认值。

例如：

如果在指定的 ServiceManifest 中

```xml
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint1" Protocol="tcp"/>
    </Endpoints>
  </Resources>
```

假设应用程序参数的 Port1 和 Protocol1 值为 null 或为空。 端口将由 ServiceFabric 决定，Protocol 将为 tcp。

假设指定了错误值。 假设为“Port”指定了字符串值“Foo”而不是 int。New-ServiceFabricApplication 命令会失败并显示错误：`The override parameter with name 'ServiceEndpoint1' attribute 'Port1' in section 'ResourceOverrides' is invalid. The value specified is 'Foo' and required is 'int'.`

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Service Fabric 的服务清单中定义终结点。 有关更多详细示例，请参阅：

> [!div class="nextstepaction"]
> [应用程序和服务清单示例](service-fabric-manifest-examples.md)

有关在 Service Fabric 群集上打包和部署现有应用程序的演练，请参阅：

> [!div class="nextstepaction"]
> [打包现有可执行文件并将其部署到 Service Fabric](service-fabric-deploy-existing-app.md)
