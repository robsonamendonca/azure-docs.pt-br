---
title: Conectar-se ao twilio de aplicativos lógicos do Azure
description: Automatizar tarefas e fluxos de trabalho que gerenciam mensagens globais de SMS, MMS e IP por meio de sua conta do Twilio usando os Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: e5b218efd9c8cfaad99d76d8118d181390a977c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789096"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Gerenciar mensagens no Twilio com os Aplicativos Lógicos do Azure

Com os Aplicativos Lógicos do Azure e o conector do Twilio, você pode criar tarefas automatizadas e fluxos de trabalho que obtém, enviam e listam as mensagens do Twilio, que incluem mensagens globais de SMS, MMS e IP. Você pode usar essas ações para executar tarefas com sua conta do Twilio. Você também pode fazer com que outras ações usem a saída das ações do Twilio. Por exemplo, quando uma nova mensagem chega, você pode enviar o conteúdo da mensagem com o conector do Slack. Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* No [Twilio](https://www.twilio.com/): 

  * A ID da conta do Twilio e o [token de autenticação](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them), que podem ser encontrados no seu painel do Twilio

    Suas credenciais autorizam o aplicativo lógico a criar uma conexão e acessar sua conta do Twilio por meio do aplicativo lógico. 
    Se você estiver usando uma conta de avaliação do Twilio, poderá enviar SMS somente para números de telefone *verificados*.

  * Um número de telefone Twilio verificado que possa enviar SMS

  * Um número de telefone Twilio verificado que possa receber SMS

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar a conta do Twilio. Para usar uma ação do Twilio, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho **Recorrência**.

## <a name="connect-to-twilio"></a>Conectar-se ao Twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Escolha um caminho: 

     * Na última etapa em que você deseja adicionar uma ação, escolha **Nova etapa**. 

       -ou-

     * Entre as etapas em que você deseja adicionar uma ação, mova o ponteiro sobre a seta entre as etapas. 
     Escolha o sinal de adição**+**() que aparece e, em seguida, selecione **Adicionar uma ação**.
     
       Na caixa de pesquisa, insira "twilio" como o filtro. 
       Na lista de ações, selecione a ação desejada.

1. Forneça os detalhes necessários para sua conexão e, em seguida, escolha **criar**:

   * O nome a usar para a conexão
   * A ID da sua conta do Twilio 
   * O token de acesso (autenticação) do Twilio

1. Forneça os detalhes necessários para a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/twilio/) do conector.

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)