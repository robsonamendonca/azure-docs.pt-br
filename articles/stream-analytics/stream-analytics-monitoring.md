---
title: Noções básicas sobre o monitoramento de trabalhos no Azure Stream Analytics
description: Este artigo descreve como monitorar trabalhos do Azure Stream Analytics no portal do Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 54bff88e9650240a3703e18d583f603cafeb3022
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611884"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Noções básicas sobre o monitoramento de trabalhos do Stream Analytics e como monitorar consultas

## <a name="introduction-the-monitor-page"></a>Introdução: Página do monitor
O portal do Azure superfícies principais métricas de desempenho que podem ser usadas para monitorar e solucionar problemas de desempenho de consulta e de trabalho. Para ver essas métricas, procure o trabalho do Stream Analytics em cujas métricas você está interessado em ver e exiba a seção **Monitoramento** na página de visão geral.  

![Link do monitoramento de trabalho do Stream Analytics](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

A janela será exibida conforme mostrado:

![Painel do monitoramento de trabalho do Stream Analytics](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Métricas disponíveis para o Stream Analytics
| Métrica                 | Definição                               |
| ---------------------- | ---------------------------------------- |
| Eventos de Entrada Acumulados       | Número de eventos de entrada com lista de pendências. Um valor diferente de zero nessa métrica indica que seu trabalho não consegue acompanhar o número de eventos de entrada. Se esse valor for aumentando lentamente ou for consistentemente diferente de zero, expanda seu trabalho. Você pode aprender mais visitando [Compreender e ajustar as Unidades de Streaming](stream-analytics-streaming-unit-consumption.md). |
| Erros de conversão de dados | Número de eventos de saída que não pôde ser convertido para o esquema de saída esperado. A política de erro pode ser alterada para 'Remover' a fim de remover os eventos que encontram esse cenário. |
| Eventos de Entrada Antecipados       | Eventos cujos carimbos de data/hora do aplicativo sejam anteriores à hora de chegada por mais de cinco minutos. |
| Solicitações de função com falha | Número de chamadas à função Azure Machine Learning com falha (se presente). |
| Eventos de função        | Número de eventos enviados à função Azure Machine Learning (se presente). |
| Solicitações de função      | Número de chamadas à função Azure Machine Learning (se presente). |
| Erros de desserialização de entrada       | Número de eventos de entrada que não puderam ser desserializados.  |
| Bytes de evento de entrada      | Quantidade de dados recebidos pelo trabalho do Stream Analytics, em bytes. Isso pode ser usado para validar que os eventos estão sendo enviados para a fonte de entrada. |
| Eventos de entrada           | Número de registros desserializados nos eventos de entrada. Essa contagem não inclui eventos de entrada que resultam em erros de desserialização. Os mesmos eventos podem ser ingeridos por Stream Analytics várias vezes em cenários como recuperações internas e autojunções. Portanto, é recomendável não esperar eventos de entrada e métricas de eventos de saída para corresponder se seu trabalho tiver uma consulta "passagem" simples. |
| Fontes de Entrada Recebidas       | Número de mensagens recebidas pelo trabalho. Para o Hub de eventos, uma mensagem é um único EventData. Para BLOB, uma mensagem é um único BLOB. Observe que as fontes de entrada são contadas antes da desserialização. Se houver erros de desserialização, as fontes de entrada poderão ser maiores que os eventos de entrada. Caso contrário, ele pode ser menor ou igual a eventos de entrada, pois cada mensagem pode conter vários eventos. |
| Eventos de entrada atrasados      | Eventos que chegaram mais tarde do que a janela de tolerância de chegada tardia configurada. Saiba mais sobre [Considerações sobre a ordem dos eventos do Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md). |
| Eventos fora de ordem    | Número de eventos recebidos fora de ordem que foram descartados ou que receberam um carimbo de data/hora ajustado, com base na Política de ordenação de evento. Isso pode ser afetado pela configuração da definição da Janela de tolerância fora de ordem. |
| Eventos de saída          | Quantidade de dados enviados pelo trabalho do Stream Analytics para o destino de saída, em números de evento. |
| Erros de runtime         | Número total de erros relacionados ao processamento de consulta (excluindo erros encontrados ao ingerir eventos ou resultados de saída) |
| % de utilização do SU       | A utilização das unidades de Streaming é atribuída a um trabalho na guia Escala do trabalho. Se esse indicador alcançar 80% ou acima, há grande probabilidade de que o processamento de eventos se atrase ou pare. |
| Atraso de Marca-d'água       | O atraso máximo de marca d'água em todas as partições de todas as saídas no trabalho. |

É possível usar essas métricas para [monitorar o desempenho de seu trabalho do Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Personalizando o Monitoramento no Portal do Azure
Você pode ajustar o tipo de gráfico, as métricas mostradas e o intervalo de hora nas configurações de Editar Gráfico. Para obter detalhes, veja [Como personalizar o monitoramento](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Gráfico de tempo do monitor de consultas do Stream Analytics](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Saída mais recente
Outro ponto de dados interessante para monitorar o trabalho é a hora da última saída, mostrada na página Visão geral.
Essa é o tempo de aplicação (ou seja, a hora usando o carimbo de data/hora dos dados de evento) da saída mais recente do trabalho.

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST de gerenciamento de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
