---
title: Estender o suporte para SQL Server 2008 & 2008 R2
description: Estenda o suporte para SQL Server 2008 e SQL Server 2008 R2 migrando sua instância de SQL Server para o Azure ou comprando o suporte estendido para manter as instâncias locais.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: faaff9e9ad5982efac6409f2284158a3d0711331
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80548400"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Estender o suporte para SQL Server 2008 e SQL Server 2008 R2 com o Azure

SQL Server 2008 e SQL Server 2008 R2 atingiram o [ciclo de vida do fim de suporte (EOS)](https://www.microsoft.com/sql-server/sql-server-2008). Como muitos clientes ainda estão usando ambas as versões, estamos fornecendo várias opções para continuar a obter suporte. Você pode migrar suas instâncias de SQL Server locais para VMs (máquinas virtuais) do Azure, migrar para o banco de dados SQL do Azure ou manter-se no local e comprar atualizações de segurança estendidas.

Ao contrário de uma instância gerenciada, migrar para uma VM do Azure não exige a recertificação de seus aplicativos. E, ao contrário de ficar no local, você receberá patches de segurança estendidos gratuitos migrando para uma VM do Azure.

O restante deste artigo fornece considerações para migrar sua instância de SQL Server para uma VM do Azure.

Para obter mais informações sobre o fim das opções de suporte, consulte [fim do suporte](/sql/sql-server/end-of-support/sql-server-end-of-life-overview).

## <a name="provisioning"></a>Provisionamento

Há uma imagem paga conforme o uso **SQL Server 2008 R2 no Windows Server 2008 R2** , disponível no Azure Marketplace.

Os clientes que estiverem no SQL Server 2008 precisarão instalar automaticamente ou atualizar para SQL Server 2008 R2. Da mesma forma, os clientes do Windows Server 2008 precisarão implantar sua VM de um VHD personalizado ou atualizar para o Windows Server 2008 R2.

As imagens implantadas por meio do Azure Marketplace vêm com a extensão IaaS do SQL pré-instalada. A extensão de IaaS do SQL é um requisito para licenciamento flexível e aplicação de patch automatizada. Os clientes que implantam VMs autoinstaladas precisarão instalar manualmente a extensão SQL IaaS. A extensão de IaaS do SQL não tem suporte no Windows Server 2008.

> [!NOTE]
> Embora o SQL Server **criar** e **gerenciar** as folhas funcionem com a imagem do SQL Server 2008 R2 no portal do Azure, _não há suporte para_os seguintes recursos: backups automáticos, integração de Azure Key Vault, serviços de R e configuração de armazenamento.

## <a name="licensing"></a>Licenciamento
As implantações pré-pagas SQL Server 2008 R2 podem converter em [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Para converter uma licença baseada no Software Assurance (SA) para pré-paga, os clientes devem se registrar no [provedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md)da VM do SQL. Após esse registro, o tipo de licença do SQL será intercambiável entre Benefício Híbrido do Azure e pré-pago.

As instâncias autoinstaladas do SQL Server 2008 ou SQL Server 2008 R2 em uma VM do Azure podem se registrar no provedor de recursos da VM do SQL e converter seu tipo de licença para pré-pago.

## <a name="migration"></a>Migração
Você pode migrar instâncias de SQL Server de EOS para uma VM do Azure com métodos de backup/restauração manuais. Esse é o método de migração mais comum do local para uma VM do Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Para migrações em massa, recomendamos o serviço [Azure site Recovery](/azure/site-recovery/site-recovery-overview) . Com o Azure Site Recovery, os clientes podem replicar toda a VM, incluindo SQL Server do local para a VM do Azure.

SQL Server requer instantâneos de Azure Site Recovery consistentes com o aplicativo para garantir a recuperação. Azure Site Recovery dá suporte a instantâneos consistentes com o aplicativo com um intervalo mínimo de 1 hora. O RPO (objetivo de ponto de recuperação) mínimo possível para SQL Server com migrações de Azure Site Recovery é de 1 hora. O RTO (objetivo de tempo de recuperação) é 2 horas mais SQL Server tempo de recuperação.

### <a name="database-migration-service"></a>Serviço de Migração de Banco de Dados

O [serviço de migração de banco de dados](/azure/dms/dms-overview) é uma opção para clientes se eles estiverem migrando do local para uma VM do Azure atualizando SQL Server para a versão 2012 ou posterior.

## <a name="disaster-recovery"></a>Recuperação de desastre

As soluções de recuperação de desastre para SQL Server de EOS em uma VM do Azure são as seguintes:

- **Backups de SQL Server**: Use o backup do Azure para ajudar a proteger seu EOS SQL Server 2008 e 2008 R2 contra ransomware, exclusão acidental e corrupção com RPO de 15 min e recuperação pontual. Para obter mais detalhes, consulte [Este artigo](https://docs.microsoft.com/azure/backup/sql-support-matrix#scenario-support).
- **Envio de logs**: você pode criar uma réplica de envio de logs em outra zona ou em uma região do Azure com restaurações contínuas para reduzir o RTO. Você precisa configurar manualmente o envio de logs.
- **Azure site Recovery**: você pode replicar sua VM entre zonas e regiões por meio da replicação Azure site Recovery. SQL Server requer instantâneos consistentes com o aplicativo para garantir a recuperação em caso de desastre. O Azure Site Recovery oferece um RPO mínimo de 1 hora e um RTO de 2 horas (mais SQL Server tempo de recuperação) para a recuperação de desastres de EOS SQL Server.

## <a name="security-patching"></a>Aplicação de patch de segurança
As atualizações de segurança estendidas para VMs SQL Server são entregues por meio dos canais de Microsoft Update após a SQL Server VM ter sido registrada com o [provedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md)de VM do SQL. Os patches podem ser baixados manual ou automaticamente.

*Automated patching* está habilitada por padrão. A aplicação de patch automatizada permite que o Azure aplique patches automaticamente no SQL Server e no sistema operacional. Você pode especificar um dia da semana, hora e duração para uma janela de manutenção se a extensão SQL Server IaaS estiver instalada. O Azure realiza a aplicação de patch na janela de manutenção. O agendamento do período de manutenção usa a localidade da VM para a hora.  Para obter mais informações, consulte [aplicação de patch automatizada para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Próximas etapas

Migre sua VM SQL Server para o Azure:

* [Migrar um banco de dados do SQL Server para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md)

Introdução ao SQL Server em máquinas virtuais do Azure:

* [Criar uma VM do SQL Server no portal do Azure](quickstart-sql-vm-create-portal.md)

Obtenha respostas para perguntas frequentes sobre VMs SQL Server:

* [Perguntas frequentes para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-faq.md)

Saiba mais sobre o fim das opções de suporte e as atualizações de segurança estendidas:

* [Fim do suporte](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) & de[atualizações de segurança estendidas](/sql/sql-server/end-of-support/sql-server-extended-security-updates)