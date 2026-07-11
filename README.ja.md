<!-- i18n: language-switcher -->
[English](README.md) | [日本語](README.ja.md)

# Hiveコネクタ

Hive用のネイティブいろどりテーブルコネクタ拡張。

このクレートは、コネクタのメタデータ、ネイティブABIエクスポート、およびいろどり拡張マーケットプレイスで使用されるドライバ実装をパッケージ化しています。

## コネクタ

- 拡張ID: `irodori.hive`
- エンジンID: `hive`
- ワイヤープロトコル: `jdbc`
- デフォルトポート: `10000`
- ネイティブABI: `irodori.connector.native.v1`
- ドライバリンク済み: `はい`
- マーケットプレイスの公開範囲: `公開`
- パッケージバージョン: `0.1.3`

このパッケージはコネクタのメタデータとネイティブドライバを直接使用します。デスクトップアダプタのソーススナップショットは必要ありません。

コネクタのメタデータは `connector.config.json` と `irodori.extension.json` に格納されています。
Rustクレートは `src/lib.rs` からネイティブABIをエクスポートし、`irodori-connector-abi` を共有JSON/バッファヘルパーとして使用し、コネクタの動作は `src/driver.rs` に保持しています。

## 接続メタデータ

- エンドポイントモード: `catalog`, `objectStorage`, `jdbc`, `connectionString`
- トランスポートモード: `direct`, `sshTunnel`, `socks5Proxy`, `httpConnectProxy`, `proxyChain`
- TLS対応: `はい`
- TLS必須（デフォルト）: `いいえ`
- カスタムドライバオプション: `はい`

### エンドポイントフィールド

| フィールド | ラベル | 型 | 必須 |
| --- | --- | --- | --- |
| `catalogUri` | カタログURI | `uri` | いいえ |
| `warehouse` | 倉庫パス | `string` | いいえ |

## 認証

このコネクタは、クライアントが適切な資格情報フィールドをレンダリングできるように、これらの認証モードを宣伝しています。必要に応じて、`options`を通じてドライバ固有またはプロバイダ固有の値を渡すことも可能です。

| 認証方法 | ラベル | 種類 | シークレットの用途 |
| --- | --- | --- | --- |
| `none` | 認証なし | `none` | なし |
| `connectionString` | 接続文字列 / DSN | `connectionString` | なし |
| `userPassword` | ユーザー/パスワード | `userPassword` | `password` |
| `basic` | ベーシック認証 | `userPassword` | `password` |
| `bearerToken` | ベアラートークン | `token` | `token` |
| `jwt` | JWTベアラートークン | `token` | `token` |
| `oauth2` | OAuth 2.0 | `oauth2` | `token` |
| `kerberos` | Kerberos / GSSAPI | `kerberos` | `token` |
| `ldap` | LDAPユーザー/パスワード | `userPassword` | `password` |
| `clientCertificate` | クライアント証明書 / mTLS | `certificate` | `privateKey`, `privateKeyPassphrase` |
| `customDriverOptions` | カスタムドライバオプション | `custom` | `password`, `token`, `privateKey`, `privateKeyPassphrase` |

## ネイティブABI呼び出し

| メソッド | 応答 |
| --- | --- |
| `health` | コネクタのヘルス状態、エンジンID、ABIバージョン、ドライバの状態を返します。 |
| `describe` | 埋め込みマニフェストとコネクタ設定を返します。 |
| `manifest` | 生の `irodori.extension.json` を返します。 |
| `config` | 生の `connector.config.json` を返します。 |
| `connect` | ネイティブコネクタ接続を開き、検証します。 |
| `query` | コネクタクエリを実行し、構造化された行またはJSON結果を返します。 |
| `metadata` | スキーマ、テーブル、カラム、インデックス、コレクション、または同等のメタデータを読み取ります。 |
| `close` | キャッシュされたネイティブ接続を閉じて削除します。 |

## 開発

このチェックアウト内のすべての拡張クレートは `../target` を共有しており、依存関係は兄弟リポジトリ間で一度だけコンパイルされます。

```sh
make check
make build
```

リリースパッケージはプラットフォーム固有のネイティブアーティファクトを `dist/native` に配置します。

## ライセンス

0BSD。ほぼすべての目的でこのプロジェクトを使用、コピー、修正、配布できます。