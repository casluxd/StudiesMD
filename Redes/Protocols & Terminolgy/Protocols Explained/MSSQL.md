[Microsoft SQL](https://www.microsoft.com/en-us/sql-server/sql-server-2019) (`MSSQL`) é o sistema de gerenciamento de banco de dados relacional baseado em SQL da Microsoft. Ao contrário do MySQL, que discutimos na última seção, o MSSQL é de fonte fechada e foi inicialmente escrito para ser executado nos sistemas operacionais Windows. É popular entre administradores e desenvolvedores de banco de dados ao criar aplicativos executados no Microsoft .Estrutura NET devido ao seu forte apoio nativo para .LÍQUIDA. Existem versões do MSSQL que serão executadas no Linux e MacOS, mas provavelmente encontraremos instâncias do MSSQL em destinos executando o Windows.

#### Clientes MSSQL

[SQL Server Management Studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) (`SSMS`) vem como um recurso que pode ser instalado com o pacote de instalação do MSSQL ou pode ser baixado e instalado separadamente. É comumente instalado no servidor para configuração inicial e gerenciamento de longo prazo de bancos de dados por administradores. Lembre-se de que, como o SSMS é um aplicativo do lado do cliente, ele pode ser instalado e usado em qualquer sistema que um administrador ou desenvolvedor esteja planejando gerenciar o banco de dados. Ele não existe apenas no servidor que hospeda o banco de dados. Isso significa que poderíamos encontrar um sistema vulnerável com SSMS com credenciais salvas que nos permitem conectar-se ao banco de dados. A imagem abaixo mostra o SSMS em ação.

![[Pasted image 20230627133053.png]]

Muitos outros clientes podem ser usados para acessar um banco de dados em execução no MSSQL. Incluindo, mas não limitado a:

- mssql-cli
- Servidor SQL PowerShell
- HediSQL
- SQLPro
- MSsqlclient.py da Impacket

Dos clientes MSSQL listados acima, os penteners podem achar o mssqlclient.py da Impacket o mais útil devido à presença do projeto Impacket da SecureAuthCorp em muitas distribuições de pentesting na instalação. Para descobrir se e onde o cliente está localizado em nosso host, podemos usar o seguinte comando:

```shell-session
casluxd@htb[/htb]$ locate mssqlclient

/usr/bin/impacket-mssqlclient
/usr/share/doc/python3-impacket/examples/mssqlclient.py
```

#### Bancos de dados MSSQL

O MSSQL possui bancos de dados de sistema padrão que podem nos ajudar a entender a estrutura de todos os bancos de dados que podem ser hospedados em um servidor de destino. Aqui estão os bancos de dados padrão e uma breve descrição de cada um:

|Banco de dados padrão do sistema|Descrição|
|---|---|
|`master`|Rastreia todas as informações do sistema para uma instância do servidor SQL|
|`model`|Banco de dados de modelos que atua como uma estrutura para cada novo banco de dados criado. Qualquer configuração alterada no banco de dados do modelo será refletida em qualquer novo banco de dados criado após alterações no banco de dados do modelo|
|`msdb`|O SQL Server Agent usa esse banco de dados para agendar trabalhos e alertas|
|`tempdb`|Armazena objetos temporários|
|`resource`|Banco de dados somente leitura contendo objetos do sistema incluídos no servidor SQL|


## Configuração padrão

Quando um administrador instala e configura MSSQL inicialmente para ser acessível à rede, o serviço SQL provavelmente será executado como `NT SERVICE\MSSQLSERVER`. A conexão do lado do cliente é possível através da autenticação do Windows e, por padrão, a criptografia não é aplicada ao tentar se conectar.

![[Pasted image 20230627133159.png]]

Autenticação sendo definida como `Windows Authentication` significa que o sistema operacional Windows subjacente processará a solicitação de login e usará o banco de dados SAM local ou o controlador de domínio ( hospedando o Active Directory ) antes de permitir a conectividade ao sistema de gerenciamento de banco de dados. O uso do Active Directory pode ser ideal para auditar atividades e controlar o acesso em um ambiente Windows, mas se uma conta estiver comprometida, isso pode levar a uma escalada de privilégios e movimento lateral em um ambiente de domínio do Windows. Como em qualquer sistema operacional, serviço, função do servidor ou aplicativo, pode ser benéfico configurá-lo em uma VM da instalação à configuração para entender todas as configurações padrão e possíveis erros que o administrador pode cometer.

## Configurações perigosas

Pode ser benéfico nos colocar na perspectiva de um administrador de TI quando estamos em um compromisso. Essa mentalidade pode nos ajudar a lembrar de procurar várias configurações que podem ter sido mal configuradas ou configuradas de maneira perigosa por um administrador. Um dia de trabalho em TI pode ser bastante movimentado, com muitos projetos diferentes acontecendo simultaneamente e a pressão para executar com velocidade e precisão sendo uma realidade em muitas organizações, erros podem ser facilmente cometidos. É preciso apenas uma pequena configuração incorreta que possa comprometer um servidor ou serviço crítico na rede. Isso se aplica a praticamente todas as funções de serviço de rede e servidor que podem ser configuradas, incluindo MSSQL.

Esta não é uma lista extensa, porque existem inúmeras maneiras pelas quais os bancos de dados MSSQL podem ser configurados por administradores com base nas necessidades de suas respectivas organizações. Podemos nos beneficiar analisando o seguinte:

- Clientes MSSQL que não usam criptografia para se conectar ao servidor MSSQL
- O uso de certificados autoassinados quando a criptografia está sendo usada. É possível falsificar certificados autoassinados
- O uso de [named pipes](https://docs.microsoft.com/en-us/sql/tools/configuration-manager/named-pipes-properties?view=sql-server-ver15)
- Fraco e padrão `sa` credenciais. Os administradores podem esquecer de desativar esta conta

## Footprinting

Existem muitas maneiras de abordar a pegada do serviço MSSQL, quanto mais específicos pudermos obter com nossas digitalizações, mais informações úteis poderemos coletar. O NMAP possui scripts mssql padrão que podem ser usados para segmentar a porta tcp padrão `1433` que o MSSQL escuta.

A verificação NMAP com script abaixo nos fornece informações úteis. Nós podemos ver o `hostname`, `database instance name`, `software version of MSSQL` e `named pipes are enabled`. Vamos nos beneficiar adicionando essas descobertas às nossas anotações.

#### Footprinting de script MSSQ do NMAP

```shell-session
casluxd@htb[/htb]$ sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.201.248

Starting Nmap 7.91 ( https://nmap.org ) at 2021-11-08 09:40 EST
Nmap scan report for 10.129.201.248
Host is up (0.15s latency).

PORT     STATE SERVICE  VERSION
1433/tcp open  ms-sql-s Microsoft SQL Server 2019 15.00.2000.00; RTM
| ms-sql-ntlm-info: 
|   Target_Name: SQL-01
|   NetBIOS_Domain_Name: SQL-01
|   NetBIOS_Computer_Name: SQL-01
|   DNS_Domain_Name: SQL-01
|   DNS_Computer_Name: SQL-01
|_  Product_Version: 10.0.17763

Host script results:
| ms-sql-dac: 
|_  Instance: MSSQLSERVER; DAC port: 1434 (connection failed)
| ms-sql-info: 
|   Windows server name: SQL-01
|   10.129.201.248\MSSQLSERVER: 
|     Instance name: MSSQLSERVER
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|     TCP port: 1433
|     Named pipe: \\10.129.201.248\pipe\sql\query
|_    Clustered: false

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.52 seconds
```

Também podemos usar o Metasploit para executar um scanner auxiliar chamado `mssql_ping` que irá verificar o serviço MSSQL e fornecer informações úteis em nosso processo de footprinting.

#### Ping MSSQL em Metasploit

```shell-session
msf6 auxiliary(scanner/mssql/mssql_ping) > set rhosts 10.129.201.248

rhosts => 10.129.201.248


msf6 auxiliary(scanner/mssql/mssql_ping) > run

[*] 10.129.201.248:       - SQL Server information for 10.129.201.248:
[+] 10.129.201.248:       -    ServerName      = SQL-01
[+] 10.129.201.248:       -    InstanceName    = MSSQLSERVER
[+] 10.129.201.248:       -    IsClustered     = No
[+] 10.129.201.248:       -    Version         = 15.0.2000.5
[+] 10.129.201.248:       -    tcp             = 1433
[+] 10.129.201.248:       -    np              = \\SQL-01\pipe\sql\query
[*] 10.129.201.248:       - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

#### Conectando-se com Mssqlclient.py

Se conseguirmos adivinhar ou obter acesso a credenciais, isso nos permitirá conectar-se remotamente ao servidor MSSQL e começar a interagir com bancos de dados usando T-SQL (`Transact-SQL`). Autenticar com o MSSQL nos permitirá interagir diretamente com os bancos de dados através do SQL Database Engine. No Pwnbox ou em um host de ataque pessoal, podemos usar o mssqlclient.py do Impacket para conectar-se como visto na saída abaixo. Depois de conectado ao servidor, pode ser bom obter uma configuração da terra e listar os bancos de dados presentes no sistema.

```shell-session
casluxd@htb[/htb]$ python3 mssqlclient.py Administrator@10.129.201.248 -windows-auth

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

Password:
[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(SQL-01): Line 1: Changed database context to 'master'.
[*] INFO(SQL-01): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (150 7208) 
[!] Press help for extra shell commands

SQL> select name from sys.databases

name                                                                                                                               

--------------------------------------------------------------------------------------

master                                                                                                                             

tempdb                                                                                                                             

model                                                                                                                              

msdb                                                                                                                               

Transactions    
```

