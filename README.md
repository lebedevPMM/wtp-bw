# WTP × Bluewaters — Event Landing

Один self-contained HTML-файл (`index.html`). Tailwind подгружается с CDN, без сборки.
Заменяет `event-landing-spec.md`.

---

## Перед деплоем — заменить 3 placeholder'а

1. **Telegram-handle Оли.** Сейчас: `https://t.me/Olga_WTP` (4 раза в файле).
   ```bash
   sed -i '' 's|t.me/Olga_WTP|t.me/REAL_HANDLE|g' index.html
   ```

2. **Formspree endpoint.** Создать форму на https://formspree.io → скопировать ID → заменить:
   ```bash
   sed -i '' 's|formspree.io/f/PLACEHOLDER|formspree.io/f/xxxxxxxx|g' index.html
   ```
   Альтернатива: оставить `PLACEHOLDER` — форма сама свалится в `mailto:hello@wtp.ae` (уже зашито).

3. **GA4 Measurement ID.** Сейчас: `G-XXXXXXXXXX` (2 места). Заменить на реальный из GA4 Admin:
   ```bash
   sed -i '' 's/G-XXXXXXXXXX/G-РЕАЛЬНЫЙ_ID/g' index.html
   ```
   Если GA4 не нужен — снести оба `<script>` блока (тег `gtag`).

---

## Локальная проверка

```bash
cd "$(dirname "$0")"
python3 -m http.server 8080
# открыть http://localhost:8080
```

Тестируем:
- Mobile (Chrome DevTools, iPhone 14 Pro): hero занимает 100dvh, CTA-кнопка по&nbsp;центру, touch-targets ≥ 48px
- Desktop ≥ 1024px: 4 колонки в Operational Ladder
- Form submit с placeholder → редиректит в mailto: (это намеренно)
- Form submit с настоящим Formspree → inline success-сообщение
- UTM capture: открыть `http://localhost:8080/?utm_source=tg&utm_campaign=may6` → submit → проверить, что utm_source долетел до Formspree

---

## Деплой — 3 варианта

### Вариант 1 — Cloudflare Pages (drag-and-drop, ~1 минута)

1. https://dash.cloudflare.com → Workers & Pages → Create → Pages → Upload assets
2. Перетащить **папку** `landing/` (НЕ только `index.html`)
3. Назвать проект `wtp-bw` → Deploy
4. Получится URL вида `wtp-bw.pages.dev`. Можно прицепить кастомный домен `bw.wtp.ae` через Cloudflare DNS.

### Вариант 2 — Netlify (drag-and-drop)

1. https://app.netlify.com/drop → перетащить папку `landing/`
2. Получится URL вида `wtp-bw-xxxxx.netlify.app`
3. Domain settings → custom domain → `bw.wtp.ae`

### Вариант 3 — на существующий wtp.ae

Если у wtp.ae есть статический хостинг (Vercel / Netlify / nginx):

```bash
# Скопировать файл как /bw/index.html
scp index.html user@wtp.ae:/var/www/wtp.ae/bw/index.html
# или коммит в репо wtp.ae в подпапку /bw/
```

Финальный URL: `https://wtp.ae/bw`

---

## После деплоя — smoke-тест

- [ ] Открыть на телефоне (реальном, не эмуляторе) — hero выглядит ок
- [ ] Кликнуть «Написать Оле» → открывается Telegram-app
- [ ] Заполнить форму → проверить, что письмо/уведомление пришло в Formspree inbox
- [ ] Открыть страницу с UTM (`?utm_source=tg&utm_medium=ads&utm_campaign=may6`) → submit → проверить, что utm-поля в письме заполнены
- [ ] Lighthouse: Performance ≥ 95, LCP < 2.5s (без images = легко проходит)
- [ ] `<meta name="robots" content="noindex,nofollow">` присутствует — Google не индексирует

---

## Что НЕ нужно делать

- Не подключать Google Fonts через `<link>` — Inter уже грузится с rsms.me, это быстрее
- Не добавлять JS-фреймворк (React/Vue) — на одной странице не нужно
- Не превращать форму в multi-step — 3 поля = идеальная длина для warmup-аудитории
- Не ставить countdown / FOMO — community-tone не переносит давление (см. `01-constitution.md` NEVER §5)

---

## Связанные файлы

- `01-constitution.md` — tone, MUST/NEVER
- `04-olga-anketa.md` — позиционирование Оли (Client Success Manager)
- `08-funnel-architecture.md` — раздел 5 «Канал 6 — Лендинг» и раздел 3 «Operational Ladder»
- `06-warmup-roadmap.md` — таймлайн warmup-кампании
