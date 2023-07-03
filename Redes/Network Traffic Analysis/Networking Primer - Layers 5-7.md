
Vimos como funciona a rede de nível inferior, agora veremos alguns dos protocolos da camada superior que lidam com nossos aplicativos. São necessários muitos aplicativos e serviços diferentes para manter uma conexão de rede e garantir que os dados possam ser transferidos entre os hosts. Esta seção irá delinear apenas alguns vitais.

# HTTP

O protocolo de transferência de hipertexto (HTTP) é um protocolo de camada de aplicativo sem estado que está em uso desde 1990. O HTTP permite a transferência de dados em texto não criptografado entre um cliente e um servidor por TCP. O cliente enviaria uma solicitação HTTP ao servidor, solicitando um recurso. Uma sessão é estabelecida e o servidor responde com a mídia solicitada (HTML, imagens, hiperlinks, vídeo). O HTTP utiliza as portas 80 ou 8000 sobre TCP durante as operações normais. Em circunstâncias excepcionais, pode ser modificado para usar portas alternativas ou, às vezes, UDP.

### HTTP Methods

Para executar operações como buscar páginas da web, solicitar itens para download ou postar seu tweet mais recente, tudo requer o uso de métodos específicos. Esses métodos definem as ações tomadas ao solicitar um URI. Métodos:

|**Method**|**Description**|
|---|---|
|`HEAD`|`required` is a safe method that requests a response from the server similar to a Get request except that the message body is not included. It is a great way to acquire more information about the server and its operational status.|
|`GET`|`required` Get is the most common method used. It requests information and content from the server. For example, `GET http://10.1.1.1/Webserver/index.html` requests the index.html page from the server based on our supplied URI.|
|`POST`|`optional` Post is a way to submit information to a server based on the fields in the request. For example, submitting a message to a Facebook post or website forum is a POST action. The actual action taken can vary based on the server, and we should pay attention to the response codes sent back to validate the action.|
|`PUT`|`optional` Put will take the data appended to the message and place it under the requested URI. If an item does not exist there already, it will create one with the supplied data. If an object already exists, the new PUT will be considered the most up-to-date, and the object will be modified to match. The easiest way to visualize the differences between PUT and POST is to think of it like this; PUT will create or update an object at the URI supplied, while POST will create child entities at the provided URI. The action taken can be compared with the difference between creating a new file vs. writing comments about that file on the same page.|
|`DELETE`|`optional` Delete does as the name implies. It will remove the object at the given URI.|
|`TRACE`|`optional` Allows for remote server diagnosis. The remote server will echo the same request that was sent in its response if the TRACE method is enabled.|
|`OPTIONS`|`optional` The Options method can gather information on the supported HTTP methods the server recognizes. This way, we can determine the requirements for interacting with a specific resource or server without actually requesting data or objects from it.|
|`CONNECT`|`optional` Connect is reserved for use with Proxies or other security devices like firewalls. Connect allows for tunneling over HTTP. (`SSL tunnels`)|

Observe que listamos obrigatórios ou opcionais ao lado de cada método. Como requisito do padrão, GET e HEAD devem sempre funcionar e existir com implementações HTTP padrão. Isso é verdade apenas para eles. Os métodos trace, options, delete, put e post são funcionalidades opcionais que podem ser permitidas. Um exemplo disso é uma página da Web somente leitura, como uma postagem de blog. O PC cliente pode solicitar um recurso da página, mas não modificar, adicionar ou excluir o recurso ou recursos.

## HTTPs

HTTP Secure (HTTPS) é uma modificação do protocolo HTTP projetado para utilizar Transport Layer Security (TLS) ou Secure Sockets Layer (SSL) com aplicativos mais antigos para segurança de dados. O TLS é utilizado como um mecanismo de criptografia para proteger as comunicações entre um cliente e um servidor. O TLS pode agrupar o tráfego HTTP regular dentro do TLS, o que significa que podemos criptografar toda a nossa conversa, não apenas os dados enviados ou solicitados. Antes da implantação do mecanismo TLS, éramos vulneráveis ​​a ataques Man-in-the-middle e outros tipos de reconhecimento ou sequestro, o que significa que qualquer pessoa na mesma LAN que o cliente ou servidor pode visualizar o tráfego da Web se estiver ouvindo na rede. Agora podemos ter a segurança implementada no navegador, permitindo que todos criptografem seus hábitos na web, solicitações de pesquisa, sessões ou transferências de dados, transações bancárias e muito mais.

