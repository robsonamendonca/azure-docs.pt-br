---
author: cynthn
ms.author: cynthn
ms.date: 01/23/2020
ms.topic: include
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 658910dc4291375c7b2ab22e88c599b970b885af
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80419207"
---
As imagens de VM (máquina virtual) padronizadas permitem que as organizações migrem para a nuvem e garantam a consistência nas implantações. As imagens normalmente incluem configurações predefinidas de segurança e configuração e software necessário. Configurar seu próprio pipeline de geração de imagens requer tempo, infraestrutura e configuração, mas com o construtor de imagem de VM do Azure, basta fornecer uma configuração simples que descreva sua imagem, enviá-la ao serviço, e a imagem seja criada e distribuída.
 
O construtor de imagem de VM do Azure (Construtor de imagens do Azure) permite que você comece com uma imagem do Azure Marketplace baseada em Windows ou Linux, imagens personalizadas existentes ou Red Hat Enterprise Linux (RHEL) ISO e comece a adicionar suas próprias personalizações. Como o construtor de imagem se baseia no [HashiCorp Packer](https://packer.io/), você também pode importar os scripts de provisionamento do shell do Pack existente. Você também pode especificar onde deseja que suas imagens sejam hospedadas, na [Galeria de imagens compartilhadas do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), como uma imagem gerenciada ou um VHD.

> [!IMPORTANT]
> O construtor de imagem do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="preview-features"></a>Versão prévia dos recursos

Para a versão prévia, há suporte para esses recursos:

- Criação de imagens de linha de base Golden, que inclui a segurança mínima e as configurações corporativas, e permite que os departamentos a personalizem ainda mais para suas necessidades.
- Aplicação de patch de imagens existentes, o Image Builder permitirá que você corrija continuamente as imagens personalizadas existentes.
- Conecte o Image Builder às suas redes virtuais existentes, para que você possa se conectar a servidores de configuração existentes (DSC, chefe, Puppet, etc.), compartilhamentos de arquivos ou quaisquer outros servidores/serviços roteáveis.
- A integração com a Galeria de imagens compartilhadas do Azure permite distribuir, apresentar e dimensionar imagens globalmente e fornece um sistema de gerenciamento de imagens.
- Integração com pipelines de Build de imagem existentes, basta chamar o Image Builder de seu pipeline ou usar a tarefa Simple DevOps do Azure Image Builder.
- Migre um pipeline de personalização de imagem existente para o Azure. Use seus scripts, comandos e processos existentes para personalizar imagens.
- Criação de imagens no formato VHD.
 

## <a name="regions"></a>Regiões
O serviço do construtor de imagens do Azure estará disponível para visualização nessas regiões. As imagens podem ser distribuídas fora dessas regiões.
- Leste dos EUA
- Leste dos EUA 2
- Centro-Oeste dos EUA
- Oeste dos EUA
- Oeste dos EUA 2
- Norte da Europa
- Europa Ocidental

## <a name="os-support"></a>Suporte do so
O AIB dará suporte a imagens do sistema operacional base do Azure Marketplace:
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7,6, 7,7
- CentOS 7,6, 7,7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise Multi-Session/Professional
- Windows 2016
- Windows 2019

O suporte do RHEL ISOs está sendo preterido. Examine a documentação do modelo para obter mais detalhes.

## <a name="how-it-works"></a>Como ele funciona


![Desenho conceitual do construtor de imagens do Azure](./media/virtual-machines-image-builder-overview/image-builder.png)

O construtor de imagens do Azure é um serviço do Azure totalmente gerenciado que é acessível por um provedor de recursos do Azure. O processo do construtor de imagens do Azure tem três partes principais: origem, personalizar e distribuir, que são representadas em um modelo. O diagrama a seguir mostra os componentes, com algumas de suas propriedades. 
 


**Processo do construtor de imagem** 

![Desenho conceitual do processo do construtor de imagem do Azure](./media/virtual-machines-image-builder-overview/image-builder-process.png)

1. Crie o modelo de imagem como um arquivo. JSON. Esse arquivo. JSON contém informações sobre a origem, as personalizações e a distribuição da imagem. Há vários exemplos no [repositório GitHub do Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).
1. Enviá-lo para o serviço; isso criará um artefato de modelo de imagem no grupo de recursos que você especificar. Em segundo plano, o Image Builder baixará a imagem de origem ou a ISO e os scripts, conforme necessário. Eles são armazenados em um grupo de recursos separado que é criado automaticamente em sua assinatura, no formato: IT_\<DestinationResourceGroup>_\<TemplateName>. 
1. Depois que o modelo de imagem for criado, você poderá criar a imagem. No construtor de imagem de plano de fundo usa o modelo e os arquivos de origem para criar uma VM (tamanho padrão: Standard_D1_v2), rede, IP público, NSG e\<armazenamento no\<IT_ DestinationResourceGroup>_ TemplateName> grupo de recursos.
1. Como parte da criação da imagem, o Image Builder distribui a imagem de acordo com o modelo e, em seguida, exclui os recursos adicionais\<no IT_\<DestinationResourceGroup>_ TemplateName> grupo de recursos que foi criado para o processo.


## <a name="permissions"></a>Permissões

Para permitir que o construtor de imagens de VM do Azure distribua imagens para as imagens gerenciadas ou para uma galeria de imagens compartilhadas, você precisará fornecer permissões de ' colaborador ' para o serviço "Construtor de imagem de máquina virtual do Azure" (ID do aplicativo: cf32a0cc-373c-47c9-9156-0db11f6a6dfc) nos grupos de recursos. 

Se você estiver usando uma imagem gerenciada personalizada ou uma versão de imagem existente, o construtor de imagens do Azure precisará de, no mínimo, o acesso de "leitor" a esses grupos de recursos.

Você pode atribuir acesso usando o CLI do Azure:

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName>
```

Você pode atribuir acesso usando o PowerShell:

```azurePowerShell-interactive
New-AzRoleAssignment -ObjectId ef511139-6170-438e-a6e1-763dc31bdf74 -Scope /subscriptions/$subscriptionID/resourceGroups/<distributeResoureGroupName> -RoleDefinitionName Contributor
```


Se a conta de serviço não for encontrada, isso pode significar que a assinatura em que você está adicionando a atribuição de função ainda não foi registrada para o provedor de recursos.


## <a name="costs"></a>Custos
Você incorrerá em alguns custos de computação, rede e armazenamento ao criar, criar e armazenar imagens com o Azure Image Builder. Esses custos são semelhantes aos custos incorridos na criação manual de imagens personalizadas. Para os recursos, você será cobrado com suas tarifas do Azure. 

Durante o processo de criação de imagem, os arquivos são baixados `IT_<DestinationResourceGroup>_<TemplateName>` e armazenados no grupo de recursos, o que incorrerá em um pequeno custo de armazenamento. Se você não quiser mantê-los, exclua o **modelo de imagem** após a criação da imagem.
 
O Image Builder cria uma VM usando um tamanho de VM D1v2 e o armazenamento e a rede necessários para a VM. Esses recursos durarão por último a duração do processo de compilação e serão excluídos assim que o construtor de imagem terminar de criar a imagem. 
 
O construtor de imagens do Azure distribuirá a imagem para as regiões escolhidas, o que pode incorrer em encargos de saída de rede.
 
## <a name="next-steps"></a>Próximas etapas 
 
Para experimentar o construtor de imagens do Azure, consulte os artigos para criar imagens do [Linux](../articles/virtual-machines/linux/image-builder.md) ou do [Windows](../articles/virtual-machines/windows/image-builder.md) .
 
 
