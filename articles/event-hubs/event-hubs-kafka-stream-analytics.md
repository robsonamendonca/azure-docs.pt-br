---
title: Hubs de Eventos do Azure – processar eventos do Apache Kafka
description: 'Tutorial: Este artigo mostra como processar eventos do Kafka que são ingeridos por meio de hubs de eventos, usando Azure Stream Analytics'
services: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 04/02/2020
ms.author: spelluru
ms.openlocfilehash: 9c678a91b88b87acb438311b4968be4cae46733b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632796"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Tutorial: Processar Apache Kafka para eventos dos Hubs de Eventos usando o Stream Analytics 
Este artigo mostra como transmitir dados para os Hubs de Eventos e processá-los com o Azure Stream Analytics. Este artigo apresenta as seguintes etapas: 

1. Crie um namespace dos Hubs de Eventos.
2. Criar um cliente Kafka que envia mensagens para o hub de eventos.
3. Criar um trabalho do Stream Analytics que copia dados do hub de eventos para um armazenamento de Blobs do Azure. 

Não é necessário alterar os clientes de protocolo ou executar seus próprios clusters ao usar o ponto de extremidade do Kafka exposto por um hub de eventos. Hubs de Eventos do Azure dá suporte para [Apache Kafka versão 1.0.](https://kafka.apache.org/10/documentation.html) e posterior. 


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa atender aos seguinte pré-requisitos:

* Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Realizar o download](https://maven.apache.org/download.cgi) e [instalar](https://maven.apache.org/install.html) um armazenamento binário Maven
* [Git](https://www.git-scm.com/)
* Uma **conta do Armazenamento do Azure**. Se não tiver, [crie uma](../storage/common/storage-account-create.md) antes de prosseguir. O trabalho do Stream Analytics neste passo a passo armazena os dados de saída em um armazenamento de Blobs do Azure. 


## <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos
Quando você cria um namespace dos Hubs de Eventos do nível **Standard**, o ponto de extremidade do Kafka para o namespace é habilitado automaticamente. Você pode transmitir eventos dos seus aplicativos que usam o protocolo Kafka para Hubs de Eventos do nível Standard. Siga as instruções passo a passo em [Criar um hub de eventos usando o portal do Azure](event-hubs-create.md) para criar um namespace de Hubs de Eventos do nível **Standard**. 

> [!NOTE]
> Os Hubs de Eventos para Kafka estão disponíveis somente nas camadas **Standard** e **dedicadas**. O nível **básico** não dá suporte a Kafka em Hubs de Eventos.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Enviar mensagens com Kafka em Hubs de Eventos

1. Clone o [repositório de Hubs de Eventos do Azure para Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) no computador.
2. Navegue até a pasta: `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Atualize os detalhes de configuração para o produtor em `src/main/resources/producer.config`. Especifique o **nome** e a **cadeia de conexão** do **namespace do hub de eventos**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Navegue até `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/` e abra o arquivo **TestDataReporter.java** em um editor de sua escolha. 
6. Comente a seguinte linha de código:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Adicione a seguinte linha de código no lugar do código comentado: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Esse código envia os dados do evento no formato **JSON**. Ao configurar a entrada para uma tarefa do Stream Analytics, você especifica JSON como o formato dos dados de entrada. 
7. **Execute o produtor** e transmita para os hubs de eventos. Em um computador Windows, ao usar um **prompt de comando Node.js**, alterne para a pasta `azure-event-hubs-for-kafka/quickstart/java/producer` antes de executar esses comandos. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Verifique se o hub de eventos recebe os dados

1. Selecione **Hubs de Eventos** em **ENTIDADES**. Confirme se você vê um hub de eventos nomeado **teste**. 

    ![Hub de eventos - teste](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Confirme se você vê mensagens chegando ao hub de eventos. 

    ![Hub de eventos - mensagens](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Processar dados do evento usando um trabalho do Stream Analytics
Nesta seção, você cria um trabalho do Azure Stream Analytics. O cliente Kafka envia eventos para o hub de eventos. Você cria um trabalho do Stream Analytics que recebe dados do evento como entrada e os envia para um armazenamento de Blobs do Azure. Se não tiver uma **conta de Armazenamento do Microsoft Azure**, [crie uma](../storage/common/storage-account-create.md).

A consulta no trabalho do Stream Analytics passa pelos dados sem executar quaisquer análises. É possível criar uma consulta que transforma os dados de entrada para produzir dados de saída em um formato diferente ou com insights obtidos.  

### <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics 

1. Selecione **+ Criar um recurso** no [portal do Azure](https://portal.azure.com).
2. Selecione **Analytics** no menu **Azure Marketplace** e selecione **trabalho do Stream Analytics**. 
3. Na página **Novo Stream Analytics**, execute as seguintes ações: 
    1. Insira um **nome** para o trabalho. 
    2. Selecione sua **assinatura**.
    3. Selecione **Criar novo** para o **grupo de recursos** e insira o nome. Também é possível **usar** um grupo de recursos existente. 
    4. Selecione um **local** para o trabalho.
    5. Selecione **Criar** para criar o trabalho. 

        ![Novo trabalho do Stream Analytics](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Configurar entrada de trabalho

1. Na mensagem de notificação, selecione **Ir para o recurso** para ver a página do **trabalho do Stream Analytics**. 
2. Selecione **Entradas** na seção **TOPOLOGIA DO TRABALHO** no menu esquerdo.
3. Selecione **Adicionar entrada de fluxo** e, em seguida, selecione **Hub de eventos**. 

    ![Adicionar hub de eventos como uma entrada](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Na página de configuração **entrada do Hub de Eventos**, execute as seguintes ações: 

    1. Especifique um **alias** para a entrada. 
    2. Selecione sua **assinatura do Azure**.
    3. Selecione o **namespace do hub de eventos** criado anteriormente. 
    4. Selecione **testar** para o **hub de eventos**. 
    5. Clique em **Salvar**. 

        ![Configuração de entrada do hub de eventos](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Configurar saída de trabalho 

1. Selecione **Saídas** na seção **TOPOLOGIA DO TRABALHO** no menu. 
2. Selecione **+ Adicionar** na barra de ferramentas e selecione **Armazenamento de Blobs**
3. Na página de configurações de saída de Armazenamento de Blobs, execute as seguintes ações: 
    1. Especifique um **alias** para a saída. 
    2. Selecione sua **assinatura**do Azure. 
    3. Selecione a **conta de Armazenamento do Microsoft Azure**. 
    4. Insira um **nome para o contêiner** que armazena os dados de saída da consulta do Stream Analytics.
    5. Clique em **Salvar**.

        ![Configuração de saída do Armazenamento de Blobs](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Definir uma consulta
Depois configurar um trabalho do Stream Analytics para ler um fluxo de dados de entrada, a próxima etapa é criar uma transformação que analisa os dados em tempo real. Defina a consulta de transformação usando a [Linguagem de consulta do Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Neste passo a passo, você define uma consulta que passa pelos dados sem executar nenhuma transformação.

1. Selecione **Consulta**.
2. Na janela de consulta, substitua `[YourOutputAlias]` pelo alias de saída criado anteriormente.
3. Substitua `[YourInputAlias]` pelo alias de entrada criado anteriormente. 
4. Selecione **Salvar** na barra de ferramentas. 

    ![Consulta](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Executar o trabalho do Stream Analytics

1. Selecione **Visão geral** no menu esquerdo. 
2. Selecione **Iniciar**. 

    ![Menu Iniciar](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Na página **Iniciar trabalho**, selecione **Iniciar**. 

    ![Página Iniciar trabalho](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Aguarde até que o status do trabalho seja alterado de **Iniciando** para **em execução**. 

    ![Status do trabalho - em execução](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Testar o cenário
1. Execute o **produtor do Kafka** novamente para enviar eventos para o hub de eventos. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Confirme se você vê os **dados de saída** sendo gerados no **armazenamento de Blobs do Azure**. Você vê um arquivo JSON no contêiner com 100 linhas semelhantes às linhas de exemplo a seguir: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    O job do Azure Stream Analytics recebeu dados de entrada do hub de eventos e os armazenou no armazenamento de blob do Azure nesse cenário. 



## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como transmitir para os Hubs de Eventos sem alterar seus clientes de protocolo ou seus próprios clusters em execução. Para saber mais sobre os Hubs de Eventos para Apache Kafka, confira [guia do desenvolvedor do Apache Kafka para Hubs de Eventos do Azure](apache-kafka-developer-guide.md). 