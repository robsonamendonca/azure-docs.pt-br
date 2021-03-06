---
title: Configurar o suporte de identidade gerenciada para um novo cluster Service Fabric
description: Veja como habilitar o suporte a identidades gerenciadas em um novo cluster de Service Fabric do Azure
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: dd0cbd3251185a7831852ead47ca0b120126cf55
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415664"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster"></a>Configurar o suporte de identidade gerenciada para um novo cluster Service Fabric

Para usar [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) em seus aplicativos Service Fabric, primeiro habilite o *serviço de token de identidade gerenciado* no cluster. Esse serviço é responsável pela autenticação de Service Fabric aplicativos usando suas identidades gerenciadas e pela obtenção de tokens de acesso em seu nome. Quando o serviço estiver habilitado, você poderá vê-lo em Service Fabric Explorer na seção **sistema** no painel esquerdo, executando sob o nome **Fabric:/System/ManagedIdentityTokenService** ao lado de outros serviços do sistema.

> [!NOTE]
> O Service Fabric Runtime versão 6.5.658.9590 ou superior é necessário para habilitar o **serviço de token de identidade gerenciado**.  

## <a name="enable-the-managed-identity-token-service"></a>Habilitar o serviço de token de identidade gerenciado

Para habilitar o serviço de token de identidade gerenciado no momento da criação do cluster, adicione o seguinte trecho ao seu modelo de Azure Resource Manager de cluster:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

## <a name="errors"></a>Errors

Se a implantação falhar com essa mensagem, isso significa que o cluster não está na versão necessária do Service Fabric (o tempo de execução mínimo com suporte é 6,5 CU2):


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>Artigos relacionados

* Examinar o [suporte de identidade gerenciada](./concepts-managed-identity.md) no Azure Service Fabric

* [Habilitar o suporte de identidade gerenciada em um cluster existente do Azure Service Fabric](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Próximas etapas

* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implantar um aplicativo de Service Fabric do Azure com uma identidade gerenciada atribuída pelo usuário](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Aproveite a identidade gerenciada de um aplicativo Service Fabric do código de serviço](./how-to-managed-identity-service-fabric-app-code.md)
* [Conceder a um aplicativo de Service Fabric do Azure acesso a outros recursos do Azure](./how-to-grant-access-other-resources.md)