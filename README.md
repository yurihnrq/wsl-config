# Configuração do Ubuntu no WSL <!-- omit in toc -->

Este repositório contém o passo a passo para as configurações que devo realizar para configurar um ambiente de desenvolvimento no WSL utilizando o Ubuntu como distribuição Linux.

## Sumário <!-- omit in toc -->

- [Instalação e configuração inicial](#instalação-e-configuração-inicial)
  - [Habilitar WSL no Windows](#habilitar-wsl-no-windows)
- [Configuração do Windows Terminal](#configuração-do-windows-terminal)
  - [Dracula Theme](#dracula-theme)
  - [Profile JSON](#profile-json)
- [Instalação do ZSH](#instalação-do-zsh)
  - [Spaceship Theme](#spaceship-theme)
    - [Habilitar tema](#habilitar-tema)
    - [Personalização do tema](#personalização-do-tema)
  - [YuriHnrq's Theme](#yurihnrqs-theme)
  - [Plugins](#plugins)
- [Instalação de pacotes](#instalação-de-pacotes)
  - [Git & GitHub](#git--github)
  - [Node.js](#nodejs)
  - [PostgreSQL](#postgresql)
  - [Haskell](#haskell)
  - [R](#r)
  - [C/C++](#cc)
  - [Java SDK](#java-sdk)
  - [Android SDK](#android-sdk)
  - [Pacotes de utilitários](#pacotes-de-utilitários)
- [Funções e aliases](#funções-e-aliases)
- [Acesso LAN](#acesso-lan)
- [SSH Agent](#ssh-agent)

## Instalação e configuração inicial

### Habilitar WSL no Windows

No Powershell do Windows, como administrador, rode o comando:

```powershell
wsl --install
```

Aguarde o processo e reinicie o computador. Após este processo, você terá o WSL na versão mais atual e com a distribuição Ubuntu instalada.

Para outras opções de distruibuição Linux, você pode verificar estes links:

1. [Usar Distro Customizada](https://docs.microsoft.com/pt-br/windows/wsl/use-custom-distro);
2. [Alterar distribuição Linux padrão](https://docs.microsoft.com/pt-br/windows/wsl/install#change-the-default-linux-distribution-installed).

## Configuração do Windows Terminal

### Dracula Theme

Primeiramente, instalo o tema Dracula no meu Windows Terminal.
O passo a passo para este processo pode ser encontrado no [site oficial]("https://draculatheme.com/windows-terminal").

### Profile JSON

A seguir segue as configurações que eu modifico no profile do Ubuntu no Windows Terminal. Você deve modificá-las conforme o gosto/necessidade:

```json
{
 // Outras configurações aqui em cima...
 "cursorShape": "underscore",
 // Em startingDirectory preencha com o diretório inicial que você deseja.
 "startingDirectory": "\\\\wsl.localhost\\Ubuntu\\home\\yurih",
 // Em tabColor eu utilizo a mesma cor do background do tema definido em colorScheme.
 "tabColor": "#282A36",
 "scrollbarState": "hidden",
 "colorScheme": "Dracula"
},
```

## Instalação do ZSH

Costumo utilizar o ZSH como shell padrão. Sendo assim, devemos configurá-lo também.
Para instalá-lo no Ubuntu, basta rodarmos os comandos:

```bash
# Você precisará do git, por isso temos o comando para sua instalação aqui.
# O pacote util-linux-user pode ser necessário para modificar o shell padrão do sistema.
sudo apt install -y git zsh;
# Baixa e executa o script para configuração do ZSH.
# Este script perguntará se você deseja definir o ZSH como shell padrão, responda que sim (Y/y).
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)";
```

A configuração também deve ser feita em outros usuários (como root), caso deseje que eles também utilizem o ZSH.

### Spaceship Theme

Este é um ótimo tema para utilizarmos no ZSH.
A instruções para sua instalação pode ser encontrada no [repositório oficial](https://github.com/spaceship-prompt/spaceship-prompt).
Caso não queira perder tempo entendendo como funciona o processo de instalação, apenas rode os comandos a seguir:

```bash
git clone https://github.com/spaceship-prompt/spaceship-prompt.git "$ZSH_CUSTOM/themes/spaceship-prompt" --depth=1;
ln -s "$ZSH_CUSTOM/themes/spaceship-prompt/spaceship.zsh-theme" "$ZSH_CUSTOM/themes/spaceship.zsh-theme";
# Os comandos devem ser rodados em todos os usuários que deseja que utilizem o tema.
```

#### Habilitar tema

Após realizar a instalação to tema, defina ZSH_THEME="spaceship" no arquivo .zshrc.
O arquivo fica localizado no diretório home do usuário e a configuração deve ser feita em todos os usuários em que você deseja que o tema seja aplicado.

Recomendo fazer um backup do arquivo .zshrc antes de começarmos a modificá-lo. Para isso execute os seguintes comandos nos usuários onde for modificá-lo:

```bash
cp ~/.zshrc ~/.zshrc.bak;
```

O seguinte erro pode ser apresentado quando utilizar o ZSH ao habiltiar o tema Spaceship:

```bash
(upower:3505): UPower-WARNING **: 12:46:17.648: Cannot connect to upowerd: Could not connect: No such file or directory
```

Este erro está relacionado a obtenção do nível de energia da máquina. Isto não é possível no WSL. Para desabilitar essa opção basta adicionar "export SPACESHIP_BATTERY_SHOW=false" no arquivo .zshrc, logo abaixo da definição do tema.

#### Personalização do tema

Você pode personalizar o Spaceship Theme inserindo configurações logo abaixo da definição do tema no arquivo .zshrc do usuário desejado. As opções disponíveis para customização estão listadas no [site oficial](https://spaceship-prompt.sh/options/).
A configuração que eu utilizo é a seguinte:

```bash
ZSH_THEME="spaceship"
# Coloquei ZSH_THEME aqui só para mostrar que as personalizações devem ficar abaixo de sua definição.
# SPACESHIP_BATTERY_SHOW configura exibição de status da bateria.
export SPACESHIP_BATTERY_SHOW=false
# SPACESHIP_USER_SHOW configura exibição do nome do usuário.
export SPACESHIP_USER_SHOW=always
# SPACESHIP_HOST_SHOW configura exibição do nome do host.
export SPACESHIP_HOST_SHOW=always
# SPACESHIP_PROMPT_ADD_NEWLINE configura adição de nova linha a cada comando do prompt.
export SPACESHIP_PROMPT_ADD_NEWLINE=false
# SPACESHIP_DIR_TRUNC configura nível de profundidade na exibição do pwd.
# 1 = somente cwd.
# 0 = todos
# Outros valores aumentam a profundidade a partir do cwd.
export SPACESHIP_DIR_TRUNC=1
# SPACESHIP_PACKAGE_SHOW configura a exibição da versão da aplicação lida em arquivos de configuração.
export SPACESHIP_PACKAGE_SHOW=false
export SPACESHIP_CHAR_SYMBOL="zsh ➜ "
```

### YuriHnrq's Theme

Este é um tema que eu mesmo criei. Na verdade, não passa de uma leve modificação sobre o [Robbyrussell Theme](https://github.com/ohmyzsh/ohmyzsh/blob/master/themes/robbyrussell.zsh-theme).

Para adicioná-lo, é necessário criar um arquivo chamado `yurihnrq.zsh-theme` no diretório `~/.oh-my-zsh/themes`. Neste arquivo, insira o seguinte conteúdo:

```bash
PROMPT='%{$fg[cyan]%}%c%{$reset_color%} $(git_prompt_info)'
PROMPT_ARROW=$'\n%(?:%{$fg_bold[green]%}➜ :%{$fg_bold[red]%}➜ )'
PROMPT+="$PROMPT_ARROW"

ZSH_THEME_GIT_PROMPT_PREFIX="%{$fg_bold[blue]%}git:(%{$fg[red]%}"
ZSH_THEME_GIT_PROMPT_SUFFIX="%{$reset_color%} "
ZSH_THEME_GIT_PROMPT_DIRTY="%{$fg[blue]%}) %{$fg[yellow]%}✗"
ZSH_THEME_GIT_PROMPT_CLEAN="%{$fg[blue]%})"
```

Agora basta editar o arquivo `~/.zshrc` e alterar a linha `ZSH_THEME="robbyrussell"` para `ZSH_THEME="yurihnrq"`. Seu prompt ficará com a seguinte aparência:

![YuriHnrq's Theme](images/yurihnrq-theme.png)  

### Plugins

Eu utilizo dois plugins: [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions) que nos fornece um autocomplete baseado no histórico de comandos do shell e [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting) que adiciona syntax highlighting ao ZSH.

Para instalar os plugins, devemos rodas os seguintes comandos (no usuário que desejamos utilizar os plugins):

```bash
# Clona repositório do zsh-autosuggestions para o diretório de plugins do ZSH.
git clone https://github.com/zsh-users/zsh-autosuggestions "${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions";
# Clona repositório do zsh-syntax-highlighting para o diretório de plugins do ZSH.
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git "${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting";

```

Depois, basta editar o arquivo .zshrc e adicionar os plugins:

```bash
plugins=( [plugins...] zsh-syntax-highlighting zsh-autosuggestions)
```

## Instalação de pacotes

Nesta seção disponilizo os scripts/comandos que utilizo para instalação e configuração de pacotes que utilizo ou posso vir a utilizar no meu sistema.

Nos scripts a seguir, as configurações que exigem nome de usuário, email e demais informacões pessoais devem ser preenchidas. Se for utilizar os scripts a seguir, modifique-os para seu próprio uso.

### Git & GitHub

```bash
cd;
sudo apt install -y git;
mkdir ~/.ssh
echo -ne 'id_github\n\n\n' | ssh-keygen -t ed25519 -C "email here";
mv id_github id_github.pub ~/.ssh;
eval "$(ssh-agent -s)";
ssh-add ~/.ssh/id_github;
less ~/.ssh/id_github.pub | clip.exe;
echo "Go to GitHub SSH config page. The key is in the clipboard.";
git config --global user.name "your name here";
git config --global user.email "email here";
git config --global init.defaultBranch main;
```

### Node.js

Utilizo o [nvm](https://github.com/nvm-sh/nvm) para gerenciar as versões do node e npm instaladas na minha máquina. Sendo assim, rodo o script de instalação disponibilizado no repositõrio oficial:

```bash
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash;
# Comando source para que o nvm passe a ser reconhecido após a configuração feita pelo script.
source ~/.zshrc;
# O comando a seguir instala a versão LTS do node.
nvm install --lts;
```

Feito isso, adiciono o código a seguir no arquivo ~/.zshrc para que o nvm reconheça arquivos .nvmrc automaticamente:

```bash
# place this after nvm initialization!
# zsh only!
autoload -U add-zsh-hook
load-nvmrc() {
  local node_version="$(nvm version)"
  local nvmrc_path="$(nvm_find_nvmrc)"

  if [ -n "$nvmrc_path" ]; then
    local nvmrc_node_version=$(nvm version "$(cat "${nvmrc_path}")")

    if [ "$nvmrc_node_version" = "N/A" ]; then
      nvm install
    elif [ "$nvmrc_node_version" != "$node_version" ]; then
      nvm use
    fi
  elif [ "$node_version" != "$(nvm version default)" ]; then
    echo "Reverting to nvm default version"
    nvm use default
  fi
}
add-zsh-hook chpwd load-nvmrc
load-nvmrc
```

### PostgreSQL

```bash
sudo apt install -y postgresql postgresql-contrib;
```

Para obter o local do arquivo de configuração de autenticação, utilize:

```bash
sudo -u postgres psql -c "SHOW hba_file;";
```

`pg_hba.conf` é onde se configura quais hosts podem realizar conexões com banco. `trust` permite conexão, `md5` irá exigir uma senha e `peer` utiliza o mesmo usuário do sistema operacional para autenticar (conexão local).

Para obter o local do arquivo de configuração do servidor, utilize:

```bash
sudo -u postgres psql -c "SHOW config_file;";
```

Para conctar-se ao banco, utilize:

```bash
# Este método irá exigir a senha do usuário postgres.
psql -U postgres -h 127.0.0.1;

# Este método irá exigir a senha do usuário do sistema.
sudo -u postgres psql;
```

Para trocar a senha do usuário postgres, conecte-se ao banco e utilize:

```bash
ALTER USER user_name WITH PASSWORD '<new-password>';
```

### Haskell

```bash
sudo apt install -y ghc cabal-install;
```

### R

Primeiro devemos seguir os passos descritos no [site oficial do R](https://cran.r-project.org/):

```bash
# Atualiza lista de pacotes.
sudo apt update -qq
# Instala pacotes necessários para instalação do R
sudo apt install --no-install-recommends software-properties-common dirmngr
# Adiciona chave necessária para repositorio do R
wget -qO- https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc | sudo tee -a /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc
# Adiciona reposiório com versões mais recentes do R
sudo add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu $(lsb_release -cs)-cran40/"
# Instala o R
sudo apt install --no-install-recommends r-base;
```

Agora instalamos alguns pacotes necessários para o bom funcionamento do R:

```bash
sudo apt install build-essential -y;

sudo apt install libxml2-dev -y;
```

Feito isso, dentro do R, instalamos o language server, para utilizarmos o R no VSCode:

```R
install.packages("languageserver");
```

Com isso, basta instalar a extensão [R para VSCode](https://marketplace.visualstudio.com/items?itemName=REditorSupport.r).

### C/C++

```bash
sudo apt install -y gcc g++ gdb;
```

### Java SDK

```bash
# Primeiro listamos as versões do OpenJDK
sudo apt list | grep "openjdk";
# Depois instalamos a versão desejada. Ex:
sudo apt install openjdk-13-jdk;
```

Agora temos que definir a variável de ambiente JAVA_HOME. Para isso podemos adicionar

```bash
export JAVA_HOME="/usr/lib/jvm/java-13-openjdk-amd64"
```

no arquivo ~/.zshrc. Depois, salvamos e rodamos:

```bash
source ~/.zshrc
```

### Android SDK

Primero devemos baixas o commandline-tools no site oficial do [Android Studio](https://developer.android.com/studio#command-tools).
Com uma instância do ZSH aberta na pasta do onde baixamos o arquivo de nome semelhante a commandlinetools-linux-8092744_latest.zip, realizamos:

```bash
# Precisaremos do unzip para descompactar os arquivos.
sudo apt install -y unzip;
mkdir ~/.android;
mkdir ~/.android/android_sdk;
# Corrija o nome do arquivo zip para o que você fez download.
unzip commandlinetools-linux-8092744_latest.zip -d ~/.android/android_sdk
```

Agora teremos o conteúdo do arquivo .zip no diretório ~/.android/cmdline-tools. Dentro deste diretório, crie uma pasta com a versão do cmdline-tools que você baixou como nome e copie tudo no diretório para dentro dela.

Feito isso, teremos algo como:
~/.android/andoird_sdk/cmdline-tools/\<versão\>/\<conteúdo do zip aqui\>.

Agora entramos no arquivo ~/.zshrc e definimos a váriavel ANDROID_HOME, além de adicionar os executáveis do SDK ao PATH:

```bash
export ANDROID_HOME = ~/.android/andoird_sdk
# Substitua <versao> pelo nome que você deu a pasta com o conteúdo do arquivo .zip.
export PATH= $PATH:"~/.android/andoird_sdk/cmdline-tools/<versao>/bin/":"~/.android/andoird_sdk/platform-tools"
```

Agora rodamos o comando a seguir no terminal, para baixar ferramentas da plataforma Android:

```bash
sdkmanager "platform-tools";
```

### Pacotes de utilitários

```bash
sudo apt install -y net-tools nano htop;
```

## Funções e aliases

A seguir as funções e aliases que utilizo no meu shell.
Devem ser adicionadas no final do arquivo .bashrc (caso utilize o BASH) ou .zshrc (caso utilize o ZSH).

```bash
alias wtsp="wt.exe -w 0 sp -d ."
alias wtnt="wt.exe -w 0 nt -d ."
alias exp="explorer.exe ."
alias wps="pwsh.exe -nologo"
# Usar wps="powershell.exe -nologo" se o PS 7 não estiver instalado.
alias wslip="ifconfig eth0 | grep 'inet '"
alias winip="ipconfig.exe | grep -m 1 'IPv4 Address'"

code-at() {
  if [ $# -ge 1 ]
  then
    if [ -d "$@" ]
    then
      cd $@ && code .;
      return;
    else
      echo "code-at: $@ is not a directory.";
      return;
    fi
  else
    code .;
    return;
  fi
}
```

## Acesso LAN

O WSL vem por padrão com uma interface de rede NAT. Isso significa que ele pode acessar recursos exteros, mas tem seu IP mascarado pelo host. Desse modo, computadores na LAN não conseguem se comunicar com o WSL.

A solução para este problema foi disponibilizada por um usuário [nesta issue](https://github.com/microsoft/WSL/issues/4150#issuecomment-504209723) no GitHub.
Para criar uma solução de fácil acesso, sigo os seguintes passos:

1) Configuro e salvo o script em um diretório do Windows. A versão do script que utilizo eleva o processo do Powershell para administrador quando necessário:

    ```bash
    If (-NOT ([Security.Principal.WindowsPrincipal][Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole] "Administrator")) {   
      $arguments = "& '" + $myinvocation.mycommand.definition + "'"
      Start-Process powershell -Verb runAs -ArgumentList $arguments
      Break
    }

    $remoteport = bash.exe -c "ifconfig eth0 | grep 'inet '"
    $found = $remoteport -match '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}';

    if ($found) {
      $remoteport = $matches[0];
    }
    else {
      Write-Output "IP address could not be found";
      exit;
    }

    #Here you define which ports you want to be forwarded.
    #3000 && 3001: React.js and Node.js; 8081: Metro Bundler
    $ports=@(3000, 3001, 8081);

    $ports_a = $ports -join ",";

    #Remove Firewall Exception Rules
    Invoke-Expression "Remove-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' ";

    #adding Exception Rules for inbound and outbound Rules
    Invoke-Expression "New-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' -Direction Outbound -LocalPort $ports_a -Action Allow -Protocol TCP";
    Invoke-Expression "New-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' -Direction Inbound -LocalPort $ports_a -Action Allow -Protocol TCP";

    Invoke-Expression "netsh interface portproxy reset";

    for ($i = 0; $i -lt $ports.length; $i++) {
      $port = $ports[$i];
      Invoke-Expression "netsh interface portproxy add v4tov4 listenport=$port connectport=$port connectaddress=$remoteport";
      Invoke-Expression "netsh advfirewall firewall add rule name=$port dir=in action=allow protocol=TCP localport=$port";
    }

    Invoke-Expression "netsh interface portproxy show v4tov4"
    ```

2) Permito que o Powershell execute scripts assinados rodando o seguinte comando como administrador:

    ```powershell
    # Este comando é válido somente no Powershell 5.1.
    set-executionpolicy remotesigned;
    ```

3) Crio um alias no WSL que execute o script que realizar o port forwarding:

    ```bash
    # Substitua <script-start> pelo path do script que realizar a configuração no Windows.
    alias pfstart="powershell.exe \"& '<script-start>'\" > ~/.netpf.log"
    ```

    Agora sempre que executar netpf no shell do WSL, o script será executado e irá fazer com que computadores LAN acessem o WSL utilizando o IP do Windows e as portas especificadas no script.

4) Crio um arquivo de script que reseta as configurações:

    ```powershell
    If (-NOT ([Security.Principal.WindowsPrincipal][Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole] "Administrator")) {   
      $arguments = "& '" + $myinvocation.mycommand.definition + "'"
      Start-Process powershell -Verb runAs -ArgumentList $arguments
      Break
    }

    #Remove Firewall Exception Rules
    Invoke-Expression "Remove-NetFireWallRule -DisplayName 'WSL 2 Firewall Unlock' ";

    Invoke-Expression "netsh interface portproxy reset";

    Invoke-Expression "netsh interface portproxy show v4tov4";
    ```

5) Crio um alias no WSL que execute o script que deleta as configuracões de port forwarding:

    ```bash
    # Substitua <script-start> pelo path do script que 'reseta' a configuração no Windows.
    alias pfreset="powershell.exe \"& '<script-reset>'\" > ~/.netpf.log"
    ```

Você deve ter o pacote net-tools instalado na sua distribuição Linux no WSL para que o script funcione. Para instalar o pacote utilize:

```bash
sudo apt install -y net-tools;
```

## SSH Agent

Para que o agente SSH inicie a carregue automaticamente suas chaves, adicione o seguinte no arquivo .zshrc:

```bash
if [ -z "$SSH_AUTH_SOCK" ] ; then
  eval `ssh-agent -s` > /dev/null 2>&1
  # Aqui você adiciona cada uma das chaves que deseja carregar no agente.
  ssh-add ~/.ssh/id_github > /dev/null 2>&1
fi
```
