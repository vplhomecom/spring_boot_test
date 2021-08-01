# REST API   - Project
Esta aplicação está rodando online em duas versões:
- InMemory database - **H2**
- MySQL server - **MariaDB**

## Configurações do H2 database
Para uso com o **H2 database** algumas vezes será necessário configurar o endereço url e senha de acesso da base de dados em questão.
```
JDBC URL: jdbc:h2:mem:testdb
Password: 123
```
Não há necessidade de modificar as outras configurações. Esta base de dados está disponível no endereço *http://vplhome.com:8080/apitasksh2/h2-db*.

## Swagger - Informações

Foi o **Swagger2** para fazer a documentação da aplicação. Tal repositório está disponível em *http://vplhome.com:8080/apitasksh2/swagger-ui/index.html* [**H2**] e *http://vplhome.com:8080/apitaskssql/swagger-ui/index.html* [**MariaDB**].
Para realizar as implementações disponibilizadas na interface é necessário estar autendicado.
A exceção são os casos de autenticação e cadastro de usuário (username é o e-mail). Foi definido desta forma, pois é o nome padrão do parâmetro no *Spring Security*.

## Controle de autenticação

- Descrição: **Validar credenciais de usuário**
  - Método: **POST**
  - URL: `http://vplhome.com:8080/apitasksh2/auth`
  - Json:
```
{
  "password": "string",
  "username": "string"
}
```
- Descrição: **Recuperar senha do usuário**
  - Método: **PUT**
  - URL: `http://vplhome.com:8080/apitasksh2/auth`
  - Json:
```
{
  "email": "string"
}
```
- Descrição: **Validar e-mail do usuário**
  - Método: **GET**
  - URL: `http://vplhome.com:8080/apitasksh2/auth/{token}`

## Controle de Usuarios

- Descrição: **Listar todos os usuários com paginação**
  - Método: **GET**
  - URL: `http://vplhome.com:8080/apitasksh2/api/user?page=3&size=1&sort=id`
  - Parâmetros: `page`, `size`, `sort`
- Descrição: **Buscar usuários por ID**
  - Método: **GET**
  - URL: `/api/user/{id}`
- Descrição: **Cadastrar novo usuário**
  - Método: **POST**
  - URL: `http://vplhome.com:8080/apitasksh2/api/user`
  - Json:
```
{
  "cpf": "string",
  "email": "string",
  "name": "string",
  "password": "string"
}
```
- Descrição: **Atualizar usuário logado**
  - Método: **PUT**
  - URL: `http://vplhome.com:8080/apitasksh2/api/user`
  - Json:
```
{
  "cpf": "string",
  "email": "string",
  "name": "string",
  "password": "string"
}
```
- Descrição: **Atualizar usuário por ID**
  - Método: **PUT**
  - URL: `http://vplhome.com:8080/apitasksh2/api/user/{id}`
  - Json:
```
{
  "cpf": "string",
  "email": "string",
  "name": "string",
  "password": "string",
  "role": "ADMIN",
  "status": "ATIVO"
}
```
- Descrição: **Deletar usuário por ID**
  - Método: **DELETE**
  - URL: `http://vplhome.com:8080/apitasksh2/api/user/{id}`

## Controle de Tarefas

- Descrição: **Listar tarefas do usuário logado com paginação**
  - Método: **GET**
  - URL: `http://vplhome.com:8080/apitasksh2/api/task/?page=0&size=10&sort=id`
  - Parâmetros: `page`, `size`, `sort`
- Descrição: **Buscar tarefa por ID**
  - Método: **GET**
  - URL: `http://vplhome.com:8080/apitasksh2/api/task/{id}`
- Descrição: **Listar total de tarefas do usuário por Status**
  - Método: **GET**
  - URL: `http://vplhome.com:8080/apitasksh2/api/task/count`
- Descrição: **Cadastrar nova tarefa**
  - Método: **POST**
  - URL: `http://vplhome.com:8080/apitasksh2/api/task`
  - Json:
```
{
  "detail": "string"
}
```
- Descrição: **Atualizar tarefa por ID**
  - Método: **PUT**
  - URL: `http://vplhome.com:8080/apitasksh2/api/task/{id}`
  - Json:
```
{
  "detail": "string",
  "status": "CONCLUIDA"
}
```
- Descrição: **Deletar tarefa por ID**
  - Método: **DELETE**
  - URL: `http://vplhome.com:8080/apitasksh2/api/task/{id}`
  
## Regras de negócio
Quando utilizando o Swagger para ativar as autorizações é necessário **validar as credenciais** e adicionar o **Token** recebido em "*Available authorizations*".
É essencial que o Token seja precedido da expressão "Bearer ", exemplo: `Bearer TOKEN`
### Da autenticação
- Todo novo usuário cadastrado precisa validar seu e-mail, caso contrário não conseguirá ser autenticado no sistema;
- Quando um usuário é logado no sistema, o mesmo receberá um **Token** com validade de 10 minutos. Contudo, toda ação na API que requira autenticação devolve ao requerente um novo Token atualizado;
- Caso a senha tenha sido esquecida, poderá ser recuperada com o uso do e-mail.
### Do usuário
- Existem dois papeis definidos para os usuários (`ADMIN` ou `USER`);
- Apenas `ADMIN` consegue: **listar todos ou qualquer usuário**, **atualizar qualquer usuário**, **deletar usuários**;
- O `USER` consegue executar apenas ações relativas a ele, não podendo se **deletar** ou **atualizar seus status**;
- Qualquer acesso poderá se cadastrar no sistema.
### Da tarefa
- Qualquer usuário pode adicionar uma tarefa;
- Toda nova tarefa é criada como `PENDENTE`;
- Apenas `ADMIN` pode executar ações sobre todas as tarefa;
- O `USER` pode aplicar ações apenas sobre suas tarefas;
