# PADDON / PALLET — yog‘och palletlar sayti

Yog‘och palletlar ishlab chiqaruvchi kompaniya uchun to‘liq ishlaydigan sayt: katalog (filtr va qidiruv bilan), interaktiv o‘lchamlar bo‘limi, maxsus buyurtma konfiguratori, 3 til (UZ / RU / EN) va admin panel.

**Texnologiyalar:** Next.js 15 (App Router) · TypeScript · Tailwind CSS 4 · shadcn/ui uslubidagi komponentlar · Framer Motion · Lucide · PostgreSQL + Prisma · Vercel Blob · Telegram Bot API

---

## 1. Tezkor ishga tushirish (o‘z kompyuteringizda)

Kerak: **Node.js 20+** va **PostgreSQL** ma’lumotlar bazasi (bepul variant: [neon.tech](https://neon.tech)).

```bash
npm install
cp .env.example .env        # Windows: copy .env.example .env
# .env faylini oching va DATABASE_URL, DIRECT_URL, AUTH_SECRET, ADMIN_EMAIL, ADMIN_PASSWORD ni to‘ldiring
npm run db:push             # bazada jadvallarni yaratadi
npm run db:seed             # admin foydalanuvchi + 7 ta DEMO mahsulot
npm run dev                 # http://localhost:3000
```

- Sayt: <http://localhost:3000> (avtomatik `/uz` ga o‘tadi)
- Admin panel: <http://localhost:3000/admin> — `.env` dagi `ADMIN_EMAIL` / `ADMIN_PASSWORD` bilan kiring.

`AUTH_SECRET` yaratish:

```bash
node -e "console.log(require('crypto').randomBytes(48).toString('base64'))"
```

---

## 2. Yozuvlarni o‘zingizga moslash ✏️

Saytdagi **barcha** matnlarni ikki yo‘l bilan o‘zgartirish mumkin:

### A) Admin panel orqali (kod yozmasdan) — tavsiya etiladi

| Nima o‘zgartiriladi | Qayerda |
|---|---|
| Sarlavhalar, tugmalar, bo‘lim matnlari, “Biz haqimizda” matni (3 tilda) | **Admin → Sayt matnlari** |
| Telefon, Telegram, email, manzil, ish vaqti, Google xarita | **Admin → Sozlamalar** |
| Bosh ekran va “Biz haqimizda” rasmlari | **Admin → Sozlamalar → Sayt rasmlari** |
| Mahsulotlar: nomi, tavsifi, o‘lchami, materiali, narxi, rasmlari | **Admin → Mahsulotlar** |
| Buyurtma statuslari va izohlar | **Admin → Buyurtmalar** |

“Sayt matnlari” bo‘limida har bir yozuv yonida UZ / RU / EN maydonlari bor. Saqlangan matn darhol saytda ko‘rinadi. “Standart” tugmasi asl matnni qaytaradi. Qidiruv maydoni orqali kerakli yozuvni tez topasiz.

### B) Fayllar orqali (standart matnni butunlay almashtirish)

| Fayl | Nima bor |
|---|---|
| `src/i18n/dictionaries/uz.ts` | O‘zbekcha barcha matnlar |
| `src/i18n/dictionaries/ru.ts` | Ruscha matnlar |
| `src/i18n/dictionaries/en.ts` | Inglizcha matnlar |
| `src/app/globals.css` (`@theme` bloki) | Ranglar va shriftlar |
| `public/images/` | Standart rasmlar |
| `prisma/seed.ts` | Boshlang‘ich (demo) mahsulotlar |

> `{count}`, `{number}`, `{kg}` kabi jingalak qavsdagi so‘zlarni o‘chirmang — ular avtomatik to‘ldiriladi.
> Admin panelda o‘zgartirilgan matn fayldagi matndan ustun turadi.

---

## 3. Demo va haqiqiy ma’lumotlar

| Ma’lumot | Holati |
|---|---|
| 7 ta mahsulot (`prisma/seed.ts`) | **DEMO** — saytda “Demo” belgisi bilan ko‘rinadi |
| Yuk ko‘tarish quvvati, narx | Kiritilmagan (o‘ylab topilmagan). Kartada “—” va “Narxni so‘rash” chiqadi |
| Telefon, Telegram, manzil, ish vaqti, xarita | Kiritilmagan. Admin panelda o‘zingiz kiritasiz |
| “Biz haqimizda” matni | Umumiy namunaviy matn, soxta statistika, sertifikat yoki sharhlar **yo‘q** |
| Rasmlar | Siz yuborgan rasmlar: yevropallet, 1200×1000 pallet, yarim pallet va ombor rasmi |

Rasmi yo‘q mahsulotlar (kvadrat, maxsus, katta pallet) uchun sayt o‘lchamga mos **vizual izometrik model** ko‘rsatadi — buzilgan rasm hech qachon chiqmaydi. Haqiqiy foto yuklasangiz, u avtomatik birinchi o‘ringa chiqadi.

**Tekshiruv ro‘yxati:** Admin → Boshqaruv sahifasida “Saytni ishga tayyorlash” bloki nima to‘ldirilmaganini ko‘rsatadi.

---

## 4. Vercel’ga joylashtirish

1. Loyihani GitHub’ga yuklang (`.env` yuklanmaydi — bu to‘g‘ri).
2. [vercel.com](https://vercel.com) → **Add New → Project** → repozitoriyani tanlang.
3. **Storage** bo‘limida:
   - **Postgres (Neon)** yarating → `DATABASE_URL` va `DATABASE_URL_UNPOOLED` avtomatik qo‘shiladi. `DIRECT_URL` nomli o‘zgaruvchi qo‘shib, unga `DATABASE_URL_UNPOOLED` qiymatini bering.
   - **Blob** yarating → `BLOB_READ_WRITE_TOKEN` avtomatik qo‘shiladi (rasm yuklash uchun **shart**).
4. **Settings → Environment Variables** ga qo‘shing: `AUTH_SECRET`, `NEXT_PUBLIC_SITE_URL` (masalan `https://pallet.uz`), ixtiyoriy `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID`.
5. Bazani tayyorlash (bir marta, o‘z kompyuteringizdan, `.env` ga production `DATABASE_URL`/`DIRECT_URL` ni qo‘yib):
   ```bash
   npm run db:push
   ADMIN_EMAIL=siz@pochta.uz ADMIN_PASSWORD='kuchli-parol' npm run db:seed
   ```
6. **Deploy**. Domen ulash: Vercel → Settings → Domains.

> Vercel’da fayl tizimi vaqtinchalik, shuning uchun rasmlar faqat Vercel Blob orqali doimiy saqlanadi.

---

## 5. Telegram xabarnomalarini ulash

1. Telegram’da [@BotFather](https://t.me/BotFather) → `/newbot` → bot yarating va **token** oling → `TELEGRAM_BOT_TOKEN`.
2. Botingizga istalgan xabar yozing (yoki botni guruhga qo‘shing).
3. Brauzerda oching: `https://api.telegram.org/bot<TOKEN>/getUpdates` → javobdagi `"chat":{"id": ...}` qiymati → `TELEGRAM_CHAT_ID` (guruh ID’si `-100...` bilan boshlanadi).
4. Saytni qayta ishga tushiring. Admin → Sozlamalar → **Test xabar** tugmasi bilan tekshiring.

Har bir yangi buyurtma va aloqa formasi xabari administratorga yuboriladi. Telegram ishlamasa ham buyurtma bazaga saqlanadi.

---

## 6. Loyiha tuzilishi

```
prisma/
  schema.prisma            ma’lumotlar bazasi sxemasi
  seed.ts                  admin + demo mahsulotlar
public/images/             standart rasmlar (mahsulotlar, sayt)
src/
  middleware.ts            til yo‘naltirish (/uz /ru /en) va /admin himoyasi
  actions/admin.ts         admin server amallari (saqlash, o‘chirish, status...)
  app/
    [locale]/              ommaviy sahifalar
      page.tsx             bosh sahifa
      products/            katalog + mahsulot sahifasi
      sizes/               interaktiv o‘lchamlar
      order/               buyurtma konfiguratori
      about/  contact/
    admin/                 admin panel (login, mahsulotlar, buyurtmalar, matnlar, sozlamalar)
    api/orders             buyurtmani qabul qilish (server validatsiya + Telegram)
    api/contact            aloqa formasi
    api/admin/upload       rasm yuklash
    media/[...path]        lokal yuklangan rasmlar (Blob’siz rejim)
  components/
    ui/                    tugma, input, badge, dialog (shadcn uslubi)
    site/                  sayt komponentlari (hero, katalog, pallet modeli...)
    admin/                 admin komponentlari
  i18n/dictionaries/       UZ / RU / EN matnlar
  lib/                     baza, auth, validatsiya, saqlash, Telegram, sozlamalar
```

## 7. Xavfsizlik

- Parollar bcrypt bilan xeshlanadi; sessiya — imzolangan JWT, `httpOnly` cookie.
- `/admin` middleware va har bir server amalida tekshiriladi.
- Kirish urinishlari va formalar uchun so‘rovlar cheklovi, bot-tuzoq (honeypot) maydoni.
- Barcha formalar serverda Zod bilan tekshiriladi.
- Telefon, token, parollar kodda yozilmagan — faqat `.env` va admin panel orqali.

## 8. Foydali buyruqlar

| Buyruq | Vazifasi |
|---|---|
| `npm run dev` | Ishlab chiqish serveri |
| `npm run build && npm start` | Production build |
| `npm run typecheck` | TypeScript tekshiruvi |
| `npm run db:push` | Sxemani bazaga qo‘llash |
| `npm run db:seed` | Admin va demo ma’lumotlar |
| `npm run db:studio` | Bazani brauzerda ko‘rish (Prisma Studio) |
