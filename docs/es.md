# Trading — mercaderes y la ventana de comercio

Trading añade tiendas a un juego. Una tienda son datos: qué vende el mercader, qué compra de vuelta y a qué precios. La abre un escenario, y el jugador comercia en una ventana con dos inventarios: los artículos del mercader a la izquierda, la bolsa del jugador a la derecha. El dinero es un objeto corriente, la moneda de oro: descansa en la bolsa, se guarda con ella y un escenario puede entregarla como cualquier otro objeto.

En RPG Plus comercia así el mercader del pueblo: háblale y se abre la ventana.

## Comprar

Elige artículos a la izquierda. El centro muestra el objeto, su precio, tu monedero, un deslizador de cantidad con **−**, **+** y **Todo**, y el total. **Comprar** cierra el trato:

![La ventana "Merchant's shop": los artículos del mercader a la izquierda con etiquetas de precio (elixir 10 c, espada de hierro 30 c, daga 18 c, bastón 24 c, armadura de cuero 20 c, túnica 16 c, anillo de plata 22 c, tónico de fuerza 40 c, resina 4 c, champiñón 3 c, ramas 2 c), el Elixir seleccionado en el centro (price 10, wallet 15, quantity 1/1, Total: 10 coins, Buy) con "Bought: Elixir ×1 for 10 coins", y la bolsa a la derecha con tres pieles de lobo y el elixir; el monedero arriba a la derecha marca 15.](assets/trade-buy.jpg)

El héroe llegó con 25 monedas; el elixir costó 10, así que el monedero marca 15 y el elixir está en la bolsa. El deslizador solo llega hasta donde el dinero alcanza — ahora 1.

## Vender

Elige un objeto de tu bolsa. Los objetos que el mercader compra llevan etiqueta de precio; los demás no se pueden vender aquí. **Todo** pone la cantidad en todo lo que tienes:

![La Silver wolf fur seleccionada desde la bolsa: Yours, price 4, you have 3, quantity 3/3, Total: 12 coins, Sell.](assets/trade-sell.jpg)

![Tras vender: "Sold: Silver wolf fur ×3 for 12 coins", la bolsa solo contiene el elixir y el monedero marca 27.](assets/trade-sold.jpg)

15 + 3 × 4 = **27** monedas. La ventana se cierra con Esc o con la cruz.

El surtido del mercader nunca se agota, y lo que vendes no vuelve a ofrecerse: una tienda no tiene estado propio, solo cambia tu bolsa.

## Una tienda en datos

Las tiendas son entidades de tipo `shop`, en la carpeta `shops/` de un mod. El mercader del pueblo tal como lo define RPG Demo:

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

- `Name` — la clave de traducción del título de la ventana.
- `Sells` — lo que el jugador puede comprar, en ese orden, con el precio de una unidad.
- `Buys` — por lo que el mercader paga; todo lo demás lo rechaza.
- `Currency` (opcional) — el objeto de dinero; por defecto es `money/coin`, la moneda de oro de este mod.
- Un precio menor que 1 se ignora: un objeto gratis es un regalo, y los regalos son cosa del escenario.

RPG Plus añade su equipo y sus mercancías al mismo mercader sin tocar el archivo de RPG Demo, con un parche en su propio `patches/shop.json`:

```json
{ "Items": [ { "Id": "rpg_demo/village_merchant",
  "Sells$add": [ { "Item": "classic/iron_sword", "Price": 30 } ],
  "Buys$add": [ { "Item": "rpg_plus/wolf_pelt", "Price": 4 } ] } ] }
```

## Abrir una tienda

```scenario
text "¡Oh, un cliente! Elixires frescos, casi sin poso." key=rpg_demo/merchant/line1
action trade.open shop=rpg_demo/village_merchant
end
```

El evento de mapa del mercader ejecuta este escenario. Cualquier escenario puede abrir una tienda igual — una respuesta de diálogo, un clic en un objeto interactivo.

## Si algo falla

1. **Un objeto de la bolsa no se puede vender.** No está en el `Buys` de la tienda.
2. **Comprar no hace nada.** No alcanzan las monedas ni para uno; la ventana lo dice.
3. **La ventana no se abre.** El id de `shop=` no coincide con el `Id` de ninguna tienda; el log dice cuál.
