





```
# 新しいディレクトリを作成します
$ mkdir sample 

# そのディレクトリに移動します
$ cd sample 

# Gitを初期化します。ここまでは前準備です
$ git init 

# ファイルの中身が「Hello, world!」というgreetingというファイルを作成します
$ echo 'Hello, world!' > greeting 

# ここでいったん作成したファイル構成を確認

# git hash-object コマンドでgreetingのハッシュIDを確認します
$ git hash-object greeting
af5626b4a114abcb82d63db7c8082c3c4756e51b
```



このように、ファイルには作成時点で、ハッシュIDがついています

このハッシュIDはヘッダー（ファイル内容の文字数など、ファイルのメタ情報）とファイル内容をハッシュ化したものになります



```
# ここで一度.gitフォルダをみてみましょう
# まだGitオブジェクトが作成されていないため、オブジェクトフォルダ配下にはなにもありません
$ tree .git

# では次に git add して圧縮ファイルを作成してみます
# git add することで圧縮ファイルを作成します
$ git add greeting 

# .git以下のファイル構造を表示します。以下は今回関係している部分だけを抜粋
$ tree .git
.git
├─ objects   
	├─ af      
		└─ 5626b4a114abcb82d63db7c8082c3c4756e51b
```



こんなふうに、ハッシュIDのうち、先頭2文字をディレクトリ名に、残り38文字をファイル名にして保存します。



```
# ここでもう一度git add すると何がおきるでしょうか？
$ git add greeting
```



なにも起きませんよね。

なぜ何も起きないのでしょうか

ハッシュIDというのは、ファイルの中身に対して一意になっています

中身が同じファイルであれば必ず同じハッシュIDになります

そのため、ファイルの中身が同じであれば git add しても同じハッシュIDがあると判定されて、追加で圧縮ファイルが作られることはありません



ちなみに、このファイルの中身そのものを圧縮したファイルのことを、「blob（ブロブ）オブジェクト」といいます。

blobというのは「カタマリ」という意味で、ファイルの中身を圧縮しただけのカタマリということになります



---



いまのところ

私がつけてあげたファイル名の情報「greeting」はどこにも見当たりません



そこで、ファイル名とファイルの中身の組み合わせ（ファイル構造）を保存するためにあるのがツリーファイルでした

次に、ツリーファイルを確認していきます

```
# コミットをするとツリーファイルが作成されます
$ git commit -m 'add greeting'
[master (root-commit) ae682f6] 
add greeting 1 file changed, 1 insertion(+) create mode 100644 greeting 

# master ブランチ上での最後のコミットが指しているツリーファイルの中身を表示します
$ git cat-file -p master^{tree}
100644 blob af5626b4a114abcb82d63db7c8082c3c4756e51b    greeting
```



ここで、ツリーファイルにはもともとのファイル名「greeting」と、ハッシュ値のファイル名が保存されていることがわかります

blobというのは、これがファイルの中身そのものを圧縮したファイル、「blob（ブロブ）オブジェクト」であることを表示しています



<u>ツリーファイルをみてみると、このように対応関係が保存されています</u>



---



次に、ディレクトリを追加してコミットした場合のツリーファイルをみてみます

```
$ mkdir subdir 

# subdir ディレクトリの下に goodmorning というファイルを作成します
$ echo 'Goodmorning!' > subdir/goodmorning 

# ここで作成したファイル構成を確認

$ git add subdir

$ git commit -m 'add subdir'
[master 75458c8] add subdir 
1 file changed, 1 insertion(+) 
create mode 100644 subdir/goodmorning 

# ツリーファイルのIDを取得するために、最後のコミットの中身を表示します
# これがコミットファイルの中身になっていて、いつ、だれが、なにを、何の目的のために変更したかわかります
$  git cat-file -p HEAD
tree acd75d1289b95787ecaab96c73fe1f3dbfa9cf67
parent ae682f61f39b5c364781cb179035ae534c56a326
author kiyodori <メールアドレス> 1493763216 +0900
committer kiyodori <メールアドレス> 1493763216 +0900 
add subdir 

# ツリーファイルの先頭の文字を指定して、ツリーファイルの中身を表示します
$ git cat-file -p acd75d
100644 blob af5626b4a114abcb82d63db7c8082c3c4756e51b    greeting
040000 tree 60ac1b2d01e7f0c21178dcc2e767fb9a24d97124    subdir
```



先程と同じようにblogオブジェクトがあり、そのほかにTREEファイルがあります

ツリーファイルの中身をみたらツリーファイルが入っていました

ツリーファイルはディレクトリに対応しているものです

さらにこのツリーファイルをのぞくと、subdirフォルダ配下に保存したgoodmorningファイルの対応関係が保存されています

```
# ツリーファイルの先頭の文字を指定して、ツリーファイルの中身を表示します
$ git cat-file -p 60ac1b
100644 blob fa476f276a6fa984a789416f63f925e999834081    goodmorning
```





https://qiita.com/shyamahira/items/22c4a2423c21c288cb8e

