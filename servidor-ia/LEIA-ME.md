# Servidor da IA — cópia de teste "Inspeção IA"

A cópia com IA (https://lauraduarte1007-sys.github.io/Inspe-o-campo-IA/) manda as fotos de um item para a IA, que sugere elemento, material, manifestação, causa, origem e uma descrição. O inspetor confere antes de salvar.

A IA padrão é o **Gemini, do Google, no plano grátis** (sem cartão). O **Claude** continua como opção paga.

A chave da IA **não pode ficar no app**: qualquer pessoa que abrisse o site conseguiria copiá-la. Por isso ela fica num servidor pequeno, o `worker.js` desta pasta, publicado no **Cloudflare Workers** (plano grátis). O servidor:

- só atende quem está com login **ativo** na cópia com IA (confere o login do Firebase e o cadastro em `ia_users`);
- só aceita pedidos vindos do site do app;
- limita a 40 pedidos a cada 10 minutos por pessoa;
- não guarda as fotos: repassa para a IA e devolve a sugestão.

## 1. Chave grátis do Gemini

1. Entre em **aistudio.google.com** com uma conta Google (de preferência uma conta da equipe, não pessoal).
2. Toque em **Get API key** (Obter chave de API) → **Create API key** (Criar chave de API). Se pedir, crie um projeto novo.
3. Copie a chave. **Não ative o faturamento** nesse projeto: sem faturamento, o uso fica no plano grátis e não há cobrança.
4. Guarde a chave só no Cloudflare (passo 2). Não mande a chave por chat, e-mail ou código.

**Atenção:** no plano grátis, o Google pode usar o que é enviado (fotos e respostas) para melhorar os produtos dele, e pessoas podem revisar esse conteúdo. Os termos pedem para não enviar informação sigilosa ou pessoal. Evite fotografar documentos, rostos e placas.

## 2. Servidor no Cloudflare

Se o Worker `inspecao-ia` já existe (foi criado para o Claude), pule para o item 3.

1. Crie uma conta grátis em **dash.cloudflare.com**.
2. Abra **Workers e Pages** → **Criar** → **Criar Worker** (modelo "Hello World"). Nome: `inspecao-ia`. Toque em **Implantar**.
3. Abra o Worker → **Editar código**, apague tudo, cole o conteúdo do arquivo `worker.js` desta pasta e toque em **Implantar**.
4. Em **Configurações → Variáveis e segredos** → **Adicionar**:
   - Tipo **Segredo**, nome `GEMINI_API_KEY`, valor: a chave do passo 1.
   - Salve e implante de novo, se o painel pedir.
5. Abra o endereço do Worker no navegador (algo como `https://inspecao-ia.<sua-conta>.workers.dev`). Deve aparecer `"provedor":"gemini"` e `"ok"` ao lado de cada modelo.
6. Se o endereço for novo, coloque-o em `config.js`, no campo `ia: { url: '...' }`, ou mande para quem mantém o app.

Se a chave do Claude (`ANTHROPIC_API_KEY`) também estiver lá, ela pode ficar: com as duas chaves, o servidor usa o Gemini.

## Limites do plano grátis

- O Gemini grátis tem uma cota de pedidos por dia, que vale para o projeto inteiro (a equipe toda divide). Cada sugestão é 1 pedido.
- O servidor usa três modelos em sequência: quando a cota do dia de um acaba, ele passa para o próximo.
- Quando a cota de todos acaba, o app avisa e deixa os itens na fila. A cota renova à meia-noite do horário do Pacífico (5h em Brasília; 4h entre março e novembro), e a fila anda sozinha quando o app estiver aberto.
- A cota exata de cada modelo aparece no Google AI Studio, em **Rate limit**.

## Variáveis opcionais

Mesma tela, tipo **Texto**:

| Nome | Padrão | Para quê |
|---|---|---|
| `GEMINI_MODELS` | `gemini-3.5-flash-lite,gemini-4-hydrogen,gemini-3.8-flash` | Modelos do Gemini, na ordem de uso. Troque se o Google aposentar algum. |
| `IA` | (automático) | `claude` para usar o Claude mesmo com a chave do Gemini cadastrada. |
| `ALLOWED_ORIGIN` | `https://lauraduarte1007-sys.github.io` | Site que pode usar o servidor (separe vários por vírgula). |
| `FIREBASE_PROJECT_ID` | `inspecao-campo` | Projeto Firebase do login. |
| `USERS_COLLECTION` | `ia_users` | Coleção dos cadastros da cópia com IA. |

## Opção paga: Claude

1. Em **platform.claude.com** (Claude Console), adicione créditos em **Billing** e crie uma chave em **API Keys**.
2. No Cloudflare, adicione o segredo `ANTHROPIC_API_KEY` com essa chave e a variável `IA` = `claude` (ou apague o segredo `GEMINI_API_KEY`).
3. Variável opcional `MODEL` (padrão `claude-sonnet-5-5`). Com `claude-haiku-5-5`, cada item custa por volta de US$ 0,001 a 0,002; com o Sonnet, por volta de US$ 0,02. Preços: https://platform.claude.com/docs/en/about-claude/pricing

No Claude, os termos comerciais não permitem treinar modelos com o que é enviado.

## Levar para a TI depois

O mesmo código pode virar uma rota do servidor da empresa (`servidor-ti`): ele só precisa da chave da IA e do mesmo controle de login.
