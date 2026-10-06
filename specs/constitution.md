# Constituição do Projeto

## Stack
- **Framework Target:** .NET 10 (C#)
- **Tipo de Aplicação:** Minimal API
- **Banco de Dados & ORM:** SQLite gerenciado via Entity Framework Core (EF Core)
- **Framework de Testes:** xUnit

## Arquitetura
- **Separação de Responsabilidades:** NENHUM endpoint Minimal API deve acessar o banco de dados diretamente ou conter regras de negócio. Todo o acesso a dados e orquestração de lógica devem ser delegados a um serviço de aplicação (`Application Service`).
- **Isolamento de Regra de Negócio:** Nenhuma regra de negócio (geração de senhas, validação de critérios) pode viver no endpoint ou em handlers HTTP.
- **Gerenciamento de Dependências:** Nenhuma dependência externa/pacote NuGet novo pode ser adicionado sem justificativa técnica explicitamente registrada no arquivo `plan.md`.

## Qualidade & Testes
- **Testes Unitários:** 100% das regras de negócio (ex.: gerador de senhas, validações) devem ser cobertas por testes unitários usando xUnit.
- **Testes de Integração:** Todo endpoint exposto pela API deve possuir pelo menos um teste de integração cobrindo o caminho feliz (`Happy Path`).
- **Tratamento de Erros de Validação:** Erros de validação de requisição ou de parâmetros devem retornar obrigatoriamente o código HTTP `400 Bad Request` padronizado via formato `ProblemDetails`.

## Convenções
- **Formato de Senhas Geradas:**
  - Comprimento mínimo e padrão de 16 caracteres.
  - Deve conter caracteres especiais.
  - **Proibido:** Não pode conter espaços em branco (` `).
- **Consultas de Senha:** A busca deve ser realizada exclusivamente por um identificador no formato `GUID`.
- **Formato de Retorno de Erros:** Todas as respostas de erro devem seguir a RFC 7807 (`ProblemDetails`).

## Governança
- **Processo Spec-Driven (SDD):** Nenhuma linha de código de funcionalidade pode ser implementada sem estar vinculada a uma tarefa aprovada no arquivo `tasks.md`.
- **Garantia de Compilação e Qualidade:** O projeto é considerado pronto apenas quando compilar zero erros/warnings e 100% dos testes unitários e de integração passarem no pipeline de execução.