---
title: 配置复制中数据以将数据复制到 Azure Database for MySQL。
description: 本文介绍如何为 Azure Database for MySQL 设置复制中数据。
services: mysql
author: WenJason
ms.author: v-jay
manager: digimobile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
origin.date: 08/31/2018
ms.date: 10/22/2018
ms.openlocfilehash: 1497aa354834d075113b723ce383570f39aa1ad9
ms.sourcegitcommit: 5fc46672ae90b6598130069f10efeeb634e9a5af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2019
ms.locfileid: "67236641"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>如何配置 Azure Database for MySQL 的数据传入复制

> [!NOTE]
> 将要查看的是 Azure Database for MySQL 的新服务。 若要查看经典 MySQL Database for Azure 的文档，请访问[此页](https://docs.azure.cn/zh-cn/mysql-database-on-azure/)。

本文介绍如何通过配置主服务器和副本服务器在 Azure Database for MySQL 服务中设置“数据传入复制”。 凭借“数据传入复制”，可以将在本地或虚拟机中运行的主 MySQL 服务器或其他云提供程序托管的数据库服务中的数据同步到 Azure Database for MySQL 服务中的副本。 

本文假定你至少已经有一些 MySQL 服务器和数据库的使用经验。

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>创建将要用作副本的 MySQL 服务器

1. 创建新的 Azure Database for MySQL 服务器

创建新的 MySQL 服务器（例如 “replica.mysql.database.chinacloudapi.cn”）。 请参阅[使用 Azure 门户创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md)，了解如何创建服务器。 在数据传入复制中，此服务器为“副本”服务器。

   > [!IMPORTANT]
   > 必须在“常规用途”或“内存优化”定价层中创建 Azure Database for MySQL 服务器。
   > 

2. 创建相同的用户帐户和相应的特权

   用户帐户不会从主服务器复制到副本服务器。 如果打算为用户提供访问副本服务器的权限，则需在这个新创建的 Azure Database for MySQL 服务器上手动创建所有帐户和相应的特权。

## <a name="configure-the-master-server"></a>配置主服务器
以下步骤准备并配置本地或虚拟机中托管的 MySQL 服务器或其他云提供程序托管的数据库服务，以便向内复制数据。 此服务器是“数据传入复制”中的“主”服务器。 

1. 启用二进制日志记录

   运行以下命令以检查是否已在主服务器上启用了二进制日志记录： 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   如果返回了包含值“ON”的变量 [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin)，则表示已在服务器上启用了二进制日志记录。 

   如果返回了包含值“OFF”的 `log_bin`，请将 my.cnf 文件编辑为 `log_bin=ON` 以启用二进制日志记录，并重启服务器，使更改生效。

2. 主服务器设置

   “数据传入复制”要求参数 `lower_case_table_names` 在主服务器与副本服务器之间保持一致。 在 Azure Database for MySQL 中，该参数默认为 1。 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. 创建新的复制角色并设置权限

   在主服务器上创建一个配置有复制特权的用户帐户。 这可以通过 SQL 命令或 MySQL Workbench 之类的工具来完成。 考虑是否打算使用 SSL 进行复制，因为这需要在创建用户时指定。 请参阅 MySQL 文档以了解如何在主服务器上[添加用户帐户](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html)。 

   在以下命令中，创建的新复制角色能够从任何计算机访问主服务器，而不仅仅是从本身托管主服务器的计算机访问主服务器。 这可以通过在创建用户的命令中指定“syncuser@'%'”来完成。 请参阅 MySQL 文档，详细了解如何[指定帐户名称](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)。

   **SQL 命令**

   *使用 SSL 复制*

   若要对所有用户连接要求使用 SSL，请使用以下命令创建用户： 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *不使用 SSL 复制*

   若不要求对所有连接使用 SSL，请使用以下命令创建用户：

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   若要在 MySQL Workbench 中创建复制角色，请从“管理”面板打开“用户和特权”面板。   然后单击“添加帐户”  。 
 
   ![用户和特权](./media/howto-data-in-replication/users_privileges.png)

   在“登录名”字段中键入用户名。  

   ![同步用户](./media/howto-data-in-replication/syncuser.png)
 
   单击“管理角色”面板，然后从“全局特权”列表中选择“复制从属角色”。    然后单击“应用”  ，创建复制角色。

   ![复制从属实例](./media/howto-data-in-replication/replicationslave.png)


4. 将主服务器设置为只读模式

   在开始转储数据库之前，需将服务器置于只读模式。 在只读模式下，主服务器无法处理任何写入事务。 评估对业务的影响，根据需要在非高峰时间计划只读窗口。

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. 获取二进制日志文件名和偏移量

   运行 [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) 命令，确定当前的二进制日志文件名和偏移量。
    
   ```sql
   show master status;
   ```
   结果应如下所示。 请务必记下二进制文件名，因为需要在后续步骤中用到。

   ![主机状态结果](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>转储并还原主服务器

1. 从主服务器转储所有数据库

   可以使用 mysqldump 从主服务器转储数据库。 有关详细信息，请参阅[转储和还原](concepts-migrate-dump-restore.md)。 不需转储 MySQL 库和测试库。

2. 将主服务器设置为读/写模式

   转储数据库后，将 MySQL 主服务器改回读/写模式。

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. 将转储文件还原到新服务器

   将转储文件还原到在 Azure Database for MySQL 服务中创建的服务器。 请参阅[转储和还原](concepts-migrate-dump-restore.md)，了解如何将转储文件还原到 MySQL 服务器。 如果转储文件较大，请将它上传到副本服务器所在区域的 Azure 中的虚拟机。 将它从虚拟机还原到 Azure Database for MySQL 服务器。

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>链接主服务器和副本服务器以启动“数据传入复制”

1. 设置主服务器

   所有数据传入复制功能都是通过存储过程完成的。 可以在[数据传入复制存储过程](reference-data-in-stored-procedures.md)中找到所有过程。 这些存储过程可以在 MySQL shell 或 MySQL Workbench 中运行。 

   若要链接两个服务器并启动复制，请在 Azure DB for MySQL 服务中登录到目标副本服务器，并将外部实例设置为主服务器。 为此，可在 Azure DB for MySQL 服务器上使用 `mysql.az_replication_change_master` 存储过程。

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host：主服务器的主机名
   - master_user：主服务器的用户名
   - master_password：主服务器的密码
   - master_log_file：正在运行的 `show master status` 中的二进制日志文件名
   - master_log_pos：正在运行的 `show master status` 中的二进制日志位置
   - master_ssl_ca：CA 证书的上下文。 如果不使用 SSL，请传入空字符串。
       - 建议以变量形式传入此参数。 有关详细信息，请参阅以下示例。

> [!NOTE]
> 如果主服务器托管在 Azure VM 中，请将“允许访问 Azure 服务”设置为“启用”，以允许主服务器和副本服务器相互通信。 从“连接安全性”选项可更改此设置  。 请参阅[使用门户管理防火墙规则](howto-manage-firewall-using-portal.md)获取详细信息。

   **示例**

   *使用 SSL 复制*

   运行以下 MySQL 命令创建变量 `@cert`： 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   在域“companya.com”中托管的主服务器与 Azure Database for MySQL 中托管的副本服务器之间设置了使用 SSL 进行复制。 将在副本上运行此存储过程。 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *不使用 SSL 复制*

   在域“companya.com”中托管的主服务器与 Azure Database for MySQL 中托管的副本服务器之间设置了不使用 SSL 进行复制。 将在副本上运行此存储过程。

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

2. 启动复制

   调用 `mysql.az_replication_start` 存储过程来启动复制。

   ```sql
   CALL mysql.az_replication_start;
   ```

3. 检查复制状态

   在副本服务器上调用 [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) 命令查看复制状态。
    
   ```sql
   show slave status;
   ```

   如果 `Slave_IO_Running` 和 `Slave_SQL_Running` 状态为“yes”，并且 `Seconds_Behind_Master` 的值为“0”，则表示复制正常运行。 `Seconds_Behind_Master` 指示副本的陈旧状态。 如果其值不为“0”，则表示副本正在处理更新。 

## <a name="other-stored-procedures"></a>其他存储过程

### <a name="stop-replication"></a>停止复制

若要停止主服务器与副本服务器之间的复制，请使用以下存储过程：

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>删除复制关系

若要删除主服务器与副本服务器之间的关系，请使用以下存储过程：

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>跳过复制错误

若要跳过复制错误并允许复制继续，请使用以下存储过程：
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>后续步骤
- 详细了解 Azure Database for MySQL 的[向内复制数据](concepts-data-in-replication.md)。 