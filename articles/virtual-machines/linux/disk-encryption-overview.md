---
title: Habilitar o Azure Disk Encryption para VMs do Linux
description: Este artigo fornece instruções sobre como habilitar a criptografia de disco Microsoft Azure para VMs do Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: f75e5c856e05cc5ce53598849a7cb11ed059827a
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838851"
---
# <a name="azure-disk-encryption-for-linux-vms"></a>Azure Disk Encryption para VMs Linux 

O Azure Disk Encryption ajuda a proteger seus dados e a atender aos compromissos de conformidade e segurança de sua organização. Ele usa o recurso [DM-cript](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o sistema operacional e os discos de dados das VMs (máquinas virtuais) do Azure e é integrado com o [Azure Key Vault](../../key-vault/index.yml) para ajudá-lo a controlar e gerenciar as chaves e os segredos de criptografia de disco. 

Se você usar a [central de segurança do Azure](../../security-center/index.yml), você será alertado se tiver VMs que não estão criptografadas. Esses alertas são mostrados como Alta Gravidade e a recomendação é criptografar essas VMs.

![Alerta de criptografia de disco na Central de Segurança do Azure](media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Se você usou anteriormente Azure Disk Encryption com o Azure AD para criptografar uma VM, você deve continuar a usar essa opção para criptografar sua VM. Consulte [Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-overview-aad.md) para obter detalhes. 
> - Determinadas recomendações podem aumentar o uso de recursos de dados, rede ou computação, resultando em custos adicionais de licença ou inscrição. Você deve ter uma assinatura ativa válida do Azure para criar recursos no Azure nas regiões com suporte.
> - Atualmente, as VMs de geração 2 não dão suporte a Azure Disk Encryption. Consulte [suporte para VMs de geração 2 no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) para obter detalhes.

Você pode aprender os conceitos básicos do Azure Disk Encryption para Linux em apenas alguns minutos com a [criação e a criptografia de uma VM do Linux com CLI do Azure início rápido](disk-encryption-cli-quickstart.md) ou a [criação e a criptografia de uma VM do Linux com o guia de início rápido do Azure PowerShell](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>VMs e sistemas operacionais com suporte

### <a name="supported-vms"></a>VMs com suporte

As VMs do Linux estão disponíveis em [vários tamanhos](sizes.md). O Azure Disk Encryption não está disponível em [VMs básicas da série A](https://azure.microsoft.com/pricing/details/virtual-machines/series/)ou em máquinas virtuais que não atendem a esses requisitos mínimos de memória:

| Máquina virtual | Requisito mínimo de memória |
|--|--|
| VMs do Linux ao criptografar apenas os volumes de dados| 2 GB |
| VMs do Linux ao criptografar volumes de dados e de so e onde o uso do sistema de arquivos raiz (/) é de 4 GB ou menos | 8 GB |
| VMs do Linux ao criptografar volumes de dados e de so e onde o uso do sistema de arquivos raiz (/) é maior que 4 GB | O uso do sistema de arquivos raiz * 2. Por exemplo, os 16 GB de uso do sistema de arquivos raiz exigem pelo menos 32 GB de RAM |

Depois que o processo de criptografia de disco do sistema operacional for concluído em máquinas virtuais Linux, a VM poderá ser configurada para ser executada com menos memória. 

Azure Disk Encryption também está disponível para VMs com armazenamento Premium.

Azure Disk Encryption não está disponível em [VMs de geração 2](generation-2.md#generation-1-vs-generation-2-capabilities)) e em [VMs da série Lsv2](../lsv2-series.md)). Para obter mais exceções, consulte [Azure Disk Encryption: cenários sem suporte](disk-encryption-linux.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

Azure Disk Encryption tem suporte em um subconjunto de [distribuições Linux endossadas pelo Azure](endorsed-distros.md), que é, em si, um subconjunto de todas as distribuições possíveis do servidor Linux.

![Diagrama de Venn das distribuições do servidor Linux que dão suporte ao Azure Disk Encryption](./media/disk-encryption/ade-supported-distros.png)

As distribuições do servidor Linux que não são endossadas pelo Azure não dão suporte a Azure Disk Encryption; daqueles que são endossados, somente as seguintes distribuições e versões são compatíveis Azure Disk Encryption:

| Publicador | Oferta | SKU | URN | Tipo de volume suportado para criptografia |
| --- | --- |--- | --- |
| Canônico | Ubuntu | 18.04-LTS | Canonical: UbuntuServer: 18.04-LTS: mais recente | SO e disco de dados |
| Canônico | Ubuntu 18.04 | 18, 4-DAILY-LTS | Canônico: UbuntuServer: 18.04-DAILY-LTS: mais recente | SO e disco de dados |
| Canônico | Ubuntu 16.04 | 16.04-LTS-DIÁRIO | Canônico: UbuntuServer: 16.04-DAILY-LTS: mais recente | SO e disco de dados |
| Canônico | Ubuntu 14.04.5</br>[com kernel ajustado para Azure atualizado para 4.15 ou posterior](disk-encryption-troubleshooting.md) | 14.04.5-LTS | Canonical: UbuntuServer: 14.04.5-LTS: mais recente | SO e disco de dados |
| Canônico | Ubuntu 14.04.5</br>[com kernel ajustado para Azure atualizado para 4.15 ou posterior](disk-encryption-troubleshooting.md) | 14.04.5-LTS-DIÁRIO | Canônico: UbuntuServer: 14.04.5-DAILY-LTS: mais recente | SO e disco de dados |
| RedHat | RHEL 7,7 | 7,7 | RedHat: RHEL: 7.7: mais recente | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 7,7 | 7-RAW | RedHat: RHEL: 7-RAW: mais recente | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 7,7 | 7-LVM | RedHat: RHEL: 7-LVM: mais recente | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 7,6 | 7.6 | RedHat: RHEL: 7.6: mais recente | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 7.5 | 7.5 | RedHat: RHEL: 7.5: mais recente | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 7.4 | 7.4 | RedHat: RHEL: 7.4: mais recente | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 7.3 | 7.3 | RedHat: RHEL: 7.3: mais recente | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 7.2 | 7.2 | RedHat: RHEL: 7.2: mais recente | Sistema operacional e disco de dados (veja a observação abaixo) |
| RedHat | RHEL 6,8 | 6,8 | RedHat: RHEL: 6.8: mais recente | Disco de dados (veja a observação abaixo) |
| RedHat | RHEL 6,7 | 6.7 | RedHat: RHEL: 6.7: mais recente | Disco de dados (veja a observação abaixo) |
| OpenLogic | CentOS 7,7 | 7,7 | OpenLogic: CentOS: 7.7: mais recente | SO e disco de dados |
| OpenLogic | CentOS 7,7 | 7-LVM | OpenLogic: CentOS: 7-LVM: mais recente | SO e disco de dados |
| OpenLogic | CentOS 7,6 | 7.6 | OpenLogic: CentOS: 7.6: mais recente | SO e disco de dados |
| OpenLogic | CentOS 7.5 | 7.5 | OpenLogic: CentOS: 7.5: mais recente | SO e disco de dados |
| OpenLogic | CentOS 7.4 | 7.4 | OpenLogic: CentOS: 7.4: mais recente | SO e disco de dados |
| OpenLogic | CentOS 7.3 | 7.3 | OpenLogic: CentOS: 7.3: mais recente | SO e disco de dados |
| OpenLogic | CentOS 7.2 n | 7.2n | OpenLogic: CentOS: 7.2 n: mais recente | SO e disco de dados |
| OpenLogic | CentOS 7.1 | 7.1 | OpenLogic: CentOS: 7.1: mais recente | Somente o disco de dados |
| OpenLogic | CentOS 7,0 | 7.0 | OpenLogic: CentOS: 7.0: mais recente | Somente o disco de dados |
| OpenLogic | CentOS 6.8 | 6,8 | OpenLogic: CentOS: 6.8: mais recente | Somente o disco de dados |
| SUSE | OpenSUSE 42.3 | 42.3 | SUSE:openSUSE-Leap:42.3:latest | Somente o disco de dados |
| SUSE | SLES 12-SP4 | 12-SP4 | SUSE: SLES: 12-SP4: mais recente | Somente o disco de dados |
| SUSE | SLES HPC 12 – SP3 | 12-SP3 | SUSE:SLES-HPC:12-SP3:latest | Somente o disco de dados |

> [!NOTE]
> Há suporte para a nova implementação de Azure Disk Encryption para o sistema operacional RHEL e o disco de dados para imagens RHEL7 de pagamento conforme o uso.  
>
> O ADE também tem suporte para imagens Gold traga sua própria assinatura do RHEL, mas somente **depois** que a assinatura tiver sido registrada. Para obter mais informações, consulte [Red Hat Enterprise Linux imagens Gold de sua própria assinatura no Azure](../workloads/redhat/byos.md#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)

## <a name="additional-vm-requirements"></a>Requisitos adicionais de VM

Azure Disk Encryption requer que os módulos DM-cript e vfat estejam presentes no sistema. Remover ou desabilitar o vfat da imagem padrão impedirá que o sistema Leia o volume de chave e obtenha a chave necessária para desbloquear os discos em reinicializações subsequentes. As etapas de proteção do sistema que removem o módulo vfat do sistema não são compatíveis com Azure Disk Encryption. 

Antes de habilitar a criptografia, os discos de dados a serem criptografados devem estar corretamente listados em/etc/fstab. Use um nome de dispositivo de bloco persistente para essa entrada, pois nomes de dispositivo no formato "/dev/sdX" não podem ser considerados para serem associados com o mesmo disco entre as reinicializações, principalmente depois que a criptografia é aplicada. Para obter mais detalhes sobre esse comportamento, consulte: [alterações de nome de dispositivo de solucionar problemas de VM do Linux](troubleshoot-device-names-problems.md)

Verifique se as configurações de /etc/fstab estão definidas corretamente para a montagem. Para definir essas configurações, execute o comando mount - a ou reinicie a VM e disparar a remontagem dessa forma. Quando terminar, verifique a saída do comando lsblk para verificar se a unidade ainda está montada. 
- Se o arquivo /etc/fstab não montar a unidade corretamente antes de habilitar a criptografia, o Azure Disk Encryption não será capaz de montá-la corretamente.
- O processo de criptografia de disco do Azure se moverá as informações de montagem fora /etc/fstab e em seu próprio arquivo de configuração como parte do processo de criptografia. Não se assuste para ver a entrada ausente do /etc/fstab após a criptografia de unidade de dados é concluída.
- Antes de iniciar a criptografia, certifique-se de interromper todos os serviços e processos que podem estar gravando em discos de dados montados e desabilitá-los, para que eles não sejam reiniciados automaticamente após uma reinicialização. Eles poderiam manter os arquivos abertos nessas partições, impedindo o procedimento de criptografia para remontá-los, causando a falha da criptografia. 
- Após a reinicialização, ela levará tempo para o processo de criptografia de disco do Azure montar os discos criptografados recentemente. Eles não estarão disponíveis imediatamente após uma reinicialização. O processo precisa de tempo para iniciar, desbloquear e, em seguida, montar as unidades criptografadas antes de estar disponível para ser acessado por outros processos. Esse processo pode levar mais de um minuto após a reinicialização, dependendo das características do sistema.

Um exemplo de comandos que podem ser usados para montar os discos de dados e criar as entradas de/etc/fstab necessárias pode ser encontrado no [script da CLI de Azure Disk Encryption pré-requisitos](https://github.com/ejarvi/ade-cli-getting-started) (linhas 244-248) e o [script do PowerShell de Azure Disk Encryption pré-requisitos](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts). 

## <a name="networking-requirements"></a>Requisitos de rede

Para habilitar o recurso Azure Disk Encryption, as VMs do Linux devem atender aos seguintes requisitos de configuração de ponto de extremidade de rede:
  - Para obter um token para se conectar ao cofre de chaves, a VM do Linux deve ser capaz de se conectar a um \[ponto\]de extremidade Azure Active Directory, login.microsoftonline.com.
  - Para gravar as chaves de criptografia no cofre de chaves, a VM do Linux deve ser capaz de se conectar ao ponto de extremidade do cofre de chaves.
  - A VM do Linux deve ser capaz de se conectar a um ponto de extremidade de armazenamento do Azure que hospeda o repositório de extensões do Azure e uma conta de armazenamento do Azure que hospeda os arquivos VHD.
  -  Se a política de segurança limita o acesso de VMs do Azure à Internet, você pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída para os IPs. Para obter mais informações, consulte [Azure Key Vault por trás de um firewall](../../key-vault/general/access-behind-firewall.md).  

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chave de criptografia  

Azure Disk Encryption requer uma Azure Key Vault para controlar e gerenciar chaves e segredos de criptografia de disco. O cofre de chaves e as VMs devem residir na mesma região e assinatura do Azure.

Para obter detalhes, consulte [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologia
A tabela a seguir define alguns dos termos comuns usados na documentação de criptografia de disco do Azure:

| Terminologia | Definição |
| --- | --- |
| Cofre de Chave do Azure | Key Vault é um serviço de gerenciamento de chaves criptográfico baseado em módulos de segurança de hardware validados pelo FIPS (Federal Information Processing Standards). Esses padrões ajudam a proteger as chaves criptográficas e os segredos confidenciais. Para obter mais informações, consulte a documentação do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md). |
| CLI do Azure | [O CLI do Azure](/cli/azure/install-azure-cli) é otimizado para gerenciar e administrar os recursos do Azure na linha de comando.|
| DM-Crypt |[DM-cript](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) é o subsistema de criptografia de disco transparente, baseado em Linux, usado para habilitar a criptografia de disco em VMs do Linux. |
| Chave de criptografia de chave (KEK) | A chave assimétrica (RSA 2048) que você pode usar para proteger ou encapsular o segredo. É possível fornecer uma chave protegida por HSM (módulos de segurança de hardware) ou uma chave protegida por software. Para obter mais informações, consulte a documentação do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md). |
| Cmdlets do PowerShell | Para obter mais informações, confira [cmdlets do Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Próximas etapas

- [Início rápido-criar e criptografar uma VM Linux com CLI do Azure](disk-encryption-cli-quickstart.md)
- [Início rápido-criar e criptografar uma VM do Linux com o Azure PowerShell](disk-encryption-powershell-quickstart.md)
- [Cenários de Azure Disk Encryption em VMs Linux](disk-encryption-linux.md)
- [Script da CLI de pré-requisitos Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started)
- [Script do PowerShell de Azure Disk Encryption pré-requisitos](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md)


