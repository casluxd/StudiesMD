
Enquanto os sistemas operacionais Windows usam uma variedade de protocolos para se comunicar, o Active Directory requer especificamente o Lightweight Directory Access Protocol (LDAP), a versão do Kerberos da Microsoft, DNS para autenticação e comunicação, e MSRPC, que é a implementação da Microsoft de Remote Procedure Call (RPC), uma técnica de comunicação entre processos usada para aplicativos baseados em modelo cliente-servidor.

# Kerberos



O Kerberos é o protocolo de autenticação padrão para contas de domínio desde o Windows 2000. O Kerberos é um padrão aberto e permite a interoperabilidade com outros sistemas que usam o mesmo padrão. Quando um usuário faz login em seu PC, o Kerberos é usado para autenticá-lo por meio de autenticação mútua, ou tanto o usuário quanto o servidor verificam sua identidade. Kerberos é um protocolo de autenticação sem estado baseado em tickets em vez de transmitir senhas de usuário pela rede. Como parte dos Serviços de Domínio Active Directory (AD DS), os Controladores de Domínio têm um Centro de Distribuição de Chaves Kerberos (KDC) que emite tíquetes. Quando um usuário inicia uma solicitação de login em um sistema, o cliente que está usando para autenticar solicita um ticket do KDC, criptografando a solicitação com a senha do usuário. Se o KDC puder descriptografar a solicitação (AS-REQ) usando sua senha, ele criará um Ticket Granting Ticket (TGT) e o transmitirá ao usuário. O usuário então apresenta seu TGT a um Controlador de Domínio para solicitar um tíquete do Ticket Granting Service (TGS), criptografado com o hash de senha NTLM do serviço associado. Por fim, o cliente solicita acesso ao serviço requerido apresentando o TGS ao aplicativo ou serviço, que o descriptografa com seu hash de senha. Se todo o processo for concluído adequadamente, o usuário terá permissão para acessar o serviço ou aplicativo solicitado.

A autenticação Kerberos separa efetivamente as credenciais dos usuários de suas solicitações para recursos consumíveis, garantindo que sua senha não seja transmitida pela rede (ou seja, acessando um site interno da intranet do SharePoint). O Kerberos Key Distribution Center (KDC) não registra transações anteriores. Em vez disso, o tíquete do serviço de concessão de tíquete Kerberos (TGS) depende de um Ticket Granting Ticket (TGT) válido. Ele assume que, se o usuário tiver um TGT válido, ele deve ter comprovado sua identidade. O diagrama a seguir mostra esse processo em alto nível.

## Kerberos Authentication Process

1. O usuário faz logon e sua senha é convertida em um hash NTLM, que é usado para criptografar o tíquete TGT. Isso separa as credenciais do usuário de solicitações para recursos.
2. O serviço KDC no DC verifica a solicitação de serviço de autenticação (AS-REQ), verifica as informações do usuário e cria um Ticket Granting Ticket (TGT), que é entregue ao usuário.
3. O utilizador apresenta o TGT ao CD, solicitando um ticket de Serviço de Concessão de Bilhete (TGS) para um determinado serviço. Este é o TGS-REQ. Se o TGT for validado com sucesso, seus dados são copiados para criar um ticket TGS.
4. O TGS é criptografado com o hash de senha NTLM do serviço ou conta de computador em cujo contexto a instância do serviço está sendo executada e é entregue ao usuário no TGS_REP.
5. O usuário apresenta o TGS ao serviço e, se for válido, o usuário tem permissão para se conectar ao recurso (AP_REQ).

![[Pasted image 20230601172621.png]]

O protocolo Kerberos usa a porta 88 (TCP e UDP). Ao enumerar um ambiente do Active Directory, geralmente podemos localizar controladores de domínio realizando varreduras de porta procurando a porta 88 aberta usando uma ferramenta como o Nmap.

## DNS

