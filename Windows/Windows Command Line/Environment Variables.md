
Agora que já sabemos como usar o prompt de comando, vamos discutir um dos tópicos mais críticos ao pensar sobre como os aplicativos e scripts funcionam no Windows: variáveis de ambiente.

### O que é variável de ambiente?

Variáveis de ambiente são configurações aplicadas globalmente aos nossos hosts. Eles podem ser encontrados em hosts Windows, Linux e macOS. Este conceito não é independente de um tipo de sistema operacional, mas eles funcionam de maneira diferente em cada sistema operacional. Variáveis de ambiente podem ser acessadas pela maioria dos usuários e aplicações no host e são usadas para rodar scripts e acelerar o funcionamento dos aplicativos e os dados de referência.

Em um host Windows, as variáveis de ambiente não diferenciam maiúsculas de minúsculas e podem ter espaços e números no nome. O único problema real que encontraremos é que eles não podem ter um nome que comece com um número ou inclua um sinal de igual. Quando referenciados, veremos essas variáveis chamadas assim: `%SUPER_IMPORTANT_VARIABLE%` 

É normal ver essas variáveis (principalmente aquelas já incorporadas ao sistema) exibidas em letras maiúsculas e utilizando sublinhado para vincular quaisquer palavras no nome. Antes de prosseguir, devemos mencionar um conceito crucial sobre variáveis de ambiente conhecido como escopo.

#### Escopo de variável

Nesse contexto, escopo é um conceito de programação que se refere a onde a variável pode ser acessada ou referenciada. Escopo pode ser separado em duas categorias:

- Global: variáveis globais são acessadas globalmente. Nesse contexto, o escopo global nos permite acessar e referenciar os dados armazenados na variável de qualquer lugar.
- Local: variáveis locais são acessadas apenas no contexto local. Local significa que os dados armazenados pode ser acessados e referenciados dentro da função ou contexto em que foram declarados.

Vamos percorrer um cenário de exemplo juntos para entender melhor as diferenças. Neste cenário, temos dois usuários: Alice e Bob. Ambos os usuários têm uma sessão de prompt de comando padrão e estão conectados simultaneamente na mesma máquina. Além disso, ambos os usuários emitem um comando para imprimir os dados armazenados na variável `%WINDIR%` como visto nos exemplos abaixo:

```cmd-session
C:\Users\alice> echo %WINDIR%

C:\Windows
```

```cmd-session
C:\Users\bob> echo %WINDIR%

C:\Windows
```

Como nós podemos ver, essa variável está acessível para ambos os usuários. Isso acontece porque a variável `%WINDIR%` é uma variável global que é definida pelo sistema operacional Windows. Entretanto, se Alice quisesse criar uma variável secreta que Bob não pudesse visualizar ou acessar, como faria isso?

```cmd-session
C:\Users\alice> set SECRET=HTB{5UP3r_53Cr37_V4r14813}

C:\Users\alice> echo %SECRET%
HTB{5UP3r_53Cr37_V4r14813}
```

```cmd-session
C:\Users\bob> echo %SECRET%
%SECRET%

C:\Users\bob> set %SECRET%
Environment variable %SECRET% not defined
```

No primeiro exemplo, Alice cria uma variável chamada `SECRET` e armazena o valor `HTB{5UP3r_53Cr37_V4r14813}` dentro. Após setar o valor da variável, Alice tenta obter o valor da variável usando o comando **echo** para visualizar o valor armazenado. Entretanto, quando Bob tenta visualizar a mesma variável, ele não pode, pois não está definido no seu ambiente atual. Quando Alice cria uma **variável local** apenas ela pode visualizar pois está definida no contexto do seu ambiente local.

Agora que temos um entendimento básico de variáveis e uma ideia geral do básico por trás de escopos, vamos entrar em como o Windows interage e armazena variáveis de ambiente e como nós interagimos com eles. Como antes, Windows, como qualquer outro programa, contém seu próprio conjunto de variáveis conhecido como variáveis de ambiente. Essas variáveis são separadas em escopos **System** e **User**. Além disso, há mais um escopo definido chamado de **Process**. Porém, é volátil por natureza e é considerado a ser um sub-escopo de ambos escopos **System** e **User**. Mantendo isso em mente, vamos explorar suas diferenças e funcionalidades pretendidas.

