---
title: 使用 Bicep Linter
description: 了解如何使用 Bicep Linter。
ms.topic: conceptual
ms.date: 09/10/2021
ms.openlocfilehash: cef701d9a9f64990c0afbe265c3355f9c1a850ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631165"
---
# <a name="use-bicep-linter"></a>使用 Bicep Linter

Bicep Linter 可用于分析 Bicep 文件。 它使你可以在生成或部署 Bicep 文件之前查找语法错误和违反最佳做法的行为。 你可以自定义一组用于检查文件的创作最佳做法。 Linter 在开发过程中提供指导，可让用户更轻松地强制实施编程标准。

## <a name="linter-requirements"></a>Linter 要求

linter 集成到 Bicep CLI 和 VS Code 扩展中。 要使用它，必须具有 0.4 或更高版本。

## <a name="customize-linter"></a>自定义 Linter

使用 bicepconfig.json 可以启用或禁用 Linter，提供特定于规则的值，以及设置规则级别。 以下示例展示默认 bicepconfig.json：

```json
{
  "analyzers": {
    "core": {
      "verbose": false,
      "enabled": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning",
          "disallowedhosts": [
            "management.core.windows.net",
            "gallery.azure.com",
            "management.core.windows.net",
            "management.azure.com",
            "database.windows.net",
            "core.windows.net",
            "login.microsoftonline.com",
            "graph.windows.net",
            "trafficmanager.net",
            "vault.azure.net",
            "datalake.azure.net",
            "azuredatalakestore.net",
            "azuredatalakeanalytics.net",
            "vault.azure.net",
            "api.loganalytics.io",
            "api.loganalytics.iov1",
            "asazure.windows.net",
            "region.asazure.windows.net",
            "api.loganalytics.iov1",
            "api.loganalytics.io",
            "asazure.windows.net",
            "region.asazure.windows.net",
            "batch.core.windows.net"
          ],
          "excludedhosts": [
            "schema.management.azure.com"
          ]
        }
      }
    }
  }
}
```

可将自定义的 bicepconfig.json 与模板一起放在同一个目录中。 将使用文件夹树中位于上层的最接近配置文件。

以下 JSON 是一个示例 bicepconfig.json：

```json
{
  "analyzers": {
    "core": {
      "enabled": true,
      "verbose": true,
      "rules": {
        "no-hardcoded-env-urls": {
          "level": "warning"
        },
        "no-unused-params": {
          "level": "error"
        },
        "no-unused-vars": {
          "level": "error"
        },
        "prefer-interpolation": {
          "level": "warning"
        },
        "secure-parameter-default": {
          "level": "error"
        },
        "simplify-interpolation": {
          "level": "warning"
        }
      }
    }
  }
}
```

- enabled：输入 true 会启用 Linter，输入 false 会禁用 Linter  。
- verbose：输入 true 会显示 Visual Studio Code 使用的 bicepconfig.json 文件。
- rules：输入特定于规则的值。 每个规则至少有一个属性和级别。 此属性控制 Bicep 的行为。

可将多个值用于规则级别：

| **level**  | **生成时行为** | 编辑器行为 |
|--|--|--|
| `Error` | 冲突在命令行生成输出中显示为“错误”，并导致生成失败。 | 有问题的代码带有红色的波浪下划线，并显示在“问题”选项卡中。 |
| `Warning` | 冲突在命令行生成输出中显示为“警告”，但不会导致生成失败。 | 有问题的代码带有黄色的波浪下划线，并显示在“问题”选项卡中。 |
| `Info` | 冲突不会出现在命令行生成输出中。 | 有问题的代码带有蓝色的波浪下划线，并显示在“问题”选项卡中。 |
| `Off` | 完全禁止显示。 | 完全禁止显示。 |

当前的 Linter 规则集非常精简，取自 [arm-ttk 测试用例](../templates/template-test-cases.md)。 默认情况下，Visual Studio Code 扩展和 Bicep CLI 都会检查所有可用规则，所有规则设置为警告级别。 根据规则级别，编辑器中会显示错误、警告或信息性消息。

- [no-hardcoded-env-urls](./linter-rule-no-hardcoded-environment-urls.md)
- [no-unused-params](./linter-rule-no-unused-parameters.md)
- [no-unused-vars](./linter-rule-no-unused-variables.md)
- [prefer-interpolation](./linter-rule-prefer-interpolation.md)
- [secure-parameter-default](./linter-rule-secure-parameter-default.md)
- [simplify-interpolation](./linter-rule-simplify-interpolation.md)

Visual Studio Code 的 Bicep 扩展提供 Intellisense 用于编辑 Bicep 配置文件：

:::image type="content" source="./media/linter/bicep-linter-configure-intellisense.png" alt-text="用于配置 bicepconfig.json 的 Intellisense 支持。":::

## <a name="use-in-visual-studio-code"></a>在 Visual Studio Code 中使用

安装 Bicep 扩展 0.4 或更高版本以使用 Linter。  以下屏幕截图显示了操作中的 Linter：

:::image type="content" source="./media/linter/bicep-linter-show-errors.png" alt-text="在 Visual Studio Code 中使用 Bicep Linter。":::

在屏幕截图所示的“问题”窗格中，显示了四条错误消息、一条警告消息和一条信息性消息。  信息消息显示了使用的 Bicep 配置文件。 仅当已在配置文件中将 verbose 设置为 true 时，此消息才显示此信息片断 。

将鼠标光标悬停在某个问题区域上。 Linter 将提供有关错误或警告的详细信息。 选择该区域时，还会显示一个蓝色灯泡：

:::image type="content" source="./media/linter/bicep-linter-show-quickfix.png" alt-text="在 Visual Studio Code 中使用 Bicep Linter - 显示快速修复。":::

选择灯泡或“快速修复”链接以查看解决方法：

:::image type="content" source="./media/linter/bicep-linter-show-quickfix-solution.png" alt-text="在 Visual Studio Code 中使用 Bicep Linter - 显示快速修复解决方法。":::

选择解决方法以自动解决问题。

## <a name="use-in-bicep-cli"></a>使用 Bicep CLI

安装 Bicep CLI 0.4 或更高版本以使用 Linter。  以下屏幕截图显示了操作中的 Linter。 Bicep 文件与[在 Visual Studio Code 中使用](#use-in-visual-studio-code)部分中使用的文件相同。

:::image type="content" source="./media/linter/bicep-linter-command-line.png" alt-text="在命令行中使用 Bicep Linter。":::

可将这些检查集成为 CI/CD 管道的一部分。 可以使用 GitHub 操作来尝试生成 Bicep。 出错会导致管道失败。

## <a name="next-steps"></a>后续步骤

有关使用 Visual Studio Code 和 Bicep 扩展的详细信息，请参阅[快速入门：使用 Visual Studio Code 创建 Bicep 文件](./quickstart-create-bicep-use-visual-studio-code.md)。
