
Bash é a linguagem de script que usamos para nos comunicar com o sistema operacional baseados em Unix e fornecer comandos ao sistema. Desde maio de 2019, o Windows fornece um subsistema Windows para Linux que nos permite usar o Bash em um ambiente Windows. É essencial dominar o idioma para trabalhar com eficiência com ele. A principal diferença entre linguagens de script e de programação é que não precisamos compilar o código para executar a linguagem de script, ao contrário das linguagens de programação. 

Como testadores de penetração, devemos ser capazes de trabalhar com qualquer sistema operacional, seja Windows ou baseado em Unix. A eficiência depende principalmente do conhecimento dos sistemas, principalmente na área de escalonamento de privilégios. Em sistemas baseados em Unix, é essencial aprender a usar o terminal, filtrar dados e automatizar esses processos. Especialmente em grandes redes corporativas baseadas em Unix, teremos que lidar com grandes quantidades de dados. Temos que classificar e filtrar adequadamente para determinar possíveis lacunas e informações o mais rápido possível.

Também é essencial aprender a combinar vários comandos e trabalhar com resultados individuais. É aqui que entra o script, aumentando nossa velocidade e eficiência. Como uma linguagem de programação, uma linguagem de script tem quase a mesma estrutura, que pode ser dividida em:

- Input/Output
- Arguments, Variables & Arrays
- Conditional Execution
- Arithmetic
- Loops
- Comparison operators
- Functions

Muitas vezes é comum automatizar alguns processos para não repeti-los o tempo todo ou processar e filtrar uma grande quantidade de informações. Em geral, um script não cria um processo, mas é executado pelo interpretador que executa o script, no caso, o Bash. Para executar um script, temos que especificar o interpretador e dizer qual script ele deve processar. Tal chamada se parece com isto:

```shell-session
casluxd@htb[/htb]$ bash script.sh <optional arguments>
```

```shell-session
casluxd@htb[/htb]$ sh script.sh <optional arguments>
```

```shell-session
casluxd@htb[/htb]$ ./script.sh <optional arguments>
```

Vamos olhar para tal script e ver como eles podem ser criados para obter resultados específicos. Se executarmos esse script e especificarmos um domínio, veremos quais informações esse script fornece.

```shell-session
casluxd@htb[/htb]$ ./CIDR.sh inlanefreight.com

Discovered IP address(es):
165.22.119.202

Additional options available:
	1) Identify the corresponding network range of target domain.
	2) Ping discovered hosts.
	3) All checks.
	*) Exit.

Select your option: 3

NetRange for 165.22.119.202:
NetRange:       165.22.0.0 - 165.22.255.255
CIDR:           165.22.0.0/16

Pinging host(s):
165.22.119.202 is up.

1 out of 1 hosts are up.
```

Agora, vamos examinar esse script em detalhes e lê-lo linha por linha da melhor maneira possível. Nas próximas seções, veremos e analisaremos todas as partes desse script.

```bash
#!/bin/bash

# Check for given arguments
if [ $# -eq 0 ]
then
	echo -e "You need to specify the target domain.\n"
	echo -e "Usage:"
	echo -e "\t$0 <domain>"
	exit 1
else
	domain=$1
fi

# Identify Network range for the specified IP address(es)
function network_range {
	for ip in $ipaddr
	do
		netrange=$(whois $ip | grep "NetRange\|CIDR" | tee -a CIDR.txt)
		cidr=$(whois $ip | grep "CIDR" | awk '{print $2}')
		cidr_ips=$(prips $cidr)
		echo -e "\nNetRange for $ip:"
		echo -e "$netrange"
	done
}

# Ping discovered IP address(es)
function ping_host {
	hosts_up=0
	hosts_total=0
	
	echo -e "\nPinging host(s):"
	for host in $cidr_ips
	do
		stat=1
		while [ $stat -eq 1 ]
		do
			ping -c 2 $host > /dev/null 2>&1
			if [ $? -eq 0 ]
			then
				echo "$host is up."
				((stat--))
				((hosts_up++))
				((hosts_total++))
			else
				echo "$host is down."
				((stat--))
				((hosts_total++))
			fi
		done
	done
	
	echo -e "\n$hosts_up out of $hosts_total hosts are up."
}

# Identify IP address of the specified domain
hosts=$(host $domain | grep "has address" | cut -d" " -f4 | tee discovered_hosts.txt)

echo -e "Discovered IP address:\n$hosts\n"
ipaddr=$(host $domain | grep "has address" | cut -d" " -f4 | tr "\n" " ")

# Available options
echo -e "Additional options available:"
echo -e "\t1) Identify the corresponding network range of target domain."
echo -e "\t2) Ping discovered hosts."
echo -e "\t3) All checks."
echo -e "\t*) Exit.\n"

read -p "Select your option: " opt

case $opt in
	"1") network_range ;;
	"2") ping_host ;;
	"3") network_range && ping_host ;;
	"*") exit 0 ;;
esac
```

Como podemos ver, comentamos aqui várias partes do script nas quais podemos dividi-lo.

1. Verifique os argumentos fornecidos: Na primeira parte do script, temos uma instrução if-else que verifica se especificamos um domínio que representa a empresa-alvo.
2. Identifique o intervalo de rede para o(s) endereço(s) IP especificado(s): Aqui criamos uma função que faz uma consulta "whois" para cada endereço IP e exibe a linha para o intervalo de rede reservado e a armazena no CIDR.txt.
3. Efetuar ping no(s) endereço(s) IP descoberto(s): Esta função adicional é usada para verificar se os hosts encontrados podem ser acessados ​​com os respectivos endereços IP. Com o For-Loop, fazemos ping em cada endereço IP no intervalo de rede e contamos os resultados.
4. Identifique o(s) endereço(s) IP do domínio especificado: Como primeira etapa desse script, identificamos o endereço IPv4 do domínio que nos foi retornado.
5. Opções disponíveis: Em seguida, decidimos quais funções queremos usar para obter mais informações sobre a infraestrutura.