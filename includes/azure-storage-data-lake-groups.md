---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 9750eabf2aa5af4f431f2db17e113b07d3bce863
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017659"
---
始终将 [Azure AD 安全组](../articles/active-directory/fundamentals/active-directory-manage-groups.md)用作 ACL 条目中分配的主体。 拒绝直接分配各个用户或服务主体。 使用此结构，你可以添加和删除用户或服务主体，不需要向整个目录结构重新应用 ACL。 可以仅在适当的 Azure AD 安全组中添加或删除用户和服务主体。 

有多种不同的方法可用来设置组。 例如，假设你有一个名为 /LogData 的目录，该目录包含服务器生成的日志数据。 Azure 数据工厂 (ADF) 将数据引入到该文件夹中。 服务工程团队中的特定用户将上传日志并管理此文件夹的其他用户，而各个 Databricks 群集将分析来自该文件夹的日志。 

若要启用这些活动，可以创建一个 `LogsWriter` 组和一个 `LogsReader` 组。 然后，可以按如下所示分配权限：

- 将 `LogsWriter` 组添加到 /LogData 目录的具有 `rwx` 权限的 ACL。
- 将 `LogsReader` 组添加到 /LogData 目录的具有 `r-x` 权限的 ACL。
- 向 `LogsWriters` 组添加用于 ADF 的服务主体对象或托管服务标识 (MSI)。
- 将服务工程团队中的用户添加到 `LogsWriter` 组。
- 将 Databricks 的服务主体对象或 MSI 添加到 `LogsReader` 组。

如果服务工程团队中的用户离开了公司，则只需将其从 `LogsWriter` 组中删除即可。 如果未将该用户添加到组中，而是为该用户添加了专用 ACL 条目，则必须从 /LogData 目录中删除此 ACL 条目。 还必须从 /LogData 目录的整个目录层次结构中的所有子目录和文件中删除此条目。 

若要创建组并添加成员，请参阅[使用 Azure Active Directory 创建基本组并添加成员](../articles/active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。