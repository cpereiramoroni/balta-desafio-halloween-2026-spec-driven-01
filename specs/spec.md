# Gerador de Senhas Fortes

## Problema
Usuários e aplicações frequentemente precisam de senhas fortes e aleatórias para garantir segurança e acesso a serviços. A criação manual de senhas costuma resultar em combinações fracas, previsíveis ou fora dos padrões mínimos exigidos. Além disso, em cenários de uso temporário ou consulta posterior, é necessário resgatar a chave gerada através de uma referência única.

## Objetivo
Prover uma interface de programação (API) simples e automatizada que:
1. Gere senhas seguras respeitando regras rígidas de composição e tamanho.
2. Armazene a senha associada a um identificador único de consulta.
3. Permita a recuperação do valor de uma senha gerada utilizando apenas seu identificador único.

## Usuários
- **Sistemas / Desenvolvedores:** Aplicações ou usuários finais buscando integração para geração rápida de credenciais de acesso seguras para fins lúdicos e educativos.

## Histórias de Usuário
- **US-01:** Como usuário da API, quero solicitar a geração de uma senha forte para receber uma combinação segura acompanhada de um código de identificação.
- **US-02:** Como usuário da API, quero consultar uma senha previamente criada informando seu código de identificação para recuperar o valor original da senha.

## Requisitos Funcionais
- **RF-01:** A aplicação deve gerar uma senha aleatória que atenda aos critérios de segurança estabelecidos.
- **RF-02:** A aplicação deve gerar e associar um identificador único universal (GUID) para cada nova senha criada.
- **RF-03:** A aplicação deve salvar o registro da senha gerada e seu identificador em armazenamento persistente.
- **RF-04:** A aplicação deve permitir a busca e exibição de um registro de senha através do identificador GUID fornecido.
- **RF-05:** A aplicação deve retornar uma notificação clara e padronizada de erro caso o formato do identificador consultado seja inválido.
- **RF-06:** A aplicação deve retornar uma resposta adequada indicando que a senha não foi localizada caso o identificador consultado não exista no sistema.

## Regras de Negócio
- **RN-01 (Tamanho Mínimo):** Toda senha gerada deve ter no mínimo 16 caracteres.
- **RN-02 (Complexidade):** Toda senha gerada deve conter obrigatoriamente caracteres especiais (ex.: `!`, `@`, `#`, `$`, `%`, etc.).
- **RN-03 (Proibição de Espaços):** A senha gerada não pode conter espaços em branco em nenhuma posição.
- **RN-04 (Imutabilidade):** Uma vez gerada e salva, a senha e seu identificador não podem ser alterados nem sobrescritos.

## Casos de Borda
- **CB-01 (Formato de Identificador Inválido):** O usuário envia uma string em formato diferente de um identificador GUID válido no momento da consulta. A API deve rejeitar a requisição imediatamente indicando erro nos parâmetros de entrada.
- **CB-02 (Identificador Inexistente):** O usuário envia um GUID com formato válido, mas que não corresponde a nenhuma senha registrada. O sistema deve indicar que a informação não foi encontrada.

## Fora de Escopo
- Autenticação e autorização de usuários para acessar os recursos da API.
- Criptografia, mascaramento ou hashing das senhas armazenadas (trata-se de uma aplicação lúdica).
- Funcionalidades de alteração, renovação ou exclusão de senhas.
- Expiração temporal ou limpeza automática de senhas salvas.

## Critérios de Aceite
- **CA-01:** Ao solicitar a criação de uma senha, o retorno deve conter o identificador GUID e uma string de no mínimo 16 caracteres com ao menos um caractere especial e nenhum espaço.
- **CA-02:** A consulta por um GUID válido previamente gerado deve retornar exatamente o mesmo valor de senha salvo.
- **CA-03:** A consulta por uma chave com formato inválido deve retornar código e mensagem de erro padronizados de validação.