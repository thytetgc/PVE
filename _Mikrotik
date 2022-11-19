#!/bin/bash

#+----------------GERANDO LOG-----------------------+OK"
SCRIPT=`basename $0`
LOG=`echo $SCRIPT.log | sed s/'.sh'/'.log'/g`
exec &> >(tee -a "$LOG")
echo "[`date`] ==== Inicio de rotina..."
#+----------------GERANDO LOG-----------------------+OK"

Principal() {
	clear
     dir="Diretorio Atual		 : `pwd`"
	 hostname="Hostname			 : `hostname --fqdn`"
	 ip="IP						 : `wget -qO - icanhazip.com`"
     versaoso="Versao S.O.		 : `lsb_release -d | cut -d : -f 2- | sed 's/^[ \t]*//;s/[ \t]*$//'`"
	 release="Release			 : `lsb_release -r | cut -d : -f 2- | sed 's/^[ \t]*//;s/[ \t]*$//'`"
	 codename="Codename			 : `lsb_release -c | cut -d : -f 2- | sed 's/^[ \t]*//;s/[ \t]*$//'`"
	 kernel="Kernel				 : `uname -r`"
	 arquitetura="Arquitetura	 : `uname -m`"
	 echo
     echo "+-------------------------------------------------+"
     echo "|           Utilitario para rSync                 |"
     echo "+-------------------------------------------------+"
     echo "| Implantar RouterOS                      v1.00   |"
     echo "+-------------------------------------------------+"
     echo "| Escrito por:                                    |"
     echo "| Thiago Castro - www.hostlp.cloud                |"
     echo "+-------------------------------------------------+"
     echo
     echo $dir
	 echo "+-------------------------------------------------+"
	 echo $hostname
	 echo "+-------------------------------------------------+"
	 echo $ip
	 echo "+-------------------------------------------------+"
	 echo $versaoso
	 echo "+-------------------------------------------------+"
	 echo $release
	 echo "+-------------------------------------------------+"
	 echo $codename
	 echo "+-------------------------------------------------+"
     echo $kernel
	 echo "+-------------------------------------------------+"
     echo $arquitetura
	 echo "+-------------------------------------------------+"
	 echo
     echo
	 echo "Aperte <ENTER> para continuar e começar..."
	 echo
	 
#Acertando escrita com bloco de notas	 
sed -i -e 's/\r$//' *.sh

#Variaveis
version="nil"
vmID="nil"
dirtemp="/root/temp"

echo "############## Início do Script ##############

## Verificando se o diretório temporário está disponível..."
if [ -d "$dirtemp" ] 
then
    echo "-- Diretório existe!"
else
    echo "-- Criando diretório temporário em "$dirtemp"!"
    mkdir "$dirtemp"
fi

# Pergunte ao usuário sobre a versão
echo "## Preparando para download de imagem e criação de VM!"
read -p "Por favor, insira a versão do CHR para implantar (6.48.3, etc):" version

# Verifique se a imagem está disponível e baixe se necessário
if [ -f "$dirtemp"/chr-$version.img ] 
then
    echo "-- Imagem CHR está disponível."
else
    echo "-- Baixando arquivo de imagem da versão CHR $."
    cd  "$dirtemp"
    echo "---------------------------------------------------------------------------"
    wget https://download.mikrotik.com/routeros/$version/chr-$version.img.zip
    unzip chr-$version.img.zip
    echo "---------------------------------------------------------------------------"
fi

# Liste VMs já existentes e peça vmID
echo "== Exibindo lista de VMs neste hipervisor!"
qm list
echo ""
read -p "Por favor, digite o VMID livre para usar:" vmID
echo ""

# Crie um diretório de armazenamento para VM, se necessário.
if [ -d /var/lib/vz/images/$vmID ] 
then
    echo "-- O diretório VM existe! O ideal é tentar outro VMID!"
    read -p "Por favor, digite o VMID livre para usar:" vmID
else
    echo "-- Criando diretório de imagem VM!"
    mkdir /var/lib/vz/images/$vmID
fi

# Criando imagem qcow2 para CHR.
echo "-- Convertendo imagem para o formato qcow2 "
qemu-img convert \
    -f raw \
    -O qcow2 \
    "$dirtemp"/chr-$version.img \
    /var/lib/vz/images/$vmID/vm-$vmID-disk-1.qcow2

# Criando VM
echo "-- Creating new CHR VM"
qm create $vmID \
  --name chr-$version \
  --net0 virtio,bridge=vmbr0 \
  --bootdisk virtio0 \
  --ostype l26 \
  --memory 256 \
  --onboot no \
  --sockets 1 \
  --cores 1 \
  --virtio0 local:$vmID/vm-$vmID-disk-1.qcow2
echo "############## End of Script ##############"

echo "[`date`] ==== Fim da rotina..."
