
## Procurando com CMD

#### Usando where

```cmd-session
C:\Users\student\Desktop>where calc.exe

C:\Windows\System32\calc.exe

C:\Users\student\Desktop>where bio.txt

INFO: Could not find files for the given pattern(s).
```

Acima, podemos ver duas tentativas diferentes usando o comando where. Primeiro, procuramos calc.exe e ele nos mostrou a caminho para calc.exe. Este comando funcionou porque a pasta system32 está em nosso environment variable path, então o comando where pode procurar por estas pastas automaticamente.

A segunda tentativa falhou. Isso aconteceu porque estamos procurando um arquivo que não existe nesse caminho de ambiente. Ele está localizado dentro de nosso diretório de usuários. Nesse caso, nós precisamos especificar o caminho para buscar e para garantir que vasculhamos todos os diretórios desse caminho, podemos usar a opção /R.

```cmd-session
C:\Users\student\Desktop>where /R C:\Users\student\ bio.txt

C:\Users\student\Downloads\bio.txt
```

Acima, pesquisamos recursivamente, procurando por bio.txt. O arquivo foi encontrado na pasta `C:\Users\student\Downloads\`. A opção /R forçou o comando where a pesquisar todas as pastas do diretório do usuário student. Além de procurar arquivos, também podemos pesquisar curingas para strings específicas, tipos de arquivos e muito mais. Abaixo está um exemplo de pesquisa do tipo de arquivo csv no diretório do aluno.

```cmd-session
C:\Users\student\Desktop>where /R C:\Users\student\ *.csv

C:\Users\student\AppData\Local\live-hosts.csv
```

Usamos where para nos dar uma ideia de como procurar arquivos e aplicativos no host. 

O **find** é usado para pesquisar strings de texto ou sua ausência em um arquivo ou arquivos. Também pode-se usar find na saída do console ou outro comando. O find é limitado, no entanto, em sua capacidade de utilizar padrões curinga em sua correspondência. O exemplo abaixo nos mostrará uma pesquisa simples com **find** no arquivo not-password.txt

```cmd-session
C:\Users\student\Desktop>where /R C:\Users\student\ *.csv

C:\Users\student\AppData\Local\live-hosts.csv
```

Podemos modificar a maneira de encontrar pesquisas usando vários interruptores. O modificador /V pode alterar nossa pesquisa de uma cláusula correspondente para uma cláusula **Not**. Assim, por exemplo, se usarmos /V com a string de pesquisa, ele nos mostrará qualquer linha que não tenha a string especificada. Também podemos usar a opção /N para exibir números de linha para nós e a exibição /I para ignorar a distinção entre maiúsculas e minúsculas. No exemplo abaixo, usamos todos os modificadores para nos motrar quaisquer linhas que não correspondam à string "Endereço IP" enquanto solicitamos que exiba números de linha e ignore o caso da string.

```cmd-session
C:\Users\student\Desktop>where /R C:\Users\student\ *.csv

C:\Users\student\AppData\Local\live-hosts.csv
```

Para pesquisas rápidas, find é fácil de usar, mas poderia ser mais robusto na forma como pesquisar. No entanto, se precisarmos de algo mais específico, findstr é o que precisamos. O comando findstr é semelhante ao find no sentido de que pesquisa nos arquivos, mas em vez de padrões. Ele procurará qualquer coisa que corresponda a um padrão, valor regex, curingas e muito mais. Pense nisso como find 2.0. Para aqueles familiarizados com Linux, findstr está mais próximo de grep.

### Avaliando e classificando arquivos

Vimos como trabalhar, pesquisar determinados arquivos e pesquisar strings dentro de arquivos. Além disso, também aprendemos como criar e modificar arquivos. Agora vamos discutir algumas opções para avaliar esses arquivos e compará-los entre si. Os comandos comp, fc e sort são como faremos isso.

O **comp** verificará cada byte em dois arquivos procurando diferenças e exibirá onde elas começam. Por padrão, as diferenças são mostradas em formato decimal. Podemos usar o modificador /A se quisermos ver a diferenças no formato ASCII. O modificador /L também pode nos fornecer os números de linha.

```cmd-session
C:\Users\student\Desktop> comp .\file-1.md .\file-2.md

