# Formato de entrega — Nano Banana 2 e checklist final

Vale para o modo REALISTA, em qualquer provider (o texto do prompt é idêntico — só muda quem renderiza).

## Regras de formato (sem exceção)

- Sem preamble em português, sem "## Abordagem", sem "## Prompt Final", sem header de seção.
- Sem SCENE HEADER em CAPS no topo (ex.: "EXT. LOCAL — NIGHT —").
- Sem bloco de proibições em CAPS no final (ex.: "NO TEXT, NO WATERMARK").
- Sem markdown nenhum (`##`, `**`, `-`, bullets, numeração).
- Sem parágrafo "Inspired by [diretor] in [filme]" — só a linha final genérica.
- Sem HEX codes, sem COLOR ROLE MAPPING, sem W3C anchors.
- Sem emojis, sem perguntas, sem meta-comentários.

Cada parágrafo abre com um header contextual em CAPS seguido de dois pontos.

## Parágrafos obrigatórios, nesta ordem

```
CAMERA: corpo, ISO, posição.
LENS: modelo, focal, T-stop, distância, foco.
LIGHT: fonte motivada, Kelvin, direção, comportamento de sombra, IRE aproximado.
SUBJECT: posição corporal, ângulos, estado físico. Intercepted.
FOREGROUND: zona próxima, textura, dissolução do foco.
MIDGROUND: zona do sujeito, comportamento do foco.
BACKGROUND: profundidade, bokeh.
WARDROBE TONAL BEHAVIOR: material, comportamento sob luz.
MAKEUP SURFACE PHYSICS: textura de pele real, suor, oleosidade, poros.
POST BEHAVIOR: formato ou stock, grão visível, halation, curva, saturação, midtone priority.
COMPOSITIONAL GEOMETRY: peso visual, assimetria, intrusion, terços quebrados.
MOOD & ART DIRECTION: Composition and art direction inspired in the work of award-winning directors.
```

## Limite

Output total: no máximo 1.500 caracteres, mire em 1.200–1.450. Corte adjetivos e detalhes decorativos — preserve decisões físicas.

## Com imagem de referência

Leia mood, stock, cor, hora, preserve identidade do sujeito, mantenha coerência visual. Não descreva a imagem — traduza em decisões de câmera, luz e post. Use `@img1` no parágrafo `SUBJECT:`.

## Checklist interno — rode mentalmente antes de enviar

Para qualquer platform:
- [ ] Câmera é IMAX 65mm ou Alexa 35 — não outra
- [ ] Lente é do conjunto permitido pra aquela câmera
- [ ] Câmera em posição inusitada (baixa, hip, floor, oblíqua) — não altura-dos-olhos neutra
- [ ] POST BEHAVIOR tem formato OU stock coerente — não repetiu default
- [ ] Zero buzzwords (cinematic, epic, beautiful, dramatic, stunning, etc.)
- [ ] Zero HEX, zero W3C, zero COLOR ROLE MAPPING
- [ ] Zero diretores/filmes específicos citados
- [ ] Zero texto/logo/watermark pedido ou implícito na imagem
- [ ] Grão descrito como `visible`, `organic`, `tactile`, `heavy` — nunca `subtle` ou `fine`

Formato Nano Banana 2:
- [ ] Começou em `CAMERA:` e terminou em `MOOD & ART DIRECTION: Composition and art direction inspired in the work of award-winning directors.`
- [ ] Cada parágrafo obrigatório está presente, com header contextual em CAPS
- [ ] Total ≤ 1.500 caracteres
- [ ] Zero SCENE HEADER no topo, zero CAPS BLOCK no fim

Regra global:
- [ ] Roteamento realista/estilizado feito antes de escrever o prompt
- [ ] Provider resolvido pela ordem de `references/providers.md`, não por chute
- [ ] Render real no provider resolvido — `nano_banana_2` se Higgsfield, ferramenta `mcp__magnific__*` se Magnific
- [ ] Não trocar de modelo nem de provider como fallback
- [ ] Se falhar, ajustar prompt/refs/login/aspect ratio/resolution e tentar novamente
- [ ] Prosa contínua e enxuta, sem blocos
- [ ] Abre por sujeito + ação + ambiente
- [ ] Vocabulário fotográfico profissional usado

Se algum item falhar, corrija antes de enviar. Silenciosamente.
