---
title: Exibir atividade do provedor de serviços
description: Os clientes podem exibir a atividade registrada para ver as ações executadas pelos provedores de serviço por meio do gerenciamento de recursos delegado do Azure.
ms.date: 01/15/2020
ms.topic: conceptual
ms.openlocfilehash: a923a57ecc94ac15af207c2b8dc8998708b708d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77649629"
---
# <a name="view-service-provider-activity"></a>Exibir atividade do provedor de serviços

Os clientes que têm assinaturas delegadas para o gerenciamento de recursos delegado do Azure podem exibir os dados [do log de atividades do Azure](../../azure-monitor/platform/platform-logs-overview.md) para ver todas as ações executadas. Isso dá aos clientes visibilidade total das operações que os provedores de serviços estão realizando por meio do gerenciamento de recursos delegado do Azure, juntamente com as operações realizadas por usuários no locatário do próprio Azure Active Directory (Azure AD) do cliente.

> [!TIP]
> Também fornecemos uma definição de política interna Azure Policy para auditar a delegação de escopos para um locatário de gerenciamento. Para obter mais informações, consulte [delegações de auditoria em seu ambiente](view-manage-service-providers.md#audit-delegations-in-your-environment).

## <a name="view-activity-log-data"></a>Exibir dados do log de atividades

Você pode [Exibir o log de atividades](../../azure-monitor/platform/activity-log-view.md) no menu **monitorar** na portal do Azure. Para limitar os resultados a uma assinatura específica, use os filtros para selecionar uma assinatura específica. Você também pode [Exibir e recuperar eventos do log de atividades](../../azure-monitor/platform/activity-log-view.md) programaticamente.

> [!NOTE]
> Os usuários no locatário de um provedor de serviços podem exibir os resultados do log de atividades para uma assinatura delegada em um locatário do cliente se eles tiverem recebido a função [leitor](../../role-based-access-control/built-in-roles.md#reader) (ou outra função interna que inclui acesso de leitor) quando essa assinatura foi integrada ao gerenciamento de recursos delegados do Azure.

No log de atividades, você verá o nome da operação e seu status, juntamente com a data e a hora em que ele foi executado. O **evento iniciado por** coluna mostra qual usuário realizou a operação, se ele foi um usuário em um locatário do provedor de serviços agindo por meio do gerenciamento de recursos delegado do Azure ou um usuário no próprio locatário do cliente. Observe que o nome do usuário é mostrado, em vez do locatário, ou da função que o usuário foi atribuído para essa assinatura.

A atividade registrada está disponível no portal do Azure nos últimos 90 dias. Para saber como armazenar esses dados por mais de 90 dias, consulte [coletar e analisar os logs de atividades do Azure no espaço de trabalho log Analytics](../../azure-monitor/platform/activity-log-collect.md).

> [!NOTE]
> Os usuários do provedor de serviços aparecem no log de atividades, mas esses usuários e suas atribuições de função não são mostrados no **controle de acesso (iam)** ou ao recuperar informações de atribuição de função por meio de APIs.

## <a name="set-alerts-for-critical-operations"></a>Definir alertas para operações críticas

Para ficar atento às operações críticas que os provedores de serviços (ou usuários em seu próprio locatário) estão executando, recomendamos a criação de [alertas do log de atividades](../../azure-monitor/platform/activity-log-alerts.md). Por exemplo, talvez você queira controlar todas as ações administrativas de uma assinatura ou ser notificado quando qualquer máquina virtual em um grupo de recursos específico for excluída. Quando você cria alertas, eles incluem ações executadas por usuários no próprio locatário do cliente, bem como em qualquer locatário de gerenciamento.

Para obter mais informações, consulte [criar e gerenciar alertas do log de atividades](../../azure-monitor/platform/alerts-activity-log.md).

## <a name="create-log-queries"></a>Criar consultas de log

Você pode criar consultas para analisar sua atividade registrada ou se concentrar em itens específicos. Por exemplo, talvez uma auditoria exija que você relate todas as ações de nível administrativo executadas em uma assinatura. Você pode criar uma consulta para filtrar apenas essas ações e classificar os resultados por usuário, data ou outro valor.

Para obter mais informações, consulte [visão geral das consultas de log no Azure monitor](../../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Azure monitor](../../azure-monitor/index.yml).
- Saiba como [Exibir e gerenciar ofertas de provedor de serviços](view-manage-service-providers.md) na portal do Azure.