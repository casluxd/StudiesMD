Todo sistema operacional possui um shell e, para interagir com ele, precisamos usar um aplicativo conhecido como `terminal emulator`. Aqui estão alguns dos emuladores de terminais mais comuns:

|**Emulador de terminal**|**Sistema operacional**|
|:--|:--|
|[Terminal do Windows](https://github.com/microsoft/terminal)|Windows|
|[cmder](https://cmder.app/)|Windows|
|[PuTTY](https://www.putty.org/)|Windows|
|[gatinho](https://sw.kovidgoyal.net/kitty/)|Windows, Linux e MacOS|
|[Alacritty](https://github.com/alacritty/alacritty)|Windows, Linux e MacOS|
|[xterm](https://invisible-island.net/xterm/)|Linux|
|[Terminal GNOME](https://en.wikipedia.org/wiki/GNOME_Terminal)|Linux|
|[Terminal MATE](https://github.com/mate-desktop/mate-terminal)|Linux|
|[Konsole](https://konsole.kde.org/)|Linux|
|[Terminal](https://en.wikipedia.org/wiki/Terminal_(macOS))|MacOS|
|[iTERm2](https://iterm2.com/)|MacOS|

Esta lista não é de forma alguma todo emulador de terminal disponível, mas inclui alguns dignos de nota. Além disso, como muitas dessas ferramentas são de código aberto, podemos instalá-las em diferentes sistemas operacionais de maneira a diferir das intenções originais dos desenvolvedores. No entanto, esse é um projeto além do escopo deste módulo. Selecionar o emulador de terminal adequado para o trabalho é principalmente uma preferência pessoal e estilística, com base em nossos fluxos de trabalho que se desenvolvem à medida que nos familiarizamos com o sistema operacional de escolha. Portanto, não deixe ninguém fazer você se sentir mal por selecionar uma opção em detrimento da outra. O emulador de terminal com o qual interagimos nos destinos dependerá essencialmente do que existe no sistema nativamente.

## Intérpretes de linguagem de comando

Assim como um intérprete de linguagem humana traduzirá a linguagem falada ou de sinais em tempo real, um `command language interpreter` é um programa que trabalha para interpretar as instruções fornecidas pelo usuário e emitir as tarefas para o sistema operacional para processamento. Portanto, quando discutimos interfaces de linha de comando, sabemos que é uma combinação do sistema operacional, aplicativo emulador de terminal e intérprete de linguagem de comando. Muitos intérpretes de linguagem de comando diferentes podem ser usados, alguns dos quais também são chamados `shell scripting languages` ou `Command and Scripting interpreters` conforme definido no [Técnicas de execução](https://attack.mitre.org/techniques/T1059/) do `MITRE ATT&CK Matrix`. Não precisamos ser desenvolvedores de software para entender esses conceitos, mas quanto mais sabemos, mais sucesso podemos ter ao tentar explorar sistemas vulneráveis para obter uma sessão de shell.

Compreender o intérprete de linguagem de comando em uso em qualquer sistema também nos dará uma idéia de quais comandos e scripts devemos usar. Vamos nos dedicar a alguns desses conceitos.

## Prático com emuladores e campainhas terminais

Vamos usar o nosso `Parrot OS` Pwnbox para explorar ainda mais a anatomia de uma concha. Clique no `green` ícone quadrado na parte superior da tela para abrir a `MATE` emulador de terminal e, em seguida, digite algo aleatório e pressione enter.

#### Exemplo de terminal

![imagem](https://academy.hackthebox.com/storage/modules/115/green-square.png)

Assim que selecionamos o ícone, ele abriu o aplicativo emulador de terminal MATE, que foi pré-configurado para usar um intérprete de linguagem de comando. Nesse caso, estamos "colados" ao que o intérprete de idioma está em uso ao ver o `$` sinal. Este sinal $ é usado em Bash, Ksh, POSIX e em muitos outros idiomas do shell para marcar o início do `shell prompt`onde o usuário pode começar a digitar comandos e outras entradas. Quando digitamos nosso texto aleatório e pressionamos enter, nosso intérprete de linguagem de comando foi identificado. Isso é Bash nos dizendo que não reconheceu o comando que digitamos. Então, aqui, podemos ver que os intérpretes de linguagem de comando podem ter seu próprio conjunto de comandos que eles reconhecem. Outra maneira de identificar o intérprete de idioma é visualizando os processos em execução na máquina. No Linux, podemos fazer isso usando o seguinte comando:

#### Validação de shell de 'ps'

```shell-session
casluxd@htb[/htb]$ ps

    PID TTY          TIME CMD
   4232 pts/1    00:00:00 bash
  11435 pts/1    00:00:00 ps
```

Também podemos descobrir qual linguagem de shell está em uso visualizando as variáveis de ambiente usando o `env` comando:

#### Validação de shell usando 'env'

```shell-session
casluxd@htb[/htb]$ env

SHELL=/bin/bash
```

Agora vamos selecionar o ícone quadrado azul na parte superior da tela no Pwnbox.

#### PowerShell vs. Bash

![imagem](https://academy.hackthebox.com/storage/modules/115/blue-box.png)

Selecionar esse ícone também abre o aplicativo de terminal MATE, mas usa um intérprete de linguagem de comando diferente desta vez. Compare-os quando forem colocados lado a lado.

- `What differences can we identify?`
- `Why would we use one over the other on the same system?`

Existem inúmeras diferenças e personalizações que poderíamos descobrir. Tente usar alguns comandos que você conhece em ambos e anote as diferenças na saída e quais comandos são reconhecidos. Um dos principais pontos que podemos tirar disso é que um emulador de terminal não está vinculado a um idioma específico. Na verdade, o idioma do shell pode ser alterado e personalizado para atender às necessidades pessoais, de fluxo de trabalho e técnicas do administrador do sistema, desenvolvedor ou pentista.