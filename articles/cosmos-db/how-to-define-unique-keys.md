---
title: Definir chaves exclusivas para um contêiner do Azure Cosmos
description: Saiba como definir chaves exclusivas para um contêiner Cosmos do Azure usando portal do Azure, PowerShell, .net, Java e vários outros SDKs.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: thweiss
ms.openlocfilehash: af68d733dfb0e0d1c257c8db03656112eec7381b
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871013"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Definir chaves exclusivas para um contêiner do Azure Cosmos

Este artigo apresenta as diferentes maneiras de definir [chaves exclusivas](unique-keys.md) durante a criação de um contêiner do Azure Cosmos. Atualmente é possível executar essa operação usando o portal do Azure ou por meio de um dos SDKs.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-sql-api-dotnet.md#create-account) ou selecione uma conta existente.

1. Abra o painel do **Data Explorer** e selecione o contêiner no qual deseja trabalhar.

1. Clique em **Novo Contêiner**.

1. Na caixa de diálogo **Adicionar Contêiner**, clique em **+ Adicionar chave exclusiva** para adicionar uma entrada de chave exclusiva.

1. Insira o(s) caminho(s) de restrição de chave exclusiva

1. Se necessário, adicione mais entradas de chave exclusiva, clicando em **+ Adicionar chave exclusiva**

    ![Captura de tela de entrada de restrição de chave exclusiva no portal do Azure](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-powershell"></a>Usar o Powershell

Para criar um contêiner com chaves exclusivas, consulte [criar um contêiner Cosmos do Azure com chave exclusiva e TTL](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="use-the-net-sdk"></a>Usar o SDK .NET

# <a name="net-sdk-v2"></a>[SDK DO .NET V2](#tab/dotnetv2)

Ao criar um novo contêiner usando o [SDK do .NET v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/), um objeto `UniqueKeyPolicy` pode ser usado para definir restrições de chave exclusiva.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    PartitionKey = new PartitionKeyDefinition { Paths = new Collection<string>(new List<string> { "/myPartitionKey" }) },
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "/emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

# <a name="net-sdk-v3"></a>[SDK DO .NET V3](#tab/dotnetv3)

Ao criar um novo contêiner usando o [SDK do .net v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), use a API Fluent do SDK para declarar chaves exclusivas de maneira concisa e legível.

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithUniqueKey()
        .Path("/firstName")
        .Path("/lastName")
        .Path("/emailAddress")
    .Attach()
    .WithUniqueKey()
        .Path("/address/zipCode")
    .Attach()
    .CreateIfNotExistsAsync();
```
---

## <a name="use-the-java-sdk"></a>Usar o SDK do Java

Ao criar um novo contêiner usando o [SDK do Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb), um objeto `UniqueKeyPolicy` pode ser usado para definir restrições de chave exclusiva.

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");

// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");

// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);

// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);

// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);

// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>Usar o SDK do Node.js

Ao criar um novo contêiner usando o [SDK do Node.js](https://www.npmjs.com/package/@azure/cosmos), um objeto `UniqueKeyPolicy` pode ser usado para definir restrições de chave exclusiva.

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>Usar o SDK do Python

Ao criar um novo contêiner usando o [SDK do Python](https://pypi.org/project/azure-cosmos/), restrições de chave exclusivas podem ser especificadas como parte do dicionário passado como parâmetro.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            {'paths': ['/firstName', '/lastName', '/emailAddress']},
            {'paths': ['/address/zipCode']}
        ]
    }
})
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [particionamento](partition-data.md)
- Explore [como funciona a indexação](index-overview.md)