Active Directory Domain Services (AD DS) usa DNS para permitir que clientes (estações de trabalho, servidores e outros sistemas que se comunicam com o domínio) localizem controladores de domínio e para os controladores de domínio que hospedam o serviço de diretório se comunicarem entre si. O DNS é usado para resolver nomes de host para endereços IP e é amplamente usado em redes internas e na Internet. Redes internas privadas usam namespaces DNS do Active Directory para facilitar a comunicação entre servidores, clientes e pares. O AD mantém um banco de dados de serviços em execução na rede na forma de registros de serviço (SRV). Esses registros de serviço permitem que os clientes em um ambiente AD localizem os serviços de que precisam, como servidor de arquivos, impressora ou controlador de domínio. O DNS dinâmico é usado para fazer alterações no banco de dados DNS automaticamente, caso o endereço IP de um sistema seja alterado. Fazer essas entradas manualmente consumiria muito tempo e deixaria espaço para erros. Se o banco de dados DNS não tiver o endereço IP correto para um host, os clientes não poderão localizá-lo e se comunicar com ele na rede. Quando um cliente ingressa na rede, ele localiza o controlador de domínio enviando uma consulta ao serviço DNS, recuperando um registro SRV do banco de dados DNS e transmitindo o nome de host do controlador de domínio ao cliente. O cliente então usa esse nome de host para obter o endereço IP do controlador de domínio. O DNS usa TCP e porta UDP 53. A porta UDP 53 é o padrão, mas volta para TCP quando não consegue mais se comunicar e as mensagens DNS são maiores que 512 bytes.

![[Pasted image 20230601173527.png]]

## Forward DNS Lookup

Vejamos um exemplo. Podemos executar um nslookup para o nome de domínio e recuperar todos os endereços IP dos controladores de domínio em um domínio.

```powershell-session
PS C:\htb> nslookup INLANEFREIGHT.LOCAL

Server:  172.16.6.5
Address:  172.16.6.5

Name:    INLANEFREIGHT.LOCAL
Address:  172.16.6.5
```

## Rerverse DNS Lookup

Se quisermos obter o nome DNS de um único host usando o endereço IP, podemos fazer o seguinte:

```powershell-session
PS C:\htb> nslookup 172.16.6.5

Server:  172.16.6.5
Address:  172.16.6.5

Name:    ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
Address:  172.16.6.5
```

## Finding IP Address of a Host

Se quisermos encontrar o endereço IP de um único host, podemos fazer isso ao contrário. Podemos fazer isso com ou sem especificar o FQDN.

```powershell-session
PS C:\htb> nslookup ACADEMY-EA-DC01

Server:   172.16.6.5
Address:  172.16.6.5

Name:    ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
Address:  172.16.6.5
```

# LDAP

O Active Directory oferece suporte ao Lightweight Directory Access Protocol (LDAP) para pesquisas de diretório. O LDAP é um protocolo de plataforma cruzada e de código aberto usado para autenticação em vários serviços de diretório (como AD). A especificação LDAP mais recente é a versão 3, publicada como RFC 4511. Um entendimento firme de como o LDAP funciona em um ambiente AD é crucial para invasores e defensores. O LDAP usa a porta 389 e o LDAP sobre SSL (LDAPS) se comunica pela porta 636.

O AD armazena informações da conta do usuário e informações de segurança, como senhas, e facilita o compartilhamento dessas informações com outros dispositivos na rede. LDAP é a linguagem que os aplicativos usam para se comunicar com outros servidores que fornecem serviços de diretório. Em outras palavras, o LDAP é como os sistemas no ambiente de rede podem "falar" com o AD.

Uma sessão LDAP começa primeiro conectando-se a um servidor LDAP, também conhecido como Agente do Sistema de Diretório. O controlador de domínio no AD escuta ativamente as solicitações LDAP, como solicitações de autenticação de segurança.

![[Pasted image 20230601173955.png]]

A relação entre AD e LDAP pode ser comparada ao Apache e HTTP. Da mesma forma que o Apache é um servidor web que utiliza o protocolo HTTP, o Active Directory é um servidor de diretórios que utiliza o protocolo LDAP.

