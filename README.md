# Spring Security API

Este projeto implementa autenticação e autorização usando Spring Security, JWT e OAuth2, permitindo o gerenciamento de usuários, autenticação via token JWT e controle de acesso baseado em roles. O sistema foi projetado para garantir segurança, escalabilidade e fácil manutenção, tornando-o adequado para aplicações empresariais e projetos pessoais. Além disso, oferece suporte para múltiplas configurações de autenticação e pode ser facilmente adaptado para diferentes cenários de segurança, atendendo desde pequenas aplicações a sistemas complexos de grande escala.

## Tecnologias Utilizadas
- Java 21
- Spring Boot 3
- Spring Security
- OAuth2
- JWT (JSON Web Token)
- MySQL
- Docker
- Maven
- Hibernate
- Postman (para testes de API)
- OpenSSL (para geração de chaves de segurança)

## Configuração do Ambiente

### Requisitos
Para executar este projeto, você precisará dos seguintes requisitos instalados em sua máquina:
- Docker e Docker Compose instalados
- JDK 21+
- Maven 3+
- MySQL Client (opcional para administração do banco de dados)
- Postman ou cURL para testes de endpoints
- OpenSSL para geração de chaves de segurança

### Subindo o Banco de Dados
O projeto utiliza um banco de dados MySQL em um contêiner Docker. Para iniciá-lo, execute:
```sh
docker-compose up -d
```
Isso criará e executará um contêiner MySQL configurado automaticamente. Caso seja necessário derrubar o contêiner posteriormente, utilize:
```sh
docker-compose down
```
Se precisar limpar os volumes do banco de dados, utilize:
```sh
docker-compose down -v
```

### Configuração do Banco de Dados (Docker Compose)
```yaml
services:
  mysql:
    image: mysql:8
    container_name: mysql-container
    restart: always
    ports:
      - "3307:3306"
    expose:
      - "3307"
    environment:
      MYSQL_USER: admin
      MYSQL_PASSWORD: 123
      MYSQL_DATABASE: mydb
      MYSQL_ROOT_PASSWORD: 123
```
Além disso, certifique-se de que a conexão com o banco está correta no `application.properties`:
```properties
spring.datasource.url=jdbc:mysql://localhost:3307/mydb
spring.datasource.username=admin
spring.datasource.password=123
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.jpa.hibernate.ddl-auto=update
```

### Configuração do JWT
Adicione as chaves RSA no `application.properties` ou `application.yml` para garantir a correta geração e validação dos tokens JWT.
```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: http://localhost:8081/realms/my-realm
jwt:
  public.key: classpath:public.pem
  private.key: classpath:private.pem
```
As chaves RSA podem ser geradas utilizando OpenSSL:
```sh
openssl genrsa -out private.pem 2048
openssl rsa -in private.pem -pubout -out public.pem
```
Coloque esses arquivos na pasta `resources` do projeto.

## Executando a Aplicação
Para rodar o projeto, utilize o Maven:
```sh
mvn spring-boot:run
```
Caso queira construir um JAR e executar manualmente:
```sh
mvn clean package
java -jar target/spring-security-api.jar
```
Se precisar rodar testes automatizados antes de subir a aplicação:
```sh
mvn verify
```

## Endpoints
A API conta com os seguintes endpoints para manipulação de usuários e tweets:

### Autenticação
- `POST /login` - Gera um token JWT para autenticação.

### Usuários
- `POST /users` - Cria um novo usuário.
- `GET /users` - Lista todos os usuários (somente para ADMIN).

### Tweets
- `POST /tweets` - Cria um novo tweet (usuário autenticado).
- `GET /feed` - Obtém o feed de tweets em paginação.
- `DELETE /tweets/{id}` - Remove um tweet (autor do tweet ou ADMIN).

## Estrutura do Projeto
O código está estruturado de forma modular, garantindo a organização e a fácil manutenção:
```
/src/main/java/tech/buildrun/springsecurity
├── config
│   ├── AdminUserConfig.java
│   ├── SecurityConfig.java
├── controller
│   ├── TokenController.java
│   ├── TweetController.java
│   ├── UserController.java
│   ├── dto
│   │   ├── CreateTweetDto.java
│   │   ├── CreateUserDto.java
│   │   ├── FeedDto.java
│   │   ├── FeedItemDto.java
│   │   ├── LoginRequest.java
│   │   ├── LoginResponse.java
├── entities
│   ├── Role.java
│   ├── Tweet.java
│   ├── User.java
├── repository
│   ├── RoleRepository.java
│   ├── TweetRepository.java
│   ├── UserRepository.java
```

## Testes
Os testes unitários e de integração são essenciais para garantir a confiabilidade do sistema. Utilize JUnit para rodá-los:
```sh
mvn test
```
Além disso, recomenda-se o uso do Postman ou cURL para testar os endpoints manualmente.

## Notas
- Para ambiente de produção, habilite a proteção CSRF no `SecurityConfig`.
- Utilize variáveis de ambiente para armazenar credenciais sensíveis.

## Autor
Projeto desenvolvido por Felipe(https://github.com/Felipesouzacunha).

