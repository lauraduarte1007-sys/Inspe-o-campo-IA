# Inspeção IA — cópia de teste com IA

Cópia do app de inspeção estrutural que usa IA para sugerir, a partir das fotos de cada item, o elemento, o material, a manifestação, a causa, a origem e uma descrição. O inspetor confere antes de salvar.

A IA é o **Gemini, do Google, no plano grátis** (sem cartão). No plano grátis, o Google pode usar as fotos enviadas para melhorar os produtos dele; evite fotografar documentos, rostos e placas. O **Claude** fica como opção paga.

**Abrir:** https://lauraduarte1007-sys.github.io/Inspe-o-campo-IA/

- O app principal (https://lauraduarte1007-sys.github.io/Inspecao-campo/) não é alterado por esta cópia.
- Os dados ficam separados: coleções `ia_` no mesmo projeto Firebase, banco local próprio no aparelho e liberações próprias. Para entrar, use a mesma matrícula e senha do app principal; a primeira pessoa a entrar vira administradora da cópia.
- As regras de segurança (`firestore.rules`) incluem o app principal e esta cópia. Publique o arquivo inteiro em *Firebase → Firestore Database → Regras*.
- A IA passa pelo servidor da pasta [`servidor-ia`](servidor-ia/LEIA-ME.md), no Cloudflare (grátis), que guarda a chave da IA. O endereço dele fica em `config.js` (campo `ia.url`): hoje, `https://inspecao-ia.laura-duarte1007.workers.dev`. Sem esse endereço, a cópia funciona sem IA.

## Acompanhamento das anomalias

Na tela inicial, o botão **Acompanhamento das anomalias** abre todas as anomalias de todos os prédios num lugar só:

- resumo geral e por prédio: abertas, em andamento, concluídas, atrasadas e % concluído;
- filtros por prédio e por status, e ordem por prazo, risco ou GUT;
- em cada anomalia, **status** (aberta, em andamento, concluída), **prazo** (vem do modelo pelo risco e pela GUT; pode ser trocado), **data de conclusão** e **observação**. Tudo sincroniza com a equipe na hora, como o resto da inspeção;
- **Exportar compilado (.xlsx)**: uma planilha nova com as abas **Compilação** (uma linha por prédio, com totais), **Acompanhamento** (uma linha por anomalia, com as colunas em amarelo para preencher e situação calculada por fórmula) e **Critérios** (as faixas de risco e de GUT do modelo, usadas nas fórmulas).

Itens de bom estado e pontos cegos não contam como anomalias. Uma anomalia concluída deixa de contar como prazo vencido na lista de inspeções.

Sem sinal, a cópia continua funcionando: os pedidos para a IA ficam na fila e são feitos quando a internet volta. Se a cota grátis do dia acabar, os itens também ficam na fila e a IA os analisa quando a cota renovar (de madrugada).
