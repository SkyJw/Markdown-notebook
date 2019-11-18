# Manjaro-i3安装折腾笔记



​		在笔记本上的ubuntu上使用了i3wm后，根本停不下来，回家想在manjaro上装一个i3.发现manjaro-kde装i3,好像比较麻烦，而且manjaro社区版中有manjaro-i3版本，于是乎心痒难耐，忍不住又来折腾一番，把原来的manjaro-kde版本格式化了，又重新装了个manjaro-i3版本。正好把一直想写的安装笔记，好好整理一番。

#### Manjaro-i3安装

​	这个之前安装过很多次，比较熟悉。要说的就两点

- gpt双硬盘安装时，将manjaro的/boot/efi挂载点，保留安装到之前win10的efi分区中

- 从U盘启动时，driver一栏选择nofree，应该会安装非开源驱动，效果更好（其实不知道是否有效）

#### 更换国内源

​	终端执行如下命令：

​	`sudo pacman-mirrors -i -c China -m rank //更新镜像排名`

​	然后从弹出对话框中，选择第一个源。然后执行以下命令更新源

​	`sudo pacman -Syy`

​	`sudo pacman -S archlinux-keyring `

​	设置 archlinuxcn 源，在 /etc/pacman.conf末尾添加如下命令：

```
[archlinuxcn]
SigLevel = Optional TrustedOnly
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

 	执行以下命令进行系统更新：

​	` sudo pacman -Syyu `

#### 安装搜狗输入法

​	在终端中执行以下命令:

```
$ sudo pacman -S fcitx-sogoupinyin
$ sudo pacman -S fcitx-im     # 按回车,使用默认全部安装
$ sudo pacman -S fcitx-configtool     # 图形化配置工具
```

​	在`~/.xprofile`中(没有则手动创建)，添加如下命令：

```
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=”@im=fcitx”
```

​	最后还需要在` ～/.i3/config `文件中添加如下命令，使得fcitx可能开机启动：

` exec --no-startup-id fcitx `

#### 右上角系统信息中文日期乱码

​	在安装和安装好以后，右上角的日期 某月 星期几 都是框框乱码。解决办法如下：

1. 找到 `/usr/share/conky/conky_maia`
2. 使用 `vim` 命令 `：%s/Bitstream Vera/anti/` 统一修改把 `Bitstream Vera` 修改为 `anti`

然后就方块就变成中文了。

#### 安装chrome浏览器

​	在终端使用如下命令进行安装：

​	` sudo pacman -S google-chrome `

#### 使用SSR科学上网

​	将下载好的ssr文件拷贝到` /usr/local/bin `文件夹下，然后执行以下命令：

​	` ssr install `

​	该命令会自动从github下下载ssr相关文件，完成后使用以下命令进行配置：

​	` ssr config `

​	常用命令如下：

```
	ssr start

	ssr stop

	ssr restart
```

#### 配置chrome浏览器科学上网

​	ssr启动后，系统还不能直接访问外网，需要设置一下本地代理。在chrome中需要使用` SwitchOmega `这个插件来完成。

​	首先在github上搜索并下载该插件，然后打开chrome的扩展页面，打开开发者模式，将下载好的.crx格式的插件解压成文件夹，然后点击从解压后的文件夹安装即可完成安装。

​	完成后，进入插件配置界面，如下图所示进行配置：

![proxy配置](./image/1.png)

​	图上漏了一步，应该将代理协议设置为sock5.

​	这一步配置完成后，在左侧情景模式中，点击auto switch，并点击添加规则列表，在弹出的规则列表网址中输入如下地址：

​	` https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt `

​	并点击立即更新情景模式。

​	然后将切换规则按如下图所示进行配置：

![auto_switch](./image/2.png)

​	至此应该就能在chrome中成功实现科学上网。另若是想在终端中科学上网，另需使用proxychains工具，在此不进行赘述。

#### 画面撕裂问题

​	之前用kde的时候没有这个问题，用i3明显发现在chrome浏览器中滑动滚轮时有画面撕裂感。我在安装manjaro时，driver选的nofree，故直接安装的闭源驱动，所以以下设置是针对闭源显卡驱动。首先输入如下命令：

```
$sudo nvidia-xconfig
$sudo nvidia-settings #为了root的身份保存 Configuration File
```

​	第二条命令执行后，会进入显卡配置界面：

- 首先点击左侧的` X Server Display Configuration `条目
- 然后点击右下角的` Advance `选项
- 然后将` Force Full Composition Pipeline `和` Force Composition Pipeline `选项打上勾
- 点击` Apply `
- 点击` Save to Configuration File `

​	如上设置后，没有在出现画面撕裂现象，另附一个画面撕裂测试[视频](https://www.youtube.com/watch?v=gmHaa5pvpVc&t=0s&index=1&list=FLh5Vlje9XYc_5j18Wz7xlUQ)（需科学上网）

##### 更新

​	如上设置后，重启后仍然会出现画面撕裂。需要在` /etc/X11/xorg.conf.d/90-mhwd.conf `文件的Section "Screen"章节中加入如下一行：

​	` Option "metamodes" "nvidia-auto-select +0+0 {ForceCompositionPipeline=On, ForceFUllComp    ositionPipeline=On}" `

​	设置后重启，画面撕裂情况消失

#### 更换shell为zsh并使用oh-my-zsh

​	zsh为manjaro-i3自带软件，所以这里直接使用以下命令安装oh-my-zsh，它是一个较为流行的对zsh的配置脚本：

` sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" `

