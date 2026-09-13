# Lớp học cô Ngân

App quản lý lớp học tiểu học: điểm danh, hồ sơ học sinh, điểm/nhận xét,
thời khoá biểu — và một link riêng cho mỗi học sinh để gửi phụ huynh
qua Zalo, phụ huynh xem được (không cần đăng nhập) nhưng không sửa được.

- Cô đăng nhập bằng email/mật khẩu để quản lý.
- Mỗi học sinh có 1 link dạng `.../hs/xxxxx` — copy trong mục "Học sinh".
- Dữ liệu lưu trên Supabase (miễn phí ở quy mô 1 lớp), không mất khi tắt máy.

Làm theo đúng thứ tự 3 phần dưới đây: **Supabase → biến môi trường → Vercel**.
Toàn bộ đều thao tác bằng chuột trên trình duyệt, không cần biết lập trình.

---

## Phần 1 — Tạo database trên Supabase (khoảng 5 phút)

1. Vào https://supabase.com → **Start your project** → đăng nhập bằng GitHub hoặc email.
2. Bấm **New project**. Điền:
   - **Name**: `lop-hoc-co-ngan` (tuỳ ý)
   - **Database Password**: đặt một mật khẩu mạnh, **lưu lại** (không dùng để đăng nhập app, chỉ để phòng trường hợp cần truy cập trực tiếp database)
   - **Region**: chọn Singapore (gần Việt Nam nhất)
   - Bấm **Create new project**, đợi khoảng 1-2 phút để khởi tạo.
3. Vào mục **SQL Editor** (biểu tượng ở thanh bên trái) → **New query**.
4. Mở file `supabase/schema.sql` trong bộ code này, copy toàn bộ nội dung,
   dán vào ô SQL Editor, rồi bấm **Run**. Nếu thấy "Success. No rows returned"
   là đã tạo xong toàn bộ bảng dữ liệu.
5. Tạo tài khoản đăng nhập cho cô: vào mục **Authentication** → **Users** →
   **Add user** → **Create new user**. Điền email và mật khẩu cô sẽ dùng để
   đăng nhập app, tick **Auto Confirm User**, bấm **Create user**.

## Phần 2 — Lấy 2 thông tin kết nối

1. Vào **Project Settings** (biểu tượng bánh răng) → **Data API**.
2. Ghi lại 2 giá trị:
   - **Project URL** (dạng `https://xxxxxxxx.supabase.co`)
   - **anon public key** (chuỗi dài bắt đầu bằng `eyJ...`)

Hai giá trị này **không phải mật khẩu bí mật** — chúng được thiết kế để dùng
công khai ở phía trình duyệt, vì mọi quyền truy cập dữ liệu thật đã được
kiểm soát bằng RLS (Row Level Security) đã tạo ở bước SQL phía trên.

## Phần 3 — Đưa app lên mạng bằng Vercel (khoảng 5 phút)

**Cách dễ nhất — không cần cài gì trên máy:**

1. Vào https://vercel.com → đăng nhập (nên chọn **Continue with GitHub**).
2. Nếu bộ code chưa có trên GitHub: vào https://github.com/new → tạo một
   repository mới (Private hoặc Public đều được) → làm theo hướng dẫn của
   GitHub để tải (upload) toàn bộ thư mục `lop-hoc-co-ngan` này lên
   (GitHub có nút "uploading an existing file" cho phép kéo-thả cả thư mục).
3. Quay lại Vercel → **Add New** → **Project** → chọn repository vừa tạo →
   **Import**.
4. Ở bước cấu hình, mở mục **Environment Variables**, thêm đúng 2 dòng:
   - `NEXT_PUBLIC_SUPABASE_URL` = Project URL đã lấy ở Phần 2
   - `NEXT_PUBLIC_SUPABASE_ANON_KEY` = anon public key đã lấy ở Phần 2
5. Bấm **Deploy**. Đợi 1-2 phút, Vercel sẽ đưa ra một link dạng
   `https://lop-hoc-co-ngan.vercel.app` — đây là link app thật, cô có thể
   mở trên điện thoại hoặc máy tính bất kỳ.

Từ giờ, mỗi khi cô cần cập nhật code, chỉ cần cập nhật lại file trên GitHub —
Vercel sẽ tự động build và cập nhật link, không cần làm lại từ đầu.

---

## Dùng thử ở máy cá nhân (không bắt buộc)

Nếu muốn xem thử trước khi đưa lên mạng:

```bash
npm install
cp .env.example .env.local   # rồi điền 2 giá trị Supabase vào .env.local
npm run dev
```

Mở http://localhost:3000

## Cấu trúc thư mục (để tham khảo khi cần chỉnh sửa thêm)

- `supabase/schema.sql` — toàn bộ cấu trúc database
- `app/` — các trang: `dashboard`, `hoc-sinh`, `diem-danh`, `thoi-khoa-bieu`,
  `hs/[code]` (trang phụ huynh xem, không cần đăng nhập), `login`
- `lib/actions/` — các thao tác ghi dữ liệu (thêm học sinh, lưu điểm danh...)
- `components/` — các khối giao diện dùng lại (form, nút, sidebar...)

## Nếu muốn nhờ AI chỉnh sửa thêm sau này

Có thể dán nguyên văn thư mục này vào Claude Code và mô tả thay đổi mong
muốn, ví dụ: "thêm cột xếp loại hạnh kiểm vào trang học sinh" — code đã có
cấu trúc rõ ràng, dễ mở rộng theo từng phần trên.
