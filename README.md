# 日本語比較構文アノテーションデータセット

## 概要
[BCCWJコーパス](https://clrd.ninjal.ac.jp/bccwj/)のうち，図書館サブコーパス（LB）から「より」または「と比べて」を含む文を全て抽出し，
重複を除き，整形したもの（計3454文）に対して，比較構文のアノテーションを施したものである。
「より」または「と比べて」を含む文が比較構文を構成しない文もある（例：「学校よりお知らせが来ました」）。
このような文についても，除外せずに収録した。

## 利用方法
### 復号
BCCWJコーパスのライセンスの制約により，本文は公開できず，アノテーション部分のみの公開となる。
BCCWJコーパスのDVD（v1.1）を所持している場合は，以下の手順で復号できる。

1. [このページ](https://github.com/ABCTreebank/abctk.utils.comparative/releases/latest)からバイナリをダウンロードする。
    * 現時点では，Linux AMD64環境のみサポート。
2. 以下を実行する。
```sh
# バイナリの名前を変更する。
mv downloaded_binary abctk.utils.comparative
# 実行権限を与える。
chmod +x abctk.utils.comparative 
```
3. BCCWJコーパス（Disc 03）から必要なデータを抽出する。
```sh
abctk.utils.comparative BCCWJ cache DVD_03_folder cache.pickle
```
4. 本文を復号する。
```sh
abctk.utils.comparative annot \
    load -e jsonl -s separate ABCT-COMP-BCCWJ_0001.jsonl \
    incorp-text BCCWJ cache.pickle \
    decrypt \
    write -e jsonl -s separate output.jsonl
```
他のオプションについては，[説明ファイル](https://github.com/ABCTreebank/abctk.utils.comparative/blob/main/README.md)を参照。

### 形式
アノテーションファイルは，JSONL形式で保存されている。
ファイルにおいては，1行ごとに文が1つ，JSON objectとして記録されている。
JSON objectは以下のスキーマを持つ：
* ID: `string`: 文のID
* tokens: `list[string]`: 文（BCCWJ/Unidicの短単位で区切られている）
* comp: `list[object]`: 比較構文のアノテーション。文のスパンとしてアノテートされている。
    * start: `int`: `tokens`におけるスパンの開始位置
    * end: `int`: `tokens`におけるスパンの終了位置（最後の語の1つ後）
    * label: `string`: 比較構文のラベル
* comments: `list[string]`: アノテーションにおけるコメント（通常の利用では不要）
* ID_v1: `string`: 旧ID（通常の利用では不要）

## タグ
* `root`: 比較構文のスコープ全体
* `prej`: 「より」句。
    * 「より」の補部を含めた「より」句全体に対して振る。助詞が後続する場合は助詞を含める。
    * 例：「**現金よりも**，カードの方が便利だ」
    * 名前の由来：prejacent（pivotのほうがよりよい用語だったが）
* `cont`:
    * 「より」句と対比される要素。助詞を伴う場合は助詞を含めて振る。
    * 例：「現金よりも，**カードの方が**便利だ」
    * 名前の由来：contrast
* `deg`: 程度表現を提供する要素（述語か連用修飾句）。
    * 例：「3キロ**速い**」
    * 名前の由来：predicate of degrees (gradable predicate)
* `diff`: 差についての表現
    * 例：「**3キロ**速い」
    * 名前の由来：difference

## 構文
通常の構文におけるアノテーションの例：
```yaml
- ID: ABCT-COMP-BCCWJ;yori;LBj3_00106,9710
  annot: |-
    [ユダヤ地下政府とユダヤ指導者たちは、[『旧約聖書』よりも]prej [はるかに]diff [このバビロニア版の「ユダヤ・タルムード」を]cont [重んじ]deg ている。]root
```
ここで注目すべきは，この例において，`root`は構文全体のスコープをスパンとしてアノテートしていることである。
比較構文の各要素は，この`root`のスコープの中に含まれている。

ただし，連体修飾構文において，比較構文の要素が連体修飾の主要部にあたる場合，当該主要部へのアノテーションは，`root`スパンの外側でなされるものとする。
```yaml
ID: ABCT-COMP-BCCWJ;yori;LBg2_00048,14780
annot: |-
    [[私より]prej [もっと]diff 条件 の [悪い]deg]root [人]cont も い ます 。
```

程度表現は，程度述語が担う場合が多い：
```yaml
ID: ABCT-COMP-BCCWJ;yori;LBo9_00076,19520
annot: |-
  頭の回路という見方でいけば、[[父よりも]prej [母のほうが]cont 回路の数としては [多かっ]deg た]root ように思う。
```
しかし，程度表現を連用修飾句が担う場合もある：
```yaml
ID: ABCT-COMP-BCCWJ;yori;LBl3_00152,30380
annot: |-
  飛行機に乗れば、[[鳥より]prej [高く]deg [速く]deg 飛ぶ]root 能力を備えた。
```

## 作成者
- 天本 貴之
- 林 則序
- 峯島 宏次
- 窪田 悠介

## ライセンス
[CC-BY 4.0](./LICENSE)