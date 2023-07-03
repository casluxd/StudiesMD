Podemos considerar [WHOIS](https://en.wikipedia.org/wiki/WHOIS) como as "páginas brancas" para nomes de domínio. É um protocolo de consulta / resposta orientado a transações baseado em TCP que escuta na porta TCP 43 por padrão. Podemos usá-lo para consultar bancos de dados que contêm nomes de domínio, endereços IP ou sistemas autônomos e fornecer serviços de informações aos usuários da Internet. O protocolo é definido em [RFC 3912](https://datatracker.ietf.org/doc/html/rfc3912). O primeiro diretório WHOIS foi criado no início dos anos 70 por [Elizabeth Feinler](https://en.wikipedia.org/wiki/Elizabeth_J._Feinler) e sua equipe trabalhando no Centro de Informações da Rede da Universidade de Stanford ( NIC ). Juntamente com sua equipe, eles criaram domínios divididos em categorias com base no endereço físico de um computador. Podemos ler mais sobre a fascinante história do WHOIS [aqui](https://en.wikipedia.org/wiki/WHOIS#History).

As pesquisas de domínio WHOIS nos permitem recuperar informações sobre o nome de domínio de um domínio já registrado. O [Corporação da Internet de Nomes e Números Atribuídos](https://www.icann.org/get-started) (`ICANN`) exige que os registradores credenciados insiram as informações de contato do titular, as datas de criação e expiração do domínio e outras informações no banco de dados Whois imediatamente após o registro de um domínio. Em termos simples, o banco de dados Whois é uma lista pesquisável de todos os domínios atualmente registrados em todo o mundo.

As pesquisas do WHOIS foram realizadas inicialmente usando ferramentas de linha de comando. Atualmente, existem muitas ferramentas baseadas na Web, mas as opções de linha de comando geralmente nos dão mais controle sobre nossas consultas e ajudam a filtrar e classificar a saída resultante. [WHOIS Sysinternals](https://docs.microsoft.com/en-gb/sysinternals/downloads/whois) para Windows ou Linux [WHOIS](https://linux.die.net/man/1/whois) utilitário de linha de comando são nossas ferramentas preferidas para coletar informações. No entanto, existem algumas versões online como [whois.domaintools.com](https://whois.domaintools.com/) nós também podemos usar.

Obteríamos a seguinte resposta do comando anterior para executar um `whois` olhar contra o `facebook.com` domínio. Um exemplo disso `whois` comando é:

```shell-session
casluxd@htb[/htb]$ export TARGET="facebook.com" # Assign our target to an environment variable
casluxd@htb[/htb]$ whois $TARGET

Domain Name: FACEBOOK.COM
Registry Domain ID: 2320948_DOMAIN_COM-VRSN
Registrar WHOIS Server: whois.registrarsafe.com
Registrar URL: https://www.registrarsafe.com
Updated Date: 2021-09-22T19:33:41Z
Creation Date: 1997-03-29T05:00:00Z
Registrar Registration Expiration Date: 2030-03-30T04:00:00Z
Registrar: RegistrarSafe, LLC
Registrar IANA ID: 3237
Registrar Abuse Contact Email: abusecomplaints@registrarsafe.com
Registrar Abuse Contact Phone: +1.6503087004
Domain Status: clientDeleteProhibited https://www.icann.org/epp#clientDeleteProhibited
Domain Status: clientTransferProhibited https://www.icann.org/epp#clientTransferProhibited
Domain Status: clientUpdateProhibited https://www.icann.org/epp#clientUpdateProhibited
Domain Status: serverDeleteProhibited https://www.icann.org/epp#serverDeleteProhibited
Domain Status: serverTransferProhibited https://www.icann.org/epp#serverTransferProhibited
Domain Status: serverUpdateProhibited https://www.icann.org/epp#serverUpdateProhibited
Registry Registrant ID:
Registrant Name: Domain Admin
Registrant Organization: Facebook, Inc.
Registrant Street: 1601 Willow Rd
Registrant City: Menlo Park
Registrant State/Province: CA
Registrant Postal Code: 94025
Registrant Country: US
Registrant Phone: +1.6505434800
Registrant Phone Ext:
Registrant Fax: +1.6505434800
Registrant Fax Ext:
Registrant Email: domain@fb.com
Registry Admin ID:
Admin Name: Domain Admin
Admin Organization: Facebook, Inc.
Admin Street: 1601 Willow Rd
Admin City: Menlo Park
Admin State/Province: CA
Admin Postal Code: 94025
Admin Country: US
Admin Phone: +1.6505434800
Admin Phone Ext:
Admin Fax: +1.6505434800
Admin Fax Ext:
Admin Email: domain@fb.com
Registry Tech ID:
Tech Name: Domain Admin
Tech Organization: Facebook, Inc.
Tech Street: 1601 Willow Rd
Tech City: Menlo Park
Tech State/Province: CA
Tech Postal Code: 94025
Tech Country: US
Tech Phone: +1.6505434800
Tech Phone Ext:
Tech Fax: +1.6505434800
Tech Fax Ext:
Tech Email: domain@fb.com
Name Server: C.NS.FACEBOOK.COM
Name Server: B.NS.FACEBOOK.COM
Name Server: A.NS.FACEBOOK.COM
Name Server: D.NS.FACEBOOK.COM
DNSSEC: unsigned

<SNIP>
```

Podemos reunir os mesmos dados usando `whois.exe` do Windows Sysinternals:

```cmd-session
C:\htb> whois.exe facebook.com

Whois v1.21 - Domain information lookup
Copyright (C) 2005-2019 Mark Russinovich
Sysinternals - www.sysinternals.com

Connecting to COM.whois-servers.net...

WHOIS Server: whois.registrarsafe.com
   Registrar URL: http://www.registrarsafe.com
   Updated Date: 2021-09-22T19:33:41Z
   Creation Date: 1997-03-29T05:00:00Z
   Registry Expiry Date: 2030-03-30T04:00:00Z
   Registrar: RegistrarSafe, LLC
   Registrar IANA ID: 3237
   Registrar Abuse Contact Email: abusecomplaints@registrarsafe.com
   Registrar Abuse Contact Phone: +1-650-308-7004
   Domain Status: clientDeleteProhibited https://icann.org/epp#clientDeleteProhibited
   Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
   Domain Status: clientUpdateProhibited https://icann.org/epp#clientUpdateProhibited
   Domain Status: serverDeleteProhibited https://icann.org/epp#serverDeleteProhibited
   Domain Status: serverTransferProhibited https://icann.org/epp#serverTransferProhibited
   Domain Status: serverUpdateProhibited https://icann.org/epp#serverUpdateProhibited
   Name Server: A.NS.FACEBOOK.COM
   Name Server: B.NS.FACEBOOK.COM
   Name Server: C.NS.FACEBOOK.COM
   Name Server: D.NS.FACEBOOK.COM
   DNSSEC: unsigned
   URL of the ICANN Whois Inaccuracy Complaint Form: https://www.icann.org/wicf/
>>> Last update of whois database: 2021-10-11T06:03:10Z <<<

<SNIP>
```

