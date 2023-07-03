
As vulnerabilidades de componentes de back-end mais críticas são aquelas que podem ser atacadas externamente e podem ser aproveitadas para assumir o controle do servidor de back-end sem a necessidade de acesso local a esse servidor (ou seja, teste de penetração externa). Essas vulnerabilidades geralmente são causadas por erros de codificação cometidos durante o desenvolvimento dos componentes de back-end de um aplicativo da web. Portanto, existe uma grande variedade de tipos de vulnerabilidade nessa área, desde vulnerabilidades básicas que podem ser exploradas com relativa facilidade até vulnerabilidades sofisticadas que exigem conhecimento profundo de toda a aplicação web.

## Public CVE

Como muitas organizações implantam aplicativos da web que são usados ​​publicamente, como aplicativos da web de código aberto e proprietários, esses aplicativos da web tendem a ser testados por muitas organizações e especialistas em todo o mundo. Isso leva à descoberta frequente de um grande número de vulnerabilidades, a maioria das quais são corrigidas e, em seguida, compartilhadas publicamente e atribuídas a um registro e pontuação CVE (Common Vulnerabilities and Exposures).

Muitos testadores de penetração também fazem explorações de prova de conceito para testar se uma determinada vulnerabilidade pública pode ser explorada e geralmente disponibilizam essas explorações para uso público, para fins educacionais e de teste. Isso faz com que a busca por explorações públicas seja a primeira etapa pela qual devemos passar para aplicativos da web.

`Dica: A primeira etapa é identificar a versão do aplicativo da web. Isso pode ser encontrado em vários locais, como o código-fonte do aplicativo da web. Para aplicativos da web de código aberto, podemos verificar o repositório do aplicativo da web e identificar onde o número da versão é mostrado (por exemplo, na página (version.php)) e, em seguida, verificar a mesma página em nosso aplicativo da web de destino para confirmar.`

Depois de identificar a versão do aplicativo da web, podemos pesquisar no Google por exploits públicos para esta versão do aplicativo da web. Também podemos utilizar bancos de dados de exploração online, como Exploit DB, Rapid7 DB ou Vulnerability Lab. O exemplo a seguir mostra uma pesquisa por explorações públicas do WordPress no banco de dados Rapid7:

![[Pasted image 20230612203434.png]]

Normalmente, estaríamos interessados ​​em explorações com uma pontuação CVE de 8 a 10 ou explorações que levam à execução remota de código. Outros tipos de explorações públicas também devem ser considerados se nenhuma das opções acima estiver disponível.

## CVSS

O Common Vulnerability Scoring System (CVSS) é um padrão da indústria de código aberto para avaliar a gravidade das vulnerabilidades de segurança. Esse sistema de pontuação costuma ser usado como uma medida padrão para organizações e governos que precisam produzir pontuações de gravidade precisas e consistentes para as vulnerabilidades de seus sistemas. Isso ajuda na priorização de recursos e na resposta a uma determinada ameaça.

As pontuações do CVSS são baseadas em uma fórmula que usa várias métricas: Base, Temporal e Ambiental. Ao calcular a gravidade de uma vulnerabilidade usando CVSS, as métricas Base produzem uma pontuação que varia de 0 a 10, modificada pela aplicação de métricas Temporais e Environmental. O National Vulnerability Database (NVD) fornece pontuações CVSS para quase todas as vulnerabilidades conhecidas e divulgadas publicamente. No momento, o NVD fornece apenas pontuações básicas com base nas características inerentes de uma determinada vulnerabilidade. Os sistemas de pontuação atuais são CVSS v2 e CVSS v3. Existem várias diferenças entre os sistemas v2 e v3, nomeadamente alterações nos grupos Base e Environmental para contabilizar métricas adicionais. Mais informações sobre as diferenças entre os dois sistemas de pontuação podem ser encontradas aqui(https://www.balbix.com/insights/cvss-v2-vs-cvss-v3/).

As classificações de pontuação CVSS diferem ligeiramente entre V2 e V3, como pode ser visto nas tabelas a seguir:

CVSS V2.0 Ratings
- Low: 0.0-3.9
- Medium: 4.0-6.9
- High: 7.0-10.0

CVSS V3.0 Ratings
- None: 0.0
- Low: 0.1-3.9
- Medium: 4.0-6.9
- High: 7.0-8.9
- Critical: 9.0-10.0

O NVD não leva em consideração métricas Temporais e Environmental porque as primeiras podem mudar ao longo do tempo devido a eventos externos. O último é uma métrica personalizada com base no impacto potencial da vulnerabilidade em uma determinada organização. O NVD fornece uma calculadora CVSS v2 e uma calculadora CVSS v3 que as organizações podem usar para fatorar riscos adicionais de dados Temporais e Environmental exclusivos para elas. As calculadoras são muito interativas e podem ser usadas para ajustar a pontuação CVSS ao nosso ambiente. Podemos passar por cada métrica para ler mais sobre ela e determinar exatamente como ela se aplica à nossa organização.

![[Pasted image 20230612204305.png]]

Brinque com a calculadora CVSS e veja como as várias métricas podem ser ajustadas para chegar a uma determinada pontuação. Revise alguns CVEs e tente chegar à mesma pontuação CVSS. Como a pontuação CVSS muda quando você aplica métricas Temporais e Environmental? Este guia prático(https://www.first.org/cvss/user-guide) é extremamente útil para entender V2 e V3 e como usar as calculadoras para chegar a uma determinada pontuação.

# Back-end Server Vulnerabilities

Assim como as vulnerabilidades públicas para aplicativos da web, também devemos considerar a procura de vulnerabilidades para outros componentes de back-end, como o servidor de back-end ou o servidor da web. Um exemplo de vulnerabilidade de servidor web bem conhecida é o Shell-Shock, que afetou os servidores web Apache lançados durante e antes de 2014 e utilizou solicitações HTTP para obter controle remoto sobre o servidor back-end.

Quanto às vulnerabilidades no servidor back-end ou no banco de dados, elas geralmente são utilizadas após obter acesso local ao servidor back-end ou rede back-end, que pode ser obtido por meio de vulnerabilidades externas ou durante testes de penetração interna. Eles geralmente são usados ​​para obter acesso altamente privilegiado no servidor de back-end ou na rede de back-end ou obter controle sobre outros servidores na mesma rede.

Embora não sejam diretamente exploráveis ​​externamente, essas vulnerabilidades ainda são críticas e precisam ser corrigidas para evitar que todo o aplicativo da Web seja comprometido.