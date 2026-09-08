# Moldura de perfil — Val Ceasa 25888

Handoff para continuar o projeto no Claude Code. Escrito para ser lido por um
agente que está pegando o trabalho do zero.

---

## 1. Contexto

Duan está entrando na equipe de marketing da campanha de **Val Ceasa**, candidato
à reeleição a **deputado estadual pelo Rio de Janeiro**, número **25888**.

Primeiro turno: **4 de outubro de 2026**. O prazo é curto e isso deve pesar em
qualquer decisão de arquitetura.

O material existente da campanha era fraco. A primeira entrega foi uma **moldura
de foto de perfil** para apoiadores usarem no WhatsApp, Facebook e Instagram,
feita do zero, mantendo a identidade da campanha mas com acabamento refinado.

Estado atual: moldura pronta em duas versões + um gerador HTML que roda no
navegador. O que está em aberto é como distribuir e automatizar isso.

---

## 2. O que já existe

```
moldura-val-25888-completa-1080.png            moldura com selo "EU APOIO!"
moldura-val-25888-completa-500.png
moldura-val-25888-completa-mascara-branca-1080.png
moldura-val-25888-enxuta-1080.png              mesma arte, sem o selo
moldura-val-25888-enxuta-500.png
moldura-val-25888-enxuta-mascara-branca-1080.png
gerador-foto-perfil.html                        gerador standalone (~350 KB)
apresentacao-moldura.png                        folha de apresentação p/ a equipe
```

Duas famílias de PNG:

- **normal**: miolo transparente E fora do círculo transparente. Para compor por
  código, onde o recorte circular é feito no canvas.
- **mascara-branca**: miolo transparente, fora do círculo preenchido de branco.
  Para montagem manual em Canva/PicsArt, onde não há como recortar o círculo.

O gerador é **um arquivo só**, com os dois PNGs embutidos em base64. Não depende
de rede, não sobe foto para lugar nenhum, processa tudo no dispositivo.

---

## 3. Especificação da arte

Tudo em espaço de 1080x1080. O render é feito em 3x (3240px) e reduzido com
LANCZOS no final.

### Cores

| uso | hex |
|---|---|
| azul topo do degradê | `#12429E` |
| azul base do degradê | `#04184A` |
| amarelo | `#FFD21E` |
| azul do texto sobre amarelo | `#061E56` |
| sombra | `rgba(2,14,46,0.41)` |

O amarelo original da campanha é `#CEE202`, um amarelo esverdeado que suja em
cima do azul. Foi trocado por `#FFD21E`. Se a campanha exigir o original, é um
parâmetro só.

### Geometria

```
círculo:            centro (540,540), raio 540
anel externo:       RING = 26 px
filete amarelo:     LINE = 7 px
borda da faixa:     y(x) = 785 + 0.00016 * (x - 540)^2

abertura da foto  = distância <= (540 - RING)  E  y < borda_da_faixa(x)
filete amarelo    = mesma região dilatada em LINE, menos a abertura,
                    limitada ao círculo
```

O degradê azul é vertical, aplicado no quadrado inteiro, e só aparece onde o
alpha é 255 (fora da abertura).

### Lockup da faixa (logo + número)

Grupo centrado horizontalmente, centro vertical em **y = 932**.

- logo VAL CEASA em branco, altura **132**
- espaço de **30**
- filete amarelo vertical, 4px de largura, 86% da altura do logo
- "25888" em Anton, **altura de caixa alta 118**, com sombra de 3px

O tamanho da fonte é calculado por iteração até bater a altura de caixa alta
pedida, não chutado. Ver `fit_font()`.

**Restrição importante:** esse grupo está no limite geométrico. O canto inferior
esquerdo do logo fica a ~534px do centro, contra um raio de 540. Aumentar o
número ou o logo faz o conteúdo sair do círculo e ser cortado. Se precisar
crescer, tem que subir o grupo, e aí a faixa precisa começar mais alto.

### Selo "EU APOIO!" (só na versão completa)

Pílula amarela apoiada na borda de cima da faixa, invadindo a foto do apoiador.

- centro vertical **y = 757** (a faixa começa em 785, então o selo fica
  majoritariamente sobre a foto)
- texto em Anton, altura de caixa alta **50**, cor `#061E56`
- padding 44 na horizontal, 27 na vertical
- raio do canto = metade da altura
- sombra: offset 5px, blur 7px. Sem ela o selo some quando a foto do apoiador
  tem fundo claro.

### Tipografia

- **Anton Regular** — número e selo. Condensada e pesada, aguenta redução.
- **Montserrat variable** — textos de apoio na folha de apresentação.

Baixadas de `raw.githubusercontent.com/google/fonts/main/ofl/...`.

### Logo

A campanha não forneceu vetor. O logo branco foi **extraído por código** de um
material existente:

1. upscale 4x da imagem original
2. máscara de "não branco", label de componentes conectados
3. flood fill a partir das bordas, o que não é alcançado é o adesivo do logo
   (o contorno branco fecha a silhueta)
4. dentro da silhueta, alpha proporcional ao quanto o pixel é escuro, o que
   converte o preenchimento azul-marinho em branco e deixa os vãos internos
   transparentes

Resultado em `val_logo_white.png`. Funciona bem no tamanho usado.

**Pendência real:** pedir à campanha o arquivo vetorial original (AI, EPS ou
SVG) antes de fechar qualquer arte impressa ou em tamanho maior.

---

## 4. O gerador HTML

Arquivo único, sem dependências, sem rede, sem storage.

### O que ele faz

