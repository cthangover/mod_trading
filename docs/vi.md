# Trading — thương nhân và cửa sổ giao dịch

Trading thêm các cửa hàng vào trò chơi. Một cửa hàng là dữ liệu: thương nhân bán gì, mua lại gì, và giá bao nhiêu. Kịch bản mở cửa hàng ra, và người chơi giao dịch trong một cửa sổ có hai túi đồ — hàng của thương nhân bên trái, túi của người chơi bên phải. Tiền là một vật phẩm bình thường, đồng vàng: nó nằm trong túi đồ, được lưu cùng túi, và kịch bản có thể trao nó như mọi vật phẩm khác.

Trong RPG Plus, thương nhân làng giao dịch thế này: hãy nói chuyện với ông ấy, cửa sổ sẽ mở ra.

## Mua

Chọn hàng bên trái. Phía giữa hiển thị vật phẩm, giá của nó, túi tiền của bạn, một thanh trượt số lượng với **−**, **+** và **Tất cả**, cùng tổng cộng. **Mua** chốt cuộc mua bán:

![Cửa sổ "Merchant's shop": hàng của thương nhân bên trái kèm giá (thuốc tiên 10 c, kiếm sắt 30 c, dao găm 18 c, trượng 24 c, giáp da 20 c, áo choàng 16 c, nhẫn bạc 22 c, thuốc tăng lực 40 c, nhựa sống 4 c, nấm 3 c, cành cây 2 c), Elixir đang chọn ở giữa (price 10, wallet 15, quantity 1/1, Total: 10 coins, Buy) với "Bought: Elixir ×1 for 10 coins", bên phải là túi đồ có ba tấm lông sói và thuốc tiên; túi tiền góc trên phải hiển thị 15.](assets/trade-buy.jpg)

Anh hùng đến với 25 đồng; thuốc tiên giá 10, nên túi tiền còn 15 và thuốc tiên nằm trong túi đồ. Thanh trượt chỉ kéo tới mức tiền cho phép — hiện là 1.

## Bán

Chọn một vật phẩm trong túi đồ. Những vật phẩm thương nhân mua lại có gắn giá; những cái khác không bán được ở đây. **Tất cả** đặt số lượng bằng tất cả những gì bạn có:

![Silver wolf fur được chọn từ túi đồ: Yours, price 4, you have 3, quantity 3/3, Total: 12 coins, Sell.](assets/trade-sell.jpg)

![Sau khi bán: "Sold: Silver wolf fur ×3 for 12 coins", túi đồ chỉ còn thuốc tiên, túi tiền hiển thị 27.](assets/trade-sold.jpg)

15 + 3 × 4 = **27** đồng. Esc hay dấu × đều đóng cửa sổ.

Hàng của thương nhân không bao giờ cạn, và thứ bạn bán không được bày ra lại: cửa hàng không có trạng thái riêng, chỉ có túi đồ của bạn thay đổi.

## Cửa hàng dưới dạng dữ liệu

Cửa hàng là thực thể loại `shop`, nằm trong thư mục `shops/` của một mod. Thương nhân làng được RPG Demo định nghĩa như sau:

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

- `Name` — khóa dịch của tiêu đề cửa sổ.
- `Sells` — những gì người chơi có thể mua, theo đúng thứ tự này, kèm giá của một món.
- `Buys` — những gì thương nhân trả tiền; thứ khác đều bị từ chối.
- `Currency` (không bắt buộc) — vật phẩm tiền tệ; mặc định là `money/coin`, đồng vàng của mod này.
- Giá nhỏ hơn 1 bị bỏ qua: vật phẩm miễn phí là món quà, và quà tặng là việc của kịch bản.

RPG Plus thêm trang bị và hàng hóa của mình vào cùng thương nhân mà không đụng tới tệp của RPG Demo, bằng một bản vá trong `patches/shop.json` của nó:

```json
{ "Items": [ { "Id": "rpg_demo/village_merchant",
  "Sells$add": [ { "Item": "classic/iron_sword", "Price": 30 } ],
  "Buys$add": [ { "Item": "rpg_plus/wolf_pelt", "Price": 4 } ] } ] }
```

## Mở một cửa hàng

```scenario
text "Ồ, có khách! Thuốc tiên mới, hầu như không cặn." key=rpg_demo/merchant/line1
action trade.open shop=rpg_demo/village_merchant
end
```

Sự kiện bản đồ của thương nhân chạy kịch bản này. Kịch bản nào cũng có thể mở cửa hàng theo cách đó — một lựa chọn đối thoại, một cú bấm vào vật thể tương tác.

## Khi có gì đó không ổn

1. **Một vật phẩm trong túi đồ không bán được.** Nó không có trong `Buys` của cửa hàng.
2. **Mua không làm gì cả.** Không đủ tiền mua một món; cửa sổ nói điều đó.
3. **Cửa sổ không mở.** id trong `shop=` không khớp `Id` của cửa hàng nào; log sẽ nêu tên.