Embora incomum, você pode encontrar organizações ao realizar uma avaliação que não têm AD, mas estão usando LDAP, o que significa que provavelmente usam outro tipo de servidor LDAP, como OpenLDAP.

### AD LDAP Authentication

O LDAP é configurado para autenticar credenciais no AD usando uma operação "BIND" para definir o estado de autenticação para uma sessão LDAP. Existem dois tipos de autenticação LDAP.

1. Simple Authentication: Isso inclui autenticação anônima, autenticação não autenticada e autenticação de nome de usuário/senha. Autenticação simples significa que um nome de usuário e senha criam uma solicitação BIND para autenticação no servidor LDAP.
2. SASL Authentication: O framework Simple Authentication and Security Layer (SASL) usa outros serviços de autenticação, como Kerberos, para vincular ao servidor LDAP e, em seguida, usa esse serviço de autenticação (Kerberos neste exemplo) para autenticar no LDAP. O servidor LDAP usa o protocolo LDAP para enviar uma mensagem LDAP ao serviço de autorização, que inicia uma série de mensagens de desafio/resposta resultando em autenticação bem-sucedida ou malsucedida. O SASL pode fornecer segurança adicional devido à separação dos métodos de autenticação dos protocolos de aplicativos.

As mensagens de autenticação LDAP são enviadas em texto não criptografado por padrão para que qualquer pessoa possa farejar as mensagens LDAP na rede interna. Recomenda-se usar criptografia TLS ou similar para proteger essas informações em trânsito.

## MSRPC

Conforme mencionado acima, o MSRPC é a implementação da Microsoft de Chamada de Procedimento Remoto (RPC), uma técnica de comunicação entre processos usada para aplicativos baseados em modelo cliente-servidor. Os sistemas Windows usam MSRPC para acessar sistemas no Active Directory usando quatro interfaces RPC principais.

- lsarpc: Um conjunto de chamadas RPC para o sistema Local Security Authority (LSA) que gerencia a política de segurança local em um computador, controla a política de auditoria, e fornece serviços de autenticação interativos. O LSARPC é usado para executar o gerenciamento nas políticas de segurança do domínio.
- netlogon: Netlogon é um processo do Windows usado para autenticar usuários e outros serviços no ambiente de domínio. É um serviço que é executado continuamente em segundo plano.
- samr: O Remote SAM (samr) fornece funcionalidade de gerenciamento para o banco de dados de contas de domínio, armazenando informações sobre usuários e grupos. Os administradores de TI usam o protocolo para gerenciar usuários, grupos e computadores, permitindo que os administradores criem, leiam, atualizem e excluam informações sobre princípios de segurança. Atacantes (e pentesters) podem usar o protocolo samr para realizar reconhecimento sobre o domínio interno usando ferramentas como BloodHound para mapear visualmente a rede AD e crie "caminhos de ataque" para ilustrar visualmente como o acesso administrativo ou o comprometimento total do domínio podem ser alcançados. As organizações podem se proteger contra esse tipo de reconhecimento alterando uma chave de registro do Windows para permitir apenas que os administradores executem consultas SAM remotas, pois, por padrão, todos os usuários de domínio autenticados podem fazer essas consultas para reunir uma quantidade considerável de informações sobre o domínio AD.
- drsuapi: drsuapi é a API da Microsoft que implementa o Protocolo Remoto do Serviço de Replicação de Diretório (DRS), que é usado para executar tarefas relacionadas à replicação em Controladores de Domínio em um ambiente multi-DC. Os invasores podem utilizar drsuapi para criar uma cópia do arquivo de banco de dados de domínio do Active Directory (NTDS.dit) para recuperar hashes de senha para todas as contas no domínio, que pode então ser usado para executar ataques Pass-the-Hash para acessar mais sistemas ou crackeado offline usando uma ferramenta como Hashcat para obter a senha de texto não criptografado para fazer login nos sistemas usando protocolos de gerenciamento remoto, como Remote Desktop (RDP) e WinRM.