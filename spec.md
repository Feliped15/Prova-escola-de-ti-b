---
title: "Spec — Zona Azul"
version: 1.0.0
updated: 2026-10-07
---
# Spec

Variante: tarifa 400, fração 30 min, teto 5000, tolerância 10 min, porta 8001.
Erros sempre {"erro": "codigo"}. Erro 422 vem antes do 409.
Datas sempre em -03:00. Entrada sem fuso conta como -03:00.

## UC1 Abrir - POST /bilhetes
- Body {"placa": "ABC1D23"}, "entrada" opcional → 201 {"id": 1, "placa": "ABC1D23", "entrada": "2026-10-12T08:30:00-03:00", "status": "aberto"}
- Placa fora de 7 letras maiúsculas/números → 422 placa_invalida
- Entrada que não é data → 422 entrada_invalida
- Placa com bilhete aberto → 409 bilhete_em_aberto

## UC2 Encerrar - POST /bilhetes/{id}/encerramento
- 200 só com id, placa, entrada, saida, minutos, valor_centavos
- Não existe → 404 bilhete_nao_encontrado. Já encerrado ou cancelado → 409 bilhete_ja_encerrado
- Minutos completos. Até 10 → 0. Mais de 10 → cobra tudo: frações de 30 min para cima, 200 cada, máximo 5000
- Exemplos: 10→0, 11→200, 30→200, 31→400, 61→600, 721→5000

## UC3 Ativos - GET /bilhetes/ativos
- Só abertos, mais recente primeiro. Nenhum → []

## UC4 Relatório - GET /relatorios/diario?data=AAAA-MM-DD
- 200 com data, total_bilhetes, faturamento_centavos, tempo_medio_minutos
- Total = entradas do dia. Faturamento e média = encerrados no dia
- Média arredonda 0,5 para cima (10 e 11 → 11). Dia vazio → 0
- Data inválida ou ausente → 422 data_invalida

## UC5 Cancelar - POST /bilhetes/{id}/cancelamento
- 200 com id, placa, entrada e "status": "cancelado"
- Não aberto → 409 bilhete_nao_aberto. Não existe → 404 bilhete_nao_encontrado

## UC6 Histórico - GET /bilhetes?placa=ABC1D23
- Todos os bilhetes da placa, mais recente primeiro. Nunca usou → []
- Placa inválida ou ausente → 422 placa_invalida