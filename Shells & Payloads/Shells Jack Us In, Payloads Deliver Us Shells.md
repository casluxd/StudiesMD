![[Pasted image 20230703222851.png]]

A `shell` é um programa que fornece ao usuário do computador uma interface para inserir instruções no sistema e visualizar a saída de texto ( Bash, Zsh, cmd e PowerShell, por exemplo ). Como testadores de penetração e profissionais de segurança da informação, um shell geralmente é o resultado da exploração de uma vulnerabilidade ou da exclusão de medidas de segurança para obter acesso interativo a um host. Podemos ter ouvido ou lido as seguintes frases usadas por pessoas discutindo um compromisso ou uma sessão de treinos recente:

- `"I caught a shell."`
- `"I popped a shell!"`
- `"I dropped into a shell!"`
- `"I'm in!"`

Normalmente, essas frases se traduzem no entendimento de que essa pessoa explorou com sucesso uma vulnerabilidade em um sistema e conseguiu obter o controle remoto do shell no sistema operacional do computador de destino. Esse é um objetivo comum que um testador de penetração terá ao tentar acessar uma máquina vulnerável. Percederemos que a maior parte deste módulo se concentrará no que vem após a enumeração e identificação de façanhas promissoras.

## Por que obter uma shell?

Lembre-se de que o shell nos dá acesso direto ao `OS`, `system commands`, e `file system`. Portanto, se obtivermos acesso, podemos começar a enumerar o sistema para vetores que podem nos permitir aumentar privilégios, pivô, transferir arquivos e muito mais. Se não estabelecermos uma sessão de shell, estamos bastante limitados sobre até onde podemos chegar em uma máquina de destino.

Estabelecer um shell também nos permite manter a persistência no sistema, dando-nos mais tempo para trabalhar. Pode facilitar o uso do nosso `attack tools`, `exfiltrate data`, `gather`, `store` e `document` todos os detalhes do nosso ataque, como veremos em breve nas manifestações do processo. É importante observar que estabelecer um shell quase sempre significa que estamos acessando a CLI do sistema operacional, e isso pode nos tornar mais difíceis de notar do que se estivéssemos acessando remotamente um shell gráfico [VNC](https://en.wikipedia.org/wiki/Virtual_Network_Computing) ou [RDP](https://www.cloudflare.com/learning/access-management/what-is-the-remote-desktop-protocol/). Outro benefício significativo de se tornar qualificado com interfaces de linha de comando é que elas podem ser `harder to detect than graphical shells`, `faster to navigate the OS`, e `easier to automate our actions`. Vemos shells através da lente das seguintes perspectivas ao longo deste módulo:
- Computing: O ambiente de usuário baseado em texto que é utilizado para administrar tarefas e enviar instruções em um PC. Pense em Bash, Zsh, cmd e PowerShell.
- Exploitation & Security: Um shell geralmente é o resultado de explorar uma vulnerabilidade ou ignorar medidas de segurança para obter acesso interativo a um host. Um exemplo seria desencadear [EternalBlue](https://www.cisecurity.org/wp-content/uploads/2019/01/Security-Primer-EternalBlue.pdf) em um host do Windows para obter acesso ao cmd-prompt em um host remotamente
- Web: Isso é um pouco diferente. Um shell da Web é muito parecido com um shell padrão, exceto que explora uma vulnerabilidade ( com a capacidade de fazer upload de um arquivo ou script ) que fornece ao invasor uma maneira de emitir instruções, leia e acesse arquivos e execute ações destrutivas potencialmente no host subjacente. O controle do shell da web geralmente é feito chamando o script dentro de uma janela do navegador.

## Shells Jack Us In, Payloads Deliver Us Shells

Dentro do setor de TI como um todo, um `payload` pode ser definido de algumas maneiras diferentes:

- `Networking`: A parte de dados encapsulada de um pacote que atravessa redes de computadores modernas.
- `Basic Computing`: Uma carga útil é a parte de um conjunto de instruções que define a ação a ser executada. Cabeçalhos e informações de protocolo removidos.
- `Programming`: A parte de dados referenciada ou transportada pela instrução da linguagem de programação
- `Exploitation & Security`: Uma carga útil é `code` criado com a intenção de explorar uma vulnerabilidade em um sistema de computador. O termo carga útil pode descrever vários tipos de malware, incluindo, entre outros, ransomware

Neste módulo, trabalharemos com muitos tipos diferentes de `payloads` e métodos de entrega no contexto de nos conceder acesso a um host e estabelecer `remote shell`
sessões com sistemas vulneráveis.