---
title: Migrar para um recurso de criação do Azure
titleSuffix: Azure Cognitive Services
description: Migre para um recurso de criação do Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: f5197ae79670e4543c58224a33838706edae6218
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "78194624"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Etapas para migrar para o recurso de criação do Azure

No portal de Reconhecimento vocal (LUIS), migre todos os aplicativos que você possui para usar o recurso de criação do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* **Opcionalmente**, faça backup dos aplicativos da lista de aplicativos do portal do Luis exportando cada aplicativo ou usando a [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)de exportação.
* **Opcionalmente**, salve a lista de collaborator's de cada aplicativo. Todos os colaboradores podem receber um email como parte do processo de migração.
* **Necessário**, você precisa ter uma [assinatura do Azure](https://azure.microsoft.com/free/). Uma parte do processo de assinatura requer informações de cobrança. No entanto, você pode usar tipos de preço gratuitos (F0) ao usar o LUIS. Você pode, eventualmente, achar que precisa de uma camada paga, à medida que seu uso aumenta.

Se você não tiver uma assinatura do Azure, [Inscreva-](https://azure.microsoft.com/free/)se.

## <a name="access-the-migration-process"></a>Acessar o processo de migração

Em uma base semanal, você será solicitado a migrar seus aplicativos. Você pode cancelar esta janela sem migrar. Se você quiser migrar antes do próximo período agendado, poderá iniciar o processo de migração no ícone **do Azure** na barra de ferramentas superior do portal do Luis.

> [!div class="mx-imgBorder"]
> ![Ícone de migração](./media/migrate-authoring-key/migration-button.png)

## <a name="app-owner-begins-the-migration-process"></a>O proprietário do aplicativo inicia o processo de migração

O processo de migração estará disponível se você for o proprietário de qualquer aplicativo LUIS.

1. Entre no [portal do Luis](https://www.luis.ai) e concorde com os termos de uso.
1. A janela pop-up de migração permite que você continue a migração ou migre mais tarde. Selecione **migrar agora**. Se você optar por migrar mais tarde, terá 9 meses para migrar para a nova chave de criação no Azure.

    ![Primeira janela pop-up no processo de migração, selecione migrar agora.](./media/migrate-authoring-key/migrate-now.png)

1. Opcionalmente, se algum dos seus aplicativos tiver colaboradores, você será solicitado a **enviar um email** informando-os sobre a migração. Esta é uma etapa opcional.

    Depois de migrar sua conta para o Azure, seus aplicativos não estarão mais disponíveis para colaboradores.

    Para cada colaborador e aplicativo, o aplicativo de email padrão é aberto com um email levemente formatado. Você pode editar o email antes de enviá-lo.

    O modelo de email inclui a ID do aplicativo e o nome do aplicativo exatos.

    ```html
    Dear Sir/Madam,

    I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

    App Id: <app-ID-omitted>
    App name: Human Resources

    Thank you
    ```

1. Escolha criar um recurso de criação de LUIS selecionando para usar um recurso de criação existente ou para criar um novo recurso de criação.

    > [!div class="mx-imgBorder"]
    > ![Criar recurso de criação](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

1. Na próxima janela, insira as informações da chave de recurso. Depois de inserir as informações, selecione **criar recurso**. Você pode ter 10 recursos de criação gratuitos por região, por assinatura.

    ![Criar recurso de criação](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    Ao **criar um novo recurso de criação**, forneça as seguintes informações:

    * **Nome do recurso** – um nome personalizado que você escolher, usado como parte da URL para as consultas de ponto de extremidade de criação e previsão.
    * **Locatário** – o locatário ao qual sua assinatura do Azure está associada.
    * **Nome da assinatura** – a assinatura que será cobrada pelo recurso.
    * **Grupo de recursos** – o nome de um grupo de recursos personalizado que você escolher ou criar. Os grupos de recursos permitem agrupar os recursos do Azure para acesso e gerenciamento.
    * **Local** – a escolha de local é baseada na seleção do **grupo de recursos**.
    * **Tipo de preço** – o tipo de preço determina a transação máxima por segundo e mês.

1. Valide seu recurso de criação e **migre agora**.

    ![Criar recurso de criação](./media/migrate-authoring-key/choose-authoring-resource-and-migrate.png)

1. Quando o recurso de criação é criado, a mensagem de êxito é mostrada. Selecione **fechar** para fechar a janela pop-up.

    ![O recurso de criação foi criado com êxito.](./media/migrate-authoring-key/migration-success.png)

    A lista **meus aplicativos** mostra os aplicativos migrados para o novo recurso de criação.

    Você não precisa saber a chave do recurso de criação para continuar editando seus aplicativos no portal do LUIS. Se você planeja editar seus aplicativos programaticamente, precisará dos valores de chave de criação. Esses valores são exibidos na página **gerenciar > recursos do Azure** no portal do Luis e também estão disponíveis no portal do Azure na página **chaves** do recurso.

1. Antes de acessar seus aplicativos, selecione a assinatura e o recurso de criação de LUIS para ver os aplicativos que você pode criar.

    ![Selecione assinatura e recurso de criação de LUIS para ver os aplicativos que você pode criar.](./media/migrate-authoring-key/app-list-by-subscription-and-resource.png)


## <a name="app-contributor-begins-the-migration-process"></a>O colaborador do aplicativo começa o processo de migração

Siga as mesmas etapas do proprietário do aplicativo para a migração. O processo cria um novo recurso de criação de tipo `LUIS.Authoring`.

Você precisa migrar sua conta para ser adicionada como um colaborador a aplicativos migrados pertencentes a outros.

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>Após o processo de migração, adicione colaboradores ao recurso de criação

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Saiba [como adicionar colaboradores](luis-how-to-collaborate.md).

## <a name="troubleshooting-errors-with-the-migration-process"></a>Solucionando erros com o processo de migração

Se você receber um `MissingSubscriptionRegistration` erro no portal do Luis com uma barra de notificação vermelha durante o processo de migração, crie um recurso de serviço cognitiva no [portal do Azure](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) ou [CLI do Azure](luis-how-to-azure-subscription.md#create-resources-in-azure-cli). Saiba mais sobre as [causas desse erro](../../azure-resource-manager/templates/error-register-resource-provider.md#cause).

## <a name="next-steps"></a>Próximas etapas


* Examinar [conceitos](luis-concept-keys.md) sobre criação e chaves de tempo de execução
* Examine [como atribuir chaves](luis-how-to-azure-subscription.md) e adicionar [colaboradores](luis-how-to-collaborate.md)
