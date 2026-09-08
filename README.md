# Moldura de perfil — Val Ceasa 25888

Gerador de foto de perfil com moldura da campanha de Val Ceasa, candidato à
reeleição a deputado estadual pelo Rio de Janeiro (número 25888).

Página estática, sem back-end. Tudo processado no navegador do apoiador: a
foto nunca sai do dispositivo. As duas artes da moldura (versão com selo "EU
APOIO!" e versão enxuta) estão embutidas em base64 dentro do próprio HTML.

## Uso

Abrir [`index.html`](index.html) direto no navegador, ou publicar como site
estático (GitHub Pages, Netlify, etc.).

## Contexto e decisões de projeto

Ver [`HANDOFF.md`](HANDOFF.md): especificação da arte, geometria da moldura,
decisões não óbvias do gerador (redução de imagem em etapas, recorte
circular, fundo branco por padrão) e o histórico da investigação sobre
automação via WhatsApp — bloqueada pela política da Meta para campanhas
políticas.

## Próximos passos

1. Detecção de rosto para enquadramento automático
2. Botão de compartilhar via Web Share API
3. Pedir o logo vetorial original à campanha
4. Estender a identidade para story, capa de Facebook e santinho
