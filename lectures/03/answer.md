# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：第1回の素のLLMは講座固有の「3レベル」を知らず、答えも揺れた。エージェントは用語集を検索してから答え、基礎・実装・発展のどれか1つで完了、と正しい内容を返した。
- `--trace` で見えたツール呼び出し（ツール名と引数）：`search_glossary({'query': 'LoRA'})` と `search_glossary({'query': 'QLoRA'})`。用語は1つずつ検索していた。
- 1つの質問での LLM 呼び出し回数（`requests`）：2（`tool_calls` も 2）
- 追加したツールと、docstring に書いた説明：`make_quiz(lecture: int)` を追加し、`agent.tool_plain(tools.make_quiz)` で登録した。docstring には「確認問題を出して」「クイズを1問」と頼まれたときに使うこと、`lecture` は1始まり、と書いた。`--list-tools` で3ツールが見え、「第2回の内容から確認問題を1問出して」では `make_quiz({'lecture': 2})` が呼ばれ、QLoRA の問題が出た。`requests` は 2、`pytest` は 15 passed。

## 宿題

- 取り組んだレベル：基礎
- やったこと：`app/agent.py` の `INSTRUCTIONS` から、「用語や講座に固有のことを答える前に、必ず search_glossary で用語集を確認する。」の1行を削除した。同じ質問 `LoRAとQLoRAの違いは？` を `--trace` で再実行し、削除前と比べた。比較後、この行は戻していない。
- 結果：削除前も削除後も `search_glossary({'query': 'LoRA'})` と `search_glossary({'query': 'QLoRA'})` が呼ばれ、`requests` はどちらも 2、`tool_calls` も 2 だった。回答の内容も、用語集に沿った LoRA / QLoRA の説明で大きくは変わらなかった。
- 気づき・疑問：全体指示の1行を消しても、ツールの docstring（答える前に使う、用語は1つずつ、など）が残っていると、モデルは同じように検索することがある。指示は `INSTRUCTIONS` と docstring の両方に効く、と分かった。