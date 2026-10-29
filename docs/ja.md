# Trading——商人と取引ウィンドウ

Trading はゲームに店を追加します。店はデータです: 商人が何を売り、何を買い取り、いくらか。開くのはシナリオで、プレイヤーは 2 つのインベントリを持つウィンドウで取引します — 左に商人の品物、右にプレイヤーのかばん。お金は普通のアイテム、金貨です: かばんの中にあり、かばんと一緒にセーブされ、シナリオはほかのアイテムと同じように渡せます。

RPG Plus では村の商人がこうして取引します: 彼に話しかければウィンドウが開きます。

## 買う

左で品物を選びます。中央にはアイテム、価格、あなたの財布、**−** と **+** と **すべて** の付いた数量スライダー、合計が出ます。**買う** で取引が成立します:

!["Merchant's shop" ウィンドウ: 左に値札付きの商人の品物（エリクサー 10 c、鉄の剣 30 c、短剣 18 c、杖 24 c、革の鎧 20 c、ローブ 16 c、銀の指輪 22 c、力のトニック 40 c、樹脂 4 c、キノコ 3 c、枝 2 c）。中央でエリクサーを選択（price 10, wallet 15, quantity 1/1, Total: 10 coins, Buy)し、"Bought: Elixir ×1 for 10 coins" と表示。右はかばんで、狼の毛皮 3 枚とエリクサー。右上の財布は 15。](assets/trade-buy.jpg)

主人公は 25 枚を持ってきて、エリクサーに 10 枚払ったので、財布には 15 枚残り、エリクサーはかばんの中です。スライダーはお金のあるところまでしか動きません — 今は 1 まで。

## 売る

かばんでアイテムを選びます。商人が買い取るアイテムには値札が付き、ほかはここでは売れません。**すべて** は数量を所持数いっぱいにします:

![かばんから Silver wolf fur を選択: Yours, price 4, you have 3, quantity 3/3, Total: 12 coins, Sell。](assets/trade-sell.jpg)

![売却後: "Sold: Silver wolf fur ×3 for 12 coins"、かばんにはエリクサーだけ、財布は 27。](assets/trade-sold.jpg)

15 + 3 × 4 = **27** 枚。Esc か × でウィンドウを閉じます。

商人の在庫は減らず、売ったものが店に戻ることもありません: 店には自分の状態がなく、変わるのはあなたのかばんだけです。

## データとしての店

店は `shop` 種類のエンティティで、mod の `shops/` フォルダに入ります。村の商人を RPG Demo はこう定義します:

```json
{
  "Kind": "shop",
  "Items": [
    {
      "Id": "rpg_demo/village_merchant",
      "Name": "rpg_demo/shop/village_merchant",
      "Sells": [ { "Item": "rpg_demo/elixir", "Price": 10 } ],
      "Buys": [
        { "Item": "rpg_demo/branches", "Price": 1 },
        { "Item": "rpg_demo/stones", "Price": 2 }
      ]
    }
  ]
}
```

- `Name` — ウィンドウ見出しの翻訳キー。
- `Sells` — プレイヤーが買えるもの。この順で、1 個の値段付き。
- `Buys` — 商人がお金を払うもの。それ以外は拒否します。
- `Currency`（省略可）— お金のアイテム。既定はこの mod の金貨 `money/coin`。
- 1 未満の値段は無視されます: 無料のアイテムは贈り物で、贈り物はシナリオの仕事です。

RPG Plus は、RPG Demo のファイルを触らずに、自分の `patches/shop.json` のパッチで同じ商人に装備と品物を追加します:

```json
{ "Items": [ { "Id": "rpg_demo/village_merchant",
  "Sells$add": [ { "Item": "classic/iron_sword", "Price": 30 } ],
  "Buys$add": [ { "Item": "rpg_plus/wolf_pelt", "Price": 4 } ] } ] }
```

## 店を開く

```scenario
text "おや、お客さんだ! エリクサーは新鮮、沈殿もほとんどなし。" key=rpg_demo/merchant/line1
action trade.open shop=rpg_demo/village_merchant
end
```

商人のマップイベントがこのシナリオを実行します。どんなシナリオでも同じ方法で店を開けられます — ダイアログの選択肢からでも、インタラクティブなオブジェクトのクリックからでも。

## うまくいかないとき

1. **かばんのアイテムが売れない。** 店の `Buys` にありません。
2. **買うが何もしない。** 1 個も買えるお金がありません。ウィンドウがそう言ってくれます。
3. **ウィンドウが開かない。** `shop=` の id がどの店の `Id` とも一致しません。ログが名前を挙げます。
