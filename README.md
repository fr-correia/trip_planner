# Trip Planner

Aplicação web para planear itinerários de viagem com controlo de orçamento, alojamento, transporte e atividades por dia. Construída como ficheiro HTML único com backend Supabase para persistência e sincronização em tempo real entre utilizadores.

## Funcionalidades

- **Itinerário por cidades** — cidades organizadas em sequência, com dias editáveis por cidade
- **Orçamento detalhado** — breakdown por categoria (viagem, comida, alojamento, outros) com total em tempo real
- **Mapa SVG interativo** — pins por cidade com rotas visuais e drag-and-drop para reposicionar
- **Multi-utilizador** — presença em tempo real (indicadores de quem está online) e sincronização automática via polling
- **Modo edição** — snapshot para rollback, drag-and-drop de dias entre cidades
- **Tags por dia** — etiquetas coloridas para categorizar atividades
- **Export PDF/imprimir** — layout otimizado para impressão
- **Responsivo** — suporte mobile e desktop

## Stack

- HTML/CSS/JS vanilla (sem frameworks)
- [Supabase](https://supabase.com) — base de dados PostgreSQL + REST API
- Google Fonts (Playfair Display + DM Sans)

## Setup

### 1. Supabase

Cria um projeto em [supabase.com](https://supabase.com) e executa o seguinte SQL para criar as tabelas necessárias:

```sql
-- Tabela principal do itinerário
create table itinerary (
  id text primary key,
  data jsonb,
  updated_at timestamptz default now()
);

-- Inserir linha inicial
insert into itinerary (id, data) values ('main', '{}');

-- Tabela de presença (utilizadores online)
create table presence (
  username text primary key,
  last_seen timestamptz default now()
);
```

### 2. Configuração

No `index.html`, substitui as variáveis de configuração no topo do bloco `<script>`:

```js
var SUPA_URL = 'https://<teu-projeto>.supabase.co';
var SUPA_KEY = '<tua-anon-key>';
```

Atualiza também as credenciais de acesso:

```js
var USERS = {
  'NomeUtilizador1': 'password1',
  'NomeUtilizador2': 'password2'
};
```

> **Nota:** A autenticação atual é apenas client-side (adequada para uso privado/familiar). Para uso público, implementa autenticação via Supabase Auth.

### 3. Conteúdo da viagem

Personaliza os seguintes valores para a tua viagem:

```html
<!-- Título da página (linha 4) -->
<title>País A + País B — Mês/Ano</title>

<!-- Header (linhas 420-421) -->
<h1>País A &amp; País B</h1>
<p>Cidade Partida · Data Início — Data Fim</p>
```

E as cidades iniciais no JS:

```js
// Linha 547 — lista de cidades (chaves internas)
var CITIES = ['cidade1', 'cidade2', 'cidade3'];

// Coordenadas dos pins no mapa SVG (ajustar ao viewBox do mapa)
var PIN_CX = { cidade1: 149, cidade2: 171 };
var PIN_CY = { cidade1: 108, cidade2: 109 };
```

## Parâmetros hardcoded

Lista de valores atualmente hardcoded no `index.html` que podem precisar de ser alterados:

### Credenciais e autenticação

| Linha | Parâmetro | Valor atual |
|-------|-----------|-------------|
| 967 | `SUPA_URL` | `https://dhvvdbfjqeuoewtqnvqq.supabase.co` |
| 968 | `SUPA_KEY` | `sb_publishable_bvwHu05NfTKZNlQUa7VHuw_Q4OzE87Q` |
| 2147 | `USERS` | `{ 'Francisco': 'Sofia95', 'Cátia': 'Sofia95' }` |
| 2156 | session key | `'vn_user'` |

### Conteúdo da viagem

| Linha | Parâmetro | Valor atual |
|-------|-----------|-------------|
| 4 | `<title>` | `Vietname + Singapura — Nov/Dez 2025` |
| 420 | `<h1>` | `Vietname & Singapura` |
| 421 | subtítulo | `Lisboa · 21 Nov — 8 Dez 2025` |
| 547 | `CITIES` | `['hanoi','halong','danang','hoian','hcmc','singapore']` |
| 2393 | `PIN_CX` | `{hanoi:149, halong:171, danang:191, hoian:193, hcmc:163, singapore:219}` |
| 2394 | `PIN_CY` | `{hanoi:108, halong:109, danang:236, hoian:253, hcmc:370, singapore:518}` |
| 22–23 | CSS city colors | `--hanoi:#c94030`, `--halong:#2a7e96`, `--danang:#c07020`, `--hoian:#a07828`, `--hcmc:#6b44c4`, `--singapore:#1e8f66` |
| 2106, 2114 | export filename | `'itinerario_vietname.html'` |

### Base de dados

| Linha | Parâmetro | Valor atual |
|-------|-----------|-------------|
| 1025, 1045 | table + row ID | `'itinerary'`, `'main'` |
| 2206–2215 | presence table | `'presence'` |

### Timings e UI

| Linha | Parâmetro | Valor atual |
|-------|-----------|-------------|
| 1021 | save debounce | `800ms` |
| 1041 | poll interval | `8000ms` |
| 2151 | heartbeat | `10000ms` |
| 2152 | offline threshold | `25000ms` |
| 1394–1395 | currency symbol | `€` |

## Notas de segurança

- As passwords estão em texto simples no HTML — adequado apenas para uso privado
- A `SUPA_KEY` é uma chave pública (`sb_publishable_*`) — não é uma chave secreta, mas deve ter as RLS policies corretas configuradas no Supabase
- Recomenda-se ativar Row Level Security (RLS) nas tabelas Supabase para limitar acesso
