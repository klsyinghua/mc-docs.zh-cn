---
title: 将自定义域映射到 Azure Blob 存储终结点
titleSuffix: Azure Storage
description: 将自定义域映射到 Azure 存储帐户中的 Blob 存储或 Web 终结点。
author: WenJason
ms.service: storage
ms.topic: conceptual
origin.date: 01/23/2020
ms.date: 02/10/2020
ms.author: v-jay
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 1adc8a339008f3f5cdb60d10a63ede3551d5cb00
ms.sourcegitcommit: 5c4141f30975f504afc85299e70dfa2abd92bea1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77028899"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>将自定义域映射到 Azure Blob 存储终结点

可将自定义域映射到 Blob 服务终结点。 

> [!NOTE] 
> 这种映射仅适用于子域（例如：`www.contoso.com`）。 

<a id="enable-http" />

## <a name="map-a-custom-domain-with-only-http-enabled"></a>在启用仅限 HTTP 的情况下映射自定义域

此方法更简单，但要启用仅限 HTTP 的访问。

<a id="map-a-domain" />

### <a name="map-a-custom-domain"></a>映射自定义域

> [!IMPORTANT]
> 在完成配置的过程中，用户会有短暂的时间无法使用你的自定义域。 如果域当前支持的某个应用程序所附带的服务级别协议 (SLA) 要求停机时间为零，请遵循本文的[在不停机的情况下映射自定义域](#zero-down-time)部分中的步骤，以确保用户在 DNS 映射期间能够访问该域。

如果你不担心域有短暂的时间对用户不可用，请执行以下步骤。

:heavy_check_mark:步骤 1：获取存储终结点的主机名。

:heavy_check_mark:步骤 2：在域提供商的配合下创建规范名称 (CNAME) 记录。

:heavy_check_mark:步骤 3：将自定义域注册到 Azure。 

:heavy_check_mark:步骤 4：测试自定义域。

<a id="endpoint" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>步骤 1：获取存储终结点的主机名 

主机名是没有协议标识符和尾部斜杠的存储终结点 URL。 

1. 在 [Azure 门户](https://portal.azure.cn)中转到自己的存储帐户。

2. 在菜单窗格中的“设置”下，选择“属性”。    

3. 将“主 Blob 服务终结点”的值复制到文本文件。  

4. 从该字符串中删除协议标识符（例如 HTTPS）和尾部斜杠。  下表提供了一些示例。

   | 终结点的类型 |  endpoint | 主机名 |
   |------------|-----------------|-------------------|
   |Blob 服务  | `https://mystorageaccount.blob.core.chinacloudapi.cn/` | `mystorageaccount.blob.core.chinacloudapi.cn` |
  
   请设置此值供稍后使用。

<a id="create-cname-record" />

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>步骤 2：在域提供商的配合下创建规范名称 (CNAME) 记录

创建一条 CNAME 记录，以指向主机名。 CNAME 记录是一种 DNS 记录，用于将源域名映射到目标域名。

1. 登录到域注册机构的网站，并转到用于管理 DNS 设置的页面。

   可在名为“域名”、“DNS”或“名称服务器管理”的部分中找到该页。   

2. 找到用于管理 CNAME 记录的部分。 

   可能需要转到高级设置页，并查找“CNAME”、“别名”或“子域”。   

3. 创建 CNAME 记录。 在该记录中提供以下项： 

   - 子域别名，例如 `www` 或 `photos`。 必须指定子域，不支持根域。 
      
   - 在本文前面的[获取存储终结点的主机名](#endpoint)部分获取的主机名。 

<a id="register" />

#### <a name="step-3-register-your-custom-domain-with-azure"></a>步骤 3：将自定义域注册到 Azure

1. 在 [Azure 门户](https://portal.azure.cn)中转到自己的存储帐户。

2. 在菜单窗格中的“Blob 服务”下，选择“自定义域”。    

   此时会打开“自定义域”窗格。 

3. 在“域名”文本框中输入自定义域的名称，包括子域   
   
   例如，如果域是 *contoso.com*，子域别名是 *www*，请输入 `www.contoso.com`。 如果子域是 *photos*，请输入 `photos.contoso.com`。

4. 若要注册自定义域，请选择“保存”按钮。 

   CNAME 记录通过域名服务器 (DNS) 传播后，如果用户具有相应的权限，则他们可以使用自定义域查看 Blob 数据。

#### <a name="step-4-test-your-custom-domain"></a>步骤 4：测试自定义域

若要确认自定义域是否映射到了 Blob 服务终结点，请在存储帐户中的公共容器内创建一个 Blob。 然后在 Web 浏览器中，使用以下格式的 URI 来访问该 Blob：`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

例如，若要访问 *photos.contoso.com* 自定义子域中的 *myforms* 容器内的 Web 窗体：可使用以下 URI：`http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time" />

### <a name="map-a-custom-domain-with-zero-downtime"></a>在不停机的情况下映射自定义域

> [!NOTE]
> 如果你不担心域有短暂的时间对用户不可用，请考虑执行本文的[映射自定义域](#map-a-domain)部分中的步骤。 这是一种更简单的方法，且步骤更少。  

如果域当前支持的某个应用程序所附带的服务级别协议 (SLA) 要求停机时间为零，请执行这些步骤，以确保用户在 DNS 映射期间能够访问该域。 

:heavy_check_mark:步骤 1：获取存储终结点的主机名。

:heavy_check_mark:步骤 2：在域提供商的配合下创建一条中间规范名称 (CNAME) 记录。

:heavy_check_mark:步骤 3：将自定义域预先注册到 Azure。

:heavy_check_mark:步骤 4：在域提供商的配合下创建 CNAME 记录。

:heavy_check_mark:步骤 5：测试自定义域。

<a id="endpoint-2" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>步骤 1：获取存储终结点的主机名 

主机名是没有协议标识符和尾部斜杠的存储终结点 URL。 

1. 在 [Azure 门户](https://portal.azure.cn)中转到自己的存储帐户。

2. 在菜单窗格中的“设置”下，选择“属性”。    

3. 将“主 Blob 服务终结点”的值复制到文本文件。  

4. 从该字符串中删除协议标识符（例如 HTTPS）和尾部斜杠。  下表提供了一些示例。

   | 终结点的类型 |  endpoint | 主机名 |
   |------------|-----------------|-------------------|
   |Blob 服务  | `https://mystorageaccount.blob.core.chinacloudapi.cn/` | `mystorageaccount.blob.core.chinacloudapi.cn` |
  
   请设置此值供稍后使用。

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>步骤 2：在域提供商的配合下创建一条中间规范名称 (CNAME) 记录

创建一条临时 CNAME 记录，以指向主机名。 CNAME 记录是一种 DNS 记录，用于将源域名映射到目标域名。

1. 登录到域注册机构的网站，并转到用于管理 DNS 设置的页面。

   可在名为“域名”、“DNS”或“名称服务器管理”的部分中找到该页。   

2. 找到用于管理 CNAME 记录的部分。 

   可能需要转到高级设置页，并查找“CNAME”、“别名”或“子域”。   

3. 创建 CNAME 记录。 在该记录中提供以下项： 

   - 子域别名，例如 `www` 或 `photos`。 必须指定子域，不支持根域。

     将 `asverify` 子域添加到别名。 例如： `asverify.www` 或 `asverify.photos` 。
       
   - 在本文前面的[获取存储终结点的主机名](#endpoint)部分获取的主机名。 

     将子域 `asverify` 添加到主机名。 例如：`asverify.mystorageaccount.blob.core.chinacloudapi.cn`。

4. 若要注册自定义域，请选择“保存”按钮。 

   如果注册成功，则门户会通知存储帐户已成功更新。 自定义域已由 Azure 验证，但发往域的流量尚未路由到存储帐户。

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>步骤 3：将自定义域预先注册到 Azure

将自定义域预先注册到 Azure 后，可使 Azure 识别该自定义域，且无需修改该域的 DNS 记录。 这样，在修改该域的 DNS 记录时，它会映射到 Blob 终结点，并且不会出现停机。

1. 在 [Azure 门户](https://portal.azure.cn)中转到自己的存储帐户。

2. 在菜单窗格中的“Blob 服务”下，选择“自定义域”。    

   ![自定义域选项](./media/storage-custom-domain-name/custom-domain-button.png "自定义域")

   此时会打开“自定义域”窗格。 

3. 在“域名”文本框中输入自定义域的名称，包括子域   
   
   例如，如果域是 *contoso.com*，子域别名是 *www*，请输入 `www.contoso.com`。 如果子域是 *photos*，请输入 `photos.contoso.com`。

4. 选中“使用间接 CNAME 验证”复选框。 

5. 若要注册自定义域，请选择“保存”按钮。 
  
   CNAME 记录通过域名服务器 (DNS) 传播后，如果用户具有相应的权限，则他们可以使用自定义域查看 Blob 数据。

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>步骤 4：在域提供商的配合下创建 CNAME 记录

创建一条临时 CNAME 记录，以指向主机名。

1. 登录到域注册机构的网站，并转到用于管理 DNS 设置的页面。

   可在名为“域名”、“DNS”或“名称服务器管理”的部分中找到该页。   

2. 找到用于管理 CNAME 记录的部分。 

   可能需要转到高级设置页，并查找“CNAME”、“别名”或“子域”。   

3. 创建 CNAME 记录。 在该记录中提供以下项： 

   - 子域别名，例如 `www` 或 `photos`。 必须指定子域，不支持根域。
      
   - 在本文前面的[获取存储终结点的主机名](#endpoint-2)部分获取的主机名。 

#### <a name="step-5-test-your-custom-domain"></a>步骤 5：测试自定义域

若要确认自定义域是否映射到了 Blob 服务终结点，请在存储帐户中的公共容器内创建一个 Blob。 然后在 Web 浏览器中，使用以下格式的 URI 来访问该 Blob：`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

例如，若要访问 *photos.contoso.com* 自定义子域中的 *myforms* 容器内的 Web 窗体：可使用以下 URI：`http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>删除自定义域映射

若要删除自定义域映射，请取消注册自定义域。 使用以下过程之一。

#### <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

若要删除自定义域设置，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.cn)中转到自己的存储帐户。

2. 在菜单窗格中的“Blob 服务”下，选择“自定义域”。    
   此时会打开“自定义域”窗格。 

3. 清除包含自定义域名的文本框的内容。

4. 选择“保存”按钮。 

成功删除自定义域后，会看到一条门户通知，指出存储帐户已成功更新

#### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

若要删除自定义域注册，请使用 [az storage account update](/cli/storage/account) CLI 命令，并为 `--custom-domain` 参数值指定空字符串 (`""`)。

* 命令格式：

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* 命令示例：

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

#### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要删除自定义域注册，请使用 [Set-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) PowerShell cmdlet，并为 `-CustomDomainName` 参数值指定空字符串 (`""`)。

* 命令格式：

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* 命令示例：

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```
---

