
# Documentação de Integração da Plataforma CX CORP
**CASO DE USO:** PDF DO BOLETO

## 1. Introdução

Esta documentação detalha a integração entre a plataforma CX CORP e o sistema ERP para obter o PDF de um boleto listado em [Boletos do Cooperado](boletos.md).

A integração acontece em duas etapas:

1. O CX CORP chama a rota `cxcorp_contasReceber/pdfBoleto`, que retorna a URL do PDF do boleto.
2. O CX CORP faz um `GET` nessa URL, **com o mesmo header `Authorization`**, e recebe o arquivo PDF.

**Pré-requisitos**: Acesso à API da CX CORP, credenciais de autenticação, e conhecimento básico de APIs RESTful.

---

## 2. Autenticação

Para acessar a API, é necessário o uso de um token de autenticação. Veja [Autenticação](../Autenticacao/autenticacao.md).

```bash
curl -X GET 'https://{host}/cxcorp_contasReceber/pdfBoleto?cliente=000456&emissao=20260301&vencimento=20260410&numDoc=000098765&filial=0101&loja=01' \
-H 'Authorization: Bearer {token}'
```

---

## 3. Parâmetros da Requisição

| Parâmetro  | Tipo   | Obrigatório | Descrição                                         |
|------------|--------|-------------|---------------------------------------------------|
| cliente    | string | Sim         | Código do cliente no ERP (`cliente` do boleto)    |
| emissao    | string | Sim         | Data de emissão, no formato `yyyyMMdd`            |
| vencimento | string | Sim         | Data de vencimento, no formato `yyyyMMdd`         |
| numDoc     | string | Sim         | Número do título (`titulo` do boleto)             |
| filial     | string | Sim         | Código da filial (`filial` do boleto)             |
| loja       | string | Sim         | Loja do cliente no ERP (`loja` do boleto)         |

---

## 4. Estrutura da Resposta

A API retorna um array JSON. Para cada elemento, o CX CORP baixa o PDF a partir de `response.urlPdf`.

| Parâmetro  | Tipo   | Descrição                                   |
|------------|--------|---------------------------------------------|
| URLBoleto  | string | URL do boleto (não utilizada pelo CX CORP)  |
| response   | object | Dados do PDF gerado                         |

### 4.1. Objeto `response`

| Parâmetro       | Tipo   | Obrigatório | Descrição                                                                                     |
|-----------------|--------|-------------|-----------------------------------------------------------------------------------------------|
| urlPdf          | string | Sim         | URL do arquivo PDF. Deve responder a um `GET` com o header `Authorization` retornando o PDF binário |
| referenceNumber | string | Sim         | Número do documento do boleto                                                                 |
| expiresAt       | string | Não         | Data/hora de expiração da URL                                                                 |

---

## 5. Exemplo de Resposta

```json
[
  {
    "URLBoleto": "https://{host}/boletos/000098765",
    "response": {
      "urlPdf": "https://{host}/boletos/000098765.pdf",
      "referenceNumber": "000098765",
      "expiresAt": "2026-04-10T23:59:59Z"
    }
  }
]
```

A requisição à `urlPdf` deve retornar o conteúdo binário do PDF (`Content-Type: application/pdf`).

---

## 6. Tratamento de Erros

| Código | Descrição                   |
|--------|-----------------------------|
| 400    | Requisição mal formatada    |
| 401    | Falha de autenticação       |
| 404    | Registro não encontrado     |

---
