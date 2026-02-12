**Communication Diagram details-**

**Entities and Their Roles:**

| Entity | Role in the System |
| --- | --- |
| Buyer | Initiates the transaction by browsing items and placing bids |
| Catalog | Displays available items and handles bid placement and payment requests |
| Bidding | Manages the bidding process, allowing buyers to compete for items |
| Payment | Processes financial transactions once a bid is accepted |
| Order & Shipping | Creates orders and manages logistics for item delivery |
| Seller | Fulfils the order by shipping the item |

**Interaction Flow Explained**

1.  **Buyer → Catalog: "Place Bid"**
    *   The buyer selects an item and places a bid via the catalog interface.
    *   This triggers the catalog to initiate the bidding process.
2.  **Buyer → Bidding: "Buy Item"**
    *   If the bid is successful or the item is available for direct purchase, the buyer proceeds to finalize the purchase through the bidding system.
3.  **Catalog → Payment: "Request Payment"**
    *   Once the buyer commits to the purchase, the catalog requests payment authorization.
4.  **Bidding → Payment: "Buy Item"**
    *   The bidding system confirms the buyer’s intent and passes control to the payment system for transaction processing.
5.  **Payment → Order & Shipping: "Create Order"**
    *   After successful payment, the system generates an order and initiates shipping logistics.
6.  **Order & Shipping → Seller: "Ship Item"**
    *   The seller receives the order details and ships the item to the buyer.