
Nesta seção, experimentaremos criar, modificar e excluir arquivos e diretórios, juntamente com uma rápida introdução às permissões de arquivo e como enumerá-las. Até agora, devemos estar familiarizados com os verbos Get, Set, New, entre outros, então vamos agilizar isso com nossos exemplos combinando vários comandos em uma única sessão de shell.

# Criando/Movendo/Excluindo arquivos e diretórios

Muitos dos cmdlets que discutiremos nesta seção podem ser aplicados ao trabalho com arquivos e pastas, portanto, combinaremos algumas de nossas ações para trabalhar com mais eficiência (como qualquer bom pentester ou sysadmin deve se esforçar). A tabela abaixo lista os cmdlets comumente usados ao lidar com objetos no PowerShell.

## Comandos comuns usados para gerenciamento de arquivos e pastas

- Get-Item(alias -> gi): Recuperar um objeto (pode ser um arquivo, pasta, objeto de registro, etc.)
- Get-ChildItem (alias -> ls / dir / gci): Lista o conteúdo de uma pasta ou seção de registro.
- New-Item (alias -> md / mkdir / ni): Cria novos objetos (podem ser arquivos, pastas, symlinks, entradas de registro e mais).
- Set-Item (alias -> si): Modifica os valores de propriedade de um objeto.
- Copy-Item (alias -> copy / cp / ci): Faz uma duplicidade do item.
- Rename-Item (alias -> ren / rni): Altera o nome do objeto.
- Remove-Item (alias -> rm / del / rmdir): Deleta o objeto
- Get-Content (alias -> cat / type): Exibe o conteúdo dentro de um arquivo ou objeto.
- Add-Content (alias -> ac): Anexa conteúdo a um arquivo.
- Set-Content (alias -> sc): Reescreve qualquer conteúdo de um arquivo com novos dados.
- Clear-Content (alias -> clc): Limpa o conteúdo dos arquivos sem excluir o próprio arquivo.
- Compare-Object (alias -> diff / compare): Compare dois ou mais objetos entre si. Isso inclui o próprio objeto e o conteúdo dentro dele.

Cenário: O novo chefe de segurança de Greenhorn, Sr. Tanaka, solicitou que um conjunto de arquivos e pastas fosse criado para ele. Ele planeja usá-los para documentação de SOP para a equipe de segurança. Como ele acabou de obter acesso ao host, concordamos em definir a estrutura de arquivos e pastas para ele. Se você quiser seguir os exemplos abaixo, fique à vontade. Para sua prática, removemos as pastas e arquivos discutidos abaixo para que você possa recriá-los.

Primeiro, vamos começar com a estrutura de pastas que ele solicitou. Vamos criar três pastas chamadas:

- SOPs
  - Physical Sec
  - Cyber Sec
  - Training

Nós iremos usar os comandos Get-Item, Get-ChildItem e New-Item para criar nossa estrutura de pastas. Primeiro precisamos determinar onde estamos no host e, em seguida, passar para a pasta Documents do Mr. Tanaka.

```powershell-session
PS C:\htb> Get-Location

Path
----
C:\Users\MTanaka

PS C:\Users\MTanaka> cd Documents
PS C:\Users\MTanaka\Documents>
```

```powershell-session
PS C:\Users\MTanaka\Documents>  new-item -name "SOPs" -type directory


    Directory: C:\Users\MTanaka\Documents


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         10/5/2022  12:20 PM                SOPs
```

```powershell-session
PS C:\Users\MTanaka\Documents> cd SOPs 

PS C:\Users\MTanaka\Documents\SOPs> mkdir "Physical Sec"

    Directory: C:\Users\MTanaka\Documents\SOPs


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         10/5/2022   4:30 PM                Physical Sec

PS C:\Users\MTanaka\Documents\SOPs> mkdir "Cyber Sec"

    Directory: C:\Users\MTanaka\Documents\SOPs


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         10/5/2022   4:30 PM                Cyber Sec

PS C:\Users\MTanaka\Documents\SOPs> mkdir "Training"

    Directory: C:\Users\MTanaka\Documents\SOPs


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         10/5/2022   4:31 PM                Training  

PS C:\Users\MTanaka\Documents\SOPs> Get-ChildItem 

Directory: C:\Users\MTanaka\Documents\SOPs


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        10/5/2022   9:08 AM                Cyber Sec
d-----        11/5/2022   9:09 AM                Physical Sec
d-----        11/5/2022   9:08 AM                Training
```

