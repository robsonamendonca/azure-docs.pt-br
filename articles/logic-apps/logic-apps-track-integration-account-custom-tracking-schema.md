---
title: Esquemas de acompanhamento personalizados para mensagens B2B
description: Criar esquemas de acompanhamento personalizados para monitorar mensagens B2B em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76903056"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Criar esquemas de acompanhamento personalizados que monitorem fluxos de trabalho de ponta a ponta na lógica A do Azure

Os aplicativos lógicos do Azure têm rastreamento interno que você pode habilitar para partes do seu fluxo de trabalho. No entanto, você pode configurar o acompanhamento personalizado que registra eventos desde o início até o fim dos fluxos de trabalho, por exemplo, fluxos de trabalho que incluem um aplicativo lógico, BizTalk Server, SQL Server ou qualquer outra camada. Este artigo fornece código personalizado que você pode usar nas camadas fora do aplicativo lógico.

## <a name="custom-tracking-schema"></a>Esquema de controle personalizado

```json
{
   "sourceType": "",
   "source": {
      "workflow": {
         "systemId": ""
      },
      "runInstance": {
         "runId": ""
      },
      "operation": {
         "operationName": "",
         "repeatItemScopeName": "",
         "repeatItemIndex": ,
         "trackingId": "",
         "correlationId": "",
         "clientRequestId": ""
      }
   },
   "events": [
      {
         "eventLevel": "",
         "eventTime": "",
         "recordType": "",
         "record": {}
      }
   ]
}
```

| Propriedade | Obrigatório | Type | Descrição |
|----------|----------|------|-------------|
| sourceType | Sim | String | Tipo de fonte de execução com estes valores permitidos: `Microsoft.Logic/workflows`,`custom` |
| source | Sim | Cadeia de caracteres ou JToken | Se o tipo de origem `Microsoft.Logic/workflows`for, as informações de origem precisarão seguir este esquema. Se o tipo de origem `custom`for, o esquema será um JToken. |
| systemId | Sim | String | ID do sistema do aplicativo lógico |
| runId | Sim | String | ID de execução do aplicativo lógico |
| operationName | Sim | String | Nome da operação, por exemplo, ação ou gatilho |
| repeatItemScopeName | Sim | String | Repita o nome do item se a ação estiver `foreach`dentro `until` de um loop ou |
| repeatItemIndex | Sim | Integer | Indica que a ação está dentro de `foreach` um `until` loop ou e é o número de índice de item repetido. |
| trackingId | Não | Cadeia de caracteres | ID de rastreamento para correlacionar as mensagens |
| correlationId | Não | Cadeia de caracteres | ID de correlação para correlacionar as mensagens |
| clientRequestId | Não | Cadeia de caracteres | O cliente pode popular essa propriedade para correlacionar mensagens |
| eventLevel | Sim | String | Nível do evento |
| eventTime | Sim | Datetime | Hora do evento no formato UTC: *yyyy-mm-ddThh: mm: SS. 00000Z* |
| recordType | Sim | String | Tipo de registro de faixa com este valor permitido apenas:`custom` |
| registro | Sim | JToken | Tipo de registro personalizado com formato JToken somente |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de acompanhamento do protocolo B2B

Para obter informações sobre esquemas de acompanhamento do protocolo B2B, veja:

* [Esquemas de acompanhamento do AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de acompanhamento de X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como [monitorar mensagens B2B com logs de Azure monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)