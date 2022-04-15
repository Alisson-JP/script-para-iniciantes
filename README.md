#!/bin/bash

# --- Script criado por Alisson Pereira --- #

# esse script irá capturar automaticamente as informações gerais de um ambiente CentOS7:
# - versão
# - usuário
# - quando a distro foi instalada
# - status de memória
# - status de uso de disco e suas partições
# - status do tempo que a máquina está rodando
# e, irá efetuar a verificação de um item de hardening usando a documentação do CIS Control como referência

#---------------------------------------------------------------------------------------------------#
#--------------------------------------Considerações Iniciais---------------------------------------#
# a primeira linha [ #!/bin/bash ] é OBRIGATÓRIA, nela está a informação de onde o script será rodado
# todas as linhas que contém "#" no início são linhas comentadas, e não são executadas pelo script 
# (única exceção é a primeira linha) - NÃO remova o # da primeira linha; 

# Para executar esse script, siga os passos abaixo:
# 1º - Crie um documento com vi, vim, nano ou outro editor de preferênca
# exemplo, comando abaixo:
# vi script-teste.sh
# 2º - Dentro do documento, tecle "Insert" para adicionar conteúdo 
# 3º - insira todo esse conteúdo dentro do documento criado 
# (selecione esse texto todo, ctrl + c, depois no documento, ctrl + v)

# pode acontecer da sua máquina virtual não permitir o compartilhamento da área de transferência
# caso isso ocorra, uma alternativa é acessar a própria máquina via "putty" (SSH)
# o puuty vem por padrão com a área de transferência habilitada

# 4º - será necessário adicionar permissão executável para o o documento criado
# exemplo, comando abaixo (considerando que o nome do script seja o mesmo):
# chmod 755 script-teste.sh

# após permissão adicionada, o documento passa a ser, de fato, um script
# para verificar a permissão de execução concedida, pode-se fazê-lo com o comando
# ls -la

# 5º - para executá-lo, coloque o endereço completo a partir da pasta origem,
# ou "./" se estiver na pasta destino
# exemplo (se estiver no mesmo diretório onde o script foi criado)
# ./script-teste.sh
# caso esteja em outro local, pode-se digitar todo o caminho, exemplo:
# /home/script-teste.sh

# para não precisar colocar o endereço completo todas as vezes, é necessário incluí-lo
# no endereçamento PATH
# echo $PATH
# export PATH=$PATH:directory

# após rodar o script, confira o documento criado, que recebeu as informações coletadas pelo script

# A tabela abaixo [ Cores ] modifica a formatação de cor original do texto
# para utilizar deve-se inserir os valores de formatação em variáveis
# e chamar a variável antes do texto que se pretende modificar
# [ ${nomeDaCorDesejada} ]
# as chaves {} entre a variável protege o conteúdo e a própria variável de cor


#------------------Cores-------------------#
red='\033[1;31m'
green='\033[1;32m'
yellow='\033[1;33m'
blue='\033[1;34m'
purple='\033[1;35m'
white='\033[1;37m'
#------------------------------------------#


# Iremos automatizar a criação de um novo documento sempre que o script for executado
# utilizando a data como forma de diferenciação
# esse doc [ log_list_date ] receberá todas as informações desse script

# o comando [ date ] será apresentado utilizando a máscara: dia (%d), mês (%m), e assim por diante
# será criando então, no diretório [ /home ] o arquivo com o nome supracitado
# o comando [ ls -t | head -1 ] busca na pasta/diretório o nome do último arquivo criado
# a variável [ doc 1 ] receberá o nome do último arquivo criado, uma vez que será o nome + a data e hora de execução


#-------------Criação de log file----------#
arq=`date +%d%m%y%H%M`					
touch /home > "log_list_$arq"
doc1=`ls -t | head -1`
#------------------------------------------#


# no caso acima, o arquivo que receberá as informações do script é chamdado de log_list + a data e hora atual
# a data e hora será inserida automaticamente ao executar o script
# o prefixo do nome do documento ( log_list ) pode ser alterado conforme necessário, alterando-o na linha 83


# será criado duas variáveis para receber o resultado adquirido de cada comando pelo script
# na prática, para cada teste, se o teste retornar positivo, será adicionado 1 em [ okay ]
# em contrapartida, será adicionado 1 em [ verificar ] sempre que o resultado retornar negativo


#--------- Status de Verificação ----------#
okay=0
verificar=0
#------------------------------------------#


# abaixo, segue a senha escolhida para rodar o script, assim que solicitado
# a senha faz alusão a Teste + Script

#------------Senha para executar o script-----------#
senha_de_acesso=testeScript							# essa senha pode ser alterada ( após o símbolo de = )
#---------------------------------------------------#

# Início
# limpe a tela
clear

