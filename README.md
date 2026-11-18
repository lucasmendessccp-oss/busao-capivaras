# Busão Lotado das Capivaras · Fase 5.3 (tela limpa: menos texto, tudo perto de quem falou)

Jogo 3D de LIVE para o TikTok. Um busão em corte lateral roda pela cidade; as capivaras do público sobem, se espremem dentro e no teto; Motorista e Cobrador se conquistam com presente; dez presentes de caos bagunçam o busão; a LIVE entra pelo tik.tools (celular) ou pelo TikFinity (PC).

**A regra da Fase 5.1 continua.** Em uma frase para falar na LIVE:

> **"Fica no busão o máximo que puder; o resto tenta te derrubar!"**

**A Fase 5.2 mexeu no equilíbrio** (tronos, cobrador e teto) e continua valendo:

> **"Trono é seguro só no primeiro minuto!"**
> **"O cobrador cobra 1 segundo de cada um que passa na catraca!"**
> **"Lá em cima no teto é perigoso: abaixa que vem galho!"**

**A Fase 5.3 não muda nenhuma regra de jogo.** Ela só reorganiza o que aparece escrito na tela. A regra desta fase:

> **Ou o texto fica colado na capivara, ou fica num lugar fixo do HUD. Nada solto no meio da tela.**
> Se não há espaço perto da capivara, o texto **não aparece** (nunca é empurrado para longe).

Depois desta fase vem a **Fase 6** (teste em LIVE real). O roteiro está em `CHECKLIST-FASE6.md` (atualizado com os itens da 5.3).

---

## Fase 5.3 · tela limpa

### 1. Zonas fixas da tela (`CONFIG.tela.zonas`, em fração da altura)
| Zona | Altura | O que fica ali |
|---|---|---|
| Topo livre | 0 a 9 % | nada (barra do TikTok). É o mesmo `CONFIG.layout.topoLivre` |
| HUD | 9 a 19,5 % | ranking (esquerda), letreiro (meio), card de dica (direita) |
| Faixa dos tronos | 19,5 a 22,8 % | motorista e cobrador (item 4) |
| Faixa do anúncio | 22,8 a 29 % | um anúncio por vez (item 5) |
| Busão | 29 a 64 % | capivaras + balões, plaquinhas e "CAIU!" colados nelas |
| Chat do TikTok | abaixo de 64 % | nada (a calçada continua sem texto) |

- **Por que 19,5 / 22,8 / 29 % e não 21 / 24 / 33 % (o ponto de partida do pedido):** medi onde ficam as cabeças. As fotinhos de quem está no **teto** ficam em ~35 % da altura; com a faixa do anúncio indo até 33 %, o balão de quem está no teto **nunca cabia**. Apertei o HUD (ranking numa linha por posição) e as faixas, mantendo a ordem e sem uma invadir a outra. Cabeças medidas: teto 42–44 %, dentro 51–55 %, fila do ponto 62–65 %.
- O anúncio é **encolhido** (variável `--esc`) para caber na faixa dele: nunca cobre o ranking, o letreiro nem o card de dica.
- Balões, plaquinhas e "CAIU!" **nunca** sobem para o HUD nem para as faixas e nunca descem para o chat. Se não couber, não aparece.

### 2. Nome só para quem importa naquele momento
- **Fixo na capivara:** passageiro comum não tem nome fixo (só a fotinho redonda, como antes). O **Rei** continua só com a **coroa**. Motorista e cobrador ganham só um **ícone pequeno** (volante vermelho / moeda roxa) desenhado na própria fotinho, sem nome nem tempo (0 desenho 3D a mais).
- **Temporário:** quem acabou de fazer algo (entrar, mandar presente, virar VIP, ganhar escudo) ganha a plaquinha com foto e nome **colada em cima da cabeça** (com setinha), por **3 s** (`placas.destaqueSeg` 5 → 3), no máximo **3** (`placas.maxGrandes` 5 → 3; motorista e cobrador não contam mais, porque não têm plaquinha). Se já tem 3, a mais antiga sai para a nova entrar (quem só embarcou não derruba uma plaquinha com menos de 1,5 s).
- **Quem comentou não ganha plaquinha separada:** o nome vai dentro do balão.
- **Fotinho redonda de todo mundo:** continua (nas capturas ela não poluiu). `CONFIG.tela.fotinhaTodos: false` deixa a fotinho só no motorista e no cobrador.

### 3. Balão de chat em cima da cabeça, curto e com limite
- **Acabou o balão no céu com fio longo** (Ajuste 17/11). O balão sai **logo acima da cabeça** de quem comentou, com a setinha apontando para ela, e com **foto + @nome + texto** numa peça só.
- A ponta da setinha fica a no máximo `CONFIG.tela.balaoDistMax` (**6 % da altura**) da cabeça. Se bater em outro texto, o balão pode deslizar para o lado (a setinha continua em cima da cabeça), desde que respeite os 6 %. Passando disso, **não aparece**.
- **Limites:** `baloes.max` 5 → **3**; duração `minSeg`/`maxSeg` **2,5 a 3,5 s**; no máximo 2 linhas; `baloes.maxCaracteres` 40 → **32**; letra `baloes.fonte` 3,7 → 3,5; o nome dentro do balão é cortado em 14 letras (`tela.balaoNomeMax`) para o comentário caber.
- **Não aparece** se a capivara estiver escondida: sem desenho (fila escondida), fora da tela ou com a fotinho quase toda tapada pela de outra capivara mais perto da câmera (fundo do busão, atrás de outras). Medido no busão cheio: 4 de 17 sentados contam como escondidos no celular; ninguém do teto.
- **Fila curta:** com 3 balões na tela, o comentário espera no máximo **1 s** (`tela.filaBalaoSeg`, até 3 na fila: `tela.filaBalaoMax`). Depois é **descartado**. O comentário continua valendo para o jogo (entra na fila do ponto, acorda do cochilo); só não vira balão.
- **Quem está na fila do ponto:** o balão sai em cima da capivara dela no ponto, se ela estiver desenhada. Se não estiver, não aparece.
- A procura de lugar (colisão) roda no máximo a cada **0,1 s** (`tela.colisaoSeg`); entre uma e outra, cada texto só acompanha a cabeça.
- Na prática: com o busão lotado e muita gente comentando ao mesmo tempo, **a maioria dos comentários não vira balão** (as cabeças ficam juntas e não cabem 3 balões lado a lado a menos de 6 % delas). Isso é o pedido ("se não couber, não aparece"); os números estão nos testes abaixo.

