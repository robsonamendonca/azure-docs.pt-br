---
title: Conecte seu aplicativo do Azure IoT Central com os Serviços de Campo do Dynamics 365 | Microsoft Docs
description: Saiba como criar uma solução de ponta a ponta com o Azure IoT Central e o Serviço de Campo do Dynamics 365
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7c708268eaeade1cfb625c73ff8758a1941ff5b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80157442"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Criar uma solução de ponta a ponta com o Azure IoT Central e o Serviço de Campo do Dynamics 365 



Como um construtor, você pode habilitar a integração do seu aplicativo Azure IoT Central a outros sistemas de negócios. 


Por exemplo, em uma solução de gerenciamento de resíduos conectada, você pode otimizar a expedição de caminhões de coleta de lixo. A otimização pode ser feita com base nos dados de sensores IoT de compartimentos de lixo conectados. Em seu [IOT central aplicativo de gerenciamento de resíduos conectado](./tutorial-connected-waste-management.md) , você pode configurar regras e ações e defini-lo para disparar a criação de alertas no serviço de campo do Dynamics. Esse cenário é realizado com o Microsoft Flow, que será configurado diretamente no IoT Central para automatizar fluxos de trabalho entre aplicativos e serviços. Além disso, com base nas atividades de serviço no Serviço de Campo, as informações podem ser enviadas de volta ao Azure IoT Central. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Como conectar seu aplicativo do Azure IoT Central com os Serviços de Campo do Dynamics 365 

Os processos de integração abaixo podem ser implementados com facilidade com base em uma experiência de configuração pura:
* O Azure IoT Central pode enviar informações sobre anomalias de dispositivo para o serviço de campo conectado (como um alerta de IoT) para diagnóstico.
* O serviço de campo conectado pode criar casos ou ordens de serviço disparadas de anomalias de dispositivo.
* O serviço de campo conectado pode agendar técnicos para inspeção para evitar incidentes que causem tempo de inatividade.
* O painel do dispositivo do Azure IoT Central pode ser atualizado com informações relevantes sobre serviço e agendamento.


## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre os [modelos de governo do IoT Central](./overview-iot-central-government.md)
* Saiba mais sobre o [IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
* Saiba mais sobre os [Serviços de Campo do Dynamics 365](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)
