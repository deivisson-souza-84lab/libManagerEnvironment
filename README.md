# Repositório LibManager

## Apresentação do Repositório
Este repositório contém a configuração necessária para a execução do projeto **LibManager** utilizando Docker e Docker Compose. O objetivo principal é fornecer um ambiente de desenvolvimento consistente e fácil de configurar, permitindo que os desenvolvedores possam rapidamente iniciar e trabalhar no projeto sem se preocupar com a instalação e configuração manual das dependências.

### Estrutura do Repositório
O repositório está organizado da seguinte forma:
-  `application/`: Diretório que contém o código-fonte do projeto LibManager.
-  `dockerfiles/`: Diretório que contém os Dockerfiles personalizados para a construção das imagens Docker.
-  `php/`: Dockerfile para a configuração do contêiner da aplicação PHP.
-  `nginx/`: Configuração do Nginx utilizada para servir a aplicação.
-  `databases/`: Diretório destinado a persistir os dados do banco de dados MariaDB.

### Papel Fundamental do Docker Compose
O arquivo `compose.yaml` é a peça central deste repositório. Ele orquestra a construção e execução dos diferentes serviços que compõem o ambiente de desenvolvimento do LibManager. O Docker Compose permite definir e gerenciar todos os contêineres necessários de forma declarativa e integrada. A seguir estão os principais serviços definidos no arquivo `compose.yaml`:
1.  **application**:
	- **Descrição**: Este serviço constrói e executa a aplicação PHP do LibManager.
	- **Configuração**:
		- Usa um Dockerfile personalizado localizado em `dockerfiles/php/Dockerfile`.
		- Mapeia a pasta `application` do host para o contêiner, permitindo o desenvolvimento em tempo real.
		- Define variáveis de ambiente necessárias para a aplicação, como detalhes do banco de dados e SMTP.

2.  **nginx**:
	-  **Descrição**: Servidor web Nginx que atua como proxy para a aplicação PHP.
	-  **Configuração**:
		- Utiliza a imagem `nginx:alpine`, uma versão leve do Nginx.
		- Mapeia a configuração personalizada do Nginx e o código da aplicação.

3.  **mysql**:
	-  **Descrição**: Banco de dados MariaDB utilizado pela aplicação.
	-  **Configuração**:
		- Usa a imagem `mariadb:11.3.2`.
		- Define variáveis de ambiente para configuração do banco de dados, como usuário, senha e nome do banco.
		- Mapeia um volume para persistir os dados do banco de dados.

4.  **mailhog**:
	-  **Descrição**: Serviço de captura de emails, utilizado para desenvolvimento.
	-  **Configuração**:
		- Usa a imagem `mailhog/mailhog`.
		- Expõe portas para acesso à interface web e SMTP.

### Rede
Todos os serviços definidos no `compose.yaml` estão conectados a uma rede Docker chamada `application`. Esta rede isolada permite que os contêineres se comuniquem entre si de forma segura e eficiente, sem interferir na rede do host.

### Conclusão
O uso do Docker Compose neste repositório simplifica enormemente o processo de configuração e execução do ambiente de desenvolvimento do LibManager. Com um único comando, todos os serviços necessários são iniciados e configurados, proporcionando um ambiente de desenvolvimento consistente e reproduzível.

Acompanhe nosso [manual de instalação](INSTALL.MD) e veja o passo a passo de como executar este projeto.