# 🎨 DESIGN SPECIFICATION: App Chat Nội Bộ Doanh Nghiệp

**Version:** 1.0  
**Ngày:** 28/05/2026  
**Thiết bị mục tiêu:** Mobile App (iOS / Android) & Web / Desktop Adaptation  

---

## 1. Tổng Quan (Overview)

- **Mục tiêu UX:** 
  - **Chuyên nghiệp và Tin cậy:** Giao diện tối giản, tập trung vào công việc, tạo cảm giác an toàn và tuân thủ.
  - **Thao tác nhanh:** Tối ưu hóa số lượt chạm để gửi tin nhắn, tìm kiếm tài liệu.
  - **Liền mạch đa thiết bị:** Đồng bộ realtime tức thời trạng thái tin nhắn giữa điện thoại và máy tính.
- **User Flow tóm tắt:**
  - *Luồng đăng nhập:* Đăng nhập (Số điện thoại) → Nhận SMS OTP → Xác thực OTP → Vào Màn hình chính (Danh sách hội thoại).
  - *Luồng chat:* Chọn Hội thoại → Đọc tin nhắn → Phản hồi.

---

## 2. Chi Tiết Từng Màn Hình (Screen Details)

### Screen 1: Màn hình Đăng nhập & Xác thực OTP (Authentication Screen)
**Mục đích:** Xác minh danh tính nhân viên và cho phép truy cập an toàn vào hệ thống.

#### A. Phác họa Giao diện (Wireframe - Visual Sequence)
<details>
<summary>📱 Đăng nhập <code>auth-login</code></summary>

**Mô tả:** Màn hình đăng nhập bằng số điện thoại đã đăng ký trong hồ sơ nhân viên và nhập mã OTP 6 số gửi về thiết bị để xác thực.

```text
┌─────────────────────────────────────────┐
│              Internal Chat              │
│   ┌─────────────────────────────────┐   │
│   │           Đăng nhập             │   │
│   ├─────────────────────────────────┤   │
│   │                                 │   │
│   │   [! Banner lỗi nếu có ]        │   │
│   │                                 │   │
│   │   Số điện thoại                 │   │
│   │   ┌─────────────────────────┐   │   │
│   │   │ [+84] | Nhập số ĐT...   │   │   │
│   │   └─────────────────────────┘   │   │
│   │                                 │   │
│   │   ┌─────────────────────────┐   │   │
│   │   │        Tiếp tục         │   │   │
│   │   └─────────────────────────┘   │   │
│   │                                 │   │
│   │   *Đăng nhập bằng số điện thoại │   │
│   │    đăng ký với HR để xác thực   │   │
│   └─────────────────────────────────┘   │
└─────────────────────────────────────────┘
```
</details>

#### B. Cấu trúc Layout & Components (Anatomy)
- **Header:** Logo doanh nghiệp + Tiêu đề chào mừng.
- **Body/Main Content:**
  - Ô nhập Số điện thoại kèm nút chọn Mã quốc gia (mặc định +84).
  - Điều khoản sử dụng & Cam kết bảo mật thông tin (Tuân thủ Nghị định 13/2023/NĐ-CP).
  - Nút Primary CTA "Tiếp tục".
  - *Khi chuyển sang bước OTP:* Ô nhập mã OTP 6 chữ số, đồng hồ đếm ngược gửi lại mã (60s), nút quay lại màn hình nhập số điện thoại.
- **Footer:** Hotline hỗ trợ kỹ thuật nội bộ (IT Helpdesk).

#### C. Component & Data
| Component | Loại | Mô tả dữ liệu / Logic | Ràng buộc (Validation) |
|-----------|------|------------------------|-----------------------|
| Ô nhập Số điện thoại | Input Text | Chỉ cho phép nhập ký số, tự động loại bỏ số 0 ở đầu nếu nhập mã quốc gia. | Từ 9 đến 11 ký số |
| Nút "Tiếp tục" | Primary CTA | Gửi số điện thoại lên hệ thống để yêu cầu gửi mã OTP. | Bị vô hiệu hóa (disabled) nếu số điện thoại không hợp lệ. |
| Ô nhập mã OTP | Input Pin | Gồm 6 ô nhập số riêng biệt, tự động chuyển focus sang ô kế tiếp khi gõ. | Chỉ cho phép nhập số, bắt buộc đủ 6 số. |
| Nút "Gửi lại mã" | Text Link | Cho phép gửi lại mã OTP mới sau khi hết thời gian đếm ngược. | Chỉ hiển thị / kích hoạt sau khi đếm ngược về 0s. |

#### D. Trạng thái giao diện (UI States)
- ⚪ **Empty State:** Trường nhập trống, nút "Tiếp tục" ở trạng thái disabled.
- 🔄 **Loading State:** Spinner trên nút "Tiếp tục" khi đang xác thực số điện thoại hoặc OTP.
- 🔴 **Error State:**
  - Số điện thoại không tồn tại trên hệ thống nhân sự: Hiển thị thông báo dưới ô nhập: *"Số điện thoại chưa được đăng ký trên hệ thống nhân sự. Vui lòng liên hệ HR."*
  - Sai mã OTP: Hiển thị viền đỏ quanh các ô nhập OTP và thông báo: *"Mã OTP không chính xác. Bạn còn X lần thử."* (lockout sau 5 lần sai).
- 🟢 **Success State:** Chuyển mượt mà sang màn hình chính với hiệu ứng Fade-in.

#### E. Copywriting (Microcopy)
- Title Đăng nhập: `Đăng nhập hệ thống`
- Helper text Số điện thoại: `Nhập số điện thoại di động được công ty cấp để nhận mã kích hoạt.`
- Helper text OTP: `Chúng tôi đã gửi mã xác thực gồm 6 chữ số đến số điện thoại của bạn.`
- CTA Button: `Tiếp tục` | `Xác nhận OTP`

---

### Screen 2: Màn hình Danh sách Hội thoại (Chat List Screen - Home)
**Mục đích:** Trung tâm điều hướng, hiển thị toàn bộ các cuộc hội thoại làm việc của nhân viên.

#### A. Phác họa Giao diện (Wireframe - Visual Sequence)
<details>
<summary>📱 Danh sách hội thoại <code>chat-list</code></summary>

**Mô tả:** Màn hình chính sau khi đăng nhập, hiển thị các luồng chat, ghim tin nhắn, lọc theo nhãn thư mục công việc và thanh điều hướng bên dưới.

```text
┌─────────────────────────────────────────┐
│ [ (O) ]  Trò chuyện                 [➕]│
├─────────────────────────────────────────┤
│ [🔍 Tìm kiếm đồng nghiệp, nhóm...]      │
├─────────────────────────────────────────┤
│ [ Tất cả ] [ Công việc ] [ Quy trình ]  │
├─────────────────────────────────────────┤
│ [ (O) ] Dự án Chiến Dịch X       10:45  │
│         Nguyễn Văn A: Đã gửi tài... [2] │
├─────────────────────────────────────────┤
│ [ (O) ] Lê Thị B (Nhân sự)       09:30  │
│         Mute  Mời duyệt đơn phép    [1] │
├─────────────────────────────────────────┤
│ [ (O) ] Trợ lý AI                08:15  │
│         Hỏi đáp quy chế nội bộ...       │
├─────────────────────────────────────────┤
│   💬         👥         👤         │
│ Tin nhắn   Danh bạ   Cá nhân       │
└─────────────────────────────────────────┘
```
</details>

#### B. Cấu trúc Layout & Components (Anatomy)
- **Header:**
  - Avatar nhân viên (Click để vào Profile) + Trạng thái hiện diện (chấm xanh Online).
  - Tiêu đề ứng dụng + Thanh tìm kiếm nhanh toàn cục.
  - Icon Tạo mới (Chat 1-1, Tạo Nhóm, Tạo Kênh).
- **Body/Main Content:**
  - Thanh Tab Nhãn/Thư mục (Tất cả, Công việc, Quy trình, Cá nhân, Lưu trữ).
  - Danh sách cuộn các hội thoại (Sắp xếp theo tin mới nhất). Mỗi item gồm: Avatar hội thoại, Tên, Biểu tượng Pin (nếu được ghim), Nội dung tin nhắn cuối cùng, Thời gian, Badge số tin chưa đọc.
- **Footer/Bottom Navigation:**
  - Tin nhắn (Active) | Danh bạ | Cá nhân.

#### C. Component & Data
| Component | Loại | Mô tả dữ liệu / Logic | Ràng buộc (Validation) |
|-----------|------|------------------------|-----------------------|
| Thanh tìm kiếm | Search Bar | Tìm nhanh người dùng, nhóm hoặc nội dung tin nhắn. | Kích hoạt ngay khi nhập ký tự đầu tiên. |
| Danh sách hội thoại | List View | Vuốt sang trái để hiện các action nhanh: Ghim (Pin), Tắt thông báo (Mute), Lưu trữ (Archive). | Hỗ trợ ghim tối đa 5 hội thoại lên đầu. |
| Badge tin chưa đọc | Badge | Hiển thị số lượng tin chưa đọc của hội thoại. | Dạng số tròn đỏ. Nếu tắt thông báo (Mute) thì chuyển sang màu xám. |

#### D. Trạng thái giao diện (UI States)
- ⚪ **Empty State:** Hiển thị hình minh họa hộp thư sạch sẽ + Text: *"Chưa có cuộc hội thoại nào. Bắt đầu chat ngay bằng cách click vào icon Tạo mới."*
- 🔄 **Loading State:** Hiển thị các block Skeleton loading cho danh sách hội thoại khi mở app lần đầu.
- 🔴 **Error State:** Rớt mạng: Hiển thị banner mỏng màu vàng dưới header: *"Đang kết nối lại..."*.
- 🟢 **Success State:** Tin nhắn mới hiển thị realtime ở đầu danh sách với hiệu ứng đẩy nhẹ nhàng.

#### E. Copywriting (Microcopy)
- Search Placeholder: `Tìm kiếm đồng nghiệp, nhóm hoặc tin nhắn...`
- Tabs: `Tất cả` | `Công việc` | `Quy trình` | `Lưu trữ`
- Empty Text: `Không có tin nhắn chưa đọc`

---

### Screen 3: Màn hình Chi tiết Chat (1-1 / Nhóm / AI Chat Screen)
**Mục đích:** Nơi diễn ra các tương tác trao đổi công việc realtime, chia sẻ dữ liệu nghiệp vụ.

#### A. Phác họa Giao diện (Wireframe - Visual Sequence)
<details>
<summary>📱 Chi tiết hội thoại <code>chat-detail</code></summary>

**Mô tả:** Khung chat chi tiết hỗ trợ bong bóng chat, các thẻ quy trình duyệt nhanh (Duyệt/Từ chối) và ô nhập văn bản tích hợp ghi âm.

```text
┌─────────────────────────────────────────┐
│ [ < ]  Lê Thị B (Nhân sự)           [⚙️]│
│        đang hoạt động                   │
├─────────────────────────────────────────┤
│                                   09:28 │
│        ┌────────────────────────────┐   │
│        │ Chào B, xem hộ mình đơn    │   │
│        │ nghỉ phép thứ 6 này nhé.   │   │
│        └────────────────────────────┘   │
│ 09:30                                   │
│ ┌────────────────────────────┐          │
│ │ Đơn xin nghỉ phép thứ 6    │          │
│ │ Trạng thái: Chờ duyệt      │          │
│ │ ┌──────────┐  ┌──────────┐ │          │
│ │ │  Duyệt   │  │ Từ chối  │ │          │
│ │ └──────────┘  └──────────┘ │          │
│ └────────────────────────────┘          │
├─────────────────────────────────────────┤
│ [➕] [ Nhập tin nhắn...        ] [🎙️] [😊]│
└─────────────────────────────────────────┘
```
</details>

#### B. Cấu trúc Layout & Components (Anatomy)
- **Header:**
  - Nút Back.
  - Avatar Cá nhân / Nhóm (Hình tròn, kích thước tiêu chuẩn. Đối với nhóm chưa cài ảnh, hiển thị avatar ghép từ các thành viên).
  - Tên đối phương / Tên nhóm hiển thị nổi bật + Subtitle trạng thái (Online / Lần cuối hoạt động / Số thành viên / "đang nhập...").
  - Các nút tính năng (Gọi thoại, Gọi video) và Icon Menu mở rộng [⚙️]/Thư viện Media.
