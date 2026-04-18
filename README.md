# テーブルリレーション構造可視化

演奏会情報管理システムのデータベーステーブル間のリレーション構造を可視化するツールです。

## ファイル構成

- `extract_relations.py`: テーブル定義ファイルからリレーション構造を抽出するPythonスクリプト
- `table-relations.json`: 抽出されたリレーション構造データ（JSON形式）
- `table-relations-visualization.html`: ブラウザで表示する可視化HTMLファイル

## 使い方

### 1. リレーション構造の抽出

テーブル定義ファイルを解析してリレーション構造を抽出します：

```bash
python3 extract_relations.py
```

このスクリプトは以下の処理を行います：

1. `Docs/テーブル設計/` 配下のすべてのテーブル定義ファイル（`*テーブル定義.md`）を検索
2. 各ファイルからテーブル名、カテゴリ、説明、外部キー制約を抽出
3. リレーション構造を以下の2つの形式で出力：
   - `table-relations.json`: JSON形式（Webサーバー使用時）
   - `table-relations-data.js`: JavaScript変数形式（**Webサーバー不要、推奨**）

### 2. 可視化の表示

**Webサーバーは不要です。** HTMLファイルを直接ブラウザで開けます：

```bash
# macOSの場合
open table-relations-visualization.html

# Linuxの場合
xdg-open table-relations-visualization.html

# Windowsの場合
start table-relations-visualization.html
```

または、ブラウザのファイルメニューから直接開いてください。

### データの読み込み方法

HTMLファイルは2つの方法でデータを読み込みます：

1. **JavaScript変数から読み込み（推奨）**: `table-relations-data.js` を `<script>` タグで読み込みます。**Webサーバー不要**で動作します。
2. **fetchでJSONから読み込み**: `table-relations.json` を `fetch()` APIで読み込みます。この方法は**Webサーバーが必要**です（ローカルファイルシステムのセキュリティ制限のため）。

デフォルトでは方法1が使用されるため、**Webサーバーなしで動作します**。

## 機能

### インタラクティブな操作

- **ドラッグ&ドロップ**: ノード（テーブル）をドラッグして配置を調整
- **ズーム**: マウスホイールでズームイン/アウト
- **ノードクリック**: テーブルをクリックすると詳細情報を表示
  - テーブルの説明
  - 外部キー（参照先テーブル）
  - 参照元テーブル

### カテゴリ別の色分け

テーブルは以下のカテゴリごとに色分けされています：

- **イベント中心** (#FF6B6B): `events` テーブル
- **マスタテーブル** (#4ECDC4): 種別、会場、役割などのマスタテーブル
- **イベント関連** (#00bfff): イベントと他のエンティティの中間テーブル
- **人物・団体** (#FFE66D): 人物・団体関連のテーブル
- **作品** (#A8E6CF): 作品関連のテーブル
- **メディア管理** (#C7CEEA): メディアファイル関連のテーブル
- **投稿記事** (#B5EAD7): 投稿記事関連のテーブル
- **システム管理** (#FFDAC1): システム管理関連のテーブル

### 操作ボタン

- **全体表示**: すべてのノードが画面に収まるように調整
- **ズームリセット**: ズームレベルを1.0にリセット
- **レイアウトリセット**: 物理演算を再実行してレイアウトを再配置

## データ構造

`table-relations.json` の構造：

```json
{
  "tables": [
    {
      "name": "events",
      "category": "イベント中心",
      "description": "演奏会・イベントの基本情報を管理",
      "foreign_keys": [
        {
          "column": "event_type_id",
          "references_table": "event_types",
          "references_column": "id"
        }
      ]
    }
  ],
  "graph": {
    "nodes": [...],
    "edges": [...]
  },
  "categories": [...],
  "category_colors": {...}
}
```

## 更新方法

テーブル定義ファイルを更新した場合は、以下の手順で可視化を更新してください：

1. テーブル定義ファイルを編集（`Docs/テーブル設計/` 配下）
2. `extract_relations.py` を再実行してJSONを再生成
3. ブラウザで `table-relations-visualization.html` をリロード

### 外部キー抽出の対象

スクリプトは次のいずれかから参照先を読み取ります。

- `## N. SQL定義` 内の `REFERENCES テーブル(カラム)`
- `## 外部キー制約` / `### N.N 外部キー制約` / `#### 外部キー制約` 配下の箇条書き（`` `列` → `参照先.id` `` または `` `列`: `表(id)` ``）

いずれにも外部キーが書かれていないテーブルは、他テーブルへの矢印が表示されません（孤立ノードになることがあります）。

### 直近の再生成（2026-04-18）

- テーブル定義ファイル数: 50
- グラフ上のリレーション（エッジ）数: `extract_relations.py` 実行結果に依存（例: 66）

## 注意事項

- `table-relations.json` と `table-relations-data.js` は `extract_relations.py` を実行することで自動生成されます
- HTMLファイルは `table-relations-data.js` と同じディレクトリに配置してください
- **Webサーバーは不要です**。HTMLファイルを直接ブラウザで開けます
- テーブル定義ファイルを更新した場合は、`extract_relations.py` を再実行してデータファイルを再生成してください

## 技術スタック

- **vis.js Network**: ネットワークグラフの可視化ライブラリ
- **Python 3**: テーブル定義ファイルの解析スクリプト

