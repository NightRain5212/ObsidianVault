

## 效果图

![[archwsl1.png]]
![[archwsl2.png]]
![[archwsl3.png]]

## 安装WSL2

- 前往官方文档进行安装
	- https://learn.microsoft.com/zh-cn/windows/wsl/install
## 下载发行版

- 进入Release下载发行版到目标文件夹
	- https://github.com/yuk7/ArchWSL

## 下载Nerd Font字体

- 前往网址下载中意的字体，并给终端换上即可。
- https://www.nerdfonts.com/

## 安装arch

- 解压压缩包，双击打开Arch.exe。完成后退出再次打开即可。

## 配置用户

- 设置root用户密码
```bash
passwd 
```
- 添加用户组
```bash
echo "%wheel ALL=(ALL) ALL" > /etc/sudoers.d/wheel
```
- 添加用户
```bash
useradd -m -G wheel -s /bin/bash {UserName} #{UserName}替换为用户名
```
- 设置用户密码
```bash
passwd {UserName} #{UserName}替换为用户名
```
- 设置默认登录用户
	- 在目录位置打开终端（存放Arch的目录）
```powershell
.\Arch.exe config --default-user {UserName} #{UserName}替换为用户名
```

## 初始化密钥环

- 依次执行
```bash
sudo pacman-key --init
sudo pacman-key --populate
sudo pacman -Syy archlinux-keyring
```

## 卸载Arch

- 在目录中打开pwsh
```powershell
.\Arch.exe --clean
```
## 换源

- 编辑/etc/pacman.d/mirrorlist文件，在头部加入以下内容
- 可使用`sudo nano /etc/pacman.d/mirrorlist`编辑文件，Ctrl+S，Ctrl+X保存并退出。
```shell
# 华为镜像站
Server = https://repo.huaweicloud.com/archlinux/$repo/os/$arch
# 阿里镜像站
Server = https://mirrors.aliyun.com/archlinux/$repo/os/$arch
# 清华镜像站
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
```

- `sudo nano /etc/pacman.conf`添加archlinuxcn
```shell
[archlinuxcn]
# 阿里archlinuxcn源
Server = https://mirrors.aliyun.com/archlinuxcn/$arch
# 清华archlinuxcn源
# Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

- 更新
```bash
sudo pacman -Suyy
sudo pacman -S archlinuxcn-keyring
```

## 本地化

- 进入文件取消注释`en_US.UTF-8 UTF-8` 和 `zh_CN.UTF-8 UTF-8` 
```bash
sudo nano /etc/locale.gen
```
- 更新
```bash
locale-gen
```
- 设置时区
```bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

## 安装yay

```bash
sudo pacman -S yay
```

## 一些基本软件安装更新

```bash
sudo pacman -S git
sudo pacman -S neovim yay zsh fastfetch
sudo pacman -S lsd
```

```bash
sudo pacman -Syyu base base-devel git zip unzip net-tools tree python wget btop fastfetch --needed --noconfirm
```

- 如果后续安装软件包缺少依赖, 可以临时注释掉`/etc/pacman.conf`中所有后缀为`testing`或`staging`的软件源, 完成后使用`sudo pacman -Syyu`更新依赖即可.
## 安装 vs code

```bash
sudo pacman -S code
```

## git clone 加速

```bash
git config --global url."https://gitclone.com/".insteadOf https://
```
- 取消更改
```bash
git config --global --unset url.https://gitclone.com/.insteadOf
```
- 或者进入配置文件删除
```zsh
sudo nano .gitconfig
```

- gdb安装
```bash
sudo pacman -S gdb
```

- cmake安装
```bash
yay -S cmake
```

- 然后在vscode下下载对应的扩展即可
	- WSL，C/C++，Code Runner

## npm换国内源

- 华为云
```bash
npm config set registry https://mirrors.huaweicloud.com/repository/npm/
```

## 安装文件管理器YAZI

```bash
sudo pacman -S yazi ffmpegthumbnailer unarchiver jq poppler fd ripgrep fzf zoxide
```

### 更换yazi主题

- 进入官方页面选择主题，按文档安装即可
	- https://github.com/yazi-rs/flavors

## 终端美化oh-my-zsh

- 安装oh-my-zsh
```bash
git clone --depth=1 https://github.com/ohmyzsh/ohmyzsh.git ~/.oh-my-zsh
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```

- 设置主题（powerlevel10k）
- 安装主题
```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```
- 设置主题
```bash
sudo nano ~/.zshrc
# 找到并替换为下面的值
ZSH_THEME="powerlevel10k/powerlevel10k"
```
- 更改默认终端
```bash
sudo chsh -s /bin/zsh  #root用户终端
sudo chsh -s /bin/zsh {username}  #非roo用户
# {username} 替换为实际用户名
zsh # 切换终端
```
- 配置p10k
```bash
source ~/.zshrc # 启动配置
```
- 然后根据向导配置即可

## 配置nvim

- 安装nvim
```zsh
sudo pacman -S neovim
```
- 配置Astrovim
- 清理原有配置
```bash
mv ~/.local/share/nvim ~/.local/share/nvim.bak
mv ~/.local/state/nvim ~/.local/state/nvim.bak
mv ~/.cache/nvim ~/.cache/nvim.bak
```
- 克隆仓库，打开更新
```bash
git clone --depth 1 https://github.com/AstroNvim/template ~/.config/nvim
rm -rf ~/.config/nvim/.git
nvim
```