### 4. Faixa dos tronos (no lugar das plaquinhas "MOTORISTA 11 s" / "COBRADOR 33 s")
- Faixa fina, de largura inteira, logo abaixo do HUD: `[volante] Marileide [0:51]  ·  [moeda] Josué [perigo] +5s`.
- Cada metade: ícone, nome (cortado com "…" em `tela.faixaNomeMax` = 12 letras) e o **selo da 5.2** pequeno (escudinho "0:45" no minuto imune; ícone de perigo que avermelha a cada degrau e pisca no máximo). O "11 s" saiu (o tempo no trono continua no painel).
- Sem dono de verdade: **"Empresa"** em cinza.
- **Catraca:** o "+1s" dourado agora pisca **ao lado do nome do cobrador na faixa**, somando tudo do ponto ("+8s"); some 4 s depois da última passagem (`tela.somaCobradorSeg`). **Acabou o "−1s" vermelho voando** em cima de cada passageiro: o desconto continua acontecendo, só não é desenhado.
- Saíram do 3D as plaquinhas "MOTORISTA"/"COBRADOR" e o fio da plaquinha do trono.
- Os ícones da faixa e do card são desenhos próprios (SVG), não emoji: assim aparecem iguais em qualquer celular.

### 5. Um anúncio por vez, num lugar só
- O `#anuncio` fica **sempre na faixa do anúncio**, na mesma posição.
- **Grande** (`tela.anuncioGrandeSeg` 2,5 s): novo motorista/cobrador, "DERRUBOU O MOTORISTA/COBRADOR/REI", novo Rei e "O REI VOLTOU!", "ABAIXA!" (continua passando na frente de tudo) e **presente grande**. Ficou com foto + duas linhas; a 3ª linha ("tirou @fulano do volante", "@fulano voou!") saiu para caber na faixa.
- **Compacto, uma linha** (`tela.anuncioCompactoSeg` 1,8 s): caos mandado por presente, com crédito para quem pagou ("**@Capivânia → BURACO!**"), "PRÓXIMO PONTO!", marcos ("30 minutos de busão!") e avisos do painel.
- **Presente grande** = acima de `tela.presenteGrandeMoedas` (**99**). As moedas vêm da LIVE; quando não vêm, uso a tabela `tela.moedasEfeito` (a mesma do README). **Decisão minha (confirme):** com 99, **Confete (PAGODE, 100 moedas), Arma de dinheiro (PNEU), Cisne (GIGANTE) e Galáxia (VIP) ganham o anúncio grande**; GG, Sorvete, Coração com dedo, Rosquinha, Guindaste e Chapéu ficam compactos. O seu exemplo do pedido usa PAGODE compacto: se preferir assim, ponha `presenteGrandeMoedas: 100`. Presente acima de 99 que não é caos nem trono (ex.: um Leão) ganha "@fulano mandou Leão!" grande.
- **Fila:** no máximo **3** esperando (`tela.filaAnuncioMax`); os grandes passam na frente dos compactos; compactos do mesmo caos juntam ("**@A, @B +3 → LOMBADA!**"; uma pessoa só com combo: "→ FREADA! (×5)"); trocas do mesmo trono continuam juntando ("+2 trocas"). Passou de 3, sai o compacto mais novo (só grandes: o grande mais novo). **O efeito do jogo acontece igual.**
- Para isso o caos agora guarda **todos os nomes** de quem mandou o mesmo efeito (antes guardava só o último).

### 6. Efeitos pequenos colados na capivara
- **"CAIU!"**: balãozinho vermelho pequeno no **ponto da queda** (fica parado ali enquanto a capivara sai rolando), 1,2 s (`tela.efeitoSeg`). No máximo 3 (`tela.maxCaiu`); caindo mais, aparece **um só "CAIU! ×N" em cima do busão**. Capivara caiu fora da tela: nada. **Acabou a etiqueta "@fulano CAIU!"** que seguia a capivara até a borda.
- **"SEGURA AÍ!"** (cobrador segurou) agora fica **colado em quem foi salvo** (antes saía do cobrador). **"SEGUROU!"** (escudo) e **"Zzz… perdi o ponto!"** viraram o mesmo balãozinho pequeno. Todos contam no limite.
- O "Zzz" na fotinho de quem vai cochilar continua.
- **Falas automáticas:** **não existiam no código** (as frases "cabe mais um?", "cadê o cobrador" eram só da LIVE falsa do simulador, que imita comentários de verdade e por isso continuam como comentários). Criei o ponto pedido: a **capivara da empresa** (volante ou catraca vagos) fala de vez em quando (a cada 25–45 s, `tela.falasAutoSeg`), **no máximo 1 por vez e só sem nenhum balão de chat de verdade na tela**; comentário de verdade tira a fala na hora. `tela.falasAuto: true`, com botão **Falas automáticas** no painel (fica salvo).

