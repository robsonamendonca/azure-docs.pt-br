---
title: Planejar políticas de acesso condicional no Azure Active Directory | Microsoft Docs
description: Neste artigo, você aprenderá a planejar políticas de acesso condicional para Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1c75d5022432a9a57b30aabec4dd2c4f76f2f29
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78671823"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>Como planejar sua implantação de acesso condicional no Azure Active Directory

Planejar sua implantação de acesso condicional é essencial para garantir que você obtenha a estratégia de acesso necessária para aplicativos e recursos em sua organização. Passe a maior parte do tempo durante a fase de planejamento de sua implantação para projetar as várias políticas necessárias para conceder ou bloquear o acesso aos usuários sob as condições escolhidas. Este documento explica as etapas que você deve seguir para implementar políticas de acesso condicional seguras e eficazes. Antes de começar, verifique se você entendeu como o [acesso condicional](overview.md) funciona e quando você deve usá-lo.

## <a name="what-you-should-know"></a>O que você deve saber

Considere o acesso condicional como uma estrutura que permite controlar o acesso aos aplicativos e recursos de sua organização, em vez de um recurso autônomo. Consequentemente, algumas configurações de acesso condicional exigem recursos adicionais a serem configurados. Por exemplo, é possível configurar uma política que responda a um [nível de risco de credenciais](../identity-protection/howto-identity-protection-configure-risk-policies.md) específico. No entanto, uma política que se baseia em um nível de risco de credenciais exige que o [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md) seja habilitado.

Se forem necessários recursos adicionais, talvez você também precise obter licenças relacionadas. Por exemplo, embora o acesso condicional seja Azure AD Premium recurso P1, a proteção de identidade requer uma licença Azure AD Premium P2.

Há dois tipos de políticas de acesso condicional: linha de base e padrão. Uma [política de linha de base](baseline-protection.md) é uma política de acesso condicional predefinida. A meta dessas políticas é garantir que você tenha pelo menos o nível básico de segurança habilitado. Políticas de linha de base. As políticas de linha de base estão disponíveis em todas as edições do Azure Active Directory e fornecem apenas opções de personalização limitadas. Se um cenário exigir mais flexibilidade, desabilite a política de linha de base e implemente seus requisitos em uma política padrão personalizada.

Em uma política de acesso condicional padrão, você pode personalizar todas as configurações para ajustar a política aos seus requisitos de negócios. As políticas padrão exigem uma licença P1 do Azure AD Premium.

>[!NOTE]
> É recomendável usar a política de acesso condicional baseada em dispositivo do Azure AD para obter a melhor imposição após a autenticação inicial do dispositivo. Isso inclui sessões de fechamento se o dispositivo ficar sem conformidade e fluxo de código de dispositivo.

## <a name="draft-policies"></a>Rascunho de políticas

Azure Active Directory acesso condicional permite que você traga a proteção de seus aplicativos de nuvem para um novo nível. Nesse novo nível, o modo de acessar um aplicativo em nuvem baseia-se em uma avaliação de política dinâmica, e não em uma configuração de acesso estático. Com uma política de acesso condicional, você define uma resposta (**faça isso**) para uma condição de acesso (**quando isso acontecer**).

![Motivo e resposta](./media/plan-conditional-access/10.png)

Defina cada política de acesso condicional que você deseja implementar usando este modelo de planejamento. O exercício de planejamento:

- Ajuda a descrever as respostas e condições para cada política.
- Resulta em um catálogo de política de acesso condicional bem documentado para sua organização. 

Você pode usar o catálogo para avaliar se a implementação da sua política reflete os requisitos de negócios da organização. 

Use o modelo de exemplo a seguir para criar políticas de acesso condicional para sua organização:

|Quando *isso* ocorrer:|Faça *isso*:|
|-|-|
|For feita uma tentativa de acesso:<br>-Para um aplicativo*<br>de nuvem – por usuários e grupos*<br>Usando:<br>- Condição 1 (por exemplo, fora da rede corporativa)<br>- Condição 2 (por exemplo, plataformas de dispositivo)|Bloqueie o acesso ao aplicativo|
|For feita uma tentativa de acesso:<br>-Para um aplicativo*<br>de nuvem – por usuários e grupos*<br>Usando:<br>- Condição 1 (por exemplo, fora da rede corporativa)<br>- Condição 2 (por exemplo, plataformas de dispositivo)|Conceda acesso com (E):<br>- Requisito 1 (por exemplo, MFA)<br>- Requisito 2 (por exemplo, conformidade de dispositivo)|
|For feita uma tentativa de acesso:<br>-Para um aplicativo*<br>de nuvem – por usuários e grupos*<br>Usando:<br>- Condição 1 (por exemplo, fora da rede corporativa)<br>- Condição 2 (por exemplo, plataformas de dispositivo)|Conceda acesso com (OU):<br>- Requisito 1 (por exemplo, MFA)<br>- Requisito 2 (por exemplo, conformidade de dispositivo)|

