# Plano Técnico

## Contexto
O objetivo deste projeto é implementar uma API simples e lúdica para geração e consulta de senhas fortes usando a metodologia Spec-Driven Development (SDD). A aplicação deve gerar uma senha aleatória que atenda a critérios rígidos de segurança, persistir essa senha vinculada a um identificador único (GUID) e permitir a recuperação posterior dessa mesma senha a partir do GUID fornecido.

## Arquitetura
A aplicação seguirá uma arquitetura em camadas simplificada (Layered/Clean-lite), visando o desacoplamento exigido pela constituição:

- **Presentation / API Layer (Minimal API):** Responsável apenas por mapear as rotas HTTP, validar parâmetros primitivos da requisição e delegar a execução para a camada de aplicação. Retorna HTTP 200/201 ou 400 (ProblemDetails).
- **Application Layer (`PasswordService`):** Orquestra o fluxo de negócio. Executa a lógica de geração de senha e realiza chamadas ao repositório/banco de dados.
- **Domain Layer (`Password` Domain / Rules):** Contém o algoritmo puro de geração de senhas e validações de critérios (16+ caracteres, caracteres especiais, ausência de espaços).
- **Infrastructure Layer (`PasswordDbContext`):** Persistência via EF Core acessando banco SQLite local.

## Decisões
- **D-01: Uso do SQLite com EF Core**
  - *Justificativa:* Atende à necessidade de persistência sem complexidade de infraestrutura externa (Docker/containers), permitindo execução nativa rápida e isolada nos testes.
- **D-02: Algoritmo customizado de geração de senhas via `RandomNumberGenerator` (`System.Security.Cryptography`)**
  - *Justificativa:* Garante aleatoriedade criptograficamente segura para a inclusão obrigatoria de letras maiúsculas, minúsculas, números e caracteres especiais, evitando o uso de pacotes/dependências externas desnecessárias (conforme regra de governança).
- **D-03: Padrão Repositório / DbContext no Application Service**
  - *Justificativa:* Cumpre a regra constitucional de que nenhum endpoint HTTP acesse diretamente a camada de banco de dados (`DbContext`).
- **D-04: Testes de Integração via `WebApplicationFactory` (`Microsoft.AspNetCore.Mvc.Testing`) e SQLite em Memória**
  - *Justificativa:* Permite testar o pipeline HTTP completo dos endpoints sem depender do banco SQLite físico do ambiente local, mantendo os testes rápidos e determinísticos.

## Modelo de Dados

### Tabela: `Passwords`
| Coluna | Tipo | Chave | Descrição / Restrições |
| :--- | :--- | :--- | :--- |
| `Id` | `Guid` | PK | Identificador único gerado no momento da criação. |
| `Value` | `string` | - | O valor da senha gerada (máx 100 caracteres, não nulo). |
| `CreatedAt` | `DateTimeOffset` | - | Data e hora UTC de criação do registro. |

## Contratos

### 1. POST `/api/passwords/generate`
Gera uma nova senha forte de acordo com os critérios, armazena no banco de dados e retorna o identificador GUID e a senha gerada.

- **Request:** Sem corpo (payload vazio).
- **Response `201 Created`:**
  ```json
  {
    "id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
    "password": "aB3!kL9#mP0$qR2@",
    "createdAt": "2026-10-06T18:00:00Z"
  }


###   2. GET /api/passwords/{id}
Consulta uma senha existente a partir de seu GUID.
- **Request Parameters: id (GUID no path).
- **Response 200 OK:
```json
    {
      "id": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
      "password": "aB3!kL9#mP0$qR2@",
      "createdAt": "2026-10-06T18:00:00Z"
    }
```
- **Response 400 Bad Request (ProblemDetails - GUID inválido):

    ```json
        {
          "type": "[https://tools.ietf.org/html/rfc7231#section-6.5.1](https://tools.ietf.org/html/rfc7231#section-6.5.1)",
          "title": "Bad Request",
          "status": 400,
          "detail": "O identificador informado não é um GUID válido."
        }
    ``` 
- **Response 404 Not Found: Caso o GUID seja válido, mas não conste na base de dados.

### Riscos

R-01: Falha na inclusão obrigatória de caractere especial por aleatoriedade pura

Mitigação: O algoritmo de geração garantirá explicitamente a inserção prévia de pelo menos um caractere de cada grupo necessário (especial, número, maiúscula e minúscula) antes do embaralhamento final da string.

R-02: Concorrência/Conflitos de arquivo no SQLite durante testes de integração

Mitigação: Utilizar conexões SQLite em memória (Filename=:memory:) e garantir o EnsureCreated() ao inicializar a fábrica de testes.