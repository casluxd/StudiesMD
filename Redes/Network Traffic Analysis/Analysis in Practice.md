
Esta não é uma ciência exata. Pode ser um processo muito dinâmico e não é um loop direto. É muito influenciado pelo que procuramos (erros de rede versus ações maliciosas) e onde você tem visibilidade em sua rede. No entanto, a análise pode ser reduzida a alguns inquilinos básicos.

## Análise Descritiva

A análise descritiva é uma etapa essencial em qualquer análise de dados. Serve para descrever um conjunto de dados com base em características individuais. Ajuda a detectar possíveis erros na coleta de dados e/ou outliers no conjunto de dados.

1. Qual é o problema: Suspeita de violação? Problema de rede?
2. Definir nosso escopo e o objetivo. (o que estamos procurando? qual período de tempo?): 
  - Alvo: vários hosts potencialmente baixando um arquivo malicioso de bad.example.com
  - Quando: nas últimas 48 horas + 2 horas a partir de agora.
  - Informações de suporte: nomes/tipos de arquivos 'superbad.exe' 'new-crypto-miner.exe'
3. Definir nosso(s) alvo(s) (rede / host(s) / protocolo): Escopo: rede 192.168.100.0/24, os protocolos utilizados foram HTTP e FTP.

Usando nosso fluxo de trabalho, determinaremos nosso problema, o que estamos procurando, quando e onde encontrá-lo. A análise descritiva cobre esses conceitos críticos para nossa análise.

## Análise Diagnóstica

A análise diagnóstica esclarece as causas, efeitos e interações das condições. Ao fazer isso, fornece insights que são obtidos por meio de correlações e interpretações. A característica aqui é uma visão retrospectiva, como na análise descritiva intimamente relacionada, com a sutil diferença de tentar encontrar razões para eventos e desenvolvimentos.

1. Capturar tráfego de rede: Conecte-se a um link com acesso à rede 192.168.100.0/24 para capturar o tráfego ao vivo para tentar pegar um dos executáveis ​​na transferência. Veja se um administrador pode extrair dados de PCAP e/ou netflow de nosso SIEM para os dados históricos.
2. Identificação dos componentes de tráfego de rede necessários (filtragem): Assim que tivermos tráfego, filtre quaisquer pacotes desnecessários para esta investigação incluir; qualquer tráfego que corresponda à nossa linha de base comum e mantenha tudo o que for relevante para o escopo da investigação. Por exemplo, HTTP e FTP da sub-rede, qualquer coisa transferindo ou contendo uma solicitação GET para os arquivos executáveis ​​suspeitos.
3. Uma compreensão do tráfego de rede capturado: Depois de filtrar o ruído, é hora de cavar em busca de nossos alvos - filtrar coisas como dados de ftp para encontrar quaisquer arquivos transferidos e reconstruí-los. Para HTTP, podemos filtrar em http.request.method == "GET" para ver quaisquer solicitações GET que correspondam aos nomes de arquivo que estamos procurando. Isso pode nos mostrar quem adquiriu os arquivos e potencialmente outras transferências internas à rede nos mesmos protocolos.

## Análise preditiva

Ao avaliar dados históricos e atuais, a análise preditiva cria um modelo preditivo para probabilidades futuras. Com base nos resultados de análises descritivas e diagnósticas, esse método de análise de dados permite identificar tendências, detectar desvios dos valores esperados em um estágio inicial e prever ocorrências futuras com a maior precisão possível.

1. Anotação e mapeamento mental dos resultados encontrados:
- Anotar tudo o que fazemos, vemos ou encontramos ao longo da investigação é crucial. Certifique-se de que estamos fazendo anotações amplas, incluindo:
- Períodos de tempo durante os quais capturamos o tráfego.
- Hosts suspeitos dentro da rede.
- Conversas contendo os arquivos em questão. (para incluir timestamps e números de pacotes)
2. Resumo da análise (o que encontramos?)
- Por fim, resuma o que descobrimos explicando os detalhes relevantes para que os superiores possam decidir colocar em quarentena os hosts afetados ou realizar uma resposta mais significativa a incidentes.
- Nossa análise afetará as decisões tomadas, por isso é essencial ser o mais claro e conciso possível.
3. Realizando uma avaliação dos dados que encontramos, comparando-os com nosso tráfego de linha de base e dados ruins conhecidos, como marcadores de infiltração ou exploração (como assinaturas de vírus e outras ferramentas de hacking), estamos realizando Análise Preditiva. Nesse processo, pintamos um quadro claro para que ações apropriadas possam ser tomadas em resposta.

## Análise Prescritiva

A análise prescritiva visa restringir as ações a serem tomadas para eliminar ou prevenir um problema futuro ou desencadear uma atividade ou processo específico. Usando os resultados de nosso fluxo de trabalho, podemos tomar decisões sensatas sobre quais ações são necessárias para resolver o problema e evitar que ele aconteça novamente. Prescrever uma solução é o culminar deste fluxo de trabalho. Uma vez feito e resolvido o problema, é prudente refletir sobre todo o processo e desenvolver as lições aprendidas. Essas lições, quando documentadas, nos permitirão fortalecer nossos processos - documentar o que foi feito corretamente, quais ações não ajudaram e o que poderia melhorar.

