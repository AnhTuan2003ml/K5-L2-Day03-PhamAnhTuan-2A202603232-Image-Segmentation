# Báo cáo thực hành Day 3

- Học viên: Phạm Anh Tuấn; mã theo tên thư mục bài làm: `2A202603232` (cần xác nhận mã lớp).
- Ngày và công cụ thực tế đã sử dụng: chưa có thông tin để xác nhận.

## 1. Bài đã nộp

Chín ZIP đều đọc được và vượt qua kiểm tra cấu trúc bằng `scripts/inspect_submissions.py`. Số ảnh dưới đây là số ảnh trong export, không khẳng định mọi vùng đã được gán nhãn chính xác hay đã hoàn thành QC trực quan.

| Task | File ZIP | Ảnh trong export | Điểm tối đa |
| --- | --- | ---: | ---: |
| easy_semantic | `submissions/easy_semantic.zip` | 3 / 3 | 20 |
| medium_instance | `submissions/medium_instance.zip` | 3 / 3 | 32 |
| hard_panoptic | `submissions/hard_panoptic.zip` | 2 / 2 | 30 |
| cp1_holes | `submissions/cp1_holes.zip` | 1 / 1 | 3 |
| cp2_slice | `submissions/cp2_slice.zip` | 1 / 1 | 3 |
| cp5_occlusion | `submissions/cp5_occlusion.zip` | 1 / 1 | 3 |
| cp3_thin | `submissions/cp3_thin.zip` | 1 / 1 | 3 |
| cp4_curb | `submissions/cp4_curb.zip` | 1 / 1 | 3 |
| cp6_coverage | `submissions/cp6_coverage.zip` | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

Đối chiếu ba tier với `tiers_gt` bằng scorer của repo:

| Task | Metric | Kết quả | Điểm phản hồi |
| --- | --- | ---: | ---: |
| easy_semantic | mIoU | 0,791 | 17,4 / 20 |
| medium_instance | mean matched IoU × recall | 0,686 | 20,3 / 32 |
| hard_panoptic | PQ | 0,361 | 10,7 / 30 |
| **Tổng ba tier** | | | **48,4 / 82** |

Đây là điểm tự đánh giá so với reference, không phải điểm cuối. Không có reference riêng cho sáu checkpoint nên chưa tính 18 điểm còn lại. Kiểm tra cấu trúc không chứng minh đúng biên, số vật hay độ phủ; đặc biệt Hard cần kiểm tra chồng lấn và phủ vùng trong CVAT.

## 2. Một quyết định trước khi dùng gợi ý

ZIP không ghi lại thứ tự vẽ hay việc sử dụng gợi ý. Chưa xác định được object Medium đầu tiên tự vẽ, quy tắc biên và có dùng gợi ý hay không. Học viên cần bổ sung ảnh, vị trí, class, quyết định biên và thao tác với gợi ý nếu có.

## 3. Một lỗi tìm thấy và hướng xử lý

- Task: `medium_instance`, ảnh `000000181542.jpg`.
- Bằng chứng: export có 68 annotation; reference có 20 object trên cùng ảnh. Trên toàn task, scorer ghi nhận 63 TP, 100 FP và 8 FN ở ngưỡng IoU 0,5; precision 0,39 và recall 0,89. Chênh lệch số lượng cho thấy cần kiểm tra các mask thừa, trùng hoặc tách nhầm; chưa thể kết luận nguyên nhân từng mask chỉ từ số đếm.
- Hành động đề xuất: mở ảnh trong CVAT, đối chiếu từng object và class, sửa mask thừa hoặc gộp/tách sai nếu xác nhận, sau đó Save và export lại. Chưa có bằng chứng thao tác sửa, Save hoặc export lại; cần học viên xác nhận sau khi thực hiện.
- Kết quả liên quan: mean matched IoU 0,773; metric chấm là 0,686. Precision không trực tiếp nằm trong công thức điểm Medium nên cần xem riêng FP.

## 4. Ba ca cần kiểm tra hoặc hỏi coach

| Ảnh/vị trí | Hai cách hiểu hoặc vấn đề | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi |
| --- | --- | --- | --- |
| Easy, vùng `sidewalk` trong ba ảnh | Ranh vỉa hè với `road` hoặc vùng khác có thể chưa khớp | IoU `sidewalk` 0,399; `road` 0,959; coverage 96,8% | Mở các ảnh và kiểm tra theo bó vỉa, chức năng sử dụng; chưa xác định được vị trí lỗi cụ thể từ metric tổng. |
| Medium, `000000181542.jpg` | Mask dư có thể là vật thật, mask trùng hoặc vật bị tách | 68 annotation so với 20 object reference trên ảnh | Kiểm từng instance và phần nhìn thấy; hỏi coach với object khó phân biệt. |
| Hard, hai ảnh của task, vùng `sidewalk` và các instance `person` | Phân lớp hoặc biên vùng vỉa hè; object người có thể bị thừa hoặc bỏ sót | PQ `sidewalk` 0,000 (0 TP, 2 FP, 2 FN); `person` 0,090 (3 TP, 31 FP, 6 FN) | Kiểm ranh, class và từng instance trong CVAT; chưa xác định được vị trí cụ thể hoặc nguyên nhân chỉ từ số liệu tổng. |
