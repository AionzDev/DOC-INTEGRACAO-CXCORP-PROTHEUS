
# Documentação de Integração da Plataforma CX CORP
**CASO DE USO:** PDF DA NF-e DO LEITE

## 1. Introdução

Esta documentação detalha a integração entre a plataforma CX CORP e o sistema ERP para obter o PDF de uma nota fiscal de leite listada em [Notas Fiscais da Folha do Leite](notas-folha-leite.md).

**Pré-requisitos**: Acesso à API da CX CORP, credenciais de autenticação, e conhecimento básico de APIs RESTful.

---

## 2. Autenticação

Para acessar a API, é necessário o uso de um token de autenticação. Veja [Autenticação](../Autenticacao/autenticacao.md).

```bash
curl -X GET 'https://{host}/cxcorp_nfeLeite/pdfnfe?chave=35260312345678000190550020000456781000456780&mesAno=03-2026' \
-H 'Authorization: Bearer {token}' \
-H 'Accept: application/json'
```

---

## 3. Parâmetros da Requisição

| Parâmetro | Tipo   | Obrigatório | Descrição                                                                 |
|-----------|--------|-------------|---------------------------------------------------------------------------|
| chave     | string | Sim         | Chave de acesso da NF-e (campo `chave` da listagem de notas)              |
| mesAno    | string | Sim         | Mês/ano de referência da nota. Repassado pelo CX CORP sem conversão       |

---

## 4. Estrutura da Resposta

| Parâmetro | Tipo   | Descrição                                                                                   |
|-----------|--------|---------------------------------------------------------------------------------------------|
| chave     | string | Chave de acesso da NF-e                                                                     |
| pdf       | string | Conteúdo do PDF codificado em **Base64**. O prefixo `data:application/pdf;base64,` é aceito |

---

## 5. Exemplo de Resposta

```json
{
  "chave": "35260312345678000190550020000456781000456780",
  "pdf": "JVBERi0xLjMKJbe+raoKMSAwIG9iago8PA..."
}
```

---

## 6. Tratamento de Erros

| Código | Descrição                   |
|--------|-----------------------------|
| 400    | Requisição mal formatada    |
| 401    | Falha de autenticação       |
| 404    | Registro não encontrado     |

> Em caso de falha, o CX CORP exibe ao cooperado a mensagem "Falha ao encontrar PDF. Entre em contato com a TI."

---
