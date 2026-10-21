# PRD — Caça-Palavras (Jogo Casual de Adivinhação de Palavras em Grade)

| Campo | Valor |
|---|---|
| **Produto** | Caça-Palavras — jogo casual de procurar palavras em grade |
| **Versão do documento** | 1.0 |
| **Data** | 2026-10-21 |
| **Status** | Aprovado para desenvolvimento (escopo MVP definido) |
| **Responsável** | Francis (Product Owner) |
| **Plataforma alvo** | Web app responsivo (React + Vite + TypeScript) |
| **Idioma** | Português do Brasil (PT-BR) exclusivamente |
| **Público-alvo** | Jovens de 13 a 17 anos (secundário) |

---

## 1. Sumário

1. [Contexto e visão do produto](#1-contexto-e-visão-do-produto)
2. [Pesquisa de mercado e lições aprendidas](#2-pesquisa-de-mercado-e-lições-aprendidas)
3. [Objetivos, metas e não-objetivos](#3-objetivos-metas-e-não-objetivos)
4. [Público-alvo e personas](#4-público-alvo-e-personas)
5. [Escopo funcional do MVP](#5-escopo-funcional-do-mvp)
6. [Regras de jogo e mecânicas](#6-regras-de-jogo-e-mecânicas)
7. [Progressão, temas e dificuldade](#7-progressão-temas-e-dificuldade)
8. [Sistema de pontuação](#8-sistema-de-pontuação)
9. [Requisitos funcionais](#9-requisitos-funcionais)
10. [Requisitos não funcionais](#10-requisitos-não-funcionais)
11. [UX, telas e acessibilidade](#11-ux-telas-e-acessibilidade)
12. [Especificação técnica](#12-especificação-técnica)
13. [Persistência e modelo de dados](#13-persistência-e-modelo-de-dados)
14. [Dicionário de palavras](#14-dicionário-de-palavras)
15. [Analytics e métricas](#15-analytics-e-métricas)
16. [Riscos e mitigações](#16-riscos-e-mitigações)
17. [Roadmap e marcos](#17-roadmap-e-marcos)
18. [Fora de escopo](#18-fora-de-escopo)
19. [Anexos](#19-anexos)

---

## 1. Contexto e visão do produto

### 1.1 Resumo

**Caça-Palavras** é um jogo casual de navegador, em português do Brasil, no qual o jogador procura palavras escondidas dentro de uma grade de letras. Cada rodada é um tabuleiro gerado proceduralmente e validado, populado por palavras de uma categoria temática (animais, comida e bebida, esportes e jogos, coisas do dia a dia). O jogador localiza as palavras arrastando o dedo (ou o mouse) sobre as letras, acumulando pontos, combos e bônus de velocidade. A progressão é feita por uma escada infinita de níveis de dificuldade crescente, com um "Modo Difícil" que remove as dicas, amplia a grade e libera palavras invertidas.

O jogo é **100% anônimo** (sem cadastro, sem login), **sem anúncios** e roda inteiramente no navegador — todo o estado do jogador vive no `localStorage`. Não há servidor, backend ou conta de usuário no MVP.

### 1.2 Problema e oportunidade

A busca por "palavras em grade" é dominada por apps mobile de megacorporações (Wordscapes, Word Search Explorer, Words of Wonder), que exigem instalação, cadastro ou tracking agressivo e cobram por anúncios. Em PT-BR, a oferta é concentrada em jogos mobile com ads. Existe espaço para um jogo **web, leve, sem ads, sem cadastro**, que abre em menos de 2 segundos e entrega a mesma sensação de "achar a palavra" com acabamento visual colorido, pensado para o público de 13–17 anos que joga no intervalo entre aulas, no celular ou no computador.

### 1.3 Visão do produto

> Um caça-palavras em português que carrega instantaneamente, não pede cadastro, não mostra anúncio e dá vontade de fechar uma rodada e abrir outra.

### 1.4 Princípios de produto

1. **Zero atrito de entrada** — do load ao primeiro toque em menos de 10 segundos, sem tutorial modal obrigatório, sem login.
2. **Encontro vs. atrito** — cada interação tem resposta visual e sonora imediata (< 100 ms).
3. **Frustrar pouco** — nunca há estado de "fim de jogo" com punição; o jogador sempre pode avançar.
4. **Só progresso, nunca cobrança** — sem perder sequência, sem perder moedas, sem tela de derrota punitiva.
5. **Curto por natureza** — rodadas de 1 a 4 minutos, compatíveis com o intervalo de 5 a 10 minutos do público-alvo.

---

## 2. Pesquisa de mercado e lições aprendidas

### 2.1 O gênero e suas variantes

A "adivinhação de palavras" (Wordle e derivados) e o "caça-palavras em grade" são subgêneros distintos, ambos dentro da categoria casual de palavras. A pesquisa comparou os dois e **o caça-palavras em grade foi escolhido** por: baixa barreira cognitiva inicial, feedback contínuo (você sempre acha *alguma* palavra), ausência de estado de derrota absoluto, e compatibilidade direta com a leitura de listas de palavras PT-BR por tema.

| Referência | Modelo | Lição aplicada neste PRD |
|---|---|---|
| **Wordle** (Josh Wardle, 2021) | 6 tentativas, 5 letras, feedback por cor | O feedback de alto contraste e a "compartilhabilidade sem spoiler" são os padrões de UX que definiram o gênero |
| **TERMO** / **Letreco** (BR, 2022) | Adaptação PT-BR do Wordle | Prova de demanda do formato em português; dicionário PT-BR é a principal barreira técnica |
| **Wordscapes / Words of Wonder / Word Search Explorer** | Caça-palavras em grade mobile | Referência comercial do formato escolhido: sessão média de ~23 min, ~4 sessões/dia, leaderboard de milhões em anúncios |
| **Wordle Unlimited / Hello Wordl** | Clones com modo infinito + níveis | Confirma que "modo infinito + níveis" é a extensão natural de retenção quando não há modo diário |
| **Purdle** (jogo moderno, 2025) | Solver de entropia no cliente, paleta daltônica | É aceitável implementar "dica inteligente" 100% no cliente, sem servidor |

### 2.2 Dados de mercado e benchmarks

- **Retenção**: jogos de palavra têm D1 médio de **7,6%** (13% acima da média da plataforma de referência), e conversão de **71%** dos jogadores ficam mais de 1 minuto. A retenção é o gargalo real, não a monetização.
- **Tempo de sessão**: a média dos melhores-performing word games é de **14 minutos** por sessão.
- **Publicidade (referência, não escopo do MVP)**: banner em menus e entre puzzles (nunca durante a jogabilidade), *rewarded video* para dica/tentativa extra/proteger sequência — nunca vender a resposta.
- **Monetização**: manter o *daily* gratuito e monetizar o que cerca dele (arquivo, rodadas extras). Como este projeto **não tem modo diário no MVP**, a monetização futura fica fora de escopo até a validação do core loop.

### 2.3 Lições de design aplicadas

1. **Speed-to-fun**: colocar o tabuleiro na primeira tela; o botão "Como jogar" pode ser pulado. O jogador precisa estar digitando em menos de 30 segundos.
2. **Ensinar fazendo**: um mini-tutorial embutido no primeiro nível (a primeira palavra vem pré-destacada com um traço animado) em vez de um modal de texto.
3. **Feedback que guia**: cada palavra encontrada precisa de confirmação inequívoca (flash da palavra + som + contador + combo).
4. **Revelar a resposta**: sempre mostrar a palavra encontrada e as restantes ao fim da rodada — o aprendizado de vocabulário é o valor de retenção.
5. **Nada de manipulação**: sem temporizadores agressivos, sem pressão de streak, sem nags de rating.
6. **Persistência que respeita o tempo do jogador**: recarregar a página nunca deve custar uma rodada em andamento.
7. **Acessibilidade como recurso de alcance**: paleta de alto contraste e respeito a `prefers-reduced-motion` custam pouco e ampliam o público.

### 2.4 Por que "Modo Difícil" e não "Modo Diário"

Sem modo diário compartilhado, o gancho de retorno passa a ser a **escada de níveis** + o **recorde local** (decidido no levantamento com o PO). Isso reduz o *network effect* do formato Wordle, mas elimina a necessidade de gerar tabuleiros determinísticos por data e de sincronização de estado — o que mantém o MVP inteiro no cliente, sem backend. O Modo Difícil é o mecanismo de *dificuldade* e de **retenção de jogadores habilidosos** que se entediariam no nível 5.

---

## 3. Objetivos, metas e não-objetivos

### 3.1 Objetivos do produto

| # | Objetivo |
|---|---|
| O1 | Entregar um core loop de caça-palavras em PT-BR que seja jogável em menos de 10 segundos após o load |
| O2 | Garantir que 100% dos tabuleiros gerados sejam **completáveis** (toda palavra da lista é encontrável na grade) |
| O3 | Sustentar retenção com progressão infinita e dificuldade crescente sem exigir servidor |
| O4 | Criar uma identidade visual "colorida e divertida" reconhecível e compartilhável |
| O5 | Manter o custo de operação em **zero** (sem backend, sem CDN de assets pesados, sem ads) |

### 3.2 Metas de sucesso (KPIs)

**Primários (produto)**
- **Time to First Word (TTFW)** ≤ 30 s desde o load da página
- **Taxa de conclusão de nível** ≥ 85% na primeira tentativa de um dado nível
- **Palavras encontradas por sessão** ≥ 15 (média)
- **Sessões por usuário ativo** ≥ 3 por dia

**Secundários (saúde)**
- **Retenção D1** ≥ 15% (acima da média de 7,6% da categoria, por não depender de rede social diurna)
- **Retenção D7** ≥ 5%
- **Erro de runtime** < 0,5% das sessões
- **LCP** < 1,8 s em 4G mobile (bundle-alvo < 200 KB gzip)

### 3.3 Não-objetivos (o que este projeto NÃO é)

- Não é um jogo multiplayer, competitivo ou com ranking online.
- Não é uma plataforma de conteúdo autoral (sem criador de palavras do usuário no MVP).
- Não é um jogo mobile nativo nem está na App Store / Play Store.
- Não é um jogo monetizado no MVP (sem ads, sem IAP, sem moedas).
- Não é um jogo de adivinhação estilo Wordle (grade de tentativas com feedback por cor).

---

## 4. Público-alvo e personas

### 4.1 Público primário — Jovens 13–17 anos

- **Contexto de uso**: celular, em intervalos curtos (15 min), compartilham tela com amigos; ou computador, em sessão mais longa.
- **Motivo de uso**: desafio curto, progressão visível, comparação de recordes no grupo de amigos.
- **Frustrações a evitar**: cadastro, anúncio no meio da jogabilidade, tabela de letras impossível, erro que apaga progresso.
- **Referência visual**: colorido, divertido, alto contraste, com animação de descoberta.

### 4.2 Personas

**Ana, 14 — "A do intervalo"**
> Joga no celular no recreio. Quer terminar 3 níveis antes da aula voltar. Se o jogo pedir cadastro, ela desiste em 5 segundos. Quer ver o número do nível crescendo e um recorde pessoal.

**Bruno, 16 — "O competitivo"**
> Joga no computador à noite, vai fundo nos níveis altos e no Modo Difícil. Quer bater o recorde dele mesmo (não dos outros — não há ranking online). Gosta de sentir o combo subir.

**Carla, 34 — "A casual"**
> Joga 5 minutos por dia no celular para desligar o cérebro. Precisa que o jogo seja legível, sem pressão de tempo, e que funcione bem em tela pequena. Também joga no Modo Difícil por opção, não por obrigação.

### 4.3 Jobs to be done

- *"Quando tenho 10 minutos livres, quero encontrar palavras numa grade que me prendam sem me estressar."*
- *"Quando termino um nível difícil, quero sentir que cheguei longe e querer tentar de novo."*
- *"Quando travo numa palavra, quero uma ajuda rápida que não estrague minha rodada."*

---

## 5. Escopo funcional do MVP

### 5.1 Visão geral das telas

```
[Home]
  ├─ Hero: logo + "Jogar" + "Continuar nível N" + "Recordes" + "Ajustes"
  ├─ Grade de categorias: [Animais] [Comida e bebida] [Esportes e jogos] [Coisas do dia a dia]
  ├─ Seletor de modo: [Normal ▾]  (Normal | Difícil)
  └─ "Misturado" (categoria aleatória a cada nível)

[Partida]  ← tela principal
  ├─ Header: nível, tema, cronômetro, pontos, combo, botão de dica, botão sair
  ├─ Grade N×N (letras)
  ├─ Barra de progresso: "X de Y palavras"
  └─ Lista de palavras do tema (encontradas em destaque, restantes em claro)

[Resultado da rodada]
  ├─ Palavras encontradas + tempo + pontos + combo máximo
  ├─ Botão principal: "Próximo nível"
  ├─ Secundários: "Jogar de novo", "Compartilhar" (desabilitado no MVP), "Voltar"
  └─ Recorde local superado → selo visual

[Ajustes]
  ├─ Modo padrão (Normal/Difícil)
  ├─ Sons (ligado/desligado)
  ├─ Movimento reduzido
  ├─ Alto contraste
  └─ Limpar progresso (com confirmação)
```

### 5.2 Modos de jogo

| Modo | Descrição | No MVP |
|---|---|---|
| **Normal** | Campanha infinita por tema. Palavras em horizontal, vertical e diagonal, sempre da esquerda→direita e de cima→baixo. Dicas disponíveis. | ✅ |
| **Difícil** | Mesma base, mas: sem dicas, grade maior e palavras invertidas (direita→esquerda, baixo→cima, diagonais invertidas) habilitadas. | ✅ |
| **Misturado** | Categoria sorteada por nível. Mantém a mesma progressão infinita. | ✅ |
| **Diário compartilhado** | Mesma grade para todos, muda por data. | ❌ Fora de escopo |

### 5.3 Escopo por prioridade (MoSCoW)

| ID | Item | Prioridade |
|---|---|---|
| S1 | Geração procedural de tabuleiro com validação | Must |
| S1 | Seleção por arrasto (swipe) + clique letra a letra | Must |
| S2 | Listas de palavras por tema (4 temas) | Must |
| S1 | Progressão infinita por nível | Must |
| S1 | Pontuação: base + bônus de tempo + combo | Must |
| S1 | Cronômetro crescente | Must |
| S2 | Dica "destacar letra" | Must |
| S2 | Modo Difícil (sem dicas + invertidas + grade maior) | Must |
| S1 | Recordes locais por tema | Must |
| S2 | Persistência de sessão em andamento | Must |
| S2 | Acessibilidade básica (alto contraste, reduced motion, foco visível, ARIA) | Must |
| S3 | Sons procedurais (Web Audio API) | Should |
| S3 | Animações de descoberta (flash, combo, confete na virada de milestone) | Should |
| S3 | Tutorial interativo de primeiro nível | Should |
| S4 | Card de resultado compartilhável | Could |
| S4 | Modo offline / PWA | Could |

---

## 6. Regras de jogo e mecânicas

### 6.1 Core loop

```
Home → Escolhe tema/modo → Gera tabuleiro (seed) → Procura e marca palavras
  → Acerta → feedback + pontos + combo → Repetir até achar todas
  → Rodada concluída → Tela de resultado → Próximo nível (dificuldade +1)
  → Volta ao loop
```

### 6.2 Regras de geração de tabuleiro

**R1.** A grade é quadrada, `N × N`, com `N` definido pela curva de dificuldade (§7.1).

**R2.** Cada tabuleiro contém uma lista de palavras do tema selecionado, filtradas por:
- comprimento entre 3 e 8 letras;
- sem nomes próprios, sem abreviações, sem termos ofensivos ou obscenos;
- sem duplicatas dentro da mesma rodada.

**R3.** Cada palavra é posicionada em **linha reta** (horizontal, vertical ou diagonal) a partir de uma célula válida, na ordem esquerda→direita / cima→baixo. No Modo Difícil, as 4 direções inversas também são permitidas.

**R4.** O gerador **maximiza o cruzamento** entre palavras (prefere ≥ 2 letras compartilhadas), o que produz grades mais densas, visualmente mais gratas e com menos letras "de enchimento".

**R5.** O gerador deve **evitar palavras acidentais**: após posicionadas as palavras-alvo, a grade é varrida em busca de sequências de ≥ 4 letras que formem palavras válidas do dicionário mas **não** estejam na lista alvo. Encontradas, são tratadas como **palavras extras** (bônus de +50 pontos cada) — ver §8.

**R6.** O gerador é **determinístico**: dada a mesma seed e a mesma lista de palavras, produz exatamente o mesmo tabuleiro. Isso permite reproduzir bugs, salvar/restaurar partidas e, futuramente, gerar links de desafio.

**R7.** O gerador tem **fallback em cascata**: se não conseguir posicionar todas as palavras após N tentativas (ver §12.3), ele reduz o número de palavras-alvo e tenta novamente; se ainda assim falhar, serve um tabuleiro gerado por uma seed alternativa pré-validada. **Um tabuleiro impossível nunca chega ao jogador.**

**R8.** As células restantes (não cobertas por palavras) são preenchidas com letras sorteadas da distribuição de frequências do português, excluindo `Q`, `K`, `W`, `X`, `Y` quando possível (rara vez aparecem em palavras PT-BR e poluem a leitura).

### 6.3 Regras de seleção

**R9.** O jogador pode selecionar de duas formas:
- **Arrasto (swipe):** pressiona uma célula e arrasta. A seleção segue a direção do vetor entre a última célula e a célula sob o ponteiro, normalizada para uma das 8 direções cardeais. Células fora da linha reta são ignoradas; a seleção "gruda" na linha.
- **Clique letra a letra:** clica na primeira e na última letra da palavra. Suportado em todas as plataformas e no teclado físico (navegação por setas + Enter).

**R10.** Ao soltar, o jogo compara a sequência selecionada (normalizada) com a lista de palavras:
- **Encontrada:** a palavra é marcada como descoberta, recebe flash visual, som e pontuação.
- **Não encontrada:** a seleção se dissolve em ~300 ms, sem penalidade de pontuação e **sem consumo de tentativa** (não há conceito de "tentativa errada").

**R11.** **Não há estado de derrota.** Uma rodada termina quando:
- todas as palavras do nível foram encontradas → **Concluída**; ou
- o jogador sai voluntariamente → estado salvo como *abandonada*, sem penalidade, retomável.

**R12.** O jogador pode **desistir** de uma palavra travada marcando-a como `pulada` (toque longo na lista de palavras), o que a remove da contagem do nível sem conceder pontos. No máximo 1 pulo por rodada no MVP.

### 6.4 Regras de palavras extras (§R5)

**R13.** Palavras acidentais encontradas **não** são computadas na progressão do nível (não contam para "X de Y"), mas concedem **+50 pontos** e são exibidas na tela de resultado com selo "extra".

### 6.5 Dicas

**R14.** A única dica do MVP é **"Destacar letra"**: o jogador aciona o botão de dica e o jogo acende **todas as ocorrências de uma letra sorteada** (não presente nas palavras já encontradas) na grade, com um efeito de pulso visual.

- A letra sorteada é escolhida entre as letras ainda não descobertas pelo jogador.
- Cada uso de dica aplica **penalidade de −50 pontos** na pontuação final da rodada.
- No **Modo Difícil**, o botão de dica não existe.
- O número de dicas por rodada é **ilimitado**, limitado apenas pela penalidade.

**R15.** A lista de palavras pendentes é exibida acima ou ao lado da grade. No Modo Normal, ela **não mostra a posição nem letras**; apenas o termo completo. O jogador pode tocar numa palavra pendente para ouvi-la/ler a definição curta (quando disponível) — sem custo.

### 6.6 Cronômetro e pontuação (§8 detalhado)

**R16.** O cronômetro conta **para cima** desde o início da rodada. Não há limite de tempo; não existe derrota por tempo. Ele serve para calcular bônus e é congelado ao fim da rodada.

---

## 7. Progressão, temas e dificuldade

### 7.1 Curva de dificuldade (Modo Normal)

| Faixa de nível | Grade | Palavras-alvo | Comprimento das palavras | Tempo-alvo |
|---|---|---|---|---|
| 1–5 | 8×8 | 6 | 4–7 | 90 s |
| 6–15 | 9×9 | 7 | 4–7 | 105 s |
| 16–30 | 10×10 | 8 | 3–7 | 120 s |
| 31–50 | 11×11 | 9 | 3–6 | 135 s |
| 51–100 | 11×11 | 10 | 3–6 | 150 s |
| 101+ | 12×12 | 11 | 3–6 | 165 s |

> O nível é calculado por fórmula contínua com teto, para que futuras mudanças não exijam tabelas manuais:
> `N = min(12, 8 + floor((nivel - 1) / 8))`, `qtdPalavras = min(12, 6 + floor((nivel - 1) / 5))`.

### 7.2 Modo Difícil

| Aspecto | Normal | Difícil |
|---|---|---|
| Dicas | Disponíveis | **Indisponíveis** |
| Direções | →, ↓, ↘ | →, ↓, ↘ **+** ←, ↑, ↖, ↗, ↙ |
| Tamanho da grade | `N` | `N + 1` (teto 13×13) |
| Palavras-alvo | `q` | `q + 1` (teto 14) |
| Progressão | Sequencial | Progressão **independente** do Normal (nível próprio por tema) |

> Justificativa: inverter as direções exige que o jogador leia a palavra "de trás para frente", o que inverte o padrão de varredura visual que o jogador já domina e aumenta a dificuldade cognitiva de forma qualitativa — mais forte do que simplesmente aumentar o tamanho da grade.

### 7.3 Temas (conteúdo)

| Tema | Exemplos de palavras | Volume alvo do dicionário |
|---|---|---|
| **Animais** | gato, cachorro, leão, baleia, tartaruga, camelo, gorila, abelha, polvo, cervo | 80–120 |
| **Comida e bebida** | pizza, banana, feijão, pão, bolo, sushi, ojeda, água, café, doces | 80–120 |
| **Esportes e jogos** | futebol, basquete, natação, surfer, xadrez, damas, vôlei, corrida, videogame, skate | 80–120 |
| **Coisas do dia a dia** | cadeira, janela, garrafa, chave, sapato, relógio, colher, mochila, tesoura | 100–150 |

Cada tema tem, além da lista completa, uma **lista de palavras "longas"** (≥ 6 letras) usada pelo gerador em níveis avançados.

### 7.4 Progressão e salvamento

- O progresso é salvo **por tema e por modo**, permitindo pular entre "Animais nível 12" e "Comida nível 3" sem perder nada.
- Ao entrar em um tema, o jogo retoma o nível mais alto alcançado, com a opção de "recomeçar do nível 1" (mantendo o recorde).
- Recordes são **locais**: maior pontuação por tema/modo, maior nível alcançado, maior combo.

---

## 8. Sistema de pontuação

### 8.1 Fórmula

```
pontosDaPalavra = 100 + (comprimento - 3) × 25
pontosDaPalavra ×= multiplicadorDeCombo
pontosDaPalavra += bonusDePalavraExtra   (se a palavra for acidental, ver §8.2)

pontuacaoDaRodada = Σ pontosDasPalavras
                   + bonusDeTempo
                   + bonusDeConclusao
                   − penalidadeDeDicas
```

### 8.2 Regras

**Bônus de tempo** — `bonusDeTempo = max(0, (tempoAlvo − tempoReal) × 10)`, limitado a `+1500`. Ou seja, terminar 15 s antes do tempo-alvo rende +150 pontos.

**Bônus de conclusão** — `+250` ao completar todas as palavras do nível.

**Combo** — o combo conta o número de **palavras consecutivas** encontradas com intervalo inferior a **10 s** entre elas.
- Multiplicador: `1x` (1ª), `1,5x` (2ª), `2x` (3ª), `3x` (4ª), `4x` (5ª+), **teto em 4x**.
- O combo **zera** quando o intervalo entre palavras supera 10 s, ou quando a dica é usada.
- Feedback visual: número de combo pulsa e sobe de escala a cada nível de multiplicador; som com tom ascendente.

**Penalidade de dica** — `−50` por uso da dica "destacar letra".

**Palavras extras** — `+50` cada, sem multiplicador de combo.

### 8.3 Sistema de letras bônus (decisão em aberto — ver §16)

Duas opções foram avaliadas para dar profundidade sem travar o jogador:

- **Opção A (escolhida para o MVP)**: não há letras bônus. A complexidade extra entra via combo, bônus de tempo e palavras extras acidentais.
- **Opção B (fase 2)**: 2 a 3 letras "douradas" na grade valem +100 e podem ser coletadas antes da palavra.

---

## 9. Requisitos funcionais

Prioridade: **M** = Must (MVP), **S** = Should (dentro do MVP se couber no prazo), **C** = Could.

### 9.1 Tabuleiro e geração

| ID | Requisito | Prio. |
|---|---|---|
| RF-001 | O sistema gera um tabuleiro `N×N` proceduralmente a partir da seed do nível | M |
| RF-002 | O gerador posiciona todas as palavras-alvo do tema com validação de encaixe | M |
| RF-003 | O gerador nunca entrega tabuleiro com palavra impossível; há fallback em cascata | M |
| RF-004 | A mesma seed + tema + modo reproduzem exatamente o mesmo tabuleiro | M |
| RF-005 | O gerador maximiza o cruzamento de palavras (≥ 2 letras compartilhadas quando possível) | M |
| RF-006 | O gerador detecta e sinaliza palavras acidentais válidas na grade | M |
| RF-007 | Modo Difícil habilita as 4 direções inversas e amplia a grade | M |
| RF-008 | A seed do nível é persistida; recarregar a página restaura a partida idêntica | M |

### 9.2 Jogabilidade

| ID | Requisito | Prio. |
|---|---|---|
| RF-010 | Arrastar sobre as células seleciona letras em linha reta, com "aderência" na direção | M |
| RF-011 | Clique na primeira e na última letra seleciona a palavra | M |
| RF-012 | Ao soltar, a seleção é comparada às palavras-alvo já normalizadas | M |
| RF-013 | Palavra encontrada: flash, som, remoção da pendência, pontos e combo | M |
| RF-014 | Seleção inválida se dissolve sem penalidade e sem consumir tentativa | M |
| RF-015 | O jogador pode pular no máximo 1 palavra por rodada, sem pontos | S |
| RF-016 | Cronômetro crescente visível, congelado ao fim da rodada | M |
| RF-017 | A lista de palavras pendentes é exibida; encontradas ficam marcadas | M |
| RF-018 | Não há estado de derrota; a rodada sempre pode ser concluída ou abandonada | M |

### 9.3 Dicas

| ID | Requisito | Prio. |
|---|---|---|
| RF-020 | Botão "Destacar letra" acende todas as ocorrências de uma letra não descoberta | M |
| RF-021 | Cada uso de dica aplica penalidade de −50 e zera o combo | M |
| RF-022 | O botão de dica não existe no Modo Difícil | M |
| RF-023 | A dica nunca revela a resposta da rodada | M |

### 9.4 Progressão, pontuação e recordes

| ID | Requisito | Prio. |
|---|---|---|
| RF-030 | Pontuação conforme §8 (base, comprimento, combo, tempo, conclusão, penalidade) | M |
| RF-031 | Multiplicador de combo exibido e animado | M |
| RF-032 | Avanço de nível automático após concluir a rodada | M |
| RF-033 | Progresso e recordes salvos por (tema, modo) | M |
| RF-034 | Tela de resultado mostra tempo, pontos, combo máximo, palavras e extras | M |
| RF-035 | Selo visual quando um recorde local é superado | S |
| RF-036 | Contraste alto e movimento reduzido podem ser alternados e persistidos | M |
| RF-037 | Sons podem ser desligados | S |
| RF-038 | Limpar todo o progresso (com confirmação explícita) | M |

### 9.5 Navegação

| ID | Requisito | Prio. |
|---|---|---|
| RF-040 | Home com escolha de tema, modo e "Continuar" | M |
| RF-041 | Botão "Como jogar" acessível em qualquer tela | M |
| RF-042 | Tutorial interativo no nível 1 (primeira palavra pré-demonstrada) | S |
| RF-043 | Sair da partida retoma de onde parou | M |
| RF-044 | Opção de reiniciar o nível atual do zero | M |

---

## 10. Requisitos não funcionais

| ID | Categoria | Requisito |
|---|---|---|
| RNF-001 | Performance | LCP < 1,8 s em 4G mobile; bundle JS < 200 KB gzip; sem carregamento bloqueante de dicionário (lazy-load + cache) |
| RNF-002 | Performance | Geração do tabuleiro < 120 ms no mobile de gama média; sem "trava" perceptível na troca de tela |
| RNF-003 | Performance | 60 fps durante o arraste na grade (transformações CSS, sem re-render global por célula) |
| RNF-004 | Responsividade | Funciona de 320 px (iPhone SE) a 2560 px; alvos de toque ≥ 44×44 px |
| RNF-005 | Offline | Sem chamadas de rede após o primeiro carregamento (o jogo funciona 100% client-side) |
| RNF-006 | Acessibilidade | `prefers-reduced-motion` respeitado; foco visível; contraste ≥ 4,5:1 no texto e ≥ 3:1 nos tiles de estado; ARIA labels em todos os controles; `aria-live` para feedback de palavra encontrada/erro |
| RNF-007 | Compatibilidade | Chrome/Edge/Firefox/Safari (últimas 2 versões) desktop e mobile; iOS Safari com `touch-action` configurado para evitar zoom/scroll durante o arraste |
| RNF-008 | Privacidade | Nenhum dado pessoal coletado; nenhum cookie de terceiros; nenhum tracker de ads |
| RNF-009 | Resiliência | Falha de `localStorage` (modo privado, cota cheia) não quebra o jogo — degrada para estado em memória |
| RNF-010 | Segurança | Nenhuma dependência de servidor; nenhum segredo no cliente; CSP restritiva |
| RNF-011 | Qualidade | Cobertura de testes unitários ≥ 80% nos módulos de `game-engine`, `generator` e `scoring` |
| RNF-012 | Acessibilidade de cor | Paleta de alto contraste com alternativas azul/laranja para daltonismo verde-vermelho |

---

## 11. UX, telas e acessibilidade

### 11.1 Layout da tela de partida (mobile-first)

```
┌──────────────────────────────┐
│ ← Nível 7 · Animais    ⚙ ⚑   │  header compacto
│ 02:14         1.240 pts  🔥3  │  cronômetro / pontos / combo
├──────────────────────────────┤
│  ▢ ▢ ▢ ▢ ▢ ▢ ▢ ▢            │
│  ▢ ▢ ▢ ▢ ▢ ▢ ▢ ▢            │  grade N×N
│  ▢ ▢ ▢ ▢ ▢ ▢ ▢ ▢            │  (altura = viewport - header)
│  ▢ ▢ ▢ ▢ ▢ ▢ ▢ ▢            │
├──────────────────────────────┤
│  GATO   CAVALO   ✦ PEIXE     │  lista de palavras
│  ✦ ABELHA      RANHO         │  ✦ = encontrada
├──────────────────────────────┤
│  4 / 7 palavras    [ 💡 Dica ]│  progresso + dica
└──────────────────────────────┘
```

**Regras de layout:**
- Em telas < 600 px, a lista de palavras fica **abaixo** da grade e rola horizontalmente.
- A grade **nunca** é cortada: se não couber com folga, reduz o tamanho da célula (mínimo 26 px) e a lista de palavras passa a ocupar uma faixa rolável.
- Em desktop (≥ 900 px), a lista de palavras fica em coluna lateral à direita, fixa.
- O botão de dica é *thumb-friendly* e **desaparece** (não fica desabilitado) no Modo Difícil.

### 11.2 Microinterações

| Evento | Resposta | Duração |
|---|---|---|
| Pressionar tecla/célula | escala 1.0 → 1.08 + som de toque | 80 ms |
| Selecionar linha | traço contínuo com "capas" arredondadas seguindo o cursor | 60 ms |
| Palavra encontrada | flash da palavra na lista + pulse no contador + som ascendente | 400 ms |
| Combo subindo | número do combo cresce com overshoot + tom mais agudo | 250 ms |
| Conclusão da rodada | confete + card de resultado | 600 ms |
| Palavra inválida | dissolução da seleção + tremor leve | 300 ms |

### 11.3 Acessibilidade

- **Alto contraste** — paleta alternativa azul/laranja/roxo no lugar de verde/amarelo/cinza (padrão para daltonismo de deuteranopia/protanopia).
- **Movimento reduzido** — desliga confete, overshoot e transições de flip; mantém apenas mudanças de cor e opacidade.
- **Teclado físico** — `A–Z` digita, `Backspace` apaga, `Enter` confirma, setas movem o cursor da grade, `Esc` abre ajustes.
- **Leitor de tela** — região `aria-live="polite"` anuncia "Palavra encontrada: GATO", "Palavra inválida", "Nível concluído".
- **Sem dependência exclusiva de cor** — cada estado de tile tem um glifo/ícone sutil em modo alto contraste.

### 11.4 Tom de voz

Curto, incentivador, nunca culpabilizador:
- "Boa! Achou **GATO**." / "Faltam só 3!" / "Nível 8 concluído. Bora para o 9?"
- **Nunca**: "Você perdeu", "Tempo esgotado", "Cadastre-se para continuar".

---

## 12. Especificação técnica

### 12.1 Stack

| Camada | Tecnologia | Justificativa |
|---|---|---|
| Framework | **React 19** + **TypeScript** (strict) | Ecossistema maduro, testabilidade, tipagem do motor de jogo |
| Build | **Vite 7** | Build instantâneo, HMR, bundle enxuto |
| Estilo | **Tailwind CSS v4** + CSS variables para temas | Tokens semânticos (cores de estado) trocáveis por tema/contraste |
| Estado | **Zustand** | Stores pequenas e desacopladas (`game`, `settings`, `progress`); sem boilerplate |
| Áudio | **Web Audio API** | Sons procedurais, zero arquivos de asset, ~0 KB de payload |
| Testes | **Vitest** + **Testing Library** + **Playwright** (e2e) | Feedback rápido em unit, cobertura de fluxos críticos em e2e |
| Deploy | **Vercel / Netlify** (estático) | Zero backend, zero custo em escala baixa, HTTPS nativo |
| Analytics | **PostHog** ou **Umami** (auto-hospedado) | Eventos de produto sem tracking de terceiros |

**Decisões deliberatemente NÃO tomadas no MVP:** sem framework SSR (o jogo é 100% client-side; SSR só adicionaria complexidade), sem banco de dados, sem service worker (offline é "Could"), sem biblioteca de animação pesada (CSS puro + Web Animations API).

### 12.2 Estrutura de pastas

```
src/
├── app/                    # Shell, rotas, providers
│   ├── App.tsx
│   └── routes.tsx
├── pages/
│   ├── HomePage.tsx
│   ├── GamePage.tsx
│   ├── ResultPage.tsx
│   └── SettingsPage.tsx
├── components/
│   ├── board/              # Board, Cell, SelectionOverlay
│   ├── wordlist/           # WordList, WordChip
│   ├── hud/                # Timer, Score, Combo, LevelBadge
│   ├── feedback/           # Toast, Confetti, TutorialHint
│   └── layout/             # AppShell, Header, Button, Modal
├── game/
│   ├── engine.ts           # máquina de estados da rodada
│   ├── generator.ts        # geração procedural + validação
│   ├── placement.ts        # posicionamento de palavras
│   ├── selection.ts        # seleção por arrasto/clique
│   ├── evaluate.ts         # normalização e comparação de palavras
│   ├── scoring.ts          # pontuação, combo, bônus
│   ├── difficulty.ts       # curva de nível e modos
│   └── rng.ts              # PRNG com seed (mulberry32)
├── data/
│   ├── themes/
│   │   ├── animais.ts
│   │   ├── comida.ts
│   │   ├── esportes.ts
│   │   └── dia-a-dia.ts
│   ├── dictionary.ts       # índice normalizado → palavra canônica
│   └── accents.ts          # normalização UTF-8/NFD + ç→c
├── store/
│   ├── gameStore.ts
│   ├── progressStore.ts
│   └── settingsStore.ts
├── hooks/
│   ├── usePointerSelection.ts
│   ├── useKeyboard.ts
│   ├── useTimer.ts
│   └── useCombo.ts
├── lib/
│   ├── storage.ts          # wrapper de localStorage com fallback em memória
│   ├── audio.ts            # síntese de sons via Web Audio
│   └── analytics.ts
└── styles/
    ├── tokens.css          # variáveis de cor/tamanho/espaçamento
    └── themes.css          # tema claro/escuro + alto contraste
```

### 12.3 Algoritmo de geração

**Passo 1 — PRNG com seed.**
```ts
// PRNG determinístico (mulberry32) — mesma seed ⇒ mesma sequência
export function createRng(seed: number) {
  let a = seed >>> 0;
  return () => {
    a = (a + 0x6d2b79f5) >>> 0;
    let t = Math.imul(a ^ (a >>> 15), 1 | a);
    t = (t + Math.imul(t ^ (t >>> 7), 61 | t)) ^ t;
    return ((t ^ (t >>> 14)) >>> 0) / 4294967296;
  };
}
```
A seed do nível é `hash(temaId + modo + nivel)` — estável entre sessões e dispositivos, e o mesmo tabuleiro pode ser reproduzido a partir de um único número.

**Passo 2 — Seleção das palavras-alvo.**
```ts
interface TargetWord {
  id: string;
  display: string;    // forma canônica com acentos, ex.: "caça"
  normalized: string; // forma normalizada, ex.: "caca"
  cells: Cell[];      // preenchido no posicionamento
}
```
Critérios: comprimento entre 3 e 8; pertencente ao tema; preferencialmente **letras distintas** (palavras com muitas repetidas são menos prazerosas de achar e poluem a grade).

**Passo 3 — Posicionamento (backtracking com heurística de cruzamento).**

Para cada palavra (ordenada da mais longa para a mais curta), gerar candidatos:
```
para cada célula (r, c) e cada direção d permitida:
    se a palavra couber inteiramente na grade:
        custo = penalidadeDeConflito(palavra, células)   // letras diferentes na mesma célula => conflito
        custo -= bonusDeCruzamento(nº de sobreposições com palavras já posicionadas)
        custo += jitterAleatório(0..2)                    // evita determinismo excessivo
    guarda o melhor candidato
```
- `bonusDeCruzamento` é crescente até 3 sobreposições (≈ 30 pontos), o que garante grades densas.
- Se nenhum candidato for viável para a palavra atual: **backtrack** — desfaz o último posicionamento e tenta outra combinação (limite de 200 backtracks). Se esgotar, aplica o fallback em cascata (§R7).

**Passo 4 — Preenchimento.**
Células livres recebem letras sorteadas por pesos:
```
a 12, e 11, o 9, i 7, s 7, r 6, n 5, m 5, d 4, t 4, c 4, u 4, l 4, p 3, v 2, g 2, h 2, b 2, q 1, f 1, ç 1, z 1, j 1, k 0, w 0, x 0, y 0
```
(Pesos derivados da frequência aproximada do português; `k`, `w`, `x`, `y` têm peso 0 — praticamente não aparecem em palavras PT-BR de 3–8 letras e só geram ruído.)

**Passo 5 — Varredura de palavras acidentais.**
Para cada linha, coluna e diagonal, extrair sequências de ≥ 4 letras e consultar o dicionário normalizado. Se uma sequência for palavra válida **e não** for alvo da rodada, registrar como **extra**.

**Passo 6 — Fallback em cascata.**
```
tentar(MAX_TENTATIVAS = 200) → falhou?
  → reduzir qtdPalavras em 1 e tentar novamente
    → ainda falhou?
      → reduzir qtdPalavras em mais 1 e tentar com seed alternativa
        → ainda falhou?
          → usar seed determinística de emergência pré-validada (tabuleiro fixo por faixa de nível)
```

**Passo 7 — Validação final (asserção).**
```ts
function validateBoard(board, targets, directions): boolean {
  return targets.every(t => findAllOccurrences(board, t.normalized, directions).length > 0);
}
```
Essa asserção roda em **desenvolvimento** (teste) e em produção como rede de segurança antes de entregar o tabuleiro ao jogador.

### 12.4 Algoritmo de seleção (arrasto)

```
ao iniciar o arraste:   start = célula sob o ponteiro; last = start; path = [start]
a cada pointermove:
    delta = célulaAtual - last
    dir   = normalizarPara8Direções(delta)   // escolhe a direção cardeal mais próxima
    if dir ≠ null:
        next = last + dir
        se next está dentro da grade e next ≠ last:
            se path não contém next:
                path.push(next); last = next
```
- A "aderência" (snap) é o que faz o arraste diagonal funcionar: o ponteiro raramente está exatamente na diagonal, mas a direção é capturada e as células são percorridas em linha reta.
- Alternância entre diagonal e horizontal é bloqueada no mesmo arraste (a direção é fixada no primeiro movimento significativo), evitando seleções "quebradas".
- Ao soltar: `normalized = path.map(c => board[c]).join('')`; compara com o mapa de palavras.

### 12.5 Modelo de dados (TypeScript)

```ts
type ThemeId = 'animais' | 'comida' | 'esportes' | 'dia-a-dia';
type Mode = 'normal' | 'dificil';

interface Cell { row: number; col: number; char: string; }

interface WordTarget {
  id: string;
  display: string;
  normalized: string;
  length: number;
  cells: Cell[];
  found: boolean;
  skipped: boolean;
  isExtra: boolean;
}

interface Board {
  size: number;
  seed: number;
  cells: string[];        // size * size, índice = row * size + col
  words: WordTarget[];
  extras: string[];       // palavras acidentais detectadas
}

type GameStatus = 'idle' | 'playing' | 'completed' | 'abandoned';

interface GameState {
  status: GameStatus;
  mode: Mode;
  themeId: ThemeId;
  level: number;
  board: Board | null;
  selection: Cell[];
  foundWordIds: string[];
  extraWordsFound: string[];
  startedAt: number | null;
  elapsedMs: number;
  score: number;
  combo: number;
  maxCombo: number;
  hintsUsed: number;
  hintLetter: string | null;
}
```

### 12.6 Estratégia de testes

| Camada | Foco | Ferramenta |
|---|---|---|
| Unit | `rng` (determinismo), `evaluate` (normalização/acentos), `scoring` (fórmula e combo), `difficulty` (curva), `placement` (cruzamento) | Vitest |
| Propriedade | Para 500 seeds aleatórias: **todo** tabuleiro gerado é completável e todas as palavras cabem | Vitest (property-based) |
| Integração | Fluxo de uma rodada completa: selecionar → achar → combo → pontuação → conclusão | Testing Library |
| E2E | Home → escolher tema → jogar → achar palavras → resultado → próximo nível; recarregar a página e retomar | Playwright |
| Snapshot | Render do board e do card de resultado | Vitest |

> O teste de propriedade é o mais importante do projeto: é a garantia estatística de que o **RF-003** (nunca um tabuleiro impossível) se sustenta.

### 12.7 Performance

- A grade é renderizada com **DOM + CSS Grid** (máx. 169 células) — sem canvas, o que preserva acessibilidade e permite `transform` barato no arraste.
- Durante o arraste, apenas a camada de seleção é reposicionada via `transform`; o estado do React não é atualizado a cada `pointermove` (usa-se `requestAnimationFrame` + refs).
- Os dicionários de tema são **lazy-loaded** por módulo dinâmico e cacheados; o bundle inicial fica pequeno.
- Sem imagens: toda a arte é SVG inline ou CSS. Sem fontes web customizadas além de uma variável de sistema.

---

## 13. Persistência e modelo de dados

### 13.1 Chaves no `localStorage`

| Chave | Conteúdo | TTL |
|---|---|---|
| `cacapalavras:v1:settings` | { mode, sound, reducedMotion, highContrast } | — |
| `cacapalavras:v1:progress` | { [themeId]: { [mode]: { level, bestScore, bestCombo, bestLevel } } } | — |
| `cacapalavras:v1:session` | { seed, themeId, mode, level, startedAt, elapsedMs, foundWordIds, score, combo } | Substituída a cada ação relevante |
| `cacapalavras:v1:seen` | { tutorialV1: boolean } | — |

**Regras:**
- Todas as chaves são versionadas (`v1`) para permitir migração futura.
- Falha de escrita (modo privado / cota) → degrada para store em memória, com aviso discreto na Home. O jogo **nunca quebra**.
- `cacapalavras:v1:session` é apagada ao concluir o nível.

### 13.2 Versionamento e migração

O objeto raiz de cada chave contém `version: 1`. Ao ler, um `migrate(raw)` converte formatos antigos; formato desconhecido → descarta e usa o default (fail-safe, nunca crash).

---

## 14. Dicionário de palavras

### 14.1 Fontes de dados

O dicionário PT-BR é a maior barreira técnica do projeto. Fontes públicas identificadas na pesquisa:

| Fonte | Licença | Uso |
|---|---|---|
| **VERO** — dicionário pt_BR do LibreOffice (Hunspell) | LGPL-3.0 / MPL-2.0 | Base bruta de palavras válidas |
| **IME-USP** — lista de dicionário (`pf/dicios`) | Domínio público | Validação cruzada e complemento de termos |
| **wordfreq** — frequências para português | CC BY-SA 4.0 | Filtragem por commonidade (Zipf ≥ 3 para listas de palavras-alvo) |
| Listas curadas da comunidade (ex.: `termo-dicionario`, ~6 mil palavras de 5 letras) | MIT / uso livre | Semeadura inicial e conferência |

> **Requisito de conformidade:** os dicionários derivados são obras modificadas do VERO e do wordfreq. O README do projeto deve incluir a atribuição e o texto das licenças aplicáveis (LGPL/MPL + CC BY-SA). A filtragem por frequência do wordfreq (CC BY-SA 4.0) é share-alike: se o dicionário derivado for distribuído, ele também deve ser CC BY-SA.

### 14.2 Regras de normalização (crítico)

O português tem acentos; o teclado do jogador não sempre tem. Decisão:

- **Exibição na grade:** letras sem acento, **preservando o `ç`** (que é uma letra distinta no PT-BR e que o jogador digita sem esforço). Ex.: a palavra `caça` aparece na grade como `caca`.
- **Normalização para dicionário:** `NFD → remover diacríticos → ç → c → minúsculas`. Assim, `caca` casa com `caça`, `cafe` com `café`.
- **Exibição da palavra encontrada:** usa a **forma canônica com acentos** (`CAÇA`) — é um momento de aprendizado e valoriza o vocabulário.
- A entrada de palavras do jogador (quando houver busca por palavra, fase 2) segue a mesma normalização.

### 14.3 Curadoria

- Cada tema tem uma lista revisada manualmente de **80–150 palavras**, todas com 3–8 letras.
- Excluídos: nomes próprios, topônimos, abreviações, xingamentos, termos médicos/sexuais explícitos, obsoletos e vírgulas-BR (só nomes de plantas e animais, por exemplo, não entram).
- Critério de inclusão: **frequência Zipf ≥ 3** (aparece pelo menos ~1 vez por milhão de tokens) — garante que o jogador o conheça.
- Palavras com letra repetida são usadas com moderação (≤ 20% da lista de um tema).

---

## 15. Analytics e métricas

### 15.1 Eventos

| Evento | Propriedades |
|---|---|
| `session_start` | `isFirstEver`, `themeId`, `mode` |
| `board_generated` | `level`, `size`, `wordCount`, `seed`, `generationMs`, `usedFallback` |
| `word_found` | `wordLength`, `isExtra`, `combo`, `elapsedSinceLast` |
| `hint_used` | `letter`, `hintsSoFar` |
| `level_completed` | `level`, `durationMs`, `score`, `maxCombo`, `hintsUsed` |
| `level_abandoned` | `level`, `progressPct` |
| `combo_milestone` | `combo` |
| `game_session_end` | `levelsPlayed`, `totalScore`, `durationMs` |
| `settings_changed` | `setting`, `value` |

### 15.2 KPIs monitorados
- TTFW (time to first word) — p50 e p90.
- Taxa de conclusão por faixa de nível (detecta níveis "impossíveis" ou chatos).
- Taxa de abandono por nível (sinal de dificuldade desalinhada).
- Uso de dicas por nível (sinal de gerar frustração).
- Taxa de fallback do gerador (deve ser < 1%).
- Sessões/dia por usuário e distribuição de níveis alcançados.

### 15.3 Privacidade
Eventos sem PII, sem fingerprinting, sem cookie de terceiros. Opt-out no link "Privacidade" do rodapé.

---

## 16. Riscos e mitigações

| # | Risco | Prob. | Impacto | Mitigação |
|---|---|---|---|---|
| R1 | **Tabuleiro impossível ou sem solução** | Média | Alto | Property-based tests (500 seeds), asserção `validateBoard` em runtime, fallback em cascata (§12.3 Passo 6) |
| R2 | **Palavras acidentais confusas** | Alta | Médio | Detecção de extras (§R5), bônus em vez de erro, revisão manual das listas por tema |
| R3 | **Dicionário PT-BR de qualidade heterogênea** (regionalismos, obsoletos) | Alta | Médio | Curadoria manual por tema + filtro Zipf ≥ 3 + revisão de listas; fontes cruzadas (VERO + IME-USP) |
| R4 | **Acentos/cedilha causam confusão** | Média | Médio | Normalização documentada (§14.2), `ç` preservado na grade, palavra canônica com acentos ao ser encontrada |
| R5 | **Retenção baixa sem modo diário** | Média | Alto | Progressão infinita + recorde local + "Continuar de onde parei"; validar D7 em 30 dias; se D7 < 3%, avaliar modo diário na v2 |
| R6 | **Dificuldade desbalanceada** (níveis 8–15 travam) | Média | Médio | Curva contínua e ajustável por config; monitorar abandono por nível; ajuste de curva sem deploy |
| R7 | **Falha de localStorage em modo privado** | Baixa | Médio | Wrapper com fallback em memória (§RNF-009) |
| R8 | **Violação de licença do dicionário** | Baixa | Alto | Atribuição no README; preferir seed MIT/domínio público; aplicar share-alike do wordfreq quando aplicável |
| R9 | **Abandono por lentidão em celular** | Baixa | Alto | Bundle < 200 KB, sem fontes web, PRNG local, geração < 120 ms |
| R10 | **Publicidade futura dilui o público 13–17** | Média | Médio | Ads só entre rodadas, nunca durante a jogabilidade; nunca vender a resposta |

---

## 17. Roadmap e marcos

### Marco 0 — Fundação (semana 1)
- Setup do projeto: Vite + React + TS + Tailwind + Vitest.
- Tokens de design, tema claro/escuro, alto contraste.
- `rng`, `evaluate`, `scoring`, `difficulty` com testes unitários.

### Marco 1 — Motor do jogo (semanas 2–3)
- Gerador procedural com placement, validação e fallback.
- Property-based tests (500 seeds).
- Componente de grade, seleção por arrasto e por clique.
- Loop completo de uma rodada (sem UI de produção).

### Marco 2 — UI e progressão (semanas 3–4)
- Home, GamePage, ResultPage, Settings.
- Progressão por tema/modo, recordes, persistência.
- Lista de palavras e HUD (tempo, pontos, combo).

### Marco 3 — Conteúdo e polimento (semana 5)
- Dicionários dos 4 temas (curadoria + validação).
- Modo Difícil.
- Dica "destacar letra".
- Sons, animações, tutorial interativo do nível 1.

### Marco 4 — Lançamento (semana 6)
- E2E tests, auditoria de acessibilidade, budget de performance.
- Analytics, SEO mínimo, monitoramento de erros.
- Soft launch e leitura das métricas por 30 dias.

### Backlog v2 (pós-lançamento)
- Modo diário compartilhado (com seed determinística por data).
- Card de resultado compartilhável.
- Gerador de tabuleiro **por semente compartilhável** (desafio por link).
- Modo offline / PWA instalável.
- Ranking opcional com backend e login.
- "Letras bônus" douradas (§8.3 opção B).
- Editor de palavras pelos usuários (com curadoria).

---

## 18. Fora de escopo

Explicitamente **não** farão parte do MVP:

1. Cadastro, login, contas, sincronização entre dispositivos.
2. Ranking global, placar multiplayer, salas ao vivo.
3. Modo diário compartilhado.
4. Anúncios, IAP, moedas, energia, lives.
5. PWA instalável / service worker / modo offline completo.
6. Internacionalização — PT-BR apenas.
7. Suporte a outros idiomas de teclado (AZERTY etc.).
8. Multiplayer versus local.
9. Editor de tabuleiro pelo usuário.
10. Aplicativo mobile nativo (App Store / Play Store).
11. Leaderboard com backend e política de privacidade avançada.
12. Narração por voz / áudio de letras.

---

## 19. Anexos

### 19.1 Glossário

| Termo | Definição |
|---|---|
| **Alvo** | Palavra que o jogador deve encontrar, posicionada propositalmente no tabuleiro. |
| **Extra** | Palavra válida acidentalmente contida na grade; dá bônus, não conta para a progressão. |
| **Combo** | Sequência de palavras encontradas com menos de 10 s de intervalo entre elas. |
| **Seed** | Número que determina de forma reproduzível a geração do tabuleiro. |
| **Aderência (snap)** | Comportamento da seleção que "gruda" na linha reta durante o arraste. |
| **D1 / D7** | Retenção de 1 e 7 dias. |
| **TTFW** | Tempo até a primeira palavra encontrada. |
| **Normalização** | Conversão de uma palavra para a forma de busca: sem acentos, `ç`→`c`, minúsculas. |
| **Zipf** | Medida de frequência de palavras em corpus (≥ 3 = comum). |

### 19.2 Registro de decisões (com base na pesquisa)

| Decisão | Alternativas descartadas | Justificativa |
|---|---|---|
| Caça-palavras em grade | Wordle/TERMO (adivinhação por tentativas) | Escolha explícita do PO; feedback contínuo, sem estado de derrota, melhor leitura de listas temáticas |
| Web app (React + Vite) | PWA, Expo/RN | Zero atrito, sem loja de apps, iteração rápida; MVP não justifica build mobile |
| Geração procedural validada | Tabuleiros curados à mão | Escala infinita de níveis sem produção manual; property tests garantem qualidade |
| Níveis infinitos | Modo diário | Escolha do PO; mantém o jogo 100% client-side |
| Dica única (destacar letra) | 5 tipos de dica | Escopo mínimo; o PO escolheu apenas uma |
| Sem pontuação por rodada | Pontuação por rodada | Escolha do PO; simplifica o balanceamento inicial |
| Cronômetro crescente | Contagem regressiva | Escolha do PO; sem pressão de derrota, bônus por velocidade |
| Sem ranking online | Ranking global | Exige backend, login e política de privacidade — fora de escopo |
| Acentos normalizados, `ç` preservado | Acentos completos na grade | Evita que o jogador precise digitar `â`/`õ`; `ç` é letra distinta do PT-BR |

### 19.3 Referências da pesquisa

- Josh Wardle — *Deconstructing and rebuilding Wordle* (2022) — anatomia do gênero.
- Game Developer — *The rise of once-a-day games* (2022) — os 3 pilares de jogos "once-a-day".
- Appodeal — *Mobile Casual Benchmarks Report 2025* — retenção e monetização por gênero.
- CrazyGames Docs — *Ad Tips for Word Games* — regras de posicionamento de anúncios.
- Apple Developer — *Onboarding for Games* — FTUE e medição de onboarding.
- Gamestage — *Design a game fans come back to* — checklist de regras de design casual.
- Liquid & Grit — *First-Time User Experience (FTUE) Toolkit*.
- Termo (Fernando Serboncini) e Letreco (Gabriel Toschi) — precursores PT-BR.
- Toolboxday — listas de palavras PT-BR derivadas do VERO + wordfreq (licenças e construção).
- IME-USP — *Dicionário br.ispell*.
- Referências de implementação: repositórios públicos `letreco`, `Purdle`, `wordle-clone`, `Hello Wordl`.

---

**Fim do documento — v1.0 — 2026-10-21**
