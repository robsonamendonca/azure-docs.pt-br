---
title: Suporte a software livre no Azure HDInsight
description: O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 65f21450f194175af37c6c198468978392cee462
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195155"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Suporte a software livre no Azure HDInsight

O serviço de Microsoft Azure HDInsight usa um ambiente de tecnologias de software livre formado por Apache Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre. Para saber mais, confira a seção **Escopo do Suporte** nas [Perguntas frequentes sobre o Suporte do Azure](https://azure.microsoft.com/support/faq/). O serviço HDInsight fornece um nível adicional de suporte a componentes internos.

## <a name="components"></a>Componentes

Dois tipos de componentes de software livre estão disponíveis no serviço do HDInsight:

### <a name="built-in-components"></a>Componentes internos

Esses componentes estão pré-instalados em clusters do HDInsight e fornecem os recursos básicos do cluster. Os seguintes componentes pertencem a essa categoria:

* [Apache HADOOP yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Gerenciador de recursos.
* A linguagem de consulta do Hive, [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
* [Apache Mahout](https://mahout.apache.org/).

Uma lista completa de componentes de cluster está disponível em [Quais são os componentes e versões do Apache Hadoop disponíveis com o HDInsight?](hdinsight-component-versioning.md)

### <a name="custom-components"></a>Componentes personalizados

Como usuário do cluster, você pode instalar ou usar em sua carga de trabalho qualquer componente disponível na comunidade ou criado por você.

> [!WARNING]  
> Componentes fornecidos com o cluster HDInsight contam com suporte total. O Suporte da Microsoft ajuda a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercial razoável para ajudar você a solucionar o problema. O Suporte da Microsoft pode conseguir resolver o problema. Ou eles podem solicitar que você busque nos canais disponíveis as tecnologias de código-fonte aberto, onde é possível encontrar conhecimento aprofundado sobre essa tecnologia. Muitos sites de comunidades podem ser usados. Por exemplo, o [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) e o [Stack Overflow](https://stackoverflow.com).
>
> Os projetos do Apache têm sites de projeto no [site do Apache](https://apache.org). Um exemplo é o [Hadoop](https://hadoop.apache.org/).

## <a name="component-usage"></a>Uso do componente

O serviço HDInsight possibilita o uso de componentes personalizados de várias formas. O mesmo nível de suporte se aplica, independentemente de como um componente é usado ou instalado no cluster. A tabela a seguir descreve as maneiras mais comuns de usar os componentes personalizados em clusters HDInsight:

|Uso |Descrição |
|---|---|
|Envio do trabalho|Trabalhos do Hadoop ou de outros tipos que executam ou usam componentes personalizados podem ser enviados para o cluster.|
|Personalização do cluster|Durante a criação de clusters, você pode especificar configurações adicionais e componentes personalizados para instalação nos nós de cluster.|
|Exemplos|Para componentes personalizados populares, a Microsoft e outras empresas podem fornecer exemplos de uso desses componentes em clusters HDInsight. Esses exemplos são fornecidos sem suporte.|

## <a name="next-steps"></a>Próximas etapas

* [Personalizar clusters do Azure HDInsight usando ações de script](./hdinsight-hadoop-customize-cluster-linux.md)
* [Desenvolver scripts de ação de script para o HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Gerenciar com segurança o ambiente do Python no Azure HDInsight usando a Ação de Script](./spark/apache-spark-python-package-installation.md)
