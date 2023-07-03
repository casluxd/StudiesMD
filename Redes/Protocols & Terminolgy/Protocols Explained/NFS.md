`Network File System` (`NFS`) é um sistema de arquivos de rede desenvolvido pela Sun Microsystems e tem o mesmo objetivo que o SMB. Seu objetivo é acessar sistemas de arquivos em uma rede como se fossem locais. No entanto, ele usa um protocolo totalmente diferente. [NFS](https://en.wikipedia.org/wiki/Network_File_System) é usado entre sistemas Linux e Unix. Isso significa que os clientes NFS não podem se comunicar diretamente com os servidores SMB. O NFS é um padrão da Internet que rege os procedimentos em um sistema de arquivos distribuído. Enquanto o protocolo NFS versão 3.0 (`NFSv3`), que está em uso há muitos anos, autentica o computador cliente, isso muda com `NFSv4`. Aqui, como no protocolo SMB do Windows, o usuário deve se autenticar.

|**Versão**|**Recursos**|
|---|---|
|`NFSv2`|É mais antigo, mas é suportado por muitos sistemas e foi inicialmente operado inteiramente pela UDP.|
|`NFSv3`|Possui mais recursos, incluindo tamanho de arquivo variável e melhor relatório de erros, mas não é totalmente compatível com os clientes NFSv2.|
|`NFSv4`|Inclui Kerberos, trabalha através de firewalls e na Internet, não requer mais portmappers, suporta ACLs, aplica operações baseadas em estado e fornece melhorias de desempenho e alta segurança. É também a primeira versão a ter um protocolo com estado.|

A versão 4.1 do NFS ([RFC 8881](https://datatracker.ietf.org/doc/html/rfc8881) ) visa fornecer suporte de protocolo para alavancar implantações de servidores de cluster, incluindo a capacidade de fornecer acesso paralelo escalável a arquivos distribuídos em vários servidores ( extensão pNFS ). Além disso, o NFSv4.1 inclui um mecanismo de entroncamento de sessão, também conhecido como multipatia do NFS. Uma vantagem significativa do NFSv4 sobre seus antecessores é que apenas uma porta UDP ou TCP `2049` é usado para executar o serviço, o que simplifica o uso do protocolo em firewalls.

O NFS é baseado no [Chamada de procedimento remoto de computação em rede aberta](https://en.wikipedia.org/wiki/Sun_RPC) (Protocolo `ONC-RPC`/`SUN-RPC` ) exposto em portas `TCP` e `UDP` `111`, que usa [Representação externa de dados](https://en.wikipedia.org/wiki/External_Data_Representation) (`XDR`) para a troca de dados independente do sistema. O protocolo NFS não possui mecanismo para `autenticação` ou `autorização`. Em vez disso, a autenticação é completamente alterada para as opções do protocolo RPC. A autorização é retirada das informações disponíveis do sistema de arquivos em que o servidor é responsável por traduzir as informações do usuário fornecidas pelo cliente para as do sistema de arquivos e converter o informações de autorização correspondentes, o mais corretamente possível, na sintaxe exigida pelo UNIX.

A autenticação mais comum é via UNIX `UID`/`GID` e `group memberships`, é por isso que é mais provável que essa sintaxe seja aplicada ao protocolo NFS. Um problema é que o cliente e o servidor não precisam necessariamente ter os mesmos mapeamentos de UID / GID para usuários e grupos, e o servidor não precisa fazer mais nada. Nenhuma verificação adicional pode ser feita por parte do servidor. É por isso que o NFS deve ser usado apenas com esse método de autenticação em redes confiáveis.

## Configuração padrão

O NFS não é difícil de configurar porque não há tantas opções quanto o FTP ou o SMB. O arquivo `/etc/exports` contém uma tabela de sistemas de arquivos físicos em um servidor NFS acessível pelos clientes. A [Tabela de Exportações do NFS](http://manpages.ubuntu.com/manpages/trusty/man5/exports.5.html) mostra quais opções ele aceita e, portanto, indica quais opções estão disponíveis para nós.

#### Exporta arquivo

```shell-session
casluxd@htb[/htb]$ cat /etc/exports 

# /etc/exports: the access control list for filesystems which may be exported
#               to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
```

O arquivo padrão `exports` também contém alguns exemplos de configuração de compartilhamentos NFS. Primeiro, a pasta é especificada e disponibilizada a outras pessoas e, em seguida, os direitos que elas terão sobre esse compartilhamento do NFS são conectados a um host ou sub-rede. Finalmente, opções adicionais podem ser adicionadas aos hosts ou sub-redes.

|**Opção**|**Descrição**|
|---|---|
|`rw`|Leia e escreva permissões.|
|`ro`|Leia apenas permissões.|
|`sync`|Transferência de dados síncrona. ( Um pouco mais lento )|
|`async`|Transferência de dados assíncrona. ( Um pouco mais rápido )|
|`secure`|Portas acima de 1024 não serão usadas.|
|`insecure`|Portas acima de 1024 serão usadas.|
|`no_subtree_check`|Esta opção desativa a verificação de árvores subdiretórias.|
|`root_squash`|Atribui todas as permissões aos arquivos do UID / GID raiz 0 ao UID / GID de anônimo, o que impede `root` acessando arquivos em uma montagem NFS.|

Vamos criar essa entrada para fins de teste e brincar com as configurações.

#### ExportFS

```shell-session
root@nfs:~# echo '/mnt/nfs  10.129.14.0/24(sync,no_subtree_check)' >> /etc/exports
root@nfs:~# systemctl restart nfs-kernel-server 
root@nfs:~# exportfs

/mnt/nfs      	10.129.14.0/24
```

Compartilhamos a pasta `/mnt/nfs` para a sub-rede `10.129.14.0/24` com a configuração mostrada acima. Isso significa que todos os hosts da rede poderão montar esse compartilhamento do NFS e inspecionar o conteúdo desta pasta.

## Configurações perigosas

No entanto, mesmo com o NFS, algumas configurações podem ser perigosas para a empresa e sua infraestrutura. Aqui estão alguns deles listados:

|**Opção**|**Descrição**|
|---|---|
|`rw`|Leia e escreva permissões.|
|`insecure`|Portas acima de 1024 serão usadas.|
|`nohide`|Se outro sistema de arquivos foi montado abaixo de um diretório exportado, esse diretório é exportado por sua própria entrada de exportações.|
|`no_root_squash`|Todos os arquivos criados pelo root são mantidos com o UID / GID 0.|

É altamente recomendável criar uma VM local e experimentar as configurações. Descobriremos métodos que nos mostrarão como o servidor NFS está configurado. Para isso, podemos criar várias pastas e atribuir opções diferentes a cada uma. Em seguida, podemos inspecioná-los e ver quais configurações podem ter que efeito no compartilhamento do NFS e suas permissões e no processo de enumeração.

Podemos dar uma olhada na opção `insecure` . Isso é perigoso porque os usuários podem usar portas acima de 1024. As primeiras 1024 portas podem ser usadas apenas por raiz. Isso evita o fato de que nenhum usuário pode usar soquetes acima da porta 1024 para o serviço NFS e interagir com ele.

## Pegando o serviço

Ao fazer o scan do NFS, as portas TCP `111` e `2049` são essenciais. Também podemos obter informações sobre o serviço NFS e o host via RPC, conforme mostrado abaixo no exemplo.

```shell-session
casluxd@htb[/htb]$ sudo nmap 10.129.14.128 -p111,2049 -sV -sC

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 17:12 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00018s latency).

PORT    STATE SERVICE VERSION
111/tcp open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      41982/udp6  mountd
|   100005  1,2,3      45837/tcp   mountd
|   100005  1,2,3      47217/tcp6  mountd
|   100005  1,2,3      58830/udp   mountd
|   100021  1,3,4      39542/udp   nlockmgr
|   100021  1,3,4      44629/tcp   nlockmgr
|   100021  1,3,4      45273/tcp6  nlockmgr
|   100021  1,3,4      47524/udp6  nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
2049/tcp open  nfs_acl 3 (RPC #100227)
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.58 seconds
```

O script NSE `rpcinfo`recupera uma lista de todos os serviços RPC atualmente em execução, seus nomes e descrições e as portas que eles usam. Isso nos permite verificar se o compartilhamento de destino está conectado à rede em todas as portas necessárias. Além disso, para o NFS, o Nmap possui alguns scripts NSE que podem ser usados para as digitalizações. Eles podem nos mostrar, por exemplo, o `contents` da parte e sua `stats`.

```shell-session
casluxd@htb[/htb]$ sudo nmap --script nfs* 10.129.14.128 -sV -p111,2049

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 17:37 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00021s latency).

PORT     STATE SERVICE VERSION
111/tcp  open  rpcbind 2-4 (RPC #100000)
| nfs-ls: Volume /mnt/nfs
|   access: Read Lookup NoModify NoExtend NoDelete NoExecute
| PERMISSION  UID    GID    SIZE  TIME                 FILENAME
| rwxrwxrwx   65534  65534  4096  2021-09-19T15:28:17  .
| ??????????  ?      ?      ?     ?                    ..
| rw-r--r--   0      0      1872  2021-09-19T15:27:42  id_rsa
| rw-r--r--   0      0      348   2021-09-19T15:28:17  id_rsa.pub
| rw-r--r--   0      0      0     2021-09-19T15:22:30  nfs.share
|_
| nfs-showmount: 
|_  /mnt/nfs 10.129.14.0/24
| nfs-statfs: 
|   Filesystem  1K-blocks   Used       Available   Use%  Maxfilesize  Maxlink
|_  /mnt/nfs    30313412.0  8074868.0  20675664.0  29%   16.0T        32000
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      41982/udp6  mountd
|   100005  1,2,3      45837/tcp   mountd
|   100005  1,2,3      47217/tcp6  mountd
|   100005  1,2,3      58830/udp   mountd
|   100021  1,3,4      39542/udp   nlockmgr
|   100021  1,3,4      44629/tcp   nlockmgr
|   100021  1,3,4      45273/tcp6  nlockmgr
|   100021  1,3,4      47524/udp6  nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
2049/tcp open  nfs_acl 3 (RPC #100227)
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.45 seconds
```

Depois de descobrirmos esse serviço NFS, podemos montá-lo em nossa máquina local. Para isso, podemos criar uma nova pasta vazia na qual o compartilhamento do NFS será montado. Uma vez montado, podemos navegar e visualizar o conteúdo como o nosso sistema local.

#### Mostrar compartilhamentos NFS disponíveis

```shell-session
casluxd@htb[/htb]$ showmount -e 10.129.14.128

Export list for 10.129.14.128:
/mnt/nfs 10.129.14.0/24
```

#### Montagem do NFS Share

```shell-session
casluxd@htb[/htb]$ mkdir target-NFS
casluxd@htb[/htb]$ sudo mount -t nfs 10.129.14.128:/ ./target-NFS/ -o nolock
casluxd@htb[/htb]$ cd target-NFS
casluxd@htb[/htb]$ tree .

.
└── mnt
    └── nfs
        ├── id_rsa
        ├── id_rsa.pub
        └── nfs.share

2 directories, 3 files
```

Lá teremos a oportunidade de acessar os direitos e os nomes de usuário e grupos aos quais os arquivos mostrados e visíveis pertencem. Porque uma vez que tenhamos os nomes de usuário, nomes de grupo, UIDs e GUIDs, podemos criá-los em nosso sistema e adaptá-los ao compartilhamento do NFS para visualizar e modificar os arquivos.

```shell-session
casluxd@htb[/htb]$ ls -l mnt/nfs/

total 16
-rw-r--r-- 1 cry0l1t3 cry0l1t3 1872 Sep 25 00:55 cry0l1t3.priv
-rw-r--r-- 1 cry0l1t3 cry0l1t3  348 Sep 25 00:55 cry0l1t3.pub
-rw-r--r-- 1 root     root     1872 Sep 19 17:27 id_rsa
-rw-r--r-- 1 root     root      348 Sep 19 17:28 id_rsa.pub
-rw-r--r-- 1 root     root        0 Sep 19 17:22 nfs.share
```

#### Liste o conteúdo com UIDs e GUIDs

```shell-session
casluxd@htb[/htb]$ ls -n mnt/nfs/

total 16
-rw-r--r-- 1 1000 1000 1872 Sep 25 00:55 cry0l1t3.priv
-rw-r--r-- 1 1000 1000  348 Sep 25 00:55 cry0l1t3.pub
-rw-r--r-- 1    0 1000 1221 Sep 19 18:21 backup.sh
-rw-r--r-- 1    0    0 1872 Sep 19 17:27 id_rsa
-rw-r--r-- 1    0    0  348 Sep 19 17:28 id_rsa.pub
-rw-r--r-- 1    0    0    0 Sep 19 17:22 nfs.share
```

É importante observar que se o `root_squash` opção está definida, não podemos editar o `backup.sh` arquivo mesmo como `root`.

Também podemos usar o NFS para escalonamento adicional. Por exemplo, se tivermos acesso ao sistema via SSH e desejarmos ler arquivos de outra pasta que um usuário específico possa ler, precisaríamos fazer upload de um shell para o compartilhamento NFS que possui o `SUID` desse usuário e, em seguida, execute o shell através do usuário SSH.

Depois de executarmos todas as etapas necessárias e obtermos as informações necessárias, podemos desmontar o compartilhamento do NFS.

#### Desmontagem

```shell-session
casluxd@htb[/htb]$ cd ..
casluxd@htb[/htb]$ sudo umount ./target-NFS
```

