Rastejar um site é o processo sistemático ou automático de explorar um site para listar todos os recursos encontrados ao longo do caminho. Ele nos mostra a estrutura do site que estamos auditando e uma visão geral da superfície de ataque que testaremos no futuro. Usamos o processo de rastreamento para encontrar o maior número possível de páginas e subdiretórios pertencentes a um site.

## ZAP

Zed Attack Proxy é um proxy da web de código aberto que pertence ao [Projeto de segurança de aplicativos da Web aberta](https://owasp.org/) (`OWASP`). Ele nos permite realizar testes de segurança manuais e automatizados em aplicativos da web. Usá-lo como um servidor proxy nos permitirá interceptar e manipular todo o tráfego que passa por ele.

Podemos usar a funcionalidade de spidering seguindo as próximas etapas. Abra o ZAP e, no canto superior direito, abra o navegador.

![[Pasted image 20230702194346.png]]

Escreva o site na barra de endereços e adicione-o ao escopo usando a primeira entrada no menu esquerdo.

![[Pasted image 20230702194355.png]]

Volte para a janela ZAP, clique com o botão direito do mouse no site de destino, clique no menu Ataque e, em seguida, no submenu Spider.

![[Pasted image 20230702194403.png]]

Depois que o processo terminar, podemos ver os recursos descobertos pelo processo de spidering.

![[Pasted image 20230702194410.png]]

Um recurso útil do ZAP é o Fuzzer embutido e o Editor de solicitações manuais. Podemos enviar qualquer solicitação para alterá-lo manualmente ou preenchê-lo com uma lista de cargas úteis clicando com o botão direito do mouse na solicitação e usando o menu "Abrir / Enviar com o Editor de solicitações..." ou o submenu "Fuzz ..." no menu Ataque.

![[Pasted image 20230702194423.png]]

![[Pasted image 20230702194428.png]]

ZAP tem excelente [documentação](https://www.zaproxy.org/docs/desktop/start/) isso pode ajudá-lo a se acostumar rapidamente.

## FFuF

O módulo de spidering ZAP enumera apenas os recursos encontrados em links e formulários, mas pode perder informações importantes, como pastas ocultas ou arquivos de backup.

Nós podemos usar [ffuf](https://github.com/ffuf/ffuf) descobrir arquivos e pastas que não podemos detectar simplesmente navegando no site. Tudo o que precisamos fazer é lançar `ffuf` com uma lista de nomes de pastas e instrua-a a procurar recursivamente através deles.

```shell-session
casluxd@htb[/htb]$ ffuf -recursion -recursion-depth 1 -u http://192.168.10.10/FUZZ -w /opt/useful/SecLists/Discovery/Web-Content/raft-small-directories-lowercase.txt

        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://192.168.10.10/FUZZ
 :: Wordlist         : FUZZ: /opt/useful/SecLists/Discovery/Web-Content/raft-small-directories-lowercase.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
________________________________________________

wp-admin                [Status: 301, Size: 317, Words: 20, Lines: 10]
[INFO] Adding a new job to the queue: http://192.168.10.10/wp-admin/FUZZ

wp-includes             [Status: 301, Size: 320, Words: 20, Lines: 10]
[INFO] Adding a new job to the queue: http://192.168.10.10/wp-includes/FUZZ

wp-content              [Status: 301, Size: 319, Words: 20, Lines: 10]
[INFO] Adding a new job to the queue: http://192.168.10.10/wp-content/FUZZ

admin                   [Status: 302, Size: 0, Words: 1, Lines: 1]
login                   [Status: 302, Size: 0, Words: 1, Lines: 1]
feed                    [Status: 301, Size: 0, Words: 1, Lines: 1]
[INFO] Adding a new job to the queue: http://192.168.10.10/feed/FUZZ
...
```

- `-recursion`: Ativa a verificação recursiva.
- `-recursion-depth`: Especifica a profundidade máxima a ser digitalizada.
- `-u`: Nosso URL de destino e `FUZZ` será o ponto de injeção.
- `-w`: Caminho para a nossa lista de palavras.

Podemos ver na imagem como `ffuf` cria novos trabalhos para cada pasta detectada. Essa tarefa pode ser muito intensiva em recursos para o servidor de destino. Se o site responder mais devagar do que o normal, podemos diminuir a taxa de solicitações usando o `-rate` parâmetro.

## Divulgação de informações sensíveis

É típico que o servidor da web e o aplicativo da web manipulem os arquivos necessários para funcionar. No entanto, é comum encontrar arquivos de backup ou não referenciados que possam ter informações ou credenciais importantes. Arquivos de backup ou não referenciados podem ser gerados criando instantâneos, versões diferentes de um arquivo ou de um editor de texto sem o conhecimento do desenvolvedor da web. Existem algumas listas de extensões comuns que podemos encontrar no `raft-[ small | medium | large ]-extensions.txt` arquivos de [Listas de Sec](https://github.com/danielmiessler/SecLists/tree/master/Discovery/Web-Content).

Combinaremos algumas das pastas que encontramos antes, uma lista de extensões comuns, e algumas palavras extraídas do site para ver se conseguimos encontrar algo que não deveria estar lá. O primeiro passo será criar um arquivo com os seguintes nomes de pastas e salvá-lo como `folders.txt`.

```shell-session
wp-admin
wp-content
wp-includes
```

Em seguida, extrairemos algumas palavras-chave do site usando [CeWL](https://github.com/digininja/CeWL). Instruiremos a ferramenta para extrair palavras com um comprimento mínimo de 5 caracteres `-m5`, converta-os em minúsculas `--lowercase` e salve-os em um arquivo chamado wordlist.txt `-w <FILE>`:

```shell-session
casluxd@htb[/htb]$ cewl -m5 --lowercase -w wordlist.txt http://192.168.10.10
```

O próximo passo será combinar tudo no ffuf para ver se conseguimos encontrar informações interessantes. Para isso, usaremos os seguintes parâmetros em `ffuf`:

- `-w`: Separamos as listas de palavras por coma e adicionamos um alias a elas para injetá-las como pontos de penugem mais tarde
- `-u`: Nosso URL de destino com os pontos de inflamação.

```shell-session
casluxd@htb[/htb]$ ffuf -w ./folders.txt:FOLDERS,./wordlist.txt:WORDLIST,./extensions.txt:EXTENSIONS -u http://192.168.10.10/FOLDERS/WORDLISTEXTENSIONS

        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://192.168.10.10/FOLDERS/WORDLISTEXTENSIONS
 :: Wordlist         : FOLDERS: ./folders.txt
 :: Wordlist         : WORDLIST: ./wordlist.txt
 :: Wordlist         : EXTENSIONS: ./extensions.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
________________________________________________

[Status: 200, Size: 8, Words: 1, Lines: 2]
    * EXTENSIONS: ~
    * FOLDERS: wp-content
    * WORDLIST: secret

[Status: 200, Size: 0, Words: 1, Lines: 1]
    * FOLDERS: wp-includes
    * WORDLIST: comment
    * EXTENSIONS: .php

[Status: 302, Size: 0, Words: 1, Lines: 1]
    * FOLDERS: wp-admin
    * WORDLIST: comment
    * EXTENSIONS: .php

...
```

```shell-session
casluxd@htb[/htb]$ curl http://192.168.10.10/wp-content/secret~

Oooops!
```

Seguindo essa abordagem, encontramos com sucesso um arquivo secreto.