## Trading Engine

This is a small trading engine implemented in Rust with some of the below functionality.
Each exchange maintains an Order Book for each security traded,
  - Limit Order Book
  - Centralized Limit Order Book

### Limit Order Book

Specification
  - Security symbol
  - Order direction
  - Limit price
  - Size 
  - Duration - GTC, DAY
Bid side represents open offers to buy, sorted in descending order.
Ask side represent open offers to sell, sorted in ascending order.
Trades are made when highest bid >= lowest ask(spread is crossed)
Price at which trade is executed is that of the trade already in the order book.
If client submits a buy or sell order that cannot be filled, it gets stored in order book.
Orders are executed at the best possible price first and if many order have the same price the one that was submitted earliest is chosen.
Liqudity is the ability to trade at a reasonable price at a given time, more order, more liquidity.
The spread is the difference between the best ask price and the best bid price in the market.
Limit price depends on aggresiveness of the trader, passive(portfolio), Active(speed, improves on the best bid,ask)
Marketable Limit order is a limit order prices at or better than the current market price.

### Interface

PlaceOrder(order)
CancelOrder(orderId)
  - actually remove order from the limit order
  - lazily mark order as cancelled, skipping it during execution.
GetVolumeAtPrice(price, buyingOrSelling)
  - Needs to happen as fast as possible, easier to precompute.

### Data structures for Limit Order

A heap inserts and pops at O(logn), minheap and maxheap
Order process
  - First check the lowest price of the sell side of the limit book.
  - If the lowest price of the sell side is less that or equal to the buy side, execute a trade.
  - If the buyer still has more volume left to fill, look at the next lowest price on the side and keep going.
  - If there is unfilled volume for the buyer;s trade, add it to the buyer heap!

### Matching Algorithms

FIFO - First In First Out.
PRO-RATA - Price volume/quantity priority gets in first.
TOP - Best price at a given quantity, best price at 200/=
LMM - Lead market maker, program amongst firms, i.e 10% trades.


### Market orders
Specification
  - Security symbol
  - Order direction
  - Size
Market orders will trade immediately at the best price available as they don't have a limit.
Market orders take away liquidity as opposed to limit orders which build up liquidity.
They don't specify price, they just specify buy or sell volumes.
Go through the opposite side of the order book until volume for trade is exhausted and then do not store trade in limit order book.
Timing is a huge concern.

### Trigger orders

Set to trigger once a trade executes above or below a certain price(trigger orders may inturn trigger other trigger orders)
Keep separate heaps for buy and sell trigger orders to determine if the price threshold has been reached to trigger them and if so start calling placeOrder with each triggered order.

