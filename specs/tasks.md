# Tarefas

| ID | Tarefa | Origem | Depende de | Concluída quando |
| :--- | :--- | :--- | :--- | :--- |
| **T-01** | Criar projeto Web API (.NET 10), projeto de testes xUnit e configurar a estrutura básica de pastas conforme a arquitetura. | Constituição | - | A solução e os projetos compilam sem erros ou warnings (`dotnet build`). |
| **T-02** | Configurar Entity Framework Core com SQLite, criar a entidade `Password` e a migração inicial. | RF-02, RF-03, D-01 | T-01 | O `DbContext` é configurado e a migração/schema aplica sem erros no SQLite. |
| **T-03** | Criar classe de domínio/gerador para criar senhas fortes atendendo às regras de negócio. | RF-01, RN-01, RN-02, RN-03, D-02 | T-01 | Testes unitários validam tamanho (>= 16), presença de caractere especial e ausência de espaços. |
| **T-04** | Criar o serviço de aplicação `PasswordService` para orquestrar a geração, persistência e busca de senhas. | RF-03, RF-04, D-03 | T-02, T-03 | Testes unitários do serviço cobrem criação, salvamento no banco e busca por GUID. |
| **T-05** | Implementar o endpoint `POST /api/passwords/generate` na Minimal API delegando para o `PasswordService`. | RF-01, RF-02, RF-03, CA-01 | T-04 | Teste de integração de caminho feliz usando `WebApplicationFactory` retorna `201 Created` com GUID e senha. |
| **T-06** | Implementar o endpoint `GET /api/passwords/{id}` com validação de formato GUID e tratamento de 404. | RF-04, RF-05, RF-06, CB-01, CB-02, CA-02, CA-03 | T-04 | Testes de integração cobrem retorno `200 OK` (caminho feliz), `404 Not Found` (GUID inexistente) e `400 Bad Request` com `ProblemDetails` (GUID inválido). |
| **T-07** | Executar suíte completa de testes e verificação de compilação sem warnings/erros. | Definição de Pronto | T-05, T-06 | `dotnet test` passa com 100% de sucesso e a aplicação roda sem erros. |