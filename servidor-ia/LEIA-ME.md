# Servidor da IA — cópia de teste "Inspeção IA"

A cópia com IA (https://lauraduarte1007-sys.github.io/Inspe-o-campo-IA/) manda as fotos de um item para o **Claude**, que sugere elemento, material, manifestação, causa, origem e uma descrição. O inspetor confere antes de salvar.

A chave da API do Claude **não pode ficar no app**: qualquer pessoa que abrisse o site conseguiria copiá-la. Por isso ela fica num servidor pequeno, o `worker.js` desta pasta, publicado no **Cloudflare Workers** (plano gratuito). O servidor:

- só atende quem está com login **ativo** na cópia com IA (confere o login do Firebase e o cadastro em `ia_users`);
- só aceita pedidos vindos do site do app;
- limita a 40 pedidos a cada 10 minutos por pessoa;
- não guarda as fotos: repassa para o Claude e devolve a sugestão.

## 1. Chave da API do Claude

1. Entre em **platform.claude.com** (Claude Console) e crie a conta. Conta nova pode resgatar um crédito grátis confirmando o celular. Depois, compre créditos em **Adicionar saldo** (Billing). O uso da API é cobrado por consumo.
2. Em **Chaves de API** (API keys), toque em **Criar chave** e escolha **Em outro lugar** (usar uma chave de API).
3. Preencha: nome `inspecao-ia`; validade **Nunca** (se a chave expirar, a IA para até você criar outra); conta vinculada: você; **espaço de trabalho: escolha um**. Sem espaço de trabalho, o servidor também precisaria da variável `ANTHROPIC_WORKSPACE_ID`.
4. Copie a chave (começa com `sk-ant-`): ela só aparece uma vez. Guarde a chave só no Cloudflare (passo 2). Não mande a chave por chat, e-mail ou código.

## 2. Publicar o servidor no Cloudflare

1. Crie uma conta gratuita em **dash.cloudflare.com**.
2. Abra **Workers e Pages** → **Criar** → **Criar Worker** (modelo "Hello World"). Nome: `inspecao-ia`. Toque em **Implantar**.
3. Toque em **Editar código**, apague tudo, cole o conteúdo do arquivo `worker.js` e toque em **Implantar**.
4. Em **Configurações → Variáveis e segredos**, adicione:
   - Tipo **Segredo**, nome `ANTHROPIC_API_KEY`, valor: a chave do passo 1.
   - Salve e implante de novo, se o painel pedir.
5. Abra o endereço do Worker no navegador (algo como `https://inspecao-ia.<sua-conta>.workers.dev`). Deve aparecer `"chave":true` e `"claude":"ok"`. Se em `"claude"` aparecer outra mensagem, ela diz o que corrigir (chave recusada, modelo não encontrado ou espaço de trabalho). Esse teste não confere o saldo: sem créditos, o app avisa na hora de pedir a sugestão.
6. Coloque esse endereço no arquivo `config.js` deste repositório, no campo `ia: { url: '...' }`, ou mande para quem mantém o app.

Variáveis opcionais (mesma tela, tipo **Texto**):

| Nome | Padrão | Para quê |
|---|---|---|
| `MODEL` | `claude-sonnet-5-5` | Modelo do Claude. `claude-haiku-5-5` é bem mais barato e mais rápido, com descrições mais simples. |
| `ALLOWED_ORIGIN` | `https://lauraduarte1007-sys.github.io` | Site que pode usar o servidor (separe vários por vírgula). |
| `FIREBASE_PROJECT_ID` | `inspecao-campo` | Projeto Firebase do login. |
| `USERS_COLLECTION` | `ia_users` | Coleção dos cadastros da cópia com IA. |
| `ANTHROPIC_WORKSPACE_ID` | (vazio) | Só se a chave foi criada sem espaço de trabalho: o ID do espaço (`wrkspc_...`). |

## Custo estimado

Cada item manda até 4 fotos de 1280 px. Com o Claude Sonnet 5.5, isso dá cerca de 8 mil tokens de entrada e algumas centenas de saída por item, ou seja, por volta de **US$ 0,02 por item** (preço de tabela: US$ 2 por milhão de tokens de entrada e US$ 10 por milhão de saída). Com o **Claude Haiku 5.5** (variável `MODEL` = `claude-haiku-5-5`), cai para cerca de **US$ 0,001 a 0,002 por item**, ou US$ 1 a 2 a cada mil itens (US$ 0,10 por milhão de tokens de entrada e US$ 0,50 por milhão de saída). Preços atualizados: https://platform.claude.com/docs/en/about-claude/pricing

O Cloudflare não cobra nada: o plano gratuito aceita 100 mil pedidos por dia e 10 ms de processamento por pedido. Este servidor usa cerca de 4 ms por pedido (o tempo esperando o Claude responder não conta). Se um dia passar de 100 mil pedidos, ele só para de responder até a meia-noite UTC (21h em Brasília).

Se você já tinha colado o `worker.js` no Cloudflare antes de 26/11/2026, cole a versão nova e implante de novo: ela gasta metade do processamento.

## Levar para a TI depois

O mesmo código pode virar uma rota do servidor da empresa (`servidor-ti`): ele só precisa da chave da API e do mesmo controle de login.
