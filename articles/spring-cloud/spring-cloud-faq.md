---
title: Perguntas frequentes sobre o Azure Spring Cloud | Microsoft Docs
description: Este artigo responde às perguntas frequentes sobre o Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 95260d9a15fdc32c9fddccbcf63ae9fa564fd36a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176763"
---
# <a name="azure-spring-cloud-faq"></a>FAQ do Azure Spring Cloud

Este artigo responde às perguntas frequentes sobre o Azure Spring Cloud.

## <a name="general"></a>Geral

### <a name="why-azure-spring-cloud"></a>Por que o Azure Spring Cloud?

O Azure Spring Cloud fornece uma PaaS (plataforma como serviço) para desenvolvedores de nuvem Spring. O Azure Spring Cloud gerencia sua infraestrutura de aplicativo para que você possa se concentrar no código do aplicativo e na lógica de negócios. Os principais recursos incorporados ao Azure Spring Cloud incluem Eureka, servidor de configuração, servidor de registro de serviço, serviço de compilação dinâmica, implantações em verde-azulado e muito mais. Esse serviço também permite que os desenvolvedores associem seus aplicativos a outros serviços do Azure, como Azure Cosmos DB, banco de dados do Azure para MySQL e cache do Azure para Redis.

O Azure Spring Cloud aprimora a experiência do Application Diagnostics para desenvolvedores e operadores integrando Azure Monitor, Application Insights e Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Quais planos de serviço o Azure Spring Cloud oferece?

O Azure Spring Cloud oferece um plano de serviço durante o período de versão prévia.  Uma implantação de Spring Cloud contém 16 núcleos de vCPU e 32 gigabytes (GB) de memória.  O limite superior para cada instância de microserviço em uma implantação é de 4 núcleos vCPU com 8 GB de memória.

Recurso | Amount
------- | -------
Instâncias de aplicativo por aplicativo Spring | 20
Instâncias totais de aplicativo por instância de serviço do Azure Spring Cloud | 500
Instâncias de serviço do Azure Spring Cloud por região por assinatura | 10
Volumes persistentes | 10 x 50 GBytes