### 7. HUD sem repetição
- **Letreiro:** "LINHA 067" e embaixo "**37 a bordo · 3 na fila**". Saiu a linha "Rei: @fulano 00:42" (o Rei já é o 1º do ranking).
- **Ranking:** top 3 com a coroa no 1º, agora **uma linha por posição** (posição · nome · tempo), para caber no HUD. Nada passa por cima dele.
- **Card da direita:** `#cta` (COMENTE), `#rodizio` (presente → o que faz) e o novo `#dicaTronos` (Óculos de sol → vira MOTORISTA · Corações de mão → vira COBRADOR) viram **um card só**, que alterna a cada `tela.dicaSeg` (6 s) com transição suave e sempre do mesmo tamanho. O rodízio pula os presentes dos tronos (eles têm a face própria). Os botões "Rodízio de presentes" do painel e `CONFIG.aviso.ligado` continuam valendo (a face some do revezamento).
- **Saíram:** o card "Próximo ponto: N esperando" (`#proxPonto`) e a plaquinha "+N esperando" perto do ponto. O número está no letreiro.

### 8. Orçamento de texto
Na zona do busão, no máximo **6 textos** (`tela.maxTextosBusao`): até **3 balões de chat** + até **3** entre plaquinhas de nome, "CAIU!" e "SEGURA AÍ!" (`tela.maxPlaquinhasEfeitos`). Ícones (coroa, volante, moeda, Zzz) não contam. Estourou: sai o mais antigo, menos o balão de chat (que só sai por tempo). Os textos saem na hora (sem animação de saída), para a conta nunca passar do limite.

### O que saiu da tela e para onde foi
| Antes (5.2) | Agora (5.3) |
|---|---|
| Balão no céu com fio longo até a cabeça | balão logo acima da cabeça (foto + @nome + texto), no máximo 3 |
| Plaquinha grande de quem comentou | nome dentro do balão |
| Plaquinhas "MOTORISTA 11 s 0:51" / "COBRADOR 33 s" no meio do busão + fio | faixa dos tronos (nome + selo) e ícone pequeno na fotinho |
| "−1s" voando de cada passageiro | não é desenhado (o desconto continua) |
| "+1s" ao lado da plaquinha do cobrador | "+Ns" piscando ao lado do nome dele na faixa |
| Anúncio grande cobrindo o ranking | faixa do anúncio; caos em uma linha com crédito |
| Etiqueta "@fulano CAIU!" indo até a borda | "CAIU!" pequeno no ponto da queda; muitos = "CAIU! ×N" |
| "SEGURA AÍ!" saindo do cobrador | colado em quem foi salvo |
| "Rei: @fulano" no letreiro | só no ranking (1º, com coroa) |
| "Próximo ponto: N esperando" e "+N esperando" | "N na fila" no letreiro |
| COMENTE + rodízio (dois cards) | um card só, alternando 3 mensagens |

### Números novos (`CONFIG.tela`) e o que mudou
| Onde | Padrão | O que é |
|---|---|---|
| `tela.zonas` | 0,09 / 0,195 / 0,228 / 0,29 / 0,64 | topo livre / fim do HUD / fim da faixa dos tronos / fim da faixa do anúncio / fim do busão |
| `tela.balaoDistMax` | 0,06 | ponta da setinha até a cabeça (fração da altura) |
| `tela.filaBalaoSeg` / `filaBalaoMax` | 1 / 3 | fila curta dos balões |
| `tela.colisaoSeg` | 0,1 | procura de lugar no máximo a cada 0,1 s |
| `tela.efeitoSeg` | 1,2 | "CAIU!", "SEGURA AÍ!", "SEGUROU!", "Zzz…" |
| `tela.maxCaiu` | 3 | mais que isso vira "CAIU! ×N" |
| `tela.maxTextosBusao` / `maxPlaquinhasEfeitos` | 6 / 3 | orçamento de texto |
| `tela.falasAuto` / `falasAutoSeg` | true / 25–45 s | falas da capivara da empresa (painel, salvo) |
| `tela.fotinhaTodos` | true | fotinho de todo mundo |
| `tela.faixaNomeMax` / `balaoNomeMax` | 12 / 14 | nome cortado com "…" |
| `tela.somaCobradorSeg` | 4 | "+Ns" na faixa |
| `tela.filaAnuncioMax` | 3 | anúncios esperando |
| `tela.anuncioGrandeSeg` / `anuncioCompactoSeg` | 2,5 / 1,8 | duração dos dois tamanhos |
| `tela.presenteGrandeMoedas` | 99 | acima disso, anúncio grande |
| `tela.moedasEfeito` | tabela | moedas de cada caos quando a LIVE não manda |
| `tela.dicaSeg` | 6 | troca do card da direita |
| `placas.destaqueSeg` | 5 → **3** | |
| `placas.maxGrandes` | 5 → **3** | motorista e cobrador não contam mais |
| `baloes.max` | 5 → **3** | |
| `baloes.maxCaracteres` | 40 → **32** | |
| `baloes.minSeg` / `maxSeg` | 3 / 5 → **2,5 / 3,5** | |
| `baloes.fonte` | 3,7 → **3,5** | balão mais estreito, colado na cabeça |

### Painel
- Botão novo **Falas automáticas** (seção Modo).
- Medidor: "Balões N/3 (esperando N) · plaquinhas · CAIU/SEGURA · sem espaço · fila descartou" e "Anúncios: grandes · compactos · juntados · descartados".

