---
title: incluir arquivo
description: incluir arquivo
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 06/08/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: e683d17422321b780a1c01b3011292f2e2c631cb
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2020
ms.locfileid: "67171969"
---
Para excluir um contêiner de volume, você deve
 - excluir os volumes no contêiner de volume. Se o contêiner de volume tiver volumes associados, coloque esses volumes offline primeiro. Siga as etapas em [Colocar um volume offline](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Depois que os volumes estiverem offline, você poderá excluí-los. 
 - excluir as políticas de backup associadas e instantâneos na nuvem. Verifique se o contêiner de volume tem políticas de backup e instantâneos em nuvem associados. Nesse caso, em seguida, [exclua as políticas de backup](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Isso também excluirá os instantâneos de nuvem. 
 
Quando o contêiner de volume não possui nenhum volume associado, políticas de backup e instantâneos em nuvem, você poderá excluí-lo. Execute o procedimento a seguir para excluir um contêiner de volume.

#### <a name="to-delete-a-volume-container"></a>Excluir um contêiner de volume
1. Acesse o serviço Gerenciador de Dispositivo StorSimple e clique em **Dispositivos**. Selecione e clique no dispositivo e vá para **Configurações > Gerenciar > Contêineres de Volume**.

    ![Folha de contêineres de volume](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. Na lista tabular de contêineres de volume, selecione o contêiner de volume que você deseja excluir, clique com o botão direito do mouse em **...** e selecione **Excluir**.

    ![Excluir contêiner de volume](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Se um contêiner de volume não possuir nenhum volume associado, políticas de backup e instantâneos em nuvem, você poderá excluí-lo. Quando a confirmação for solicitada, examine e marque a caixa de seleção indicando o impacto da exclusão do contêiner de volume. Clique em **Excluir** para excluir o contêiner de volume.

    ![Confirmar exclusão](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

A lista de contêineres de volume é atualizada para refletir o contêiner de volume excluído.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


