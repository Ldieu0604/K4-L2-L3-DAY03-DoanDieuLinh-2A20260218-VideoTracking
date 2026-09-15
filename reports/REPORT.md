# Báo cáo Ngày 3 — Tracking Annotation

Chép file này thành `reports/REPORT.md` rồi điền. Giữ nguyên các tiêu đề.

Họ tên / nhóm: Đoàn Diệu Linh
Ngày: 14/09/2026

---

## 1. Quá trình gán nhãn

| Mục | Giá trị |
| --- | --- |
| Công cụ | CVAT |
| Thời gian gán `clip_02` (warm-up) | 42 phút |
| Thời gian gán `clip_01` | 135 phút |
| Số track đã vẽ trong `clip_01` | 8 |
| Số keyframe trung bình mỗi track | 2.98 |

Ba tình huống khó nhất khi gán clip này, và bạn xử lý thế nào:

1. phương tiện nhìn thấy nhưng bị che khuất. Xử lý bằng cách annotation kèm occulate
2. Vật thể mờ không nhận biết được vật thể hay không. Xử lý bằng các hỏi labcoach và check nhiều frame để xác định
3. phương tiện ra khỏi frame nhưng vẫn còn chút ảnh mờ nhòe. Xử lý bằng các hỏi labcoach để xác định xem có đủ điều kiện xác định nó là vật thể hay không

## 2. Tự kiểm và kiểm chéo

Ba lượt tua bắt được gì (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa):

- Lượt 1: ID phương tiện không bị lệch khi tua, một phương tiện không bị dính nhiều ID
- Lượt 2: Phương tiện ra khỏi frame thì bondbox đã bị mất khỏi frame không bị lỗi
- Lượt 3: không còn box của phương tiện ra khỏi frame, ID thống nhất. 1 ID ứng 1 vật thể

Kiểm chéo với: chưa thực hiện. Không có `reports/review_partner.md` trong workspace.
Số lỗi bạn tìm được trong bản của bạn ấy: chưa có dữ liệu. Số lỗi bạn ấy tìm được trong bản của bạn: chưa có dữ liệu.

Ca nào hai người quyết khác nhau, và luật nào còn thiếu trong `GUIDELINE_MINI.md`?

Đây là bài làm cá nhân nên không có ca quyết định khác nhau để đối chiếu.

## 3. Pre-gold lock và chấm trước/sau rework

| Evidence | Giá trị |
| --- | --- |
| SHA-256 từ `evidence/pre-gold/clip_01/manifest.json` | `4bad6f3dba61c18b754cea5fd28d7187318681824205438880f1ad99b8bc082e` |
| Thời điểm khóa | `16:45 15/09/2026` |
| Số row / frame / track trước khi mở reference | `567 / 190 / 8` |

| | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Bản pre-gold | 0.8519 | 0.8276 | 0.8826 | 0.8949 | 0.9702 | 0.9407 | 0.8837 | 14 | 20 | 0 |
| Sau rework | 0.8519 | 0.8276 | 0.8826 | 0.8949 | 0.9702 | 0.9407 | 0.8837 | 14 | 20 | 0 |

Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **có**

Sau khi đọc danh sách lỗi, bạn đã sửa cụ thể những gì? Ghi theo frame và ID:

| Loại lỗi | Frame | ID | Đã sửa thế nào |
| --- | --- | --- | --- |
| Không sửa bắt buộc | 88, 94 | 5 | Kiểm tra bbox trôi; kết quả đã đạt cổng nên giữ nguyên bản khóa. |
| Không sửa bắt buộc | 106, 107, 111-115 | 6, 7 | Kiểm tra bbox trôi; không có ID switch và giữ nguyên evidence pre-gold. |
| Không sửa bắt buộc | - | - | Không có missed track, fragmented track hoặc ghost track trong annotation. |

## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục | Giá trị |
| --- | --- |
| Python / ultralytics / torch / lap | `3.13.15 / 8.4.145 / 2.11.0+cu128 / 0.5.13` |
| weights / hai tracker | `yolo26n.pt / bytetrack.yaml / botsort-reid.yaml` |
| conf / IoU / imgsz / classes | `0.25 / 0.70 / 960 / 2, 5, 7` |
| device | `0` |

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| bạn vs gold | 0.8519 | 0.8276 | 0.8826 | 0.8949 | 0.9702 | 0.9407 | 0.8837 | 14 | 20 | 0 |
| ByteTrack control vs gold | 0.7085 | 0.6487 | 0.7761 | 0.8463 | 0.8746 | 0.7487 | 0.8226 | 88 | 54 | 2 |
| BoT-SORT + ReID vs gold | 0.7635 | 0.7110 | 0.8204 | 0.8721 | 0.9001 | 0.7923 | 0.8595 | 91 | 26 | 2 |
| ReID vs bạn | 0.7721 | 0.7134 | 0.8390 | 0.8625 | 0.9178 | 0.8272 | 0.8461 | 84 | 13 | 1 |

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

