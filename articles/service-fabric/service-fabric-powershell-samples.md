---
title: Exemplos do Azure PowerShell – Service Fabric
description: Saiba mais sobre a criação e o gerenciamento de clusters, aplicativos e serviços do Azure Service Fabric usando o PowerShell.
ms.topic: sample
ms.date: 11/29/2018
ms.custom: mvc
ms.openlocfilehash: f9ba1b4833b3aff13284a68c23f398ea47ef2ae2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75645643"
---
# <a name="azure-service-fabric-powershell-samples"></a>Amostras do Azure Service Fabric no PowerShell

A tabela a seguir contém links para exemplos de scripts do PowerShell que criam e gerenciam clusters, aplicativos e serviços do Service Fabric.

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Criar cluster** ||
| [Criar um cluster (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Cria um cluster do Azure Service Fabric. |
| **Gerenciar o cluster, os nós e a infraestrutura** ||
| [Adicionar um certificado de aplicativo](./scripts/service-fabric-powershell-add-application-certificate.md)| Cria um certificado X509 para o Key Vault e o implanta em um conjunto de dimensionamento de máquinas virtuais no cluster. |
| [Atualizar o intervalo da porta RDP nas VMs do cluster](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Altera o intervalo de porta do RDP nas VMs do nó de cluster em um cluster implantado.|
| [Atualizar o usuário administrador e a senha para as VMs do nó de cluster](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Atualiza o nome de usuário administrador e a senha para as VMs do nó de cluster. |
| [Abrir uma porta no balanceador de carga](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Abra uma porta de aplicativo no balanceador de carga do Azure para permitir o tráfego de entrada em uma porta específica. |
| [Criar uma regra de grupo de segurança de rede de entrada](./scripts/service-fabric-powershell-add-nsg-rule.md) | Crie uma regra de grupo de segurança de rede de entrada para permitir o tráfego de entrada para o cluster em uma porta específica. |
| **Gerenciar aplicativos** ||
| [Implantar um aplicativo](./scripts/service-fabric-powershell-deploy-application.md)| Implantar um aplicativo em um cluster.|
| [Atualizar um aplicativo](./scripts/service-fabric-powershell-upgrade-application.md)| Atualize um aplicativo.|
| [Remover um aplicativo](./scripts/service-fabric-powershell-remove-application.md)| Remover um aplicativo de um cluster.|
