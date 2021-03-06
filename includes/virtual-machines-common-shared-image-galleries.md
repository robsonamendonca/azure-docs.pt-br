---
title: incluir arquivo
description: incluir arquivo
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 04/16/2020
ms.author: akjosh
ms.custom: include file
ms.openlocfilehash: 5cb3e6d53f6840b8f4e535976739c188daed18b2
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789022"
---
A Galeria de imagens compartilhadas é um serviço que ajuda você a criar estrutura e organização em suas imagens gerenciadas. As galerias de imagens compartilhadas fornecem:

- Replicação global gerenciada de imagens.
- Controle de versão e agrupamento de imagens para facilitar o gerenciamento.
- Imagens altamente disponíveis com contas de ZRS (armazenamento com redundância de zona) em regiões que dão suporte a Zonas de Disponibilidade. O ZRS oferece maior resiliência contra falhas zonais.
- Suporte ao armazenamento Premium (Premium_LRS).
- Compartilhamento entre assinaturas e até mesmo entre locatários de Active Directory (AD), usando o RBAC.
- Dimensionando suas implantações com réplicas de imagem em cada região.

Usando uma Galeria de Imagens Compartilhadas, é possível compartilhar suas imagens com diferentes usuários, entidades de serviço ou grupos do AD dentro de sua organização. As imagens compartilhadas podem ser replicadas para várias regiões para dimensionar suas implantações mais rápido.

Uma imagem é uma cópia de uma VM completa (incluindo discos de dados anexados) ou apenas do disco do sistema operacional, dependendo de como ele é criado. Quando você cria uma VM a partir da imagem, uma cópia dos VHDs na imagem é usada para criar os discos da nova VM. A imagem permanece no armazenamento e pode ser usada repetidamente para criar novas VMs.

Se você tiver um grande número de imagens que precisa manter e desejar disponibilizá-las em toda a empresa, poderá usar uma galeria de imagens compartilhadas como um repositório. 

O recurso Galeria de Imagens Compartilhadas tem vários tipos de recursos:

