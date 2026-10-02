# Aura Widget — widget trình phát nhạc cho Android

Widget màn hình chính (home screen widget) điều khiển nhạc của **mọi app**: YouTube, YouTube Music, Spotify, Apple Music, SoundCloud, Zing MP3, trình phát nhạc máy…

## Tính năng

| | |
|---|---|
| 5 theme | Liquid Glass · Nothing (font dot-matrix Doto) · Material You (màu từ hình nền, thanh sóng M3) · AMOLED · Album blur (nền là ảnh bìa làm mờ) |
| Bắt media | `NotificationListenerService` + `MediaSessionManager` — tự chuyển sang app vừa bắt đầu phát |
| Điều khiển | Phát/tạm dừng, bài trước/tiếp, chạm ảnh bìa để mở app nguồn |
| Thanh tiến trình | Vẽ bằng bitmap, cập nhật mỗi giây khi đang phát và màn hình đang bật (tắt màn hình thì dừng để tiết kiệm pin) |
| Chọn output | Nút tai nghe mở bottom sheet: danh sách thiết bị (loa, Bluetooth, tai nghe dây, USB, HDMI, cast), thanh âm lượng, nút mở bộ chuyển output của hệ thống |

## Build

1. Mở thư mục `AuraWidget` bằng **Android Studio** (Ladybug 2024.2 trở lên).
2. Chờ Gradle sync (AGP 8.7.3, Kotlin 2.0.21, compileSdk 35, minSdk 26).
3. Chạy `app` lên điện thoại, hoặc `Build → Build APK(s)`.

Dòng lệnh: `./gradlew assembleDebug` → `app/build/outputs/apk/debug/app-debug.apk`

## Lần đầu dùng

1. Mở app **Aura Widget** → **Cấp quyền** → bật *Notification access* (quyền truy cập thông báo).
   - Android 13+ với APK cài ngoài Play Store: nếu công tắc bị mờ ("Restricted setting"), vào *Thông tin app → ⋮ → Allow restricted settings*.
2. Chọn theme → **Thêm widget vào màn hình chính** (hoặc nhấn giữ màn hình chính → Widget → Aura Player).
3. Phát nhạc ở bất kỳ app nào; widget tự cập nhật.

## Cấu trúc

```
media/MediaListenerService.kt   bắt media session, ticker 1 giây, theo dõi thiết bị âm thanh
media/MediaRepository.kt        controller hiện tại + MediaSnapshot (tên bài, ảnh bìa, vị trí…)
widget/WidgetUpdater.kt         dựng RemoteViews cho từng theme, cập nhật nhẹ mỗi giây
widget/ProgressRenderer.kt      vẽ thanh tiến trình riêng cho từng theme
widget/BitmapUtils.kt           bo góc ảnh bìa, blur nền album, lấy màu nhấn (Palette)
widget/AuraWidgetProvider.kt    nhận cú bấm trên widget -> transportControls
output/OutputSheetActivity.kt   bottom sheet chọn output (Compose)
output/OutputSwitcher.kt        mở bộ chuyển output hệ thống theo phiên bản Android
ui/MainActivity.kt              cấp quyền, chọn theme, xem trước widget thật
tools/gen_layouts.py            sinh 5 layout widget (sửa thông số theme ở đây rồi chạy lại)
```

## Giới hạn của Android (cần biết)

- **Lock screen**: app bên thứ ba không đặt được widget lên màn hình khóa trên điện thoại; player trong ảnh mẫu do ROM vẽ. Aura Widget nằm ở màn hình chính.
- **Liquid Glass**: launcher không cho widget làm mờ hình nền phía sau, nên hiệu ứng kính được mô phỏng bằng các lớp trong suốt, viền sáng và vệt phản chiếu. Đẹp nhất trên hình nền có độ tương phản vừa phải.
- **Chọn output**: app thường không được tự chuyển âm thanh của app khác (cần quyền hệ thống `MEDIA_CONTENT_CONTROL`). Aura liệt kê thiết bị, chỉnh âm lượng, và mở **bộ chuyển output của hệ thống**:
  - Android 12–13: mở thẳng hộp thoại Media output của app đang phát (ROM gốc/Pixel; một số ROM bỏ qua).
  - Android 14+: mở bộ chuyển output hệ thống (`MediaRouter2`) — hộp thoại chung, không gắn riêng app đang phát.
  - Android 8–11 hoặc máy không hỗ trợ (một số ROM như MIUI/HyperOS, ColorOS): mở bảng âm lượng hoặc cài đặt Bluetooth.
- **Font Doto** (theme Nothing) chỉ có Latin + Latin mở rộng: các chữ như ầ, ệ, ư, ơ sẽ dùng font hệ thống thay thế.
- Kéo để tua (seek) không làm được trên widget (RemoteViews không nhận tọa độ chạm).

## Ý tưởng mở rộng

- Theme riêng cho từng widget (màn hình cấu hình khi thêm widget)
- Layout thu gọn 4×1 bằng responsive RemoteViews (Android 12+)
- Nút like/shuffle qua `PlaybackState.customActions`

## Tài liệu cho Claude Code
- `CLAUDE.md` — hướng dẫn cho Claude Code (kiến trúc, build, ràng buộc)
- `docs/HANDOFF.md` — tóm tắt cuộc hội thoại thiết kế và các quyết định
- `docs/TODO.md` — việc tiếp theo theo thứ tự ưu tiên
- `docs/design/` — mockup HTML, ảnh các theme, ảnh tham chiếu
# one-player-widget
