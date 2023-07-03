Para comparar valores específicos entre si, precisamos de elementos que são chamados de operadores de comparação. Os operadores de comparação são usados ​​para determinar como os valores definidos serão comparados. Para esses operadores, diferenciamos entre:
- string operators
- integer operators
- file operators
- boolean operators

## String Operators

Se compararmos strings, saberemos o que gostaríamos de ter no valor correspondente.
- == : é igual a
- != : não é igual a
- <: é menor que em ordem alfabética ASCII
- >: é maior que na ordem alfabética ASCII
-  -z: se a string estiver vazia (nulo)
- -n: se a string não for nula

É importante observar aqui que colocamos a variável para o argumento dado ($1) entre aspas ("$1"). Isso informa ao Bash que o conteúdo da variável deve ser tratado como uma string. Caso contrário, teríamos um erro.

```bash
#!/bin/bash

# Check the given argument
if [ "$1" != "HackTheBox" ]
then
	echo -e "You need to give 'HackTheBox' as argument."
	exit 1

elif [ $# -gt 1 ]
then
	echo -e "Too many arguments given."
	exit 1

else
	domain=$1
	echo -e "Success!"
fi
```

Operadores de comparação de strings "< / >" funcionam apenas dentro dos colchetes duplos `[[ <condição> ]]`. Podemos encontrar a tabela ASCII na Internet ou usando o seguinte comando no terminal. Vamos dar uma olhada em um exemplo mais tarde.

```shell-session
casluxd@htb[/htb]$ man ascii
```

|**Decimal**|**Hexadecial**|**Character**|**Description**|
|---|---|---|---|
|0|00|NUL|End of a string|
|...|...|...|...|
|65|41|A|Capital A|
|66|42|B|Capital B|
|67|43|C|Capital C|
|68|44|D|Capital D|
|...|...|...|...|
|127|7F|DEL|Delete|

ASCII significa American Standard Code for Information Interchange e representa uma codificação de caracteres de 7 bits. Como cada bit pode assumir dois valores, existem 128 padrões de bits diferentes, que também podem ser interpretados como inteiros decimais de 0 a 127 ou em valores hexadecimais de 00 a 7F. Os primeiros 32 códigos de caracteres ASCII são reservados como os chamados caracteres de controle.

## Integer Operators

A comparação de números inteiros pode ser muito útil para nós se soubermos quais valores queremos comparar. Assim, definimos os próximos passos e comandos de como o script deve tratar o valor correspondente.

- -eq: é igual a
- -ne: não é igual a
- -lt: é menor que
- -le: é menor que ou igual a
- -gt: é maior que
- -ge: é maior que ou igual a

```bash
#!/bin/bash

# Check the given argument
if [ $# -lt 1 ]
then
	echo -e "Number of given arguments is less than 1"
	exit 1

elif [ $# -gt 1 ]
then
	echo -e "Number of given arguments is greater than 1"
	exit 1

else
	domain=$1
	echo -e "Number of given arguments equals 1"
fi
```

## File Operators

Os operadores de arquivo são úteis se quisermos descobrir permissões específicas ou se elas existem.

- -e: se o arquivo existe
- -f: se é um arquivo
- -d: se é um diretorio
- -L: se é um link simbólico
- -N: verifica se o arquivo foi modificado após sua última leitura
- -O: se o usuário atual for o proprietário do arquivo
- -G: se o ID do grupo do arquivo corresponder ao do usuário atual
- -s: testa se o arquivo tem tamanho maior que 0
- -r: testa se o arquivo tem permissão de leitura
- -w: testa se o arquivo tem permissão de gravação
- -x: testa se o arquivo tem permissão de execução

```bash
#!/bin/bash

# Check if the specified file exists
if [ -e "$1" ]
then
	echo -e "The file exists."
	exit 0

else
	echo -e "The file does not exist."
	exit 2
fi
```


## Boolean and Logical Operators

Obtemos um valor booleano "falso" ou "verdadeiro" como resultado com operadores lógicos. Bash nos dá a possibilidade de comparar strings usando colchetes duplos `[[ <condição> ]]`. Para obter esses valores booleanos, podemos usar os operadores de string. Independentemente de a comparação corresponder ou não, obtemos o valor booleano "falso" ou "verdadeiro".

```bash
#!/bin/bash

# Check the boolean value
if [[ -z $1 ]]
then
	echo -e "Boolean value: True (is null)"
	exit 1

elif [[ $# > 1 ]]
then
	echo -e "Boolean value: True (is greater than)"
	exit 1

else
	domain=$1
	echo -e "Boolean value: False (is equal to)"
fi
```

#### Logical Operators

Com operadores lógicos, podemos definir várias condições dentro de uma. Isso significa que todas as condições que definimos devem corresponder antes que o código correspondente possa ser executado.

- !: negociação lógica NÃO
- &&: AND
- ||: OR

```bash
#!/bin/bash

# Check if the specified file exists and if we have read permissions
if [[ -e "$1" && -r "$1" ]]
then
	echo -e "We can read the file that has been specified."
	exit 0

elif [[ ! -e "$1" ]]
then
	echo -e "The specified file does not exist."
	exit 2

elif [[ -e "$1" && ! -r "$1" ]]
then
	echo -e "We don't have read permission for this file."
	exit 1

else
	echo -e "Error occured."
	exit 5
fi
```

