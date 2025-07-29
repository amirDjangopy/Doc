# 🧑‍💼 مستندات API بخش مدیریت کاربران با نقش‌ها

## 🎯 معرفی کلی
این سرویس مسئول مدیریت کاربران در سیستم فروشگاه آنلاین است. شامل عملیات‌هایی مانند:

- ثبت‌نام کاربران

- ورود و خروج

- مشاهده و ویرایش پروفایل

- تخصیص نقش به کاربران

- حذف کاربران توسط مدیر

احراز هویت با **JWT** انجام می‌شود و سطوح دسترسی با استفاده از نقش‌های مختلف کنترل می‌گردد.


## 👥 نقش‌های تعریف‌شده در سیستم

- admin	مدیر سیستم - دسترسی کامل به تمام عملیات و کاربران
- editor	ویرایشگر فروشگاه - ویرایش اطلاعات محصولات و محتوا
- support	پشتیبانی مشتری - مشاهده و پاسخ به تیکت‌ها
- vendor	فروشنده - ایجاد و مدیریت محصولات خود
- affiliate	بازاریاب - مشاهده لینک‌ها و کارکرد سیستم بازاریابی

## 🔐 مجوزها و دسترسی‌ها
## 📊 جدول دسترسی به مسیرهای API بر اساس نقش‌ها

| عملیات                | مسیر API                          | دسترسی توسط نقش              |
|------------------------|------------------------------------|------------------------------|
| دریافت لیست کاربران    | `GET /api/v1/users/`               | فقط `admin`                 |
| ثبت‌نام               | `POST /api/v1/users/`              | همه (`Public`)              |
| مشاهده پروفایل        | `GET /api/v1/users/{id}/`          | خود کاربر یا `admin`        |
| ویرایش پروفایل        | `PUT /api/v1/users/{id}/`          | خود کاربر یا `admin`        |
| حذف کاربر            | `DELETE /api/v1/users/{id}/`       | فقط `admin`                 |
| ورود                  | `POST /api/v1/auth/login/`         | همه                         |
| خروج                  | `POST /api/v1/auth/logout/`        | فقط کاربران احراز شده       |
| مشاهده نقش کاربر      | `GET /api/v1/users/{id}/role/`     | صاحب حساب یا `admin`        |
| تغییر نقش کاربر       | `PATCH /api/v1/users/{id}/role/`   | فقط `admin`                 |


## 📝 ثبت‌نام کاربر جدید
```
Request POST /api/v1/users/
```
```
{
  "username": "mohammad123",
  "email": "mohammad@example.com",
  "password": "StrongPassword123!"
}
```

```
Validation:
username: یکتا، حداقل ۳ کاراکتر
email: معتبر و یکتا
password: حداقل ۸ کاراکتر، ذخیره به‌صورت هش‌شده
```

```
Response (201 Created):
{
  "id": 12,
  "username": "mohammad123",
  "email": "mohammad@example.com",
  "role": "affiliate",
  "date_joined": "2025-07-29T10:34:12Z"
}
```

```
🔑 ورود به سیستم
Request POST /api/v1/auth/login/


{
  "username": "mohammad123",
  "password": "StrongPassword123!"
}
Response (200 OK):

````

```
{
  "access": "توکن JWT برای هدر Authorization",
  "refresh": "توکن تجدید برای دریافت مجدد access"
}
🎭 تخصیص نقش به کاربر (Admin Only)
Request PATCH /api/v1/users/12/role/
Header: Authorization: Bearer <admin_access_token>

```

```
{
  "role": "vendor"
}
Response:


{
  "id": 12,
  "username": "mohammad123",
  "role": "vendor"
}
```

```
## 🛠 مدل پایگاه داده (خلاصه‌شده)

class User(AbstractUser):
    email = models.EmailField(unique=True)
    ROLE_CHOICES = [
        ("admin", "مدیر"),
        ("editor", "ویرایشگر"),
        ("support", "پشتیبانی"),
        ("vendor", "فروشنده"),
        ("affiliate", "بازاریاب"),
    ]
    role = models.CharField(max_length=20, choices=ROLE_CHOICES, default="affiliate")
```

```

## 🚫 مدیریت خطا
ساختار پاسخ خطا:

{
  "detail": "توضیح خطا"
}
نمونه‌ها:

کد وضعیت	توضیح
400	خطا در اعتبارسنجی داده‌ها
401	احراز هویت انجام نشده
403	دسترسی غیرمجاز
404	کاربر یافت نشد
500	خطای داخلی سرور

```
