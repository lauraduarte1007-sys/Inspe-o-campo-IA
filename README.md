# Inspeção IA — cópia de teste com IA

Cópia do app de inspeção estrutural que usa o **Claude** para sugerir, a partir das fotos de cada item, o elemento, o material, a manifestação, a causa, a origem e uma descrição. O inspetor confere antes de salvar.

**Abrir:** https://lauraduarte1007-sys.github.io/Inspe-o-campo-IA/

- O app principal (https://lauraduarte1007-sys.github.io/Inspecao-campo/) não é alterado por esta cópia.
- Os dados ficam separados: coleções `ia_` no mesmo projeto Firebase, banco local próprio no aparelho e liberações próprias. Para entrar, use a mesma matrícula e senha do app principal; a primeira pessoa a entrar vira administradora da cópia.
- As regras de segurança (`firestore.rules`) incluem o app principal e esta cópia. Publique o arquivo inteiro em *Firebase → Firestore Database → Regras*.
- A IA passa pelo servidor da pasta [`servidor-ia`](servidor-ia/LEIA-ME.md), que guarda a chave da API do Claude. O endereço dele fica em `config.js` (campo `ia.url`): hoje, `https://inspecao-ia.laura-duarte1007.workers.dev`. Sem esse endereço, a cópia funciona sem IA.

Sem sinal, a cópia continua funcionando: os pedidos para a IA ficam na fila e são feitos quando a internet volta.
