---
title: "Tests — Zona Azul"
version: 1.0.0
updated: 2026-10-07
---
# Tests

Cada teste começa com a memória vazia. "N min" = abrir com entrada = agora menos N minutos e encerrar logo em seguida.
Variante: tarifa 400, fração 30, teto 5000, tolerância 10, porta 8001.

| # | Cenário → esperado | Tipo |
|---|---|---|
| T1 | POST /bilhetes {"placa": "ABC1D23"} → 201, id 1, status "aberto" | feliz |
| T2 | placa "abc1d23" → 422 placa_invalida | borda |
| T3 | entrada "ontem" → 422 entrada_invalida | borda |
| T4 | mesma placa aberta de novo → 409 bilhete_em_aberto | borda |
| T5 | placa aberta + entrada "ontem" → 422 entrada_invalida (422 antes do 409) | borda |
| T6 | 10 min → valor 0 | borda |
| T7 | 11 min → valor 200 | borda |
| T8 | 30 min → 200; 31 min → 400 | borda |
| T9 | 61 min → 600 | borda |
| T10 | 720 min → 4800; 721 min → 5000; 1440 min → 5000 | borda |
| T11 | encerrar duas vezes → 409 bilhete_ja_encerrado | borda |
| T12 | encerrar id 999 → 404 bilhete_nao_encontrado | borda |
| T13 | cancelar aberto → 200 status "cancelado", sem saida e sem valor | feliz |
| T14 | cancelar de novo → 409 bilhete_nao_aberto | borda |
| T15 | GET /bilhetes/ativos sem bilhetes → [] | borda |
| T16 | dois abertos com entrada 08:00 e 09:00 → 09:00 vem primeiro | feliz |
| T17 | relatório com encerrados de 10 e 11 min no dia → tempo_medio_minutos 11 | borda |
| T18 | relatório de dia vazio → total 0, faturamento 0, média 0 | borda |
| T19 | data "05/10/2026" → 422 data_invalida | borda |
| T20 | GET /bilhetes?placa=ZZZ9Z99 (nunca usada) → [] | borda |
| T21 | GET /bilhetes sem placa → 422 placa_invalida | borda |

## Smoke Docker
1. docker build -t zona-azul .
2. docker run -p 8001:8001 zona-azul
3. GET localhost:8001/bilhetes/ativos → 200