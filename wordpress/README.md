# [WordPress](https://wordpress.org/)

WordPress é um sistema livre e aberto de gestão de conteúdo para internet (do inglês: Content Management System - CMS), baseado em PHP com banco de dados MySQL, executado em um servidor interpretador, voltado principalmente para a criação de páginas eletrônicas (sites) e blogs online.
<br/>
O WordPress combina simplicidade para usuários e editores com complexidade para os desenvolvedores. Você também é livre para fazer o que quiser com o código WordPress, estendê-lo ou modificá-lo de qualquer maneira ou usá-lo para projetos comerciais sem qualquer taxa de licenciamento.<br/>
Alguns dos recursos que o WordPress oferece:
*   Flexibilidade - Com o WordPress você pode criar qualquer tipo de site: um blog ou site pessoal, um site de negócios, um portfólio profissional, um site governamental, um site de notícias ou tipo revista, uma comunidade on-line ou até mesmo uma rede de sites;
*   Gerenciamento de usuários - Nem todo mundo precisa do mesmo nível de acesso ao seu site. Administradores gerenciam o site, editores trabalham com conteúdo, autores e contribuidores criam o conteúdo e assinantes tem um perfil que eles podem gerenciar;
*   Total conformidade com os padrões - Todo trecho de código que é gerado pelo WordPress está em total conformidade com os padrões definidos pelo W3C. Isto significa que o seu site funcionará em todos os navegadores atuais, ao mesmo tempo em que já mantém compatibilidade com a próxima geração;
*   Sistema de plugins - As APIs do WordPress tornam possível a criação de plugins para estendê-lo. A extensibilidade do WordPress se baseia nos milhares de hooks à sua disposição;
*   Estrutura para apps - O WordPress fornece muitos dos recursos que o seu aplicativo precisará: traduções, gerenciamento de usuários, solicitações HTTP, bancos de dados, roteamento de URLs e muito, muito mais. Você também pode usar uma API REST para interagir com a sua aplicação.

## Instalação

Os passos abaixo representam a instalação do WordPress em máquinas com sistema operacional Linux. 

### 1. Instale o servidor HTTP Apache2
```
$ sudo apt update
$ sudo apt upgrade
$ sudo apt install apache2
```
Após a instalação, reinicie o Apache e configure para que o mesmo sempre inicie quando o computador for ligado:

```
$ sudo service apache2 stop
$ sudo service apache2 start
$ sudo service apache2 enable
```

Você pode conferir se o Apache2 foi instalado com sucesso abrindo a url ```http://localhost``` no navegador. Caso a página abaixo apareça, o Apache2 foi instalado com sucesso.

