---
title: Solucionar problemas de criptografia de dados-banco de dado do Azure para MySQL
description: Saiba como solucionar problemas de criptografia de dados no Azure Database para MySQL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 42956d115590fd322d2851fd546c505a76a851fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79297033"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-mysql"></a>Solucionar problemas de criptografia de dados no Azure Database para MySQL

Este artigo descreve como identificar e resolver problemas comuns que podem ocorrer no banco de dados do Azure para MySQL quando configurados com a criptografia de dado usando uma chave gerenciada pelo cliente.

## <a name="introduction"></a>Introdução

Quando você configura a criptografia de dados para usar uma chave gerenciada pelo cliente no Azure Key Vault, os servidores exigem acesso contínuo à chave. Se o servidor perder o acesso à chave gerenciada pelo cliente no Azure Key Vault, ele negará todas as conexões, retornará a mensagem de erro apropriada e alterará seu estado para ***inacessível*** no portal do Azure.

Se você não precisar mais de um servidor de banco de dados do Azure para MySQL inacessível, poderá excluí-lo para parar de incorrer em custos. Nenhuma outra ação no servidor é permitida até que o acesso ao cofre de chaves tenha sido restaurado e o servidor esteja disponível. Também não é possível alterar a opção de criptografia de dados de `Yes`(gerenciada pelo cliente) `No` para (gerenciada pelo serviço) em um servidor inacessível quando ele é criptografado com uma chave gerenciada pelo cliente. Você precisará revalidar a chave manualmente para que o servidor possa ser acessado novamente. Essa ação é necessária para proteger os dados contra o acesso não autorizado enquanto as permissões para a chave gerenciada pelo cliente são revogadas.

## <a name="common-errors-that-cause-the-server-to-become-inaccessible"></a>Erros comuns que fazem com que o servidor se torne inacessível

As seguintes configurações incorretas causam a maioria dos problemas com a criptografia de dados que usam chaves de Azure Key Vault:

- O cofre de chaves está indisponível ou não existe:
  - O cofre de chaves foi excluído por engano.
  - Um erro de rede intermitente faz com que o cofre de chaves fique indisponível.

- Você não tem permissões para acessar o cofre de chaves ou a chave não existe:
  - A chave expirou ou foi excluída ou desabilitada acidentalmente.
  - A identidade gerenciada da instância do banco de dados do Azure para MySQL foi excluída acidentalmente.
  - A identidade gerenciada da instância do banco de dados do Azure para MySQL não tem permissões de chave suficientes. Por exemplo, as permissões não incluem obter, encapsular e desencapsular.
  - As permissões de identidade gerenciadas para a instância do banco de dados do Azure para MySQL foram revogadas ou excluídas.

## <a name="identify-and-resolve-common-errors"></a>Identificar e resolver erros comuns

### <a name="errors-on-the-key-vault"></a>Erros no cofre de chaves

#### <a name="disabled-key-vault"></a>Cofre de chaves desabilitado

- `AzureKeyVaultKeyDisabledMessage`
- **Explicação**: a operação não pôde ser concluída no servidor porque a chave de Azure Key Vault está desabilitada.

#### <a name="missing-key-vault-permissions"></a>Permissões do Key Vault ausentes

- `AzureKeyVaultMissingPermissionsMessage`
- **Explicação**: o servidor não tem as permissões obter, encapsular e desencapsular necessárias para Azure Key Vault. Conceda quaisquer permissões ausentes à entidade de serviço com ID.

### <a name="mitigation"></a>Atenuação

- Confirme se a chave gerenciada pelo cliente está presente no cofre de chaves.
- Identifique o cofre de chaves e vá até ele no portal do Azure.
- Verifique se o URI da chave identifica uma chave que está presente.

## <a name="next-steps"></a>Próximas etapas

[Use o portal do Azure para configurar a criptografia de dados com uma chave gerenciada pelo cliente no banco de dados do Azure para MySQL](howto-data-encryption-portal.md)
