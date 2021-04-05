---
title: "C言語(再)入門"
date: 2021-04-05T22:25:26+09:00
draft: false
---

# インストール

C言語のコンパイルに必要なツールをインストールしましょう。  
```bash
sudo apt install build-essential
```

# 最初の(最小の)Cソース

作業用のディレクトリを作成し、Cソースファイルを作成しましょう。  
```bash
mkdir src
cd $_
vim tmp.c
```
```c
main(){}
```

それでは早速このCソースファイルをコンパイルしましょう。  
```bash
make tmp
```
```
cc     tmp.c   -o tmp
tmp.c:1:1: warning: return type defaults to ‘int’ [-Wimplicit-int]
    1 | main(){}
      | ^~~~
```
警告は出ていますが、コンパイルは成功したようです。実行してみましょう。
```bash
./tmp
```
何もしないプログラムなので、特に何も起きません。

# FizzBuzz

もう少し意味のあるプログラムを作成しましょう。  
下のプログラムは、3で割り切れる数のときには"fizz"、5で割り切れる数のときには"buzz"、
3でも5でも割り切れるときには"fizzbuzz"と表示し、そうでない数の場合はその数をそのまま表示します(が、そのとおりに動きません)。

```bash
vim fizzbuzz.c
```
```c
#include <stdio.h>

int main() {
  for (int i = 1; i <= 20; ++i)
    if (i % 3 == 0) puts("fizz");
    else if (i % 5 == 0) puts("buzz");
    else if (i % 3 == 0 && i % 5 == 0) puts("fizzbuzz");
    else printf("%d\n", i);
}
```
コンパイルして動かしてみましょう。
```bash
make fizzbuzz
```
```
cc     fizzbuzz.c   -o fizzbuzz
```
```bash
./fizzbuzz
```
```
1
2
fizz
4
buzz
fizz
7
8
fizz
buzz
11
fizz
13
14
fizz
16
17
fizz
19
20
```
15のときに"fizzbuzz"と表示されてほしいのですが、"fizz"と表示されています。  
プログラムを1行ずつ実行して、何が起きているのか確認しましょう。

# デバッガ入門
まずはデバッガ(gdb)をインストールしましょう。
```bash
sudo apt install gdb
```
gdbを使ってプログラムを起動してみましょう。
```bash
gdb fizzbuzz
```
```
GNU gdb (Ubuntu 9.2-0ubuntu1~20.04) 9.2
Copyright (C) 2020 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from fizzbuzz...
(No debugging symbols found in fizzbuzz)
(gdb) 
```
デバッグシンボルを含めずにコンパイルしたため```(No debugging symbols found in fizzbuzz)```と表示されています。
一度gdbを終了して、デバッグシンボルを含めてコンパイルし直しましょう。
```
(gdb) quit
```
```bash
CFLAGS=-g make fizzbuzz
```
```
cc -g    fizzbuzz.c   -o fizzbuzz
```
もう一度gdbを使ってプログラムを起動してみます。
```bash
gdb fizzbuzz 
```
```
GNU gdb (Ubuntu 9.2-0ubuntu1~20.04) 9.2
Copyright (C) 2020 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from fizzbuzz...
```
実行してみましょう。
```
(gdb) start
```
```
Temporary breakpoint 1 at 0x1169: file fizzbuzz.c, line 3.
Starting program: /home/user/src/fizzbuzz 

Temporary breakpoint 1, main () at fizzbuzz.c:3
3	int main() {
```
3行目で中断(break)しています。そのまま1行ずつ実行してみましょう。
```
(gdb) next
```
```
4	  for (int i = 1; i <= 20; ++i)
```
```
(gdb) next
```
```
5	    if (i % 3 == 0) puts("fizz");
```
```
(gdb) next
```
```
6	    else if (i % 5 == 0) puts("buzz");
```
```
(gdb) next
```
```
7	    else if (i % 3 == 0 && i % 5 == 0) puts("fizzbuzz");
```
```
(gdb) next
```
```
8	    else printf("%d\n", i);
```
```
(gdb) next
```
```
1
4	  for (int i = 1; i <= 20; ++i)
```
```
(gdb) next
```
```
5	    if (i % 3 == 0) puts("fizz");
```
```
(gdb) next
```
```
6	    else if (i % 5 == 0) puts("buzz");
```
```
(gdb) next
```
```
7	    else if (i % 3 == 0 && i % 5 == 0) puts("fizzbuzz");
```
```
(gdb) next
```
```
8	    else printf("%d\n", i);
```
```
(gdb) next
```
```
2
4	  for (int i = 1; i <= 20; ++i)
```
```
(gdb) next
```
```
5	    if (i % 3 == 0) puts("fizz");
```
```
(gdb) next
```
```
fizz
4	  for (int i = 1; i <= 20; ++i)
```
ソースコードを見ながら実行もできます。
```
(gdb) layout src
```
何回かnextしてiが15になるまで進めます。途中でiの値がわからなくなったら
```
(gdb) print i
```
で現在のiの値を確認できます。
```
(gdb) next
4	  for (int i = 1; i <= 20; ++i)
(gdb) next
5	    if (i % 3 == 0) puts("fizz");
(gdb) next
4	  for (int i = 1; i <= 20; ++i)

```
i == 15のとき、7行目の分岐```else if (i % 3 == 0 && i % 5 == 0) puts("fizzbuzz");```に入ることが期待結果ですが
5行目の分岐```if (i % 3 == 0) puts("fizz");```に入り、次のループを実行していることがわかります。
