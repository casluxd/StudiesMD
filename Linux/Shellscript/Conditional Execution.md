
A execução condicional nos permite controlar o fluxo do nosso script alcançando diferentes condições. Esta função é um dos componentes essenciais. Caso contrário, só poderíamos executar um comando após o outro.

Ao definir várias condições, especificamos quais funções ou seções de código devem ser executadas para um valor específico. Se atingirmos uma condição específica, apenas o código dessa condição é executado e os outros são ignorados. Assim que a seção de código for concluída, os seguintes comandos serão executados fora da execução condicional. Vamos olhar para a primeira parte do script novamente e analisá-lo.

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

Em resumo, esta seção de código funciona com os seguintes componentes:

- #!/bin/bash - Shebang.
- if-else-fi - Execução condicional.
- echo - Imprime uma saída específica.
- $# / $0 / $1 - Variáveis ​​especiais.
- domain - Variáveis.

As condições das execuções condicionais podem ser definidas usando variáveis ​​($#, $0, $1, domínio), valores (0) e strings, como veremos nos próximos exemplos. Esses valores são comparados com os operadores de comparação (-eq) que veremos na próxima seção.

# Shebang

A linha shebang está sempre no topo de cada script e sempre começa com "#!". Esta linha contém o caminho para o interpretador especificado (/bin/bash) com o qual o script é executado. Também podemos usar o Shebang para definir outros interpretadores como Python, Perl e outros.

```python
#!/usr/bin/env python
```

```perl
#!/usr/bin/env perl
```

# if-else-fi

Uma das tarefas de programação mais fundamentais é verificar diferentes condições para lidar com elas. A verificação de condições geralmente tem duas formas diferentes em linguagens de programação e script, a condição if-else e as instruções case. No pseudocódigo, a condição if significa o seguinte:

```bash
if [ the number of given arguments equals 0 ]
then
	Print: "You need to specify the target domain."
	Print: "<empty line>"
	Print: "Usage:"
	Print: "   <name of the script> <domain>"
	Exit the script with an error
else
	The "domain" variable serves as the alias for the given argument 
finish the if-condition
```

Por padrão, uma condição If-Else pode conter apenas um único "If", conforme mostrado no próximo exemplo.

```bash
#!/bin/bash

value=$1

if [ $value -gt "10" ]
then
        echo "Given argument is greater than 10."
fi
```

```shell-session
casluxd@htb[/htb]$ bash if-only.sh 5
```

```shell-session
casluxd@htb[/htb]$ bash if-only.sh 12

Given argument is greater than 10.
```

Ao adicionar Elif ou Else, adicionamos alternativas para tratar valores ou status específicos. Se um determinado valor não se aplica ao primeiro caso, ele será capturado por outros.

```bash
#!/bin/bash

value=$1

if [ $value -gt "10" ]
then
	echo "Given argument is greater than 10."
elif [ $value -lt "10" ]
then
	echo "Given argument is less than 10."
else
	echo "Given argument is not a number."
fi
```

```shell-session
casluxd@htb[/htb]$ bash if-elif-else.sh 5

Given argument is less than 10.
```

```shell-session
casluxd@htb[/htb]$ bash if-elif-else.sh 12

Given argument is greater than 10.
```

```shell-session
casluxd@htb[/htb]$ bash if-elif-else.sh HTB

if-elif-else.sh: line 5: [: HTB: integer expression expected
if-elif-else.sh: line 8: [: HTB: integer expression expected
Given argument is not a number.
```

Poderíamos estender nosso script e especificar várias condições. Isso pode parecer algo assim:

```bash
#!/bin/bash

# Check for given argument
if [ $# -eq 0 ]
then
	echo -e "You need to specify the target domain.\n"
	echo -e "Usage:"
	echo -e "\t$0 <domain>"
	exit 1
elif [ $# -eq 1 ]
then
	domain=$1
else
	echo -e "Too many arguments given."
	exit 1
fi

<SNIP>
```
