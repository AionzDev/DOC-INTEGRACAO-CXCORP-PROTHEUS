
# Documentação de Integração da Plataforma CX CORP
**CASO DE USO:** COMPRAS DO COOPERADO

## 1. Introdução

Esta documentação detalha a integração entre a plataforma CX CORP e o sistema ERP para obter as compras (notas fiscais de saída) realizadas pelo cooperado. São duas rotas com o mesmo contrato de resposta:

| Rota                                   | Descrição                                            |
|----------------------------------------|------------------------------------------------------|
| `cxcorp_ClientesVendas/clientesVendas` | Compras do próprio cooperado                         |
| `cxcorp_ClientesVendas/grupoVendas`    | Compras de todos os cooperados do grupo do cooperado |

**Pré-requisitos**: Acesso à API da CX CORP, credenciais de autenticação, e conhecimento básico de APIs RESTful.

---

## 2. Autenticação

Para acessar a API, é necessário o uso de um token de autenticação. Veja [Autenticação](../Autenticacao/autenticacao.md).

```bash
curl -X GET 'https://{host}/cxcorp_ClientesVendas/clientesVendas?cnpjCpf=12345678910&dataini=20260101&datafim=20260331&page=1&pageSize=10' \
-H 'Authorization: Bearer {token}'
```

```bash
curl -X GET 'https://{host}/cxcorp_ClientesVendas/grupoVendas?cnpjCpf=12345678910&page=1&pageSize=10' \
-H 'Authorization: Bearer {token}'
```

---

## 3. Parâmetros da Requisição

Os parâmetros opcionais só são enviados quando o cooperado aplica o filtro correspondente.

| Parâmetro | Tipo    | Obrigatório | Descrição                                                        |
|-----------|---------|-------------|------------------------------------------------------------------|
| cnpjCpf   | string  | Sim         | CPF ou CNPJ do cooperado                                         |
| dataini   | string  | Não         | Data inicial de emissão, no formato `yyyyMMdd`                   |
| datafim   | string  | Não         | Data final de emissão, no formato `yyyyMMdd`                     |
| filial    | string  | Não         | Código da filial. **Enviado apenas na rota `clientesVendas`**    |
| valorMin  | string  | Não         | Valor bruto mínimo da nota                                       |
| valorMax  | string  | Não         | Valor bruto máximo da nota                                       |
| titulo    | string  | Não         | Filtro por título, repassado como informado pelo cooperado       |
| doc       | string  | Não         | Número do documento (nota fiscal)                                |
| serie     | string  | Não         | Série da nota fiscal                                             |
| page      | integer | Não         | Número da página (começa em `1`)                                 |
| pageSize  | integer | Não         | Quantidade de itens por página                                   |

---

## 4. Estrutura da Resposta

A API retorna um array JSON em que cada elemento é uma nota fiscal de compra.

| Parâmetro    | Tipo   | Obrigatório | Descrição                                                                 |
|--------------|--------|-------------|---------------------------------------------------------------------------|
| tipo         | string | Sim         | Tipo da compra. Exibido como **título** da compra no CX CORP              |
| doc          | string | Sim         | Número da nota fiscal                                                     |
| serie        | string | Sim         | Série da nota fiscal                                                      |
| filial       | string | Sim         | Código da filial emissora                                                 |
| chave        | string | Sim         | Chave de acesso da NF-e                                                   |
| emissao      | string | Sim         | Data de emissão, **obrigatoriamente** no formato `dd/MM/yyyy`             |
| valBrut      | number | Sim         | Valor bruto da nota                                                       |
| nomeFantasia | string | Não         | Nome exibido na compra (ex.: nome do cooperado ou da loja)                |
| grupo        | string | Não         | Código do grupo do cooperado                                              |
| cnpj         | string | Não         | CPF ou CNPJ do cooperado                                                  |
| codCli       | string | Não         | Código do cliente no ERP                                                  |
| lojaCli      | string | Não         | Loja do cliente no ERP                                                    |
| itens        | array  | Sim         | Itens da nota. Enviar `[]` quando não houver itens (nunca `null`)         |

### 4.1. Itens (`itens`)

| Parâmetro | Tipo    | Obrigatório | Descrição                          |
|-----------|---------|-------------|------------------------------------|
| produto   | string  | Sim         | Código do produto                  |
| descricao | string  | Sim         | Descrição do produto               |
| quant     | integer | Sim         | Quantidade (número inteiro)        |
| vlUnit    | number  | Sim         | Valor unitário                     |
| vlTotal   | number  | Sim         | Valor total do item                |
| vlBruto   | number  | Não         | Valor bruto do item                |

---

## 5. Exemplo de Resposta

```json
[
  {
    "tipo": "VENDA BALCÃO",
    "grupo": "000123",
    "cnpj": "12345678910",
    "codCli": "000456",
    "lojaCli": "01",
    "nomeFantasia": "JOÃO DA SILVA",
    "doc": "000012345",
    "chave": "35260312345678000190550010000123451000123450",
    "serie": "1",
    "filial": "0101",
    "emissao": "15/03/2026",
    "valBrut": 1250.5,
    "itens": [
      {
        "produto": "PROD001",
        "descricao": "RAÇÃO BOVINA 40KG",
        "quant": 5,
        "vlUnit": 210.1,
        "vlTotal": 1050.5,
        "vlBruto": 1050.5
      },
      {
        "produto": "PROD002",
        "descricao": "SAL MINERAL 25KG",
        "quant": 2,
        "vlUnit": 100.0,
        "vlTotal": 200.0,
        "vlBruto": 200.0
      }
    ]
  }
]
```

> Uma data de `emissao` fora do formato `dd/MM/yyyy` faz o CX CORP rejeitar a resposta inteira.

---

## 6. Tratamento de Erros

| Código | Descrição                   |
|--------|-----------------------------|
| 400    | Requisição mal formatada    |
| 401    | Falha de autenticação       |
| 404    | Registro não encontrado     |

---