### Desempenho (Chrome sem tela, desenho pelo processador; mesma lotação e mesmos 5 comentários/s nos dois arquivos)
| | Antes (5.2) | Depois (5.3) |
|---|---|---|
| Desenhos 3D, celular 41 dentro + 10 teto | 34 | **34** |
| Desenhos 3D, PC 99 dentro + 10 teto | 34 | **34** |
| Elementos HTML da página (sem painel) | 540 | **319** |
| Lógica por quadro, celular, 60 s | 1,91 ms | **0,81 ms** |
| Lógica por quadro, PC, 60 s | 2,38 ms | **1,15 ms** |
| Enxurrada 20 mil mensagens a 50/s (celular / PC) | 1,44 / 1,15 ms (medido na 5.2) | 1,43 / 1,05 ms |
| 2 horas aceleradas (celular / PC) | — | 0,83 / 0,92 ms |

A medida de antes/depois rodou ao mesmo tempo que os testes de carga (os números absolutos são mais altos que os da 5.2 por isso); a comparação é justa porque os dois arquivos rodaram nas mesmas condições. Tudo é HTML reaproveitado: nada é criado por evento. Quadros por segundo reais só no aparelho (Fase 6).

### Testes da Fase 5.3 (Chrome sem tela, Three.js r170 de um clone esparso, `passoLogica`, LIVE falsa `?fonte=teste&simulador=0`, `Math.random` com semente fixa)
**Resultado: 42 + 42 (Fase 5.3) + 62 + 62 (Fase 5.2) + 43 + 43 (regressão 5.1) + 8 × 4 (carga) = 326 verificações, 0 falhas**, nos dois modos.

Medindo os elementos na tela (`getBoundingClientRect`) a cada 0,5 s de jogo:
- **Pior momento** (busão lotado, tronos ocupados, **20 comentários/s por 90 s**, um caos a cada 2 s e troca de trono a cada 10 s), 180 amostras: nada nos 9 % de cima nem abaixo de 64 %; anúncio sempre na faixa dele, sem encostar no ranking, letreiro, card ou faixa dos tronos; nenhum balão/plaquinha no HUD ou nas faixas; **toda ponta a no máximo 6 % da cabeça** (pior 5,9 %); nunca texto de capivara sem desenho ou fora da tela; no máximo 3 balões, 3 plaquinhas+efeitos, 6 textos, fila de anúncio 3; nenhum texto cortado na borda; nenhum texto em cima de outro. Desses 1.800 comentários, cerca de 100 viraram balão (o resto não coube perto da cabeça, estava escondido ou esperou mais de 1 s), como pede a regra; todos valeram no jogo.
- **Enxurrada de comentários** (**50/s por 2 min** pelo adaptador da LIVE, com presentes): os mesmos limites seguram; 5.403 comentários chegaram ao jogo e a fila do ponto encheu (3 → 200); **nenhum presente perdido** (597 = 597).
- **Faixa dos tronos:** "Empresa" em cinza nos dois quando vago; dono certo com o nome cortado em 12 letras; minuto imune com o escudinho "0:45"; depois perigo (degrau certo, máximo piscando); nenhuma plaquinha "MOTORISTA/COBRADOR" no busão; **"+8s" na faixa = 8 passagens cobradas na catraca**; nenhum "−1s" desenhado; o "+Ns" some depois de 4 s; derrubado, volta "Empresa".
- **Anúncios:** 5 lombadas de 5 pessoas = 1 anúncio compacto "@A, @B +3 → LOMBADA!"; o novo motorista (grande) passa na frente de 2 compactos; 40 caos em 2 s: a fila nunca passou de 3 e **os 43 caos aconteceram**; tamanhos certos (trono, derrubou, Rei, ABAIXA!, presente acima de 99 = grande; o resto compacto).
- **Quedas, HUD e card:** 6 quedas juntas = no máximo 3 "CAIU!" e um "CAIU! ×6"; nenhuma etiqueta seguindo a capivara; letreiro "N a bordo · M na fila" sem o Rei; sem "Próximo ponto" e sem "+N esperando"; o card alterna as 3 mensagens sempre do mesmo tamanho.
- **Falas automáticas:** no máximo 1; comentário de verdade tira a fala; com balão de verdade na tela não aparece; desligada no painel, nenhuma.
- **Carga** (as mesmas da 5.2, mais limites de texto e contagem de elementos HTML):
  | | Celular | PC |
  |---|---|---|
  | Enxurrada: presentes mandados = adaptador = barramento | 9.842 = 9.842 = 9.842 | 9.797 = 9.797 = 9.797 |
  | Enxurrada: caos mandados = recebidos = executados | 1.067 | 1.060 |
  | 2 h: caos recebidos = executados / passagens / "SEGURA AÍ!" / perigos | 2.483 / 784 / 203 / 36 | 2.486 / 728 / 198 / 34 |
  | Limites de texto (balões / plaquinhas+efeitos / textos / fila de anúncio) | ≤ 3 / 3 / 6 / 3 | ≤ 3 / 3 / 6 / 3 |
  | Elementos HTML no fim ≤ no começo | sim | sim |
  | Problemas | 0 | 0 |
- **Memória nas 2 horas:** 4,2 → 9,1 (10 min) → 19,0 (1 h) → 29,1 MB (2 h) no celular, 4,4 → 29,1 MB no PC. Rodei o **mesmo teste no arquivo da 5.2**: 4,3 → 9,5 → 20,2 → **31,4 MB**. O crescimento vem do teste (o placar acumula ~5.000 pessoas novas da LIVE falsa, o limite do `placar.maxPessoas`), não desta fase, que gasta um pouco menos. Os números menores do README da 5.2 eram de um gerador de mensagens mais leve.
- **Capturas** (412×915 e 1080×1920), **ANTES (5.2) ao lado do DEPOIS (5.3) na mesma situação**: `folha-celular-1/2/3.png` e `folha-pc-1/2/3.png` (pior momento com a câmera da troca de motorista; o mesmo no plano geral; trono no minuto imune; trono no perigo máximo; freada ×5 com "CAIU! ×6"; as 3 mensagens do card; painel).

