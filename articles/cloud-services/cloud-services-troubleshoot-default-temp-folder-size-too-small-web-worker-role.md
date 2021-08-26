---
title: 角色的默认 TEMP 文件夹大小太小 | Microsoft Docs
description: 云服务角色的 TEMP 文件夹的空间有限。 本文针对如何避免磁盘空间不足的问题提供了一些建议。
ms.topic: troubleshooting
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 0d61e2effe690e7ba506a0f6a913934bef3c032f
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824765"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-classic-webworker-role"></a>云服务（经典）Web 角色/辅助角色的默认 TEMP 文件夹大小太小


[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

云服务辅助角色或 Web 角色的默认临时目录的最大大小为 100 MB，该目录可能会在某个时候被填满。 本文介绍如何避免临时目录空间不足的问题。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>为什么空间会不足？
标准 Windows 环境变量 TEMP 和 TMP 可供应用程序中运行的代码使用。 TEMP 和 TMP 都指向一个最大大小为 100 MB 的目录。 此目录中存储的任何数据都不会在云服务的整个生命周期中持久保存；如果云服务中的角色实例被回收，则会清除此目录。

## <a name="suggestion-to-fix-the-problem"></a>解决此问题的建议
实现以下备选方案之一：

* 配置本地存储资源，直接对其进行访问而不使用 TEMP 或 TMP。 若要通过应用程序内运行的代码访问本地存储资源，请调用 [RoleEnvironment.GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) 方法。
* 配置本地存储资源，将 TEMP 和 TMP 目录指向本地存储资源的路径。 应在 [RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) 方法中进行这种修改。

下面的代码示例演示了如何在 OnStart 方法中修改 TEMP 和 TMP 的目标目录：

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>后续步骤
阅读说明 [如何增加 Azure Web 角色 ASP.NET 临时文件夹大小](/archive/blogs/kwill/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder)的博客。

查看更多针对云服务的 [故障排除文章](/visualstudio/azure/vs-azure-tools-debugging-cloud-services-overview) 。

若要了解如何使用 Azure PaaS 计算机诊断数据对云服务角色问题进行故障排除，请查看 [Kevin Williamson 博客系列](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)。