| Recurso | Descrição|
|----------|------------|
| **Origem da imagem** | Esse é um recurso que pode ser usado para criar uma **versão de imagem** em uma galeria de imagens. Uma origem de imagem pode ser uma VM do Azure existente que seja [generalizada ou especializada](#generalized-and-specialized-images), uma imagem gerenciada, um instantâneo ou uma versão de imagem em outra galeria de imagens. |
| **Galeria de imagens** | Como o Azure Marketplace, uma **galeria de imagens** é um repositório para gerenciar e compartilhar imagens, mas você controla quem tem acesso. |
| **Definição da imagem** | As definições de imagem são criadas dentro de uma galeria e contêm informações sobre a imagem e os requisitos para usá-las internamente. Isso inclui se a imagem é Windows ou Linux, notas sobre a versão e requisitos mínimos e máximos de memória. É uma definição de um tipo de imagem. |
| **Versão da imagem** | Uma **versão da imagem** é usada para criar uma VM ao usar uma galeria. Você pode ter diversas versões de uma imagem conforme necessário para seu ambiente. Como uma imagem gerenciada, quando você usa uma **versão da imagem** para criar uma VM, a versão da imagem é usada para criar novos discos para a VM. Versões de imagem podem ser usadas várias vezes. |

<br>

![Gráfico mostrando como você pode ter várias versões de uma imagem na sua galeria](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definições de imagem

As definições de imagem são um agrupamento lógico de versões de uma imagem. A definição de imagem contém informações sobre por que a imagem foi criada, em qual sistema operacional ela é e outras informações sobre como usar a imagem. Uma definição de imagem é como um plano para todos os detalhes sobre a criação de uma imagem específica. Você não implanta uma VM a partir de uma definição de imagem, mas das versões de imagem criadas com base na definição.

Há três parâmetros para cada definição de imagem que são usados em combinação- **Publicador**, **oferta** e **SKU**. Eles são usados para localizar uma definição de imagem específica. Você pode ter versões de imagem que compartilham um ou dois, mas não todos os três valores.  Por exemplo, aqui estão três definições de imagem e seus valores:

|Definição de imagem|Publicador|Oferta|Sku|
|---|---|---|---|
|myImage1|Contoso|Finanças|Back-end|
|myImage2|Contoso|Finanças|Front-end|
|myImage3|Testando|Finanças|Front-end|

Todos os três têm conjuntos exclusivos de valores. O formato é semelhante a como você pode especificar atualmente Publicador, oferta e SKU para [imagens do Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) em Azure PowerShell para obter a versão mais recente de uma imagem do Marketplace. Cada definição de imagem precisa ter um conjunto exclusivo desses valores.

Veja a seguir outros parâmetros que podem ser definidos na definição de imagem para que você possa controlar seus recursos com mais facilidade:

* Estado do sistema operacional – você pode definir o estado do so como [generalizado ou especializado](#generalized-and-specialized-images).
* Sistema operacional – pode ser Windows ou Linux.
* Descrição – use a descrição para fornecer informações mais detalhadas sobre por que a definição da imagem existe. Por exemplo, você pode ter uma definição de imagem para o servidor front-end que tem o aplicativo pré-instalado.
* EULA – pode ser usado para apontar para um contrato de licença de usuário final específico para a definição de imagem.
* Política de privacidade e notas de versão – armazenar notas de versão e declarações de privacidade no armazenamento do Azure e fornecer um URI para acessá-las como parte da definição da imagem.
* Data de fim da vida útil – anexe uma data de fim da vida útil à sua definição de imagem para poder usar a automação para excluir definições de imagem antigas.
* Marca-você pode adicionar marcas ao criar a definição de imagem. Para obter mais informações sobre marcas, consulte [usando marcas para organizar seus recursos](../articles/azure-resource-manager/management/tag-resources.md)
* Recomendações de memória e vCPU mínimas e máximas – se sua imagem tiver recomendações de memória e vCPU, você poderá anexar essas informações à definição de imagem.
* Tipos de disco não permitidos-você pode fornecer informações sobre as necessidades de armazenamento para sua VM. Por exemplo, se a imagem não for adequada para discos HDD padrão, você as adicionará à lista de não permitir.
* Geração do Hyper-V-você pode especificar se a imagem foi criada a partir de um VHD do Hyper-V de Gen 1 ou Gen 2.

## <a name="generalized-and-specialized-images"></a>Imagens generalizadas e especializadas

Há dois Estados do sistema operacional com suporte pela galeria de imagens compartilhadas. Normalmente, as imagens exigem que a VM usada para criar a imagem tenha sido generalizada antes de tirar a imagem. Generalizar é um processo que remove informações específicas do computador e do usuário da VM. Para o Windows, o Sysprep também é usado. Para o Linux, você pode [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` usar waagent `-deprovision+user` ou parâmetros.

VMs especializadas não passaram por um processo para remover informações e contas específicas do computador. Além disso, as VMs criadas a partir de imagens especializadas não têm uma `osProfile` associada a elas. Isso significa que imagens especializadas terão algumas limitações, além de alguns benefícios.

- As VMs e os conjuntos de dimensionamento criados a partir de imagens especializadas podem estar ativos e em execução mais rápido. Como eles são criados a partir de uma fonte que já foi pela primeira inicialização, as VMs criadas a partir dessas imagens são inicializadas mais rapidamente.
- As contas que poderiam ser usadas para fazer logon na VM também podem ser usadas em qualquer VM criada usando a imagem especializada que é criada a partir dessa VM.
- As VMs terão o **nome do computador** da VM da qual a imagem foi obtida. Você deve alterar o nome do computador para evitar colisões.
- O `osProfile` é como algumas informações confidenciais são passadas para a VM, `secrets`usando. Isso pode causar problemas ao usar o keyvault, o WinRM e outras `secrets` funcionalidades que `osProfile`o usa no. Em alguns casos, você pode usar as identidades de serviço gerenciadas (MSI) para contornar essas limitações.

## <a name="regional-support"></a>Suporte regional

As regiões de origem são listadas na tabela a seguir. Todas as regiões públicas podem ser regiões de destino, mas para replicação na Austrália Central e na Austrália Central 2, você precisa ter sua assinatura na lista de permissões. Para solicitar a inclusão na lista de permissões, acesse: https://azure.microsoft.com/global-infrastructure/australia/contact/


| Regiões de origem        |                   |                    |                    |
| --------------------- | ----------------- | ------------------ | ------------------ |
| Austrália Central     | Leste da China        | Sul da Índia        | Europa Ocidental        |
| Austrália Central 2   | Leste da China 2      | Sudeste Asiático     | Sul do Reino Unido           |
| Leste da Austrália        | Norte da China       | Leste do Japão         | Oeste do Reino Unido            |
| Sudeste da Austrália   | Norte da China 2     | Oeste do Japão         | DoD Central dos EUA     |
| Sul do Brasil          | Leste da Ásia         | Coreia Central      | DoD do Leste dos EUA        |
| Canadá Central        | Leste dos EUA           | Sul da Coreia        | Governo dos EUA do Arizona     |
| Leste do Canadá           | Leste dos EUA 2         | Centro-Norte dos EUA   | Governo dos EUA do Texas       |
| Índia Central         | Leste dos EUA 2 EUAP    | Norte da Europa       | Gov. dos EUA – Virgínia    |
| Centro dos EUA            | França Central    | Centro-Sul dos Estados Unidos   | Oeste da Índia         |
| EUA Central EUAP       | Sul da França      | Centro-Oeste dos EUA    | Oeste dos EUA            |
|                       |                   |                    | Oeste dos EUA 2          |



## <a name="limits"></a>limites 

Há limites, por assinatura, para implantar recursos usando galerias de imagens compartilhadas:
- 100 galerias de imagens compartilhadas, por assinatura, por região
- 1.000 definições de imagem, por assinatura, por região
- 10.000 versões de imagem, por assinatura, por região
- 10 réplicas de versão de imagem, por assinatura, por região
- Qualquer disco anexado à imagem deve ser menor ou igual a 1 TB de tamanho

Para obter mais informações, consulte [verificar o uso de recursos em relação aos limites](https://docs.microsoft.com/azure/networking/check-usage-against-limits) para obter exemplos de como verificar seu uso atual.
 
## <a name="scaling"></a>Scaling
A Galeria de Pesquisa de Imagem permite que você especifique o número de réplicas que você deseja que o Azure mantenha das imagens. Isso ajuda em cenários de implantação de várias VMs, já que as implantações de VM podem ser distribuídas para diferentes réplicas, reduzindo a chance de o processamento de criação de instância ser limitado devido à sobrecarga de uma única réplica.

Com a Galeria de imagens compartilhadas, agora você pode implantar até uma instância de VM 1.000 em um conjunto de dimensionamento de máquinas virtuais (acima de 600 com imagens gerenciadas). As réplicas de imagem oferecem melhor desempenho, confiabilidade e consistência da implantação. Você pode definir uma contagem de réplicas diferente em cada região de destino, com base nas necessidades de escala para a região. Como cada réplica é uma cópia profunda da imagem, isso ajuda a dimensionar suas implantações linearmente com cada réplica extra. Embora não entendamos que duas imagens ou regiões são as mesmas, aqui está nossa orientação geral sobre como usar réplicas em uma região:

- Para implantações de VMSS (conjunto de dimensionamento de máquinas virtuais) – para cada 20 VMs que você cria simultaneamente, recomendamos que você mantenha uma réplica. Por exemplo, se você estiver criando VMs 120 simultaneamente usando a mesma imagem em uma região, sugerimos que você mantenha pelo menos 6 réplicas da sua imagem. 
- Para implantações de VMSS (conjunto de dimensionamento de máquinas virtuais) – para cada implantação de conjunto de dimensionamento com até 600 instâncias, recomendamos que você mantenha pelo menos uma réplica. Por exemplo, se você estiver criando cinco conjuntos de dimensionamento simultaneamente, cada um com instâncias de VM 600 usando a mesma imagem em uma única região, sugerimos que você mantenha pelo menos 5 réplicas de sua imagem. 

Sempre recomendamos que você provisione o número de réplicas devido a fatores como tamanho da imagem, conteúdo e tipo de so.

![Gráfico mostrando como você pode dimensionar imagens](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Tornar suas imagens altamente disponíveis

O [armazenamento com redundância de zona do Azure (ZRS)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) fornece resiliência contra uma falha de zona de disponibilidade na região. Com a disponibilidade geral da Galeria de imagens compartilhadas, você pode optar por armazenar suas imagens em contas do ZRS em regiões com Zonas de Disponibilidade. 

Você também pode escolher o tipo de conta para cada uma das regiões de destino. O tipo de conta de armazenamento padrão é Standard_LRS, mas você pode escolher Standard_ZRS para regiões com Zonas de Disponibilidade. Verifique a disponibilidade regional do ZRS [aqui](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs).

![Gráfico mostrando ZRS](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replicação
A Galeria de Imagens Compartilhadas também permite replicar imagens para outras regiões do Azure automaticamente. Cada versão de imagem compartilhada pode ser replicada para diferentes regiões, dependendo do que faz sentido para sua organização. Um exemplo é sempre replicar a imagem mais recente em várias regiões, enquanto todas as versões mais antigas só estão disponíveis em uma região. Isso pode ajudar a economizar nos custos de armazenamento das versões de imagem compartilhada. 

As regiões para as quais uma versão de Imagem compartilhada é replicada podem ser atualizadas após o horário de criação. O tempo necessário para replicar em diferentes regiões depende da quantidade de dados copiados e do número de regiões para as quais a versão é replicada. Isso pode levar algumas horas em alguns casos. Enquanto a replicação está em andamento, você pode exibir o status da replicação por região. Depois que a replicação de imagem for concluída em uma região, você poderá implantar uma VM ou um conjunto de dimensionamento usando essa versão de imagem na região.

![Gráfico mostrando como você pode replicar imagens](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Acesso

Como a Galeria de imagens compartilhadas, a definição de imagem e a versão de imagem são todos os recursos, elas podem ser compartilhadas usando controles nativos do Azure RBAC internos. Usando o RBAC, você pode compartilhar esses recursos para outros usuários, entidades de serviço e grupos. Você pode até compartilhar o acesso a pessoas fora do locatário em que foram criadas. Quando um usuário tem acesso à versão da imagem compartilhada, ele pode implantar uma VM ou um conjunto de dimensionamento de máquinas virtuais.  Aqui está a matriz de compartilhamento que ajuda a entender ao que o usuário obtém acesso:

| Compartilhado com o usuário     | Galeria de imagens compartilhadas | Definição de imagem | Versão da imagem |
|----------------------|----------------------|--------------|----------------------|
| Galeria de imagens compartilhadas | Sim                  | Sim          | Sim                  |
| Definição de imagem     | Não                   | Sim          | Sim                  |

É recomendável compartilhar no nível da galeria para obter a melhor experiência. Não recomendamos o compartilhamento de versões de imagem individuais. Para obter mais informações sobre o RBAC, consulte [gerenciar o acesso aos recursos do Azure usando o RBAC](../articles/role-based-access-control/role-assignments-portal.md).

As imagens também podem ser compartilhadas, em escala, mesmo entre locatários usando um registro de aplicativo multilocatário. Para obter mais informações sobre como compartilhar imagens entre locatários, consulte [compartilhar imagens de VM de galeria entre locatários do Azure](../articles/virtual-machines/linux/share-images-across-tenants.md).

## <a name="billing"></a>Cobrança
Não há custo adicional para usar o serviço de Galeria de Imagens Compartilhadas. Você será cobrado pelos seguintes recursos:
- Custos de armazenamento do armazenamento das versões de imagem compartilhada. O custo depende do número de réplicas da versão da imagem e do número de regiões para as quais a versão é replicada. Por exemplo, se você tiver duas imagens e ambas forem replicadas para três regiões, você será cobrado por 6 discos gerenciados com base em seu tamanho. Para obter mais informações, consulte [preços de Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).
- Encargos de saída de rede para replicação da primeira versão de imagem da região de origem para as regiões replicadas. As réplicas subsequentes são tratadas dentro da região, portanto, não há encargos adicionais. 

## <a name="updating-resources"></a>Atualização de recursos

Depois de criado, você pode fazer algumas alterações nos recursos da Galeria de imagens. Elas são limitadas a:
 
Galeria de imagens compartilhadas:
- Descrição

definição da imagem:
- vCPUs recomendadas
- Memória recomendada
- Descrição
- Data de fim da vida útil

Versão da imagem:
- Contagem de réplicas regionais
- Regiões de destino
- Excluir da versão mais recente
- Data de fim da vida útil

## <a name="sdk-support"></a>Suporte a SDK

Os seguintes SDKs dão suporte à criação de Galerias de Imagens Compartilhadas:

- [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/virtualmachines/management?view=azure-dotnet)
- [Java](https://docs.microsoft.com/java/azure/?view=azure-java-stable)
- [Node.js](https://docs.microsoft.com/javascript/api/@azure/arm-compute)
- [Python](https://docs.microsoft.com/python/api/overview/azure/virtualmachines?view=azure-python)
- [Go](https://docs.microsoft.com/azure/go/)

## <a name="templates"></a>Modelos

Você pode criar um recurso de Galeria de Imagens Compartilhadas usando modelos. Há vários Modelos de Início Rápido do Azure disponíveis: 

- [Criar uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Criar uma definição de imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Criar uma versão de imagem em uma Galeria de Imagens Compartilhadas](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Criar uma VM por meio de uma versão de imagem](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

## <a name="frequently-asked-questions"></a>Perguntas frequentes 

* [Como posso listar todos os recursos da Galeria de Imagens Compartilhadas entre assinaturas?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [Posso mover minha imagem existente para a galeria de imagens compartilhadas?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [Posso criar uma versão da imagem de um disco especializado?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Posso mover o recurso da Galeria de imagens compartilhadas para uma assinatura diferente depois que ela tiver sido criada?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Posso replicar minhas versões de imagem entre nuvens, como Azure China 21Vianet ou Azure Alemanha ou nuvem do Azure governamental?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [Posso replicar minhas versões de imagem entre assinaturas?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Posso compartilhar versões de imagens entre os locatários do Azure AD?](#can-i-share-image-versions-across-azure-ad-tenants)
* [Quanto tempo leva para replicar as versões de imagem entre todas as regiões de destino?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [Qual é a diferença entre região de origem e região de destino?](#what-is-the-difference-between-source-region-and-target-region)
* [Como faço para especificar a região de origem ao criar a versão da imagem?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [Como faço para especificar o número de réplicas de versão da imagem a serem criadas em cada região?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [Posso criar a Galeria de imagens compartilhadas em um local diferente daquele para a definição da imagem e a versão da imagem?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Quais são os encargos para usar a Galeria de Imagens Compartilhadas?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Qual versão de API devo usar para criar a Galeria de imagens compartilhada e a imagem e a versão da imagem?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [Qual versão de API devo usar para criar uma VM compartilhada ou um conjunto de dimensionamento de máquinas virtuais da versão da imagem?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)
* [Posso atualizar meu conjunto de dimensionamento de máquinas virtuais criado usando a imagem gerenciada para usar imagens da Galeria de imagens compartilhadas?]

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>Como posso listar todos os recursos da Galeria de Imagens Compartilhadas entre assinaturas?

Para listar todos os recursos da Galeria de imagens compartilhadas nas assinaturas às quais você tem acesso na portal do Azure, siga as etapas abaixo:

1. Abra o [portal do Azure](https://portal.azure.com).
1. Role a página e selecione **todos os recursos**.
1. Selecione todas as assinaturas sob as quais você gostaria de listar todos os recursos.
1. Procure recursos do tipo **Galeria de imagens compartilhadas**,.
  
Para listar todos os recursos da Galeria de Imagens Compartilhada entre assinaturas para as quais você tem permissões, use o seguinte comando na CLI do Azure:

```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
```

Para obter mais informações, consulte **gerenciar recursos da Galeria** usando o [CLI do Azure](../articles/virtual-machines/update-image-resources-cli.md) ou o [PowerShell](../articles/virtual-machines/update-image-resources-powershell.md).

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>Posso mover minha imagem existente para a galeria de imagens compartilhadas?
 
Sim. Há três cenários com base nos tipos de imagens que você pode ter.

 Cenário 1: se você tiver uma imagem gerenciada, poderá criar uma definição de imagem e a versão da imagem usando essa definição. Para obter mais informações, consulte **migrar de uma imagem gerenciada para uma versão de imagem** usando o [CLI do Azure](../articles/virtual-machines/image-version-managed-image-cli.md) ou o [PowerShell](../articles/virtual-machines/image-version-managed-image-powershell.md).

 Cenário 2: se você tiver uma imagem não gerenciada, poderá criar uma imagem gerenciada a partir dela e, em seguida, criar uma definição de imagem e uma versão de imagem a partir dela. 

 Cenário 3: se você tiver um VHD em seu sistema de arquivos local, precisará carregar o VHD em uma imagem gerenciada e, em seguida, poderá criar uma definição de imagem e uma versão de imagem a partir dela.

- Se o VHD for de uma VM do Windows, consulte [carregar um VHD](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed).
- Se o VHD for para uma VM do Linux, veja [Carregar um VHD](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd#option-1-upload-a-vhd)

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>Posso criar uma versão da imagem de um disco especializado?

Sim, o suporte para discos especializados como imagens está em versão prévia. Você só pode criar uma VM de uma imagem especializada usando o portal, o PowerShell ou a API. 


Use o [PowerShell para criar uma imagem de uma VM especializada](../articles/virtual-machines/image-version-vm-powershell.md).

Use o portal para criar um [Windows](../articles/virtual-machines/linux/shared-images-portal.md) ou [Linux] (.. /articles/Virtual-Machines/Linux/Shared-images-Portal.MD). 


### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Posso mover o recurso da Galeria de imagens compartilhadas para uma assinatura diferente depois que ela tiver sido criada?

Não, você não pode mover o recurso da Galeria de imagens compartilhadas para uma assinatura diferente. Você pode replicar as versões da imagem na galeria para outras regiões ou copiar uma imagem de outra galeria usando o [CLI do Azure](../articles/virtual-machines/image-version-another-gallery-cli.md) ou o [PowerShell](../articles/virtual-machines/image-version-another-gallery-powershell.md).

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Posso replicar minhas versões de imagem entre nuvens, como Azure China 21Vianet ou Azure Alemanha ou nuvem do Azure governamental?

Não, você não pode replicar as versões de imagem entre nuvens.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>Posso replicar minhas versões de imagem entre assinaturas?

Não, você não pode replicar as versões de imagem entre regiões em uma assinatura e usá-las em outras assinaturas por meio de RBAC.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Posso compartilhar versões de imagens entre os locatários do Azure AD? 

Sim, você pode usar o RBAC para compartilhar com indivíduos entre locatários. Mas, para compartilhar em escala, consulte "compartilhar imagens da Galeria entre locatários do Azure" usando o [PowerShell](../articles/virtual-machines/windows/share-images-across-tenants.md) ou a [CLI](../articles/virtual-machines/linux/share-images-across-tenants.md).

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>Quanto tempo leva para replicar as versões de imagem entre todas as regiões de destino?

O tempo de replicação de versão de imagem depende inteiramente do tamanho da imagem e do número de regiões para as quais ela está sendo replicada. No entanto, como uma melhor prática, é recomendável manter a imagem pequena e as regiões de origem e destino perto para melhores resultados. Você pode verificar o status da replicação usando o sinalizador -ReplicationStatus.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>Qual é a diferença entre região de origem e região de destino?

Região de origem é a região em que sua versão da imagem será criada e regiões de destino são as regiões em que uma cópia da sua versão da imagem será armazenada. Para cada versão de imagem, você pode ter apenas uma região de origem. Além disso, passe o local da região de origem como uma das regiões de destino ao criar uma versão da imagem.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>Como faço para especificar a região de origem ao criar a versão da imagem?

Durante a criação de uma versão de imagem, você pode usar a marca **--location** na CLI e a marca **-Location** no PowerShell para especificar a região de origem. Verifique se a imagem gerenciada que você está usando como imagem base para criar a versão da imagem está no local em que você pretende criar a versão da imagem. Além disso, passe o local da região de origem como uma das regiões de destino ao criar uma versão da imagem.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>Como faço para especificar o número de réplicas de versão da imagem a serem criadas em cada região?

Há duas maneiras de especificar o número de réplicas de versão da imagem a serem criadas em cada região:
 
1. A contagem de réplica regionais que especifica o número de réplicas que você deseja criar por região. 
2. A contagem de réplicas comuns, que é a contagem padrão por região caso a contagem de réplicas regionais não seja especificada. 

Para especificar a contagem de réplica regional, passe o local junto com o número de réplicas que você deseja criar nessa região: "South EUA Central = 2". 

Se a contagem de réplicas regionais não for especificada com cada local, o número de réplicas padrão será a contagem de réplicas comuns que você especificou. 

Para especificar a contagem de réplicas comuns na CLI, use o argumento **--replica-count** no comando `az sig image-version create`.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>Posso criar a Galeria de imagens compartilhadas em um local diferente daquele para a definição da imagem e a versão da imagem?

Sim, isso é possível. Mas, como prática recomendada, incentivamos você a manter o grupo de recursos, a Galeria de imagens compartilhadas, a definição da imagem e a versão da imagem no mesmo local.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>Quais são os encargos para usar a Galeria de Imagens Compartilhadas?

Não há encargos para usar o serviço Galeria de Imagens Compartilhadas, exceto pelos encargos de armazenamento para armazenar as versões de imagem e os encargos de saída de rede para replicar as versões de imagem da região de origem para as regiões de destino.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Qual versão de API devo usar para criar a Galeria de imagens compartilhada e a imagem e a versão da imagem?

Para trabalhar com galerias de imagens compartilhadas, definições de imagem e versões de imagem, é recomendável usar a API versão 2018-06-01. O ZRS (armazenamento com redundância de zona) requer a versão 2019-03-01 ou posterior.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>Qual versão de API devo usar para criar uma VM compartilhada ou um conjunto de dimensionamento de máquinas virtuais da versão da imagem?

Para implantações de conjunto de dimensionamento de máquina virtual e VM usando uma versão de imagem, recomendamos usar a versão da API 2018-04-01 ou superior.

### <a name="can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images"></a>Posso atualizar meu conjunto de dimensionamento de máquinas virtuais criado usando a imagem gerenciada para usar imagens da Galeria de imagens compartilhadas?

Sim, você pode atualizar a referência de imagem do conjunto de dimensionamento de uma imagem gerenciada para uma imagem da Galeria de imagens compartilhada, desde que o tipo de so, geração do Hyper-V e o layout do disco de dados correspondam entre as imagens. 
