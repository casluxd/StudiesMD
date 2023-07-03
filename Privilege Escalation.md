
Nosso acesso inicial a um servidor remoto geralmente ocorre no contexto de um usuário com poucos privilégios, o que não nos daria acesso completo pela caixa. Para obter acesso total, precisaremos encontrar uma vulnerabilidade interna/local que aumente nossos privilégios para o usuário `root` em `Linux` ou o usuário `administrator`/ `SYSTEM` em `Windows`. Vamos percorrer alguns métodos comuns de escalar nossos privilégios.

## Listas de verificação do PrivEsc

Depois de obter acesso inicial a uma caixa, queremos enumerar completamente a caixa para encontrar quaisquer vulnerabilidades em potencial que possamos explorar para obter um nível de privilégio mais alto. Podemos encontrar muitas listas de verificação e folhas de dicas on-line que possuem uma coleção de verificações que podemos executar e os comandos para executar essas verificações. Um recurso excelente é [o HackTricks](https://book.hacktricks.xyz/) , que tem uma excelente lista de verificação para escalonamento de privilégios locais [no Linux](https://book.hacktricks.xyz/linux-unix/linux-privilege-escalation-checklist) e [no Windows](https://book.hacktricks.xyz/windows/checklist-windows-privilege-escalation) . Outro repositório excelente é [o PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings) , que também possui listas de verificação para [Linux](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md) e [Windows](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md). Devemos começar a experimentar vários comandos e técnicas e nos familiarizar com eles para entender vários pontos fracos que podem levar à escalada de nossos privilégios.

## Scripts de Enumeração

Muitos dos comandos acima podem ser executados automaticamente com um script para percorrer o relatório e procurar por quaisquer pontos fracos. Podemos executar muitos scripts para enumerar automaticamente o servidor executando comandos comuns que retornam quaisquer descobertas interessantes. Alguns dos scripts comuns de enumeração do Linux incluem [LinEnum](https://github.com/rebootuser/LinEnum.git) e [linuxprivchecker](https://github.com/sleventyeleven/linuxprivchecker) , e para Windows incluem [Seatbelt](https://github.com/GhostPack/Seatbelt) e [JAWS](https://github.com/411Hall/JAWS) .

Outra ferramenta útil que podemos usar para enumeração de servidor é o [Privilege Escalation Awesome Scripts SUITE (PEASS)](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite) , pois é bem mantido para permanecer atualizado e inclui scripts para enumerar Linux e Windows.

Observação: esses scripts executarão muitos comandos conhecidos por identificar vulnerabilidades e criar muito "ruído" que pode acionar software antivírus ou software de monitoramento de segurança que procura esses tipos de eventos. Isso pode impedir que os scripts sejam executados ou até mesmo disparar um alarme de que o sistema foi comprometido. Em alguns casos, podemos querer fazer uma enumeração manual em vez de executar scripts.

Vamos dar um exemplo de execução do script Linux `PEASS` chamado `LinPEAS`:

```shell-session
casluxd@htb[/htb]$ ./linpeas.sh
...SNIP...

Linux Privesc Checklist: https://book.hacktricks.xyz/linux-unix/linux-privilege-escalation-checklist
 LEYEND:
  RED/YELLOW: 99% a PE vector
  RED: You must take a look at it
  LightCyan: Users with console
  Blue: Users without console & mounted devs
  Green: Common things (users, groups, SUID/SGID, mounts, .sh scripts, cronjobs)
  LightMangenta: Your username


====================================( Basic information )=====================================
OS: Linux version 3.9.0-73-generic
User & Groups: uid=33(www-data) gid=33(www-data) groups=33(www-data)
...SNIP...
```

Como podemos ver, assim que o script é executado, ele começa a coletar informações e exibi-las em um excelente relatório. Vamos discutir algumas das vulnerabilidades que devemos procurar na saída desses scripts.

## Exploits de Kernel

Sempre que encontrarmos um servidor executando um sistema operacional antigo, devemos começar procurando possíveis vulnerabilidades do kernel que possam existir. Suponha que o servidor não esteja sendo mantido com as últimas atualizações e patches. Nesse caso, é provável que seja vulnerável a exploits de kernel específicos encontrados em versões não corrigidas do Linux e do Windows.

Por exemplo, o script acima nos mostrou que a versão do Linux é `3.9.0-73-generic`. Se explorarmos o Google para esta versão ou usarmos `searchsploit`, encontraremos um `CVE-2016-5195`, também conhecido como `DirtyCow`. Podemos procurar e baixar o exploit [DirtyCow](https://github.com/dirtycow/dirtycow.github.io/wiki/PoCs) e executá-lo no servidor para obter acesso root.

O mesmo conceito também se aplica ao Windows, pois há muitas vulnerabilidades em versões antigas/não corrigidas do Windows, com várias vulnerabilidades que podem ser usadas para escalonamento de privilégios. Devemos ter em mente que as explorações do kernel podem causar instabilidade no sistema e devemos tomar muito cuidado antes de executá-las em sistemas de produção. É melhor experimentá-los em um ambiente de laboratório e apenas executá-los em sistemas de produção com aprovação explícita e coordenação com nosso cliente.

## Software Vulnerável

Outra coisa que devemos procurar é o software instalado. Por exemplo, podemos usar o comando `dpkg -l` no Linux ou olhar `C:\Program Files` no Windows para ver qual software está instalado no sistema. Devemos procurar exploits públicos para qualquer software instalado, especialmente se alguma versão mais antiga estiver em uso, contendo vulnerabilidades não corrigidas.

## Privilégios do usuário

Outro aspecto crítico a ser observado após obter acesso a um servidor são os privilégios disponíveis para o usuário ao qual temos acesso. Suponha que tenhamos permissão para executar comandos específicos como root (ou como outro usuário). Nesse caso, podemos aumentar nossos privilégios para usuários root/sistema ou obter acesso como um usuário diferente. Abaixo estão algumas maneiras comuns de explorar certos privilégios do usuário:
- sudo
- SUID
- Privilégios de token do WIndows

O comando `sudo` no Linux permite que um usuário execute comandos como um usuário diferente. Geralmente é usado para permitir que usuários com privilégios inferiores executem comandos como root sem dar a eles acesso ao usuário root. Isso geralmente é feito porque comandos específicos só podem ser executados como root 'like `tcpdump`' ou permitir que o usuário acesse determinados diretórios somente root. Podemos verificar quais privilégios `sudo` temos com o comando `sudo -l`:

```shell-session
casluxd@htb[/htb]$ sudo -l

[sudo] password for user1:
...SNIP...

User user1 may run the following commands on ExampleServer:
    (ALL : ALL) ALL
```

A saída acima diz que podemos executar todos os comandos com `sudo`, o que nos dá acesso completo, e podemos usar o comando `su` com `sudo` para alternar para o usuário root:

```shell-session
casluxd@htb[/htb]$ sudo su -

[sudo] password for user1:
whoami
root
```

O comando acima requer uma senha para executar qualquer comando com `sudo`. Existem certas ocasiões em que podemos executar determinados aplicativos, ou todos os aplicativos, sem a necessidade de fornecer uma senha:

```shell-session
casluxd@htb[/htb]$ sudo -l

    (user : user) NOPASSWD: /bin/echo
```

A entrada `NOPASSWD` mostra que o comando `/bin/echo` pode ser executado sem uma senha. Isso seria útil se tivéssemos acesso ao servidor por meio de uma vulnerabilidade e não tivéssemos a senha do usuário. Como diz `user`, podemos executar `sudo` como esse usuário e não como root. Para fazer isso, podemos especificar o usuário com `-u user`:

```shell-session
casluxd@htb[/htb]$ sudo -u user /bin/echo Hello World!

    Hello World!
```

[LOLBAS](https://lolbas-project.github.io/#) também contém uma lista de aplicativos do Windows que podemos aproveitar para realizar determinadas funções, como baixar arquivos ou executar comandos no contexto de um usuário privilegiado.

## Atividades agendadas

Tanto no Linux quanto no Windows, existem métodos para executar scripts em intervalos específicos para realizar uma tarefa. Alguns exemplos são a execução de uma varredura antivírus a cada hora ou um script de backup executado a cada 30 minutos. Geralmente, existem duas maneiras de aproveitar as tarefas agendadas (Windows) ou cron jobs (Linux) para escalar nossos privilégios:
1. Adicionar novas tarefas agendadas/cron jobs
2.  Enganá-los para executar um software malicioso

A maneira mais fácil é verificar se podemos adicionar novas tarefas agendadas. No Linux, uma forma comum de manter tarefas agendadas é por meio de arquivos `Cron Jobs`. Existem diretórios específicos que podemos utilizar para adicionar novos trabalhos cron se tivermos permissões `write` sobre eles. Esses incluem:

1. `/etc/crontab`
2. `/etc/cron.d`
3. `/var/spool/cron/crontabs/root`

Se pudermos gravar em um diretório chamado por um trabalho cron, podemos escrever um script bash com um comando shell reverso, que deve nos enviar um shell reverso quando executado.

## Credenciais expostas

Em seguida, podemos procurar arquivos que possamos ler e ver se eles contêm alguma credencial exposta. Isso é muito comum com arquivos `configuration`, arquivos `log` e arquivos de histórico do usuário ( `bash_history` no Linux e `PSReadLine` no Windows). Os scripts de enumeração que discutimos no início geralmente procuram senhas potenciais em arquivos e as fornecem para nós, conforme abaixo:

```shell-session
...SNIP...
[+] Searching passwords in config PHP files
[+] Finding passwords inside logs (limit 70)
...SNIP...
/var/www/html/config.php: $conn = new mysqli(localhost, 'db_user', 'password123');
```

Como podemos ver, a senha do banco de dados ' `password123`' está exposta, o que nos permitiria acessar os bancos de dados locais `mysql` e procurar informações interessantes. Também podemos verificar `Password Reuse`, pois o usuário do sistema pode ter usado sua senha para os bancos de dados, o que pode nos permitir usar a mesma senha para alternar para esse usuário, conforme segue:

```shell-session
casluxd@htb[/htb]$ su -

Password: password123
whoami

root
```

Também podemos usar as credenciais do usuário para entrar `ssh` e no servidor como esse usuário.

## Chaves SSH

Finalmente, vamos discutir as chaves SSH. Se tivermos acesso de leitura ao `.ssh`diretório para um usuário específico, podemos ler suas chaves ssh privadas encontradas em `/home/user/.ssh/id_rsa` ou `/root/.ssh/id_rsa`, e usá-las para fazer login no servidor. Se pudermos ler o diretório `/root/.ssh/` e o arquivo `id_rsa`, podemos copiá-lo para nossa máquina e usar o sinalizador `-i` para fazer login com ele:

```shell-session
casluxd@htb[/htb]$ vim id_rsa
casluxd@htb[/htb]$ chmod 600 id_rsa
casluxd@htb[/htb]$ ssh user@10.10.10.10 -i id_rsa

root@remotehost#
```

Observe que usamos o comando 'chmod 600 id_rsa' na chave depois de criá-la em nossa máquina para alterar as permissões do arquivo para serem mais restritivas. Se as chaves ssh tiverem permissões negligentes, ou seja, talvez lidas por outras pessoas, o servidor ssh as impediria de funcionar.

Se tivermos acesso de gravação a um diretório de usuários `/.ssh/`, podemos colocar nossa chave pública no diretório ssh do usuário em `/home/user/.ssh/authorized_keys`. Essa técnica geralmente é usada para obter acesso ssh após obter um shell como esse usuário. A configuração SSH atual não aceitará chaves escritas por outros usuários, portanto, só funcionará se já tivermos controle sobre esse usuário. Devemos primeiro criar uma nova chave com `ssh-keygen` e o sinalizador `-f` para especificar o arquivo de saída:

```shell-session
casluxd@htb[/htb]$ ssh-keygen -f key

Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase): *******
Enter same passphrase again: *******

Your identification has been saved in key
Your public key has been saved in key.pub
The key fingerprint is:
SHA256:...SNIP... user@parrot
The key's randomart image is:
+---[RSA 3072]----+
|   ..o.++.+      |
...SNIP...
|     . ..oo+.    |
+----[SHA256]-----+
```

Isso nos dará dois arquivos: `key`(que usaremos com `ssh -i`) e `key.pub`, que copiaremos para a máquina remota. Vamos copiar `key.pub`, então na máquina remota, vamos adicioná-lo em `/root/.ssh/authorized_keys`:

```shell-session
user@remotehost$ echo "ssh-rsa AAAAB...SNIP...M= user@parrot" >> /root/.ssh/authorized_keys
```

Agora, o servidor remoto deve nos permitir fazer login como esse usuário usando nossa chave privada:

```shell-session
casluxd@htb[/htb]$ ssh root@10.10.10.10 -i key

root@remotehost# 
```

