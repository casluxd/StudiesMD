DNS pode ser conhecido por ser responsável pela tradução de FQDN para IP. Porém o DNS vai além disso e é responsável também por fornecer informações abragentes sobre um domínio, entre essas informações estão:
- Subdomínios: Um domínio pode ter vários subdomínios associados a ele, como "www", "blog", "ftp" e assim por diante. O servidor DNS retorna os registros de subdomínios para direcionar solicitações específicas para os recursos correspondentes. Cada subdomínio pode ter diferentes registros DNS associados, como endereços IP, registros MX para e-mail, registros TXT para informações adicionais, etc.
- Registros TXT: 1. Os registros TXT (text) são usados para armazenar informações arbitrárias em formato de texto. Eles podem ser usados para vários fins, como fornecer informações de autenticação, políticas de segurança, verificação de domínio para serviços de terceiros, entre outros. Os registros TXT podem conter dados como chaves de API, chaves de assinatura de e-mail, diretrizes de SPF (Sender Policy Framework), DKIM (DomainKeys Identified Mail) e muito mais.
- Outros registros DNS: Além de subdomínios e registros TXT, o servidor DNS também pode retornar outros tipos de registros, como registros A (endereços IPv4), registros AAAA (endereços IPv6), registros MX (servidores de e-mail), registros CNAME (aliases de domínio), registros NS (servidores de nomes autoritativos) e assim por diante. Cada tipo de registro desempenha um papel específico no mapeamento do nome de domínio para os recursos correspondentes.

Em resumo, quando você faz uma consulta para o servidor DNS de um website, ele retorna informações detalhadas para ajudar na resolução correta do domínio em um endereço IP e fornecer outros detalhes relevantes para o funcionamento e configuração do domínio, como subdomínios, registros TXT e outros registros DNS específicos.

# Processo de tradução FQDN para IP

Ao digitar a URL de um site para acessá-lo, para obter o endereço IP desse site, o seguinte processo é feito:
1. Quando você digita a URL de um site em um navegador, seu computador (cliente) verifica seu cache local de DNS para ver se já possui o endereço IP correspondente ao nome de domínio. Se estiver armazenado em cache e ainda válido, ele retorna o endereço IP diretamente.
2. Caso não esteja armazenado em cache ou tenha expirado, o cliente enviará uma solicitação de resolução de DNS ao servidor de DNS designado, que é tipicamente o servidor DNS do seu provedor de serviços de Internet (ISP) ou o servidor DNS local em sua rede.
3. O servidor de DNS local, conhecido como Resolving Name Server, verificará seu próprio cache para ver se possui o registro DNS solicitado armazenado. Se tiver em cache e ainda válido, ele retornará o endereço IP para o cliente.
4. Se o servidor de DNS local não tiver o registro em cache, ele iniciará o processo de resolução consultando os servidores raiz (Root Name Servers). Os servidores raiz são responsáveis por lidar com as consultas de DNS relacionadas aos domínios de nível superior (TLDs) como ".com", ".org", ".net", etc.
5. O servidor raiz encaminhará a solicitação do Resolving Name Server para o servidor de nomes TLD (TLD Name Server) apropriado, com base na extensão do domínio solicitado. Por exemplo, se o domínio for "example.com", a consulta será encaminhada para os servidores TLD responsáveis por ".com".
6. O servidor TLD, por sua vez, encaminhará a consulta ao servidor de nomes autoritativo (Authoritative Name Server) associado ao domínio solicitado(ou seja, o servidor DNS do domínio). Esse servidor de nomes autoritativo é responsável por manter os registros DNS do domínio específico.
7. O servidor de nomes autoritativo recebe a consulta e responde ao Resolving Name Server com o endereço IP do domínio solicitado.
8. O Resolving Name Server, finalmente, recebe a resposta do servidor de nomes autoritativo e armazena o registro em cache para consultas futuras. Em seguida, ele retorna o endereço IP para o cliente solicitante.
9. Com o endereço IP em mãos, o cliente pode estabelecer uma conexão direta com o servidor web correto do site desejado e fazer a solicitação para carregar o conteúdo da página.

Esse processo de resolução de DNS ocorre em questão de milissegundos, permitindo que você acesse os sites digitando apenas a URL amigável, enquanto o sistema de DNS cuida dos bastidores para converter o nome de domínio em um endereço IP correspondente.

#### E qual o papel do servidor DNS do website nisso tudo?

O servidor DNS do website desempenha um papel crucial no processo de resolução de nomes (DNS). Aqui estão as principais funções do servidor DNS do website:

