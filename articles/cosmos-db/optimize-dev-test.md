---
title: Otimizar para desenvolvimento e teste no Azure Cosmos DB
description: Este artigo explica como o Azure Cosmos DB oferece várias opções para desenvolvimento e teste do serviço gratuitamente.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 3fe5ea98f8db633eed7ce4e2c0ac0cafa56408ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82194509"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Otimizar o custo de desenvolvimento e teste no Azure Cosmos DB

Este artigo descreve as diferentes opções para usar Azure Cosmos DB para desenvolvimento e teste gratuitamente, bem como técnicas para otimizar o custo em contas de desenvolvimento ou teste.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Emulador do Azure Cosmos DB (versão local para download)

O [Emulador do Azure Cosmos DB](local-emulator.md) é uma versão local para download que imita o serviço de nuvem do Azure Cosmos DB. Você pode escrever e testar o código que usa as APIs do Azure Cosmos DB, mesmo se não tiver conexão à rede e sem incorrer em custos. O emulador do Azure Cosmos DB fornece um ambiente local para fins de desenvolvimento com alta fidelidade para o serviço de nuvem. Você pode desenvolver e testar seu aplicativo no local, sem criar uma assinatura do Azure. Quando estiver pronto para implantar seu aplicativo na nuvem, atualize a cadeia de conexão para se conectar ao ponto de extremidade do Azure Cosmos DB na nuvem, nenhuma outra modificação será necessária. Você também pode [configurar um pipeline de CI/CD com a tarefa de build do emulador do Azure Cosmos DB](tutorial-setup-ci-cd.md) no Azure DevOps para executar testes. Comece a usar visitando o artigo [Emulador do Azure Cosmos DB](local-emulator.md).

## <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB camada gratuita

Azure Cosmos DB camada gratuita facilita a introdução, o desenvolvimento e teste de seus aplicativos ou até mesmo a execução de cargas de trabalho de produção pequenas gratuitamente. Quando a camada gratuita estiver habilitada em uma conta, você obterá os primeiros 400 RU/s e 5 GB de armazenamento na conta livre. Você também pode criar um banco de dados de produtividade compartilhado com 25 contêineres que compartilham 400 RU/s no nível do banco de dados, todos cobertos pela camada gratuita (limite 5 bancos de dados de taxa de transferência compartilhada em uma conta de camada gratuita). A camada gratuita dura indefinidamente pelo tempo de vida da conta e vem com todos os [benefícios e recursos](introduction.md#key-benefits) de uma conta Azure Cosmos DB regular, incluindo armazenamento ilimitado e taxa de transferência (ru/s), SLAs, alta disponibilidade, distribuição global completa em todas as regiões do Azure e muito mais. Você pode ter até uma conta de camada gratuita por assinatura do Azure e deve aceitar ao criar a conta. Para começar, [crie uma nova conta no portal do Azure com a camada gratuita habilitada](create-cosmosdb-resources-portal.md) ou use um [modelo ARM](manage-sql-with-resource-manager.md#free-tier). Consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter mais detalhes.

## <a name="try-azure-cosmos-db-for-free"></a>Experimente o Azure Cosmos DB gratuitamente

[Experimente Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) é uma experiência gratuita que permite que você experimente Azure Cosmos DB na nuvem sem se inscrever em uma conta do Azure ou usando seu cartão de crédito. As contas do try Azure Cosmos DB estão disponíveis por um período limitado, atualmente 30 dias. Você pode renová-las a qualquer momento. Experimente Azure Cosmos DB contas facilita a avaliação Azure Cosmos DB, a criação e o teste de um aplicativo ou o uso de guias de início rápido ou tutoriais. Você também pode criar uma demonstração, executar testes de unidade ou até mesmo criar uma conta de várias regiões e executar um aplicativo nele sem incorrer em nenhum custo. Em uma conta do try Azure Cosmos DB, você pode ter um banco de dados de produtividade compartilhado com um máximo de 25 contêineres e 20.000 RU/s de taxa de transferência ou um contêiner com até 5000 RU/s. Para começar, consulte a página [Experimente o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/).

## <a name="azure-free-account"></a>Conta gratuita do Azure

O Azure Cosmos DB está incluso na [conta gratuita do Azure](https://azure.microsoft.com/free), que oferece recursos e créditos do Azure gratuitamente por um determinado período de tempo. Especificamente para o Azure Cosmos DB, a conta gratuita oferece armazenamento de 5 GB e 400 RUs de taxa de transferência provisionada para o ano todo. Essa experiência permite que qualquer desenvolvedor teste facilmente os recursos do Azure Cosmos DB ou integre-os a outros serviços do Azure sem custo. Com a conta gratuita do Azure, você recebe um crédito de US$ 200,00 para gastar nos primeiros 30 dias. Você não será cobrado, mesmo se começar a usar os serviços, até que você opte por atualizar. Para começar, visite a página [Conta gratuita do Azure](https://azure.microsoft.com/free).

## <a name="use-shared-throughput-databases"></a>Usar bancos de dados de produtividade compartilhada

Em um [banco de dados de produtividade compartilhado](set-throughput.md#set-throughput-on-a-database), todos os contêineres dentro do banco de dados compartilham a taxa de transferência provisionada (ru/s) do banco de dados. Por exemplo, se você provisionar um banco de dados com 400 RU/s e tiver quatro contêineres, todos os quatro contêineres compartilharão as 400 RU/s. Em um ambiente de desenvolvimento ou teste, onde cada contêiner pode ser acessado com menos frequência e, portanto, exigir menos do que o mínimo de 400 RU/s, colocar contêineres em um banco de dados de produtividade compartilhado pode ajudar a otimizar o custo.

Por exemplo, suponha que sua conta de desenvolvimento ou teste tenha quatro contêineres. Se você criar quatro contêineres com taxa de transferência dedicada (mínimo de 400 RU/s), o total de RU/s será de 1600 RU/s. Por outro lado, se você criar um banco de dados de produtividade compartilhado (mínimo de 400 RU/s) e colocar seus contêineres, o total de RU/s será apenas 400 RU/s. Em geral, os bancos de dados de produtividade compartilhada são ótimos para cenários em que você não precisa de taxa de transferência garantida em nenhum contêiner individual.  Saiba mais sobre [bancos de dados de produtividade compartilhada.](set-throughput.md#set-throughput-on-a-database)

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o emulador ou as contas gratuitas do Azure Cosmos DB, leia os seguintes artigos:

* Saiba mais sobre [Otimizando para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [Entender sua cobrança do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre [Otimizando o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre [Otimizando o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [Otimizando o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [Otimizando o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre [Otimizando o custo de contas do Azure Cosmos em várias regiões](optimize-cost-regions.md)
