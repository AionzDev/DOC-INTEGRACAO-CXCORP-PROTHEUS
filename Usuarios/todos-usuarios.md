
# Documentação de Integração da Plataforma CX CORP
**CASO DE USO:** LISTA DE USUÁRIOS

## 1. Introdução

Esta documentação detalha a integração entre a plataforma CX CORP e o sistema ERP para obter a lista paginada de usuários.

**Pré-requisitos**: Acesso à API da CX CORP, credenciais de autenticação, e conhecimento básico de APIs RESTful.

---

## 2. Autenticação

Para acessar a API, é necessário o uso de uma chave de autenticação.

### Autenticação via API Key

```bash
curl -X GET 'https://{host}/cxcorp_usuarios/todosUsuarios?page=1&pageSize=10' \
-H 'Authorization: Bearer {api_key}'
```

---

## 3. Parâmetros da Requisição

| Parâmetro | Tipo    | Obrigatório | Descrição                                      |
|-----------|---------|-------------|------------------------------------------------|
| page      | integer | Sim         | Número da página (começa em `1`)               |
| pageSize  | integer | Sim         | Quantidade de itens por página                 |

---

## 4. Headers da Resposta

| Header         | Tipo    | Descrição                                  |
|----------------|---------|--------------------------------------------|
| x-total-items  | integer | Total de usuários disponíveis na listagem  |

---

## 5. Estrutura da Resposta

A API retorna um array JSON com os dados dos usuários.

| Parâmetro  | Tipo    | Descrição                          |
|------------|---------|------------------------------------|
| nome       | string  | Nome completo do usuário           |
| email      | string  | E-mail do usuário                  |
| cnpjCpf    | string  | CPF ou CNPJ do usuário             |
| telefone   | string  | Telefone principal                 |
| telefone2  | string  | Telefone secundário                |
| ativo      | boolean | Indica se o usuário está ativo     |

---

## 6. Exemplo de Resposta

```json
[
  {
    "nome": "João da Silva",
    "email": "joao.silva@email.com",
    "cnpjCpf": "12345678910",
    "telefone": "44999990001",
    "telefone2": "44999990002",
    "ativo": true
  },
  {
    "nome": "Maria Souza",
    "email": "maria.souza@email.com",
    "cnpjCpf": "12345678911",
    "telefone": "44999990003",
    "telefone2": "",
    "ativo": false
  }
]
```

> O header `x-total-items` da resposta indica o total de registros disponíveis, permitindo ao CX CORP calcular o número de páginas.

---

## 7. Tratamento de Erros

| Código | Descrição                   |
|--------|-----------------------------|
| 400    | Requisição mal formatada    |
| 401    | Falha de autenticação       |
| 404    | Registro não encontrado     |

---