Este fluxo de trabalho é um exemplo de como iniciar o processo de análise no tráfego capturado. Acima, dividimos em suas partes para explicar onde elas se encaixam no processo de análise e a que tipo de análise ela pertence. Nós o incluímos aqui novamente como um todo para que possa servir de modelo.

1. Qual é o problema: Suspeita de violação? Problema de rede?
2. Definir nosso escopo e objetivo (o que estamos procurando? em que período de tempo?): alvo: vários hosts potencialmente baixando um arquivo malicioso de bad.example.com, quando: nas últimas 48 horas + 2 horas a partir de agora, informações de suporte: nomes/tipos de arquivos 'superbad.exe' 'new-crypto-miner.exe'
3. Definir nosso(s) alvo(s) (rede / host(s) / protocolo): escopo: 192.168.100.0/24 os protocolos de rede usados ​​foram HTTP e FTP.
4. Capturar tráfego de rede: conecte-se a um link com acesso à rede 192.168.100.0/24 para capturar o tráfego ao vivo para tentar pegar um dos executáveis ​​na transferência. Veja se um administrador pode extrair dados de PCAP e/ou netflow de nosso SIEM para os dados históricos.
5. Identificação dos componentes de tráfego de rede necessários (filtragem): assim que tivermos tráfego, filtre qualquer tráfego desnecessário para esta investigação incluir; qualquer tráfego que corresponda à nossa linha de base comum e mantenha tudo o que for relevante para o escopo. HTTP e FTP da sub-rede, qualquer coisa transferindo ou contendo uma solicitação GET para os arquivos executáveis ​​suspeitos;
6. Uma compreensão do tráfego de rede capturado: Depois de filtrar o ruído, é hora de cavar em busca de nossos alvos - filtrar coisas como dados de ftp para encontrar quaisquer arquivos transferidos e reconstruí-los. Para HTTP, podemos filtrar em http.request.method == "GET" para ver quaisquer solicitações GET que correspondam aos nomes de arquivo que estamos procurando. Isso pode nos mostrar quem adquiriu os arquivos e possíveis outras transferências internas à rede nos mesmos protocolos.
7. Anotação e mapeamento mental dos resultados encontrados: Anotar tudo o que fazemos, vemos ou encontramos ao longo da investigação é crucial. Certifique-se de que estamos fazendo anotações amplas, incluindo: Períodos de tempo durante os quais capturamos o tráfego, hosts suspeitos dentro da rede, Conversas contendo os arquivos em questão. (para incluir timestamps e números de pacotes)
8. Resumo da análise (o que encontramos?): Por fim, resuma o que foi encontrado, explicando os detalhes relevantes para que os superiores possam tomar uma decisão informada de colocar em quarentena os hosts afetados ou realizar uma resposta mais significativa a incidentes. Nossa análise afetará as decisões tomadas, por isso é essencial ser o mais claro e conciso possível.

# Principais componentes de uma análise eficaz

## 1. Conheça seu ambiente

Existem vários componentes-chave para executar a análise de tráfego de forma eficaz. Primeiro, conheça o ambiente. Se não tivermos certeza se um host pertence à rede, como podemos determinar se ele é invasor ou não? Manter inventários de ativos e mapas de rede é vital. Isso ajudará no processo de análise.

## 2. Posicionamento é a chave

Em seguida, o posicionamento do nosso host para capturar o tráfego é algo crítico. O mais próximo da origem do problema é o posicionamento ideal da nossa ferramenta de captura. Se o tráfego em questão vier da Internet, ouvir os links de entrada é uma ótima maneira de ver o quadro completo. É o mais próximo da fonte que nós, os administradores, podemos chegar. Se o problema parece estar isolado em um host em nossa rede interna, tente colocar as ferramentas de captura no mesmo segmento que o host com problema e veja qual tráfego está acontecendo dentro do segmento.

## 3. Persistência

A persistência é o próximo componente crítico para nós. O problema nem sempre será fácil de detectar. Pode até não ser um evento frequente na rede. Por exemplo, o servidor de Comando e Controle de um invasor alcançando os computadores da vítima pode acontecer apenas em um intervalo de tempo de várias horas ou até mesmo uma vez por dia ou menos. Isso significa que, se não o detectarmos na primeira vez, pode demorar um pouco até que ele apareça em nossos logs. Não perca a motivação para encontrar o problema. Isso pode significar a diferença entre parar o invasor e uma violação em grande escala, como um ataque de ransomware.

# Abordagem de análise

Passamos algum tempo discutindo o processo de análise e como iniciar um fluxo de trabalho básico ao realizar nossas tarefas. Vamos tomar um segundo para discutir algumas vitórias fáceis ao olhar para o tráfego e encontrar problemas.