- **Uma vez só:** numa das 6 rodadas da `fase52-test.mjs pc`, um galho/viaduto passou sem derrubar ninguém (a regra pede 1 ou 2). Rodei de novo e passou (62 de 62); não consegui repetir. Como a 5.3 não mexe nos perigos, deixo anotado para olhar na Fase 6 se algum "ABAIXA!" passar sem ninguém cair.

### O que mudou nos testes antigos (e por quê)
- `fase52-test.mjs`: **"SEGURA AÍ!"** agora é procurado colado em **quem foi salvo** (antes, no cobrador). Pede ≥ 9 de 10, porque se a capivara salva estiver fora da tela o balãozinho não aparece (regra desta fase). Medido: 10 de 10. O resto é igual.
- `regressao51-test.mjs`: igual.
- `carga-test.mjs`: soma os limites de texto e a contagem de elementos HTML.
- `capturas.mjs` (5.2) e `quedas-balao-test.mjs` (Ajuste 17/11) mediam o "−1s/+1s", o balão no céu e a etiqueta "@nick / CAIU!", que saíram da tela. Ficaram no projeto como histórico; os testes que valem agora são `fase53-test.mjs` e `capturas53.mjs`.
- `comum.mjs` aceita `ARQ=antes52.html` para rodar o mesmo teste no arquivo da 5.2 (capturas antes/depois e desempenho).

### A conferir na LIVE (Fase 6)
- Se os balões aparecem o bastante. Com o busão cheio e muita gente comentando, a maioria não cabe. Se ficar pouco, dá para subir `tela.balaoDistMax` (ex.: 0,08) ou baixar `baloes.fonte`.
- Se o anúncio grande, encolhido para a faixa de 6 %, dá para ler no celular. Se não, dá para descer `tela.zonas.anuncioFim` para 0,30 (o balão de quem está no teto fica mais apertado).
- Se `presenteGrandeMoedas` 99 está bom (PAGODE grande ou compacto).

---

## Fase 5.2 · as regras novas, simples (continuam valendo)

### 1. Trono é seguro só no primeiro minuto (Motorista e Cobrador)
Antes, quem estava no volante ou na catraca **nunca** caía. Agora a chance de cair vai subindo, contando a partir do momento em que a pessoa **sentou** no trono:

| Tempo no trono | Peso para ser sorteado num caos |
|---|---|
| 0 a 1 min | 0 (imune) |
| 1 a 2 min | 0,1 |
| 2 a 3 min | 0,2 |
| 3 a 4 min | 0,3 |
| 4 a 5 min | 0,4 |
| a partir de 5 min | 0,5 (igual a qualquer passageiro sentado) |

(Mesma escala dos lugares: em pé = 1, sentado = 0,5, teto = 3.)

- Vale para os caos que **derrubam**: freada, buraco, janela e fiscal. A **lombada** e os **perigos do caminho** só pegam o **teto**, então nunca pegam o trono.
- **Decisão minha (confirme):** a freada pega "quem está em pé" **e os tronos** (motorista e cobrador ficam lá na frente e levam o tranco). Sem isso, o botão "Derrubar o motorista" do simulador (que manda uma freada) nunca funcionaria.
- O **empurrão** do presente de entrar continua **nunca** pegando o trono.
- **Escudo não vale no trono:** fica guardado (não gasta e não segura). O aro azul some enquanto a pessoa está no trono e volta quando ela sai.
- **Quando o dono do trono cai:** anúncio **"@fulano DERRUBOU O MOTORISTA!"** (ou **COBRADOR**), com o @ de quem mandou o caos; a capivara cai como as outras e sai de cena; o trono passa para o **próximo da fila do trono** (se tiver) ou o **funcionário da empresa** senta; a proteção de 3 s depois de uma troca continua. Observação: a fila do trono só existe nos 3 s de proteção depois de uma troca; na prática quem senta depois de uma derrubada é quase sempre o funcionário da empresa.
- **Selo do trono:** no minuto imune, um **escudinho azul com a contagem** ("0:45"); depois, um **triângulo de perigo** que fica mais vermelho a cada degrau (amarelo → laranja → vermelho → vermelho escuro, que pisca de leve no máximo). **Desde a 5.3 o selo fica na faixa dos tronos.**
- Ser derrubado do trono **não** acorda do cochilo (não é interação), como as outras quedas.
- Os perigos do caminho nunca pegam o trono, então o anúncio "O GALHO DERRUBOU O COBRADOR!" fica pronto no código, mas não tem como acontecer com as regras de hoje. "O GALHO DERRUBOU O REI!" acontece (se o rei estiver no teto).

### 2. Tempo no trono volta a contar no placar
- No **minuto imune** conta **metade**; depois conta **inteiro**. Medido: 60 s imune = 30 s no placar; os 60 s seguintes = 60 s.
- Quem sai do trono continua somando normalmente. O Rei que vira motorista ou cobrador sai do Banco do Rei e vai para o trono, com a coroa.

