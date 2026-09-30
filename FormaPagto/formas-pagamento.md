
# Documentação de Integração da Plataforma CX CORP
**CASO DE USO:** FORMAS DE PAGAMENTO

## 1. Introdução

Esta documentação detalha a integração entre a plataforma CX CORP e o sistema ERP para obter a lista geral de formas de pagamento cadastradas no ERP.

As formas de pagamento liberadas para um cooperado específico são obtidas pela rota descrita em [Formas de Pagamento do Cliente](../Cliente_FormaPagto/formas-pagamento-cliente-filial.md).

**Pré-requisitos**: Acesso à API da CX CORP, credenciais de autenticação, e conhecimento básico de APIs RESTful.

---

## 2. Autenticação

Para acessar a API, é necessário o uso de um token de autenticação. Veja [Autenticação](../Autenticacao/autenticacao.md).

```bash
curl -X GET 'https://{host}/cxcorp_FormaPagto/listaFormaPagto' \
-H 'Authorization: Bearer {token}'
```

---

## 3. Parâmetros da Requisição

Esta rota não recebe parâmetros.

---

## 4. Estrutura da Resposta

A API retorna um array JSON com as formas de pagamento.

| Parâmetro | Tipo   | Descrição                                                   |
|-----------|--------|-------------------------------------------------------------|
| idFilial  | string | Código da filial. Vazio quando a forma vale para todas      |
| codigo    | string | Código da forma de pagamento                                |
| descricao | string | Descrição da forma de pagamento                             |

---

## 5. Exemplo de Resposta

```json
[
  {
    "idFilial": "",
    "codigo": "BOL",
    "descricao": "BOLETO"
  },
  {
    "idFilial": "",
    "codigo": "CC",
    "descricao": "CARTÃO DE CRÉDITO"
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
