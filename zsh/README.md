## 1. Install zsh
### For Debian
```sh
$ sudo apt-get install zsh`
```
### For MacOS
```sh
$ brew install zsh
```
## 2. Configure theme
```sh
$ cd ~
$ vim .zshrc
  ...
  ZSH_THEME="agnoster"
  ...
$ source .zshrc
```
## 3. Add plugins
### zsh-command-time
```sh
$ git clone https://github.com/popstas/zsh-command-time.git ~/.oh-my-zsh/custom/plugins/command-time
```
- `plugins=(command-time)`
### zsh-autosuggestions
```sh
$ git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```
- `plugins=(zsh-autosuggestions)`
### zsh-syntax-highlighting
```sh
$ git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```
- `plugins=(zsh-syntax-highlighting)`