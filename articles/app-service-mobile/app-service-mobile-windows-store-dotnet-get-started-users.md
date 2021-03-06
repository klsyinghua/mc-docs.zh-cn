---
title: 向 UWP 应用添加身份验证
description: 了解如何使用 Azure 应用服务移动应用通过标识提供者（如 AAD、Google、Facebook、Twitter 和 Microsoft）对通用 Windows 平台 (UWP) 应用的用户进行身份验证。
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
origin.date: 06/25/2019
ms.date: 12/16/2019
ms.author: v-tawe
ms.openlocfilehash: d7484d84fddcb0267257a1634ce4b71a5829d9ca
ms.sourcegitcommit: cebee33429c25996658d322d337dd05ad1439f89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75600541"
---
# <a name="add-authentication-to-your-windows-app"></a>向 Windows 应用添加身份验证
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center 支持以移动应用开发为中心的端到端集成服务。 开发人员可以使用“生成”  、“测试”  和“分发”  服务来设置“持续集成和交付”管道。 部署应用后，开发人员可以使用**分析**和**诊断**服务监视其应用的状态和使用情况，并使用**推送**服务与用户互动。 开发人员还可以利用“身份验证”  对其用户进行身份验证，并使用“数据”  服务在云中保留和同步应用数据。
>
> 如果希望将云服务集成到移动应用程序中，请立即注册到 [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) 中。

## <a name="overview"></a>概述
本主题演示如何向移动应用添加基于云的身份验证。 在本教程中，使用 Azure 应用服务支持的标识提供者向移动应用的通用 Windows 平台 (UWP) 快速入门项目添加身份验证。 在移动应用后端成功进行身份验证和授权后，显示用户 ID 值。

本教程基于移动应用快速入门。 必须先完成[移动应用入门](app-service-mobile-windows-store-dotnet-get-started.md)教程。

## <a name="register"></a>注册应用以进行身份验证并配置应用服务
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>将应用添加到允许的外部重定向 URL

安全身份验证要求为应用定义新的 URL 方案。 此方案允许在完成身份验证过程后，身份验证系统重定向到应用。 在本教程中，我们自始至终使用 URL 方案 _appname_ 。 但是，可以使用任何你所选的 URL 方案。 对于移动应用程序而言，它应是唯一的。 在服务器端启用重定向：

1. 在 [Azure 门户](https://portal.azure.cn)中，选择应用服务。

2. 单击“身份验证/授权”  菜单选项。

3. 在“允许的外部重定向 URL”  中，输入 `url_scheme_of_your_app://easyauth.callback`。  此字符串中的 **url_scheme_of_your_app** 是移动应用程序的 URL 方案。  它应该遵循协议的正常 URL 规范（仅使用字母和数字，并以字母开头）。  应记下此字符串，因为在一些地方需要使用此 URL 方案调整移动应用代码。

4. 单击“保存”  。

## <a name="permissions"></a>将权限限制给已经过身份验证的用户
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

现在，可以验证是否已禁用对后端的匿名访问。 将 UWP 应用项目设为启动项目后，部署并运行该应用；验证启动该应用后，是否会引发状态代码为 401（“未授权”）的未处理异常。 发生此异常的原因是应用尝试以未经身份验证的用户身份访问移动应用代码，但 *TodoItem* 表现在要求身份验证。

接下来，更新应用，以便在从应用服务请求资源之前对用户进行身份验证。

## <a name="add-authentication"></a>向应用程序添加身份验证
1. 在 UWP 应用项目文件 MainPage.xaml.cs 中，添加以下代码片段：

    ```
    // Define a member variable for storing the signed-in user. 
    private MobileServiceUser user;

    // Define a method that performs the authentication process
    // using a Microsoft sign-in. 
    private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
    {
        string message;
        bool success = false;
        try
        {
            // Change 'MobileService' to the name of your MobileServiceClient instance.
            // Sign-in using MicrosoftAccount authentication.
            user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.MicrosoftAccount, "{url_scheme_of_your_app}");
            message =
                string.Format("You are now signed in - {0}", user.UserId);

            success = true;
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
        return success;
    }
    ```

    此代码使用 MicrosoftAccount 登录对用户进行身份验证。 如果使用的标识提供者不是 MicrosoftAccount，请将上述 **MobileServiceAuthenticationProvider** 的值更改为提供者的值。
2. 替换 MainPage.xaml.cs 中的 **OnNavigatedTo()** 方法。 接下来，向应用添加用于触发身份验证的“登录”  按钮。

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. 将以下代码片段添加到 MainPage.xaml.cs：

    ```
    private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
    {
        // Login the user and then load data from the mobile app.
        if (await AuthenticateAsync())
        {
            // Switch the buttons and load items from the mobile app.
            ButtonLogin.Visibility = Visibility.Collapsed;
            ButtonSave.Visibility = Visibility.Visible;
            //await InitLocalStoreAsync(); //offline sync support.
            await RefreshTodoItems();
        }
    }
    ```

4. 打开 MainPage.xaml 项目文件，找到定义“保存”  按钮的元素，将其替换为以下代码：

    ```
    <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
            Click="ButtonSave_Click">
        <StackPanel Orientation="Horizontal">
            <SymbolIcon Symbol="Add"/>
            <TextBlock Margin="5">Save</TextBlock>
        </StackPanel>
    </Button>
    <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
            Click="ButtonLogin_Click" TabIndex="0">
        <StackPanel Orientation="Horizontal">
            <SymbolIcon Symbol="Permissions"/>
            <TextBlock Margin="5">Sign in</TextBlock> 
        </StackPanel>
    </Button>
    ```
    
5. 将以下代码片段添加到 App.xaml.cs：

        protected override void OnActivated(IActivatedEventArgs args)
        {
            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                Frame content = Window.Current.Content as Frame;
                if (content.Content.GetType() == typeof(MainPage))
                {
                    content.Navigate(typeof(MainPage), protocolArgs.Uri);
                }
            }
            Window.Current.Activate();
            base.OnActivated(args);
        }
6. 打开 Package.appxmanifest 文件，导航到“声明”  ，在“可用声明”  下拉列表中，选择“协议”  并单击“添加”  按钮。 现在，配置**协议**声明的**属性**。 在“显示名称”  中，添加要向应用程序的用户显示的名称。 在“名称”  中，添加 {url_scheme_of_your_app}。
7. 按 F5 键运行该应用，单击“登录”  按钮，然后使用所选的标识提供者登录到该应用。 成功登录后，该应用运行时不会出错，用户能够查询后端，并对数据进行更新。

## <a name="tokens"></a>在客户端上存储身份验证令牌
前一示例显示了标准登录，这要求在该应用每次启动时客户端同时联系标识提供者和应用服务。 此方法不仅效率低下，而且如果很多客户尝试同时启动应用，会遇到关于使用率的问题。 更好的方法是缓存应用服务返回的授权令牌，并在使用基于提供者的登录之前首先尝试使用此令牌。

> [!NOTE]
> 无论使用的是客户端管理的还是服务管理的身份验证，都可以缓存应用服务颁发的令牌。 本教程使用服务管理的身份验证。
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>后续步骤
完成此基本身份验证教程后，请考虑继续学习以下教程之一：

* [向应用添加推送通知](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  了解如何为应用添加推送通知支持，以及如何将移动应用后端配置为使用 Azure 通知中心发送推送通知。
* [为应用启用脱机同步](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  了解如何使用移动应用后端向应用添加脱机支持。 借助脱机同步，最终用户即使在没有网络连接时也能够与移动应用进行交互（查看、添加或修改数据）。

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
