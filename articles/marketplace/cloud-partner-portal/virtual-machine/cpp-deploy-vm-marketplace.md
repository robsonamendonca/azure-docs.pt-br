---
title: Implantar uma VM do Azure Marketplace
description: Explica como implantar uma máquina virtual de uma máquina virtual pré-configurada do Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 2fb04e2e953e2bcbe479f7685b8042fdc7ea1767
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147001"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Implantar uma máquina virtual do Azure Marketplace

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover o gerenciamento de suas ofertas de máquina virtual do Azure para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções em [certificação de imagem de VM do Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) para gerenciar suas ofertas migradas.

Este artigo explica como implantar uma VM (máquina virtual) pré-configurada do Azure Marketplace usando o script do Azure PowerShell fornecido.  Esse script também expõe os pontos de extremidade HTTP e HTTPS do WinRM na VM.  O script requer que um certificado já esteja carregado no Azure Key Vault, conforme descrito em [Criar certificados para o Azure Key Vault](./cpp-create-key-vault-cert.md). 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>Modelo de implantação de VM

O modelo de implantação de VM do Azure de início rápido, que está disponível como o arquivo online [azuredeploy.json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Ele contém os seguintes parâmetros:

|  **Parâmetro**        |   **Descrição**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName    | Nome da conta de armazenamento                       |
| dnsNameForPublicIP    | Nome DNS do IP público. Precisa estar em minúsculas.    |
| adminUserName            | Nome do usuário administrador                          |
| adminPassword            | Senha do administrador                          |
| imagePublisher        | Editor de imagem                                   |
| imageOffer            | Oferta de imagem                                       |
| imageSKU                | SKU de imagem                                         |
| vmSize                | Tamanho da VM                                    |
| vmName                | Nome da VM                                    |
| vaultName                | Nome do cofre de chaves                             |
| vaultResourceGroup    | Grupo de recursos do cofre de chaves                   |
| certificateUrl        | URL do certificado, incluindo a versão no KeyVault, por exemplo `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Script de implantação

Edite o seguinte script do Azure PowerShell e execute-o para implantar a VM do Azure Marketplace especificada.

```powershell

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>Próximas etapas

Depois de implantar uma VM pré-configurada, você poderá configurar e acessar as soluções e os serviços que ela contém ou usá-la para desenvolvimento futuro. 
