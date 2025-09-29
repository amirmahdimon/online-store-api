# Online Store API Documentation

## Base URL
```
https://your-api-domain.com/api
```

## Authentication
تمامی درخواست‌های نیازمند احراز هویت باید با هدر `Authorization` و توکن JWT ارسال شوند:
```http
Authorization: Bearer YOUR_JWT_TOKEN
```

## Response Format
تمام پاسخ‌ها در قالب JSON و با فرمت زیر برگردانده می‌شوند:
```json
{
    "success": true/false,
    "data": {},      // در صورت موفقیت
    "error": ""      // در صورت خطا
}
```

## Endpoints

### 👤 Authentication

#### ثبت نام کاربر جدید
```http
POST /users/register
Content-Type: application/json
```
پارامترها:
```json
{
    "name": "نام کاربر",
    "email": "example@email.com",
    "password": "رمز عبور",
    "phone": "شماره موبایل"
}
```
نمونه پاسخ موفق:
```json
{
    "success": true,
    "data": {
        "token": "JWT_TOKEN",
        "user": {
            "id": "user_id",
            "name": "نام کاربر",
            "email": "example@email.com",
            "phone": "شماره موبایل"
        }
    }
}
```

#### ورود کاربر
```http
POST /users/login
Content-Type: application/json
```
پارامترها:
```json
{
    "email": "example@email.com",
    "password": "رمز عبور"
}
```

### 🛍️ محصولات

#### دریافت لیست محصولات
```http
GET /products
```
پارامترهای Query (اختیاری):
- `category`: شناسه دسته‌بندی
- `brand`: شناسه برند
- `search`: جستجو در نام و توضیحات
- `page`: شماره صفحه (پیش‌فرض: 1)
- `limit`: تعداد در هر صفحه (پیش‌فرض: 10)
- `sort`: نحوه مرتب‌سازی (options: price_asc, price_desc, newest)

#### دریافت جزئیات محصول
```http
GET /products/{id}
```
نمونه پاسخ:
```json
{
    "success": true,
    "data": {
        "id": "product_id",
        "name": "نام محصول",
        "description": "توضیحات",
        "price": 100000,
        "images": ["url1", "url2"],
        "category": {
            "id": "category_id",
            "name": "نام دسته‌بندی"
        },
        "brand": {
            "id": "brand_id",
            "name": "نام برند"
        },
        "variants": [
            {
                "type": "رنگ",
                "options": ["قرمز", "آبی", "سبز"]
            },
            {
                "type": "سایز",
                "options": ["S", "M", "L"]
            }
        ]
    }
}
```

### 🛒 سبد خرید و سفارش‌ها

#### ایجاد سفارش جدید
```http
POST /orders
Authorization: Bearer YOUR_JWT_TOKEN
Content-Type: application/json
```
پارامترها:
```json
{
    "items": [
        {
            "productId": "شناسه محصول",
            "quantity": 2,
            "variants": {
                "رنگ": "قرمز",
                "سایز": "L"
            }
        }
    ],
    "shippingAddress": {
        "address": "آدرس کامل",
        "city": "شهر",
        "postalCode": "کد پستی"
    }
}
```

#### پیگیری وضعیت سفارش
```http
GET /orders/{orderId}
Authorization: Bearer YOUR_JWT_TOKEN
```

### 💳 پرداخت

#### ایجاد تراکنش جدید
```http
POST /payment/create
Authorization: Bearer YOUR_JWT_TOKEN
Content-Type: application/json
```
پارامترها:
```json
{
    "orderId": "شناسه سفارش",
    "amount": 100000,
    "gateway": "zarinpal" // یا سایر درگاه‌های پرداخت
}
```
نمونه پاسخ:
```json
{
    "success": true,
    "data": {
        "paymentUrl": "لینک درگاه پرداخت",
        "authority": "کد پیگیری"
    }
}
```

#### تایید پرداخت
```http
GET /payment/verify
```
پارامترهای Query:
- `Authority`: کد پیگیری دریافتی از درگاه
- `Status`: وضعیت تراکنش

### 🎫 کد تخفیف

#### اعمال کد تخفیف
```http
POST /coupons/apply
Authorization: Bearer YOUR_JWT_TOKEN
Content-Type: application/json
```
پارامترها:
```json
{
    "code": "کد تخفیف",
    "orderId": "شناسه سفارش"
}
```

## کدهای خطا
- `400`: پارامترهای نامعتبر
- `401`: عدم احراز هویت
- `403`: دسترسی غیرمجاز
- `404`: منبع یافت نشد
- `422`: خطای اعتبارسنجی
- `500`: خطای سرور

## نکات مهم
1. تمام درخواست‌ها باید با هدر `Content-Type: application/json` ارسال شوند (به جز آپلود فایل)
2. برای آپلود تصاویر از `Content-Type: multipart/form-data` استفاده کنید
3. مقادیر پولی به ریال محاسبه می‌شوند
4. محدودیت حجم فایل برای آپلود تصاویر: 2MB
5. فرمت‌های مجاز تصاویر: JPG, PNG, WEBP

## مثال‌های کاربردی

### ثبت نام و ورود با Flutter
```dart
Future<void> register() async {
  final response = await http.post(
    Uri.parse('$baseUrl/users/register'),
    headers: {'Content-Type': 'application/json'},
    body: jsonEncode({
      'name': 'نام کاربر',
      'email': 'example@email.com',
      'password': 'password123',
      'phone': '09123456789'
    }),
  );
  
  if (response.statusCode == 200) {
    final data = jsonDecode(response.body);
    // ذخیره توکن
    final token = data['data']['token'];
    // استفاده از توکن در درخواست‌های بعدی
  }
}
```

### دریافت لیست محصولات
```dart
Future<List<Product>> getProducts({String? categoryId}) async {
  final response = await http.get(
    Uri.parse('$baseUrl/products').replace(
      queryParameters: {
        if (categoryId != null) 'category': categoryId,
        'page': '1',
        'limit': '10'
      },
    ),
    headers: {
      'Authorization': 'Bearer $token',
    },
  );
  
  if (response.statusCode == 200) {
    final data = jsonDecode(response.body);
    return (data['data'] as List)
        .map((json) => Product.fromJson(json))
        .toList();
  }
  throw Exception('Failed to load products');
}
```
