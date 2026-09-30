
# Documentação de Integração da Plataforma CX CORP
**CASO DE USO:** MOVIMENTAÇÃO DE COTA CAPITAL

## 1. Introdução

Esta documentação detalha a integração entre a plataforma CX CORP e o sistema ERP para obter o extrato de movimentações da cota capital do cooperado (integralizações, resgates e devoluções), com o saldo após cada movimento.

O saldo consolidado da cota capital é obtido pela rota descrita em [Saldo de Cota Capital](../SaldoCota/saldo-cota-capital.md).

**Pré-requisitos**: Acesso à API da CX CORP, credenciais de autenticação, e conhecimento básico de APIs RESTful.

---

## 2. Autenticação

Para acessar a API, é necessário o uso de um token de autenticação. Veja [Autenticação](../Autenticacao/autenticacao.md).

```bash
curl -X GET 'https://{host}/cxcorp_movimentosCota/movimentosCotaCapital?cnpjCpF=12345678910' \
-H 'Authorization: Bearer {token}'
```

---

## 3. Parâmetros da Requisição

| Parâmetro | Tipo   | Obrigatório | Descrição                |
|-----------|--------|-------------|--------------------------|
| cnpjCpF   | string | Sim         | CPF ou CNPJ do cooperado |

> O CX CORP não envia período. O ERP deve definir o período padrão do extrato (a implementação de referência em Protheus retorna os últimos 30 dias).

---

## 4. Estrutura da Resposta

A API retorna um array JSON em que cada elemento é um movimento da cota capital. O CX CORP ordena os movimentos por `datamovto`, do mais recente para o mais antigo.

| Parâmetro | Tipo   | Descrição                                                   |
|-----------|--------|-------------------------------------------------------------|
| datamovto | string | Data do movimento, no formato `yyyy-MM-dd`                  |
| historico | string | Histórico / descrição do movimento                          |
| documento | string | Número do documento de origem                               |
| tipo      | string | Tipo do movimento                                           |
| entrada   | number | Valor de entrada (integralização). `0` quando não se aplica |
| saida     | number | Valor de saída (resgate/devolução). `0` quando não se aplica|
| saldo     | number | Saldo da cota capital após o movimento                      |

---

## 5. Exemplo de Resposta

```json
[
  {
    "datamovto": "2026-03-10",
    "historico": "INTEGRALIZAÇÃO DE CAPITAL",
    "documento": "000012345",
    "tipo": "01",
    "entrada": 500.0,
    "saida": 0,
    "saldo": 5500.0
  },
  {
    "datamovto": "2026-03-25",
    "historico": "RESGATE DE CAPITAL",
    "documento": "000012399",
    "tipo": "10",
    "entrada": 0,
    "saida": 200.0,
    "saldo": 5300.0
  }
]
```

---

## 6. Tratamento de Erros

| Código | Descrição                   |
|--------|-----------------------------|
| 400    | Requisição mal formatada    |
| 401    | Falha de autenticação       |
| 404    | Registro não encontrado     |

---
