---
title: Exibir e baixar uso e encargos do Azure
description: Descreve como baixar ou exibir usos e encargos diários do Azure.
keywords: cobrança por uso, preço por uso, download de uso, exibir uso, fatura do azure, uso do azure
author: bandersmsft
ms.author: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 3a7e1e8670911d361b3c59aacc0973eac035fb70
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80065713"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Exibir e baixar uso e encargos do Azure

Baixe um detalhamento diário do uso e dos encargos do Azure no portal do Azure. Apenas determinadas funções têm permissão para receber informações de uso do Azure, como o administrador da conta ou o administrador corporativo. Para saber mais sobre como obter acesso a informações de cobrança, consulte [Gerenciar o acesso à cobrança do Azure usando funções](../manage/manage-billing-access.md).

Caso tenha um MCA (Contrato de Cliente da Microsoft), você precisará ser proprietário, colaborador, leitor ou gerenciador de faturas de um perfil de cobrança para exibir o uso e os encargos do Azure.  Caso você tenha um MPA (Contrato de Parceiro da Microsoft), somente as funções Administrador Global e Agente Administrativo na organização do parceiro com a Microsoft poderão exibir e baixar o uso e os encargos do Azure. [Verifique o tipo de sua conta de cobrança no portal do Azure](#check-your-billing-account-type).

Com base no tipo de assinatura que você usa, as opções para baixar o uso e os encargos variam. 

## <a name="download-usage-from-the-azure-portal-csv"></a>Baixar o uso no portal do Azure (.csv)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Pesquise *Gerenciamento de Custos + Cobrança*.  
    ![Captura de tela que mostra a pesquisa do portal do Azure](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança ou um perfil de cobrança.
1. No menu esquerdo, selecione **Faturas** em **Cobrança**.
1. Na grade da fatura, encontre a linha do período de cobrança correspondente ao uso que deseja baixar.
1. Selecione o **ícone de download** ou as reticências (`...`) à direita.  
  ![Captura de tela mostrando o local de download Uso + encargos](./media/download-azure-daily-usage/download-usage-others.png)  
1. O painel Download se abre à direita. Selecione **Download** na seção **Detalhes de Uso**.  


## <a name="download-usage-for-ea-customers"></a>Faça o download uso para clientes do EA

Para exibir e baixar os dados de uso como cliente do EA, você deverá ser administrador corporativo, proprietário da conta ou administrador do departamento com a política de cobranças de exibição habilitada.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Pesquise *Gerenciamento de Custos + Cobrança*.  
    ![Captura de tela que mostra a pesquisa do portal do Azure](./media/download-azure-daily-usage/portal-cm-billing-search.png)
1. Selecione **uso + encargos**.
1. No mês que você deseja fazer o download, selecione **fazer o download**.  
    ![Captura de tela mostrando o local de download Uso + encargos](./media/download-azure-daily-usage/download-usage-ea.png)

## <a name="download-usage-for-pending-charges"></a>Baixar uso para encargos pendentes

Caso tenha um Contrato de Cliente da Microsoft, você poderá baixar o uso do mês atual para o período de cobrança atual. Esses encargos de uso que ainda não foram cobrados.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Pesquise *Gerenciamento de Custos + Cobrança*.
3. Selecione um perfil de cobrança. Dependendo de seu acesso, talvez você precise selecionar uma conta de cobrança primeiro.
4. Na área **Visão Geral**, encontre os links de download abaixo dos encargos do mês atual.
5. Selecione **Uso e encargos do Azure**.

    ![Captura de tela que mostra o download em Visão Geral](./media/download-azure-daily-usage/open-usage01.png)

## <a name="check-your-billing-account-type"></a>Verificar o tipo de conta de cobrança
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a fatura e os encargos, de uso, confira:

- [Compreender os termos em uso detalhado do Microsoft Azure](understand-usage.md)
- [Entenda sua fatura do Microsoft Azure](review-individual-bill.md)
- [Exibir e baixar a fatura do Microsoft Azure](download-azure-invoice.md)
- [Exibir e baixar o preço do Azure de sua organização](../manage/ea-pricing.md)

Se você tiver um Contrato de Cliente da Microsoft, confira:

- [Noções básicas sobre os termos no seu uso detalhado do Azure do Contrato de Cliente da Microsoft](mca-understand-your-usage.md)
- [Noções básicas sobre os encargos em sua fatura do Contrato de Cliente da Microsoft](review-customer-agreement-bill.md)
- [Exibir e baixar a fatura do Microsoft Azure](download-azure-invoice.md)
- [Exibir e baixar documentos de imposto do seu Contrato de Cliente da Microsoft](mca-download-tax-document.md)
- [Exibir e baixar o preço do Azure de sua organização](../manage/ea-pricing.md)
