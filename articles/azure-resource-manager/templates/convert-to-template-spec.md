---
title: 将门户模板转换为模板规范
description: 描述如何将 Azure 门户库中的现有模板转换为模板规范。
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 8fe02f55348f2cdcabb43e05bb547819d4b51228
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98739083"
---
# <a name="convert-template-gallery-in-portal-to-template-specs"></a>将门户中的模板库转换为模板规范

Azure 门户提供了一种在帐户中存储 Azure 资源管理器模板 (ARM) 模板的方式。 **此功能已弃用。** 若要继续使用此库中的模板，请将它们转换为 [模板规范](template-specs.md)。

本文介绍如何将模板库中的现有模板转换为模板规范。

在门户中，不推荐使用的功能称为 **模板 (预览)**。 若要查看是否有任何要转换的模板，请查看 [门户中的模板库](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems)。 这些模板具有资源类型 `Microsoft.Gallery/myareas/galleryitems` 。

## <a name="deprecation-of-portal-feature"></a>弃用门户功能

门户中的模板库在2021年1月21日被弃用。 你可以继续使用它，直到2月21日。 从2月22日开始，你无法在门户库中创建新的模板，但仍可查看和部署现有模板。

6月22日，将从门户中删除该功能，所有 API 操作都将被阻止。 你将无法从库中查看或部署任何模板。

6月22日之前，你应该迁移要继续使用的任何模板。 你可以使用本文中所示的方法之一来迁移模板。 删除此功能后，需要打开支持案例才能获取尚未迁移的任何模板。

## <a name="convert-with-powershell-script"></a>转换为 PowerShell 脚本

若要简化模板库中的模板转换，请使用 Azure 快速入门模板存储库中的 PowerShell 脚本。 运行脚本时，可以为每个模板创建新的模板规范，或下载创建模板规范的模板。此脚本不会从模板库中删除模板。

1. 复制 [迁移脚本](https://github.com/Azure/azure-quickstart-templates/blob/master/201-templatespec-migrate-create/Migrate-GalleryItems.ps1)。 保存名称为 *Migrate-GalleryItems.ps1* 的本地副本。
1. 若要创建新的模板规格，请提供 `-ResourceGroupName` 和 `-Location` 参数的值。 

   设置 `ItemsToExport` 为 `MyGalleryItems` 以导出模板。 将其设置为 `AllGalleryItems` 以导出有权访问的所有模板。

   以下示例为资源组中名为 **migratedRG** 的每个模板创建新的模板规范。 如果资源组不存在，该脚本将创建该资源组。

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ResourceGroupName migratedRG -Location westus2 -ItemsToExport MyGalleryItems
   ```

1. 若要下载可用于创建模板规范的模板，请不要为资源组或位置提供值。 请改为指定 `-ExportToFile` 。 模板与库中的模板不同。 相反，它包含为模板创建模板规范的模板规范资源。

   下面的示例将下载模板而不创建模板规范。

   ```azurepowershell
   .\Migrate-GalleryItems.ps1 -ItemsToExport MyGalleryItems -ExportToFile
   ```

   若要了解如何部署用于创建模板规范的模板，请参阅 [快速入门：创建和部署模板规范 (预览) ](quickstart-create-template-specs.md)。

有关脚本及其参数的详细信息，请参阅 [从模板库模板创建 TemplateSpecs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-templatespec-migrate-create)。

## <a name="manually-convert-through-portal"></a>通过门户手动转换

您可以手动将模板从库复制到新的模板规范。

1. 在门户中打开 " [模板 (预览") ](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Gallery%2Fmyareas%2Fgalleryitems) 。
1. 选择要迁移的模板。
1. 选择 " **查看模板**"。
1. 复制模板内容。
1. 在门户搜索栏中，搜索 " **模板规范**"。 选择该选项。
1. 选择“创建模板规格”。
1. 提供 "名称"、"订阅"、"资源组"、"位置" 和 "版本" 的值。
1. 选择 **下一步：编辑模板**。
1. 对于模板的内容，粘贴从模板库中复制的模板。
1. 选择“查看 + 创建”  。
1. 验证成功完成后，选择 " **创建**"。

如果需要与组织中的其他用户共享模板规范，请为需要访问权限的组或用户 [设置基于角色的访问控制](../../role-based-access-control/tutorial-role-assignments-group-powershell.md) 。

## <a name="next-steps"></a>后续步骤

若要了解有关模板规范的详细信息，请参阅 [创建和部署模板规范](template-specs.md)。
