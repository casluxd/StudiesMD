Enumeração é um termo amplamente usado em segurança cibernética. Representa a coleta de informações usando métodos ativos ( de varredura ) e uso passivo ( de terceiros ). É importante observar que o OSINT é um procedimento independente e deve ser realizado separadamente da enumeração, porque `OSINT é baseado exclusivamente na obtenção de informações de forma passiva` e não envolve enumeração ativa do alvo especificado. A enumeração é um ciclo no qual coletamos informações repetidamente com base em quais dados temos ou já descobrimos.

As informações podem ser coletadas de domínios, endereços IP, serviços acessíveis e muitas outras fontes.

Depois de identificarmos os alvos na infraestrutura de nossos clientes, precisamos examinar os serviços e protocolos individuais. Na maioria dos casos, são serviços que permitem a comunicação entre clientes, infraestrutura, administração e funcionários.

Se imaginarmos que fomos contratados para investigar a segurança de TI de uma empresa, começaremos a desenvolver um entendimento geral da funcionalidade da empresa. Por exemplo, precisamos entender como a empresa está estruturada, quais serviços e fornecedores terceirizados ela usa, quais medidas de segurança podem estar em vigor e muito mais. É aqui que esse estágio pode ser um pouco incompreendido, porque a maioria das pessoas se concentra no óbvio e tenta entrar nos sistemas da empresa, em vez de entender como a infraestrutura é definida e quais aspectos e serviços técnicos são necessários para poder oferecer um serviço específico.

Um exemplo de uma abordagem tão errada pode ser que, depois de encontrar serviços de autenticação como SSH, RDP, WinRM e similares, tentamos fazer força bruta com senhas e nomes de usuário comuns / fracos. Infelizmente, a força bruta é um método barulhento e pode facilmente levar à lista negra, impossibilitando mais testes. Principalmente, isso pode acontecer se não soubermos sobre as medidas defensivas de segurança da empresa e sua infraestrutura. Alguns podem sorrir para essa abordagem, mas a experiência mostrou que muitos testadores adotam esse tipo de abordagem.

`Nosso objetivo não é chegar aos sistemas, mas encontrar todas as maneiras de chegar lá.`

Podemos pensar nisso como uma analogia de um caçador de tesouros se preparando para sua expedição. Ele não apenas pegou uma pá e começou a cavar em algum local aleatório, mas ele planejava e reunia seus equipamentos, estudava mapas e aprendia sobre o terreno que ele tem para cobrir e onde o tesouro pode estar para que ele possa trazer as ferramentas adequadas. Se ele andar por aí cavando buracos por toda parte, causará danos, desperdiçará tempo e energia e provavelmente nunca alcançará seu objetivo. O mesmo pode ser dito para entender a infraestrutura interna e externa de uma empresa, mapeando-a e formulando cuidadosamente nosso plano de ataque.

Os princípios de enumeração são baseados em algumas questões que facilitarão todas as nossas investigações em qualquer situação concebível. Na maioria dos casos, o foco principal de muitos testadores de penetração é o que eles podem ver e não o que não podem ver. No entanto, mesmo o que não podemos ver é relevante para nós e pode muito bem ser de grande importância. A diferença aqui é que começamos a ver os componentes e aspectos que não são visíveis à primeira vista com a nossa experiência.
- O que podemos ver?
- Que razões podemos ter para vê-lo?
- Que imagem o que vemos cria para nós?
- O que ganhamos com isso?
- Como podemos usá-lo?
- O que não podemos ver?
- Que razões podem existir que não vemos?
- Que imagem resulta para nós do que não vemos?

Um aspecto importante que não deve ser confundido aqui é que sempre há exceções às regras. Os princípios, no entanto, não mudam. Outra vantagem desses princípios é que podemos ver pelas tarefas práticas que não nos faltam habilidades de teste de penetração, mas entendimento técnico quando de repente não sabemos como proceder, porque nossa tarefa principal não é explorar as máquinas, mas descobrir como elas podem ser exploradas.

|**`No.`**|**`Principle`**|
|---|---|
|1.|Há mais do que aparenta. Considere todos os pontos de vista.|
|2).|Distinguir entre o que vemos e o que não vemos.|
|3).|Sempre existem maneiras de obter mais informações. Entenda o alvo.|

Para nos familiarizarmos com esses princípios, devemos anotar essas questões e princípios em que sempre podemos vê-los e nos referir a eles com facilidade.