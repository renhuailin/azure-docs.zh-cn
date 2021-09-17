---
title: 向特定设备（通用 Windows 平台）发送通知 | Microsoft Docs
description: 将 Azure 通知中心与注册中的标记配合使用，将突发新闻发送到通用 Windows 平台应用。
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 08/23/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 3ee33accf751184636d7a345c5bba527a2525a72
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768173"
---
# <a name="tutorial-send-notifications-to-specific-devices-running-universal-windows-platform-applications"></a>教程：向运行通用 Windows 平台应用程序的特定设备发送通知

> [!NOTE]
> Microsoft 推送通知服务 (MPNS) 已弃用，不再受支持。

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>概述

本教程演示如何使用 Azure 通知中心来广播突发新闻通知。 本教程涵盖 Windows 应用商店或 Windows Phone 8.1（非 Silverlight）应用程序。 如果你的目标是 Windows Phone 8.1 Silverlight，请参阅[使用 Azure 通知中心向特定 Windows Phone 设备推送通知](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)。

本教程介绍如何使用 Azure 通知中心将通知推送到运行通用 Windows 平台 (UWP) 应用程序的特定 Windows 设备。 完成本教程后，可注册感兴趣的突发新闻类别， 之后你仅会收到这些类别的推送通知。

在通知中心创建注册时，可通过加入一个或多个标记来启用广播方案  。 将通知发送到标记时，已注册该标记的所有设备将接收通知。 有关标记的详细信息，请参阅[路由和标记表达式](notification-hubs-tags-segment-push-message.md)。

> [!NOTE]
> Visual Studio 2019 中不支持 Windows 应用商店和 Windows Phone 项目 8.1 及更早的版本。 有关详细信息，请参阅 [Visual Studio 2019 平台目标以及兼容性](/visualstudio/releases/2019/compatibility)。

在本教程中，你将执行以下任务：

> [!div class="checklist"]
> * 向移动应用添加类别选择
> * 注册通知
> * 发送带标记的通知
> * 运行应用并生成通知

## <a name="prerequisites"></a>先决条件

在开始本教程之前完成[教程：使用 Azure 通知中心向通用 Windows 平台应用发送通知][get-started]。  

## <a name="add-category-selection-to-the-app"></a>向应用程序中添加类别选择

第一步是向现有主页添加 UI 元素，以便用户选择要注册的类别。 所选类别会存储在设备上。 应用启动时，系统会将所选类别当做标记在通知中心创建设备注册。

1. 打开 *MainPage.xaml* 项目文件，并复制 `Grid` 元素中的以下代码：

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
    </Grid>
    ```

1. 在“解决方案资源管理器”  中，右键单击该项目，然后选择“添加”   >   “类”。 在“添加新项”中，将类命名为 *Notifications*，然后选择“添加”。   必要时向类定义添加 `public` 修饰符。

1. 向新文件添加以下 `using` 语句：

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

1. 将以下代码复制到新的 `Notifications` 类：

    ```csharp
    private NotificationHub hub;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        return await SubscribeToCategories(categories);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
        return categories != null ? categories.Split(','): new string[0];
    }

    public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                categories);
    }
    ```

    此类使用本地存储区存储此设备必须接收的新闻类别。 使用模板注册来注册类别时，应调用 `RegisterTemplateAsync` 方法，而不能调用 `RegisterNativeAsync`。

    如果需要注册多个模板，请提供模板名称，例如 *simpleWNSTemplateExample*。 为模板命名以便更新或删除模板。 可以注册多个模板，一个用于 toast 通知，一个用于磁贴。

    >[!NOTE]
    > 通过通知中心，设备可使用同一标记注册多个模板。 在这种情况下，针对该标签的传入的邮件将导致系统向设备发送多个通知（每个通知对应一个模板）。 此过程可以在多个可视通知中显示同一消息，如显示为 Windows 应用商店应用中的徽章和 toast 通知。

    有关详细信息，请参阅[模板](notification-hubs-templates-cross-platform-push-messages.md)。

1. 在 *App.xaml.cs* 项目文件中，将以下属性添加到 `App` 类：

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    使用此属性创建和访问 `Notifications` 实例。

    在代码中，将 `<hub name>` 和 `<connection string with listen access>` 占位符替换为通知中心的名称和之前获取的 **DefaultListenSharedAccessSignature** 的连接字符串。

   > [!NOTE]
   > 使用客户端应用分发的凭据通常不安全，因此请使用客户端应用仅分发具有侦听访问权限的密钥  。 拥有侦听访问权限后，应用可注册通知，但是无法修改现有注册，也无法发送通知。 在受保护的后端服务中使用完全访问权限密钥，以便发送通知和更改现有注册。

1. 在 *MainPage.xaml.cs* 文件中添加以下行：

    ```csharp
    using Windows.UI.Popups;
    ```

1. 在 *MainPage.xaml.cs* 文件中添加以下方法：

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```

    此方法创建一个类别列表并使用 `Notifications` 类将该列表存储在本地存储中。 它还会向通知中心注册相应的标记。 类别更改时，会使用新类别重新创建注册。

现在，应用可在设备的本地存储中存储一组类别。 每当用户更改类别选择时，应用都会向通知中心注册。

## <a name="register-for-notifications"></a>注册通知

本部分中将使用存储在本地存储中的类别，在启动时向通知中心注册。

> [!NOTE]
> 由于 Windows 通知服务 (WNS) 分配的通道 URI 随时可能更改，因此应该经常注册通知以避免通知失败。 此示例在每次应用程序启动时注册通知。 对于经常运行（例如，一天一次以上）的应用，如果距上次注册时间不到一天，可以跳过注册，以节省带宽。

1. 若要使用 `notifications` 类基于类别订阅，请打开 *App.xaml.cs* 文件，然后更新 `InitNotificationsAsync` 方法。

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    此过程可确保应用启动时会从本地存储区检索类别 并请求注册这些类别。 我们已在[使用 Azure 通知中心向通用 Windows 平台应用发送通知][get-started]教程中创建 `InitNotificationsAsync` 方法。
2. 在 *MainPage.xaml.cs* 项目文件的 `OnNavigatedTo` 方法中添加以下代码：

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;
    }
    ```

    此代码基于以前保存的类别状态更新主页。

应用现已完成。 它可以在设备的本地存储中存储一组类别。 当用户更改类别选择时，保存的类别用于向通知中心注册。 下一部分将定义一个后端，该后端可将类别通知发送到此应用。

## <a name="run-the-uwp-app"></a>运行 UWP 应用

1. 在 Visual Studio 中，选择 F5 编译并启动应用。 应用 UI 提供了一组开关，可以使用它们选择要订阅的类别。

   ![突发新闻应用](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breaking-news.png)

1. 启用一个或多个类别切换，然后选择“订阅”  。

   应用程序将所选类别转换为标签并针对所选标签从通知中心请求注册新设备。 应用在对话框中显示注册的类别。

    ![类别切换和订阅按钮](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>创建一个控制台应用以发送带标记的通知



[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>运行控制台应用以发送带标记的通知

运行上一部分中创建的应用。 所选类别的通知作为 toast 通知显示。

## <a name="next-steps"></a>后续步骤

我们已通过本文了解了如何按类别广播突发新闻。 后端应用程序将带标记的通知推送到特定设备，这些设备已注册接收该标记的通知。 若要了解如何向特定用户推送通知而不管这些用户使用什么设备，请转到以下教程：

> [!div class="nextstepaction"]
> [使用 Azure 通知中心向 Windows 应用推送本地化通知](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- URLs.-->
[get-started]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)