Agora que temos nossa estrutura de diretórios no lugar, é hora de começar a preencher os arquivos necessários. O Mr.Tanaka pediu um arquivo Markdown em cada pasta da seguinte forma:

- SOPs > Readme.md
  - Physical Sec -> Physical-Sec-draft.md
  - Cyber Sec -> Cyber-Sec-draft.md
  - Training -> Employee-Tranining-draft.md

Em cada arquivo, ele solicitou este cabeçalho no topo:

-   Title: Insert Document Title Here
-   Date: x/x/202x
-   Author: MTanaka
-   Version: 0.1 (Draft)

Devemos ser capazes de fazer isso rapidamente usando o cmdlet New-Item e o cmdlet Add-Content.

```powershell-session
PS C:\htb> PS C:\Users\MTanaka\Documents\SOPs> new-Item "Readme.md" -ItemType File

    Directory: C:\Users\MTanaka\Documents\SOPs

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/10/2022   9:12 AM              0 Readme.md

PS C:\Users\MTanaka\Documents\SOPs> cd '.\Physical Sec\'
PS C:\Users\MTanaka\Documents\SOPs\Physical Sec> ls
PS C:\Users\MTanaka\Documents\SOPs\Physical Sec> new-Item "Physical-Sec-draft.md" -ItemType File

    Directory: C:\Users\MTanaka\Documents\SOPs\Physical Sec

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/10/2022   9:14 AM              0 Physical-Sec-draft.md

PS C:\Users\MTanaka\Documents\SOPs\Physical Sec> cd ..
PS C:\Users\MTanaka\Documents\SOPs> cd '.\Cyber Sec\'

PS C:\Users\MTanaka\Documents\SOPs\Cyber Sec> new-Item "Cyber-Sec-draft.md" -ItemType File

    Directory: C:\Users\MTanaka\Documents\SOPs\Cyber Sec

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/10/2022   9:14 AM              0 Cyber-Sec-draft.md

PS C:\Users\MTanaka\Documents\SOPs\Cyber Sec> cd ..
PS C:\Users\MTanaka\Documents\SOPs> cd .\Training\
PS C:\Users\MTanaka\Documents\SOPs\Training> ls
PS C:\Users\MTanaka\Documents\SOPs\Training> new-Item "Employee-Training-draft.md" -ItemType File

    Directory: C:\Users\MTanaka\Documents\SOPs\Training

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/10/2022   9:15 AM              0 Employee-Training-draft.md

PS C:\Users\MTanaka\Documents\SOPs\Training> cd ..
PS C:\Users\MTanaka\Documents\SOPs> tree /F
Folder PATH listing
Volume serial number is F684-763E
C:.
│   Readme.md
│
├───Cyber Sec
│       Cyber-Sec-draft.md
│
├───Physical Sec
│       Physical-Sec-draft.md
│
└───Training
        Employee-Training-draft.md
```

Agora que temos nossos arquivos, precisamos adicionar conteúdo dentro deles. Podemos fazer isso com o cmdlet Add-Content.

```powershell-session
PS C:\htb> Add-Content .\Readme.md "Title: Insert Document Title Here
>> Date: x/x/202x
>> Author: MTanaka
>> Version: 0.1 (Draft)"  
  
PS C:\Users\MTanaka\Documents\SOPs> cat .\Readme.md
Title: Insert Document Title Here
Date: x/x/202x
Author: MTanaka
Version: 0.1 (Draft)
```

Em seguida, executaríamos o mesmo processo que fizemos para o Readme.md em todos os outros arquivos que criamos para o Sr. Tanaka. Este cenário parecia um pouco tedioso, certo? Criar arquivos repetidamente à mão pode ser cansativo. É aqui que a automação e o script entram em ação. Está um pouco fora de alcance agora, mas em uma seção posterior deste módulo, discutiremos como criar um módulo PowerShell rápido, usando variáveis ​​e escrevendo scripts para facilitar as coisas.

Cenário Cont.: O Sr. Tanaka nos pediu para alterar o nome do arquivo `Cyber-Sec-draft.md` para `Infosec-SOP-draft.md`.

```powershell-session
PS C:\Users\MTanaka\Documents\SOPs\Cyber Sec> ls

    Directory: C:\Users\MTanaka\Documents\SOPs\Cyber Sec

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/10/2022   9:14 AM              0 Cyber-Sec-draft.md

PS C:\Users\MTanaka\Documents\SOPs\Cyber Sec> Rename-Item .\Cyber-Sec-draft.md -NewName Infosec-SOP-draft.md
PS C:\Users\MTanaka\Documents\SOPs\Cyber Sec> ls

    Directory: C:\Users\MTanaka\Documents\SOPs\Cyber Sec

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/10/2022   9:14 AM              0 Infosec-SOP-draft.md
```

