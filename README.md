# Archlinux Doc!
Minha documentação para o Archlinux, baseando-se em fontes como o guia de instalação da wiki do [Arch Linux](https://wiki.archlinux.org/title/Installation_guide).

***Atenção: Documento exclusivo para dispositivos EFI***

## Divisão das etapas
1. Pré-Instalação
2. Instalação
3. Particionamento do disco
4. Mirror e Sistema
5. Configuração e Usuário
6. Bootloader
7. Pós-configuração

# Pré-Instalação
Para instalar o sistema operacional Arch em alguma máquina será preciso dos seguintes recursos:

1. Um computador com acesso à internet
2. Um pendrive (mínimo: 4Gb)

Além disto, precisaremos do software chamado [ventoy](https://www.ventoy.net/en/download.html), um aplicativo capaz de transformar um pendrive em um dispositivo de multiboot (carregar múltiplos sistemas operacionais em um único pendrive)

# Instalação
Após dá boot com o Arch, 
será preciso verificar a conexão com a internet.

## Conexão com a rede

**Obs:** Caso esteja em máquina virtual ou utilize cabo de ethernet, pode pular este procedimento, porque o archlinux irá detectar automaticamente.

Um dos comandos mais rápidos e eficazes de verificar a conexão com a internet é verificar se o seu computador testa a conectividade com algum destinatário (site, servidor ou outro pc).
Neste exemplo, irei utilizar o site da Google.

`ping google.com`

Será preciso utilizar o iwctl que já vem por padrão com o Arch.

`iwctl` o seu terminal mudará para o iwctl que serve para digitar os comandos exclusivos do iwctl.

`device list` mostrará todos os dispositivos que serve para se conectar a internet, geralmente o nome será **wlan0**

`station wlan0 scan` com o dispositivo selecionado, ele irá escanear as redes

`station wlan0 get-networks` listará todas as redes encontradas

`station wlan0 connect [Nome da Rede]` irá servir para selecionar qual rede você deseja se conectar, e após selecionar, irá abrir um menu pedindo a senha desta rede.

`exit` sai do iwctl

Após isso, verifique com o comando `ping` para verificar se está tudo funcionando como deveria.

## Modelo de teclado
Caso queira deixar o modelo do teclado `br-abnt2`, digite o seguinte comando:

`loadkeys [modelo do teclado]`

# Particionamento de Disco

Essa etapa servirá para definir quanto de swap, armazenamento e bootloader queremos.

## Particionando
Iremos utilizar a ferramenta `cfdisk`

## Partições necessárias
1. Root 
2. Boot (EFI, tamanho recomendável: 512Mb)
3. Swap (opcional, mas recomendável, tamanho recomendável: 4Gb)

**Função de cada**
1. Root: Onde todos os arquivos irão estar, incluindo o seus aplicativos e sistema operacional

2. Boot: Pequena partição que servirá para inicializar o sistema

3. Swap: Memória "RAM" de reserva, caso a sua memória RAM esteja 100% ocupada irá utilizar a swap para carregar os outros programas que não cabe na RAM, mas é extremamente lenta, porque depende do seu armazenamento.

No terminal digite `cfdisk`

**Obs:** Para especificar o disco, digite `cfdisk /dev/sdX`

Agora, crie as partições.
Após a criação das partições, especifique cada uma com o `[Type]`, e depois de especificar, escreva todas com o `[Write]`, e logo após, selecione `[Quit]`

## Formatando partições
**Dica:** Utilize o `fdisk -l` para listar todas as partições.

`mkfs.ext4 /dev/partição_root` transformar a partição root no formato ext4

`mkswap /dev/partição_swap` transforma a partição em swap

`mkfs.fat -F 32 /dev/partição_efi` transforma em partição efi

# Montando as partições

`mount --mkdir /dev/partição_efi /mnt/boot` monta a partição efi para que seja possível o boot

`swapon /dev/partição_swap` faz com que seja possível a partição swap funcionar

# Mirror e Sistema
## Mirror
Um mirror é onde, primeiro, o gerenciador de pacotes deve pegar os pacotes é bastante utilizado para aumentar a velocidade de download dos pacotes.

`nano /etc/pacman.d/mirrorlist` vá até o seu país e retire todas as #

## Sistema
`pacstrap -K /mnt [pacotes aqui]` aqui abaixo mostrará os pacotes que você deve colocar ou pacotes opcionais

1. `base` (essencial, é um metapacote)
2. `linux` (essencial, instala o kernel linux)
3. `linux-firmware` (essencial, instala os firmwares)
4. `intel-ucode` (altamente recomendável, utilize se tiver processador da Intel)
5. `amd-ucode` (altamente recomendável, utilize se tiver processador da AMD)
6. `networkmanager` (altamente recomendável, serve para se conectar a internet e entre outros afins)
7. `nano` (recomendável, serve para editar textos)
8. `base-devel` (essencial, conjunto de ferramentas de compilação)
9. `linux-headers` (essencial, fornece os arquivos de interface)
10. `vim` (recomendável, editor de texto avançado)
11. `sudo` (altamente recomendável, gerenciará as permissões)
12. `grub` (altamente recomendável, é o bootloader)
13. `efibootmgr` (altamente recomendável, ferramente para que o grub consiga conversar com a placa mãe e criar a entrada de inicialização)

# Configuração

`genfstab -U /mnt >> /mnt/etc/fstab` irá dizer como as partições vão ser montadas automaticamente toda vez que o sistema iniciar

`arch-chroot /mnt` acessa o sistema montado

## Definindo o tempo
`ln -sf /usr/share/zoneinfo/*Area*/*Local* /etc/localtime`

**Exemplo**
`ln -sf /usr/share/zoneinfo/America/Recife /etc/localtime`

`hwclock --systohc` vai assumir que o hardware do relógio está setado em UTC

## Localização
`/etc/locale.gen` utilize o editor de texto na qual você instalou (como o nano ou o vim) e retire a # na qual pertence ao local

`locale-gen` vai gerar as configurações

`/etc/locale.conf` aqui você definará a variável da linguagem

**Exemplo**
`LANG=en_US.UTF-8`

`/etc/vconsole.conf` vai setar o layout do teclado

**Exemplo**
`KEYMAP=de-latin1`

## Configuração da Rede
`/etc/hostname` aqui você irá definir o nome do seu hostname

## Initramfs
`mkinitcpio -P` reconstrói os arquivos essenciais para que seja possível com quue o sistema consiga montar a partição raiz e inicializar o kernel durante o boot

# Usuário

## Senha Root
`passwd` digite este comando e coloque a senha para o root

`useradd  -m -G wheel,audio,video -s /bin/bash [nome do seu usuário]` criará um usuário com todas as permissões

`EDITOR=nano visudo` acesse e procure por `%wheel ALL=(ALL:ALL) ALL` e delete a #, faz com que você tenha todas as permissões

# Bootloader
`grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=Arch` irá criar um arquivo que permitirá o grub

**Obs:** O `Arch` é apenas um nome, você pode colocar outro nome como por exemplo `GRUB`

`grub-mkconfig -o /boot/grub/grub.cfg` configurará o grub para dizer como o sistema deve iniciar

# Pós-configuração
Após finalizar tudo, prossiga com os seguintes passos

`exit` para sair do chroot

`umount -R /mnt` desmontará todas as pastas da forma correta

`reboot` reiniciará o sistema
