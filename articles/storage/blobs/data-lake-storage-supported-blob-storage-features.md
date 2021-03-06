---
title: Recursos de armazenamento de BLOBs disponíveis no Azure Data Lake Storage Gen2 | Microsoft Docs
description: Saiba quais recursos de armazenamento de BLOBs você pode usar com Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: cfc7e12efb0e5e4613bb4cc5f2f9a35383f37546
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857246"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Recursos de armazenamento de BLOBs disponíveis no Azure Data Lake Storage Gen2

Os recursos de armazenamento de BLOBs, como [log de diagnóstico](../common/storage-analytics-logging.md), [camadas de acesso](storage-blob-storage-tiers.md)e [políticas de gerenciamento do ciclo de vida do armazenamento de BLOBs](storage-lifecycle-management-concepts.md) , agora funcionam com contas que têm um namespace hierárquico. Portanto, você pode habilitar namespaces hierárquicos em suas contas de armazenamento de BLOBs sem perder o acesso a esses recursos.

Esta tabela lista os recursos de armazenamento de BLOBs que você pode usar com Azure Data Lake Storage Gen2. Os itens que aparecem nessas tabelas serão alterados ao longo do tempo, pois o suporte continuará a ser expandido.

## <a name="supported-blob-storage-features"></a>Recursos do Armazenamento de Blobs compatíveis

> [!NOTE]
> O nível de suporte refere-se apenas a como o recurso tem suporte com Data Lake Storage Gen2.

|Recurso de armazenamento de BLOBs |Nível de suporte |Artigos relacionados |
|---------------|-------------------|---|
|Camada de acesso quente|Disponível para o público geral|[Armazenamento de Blobs do Azure: camadas de acesso frequentes, esporádicas e de arquivo](storage-blob-storage-tiers.md)|
|Camada de acesso frio|Disponível para o público geral|[Armazenamento de Blobs do Azure: camadas de acesso frequentes, esporádicas e de arquivo](storage-blob-storage-tiers.md)|
|Eventos|Disponível para o público geral|[Reagir aos eventos de armazenamento de Blobs](storage-blob-event-overview.md)|
|Métricas (clássicas)|Disponível para o público geral|[Métricas da análise de armazenamento do Azure (clássico)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Métricas no Azure Monitor|Disponível para o público geral|[Métricas do Armazenamento do Microsoft Azure no Azure Monitor](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Comandos do PowerShell de armazenamento de BLOBs|Disponível para o público geral|[Início rápido: carregar, baixar e listar BLOBs com o PowerShell](storage-quickstart-blobs-powershell.md)|
|Comandos de CLI do Azure de armazenamento de BLOBs|Disponível para o público geral|[Início rápido: criar, baixar e listar BLOBs com CLI do Azure](storage-quickstart-blobs-cli.md)|
|APIs de armazenamento de Blob|Disponível para o público geral|[Início rápido: biblioteca de cliente do armazenamento de BLOBs do Azure V12 para .NET](storage-quickstart-blobs-dotnet.md)<br>[Início rápido: gerenciar BLOBs com o SDK do Java V12](storage-quickstart-blobs-java.md)<br>[Início rápido: gerenciar BLOBs com o SDK do Python V12](storage-quickstart-blobs-python.md)<br>[Início rápido: gerenciar BLOBs com o SDK do JavaScript V12 no node. js](storage-quickstart-blobs-nodejs.md)|
|Camada de acesso de arquivamento|Visualizar|[Armazenamento de Blobs do Azure: camadas de acesso frequentes, esporádicas e de arquivo](storage-blob-storage-tiers.md)|
|Políticas de gerenciamento do ciclo de vida|Visualizar|[Gerenciar o ciclo de vida de armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md)|
|Logs de diagnóstico|Disponível para o público geral|[Log da análise do Armazenamento do Azure](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Feed de alteração|Ainda não tem suporte|[Suporte ao feed de alterações no armazenamento de BLOBs do Azure](storage-blob-change-feed.md)|
|Failover de conta|Ainda não tem suporte|[Recuperação de desastres e failover de conta de armazenamento](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|ACL de contêiner de BLOB|Ainda não tem suporte|[Definir ACL do contêiner](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|Domínios personalizados|Ainda não tem suporte|[Mapear um domínio personalizado para um ponto de extremidade do armazenamento de BLOBs do Azure](storage-custom-domain-name.md)|
|Armazenamento imutável|Ainda não tem suporte|[Armazene dados de blob críticos para os negócios com armazenamento imutável](storage-blob-immutable-storage.md)|
|Instantâneos|Ainda não tem suporte|[Criar e gerenciar um instantâneo de blob no .NET](storage-blob-snapshots.md)|
|Exclusão reversível|Ainda não tem suporte|[Exclusão reversível para blobs do Armazenamento do Azure ](storage-blob-soft-delete.md)|
|Sites estáticos|Ainda não tem suporte|[Hospedagem de site estático no Armazenamento do Microsoft Azure](storage-blob-static-website.md)|
|Fazendo logon Azure Monitor|Ainda não tem suporte|Ainda não está disponível|
|Blobs de blocos Premium|Ainda não tem suporte|[Criar uma conta do BlockBlobStorage](storage-blob-create-account-block-blob.md)|

## <a name="see-also"></a>Confira também

- [Problemas conhecidos com o Azure Data Lake Storage Gen2 | Microsoft Docs](data-lake-storage-known-issues.md)
- [Serviços do Azure que dão suporte ao Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plataformas de software livre que dão suporte a Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Acesso de vários protocolos no Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)