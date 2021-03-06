---
title: Copiar dados em massa usando o portal do Azure
description: Saiba como usar o Azure Data Factory e atividade de cópia para copiar dados em massa de um armazenamento de dados de origem para um armazenamento de dados de destino.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/27/2020
ms.openlocfilehash: 04469fa1bd0473710d9fa0bf0190c6459f1f8a07
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81418772"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Copiar várias tabelas em massa usando o Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este tutorial demonstra como **copiar uma série de tabelas do Banco de Dados SQL do Azure para o Azure Synapse Analytics (anteriormente conhecido como SQL DW)** . Você também pode aplicar o mesmo padrão em outros cenários de cópia. Por exemplo, copiando tabelas do SQL Server/Oracle para o Banco de Dados SQL do Azure/Azure Synapse Analytics (anteriormente conhecido como SQL DW)/Blob do Azure, copiando diferentes caminhos do Blob para tabelas do Banco de Dados SQL do Azure.

> [!NOTE]
> - Se estiver se familiarizando com o Azure Data Factory, confira [Introdução ao Azure Data Factory](introduction.md).

De forma mais abrangente, este tutorial envolve as seguintes etapas:

> [!div class="checklist"]
> * Criar um data factory.
> * Criar um Banco de Dados SQL do Azure, o Azure Synapse Analytics (anteriormente conhecido como SQL DW) e os serviços vinculados do Armazenamento do Azure.
> * Criar conjuntos de dados do Banco de Dados SQL do Azure e do Azure Synapse Analytics (anteriormente conhecido como SQL DW).
> * Crie um pipeline para consultar as tabelas a serem copiadas e outro pipeline para executar a operação de cópia propriamente dita. 
> * Iniciar uma execução de pipeline.
> * Monitore as execuções de pipeline e de atividade.

