---
title: Como configurar o serviço RHEL/CentOS 7-Speech
titleSuffix: Azure Cognitive Services
description: Saiba como configurar o RHEL/CentOS 7 para que o SDK de fala possa ser usado.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: dc09d517d95b5a3f2a88504a14f1451d1de5ffc9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80639158"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>Configurar o RHEL/CentOS 7 para o SDK de fala

Red Hat Enterprise Linux (RHEL) 8 x64 e CentOS 8 x64 são oficialmente suportados pelo Speech SDK versão 1.10.0 e posterior. Também é possível usar o SDK de fala no RHEL/CentOS 7 x64, mas isso requer a atualização do compilador C++ (para desenvolvimento em C++) e da biblioteca de tempo de execução C++ compartilhada em seu sistema.

Para verificar a versão do compilador C++, execute:

```bash
g++ --version
```

Se o compilador estiver instalado, a saída deverá ter a seguinte aparência:

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

Essa mensagem permite que você saiba que a versão 4 do GCC principal está instalada. Esta versão não tem suporte completo para o padrão C++ 11, que o SDK de fala usa. Tentar compilar um programa em C++ com essa versão GCC e os cabeçalhos do SDK de fala resultarão em erros de compilação.

Também é importante verificar a versão da biblioteca de tempo de execução C++ compartilhada (libstdc + +). A maior parte do SDK de fala é implementada como bibliotecas C++ nativas, o que significa que ela depende de libstdc + +, independentemente da linguagem usada para desenvolver aplicativos.

Para encontrar o local de libstdc + + no seu sistema, execute:

```bash
ldconfig -p | grep libstdc++
```

A saída no RHEL/CentOS 7 (x64) é:

```
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

Com base nessa mensagem, você desejará verificar as definições de versão com este comando:

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

A saída deverá ser como a seguinte:

```
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

O SDK de fala requer **CXXABI_1.3.9** e **GLIBCXX_3.4.21**. Você pode encontrar essas informações executando `ldd libMicrosoft.CognitiveServices.Speech.core.so` nas bibliotecas do SDK de fala do pacote do Linux.

> [!NOTE]
> É recomendável que a versão do GCC instalado no sistema seja pelo menos **5.4.0**, com bibliotecas de tempo de execução correspondentes.

## <a name="example"></a>Exemplo

Este é um comando de exemplo que ilustra como configurar o RHEL/CentOS 7 x64 para desenvolvimento (C++, C#, Java, Python) com o SDK de fala 1.10.0 ou posterior:

```bash
# Only run ONE of the following two commands
# - for CentOS 7:
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
# - for RHEL 7:
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl python3
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free

# Build GCC 5.4.0 and runtimes and install them under /usr/local
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip

# Set SSL cert file location
# (this is required for any development/testing with Speech SDK)
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt

# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-1.10.0/lib/x64:$LD_LIBRARY_PATH

# For Python: install the Speech SDK module
python3 -m pip install azure-cognitiveservices-speech --user
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Sobre o SDK de Fala](speech-sdk.md)
