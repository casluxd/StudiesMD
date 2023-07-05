No final da última seção, estabelecemos uma sessão de shell com o alvo. Inicialmente, nossa shell era limitada ( às vezes referida como prison shell ), então usamos python para gerar um shell TTY bourne para nos dar acesso a mais comandos e um prompt para trabalhar. Provavelmente será uma situação em que nos encontramos cada vez mais à medida que praticamos no Hack The Box e no mundo real em compromissos.

Pode haver momentos em que pousamos em um sistema com um shell limitado e o Python não está instalado. Nesses casos, é bom saber que poderíamos usar vários métodos diferentes para gerar um shell interativo. Vamos examinar alguns deles.

Saiba que sempre que vemos `/bin/sh` ou `/bin/bash`, isso também pode ser substituído pelo binário associado ao idioma do intérprete de shell presente nesse sistema. Com a maioria dos sistemas Linux, provavelmente nos depararemos `bourne shell` (`/bin/sh`) e `bourne again shell` (`/bin/bash`) presente no sistema nativamente.

## /bin / sh -i

Este comando executará o intérprete de shell especificado no caminho no modo interativo (`-i`).

```shell-session
/bin/sh -i
sh: no job control in this shell
sh-4.2$
```

## Perl

Se a linguagem de programação [Perl](https://www.perl.org/) está presente no sistema, esses comandos executam o intérprete de shell especificado.

```shell-session
perl —e 'exec "/bin/sh";'
```

```shell-session
perl: exec "/bin/sh";
```

O comando diretamente acima deve ser executado a partir de um script.

## Ruby

Se a linguagem de programação [Ruby](https://www.ruby-lang.org/en/) está presente no sistema, este comando executará o intérprete de shell especificado:

#### Ruby To Shell

```shell-session
ruby: exec "/bin/sh"
```

O comando diretamente acima deve ser executado a partir de um script.

## Lua

Se a linguagem de programação [Lua](https://www.lua.org/) está presente no sistema, podemos usar o método `os.execute` para executar o intérprete de shell especificado usando o comando completo abaixo:

#### Lua para Shell

```shell-session
lua: os.execute('/bin/sh')
```

O comando diretamente acima deve ser executado a partir de um script.

## AWK

[AWK](https://man7.org/linux/man-pages/man1/awk.1p.html) é uma linguagem de processamento e varredura de padrões do tipo C, presente na maioria dos sistemas baseados em UNIX / Linux, amplamente usada por desenvolvedores e administradores de sistemas para gerar relatórios. Também pode ser usado para gerar um shell interativo. Isso é mostrado no script awk curto abaixo:

```shell-session
awk 'BEGIN {system("/bin/sh")}'
```

## Find

[Find](https://man7.org/linux/man-pages/man1/find.1.html) é um comando presente na maioria dos sistemas Unix / Linux amplamente usado para procurar e através de arquivos e diretórios usando vários critérios. Também pode ser usado para executar aplicativos e chamar um intérprete de shell.

```shell-session
find / -name nameoffile -exec /bin/awk 'BEGIN {system("/bin/sh")}' \;
```

Esse uso do comando find usa a opção execute (`-exec`) para iniciar o intérprete de shell diretamente. Se `find` não é possível encontrar o arquivo especificado, nenhum shell será atingido.

## VIM

Sim, podemos definir o idioma do intérprete de shell de dentro do popular editor de texto baseado em linha de comando `VIM`. Essa é uma situação muito nicho em que precisaríamos usar esse método, mas é bom saber apenas por precaução.

```shell-session
vim -c ':!/bin/sh'
```

```shell-session
vim
:set shell=/bin/sh
:shell
```

## Considerações sobre permissões de execução

Além de conhecer todas as opções listadas acima, devemos estar atentos às permissões que temos com a conta da sessão do shell. Sempre podemos tentar executar este comando para listar as propriedades e permissões de arquivos que nossa conta possui sobre qualquer arquivo ou binário:

#### Permissões

  Permissões

```shell-session
ls -la <path/to/fileorbinary>
```

Também podemos tentar executar este comando para verificar o que `sudo` permite que a conta em que chegamos tenha:

#### Sudo -l

  Sudo -l

```shell-session
sudo -l
Matching Defaults entries for apache on ILF-WebSrv:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User apache may run the following commands on ILF-WebSrv:
    (ALL : ALL) NOPASSWD: ALL
```

O comando sudo -l acima precisará de um shell interativo estável para ser executado. Se você não estiver com uma shell cheia ou em uma shell instável, poderá não receber nenhum retorno dela. Não apenas considerar as permissões nos permitirá ver quais comandos podemos executar, mas também pode começar a nos dar uma ideia de vetores em potencial que nos permitirão aumentar os privilégios.


