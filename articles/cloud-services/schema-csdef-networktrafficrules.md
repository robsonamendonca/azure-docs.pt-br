---
title: Esquema de Def. NetworkTrafficRules do Azure Cloud Services | Microsoft Docs
description: Saiba mais sobre o NetworkTrafficRules, que limita as funções que podem acessar os pontos de extremidade internos de uma função. Ele combina com funções em um arquivo de definição de serviço.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 351b369f-365e-46c1-82ce-03fc3655cc88
caps.latest.revision: 17
author: tgore03
ms.author: tagore
ms.openlocfilehash: e53c10395ec3168e656633cc43fb2d01902209fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79534721"
---
# <a name="azure-cloud-services-definition-networktrafficrules-schema"></a>Esquema NetworkTrafficRules de definição dos Serviços de Nuvem do Azure
O nó `NetworkTrafficRules` é um elemento opcional no arquivo de definição de serviço que especifica como as funções comunicam-se entre si. Ele limita quais funções podem acessar os pontos de extremidade internos da função específica. O `NetworkTrafficRules` não é um elemento autônomo; ele é combinado com duas ou mais funções em um arquivo de definição de serviço.

A extensão padrão do arquivo de definição de serviço é .csdef.

> [!NOTE]
>  O nó `NetworkTrafficRules` só está disponível usando o SDK do Azure versão 1.3 ou superior.

## <a name="basic-service-definition-schema-for-the-network-traffic-rules"></a>Esquema de definição de serviço básico para as regras de tráfego de rede
O formato básico de um arquivo de definição de serviço que contém definições de tráfego de rede é o seguinte.

```xml
<ServiceDefinition …>
   <NetworkTrafficRules>
      <OnlyAllowTrafficTo>
         <Destinations>
            <RoleEndpoint endpointName="<name-of-the-endpoint>" roleName="<name-of-the-role-containing-the-endpoint>"/>
         </Destinations>
         <AllowAllTraffic/>
         <WhenSource matches="[AnyRule]">
            <FromRole roleName="<name-of-the-role-to-allow-traffic-from>"/>
         </WhenSource>
      </OnlyAllowTrafficTo>
   </NetworkTrafficRules>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementos de esquema
O nó `NetworkTrafficRules` do arquivo de definição de serviço inclui estes elementos, descritos em detalhes nas seções subsequentes neste tópico:

[Elemento NetworkTrafficRules](#NetworkTrafficRules)

[Elemento OnlyAllowTrafficTo](#OnlyAllowTrafficTo)

[Elemento destinos](#Destinations)

[Elemento RoleEndpoint](#RoleEndpoint)

Elemento AllowAllTraffic

[Elemento WhenSource](#WhenSource)

[Elemento FromRole](#FromRole)

##  <a name="networktrafficrules-element"></a><a name="NetworkTrafficRules"></a>Elemento NetworkTrafficRules
O elemento `NetworkTrafficRules` especifica quais funções podem se comunicar com qual ponto de extremidade em outra função. Um serviço pode conter uma definição `NetworkTrafficRules`.

##  <a name="onlyallowtrafficto-element"></a><a name="OnlyAllowTrafficTo"></a> Elemento OnlyAllowTrafficTo
O elemento `OnlyAllowTrafficTo` descreve uma coleção de pontos de extremidade de destino e as funções que podem se comunicar com eles. É possível especificar vários nós `OnlyAllowTrafficTo`.

##  <a name="destinations-element"></a><a name="Destinations"></a> Elemento Destinations
O elemento `Destinations` descreve uma coleção de RoleEndpoints com a qual é possível se comunicar.

##  <a name="roleendpoint-element"></a><a name="RoleEndpoint"></a>Elemento RoleEndpoint
O elemento `RoleEndpoint` descreve um ponto de extremidade em uma função com a qual ele permite comunicações. Será possível especificar vários elementos `RoleEndpoint` se houver mais de um ponto de extremidade na função.

| Atributo      | Type     | Descrição |
| -------------- | -------- | ----------- |
| `endpointName` | `string` | Obrigatórios. O nome do ponto de extremidade com o qual permitir o tráfego.|
| `roleName`     | `string` | Obrigatórios. O nome da função web com a qual permitir a comunicação.|

## <a name="allowalltraffic-element"></a>Elemento AllowAllTraffic
O elemento `AllowAllTraffic` é uma regra que permite que todas as funções se comuniquem com os pontos de extremidade definidos no nó `Destinations`.

##  <a name="whensource-element"></a><a name="WhenSource"></a>Elemento whenname
O elemento `WhenSource` descreve uma coleção de funções que podem se comunicar com os pontos de extremidade definidos no nó `Destinations`.

| Atributo | Type     | Descrição |
| --------- | -------- | ----------- |
| `matches` | `string` | Obrigatórios. Especifica a regra a ser aplicada ao permitir comunicações. No momento, o único valor válido é `AnyRule`.|
  
##  <a name="fromrole-element"></a><a name="FromRole"></a>Elemento FromRole
O elemento `FromRole` especifica as funções que podem se comunicar com os pontos de extremidade definidos no nó `Destinations`. Será possível especificar vários elementos `FromRole` se houver mais de uma função que pode se comunicar com os pontos de extremidade.

| Atributo  | Type     | Descrição |
| ---------- | -------- | ----------- |
| `roleName` | `string` | Obrigatórios. O nome da função da qual permitir a comunicação.|

## <a name="see-also"></a>Consulte Também
[Esquema de definição do Serviço de Nuvem (clássico)](schema-csdef-file.md)




