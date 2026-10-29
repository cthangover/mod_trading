# Trading — merchants and the trade window

Trading adds shops to a game. A shop is data: what the merchant sells, what they buy back, and at which prices. A scenario opens it, and the player trades in a window with two inventories — the merchant's goods on the left, the player's bag on the right. Money is an ordinary item, the gold coin, so it lies in the bag, is saved with it and can be given by a scenario like any other item.

In RPG Plus the village merchant trades this way: talk to him and the window opens.

## Buying

Pick goods on the left. The middle shows the item, its price, your wallet, a quantity slider with **−**, **+** and **All**, and the total. **Buy** completes the deal:

![The Merchant's shop window: the merchant's goods on the left with price tags (elixir 10 c, iron sword 30 c, dagger 18 c, staff 24 c, leather armour 20 c, robe 16 c, silver ring 22 c, strength tonic 40 c, resin 4 c, mushroom 3 c, branches 2 c), the Elixir selected in the middle (price 10, wallet 15, quantity 1/1, Total: 10 coins, Buy) with "Bought: Elixir ×1 for 10 coins", and the bag on the right with three wolf furs and the elixir; the wallet at the top right shows 15.](assets/trade-buy.jpg)

The hero came with 25 coins; the elixir cost 10, so the wallet shows 15 and the elixir is in the bag. The slider only goes as far as the money allows — now 1.

## Selling

Pick an item in your bag. Items the merchant buys have a price tag; the others cannot be sold here. **All** sets the quantity to everything you have:

![The Silver wolf fur selected from the bag: Yours, price 4, you have 3, quantity 3/3, Total: 12 coins, Sell.](assets/trade-sell.jpg)

![After selling: "Sold: Silver wolf fur ×3 for 12 coins", the bag holds only the elixir, the wallet shows 27.](assets/trade-sold.jpg)

15 + 3 × 4 = **27** coins. Esc or the cross closes the window.

The merchant's stock never runs out, and what you sell is not offered back: a shop has no state of its own, only your bag changes.

## A shop in data

Shops are entities of kind `shop`, in a mod's `shops/` folder. The village merchant as RPG Demo defines it:

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

- `Name` — the translation key of the window title.
- `Sells` — what the player can buy, in this order, with the price of one.
- `Buys` — what the merchant pays for; anything else is refused.
- `Currency` (optional) — the money item; the default is `money/coin`, the gold coin of this mod.
- A price below 1 is ignored: a free item is a gift and belongs in a scenario.

RPG Plus adds its gear and goods to the same merchant without touching RPG Demo's file, with a patch in its own `patches/shop.json`:

```json
{ "Items": [ { "Id": "rpg_demo/village_merchant",
  "Sells$add": [ { "Item": "classic/iron_sword", "Price": 30 } ],
  "Buys$add": [ { "Item": "rpg_plus/wolf_pelt", "Price": 4 } ] } ] }
```

## Opening a shop

```scenario
text "Oh, a customer! Elixirs, fresh, almost no sediment." key=rpg_demo/merchant/line1
action trade.open shop=rpg_demo/village_merchant
end
```

The merchant's map event runs this scenario. Any scenario can open a shop the same way — a dialog answer, a click on an interactive object.

## If something goes wrong

1. **An item in the bag cannot be sold.** It is not in the shop's `Buys`.
2. **Buy does nothing.** Not enough coins for one; the window says so.
3. **The window does not open.** The `shop=` id does not match a shop's `Id`; the log names it.
