---
title: Problemas para entrar em um aplicativo de desenvolvimento personalizado | Microsoft Docs
description: Erros comuns que podem estar fazendo com que você não consiga entrar em um aplicativo que você desenvolveu com o Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01a424129abf88c18500c96bd1889fc6dcce2ec6
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890697"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Problemas ao entrar em um aplicativo personalizado

Há vários erros que podem estar causando a impossibilidade de entrar em um aplicativo. O principal motivo das pessoas se depararem com esse problema está relacionado com aplicativos configurados incorretamente.

## <a name="errors-related-to--misconfigured-apps"></a>Erros relacionados com aplicativos configurados incorretamente

* Verifique se as configurações do portal correspondem às que você tem no seu aplicativo. Especificamente, compare o ID do Aplicativo/Cliente, URLs de Resposta, Chaves/Segredos do Cliente e URI da ID do Aplicativo.

* Compare o recurso que você está solicitando acesso no código com as permissões configuradas na guia **Recursos Necessários** para certificar-se de que está solicitando somente os recursos que você configurou.

* Consulte [StackOverflow do Azure AD ](https://stackoverflow.com/questions/tagged/azure-active-directory) para quaisquer erros ou problemas semelhantes.

## <a name="next-steps"></a>Próximas etapas

[Guia do Desenvolvedor do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Consentir e Integrar Aplicativos ao Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Permissões e consentimento no ponto de extremidade da plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[StackOverflow do Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory>)