### 3. Cobrador cobra passagem (rouba segundos)
- Só com **cobrador de verdade** (pessoa da LIVE). Com o funcionário da empresa, ninguém paga.
- Cada capivara que **passa pela catraca** paga **1 s**: o cobrador **ganha +1 s** e quem passou **perde 1 s** (com 0 s não perde nada e nunca fica negativo; o cobrador ganha mesmo assim).
- Passa pela catraca quem entra **pela porta** e vai para **dentro**: a fila subindo no ponto e quem sobe na hora com presente. **Não** passam: quem vai para o **teto**, o **Banco do Rei**, o motorista e o próprio cobrador.
- **Limite: 8 por ponto.** O resto entra **de graça**. A conta zera quando o busão **sai** do ponto, então quem sobe com o busão andando soma no limite do **próximo** ponto.
- Na tela (desde a 5.3): **"+Ns" dourado piscando ao lado do nome do cobrador na faixa dos tronos**, somando o ponto; o "−1s" de cada passageiro não é mais desenhado. "Plim" de moeda no máximo 1 a cada 0,25 s.
- Painel (seção Tronos): passagens cobradas, segundos roubados, de graça e quantos já passaram neste ponto.

### 4. Cobrador "segura" 1 pessoa nos caos
- O caos **sempre acontece** (anúncio, tremida, animação).
- Com cobrador de verdade sentado, **1 a cada 3 caos que derrubam gente**, uma das vítimas sorteadas **balança e não cai**: balãozinho roxo **"SEGURA AÍ!"** (desde a 5.3, colado em quem foi salvo) e um som curto.
- Não salva o próprio cobrador (se ele for sorteado, cai normal) e não gasta escudo de ninguém.
- O contador é **do trono** (troca de cobrador não zera) e só anda com cobrador de verdade. Os perigos do caminho não contam.

### 5. Teto pequeno e perigoso
- **No máximo 10 no teto**, no celular e no PC (eram 20 / 40). As 10 vagas são um zigue-zague de ponta a ponta, todas visíveis.
- Teto pesa **3×** para cair (era 2×).
- **Lombada:** continua derrubando só o teto. **Freada:** além de quem está em pé (e os tronos), joga **1 do teto** para a frente; o total respeita o máximo da freada (6). **Buraco:** qualquer um, com o teto pesando 3×.
- **Perigos do caminho** (bloco `perigos`): **galho baixo** ou **viaduto** (sorteado), a cada **60 a 120 s**; o relógio só anda com o busão **rodando** e com **gente no teto**; **"ABAIXA!"** laranja + alarme 1,5 s antes (passa na frente dos outros anúncios); o obstáculo passa rente ao teto, todo mundo lá em cima se abaixa e **1 ou 2 caem**; o **escudo segura**; não conta como interação; sem autor; **um objeto 3D só**, reaproveitado; painel: liga/desliga **Perigos do caminho** (salvo), os tempos (salvos) e o botão **Galho agora**.
- O tempo no teto conta **1×** no placar (`CONFIG.teto.placar`).

### 6. Painel e simulador
- **Seção Tronos:** dono, tempo no trono, fase ("imune 0:45" ou "peso 0,3"), fila do trono e quantas vezes foi derrubado; do cobrador: passagens cobradas, segundos roubados, de graça e seguradas.
- **Seção Perigos do caminho:** liga/desliga, tempos, quantos apareceram (galho / viaduto), quantos derrubaram e quantas vezes o escudo segurou.
- **Simulador:** **Derrubar o motorista**, **Derrubar o cobrador**, **Galho agora** e **Ponto cheio**.

### Números da 5.2 (`CONFIG`)
| Onde | Padrão | O que é |
|---|---|---|
| `tronos.imuneSeg` | 60 | minuto imune no trono |
| `tronos.pesoPasso` | 0,1 | quanto o peso sobe a cada degrau |
| `tronos.passoSeg` | 60 | tamanho do degrau (s) |
| `tronos.pesoMax` | 0,5 | o peso para de subir aqui |
| `tronos.placarImune` | 0,5 | tempo no trono, no minuto imune, conta metade |
| `tronos.placarDepois` | 1 | depois conta inteiro |
| `cobrador.passagemSeg` | 1 | quanto cada passagem custa / rende (s) |
| `cobrador.maxPorPonto` | 8 | passagens cobradas por ponto; o resto entra de graça |
| `cobrador.seguraCada` | 3 | 1 a cada 3 caos que derrubam, o cobrador segura 1 |
| `cobrador.plimSeg` | 0,25 | no máximo 1 "plim" a cada 0,25 s |
| `teto.placar` | 1 | quanto o tempo no teto vale no placar |
| `perigos.ligado` | true | liga/desliga (também no painel, salvo) |
| `perigos.minSeg` / `maxSeg` | 60 / 120 | intervalo sorteado entre um perigo e outro |
| `perigos.avisoSeg` | 1,5 | "ABAIXA!" antes do obstáculo chegar |
| `perigos.derrubaMin` / `derrubaMax` | 1 / 2 | quantos caem do teto |
| `caos.pesoLugar.teto` | 3 | era 2 |
| `caos.freadaTeto` | 1 | quantos do teto a freada joga para a frente |
| `modos.celular.teto` / `modos.pc.teto` | 10 / 10 | eram 20 / 40 |

---

## Ajuste 17/11 · câmera, balões e aviso "COMENTE"
**1. Câmera mais longe: o busão inteiro na tela** (continua). Plano geral `GERAL.pos (0.2, 6.2, 26)` mirando `(0.2, 3.05, 0.6)`; o busão ocupa ~81 % da largura. As tomadas de tremida só tremem; as especiais (close, trono, freada, fiscal, pneu, pagode, janela, gigante, VIP) continuam.

**2. Área segura do TikTok (`CONFIG.layout`)** (continua, agora detalhada nas zonas da 5.3): `topoLivre: 0.09`; nada abaixo de ~64 % da altura.

**3. Aviso "COMENTE e entre na fila do busão!"** (`CONFIG.aviso`): desde a 5.3 é uma das faces do card de dica.

**4. Balões no céu com fio até a cabeça:** **substituídos na 5.3** pelo balão colado em cima da cabeça (sem fio). A etiqueta "@nick / CAIU!" que acompanhava a queda também saiu (agora "CAIU!" pequeno no ponto da queda).

