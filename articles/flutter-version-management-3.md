---
title: "FVM 3.0 使ってみた（Flutter Version Management）"
emoji: "🔄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [flutter, fvm]
published: false
publication_name: "altiveinc"
# cSpell:words fvmrc yourrank
---
こんにちは、Flutterでのアプリ開発をメインとしている「[Altive株式会社](https://altive.co.jp)」の村松龍之介（[@riscait](https://x.com/riscait)）です！

2年前に「[FVMでFlutter SDKのバージョンをプロジェクト毎に管理する](https://zenn.dev/altiveinc/articles/flutter-version-management)」という記事を書きました。

それからずっとFVMを使っていますが、v3.0.0-betaがリリースされているので使ってみました！

## FVMとは

ご存知の方も多いとは思いますが、FVMは、**Flutter SDKのバージョン管理ツール**です。

複数のFlutter SDKをインストールし、**プロジェクトごとにバージョンを容易に切り替えることができます。**

何らかの理由で最新ではないバージョンや、逆に安定版ではないBetaバージョンを使用したい場合にも便利です。

## FVM 3.0 で変わること

最新版はこちらのGitHub Repositoryで確認できます。
https://github.com/leoafarias/fvm/releases

### 追加機能

- VSCodeとの統合が強化され、正しいFlutter SDKバージョンを自動的に設定し、ターミナルパスを更新する機能を追加。これにより、flutterコマンドを直接使用できるようになります
- Git Flutterリポジトリのミラーリングにより、新しいSDKバージョンのクローンが速くなります
- .gitignoreチェック: '.fvm'ディレクトリのチェックと必要に応じて自動追加される機能を追加。
- Flutter SDKのキャッシュ検証: キャッシュされたFlutter SDKがアップグレードされたかを検証し、修正アクションのオプションを提供。
- Flutter SDKの互換性チェック: 現在のプロジェクトとの互換性を確認する機能。
- FVM設定管理の改善: プロジェクトごとにFVM設定をオーバーライドする機能を含む。

### 改善点

- エラーメッセージ、ログ、ヘルプ情報の改善。
- fvm flutterコマンドのカラー出力。
fvm doctorコマンドの改善。
fvm releasesおよびfvm list出力の改善。
fvm globalコマンドのエラーチェックの改善。
FVMアップデートチェックは1日1回に制限。--update-checkフラグで無効化可能。

### 変更点

- fvm releasesコマンドはデフォルトで安定版に設定。全リリースを見るには--allフラグを使用。
- "flavor"コマンドを削除し、fvm use {flavor}に置き換え。
- "destroy"コマンドを削除し、fvm remove --allに置き換え。
- 設定ファイルは.fvmrcに変更。.fvmは.gitignoreに追加可能、FVMが自動的に移行。
- fvm use {version} --env {flavor}をflavorのエイリアスとして使用可能。
- Flutterリポジトリコミットハッシュを使用する際、ハッシュは10桁必要。FVMが検証し、正しいハッシュを提供。

### 破壊的変更

- デフォルトのFVM設定場所が.fvmrcに変更。自動的に移行されますが、.fvmは無視されるべきです。
- fvm installはデフォルトでセットアップしない。--setupフラグでセットアップ可能。
- fvm releasesはデフォルトで安定版を表示。
- fvm flavorを削除し、fvm use {flavor}に置き換え。
- fvm useはデフォルトでセットアップを行う。--skip-setupフラグでスキップ可能。
- 環境変数FVM_HOMEはFVM_CACHE_PATHに、FVM_GIT_CACHEはFVM_FLUTTER_URLに変更されました。

## 筆者環境

- 各アプリプロジェクトではFVMを使用してチームでFlutterバージョンを統一している
- OSS開発ではFVMは使用せず、Flutter公式サイトからダウンロードしたstable最新版を使用している
- FVMの `global` 設定は使用していない

## FVM 3.0 のインストール

### v2のアンインストール

まず、v3.0のインストールの前に、既存のFVM (v2)をアンインストールしました。

```shell
# Homebrewを使ってインストールしていた場合
brew uninstall fvm
```

### v3.0のダウンロード

[RepositoryのReleases](https://github.com/leoafarias/fvm/releases)から、執筆時点で最新の `fvm 3.0.0-beta.5` をダウンロードしました。

:::message
筆者は Apple Silicon搭載のMacを使用しているため、「fvm-3.0.0-beta.5-macos-arm64.tar.gz」をダウンロードしました。
環境により、適切なAssetをお選びください。
正式リリース後は、Homebrewでインストールできるようになると思います。
:::

---

ダウンロードが完了したら解凍し、任意のフォルダに配置します。

筆者はホームフォルダに `tools` というフォルダを作成し、その中に配置しました。

`~/tools/fvm` という感じです。

### PATHの設定
`.zshrc` に以下を追記し、パスを通しました。

```zsh
export PATH="$PATH:$HOME/tools/fvm"
```

パスが通り、 `fvm` コマンドが使用できるようになったことを確認します。

```shell
which fvm
/Users/riscait/tools/fvm/fvm

fvm --version
3.0.0-beta.5
```

### fvm doctor

`fvm doctor` コマンドを実行し、環境が正しく設定されているか確認します。

:::details fvm doctorの出力結果（長いので折りたたみ）
弊社の `yourrank` と言うアプリのプロジェクトで実行した結果です。

```shell
FVM Doctor:                                                                                                                                    
-----------------------------------------------------------------------------------------------------------------------------------------------
Project:
┌─────────────────────────────┬─────────────────────────────────────────────┐
│ Project                     │ yourrank                                    │
├─────────────────────────────┼─────────────────────────────────────────────┤
│ Directory                   │ /Users/riscait/Repositories/altive/yourrank │
├─────────────────────────────┼─────────────────────────────────────────────┤
│ Active Flavor               │ None                                        │
├─────────────────────────────┼─────────────────────────────────────────────┤
│ Is Flutter Project          │ No                                          │
├─────────────────────────────┼─────────────────────────────────────────────┤
│ Dart Tool Generator Version │ Not available                               │
├─────────────────────────────┼─────────────────────────────────────────────┤
│ Dart tool version           │ Not available                               │
├─────────────────────────────┼─────────────────────────────────────────────┤
│ .gitignore Present          │ Yes                                         │
├─────────────────────────────┼─────────────────────────────────────────────┤
│ Config Present              │ Yes                                         │
├─────────────────────────────┼─────────────────────────────────────────────┤
│ Pinned Version              │ 3.16.3                                      │
├─────────────────────────────┼─────────────────────────────────────────────┤
│ Config path                 │ .fvmrc                                      │
├─────────────────────────────┼─────────────────────────────────────────────┤
│ Local cache dir             │ .fvm/versions                               │
├─────────────────────────────┼─────────────────────────────────────────────┤
│ Version symlink             │ .fvm/versions/3.16.3                        │
└─────────────────────────────┴─────────────────────────────────────────────┘


IDEs:
┌─────────────────────────┬─────────────────────────────────────────────────────┐
│ IDEs                    │ Value                                               │
├─────────────────────────┼─────────────────────────────────────────────────────┤
│ VSCode                  │                                                     │
├─────────────────────────┼─────────────────────────────────────────────────────┤
│ dart.flutterSdkPath     │ .fvm/flutter_sdk                                    │
├─────────────────────────┼─────────────────────────────────────────────────────┤
│ Matches pinned version: │ false                                               │
├─────────────────────────┼─────────────────────────────────────────────────────┤
│ Android Studio          │                                                     │
├─────────────────────────┼─────────────────────────────────────────────────────┤
│ Android Studio          │ No local.properties file found in android directory │
└─────────────────────────┴─────────────────────────────────────────────────────┘

Environment:
┌───────────────────────┬────────────────────────────────────────────────┐
│ Environment Variables │ Value                                          │
├───────────────────────┼────────────────────────────────────────────────┤
│ Flutter PATH          │ /Users/riscait/fvm/versions/3.16.3/bin/flutter │
├───────────────────────┼────────────────────────────────────────────────┤
│ Dart PATH             │ /Users/riscait/fvm/versions/3.16.3/bin/dart    │
├───────────────────────┼────────────────────────────────────────────────┤
│ FVM_CACHE_PATH        │ N/A                                            │
├───────────────────────┼────────────────────────────────────────────────┤
│ FVM_GIT_CACHE         │ N/A                                            │
├───────────────────────┼────────────────────────────────────────────────┤
│ FVM_GIT_CACHE_PATH    │ N/A                                            │
├───────────────────────┼────────────────────────────────────────────────┤
│ FVM_FLUTTER_URL       │ N/A                                            │
├───────────────────────┼────────────────────────────────────────────────┤
│ Flutter PATH          │ /Users/riscait/fvm/versions/3.16.3/bin/flutter │
├───────────────────────┼────────────────────────────────────────────────┤
│ Dart PATH             │ /Users/riscait/fvm/versions/3.16.3/bin/dart    │
└───────────────────────┴────────────────────────────────────────────────┘
┌──────────────┬──────────────────────────────────────────────────────────────────┐
│ Platform     │ Value                                                            │
├──────────────┼──────────────────────────────────────────────────────────────────┤
│ OS           │ macos Version 14.1.2 (Build 23B92)                               │
├──────────────┼──────────────────────────────────────────────────────────────────┤
│ Dart Locale  │ en-JP                                                            │
├──────────────┼──────────────────────────────────────────────────────────────────┤
│ Dart runtime │ 3.1.3 (stable) (Tue Sep 26 14:25:13 2023 +0000) on "macos_arm64" │
└──────────────┴──────────────────────────────────────────────────────────────────┘
```
:::

:::message
最初、以下のエラーが発生し、途中までしか出力されませんでした。
> IDEs:
Error parsing Vscode settings.json on /Users/riscait/Repositories/altive/yourrank/.vscode/settings.json
Please use a tool like https://jsonformatter.curiousconcept.com to validate and fix it
✗ Could not get vscode settings, please check settings.json

これは、`.vscode/settings.json` にコメントと末尾カンマがあったためでした。
（厳密ですね👀）
コメントと末尾カンマを取り除いて、JSONフォーマットに従えば問題ありません。
:::

---

`fvm doctor` を実行すると、 `fvm_config.json` が削除され、 `.fvmrc` が自動作成されました。

```json
// ↓ 削除：fvm_config.json
{
  "flutterSdkVersion": "3.16.3",
  "flavors": {}
}

// ↓ 生成：.fvmrc
{
  "flutter": "3.16.3"
}
```

## fvm use でFlutter SDKのバージョンを指定する

```shell
fvm use 3.16.5
```

:::message
v2では、モノレポや、 `flutter create` 前など、実行したい場所がFlutterプロジェクトではない場合は、
--forceをつける必要がありましたが、v3ではつけなくても、プロンプトで尋ねてくれるようになりました。

```shell
This does not seem to be a Flutter project directory
✔ Would you like to continue?
```

`--force` は、引き続き使用できるので、プロンプトを省略したい場合は指定しましょう。
:::

### .gitignore

ここで、 `.gitignore` に `.fvm/` を追加するか尋ねられました。

```shell
You should add the fvm version directory ".fvm/" to .gitignore?
✔ Would you like to do that now? · yes                                                                                                         
✓ Added .fvm/ to .gitignore
```

```shell
# FVM Version Cache
.fvm
```

以前は `.fvm/flutter_sdk` を指定していたと思いますが、v3では `.fvm` でOKです。

### settings.jsonの `dart.flutterSdkPath` の自動書き換え

```shell
This does not seem to be a Flutter project directory
✔ Would you like to continue? · yes                                                                                                            
✗ Could not resolve dependencies. (0.7s)

Expected to find project root in current working directory.

The error could indicate incompatible dependencies to the SDK.
✔ Would you like to continue pinning this version anyway? · yes                                                                                
✓ Project now uses Flutter SDK : SDK Version : 3.16.5
┌────────────────────────────────────────────────────────────────────┐
│ ✓ Running on VsCode, please restart the terminal to apply changes. │
└────────────────────────────────────────────────────────────────────┘
You can then use "flutter" command within the VsCode terminal.
```

```json
  // Before
  "dart.flutterSdkPath": ".fvm/flutter_sdk"
  // After
  "dart.flutterSdkPath": ".fvm/versions/3.16.5"
```

下記のような構成になっていました。

```
-.fvm
  - versions
    - 3.16.5
  - release
  - version
```


これで無事に Flutter SDKバージョンを指定することができました！
出力された通り、VS CodeのTerminalを再起動すると、 `flutter` コマンドで指定したSDKバージョンが使用できるようになります🙌

## チームメンバーや新しく入ったプロジェクトでは `fvm install` を実行

`fvm use` を実行していないメンバーは、従来通り `fvm install` を実行して、指定のSDKをインストールしましょう。

```shell
fvm install --setup
```

`--setup` を付けると、Flutter SDKのビルドも行ってくれます。

## おわりに
