`Enumeration` é a parte mais crítica de todas. A arte, a dificuldade e o objetivo não são obter acesso ao nosso computador de destino. Em vez disso, está identificando todas as maneiras pelas quais poderíamos atacar um alvo que devemos encontrar.

Não se baseia apenas nas ferramentas que usamos. Eles não servirão de muito se não soubermos o que fazer com as informações que recebemos deles. As ferramentas são apenas ferramentas que nunca devemos substituir por nosso conhecimento e nossa atenção aos detalhes. Aqui, trata-se muito mais de interagir ativamente com os serviços individuais para ver quais informações eles nos fornecem e quais possibilidades eles nos oferecem.

É essencial compreender como funcionam estes serviços e que sintaxe utilizam para uma comunicação e interação efetiva com os diferentes serviços.

Esta fase visa melhorar nosso conhecimento e compreensão das tecnologias, protocolos e como eles funcionam e aprender a lidar com novas informações e se adaptar aos nossos conhecimentos já adquiridos. A enumeração está coletando o máximo de informações possível. Quanto mais informações tivermos, mais fácil será para encontrarmos vetores de ataque.

Imagine a seguinte situação:

Nosso parceiro não está em casa e perdeu as chaves do carro. Ligamos para o nosso parceiro e perguntamos onde estão as chaves. Se obtivermos uma resposta como "na sala de estar", não está totalmente claro e pode levar muito tempo para encontrá-los lá. No entanto, e se nosso parceiro nos disser algo como "na sala, na prateleira branca, ao lado da TV, na terceira gaveta"? Como resultado, será muito mais fácil encontrá-los.

Não é difícil obter acesso ao sistema de destino, uma vez que saibamos como fazê-lo. A maioria das maneiras pelas quais podemos obter acesso, podemos restringir aos dois pontos a seguir:
- Funções e/ou recursos que nos permitem interagir com o alvo e/ou fornecer informações adicionais.
- Informações que nos fornecem informações ainda mais importantes para acessar nosso alvo.

Ao digitalizar e inspecionar, procuramos exatamente essas duas possibilidades. A maioria das informações que obtemos vem de configurações incorretas ou negligência da segurança dos respectivos serviços. As configurações incorretas são resultado de ignorância ou de uma mentalidade de segurança incorreta. Por exemplo, se o administrador depende apenas do firewall, Objetos de Diretiva de Grupo (GPOs) e atualizações contínuas, isso geralmente não é suficiente para proteger a rede.

`Enumeration is the key`.

Isso é o que a maioria das pessoas diz, e eles estão certos. No entanto, muitas vezes é mal compreendida. A maioria das pessoas entende que não experimentou todas as ferramentas para obter as informações de que precisa. Na maioria das vezes, porém, não são as ferramentas que não experimentamos, mas sim o fato de não sabermos como interagir com o serviço e o que é relevante.

Essa é precisamente a razão pela qual tantas pessoas ficam presas em um ponto e não avançam. Se essas pessoas tivessem investido algumas horas aprendendo mais sobre o serviço, como funciona e para que serve, economizariam algumas horas ou até dias antes de atingir seu objetivo e obter acesso ao sistema.

`Enumeração manual` é um componente `critical`. Muitas ferramentas de digitalização simplificam e aceleram o processo. No entanto, estes nem sempre podem contornar as medidas de segurança dos serviços. A maneira mais fácil de ilustrar isso é usar o seguinte exemplo:

A maioria das ferramentas de verificação tem um tempo limite definido até receber uma resposta do serviço. Se esta ferramenta não responder dentro de um tempo específico, este serviço/porta será marcado como fechado, filtrado ou desconhecido. Nos dois últimos casos, ainda poderemos trabalhar com ele. No entanto, se uma porta estiver marcada como fechada e o Nmap não a mostrar para nós, estaremos em uma situação ruim. Este serviço/porta pode nos fornecer a oportunidade de encontrar uma maneira de acessar o sistema. Portanto, esse resultado pode levar muito tempo desnecessário até que o encontremos.
