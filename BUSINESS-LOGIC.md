# 📊 NopCommerce - Phân tích Chi tiết Nghiệp vụ Cốt lõi

**Tài liệu này mô tả các nghiệp vụ chính và đặc sắc của nopCommerce - nền tảng e-commerce mã nguồn mở dựa trên .NET 9**

---

## 📑 Mục lục

1. [Tổng quan dự án](#tổng-quan-dự-án)
2. [Kiến trúc chính](#kiến-trúc-chính)
3. [Nghiệp vụ cốt lõi](#nghiệp-vụ-cốt-lõi)
4. [Quy trình mua hàng chi tiết](#quy-trình-mua-hàng-chi-tiết)
5. [Công nghệ sử dụng](#công-nghệ-sử-dụng)
6. [Tính năng nổi bật](#tính-năng-nổi-bật)

---

## 🎯 Tổng quan dự án

### Định nghĩa
**nopCommerce** là một nền tảng thương mại điện tử (e-commerce) mã nguồn mở, được xây dựng trên **.NET 9**, cho phép các doanh nghiệp xây dựng cửa hàng trực tuyến với các tính năng quản lý sản phẩm, đơn hàng, khách hàng, thanh toán, vận chuyển và nhiều hơn nữa.

### Đặc điểm chính
- ✅ **Open Source** - Mã nguồn mở, miễn phí
- ✅ **Multi-Store** - Hỗ trợ quản lý nhiều cửa hàng
- ✅ **Plugin Architecture** - Kiến trúc plugin mở rộng
- ✅ **Enterprise-Ready** - Sẵn sàng cho doanh nghiệp lớn
- ✅ **Multi-Language & Multi-Currency** - Đa ngôn ngữ, đa tiền tệ
- ✅ **Mobile-Friendly** - Thân thiện di động
- ✅ **Modern Stack** - Sử dụng công nghệ hiện đại

---

## 🏗️ Kiến trúc chính

### Cấu trúc thư mục

```
nopCommerce/src/
├── Libraries/              # Thư viện cốt lõi
│   ├── Nop.Core/ # Entities, Interfaces, Core logic
│   ├── Nop.Data/          # Database, Migrations, DbContext
│   ├── Nop.Services/      # Business logic, Services
│   └── Nop.Web.Framework/ # Framework dùng chung
├── Presentation/          # Lớp hiển thị
│   ├── Nop.Web/     # Ứng dụng web chính (Admin + Frontend)
│   └── Nop.Web.Framework/ # Framework web
├── Plugins/               # Các plugin mở rộng
│   ├── Payments/          # Plugin thanh toán
│   ├── Shipping/          # Plugin vận chuyển
│   ├── Tax/        # Plugin tính thuế
│   ├── Widgets/  # Widget UI
│   └── Misc/          # Plugin khác
└── Tests/     # Unit tests
    └── Nop.Tests/
```

### 3 Lớp chính

#### 1️⃣ **Libraries (Lớp Logic nghiệp vụ)**
- **Nop.Core**: Entities (sản phẩm, đơn hàng, khách hàng, v.v.), Interfaces, Constants
- **Nop.Data**: Database context, Migrations, Repository pattern
- **Nop.Services**: Business logic chính (xử lý đơn hàng, tính giá, v.v.)
- **Nop.Web.Framework**: Framework dùng chung (model binding, filters, helpers)

#### 2️⃣ **Presentation (Lớp hiển thị)**
- **Nop.Web**: Ứng dụng Razor Pages chính
  - Admin Interface: Quản lý sản phẩm, đơn hàng, khách hàng
  - Frontend: Giao diện mua sắm cho khách hàng

#### 3️⃣ **Plugins (Lớp mở rộng)**
- Plugin thanh toán: PayPal, Stripe, Manual Payment
- Plugin vận chuyển: UPS, Fixed By Weight, Pickup In Store
- Plugin thuế: Avalara, Fixed By Country
- Plugin khác: Analytics, Email Marketing, v.v.

---

## 🎪 Nghiệp vụ Cốt lõi

### 1️⃣ QUẢN LÝ HÀNG HÓA (Catalog Management) - CỰC KỲ PHỨC TẠP

#### 1.1 Sản phẩm (Products)
**Lớp dữ liệu**: `Product` entity
**Services**: `IProductService`, `ProductService`

- **CRUD sản phẩm**: Tạo, đọc, cập nhật, xóa sản phẩm
- **Product Visibility**: Ẩn/Hiện sản phẩm
- **Product Pricing**: Giá cơ bản, giá nhập
- **Product Images & Videos**: Quản lý ảnh, video sản phẩm
- **Product Status**: Hoạt động/Không hoạt động

**Ví dụ thực tế**: iPhone 13 Pro
```
Tên: iPhone 13 Pro
Mô tả: Apple smartphone cao cấp
Giá cơ bản: 999 USD
Hình ảnh: 5 ảnh khác nhau
Video: Demo sử dụng
Danh mục: Electronics > Smartphones
Nhà sản xuất: Apple
```

#### 1.2 Thuộc tính Sản phẩm (Product Attributes) - RẤT PHỨC TẠP
**Entities**: `ProductAttribute`, `ProductAttributeValue`, `ProductAttributeMapping`, `ProductAttributeCombination`
**Services**: `IProductAttributeService`, `ProductAttributeService`

Sản phẩm có thể có nhiều thuộc tính tùy chỉnh:

```
Sản phẩm: Áo sơ mi nam
├── Thuộc tính 1: Size (XS, S, M, L, XL, XXL)
├── Thuộc tính 2: Màu sắc (Đỏ, Xanh, Trắng, Đen)
└── Thuộc tính 3: Chất liệu (Cotton, Polyester, Silk)
```

**Product Attribute Combination (SKU)**:
- Kết hợp Size M + Màu Đỏ + Chất Cotton = SKU123
- Kết hợp Size L + Màu Xanh + Chất Polyester = SKU124

**Tính năng**:
- Thuộc tính định dạng: Dropdownlist, Radio button, Checkbox, Text input
- Thuộc tính giá: Mỗi tổ hợp có thể có giá khác nhau
- Hình ảnh thuộc tính: Mỗi tổ hợp có thể có hình ảnh khác nhau

#### 1.3 Giá tiền (Pricing System) - RẤT PHỨC TẠP

**Công thức tính giá**:
```
Giá cơ bản
   ↓
+ Tier Price (giảm giá khi mua nhiều)
 ↓
+ Discount (chiết khấu)
   ↓
+ Tax (thuế)
   ↓
= Giá cuối cùng
```

**Tier Price (Giảm giá theo số lượng)**:
```
Sản phẩm: Áo sơ mi
├─ 1-10 cái: 100,000 VNĐ/cái
├─ 11-50 cái: 80,000 VNĐ/cái
└─ 51+ cái: 60,000 VNĐ/cái
```

**Services**: `IPriceCalculationService`, `PriceCalculationService`

#### 1.4 Kho hàng (Inventory) - CỰC KỲ PHỨC TẠP
**Entities**: `ProductWarehouseInventory`, `Warehouse`, `StockQuantityHistory`
**Services**: `IWarehouseService`, `WarehouseService`

**Tính năng**:
- Quản lý tồn kho theo từng kho (multi-warehouse)
- Theo dõi lịch sử tồn kho
- Back-order support (cho phép đặt hàng khi hết)
- Back-In-Stock Subscription (thông báo khi hàng về)
- Low Stock warnings (cảnh báo hết hàng sắp xảy ra)

**Ví dụ**:
```
Sản phẩm: iPhone 13 Pro
├─ Kho 1 (Hà Nội): 50 cái
├─ Kho 2 (TP.HCM): 30 cái
└─ Kho 3 (Đà Nẵng): 20 cái
Tổng: 100 cái
```

#### 1.5 Danh mục & Nhà sản xuất
**Services**: `ICategoryService`, `IManufacturerService`

- **Categories**: Phân loại sản phẩm (Electronics > Smartphones > Apple)
- **Manufacturers**: Nhà sản xuất (Apple, Samsung, Sony, v.v.)
- **Category Templates**: Mẫu hiển thị danh mục khác nhau
- **Manufacturer Templates**: Mẫu hiển thị nhà sản xuất

#### 1.6 Sản phẩm Liên quan
**Services**: `ICompareProductsService`, `IRecentlyViewedProductsService`

- **Related Products**: Sản phẩm gợi ý (accessories)
- **Cross-Sell Products**: Sản phẩm bán chéo (what else can you buy)
- **Recently Viewed Products**: Sản phẩm đã xem gần đây
- **Compare Products**: So sánh sản phẩm

---

### 2️⃣ QUẢN LÝ ĐƠN HÀNG (Order Management) - CỰC KỲ PHỨC TẠP

#### 2.1 Giỏ hàng (Shopping Cart)
**Entities**: `ShoppingCartItem`, `ShoppingCart`
**Services**: `IShoppingCartService`, `ShoppingCartService`

**Tính năng**:
- Thêm sản phẩm vào giỏ
- Cập nhật số lượng
- Xóa sản phẩm khỏi giỏ
- Lưu giỏ (persistent cart)
- Giỏ hàng tạm thời (for guest users)

**Trạng thái giỏ hàng**:
```
ShoppingCartType
├─ ShoppingCart (Mua hàng)
└─ Wishlist (Danh sách yêu thích)
```

#### 2.2 Checkout Attributes
**Entities**: `CheckoutAttribute`, `CheckoutAttributeValue`

Tính năng cho phép thêm yêu cầu bổ sung tại checkout:

```
Ví dụ:
├─ Bạn muốn bao gói quà không? (Yes/No)
├─ Ghi chú đặc biệt cho người nhận: [Text input]
└─ Chọn loại bao bì: (Standard/Premium/Eco-friendly)
```

#### 2.3 Xử lý Đơn hàng (Order Processing)
**Entities**: `Order`, `OrderItem`, `OrderNote`
**Services**: `IOrderService`, `IOrderProcessingService`

**Trạng thái Đơn hàng**:
```
OrderStatus
├─ Pending (Chờ xử lý)
├─ Processing (Đang xử lý)
├─ Complete (Hoàn thành)
├─ Cancelled (Hủy)
└─ NotDelivered (Không giao được)
```

**Trạng thái Thanh toán**:
```
PaymentStatus
├─ Pending (Chờ thanh toán)
├─ Authorized (Cấp phép)
├─ Paid (Đã thanh toán)
└─ Refunded (Đã hoàn tiền)
```

**Trạng thái Vận chuyển**:
```
ShippingStatus
├─ NotYetShipped (Chưa vận chuyển)
├─ Shipped (Đã vận chuyển)
└─ Delivered (Đã giao)
```

**Thao tác Đơn hàng**:
```
Order Processing Service
├─ MarkAsAuthorized() - Cấp phép thanh toán
├─ Capture() - Thu tiền
├─ MarkAsPaid() - Đánh dấu đã thanh toán
├─ MarkAsShipped() - Đánh dấu vận chuyển
├─ MarkAsDelivered() - Đánh dấu đã giao
└─ CancelOrder() - Hủy đơn
```

#### 2.4 Tính toán Tổng đơn hàng (Order Total Calculation)
**Services**: `IOrderTotalCalculationService`, `OrderTotalCalculationService`

**Công thức**:
```
Subtotal (Tổng giá sản phẩm)
  = Sum(Quantity × Unit Price)

Order Total
  = Subtotal
  + Shipping Fee
  + Tax
  - Discount
  + Surcharge (nếu có)
```

**Ví dụ tính toán**:
```
2x iPhone 13 Pro @ 999 USD = 1,998 USD (Subtotal)
+ Shipping: 50 USD
+ Tax (10%): 204.8 USD
- Discount (Coupon): -100 USD
= Total: 2,152.8 USD
```

**Services tính từng phần**:
```
Order Total Calculation Service
├─ GetShoppingCartSubTotal()
├─ GetShippingTotal()
├─ GetTaxTotal()
├─ GetDiscountTotal()
└─ GetOrderTotal()
```

#### 2.5 Hoàn trả (Return Request)
**Entities**: `ReturnRequest`, `ReturnRequestReason`, `ReturnRequestAction`
**Services**: `IReturnRequestService`, `ReturnRequestService`

**Quy trình hoàn trả**:
```
Khách đề nghị hoàn trả
    ↓
Admin xem xét
    ↓
Chấp nhận / Từ chối
    ↓
Khách gửi hàng về
    ↓
Admin nhận hàng
    ↓
Hoàn tiền
    ↓
Hoàn thành
```

**Lý do hoàn trả**:
```
├─ Sản phẩm lỗi / Hư hỏng
├─ Không đúng như mô tả
├─ Không vừa / Không phù hợp
└─ Không muốn nữa
```

#### 2.6 Thẻ quà (Gift Cards)
**Entities**: `GiftCard`, `GiftCardUsageHistory`
**Services**: `IGiftCardService`, `GiftCardService`

**Loại thẻ quà**:
```
GiftCardType
├─ Virtual (Thẻ ảo)
└─ Physical (Thẻ vật lý)
```

**Tính năng**:
- Tạo thẻ quà với mã code
- Sử dụng thẻ quà khi thanh toán
- Theo dõi lịch sử sử dụng
- Kiểm tra số dư

#### 2.7 Wishlist Tùy chỉnh (Custom Wishlists)
**Entities**: `CustomWishlist`
**Services**: `ICustomWishlistService`, `CustomWishlistService`

Cho phép khách hàng tạo danh sách yêu thích riêng (ví dụ: "Quà cho bố", "Các sản phẩm tôi quan tâm")

#### 2.8 Điểm thưởng (Reward Points)
**Entities**: `RewardPointsHistory`
**Services**: `IRewardPointService`, `RewardPointService`

**Quy trình**:
```
Khách mua hàng
  ↓
Tính điểm: 1 USD = X điểm
    ↓
Thêm vào tài khoản khách
    ↓
Khách có thể sử dụng điểm để giảm giá
```

**Ví dụ**:
```
Cấu hình: 1 USD = 10 điểm
Mua hàng: 100 USD → Nhận 1,000 điểm
Tích lũy: Sau 10 lần mua → 10,000 điểm
Sử dụng: 1,000 điểm = 10 USD giảm giá
```

**Settings**: Có thể cấu hình:
- Tỷ lệ tích lũy
- Tỷ lệ sử dụng
- Khoảng thời gian hoạt động
- Có thể hoàn điểm khi hoàn trả hay không

#### 2.9 Đơn hàng Định kỳ (Recurring Payments)
**Entities**: `RecurringPayment`, `RecurringPaymentHistory`

**Mô tả**: Thanh toán lặp lại (subscription model)

```
Khách đăng ký gói hàng tháng
    ↓
Mỗi tháng hệ thống tự động:
    ├─ Tạo đơn hàng mới
    ├─ Xử lý thanh toán
    ├─ Gửi hàng
    └─ Ghi lịch sử
```

**Ví dụ**:
```
Gói đăng ký: "Hộp quà hàng tháng"
Giá: 99 USD/tháng
Chu kỳ: Monthly (Hàng tháng)
Thời hạn: 12 tháng
Tổng: 1,188 USD (chia thành 12 lần)
```

#### 2.10 Báo cáo Đơn hàng (Order Reports)
**Services**: `IOrderReportService`, `OrderReportService`

**Báo cáo**:
```
├─ Best Sellers (Sản phẩm bán chạy nhất)
├─ Sales by Country (Bán hàng theo quốc gia)
├─ Average Report (Báo cáo trung bình)
│ └─ Trung bình order value, trung bình number of orders
├─ Customer Reports (Báo cáo khách hàng)
│   └─ Best customers, customers who haven't bought, etc.
└─ Incomplete Orders (Đơn hàng chưa hoàn thành)
```

---

### 3️⃣ THANH TOÁN (Payment) - CỰC KỲ PHỨC TẠP

#### 3.1 Phương thức Thanh toán (Payment Methods)
**Entities**: `PaymentSettings`
**Services**: `IPaymentService`, `PaymentService`, `IPaymentPluginManager`

**Các loại cổng thanh toán** (thông qua plugin):
```
Credit/Debit Card
├─ PayPal Commerce
├─ Stripe
└─ Authorize.net

Digital Wallet
├─ Apple Pay
└─ Google Pay

Direct Bank Transfer
├─ Wire Transfer
└─ ACH Transfer

Offline Methods
├─ Check/Money Order
├─ Cash on Delivery (COD)
└─ Bank Transfer
```

#### 3.2 Payment Processing Flow
**Services**: `IPaymentMethod` interface (plugin implement)

```
1. Khách chọn phương thức thanh toán
    ↓
2. System gọi IPaymentMethod.ProcessPayment()
  ↓
3. Cổng thanh toán xử lý
    ├─ Validate card/wallet
    ├─ Authorize transaction
    └─ Capture funds
    ↓
4. Nhận response từ cổng
    ↓
5. Cập nhật trạng thái order
```

**Các thao tác thanh toán**:
```
ProcessPaymentRequest
├─ Thông tin đơn hàng
├─ Thông tin khách hàng
└─ Chi tiết thanh toán

ProcessPaymentResult
├─ Success/Error
├─ Authorization code
├─ Transaction ID
└─ Error message (nếu có)
```

**Post-Processing**:
```
Capture Payment: Thu tiền thực tế từ tài khoản khách
Refund Payment: Hoàn tiền lại cho khách
Void Payment: Hủy thanh toán (trước khi capture)
```

#### 3.3 Recurring Payments (Thanh toán định kỳ)
**Loại chu kỳ**:
```
RecurringProductCyclePeriod
├─ Days (Ngày)
├─ Weeks (Tuần)
├─ Months (Tháng)
└─ Years (Năm)
```

**Quy trình**:
```
Khách đăng ký recurring
    ↓
Hệ thống lên lịch thanh toán
    ↓
Vào ngày thanh toán:
    ├─ Tạo đơn hàng mới
    ├─ Xử lý thanh toán tự động
    ├─ Ghi lịch sử
    └─ Gửi email xác nhận
```

---

### 4️⃣ VẬN CHUYỂN (Shipping) - CỰC KỲ PHỨC TẠP

#### 4.1 Phương thức Vận chuyển (Shipping Methods)
**Entities**: `ShippingMethod`, `ShippingMethodCountryMapping`
**Services**: `IShippingService`, `ShippingService`

**Các loại phương thức**:
```
Fixed Rate
├─ Cố định cho tất cả: 50,000 VNĐ

By Weight
├─ 0-1kg: 20,000 VNĐ
├─ 1-5kg: 50,000 VNĐ
└─ 5kg+: 100,000 VNĐ

By Order Total
├─ < 500,000 VNĐ: 30,000 VNĐ
├─ 500,000-2,000,000 VNĐ: 20,000 VNĐ
└─ > 2,000,000 VNĐ: Miễn phí

Real-time APIs
├─ UPS
├─ FedEx
├─ DHL
└─ Local carriers
```

#### 4.2 Tính phí Vận chuyển (Shipping Rate Calculation)
**Services**: `IShippingRateComputationMethod` (plugin implement)

**Quy trình**:
```
GetShippingOptionRequest
├─ Địa chỉ giao hàng
├─ Trọng lượng sản phẩm
├─ Các sản phẩm trong đơn
└─ Tổng tiền đơn

    ↓ (tính toán)

GetShippingOptionResponse
├─ Danh sách các phương thức khả dụng
├─ Giá vận chuyển cho từng phương thức
└─ Thời gian giao hàng dự kiến
```

**Ví dụ**:
```
Đơn hàng đến Hà Nội, trọng lượng 5kg, tổng 1,000,000 VNĐ

Phương thức 1: Standard Shipping
├─ Giá: 50,000 VNĐ
└─ Thời gian: 5-7 ngày

Phương thức 2: Express Shipping
├─ Giá: 150,000 VNĐ
└─ Thời gian: 2-3 ngày

Phương thức 3: Overnight
├─ Giá: 300,000 VNĐ
└─ Thời gian: Hôm sau
```

#### 4.3 Quản lý Shipment (Lô hàng)
**Entities**: `Shipment`, `ShipmentItem`
**Services**: `IShipmentService`, `ShipmentService`

**Tính năng**:
- Tạo shipment từ order items
- Tách order thành nhiều shipment (nếu cần)
- Theo dõi tracking number
- Cập nhật trạng thái giao hàng

**Trạng thái Shipment**:
```
├─ NotYetShipped
├─ Shipped
├─ ReadyForPickup
└─ Delivered
```

#### 4.4 Tracking & Notification
**Services**: `IShipmentTracker` (plugin implement)

**Tính năng**:
- Lưu tracking number từ carrier (UPS, FedEx, v.v.)
- Cập nhật tự động trạng thái shipment
- Gửi email thông báo cho khách khi giao hàng

#### 4.5 Pickup Points (Điểm nhận hàng)
**Entities**: `PickupPoint`
**Services**: `IPickupPointProvider` (plugin implement)

**Loại pickup**:
```
Pickup In Store
├─ Nhận hàng tại cửa hàng

Locker / Parcel Machine
├─ Nhận hàng tại tủ tự động

Partner Pickup Points
├─ Nhận tại điểm tổng hợp của đối tác
```

#### 4.6 Khoảng thời gian giao hàng (Delivery Dates)
**Entities**: `DeliveryDate`, `ProductAvailabilityRange`
**Services**: `IDateRangeService`

**Tính năng**:
- Cấu hình khoảng thời gian giao hàng (2-3 days, 3-5 days, v.v.)
- Gắn với sản phẩm
- Hiển thị cho khách khi đặt hàng

#### 4.7 Quản lý Kho (Warehouse Management)
**Entities**: `Warehouse`
**Services**: `IWarehouseService`

**Tính năng**:
- Quản lý nhiều kho
- Tồn kho theo từng kho
- Chọn kho nào để gửi đơn hàng

---

### 5️⃣ THUẾ (Tax) - CỰC KỲ PHỨC TẠP

#### 5.1 Tính thuế (Tax Calculation)
**Entities**: `TaxCategory`, `TaxRate`
**Services**: `ITaxService`, `TaxService`, `ITaxPluginManager`

**Các phương thức tính thuế**:
```
Fixed Rate
├─ Cố định 10% cho tất cả sản phẩm

By Country/State/Zip
├─ Việt Nam: 10% VAT
├─ Thái Lan: 7% VAT
├─ Hoa Kỳ (California): 8.625% Sales Tax
└─ EU: Khác nhau 15%-25% theo quốc gia

Avalara API (USA)
├─ Tính thuế real-time dựa trên địa chỉ
├─ Tuân thủ luật thuế tiểu bang
└─ Hỗ trợ special districts & local taxes
```

#### 5.2 Tax Category (Danh mục tính thuế)
**Tính năng**:
- Một số sản phẩm không tính thuế (books, food)
- Một số sản phẩm tính thuế cao (luxury)
- Mỗi sản phẩm gắn với một tax category

```
Tax Categories:
├─ Books (0% tax)
├─ Food (0% tax)
├─ Standard Items (10% tax)
└─ Luxury Items (15% tax)
```

#### 5.3 Quy trình tính thuế
```
Sản phẩm X: 100 USD, Tax Category = "Standard"
Khách ở Việt Nam

    ↓ tính toán

Tax Rate cho Việt Nam + Standard = 10%
Tax = 100 × 10% = 10 USD

    ↓

Tổng = 100 + 10 = 110 USD
```

#### 5.4 VAT Checking (Kiểm tra VAT)
**Services**: `ICheckVatService`

**Tính năng**:
- Kiểm tra mã VAT hợp lệ (EU)
- Xác định đối tượng chịu thuế
- Hỗ trợ B2B, B2C scenarios

---

### 6️⃣ KHUYẾN MÃI & GIẢM GIÁ (Discounts) - CỰC KỲ PHỨC TẠP

#### 6.1 Loại Giảm giá (Discount Types)
**Entities**: `Discount`
**Services**: `IDiscountService`, `DiscountService`

```
Discount Type:
├─ Percentage (%)
│   └─ Ví dụ: Giảm 20%
│
└─ Fixed Amount (tiền cố định)
    └─ Ví dụ: Giảm 100,000 VNĐ
```

#### 6.2 Phạm vi áp dụng (Discount Scopes)
```
Applied to:
├─ Products (sản phẩm cụ thể)
├─ Categories (danh mục sản phẩm)
├─ Manufacturers (nhà sản xuất)
└─ Orders (toàn bộ đơn hàng)

(Thông qua các mapping tables:
 DiscountProductMapping,
 DiscountCategoryMapping,
 DiscountManufacturerMapping)
```

#### 6.3 Điều kiện áp dụng (Requirements) - RẤT PHỨC TẠP
**Entities**: `DiscountRequirement`, `IDiscountRequirementRule` (plugin implement)

```
Discount "Summer Sale"
├─ Giảm 20% cho sản phẩm summer
├─ Áp dụng khi:
│   ├─ Tối thiểu order value: 500,000 VNĐ
│   ├─ Chỉ cho khách hàng VIP
│   ├─ Chỉ cho khách hàng mới (lần đầu mua)
│   ├─ Chỉ trong khoảng thời gian (01/06 - 30/08)
│   └─ Không tích lũy với discount khác
```

**Built-in Requirements**:
```
├─ Minimum Order Amount
├─ Maximum Order Amount
├─ Shippable Products Only
├─ Products from Categories
├─ Products with Given Attributes
└─ Customer Roles (VIP, Wholesaler, v.v.)
```

#### 6.4 Mã khuyến mãi (Coupon Codes)
**Tính năng**:
- Tạo mã giảm giá (ví dụ: SUMMER20, NEWYEAR50)
- Giới hạn số lần dùng (vô hạn hoặc N lần)
- Giới hạn số người dùng (vô hạn hoặc N người)
- Hết hạn sau ngày nào

```
Coupon: SUMMER20
├─ Loại: Percentage - 20%
├─ Áp dụng cho: Summer collection
├─ Tối thiểu order: 500,000 VNĐ
├─ Số lần dùng tối đa: 100
├─ Số người dùng tối đa: 100
├─ Mỗi khách dùng tối đa: 1 lần
└─ Hết hạn: 31/08/2024
```

#### 6.5 Lịch sử sử dụng (Usage History)
**Entities**: `DiscountUsageHistory`

Theo dõi:
- Khách nào dùng mã
- Lúc nào dùng
- Đơn hàng nào
- Giảm giá bao nhiêu

---

### 7️⃣ QUẢN LÝ KHÁCH HÀNG (Customer Management)

#### 7.1 Customer Management
**Entities**: `Customer`
**Services**: `ICustomerService`, `CustomerService`

**Thông tin khách hàng**:
```
├─ Thông tin cơ bản: Tên, email, phone
├─ Mật khẩu: Hashed, Encrypted, hoặc Plain text
├─ Status: Active / Deleted
├─ Roles: Customer, Admin, Vendor, Guest
├─ Language: Ngôn ngữ ưa thích
├─ Currency: Tiền tệ ưa thích
└─ Time zone: Múi giờ
```

#### 7.2 Thuộc tính khách hàng (Customer Attributes)
**Entities**: `CustomerAttribute`, `CustomerAttributeValue`

Cho phép thêm thông tin tùy chỉnh:
```
├─ Company Name
├─ Job Title
├─ Birth Date
├─ Preferred Language
└─ Custom fields
```

#### 7.3 Đăng ký tài khoản (Customer Registration)
**Services**: `ICustomerRegistrationService`, `CustomerRegistrationService`

**Quy trình**:
```
Khách điền form đăng ký
    ↓
Validate thông tin
    ├─ Email chưa tồn tại?
    ├─ Username hợp lệ?
    └─ Mật khẩu đủ mạnh?
    ↓
Mã hóa mật khẩu
    ↓
Tạo tài khoản
    ↓
(Optional) Gửi email xác nhận
```

**Password Format**:
```
├─ Hashed: MD5, SHA256, PBKDF2 (không thể khôi phục)
├─ Encrypted: AES encryption (có thể giải mã)
└─ Plain: Lưu trữ password text (không khuyến khích!)
```

#### 7.4 Multi-Factor Authentication (2FA)
**Entities**: `MultiFactorAuthenticationSettings`
**Services**: `IMultiFactorAuthenticationMethod` (plugin implement)

**Loại 2FA**:
```
├─ Google Authenticator / Authy
├─ SMS verification
├─ Email verification
└─ Recovery codes
```

#### 7.5 External Authentication (Đăng nhập ngoài)
**Services**: `IExternalAuthenticationMethod` (plugin implement)

**OAuth Providers**:
```
├─ Facebook
├─ Google
├─ Microsoft
├─ Apple
└─ Custom providers
```

**Quy trình OAuth**:
```
Khách click "Sign in with Facebook"
    ↓
Redirect tới Facebook OAuth
    ↓
Facebook xác thực, cấp token
    ↓
Hệ thống nhận thông tin khách
    ↓
Auto-link hoặc create account
    ↓
Đăng nhập thành công
```

#### 7.6 Địa chỉ Khách hàng (Customer Addresses)
**Entities**: `Address`, `CustomerAddressMapping`
**Services**: `IAddressService`

**Tính năng**:
- Một khách có nhiều địa chỉ
- Một địa chỉ mặc định cho billing
- Một địa chỉ mặc định cho shipping
- Lịch sử địa chỉ

#### 7.7 Báo cáo Khách hàng (Customer Reports)
**Services**: `ICustomerReportService`

**Báo cáo**:
```
├─ Best Customers (Chi tiêu nhiều nhất)
├─ Registered Customers (Tổng số khách đã đăng ký)
├─ Customers with Orders (Khách đã mua)
└─ Customers without Orders (Khách chưa mua)
```

---

### 8️⃣ QUẢN LÝ CỬA HÀNG (Store Management)

#### 8.1 Multi-Store Support
**Entities**: `Store`
**Services**: `IStoreService`, `IStoreMappingService`

**Tính năng**:
- Quản lý nhiều cửa hàng từ một admin
- Mỗi cửa hàng có domain riêng
- Cấu hình độc lập per-store
- Khách hàng chung (hoặc riêng per-store)

```
Example:
├─ Store 1: shop1.com (tiếng Việt, VNĐ)
├─ Store 2: shop2.com (tiếng Anh, USD)
└─ Store 3: shop3.com (tiếng Thái, THB)
```

#### 8.2 Configuration (Cấu hình)
**Services**: `ISettingService`, `SettingService`

**Cài đặt hệ thống**:
```
├─ Catalog Settings (hiển thị sản phẩm)
├─ Order Settings (đơn hàng)
├─ Shipping Settings (vận chuyển)
├─ Tax Settings (thuế)
├─ Payment Settings (thanh toán)
├─ Customer Settings (khách hàng)
├─ Common Settings (cài đặt chung)
├─ Localization Settings (ngôn ngữ, tiền tệ)
├─ SEO Settings (SEO)
├─ Security Settings (bảo mật)
└─ Email Settings (email)
```

**Mỗi cài đặt có thể**:
- Set per-store
- Set per-customer
- Set globally

---

### 9️⃣ TÌM KIẾM & SEO

#### 9.1 Product Search (Tìm kiếm sản phẩm)
**Services**: `ISearchProvider` (plugin implement)

**Search Providers**:
```
SQL Full-Text Search
├─ Tìm kiếm cơ bản
└─ Hiệu suất tốt cho shop nhỏ-vừa

Elasticsearch
├─ Tìm kiếm nâng cao
├─ Faceted search (lọc theo filter)
└─ Autocomplete
└─ Hiệu suất tốt cho shop lớn

Lucene
├─ Full-text search
├─ Proximity search
└─ Wildcard search
```

**Search Features**:
```
├─ Keyword search
├─ Category filter
├─ Price range filter
├─ Brand filter
├─ Attribute filter
├─ Rating filter
└─ Stock status filter
```

#### 9.2 SEO (Search Engine Optimization)
**Services**: `IUrlRecordService`

**Tính năng**:
```
URL Slug
├─ iphone-13-pro (thay vì product.aspx?id=123)

Meta Tags
├─ Meta Title: "Buy iPhone 13 Pro - Best Price"
├─ Meta Description: "Get iPhone 13 Pro with..."
├─ Meta Keywords: "iphone, smartphone, 5G"

Social Meta Tags
├─ Open Graph (Facebook, LinkedIn)
└─ Twitter Card

Robots & Sitemap
├─ robots.txt
├─ sitemap.xml
└─ Canonical tags
```

---

### 🔟 TIN NHẮN & EMAIL (Messaging)

#### 10.1 Email System (Hệ thống email)
**Entities**: `QueuedEmail`, `EmailAccount`
**Services**: `IQueuedEmailService`, `IEmailSender`

**Tính năng**:
- Email được xếp hàng (queued), không gửi trực tiếp
- Gửi nền (background job) để tránh chậm trang web
- Xác định độ ưu tiên (high, normal, low)
- Retry logic (thử lại nếu gửi lỗi)

**SMTP Configuration**:
```
├─ Host: smtp.gmail.com
├─ Port: 587 (hoặc 465 cho SSL)
├─ Username: your-email@gmail.com
├─ Password: app-password
├─ Use SSL/TLS
└─ Default from address
```

**Tác vụ nền (Background Jobs)**:
```
QueuedMessagesSendTask
├─ Chạy mỗi N phút
├─ Gửi tất cả email pending
├─ Cập nhật trạng thái
└─ Log lỗi
```

#### 10.2 Message Templates (Mẫu tin nhắn)
**Entities**: `MessageTemplate`
**Services**: `IMessageTemplateService`

**Built-in Templates**:
```
├─ Customer registration
├─ Password recovery
├─ Order confirmation
├─ Shipped notification
├─ Delivery confirmation
├─ Return request notification
├─ Newsletter
├─ Gift card notification
└─ Vendor registration
```

#### 10.3 Tokenization (Thay thế token)
**Services**: `ITokenizer`, `IMessageTokenProvider`

**Token Examples**:
```
Template: "Hello {{Customer.FirstName}},
           Your order {{Order.OrderNumber}} has been shipped."

Replace:
├─ {{Customer.FirstName}} → "John"
├─ {{Order.OrderNumber}} → "ORD-12345"

Result: "Hello John,
     Your order ORD-12345 has been shipped."
```

**Available Tokens**:
```
Customer:
├─ {{Customer.FirstName}}
├─ {{Customer.LastName}}
├─ {{Customer.Email}}
└─ {{Customer.Username}}

Order:
├─ {{Order.OrderNumber}}
├─ {{Order.OrderTotal}}
├─ {{Order.OrderSubtotal}}
└─ {{Order.OrderUrl}}

Shipping:
├─ {{Shipment.ShippingDate}}
├─ {{Shipment.TrackingNumber}}
└─ {{Shipment.TrackingUrl}}
```

#### 10.4 Campaigns (Chiến dịch tiếp thị)
**Entities**: `Campaign`
**Services**: `ICampaignService`

**Tính năng**:
- Tạo chiến dịch email
- Chọn đối tượng nhận email (tất cả khách, khách cũ, v.v.)
- Lên lịch gửi
- Theo dõi hiệu suất (gửi, mở, click)

#### 10.5 Newsletter (Bản tin)
**Entities**: `NewsLetterSubscription`
**Services**: `INewsLetterSubscriptionService`

**Tính năng**:
- Khách hàng đăng ký nhận bản tin
- Quản lý danh sách subscribers
- Gửi bản tin định kỳ
- Unsubscribe link

---

### 1️⃣1️⃣ BÁO CÁO & ANALYTICS

#### 11.1 Reports (Báo cáo)
**Services**: `IOrderReportService`, `ICustomerReportService`

**Báo cáo doanh số**:
```
├─ Sales Summary
│ ├─ Total Orders
│   ├─ Total Revenue
│   ├─ Average Order Value
│   └─ By Period (Daily, Weekly, Monthly)
│
├─ Product Reports
│   ├─ Best Sellers
│   ├─ Least Sellers
│   └─ By Category
│
├─ Customer Reports
│   ├─ Best Customers
│   ├─ New Customers
│   └─ Inactive Customers
│
└─ Geographic Reports
    ├─ Sales by Country
    └─ Sales by City
```

#### 11.2 Third-party Analytics
**Services**: `IWidgetPluginManager`

**Integrations**:
```
├─ Google Analytics
├─ Google Tag Manager (GTM)
├─ Facebook Pixel
├─ Microsoft Clarity
└─ Custom Analytics
```

**Tính năng**:
- Nhúng tracking code vào trang
- Theo dõi user behavior (pageviews, clicks, conversions)
- Hỗ trợ remarketing campaigns

---

### 1️⃣2️⃣ PLUGIN & THEMES

#### 12.1 Plugin System
**Services**: `IPluginManager`, `IPluginService`

**Plugin Types**:
```
├─ Payment Plugin (IPaymentMethod)
├─ Shipping Plugin (IShippingRateComputationMethod)
├─ Tax Plugin (ITaxProvider)
├─ Discount Plugin (IDiscountRequirementRule)
├─ Widget Plugin (IWidgetPlugin)
├─ Authentication Plugin (IExternalAuthenticationMethod)
├─ Multi-factor Auth Plugin (IMultiFactorAuthenticationMethod)
├─ Search Plugin (ISearchProvider)
├─ Export/Import Plugin
└─ Misc Plugin (IMiscPlugin)
```

**Plugin Lifecycle**:
```
Tải Plugin
    ↓
Cài đặt
    ↓
Bật/Tắt
    ↓
Cấu hình
    ↓
Cập nhật
    ↓
Gỡ cài đặt
```

#### 12.2 Theme System
**Services**: `IThemeProvider`

**Theme Features**:
```
├─ Responsive design
├─ Customizable colors
├─ Layout options
├─ Widget areas
└─ Mobile friendly
```

---

### 1️⃣3️⃣ BẢO MẬT (Security)

#### 13.1 Permissions & ACL
**Services**: `IPermissionService`, `IAclService`

**Permission Types**:
```
├─ Manage catalog
├─ Manage orders
├─ Manage customers
├─ Manage discounts
├─ Manage content
├─ Manage settings
└─ Advanced (plugin management, etc.)
```

**Access Control**:
```
Sản phẩm X
├─ Có thể nhìn thấy bởi: Tất cả khách
├─ Có thể mua bởi: Khách hàng đã đăng ký
└─ Hiện giá bởi: VIP customers
```

#### 13.2 Encryption (Mã hóa)
**Services**: `IEncryptionService`

**Dữ liệu mã hóa**:
```
├─ Thông tin thanh toán (credit card)
├─ Mật khẩu
├─ Thông tin nhạy cảm
└─ Khóa bí mật của plugin
```

#### 13.3 Security Settings
**Tính năng**:
```
├─ HTTPS enforcement
├─ Security headers
├─ CSRF protection
├─ XSS protection
├─ SQL injection protection
├─ CAPTCHA for registration
├─ Brute force protection
└─ Session management
```

---

### 1️⃣4️⃣ QUẢN LÝ NỘI DUNG (Content Management)

#### 14.1 Pages & Topics
**Entities**: `Topic`
**Services**: `ITopicService`

**Pages** (trang tĩnh):
```
├─ About Us
├─ Contact Us
├─ Privacy Policy
├─ Terms & Conditions
└─ FAQ
```

#### 14.2 Blogs
**Entities**: `BlogPost`, `BlogComment`
**Services**: `IBlogService`

**Tính năng**:
```
├─ Tạo bài viết blog
├─ Bình luận
├─ Tags
├─ Categories
└─ Publishing dates
```

#### 14.3 Forums (Diễn đàn)
**Entities**: `ForumGroup`, `Forum`, `ForumTopic`, `ForumPost`
**Services**: `IForumService`

**Cấu trúc**:
```
Forum Group (Nhóm diễn đàn)
├─ Forum 1 (Thảo luận sản phẩm)
│   ├─ Topic 1 (iPhone 13 Pro)
│   │   ├─ Post by User A
│   │   ├─ Post by User B
││   └─ Reply by User A
│   └─ Topic 2 (Samsung S21)
├─ Forum 2 (Hỏi đáp)
│   └─ ...
```

#### 14.4 Polls (Khảo sát)
**Entities**: `Poll`, `PollAnswer`, `PollVotingRecord`
**Services**: `IPollService`

**Ví dụ**:
```
Poll: "What's your favorite smartphone brand?"
├─ Apple - 500 votes
├─ Samsung - 350 votes
├─ Xiaomi - 200 votes
└─ Other - 150 votes
```

---

### 1️⃣5️⃣ TÁC VỤ ĐỊNH KỲ (Scheduled Tasks)

#### 15.1 Background Jobs
**Services**: `ITaskScheduler`, `IScheduleTaskRunner`

**Built-in Tasks**:
```
├─ QueuedMessagesSendTask (Gửi email)
├─ DeleteGuestsTask (Xóa guest users cũ)
├─ UpdateExchangeRateTask (Cập nhật tỷ giá)
├─ ClearCacheTask (Xóa cache)
├─ ClearLogTask (Xóa log cũ)
└─ DeleteInactiveCustomersTask (Xóa khách vô hoạt động)
```

**Cấu hình Task**:
```
Task: SendEmails
├─ Enabled: Yes
├─ Run Every: 1 minute
├─ Timeout: 60 seconds
└─ Stop on Error: No
```

---

### 1️⃣6️⃣ LOCALIZATION & INTERNATIONALIZATION

#### 16.1 Multi-language Support
**Entities**: `Language`, `LocaleStringResource`, `LocalizedProperty`
**Services**: `ILocalizationService`

**Tính năng**:
```
├─ Quản lý ngôn ngữ (thêm, xóa, cài đặt)
├─ Dịch thuật tự động (Google Translate API)
├─ Import/Export ngôn ngữ
├─ Per-store languages
└─ Customer language preference
```

**Localization Strategy**:
```
Database Localization
├─ Tất cả string trong database
├─ Dễ cập nhật không cần recompile
└─ Hỗ trợ bất cứ ngôn ngữ nào

Entity Localization
├─ Tên sản phẩm, mô tả, SEO URL
├─ Mỗi ngôn ngữ có record riêng
└─ Quản lý qua admin
```

#### 16.2 Multi-currency Support
**Entities**: `Currency`
**Services**: `ICurrencyService`, `IExchangeRateProvider`

**Tính năng**:
```
├─ Quản lý tiền tệ
├─ Tỷ giá hối đoái real-time
├─ Display prices in customer currency
├─ Exchange rate caching
└─ Rounding options
```

**Exchange Rate Update**:
```
ECB (European Central Bank) Provider
├─ Daily updates
├─ Automatic rate adjustment
└─ Fallback to manual rates
```

#### 16.3 Units of Measure
**Entities**: `MeasureWeight`, `MeasureDimension`
**Services**: `IMeasureService`

**Đơn vị**:
```
Weight:
├─ Kilogram (kg)
├─ Pound (lb)
├─ Gram (g)
└─ Ounce (oz)

Dimension:
├─ Centimeter (cm)
├─ Inch (in)
└─ Meter (m)
```

---

### 1️⃣7️⃣ VENDORS & AFFILIATES

#### 17.1 Vendors (Người bán)
**Entities**: `Vendor`
**Services**: `IVendorService`

**Tính năng**:
```
├─ Vendor registration
├─ Vendor commission
├─ Vendor analytics
├─ Vendor messaging
└─ Vendor approval process
```

**Vendor Dashboard**:
```
├─ My Products
├─ My Orders
├─ Sales Report
├─ Commission
└─ Support Messages
```

#### 17.2 Affiliates
**Entities**: `Affiliate`
**Services**: `IAffiliateService`

**Tính năng**:
```
├─ Affiliate registration
├─ Affiliate code
├─ Commission tracking
├─ Affiliate reports
└─ Payment management
```

**Commission Calculation**:
```
Order Total: 1,000 USD
Commission Rate: 5%
Commission: 50 USD
```

---

### 1️⃣8️⃣ GDPR COMPLIANCE

**Entities**: `GdprConsent`, `GdprLog`, `CustomerPermanentlyDeleted`
**Services**: `IGdprService`

**Tính năng**:
```
├─ Cookie consent management
├─ Personal data request
├─ Right to be forgotten (delete account)
├─ Data portability
└─ Audit trail
```

---

### 1️⃣9️⃣ ARTIFICIAL INTELLIGENCE (AI) - TỪ NĂM 2024

**Services**: `IArtificialIntelligenceService`

**AI Providers**:
```
├─ OpenAI (ChatGPT)
├─ Google (Gemini / Bard)
├─ DeepSeek
└─ Custom providers
```

**Use Cases**:
```
├─ Generate Product Descriptions
│   Input: Product attributes, category
│   Output: SEO-friendly description
│
├─ Generate Meta Tags
│   Input: Product name, description
│   Output: Meta title, meta description, keywords
│
├─ Product Image Generation
│   Input: Product text description
│   Output: AI-generated product images
│
├─ Customer Support (Chatbot)
│   Input: Customer question
│   Output: Answer
│
└─ Product Recommendations
    Input: Customer browsing history
    Output: Recommended products
```

---

## 🔄 Quy trình mua hàng Chi tiết

### Sơ đồ luồng thanh lý

```
┌─────────────────────────────────────────────────────────────┐
│ 1️⃣ KHÁM PHÁ SẢN PHẨM (Discovery)          │
├─────────────────────────────────────────────────────────────┤
│ • Search by keyword, category, brand    │
│ • Filter by price range, attributes, ratings              │
│ • Sort by relevance, price, popularity, newest             │
│ • View product details, images, videos, reviews          │
│ • Compare products           │
│ • Add to wishlist          │
└─────────────────────────────────────────────────────────────┘
          ↓
┌─────────────────────────────────────────────────────────────┐
│ 2️⃣ CHỌN SẢN PHẨM (Product Selection)       │
├─────────────────────────────────────────────────────────────┤
│ • View product details        │
│ • Select attributes (size, color, etc.)     │
│ • Select quantity          │
│ • Check stock status       │
│ • View delivery timeframe      │
│ • Add to cart     │
└─────────────────────────────────────────────────────────────┘
   ↓
┌─────────────────────────────────────────────────────────────┐
│ 3️⃣ XEM GIỎ HÀNG (Shopping Cart Review)        │
├─────────────────────────────────────────────────────────────┤
│ • Review cart items, quantities, prices          │
│ • Update quantities, remove items                  │
│ • Apply coupon code (discount validation) │
│   └─ Check minimum order value                    │
│   └─ Check applicable products/categories    │
│   └─ Check customer eligibility    │
│   └─ Calculate discount amount             │
│ • Proceed to checkout │
└─────────────────────────────────────────────────────────────┘
   ↓
┌─────────────────────────────────────────────────────────────┐
│ 4️⃣ THÔNG TIN GIAO HÀNG (Shipping Information)              │
├─────────────────────────────────────────────────────────────┤
│ • Enter/select billing address          │
│ • Enter/select shipping address     │
│ • Select shipping method  │
│   └─ Get available shipping options     │
│   └─ Calculate shipping fee based on:     │
│      ├─ Shipping address (zone)            │
│    ├─ Product weight & dimensions   │
│      ├─ Cart subtotal       │
│      └─ Selected method rate rules        │
│ • View estimated delivery date     │
└─────────────────────────────────────────────────────────────┘
         ↓
┌─────────────────────────────────────────────────────────────┐
│ 5️⃣ PHƯƠNG THỨC THANH TOÁN (Payment Method)       │
├─────────────────────────────────────────────────────────────┤
│ • Select payment method              │
│   ├─ Credit/Debit Card        │
│   ├─ PayPal           │
│   ├─ Bank Transfer        │
│   ├─ Cash on Delivery           │
│   └─ Other gateways (via plugins)         │
│ • Enter payment details        │
│ • Optional: Save payment method for future         │
└─────────────────────────────────────────────────────────────┘
   ↓
┌─────────────────────────────────────────────────────────────┐
│ 6️⃣ THUỘC TÍNH CHECKOUT (Checkout Attributes)     │
├─────────────────────────────────────────────────────────────┤
│ • Gift wrapping? (Yes/No)    │
│ • Gift message? [Text area]        │
│ • Special instructions? [Text area]            │
│ • Opt-in newsletter? (Checkbox) │
└─────────────────────────────────────────────────────────────┘
         ↓
┌─────────────────────────────────────────────────────────────┐
│ 7️⃣ XEM LẠI ĐƠN HÀNG (Order Review)     │
├─────────────────────────────────────────────────────────────┤
│ Order Summary:        │
│  │
│ Items:     │
│  ├─ iPhone 13 Pro × 1 @ $999 = $999 │
│  └─ AirPods Pro × 2 @ $249 = $498     │
│ │
│ Subtotal:        $1,497.00            │
│ + Shipping (Express):   $   50.00     │
│ + Tax (10%):     $  154.70      │
│ - Discount (SAVE20):    $ (200.00)│
│ ─────────────────────────────    │
│ = TOTAL:            $1,501.70 │
│      │
│ [Confirm Order]        │
└─────────────────────────────────────────────────────────────┘
      ↓
┌─────────────────────────────────────────────────────────────┐
│ 8️⃣ THANH TOÁN (Payment Processing)    │
├─────────────────────────────────────────────────────────────┤
│ System:          │
│ • Validate cart items & prices         │
│ • Validate discount codes             │
│ • Validate shipping method                  │
│ • Call IPaymentMethod.ProcessPayment()            │
│   └─ Send request to payment gateway        │
│   └─ Gateway validates & authorizes         │
│   └─ Receive authorization code      │
│ • Check payment result         │
│   ├─ Success → Continue to order creation         │
│   └─ Failed → Show error, retry   │
└─────────────────────────────────────────────────────────────┘
            ↓
┌─────────────────────────────────────────────────────────────┐
│ 9️⃣ TẠO ĐƠN HÀNG (Order Creation)              │
├─────────────────────────────────────────────────────────────┤
│ System:       │
│ • Create Order record │
│ • Create OrderItems         │
│ • Update Stock (decrease inventory)    │
│ • Clear shopping cart │
│ • Update customer reward points   │
│ • Add order notes             │
│ • Set order status = "Pending"            │
│ • Set payment status = "Pending/Paid"     │
│ • Record customer purchase        │
└─────────────────────────────────────────────────────────────┘
      ↓
┌─────────────────────────────────────────────────────────────┐
│ 🔟 XÁC NHẬN ĐẶT HÀNG (Order Confirmation)       │
├─────────────────────────────────────────────────────────────┤
│ • Show order confirmation page    │
│ • Display order number, total, shipping address    │
│ • Send confirmation email│
│   └─ Template: OrderPlacedTokensAddedEvent          │
│└─ Tokens: {{Order.OrderNumber}}, {{Order.Total}}, etc. │
│ • Optional: Create shipment       │
│ • Log activity in activity log     │
└─────────────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────────────┐
│ 1️⃣1️⃣ XỬ LÝ VẬN CHUYỂN (Shipping Processing)   │
├─────────────────────────────────────────────────────────────┤
│ Admin:             │
│ • Review order    │
│ • Prepare shipment (pick items from warehouse)          │
│ • Generate shipping label           │
│ • Get tracking number from carrier              │
│ • Create Shipment record in system              │
│ • Update order status = "Shipped"  │
│ • Mark shipment as "Shipped"           │
│ • Add tracking number to shipment      │
│ • Send shipping notification email          │
│   └─ Include tracking URL so customer can track           │
└─────────────────────────────────────────────────────────────┘
       ↓
┌─────────────────────────────────────────────────────────────┐
│ 1️⃣2️⃣ GIAO HÀNG (Delivery)│
├─────────────────────────────────────────────────────────────┤
│ Carrier:             │
│ • Transport package to destination             │
│ • Update tracking status (in transit, out for delivery)   │
│ • Deliver to customer         │
││
│ System:    │
│ • (Optional) Auto-update shipment status from carrier API │
│ • Mark shipment as "Delivered" when confirmed │
│ • Update order status = "Complete"    │
│ • Send delivery confirmation email   │
└─────────────────────────────────────────────────────────────┘
      ↓
┌─────────────────────────────────────────────────────────────┐
│ 1️⃣3️⃣ SAU GIAO HÀNG (Post-Delivery)            │
├─────────────────────────────────────────────────────────────┤
│ Customer:        │
│ • Review product             │
│ • Write review (rating + comment)             │
│ • (Optional) Return request if dissatisfied       │
│   ├─ Select reason               │
│   ├─ Add comment    │
│   └─ Submit return request       │
│     │
│ System:    │
│ • Create ProductReview record   │
│ • Calculate and update product rating            │
│ • Update customer reward points   │
│ • Process return if requested       │
│   ├─ Create ReturnRequest            │
│   ├─ Admin approves/rejects    │
│   ├─ If approved: Issue RMA (Return Merchandise Auth.)    │
│   ├─ Customer ships back       │
│   ├─ Admin receives & inspects      │
│   ├─ Issue refund if approved        │
│   └─ Update inventory         │
└─────────────────────────────────────────────────────────────┘
```

---

## 💻 Công nghệ sử dụng

### Backend
- **.NET 9** - Framework chính
- **ASP.NET Core** - Web framework
- **Entity Framework Core** - ORM
- **SQL Server** - Database chính (hỗ trợ PostgreSQL, MySQL)
- **Razor Pages** - Frontend MVC

### Frontend
- **HTML5, CSS3** - Markup & styling
- **JavaScript, jQuery** - Interactivity
- **Bootstrap** - Responsive design
- **Vue.js / React** (optional) - Advanced UIs

### Infrastructure
- **Azure** (tùy chọn) - Cloud hosting
- **Redis** - Caching
- **NuGet** - Package manager
- **Docker** - Containerization

### Security
- **HTTPS** - Encrypted communication
- **Password Hashing** - PBKDF2
- **Data Encryption** - AES
- **CORS** - Cross-origin requests
- **CSRF Protection** - Token-based

---

## 🎯 Tính năng nổi bật

### 1. Kiến trúc Plugin linh hoạt
Cho phép mở rộng tính năng mà không sửa code chính

### 2. Multi-Store support
Quản lý nhiều cửa hàng từ một admin

### 3. Pricing Engine nâng cao
- Tier prices
- Bulk discounts
- Dynamic pricing
- Tax calculation
- Shipping rates

### 4. SEO-friendly
- Clean URLs
- Meta tags
- Structured data (JSON-LD)
- Sitemap generation

### 5. Performance optimized
- Caching (in-memory, Redis, distributed)
- Database indexing
- Image optimization
- CDN support

### 6. Security first
- HTTPS enforcement
- Input validation
- XSS/CSRF protection
- Secure password handling
- Audit logging

### 7. Mobile responsive
- Responsive design
- Mobile app support (via API)
- Fast loading
- Touch-friendly UI

### 8. Comprehensive reporting
- Sales analytics
- Customer insights
- Product performance
- Revenue tracking

---

## 📚 Thêm thông tin

Để hiểu sâu hơn về từng module, hãy tìm kiếm trong:

```
Libraries/Nop.Services/
├── Orders/         # Xử lý đơn hàng
├── Payments/         # Thanh toán
├── Shipping/    # Vận chuyển
├── Tax/          # Thuế
├── Catalog/  # Sản phẩm
├── Customers/  # Khách hàng
├── Discounts/        # Khuyến mãi
├── Common/        # Chức năng chung
└── ...
```