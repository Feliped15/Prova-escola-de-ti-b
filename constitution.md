---
title: "Constitution — Zona Azul Digital"
version: 1.0.0
updated: 2026-10-07
---
# Constitution

## Núcleo
- Rotas: POST /bilhetes, POST /bilhetes/{id}/encerramento, POST /bilhetes/{id}/cancelamento, GET /bilhetes/ativos, GET /bilhetes?placa=, GET /relatorios/diario?data=
- Erros sempre no formato {"erro": "<codigo>"}; códigos 422, 404 e 409 conforme spec.md.
- Datas em ISO-8601 com fuso -03:00. Valores em centavos inteiros.
- Porta 8081, host 0.0.0.0.

## Regras
1. Documentação em português. Código em inglês. Rotas, campos e códigos de erro copiados literalmente do contrato, em português.
2. Stack: Python 3.12 + FastAPI + uvicorn. Testes com pytest + httpx. Nenhuma outra dependência.
3. Persistência em memória. IDs inteiros sequenciais começando em 1.
4. Valores monetários sempre em centavos, tipo inteiro. A API nunca retorna número com casas decimais.
5. Todo endpoint documenta seus status de erro. Todo erro tem corpo {"erro": "<codigo>"}; nenhum erro sai no formato padrão do FastAPI ({"detail": ...}).
6. Validação de formato (422) vem antes de regra de negócio (409).
7. O app roda em container pelo Dockerfile descrito no plan.md, escutando em 0.0.0.0:8081.
8. Só o que o contrato pede: sem autenticação, banco, paginação ou rotas extras.