\*_Para aumentar o limite, abra um [tíquete de suporte](https://azure.microsoft.com/support/faq/)._

Para obter mais informações, consulte [perguntas frequentes sobre o suporte do Azure](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Quão seguro é o Azure Spring Cloud?

A segurança e a privacidade estão entre as principais prioridades do Azure e dos clientes do Azure Spring Cloud. O Azure ajuda a garantir que somente os clientes tenham acesso a dados, logs ou configurações de aplicativos, criptografando com segurança todos esses dados. Todas as instâncias de serviço no Azure Spring Cloud estão isoladas umas das outras.

O Azure Spring Cloud fornece o TLS/SSL e o gerenciamento de certificados completos.

Os patches de segurança críticos para OpenJDK e tempos de execução do Spring Cloud são aplicados ao Azure Spring Cloud o quanto antes.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Em quais regiões o Azure Spring Cloud está disponível?

Leste dos EUA, Oeste dos EUA 2, Oeste da Europa e Sudeste Asiático.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Quais são as limitações conhecidas do Azure Spring Cloud?

Durante a versão de visualização, o Azure Spring Cloud tem as seguintes limitações conhecidas:

* `spring.application.name`será substituído pelo nome do aplicativo usado para criar cada aplicativo.
* `server.port`Não é permitido no arquivo de configuração do repositório git. Adicioná-lo ao arquivo de configuração provavelmente tornará o aplicativo inacessível de outros aplicativos ou da Internet.
* Os modelos portal do Azure e Azure Resource Manager não dão suporte ao carregamento de pacotes de aplicativos. Você pode carregar pacotes de aplicativos somente implantando o aplicativo por meio do CLI do Azure.
* Para saber mais sobre limitações de cota, consulte [quais planos de serviço a oferta do Azure Spring Cloud oferece?](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Como eu posso fornecer comentários e relato problemas?

Se você encontrar problemas com o Azure Spring Cloud, crie uma [solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Para enviar uma solicitação de recurso ou fornecer comentários, acesse [comentários do Azure](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Desenvolvimento

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Sou um desenvolvedor de nuvem Spring, mas novo no Azure. Qual é a maneira mais rápida de aprender a desenvolver um aplicativo de nuvem Spring do Azure?

Para obter a maneira mais rápida de começar a usar o Azure Spring Cloud, siga as instruções em [início rápido: iniciar um aplicativo do Azure Spring Cloud usando o portal do Azure](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>A qual Java Runtime o Azure Spring Cloud dá suporte?

O Azure Spring Cloud dá suporte ao Java 8 e 11.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Onde posso exibir meus logs e métricas de aplicativos de nuvem Spring?

Encontre métricas na guia Visão Geral do Aplicativo e na guia [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics).

O Azure Spring Cloud dá suporte à exportação de métricas e logs de aplicativos de nuvem Spring para o armazenamento do Azure, o EventHub e o [log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). O nome da tabela em Log Analytics é *AppPlatformLogsforSpring*. Para saber como habilitá-lo, consulte [serviços de diagnóstico](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>O Azure Spring Cloud dá suporte ao rastreamento distribuído?

Sim. Para obter mais informações, consulte [tutorial: usar o rastreamento distribuído com o Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>A quais tipos de recurso a Associação de Serviço dá suporte?

Atualmente, há suporte para três serviços: Azure Cosmos DB, banco de dados do Azure para MySQL e cache do Azure para Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Posso exibir, adicionar ou mover volumes persistentes de dentro de meus aplicativos?

Sim.

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>Quando eu excluir/mover uma instância do serviço de nuvem do Azure Spring, seus recursos de extensão também serão excluídos/movidos?

Depende das lógicas dos provedores de recursos para os quais os recursos de extensão pertencem. Os recursos de extensão de `Microsoft.AppPlatform` uma instância não pertencem ao mesmo namespace, portanto, os comportamentos variam de acordo com provedores de recursos diferentes. Por exemplo, a operação de exclusão/movimentação não será em cascata para os recursos de **configurações de diagnóstico** . Se uma nova instância do Azure Spring Cloud for provisionada com a mesma ID de recurso que a excluída, ou se a instância anterior do Azure Spring Cloud for movida de volta, os recursos anteriores de **configurações de diagnóstico** continuarão a estendê-la.

## <a name="deployment"></a>Implantação

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>O Azure Spring Cloud dá suporte à implantação azul-verde?
Sim. Para obter mais informações, consulte [configurar um ambiente de preparo](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Posso acessar o Kubernetes para manipular meus contêineres de aplicativos?

Não.  O Azure Spring Cloud abstrai o desenvolvedor da arquitetura subjacente, permitindo que você se concentre no código do aplicativo e na lógica de negócios.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>O Azure Spring Cloud dá suporte à associação de contêineres da origem?

Sim. Para obter mais informações, consulte [Iniciar seu aplicativo Spring Cloud do código-fonte](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>O Azure Spring Cloud dá suporte ao dimensionamento automático nas instâncias do aplicativo?

Não.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Quais são as práticas recomendadas para migrar os microserviços existentes do Spring Cloud para o Azure Spring Cloud?

À medida que estiver migrando os microserviços existentes do Spring Cloud para o Azure Spring Cloud, é uma boa ideia observar as seguintes práticas recomendadas:
* todas as dependências do aplicativo precisam ser resolvidas.
* Prepare suas entradas de configuração, variáveis de ambiente e parâmetros JVM para que você possa compará-las com a implantação no Azure Spring Cloud.
* Se você quiser usar a associação de serviço, passe pelos serviços do Azure e verifique se você definiu as permissões de acesso apropriadas.
* Recomendamos que você remova ou desabilite os serviços inseridos que podem entrar em conflito com os serviços gerenciados pelo Azure Spring Cloud, como nosso serviço de descoberta de serviços, servidor de configuração e assim por diante.
* Recomendamos que você use bibliotecas dinâmicas e estáveis do Spring pivot. Versões não oficiais, beta ou bifurcadas de bibliotecas de Spring dinâmicos não têm suporte de SLA (contrato de nível de serviço).

Após a migração, monitore suas métricas de CPU/RAM e o tráfego de rede para garantir que as instâncias do aplicativo sejam dimensionadas adequadamente.

## <a name="next-steps"></a>Próximas etapas

Se você tiver outras dúvidas, consulte o [Guia de solução de problemas do Azure Spring Cloud](spring-cloud-troubleshoot.md).