- Comece com os protocolos padrão primeiro e trabalhe até os austeros e específicos apenas para a organização. A maioria dos ataques vem da internet, então ele precisa acessar a rede interna de alguma forma. Isso significa que haverá tráfego gerado e logs escritos sobre ele. HTTP/S, FTP, e-mail e tráfego básico de TCP e UDP serão as coisas mais comuns vistas no mundo. Comece por eles e elimine tudo o que não for necessário para a investigação. Depois disso, verifique os protocolos padrão que permitem a comunicação entre redes, como SSH, RDP ou Telnet. Ao procurar por esses tipos de anomalias, fique atento à política de segurança da rede. O plano de segurança e as implementações de nossa organização permitem sessões RDP iniciadas fora da empresa? E quanto ao uso do Telnet?

- Procure padrões. Um host específico ou um conjunto de hosts está verificando algo na Internet no mesmo horário diariamente? Esta é uma configuração típica de perfil de Comando e Controle que pode ser facilmente detectada procurando padrões em nossos dados de tráfego.

- Verifique qualquer host para hospedar em nossa rede. Em uma configuração padrão, os hosts do usuário raramente conversam entre si. Portanto, desconfie de qualquer tráfego que apareça assim. Normalmente, os hosts conversam com a infraestrutura para obter concessões de endereço IP, solicitações de DNS, serviços corporativos e descobrir sua rota. Também veremos hosts conversando com servidores da Web locais, compartilhamentos de arquivos e outras infraestruturas críticas para o ambiente funcionar como controladores de domínio e aplicativos de autenticação.

- Procure por eventos únicos. Coisas como um host que costuma visitar um site específico dez vezes ao dia mudando seu padrão e só o fazendo uma vez é curioso. Ver uma sequência de agente de usuário diferente que não corresponda aos nossos aplicativos ou hosts conversando com um servidor na Internet também é algo para se preocupar. Uma porta aleatória sendo vinculada apenas uma ou duas vezes em um host também é digna de nota. Isso pode ser uma abertura para coisas como retornos de chamada C2, alguém abrindo uma porta para fazer algo fora do padrão ou um aplicativo mostrando comportamento anormal. Em ambientes grandes, os padrões são esperados, então qualquer coisa que se destaque merece uma olhada.

- Não tenha medo de pedir ajuda. Isso pode parecer exagerado e óbvio, mas depois de um tempo observando as capturas de pacotes, as coisas podem se misturar, as coisas podem se misturar e podemos não ver o quadro completo. Ter um segundo par de olhos sobre os dados pode ser uma grande ajuda para identificar coisas que podem ter sido encobertas.

 - Qual é o problema? Um breve resumo do assunto
 - definir nosso escopo e o objetivo (o que estamos procurando? qual período de tempo?)
 - definir nosso(s) alvo(s) (rede / host(s) / protocolo)
 - capturar tráfego de rede/ler de PCAP capturado anteriormente.
 - identificação dos componentes de tráfego de rede necessários (filtragem). Assim que tivermos nosso tráfego, filtre qualquer tráfego desnecessário para esta investigação incluir; qualquer tráfego que corresponda à nossa linha de base comum, qualquer tráfego que corresponda à nossa linha de base comum e mantenha tudo o que for relevante para o escopo da investigação.
 - Uma compreensão do tráfego de rede capturado. Depois de filtrarmos o ruído, é hora de cavar em busca de nossos alvos. Comece amplo e feche o círculo em torno de nosso escopo.
 - anotações / mapeamento mental dos resultados encontrados. Anotar tudo o que fazemos, vemos ou encontramos ao longo da investigação é crucial. Certifique-se de fazer anotações amplas, incluindo: Períodos de tempo durante os quais capturamos o tráfego, Hosts/portas suspeitos dentro da rede, Conversas contendo qualquer coisa suspeita. (para incluir timestamps e números de pacotes, arquivos, etc.)
 - resumo da análise (o que encontramos?). Por fim, resuma o que foi encontrado, explicando os detalhes relevantes para que os superiores possam decidir colocar em quarentena os hosts afetados ou realizar uma missão de resposta a incidentes mais críticos. Nossa análise afetará as decisões tomadas, por isso é essencial ser o mais claro e conciso possível.

Em resumo, o processo de análise é uma tarefa muito dinâmica e nossos dias nunca mais serão os mesmos. Continue aprendendo, entenda o que está acontecendo ao nosso redor e, à medida que suas habilidades aumentam, também aumenta a capacidade de detectar ameaças. Esse processo não depende apenas do uso de ferramentas como tcpdump e Wireshark. Existem muitas ferramentas úteis como Snort, Security Onion, Firewalls e SIEMs que podem ajudar a enriquecer nossa compreensão do meio ambiente e proporcionar uma melhor proteção. Não tenha medo de utilizá-los em investigações.