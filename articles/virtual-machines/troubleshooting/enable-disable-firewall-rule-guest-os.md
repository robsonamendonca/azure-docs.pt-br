---
title: Habilitar ou desabilitar uma regra de firewall em um sistema operacional convidado na VM do Azure|Microsoft Docs
description: Saiba como usar as ferramentas remotas online ou offline ou configurações do registro para habilitar ou desabilitar regras de firewall do sistema operacional convidado em uma VM remota do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: e93dbd085ce99b8d555d6b9bb04e7eb6f60de0ee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422886"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Habilitar ou desabilitar uma regra de firewall em um sistema operacional convidado da VM do Azure

Este artigo fornece uma referência para solucionar uma situação na qual você suspeita que o firewall do sistema operacional convidado está filtrando o tráfego parcial em uma máquina virtual (VM). Isso pode ser útil pelos seguintes motivos:

*   Se uma alteração foi feita deliberadamente no firewall que causou falha nas conexões RDP, o uso do recurso Custom Script Extension poderá resolver o problema.

*   A desativação de todos os perfis de firewall é uma maneira mais segura de solucionar problemas do que a configuração da regra de firewall específica do RDP.

## <a name="solution"></a>Solução

A maneira como você configura as regras do firewall depende do nível de acesso à VM necessária. Os exemplos a seguir usam regras de RDP. No entanto, os mesmos métodos podem ser aplicados a qualquer outro tipo de tráfego, apontando para a chave de registro correta.

### <a name="online-troubleshooting"></a>Resolução de problemas online 

#### <a name="mitigation-1-custom-script-extension"></a>Mitigação 1: extensão de script personalizada

1.  Crie seu script usando o modelo a seguir.

    *   Para habilitar uma regra:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Para desabilitar uma regra:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Carregue esse script no portal do Azure usando o recurso [extensão de Script personalizado](../extensions/custom-script-windows.md). 

#### <a name="mitigation-2-remote-powershell"></a>Mitigação 2: PowerShell Remoto

Se a VM estiver on-line e puder ser acessada em outra VM na mesma rede virtual, você poderá fazer as mitigações a seguir usando a outra VM.

1.  Na VM de solução de problemas, abra uma janela do console do PowerShell.

2.  Execute os seguintes comandos, conforme apropriado.

    *   Para habilitar uma regra:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   Para desabilitar uma regra:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>Mitigação 3: comandos do PSTools

Se a VM estiver on-line e puder ser acessada em outra VM na mesma rede virtual, você poderá fazer as mitigações a seguir usando a outra VM.

