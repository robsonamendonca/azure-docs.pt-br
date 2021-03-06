---
title: Chave de segurança sem senha no Windows-Azure Active Directory
description: Saiba como habilitar a entrada de chave de segurança sem senha para Azure Active Directory usando chaves de segurança FIDO2 (versão prévia)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b8f5d6aaa96c24eb37eb78d237a489f1d25293c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80653997"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory-preview"></a>Habilitar a entrada de chave de segurança sem senha em dispositivos Windows 10 com Azure Active Directory (versão prévia)

Este documento se concentra em habilitar a autenticação sem senha baseada em chave de segurança FIDO2 com dispositivos Windows 10. No final deste artigo, você poderá entrar no Azure AD e no Azure AD híbrido ingressado em dispositivos Windows 10 com sua conta do Azure AD usando uma chave de segurança FIDO2.

|     |
| --- |
| As chaves de segurança do FIDO2 são um recurso de visualização pública do Azure Active Directory. Para obter mais informações sobre visualizações, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Requisitos

| Tipo de dispositivo | Adicionado ao Azure AD | Adicionado ao Azure AD híbrido |
| --- | --- | --- |
| [Autenticação Multifator do Azure](howto-mfa-getstarted.md) | X | X |
| [Visualização do registro de informações de segurança combinadas](concept-registration-mfa-sspr-combined.md) | X | X |
| [Chaves de segurança FIDO2](concept-authentication-passwordless.md#fido2-security-keys) compatíveis | X | X |
| Webauthn requer o Windows 10 versão 1809 ou superior | X | X |
| [Dispositivos ingressados no Azure ad](../devices/concept-azure-ad-join.md) requerem o Windows 10 versão 1903 ou superior | X |   |
| [Dispositivos ingressados no Azure ad híbrido](../devices/concept-azure-ad-join-hybrid.md) exigem o Windows 10 Insider Build 18945 ou superior |   | X |
| Controladores de domínio do Windows Server 2016/2019 totalmente corrigidos. |   | X |
| [Azure ad Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) versão 1.4.32.0 ou posterior |   | X |
| [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (opcional) | X | X |
| Pacote de provisionamento (opcional) | X | X |
| Política de Grupo (opcional) |   | X |

### <a name="unsupported-scenarios"></a>Cenários sem suporte

Os cenários a seguir não têm suporte:

- Implantação do Windows Server Active Directory Domain Services (AD DS) ingressado no domínio (dispositivos somente locais).
- Cenários de RDP, VDI e Citrix usando uma chave de segurança.
- S/MIME usando uma chave de segurança.
- "Executar como" usando uma chave de segurança.
- Faça logon em um servidor usando uma chave de segurança.
- Se você não tiver usado sua chave de segurança para entrar em seu dispositivo enquanto estiver online, você não poderá usá-lo para entrar ou desbloquear offline.
- Entrar ou desbloquear um dispositivo Windows 10 com uma chave de segurança que contém várias contas do Azure AD. Esse cenário utiliza a última conta adicionada à chave de segurança. Webauthn permite que os usuários escolham a conta que desejam usar.
- Desbloqueie um dispositivo que executa o Windows 10 versão 1809. Para obter a melhor experiência, use o Windows 10 versão 1903 ou superior.

## <a name="prepare-devices-for-preview"></a>Preparar dispositivos para visualização

Os dispositivos ingressados no Azure AD que você está realizando o piloto durante a visualização do recurso com devem executar o Windows 10 versão 1809 ou superior. A melhor experiência é no Windows 10 versão 1903 ou superior.

Dispositivos ingressados no Azure AD híbrido devem executar o Windows 10 Insider Build 18945 ou mais recente.

## <a name="enable-security-keys-for-windows-sign-in"></a>Habilitar chaves de segurança para entrada no Windows

As organizações podem optar por usar um ou mais dos seguintes métodos para habilitar o uso de chaves de segurança para entrada do Windows com base nos requisitos de sua organização:

- [Habilitar com o Intune](#enable-with-intune)
- [Implantação do Intune de destino](#targeted-intune-deployment)
- [Habilitar com um pacote de provisionamento](#enable-with-a-provisioning-package)
- [Habilitar com Política de Grupo (somente dispositivos ingressados no Azure AD híbrido)](#enable-with-group-policy)

> [!IMPORTANT]
> As organizações com **dispositivos ingressados no Azure ad híbrido** **também** devem concluir as etapas no artigo [habilitar a autenticação FIDO2 para recursos locais](howto-authentication-passwordless-security-key-on-premises.md) antes que a autenticação de chave de segurança do Windows 10 FIDO2 funcione.
>
> As organizações com **dispositivos ingressados no Azure ad** devem fazer isso antes que seus dispositivos possam se autenticar em recursos locais com chaves de segurança FIDO2.

### <a name="enable-with-intune"></a>Habilitar com o Intune

Para habilitar o uso de chaves de segurança usando o Intune, conclua as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Navegue até **Microsoft Intune** > registro de**dispositivo** > inscrição do**Windows** > **Windows Hello para empresas** > **Propriedades**.
1. Em **configurações**, defina **usar chaves de segurança para entrar** como **habilitado**.

A configuração de chaves de segurança para entrada não depende da configuração do Windows Hello para empresas.

### <a name="targeted-intune-deployment"></a>Implantação do Intune de destino

Para direcionar grupos de dispositivos específicos para habilitar o provedor de credenciais, use as seguintes configurações personalizadas por meio do Intune:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Navegue até **Microsoft Intune** > **Device configuration** > **Profiles**perfis > de configuração de dispositivo**Criar perfil**.
1. Configure o novo perfil com as seguintes configurações:
   - Nome: chaves de segurança para entrar no Windows
   - Descrição: permite que as chaves de segurança FIDO sejam usadas durante a entrada do Windows
   - Plataforma: Windows 10 e posterior
   - Tipo de perfil: personalizado
   - Configurações personalizadas de OMA-URI:
      - Nome: ativar as chaves de segurança do FIDO para entrar no Windows
      - OMA-URI:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - Tipo de dados: inteiro
      - Valor: 1
1. Essa política pode ser atribuída a usuários, dispositivos ou grupos específicos. Para obter mais informações, consulte [atribuir perfis de usuário e de dispositivo no Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Criação de política de configuração de dispositivo personalizada do Intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Habilitar com um pacote de provisionamento

Para dispositivos não gerenciados pelo Intune, um pacote de provisionamento pode ser instalado para habilitar a funcionalidade. O aplicativo do Windows Configuration designer pode ser instalado por meio do [Microsoft Store](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22). Conclua as seguintes etapas para criar um pacote de provisionamento:

1. Inicie o designer de configuração do Windows.
1. Selecione **arquivo** > **novo projeto**.
1. Dê um nome ao projeto e anote o caminho em que o projeto foi criado e, em seguida, selecione **Avançar**.
1. Deixe o *pacote de provisionamento* selecionado como o **fluxo de trabalho do projeto selecionado** e selecione **Avançar**.
1. Selecione *todas as edições da área de trabalho do Windows* em **escolher quais configurações exibir e configurar e**, em seguida, selecione **Avançar**.
1. Selecione **Concluir**.
1. Em seu projeto recém-criado, navegue até **configurações** > de tempo de execução**WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Defina **UseSecurityKeyForSignIn** como *habilitado*.
1. Selecione **Exportar** > **pacote de provisionamento**
1. Deixe os padrões na janela de **compilação** em **descrever o pacote de provisionamento**e selecione **Avançar**.
1. Deixe os padrões na janela de **compilação** em **selecionar detalhes de segurança para o pacote de provisionamento** e selecione **Avançar**.
1. Anote ou altere o caminho nas janelas de **Build** em **selecionar onde salvar o pacote de provisionamento** e selecione **Avançar**.
1. Selecione **Compilar** na página **criar o pacote de provisionamento** .
1. Salve os dois arquivos criados (*ppkg* e *Cat*) em um local em que você possa aplicá-los aos computadores mais tarde.
1. Para aplicar o pacote de provisionamento que você criou, consulte [aplicar um pacote de provisionamento](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package).

> [!NOTE]
> Os dispositivos que executam o Windows 10 versão 1809 também devem habilitar o modo de computador compartilhado (*EnableSharedPCMode*). Para obter mais informações sobre como habilitar essa funcionalidade, consulte [configurar um PC compartilhado ou convidado com o Windows 10](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc).

### <a name="enable-with-group-policy"></a>Habilitar com Política de Grupo

Para **dispositivos ingressados no Azure ad híbrido**, as organizações podem definir a configuração de política de grupo a seguir para habilitar a entrada de chave de segurança do Fido. A configuração pode ser encontrada em **configuração** > do computador**modelos administrativos** > **System** > **logon** > **do sistema Ativar entrada de chave de segurança**:

- Definir essa política como **habilitada** permite que os usuários entrem com chaves de segurança.
- Definir essa política como **desabilitada** ou **não configurada** impede que os usuários entrem com chaves de segurança.

Essa configuração de Política de Grupo requer uma versão atualizada do `credentialprovider.admx` modelo de política de grupo. Esse novo modelo está disponível com a próxima versão do Windows Server e com o Windows 10 20H1. Essa configuração pode ser gerenciada com um dispositivo que executa uma dessas versões mais recentes do Windows ou centralmente seguindo as orientações no tópico de suporte, [como criar e gerenciar o armazenamento central para Política de Grupo modelos administrativos no Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="sign-in-with-fido2-security-key"></a>Entrar com a chave de segurança do FIDO2

No exemplo a seguir, um usuário chamado bala Sandhu já provisionou sua chave de segurança do FIDO2 usando as etapas no artigo anterior, [Habilitar entrada de chave de segurança sem senha](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). Para dispositivos ingressados no Azure AD híbrido, certifique-se de que você também tenha [habilitado a entrada de chave de segurança sem senha para recursos locais](howto-authentication-passwordless-security-key-on-premises.md). Bala pode escolher o provedor de credenciais de chave de segurança na tela de bloqueio do Windows 10 e inserir a chave de segurança para entrar no Windows.

![Entrada de chave de segurança na tela de bloqueio do Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Gerenciar chave de segurança biométrica, PIN ou redefinição de chaves de segurança

* Windows 10 versão 1903 ou superior
   * Os usuários podem abrir **as configurações do Windows** em seu dispositivo >**chave de segurança** de **contas** > 
   * Os usuários podem alterar seu PIN, atualizar a biometria ou redefinir sua chave de segurança

## <a name="troubleshooting-and-feedback"></a>Solução de problemas e comentários

Se você quiser compartilhar comentários ou encontrar problemas ao visualizar esse recurso, compartilhe por meio do aplicativo Hub de comentários do Windows usando as seguintes etapas:

1. Inicie o **Hub de comentários** e verifique se você está conectado.
1. Envie comentários sob a seguinte categorização:
   - Categoria: segurança e privacidade
   - Subcategoria: FIDO
1. Para capturar logs, use a opção para **recriar meu problema**

## <a name="next-steps"></a>Próximas etapas

[Habilitar o acesso a recursos locais para o Azure AD e dispositivos ingressados no Azure AD híbrido](howto-authentication-passwordless-security-key-on-premises.md)

[Saiba mais sobre o registro de dispositivo](../devices/overview.md)

[Saiba mais sobre a Autenticação Multifator do Azure](../authentication/howto-mfa-getstarted.md)