echo -e "$green------- Coloque aqui o nome da sua empresa, por exemplo ---------$white"
echo -e "$green------- Coloque aqui o setor que vc trabalha, por exemplo -------$white"
echo -e "$green------- Todos os direitos reservados ----------------------------$white"

# peça o nome do usuário, crie uma variável para receber esse nome e
# imprima na tela esse pedido de identificação
# uma linha echo vazia serve para pular uma linha entre um comando e outro

echo
echo "Digite seu nome: "
read nome1
# variável [ $nome1 ] foi criada
# envie para o arquivo log [ $doc1 ] a informação obtida
echo "" >> $doc1
# a linha acima serve para pular uma linha no arquivo de log
echo "O usuário $nome1 iniciou o processo." >> $doc1
# todo o trecho acima entre as aspas foi enviado ao doc1
# esse envio foi feito através de >>
# quando utilizamos >> é adicionado uma nova linha do endereço de destino
# quando utilizamos apenas um > é substituído o conteúdo anterior


# peça a senha, armazene essa senha recebida em uma variável
# para que a digitação da senha não apareça na tela, ou seja, modo silencioso, utilize o "-s"
printf "Digite a senha: "
read -s senha1
# a variável [ $senha1 ] foi criada
# limpe a tela após receber a senha
clear

# confira a senha usando condicionais (if, then, else)
if [ $senha1 = $senha_de_acesso ];					# if = se
then									# then = então
	echo -e "${green}Autenticação confirmada!${white}"
	sleep 2
else									# else = senão
	echo -e "${red}Falha na autenticação!${white}"
	sleep 2								# sleep = tempo de descanso até o próximo comando
	exit 1
fi									# fi = "if" ao contrário, serve para indicar onde a condicional termina

# o parâmetro -e em [ echo -e ] serve para adicionar os carateres especiais, 
# quanto para adicionar as formatações de cores


# mostre uma mensagem de processo iniciando
echo 
echo -e "${yellow}Iniciando..."
echo
sleep 2
echo -e "${blue}Lendo todo o sistema..."
echo
sleep 2
echo -e "${green}Executando!${white}"
echo

# uma linha com o comando echo vazio, irá pular uma linha na tela, para melhorar a visualização

# Monitorando o sistema
# mostre na tela que será feito uma varredura de informações sobre o sistema
echo "Iniciando coleta de informações sobre o sistema..."
# envie para o log o aviso de início
echo "" >> $doc1
echo "Iniciado varredura de informações do sistema." >> $doc1
sleep 2
# o comando [ sleep ] retém o script pelo tempo escolhido, até a executar a próxima linha

# a partir desse ponto, iremos criar uma variável que irá receber o comando devido
# o comando precisa estar entre crases [ ` ]

# versão do servidor
echo
versao_server=`uname -m`
# acima, criamos a variável "versao_server", que recebeu o comando "uname -m"
echo -n -e "O sistema opera atualmente com a versão ${purple}$versao_server${white}"
# o parâmetro [ -n ] na linha echo, serve para não pular de linha e ativar o próximo comando
echo "" >> $doc1
echo "Sistema atual: $versao_server" >> $doc1
sleep 3
echo


# pesquise quem é o usuário e mostre na tela
echo
user_logado=`whoami`
echo -n -e "O usuário logado no momento é ${purple}$user_logado${white}"
echo "" >> $doc1
echo "O usuário fez a checagem logado como: $user_logado" >> $doc1
sleep 3


# pesquise quando a distro foi instalada
echo
data_de_insta=`ls -lct /etc | tail -1 | awk '{print $6, $7, $8}'`
# criada variável [ data_de_insta ] contendo a informação
echo
echo -n "Essa distribuição foi instalada em: "
echo -e "${purple}$data_de_insta${white}"
echo "" >> $doc1
echo "Data de instalação dessa distribuição: " >> $doc1
echo "$data_de_insta" >> $doc1
sleep 3


# status de memória
echo
status_de_memoria=`free -m`
echo "Status atual de memória livre "
echo -e "${purple}$status_de_memoria${white}"
echo "" >> $doc1
echo "Status de memória livre: " >> $doc1
echo "$status_de_memoria" >> $doc1
sleep 3


# status de uso de disco e suas partições
echo
status_uso_de_disco=`df -h`
echo "Status do uso de disco e partições: "
echo -e "${purple}$status_uso_de_disco${white}"
echo "" >> $doc1
echo "Status do uso de disco e partições: " >> $doc1
echo "$status_uso_de_disco" >> $doc1
sleep 3


# status do tempo que a máquina está rodando
echo
status_tempo_running=`uptime`
echo "O sistema está rodando há:"
echo -e "${purple}$status_tempo_running${white}"
echo "" >> $doc1
echo "O sistema está rodando há:" >> $doc1
echo "$status_tempo_running" >> $doc1
sleep 3


