---
title: Script da CLI – Baixar logs de consulta lenta – Banco de Dados do Azure para MariaDB
description: Esse exemplo de script de CLI do Azure mostra como habilitar e baixar os logs de consulta lenta de um servidor do Banco de Dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: a6d4ed70418f39b24a68362d5006f3f374693cb6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "74777341"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Habilitar e fazer o download dos logs de consulta lenta de um servidor do Banco de Dados do Azure para MariaDB usando a CLI do Azure
Esse exemplo de script de CLI mostra como habilitar e fazer o download dos logs de consulta lenta de um único servidor do Banco de Dados do Azure para MariaDB.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se você optar por executar a CLI localmente, este artigo exigirá a CLI do Azure versão 2.0 ou posterior. Verifique a versão executando `az --version`. Confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli) para instalar ou atualizar sua versão da CLI do Azure. 

## <a name="sample-script"></a>Exemplo de script
Neste script de exemplo, edite as linhas destacadas para atualizar o nome de usuário administrador e a senha com os seus próprios. Substitua &lt;log_file_name&gt; nos comandos `az monitor` pelo seu próprio nome de arquivo de log do servidor.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele após executar o exemplo de script. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação sobre o script
Esse script usa os comandos descritos na tabela abaixo:

| **Comando** | **Observações** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Cria um servidor MariaDB que hospeda os bancos de dados. |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Liste os valores de configuração para um servidor. |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Atualize a configuração de um servidor. |
| [az mariadb server-logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) | Lista arquivos de log para um servidor. |
| [az mariadb server-logs download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) | Faça o download de arquivos de log. |
| [az group delete](/cli/azure/group#az-group-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
- Para ler mais sobre a CLI do Azure: [documentação da CLI do Azure](/cli/azure).
- Experimente scripts adicionais: [exemplos da CLI do Azure para o Banco de Dados do Azure para MariaDB](../sample-scripts-azure-cli.md)