No mínimo, **quando isso ocorrer** define a entidade de segurança (**quem**) que tenta acessar um aplicativo em nuvem (**o quê**). Se necessário, você também pode incluir **como** uma tentativa de acesso é realizada. No acesso condicional, os elementos que definem quem, o que e como são conhecidos como condições. Para obter mais informações, consulte [o que são condições em Azure Active Directory acesso condicional?](concept-conditional-access-conditions.md) 

Com **faça isso**, você define a resposta da sua política a uma condição de acesso. Em sua resposta, você bloqueia ou concede acesso com requisitos adicionais, por exemplo, MFA (autenticação multifator). Para obter uma visão geral completa, consulte [o que são controles de acesso no Azure Active Directory acesso condicional?](controls.md)  

A combinação de condições e seus controles de acesso representa uma política de Acesso Condicional.

![Motivo e resposta](./media/plan-conditional-access/51.png)

Para saber mais, consulte [O que é necessário para que uma política funcione](best-practices.md#whats-required-to-make-a-policy-work).

Agora é um bom momento para decidir sobre um padrão de nomenclatura para as suas políticas. O padrão de nomenclatura ajuda você a localizar as políticas e a entender a finalidade delas sem precisar abri-las no portal de administração do Azure. Nomeie sua política para mostrar:

- Um número sequencial
- O aplicativo em nuvem ao qual ela se aplica
- A resposta
- A quem ela se aplica
- Quando ela se aplica (se aplicável)
 
![Padrão de nomenclatura](./media/plan-conditional-access/11.png)

Embora um nome descritivo ajude você a manter uma visão geral da sua implementação de acesso condicional, o número de sequência será útil se você precisar fazer referência a uma política em uma conversa. Por exemplo, se você falar com um colega de administrador no telefone, solicite a eles que abram o Policy EM063 para resolver um problema.

Por exemplo, o seguinte nome declara que a política exige MFA para usuários de marketing em redes externas usando o aplicativo Dynamics CRP:

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

Além das suas políticas ativas, também é aconselhável implementar políticas desabilitadas que atuem como [controles de acesso resilientes secundários em cenários de interrupção/emergência](../authentication/concept-resilient-controls.md). O padrão de nomenclatura para as políticas de contingência deve incluir mais alguns itens: 

- `ENABLE IN EMERGENCY` no início para destacar o nome entre as outras políticas.
- O nome da interrupção a qual ele deve ser aplicado.
- Um número de sequência de ordenação para ajudar o administrador a saber em qual ordem as políticas devem ser habilitadas. 

Por exemplo, o seguinte nome indica que essa política é a primeira política de quatro que você deve habilitar se houver uma interrupção de MFA:

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>Planejar políticas

Ao planejar sua solução de política de acesso condicional, avalie se você precisa criar políticas para obter os resultados a seguir. 

### <a name="block-access"></a>Acesso bloqueado

A opção de bloquear o acesso é eficiente porque:

- Supera todas as outras atribuições de um usuário
- Tem a capacidade de impedir que a organização inteira entre no seu locatário
 
Se quiser bloquear o acesso para todos os usuários, você deve excluir, pelo menos, um usuário (geralmente contas de acesso à emergência) da política. Para saber mais, consulte [Selecionar usuários e grupos](block-legacy-authentication.md#select-users-and-cloud-apps).  

### <a name="require-mfa"></a>Exigir MFA

Para simplificar a experiência de entrada dos usuários, talvez você deva permitir que eles entrem nos aplicativos de nuvem usando um nome de usuário e uma senha. Entretanto, de modo geral, existem alguns cenários para os quais é aconselhável exigir um formulário de verificação de conta mais rigoroso. Com uma política de acesso condicional, você pode limitar o requisito de MFA para determinados cenários. 

Os casos de uso comuns para exigir MFA são os acessos:

- [Por administradores](howto-baseline-protect-administrators.md)
- [Para aplicativos específicos](app-based-mfa.md) 
- [Em locais de rede nos quais você não confia](untrusted-networks.md).

### <a name="respond-to-potentially-compromised-accounts"></a>Responder a contas potencialmente comprometidas

Com as políticas de acesso condicional, você pode implementar respostas automatizadas para entradas de identidades potencialmente comprometidas. A probabilidade de que uma conta tenha sido comprometida é expressa na forma de níveis de risco. Há dois níveis de risco calculados pela proteção de identidade: risco de credenciais e risco de usuário. Para implementar uma resposta em um risco de credenciais, você tem duas opções:

- [A condição de risco de entrada](concept-conditional-access-conditions.md#sign-in-risk) na política de acesso condicional
- [A política de risco de credenciais](../identity-protection/howto-sign-in-risk-policy.md) na proteção de identidade 

Abordar o risco de credenciais como condição é o método preferencial, pois ele proporciona a você mais opções de personalização.

O nível de risco de usuário está disponível somente como [política de risco de usuário](../identity-protection/howto-user-risk-policy.md) na proteção de identidade. 

Para saber mais, consulte [O que é o Azure Active Directory Identity Protection?](../identity-protection/overview.md) 

### <a name="require-managed-devices"></a>Requer dispositivos gerenciados

A proliferação de dispositivos com suporte para acessar os recursos de nuvem ajuda a melhorar a produtividade dos usuários. Por outro lado, não convém certos recursos em seu ambiente para ser acessado por dispositivos com um nível de proteção desconhecido. Para os recursos afetados, você deve exigir que os usuários possam acessar somente usando um dispositivo gerenciado. Para obter mais informações, consulte [como exigir dispositivos gerenciados para acesso ao aplicativo de nuvem com acesso condicional](require-managed-devices.md). 

### <a name="require-approved-client-apps"></a>Requer aplicativos de cliente aprovados

Uma das primeiras decisões que você precisa tomar nos cenários BYOD (traga seus próprios dispositivos) é se há necessidade de gerenciar todo o dispositivo ou apenas seus dados. Os funcionários usam dispositivos móveis para tarefas de pessoais e corporativas. Ao mesmo tempo que garante que seus funcionários sejam produtivos, você também quer impedir a perda de dados. Com o acesso condicional do Azure Active Directory (AD do Azure), você pode restringir o acesso aos aplicativos de nuvem a aplicativos cliente aprovados que podem proteger seus dados corporativos. Para obter mais informações, consulte [como exigir aplicativos cliente aprovados para acesso de aplicativo de nuvem com acesso condicional](app-based-conditional-access.md).

### <a name="block-legacy-authentication"></a>Bloquear a autenticação herdada

O Azure AD dá suporte para vários dos protocolos de autenticação e autorização mais amplamente utilizados, incluindo a autenticação herdada. Como é possível impedir que aplicativos usando autenticação herdada acessem os recursos do locatário? A recomendação é simplesmente bloqueá-los com uma política de acesso condicional. Se necessário, você permite que apenas determinados usuários e locais de rede específicos usem aplicativos baseados em autenticação herdada. Para obter mais informações, consulte [como bloquear a autenticação herdada no Azure AD com acesso condicional](block-legacy-authentication.md).

## <a name="test-your-policy"></a>Testar sua política

Antes de implementar uma política no ambiente de produção, você deve testá-la a fim de verificar se ela se comporta conforme o esperado.

1. Criar usuários de teste
1. Criar um plano de deste
1. Configurar a política
1. Avaliar uma entrada simulada
1. Testar sua política
1. Limpeza

### <a name="create-test-users"></a>Criar usuários de teste

Para testar uma política, crie um conjunto de usuários semelhante aos usuários do seu ambiente. A criação de usuários de teste permite verificar se as políticas funcionam conforme esperado antes de afetar os usuários reais e potencialmente interromper o acesso deles aos aplicativos e recursos. 

Algumas organizações têm locatários de teste para essa finalidade. No entanto, pode ser difícil recriar todas as condições e os aplicativos em um locatário de teste para testar totalmente o resultado de uma política. 

### <a name="create-a-test-plan"></a>Criar um plano de deste

O plano de teste é importante para que se tenha uma comparação entre os resultados esperados e os resultados reais. Você sempre deve ter uma expectativa antes de testar algo. A tabela a seguir descreve exemplos de casos de teste. Ajuste os cenários e os resultados esperados com base na configuração de suas políticas de autoridade de certificação.

|Política |Cenário |Resultado esperado | Result |
|---|---|---|---|
|[Exigir MFA quando não estiver no trabalho](/azure/active-directory/conditional-access/untrusted-networks)|Usuário autorizado entra no *Aplicativo* quando está no trabalho/em um local confiável|Não é solicitado que o usuário use a MFA| |
|[Exigir MFA quando não estiver no trabalho](/azure/active-directory/conditional-access/untrusted-networks)|Usuário autorizado entra no *Aplicativo* quando não está no trabalho/em um local confiável|É solicitado que o usuário use a MFA para se conectar com êxito| |
|[Exigir MFA (para administradores)](/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|Administrador global entra no *Aplicativo*|É solicitado que o administrador use MFA| |
|[Entradas de risco](/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|O usuário entra no *Aplicativo* usando um [navegador Tor](/azure/active-directory/active-directory-identityprotection-playbook)|É solicitado que o administrador use MFA| |
|[Gerenciamento de dispositivos](/azure/active-directory/conditional-access/require-managed-devices)|O usuário autorizado tenta se conectar usando um dispositivo autorizado|Acesso concedido| |
|[Gerenciamento de dispositivos](/azure/active-directory/conditional-access/require-managed-devices)|O usuário autorizado tenta se conectar usando um dispositivo não autorizado|Acesso bloqueado| |
|[Alteração de senha para usuários arriscados](/azure/active-directory/identity-protection/howto-user-risk-policy)|O usuário autorizado tenta se conectar com credenciais comprometidas (conexão de alto risco)|O usuário é solicitado a alterar a senha ou o acesso é bloqueado com base na sua política| |

### <a name="configure-the-policy"></a>Configurar a política

O gerenciamento de políticas de acesso condicional é uma tarefa manual. No portal do Azure, você pode gerenciar suas políticas de acesso condicional em um local central-a página de acesso condicional. Um ponto de entrada para a página de acesso condicional é a seção **segurança** no painel de navegação **Active Directory** . 

![Acesso condicional](media/plan-conditional-access/03.png)

Se você quiser saber mais sobre como criar políticas de acesso condicional, consulte [exigir MFA para aplicativos específicos com Azure Active Directory acesso condicional](app-based-mfa.md). Este início rápido ajuda você a:

- Familiarizar-se com a interface do usuário.
- Obtenha uma primeira impressão de como o acesso condicional funciona. 

### <a name="evaluate-a-simulated-sign-in"></a>Avaliar uma entrada simulada

Agora que você configurou a política de acesso condicional, provavelmente deseja saber se ela funciona conforme o esperado. Como uma primeira etapa, use a ferramenta de política de acesso condicional [What If](what-if-tool.md) para simular uma entrada de seu usuário de teste. A simulação calcula o impacto que esse logon tem em suas políticas e gera um relatório de simulação.

>[!NOTE]
> Embora uma execução simulada lhe dê a impressão do impacto de uma política de acesso condicional, ela não substitui uma execução de teste real.

### <a name="test-your-policy"></a>Testar sua política

Execute casos de teste de acordo com o seu plano de teste. Nessa etapa, você passa por um teste completo de cada política para os usuários de teste a fim de garantir que cada política se comporte corretamente. Use os cenários criados acima para executar cada teste.

É importante ter absoluta certeza de testar os critérios de exclusão de uma política. Por exemplo, você pode excluir um usuário ou grupo de uma política que exige MFA. Teste se os usuários excluídos são solicitados pela MFA, porque a combinação de outras políticas pode exigir MFA para esses usuários.

### <a name="cleanup"></a>Limpeza

O procedimento de limpeza consiste nas seguintes etapas:

1. Desabilitar a política.
1. Remover os usuários e grupos atribuídos.
1. Excluir os usuários de teste.  

## <a name="move-to-production"></a>Mover para ambiente de produção

Quando novas políticas estiverem prontas para o seu ambiente, implante-as em fases:

- Comunique essa mudança interna aos usuários finais.
- Comece com um pequeno conjunto de usuários e verifique se a política se comporta conforme esperado.
- Ao expandir uma política para incluir mais usuários, continue a excluir todos os administradores. A exclusão de administradores garante que alguém ainda tenha acesso a uma política se for necessária uma alteração.
- Aplique uma política a todos os usuários somente se ela for necessária.

Como prática recomendada, crie pelo menos uma conta de usuário que seja:

- Dedicado à administração de política
- Excluído de todas as suas políticas

## <a name="rollback-steps"></a>Etapas de reversão

Caso você precise reverter as suas políticas recentemente implementadas, use uma ou várias das opções a seguir para isso:

1. **Desabilitar a política**: desabilitar uma política garante que ela não seja aplicada quando um usuário tenta se conectar. Você sempre pode voltar e habilitar a política quando quiser usá-la.

   ![Desabilitar política](media/plan-conditional-access/07.png)

1. **Excluir um usuário/grupo de uma política**: se um usuário não puder acessar o aplicativo, opte por excluir o usuário da política

   ![Excluir usuários](media/plan-conditional-access/08.png)

   > [!NOTE]
   > Essa opção deve ser usada com moderação, somente em situações em que o usuário é confiável. O usuário deve ser adicionado de volta à política ou ao grupo assim que possível.

1. **Excluir a política**: se a política não for mais necessária, exclua-a.

## <a name="next-steps"></a>Próximas etapas

Consulte a [Documentação de Acesso Condicional do Azure Active Directory](index.yml) para obter uma visão geral das informações disponíveis.
