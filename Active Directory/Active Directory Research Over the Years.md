O Active Directory tem sido uma importante área de foco para pesquisadores de segurança na última década. A partir de 2014, começamos a ver muitas das ferramentas e muitas das pesquisas surgindo, levando à descoberta de ataques e técnicas comuns que ainda são usados ​​hoje. Abaixo está uma linha do tempo de eventos que destacam a descoberta de alguns dos ataques e falhas mais impactantes por pesquisadores incríveis, bem como o lançamento de algumas das ferramentas mais utilizadas pelos testadores de penetração até hoje. Embora muitas técnicas tenham sido descobertas ao longo dos anos, o AD (e agora o Azure AD) apresenta uma vasta superfície de ataque e novos ataques ainda estão sendo descobertos. Surgem novas ferramentas que tanto os testadores de penetração quanto os defensores devem ter um domínio firme para ajudar as organizações na tarefa difícil, mas crítica, de proteger os ambientes de AD.

Como podemos ver na linha do tempo abaixo, falhas críticas são continuamente descobertas. O ataque noPac foi descoberto em dezembro de 2021 e é o ataque crítico de AD mais recente descoberto no momento da redação deste artigo (janeiro de 2022).

À medida que avançamos em 2022, certamente veremos novas ferramentas e ataques lançados e novos métodos para explorar e encadear vulnerabilidades conhecidas. Devemos estar atualizados com as melhores e mais recentes pesquisas do Active Directory para melhor ajudar os clientes a protegê-lo ou protegê-lo nós mesmos.

Esta não é de forma alguma uma lista abrangente de todas as excelentes pesquisas e ferramentas que foram lançadas ao longo dos anos, mas é um resumo de muitas das mais importantes vistas na última década. O trabalho árduo dos pesquisadores listados nesta linha do tempo (e muitos outros) levou a descobertas notáveis ​​e à criação de ferramentas que podem ajudar os testadores de penetração e os defensores a se aprofundar nos ambientes do Active Directory. Com eles, é mais fácil encontrar falhas óbvias e obscuras de alto risco antes que os invasores o façam.

## AD Attacks & Tools Timeline

## 2021

A vulnerabilidade PrintNightmare foi lançada. Essa era uma falha de execução remota de código no spooler de impressão do Windows que poderia ser usada para assumir o controle de hosts em um ambiente AD. O ataque Shadow Credentials foi lançado, o que permite que usuários com poucos privilégios representem outras contas de usuário e computador se as condições estiverem corretas e pode ser usado para escalar privilégios em um domínio. O ataque noPac foi lançado em meados de dezembro de 2021, quando grande parte do mundo da segurança estava focado nas vulnerabilidades do Log4j. Esse ataque permite que um invasor obtenha controle total sobre um domínio a partir de uma conta de usuário de domínio padrão, se existirem as condições corretas.

## 2020

O ataque ZeroLogon estreou no final de 2020. Essa foi uma falha crítica que permitiu a um invasor representar qualquer controlador de domínio não corrigido em uma rede.

## 2019

harmj0y apresentou a palestra "Kerberoasting Revisited" na DerbyCon, que apresentou novas abordagens para o Kerberoasting. Elad Shamir lançou uma postagem de blog descrevendo técnicas para abusar da delegação restrita baseada em recursos (RBCD) no Active Directory. A empresa BC Security lançou o Empire 3.0 (agora versão 4), que foi um relançamento da estrutura PowerShell Empire escrita em Python3 com muitas adições e alterações.

## 2018

O bug "Printer Bug" foi descoberto por Lee Christensen e a ferramenta SpoolSample PoC foi lançada, que aproveita esse bug para forçar os hosts do Windows a se autenticarem em outras máquinas por meio da interface MS-RPRN RPC. harmj0y lançou o kit de ferramentas Rubeus para atacar o Kerberos. No final de 2018, harmj0y também lançou o blog "Not A Security Boundary: Breaking Forest Trusts", que apresentou pesquisas importantes sobre a realização de ataques em florestas. A técnica de ataque DCShadow também foi lançada por Vincent LE TOUX e Benjamin Delpy na conferência Bluehat IL 2018. A ferramenta Ping Castle foi lançada por Vincent LE TOUX para realizar auditorias de segurança do Active Directory, procurando por configurações incorretas e outras falhas que possam elevar o nível de risco de um domínio e produzindo um relatório que pode ser usado para identificar maneiras de endurecer ainda mais o ambiente.

## 2017

A técnica ASREPRoast foi introduzida para atacar contas de usuário que não requerem pré-autenticação Kerberos. wald0 e harmj0y apresentaram a palestra fundamental sobre ataques ACL do Active Directory "ACE Up the Sleeve" na Black Hat e DEF CON. harmj0y lançou sua postagem no blog "A Guide to Attacking Domain Trusts" sobre como enumerar e atacar domínios de confiança.

## 2016

BloodHound foi lançado como uma ferramenta de mudança de jogo para visualizar caminhos de ataque no AD na DEF CON 24.

## 2015

2015 viu o lançamento de algumas das ferramentas de Active Directory mais impactantes de todos os tempos. A estrutura do PowerShell Empire foi lançada. O PowerView 2.0 lançado como parte do repositório PowerTools (agora obsoleto), que fazia parte da conta PowerShellEmpire GitHub. O ataque DCSync foi lançado pela primeira vez por Benjamin Delpy e Vincent Le Toux como parte da ferramenta mimikatz. Desde então, foi incluído em outras ferramentas. A primeira versão estável do CrackMapExec ((v1.0.0) foi introduzida. Sean Metcalf deu uma palestra na Black Hat USA sobre os perigos da delegação irrestrita do Kerberos e lançou uma excelente postagem(https://adsecurity.org/?p=1667) no blog sobre o assunto. O kit de ferramentas Impacket também foi lançado em 2015. Esta é uma coleção de ferramentas Python, muitas das quais podem ser usadas para executar ataques do Active Directory. Ele ainda é mantido ativamente em janeiro de 2022 e é uma parte essencial do kit de ferramentas de quase todos os testadores de penetração.

## 2014

Veil-PowerView lançado pela primeira vez. Mais tarde, esse projeto tornou-se parte da estrutura do PowerSploit como a ferramenta de reconhecimento PowerView.ps1 AD (não mais suportada). O ataque Kerberoasting foi apresentado pela primeira vez em uma conferência por Tim Medin no SANS Hackfest 2014.

## 2013

A ferramenta Responder foi lançada por Laurent Gaffie. Responder é uma ferramenta usada para envenenar LLMNR, NBT-NS e MDNS em uma rede do Active Directory. Ele pode ser usado para obter hashes de senha e também executar ataques SMB Relay (quando combinado com outras ferramentas) para mover lateralmente e verticalmente em um ambiente AD. Ele evoluiu consideravelmente ao longo dos anos e ainda tem suporte ativo (com novos recursos adicionados) desde janeiro de 2022.