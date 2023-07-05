Laudanum é um repositório de arquivos prontos que podem ser usados para injetar na vítima e receber acesso de volta por meio de um shell reverso, execute comandos no host da vítima diretamente do navegador e muito mais. O repo inclui arquivos injetáveis para muitos idiomas diferentes de aplicativos da web para incluir `asp, aspx, jsp, php,` e mais. Este é um item básico para qualquer pentest. Se você estiver usando sua própria VM, o Laudanum será incorporado ao Parrot OS e ao Kali por padrão. Para qualquer outra distribuição, você provavelmente precisará puxar uma cópia para baixo para usar. Você pode obtê-lo [aqui](https://github.com/jbarcia/Web-Shells/tree/master/laudanum). Vamos examinar Laudanum e ver como ele funciona.

## Trabalhando com Laudanum

Os arquivos Laudanum podem ser encontrados no diretório `/usr/share/webshells/laudanum` . Para a maioria dos arquivos no Laudanum, você pode copiá-los como estão e colocá-los onde precisar na vítima para executar. Para arquivos específicos, como os shells, você deve editar o arquivo primeiro para inserir seu `attacking` host endereço IP para garantir que você possa acessar o shell da Web ou receber um retorno de chamada na instância em que você usa um shell reverso. Antes de usar os diferentes arquivos, leia o conteúdo e os comentários para garantir que você tome as ações adequadas.

## Demonstração de Laudanum

Agora que entendemos o que é o Laudanum e como ele funciona, vejamos um aplicativo da web que encontramos em nosso ambiente de laboratório e ver se podemos executar um shell da web. Se você deseja acompanhar esta demonstração, precisará adicionar uma entrada à sua `/etc/hosts` arquivo no seu ataque VM ou no Pwnbox para o host que estamos atacando. Essa entrada deve ler-se: `<target ip> status.inlanefreight.local`. Feito isso, você pode reproduzir e explorar esta demonstração enquanto estiver na VPN ou usando o Pwnbox.

#### Mover uma cópia para modificação

```shell-session
casluxd@htb[/htb]$ cp /usr/share/webshells/laudanum/aspx/shell.aspx /home/tester/demo.aspx
```

Adicione seu endereço IP a variável `allowedIps` na linha `59`. Faça outras alterações que desejar. Pode ser prudente remover a arte ASCII e os comentários do arquivo. Esses itens em uma carga útil geralmente são sinalizados e podem alertar os defensores / AV sobre o que você está fazendo.

![[Pasted image 20230705161538.png]]

Estamos aproveitando a função de upload na parte inferior da página de status (`Green Arrow`) para que isso funcione. Selecione seu arquivo de shell e pressione upload. Se for bem-sucedido, ele deve imprimir o caminho para onde o arquivo foi salvo ( Seta Amarela ). Use a função de upload. O sucesso imprime para onde o arquivo foi, navegue até ele.

![[Pasted image 20230705161551.png]]

Depois que o upload for bem-sucedido, você precisará navegar até o shell da web para utilizar suas funções. A imagem abaixo mostra como fazê-lo. Como visto na última imagem, nosso shell foi carregado no `\\files\` diretório e o nome foi mantido o mesmo. Nem sempre será esse o caso. Você pode executar algumas implementações que randomizam os nomes de arquivos no upload que não possuem um diretório de arquivos públicos ou qualquer outro número de possíveis salvaguardas. Por enquanto, temos sorte que não é o caso. Com esse aplicativo da web em particular, nosso arquivo foi para `status.inlanefreight.local\\files\demo.aspx` e exigirá que procuremos o upload usando esse \ no caminho, em vez do normal / like. Depois de fazer isso, seu navegador o limpará na janela do URL para aparecer como `status.inlanefreight.local//files/demo.aspx`.

#### Navegue até o nosso shell

![[Pasted image 20230705161602.png]]

Agora podemos utilizar o shell Laudanum que carregamos para emitir comandos para o host. Podemos ver no exemplo que o `systeminfo` comando foi executado.

![[Pasted image 20230705161610.png]]

