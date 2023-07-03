A vantagem dos scripts bash é que sempre podemos passar até 9 argumentos ($0-$9) para o script sem atribuí-los a variáveis ​​ou definir os requisitos correspondentes para eles. 9 argumentos porque o primeiro argumento `$0` é reservado para o script. Como podemos ver aqui, precisamos do cifrão `($)` antes do nome da variável para usá-la na posição especificada. A atribuição ficaria assim em comparação:

```shell-session
casluxd@htb[/htb]$ ./script.sh ARG1 ARG2 ARG3 ... ARG9
       ASSIGNMENTS:       $0      $1   $2   $3 ...   $9
```

Isso significa que atribuímos automaticamente os argumentos correspondentes às variáveis ​​predefinidas neste local. Essas variáveis ​​especiais servem como espaços reservados. Se olharmos novamente para a seção de código, veremos onde e quais argumentos foram usados.

```bash
#!/bin/bash

# Check for given argument
if [ $# -eq 0 ]
then
	echo -e "You need to specify the target domain.\n"
	echo -e "Usage:"
	echo -e "\t$0 <domain>"
	exit 1
else
	domain=$1
fi

<SNIP>
```

Existem várias maneiras de como podemos executar nosso script. No entanto, devemos primeiro definir os privilégios de execução do script antes de executá-lo com o interpretador definido nele.

```shell-session
casluxd@htb[/htb]$ chmod +x cidr.sh
```

```shell-session
casluxd@htb[/htb]$ ./cidr.sh

You need to specify the target domain.

Usage:
	cidr.sh <domain>
```

```shell-session
casluxd@htb[/htb]$ bash cidr.sh

You need to specify the target domain.

Usage:
	cidr.sh <domain>
```

# Special variables

Variáveis ​​especiais usam o Separador de Campo Interno (IFS) para identificar quando um argumento termina e o próximo começa. O Bash fornece várias variáveis ​​especiais que auxiliam durante o script. Algumas dessas variáveis ​​são:

- $#: Essa variável contém o número de argumentos passados ​​para o script.
- $@: Essa variável pode ser usada para recuperar a lista de argumentos de linha de comando.
- $n: Cada argumento de linha de comando pode ser recuperado seletivamente usando sua posição. Por exemplo, o primeiro argumento é encontrado em $1.
- `$$`: O ID do processo em execução no momento.
- $?: O status de saída do script. Esta variável é útil para determinar o sucesso de um comando. O valor 0 representa a execução bem-sucedida, enquanto 1 é resultado de uma falha.

Das mostradas acima, temos 3 dessas variáveis ​​especiais em nossa condição if-else.

- $#: Nesse caso, precisamos de apenas uma variável que precisa ser atribuída à variável de domínio. Esta variável é usada para especificar o alvo com o qual queremos trabalhar.
- $0: Esta variável especial recebe o nome do script executado, que é mostrado no exemplo "Usage:".
- $1: Separado por um espaço, o primeiro argumento é atribuído a essa variável especial.

# Variáveis

```bash
<SNIP>
else
	domain=$1
fi
<SNIP>
```

Em contraste com outras linguagens de programação, não há diferenciação e reconhecimento direto entre os tipos de variáveis ​​no Bash, como "strings", "inteiros" e "booleanos". Todo o conteúdo das variáveis ​​são tratados como caracteres de string. O Bash permite funções aritméticas dependendo se apenas números são atribuídos ou não. É importante observar ao declarar variáveis ​​que elas não contêm um espaço. Caso contrário, o nome da variável real será interpretado como uma função interna ou um comando.

```shell-session
casluxd@htb[/htb]$ variable = "this will result with an error."

command not found: variable
```

```shell-session
casluxd@htb[/htb]$ variable="Declared without an error."
casluxd@htb[/htb]$ echo $variable

Declared without an error.
```

# Arrays

Existe também a possibilidade de atribuir vários valores a uma única variável no Bash. Isso pode ser benéfico se quisermos verificar vários domínios ou endereços IP. Essas variáveis ​​são chamadas de arrays que podemos usar para armazenar e processar uma sequência ordenada de valores de tipos específicos. Arrays identificam cada entrada armazenada com um índice começando com 0. Quando queremos atribuir um valor a um componente de array, fazemos isso da mesma forma que com variáveis ​​shell padrão. Tudo o que fazemos é especificar o índice de campo entre colchetes. A declaração para arrays se parece com isso no Bash:

```bash
#!/bin/bash

domains=(www.inlanefreight.com ftp.inlanefreight.com vpn.inlanefreight.com www2.inlanefreight.com)

echo ${domains[0]}
```

Também podemos recuperá-los individualmente usando o índice usando a variável com o índice correspondente entre chaves. Os colchetes são usados ​​para expansão variável.

```shell-session
casluxd@htb[/htb]$ ./Arrays.sh

www.inlanefreight.com
```

É importante observar que as aspas simples (' ... ') e aspas duplas (" ... ") impedem a separação por espaço dos valores individuais na matriz. Isso significa que todos os espaços entre aspas simples e duplas são ignorados e tratados como um único valor atribuído ao array.

```bash
#!/bin/bash

domains=("www.inlanefreight.com ftp.inlanefreight.com vpn.inlanefreight.com" www2.inlanefreight.com)
echo ${domains[0]}
```

```shell-session
casluxd@htb[/htb]$ ./Arrays.sh

www.inlanefreight.com ftp.inlanefreight.com vpn.inlanefreight.com
```

