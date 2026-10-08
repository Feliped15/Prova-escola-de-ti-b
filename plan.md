---
title: "Plan — Zona Azul"
version: 1.0.0
updated: 2026-10-07
---
# Plan

## Stack
- Python 3.12 + FastAPI + uvicorn, porque é rápido para fazer API REST com JSON.
- pytest + httpx para testes (o TestClient do FastAPI precisa do httpx).
- Dados em memória, porque o enunciado não pede banco. IDs começam em 1.

## Arquivos
- main.py: app FastAPI (variável app) e rotas
- test_app.py: testes do tests.md
- requirements.txt, Dockerfile, README.md (como rodar local, no Docker e os testes)

## Decisões
1. Dinheiro em centavos inteiros, porque float erra (0.1 + 0.2 ≠ 0.3).
2. Erros de validação do FastAPI viram 422 com {"erro": "codigo"}, porque o padrão {"detail": ...} quebra o contrato.
3. Média com 0,5 para cima sem usar round(), porque round(2.5) dá 2.
4. "Agora" com fuso fixo -03:00, porque o container roda em UTC.
5. Minutos sem os segundos, porque o teste encerra segundos depois de abrir.

## requirements.txt
```text
fastapi>=0.110,<1.0
uvicorn>=0.29,<1.0
pytest>=8,<9
httpx>=0.27,<1.0
```

## Dockerfile
```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8001
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8001"]
```

## Comandos
- docker build -t zona-azul .
- docker run -p 8001:8001 zona-azul
- pytest