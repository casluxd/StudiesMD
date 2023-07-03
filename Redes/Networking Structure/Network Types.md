
Cada rede é estruturada de forma diferente e pode ser configurada individualmente. Por esse motivo, foram desenvolvidos os chamados tipos e topologias que podem ser usados ​​para categorizar essas redes. Ao ler sobre todos os tipos de redes, pode haver um pouco de sobrecarga de informações, pois alguns tipos de rede incluem o alcance geográfico. Raramente ouvimos algumas das terminologias na prática, então esta seção será dividida em Termos Comuns e Termos do Livro. É bom saber os termos do livro, pois houve um único caso documentado de um servidor de e-mail que não conseguiu entregar e-mails com mais de 500 milhas, mas não se espera que seja capaz de recitá-los sob demanda, a menos que você esteja estudando para um exame de rede.

## Terminologias comuns

- Wide Area Network (WAN): Internet
- Local Area Network (LAN): Redes internas (Ex: Home ou Office)
- Wireless Local Area Network (WLAN): Redes internas acessíveis via Wi-Fi
- Virtual Private Network (VPN): Conecta vários sites de rede a uma LAN


## WAN

A WAN (Wide Area Network) é comumente chamada de Internet. Ao lidar com equipamentos de rede, geralmente temos um endereço WAN e um endereço LAN. A WAN é o endereço geralmente acessado pela Internet. Dito isto, não é inclusivo para a Internet; uma WAN é apenas um grande número de LANs unidas. Muitas grandes empresas ou agências governamentais terão uma "WAN Interna" (também chamada de Intranet, Rede Airgap, etc.). De um modo geral, a principal maneira de identificar se a rede é uma WAN é usar um protocolo de roteamento específico da WAN, como o BGP, e se o esquema de IP em uso não estiver dentro do RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

## LAN / WLAN

LANs (rede local) e WLANs (rede local sem fio) normalmente atribuirão endereços IP designados para uso local (RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16). Em alguns casos, como algumas faculdades ou hotéis, você pode receber um endereço IP roteável (internet) ao ingressar na LAN, mas isso é muito menos comum. Não há nada diferente entre uma LAN ou WLAN, exceto que as WLANs apresentam a capacidade de transmitir dados sem cabos. É principalmente uma designação de segurança.

## VPN

Existem três tipos principais de Redes Privadas Virtuais (VPN), mas todos os três têm o mesmo objetivo de fazer o usuário se sentir como se estivesse conectado a uma rede diferente.

- Site-to-Site VPN: Tanto o cliente quanto o servidor são dispositivos de rede, geralmente roteadores ou firewalls, e compartilham intervalos de rede inteiros. Isso é mais comumente usado para unir redes de empresas pela Internet, permitindo que vários locais se comuniquem pela Internet como se fossem locais.
- Remote Access VPN: Isso envolve o computador do cliente criando uma interface virtual que se comporta como se estivesse na rede de um cliente. Hack The Box utiliza OpenVPN, que faz um adaptador TUN que nos permite acessar os laboratórios. Ao analisar essas VPNs, uma peça importante a considerar é a tabela de roteamento criada ao ingressar na VPN. Se a VPN criar apenas rotas para redes específicas (ex: 10.10.10.0/24), isso é chamado de VPN Split-Tunnel, o que significa que a conexão com a Internet não sai da VPN. Isso é ótimo para o Hack The Box porque fornece acesso ao laboratório sem a preocupação com a privacidade de monitorar sua conexão com a Internet. No entanto, para uma empresa, as VPNs de túnel dividido normalmente não são ideais porque, se a máquina estiver infectada com malware, os métodos de detecção baseados em rede provavelmente não funcionarão, pois o tráfego sai da Internet.
- SSL VPN: Esta é essencialmente uma VPN que é feita em nosso navegador da web e está se tornando cada vez mais comum à medida que os navegadores da web se tornam capazes de fazer qualquer coisa. Normalmente, eles transmitirão aplicativos ou sessões inteiras da área de trabalho para o seu navegador da web. Um ótimo exemplo disso seria o HackTheBox Pwnbox.

# Book Terms

- Global Area Network (GAN): Rede global (Internet)
- Metropolitan Area Network (MAN): Rede regional (múltiplas LANs)
- Wireless Personal Area Network (WPAN): Rede pessoal (Bluetooth)


### GAN

Uma rede mundial como a Internet é conhecida como Global Area Network (GAN). No entanto, a Internet não é a única rede de computadores desse tipo. As empresas ativas internacionalmente também mantêm redes isoladas que abrangem várias WANs e conectam computadores da empresa em todo o mundo. As GANs usam a infraestrutura de fibras de vidro de redes de longa distância e as interconectam por cabos submarinos internacionais ou transmissão por satélite.

### MAN

Metropolitan Area Network (MAN) é uma rede de telecomunicações de banda larga que conecta várias LANs em proximidade geográfica. Regra geral, trata-se de sucursais individuais de uma empresa ligadas a um MAN através de linhas alugadas. Roteadores de alto desempenho e conexões de alto desempenho baseadas em fibras de vidro são usados, que permitem uma taxa de transferência de dados significativamente maior do que a Internet. A velocidade de transmissão entre dois nós remotos é comparável à comunicação dentro de uma LAN.

As operadoras de rede que operam internacionalmente fornecem a infraestrutura para MANs. Cidades cabeadas como Redes de Área Metropolitana podem ser integradas supra-regionalmente em Redes de Área Ampla (WAN) e internacionalmente em Redes de Área Global (GAN).

### PAN/WPAN

Dispositivos finais modernos, como smartphones, tablets, laptops ou computadores de mesa, podem ser conectados ad hoc para formar uma rede para permitir a troca de dados. Isso pode ser feito por cabo na forma de uma rede de área pessoal (PAN).

A variante wireless Wireless Personal Area Network (WPAN) é baseada em tecnologias Bluetooth ou Wireless USB. Uma rede de área pessoal sem fio estabelecida via Bluetooth é chamada de Piconet. PANs e WPANs geralmente se estendem por apenas alguns metros e, portanto, não são adequados para conectar dispositivos em salas separadas ou mesmo em edifícios.

No contexto da Internet das Coisas (IoT), as WPANs são utilizadas para comunicar aplicações de controle e monitoramento com baixas taxas de dados. Protocolos como Insteon, Z-Wave e ZigBee foram explicitamente projetados para casas inteligentes e automação residencial.