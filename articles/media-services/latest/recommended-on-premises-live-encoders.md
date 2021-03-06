---
title: Codificadores de streaming dinâmicos recomendados pelos Serviços de Mídia – Azure | Microsoft Docs
description: Saiba mais sobre codificadores locais de transmissão ao vivo recomendados pelos Serviços de Mídia
services: media-services
keywords: codificação; codificadores; mídia
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 04/16/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 0676b6b183c64dcd0fb15b87de48a4afed3a0011
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641797"
---
# <a name="tested-on-premises-live-streaming-encoders"></a>Codificadores de streaming ao vivo locais testados

Nos Serviços de Mídia do Azure, um [Evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents) (canal) representa um pipeline para processamento de conteúdo de streaming dinâmico. O Evento ao vivo recebe fluxos de entrada dinâmicos em uma de duas maneiras.

* Um codificador dinâmico local envia um RTMP com múltiplas taxas de bits ou fluxo Smooth Streaming (MP4 fragmentado) para o evento ao vivo que não está habilitado para executar a codificação ativa com os Serviços de Mídia. Os fluxos ingeridos passam por eventos ao vivo sem nenhum processamento adicional. Esse método é chamado **passagem**. Recomendamos que o codificador ao vivo envie fluxos de múltiplas taxas de bits em vez de um fluxo de taxa de bits única para um evento de passagem ao vivo para permitir o streaming de taxa de bits adaptável para o cliente. 

    Se você estiver usando fluxos de várias taxas de bits para o evento de passagem ao vivo, o tamanho do vídeo GOP e os fragmentos de vídeo em taxas de bits diferentes devem ser sincronizados para evitar um comportamento inesperado no lado da reprodução.

  > [!TIP]
  > O uso de um método de passagem é a maneira mais econômica de realizar uma transmissão ao vivo.
 
* Um codificador ao vivo local envia um fluxo de taxa de bits única para o evento ao vivo que está habilitado para executar a codificação ativa com os serviços de mídia em um dos seguintes formatos: RTMP ou Smooth Streaming (MP4 fragmentado). O evento ao vivo, em seguida, executa a codificação ativa do fluxo de entrada de taxa de bits única para um fluxo de vídeo de múltiplas taxas de bits (adaptável).

Este artigo aborda codificadores de transmissão ao vivo locais testados. Para obter instruções sobre como verificar seu codificador ao vivo local, consulte [verificar seu codificador local](become-on-premises-encoder-partner.md)

Para obter informações detalhadas sobre a codificação ativa com os Serviços de Mídia, consulte [Transmissão ao vivo com Serviços de Mídia v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Requisitos do codificador

Os codificadores devem dar suporte a TLS 1,2 ao usar protocolos HTTPS ou RTMPS.

## <a name="live-encoders-that-output-rtmp"></a>Codificadores dinâmicos com RTMP de saída

Os Serviços de Mídias recomendam usar um dos seguintes codificadores dinâmicos que têm RTMP como saída. Os esquemas de URL com suporte são `rtmp://` ou `rtmps://`.

Ao transmitir via RTMP, verifique as configurações de firewall e/ou proxy para confirmar se as portas TCP de saída 1935 e 1936 estão abertas.<br/><br/>
Ao transmitir via RTMPS, verifique as configurações de firewall e/ou proxy para confirmar se as portas TCP de saída 2935 e 2936 estão abertas.

> [!NOTE]
> Os codificadores devem dar suporte a TLS 1,2 ao usar protocolos RTMPS.

- Adobe Flash Media Live Encoder 3.2
- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- Elemento dinâmico (versão 2.14.15 e superior)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Alternador Studio (iOS)
- Telestream Wirecast (versão 13.0.2 ou superior devido ao requisito de TLS 1,2)
- Telestream Wirecast S (há suporte apenas para RTMP)
- Teradek Slice 756
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 e Hero 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Codificadores dinâmicos que transmitem MP4 fragmentado

Os Serviços de Mídia recomendam usar um dos seguintes codificadores dinâmicos que têm Smooth Streaming com múltiplas taxas de bits (MP4 fragmentado) como saída. Os esquemas de URL com suporte são `http://` ou `https://`.

> [!NOTE]
> Os codificadores devem dar suporte a TLS 1,2 ao usar protocolos HTTPS.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemento dinâmico (versão 2.14.15 e superior devido ao requisito de TLS 1,2)
- Envivio 4Caster C4 Gen III 
- Imagine Communications Selenio MCP3
- Mídia Hero ao vivo e Hero 4K (UHD/HEVC) do Excel
- [Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  Se você estiver transmitindo eventos ao vivo em vários idiomas (por exemplo, uma faixa de áudio em inglês e uma faixa de áudio em espanhol), poderá fazer isso com o Media Excel Live Encoder configurado para enviar o feed ao vivo para um evento ao vivo de passagem.

## <a name="configuring-on-premises-live-encoder-settings"></a>Definir as configurações do codificador dinâmico local

Para obter informações sobre quais configurações são válidas para o seu tipo de evento ao vivo, consulte [Comparação de tipos de evento ao vivo](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Requisitos de reprodução

Para reproduzir conteúdo, os fluxos de áudio e de vídeo devem estar presentes. Não há suporte para a reprodução de fluxo somente de vídeo.

### <a name="configuration-tips"></a>Dicas de configuração

- Sempre que possível, use uma conexão de Internet com fio.
- Ao determinar os requisitos de largura de banda, dobre as taxas de bits de streaming. Embora não seja obrigatório, essa regra simples ajuda a atenuar o impacto do congestionamento de rede.
- Ao usar codificadores baseados em software, feche todos os programas desnecessários.
- A alteração da configuração do codificador após ela ter começado a efetuar push tem efeitos negativos sobre o evento. As alterações na configuração podem fazer o evento ficar instável. 
- Reserve bastante tempo para configurar seu evento. Para eventos em alta escala, recomendamos que você comece a configuração uma hora antes do evento.
- Use o vídeo H. 264 e a saída do codec de áudio AAC.
- Verifique se há um quadro-chave ou um alinhamento temporal GOP entre as qualidades de vídeo.
- Verifique se há um nome de fluxo exclusivo para cada qualidade de vídeo.
- Use a codificação CBR estrita recomendada para desempenho ideal de taxa de bits adaptável.

> [!IMPORTANT]
> Observe a condição física da máquina (CPU/memória/etc.), já que o carregamento de fragmentos para a nuvem envolve operações de CPU e e/s. Se você alterar as configurações no codificador, será determinado que redefina os canais/evento ao vivo para que a alteração tenha efeito.

## <a name="see-also"></a>Confira também

[Transmissão ao vivo com os Serviços de Mídia v3](live-streaming-overview.md)

## <a name="next-steps"></a>Próximas etapas

[Como verificar seu codificador](become-on-premises-encoder-partner.md)