- **Body/Main Content:**
  - Khung cuộn nội dung tin nhắn. Bong bóng trái (đối phương) và phải (bản thân).
  - Avatar và Tên người gửi: Luôn hiển thị cạnh bong bóng chat của người khác (đặc biệt quan trọng trong chat nhóm).
  - Phân loại hiển thị đa phương tiện:
    - Text thông thường (Rich text, link preview).
    - Sticker tĩnh/động, Emoji kích thước lớn (nếu chỉ gửi 1 emoji duy nhất không kèm text).
    - Đoạn hội thoại đính kèm GIF (tự động lặp lại - autoplay).
    - Tin nhắn thoại (Voice message với waveform).
  - **Context Menu (Khi chuột phải hoặc Nhấn giữ tin nhắn):**
    - Trả lời (Reply)
    - Chuyển tiếp (Forward)
    - Ghim (Pin)
    - Sao chép (Copy text / link)
    - Chọn nhiều tin nhắn (Select)
    - Dịch tự động (Translate)
    - Chỉnh sửa (Edit - giới hạn thời gian/quyền)
    - Thu hồi / Xóa (Delete/Unsend)
    - **Chi tiết đã xem (Message Info):** Hiển thị danh sách những người đã xem tin nhắn và thời gian xem cụ thể (Read Receipts).
- **Footer/Input Area:**
  - Nút Đính kèm `[+]` (Mở rộng menu Menu Attachment kiểu lưới):
    - 📷 Ảnh/Video (Từ thư viện hoặc Camera).
    - 📁 Tệp tài liệu (File).
    - 📊 Bình chọn (Poll/Quiz).
    - 📍 Vị trí (Location / Live Location).
    - 👤 Liên hệ (Contact).
  - Icon Stickers/GIFs/Emojis (Panel tương tác chọn biểu cảm/ảnh động).
  - Ô nhập văn bản (Giãn dòng tự động, @mention). **Nhấn giữ nút Gửi** để mở tuỳ chọn nâng cao: "Gửi hẹn giờ" (Schedule message) hoặc "Gửi không thông báo" (Silent message).
  - Nút Primary CTA "Gửi" (Sẽ chuyển thành icon Micro 🎙️ để ghi âm hoặc icon Camera 📷 tròn để quay video message ngắn khi ô nhập liệu trống).

#### C. Component & Data
| Component | Loại | Mô tả dữ liệu / Logic | Ràng buộc (Validation) |
|-----------|------|------------------------|-----------------------|
| Avatar | Image/Badge | Hình ảnh đại diện cá nhân hoặc nhóm (ghép từ nhiều user avatar nếu không có ảnh gốc). | Tỷ lệ 1:1, bo tròn. Kích thước 40px ở Header, 32px ở cạnh tin nhắn. |
| Group/User Name | Typography | Tên định danh của người chat hoặc nhóm. | Cắt chữ (truncate) `...` nếu vượt quá không gian hiển thị của màn hình. |
| Tin nhắn văn bản | Rich Text Bubble | Hiển thị chữ, @mention, inline-link preview. Hỗ trợ định dạng văn bản (Bold, Italic, Strikethrough, Monospace, **Spoiler** làm mờ chữ). Cho phép thả **Reaction** bằng emoji trực tiếp. | Tối đa 4000 ký tự mỗi tin. |
| Context Menu (Menu Tương tác) | Dropdown/Modal | Chuột phải/Long press vào tin nhắn để mở menu (Reply, Forward, Pin, Copy, Edit, Delete, Translate, Message Info). | Edit/Delete chỉ dành cho tin nhắn của chính mình. Tính năng "Xem ai đã đọc" chỉ khả dụng cho tin nhắn của mình gửi đi trong nhóm nhỏ. |
| Đính kèm file & Media | File Upload | Gửi hình ảnh (cho phép gom nhóm thành Album), video, tệp tài liệu nguyên bản không giảm dung lượng. | Giới hạn tối đa 50MB/file (hoặc tuỳ server). |
| Bình chọn (Poll) | Interactive Element | Form khảo sát trực tiếp trong chat. Hỗ trợ các chế độ: Ẩn danh (Anonymous), Đa lựa chọn (Multiple answers), Chế độ đố vui (Quiz mode). | Tối đa 10 lựa chọn mỗi Poll. |
| Vị trí (Location) | Maps UI | Gửi bản đồ tĩnh có ghim vị trí hoặc Chia sẻ vị trí trực tiếp (Live Location) chạy theo thời gian thực (15 phút, 1 giờ, 8 giờ). | Yêu cầu cấp quyền truy cập GPS của thiết bị. |
| Liên hệ (Contact) | VCard | Gửi thẻ định danh của nhân viên khác để truy cập nhanh vào hồ sơ (Profile) của họ. | Chỉ gửi được user nội bộ. |
| Tin nhắn thoại / Video | Audio/Video UI | Nhấn giữ icon micro để ghi âm (Voice message hiển thị waveform), hoặc nhấn giữ icon camera để quay video tin nhắn hình tròn (đặc trưng của Telegram). | Tối đa 5 phút/tin. Hỗ trợ nghe nhanh x1.5, x2. |
| Stickers / Emojis / GIFs | Rich Media | Tích hợp hệ thống Emojis chuẩn, bảng Sticker tĩnh/động nội bộ, và công cụ tìm kiếm GIF trực tuyến. | Emoji lớn khi gửi độc lập. GIF tự động phát (có thể tắt). |
| Menu Tin nhắn AI | AI Actions | Nút hỗ trợ AI (Tóm tắt, Dịch, Sửa lỗi viết) xuất hiện khi nhấn giữ tin nhắn của đối phương hoặc trong ô nhập của bản thân. | Chỉ hiển thị với các tin nhắn văn bản. |

#### D. Trạng thái giao diện (UI States)
- ⚪ **Empty State:** Giao diện chat trống. Hiển thị text: *"Hãy gửi lời chào an toàn đến đồng nghiệp."* kèm các gợi ý câu chào lịch sự.
- 🔄 **Loading State:** Spinner quay nhẹ ở header khi đang tải lịch sử tin nhắn cũ.
- 🔴 **Error State:** Gửi tin nhắn lỗi (mất mạng): Bong bóng tin nhắn mờ đi, cạnh tin nhắn xuất hiện icon chấm than đỏ. Click vào chấm than để chọn *"Gửi lại"* hoặc *"Xóa tin"*.
- 🟢 **Success State:** Tin nhắn gửi thành công xuất hiện dấu tick đơn (đã gửi lên server) -> tick kép (đã nhận) -> tick kép màu xanh (đã đọc).

#### E. Copywriting (Microcopy)
- Input Placeholder: `Tin nhắn...` hoặc `Nhập tin nhắn...`
- System notification: `[Tên] đã tham gia nhóm bằng mã QR.`
- Context Menu: `Trả lời` | `Chuyển tiếp` | `Ghim` | `Sao chép` | `Chỉnh sửa` | `Thu hồi` | `Xóa cho tôi` | `Dịch` | `Chi tiết đã xem`
- Typing Indicator: `[Tên] đang nhập tin nhắn...` / `[Tên] đang gửi một hình ảnh...`

---

### Screen 4: Màn hình Quản lý Nhóm & Phân quyền (Group Settings & Admin)
**Mục đích:** Admin nhóm thiết lập quyền hạn, quản lý thành viên và xem lại kho lưu trữ dữ liệu nhóm.

#### A. Phác họa Giao diện (Wireframe - Visual Sequence)
<details>
<summary>📱 Tùy chỉnh nhóm <code>group-settings</code></summary>

**Mô tả:** Màn hình cung cấp các công cụ quản lý nhóm mạnh mẽ tương tự Telegram, với khả năng phân quyền chi tiết.

```text
┌─────────────────────────────────────────┐
│ [ < ]  Tùy chỉnh nhóm               [⚙️]│
├─────────────────────────────────────────┤
│        [ 👥 Avatar Nhóm ]               │
│        Dự án Chiến dịch X (45 TV)       │
│        [ 🔗 Sao chép link mời ]         │
├─────────────────────────────────────────┤
│ 🔔 Thông báo & Âm thanh         [ Bật ] │
│ 📁 Kho lưu trữ: 12 Ảnh, 5 Files, 3 Links│
│ 📌 Tin nhắn đã ghim (3)                 │
├─────────────────────────────────────────┤
│ 👥 Thành viên nhóm:                     │
│ - 👑 Nguyễn Văn A (Trưởng nhóm)         │
│ - 👤 Lê Thị B (Thành viên)       [Xóa]  │
│ [➕ Thêm thành viên mới]                │
├─────────────────────────────────────────┤
│ 🛡️ Phân quyền nhóm (Dành cho Admin)     │
│ - Phê duyệt người vào nhóm:      [ Bật ]│
│ - Quyền gửi tin:      [Tất cả mọi người]│
│ - Quyền ghim tin nhắn:           [Admin]│
│ - Chế độ chậm (Slow mode):       [ Tắt ]│
├─────────────────────────────────────────┤
│ 🚫 Danh sách chặn (Banned Users)        │
└─────────────────────────────────────────┘
```
</details>

#### B. Cấu trúc Layout & Components (Anatomy)
- **Header:** Nút Back, Tên nhóm, Menu Hành động (Rời nhóm, Chuyển quyền).
- **Body/Main Content:**
  - **Khối Thông tin chung:** Avatar lớn, Tên nhóm, Mô tả nhóm. Nút chia sẻ link mời nhanh (Invite Link / QR Code).
  - **Khối Cài đặt chung:** 
    - Thông báo (Tắt trong 1h, 8h, Mãi mãi). 
    - Tùy chỉnh âm thanh riêng biệt cho nhóm.
  - **Khối Kho lưu trữ (Shared Media):** Phân chia thành các tab riêng: Media (Ảnh/Video), Files (Tài liệu), Links (Liên kết web), Voice (Ghi âm).
  - **Khối Thành viên:** Danh sách user, role (Owner, Admin, Member). Nút thêm thành viên mới, nút kick/ban.
  - **Khối Quản trị (Chỉ Admin thấy):** 
    - Yêu cầu phê duyệt người tham gia (Approve new members).
    - Cài đặt quyền nhóm (Gửi tin, Gửi Media, Thêm người, Ghim tin).
    - Chế độ chậm (Slow Mode - chống spam).
    - Danh sách chặn (Banned Users).

#### C. Component & Data
| Component | Loại | Mô tả dữ liệu / Logic | Ràng buộc (Validation) |
|-----------|------|------------------------|-----------------------|
| Kho lưu trữ | Tabbed View | Hiển thị tất cả file từng gửi trong nhóm. | Chỉ hiển thị file còn tồn tại. |
| Link mời (Invite Link) | Clipboard / QR | Cho phép admin tạo link tham gia. Có thể thu hồi (Revoke) link cũ. | Link mã hóa an toàn. |
| Danh sách thành viên | List Item | Hiển thị kèm badge chức danh (👑 Admin, 🛡️ Trợ lý). | Sắp xếp: Mình > Admin > Online > Offline. |
| Menu phân quyền | Toggle / Select | Bật/tắt các đặc quyền trong nhóm. | Chỉ Owner/Admin mới được thao tác. |

---

### Screen 5: Màn hình Danh bạ & Tổ chức (Contact & Org Chart)
**Mục đích:** Tra cứu nhanh thông tin liên lạc của đồng nghiệp theo sơ đồ phòng ban hoặc chuyên môn.

#### A. Phác họa Giao diện (Wireframe - Visual Sequence)
<details>
<summary>📱 Danh bạ <code>contact-directory</code></summary>

```text
┌─────────────────────────────────────────┐
│ Danh bạ tổ chức                     [🔍]│
├─────────────────────────────────────────┤
│ [ Tên, Số điện thoại, Kỹ năng...      ] │
├─────────────────────────────────────────┤
│ ⭐ Yêu thích (Favorites)                │
│ - (O) Lê Thị B (QA)                     │
├─────────────────────────────────────────┤
│  [ Công ty ]  [ Nhóm làm việc ]         │
├─────────────────────────────────────────┤
│ 🏢 Khối Công nghệ (120)                 │
│   ├─ 💻 Phòng Phát triển (50)           │
│   │   ├─ (O) Nguyễn Văn A (Dev)         │
│   │   └─ (O) Lê Thị B (QA)              │
│   └─ 🔌 Phòng Hạ tầng (70)              │
├─────────────────────────────────────────┤
│ 🏢 Khối Kinh doanh (200)                │
│   └─ ...                                │
├─────────────────────────────────────────┤
│   💬         👥         👤              │
└─────────────────────────────────────────┘
```
</details>

