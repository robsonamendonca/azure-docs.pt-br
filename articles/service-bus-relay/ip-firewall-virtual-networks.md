---
title: Firewall de IP Congigure para o namespace de retransmissão do Azure
description: Este artigo descreve como usar regras de firewall para permitir conexões de endereços IP específicos para namespaces de retransmissão do Azure.
services: service-bus-relay
documentationcenter: ''
author: spelluru
ms.service: service-bus-relay
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 05/07/2020
ms.author: spelluru
ms.openlocfilehash: e2a9e89ca8c3b77b09a58a45959f1acda1457b56
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984805"
---
# <a name="configure-ip-firewall-for-an-azure-relay-namespace"></a>Configurar o firewall IP para um namespace de retransmissão do Azure
Por padrão, os namespaces de retransmissão são acessíveis da Internet, desde que a solicitação venha com autenticação e autorização válidas. Com o firewall de IP, você pode restringir ainda mais a um conjunto de endereços IPv4 ou intervalos de endereços IPv4 na notação [CIDR (roteamento entre domínios sem classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Esse recurso é útil em cenários nos quais a retransmissão do Azure deve ser acessada apenas de determinados sites conhecidos. As regras de firewall permitem que você configure regras para aceitar o tráfego originado de endereços IPv4 específicos. Por exemplo, se você usar a retransmissão com o [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services), poderá criar uma **regra de firewall** para permitir o tráfego somente de seus endereços IP de infraestrutura local. 


> [!IMPORTANT]
> Esse recurso está atualmente na visualização. 


## <a name="enable-ip-firewall-rules"></a>Habilitar regras de firewall IP
As regras de firewall IP são aplicadas no nível de namespace. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte. Qualquer tentativa de conexão de um endereço IP que não corresponda a uma regra de IP permitida no namespace é rejeitada como não autorizada. A resposta não menciona a regra IP. As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

### <a name="use-azure-portal"></a>Usar o portal do Azure
Esta seção mostra como usar o portal do Azure para criar regras de firewall de IP para um namespace. 

1. Navegue até o **namespace de retransmissão** no [portal do Azure](https://portal.azure.com).
2. No menu à esquerda, selecione a opção **rede** . Se você selecionar a opção **todas as redes** na seção **permitir acesso de** , o namespace de retransmissão aceitará conexões de qualquer endereço IP. Essa configuração é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0. 

    ![Firewall – opção todas as redes selecionada](./media/ip-firewall/all-networks-selected.png)
1. Para restringir o acesso a redes e endereços IP específicos, selecione a opção **redes selecionadas** . Na seção **Firewall** , siga estas etapas:
    1. Selecione a opção **Adicionar seu endereço IP do cliente** para fornecer ao IP do cliente atual o acesso ao namespace. 
    2. Para **intervalo de endereços**, insira um endereço IPv4 específico ou um intervalo de endereços IPv4 na notação CIDR. 
    3. Especifique se deseja **permitir que os serviços confiáveis da Microsoft ignorem esse firewall**. 

        ![Firewall – opção todas as redes selecionada](./media/ip-firewall/selected-networks-trusted-access-disabled.png)
3. Selecione **salvar** na barra de ferramentas para salvar as configurações. Aguarde alguns minutos para que a confirmação seja exibida nas notificações do Portal.


### <a name="use-resource-manager-template"></a>Usar modelo do Resource Manager
O modelo do Resource Manager a seguir permite adicionar uma regra de filtro IP a um namespace de retransmissão existente.

O modelo usa um parâmetro: **ipMask**, que é um único endereço IPv4 ou um bloco de endereços IP na notação CIDR. Por exemplo, na notação CIDR 70.37.104.0/24, representa os 256 endereços IPv4 de 70.37.104.0 a 70.37.104.255, em que 24 indica o número de bits de prefixo significativos para o intervalo.

> [!NOTE]
> Embora não haja nenhuma regra de negação possível, o modelo do Azure Resource Manager tem a ação padrão definida como **"Allow"**, que não restringe as conexões.
> Ao criar as regras de rede virtual ou de firewalls, devemos alterar a ***"defaultAction"***
> 
> de
> ```json
> "defaultAction": "Allow"
> ```
> para
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "relayNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Relay namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('relayNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('relayNamespaceName')]",
        "type": "Microsoft.Relay/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.Relay/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.Relay/namespaces/', parameters('relayNamespaceName'))]"
        ],
        "properties": {
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Para implantar o modelo, siga as instruções para o [Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).



## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre outros recursos relacionados à segurança de rede, consulte [segurança de rede](network-security.md).


<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-Deploy]:: 
