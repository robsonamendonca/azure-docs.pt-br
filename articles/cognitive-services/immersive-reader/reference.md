---
title: Referência do SDK do leitor de imersão
titleSuffix: Azure Cognitive Services
description: O SDK do leitor de imersão contém uma biblioteca JavaScript que permite integrar o leitor de imersão ao seu aplicativo.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: cb88fb24ceed943d4104da6914959e4b79c35571
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231910"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Guia de referência do SDK do leitor de imersão

O SDK do leitor de imersão contém uma biblioteca JavaScript que permite integrar o leitor de imersão ao seu aplicativo.

## <a name="functions"></a>Funções

O SDK expõe as funções:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Inicia o leitor de imersão dentro `iframe` de um em seu aplicativo Web.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>Parâmetros

| Nome | Type | Descrição |
| ---- | ---- |------------ |
| `token` | string | O token de autenticação do Azure AD. |
| `subdomain` | cadeia de caracteres | O subdomínio personalizado do seu recurso de leitor de imersão no Azure. |
| `content` | [Conteúdo](#content) | Um objeto que contém o conteúdo a ser mostrado no leitor de imersão. |
| `options` | [Opções](#options) | Opções para configurar determinados comportamentos do leitor de imersão. Opcional. |

### <a name="returns"></a>Retornos

Retorna um `Promise<LaunchResponse>`, que resolve quando o leitor de imersão é carregado. O `Promise` é resolvido para um [`LaunchResponse`](#launchresponse) objeto.

### <a name="exceptions"></a>Exceções

O retornado `Promise` será rejeitado com um [`Error`](#error) objeto se o leitor de imersão não for carregado. Para obter mais informações, consulte os [códigos de erro](#error-codes).

## <a name="close"></a>close

Fecha o leitor de imersão.

Um exemplo de caso de uso para essa função é se o botão sair estiver oculto ```hideExitButton: true``` pela configuração em [Opções](#options). Em seguida, um botão diferente (por exemplo, seta para voltar do cabeçalho móvel) pode ```close``` chamar essa função quando ele é clicado.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

Essa função define e atualiza os elementos do botão de leitura imersiva do documento. Se ```options.elements``` for fornecido, essa função renderizará os botões em ```options.elements```. Caso contrário, os botões serão renderizados dentro dos elementos do documento que têm a ```immersive-reader-button```classe.

Essa função é chamada automaticamente pelo SDK quando a janela é carregada.

Consulte [atributos opcionais](#optional-attributes) para obter mais opções de renderização.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Parâmetros

| Nome | Type | Descrição |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | Opções para configurar determinados comportamentos da função renderButtons. Opcional. |

## <a name="types"></a>Tipos

### <a name="content"></a>Conteúdo

Contém o conteúdo a ser mostrado no leitor de imersão.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Parte

Um único bloco de dados, que será passado para o conteúdo do leitor de imersão.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>LaunchResponse

Contém a resposta da chamada para `ImmersiveReader.launchAsync`.

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>CookiePolicy enum

Uma enumeração usada para definir a política para o uso do cookie do leitor de imersão. Consulte [Opções](#options).

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>Tipos MIME com suporte

| Tipo MIME | Descrição |
| --------- | ----------- |
| texto/sem formatação | Texto sem formatação. |
| texto/html | Conteúdo HTML. [Saiba mais](#html-support)|
| aplicativo/MathML + XML | MathML (matematica Markup Language). [Saiba mais](./how-to/display-math.md).
| application/vnd. openxmlformats-officeDocument. WordprocessingML. Document | Documento de formato Microsoft Word. docx.

### <a name="html-support"></a>Suporte a HTML

| HTML | Conteúdo com suporte |
| --------- | ----------- |
| Estilos de fonte | Negrito, itálico, sublinhado, código, tachado, sobrescrito, subscrito |
| Listas não ordenadas | Disco, círculo, quadrado |
| Listas ordenadas | Decimal, superior-alfa, inferior-alfa, maiúsculo-Romano, minúsculo |

Marcas sem suporte serão renderizadas comparativamente. Não há suporte para imagens e tabelas no momento.

### <a name="options"></a>Opções

Contém propriedades que configuram determinados comportamentos do leitor de imersão.

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
    cookiePolicy?: CookiePolicy; // Setting for the Immersive Reader's cookie usage (default is CookiePolicy.Disable). It's the responsibility of the host application to obtain any necessary user consent in accordance with EU Cookie Compliance Policy.
}
```

### <a name="renderbuttonsoptions"></a>RenderButtonsOptions

Opções para renderizar os botões de leitura imersiva.

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

### <a name="error"></a>Erro do

Contém informações sobre o erro.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Códigos do Erro

| Código | Descrição |
| ---- | ----------- |
| BadArgument | O argumento fornecido é inválido, `message` consulte para obter detalhes. |
| Tempo limite | Falha ao carregar o leitor de imersão no tempo limite especificado. |
| TokenExpired | O token fornecido expirou. |
| Acelerado | O limite de taxa de chamada foi excedido. |

## <a name="launching-the-immersive-reader"></a>Iniciando o leitor de imersão

O SDK fornece o estilo padrão para o botão iniciar o leitor de imersão. Use o `immersive-reader-button` atributo Class para habilitar esse estilo. Consulte [Este artigo](./how-to-customize-launch-button.md) para obter mais detalhes.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Atributos opcionais

Use os atributos a seguir para configurar a aparência do botão.

| Atributo | Descrição |
| --------- | ----------- |
| `data-button-style` | Define o estilo do botão. Pode ser `icon`, `text` ou `iconAndText`. O padrão é `icon`. |
| `data-locale` | Define a localidade. Por exemplo, `en-US` ou `fr-FR`. O padrão é inglês `en`. |
| `data-icon-px-size` | Define o tamanho do ícone em pixels. O padrão é 20px. |

## <a name="browser-support"></a>Suporte ao navegador

Use as versões mais recentes dos seguintes navegadores para obter a melhor experiência com o leitor de imersão.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Próximas etapas

* Explorar o [SDK da Leitura Avançada no GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Início rápido: criar um aplicativo Web que inicia o leitor de imersão (C#)](./quickstart.md)