#### B. Cấu trúc Layout & Components (Anatomy)
- **Header:** Tiêu đề và nút Tìm kiếm nâng cao.
- **Body/Main Content:**
  - **Thanh tìm kiếm thông minh:** Hỗ trợ tìm kiếm theo Tên, Email, Số điện thoại, hoặc chuyên môn/kỹ năng (VD: "Designer", "Python").
  - **Liên hệ yêu thích (Favorites):** Danh sách ghim các đồng nghiệp hay trao đổi.
  - **Cấu trúc cây tổ chức (Tree view):** Phân cấp theo Khối -> Phòng ban -> Nhân viên.
  - **Mini-profile (Popup):** Khi chạm vào một liên hệ, hiển thị popup nhanh chứa: Avatar lớn, Email, SĐT, Trạng thái hoạt động, và các nút CTA (Chat nhanh, Gọi điện thoại, Gọi Video).
- **Footer:** Bottom Navigation Bar (`Tin nhắn | Danh bạ | Cá nhân`).

#### C. Component & Data
| Component | Loại | Mô tả dữ liệu / Logic | Ràng buộc (Validation) |
|-----------|------|------------------------|-----------------------|
| Tree View | Accordion List | Hiển thị cấu trúc công ty. | Expand/Collapse mượt mà. Tự động tải lười (Lazy load) khi nhánh quá đông. |
| Cột trạng thái | Icon/Badge | Hiển thị (O) Online, (Z) Away, (X) Offline. | Real-time update qua WebSocket. |
| Mini-Profile | Bottom Sheet / Modal | Tóm tắt hồ sơ nhân viên để tương tác nhanh. | Nếu người dùng cài đặt riêng tư ẩn SĐT thì hiển thị `***`. |

---

### Screen 6: Màn hình Tìm kiếm toàn cục (Global Search)
**Mục đích:** Tìm kiếm mạnh mẽ và có bộ lọc chi tiết cho toàn bộ dữ liệu trong hệ thống.

#### A. Phác họa Giao diện (Wireframe - Visual Sequence)
<details>
<summary>📱 Tìm kiếm toàn cục <code>global-search</code></summary>

```text
┌─────────────────────────────────────────┐
│ [ < ] [ báo cáo tháng 10...         ] [X]│
├─────────────────────────────────────────┤
│ Bộ lọc: [📅 Từ ngày] [👤 Người gửi]     │
│         [📎 Chứa tệp] [🔗 Chứa link]    │
├─────────────────────────────────────────┤
│ [ Tất cả ] [ Tin nhắn ] [ Người ] [ Tệp ]│
├─────────────────────────────────────────┤
│ Kết quả tìm kiếm (15):                  │
│ 👤 Nhóm Kế toán                         │
│    Lê Thị B: Nhớ nộp báo cáo tháng 10...│
│    20/10/2026                           │
│                                         │
│ 📁 Báo_cáo_tháng_10_Final.pdf           │
│    Từ: Nguyễn Văn A (Phòng ban X)       │
└─────────────────────────────────────────┘
```
</details>

#### B. Cấu trúc Layout & Components (Anatomy)
- **Header:** Thanh Input Search tự động focus.
- **Body/Main Content:**
  - **Trạng thái Trống (Empty State):** Khi chưa gõ từ khóa, hiển thị "Lịch sử tìm kiếm" và "Từ khóa gợi ý".
  - **Bộ lọc nâng cao (Advanced Filters):** Dạng thẻ cuộn ngang (Chips) để lọc theo: `Từ người gửi`, `Ngày tháng`, `Chứa tệp đính kèm`, `Trong nhóm cụ thể`.
  - **Tabs phân loại:** Chia kết quả thành `Tất cả` (All), `Tin nhắn` (Messages), `Người/Nhóm` (Chats), `Media/Files` (Tệp).
  - **Danh sách kết quả:** Highlight (bôi vàng) từ khóa trùng khớp trong văn bản.

#### C. Component & Data
| Component | Loại | Mô tả dữ liệu / Logic | Ràng buộc (Validation) |
|-----------|------|------------------------|-----------------------|
| Thanh Search | Input Field | Hỗ trợ cú pháp đặc biệt (VD: `from:Admin`). | Debounce 300ms trước khi gọi API. |
| Tabs Kết quả | Tab Navigation | Lọc kết quả theo định dạng dữ liệu. | Hiển thị số lượng tìm thấy (VD: Tệp (3)). |
| Highlight Text | Styled Text | Bôi đậm/vàng từ khóa trong chuỗi kết quả trả về. | Hỗ trợ không phân biệt dấu tiếng Việt. |

---

### Screen 7: Màn hình Thông tin cá nhân & Cài đặt (User Profile & Settings)
**Mục đích:** Quản lý tài khoản, trạng thái hiện diện và cài đặt ứng dụng.

#### A. Phác họa Giao diện (Wireframe - Visual Sequence)
<details>
<summary>📱 Cài đặt cá nhân <code>user-profile</code></summary>

```text
┌─────────────────────────────────────────┐
│ Thông tin cá nhân                       │
├─────────────────────────────────────────┤
│        [ 👤 Avatar ]                    │
│        Nguyễn Văn A                     │
│        Trưởng nhóm Phát triển           │
│  [ 🌴 Đang nghỉ phép mát... ✏️ ]        │
├─────────────────────────────────────────┤
│ ⚙️ Cài đặt chung                        │
│ 🔔 Thông báo & Âm thanh                 │
│ 🔒 Quyền riêng tư & Bảo mật             │
│ 🎨 Giao diện (Sáng/Tối)                 │
│ 💾 Dữ liệu & Lưu trữ (Xóa cache)        │
│ 💻 Thiết bị đang đăng nhập (3)          │
├─────────────────────────────────────────┤
│ [ Đăng xuất ]                           │
├─────────────────────────────────────────┤
│   💬         👥         👤              │
└─────────────────────────────────────────┘
```
</details>

#### B. Cấu trúc Layout & Components (Anatomy)
- **Header:** Tiêu đề "Cá nhân".
- **Khối Thông tin (Profile):** Avatar, Tên, Chức vụ. Nút chỉnh sửa trạng thái tùy chỉnh (Custom Status) kèm Emoji, có thể hẹn giờ tự động xóa trạng thái (Sau 1 ngày, 1 tuần).
- **Khối Menu Cài đặt (Settings):**
  - **Thông báo:** Bật/tắt thông báo rung, chuông báo, nhắc tên @.
  - **Quyền riêng tư (Privacy):** Tùy chọn ẩn "Lần cuối hoạt động", ẩn Số điện thoại.
  - **Giao diện (Theme):** Light Mode / Dark Mode / Theo hệ thống.
  - **Lưu trữ:** Công cụ quản lý dung lượng, tự động xóa bộ nhớ đệm (Clear Cache).
  - **Thiết bị (Sessions):** Xem danh sách máy tính, điện thoại đang đăng nhập. Nút "Đăng xuất tất cả thiết bị khác" (Force Logout).
- **Footer:** Bottom Navigation Bar (`Tin nhắn | Danh bạ | Cá nhân`).

#### C. Component & Data
| Component | Loại | Mô tả dữ liệu / Logic | Ràng buộc (Validation) |
|-----------|------|------------------------|-----------------------|
| Custom Status | Input/Emoji | Trạng thái hiển thị cạnh tên ở mọi nơi trong app. | Tối đa 50 ký tự. Hỗ trợ hẹn giờ tự tắt. |
| Devices/Sessions| List | Danh sách Token/Thiết bị hiện thời. | Yêu cầu nhập lại mật khẩu nếu muốn đăng xuất máy khác. |
| Clear Cache | Action Button | Xóa tệp tải về cục bộ để giải phóng bộ nhớ thiết bị. | Cảnh báo trước khi xóa. |

---

### Screen 8: Bảng điều khiển Quản trị (Admin Panel)
**Mục đích:** Dành riêng cho cấp Quản lý hệ thống (Super Admin) để thanh tra, cấu hình bảo mật toàn cục.

#### A. Phác họa Giao diện (Wireframe - Visual Sequence)
<details>
<summary>💻 Admin Dashboard <code>admin-panel</code></summary>

```text
┌────────────────────────────────────────────────────────┐
│ 🛡️ Bảng điều khiển Quản trị Hệ thống                  │
├─────────┬──────────────────────────────────────────────┤
│ 👤 Users│ 📊 Thống kê Hệ thống                         │
│ 🏢 Nhóm │ - Tổng User: 1,200 | Online: 450             │
│ ⚙️ Conf │ - Dung lượng lưu trữ: 1.5 TB                 │
│ 📝 Audit├──────────────────────────────────────────────┤
│ 📢 B.ast│ 📝 Nhật ký truy cập (Audit Logs)             │
│         │ [ Tìm kiếm ID/Tên... ] [ Tải file CSV ⬇️ ]    │
│         │ 10:25 - [NV01] đăng nhập từ IP 192.168.x     │
│         │ 10:15 - [NV05] xóa nhóm "Báo cáo Mật"        │
│         │ 09:30 - [Ad01] thay đổi quyền NV02           │
│         ├──────────────────────────────────────────────┤
│         │ 📢 Thông báo toàn hệ thống (Broadcast)       │
│         │ [ Nhập thông báo khẩn cấp...           ] [Gửi]│
└─────────┴──────────────────────────────────────────────┘
```
</details>

#### B. Cấu trúc Layout & Components (Anatomy)
- **Menu Sidebar (Trái):** Quản lý Users, Nhóm, Cấu hình, Nhật ký (Audit Logs), Gửi thông báo (Broadcast).
- **Body (Phải):**
  - **Dashboard:** Thống kê lượng user online, dung lượng lưu trữ server.
  - **Audit Logs:** Ghi nhận mọi sự kiện quan trọng (Đăng nhập, Xóa dữ liệu, Thay đổi quyền). Có bộ lọc thời gian và nút Xuất dữ liệu (Export to CSV).
  - **Role-based Access (RBAC):** Giao diện gán quyền hệ thống cho từng cá nhân.
  - **Broadcast System:** Nút cho phép Admin gửi thông báo khẩn cấp đẩy push notification tới mọi thiết bị của toàn bộ nhân viên công ty.
  - **Device Management:** Theo dõi và Forced Logout (Buộc đăng xuất) tài khoản của nhân viên từ xa khi có rủi ro bảo mật.

#### C. Component & Data
| Component | Loại | Mô tả dữ liệu / Logic | Ràng buộc (Validation) |
|-----------|------|------------------------|-----------------------|
| Audit Log Table | Data Table | Lưu trữ hành vi người dùng, thao tác nhạy cảm. | Chỉ xem (Read-only), không cho phép xóa log. |
| Export Button | Action | Trích xuất log ra tệp `.csv` hoặc `.xlsx`. | Yêu cầu xác thực OTP của Admin. |
| Broadcast Input | Form | Gửi thông báo khẩn cấp (vd: "Server bảo trì lúc 12h"). | Chỉ Super Admin mới có quyền này. |


## 3. Tổng Hợp Các Màn Hình (Screens Overview)

Bảng tổng hợp nhanh các màn hình đã thiết kế chi tiết ở trên để theo dõi trạng thái và sự liên kết:

