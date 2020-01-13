# For Debian
## 1. Install zsh
- `$ sudo apt-get install zsh`
```sh
$ cd ~
$ vim .zshrc
  ...
  ZSH_THEME="agnoster"
  ...
$ source .zshrc
```
## 2. Add plugins
### zsh-command-time
- `$ git clone https://github.com/popstas/zsh-command-time.git ~/.oh-my-zsh/custom/plugins/command-time`
- `plugins=(command-time)`
### zsh-autosuggestions
- `$ git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions`
- `plugins=(zsh-autosuggestions)`
### zsh-syntax-highlighting
- `$ git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting`
- `plugins=(zsh-syntax-highlighting)`
