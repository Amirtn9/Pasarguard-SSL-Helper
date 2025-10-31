<!DOCTYPE html>
<html lang="fa">
<head>
<meta charset="UTF-8">
<title>آموزش گرفتن گواهی SSL برای پاسارگارد</title>
</head>
<body dir="rtl">

  <h1>🚀 آموزش گرفتن گواهی SSL برای پاسارگارد</h1>

  <p>سلام! 👋</p>
  <p>این پروژه به شما کمک می‌کند تا به سادگی <strong>گواهی SSL</strong> برای دامنه‌های خود دریافت کرده و آن‌ها را در <strong>پاسارگارد</strong> ذخیره کنید. با این روش، اتصال سرور شما امن و معتبر خواهد بود و نیاز به انجام مراحل پیچیده به صورت دستی نخواهید داشت.</p>
  <p>این آموزش مخصوص افرادی است که می‌خواهند بدون دردسر و با <strong>یک اسکریپت آماده</strong>، گواهی SSL دریافت کرده و روی سرور پاسارگارد خود نصب کنند.</p>

  <hr>

  <h2>📝 مرحله ۱: ساخت فایل اسکریپت</h2>
  <p>ابتدا یک فایل اسکریپت با نام <code>get_cert.sh</code> بسازید:</p>
  <pre>nano get_cert.sh</pre>

  <hr>

  <h2>📝 مرحله ۲: محتویات اسکریپت</h2>
  <p>محتویات زیر را به صورت کامل کپی کرده و داخل فایل <code>get_cert.sh</code> پیست کنید.</p>
  
  <blockquote>
    <p><strong>توجه بسیار مهم:</strong> قبل از ذخیره کردن، حتماً در خط پنجم اسکریپت، <code>YOURDOMAIN.COM</code> را با آدرس دامنه واقعی خود جایگزین کنید.</p>
  </blockquote>

  <pre>
#!/bin/bash

# ====== تنظیمات ======
# 👇 این خط را ویرایش کنید 👇
DOMAIN="YOURDOMAIN.COM"
DEST_DIR="/var/lib/pasarguard/certs/$DOMAIN"

# ====== بررسی و نصب Certbot ======
if ! command -v certbot &> /dev/null; then
    echo "[INFO] Certbot نصب نیست، در حال نصب..."
    sudo apt update
    sudo apt install snapd -y
    sudo snap install core; sudo snap refresh core
    sudo snap install --classic certbot
    sudo ln -s /snap/bin/certbot /usr/bin/certbot
else
    echo "[INFO] Certbot نصب است."
fi

# ====== ساخت مسیر نهایی ======
sudo mkdir -p "$DEST_DIR"

# ====== گرفتن گواهی SSL و ذخیره مستقیم در مسیر نهایی ======
echo "[INFO] در حال گرفتن گواهی SSL برای $DOMAIN ..."
sudo certbot certonly \
  --standalone \
  --agree-tos \
  --register-unsafely-without-email \
  -d "$DOMAIN" \
  --cert-path "$DEST_DIR/cert.pem" \
  --key-path "$DEST_DIR/privkey.pem" \
  --fullchain-path "$DEST_DIR/fullchain.pem" \
  --chain-path "$DEST_DIR/chain.pem" \
  --preferred-challenges http \
  --non-interactive

echo "[INFO] عملیات تمام شد. فایل‌های گواهی در مسیر $DEST_DIR قرار دارند."
  </pre>

  <hr>

  <h2>⚙️ مرحله ۳: دادن دسترسی اجرایی به اسکریپت</h2>
  <p>پس از ذخیره فایل، دستور زیر را برای قابل اجرا کردن آن وارد کنید:</p>
  <pre>chmod +x get_cert.sh</pre>

  <hr>

  <h2>🚀 مرحله ۴: اجرای اسکریپت برای گرفتن گواهی SSL</h2>
  <p>اکنون اسکریپت را با دسترسی روت (sudo) اجرا کنید تا فرآیند دریافت گواهی آغاز شود:</p>
  <pre>sudo ./get_cert.sh</pre>

  <hr>

  <h2>💡 نکته مهم: انتقال دستی گواهی</h2>
  <p>اگر از قبل با روش دیگری گواهی SSL گرفته‌اید (مثلاً در مسیر پیش‌فرض Certbot) و فقط می‌خواهید آن‌ها را به مسیر مورد تایید پاسارگارد منتقل کنید، از دستورات زیر استفاده نمایید (فراموش نکنید <code>YOURDOMAIN.COM</code> را جایگزین کنید):</p>

  <pre>
sudo mkdir -p /var/lib/pasarguard/certs/YOURDOMAIN.COM
sudo cp /etc/letsencrypt/live/YOURDOMAIN.COM/fullchain.pem /var/lib/pasarguard/certs/YOURDOMAIN.COM/
sudo cp /etc/letsencrypt/live/YOURDOMAIN.COM/privkey.pem /var/lib/pasarguard/certs/YOURDOMAIN.COM/
  </pre>

</body>
</html>
