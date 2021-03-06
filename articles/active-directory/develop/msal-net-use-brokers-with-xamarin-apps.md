---
title: 将代理与 Xamarin、iOS 和 Android 配合使用 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何将可以使用 Microsoft Authenticator 的 Xamarin iOS 应用程序从适用于 .NET 的 Azure AD 身份验证库 (ADAL.NET) 迁移到适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET)
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/06/2020
ms.author: v-junlch
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffdff60f76c414f643f98d42f5fa685c828cab0e
ms.sourcegitcommit: 1bc154c816a5dff47ee051c431cd94826e57aa60
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75776986"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>在 Xamarin 应用程序中使用 Microsoft Authenticator 或 Microsoft Intune 公司门户

在 Android 和 iOS 上，Microsoft Authenticator 或 Microsoft Intune 公司门户（仅限 Android）等中介可以实现：

- 单一登录 (SSO)。 用户无需登录到每个应用程序。
- 设备标识。 中介会访问设备证书，该证书是在设备加入工作区时在设备上创建的。
- 应用程序标识验证。 应用程序在调用中介时会传递其重定向 URL，而中介会验证该 URL。

若要启用这些功能之一，应用程序开发人员需要在调用 `PublicClientApplicationBuilder.CreateApplication` 方法时使用 `WithBroker()` 参数。 `.WithBroker()` 默认设置为 true。 开发人员还需要对 [iOS](#brokered-authentication-for-ios) 或 [Android](#brokered-authentication-for-android) 应用程序执行以下步骤。

## <a name="brokered-authentication-for-ios"></a>适用于 iOS 的中介身份验证

请按以下步骤操作，使 Xamarin.iOS 应用能够与 Microsoft Authenticator 应用通信。

### <a name="step-1-enable-broker-support"></a>步骤 1：启用中介支持
中介支持是按 PublicClientApplication 启用的。 此项默认禁用。 通过 PublicClientApplicationBuilder 创建 PublicClientApplication 时，请使用 `WithBroker()` 参数（默认设置为 true）。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>步骤 2：启用密钥链访问

若要启用密钥链访问，应用程序必须有密钥链访问组。 创建应用程序时，可以使用 `WithIosKeychainSecurityGroup()` API 来设置密钥链访问组：

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

有关详细信息，请参阅[启用密钥链访问](msal-net-xamarin-ios-considerations.md#enable-keychain-access)。

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>步骤 3：更新 AppDelegate 以处理回调
当适用于 .NET 的 Microsoft Authentication 库 (MSAL.NET) 调用中介时，中介会通过 `AppDelegate` 类的 `OpenUrl` 方法回调应用程序。 由于 MSAL 会等待来自中介的响应，因此应用程序需要进行协作才能回调 MSAL.NET。 若要启用此协作，请更新 `AppDelegate.cs` 文件以重写以下方法。

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, 
                             string sourceApplication,
                             NSObject annotation)
{
    if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
    {
      AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
      return true;
    }
    
    else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
    {               
         return false;                
    }
    
    return true;     
}           
```

每次启动应用程序都会调用此方法。 可以借此机会处理中介的响应，并完成 MSAL.NET 发起的身份验证过程。

### <a name="step-4-set-a-uiviewcontroller"></a>步骤 4：设置 UIViewController()
需要在 `AppDelegate.cs` 中设置对象窗口。 在 Xamarin iOS 中，通常不需设置对象窗口。 若要发送请求并接收中介的响应，则需要一个对象窗口。 

为此，请执行以下两项操作。 
1. 在 `AppDelegate.cs` 中，将 `App.RootViewController` 设置为新的 `UIViewController()`。 这种分配可确保提供一个 UIViewController 来调用中介。 如果未正确设置此参数，可能会收到以下错误：`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. 在 AcquireTokenInteractive 调用中，使用 `.WithParentActivityOrWindow(App.RootViewController)` 并传入对你要使用的对象窗口的引用。

例如： 

在 `App.cs`中：
```csharp
   public static object RootViewController { get; set; }
```
在 `AppDelegate.cs`中：
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
在“获取令牌”调用中：
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

### <a name="step-5-register-a-url-scheme"></a>步骤 5：注册 URL 方案
MSAL.NET 使用 URL 调用中介，然后将中介响应返回到应用。 若要完成往返过程，请在 `Info.plist` 文件中注册应用的 URL 方案。

`CFBundleURLSchemes` 名称必须包含 `msauth.` 作为前缀，后接 `CFBundleURLName`。

`$"msauth.(BundleId)"`

例如： 

`msauth.com.yourcompany.xforms`

> [!NOTE]
> 此 URL 方案会成为重定向 URI 的一部分，该 URI 用于在应用接收中介的响应时对应用进行唯一标识。

```XML
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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>步骤 6：将中介标识符添加到 LSApplicationQueriesSchemes 节
MSAL 使用 `-canOpenURL:` 来检查是否在设备上安装了中介。 在 iOS 9 中，Apple 锁定了应用程序可以查询的方案。 

将 `msauthv2` 添加到 `Info.plist` 文件的 `LSApplicationQueriesSchemes` 节。

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>步骤 7：在应用程序门户中注册重定向 URI
使用中介需要满足重定向 URI 方面的额外要求。 重定向 URI  必须采用以下格式：
```csharp
$"msauth.{BundleId}://auth"
```
例如： 
```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
请注意，重定向 URI 与 `Info.plist` 文件中包含的 `CFBundleURLSchemes` 名称匹配。

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>步骤 8：确保将重定向 URI 注册到应用

需要在应用注册门户 (https://portal.azure.cn) 中将此重定向 URI 注册为应用程序的有效重定向 URI。 

应用注册门户提供新的体验，允许你根据捆绑 ID 计算中介回复 URI。

1. 在应用注册中选择“身份验证”，然后选择“尝试新体验”。  

   ![尝试新的应用注册体验](./media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. 选择“添加平台”。 

   ![添加平台](./media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. 如果支持平台列表，请选择“iOS”。 

   ![配置 iOS](./media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. 依请求输入捆绑 ID，然后选择“配置”。 

   ![输入捆绑 ID](./media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

1. 此时会为你计算重定向 URI。

   ![复制重定向 URI](./media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>适用于 Android 的中介身份验证

目前 MSAL.NET 仅支持 Xamarin.iOS 平台。 它尚不支持适用于 Xamarin.Android 平台的代理。

MSAL Android 本机库已支持它。 有关详细信息，请参阅 [Android 中的代理身份验证](brokered-auth.md)

## <a name="next-steps"></a>后续步骤

了解[与 MSAL.NET 配合使用时特定于通用 Windows 平台的注意事项](msal-net-uwp-considerations.md)。

<!-- Update_Description: wording update -->