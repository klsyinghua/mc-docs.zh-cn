---
title: 将使用 Microsoft Authenticator 的 Xamarin iOS 应用程序从 ADAL.NET 迁移到 MSAL.NET | Azure
description: 了解如何将使用 Microsoft Authenticator 的 Xamarin iOS 应用程序从适用于 .NET 的 Azure AD 身份验证库 (ADAL.NET) 迁移到适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET)
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
origin.date: 09/08/2019
ms.date: 09/30/2019
ms.author: v-junlch
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3315fa34aedfa25c3fb2755d7b807525d2e66895
ms.sourcegitcommit: 74f50c9678e190e2dbb857be530175f25da8905e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2019
ms.locfileid: "72292163"
---
# <a name="migrating-ios-applications-using-microsoft-authenticator-from-adalnet-to-msalnet"></a>将使用 Microsoft Authenticator 的 iOS 应用程序从 ADAL.NET 迁移到 MSAL.NET

你使用 ADAL.NET 和 iOS 中介有一段时间，现在是时候迁移到 MSAL.NET [Microsoft 身份验证库](msal-overview.md)了。从版本 4.3 开始，该库支持 iOS 上的中介。 

在何处入门？ 本文帮助你将 Xamarin iOS 应用从 ADAL 迁移到 MSAL。

## <a name="prerequisites"></a>先决条件
本文档假设你已有一个与 iOS 中介集成的 Xamarin iOS 应用。 如果没有，最好是直接迁移到 MSAL.NET，然后在其中开始实施中介。 有关使用新应用程序调用 MSAL.NET 中的 iOS 中介的详细信息，请参阅[此文档](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)。

## <a name="background"></a>背景

### <a name="what-are-brokers"></a>什么是中介？

中介是 Microsoft 在 Android 和 iOS 上提供的应用程序（iOS 和 Android 上的 Microsoft Authenticator，以及 Android 上的 Intune 公司门户）。 

中介可以实现：

- 单一登录；
- 某些[条件访问策略](../conditional-access/overview.md)所需的设备标识（请参阅[设备管理](../conditional-access/conditions.md#device-platforms)）
- 同样在某些企业方案中需要的应用程序标识验证（例如，参阅 [Intune 移动应用程序管理 (MAM)](https://docs.microsoft.com/intune/mam-faq)）

## <a name="migrate-from-adal-to-msal"></a>从 ADAL 迁移到 MSAL

### <a name="step-1-enable-the-broker"></a>步骤 1：启用中介

<table>
<tr><td>当前 ADAL 代码：</td><td>对应的 MSAL 代码：</td></tr>
<tr><td>
在 ADAL.NET 中，中介支持将按身份验证上下文启用，默认已禁用。 必须在 `PlatformParameters` 构造函数中将 `useBroker` 标志设置为 true 才能调用中介：

```CSharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
此外，在特定于平台的代码中（对于本示例，是在 iOS 的页面呈现器中）将 `useBroker` 
标志设置为 true：
```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

然后，在获取令牌调用中包含参数：
```CSharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource, 
                              ClientId, 
                              new Uri(RedirectURI), 
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
在 MSAL.NET 中，中介支持是按公共客户端应用程序启用的。 此项默认禁用。 若要启用它，请使用： 

`WithBroker()` 参数（默认设置为 true），以调用中介：

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
在“获取令牌”调用中：
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>步骤 2：设置 UIViewController()
在 ADAL.NET 中，你已传入 UIViewController 作为 PlatformParameters 的一部分（参阅步骤 1 中的示例）。 但是，在 MSAL.NET 中，为了让开发人员获得更大的灵活性，将使用对象窗口，但在一般的 iOS 用途中不需要使用它。 不过，若要使用中介，需要设置对象窗口，以便向/从中介发送和接收响应。 
<table>
<tr><td>当前 ADAL 代码：</td><td>对应的 MSAL 代码：</td></tr>
<tr><td>
UIViewController 将传入到 iOS 特定平台中的 PlatformParamters 内。

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
在 MSAL.NET 中，需要执行以下两项操作来设置 iOS 的对象窗口：

1) 在 `AppDelegate.cs` 中，将 `App.RootViewController` 设置为新的 `UIViewController()`。 这种分配可确保提供一个 UIViewController 来调用中介。 如果未正确设置此参数，可能会收到以下错误：`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) 在 AcquireTokenInteractive 调用中使用 `.WithParentActivityOrWindow(App.RootViewController)`
并传入所要使用的对象窗口的引用。

例如： 

在 `App.cs`中：
```CSharp
   public static object RootViewController { get; set; }
```
在 `AppDelegate.cs`中：
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
在“获取令牌”调用中：
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>步骤 3：更新 AppDelegate 以处理回调
ADAL 和 MSAL 都会调用中介，而中介通过 `AppDelegate` 类的 `OpenUrl` 方法回调应用程序。 [此处](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS/_edit#step-two-update-appdelegate-to-handle-the-callback)提供了详细信息

:heavy_check_mark:**ADAL.NET 和 MSAL.NET 在此方面没有差别**

### <a name="step-4-register-a-url-scheme"></a>步骤 4：注册 URL 方案
ADAL.NET 和 MSAL.NET 使用 URL 调用中介，然后将中介响应返回到应用。 按如下所示在应用的 `Info.plist` 文件中注册 URL 方案：

<table>
<tr><td>当前 ADAL 代码：</td><td>对应的 MSAL 代码：</td></tr>
<tr><td>
URL 方案对于应用是唯一的。
</td><td>
The 

`CFBundleURLSchemes` 名称必须包含 

`msauth.`

作为前缀，后接 `CFBundleURLName`

例如： `$"msauth.(BundleId")`

```CSharp
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

> [!NOTE]
>  接收中介的响应时，此 URL 方案将成为用于唯一标识应用的 RedirectUri 的一部分

</table>

### <a name="step-5-lsapplicationqueriesschemes"></a>步骤 5：LSApplicationQueriesSchemes

ADAL.NET 和 MSAL.NET 都使用 `-canOpenURL:` 来检查是否在设备上安装了中介。 按如下所示，将 iOS 中介的正确标识符添加到 info.plist 文件的 LSApplicationQueriesSchemes 节： 
<table>
<tr><td>当前 ADAL 代码：</td><td>对应的 MSAL 代码：</td></tr>
<tr><td>
使用 

`msauth`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
使用 

`msauthv2`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
</array>
```
</table>

### <a name="step-6-register-you-redirecturi-in-the-portal"></a>步骤 6：在门户中注册 RedirectUri

在以中介为目标时，ADAL.NET 和 MSAL.NET 都在 redirectUri 方面施加额外的要求。 在门户中将重定向 URI 注册到应用程序。
<table>
<tr><td>当前 ADAL 代码：</td><td>对应的 MSAL 代码：</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"` 示例：`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

示例：

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

有关在门户中注册 redirectUri 的详细信息，请参阅[在 Xamarin.iOS 应用程序中利用中介](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app)。

## <a name="next-steps"></a>后续步骤

了解[与 MSAL.NET 配合使用时特定于 Xamarin iOS 的注意事项](msal-net-xamarin-ios-considerations.md)。 
