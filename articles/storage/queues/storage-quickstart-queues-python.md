---
title: 快速入门：Azure 队列存储库 v12 - Python
description: 了解如何使用 Azure 队列 Python v12 库创建队列并向队列中添加消息。 接下来，介绍如何在队列中读取和删除消息。 还将介绍如何删除队列。
author: WenJason
ms.author: v-jay
origin.date: 12/10/2019
ms.date: 01/06/2020
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: 517110eee2be48144d151a6322a472c552a85740
ms.sourcegitcommit: 6a8bf63f55c925e0e735e830d67029743d2c7c0a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75624391"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>快速入门：适用于 Python 的 Azure 队列存储客户端库 v12

适用于 Python 的 Azure 队列存储客户端库版本 12 入门。 Azure 队列存储服务可存储大量消息供稍后检索和处理。 请按照以下步骤安装程序包并试用基本任务的示例代码。

使用适用于 Python 的 Azure 队列存储客户端库 v12：

* 创建队列
* 向队列添加消息
* 查看队列中的消息
* 更新队列中的消息
* 从队列接收消息
* 从队列中删除消息
* 删除队列

[API 参考文档](https://docs.microsoft.com/python/api/azure-storage-queue/index) | [库源代码](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue) | [包 （Python 包索引）](https://pypi.org/project/azure-storage-queue/) | [示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [创建一个 1 元试用帐户](https://www.azure.cn/pricing/1rmb-trial-full/?form-type=identityauth)
* Azure 存储帐户 - [创建存储帐户](/storage/common/storage-quickstart-create-account)
* 适用于你的操作系统的 [Python](https://www.python.org/downloads/) - 2.7、3.5 或更高版本

## <a name="setting-up"></a>设置

本部分逐步指导如何准备一个项目，使其与适用于 Python 的 Azure 队列存储客户端库 v12 配合使用。

### <a name="create-the-project"></a>创建项目

创建名为 queues-quickstart-v12 的 Python 应用程序  。

1. 在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为项目创建新目录。

    ```console
    mkdir queues-quickstart-v12
    ```

1. 切换到新创建的 queues-quickstart-v12 目录  。

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>安装包

使用 `pip install` 命令安装适用于 Python 包的 Azure Blob 存储客户端库。

```console
pip install azure-storage-queue
```

此命令安装适用于 Python 包的 Azure 队列存储客户端库及其依赖的所有库。 在本例中，即适用于 Python 的 Azure Core 库。

### <a name="set-up-the-app-framework"></a>设置应用框架

1. 在代码编辑器中打开新文本文件
1. 添加 `import` 语句
1. 为程序创建结构，包括非常基本的异常处理

    代码如下：

    ```python
    import os, uuid
    from azure.storage.queue import QueueServiceClient, QueueClient, QueueMessage

    try:
        print("Azure Queue storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)

    ```

1. 将新文件在 queues-quickstart-v12 目录中另存为 queues-quickstart-v12.py   。

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>对象模型

Azure 队列存储是一个可存储大量消息的服务。 队列消息大小最大可为 64 KB。 一个队列可以包含数百万条消息，直至达到存储帐户的总容量限值。 队列通常用于创建要异步处理的积压工作 (backlog)。 队列存储提供三种类型的资源：

* 存储帐户
* 存储帐户中的队列
* 队列中的消息

以下图示显示了这些资源之间的关系。

![队列存储体系结构的图示](./media/storage-queues-introduction/queue1.png)

使用以下 Python 类与这些资源进行交互：

* [QueueServiceClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient)：通过 `QueueServiceClient` 可管理存储帐户中的所有队列。
* [QueueClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient)：通过 `QueueClient` 类可管理和操纵单个队列及其消息。
* [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage)：`QueueMessage` 类表示在队列中调用 [receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) 时返回的各个对象。

## <a name="code-examples"></a>代码示例

这些示例代码片段演示如何使用适用于 Python 的 Azure 队列存储客户端库执行以下操作：

* [获取连接字符串](#get-the-connection-string)
* [创建队列](#create-a-queue)
* [向队列添加消息](#add-messages-to-a-queue)
* [查看队列中的消息](#peek-at-messages-in-a-queue)
* [更新队列中的消息](#update-a-message-in-a-queue)
* [从队列接收消息](#receive-messages-from-a-queue)
* [从队列中删除消息](#delete-messages-from-a-queue)
* [删除队列](#delete-a-queue)

### <a name="get-the-connection-string"></a>获取连接字符串

以下代码检索存储帐户的连接字符串。 连接字符串存储在[配置存储连接字符串](#configure-your-storage-connection-string)部分中创建的环境变量中。

在 `try` 块内添加此代码：

```python
    # Retrieve the connection string for use with the application. The storage
    # connection string is stored in an environment variable on the machine
    # running the application called AZURE_STORAGE_CONNECTION_STRING. If the
    # environment variable is created after the application is launched in a
    # console or with Visual Studio, the shell or application needs to be
    # closed and reloaded to take the environment variable into account.
    connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-queue"></a>创建队列

确定新队列的名称。 以下代码将 UUID 值追加到队列名称，从而确保其是唯一的。

> [!IMPORTANT]
> 队列名称只能包含小写字母、数字和连字符，并且必须以字母或数字开头。 每个连字符的前后必须为非连字符字符。 名称的长度还必须介于 3 到 63 个字符之间。 有关命名队列的详细信息，请参阅[命名队列和元数据](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata)。

创建 [QueueClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient) 类的实例。 然后，调用 [create_queue](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) 方法，以便在存储帐户中创建队列。

将此代码添加到 `try` 块的末尾：

```python
    # Create a unique name for the queue
    queue_name = "quickstartqueues-" + str(uuid.uuid4())

    print("Creating queue: " + queue_name)

    # Instantiate a QueueClient which will be
    # used to create and manipulate the queue
    queue_client = QueueClient.from_connection_string(connect_str, queue_name)

    # Create the queue
    queue_client.create_queue()
```

### <a name="add-messages-to-a-queue"></a>向队列添加消息

下面的代码片段调用 [send_message](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) 方法将消息添加到队列中。 它还保存从第三次 `send_message` 调用返回的 [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage)。 `saved_message` 用于稍后在程序中更新消息内容。

将此代码添加到 `try` 块的末尾：

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>查看队列中的消息

调用 [peek_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) 方法来查看队列中的消息。 `peek_messages` 方法从队列的前部检索一条或多条消息，但不会更改消息的可见性。

将此代码添加到 `try` 块的末尾：

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>更新队列中的消息

调用 [update_message](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) 方法来更新消息的内容。 `update_message` 方法可以更改消息的可见性超时和内容。 消息内容必须是不超过 64 KB 的 UTF-8 编码字符串。 除新内容外，还要在代码中传入之前保存的消息中的值。 `saved_message` 值标识要更新的消息。

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>从队列接收消息

调用 [receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) 方法来下载以前添加的消息。

将此代码添加到 `try` 块的末尾：

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>从队列中删除消息

接收并处理消息后，从队列中删除消息。 在本例中，“处理”即在控制台上显示消息。

在处理和删除消息之前，应用会调用 `input` 以暂停并等待用户输入。 在删除资源之前，请先在 [Azure 门户](https://portal.azure.cn)中验证资源已正确创建。 未显式删除的任何消息都将在队列中再次变为可见，以方便再一次进行处理。

将此代码添加到 `try` 块的末尾：

```python
    print("\nPress Enter key to 'process' messages and delete them from the queue...")
    input()

    for msg_batch in messages.by_page():
            for msg in msg_batch:
                # "Process" the message
                print(msg.content)
                # Let the service know we're finished with
                # the message and it can be safely deleted.
                queue_client.delete_message(msg)
```

### <a name="delete-a-queue"></a>删除队列

以下代码使用 [delete_queue](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) 方法删除队列，从而清除应用所创建的资源。

将此代码添加到 `try` 块的末尾并保存文件：

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>运行代码

此应用创建三条消息并将其添加到 Azure 队列。 此代码列出队列中的消息，然后检索并删除它们，最后删除队列。

在控制台窗口中，导航到包含 queues-quickstart-v12.py 文件的目录，然后执行以下 `python` 命令来运行应用  。

```console
python queues-quickstart-v12.py
```

应用的输出类似于以下示例：

```output
Azure Queue storage v12 - Python quickstart sample
Creating queue: quickstartqueues-cac365be-7ce6-4065-bd65-3756ea052cb8

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

First message
Second message
Third message has been updated

Press Enter key to delete the queue...

Deleting queue...
Done
```

当应用在接收到消息之前暂停时，请在 [Azure 门户](https://portal.azure.cn)中检查存储帐户。 验证消息是否在队列中。

按“Enter”  键接收和删除消息。 出现提示时，请再次按“Enter”  键，删除队列并完成演示。

## <a name="next-steps"></a>后续步骤

通过本快速入门，你了解了如何使用 Python 代码创建队列并向其添加消息。 然后了解了如何查看、检索和删除消息。 最后，你还了解了如何删除消息队列。

有关教程、示例、快速入门和其他文档，请访问：

> [!div class="nextstepaction"]
> [面向 Python 开发人员的 Azure](https://docs.microsoft.com/azure/python/)

* 若要了解详细信息，请参阅[用于 Python 的 Azure 存储库](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)。
* 若要查看更多 Azure 队列存储示例应用，请继续阅读 [Azure 队列存储 v12 Python 客户端库示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples)。
