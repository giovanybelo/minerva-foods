# Modo estilizado (não-realista)

> **Nota de origem:** os arquivos enviados para montar esta skill (`COMECE-AQUI.md`, `CLAUDE.md`, `imageprompts.md`, `providers.md`) referenciam um `stylized.md` como o playbook completo do modo estilizado, mas esse arquivo não foi enviado. Este arquivo cobre o que os outros quatro documentos já deixam explícito sobre o modo estilizado — o suficiente para rotear e não quebrar a regra mais importante (nunca aplicar aparato fotorrealista fora do modo realista). Se o `stylized.md` original existir no projeto onde esta skill for usada, ele tem prioridade sobre este arquivo: leia-o primeiro.

## Quando entrar nesse modo

Gatilhos: ilustração, desenho 2D, render 3D estilizado, cartoon, anime, vetor, flat, aquarela, pixel art, colagem, ícone, mascote, ou o próprio usuário dizendo "não realista", "sem ser foto", "estilizado". Trate isso como decisão de **meio** (o resultado não deve parecer capturado por uma câmera), não de humor — "cinematográfico", "dramático" ou "anos 70" não tiram do realista.

## O que muda

Desligue todo o aparato fotográfico: câmera, lente, ISO, T-stop, Kelvin, stock de filme, grão, halation, textura de pele real. Pedir "grão de filme Kodak 5219 e poros na pele" numa ilustração flat empurra o modelo de volta para o fotorrealismo e estraga o resultado.

No lugar disso, escreva um prompt de **direção de arte**, cobrindo:

- **Meio e técnica** — o que é fisicamente (ilustração vetorial, aquarela sobre papel texturizado, render 3D estilizado com toon shading, colagem recortada, pixel art de N cores).
- **Paleta** — cores dominantes e como se relacionam (complementar, monocromática, pastel, alto contraste), descritas em linguagem natural, não em HEX.
- **Linha e forma** — espessura e caráter do traço, geometria (orgânica vs. geométrica, arredondada vs. angular).
- **Acabamento/textura de superfície** — grão de papel, textura de tinta, ruído digital controlado, sombreamento chapado vs. gradiente.
- **Composição** — enquadramento, peso visual, o mesmo espírito de "ângulo pouco óbvio" do modo realista, adaptado ao meio (perspectiva incomum, corte ousado, assimetria).

O restante do fluxo é igual ao modo realista: sujeito + ação primeiro, zero buzzwords vazios ("bonito", "incrível"), zero texto/logo/watermark salvo pedido explícito, prompt final em inglês, mesmo contrato de pasta (`human-output/image/{slug}/`), mesmo processo de confirmação (projeto, quantidade, aspect ratio, **estilo/meio** no lugar de iluminação, resolução), e mesmo render paralelo em batch via `references/providers.md`.

## Checklist (substitui o checklist do modo realista quando estilizado)

- [ ] Nenhuma menção a câmera, lente, ISO, T-stop, Kelvin, stock de filme, grão ou textura de pele real
- [ ] Meio e técnica declarados com clareza (o que fisicamente é a imagem)
- [ ] Paleta descrita em linguagem natural, não HEX
- [ ] Composição com peso visual e ângulo não óbvio, adaptados ao meio
- [ ] Zero texto/logo/watermark pedido ou implícito
- [ ] Zero buzzwords vazios
- [ ] Prompt em inglês, sem markdown, sem preamble
- [ ] Provider resolvido pela ordem de `references/providers.md`, sem fallback silencioso
