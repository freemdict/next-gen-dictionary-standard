# 格式规范

使用 SQLite 3 作为词典存储载体。不存在的值一律存储为 `null`。 

## 1. metadata 表

词典的**所有**基本信息，如词条数，作者，年月，出版社，编码，zstd 字典等，是否有全文索引，详细列表可之后完善再列出。

| 字段名 | 数据类型 | 其他 | 含义 | 
| :--- | :--- | :--- | :--- |
| id | int | AI PK | - |
| key  | text | - | - |
| value | text  | - | - |
| bin | blob | - | 存储二进制数据，如 zstd 字典 |

**必须存储的词典信息**

以下信息必须存储在数据库，不存在则存储 `null`。

| 键名 | 含义 |
| :--- | :--- |
| ---词典基本信息--- | |
| name_main | 词典主要名字 |
| name_secondary | 词典的别名，如双语词典的第二个名字 |
| name_tertiary | 词典的别名 |
| author | 词典作者 |
| publisher | 出版社 |
| publish_at | 词典的出版日期 unix timestamp |
| info | 词典信息 |
| maker | SQLite 词典制作者 |
| make_at | 制作 SQLite 电子词典的日期 unix timestamp |
| update_at | 更新 SQLite 电子词典的日期 unix timestamp |
| maker_comments | SQLite 电子词典制作者备注 |
| ---技术层面--- | |
| encoding | 编码 utf8 或者 utf16 |
| zstd_compress_level | 压缩等级 |
| zstd_compression_dict | zstd 字典，二进制数据，null 则不使用自定义字典 |
| fts_enabled | 是否使用全文索引 1为是，0为否，此项待定 |

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
| type | text | - | 跳转的类型，可以是 `KEY` 或者 `EXT` |

## 4. history 表

可选项。

| 字段名 | 数据类型 | 其他 | 含义 | 
| :--- | :--- | :--- | :--- |
| id | int | AI PK | - | 
| content_id | int | indexed | content 表 id | 
| value | blob | zstd 压缩数据 | 词条数据 |
| comment | text | - | 修订说明 |

## 5. 全文索引

可选项，遵循 SQLite FTS5 规范。


## 6. 多媒体数据

使用 ZIP 进行存储。

# 搜索规范