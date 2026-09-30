
# Documentação de Integração da Plataforma CX CORP
**CASO DE USO:** AUTENTICAÇÃO

## 1. Introdução

Esta documentação detalha como a plataforma CX CORP se autentica no sistema ERP antes de consumir as demais rotas de integração.

Antes de **cada** chamada às rotas de integração, o CX CORP solicita um token OAuth2 (fluxo *password*) com o usuário e a senha de integração cadastrados para o ERP. O token obtido é enviado no header `Authorization` de todas as demais requisições.

**Pré-requisitos**: Usuário e senha de integração criados no ERP e liberados para as rotas de integração do CX CORP.

---

## 2. Requisição do Token

```bash
curl -X POST 'https://{host}/api/oauth2/v1/token?grant_type=password&username={usuario}&password={senha}' \
-H 'Content-Type: application/json'
```

> O corpo da requisição é enviado vazio. As credenciais são enviadas como parâmetros de query.

---

## 3. Parâmetros da Requisição

| Parâmetro  | Tipo   | Obrigatório | Descrição                                |
|------------|--------|-------------|------------------------------------------|
| grant_type | string | Sim         | Sempre `password`                        |
| username   | string | Sim         | Usuário de integração cadastrado no ERP  |
| password   | string | Sim         | Senha do usuário de integração           |

---

## 4. Estrutura da Resposta

| Parâmetro    | Tipo   | Obrigatório | Descrição                                                 |
|--------------|--------|-------------|-----------------------------------------------------------|
| access_token | string | Sim         | Token de acesso                                           |
| token_type   | string | Sim         | Tipo do token (ex.: `Bearer`)                             |

Outros campos (como `refresh_token`, `scope` e `expires_in`) podem ser retornados, mas não são utilizados pelo CX CORP.

---

## 5. Exemplo de Resposta

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refresh_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "scope": "default",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

---

## 6. Uso do Token

O CX CORP monta o header `Authorization` concatenando `token_type`, um espaço e `access_token`:

```bash
curl -X GET 'https://{host}/cxcorp_filiais/listaFiliais' \
-H 'Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...'
```

---

## 7. Tratamento de Erros

| Código | Descrição                         |
|--------|-----------------------------------|
| 400    | Requisição mal formatada          |
| 401    | Usuário ou senha inválidos        |

> Se o token não for obtido, a chamada à rota de integração não é realizada e o CX CORP exibe erro ao cooperado.

---
