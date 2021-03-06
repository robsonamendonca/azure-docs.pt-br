---
title: Compartilhar link para solicitar um pacote de acesso no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Saiba como compartilhar o link para solicitar um pacote de acesso em Azure Active Directory gerenciamento de direitos.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea90032b1f0cfe598ffdb3d35448a996f3111036
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78968755"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Compartilhar link para solicitar um pacote de acesso no gerenciamento de direitos do Azure AD

A maioria dos usuários em seu diretório pode entrar no portal meu acesso e ver automaticamente uma lista de pacotes de acesso que eles podem solicitar. No entanto, para usuários externos de parceiros comerciais que ainda não estão em seu diretório, você precisará enviar um link que eles possam usar para solicitar um pacote de acesso. 

Desde que o catálogo do pacote de acesso esteja [habilitado para usuários externos](entitlement-management-catalog-create.md) e você tenha uma [política para o diretório do usuário externo](entitlement-management-access-package-request-policy.md), o usuário externo poderá usar o link meu portal de acesso para solicitar o pacote de acesso.

## <a name="share-link-to-request-an-access-package"></a>Compartilhar link para solicitar um pacote de acesso

**Função de pré-requisito:** Administrador global, Administrador de usuário, Proprietário do catálogo ou Gerenciador de pacote de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Na página Visão geral, copie o **link meu portal de acesso**.

    ![Visão geral do pacote de acesso – Link do portal Meus Acessos](./media/entitlement-management-shared/my-access-portal-link.png)

    É importante que você copie todo o link meu portal de acesso ao enviá-lo para um parceiro comercial interno. Isso garante que o parceiro terá acesso ao portal do seu diretório para fazer sua solicitação. O link começa com `myaccess`, inclui uma dica de diretório e termina com uma ID de pacote de acesso.  (Para o governo dos EUA, o domínio no link meu portal de acesso `myaccess.microsoft.us`será.)

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Envie um email para o parceiro comercial externo. Eles podem compartilhar o link com seus usuários para solicitar o pacote de acesso.

## <a name="next-steps"></a>Próximas etapas

- [Solicitar acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Aprovar ou negar solicitações de acesso](entitlement-management-request-approve.md)