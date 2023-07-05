
## Conceitos básicos e fundamentais de S.O

Antes de definir o SO, um sistema computacional consiste de:
- Um ou mais processadores(cores)
- Memória principal
- Discos, impressoras, teclado, monitor, interfaces de redes e outros dispositivos de E/S

## Importância do SO

###### Aplicação sem SO

Vamos entender inicialmente, qual a importância desse sistema operacional, por que ser considerado um software básico?

Ele é considerado básico, primeiramente, porque ele tem a funcionalidade de prover um suporte para as aplicações, de forma com que o sistema operacional possa desempenhar uma interface entre o hardware e as aplicações. Ou seja, ele atua como um tradutor genérico, de forma com que a aplicação possa conversar com o dispositivo de hardware, podendo ser memória, processador, entre outros.

A aplicação sem um sistema operacional, tem entre alguns dos seus problemas:
- Gasto maior de tempo de programação
- Aumento da dificuldade
- Usuário preocupado com detalhes de hardware

##### Aplicação com SO

Com o sistema operacional, há uma:
- Maior racionalidade, uma vez que a funcionalidade da aplicação vai ser incorporada na camada de aplicação e as funcionalidades inerentes a cada dispositivo de hardware fica de responsabilidade do sistema operacional
- Maior dedicação aos problemas de alto nível
- Maior portabilidade

Nesse caso, nós temos uma máquina multinível, pois o sistema operacional é quem vai prover toda a funcionalidade básica(daí chamado de software básico), de forma que as aplicações vão ficar preocupadas apenas com a funcionalidade do sistema projetado.

![[Pasted image 20230705173132.png]]

## Definição de SO

Um SO é um programa, ou conjunto de programas, interrelacionados cuja finalidade é agir como:
- Intermediário (ou interface) entre o usuário e o hardware
- Gerenciador de recursos

![[Pasted image 20230705173301.png]]

Assim sendo, um sistema operacional têm que ser voltado para uma determinada plataforma, dessa forma, não existe um sistema operacional genérico para todas as plataformas. Por isso, existe uma versão do Unix para sistemas HP, IBM e o próprio Linux que é voltado para arquiteturas Intel.

Pelo fato de ser um software grande, também há problemas, como a de eficiência.

Vantagens do sistema operacional:
- Apresentar uma máquina mais flexível;
- Permitir o uso eficiente e controlado dos componentes de hardware;
- Permitir o uso compartilhado e protegido dos diversos componentes de hardware e software, por diversos usuários;

## Interação com o SO

O usuário pode interagir com o SO através de uma linguagem de comunicação especial, chamada de "linguagem de comando".
Ex: JCL (Job Control Language)

O usuário vai necessitar interagir com o sistema operacional e nesse caso o sistema operacional deve prover um mecanismo para essa comunicação. Existem três mecanismos:
- JCL (Interação textual)
- Gráfica

###### E pelos programas dos usuários?

Essa comunicação ocorre da invocação de serviços do SO por meio das "chamadas ao sistema operacional".
Chamadas de Sistema (system calls) permitem um controle mais eficiente sobre as operações do sistema. Cada funcionalidade implementada no sistema operacional vai possuir um nome, por exemplo: fork(criar um processo), close(fechar um arquivo)

## Formas de processamento do SO

- Serial (Monoprogramação): Recursos alocados a um único programa
- Concorrente (Multiprogramação): Recursos dinamicamente reassociados entre uma coleção de programas em diferentes estágios

# Histórico

### 1a Geração 1945/1955

- Computadores a Válvula
- Ausência de um S.O
- Programação em linguagem de máquina, ex: Colossus Mark I

### 2a Geração 1955/1965

- Invenção e uso dos transistores
- Uso da linguagem Assembly e FORTRAN
- SOs do tipo lote (batch)

### 3a Geração 1965/1980

- Circuitos integrados
- Multiprogramação
- Time-sharing
- Sistema OS/360 (IBM): 1o. a user circuitos SSI

### 4a Geração 1980/1990

- Circuitos Integrados com alta escala de integrçaão (LSI - Large Scale Integration)
- SO para micros (MS-DOS e Windows)
- Difusão da Internet

### 5a Geração 1990/Hoje

- Difusão da Internet
- SO com suporte para TCP/IP
- Cliente/Servidor
- Sistemas de Tempo-real
- Computação ubíqua
- Internet das Coisas
- CPS, etc.