---

## A regra da Fase 5.1, simples (continua valendo)
1. **O busão para em pontos.** Ele roda 40 s, anuncia **"PRÓXIMO PONTO!"**, para 8 s no ponto e segue viagem.
2. **Comentou = vai para a fila do próximo ponto.** Quem comenta não aparece na rua na hora: espera o próximo ponto (o letreiro mostra **"N na fila"**). No ponto, a fila sobe pela catraca até lotar (dentro primeiro, depois o teto, que tem só 10 vagas); quem não coube espera o ponto seguinte.
3. **Mandou Rosa (ou qualquer presente sem efeito) = sobe NA HORA.** Com o busão lotado, ela **empurra alguém que está em pé**. Sem ninguém em pé para empurrar, vai para o teto; teto cheio, empurra alguém do teto.
4. **Ninguém desce por tempo.** Só sai do busão quem é **derrubado** (caos, empurrão ou galho/viaduto) ou quem **cochilou** (10 min sem comentar nem mandar presente: desce no próximo ponto com "Zzz… perdi o ponto!").
5. **Sabotagem derruba gente.** Quem cai some de cena; para voltar, comenta (fila) ou manda presente (sobe na hora).
6. **Escudo "Segura firme!"** (proposta: **Perfume**, 20 moedas): segura uma derrubada ou um empurrão. Acumula até 3. **No trono não vale.**
7. **Placar sem fim.** Cada pessoa soma o **tempo a bordo** (dentro, no teto e no trono). Cair não zera. O 1º é o **REI DO BUSÃO**: coroa na cabeça, **Banco do Rei** dourado, 1º lugar no ranking e volta triunfal.

---

## Tabela de presentes PROPOSTA (tudo "confirmar na LIVE")
| Presente (pt / en) | Moedas | O que faz | Anúncio (5.3) |
|---|---|---|---|
| Rosa / Rose | 1 | **sobe no busão na hora** (lotado: empurra alguém em pé) | — |
| **Perfume / Perfume** | 20 | **ganha ESCUDO** (e sobe na hora se não estiver a bordo) · *confirmar na LIVE* | — |
| GG | 1 | LOMBADA (derruba 1–3 do teto) | compacto |
| Sorvete / Ice Cream Cone | 1 | BUZINAÇO | compacto |
| Coração com dedo / Finger Heart | 5 | BURACO (derruba 1) | compacto |
| Rosquinha / Doughnut | 30 | FREADA (derruba 1–3 em pé ou do trono + 1 do teto) | compacto |
| Guindaste de papel / Paper Crane | 99 | JANELA (1 voa e cai) | compacto |
| Chapéu e bigode / Hat and Mustache | 99 | FISCAL (leva 2–4 embora) | compacto |
| Corações de mão / Hand Hearts | 100 | vira **COBRADOR** | grande |
| Confete / Confetti | 100 | PAGODE | grande (com 99) |
| Óculos de sol / Sunglasses | 199 | vira **MOTORISTA** | grande |
| Arma de dinheiro / Money Gun | 500 | PNEU furado | grande |
| Cisne / Swan | 699 | vira GIGANTE | grande |
| Galáxia / Galaxy | 1.000 | vira VIP | grande |

- Qualquer outro presente (sem efeito na tabela) **também sobe na hora** (acima de 99 moedas, ganha "@fulano mandou …!" grande).
- Quem manda presente de **caos não sobe** (sabota de fora). Para voltar ao jeito antigo: `CONFIG.caosTambemEmbarca = true`.
- Trocar nomes/ids continua igual: `CONFIG.presentes` (minúsculo, sem acento) e `CONFIG.presentesPorId` (vale antes do nome).

## O que cada caos derruba (`CONFIG.caos.derruba`)
| Caos | Quem cai |
|---|---|
| Lombada | 1–3 do **teto** |
| Freada | 1–3 de quem está **em pé** ou nos **tronos** **+ 1 do teto** |
| Buraco | 1 aleatório (tronos entram depois do minuto imune) |
| Janela | 1 voa pela janela e cai na calçada (tronos entram depois do minuto imune) |
| Fiscal | leva 2–4 **embora de vez** (tronos entram depois do minuto imune) |
| Pneu | ninguém (a rua para, ninguém embarca e o tempo até o ponto pausa) |
| Buzinaço, Pagode, Gigante, VIP | ninguém (diversão) |
| **Galho / viaduto (sem presente)** | 1–2 do **teto** |

- **O lugar importa** (`pesoLugar`): sentado = metade da chance, em pé = normal, **teto = o triplo**, trono = 0 no minuto imune e depois 0,1 … 0,5.
- **Combo** (×2, ×3…): +1 derrubado por nível (`porCombo`), com teto por efeito (`maxDerrubados`: lombada 6, freada 6 contando o do teto, buraco 4, janela 3, fiscal 8).
- Quem está VIP **nunca** cai. O escudo "gasta" a vez ("SEGUROU!"). No trono o escudo não vale.
- Ser vítima de caos **não** conta como interação: não acorda do cochilo.

