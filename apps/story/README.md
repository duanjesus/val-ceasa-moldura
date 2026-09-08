# Story

Gerador de moldura para stories do Instagram e Facebook (1080x1920). Arquivo
único (`index.html`), sem dependências e sem rede.

A faixa com logo e número reaproveita exatamente a mesma arte do
[app de perfil](../perfil) — foi recortada da moldura circular já existente
(recorte analítico por coordenadas, sem reconstruir texto ou logo do zero) e
composta como uma peça flutuante sobre a foto, posicionada acima da faixa
inferior que Instagram e Facebook cobrem com a barra de resposta/stickers.

Decisões de projeto compartilhadas (redução de imagem em etapas, preview na
densidade da tela, sanitização do nome do arquivo): ver
[`HANDOFF.md`](../../HANDOFF.md) na raiz do repositório — os mesmos
princípios do gerador de perfil se aplicam aqui, adaptados para o formato
retangular vertical (sem recorte circular, sem opção de fundo transparente,
já que a foto sempre cobre o quadro inteiro).