1. Registro de DNS: O servidor DNS do website é responsável por armazenar os registros DNS associados ao domínio específico. Esses registros incluem informações como endereços IP, registros MX para e-mail, registros TXT para informações adicionais, registros CNAME para aliases, entre outros.
2. Autoridade DNS: O servidor DNS do website é considerado um servidor de nomes autoritativo para o domínio. Isso significa que ele é a fonte definitiva e confiável de informações DNS para esse domínio. Quando um Resolving Name Server faz uma consulta para o domínio, o servidor DNS do website é consultado para fornecer as informações corretas.
3. Respostas às consultas de DNS: Quando o servidor DNS do website recebe uma consulta de resolução de DNS de um Resolving Name Server, ele processa a consulta e retorna as informações solicitadas. Isso inclui o fornecimento do endereço IP correto associado ao domínio.
4. Configuração de TTL: O servidor DNS do website define o valor de TTL (Time-to-Live) nos registros DNS. O TTL determina por quanto tempo as informações de DNS devem ser armazenadas em cache pelos Resolving Name Servers antes de expirarem. Isso ajuda a controlar a frequência com que os Resolving Name Servers precisam fazer consultas ao servidor DNS do website.
5. Atualização de registros DNS: O servidor DNS do website permite a atualização e modificação dos registros DNS. Isso é importante quando há alterações no servidor web, alterações de endereços IP, configurações de e-mail ou outras configurações relacionadas ao domínio. As atualizações no servidor DNS do website garantem que as informações corretas sejam fornecidas aos Resolving Name Servers.

Em resumo, o servidor DNS do website é responsável por manter os registros DNS do domínio, responder às consultas de resolução de DNS e garantir que as informações corretas sejam fornecidas aos Resolving Name Servers. É uma peça fundamental para garantir a correta resolução do domínio em um endereço IP e a disponibilidade e funcionamento adequados do website.

#### Se não existe o servidor DNS do website, o que acontece?

Se não existir um servidor DNS configurado para um website, isso significa que não há um servidor de nomes autoritativo para o domínio específico. Nesse caso, algumas consequências podem ocorrer:

1. Falha na resolução de DNS: Quando um cliente tenta acessar o website digitando sua URL em um navegador, a resolução de DNS falhará, pois não há um servidor DNS para fornecer o endereço IP correspondente ao domínio. Como resultado, o website não será acessível pelo nome de domínio.
2. Erros de "endereço não encontrado": Os clientes receberão mensagens de erro, como "Endereço não encontrado" ou "Servidor DNS não encontrado", indicando que o domínio não pôde ser resolvido em um endereço IP válido.
3. Acesso por endereço IP: Embora o acesso ao website pelo nome de domínio não seja possível, ainda é possível acessá-lo diretamente pelo seu endereço IP, se você souber qual é. No entanto, isso não é prático para os usuários, pois eles precisam conhecer o endereço IP específico e digitar diretamente no navegador.
4. Ausência de serviços associados: Sem um servidor DNS configurado, os serviços associados ao domínio, como servidores de e-mail (registros MX), servidores FTP (registros A/CNAME) e outros, podem não funcionar corretamente ou não estar disponíveis.

É importante destacar que um servidor DNS é essencial para o funcionamento adequado de um website e sua acessibilidade pelo nome de domínio. É responsabilidade do proprietário do website configurar e manter corretamente um servidor DNS autoritativo para o domínio, a fim de permitir a correta resolução de DNS e o acesso ao website por meio do nome de domínio.

## Quais são os tipos de servidores DNS usados?

