> **__Warning:__** DO NOT ADD PRIVATE CREDENTIALS OR DETAILS IN THIS REPO.

---

# How I like my mac?
I like my mac stock and low maintenance - don't get in my way but also allow me ample flexibility to experiment. I also like my software up-to-date.
Stuff that I like to use:
1. **Nix** - has a large collection of mostly up-to-date software
2. **Home Manager with flakes** - declarative way to define software packages to install and link my dotfiles
3. **Brew** - manager of choice for installing casks, UI software

## Why don't I use nix darwin?

I tried it and only interacted it once while setting up my mac some 3 years ago. It doesn't get in my way (lovely!). But, I realised that when it will break, it will be too much work for me. The complexity works for now but it is complexity nonetheless. And I was using it simply to set a few flags for my keyboard and touchpad. Nix-darwin is too overpowered for my use case.

# Setup a new mac
Steps to make a mac behave the way I like

## 1. Mac command line tools
[Apple Docs](https://developer.apple.com/documentation/xcode/installing-the-command-line-tools/)
   ```bash
   xcode-select --install
   ``` 

## 2. Nix
Install nix in "multi user mode". Get the command from [Nix Downloads](https://nixos.org/download/#nix-install-macos)

> **_NOTE:_**  Reopen your terminal

## 3. Brew
Get the command from [Brew](https://brew.sh/)

> **_NOTE:_**  After the brew installer completes, it will give you a few commands that you need to run. Run them.

> **_NOTE:_**  Reopen your terminal

## 4. Install software from brew
```bash
brew install --cask brave-browser ghostty visual-studio-code libreoffice
```

## 5. Setup git and gihub
1. Follow this doc to setup ssh keys: [Github - Setup SSH Keys](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
2. Setup name and email in git config
  ```bash
  cat << EOF > ~/.gitconfig
  [user]
    name = <Bruce Wayne - Change me!>
    email = <bat@the.man - Change me!>
  
  EOF
  ```

## 6. Clone home-manager configs
```bash
git clone git@github.com:singaaka/mac.git ~/.config/home-manager/
```

## 7. Activate home-manager
```bash
nix --extra-experimental-features "nix-command flakes" run home-manager/master -- init --switch
```
[Home-Manager Docs](https://nix-community.github.io/home-manager/index.xhtml#sec-flakes-standalone)

> **_NOTE:_** `--extra-experimental-features "nix-command flakes"` is only needed the first time. After home-manager is activated for the first time, it will write `~/.config/nix/nix.conf` to auto configure the experimental features. Next runs of nix command can then omit this extra flag.

## 8. Sudo using touch id
```bash
sudo bash -c 'echo "auth    sufficient    pam_tid.so" >> /etc/pam.d/sudo_local'
```

## 9. Mac keyboard and touchpad config
```bash
defaults write -g ApplePressAndHoldEnabled -bool false
defaults write NSGlobalDomain KeyRepeat -int 2
defaults write NSGlobalDomain InitialKeyRepeat -int 15
defaults write NSGlobalDomain com.apple.mouse.tapBehavior -int 1
defaults write com.apple.menuextra.clock Show24Hour 1
```

## 10. Setup lazyvim
Follow: [](https://www.lazyvim.org/installation)

## 11. "Make ZSH Great Again!"
1. [Install oh-my-zsh](https://ohmyz.sh/#install)
2. Reopen terminal
3. [Install recommended fonts](https://github.com/romkatv/powerlevel10k?tab=readme-ov-file#meslo-nerd-font-patched-for-powerlevel10k)
4. [Install powerlevel10k](https://github.com/romkatv/powerlevel10k?tab=readme-ov-file#oh-my-zsh)
5. [Install zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions/blob/master/INSTALL.md#oh-my-zsh)
6. [Install zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md#oh-my-zsh)
7. Add plugins
```bash
sed -i '' 's/plugins=(git)/plugins=(git docker zsh-syntax-highlighting zsh-autosuggestions)/' ~/.zshrc
```
8. Add fix for slow paste
```bash
bash -c "cat << EOF >> ~/.zshrc
### Start: Fix slowness of pastes with zsh-syntax-highlighting.zsh
pasteinit() {
  OLD_SELF_INSERT=${${(s.:.)widgets[self-insert]}[2,3]}
  zle -N self-insert url-quote-magic # I wonder if you'd need `.url-quote-magic`?
}

pastefinish() {
  zle -N self-insert $OLD_SELF_INSERT
}
zstyle :bracketed-paste-magic paste-init pasteinit
zstyle :bracketed-paste-magic paste-finish pastefinish
### End: Fix slowness of pastes with zsh-syntax-highlighting.zsh

EOF"
```

## 12. Aliases for easy software management
```bash
cat << EOF >> ~/.zshrc
# Nix and brew related aliases
alias pkgs="nvim ~/.config/home-manager/home.nix"
alias update="nix flake update --flake ~/.config/home-manager"
alias switch="home-manager switch"
alias brew-upgrade="brew upgrade --cask --greedy"
alias upgrade="update && switch"

EOF
```

---

Viola! All done. Now enjoy the sweet sweet setup!