## Números (`CONFIG.viagem` e `CONFIG.placar`)
| | Padrão | O que é |
|---|---|---|
| `viagem.rodandoSeg` | 40 | tempo rodando até o próximo ponto |
| `viagem.chegandoSeg` | 3 | desacelera até parar com a porta na frente do ponto |
| `viagem.paradoSeg` | 8 | porta aberta |
| `viagem.limiteFila` | 200 | gente esperando o próximo ponto; passou disso, fica só no placar |
| `viagem.visiveis` | 12 celular / 24 PC | capivaras desenhadas no ponto |
| `placar.cochiloMin` | 10 | sem interagir: desce no próximo ponto ("Zzz" aparece 1 min antes) |
| `placar.pontoMin` | 3 | quem está na fila e não interage vai embora |
| `placar.maxEscudos` | 3 | |
| `placar.marcosMin` | 5, 15, 30, 60 | anúncio compacto "@fulano: 30 minutos de busão!" |
| `placar.salvarSeg` | 10 | salva o placar no navegador |
| `placar.validadeHoras` | 10 | ao recarregar, o placar volta se for o mesmo @ e tiver menos de 10 h |
| `placar.maxPessoas` | 5.000 | passando disso, esquece quem tem menos tempo |
| `placar.anuncioReiMinSeg` | 60 | "@B passou @A…" só com o novo rei tendo 1 min de busão |

Os tempos do ciclo, do cochilo, da fila e dos perigos do caminho também se mudam no **painel** (ficam salvos).

---

## O que entrou na Fase 5.1 (resumo)
1. **Busão "piscando" preto na lateral** corrigido na causa.
2. **Pontos fixos** (bloco `viagem`): rodando 40 s → chegando ~3 s → parado 8 s → partindo.
3. **Presente de entrar = sobe na hora**; lotado empurra alguém em pé; sem ninguém em pé vai para o teto; teto cheio empurra do teto. Escudo segura o empurrão.
4. **Ninguém desce por tempo**; cochilo de 10 min.
5. **Sabotagem derruba gente**.
6. **Escudo** (aro azul na fotinho; "SEGUROU!").
7. **Placar "Rei do Busão"**: top 3, coroa, Banco do Rei, anúncios, salvo no navegador, Copiar/Zerar no painel.
8. **Painel**: Viagem, Placar, Simulador.

## O que só a LIVE real confirma
1. **Perfume** como escudo (nome e id reais: "Presentes recebidos").
2. Se 40/3/8 s é um bom ritmo com público de verdade (ajuste no painel).
3. Se o empurrão/derrubada fica divertido ou frustrante para o público 40+.
4. Nomes/ids dos presentes, fotos (CORS), sons e quadros por segundo no Edge 60 Fusion.
5. Se a área segura bate com o TikTok: nada importante embaixo do chat nem da barra de cima, e nada cortado dos lados.
6. **(5.2)** Se o trono ficou fácil ou difícil de derrubar (`CONFIG.tronos`), se o cobrador ganha tempo demais (`CONFIG.cobrador`), se o galho/viaduto aparece muito ou pouco (`CONFIG.perigos`) e se o teto com 10 ficou bom.
7. **(5.3)** Se a tela ficou fácil de entender, se aparecem balões suficientes e se o anúncio dá para ler (veja "A conferir na LIVE" da 5.3).

## Como usar (igual à Fase 5)
- **Celular:** GitHub Pages no Chrome, tela de começo (@, tik.tools, chave), **Começar**. Painel: 3 toques no canto de cima, à esquerda.
- **PC:** fonte de navegador 1080×1920 no OBS com TikFinity aberto (`?modo=pc&auto=1`). Painel: F2.
- Parâmetros: `?modo=`, `?auto=1`, `?fonte=`, `?usuario=`, `?publicar=0`, `?tikfinityUrl=`, `?tiktoolsUrl=` e `?simulador=0`.

## Técnica
- Arquivo único com os blocos `CONFIG`, `cena`, `busao`, `capivaras`, `rotulos`, `fotos`, `cameraDiretora`, `som`, `anuncios`, `tronos`, `caos`, `eventos`, `simulador`, `viagem`, `placar`, `cobranca`, `perigos`, `live`, `rodizio`, **`dica`, `faixaTronos`, `falasAuto` (5.3)**, `painel`, `inicio` e `avisoComente`.
- **5.3:**
  - `rotulos` reescrito: `balao(c, texto)` (fila curta de 1 s), `fala(c, texto)` (automática), `efeito(c, 'caiu'|'segura'|'segurou'|'sono', texto)`, `queda(c)`, `destacar(c, seg, baixa)`; procura de lugar a cada 0,1 s (`procurarLugares`) com a ponta da setinha presa a 6 % da cabeça (`--rx` no CSS = onde fica a setinha); `escondida(c)`; `itens()` (para os testes); `cont` (montados, sem espaço, escondida, fila descartou…). Saíram `flutuar`, os fios e o céu.
  - `anuncios`: `tamanho(item)` ('grande' | 'compacto'), `--esc` para caber na faixa, `medirZona()`, fila de 3 com descarte e nomes juntados (`item.nomes`). O caos guarda `nomes` e `moedas` em cada item; a LIVE passa as moedas do presente (`extra.moedas`).
  - `faixaTronos`: `atualizar`, `cobrou(valor)`, `texto(nome)`; `dica`: `atualizar`, `forcar(face)`; `falasAuto`: `atualizar`, `alternar`.
  - As zonas viram variáveis do CSS no `redimensionar` (`--hudAlt`, `--zTronos`, `--altTronos`, `--zAnuncio`).
  - `window.__busao` expõe também `faixaTronos`, `dica` e `falasAuto`.
- Testes (no projeto, em `busao-capivaras/testes/`): **`fase53-test.mjs [celular|pc]`**, `fase52-test.mjs [celular|pc]`, `regressao51-test.mjs [celular|pc]`, `carga-test.mjs enxurrada|2h [celular|pc]`, `desempenho-test.mjs` (5.2 × 5.3), **`capturas53.mjs [celular|pc]`** (antes × depois) (ajudantes: `comum.mjs`, `ajudantes-pagina.js`; `ARQ=antes52.html` roda no arquivo da 5.2).
