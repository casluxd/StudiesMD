
Por incrível que pareça o PowerShell, ele é tão bom quanto o usamos. Grande parte da linguagem e da funcionalidade do PowerShell pode ser utilizada de maneira automatizada. Ter a capacidade de criar scripts e módulos para nós no PowerShell (não importa quão simples ou complexos) pode aliviar nossa carga administrativa ou eliminar algumas tarefas fáceis de nosso prato como pentesters. Este módulo discutirá as peças e partes que compõem um script e módulo do PowerShell. Ao final, teremos criado nosso próprio módulo personalizável e fácil de usar.

## Compreendendo os scripts do PowerShell

O PowerShell, por sua natureza, é modular e permite uma quantidade significativa de controle com seu uso. O pensamento tradicional ao lidar com scripts é que estamos escrevendo algum tipo de executável que executa tarefas para nós na linguagem em que foi criado. Com o PowerShell, isso ainda é verdade, com a exceção de que ele pode lidar com entrada de vários idiomas e tipos de arquivo diferentes e pode lidar com muitos tipos de objetos diferentes. Podemos utilizar scripts singulares da maneira usual chamando-os utilizando a sintaxe `.\script` e importando módulos usando o cmdlet Import-Module. Agora vamos falar um pouco sobre scripts e módulos.

### Scripts vs Modules

A maneira mais fácil de pensar nisso é que um script é um arquivo de texto executável contendo cmdlets e funções do PowerShell, enquanto um módulo pode ser apenas um script simples ou uma coleção de vários arquivos de script, manifestos e funções agrupadas. A outra diferença principal está em seu uso. Normalmente, você chamaria um script executando-o diretamente, enquanto você pode importar um módulo e todos os scripts e funções associados para chamá-los quando quiser. Para o bem desta seção, vamos discuti-los usando o mesmo termo, e tudo o que falamos em um arquivo de módulo funciona em um script padrão do PowerShell. Primeiro, as extensões de arquivo e o que elas significam para nós.

## File Extensions

Para nos familiarizarmos com algumas extensões de arquivos que encontraremos ao trabalhar com scripts e módulos do PowerShell, montamos uma pequena tabela com as extensões e suas descrições.

- ps1: A extensão de arquivo `*.ps1` representa scripts executáveis ​​do PowerShell.
- psm1: A extensão de arquivo `*.psm1` representa um arquivo de módulo do PowerShell. Ele define o que é o módulo e o que está contido nele.
- psd1: O `*.psd1` é um arquivo de dados do PowerShell que detalha o conteúdo de um módulo do PowerShell em uma tabela de pares chave/valor.

