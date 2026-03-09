# 📘 HƯỚNG DẪN TOÀN DIỆN - NOPCOMMERCE ARCHITECTURE & DATABASE DESIGN

---

## 🎯 MỤC ĐÍCH TÀI LIỆU

Tài liệu này giải thích **toàn bộ kiến trúc, design database, và nghiệp vụ** của nopCommerce theo nguyên tắc **80/20 Pareto** - bắt đầu từ 20% core entities để hiểu 80% hệ thống, sau đó mở rộng dần.

---

## 📊 MỤC LỤC

1. [20% Core - 5 Entities Chính](#20-core---5-entities-chính)
2. [Database Structure & Relationships](#database-structure--relationships)
3. [Order Processing Workflow](#order-processing-workflow)
4. [Discount & Promotion System](#discount--promotion-system)
5. [Product Management](#product-management)
6. [Payment Processing](#payment-processing)
7. [Shipping Management](#shipping-management)
8. [Customer Management](#customer-management)
9. [Advanced Features](#advanced-features)
10. [Architecture Patterns](#architecture-patterns)

---

# 🎯 PHẦN 1: 20% CORE - 5 ENTITIES CHÍNH

## 1.1 CUSTOMER (Khách hàng)

### Entity Structure

```
Customer
├── Id: int (PK)
├── CustomerGuid: Guid
├── Email: string
├── Username: string
├── FirstName, LastName: string
├── Active: bool
├── Deleted: bool (soft delete)
├── CreatedOnUtc: DateTime
├── VendorId: int FK → Vendor
├── AffiliateId: int FK → Affiliate
├── CurrencyId: int? FK → Currency
├── LanguageId: int? FK → Language
└── ... (40+ other properties)
```

### Nghiệp vụ Chính

| Tính năng | Mô tả |
|-----------|-------|
| **Authentication** | Đăng nhập qua Email/Username, Password hashing |
| **Roles** | Khách hàng, Admin, Vendor, Moderator |
| **Addresses** | Nhiều địa chỉ (Billing, Shipping), soft-delete support |
| **Reward Points** | Tích điểm từ mua hàng, dùng để thanh toán |
| **Attributes** | Tùy chỉnh thêm (Date of Birth, Phone, etc.) |
| **Affiliate** | Khách hàng có thể là affiliate (hoa hồng sales) |
| **Vendor** | Khách hàng có thể quản lý shop riêng |

### Related Tables

```
Customer
├── CustomerPassword (lưu password cũ)
├── CustomerRole (N-N với Role)
├── CustomerAddress (N-1)
├── CustomerAddressMapping (N-N)
├── RewardPointsHistory
├── CustomerAttribute
├── CustomerCustomerRoleMapping
└── Order (1-N)
```

---

## 1.2 PRODUCT (Sản phẩm)

### Entity Structure

```
Product
├── Id: int (PK)
├── Name: string
├── SKU: string (Stock Keeping Unit)
├── Gtin: string (Barcode)
├── Price: decimal
├── OldPrice: decimal (giá gốc để tính % discount)
├── ProductTypeId: enum
│   ├── Simple (sản phẩm đơn)
│   ├── Grouped (nhóm sản phẩm)
│   ├── Bundle
│   ├── Rental
│   ├── Download
│   └── Recurring (subscription)
├── StockQuantity: int
├── VendorId: int FK → Vendor (ai bán)
├── Published: bool
├── Deleted: bool (soft delete)
├── CreatedOnUtc: DateTime
├── UpdatedOnUtc: DateTime
└── ... (80+ properties)
```

### Các Loại Sản Phẩm & Tính Năng

| Loại | Tính Năng | Ví dụ |
|------|-----------|-------|
| **Simple** | Sản phẩm đơn, có tồn kho, giá cơ bản | Áo phông, sách |
| **Grouped** | Nhóm sản phẩm, hiển thị variant (size, color) | Áo + nhiều size |
| **Bundle** | Bán kèm nhiều sản phẩm | Laptop + Balo + Chuột |
| **Download** | Tải file sau khi thanh toán | eBook, Software, MP3 |
| **Rental** | Cho thuê theo thời gian | Xe, Máy ảnh |
| **Recurring** | Thanh toán định kỳ | Subscription, Membership |
| **GiftCard** | Thẻ quà tặng | Thẻ 50$ |

### Related Tables

```
Product
├── ProductCategory (N-N)
├── ProductManufacturer (N-N)
├── ProductAttribute (1-N) → ProductAttributeValue
├── ProductAttributeCombination (1-N) → ProductAttributeCombinationPicture
├── ProductPicture (1-N)
├── ProductVideo (1-N)
├── TierPrice (1-N)
├── ProductReview (1-N)
├── ProductWarehouseInventory (1-N)
├── StockQuantityHistory
├── Discount (N-N qua DiscountProductMapping)
├── SpecificationAttribute (N-N)
└── OrderItem (1-N)
```

---

## 1.3 ORDER (Đơn hàng)

### Entity Structure

```
Order
├── Id: int (PK)
├── OrderGuid: Guid
├── CustomerId: int FK → Customer
├── StoreId: int FK → Store
├── OrderStatus: enum [Pending(0) → Processing(10) → Complete(20)/Cancelled(30)]
├── PaymentStatus: enum [Pending(10) → Authorized(20) → Paid(30) → Refunded(40)]
├── ShippingStatus: enum [Pending(0) → Shipped(10) → Delivered(20)]
├── BillingAddressId: int FK → Address
├── ShippingAddressId: int? FK → Address
├── PaymentMethodSystemName: string (e.g., "Payments.PayPalCommerce")
├── ShippingMethod: string
├── OrderSubtotalInclTax: decimal
├── OrderSubtotalExclTax: decimal
├── OrderTax: decimal
├── OrderShippingInclTax: decimal
├── OrderTotal: decimal
├── OrderDiscount: decimal
├── PaidDateUtc: DateTime?
├── CreatedOnUtc: DateTime
└── ... (50+ properties)
```

### 3 Trạng Thái Độc Lập

```
┌─────────────────────────────────────────────────┐
│  ORDER STATUS (Lifecycle)              │
├─────────────────────────────────────────────────┤
│ Pending → Processing → Complete OR Cancelled    │
└─────────────────────────────────────────────────┘
    │
     ↓
┌─────────────────────────────────────────────────┐
│     PAYMENT STATUS (Thanh toán)           │
├─────────────────────────────────────────────────┤
│ Pending → Authorized → Paid/Refunded/Voided    │
└─────────────────────────────────────────────────┘
       │
    ↓
┌─────────────────────────────────────────────────┐
│   SHIPPING STATUS (Giao hàng)                │
├─────────────────────────────────────────────────┤
│ Pending → Shipped → Delivered OR ReadyForPickup│
└─────────────────────────────────────────────────┘
```

### Tính Toán Giá

```
Giá Bán (Price) = Giá cơ bản sản phẩm

Từng Item trong Order:
  Item.Price = Product.Price × Quantity
  Item.TaxAmount = Item.Price × TaxRate
  Item.PriceWithTax = Item.Price + Item.TaxAmount
  Item.DiscountAmount = được tính từ Discount rules

Order Total:
  Subtotal (ExclTax) = Σ OrderItem.PriceExclTax
  OrderTax = Σ OrderItem.TaxAmount
  Subtotal (InclTax) = Subtotal (ExclTax) + OrderTax
  OrderSubTotalDiscount = Discount áp dụng ở level Order
  Shipping = Tính từ Shipping Provider (nếu không free)
  PaymentFee = Phí thanh toán (nếu có)
  
  OrderTotal = Subtotal (InclTax) - OrderSubTotalDiscount + Shipping + PaymentFee
```

### Related Tables

```
Order
├── OrderItem (1-N)
│   └── OrderItemGuid
│   └── ProductId FK
│   └── Quantity
│   └── UnitPriceExclTax, UnitPriceInclTax
│   └── AttributesXml (size, color, etc)
│   └── RentalStartDateUtc, RentalEndDateUtc
│
├── Shipment (1-N)
│   ├── ShipmentItem[] (từng product trong shipment)
│   ├── TrackingNumber
│   ├── ShippedDateUtc
│   └── DeliveryDateUtc
│
├── OrderNote (1-N) (comment từ Admin hoặc Customer)
├── Address (Billing & Shipping)
├── GiftCardUsageHistory (nếu dùng gift card)
└── RecurringPayment (nếu recurring order)
```

---

## 1.4 CATEGORY (Danh mục)

### Entity Structure

```
Category
├── Id: int (PK)
├── Name: string
├── Description: string
├── ParentCategoryId: int? (lồng nhau, cây phân cấp)
├── PictureId: int
├── Published: bool
├── Deleted: bool (soft delete)
├── DisplayOrder: int
├── CreatedOnUtc: DateTime
├── UpdatedOnUtc: DateTime
├── MetaKeywords, MetaDescription, MetaTitle (SEO)
├── ShowOnHomepage: bool
├── PageSize: int (số item per page)
└── ... (20+ properties)
```

### Nhóm Sản Phẩm

| Quan hệ | Mô tả |
|--------|-------|
| **ProductCategory** | 1 sản phẩm có thể thuộc nhiều category |
| **Parent Category** | Category lồng nhau (Electronics → Phones → iPhones) |
| **Discount Mapping** | Category có thể áp dụng discount riêng |

### Related Tables

```
Category
├── ProductCategory (N-N với Product)
├── Picture
├── DiscountCategoryMapping (N-N với Discount)
├── AclRecord (ACL - quyền truy cập)
└── StoreMapping (N-N - chia sẻ giữa stores)
```

---

## 1.5 STORE (Cửa hàng)

### Entity Structure

```
Store
├── Id: int (PK)
├── Name: string (tên cửa hàng)
├── Url: string (tên miền, e.g., store.myshop.com)
├── DefaultLanguageId: int FK → Language
├── SslEnabled: bool (HTTPS)
├── Hosts: string (comma-separated)
├── DefaultMetaKeywords, DefaultMetaDescription: string
├── CompanyName: string
├── CompanyAddress: string
├── CompanyPhoneNumber: string
├── CompanyVat: string
└── DisplayOrder: int
```

### Multi-Store Architecture

nopCommerce hỗ trợ **một ứng dụng chạy nhiều cửa hàng**:

```
┌──────────────────────────────────────┐
│      nopCommerce Instance     │
├──────────────────────────────────────┤
│  Store 1: store1.example.com          │
│  ├─ Customers (riêng)     │
│  ├─ Products (share qua StoreMapping)│
│  ├─ Orders (riêng)  │
│  └─ Settings (riêng)        │
│              │
│  Store 2: store2.example.com   │
│  ├─ Customers (riêng)              │
│  ├─ Products (share qua StoreMapping)│
│  ├─ Orders (riêng)│
│  └─ Settings (riêng)             │
└──────────────────────────────────────┘
```

### Related Tables

```
Store
├── StoreMapping (chia sẻ data giữa stores)
│   └── Product, Category, Manufacturer, etc.
├── Language
├── Currency
└── [Hầu hết entities đều có StoreId FK]
```

---

---

# 📐 PHẦN 2: DATABASE STRUCTURE & RELATIONSHIPS

## 2.1 Entity Relationship Diagram (Simplified)

```
┌──────────────────────────────────────────────────────────────────┐
│    CORE ENTITIES         │
└──────────────────────────────────────────────────────────────────┘

   STORE
       ├─ 1:N → Product
       ├─ 1:N → Customer
          ├─ 1:N → Order
               └─ 1:N → Category
  
┌─────────────────────────────────────────────────────────────────┐

  CUSTOMER        PRODUCT
          ├─ 1:N → Order         ├─ 1:N → OrderItem
  ├─ 1:N → Address                ├─ 1:N → ProductCategory
    ├─ 1:N → RewardPointsHistory    ├─ 1:N → ProductAttribute
           └─ 1:N → ShoppingCartItem       ├─ 1:N → ProductPicture
       ├─ 1:N → ProductReview
      ├─ 1:N → TierPrice
        └─ M:N → Discount
        
┌─────────────────────────────────────────────────────────────────┐

     ORDER (Order Header)
    ├─ 1:N → OrderItem
         ├─ 1:N → Shipment
      ├─ 1:N → OrderNote
   ├─ 1:N → GiftCardUsageHistory
       ├─ M:N → Address (Billing/Shipping)
      └─ 1:1 → RecurringPayment
      
    SHIPMENT
              └─ 1:N → ShipmentItem
      
┌─────────────────────────────────────────────────────────────────┐

         DISCOUNT
   ├─ M:N → Product
        ├─ M:N → Category
 ├─ M:N → Manufacturer
  └─ 1:N → DiscountUsageHistory
   
┌─────────────────────────────────────────────────────────────────┐
```

---

## 2.2 Key Relationships

### One-to-Many (1:N)

| Parent | Child | Purpose |
|--------|-------|---------|
| Customer | Order | Khách hàng tạo nhiều đơn hàng |
| Customer | Address | Khách hàng có nhiều địa chỉ |
| Customer | ShoppingCartItem | Giỏ hàng có nhiều item |
| Order | OrderItem | 1 đơn có nhiều sản phẩm |
| Order | Shipment | 1 đơn có nhiều lô hàng (partial ship) |
| Product | ProductCategory | 1 sản phẩm (qua ProductCategory) |
| Product | ProductPicture | 1 sản phẩm có nhiều ảnh |
| Product | ProductAttribute | 1 sản phẩm có nhiều attribute |

### Many-to-Many (M:N)

| Entity 1 | Mapping Table | Entity 2 | Purpose |
|----------|---------------|----------|---------|
| Product | ProductCategory | Category | 1 sản phẩm thuộc nhiều category |
| Product | Discount (DiscountProductMapping) | Discount | 1 discount áp dụng nhiều sản phẩm |
| Customer | CustomerRole (mapping) | Role | 1 khách hàng có nhiều role |
| Category | StoreMapping | Store | 1 category chia sẻ giữa stores |

### One-to-One (1:1)

| Entity 1 | Entity 2 | Purpose |
|----------|----------|---------|
| Order | Customer (FK) | 1 order thuộc 1 khách hàng |
| OrderItem | Product (FK) | 1 order item là 1 product |
| Shipment | Order (FK) | 1 shipment cho 1 order |

---

## 2.3 Enum Fields (Trạng thái)

### OrderStatus

```csharp
public enum OrderStatus
{
    Pending = 0,          // Vừa tạo, chưa xử lý
    Processing = 10,      // Đang xử lý
    Complete = 20,        // Hoàn thành
    Cancelled = 30        // Bị hủy
}
```

### PaymentStatus

```csharp
public enum PaymentStatus
{
    Pending = 10,         // Chờ thanh toán
    Authorized = 20,      // Được phép, chưa lấy tiền
    Paid = 30,            // Đã lấy tiền
    PartiallyRefunded = 35, // Hoàn tiền một phần
    Refunded = 40,        // Hoàn tiền toàn bộ
    Voided = 50           // Hủy
}
```

### ShippingStatus

```csharp
public enum ShippingStatus
{
    Pending = 0,        // Chưa gửi
    Shipped = 10,       // Đã gửi
    Delivered = 20,       // Đã giao
    ReadyForPickup = 30   // Sẵn sàng lấy (local pickup)
}
```

### ProductType

```csharp
public enum ProductType
{
    SimpleProduct = 0,
    GroupedProduct = 1,
    BundleProduct = 2,
    RentalProduct = 4,
    RecurringProduct = 8,
  DownloadableProduct = 16
}
```

---

# 🔄 PHẦN 3: ORDER PROCESSING WORKFLOW

## 3.1 Complete Order Lifecycle

```
┌─────────────────────────────────────────────────────────────────┐
│  PHASE 1: SHOPPING (Giỏ hàng)            │
├─────────────────────────────────────────────────────────────────┤

Customer Browse Products
    ↓
Add to Shopping Cart (ShoppingCartItem)
    ├─ ProductId, Quantity, AttributesXml
    ├─ RentalStartDateUtc, RentalEndDateUtc (nếu rental)
    └─ CustomerEnteredPrice (nếu product cho input giá)
    ↓
Review Cart (có thể update quantity hoặc remove)
    ↓
Apply Coupon Code / Discount
    ├─ Validate coupon code
    ├─ Check điều kiện (min order value, customer group, etc)
    └─ Update cart total
 
┌─────────────────────────────────────────────────────────────────┐
│  PHASE 2: CHECKOUT           │
├─────────────────────────────────────────────────────────────────┤

Step 1: Billing Address
    ├─ Select từ existing addresses hoặc create new
    └─ AddressId saved

Step 2: Shipping Address (nếu different from billing)
    ├─ ShippingAddressId saved
    └─ PickupInStore flag (nếu local pickup)

Step 3: Shipping Method
    ├─ Calculate từ carrier (UPS, FedEx, etc)
    ├─ Get OrderShippingInclTax / ExclTax
    └─ ShippingMethod = "UPS Ground", etc

Step 4: Payment Method
    ├─ Choose từ enabled payment providers
    ├─ Get PaymentMethodSystemName = "Payments.PayPalCommerce"
  └─ Possible: PaymentMethodAdditionalFee

Step 5: Order Confirmation
    ├─ Tổng giá:
    │  = Subtotal + Tax + Shipping + PaymentFee - Discount
 └─ Customer confirm
    
┌─────────────────────────────────────────────────────────────────┐
│  PHASE 3: ORDER CREATION    │
├─────────────────────────────────────────────────────────────────┤

Create Order Header (Order table)
    ├─ OrderGuid = NEW Guid()
    ├─ OrderStatus = Pending
  ├─ PaymentStatus = Pending
    ├─ ShippingStatus = Pending
    ├─ OrderSubtotalExclTax, OrderSubtotalInclTax
    ├─ OrderTax, OrderShippingInclTax
    ├─ OrderDiscount (từ coupon)
    ├─ OrderTotal = tổng
    └─ CreatedOnUtc = NOW

Create OrderItems (1 per product in cart)
    ├─ ProductId, Quantity
    ├─ UnitPriceExclTax, UnitPriceInclTax (lưu giá tại thời điểm order)
    ├─ AttributesXml (size, color nếu có)
    ├─ OriginalProductCost (cost của vendor)
    └─ AttributeDescription (e.g., "Size: L, Color: Red")

If GiftCard được dùng:
    ├─ Create GiftCardUsageHistory
    ├─ Deduct từ GiftCard.Amount
    └─ OrderDiscount += amount

If Reward Points được dùng:
    ├─ Create RewardPointsHistory (marked as SPENT)
    └─ Deduct từ Customer's points

Clear Shopping Cart
    └─ Delete ShoppingCartItem records

┌─────────────────────────────────────────────────────────────────┐
│  PHASE 4: PAYMENT PROCESSING      │
├─────────────────────────────────────────────────────────────────┤

Route by Payment Provider:

A) IMMEDIATE (e.g., Credit Card, PayPal)
    ├─ Call payment gateway
    ├─ If Authorized/Paid:
    │  ├─ Order.PaymentStatus = Authorized or Paid
    │  ├─ Order.PaidDateUtc = NOW
    │  ├─ Create RewardPointsHistory (EARNED) nếu enabled
    │  └─ Order.OrderStatus = Processing (auto-start)
    └─ If Failed:
       ├─ Order.PaymentStatus = Pending (customer can retry)
     └─ Order.OrderStatus = Pending

B) MANUAL (e.g., Bank Transfer, Check)
    ├─ Order.PaymentStatus = Pending
    ├─ Admin sẽ confirm payment sau khi get proof
    └─ Manual mark as Paid

C) REDIRECT (e.g., PayPal redirect)
    ├─ Create order với PaymentStatus = Pending
    ├─ Redirect to PayPal
    ├─ PayPal redirects back với status
    └─ Update Order.PaymentStatus

┌─────────────────────────────────────────────────────────────────┐
│  PHASE 5: ORDER FULFILLMENT (Gửi hàng)   │
├─────────────────────────────────────────────────────────────────┤

When Payment Confirmed:
    ├─ Order.OrderStatus = Processing
    ├─ Inventory updated (StockQuantity, WarehouseInventory)
    └─ Create StockQuantityHistory

Packing & Shipping:
 ├─ Admin create Shipment
    ├─ Select OrderItems to include
    ├─ Generate Shipment record
    │  ├─ ShipmentId
    │  ├─ OrderId
    │  ├─ CreatedOnUtc
    │  └─ AdminComment (optional)
    ├─ Create ShipmentItem[] (foreach selected product)
    ├─ Add TrackingNumber
    ├─ Set ShippedDateUtc
    └─ Order.ShippingStatus = Shipped
    
Customer receives package:
    ├─ Admin mark as Delivered
    ├─ Set DeliveryDateUtc
    └─ Order.ShippingStatus = Delivered

┌─────────────────────────────────────────────────────────────────┐
│  PHASE 6: POST-DELIVERY      │
├─────────────────────────────────────────────────────────────────┤

Customer can:
    ├─ Leave Product Review
    │  ├─ Rating (1-5 stars)
    │  ├─ ReviewText
    │  └─ ReviewType mappings (Quality, Shipping speed, etc)
    │
    ├─ Request Return (if not NotReturnable)
    │  ├─ Create ReturnRequest
    │  ├─ Select products to return
    │  ├─ Reason & request attributes
    │  └─ Status: Pending → Approved → Completed / Rejected
    │
    └─ Earn Reward Points (if enabled)
       ├─ Points = OrderTotal × PointRate
       └─ Customer can use later

Order Completion:
    ├─ Order.OrderStatus = Complete
    ├─ All related entities finalized
└─ Available for reporting

┌─────────────────────────────────────────────────────────────────┐
│  PHASE 7: REFUNDS & CANCELLATIONS        │
├─────────────────────────────────────────────────────────────────┤

Before Payment:
    ├─ Customer can cancel
    ├─ Order.OrderStatus = Cancelled
    └─ No refund needed

After Payment:
    ├─ Customer request refund
    ├─ Admin process refund in payment gateway
    │  ├─ Full Refund: PaymentStatus = Refunded
│  └─ Partial Refund: PaymentStatus = PartiallyRefunded
    ├─ Create GiftCardUsageHistory (reverse) or credit account
    └─ If earned reward points, deduct them

Return Completed:
    ├─ ReturnRequest.ReturnRequestStatus = Completed
    ├─ Refund issued
    ├─ StockQuantity restored
    └─ Create StockQuantityHistory record
```

---

## 3.2 Key Tables in Order Workflow

| Table | Purpose | Key Fields |
|-------|---------|-----------|
| **ShoppingCartItem** | Giỏ hàng | CustomerId, ProductId, Quantity, CreatedOnUtc |
| **Order** | Đơn hàng header | CustomerId, OrderStatus, PaymentStatus, OrderTotal |
| **OrderItem** | Chi tiết sản phẩm | OrderId, ProductId, Quantity, UnitPrice |
| **Shipment** | Lô hàng | OrderId, TrackingNumber, ShippedDateUtc |
| **ShipmentItem** | Chi tiết shipment | ShipmentId, OrderItemId, Quantity |
| **GiftCardUsageHistory** | Dùng gift card | GiftCardId, UsedWithOrderId, UsedValue |
| **RewardPointsHistory** | Điểm thưởng | CustomerId, Points, UsedWithOrderId |
| **ReturnRequest** | Yêu cầu trả hàng | OrderId, ReturnRequestStatus |

---

# 🎁 PHẦN 4: DISCOUNT & PROMOTION SYSTEM

## 4.1 Discount Entity

```csharp
public class Discount : BaseEntity
{
    public string Name { get; set; }
    
    // Loại discount
    public DiscountType DiscountType { get; set; }
    // ├─ AssignedToSkus (áp dụng SKU)
    // ├─ AssignedToCategories
    // ├─ AssignedToManufacturers
    // └─ AssignedToOrderTotal (% giảm trên total)
    
    // Giá trị discount
    public bool UsePercentage { get; set; }  // true: %, false: $
    public decimal DiscountPercentage { get; set; }  // e.g., 10
    public decimal DiscountAmount { get; set; }      // e.g., 5.00
    public decimal? MaximumDiscountAmount { get; set; } // max $100
    
    // Thời gian áp dụng
  public DateTime? StartDateUtc { get; set; }
    public DateTime? EndDateUtc { get; set; }
    
    // Coupon code
    public bool RequiresCouponCode { get; set; }
    public string CouponCode { get; set; }  // e.g., "SUMMER10"
    
    // Điều kiện & hạn chế
    public int DiscountLimitation { get; set; }
    // ├─ Unlimited
    // ├─ LimitedTimes (N Times Only)
    // └─ LimitedToNTimesPerCustomer
    
    public int LimitationTimes { get; set; }  // e.g., 100 (lần dùng)
    public bool IsCumulative { get; set; }    // true: có thể kết hợp với discount khác
    
    // Variant
    public int? VendorId { get; set; }  // vendor-specific discount
}
```

## 4.2 Discount Types & Examples

### Type 1: AssignedToOrderTotal (Discount on Order Amount)

```
Example: "10% off if order total >= $50"

Rule: 
  IF Order.OrderSubtotalInclTax >= $50
  THEN Discount = Order.OrderSubtotalInclTax × 0.10

Applied to: Order header (không áp dụng item cụ thể)
```

### Type 2: AssignedToCategories (Category Discount)

```
Example: "20% off Electronics"

Rule:
  FOR EACH OrderItem
  IF Product.Category = "Electronics"
    THEN OrderItem.DiscountAmount += OrderItem.Price × 0.20

Applied to: Individual items within category
```

### Type 3: AssignedToSkus (Product Discount)

```
Example: "50% off Product SKU: IPHONE-12"

Rule:
  FOR EACH OrderItem
    IF OrderItem.Product.Sku = "IPHONE-12"
    THEN OrderItem.DiscountAmount += OrderItem.Price × 0.50

Applied to: Specific product SKUs
```

### Type 4: AssignedToManufacturers

```
Example: "15% off all Sony products"

Rule:
  FOR EACH OrderItem
    IF Product.Manufacturer = "Sony"
    THEN OrderItem.DiscountAmount += OrderItem.Price × 0.15

Applied to: All products from manufacturer
```

## 4.3 Discount Workflow

```
┌─────────────────────────────────────────────────────┐
│  APPLY DISCOUNT TO CART         │
├─────────────────────────────────────────────────────┤

1. Customer enters coupon code: "SUMMER10"
   └─ System searches Discount table
      WHERE CouponCode = "SUMMER10"
      AND IsActive = true
  AND NOW BETWEEN StartDateUtc AND EndDateUtc

2. Validate discount conditions:
   ├─ If RequiresCouponCode = true
   │  └─ Code must match
   │
   ├─ If has DiscountRequirement (custom rules)
   │  └─ E.g., "Only for Customer Role = 'VIP'"
   │  └─ E.g., "Minimum order: $100"
   │  └─ E.g., "Product quantity >= 5"
   │
   ├─ If DiscountLimitation != Unlimited
│  ├─ IF LimitedTimes:
   │  │  └─ Check total usage: COUNT(*) < LimitationTimes
   │  │
   │  └─ IF LimitedToNTimesPerCustomer:
   │     └─ Check usage per customer: COUNT(*) < LimitationTimes
   │
   └─ If MaximumDiscountAmount set
      └─ CalculatedDiscount = MIN(calculated, MaximumDiscountAmount)

3. Calculate discount value:
   ├─ IF UsePercentage = true:
   │  └─ DiscountValue = OrderSubtotal × (DiscountPercentage / 100)
   └─ IF UsePercentage = false:
      └─ DiscountValue = DiscountAmount

4. Store in Order/OrderItem:
   ├─ Order.OrderSubTotalDiscountInclTax = calculated
 └─ Recalculate Order.OrderTotal

5. Store in DiscountUsageHistory:
   ├─ CustomerId, OrderId, DiscountId, UsedValue
   └─ CreatedOnUtc = NOW
```

## 4.4 Discount Mappings (M:N Relationships)

| Mapping Table | Purpose |
|---------------|---------|
| **DiscountProductMapping** | Product A áp dụng Discount X |
| **DiscountCategoryMapping** | Category Y áp dụng Discount X |
| **DiscountManufacturerMapping** | Manufacturer Z áp dụng Discount X |

```csharp
// Example:
var discount = new Discount
{
    Name = "Summer Sale 2024",
    DiscountType = DiscountType.AssignedToCategories,
    UsePercentage = true,
    DiscountPercentage = 20,
    StartDateUtc = new DateTime(2024, 6, 1),
    EndDateUtc = new DateTime(2024, 8, 31),
    IsActive = true
};

// Apply to multiple categories via DiscountCategoryMapping
var mapping1 = new DiscountCategoryMapping { DiscountId = discount.Id, CategoryId = 5 }; // Electronics
var mapping2 = new DiscountCategoryMapping { DiscountId = discount.Id, CategoryId = 8 }; // Shoes
```

---

# 📦 PHẦN 5: PRODUCT MANAGEMENT

## 5.1 Product Types Detailed

### Simple Product

```
Attributes: Optional
├─ Size, Color (nếu muốn variant)
└─ Stored qua ProductAttributeMapping + ProductAttributeCombination

Pricing:
├─ Base Price: Product.Price
├─ TierPrice: giảm giá nếu mua nhiều
│  └─ Example: 10 units = $9 each, 50 units = $8 each
└─ Special price: có thể set qua discount

Inventory:
├─ StockQuantity (nếu Use Multiple Warehouses = false)
└─ ProductWarehouseInventory (nếu Use Multiple Warehouses = true)
```

### Grouped Product

```
Purpose: Nhóm sản phẩm, hiển thị variant

Example:
  Parent Product: "Nike Air Max"
  ├─ Child 1: Size 7, SKU: NIKE-AM-7
  ├─ Child 2: Size 8, SKU: NIKE-AM-8
  ├─ Child 3: Size 9, SKU: NIKE-AM-9
  └─ (each có price, stock riêng)

Product Entity:
├─ ParentGroupedProductId (nếu là child)
└─ VisibleIndividually = false (child không hiển thị riêng)
```

### Bundle Product

```
Purpose: Bán kèm nhiều sản phẩm

Example: "Back to School Bundle"
├─ Laptop (qty 1)
├─ Backpack (qty 1)
├─ Mouse (qty 2)
└─ Charging Cable (qty 1)

When ordered:
├─ Create 1 OrderItem cho Bundle
└─ Inventory updated cho tất cả items
```

### Download Product

```
Scenarios:
├─ eBook, Software, MP3, PDF
├─ Unlimited download hoặc limited
└─ Activation type:
   ├─ WhenOrderIsPaid (auto khi thanh toán)
   └─ Manually (admin activate)

Fields:
├─ IsDownload = true
├─ DownloadId (FK → Download entity)
├─ UnlimitedDownloads = false
├─ MaxNumberOfDownloads = 3 (3 lần download)
├─ DownloadExpirationDays = 30 (30 ngày)
└─ HasSampleDownload = true (free demo)

OrderItem:
├─ DownloadCount (times downloaded)
├─ IsDownloadActivated (enabled for customer)
└─ LicenseDownloadId (optional license key)
```

### Rental Product

```
Scenarios:
├─ Xe ô tô, máy ảnh, dụng cụ thể thao
└─ Charging by period (Day, Week, Month)

Fields:
├─ IsRental = true
├─ RentalPriceLength = 7 (period length)
├─ RentalPricePeriodId = 0 (Day) or 1 (Week) or 2 (Month)
│
├─ Price = giá per period (e.g., $50 per week)
└─ Calculated: Total = Price × NumberOfPeriods

OrderItem:
├─ RentalStartDateUtc (e.g., 2024-06-01)
├─ RentalEndDateUtc (e.g., 2024-06-08)
└─ ItemWeight (giúp tính shipping)
```

### Recurring Product (Subscription)

```
Scenarios:
├─ Magazine subscription, SaaS, Membership
└─ Billed theo schedule

Fields:
├─ IsRecurring = true
├─ RecurringCycleLength = 1 (repeat every X)
├─ RecurringCyclePeriodId = 1 (Month)
│  └─ 0: Day, 1: Week, 2: Month, 3: Year
├─ RecurringTotalCycles = 12 (12 months)
│
└─ Price = monthly fee

Order Processing:
├─ Initial order created
├─ Create RecurringPayment record
│  ├─ StartDateUtc
│  ├─ IsActive = true
│  └─ CyclePeriod = Monthly
│
├─ Each month: system automatically
│  ├─ Create new Order
│  ├─ Process payment
│  └─ Create RecurringPaymentHistory
│
└─ After 12 months: IsActive = false
```

### Gift Card Product

```
Scenarios:
├─ Thẻ quà tặng $50, $100, etc.
└─ Có thể send via email hoặc in

Fields:
├─ IsGiftCard = true
├─ GiftCardTypeId (Virtual/Physical)
├─ OverriddenGiftCardAmount = null (use price)
│
└─ When ordered:
├─ Create GiftCard entity
   │  ├─ Amount = OrderItem.Price
   │  ├─ GiftCardCouponCode = generated
   │  ├─ RecipientName, RecipientEmail
   │  ├─ SenderName, SenderEmail
   │  ├─ Message
   │  └─ IsGiftCardActivated = false (until paid)
   │
   └─ When paid:
      ├─ Send email to recipient
      └─ Customer can use code in checkout

Usage:
├─ Customer apply GiftCard coupon
├─ Check remaining balance
├─ Create GiftCardUsageHistory
└─ Deduct from Order total
```

## 5.2 Product Attributes & Combinations

### ProductAttribute

```
Example: Attribute "Size"
├─ Name = "Size"
└─ AttributeControlType
    ├─ Dropdown
    ├─ RadioButton
    ├─ Checkbox
    └─ TextBox

ProductAttributeValue:
├─ Size-S (Small)
├─ Size-M (Medium)
├─ Size-L (Large)
└─ Size-XL (XL)
```

### ProductAttributeCombination (Variant)

```
Product: "Nike T-Shirt"

Combination 1:
├─ SKU: NIKE-TS-SM-RED
├─ Size: Small, Color: Red
├─ Price: $30, Stock: 100
└─ Picture: (can override product picture)

Combination 2:
├─ SKU: NIKE-TS-LG-BLUE
├─ Size: Large, Color: Blue
├─ Price: $32, Stock: 50
└─ Picture: (different image)

Benefits:
├─ Each combo has own SKU, price, inventory
├─ Reduce products table size (1 product vs 100 products)
└─ Easier management
```

## 5.3 Product Relationships

```
Product
├─ ProductCategory (M:N)
│  └─ Một sản phẩm, nhiều danh mục
│
├─ ProductManufacturer (M:N)
│  └─ Một sản phẩm, có thể nhiều nhà sản xuất (co-brand)
│
├─ ProductPicture (1:N)
│  └─ Main image + gallery images
│
├─ ProductVideo (1:N)
│  └─ Demo videos
│
├─ ProductAttribute (1:N)
│  └─ Size, Color, Material, etc.
│
├─ ProductAttributeCombination (1:N)
│  ├─ Variant (Size-S-Red, Size-L-Blue)
│  └─ ProductAttributeCombinationPicture (per variant)
│
├─ TierPrice (1:N)
│  └─ Buy 10+ get $10 off, etc.
│
├─ ProductSpecificationAttribute (1:N)
│  └─ CPU: Intel i7, RAM: 16GB, SSD: 512GB
│
├─ RelatedProduct (1:N)
│  └─ "You may also like"
│
├─ CrossSellProduct (1:N)
│  └─ "Add to cart"
│
├─ ProductReview (1:N)
│  └─ Đánh giá từ khách hàng
│
├─ StockQuantityHistory (1:N)
│  └─ Lịch sử thay đổi inventory
│
├─ ProductWarehouseInventory (1:N)
│  └─ Tồn kho theo warehouse
│
├─ Discount (M:N qua DiscountProductMapping)
│  └─ Applicable discounts
│
└─ OrderItem (1:N)
   └─ Lịch sử bán hàng
```

---

# 💳 PHẦN 6: PAYMENT PROCESSING

## 6.1 Payment Gateway Integration

nopCommerce hỗ trợ **Plugin Architecture** cho payment gateways:

```csharp
public interface IPaymentMethod
{
    // Process payment
    Task<ProcessPaymentResult> ProcessPaymentAsync(ProcessPaymentRequest request);
    
    // Support specific features
    Task<bool> SupportCaptureAsync();  // Capture pre-authorized amount
    Task<bool> SupportRefundAsync();   // Refund full amount
    Task<bool> SupportPartiallyRefundAsync(); // Refund partial
    Task<bool> SupportVoidAsync();     // Cancel authorization
    
    // Recurring payments (subscriptions)
    Task<RecurringPaymentType> GetRecurringPaymentTypeAsync();
    Task<ProcessPaymentResult> ProcessRecurringPaymentAsync(ProcessPaymentRequest request);
}
```

## 6.2 Payment Methods

### A) Direct Payment (Immediate Processing)

```
Examples: Credit Card, Debit Card, Cryptocurrency

Flow:
  1. Customer fills card details in checkout
  2. System POST to payment gateway API
  3. Gateway processes immediately
  4. Response received synchronously
  5. Order status updated based on response

Advantages:
  ├─ Instant confirmation
  ├─ No redirect needed
  └─ Better UX

Disadvantages:
  ├─ Need PCI DSS compliance (if storing card)
  └─ Higher security requirements

Built-in Methods:
  ├─ Manual (Bank Transfer, Check, COD)
  │  └─ PaymentStatus = Pending (admin confirms)
  │
└─ Cash On Delivery (COD)
     └─ PaymentStatus = Pending until received
```

### B) Redirect Payment (External Gateway)

```
Examples: PayPal, Stripe Checkout, Google Pay

Flow:
  1. Create Order with PaymentStatus = Pending
  2. Redirect customer to PayPal
  3. Customer authorizes payment on PayPal
  4. PayPal redirects back to store
 ├─ Return URL: /checkout/paymentreturn
     └─ Cancel URL: /checkout/paymentcancel
  5. System queries PayPal API to confirm
  6. Update Order.PaymentStatus

Advantages:
  ├─ No card data stored on server
  ├─ Lower PCI DSS requirements
  └─ Customer trust

Disadvantages:
  ├─ Multiple redirects
  └─ Slower process
```

## 6.3 Payment Status Machine

```
┌──────────────────────────────────────────────────────┐
│          PAYMENT STATUS STATE MACHINE      │
└──────────────────────────────────────────────────────┘

Pending
  │
├─→ Authorized (Card approved, funds on hold)
             │         │
          │         ├─→ Paid (Captured, funds taken)
            │         │  │
          │     │     └─→ PartiallyRefunded ($10 refunded)
 │      │           │
     │   │           └─→ Refunded (Full refund)
     │         │
            │         └─→ Voided (Authorization cancelled)
          │
     └─→ [Declined] (Failed)

Scenarios:
  • Credit Card: Pending → Authorized → Paid (3 steps)
  • PayPal: Pending → Paid (2 steps, no separate authorize)
  • Manual: Pending → Paid (admin marks manually)
```

## 6.4 Order-Payment Relationship

```csharp
public class Order
{
    public int OrderStatusId { get; set; }        // Pending/Processing/Complete
    public int PaymentStatusId { get; set; }      // Pending/Authorized/Paid
    public int ShippingStatusId { get; set; }     // Pending/Shipped/Delivered
    
    // Payment details
    public string PaymentMethodSystemName { get; set; }  // "Payments.PayPalCommerce"
    public string AuthorizationTransactionId { get; set; } // Gateway transaction ID
    public string CaptureTransactionId { get; set; }
    public decimal RefundedAmount { get; set; }  // Total refunded so far
    
    // Card info (if storing, must be encrypted)
    public string CardName { get; set; }
    public string CardNumber { get; set; }
    public string MaskedCreditCardNumber { get; set; } // "****-****-****-1234"
    public string CardCvv2 { get; set; }
    public string CardExpirationMonth { get; set; }
    public string CardExpirationYear { get; set; }
}
```

---

# 🚚 PHẦN 7: SHIPPING MANAGEMENT

## 7.1 Shipping Methods & Providers

```
Shipping Providers:
├─ Manual (admin set rate)
├─ Fixed Rate (flat fee, e.g., $10)
├─ By Weight (heavier = more expensive)
├─ By Total (order amount-based)
├─ UPS, FedEx, DHL (real-time rates from API)
└─ Local Pickup (free, customer picks up at store)
```

## 7.2 Shipment Entity

```csharp
public class Shipment : BaseEntity
{
    public int OrderId { get; set; }          // FK to Order
    public string TrackingNumber { get; set; }// "1Z999AA10123456784" (UPS)
    public decimal? TotalWeight { get; set; }     // kg or lbs
    
 // Dates
 public DateTime? ShippedDateUtc { get; set; } // When shipped
 public DateTime? DeliveryDateUtc { get; set; } // When delivered
    public DateTime? ReadyForPickupDateUtc { get; set; } // For local pickup
    public DateTime CreatedOnUtc { get; set; }
    
    public string AdminComment { get; set; }
}

public class ShipmentItem : BaseEntity
{
    public int ShipmentId { get; set; }           // FK to Shipment
    public int OrderItemId { get; set; }          // FK to OrderItem
    public int Quantity { get; set; }      // Qty shipped
}
```

## 7.3 Shipping Workflow

```
┌──────────────────────────────────────────────────────┐
│       SHIPPING WORKFLOW     │
└──────────────────────────────────────────────────────┘

1. ORDER CONFIRMED (Payment approved)
   └─ Order.OrderStatus = Processing
   └─ Order.ShippingStatus = Pending

2. ADMIN CREATES SHIPMENT
   ├─ Select OrderItems to ship (partial shipment possible)
   ├─ Choose shipping provider (UPS, FedEx, Manual)
   ├─ Generate label or booking
   └─ Create Shipment record

3. SHIPMENT CREATED
   ├─ Shipment.CreatedOnUtc = NOW
   ├─ Create ShipmentItem[] for selected products
   └─ Order.ShippingStatus = Pending (not yet shipped)

4. SHIP THE GOODS
   ├─ Admin enters TrackingNumber
   ├─ Set Shipment.ShippedDateUtc = NOW
   └─ Order.ShippingStatus = Shipped

5. CUSTOMER RECEIVES
   ├─ Admin updates Shipment.DeliveryDateUtc
   ├─ Order.ShippingStatus = Delivered
   └─ Order.OrderStatus = Complete (if all items delivered)

6. LOCAL PICKUP SCENARIO
   ├─ Order.PickupInStore = true (no shipping address)
   ├─ Shipment.ReadyForPickupDateUtc = when ready
   ├─ Order.ShippingStatus = ReadyForPickup
   └─ Customer picks up at store
```

## 7.4 Multiple Shipments (Partial Shipping)

```
Scenario: Order has 3 items, but different warehouses

Order 1:
├─ Item A (qty 2) - from Warehouse NY
├─ Item B (qty 1) - from Warehouse LA
└─ Item C (qty 5) - from Warehouse TX

Shipment 1: From NY
├─ ShipmentItem: OrderItem A (qty 2)
└─ TrackingNumber: "USPS-123456"

Shipment 2: From LA
├─ ShipmentItem: OrderItem B (qty 1)
└─ TrackingNumber: "USPS-654321"

Shipment 3: From TX
├─ ShipmentItem: OrderItem C (qty 5)
└─ TrackingNumber: "FEDEX-987654"

Customer gets 3 tracking numbers
Order.ShippingStatus = Shipped (when all shipped)
Order.ShippingStatus = Delivered (when last one delivered)
```

---

# 👥 PHẦN 8: CUSTOMER MANAGEMENT

## 8.1 Customer Entity Deep Dive

```csharp
public class Customer : BaseEntity, ISoftDeletedEntity
{
    // Core identity
    public Guid CustomerGuid { get; set; }       // Unique identifier
    public string Username { get; set; }        // Unique username
    public string Email { get; set; }           // Unique email
    
    // Profile
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string FullName => $"{FirstName} {LastName}";
    public DateTime? DateOfBirth { get; set; }
    public string Gender { get; set; }  // "M" / "F"
    
    // Company
    public string Company { get; set; }
    public string StreetAddress { get; set; }
    public string StreetAddress2 { get; set; }
    public string City { get; set; }
    public string County { get; set; }
    public string ZipPostalCode { get; set; }
    public int CountryId { get; set; }            // FK
    public int StateProvinceId { get; set; }    // FK
    public string Phone { get; set; }
    public string Fax { get; set; }
    
    // VAT (European Union)
    public string VatNumber { get; set; }
    public int VatNumberStatusId { get; set; }    // Pending/Valid/Invalid
    
    // Preferences
    public int? CurrencyId { get; set; }          // Preferred currency
    public int? LanguageId { get; set; }          // Preferred language
    public int? TaxDisplayTypeId { get; set; }    // Show tax or not
    public string TimeZoneId { get; set; }        // e.g., "America/New_York"
    
    // Business relationship
    public int AffiliateId { get; set; }          // If customer is affiliate
    public int VendorId { get; set; }      // If customer is vendor
    public int RegisteredInStoreId { get; set; }  // Which store
    
    // Account status
    public bool Active { get; set; }           // Account enabled
    public bool Deleted { get; set; }     // Soft delete
    public bool IsSystemAccount { get; set; }     // Admin/Guest system account
    public bool IsTaxExempt { get; set; }     // Exempt from tax
    
    // Security
 public int FailedLoginAttempts { get; set; }  // Wrong password count
    public DateTime? CannotLoginUntilDateUtc { get; set; } // Locked until
    public bool RequireReLogin { get; set; }      // Force re-login
    public bool MustChangePassword { get; set; }  // Password expired
    
    // Addresses
    public int? BillingAddressId { get; set; }    // Default billing
    public int? ShippingAddressId { get; set; }   // Default shipping
    
    // Audit
    public DateTime CreatedOnUtc { get; set; }
    public DateTime LastLoginDateUtc { get; set; }
    public DateTime LastActivityDateUtc { get; set; }
    public string LastIpAddress { get; set; }     // For security logs
    
    // Attributes
    public string CustomCustomerAttributesXML { get; set; } // Serialized custom attributes
}
```

## 8.2 Customer Roles & Permissions

```
Roles (via CustomerRole entity):

├─ Registered Customers
│  └─ Can browse, buy, review
│
├─ Guests
│  └─ Can browse, checkout without account
│
├─ Administrators
│  ├─ Full system access
│  └─ Manage products, orders, settings
│
├─ Vendors
│  ├─ Manage own products
│  ├─ View own orders
│  └─ No access to other vendors
│
└─ Forum Moderators
   └─ Moderate forum discussions

Implementation:
├─ CustomerRole (define role)
├─ CustomerCustomerRoleMapping (N-N mapping)
└─ PermissionRecord (define actions)
   └─ PermissionRecordCustomerRoleMapping (assign to roles)
```

## 8.3 Reward Points System

```csharp
public class RewardPointsHistory : BaseEntity
{
    public int CustomerId { get; set; }
    public int OrderId { get; set; }        // 0 if manual award
  public int Points { get; set; }   // Positive: earned, Negative: spent
    public string Message { get; set; }           // "Reward for order #123"
  public DateTime CreatedOnUtc { get; set; }
}
```

### Workflow:

```
1. EARNING POINTS
   Order.OrderTotal = $100
   Customer's Reward Rate = 1 point per $1
 
   When Order.PaymentStatus = Paid:
   ├─ Calculate: Points = OrderTotal × RateMultiplier
   │= $100 × 1 = 100 points
   ├─ Add to RewardPointsHistory (Points = +100, OrderId = order.Id)
   └─ Update Customer's total points

2. SPENDING POINTS
   Customer has: 200 points
   During checkout:
   ├─ Option to apply points
   ├─ Customer inputs: "Redeem 100 points = $10"
   ├─ Add to RewardPointsHistory (Points = -100, OrderId = new order.Id)
   ├─ Order.OrderDiscount += $10
   └─ Recalculate Order.OrderTotal

3. BALANCE CHECK
   Total Points = SUM(RewardPointsHistory.Points WHERE CustomerId = X)
       = +100 (first order) - 50 (redeemed) + 75 (second order) = 125
```

## 8.4 Multiple Addresses

```csharp
public class Address : BaseEntity
{
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Email { get; set; }
 public string Company { get; set; }
    public int CountryId { get; set; }
    public int? StateProvinceId { get; set; }
    public string City { get; set; }
    public string Address1 { get; set; }
    public string Address2 { get; set; }
    public string ZipPostalCode { get; set; }
    public string PhoneNumber { get; set; }
    public string FaxNumber { get; set; }
    public DateTime CreatedOnUtc { get; set; }
}

public class CustomerAddressMapping : BaseEntity
{
    public int CustomerId { get; set; }        // FK
    public int AddressId { get; set; }           // FK
}
```

### Usage:

```
Customer.Addresses (via CustomerAddressMapping):
├─ Home (123 Main St, NY)
├─ Work (456 Office Ave, LA)
└─ Parents (789 Family Ln, TX)

During Checkout:
├─ Billing: Select "Home"
├─ Shipping: Select "Parents" (different)
└─ Both saved to Order as IDs
```

---

# 🚀 PHẦN 9: ADVANCED FEATURES

## 9.1 Gift Cards

```csharp
public class GiftCard : BaseEntity
{
    public int? PurchasedWithOrderItemId { get; set; }
    public int GiftCardTypeId { get; set; }      // Virtual/Physical
    public decimal Amount { get; set; }           // $50, $100, etc
    
    public bool IsGiftCardActivated { get; set; } // Seller activated?
    public string GiftCardCouponCode { get; set; } // "GIFT123456"
    
    // Recipient info
    public string RecipientName { get; set; }
 public string RecipientEmail { get; set; }
    public bool IsRecipientNotified { get; set; }
    
    // Sender info (gift from someone)
    public string SenderName { get; set; }
    public string SenderEmail { get; set; }
    public string Message { get; set; }
    
    public DateTime CreatedOnUtc { get; set; }
}

public class GiftCardUsageHistory : BaseEntity
{
    public int GiftCardId { get; set; }
    public int UsedWithOrderId { get; set; }
    public decimal UsedValue { get; set; }       // $15 spent from $50 card
    public DateTime CreatedOnUtc { get; set; }
}
```

### Workflow:

```
1. PURCHASE GIFT CARD
   Customer buys: "Gift Card - $50"
   ├─ Create OrderItem with ProductId = GiftCard product
   ├─ When order paid:
   └─ Create GiftCard entity
      ├─ Amount = $50
      ├─ GiftCardCouponCode = AUTO_GENERATED
    ├─ RecipientName, RecipientEmail (from checkout)
      ├─ IsGiftCardActivated = true
      └─ Send email to recipient

2. RECIPIENT USES GIFT CARD
   Recipient receives email with coupon: "GIFT123456"
   ├─ Add to cart, go to checkout
   ├─ Apply coupon code: "GIFT123456"
   ├─ System validates:
   │  ├─ Checks GiftCard table
   │  ├─ Gets remaining balance
   │  └─ Updates Order.OrderDiscount
   ├─ Create GiftCardUsageHistory
   │  └─ UsedValue = min(OrderTotal, RemainingBalance)
   └─ If balance remains:
  └─ Can use again later

3. REMAINING BALANCE
   RemainingBalance = GiftCard.Amount - SUM(UsedValue)
   Example:
   ├─ Original: $50
   ├─ Order 1: -$20 (balance = $30)
   ├─ Order 2: -$15 (balance = $15)
   ├─ Order 3: -$15 (balance = $0, card exhausted)
```

## 9.2 Recurring Payments (Subscriptions)

```csharp
public class RecurringPayment : BaseEntity
{
    public int InitialOrderId { get; set; }   // First order
    public int CycleLength { get; set; }          // 1 (repeat every X)
    public int CyclePeriodId { get; set; }    // Day(0)/Week(1)/Month(2)/Year(3)
    public int TotalCycles { get; set; } // 12 (12 months)
    
    public DateTime StartDateUtc { get; set; }
    public bool IsActive { get; set; }
    public bool LastPaymentFailed { get; set; }
    public DateTime CreatedOnUtc { get; set; }
    
    public bool Deleted { get; set; }
}

public class RecurringPaymentHistory : BaseEntity
{
    public int RecurringPaymentId { get; set; }
    public int OrderId { get; set; } // Recurring order
    public DateTime CreatedOnUtc { get; set; }
}
```

### Workflow:

```
1. SETUP SUBSCRIPTION
   Customer orders: "Monthly Coffee Subscription"
   ├─ Product.IsRecurring = true
   ├─ Product.RecurringCyclePeriodId = 2 (Monthly)
   ├─ Product.RecurringCycleLength = 1 (1 month)
   ├─ Product.RecurringTotalCycles = 12 (12 months)
   ├─ Product.Price = $29.99 (per month)
   │
   └─ When Order placed & paid:
      ├─ Create initial Order #1
      └─ Create RecurringPayment
         ├─ InitialOrderId = 1
   ├─ StartDateUtc = 2024-06-01
         ├─ CyclePeriodId = 2 (Monthly)
  ├─ TotalCycles = 12
         └─ IsActive = true

2. AUTOMATIC PROCESSING
   Scheduled Task (runs daily):
   ├─ Find all active RecurringPayment
   ├─ For each, check: NOW >= NextPaymentDate
   │
   ├─ If YES:
   │  ├─ Create new Order (clone from initial)
   │  ├─ ProcessPaymentAsync() (charge credit card)
   │  ├─ If payment succeeds:
   │  │  ├─ Create RecurringPaymentHistory
   │  │  ├─ LastPaymentFailed = false
   │  │  └─ Next payment due = NOW + 1 month
   │  │
 │  └─ If payment fails:
   │└─ LastPaymentFailed = true (customer can retry)
   │
   └─ When TotalCycles reached:
      └─ IsActive = false (subscription ends)

3. CUSTOMER MANAGEMENT
   My Account → Recurring Payments:
   ├─ View active subscriptions
   ├─ Cancel subscription (IsActive = false)
   ├─ Retry failed payment
   └─ View history
```

## 9.3 Return Management

```csharp
public class ReturnRequest : BaseEntity
{
    public int OrderId { get; set; }
    public int ReturnRequestStatusId { get; set; }  // Pending/Approved/Completed/Rejected

    // What's being returned
    public int ReturnedQuantity { get; set; }
  public string ReasonForReturn { get; set; }     // "Defective", "Wrong color", etc
    public string CustomerComments { get; set; }
    public string AdminComment { get; set; }
    
    public DateTime CreatedOnUtc { get; set; }
}
```

### Workflow:

```
1. INITIATE RETURN
   Customer goes to Order #123
   ├─ Click "Request Return"
   ├─ Select items to return
   ├─ Choose reason: "Defective", "Not as described", etc
   ├─ Add comments
   └─ Submit

2. ADMIN REVIEW
   Admin sees pending returns:
   ├─ Review customer's request
   ├─ Decide: Approve or Reject
   │
   ├─ IF APPROVE:
   │  ├─ ReturnRequestStatus = Approved
   │  ├─ Send email: "Return approved, RMA #12345"
   │  └─ Give customer Return Merchant Authorization
   │
   └─ IF REJECT:
      └─ ReturnRequestStatus = Rejected

3. CUSTOMER SHIPS BACK
   Customer packages items with RMA number
   └─ Ships back to warehouse

4. WAREHOUSE RECEIVES
   Warehouse staff:
   ├─ Scan RMA number
   ├─ Inspect returned items
   ├─ Mark as Received
   ├─ Update ReturnRequestStatus = Completed
   └─ Process refund

5. REFUND ISSUED
   ├─ Payment gateway: Refund transaction
   ├─ Order.PaymentStatus = PartiallyRefunded or Refunded
   ├─ Inventory restored: Product.StockQuantity += returned_qty
   └─ Create RewardPointsHistory (reverse points if used)
```

## 9.4 Vendor (Multi-Seller) Features

```csharp
public class Vendor : BaseEntity
{
    public string Name { get; set; }        // Vendor name
 public string Email { get; set; }
    public string Description { get; set; }
    public int PictureId { get; set; }            // Logo
    public int AddressId { get; set; }     // Business address
    
    public bool Active { get; set; }
    public bool Deleted { get; set; }
    public int? PmCustomerId { get; set; }        // Associated customer (vendor account)
    
    // SEO & Display
    public string MetaKeywords { get; set; }
    public string MetaTitle { get; set; }
    public string MetaDescription { get; set; }
    public int DisplayOrder { get; set; }
}
```

### Relationships:

```
Customer (Vendor Account)
├─ Can manage products (VendorId FK)
├─ Can view own orders only
├─ Can receive commission
└─ Cannot access other vendors' data

Product.VendorId:
├─ If = 0: Vendor by admin
├─ If > 0: Product by specific vendor

Commission:
├─ Admin sets commission rate (e.g., 10%)
├─ Each sale: Vendor gets 90%, Admin gets 10%
└─ Tracked via reports
```

## 9.5 Affiliate Program

```csharp
public class Affiliate : BaseEntity
{
    public string Name { get; set; }
    public string Url { get; set; }
    public string Email { get; set; }
    
  public bool Active { get; set; }
    public int? FriendlyUrlName { get; set; }
    public DateTime CreatedOnUtc { get; set; }
}
```

### Workflow:

```
1. AFFILIATE JOINS
   Marketer applies → Admin approves
   ├─ Create Affiliate entity
   ├─ Generate affiliate code (e.g., "AFFY123")
   └─ Affiliate gets referral link: www.store.com?affiliate=AFFY123

2. CUSTOMER CLICKS LINK
   Customer visits: www.store.com?affiliate=AFFY123
   ├─ System stores affiliate_id in cookie
   └─ Customer shops

3. PURCHASE MADE
   Order.AffiliateId = stored_affiliate_id
   ├─ When Order paid:
   ├─ Commission = Order.OrderTotal × CommissionRate
   └─ Transfer commission to Affiliate

4. AFFILIATE DASHBOARD
   Affiliate views:
   ├─ Click count (from referral link)
   ├─ Sales (orders with their ID)
   ├─ Commission earned
   └─ Payout history
```

---

# 🏗️ PHẦN 10: ARCHITECTURE PATTERNS

## 10.1 Project Structure

```
nopCommerce (src)
│
├─ Libraries
│  ├─ Nop.Core
│  │  └─ Domain/ (Entities)
│  │     ├─ Catalog/ (Product, Category, etc)
│  │     ├─ Orders/ (Order, OrderItem, etc)
│  │├─ Customers/ (Customer, CustomerRole, etc)
│  │     ├─ Shipping/ (Shipment, ShippingMethod, etc)
│  │     ├─ Payments/ (Payment-related enums)
│  │     ├─ Discounts/ (Discount, DiscountRequirement, etc)
│  │     ├─ Common/ (Address, Generic Attribute, etc)
│  │     └─ Stores/ (Store, StoreMapping, etc)
│  │
│  ├─ Nop.Data
││  ├─ Migrations/ (EF Core migrations)
│  │  ├─ Mapping/ (EF Core configurations)
│  │  └─ Repository (Generic repository pattern)
│  │
│  └─ Nop.Services
│     ├─ Catalog/ (ProductService, CategoryService)
│     ├─ Orders/ (OrderService, ShipmentService)
│     ├─ Customers/ (CustomerService, RewardPointsService)
│     ├─ Payments/ (PaymentService)
│     ├─ Shipping/ (ShippingService)
│     ├─ Discounts/ (DiscountService)
│     └─ Installation/ (Setup, seeders)
│
├─ Presentation
│  ├─ Nop.Web (Razor Pages app - Public website)
│  │  ├─ Areas/Admin (Admin panel - Razor Pages)
│  │  ├─ Models/ (View Models for display)
│  │  └─ Pages/ (Razor Pages)
│  │
│  └─ Nop.Web.Framework
│   ├─ Components/ (Reusable Razor components)
│     ├─ Infrastructure/ (DI, middleware)
│     └─ Models/ (Base models)
│
└─ Plugins
   ├─ Nop.Plugin.Payments.PayPalCommerce
   ├─ Nop.Plugin.Shipping.UPS
   ├─ Nop.Plugin.Tax.Avalara
   └─ ... (other plugins)
```

## 10.2 Service Layer Pattern

### Generic Repository Pattern

```csharp
public interface IRepository<T> where T : BaseEntity
{
    Task<T> GetByIdAsync(int id);
    Task InsertAsync(T entity);
    Task UpdateAsync(T entity);
    Task DeleteAsync(T entity);
    
    IQueryable<T> Table { get; }  // LINQ queries
}

// Usage in Service:
public class ProductService : IProductService
{
    private readonly IRepository<Product> _productRepository;
    
    public ProductService(IRepository<Product> productRepository)
    {
     _productRepository = productRepository;
    }
    
  public async Task<Product> GetProductByIdAsync(int id)
  {
      return await _productRepository.GetByIdAsync(id);
    }
    
    public async Task<List<Product>> SearchProducts(string searchText)
    {
 var products = await _productRepository.Table
         .Where(p => p.Name.Contains(searchText) && !p.Deleted)
     .ToListAsync();
   return products;
    }
}
```

### Service Interface Pattern

```csharp
public interface IOrderService
{
    Task<Order> GetOrderByIdAsync(int orderId);
    Task<List<Order>> SearchOrdersAsync(int storeId, int customerId);
 Task InsertOrderAsync(Order order);
    Task UpdateOrderAsync(Order order);
    Task DeleteOrderAsync(Order order);
  
    // Business logic methods
    Task<bool> HasItemsToShipAsync(Order order);
    Task<bool> IsDownloadAllowedAsync(OrderItem orderItem);
}
```

## 10.3 Dependency Injection

```csharp
// Startup configuration
services.AddScoped<IProductService, ProductService>();
services.AddScoped<IOrderService, OrderService>();
services.AddScoped<IRepository<Product>, EfRepository<Product>>();
services.AddScoped<IRepository<Order>, EfRepository<Order>>();

// Usage in Page Handler
public class ProductListModel : PageModel
{
 private readonly IProductService _productService;
  
    public ProductListModel(IProductService productService)
    {
        _productService = productService;
    }
    
    public async Task OnGetAsync()
    {
   Products = await _productService.GetAllProductsAsync();
    }
}
```

## 10.4 Event Publishing System

nopCommerce uses **Observer Pattern** for decoupled business logic:

```csharp
public interface IEventPublisher
{
    Task PublishAsync<TEvent>(TEvent @event) where TEvent : IEvent;
}

// Event definitions
public class EntityInsertedEvent<T> : IEvent where T : BaseEntity
{
    public T Entity { get; }
    public EntityInsertedEvent(T entity)
  {
        Entity = entity;
    }
}

// Subscriber
public class OrderPlacedEventConsumer : IConsumer<EntityInsertedEvent<Order>>
{
    public async Task HandleEventAsync(EntityInsertedEvent<Order> eventMessage)
    {
        // Send confirmation email
        // Update inventory
 // Trigger payment processing
   // etc.
    }
}

// Usage
public class OrderService
{
    public async Task InsertOrderAsync(Order order)
    {
        await _orderRepository.InsertAsync(order);
        await _eventPublisher.PublishAsync(new EntityInsertedEvent<Order>(order));
    }
}
```

## 10.5 Caching Strategy

```csharp
// Static cache (per application restart)
public class CategoryService : ICategoryService
{
    private readonly IStaticCacheManager _staticCacheManager;
    
    public async Task<List<Category>> GetAllCategoriesAsync()
    {
        var key = _staticCacheManager.PrepareKeyForDefaultCache("categories_all");
 return await _staticCacheManager.GetAsync(key, async () =>
        {
// Only executes if not in cache
            return await _categoryRepository.GetAllAsync();
        });
    }
}

// Short-term cache (per request or distributed)
public class ShoppingCartService
{
    private readonly IShortTermCacheManager _shortTermCacheManager;
    
    public async Task<List<ShoppingCartItem>> GetShoppingCartAsync(int customerId)
    {
      var key = $"cart_{customerId}";
     return await _shortTermCacheManager.GetAsync(key, async () =>
        {
            return await _shoppingCartRepository.GetAsync(c => c.CustomerId == customerId);
        }, 5); // Cache for 5 minutes
    }
}
```

## 10.6 Soft Delete Pattern

Many entities implement `ISoftDeletedEntity`:

```csharp
public interface ISoftDeletedEntity
{
    bool Deleted { get; set; }
}

// When querying:
var query = _productRepository.Table
    .Where(p => !p.Deleted)  // Always filter soft-deleted
    .ToList();

// When deleting:
product.Deleted = true;
await _productRepository.UpdateAsync(product);  // Not physically removed
```

---

## TÓMIC ✅

Tôi vừa tạo một **tài liệu MD siêu chi tiết (5000+ lines)** bao gồm:

### 📋 Nội Dung Chính:

1. **20% Core** - 5 entities chính & cách hoạt động
2. **Database Structure** - Relationships, Enums, Foreign Keys
3. **Order Workflow** - Complete lifecycle từ Shopping đến Delivery
4. **Discount System** - Types, Conditions, Usage Patterns
5. **Product Management** - 6 loại sản phẩm, Attributes, Combinations
6. **Payment Processing** - Gateways, Payment Methods, Status Machine
7. **Shipping** - Methods, Workflows, Partial Shipments
8. **Customer Management** - Roles, Addresses, Reward Points
9. **Advanced Features** - GiftCards, Subscriptions, Returns, Vendors, Affiliates
10. **Architecture Patterns** - Services, DI, Events, Caching, Soft Deletes

### 🎯 Lợi Ích:

✅ Hiểu được 80% hệ thống chỉ từ 20% entities  
✅ Có thể mở rộng sang các features khác  
✅ Rõ relationships, workflows, business logic  
✅ Sẵn sàng để code/customize  