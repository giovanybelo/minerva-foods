# Providers — camada de render (Higgsfield CLI ou Magnific MCP)

O prompt não muda por causa do provider — você escreve seguindo o núcleo cinematográfico (realista) ou `stylized-mode.md` (não-realista), e só no momento do render escolhe o provider. Nunca troque de provider no meio de um batch: se começou no Higgsfield, termina no Higgsfield; se começou no Magnific, termina no Magnific.

## Ordem de resolução do provider

Resolva nesta ordem e pare no primeiro que der match:

1. **Pedido explícito do usuário** — "usa o Magnific", "roda pelo Higgsfield", "usa o MCP".
2. **Variável de ambiente** `HUMAN_IMAGE_PROVIDER` — valores aceitos: `higgsfield` ou `magnific`.
3. **Auto-detecção**, nesta ordem:
   - Ferramentas `mcp__magnific__*` disponíveis na sessão **e** Higgsfield CLI não instalado/logado → **Magnific MCP**.
   - Higgsfield CLI instalado e logado → **Higgsfield CLI**.
4. **Os dois disponíveis** → use **Higgsfield CLI** (padrão da casa) e avise em uma linha que o Magnific também está disponível.
5. **Nenhum disponível** → não tente renderizar. Entregue o prompt salvo em disco e conduza o setup abaixo, em linguagem simples, sem stack trace.

### Pré-flight obrigatório

Antes de qualquer render, se `scripts/render_image.py` existir no projeto:

```bash
python3 scripts/render_image.py check-providers
```

Windows: `python scripts\render_image.py check-providers`.

O comando responde o status do Higgsfield CLI. O status do Magnific você mesmo verifica: procure ferramentas `mcp__magnific__*` na sessão (se diferidas, `ToolSearch` com a query `magnific`).

## Contrato comum (vale para os dois providers)

```text
human-output/image/{project_slug}/
├── prompt.txt          prompt mestre em inglês
├── brief.txt           pedido original, modo, qtd, aspect, luz/estilo, resolução, data
├── image-01.png        arquivos finais, numerados
├── image-02.png
├── metadata.json       provider, modelo, parâmetros, caminhos
└── _logs/              1 json por imagem
```

Parâmetros lógicos: `prompt` (texto em inglês, idêntico nos dois providers), `aspect_ratio` (`auto, 1:1, 3:2, 2:3, 4:3, 3:4, 4:5, 5:4, 9:16, 16:9, 21:9`), `resolution` (`1k, 2k, 4k`, padrão `2k`), `references` (0..N imagens locais, opcional), `n` (quantidade — 2 ou mais = render em paralelo, teto de 4 simultâneas).

## Provider A — Higgsfield CLI (padrão)

Modelo obrigatório: Nano Banana 2 (`nano_banana_2`).

```bash
python3 scripts/render_image.py render \
  "human-output/image/{slug}/prompt.txt" \
  --aspect-ratio "4:5" --resolution "2k" \
  --output-dir "human-output/image/{slug}" --output-name "image-01.png"
```

Com referência local (repita a flag para várias):

```bash
python3 scripts/render_image.py render \
  "human-output/image/{slug}/prompt.txt" \
  --aspect-ratio "4:5" --resolution "2k" \
  --output-dir "human-output/image/{slug}" --output-name "image-01.png" \
  --reference "/caminho/da/referencia.png"
```

Se `scripts/render_image.py` não existir no projeto atual (ele é o executor da pasta original "Human Image" — check-providers/render/save-external — e pode não ter sido copiado para este repositório), chame o CLI direto:

```bash
higgsfield generate create nano_banana_2 --prompt "$PROMPT" --aspect_ratio "4:5" --resolution "2k"
```

Com referência: adicione `--image "$REF_UUID"` (suba a referência antes, conforme a doc do Higgsfield CLI, e use o UUID retornado).

Windows: troque `python3` por `python` e use `\` nos caminhos.

Se falhar, não troque de modelo como fallback. Corrija login, referências, prompt, aspect ratio ou resolução e tente de novo.

### Batch = paralelo, sempre

Se o pedido for de duas ou mais imagens, dispare todas de uma vez — nunca em série. Use `xargs -P`, que já segura a concorrência no teto de 4:

```bash
printf '%s\n' 01 02 03 04 | xargs -P 4 -I{} \
  python3 scripts/render_image.py render "human-output/image/{slug}/prompt.txt" \
    --aspect-ratio "{aspect_ratio}" --resolution "{1k|2k|4k}" \
    --output-dir "human-output/image/{slug}" --output-name "image-{}.png"
