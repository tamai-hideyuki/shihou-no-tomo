# 名称：shihou-no-tomo

## 概要：
司法の友（shihou-no-tomo）は、e-Gov法令XML APIを利用し、
日本法令を取得・整形・検索し、Mistral GGUFモデルで自然言語応答を行う軽量RAGエージェントです。

## 構造：
```
shihou-no-tomo/
├── README.md              
├── docs/
│   └── design_spec.md      # 詳細設計書
├── requirements.txt        # Python依存ライブラリ一覧
├── data/
│   └── minpou.xml          # e-Gov APIから取得した生XML
├── parsed/
│   └── minpou.json         # 条文単位に整形したJSONデータ
├── embeddings/
│   └── index.faiss         # FAISSインデックスファイル
├── models/
│   └── mistral-7b-q4.gguf  # GGUF量子化モデル
└── app/
    ├── govlaw_parser.py    # XML→JSON変換スクリプト
    ├── embedder.py         # 条文埋め込み作成スクリプト
    ├── retriever.py        # ベクトル検索スクリプト
    └── rag_infer.py        # RAG推論実行スクリプト
```

