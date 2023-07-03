
Os desenvolvedores do projeto incluíram muitos recursos diferentes, desde o rastreamento de conversas TCP até a quebra de credenciais wireless. A inclusão de muitos plugins diferentes torna o Wireshark uma das melhores ferramentas de análise de tráfego.

# Plugins

Os radiais de análise e estatísticas fornecem uma infinidade de plug-ins para serem executados na captura. 

## As guias Statistics e Analyze

As guias Estatísticas e Analisar podem nos fornecer uma grande percepção dos dados que estamos examinando. A partir desses pontos, podemos utilizar muitos dos plug-ins integrados que o Wireshark tem a oferecer.

Os plug-ins aqui podem nos fornecer relatórios detalhados sobre o tráfego de rede que está sendo utilizado. Ele pode nos mostrar tudo, desde os principais palestrantes em nosso ambiente até conversas específicas e até detalhamento por IP e protocolo.

![[Pasted image 20230530210307.png]]

## Analyze

Na guia Analisar, podemos utilizar plug-ins que nos permitem fazer coisas como seguir fluxos TCP, filtrar tipos de conversa, preparar novos filtros de pacotes e examinar as informações especializadas que o Wireshark gera sobre o tráfego.

![[Pasted image 20230530210330.png]]

### Following TCP Streams

O Wireshark pode costurar pacotes TCP juntos para recriar todo o fluxo em um formato legível. Essa capacidade também nos permite extrair dados (imagens, arquivos, etc.) da captura. Isso funciona para quase todos os protocolos que utilizam o TCP como mecanismo de transporte.

Para utilizar esta feature:
- clique com o botão direito do mouse em um pacote do fluxo que desejamos recriar.
- selecione follow -> TCP
- isso abrirá uma nova janela com o fluxo costurado novamente. A partir daqui, podemos ver toda a conversa.

![image](https://academy.hackthebox.com/storage/modules/81/follow-tcp.gif)


Como alternativa, podemos utilizar o filtro `tcp.stream eq #` para localizar e rastrear conversas capturadas no arquivo pcap.

## Filtrando por uma TCP Stream específica

![[Pasted image 20230530210819.png]]

Observe que os três primeiros pacotes na imagem acima têm um handshake TCP completo. Seguindo esses pacotes, podemos ver o fluxo de transferência de dados. Limpamos qualquer coisa não relacionada fora de vista, utilizando o filtro, e agora podemos ver a conversa em ordem.

## Extraindo dados e arquivos da captura

O Wireshark pode recuperar muitos tipos diferentes de dados de streams. Requer que você tenha capturado toda a conversa. Caso contrário, esta habilidade falhará em juntar um datagrama incompleto. Se quisermos uma compreensão mais aprofundada de como esse recurso funciona, confira o Módulo Networking 101 ou pesquise a fragmentação TCP/IP.

Para extrair arquivos de um stream:
- Pare a captura
- Selecione File -> Export -> selecione o formato do protocolo para extrair(DICOM, HTTP, SMB, etc)

![image](https://academy.hackthebox.com/storage/modules/81/extract-http.gif)

Outra maneira interessante de obter dados do arquivo pcap vem do FTP. O File Transfer Protocol move os dados entre um servidor e um host para retirá-los dos bytes brutos e reconstruir o arquivo. (imagem, documentos de texto, etc.) O FTP utiliza o TCP como protocolo de transporte e usa as portas 20 e 21 para funcionar. A porta TCP 20 é usada para transferir dados entre o servidor e o host, enquanto a porta 21 é usada como porta de controle FTP. Quaisquer comandos, como login, listagem de arquivos e emissão de download ou upload, acontecem por essa porta. Para fazer isso, precisamos olhar para os diferentes filtros de exibição de FTP no Wireshark. Uma lista completa deles pode ser encontrada aqui(https://www.wireshark.org/docs/dfref/f/ftp.html). Por enquanto, veremos três:

![[Pasted image 20230530211146.png]]

![[Pasted image 20230530211152.png]]

![[Pasted image 20230530211157.png]]

Como o FTP utiliza o TCP como seu mecanismo de transporte, podemos utilizar a função follow tcp stream que utilizamos anteriormente na seção para agrupar qualquer conversa que desejamos explorar. As etapas básicas para dissecar dados FTP de um pcap são as seguintes:

1. Identifique qualquer tráfego FTP usando o filtro de exibição ftp.
2. Observe os controles de comando enviados entre o servidor e os hosts para determinar se algo foi transferido e quem o fez com o filtro ftp.request.command.
3. Escolha um arquivo e, em seguida, filtre por ftp-data. Selecione um pacote que corresponda ao nosso arquivo de interesse e siga o fluxo TCP correspondente a ele.
4. Depois de concluído, altere "Mostrar e salvar dados como" para "Raw" e salve o conteúdo como o nome do arquivo original.
5. Valide a extração verificando o tipo de arquivo.

