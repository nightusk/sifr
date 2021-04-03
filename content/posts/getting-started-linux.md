---
title: "Linuxを始めましょう"
date: 2021-04-03T01:38:49+09:00
draft: false
---

# インストール

Windowsを削除してインストールしたり、Windowsとデュアルブートしたりもできますが、
失敗すると大切なデータが消えることがあります。  

ここでは最も簡単でリスクがないWSLを使用することにします。
下記ページの`Manual installation Steps`に従ってWSL2をインストールしてください。  
[https://docs.microsoft.com/en-us/windows/wsl/install-win10](https://docs.microsoft.com/en-us/windows/wsl/install-win10)
`distribution`は`Ubuntu 20.04`を選択します。  

インストールできたらWindowsスタートメニューから`Ubuntu 20.04 LTS`を起動してください。

# テキストエディタに慣れましょう

Linuxでよく使用するテキストエディタは特殊な操作をするものが多いです。
`Vim`, `Emacs`, `nano`などがよく使用されますが、ここではチュートリアルが充実している`Vim`を使用します。
下記コマンドを実行しチュートリアルに従ってください。
```bash
vimtutor
```
`command not found`と表示される場合は、コマンドを間違えているか、`Vim`がインストールされていないです。
たいていの場合`Vim`はインストールされていると思いますが、インストールするときは下記コマンドを実行してください。
```bash
sudo apt install vim
```
