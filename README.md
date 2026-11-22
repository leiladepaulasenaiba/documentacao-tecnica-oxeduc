# Documentacao-tecnica-Oxeduc-Agendamento de Eventos Escolares
Documentação técnica do projeto Agendamento de Eventos Escolares, contendo informações sobre sua estrutura, tecnologias utilizadas, configuração do banco de dados, entidades, regras de negócio, endpoints, autenticação JWT e limitações conhecidas da aplicação.

# Agendamento de Eventos Escolares

API REST desenvolvida como atividade acadêmica da disciplina de Desenvolvimento de Sistemas, com o objetivo de gerenciar solicitações de reservas de espaços escolares para a realização de eventos.

---

## 1. Sobre o projeto

**Nome:** Agendamento de Eventos Escolares

**Objetivo:** Desenvolver uma API REST para gerenciar o agendamento de espaços escolares destinados à realização de eventos. O sistema permite que professores solicitem reservas e que coordenadores analisem essas solicitações, podendo aprová-las ou rejeitá-las.

**Estudante responsável:** Leila De Paula

---

## 2. Tecnologias utilizadas

O projeto foi desenvolvido utilizando as seguintes tecnologias:

| Tecnologia | Finalidade |
|---|---|
| Java | Linguagem de programação |
| Spring Boot | Estrutura principal da aplicação |
| Spring Web | Desenvolvimento da API REST |
| Spring Data JPA | Acesso e gerenciamento dos dados |
| Hibernate | Mapeamento objeto-relacional |
| H2 Database | Banco de dados relacional em memória |
| Spring Security | Autenticação e controle de acesso |
| JWT | Geração e validação de tokens de autenticação |
| Bean Validation | Validação dos dados recebidos pela API |
| Maven | Gerenciamento de dependências |
| Postman | Testes das requisições HTTP |
| IntelliJ IDEA | Ambiente de desenvolvimento |

---

## 3. Como executar a aplicação

### Pré-requisitos

Antes de executar o projeto, é necessário possuir:

- Java instalado;
- IntelliJ IDEA ou outra IDE compatível;
- Maven;
- Postman para testar os endpoints.

### Passos para execução

1. Baixe ou clone o repositório do projeto.
2. Abra o projeto na IDE.
3. Aguarde o Maven carregar as dependências.
4. Localize a classe principal `AgendamentoEscolarApplication`.
5. Execute a aplicação.

Após a inicialização, a API estará disponível no endereço:

```text
http://localhost:8085
```

---

## 4. Configuração do banco de dados

O projeto utiliza o banco de dados **H2 em memória**, configurado por meio do arquivo `application.properties`.

### Configurações utilizadas

```properties
spring.datasource.url=jdbc:h2:mem:agendamento_escolar_db
spring.datasource.username=sa
spring.datasource.password=
spring.datasource.driver-class-name=org.h2.Driver

spring.jpa.hibernate.ddl-auto=create-drop

spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
```

O banco de dados é criado automaticamente durante a execução da aplicação.

### Acesso ao console H2

O console pode ser acessado pelo endereço:

```text
http://localhost:8085/h2-console
```

**Observação:** por utilizar um banco de dados em memória, os registros são perdidos quando a aplicação é encerrada ou reiniciada.

---

## 5. Principais entidades e relacionamentos

O sistema possui quatro entidades principais:

| Entidade | Descrição |
|---|---|
| `Usuario` | Representa os usuários do sistema: professores, coordenadores e administradores. |
| `Espaco` | Representa os ambientes escolares disponíveis para reservas, como auditório, biblioteca e laboratórios. |
| `Evento` | Representa os eventos que serão realizados na instituição. |
| `Reserva` | Representa a solicitação de utilização de um espaço para determinado evento. |

### Relacionamentos

- **Usuário e Reserva (1:N):** um usuário pode solicitar várias reservas.
- **Usuário e Reserva (1:N):** um usuário pode ser responsável pela análise de várias reservas.
- **Espaço e Reserva (1:N):** um espaço pode possuir várias reservas em diferentes datas ou horários.
- **Evento e Reserva (1:1):** cada evento está associado a uma única reserva, conforme a regra implementada no projeto.

---

## 6. Regras de negócio implementadas

O sistema possui as seguintes regras de negócio:

- Professores podem solicitar reservas de espaços escolares.
- As reservas são cadastradas inicialmente com o status `PENDENTE`.
- Coordenadores podem consultar as reservas e atualizar seu status.
- As reservas podem apresentar os seguintes estados:
  - `PENDENTE`
  - `APROVADA`
  - `REJEITADA`
- O sistema verifica se já existe uma reserva para o mesmo espaço, na mesma data e em horário conflitante.
- Reservas rejeitadas não impedem novas solicitações para o mesmo horário.
- O sistema permite atualizar e excluir reservas, respeitando as permissões configuradas.
- Cada evento pode estar associado a apenas uma reserva, devido ao relacionamento 1:1 implementado.

---

## 7. Endpoints da API

**Endereço-base:**

```text
http://localhost:8085
```

Os endpoints estão organizados conforme as entidades do sistema.

### Usuários

| Método | Endpoint | Descrição |
|---|---|---|
| POST | `/usuarios` | Cadastrar usuário |
| POST | `/usuarios/login` | Realizar login |

### Espaços

| Método | Endpoint | Descrição |
|---|---|---|
| GET | `/espacos` | Listar espaços |
| GET | `/espacos/{id}` | Buscar espaço por ID |
| POST | `/espacos` | Cadastrar espaço |
| PUT | `/espacos/{id}` | Atualizar espaço |
| DELETE | `/espacos/{id}` | Excluir espaço |

