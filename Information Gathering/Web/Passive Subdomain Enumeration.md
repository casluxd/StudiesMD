Enumeração de subdomínio refere-se ao mapeamento de todos os subdomínios disponíveis em um nome de domínio. Aumenta nossa superfície de ataque e pode descobrir painéis ocultos de back-end de gerenciamento ou aplicativos da web da intranet que os administradores de rede esperavam manter ocultos usando a estratégia "segurança por obscuridade". Nesse ponto, realizaremos apenas enumeração passiva de subdomínio usando serviços de terceiros ou informações publicamente disponíveis. Ainda assim, expandiremos as informações que coletamos em futuras atividades ativas de enumeração de subdomínios.

## VirusTotal

O VirusTotal mantém seu serviço de replicação de DNS, desenvolvido preservando as resoluções de DNS feitas quando os usuários visitam URLs fornecidos por eles. Para receber informações sobre um domínio, digite o nome do domínio na barra de pesquisa e clique na guia "Relations".

![[Pasted image 20230629220039.png]]

## Certificados

Outra fonte interessante de informação que podemos usar para extrair subdomínios são os certificados SSL / TLS. O principal motivo é a Transparência de Certificação ( CT ), um projeto que exige que todos os certificados SSL / TLS emitidos por uma Autoridade de Certificação ( CA ) sejam publicados em um log acessível ao público.

Aprenderemos como examinar os logs de CT para descobrir nomes de domínio e subdomínios adicionais para uma organização de destino usando dois recursos principais:

- https://censys.io
- https://crt.sh

Podemos navegar para https://search.censys.io/certificates ou https://crt.sh e introduzir o nome de domínio de nossa organização-alvo para começar a descobrir novos subdomínios.

![[Pasted image 20230629220103.png]]

![[Pasted image 20230629220106.png]]

Embora o site seja excelente, gostaríamos de organizar essas informações e poder combiná-las com outras fontes encontradas ao longo do processo de coleta de informações. Vamos executar uma solicitação de curvatura no site de destino solicitando uma saída JSON, pois isso é mais gerenciável para processarmos. Podemos fazer isso através dos seguintes comandos:

#### Transparência do certificado

```shell-session
casluxd@htb[/htb]$ export TARGET="facebook.com"
casluxd@htb[/htb]$ curl -s "https://crt.sh/?q=${TARGET}&output=json" | jq -r '.[] | "\(.name_value)\n\(.common_name)"' | sort -u > "${TARGET}_crt.sh.txt"
```

```shell-session
casluxd@htb[/htb]$ head -n20 facebook.com_crt.sh.txt

*.adtools.facebook.com
*.ak.facebook.com
*.ak.fbcdn.net
*.alpha.facebook.com
*.assistant.facebook.com
*.beta.facebook.com
*.channel.facebook.com
*.cinyour.facebook.com
*.cinyourrc.facebook.com
*.connect.facebook.com
*.cstools.facebook.com
*.ctscan.facebook.com
*.dev.facebook.com
*.dns.facebook.com
*.extern.facebook.com
*.extools.facebook.com
*.f--facebook.com
*.facebook.com
*.facebookcorewwwi.onion
*.facebookmail.com
```

Também podemos executar manualmente esta operação em um destino usando o OpenSSL via:

```shell-session
casluxd@htb[/htb]$ export TARGET="facebook.com"
casluxd@htb[/htb]$ export PORT="443"
casluxd@htb[/htb]$ openssl s_client -ign_eof 2>/dev/null <<<$'HEAD / HTTP/1.0\r\n\r' -connect "${TARGET}:${PORT}" | openssl x509 -noout -text -in - | grep 'DNS' | sed -e 's|DNS:|\n|g' -e 's|^\*.*||g' | tr -d ',' | sort -u

*.facebook.com
*.facebook.net
*.fbcdn.net
*.fbsbx.com
*.m.facebook.com
*.messenger.com
*.xx.fbcdn.net
*.xy.fbcdn.net
*.xz.fbcdn.net
facebook.com
messenger.com
```

## Automatizando a enumeração passiva de subdomínios

Aprendemos a adquirir informações úteis de nossa organização-alvo, como subdomínios, padrões de nomes, TLDs alternativos, intervalos de IP etc., usando serviços de terceiros sem interagir diretamente com sua infraestrutura ou confiar em ferramentas automatizadas. Agora, aprenderemos como enumerar subdomínios usando ferramentas e informações obtidas anteriormente.

[TheHarvester](https://github.com/laramies/theHarvester) é uma ferramenta simples de usar, porém poderosa e eficaz, para testes de penetração em estágio inicial e compromissos em equipe vermelha. Podemos usá-lo para coletar informações para ajudar a identificar a superfície de ataque de uma empresa. A ferramenta coleta `emails`, `names`, `subdomains`, `IP addresses`, e `URLs` de várias fontes de dados públicos para coleta passiva de informações. 

Para automatizar isso, criaremos um arquivo chamado sources.txt com o seguinte conteúdo.

```shell-session
casluxd@htb[/htb]$ cat sources.txt

baidu
bufferoverun
crtsh
hackertarget
otx
projecdiscovery
rapiddns
sublist3r
threatcrowd
trello
urlscan
vhost
virustotal
zoomeye
```

Depois que o arquivo for criado, executaremos os seguintes comandos para coletar informações dessas fontes.

```shell-session
casluxd@htb[/htb]$ export TARGET="facebook.com"
casluxd@htb[/htb]$ cat sources.txt | while read source; do theHarvester -d "${TARGET}" -b $source -f "${source}_${TARGET}";done

<SNIP>
*******************************************************************
*  _   _                                            _             *
* | |_| |__   ___    /\  /\__ _ _ ____   _____  ___| |_ ___ _ __  *
* | __|  _ \ / _ \  / /_/ / _` | '__\ \ / / _ \/ __| __/ _ \ '__| *
* | |_| | | |  __/ / __  / (_| | |   \ V /  __/\__ \ ||  __/ |    *
*  \__|_| |_|\___| \/ /_/ \__,_|_|    \_/ \___||___/\__\___|_|    *
*                                                                 *
* theHarvester 4.0.0                                              *
* Coded by Christian Martorella                                   *
* Edge-Security Research                                          *
* cmartorella@edge-security.com                                   *
*                                                                 *
*******************************************************************


[*] Target: facebook.com

[*] Searching Urlscan.

[*] ASNS found: 29
--------------------
AS12578
AS13335
AS13535
AS136023
AS14061
AS14618
AS15169
AS15817

<SNIP>
```

Quando o processo termina, podemos extrair todos os subdomínios encontrados e classificá-los através do seguinte comando:

```shell-session
casluxd@htb[/htb]$ cat *.json | jq -r '.hosts[]' 2>/dev/null | cut -d':' -f 1 | sort -u > "${TARGET}_theHarvester.txt"
```

Agora podemos mesclar todos os arquivos de reconhecimento passivo via:

```shell-session
casluxd@htb[/htb]$ cat facebook.com_*.txt | sort -u > facebook.com_subdomains_passive.txt
casluxd@htb[/htb]$ cat facebook.com_subdomains_passive.txt | wc -l

11947
```

Até o momento, conseguimos encontrar subdomínios de 11947 mesclando os arquivos de resultados de reconhecimento passivo. É importante observar aqui que existem muitos outros métodos para encontrar subdomínios passivamente. 