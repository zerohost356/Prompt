# tempmailedu.io.vn — Design Kit: Khung Inbox Cuộn & Bảng Màu

> Tài liệu thiết kế chi tiết cho giao diện inbox của **tempmailedu.io.vn**:
> khung ô cuộn (scrollable inbox frame) hiệu ứng glass + gradient, bảng màu đầy đủ,
> animation, và code copy-paste chạy ngay, kết nối thẳng vào Cloudflare Worker API hiện có.

---

## Mục lục

1. [Tổng quan](#1-tổng-quan)
2. [Bảng màu chi tiết](#2-bảng-màu-chi-tiết)
3. [Typography](#3-typography)
4. [Kiến trúc khung ô cuộn](#4-kiến-trúc-khung-ô-cuộn)
5. [Custom Scrollbar](#5-custom-scrollbar)
6. [Code hoàn chỉnh](#6-code-hoàn-chỉnh)
7. [Animations](#7-animations)
8. [Trạng thái UI](#8-trạng-thái-ui)
9. [API Reference đầy đủ](#9-api-reference-đầy-đủ)
10. [Ghi chú tích hợp API](#10-ghi-chú-tích-hợp-api)

---

## 1. Tổng quan

| Thuộc tính | Giá trị |
|---|---|
| Phong cách | Dark glassmorphism + neon gradient (indigo → fuchsia) |
| Font chính | Plus Jakarta Sans (UI) · JetBrains Mono (email/code) |
| Framework CSS | Tailwind CDN + custom CSS layer |
| Điểm nhấn | Khung inbox cuộn mượt, scrollbar gradient phát sáng, header dính (sticky) blur, hàng email hover trượt |

```
┌─────────────────────────────────────────────┐
│  ◉ HEADER STICKY (blur, viền gradient)      │
│    user@tempmailedu.io.vn        [copy][↻]  │
├─────────────────────────────────────────────┤
│ ┌─────────────────────────────────────────┐ │
│ │ ░ HÀNG EMAIL 1          10:24  24/08    │ │
│ │ ░ HÀNG EMAIL 2  ← hover: trượt + glow   │ │
│ │ ░ HÀNG EMAIL 3                          │ │
│ │ ▒ ▒ CUỘN Ở ĐÂY — scrollbar gradient     │ │
│ │ ▒ ▒ thumb indigo→fuchsia, track mờ      │ │
│ └─────────────────────────────────────────┘ │
│  FOOTER: "5 thư · tự động làm mới 10s"      │
└─────────────────────────────────────────────┘
```

---

## 2. Bảng màu chi tiết

### 2.1 Nền & Bề mặt (Backgrounds)

| Token | Hex | RGB | Tailwind | Dùng cho |
|---|---|---|---|---|
| `--bg-base` | `#020617` | `2 6 23` | `slate-950` | Nền trang |
| `--bg-card` | `rgba(15,23,42,.72)` | `15 23 42` | `slate-900/70` | Thẻ inbox (kèm blur) |
| `--bg-elevated` | `#0f172a` | `15 23 42` | `slate-900` | Modal, dropdown |
| `--bg-row` | `transparent` | — | — | Hàng email mặc định |
| `--bg-row-hover` | `rgba(30,41,59,.55)` | `30 41 59` | `slate-800/50` | Hover hàng email |
| `--bg-row-active` | `rgba(49,46,129,.35)` | `49 46 129` | `indigo-950/40` | Hàng đang mở |

### 2.2 Viền & Phân cách (Borders)

| Token | Giá trị | Dùng cho |
|---|---|---|
| `--border-subtle` | `rgba(148,163,184,.12)` | Phân cách hàng (`divide-slate-400/10`) |
| `--border-frame` | `rgba(99,102,241,.28)` | Viền khung inbox |
| `--border-glow` | gradient `#6366f1 → #a855f7 → #ec4899` | Viền khung khi có thư mới |

### 2.3 Màu chủ đạo (Brand)

| Token | Hex | Tailwind | Dùng cho |
|---|---|---|---|
| `--primary-300` | `#a5b4fc` | `indigo-300` | Icon phụ |
| `--primary-400` | `#818cf8` | `indigo-400` | Link, tiêu đề hover |
| `--primary-500` | `#6366f1` | `indigo-500` | Nút chính, scrollbar |
| `--primary-600` | `#4f46e5` | `indigo-600` | Nhấn nút (pressed) |
| `--accent-purple` | `#a855f7` | `purple-500` | Điểm gradient |
| `--accent-pink` | `#ec4899` | `pink-500` | Đầu gradient |

**Gradient thương hiệu:**

```css
--gradient-brand: linear-gradient(135deg, #6366f1 0%, #a855f7 50%, #ec4899 100%);
--gradient-brand-soft: linear-gradient(135deg,
    rgba(99,102,241,.18) 0%, rgba(168,85,247,.14) 50%, rgba(236,72,153,.10) 100%);
```

### 2.4 Trạng thái (Semantic)

| Token | Hex | Tailwind | Dùng cho |
|---|---|---|---|
| `--success` | `#10b981` | `emerald-500` | Toast thành công, badge "đã đọc" |
| `--warning` | `#f59e0b` | `amber-500` | Cảnh báo domain lạ |
| `--danger` | `#ef4444` | `red-500` | Toast lỗi, nút xóa |
| `--info` | `#38bdf8` | `sky-400` | Toast thông tin, spinner |

### 2.5 Chữ (Text hierarchy)

| Token | Hex | Tailwind | Dùng cho |
|---|---|---|---|
| `--text-primary` | `#f1f5f9` | `slate-100` | Tiêu đề email, địa chỉ mailbox |
| `--text-secondary` | `#cbd5e1` | `slate-300` | Người gửi |
| `--text-muted` | `#94a3b8` | `slate-400` | Timestamp, đếm thư |
| `--text-faint` | `#64748b` | `slate-500` | Placeholder, footer |
| `--text-brand` | `#818cf8` | `indigo-400` | Từ khóa highlight |

### 2.6 Hiệu ứng đặc biệt

```css
/* Ánh sáng viền khung khi có thư đến */
--glow-new-mail: 0 0 0 1px rgba(99,102,241,.45),
                 0 0 32px rgba(99,102,241,.22),
                 inset 0 1px 0 rgba(255,255,255,.06);

/* Đổ bóng thẻ */
--shadow-card: 0 24px 64px -16px rgba(2,6,23,.85),
               0 0 0 1px var(--border-frame);

/* Scanline chạy trên đầu khung khi đang tải */
--scan-gradient: linear-gradient(90deg, transparent,
                 rgba(129,140,248,.75), transparent);
```

---

## 3. Typography

| Vai trò | Font | Weight | Size | Letter-spacing |
|---|---|---|---|---|
| Địa chỉ mailbox | JetBrains Mono | 700 | `clamp(1rem, 2.5vw, 1.35rem)` | `-0.02em` |
| Tiêu đề email | Plus Jakarta Sans | 600 | `0.92rem` | `0` |
| Người gửi | Plus Jakarta Sans | 700 | `0.95rem` | `0` |
| Timestamp | JetBrains Mono | 400 | `0.72rem` | `0.02em` |
| Footer/nhãn nhỏ | Plus Jakarta Sans | 500 | `0.72rem` | `0.06em` uppercase |

```html
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet">
```

---

## 4. Kiến trúc khung ô cuộn

```
.inbox-shell                  ← viền gradient + shadow, bo góc 20px
 ├── .inbox-header            ← STICKY top-0, backdrop-blur, z-10
 │     ├── .mailbox-address   ← mono, chấm trạng thái xanh pulse
 │     └── .header-actions    ← nút copy / refresh (icon xoay khi load)
 ├── .inbox-scroll            ★ vùng cuộn duy nhất (max-height + overflow-y)
 │     ├── .email-row ×N      ← grid [avatar-dot | nội dung | meta]
 │     └── .empty-state       ← khi 0 thư
 └── .inbox-footer            ← đếm thư + "auto refresh 10s"
```

Nguyên tắc:

- **Chỉ `.inbox-scroll` được cuộn** (`overflow-y:auto`) — header/footer cố định.
- Header dùng `position: sticky` bên trong shell để giữ blur khi danh sách trượt lên.
- Chiều cao khung: `min(62vh, 520px)` để vừa màn hình laptop không cần cuộn trang.
- Cuộn mượt: `scroll-behavior: smooth` + `overscroll-behavior: contain` (chặn scroll chaining ra trang).

---

## 5. Custom Scrollbar

Đây là phần làm nên "cực đẹp": thumb gradient phát sáng, track trong suốt, có hover glow.

```css
/* ===== WebKit (Chrome/Edge/Safari) ===== */
.inbox-scroll {
  scrollbar-width: thin;                       /* Firefox */
  scrollbar-color: #6366f1 transparent;
}
.inbox-scroll::-webkit-scrollbar { width: 10px; }

.inbox-scroll::-webkit-scrollbar-track {
  margin: 10px 0;
  border-radius: 999px;
  background: rgba(148,163,184,.06);
  box-shadow: inset 0 0 6px rgba(2,6,23,.6);
}

.inbox-scroll::-webkit-scrollbar-thumb {
  border-radius: 999px;
  border: 2.5px solid transparent;             /* tạo khoảng thở quanh thumb */
  background: linear-gradient(180deg, #6366f1, #a855f7, #ec4899)
              border-box;
  box-shadow: 0 0 10px rgba(99,102,241,.45);
}

.inbox-scroll::-webkit-scrollbar-thumb:hover {
  background: linear-gradient(180deg, #818cf8, #c084fc, #f472b6)
              border-box;
  box-shadow: 0 0 16px rgba(168,85,247,.65);
}
```

> Mẹo: `border-box` + `border: 2.5px solid transparent` là kỹ thuật làm thumb
> mảnh hơn track mà vẫn giữ gradient — đẹp hơn nhiều so với thumb đặc.

---

## 6. Code hoàn chỉnh

Copy nguyên khối dưới đây thay thế `inboxCard` hiện tại. Đã dùng đúng token màu ở mục 2 và hook sẵn hàm `fetchEmails()` của site.

```html
<!-- ============ INBOX SHELL ============ -->
<div id="inboxCard" class="hidden">
  <div class="inbox-shell">

    <!-- HEADER STICKY -->
    <div class="inbox-header">
      <span class="status-dot"></span>
      <span id="displayEmail" class="mailbox-address">user@tempmailedu.io.vn</span>
      <div class="header-actions">
        <button onclick="handleCopy()" title="Sao chép">
          <svg><!-- icon copy --></svg>
        </button>
        <button onclick="handleRefresh()" title="Làm mới">
          <svg id="refreshIcon"><!-- icon refresh --></svg>
        </button>
      </div>
    </div>

    <!-- VÙNG CUỘN -->
    <div id="inboxContainer" class="inbox-scroll">
      <div id="emailList"></div>
    </div>

    <!-- FOOTER -->
    <div class="inbox-footer">
      <span id="emailCount">0 thư</span>
      <span class="footer-sep">·</span>
      <span>tự động làm mới mỗi 10s</span>
    </div>
  </div>
</div>

<style>
/* ---------- Shell: viền gradient + bóng ---------- */
.inbox-shell {
  position: relative;
  max-width: 680px;
  margin-inline: auto;
  border-radius: 20px;
  background: var(--bg-card);
  backdrop-filter: blur(14px);
  box-shadow: var(--shadow-card);
  overflow: hidden;
}
.inbox-shell::before {                       /* viền gradient 1px */
  content: "";
  position: absolute; inset: 0;
  border-radius: inherit;
  padding: 1px;
  background: var(--gradient-brand);
  opacity: .38;
  -webkit-mask: linear-gradient(#000 0 0) content-box, linear-gradient(#000 0 0);
  -webkit-mask-composite: xor;
          mask-composite: exclude;
  pointer-events: none;
}

/* ---------- Header sticky ---------- */
.inbox-header {
  position: sticky; top: 0; z-index: 10;
  display: flex; align-items: center; gap: 10px;
  padding: 16px 20px;
  background: rgba(2,6,23,.66);
  backdrop-filter: blur(12px);
  border-bottom: 1px solid var(--border-subtle);
}
.mailbox-address {
  flex: 1; min-width: 0;
  font-family: "JetBrains Mono", monospace;
  font-weight: 700; font-size: clamp(1rem, 2.5vw, 1.3rem);
  letter-spacing: -.02em;
  color: var(--text-primary);
  overflow: hidden; text-overflow: ellipsis; white-space: nowrap;
}
.status-dot {
  width: 9px; height: 9px; border-radius: 999px;
  background: var(--success);
  box-shadow: 0 0 0 0 rgba(16,185,129,.55);
  animation: pulse-dot 2s infinite;
  flex-shrink: 0;
}
.header-actions { display: flex; gap: 8px; }
.header-actions button {
  display: grid; place-items: center;
  width: 38px; height: 38px; border-radius: 12px;
  color: var(--text-muted);
  background: rgba(30,41,59,.45);
  border: 1px solid var(--border-subtle);
  transition: all .18s ease;
}
.header-actions button:hover {
  color: var(--primary-300);
  border-color: rgba(99,102,241,.45);
  transform: translateY(-1px);
  box-shadow: 0 4px 14px rgba(99,102,241,.25);
}

/* ---------- Vùng cuộn (chi tiết mục 5) ---------- */
.inbox-scroll {
  max-height: min(62vh, 520px);
  overflow-y: auto;
  scroll-behavior: smooth;
  overscroll-behavior: contain;
  /* …paste toàn bộ block ::-webkit-scrollbar ở mục 5… */
}

/* ---------- Hàng email ---------- */
.email-row {
  display: grid;
  grid-template-columns: auto 1fr auto;
  gap: 14px; align-items: center;
  padding: 15px 20px;
  cursor: pointer;
  border-bottom: 1px solid var(--border-subtle);
  transition: background .16s ease, transform .16s ease, box-shadow .16s ease;
}
.email-row:hover {
  background: var(--bg-row-hover);
  transform: translateX(4px);
  box-shadow: inset 3px 0 0 var(--primary-500);
}
.email-row:last-child { border-bottom: 0; }

.email-avatar {
  width: 40px; height: 40px; border-radius: 13px;
  display: grid; place-items: center;
  font-weight: 800; font-size: .9rem; color: #fff;
  background: var(--gradient-brand-soft);
  border: 1px solid rgba(99,102,241,.35);
}
.email-from {
  font-weight: 700; font-size: .95rem;
  color: var(--text-secondary);
  overflow: hidden; text-overflow: ellipsis; white-space: nowrap;
}
.email-subject {
  font-size: .88rem; color: var(--text-primary);
  opacity: .92; margin-top: 2px;
  overflow: hidden; text-overflow: ellipsis; white-space: nowrap;
}
.email-meta { text-align: right; flex-shrink: 0; }
.email-time  { font-family:"JetBrains Mono",monospace; font-size:.72rem; color:var(--text-muted); }
.email-date  { font-family:"JetBrains Mono",monospace; font-size:.62rem; color:var(--text-faint); }

/* ---------- Footer ---------- */
.inbox-footer {
  display: flex; justify-content: center; align-items: center; gap: 8px;
  padding: 11px 20px;
  font-size: .72rem; letter-spacing: .05em;
  color: var(--text-faint);
  background: rgba(2,6,23,.5);
  border-top: 1px solid var(--border-subtle);
}
.footer-sep { color: var(--primary-500); }

/* ---------- Skeleton loading ---------- */
.skeleton-row { padding:15px 20px; border-bottom:1px solid var(--border-subtle); display:flex; gap:14px; }
.skeleton-block {
  border-radius: 8px;
  background: linear-gradient(90deg, rgba(51,65,85,.35) 25%, rgba(71,85,105,.55) 50%, rgba(51,65,85,.35) 75%);
  background-size: 200% 100%;
  animation: shimmer 1.3s infinite;
}

/* ---------- Scanline khi đang tải ---------- */
.inbox-scroll.loading { position: relative; }
.inbox-scroll.loading::after {
  content: "";
  position: sticky; top: 0; left: 0; right: 0;
  height: 2px; display: block;
  background: var(--scan-gradient);
  animation: scan-x 1.6s ease-in-out infinite;
}
</style>
```

Template render một hàng email (thay cho `renderEmails` cũ):

```js
container.innerHTML = list.map((email, idx) => `
  <div class="email-row" onclick="openEmail(${idx})">
    <div class="email-avatar">${email.from.charAt(0).toUpperCase()}</div>
    <div style="min-width:0">
      <div class="email-from">${escapeHtml(email.from)}</div>
      <div class="email-subject">${escapeHtml(email.subject)}</div>
    </div>
    <div class="email-meta">
      <div class="email-time">${fmtTime(email.createdAt)}</div>
      <div class="email-date">${fmtDate(email.createdAt)}</div>
    </div>
  </div>`).join("");
```

---

## 7. Animations

```css
@keyframes pulse-dot {
  0%   { box-shadow: 0 0 0 0 rgba(16,185,129,.55); }
  70%  { box-shadow: 0 0 0 9px rgba(16,185,129,0); }
  100% { box-shadow: 0 0 0 0 rgba(16,185,129,0); }
}
@keyframes shimmer {
  from { background-position: 200% 0; }
  to   { background-position: -200% 0; }
}
@keyframes scan-x {
  0%   { transform: translateX(-100%); }
  100% { transform: translateX(100%); }
}
@keyframes slide-up {           /* toast + hàng mới xuất hiện */
  from { opacity: 0; transform: translateY(14px); }
  to   { opacity: 1; transform: translateY(0); }
}
@keyframes fade-in {
  from { opacity: 0; } to { opacity: 1; }
}
```

Áp dụng cho thư mới (so sánh id đã thấy, giống logic `seenEmails` hiện tại):

```js
if (isNew) row.style.animation = "slide-up .4s ease-out both";
```

---

## 8. Trạng thái UI

| Trạng thái | Hiển thị | Màu |
|---|---|---|
| Rỗng | Icon hộp thư + "Chưa có thư nào" | `--text-faint`, icon opacity .3 |
| Đang tải | Skeleton rows ×4 + scanline | `--bg-row-hover` shimmer |
| Có thư mới | Viền khung đổi sang `--border-glow` + toast | `--success` |
| Lỗi mạng | Toast lỗi + dot chuyển đỏ | `--danger` |
| Hàng được chọn | `inset` viền trái indigo + nền `indigo-950/40` | `--primary-500` |

---

## 9. API Reference đầy đủ

Toàn bộ API đã được probe thực tế (GET/OPTIONS an toàn, không đọc inbox người khác).

### 9.1 Tổng quan

| | |
|---|---|
| **Base URL** | `https://tempmail-email-worker.khohu2220.workers.dev` |
| **Loại** | Cloudflare Worker (serverless) |
| **Auth** | Không — hoàn toàn public, CORS `*` |
| **Methods hỗ trợ** | `GET`, `OPTIONS` duy nhất |
| **Content-Type response** | `application/json` (dữ liệu) · `text/plain;charset=UTF-8` (catch-all) |

### 9.2 Routing map (hành vi thật của worker)

```
REQUEST ──► path == /api/emails ?
              ├── CÓ  ──► handler đọc tham số ?address=
              └── KHÔNG ──► CATCH-ALL: 200 text/plain
                            "Worker Temp Mail đang chạy ổn định."
```

> Worker dùng catch-all: **mọi path khác** (`/`, `/api`, `/api/domains`, `/api/stats`,
> `/health`…) đều trả cùng một message 43 bytes với HTTP 200.
> Chỉ có đúng một endpoint chức năng duy nhất.

| # | Method | Path | Chức năng | Đã verify |
|---|---|---|---|---|
| 1 | GET | `/api/emails?address=…` | Lấy toàn bộ inbox theo địa chỉ | HTTP 200/400 + JSON |
| 2 | OPTIONS | `/api/emails` | CORS preflight | Headers đầy đủ |
| — | GET | *(mọi path khác)* | Health-check catch-all | 200 text |

### 9.3 Endpoint chính — `GET /api/emails`

#### Request

```
GET /api/emails?address={fullEmailAddress}
```

| Query param | Bắt buộc | Kiểu | Mô tả |
|---|---|---|---|
| `address` | Có | string | Địa chỉ mailbox đầy đủ, ví dụ `user@tempmailedu.io.vn`. Phải đặt tên đúng `address` — dùng `email=` sẽ lỗi 400 |

#### Response

**Thành công — `200 application/json`:**

```json
[
  {
    "subject": "Your verification code",
    "from": "\"OpenAI\" <noreply@openai.com>",
    "raw_email": "MIME raw đầy đủ (headers + body)...",
    "createdAt": "2026-08-24T05:20:11.000Z"
  }
]
```

Schema field (frontend chấp nhận nhiều tên thay thế):

| Field | Bắt buộc? | Biến thể được frontend fallback | Ghi chú |
|---|---|---|---|
| `subject` | — | — | Có thể là quoted-printable/MIME-encoded → client tự decode |
| `from` | — | `sender` | MIME-encoded (`=?UTF-8?B?…?= <mail@x>`) → client tự decode + trích `<...>` |
| `raw_email` | — | `body` → `html` | Raw MIME multipart; client tự tách boundary/header + decode QP/base64 |
| `createdAt` | — | `created_at` | ISO string; client tự thêm `Z` nếu thiếu timezone |

**Lỗi thiếu param — `400 application/json`:**

```json
{ "error": "Thiếu địa chỉ email (tham số ?address=...)" }
```

**Inbox rỗng / address không tồn tại — `200 []`:**

```json
[]
```

#### Bảng hành vi đã probe

| Trường hợp gửi | Status | Body | Nhận xét |
|---|---:|---|---|
| Thiếu `?address=` | `400` | `{"error":"Thiếu địa chỉ…"}` | Param bắt buộc |
| Dùng `?email=` thay vì `?address=` | `400` | lỗi như trên | Tên param sai → coi như thiếu |
| `?address=not-an-email` | `200` | `[]` | **Không validate format** |
| Address domain lạ/không tồn tại | `200` | `[]` | Không check domain whitelist ở tầng API |

### 9.4 CORS & Method policy (từ preflight OPTIONS)

```
access-control-allow-origin:  *
access-control-allow-headers: Content-Type
access-control-allow-methods: GET, OPTIONS
```

→ Gọi được từ **bất kỳ origin nào**; không có cookie/token; chỉ đọc, không có API gửi/xóa thư qua HTTP.

### 9.5 Ví dụ sử dụng

#### curl

```bash
# Inbox hợp lệ
curl "https://tempmail-email-worker.khohu2220.workers.dev/api/emails?address=user1@tempmailedu.io.vn"

# Preflight check
curl -X OPTIONS -D - -o /dev/null \
  "https://tempmail-email-worker.khohu2220.workers.dev/api/emails"
```

#### JavaScript — poller hoàn chỉnh (retry + backoff + parse an toàn)

```js
const WORKER = "https://tempmail-email-worker.khohu2220.workers.dev/api/emails";

async function fetchInbox(address, { retries = 2, timeoutMs = 8000 } = {}) {
  for (let i = 0; i <= retries; i++) {
    const ctrl = new AbortController();
    const timer = setTimeout(() => ctrl.abort(), timeoutMs);
    try {
      const res = await fetch(`${WORKER}?address=${encodeURIComponent(address)}`, {
        signal: ctrl.signal,
      });
      clearTimeout(timer);
      if (!res.ok) throw new Error(`HTTP ${res.status}`);
      const data = await res.json();
      return Array.isArray(data) ? data : data.emails ?? [];
    } catch (e) {
      clearTimeout(timer);
      if (i === retries) throw e;
      await new Promise(r => setTimeout(r, 1000 * (i + 1))); // backoff 1s, 2s
    }
  }
}

// Dùng trong vòng poll 10s của khung inbox:
setInterval(async () => {
  const mails = await fetchInbox("user1@tempmailedu.io.vn").catch(() => []);
  renderEmails(mails.map(m => ({
    subject: m.subject ?? "(Không tiêu đề)",
    from:    m.from ?? m.sender ?? "",
    content: m.raw_email ?? m.body ?? m.html ?? "",
    createdAt: m.createdAt ?? m.created_at,
  })));
}, 10_000);
```

### 9.6 Ví dụ Node.js cho từng endpoint

Chạy được ngay với **Node.js 18+** (global `fetch`), không cần cài thêm package nào.

#### Endpoint 1 — `GET /api/emails?address=…` (lấy inbox)

```js
// file: get-inbox.mjs
const WORKER = "https://tempmail-email-worker.khohu2220.workers.dev";

/**
 * Lấy toàn bộ inbox của một địa chỉ.
 * @returns {Promise<Array<{subject?, from?, sender?, raw_email?, body?, html?, createdAt?, created_at?}>>}
 */
async function getEmails(address, { timeoutMs = 8000 } = {}) {
  const ctrl = new AbortController();
  const timer = setTimeout(() => ctrl.abort(new Error("timeout")), timeoutMs);
  try {
    const res = await fetch(`${WORKER}/api/emails?address=${encodeURIComponent(address)}`, {
      method: "GET",
      signal: ctrl.signal,
      headers: { accept: "application/json" },
    });

    // worker có thể trả text (catch-all) → kiểm tra trước khi parse
    const ct = res.headers.get("content-type") || "";
    if (!ct.includes("application/json")) {
      throw new Error(`Unexpected content-type: ${ct}`);
    }

    if (res.status === 400) {
      const err = await res.json();
      throw new Error(`Worker lỗi: ${err.error}`);
    }
    if (!res.ok) throw new Error(`HTTP ${res.status}`);

    return res.json(); // [] hoặc mảng email
  } finally {
    clearTimeout(timer);
  }
}

// --- chạy thử ---
const mails = await getEmails("user1@tempmailedu.io.vn");
console.log(`Có ${mails.length} thư`);
for (const m of mails) {
  console.log("-", m.subject, "| từ:", m.from ?? m.sender, "| lúc:", m.createdAt ?? m.created_at);
}
```

```bash
node get-inbox.mjs
# Có 2 thư
# - Your verification code | từ: noreply@example.com | lúc: 2026-08-24T05:20:11.000Z
```

#### Endpoint 1b — Đợi thư mới (poll đến khi có kết quả)

Hữu ích khi đăng ký dịch vụ và chờ mail kích hoạt:

```js
// file: wait-for-mail.mjs
const sleep = (ms) => new Promise((r) => setTimeout(r, ms));

async function waitForMail(address, {
  intervalMs = 10_000,   // giống frontend thật (REFRESH_INTERVAL)
  maxWaitMs = 3 * 60_000,
  match,                 // lọc tùy chọn: (mail) => boolean
} = {}) {
  const deadline = Date.now() + maxWaitMs;
  while (Date.now() < deadline) {
    const mails = await getEmails(address);          // hàm ở Endpoint 1
    const hit = typeof match === "function" ? mails.find(match) : mails[0];
    if (hit) return hit;
    console.log(`Chưa có thư… thử lại sau ${intervalMs / 1000}s`);
    await sleep(intervalMs);
  }
  throw new Error(`Không nhận được thư trong ${maxWaitMs / 1000}s`);
}

const mail = await waitForMail("user1@tempmailedu.io.vn", {
  match: (m) => /verification|otp|mã/i.test(m.subject ?? ""),
});
console.log("Nhận được:", mail.subject);
```

#### Endpoint 2 — `OPTIONS /api/emails` (kiểm tra CORS/preflight)

```js
// file: check-cors.mjs
async function checkCors() {
  const res = await fetch(
    "https://tempmail-email-worker.khohu2220.workers.dev/api/emails",
    { method: "OPTIONS" }
  );
  const policy = {
    status: res.status,                                        // 200
    allowOrigin: res.headers.get("access-control-allow-origin"),  // *
    allowMethods: res.headers.get("access-control-allow-methods"), // GET, OPTIONS
    allowHeaders: res.headers.get("access-control-allow-headers"), // Content-Type
  };
  console.table(policy);
  return policy;
}

await checkCors();
```

#### Endpoint 3 — Catch-all path khác (health-check)

Mọi path lạ đều trả `200 text/plain` → dùng làm ping rẻ nhất:

```js
// file: health-check.mjs
async function isWorkerAlive() {
  try {
    const res = await fetch(
      "https://tempmail-email-worker.khohu2220.workers.dev/",
      { signal: AbortSignal.timeout(5000) }
    );
    const text = await res.text();
    // chữ ký phản hồi catch-all thật của worker
    return res.ok && text.includes("đang chạy ổn định");
  } catch {
    return false;
  }
}

console.log(await isWorkerAlive()); // true khi worker sống
```

> Chú ý: `/api/domains`, `/api/stats`… **không phải endpoint thật** — chúng rơi vào
> catch-all này (xem mục 9.2), nên đừng gọi để lấy dữ liệu.

#### Bonus — Express proxy (che worker khỏi client)

Nếu bạn dựng frontend riêng nhưng muốn ẩn URL worker / thêm rate-limit của mình:

```js
// file: server.mjs  —  npm i express
import express from "express";

const app = express();
const WORKER = "https://tempmail-email-worker.khohu2220.workers.dev";

app.get("/api/inbox", async (req, res) => {
  const address = String(req.query.address ?? "");
  if (!address || !/^[a-z0-9._-]+@[a-z0-9.-]+$/i.test(address)) {
    return res.status(400).json({ error: "address không hợp lệ" });
  }
  try {
    const r = await fetch(`${WORKER}/api/emails?address=${encodeURIComponent(address)}`);
    if (!r.headers.get("content-type")?.includes("application/json")) {
      return res.status(502).json({ error: "worker unavailable" });
    }
    res.status(r.status).json(await r.json());
  } catch {
    res.status(504).json({ error: "worker timeout" });
  }
});

app.listen(3000, () => console.log("proxy :3000"));
// GET http://localhost:3000/api/inbox?address=user1@tempmailedu.io.vn
```

### 9.7 Lưu ý bảo mật khi dùng API này

| Quan sát | Chi tiết | Ảnh hưởng |
|---|---|---|
| **Không auth** | Biết địa chỉ = đọc được toàn bộ inbox | Đặc tính temp mail — đừng nhận nội dung nhạy cảm |
| **CORS `\*`** | Bất kỳ website nào cũng query được | Không thể giới hạn theo domain |
| **Catch-all 200** | Path sai vẫn trả 200 text | Nên kiểm tra content-type trước khi parse JSON |
| **Không validate** | Format/domain address không được kiểm tra | Client phải tự sanitize trước khi hiển thị |

---

## 10. Ghi chú tích hợp API

Khung này cắm thẳng vào backend hiện có của site, **không cần đổi gì backend**:

Khung inbox cắm thẳng vào worker API ở mục 9, **không cần đổi gì backend**:

```js
const CONFIG = {
  API_URL: "https://tempmail-email-worker.khohu2220.workers.dev/api/emails",
  REFRESH_INTERVAL: 10_000,
};
// GET {API_URL}?address=<user@domain>  — chi tiết đầy đủ tại mục 9.3
```

- Worker trả CORS `*`, method `GET/OPTIONS` → gọi trực tiếp từ client như hiện tại.
- Polling 10s giữ nguyên; có thể nâng cấp sau bằng SSE nhưng worker hiện chỉ hỗ trợ GET/OPTIONS.
- Khi render nội dung email HTML vẫn phải đưa vào `<iframe sandbox>` như bản hiện tại — **không** render thẳng vào DOM chính.

---

*Tài liệu sinh từ phân tích source thật của tempmailedu.io.vn (static frontend + Cloudflare Worker).*
