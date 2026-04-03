# Debian Sway

Este projeto visa configurar o Debian 13 Trixie para meu Thinkpad T14 GEN 2 (AMD)

## Configuração inicial:

- Instale o Debian em sua versão XFCE para usar com o I3 ou em sua versão Gnome caso deseje utilizar com o Sway
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

## Setup 1 - I3 + XFCE:

- Instale as dependências do i3
  ```bash
  sudo apt install --no-install-recommends i3
  sudo apt install rofi bluez blueman lightdm-gtk-greeter-settings
  ```

- Abra ```Session and Startup```

- Selecione a Aba ```Application Autostart``` e adicione
  ```bash
  Name: i3
  Description: Window Manager
  Command: i3
  Trigger: On login
  
  Name: xfsettingsd
  Description: Xfwm Theme Workaround
  Command: xfsettingsd --replace
  Trigger: On login
  ```
  
- Agora navegue até a aba ```Current Session``` e modifique s serviços a seguir:
  ```
  Program: xfdesktop
  Restart Style: Never
  
  Program: xfwm4
  Restart Style: Never
  ```
  
- Abra ```Keyboard``` e delete todos os shortcuts

## Setup 2 - Sway + Gnome

Por estarmos configurando o Sway em cima de uma instalação Gnome, muitos utilitários e ferramentas já vem pré-instalados e configurados, um exemplo é o bluez (para bluetooth), também vamos aproveitar o utilitário de login do próprio gnome para iniciar a sessão sway, logo, só teremos que instalar alguns pacotes adicionais para que tudo funcione corretamente no Sway.

Vale ressaltar também que nesse setup, a idéia é utilizar muitas ferramentas do próprio gnome como o nautilus como file manager, o editor de texto padrão e etc, isso simplifica bastante a configuração do ambiente.

Outra vantagem dessa estrutura é, caso algum programa ou tarefa não esteja muito legal no Sway, você pode a qualquer momento alternar para o ambiente Gnome.

- Instale as dependências necessárias
  ```bash
  sudo apt install sway swaybg swaylock swayidle \
  waybar wlogout libwayland-dev wofi mako-notifier \
  brightnessctl pavucontrol blueman xdg-desktop-portal-wlr mate-polkit \
  grim slurp network-manager-gnome fonts-font-awesome kitty git
  ```
  
- Clone este repositório e mova os arquivos de configuração:
  ```bash
  cp -r debian-sway/.config/sway/ ~/.config/
  cp -r debian-sway/.config/waybar/ ~/.config/
  cp -r debian-sway/.config/wofi/ ~/.config/
  cp -r debian-sway/.config/kitty ~/.config/
  ```
  
- Instale as fontes utilizadas pelo projeto:
  ```bash
  wget -P ~/.local/share/fonts https://github.com/ryanoasis/nerd-fonts/releases/download/v3.0.2/JetBrainsMono.zip
  cd ~/.local/share/fonts
  unzip JetBrainsMono.zip
  rm JetBrainsMono.zip
  fc-cache -fv
  ```
  
- Ainda na sessão Gnome, altere o tema para Dark Mode para que nossas aplicações Gnome fiquem escuras ao alternarmos para o Sway

- Faça Logout e na tela de login, seleciona a sessão Sway

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
  
- Habilite o suporte à Flatpaks
  ```bash
  sudo apt install flatpak
  flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
  ```

- Vamos configurar o tlp para fazer o gerenciamento de energia
  ```bash
  sudo apt install -t trixie-backports tlp tlp-rdw
  sudo apt install acpi-call-dkms
  sudo systemctl enable tlp && sudo systemctl start tlp
  flatpak install flathub com.github.d4nj1.tlpui
  ```
  
- Vamos adicionar o browser
  ```bash
  flatpak install flathub com.brave.Browser
  ```
  
- Instale alguns programas úteis (opcionais)
  ```bash
  sudo apt install htop fastfetch wdisplays mesa-utils vulkan-tools
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

## Ambiente de trabalho:

- O comando abaixo instala apps úteis para o meu fluxo de trabalho e uso pessoal:
  ```bash
  flatpak install flathub \
  com.getpostman.Postman \
  io.dbeaver.DBeaverCommunity \
  com.obsproject.Studio \
  org.flameshot.Flameshot \
  com.github.tchx84.Flatseal \
  org.gnome.Boxes \
  md.obsidian.Obsidian \
  me.timschneeberger.jdsp4linux \
  org.videolan.VLC \
  org.chromium.Chromium \
  com.google.Chrome \
  org.gnome.DejaDup
  ```
  
- Instale o (spotify_player)[https://github.com/aome510/spotify-player]
  ```bash
  sudo apt install curl
	sudo apt install libssl-dev libasound2-dev libdbus-1-dev
	cargo install spotify_player --features pixelate
  ```
  
- Instale também Docker, NVM e PHP e VS Code (pacote .deb pois versão flatpak tem muitos problemas)

## Preparando sistema para jogos:

- Instale os pacotes flatpak necessários para a Steam e Heroic Games Launcher:
  ```bash
  flatpak install com.valvesoftware.Steam com.valvesoftware.Steam.Utility.vkBasalt \
  com.heroicgameslauncher.hgl com.github.Matoking.protontricks com.github.tchx84.Flatseal \
  io.github.radiolamp.mangojuice org.vinegarhq.Sober \
  org.freedesktop.Platform.VulkanLayer.MangoHud
  ```
  - Se for necessário, utilizando o FlatSeal libere as permissões do pacote flatpak do Steam para acessar outras unidades de disco.

- Instale o seguinte pacote para que a Steam possa detectar dispositivos como controles:
  ```bash
  sudo apt install steam-devices  
  ```

## Referências:

[Guia de pós-instalação do Debian 13 "Trixie"](https://www.blogopcaolinux.com.br/2025/08/Guia-pos-instalacao-Debian-13-Trixie.html)

[Roteiro de pós-instalação do Debian para arte e criação](https://github.com/eddiecsilva/debian-post-install)

[Guia atualização Kernel](https://tuxinit.com/update-kernel-debian/)

[Guia configuração XFCE + I3](https://gist.github.com/fathulfahmy/61910e84b99b38009ad9268811e4aa2a)

[Debian Sway Thinkpad](https://github.com/Tong-ST/debian_sway)

[Sway Rice](https://github.com/ziap/dotfiles)

[Fix Flameshot Issues](https://bbs.archlinux.org/viewtopic.php?id=284847)

[Nerf Fonts](https://www.nerdfonts.com/cheat-sheet)

[Monitorar uso de GPU](https://www.reddit.com/r/swaywm/comments/ncjpfz/how_to_add_gpu_usage_to_waybar/)

[Como instalar Debian 12 com BTRFS corretamente para usar o Timeshift](https://www.youtube.com/watch?v=WUI3PqSoM80)