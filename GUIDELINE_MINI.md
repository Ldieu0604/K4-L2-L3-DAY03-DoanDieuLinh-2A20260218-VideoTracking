# Mini annotation guideline — Ngày 3 (tracking)

> Điền file này **trong lúc gán nhãn**, không phải sau khi xong. Mỗi lần bạn dừng
> lại nghĩ "cái này tính sao nhỉ?" thì đó là một dòng phải ghi vào đây.
>
> Đây là tài liệu mà người gán nhãn tiếp theo sẽ đọc để làm giống bạn. Nếu hai
> người trong nhóm gán khác nhau, gần như luôn là vì file này chưa nói rõ — chứ
> không phải vì ai kém.

Nhóm / tên: Đoàn Diệu Linh
Clip: `clip_01`, `clip_02`

---

## 1. Phạm vi: gán cái gì, không gán cái gì

Một lớp duy nhất: **`vehicle`** — xe bốn bánh (xe con, van, xe buýt, xe tải).

| Gán | Không gán |
| --- | --- |
| xe con, SUV, taxi, xe bán tải | người đi bộ |
| van, minivan | xe đạp |
| xe buýt, minibus | **xe máy / mô tô** |
| xe tải, xe đầu kéo | xe trong ảnh quảng cáo, trong gương, dưới bóng nước |

Bổ sung của nhóm (nếu có): Không bổ sung 

## 2. Luật ID — phần quan trọng nhất

| Tình huống | Luật của tôi | Vì sao |
| --- | --- | --- |
| Xe bị che một phần rồi hiện lại | giữ nguyên ID nếu bị che **dưới 30 frame** (mặc định của lab: 25 frame = 2 giây @ 12.5 fps) | Vì xe vẫn trong scene không biến mất, sau vài giây xe hiện lại |
| Xe bị che lâu hơn ngưỡng trên | nhấn O nếu vượt ngưỡng | xe biến mất khỏi frame |
| Xe rời khung hình rồi quay lại | mặc định: **track mới** | quá ngưỡng nên mặc định xe mới |
| Hai xe cắt nhau / chồng lên nhau | gán 2 ID kèm occulate cho xe bị khuất sau vài frame khi xe bị khuất xuất hiện còn trc đó chỉ gán 1 ID cho xe nhìn rõ  | không đủ dữ liệu xác định có xe ở sau |

## 3. Luật bbox

| Tình huống | Luật của nhóm |
| --- | --- |
| Xe bị cắt bởi rìa ảnh | bbox chạm đúng rìa, không đoán phần ngoài ảnh |
| Xe bị xe khác che một phần | bbox ôm phần **nhìn thấy được** |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | bắt đầu track từ frame đầu tiên xác định được là xe bốn bánh; ngưỡng nhóm chọn: 2 |
| Xe đang đỗ, không di chuyển | giữ nguyên ID không đổi track |
| Keyframe đặt dày ở đâu | xe di chuyển |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

Ghi **frame cụ thể** và **ID cụ thể**, không ghi chung chung.

### Ca 1
- Clip / frame / ID: clip 01 frame 104 ID 7
- Tình huống: phương tiện nhìn thấy nhưng bị che khuất và còn mờ
- Quyết định: Đặt ngưỡng để vẽ box
- Lý do: vì xe còn xuất hiện rõ ở các frame sau

### Ca 2
- Clip / frame / ID: clip 01 frame 130 ID 5
- Tình huống: phương tiện rời khỏi frame nhưng vẫn còn bóng nhòe
- Quyết định: hỏi labcoach xác định
- Lý do: hỏi lại cho chắc chắn

### Ca 3
- Clip / frame / ID: clip 01 frame 169 ID 8
- Tình huống: phương tiện rời khỏi frame nhưng vẫn còn bóng nhòe
- Quyết định: hỏi labcoach xác định
- Lý do: xác định lại 

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo 

Không sửa lại do đã ĐẠT annotation
