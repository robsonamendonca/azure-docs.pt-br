---
title: Como garantir a validação do banco de dados do Azure para PostgreSQL – criptografia de dados
description: Saiba como validar a criptografia do banco de dados do Azure para PostgreSQL – Data Encryption usando a chave gerenciada dos clientes.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 4672a92ceba5dc52c717f76a705d0fa508ab41fd
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515463"
---
# <a name="validating-data-encryption-for-azure-database-for-postgresql"></a>Validando a criptografia de dados para o Azure Database para PostgreSQL

Este artigo ajuda você a validar que a criptografia de dados usando a chave gerenciada pelo cliente para o Azure Database para PostgreSQL está funcionando conforme o esperado.

## <a name="check-the-encryption-status"></a>Verificar o status de criptografia

### <a name="from-portal"></a>Do portal

1. Se você quiser verificar se a chave do cliente é usada para criptografia, siga estas etapas:

    * Na portal do Azure, navegue até as**chaves** de **Azure Key Vault** -> 
    * Selecione a chave usada para criptografia do servidor.
    * Defina o status da chave **habilitada** como **não**.
  
       Após algum tempo (**~ 15 min**), o **status** do servidor do banco de dados do Azure para PostgreSQL deve estar **inacessível**. Qualquer operação de e/s feita no servidor falhará, o que validará que o servidor está realmente criptografado com a chave de clientes e que a chave não é válida no momento.
    
        Para **disponibilizar o servidor para** o, você pode revalidar a chave. 
    
    * Defina o status da chave no Key Vault como **Sim**.
    * Na criptografia de **dados**do servidor, selecione **chave de revalidação**.
    * Depois que a revalidação da chave for bem-sucedida, o **status** do servidor será alterado para **disponível**

2. Na portal do Azure, se você puder garantir que a chave de criptografia esteja definida, os dados serão criptografados usando a chave de clientes usada no portal do Azure.

  ![Visão geral da política de acesso](media/concepts-data-access-and-security-data-encryption/byok-validate.png)

### <a name="from-cli"></a>Da CLI

1. Podemos usar o comando *AZ CLI* para validar os principais recursos que estão sendo usados para o servidor do banco de dados do Azure para PostgreSQL.

    ```azurecli-interactive
   az postgres server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    Para um servidor sem criptografia de dados definida, esse comando resultará em um conjunto vazio [].

### <a name="azure-audit-reports"></a>Relatórios de auditoria do Azure

Os [relatórios de auditoria](https://servicetrust.microsoft.com) também podem ser analisados, fornecendo informações sobre a conformidade com os padrões de proteção de dados e requisitos regulatórios.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a criptografia de dados, consulte [banco de dados do Azure para PostgreSQL criptografia de dado de servidor único com chave gerenciada pelo cliente](concepts-data-encryption-postgresql.md).