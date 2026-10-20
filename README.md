# Detecção e segmentação de uma pluma de fumaça com SAM

[![Abrir no Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1U_5N7gY4zkXOITe3i8lv2o67JA2HHCA5?authuser=1)

**Disciplina:** Modelagem Computacional (UNIFESP) · Turma noite
**Professora:** Raquel Jahara Lobosco
**Alunos:** Bruno de Abreu Correia (RA 168522) e Lucas Apolinario de Oliveira (RA 163913)

**Estudo de caso:** incêndio no terminal da Ultracargo (Santos-SP, abril de 2015)

## Links

- 📓 **Notebook no Colab:** [abrir no Google Colab](https://colab.research.google.com/drive/1U_5N7gY4zkXOITe3i8lv2o67JA2HHCA5?authuser=1)
- 📁 **Notebook no GitHub:** [`segmentacao_pluma_sam_santos.ipynb`](segmentacao_pluma_sam_santos.ipynb)
- 🔁 **Abrir a versão do GitHub no Colab:** [colab.research.google.com/github/...](https://colab.research.google.com/github/brunoabreuco/atv2-segmentacao-pluma-sam/blob/main/segmentacao_pluma_sam_santos.ipynb)

## Objetivo da atividade

Usar o **SAM (Segment Anything Model)** para segmentar uma pluma de fumaça em uma imagem.
A partir de um notebook inicial no Google Colab, avaliar o resultado e aprimorar o código,
os prompts e a apresentação da máscara. Como a segmentação automática pode incluir partes
do céu, nuvens ou edifícios, o foco é **desenvolver e justificar melhorias**, além de produzir
a máscara final.

### Etapas pedidas no enunciado

1. Observar a máscara gerada pelo SAM:
   - A máscara cobre a pluma?
   - Inclui regiões que não pertencem a ela?
   - Deixa partes da fumaça de fora?
2. Testar **caixa delimitadora** e **pontos positivos e negativos**, com pelo menos
   **três configurações diferentes de prompts**.
3. Mostrar lado a lado a **imagem original**, a **máscara binária** e a **máscara sobreposta**.
4. Discutir:
   - Quais características da imagem dificultaram a segmentação?
   - Como **iluminação**, **contraste** e a **semelhança entre fumaça e nuvens** afetaram o resultado?

## O que foi feito

O notebook aplica o SAM (`vit_b`) a **5 fotos** do incêndio e compara as configurações de prompt abaixo:

| Config. | Prompt |
|---|---|
| Inicial | 1 ponto positivo |
| A | só a caixa delimitadora |
| B | só pontos positivos |
| C | caixa + pontos positivos + pontos negativos |
| Final | config. C + pós-processamento (fecha buracos, remove ruído, mantém o maior componente) |

Para cada foto e configuração são exibidos lado a lado: original (com os prompts desenhados),
máscara binária e máscara sobreposta, além do *score* do SAM e da área da máscara.

### Melhoria extra: detecção automática (CLIPSeg + SAM)

Para não depender só de prompts desenhados à mão, o **CLIPSeg** recebe o texto
`"black smoke plume"` e gera um mapa grosso de onde está a fumaça. Desse mapa saem
uma **caixa** e um **ponto positivo** automáticos, que são passados ao SAM para refinar a borda.
O resultado é comparado com a config. C (IoU e área).

## Principais conclusões

- A melhor configuração manual foi a **C (caixa + pontos +/−)**: a caixa limita a região e os
  pontos negativos evitam que a máscara vaze para céu, nuvens e construções.
- Um ponto só tende a pegar apenas um pedaço da fumaça; a caixa sozinha pode puxar o que
  estiver dentro dela.
- O pipeline **CLIPSeg + SAM** detectou a pluma automaticamente em 4 das 5 fotos, com borda
  comparável à dos prompts manuais.
- **Dificuldades:** fogo misturado com a fumaça, fumaça fina que se confunde com névoa e nuvens,
  imagem de baixa resolução (foto 1) e bordas semitransparentes que a máscara binária corta de forma seca.
- **Iluminação e contraste:** fumaça escura contra céu claro dá bordas nítidas e bons resultados;
  céu claro atrás da pluma com luz alaranjada do fogo e regiões de fumaça cinza e fina reduzem a precisão.
- **Fumaça × nuvens:** o SAM não "sabe" o que é fumaça, só segmenta por forma e borda; massas
  cinzas parecidas com nuvens de tempestade ficaram de fora ou exigiram decisão via prompts.

## Como executar

1. Abra o notebook no [Google Colab](https://colab.research.google.com/drive/1U_5N7gY4zkXOITe3i8lv2o67JA2HHCA5?authuser=1).
2. Ative a GPU: *Ambiente de execução → Alterar tipo de ambiente de execução → GPU T4*.
3. Ajuste `PASTA_IMGS` para a pasta do Drive com as fotos (`foto1.png` … `foto5.png`).
4. Execute todas as células. As máscaras são salvas na pasta `saidas/`.

## Estrutura do repositório

```
├── README.md
├── segmentacao_pluma_sam_santos.ipynb   # notebook com código, figuras e análise
└── imgs/                                # imagens utilizadas
```