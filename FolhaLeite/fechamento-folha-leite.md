
# Documentação de Integração da Plataforma CX CORP
**CASO DE USO:** FECHAMENTO DA FOLHA DO LEITE

## 1. Introdução

Esta documentação detalha a integração entre a plataforma CX CORP e o sistema ERP para obter o fechamento mensal da folha do leite do cooperado, com o volume entregue, o valor bruto e os eventos (créditos e descontos) do período.

O PDF do extrato mensal é obtido pela rota descrita em [Extrato da Folha do Leite (PDF)](extrato-folha-leite-pdf.md).

**Pré-requisitos**: Acesso à API da CX CORP, credenciais de autenticação, e conhecimento básico de APIs RESTful.

---

## 2. Autenticação

Para acessar a API, é necessário o uso de um token de autenticação. Veja [Autenticação](../Autenticacao/autenticacao.md).

```bash
curl -X GET 'https://{host}/cxcorp_extratos/fechaleite/view?cnpjCpf=12345678910&periodo=202603&page=1&pageSize=10' \
-H 'Authorization: Bearer {token}' \
-H 'Accept: application/json'
```

---

## 3. Parâmetros da Requisição

| Parâmetro | Tipo    | Obrigatório | Descrição                                        |
|-----------|---------|-------------|--------------------------------------------------|
| cnpjCpf   | string  | Sim         | CPF ou CNPJ do cooperado                         |
| periodo   | string  | Sim         | Mês de referência, no formato `yyyyMM`           |
| page      | integer | Não         | Número da página (começa em `1`)                 |
| pageSize  | integer | Não         | Quantidade de itens por página                   |

---

## 4. Estrutura da Resposta

A API retorna um array JSON em que cada elemento é um fechamento (documento) do período.

> **Atenção:** os objetos devem conter **somente** os campos listados abaixo. Qualquer campo adicional faz o CX CORP rejeitar a resposta.

| Parâmetro  | Tipo   | Descrição                                   |
|------------|--------|---------------------------------------------|
| doc        | string | Número do documento do fechamento           |
| emissao    | string | Data de emissão do fechamento               |
| serie      | string | Série do documento                          |
| volume     | number | Volume de leite entregue no período (litros)|
| valorBruto | number | Valor bruto do período                      |
| itens      | array  | Eventos (créditos e descontos) do fechamento|

### 4.1. Itens (`itens`)

| Parâmetro | Tipo   | Descrição                                           |
|-----------|--------|-----------------------------------------------------|
| descricao | string | Descrição do evento                                 |
| evento    | string | Código do evento                                    |
| valor     | number | Valor do evento                                     |
| tipo      | string | Tipo do evento (ex.: crédito ou débito)             |
| titulo    | string | Número do título financeiro gerado                  |
| prefixo   | string | Prefixo do título                                   |
| parcela   | string | Parcela do título                                   |
| filial    | string | Código da filial                                    |
| emissao   | string | Data de emissão do título                           |

---

## 5. Exemplo de Resposta

```json
[
  {
    "doc": "000045678",
    "emissao": "31/03/2026",
    "serie": "2",
    "volume": 3000,
    "valorBruto": 7350.0,
    "itens": [
      {
        "descricao": "LEITE ENTREGUE NO MÊS",
        "evento": "001",
        "valor": 7350.0,
        "tipo": "C",
        "titulo": "000045678",
        "prefixo": "LTE",
        "parcela": "1",
        "filial": "0101",
        "emissao": "31/03/2026"
      },
      {
        "descricao": "FUNRURAL",
        "evento": "101",
        "valor": 110.25,
        "tipo": "D",
        "titulo": "000045678",
        "prefixo": "LTE",
        "parcela": "1",
        "filial": "0101",
        "emissao": "31/03/2026"
      }
    ]
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
