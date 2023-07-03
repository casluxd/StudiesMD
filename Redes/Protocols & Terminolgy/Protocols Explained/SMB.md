O SMB (Server Message Block) é um protocolo de rede usado principalmente em ambientes Windows para compartilhamento de arquivos, impressoras e outros recursos entre dispositivos em uma rede local. Ele permite que computadores e servidores em uma rede se comuniquem e acessem recursos compartilhados.

Aqui estão algumas informações úteis sobre o protocolo SMB:

1. Comunicação e funcionamento: A comunicação no protocolo SMB é baseada em solicitações e respostas entre um cliente SMB e um servidor SMB. O cliente envia solicitações ao servidor para acessar recursos compartilhados, como arquivos ou impressoras. O servidor processa essas solicitações e envia respostas ao cliente com os dados solicitados ou informações de status.
2. Versões do SMB: Ao longo dos anos, várias versões do SMB foram lançadas, cada uma trazendo melhorias e recursos adicionais. As versões mais comuns são SMB1, SMB2, SMB2.1, SMB3 e SMB3.1. É importante utilizar versões mais recentes do protocolo, como o SMB3, devido a melhorias de segurança e desempenho.
3. Autenticação e segurança: O SMB oferece diferentes métodos de autenticação para garantir a segurança das comunicações. Isso inclui autenticação baseada em senhas, autenticação de domínio (usando contas de usuário em um domínio Windows) e autenticação Kerberos. Além disso, o SMB pode ser usado em conjunto com o protocolo de criptografia SMB Encryption para proteger os dados transmitidos na rede.
4. Utilidade: O SMB é amplamente utilizado em ambientes Windows para compartilhar arquivos, pastas e impressoras entre computadores em uma rede local. Ele permite que os usuários acessem e editem arquivos em servidores remotos como se estivessem armazenados localmente. Além disso, o SMB também é usado para a comunicação entre clientes Windows e servidores de domínio para autenticação, logon e políticas de grupo.
5. Portas e protocolos relacionados: O SMB normalmente usa a porta TCP 445 para comunicação. No entanto, versões mais antigas do protocolo (SMB1) também usavam a porta TCP 139. Além disso, o NetBIOS (Network Basic Input/Output System) é frequentemente usado em conjunto com o SMB para resolução de nomes de NetBIOS em endereços IP.

É importante estar ciente de que, embora o SMB seja amplamente utilizado, em alguns casos, versões mais antigas do protocolo podem ter vulnerabilidades de segurança conhecidas. Portanto, é recomendável manter as versões mais recentes do SMB e implementar medidas adequadas de segurança, como criptografia e autenticação segura, para proteger a comunicação SMB em redes.

# Comunicação

