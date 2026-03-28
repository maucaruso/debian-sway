# Debian Sway

Este projeto visa configurar o Debian 13 Trixie + SwayWM para meu Thinkpad T14 GEN 2 (AMD)

## Configuração inicial:

- Instale o Debian em sua versão CLI, sem interface gráfica (Ou na versão gnome caso queira mais praticidade)
  - Lembre-se de não definir uma senha para o usuário root, pois assim nosso usuário será adicionado ao grupo root
  
- Para configurar o teclado para o layout ABNT2, acesse ```/etc/default/keyboard``` e deixe o arquivo da seguinte forma:
  ```bash
  XKBMODEL="thinkpad60"
  XKBLAYOUT="br"
  XKBVARIANT="abnt2"
  XKBOPTIONS=""
  ```
  
- Agora, acesse ```/etc/apt/sources.list``` e comente a linha iniciada em ```deb cdrom...```

- Atualize o sistema:
  ```bash
  apt update && apt upgrade
  ```

- Reinicie o sistema

## Setup ambiente gráfico:

- Instale as dependências do Sway
  ```bash
  sudo apt install sway swaybg swaylock swayidle \
  waybar wlogout libwayland-dev wofi mako-notifier \
  brightnessctl pavucontrol blueman xdg-desktop-portal-wlr mate-polkit \
  network-manager network-manager-gnome \
  htop fastfetch fonts-font-awesome kitty git
  ```
  
- Habilite o suporte à Flatpaks
  ```bash
  sudo apt install flatpak
  flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
  ```

- Vamos configurar o tlp para fazer o gerenciamento de energia
  ```bash
  sudo apt install tlp tlp-rdw acpi-call-dkms
  sudo systemctl enable tlp && sudo systemctl start tlp
  flatpak install flathub com.github.d4nj1.tlpui
  ```
  
- Vamos adicionar o browser
  ```bash
  flatpak install flathub com.brave.Browser
  ```

## Pós-instalação:

- Acesse ```/etc/apt/sources.list``` e deixe o arquivo da seguinte forma:
  ```bash
  deb https://deb.debian.org/debian/ trixie main contrib non-free non-free-firmware
  #deb-src https://deb.debian.org/debian/ trixie main contrib non-free non-free-firmware

  deb https://security.debian.org/debian-security trixie-security main contrib non-free non-free-firmware
  #deb-src https://security.debian.org/debian-security trixie-security main contrib non-free non-free-firmware

  deb https://deb.debian.org/debian trixie-updates main contrib non-free non-free-firmware
  #deb-src https://deb.debian.org/debian trixie-updates main contrib non-free non-free-firmware

  deb https://deb.debian.org/debian trixie-backports main contrib non-free non-free-firmware
  #deb-src https://deb.debian.org/debian trixie-backports main contrib non-free non-free-firmware
  ```
  - Lembre-se de fazer uma cópia do arquivo original como backup ```mv /etc/apt/sources.list /etc/apt/sources.list.original```
  - Já estamos deixando o repositório backports habilitados, então poderemos ter versões mais recentes de pacotes como drivers de vídeo, kernel e etc caso necessário, utilize backports caso realmente seja necessário, ao contrário utilize os pacotes padrões. Para instalar um pacote do repo backports para executar ```apt install -t trixie-backports <nome-pacote>```
  
- Atualize o sistema:
  ```bash
  apt update && apt upgrade
  ```

- Instale o pacote de firmware microcode de acordo com seu processador
  ```bash
  apt install amd64-microcode # caso amd
  apt install intel-microcode # caso intel
  ```

- Vamos adicionar e habilitar o firewall no sistema:
  ```bash
  sudo apt install ufw gufw
  sudo ufw enable
  ```

- Reinicie o sistema

## Otimizações de desempenho:

- Vamos instalar o kernel a partir do repositório backports, assim, teremos uma versão mais atualizada do kernel linux, que pode oferecer mais compatibilidade com hardware novo e possíveis melhorias e otimizaçoes de desempenho
  ```
  apt install -t trixie-backports linux-image-amd64 linux-headers-amd64
  ```

- Caso seu sistema tenha mais de 8gb de ram, é recomendado fazer a seguinte configuração, basicamente vamos ajustar os parametros ```vm.swappiness``` (0 evita swap ao máximo / 100 usa swap agressivamente) e ```vm.vfs_cache_pressure``` (< 100 mantém mais cache / > 100 limpa cache com mais frequência) para evitar ao máximo o uso do swap e manter mais arquivos do filesystem em cache, isso dará um ganho de performance ao sistema na leitura de arquivos, para isso acesse ```/etc/sysctl.d/99-swappiness.conf``` e deixe com a seguinte configuração:
  ```bash
  vm.swappiness=10 # default 60
  vm.vfs_cache_pressure=50 # default 100
  ```

- Caso utilize um processador Ryzen para laptops, você pode instalar a ferramenta **RyzenAdj** para melhorar o desempenho de sua CPU e GPU integrada, com ela é possível controlar o TDP do processador e GPU (integrada) do processador, fazendo com que eles trabalhem nos clocks máximos entregando o máximo de desempenho em tarefas pesadas como jogos.
  - Siga o guia de instalaçao [RyzenAdj](https://github.com/FlyGoat/RyzenAdj)
  - Fiz [esse tutorial](https://www.notion.so/Otimizando-Processadores-AMD-1e73aad84342806b90dbfa3d0aed4f74) explicando como utilizar a ferramenta

## Preparando sistema para jogos:

- Instale os pacotes flatpak necessários para a Steam e Heroic Games Launcher:
  ```bash
  flatpak install com.valvesoftware.Steam com.valvesoftware.Steam.Utility.vkBasalt \
  com.heroicgameslauncher.hgl com.github.Matoking.protontricks com.github.tchx84.Flatseal \
  io.github.radiolamp.mangojuice org.vinegarhq.Sober
  ```
  - Se for necessário, utilizando o FlatSeal libere as permissões do pacote flatpak do Steam para acessar outras unidades de disco.

## Referências:

[Guia de pós-instalação do Debian 13 "Trixie"](https://www.blogopcaolinux.com.br/2025/08/Guia-pos-instalacao-Debian-13-Trixie.html)

[Roteiro de pós-instalação do Debian para arte e criação](https://github.com/eddiecsilva/debian-post-install)

[Guia atualização Kernel](https://tuxinit.com/update-kernel-debian/)

[Debian Sway Thinkpad](https://github.com/Tong-ST/debian_sway)

[Fix Flameshot Issues](https://bbs.archlinux.org/viewtopic.php?id=284847)