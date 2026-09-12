# Materiais de campanha — Val Ceasa 25888

Monorepo de peças gráficas para a campanha de Val Ceasa, candidato à
reeleição a deputado estadual pelo Rio de Janeiro (número 25888). Primeiro
turno em 4 de outubro de 2026.

Cada peça é uma página estática autocontida, sem back-end e sem build step:
abre direto no navegador, todo o processamento acontece no dispositivo do
apoiador, nenhuma foto ou dado sai da máquina. `index.html` na raiz é a
landing page publicada no GitHub Pages, com um card para cada app.

## Apps

| pasta | status | descrição |
|---|---|---|
| [`apps/perfil`](apps/perfil) | pronto | moldura de foto de perfil (WhatsApp, Instagram, Facebook) |
| [`apps/story`](apps/story) | pronto | moldura para stories do Instagram e Facebook |
| capa-facebook | planejado | capa de perfil no Facebook |
| santinho | planejado | material impresso |

## Contexto e decisões de projeto

Ver [`HANDOFF.md`](HANDOFF.md): especificação da arte, geometria da moldura,
decisões não óbvias do gerador (redução de imagem em etapas, recorte
circular, fundo branco por padrão) e o histórico da investigação sobre
automação via WhatsApp — bloqueada pela política da Meta para campanhas
políticas.

## Próximos passos

1. Pedir o logo vetorial original à campanha
2. Construir os apps de capa de Facebook e santinho