# mostre uma mensagem de término de coleta das informações iniciais, apresentando o usuário
echo
echo "$nome1, as informações iniciais necessárias foram coletadas."
echo "" >> $doc1
echo "As informações iniciais necessárias foram coletadas." >> $doc1


# criar um loop para retornar a pergunta inicial, caso a resposta seja inválida
# esse loop será a função [ quest1 ]; caso a resposta seja inválida, o script irá chamar
# a função, reiniciando o loop (linha 310)

function quest1 {
	echo "Deseja prosseguir? [ s / n ]"
	read -n 1 -s resp1
		case $resp1 in
			S|s)
				echo -e "${green}Iniciando Etapa 1.${white}"
				;;
			N|n)
				echo -e "${red}Processo interrompido.${white}"
				sleep 3
				exit 1
				;;
			*)
				echo -e "${yellow}Opção inválida. Digite [ s ] para prosseguir e [ n ] para sair.${white}"
				sleep 2
				clear
				quest1
				;;
		esac
}

# verificar a opção digitada pelo usuário
# abaixo temos o case, que é uma ferramenta de comparação, ou seja, caso a resposta seja...
# a sintaxe do case é diferenciada, exige dois [ ;; ] entre uma comparação e outra
# o case finaliza com a palavra case ao contrário [ esac ]

# pergunte ao usuário se deseja prosseguir
echo "Deseja prosseguir com o Hardening [s/n]?"
read -n 1 -s resp1
# criado a variável [ $resp1 ]

case $resp1 in
	S|s)
	echo
	echo -e "${green}Iniciando Etapa 1.${white}"
	echo
	;;
	N|n)
	echo
	echo -e "${red}Processo interrompido.${white}"
	sleep 3
	exit 1
	;;
	*)
	echo -e "${yellow}Opção inválida. Digite [ s ] para prosseguir e [ n ] para sair. ${white}"
	sleep 2
	clear
	quest1
	;;
esac


#exemplo de condicional a seguir
## 
## v1_ =` `					# o comando em uma variável precisa estar entre crases [ ` ]
## if [ $? -eq 0 ]; then			# - eq significa "igual a / equivalente"
##	  echo " v1 okay"
##	  echo "" >> $doc1			# duplo >> irá adicionar novas linhas no destino, e não substituí-las
##	  echo " okay" >> $doc1
## else
##	  echo " Verificar  v1" >> $doc1
## fi

# Agora iremos testar um item do documento oficial de segurança do CIS Control
# Verificação dos pacotes/serviços existentes
# relacionando com os pacotes que o CIS Control recomenda verificar
# Center for Internet Security - CentOS Linux 7 Benchmark – v3.1.2 – 31/08/2021

#----------------------------------------------------------------------------------------------------#
#---------------------------------------Funcionalidade do script-------------------------------------#
# v1 = testar a primeira condição, segundo o CIS Control
# v2 = testar a segunda condição, segundo o CIS Control, e assim por diante (v3, v4, etc...)
# [ $? ] corresponde ao valor do último comando executado, onde:
# [ $? -eq 0 ] significa que o comando retornou com o resultado verdadeiro/instalado/enable, etc;
# [ $? -eq 1 ] significa que o comando retornou com o resultado falso/não instalado/ não ativado, etc;
# [ $? -eq 2 ] significa que o comando retornou com o resultado de erro, ou alguma condição especial


# 1.1.1.1 Certifique-se de que a montagem dos sistemas de arquivos cramfs esteja desabilitada
echo -e "${yellow}1.1.1.1${white} Certifique-se de que a montagem dos sistemas de arquivos cramfs esteja desabilitada"
v1_cramfs=`modprobe -n -v cramfs | grep -E '(cramfs|install)'`
if [ $? -eq 0 ]; then
	echo "" >> $doc1
	echo "1.1.1.1 v1_cramfs okay" >> $doc1
	echo -e "${green}v1_cramfs okay${white}"
	okay=$((okay + 1))
else
	echo "1.1.1.1 Verificar v1_cramfs" >> $doc1
	echo -e "${red}1.1.1.1 Verificar v1_cramfs${white}"
	verificar=$((verificar + 1))
fi

echo
echo -e "${green}Execução de teste de script para CentOS Linux 7 concluída com sucesso!!!${white}"
echo
echo -e "Relatório"
echo -e "${blue}Itens conforme documentação: $okay${white}" 
echo -e "${yellow}Itens que demandam verificação: $verificar${white}"

# após rodar o script, confira o documento criado, que recebeu as informações coletadas pelo script
echo
echo -e "${green} Dê um cat no arquivo log que foi criado, e verifique as informações coletadas!!!${white}"
echo
echo -e "${green} Parabéns Jovem Padawan!!!${white}"

