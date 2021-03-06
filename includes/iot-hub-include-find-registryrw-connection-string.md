---
title: incluir arquivo
description: incluir arquivo
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "68883839"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Para obter a cadeia de conexão do Hub IoT para a política **registryReadWrite** , siga estas etapas:

1. Na [portal do Azure](https://portal.azure.com), selecione **grupos de recursos**. Selecione o grupo de recursos onde o Hub está localizado e, em seguida, selecione o Hub na lista de recursos.

2. No painel do lado esquerdo do Hub, selecione políticas de **acesso compartilhado**.

3. Na lista de políticas, selecione a política **registryReadWrite** .

4. Em **chaves de acesso compartilhado**, selecione o ícone de cópia para a **cadeia de conexão--chave primária** e salve o valor.

    ![Mostrar como recuperar a cadeia de conexão](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Para obter mais informações sobre políticas de acesso compartilhado do Hub IoT e permissões, consulte [controle de acesso e permissões](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