​	安装完成后，按y确认，使用zsh代替默认shell

​	zsh插件安装：

- autojump插件

  高效的联想跳转插件，根据访问的频率权重来实现预判跳转

  安装：` sudo pacman -S autojump `

  然后在` ～/.zshrc `的plugins=()中，将autojump加入其括号中，插件之间使用空格隔开

- zsh-syntax-highlighting插件

  shell语法指示插件，语法错误时会标红

  安装：` git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting `

  安装完成后，同样需要在` ～/.zshrc `中进行配置

- zsh-autosuggestions插件

  高效的shell命令补全插件，根据历史命令提供补全

  安装：` git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions `

  安装完成后在` ~/.zshrc `配置即可

- extract插件

  简化解压命令插件，通过` x ‘解压文件名称’ `命令进行解压

  zsh自带插件，只需在` ~/.zshrc `的plugins中加入该插件名字即可使用

#### neovim安装及配置

##### neovim安装

` sudo pacman -S neovim `

##### neovim添加python3支持

​	首先使用如下命令，在manjaro中安装neovim的python3支持：

​	` sudo pacman -S python-neovim `

​	然后在` ～/.config/nvim/init/vim `中添加如下语句：

​	` let g:python3_host_prog=/usr/bin/python3 `

##### 安装插件管理器vim-plug

​	使用如下命令进行安装：

	 curl -fLo ~/.config/nvim/autoload/plug.vim --create-dirs \
	https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim 
##### neovim插件安装

​	因为之前配过neovim有现成的init.vim，这里就直接把init.vim配置信息贴出来

```
call plug#begin('~/.local/share/nvim/plugged')                                                                                        
Plug 'scrooloose/nerdtree', { 'on':  'NERDTreeToggle' }
Plug 'vim-airline/vim-airline'                      
Plug 'vim-airline/vim-airline-themes'               
Plug 'mhinz/vim-startify'                           
Plug 'junegunn/vim-easy-align'                      
Plug 'majutsushi/tagbar'
Plug 'Yggdroot/LeaderF'
Plug 'jiangmiao/auto-pairs'
Plug 'Valloric/YouCompleteMe'
Plug 'morhetz/gruvbox'  
                        
" Initialize plugin system
call plug#end()   
 
"for neovim
let g:python3_host_prog = '/usr/bin/python3'
                           
"for theme
syntax enable 
set background=dark
colorscheme gruvbox      
 
"for airline
let g:airline_theme='wombat'
 
"for vim-easy-align
" Start interactive EasyAlign in visual mode (e.g. vipga)
xmap ga <Plug>(EasyAlign)
" Start interactive EasyAlign for a motion/text object (e.g. gaip)
nmap ga <Plug>(EasyAlign)
 
"for nerdtree
map <C-n> :NERDTreeToggle<CR>
autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTree") && b:NERDTree.isTabTree()) | q | end    if

"for other config
set cursorline
set hls
set incsearch
set number
```

值得一提的时YouCompleteMe这个插件现在安装难度感觉比之前要小了不少了，用vim-plug安装好后，直接进入插件目录使用` ./install.py `即可安装成功。



