
# Documentação de Integração da Plataforma CX CORP
**CASO DE USO:** DOWNLOAD DE NOTA FISCAL (NF-e)

## 1. Introdução

Esta documentação detalha a integração entre a plataforma CX CORP e o sistema ERP para obter o PDF (DANFE) e o XML de uma nota fiscal de saída. É utilizada para o download das notas listadas em [Compras](../Compras/compras.md).

**Pré-requisitos**: Acesso à API da CX CORP, credenciais de autenticação, e conhecimento básico de APIs RESTful.

---

## 2. Autenticação

Para acessar a API, é necessário o uso de um token de autenticação. Veja [Autenticação](../Autenticacao/autenticacao.md).

```bash
curl -X GET 'https://{host}/cxcorp_NotasSaida/nfe?filial=0101&serie=1&num=000012345' \
-H 'Authorization: Bearer {token}'
```

---

## 3. Parâmetros da Requisição

| Parâmetro | Tipo   | Obrigatório | Descrição                         |
|-----------|--------|-------------|-----------------------------------|
| filial    | string | Sim         | Código da filial emissora da nota |
| serie     | string | Sim         | Série da nota fiscal              |
| num       | string | Sim         | Número da nota fiscal             |

---

## 4. Estrutura da Resposta

A API retorna um **array** JSON. O CX CORP utiliza apenas o primeiro elemento; um array vazio é tratado como nota não encontrada.

| Parâmetro | Tipo   | Descrição                                          |
|-----------|--------|----------------------------------------------------|
| nota      | string | Número da nota fiscal                              |
| serie     | string | Série da nota fiscal                               |
| filial    | string | Código da filial emissora                          |
| nomePdf   | string | Nome do arquivo PDF                                |
| pdf       | string | Conteúdo do PDF (DANFE) codificado em **Base64**   |
| nomeXml   | string | Nome do arquivo XML                                |
| xml       | string | Conteúdo do XML da NF-e                            |

> **Atenção:** o objeto deve conter **somente** os campos acima. Qualquer campo adicional faz o CX CORP rejeitar a resposta.

---

## 5. Exemplo de Resposta

```json
[
  {
    "nota": "000012345",
    "serie": "1",
    "filial": "0101",
    "nomePdf": "nfe-000012345.pdf",
    "pdf": "JVBERi0xLjMKJbe+raoKMSAwIG9iago8PA...",
    "nomeXml": "nfe-000012345.xml",
    "xml": "<?xml version=\"1.0\" encoding=\"UTF-8\"?><nfeProc>...</nfeProc>"
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