1.  Na VM de solução de problemas, baixe [ PSTools ](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Abra uma instância do CMD e acesse a VM por meio de seu IP interno (DIP). 

    * Para habilitar uma regra:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Para desabilitar uma regra:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>Mitigation 4: Registro remoto

Se a VM estiver on-line e puder ser acessada em outra VM na mesma rede virtual, você poderá usar [Registro Remoto](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) na outra VM.

1.  Na VM de solução de problemas, inicie o editor do registro (regedit. exe) e selecione **arquivo** > **conectar registro de rede**.

2.  Abra a ramificação \System do *computador de destino*e especifique os seguintes valores:

    * Para habilitar uma regra, abra o seguinte valor do Registro:
    
        *TARGET MACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parâmetros\FirewallPolicy\FirewallRules\Remote Desktop-User Mode-In-TCP
    
        Em seguida, altere **Ativo = FALSO** para **Ativo = VERDADEIRO** na sequência:

        **v 2.22 | Ação = permitir | Ativo = verdadeiro | Dir = in | Protocolo = 6 | Perfil = domínio | Perfil = privado | Perfil = público | LPort = 3389 | Aplicativo =%SystemRoot%\system32\svchost.exe | SVC = TermService | Nome =\@FirewallAPI. dll,-28775 | DESC =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**
    
    * Para desabilitar uma regra, abra o seguinte valor do Registro:
    
        *TARGET MACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parâmetros\FirewallPolicy\FirewallRules\Remote Desktop-User Mode-In-TCP

        Em seguida, altere **Ativo = VERDADEIRO** para **Ativo = FALSO**:
        
        **v 2.22 | Ação = permitir | Ativo = falso | Dir = in | Protocolo = 6 | Perfil = domínio | Perfil = privado | Perfil = público | LPort = 3389 | Aplicativo =%SystemRoot%\system32\svchost.exe | SVC = TermService | Nome =\@FirewallAPI. dll,-28775 | DESC =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

3.  Reinicie a VM para aplicar as alterações.

### <a name="offline-troubleshooting"></a>Solução de problemas offline 

Se você não puder acessar a VM por qualquer método, o uso da Extensão de script personalizada falhará e você terá que trabalhar no modo OFFLINE trabalhando diretamente no disco do sistema.

Antes de seguir estas etapas, tire um instantâneo do disco do sistema da VM afetada como um backup. Para obter mais informações, consulte [Instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

1.  [Anexar o disco do sistema para uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).

2.  Inicie uma conexão de área de trabalho remota para a VM de recuperação.

3.  Verifique se o disco está sinalizado como **Online** no console de Gerenciamento de Disco. Observe que a letra da unidade atribuída ao disco do sistema anexado.

4.  Antes de fazer qualquer alteração, crie uma cópia da pasta\windows\system32\config no caso de ser necessária uma reversão das alterações.

5.  Na solução de problemas da VM, inicie o Editor do Registro (regedit.exe).

6.  Realce a chave de **HKEY_LOCAL_MACHINE** e, em seguida, selecione **arquivo** > **Carregar Hive** no menu.

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Localize e abra o arquivo\windows\system32\config\SYSTEM. 

    > [!Note]
    > Você será solicitado para um nome. Insira **BROKENSYSTEM**e, em seguida, expanda **HKEY_LOCAL_MACHINE**. Agora, você verá uma chave adicional chamada **BROKENSYSTEM**. Para essa solução de problemas, estamos montando essas seções de problema como **BROKENSYSTEM**.

8.  Faça as seguintes alterações no branch BROKENSYSTEM:

    1.  Verifique qual **ControlSet** chave de registro da qual a VM está iniciando. Você verá seu número de chave em HKLM\BROKENSYSTEM\Select\Current.

    2.  Para habilitar uma regra, abra o seguinte valor do Registro:
    
        SISTEMA HKLM\BROKEN\ControlSet00X\Services\SharedAccess\Parâmetros\FirewallPolicy\FirewallRules\Modo de usuário de área de trabalho remota no TCP
        
        Em seguida, altere **Active Directory = FALSO** à **Active = Verdadeiro**.
        
        **v 2.22 | Ação = permitir | Ativo = verdadeiro | Dir = in | Protocolo = 6 | Perfil = domínio | Perfil = privado | Perfil = público | LPort = 3389 | Aplicativo =%SystemRoot%\system32\svchost.exe | SVC = TermService | Nome =\@FirewallAPI. dll,-28775 | DESC =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

    3.  Para desabilitar uma regra, abra a seguinte chave do registro:

        SISTEMA HKLM\BROKEN\ControlSet00X\Services\SharedAccess\Parâmetros\FirewallPolicy\FirewallRules\Modo de usuário de área de trabalho remota no TCP

        Em seguida, altere **Active Directory = True** à **Active Directory = FALSE**.
        
        **v 2.22 | Ação = permitir | Ativo = falso | Dir = in | Protocolo = 6 | Perfil = domínio | Perfil = privado | Perfil = público | LPort = 3389 | Aplicativo =%SystemRoot%\system32\svchost.exe | SVC = TermService | Nome =\@FirewallAPI. dll,-28775 | DESC =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

9.  Realce **BROKENSYSTEM**e, em seguida, selecione **arquivo** > **Descarregar Hive** no menu.

10. [Desanexe o disco do sistema e recrie a VM](troubleshoot-recovery-disks-portal-windows.md).

11. Verifique se o problema foi resolvido.
