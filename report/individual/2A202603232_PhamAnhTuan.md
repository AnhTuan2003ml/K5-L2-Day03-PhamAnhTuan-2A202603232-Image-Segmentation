# Báo cáo thực hành Day 3

- Họ và tên: Phạm Anh Tuấn
- Mã học viên: 2A202603232
- Ngày tự đánh giá: 12/11/2026

## 1. Bài đã nộp

Tôi nộp chín file ZIP dưới đây. Khi kiểm tra bằng `scripts/inspect_submissions.py`, các file đều đọc được và đúng cấu trúc yêu cầu. Số ảnh trong bảng là số ảnh có trong bản export; tôi chưa xác nhận số ảnh đã hoàn chỉnh và Save.

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

Tôi đối chiếu ba tier với reference `tiers_gt` được cung cấp, dùng `python scoring/scorecard.py --group tiers --dir submissions`. Kết quả tự đánh giá ngày 12/11/2026 như sau:

| Task | Metric | Kết quả | Điểm phản hồi |
| --- | --- | ---: | ---: |
| easy_semantic | mIoU | 0,791 | 17,4 / 20 |
| medium_instance | mean matched IoU × recall | 0,686 | 20,3 / 32 |
| hard_panoptic | PQ | 0,368 | 11,2 / 30 |
| **Tổng ba tier** | | | **48,9 / 82** |

Đây là điểm tự đánh giá so với reference, không phải điểm cuối. Không có reference riêng cho sáu checkpoint nên chưa tính 18 điểm còn lại. Kiểm tra cấu trúc không chứng minh đúng biên, số vật hay độ phủ; đặc biệt Hard cần kiểm tra chồng lấn và phủ vùng trong CVAT.

## 2. Một quyết định trước khi dùng gợi ý

Tôi chưa xác định được object đầu tiên đã tự vẽ ở Medium và chưa có thông tin để ghi lại việc sử dụng gợi ý. File export không lưu thứ tự vẽ, nên tôi chưa thể mô tả chính xác quyết định chọn biên của object đó.

## 3. Một lỗi tìm thấy và hướng xử lý

- Task: `medium_instance`, ảnh `000000181542.jpg`.
- Bằng chứng: export có 68 annotation; reference có 20 object trên cùng ảnh. Trên toàn task, scorer ghi nhận 63 TP, 100 FP và 8 FN ở ngưỡng IoU 0,5; precision 0,39 và recall 0,89. Chênh lệch số lượng cho thấy cần kiểm tra các mask thừa, trùng hoặc tách nhầm; chưa thể kết luận nguyên nhân từng mask chỉ từ số đếm.
- Hướng xử lý: tôi cần mở lại ảnh trong CVAT, đối chiếu từng object và class, sau đó sửa các mask thừa hoặc gộp/tách sai nếu xác nhận được. Chưa có thông tin xác nhận việc sửa, Save và export lại.
- Kết quả liên quan: mean matched IoU 0,773; metric chấm là 0,686. Precision không trực tiếp nằm trong công thức điểm Medium nên cần xem riêng FP.

## 4. Ba ca cần kiểm tra hoặc hỏi coach

| Ảnh/vị trí | Hai cách hiểu hoặc vấn đề | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi |
| --- | --- | --- | --- |
| Easy, vùng `sidewalk` trong ba ảnh | Ranh vỉa hè với `road` hoặc vùng khác có thể chưa khớp | IoU `sidewalk` 0,399; `road` 0,959; coverage 96,8% | Mở các ảnh và kiểm tra theo bó vỉa, chức năng sử dụng; chưa xác định được vị trí lỗi cụ thể từ metric tổng. |
| Medium, `000000181542.jpg` | Mask dư có thể là vật thật, mask trùng hoặc vật bị tách | 68 annotation so với 20 object reference trên ảnh | Kiểm từng instance và phần nhìn thấy; hỏi coach với object khó phân biệt. |
| Hard, hai ảnh của task, vùng `sidewalk` và các instance `person` | Phân lớp hoặc biên vùng vỉa hè; object người có thể bị thừa hoặc bỏ sót | PQ `sidewalk` 0,000 (0 TP, 2 FP, 2 FN); `person` 0,117 (3 TP, 21 FP, 6 FN) | Kiểm ranh, class và từng instance trong CVAT; chưa xác định được vị trí cụ thể hoặc nguyên nhân chỉ từ số liệu tổng. |