- DNS Root Server: Os servidores raiz do DNS são responsáveis pelos domínios de nível superior (`TLD`). Como última instância, eles são solicitados apenas se o servidor de nomes não responder. Assim, um servidor raiz é uma interface central entre usuários e conteúdo na Internet, pois vincula domínio e endereço IP. O [Corporação da Internet para nomes e números atribuídos](https://www.icann.org/) (`ICANN`) coordena o trabalho dos servidores de nomes raiz. Há `13` servidores raiz em todo o mundo.
- Authoritative Name Server: Servidores de nomes autorizados têm autoridade para uma zona específica. Eles respondem apenas a perguntas de sua área de responsabilidade e suas informações são vinculativas. Se um servidor de nomes com autoridade não puder responder à consulta de um cliente, o servidor de nomes raiz assumirá o controle nesse momento.
- Non-Authoritative Name Server: Servidores de nomes não autorizados não são responsáveis por uma zona DNS específica. Em **De novo, é necessário mencionar que o conhecimento adquirido com a instalação desses serviços e com as configurações em nosso próprio Windows Server VM para obter experiência e desenvolver o princípio funcional e o o ponto de vista do administrador não pode ser substituído pelos manuais de leitura. Portanto, é altamente recomendável configurar seu próprio Windows Server, experimentar as configurações e digitalizar esses serviços repetidamente para ver as diferenças nos resultados.

- Caching DNS Server: O cache de servidores DNS armazena em cache informações de outros servidores de nomes por um período especificado. O servidor de nomes com autoridade determina a duração desse armazenamento. 
- Forwarding Server: Os servidores de encaminhamento executam apenas uma função: eles encaminham consultas DNS para outro servidor DNS.
- Resolver: Os resolvedores não são servidores DNS autorizados, mas executam a resolução de nomes localmente no computador ou roteador.

## O DNS é seguro?

O DNS é principalmente não criptografado. Dispositivos na WLAN local e provedores de Internet podem, portanto, invadir e espionar consultas DNS. Como isso representa um risco à privacidade, agora existem algumas soluções para criptografia DNS. Por padrão, os profissionais de segurança de TI se aplicam `DNS over TLS` (`DoT`) ou `DNS over HTTPS` (`DoH`) aqui. Além disso, o protocolo de rede `NSCrypt` também criptografa o tráfego entre o computador e o servidor de nomes.

No entanto, o DNS não vincula apenas nomes de computadores e endereços IP. Ele também armazena e gera informações adicionais sobre os serviços associados a um domínio. Portanto, uma consulta DNS também pode ser usada, por exemplo, para determinar qual computador serve como servidor de email para o domínio em questão ou como os servidores de nomes do domínio são chamados.

![[Pasted image 20230619143927.png]]

Diferentes registros DNS são usados para as consultas DNS, todas com várias tarefas. Além disso, existem entradas separadas para diferentes funções, pois podemos configurar servidores de correio e outros servidores para um domínio.

|**Registro DNS**|**Descrição**|
|---|---|
|`A`|Retorna um endereço IPv4 do domínio solicitado como resultado.|
|`AAAA`|Retorna um endereço IPv6 do domínio solicitado.|
|`MX`|Retorna os servidores de correio responsáveis como resultado.|
|`NS`|Retorna os servidores DNS ( nameservers ) do domínio.|
|`TXT`|Este registro pode conter várias informações. O polivalente pode ser usado, por exemplo, para validar o console de pesquisa do Google ou validar certificados SSL. Além disso, as entradas SPF e DMARC são definidas para validar o tráfego de correio e protegê-lo contra spam.|
|`CNAME`|Este registro serve como um pseudônimo. Se o domínio www.hackthebox.eu deve apontar para o mesmo IP, e criamos um registro A para um e um registro CNAME para o outro.|
|`PTR`|O registro PTR funciona ao contrário ( pesquisa reversa ). Ele converte endereços IP em nomes de domínio válidos.|
|`SOA`|Fornece informações sobre a zona DNS correspondente e o endereço de email do contato administrativo.|

O registro SOA está localizado no arquivo de zona de um domínio e especifica quem é responsável pela operação do domínio e como as informações DNS do domínio são gerenciadas.

```shell-session
casluxd@htb[/htb]$ dig soa www.inlanefreight.com

; <<>> DiG 9.16.27-Debian <<>> soa www.inlanefreight.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 15876
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;www.inlanefreight.com.         IN      SOA

;; AUTHORITY SECTION:
inlanefreight.com.      900     IN      SOA     ns-161.awsdns-20.com. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400

;; Query time: 16 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Thu Jan 05 12:56:10 GMT 2023
;; MSG SIZE  rcvd: 128
```

O ponto é substituído por um sinal de no endereço de email. Neste exemplo, o endereço de email do administrador é `awsdns-hostmaster@amazon.com`.

## Configuração padrão

Existem muitos tipos de configuração diferentes para DNS. Portanto, discutiremos apenas os mais importantes para ilustrar melhor o princípio funcional do ponto de vista administrativo. Todos os servidores DNS funcionam com três tipos diferentes de arquivos de configuração:
1. arquivos de configuração DNS locais
2. arquivos de zona
3. arquivos de resolução de nome reverso

O servidor DNS [Bind9](https://www.isc.org/bind/) é frequentemente usado em distribuições baseadas em Linux. Seu arquivo de configuração local (`named.conf`) é dividido em duas seções, primeiro a seção de opções para configurações gerais e, em segundo lugar, as entradas de zona para os domínios individuais. Os arquivos de configuração local geralmente são:

- `named.conf.local`
- `named.conf.options`
- `named.conf.log`

Ele contém a RFC associada, onde podemos personalizar o servidor de acordo com nossas necessidades e nossa estrutura de domínio com as zonas individuais para diferentes domínios. O arquivo de configuração `named.conf` é dividido em várias opções que controlam o comportamento do servidor de nomes. É feita uma distinção entre `opções globais` e `opções de zona`.

As opções globais são gerais e afetam todas as zonas. Uma opção de zona afeta apenas a zona à qual foi atribuída. As opções não listadas em named.conf têm valores padrão. Se uma opção é global e específica da zona, a opção de zona tem precedência.

```shell-session
root@bind9:~# cat /etc/bind/named.conf.local

//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";
zone "domain.com" {
    type master;
    file "/etc/bind/db.domain.com";
    allow-update { key rndc-key; };
};
```

Neste arquivo, podemos definir as diferentes zonas. Essas zonas são divididas em arquivos individuais, que na maioria dos casos são destinados principalmente a apenas um domínio. Exceções são servidores ISP e DNS públicos. Além disso, muitas opções diferentes estendem ou reduzem a funcionalidade. Podemos procurar isso na [documentação](https://wiki.debian.org/Bind9) de Bind9.

O `zone file` é um arquivo de texto que descreve uma zona DNS com o formato de arquivo BIND. Em outras palavras, é um ponto de delegação na árvore do DNS. O formato do arquivo BIND é o formato do arquivo de zona preferida pelo setor e agora está bem estabelecido no software do servidor DNS. Um arquivo de zona descreve uma zona completamente. Deve haver precisamente um `SOA` registro e pelo menos um `NS` registro. O registro de recursos SOA geralmente está localizado no início de um arquivo de zona. O principal objetivo dessas regras globais é melhorar a legibilidade dos arquivos de zona. Um erro de sintaxe geralmente resulta em todo o arquivo de zona sendo considerado inutilizável. O servidor de nomes se comporta da mesma forma como se essa zona não existisse. Ele responde a consultas DNS com uma mensagem de erro `SERVFAIL`.

```shell-session
root@bind9:~# cat /etc/bind/db.domain.com

;
; BIND reverse data file for local loopback interface
;
$ORIGIN domain.com
$TTL 86400
@     IN     SOA    dns1.domain.com.     hostmaster.domain.com. (
                    2001062501 ; serial
                    21600      ; refresh after 6 hours
                    3600       ; retry after 1 hour
                    604800     ; expire after 1 week
                    86400 )    ; minimum TTL of 1 day

      IN     NS     ns1.domain.com.
      IN     NS     ns2.domain.com.

      IN     MX     10     mx.domain.com.
      IN     MX     20     mx2.domain.com.

             IN     A       10.129.14.5

server1      IN     A       10.129.14.5
server2      IN     A       10.129.14.7
ns1          IN     A       10.129.14.2
ns2          IN     A       10.129.14.3

ftp          IN     CNAME   server1
mx           IN     CNAME   server1
mx2          IN     CNAME   server2
www          IN     CNAME   server2
```

Para que o endereço IP seja resolvido a partir do `Fully Qualified Domain Name` (`FQDN`), o servidor DNS deve ter um arquivo de pesquisa reversa. Nesse arquivo, o nome do computador ( FQDN ) é atribuído ao último octeto de um endereço IP, que corresponde ao respectivo host, usando um `PTR` registro. Os registros PTR são responsáveis pela tradução reversa de endereços IP em nomes, como já vimos na tabela acima.

#### Arquivos de zona de resolução de nome reverso

```shell-session
root@bind9:~# cat /etc/bind/db.10.129.14

;
; BIND reverse data file for local loopback interface
;
$ORIGIN 14.129.10.in-addr.arpa
$TTL 86400
@     IN     SOA    dns1.domain.com.     hostmaster.domain.com. (
                    2001062501 ; serial
                    21600      ; refresh after 6 hours
                    3600       ; retry after 1 hour
                    604800     ; expire after 1 week
                    86400 )    ; minimum TTL of 1 day

      IN     NS     ns1.domain.com.
      IN     NS     ns2.domain.com.

5    IN     PTR    server1.domain.com.
7    IN     MX     mx.domain.com.
...SNIP...
```

## Configurações perigosas

Existem muitas maneiras pelas quais um servidor DNS pode ser atacado. Por exemplo, uma lista de vulnerabilidades direcionadas ao servidor BIND9 pode ser encontrada em [Detalhes do CVE](https://www.cvedetails.com/product/144/ISC-Bind.html?vendor_id=64). Além disso, o SecurityTrails fornece um curto [lista](https://securitytrails.com/blog/most-popular-types-dns-attacks) dos ataques mais populares em servidores DNS.

Algumas das configurações que podemos ver abaixo levam a essas vulnerabilidades, entre outras. Como o DNS pode ficar muito complicado e é muito fácil que erros entrem nesse serviço, forçando um administrador a contornar o problema até encontrar uma solução exata. Isso geralmente leva à liberação de elementos para que partes da infraestrutura funcionem conforme planejado e desejado. Nesses casos, a funcionalidade tem uma prioridade mais alta que a segurança, o que leva a configurações incorretas e vulnerabilidades.

|**Opção**|**Descrição**|
|---|---|
|`allow-query`|Define quais hosts podem enviar solicitações para o servidor DNS.|
|`allow-recursion`|Define quais hosts podem enviar solicitações recursivas ao servidor DNS.|
|`allow-transfer`|Define quais hosts podem receber transferências de zona do servidor DNS.|
|`zone-statistics`|Coleta dados estatísticos de zonas.|

## Pegando o serviço

A pegada nos servidores DNS é feita como resultado das solicitações que enviamos. Portanto, primeiro, o servidor DNS pode ser consultado sobre quais outros servidores de nomes são conhecidos. Fazemos isso usando o registro NS e a especificação do servidor DNS que queremos consultar usando o `@`. Isso ocorre porque, se houver outros servidores DNS, também podemos usá-los e consultar os registros. No entanto, outros servidores DNS podem ser configurados de maneira diferente e, além disso, podem ser permanentes para outras zonas.

#### DIG - Consulta NS 

```shell-session
casluxd@htb[/htb]$ dig ns inlanefreight.htb @10.129.14.128

; <<>> DiG 9.16.1-Ubuntu <<>> ns inlanefreight.htb @10.129.14.128
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 45010
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: ce4d8681b32abaea0100000061475f73842c401c391690c7 (good)
;; QUESTION SECTION:
;inlanefreight.htb.             IN      NS

;; ANSWER SECTION:
inlanefreight.htb.      604800  IN      NS      ns.inlanefreight.htb.

;; ADDITIONAL SECTION:
ns.inlanefreight.htb.   604800  IN      A       10.129.34.136

;; Query time: 0 msec
;; SERVER: 10.129.14.128#53(10.129.14.128)
;; WHEN: So Sep 19 18:04:03 CEST 2021
;; MSG SIZE  rcvd: 107
```

Às vezes, também é possível consultar a versão de um servidor DNS usando uma consulta CHAOS da classe e digite TXT. No entanto, essa entrada deve existir no servidor DNS. Para isso, poderíamos usar o seguinte comando:

```shell-session
casluxd@htb[/htb]$ dig CH TXT version.bind 10.129.120.85

; <<>> DiG 9.10.6 <<>> CH TXT version.bind
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 47786
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; ANSWER SECTION:
version.bind.       0       CH      TXT     "9.10.6-P1"

;; ADDITIONAL SECTION:
version.bind.       0       CH      TXT     "9.10.6-P1-Debian"

;; Query time: 2 msec
;; SERVER: 10.129.120.85#53(10.129.120.85)
;; WHEN: Wed Jan 05 20:23:14 UTC 2023
;; MSG SIZE  rcvd: 101
```

Podemos usar a opção `ANY` para visualizar todos os registros disponíveis. Isso fará com que o servidor nos mostre todas as entradas disponíveis que deseja divulgar. É importante observar que nem todas as entradas das zonas serão mostradas.

```shell-session
casluxd@htb[/htb]$ dig any inlanefreight.htb @10.129.14.128

; <<>> DiG 9.16.1-Ubuntu <<>> any inlanefreight.htb @10.129.14.128
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 7649
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 5, AUTHORITY: 0, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: 064b7e1f091b95120100000061476865a6026d01f87d10ca (good)
;; QUESTION SECTION:
;inlanefreight.htb.             IN      ANY

;; ANSWER SECTION:
inlanefreight.htb.      604800  IN      TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
inlanefreight.htb.      604800  IN      TXT     "atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"
inlanefreight.htb.      604800  IN      TXT     "MS=ms97310371"
inlanefreight.htb.      604800  IN      SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
inlanefreight.htb.      604800  IN      NS      ns.inlanefreight.htb.

;; ADDITIONAL SECTION:
ns.inlanefreight.htb.   604800  IN      A       10.129.34.136

;; Query time: 0 msec
;; SERVER: 10.129.14.128#53(10.129.14.128)
;; WHEN: So Sep 19 18:42:13 CEST 2021
;; MSG SIZE  rcvd: 437
```

`Zone transfer` refere-se à transferência de zonas para outro servidor no DNS, o que geralmente acontece na porta TCP 53. Este procedimento é abreviado `Asynchronous Full Transfer Zone` (`AXFR`). Como uma falha no DNS geralmente tem consequências graves para uma empresa, o arquivo de zona é quase sempre mantido idêntico em vários servidores de nomes. Quando são feitas alterações, deve-se garantir que todos os servidores tenham os mesmos dados. A sincronização entre os servidores envolvidos é realizada por transferência de zona. Usando uma chave secreta `rndc-key`, que vimos inicialmente na configuração padrão, os servidores garantem que eles se comuniquem com seu próprio mestre ou escravo. A transferência de zona envolve a mera transferência de arquivos ou registros e a detecção de discrepâncias nos conjuntos de dados dos servidores envolvidos.

Os dados originais de uma zona estão localizados em um servidor DNS, chamado de `primary` nome do servidor para esta zona. No entanto, para aumentar a confiabilidade, realizar uma simples distribuição de carga ou proteger o primário de ataques, um ou mais servidores adicionais são instalados na prática em quase todos os casos, chamados `secondary` servidores de nomes para esta zona. Para alguns `Top-Level Domains` (`TLDs`), criando arquivos de zona para o `Second Level Domains` acessível em pelo menos dois servidores é obrigatório.

Geralmente, as entradas DNS são criadas, modificadas ou excluídas apenas no primário. Isso pode ser feito editando manualmente o arquivo de zona relevante ou automaticamente por uma atualização dinâmica de um banco de dados. Um servidor DNS que serve como fonte direta para sincronizar um arquivo de zona é chamado de mestre. Um servidor DNS que obtém dados de zona de um mestre é chamado de escravo. Um primário é sempre um mestre, enquanto um secundário pode ser um escravo e um mestre.

O escravo busca o `SOA` registro da zona relevante do mestre em determinados intervalos, o chamado tempo de atualização, geralmente uma hora, e compara os números de série. Se o número de série do registro SOA do mestre for maior que o do escravo, os conjuntos de dados não corresponderão mais.

#### DIG - Transferência de zona AXFR

```shell-session
casluxd@htb[/htb]$ dig axfr inlanefreight.htb @10.129.14.128

; <<>> DiG 9.16.1-Ubuntu <<>> axfr inlanefreight.htb @10.129.14.128
;; global options: +cmd
inlanefreight.htb.      604800  IN      SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
inlanefreight.htb.      604800  IN      TXT     "MS=ms97310371"
inlanefreight.htb.      604800  IN      TXT     "atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"
inlanefreight.htb.      604800  IN      TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
inlanefreight.htb.      604800  IN      NS      ns.inlanefreight.htb.
app.inlanefreight.htb.  604800  IN      A       10.129.18.15
internal.inlanefreight.htb. 604800 IN   A       10.129.1.6
mail1.inlanefreight.htb. 604800 IN      A       10.129.18.201
ns.inlanefreight.htb.   604800  IN      A       10.129.34.136
inlanefreight.htb.      604800  IN      SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
;; Query time: 4 msec
;; SERVER: 10.129.14.128#53(10.129.14.128)
;; WHEN: So Sep 19 18:51:19 CEST 2021
;; XFR size: 9 records (messages 1, bytes 520)
```

Se o administrador usou uma sub-rede para o `allow-transfer` opção para fins de teste ou como solução alternativa ou configurá-la para `any`, todos consultariam o arquivo de zona inteiro no servidor DNS. Além disso, outras zonas podem ser consultadas, o que pode até mostrar endereços IP internos e nomes de host.

```shell-session
casluxd@htb[/htb]$ dig axfr internal.inlanefreight.htb @10.129.14.128

; <<>> DiG 9.16.1-Ubuntu <<>> axfr internal.inlanefreight.htb @10.129.14.128
;; global options: +cmd
internal.inlanefreight.htb. 604800 IN   SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
internal.inlanefreight.htb. 604800 IN   TXT     "MS=ms97310371"
internal.inlanefreight.htb. 604800 IN   TXT     "atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"
internal.inlanefreight.htb. 604800 IN   TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
internal.inlanefreight.htb. 604800 IN   NS      ns.inlanefreight.htb.
dc1.internal.inlanefreight.htb. 604800 IN A     10.129.34.16
dc2.internal.inlanefreight.htb. 604800 IN A     10.129.34.11
mail1.internal.inlanefreight.htb. 604800 IN A   10.129.18.200
ns.internal.inlanefreight.htb. 604800 IN A      10.129.34.136
vpn.internal.inlanefreight.htb. 604800 IN A     10.129.1.6
ws1.internal.inlanefreight.htb. 604800 IN A     10.129.1.34
ws2.internal.inlanefreight.htb. 604800 IN A     10.129.1.35
wsus.internal.inlanefreight.htb. 604800 IN A    10.129.18.2
internal.inlanefreight.htb. 604800 IN   SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
;; Query time: 0 msec
;; SERVER: 10.129.14.128#53(10.129.14.128)
;; WHEN: So Sep 19 18:53:11 CEST 2021
;; XFR size: 15 records (messages 1, bytes 664)
```

O indivíduo `A` registros com os nomes de host também podem ser descobertos com a ajuda de um ataque de força bruta. Para fazer isso, precisamos de uma lista de possíveis nomes de host, que usamos para enviar as solicitações em ordem. Essas listas são fornecidas, por exemplo, por [Listas de Sec](https://github.com/danielmiessler/SecLists/blob/master/Discovery/DNS/subdomains-top1million-5000.txt).

Uma opção seria executar um `for-loop` no Bash que lista essas entradas e envia a consulta correspondente ao servidor DNS desejado.

```shell-session
casluxd@htb[/htb]$ for sub in $(cat /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-110000.txt);do dig $sub.inlanefreight.htb @10.129.14.128 | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done

ns.inlanefreight.htb.   604800  IN      A       10.129.34.136
mail1.inlanefreight.htb. 604800 IN      A       10.129.18.201
app.inlanefreight.htb.  604800  IN      A       10.129.18.15
```

Muitas ferramentas diferentes podem ser usadas para isso, e a maioria delas funciona da mesma maneira. Uma dessas ferramentas é, por exemplo [DNSenum](https://github.com/fwaeytens/dnsenum).

```shell-session
casluxd@htb[/htb]$ dnsenum --dnsserver 10.129.14.128 --enum -p 0 -s 0 -o subdomains.txt -f /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-110000.txt inlanefreight.htb

dnsenum VERSION:1.2.6

-----   inlanefreight.htb   -----


Host's addresses:
__________________



Name Servers:
______________

ns.inlanefreight.htb.                    604800   IN    A        10.129.34.136


Mail (MX) Servers:
___________________



Trying Zone Transfers and getting Bind Versions:
_________________________________________________

unresolvable name: ns.inlanefreight.htb at /usr/bin/dnsenum line 900 thread 1.

Trying Zone Transfer for inlanefreight.htb on ns.inlanefreight.htb ...
AXFR record query failed: no nameservers


Brute forcing with /home/cry0l1t3/Pentesting/SecLists/Discovery/DNS/subdomains-top1million-110000.txt:
_______________________________________________________________________________________________________

ns.inlanefreight.htb.                    604800   IN    A        10.129.34.136
mail1.inlanefreight.htb.                 604800   IN    A        10.129.18.201
app.inlanefreight.htb.                   604800   IN    A        10.129.18.15
ns.inlanefreight.htb.                    604800   IN    A        10.129.34.136

...SNIP...
done.
```

## Tipos de consultas DNS

Existem vários tipos de consultas possíveis no DNS (Domain Name System). Cada tipo de consulta é usado para obter um tipo específico de informação relacionada a um domínio. Aqui estão os principais tipos de consultas DNS:

1. Consulta A (Address): É usada para obter o endereço IPv4 associado a um nome de domínio. Retorna o endereço IP correspondente ao domínio consultado.
2. Consulta AAAA: Similar à consulta A, mas retorna o endereço IPv6 correspondente ao domínio consultado. Essa consulta é usada para obter o endereço IPv6 de um domínio.
3. Consulta CNAME (Canonical Name): É usada para obter o nome canônico associado a um alias. Um registro CNAME é usado para redirecionar um nome de domínio para outro nome canônico.
4. Consulta MX (Mail Exchange): É usada para obter informações sobre os servidores de e-mail responsáveis por um domínio. Retorna o nome do servidor de e-mail preferencial e sua prioridade.
5. Consulta NS (Name Server): É usada para obter informações sobre os servidores de nomes autoritativos para um domínio. Retorna os servidores de nomes responsáveis por um domínio específico.
6. Consulta PTR (Pointer): É usada para obter o nome de domínio reverso associado a um endereço IP. Ela mapeia um endereço IP para um nome de domínio.
7. Consulta SOA (Start of Authority): É usada para obter informações sobre a autoridade de uma zona DNS específica. Retorna detalhes sobre o servidor de nomes primário, endereço de e-mail do administrador, número de série e outros parâmetros relacionados à zona DNS.
8. Consulta TXT: É usada para obter informações de texto associadas a um domínio. Pode ser utilizado para verificar registros SPF (Sender Policy Framework), registros DKIM (DomainKeys Identified Mail), entre outros.
9. Consulta SRV (Service): É usada para obter informações sobre serviços específicos disponíveis em um domínio. Retorna informações como o serviço, a porta, o protocolo, o peso e a prioridade do servidor que oferece o serviço.

## O que é uma zona DNS?

Uma zona DNS (Domain Name System) é uma porção do espaço de nomes de domínio da Internet que é gerenciada por um administrador específico. Ela é responsável por armazenar as informações de resolução de nomes para um domínio específico.

A zona DNS contém registros que associam nomes de domínio a endereços IP ou a outros recursos relacionados a um domínio, como servidores de e-mail (registros MX) e servidores de nomes (registros NS). Esses registros são usados para traduzir nomes de domínio legíveis para humanos em endereços IP numéricos que os computadores possam entender.

Uma zona DNS é tipicamente gerenciada por um servidor DNS autoritativo, que é responsável por responder às consultas de resolução de nomes para os domínios contidos na zona. O administrador da zona é responsável por atualizar e manter os registros dentro da zona, como adicionar, remover ou modificar registros conforme necessário.

Em resumo, uma zona DNS é uma área específica dentro do sistema de nomes de domínio que contém as informações necessárias para traduzir nomes de domínio em endereços IP e outros recursos relacionados.

## Diferença entre Name Server e zona DNS

Claro! Vou tentar explicar a diferença entre zona DNS e servidor de nomes de uma maneira simples para uma criança de 5 anos entender.

Imagine que você está brincando de enviar cartas para seus amigos. Você escreve as cartas e coloca em um envelope, mas antes de enviá-las, você precisa escrever o nome do destinatário e o endereço dele no envelope, para que o carteiro saiba para onde entregar a carta.

A zona DNS é como uma lista gigante de endereços. Ela contém todos os nomes de sites e seus endereços na internet. É como um mapa que ajuda os computadores a saberem onde encontrar os sites.

Agora, o servidor de nomes é como um carteiro. Ele é uma pessoa que ajuda a entregar as cartas. Quando você quer enviar uma carta para um amigo, você dá a carta para o carteiro e ele se encarrega de levá-la até o seu amigo.

No mundo da internet, os servidores de nomes são como os carteiro da internet. Quando você digita o nome de um site, como "[www.brincadeiraslegais.com](http://www.brincadeiraslegais.com/)", o servidor de nomes ajuda o seu computador a encontrar o endereço desse site na zona DNS. Ele olha no mapa (zona DNS) e encontra o endereço correto.

Então, a diferença é a seguinte: a zona DNS é como o mapa que tem todos os endereços dos sites, e o servidor de nomes é como o carteiro que ajuda a encontrar os endereços certos na zona DNS e entrega as informações ao seu computador.

Espero que isso tenha ajudado a entender a diferença entre zona DNS e servidor de nomes!

### Então na prática, ao realizar a consulta usando por exemplo, a ferramenta dig, "dig NS dominio", qual a resposta comum?

Ao realizar a consulta "dig NS dominio" utilizando a ferramenta `dig`, você está solicitando a obtenção dos registros NS (Name Server) de um determinado domínio. Esses registros NS especificam quais são os servidores de nomes autoritativos para o domínio consultado.

A resposta comum a essa consulta será uma lista de servidores de nomes autoritativos para o domínio. Por exemplo:

; <<>> DiG 9.16.1-Ubuntu <<>> NS exemplo.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 44810
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;exemplo.com.                   IN      NS

;; ANSWER SECTION:
exemplo.com.            86400   IN      NS      ns1.exemplo.com.
exemplo.com.            86400   IN      NS      ns2.exemplo.com.

;; Query time: 5 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Mon Aug 23 09:55:09 UTC 2021
;; MSG SIZE  rcvd: 83

Na resposta, você verá uma seção chamada "ANSWER SECTION" que contém os registros NS encontrados para o domínio consultado. Nesse exemplo, os servidores de nomes autoritativos para o domínio `exemplo.com` são `ns1.exemplo.com` e `ns2.exemplo.com`.

Esses registros NS indicam quais servidores de nomes você deve usar para obter informações sobre o domínio. Eles são importantes para ajudar a direcionar as consultas de DNS para os servidores corretos que possuem as informações atualizadas sobre o domínio.

### E o que eu poderia fazer com essa informação?

Com a informação dos registros NS obtidos através da consulta "dig NS dominio", você pode realizar diversas ações relacionadas ao domínio consultado. Aqui estão algumas possibilidades:

1. Identificar os servidores de nomes autoritativos: Os registros NS indicam quais são os servidores de nomes autoritativos para o domínio. Esses servidores são responsáveis por fornecer informações atualizadas sobre o domínio, como registros A, registros MX, registros TXT, entre outros. Saber quais são os servidores de nomes autoritativos é útil para entender quais são as fontes confiáveis de informações sobre o domínio.
2. Verificar a configuração de DNS: Ao obter os registros NS, você pode verificar se a configuração de DNS do domínio está correta. Por exemplo, você pode confirmar se os servidores de nomes listados são os esperados e se estão configurados corretamente. Isso é útil para solucionar problemas de resolução de DNS ou garantir que as configurações de DNS estejam de acordo com as melhores práticas.
3. Realizar consultas adicionais: Com os servidores de nomes autoritativos em mãos, você pode realizar consultas adicionais para esses servidores para obter informações mais detalhadas sobre o domínio. Por exemplo, você pode realizar uma consulta "dig A dominio" para obter os registros A (endereços IP) associados ao domínio, ou uma consulta "dig MX dominio" para obter os registros MX (servidores de email) do domínio.
4. Analisar a infraestrutura do domínio: Ao conhecer os servidores de nomes autoritativos de um domínio, você pode investigar mais sobre a infraestrutura desse domínio. Por exemplo, você pode pesquisar informações sobre esses servidores de nomes, como endereços IP, localização geográfica, informações de propriedade ou configurações adicionais. Isso pode ser útil para fins de segurança, análise de desempenho ou compreensão da infraestrutura de um domínio específico.

Essas são apenas algumas possibilidades do que você pode fazer com a informação dos registros NS. A consulta "dig NS" fornece um ponto de partida para explorar mais informações e entender melhor a estrutura e configuração de um domínio na internet.

## Todo domínio e subdomínio é considerado zona DNS?

Não necessariamente. Um domínio e um subdomínio podem ou não ser considerados uma zona DNS, dependendo da configuração e organização do sistema de DNS.

Em termos gerais, uma zona DNS é uma parte do espaço de nomes de domínio que é gerenciada por um conjunto específico de servidores de nomes. Uma zona DNS é tipicamente delimitada por uma autoridade de domínio e contém os registros DNS associados a esse domínio.

Normalmente, um domínio é considerado uma zona DNS quando possui um conjunto de servidores de nomes autoritativos dedicados a ele. Esses servidores de nomes são responsáveis por armazenar e fornecer os registros DNS (como registros A, registros MX, registros TXT, etc.) para aquele domínio específico.

Por outro lado, subdomínios podem ou não ser tratados como zonas DNS independentes. Em muitos casos, os subdomínios são gerenciados pelos mesmos servidores de nomes autoritativos que o domínio principal. Isso significa que os registros DNS para o subdomínio são mantidos e servidos pelos mesmos servidores de nomes que cuidam do domínio principal. Nesse caso, o subdomínio não é considerado uma zona DNS separada.

No entanto, em alguns casos, os subdomínios podem ser tratados como zonas DNS independentes, com seus próprios servidores de nomes autoritativos. Isso permite um maior controle e gerenciamento separado dos registros DNS do subdomínio. Nesse caso, o subdomínio seria considerado uma zona DNS distinta.

Em resumo, embora um domínio seja frequentemente considerado uma zona DNS, os subdomínios podem ser tratados como parte de uma zona maior ou como zonas independentes, dependendo da configuração do sistema de DNS utilizado.