---
title: 使用备份前和备份后脚本
description: 本文包含指定备份前和备份后脚本的过程。 Azure 备份服务器 (MABS)。
ms.topic: conceptual
ms.date: 07/06/2021
ms.openlocfilehash: 48d65679f4ff1b6486513067058c1f385e4f1434
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113568584"
---
# <a name="using-pre-backup-and-post-backup-scripts"></a>使用备份前和备份后脚本

适用于：Microsoft Azure 备份服务器 (MABS)

备份前脚本是位于受保护计算机上的一个脚本，在每个 MABS 备份作业之前执行，并为备份准备受保护的数据源。

备份后脚本是在 MABS 备份作业之后运行以执行任何备份后处理（例如使虚拟机重新联机）的脚本。

在计算机上安装保护代理时，会将一个 ScriptingConfig.xml 文件添加到受保护计算机上的安装路径\Microsoft Data Protection Manager\DPM\Scripting 文件夹中。 对于计算机上每个受保护的数据源，你可以在 ScriptingConfig.xml 中指定备份前脚本和备份后脚本。

>[!Note]
>备份前脚本和备份后脚本不能是 VBScript。 相反，你必须围绕包含 cscript myscript.vbs 的脚本使用包装命令。

当 MABS 运行保护作业时，将检查受保护计算机上的 ScriptingConfig.xml。 如果指定了备份前脚本，MABS 会先运行脚本，之后再完成作业。 如果指定了备份后脚本，MABS 会先完成作业，之后再运行脚本。

>[!Note]
>保护作业包括副本创建、快速完整备份、同步以及一致性检查。

MABS 通过使用本地系统帐户运行备份前和备份后脚本。 你最好应确保只有管理员和本地系统帐户才有脚本的读取和执行权限。 此级别的权限会阻止未经授权的用户修改这些脚本。

**ScriptingConfig.xml**

```
<?xml version="1.0" encoding="utf-8"?>
<ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
xmlns:xsd="http://www.w3.org/2001/XMLSchema" 
xmlns="http://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
   <DatasourceScriptConfig DataSourceName="Data source">
     <PreBackupScript>”Path\Script Parameters” </PreBackupScript>
     <PostBackupScript>"Path\Script Parameters” </PostBackupScript>
     <TimeOut>30</TimeOut>
   </DatasourceScriptConfig>
</ScriptConfiguration>
```

指定备份前和备份后脚本

1. 在受保护计算机上，使用 XML 或文本编辑器打开 ScriptingConfig.xml 文件。

   >[!Note]
   >DataSourceName 属性必须以“驱动器:”的形式提供（例如，如果数据源位于 D 驱动器上，则为“D:”）。

1. 对于每个数据源，按如下方式完成 DatasourceScriptConfig 元素：


   1. 对于 DataSourceName 属性，输入数据源卷（适用于文件数据源）或名称（适用于所有其他数据源）。 应用程序数据的数据源名称应采用以下格式：_实例\数据库_（适用于 SQL）、_存储组名称_（适用于 Exchange）、_逻辑路径\组件名称_（适用于 Virtual Server）以及 _SharePoint 场\SQL Server 名称\SQL 实例名称\SharePoint 配置 DB_（适用于 Windows SharePoint Services）。
   1. 在 PreBackupScript 标记中，输入路径和脚本名称。
   1. 在 PreBackupCommandLine 标记中，输入要传递到脚本的命令行参数（由空格分隔）。
   1. 在 PostBackupScript 标记中，输入路径和脚本名称。
   1. 在 PostBackupCommandLine 标记中，输入要传递到脚本的命令行参数（由空格分隔）。
   1. 在 TimeOut 标记中，输入 MABS 在调用脚本后应该等待的时间（以分钟为单位），之后会超时并将脚本标记为失败。

1. 保存 ScriptingConfig.xml 文件。

>[!Note]
>MABS 会在备份后脚本命令中另外添加一个布尔值 (true/false) 参数作为后缀，指明 MABS 备份作业的状态。
