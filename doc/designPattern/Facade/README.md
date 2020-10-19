# Facade パターン
**「シンプルな窓口を用意する」**

## タイムスケジュール
- 10:30 - 10:35 本日の進め方
- 10:35 - 10:45 解説(Facadeパターン)
- 10:45 - 10:55 課題
- 10:55 - 11:00 補足。質疑応答

## Facadeパターン
Facadeを簡単に一言でいうと、「窓口」です。  
（愚直に訳すと「建物の正面」らしいです。）

一つのことを実現するために様々な処理を決まった手順で呼び出す必要があったりする場合は、
Facadeパターンで窓口を用意してあげることで、使う側が多くのことを意識せずに実装できるようになります。

### 例題
独自のバージョン管理ツールAPIのFacadeを作って、`FileEditService` から利用します。  

API
- MyVersionFileUtils
- MyVersionCommand
- MyVersionBranchHelper

利用する側にMyVersionの branch -> checkout -> fix(修正 -> add -> commit -> push
を意識させずに使ってもらうFacadeを作ります。

### 課題
Gitの機能を使って、リモートリポジトリに変更のリクエストを送る機能を実装する。

使う側にGitの checkout -> fix(修正) -> add -> commit -> push  
を意識させずに使ってもらうFacadeを作ります。  

※回答はexerciseパッケージのgit以下にあります。  

### 参考
Facadeは多くは入れ子の形をしています。  
下位レベルのものをまとめて上位レベルに公開するためのFacadeを作り、そのFacadeのあつまりをより上位にシンプルに見せるためにFacadeを作る。  
アプリケーションはこのFacadeの入れ子によってより上位の概念を実現するように実装されているのです。

### 応用問題1
Gitを使ったファイル編集アプリ「GEdit」を保守していましたが、オリジナルバージョン管理ツールを使ってファイルを管理をしているシステム「SVNet」と統合するような要件が発生しました。  
ただし、このシステムのソースコードは失われ、JavaDocとjarのみが残されておりAPIはかなり複雑です。  

AdopterパターンとFacadeパターンを使って利用者側に大きな変更を加えることなく管理対象を増やしてください。  
今回は、疑似的に`designpattern.facade.exercise.FileEditService` の `upload`メソッドを用意しました。  
storageTypeによってバージョン管理システムを切り替えられるようにしてください。  
ただし、ファイルのアップロードだけでなく、uploadの前処理や後処理も行う必要があるため、今後のメンテナンス性を考慮して`FileEditService.upload`内ではif分岐をしないように実装してください。  

問題は `designpattern.facade.exercise` パッケージ以下にあります。  

#### 手順
何から手を付けてよいかわからない場合は、以下の手順を参考にしてみてください。
- Facadeを作る
  - `GitFacade` を参考に、`myversion.outerlib` 内のAPIをまとめてシンプルなアップロードメソッドを持つクラス `MyVersionFacade` を用意する
- Adopterを作る
  - GitFacadeからインターフェースを抽出して、`IRegistoryCommand` を作る
  - `IRegistoryCommand` を `MyVersionFacade` が実装するようにする(※FacadeがそのままAdopterになります)
- ロジックを切り替える
  - Facadeのインスタンスを `StorageType` によって切り替えるクラス/メソッドを作成
  - `FileEditService.upload`で作成したメソッドから`StorageType`に合致したFacadeを取得する

### 応用問題2
応用問題1で実装した構造は、とあるデザインパターンと同じ構造になっています。  
なんとうい名前でしょうか？

### (参考)イメージ
Google検索はまさにFacadeです。
検索というのは裏で非常に複雑な処理をしています。
- 事前準備
  - Webページを閲覧して回ってWebページリストを作っておく
  - 雑多なリストを意味のある順番に並べ替えておく
  - インデックスを貼る(例：辞書のように、「あ」→「あ」から始まる言葉にすぐアクセスできるようにする)
  - Webページリスト同士の関係を調べて繋がりをつくておく
  - etc...
- 検索
  - 検索文字を意味のある単位に分割する
  - 検索文字と関連する言葉を用意する
  - 文字列をもとにインデックスを探す
  - インデックスから該当ページを探し、一覧にする
  - 一覧を事前に決まったアルゴリズムで並べる

上記のような複雑な手順が必要な処理を、使う側は「検索ワードを入力する」だけで実現できます。  
これはGoogleがこの窓口ページから内部で何をやっているのかを意識させずに「検索する」という機能のみを公開しているためです。