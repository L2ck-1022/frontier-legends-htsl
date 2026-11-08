# Frontier Legends（Hypixel Housing RPG）

Minecraft のサーバー Hypixel の Housing 上で動く RPG「Frontier Legends」のスクリプト（HTSL）です。

プレイヤーは森林、暗い森、砂漠、荒野、雪原、鉱山などのエリアで敵を倒してレベルを上げ、装備や装飾品、ペットをそろえて強くなっていきます。

## このリポジトリについて

- HTSL のコードは、Housing の設定画面に処理を読み込むためのものです。
- アイテムの中身など一部は、読み込んだ後にゲーム内で手作業で仕上げています。そのため、ここにあるコードと実際のゲームには一部違いがあります。
  - オークションの出品アイテムは、コード上では仮の名前（`test`）のままです。
  - レベルアップに必要な経験値の式は、テストプレイの結果をもとにゲーム内で調整しています（下の「成長」を参照）。

## 制作体制

共同制作です。スクリプトの実装は主に私（L2ck）が担当し、マップの建築とテストプレイは仲間と分担しました。

## フォルダ構成

| ファイル | 内容 |
| --- | --- |
| `Areas/Forest.htsl` ほか | エリアごとの敵、ドロップ、ショップなど（森林、暗い森、砂漠、荒野、雪原、鉱山、Sandworm） |
| `Main Things/Attacking.htsl` | 通常の敵への攻撃、会心、受けるダメージ、倒したときの報酬 |
| `Main Things/Overall Attacking.htsl` | Fierce による追加攻撃、全プレイヤー共通のボスへの攻撃とランキング |
| `Main Things/Loop.htsl` | 能力値の計算、レベルアップ、倒されたときの処理、毒、オークションの入札 |
| `Main Things/Accessories.htsl` | 装飾品の能力値と効果 |
| `Main Things/Pets.htsl` | ペットの能力値と、状態の保存（3進数） |
| `Main Things/Quest.htsl` | クエストの判定と、達成状況の保存（2進数） |
| `Main Things/Event.htsl` | 定期イベント（経験値、採掘・伐採、オークション、パルクール） |
| `Main Things/Global Boss.htsl` | 全プレイヤー共通のボス |
| `Main Things/Mining.htsl`、`Foraging.htsl` | 採掘と伐採 |
| `Main Things/Afk Attack.htsl` | `/afkattack` コマンド |
| `Main Things/Reforge.htsl` | 装備強化 |
| `Mini Things/Daily Reward.htsl` | 毎日の報酬 |
| `Mini Things/Redstone-upg.htsl` | レッドストーンによる能力の強化 |
| `Mini Things/Menus.htsl`、`Other.htsl` | メニュー、スポーン地点、各エリアのショップ |
| `Mini Things/Player Join.htsl` | 参加したときの初期値の設定 |
| `Mini Things/Sounds.htsl` | 効果音 |
| `Museum.htsl` | 博物館 |
| `Story Quest.htsl` | ストーリークエスト |

## 主な仕組み

### 戦闘
攻撃力を $s$、会心率を $c_c$ [%]、会心ダメージを $c_d$ [%]、倍率を $m$ [%] とすると、1回の攻撃のダメージは、確率 $c_c$ % で $s \cdot \frac{c_d}{100} \cdot \frac{m}{100}$、それ以外は $s \cdot \frac{m}{100}$ です。

敵から受けるダメージは、敵の攻撃力を $S$、防御力を $d$、敵のレベルを $L$ として、次の式で求めます（`Attacking.htsl` の 20〜43 行目）。

$$D_{\mathrm{taken}} = S\left(1 - \frac{d}{d + L/2}\right)$$

### 成長
レベル $L$ から次のレベルに必要な経験値は、ゲーム内では次の式です。

$$X(L) = 10\left\lfloor \frac{25L(L+1) + L^3}{10} \right\rfloor$$

`Loop.htsl` には、調整前の式 $25L(L+1) + \lfloor L^3/10 \rfloor$ が残っています。

### Magic Find（MF）
ドロップ率は、基本の確率 $p_0$ [%] に対して $p = \min\left(100,\ p_0 \cdot \frac{\mathrm{MF}}{100}\right)$ です（`Forest.htsl`、`Desert.htsl`）。

### データの保存
クエストの達成状況は1つの整数の2進数の各桁に、ペットの状態（0〜2）は3進数の各桁にまとめて保存しています。

## AI の利用について

次の部分は AI の助けを借りて制作しました。それ以外のスクリプトは自分で書いています。

- 装備強化（`Reforge.htsl`）
- 博物館（`Museum.htsl`）
- ペットのコードの軽量化（仕組みは自分で設計し、軽量化のみ AI に依頼）
- ストーリークエストの NPC のせりふ（進行の仕組みは自分で作成）
