
# Documentação de Integração da Plataforma CX CORP
**CASO DE USO:** DECLARAÇÃO DE IMPOSTO DE RENDA

## 1. Introdução

Esta documentação detalha a integração entre a plataforma CX CORP e o sistema ERP para obter a declaração de Imposto de Renda do cooperado em formato PDF.

**Pré-requisitos**: Acesso à API da CX CORP, credenciais de autenticação, e conhecimento básico de APIs RESTful.

---

## 2. Autenticação

Para acessar a API, é necessário o uso de uma chave de autenticação.

### Autenticação via API Key

```bash
curl -X GET 'https://{host}/cxcorp_extratos/DeclaraIr?cnpjCpf=12345678910&periodo=2024' \
-H 'Authorization: Bearer {api_key}'
```

---

## 3. Parâmetros da Requisição

| Parâmetro | Tipo   | Obrigatório | Descrição                              |
|-----------|--------|-------------|----------------------------------------|
| cnpjCpf   | string | Sim         | CPF ou CNPJ do cooperado               |
| periodo   | string | Sim         | Ano de referência da declaração (ex: `2024`) |

---

## 4. Estrutura da Resposta

A API retorna um JSON contendo o PDF da declaração codificado em Base64.

| Parâmetro | Tipo   | Descrição                                                         |
|-----------|--------|-------------------------------------------------------------------|
| pdf       | string | Conteúdo do arquivo PDF codificado em **Base64**                  |

---

## 5. Exemplo de Resposta

```json
{
  "pdf": "JVBERi0xLjMKJbe+raoKMSAwIG9iago8PA..."
}
```

> O valor do campo `pdf` é uma string Base64 que representa o arquivo PDF completo da declaração de IR. O CX CORP deve decodificar essa string para exibir ou disponibilizar o arquivo ao cooperado.

---

## 6. Mapeamento Visual

A imagem abaixo ilustra a tela de solicitação da Declaração de IR no CX CORP. O cooperado seleciona o ano de referência e aciona o botão para solicitar o informe — o PDF retornado por este endpoint é então disponibilizado para download ou visualização.

![Declaração IR](Declara-IR.png)

---

## 7. Tratamento de Erros

| Código | Descrição                   |
|--------|-----------------------------|
| 400    | Requisição mal formatada    |
| 401    | Falha de autenticação       |
| 404    | Registro não encontrado     |

---