Nas redes IP, o SMB usa o protocolo TCP para esse fim, que fornece um three-way handshake entre o cliente e o servidor antes que uma conexão seja finalmente estabelecida. As especificações do protocolo TCP também regem o transporte subsequente de dados. Podemos dar uma olhada em alguns exemplos [aqui](https://winprotocoldoc.blob.core.windows.net/productionwindowsarchives/MS-SMB2/%5bMS-SMB2%5d.pdf#%5B%7B%22num%22%3A920%2C%22gen%22%3A0%7D%2C%7B%22name%22%3A%22XYZ%22%7D%2C69%2C738%2C0%5D).

Um servidor SMB pode fornecer partes arbitrárias de seu sistema de arquivos local como ações. Portanto, a hierarquia visível para um cliente é parcialmente independente da estrutura no servidor. Direitos de acesso são definidos por `Access Control Lists` (`ACL`). Eles podem ser controlados de maneira refinada, com base em atributos como `execute`, `read`, e `full access` para usuários individuais ou grupos de usuários. As ACLs são definidas com base nos compartilhamentos e, portanto, não correspondem aos direitos atribuídos localmente no servidor.

# Samba

Como mencionado anteriormente, há uma variante alternativa ao servidor SMB, chamada Samba, desenvolvida para o sistema operacional baseado em Unix. O samba implementa o `Common Internet File System` (`CIFS`Protocolo de rede ). [CIFS](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-cifs/934c2faa-54af-4526-ac74-6a24d126724e) é um "dialeto" do SMB. Em outras palavras, o CIFS é uma implementação muito específica do protocolo SMB, que por sua vez foi criado pela Microsoft. Isso permite que o Samba se comunique com os sistemas Windows mais recentes. Portanto, geralmente é referido como `SMB / CIFS`. No entanto, CIFS é a extensão do protocolo SMB. Portanto, quando passamos comandos SMB pelo Samba para um serviço NetBIOS mais antigo, ele geralmente se conecta ao servidor Samba por portas TCP `137`, `138`, `139`, mas o CIFS usa somente a porta TCP `445`. Existem várias versões do SMB, incluindo versões desatualizadas que ainda são usadas em infraestruturas específicas.

|**Versão SMB**|**Suportado**|**Recursos**|
|---|---|---|
|CIFS|Windows NT 4.0|Comunicação via interface NetBIOS|
|SMB 1.0|Windows 2000|Conexão direta via TCP|
|SMB 2.0|Windows Vista, Windows Server 2008|Atualizações de desempenho, assinatura aprimorada de mensagens, recurso de cache|
|SMB 2.1|Windows 7, Windows Server 2008 R2|Mecanismos de bloqueio|
|SMB 3.0|Windows 8, Windows Server 2012|Conexões multicanal, criptografia de ponta a ponta, acesso remoto ao armazenamento|
|SMB 3.0.2|Windows 8.1, Windows Server 2012 R2||
|SMB 3.1.1|Windows 10, Windows Server 2016|Verificação de integridade, criptografia AES-128|

Com a versão 3, o servidor Samba ganhou a capacidade de ser um membro completo de um domínio do Active Directory. Com a versão 4, o Samba ainda fornece um controlador de domínio do Active Directory. Ele contém vários daemons chamados para esse fim - que são programas de fundo do Unix. O daemon do servidor SMB (`smbd`) pertencente ao Samba fornece as duas primeiras funcionalidades, enquanto o daemon do bloco de mensagens NetBIOS (`nmbd`) implementa as duas últimas funcionalidades. O serviço SMB controla esses dois programas em segundo plano.

## Configuração padrão

Como podemos imaginar, o Samba oferece uma ampla variedade de [configurações](https://www.samba.org/samba/docs/current/man-html/smb.conf.5.html) que podemos configurar. Novamente, definimos as configurações por meio de um arquivo de texto, onde podemos obter uma visão geral de algumas das configurações. Essas configurações são as seguintes quando filtradas:

```shell-session
casluxd@htb[/htb]$ cat /etc/samba/smb.conf | grep -v "#\|\;" 

[global]
   workgroup = DEV.INFREIGHT.HTB
   server string = DEVSMB
   log file = /var/log/samba/log.%m
   max log size = 1000
   logging = file
   panic action = /usr/share/samba/panic-action %d

   server role = standalone server
   obey pam restrictions = yes
   unix password sync = yes

   passwd program = /usr/bin/passwd %u
   passwd chat = *Enter\snew\s*\spassword:* %n\n *Retype\snew\s*\spassword:* %n\n *password\supdated\ssuccessfully* .

   pam password change = yes
   map to guest = bad user
   usershare allow guests = yes

[printers]
   comment = All Printers
   browseable = no
   path = /var/spool/samba
   printable = yes
   guest ok = no
   read only = yes
   create mask = 0700

[print$]
   comment = Printer Drivers
   path = /var/lib/samba/printers
   browseable = yes
   read only = yes
   guest ok = no
```

Vemos configurações globais e dois compartilhamentos destinados a impressoras. As configurações globais são a configuração do servidor SMB disponível que é usado para todos os compartilhamentos. Nos compartilhamentos individuais, no entanto, as configurações globais podem ser substituídas, o que pode ser configurado com alta probabilidade, mesmo incorretamente. Vejamos algumas das configurações para entender como os compartilhamentos estão configurados no Samba.


|**Configurando**|**Descrição**|
|---|---|
|`[sharename]`|O nome do compartilhamento de rede.|
|`workgroup = WORKGROUP/DOMAIN`|Grupo de trabalho que aparecerá quando os clientes consultarem.|
|`path = /path/here/`|O diretório ao qual o usuário deve ter acesso.|
|`server string = STRING`|A sequência que aparecerá quando uma conexão for iniciada.|
|`unix password sync = yes`|Sincronizar a senha do UNIX com a senha do SMB?|
|`usershare allow guests = yes`|Permitir que usuários não autenticados acessem o compartilhamento definido?|
|`map to guest = bad user`|O que fazer quando uma solicitação de login do usuário não corresponde a um usuário válido do UNIX?|
|`browseable = yes`|Esse compartilhamento deve ser mostrado na lista de ações disponíveis?|
|`guest ok = yes`|Permitir a conexão com o serviço sem usar uma senha?|
|`read only = yes`|Permitir que os usuários leiam apenas arquivos?|
|`create mask = 0700`|Quais permissões precisam ser definidas para arquivos recém-criados?|

## Configurações perigosas

Algumas das configurações acima já trazem algumas opções confidenciais. No entanto, suponha que questionemos as configurações listadas abaixo e nos perguntemos o que os funcionários poderiam obter com elas, bem como com os atacantes. Nesse caso, veremos quais vantagens e desvantagens as configurações trazem consigo. Vamos pegar a configuração `browseable = yes` como um exemplo. Se nós, como administradores, adotarmos essa configuração, os funcionários da empresa terão o conforto de poder olhar para as pastas individuais com o conteúdo. Muitas pastas são usadas para melhor organização e estrutura. Se o funcionário puder navegar pelas ações, ele também poderá fazê-lo após o acesso bem-sucedido.

|**Configurando**|**Descrição**|
|---|---|
|`browseable = yes`|Permitir a listagem de ações disponíveis na ação atual?|
|`read only = no`|Proibir a criação e modificação de arquivos?|
|`writable = yes`|Permitir que os usuários criem e modifiquem arquivos?|
|`guest ok = yes`|Permitir a conexão com o serviço sem usar uma senha?|
|`enable privileges = yes`|Privilégios de honra atribuídos a SID específico?|
|`create mask = 0777`|Quais permissões devem ser atribuídas aos arquivos recém-criados?|
|`directory mask = 0777`|Quais permissões devem ser atribuídas aos diretórios recém-criados?|
|`logon script = script.sh`|Qual script precisa ser executado no login do usuário?|
|`magic script = script.sh`|Qual script deve ser executado quando o script for fechado?|
|`magic output = script.out`|Onde a saída do script mágico precisa ser armazenada?|

Vamos criar uma ação chamada `[notes]` e alguns outros e veja como as configurações afetam nosso processo de enumeração. Usaremos todas as configurações acima e as aplicaremos a esse compartilhamento. Por exemplo, essa configuração geralmente é aplicada, mesmo que apenas para fins de teste. Se for uma sub-rede interna de uma pequena equipe em um grande departamento, essa configuração geralmente é mantida ou esquecida para ser redefinida. Isso leva ao fato de que podemos navegar por todos os compartilhamentos e, com alta probabilidade, até baixá-los e inspecioná-los.

#### Exemplo de compartilhamento

```shell-session
...SNIP...

[notes]
	comment = CheckIT
	path = /mnt/notes/

	browseable = yes
	read only = no
	writable = yes
	guest ok = yes

	enable privileges = yes
	create mask = 0777
	directory mask = 0777
```

É altamente recomendável olhar para as páginas de manual do Samba e configurá-las e experimentar as configurações. Descobriremos aspectos em potencial que serão interessantes para nós como testador de penetração. Além disso, quanto mais familiarizarmos com o servidor Samba e o SMB, mais fácil será encontrar o caminho para o ambiente e usá-lo para nossos propósitos. Depois de nos ajustarmos `/etc/samba/smb.conf` para nossas necessidades, precisamos reiniciar o serviço no servidor.

#### Reiniciar Samba

```shell-session
root@samba:~# sudo systemctl restart smbd
```

Agora podemos exibir uma lista (`-L`) das ações do servidor com o `smbclient` comando do nosso anfitrião. Usamos os chamados `null session` (`-N`), que é `anonymous` acesso sem a entrada de usuários existentes ou senhas válidas.

#### SMBclient - Conectando-se à ação

```shell-session
casluxd@htb[/htb]$ smbclient -N -L //10.129.14.128

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        home            Disk      INFREIGHT Samba
        dev             Disk      DEVenv
        notes           Disk      CheckIT
        IPC$            IPC       IPC Service (DEVSM)
SMB1 disabled -- no workgroup available
```

Podemos ver que agora temos cinco ações diferentes no servidor Samba do resultado. Assim `print$` e um `IPC$` já estão incluídos por padrão na configuração básica, como já vimos. Desde que lidamos com o `[notes]` compartilhe, deixe-nos fazer login e inspecioná-lo usando o mesmo programa cliente. Se não estivermos familiarizados com o programa do cliente, podemos usar o `help` comando no login bem-sucedido, listando todos os comandos possíveis que podemos executar.

```shell-session
casluxd@htb[/htb]$ smbclient //10.129.14.128/notes

Enter WORKGROUP\<username>'s password: 
Anonymous login successful
Try "help" to get a list of possible commands.


smb: \> help

?              allinfo        altname        archive        backup         
blocksize      cancel         case_sensitive cd             chmod          
chown          close          del            deltree        dir            
du             echo           exit           get            getfacl        
geteas         hardlink       help           history        iosize         
lcd            link           lock           lowercase      ls             
l              mask           md             mget           mkdir          
more           mput           newer          notify         open           
posix          posix_encrypt  posix_open     posix_mkdir    posix_rmdir    
posix_unlink   posix_whoami   print          prompt         put            
pwd            q              queue          quit           readlink       
rd             recurse        reget          rename         reput          
rm             rmdir          showacls       setea          setmode        
scopy          stat           symlink        tar            tarmode        
timeout        translate      unlock         volume         vuid           
wdel           logon          listconnect    showconnect    tcon           
tdis           tid            utimes         logoff         ..             
!            


smb: \> ls

  .                                   D        0  Wed Sep 22 18:17:51 2021
  ..                                  D        0  Wed Sep 22 12:03:59 2021
  prep-prod.txt                       N       71  Sun Sep 19 15:45:21 2021

                30313412 blocks of size 1024. 16480084 blocks available
```

Depois de descobrir arquivos ou pastas interessantes, podemos baixá-los usando o `get` comando. O Smbclient também nos permite executar comandos do sistema local usando um ponto de exclamação no início (`!<cmd>`) sem interromper a conexão.

#### Baixar arquivos do SMB

```shell-session
smb: \> get prep-prod.txt 

getting file \prep-prod.txt of size 71 as prep-prod.txt (8,7 KiloBytes/sec) 
(average 8,7 KiloBytes/sec)


smb: \> !ls

prep-prod.txt


smb: \> !cat prep-prod.txt

[] check your code with the templates
[] run code-assessment.py
[] …	
```

Do ponto de vista administrativo, podemos verificar essas conexões usando `smbstatus`. Além da versão do Samba, também podemos ver quem, de qual host e qual compartilhamento o cliente está conectado. Isso é especialmente importante quando entramos em uma sub-rede (, talvez até uma isolada ) que as outras ainda possam acessar.

Por exemplo, com segurança no nível do domínio, o servidor samba atua como um membro de um domínio do Windows. Cada domínio possui pelo menos um controlador de domínio, geralmente um servidor Windows NT que fornece autenticação por senha. Este controlador de domínio fornece ao grupo de trabalho um servidor de senha definitivo. Os controladores de domínio acompanham os usuários e senhas por conta própria `Security Authentication Module` (`SAM`) e autentique cada usuário quando ele fizer login pela primeira vez e desejar acessar o compartilhamento de outra máquina.

#### Status do Samba

```shell-session
root@samba:~# smbstatus

Samba version 4.11.6-Ubuntu
PID     Username     Group        Machine                                   Protocol Version  Encryption           Signing              
----------------------------------------------------------------------------------------------------------------------------------------
75691   sambauser    samba        10.10.14.4 (ipv4:10.10.14.4:45564)      SMB3_11           -                    -                    

Service      pid     Machine       Connected at                     Encryption   Signing     
---------------------------------------------------------------------------------------------
notes        75691   10.10.14.4   Do Sep 23 00:12:06 2021 CEST     -            -           

No locked files
```

## Pegando o serviço

Vamos voltar a uma de nossas ferramentas de enumeração. O Nmap também possui muitas opções e scripts NSE que podem nos ajudar a examinar o serviço SMB do destino mais de perto e obter mais informações. A desvantagem, no entanto, é que essas verificações podem levar muito tempo. Portanto, também é recomendável examinar o serviço manualmente, principalmente porque podemos encontrar muito mais detalhes do que o Nmap poderia nos mostrar. Primeiro, no entanto, vamos ver o que o Nmap pode encontrar em nosso servidor Samba de destino, onde criamos o `[notes]` compartilhar para fins de teste.

#### Nmap

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.14.128 -sV -sC -p139,445

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 15:15 CEST
Nmap scan report for sharing.inlanefreight.htb (10.129.14.128)
Host is up (0.00024s latency).

PORT    STATE SERVICE     VERSION
139/tcp open  netbios-ssn Samba smbd 4.6.2
445/tcp open  netbios-ssn Samba smbd 4.6.2
MAC Address: 00:00:00:00:00:00 (VMware)

Host script results:
|_nbstat: NetBIOS name: HTB, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-09-19T13:16:04
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.35 seconds
```

Podemos ver pelos resultados que não é muito que o Nmap nos forneceu aqui. Portanto, devemos recorrer a outras ferramentas que nos permitam interagir manualmente com o SMB e enviar solicitações específicas para as informações. Uma das ferramentas úteis para isso é `rpcclient`. Esta é uma ferramenta para executar funções MS-RPC.

O [Chamada de Procedimento Remoto](https://www.geeksforgeeks.org/remote-procedure-call-rpc-in-operating-system/) (`RPC`) é um conceito e, portanto, também uma ferramenta central para realizar estruturas operacionais e de compartilhamento de trabalho em redes e arquiteturas cliente-servidor. O processo de comunicação via RPC inclui parâmetros de passagem e o retorno de um valor de função.

#### RPCclient

```shell-session
casluxd@htb[/htb]$ rpcclient -U "" 10.129.14.128

Enter WORKGROUP\'s password:
rpcclient $> 
```

O `rpcclient` oferece muitas solicitações diferentes com as quais podemos executar funções específicas no servidor SMB para obter informações. Uma lista completa de todas essas funções pode ser encontrada no [página de manual](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html) do rpcclient.

|**Consulta**|**Descrição**|
|---|---|
|`srvinfo`|Informações do servidor.|
|`enumdomains`|Enumere todos os domínios que são implantados na rede.|
|`querydominfo`|Fornece informações de domínio, servidor e usuário de domínios implantados.|
|`netshareenumall`|Enumera todas as ações disponíveis.|
|`netsharegetinfo <share>`|Fornece informações sobre um compartilhamento específico.|
|`enumdomusers`|Enumera todos os usuários do domínio.|
|`queryuser <RID>`|Fornece informações sobre um usuário específico.|

```shell-session
rpcclient $> srvinfo

        DEVSMB         Wk Sv PrQ Unx NT SNT DEVSM
        platform_id     :       500
        os version      :       6.1
        server type     :       0x809a03
		
		
rpcclient $> enumdomains

name:[DEVSMB] idx:[0x0]
name:[Builtin] idx:[0x1]


rpcclient $> querydominfo

Domain:         DEVOPS
Server:         DEVSMB
Comment:        DEVSM
Total Users:    2
Total Groups:   0
Total Aliases:  0
Sequence No:    1632361158
Force Logoff:   -1
Domain Server State:    0x1
Server Role:    ROLE_DOMAIN_PDC
Unknown 3:      0x1


rpcclient $> netshareenumall

netname: print$
        remark: Printer Drivers
        path:   C:\var\lib\samba\printers
        password:
netname: home
        remark: INFREIGHT Samba
        path:   C:\home\
        password:
netname: dev
        remark: DEVenv
        path:   C:\home\sambauser\dev\
        password:
netname: notes
        remark: CheckIT
        path:   C:\mnt\notes\
        password:
netname: IPC$
        remark: IPC Service (DEVSM)
        path:   C:\tmp
        password:
		
		
rpcclient $> netsharegetinfo notes

netname: notes
        remark: CheckIT
        path:   C:\mnt\notes\
        password:
        type:   0x0
        perms:  0
        max_uses:       -1
        num_uses:       1
revision: 1
type: 0x8004: SEC_DESC_DACL_PRESENT SEC_DESC_SELF_RELATIVE 
DACL
        ACL     Num ACEs:       1       revision:       2
        ---
        ACE
                type: ACCESS ALLOWED (0) flags: 0x00 
                Specific bits: 0x1ff
                Permissions: 0x101f01ff: Generic all access SYNCHRONIZE_ACCESS WRITE_OWNER_ACCESS WRITE_DAC_ACCESS READ_CONTROL_ACCESS DELETE_ACCESS 
                SID: S-1-1-0
```

Esses exemplos nos mostram quais informações podem ser vazadas para usuários anônimos. Uma vez o usuário `anonymous` tem acesso a um serviço de rede, é preciso apenas um erro para fornecer permissões demais ou visibilidade demais para colocar toda a rede em risco significativo.

Mais importante ainda, o acesso anônimo a esses serviços também pode levar à descoberta de outros usuários, que podem ser atacados com força bruta no caso mais agressivo. Os seres humanos são mais propensos a erros do que os processos de computador configurados adequadamente, e a falta de conscientização e preguiça da segurança geralmente leva a senhas fracas que podem ser facilmente quebradas. Vamos ver como podemos enumerar usuários usando o `rpcclient`.

#### Rpcclient - Enumeração do Usuário

```shell-session
rpcclient $> enumdomusers

user:[mrb3n] rid:[0x3e8]
user:[cry0l1t3] rid:[0x3e9]


rpcclient $> queryuser 0x3e9

        User Name   :   cry0l1t3
        Full Name   :   cry0l1t3
        Home Drive  :   \\devsmb\cry0l1t3
        Dir Drive   :
        Profile Path:   \\devsmb\cry0l1t3\profile
        Logon Script:
        Description :
        Workstations:
        Comment     :
        Remote Dial :
        Logon Time               :      Do, 01 Jan 1970 01:00:00 CET
        Logoff Time              :      Mi, 06 Feb 2036 16:06:39 CET
        Kickoff Time             :      Mi, 06 Feb 2036 16:06:39 CET
        Password last set Time   :      Mi, 22 Sep 2021 17:50:56 CEST
        Password can change Time :      Mi, 22 Sep 2021 17:50:56 CEST
        Password must change Time:      Do, 14 Sep 30828 04:48:05 CEST
        unknown_2[0..31]...
        user_rid :      0x3e9
        group_rid:      0x201
        acb_info :      0x00000014
        fields_present: 0x00ffffff
        logon_divs:     168
        bad_password_count:     0x00000000
        logon_count:    0x00000000
        padding1[0..7]...
        logon_hrs[0..21]...


rpcclient $> queryuser 0x3e8

        User Name   :   mrb3n
        Full Name   :
        Home Drive  :   \\devsmb\mrb3n
        Dir Drive   :
        Profile Path:   \\devsmb\mrb3n\profile
        Logon Script:
        Description :
        Workstations:
        Comment     :
        Remote Dial :
        Logon Time               :      Do, 01 Jan 1970 01:00:00 CET
        Logoff Time              :      Mi, 06 Feb 2036 16:06:39 CET
        Kickoff Time             :      Mi, 06 Feb 2036 16:06:39 CET
        Password last set Time   :      Mi, 22 Sep 2021 17:47:59 CEST
        Password can change Time :      Mi, 22 Sep 2021 17:47:59 CEST
        Password must change Time:      Do, 14 Sep 30828 04:48:05 CEST
        unknown_2[0..31]...
        user_rid :      0x3e8
        group_rid:      0x201
        acb_info :      0x00000010
        fields_present: 0x00ffffff
        logon_divs:     168
        bad_password_count:     0x00000000
        logon_count:    0x00000000
        padding1[0..7]...
        logon_hrs[0..21]...
```

Podemos então usar os resultados para identificar o RID do grupo, que podemos usar para recuperar informações de todo o grupo.

```shell-session
rpcclient $> querygroup 0x201

        Group Name:     None
        Description:    Ordinary Users
        Group Attribute:7
        Num Members:2
```

No entanto, também pode acontecer que nem todos os comandos estejam disponíveis para nós e temos certas restrições baseadas no usuário. No entanto, a consulta `queryuser <RID>` é permitido principalmente com base no RID. Assim, podemos usar o rpcclient para brutar forçar os RIDs a obter informações. Como podemos não saber quem foi designado para qual RID, sabemos que obteremos informações sobre ele assim que consultarmos um RID atribuído. Existem várias maneiras e ferramentas que podemos usar para isso. Para ficar com a ferramenta, podemos criar um `For-loop` usando `Bash` onde enviamos um comando para o serviço usando rpcclient e filtramos os resultados.

#### RIDs de usuários de forjamento bruto

```shell-session
casluxd@htb[/htb]$ for i in $(seq 500 1100);do rpcclient -N -U "" 10.129.14.128 -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done

        User Name   :   sambauser
        user_rid :      0x1f5
        group_rid:      0x201
		
        User Name   :   mrb3n
        user_rid :      0x3e8
        group_rid:      0x201
		
        User Name   :   cry0l1t3
        user_rid :      0x3e9
        group_rid:      0x201
```

Uma alternativa para isso seria um script Python de [Impacket](https://github.com/SecureAuthCorp/impacket) chamado [samrdump.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/samrdump.py).

```shell-session
casluxd@htb[/htb]$ samrdump.py 10.129.14.128

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Retrieving endpoint list from 10.129.14.128
Found domain(s):
 . DEVSMB
 . Builtin
[*] Looking up users in domain DEVSMB
Found user: mrb3n, uid = 1000
Found user: cry0l1t3, uid = 1001
mrb3n (1000)/FullName: 
mrb3n (1000)/UserComment: 
mrb3n (1000)/PrimaryGroupId: 513
mrb3n (1000)/BadPasswordCount: 0
mrb3n (1000)/LogonCount: 0
mrb3n (1000)/PasswordLastSet: 2021-09-22 17:47:59
mrb3n (1000)/PasswordDoesNotExpire: False
mrb3n (1000)/AccountIsDisabled: False
mrb3n (1000)/ScriptPath: 
cry0l1t3 (1001)/FullName: cry0l1t3
cry0l1t3 (1001)/UserComment: 
cry0l1t3 (1001)/PrimaryGroupId: 513
cry0l1t3 (1001)/BadPasswordCount: 0
cry0l1t3 (1001)/LogonCount: 0
cry0l1t3 (1001)/PasswordLastSet: 2021-09-22 17:50:56
cry0l1t3 (1001)/PasswordDoesNotExpire: False
cry0l1t3 (1001)/AccountIsDisabled: False
cry0l1t3 (1001)/ScriptPath: 
[*] Received 2 entries.
```

As informações que já obtivemos com `rpcclient` também pode ser obtido usando outras ferramentas. Por exemplo, o [SMBMap](https://github.com/ShawnDEvans/smbmap) e [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) as ferramentas também são amplamente utilizadas e úteis para a enumeração de serviços SMB.

```shell-session
casluxd@htb[/htb]$ smbmap -H 10.129.14.128

[+] Finding open SMB ports....
[+] User SMB session established on 10.129.14.128...
[+] IP: 10.129.14.128:445       Name: 10.129.14.128                                     
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        print$                                                  NO ACCESS       Printer Drivers
        home                                                    NO ACCESS       INFREIGHT Samba
        dev                                                     NO ACCESS       DEVenv
        notes                                                   NO ACCESS       CheckIT
        IPC$                                                    NO ACCESS       IPC Service (DEVSM)
```

#### CrackMapExec

```shell-session
casluxd@htb[/htb]$ crackmapexec smb 10.129.14.128 --shares -u '' -p ''

SMB         10.129.14.128   445    DEVSMB           [*] Windows 6.1 Build 0 (name:DEVSMB) (domain:) (signing:False) (SMBv1:False)
SMB         10.129.14.128   445    DEVSMB           [+] \: 
SMB         10.129.14.128   445    DEVSMB           [+] Enumerated shares
SMB         10.129.14.128   445    DEVSMB           Share           Permissions     Remark
SMB         10.129.14.128   445    DEVSMB           -----           -----------     ------
SMB         10.129.14.128   445    DEVSMB           print$                          Printer Drivers
SMB         10.129.14.128   445    DEVSMB           home                            INFREIGHT Samba
SMB         10.129.14.128   445    DEVSMB           dev                             DEVenv
SMB         10.129.14.128   445    DEVSMB           notes           READ,WRITE      CheckIT
SMB         10.129.14.128   445    DEVSMB           IPC$                            IPC Service (DEVSM)
```

Outra ferramenta que vale a pena mencionar é a chamada [enum4linux-ng](https://github.com/cddmp/enum4linux-ng), que é baseado em uma ferramenta mais antiga, enum4linux. Essa ferramenta automatiza muitas das consultas, mas não todas, e pode retornar uma grande quantidade de informações.

```shell-session
casluxd@htb[/htb]$ git clone https://github.com/cddmp/enum4linux-ng.git
casluxd@htb[/htb]$ cd enum4linux-ng
casluxd@htb[/htb]$ pip3 install -r requirements.txt
```

```shell-session
casluxd@htb[/htb]$ ./enum4linux-ng.py 10.129.14.128 -A

ENUM4LINUX - next generation

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.129.14.128
[*] Username ......... ''
[*] Random Username .. 'juzgtcsu'
[*] Password ......... ''
[*] Timeout .......... 5 second(s)

 =====================================
|    Service Scan on 10.129.14.128    |
 =====================================
[*] Checking LDAP
[-] Could not connect to LDAP on 389/tcp: connection refused
[*] Checking LDAPS
[-] Could not connect to LDAPS on 636/tcp: connection refused
[*] Checking SMB
[+] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS
[+] SMB over NetBIOS is accessible on 139/tcp

 =====================================================
|    NetBIOS Names and Workgroup for 10.129.14.128    |
 =====================================================
[+] Got domain/workgroup name: DEVOPS
[+] Full NetBIOS names information:
- DEVSMB          <00> -         H <ACTIVE>  Workstation Service
- DEVSMB          <03> -         H <ACTIVE>  Messenger Service
- DEVSMB          <20> -         H <ACTIVE>  File Server Service
- ..__MSBROWSE__. <01> - <GROUP> H <ACTIVE>  Master Browser
- DEVOPS          <00> - <GROUP> H <ACTIVE>  Domain/Workgroup Name
- DEVOPS          <1d> -         H <ACTIVE>  Master Browser
- DEVOPS          <1e> - <GROUP> H <ACTIVE>  Browser Service Elections
- MAC Address = 00-00-00-00-00-00

 ==========================================
|    SMB Dialect Check on 10.129.14.128    |
 ==========================================
[*] Trying on 445/tcp
[+] Supported dialects and settings:
SMB 1.0: false
SMB 2.02: true
SMB 2.1: true
SMB 3.0: true
SMB1 only: false
Preferred dialect: SMB 3.0
SMB signing required: false

 ==========================================
|    RPC Session Check on 10.129.14.128    |
 ==========================================
[*] Check for null session
[+] Server allows session using username '', password ''
[*] Check for random user session
[+] Server allows session using username 'juzgtcsu', password ''
[H] Rerunning enumeration with user 'juzgtcsu' might give more results

 ====================================================
|    Domain Information via RPC for 10.129.14.128    |
 ====================================================
[+] Domain: DEVOPS
[+] SID: NULL SID
[+] Host is part of a workgroup (not a domain)

 ============================================================
|    Domain Information via SMB session for 10.129.14.128    |
 ============================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: DEVSMB
NetBIOS domain name: ''
DNS domain: ''
FQDN: htb

 ================================================
|    OS Information via RPC for 10.129.14.128    |
 ================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found OS information via SMB
[*] Enumerating via 'srvinfo'
[+] Found OS information via 'srvinfo'
[+] After merging OS information we have the following result:
OS: Windows 7, Windows Server 2008 R2
OS version: '6.1'
OS release: ''
OS build: '0'
Native OS: not supported
Native LAN manager: not supported
Platform id: '500'
Server type: '0x809a03'
Server type string: Wk Sv PrQ Unx NT SNT DEVSM

 ======================================
|    Users via RPC on 10.129.14.128    |
 ======================================
[*] Enumerating users via 'querydispinfo'
[+] Found 2 users via 'querydispinfo'
[*] Enumerating users via 'enumdomusers'
[+] Found 2 users via 'enumdomusers'
[+] After merging user results we have 2 users total:
'1000':
  username: mrb3n
  name: ''
  acb: '0x00000010'
  description: ''
'1001':
  username: cry0l1t3
  name: cry0l1t3
  acb: '0x00000014'
  description: ''

 =======================================
|    Groups via RPC on 10.129.14.128    |
 =======================================
[*] Enumerating local groups
[+] Found 0 group(s) via 'enumalsgroups domain'
[*] Enumerating builtin groups
[+] Found 0 group(s) via 'enumalsgroups builtin'
[*] Enumerating domain groups
[+] Found 0 group(s) via 'enumdomgroups'

 =======================================
|    Shares via RPC on 10.129.14.128    |
 =======================================
[*] Enumerating shares
[+] Found 5 share(s):
IPC$:
  comment: IPC Service (DEVSM)
  type: IPC
dev:
  comment: DEVenv
  type: Disk
home:
  comment: INFREIGHT Samba
  type: Disk
notes:
  comment: CheckIT
  type: Disk
print$:
  comment: Printer Drivers
  type: Disk
[*] Testing share IPC$
[-] Could not check share: STATUS_OBJECT_NAME_NOT_FOUND
[*] Testing share dev
[-] Share doesn't exist
[*] Testing share home
[+] Mapping: OK, Listing: OK
[*] Testing share notes
[+] Mapping: OK, Listing: OK
[*] Testing share print$
[+] Mapping: DENIED, Listing: N/A

 ==========================================
|    Policies via RPC for 10.129.14.128    |
 ==========================================
[*] Trying port 445/tcp
[+] Found policy:
domain_password_information:
  pw_history_length: None
  min_pw_length: 5
  min_pw_age: none
  max_pw_age: 49710 days 6 hours 21 minutes
  pw_properties:
  - DOMAIN_PASSWORD_COMPLEX: false
  - DOMAIN_PASSWORD_NO_ANON_CHANGE: false
  - DOMAIN_PASSWORD_NO_CLEAR_CHANGE: false
  - DOMAIN_PASSWORD_LOCKOUT_ADMINS: false
  - DOMAIN_PASSWORD_PASSWORD_STORE_CLEARTEXT: false
  - DOMAIN_PASSWORD_REFUSE_PASSWORD_CHANGE: false
domain_lockout_information:
  lockout_observation_window: 30 minutes
  lockout_duration: 30 minutes
  lockout_threshold: None
domain_logoff_information:
  force_logoff_time: 49710 days 6 hours 21 minutes

 ==========================================
|    Printers via RPC for 10.129.14.128    |
 ==========================================
[+] No printers returned (this is not an error)

Completed after 0.61 seconds
```

Precisamos usar mais de duas ferramentas para enumeração. Como pode acontecer que, devido à programação das ferramentas, obtenhamos informações diferentes que precisamos verificar manualmente. Portanto, nunca devemos confiar apenas em ferramentas automatizadas onde não sabemos exatamente como elas foram escritas.

# rpcclient

O rpcclient é uma ferramenta de linha de comando que faz parte da suíte Samba. Ele é usado para interagir com serviços e servidores remotos que utilizam o Remote Procedure Call (RPC) como protocolo de comunicação.

O RPC é um protocolo amplamente utilizado em redes para permitir a comunicação e a chamada de procedimentos entre processos em diferentes sistemas. O rpcclient permite que você se conecte a um servidor RPC e execute uma variedade de operações, como consultas, chamadas de procedimentos remotos, testes de conectividade e gerenciamento de serviços.

Aqui estão alguns casos de uso comuns do rpcclient:

1. Teste de conectividade: O rpcclient pode ser usado para testar a conectividade com um servidor RPC específico. Isso pode ser útil para verificar se um servidor está acessível e se as portas necessárias estão abertas.
2. Enumeração de serviços: O rpcclient permite listar os serviços disponíveis em um servidor RPC. Isso pode ser útil para descobrir quais serviços estão sendo executados em um sistema remoto.
3. Consulta e gerenciamento de informações do sistema: Com o rpcclient, você pode obter informações sobre o sistema remoto, como detalhes do sistema operacional, usuários, grupos, políticas de segurança, permissões de compartilhamento, configurações do Registro do Windows, entre outros.
4. Execução remota de comandos: O rpcclient permite executar comandos ou procedimentos em um sistema remoto usando a funcionalidade de chamada de procedimento remoto. Isso pode ser útil para realizar tarefas de gerenciamento e administração em sistemas remotos.

Em resumo, o rpcclient é uma ferramenta útil para interagir com servidores que utilizam o protocolo RPC. Ele fornece recursos de teste de conectividade, consulta e gerenciamento de informações do sistema, além de permitir a execução remota de comandos. O rpcclient é amplamente utilizado em ambientes que empregam o protocolo RPC, como redes Windows e sistemas baseados em Samba.
