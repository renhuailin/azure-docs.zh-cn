---
title: 如何为开发人员门户提供小组件
titleSuffix: Azure API Management
description: 了解在向 API Management 开发人员门户存储库提供小组件时应遵循的建议准则。
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: ae491880dc7dc69a3930e6c967fb7a660db75f38
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631469"
---
# <a name="how-to-contribute-widgets-to-the-api-management-developer-portal"></a>如何向 API Management 开发人员门户提供小组件

如果要向 API Management 开发人员门户 [GitHub 存储库](https://github.com/Azure/api-management-developer-portal)提供小组件，请执行以下三步流程：

1. 创建存储库分支。

1. 实现小组件。

1. 打开拉取请求，将小组件包含在官方存储库中。

你的小组件将继承存储库的许可证。 它将在自托管版本门户的[选择加入安装](developer-portal-use-community-widgets.md)中提供。 开发人员门户团队还可以决定将其包含在门户的托管版本中。

有关如何开发自己的小组件的示例，请参阅[小组件实现](developer-portal-implement-widgets.md)教程。

## <a name="contribution-guidelines"></a>贡献准则

本指南旨在确保客户及其门户访问者的安全和隐私。 请遵循以下准则以确保你的贡献被接受：

1. 将小组件置于 `community/widgets/<your-widget-name>` 文件夹中。

1. 小组件的名称必须为小写和字母数字，并用短划线分隔字词。 例如，`my-new-widget`。

1. 文件夹必须包含小组件在已发布的门户中的屏幕截图。

1. 文件夹必须包含 `readme.md` 文件，该文件应遵循 `/scaffolds/widget/readme.md` 文件中的模板。

1. 文件夹可以包含一个 `npm_dependencies` 文件，其中包含用于安装或管理小组件依赖项的 npm 命令。

    显式指定每个依赖项的版本。 例如：  

    ```console
    npm install azure-storage@2.10.3 axios@0.19.1
    ```

    小组件需要的依赖项应该最少。 审阅者将仔细检查每个依赖项。 特别是，小组件的核心逻辑在小组件文件夹中应该是开放源代码。 请勿将其包装在 npm 包中。

1. 小组件的文件夹之外的任何文件的更改都不允许作为小组件贡献的一部分。 这包括但不限于 `/package.json` 文件。

1. 不允许插入跟踪脚本或将客户编写的数据发送到自定义服务。

    > [!NOTE]
    > 你只能通过 `Logger` 接口收集客户编写的数据。

## <a name="next-steps"></a>后续步骤

- 有关贡献的详细信息，请参阅 API Management 开发人员门户 [GitHub 存储库](https://github.com/Azure/api-management-developer-portal/)。

- 请参阅[实现小组件](developer-portal-implement-widgets.md)，了解如何逐步开发自己的小组件。

- 请参阅[使用社区小组件](developer-portal-use-community-widgets.md)，了解如何使用社区提供的小组件。