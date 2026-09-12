# Story

Gerador de moldura para stories do Instagram e Facebook (1080x1920). Arquivo
único (`index.html`), sem dependências e sem rede.

O logo e o número reaproveitam a mesma arte do [app de perfil](../perfil) —
extraídos da moldura circular já existente (o texto e o logo não foram
redesenhados) e remontados numa faixa reta de ponta a ponta, sem o contorno
curvo/pontudo que sobrava do recorte circular original. A faixa fica
posicionada acima da área que Instagram e Facebook cobrem com a barra de
resposta/stickers.

Ao carregar a foto, tenta centralizar o rosto sozinho (Shape Detection API —
hoje só Chrome/Edge, principalmente Android; nos demais navegadores cai no
enquadramento central de sempre). O botão "Compartilhar" (Web Share API) só
aparece em navegadores que sabem compartilhar arquivo — normalmente celular.

Decisões de projeto compartilhadas (redução de imagem em etapas, preview na
densidade da tela, sanitização do nome do arquivo): ver
[`HANDOFF.md`](../../HANDOFF.md) na raiz do repositório — os mesmos
princípios do gerador de perfil se aplicam aqui, adaptados para o formato
retangular vertical (sem recorte circular, sem opção de fundo transparente,
já que a foto sempre cobre o quadro inteiro).
