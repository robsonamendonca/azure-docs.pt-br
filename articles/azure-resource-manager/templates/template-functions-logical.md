---
title: Funções de modelo – lógica
description: Descreve as funções a serem usadas em um modelo do Resource Manager para determinar valores lógicos.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 0072593e7d7830e75e2386bcfdd2907a873c7a87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192307"
---
# <a name="logical-functions-for-arm-templates"></a>Funções lógicas para modelos ARM

O Gerenciador de recursos fornece várias funções para fazer comparações em seus modelos de Azure Resource Manager (ARM).

* [e](#and)
* [bool](#bool)
* [if](#if)
* [válido](#not)
* [or](#or)

## <a name="and"></a>e

`and(arg1, arg2, ...)`

Verifica se todos os valores de parâmetros são verdadeiros.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |booleano |O primeiro valor para verificar se é verdadeiro. |
| arg2 |Sim |booleano |O segundo valor para verificar se é verdadeiro. |
| argumentos adicionais |Não |booleano |Argumentos adicionais para verificar se são verdadeiros. |

### <a name="return-value"></a>Valor retornado

Retorna **True** se todos os valores forem verdadeiros; caso contrário, **False**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

O resultado do exemplo anterior é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Falso |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | Falso |

## <a name="bool"></a>bool

`bool(arg1)`

Converte o parâmetro em um booliano.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |cadeia de caracteres ou inteiro |O valor a ser convertido em um booliano. |

### <a name="return-value"></a>Valor retornado
Um booliano do valor convertido.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) a seguir mostra como usar um booliano com uma cadeia de caracteres ou um inteiro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

A saída do exemplo anterior com os valores padrão é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | Falso |
| trueInt | Bool | True |
| falseInt | Bool | Falso |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Retorna um valor com base em se uma condição é verdadeira ou falsa.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| condition |Sim |booleano |O valor para verificar se é verdadeiro ou falso. |
| trueValue |Sim | cadeia de caracteres, inteiro, objeto ou matriz |O valor a ser retornado quando a condição é verdadeira. |
| falseValue |Sim | cadeia de caracteres, inteiro, objeto ou matriz |O valor a ser retornado quando a condição é falsa. |

### <a name="return-value"></a>Valor retornado

Retorna o segundo parâmetro quando o primeiro parâmetro é **True**; caso contrário, retorna o terceiro parâmetro.

### <a name="remarks"></a>Comentários

Quando a condição for **verdadeira**, somente o valor verdadeiro será avaliado. Quando a condição for **falsa**, somente o valor false será avaliado. Com a função **If** , você pode incluir expressões que são apenas condicionalmente válidas. Por exemplo, você pode fazer referência a um recurso que existe sob uma condição, mas não sob a outra condição. Um exemplo de expressões de avaliação condicional é mostrado na seção a seguir.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) a seguir mostra como usar a função `if`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
        }
    }
}
```

O resultado do exemplo anterior é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| yesOutput | String | sim |
| noOutput | String | não |
| objectOutput | Objeto | { "test": "value1" } |

O [modelo de exemplo](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) a seguir mostra como usar essa função com expressões que são apenas condicionalmente válidas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "logAnalytics": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "condition": "[not(empty(parameters('logAnalytics')))]",
            "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2017-03-30",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
```

## <a name="not"></a>não

`not(arg1)`

Converte o valor booliano em seu valor oposto.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |booleano |O valor a ser convertido. |

### <a name="return-value"></a>Valor retornado

Retorna **True** quando o parâmetro é **False**. Retorna **False** quando o parâmetro é **True**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

O resultado do exemplo anterior é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Falso |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | Falso |

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) a seguir usa **not** com [equals](template-functions-comparison.md#equals).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
}
```

O resultado do exemplo anterior é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>ou o

`or(arg1, arg2, ...)`

Verifica se qualquer valor do parâmetro é verdadeiro.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Tipo | Descrição |
|:--- |:--- |:--- |:--- |
| arg1 |Sim |booleano |O primeiro valor para verificar se é verdadeiro. |
| arg2 |Sim |booleano |O segundo valor para verificar se é verdadeiro. |
| argumentos adicionais |Não |booleano |Argumentos adicionais para verificar se são verdadeiros. |

### <a name="return-value"></a>Valor retornado

Retorna **True** se qualquer valor for verdadeiro; caso contrário, **False**.

### <a name="examples"></a>Exemplos

O [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) a seguir mostra como usar funções lógicas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

O resultado do exemplo anterior é:

| Nome | Type | Valor |
| ---- | ---- | ----- |
| andExampleOutput | Bool | Falso |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | Falso |

## <a name="next-steps"></a>Próximas etapas

* Para obter uma descrição das seções em um modelo de Azure Resource Manager, consulte [entender a estrutura e a sintaxe de modelos ARM](template-syntax.md).

