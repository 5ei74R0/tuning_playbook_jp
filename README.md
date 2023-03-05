### From the translator, 5ei74R0
This document is a translated & organized to be more accessible version of awesome [google-research/tuning_playbook](https://github.com/google-research/tuning_playbook).

I manually translated the original, in order to avoid potential mistranslations on technical terms and/or phrasing which could be caused by probabilistic output of the translation model trained on general-purpose corpora.

I would appreciate it if you could please leave a star for [this](https://github.com/5ei74R0/tuning_playbook_jp) and/or [original](https://github.com/google-research/tuning_playbook) repositories.

(March 5<sup>th</sup>, 2023)
<br>

本ドキュメントは [google-research/tuning_playbook](https://github.com/google-research/tuning_playbook) を情報にアクセスしやすいよう翻訳・整理したものです．

汎用コーパスで学習させた翻訳モデルの確率的な出力に起因する専門用語や表現の誤訳を低減するため，可能な限り手動で翻訳しています．

よろしければ[本リポジトリ](https://github.com/5ei74R0/tuning_playbook_jp)や[元リポジトリ](https://github.com/google-research/tuning_playbook)にスターを付けていただけると幸いです．

(2023/03/05)
<br>
<br>

# Deep Learning Tuning Playbook (日本語版)

*本ドキュメントは公式にサポートされたGoogleのプロダクトではありません．*

**Varun Godbole<sup>&dagger;</sup>, George E. Dahl<sup>&dagger;</sup>, Justin Gilmer<sup>&dagger;</sup>, Christopher J. Shallue<sup>&Dagger;</sup>, Zachary Nado<sup>&dagger;</sup>**


&dagger; Google Research, Brain Team

&Dagger; Harvard University

## 目次

-   [本ドキュメントの対象読者](#本ドキュメントの対象読者)
-   [Why a tuning playbook?](#why-a-tuning-playbook)
-   [新規プロジェクトを開始するためのガイド](#新規プロジェクトを開始するためのガイド)
    -   [モデルアーキテクチャの選択](#モデルアーキテクチャの選択)
    -   [オプティマイザの選択](#オプティマイザの選択)
    -   [バッチサイズの選択](#バッチサイズの選択)
    -   [ハイパーパラメータ等の初期設定](#ハイパーパラメータ等の初期設定)
-   [モデル性能を改善するための科学的アプローチ](#モデル性能を改善するための科学的アプローチ)
    -   [漸進的なチューニング戦略](#漸進的なチューニング戦略)
    -   [探索と活用 (Exploration vs exploitation)](#探索と活用-(Exploration-vs-exploitation))
    -   [次の実験における目標設定](#次の実験における目標設定)
    -   [次の実験の設計](#次の実験の設計)
    -   [学習パイプラインに対する変更及びハイパーパラメータの更新を行うか判断する](#学習パイプラインに対する変更及びハイパーパラメータの更新を行うか判断する)
    -   [探索空間を特定した後 (After exploration concludes)](#探索空間を特定した後-(After-exploration-concludes))
-   [学習におけるステップ数の決定](#学習におけるステップ数の決定)
    -   [学習時間の決定 (計算資源が制約とならない場合)](#学習時間の決定-(計算資源が制約とならない場合))
    -   [学習時間の決定 (計算資源が制約となる場合)](#学習時間の決定-(計算資源が制約となる場合))
-   [学習パイプラインに関する追加資料](#学習パイプラインに関する追加資料)
    -   [入力パイプラインの最適化入力パイプラインの最適化](#入力パイプラインの最適化)
    -   [モデルの性能評価](#モデルの性能評価)
    -   [チェックポイントの保存と最適なチェックポイントの選択](#チェックポイントの保存と最適なチェックポイントの選択)
    -   [実験のトラッキング](#実験のトラッキング)
    -   [Batch Normalizationの実装の詳細](#Batch-Normalizationの実装の詳細)
    -   [マルチホストのトレーニングに関する注意](#マルチホストのトレーニングに関する注意)
-   [FAQs](#faqs)
-   [謝辞](#謝辞)
-   [引用](#引用)
-   [Contributing](#contributing)

## 本ドキュメントの対象読者
XXX

## Why a tuning playbook?
XXX

## 新規プロジェクトを開始するためのガイド
XXX

### モデルアーキテクチャの選択

***概要:***
*新しくプロジェクトを始める際は，既に動作するモデルの再利用を試みよう．*

- XXX

### オプティマイザの選択

***概要:***
*XXX*

- XXX

### バッチサイズの選択

***概要:***
*XXX*

- XXX

### ハイパーパラメータ等の初期設定

## モデル性能を改善するための科学的アプローチ

### 漸進的なチューニング戦略

***概要:***
*XXX*

- XXX

### 探索と活用 (Exploration vs exploitation)

***概要:***
*XXX*

- XXX

### 次の実験における目標設定

***概要:***
*XXX*

- XXX


### 次の実験の設計

***概要:***
*XXX*

#### Identifying scientific, nuisance, and fixed hyperparameters
<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

#### Creating a set of studies

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>


#### Striking a balance between informative and affordable experiments

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

### Extracting insight from experimental results

***概要:***
*XXX*

- XXX

#### Identifying bad search space boundaries

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

#### Not sampling enough points in the search space

<details><summary><em>[Click to expand]</em></summary>

<br>


</details>

#### Examining the training curves

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>


#### Detecting whether a change is useful with isolation plots

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

#### Automate generically useful plots

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

### 学習パイプラインに対する変更及びハイパーパラメータの更新を行うか判断する

***概要:***
*XXX*

- XXX

### 探索空間を特定した後 (After exploration concludes)

***概要:***
*XXX*

- XXX

## 学習におけるステップ数の決定

- XXX

### 学習時間の決定 (計算資源が制約とならない場合)

- XXX

#### Algorithm for picking an initial candidate for max_train_steps using a learning rate sweep

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

### 学習時間の決定 (計算資源が制約となる場合)

- XXX

#### Round 1

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

#### Round 2

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

## 学習パイプラインに関する追加資料

### 入力パイプラインの最適化

***概要:***
*XXX*

- XXX

### モデルの性能評価

***概要:***
*XXX*

#### Evaluation settings

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

#### Setting up periodic evaluations

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

#### Choosing a sample for periodic evaluation

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

### チェックポイントの保存と最適なチェックポイントの選択

***概要:***
*XXX*

- XXX

### 実験のトラッキング

***概要:***
*XXX*

- XXX

### Batch Normalizationの実装の詳細

***概要:***
*XXX*

- XXX

### マルチホストのトレーニングに関する注意

***概要:***
*XXX*

- XXX

## FAQs

### What is the best learning rate decay schedule family?

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

### Which learning rate decay should I use as a default?

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

### Why do some papers have complicated learning rate schedules?

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

### How should Adam’s hyperparameters be tuned?

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

### Why use quasi-random search instead of more sophisticated black box optimization algorithms during the exploration phase of tuning?

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

### Where can I find an implementation of quasi-random search?

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

### How many trials are needed to get good results with quasi-random search?

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

### How can optimization failures be debugged and mitigated?

<details><summary><em>[Click to expand]</em></summary>

<br>



***概要:***
*XXX*

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

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

### Why shouldn't the batch size be tuned to directly improve validation set performance?

<details><summary><em>[Click to expand]</em></summary>

<br>


- XXX

</details>

### What are the update rules for all the popular optimization algorithms?

<details><summary><em>[Click to expand]</em></summary>

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
