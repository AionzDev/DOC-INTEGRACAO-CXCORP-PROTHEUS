
# Documentação de Integração da Plataforma CX CORP
**CASO DE USO:** CATÁLOGO DE PRODUTOS

## 1. Introdução

Esta documentação detalha a integração entre a plataforma CX CORP e o sistema ERP para sincronizar o catálogo de produtos da loja, agrupado por categoria (família).

A cada sincronização, o CX CORP cria ou atualiza as categorias pelo `idCategoria` e os produtos pelo `idProduto`.

**Pré-requisitos**: Acesso à API da CX CORP, credenciais de autenticação, e conhecimento básico de APIs RESTful.

---

## 2. Autenticação

Para acessar a API, é necessário o uso de um token de autenticação. Veja [Autenticação](../Autenticacao/autenticacao.md).

```bash
curl -X GET 'https://{host}/CXCORP_PRODUTOS' \
-H 'Authorization: Bearer {token}'
```

---

## 3. Parâmetros da Requisição

Esta rota não recebe parâmetros.

---

## 4. Estrutura da Resposta

A API retorna um JSON com o array `familias` (também aceito como `FAMILIAS`).

### 4.1. Famílias (`familias`)

| Parâmetro   | Tipo   | Obrigatório | Descrição                              |
|-------------|--------|-------------|----------------------------------------|
| idCategoria | string | Sim         | Código da categoria (família)          |
| descricao   | string | Sim         | Descrição da categoria                 |
| filial      | string | Não         | Código da filial                       |
| produtos    | array  | Sim         | Produtos da categoria                  |

### 4.2. Produtos (`familias[].produtos`)

| Parâmetro         | Tipo    | Obrigatório | Descrição                                             |
|-------------------|---------|-------------|-------------------------------------------------------|
| idProduto         | string  | Sim         | Código do produto no ERP. Chave usada na atualização  |
| descricao         | string  | Sim         | Descrição do produto                                  |
| idCategoria       | string  | Sim         | Código da categoria do produto                        |
| preco             | number  | Sim         | Preço de venda                                        |
| unidadeMedida     | string  | Sim         | Unidade de medida (ex.: `UN`, `KG`)                   |
| ativo             | boolean | Sim         | Indica se o produto está disponível na loja           |
| codBarras         | string  | Não         | Código de barras (EAN)                                |
| informacaoTecnica | string  | Não         | Informações técnicas do produto                       |
| filial            | string  | Não         | Código da filial                                      |

> O `idProduto` deve ser único em todo o catálogo.

---

## 5. Exemplo de Resposta

```json
{
  "familias": [
    {
      "filial": "0101",
      "idCategoria": "0001",
      "descricao": "NUTRIÇÃO ANIMAL",
      "produtos": [
        {
          "idProduto": "PROD001",
          "descricao": "RAÇÃO BOVINA 40KG",
          "idCategoria": "0001",
          "preco": 210.1,
          "unidadeMedida": "SC",
          "codBarras": "7891234567890",
          "informacaoTecnica": "Proteína bruta mínima de 18%",
          "ativo": true,
          "filial": "0101"
        }
      ]
    }
  ]
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
