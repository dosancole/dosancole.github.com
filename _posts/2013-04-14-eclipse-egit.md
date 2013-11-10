---
layout: post
title: "Eclipse EGit"
description: ""
category: 
published: false
tags: [Eclipse git]
---
{% include JB/setup %}

概要
--------------------------------------------------

EclipseのEgitによる、github pages の運用方法についてのメモです。


Eclispe EGit での環境構築 2013.3.31
--------------------------------------------------

1)EGIT Repoのビューを出して、clone(右上のアイコン)する。
　URIと dosancole/ktxxxxktxxxx
　まずはローカルにリポジトリを作成すると、Repoビューに出現する。

2)トップを右クリックでImport Projects。
　general projectで生成する。

3)同様に gh-pagesをInitial branchとたプロジェクトも引き出す。
　switch projectだとうまくいかないため。別扱いとする。
 .projectはコミットしなくて良いと考える。
 純粋な Eclipse のプロジェクトなら別だけど。

gh-pagesの運用開始 2013.3.31
--------------------------------------------------
・gh-pagesプロジェクトで、_layoutsフォルダを作成。
　作成済みのindex.htmlをdefault.htmlにして
　_layoutsにコピー。切り替える部分を {{ content }} にする。
　index.htmlを削除して、index.mdを作成。
・masterからコピーするwebやsampleなど用意できたら
　git branch で master が出てくることを確認。なければ
　git checkout master で一旦切り替え、git pull等で更新。
　git checkout gh-pagesで元に戻して
  git checkout master -- sample
  git checkout master -- web
  でコピー。
・上記の通り、masterプロジェクト側でpushしても、
　gh-pages側のプロジェクトは別のmasterを持っている状況なので、
　一旦pullする必要あるので注意。
・以下のシェルスクリプトを作成して、容易化
#!/bin/sh
git checkout master
git pull
git checkout gh-pages
git checkout master -- web
git checkout master -- sample