- Escopo System: O escopo System contém variáveis de ambiente definidos pelo sistema operacional e é acessível globalmente por todos os usuários e contas do sistema. O sistema operacional necessita dessas variáveis para funcionar apropriadamente e é carregada durante o tempo de execução. As permissões requeridas para acessa-las são **Local Administrator** ou **Domain Administrator**. A Registry Location é:` HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment`

- Escopo User: O escopo User contem variáveis de ambiente definidas pelo atual usuário ativo e é acessível apenas por ele. Permissões requeridas são **Current Active User**, **Local Administrator** ou **Domain Administrator**. A Registry Local é: `HKEY_CURRENT_USER\Environment`.

- Escopo Process: O escopo Process contém variáveis de ambiente que são definidas e acessíveis apenas durante o processo em execução. Devido à sua natureza transitória, seu tempo de vida dura apenas para o processo em execução no qual foram inicialmente definidos. Eles também herdam variáveis ​​dos escopos do sistema/usuário e do processo pai que o gera (somente se for um processo filho). Permissões requeridas são **Current Child Process**, **Parent Process** ou **Current Active User**. Não possui Registry Local pois são armazenadas na memória do processo.

## Usando set e echo para visualizar variáveis

Para entender as mudanças que ocorrem nas variáveis de ambiente, precisamos de alguma maneira visualizar seu conteúdo por meio do prompt de comando. Felizmente,  temos algumas opções disponíveis: **set** e **echo**.

```cmd-session
C:\Users\htb\Desktop>set %SYSTEMROOT%

Environment variable C:\Windows not defined
```

Ao abrir o prompt de comando, você pode emitir o conjunto de comandos para imprimir todas as variáveis de ambiente disponíveis no sistema. Como alternativa, você pode inserir o mesmo comando novamente com o nome da variável sem defini-la igual a nada para imprimir o valor de uma variável específica. Vemos que, neste caso, o valor é que não está definido; no entanto, isso ocorre porque não estamos definindo o valor de %SYSTEMROOT% usando set neste exemplo.

```cmd-session
C:\Users\htb\>echo %PATH%

C:\Users\htb\Desktop
```

Semelhante ao exemplo acima, você pode usar echo para exibir o valor de uma variável de ambiente. Ao contrário do comando anterior, **echo** é usado para imprimir o valor contido na variável e não possui recursos integrados adicionais para editar variáveis de ambiente. 

### Quando usar set e setx

Ambos set e setx são utilitários de linha de comando usados para mostrar, setar e remover variáveis de ambiente. A diferença está em como eles alcaçam esses objetivos. O utilitário set apenas manipula variáveis de ambiente na sessão da linha de comando atual. Isso significa que, assim que fecharmos nossa sessão atual, quaisquer adições, remoções ou alterações não serão refletidas na próxima vez que abrirmos um prompt de comando. Suponha que precisamos fazer alterações permanentes nas variáveis de ambiente. Nesse caso, nós usamos o setx para fazer as alterações apropriadas no registry, que existirão após a reinicialização de nossa sessão atual do prompt de comando.

Usando setx, também adicionamos algumas funcionalidades adicionais, como a capacidade de criar e ajustar variáveis em computadores no domínio, bem como em nossa máquina local.

Agora devemos estar familiarizados com algumas das principais diferenças entre os dois comandos discutidos acima. Haverá momentos e situações em que um deve ser priorizado sobre o outro. Como atacante, haverá momentos que precisaremos enumerar as variáveis de ambiente existentes para obter informações. Vamos seguir em frente e criar algumas variáveis reais que podemos usar.

### Criando variáveis

Criar variáveis de ambiente é uma tarefa bastante simples. Podemos usar set ou setx dependendo da tarefa em questão e do nosso objetivo geral. Os exemplos a seguir mostrarão ambos sendo colocados em ação para nos dar uma ideia da sintaxe em torno de cada comando. Observe que a sintaxe entre ambos em alguns casos é muito semelhante; no entanto, setx tem alguns recursos adicionais que tentaremos explorar aqui. Além disso, para garantir que as coisas não fiquem muito repetitivas, mostraremos apenas os comandos set e setx para criar variáveis e utilizar setx para todos os outros exemplos. Apenas saiba que a sintaxe entre criar, remover e editar variáveis de ambiente é idêntica.

