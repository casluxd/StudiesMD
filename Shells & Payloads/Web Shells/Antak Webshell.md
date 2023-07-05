## ASPX e uma dica de aprendizado rápido

Antes de mergulhar nos conceitos e exercícios de shell de aspx, devemos reservar um tempo para cobrir um recurso de aprendizado que possa ajudar a reforçar a maioria dos conceitos abordados. Ocasionalmente, pode ser um desafio visualizar um conceito usando apenas um método de aprendizado. É bom complementar a leitura assistindo a demonstrações e realizando ações práticas, como temos feito até agora. Os passeios em vídeo podem ser uma maneira incrível de aprender conceitos, além de poderem ser consumidos casualmente ( comendo almoço, deitados na cama, sentados no sofá, etc. ). Um grande recurso a ser usado no aprendizado é `IPPSEC's`  [ippsec.rocks](https://ippsec.rocks/?#). O site é uma poderosa ferramenta de aprendizado. Tomemos, por exemplo, o conceito de shells da web. Podemos usar o site dele para digitar o conceito que queremos aprender, como o aspx.

Seu site rastreia as descrições de cada um dos vídeos que ele postou no YouTube e recomenda um carimbo de data / hora associado a essa palavra-chave. Quando clicamos em um dos links, ele nos leva à seção do vídeo em que esse conceito é demonstrado. É como um mecanismo de pesquisa para aprender habilidades de hackers. Para obter um bom entendimento básico do que é um shell da web aspx, vamos assistir à pequena parte da demonstração da caixa aposentada pelo IPPSEC [Cereal](https://www.youtube.com/watch?v=04ZBIioD5pA&t=4677s). O link deve começar com 1 hora e 17 minutos. Assista de 1 hora e 17 minutos até a marca de 1 hora e 20 minutos.

Percederemos que ele carregou o arquivo via FTP e depois navegou para o arquivo usando o navegador da web. Isso lhe deu a capacidade de enviar comandos e receber saída do sistema operacional Windows subjacente.

## ASPX explicado

`Active Server Page Extended` (`ASPX`) é um tipo / extensão de arquivo gravado para [ASP.NET Framework da Microsoft](https://docs.microsoft.com/en-us/aspnet/overview). Em um servidor da Web executando a estrutura ASP.NET, páginas de formulários da Web podem ser geradas para os usuários inserirem dados. No lado do servidor, as informações serão convertidas em HTML. Podemos tirar proveito disso usando um shell da Web baseado em ASPX para controlar o sistema operacional Windows subjacente. Vamos testemunhar isso em primeira mão, utilizando o Antak Webshell.

## Antak Webshell

Antak é um shell da web incorporado ao ASP.Net incluído no [Projeto Nishang](https://github.com/samratashok/nishang). Nishang é um conjunto de ferramentas ofensivo do PowerShell que pode fornecer opções para qualquer parte do seu pentest. Como estamos focados em aplicativos da web no momento, vamos manter os olhos `Antak`. A Antak utiliza o PowerShell para interagir com o host, tornando-o ótimo para adquirir um shell da Web em um servidor Windows. A interface do usuário tem até o tema como PowerShell. É hora de mergulhar e experimentar Antak.

## Trabalhando com Antak

Os arquivos Antak podem ser encontrados no diretório `/usr/share/nishang/Antak-WebShell`.

```shell-session
casluxd@htb[/htb]$ ls /usr/share/nishang/Antak-WebShell

antak.aspx  Readme.md
```

O shell da web Antak funciona como um console Powershell. No entanto, ele executará cada comando como um novo processo. Ele também pode executar scripts na memória e codificar comandos que você envia. Como um shell da web, Antak é uma ferramenta bastante poderosa.

## Demonstração Antak

Agora que entendemos o que é o Antak e como ele funciona, vamos testá-lo contra o mesmo aplicativo da web da seção Laudanum. Se você deseja acompanhar esta demonstração, precisará adicionar uma entrada à sua `/etc/hosts` arquivo no seu ataque VM ou no Pwnbox para o host que estamos atacando. Essa entrada deve ler-se: `<target ip> status.inlanefreight.local`. Feito isso, desde que você esteja na VPN ou usando o Pwnbox, também poderá reproduzir e explorar esta demonstração.

#### Mover uma cópia para modificação

```shell-session
casluxd@htb[/htb]$ cp /usr/share/nishang/Antak-WebShell/antak.aspx /home/administrator/Upload.aspx
```

Certifique-se de definir credenciais para acesso ao shell da web. Modificar `line 14`, adicionando uma seta verde ( do usuário ) e senha ( seta laranja ). Isso entra em jogo quando você navega no seu shell da web, assim como o Laudanum. Isso pode ajudar a tornar suas operações mais seguras, garantindo que pessoas aleatórias não possam simplesmente tropeçar no uso do shell. Pode ser prudente remover a arte ASCII e os comentários do arquivo. Esses itens em uma carga útil geralmente são sinalizados e podem alertar os defensores / AV sobre o que você está fazendo.

#### Modifique o Shell para uso

![[Pasted image 20230705165618.png]]

Para demonstrar a ferramenta, estamos carregando-a no mesmo portal de status que usamos para o Laudanum. Esse host era um host do Windows, portanto nosso shell deve funcionar muito bem com o PowerShell. Carregue o arquivo e navegue até a página para uso. Ele fornecerá um prompt de usuário e senha. Lembre-se, com este aplicativo da web, os arquivos são armazenados no `\\files\` diretório. Quando você navega para o `upload.aspx` arquivo, você deve ver um prompt como temos abaixo.

![[Pasted image 20230705165626.png]]

Como visto na imagem a seguir, teremos acesso se nossas credenciais forem inseridas corretamente.

![[Pasted image 20230705165632.png]]

Agora que temos acesso, podemos utilizar os comandos do PowerShell para navegar e executar ações contra o host. Podemos emitir comandos básicos da janela do shell Antak, fazer upload e baixar arquivos, codificar e executar scripts e muito mais ( seta verde abaixo ). Essa é uma excelente maneira de utilizar uma Webshell para nos fornecer um retorno de chamada para nossa plataforma de comando e controle. Poderíamos fazer upload da carga útil através da função Upload ou usar um one-liner do PowerShell para baixar e executar o shell para nós. Se você não tiver certeza de onde começar, emita o comando `help` na janela de prompt ( seta laranja ) abaixo.

#### Comandos Emissores

![[Pasted image 20230705165644.png]]

