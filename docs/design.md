## 📜 shihou-no-tomo：設計書（V1.0）

## 🎯 目的：
- 日本のe-Gov法令XML APIをもとに、
法令データを抽出・整形・検索できるローカル特化型LLM「shihou-no-tomo」を構築する。

## ⚙️ 技術スタック：
| 項目      | 技術                                             | 理由             |
| ------- | ---------------------------------------------- | -------------- |
| LLM     | Mistral 7B Instruct (GGUF, q4\_K\_M)           | Metal対応・軽量・高性能 |
| 推論ランタイム | llama.cpp (macOS Metal)                        | MacBook Air最適  |
| ベクトル検索  | FAISS                                          | 単純・軽量・ローカル完結   |
| 埋め込みモデル | sentence-transformers（e.g. `all-MiniLM-L6-v2`） | 軽量で高精度         |
| API連携   | e-Gov 法令XML API                                | 公的、構造化、信頼性あり   |
| UI      | CLI                                            | まずはシンプルに       |

## 🏗️ ディレクトリ構成（初期）
```
shihou-no-tomo/
├── README.md
├── data/
│   └── minpou.xml             # e-GovからDLした法令
├── parsed/
│   └── minpou.json            # 条文単位で整形されたデータ
├── embeddings/
│   └── index.faiss            # ベクトルインデックス
├── models/
│   └── mistral-7b-q4.gguf     # GGUFモデル（手動DL）
├── app/
│   ├── govlaw_parser.py       # XML → JSON整形スクリプト
│   ├── embedder.py            # 条文を埋め込みベクトル化
│   ├── retriever.py           # クエリに対し類似条文検索
│   └── rag_infer.py           # llama.cppで実行、回答生成
└── requirements.txt

```

## 🔁 処理フロー（構文詩）：
1.法令取得（手動 or スクリプト）

- curl https://elaws.e-gov.go.jp/api/1/lawdata/民法

- data/minpou.xml に保存

2.パース & 整形

- govlaw_parser.py にてXML → JSON

- 条文番号・条文内容を保持した配列形式

3.埋め込みベクトル化

- embedder.py：条文ごとに埋め込みを取得

- FAISSにより index.faiss を生成

4.クエリ実行（RAG）

- retriever.py：ユーザークエリに類似条文を返す

- rag_infer.py：対象条文をプロンプトに埋め込み、Mistralで解釈・要約を行う

## 🧪 プロンプトテンプレート（system prompt）
```
あなたは法律の専門家です。
以下の日本法令の条文を参考に、質問に簡潔かつ正確に答えてください。

【参考条文】
{context}

【質問】
{question}

```

## 💡 機能要件（MVP）:
| 機能     | 内容                        |
| ------ | ------------------------- |
| 法令パース  | e-Gov XML → JSON変換（条文単位）  |
| 条文検索   | 類似質問に該当する条文を返す（FAISS）     |
| 要約・説明  | llama.cpp により自然言語で回答      |
| CLI UI | `python main.py` にて質問対話可能 |

## 💻 システム要件:
| 要素       | 内容                      |
| -------- | ----------------------- |
| OS       | macOS (Apple Silicon対応) |
| RAM      | 10GB以内                  |
| CUDA/GPU | 不要（Metalにて推論）           |
| Python   | 3.10〜3.12（venv推奨）       |

