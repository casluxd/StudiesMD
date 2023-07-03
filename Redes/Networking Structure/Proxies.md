
Muitas pessoas têm opiniões diferentes sobre o que é um proxy:

- Os profissionais de segurança jump para Proxies HTTP (BurpSuite) ou pivoting com um Proxy SOCKS/SSH (Chisel, ptunnel, sshuttle).
- Os desenvolvedores da Web usam proxies como Cloudflare ou ModSecurity para bloquear o tráfego malicioso.
- As pessoas comuns podem pensar que um proxy é usado para ofuscar sua localização e acessar o catálogo Netflix de outro país.
- A aplicação da lei geralmente atribui proxies a atividades ilegais.

Nem todos os exemplos acima estão corretos. Um proxy é quando um dispositivo ou serviço fica no meio de uma conexão e atua como um mediador. O mediador é a informação crítica porque significa que o dispositivo no meio deve ser capaz de inspecionar o conteúdo do tráfego. Sem a capacidade de ser um mediador, o dispositivo é tecnicamente um gateway, não um proxy.

Voltando à pergunta acima, a pessoa comum tem uma ideia errada do que é um proxy, pois provavelmente está usando uma VPN para ofuscar sua localização, o que tecnicamente não é um proxy. A maioria das pessoas pensa que sempre que um endereço IP muda, é um proxy e, na maioria dos casos, provavelmente é melhor não corrigi-lo, pois é um equívoco comum e inofensivo. Corrigi-los pode levar a uma conversa mais extensa que inclui guias versus espaços, emacs versus vim ou descobrir que eles são um usuário nano.

Se você tiver problemas para lembrar disso, os proxies quase sempre operarão na camada 7 do modelo OSI. Existem muitos tipos de serviços de proxy, mas os principais são:
- Dedicated Proxy / Forward Proxy
- Reverse Proxy
- Transparent Proxy

# Dedicated Proxy / Forward Proxy

O Forward Proxy, é o que a maioria das pessoas imagina que seja um proxy. Um Forward Proxy é quando um cliente faz uma solicitação a um computador e esse computador realiza a solicitação.

Por exemplo, em uma rede corporativa, computadores sensíveis podem não ter acesso direto à Internet. Para acessar um site, eles devem passar por um proxy (ou filtro da web). Essa pode ser uma linha de defesa incrivelmente poderosa contra malware, pois não só precisa ignorar o filtro da Web (fácil), mas também precisa reconhecer proxy ou usar um C2 não tradicional (uma maneira de o malware receber informações de tarefas). Se a organização utiliza apenas o FireFox, a probabilidade de obter malware com reconhecimento de proxy é improvável.

Navegadores da Web como Internet Explorer, Edge ou Chrome obedecem às configurações de "Proxy do sistema" por padrão. Se o malware utilizar o WinSock (API nativa do Windows), provavelmente reconhecerá o proxy sem nenhum código adicional. O Firefox não usa WinSock e, em vez disso, usa libcurl, que permite usar o mesmo código em qualquer sistema operacional. Isso significa que o malware precisaria procurar o Firefox e obter as configurações de proxy, o que é altamente improvável que o malware faça.

Como alternativa, o malware pode usar o DNS como um mecanismo c2, mas se uma organização estiver monitorando o DNS (o que é feito facilmente usando o Sysmon ), esse tipo de tráfego deve ser capturado rapidamente.

Outro exemplo de Forward Proxy é o Burp Suite, já que a maioria das pessoas o utiliza para encaminhar solicitações HTTP. Porém, esse aplicativo é o canivete suíço dos Proxies HTTP e pode ser configurado para ser um proxy reverso ou transparente!

#### Forward Proxy

![[Pasted image 20230526230912.png]]

## Reverse Proxy

Como você deve ter adivinhado, um proxy reverso é o inverso de um proxy de encaminhamento. Em vez de ser projetado para filtrar as solicitações de saída, ele filtra as de entrada. O objetivo mais comum com um proxy reverso é escutar em um endereço e encaminhá-lo para uma rede fechada.

Muitas organizações usam o CloudFlare porque possuem uma rede robusta que pode suportar a maioria dos ataques DDOS. Ao usar o Cloudflare, as organizações têm uma maneira de filtrar a quantidade (e o tipo) de tráfego que é enviado para seus servidores da web.

Os Penetration Testers configurarão proxies reversos em endpoints infectados. O terminal infectado escutará em uma porta e enviará qualquer cliente que se conectar à porta de volta ao invasor por meio do terminal infectado. Isso é útil para contornar firewalls ou evitar o log. As organizações podem ter IDS (Sistemas de Detecção de Intrusão), monitorando solicitações externas da web. Se o invasor obtiver acesso à organização por SSH, um proxy reverso poderá enviar solicitações da Web por meio do túnel SSH e evitar o IDS.

Outro proxy reverso comum é o ModSecurity, um Web Application Firewall (WAF). Os firewalls de aplicativos da Web inspecionam solicitações da Web em busca de conteúdo mal-intencionado e bloqueiam a solicitação se for mal-intencionada. Se você quiser saber mais sobre isso, recomendamos a leitura do conjunto de regras básicas do ModSecurity, pois é um excelente ponto de partida. Cloudflare também pode atuar como um WAF, mas isso requer permitir que eles descriptografem o tráfego HTTPS, o que algumas organizações podem não querer.

![[Pasted image 20230526231103.png]]

## Non Transparent Proxy

Todos esses serviços de proxy atuam de forma transparente ou não transparente.

Com um proxy transparente, o cliente não sabe de sua existência. O proxy transparente intercepta as solicitações de comunicação do cliente para a Internet e atua como uma instância substituta. Para o exterior, o proxy transparente, assim como o proxy não transparente, atua como um parceiro de comunicação.

Se for uma proxy não transparente, devemos ser informados sobre sua existência. Para isso, nós e o software que queremos usar recebemos uma configuração de proxy especial que garante que o tráfego para a Internet seja endereçado primeiro ao proxy. Se esta configuração não existir, não podemos nos comunicar via proxy. No entanto, como o proxy geralmente fornece o único caminho de comunicação para outras redes, a comunicação com a Internet geralmente é interrompida sem uma configuração de proxy correspondente.