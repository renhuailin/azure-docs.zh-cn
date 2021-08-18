---
title: Azure 实验室服务中的容量限制
description: 了解 Azure 实验室服务中的容量限制（虚拟机数限制）。
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 344ca5950e38d3c4850accdbfb5062a011059919
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112294501"
---
# <a name="capacity-limits-in-azure-lab-services"></a>Azure 实验室服务中的容量限制
为了遵守 Azure 计算配额限制并减少欺诈，Azure 实验室服务对 Azure 订阅有默认的容量限制。 所有 Azure 订阅都有一个初始容量限制，此限制可能会因订阅类型、标准计算核心数和 Azure 实验室服务内可用的 GPU 核心数而异。 它限制了在你需要请求调高限制之前可以在实验室中创建多少台虚拟机。  

如果接近或已经达到了订阅的虚拟机核心数限制，那么当你尝试执行创建额外虚拟机的操作时，将看到来自 Azure 实验室服务的消息。 例如： 

- 创建实验室
- 发布实验室
- 调整实验室容量，以向现有实验室添加更多的虚拟机

如果已经达到了核心数限制，那么这些操作也可能被禁用。 

![核心数限制 - 警告消息](./media/capacity-limits/warning-message.png)

## <a name="subscriptions-with-default-limit-of-zero-cores"></a>默认限制为 0 个核心的订阅
一些更常用于欺诈的罕见订阅类型的默认限制是 0 个标准核心和 0 个 GPU 核心。 如果你使用的是这些订阅类型之一，则创建你的实验室帐户的管理员需要先请求调高限制，然后你才能使用 Azure 实验室服务。 

管理员可以按照以下步骤操作来请求调高限制：  

1.  在你的订阅中，[创建实验室帐户](tutorial-setup-lab-account.md)。
2.  在实验室帐户的“概述”页上，单击顶部的“请求调高限制”按钮。 
3.  按照窗体中的步骤操作，以提交调高限制的支持请求。

## <a name="request-a-limit-increase"></a>请求调高限制
如果已经达到了核心数限制，则可以请求调高限制来继续使用 Azure 实验室服务。 请求过程是一个检查点，以确保你的订阅不涉及任何欺诈或无意的、突然的大规模部署。

Azure 实验室服务门户中关于虚拟机核心数限制的消息包括用于请求调高限制的链接。 此链接会打开一个新的浏览器标签页，你可以在其中新建支持请求。 问题类型、订阅和配额类型信息将自动填写，如下图所示： 

![新建支持请求](./media/capacity-limits/new-support-request.png)


然后，系统会提示你提供有关调高限制的详细信息。 在“说明”字段中，提供以下详细信息：

- 你要尝试执行的操作（例如，创建实验室来教授计算机科学课程、运行编程马拉松等。）
- 你要对此实验室使用的虚拟机大小
- 你需要的虚拟机数

在你提交支持请求后，我们将审阅请求。 我们将视需要联系你来获取更多详细信息。 

## <a name="next-steps"></a>后续步骤
参阅以下文章：
- [管理员指南 - VM 大小调整](administrator-guide.md#vm-sizing)。
- [常见问题解答](classroom-labs-faq.yml)。