Mesmo sendo HTTP em sua base, o HTTPS utiliza as portas 443 e 8443 ao invés da porta padrão 80. Essa é uma forma simples de o cliente sinalizar ao servidor que deseja estabelecer uma conexão segura. Vejamos uma saída de tráfego HTTPS e vejamos como um handshake TLS funciona por um minuto.

![[Pasted image 20230529212612.png]]

Nos primeiros pacotes, podemos ver que o cliente estabelece uma sessão com o servidor usando a porta 443 em caixa azul. Isso sinaliza ao servidor que deseja usar HTTPS como o protocolo de comunicação do aplicativo.

Depois que uma sessão é iniciada via TCP, um TLS ClientHello é enviado em seguida para iniciar o handshake TLS. Durante o handshake, vários parâmetros são acordados, incluindo identificador de sessão, certificado peer x509, algoritmo de compactação a ser usado, o algoritmo de criptografia de especificação de cifra, se a sessão for retomável e um segredo mestre de 48 bytes compartilhado entre o cliente e o servidor para validar a sessão.

Depois que a sessão for estabelecida, todos os dados e métodos serão enviados por meio da conexão TLS e aparecerão como Dados do aplicativo TLS, conforme visto na caixa vermelha. O TLS ainda está usando o TCP como seu protocolo de transporte, portanto, ainda veremos pacotes de confirmação do fluxo vindo da porta 443.

Resumindo o Three-way handshake:
1. Cliente e servidor trocam mensagens de saudação para concordar com os parâmetros de conexão.
2. O cliente e o servidor trocam os parâmetros criptográficos necessários para estabelecer um segredo pré-mestre.
3. O cliente e o servidor trocarão certificados x.509 e informações criptográficas que permitam a autenticação na sessão.
4. Gere um segredo mestre a partir do segredo pré-mestre e troque valores aleatórios.
5. O cliente e o servidor emitem parâmetros de segurança negociados para a parte da camada de registro do protocolo TLS.
6. O cliente e o servidor verificam se seus pares calcularam os mesmos parâmetros de segurança e se o handshake ocorreu sem adulteração por um invasor.

A criptografia em si é um tópico complexo e extenso que merece seu próprio módulo. Esta seção é um resumo simples de como HTTP e TLS fornecem segurança dentro do protocolo de aplicativo HTTPS. Para obter mais informações sobre como o HTTPS funciona e como o TLS realiza operações de segurança, consulte RFC:2246.

# FTP

File Transfer Protocol (FTP) é um protocolo de camada de aplicativo que permite a transferência rápida de dados entre dispositivos de computação. O FTP pode ser utilizado na linha de comando, navegador da Web ou por meio de um cliente FTP gráfico, como o FileZilla. O próprio FTP é estabelecido como um protocolo inseguro, e a maioria dos usuários passou a utilizar ferramentas como SFTP para transferir arquivos por meio de canais seguros. Como uma observação para o futuro, a maioria dos navegadores modernos desativou o suporte para FTP a partir de 2020.

Quando pensamos em comunicação entre hosts, normalmente pensamos em um cliente e um servidor conversando em um único soquete. Por meio desse soquete, tanto o cliente quanto o servidor enviam comandos e dados pelo mesmo link. Nesse aspecto, o FTP é único, pois utiliza várias portas ao mesmo tempo. O FTP usa as portas 20 e 21 sobre TCP. A porta 20 é usada para transferência de dados, enquanto a porta 21 é utilizada para emitir comandos que controlam a sessão FTP. No que diz respeito à autenticação, o FTP oferece suporte à autenticação do usuário, além de permitir o acesso anônimo, se configurado.

