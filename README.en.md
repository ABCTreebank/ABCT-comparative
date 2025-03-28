# Dataset on Japanese Comparative Constructions

## Overview
This dataset consists of sentences containing _yori_ (より) or _to kurabete_ (と比べて) extracted from the Library Subcorpus (LB) of the [BCCWJ Corpus](https://clrd.ninjal.ac.jp/bccwj/).
After removing duplicates and formatting, a total of 3,454 sentences were annotated for comparative constructions.

Note that some sentences containing _yori_ or _to kurabete_ do not form comparative constructions (e.g., 学校よりお知らせが来ました 'A notice has come **from** the school'). 
Such sentences are INCLUDED from the dataset.

## Usage
### Decryption
Due to the licensing issues of the BCCWJ Corpus, the texts cannot be made public.
Thus we provide annotations along with the texts obfuscated.

The texts can be recovered by following the steps below if you own the BCCWJ Corpus DVD set (v1.1).

1. [Download the binary](https://github.com/ABCTreebank/abctk.utils.comparative/releases/latest).
    * Currently, only Linux AMD64 environments are supported.
2. Execute the following commands:
```sh
# Rename the binary
mv downloaded_binary abctk.utils.comparative
# Grant the execution permission
chmod +x abctk.utils.comparative 
```
3. Extract the necessary data from the BCCWJ Corpus (Disc 03).
```sh
abctk.utils.comparative BCCWJ cache DVD_03_folder cache.pickle
```
4. Decrypt the text.
```sh
abctk.utils.comparative annot \
    load -e jsonl -s separate ABCT-COMP-BCCWJ_0001.jsonl \
    incorp-text BCCWJ cache.pickle \
    decrypt \
    write -e jsonl -s separate output.jsonl
```
For other options, refer to the [documentation](https://github.com/ABCTreebank/abctk.utils.comparative/blob/main/README.md).

### Format
The annotation files are saved in the JSONL format. 
Each line corresponds to a single sentence, recorded as a JSON object. 
The JSON object follows the schema below:
* ID: `string`: The ID of the sentence.
* tokens: `list[string]`: The text of the sentence, tokenized into short units (短単位) defined by BCCWJ & Unidic.
* comp: `list[object]`: Annotations for comparative constructions. annotated as spans within the sentence.
    * start: `int`: The starting position of the span in `tokens`.
    * end: `int`: The ending position of the span in `tokens` (the position after the last token in the span).
    * label: `string`: The label for the comparative construction.
* comments: `list[string]`: Remarks by annotators (irrelevant for most cases).
* ID_v1: `string`: The old ID (irrelevant for most cases).

## Tags
* `root`: The entire clause of the comparative construction.
* `prej`: The _yori_ (より) phrase.
    * The entire _yori_ phrase, including its complement, is annotated. 
      If a particle follows, include the particle. 
    * Example: **現金よりも**, カードの方が便利だ '**than cash**, cards are more convenient'
    * Origin of the name: prejacent (_pivot_ would have been a better term but was not used).
* `cont`:
    * The part contrasted with the _yori_ phrase.
    * If accompanied by a particle, include the particle.
    * Example: 現金よりも, **カードの方が**便利だ 'than cash, **cards** are more convenient'.
    * Origin of the name: contrast
* `deg`: The element(s) providing degree expressions (either a predicate or a modifying adveribal).
    * Example: 3キロ**速い** '3 kilometers **faster**'
    * Origin of the name: predicate of degrees (gradable predicate)
* `diff`: The element(s) denoting difference.
    * Example: **3キロ**速い '**3 kilometers** faster'
    * Origin of the name: difference

## Syntax
Here is a standard example:
```yaml
- ID: ABCT-COMP-BCCWJ;yori;LBj3_00106,9710
  annot: |-
    [ユダヤ地下政府とユダヤ指導者たちは、[『旧約聖書』よりも]prej [はるかに]diff [このバビロニア版の「ユダヤ・タルムード」を]cont [重んじ]deg ている。]root
```
It is noted that in this example, the `root` span covers the entier scope of the construction.
The elements of the comparative construction are all inside this span.

For cases where the head of a relative clause bears some role(s) in the comparative construction, the role(s) are annotated to the head out of the `root` span.
However, in the case of a relative clause construction where an element of the comparative construction corresponds to the head of the relative clause, the annotation for the head should be placed outside the `root` span.
```yaml
ID: ABCT-COMP-BCCWJ;yori;LBg2_00048,14780
annot: |-
    [[私より]prej [もっと]diff 条件 の [悪い]deg]root [人]cont も い ます 。
```

Degree expressions are often provided by gradable predicates:
```yaml
ID: ABCT-COMP-BCCWJ;yori;LBo9_00076,19520
annot: |-
  頭の回路という見方でいけば、[[父よりも]prej [母のほうが]cont 回路の数としては [多かっ]deg た]root ように思う。
```
However, they can also be provided by adverbial modifiers:
```yaml
ID: ABCT-COMP-BCCWJ;yori;LBl3_00152,30380
annot: |-
  飛行機に乗れば、[[鳥より]prej [高く]deg [速く]deg 飛ぶ]root 能力を備えた。
```

## Contributors
- Amamoto, Takayuki
- Hayashi, Nori
- Mineshima, Koji
- Kubota, Yusuke

## License
[CC-BY 4.0](./LICENSE)