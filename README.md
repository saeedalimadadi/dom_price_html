
-----

## ساده‌سازی HTML قیمت در ووکامرس

این قطعه کد به شما اجازه می‌دهد تا ساختار HTML تولید شده توسط ووکامرس برای نمایش قیمت محصولات را در بخش کاربری سایت (Front-end) تغییر دهید. کد با استفاده از فیلتر `woocommerce_get_price_html`، تگ‌های اضافی مانند `<bdi>` و `<span>` های دور واحد پولی را حذف می‌کند. این کار به شما کنترل بیشتری برای استایل‌دهی با CSS یا دستکاری قیمت با جاوااسکریپت می‌دهد.

**نکته:** این کد در پنل مدیریت وردپرس اجرا نمی‌شود و قیمت‌ها در بخش مدیریت به شکل استاندارد ووکامرس نمایش داده می‌شوند.

### نصب

کد زیر را به فایل **`functions.php`** قالب فعال وردپرس خود یا در یک افزونه سفارشی اضافه کنید.

```php
add_filter('woocommerce_get_price_html', 'dom_price_html', 10, 2);

function dom_price_html($price, $product) {
    // فقط در بخش کاربری سایت اجرا شود و نه در پنل مدیریت
    if (is_admin()) {
        return $price;
    }

    $currency_symbol = get_woocommerce_currency_symbol();

    // حذف تگ‌های <bdi>
    $price = str_replace(['<bdi>', '</bdi>'], '', $price);

    // حذف تگ span دور واحد پولی و جایگزینی آن با خود واحد پولی
    $price = preg_replace('/<span class="woocommerce-Price-currencySymbol">.*?<\/span>/', $currency_symbol, $price);

    // حذف تگ مخصوص صفحه‌خوان‌ها (Screen Readers)
    $price = preg_replace('/<span class="screen-reader-text">.*?<\/span>/', '', $price);

    return $price;
}
```

### عملکرد کد

1.  **بررسی محیط:** ابتدا چک می‌کند که آیا صفحه در بخش مدیریت وردپرس است یا خیر. اگر در بخش مدیریت باشد، قیمت اصلی را بدون هیچ تغییری باز می‌گرداند.
2.  **حذف تگ `<bdi>`:** این تگ‌ها که برای پشتیبانی از متون دوجهته (مثل فارسی و انگلیسی در کنار هم) استفاده می‌شوند، از خروجی حذف می‌شوند.
3.  **حذف `<span>` واحد پولی:** تگ `<span>` با کلاس `woocommerce-Price-currencySymbol` که واحد پولی را در بر می‌گیرد، حذف می‌شود تا فقط متن خالی واحد پولی باقی بماند.
4.  **حذف متن صفحه‌خوان:** تگ `<span>` با کلاس `screen-reader-text` که برای بهبود دسترسی‌پذیری (Accessibility) برای کاربران نابینا استفاده می‌شود، حذف می‌گردد.

⚠️ **توجه:** حذف `screen-reader-text` ممکن است بر تجربه کاربری افرادی که از صفحه‌خوان‌ها استفاده می‌کنند تأثیر منفی بگذارد. این کد را با آگاهی کامل از این موضوع به کار ببرید.
