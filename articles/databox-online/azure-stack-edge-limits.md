---
title: Limites de borda Azure Stack | Microsoft Docs
description: Descreve os limites do sistema e os tamanhos recomendados para o Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 4f7800efb5d4382e8d73c819d950fdfafd10f296
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569830"
---
# <a name="azure-stack-edge-limits"></a>Limites do Azure Stack Edge

Considere esses limites ao implantar e operar sua solução de borda de Microsoft Azure Stack. 

## <a name="azure-stack-edge-service-limits"></a>Limites de serviço do Azure Stack Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-device-limits"></a>Azure Stack limites de dispositivo

A tabela a seguir descreve os limites para o dispositivo Azure Stack Edge. 

| Descrição | Valor |
|---|---|
|Não. de arquivos por dispositivo |100 milhões |
|Não. de compartilhamentos por dispositivo |24 |
|Não. de compartilhamentos por contêiner |1 |
|Tamanho máximo do arquivo gravado em um compartilhamento| 5 TB |

## <a name="azure-storage-limits"></a>Limites de armazenamento do Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Limitações de upload de dados

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limites de tamanho da conta de armazenamento e tamanho do objeto do Azure

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limites de tamanho do objeto do Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Próximas etapas

- [Preparar para implantar Azure Stack borda](azure-stack-edge-deploy-prep.md)
