---
title: 'Padrão: Efeitos de uma definição de política'
description: Esse padrão do Azure Policy fornece um exemplo de como usar os diferentes efeitos de uma definição de política.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 1a9aec50bd328b76271d54f7830c75e0848d3cde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372629"
---
# <a name="azure-policy-pattern-effects"></a>Padrão do Azure Policy: efeitos

O Azure Policy tem vários [efeitos](../concepts/effects.md) que determinam como o serviço reage a recursos não compatíveis. Alguns efeitos são simples e não requerem propriedades adicionais na definição de política, enquanto outros exigem várias propriedades.

## <a name="sample-1-simple-effect"></a>Exemplo 1: Efeito simples

Esta definição de política verifica se a marca definida no parâmetro **tagName** existe no recurso avaliado. Se a marca ainda não existir, o efeito [modify](../concepts/effects.md#modify) será acionado para adicionar a marca com o valor no parâmetro **tagValue**.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json":::

### <a name="sample-1-explanation"></a>Exemplo 1: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-1.json" range="40-50":::

Um efeito **modify** requer o bloco **policyRule.then.details** que define **roleDefinitionIds** e **operations**. Esses parâmetros informam ao Azure Policy quais funções são necessárias para adicionar a marca e corrigir o recurso e qual operação **modify** a ser executada. Neste exemplo, a **operação** é _adicionar_ e os parâmetros são usados para definir a marca e seu valor.

## <a name="sample-2-complex-effect"></a>Exemplo 2: Efeito complexo

Essa definição de política audita cada máquina virtual para saber quando não existe uma extensão, definida nos parâmetros **editor** e **tipo**. [auditIfNotExists](../concepts/effects.md#auditifnotexists) é usado para verificar um recurso relacionado à máquina virtual para verificar se existe uma instância que corresponde aos parâmetros definidos. Este exemplo verifica o tipo de **extensão**.

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json":::

### <a name="sample-2-explanation"></a>Exemplo 2: Explicação

:::code language="json" source="~/policy-templates/patterns/pattern-effect-details-2.json" range="45-58":::

Um efeito **auditIfNotExists** requer que o bloco **policyRule.then.details** defina um **tipo** e **existenceCondition** para procurar. **existenceCondition** usa elementos de linguagem de política, como [operadores lógicos](../concepts/definition-structure.md#logical-operators), para determinar se existe um recurso relacionado correspondente. Neste exemplo, os valores verificados em cada [alias](../concepts/definition-structure.md#aliases) são definidos em parâmetros.

## <a name="next-steps"></a>Próximas etapas

- Revise outros [padrões e definições internas](./index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).