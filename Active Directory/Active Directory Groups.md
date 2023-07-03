
Depois dos usuários, os grupos são outro objeto significativo no Active Directory. Eles podem colocar usuários semelhantes juntos e atribuir direitos e acesso em massa. Os grupos são outro alvo importante para invasores e testadores de penetração, pois os direitos que eles conferem a seus membros podem não ser prontamente aparentes, mas podem conceder privilégios excessivos (e até não intencionais) que podem ser abusados ​​se não forem configurados corretamente. Existem muitos grupos internos(https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-groups#about-active-directory-groups) no Active Directory, e a maioria das organizações também cria seus próprios grupos para definir direitos e privilégios, gerenciando ainda mais o acesso dentro do domínio. O número de grupos em um ambiente do AD pode aumentar e tornar-se pesado, levando potencialmente a acesso não intencional se não for verificado. É essencial entender o impacto do uso de diferentes tipos de grupos e para qualquer organização auditar periodicamente quais grupos existem em seu domínio, os privilégios que esses grupos concedem a seus membros e verificar se há associação excessiva a grupos além do que é necessário para que um usuário execute seu trabalho diário. Seguindo em frente, discutiremos os diferentes tipos de grupos existentes e os escopos aos quais eles podem ser atribuídos.

Uma questão que surge frequentemente é a diferença entre Grupos e Unidades Organizacionais (OUs). Conforme discutido anteriormente no módulo, as OUs são úteis para agrupar usuários, grupos e computadores para facilitar o gerenciamento e a implantação de configurações de Diretiva de Grupo para objetos específicos no domínio. Os grupos são usados ​​principalmente para atribuir permissões para acessar recursos. As OUs também podem ser usadas para delegar tarefas administrativas a um usuário, como redefinir senhas ou desbloquear contas de usuário sem conceder a eles direitos administrativos adicionais que podem herdar por meio da associação ao grupo.

## Type of Groups

Em termos mais simples, os grupos são usados ​​para colocar usuários, computadores e objetos de contato em unidades de gerenciamento que facilitam a administração de permissões e facilitam a atribuição de recursos como impressoras e acesso ao compartilhamento de arquivos. Por exemplo, se um administrador precisar atribuir acesso de 50 membros de um departamento a uma nova unidade de compartilhamento, seria demorado adicionar a conta de cada usuário individualmente. Conceder permissões dessa maneira também dificultaria a auditoria de quem tem acesso aos recursos e dificultaria a limpeza/revogação de permissões. Em vez disso, um administrador de sistema pode usar um grupo existente ou criar um novo grupo e conceder a esse grupo específico permissões sobre o recurso. A partir daqui, todos os usuários do grupo herdarão as permissões com base em sua participação no grupo. Se as permissões precisarem ser modificadas ou revogadas para um ou mais usuários, eles podem simplesmente ser removidos do grupo, deixando os outros usuários inalterados e suas permissões intactas.

Grupos no Active Directory possuem duas características fundamentais: tipo e escopo. O tipo de grupo define a finalidade do grupo, enquanto o escopo do grupo mostra como o grupo pode ser usado no domínio ou na floresta. Ao criar um novo grupo, devemos selecionar um tipo de grupo. Existem dois tipos principais: grupos de segurança e de distribuição.

![[Pasted image 20230602212939.png]]

O tipo de grupos de segurança é principalmente para facilitar a atribuição de permissões e direitos a uma coleção de usuários em vez de um de cada vez. Eles simplificam o gerenciamento e reduzem a sobrecarga ao atribuir permissões e direitos para um determinado recurso. Todos os usuários adicionados a um grupo de segurança herdarão todas as permissões atribuídas ao grupo, tornando mais fácil mover usuários para dentro e para fora dos grupos, deixando as permissões do grupo inalteradas.

O tipo Grupos de distribuição é usado por aplicativos de e-mail, como o Microsoft Exchange, para distribuir mensagens aos membros do grupo. Eles funcionam como listas de e-mail e permitem a adição automática de e-mails no campo "Para" ao criar um e-mail no Microsoft Outlook. Esse tipo de grupo não pode ser usado para atribuir permissões a recursos em um ambiente de domínio.

### Escopos de grupo

1. Domain Local Group
2. Global Group
3. Universal Group

#### Domain Local Group

Os grupos locais de domínio só podem ser usados ​​para gerenciar permissões para recursos de domínio no domínio em que foram criados.
Grupos locais não podem ser usados ​​em outros domínios, mas PODEM conter usuários de OUTROS domínios. Grupos locais podem ser aninhados (contidos em) outros grupos locais, mas NÃO dentro de grupos globais.

#### Global Group

Grupos globais podem ser usados ​​para conceder acesso a recursos em outro domínio. Um grupo global só pode conter contas do domínio onde foi criado. Grupos globais podem ser adicionados a outros grupos globais e grupos locais.

#### Universal Group

O escopo do grupo universal pode ser usado para gerenciar recursos distribuídos em vários domínios e pode receber permissões para qualquer objeto dentro da mesma floresta. Eles estão disponíveis para todos os domínios de uma organização e podem conter usuários de qualquer domínio. Ao contrário dos grupos locais e globais de domínio, os grupos universais são armazenados no Catálogo Global (GC), e adicionar ou remover objetos de um grupo universal aciona a replicação em toda a floresta. Recomenda-se que os administradores mantenham outros grupos (como grupos globais) como membros de grupos universais porque a associação de grupo global dentro de grupos universais tem menos probabilidade de mudar do que a associação de usuário individual em grupos globais. A replicação só é acionada no nível de domínio individual quando um usuário é removido de um grupo global. Se usuários e computadores individuais (em vez de grupos globais) forem mantidos em grupos universais, isso acionará a replicação em toda a floresta sempre que uma alteração for feita. Isso pode criar muita sobrecarga de rede e potencial para problemas. Abaixo está um exemplo dos grupos no AD e suas configurações de escopo. Por favor, preste atenção a alguns dos grupos críticos e seu escopo. (Administradores corporativos e de esquema em comparação com administradores de domínio, por exemplo.)

```powershell
PS C:\htb> Get-ADGroup  -Filter * |select samaccountname,groupscope

samaccountname                           groupscope
--------------                           ----------
Administrators                          DomainLocal
Users                                   DomainLocal
Guests                                  DomainLocal
Print Operators                         DomainLocal
Backup Operators                        DomainLocal
Replicator                              DomainLocal
Remote Desktop Users                    DomainLocal
Network Configuration Operators         DomainLocal
Distributed COM Users                   DomainLocal
IIS_IUSRS                               DomainLocal
Cryptographic Operators                 DomainLocal
Event Log Readers                       DomainLocal
Certificate Service DCOM Access         DomainLocal
RDS Remote Access Servers               DomainLocal
RDS Endpoint Servers                    DomainLocal
RDS Management Servers                  DomainLocal
Hyper-V Administrators                  DomainLocal
Access Control Assistance Operators     DomainLocal
Remote Management Users                 DomainLocal
Storage Replica Administrators          DomainLocal
Domain Computers                             Global
Domain Controllers                           Global
Schema Admins                             Universal
Enterprise Admins                         Universal
Cert Publishers                         DomainLocal
Domain Admins                                Global
Domain Users                                 Global
Domain Guests                                Global

<SNIP>
```

Os escopos do grupo podem ser alterados, mas há algumas ressalvas:
- Um Grupo Global só pode ser convertido em Grupo Universal se NÃO fizer parte de outro Grupo Global.
- Um Grupo Local de Domínio só pode ser convertido em um Grupo Universal se o Grupo Local de Domínio NÃO contiver nenhum outro Grupo Local de Domínio como membro.
- Um Grupo Universal pode ser convertido em um Grupo Local de Domínio sem quaisquer restrições.
- Um Grupo Universal só pode ser convertido em um Grupo Global se NÃO contiver nenhum outro Grupo Universal como membro.

#### Built-in vs Custom Groups

Vários security groups integrados são criados com um escopo Domain Local Group quando um domínio é criado. Esses grupos são usados ​​para fins administrativos específicos e são discutidos mais na próxima seção. É importante observar que apenas contas de usuário podem ser adicionadas a esses grupos integrados, pois não permitem aninhamento de grupos (grupos dentro de grupos). Alguns exemplos de grupos integrados incluem Administradores de domínio, que é um grupo de segurança global e só pode conter contas de seu próprio domínio. Se uma organização deseja permitir que uma conta do domínio B execute funções administrativas em um controlador de domínio no domínio A, a conta teria que ser adicionada ao grupo interno Administradores, que é um grupo local de domínio. Embora o Active Directory venha pré-preenchido com muitos grupos, é comum para a maioria das organizações criar grupos adicionais (tanto de segurança quanto de distribuição) para seus próprios propósitos. Alterações/acréscimos em um ambiente AD também podem desencadear a criação de grupos adicionais. Por exemplo, quando o Microsoft Exchange é adicionado a um domínio, ele adiciona vários grupos de segurança diferentes ao domínio, alguns dos quais são altamente privilegiados e, se não forem gerenciados adequadamente, podem ser usados ​​para obter acesso privilegiado dentro do domínio.

## Nested Group Membership

Nested group membership é um conceito importante no AD. Conforme mencionado anteriormente, um Grupo Local de Domínio pode ser membro de outro Grupo Local de Domínio no mesmo domínio. Por meio dessa associação, um usuário pode herdar privilégios não atribuídos diretamente à sua conta ou mesmo ao grupo do qual é membro direto, mas sim ao grupo do qual seu grupo é membro. Às vezes, isso pode levar a privilégios não intencionais concedidos a um usuário que são difíceis de descobrir sem uma avaliação detalhada do domínio. Ferramentas como BloodHound são particularmente úteis para descobrir privilégios que um usuário pode herdar por meio de um ou mais aninhamentos de grupos. Esta é uma ferramenta chave para testadores de penetração para descobrir configurações incorretas diferenciadas e também é extremamente poderosa para administradores de sistema e afins para obter insights profundos (visualmente) sobre a postura de segurança de seu(s) domínio(s).

Abaixo está um exemplo de privilégios herdados por meio de associação de grupo aninhado. Embora o DCorner não seja um membro direto do Helpdesk Nível 1, sua associação ao Help Desk concede a eles os mesmos privilégios que qualquer membro do Helpdesk Nível 1 possui. Nesse caso, o privilégio permitiria que eles adicionassem um membro ao grupo Tier 1 Admins (GenericWrite). Se esse grupo conferir quaisquer privilégios elevados no domínio, provavelmente seria um alvo importante para um testador de penetração. Aqui, podemos adicionar nosso usuário ao grupo e obter privilégios concedidos aos membros do grupo Tier 1 Admins, como acesso de administrador local a um ou mais hosts que podem ser usados ​​para acesso adicional.

![[Pasted image 20230602214008.png]]

#### Important Group Attributes

Assim como os usuários, os grupos têm muitos atributos. Alguns dos atributos de grupo mais importantes incluem:
- cn: O cn ou Common-Name é o nome do grupo nos Serviços de Domínio Active Directory.
- member: Quais usuários, grupos e objetos de contato são membros do grupo.
- groupType: Um número inteiro que especifica o tipo e o escopo do grupo.
- memberOf: Uma listagem de quaisquer grupos que contenham o grupo como um membro (associação de grupo aninhado).
- objectSid: Este é o identificador de segurança ou SID do grupo, que é o valor exclusivo usado para identificar o grupo como uma entidade de segurança.

Grupos são objetos fundamentais no AD que podem ser usados ​​para agrupar outros objetos e facilitar o gerenciamento de direitos e acesso. Reserve um tempo para estudar as diferenças entre os tipos e escopos de grupo. Reserve um tempo para estudar as diferenças entre os tipos e escopos de grupo. Esse conhecimento é útil para administrar AD, bem como para entender as relações entre grupos no mesmo e em diferentes domínios e quais informações podem ser enumeradas durante a fase de reconhecimento de um teste de penetração. Compreender como diferentes tipos de grupos podem ser utilizados para realizar ataques em um único domínio e além dos limites de confiança é um excelente conhecimento para se ter. Nós nos aprofundamos nos Grupos nesta seção, agora vamos examinar as diferenças entre Direitos e Privilégios.