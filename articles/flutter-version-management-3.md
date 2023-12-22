---
title: "FVM 3.0 使ってみた（Flutter Version Management）"
emoji: "🔄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [flutter, fvm]
published: true
publication_name: "altiveinc"
# cSpell:words fvmrc yourrank
---
:::message
FVM 3.0.0 はまだBeta版のPre-releaseです。
安定版が正式リリースされたら、改めてこの記事を更新する予定です🚀
:::

こんにちは、Flutterでのアプリ開発をメインとしている「[Altive株式会社](https://altive.co.jp)」の村松龍之介（[@riscait](https://x.com/riscait)）です！

2年前に「[FVMでFlutter SDKのバージョンをプロジェクト毎に管理する](https://zenn.dev/altiveinc/articles/flutter-version-management)」という記事を書きました。（もうそんな経ったの…⁉️）

それからずっとFVMを使っていますが、v3.0.0-beta (Pre-release)がリリースされているので使ってみました！

## FVMとは

ご存知の方も多いとは思いますが、FVMは、**Flutter SDKのバージョン管理ツール**です。

複数のFlutter SDKをインストールし、**プロジェクトごとにバージョンを容易に切り替えることができます。**

何らかの理由で最新ではないバージョンや、逆に安定版ではないBetaバージョンを使用したい場合にも便利です。

## FVM 3.0 で変わること

`fvm 3.0.0-beta.5 (Pre-release)` のリリースノートで確認でき、気になる項目をざっくりリストアップしました。

### 追加機能

- VSCodeとの統合が強化され、Flutter SDKバージョンを自動的に設定し、ターミナルパスを更新する機能が追加。
**これにより、flutterコマンドを直接使用できるようになります**
- Git Flutterリポジトリのミラーリングにより、新しいSDKバージョンのクローンが速くなります
- .gitignoreの自動追加: `.fvm` ディレクトリがチェックされて自動で追記されます
- Flutter SDKのキャッシュ検証: キャッシュされたFlutter SDKがアップグレードされたかを検証し、修正アクションのオプションを提供されます
- Flutter SDKの互換性チェック: 現在のプロジェクトとの互換性を確認する機能が追加

### 改善点

- エラーメッセージ、ログ、ヘルプ情報の改善
- fvm flutterコマンドの出力がカラー化
- fvm doctorコマンドの改善。
- fvm releasesおよびfvm list出力の改善
- fvm globalコマンドのエラーチェックの改善

### 変更点

- `fvm releases` コマンドはデフォルトで安定版のみ表示されるように。全てのリリースを見るには `--all` フラグを使用。
- `flavor` コマンドが削除され、 `fvm use {flavor}` に変更
- `destroy` コマンドが削除され、 `fvm remove --all` に変更
- **設定ファイルを `fvm_config.json` から `.fvmrc` に変更**
これにより、 `.fvm` を `.gitignore` に追加可能に。FVMが自動的に移行します。
- `fvm use {version} --env {flavor}` をflavorのエイリアスとして使用可能に
- Flutterリポジトリコミットハッシュを使用する際、ハッシュは10桁必要に。FVMが検証し、正しいハッシュが提供されます
- `fvm install` はデフォルトでセットアップを行わなくなりました。 `--setup` フラグでセットアップも同時に行われるようになります
`fvm use` はデフォルトでセットアップを行います。 `--skip-setup` フラグでスキップ可能
- 環境変数 `FVM_HOME` が `FVM_CACHE_PATH` に、 `FVM_GIT_CACHE` は `FVM_FLUTTER_URL` に変更されました

:::message
最新版、および全ての変更点はこちらのGitHub Repositoryで確認できます。
https://github.com/leoafarias/fvm/releases
:::

## FVM 3.0.0-beta のインストール

安定版がリリースされれば、Homebrew等でシンプルにインストールできるようになると思いますが、
現時点では、GitHub Repository Releasesからインストールしました。

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
`settings.json` からコメントと末尾カンマを取り除いて、JSONフォーマットに従えば問題ありませんでした。
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

## `fvm use` でFlutter SDKのバージョンを指定する

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

### `.gitignore` が自動で修正される

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

### `settings.json` の `dart.flutterSdkPath` が自動で修正される

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

:::message
v2では `.fvm/flutter_sdk` のように、具代的なバージョンは記述していなかったですが、
v3では、具体的なバージョンが記述されるようになりました。

`fvm use` コマンドで自動で書き換えてくれる＆ローカルパスではないので、引き続き `settings.json` をGit管理に入れてチームで共有していても問題はありません。
:::

ちなみに `.fvm` ディレクトリは下記のような構成になっていました👀

```
-.fvm
  - versions
    - 3.16.5
  - release
  - version
```

---

これで無事に Flutter SDKバージョンを指定することができました！
出力された通り、VS CodeのTerminalを再起動すると、 `flutter` コマンドで指定したSDKバージョンが使用できるようになります🙌

## `fvm install` でプロジェクトで指定されているFlutter SDKをインストールする

`fvm use` を実行していないチームメンバーや、新しく入ったプロジェクトでは、従来通り `fvm install` を実行して指定のSDKをインストールしましょう👌

```shell
fvm install --setup
```

`--setup` を付けると、Flutter SDKのビルドも行ってくれます。

## Melosとの相性は問題なさそう

弊社では、モノレポ管理ツールの [Melos](https://melosjs.github.io/melos/) を使用しています。

Melosへの影響を調べました。

FVM v2では、設定ファイル `melos.yaml` で、以下のようにFVMのFlutter SDKパスを指定していました。

```yaml
name: yourrank
repository: https://github.com/altive/yourrank
sdkPath: .fvm/flutter_sdk
```

ところが FVM v3では、SDKの配置場所が `.fvm/flutter_sdk` ではなくなったので、 `sdkPath` の指定を変更する必要がありますね。

試しに、 `sdkPath` を 削除（未指定）にして、 Melos経由でFlutter SDKのバージョンを確認してみました。

```shell
% melos exec -- "flutter --version"
$ melos exec
  └> flutter --version
     └> RUNNING (in 2 packages)

-----------------------------------------------------------------------------------------------------------------------------------------------
[yourrank_api_client]: Waiting for another flutter command to release the startup lock...

[yourrank]: Flutter 3.16.5 • channel stable • https://github.com/flutter/flutter.git
[yourrank]: Framework • revision 78666c8dc5 (2 days ago) • 2023-12-19 16:14:14 -0800
[yourrank]: Engine • revision 3f3e560236
[yourrank]: Tools • Dart 3.2.3 • DevTools 2.28.4
[yourrank_api_client]: Flutter 3.16.5 • channel stable • https://github.com/flutter/flutter.git
[yourrank_api_client]: Framework • revision 78666c8dc5 (2 days ago) • 2023-12-19 16:14:14 -0800
[yourrank_api_client]: Engine • revision 3f3e560236
[yourrank_api_client]: Tools • Dart 3.2.3 • DevTools 2.28.4
-----------------------------------------------------------------------------------------------------------------------------------------------
```

問題なく、 FVMで指定したFlutter SDKのバージョンが使用されています🙌

Melosとの併用も問題なさそうです👌

## v2とv3の共存

以下に注意することで、v2とv3を共存させることは一応可能だと思います。

- `.fvmrc` をコミットする
- `fvm_config.json` を削除しない
- `fvm_config.json` は `.gitignore` されないようにする
```
# FVM Version Cache
.fvm
!.fvm/fvm_config.json
```
- `settings.json` をGit管理にしない。または、 `dart.flutterSdkPath` だけはローカルの差分として保持する
- `melos.yaml` で `sdkPath: .fvm/flutter_sdk` を指定しない

最後の `settings.json` と `melos.yaml` がちょっと面倒ですね…🤔

## おわりに

FVM v3.0.0は、まだベータ版ですが、v2からの改善点が多く、使いやすくなっていると感じました。安定版のリリースが待ち遠しいですね！

Xでは、主にアプリ開発について呟いております。
フォローしていただければ嬉しいです☺️ → 村松龍之介（[@riscait](https://x.com/riscait)）

### 宣伝
Riverpod の実践入門本を公開中です👍
https://zenn.dev/riscait/books/flutter-riverpod-practical-introduction
