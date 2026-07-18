# Color Match — o que mudou e como configurar

## O que mudou no quiz

1. **Paletas infinitas.** As cores agora são geradas por um algoritmo (matiz, saturação e
   luminosidade calculados a partir das suas 20 respostas + a sequência exata das respostas
   como "semente" de variação). Isso roda 100% no navegador — não depende de internet.
   Duas pessoas com respostas parecidas ainda recebem tons ligeiramente diferentes.
2. **Botões separados de Google e Pinterest** na tela de resultado, para ver ambientes
   com a paleta gerada em cada uma das duas plataformas.
3. **Removida** a informação "50 resultados possíveis" da tela inicial.
4. **Link de analytics** (arquivo `color-match-analytics.html`), separado do quiz, que
   mostra estatísticas agregadas de todas as respostas (cores mais comuns, distribuição
   dos 4 eixos de gosto, referências de tinta mais indicadas, últimos resultados). Esse
   arquivo não tem nenhum link a partir do quiz — só quem tiver o link consegue abrir.

## Passo a passo para ativar o registro de dados (analytics)

O quiz sozinho não guarda nada — para acumular estatísticas de todo mundo que responde,
ele precisa de um lugar online para salvar cada resultado. A opção mais simples e gratuita
é o Google Sheets + Apps Script (não exige servidor nem conta paga).

**1. Crie a planilha e o backend**
   - Crie uma planilha nova em [sheets.google.com](https://sheets.google.com).
   - Menu **Extensões > Apps Script**.
   - Apague o conteúdo padrão e cole o conteúdo do arquivo `Code.gs` (entregue junto).
   - Clique em **Implantar > Nova implantação**.
   - Tipo: **App da Web**. Executar como: **Eu**. Quem pode acessar: **Qualquer pessoa**.
   - Implante, autorize as permissões pedidas, e copie a URL gerada (termina em `/exec`).

**2. Conecte o quiz ao backend**
   - Abra `color-match-quiz.html` num editor de texto.
   - Procure por `ANALYTICS_ENDPOINT` (uma vez só, perto do fim do arquivo).
   - Substitua `'COLE_AQUI_A_URL_DO_APPS_SCRIPT'` pela URL copiada no passo 1.
   - Salve e publique o arquivo atualizado onde o quiz já está hospedado.

**3. Conecte o painel de analytics ao mesmo backend**
   - Abra `color-match-analytics.html`.
   - Procure por `ANALYTICS_ENDPOINT_DEFAULT` no início do `<script>`.
   - Cole a mesma URL do passo 1.
   - (Alternativa: se preferir não editar o arquivo, você pode colar a URL direto na
     tela de bloqueio do painel toda vez que abrir — funciona também, só não fica salvo.)

**4. Proteja o painel com senha**
   - A senha de acesso já está configurada como `F3rr@b1sk`.
   - O arquivo guarda só o hash (SHA-256) da senha, nunca o texto puro — então, mesmo que
     alguém abra o código-fonte do arquivo, não vê a senha em si.
   - Pra trocar a senha depois: gere o hash SHA-256 do novo texto (qualquer gerador
     online de "SHA-256 hash" resolve, ou peça pra mim) e substitua o valor da constante
     `PASSWORD_HASH` no início do `<script>`.
   - A senha fica pedida uma vez por sessão do navegador (some se fechar a aba).

**5. Mantenha o link do painel só com você**
   - Hospede `color-match-analytics.html` num endereço que não seja linkado de nenhum
     lugar público (nem do quiz, nem do seu site, nem do Instagram).
   - Vale lembrar: por ser um arquivo estático (sem servidor de verdade por trás), essa
     senha é uma camada de proteção razoável para o caso de uso, mas não substitui um
     sistema de login real — é mais pra impedir acesso casual do que um ataque técnico
     deliberado.

## Sobre a referência de tinta

Como a cor agora é gerada ponto a ponto (e não escolhida entre um conjunto fixo), a
"referência de tinta" mostrada é a cor mais próxima dentro de um banco de ~130 tons de
referência (extraídos das famílias Suvinil já usadas antes). O texto já deixa isso claro
como aproximação — igual acontecia antes, só que agora a cor exata gerada também aparece
ao lado, para quem for pedir a tinta sob medida numa loja.
