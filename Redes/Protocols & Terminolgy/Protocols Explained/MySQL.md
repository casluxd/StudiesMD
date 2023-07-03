`MySQL` é um sistema de gerenciamento de banco de dados relacional SQL de código aberto desenvolvido e suportado pelo Oracle. Um banco de dados é simplesmente uma coleção estruturada de dados organizada para fácil uso e recuperação. O sistema de banco de dados pode processar rapidamente grandes quantidades de dados com alto desempenho. Dentro do banco de dados, o armazenamento de dados é feito de maneira a ocupar o mínimo de espaço possível. O banco de dados é controlado usando o [Linguagem de banco de dados SQL](https://www.w3schools.com/sql/sql_intro.asp). O MySQL funciona de acordo com o `client-server principle` e consiste em um servidor MySQL e um ou mais clientes MySQL. O servidor MySQL é o sistema de gerenciamento de banco de dados real. Ele cuida do armazenamento e distribuição de dados. Os dados são armazenados em tabelas com diferentes colunas, linhas e tipos de dados. Esses bancos de dados geralmente são armazenados em um único arquivo com a extensão do arquivo `.sql`, por exemplo, como `wordpress.sql`.

#### Clientes MySQL

Os clientes MySQL podem recuperar e editar os dados usando consultas estruturadas ao mecanismo de banco de dados. A inserção, exclusão, modificação e recuperação de dados é feita usando a linguagem do banco de dados SQL. Portanto, o MySQL é adequado para gerenciar muitos bancos de dados diferentes para os quais os clientes podem enviar várias consultas simultaneamente. Dependendo do uso do banco de dados, o acesso é possível através de uma rede interna ou da Internet pública.

Um dos melhores exemplos de uso do banco de dados é o CMS WordPress. O WordPress armazena todas as postagens, nomes de usuário e senhas criadas em seu próprio banco de dados, acessível apenas pelo host local. No entanto, conforme explicado em mais detalhes no módulo [Introdução aos aplicativos da Web](https://academy.hackthebox.com/course/preview/introduction-to-web-applications), também existem estruturas de banco de dados distribuídas em vários servidores.

#### Bancos de dados MySQL

O MySQL é ideal para aplicativos como `dynamic websites`, onde sintaxe eficiente e alta velocidade de resposta são essenciais. É frequentemente combinado com um sistema operacional Linux, PHP e um servidor web Apache e também é conhecido nessa combinação como [LAMP](https://en.wikipedia.org/wiki/LAMP_(software_bundle)) ( Linux, Apache, MySQL, PHP ) ou ao usar Nginx, como [LEMP](https://lemp.io/). Em uma hospedagem na web com banco de dados MySQL, isso serve como uma instância central na qual o conteúdo exigido pelos scripts PHP é armazenado.

Dados confidenciais, como senhas, podem ser armazenados em sua forma de texto sem formatação pelo MySQL; no entanto, eles geralmente são criptografados previamente pelos scripts PHP usando métodos seguros, como [One-way Encription](https://en.citizendium.org/wiki/One-way_encryption).

#### Comandos MySQL

Um banco de dados MySQL traduz os comandos internamente em código executável e executa as ações solicitadas. O aplicativo da web informa o usuário se ocorrer um erro durante o processamento, que vários `SQL injections` pode provocar. Freqüentemente, essas descrições de erro contêm informações importantes e confirmam, entre outras coisas, que o aplicativo da Web interage com o banco de dados de uma maneira diferente da pretendida pelos desenvolvedores.

O aplicativo da web envia as informações geradas de volta ao cliente se os dados forem processados corretamente. Essas informações podem ser os extratos de dados de uma tabela ou registros necessários para processamento adicional com logins, funções de pesquisa etc. Os comandos SQL podem exibir, modificar, adicionar ou excluir linhas nas tabelas. Além disso, o SQL também pode alterar a estrutura das tabelas, criar ou excluir relacionamentos e índices e gerenciar usuários.

`MariaDB`, que geralmente é conectado ao MySQL, é um fork do código MySQL original. Isso ocorre porque o desenvolvedor principal do MySQL deixou a empresa `MySQL AB` depois de ter sido adquirido por `Oracle` e desenvolveu outro sistema de gerenciamento de banco de dados SQL de código aberto com base no código fonte do MySQL e o chamou de MariaDB.

## Configuração padrão

O gerenciamento de bancos de dados SQL e suas configurações é um vasto tópico. É tão grande que profissões inteiras, como `database administrator`, lide com quase nada além de bancos de dados. Essas estruturas se tornam muito grandes rapidamente e seu planejamento pode se tornar complicado. Entre outras coisas, o gerenciamento de DB é uma competência essencial para `software developers` mas também `information security analysts`. Cobrir completamente essa área iria além do escopo deste módulo. Portanto, recomendamos configurar uma instância MySQL / MariaDB para experimentar as várias configurações para entender melhor a funcionalidade e as opções de configuração disponíveis. Vamos dar uma olhada na configuração padrão do MySQL.

#### Configuração padrão

```shell-session
casluxd@htb[/htb]$ sudo apt install mysql-server -y
casluxd@htb[/htb]$ cat /etc/mysql/mysql.conf.d/mysqld.cnf | grep -v "#" | sed -r '/^\s*$/d'

[client]
port		= 3306
socket		= /var/run/mysqld/mysqld.sock

[mysqld_safe]
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
nice		= 0

[mysqld]
skip-host-cache
skip-name-resolve
user		= mysql
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
port		= 3306
basedir		= /usr
datadir		= /var/lib/mysql
tmpdir		= /tmp
lc-messages-dir	= /usr/share/mysql
explicit_defaults_for_timestamp

symbolic-links=0

!includedir /etc/mysql/conf.d/
```

## Configurações perigosas

Muitas coisas podem ser mal configuradas com o MySQL. Podemos olhar com mais detalhes para o [Referência MySQL](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html) para determinar quais opções podem ser feitas na configuração do servidor. As principais opções relevantes para a segurança são:

|**Configurações**|**Descrição**|
|---|---|
|`user`|Define como usuário o serviço MySQL será executado.|
|`password`|Define a senha para o usuário MySQL.|
|`admin_address`|O endereço IP no qual ouvir as conexões TCP / IP na interface administrativa da rede.|
|`debug`|Essa variável indica as configurações atuais de depuração|
|`sql_warnings`|Essa variável controla se as instruções INSERT de linha única produzem uma sequência de informações se ocorrerem avisos.|
|`secure_file_priv`|Essa variável é usada para limitar o efeito das operações de importação e exportação de dados.|

As configurações `user`, `password`, e `admin_address` são relevantes para a segurança porque as entradas são feitas em texto sem formatação. Freqüentemente, os direitos para o arquivo de configuração do servidor MySQL não são atribuídos corretamente. Se conseguirmos outra maneira de ler arquivos ou até um shell, podemos ver o arquivo, o nome de usuário e a senha do servidor MySQL. Suponha que não haja outras medidas de segurança para impedir o acesso não autorizado. Nesse caso, todo o banco de dados e todas as informações, endereços de email, senhas e dados pessoais dos clientes existentes podem ser visualizados e até editados.

O `debug` e `sql_warnings` as configurações fornecem saída de informações verbais em caso de erros, essenciais para o administrador, mas não devem ser vistas por outras pessoas. Essas informações geralmente contêm conteúdo sensível, que pode ser detectado por tentativa e erro para identificar outras possibilidades de ataque. Essas mensagens de erro geralmente são exibidas diretamente em aplicativos da web. Consequentemente, as injeções de SQL podem ser manipuladas até para que o servidor MySQL execute comandos do sistema. Isso é discutido e mostrado no módulo [Fundamentos da injeção de SQL](https://academy.hackthebox.com/course/preview/sql-injection-fundamentals) e [SQLMap Essentials](https://academy.hackthebox.com/course/preview/sqlmap-essentials).

## Footprinting

Há muitas razões pelas quais um servidor MySQL pode ser acessado a partir de uma rede externa. No entanto, está longe de ser uma das melhores práticas e sempre podemos encontrar bancos de dados que podemos alcançar. Freqüentemente, essas configurações eram apenas temporárias, mas eram esquecidas pelos administradores. Essa configuração do servidor também pode ser usada como uma solução alternativa devido a um problema técnico. Normalmente, o servidor MySQL é executado em `TCP port 3306`, e podemos verificar esta porta com `Nmap` para obter informações mais detalhadas.

#### Digitalizando o MySQL Server

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql*

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-21 00:53 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00021s latency).

PORT     STATE SERVICE     VERSION
3306/tcp open  nagios-nsca Nagios NSCA
| mysql-brute: 
|   Accounts: 
|     root:<empty> - Valid credentials
|_  Statistics: Performed 45010 guesses in 5 seconds, average tps: 9002.0
|_mysql-databases: ERROR: Script execution failed (use -d to debug)
|_mysql-dump-hashes: ERROR: Script execution failed (use -d to debug)
| mysql-empty-password: 
|_  root account has empty password
| mysql-enum: 
|   Valid usernames: 
|     root:<empty> - Valid credentials
|     netadmin:<empty> - Valid credentials
|     guest:<empty> - Valid credentials
|     user:<empty> - Valid credentials
|     web:<empty> - Valid credentials
|     sysadmin:<empty> - Valid credentials
|     administrator:<empty> - Valid credentials
|     webadmin:<empty> - Valid credentials
|     admin:<empty> - Valid credentials
|     test:<empty> - Valid credentials
|_  Statistics: Performed 10 guesses in 1 seconds, average tps: 10.0
| mysql-info: 
|   Protocol: 10
|   Version: 8.0.26-0ubuntu0.20.04.1
|   Thread ID: 13
|   Capabilities flags: 65535
|   Some Capabilities: SupportsLoadDataLocal, SupportsTransactions, Speaks41ProtocolOld, LongPassword, DontAllowDatabaseTableColumn, Support41Auth, IgnoreSigpipes, SwitchToSSLAfterHandshake, FoundRows, InteractiveClient, Speaks41ProtocolNew, ConnectWithDatabase, IgnoreSpaceBeforeParenthesis, LongColumnFlag, SupportsCompression, ODBCClient, SupportsMultipleStatments, SupportsAuthPlugins, SupportsMultipleResults
|   Status: Autocommit
|   Salt: YTSgMfqvx\x0F\x7F\x16\&\x1EAeK>0
|_  Auth Plugin Name: caching_sha2_password
|_mysql-users: ERROR: Script execution failed (use -d to debug)
|_mysql-variables: ERROR: Script execution failed (use -d to debug)
|_mysql-vuln-cve2012-2122: ERROR: Script execution failed (use -d to debug)
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.21 seconds
```

Como em todas as nossas digitalizações, devemos ter cuidado com os resultados e confirmar manualmente as informações obtidas, pois algumas das informações podem ser falsas. Essa verificação acima é um excelente exemplo disso, pois sabemos que o servidor MySQL de destino não usa uma senha vazia para o usuário `root`, mas uma senha fixa. Podemos testar isso com o seguinte comando:

#### Interação com o MySQL Server

```shell-session
casluxd@htb[/htb]$ mysql -u root -h 10.129.14.132

ERROR 1045 (28000): Access denied for user 'root'@'10.129.14.1' (using password: NO)
```

Por exemplo, se usarmos uma senha que adivinhamos ou encontramos em nossa pesquisa, poderemos fazer login no servidor MySQL e executar alguns comandos.

```shell-session
casluxd@htb[/htb]$ mysql -u root -pP4SSw0rd -h 10.129.14.128

Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 150165
Server version: 8.0.27-0ubuntu0.20.04.1 (Ubuntu)                                                         
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.                                     
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.                           
      
MySQL [(none)]> show databases;                                                                          
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.006 sec)


MySQL [(none)]> select version();
+-------------------------+
| version()               |
+-------------------------+
| 8.0.27-0ubuntu0.20.04.1 |
+-------------------------+
1 row in set (0.001 sec)


MySQL [(none)]> use mysql;
MySQL [mysql]> show tables;
+------------------------------------------------------+
| Tables_in_mysql                                      |
+------------------------------------------------------+
| columns_priv                                         |
| component                                            |
| db                                                   |
| default_roles                                        |
| engine_cost                                          |
| func                                                 |
| general_log                                          |
| global_grants                                        |
| gtid_executed                                        |
| help_category                                        |
| help_keyword                                         |
| help_relation                                        |
| help_topic                                           |
| innodb_index_stats                                   |
| innodb_table_stats                                   |
| password_history                                     |
...SNIP...
| user                                                 |
+------------------------------------------------------+
37 rows in set (0.002 sec)
```

Se olharmos para os bancos de dados existentes, veremos vários já existirem. Os bancos de dados mais importantes para o servidor MySQL são os `system schema` (`sys`) e `information schema` (`information_schema`). O esquema do sistema contém tabelas, informações e metadados necessários para o gerenciamento. Mais sobre esse banco de dados podem ser encontrados no [manual de referência](https://dev.mysql.com/doc/refman/8.0/en/system-schema.html#:~:text=The%20mysql%20schema%20is%20the,used%20for%20other%20operational%20purposes) do MySQL.

```shell-session
mysql> use sys;
mysql> show tables;  

+-----------------------------------------------+
| Tables_in_sys                                 |
+-----------------------------------------------+
| host_summary                                  |
| host_summary_by_file_io                       |
| host_summary_by_file_io_type                  |
| host_summary_by_stages                        |
| host_summary_by_statement_latency             |
| host_summary_by_statement_type                |
| innodb_buffer_stats_by_schema                 |
| innodb_buffer_stats_by_table                  |
| innodb_lock_waits                             |
| io_by_thread_by_latency                       |
...SNIP...
| x$waits_global_by_latency                     |
+-----------------------------------------------+


mysql> select host, unique_users from host_summary;

+-------------+--------------+                   
| host        | unique_users |                   
+-------------+--------------+                   
| 10.129.14.1 |            1 |                   
| localhost   |            2 |                   
+-------------+--------------+                   
2 rows in set (0,01 sec)  
```

O `information schema` também é um banco de dados que contém metadados. No entanto, esses metadados são recuperados principalmente do `system schema` banco de dados. O motivo da existência desses dois é o padrão ANSI / ISO que foi estabelecido. `System schema` é um catálogo de sistemas da Microsoft para servidores SQL e contém muito mais informações que o `information schema`.

Alguns dos comandos que devemos lembrar e anotar para trabalhar com bancos de dados MySQL são descritos abaixo na tabela.

|**Comando**|**Descrição**|
|---|---|
|`mysql -u <user> -p<password> -h <IP address>`|Conecte-se ao servidor MySQL. Deveria **não** seja um espaço entre o sinalizador '-p' e a senha.|
|`show databases;`|Mostrar todos os bancos de dados.|
|`use <database>;`|Selecione um dos bancos de dados existentes.|
|`show tables;`|Mostre todas as tabelas disponíveis no banco de dados selecionado.|
|`show columns from <table>;`|Mostre todas as colunas no banco de dados selecionado.|
|`select * from <table>;`|Mostre tudo na tabela desejada.|
|`select * from <table> where <column> = "<string>";`|Procure o necessário `string` na tabela desejada.|

Precisamos saber como interagir com diferentes bancos de dados. Portanto, recomendamos instalar e configurar um servidor MySQL em uma de nossas VMs para experimentação. Há também um amplamente coberto [problemas de segurança](https://dev.mysql.com/doc/refman/8.0/en/general-security-issues.html) seção no manual de referência que abrange as melhores práticas para proteger servidores MySQL. Devemos usar isso ao configurar nosso servidor MySQL para entender melhor por que algo pode não funcionar. 