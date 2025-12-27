# Archlinux Doc!
Minha documentação para o Archlinux, baseando-se em fontes como a wiki do [Arch](https://wiki.archlinux.org/title/Main_page).

***Atenção: Documento exclusivo para dispositivos EFI***

## Divisão das etapas
1. Pré-Instalação
2. Instalação
3. Particionamento do disco
4. Sistema e Pacotes
5. Usuário
6. Bootloader
7. Pós-configurações

## Pré-Instalação
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
Neste exemplo, irei utilizar um site.

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

# Particionando
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


