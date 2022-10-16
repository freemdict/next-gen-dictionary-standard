# 格式规范

使用 SQLite 3 作为词典存储载体。

## 1. metadata 表

词典的**所有**基本信息，如词条数，作者，年月，出版社，编码，zstd 字典（base64）等，是否有全文索引，详细列表可之后完善再列出。

| 字段名 | 数据类型 | 其他 | 含义 | 
| :--- | :--- | :--- | :--- |
| id | int | AI PK | - | 
| key  | text |  | - | 
| value | text  | | - |

## 2. content 表

非跳转词条数据。

| 字段名 | 数据类型 | 其他 | 含义 | 
| :--- | :--- | :--- | :--- |
| id | int | AI PK | - | 
| key | text | indexed | 词头 |
| value | blob | zstd 压缩数据 | 词条数据 |
| seq | int | nullable | 词条顺序 |

## 3. link 表

注意：变形词等由词典软件处理。

跳转词条数据。如 `沸く` 跳转到 `わ・く` 。

| 字段名 | 数据类型 | 其他 | 含义 | 
| :--- | :--- | :--- | :--- |
| id | int | AI PK | - | 
| key | text | indexed |  词头 |
| to | int | - | content 表 id |
| type | text |  | 跳转的类型，可以是 `KEY` 或者 `EXT` ，如 @last_idol 所指 |

## 4. history 表

可选项。

| 字段名 | 数据类型 | 其他 | 含义 | 
| :--- | :--- | :--- | :--- |
| id | int | AI PK | - | 
| content_id | int | indexed | content 表 id | 
| value | blob | zstd 压缩数据 | 词条数据 |
| comment | text | | 修订说明 |

## 5. 全文索引

可选项，遵循 SQLite FTS5 规范。


## 6. 多媒体数据

使用 ZIP 进行存储。
