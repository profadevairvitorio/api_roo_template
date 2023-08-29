Para Gerar uma nova API Ruby on Rails sem Minitest
```shell
docker-compose run --no-deps web rails new . --api --force --database=postgresql -T
```

Se você trabalha com minitest, pode remover o sinalizador `-T` do comando.

Isto é o que acontece quando você executa este comando:

1. Ele constrói uma imagem baseada no Dockerfile
2. Ele cria um novo contêiner baseado nessa imagem
3. Executa `rails new` dentro do contêiner

`rails new` atualiza o Gemfile, então precisaremos construir a imagem novamente:

```shell
docker-compose build
```

Ajuste o `config/database.yml`. Em vez de apontar para localhost (rails
padrão) deve apontar para o contêiner `db`. O nome de usuário e a senha também
precisa ser ajustado aos padrões definidos pela imagem `postgres`.

```
default: &default
  adapter: postgresql
  encoding: unicode
  host: db
  username: postgres
  password: password
  pool: 5

development:
  <<: *default
  database: myapp_development


test:
  <<: *default
  database: myapp_test
```

Crie o banco de dados
```shell
docker-compose run web rake db:create
```

E finalmente, você pode inicializar o aplicativo:

```shell
docker-compose up
```

Isso vai:

1. Criar uma rede para conectar o contêiner do aplicativo (web) ao banco de dados (postgres)
2. Criar o contêiner postgres
3. Criar o contêiner da web
4. Iniciar os dois contêineres

Referência: [API Ruby on Rails + Docker + PostgreSQL](https://docs.docker.com/samples/rails/)
