---
title: 在开发人员门户中使用社区小组件
titleSuffix: Azure API Management
description: 了解 API Management 开发人员门户的社区小组件，以及如何在代码中注入和使用它们。
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 3fe751483ea22ff3464cc1fd505b313a5a48ade7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656315"
---
# <a name="use-community-widgets-in-the-developer-portal"></a>在开发人员门户中使用社区小组件

所有开发人员将其社区提供的小组件放置在 API Management 开发人员门户 [GitHub 存储库](https://github.com/Azure/api-management-developer-portal)的 `/community/widgets/` 文件夹中。 开发人员门户团队已接受所有小组件。 你可以通过将小组件注入门户的[自承载版本](developer-portal-self-host.md)中来使用小组件。 开发人员门户的托管版本当前不支持社区小组件。

> [!NOTE]
> 开发人员门户团队已全面检查所提供的小组件及其依赖项。 但是，团队无法保证加载小组件是安全的。 决定使用社区提供的小组件时，请自行判断。 若要了解预防措施，请参阅[小组件贡献准则](developer-portal-widget-contribution-guidelines.md#contribution-guidelines)。

## <a name="inject-and-use-external-widgets"></a>注入和使用外部小组件

1. 为最新版本的开发人员门户设置[本地环境](developer-portal-self-host.md#step-1-set-up-local-environment)。

1. 转到 `/community/widgets` 目录中的小组件文件夹。 阅读 `readme.md` 文件中的小组件说明。

1. 在门户的模块中注册小组件：

    1. `src/apim.design.module.ts` - 用于注册设计时依赖项的模块。
    
        ```typescript
        import { WidgetNameDesignModule } from "../community/widgets/<widget-name>/widget.design.module";
    
        ...
    
            injector.bindModule(new WidgetNameDesignModule());
        ```
    
    1. `src/apim.publish.module.ts` - 用于注册发布时依赖项的模块。
    
        ```typescript
        import { WidgetNamePublishModule } from "../community/widgets/<widget-name>/widget.publish.module";
    
        ...
    
            injector.bindModule(new WidgetNamePublishModule());
        ```
    
    1. `src/apim.runtime.module.ts` - 用于注册运行时依赖项的模块。
    
        ```typescript
        import { WidgetNameRuntimeModule } from "../community/widgets/<widget-name>/widget.runtime.module";
    
        ...
    
            injector.bindModule(new WidgetNameRuntimeModule());
        ```

1. 查看小组件是否包含 `npm_dependencies` 文件。

1. 如果是，请复制该文件中的命令，并在存储库的顶层目录中运行这些命令。

    这样做会安装小组件的依赖项。

1. 运行 `npm start`。

你可以在小组件选择器中看到“社区”类别中的小组件。

:::image type="content" source="media/developer-portal-use-community-widgets/widget-selector.png" alt-text="小组件选择器的屏幕截图":::


## <a name="next-steps"></a>后续步骤


详细了解开发人员门户：

- [Azure API 管理开发人员门户概述](api-management-howto-developer-portal.md)

- [提供小组件](developer-portal-widget-contribution-guidelines.md)
