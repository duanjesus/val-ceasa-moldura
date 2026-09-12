# Foto de perfil

Gerador de foto de perfil com a moldura da campanha. Arquivo único
(`index.html`), sem dependências e sem rede — as duas artes da moldura
(versão com selo "EU APOIO!" e versão enxuta) estão embutidas em base64.

Ao carregar a foto, tenta centralizar o rosto sozinho na metade de cima do
círculo (usando a Shape Detection API do navegador — hoje só Chrome/Edge,
principalmente Android; nos demais navegadores cai no enquadramento central
de sempre, e a pessoa sempre pode arrastar pra ajustar). O botão
"Compartilhar" (Web Share API) só aparece em navegadores que sabem
compartilhar arquivo — normalmente celular.

Decisões de projeto e especificação da arte: ver [`HANDOFF.md`](../../HANDOFF.md)
na raiz do repositório.
