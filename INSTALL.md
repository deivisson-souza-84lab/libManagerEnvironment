# Manual de Instalação

## Requisitos
Para esta instalação serão necessárias as ferramentas:
- Git
- Docker
- Docker Compose

## Instalação
O primeiro passo é clonar o projeto deste repositório.
```bash
$ git clone https://github.com/deivisson-souza-84lab/libManagerEnvironment.git libManagerEnvironment
```
Em seguida, caminhe para dentro da pasta do projeto.
```bash
$ cd libManagerEnvironment
```
Crie um arquivo `.env` a partir do arquivo `.env-example`. Nenhuma edição será necessária.
```bash
$ cp .env-example .env
```
Com o arquivo `.env` criado, inicie o docker compose.
```bash
$ docker compose up -d
```
Verifique o estado dos contêineres através do comando `ps`.
```bash
$ docker compose ps
```
O resultado será algo parecido com a informação abaixo. Note que todos os contêineres estão com a mensagem "Up" na coluna `STATUS`. Isso significa que subiram a X segundos. Já na coluna `NAME` pode-se verificar os contêineres ativos, que neste exemplo são: application, mailhog, mariadb e nginx.
```bash
NAME          IMAGE             COMMAND                  SERVICE       CREATED          STATUS          PORTS
application   application       "docker-php-entrypoi…"   application   30 seconds ago   Up 23 seconds   0.0.0.0:9000->9000/tcp
mailhog       mailhog/mailhog   "MailHog"                mailhog       30 seconds ago   Up 23 seconds   0.0.0.0:1025->1025/tcp, 0.0.0.0:8025->8025/tcp
mariadb       mariadb:11.3.2    "docker-entrypoint.s…"   mysql         30 seconds ago   Up 4 seconds    0.0.0.0:3306->3306/tcp
nginx         nginx:alpine      "/docker-entrypoint.…"   nginx         30 seconds ago   Up 23 seconds   0.0.0.0:8000->80/tcp
```
Com todos os contêineres online, o próximo passo será acessar o contêiner da aplicação para, dentro dele, baixar o projeto LibManager.
```bash
$ docker compose exec application bash
```
> A saída do comando acima será algo parecido com:
> ```bash
> 4dc83a933195:/var/www/app#
> ```
> Onde `4dc83a933195` é o ID do contêiner, `/var/www/app` é a pasta onde está atualmente e `#` que indica que está conectado como `superuser`

Dentro do contêiner da aplicação, vamos fazer o download do projeto `libManager` do repositório no GitHub. Note que o `.` no final do comando indica que o download deve ser feito diretamente nesta pasta.
```bash
4dc83a933195:/var/www/app# git clone https://github.com/deivisson-souza-84lab/libManager.git .
```
Com os arquivos baixados, crie um novo arquivo `.env`com base no arquivo `.env-example`.
```bash
4dc83a933195:/var/www/app# cp .env-example .env
```
Abra este novo arquivo `.env` e edite-o com as informações que estão mais abaixo.
```bash
4dc83a933195:/var/www/app# vi .env
```
Assim que abrir o `.env`através do `vi`, ele estará em modo leitura. Pressione a tecla **"Insert"**, isto habilitará o modo de edição do arquivo. Então busque os campos a seguir e deixe-os com os valores informados.
```
DB_CONNECTION=mysql
DB_HOST=${DB_HOST}
DB_PORT=${DB_PORT}
DB_DATABASE=${DB_NAME}
DB_USERNAME=${DB_USERNAME}
DB_PASSWORD=${DB_PASSWORD}
 
MAIL_HOST=${SMTP_HOST}
MAIL_PORT=${SMTP_PORT}
```
Feitas as modificações no arquivo `.env`, pressione a tecla **"Esc"** e então digite `:wq` e pressione **"Enter"**.

Depois disso, faça a instalação do projeto com o composer.
```bash
4dc83a933195:/var/www/app# composer install
```
Após a instalação, vamos gerar a chave de criptografia para a aplicação. Esta chave é utilizada pelo Laravel para diversas operações criptográficas.
```bash
4dc83a933195:/var/www/app# php artisan key:generate
```
Vamos também gerar uma `secret`. Este comando gera uma chave secreta que será usada para assinar os tokens JWT.
```bash
4dc83a933195:/var/www/app# php artisan jwt:secret
```
E, por fim, vamos executar as `migrations` para podermos criar toda a estrutura de dados da aplicação no MariaDB.
```bash
4dc83a933195:/var/www/app# php artisan migrate
```

> ## Opcional
> De modo opcional, você pode querer executar testes para ver se tudo está funcionando bem. Para isso, execute o comando abaixo.
> ```bash
> 4dc83a933195:/var/www/app# php artisan test
> ```
> 
> O resultado esperado será algo parecido com:
> ```
> PASS  Tests\Unit\ExampleTest
> ✓ that true is true																			0.45s
> 
> PASS  Tests\Feature\Api\ApiControllerTest
> ✓ register																					13.20s
> ✓ login																						3.52s
> ✓ invalid login																				0.21s
> ✓ profile																					0.18s
> ✓ refresh token																				0.34s
> ✓ logout																					0.30s
> 
> PASS  Tests\Feature\Api\AuthorsControllerTest
> ✓ index not authorized																		0.29s
> ✓ index not found																			0.35s
> ✓ store																						0.46s
> ✓ index																						0.37s
> ✓ show																						0.20s
> ✓ show not found																			0.19s
> ✓ destroy																					0.20s
> 
> PASS  Tests\Feature\Api\BooksControllerTest
> ✓ index not authorized																		0.41s
> ✓ index not found																			0.23s
> ✓ index																						0.29s
> ✓ store																						0.25s
> ✓ show																						0.21s
> ✓ show not found																			0.18s
> ✓ destroy																					0.20s
> 
> PASS  Tests\Feature\Api\LoansControllerTest
> ✓ index not authorized																		0.19s
> ✓ index not found																			0.20s
> ✓ index																						0.64s
> ✓ store																						10.19s
> ✓ show																						0.81s
> ✓ show not found																			0.43s
> ✓ destroy																					0.78s
> 
> Tests:																	28 passed (198 assertions)
> Duration:																					39.85s
> ```
> Após a execução dos testes o banco de dados será higienizado, o que significa que tadas as informações ali serão perdidas. Se desejar trabalhar com um volume de informações fakes para o desenvolvimento de um front-end para esta aplicação, por exemplo, pode usar os Seeder do Laravel que populam o banco através de Factories.
> Basta executar:
> ```bash
> php artisan db:seed
> ```
> Com esse comando a base de dados será populada e um novo usuário admin será criado para utilização.

Por fim, aqui a instalação já estará apta para utilização, contudo, não estará completa sem antes iniciarmos a fila de trabalho que está responsável pelo envio de e-mail quando um empréstimo for criado.

Há duas maneiras de se fazer isso. O comando abaixo vai abrir a fila de serviço no seu terminal, mas vai manter a seção ocupada. Se você só quiser ver a coisa acontecendo e não for utilizar o terminal, é uma boa opção. Utilize "**Ctrl+C**" para sair.
```bash
4dc83a933195:/var/www/app# php artisan queue:work -v
```

Caso queira iniciar a fila como um serviço, utilize o comando abaixo para iniciar um serviço que montamos utilizando o `supervisor` um recurso do Linux. A fila será iniciada e rodará em background.
```bash
nohup supervisord -c /etc/supervisord.conf &
```

Para ver os emails disparados acesse o mailhog no seu navegador em `http://localhost:8025`.