![Apache2 homepage](https://raw.githubusercontent.com/ericfonsecalima/dio-first-repo-github-challenge/master/wordpress/assets/img/apache-home.png)

### 2. Instale o PHP 7.0 e algumas dependências

O Pico utiliza o PHP como back-end, então precisamos instalá-lo. Execute os seguintes comandos para indicar o repositório do PHP e instalar as dependências necessárias:
```
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository ppa:ondrej/php
$ sudo apt update
$ sudo apt upgrade
$ sudo apt install php7.0 libapache2-mod-php7.0 php7.0-common php7.0-mbstring php7.0-xmlrpc php7.0-sqlite3 php7.0-soap php7.0-gd php7.0-xml php7.0-cli php7.0-curl php7.0-zip
```

Após a instalação, execute o seguinte comando para abrir o arquivo de configuração do PHP para o Apache2:

``` 
$ sudo nano /etc/php/7.0/apache2/php.ini 
```

Atualize o arquivo com a configuração abaixo:

```
file_uploads = On
allow_url_fopen = On
short_open_tag = On
memory_limit = 256M
upload_max_filesize = 100M
max_execution_time = 360
date.timezone = America/Chicago
```

Reinicie o servidor Apache2 para que as configurações sejam salvas com sucesso:

```
$ sudo service apache2 restart 
```

### 3. Instale o MariaDB Database Server e Client

```
$ sudo apt-get install mariadb-server mariadb-client
```

Após a instalação, reinicie o MariaDB e configure para que o mesmo sempre inicie quando o computador for ligado:

```
$ sudo service mysql stop
$ sudo service mysql start
$ sudo service mysql enable
```
Em seguida, inicie a configuração de segurança criando um usuário root e desabilitando o acesso remoto ao MariaDB Server:
```
$ sudo mysql_secure_installation
```

Quando solicitado, responda as seguintes perguntas:
* Enter current password for root (enter for none): Aperte ENTER
* Set root password? [Y/n]: Y
* New password: Digite uma senha
* Re-enter new password: Repita a senha
* Remove anonymous users? [Y/n]: Y
* Disallow root login remotely? [Y/n]: Y
* Remove test database and access to it? [Y/n]:  Y
* Reload privilege tables now? [Y/n]:  Y

Reinicie o MariaDB Server:

```
$ sudo service mysql restart
```
Para testar se o MariaDB está instalado, digite o comando a seguir para logar-se no servidor MariaDB, em seguida insira a senha definida no passo anterior:
```
$ sudo mysql -u root -p
```
Se tudo deu certo, você verá a tela de boas vindas do MariaDB:

![MariaDB](https://raw.githubusercontent.com/ericfonsecalima/dio-first-repo-github-challenge/master/wordpress/assets/img/mariadb-home.png)

Crie um banco de dados chamado wordpress:

```
CREATE DATABASE wordpress;
```
Crie um usuário chamado wordpress com uma nova senha:
```
CREATE USER 'wordpress'@'localhost' IDENTIFIED BY 'nova_senha_aqui';
```
Garanta acesso total ao bando de dados para usuário recém criado:
```
GRANT ALL ON wordpress.* TO 'wordpress'@'localhost' IDENTIFIED BY 'senha_do_usuario_aqui' WITH GRANT OPTION;
```
Finalmente, salve suas alterações e saia:
```
FLUSH PRIVILEGES;
EXIT;
```

### Instale o WordPress
Baixe, extraia e mova os arquivos do WordPress para a pasta do Apache:
```
$ cd /tmp && wget https://wordpress.org/latest.tar.gz
$ tar -zxvf latest.tar.gz
$ sudo mv wordpress /var/www/html/wordpress
```
Execute os seguintes comandos para habilitar as permissões corretas para o WordPress:
```
$ sudo chown -R www-data:www-data /var/www/html/wordpress/
$ sudo chmod -R 755 /var/www/html/wordpress/
```
Agora crie o arquivo de configuração do WordPress `wp-config.php`, usando os seguintes comandos:
```
$ sudo mv /var/www/html/wordpress/wp-config-sample.php /var/www/html/wordpress/wp-config.php
```
Edite esse arquivo de configuração utilizando o `nano`:
```
$ sudo nano /var/www/html/wordpress/wp-config.php
```
Insira os dados de acordo com o que foi utilizado quando criado o banco de dados:
```
// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', 'wordpress');

/** MySQL database username */
define('DB_USER', 'wordpress');

/** MySQL database password */
define('DB_PASSWORD', 'senha_do_usuario_aqui');

/** MySQL hostname */
define('DB_HOST', 'localhost');

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');
```
Salve e reinicie o Apache. Em seguida, inicie a configuração do site/blog através do navegador, utilizando o endereço `localhost`, siga as instruções na tela e o WordPress estará pronto para ser utilizado.
![WordPress install](https://raw.githubusercontent.com/ericfonsecalima/dio-first-repo-github-challenge/master/wordpress/assets/img/wp-install.png)

Parabéns! Você instalou com sucesso o WordPress no linux com Apache2, MariaDB e PHP.
![WordPress success](https://raw.githubusercontent.com/ericfonsecalima/dio-first-repo-github-challenge/master/wordpress/assets/img/wp-home.png)


## Atualizando

### Usando o Atualizador Automático

1.  Abra [wp-admin/update-core.php](https://github.com/WordPress/WordPress/blob/master/wp-admin/update-core.php) no seu navegador e siga as instruções.
2.  Você queria mais, talvez? É isso aí!

### Atualizando Manualmente

1.  Antes de atualizar qualquer coisa, verifique se você tem cópias de backup de todos os arquivos que você pode ter modificado, como `index.php`.
2.  Exclua seus arquivos antigos do WordPress, salvando os que você modificou.
3.  Carregue os novos arquivos.
4.  Aponte seu navegador para [/wp-admin/upgrade.php](https://github.com/WordPress/WordPress/blob/master/wp-admin/upgrade.php).

## Migrando de outros sistemas

O WordPress pode [importar de vários sistemas](https://wordpress.org/support/article/importing-content/). Primeiro, você precisa instalar o WordPress e funcionar como descrito acima, antes de usar [as ferramentas de importação](https://github.com/WordPress/WordPress/blob/master/wp-admin/import.php).

## Requisitos de Sistema

*   [PHP](https://secure.php.net/) versão **5.6.20** ou mais recente.
*   [MySQL](https://www.mysql.com/) versão **5.0** ou mais recente.

### Recomendações

*   [PHP](https://secure.php.net/) versão **7.3** ou mais recente.
*   [MySQL](https://www.mysql.com/) versão **5.6** ou mais recente.
*   O módulo [mod_rewrite](https://httpd.apache.org/docs/2.2/mod/mod_rewrite.html) do Apache.
*   Suporte [HTTPS](https://wordpress.org/news/2016/12/moving-toward-ssl/).
*   Um link para [wordpress.org](https://wordpress.org/) no seu site.

## Recursos Online

Se você tiver alguma dúvida que não seja abordada neste documento, aproveite os inúmeros recursos on-line do WordPress:

<dl>

<dt>[The WordPress Codex](https://codex.wordpress.org/)</dt>

<dd>O Codex é a enciclopédia de todas as coisas do WordPress. É a fonte mais abrangente de informações para o WordPress disponível.</dd>

<dt>[The WordPress Blog](https://wordpress.org/news/)</dt>

<dd>É aqui que você encontra as últimas atualizações e notícias relacionadas ao WordPress. Notícias recentes do WordPress aparecem no seu painel administrativo por padrão.</dd>

<dt>[WordPress Planet](https://planet.wordpress.org/)</dt>

<dd>O WordPress Planet é um agregador de notícias que reúne postagens de blogs WordPress em toda a web.</dd>

<dt>[WordPress Support Forums](https://wordpress.org/support/forums/)</dt>

<dd>Se você já procurou todos os lugares e ainda não consegue encontrar uma resposta, os fóruns de suporte são muito ativos e têm uma grande comunidade pronta para ajudar. Para ajudá-los, use um título de tópico descritivo e descreva sua pergunta com o máximo de detalhes possível.</dd>

<dt>[WordPress <abbr>IRC</abbr> (Internet Relay Chat) Channel](https://codex.wordpress.org/IRC)</dt>

<dd>Existe um canal de bate-papo on-line que é usado para discussão entre pessoas que usam o WordPress e ocasionalmente dão suporte a tópicos. A página wiki acima deve apontar você na direção certa. ([irc.freenode.net #wordpress](irc://irc.freenode.net/wordpress))</dd>

</dl>

## Notas Finais

*   Se você tiver alguma sugestão, idéia ou comentário, ou se você encontrou um bug, junte-se ao WordPress nos [Support Forums](https://wordpress.org/support/forums/).
*   O WordPress possui um plugin robusto <abbr>API</abbr> (Application Programming Interface) que facilita a extensão do código. Se você é um desenvolvedor interessado em utilizar isso, consulte o [Manual do desenvolvedor de plug-ins](https://developer.wordpress.org/plugins/). Você não deve modificar nenhum código principal.


## Licença

O WordPress é um software livre, lançado sob os termos da <abbr>GPL</abbr> (GNU General Public License) versão 2 ou (a seu critério) qualquer versão posterior. Veja o [license.txt](https://github.com/WordPress/WordPress/blob/master/license.txt).