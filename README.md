Cygwin_config
=============

参考：

[RubyGems + Cygwin: POSIX path not found by ruby.exe](http://stackoverflow.com/questions/3831131/rubygems-cygwin-posix-path-not-found-by-ruby-exe)  
[Cygwinを導入してみた](http://nukino.github.io/blog/2012/02/02/Cygwin/)

#Cygwinをセットアップするときに個人的に入れといた方がいいパッケージ
Vimとssh（GitHub等）使うなら
- Editers/vim 
- Net/openssh         

後述
- Web/wget            
  - Devel/git-svn
  - Base/gawk 
  - Utils/bzip2 
  - Utils/tar 

wgetを入れて、apt-cygでパッケージ管理をするととても楽  
参考:[Cygwinにapt-cygを導入する ](http://kowaimononantenai.blogspot.jp/2013/12/cygwinapt-cyg.html)

```
$ git clone git@github.com:kou1okada/apt-cyg.git
$ cd apt-cyg
$ chmod +x apt-cyg
```

###↑で通らない場合
参考:[CygWinのapt-cygが上手くいかない場合](http://hujo.hateblo.jp/entry/2013/11/10/213119)

```
$ wget https://raw.github.com/kou1okada/apt-cyg/master/apt-cyg
$ chmod +x apt-cyg
$ cp apt-cyg /bin/
$ apt-cyg --use-setuprc update
```

ほかにもあったけど忘れた  
(zshというのが補完機能的な意味で強力らしいが今回はスルー)

#言語設定(日本語化する)
参考:[Cygwin/日本語化 ](http://www.bugbearr.jp/?Cygwin/%E6%97%A5%E6%9C%AC%E8%AA%9E%E5%8C%96)

基本はutf-8を使う。  
postgresql使うときはsjisで表示しないと文字化けするので以下のutf8をsjisに変更する。
(postgresql側をutf8に変更してもいい)

- タイトルバーのCygwinアイコンをクリック→options→Text
  - Locale : ja_JP
  - Character set : UTF-8 

- /etc/profile
```
# 日本時間の時差
export TZ=JST-9
# 言語の指定
export LANG=ja_JP.utf8
# 出力文字セットをUTF-8とする。(libiconv)
export OUTPUT_CHARSET=utf8
# 日本語ファイル名表示
alias ls='ls --show-control-chars'
```

- .inputrc
```
set kanji-code utf8
set convert-meta off
set meta-flag on
set output-meta on 
```

#.bashrc
書き換えたら`source ~/.bashrc`実行かCygwinを再起動

```
#Cygwinでは先にgemファイルの指定が必要(なかったら直打ち)
alias gem='gem.bat'
alias rake='rake.bat'
alias erb='erb.bat'
alias irb='irb.bat'
alias rdoc='rdoc.bat'
alias ri='ri.bat'
alias rspec='rspec.bat'
alias bundle='bundle.bat'

#↑を自動化したやつ
if [[ -n "$(which ruby 2>/dev/null)" ]]; then
  RUBY_BIN=$(cygpath -u $(ruby -e 'puts RbConfig::CONFIG["bindir"]') | tr '\    r' ' ')
  for f in $(find ${RUBY_BIN} -regex ".*bat$"| xargs -n1 basename); do
    alias ${f%.bat}=${f}
  done
fi

#lsコマンドするときにアイテムの種類によって色分け
if [ -x /usr/bin/dircolors ]; then
  test -r ~/.dircolors && eval "$(dircolors -b ~/.dircolors)" || eval "$(dircolors -b)"
  #alias dir='dir --color=auto'
  #alias vdir='vdir --color=auto'
  alias ls='ls -hF --color=auto' # classify files in colour
  alias dir='ls --color=auto --format=vertical'
  alias vdir='ls --color=auto --format=long'
fi

```