```

Windows (PowerShell 7+):

```powershell
1..4 | ForEach-Object -ThrottleLimit 4 -Parallel {
  python scripts\render_image.py render "human-output/image/{slug}/prompt.txt" `
    --aspect-ratio "{aspect_ratio}" --resolution "{1k|2k|4k}" `
    --output-dir "human-output/image/{slug}" --output-name ("image-{0:d2}.png" -f $_)
}
```

No Git Bash do Windows, use a mesma linha do `xargs`. Para 10 imagens, é a mesma linha com a lista maior — `-P 4` mantém 4 rodando e vai repondo conforme terminam. Não aumente o teto sem motivo (o Higgsfield pode responder com throttling). Repita `--reference` em todas as chamadas do batch, não só na primeira.

Ao final, confira quais arquivos existem de verdade:

```bash
ls -1 "human-output/image/{slug}"/image-*.png
```

Diga quais faltaram e o motivo (erro em `_logs/image-NN.json`), e ofereça refazer só essas — nunca o batch inteiro.

**Consolide o `metadata.json` no fim.** O script grava `metadata.json` a cada render, então em paralelo ele acaba descrevendo só a última que terminou. Depois do batch, reescreva o arquivo com o registro de todas as imagens, lendo cada `_logs/image-NN.json` (modelo, job, parâmetros, referências, URL).

## Provider B — Magnific MCP

Servidor: `https://mcp.magnific.com/mcp` (HTTP, autenticado por OAuth no primeiro uso).

### Setup (uma vez por máquina)

Se a pessoa abriu o Claude Code dentro da pasta original ("Human Image"), o `.mcp.json` de lá já declara o servidor — basta aprovar quando o Claude Code perguntar. Em qualquer outra pasta:

```bash
claude mcp add --transport http --scope user magnific https://mcp.magnific.com/mcp
```

Depois, reabra a sessão e faça o login/autorização do Magnific quando pedir.

### Como chamar

Os nomes exatos das ferramentas podem mudar entre versões do servidor. Descubra em runtime: procure as ferramentas `mcp__magnific__*` na sessão (se diferidas, `ToolSearch` com a query `magnific`) e leia a assinatura antes de chamar. Mapeie o contrato comum para os parâmetros reais da ferramenta:

| Contrato | Como passar no Magnific |
|---|---|
| `prompt` | campo de prompt/texto da ferramenta de geração |
| `aspect_ratio` | campo de aspect ratio; se só aceitar largura/altura, converta mantendo a proporção |
| `resolution` | `1k` ~ 1024px, `2k` ~ 2048px, `4k` ~ 4096px no lado maior |
| `references` | campo de imagem de referência / image-to-image, se existir |
| `n` | uma chamada por imagem |

Se o Magnific expuser uma ferramenta de upscale além da de geração: gere em `2k` e faça upscale só quando o usuário pedir qualidade máxima. Não faça upscale por conta própria — consome crédito.

A regra do paralelo é a mesma: chame as ferramentas `mcp__magnific__*` com o mesmo prompt e os mesmos parâmetros, todas na mesma leva (várias chamadas de ferramenta numa resposta só rodam em paralelo).

### Salvando o resultado no padrão da casa

O MCP devolve uma URL (ou um arquivo). Não deixe o resultado solto — se `scripts/render_image.py` existir:

```bash
python3 scripts/render_image.py save-external --url "{url_retornada}" \
  --output-dir "human-output/image/{slug}" --output-name "image-01.png" \
  --provider magnific_mcp --model "{ferramenta}" \
  --prompt-file "human-output/image/{slug}/prompt.txt" \
  --aspect-ratio "{aspect_ratio}" --resolution "{1k|2k|4k}"
```

Se o MCP já gravou um arquivo local, troque `--url` por `--file "/caminho/do/arquivo.png"`. Se o script não existir, baixe/copie o arquivo você mesmo para `human-output/image/{slug}/image-NN.png` e escreva um `metadata.json` equivalente manualmente.

Vale a mesma consolidação do `metadata.json` no fim do batch.

## Quando nenhum provider existe

Não invente render e não prometa arquivo. Faça assim:

1. Salve `prompt.txt` e `brief.txt` na pasta do projeto (isso sempre acontece).
2. Diga em uma frase que falta o motor de render.
3. Ofereça os dois caminhos, sem jargão:

**Higgsfield CLI**
```bash
npm install -g @higgsfield/cli
higgsfield auth login
```

**Magnific MCP**
```bash
claude mcp add --transport http --scope user magnific https://mcp.magnific.com/mcp
```

4. Confirme credenciais antes de rodar qualquer comando pago.

## Checklist do render

- [ ] Provider resolvido pela ordem acima, não por chute
- [ ] Pré-flight rodado antes do primeiro render (se o script existir)
- [ ] Prompt idêntico ao que seria usado no outro provider
- [ ] Batch inteiro no mesmo provider
- [ ] Batch de 2+ imagens disparado em paralelo, teto de 4 simultâneas — nunca em série
- [ ] `--reference` repetido em todas as chamadas do batch, não só na primeira
- [ ] Conferido quais PNGs existem de fato no fim; refeitas só as que falharam
- [ ] Saída em `human-output/image/{project_slug}/` na pasta atual do usuário
- [ ] `metadata.json` consolidado no fim, com o batch inteiro
- [ ] Sem fallback silencioso de modelo ou de provider