MOTA của tôi thấp hơn IDF1 (`0.9407` so với `0.9702`). Với model, MOTA có thể cao nhưng IDF1 thấp khi detector vẫn bắt được nhiều vật thể nhưng gán hoặc duy trì ID kém. MOTA chủ yếu trừ FN, FP và IDSW trên tổng số ground-truth object hiện diện; một số lỗi nhận dạng ID không tạo ra nhiều lỗi detection nên không bị phạt nặng như trong IDF1, vốn đo trực tiếp độ đúng của liên kết danh tính.

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

So với gold, BoT-SORT + ReID cao hơn ByteTrack ở IDF1 (`0.9001` so với `0.8746`) và AssA (`0.8204` so với `0.7761`), nhưng cả hai đều có 2 IDSW. ReID cải thiện association tổng thể nhưng không loại bỏ hoàn toàn việc đổi ID. Một chuỗi đáng chú ý là vùng track 5 quanh frame 87-94 và track 6 quanh frame 104-115: ReID có các đoạn fragment tại frame 87 và 113, còn ByteTrack có IDSW ở frame 94 và track 4 ở frame 59. Đây không phải causal effect thuần của ReID vì hai kết quả dùng hai tracker implementation khác nhau.

**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

ReID có DetA `0.7110`, cao hơn ByteTrack `0.6487`; FN giảm từ `54` xuống `26`, nhưng FP tăng từ `88` lên `91`. Vì vậy ReID giúp phủ đúng vật thể tốt hơn, nhưng vẫn tạo thêm một số detection dư. Lỗi còn lại nghiêng về detector/coverage và một phần association: ByteTrack có 2 IDSW, ReID cũng có 2 IDSW; các track bị fragment cho thấy association vẫn chưa ổn định ở vùng che khuất hoặc chuyển động nhanh.

**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

Frame 110, track 7: annotation của tôi giữ ID ổn định, trong khi ReID có IDSW từ track 28 sang track 31. Đây là trường hợp annotation đúng hơn theo gold vì không phát sinh đổi ID.

**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao), hoặc lý do evidence cho thấy model sai:**

ReID không cung cấp bằng chứng đủ mạnh để sửa annotation. Ở vùng frame 104-115, ReID có bbox IoU thấp và đổi ID quanh track 6/7, trong khi annotation đạt `IDSW = 0` và chỉ có cảnh báo bbox trôi. Vì vậy evidence hiện tại cho thấy model gặp khó khi association, không phải annotation cần đổi.

## 6. Nếu phải gán thêm 10 clip nữa

Bạn sẽ sửa gì trong `GUIDELINE_MINI.md`, và đổi gì trong quy trình làm việc của mình?

Tôi sẽ ghi rõ hơn các quy tắc cho xe bị che, xe rời khung và thời điểm nhấn `outside`; thêm ví dụ frame/ID cụ thể cho từng ca. Quy trình mới: kiểm tra clip một lượt để lập danh sách ID, đặt keyframe dày hơn quanh lúc xe đổi hướng hoặc bị che, chạy `check_mot_labels.py`, xem visualization, khóa pre-gold bằng manifest, rồi mới dùng gold để phân tích và chạy hai tracker với cùng cấu hình detector.

## 7. Tệp đã nộp

- [x] `annotations/clip_01/gt.txt`
- [x] `annotations/clip_02/gt.txt`
- [x] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json`
- [x] `GUIDELINE_MINI.md` đã điền
- [x] `outputs/eval_vs_gold.json`
- [x] `outputs/model_bytetrack_clip_01.txt`
- [x] `outputs/model_reid_clip_01.txt`
- [x] `outputs/model_run_config.json`
- [x] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [ ] `reports/review_partner.md` (bài cá nhân, không thực hiện kiểm chéo)
- [x] `reports/REPORT.md` (file này)
