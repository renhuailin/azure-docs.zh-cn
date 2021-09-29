---
title: 在开发人员门户中实现小组件
titleSuffix: Azure API Management
description: 了解如何实现从外部 API 使用数据并将其显示在 API 管理开发人员门户中的小组件。
author: dlepow
ms.author: danlep
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: af2ac28df5b41606d9199d6fa36c3f683e86c59d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639900"
---
# <a name="implement-widgets-in-the-developer-portal"></a>在开发人员门户中实现小组件

在本教程中，你会实现从外部 API 使用数据并将其显示在 API 管理开发人员门户中的小组件。

小组件会从示例[会议 API](https://conferenceapi.azurewebsites.net/?format=json) 检索会话说明。 会话标识符会通过指定小组件编辑器进行设置。

为了在开发过程中帮助你，请参阅位于 API 管理开发人员门户 [GitHub 存储库](https://github.com/Azure/api-management-developer-portal/)的 `examples` 文件夹中的已完成小组件：`/examples/widgets/conference-session`。

:::image type="content" source="media/developer-portal-implement-widgets/widget-published.png" alt-text="已发布小组件的屏幕截图":::

## <a name="prerequisites"></a>必备条件

* 为最新版本的开发人员门户设置[本地环境](developer-portal-self-host.md#step-1-set-up-local-environment)。

* 应了解 [Paperbits 小组件剖析](https://paperbits.io/wiki/widget-anatomy)。


## <a name="copy-the-scaffold"></a>复制基架

使用 `/scaffolds` 文件夹中的 `widget` 基架作为起点来构建新的小组件。

1. 将文件夹 `/scaffolds/widget` 复制到 `/community/widgets`。
1. 将该文件夹重命名为 `conference-session`。

## <a name="rename-exported-module-classes"></a>重命名导出的模块类

通过在这些文件中将 `Widget` 前缀替换为 `ConferenceSession`，来重命名导出的模块类：

- `widget.design.module.ts`

- `widget.publish.module.ts`

- `widget.runtime.module.ts`
    
例如，在 `widget.design.module.ts` 文件夹，将 `WidgetDesignModule` 更改为 `ConferenceSessionDesignModule`：
    
```typescript
export class WidgetDesignModule implements IInjectorModule {
```
to 
    
```typescript
export class ConferenceSessionDesignModule implements IInjectorModule {
```
    
   
## <a name="register-the-widget"></a>注册小组件

通过在相应文件中添加以下行，在门户的根模块中注册小组件的模块：

1. `src/apim.design.module.ts` - 用于注册设计时依赖项的模块。

   ```typescript
   import { ConferenceSessionDesignModule } from "../community/widgets/conference-session/widget.design.module";
   
   ...
   injector.bindModule(new ConferenceSessionDesignModule());
   ```
1. `src/apim.publish.module.ts` - 用于注册发布时依赖项的模块。

   ```typescript
   import { ConferenceSessionPublishModule } from "../community/widgets/conference-session/widget.publish.module";

    ...

    injector.bindModule(new ConferenceSessionPublishModule());
    ```

1. `src/apim.runtime.module.ts` - 运行时依赖项。

    ```typescript
    import { ConferenceSessionRuntimeModule } from "../community/widgets/conference-session/widget.runtime.module";

    ...

    injector.bindModule(new ConferenceSessionRuntimeModule());
    ```

## <a name="place-the-widget-in-the-portal"></a>将小组件置于门户中

现在，你已准备好在开发人员门户中插入复制的基架并使用它。

1. 运行 `npm start` 命令。

1. 当应用程序加载时，将新的小组件置于页面上。 可以在小组件选择器的 `Community` 类别中的名称 `Your widget` 下找到它。

    :::image type="content" source="media/developer-portal-implement-widgets/widget-selector.png" alt-text="小组件选择器的屏幕截图":::

1. 通过按 Ctrl+S（或是在 macOS 上为 ⌘+S）来保存页面。

    > [!NOTE]
    > 在设计时，仍可以通过按住 Ctrl（或 ⌘）键来与网站交互。

## <a name="add-custom-properties"></a>添加自定义属性

若要使小组件提取会话说明，它需要知道会话标识符。 将 `Session ID` 属性添加到相应的接口和类：

若要使小组件提取会话说明，它需要知道会话标识符。 将会话 ID 属性添加到相应的接口和类：

1. `widgetContract.ts` - 数据协定（数据层），定义如何保持小组件配置。

    ```typescript
    export interface WidgetContract extends Contract {
        sessionNumber: string;
    }
    ```

1. `widgetModel.ts` - 模型（业务层）- 系统中小组件的主要表示形式。 它由编辑器进行更新，由表示层进行呈现。

    ```typescript
    export class WidgetModel {
        public sessionNumber: string;
    }
    ```

1. `ko/widgetViewModel.ts` - 视图模型（表示层）- 开发人员门户使用 HTML 模板呈现的特定于 UI 框架的对象。

    > [!NOTE]
    > 无需更改此文件中的任何内容。

## <a name="configure-binders"></a>配置绑定器

启用 `sessionNumber` 从数据源流到小组件表示。 编辑 `ModelBinder` 和 `ViewModelBinder` 实体：

1. `widgetModelBinder.ts` 可帮助使用协定中所述的数据准备模型。

    ```typescript
    export class WidgetModelBinder implements IModelBinder<WidgetModel> {
        public async contractToModel(contract: WidgetContract): Promise<WidgetModel> {
            model.sessionNumber = contract.sessionNumber || "107"; // 107 is the default session id
            ...
        }
    
        public modelToContract(model: WidgetModel): Contract {
            const contract: WidgetContract = {
                sessionNumber: model.sessionNumber
                ...
            };
            ...
        }
    }
    ```

1. `ko/widgetViewModelBinder.ts` 知道开发人员门户需要如何在特定 UI 框架中提供模型（以视图模型的形式）。

    ```typescript
    ...
    public async updateViewModel(model: WidgetModel, viewModel: WidgetViewModel): Promise<void> {
            viewModel.runtimeConfig(JSON.stringify({
                sessionNumber: model.sessionNumber
            }));
        }
    }
    ...
    ```

## <a name="adjust-design-time-widget-template"></a>调整设计时小组件模板

每个范围的组件独立运行。 它们具有单独的依赖项注入容器、自己的配置、生命周期等。它们甚至可能由不同的 UI 框架提供支持（在此示例中为 Knockout JS）。

从设计时角度来看，任何运行时组件都只是具有特定属性和/或内容的 HTML 标记。 如果需要，配置会通过纯文本标记进行传递。 在简单情况下（如此示例），参数在属性中进行传递。 如果配置更加复杂，则可以使用由指定配置提供程序（例如 `ISettingsProvider`）提取的所需设置的标识符。

1. 更新 `ko/widgetView.html` 文件：

    ```html
    <widget-runtime data-bind="attr: { params: runtimeConfig }"></widget-runtime>
    ```

    当开发人员门户在设计时或发布时运行 `attr` 绑定时，生成的 HTML 为 ：

    ```html
    <widget-runtime params="{ sessionNumber: 107 }"></widget-runtime>
    ```

    随后在运行时，`widget-runtime` 组件会读取 `sessionNumber` 并在初始化代码中使用它（请参阅下文）。

1. 更新 `widgetHandlers.ts` 文件，以便在创建时分配会话 ID：

    ```typescript
    ...
    createModel: async () => {
        var model = new ConferenceSessionModel();
        model.sessionNumber = "107";
            return model;
        }
    ...
    ```

## <a name="revise-runtime-view-model"></a>修订运行时视图模型

运行时组件是在网站本身运行的代码。 例如，在 API 管理开发人员门户中，它们是动态组件（例如，API 详细信息、API 控制台）幕后的所有脚本，处理各种操作（如代码示例生成、发送请求等） 。

运行时组件的视图模型需要具有以下方法和属性：

- `sessionNumber` 属性（使用 `Param` 修饰器进行标记），用作从外部传递的组件输入参数（设计时生成的标记；请参阅上一步）。
- `sessionDescription` 属性，绑定到小组件模板（请参阅本文后面的 `widget-runtime.html`）。
- `initialize` 方法（具有 `OnMounted` 修饰器），在创建小组件并且分配其所有参数之后调用。 这是读取 `sessionNumber` 并使用 `HttpClient` 调用 API 的好位置。 `HttpClient` 是由 IoC（控制反转）容器注入的依赖项。

- 首先，开发人员门户会创建小组件并分配其所有参数。 随后它会调用 `initialize` 方法。

    ```typescript
    ...
    import * as ko from "knockout";
    import { Component, RuntimeComponent, OnMounted, OnDestroyed, Param } from "@paperbits/common/ko/decorators";
    import { HttpClient, HttpRequest } from "@paperbits/common/http";
    ...

    export class WidgetRuntime {
        public readonly sessionDescription: ko.Observable<string>;

        constructor(private readonly httpClient: HttpClient) {
            ...
            this.sessionNumber = ko.observable();
            this.sessionDescription = ko.observable();
            ...
        }

        @Param()
        public readonly sessionNumber: ko.Observable<string>;

        @OnMounted()
        public async initialize(): Promise<void> {
            ...
            const sessionNumber = this.sessionNumber();

            const request: HttpRequest = {
                url: `https://conferenceapi.azurewebsites.net/session/${sessionNumber}`,
                method: "GET"
            };

            const response = await this.httpClient.send<string>(request);
            const sessionDescription = response.toText();
    
            this.sessionDescription(sessionDescription);
            ...
        }
        ...
    }
    ```

## <a name="tweak-the-widget-template"></a>调整小组件模板

更新小组件以显示会话说明。

在 `ko/runtime/widget-runtime.html` 文件中使用段落标记和 `markdown`（或 `text`）绑定来呈现说明：

```html
<p data-bind="markdown: sessionDescription"></p>
```

## <a name="add-the-widget-editor"></a>添加小组件编辑器

小组件现已配置为提取 `107` 会话的说明。 你在代码中将 `107` 指定为默认会话。 若要检查是否正确执行了所有操作，请运行 `npm start` 并确认开发人员门户在页面上显示说明。

现在执行以下步骤，使用户可以通过小组件编辑器设置会话 ID：

1. 更新 `ko/widgetEditorViewModel.ts` 文件：

    ```typescript
    export class WidgetEditor implements WidgetEditor<WidgetModel> {
        public readonly sessionNumber: ko.Observable<string>;

        constructor() {
            this.sessionNumber = ko.observable();
        }

        @Param()
        public model: WidgetModel;

        @Event()
        public onChange: (model: WidgetModel) => void;

        @OnMounted()
        public async initialize(): Promise<void> {
            this.sessionNumber(this.model.sessionNumber);
            this.sessionNumber.subscribe(this.applyChanges);
        }

        private applyChanges(): void {
            this.model.sessionNumber = this.sessionNumber();
            this.onChange(this.model);
        }
    }
    ```

    编辑器视图模型使用的方法与之前查看的方法相同，但有一个新属性 `onChange`，它使用 `@Event()` 进行修饰。 它会连接回调以向侦听器（在本例中为内容编辑器）通知模型的更改。

1. 更新 `ko/widgetEditorView.html` 文件：

    ```html
    <input type="text" class="form-control" data-bind="textInput: sessionNumber" />
    ```

1. 再次运行 `npm start`。 你应该能够在小组件编辑器中更改 `sessionNumber`。 将 ID 更改为 `108`，保存更改，然后刷新浏览器的选项卡。如果遇到问题，则可能需要再次将小组件添加到页面上。

    :::image type="content" source="media/developer-portal-implement-widgets/widget-editor.png" alt-text="小组件编辑器的屏幕截图":::

## <a name="rename-the-widget"></a>重命名小组件

在 `constants.ts` 文件中更改小组件名称：

```typescript
...
export const widgetName = "conference-session";
export const widgetDisplayName = "Conference session";
...
```

> [!NOTE]
> 如果将小组件贡献到存储库，则 `widgetName` 需要与其文件夹名称相同，并且需要从显示名称派生（小写和空格替换为短划线）。 类别应保留 `Community`。

## <a name="next-steps"></a>后续步骤


详细了解开发人员门户：

- [Azure API 管理开发人员门户概述](api-management-howto-developer-portal.md)

- [贡献小组件](developer-portal-widget-contribution-guidelines.md) - 我们欢迎并鼓励社区贡献。

- 请参阅[使用社区小组件](developer-portal-use-community-widgets.md)，了解如何使用社区提供的小组件。
