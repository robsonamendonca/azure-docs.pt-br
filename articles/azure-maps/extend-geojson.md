---
title: Geometrias geojson estendidas | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá como o Microsoft Azure Maps estende a especificação geojson para representar determinadas geometrias.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 98db10f0fc7a417f39d4bb00e77af6bdea034a03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79276394"
---
# <a name="extended-geojson-geometries"></a>Geometrias geojson estendidas

O mapas do Azure fornece uma lista de APIs poderosas para pesquisar dentro e além dos recursos geográficos. Essas APIs aderem à [especificação geojson][1] padrão de representar recursos geográficos.  

A [especificação geojson][1] dá suporte apenas às seguintes geometrias:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

Algumas APIs do Azure Maps aceitam geometrias que não fazem parte da [especificação geojson][1]. Por exemplo, a [pesquisa dentro](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) da API de geometria aceita círculo e polígonos.

Este artigo fornece uma explicação detalhada sobre como o Azure Mapas estende a [especificação GeoJSON][1] para representar determinadas geometrias.

## <a name="circle"></a>Circle

A `Circle` [especificação geojson][1]não dá suporte à geometria. Usamos um `GeoJSON Point Feature` objeto para representar um círculo.

Uma `Circle` geometria representada usando o `GeoJSON Feature` objeto __deve__ conter as seguintes coordenadas e propriedades:

- Centro

    O centro do círculo é representado usando um `GeoJSON Point` objeto.

- Raio

    O círculo `radius` é representada usando as propriedades de `GeoJSON Feature`. O valor de raio está em _metros_ e deve ser do tipo `double`.

- SubType

    A geometria do círculo também deve conter a propriedade `subType`. Essa propriedade deve ser uma parte das propriedades `GeoJSON Feature`do e seu valor deve ser _Circle_

#### <a name="example"></a>Exemplo

Veja como você representará um círculo usando um `GeoJSON Feature` objeto. Vamos centralizar o círculo em latitude: 47,639754 e Longitude:-122,126986 e atribuir a ele um raio igual a 100 medidores:

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

## <a name="rectangle"></a>Retângulo

A `Rectangle` [especificação geojson][1]não dá suporte à geometria. Usamos um `GeoJSON Polygon Feature` objeto para representar um retângulo. A extensão de retângulo é usada principalmente pelo módulo de ferramentas de desenho do SDK da Web.

Uma `Rectangle` geometria representada usando o `GeoJSON Polygon Feature` objeto __deve__ conter as seguintes coordenadas e propriedades:

- Corner

    Os cantos do retângulo são representados usando as coordenadas de `GeoJSON Polygon` um objeto. Deve haver cinco coordenadas, uma para cada canto. E, uma quinta coordenada que seja igual à primeira coordenada, para fechar o anel do polígono. Supõe-se que essas coordenadas se alinhem e que o desenvolvedor pode girá-las conforme desejado.

- SubType

    A geometria do retângulo também deve conter `subType` a propriedade. Essa propriedade deve ser uma parte das propriedades `GeoJSON Feature`do e seu valor deve ser _Rectangle_

### <a name="example"></a>Exemplo

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre dados geojson no Azure Maps:

> [!div class="nextstepaction"]
> [Formato geojson de limite geográfico](geofence-geojson.md)

Examine o Glossário de termos técnicos comuns associados a aplicativos do Azure Maps e do local Intelligence:

> [!div class="nextstepaction"]
> [Glossário do Azure Maps](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
