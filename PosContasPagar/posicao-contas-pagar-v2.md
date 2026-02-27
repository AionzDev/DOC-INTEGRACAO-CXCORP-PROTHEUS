
# Documentação de Integração da Plataforma CX CORP
**CASO DE USO:** POSIÇÃO DE CONTAS A PAGAR (v2)

## 1. Introdução

Esta documentação detalha a integração entre a plataforma CX CORP e o sistema ERP para obter a posição de contas a pagar do cooperado. Esta é a versão 2 do endpoint, com suporte a resumo consolidado e listagem de itens com detalhamento por vencimento.

**Pré-requisitos**: Acesso à API da CX CORP, credenciais de autenticação, e conhecimento básico de APIs RESTful.

---

## 2. Autenticação

Para acessar a API, é necessário o uso de uma chave de autenticação.

### Autenticação via API Key

```bash
curl -X GET 'https://{host}/cxcorp_posContasPagar/posicaoContasPagar' \
-H 'Authorization: Bearer {api_key}'
```

---

## 3. Estrutura da Resposta

A API retorna um JSON com resumo consolidado e listagem de itens.

### 3.1. Resumo (`resumo`)

Exibido no **cabeçalho** da tela de Posição Financeira — Contas a Pagar.

| Parâmetro      | Tipo   | Descrição                                  |
|----------------|--------|--------------------------------------------|
| totalVencido   | float  | Total consolidado de valores vencidos      |
| totalAVencer   | float  | Total consolidado de valores a vencer      |

### 3.2. Itens (`itens`)

Exibidos na **listagem de cards** da tela. Cada item representa uma entrada com saldo por vencimento.

| Parâmetro    | Tipo   | Renderizado | Descrição                                                                                       |
|--------------|--------|-------------|-------------------------------------------------------------------------------------------------|
| id           | string | Não         | Identificador do item. Utilizado pelo CX CORP para navegar à rota de detalhamento ao clicar no card. |
| descricao    | string | Sim         | Texto exibido no **título do card**                                                             |
| saldoVencido | float  | Sim         | Valor exibido no campo **Vencido** do card                                                      |
| saldoAVencer | float  | Sim         | Valor exibido no campo **A vencer** do card                                                     |

---

## 4. Exemplo de Resposta

```json
{
  "resumo": {
    "totalVencido": 1800,
    "totalAVencer": 4500
  },
  "itens": [
    {
      "id": "000361189",
      "descricao": "Item XYZ",
      "saldoVencido": 0,
      "saldoAVencer": 200
    },
    {
      "id": "000361188",
      "descricao": "Fornecedor XYZ",
      "saldoVencido": 1800,
      "saldoAVencer": 4300
    },
    {
      "id": "000361188",
      "descricao": "0101",
      "saldoVencido": 1800,
      "saldoAVencer": 4300
    }
  ]
}
```

---

## 5. Mapeamento Visual

A imagem abaixo ilustra onde cada campo do retorno é renderizado na interface do CX CORP.

![Posição de Contas a Pagar](Contas%20a%20Pagar.png)

| Campo da resposta       | Onde é exibido na tela                                              |
|-------------------------|---------------------------------------------------------------------|
| `resumo.totalVencido`   | Cabeçalho — card **Vencido**                                        |
| `resumo.totalAVencer`   | Cabeçalho — card **A vencer**                                       |
| `itens[].descricao`     | **Título** de cada card na listagem                                 |
| `itens[].saldoVencido`  | Campo **Vencido** de cada card na listagem                          |
| `itens[].saldoAVencer`  | Campo **A vencer** de cada card na listagem                         |
| `itens[].id`            | **Não renderizado** — usado para compor a rota de detalhamento do card |

---

## 6. Tratamento de Erros

| Código | Descrição                   |
|--------|-----------------------------|
| 400    | Requisição mal formatada    |
| 401    | Falha de autenticação       |
| 404    | Registro não encontrado     |

---
