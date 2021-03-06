---
title: Configurar um contêiner personalizado do Linux
description: Saiba como configurar um contêiner personalizado do Linux no serviço Azure App. Este artigo mostra as tarefas de configuração mais comuns.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79280138"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Configurar um contêiner personalizado do Linux para o serviço Azure App

Este artigo mostra como configurar um contêiner personalizado do Linux para ser executado no serviço Azure App.

Este guia fornece os principais conceitos e instruções para a Containerização de aplicativos do Linux no serviço de aplicativo. Se você nunca usou Azure App serviço, siga primeiro o [início rápido](quickstart-docker-go.md) e o [tutorial](tutorial-custom-docker-image.md) do contêiner personalizado. Há também um início rápido e [tutorial](tutorial-multi-container-app.md)de [aplicativo de vários contêineres](quickstart-multi-container.md) .

## <a name="configure-port-number"></a>Configurar número da porta

O servidor Web em sua imagem personalizada pode usar uma porta diferente de 80. Você informa ao Azure sobre a porta que seu contêiner personalizado usa usando a `WEBSITES_PORT` configuração do aplicativo. A página do GitHub que contém a [amostra do Python neste tutorial](https://github.com/Azure-Samples/docker-django-webapp-linux) mostra que você precisa definir `WEBSITES_PORT` para _8000_. Você pode defini-lo executando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) o comando na Cloud Shell. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Configurar variáveis de ambiente

Seu contêiner personalizado pode usar variáveis de ambiente que precisam ser fornecidas externamente. Você pode passá-los no executando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) o comando no Cloud Shell. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Esse método funciona tanto para aplicativos de contêiner único quanto para aplicativos de vários contêineres, onde as variáveis de ambiente são especificadas no arquivo *Docker-Compose. yml* .

## <a name="use-persistent-shared-storage"></a>Usar armazenamento compartilhado persistente

Você pode usar o diretório */Home* no sistema de arquivos do seu aplicativo para manter os arquivos entre as reinicializações e compartilhá-los entre instâncias. O `/home` em seu aplicativo é fornecido para permitir que seu aplicativo de contêiner acesse o armazenamento persistente.

Quando o armazenamento persistente é desabilitado, as gravações `/home` no diretório não são mantidas entre as reinicializações do aplicativo ou entre várias instâncias. A única exceção é o `/home/LogFiles` diretório, que é usado para armazenar os logs do Docker e do contêiner. Quando o armazenamento persistente está habilitado, todas as gravações `/home` no diretório são persistidas e podem ser acessadas por todas as instâncias de um aplicativo expandido.

Por padrão, o armazenamento persistente é *habilitado* e a configuração não é exposta nas configurações do aplicativo. Para desabilitá-lo, `WEBSITES_ENABLE_APP_SERVICE_STORAGE` defina a configuração do [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) aplicativo executando o comando no Cloud Shell. Por exemplo:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> Você também pode [configurar seu próprio armazenamento persistente](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Habilitar SSH

O SSH permite a comunicação segura entre um contêiner e um cliente. Para que um contêiner personalizado dê suporte ao SSH, você deve adicioná-lo no próprio Dockerfile.

> [!TIP]
> Todos os contêineres internos do Linux adicionaram as instruções de SSH em seus repositórios de imagem. Você pode percorrer as instruções a seguir com o [repositório node. js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14) para ver como ele está habilitado lá.

- Use a instrução [Run](https://docs.docker.com/engine/reference/builder/#run) para instalar o servidor SSH e definir a senha da conta raiz como `"Docker!"`. Por exemplo, para uma imagem baseada em [Alpine Linux](https://hub.docker.com/_/alpine), você precisa dos seguintes comandos:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Essa configuração não permite conexões externas com o contêiner. O SSH está disponível apenas `https://<app-name>.scm.azurewebsites.net` por meio de e autenticado com as credenciais de publicação.

- Adicione [este arquivo de sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) ao seu repositório de imagens e use a instrução de [cópia](https://docs.docker.com/engine/reference/builder/#copy) para copiar o arquivo para o diretório */etc/ssh/* Para obter mais informações sobre *sshd_config* arquivos, consulte a [documentação do OpenBSD](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > O arquivo *sshd_config* deve incluir os seguintes itens:
    > - `Ciphers` deve incluir pelo menos um item nesta lista: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` deve incluir pelo menos um item nesta lista: `hmac-sha1,hmac-sha1-96`.

- Use a instrução de [exposição](https://docs.docker.com/engine/reference/builder/#expose) para abrir a porta 2222 no contêiner. Embora a senha raiz seja conhecida, a porta 2222 não pode ser acessada pela Internet. Ele é acessível somente por contêineres dentro da rede de ponte de uma rede virtual privada.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- No script de inicialização do seu contêiner, inicie o servidor SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Para obter um exemplo, consulte como o [contêiner padrão node. js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) inicia o servidor SSH.

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Configurar aplicativos de vários contêineres

- [Usar armazenamento persistente no Docker Compose](#use-persistent-storage-in-docker-compose)
- [Limitações de visualização](#preview-limitations)
- [Opções de Docker Compose](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Usar armazenamento persistente no Docker Compose

Aplicativos de vários contêineres, como o WordPress, precisam de armazenamento persistente para funcionar corretamente. Para habilitá-lo, sua configuração de Docker Compose deve apontar para um local de armazenamento *fora* do contêiner. Os locais de armazenamento dentro de seu contêiner não mantêm as alterações além da reinicialização do aplicativo.

Habilite o armazenamento persistente definindo `WEBSITES_ENABLE_APP_SERVICE_STORAGE` a configuração do aplicativo, usando o comando [AZ webapp config appSettings Set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

No arquivo *Docker-Compose. yml* , mapeie a `volumes` opção para `${WEBAPP_STORAGE_HOME}`. 

`WEBAPP_STORAGE_HOME` é uma variável de ambiente no Serviço de Aplicativo mapeada para armazenamento persistente para o aplicativo. Por exemplo:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Limitações de visualização

No momento, vários contêineres estão em versão prévia. Os seguintes recursos da plataforma do serviço de aplicativo não têm suporte:

- Autenticação/Autorização
- Identidades gerenciadas

### <a name="docker-compose-options"></a>Opções de Docker Compose

As listas a seguir mostram opções de configuração com e sem suporte Docker Compose:

#### <a name="supported-options"></a>Opções com suporte

- command
- entrypoint
- environment
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Opções sem suporte

- build (não permitido)
- depends_on (ignorado)
- networks (ignorada)
- secrets (ignorados)
- portas diferentes de 80 e 8080 (ignoradas)

> [!NOTE]
> Quaisquer outras opções não explicitamente chamadas são ignoradas na visualização pública.

## <a name="configure-vnet-integration"></a>Configurar integração VNet

Usar um contêiner personalizado com integração VNet pode exigir configuração de contêiner adicional. Consulte [Integrar seu aplicativo a uma Rede Virtual do Azure](../web-sites-integrate-with-vnet.md).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: implantar do repositório de contêiner privado](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Tutorial: aplicativo WordPress com vários contêineres](tutorial-multi-container-app.md)
