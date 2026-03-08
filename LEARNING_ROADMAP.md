# ?? L? Trình H?c T?p & N?m V?ng nopCommerce v5.00

## ?? M?c L?c

- [1. Gi?i Thi?u D? Án](#1-gi?i-thi?u-d?-án)
- [2. Chu?n B? Ban ??u](#2-chu?n-b?-ban-??u)
- [3. Giai ?o?n 1: N?m Ki?n Th?c N?n T?ng](#3-giai-?o?n-1-n?m-ki?n-th?c-n?n-t?ng)
- [4. Giai ?o?n 2: Tìm Hi?u L?p Core](#4-giai-?o?n-2-tìm-hi?u-l?p-core)
- [5. Giai ?o?n 3: Tìm Hi?u L?p Data Access](#5-giai-?o?n-3-tìm-hi?u-l?p-data-access)
- [6. Giai ?o?n 4: Tìm Hi?u L?p Services](#6-giai-?o?n-4-tìm-hi?u-l?p-services)
- [7. Giai ?o?n 5: Tìm Hi?u L?p Presentation](#7-giai-?o?n-5-tìm-hi?u-l?p-presentation)
- [8. Giai ?o?n 6: Hi?u Plugin System](#8-giai-?o?n-6-hi?u-plugin-system)
- [9. Giai ?o?n 7: Th?c Hành Th?c T?](#9-giai-?o?n-7-th?c-hành-th?c-t?)
- [10. Timeline & Checklist](#10-timeline--checklist)
- [11. Tài Li?u Tham Kh?o](#11-tài-li?u-tham-kh?o)

---

## 1. Gi?i Thi?u D? Án

### 1.1 Thông Tin C? B?n
- **Tên D? Án:** nopCommerce
- **Phiên B?n:** 5.00
- **Framework:** .NET 9 + ASP.NET Core
- **Lo?i ?ng D?ng:** E-Commerce Platform (N?n t?ng th??ng m?i ?i?n t?)
- **License:** nopCommerce License
- **Repository:** https://github.com/nopSolutions/nopCommerce
- **Branch Hi?n T?i:** develop

### 1.2 M?c ?ích D? Án
nopCommerce là m?t n?n t?ng th??ng m?i ?i?n t? **hoàn toàn mã ngu?n m?**, cho phép:
- Xây d?ng c?a hàng online m?nh m?
- Qu?n lý s?n ph?m, danh m?c, ??n hàng
- H? tr? thanh toán, v?n chuy?n, thu?
- Tích h?p v?i các d?ch v? bên th? ba
- M? r?ng ch?c n?ng thông qua plugin system

### 1.3 ??c ?i?m Chính
? **Modular & Extensible** - Plugin system m?nh m?  
? **Multi-Database** - SQL Server, MySQL, PostgreSQL  
? **Multi-Language & Multi-Currency** - H? tr? ?a ngôn ng?  
? **Modern Stack** - .NET 9, Razor Pages, Entity Framework  
? **Production Ready** - ?ã tri?n khai trên hàng ngàn trang web  

---

## 2. Chu?n B? Ban ??u

### 2.1 Yêu C?u H? Th?ng
```
? .NET 9 SDK (ho?c cao h?n)
? Visual Studio 2022 (ho?c VS Code)
? SQL Server Express / LocalDB
? Git
? Node.js (optional - cho asset building)
```

### 2.2 C?u Trúc Th? M?c Workspace

```
D:\nop-commerce\nopCommerce\
?
??? src/      # ?? Th? m?c chính
?   ??? Libraries/              # ?? Core libraries
?   ?   ??? Nop.Core/      # Domain & Infrastructure
? ?   ??? Nop.Data/       # Data Access Layer
?   ?   ??? Nop.Services/         # Business Logic Layer
??
?   ??? Presentation/  # ?? Presentation Layer
?   ?   ??? Nop.Web/   # Main Web Application
?   ?   ??? Nop.Web.Framework/  # Framework & Shared Components
?   ?
? ??? Plugins/                 # ?? Plugin System
?   ? ??? Nop.Plugin.Payments.*/  # Payment plugins
?   ?   ??? Nop.Plugin.Shipping.*/    # Shipping plugins
?   ?   ??? Nop.Plugin.Tax.*/         # Tax plugins
?   ?   ??? Nop.Plugin.Widgets.*/     # Widget plugins
?   ?   ??? Nop.Plugin.Misc.*/        # Miscellaneous plugins
?   ?   ??? ...
?   ?
?   ??? Tests/ # ?? Test Projects
?       ??? Nop.Tests/
?
??? Build/        # ?? Build scripts
??? Samples/    # ?? Sample code
??? LEARNING_ROADMAP.md              # ?? Tài li?u này
```

### 2.3 Cài ??t & Ch?y D? Án

```bash
# 1. Clone d? án (ho?c ?ã có s?n)
cd D:\nop-commerce\nopCommerce

# 2. Restore packages
dotnet restore

# 3. Build solution
dotnet build

# 4. Ch?y web application
cd src/Presentation/Nop.Web
dotnet run

# 5. M? browser
# http://localhost:5000 - Public store
# http://localhost:5000/admin - Admin area
```

---

## 3. Giai ?o?n 1: N?m Ki?n Th?c N?n T?ng

**?? Th?i Gian: 1-2 tu?n**

### 3.1 Các Khái Ni?m C?n Hi?u

#### A. Clean Architecture
```
???????????????????????????????????????
?  Presentation Layer (UI)      ?
?  - Razor Pages, Controllers      ?
???????????????????????????????????????
?  Application Layer (Services)        ?
?  - Business Logic                    ?
???????????????????????????????????????
?  Domain Layer (Entities)        ?
?  - Business Rules        ?
???????????????????????????????????????
?  Infrastructure Layer (Data Access)  ?
?  - Database, APIs           ?
???????????????????????????????????????
```

**Tài li?u ??c:**
- [ ] `src/Presentation/Nop.Web/Program.cs` - Application startup
- [ ] `src/Presentation/Nop.Web.Framework/Infrastructure/` - Startup extensions
- [ ] `src/Libraries/Nop.Core/Domain/BaseEntity.cs` - Base entity class

#### B. Dependency Injection (DI)
nopCommerce s? d?ng **Autofac** ho?c **Default DI Container** ?? qu?n lý ph? thu?c.

```csharp
// Program.cs - C?u hình DI
if (useAutofac)
    builder.Host.UseServiceProviderFactory(new AutofacServiceProviderFactory());
```

**Tài li?u ??c:**
- [ ] `src/Presentation/Nop.Web.Framework/Infrastructure/Extensions/` - DI setup

#### C. Plugin Architecture
nopCommerce có th? m? r?ng ch?c n?ng qua plugin system.

```
Nop.Plugin.Payments.PayPal/
??? plugin.json       ? Metadata
??? PayPalPlugin.cs  ? Main class
??? Startup.cs            ? DI registration
??? Models/, Views/, etc.
```

**Tài li?u ??c:**
- [ ] `src/Plugins/*/plugin.json` - Plugin configuration
- [ ] `src/Libraries/Nop.Core/Infrastructure/Plugins/` - Plugin interfaces

#### D. SOLID Principles
- **S**ingle Responsibility Principle
- **O**pen/Closed Principle
- **L**iskov Substitution Principle
- **I**nterface Segregation Principle
- **D**ependency Inversion Principle

### 3.2 Bài T?p Giai ?o?n 1

#### Bài 1: Cài ??t & Ch?y D? Án
- [ ] Clone ho?c pull d? án t? GitHub
- [ ] Restore packages: `dotnet restore`
- [ ] Build solution: `dotnet build`
- [ ] Ch?y ?ng d?ng: `cd src/Presentation/Nop.Web && dotnet run`
- [ ] Truy c?p: http://localhost:5000

#### Bài 2: Khám Phá C?u Trúc
- [ ] M? solution trong Visual Studio
- [ ] Xem th? t? các projects (Core ? Data ? Services ? Web)
- [ ] V? dependency diagram
- [ ] Li?t kê 10 services chính

#### Bài 3: Hi?u Startup Process
- [ ] ??t breakpoint ? `Program.Main()`
- [ ] Theo dõi quá trình initialization
- [ ] Ghi chú các b??c chính
- [ ] Tìm n?i DI ???c c?u hình

**Ghi Chú:**
```
Startup flow:
1. CreateBuilder()
2. Configuration (appsettings.json)
3. Services configuration
4. Application pipeline setup
5. App.Run()
```

---

## 4. Giai ?o?n 2: Tìm Hi?u L?p Core

**?? Th?i Gian: 2-3 tu?n**

### 4.1 Nop.Core - Domain Layer

?ây là **trái tim** c?a d? án, ch?a t?t c? các domain entities và interfaces.

#### C?u Trúc Th? M?c
```
Nop.Core/
??? Domain/
?   ??? BaseEntity.cs ? Base class cho t?t c? entities
?   ??? Customers/
?   ?   ??? Customer.cs       ? Entity: Khách hàng
?   ?   ??? CustomerRole.cs          ? Entity: Vai trò khách hàng
?   ? ??? Address.cs        ? Entity: ??a ch?
?   ??? Orders/
?   ?   ??? Order.cs        ? Entity: ??n hàng
??   ??? OrderItem.cs ? Entity: Chi ti?t ??n hàng
?   ?   ??? OrderNote.cs       ? Entity: Ghi chú ??n hàng
?   ??? Catalog/
?   ?   ??? Product.cs     ? Entity: S?n ph?m
?   ?   ??? Category.cs     ? Entity: Danh m?c
?   ?   ??? ProductAttribute.cs      ? Entity: Thu?c tính s?n ph?m
?   ?   ??? ...
?   ??? Discounts/
?   ?   ??? Discount.cs              ? Entity: Chi?t kh?u
?   ?   ??? ...
?   ??? [Các domain khác]/
?
??? Caching/
?   ??? CacheKey.cs     ? Cache key definitions
?   ??? IStaticCacheManager.cs     ? Static cache interface
?   ??? IDistributedCacheManager.cs  ? Distributed cache interface
?
??? Events/
?   ??? IConsumer.cs     ? Event consumer interface
?   ??? [Entity]Events.cs  ? Event definitions
?   ??? ...
?
??? Infrastructure/
?   ??? DependencyRegistrar.cs ? DI registration
?   ??? Plugins/
?   ?   ??? IPlugin.cs         ? Plugin base interface
?   ?   ??? IPaymentMethod.cs        ? Payment method interface
?   ? ??? IShippingRateComputationMethod.cs
?   ?   ??? ...
?   ??? ...
?
??? Security/
?   ??? EncryptionService.cs         ? Encryption utilities
?   ??? ...
?
??? Http/
    ??? HttpClient.cs   ? HTTP client utilities
    ??? ...
```

#### 4.2 Entities Chính C?n Hi?u

| Entity | V? Trí | Mô T? |
|---|---|---|
| **BaseEntity** | `Domain/BaseEntity.cs` | Base class - t?t c? entity k? th?a t? ?ây |
| **Customer** | `Domain/Customers/Customer.cs` | Khách hàng |
| **Order** | `Domain/Orders/Order.cs` | ??n hàng |
| **Product** | `Domain/Catalog/Product.cs` | S?n ph?m |
| **Category** | `Domain/Catalog/Category.cs` | Danh m?c |
| **Discount** | `Domain/Discounts/Discount.cs` | Chi?t kh?u/Coupon |
| **ShoppingCartItem** | `Domain/Orders/ShoppingCartItem.cs` | M?c trong gi? hàng |

#### 4.3 Tìm Hi?u BaseEntity

```csharp
// T?t c? entities k? th?a t? BaseEntity
public abstract class BaseEntity
{
    public int Id { get; set; }  // Primary key
}

// Ví d?:
public class Product : BaseEntity
{
    public string Name { get; set; }
    public string Description { get; set; }
    public decimal Price { get; set; }
    // ...
}
```

**Tài li?u ??c:**
- [ ] `src/Libraries/Nop.Core/Domain/BaseEntity.cs`
- [ ] M?t vài entity ví d? (Product, Customer, Order)

#### 4.4 Hi?u Caching System

nopCommerce h? tr? **2 lo?i caching:**

```csharp
// 1. Static Cache - L?u trong memory
IStaticCacheManager.GetAsync<Product>(CacheKey.Products(id));

// 2. Distributed Cache - L?u trong Redis ho?c SQL Server
IDistributedCacheManager.GetAsync<List<Product>>(cacheKey);
```

**Tài li?u ??c:**
- [ ] `src/Libraries/Nop.Core/Caching/`

#### 4.5 Event System

nopCommerce s? d?ng event-driven architecture:

```csharp
// Publish event
await _eventPublisher.PublishAsync(new ProductInsertedEvent { Product = product });

// Subscribe to event
public class ProductInsertedEventConsumer : IConsumer<ProductInsertedEvent>
{
    public async Task HandleEventAsync(ProductInsertedEvent eventMessage)
    {
        // Handle event
    }
}
```

**Tài li?u ??c:**
- [ ] `src/Libraries/Nop.Core/Events/`

### 4.6 Bài T?p Giai ?o?n 2

#### Bài 1: Khám Phá Entities
- [ ] Li?t kê t?t c? entities chính (ít nh?t 15 cái)
- [ ] V? Entity Relationship Diagram (ERD)
- [ ] Xác ??nh các relationships (1:1, 1:N, M:N)

#### Bài 2: Hi?u BaseEntity
- [ ] Tìm `BaseEntity.cs`
- [ ] Xem có gì trong base class
- [ ] Tìm 3 entity k? th?a t? BaseEntity
- [ ] Vi?t tóm t?t v? BaseEntity

#### Bài 3: Tìm Hi?u Cache Keys
- [ ] Tìm file `CacheKey.cs`
- [ ] Li?t kê t?t c? cache key definitions
- [ ] Ghi chú nh?ng cache key quan tr?ng nh?t

#### Bài 4: Event System
- [ ] Tìm vài event definitions
- [ ] Tìm vài event consumers
- [ ] V? s? ?? event flow (vd: Product inserted event)

---

## 5. Giai ?o?n 3: Tìm Hi?u L?p Data Access

**?? Th?i Gian: 2-3 tu?n**

### 5.1 Nop.Data - Data Access Layer

L?p này ch?u trách nhi?m truy c?p d? li?u t? database.

#### C?u Trúc Th? M?c

```
Nop.Data/
??? Configuration/
?   ??? CustomerConfiguration.cs     ? Fluent mapping cho Customer entity
?   ??? ProductConfiguration.cs      ? Fluent mapping cho Product entity
?   ??? [Entity]Configuration.cs
?
??? Migrations/
?   ??? 2024_01_[name].cs       ? Migration files
?   ??? ...
?
??? Mapping/
?   ??? [Entities mapping files]/
?   ??? ...
?
??? Repository/
?   ??? IRepository.cs           ? Generic repository interface
?   ??? Repository.cs     ? Generic repository implementation
?   ??? ...
?
??? Context/
    ??? NopDbContext.cs             ? EF Core DbContext
    ??? [Database providers]/
    ??? ...
```

### 5.2 Repository Pattern

```csharp
// Generic Repository Interface
public interface IRepository<T> where T : BaseEntity
{
    Task<T> GetByIdAsync(object id);
    Task<List<T>> GetAllAsync();
    Task<IPagedList<T>> GetAllPagedAsync(...);
    Task InsertAsync(T entity);
    Task UpdateAsync(T entity);
  Task DeleteAsync(T entity);
}

// Usage
public class ProductService : IProductService
{
    private readonly IRepository<Product> _productRepository;
    
    public async Task<Product> GetProductByIdAsync(int id)
    {
        return await _productRepository.GetByIdAsync(id);
    }
}
```

**Tài li?u ??c:**
- [ ] `src/Libraries/Nop.Data/Repository/IRepository.cs`
- [ ] `src/Libraries/Nop.Data/Repository/Repository.cs`

### 5.3 Entity Framework Core & FluentMigrator

#### Database Mapping (Fluent API)

```csharp
// Nop.Data/Configuration/CustomerConfiguration.cs
public class CustomerConfiguration : IEntityTypeConfiguration<Customer>
{
    public void Configure(EntityTypeBuilder<Customer> builder)
 {
        builder.ToTable(NopMappingDefaults.CustomerTableName);
        builder.HasKey(customer => customer.Id);
        
        builder.Property(customer => customer.Email)
            .IsRequired()
            .HasMaxLength(255);
        
   builder.HasMany(c => c.Addresses)
         .WithOne(a => a.Customer)
            .HasForeignKey(a => a.CustomerId)
        .OnDelete(DeleteBehavior.Cascade);
    }
}
```

**Tài li?u ??c:**
- [ ] `src/Libraries/Nop.Data/Configuration/` - Các entity configurations

#### Database Migrations (FluentMigrator)

```csharp
// Migration file
[Migration(202401010000)]
public class AddNewColumnToProductTable : Migration
{
    public override void Up()
    {
        Create.Column("NewColumn")
      .OnTable("Product")
   .AsString(255)
            .Nullable();
    }
    
    public override void Down()
    {
        Delete.Column("NewColumn")
            .FromTable("Product");
    }
}
```

**Tài li?u ??c:**
- [ ] `src/Libraries/Nop.Data/Migrations/` - Migration examples

### 5.4 H? Tr? Multiple Database

nopCommerce h? tr? 3 database chính:

| Database | Package | C?u Hình |
|---|---|---|
| **SQL Server** | `Microsoft.Data.SqlClient` | `appsettings.json` |
| **MySQL** | `MySqlConnector` | `appsettings.json` |
| **PostgreSQL** | `Npgsql` | `appsettings.json` |

```json
// appsettings.json - Database configuration
{
  "Data": {
    "DataProvider": "sqlserver",
    "ConnectionString": "Server=localhost;Database=nopCommerce;..."
  }
}
```

### 5.5 Bài T?p Giai ?o?n 3

#### Bài 1: Hi?u Repository Pattern
- [ ] Tìm `IRepository<T>` interface
- [ ] Tìm `Repository<T>` implementation
- [ ] Li?t kê t?t c? methods
- [ ] Vi?t ví d? s? d?ng repository

#### Bài 2: Explore Entity Configurations
- [ ] Tìm `CustomerConfiguration.cs`
- [ ] Tìm `ProductConfiguration.cs`
- [ ] Hi?u cách fluent API ???c s? d?ng
- [ ] Ghi chú các entity relationships

#### Bài 3: Database Migrations
- [ ] Tìm migrations folder
- [ ] Li?t kê 5 migration files g?n ?ây
- [ ] Hi?u c?u trúc migration
- [ ] Vi?t migration ??n gi?n (t?o table m?i ho?c thêm column)

#### Bài 4: Database Configuration
- [ ] Tìm `appsettings.json`
- [ ] Xem database configuration
- [ ] Th? ??i connection string
- [ ] Th? restore database

---

## 6. Giai ?o?n 4: Tìm Hi?u L?p Services

**?? Th?i Gian: 3 tu?n**

### 6.1 Nop.Services - Business Logic Layer

?ây là **Business Logic Layer (BAL)**, n?i t?t c? business logic ???c tri?n khai.

#### C?u Trúc Th? M?c

```
Nop.Services/
??? Catalog/
?   ??? IProductService.cs? Service interface
?   ??? ProductService.cs            ? Service implementation
?   ??? ICategoryService.cs
?   ??? CategoryService.cs
?   ??? IProductAttributeService.cs
?   ??? ...
?
??? Orders/
?   ??? IOrderService.cs
?   ??? OrderService.cs
?   ??? IShoppingCartService.cs
? ??? ShoppingCartService.cs
?   ??? ...
?
??? Customers/
?   ??? ICustomerService.cs
?   ??? CustomerService.cs
? ??? ICustomerAttributeService.cs
?   ??? ...
?
??? Payments/
?   ??? IPaymentService.cs
?   ??? PaymentService.cs
?   ??? ...
?
??? Shipping/
?   ??? IShippingService.cs
?   ??? ShippingService.cs
?   ??? ...
?
??? Tax/
?   ??? ITaxService.cs
?   ??? TaxService.cs
?   ??? ...
?
??? Discounts/
?   ??? IDiscountService.cs
?   ??? DiscountService.cs
?   ??? ...
?
??? Messages/
?   ??? IEmailSender.cs
?   ??? EmailSender.cs
?   ??? IMessageTokenProvider.cs
?   ??? ...
?
??? Localization/
?   ??? ILocalizationService.cs
?   ??? LocalizationService.cs
?   ??? ...
?
??? Installation/
?   ??? IInstallationService.cs
?   ??? ...
?
??? [Các services khác]/
```

### 6.2 Service Pattern

#### Basic Service Structure

```csharp
// IProductService.cs - Interface definition
public interface IProductService
{
    Task<Product> GetProductByIdAsync(int productId);
    Task<List<Product>> GetProductsByCategoryAsync(int categoryId);
 Task InsertProductAsync(Product product);
    Task UpdateProductAsync(Product product);
    Task DeleteProductAsync(Product product);
}

// ProductService.cs - Implementation
public class ProductService : IProductService
{
    private readonly IRepository<Product> _productRepository;
    private readonly IEventPublisher _eventPublisher;
    private readonly IStaticCacheManager _cacheManager;
    
    public ProductService(
        IRepository<Product> productRepository,
        IEventPublisher eventPublisher,
  IStaticCacheManager cacheManager)
    {
        _productRepository = productRepository;
     _eventPublisher = eventPublisher;
     _cacheManager = cacheManager;
    }
    
    public async Task<Product> GetProductByIdAsync(int productId)
    {
 var cacheKey = CacheKey.Products(productId);
      
        return await _cacheManager.GetAsync(cacheKey, async () =>
        {
   return await _productRepository.GetByIdAsync(productId);
   });
}
    
    public async Task InsertProductAsync(Product product)
    {
        ArgumentNullException.ThrowIfNull(product);
        
        await _productRepository.InsertAsync(product);
        
      // Publish event
        await _eventPublisher.PublishAsync(new ProductInsertedEvent { Product = product });
        
  // Clear cache
    await _cacheManager.RemoveByPrefixAsync(CacheKey.Products_Prefix);
    }
}
```

**Tài li?u ??c:**
- [ ] `src/Libraries/Nop.Services/Catalog/IProductService.cs`
- [ ] `src/Libraries/Nop.Services/Catalog/ProductService.cs`

### 6.3 Services Chính C?n Hi?u

| Service | V? Trí | Ch?c N?ng |
|---|---|---|
| **IProductService** | `Catalog/` | Qu?n lý s?n ph?m |
| **ICategoryService** | `Catalog/` | Qu?n lý danh m?c |
| **IOrderService** | `Orders/` | Qu?n lý ??n hàng |
| **IShoppingCartService** | `Orders/` | Qu?n lý gi? hàng |
| **ICustomerService** | `Customers/` | Qu?n lý khách hàng |
| **IPaymentService** | `Payments/` | X? lý thanh toán |
| **IShippingService** | `Shipping/` | Tính phí v?n chuy?n |
| **ITaxService** | `Tax/` | Tính thu? |
| **IDiscountService** | `Discounts/` | Qu?n lý khuy?n mãi |
| **ILocalizationService** | `Localization/` | ?a ngôn ng? |
| **IEmailSender** | `Messages/` | G?i email |

### 6.4 Patterns & Best Practices

#### A. Dependency Injection in Services

```csharp
// Constructor injection
public class OrderService : IOrderService
{
    private readonly IRepository<Order> _orderRepository;
    private readonly IProductService _productService;
    private readonly IEventPublisher _eventPublisher;
  
    public OrderService(
        IRepository<Order> orderRepository,
      IProductService productService,
        IEventPublisher eventPublisher)
    {
        _orderRepository = orderRepository;
        _productService = productService;
        _eventPublisher = eventPublisher;
    }
}
```

#### B. Caching in Services

```csharp
// Get with caching
var product = await _cacheManager.GetAsync(cacheKey, async () =>
{
    return await _productRepository.GetByIdAsync(id);
});

// Invalidate cache
await _cacheManager.RemoveAsync(cacheKey);
await _cacheManager.RemoveByPrefixAsync(CacheKey.Products_Prefix);
```

#### C. Event Publishing

```csharp
// Publish event
await _eventPublisher.PublishAsync(new OrderInsertedEvent { Order = order });

// Handle event
public class OrderInsertedEventConsumer : IConsumer<OrderInsertedEvent>
{
    public async Task HandleEventAsync(OrderInsertedEvent eventMessage)
    {
        var order = eventMessage.Order;
        // Process order...
    }
}
```

### 6.5 Bài T?p Giai ?o?n 4

#### Bài 1: Khám Phá Services
- [ ] Li?t kê 15+ services chính
- [ ] V? diagram c?a service dependencies
- [ ] Ghi chú các service quan tr?ng nh?t

#### Bài 2: Hi?u Service Pattern
- [ ] Tìm `IProductService.cs`
- [ ] Tìm `ProductService.cs`
- [ ] V? s? ?? class cho ProductService
- [ ] Li?t kê t?t c? dependencies

#### Bài 3: Caching & Events
- [ ] Tìm n?i cache ???c s? d?ng trong services
- [ ] Tìm n?i events ???c publish
- [ ] Vi?t s? ?? v? cache invalidation
- [ ] Vi?t s? ?? v? event flow

#### Bài 4: Write Simple Service
- [ ] T?o interface m?i: `IMyCustomService`
- [ ] T?o implementation: `MyCustomService`
- [ ] Register trong DI container
- [ ] Vi?t unit test ??n gi?n

---

## 7. Giai ?o?n 5: Tìm Hi?u L?p Presentation

**?? Th?i Gian: 2-3 tu?n**

### 7.1 Nop.Web.Framework - Framework Layer

?ây là **class library** ch?a common components cho t?t c? presentation projects.

#### C?u Trúc Th? M?c

```
Nop.Web.Framework/
??? Models/
?   ??? BaseModel.cs ? Base model cho t?t c? models
?   ??? BasePageModel.cs             ? Base model cho Razor Pages
?   ??? BaseAdminModel.cs            ? Base model cho admin area
?   ??? ...
?
??? Controllers/
?   ??? BaseController.cs            ? Base controller
?   ??? BaseAdminController.cs       ? Base admin controller
? ??? ...
?
??? Mvc/
?   ??? Filters/           ? Action filters
?   ??? Attributes/           ? Custom attributes
?   ?   ??? HttpsRequirementAttribute.cs
?   ?   ??? AclResourceAttribute.cs
?   ?   ??? ...
?   ??? ...
?
??? Security/
?   ??? IPermissionProvider.cs       ? Permission interface
?   ??? StandardPermissionProvider.cs
?   ??? ...
?
??? Localization/
?   ??? ILocalizationProvider.cs
?   ??? Enums/
?   ??? ...
?
??? Infrastructure/
?   ??? Extensions/
?   ?   ??? ApplicationBuilderExtensions.cs
?   ?   ??? ServiceCollectionExtensions.cs
?   ?   ??? ...
?   ??? ...
?
??? TagHelpers/
?   ??? [Custom tag helpers]/
?   ??? ...
?
??? Views/
    ??? Shared/
    ?   ??? _Layout.cshtml           ? Master layout
    ?   ??? Components/              ? View components
    ?   ??? ...
    ??? ...
```

**Tài li?u ??c:**
- [ ] `src/Presentation/Nop.Web.Framework/Models/BaseModel.cs`
- [ ] `src/Presentation/Nop.Web.Framework/Controllers/BaseController.cs`

### 7.2 Nop.Web - Main Web Application

#### C?u Trúc Th? M?c

```
Nop.Web/
??? Pages/     ? RAZOR PAGES (Public Store)
?   ??? Cart/
?   ?   ??? Index.cshtml             ? Cart page
?   ?   ??? Index.cshtml.cs          ? PageModel
?   ??? Checkout/
?   ?   ??? Index.cshtml
?   ?   ??? Index.cshtml.cs
?   ??? Product/
?   ?   ??? Details.cshtml
?   ?   ??? Details.cshtml.cs
?   ??? Search/
?   ?   ??? Search.cshtml
?   ?   ??? Search.cshtml.cs
?   ??? Account/
?   ?   ??? Login.cshtml
?   ?   ??? Register.cshtml
?   ?   ??? ...
?   ??? Index.cshtml    ? Homepage
?   ??? _Layout.cshtml        ? Master layout
?
??? Areas/Admin/          ? ADMIN AREA (MVC)
?   ??? Models/
?   ?   ??? ProductModel.cs
?   ?   ??? OrderModel.cs
?   ?   ??? ...
?   ??? Controllers/
?   ?   ??? ProductController.cs
?   ?   ??? OrderController.cs
?   ?   ??? CustomerController.cs
?   ?   ??? ...
?   ??? Views/
?  ??? Product/
?       ?   ??? List.cshtml
?   ?   ??? Edit.cshtml
?       ?   ??? Create.cshtml
?       ?   ??? ...
?       ??? Order/
?       ?   ??? List.cshtml
?       ?   ??? Edit.cshtml
?     ?   ??? ...
?       ??? ...
?
??? Controllers/           ? Optional API Controllers
?   ??? ...
?
??? Views/
?   ??? Shared/
?   ?   ??? _Layout.cshtml
?   ?   ??? Components/    ? View components
?   ?   ??? ...
?   ??? ...
?
??? wwwroot/               ? Static files
?   ??? css/
?   ??? js/
?   ??? images/
?   ??? lib/        ? Frontend libraries
?   ??? ...
?
??? Themes/      ? Theme system
?   ??? DefaultClean/        ? Default theme
?   ?   ??? Views/
?   ?   ??? Content/   ? CSS, JS, Images
?   ?   ??? theme.config
?   ??? Custom/
?   ?   ??? ...
?   ??? ...
?
??? Plugins/? Plugins directory
?   ??? Nop.Plugin.Payments.PayPal/
?   ??? Nop.Plugin.Shipping.UPS/
?   ??? ...
?
??? Program.cs   ? Application entry point
??? appsettings.json       ? Configuration
??? ...
```

### 7.3 Razor Pages - Public Store

#### PageModel Pattern

```csharp
// Pages/Cart/Index.cshtml.cs
public class CartModel : BasePageModel
{
    private readonly IShoppingCartService _shoppingCartService;
    private readonly IProductService _productService;
    
    public CartModel(
        IShoppingCartService shoppingCartService,
IProductService productService)
    {
  _shoppingCartService = shoppingCartService;
        _productService = productService;
    }
    
    [BindProperty]
    public List<CartItemModel> Items { get; set; }
    
    public async Task OnGetAsync()
 {
        // Load cart items
        var cartItems = await _shoppingCartService.GetShoppingCartAsync(...);
        Items = new List<CartItemModel>();
        
        foreach (var item in cartItems)
        {
            Items.Add(new CartItemModel
   {
 ProductId = item.ProductId,
     Quantity = item.Quantity
            });
        }
    }
    
    public async Task<IActionResult> OnPostUpdateAsync()
 {
        // Update cart
        // ...
        return RedirectToPage();
    }
}
```

**Tài li?u ??c:**
- [ ] `src/Presentation/Nop.Web/Pages/Cart/`
- [ ] `src/Presentation/Nop.Web/Pages/Product/`
- [ ] `src/Presentation/Nop.Web/Pages/Index.cshtml`

### 7.4 MVC Controllers - Admin Area

#### Admin Controller Pattern

```csharp
// Areas/Admin/Controllers/ProductController.cs
[Area("Admin")]
[Route("Admin/[controller]/[action]")]
[AuthorizeAdmin]
public class ProductController : BaseAdminController
{
    private readonly IProductService _productService;
 private readonly ICategoryService _categoryService;
    
 public ProductController(
        IProductService productService,
        ICategoryService categoryService)
    {
        _productService = productService;
        _categoryService = categoryService;
    }
    
    // GET: /Admin/Product/List
    public async Task<IActionResult> List()
    {
        var products = await _productService.GetAllAsync();
        var models = products.Select(p => new ProductModel
        {
            Id = p.Id,
  Name = p.Name,
            Price = p.Price
        }).ToList();
        
        return View(models);
 }
    
    // GET: /Admin/Product/Edit/5
    public async Task<IActionResult> Edit(int id)
    {
        var product = await _productService.GetProductByIdAsync(id);
  if (product == null)
      return NotFound();
   
        var model = new ProductModel { ... };
        return View(model);
    }
    
 // POST: /Admin/Product/Edit/5
    [HttpPost]
    public async Task<IActionResult> Edit(ProductModel model)
    {
var product = await _productService.GetProductByIdAsync(model.Id);
        product.Name = model.Name;
        product.Price = model.Price;
        
        await _productService.UpdateProductAsync(product);
  
        return RedirectToAction("List");
    }
}
```

**Tài li?u ??c:**
- [ ] `src/Presentation/Nop.Web/Areas/Admin/Controllers/ProductController.cs`
- [ ] `src/Presentation/Nop.Web/Areas/Admin/Controllers/OrderController.cs`

### 7.5 Bài T?p Giai ?o?n 5

#### Bài 1: Khám Phá Razor Pages
- [ ] Tìm `Pages/Cart/Index.cshtml.cs`
- [ ] Tìm `Pages/Product/Details.cshtml.cs`
- [ ] Xem cách PageModel ???c s? d?ng
- [ ] Ghi chú pattern

#### Bài 2: Khám Phá Admin Controllers
- [ ] Tìm `Areas/Admin/Controllers/ProductController.cs`
- [ ] Li?t kê t?t c? action methods
- [ ] Xem cách model ???c s? d?ng
- [ ] V? s? ?? CRUD actions

#### Bài 3: T?o Razor Page M?i
- [ ] T?o `Pages/MyPage/Index.cshtml`
- [ ] T?o `Pages/MyPage/Index.cshtml.cs`
- [ ] Implement OnGetAsync()
- [ ] Thêm HTML/CSS c? b?n
- [ ] Test truy c?p page

#### Bài 4: T?o Admin Controller & Views
- [ ] T?o controller trong Areas/Admin/Controllers/
- [ ] T?o List action
- [ ] T?o Edit action
- [ ] T?o view t??ng ?ng
- [ ] Test admin functionality

---

## 8. Giai ?o?n 6: Hi?u Plugin System

**?? Th?i Gian: 2 tu?n**

### 8.1 Plugin Architecture

nopCommerce s? d?ng **plugin-based architecture**, cho phép m? r?ng ch?c n?ng mà không c?n thay ??i core code.

#### Types of Plugins

| Plugin Type | V? Trí | Ch?c N?ng |
|---|---|---|
| **Payment** | `Plugins/Nop.Plugin.Payments.*` | X? lý thanh toán |
| **Shipping** | `Plugins/Nop.Plugin.Shipping.*` | Tính phí v?n chuy?n |
| **Tax** | `Plugins/Nop.Plugin.Tax.*` | Tính thu? |
| **Widget** | `Plugins/Nop.Plugin.Widgets.*` | Thêm widget vào trang |
| **Discount Rules** | `Plugins/Nop.Plugin.DiscountRules.*` | Quy t?c khuy?n mãi |
| **External Auth** | `Plugins/Nop.Plugin.ExternalAuth.*` | Xác th?c bên th? ba |
| **Misc** | `Plugins/Nop.Plugin.Misc.*` | Ti?n ích khác |

### 8.2 Plugin Structure

#### C?u Trúc File Plugin

```
Nop.Plugin.Payments.PayPal/
??? plugin.json     ? Plugin metadata
??? PayPalPlugin.cs        ? Main plugin class
??? Startup.cs    ? DI configuration
??? Models/
?   ??? ConfigurationModel.cs
?   ??? PaymentInfoModel.cs
??? Controllers/
?   ??? PayPalController.cs
??? Views/
?   ??? Configure.cshtml   ? Admin configuration
?   ??? PaymentInfo.cshtml   ? Payment form
?   ??? ...
??? Services/
?   ??? IPayPalService.cs
?   ??? PayPalService.cs
??? Migrations/
?   ??? 202401010000_Initial.cs
??? Nop.Plugin.Payments.PayPal.csproj
```

### 8.3 plugin.json

```json
{
  "Name": "PayPal Commerce",
  "SystemName": "Payments.PayPalCommerce",
  "Version": "1.00",
  "SupportedVersions": ["5.00"],
  "Author": "nopCommerce team",
  "DisplayOrder": 1,
  "FileName": "Nop.Plugin.Payments.PayPalCommerce.dll",
  "Description": "This plugin enables customers to pay using PayPal Commerce."
}
```

### 8.4 Main Plugin Class

#### Payment Plugin Example

```csharp
// PayPalPlugin.cs
[PluginDescriptor(
    FriendlyName = "PayPal Commerce",
    SystemName = "Payments.PayPalCommerce",
    Version = "1.00",
    SupportedVersions = new[] { "5.00" })]
public class PayPalPlugin : BasePlugin, IPaymentMethod
{
    private readonly IRepository<PayPalSetting> _settingsRepository;
    private readonly ISettingService _settingService;
    
    public PayPalPlugin(
        IRepository<PayPalSetting> settingsRepository,
    ISettingService settingService)
    {
        _settingsRepository = settingsRepository;
 _settingService = settingService;
    }
    
    // IPaymentMethod implementation
    
    public async Task<decimal> GetAdditionalHandlingFeeAsync(List<ShoppingCartItem> cart)
    {
    // Calculate additional fee
        return 0;
    }
    
    public async Task PostProcessPaymentAsync(PostProcessPaymentRequest request)
    {
        // Redirect to PayPal for payment
        var redirectUrl = await GeneratePayPalRedirectUrlAsync(request.Order);
        // ...
    }
    
    public async Task<ProcessPaymentResult> ProcessPaymentAsync(ProcessPaymentRequest request)
    {
        var result = new ProcessPaymentResult { NewPaymentStatus = PaymentStatus.Pending };
 return result;
    }
    
    // Installation & Uninstallation
    
    public override async Task InstallAsync()
    {
        // Create plugin settings in database
        var settings = new PayPalSettings { /* ... */ };
   await _settingsRepository.InsertAsync(settings);
      
        await base.InstallAsync();
    }
    
    public override async Task UninstallAsync()
  {
   // Remove plugin settings from database
     await _settingsRepository.DeleteAsync(
            await _settingsRepository.GetAllAsync());
        
        await base.UninstallAsync();
    }
    
    // Get configuration page
    public override string GetConfigurationPageUrl()
    {
        return $"{_webHelper.GetStoreLocation()}Admin/PayPal/Configure";
    }
}
```

**Tài li?u ??c:**
- [ ] `src/Plugins/Nop.Plugin.Payments.PayPalCommerce/PayPalCommercePlugin.cs`
- [ ] `src/Plugins/Nop.Plugin.Shipping.UPS/UPSShippingPlugin.cs`

### 8.5 Plugin Interfaces

```csharp
// Payment Method Interface
public interface IPaymentMethod
{
    Task<decimal> GetAdditionalHandlingFeeAsync(List<ShoppingCartItem> cart);
    Task PostProcessPaymentAsync(PostProcessPaymentRequest request);
    Task<ProcessPaymentResult> ProcessPaymentAsync(ProcessPaymentRequest request);
}

// Shipping Rate Computation Interface
public interface IShippingRateComputationMethod
{
    Task<GetShippingOptionResponse> GetShippingOptionsAsync(GetShippingOptionRequest request);
}

// Widget Interface
public interface IWidget
{
    Task<string> GetWidgetViewComponentNameAsync(string widgetZone);
}

// Discount Rule Interface
public interface IDiscountRequirementRule
{
    Task<bool> CheckRequirementAsync(DiscountRequirement requirement);
}
```

### 8.6 Plugin Startup (Dependency Injection)

```csharp
// Startup.cs
public class Startup : INopStartup
{
    public int Order => 1;
  
    public async Task ConfigureAsync(IServiceCollection services)
    {
        // Register plugin services
    services.AddScoped<IPayPalService, PayPalService>();
        services.AddScoped<PayPalManager>();
        
        // Register repositories
        services.AddScoped<IRepository<PayPalSetting>>();
    }
}
```

### 8.7 Bài T?p Giai ?o?n 6

#### Bài 1: Khám Phá Plugin Structure
- [ ] Tìm vài plugin examples
- [ ] V? c?u trúc plugin diagram
- [ ] Xem plugin.json files
- [ ] Ghi chú các file quan tr?ng

#### Bài 2: Hi?u Payment Plugin
- [ ] Tìm `Nop.Plugin.Payments.PayPalCommerce`
- [ ] Xem `plugin.json`
- [ ] Xem main plugin class
- [ ] Xem IPaymentMethod implementation
- [ ] Li?t kê t?t c? methods

#### Bài 3: T?o Simple Plugin
- [ ] T?o folder: `Nop.Plugin.Misc.HelloWorld`
- [ ] T?o `plugin.json`
- [ ] T?o `HelloWorldPlugin.cs` (extends BasePlugin)
- [ ] Implement `InstallAsync()` & `UninstallAsync()`
- [ ] Build plugin
- [ ] Cài ??t plugin

#### Bài 4: T?o Custom Widget Plugin
- [ ] T?o widget plugin: `Nop.Plugin.Widgets.CustomWidget`
- [ ] Implement `IWidget` interface
- [ ] T?o view component
- [ ] T?o configuration page
- [ ] Test widget trên store

---

## 9. Giai ?o?n 7: Th?c Hành Th?c T?

**?? Th?i Gian: 3-4 tu?n**

### 9.1 Bài T?p C?p ?? 1 - C? B?n

#### ? Task 1.1: Thêm C?t M?i vào B?ng Product

**M?c tiêu:** T?o migration, c?p nh?t entity, service và UI

**B??c th?c hi?n:**
1. T?o migration m?i
2. Thêm column vào Product entity
3. C?p nh?t ProductConfiguration
4. C?p nh?t ProductService
5. C?p nh?t admin view ?? hi?n th? column m?i
6. Test: t?o/s?a s?n ph?m v?i column m?i

**H??ng d?n:**
```bash
# 1. T?o migration
cd src/Libraries/Nop.Data
# T?o file migration

# 2. Update Product entity
# src/Libraries/Nop.Core/Domain/Catalog/Product.cs
public string MyNewColumn { get; set; }

# 3. Update Configuration
# src/Libraries/Nop.Data/Configuration/ProductConfiguration.cs
builder.Property(p => p.MyNewColumn).HasMaxLength(500);

# 4. Update Service
# src/Libraries/Nop.Services/Catalog/ProductService.cs
// Add logic if needed

# 5. Update Admin View
# src/Presentation/Nop.Web/Areas/Admin/Views/Product/Edit.cshtml
<input asp-for="@Model.MyNewColumn" />

# 6. Build & Test
dotnet build
```

#### ? Task 1.2: T?o Razor Page M?i

**M?c tiêu:** T?o public store page m?i

**B??c th?c hi?n:**
1. T?o `Pages/MyNewPage/Index.cshtml.cs`
2. T?o `Pages/MyNewPage/Index.cshtml`
3. Implement OnGetAsync() ho?c OnPostAsync()
4. Thêm vào navigation menu
5. Test truy c?p page

**H??ng d?n:**
```csharp
// Pages/MyNewPage/Index.cshtml.cs
public class MyNewPageModel : BasePageModel
{
private readonly IProductService _productService;
    
    public MyNewPageModel(IProductService productService)
 {
        _productService = productService;
    }
    
    public List<ProductModel> Products { get; set; }
    
    public async Task OnGetAsync()
    {
        var products = await _productService.GetAllAsync();
        Products = products.Select(p => new ProductModel { ... }).ToList();
    }
}
```

#### ? Task 1.3: Thêm Filter Vào Catalog

**M?c tiêu:** Thêm filter (vd: theo giá, theo brand) vào catalog page

**B??c th?c hi?n:**
1. Tìm `Pages/Search/Search.cshtml.cs`
2. Thêm filter parameter
3. C?p nh?t query logic
4. C?p nh?t UI ?? hi?n th? filter
5. Test filter functionality

---

### 9.2 Bài T?p C?p ?? 2 - Nâng Cao

#### ? Task 2.1: T?o Custom Service

**M?c tiêu:** T?o service tùy ch?nh v?i DI, caching, events

**B??c th?c hi?n:**
1. T?o interface: `IMyCustomService.cs`
2. T?o implementation: `MyCustomService.cs`
3. Thêm dependency injection
4. Implement caching
5. Publish events
6. Vi?t unit tests

**Ví d?:**
```csharp
// Nop.Services/Custom/IMyCustomService.cs
public interface IMyCustomService
{
    Task<MyEntity> GetByIdAsync(int id);
    Task InsertAsync(MyEntity entity);
    Task DeleteAsync(MyEntity entity);
}

// Nop.Services/Custom/MyCustomService.cs
public class MyCustomService : IMyCustomService
{
    private readonly IRepository<MyEntity> _repository;
    private readonly IEventPublisher _eventPublisher;
    private readonly IStaticCacheManager _cacheManager;
    
    public MyCustomService(
        IRepository<MyEntity> repository,
        IEventPublisher eventPublisher,
        IStaticCacheManager cacheManager)
    {
        _repository = repository;
        _eventPublisher = eventPublisher;
        _cacheManager = cacheManager;
    }
    
  public async Task<MyEntity> GetByIdAsync(int id)
    {
        var cacheKey = $"my_entity_{id}";
        return await _cacheManager.GetAsync(cacheKey, async () =>
        {
  return await _repository.GetByIdAsync(id);
  });
    }
    
    public async Task InsertAsync(MyEntity entity)
  {
      await _repository.InsertAsync(entity);
 await _eventPublisher.PublishAsync(new MyEntityInsertedEvent { Entity = entity });
    }
    
    public async Task DeleteAsync(MyEntity entity)
    {
        await _repository.DeleteAsync(entity);
        await _cacheManager.RemoveAsync($"my_entity_{entity.Id}");
  }
}
```

#### ? Task 2.2: T?o Simple Payment Plugin

**M?c tiêu:** T?o plugin thanh toán ??n gi?n (t??ng t? Manual Payment)

**B??c th?c hi?n:**
1. T?o plugin folder
2. T?o `plugin.json`
3. T?o plugin class (implement IPaymentMethod)
4. T?o configuration controller
5. T?o configuration view
6. T?o payment info view
7. Register trong DI
8. Cài ??t & test plugin

#### ? Task 2.3: T?o Report Tùy Ch?nh

**M?c tiêu:** T?o báo cáo custom có th? xu?t Excel

**B??c th?c hi?n:**
1. T?o report service
2. L?y d? li?u t? database
3. Format d? li?u
4. Xu?t Excel (dùng ClosedXML)
5. Thêm vào Admin area
6. Test xu?t file

---

### 9.3 Bài T?p C?p ?? 3 - Expert

#### ? Task 3.1: T?i ?u Performance

**M?c tiêu:** Tìm và t?i ?u hóa performance issues

**B??c th?c hi?n:**
1. S? d?ng profiler ?? tìm bottlenecks
2. Implement caching strategy
3. Optimize SQL queries (s? d?ng Include, Select)
4. Implement pagination
5. Benchmark improvements

#### ? Task 3.2: Customize Theme

**M?c tiêu:** T?o custom theme ho?c modify existing theme

**B??c th?c hi?n:**
1. Copy default theme
2. Modify layout & styles
3. Thêm custom components
4. Update theme.config
5. Test theme on store

#### ? Task 3.3: Tích H?p 3rd Party API

**M?c tiêu:** Tích h?p external service (vd: Email API, SMS gateway)

**B??c th?c hi?n:**
1. T?o wrapper service
2. Implement API calls
3. Thêm configuration
4. Implement error handling
5. Vi?t unit tests

---

## 10. Timeline & Checklist

### 10.1 Timeline H?c T?p

| Giai ?o?n | Tu?n | Ho?t ??ng | Status |
|---|---|---|---|
| **1** | 1-2 | Ki?n th?c n?n t?ng | ? |
| **2** | 3-4 | L?p Core | ? |
| **3** | 5-6 | L?p Data Access | ? |
| **4** | 7-9 | L?p Services | ? |
| **5** | 10-11 | L?p Presentation | ? |
| **6** | 12-13 | Plugin System | ? |
| **7** | 14-17 | Th?c Hành | ? |
| **8** | 18+ | T?i ?u & Advanced | ? |

### 10.2 Learning Checklist

#### ? Giai ?o?n 1
- [ ] Cài ??t & ch?y d? án
- [ ] Hi?u Clean Architecture
- [ ] Hi?u Dependency Injection
- [ ] Hi?u Plugin concept
- [ ] ??t breakpoint & debug

#### ? Giai ?o?n 2
- [ ] Li?t kê 15+ entities chính
- [ ] V? Entity Relationship Diagram
- [ ] Hi?u BaseEntity
- [ ] Hi?u Cache Keys
- [ ] Hi?u Event System

#### ? Giai ?o?n 3
- [ ] Hi?u Repository Pattern
- [ ] Explore Entity Configurations
- [ ] Hi?u Database Migrations
- [ ] T?o migration ??n gi?n
- [ ] Hi?u Multi-Database support

#### ? Giai ?o?n 4
- [ ] Li?t kê 15+ services
- [ ] Hi?u Service Pattern
- [ ] Hi?u Caching trong services
- [ ] Hi?u Event Publishing
- [ ] T?o custom service

#### ? Giai ?o?n 5
- [ ] Hi?u Razor Pages
- [ ] Hi?u Admin Controllers
- [ ] T?o Razor Page m?i
- [ ] T?o Admin Controller & Views
- [ ] Test functionality

#### ? Giai ?o?n 6
- [ ] Hi?u Plugin Structure
- [ ] Hi?u plugin.json
- [ ] Hi?u Main Plugin Class
- [ ] T?o simple plugin
- [ ] Cài ??t & test plugin

#### ? Giai ?o?n 7
- [ ] Hoàn thành Level 1 tasks (3 tasks)
- [ ] Hoàn thành Level 2 tasks (3 tasks)
- [ ] Hoàn thành Level 3 tasks (3 tasks)

---

## 11. Tài Li?u Tham Kh?o

### 11.1 Các File Quan Tr?ng

```
?? ENTRY POINTS:
   src/Presentation/Nop.Web/Program.cs
   src/Presentation/Nop.Web/Startup.cs

?? CORE LAYER:
   src/Libraries/Nop.Core/Domain/BaseEntity.cs
   src/Libraries/Nop.Core/Domain/[Entities]/
   src/Libraries/Nop.Core/Caching/
   src/Libraries/Nop.Core/Events/
   src/Libraries/Nop.Core/Infrastructure/

?? DATA LAYER:
   src/Libraries/Nop.Data/Repository/
   src/Libraries/Nop.Data/Configuration/
   src/Libraries/Nop.Data/Migrations/

?? SERVICES LAYER:
   src/Libraries/Nop.Services/[Services]/

?? PRESENTATION LAYER:
   src/Presentation/Nop.Web.Framework/
   src/Presentation/Nop.Web/Pages/
   src/Presentation/Nop.Web/Areas/Admin/

?? PLUGINS:
   src/Plugins/*/plugin.json
   src/Plugins/*/[PluginName]Plugin.cs
```

### 11.2 External Resources

- **Official nopCommerce:** https://www.nopcommerce.com/
- **GitHub Repository:** https://github.com/nopSolutions/nopCommerce
- **Community Forum:** https://www.nopcommerce.com/boards
- **.NET Documentation:** https://docs.microsoft.com/dotnet
- **ASP.NET Core:** https://docs.microsoft.com/aspnet/core

### 11.3 Concepts to Deep Dive

- [ ] Entity Framework Core
- [ ] FluentMigrator
- [ ] Autofac Dependency Injection
- [ ] Razor Pages Model Binding
- [ ] ASP.NET Core Filters & Middleware
- [ ] SQL Query Optimization
- [ ] Caching Strategies
- [ ] Event-Driven Architecture
- [ ] Repository Pattern
- [ ] Clean Architecture

---

## 12. Tips & Best Practices

### 12.1 Development Tips

```csharp
// 1. Always use async/await
public async Task<Product> GetProductAsync(int id)
{
    return await _repository.GetByIdAsync(id);
}

// 2. Use constructor injection
public class MyService
{
    private readonly IRepository<Product> _repository;
    
    public MyService(IRepository<Product> repository)
    {
      _repository = repository;
  }
}

// 3. Implement caching for expensive operations
var product = await _cacheManager.GetAsync(cacheKey, async () =>
{
    return await _repository.GetByIdAsync(id);
});

// 4. Publish events for domain changes
await _eventPublisher.PublishAsync(new ProductInsertedEvent { Product = product });

// 5. Use interfaces for loose coupling
public interface IMyService { }
public class MyService : IMyService { }
```

### 12.2 Common Mistakes to Avoid

? **Không làm:**
- T?o instance thay vì inject dependencies
- Sync over async (Result, Wait)
- N+1 query problems
- Hardcoding connection strings
- Ignoring events system

? **Làm:**
- Luôn inject dependencies
- S? d?ng async/await
- Optimize queries v?i Include()
- S? d?ng configuration
- Publish & handle events

### 12.3 Debugging Tips

```csharp
// 1. Set breakpoint
// Ctrl + F5 ?? ch?y without debugging
// F5 ?? debug

// 2. Watch variables
// Debug ? Windows ? Watch

// 3. Check DI container
// Inspect registered services trong ConfigureServices

// 4. View SQL queries
// Enable SQL logging
services.AddLogging(builder => 
    builder.AddConsole().AddDebug());

// 5. Check plugin loading
// View Plugins folder after build
```

---

## ?? Completion Status

| Giai ?o?n | Hoàn Thành |
|---|---|
| Giai ?o?n 1 | ? 0% |
| Giai ?o?n 2 | ? 0% |
| Giai ?o?n 3 | ? 0% |
| Giai ?o?n 4 | ? 0% |
| Giai ?o?n 5 | ? 0% |
| Giai ?o?n 6 | ? 0% |
| Giai ?o?n 7 | ? 0% |
| **T?NG** | **? 0%** |

---

**Ngày b?t ??u:** ___________
**Ngày hoàn thành d? ki?n:** ___________
**Ghi chú:** _______________________________________________________

---

*Tài li?u này ???c t?o ra ?? h? tr? quá trình h?c t?p nopCommerce. Hãy c?p nh?t progress c?a b?n th??ng xuyên!* ??