### Eventos

| Método | Endpoint | Descrição |
|---|---|---|
| GET | `/eventos` | Listar eventos |
| GET | `/eventos/{id}` | Buscar evento por ID |
| POST | `/eventos` | Cadastrar evento |
| PUT | `/eventos/{id}` | Atualizar evento |
| DELETE | `/eventos/{id}` | Excluir evento |

### Reservas

| Método | Endpoint | Descrição |
|---|---|---|
| GET | `/reservas` | Listar reservas |
| GET | `/reservas/{id}` | Buscar reserva por ID |
| POST | `/reservas` | Cadastrar reserva |
| PUT | `/reservas/{id}` | Atualizar reserva |
| DELETE | `/reservas/{id}` | Excluir reserva |

---

## 8. Exemplos de requisição

Os exemplos abaixo demonstram algumas operações disponíveis na API.

### 8.1 Cadastro de usuário

**Método:** `POST`

**Endpoint:** `/usuarios`

**Corpo da requisição:**

```json
{
  "nome": "João Silva",
  "email": "joao@escola.com",
  "senha": "123456",
  "perfil": "PROFESSOR",
  "ativo": true
}
```

### 8.2 Login de usuário

**Método:** `POST`

**Endpoint:** `/usuarios/login`

**Corpo da requisição:**

```json
{
  "email": "joao@escola.com",
  "senha": "123456"
}
```

A resposta retorna um token JWT, que deverá ser utilizado nas requisições protegidas.

### 8.3 Cadastro de reserva

**Método:** `POST`

**Endpoint:** `/reservas`

**Corpo da requisição:**

```json
{
  "dataSolicitacao": "2026-11-21",
  "dataEvento": "2026-12-20",
  "horarioInicio": "09:00:00",
  "horarioFim": "12:00:00",
  "observacao": "Reserva para a Mostra Cultural",
  "usuarioSolicitanteId": 4,
  "espacoId": 4,
  "eventoId": 4
}
```

**Importante:** os identificadores informados devem corresponder a registros existentes no banco de dados. Além disso, o evento não pode estar associado a outra reserva.

---

## 9. Autenticação e controle de permissões

A API utiliza autenticação por **JWT (JSON Web Token)** para controlar o acesso aos endpoints protegidos.

### Como realizar a autenticação

1. Realize o cadastro do usuário, caso ainda não esteja cadastrado.
2. Envie uma requisição `POST` para `/usuarios/login`, informando e-mail e senha.
3. Copie o token JWT retornado pela API.
4. No Postman, acesse a opção **Authorization**.
5. Selecione o tipo **Bearer Token**.
6. Cole o token recebido no campo correspondente.
7. Envie a requisição para acessar o endpoint protegido.

### Perfis de acesso

O sistema possui três perfis:

| Perfil | Permissões principais |
|---|---|
| `PROFESSOR` | Pode solicitar reservas de espaços escolares. |
| `COORDENADOR` | Pode consultar e atualizar reservas, incluindo a aprovação ou rejeição das solicitações. |
| `ADMINISTRADOR` | Pode solicitar, consultar, atualizar e excluir reservas. |

Os demais endpoints protegidos exigem autenticação JWT, sem restrição específica de perfil na configuração atual.

Os seguintes endpoints são públicos:

- `POST /usuarios` — cadastro de usuário.
- `POST /usuarios/login` — autenticação.

### Observação de segurança

Atualmente, o cadastro público permite informar o perfil do usuário no momento do registro. Portanto, a implementação ainda não impede que um usuário tente se cadastrar com o perfil de administrador.

---

## 10. Tratamento de erros da API

A aplicação possui uma classe `GlobalExceptionHandler`, responsável por centralizar o tratamento de exceções.

Atualmente, contempla os seguintes casos:

| Situação | Código HTTP | Descrição |
|---|---|---|
| Registro não encontrado | 404 — NOT FOUND | Registro solicitado não localizado. |
| Conflito de horário de reserva | 409 — CONFLICT | Espaço já reservado em horário conflitante. |
| Outras exceções RuntimeException | 400 — BAD REQUEST | Requisição não processada devido a uma exceção tratada. |

**Observação:** os erros específicos de validação dos dados ainda não possuem tratamento personalizado.

---

## 11. Limitações conhecidas

A implementação possui algumas limitações que podem ser consideradas em futuras melhorias:

- O banco H2 é em memória, portanto os dados são perdidos ao encerrar ou reiniciar a aplicação.
- O projeto utiliza um banco de dados de desenvolvimento, não uma base de dados persistente de produção.
- O relacionamento entre Evento e Reserva permite apenas uma reserva por evento.
- O cadastro público permite informar o perfil do usuário, sem uma restrição específica para impedir o cadastro como administrador.
- O tratamento global de erros ainda não possui tratamento personalizado para erros de validação.
- A aplicação foi testada manualmente utilizando o Postman.

---

## 12. Considerações finais

O projeto **Agendamento de Eventos Escolares** foi desenvolvido com o objetivo de aplicar os conhecimentos de desenvolvimento de APIs REST utilizando Java e Spring Boot.

A implementação contempla o gerenciamento de usuários, espaços, eventos e reservas, além de autenticação JWT, controle de permissões por perfil, relacionamentos entre entidades e regras de negócio relacionadas ao processo de agendamento.

Esta documentação apresenta as principais características técnicas da aplicação, suas configurações, funcionalidades, endpoints e limitações conhecidas.

---

**Projeto desenvolvido como atividade acadêmica — Situação de Aprendizagem 4.**
