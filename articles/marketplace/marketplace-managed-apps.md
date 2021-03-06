---
title: Guia de publicação da oferta de aplicativo gerenciado por aplicativos do Azure-Azure Marketplace
description: Este artigo descreve os requisitos para publicar um aplicativo gerenciado no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: dsindona
ms.openlocfilehash: 5bd89e406c3067585595479dc1d8351e9ea7eea8
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856108"
---
# <a name="publishing-guide-for-azure-managed-applications"></a>Guia de publicação para aplicativos gerenciados do Azure

Uma oferta de *aplicativo gerenciado* do Azure é uma maneira de publicar um aplicativo do Azure no Azure Marketplace. Os aplicativos gerenciados são ofertas do Transact que são implantadas e cobradas por meio do Azure Marketplace. O plano de ação que um usuário vê é *obtê-lo agora*.

Este artigo explica os requisitos para o tipo de oferta de aplicativo gerenciado.

Use o tipo de oferta de aplicativo gerenciado sob as seguintes condições:

- Você está implantando uma solução baseada em assinatura para seu cliente usando uma VM (máquina virtual) ou uma solução baseada em IaaS (infraestrutura como serviço) inteira.
- Você ou seu cliente exige que a solução seja gerenciada por um parceiro.

>[!NOTE]
>Por exemplo, um parceiro pode ser um integrador de sistemas ou um provedor de serviços gerenciado (MSP).  

## <a name="managed-application-offer-requirements"></a>Requisitos da oferta de aplicativo gerenciado

|Requisitos |Detalhes  |
|---------|---------|
|Uma assinatura do Azure | Os aplicativos gerenciados devem ser implantados na assinatura de um cliente, mas podem ser gerenciados por terceiros. |
|Cobrança e medição    |  Os recursos são fornecidos na assinatura do Azure de um cliente. As VMs que usam o modelo de pagamento pago conforme o uso são transacionadas com o cliente pela Microsoft e cobradas por meio da assinatura do Azure do cliente. <br><br> Para suas VMs traga sua própria licença, a Microsoft cobra os custos de infraestrutura incorridos na assinatura do cliente, mas você tem transações de licenciamento de software diretamente com o cliente.        |
|Um VHD (disco rígido virtual) compatível com o Azure    |   As VMs devem ser criadas em Windows ou Linux.<br><br>Para obter mais informações sobre como criar um VHD Linux, consulte [Distribuições do Linux endossadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).<br><br>Para obter mais informações sobre como criar um VHD do Windows, consulte [criar uma oferta de aplicativo do Azure](./partner-center-portal/create-new-azure-apps-offer.md). |

---

> [!NOTE]
> Os aplicativos gerenciados devem ser implantados por meio do Azure Marketplace. Se a comunicação do cliente for uma preocupação, entre em contato com os clientes interessados depois de habilitar o compartilhamento de leads.  

> [!Note]
> Uma aceitação de canal de parceiro do CSP (provedor de soluções na nuvem) já está disponível. Para obter mais informações sobre como comercializar sua oferta por meio dos canais de parceiros do Microsoft CSP, consulte [provedores de soluções de nuvem](./cloud-solution-providers.md).

## <a name="next-steps"></a>Próximas etapas

Se você ainda não fez isso, saiba como [aumentar seu negócio de nuvem com o Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

Para se registrar e começar a trabalhar no Partner Center:

- [Entre no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou concluir sua oferta.
- Consulte [criar uma oferta de aplicativo do Azure](./partner-center-portal/create-new-azure-apps-offer.md) para obter mais informações.
