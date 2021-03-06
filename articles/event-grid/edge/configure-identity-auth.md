---
title: Configurar identidade-IoT Edge de grade de eventos do Azure | Microsoft Docs
description: Configurar a identidade do módulo de grade de eventos
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76841758"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Configurar identidade para o módulo de grade de eventos

Este artigo mostra como configurar a identidade para grade no Edge. Por padrão, o módulo de grade de eventos apresenta seu certificado de identidade conforme configurado pelo daemon de segurança de IoT. A grade de eventos na borda apresenta seu certificado de identidade com suas chamadas de saída ao entregar eventos. Um assinante pode validar se é o módulo de grade de eventos que enviou o evento antes de aceitar.

Consulte o guia de [segurança e autenticação](security-authentication.md) para todas as configurações possíveis.

## <a name="always-present-identity-certificate"></a>Sempre apresentar certificado de identidade
Aqui está um exemplo de configuração para sempre apresentar um certificado de identidade em chamadas de saída. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Não apresentar certificado de identidade
Aqui está um exemplo de configuração para não apresentar um certificado de identidade em chamadas de saída. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
