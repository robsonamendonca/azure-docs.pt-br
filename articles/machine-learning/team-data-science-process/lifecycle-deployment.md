---
title: Estágio de implantação do ciclo de vida do Processo de Ciência de Dados de Equipe
description: As metas, as tarefas e as entregas para o estágio de implantação dos seus projetos de ciência de dados
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1138c95274c769186a9a29aa4d35517e378baeae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720479"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Estágio de implantação do ciclo de vida do Processo de Ciência de Dados de Equipe

Este artigo descreve as metas, as tarefas e as entregas associadas à implantação do TDSP (Processo de Ciência de Dados da Equipe). Esse processo fornece um ciclo de vida recomendado que você pode usar para estruturar seus projetos de ciência de dados. O ciclo de vida descreve os estágios principais que os projetos normalmente executam, com frequência de modo iterativo:

   1. **Noções básicas sobre negócios**
   2. **Aquisição e compreensão de dados**
   3. **Modelagem**
   4. **Implantação**
   5. **Aceitação do cliente**

Aqui está uma representação visual do ciclo de vida do TDSP: 

![Ciclo de vida do TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Goal
Implante modelos com um pipeline de dados em um ambiente de produção ou semelhante à produção para aceitação do usuário final. 

## <a name="how-to-do-it"></a>Como fazer isso
A principal tarefa abordada neste estágio:

**Operacionalizar o modelo**: implante o modelo e o pipeline em um ambiente de produção ou semelhante à produção para consumo do aplicativo.

### <a name="operationalize-a-model"></a>Operacionalizar um modelo
Depois que você tiver um conjunto de modelos com um bom desempenho, eles poderão ser operacionalizados para o consumo de outros aplicativos. Dependendo dos requisitos de negócios, as previsões são feitas em tempo real ou em lotes. Para implantar modelos, exponha-os com uma interface de API livre. A interface permite que o modelo seja consumido facilmente de vários aplicativos, tais como:

   * Sites online
   * Planilhas 
   * Painéis
   * Aplicativos de linha de negócios 
   * Aplicativos de back-end 

Para obter exemplos de operacionalização de modelos com um serviço Web do Azure Machine Learning, consulte [Implantar um serviço Web do Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md). É uma prática recomendada integrar a telemetria e o monitoramento no modelo de produção e no pipeline de dados implantados. Essa prática ajuda com o relatório de status do sistema e a solução de problemas subsequentes.  

## <a name="artifacts"></a>Artefatos

* Um painel de status que exibe informações sobre a integridade do sistema e as principais métricas
* Um relatório de modelagem final com detalhes da implantação
* Um documento de arquitetura da solução final


## <a name="next-steps"></a>Próximas etapas

Aqui estão os links para cada etapa do ciclo de vida do TDSP:

   1. [Noções básicas sobre negócios](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de dados](lifecycle-data.md)
   3. [Modelagem](lifecycle-modeling.md)
   4. [Implantação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos orientações completas que demonstram todas as etapas no processo para cenários específicos. O artigo [Passo a passos de exemplo](walkthroughs.md) fornece uma lista dos cenários com descrições de links e miniaturas. Os passo a passo ilustram como combinar ferramentas e serviços de nuvem e locais em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente. 

Para obter exemplos de como executar etapas nos TDSPs que usam o Azure Microsoft Azure Machine Learning Studio, consulte [Use the TDSP with Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) (Usar o TDSP com o Azure Machine Learning).
