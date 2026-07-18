# Color Match — Ferrabisk Arquitetura & Design

Quiz interativo de paletas de cores para ambientes. O visitante responde
20 perguntas e recebe, ao final, uma das **50 paletas** possíveis, com nome,
descrição, três cores (com código HEX), uma referência de tinta e uma
justificativa de uso. O resultado pode ser **baixado em PDF** e o visitante
ainda pode abrir uma **pesquisa de imagens** de ambientes com aquela paleta.

Tudo isso vive em **um único arquivo**: `color-match-quiz-completo.html`.

---

## Destaques

- **Arquivo único e autossuficiente** (~210 KB). Sem build, sem servidor, sem banco de dados.
- **Funciona 100% offline.** O quiz e a geração do PDF não dependem de nenhuma biblioteca ou serviço externo.
- **20 perguntas** (3 opções cada) e **50 paletas** de resultado.
- **PDF do resultado** gerado por um motor próprio em JavaScript puro, com a logo da marca embutida e a mesma diagramação da tela.
- **Tipografia da marca embutida** (Poppins e Bricolage Grotesque) — nada é buscado no Google Fonts.
- **Único recurso online:** o botão "Ver ambientes com esta paleta", que abre uma pesquisa de imagens em nova aba (por design).

---

## Como hospedar

É um site estático. Basta enviar o arquivo `color-match-quiz-completo.html`
para qualquer hospedagem estática (Hostinger, Netlify, Vercel, GitHub Pages,
Cloudflare Pages, ou a hospedagem do seu próprio site).

1. Faça upload do arquivo.
2. Acesse pela URL gerada.
3. Pronto.

Se quiser que ele seja a página principal, renomeie para `index.html`.

**Recomendações**

- Sirva por **HTTPS** (padrão em qualquer serviço moderno). Isso evita avisos do navegador e garante o funcionamento do download e do botão de imagens.
- Para testar localmente, prefira um servidor simples a abrir o arquivo direto pelo `file://`. Exemplo, na pasta do arquivo:
  ```
  python3 -m http.server 8000
  ```
  e acesse `http://localhost:8000/color-match-quiz-completo.html`.

---

## Como funciona (visão técnica)

O HTML tem três telas controladas por exibição (`display`):

- `intro-screen` — apresentação e botão de iniciar.
- `quiz-screen` — uma pergunta por vez; a barra de progresso avança a cada resposta.
- `result-screen` — a paleta final + botões de ação.

Cada resposta soma pontos em quatro eixos internos (temperatura, intensidade,
luminosidade e clássico↔ousado). Ao final, o perfil escolhido é o mais próximo
do vetor de pontuação do visitante, dentro de um espaço calibrado para que as
50 paletas sejam todas alcançáveis e bem distribuídas.

O arquivo contém **três blocos `<script>`**, nesta ordem:

1. **Gerador de PDF** (`FerrabiskPDF`) — motor próprio que monta o PDF (texto, retângulos, cantos arredondados e a logo) sem nenhuma biblioteca externa. Usa as fontes nativas do PDF (Helvetica), então nenhuma fonte precisa ser embutida no documento gerado.
2. **Dados** — `questions` (as 20 perguntas e suas opções), `profiles` (as 50 paletas) e `profileVectors` (os vetores usados para escolher o resultado).
3. **Lógica** — pontuação, escolha do perfil, renderização das telas, download do PDF e o botão de pesquisa de imagens.

---

## Como editar o conteúdo

Todo o conteúdo editável está no **bloco de dados** (o segundo `<script>`).

### Perguntas

Procure por `const questions = [`. Cada pergunta tem um enunciado e uma lista de
opções; cada opção carrega os pontos que soma nos eixos. O layout se adapta
automaticamente ao número de opções, então dá para ajustar textos livremente.

### Paletas (resultados)

Procure por `const profiles = {`. Cada paleta segue este formato:

```js
chave_do_perfil: {
  name: "Nome da paleta",
  eyebrowColor: "#B5562F",          // cor do rótulo "SEU RESULTADO"
  desc: "Descrição do perfil...",
  colors: [
    { hex: "#B5562F", name: "Terracota" },
    { hex: "#2E1B12", name: "Marrom escuro" },
    { hex: "#E0CBB0", name: "Areia clara" }
  ],
  paint: "Suvinil — família ..., tom ...",  // referência de tinta
  why: "Quando/como usar esta paleta..."
}
```

> No PDF e na tela, a cor do texto sobre cada amostra é escolhida automaticamente
> (clara ou escura) conforme a luminosidade da cor — não é preciso configurar isso.

### Textos da marca / cabeçalho

O título "Color Match" e o subtítulo "FERRABISK ARQUITETURA & DESIGN" aparecem
tanto na tela quanto no PDF. Na tela, ficam no HTML das telas; no PDF, dentro da
função `downloadResult` (no terceiro `<script>`).

### Cores da identidade

No topo do CSS (`:root`) ficam as variáveis da marca:

| Variável        | Valor     | Uso                         |
|-----------------|-----------|-----------------------------|
| `--terracota`   | `#B5562F` | destaques e detalhes        |
| `--marrom`      | `#332420` | títulos / botão principal   |
| `--texto`       | `#4A3D30` | texto corrido               |
| `--texto-claro` | `#8C7560` | textos secundários          |
| `--marfim`      | `#F4EEE3` | fundo da página             |

Alterando esses valores, a identidade muda em todo o quiz.

> **Atenção:** o fundo do cabeçalho do **PDF** e o fundo atrás da **logo** usam o
> tom marfim `#F4EEE3` fixado no código (a logo foi achatada sobre esse fundo para
> ficar perfeita no PDF). Se você mudar `--marfim`, ajuste também esses pontos na
> função `downloadResult` para manter o casamento das cores.

---

## O download em PDF

- Gerado inteiramente no navegador, **sem internet**.
- Reproduz a diagramação da tela: cabeçalho com logo, card branco, amostras de cor, referência de tinta, justificativa em itálico e aviso final.
- Nome do arquivo: `color-match-<nome-da-paleta>.pdf`.
- Formato A4, com acentuação do português e travessões corretos.

Se o navegador do visitante for muito antigo e não conseguir gerar o arquivo,
aparece um aviso amigável em vez de falhar silenciosamente.

---

## O botão de pesquisa de imagens

Na tela de resultado, o botão **"Ver ambientes com esta paleta"** abre, em uma
**nova aba**, uma busca de imagens (Google Imagens) montada a partir do nome da
paleta + as cores + "decoração de interiores".

Este é o **único** ponto do projeto que acessa a internet. Todo o restante
funciona offline.

---

## Privacidade

- Não há rastreamento, cookies, formulários de captura ou envio de dados.
- Nada é armazenado: ao recarregar a página, o quiz recomeça.
- A única requisição externa possível é o clique voluntário no botão de imagens.

---

## Compatibilidade

Funciona nos navegadores atuais de desktop e celular (Chrome, Edge, Firefox,
Safari). O download de PDF usa recursos amplamente suportados; em navegadores
muito antigos pode não estar disponível, mas o quiz em si continua funcionando.

---

## Observação sobre as referências de tinta

As referências de tinta indicadas são **aproximações** com base em catálogos
públicos. Não existe correspondência exata entre cor digital e tinta física —
o próprio resultado recomenda testar a cor no ambiente antes de pintar. Caso
você tenha os códigos oficiais atualizados de uma marca específica, é só
substituir o campo `paint` de cada paleta.

---

## Estrutura de arquivos

```
color-match-quiz-completo.html   → o quiz completo (é só isto que precisa ser publicado)
README.md                        → este documento
```
