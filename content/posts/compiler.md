---
title: "コンパイラ"
date: 2021-04-07T21:39:23+09:00
draft: false
---

[前の記事](../restart-c/)では、
```bash
make tmp
```
と実行し、
```bash
cc     tmp.c   -o tmp
```
と表示されて実行プログラムが作成されました。  
ここで、```make```はビルド自動化ツールで、```cc```がコンパイラです。  
ここではコンパイラが何をしているのか、何ができるのかを見ていきましょう。

```c
#define CONSTANT 10
#define EN 0
#define JA 1

// main関数
int main() {
#if LANGUAGE == EN
  printf("hello, %d\n", CONSTANT);
#elif LANGUAGE == JA
  printf("こんにちは, %d\n", CONSTANT);
#endif

  int i = 10;
}
```

# C言語のバージョン
C90: 1990年に策定されたC言語の仕様  
C99: 1999年に策定されたC言語の仕様  
C11: 2011年に策定されたC言語の仕様  

組み込み開発では未だにC90がよく使われます。
少しずつ文法に変更が加えられていますが、C99での変更を知っておくとよいでしょう。
代表的なものには  
- 行コメント構文(```//```)の追加  
- ブロック先頭以外でのローカル変数の宣言が可能となった

などがあります。  
コンパイル時に```-std=c90```のように指定することでバージョンを切り替えることができます。
例えば上のソースコードでは
```bash
cc tmp.c -o tmp -std=c90 -pedantic-error
```
でコンパイルすると下記のようにエラーになりますが、  
※このコンパイラではコンパイラ拡張によってC99の文法がサポートされているため、
コンパイラ拡張の使用をエラーとする```--pedantic-errors```を追加しています。
```
tmp.c:5:1: error: C++ style comments are not allowed in ISO C90
    5 | // main関数
      | ^
tmp.c:13:3: error: ISO C90 forbids mixed declarations and code [-Wdeclaration-after-statement]
   13 |   int i = 10;
      |   ^~~
```
```bash
cc tmp.c -o tmp -std=c99 -pedantic-error
```
でコンパイルすると、C90のときのエラーは出力されなくなります。

# プリプロセス
プリプロセッサディレクティブ(```#```)で始まる行を処理しソースコードを生成します。
またコメントを削除します。
```bash
cc tmp.c -E
```
でプリプロセス結果を表示することができ、上のソースコードのプリプロセス結果は下記のようになります。
```c
# 1 "tmp.c"
# 1 "<built-in>"
# 1 "<command-line>"
# 31 "<command-line>"
# 1 "/usr/include/stdc-predef.h" 1 3 4
# 32 "<command-line>" 2
# 1 "tmp.c"





int main() {

  printf("hello, %d\n", 10);




  int i = 10;
}
```
上の例では、```LANGUAGE```が未定義ですが、
条件付きコンパイル(```#if```)で使われる未定義のシンボルは```0```と同義になるため```printf("hello, %d\n", 10);```が生成されました。
条件付きコンパイルで使用するシンボルは必ず未定義でないことを確認しましょう。
```c
#ifndef LANGUAGE
# error LANGUAGE is not defined.
#endif
```
シンボルを定義するには、ソースコードに```#define LANGUAGE JA```を追加するか、
コンパイル時に```-DLANGUAGE=JA```を指定します。
```bash
cc tmp.c -E -DLANGUAGE=JA
```
```
# 1 "tmp.c"
# 1 "<built-in>"
# 1 "<command-line>"
# 1 "/usr/include/stdc-predef.h" 1 3 4
# 1 "<command-line>" 2
# 1 "tmp.c"





int main() {



  printf("こんにちは, %d\n", 10);


  int i = 10;
}
```
# コンパイル
```bash
cc tmp.c -S
```
C言語をアセンブリ言語に変換します。同じディレクトリに```tmp.s```が作成され、下記のような内容となっています。
```assembly
	.file	"tmp.c"
	.text
	.section	.rodata
.LC0:
	.string	"hello, %d\n"
	.text
	.globl	main
	.type	main, @function
main:
.LFB0:
	.cfi_startproc
	endbr64
	pushq	%rbp
	.cfi_def_cfa_offset 16
	.cfi_offset 6, -16
	movq	%rsp, %rbp
	.cfi_def_cfa_register 6
	subq	$16, %rsp
	movl	$10, %esi
	leaq	.LC0(%rip), %rdi
	movl	$0, %eax
	call	printf@PLT
```

# アセンブル
C言語、アセンブリ言語を機械語に変換します。同じディレクトリにtmp.oが作成され、下記のよ
うな内容となっています。
```
00000000: 7f45 4c46 0201 0100 0000 0000 0000 0000  .ELF............
00000010: 0100 3e00 0100 0000 0000 0000 0000 0000  ..>.............
00000020: 0000 0000 0000 0000 2803 0000 0000 0000  ........(.......
00000030: 0000 0000 4000 0000 0000 4000 0e00 0d00  ....@.....@.....
00000040: f30f 1efa 5548 89e5 4883 ec10 be0a 0000  ....UH..H.......
00000050: 0048 8d3d 0000 0000 b800 0000 00e8 00
```
コンピュータが直接理解できる形式になっていますが、まだ実行はできません。
# リンク
```bash
cc tmp.o -o tmp
```
単一または複数のCソース、アセンブル結果、コンパイル結果を結合し、ひとつの実行可能ファイルを作成します。