Estas são as principais extensões com as quais estamos preocupados agora. Na realidade, os módulos do PowerShell podem ter muitos arquivos de acompanhamento diferentes com várias extensões, mas não são requisitos para o que estamos tentando fazer. Se você deseja um mergulho mais profundo nos arquivos de script do PowerShell e nos arquivos de ajuda, confira esta postagem(https://learn.microsoft.com/en-us/powershell/scripting/developer/module/writing-a-windows-powershell-module?view=powershell-7.2).

## Criando um módulo

Então vamos ao que interessa. A partir deste ponto, abordaremos os componentes de um módulo do PowerShell, o que eles contêm e como criá-los. Este processo é simples. Basta um pouco de planejamento prévio. Considere este cenário:

`Cenário: Nós nos encontramos realizando as mesmas verificações repetidas vezes ao administrar hosts. Portanto, para agilizar nossas tarefas, criaremos um módulo do PowerShell para executar as verificações para nós e, em seguida, fornecer as informações solicitadas. Nosso módulo, quando usado, deve gerar o nome do computador do host, o endereço IP e as informações básicas do domínio e nos fornecer a saída do diretório C:\Users\ para que possamos ver quais usuários efetuaram login interativamente nesse host.`

Agora que sabemos o que está acontecendo em nosso módulo, é hora de começar a construí-lo.

#### Componentes de um módulo

1. Um diretório contendo todos os arquivos e conteúdos necessários, salvos em algum lugar dentro de $env:PSModulePath.
   - Isso é feito para que, quando você tentar importá-lo para sua sessão ou perfil do PowerShell, ele possa ser encontrado automaticamente em vez de precisar especificar onde está.
2. Um arquivo de manifesto listando todos os arquivos e informações pertinentes sobre o módulo e sua função.
   - Isso pode incluir scripts associados, dependências, autor, exemplo de uso, etc.
3. Algum arquivo de código - geralmente um script do PowerShell (.ps1) ou um arquivo de módulo (.psm1) que contém nossas funções de script e outras informações.
4. Outros recursos de que o módulo precisa, como arquivos de ajuda, scripts e outros documentos de suporte.

Essa configuração é uma prática padrão, mas não é estritamente necessária. Poderíamos ter nosso módulo como apenas um arquivo `*.psm1` que contém nossos scripts e contexto, ignorando o manifesto e outros arquivos auxiliares. O PowerShell seria capaz de interpretar e entender o que fazer em qualquer instância. Por uma questão de propriedade, trabalharemos na criação de um módulo padrão do PowerShell, incluindo o arquivo de manifesto e algumas funcionalidades de ajuda integradas.

### Fazendo um diretório para armazenar nosso módulo

Criar um diretório é super simples, conforme discutido nas seções anteriores. Antes de prosseguirmos, precisamos criar o diretório para armazenar nosso módulo. Esse diretório deve estar em um dos caminhos em $env:PSModulePath. Se não tiver certeza de quais são esses caminhos, você pode chamar a variável para ver onde seria o melhor lugar. Então, vamos criar uma pasta chamada quick-recon.

```powershell-session
PS C:\htb> mkdir quick-recon  

    Directory: C:\Users\MTanaka\Documents\WindowsPowerShell\Modules


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        10/31/2022   7:38 AM                quick-recon
```

Agora que temos nosso diretório, podemos criar o módulo. Vamos discutir um arquivo de manifesto do módulo por um segundo.

### Module Manifest

Um manifesto de módulo é um arquivo .psd1 simples que contém uma tabela de hash. As chaves e valores na tabela de hash executam as seguintes funções:

- Descreva o conteúdo e os atributos do módulo.
- Defina os pré-requisitos. (módulos específicos de fora do próprio módulo, variáveis, funções, etc.)
- Determine como os componentes são processados.

Se você adicionar um arquivo de manifesto à pasta do módulo, poderá fazer referência a vários arquivos como uma única unidade referenciando o manifesto. O manifesto descreve as seguintes informações:

- Metadados sobre o módulo, como o número da versão do módulo, o autor e a descrição.
- Pré-requisitos necessários para importar o módulo, como a versão do Windows PowerShell, a versão CLR (common language runtime) e os módulos necessários.
- Diretivas de processamento, como scripts, formatos e tipos a serem processados.
- Restrições nos membros do módulo a serem exportados, como aliases, funções, variáveis ​​e cmdlets a serem exportados.

Podemos criar rapidamente um arquivo de manifesto utilizando New-ModuleManifest e especificando onde queremos colocá-lo.

```powershell
PS C:\htb> New-ModuleManifest -Path C:\Users\MTanaka\Documents\WindowsPowerShell\Modules\quick-recon\quick-recon.psd1 -PassThru

# Module manifest for module 'quick-recon'
#
# Generated by: MTanaka
#
# Generated on: 10/31/2022
#

@{

# Script module or binary module file associated with this manifest.
# RootModule = ''

# Version number of this module.
ModuleVersion = '1.0'

<SNIP>
```

Ao emitir o comando acima, provisionamos um novo arquivo de manifesto preenchido com as considerações padrão. O modificador -PassThru nos permite ver o que está sendo impresso no arquivo e no console. Agora podemos entrar e preencher as seções que queremos com as informações relevantes. Lembre-se de que todas as linhas nos arquivos de manifesto são opcionais, exceto a linha ModuleVersion. A edição do manifesto será mais fácil a partir de uma GUI onde você pode utilizar um editor de texto ou IDE como o VSCode. Se fôssemos completar nosso arquivo de manifesto agora para este módulo, seria algo como isto:

```powershell
# Module manifest for module 'quick-recon'
#
# Generated by: MTanaka
#
# Generated on: 10/31/2022
#

@{

# Script module or binary module file associated with this manifest.
# RootModule = 'C:\Users\MTanaka\WindowsPowerShell\Modules\quick-recon\quick-recon.psm1'

# Version number of this module.
ModuleVersion = '1.0'

# ID used to uniquely identify this module
GUID = '0a062bb1-8a1b-4bdb-86ed-5adbe1071d2f'

# Author of this module
Author = 'MTanaka'

# Company or vendor of this module
CompanyName = 'Greenhorn.Corp.'

# Copyright statement for this module
Copyright = '(c) 2022 Greenhorn.Corp. All rights reserved.'

# Description of the functionality provided by this module
Description = 'This module will perform several quick checks against the host for Reconnaissance of key information.'

# Functions to export from this module, for best performance, do not use wildcards and do not delete the entry, use an empty array if there are no functions to export.
FunctionsToExport = @()

# Cmdlets to export from this module, for best performance, do not use wildcards and do not delete the entry, use an empty array if there are no cmdlets to export.
CmdletsToExport = @()

# Variables to export from this module
VariablesToExport = '*'

# Aliases to export from this module, for best performance, do not use wildcards and do not delete the entry, use an empty array if there are no aliases to export.
AliasesToExport = @()

# List of all modules packaged with this module
# ModuleList = @()

# List of all files packaged with this module
# FileList = @()  
}
```

### Crie o seu próprio arquivo de script

Podemos usar o cmdlet New-Item (ni) para criar nosso arquivo.

```powershell
PS C:\htb>  ni quick-recon.psm1 -ItemType File


    Directory: C:\Users\MTanaka\Documents\WindowsPowerShell\Modules\quick-recon


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        10/31/2022   9:07 AM              0 quick-recon.psm1
```

Fácil o suficiente, certo? Agora, para preencher esta besta.

### Importando módulos que você precisa

Se nosso novo PowerShell exigir outros módulos ou cmdlets dentro deles para operar corretamente, colocaremos uma string Import-Module no início de nosso arquivo de script. O uso de Import-Module dessa maneira funciona como se o emitissemos de dentro do shell; ele chama e carrega os módulos que precisamos antes de executar nosso script. Para atingir as metas que temos para este módulo, muitos dos cmdlets e funções já estão integrados ao PowerShell. No entanto, precisamos de um do módulo ActiveDirectory PowerShell. Portanto, vamos adicionar uma linha de importação para o módulo ActiveDirectory.

```powershell
Import-Module ActiveDirectory 
```

Bem simples né? Agora temos nosso arquivo de script de módulo quick-recon.psm1 e adicionamos uma instrução de módulo de importação nele. Agora podemos chegar ao cerne do arquivo, nossas funções.

#### Funções e trabalho com Powershell

Precisamos fazer quatro coisas principais com este módulo:

- Recupere o host ComputerName
- Recupere a configuração IP dos hosts
- Recupere informações básicas do domínio
- Recupere uma saída do diretório `"C:\Users"`

Para começar, vamos nos concentrar na saída ComputerName. Podemos obter isso de várias maneiras com vários cmdlets, módulos e comandos DOS. Nosso script utilizará a variável de ambiente ($env:ComputerName) para adquirir o nome do host para a saída. Para tornar nossa saída mais fácil de ler posteriormente, usaremos outra variável chamada $hostname para armazenar a saída da variável de ambiente. Para capturar o endereço IP dos adaptadores de host ativos, usaremos o IPConfig e armazenaremos essas informações na variável $IP. Para informações de domínio básicas, usaremos Get-ADDomain e armazenaremos a saída em $Domain.  Por fim, vamos obter uma listagem das pastas de usuários em C:\Users\ com Get-ChildItem e armazená-la em $Users. 

```powershell
Import-Module ActiveDirectory 

$Hostname = $env:ComputerName
$IP = ipconfig 
$Domain = Get-ADDomain  
$Users = Get-ChildItem C:\Users\ 
```

Nossas variáveis ​​agora estão definidas para executar comandos ou funções singulares, capturando a saída necessária. Agora vamos formatar esses dados e obter uma boa saída. Podemos fazer isso escrevendo o resultado em um arquivo usando New-Item e Add-Content. Para facilitar as coisas, transformaremos esse processo de saída em uma função chamável chamada Get-Recon.

```powershell
Import-Module ActiveDirectory

function Get-Recon {  

    $Hostname = $env:ComputerName  

    $IP = ipconfig

    $Domain = Get-ADDomain 

    $Users = Get-ChildItem C:\Users\

    new-Item ~\Desktop\recon.txt -ItemType File 

    $Vars = "***---Hostname info---***", $Hostname, "***---Domain Info---***", $Domain, "***---IP INFO---***",  $IP, "***---USERS---***", $Users

    Add-Content ~\Desktop\recon.txt $Vars
  } 
```

New-Item cria nosso arquivo de saída para nós primeiro, então observe como utilizamos mais uma variável ($Vars) para formatar nossa saída. Chamamos cada variável e inserimos uma linha descritiva entre cada uma. Por fim, o cmdlet Add-Content anexa os dados que coletamos em um arquivo chamado recon.txt gravando os resultados de $Vars. Nossa função está se moldando agora. Em seguida, precisamos adicionar alguns comentários ao nosso arquivo para que outros possam entender o que estamos tentando realizar e por que fizemos da maneira que fizemos.

### Comentários dentro do Script

O (#) informará ao PowerShell que a linha contém um comentário em seu script ou arquivo de módulo. Se seus comentários abrangerem várias linhas, você pode usar <# e #> para agrupar várias linhas como um comentário grande, como visto abaixo:

```powershell
# This is a single-line comment.  

<# This line and the following lines are all wrapped in the Comment specifier. 
Nothing with this window will be ready by the script as part of a function.
This text exists purely for the creator and us to convey pertinent information.

#>  
```


```powershell
Import-Module ActiveDirectory

function Get-Recon {  
    # Collect the hostname of our PC.
    $Hostname = $env:ComputerName  
    # Collect the IP configuration.
    $IP = ipconfig
    # Collect basic domain information.
    $Domain = Get-ADDomain 
    # Output the users who have logged in and built out a basic directory structure in "C:\Users\".
    $Users = Get-ChildItem C:\Users\
    # Create a new file to place our recon results in.
    new-Item ~\Desktop\recon.txt -ItemType File 
    # A variable to hold the results of our other variables. 
    $Vars = "***---Hostname info---***", $Hostname, "***---Domain Info---***", $Domain, "***---IP INFO---***",  $IP, "***---USERS---***", $Users
    # It does the thing 
    Add-Content ~\Desktop\recon.txt $Vars
  } 
```

É simples assim. Nada muito louco com comentários. Agora precisamos incluir um pouco de sintaxe de ajuda para que outros possam entender como usar nosso módulo.

#### Incluindo Help

O PowerShell utiliza uma forma de ajuda baseada em comentários para incorporar tudo o que você precisa para o script ou módulo. Podemos utilizar blocos de comentários como os que discutimos acima, juntamente com palavras-chave reconhecidas para criar a seção de ajuda e até mesmo chamá-la usando Get-Help posteriormente. Quando se trata de posicionamento, temos duas opções aqui. Podemos colocar a ajuda dentro da própria função ou fora da função no script. Se quisermos colocá-lo dentro da função, ele deve estar no início da função, logo após a linha de abertura da função, ou no final da função, uma linha após a última ação da função. Se o colocarmos dentro do script, mas fora da função em si, devemos colocá-lo acima da nossa função com não mais do que uma linha entre a ajuda e a função. Para saber mais sobre a ajuda no PowerShell, confira este artigo(https://learn.microsoft.com/en-us/powershell/scripting/developer/help/writing-help-for-windows-powershell-scripts-and-functions?view=powershell-7.2). Agora vamos definir nossa seção de ajuda. Vamos colocá-lo fora da função na parte superior do script por enquanto.

```powershell
Import-Module ActiveDirectory

<# 
.Description  
This function performs some simple recon tasks for the user. We import the module and issue the 'Get-Recon' command to retrieve our output. Each variable and line within the function and script are commented for our understanding. Right now, this module will only work on the local host from which you run it, and the output will be sent to a file named 'recon.txt' on the Desktop of the user who opened the shell. Remote Recon functions are coming soon!  

.Example  
After importing the module run "Get-Recon"
'Get-Recon


    Directory: C:\Users\MTanaka\Desktop


Mode                 LastWriteTime         Length Name                                                                                                                                        
----                 -------------         ------ ----                                                                                                                                        
-a----         11/3/2022  12:46 PM              0 recon.txt '

.Notes  
Remote Recon functions coming soon! This script serves as our initial introduction to writing functions and scripts and making PowerShell modules.  

#>

function Get-Recon {  
<SNIP>  
```

Observe nosso uso de palavras-chave. Para especificar uma palavra-chave no bloco de comentários, usamos a sintaxe .`<palavra-chave>` e, em seguida, colocamos o texto de sabor abaixo. Especificamos apenas Description, Example e Notes, mas várias outras palavras-chave podem ser colocadas no bloco de ajuda. Para ver todas as palavras-chave disponíveis, consulte este artigo sobre palavras-chave de ajuda baseadas em comentários. Nossa última parte a discutir antes de agrupar tudo em nosso belo arquivo de módulo do PowerShell é as funções de exporting e protecting.

## Funções de proteção

Podemos adicionar funções aos nossos scripts que não queremos que sejam acessadas, exportadas ou utilizadas por outros scripts ou processos no PowerShell. Para impedir que uma função seja exportada ou para defini-la explicitamente para exportação, o Export-ModuleMember é o cmdlet para o trabalho. O conteúdo é exportável se o deixarmos fora de nossos módulos de script. Se colocá-lo no arquivo, mas deixá-lo em branco assim:

```powershell
Export-ModuleMember  
```

Ele garante que as variáveis, aliases e funções do módulo não possam ser exportadas. Se quisermos especificar o que exportar, podemos adicioná-los à string de comando da seguinte forma:

```powershell
Export-ModuleMember -Function Get-Recon -Variable Hostname 
```

Alternativamente, se você quiser apenas exportar todas as funções e uma variável específica, por exemplo, você pode emitir * após -Function e então especificar as Variáveis ​​para exportar explicitamente. Portanto, vamos adicionar o cmdlet Export-ModuleMember ao nosso script e especificar que queremos permitir que nossa função Get-Recon e nossa variável Hostname estejam disponíveis para exportação.

```powershell
<SNIP>  
function Get-Recon {  
    # Collect the hostname of our PC
    $Hostname = $env:ComputerName  
    # Collect the IP configuration
    $IP = ipconfig
    # Collect basic domain information
    $Domain = Get-ADDomain 
    # Output the users who have logged in and built out a basic directory structure in "C:\Users"
    $Users = Get-ChildItem C:\Users\
    # Create a new file to place our recon results in
    new-Item ~\Desktop\recon.txt -ItemType File 
    # A variable to hold the results of our other variables 
    $Vars = "***---Hostname info---***", $Hostname, "***---Domain Info---***", $Domain, "***---IP INFO---***",  $IP, "***---USERS---***", $Users
    # It does the thing 
    Add-Content ~\Desktop\recon.txt $Vars
  } 

Export-ModuleMember -Function Get-Recon -Variable Hostname  
```

## Escopo

Ao lidar com scripts, a sessão do PowerShell e como as coisas são reconhecidas na linha de comando, o conceito de escopo entra em jogo. O escopo, em essência, é como o PowerShell reconhece e protege os objetos dentro da sessão contra acesso ou modificação não autorizados. Atualmente, o PowerShell usa três níveis de escopo diferentes:
- Global: Este é o nível de escopo padrão do PowerShell. Afeta todos os objetos existentes quando o PowerShell é iniciado ou uma nova sessão é aberta. Quaisquer variáveis, aliases, funções e qualquer coisa que você especificar em seu perfil do PowerShell serão criados no escopo Global.
- Local: Este é o escopo atual em que você está operando. Pode ser qualquer um dos escopos padrão ou escopos filhos criados.
- Script: Este é um escopo temporário que se aplica a qualquer script sendo executado. Aplica-se apenas ao script e seu conteúdo. Outros scripts e qualquer coisa fora dele não saberão que ele existe. Para o script, seu escopo é o escopo local.

Isso é importante para nós se não quisermos nada fora do escopo em que estamos executando o script para acessar seu conteúdo. Além disso, podemos ter escopos filhos criados dentro dos escopos principais. Por exemplo, quando você executa um script, o escopo do script é instanciado e qualquer função chamada também pode gerar um escopo filho em torno dessa função e suas variáveis ​​incluídas. Se quiséssemos garantir que o conteúdo dessa função específica não fosse acessível ao restante do script ou à própria sessão do PowerShell, poderíamos modificar seu escopo. Este é um tópico complexo e algo acima do nível deste módulo atualmente, mas achamos que vale a pena mencionar. Para saber mais sobre o escopo no PowerShell, confira a documentação aqui.

#### Juntando tudo

```powershell
import-module ActiveDirectory

<# 
.Description  
This function performs some simple recon tasks for the user. We import the module and then issue the 'Get-Recon' command to retrieve our output. Each variable and line within the function and script are commented for your understanding. Right now, this only works on the local host from which you run it, and the output will be sent to a file named 'recon.txt' on the Desktop of the user who opened the shell. Remote Recon functions coming soon!  

.Example  
After importing the module run "Get-Recon"
'Get-Recon


    Directory: C:\Users\MTanaka\Desktop


Mode                 LastWriteTime         Length Name                                                                                                                                        
----                 -------------         ------ ----                                                                                                                                        
-a----         11/3/2022  12:46 PM              0 recon.txt '

.Notes  
Remote Recon functions coming soon! This script serves as our initial introduction to writing functions and scripts and making PowerShell modules.  

#>
function Get-Recon {  
    # Collect the hostname of our PC
    $Hostname = $env:ComputerName  
    # Collect the IP configuration
    $IP = ipconfig
    # Collect basic domain information
    $Domain = Get-ADDomain 
    # Output the users who have logged in and built out a basic directory structure in "C:\Users"
    $Users = Get-ChildItem C:\Users\
    # Create a new file to place our recon results in
    new-Item ~\Desktop\recon.txt -ItemType File 
    # A variable to hold the results of our other variables 
    $Vars = "***---Hostname info---***", $Hostname, "***---Domain Info---***", $Domain, "***---IP INFO---***",  $IP, "***---USERS---***", $Users
    # It does the thing 
    Add-Content ~\Desktop\recon.txt $Vars
  } 

Export-ModuleMember -Function Get-Recon -Variable Hostname 
```