- escolher foto (clique ou arrastar)
- arrastar para posicionar, slider de zoom de 100 a 300
- alternar entre as duas molduras
- campo "nome do colaborador" que entra **só no nome do arquivo**
- checkbox "fundo transparente", desmarcado por padrão
- baixar PNG 1080x1080

### Decisões que não são óbvias e não devem ser desfeitas

**Redução em etapas.** Jogar uma foto de 4000px direto num canvas de 1080px em
um passo só borra a imagem. `stepDown()` reduz pela metade repetidamente até
chegar perto do alvo, com cache por largura. Isso resolveu uma reclamação real
de qualidade.

**Preview na densidade da tela.** O canvas é dimensionado por
`devicePixelRatio` (limitado a 3), não fixo. Antes disso o preview parecia
borrado no celular e passava a impressão de que o arquivo saía ruim.

**Recorte circular na exportação.** `globalCompositeOperation = 'destination-in'`
com um arco. Sem isso a foto vaza nos cantos, já que o PNG da moldura é
transparente fora do círculo.

**Fundo branco por padrão.** Depois do recorte, `destination-over` pinta branco
atrás. Motivo: PNG transparente enviado ao WhatsApp vira JPEG e o transparente
vira **preto**. Como todas as plataformas cortam em círculo, o branco nunca
aparece. O checkbox de transparência existe para uso de designer.

**Sanitização do nome do arquivo.** Usa `/[^\p{L}\p{N} ]/gu`, ou seja, mantém só
letras, números e espaço, depois colapsa espaços, `toUpperCase()` e corta em 40
caracteres. Acento passa (JOÃO, JOSÉ saem certo). Resultado:
`FULANO DE TAL perfil-val-25888.png`, e sem nome volta para
`perfil-val-25888.png`.

> Armadilha já vivida: a primeira tentativa usava classe de caracteres com a
> lista de proibidos do Windows e produziu um intervalo inválido, quebrando a
> página inteira com `Range out of order in character class`. A versão com
> `\p{L}\p{N}` é mais simples e mais segura.

### Testes que já foram feitos

Playwright headless, Chromium. Cobrem: carregar foto, trocar de moldura, baixar,
nome com acento, nome com caractere proibido, nome vazio, nome de 60 caracteres,
export com fundo branco e com alpha. Todos passando, console limpo.

---

## 5. Automação por WhatsApp: o que foi investigado

A ideia era: apoiador manda a foto para um número business, um bot responde com
a foto já na moldura.

**Isso está bloqueado pela Meta.** A política de mensagens comerciais proíbe
"políticos ou partidos, candidatos e campanhas políticas" de usar a WhatsApp
Business Platform, que é a API. O aplicativo WhatsApp Business comum continua
liberado, mas ele não tem API, então não dá para automatizar resposta.

Fontes:
- https://www.aosfatos.org/noticias/deputados-reuniao-whatsapp/
- https://wha.tools/pt/blog/we-are-a-political-party-can-we-use-whatsapp-api

**APIs não oficiais** (Evolution API, Z-API, Green API) funcionam dirigindo o
WhatsApp Web. O risco não é técnico, é banimento do número, e o banimento tende
a chegar quando o volume sobe, ou seja, na última semana de campanha. Não vale
apostar o número oficial nisso com menos de um mês para a eleição.

**Nota jurídica:** a legislação eleitoral brasileira restringe disparo em massa
de mensagens. O fluxo aqui é iniciado pelo apoiador, o que é caso diferente, mas
isso precisa passar pelo jurídico da campanha, não por um desenvolvedor.

---

## 6. Direção recomendada

Hospedar o gerador num link e distribuir o link. Zero infraestrutura, zero risco
de política de plataforma, funciona em qualquer celular hoje.

Duas melhorias que aproximam bastante da experiência de bot:

**Centralizar o rosto automaticamente.** Hoje a pessoa arrasta. Com detecção de
rosto no navegador, o enquadramento já sai certo. Detalhe que importa: o rosto
precisa cair na **metade de cima** do círculo, porque os ~30% de baixo estão
cobertos pela faixa azul. Um crop centrado ingênuo joga o rosto atrás da faixa.
Sem rosto detectado, cai no crop central. Isso é visão computacional leve, não
LLM.

**Botão de compartilhar.** Web Share API com arquivo, para o celular abrir o
WhatsApp já com a imagem anexada. É onde esse tipo de moldura circula, no status.

LLM não serve para montar a imagem. Só faria sentido numa camada de conversa
("quero a sem frase", "sobe mais o rosto"), e isso é fase dois.

### Sobre stack

Para a campanha, o caminho mais curto não precisa de back-end nenhum: página
estática, processamento no dispositivo do apoiador.

Duan está em transição de carreira para **back-end Java com Spring Boot**. Se
quiser que isso também vire portfólio, dá para expor o processamento numa API
Spring Boot, mas aí é um projeto paralelo com outro objetivo. Não confundir as
duas coisas dentro do prazo eleitoral.

---

## 7. Próximos passos sugeridos

1. Transformar o gerador em página hospedada, com layout mobile primeiro
2. Detecção de rosto com enquadramento na metade de cima do círculo
3. Botão de compartilhar via Web Share API
4. Pedir o logo vetorial original à campanha
5. Confirmar com o jurídico da campanha antes de qualquer distribuição em massa
6. Estender a identidade para story, capa de Facebook e santinho
7. Foto de perfil oficial das contas do candidato, quando as fotos dele chegarem

---

## 8. Preferências de quem toca o projeto

Duan pediu, para textos escritos em nome dele, **evitar travessão e evitar
frases com cara de texto gerado por IA**. Vale para copy de campanha também.