Este tutorial usa o portal do Azure. Para obter informações sobre como usar outras ferramentas/SDKs para criar um data factory, consulte [Guias de início rápido](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Fluxos de trabalho completos
Nesse cenário, você tem um número de tabelas no Banco de Dados SQL do Azure que deseja copiar para o Azure Synapse Analytics (anteriormente conhecido como SQL DW). Aqui está a sequência lógica de etapas no fluxo de trabalho que ocorre em pipelines:

![Fluxo de trabalho](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* O primeiro pipeline verifica a lista de tabelas que precisam ser copiadas nos armazenamentos de dados do coletor.  Alternativamente, você pode manter uma tabela de metadados que lista todas as tabelas a serem copiadas para o armazenamento de dados de coletor. Em seguida, o pipeline dispara outro pipeline, que faz iteração por cada tabela no banco de dados e executa a operação de cópia de dados.
* O segundo pipeline realiza a cópia propriamente dita. Ele usa a lista de tabelas como um parâmetro. Para cada tabela na lista, copie a tabela específica no Banco de Dados SQL do Azure para a tabela correspondente no Azure Synapse Analytics (anteriormente conhecido como SQL DW) usando a [cópia preparada via Armazenamento de Blobs e PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter o melhor desempenho. Neste exemplo, o primeiro pipeline envia a lista de tabelas como um valor para o parâmetro. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* **Conta de Armazenamento do Azure**. A conta de Armazenamento do Azure é usada como Armazenamento de Blobs de preparo na operação de cópia em massa. 
* **Banco de dados SQL do Azure**. Este banco de dados contém os dados de origem. 
* **Azure Synapse Analytics (anteriormente conhecido como SQL DW)** . Esse data warehouse contém os dados copiados do Banco de Dados SQL. 

### <a name="prepare-sql-database-and-azure-synapse-analytics-formerly-sql-dw"></a>Preparar o Banco de Dados SQL e o Azure Synapse Analytics (anteriormente conhecido como SQL DW)

**Prepare o Banco de Dados SQL do Azure de origem**:

Crie um Banco de Dados SQL do Azure contendo dados de exemplo do Adventure Works LT, seguindo o artigo [Criar um Banco de Dados SQL do Azure](../sql-database/sql-database-get-started-portal.md). Esse tutorial copia todas as tabelas desse exemplo de banco de dados para um Azure Synapse Analytics (anteriormente conhecido como SQL DW).

**Preparar o Azure Synapse Analytics (anteriormente conhecido como SQL DW) do coletor**:

1. se você não tiver um Azure Synapse Analytics (anteriormente conhecido como SQL DW), confira o artigo [Criar um SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) para obter etapas para criar um.

1. Criar esquemas de tabela correspondentes no Azure Synapse Analytics (anteriormente conhecido como SQL DW). Você usa o Azure Data Factory para migrar/copiar dados em uma etapa posterior.

## <a name="azure-services-to-access-sql-server"></a>Permitir que os serviços do Azure acessem o SQL Server

Para o Banco de Dados SQL e o Azure Synapse Analytics (anteriormente conhecido como SQL DW), permita que os serviços do Azure acessem o SQL Server. Verifique se a configuração **Permitir acesso aos serviços e recursos do Azure para acessar este servidor** está **ATIVADA** para seu Azure SQL Server. Essa configuração permite que o serviço Data Factory leia dados do Banco de Dados SQL do Azure e grave dados no Azure Synapse Analytics (anteriormente conhecido como SQL DW). 

Para verificar e ativar essa configuração, acesse seu Azure SQL Server > Segurança > Firewalls e redes virtuais > defina a opção **Permitir que os serviços e recursos do Azure acessem este servidor** como **ATIVADA**.

## <a name="create-a-data-factory"></a>Criar uma data factory
1. Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.
1. Vá para o [Portal do Azure](https://portal.azure.com). 
1. No lado esquerdo do menu do portal do Azure, selecione **Criar um recurso** > **Análise** > **Data Factory**. 
   ![Seleção de Data Factory no painel "Novo"](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Na página **Novo data factory**, insira **ADFTutorialBulkCopyDF** como o **nome**. 
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se você se deparar com o seguinte erro para o campo nome, altere o nome do data factory (por exemplo, seunomeADFTutorialBulkCopyDF). Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
       `Data factory name "ADFTutorialBulkCopyDF" is not available`
1. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
1. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
   - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
   - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
     Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/management/overview.md).  
1. Selecione **V2** para a **versão**.
1. Selecione o **local** do data factory. Para obter uma lista de regiões do Azure no qual o Data Factory está disponível no momento, selecione as regiões que relevantes para você na página a seguir e, em seguida, expanda **Análise** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.
1. Clique em **Criar**.
1. Após a conclusão da criação, selecione **Ir para o recurso** para navegar até a página do **Data Factory**. 
   
1. Clique no bloco **Autor & Monitor** para iniciar o aplicativo IU do Data Factory em uma guia separada.
1. Na página **Introdução**, alterne para a guia **Autor** no painel esquerdo conforme mostrado na seguinte imagem:

     ![Página Introdução](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-linked-services"></a>Criar serviços vinculados
Você cria os serviços vinculados para vincular seus armazenamentos de dados e serviços de computação ao data factory. Um serviço vinculado possui as informações de conexão que o serviço do Data Factory usa para conectar-se ao armazenamento de dados no runtime. 

Neste tutorial, você vinculará o Banco de Dados SQL do Azure, o Azure Synapse Analytics (anteriormente conhecido como SQL DW) e os armazenamentos de dados do Armazenamento de Blobs do Azure ao seu data factory. O Banco de Dados SQL do Azure é o armazenamento de dados de origem. O Azure Synapse Analytics (anteriormente conhecido como SQL DW) é o armazenamento de dados de destino/coletor. O Armazenamento de Blobs do Azure serve para preparar os dados antes de eles serem carregados no Azure Synapse Analytics (anteriormente conhecido como SQL DW) usando PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Criar o serviço vinculado do Banco de Dados SQL do Azure de origem
Nesta etapa, você criará um serviço vinculado para vincular seu banco de dados SQL do Azure ao data factory. 

1. Clique em **Conexões** e, na parte inferior da janela, clique em **+ Novo** na barra de ferramentas (o botão **Conexões** fica localizado na parte inferior da coluna esquerda, sob **Recursos de Fábrica**). 

1. Na janela **Novo Serviço Vinculado**, selecione **Banco de Dados SQL do Azure** e clique em **Continuar**. 
1. Na janela **Novo serviço vinculado (Banco de Dados SQL do Azure)** , execute as etapas a seguir: 

    a. Insira **AzureSqlDatabaseLinkedService** para o **Nome**.
    
    b. Selecione o seu SQL Server do Azure para o **Nome do servidor**
    
    c. Selecione o seu banco de dados SQL do Azure para o **Nome do banco de dados**. 
    
    d. Digite **nome do usuário** para se conectar ao banco de dados SQL do Azure. 
    
    e. Insira a **senha** do usuário. 

    f. Para testar a conexão ao banco de dados SQL do Azure usando as informações especificadas, clique em **Testar conectividade**.
  
    g. Clique em **Criar** para salvar o serviço vinculado.


### <a name="create-the-sink-azure-synapse-analytics-formerly-sql-dw-linked-service"></a>Criar o serviço vinculado do Azure Synapse Analytics do coletor (anteriormente conhecido como SQL DW) do coletor

1. Na guia **Conexões**, clique em **+ Novo** na barra de ferramentas novamente. 
1. Na janela **Novo serviço vinculado**, selecione **Azure Synapse Analytics (anteriormente conhecido como SQL DW)** e clique em **Continuar**. 
1. Na janela **Novo Serviço Vinculado (Azure Synapse Analytics (anteriormente conhecido como SQL DW))** , execute as seguintes etapas: 
   
    a. Insira **AzureSqlDWLinkedService** para o **Nome**.
     
    b. Selecione o seu SQL Server do Azure para o **Nome do servidor**
     
    c. Selecione o seu banco de dados SQL do Azure para o **Nome do banco de dados**. 
     
    d. Digite **Nome de usuário** para se conectar ao Banco de Dados SQL do Azure. 
     
    e. Insira a **Senha** do usuário. 
     
    f. Para testar a conexão ao banco de dados SQL do Azure usando as informações especificadas, clique em **Testar conectividade**.
     
    g. Clique em **Criar**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Criar o serviço vinculado do Armazenamento do Azure de preparo
Neste tutorial, você usa o Armazenamento de Blobs do Azure como uma área de preparação intermediária para habilitar o PolyBase para um melhor desempenho de cópia.

1. Na guia **Conexões**, clique em **+ Novo** na barra de ferramentas novamente. 
1. Na janela **Novo Serviço Vinculado**, selecione **Armazenamento de Blobs do Azure** e clique em **Continuar**. 
1. Na janela **Novo serviço vinculado (Armazenamento de Blobs do Azure)** , execute as etapas a seguir: 

    a. Insira **AzureStorageLinkedService** como o **Nome**.                                                 
    b. Selecione sua **conta de Armazenamento do Azure** como o **Nome da conta de armazenamento**.
    
    c. Clique em **Criar**.


## <a name="create-datasets"></a>Criar conjuntos de dados
Neste tutorial você cria os conjuntos de dados de origem e do coletor, que especificam o local onde os dados são armazenados. 

O conjunto de dados de entrada **AzureSqlDatabaseDataset** refere-se a **AzureSqlDatabaseLinkedService**. O serviço vinculado especifica a cadeia de conexão para se conectar ao banco de dados. O conjunto de dados especifica o nome do banco de dados e da tabela que contêm os dados de origem. 

O conjunto de dados de saída **AzureSqlDWDataset** refere-se a **AzureSqlDWLinkedService**. O serviço vinculado especifica a cadeia de conexão para se conectar ao Azure Synapse Analytics (anteriormente conhecido como SQL DW). O conjunto de dados especifica o banco de dados e a tabela para a qual os dados são copiados. 

Neste tutorial, as tabelas SQL de origem e de destino não são embutidas nas definições de conjunto de dados. Em vez disso, a atividade ForEach passa o nome da tabela em runtime para a atividade de Cópia. 

### <a name="create-a-dataset-for-source-sql-database"></a>Criar um conjunto de dados para o Banco de Dados SQL de origem

1. Clique em **+ (adição)** no painel esquerdo e clique em **Conjunto de dados**. 

    ![Menu Novo conjunto de dados](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. Na janela **Novo Conjunto de dados**, selecione **Banco de Dados SQL do Azure** e clique em **Continuar**. 
    
1. Na janela **Definir propriedades**, em **Nome**, insira **AzureSqlDatabaseDataset**. Em **Serviço vinculado**, selecione **AzureSqlDatabaseLinkedService**. Em seguida, clique em **OK**.

1. Alterne para a guia **Conexão** e selecione qualquer tabela para **Tabela**. Esta é uma tabela fictícia. Você pode especificar uma consulta no conjunto de dados de origem ao criar um pipeline. A consulta é usada para extrair dados do Banco de Dados SQL do Azure. Como alternativa, você pode clicar na caixa de seleção **Editar** e inserir **dbo.dummyName** como o nome da tabela. 
 

### <a name="create-a-dataset-for-sink-azure-synapse-analytics-formerly-sql-dw"></a>Criar um conjunto de dados para o Azure Synapse Analytics (anteriormente conhecido como SQL DW) do coletor

1. Clique em **+ (adição)** no painel esquerdo e clique em **Conjunto de dados**. 
1. Na janela **Novo Conjunto de Dados**, selecione **Azure Synapse Analytics (anteriormente conhecido como SQL DW)** e clique em **Continuar**.
1. Na janela **Definir propriedades**, em **Nome**, insira **AzureSqlDWDataset**. Em **Serviço vinculado**, selecione **AzureSqlDWLinkedService**. Em seguida, clique em **OK**.
1. Alterne para a guia **Parâmetros**, clique em **+ Novo** e digite **DWTableName** para o nome do parâmetro. Se você copiar/colar esse nome da página, verifique se não há algum **caractere de espaço à direita** no final de **DWTableName**.
1. Alterne para a guia **Conexão**, 

    a. Para **Tabela**, marque a opção **Editar**. Insira **dbo** na primeira caixa de entrada do nome da tabela. Em seguida, selecione dentro da segunda caixa de entrada e clique no link **Adicionar conteúdo dinâmico** abaixo. 

    ![Nome da tabela da conexão de conjunto de dados](./media/tutorial-bulk-copy-portal/dataset-connection-tablename.png)

    b. Na página **Adicionar conteúdo dinâmico**, clique em **DWTAbleName** em **Parâmetros** que preencherá automaticamente a caixa de texto de expressão na parte superior `@dataset().DWTableName`, em seguida, clique em **Concluir**. A propriedade **tableName** do conjunto de dados é definida como o valor que é passado como um argumento para o parâmetro **DWTableName**. A atividade ForEach itera por meio de uma lista de tabelas e passa uma por uma para a atividade de Cópia. 

    ![Construtor do parâmetro dataset](./media/tutorial-bulk-copy-portal/dataset-parameter-builder.png)
 
## <a name="create-pipelines"></a>Criar pipelines
Neste tutorial, você cria dois pipelines: **IterateAndCopySQLTables** e **GetTableListAndTriggerCopyData**. 

O pipeline **GetTableListAndTriggerCopyData** pipeline executa duas ações:

* Pesquisa a tabela do sistema do Banco de Dados SQL do Azure para obter a lista de tabelas a serem copiadas.
* Dispara o pipeline **IterateAndCopySQLTables** para fazer a cópia de dados propriamente dita.

O pipeline **IterateAndCopySQLTables** usa uma lista de tabelas como um parâmetro. Para cada tabela na lista, ele copia dados da tabela no Banco de Dados SQL do Azure para o Azure Synapse Analytics (anteriormente conhecido como SQL DW) usando cópia preparada e o PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Criar o pipeline IterateAndCopySQLTables

1. No painel esquerdo, clique em **+ (adição)** e clique em **Pipeline**.

    ![Menu do novo pipeline](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. Na guia **Geral**, especifique **IterateAndCopySQLTables** para o nome. 

1. Alterne para a guia **Parâmetros** e faça o seguinte: 

    a. Clique em **+ Novo**. 
    
    b. Insira **tableList** para o parâmetro **Name**.
    
    c. Selecione **Matriz** para **Tipo**.

1. Na caixa de ferramentas **Atividades**, expanda **Iteração e condições** e arraste e solte a atividade **ForEach** para a superfície de designer do pipeline. Você também pode pesquisar atividades na caixa de ferramentas **Atividades**. 

    a. Na guia **Geral**, na parte inferior, digite **IterateSQLTables** para o **Nome**. 

    b. Alterne para a guia **Configurações**, clique na caixa de entrada para **Itens**, em seguida, clique no link **Adicionar conteúdo dinâmico** abaixo. 

    c. Na página **Adicionar Conteúdo Dinâmico**, recolha as seções **Variáveis do Sistema** e **Funções**, clique em **tableList** em **Parâmetros**, que preencherá automaticamente a caixa de texto de expressão superior para `@pipeline().parameter.tableList`. Em seguida, clique em **Concluir**. 

    ![Construtor do parâmetro foreach](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Alterne para a guia **Atividades**, clique no **ícone de lápis** para adicionar uma atividade filho para a atividade **ForEach**.
    ![Construtor de atividades ForEach](./media/tutorial-bulk-copy-portal/for-each-activity-builder.png)

1. Na caixa de ferramentas **Atividades**, expanda **Mover e Transferir** e arraste e solte a atividade **Copiar dados** para a superfície do designer de pipeline. Observe o menu de navegação estrutural na parte superior. **IterateAndCopySQLTable** é o nome do pipeline e **IterateSQLTables** é o nome da atividade ForEach. O designer está no escopo da atividade. Para voltar ao editor do pipeline do editor de ForEach, você pode clicar no link no menu de navegação estrutural. 

    ![Cópia no ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)

1. Alterne para a guia **Origem** e siga estas etapas:

    1. Selecione **AzureSqlDatabaseDataset** para **Conjunto de dados de origem**. 
    1. Selecione a opção de **Consulta** para **Usar consulta**. 
    1. Clique na caixa de entrada **Consulta** -> selecione **Adicionar conteúdo dinâmico** abaixo -> insira a seguinte expressão para **Consulta** -> selecione **Concluir**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 


1. Alterne para a guia **Coletor** e siga estas etapas: 

    1. Selecione **AzureSqlDWDataset** para **Conjunto de dados do coletor**.
    1. Clique na caixa de entrada para o VALUE do parâmetro DWTableName -> selecione **Adicionar conteúdo dinâmico** abaixo, insira a expressão `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]` como script -> selecione **Concluir**.
    1. Para o método Copy, selecione **PolyBase**. 
    1. Desmarque a opção **Usar padrão do tipo**. 
    1. Clique na caixa de entrada **Pré-copiar Script** -> selecione **Adicionar conteúdo dinâmico** abaixo -> insira a seguinte expressão como script -> selecione **Concluir**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Copiar as configurações do coletor](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
1. Alterne para a guia **Configurações** e siga estas etapas: 

    1. Marque a caixa de seleção para **Habilitar Preparo**.
    1. Selecione **AzureStorageLinkedService** como **Armazenar serviço vinculado da conta**.

1. Para validar as configurações de pipeline, clique em **Validar** na barra de ferramentas para o pipeline na parte superior. Verifique se não houve nenhum erro de validação. Para fechar o **Relatório de validação do pipeline** clique em **>>** .

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Criar o pipeline GetTableListAndTriggerCopyData

Esse pipeline executa duas ações:

* Pesquisa a tabela do sistema do Banco de Dados SQL do Azure para obter a lista de tabelas a serem copiadas.
* Dispara o pipeline "IterateAndCopySQLTables" para fazer a cópia de dados propriamente dita.

1. No painel esquerdo, clique em **+ (adição)** e clique em **Pipeline**.
1. Na guia **Geral**, altere o nome do pipeline para **GetTableListAndTriggerCopyData**. 

1. Na caixa de ferramentas **Atividades**, expanda **Geral** e arraste e solte a atividade de **Pesquisa** para a superfície do designer de pipeline e execute as seguintes etapas:

    1. Digite **LookupTableList** para **Nome**. 
    1. Digite **Recuperar a lista de tabelas do banco de dados SQL do Azure** para **Descrição**.

1. Alterne para a guia **Configurações** e siga estas etapas:

    1. Selecione **AzureSqlDatabaseDataset** para **Conjunto de dados de origem**. 
    1. Selecione **Consulta** para **Usar consulta**. 
    1. Insira a seguinte consulta SQL para **Consulta**.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Desmarque a caixa de seleção para o campo **Somente primeira linha**.

        ![Atividade de pesquisa - página configurações](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Arraste e solte a atividade **Executar pipeline** da caixa de ferramentas Atividades para a superfície do designer do pipeline e defina o nome como **TriggerCopy**.

1. Para **Conectar** a atividade de **Pesquisa** à atividade **Executar Pipeline**, arraste a **caixa verde** associada à atividade de Pesquisa à esquerda da atividade Executar Pipeline.

    ![Conecte as atividades de Pesquisa e Executar pipeline](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)

1. Alterne para a guia **Configurações** da janela **Pipeline** e execute as seguintes etapas: 

    1. Selecione **IterateAndCopySQLTables** para **Pipeline invocado**. 
    1. Expanda a seção **Avançado** e desmarque a caixa de seleção **Aguardar a conclusão**.
    1. Clique em **+ Novo** na seção **Parâmetros**. 
    1. Digite **tableList** para o parâmetro **Name**.
    1. Clique na caixa de texto VALOR -> selecione **Adicionar conteúdo dinâmico** abaixo -> insira `@activity('LookupTableList').output.value` como o valor do nome da tabela -> selecione **Concluir**. Você está configurando a lista de resultados da atividade de Pesquisa como uma entrada para o segundo pipeline. A lista de resultados contém a lista de tabelas cujos dados precisam ser copiados para o destino. 

        ![Atividade Executar pipeline - página de configurações](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)

1. Para validar o pipeline, clique em **Validar** na barra de ferramentas. Confirme se não houver nenhum erro de validação. Para fechar o **Relatório de validação do pipeline** clique em **>>** .

1. Para publicar entidades (conjuntos de dados, pipelines etc.) no serviço Data Factory, clique em **Publicar tudo** na parte superior da janela. Aguarde até que a publicação seja bem-sucedida. 

## <a name="trigger-a-pipeline-run"></a>Disparar uma execução de pipeline

1. Vá até o pipeline **GetTableListAndTriggerCopyData**, clique em **Adicionar Gatilho** na barra de ferramentas do pipeline superior e, em seguida, clique em **Disparar agora**. 

1. Confirme a execução na página **Execução do pipeline** e selecione **Concluir**.

## <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

1. Alterne para a guia **Monitorar**. Clique em **Atualizar** até que você veja as execuções para ambos os pipelines em sua solução. Continue atualizando a lista até que você veja o status **Bem-sucedido**. 

1. Para exibir execuções de atividade associadas ao pipeline **GetTableListAndTriggerCopyData**, clique no link do nome do pipeline para o pipeline. Você deve ver duas execuções de atividade para essa execução de pipeline. 
    ![Monitorar execução do pipeline](./media/tutorial-bulk-copy-portal/monitor-pipeline.png)
1. Para exibir a saída da atividade de **Pesquisa**, clique no link **Saída** ao lado da atividade, sob a coluna **NOME DA ATIVIDADE**. Você pode maximizar e restaurar a janela **Saída**. Depois de revisar, clique em **X** para fechar a janela **Saída**.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
1. Para voltar para a exibição **Execuções de Pipeline**, clique em **Todas as execuções de pipeline** na parte superior do menu de navegação estrutural. Clique no link **IterateAndCopySQLTables** (na coluna **NOME DO PIPELINE**) para exibir as execuções de atividade do pipeline. Observe que há uma execução de atividade de **Cópia** para cada tabela na saída da atividade **Pesquisa**. 

1. Confirme se os dados foram copiados para o Azure Synapse Analytics (anteriormente conhecido como SQL DW) de destino que você usou neste tutorial. 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você realizará os seguintes procedimentos: 

> [!div class="checklist"]
> * Criar um data factory.
> * Criar um Banco de Dados SQL do Azure, o Azure Synapse Analytics (anteriormente conhecido como SQL DW) e os serviços vinculados do Armazenamento do Azure.
> * Criar conjuntos de dados do Banco de Dados SQL do Azure e do Azure Synapse Analytics (anteriormente conhecido como SQL DW).
> * Crie um pipeline para consultar as tabelas a serem copiadas e outro pipeline para executar a operação de cópia propriamente dita. 
> * Iniciar uma execução de pipeline.
> * Monitore as execuções de pipeline e de atividade.

Avance para o tutorial a seguir para saber mais sobre como copiar dados incrementalmente de uma origem para um destino:
> [!div class="nextstepaction"]
>[Copiar dados incrementalmente](tutorial-incremental-copy-portal.md)
