
# Documentação de Integração da Plataforma CX CORP
**CASO DE USO:** BOLETOS DO COOPERADO

## 1. Introdução

Esta documentação detalha a integração entre a plataforma CX CORP e o sistema ERP para obter os boletos (títulos a receber da cooperativa) do cooperado. São duas rotas com o mesmo contrato de resposta:

| Rota                              | Descrição                                            |
|-----------------------------------|------------------------------------------------------|
| `cxcorp_contasReceber/view`       | Boletos do próprio cooperado                         |
| `cxcorp_contasReceber/viewGrupo`  | Boletos de todos os cooperados do grupo do cooperado |

O PDF de cada boleto é obtido pela rota descrita em [PDF do Boleto](pdf-boleto.md).

**Pré-requisitos**: Acesso à API da CX CORP, credenciais de autenticação, e conhecimento básico de APIs RESTful.

---

## 2. Autenticação

Para acessar a API, é necessário o uso de um token de autenticação. Veja [Autenticação](../Autenticacao/autenticacao.md).

```bash
curl -X GET 'https://{host}/cxcorp_contasReceber/view?cnpjCpf=12345678910&page=1&pageSize=10&dtVenIni=20260101&dtVenFim=20260331' \
-H 'Authorization: Bearer {token}'
```

```bash
curl -X GET 'https://{host}/cxcorp_contasReceber/viewGrupo?cnpjCpf=12345678910&page=1&pageSize=10' \
-H 'Authorization: Bearer {token}'
```

---

## 3. Parâmetros da Requisição

Os parâmetros opcionais só são enviados quando o cooperado aplica o filtro correspondente.

| Parâmetro | Tipo    | Obrigatório | Descrição                                                                           |
|-----------|---------|-------------|-------------------------------------------------------------------------------------|
| cnpjCpf   | string  | Sim         | CPF ou CNPJ do cooperado                                                            |
| page      | integer | Não         | Número da página (começa em `1`)                                                    |
| pageSize  | integer | Não         | Quantidade de itens por página                                                      |
| situacao  | string  | Não         | Situação do título, repassada como informada pelo cooperado. **Apenas na rota `view`** |
| dtVenIni  | string  | Não         | Data inicial de vencimento, no formato `yyyyMMdd`. **Apenas na rota `view`**        |
| dtVenFim  | string  | Não         | Data final de vencimento, no formato `yyyyMMdd`. **Apenas na rota `view`**          |

---

## 4. Estrutura da Resposta

A API retorna um array JSON em que cada elemento é um boleto. Todos os campos são do tipo `string`.

| Parâmetro   | Tipo   | Descrição                                                                     |
|-------------|--------|-------------------------------------------------------------------------------|
| titulo      | string | Número do título                                                              |
| prefixo     | string | Prefixo do título                                                             |
| filial      | string | Código da filial                                                              |
| cliente     | string | Código do cliente no ERP                                                      |
| loja        | string | Loja do cliente no ERP                                                        |
| codBarras   | string | Linha digitável / código de barras do boleto                                  |
| valorTitulo | string | Valor original do título                                                      |
| saldo       | string | Saldo em aberto do título                                                     |
| status      | string | Situação do título: `Pago`, `Em aberto` ou `Vencido`                          |
| emissao     | string | Data de emissão                                                               |
| vencimento  | string | Data de vencimento                                                            |
| grupo       | string | Código do grupo do cooperado                                                  |

> Os campos `cliente`, `loja`, `titulo`, `filial`, `emissao` e `vencimento` são reenviados pelo CX CORP na rota de [PDF do Boleto](pdf-boleto.md) e, portanto, devem ser preenchidos.

---

## 5. Exemplo de Resposta

```json
[
  {
    "grupo": "000123",
    "codBarras": "23793381286000001234567890123456789870000150000",
    "valorTitulo": "1500.00",
    "saldo": "1500.00",
    "status": "Em aberto",
    "emissao": "01/03/2026",
    "vencimento": "10/04/2026",
    "titulo": "000098765",
    "prefixo": "NF",
    "filial": "0101",
    "cliente": "000456",
    "loja": "01"
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
