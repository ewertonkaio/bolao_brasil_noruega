# Bolão Brasil x Noruega — Copa 2026 (oitavas)

Mesma estrutura do bolão do Japão, com TABELAS SEPARADAS no mesmo Supabase, para os dois bolões não se misturarem.

- Jogo: 05/07/2026 às 17h (Brasília) — apostas encerram nesse horário.
- Entrada: R$ 10. Prêmio: 90% ganhadores / 10% admin.
- Só ganha quem cravar o placar exato (tempo normal, sem prorrogação/pênaltis).

---

## Passo 1 — Criar as tabelas separadas no Supabase

No SQL Editor, rode:

```sql
create table palpites_noruega (
  nome_key text primary key,
  nome text not null,
  br int not null,
  jp int not null,
  pago boolean default false,
  ts bigint not null,
  mp_payment_id text,
  chave_pix text
);
create table config_noruega (
  chave text primary key,
  valor text
);
alter table palpites_noruega enable row level security;
alter table config_noruega enable row level security;
create policy "todos leem palpites_n" on palpites_noruega for select using (true);
create policy "todos inserem palpites_n" on palpites_noruega for insert with check (true);
create policy "todos atualizam palpites_n" on palpites_noruega for update using (true);
create policy "todos leem config_n" on config_noruega for select using (true);
create policy "todos escrevem config_n" on config_noruega for insert with check (true);
create policy "todos atualizam config_n" on config_noruega for update using (true);
```

A coluna do placar da Noruega continua chamando "jp" internamente (pra reaproveitar o codigo). Sem problema, e so o nome interno.

---

## Passo 2 — Publicar no Netlify (site NOVO)

Site separado do bolao do Japao. Publique via GitHub (repositorio novo) ou Netlify CLI. Nao reaproveite o mesmo site.

## Passo 3 — Variaveis de ambiente (no site novo)

- MP_ACCESS_TOKEN -> Access Token de producao do Mercado Pago (pode ser o mesmo do outro bolao)
- SUPABASE_URL -> https://zukiviolneukiqjjvakk.supabase.co
- SUPABASE_KEY -> chave service_role do Supabase
- PIX_AMOUNT -> 10

Depois de salvar, faca Trigger deploy.

## Passo 4 — Webhook do Mercado Pago

Adicione a URL de webhook do site novo:
https://SEU-SITE-NORUEGA.netlify.app/.netlify/functions/webhook

Cada site tem sua funcao apontando pra tabela palpites_noruega, entao os dois boloes ficam independentes. Se a mesma conta MP dispara os dois webhooks, cada site so acha na sua tabela os pagamentos que ele criou.

---

Resto do funcionamento e igual ao bolao do Japao (senha do organizador, premiacao, contagem regressiva, etc.).
