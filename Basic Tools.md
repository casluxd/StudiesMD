
Ferramentas como `SSH`, `Netcat`, `Tmux` e `Vim` são essenciais e são utilizadas diariamente pela maioria dos profissionais de segurança da informação. Embora essas ferramentas não sejam ferramentas de teste de penetração, elas são críticas para o processo de teste de penetração, portanto, devemos dominá-las.

# Usando SSH

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/SSH_(Secure_Shell)) é um protocolo de rede executado na porta `22` por padrão e fornece aos usuários, como administradores de sistema, uma maneira segura de acessar um computador remotamente. O SSH pode ser configurado com autenticação de senha ou sem senha usando [autenticação de chave pública](https://serverpilot.io/docs/how-to-use-ssh-public-key-authentication/) usando um par de chaves pública/privada SSH. O SSH pode ser usado para acessar remotamente sistemas na mesma rede, pela Internet, facilitar conexões a recursos em outras redes usando encaminhamento/proxy de porta e fazer upload/download de arquivos de e para sistemas remotos.

O SSH usa um modelo cliente-servidor, conectando um usuário executando um aplicativo cliente SSH, como `OpenSSH` um servidor SSH. Ao atacar uma caixa ou durante uma avaliação do mundo real, geralmente obtemos credenciais de texto não criptografado ou uma chave privada SSH que pode ser aproveitada para conectar-se diretamente a um sistema via SSH. Uma conexão SSH é normalmente muito mais estável do que uma conexão de shell reverso e pode ser frequentemente usada como um "host de salto" para enumerar e atacar outros hosts na rede, transferir ferramentas, configurar persistência, etc. Se obtivermos um conjunto de credenciais , podemos usar o SSH para fazer login remotamente no servidor usando o nome de usuário `@` do IP do servidor remoto, conforme a seguir:

```shell-session
casluxd@htb[/htb]$ ssh Bob@10.10.10.10

Bob@remotehost's password: *********

Bob@remotehost#
```

Também é possível ler chaves privadas locais em um sistema comprometido ou adicionar nossa chave pública para obter acesso SSH a um usuário específico, conforme discutiremos em uma seção posterior. Como podemos ver, o SSH é uma excelente ferramenta para se conectar com segurança a uma máquina remota. Ele também fornece uma maneira de mapear portas locais na máquina remota para nosso host local, o que pode ser útil às vezes.

# Usando o Netcat

[Netcat](https://linux.die.net/man/1/nc) , `ncat`, ou `nc`, é um excelente utilitário de rede para interagir com portas TCP/UDP. Ele pode ser usado para muitas coisas durante um pentest. Seu uso principal é para conectar-se a shells, que discutiremos mais adiante neste módulo. Além disso, `netcat` pode ser usado para conectar-se a qualquer porta de escuta e interagir com o serviço executado nessa porta. Por exemplo, `SSH` está programado para lidar com conexões na porta 22 para enviar todos os dados e chaves. Podemos nos conectar à porta TCP 22 com `netcat`:

```shell-session
casluxd@htb[/htb]$ netcat 10.10.10.10 22

SSH-2.0-OpenSSH_8.4p1 Debian-3
```

Como podemos ver, a porta 22 nos enviou seu banner, informando que `SSH`está rodando nela. Essa técnica é chamada `Banner Grabbing` e pode ajudar a identificar qual serviço está sendo executado em uma determinada porta. `Netcat`vem pré-instalado na maioria das distribuições Linux. Também podemos baixar uma cópia para máquinas Windows neste [link](https://nmap.org/download.html) . Há outra alternativa do Windows para `netcat` codificar no PowerShell chamada [PowerCat](https://github.com/besimorhino/powercat) . `Netcat` também pode ser usado para transferir arquivos entre máquinas, como discutiremos mais adiante.

Outro utilitário de rede semelhante é [o socat](https://linux.die.net/man/1/socat) , que possui alguns recursos `netcat` incompatíveis, como encaminhamento de portas e conexão com dispositivos seriais. `Socat` também pode ser usado para [atualizar um shell para um TTY totalmente interativo](https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/#method-2-using-socat) . Veremos alguns exemplos disso em uma seção posterior. `Socat`é um utilitário muito útil que deve fazer parte do kit de ferramentas de todos os testadores de penetração. Um [binário autônomo](https://github.com/andrew-d/static-binaries) de `Socat` pode ser transferido para um sistema após obter a execução remota do código para obter uma conexão shell reversa mais estável.

# Usando Tmux

Multiplexadores de terminal, como `tmux` ou `Screen`, são ótimos utilitários para expandir os recursos de um terminal Linux padrão, como ter várias janelas em um terminal e alternar entre elas. Vejamos alguns exemplos de uso de `tmux`, que é o mais comum dos dois. Se `tmux` não estiver presente em nosso sistema Linux, podemos instalá-lo com o seguinte comando:

```shell-session
casluxd@htb[/htb]$ sudo apt install tmux -y
```

![[Pasted image 20230613231053.png]]

A chave padrão para `tmux`o prefixo dos comandos de entrada é `[CTRL + B]`. Para abrir uma nova janela em `tmux`, podemos pressionar o prefixo 'ie `[CTRL + B]`' e então pressionar `C`:

![[Pasted image 20230613231105.png]]

Vemos as janelas numeradas na parte inferior. Podemos alternar para cada janela pressionando o prefixo e inserindo o número da janela, como `0` ou `1`. Também podemos dividir uma janela verticalmente em painéis pressionando o prefixo e depois `[SHIFT + %]`:

![[Pasted image 20230613231120.png]]

Também podemos dividir em painéis horizontais pressionando o prefixo e depois `[SHIFT + "]`:

![[Pasted image 20230613231131.png]]

Podemos alternar entre os painéis pressionando o prefixo e, em seguida, as setas `left` ou `right` para comutação horizontal ou as setas `up` ou `down` para comutação vertical. Os comandos acima cobrem alguns `tmux` usos básicos. É uma ferramenta poderosa e pode ser usada para muitas coisas, inclusive registro, que é muito importante durante qualquer compromisso técnico. Esta [folha de dicas](https://tmuxcheatsheet.com/) é uma referência muito útil. Além disso, esta introdução ao vídeo [tmux](https://www.youtube.com/watch?v=Lqehvpe_djs)`ippsec` vale o seu tempo.

