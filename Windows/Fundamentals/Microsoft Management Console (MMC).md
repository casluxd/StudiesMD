
O MMC pode ser usado para agrupar snap-ins ou ferramentas administrativas para gerenciar hardware, software e componentes de rede em um host Windows. Ele existe desde o Windows Server 2000 e é executado em todas as versões do Windows. Também podemos usar o MMC para criar ferramentas personalizadas e distribuí-las aos usuários. O MMC trabalha com o conceito de snap-ins, permitindo que os administradores criem um console customizado apenas com as ferramentas administrativas necessárias para gerenciar diversos serviços. Esses snap-ins podem ser adicionados para gerenciar sistemas locais e remotos.

Podemos abrir o MMC apenas digitando mmc no menu Iniciar. Quando abrimos o MMC pela primeira vez, ele estará em branco.

![image](https://academy.hackthebox.com/storage/modules/49/MMC.png)

A partir daqui, podemos navegar até Arquivo --> Adicionar ou remover snap-ins e começar a personalizar nosso console administrativo.

![image](https://academy.hackthebox.com/storage/modules/49/MMC_add_remove.png)

À medida que começamos a adicionar snap-ins, será perguntado se queremos adicionar o snap-in para gerenciar apenas o computador local ou se ele será usado para gerenciar outro computador na rede.

![image](https://academy.hackthebox.com/storage/modules/49/MMC_services.png)

Assim que terminarmos de adicionar os snap-ins, eles aparecerão no lado esquerdo do MMC. A partir daqui, podemos salvar o conjunto de snap-ins como um arquivo .msc, para que todos sejam carregados na próxima vez que abrirmos o MMC.

Por padrão, eles são salvos no diretório Ferramentas Administrativas do Windows no menu Iniciar. Da próxima vez que abrirmos o MMC, podemos optar por carregar qualquer uma das visualizações que criamos.

![[Pasted image 20230525205100.png]]

