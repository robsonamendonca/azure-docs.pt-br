---
title: incluir arquivo
description: incluir arquivo
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 179f525b7b6a7e51889b14b66df6c537ca56bd75
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80272817"
---
O armazenamento é restrito por espaço em disco ou por um limite rígido no *número máximo* de índices, documentos ou outros recursos de alto nível, o que ocorrer primeiro. A tabela a seguir documenta os limites de armazenamento. Para obter os limites máximos de índices, documentos e outros objetos, consulte [limites por recurso](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Recurso | Grátis | Básico<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| SLA (contrato de nível de serviço)<sup>3</sup>  |Não |Sim |Sim |Sim |Sim |Sim |Sim |Sim |
| Armazenamento por partição |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partições por serviço |N/D |1 |12 |12 |12 |3 |12 |12 |
| Tamanho da partição |N/D |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Réplicas |N/D |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Básico tem uma partição fixa. Nessa camada, as unidades de pesquisa adicionais são usadas para alocar mais réplicas para cargas de trabalho de consulta aumentadas.

<sup>2</sup> S3 HD tem um limite rígido de três partições, que é menor do que o limite de partição para S3. O limite de partição inferior é aplicado porque a contagem de índice de S3 HD é consideravelmente maior. Uma vez que há limites de serviço para os recursos de computação (processamento e armazenamento) e de conteúdo (índices e documentos), o limite de conteúdo é atingido primeiro.

<sup>3</sup> os contratos de nível de serviço são oferecidos para serviços faturáveis em recursos dedicados. Serviços gratuitos e recursos de visualização não têm SLA. Para serviços faturáveis, os SLAs entram em vigor quando você fornece redundância suficiente para seu serviço. Duas ou mais réplicas são necessárias para os SLAs de consulta (leitura). Três ou mais réplicas são necessárias para os SLAs de consulta e indexação (leitura/gravação). O número de partições não é uma consideração de SLA. 