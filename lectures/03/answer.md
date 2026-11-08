# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：素のLLMは講座の情報がないがagentはツールを用いて検索することが可能
- `--trace` で見えたツール呼び出し（ツール名と引数）：  
[tool call]   search_glossary({'query': 'LoRA'})
[tool call]   search_glossary({'query': 'QLoRA'})
- 1つの質問での LLM 呼び出し回数（`requests`）：2回（複数回）
- 追加したツールと、docstring に書いた説明：  
    - 講義の内容から講義に関係する情報をglossary.jsonから検索し、クイズを生成するmake_quizというtool。
    - """  
    指定した回（1始まりの整数）の用語から、ランダムに1つ確認問題を作る。  
    ユーザーが講義内容の確認問題を求めたときに使う。返り値には問題と答えを含む。  
    """


## 宿題

- 取り組んだレベル：基礎
- やったこと：INSTRUCTIONSの説明の変更やツールの使い方(docstring)の変更
- 結果：  

| 変更内容 | 質問 | 変更前 | 変更後 | 結果,考察 |
|---|---|---|---|---|
|INSTRUCTIONSの「用語や講座に固有のことを答える前に、必ず `search_glossary` で用語集を確認する。」を削除| LoRAとQLoRAの違いは？ |`search_glossary`を2回使用|`search_glossary`を2回使用| `search_glossary`のdocstringを見たモデルが使用すべきと判断したのではないか？ |
| INSTRUCTIONSに「`search_glossary`は使用しない」と明示 | LoRAとQLoRAの違いは？ | `search_glossary`を2回使用 | `search_glossary`を使用しなかった。「関係する回：第7回」という出力があったが正しくないためハルシネーションが発生した。 | 講義資料の確認が行われず不確かな内容を生成したのではないかと考える |
| INSTRUCTIONSの「回の内容を聞かれたら `get_lecture_summary` を使う。」を削除 | 第三回の内容を教えて | `get_lecture_summary`を使用 | `get_lecture_summary`を使用 | `search_glossary`のdocstringを見たモデルが使用すべきと判断したのではないか？ |
| INSTRUCTIONSの「回答の最後に、`関係する回（第N回）`を示す」を削除 | 第三回の内容について教えて | 表示あり | 表示なし | モデルが説明を読み守った |
| `search_glossary`の使用方法(docstrin)を変更。queryに調べたい単語を複数入力させる。 | LoRAとQLoRAの違いは？ |search_glossary({'query': 'LoRA'}), search_glossary({'query': 'QLoRA'})を2回呼び出し | search_glossary({'query': 'LoRA,QLoRA'})を1回呼び出し | docstringを読み`search_glossary`の呼び出し方が変化した |

- 気づき:  
「用語や講座に固有のことを答える前に、必ず search_glossary で用語集を確認する。」という指示を削除してもツールを使用したように、モデルにツールの使用を指示せずとも、使用できるツールとツールの説明を見たモデル側がある程度勝手に判断して適切なツールを選択し、使用できることが分かった。  
使用するツールを細かく指示するよりもある程度モデル側にその判断を移譲するほうが効率が良いかもしれないと感じた。（人間のマイクロマネジメントを避ける理由と同様に）  
また注意すべき点として、「ツールを使用しない」と明示したときにツールの使用が必要な質問をした場合はハルシネーションを生成したため、人間の確認も必要だと感じた。

- 疑問：  
AIモデルを提供する各社が作っているtoolとそれをまとめたハーネスはどのような構成で動いていて、モデルの呼び出しやデータのやり取りはどのように行われているのだろうと感じました。
