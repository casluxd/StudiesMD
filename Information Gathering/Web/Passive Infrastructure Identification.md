[Netcraft](https://www.netcraft.com/) pode nos oferecer informações sobre os servidores sem sequer interagir com eles, e isso é algo valioso do ponto de vista da coleta passiva de informações. Podemos usar o serviço visitando `https://sitereport.netcraft.com` e entrando no domínio de destino.

![[Pasted image 20230629220511.png]]

Alguns detalhes interessantes que podemos observar no relatório são:

- Background: Informações gerais sobre o domínio, incluindo a data em que foi vista pela primeira vez pelos rastreadores da Netcraft.
- Network: Informações sobre o proprietário do netblock, empresa de hospedagem, servidores de nomes etc.
- Hosting History: IPs mais recentes usados, servidor da web e sistema operacional de destino.

Precisamos prestar atenção especial aos IPs mais recentes usados. Às vezes, podemos detectar o endereço IP real do servidor da web antes de ser colocado atrás de um balanceador de carga, firewall de aplicativo da web ou IDS, permitindo que nos conectemos diretamente a ele, se a configuração permitir. Esse tipo de tecnologia pode interferir ou alterar nossas atividades de teste futuras.

## Wayback Machine

O [Arquivo da Internet](https://en.wikipedia.org/wiki/Internet_Archive) é uma biblioteca digital americana que fornece acesso público gratuito a materiais digitalizados, incluindo sites, coletados automaticamente por meio de rastreadores da web.

Podemos acessar várias versões desses sites usando o [Wayback Machine](http://web.archive.org/) para encontrar versões antigas que possam ter comentários interessantes no código fonte ou nos arquivos que não deveriam estar lá. Essa ferramenta pode ser usada para encontrar versões mais antigas de um site em um momento. Vamos pegar um site executando o WordPress, por exemplo. Podemos não encontrar nada de interessante ao avaliá-lo usando métodos manuais e ferramentas automatizadas, então, procuramos por ele usando o Wayback Machine e encontramos uma versão que utiliza um plug-in específico ( agora vulnerável ). Voltando à versão atual do site, descobrimos que o plug-in não foi removido corretamente e ainda pode ser acessado através do `wp-content` diretório. Podemos então utilizá-lo para obter a execução remota de código no host e uma boa recompensa.

![[Pasted image 20230629220632.png]]

Podemos verificar uma das primeiras versões do `facebook.com` capturado em 1o de dezembro de 2005, o que é interessante, talvez nos dê uma sensação de nostalgia, mas também é extremamente útil para nós como pesquisadores de segurança.

![[Pasted image 20230629220716.png]]

Também podemos usar a ferramenta [waybackurls](https://github.com/tomnomnom/waybackurls) para inspecionar URLs salvos pelo Wayback Machine e procurar palavras-chave específicas. Desde que tenhamos `Go` configurado corretamente em nosso host, podemos instalar a ferramenta da seguinte maneira:

```shell-session
casluxd@htb[/htb]$ go install github.com/tomnomnom/waybackurls@latest
```

Para obter uma lista de URLs rastreados de um domínio com a data em que foi obtido, podemos adicionar o `-dates` mude para o nosso comando da seguinte maneira:

```shell-session
casluxd@htb[/htb]$ waybackurls -dates https://facebook.com > waybackurls.txt
casluxd@htb[/htb]$ cat waybackurls.txt

2018-05-20T09:46:07Z http://www.facebook.com./
2018-05-20T10:07:12Z https://www.facebook.com/
2018-05-20T10:18:51Z http://www.facebook.com/#!/pages/Welcome-Baby/143392015698061?ref=tsrobots.txt
2018-05-20T10:19:19Z http://www.facebook.com/
2018-05-20T16:00:13Z http://facebook.com
2018-05-21T22:12:55Z https://www.facebook.com
2018-05-22T15:14:09Z http://www.facebook.com
2018-05-22T17:34:48Z http://www.facebook.com/#!/Syerah?v=info&ref=profile/robots.txt
2018-05-23T11:03:47Z http://www.facebook.com/#!/Bin595

<SNIP>
```

Se quisermos acessar um recurso específico, precisamos colocar o URL no menu de pesquisa e navegar até a data em que o instantâneo foi criado. Como mencionado anteriormente, a Wayback Machine pode ser uma ferramenta útil e não deve ser negligenciada. É muito provável que isso nos leve a descobrir ativos, páginas etc. esquecidos, o que pode levar à descoberta de uma falha.
