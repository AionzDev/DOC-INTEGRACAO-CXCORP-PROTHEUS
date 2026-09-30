
# Documentação de Integração da Plataforma CX CORP
**CASO DE USO:** CONTRATOS DE DEPÓSITO (ARMAZENAGEM)

## 1. Introdução

Esta documentação detalha a integração entre a plataforma CX CORP e o sistema ERP para obter os contratos de depósito de terceiros (armazenagem de grãos) abertos do cooperado.

Para cada contrato retornado, o CX CORP consulta os romaneios pela rota descrita em [Movimentações de Contratos e Romaneios](../Romaneios/movimentacoes-contratos-romaneios.md) e calcula a entrega realizada somando a quantidade fiscal dos romaneios de saída.

**Pré-requisitos**: Acesso à API da CX CORP, credenciais de autenticação, e conhecimento básico de APIs RESTful.

---

## 2. Autenticação

Para acessar a API, é necessário o uso de um token de autenticação. Veja [Autenticação](../Autenticacao/autenticacao.md).

```bash
curl -X GET 'https://{host}/cxcorp_ctrsDepTerceiros/contratosDeposito?cnpjCpF=12345678910' \
-H 'Authorization: Bearer {token}'
```

---

## 3. Parâmetros da Requisição

| Parâmetro | Tipo   | Obrigatório | Descrição                |
|-----------|--------|-------------|--------------------------|
| cnpjCpF   | string | Sim         | CPF ou CNPJ do cooperado |

---

## 4. Estrutura da Resposta

A API retorna um array JSON em que cada elemento é um contrato de depósito em aberto ou iniciado. O CX CORP ordena os contratos por `safra`, da mais recente para a mais antiga.

| Parâmetro        | Tipo   | Descrição                                                           |
|------------------|--------|---------------------------------------------------------------------|
| idcontrato       | string | Código do contrato. Usado para consultar os romaneios               |
| filial           | string | Código da filial do contrato. Usado para consultar os romaneios     |
| safra            | string | Código da safra                                                     |
| qtContrato       | number | Quantidade contratada (contrato inicial)                            |
| idproduto        | string | Código do produto                                                   |
| descricaoProduto | string | Descrição do produto                                                |
| unidadeMedida    | string | Unidade de medida da quantidade (ex.: `KG`, `SC`)                   |

---

## 5. Exemplo de Resposta

```json
[
  {
    "filial": "0101",
    "idcontrato": "000321",
    "safra": "2025/2026",
    "qtContrato": 120000,
    "idproduto": "SOJA001",
    "descricaoProduto": "SOJA EM GRÃOS",
    "unidadeMedida": "KG"
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
