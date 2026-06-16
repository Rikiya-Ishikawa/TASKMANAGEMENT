# 詳細設計書

## 物理データ設計（テーブル定義）

### listsテーブル

| カラム名 | データ型 | 制約 | 説明 |
|---------|---------|------|------|
| id | INTEGER | PRIMARY KEY | 識別番号（自動採番） |
| name | TEXT | NOT NULL | リスト名（未着手など） |
| position | INTEGER | NOT NULL | 表示順（左から1,2,3） |

### cardsテーブル

| カラム名 | データ型 | 制約 | 説明 |
|---------|---------|------|------|
| id | INTEGER | PRIMARY KEY | 識別番号（自動採番） |
| title | TEXT | NOT NULL | カードのタイトル |
| list_id | INTEGER | NOT NULL, FOREIGN KEY | 所属するリストのid |
| position | INTEGER | NOT NULL | リスト内での表示順 |
| created_at | TEXT | NOT NULL | 作成日時 |

### テーブルの関係

```
lists                    cards
┌──────────────┐         ┌──────────────────┐
│ id           │◀────────│ list_id          │
│ name         │  1対多   │ id               │
│ position     │         │ title            │
└──────────────┘         │ position         │
                         │ created_at       │
                         └──────────────────┘
```

---

## API設計

### エンドポイント一覧

| メソッド | URL | 説明 |
|---------|-----|------|
| GET | /lists | 全リストとカードを取得する |
| POST | /cards | カードを新規作成する |
| DELETE | /cards/{id} | 指定したカードを削除する |
| PATCH | /cards/{id} | カードを更新する（移動時に使用） |

### 各エンドポイントの詳細

**GET /lists**
```json
// レスポンス
[
  {
    "id": 1,
    "name": "未着手",
    "cards": [
      { "id": 1, "title": "企画書作成", "position": 1 },
      { "id": 3, "title": "会議の準備", "position": 2 }
    ]
  },
  {
    "id": 2,
    "name": "進行中",
    "cards": [
      { "id": 2, "title": "メール返信", "position": 1 }
    ]
  },
  {
    "id": 3,
    "name": "完了",
    "cards": []
  }
]
```

**POST /cards**
```json
// リクエスト
{ "title": "新しいタスク", "list_id": 1 }

// レスポンス
{ "id": 4, "title": "新しいタスク", "list_id": 1, "position": 3 }
```

**DELETE /cards/{id}**
```json
// リクエスト：なし（URLのidで指定）例：DELETE /cards/4

// レスポンス
{ "message": "deleted" }
```

**PATCH /cards/{id}**
```json
// リクエスト
{ "list_id": 2, "position": 1 }

// レスポンス
{ "id": 1, "title": "企画書作成", "list_id": 2, "position": 1 }
```
