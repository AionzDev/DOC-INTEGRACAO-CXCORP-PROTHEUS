
# Documentação de Integração da Plataforma CX CORP
**CASO DE USO:** NOTAS FISCAIS DA FOLHA DO LEITE

## 1. Introdução

Esta documentação detalha a integração entre a plataforma CX CORP e o sistema ERP para obter as notas fiscais de entrada de leite do cooperado. São duas rotas com o mesmo contrato de resposta:

| Rota                         | Descrição                                            |
|------------------------------|------------------------------------------------------|
| `cxcorp_nfeLeite/nfeLeite`   | Notas do próprio cooperado                           |
| `cxcorp_nfeLeite/grupo`      | Notas de todos os cooperados do grupo do cooperado   |

O PDF de cada nota é obtido pela rota descrita em [PDF da NF-e do Leite](pdf-nfe-leite.md).

**Pré-requisitos**: Acesso à API da CX CORP, credenciais de autenticação, e conhecimento básico de APIs RESTful.

---

## 2. Autenticação

Para acessar a API, é necessário o uso de um token de autenticação. Veja [Autenticação](../Autenticacao/autenticacao.md).

```bash
curl -X GET 'https://{host}/cxcorp_nfeLeite/nfeLeite?cnpjCpf=12345678910&dataini=20260101&datafim=20260331&page=1&pageSize=10' \
-H 'Authorization: Bearer {token}'
```

```bash
curl -X GET 'https://{host}/cxcorp_nfeLeite/grupo?cnpjCpf=12345678910&page=1&pageSize=10' \
-H 'Authorization: Bearer {token}'
```

---

## 3. Parâmetros da Requisição

Os parâmetros opcionais só são enviados quando o cooperado aplica o filtro correspondente.

| Parâmetro | Tipo    | Obrigatório | Descrição                                                    |
|-----------|---------|-------------|--------------------------------------------------------------|
| cnpjCpf   | string  | Sim         | CPF ou CNPJ do cooperado                                     |
| dataini   | string  | Não         | Data inicial de emissão, no formato `yyyyMMdd`               |
| datafim   | string  | Não         | Data final de emissão, no formato `yyyyMMdd`                 |
| valorMin  | string  | Não         | Valor bruto mínimo da nota                                   |
| valorMax  | string  | Não         | Valor bruto máximo da nota                                   |
| litroMin  | string  | Não         | Volume mínimo de litros                                      |
| litroMax  | string  | Não         | Volume máximo de litros                                      |
| doc       | string  | Não         | Número da nota fiscal. **Enviado apenas na rota `nfeLeite`** |
| serie     | string  | Não         | Série da nota fiscal. **Enviado apenas na rota `nfeLeite`**  |
| page      | integer | Não         | Número da página (começa em `1`)                             |
| pageSize  | integer | Não         | Quantidade de itens por página                               |

---

## 4. Estrutura da Resposta

A API retorna um array JSON em que cada elemento é uma nota fiscal de leite.

| Parâmetro | Tipo   | Obrigatório | Descrição                                                              |
|-----------|--------|-------------|------------------------------------------------------------------------|
| doc       | string | Sim         | Número da nota fiscal                                                  |
| serie     | string | Sim         | Série da nota fiscal                                                   |
| filial    | string | Sim         | Código da filial                                                       |
| chave     | string | Sim         | Chave de acesso da NF-e. Usada para obter o PDF da nota                |
| emissao   | string | Sim         | Data de emissão, **obrigatoriamente** no formato `dd/MM/yyyy`          |
| nome      | string | Sim         | Nome do produtor exibido na nota                                       |
| valBrut   | number | Sim         | Valor bruto da nota                                                    |
| mix       | string | Não         | Mix da nota, exibido como informado pelo ERP                           |
| grupo     | string | Não         | Código do grupo do cooperado                                           |
| itens     | array  | Sim         | Itens da nota. Enviar `[]` quando não houver itens (nunca `null`)      |

### 4.1. Itens (`itens`)

| Parâmetro | Tipo    | Obrigatório | Descrição                          |
|-----------|---------|-------------|------------------------------------|
| produto   | string  | Sim         | Código do produto                  |
| descricao | string  | Sim         | Descrição do produto               |
| unidade   | string  | Sim         | Unidade de medida (ex.: `L`)       |
| quant     | integer | Sim         | Quantidade (número inteiro)        |
| vlUnit    | number  | Sim         | Valor unitário                     |
| vlTotal   | number  | Sim         | Valor total do item                |

---

## 5. Exemplo de Resposta

```json
[
  {
    "grupo": "000123",
    "doc": "000045678",
    "chave": "35260312345678000190550020000456781000456780",
    "serie": "2",
    "filial": "0101",
    "emissao": "31/03/2026",
    "nome": "JOÃO DA SILVA",
    "valBrut": 7350.0,
    "mix": "2,45",
    "itens": [
      {
        "produto": "LEITE001",
        "descricao": "LEITE CRU REFRIGERADO",
        "unidade": "L",
        "quant": 3000,
        "vlUnit": 2.45,
        "vlTotal": 7350.0
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