O FTP é capaz de funcionar em dois modos diferentes, ativo ou passivo. Ativo é o método operacional padrão utilizado pelo FTP, o que significa que o servidor escuta um comando de controle PORT do cliente, informando qual porta usar para transferência de dados. O modo passivo nos permite acessar servidores FTP localizados atrás de firewalls ou um link habilitado para NAT que impossibilita conexões TCP diretas. Nesse caso, o cliente enviaria o comando PASV e aguardaria uma resposta do servidor informando ao cliente qual IP e porta utilizar para a conexão do canal de transferência de dados.

![[Pasted image 20230529214128.png]]

A imagem acima mostra vários exemplos de solicitações emitidas pelas setas verdes do canal de comando FTP e as respostas enviadas de volta pelas setas azuis do servidor FTP. Isso é tudo bastante normal. Para obter uma lista de cada comando e o que ele está fazendo, confira a tabela abaixo.

Ao observar o tráfego de FTP, alguns comandos comuns que podemos ver transmitidos pela porta 21 incluem:

|**Command**|**Description**|
|---|---|
|`USER`|specifies the user to log in as.|
|`PASS`|sends the password for the user attempting to log in.|
|`PORT`|when in active mode, this will change the data port used.|
|`PASV`|switches the connection to the server from active mode to passive.|
|`LIST`|displays a list of the files in the current directory.|
|`CWD`|will change the current working directory to one specified.|
|`PWD`|prints out the directory you are currently working in.|
|`SIZE`|will return the size of a file specified.|
|`RETR`|retrieves the file from the FTP server.|
|`QUIT`|ends the session.|

Esta não é uma lista exaustiva dos possíveis comandos de controle de FTP que podem ser vistos. Eles podem variar de acordo com o aplicativo FTP ou shell em uso. Para obter mais informações sobre FTP, consulte RFC:959.

# SMB

Server Message Block (SMB) é um protocolo amplamente visto em ambientes corporativos do Windows que permite o compartilhamento de recursos entre hosts em arquiteturas de rede comuns. SMB é um protocolo orientado a conexão que requer autenticação do usuário do host para o recurso para garantir que o usuário tenha as permissões corretas para usar esse recurso ou executar ações. No passado, o SMB utilizava o NetBIOS como seu mecanismo de transporte nas portas UDP 137 e 138. Desde as mudanças modernas, o SMB agora suporta transporte TCP direto na porta 445, NetBIOS na porta TCP 139 e até mesmo o protocolo QUIC.

Como usuário, o SMB nos fornece acesso fácil e conveniente a recursos como impressoras, unidades compartilhadas, servidores de autenticação e muito mais. Por esse motivo, o SMB também é muito atraente para invasores em potencial.

Como qualquer outro aplicativo que usa o TCP como seu mecanismo de transporte, ele executará funções padrão como o handshake de três vias e a confirmação de pacotes recebidos. Vamos dar uma olhada em algum tráfego SMB para nos familiarizarmos.

![[Pasted image 20230529214317.png]]

Observando a imagem acima, podemos ver que ele realiza o handshake TCP toda vez que estabelece uma sessão de caixas laranja. Ao observar a caixa azul das portas de origem e destino, a porta 445 está sendo utilizada, sinalizando o tráfego SMB sobre TCP. Se olharmos para as caixas verdes, o campo info nos conta um pouco sobre o que está acontecendo na comunicação SMB. Neste exemplo, há muitos erros, o que é um exemplo de algo para se aprofundar. Uma ou duas falhas de autenticação de um usuário são relativamente comuns, mas um grande grupo delas repetidas pode sinalizar um possível indivíduo não autorizado tentando acessar a conta de um usuário ou usar suas credenciais para se mover. Essa é uma tática comum dos invasores, capturar um usuário autenticado, roubar suas credenciais, utilizá-los para se mover lateralmente ou acessar recursos aos quais normalmente o acesso seria negado.

Este é apenas um exemplo de uso do SMB. Outra coisa comum que veremos é o acesso de compartilhamento de arquivos entre servidores e hosts. Na maior parte, esta é uma comunicação regular. No entanto, se virmos um arquivo de acesso de host compartilhado em outros hosts, isso não é comum. Preste atenção em quem está solicitando conexões, para onde e o que estão fazendo.