---
title: Diferenças de T-SQL de instância gerenciada
description: Este artigo aborda as diferenças do T-SQL entre uma instância gerenciada no Banco de Dados SQL do Azure e no SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova, danil
ms.date: 03/11/2020
ms.custom: seoapril2019
ms.openlocfilehash: e01c61ca4f415ffbb46c86034d4b7441bc2617d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80365490"
---
# <a name="managed-instance-t-sql-differences-and-limitations"></a>Diferenças e limitações do T-SQL da instância gerenciada

Este artigo resume e explica as diferenças na sintaxe e no comportamento entre a instância gerenciada do banco de dados SQL do Azure e o SQL Server local Mecanismo de Banco de Dados. A opção de implantação de instância gerenciada fornece alta compatibilidade com o Mecanismo de Banco de Dados do SQL Server local. A maioria dos recursos do mecanismo de banco de dados do SQL Server é compatível com uma instância gerenciada.

![Migração](./media/sql-database-managed-instance/migration.png)

Há algumas limitações de PaaS que são introduzidas em Instância Gerenciada e algumas alterações de comportamento em comparação com SQL Server. As diferenças são divididas nas seguintes categorias:<a name="Differences"></a>

- A [disponibilidade](#availability) inclui as diferenças em [Always on grupos de disponibilidade](#always-on-availability-groups) e [backups](#backup).
- A [segurança](#security) inclui as diferenças de [auditoria](#auditing), [certificados](#certificates), [credenciais](#credential), [provedores criptográficos](#cryptographic-providers), [logons e usuários](#logins-and-users)e a [chave de serviço e a chave mestra de serviço](#service-key-and-service-master-key).
- A [configuração](#configuration) inclui as diferenças na [extensão do pool de buffers](#buffer-pool-extension), [agrupamento](#collation), [níveis de compatibilidade](#compatibility-levels), [espelhamento de banco de dados](#database-mirroring), opções de banco de [dados](#database-options), [SQL Server Agent](#sql-server-agent)e [Opções de tabela](#tables).
- As [funcionalidades](#functionalities) incluem [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [transações distribuídas](#distributed-transactions), [eventos estendidos](#extended-events), [bibliotecas externas](#external-libraries), [FileStream e filetable](#filestream-and-filetable), [pesquisa semântica de texto completo](#full-text-semantic-search), [servidores vinculados](#linked-servers), [polybase](#polybase), [replicação](#replication), [restauração](#restore-statement), [Service Broker](#service-broker), [procedimentos armazenados, funções e gatilhos](#stored-procedures-functions-and-triggers).
- [Configurações de ambiente](#Environment) , como VNets e configurações de sub-rede.

A maioria desses recursos são restrições de arquitetura e representam recursos de serviço.

Os problemas temporários conhecidos que são descobertos na instância gerenciada e que serão resolvidos no futuro são descritos na [página notas de versão](sql-database-release-notes.md).

## <a name="availability"></a>Disponibilidade

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Grupos de Disponibilidade AlwaysOn

A [alta disponibilidade](sql-database-high-availability.md) é incorporada à instância gerenciada e não pode ser controlada por usuários. As instruções a seguir não têm suporte:

- [CRIAR ENDPOINT … PARA DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [CRIAR GRUPO DE DISPONIBILIDADE](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTERAR GRUPO DE DISPONIBILIDADE](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DESCARTAR GRUPO DE DISPONIBILIDADE](/sql/t-sql/statements/drop-availability-group-transact-sql)
- A cláusula [set HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) da instrução [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

Instâncias gerenciadas têm backups automáticos, para que os `COPY_ONLY` usuários possam criar backups de banco de dados completos. Não há suporte para backups diferenciais, de log e de instantâneo de arquivo.

- Com uma instância gerenciada, você pode fazer backup de um banco de dados de instância somente para uma conta de armazenamento de BLOBs do Azure:
  - Apenas `BACKUP TO URL` tem suporte.
  - `FILE`, `TAPE`e os dispositivos de backup não têm suporte.
- Há suporte para a `WITH` maioria das opções gerais.
  - `COPY_ONLY`é obrigatório.
  - Não há suporte para `FILE_SNAPSHOT`.
  - Opções de fita `REWIND`: `NOREWIND`, `UNLOAD`, e `NOUNLOAD` não têm suporte.
  - Opções específicas de log: `NORECOVERY`, `STANDBY`e `NO_TRUNCATE` não têm suporte.

 Limitações: 

- Com uma instância gerenciada, você pode fazer backup de um banco de dados de instância em um backup com até 32 faixas, o que é suficiente para bancos de dados de até 4 TB se a compactação de backup for usada.
- Não é possível `BACKUP DATABASE ... WITH COPY_ONLY` executar o em um banco de dados criptografado com o TDE (Transparent Data Encryption gerenciamento de serviços gerenciados). O TDE gerenciado por serviço força os backups a serem criptografados com uma chave TDE interna. A chave não pode ser exportada, portanto, não é possível restaurar o backup. Use backups automáticos e restauração pontual ou use o [TDE (BYOK) gerenciado pelo cliente](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key) em vez disso. Você também pode desabilitar a criptografia no banco de dados.
- O tamanho máximo de distribuição de backup usando o `BACKUP` comando em uma instância gerenciada é 195 GB, que é o tamanho máximo do blob. Aumente o número de faixas no comando de backup para reduzir o tamanho da faixa individual e permaneça dentro desse limite.

    > [!TIP]
    > Para solucionar essa limitação, ao fazer backup de um banco de dados de qualquer SQL Server em um ambiente local ou em uma máquina virtual, você pode:
    >
    > - Faça backup em `DISK` em vez de fazer backup para `URL`.
    > - Carregue os arquivos de backup no armazenamento de BLOBs.
    > - Restaure na instância gerenciada.
    >
    > O `Restore` comando em uma instância gerenciada dá suporte a tamanhos de blob maiores nos arquivos de backup porque um tipo de blob diferente é usado para o armazenamento dos arquivos de backup carregados.

Para obter informações sobre backups usando o T-SQL, consulte [BACKUP](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Segurança

### <a name="auditing"></a>Auditoria

As principais diferenças entre a auditoria em bancos de dados no Banco de Dados SQL do Azure e em bancos de dados no SQL Server são:

- Com a opção de implantação de instância gerenciada no banco de dados SQL do Azure, a auditoria funciona no nível do servidor. Os `.xel` arquivos de log são armazenados no armazenamento de BLOBs do Azure.
- Com as opções de implantação de banco de dados individual e pool elástico no Banco de Dados SQL do Azure, a auditoria funciona no nível do banco de dados.
- Em SQL Server máquinas virtuais ou locais, a auditoria funciona no nível do servidor. Os eventos são armazenados no sistema de arquivos ou nos logs de eventos do Windows.
 
A auditoria XEvent na Instância Gerenciada oferece suporte a destinos de armazenamento de blobs do Azure. Não há suporte para logs de arquivo e do Windows.

As principais diferenças na sintaxe `CREATE AUDIT` para a auditoria do armazenamento de Blobs do Azure são:

- É fornecida uma `TO URL` nova sintaxe que você pode usar para especificar a URL do contêiner de armazenamento de BLOBs do `.xel` Azure em que os arquivos são colocados.
- A sintaxe `TO FILE` não tem suporte porque uma instância gerenciada não pode acessar compartilhamentos de arquivos do Windows.

Para obter mais informações, consulte: 

- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Auditoria](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificados

Uma instância gerenciada não pode acessar compartilhamentos de arquivos e pastas do Windows, portanto, as seguintes restrições se aplicam:

- `CREATE FROM` / O `BACKUP TO` arquivo não tem suporte para certificados.
- Não `CREATE` / `BACKUP` há suporte `FILE` / `ASSEMBLY` para o certificado de. Arquivos de chave privada não podem ser usados. 

Consulte [CRIAR CERTIFICADO](/sql/t-sql/statements/create-certificate-transact-sql) e [CERTIFICADO DE BACKUP](/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Solução alternativa**: em vez de criar backup de certificado e restaurar o backup, [obtenha o conteúdo binário do certificado e a chave privada, armazene-os como arquivo. SQL e crie a partir do binário](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database):

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Credencial

Somente o Azure Key Vault e identidades `SHARED ACCESS SIGNATURE` têm suporte. Não há suporte para usuários do Windows.

Consulte [CRIAR CREDENCIAL](/sql/t-sql/statements/create-credential-transact-sql) e [ALTERAR CREDENCIAL](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Provedores criptográficos

Uma instância gerenciada não pode acessar arquivos, portanto, os provedores criptográficos não podem ser criados:

- Não há suporte para `CREATE CRYPTOGRAPHIC PROVIDER`. Consulte [CRIAR PROVEDOR CRIPTOGRÁFICO](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- Não há suporte para `ALTER CRYPTOGRAPHIC PROVIDER`. Consulte [ALTERAR PROVEDOR CRIPTOGRÁFICO](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Logons e usuários

- Os logons do SQL criados `FROM CERTIFICATE`com `FROM ASYMMETRIC KEY`o, `FROM SID` o e têm suporte. Consulte [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql).
- As entidades de segurança (logons) do servidor do Azure Active Directory (Azure AD) criadas com a sintaxe [Create login](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) ou [Create User from login [Azure ad login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) têm suporte. Esses logons são criados no nível do servidor.

    A instância gerenciada dá suporte a entidades de banco de `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`dados do Azure AD com a sintaxe. Esse recurso também é conhecido como usuários de banco de dados independente do Azure AD.

- Não há suporte para logons `CREATE LOGIN ... FROM WINDOWS` do Windows criados com a sintaxe. Use logons e usuário do Microsoft Azure Active Directory.
- O usuário do Azure AD que criou a instância tem [privilégios de administrador irrestrito](sql-database-manage-logins.md).
- Usuários de nível de banco de dados não administrador do Azure AD podem ser criados `CREATE USER ... FROM EXTERNAL PROVIDER` usando a sintaxe. Consulte [criar usuário... DO provedor externo](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
- As entidades de segurança do servidor do Azure AD (logons) dão suporte a recursos SQL somente em uma instância gerenciada. Recursos que exigem interação entre instâncias, independentemente de estarem dentro do mesmo locatário do Azure AD ou locatários diferentes, não têm suporte para usuários do Azure AD. Exemplos de recursos desse tipo são:

  - Replicação transacional do SQL.
  - Servidor de link.

- Não há suporte para logons do Azure AD mapeados para um grupo do Azure AD como proprietário do banco de dados.
- Há suporte para a representação de entidades de segurança no nível de servidor do Azure AD usando outras entidades do Azure AD, como a cláusula [Execute as](/sql/t-sql/statements/execute-as-transact-sql) . AS limitações de executar como são:

  - Não há suporte para EXECUTE AS USER para usuários do Azure AD quando o nome difere do nome de logon. Um exemplo é quando o usuário é criado por meio da sintaxe CREATE USER [myAadUser] do LOGINjohn@contoso.com[] e a representação é tentada por meio de exec como user = _myAadUser_. Quando você cria um **usuário** de uma entidade de segurança de servidor do Azure AD (logon), especifique o user_name como o mesmo Login_name do **logon**.
  - Somente as entidades de nível de SQL Server (logons) que fazem parte da `sysadmin` função podem executar as seguintes operações que visam entidades do Azure AD:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- A exportação/importação de banco de dados usando arquivos bacpac tem suporte para usuários do Azure AD na instância gerenciada usando o [SSMS v 18.4 ou posterior](/sql/ssms/download-sql-server-management-studio-ssms)ou [SqlPackage. exe](/sql/tools/sqlpackage-download).
  - As configurações a seguir têm suporte usando o arquivo bacpac do banco de dados: 
    - Exportar/importar um banco de dados entre diferentes instâncias de gerenciamento dentro do mesmo domínio do Azure AD.
    - Exporte um banco de dados da instância gerenciada e importe para o banco de dados SQL no mesmo domínio do Azure AD. 
    - Exportar um banco de dados do banco de dados SQL e importar para a instância gerenciada dentro do mesmo domínio do Azure AD.
    - Exporte um banco de dados da instância gerenciada e importe para SQL Server (versão 2012 ou posterior).
      - Nessa configuração, todos os usuários do Azure AD são criados como entidades de segurança do banco de dados SQL (usuários) sem logons. O tipo de usuário é listado como SQL (visível como SQL_USER em sys. database_principals). Suas permissões e funções permanecem no SQL Server metadados do banco de dados e podem ser usadas para representação. No entanto, eles não podem ser usados para acessar e fazer logon no SQL Server usando suas credenciais.

- Somente o logon da entidade de segurança no nível do servidor, que é criado pelo processo de provisionamento de instância gerenciada, os membros das funções `securityadmin` de `sysadmin`servidor, como ou ou outros logons com a permissão ALTER ANY login no nível do servidor, podem criar entidades de segurança do Azure Ad Server (logons) no banco de dados mestre para a instância gerenciada.
- Se o logon for uma entidade de segurança SQL, somente os logons que fizerem parte da `sysadmin` função poderão usar o comando Create para criar logons para uma conta do Azure AD.
- O logon do Azure AD deve ser um membro de um Azure AD no mesmo diretório usado para a instância gerenciada do banco de dados SQL do Azure.
- As entidades de segurança do servidor do Azure AD (logons) são visíveis no Pesquisador de objetos, começando com SQL Server Management Studio 18,0 Preview 5.
- É permitida a sobreposição de entidades de segurança do servidor (logons) do Azure AD com uma conta de administrador do Azure AD. As entidades de segurança do servidor do Azure AD (logons) têm precedência sobre o administrador do Azure AD quando você resolve a entidade de segurança e aplica permissões à instância gerenciada.
- Durante a autenticação, a sequência a seguir é aplicada para resolver a entidade de autenticação:

    1. Se a conta do Azure AD existir como mapeada diretamente para a entidade de segurança do servidor do Azure AD (logon), que está presente em sys. server_principals como tipo "E" conceder acesso e aplicar permissões da entidade de segurança do servidor do Azure AD (logon).
    2. Se a conta do Azure AD for um membro de um grupo do Azure AD que é mapeado para a entidade de segurança de servidor do Azure AD (logon), que está presente em sys. server_principals como tipo "X", conceder acesso e aplicar permissões do logon do grupo do Azure AD.
    3. Se a conta do Azure AD for um administrador especial configurado pelo portal do Azure AD para a instância gerenciada, que não existe em exibições do sistema de instância gerenciada, aplique permissões fixas especiais do administrador do Azure AD para a instância gerenciada (modo herdado).
    4. Se a conta do Azure AD existir como mapeada diretamente para um usuário do Azure AD em um banco de dados, que está presente em sys. database_principals como tipo "E" conceder acesso e aplicar permissões do usuário de banco de dados do Azure AD.
    5. Se a conta do Azure AD for um membro de um grupo do Azure AD que é mapeado para um usuário do Azure AD em um banco de dados, que está presente em sys. database_principals como tipo "X", conceder acesso e aplicar permissões do logon do grupo do Azure AD.
    6. Se houver um logon do Azure AD mapeado para uma conta de usuário do Azure AD ou uma conta de grupo do Azure AD, que seja resolvida para o usuário que está Autenticando, todas as permissões desse logon do Azure AD serão aplicadas.

### <a name="service-key-and-service-master-key"></a>Chave de serviço e chave mestra de serviço

- O [backup da chave mestra](/sql/t-sql/statements/backup-master-key-transact-sql) não tem suporte (gerenciado pelo serviço do banco de dados SQL).
- Não há suporte para a [restauração da chave mestra](/sql/t-sql/statements/restore-master-key-transact-sql) (gerenciada pelo serviço do banco de dados SQL).
- O [backup da chave mestra de serviço](/sql/t-sql/statements/backup-service-master-key-transact-sql) não tem suporte (gerenciado pelo serviço de banco de dados SQL).
- Não há suporte para a [restauração da chave mestra de serviço](/sql/t-sql/statements/restore-service-master-key-transact-sql) (gerenciada pelo serviço do banco de dados SQL).

## <a name="configuration"></a>Configuração

### <a name="buffer-pool-extension"></a>Extensão do pool de buffers

- Não há suporte para a [extensão do pool de buffers](/sql/database-engine/configure-windows/buffer-pool-extension) .
- Não há suporte para `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`. Consulte [ALTERAR CONFIGURAÇÃO DO SERVIDOR](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Collation

A ordenação de instância padrão é `SQL_Latin1_General_CP1_CI_AS` e pode ser especificada como um parâmetro de criação. Consulte [Ordenações](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Níveis de compatibilidade

- Os níveis de compatibilidade com suporte são 100, 110, 120, 130, 140 e 150.
- Não há suporte para níveis de compatibilidade abaixo de 100.
- O nível de compatibilidade padrão para novos bancos de dados é 140. Para bancos de dados restaurados, o nível de compatibilidade permanece inalterado se foi 100 e superior.

Consulte [ALTERAR Nível de Compatibilidade do BANCO DE DADOS](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Espelhamento de banco de dados

Não há suporte para espelhamento de banco de dados.

- As opções `ALTER DATABASE SET PARTNER` e `SET WITNESS` não são suportadas.
- Não há suporte para `CREATE ENDPOINT … FOR DATABASE_MIRRORING`.

Para obter mais informações, consulte [ALTER DATABASE SET PARTNER e SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) e [CREATE ENDPOINT... FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Opções de banco de dados

- Não há suporte para vários arquivos de log.
- Não há suporte para objetos na memória na camada de serviço de Uso Geral. 
- Há um limite de 280 arquivos por Uso Geral instância, o que implica um máximo de 280 arquivos por banco de dados. Os arquivos de dados e de log na camada de Uso Geral são contados em direção a esse limite. [A camada de comercialmente crítico dá suporte a 32.767 arquivos por banco de dados](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- O banco de dados não pode conter grupos de fileque contenham dado FileStream. A restauração falhará se. `FILESTREAM` bak contiver dados. 
- Cada arquivo é colocado no Armazenamento de Blobs do Azure. A E/S e a taxa de transferência por arquivo dependem do tamanho de cada arquivo individual.

#### <a name="create-database-statement"></a>Instrução CREATE DATABASE

As seguintes limitações se aplicam a `CREATE DATABASE`:

- Arquivos e grupos de arquivos não podem ser definidos. 
- Não `CONTAINMENT` há suporte para a opção. 
- `WITH`Não há suporte para as opções. 
   > [!TIP]
   > Como alternativa, use `ALTER DATABASE` depois `CREATE DATABASE` para definir opções de banco de dados para adicionar arquivos ou para definir a contenção. 

- Não `FOR ATTACH` há suporte para a opção.
- Não `AS SNAPSHOT OF` há suporte para a opção.

Para saber mais, confira [CRIAR BANCO DE DADOS](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>instrução ALTER DATABASE

Algumas propriedades de arquivo não podem ser definidas ou alteradas:

- Um caminho de arquivo não pode ser especificado `ALTER DATABASE ADD FILE (FILENAME='path')` na instrução t-SQL. Remova `FILENAME` do script porque uma instância gerenciada coloca os arquivos automaticamente. 
- Um nome de arquivo não pode ser alterado usando `ALTER DATABASE` a instrução.

As opções a seguir são definidas por padrão e não podem ser alteradas:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

As seguintes opções não podem ser modificadas:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Para saber mais, confira [ALTERAR BANCO DE DADOS](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- Não há suporte para habilitar e desabilitar SQL Server Agent atualmente na instância gerenciada. O SQL Agent sempre está em execução.
- SQL Server Agent configurações são somente leitura. O procedimento `sp_set_agent_properties` não tem suporte na instância gerenciada. 
- Trabalhos
  - As etapas de trabalho T-SQL têm suporte.
  - Os trabalhos de replicação a seguir têm suporte:
    - Leitor do log de transações
    - Instantâneo
    - Distribuidor
  - Há suporte para as etapas de trabalho do SSIS.
  - Atualmente, não há suporte para outros tipos de etapas de trabalho:
    - Não há suporte para a etapa de trabalho de replicação de mesclagem. 
    - Não há suporte para leitor de fila. 
    - Ainda não há suporte para o Shell de comando.
  - As instâncias gerenciadas não podem acessar recursos externos, por exemplo, compartilhamentos de rede via Robocopy. 
  - Não há suporte para SQL Server Analysis Services.
- Há suporte parcial para notificações.
- Há suporte para notificação por email, embora exija que você configure um perfil de Database Mail. SQL Server Agent pode usar apenas um perfil de Database Mail e deve ser chamado `AzureManagedInstance_dbmail_profile`. 
  - Não há suporte para o Pager.
  - Não há suporte para o NetSend.
  - Ainda não há suporte para alertas.
  - Não há suporte para proxies.
- O log de eventos não tem suporte.

Os seguintes recursos do SQL Agent atualmente não têm suporte:

- Proxies
- Agendando trabalhos em uma CPU ociosa
- Habilitando ou desabilitando um agente
- Alertas

Para obter informações sobre o SQL Server Agent, consulte [SQL Server Agent](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabelas

Os seguintes tipos de tabela não têm suporte:

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [Tabela externa](/sql/t-sql/statements/create-external-table-transact-sql) (polybase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (sem suporte apenas na camada uso geral)

Para obter informações sobre como criar e alterar tabelas, consulte [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) e [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funcionalidades

### <a name="bulk-insert--openrowset"></a>Inserção em massa/OPENROWSET

Uma instância gerenciada não pode acessar compartilhamentos de arquivos e pastas do Windows, portanto, os arquivos devem ser importados do armazenamento de BLOBs do Azure:

- `DATASOURCE`é necessário no `BULK INSERT` comando enquanto você importa arquivos do armazenamento de BLOBs do Azure. Consulte [INSERÇÃO EM MASSA](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE`é necessário na `OPENROWSET` função quando você lê o conteúdo de um arquivo do armazenamento de BLOBs do Azure. Consulte [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET`pode ser usado para ler dados de outros bancos de dado SQL do Azure, instâncias gerenciadas ou instâncias de SQL Server. Não há suporte para outras fontes, como bancos de dados Oracle ou arquivos do Excel.

### <a name="clr"></a>CLR

Uma instância gerenciada não pode acessar compartilhamentos de arquivos e pastas do Windows, portanto, as seguintes restrições se aplicam:

- Apenas `CREATE ASSEMBLY FROM BINARY` tem suporte. Confira [criar Assem clicando do binário](/sql/t-sql/statements/create-assembly-transact-sql). 
- Não há suporte para `CREATE ASSEMBLY FROM FILE`. Consulte [CRIAR ASSEMBLY A PARTIR DE ARQUIVO](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` não pode referenciar arquivos. Consulte [ALTERAR ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Database Mail (db_mail)
 - `sp_send_dbmail`Não é possível enviar @file_attachments anexos usando o parâmetro. O sistema de arquivos local e os compartilhamentos externos ou o armazenamento de BLOBs do Azure não são acessíveis a partir desse procedimento.
 - Consulte os problemas conhecidos relacionados ao `@query` parâmetro e à autenticação.
 
### <a name="dbcc"></a>DBCC

As instruções DBCC não documentadas que estão habilitadas no SQL Server não têm suporte em instâncias gerenciadas.

- Há suporte apenas para um número limitado de sinalizadores de rastreamento globais. Não há suporte `Trace flags` para o nível de sessão. Consulte [sinalizadores de rastreamento](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) e [DBCC tracen](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) funcionam com o número limitado de sinalizadores de rastreamento globais.
- [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) com opções REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST e REPAIR_REBUILD não podem ser usados porque o banco de dados não `SINGLE_USER` pode ser definido no modo-consulte [diferenças de ALTER DATABASE](#alter-database-statement). As possíveis corrupções de banco de dados são tratadas pela equipe de suporte do Azure. Contate o suporte do Azure se você estiver percebendo corrupção de banco de dados que deve ser corrigido.

### <a name="distributed-transactions"></a>Transações distribuídas

Atualmente, não há suporte para transações de MSDTC e [elástico](sql-database-elastic-transactions-overview.md) em instâncias gerenciadas.

### <a name="extended-events"></a>Eventos estendidos

Não há suporte para alguns destinos específicos do Windows para eventos estendidos (XEvents):

- Não `etw_classic_sync` há suporte para o destino. Armazene `.xel` arquivos no armazenamento de BLOBs do Azure. Consulte [etw_classic_sync target](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Não `event_file` há suporte para o destino. Armazene `.xel` arquivos no armazenamento de BLOBs do Azure. Consulte [event_file target](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Bibliotecas externas

R e Python no banco de dados, ainda não há suporte para bibliotecas externas. Consulte [Serviços de Machine Learning do SQL Server](/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>Fluxo de arquivos e FileTable

- Não há suporte para dados FILESTREAM.
- O banco de dados não pode conter grupos `FILESTREAM` de FileData.
- Não há suporte para `FILETABLE`.
- As tabelas não podem ter tipos `FILESTREAM`.
- Não há suporte para as seguintes funções:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Para obter mais informações, consulte [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) e [FileTables](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Pesquisa semântica de texto completo

Não há suporte para [Pesquisa semântica](/sql/relational-databases/search/semantic-search-sql-server).

### <a name="linked-servers"></a>Servidores vinculados

Os servidores vinculados em instâncias gerenciadas dão suporte a um número limitado de destinos:

- Os destinos com suporte são instâncias gerenciadas, bancos de dados individuais e instâncias de SQL Server. 
- Os servidores vinculados não dão suporte a transações graváveis distribuídas (MS DTC).
- Os destinos que não têm suporte são arquivos, Analysis Services e outros RDBMS. Tente usar a importação de CSV nativo do armazenamento de BLOBs do Azure usando `BULK INSERT` ou `OPENROWSET` como uma alternativa para a importação de arquivo.

Operações

- Não há suporte para transações de gravação entre instâncias.
- `sp_dropserver` é compatível com o descarte um servidor vinculado. Consulte [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- A `OPENROWSET` função pode ser usada para executar consultas somente em instâncias de SQL Server. Eles podem ser gerenciados, localmente ou em máquinas virtuais. Consulte [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- A `OPENDATASOURCE` função pode ser usada para executar consultas somente em instâncias de SQL Server. Eles podem ser gerenciados, localmente ou em máquinas virtuais. Somente os `SQLNCLI`valores `SQLNCLI11`, e `SQLOLEDB` têm suporte como um provedor. Um exemplo é `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Consulte [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- Os servidores vinculados não podem ser usados para ler arquivos (Excel, CSV) dos compartilhamentos de rede. Tente usar [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) ou [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) que leia arquivos CSV do armazenamento de BLOBs do Azure. Acompanhar essas solicitações no [item de comentário de instância gerenciada](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

Não há suporte para tabelas externas que fazem referência aos arquivos no HDFS ou no armazenamento de BLOBs do Azure. Para obter informações sobre o polybase, consulte [polybase](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replicação

- Há suporte para os tipos de replicação de instantâneo e bidirecional. Não há suporte para replicação de mesclagem, replicação ponto a ponto e assinaturas atualizáveis.
- A [replicação transacional](sql-database-managed-instance-transactional-replication.md) está disponível para visualização pública na instância gerenciada com algumas restrições:
    - Todos os tipos de participantes de replicação (editor, distribuidor, assinante de pull e assinante push) podem ser colocados em instâncias gerenciadas, mas o Publicador e o distribuidor devem estar tanto na nuvem quanto no local.
    - As instâncias gerenciadas podem se comunicar com as versões recentes do SQL Server. Consulte a [matriz de versões com suporte](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems) para obter mais informações.
    - A replicação transacional tem alguns [requisitos de rede adicionais](sql-database-managed-instance-transactional-replication.md#requirements).

Para obter mais informações sobre como configurar a replicação transacional, consulte os seguintes tutoriais:
- [Replicação entre um Publicador e um assinante MI](replication-with-sql-database-managed-instance.md)
- [Replicação entre um Publicador de MI, um distribuidor de MI e um assinante de SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)

### <a name="restore-statement"></a>Instrução RESTAURAR 

- Sintaxe com suporte:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Sintaxe sem suporte:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Origem: 
  - `FROM URL`(Armazenamento de BLOBs do Azure) é a única opção com suporte.
  - Não há suporte para `FROM DISK`/`TAPE`/dispositivo de backup.
  - Conjuntos de backup não são compatíveis.
- `WITH`Não há suporte para opções, como `DIFFERENTIAL` não `STATS`ou.
- `ASYNC RESTORE`: A restauração continua mesmo que a conexão do cliente seja interrompida. Se a conexão for descartada, você poderá `sys.dm_operation_status` verificar a exibição do status de uma operação de restauração e para criar e remover um banco de dados. Consulte [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

As opções de banco de dados a seguir são definidas ou substituídas e não podem ser alteradas posteriormente: 

- `NEW_BROKER`Se o agente não estiver habilitado no arquivo. bak. 
- `ENABLE_BROKER`Se o agente não estiver habilitado no arquivo. bak. 
- `AUTO_CLOSE=OFF`se um banco de dados no arquivo. bak `AUTO_CLOSE=ON`tiver. 
- `RECOVERY FULL`se um banco de dados no arquivo. bak `SIMPLE` tiver `BULK_LOGGED` o modo de recuperação ou.
- Um grupo de arquivos com otimização de memória é adicionado e chamado de XTP se ele não estava no arquivo Source. bak. 
- Qualquer grupo de arquivos com otimização de memória existente é renomeado para XTP. 
- `SINGLE_USER`as `RESTRICTED_USER` opções e são convertidas em `MULTI_USER`.

 Limitações: 

- Os backups dos bancos de dados corrompidos podem ser restaurados dependendo do tipo de corrupção, mas os backups automatizados não serão feitos até que a corrupção seja corrigida. Certifique-se de executar `DBCC CHECKDB` na instância de origem e use o `WITH CHECKSUM` backup para evitar esse problema.
- A restauração `.BAK` do arquivo de um banco de dados que contém qualquer limitação descrita neste documento (por `FILESTREAM` exemplo `FILETABLE` , ou objetos) não pode ser restaurada em instância gerenciada.
- `.BAK`os arquivos que contêm vários conjuntos de backup não podem ser restaurados. 
- `.BAK`arquivos que contêm vários arquivos de log não podem ser restaurados.
- Os backups que contêm bancos de dados maiores que 8 TB, objetos OLTP na memória ativas ou o número de arquivos que excedem 280 arquivos por instância não podem ser restaurados em uma instância de Uso Geral. 
- Os backups que contêm bancos de dados maiores que 4 TB ou objetos OLTP na memória com o tamanho total maior do que o tamanho descrito nos [limites de recursos](sql-database-managed-instance-resource-limits.md) não podem ser restaurados na instância comercialmente crítico.
Para obter informações sobre instruções RESTORE, consulte [instruções RESTORE](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > As mesmas limitações se aplicam à operação de restauração pontual interna. Por exemplo, Uso Geral banco de dados maior que 4 TB não pode ser restaurado na instância Comercialmente Crítico. Comercialmente Crítico banco de dados com arquivos OLTP na memória ou mais de 280 arquivos não podem ser restaurados na instância Uso Geral.

### <a name="service-broker"></a>Service Broker

Não há suporte para agente de serviços entre instâncias:

- `sys.routes`: Como pré-requisito, você deve selecionar o endereço de sys. routes. O endereço deve ser LOCAL em cada rota. Confira [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Você não pode `CREATE ROUTE` usar `ADDRESS` com outros `LOCAL`. Confira [CREATE ROUTE](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Você não pode `ALTER ROUTE` usar `ADDRESS` com outros `LOCAL`. Confira [ALTER ROUTE](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Procedimentos armazenados, funções e gatilhos

- `NATIVE_COMPILATION`Não tem suporte na camada de Uso Geral.
- Não há suporte para as seguintes opções [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql): 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- Não há suporte para `sp_execute_external_scripts`. Consulte [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- Não há suporte para `xp_cmdshell`. Consulte [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures`Não tem suporte, que `sp_addextendedproc`  inclui `sp_dropextendedproc`e. Consulte [procedimentos armazenados estendidos](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- Não há suporte para `sp_attach_db`, `sp_attach_single_file_db` e `sp_detach_db`. Consulte [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) e [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Funções e variáveis do sistema

As seguintes variáveis, funções e exibições retornam resultados diferentes:

- `SERVERPROPERTY('EngineEdition')`Retorna o valor 8. Essa propriedade identifica exclusivamente uma instância gerenciada. Consulte [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')`Retorna NULL porque o conceito de instância existente para SQL Server não se aplica a uma instância gerenciada. Consulte [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME`Retorna um nome de DNS "conectável" completo, por exemplo, my-managed-instance.wcus17662feb9ce98.database.windows.net. Consulte [@@SERVERNAME](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS`Retorna um nome de DNS "conectável" completo, como `myinstance.domain.database.windows.net` para as propriedades "Name" e "data_source". Consulte [SYS.SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME`Retorna NULL porque o conceito de serviço existente para SQL Server não se aplica a uma instância gerenciada. Consulte [@@SERVICENAME](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` é compatível. Retornará NULL se o logon do Azure AD não estiver em sys. syslogins. Consulte [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- Não há suporte para `SUSER_SID`. Os dados errados são retornados, o que é um problema temporário conhecido. Consulte [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Restrições de ambiente

### <a name="subnet"></a>Sub-rede
-  Você não pode inserir outros recursos (por exemplo, máquinas virtuais) na sub-rede em que você implantou sua instância gerenciada. Implante esses recursos usando uma sub-rede diferente.
- A sub-rede deve ter um número suficiente de [endereços IP](sql-database-managed-instance-connectivity-architecture.md#network-requirements)disponíveis. O mínimo é 16, enquanto a recomendação é ter pelo menos 32 endereços IP na sub-rede.
- [Os pontos de extremidade de serviço não podem ser associados à sub-rede da instância gerenciada](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Verifique se a opção pontos de extremidade de serviço está desabilitada quando você cria a rede virtual.
- O número de vCores e tipos de instâncias que você pode implantar em uma região têm algumas [restrições e limites](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
- Há algumas [regras de segurança que devem ser aplicadas na sub-rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>VNET
- A VNet pode ser implantada usando modelo de recurso-modelo clássico para VNet sem suporte.
- Depois que uma instância gerenciada é criada, não há suporte para a movimentação da instância gerenciada ou da VNet para outro grupo de recursos ou assinatura.
- Alguns serviços, como ambientes de serviço de aplicativo, aplicativos lógicos e instâncias gerenciadas (usados para replicação geográfica, replicação transacional ou por meio de servidores vinculados) não poderão acessar instâncias gerenciadas em regiões diferentes se seus VNets estiverem conectados usando o [emparelhamento global](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Você pode se conectar a esses recursos via ExpressRoute ou VNet a VNet por meio de gateways de VNet.

### <a name="tempdb"></a>TEMPDB

O tamanho máximo de arquivo `tempdb` de não pode ser maior que 24 GB por núcleo em uma camada de uso geral. O tamanho `tempdb` máximo em uma camada de comercialmente crítico é limitado pelo tamanho do armazenamento da instância. `Tempdb`o tamanho do arquivo de log é limitado a 120 GB na camada Uso Geral. Algumas consultas podem retornar um erro se precisarem de mais de 24 GB por núcleo `tempdb` no ou se produzirem mais de 120 GB de dados de log.

### <a name="msdb"></a>MSDB

Os seguintes esquemas MSDB na instância gerenciada devem ser de propriedade de suas respectivas funções predefinidas:

- Funções gerais
  - TargetServersRole
- [Funções de banco de dados fixas](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [Funções DatabaseMail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile):
  - DatabaseMailUserRole
- [Funções do Integration Services](https://docs.microsoft.com/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15):
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> Alterar os nomes de função predefinidos, nomes de esquema e proprietários de esquema por clientes afetará a operação normal do serviço. Todas as alterações feitas a elas serão revertidas para os valores predefinidos assim que forem detectadas ou na próxima atualização de serviço, na versão mais recente, para garantir a operação normal de serviço.

### <a name="error-logs"></a>Logs de erros

Uma instância gerenciada coloca informações detalhadas nos logs de erros. Há muitos eventos internos do sistema que são registrados no log de erros do. Use um procedimento personalizado para ler logs de erro que filtram algumas entradas irrelevantes. Para obter mais informações, consulte [instância gerenciada – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) ou [extensão de instância gerenciada (versão prévia)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) para Azure Data Studio.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre instâncias gerenciadas, consulte [o que é uma instância gerenciada?](sql-database-managed-instance.md)
- Para obter uma lista de recursos e comparação, consulte comparação de recursos [do banco de dados SQL do Azure](sql-database-features.md).
- Para ver atualizações de versão e o estado de problemas conhecidos, consulte [notas de versão do banco de dados SQL](sql-database-release-notes.md)
- Para obter um guia de início rápido que mostra como criar uma nova instância gerenciada, consulte [criar uma instância gerenciada](sql-database-managed-instance-get-started.md).
