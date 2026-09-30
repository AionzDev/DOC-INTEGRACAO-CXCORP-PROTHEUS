
# Documentação de Integração da Plataforma CX CORP
**CASO DE USO:** EXTRATO DA FOLHA DO LEITE (PDF)

## 1. Introdução

Esta documentação detalha a integração entre a plataforma CX CORP e o sistema ERP para obter o extrato mensal da folha do leite do cooperado em formato PDF.

**Pré-requisitos**: Acesso à API da CX CORP, credenciais de autenticação, e conhecimento básico de APIs RESTful.

---

## 2. Autenticação

Para acessar a API, é necessário o uso de um token de autenticação. Veja [Autenticação](../Autenticacao/autenticacao.md).

```bash
curl -X GET 'https://{host}/cxcorp_extratos/fechaLeite/pdf?cnpjCpf=12345678910&periodo=202603' \
-H 'Authorization: Bearer {token}' \
-H 'Accept: application/json'
```

> Observe que o path desta rota usa `fechaLeite` (com `L` maiúsculo), diferente da rota de [Fechamento da Folha do Leite](fechamento-folha-leite.md), que usa `fechaleite`.

---

## 3. Parâmetros da Requisição

| Parâmetro | Tipo   | Obrigatório | Descrição                               |
|-----------|--------|-------------|-----------------------------------------|
| cnpjCpf   | string | Sim         | CPF ou CNPJ do cooperado                |
| periodo   | string | Sim         | Mês de referência, no formato `yyyyMM`  |

---

## 4. Estrutura da Resposta

| Parâmetro | Tipo   | Descrição                                          |
|-----------|--------|----------------------------------------------------|
| pdf       | string | Conteúdo do arquivo PDF codificado em **Base64**   |

---

## 5. Exemplo de Resposta

```json
{
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

---