Comparing .\file-1.md and .\file-2.md...
Files compare OK  
```

Acima, vemos a comparação voltar OK. Os arquivos são os mesmos. Podemos usar isso como uma maneira fácil de verificar se algum script, executável ou arquivo crítico foi modificado. Abaixo temos a saída de um arquivo que foi alterado:

```powershell-session
PS C:\htb> echo a > .\file-1.md
PS C:\Users\MTanaka\Desktop> echo a > .\file-2.md
PS C:\Users\MTanaka\Desktop> comp .\file-1.md .\file-2.md /A
Comparing .\file-1.md and .\file-2.md...
Files compare OK
<SNIP>
PS C:\Users\MTanaka\Desktop> echo b > .\file-2.md
PS C:\Users\MTanaka\Desktop> comp .\file-1.md .\file-2.md /A
Comparing .\file-1.md and .\file-2.md...
Compare error at OFFSET 2
file1 = a
file2 = b  
```

Usamos echo para garantir que as strings fossem diferentes e, em seguida, reexecutamos a operação. Observe como nossa saída mudou e, usando o modificador /A, estamos vendo a diferença de caracteres entre os dois arquivos.

Agora vamos olhar para o **fc** por um minuto. **fc** diferere porque mostra quais linhas são diferentes, não apenas um caractere individual (/A) ou byte que é diferente em cada linha. O **fc** tem muito mais opções do que o **comp** tem, portanto, verifique a saída de ajuda para garantir que você a está usando a maneira que deseja.

```cmd-session
C:\htb> fc.exe /?

Compares two files or sets of files and displays the differences between
them

FC [/A] [/C] [/L] [/LBn] [/N] [/OFF[LINE]] [/T] [/U] [/W] [/nnnn]
   [drive1:][path1]filename1 [drive2:][path2]filename2
FC /B [drive1:][path1]filename1 [drive2:][path2]filename2

  /A         Displays only first and last lines for each set of differences.
  /B         Performs a binary comparison.
  /C         Disregards the case of letters.
  /L         Compares files as ASCII text.
  /LBn       Sets the maximum consecutive mismatches to the specified
             number of lines.
  /N         Displays the line numbers on an ASCII comparison.
  /OFF[LINE] Do not skip files with offline attribute set.
  /T         Does not expand tabs to spaces.
  /U         Compare files as UNICODE text files.
  /W         Compresses white space (tabs and spaces) for comparison.
  /nnnn      Specifies the number of consecutive lines that must match
             after a mismatch.
  [drive1:][path1]filename1
             Specifies the first file or set of files to compare.
  [drive2:][path2]filename2
             Specifies the second file or set of files to compare.
```

Quando o **fc** realiza sua inspeção, ele diferencia maiúsculas e minúsculas e se preocupa mais do que apenas uma comparação byte a byte. Abaixo usaremos alguns arquivos com muito mais caracteres e strings para testar sua funcionalidade. Faremos uma verificação básica e imprimiremos os números de linha e a comparação ASCII usando o modificador /N.

```cmd-session
C:\Users\student\Desktop> fc passwords.txt modded.txt /N

Comparing files passwords.txt and MODDED.TXT
***** passwords.txt
    1:  123456
    2:  password
***** MODDED.TXT
    1:  123456
    2:
    3:  password
*****

***** passwords.txt
    5:  12345
    6:  qwerty
***** MODDED.TXT
    6:  12345
    7:  Just something extra to show functionality. Did it see the space inserted above?
    8:  qwerty
*****
```

A saída do **fc** é muito mais fácil de interpretar e nos dá um pouco mais de clareza sobre as diferenças entre os arquivos. Ao comparar arquivos como arquivos de texto, planilhas ou listas, é prudente classificá-los primeiro para garantir que os dados em cada string sejam os mesmos. Caso contrário, cada linha será diferente e nossa comparação não ajudará. Vejamos **sort** agora para nos ajudar com isso. Com **sort** podemos receber a entrada do console, pipeline ou um arquivo, classificá-lo e enviar os resultados para o console ou para um arquivo ou outro comando. É relativamente simples de usar e geralmente será usado em conjunto com operadores de pipeline, como |, < ou >. 

```cmd-session
C:\Users\student\Desktop> type .\file-1.md
a
b
d
h
w
a
q
h
g

C:\Users\MTanaka\Desktop> sort.exe .\file-1.md /O .\sort-1.md
C:\Users\MTanaka\Desktop> type .\sort-1.md

a
a
b
d
g
h
h
q
w
```

Acima, podemos ver usando **sort** no arquivo file-1.md e depois enviando o resultado com o modificador /O para o arquivo sort-1.md, pegamos nossa lista de letras, as classificamos em ordem alfabética e as escrevemos no novo arquivo. Pode ficar mais complexto ao trabalhar com conjuntos de dados maiores, mas o uso básico é o mesmo. Se quiséssemos classificar apenas para retornar entradas exclusivas, também poderíamos usar o modificador **unique**. Observe as duas primeiras entradas no arquivo sort-1.md. Vamos tentar usar o **unique** e ver o que acontece.

```cmd-session
C:\htb> type .\sort-1.md

a
a
b
d
g
h
h
q
w

PS C:\Users\MTanaka\Desktop> sort.exe .\sort-1.md /unique

a
b
d
g
h
q
w  
```

Observe como temos menos resultados gerais agora. Isso ocorre porque sort não gravou entradas duplicadas do arquivo no console.