Vamos criar uma variável para manter o valor do endereço IP do controlador de domínio (DC), pois podemos achá-lo útil para testar a conectividade com o domínio ou consultar atualizações.

```cmd-session
C:\htb> set DCIP=172.16.5.2
```

Ao executar este comando, não há saída imediata. No entanto, saiba que a variável foi definida para nossa sessão de prompt de comando atual. Podemos verificar isso imprimindo seu valor usando echo.

```cmd-session
C:\htb> echo %DCIP%

172.16.5.2
```

Como podemos ver, a variável de ambiente %DCIP% agora está definida e disponível para acesso. Conforme declarado acima, esta mudança é considerada parte do escopo do processo.

```cmd-session
C:\htb> setx DCIP 172.16.5.2

SUCCESS: Specified value was saved.
```

Nesse exemplo, podemos ver que a sintaxe entre os comandos varia um pouco. Anteriormente, tínhamos que definir o valor da variável igual à própria variável. Aqui temos que fornecer o nome da variável seguido do valor. A sintaxe é a seguinte: setx nome_da_variável valor parâmetros. Após rodar esse comando, nós veremos que nosso valor foi salvo no registry desde que recebemos a mensagem de SUCCESS. Obviamente, se nós estivermos curiosos em saber se o valor foi realmente setado, nós podemos validar da forma que foi feita antes. Lembre-se de que essa alteração só ocorrerá depois que abrirmos outra sessão de prompt de comando.

### Editando variáveis

Em adição a criação das nossas próprias variáveis, nós podemos editá-las. Como já estamos familiarizados com a criação deles, a edição é igualmente fácil, exceto que substituiremos os valores existentes. 

Digamos que o endereço IP de nosso controlador de domínio mudou e precisamos atualizar o valor de nossa variável de ambiente personalizada para refletir essa alteração.

```cmd-session
C:\htb> setx DCIP 172.16.5.5

SUCCESS: Specified value was saved.
```

No exemplo anterior, nós setamos 172.16.5.2 como o valor do DC na rede; no entanto, usando set, podemos atualizar esse valor simplesmente definindo o valor novamente para nosso novo endereço, 172.16.5.5.

### Validando a edição

```cmd-session
C:\htb> echo %DCIP%

172.16.5.5
```

Nós editamos com sucesso nossa variável personalizada inicial para refletir a mudança de IP do controlador de domínio. Agora podemos prosseguir e discutir a remoção de variáveis.

### Removendo variáveis

Para remover variáveis, não podemos excluí-los diretamente como faríamos com um arquivo ou diretório; em vez disso, devemos limpar seus valores definindo-os como iguais a nada. Essa ação excluirá efetivamente a variável e impedirá que ela seja usada conforme pretendido devido à remoção do valor.

```cmd-session
C:\htb> setx DCIP ""


SUCCESS: Specified value was saved.
```

# Variáveis de ambiente importantes

%PATH%: Especifica um conjunto de diretórios(localizações) onde os executáveis dos programas estão localizados.
%OS%: O atual sistema operacional do usuário.
%SYSTEMROOT%: Expande para "C:\Windows." Uma variável somente leitura definida pelo sistema que contém a pasta do sistema Windows. Tudo o que o Windows considera importante para sua funcionalidade principal é encontrado aqui, incluindo dados importantes, binários principais do sistema e arquivos de configuração.
%LOGONSERVER%: Fornece o servidor de login para o usuário ativo no momento, seguido do nome do host da máquina. Podemos usar essas informações para saber se uma máquina ingressou em um domínio ou grupo de trabalho. 
%USERPROFILE%: Fornece a localização da home do usuário ativo no momento. Equivalente a "C:\Users\{username}"
%ProgramFiles%: Equivalente a "C:\Program Files". Este local é onde todos os programas são instalados em um sistema baseado em x64.
%ProgramFiles(x86)%: Equivalente a "C:\Program Files(x64)":  Este local é onde todos os programas de 32 bits em execução no WOW64 são instalados. Observe que essa variável só pode ser acessada em um host de 64 bits. Pode ser usado para indicar com que tipo de host estamos interagindo. (arquitetura x86 x x64)

Mais sobre variáveis de ambiente: https://ss64.com/nt/syntax-variables.html