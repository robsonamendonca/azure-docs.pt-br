---
title: Configurar unidades de teste no Microsoft Commercial Marketplace
description: Saiba mais sobre as unidades de teste. As unidades de teste permitem que novos clientes test drive sua oferta antes de confirmar a compra.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/13/2019
ms.openlocfilehash: 8bd17858fa6866549088de8307f40d7b79daef6b
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82786539"
---
# <a name="learn-about-test-drive-for-your-offer"></a>Saiba mais sobre os test drive para sua oferta

A opção **Test Drive** no Microsoft Commercial Marketplace permite configurar um tour prático e autoguiado dos principais recursos do produto. Com um test drive, novos clientes podem experimentar sua oferta antes de confirmar para comprá-la. Para obter mais informações, consulte [o que é Test Drive?](../cloud-partner-portal/test-drive/what-is-test-drive.md).

Se você não quiser mais fornecer um test drive para sua oferta, retorne à página de **instalação da oferta** e desmarque **habilitar Test Drive**.

## <a name="technical-configuration"></a>Configuração técnica

Os seguintes tipos de unidades de teste estão disponíveis, cada um com seus próprios requisitos de configuração técnica.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplicativo lógico](#technical-configuration-for-logic-app-test-drive)
- [Power bi](#technical-configuration-not-required-for-power-bi-test-drives) (configuração técnica não necessária)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Configuração técnica para Azure Resource Manager test drive

Há um modelo de implantação que contém todos os recursos do Azure que compõem sua solução. Isso é adequado para produtos que usam apenas recursos do Azure. Saiba mais sobre como configurar um [test drive de Azure Resource Manager](../cloud-partner-portal/test-drive/azure-resource-manager-test-drive.md).

- **Regiões** (obrigatórias): atualmente, há 26 regiões com suporte do Azure onde o Test Drive pode ser disponibilizado. Normalmente, você desejará disponibilizar seus test drive nas regiões em que você prevê o maior número de clientes, para que eles possam selecionar a região mais próxima para o melhor desempenho. Você precisará certificar-se de que sua assinatura tem permissão para implantar todos os recursos necessários em cada uma das regiões que você está selecionando.

- **Instâncias**: selecione o tipo (quente ou frio) e o número de instâncias disponíveis, que serão multiplicadas pelo número de regiões em que sua oferta está disponível.

  - **Quente**: esse tipo de instância é implantado e aguardando acesso por região selecionada. Os clientes podem acessar instantaneamente as instâncias *quentes* de um Test Drive, em vez de ter que esperar por uma implantação. A desvantagem é que tais instâncias estão sempre em execução na sua assinatura do Azure; portanto, incorrerão em um maior custo de tempo de atividade. É altamente recomendável ter pelo menos uma instância de *acesso* , pois a maioria dos clientes não deseja esperar por implantações completas, resultando em uma retirada no uso do cliente, se nenhuma instância de *acesso* estiver disponível.

  - **Frio**: esse tipo de instância representa o número total de instâncias que podem possivelmente ser implantadas por região. As instâncias frias exigem todo o modelo do Gerenciador de recursos da unidade de teste para implantar quando um cliente solicita a test drive, portanto, as instâncias *frias* são muito mais lentas para serem carregadas do que as instâncias *quentes* . A compensação é que você só precisa pagar pela duração da test drive, *nem* sempre está em execução em sua assinatura do Azure como com uma instância de *Hot* .

- **Testar unidade de Azure Resource Manager modelo**: carregue o. zip que contém o modelo de Azure Resource Manager.  Saiba mais sobre como criar um modelo de Azure Resource Manager no artigo de início rápido [criar e implantar modelos de Azure Resource Manager usando o portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Duração do teste de unidade** (obrigatório): Insira o período de tempo que a unidade de teste permanecerá ativa, em número de horas. O Test Drive é encerrado automaticamente após o término desse período de tempo. Essa duração só pode ser definida por um número inteiro de horas (por exemplo, "2" horas, "1,5" não é válido).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Configuração técnica do Dynamics 365 test drive

A Microsoft pode remover a complexidade de configurar um test drive hospedando e mantendo o provisionamento e a implantação de serviços usando esse tipo de test drive. A configuração para esse tipo de test drive hospedada é a mesma, independentemente de o test drive ter como alvo um centro de negócios, um compromisso com o cliente ou um público de operações.

- **Máximo de unidades de teste simultâneas** (obrigatório): defina o número máximo de clientes que podem usar seu Test Drive ao mesmo tempo. Cada usuário simultâneo consumirá uma licença do Dynamics 365 enquanto o test drive estiver ativo, portanto, será necessário garantir que você tenha licenças suficientes disponíveis para dar suporte ao limite máximo definido. O valor recomendado é de 3 a 5.

- **Duração do teste de unidade** (obrigatório): Insira o período de tempo que a unidade de teste permanecerá ativa definindo o número de horas. Depois disso, a sessão será encerrada e não consumirá mais uma de suas licenças. Recomendamos um valor de 2-24 horas, dependendo da complexidade da sua oferta. Essa duração só pode ser definida por um número inteiro de horas (por exemplo, "2" horas, "1,5" não é válido).  O usuário pode solicitar uma nova sessão se ela ficar sem tempo e desejar acessar a test drive novamente.

- **URL da instância** (obrigatório): a URL em que o cliente começará sua Test Drive. Normalmente, a URL da instância do Dynamics 365 que executa seu aplicativo com os dados de exemplo instalados `https://testdrive.crm.dynamics.com`(por exemplo,).

- **URL da API Web da instância** (necessária): recupere a URL da API da Web para sua instância do Dynamics 365 fazendo logon em sua conta do Microsoft 365 e navegando para **as configurações** \&gt; **Personalização** \&gt; **Recursos** \&para desenvolvedores gt; **API Web da instância (URL da raiz do serviço)**, copie a URL encontrada aqui ( `https://testdrive.crm.dynamics.com/api/data/v9.0`por exemplo,).

- **Nome da função** (obrigatório): forneça o nome da função de segurança que você definiu em seu Test Drive personalizado do Dynamics 365. Isso será atribuído ao usuário durante seu test drive (por exemplo, Test-Drive-Role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Configuração técnica para o aplicativo lógico test drive

Todos os produtos personalizados devem usar esse tipo de test drive modelo de implantação que abrange uma variedade de arquiteturas de solução complexas. Para obter mais informações sobre como configurar unidades lógicas de teste de aplicativo, visite [operações](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [envolvimento do cliente](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) no github.

- **Região** (requerido, lista suspensa de seleção única): atualmente, há 26 regiões com suporte do Azure onde o Test Drive pode ser disponibilizado. Os recursos para seu aplicativo lógico serão implantados na região que você selecionar. Se seu aplicativo lógico tiver qualquer recurso personalizado armazenado em uma região específica, verifique se a região está selecionada aqui. A melhor maneira de fazer isso é implantar totalmente seu aplicativo lógico localmente em sua assinatura do Azure no portal e verificar se ele funciona corretamente antes de fazer essa seleção.

- **Máximo de unidades de teste simultâneas** (obrigatório): defina o número máximo de clientes que podem usar seu Test Drive ao mesmo tempo. Essas unidades de teste já estão implantadas, permitindo que os clientes as acessem instantaneamente sem esperar por uma implantação.

- **Duração do teste de unidade** (obrigatório): Insira o período de tempo que a unidade de teste permanecerá ativa, em número de horas. O test drive é encerrado automaticamente após o término desse período de tempo.

- **Nome do grupo de recursos do Azure** (obrigatório): Insira o nome do [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) em que seu aplicativo lógico Test Drive é salvo.

- **Nome do aplicativo lógico do Azure** (obrigatório): Insira o nome do aplicativo lógico que atribui o Test Drive ao usuário. Este aplicativo lógico deve ser salvo no grupo de recursos do Azure acima.

- **Desprovisionar nome do aplicativo lógico** (obrigatório): Insira o nome do aplicativo lógico que desprovisiona o Test Drive quando o cliente é concluído. Este aplicativo lógico deve ser salvo no grupo de recursos do Azure acima.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Configuração técnica não necessária para unidades de teste de Power BI

Os produtos que desejam demonstrar um visual interativo Power BI podem usar um link incorporado para compartilhar um Dashboard personalizado como seu test drive, nenhuma configuração técnica adicional é necessária. Saiba mais sobre como configurar aplicativos de modelo de [Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview) .

## <a name="deployment-subscription-details"></a>Detalhes da assinatura da implantação

Para implantar o Test Drive em seu nome, crie e forneça uma assinatura do Azure separada e exclusiva. (Não é necessário para Power BI unidades de teste).

- **ID da assinatura do Azure** (necessária para Azure Resource Manager e aplicativos lógicos): Insira a ID da assinatura para conceder acesso aos serviços de conta do Azure para relatório e cobrança de uso de recursos. Recomendamos que você considere a [criação de uma assinatura do Azure separada](https://docs.microsoft.com/azure/billing/billing-create-subscription) a ser usada para unidades de teste, se você ainda não tiver uma. Você pode encontrar sua ID de assinatura do Azure fazendo logon no [portal do Azure](https://portal.azure.com/) e navegando até a guia **assinaturas** do menu do lado esquerdo. A seleção da guia exibirá sua ID de assinatura ( `a83645ac-1234-5ab6-6789-1h234g764ghty`por exemplo).

- **ID do locatário do Azure ad** (obrigatório): Insira sua ID de [locatário](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)do Azure Active Directory (AD). Para localizar essa ID, entre no [portal do Azure](https://portal.azure.com/), selecione a guia Active Directory no menu à esquerda, selecione **Propriedades** e procure o número de **ID de diretório** listado (por exemplo, `50c464d3-4930-494c-963c-1e951d15360e`). Você também pode pesquisar a ID de locatário da sua organização usando sua URL de nome de [https://www.whatismytenantid.com](https://www.whatismytenantid.com)domínio em:.

- **Nome do locatário do Azure ad** (necessário para o Dynamic 365): Insira seu nome de Azure Active Directory (AD). Para localizar esse nome, entre no [portal do Azure](https://portal.azure.com/), no canto superior direito, o nome do locatário será listado em seu nome de conta.

- **ID do aplicativo do Azure ad** (obrigatório): Insira sua ID do [aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)de Azure Active Directory (AD). Para localizar essa ID, entre no [portal do Azure](https://portal.azure.com/), selecione a guia Active Directory no menu à esquerda, selecione **registros de aplicativo**e procure o número de **ID do aplicativo** listado (por exemplo, `50c464d3-4930-494c-963c-1e951d15360e`).

- **Segredo do cliente de aplicativo do Azure ad** (obrigatório): Insira o [segredo do cliente](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)de aplicativo do Azure AD. Para localizar esse valor, entre no [portal do Azure](https://portal.azure.com/). Selecione a guia **Azure Active Directory** no menu à esquerda, selecione **registros de aplicativo**e, em seguida, selecione seu aplicativo Test Drive. Em seguida, **Selecione certificados e segredos**, selecione **novo segredo do cliente**, insira uma descrição, selecione **nunca** em **expirar**e, em seguida, escolha **Adicionar**. Certifique-se de copiar o valor. (Não navegue para fora da página antes de fazer isso, ou você não terá acesso ao valor.)

Lembre-se de **salvar** antes de passar para a próxima seção!

## <a name="test-drive-listings-optional"></a>Testar listagens de unidade (opcional)

A opção de **lista de unidades de teste** encontrada na guia **Test Drive** exibe os idiomas (e os mercados) em que o Test Drive está disponível, atualmente em inglês (Estados Unidos) é o único local disponível. Além disso, essa página exibe o status da listagem específica do idioma e a data/hora em que ela foi adicionada. Você precisará definir os detalhes da test drive (descrição, manual do usuário, vídeos, etc.) para cada idioma/mercado.

- **Descrição** (obrigatório): descreva sua Test Drive, o que será demonstrado, os objetivos para o usuário experimentar, os recursos a serem explorados e todas as informações relevantes para ajudar o usuário a determinar se deseja adquirir sua oferta. Até 3.000 caracteres de texto podem ser inseridos neste campo.

- **Informações de acesso** (necessárias para unidades de teste de Azure Resource Manager e lógica): explique o que um cliente precisa saber para acessar e usar esse Test Drive. Percorra um cenário para usar sua oferta e exatamente o que o cliente deve saber para acessar recursos em todo o test drive. Até 10.000 caracteres de texto podem ser inseridos neste campo.

- **Manual do usuário** (obrigatório): uma explicação detalhada de sua experiência de Test Drive. O manual do usuário deve abranger exatamente o que você deseja que o cliente tenha de apresentar o test drive e servir como uma referência para quaisquer perguntas que possam ter. O arquivo deve estar no formato PDF e ter o nome (máximo de 255 caracteres) após o carregamento.

- **Vídeos: adicionar vídeos** (opcional): os vídeos podem ser carregados no YouTube ou Vimeo e mencionados aqui com uma imagem de link e miniatura (533 x 324 pixels) para que um cliente possa exibir um passo a passo de informações para ajudá-los a entender melhor os Test Drive, incluindo como usar com êxito os recursos de sua oferta e entender os cenários que destacam seus benefícios.
  - **Nome** (obrigatório)
  - **URL (somente YouTube ou Vimeo)** (obrigatório)
  - **Miniatura (533 x 324 px)** -o arquivo de imagem deve estar no formato png.

Selecione **salvar** depois de concluir esses campos.

## <a name="next-steps"></a>Próximas etapas

[Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)
