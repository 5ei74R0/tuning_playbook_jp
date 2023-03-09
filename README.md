### From the translator, 5ei74R0
This document is a translated & organized to be more accessible version of awesome [google-research/tuning_playbook](https://github.com/google-research/tuning_playbook).
<br>
I manually translated the original, in order to avoid potential mistranslations on technical terms and/or phrasing which could be caused by probabilistic output of the translation model trained on general-purpose corpora.
<br>
I would appreciate it if you could please leave a star for [this](https://github.com/5ei74R0/tuning_playbook_jp) and/or [original](https://github.com/google-research/tuning_playbook) repositories.
<br>
(March 5<sup>th</sup>, 2023)
<br>
<br>

本書は [google-research/tuning_playbook](https://github.com/google-research/tuning_playbook) を情報にアクセスしやすいよう翻訳・整理したものです．
<br>
汎用コーパスで学習させた翻訳モデルの確率的な出力に起因する専門用語や表現の誤訳を低減するため，可能な限り手動で翻訳しています．
<br>
よろしければ[本リポジトリ](https://github.com/5ei74R0/tuning_playbook_jp)や[元リポジトリ](https://github.com/google-research/tuning_playbook)にスターを付けていただけると幸いです．
<br>
(2023/03/05)
<br>
<br>

# Deep Learning Tuning Playbook (日本語版)

**本書は公式にサポートされたGoogleのプロダクトではありません．**

**Varun Godbole<sup>&dagger;</sup>, George E. Dahl<sup>&dagger;</sup>, Justin Gilmer<sup>&dagger;</sup>, Christopher J. Shallue<sup>&Dagger;</sup>, Zachary Nado<sup>&dagger;</sup>**


&dagger; Google Research, Brain Team

&Dagger; Harvard University

## 目次

-   [本書の対象読者](#本書の対象読者)
-   [Why a tuning playbook?](#why-a-tuning-playbook)
-   [新規プロジェクトを開始するためのガイド](#新規プロジェクトを開始するためのガイド)
    -   [モデルアーキテクチャの選択](#モデルアーキテクチャの選択)
    -   [オプティマイザの選択](#オプティマイザの選択)
    -   [バッチサイズの選択](#バッチサイズの選択)
    -   [ハイパーパラメータ等の初期設定](#ハイパーパラメータ等の初期設定)
-   [モデル性能を改善するための科学的アプローチ](#モデル性能を改善するための科学的アプローチ)
    -   [漸進的なチューニング戦略](#漸進的なチューニング戦略)
    -   [探索と活用 (Exploration vs exploitation)](#探索と活用-Exploration-vs-exploitation)
    -   [次の実験における目標設定](#次の実験における目標設定)
    -   [次の実験の設計](#次の実験の設計)
    -   [学習パイプラインに対する変更及びハイパーパラメータの更新を行うか判断する](#学習パイプラインに対する変更及びハイパーパラメータの更新を行うか判断する)
    -   [探索空間を特定した後 (After exploration concludes)](#探索空間を特定した後-After-exploration-concludes)
-   [学習におけるステップ数の決定](#学習におけるステップ数の決定)
    -   [学習時間の決定 (計算資源が制約とならない場合)](#学習時間の決定-計算資源が制約とならない場合)
    -   [学習時間の決定 (計算資源が制約となる場合)](#学習時間の決定-計算資源が制約となる場合)
-   [学習パイプラインに関する追加資料](#学習パイプラインに関する追加資料)
    -   [入力パイプラインの最適化入力パイプラインの最適化](#入力パイプラインの最適化)
    -   [モデルの性能評価](#モデルの性能評価)
    -   [チェックポイントの保存と最適なチェックポイントの選択](#チェックポイントの保存と最適なチェックポイントの選択)
    -   [実験のトラッキング](#実験のトラッキング)
    -   [Batch Normalizationの実装の詳細](#Batch-Normalizationの実装の詳細)
    -   [マルチホストの学習に関する注意](#マルチホストの学習に関する注意)
-   [FAQs](#faqs)
-   [謝辞](#謝辞)
-   [引用](#引用)
-   [Contributing](#contributing)

## 本書の対象読者
本書の対象読者は**深層学習モデルのパフォーマンスの最大化**に興味を持っているエンジニア，研究者です．本書は読者が機械学習及び深層学習の概念についての基本的な知識を持っていることを仮定しています．

本書は**ハイパーパラメータチューニングの過程**に重点を置いていますが，パイプラインの実装や最適化など，深層学習の他の側面についても触れます．ただし，これらの側面を完全に扱うことは意図していません．

本書では機械学習で扱う問題が教師あり学習や類似の形式（例えば自己教師あり学習）であることを仮定します．とはいえ，ここに記載された処方箋はその他の形式のタスクに関しても適用し得るものです．


## Why a tuning playbook?
昨今，深層学習モデルを実際にうまく機能させるために，驚くほど多くの労力や推測（当てずっぽう）が費やされています．
さらに悪いことに，人々が実際に深層学習で良い結果を得るために使用したレシピを文書化することは稀です．論文ではよりきれいなストーリーを提示するために，最終的な結果に至った過程は隠蔽されてしまいますし，商業的な問題に取り組んでいる機械学習エンジニアが立ち戻って自身の踏んだ過程を一般化するために時間を取ることはほとんどありません．また，教科書はたとえその著者が応用的な仕事に置いて必要な経験を持ち，有益なアドバイスを提供できるとしても，実践的な指導を避けて基本原則の解説を優先する傾向にあります．
我々が本書を作成する準備をする際，「どのように深層学習で良い結果を得るか」を実際に説明しようという包括的な試みを見つけることはできませんでした．代わりに見つかったのはブログ記事やソーシャルメディア上の断片的なアドバイス，研究論文の付録にちらりと顔をのぞかせているトリック，さらに特定のプロジェクト，パイプラインに関連する偶発的なケーススタディ，そしてあまたの混乱です．
深層学習のエキスパートとそれに比べて熟達していない実務者が達成する成果には大きな隔たりがありますが，同時にこうしたエキスパートは，自身が行っている操作の中には十分に検証，証明されているわけではないものがあることをあっさり認めます．
深層学習が成熟し，より大きなインパクトを世界に与えるようになる中，そのコミュニティには，良い結果を得る上で極めて重要になる実践的な詳細の全てを内包し，便利なレシピを網羅するリソースがより多く必要であると言えます．

我々は長年（早いものは2006年から）深層学習に携わってきた5人のエンジニアと研究者からなるチームです．深層学習を音声認識から天文学まで幅広い問題に適用し，多くのことを学んできた．本書は我々自身がネットワークの学習，新たな機械学習エンジニアの教育，そして自身の同僚に対する深層学習の実用に関するアドバイスを行ってきた経験からうまれました．
深層学習が一握りの学術研究所で行われていた機械学習の手法から，数十億もの人々が利用する製品を支える技術になったことは喜ばしいことではありますが，未だに深層学習は工学分野としては黎明期にあります．本書がこの分野の実験手続きの体系化の一助となることを期待しています．

本書は我々自身の深層学習へのアプローチを明瞭化する試みからうまれたものです．そのため，本書は客観的事実ではなく執筆時点での著者の意見を示したものです．我々自身が特に苦労してきたのがハイパーパラメータチューニングであるため，特にハイパーパラメータチューニングに焦点が当てられています．しかし，我々が遭遇したその他の重要な（失敗に繋がった）問題に関しても取り上げます．
我々は本書が我々の信念の変化に応じて育ち，進化する生きたドキュメントにしたいと考えています．例えば学習失敗のデバッグ，低減に関する資料は最近の結果，あるいは進行中の調査に基づいているので，2年前には書けないものでした．
本書によるアドバイスのいくつかは，必然的に新たな成果やワークフローの改善を考慮して更新する必要があります．我々は深層学習のレシピとして最適なものを知りませんし，これを見つけるにはコミュニティが様々な手順を書き出して議論を始めねばなりません．そのため，本書にあるアドバイスに問題を見つけた読者には，我々が本書の内容を更新していけるよう，説得力のある証拠とともに代替案を提供していただくことを奨励します．
また，我々はコミュニティとしてベストプラクティスを目指す上で，本書のアドバイスとは異なる助言を伴う別のガイドやプレイブックを見てみたいと考えています．
最後に，🤖の絵文字が付いたセクションはさらなる調査を行いたいと考えている箇所です．
本書の執筆を試みて初めて，深層学習実践者のワークフローの中にどれだけ多くの興味深い，無視されてきた研究課題が存在するかを完全に理解しました．

## 新規プロジェクトを開始するためのガイド
チューニングの過程で生じる意思決定の多くはプロジェクト開始時に一度行われ，時折，状況が変化した時のみ再検討され得ます．

本ガイダンスにおいて，以下の事項を仮定します．
- 問題の定式化やデータクリーニングなどの本質的な作業が十分に行われており，モデルアーキテクチャ及び学習設定に時間をかける意義があること．
- 学習及び評価を行うためのパイプラインが既に構築されており，学習と予測を関心のある様々なモデルに対して簡単に実行できること．
- 適切な評価指標が選択，実装されていること．その評価指標が，モデルをデプロイする環境において行われる測定を極力代表するものであること．

### モデルアーキテクチャの選択

**概要：** 新しくプロジェクトを始める際は，既に動作するモデルの再利用を試みましょう．

- 作業を始める際は，十分に地位を確立しており，よく使われるモデルアーキテクチャを選択しましょう．カスタマイズしたモデルならあとでいつでも作成できます．
- モデルアーキテクチャは典型的にはモデルサイズやその他の詳細を決定する様々なハイパーパラメータを持っています．<br>（例：層数，層の幅，活性化関数の種類）
    - つまり，モデルアーキテクチャの選択というのは，実際には異なるモデルファミリーを選択することを意味します．（ハイパーパラメータの設定ごとに1つの特定のモデルに対応するモデルファミリーを選択することを意味します．）
    - モデルのハイパーパラメータ設定に関しては，[ハイパーパラメータ等の初期設定](#ハイパーパラメータ等の初期設定)，[モデル性能を改善するための科学的アプローチ](#モデル性能を改善するための科学的アプローチ)において後述します．
- 可能であれば対象の問題にできる限り近い課題に取り組んでいる論文を見つけ，出発点としてそのモデルの再現をすることを試みましょう．

### オプティマイザの選択

**概要：** 対象とする種類の問題において最もよく使われているオプティマイザから始めましょう．

- あらゆる問題形式，モデルアーキテクチャに対して最適なオプティマイザは存在しません．[単にオプティマイザの性能を比較するだけの作業であっても難しいものです．](https://arxiv.org/abs/1910.05446)🤖
- 特に新しくプロジェクトを開始する際は，十分に地位を確立しており，よく使われるオプティマイザを使い続けることをお薦めします．
    - 理想を言えば同種の課題に対してよく用いられているオプティマイザを選ぶことが望ましいです．
- 選択したオプティマイザの **「全ての」** ハイパーパラメータに注意を払う覚悟をしておきましょう．
    - より多くのハイパーパラメータを持つオプティマイザの最適な設定を見つけるにはチューニングにおいてより多くの手間を必要とします．
    - これは特にプロジェクトの初期段階に関連します．モデルアーキテクチャのハイパーパラメータなどの様々なハイパーパラメータの最適な値を決める過程で，さらにオプティマイザのハイパーパラメータにまで[nuisance parameters](#scientific-hyperparameternuisance-hyperparameterfixed-hyperparameterの特定)として気を配らなければならないのです．
    - プロジェクトの初期段階ではシンプルなオプティマイザ（例えば固定のモーメンタムを持つSGDや$\beta_{1}, \beta_{2}$を固定したAdam）から初めて，後から汎用的なオプティマイザに変更するのがよいかもしれません．
- 十分に地位を確立しているオプティマイザとして我々が好んでいるものには（これらには限りませんが）次のようなオプティマイザが挙げられます．
    - [SGD with momentum](#有名な深層学習モデルの最適化アルゴリズムの更新則は?)（特にNesterov variantを好んでいます）
    - [AdamおよびNAdam](#有名な深層学習モデルの最適化アルゴリズムの更新則は?)はSGD with momentumよりも一般的な手法です．Adamは4つのチューニング可能なハイパーパラメータを持ち，[その全てが重要](https://arxiv.org/abs/1910.05446)であることに注意してください．
        - [Adamのハイパーパラメータのチューニング方法は？](#adamのハイパーパラメータのチューニング方法は)も参照してください．

### バッチサイズの選択

**概要：** バッチサイズは学習速度を左右するものであり，検証集合での性能を向上させるために直接使用されるべきではありません．多くの場合理想的なバッチサイズは利用できるハードウェアに載る範囲で最大のものになります．

- バッチサイズは学習時間と計算資源の消費量を決定する鍵となる要素です．
    - 大抵バッチサイズを大きくすると学習時間は短縮します．これは例えば以下のような理由で非常に有益です．
        - 決まった時間内でより徹底的なハイパーパラメータチューニングを行えるので，最終的により良いモデルが得られる可能性があります．
        - 開発サイクル中の待ち時間を削減できるので，新しいアイデアをより頻繁に検証できます．
- バッチサイズの増加によって計算資源の消費量は増加するかもしれませんし，或いは減少するかもしれません．はたまた変わらない可能性もあります．
- バッチサイズは検証集合における性能をチューニングできるハイパーパラメータとして扱うべきでは**ありません**．
    - すべてのハイパーパラメータ（特に学習率や正則化ハイパーパラメータ）が適切にチューニングされ，学習ステップ数が十分である限り，どのバッチサイズでも同じ最終パフォーマンスを達成できます [[Shallue et al. 2018]](https://arxiv.org/abs/1811.03600)．
    - [なぜバッチサイズは直接検証集合での性能を改善するためにチューニングしない方が良いのですか？](#なぜバッチサイズは直接検証集合での性能を改善するためにチューニングしない方が良いのですか)を参照してください．

#### 実行可能なバッチサイズの決定及び学習スループットの推定

<details><summary><em>[Click to expand]</em></summary>

<br>

- モデルとオプティマイザが与えられたとき，通常は利用できるハードウェアに載るバッチサイズの範囲があり，普通はハードウェアアクセラレータのメモリが制約になります．
- 残念なことに，学習を行うプログラム全体を実行，コンパイルせずにメモリに収まるバッチサイズを計算することは困難です．
- 最も簡単なのは学習を様々なバッチサイズで（例えば2の冪で増やしながら）少ないステップだけ実行し，メモリに収まらなくなるバッチサイズを探す方法です．
- バッチサイズに対する学習スループット（"training throughput"）或いは1ステップ当たりの処理時間（"time per step"）を堅実に推定するには十分に長い時間学習を実行しなければなりません．

<p align="center">training throughput = (# examples processed per second)</p>
<p align="center">time per step = (batch size) / (training throughput)</p>

-   When the accelerators aren't yet saturated, if the batch size doubles, the
    training throughput should also double (or at least nearly double).
    Equivalently, the time per step should be constant (or at least nearly
    constant) as the batch size increases.
-   If this is not the case then the training pipeline has a bottleneck such as
    I/O or synchronization between compute nodes. This may be worth diagnosing
    and correcting before proceeding.
-   If the training throughput increases only up to some maximum batch size,
    then we should only consider batch sizes up to that maximum batch size, even
    if a larger batch size is supported by the hardware.
    -   All benefits of using a larger batch size assume the training throughput
        increases. If it doesn't, fix the bottleneck or use the smaller batch
        size.
    -   **Gradient accumulation** simulates a larger batch size than the
        hardware can support and therefore does not provide any throughput
        benefits. It should generally be avoided in applied work.
-   These steps may need to be repeated every time the model or optimizer is
    changed (e.g. a different model architecture may allow a larger batch size
    to fit in memory).

</details>


### ハイパーパラメータ等の初期設定

## モデル性能を改善するための科学的アプローチ

### 漸進的なチューニング戦略

**概要：** XXX

- XXX

### 探索と活用 (Exploration vs exploitation)

**概要：** XXX

- XXX

### 次の実験における目標設定

**概要：** XXX

- XXX


### 次の実験の設計

**概要：** XXX

#### Scientific hyperparameter，nuisance hyperparameter，fixed hyperparameterの特定
<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

#### Creating a set of studies

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>


#### Striking a balance between informative and affordable experiments

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

### Extracting insight from experimental results

**概要：** XXX

- XXX

#### Identifying bad search space boundaries

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

#### Not sampling enough points in the search space

<details><summary><em>[クリックして開く]</em></summary>

<br>


</details>

#### Examining the training curves

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>


#### Detecting whether a change is useful with isolation plots

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

#### Automate generically useful plots

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

### 学習パイプラインに対する変更及びハイパーパラメータの更新を行うか判断する

**概要：** XXX

- XXX

### 探索空間を特定した後 (After exploration concludes)

**概要：** XXX

- XXX

## 学習におけるステップ数の決定

- XXX

### 学習時間の決定 (計算資源が制約とならない場合)

- XXX

#### Algorithm for picking an initial candidate for max_train_steps using a learning rate sweep

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

### 学習時間の決定 (計算資源が制約となる場合)

- XXX

#### Round 1

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

#### Round 2

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

## 学習パイプラインに関する追加資料

### 入力パイプラインの最適化

**概要：** XXX

- XXX

### モデルの性能評価

**概要：** XXX

#### Evaluation settings

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

#### Setting up periodic evaluations

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

#### Choosing a sample for periodic evaluation

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

### チェックポイントの保存と最適なチェックポイントの選択

**概要：** XXX

- XXX

### 実験のトラッキング

**概要：** XXX

- XXX

### Batch Normalizationの実装の詳細

**概要：** XXX

- XXX

### マルチホストの学習に関する注意

**概要：** XXX

- XXX

## FAQs

### What is the best learning rate decay schedule family?

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

### Which learning rate decay should I use as a default?

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

### Why do some papers have complicated learning rate schedules?

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

### Adamのハイパーパラメータのチューニング方法は？

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

### Why use quasi-random search instead of more sophisticated black box optimization algorithms during the exploration phase of tuning?

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

### Where can I find an implementation of quasi-random search?

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

### How many trials are needed to get good results with quasi-random search?

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

### How can optimization failures be debugged and mitigated?

<details><summary><em>[クリックして開く]</em></summary>

<br>



**概要：** XXX

#### Identifying unstable workloads
- XXX

#### Potential fixes for common instability patterns
- XXX

#### Learning rate warmup
- XXX

##### When to apply learning rate warmup
- XXX

##### How to apply learning rate warmup
- XXX

#### Gradient clipping
- XXX

</details>

### Why do you call the learning rate and other optimization parameters hyperparameters? They are not parameters of any prior distribution.

<details><summary><em>[クリックして開く]</em></summary>

<br>


- XXX

</details>

### なぜバッチサイズは直接検証集合での性能を改善するためにチューニングしない方が良いのですか？

<details><summary><em>[クリックして開く]</em></summary>

<br>


- **学習パイプラインの他の要素を変更せず**にバッチサイズを変更すると，大抵の場合，検証集合での性能に影響します．
- ただし，学習パイプラインが各バッチサイズに対して独立に最適化される場合，2つのバッチサイズ間の検証集合での性能差は通常解消されます．
- バッチサイズと最も強く相互作用するハイパーパラメータは，オプティマイザのハイパーパラメータ（例：学習率，モーメント）と正則化のハイパーパラメータです．
    - **小さなバッチサイズは，サンプル分散によって学習アルゴリズムへのノイズを増やし，このノイズが正則化の効果を持つ可能性があります．そのため，大きなバッチサイズには過学習の傾向があり，より強力な正則化及びさらなる正則化テクニックを必要とする場合があります．**
- さらに，バッチサイズを変更する場合，学習ステップの数を調整する必要がある可能性があります．
- これら全ての効果を勘案すると，現時点では，バッチサイズが達成可能な検証集合での性能に影響を与えるという説得力のある証拠はありません[[Shallue et al. 2018]](https://arxiv.org/abs/1811.03600)．

</details>

### 有名な深層学習モデルの最適化アルゴリズムの更新則は?

<details><summary><em>[クリックして開く]</em></summary>

<br>


#### Stochastic gradient descent (SGD)

$$\theta_{t+1} = \theta_{t} - \eta_t \nabla \mathcal{l}(\theta_t)$$

#### Momentum

$$v_0 = 0$$

$$v_{t+1} = \gamma v_{t} + \nabla \mathcal{l}(\theta_t)$$

$$\theta_{t+1} = \theta_{t} - \eta_t v_{t+1}$$

#### Nesterov

$$v_0 = 0$$

$$v_{t+1} = \gamma v_{t} + \nabla \mathcal{l}(\theta_t)$$

$$\theta_{t+1} = \theta_{t} - \eta_t( \gamma v_{t+1} + \nabla \mathcal{l}(\theta_{t})$$

#### RMSProp

$$v_0 = 1 \text{,} m_0 = 0$$

$$v_{t+1} = \rho v_{t} + (1 - \rho) \nabla \mathcal{l}(\theta_t)^2$$

$$m_{t+1} = \gamma m_{t} + \frac{\eta_t}{\sqrt{v_{t+1} + \epsilon}}\nabla \mathcal{l}(\theta_t)$$

$$\theta_{t+1} = \theta_{t} - m_{t+1}$$

#### ADAM

$$m_0 = 0 \text{,} v_0 = 0$$

$$m_{t+1} = \beta_1 m_{t} + (1 - \beta_1) \nabla \mathcal{l} (\theta_t)$$

$$v_{t+1} = \beta_2 v_{t} + (1 - \beta_2) \nabla \mathcal{l}(\theta_t)^2$$

$$b_{t+1} = \frac{\sqrt{1 - \beta_2^{t+1}}}{1 - \beta_1^{t+1}}$$

$$\theta_{t+1} = \theta_{t} - \alpha_t \frac{m_{t+1}}{\sqrt{v_{t+1}} + \epsilon} b_{t+1}$$

#### NADAM

$$m_0 = 0 \text{,} v_0 = 0$$

$$m_{t+1} = \beta_1 m_{t} + (1 - \beta_1) \nabla \mathcal{l} (\theta_t)$$

$$v_{t+1} = \beta_2 v_{t} + (1 - \beta_2) \nabla \mathcal{l} (\theta_t)^2$$

$$b_{t+1} = \frac{\sqrt{1 - \beta_2^{t+1}}}{1 - \beta_1^{t+1}}$$

$$\theta_{t+1} = \theta_{t} - \alpha_t \frac{\beta_1 m_{t+1} + (1 - \beta_1) \nabla \mathcal{l} (\theta_t)}{\sqrt{v_{t+1}} + \epsilon} b_{t+1}$$

</details>

## 謝辞
- XXX

## 引用
**Translator's Note:**<br>以下の引用は本家 ([google-research/tuning_playbook](https://github.com/google-research/tuning_playbook)) のものになります．引用を考えている場合は，本家の英語版の内容を必ず確認した上でそちらを参照してください．

```
@misc{tuningplaybookgithub,
  author = {Varun Godbole and George E. Dahl and Justin Gilmer and Christopher J. Shallue and Zachary Nado},
  title = {Deep Learning Tuning Playbook},
  url = {http://github.com/google/tuning_playbook},
  year = {2023},
  note = {Version 1.0}
}
```

## Contributing
**Translator's Note:**<br>[google-research/tuning_playbook#contributing](https://github.com/google-research/tuning_playbook#contributing)をご確認ください．

### Contributor License Agreement
**Translator's Note:**<br>[google-research/tuning_playbook#contributor-license-agreement](https://github.com/google-research/tuning_playbook#contributor-license-agreement)をご確認ください．

### Code Reviews
**Translator's Note:**<br>[google-research/tuning_playbook#code-reviews](https://github.com/google-research/tuning_playbook#code-reviews)をご確認ください．

### Community Guidelines
**Translator's Note:**<br>[google-research/tuning_playbook#community-guidelines](https://github.com/google-research/tuning_playbook#community-guidelines)をご確認ください．