Tudo o que precisávamos fazer acima era emitir o cmdlet Rename-Item, dar a ele o nome de arquivo original que queremos alterar (Cyber-Sec-draft.md) e, em seguida, informar nosso novo nome com o -NewName (Infosec-SOP-draft .md) parâmetro. Parece simples né? Poderíamos levar isso adiante e renomear todos os arquivos em um diretório ou alterar o tipo de arquivo ou várias ações diferentes. Em nosso exemplo abaixo, alteraremos os nomes de todos os arquivos de texto no Mr. Tanakas Desktop de file.txt para file.md.

```powershell-session
PS C:\Users\MTanaka\Desktop> ls

    Directory: C:\Users\MTanaka\Desktop

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        10/13/2022   1:05 PM              0 file-1.txt
-a----        10/13/2022   1:05 PM              0 file-2.txt
-a----        10/13/2022   1:06 PM              0 file-3.txt
-a----        10/13/2022   1:06 PM              0 file-4.txt
-a----        10/13/2022   1:06 PM              0 file-5.txt

PS C:\Users\MTanaka\Desktop> get-childitem -Path *.txt | rename-item -NewName {$_.name -replace ".txt",".md"}
PS C:\Users\MTanaka\Desktop> ls

    Directory: C:\Users\MTanaka\Desktop

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        10/13/2022   1:05 PM              0 file-1.md
-a----        10/13/2022   1:05 PM              0 file-2.md
-a----        10/13/2022   1:06 PM              0 file-3.md
-a----        10/13/2022   1:06 PM              0 file-4.md
-a----        10/13/2022   1:06 PM              0 file-5.md
```

Como podemos ver acima, tínhamos cinco arquivos de texto na área de trabalho. Nós os alteramos para arquivos .md usando `get-childitem -Path *.txt` para selecionar os objetos e usamos | para enviar esses objetos para o cmdlet `rename-item -NewName {$_.name -replace ".txt",".md"}` que renomeia tudo de seu nome original `($_.name)` e substitui o .txt de nome para .md. Essa é uma maneira muito mais rápida de interagir com arquivos e executar ações em massa. Agora que concluímos todas as solicitações do Sr. Tanakas, vamos discutir as permissões de arquivos e diretórios por um segundo.

# O que são permissões de arquivos e diretórios?

As permissões, simplificadas, são a maneira de nosso host determinar quem tem acesso a um objeto específico e o que eles podem fazer com ele. Essas permissões nos permitem aplicar controle de segurança granular sobre nossos objetos para manter uma postura de segurança adequada. Em ambientes como grandes organizações com vários departamentos (como RH, TI, Vendas, etc.), querem garantir que eles mantenham no acesso às informações com base no "necessidade de saber". Isso garante que um estranho não possa corromper ou fazer uso indevido dos dados. O sistema de arquivos do Windows tem muitas permissões básicas e avançadas. Alguns dos principais tipos de permissão são:

- Full Control: O controle total permite que o usuário ou grupo especificado interaja com o arquivo como bem entender. Isso inclui tudo abaixo, alterando as permissões e assumindo a propriedade do arquivo.
- Modify: permite ler, escrever e excluir arquivos e pastas.
- List Folder Contents: Isso torna possível visualizar e listar pastas e subpastas junto com a execução de arquivos. Isso se aplica apenas a pastas.
- Read and Execute: Permite que os usuários visualizem o conteúdo dos arquivos e executem executáveis ​​(.ps1, .exe, .bat, etc.)
- Write: a gravação permite ao usuário criar novos arquivos e subpastas, além de adicionar conteúdo aos arquivos.
- Read: permite visualizar e listar pastas e subpastas e visualizar o conteúdo de um arquivo.
- Traverse Folder: Traverse nos permite dar ao usuário a capacidade de acessar arquivos ou subpastas dentro de uma árvore, mas não ter acesso ao conteúdo da pasta de nível superior. Esta é uma maneira de fornecer acesso seletivo de uma perspectiva de segurança.

O Windows (NTFS, em geral) nos permite definir permissões em um diretório pai e fazer com que essas permissões preencham cada arquivo e pasta localizados nesse diretório. Isso nos economiza muito tempo em comparação com a configuração manual das permissões em cada objeto contido nele. Essa herança pode ser desativada conforme necessário para arquivos, pastas e subpastas específicos.