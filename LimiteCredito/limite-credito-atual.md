
# Documentação de Integração da Plataforma CX CORP
**CASO DE USO:** LIMITE DE CRÉDITO ATUAL

## 1. Introdução

Esta documentação detalha a integração entre a plataforma CX CORP e o sistema ERP para obter o limite de crédito atual do cooperado: limite total, valor disponível, valor em aberto e valor em atraso. Esses dados alimentam o indicador de limite de compras do cooperado.

**Pré-requisitos**: Acesso à API da CX CORP, credenciais de autenticação, e conhecimento básico de APIs RESTful.

---

## 2. Autenticação

Para acessar a API, é necessário o uso de um token de autenticação. Veja [Autenticação](../Autenticacao/autenticacao.md).

```bash
curl -X GET 'https://{host}/cxcorp_extratos/LimiteCredito?cnpjCpf=12345678910' \
-H 'Authorization: Bearer {token}' \
-H 'Accept: application/json'
```

---

## 3. Parâmetros da Requisição

| Parâmetro | Tipo   | Obrigatório | Descrição                |
|-----------|--------|-------------|--------------------------|
| cnpjCpf   | string | Sim         | CPF ou CNPJ do cooperado |

---

## 4. Estrutura da Resposta

A API retorna um JSON com o objeto `limite`. Os valores são **strings no formato numérico brasileiro** (separador de milhar `.` e decimal `,`).

### 4.1. Limite (`limite`)

| Parâmetro  | Tipo   | Descrição                                           |
|------------|--------|-----------------------------------------------------|
| atual      | string | Limite de crédito total do cooperado                |
| disponivel | string | Valor do limite ainda disponível para compras       |
| emAberto   | string | Valor já utilizado (títulos em aberto)              |
| emAtraso   | string | Valor de títulos em atraso                          |

> O CX CORP considera o limite **excedido** quando `disponivel` é menor ou igual a zero e `emAberto` é maior que `atual`, e sinaliza **atraso** quando `emAtraso` é maior que zero.

---

## 5. Exemplo de Resposta

```json
{
  "limite": {
    "atual": "15.000,00",
    "disponivel": "11.500,00",
    "emAberto": "3.500,00",
    "emAtraso": "0,00"
  }
}
```

---

## 6. Tratamento de Erros

| Código | Descrição                   |
|--------|-----------------------------|
| 400    | Requisição mal formatada    |
| 401    | Falha de autenticação       |
| 404    | Registro não encontrado     |

---
