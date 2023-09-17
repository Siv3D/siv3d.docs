# Siv3D へのコントリビューションガイド

Siv3D へのコミットを検討いただきありがとうございます。  
プロジェクトを改善する取り組みと貢献に感謝いたします。  
このガイドでは、コントリビューションをできるだけスムーズに行う方法について説明します。

## ブランチ戦略
2 つのメインブランチがあります。

- [`v6_develop` :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D/tree/v6_develop){:target="_blank"} 開発用
- [`main` :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D){:target="_blank"} 安定版

適切なブランチからフォークし、そこで変更を行ってください。  
指示がない限り、プルリクエストは `v6_develop` ブランチに送信してください。

## プルリクエストの作成
1. GitHub アカウントにリポジトリをフォークします。
2. フォークしたリポジトリをローカルマシンにクローンします。
3. 機能追加や修正のために新しいブランチを作成します。
4. [コーディングスタイル](coding-style.md){:target="_blank"}に従って変更を行います。
5. [コミットガイドライン](#コミットガイドライン)に従ってコミットします。
6. 変更をフォークにプッシュします。
7. 変更内容を説明するコメントを書いてプルリクエストを作成します。

## コミットガイドライン
- 1 つのプルリクエストに複数のコミットがあっても構いません。
- コミットメッセージは明確で簡潔にしてください。
- 可能であれば、関連する Issue 番号をコミットメッセージで参照してください。

## 議論とコミュニケーション
- 大きな変更や新機能については、作業を開始する前に [Siv3D Discord サーバー](../community/community.md){:target="_blank"}や [GitHub Issues :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D/issues){:target="_blank"} を通じてメンテナーと相談してください。
- コントリビューションプロセスのどの段階でも、質問や説明を求めることができます。

## ライブラリのビルド
- Siv3D ライブラリを自分でビルドする方法の詳細については、[ビルド手順](build.md){:target="_blank"}を参照してください。

## プルリクエストの例
- [https://github.com/Siv3D/OpenSiv3D/pull/796 :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D/pull/796){:target="_blank"}
- [https://github.com/Siv3D/OpenSiv3D/pull/805 :material-open-in-new:](https://github.com/Siv3D/OpenSiv3D/pull/805){:target="_blank"}

機能に影響を与えない些細な変更や修正の場合は、Issue を作成せずに直接プルリクエストを作成することもできます。

## OpenSiv3D 実装会
GitHub の操作に不慣れな場合は、Siv3D Discord サーバーや OpenSiv3D 実装会でサポートします。

