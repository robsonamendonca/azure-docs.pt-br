---
title: Contadores de desempenho para acompanhar o Gerenciador de mapa de fragmentos
description: Classe do ShardMapManager e contadores de desempenho de roteamento dependente de dados
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019, seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: de481dad9dd39b301a21142c67b1baf2209f76e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "73823893"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Criar contadores de desempenho para acompanhar o desempenho do Gerenciador de mapa de fragmentos

Os contadores de desempenho são usados para acompanhar o desempenho de operações de [Roteamento dependentes de dados](sql-database-elastic-scale-data-dependent-routing.md) . Esses contadores estão acessíveis no Monitor de Desempenho, na categoria "Banco de Dados Elástico: Gerenciamento de Fragmento".

Você pode capturar o desempenho de um [Gerenciador de mapa do fragmento](sql-database-elastic-scale-shard-map-management.md), especialmente ao usar [roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md). Contadores são criados com métodos da classe Microsoft.Azure.SqlDatabase.ElasticScale.Client.  


**Para obter a versão mais recente:** acesse [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Consulte também [Atualizar um aplicativo para usar a biblioteca de cliente de banco de dados elástico mais recente](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Pré-requisitos

* Para criar a categoria de desempenho e contadores, o usuário deve ser uma parte do grupo **Administradores** local no computador que hospeda o aplicativo.  
* Para criar uma instância do contador de desempenho e atualizar os contadores, o usuário deve ser um membro do grupo **Administradores** ou **Usuários do Monitor de Desempenho**.

## <a name="create-performance-category-and-counters"></a>Criar categoria e contadores de desempenho

Para criar os contadores, chame o método CreatePerformanceCategoryAndCounters da [classe ShardMapManagementFactory](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Somente um administrador pode executar o método:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Você também pode usar [este](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) script do PowerShell para executar o método.
O método cria os seguintes contadores de desempenho:  

* **Mapeamentos em cache**: número de mapeamentos armazenados em cache para o mapa de fragmentos.
* **Operações de DDR/s**: a taxa de operações de roteamento dependentes de dados para o mapa de fragmentos. Esse contador é atualizado quando uma chamada para [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) resulta em uma conexão bem-sucedida com o fragmento de destino.
* **Mapeamento de acertos de pesquisa do cache/s**: taxa de operações bem-sucedidas de pesquisa de cache para mapeamentos no mapa de fragmentos.
* **Mapeamento de erros de pesquisa de cache/s**: taxa de operações bem-sucedidas de pesquisa de cache para mapeamentos no mapa de fragmentos.
* **Mapeamentos adicionados ou atualizados no cache/s**: taxa à qual os mapeamentos estão sendo adicionados ou atualizados no cache para o mapa de fragmentos.
* **Mapeamentos removidos do cache/s**: taxa à qual os mapeamentos estão sendo removidos do cache para o mapa de fragmentos.

Contadores de desempenho são criados para cada mapa de fragmentos em cache por processo.  

## <a name="notes"></a>Observações

Os seguintes eventos disparam a criação dos contadores de desempenho:  

* Inicialização do [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) com [carregamento adiantado](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy), se o ShardMapManager contiver quaisquer mapas de fragmentos. Inclui os métodos [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) e [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager).
* Pesquisa bem-sucedida de um mapa de fragmentos (usando [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) ou [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Criação bem-sucedida do mapa de fragmentos usando CreateShardMap().

Os contadores de desempenho serão atualizados por todas as operações de cache executadas no mapa do fragmentos e nos mapeamentos. A remoção bem-sucedida do mapa de fragmentos usando DeleteShardMap() resulta na exclusão da instância de contadores de desempenho.  

## <a name="best-practices"></a>Práticas recomendadas

* A criação de categoria e dos contadores de desempenho deve ser feita apenas uma vez antes da criação do objeto ShardMapManager. Cada execução do comando CreatePerformanceCategoryAndCounters() limpa os contadores anteriores (perdendo dados relatados por todas as instâncias) e cria novos.  
* Instâncias de contador de desempenho são criadas por processo. Qualquer falha do aplicativo ou remoção de um mapa de fragmentos do cache resultará na exclusão das instâncias de contadores de desempenho.  

### <a name="see-also"></a>Confira também

[Visão geral dos recursos do Banco de Dados Elástico](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
