No Linux, tudo são arquivos. A ideia central é tratar tudo, desde arquivos regulares a diretórios, dispositivos, sockets e outros recursos do sistema, como arquivos ou objetos de arquivos virtuais. Isso permite que os programas e comandos interajam com esses recursos usando as mesmas interfaces e operações de leitura e gravação que são usadas com arquivos comuns. 

Algumas das implicações dessa abordagem incluem:
1. Acesso uniforme: Os programas podem usar chamadas de sistema de E/S (entrada/saída) padrão como, `open()`, `read()`, `write()`, `close()`, para interagir com diferentes tipos de recursos, sejam eles arquivos, dispositivos ou outras entidades.
2. Manipulação consistente: Os comandos do Linux podem usar as mesmas operações de leitura, gravação, exclusão, cópia e movimentação de arquivos para manipular tanto arquivos regulares quanto diretórios, dispositivos especiais, interfaces de rede e outros recursos do sistema.
3. Abstração simplificada: : Essa abordagem torna o sistema mais modular e oferece uma camada de abstração comum para os aplicativos. Dessa forma, os aplicativos não precisam se preocupar com as complexidades específicas de cada tipo de recurso, mas apenas com a manipulação de arquivos.

Porém nem tudo no Linux são tratados como arquivos físicos no sistema de arquivos. Alguns "arquivos" são, na verdade, interface para controlar ou acessar recursos específicos, como dispositivos de hardware ou configurações do sistema.

Os dispositivos podem ser acessados por meio de mapeamento denominados Arquivos de Dispositivos.
`Ex: /dev/sda`

Um arquivo de Dispositivo é um objeto do sistema que oferece uma interface para o dispositivo em si.
O Kernel associa os drivers de dispositivos aos arquivos de dispositivos.

Descritos de Arquivos é uma abstração de uma identificação para acessar um arquivo.
Existem três descritores:
- Entrada padrão(stdin): É um stream para entrada de texto. Por exemplo, quando realiza a leitura de um texto, é utilizado um stream de entrada de texto. Por padrão, a entrada é vinculada ao teclado. Também é conhecido como Descritor de Arquivos 0.
- Saída padrão (stdout): É um stream para a saída normal dos programas. Quando algo ocorre com sucesso, ela é direcionada para a saída padrão. Por padrão, a saída está vinculada ao terminal ou janela de terminal. Também é conhecido como Descritor de Arquivos 1.
- Erro padrão (stderr): É um stream de saída de texto, usado para exibir mensagens de erro ou mensagens que não estão relacionadas a saída normal dos programas. Por padrão, está vinculado ao terminal. Também é conhecido como Descritor de Arquivos 2.


# Pipes

Para os programas, ler dados a partir de um arquivo ou teclado é a mesma coisa. Por exemplo, quando você pede para um programa realizar a leitura, ele pode tanto realizar de um arquivo, quanto de um teclado.

Mais interessante, um programa pode ler a partir da saída de um programa ou enviar dados para entrada de outro programa.

O pipe `|` permite juntar dois ou mais comandos de modo que eles são executados em sequência.

Um exemplo:
`ls -l | less`

Basicamente a saída do `ls -l`, que por padrão é vinculado a saída padrão(terminal),  é redirecionada para o comando `less`, que nesse caso, ao invés de receber os comandos da entrada padrão, recebe da saída de `ls`.

Se forem usados mais de dois comandos com redirecionamentos,  damos o nome de pipeline à operação resultante.


# Redirecionamentos

O pipe redireciona a saída de um comando para outro comando. Mas também é possível redirecionar a saída de um comando para um arquivo ou ler a partir de um arquivo. Para isso, é utilizado o operador de redirecionamento `>`. 

Exemplo:
`ls -i > inodes.txt`

Por padrão, as saídas redirecionadas para um arquivo não são exibidas na saída padrão (terminal), exceto os erros padrão.

O operador `>` cria arquivos, se o arquivo já existe, ele é substituído.  Para não sobrescrever o conteúdo de um arquivo já existente, use o operador `>>`, ele anexa o conteúdo ao final do arquivo.

Comando: `cat < /etc/goup > /tmp/grupos`

Basicamente, o operador `<` redireciona a saída de um arquivo para um comando.

Você pode trabalhar também com redirecionamentos com base nos tipos de saídas, por exemplo: 

`ls -zz 2>/dev/null`

Basicamente, antes do operador de redirecionamento, seria colocado o número do descritor de arquivos e posteriormente você redirecionaria para o local desejado.

# Comando tee

Permite enviar a saída de um comando para um arquivo e para a tela ao mesmo tempo.

Sintaxe:
`tee [opções] arquivos`

Exemplo:
`ls -l | tee arquivo1`

Esse comando redireciona a saída do ls -l para o tee, que é responsável por gravar no arquivo1 e redirecionar a saída para a tela ao mesmo tempo.

