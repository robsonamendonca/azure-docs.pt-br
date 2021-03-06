---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: e47c8bc4dc814f1d4c5cb115a2da911544dd55f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399866"
---
:::row:::
    :::column span="3":::
        O SDK de fala só dá suporte ao **Ubuntu 16.04/18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 7/8**e **CentOS 7/8** nas seguintes arquiteturas de destino quando usado com o Linux:
        - x64
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Ao direcionar para o Linux ARM64 e usar C#-o pacote do .NET Core 3. x (dotNet-SDK-3. x) é necessário. Se você estiver visando ARM32 ou ARM64, não há suporte para Python.

> [!NOTE]
> As arquiteturas x86 do Ubuntu 16, 4, Ubuntu 18, 4 e Debian 9 dão suporte apenas ao desenvolvimento em C++ com o SDK de fala.

### <a name="system-requirements"></a>Requisitos do sistema

Para um aplicativo nativo, o SDK de fala depende do `libMicrosoft.CognitiveServices.Speech.core.so`. Verifique se a arquitetura de destino (x86, x64) corresponde ao aplicativo. Dependendo da versão do Linux, podem ser necessárias dependências adicionais.

- As bibliotecas compartilhadas da biblioteca GNU C (incluindo a biblioteca de programação de Threads POSIX, `libpthreads`)
- A biblioteca OpenSSL (`libssl.so.1.0.0` ou `libssl.so.1.0.2`)
- A biblioteca compartilhada para aplicativos ALSA (`libasound.so.2`)

# <a name="ubuntu-16041804"></a>[Ubuntu 16.04/18.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 e CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> Siga as instruções sobre [como configurar o RHEL/CentOS 7 para o SDK de fala](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).

> [!TIP]
> No RHEL/CentOS 8, siga as instruções em [como configurar o OpenSSL para Linux](../how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
