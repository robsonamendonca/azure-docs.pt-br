---
title: Dados de blob reidratar da camada de arquivo
description: Reidratar seus BLOBs do armazenamento de arquivo para que você possa acessar os dados.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 82ea4ad23e3207f5641ade196f69595cd1e7b323
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684084"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Dados de blob reidratar da camada de arquivo

Embora um blob esteja na camada de acesso de arquivamento, ele é considerado offline e não pode ser lido ou modificado. Os metadados de blob permanecem online e disponíveis, permitindo que você liste o blob e suas propriedades. A leitura e a modificação de dados de blob só estão disponíveis com camadas online, como Hot ou fria. Há duas opções para recuperar e acessar os dados armazenados na camada de acesso de arquivamento.

1. [Reidratar um blob Arquivado em uma camada online](#rehydrate-an-archived-blob-to-an-online-tier) -reidratar um blob de arquivo morto para quente ou frio alterando sua camada usando a operação [definir camada de blob](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) .
2. [Copiar um blob Arquivado em uma camada online](#copy-an-archived-blob-to-an-online-tier) – crie uma nova cópia de um blob de arquivo usando a operação de [cópia de blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) . Especifique um nome de blob diferente e uma camada de destino de alta ou fria.

 Para obter mais informações sobre camadas, consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Reidratar um blob Arquivado em uma camada online

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Copiar um blob arquivado para uma camada online

Se você não quiser reidratar o blob de arquivo morto, poderá optar por fazer uma operação de [cópia de blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) . Seu blob original permanecerá sem modificações no arquivo morto enquanto um novo blob for criado na camada online ou fria para trabalhar. Na operação copiar BLOB, você também pode definir a propriedade *x-MS-reidratar-Priority* opcional como Standard ou High para especificar a prioridade na qual você deseja que a cópia de blob seja criada.

A cópia de um blob do arquivo pode levar horas para ser concluída, dependendo da prioridade reidratar selecionada. Nos bastidores, a operação **copiar blob** lê o blob de origem do arquivo para criar um novo BLOB online na camada de destino selecionada. O novo blob pode estar visível quando você lista os BLOBs, mas os dados não estão disponíveis até que a leitura do blob de arquivo de origem seja concluída e os dados sejam gravados no novo BLOB de destino online. O novo BLOB é uma cópia independente e qualquer modificação ou exclusão feita a ele não afeta o blob de arquivo de origem.

Os blobs de arquivo só podem ser copiados para as camadas de destino online dentro da mesma conta de armazenamento. Não há suporte para a cópia de um blob de arquivo para outro blob de arquivo. A tabela a seguir indica os recursos do CopyBlob.

|                                           | **Fonte de camada quente**   | **Origem da camada fria** | **Origem da camada de arquivo**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **Destino de camada quente**                  | Com suporte             | Com suporte            | Com suporte na mesma conta; reidratar pendentes               |
| **Destino de camada fria**                 | Com suporte             | Com suporte            | Com suporte na mesma conta; reidratar pendentes               |
| **Destino da camada de arquivo**              | Com suporte             | Com suporte            | Sem suporte         |

## <a name="pricing-and-billing"></a>Preços e cobrança

Os BLOBs reidratar do arquivamento em camadas quentes ou frias são cobrados como operações de leitura e recuperação de dados. O uso de alta prioridade tem custos de operação e recuperação de dados mais altos em comparação com a prioridade padrão. Reidratação de alta prioridade aparece como um item de linha separado em sua fatura. Se uma solicitação de alta prioridade para retornar um blob de arquivo de alguns gigabytes levar mais de 5 horas, você não será cobrado pela taxa de recuperação de alta prioridade. No entanto, as taxas de recuperação padrão ainda se aplicam à medida que o reidratação foi priorizado em relação a outras solicitações.

A cópia de BLOBs do arquivo em camadas quentes ou frias é cobrada como operações de leitura e recuperação de dados. Uma operação de gravação é cobrada pela criação da nova cópia de BLOB. As tarifas de exclusão antecipada não se aplicam quando você copia para um blob online porque o blob de origem permanece inalterado na camada de arquivo morto. Os encargos de recuperação de prioridade alta se aplicam se selecionado.

Os BLOBs na camada de arquivo devem ser armazenados por um mínimo de 180 dias. Excluir ou reidratarr BLOBs arquivados antes de 180 dias incorrerá em tarifas de exclusão antecipada.

> [!NOTE]
> Para obter mais informações sobre preços para BLOBs de blocos e reidratação de dados, consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Para obter mais informações sobre encargos de transferência de dados de saída, consulte [detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart-scenarios"></a>Cenários de início rápido

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Reidratar um blob de arquivo para uma camada online
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Entre no [portal do Azure](https://portal.azure.com).

1. Na portal do Azure, procure e selecione **todos os recursos**.

1. Selecione sua conta de armazenamento.

1. Selecione seu contêiner e, em seguida, selecione seu BLOB.

1. Nas **Propriedades do blob**, selecione **alterar camada**.

1. Selecione a camada de acesso **quente** ou **fria** . 

1. Selecione uma prioridade de reidratar de **padrão** ou **alta**.

1. Selecione **salvar** na parte inferior.

![Alterar o status reidratar](media/storage-tiers/blob-access-tier.png)
![da verificação da camada da conta de armazenamento](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
O script do PowerShell a seguir pode ser usado para alterar a camada de blob de um blob de arquivo morto. A `$rgName` variável deve ser inicializada com o nome do grupo de recursos. A `$accountName` variável deve ser inicializada com o nome da conta de armazenamento. A `$containerName` variável deve ser inicializada com o nome do contêiner. A `$blobName` variável deve ser inicializada com o nome do blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", “Standard”)
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Copiar um blob de arquivo para um novo BLOB com uma camada online
O script do PowerShell a seguir pode ser usado para copiar um blob de arquivo para um novo BLOB dentro da mesma conta de armazenamento. A `$rgName` variável deve ser inicializada com o nome do grupo de recursos. A `$accountName` variável deve ser inicializada com o nome da conta de armazenamento. As `$srcContainerName` variáveis `$destContainerName` e devem ser inicializadas com seus nomes de contêiner. As `$srcBlobName` variáveis `$destBlobName` e devem ser inicializadas com seus nomes de BLOB. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre as camadas de armazenamento de BLOBs](storage-blob-storage-tiers.md)
* [Verificar o preço de frequente, esporádica e de arquivo em contas de Armazenamento de Blobs e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)
* [Gerenciar o ciclo de vida de armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md)
* [Verificar os preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