| # | Slug | Status | Used by (UC / FR) | Figma | HTML prototype | Updated |
|---|------|--------|-------------------|-------|----------------|---------|
| 1 | [auth-login](#screen-1-man-hinh-dang-nhap-xac-thuc-otp-authentication-screen) | draft | uc-login-email, uc-otp-verify / FR-1.1, FR-1.2, FR-16.1..16.3 | — | — | 2026-05-28 |
| 2 | [chat-list](#screen-2-man-hinh-danh-sach-hoi-thoai-chat-list-screen---home) | draft | uc-chat-navigation / FR-8.1, FR-8.2, FR-8.5, FR-16.12 | — | — | 2026-05-28 |
| 3 | [chat-detail](#screen-3-man-hinh-chi-tiet-chat-1-1-nhom-ai-chat-screen) | draft | uc-chat-messaging, uc-ai-helper / FR-2, FR-5, FR-11.1..11.4 | — | — | 2026-05-28 |
| 4 | [group-settings](#screen-4-man-hinh-quan-ly-nhom--phan-quyen-group-settings--admin) | draft | uc-group-admin | — | — | 2026-05-28 |
| 5 | [contact-directory](#screen-5-man-hinh-danh-ba--to-chuc-contact--org-chart) | draft | uc-contact-directory | — | — | 2026-05-28 |
| 6 | [search-results](#screen-6-man-hinh-tim-kiem-toan-cuc-global-search) | draft | uc-global-search | — | — | 2026-05-28 |
| 7 | [workflow-approval](#screen-7-man-hinh-phe-duyet-quy-trinh-workflow-approval-screen) | draft | uc-workflow-approval / FR-14.1, FR-14.2, FR-14.4 | — | — | 2026-05-28 |
| 8 | [ai-hub](#screen-8-man-hinh-tro-ly-ai--prompt-hub-ai-hub) | draft | uc-ai-hub | — | — | 2026-05-28 |
| 9 | [user-profile](#screen-9-man-hinh-thong-tin-ca-nhan--cai-dat-user-profile--settings) | draft | uc-profile-settings | — | — | 2026-05-28 |
| 10 | [bod-dashboard](#screen-10-man-hinh-dashboard-ban-lanh-dao-bod-dashboard-screen) | draft | uc-bod-decide / FR-15.1..15.4, FR-15.14 | — | — | 2026-05-28 |
| 11 | [admin-panel](#screen-11-bang-dieu-khien-quan-tri-audit-log-admin-panel) | draft | uc-admin-compliance / FR-9.1..9.3, FR-9.6 | — | — | 2026-05-28 |
| 2 | [chat-list](#screen-2-man-hinh-danh-sach-hoi-thoai-chat-list-screen---home) | draft | uc-chat-navigation / FR-8.1, FR-8.2, FR-8.5, FR-16.12 | — | — | 2026-05-28 |
| 3 | [chat-detail](#screen-3-man-hinh-chi-tiet-chat-1-1-nhom-ai-chat-screen) | draft | uc-chat-messaging, uc-ai-helper / FR-2, FR-5, FR-11.1..11.4 | — | — | 2026-05-28 |
| 4 | [workflow-approval](#screen-4-man-hinh-phe-duyet-quy-trinh-workflow-approval-screen) | draft | uc-workflow-approval / FR-14.1, FR-14.2, FR-14.4 | — | — | 2026-05-28 |
| 5 | [bod-dashboard](#screen-5-man-hinh-dashboard-ban-lanh-dao-bod-dashboard-screen) | draft | uc-bod-decide / FR-15.1..15.4, FR-15.14 | — | — | 2026-05-28 |
| 6 | [admin-panel](#screen-6-bang-dieu-khien-quan-tri-audit-log-admin-panel) | draft | uc-admin-compliance / FR-9.1..9.3, FR-9.6 | — | — | 2026-05-28 |
| 7 | [user-profile](#screen-7-man-hinh-thong-tin-ca-nhan--cai-dat-user-profile--settings) | draft | uc-profile-settings | — | — | 2026-05-28 |
| 8 | [group-settings](#screen-8-man-hinh-quan-ly-nhom--phan-quyen-group-settings--admin) | draft | uc-group-admin | — | — | 2026-05-28 |
| 9 | [contact-directory](#screen-9-man-hinh-danh-ba--to-chuc-contact--org-chart) | draft | uc-contact-directory | — | — | 2026-05-28 |
| 10 | [search-results](#screen-10-man-hinh-tim-kiem-toan-cuc-global-search) | draft | uc-global-search | — | — | 2026-05-28 |
| 11 | [ai-hub](#screen-11-man-hinh-tro-ly-ai--prompt-hub-ai-hub) | draft | uc-ai-hub | — | — | 2026-05-28 |

---

## 4. Edge Cases (Trường Hợp Ngoại Lệ & Hướng Xử Lý UX)

- **Trường hợp 1: Thiết bị mất kết nối mạng đột ngột khi đang phê duyệt quy trình.**
  - **Hướng xử lý UX:** Vô hiệu hóa tạm thời nút "Phê duyệt"/"Từ chối". Hiển thị một toast thông báo màu đỏ ở cạnh dưới màn hình: *"Mất kết nối mạng. Yêu cầu phê duyệt của bạn đã được đưa vào hàng đợi offline và sẽ tự động gửi lại khi có mạng."*
- **Trường hợp 2: Nhân viên chỉnh sửa tin nhắn có chứa nội dung phê duyệt quy trình.**
  - **Hướng xử lý UX:** Hệ thống không cho phép sửa nội dung các tin nhắn mang tính chất giao dịch quy trình (nút phê duyệt). Nếu là tin nhắn chat thường, hiển thị nhãn rõ ràng *"Đã chỉnh sửa"* kèm lịch sử sửa khi click vào nhãn (phục vụ audit).
- **Trường hợp 3: Tin nhắn chat chứa thông tin nhạy cảm hoặc vi phạm chính sách bảo mật công ty (ví dụ: chia sẻ mật khẩu, mã nguồn).**
  - **Hướng xử lý UX:** Hệ thống AI quét ngầm realtime (theo chính sách DLP của doanh nghiệp). Nếu phát hiện vi phạm, tin nhắn sẽ bị ẩn đi kèm thông báo cho người gửi: *"Tin nhắn bị ẩn do vi phạm chính sách bảo mật thông tin doanh nghiệp. Hành động đã được ghi vào nhật ký kiểm toán."*

---

## 5. Ghi Chú Thêm Cho Designer

- **Phong cách thiết kế:** Sử dụng phong cách Glassmorphism nhẹ nhàng (mờ nền) cho các popup điều hướng và bảng phê duyệt trên thiết bị di động để tạo sự hiện đại, sang trọng.
- **Khả năng tiếp cận (Accessibility):**
  - Đảm bảo tỷ lệ tương phản chữ đạt tiêu chuẩn WCAG 2.1 AA (tỷ lệ tương phản tối thiểu 4.5:1 đối với văn bản thông thường).
  - Tích hợp điều khiển giọng nói (TTS/STT) trực quan để hỗ trợ nhân viên thao tác rảnh tay.
- **Hiệu ứng chuyển cảnh (Transitions):** Các hiệu ứng mở cửa sổ chat, lướt phê duyệt cần có độ trễ cực thấp (<150ms) tạo cảm giác ứng dụng mượt mà, phản hồi tức thì.

## 3. Tổng Hợp Các Màn Hình (Screens Overview)

Bảng tổng hợp nhanh các màn hình đã thiết kế chi tiết ở trên để theo dõi trạng thái và sự liên kết:

| # | Slug | Status | Used by (UC / FR) | Figma | HTML prototype | Updated |
|---|------|--------|-------------------|-------|----------------|---------|
| 1 | [auth-login](#screen-1-man-hinh-dang-nhap-xac-thuc-otp-authentication-screen) | draft | uc-login-email | — | — | 2026-05-28 |
| 2 | [chat-list](#screen-2-man-hinh-danh-sach-hoi-thoai-chat-list-screen---home) | draft | uc-chat-navigation | — | — | 2026-05-28 |
| 3 | [chat-detail](#screen-3-man-hinh-chi-tiet-chat-1-1-nhom-ai-chat-screen) | draft | uc-chat-messaging | — | — | 2026-05-28 |
| 4 | [group-settings](#screen-4-man-hinh-quan-ly-nhom--phan-quyen-group-settings--admin) | draft | uc-group-admin | — | — | 2026-05-28 |
| 5 | [contact-directory](#screen-5-man-hinh-danh-ba--to-chuc-contact--org-chart) | draft | uc-contact-directory | — | — | 2026-05-28 |
| 6 | [search-results](#screen-6-man-hinh-tim-kiem-toan-cuc-global-search) | draft | uc-global-search | — | — | 2026-05-28 |
| 7 | [user-profile](#screen-7-man-hinh-thong-tin-ca-nhan--cai-dat-user-profile--settings) | draft | uc-profile-settings | — | — | 2026-05-28 |
| 8 | [bod-dashboard](#screen-8-man-hinh-dashboard-ban-lanh-dao-bod-dashboard-screen) | draft | uc-bod-decide | — | — | 2026-05-28 |
| 9 | [admin-panel](#screen-9-bang-dieu-khien-quan-tri-audit-log-admin-panel) | draft | uc-admin-compliance | — | — | 2026-05-28 |
| 2 | [chat-list](#screen-2-man-hinh-danh-sach-hoi-thoai-chat-list-screen---home) | draft | uc-chat-navigation / FR-8.1, FR-8.2, FR-8.5, FR-16.12 | — | — | 2026-05-28 |
| 3 | [chat-detail](#screen-3-man-hinh-chi-tiet-chat-1-1-nhom-ai-chat-screen) | draft | uc-chat-messaging, uc-ai-helper / FR-2, FR-5, FR-11.1..11.4 | — | — | 2026-05-28 |
| 4 | [workflow-approval](#screen-4-man-hinh-phe-duyet-quy-trinh-workflow-approval-screen) | draft | uc-workflow-approval / FR-14.1, FR-14.2, FR-14.4 | — | — | 2026-05-28 |
| 5 | [bod-dashboard](#screen-5-man-hinh-dashboard-ban-lanh-dao-bod-dashboard-screen) | draft | uc-bod-decide / FR-15.1..15.4, FR-15.14 | — | — | 2026-05-28 |
| 6 | [admin-panel](#screen-6-bang-dieu-khien-quan-tri-audit-log-admin-panel) | draft | uc-admin-compliance / FR-9.1..9.3, FR-9.6 | — | — | 2026-05-28 |
| 7 | [user-profile](#screen-7-man-hinh-thong-tin-ca-nhan--cai-dat-user-profile--settings) | draft | uc-profile-settings | — | — | 2026-05-28 |
| 8 | [group-settings](#screen-8-man-hinh-quan-ly-nhom--phan-quyen-group-settings--admin) | draft | uc-group-admin | — | — | 2026-05-28 |
| 9 | [contact-directory](#screen-9-man-hinh-danh-ba--to-chuc-contact--org-chart) | draft | uc-contact-directory | — | — | 2026-05-28 |
| 10 | [search-results](#screen-10-man-hinh-tim-kiem-toan-cuc-global-search) | draft | uc-global-search | — | — | 2026-05-28 |
| 11 | [ai-hub](#screen-11-man-hinh-tro-ly-ai--prompt-hub-ai-hub) | draft | uc-ai-hub | — | — | 2026-05-28 |

---

## 4. Edge Cases (Trường Hợp Ngoại Lệ & Hướng Xử Lý UX)

- **Trường hợp 1: Tin nhắn chat chứa thông tin nhạy cảm hoặc vi phạm chính sách bảo mật công ty (ví dụ: chia sẻ mật khẩu, mã nguồn).**
  - **Hướng xử lý UX:** Hệ thống AI quét ngầm realtime (theo chính sách DLP của doanh nghiệp). Nếu phát hiện vi phạm, tin nhắn sẽ bị ẩn đi kèm thông báo cho người gửi: *"Tin nhắn bị ẩn do vi phạm chính sách bảo mật thông tin doanh nghiệp. Hành động đã được ghi vào nhật ký kiểm toán."*

---

## 5. Ghi Chú Thêm Cho Designer

- **Phong cách thiết kế:** Sử dụng phong cách Glassmorphism nhẹ nhàng (mờ nền) cho các popup điều hướng và bảng phê duyệt trên thiết bị di động để tạo sự hiện đại, sang trọng.
- **Khả năng tiếp cận (Accessibility):**
  - Đảm bảo tỷ lệ tương phản chữ đạt tiêu chuẩn WCAG 2.1 AA (tỷ lệ tương phản tối thiểu 4.5:1 đối với văn bản thông thường).
  - Tích hợp điều khiển giọng nói (TTS/STT) trực quan để hỗ trợ nhân viên thao tác rảnh tay.
- **Hiệu ứng chuyển cảnh (Transitions):** Các hiệu ứng mở cửa sổ chat, lướt phê duyệt cần có độ trễ cực thấp (<150ms) tạo cảm giác ứng dụng mượt mà, phản hồi tức thì.
### Screen 9: Màn hình Thông tin cá nhân & Cài đặt (User Profile & Settings)
**Mục đích:** Quản lý thông tin định danh nội bộ, cài đặt trạng thái hoạt động và cá nhân hóa trải nghiệm.

#### A. Phác họa Giao diện (Wireframe - Visual Sequence)
<details>
<summary>📱 Thông tin cá nhân <code>user-profile</code></summary>

```text
┌─────────────────────────────────────────┐
│ [ < ]  Thông tin tài khoản              │
├─────────────────────────────────────────┤
│        [ 🧑 Avatar lớn ]                │
│        Trần Văn C                       │
│        Trưởng phòng Marketing           │
│        tranc@company.com | 0901234567   │
├─────────────────────────────────────────┤
│ Trạng thái:                             │
│ ( ) Online   (x) Bận   ( ) Đi vắng      │
├─────────────────────────────────────────┤
│ ⚙️ Cài đặt ứng dụng                      │
│ - 🔔 Thông báo (Bật)                     │
│ - 🌙 Giao diện tối (Tự động)             │
│ - 🔒 Quyền riêng tư & Bảo mật            │
├─────────────────────────────────────────┤
│ [     Đăng xuất khỏi thiết bị này     ] │
└─────────────────────────────────────────┘
```
</details>

#### B. Cấu trúc Layout & Components (Anatomy)
- **Header:** Nút Back + Tiêu đề.
- **Body/Main Content:**
  - Avatar, Họ tên, Chức vụ, Email công ty, Số điện thoại.
  - Quản lý trực tiếp (Manager) & Phòng ban trực thuộc.
  - Radio buttons/Dropdown chọn trạng thái hoạt động (Presence).
  - List menu cài đặt: Thông báo (push/email), Giao diện (Dark/Light mode), Bảo mật (Đổi mã PIN/FaceID).
- **Footer:** Nút Đăng xuất màu đỏ.

#### C. Component & Data
| Component | Loại | Mô tả dữ liệu / Logic | Ràng buộc (Validation) |
|-----------|------|------------------------|-----------------------|
| Trạng thái | Radio/Select | Đồng bộ trạng thái hiện diện (Presence) tới tất cả người dùng khác realtime. | Các trạng thái tự động reset về Online vào đầu giờ làm việc hôm sau (tùy chọn). |
| Nút Đăng xuất | Destructive CTA | Xóa toàn bộ token phiên đăng nhập, xóa cache nội dung chat offline để bảo mật. | Yêu cầu xác nhận 2 lần trước khi đăng xuất. |

---

### Screen 10: Bảng Điều Khiển Quản Trị & Audit Log (Admin Panel)
**Mục đích:** Đảm bảo tính kiểm soát, tuân thủ chính sách và bảo mật dữ liệu doanh nghiệp (Đặc thù nội bộ).

#### A. Phác họa Giao diện (Wireframe - Visual Sequence)
<details>
<summary>🖥️ Bảng quản trị <code>admin-panel</code></summary>

**Mô tả:** Bản thiết kế giao diện rộng (Desktop/Web) cho Admin theo dõi danh sách Audit Log, lọc hành động, địa chỉ IP và trạng thái phiên làm việc.

```text
┌─────────────────────────────────────────┐
│ Bảng điều khiển Admin - Audit Log [Web] │
├─────────────────────────────────────────┤
│ [👤 User] [👥 Groups] [📋 Logs] [⚙️ Config]│
├─────────────────────────────────────────┤
│ Bộ lọc: [Hành động v] [Nhân viên v]     │
├─────────────────────────────────────────┤
│ Thời gian | Nhân viên | Hành động | IP  │
├───────────┼───────────┼───────────┼─────┤
│ 10:14:15  │ Admin     │ Lock user │ 1.1 │
├───────────┼───────────┼───────────┼─────┤
│ 10:12:00  │ N.V. A    │ Login     │ 2.3 │
├───────────┼───────────┼───────────┼─────┤
│ 09:45:10  │ L.T. B    │ Edit msg  │ 4.5 │
└───────────┴───────────┴───────────┴─────┘
```
</details>

#### B. Cấu trúc Layout & Components (Anatomy)
- **Side Navigation (Bản Web/Desktop):** Người dùng | Nhóm & Kênh | Quyền hạn & Phân vai (RBAC) | Nhật ký hệ thống (Audit Log) | Cấu hình bảo mật.
- **Body/Main Content:**
  - Bảng hiển thị danh sách chi tiết (ví dụ: Danh sách Audit Log).
  - Các bộ lọc nâng cao (Tìm theo thời gian, theo hành động: Gửi/Xóa/Sửa/Đăng nhập, theo nhân viên).
  - Khối thông tin chi tiết của hành động được chọn (Thiết bị, IP, tọa độ, hành động cụ thể).
- **Header/Footer:** Tên phiên quản trị, nút Đăng xuất an toàn.

#### C. Component & Data
| Component | Loại | Mô tả dữ liệu / Logic | Ràng buộc (Validation) |
|-----------|------|------------------------|-----------------------|
| Bảng dữ liệu log | Data Table | Liệt kê các bản ghi lịch sử tương tác. Không cho phép sửa hay xóa bản ghi log dưới bất kỳ hình thức nào. | Sắp xếp giảm dần theo thời gian (mới nhất lên đầu). |
| Công cụ Lọc dữ liệu | Filter Group | Lọc log theo Loại hành động, Người thực hiện, Khoảng thời gian. | Thời gian lọc tối đa 90 ngày mỗi lượt để tối ưu hiệu năng. |
| Nút "Khóa tài khoản" | Secondary CTA | Cho phép Admin vô hiệu hóa tài khoản nhân viên ngay lập tức (khi nghỉ việc hoặc nghi ngờ lộ thông tin). | Yêu cầu Admin xác nhận mật khẩu trước khi khóa. |

#### D. Trạng thái giao diện (UI States)
- ⚪ **Empty State:** Kết quả lọc không có bản ghi nào phù hợp. Hiển thị text: *"Không tìm thấy nhật ký tương ứng với bộ lọc."*
- 🔄 **Loading State:** Hàng đợi tải dữ liệu lớn hiển thị thanh tiến trình (progress bar).
- 🔴 **Error State:** Lỗi truy vấn dữ liệu log: Hiển thị thông báo đỏ ở góc: *"Lỗi kết nối cơ sở dữ liệu nhật ký. Vui lòng kiểm tra lại cấu hình."*
- 🟢 **Success State:** Vô hiệu hóa tài khoản thành công, hệ thống lập tức ngắt toàn bộ phiên kết nối (WebSocket) đang hoạt động của tài khoản đó.

#### E. Copywriting (Microcopy)
- Table Columns: `Thời gian` | `Nhân viên` | `Hành động` | `Thiết bị` | `IP`
- Notification: `Tài khoản [Tên] đã bị khóa thành công và đăng xuất khỏi toàn bộ thiết bị.`

---


## 3. Tổng Hợp Các Màn Hình (Screens Overview)

Bảng tổng hợp nhanh các màn hình đã thiết kế chi tiết ở trên để theo dõi trạng thái và sự liên kết:

| # | Slug | Status | Used by (UC / FR) | Figma | HTML prototype | Updated |
|---|------|--------|-------------------|-------|----------------|---------|
| 1 | [auth-login](#screen-1-man-hinh-dang-nhap-xac-thuc-otp-authentication-screen) | draft | uc-login-email | — | — | 2026-05-28 |
| 2 | [chat-list](#screen-2-man-hinh-danh-sach-hoi-thoai-chat-list-screen---home) | draft | uc-chat-navigation | — | — | 2026-05-28 |
| 3 | [chat-detail](#screen-3-man-hinh-chi-tiet-chat-1-1-nhom-ai-chat-screen) | draft | uc-chat-messaging | — | — | 2026-05-28 |
| 4 | [group-settings](#screen-4-man-hinh-quan-ly-nhom--phan-quyen-group-settings--admin) | draft | uc-group-admin | — | — | 2026-05-28 |
| 5 | [contact-directory](#screen-5-man-hinh-danh-ba--to-chuc-contact--org-chart) | draft | uc-contact-directory | — | — | 2026-05-28 |
| 6 | [search-results](#screen-6-man-hinh-tim-kiem-toan-cuc-global-search) | draft | uc-global-search | — | — | 2026-05-28 |
| 7 | [user-profile](#screen-7-man-hinh-thong-tin-ca-nhan--cai-dat-user-profile--settings) | draft | uc-profile-settings | — | — | 2026-05-28 |
| 8 | [bod-dashboard](#screen-8-man-hinh-dashboard-ban-lanh-dao-bod-dashboard-screen) | draft | uc-bod-decide | — | — | 2026-05-28 |
| 9 | [admin-panel](#screen-9-bang-dieu-khien-quan-tri-audit-log-admin-panel) | draft | uc-admin-compliance | — | — | 2026-05-28 |
| 2 | [chat-list](#screen-2-man-hinh-danh-sach-hoi-thoai-chat-list-screen---home) | draft | uc-chat-navigation / FR-8.1, FR-8.2, FR-8.5, FR-16.12 | — | — | 2026-05-28 |
| 3 | [chat-detail](#screen-3-man-hinh-chi-tiet-chat-1-1-nhom-ai-chat-screen) | draft | uc-chat-messaging, uc-ai-helper / FR-2, FR-5, FR-11.1..11.4 | — | — | 2026-05-28 |
| 4 | [group-settings](#screen-4-man-hinh-quan-ly-nhom--phan-quyen-group-settings--admin) | draft | uc-group-admin | — | — | 2026-05-28 |
| 5 | [contact-directory](#screen-5-man-hinh-danh-ba--to-chuc-contact--org-chart) | draft | uc-contact-directory | — | — | 2026-05-28 |
| 6 | [search-results](#screen-6-man-hinh-tim-kiem-toan-cuc-global-search) | draft | uc-global-search | — | — | 2026-05-28 |
| 7 | [workflow-approval](#screen-7-man-hinh-phe-duyet-quy-trinh-workflow-approval-screen) | draft | uc-workflow-approval / FR-14.1, FR-14.2, FR-14.4 | — | — | 2026-05-28 |
| 8 | [ai-hub](#screen-8-man-hinh-tro-ly-ai--prompt-hub-ai-hub) | draft | uc-ai-hub | — | — | 2026-05-28 |
| 9 | [user-profile](#screen-9-man-hinh-thong-tin-ca-nhan--cai-dat-user-profile--settings) | draft | uc-profile-settings | — | — | 2026-05-28 |
| 10 | [bod-dashboard](#screen-10-man-hinh-dashboard-ban-lanh-dao-bod-dashboard-screen) | draft | uc-bod-decide / FR-15.1..15.4, FR-15.14 | — | — | 2026-05-28 |
| 11 | [admin-panel](#screen-11-bang-dieu-khien-quan-tri-audit-log-admin-panel) | draft | uc-admin-compliance / FR-9.1..9.3, FR-9.6 | — | — | 2026-05-28 |
| 2 | [chat-list](#screen-2-man-hinh-danh-sach-hoi-thoai-chat-list-screen---home) | draft | uc-chat-navigation / FR-8.1, FR-8.2, FR-8.5, FR-16.12 | — | — | 2026-05-28 |
| 3 | [chat-detail](#screen-3-man-hinh-chi-tiet-chat-1-1-nhom-ai-chat-screen) | draft | uc-chat-messaging, uc-ai-helper / FR-2, FR-5, FR-11.1..11.4 | — | — | 2026-05-28 |
| 4 | [workflow-approval](#screen-4-man-hinh-phe-duyet-quy-trinh-workflow-approval-screen) | draft | uc-workflow-approval / FR-14.1, FR-14.2, FR-14.4 | — | — | 2026-05-28 |
| 5 | [bod-dashboard](#screen-5-man-hinh-dashboard-ban-lanh-dao-bod-dashboard-screen) | draft | uc-bod-decide / FR-15.1..15.4, FR-15.14 | — | — | 2026-05-28 |
| 6 | [admin-panel](#screen-6-bang-dieu-khien-quan-tri-audit-log-admin-panel) | draft | uc-admin-compliance / FR-9.1..9.3, FR-9.6 | — | — | 2026-05-28 |
| 7 | [user-profile](#screen-7-man-hinh-thong-tin-ca-nhan--cai-dat-user-profile--settings) | draft | uc-profile-settings | — | — | 2026-05-28 |
| 8 | [group-settings](#screen-8-man-hinh-quan-ly-nhom--phan-quyen-group-settings--admin) | draft | uc-group-admin | — | — | 2026-05-28 |
| 9 | [contact-directory](#screen-9-man-hinh-danh-ba--to-chuc-contact--org-chart) | draft | uc-contact-directory | — | — | 2026-05-28 |
| 10 | [search-results](#screen-10-man-hinh-tim-kiem-toan-cuc-global-search) | draft | uc-global-search | — | — | 2026-05-28 |
| 11 | [ai-hub](#screen-11-man-hinh-tro-ly-ai--prompt-hub-ai-hub) | draft | uc-ai-hub | — | — | 2026-05-28 |

---

## 4. Edge Cases (Trường Hợp Ngoại Lệ & Hướng Xử Lý UX)

- **Trường hợp 1: Tin nhắn chat chứa thông tin nhạy cảm hoặc vi phạm chính sách bảo mật công ty (ví dụ: chia sẻ mật khẩu, mã nguồn).**
  - **Hướng xử lý UX:** Hệ thống AI quét ngầm realtime (theo chính sách DLP của doanh nghiệp). Nếu phát hiện vi phạm, tin nhắn sẽ bị ẩn đi kèm thông báo cho người gửi: *"Tin nhắn bị ẩn do vi phạm chính sách bảo mật thông tin doanh nghiệp. Hành động đã được ghi vào nhật ký kiểm toán."*

---

## 5. Ghi Chú Thêm Cho Designer

- **Phong cách thiết kế:** Sử dụng phong cách Glassmorphism nhẹ nhàng (mờ nền) cho các popup điều hướng và bảng phê duyệt trên thiết bị di động để tạo sự hiện đại, sang trọng.
- **Khả năng tiếp cận (Accessibility):**
  - Đảm bảo tỷ lệ tương phản chữ đạt tiêu chuẩn WCAG 2.1 AA (tỷ lệ tương phản tối thiểu 4.5:1 đối với văn bản thông thường).
  - Tích hợp điều khiển giọng nói (TTS/STT) trực quan để hỗ trợ nhân viên thao tác rảnh tay.
- **Hiệu ứng chuyển cảnh (Transitions):** Các hiệu ứng mở cửa sổ chat, lướt phê duyệt cần có độ trễ cực thấp (<150ms) tạo cảm giác ứng dụng mượt mà, phản hồi tức thì.

## 3. Tổng Hợp Các Màn Hình (Screens Overview)

Bảng tổng hợp nhanh các màn hình đã thiết kế chi tiết ở trên để theo dõi trạng thái và sự liên kết:

| # | Slug | Status | Used by (UC / FR) | Figma | HTML prototype | Updated |
|---|------|--------|-------------------|-------|----------------|---------|
| 1 | [auth-login](#screen-1-man-hinh-dang-nhap-xac-thuc-otp-authentication-screen) | draft | uc-login-email | — | — | 2026-05-28 |
| 2 | [chat-list](#screen-2-man-hinh-danh-sach-hoi-thoai-chat-list-screen---home) | draft | uc-chat-navigation | — | — | 2026-05-28 |
| 3 | [chat-detail](#screen-3-man-hinh-chi-tiet-chat-1-1-nhom-ai-chat-screen) | draft | uc-chat-messaging | — | — | 2026-05-28 |
| 4 | [group-settings](#screen-4-man-hinh-quan-ly-nhom--phan-quyen-group-settings--admin) | draft | uc-group-admin | — | — | 2026-05-28 |
| 5 | [contact-directory](#screen-5-man-hinh-danh-ba--to-chuc-contact--org-chart) | draft | uc-contact-directory | — | — | 2026-05-28 |
| 6 | [search-results](#screen-6-man-hinh-tim-kiem-toan-cuc-global-search) | draft | uc-global-search | — | — | 2026-05-28 |
| 7 | [user-profile](#screen-7-man-hinh-thong-tin-ca-nhan--cai-dat-user-profile--settings) | draft | uc-profile-settings | — | — | 2026-05-28 |
| 8 | [admin-panel](#screen-8-bang-dieu-khien-quan-tri-audit-log-admin-panel) | draft | uc-admin-compliance | — | — | 2026-05-28 |
| 2 | [chat-list](#screen-2-man-hinh-danh-sach-hoi-thoai-chat-list-screen---home) | draft | uc-chat-navigation / FR-8.1, FR-8.2, FR-8.5, FR-16.12 | — | — | 2026-05-28 |
| 3 | [chat-detail](#screen-3-man-hinh-chi-tiet-chat-1-1-nhom-ai-chat-screen) | draft | uc-chat-messaging, uc-ai-helper / FR-2, FR-5, FR-11.1..11.4 | — | — | 2026-05-28 |
| 4 | [group-settings](#screen-4-man-hinh-quan-ly-nhom--phan-quyen-group-settings--admin) | draft | uc-group-admin | — | — | 2026-05-28 |
| 5 | [contact-directory](#screen-5-man-hinh-danh-ba--to-chuc-contact--org-chart) | draft | uc-contact-directory | — | — | 2026-05-28 |
| 6 | [search-results](#screen-6-man-hinh-tim-kiem-toan-cuc-global-search) | draft | uc-global-search | — | — | 2026-05-28 |
| 7 | [workflow-approval](#screen-7-man-hinh-phe-duyet-quy-trinh-workflow-approval-screen) | draft | uc-workflow-approval / FR-14.1, FR-14.2, FR-14.4 | — | — | 2026-05-28 |
| 8 | [ai-hub](#screen-8-man-hinh-tro-ly-ai--prompt-hub-ai-hub) | draft | uc-ai-hub | — | — | 2026-05-28 |
| 9 | [user-profile](#screen-9-man-hinh-thong-tin-ca-nhan--cai-dat-user-profile--settings) | draft | uc-profile-settings | — | — | 2026-05-28 |
| 10 | [bod-dashboard](#screen-10-man-hinh-dashboard-ban-lanh-dao-bod-dashboard-screen) | draft | uc-bod-decide / FR-15.1..15.4, FR-15.14 | — | — | 2026-05-28 |
| 11 | [admin-panel](#screen-11-bang-dieu-khien-quan-tri-audit-log-admin-panel) | draft | uc-admin-compliance / FR-9.1..9.3, FR-9.6 | — | — | 2026-05-28 |
| 2 | [chat-list](#screen-2-man-hinh-danh-sach-hoi-thoai-chat-list-screen---home) | draft | uc-chat-navigation / FR-8.1, FR-8.2, FR-8.5, FR-16.12 | — | — | 2026-05-28 |
| 3 | [chat-detail](#screen-3-man-hinh-chi-tiet-chat-1-1-nhom-ai-chat-screen) | draft | uc-chat-messaging, uc-ai-helper / FR-2, FR-5, FR-11.1..11.4 | — | — | 2026-05-28 |
| 4 | [workflow-approval](#screen-4-man-hinh-phe-duyet-quy-trinh-workflow-approval-screen) | draft | uc-workflow-approval / FR-14.1, FR-14.2, FR-14.4 | — | — | 2026-05-28 |
| 5 | [bod-dashboard](#screen-5-man-hinh-dashboard-ban-lanh-dao-bod-dashboard-screen) | draft | uc-bod-decide / FR-15.1..15.4, FR-15.14 | — | — | 2026-05-28 |
| 6 | [admin-panel](#screen-6-bang-dieu-khien-quan-tri-audit-log-admin-panel) | draft | uc-admin-compliance / FR-9.1..9.3, FR-9.6 | — | — | 2026-05-28 |
| 7 | [user-profile](#screen-7-man-hinh-thong-tin-ca-nhan--cai-dat-user-profile--settings) | draft | uc-profile-settings | — | — | 2026-05-28 |
| 8 | [group-settings](#screen-8-man-hinh-quan-ly-nhom--phan-quyen-group-settings--admin) | draft | uc-group-admin | — | — | 2026-05-28 |
| 9 | [contact-directory](#screen-9-man-hinh-danh-ba--to-chuc-contact--org-chart) | draft | uc-contact-directory | — | — | 2026-05-28 |
| 10 | [search-results](#screen-10-man-hinh-tim-kiem-toan-cuc-global-search) | draft | uc-global-search | — | — | 2026-05-28 |
| 11 | [ai-hub](#screen-11-man-hinh-tro-ly-ai--prompt-hub-ai-hub) | draft | uc-ai-hub | — | — | 2026-05-28 |

---

## 4. Edge Cases (Trường Hợp Ngoại Lệ & Hướng Xử Lý UX)

- **Trường hợp 1: Thiết bị mất kết nối mạng đột ngột khi đang phê duyệt quy trình.**
  - **Hướng xử lý UX:** Vô hiệu hóa tạm thời nút "Phê duyệt"/"Từ chối". Hiển thị một toast thông báo màu đỏ ở cạnh dưới màn hình: *"Mất kết nối mạng. Yêu cầu phê duyệt của bạn đã được đưa vào hàng đợi offline và sẽ tự động gửi lại khi có mạng."*
- **Trường hợp 2: Nhân viên chỉnh sửa tin nhắn có chứa nội dung phê duyệt quy trình.**
  - **Hướng xử lý UX:** Hệ thống không cho phép sửa nội dung các tin nhắn mang tính chất giao dịch quy trình (nút phê duyệt). Nếu là tin nhắn chat thường, hiển thị nhãn rõ ràng *"Đã chỉnh sửa"* kèm lịch sử sửa khi click vào nhãn (phục vụ audit).
- **Trường hợp 3: Tin nhắn chat chứa thông tin nhạy cảm hoặc vi phạm chính sách bảo mật công ty (ví dụ: chia sẻ mật khẩu, mã nguồn).**
  - **Hướng xử lý UX:** Hệ thống AI quét ngầm realtime (theo chính sách DLP của doanh nghiệp). Nếu phát hiện vi phạm, tin nhắn sẽ bị ẩn đi kèm thông báo cho người gửi: *"Tin nhắn bị ẩn do vi phạm chính sách bảo mật thông tin doanh nghiệp. Hành động đã được ghi vào nhật ký kiểm toán."*

---

## 5. Ghi Chú Thêm Cho Designer

- **Phong cách thiết kế:** Sử dụng phong cách Glassmorphism nhẹ nhàng (mờ nền) cho các popup điều hướng và bảng phê duyệt trên thiết bị di động để tạo sự hiện đại, sang trọng.
- **Khả năng tiếp cận (Accessibility):**
  - Đảm bảo tỷ lệ tương phản chữ đạt tiêu chuẩn WCAG 2.1 AA (tỷ lệ tương phản tối thiểu 4.5:1 đối với văn bản thông thường).
  - Tích hợp điều khiển giọng nói (TTS/STT) trực quan để hỗ trợ nhân viên thao tác rảnh tay.
- **Hiệu ứng chuyển cảnh (Transitions):** Các hiệu ứng mở cửa sổ chat, lướt phê duyệt cần có độ trễ cực thấp (<150ms) tạo cảm giác ứng dụng mượt mà, phản hồi tức thì.

## 3. Tổng Hợp Các Màn Hình (Screens Overview)

Bảng tổng hợp nhanh các màn hình đã thiết kế chi tiết ở trên để theo dõi trạng thái và sự liên kết:

| # | Slug | Status | Used by (UC / FR) | Figma | HTML prototype | Updated |
|---|------|--------|-------------------|-------|----------------|---------|
| 1 | [auth-login](#screen-1-man-hinh-dang-nhap-xac-thuc-otp-authentication-screen) | draft | uc-login-email | — | — | 2026-05-28 |
| 2 | [chat-list](#screen-2-man-hinh-danh-sach-hoi-thoai-chat-list-screen---home) | draft | uc-chat-navigation | — | — | 2026-05-28 |
| 3 | [chat-detail](#screen-3-man-hinh-chi-tiet-chat-1-1-nhom-ai-chat-screen) | draft | uc-chat-messaging | — | — | 2026-05-28 |
| 4 | [group-settings](#screen-4-man-hinh-quan-ly-nhom--phan-quyen-group-settings--admin) | draft | uc-group-admin | — | — | 2026-05-28 |
| 5 | [contact-directory](#screen-5-man-hinh-danh-ba--to-chuc-contact--org-chart) | draft | uc-contact-directory | — | — | 2026-05-28 |
| 6 | [search-results](#screen-6-man-hinh-tim-kiem-toan-cuc-global-search) | draft | uc-global-search | — | — | 2026-05-28 |
| 7 | [user-profile](#screen-7-man-hinh-thong-tin-ca-nhan--cai-dat-user-profile--settings) | draft | uc-profile-settings | — | — | 2026-05-28 |
| 8 | [admin-panel](#screen-8-bang-dieu-khien-quan-tri-audit-log-admin-panel) | draft | uc-admin-compliance | — | — | 2026-05-28 |
| 2 | [chat-list](#screen-2-man-hinh-danh-sach-hoi-thoai-chat-list-screen---home) | draft | uc-chat-navigation | — | — | 2026-05-28 |
| 3 | [chat-detail](#screen-3-man-hinh-chi-tiet-chat-1-1-nhom-ai-chat-screen) | draft | uc-chat-messaging | — | — | 2026-05-28 |
| 4 | [group-settings](#screen-4-man-hinh-quan-ly-nhom--phan-quyen-group-settings--admin) | draft | uc-group-admin | — | — | 2026-05-28 |
| 5 | [contact-directory](#screen-5-man-hinh-danh-ba--to-chuc-contact--org-chart) | draft | uc-contact-directory | — | — | 2026-05-28 |
| 6 | [search-results](#screen-6-man-hinh-tim-kiem-toan-cuc-global-search) | draft | uc-global-search | — | — | 2026-05-28 |
| 7 | [user-profile](#screen-7-man-hinh-thong-tin-ca-nhan--cai-dat-user-profile--settings) | draft | uc-profile-settings | — | — | 2026-05-28 |
| 8 | [bod-dashboard](#screen-8-man-hinh-dashboard-ban-lanh-dao-bod-dashboard-screen) | draft | uc-bod-decide | — | — | 2026-05-28 |
| 9 | [admin-panel](#screen-9-bang-dieu-khien-quan-tri-audit-log-admin-panel) | draft | uc-admin-compliance | — | — | 2026-05-28 |
| 2 | [chat-list](#screen-2-man-hinh-danh-sach-hoi-thoai-chat-list-screen---home) | draft | uc-chat-navigation / FR-8.1, FR-8.2, FR-8.5, FR-16.12 | — | — | 2026-05-28 |
| 3 | [chat-detail](#screen-3-man-hinh-chi-tiet-chat-1-1-nhom-ai-chat-screen) | draft | uc-chat-messaging, uc-ai-helper / FR-2, FR-5, FR-11.1..11.4 | — | — | 2026-05-28 |
| 4 | [workflow-approval](#screen-4-man-hinh-phe-duyet-quy-trinh-workflow-approval-screen) | draft | uc-workflow-approval / FR-14.1, FR-14.2, FR-14.4 | — | — | 2026-05-28 |
| 5 | [bod-dashboard](#screen-5-man-hinh-dashboard-ban-lanh-dao-bod-dashboard-screen) | draft | uc-bod-decide / FR-15.1..15.4, FR-15.14 | — | — | 2026-05-28 |
| 6 | [admin-panel](#screen-6-bang-dieu-khien-quan-tri-audit-log-admin-panel) | draft | uc-admin-compliance / FR-9.1..9.3, FR-9.6 | — | — | 2026-05-28 |
| 7 | [user-profile](#screen-7-man-hinh-thong-tin-ca-nhan--cai-dat-user-profile--settings) | draft | uc-profile-settings | — | — | 2026-05-28 |
| 8 | [group-settings](#screen-8-man-hinh-quan-ly-nhom--phan-quyen-group-settings--admin) | draft | uc-group-admin | — | — | 2026-05-28 |
| 9 | [contact-directory](#screen-9-man-hinh-danh-ba--to-chuc-contact--org-chart) | draft | uc-contact-directory | — | — | 2026-05-28 |
| 10 | [search-results](#screen-10-man-hinh-tim-kiem-toan-cuc-global-search) | draft | uc-global-search | — | — | 2026-05-28 |
| 11 | [ai-hub](#screen-11-man-hinh-tro-ly-ai--prompt-hub-ai-hub) | draft | uc-ai-hub | — | — | 2026-05-28 |

---

## 4. Edge Cases (Trường Hợp Ngoại Lệ & Hướng Xử Lý UX)

- **Trường hợp 1: Tin nhắn chat chứa thông tin nhạy cảm hoặc vi phạm chính sách bảo mật công ty (ví dụ: chia sẻ mật khẩu, mã nguồn).**
  - **Hướng xử lý UX:** Hệ thống AI quét ngầm realtime (theo chính sách DLP của doanh nghiệp). Nếu phát hiện vi phạm, tin nhắn sẽ bị ẩn đi kèm thông báo cho người gửi: *"Tin nhắn bị ẩn do vi phạm chính sách bảo mật thông tin doanh nghiệp. Hành động đã được ghi vào nhật ký kiểm toán."*

---

## 5. Ghi Chú Thêm Cho Designer

- **Phong cách thiết kế:** Sử dụng phong cách Glassmorphism nhẹ nhàng (mờ nền) cho các popup điều hướng và bảng phê duyệt trên thiết bị di động để tạo sự hiện đại, sang trọng.
- **Khả năng tiếp cận (Accessibility):**
  - Đảm bảo tỷ lệ tương phản chữ đạt tiêu chuẩn WCAG 2.1 AA (tỷ lệ tương phản tối thiểu 4.5:1 đối với văn bản thông thường).
  - Tích hợp điều khiển giọng nói (TTS/STT) trực quan để hỗ trợ nhân viên thao tác rảnh tay.
- **Hiệu ứng chuyển cảnh (Transitions):** Các hiệu ứng mở cửa sổ chat, lướt phê duyệt cần có độ trễ cực thấp (<150ms) tạo cảm giác ứng dụng mượt mà, phản hồi tức thì.
### Screen 9: Màn hình Thông tin cá nhân & Cài đặt (User Profile & Settings)
**Mục đích:** Quản lý thông tin định danh nội bộ, cài đặt trạng thái hoạt động và cá nhân hóa trải nghiệm.

#### A. Phác họa Giao diện (Wireframe - Visual Sequence)
<details>
<summary>📱 Thông tin cá nhân <code>user-profile</code></summary>

```text
┌─────────────────────────────────────────┐
│ [ < ]  Thông tin tài khoản              │
├─────────────────────────────────────────┤
│        [ 🧑 Avatar lớn ]                │
│        Trần Văn C                       │
│        Trưởng phòng Marketing           │
│        tranc@company.com | 0901234567   │
├─────────────────────────────────────────┤
│ Trạng thái:                             │
│ ( ) Online   (x) Bận   ( ) Đi vắng      │
├─────────────────────────────────────────┤
│ ⚙️ Cài đặt ứng dụng                      │
│ - 🔔 Thông báo (Bật)                     │
│ - 🌙 Giao diện tối (Tự động)             │
│ - 🔒 Quyền riêng tư & Bảo mật            │
├─────────────────────────────────────────┤
│ [     Đăng xuất khỏi thiết bị này     ] │
└─────────────────────────────────────────┘
```
</details>

#### B. Cấu trúc Layout & Components (Anatomy)
- **Header:** Nút Back + Tiêu đề.
- **Body/Main Content:**
  - Avatar, Họ tên, Chức vụ, Email công ty, Số điện thoại.
  - Quản lý trực tiếp (Manager) & Phòng ban trực thuộc.
  - Radio buttons/Dropdown chọn trạng thái hoạt động (Presence).
  - List menu cài đặt: Thông báo (push/email), Giao diện (Dark/Light mode), Bảo mật (Đổi mã PIN/FaceID).
- **Footer:** Nút Đăng xuất màu đỏ.

#### C. Component & Data
| Component | Loại | Mô tả dữ liệu / Logic | Ràng buộc (Validation) |
|-----------|------|------------------------|-----------------------|
| Trạng thái | Radio/Select | Đồng bộ trạng thái hiện diện (Presence) tới tất cả người dùng khác realtime. | Các trạng thái tự động reset về Online vào đầu giờ làm việc hôm sau (tùy chọn). |
| Nút Đăng xuất | Destructive CTA | Xóa toàn bộ token phiên đăng nhập, xóa cache nội dung chat offline để bảo mật. | Yêu cầu xác nhận 2 lần trước khi đăng xuất. |

---

### Screen 10: Màn hình Dashboard Ban Lãnh Đạo (BOD Dashboard Screen)
**Mục đích:** Cung cấp thông tin tổng hợp trực quan và hỗ trợ AI ra quyết định nhanh cho ban lãnh đạo.

#### A. Phác họa Giao diện (Wireframe - Visual Sequence)
<details>
<summary>📱 Dashboard điều hành <code>bod-dashboard</code></summary>

**Mô tả:** Màn hình của lãnh đạo với thẻ KPIs tài chính, hộp thoại cảnh báo chủ động và cổng giao việc nhanh bằng AI.

```text
┌─────────────────────────────────────────┐
│        Báo cáo điều hành            [⚙️]│
│        Cập nhật: 10:00 (hôm nay)        │
├─────────────────────────────────────────┤
│  KPIs:                                  │
│  ┌─────────────────┐ ┌────────────────n│
│  │ Doanh thu tháng │ │ Nhân sự mới    │ │
│  │ 2.4 Tỷ (+12%)   │ │ 15 người       │ │
│  └─────────────────┘ └────────────────┘ │
│                                         │
│  ⚠️ Cảnh báo: Ngân sách Dự án X vượt 15%│
├─────────────────────────────────────────┤
│ 🤖 Trợ lý số liệu BOD                   │
│ ┌─────────────────────────────────────┐ │
│ │ Hãy hỏi trợ lý về số liệu ERP...   │ │
│ └─────────────────────────────────────┘ │
│ ┌─────────────────────────────────────┐ │
│ │ [➕ Giao việc nhanh cho cấp dưới]   │ │
│ └─────────────────────────────────────┘ │
└─────────────────────────────────────────┘
```
</details>

#### B. Cấu trúc Layout & Components (Anatomy)
- **Header:** Tiêu đề "Báo cáo điều hành" + Thời gian cập nhật gần nhất + Icon cấu hình cảnh báo.
- **Body/Main Content:**
  - Khối biểu đồ KPI tóm tắt (Doanh thu, Nhân sự, Tiến độ dự án trọng điểm).
  - Khối Cảnh báo sớm (Anomaly Alert): Các thông số vượt ngưỡng (Ví dụ: *"Dự án X chậm tiến độ 3 ngày"*).
  - Ô chat tương tác trực tiếp với Trợ lý AI của lãnh đạo.
- **Footer:** Bottom navigation.

#### C. Component & Data
| Component | Loại | Mô tả dữ liệu / Logic | Ràng buộc (Validation) |
|-----------|------|------------------------|-----------------------|
| Biểu đồ KPI | Chart Widget | Hiển thị dạng đường, cột hoặc tròn. Cho phép click để xem chi tiết theo tháng/tuần. | Dữ liệu đồng bộ tự động mỗi 1 giờ. |
| Khung Chat AI Lãnh đạo | Text Input | Lãnh đạo nhập câu hỏi tự nhiên về số liệu (Ví dụ: *"So sánh chi phí dự án A và B"*). AI phản hồi bằng bảng số liệu và biểu đồ trực quan. | Phân quyền nghiêm ngặt chỉ tài khoản thuộc nhóm BOD mới truy cập được. |
| Nút "Giao việc nhanh" | Action CTA | Xuất hiện bên cạnh câu trả lời của AI hoặc thẻ KPI lỗi. Click để tạo nhanh task giao xuống cấp dưới. | Bắt buộc chọn Người chịu trách nhiệm (Assignee) và Hạn chót (Deadline). |

#### D. Trạng thái giao diện (UI States)
- ⚪ **Empty State:** Chưa cấu hình chỉ số KPI nào. Hiển thị text gợi ý: *"Vui lòng chọn các chỉ số cốt lõi bạn muốn theo dõi hàng ngày."*
- 🔄 **Loading State:** Hiển thị hiệu ứng shimmer cho các biểu đồ trong lúc tính toán số liệu.
- 🔴 **Error State:** Lỗi tải dữ liệu ERP: Hiển thị icon cảnh báo lỗi kèm nút *"Tải lại dữ liệu"*.
- 🟢 **Success State:** Quyết định giao việc được tạo thành công, tự động gửi thông báo xuống ứng dụng của nhân viên chịu trách nhiệm.

#### E. Copywriting (Microcopy)
- Title: `Dashboard Điều Hành`
- AI Assistant Input: `Hỏi trợ lý số liệu doanh nghiệp...`
- Alert Text: `⚠️ Cảnh báo: Ngân sách dự án Y đã vượt 15% hạn mức.`

---

### Screen 11: Bảng Điều Khiển Quản Trị & Audit Log (Admin Panel)
**Mục đích:** Đảm bảo tính kiểm soát, tuân thủ chính sách và bảo mật dữ liệu doanh nghiệp (Đặc thù nội bộ).

#### A. Phác họa Giao diện (Wireframe - Visual Sequence)
<details>
<summary>🖥️ Bảng quản trị <code>admin-panel</code></summary>

**Mô tả:** Bản thiết kế giao diện rộng (Desktop/Web) cho Admin theo dõi danh sách Audit Log, lọc hành động, địa chỉ IP và trạng thái phiên làm việc.

```text
┌─────────────────────────────────────────┐
│ Bảng điều khiển Admin - Audit Log [Web] │
├─────────────────────────────────────────┤
│ [👤 User] [👥 Groups] [📋 Logs] [⚙️ Config]│
├─────────────────────────────────────────┤
│ Bộ lọc: [Hành động v] [Nhân viên v]     │
├─────────────────────────────────────────┤
│ Thời gian | Nhân viên | Hành động | IP  │
├───────────┼───────────┼───────────┼─────┤
│ 10:14:15  │ Admin     │ Lock user │ 1.1 │
├───────────┼───────────┼───────────┼─────┤
│ 10:12:00  │ N.V. A    │ Login     │ 2.3 │
├───────────┼───────────┼───────────┼─────┤
│ 09:45:10  │ L.T. B    │ Edit msg  │ 4.5 │
└───────────┴───────────┴───────────┴─────┘
```
</details>

#### B. Cấu trúc Layout & Components (Anatomy)
- **Side Navigation (Bản Web/Desktop):** Người dùng | Nhóm & Kênh | Quyền hạn & Phân vai (RBAC) | Nhật ký hệ thống (Audit Log) | Cấu hình bảo mật.
- **Body/Main Content:**
  - Bảng hiển thị danh sách chi tiết (ví dụ: Danh sách Audit Log).
  - Các bộ lọc nâng cao (Tìm theo thời gian, theo hành động: Gửi/Xóa/Sửa/Đăng nhập, theo nhân viên).
  - Khối thông tin chi tiết của hành động được chọn (Thiết bị, IP, tọa độ, hành động cụ thể).
- **Header/Footer:** Tên phiên quản trị, nút Đăng xuất an toàn.

#### C. Component & Data
| Component | Loại | Mô tả dữ liệu / Logic | Ràng buộc (Validation) |
|-----------|------|------------------------|-----------------------|
| Bảng dữ liệu log | Data Table | Liệt kê các bản ghi lịch sử tương tác. Không cho phép sửa hay xóa bản ghi log dưới bất kỳ hình thức nào. | Sắp xếp giảm dần theo thời gian (mới nhất lên đầu). |
| Công cụ Lọc dữ liệu | Filter Group | Lọc log theo Loại hành động, Người thực hiện, Khoảng thời gian. | Thời gian lọc tối đa 90 ngày mỗi lượt để tối ưu hiệu năng. |
| Nút "Khóa tài khoản" | Secondary CTA | Cho phép Admin vô hiệu hóa tài khoản nhân viên ngay lập tức (khi nghỉ việc hoặc nghi ngờ lộ thông tin). | Yêu cầu Admin xác nhận mật khẩu trước khi khóa. |

#### D. Trạng thái giao diện (UI States)
- ⚪ **Empty State:** Kết quả lọc không có bản ghi nào phù hợp. Hiển thị text: *"Không tìm thấy nhật ký tương ứng với bộ lọc."*
- 🔄 **Loading State:** Hàng đợi tải dữ liệu lớn hiển thị thanh tiến trình (progress bar).
- 🔴 **Error State:** Lỗi truy vấn dữ liệu log: Hiển thị thông báo đỏ ở góc: *"Lỗi kết nối cơ sở dữ liệu nhật ký. Vui lòng kiểm tra lại cấu hình."*
- 🟢 **Success State:** Vô hiệu hóa tài khoản thành công, hệ thống lập tức ngắt toàn bộ phiên kết nối (WebSocket) đang hoạt động của tài khoản đó.

#### E. Copywriting (Microcopy)
- Table Columns: `Thời gian` | `Nhân viên` | `Hành động` | `Thiết bị` | `IP`
- Notification: `Tài khoản [Tên] đã bị khóa thành công và đăng xuất khỏi toàn bộ thiết bị.`

---


## 3. Tổng Hợp Các Màn Hình (Screens Overview)

Bảng tổng hợp nhanh các màn hình đã thiết kế chi tiết ở trên để theo dõi trạng thái và sự liên kết:

| # | Slug | Status | Used by (UC / FR) | Figma | HTML prototype | Updated |
|---|------|--------|-------------------|-------|----------------|---------|
| 1 | [auth-login](#screen-1-man-hinh-dang-nhap-xac-thuc-otp-authentication-screen) | draft | uc-login-email | — | — | 2026-05-28 |
| 2 | [chat-list](#screen-2-man-hinh-danh-sach-hoi-thoai-chat-list-screen---home) | draft | uc-chat-navigation | — | — | 2026-05-28 |
| 3 | [chat-detail](#screen-3-man-hinh-chi-tiet-chat-1-1-nhom-ai-chat-screen) | draft | uc-chat-messaging | — | — | 2026-05-28 |
| 4 | [group-settings](#screen-4-man-hinh-quan-ly-nhom--phan-quyen-group-settings--admin) | draft | uc-group-admin | — | — | 2026-05-28 |
| 5 | [contact-directory](#screen-5-man-hinh-danh-ba--to-chuc-contact--org-chart) | draft | uc-contact-directory | — | — | 2026-05-28 |
| 6 | [search-results](#screen-6-man-hinh-tim-kiem-toan-cuc-global-search) | draft | uc-global-search | — | — | 2026-05-28 |
| 7 | [user-profile](#screen-7-man-hinh-thong-tin-ca-nhan--cai-dat-user-profile--settings) | draft | uc-profile-settings | — | — | 2026-05-28 |
| 8 | [admin-panel](#screen-8-bang-dieu-khien-quan-tri-audit-log-admin-panel) | draft | uc-admin-compliance | — | — | 2026-05-28 |
| 2 | [chat-list](#screen-2-man-hinh-danh-sach-hoi-thoai-chat-list-screen---home) | draft | uc-chat-navigation | — | — | 2026-05-28 |
| 3 | [chat-detail](#screen-3-man-hinh-chi-tiet-chat-1-1-nhom-ai-chat-screen) | draft | uc-chat-messaging | — | — | 2026-05-28 |
| 4 | [group-settings](#screen-4-man-hinh-quan-ly-nhom--phan-quyen-group-settings--admin) | draft | uc-group-admin | — | — | 2026-05-28 |
| 5 | [contact-directory](#screen-5-man-hinh-danh-ba--to-chuc-contact--org-chart) | draft | uc-contact-directory | — | — | 2026-05-28 |
| 6 | [search-results](#screen-6-man-hinh-tim-kiem-toan-cuc-global-search) | draft | uc-global-search | — | — | 2026-05-28 |
| 7 | [user-profile](#screen-7-man-hinh-thong-tin-ca-nhan--cai-dat-user-profile--settings) | draft | uc-profile-settings | — | — | 2026-05-28 |
| 8 | [bod-dashboard](#screen-8-man-hinh-dashboard-ban-lanh-dao-bod-dashboard-screen) | draft | uc-bod-decide | — | — | 2026-05-28 |
| 9 | [admin-panel](#screen-9-bang-dieu-khien-quan-tri-audit-log-admin-panel) | draft | uc-admin-compliance | — | — | 2026-05-28 |
| 2 | [chat-list](#screen-2-man-hinh-danh-sach-hoi-thoai-chat-list-screen---home) | draft | uc-chat-navigation / FR-8.1, FR-8.2, FR-8.5, FR-16.12 | — | — | 2026-05-28 |
| 3 | [chat-detail](#screen-3-man-hinh-chi-tiet-chat-1-1-nhom-ai-chat-screen) | draft | uc-chat-messaging, uc-ai-helper / FR-2, FR-5, FR-11.1..11.4 | — | — | 2026-05-28 |
| 4 | [group-settings](#screen-4-man-hinh-quan-ly-nhom--phan-quyen-group-settings--admin) | draft | uc-group-admin | — | — | 2026-05-28 |
| 5 | [contact-directory](#screen-5-man-hinh-danh-ba--to-chuc-contact--org-chart) | draft | uc-contact-directory | — | — | 2026-05-28 |
| 6 | [search-results](#screen-6-man-hinh-tim-kiem-toan-cuc-global-search) | draft | uc-global-search | — | — | 2026-05-28 |
| 7 | [workflow-approval](#screen-7-man-hinh-phe-duyet-quy-trinh-workflow-approval-screen) | draft | uc-workflow-approval / FR-14.1, FR-14.2, FR-14.4 | — | — | 2026-05-28 |
| 8 | [ai-hub](#screen-8-man-hinh-tro-ly-ai--prompt-hub-ai-hub) | draft | uc-ai-hub | — | — | 2026-05-28 |
| 9 | [user-profile](#screen-9-man-hinh-thong-tin-ca-nhan--cai-dat-user-profile--settings) | draft | uc-profile-settings | — | — | 2026-05-28 |
| 10 | [bod-dashboard](#screen-10-man-hinh-dashboard-ban-lanh-dao-bod-dashboard-screen) | draft | uc-bod-decide / FR-15.1..15.4, FR-15.14 | — | — | 2026-05-28 |
| 11 | [admin-panel](#screen-11-bang-dieu-khien-quan-tri-audit-log-admin-panel) | draft | uc-admin-compliance / FR-9.1..9.3, FR-9.6 | — | — | 2026-05-28 |
| 2 | [chat-list](#screen-2-man-hinh-danh-sach-hoi-thoai-chat-list-screen---home) | draft | uc-chat-navigation / FR-8.1, FR-8.2, FR-8.5, FR-16.12 | — | — | 2026-05-28 |
| 3 | [chat-detail](#screen-3-man-hinh-chi-tiet-chat-1-1-nhom-ai-chat-screen) | draft | uc-chat-messaging, uc-ai-helper / FR-2, FR-5, FR-11.1..11.4 | — | — | 2026-05-28 |
| 4 | [workflow-approval](#screen-4-man-hinh-phe-duyet-quy-trinh-workflow-approval-screen) | draft | uc-workflow-approval / FR-14.1, FR-14.2, FR-14.4 | — | — | 2026-05-28 |
| 5 | [bod-dashboard](#screen-5-man-hinh-dashboard-ban-lanh-dao-bod-dashboard-screen) | draft | uc-bod-decide / FR-15.1..15.4, FR-15.14 | — | — | 2026-05-28 |
| 6 | [admin-panel](#screen-6-bang-dieu-khien-quan-tri-audit-log-admin-panel) | draft | uc-admin-compliance / FR-9.1..9.3, FR-9.6 | — | — | 2026-05-28 |
| 7 | [user-profile](#screen-7-man-hinh-thong-tin-ca-nhan--cai-dat-user-profile--settings) | draft | uc-profile-settings | — | — | 2026-05-28 |
| 8 | [group-settings](#screen-8-man-hinh-quan-ly-nhom--phan-quyen-group-settings--admin) | draft | uc-group-admin | — | — | 2026-05-28 |
| 9 | [contact-directory](#screen-9-man-hinh-danh-ba--to-chuc-contact--org-chart) | draft | uc-contact-directory | — | — | 2026-05-28 |
| 10 | [search-results](#screen-10-man-hinh-tim-kiem-toan-cuc-global-search) | draft | uc-global-search | — | — | 2026-05-28 |
| 11 | [ai-hub](#screen-11-man-hinh-tro-ly-ai--prompt-hub-ai-hub) | draft | uc-ai-hub | — | — | 2026-05-28 |

---

## 4. Edge Cases (Trường Hợp Ngoại Lệ & Hướng Xử Lý UX)

- **Trường hợp 1: Tin nhắn chat chứa thông tin nhạy cảm hoặc vi phạm chính sách bảo mật công ty (ví dụ: chia sẻ mật khẩu, mã nguồn).**
  - **Hướng xử lý UX:** Hệ thống AI quét ngầm realtime (theo chính sách DLP của doanh nghiệp). Nếu phát hiện vi phạm, tin nhắn sẽ bị ẩn đi kèm thông báo cho người gửi: *"Tin nhắn bị ẩn do vi phạm chính sách bảo mật thông tin doanh nghiệp. Hành động đã được ghi vào nhật ký kiểm toán."*

---

## 5. Ghi Chú Thêm Cho Designer

- **Phong cách thiết kế:** Sử dụng phong cách Glassmorphism nhẹ nhàng (mờ nền) cho các popup điều hướng và bảng phê duyệt trên thiết bị di động để tạo sự hiện đại, sang trọng.
- **Khả năng tiếp cận (Accessibility):**
  - Đảm bảo tỷ lệ tương phản chữ đạt tiêu chuẩn WCAG 2.1 AA (tỷ lệ tương phản tối thiểu 4.5:1 đối với văn bản thông thường).
  - Tích hợp điều khiển giọng nói (TTS/STT) trực quan để hỗ trợ nhân viên thao tác rảnh tay.
- **Hiệu ứng chuyển cảnh (Transitions):** Các hiệu ứng mở cửa sổ chat, lướt phê duyệt cần có độ trễ cực thấp (<150ms) tạo cảm giác ứng dụng mượt mà, phản hồi tức thì.
