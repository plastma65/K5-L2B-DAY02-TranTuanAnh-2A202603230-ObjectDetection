# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Trần Tuấn Anh
- MSSV: 2A202603230
- Email: 26ai.anhtt5@vinuni.edu.vn
- Lớp: K5 Level 2B
- Link repo bài làm: https://github.com/plastma65/K5-L2B-DAY02-TranTuanAnh-2A202603230-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/drive/1mSUJInM5ALzleRIsk5t9z3IT7DibvB36?usp=sharing

## 2. Schema và mini guideline

Chi tiết ở [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Tóm tắt các ca mơ hồ đã quyết định:

- `drive_006`: minivan xanh bạc hà có giá nóc, phân vân `car`/`van` → gán `van` (thân hộp cao, minivan chở người thuộc `van`).
- `drive_014` (cùng xe ở `drive_038`): xe cứu hộ có cần cẩu → gán `truck` (cabin riêng + thiết bị phía sau).
- `drive_002`: van bị cắt ở rìa phải → gán `van`, box chạm rìa ảnh.
- `drive_038`: van đỏ chồng lên xe tải nhỏ → hai box riêng `van` và `truck`.

Cần giảng viên làm rõ: bảng schema trong README và `data.yaml` ghi `1 truck, 2 bus, 3 van`, nhưng gold train và nhãn eval dùng `1 van, 2 truck, 3 bus`. Bộ nhãn nộp theo quy ước của gold/eval để khớp tập validation (xem mục 4).

## 3. Annotation và self-QC

- Số file nhãn train: 50 (`dataset/labels/train/`).
- Kết quả checker: `python3 tools/check_yolo_labels.py` → "ĐẠT cấu trúc: 50/50 file nhãn hợp lệ".
- Danh sách ảnh đã mở lại và rà bằng mắt trong CVAT (10 ảnh): `drive_002`, `003`, `008`, `012`, `022`, `024`, `026`, `027`, `038`, `041`.
- Lỗi phát hiện khi rà và cách sửa:

  **Bỏ xe nhỏ hoặc mơ hồ ở xa**

  | Ảnh | Thay đổi |
  |---|---|
  | `drive_012` | Bỏ 15 box qua 3 lượt: xe nhỏ ở hàng xa, xe bị cắt ở mép khung, xe bị che sau xe khác |
  | `drive_003` | Bỏ 14 box: xe nhỏ dưới cầu vượt, xe bị kẹp giữa hoặc che sau taxi |
  | `drive_002` | Bỏ 8 box: xe con nhỏ, 1 van và 1 bus ở xa |
  | `drive_008` | Bỏ 5 box: xe con nhỏ ở xa và 1 bus bị cắt ở mép trên |

  **Thêm xe bị sót**

  | Ảnh | Thay đổi |
  |---|---|
  | `drive_041` | Thêm 15 xe con trong hàng xe chờ ở cuối đường |
  | `drive_024` | Thêm 12 xe con và 1 van trắng; đổi 1 xe con màu tối từ van sang car |
  | `drive_027` | Thêm 9 xe con, có cả xe bị bus che một phần; đổi 1 box từ van sang car |
  | `drive_026` | Thêm 8 xe con và 1 bus đỏ sau bus vàng; đổi 1 xe cứu hộ từ van sang truck; đổi 1 SUV từ van sang car |

  **Đổi sang van**

  | Ảnh | Thay đổi |
  |---|---|
  | `drive_022` | 1 xe minivan màu bạc, đổi từ car sang van |
  | `drive_038` | 1 xe van xanh đậm, đổi từ truck sang van (box do model YOLOv7 tự động gán trong CVAT đã nhận nhầm là xe tải; mình kiểm tra lại và sửa) |

- Commit khóa nhãn ban đầu: `d9c807f` (trước khi rework).

## 4. Gold QC và rework

| Chỉ số | Trước sửa (`d9c807f`) | Sau sửa (`489361b`) |
|---|---:|---:|
| TP | 459 | 536 |
| FP | 658 | 160 |
| FN | 194 | 117 |
| Precision | 0.4109 | 0.7701 |
| Recall | 0.7029 | 0.8208 |
| F1 | 0.5186 | 0.7947 |
| mean_iou_of_matches | 0.8416 | 0.8548 |
| passed | false | false (F1 thiếu 0.005 so với ngưỡng 0.80) |

Kết quả cuối: [`outputs/annotation_evaluation.json`](../../outputs/annotation_evaluation.json).

Các vòng rework:

1. **Lệch quy ước ID lớp.** Bản đầu gán theo README (`1 truck, 2 bus, 3 van`); gold dùng `1 van, 2 truck, 3 bus` (kiểm tra trên `drive_008`: xe buýt nối = 3, xe ben = 2, xe van = 1; nhãn eval `drive_053`, `drive_058` cũng xe buýt = 3). Đổi ID cho khớp → F1 0.52 → 0.64.
2. **Xe rất nhỏ/mơ hồ.** Bản đầu có 1117 box so với 653 của gold, phần lớn box thừa là xe nhỏ ở xa. Bỏ box xe quá nhỏ theo guideline → F1 0.70.
3. **Rà lại từng ảnh** trong công cụ gán nhãn (ảnh lệch nhiều: `drive_002`, `003`, `012`, `024`, `027`, `041`…): bỏ xe mơ hồ, bổ sung xe cỡ vừa bị sót, sửa lớp van/car → F1 0.71 → 0.73 → 0.75 → 0.78 → 0.79.

Mean IoU luôn khoảng 0.85, cho thấy box bám sát tốt; vấn đề chính nằm ở **độ phủ** (gán xe nào, bỏ xe nào) và **quy ước lớp**, không phải độ chặt của box. Dừng ở F1 0.7947 vì các lần bỏ box tiếp theo bắt đầu làm mất cả box đúng (recall giảm).

## 5. Kết quả YOLO

- Runtime: Google Colab, GPU (`device: "0"`). Tesla T4 (14913MiB). Ultralytics 8.4.208, Python 3.13.16, torch 2.14.1+cu130
- Tham số: giữ nguyên notebook (`yolo26n.pt`, `epochs=50`, `imgsz=640`, `batch=-1`, `seed=20260909`).
- Best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt` (không commit `.pt`).

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.5172 |
| Recall | 0.4664 |
| mAP50 | 0.5472 |
| mAP50–95 | 0.3722 |

Kết quả: [ảnh dự đoán 10 ảnh eval](assets/eval_predictions/visuals/), [confusion matrix eval](assets/yolo26_runs/vehicle_eval/confusion_matrix.png), [log train](assets/yolo26_runs/vehicle_annotation/results.csv), [đồ thị train](assets/yolo26_runs/vehicle_annotation/results.png).

**Lưu ý đọc kết quả:** Colab đặt tên lớp theo `data.yaml` (`1 truck, 2 bus, 3 van`) trong khi nhãn train/eval thực tế là `1 van, 2 truck, 3 bus`. Vì vậy trên ảnh và confusion matrix: nhãn "truck" = van thật, "bus" = truck thật, "van" = bus thật.

**Trả lời bốn câu hỏi cuối notebook:**

1. **Lớp có recall thấp nhất:** van. Theo [confusion matrix](assets/yolo26_runs/vehicle_eval/confusion_matrix.png), 5 xe van thật trong eval chỉ 2 xe được nhận đúng, 3 xe bị dự đoán thành `car` (recall ≈ 0.40). Truck cũng thấp (1/2 xe), nhưng chỉ có 2 mẫu nên kém tin cậy hơn; `car` (86/94) và bus (10/12) cao. Ảnh minh họa: [`drive_056`](assets/eval_predictions/visuals/drive_056.jpg) — xe van đỏ ở tiền cảnh bị dự đoán `car 0.81`. Ngoài ra ở [`drive_052`](assets/eval_predictions/visuals/drive_052.jpg) một xe buýt lớn bị dự đoán thành `car 0.60`.
2. **mAP50 cao hơn mAP50–95 (0.547 so với 0.372):** model tìm đúng vị trí xe ở mức IoU 0.5 nhưng box chưa khớp chặt khi yêu cầu IoU cao. Cần kiểm tra mép box (lẹm thân xe hoặc lấn bóng đổ), xe bị che/chồng nhau và xe nhỏ ở xa — nơi lệch vài pixel đã làm IoU giảm mạnh. Tập train chỉ 50 ảnh cũng khiến box regression chưa ổn định.
3. **Lỗi lớp liên quan guideline:** nhầm van ↔ car và bus → car. Ranh giới van/car (xe hộp cao, minivan) chính là ca mơ hồ trong guideline (`drive_006`). Dữ kiện: 3/5 van bị đoán thành `car`; trong lúc rework, lớp van cũng là lớp bản thân thay đổi nhiều nhất (59 → 29 → 64 box qua các vòng), cho thấy định nghĩa van chưa đủ nhất quán. Thêm vào đó, lệch quy ước ID giữa README và gold khiến tên lớp hiển thị bị đảo. Mất cân bằng lớp (nhãn train cuối: 533 car, 57 van, 23 truck, 83 bus) cũng góp phần.
4. **Không dùng 10 ảnh eval để train:** chúng là tập validation để chọn `best.pt` và đo khả năng tổng quát trên ảnh model chưa thấy. Nếu đưa vào train, chỉ số sẽ cao giả tạo (model đã thấy đáp án) và không còn phản ánh chất lượng thật. Vì eval đồng thời được dùng chọn checkpoint nên nó cũng không phải test set độc lập hoàn toàn.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Chấm gold lần 1 | F1 0.52, toàn bộ box van/bus/truck bị tính sai | Vẽ nhãn gold và nhãn của mình lên `drive_008`, đối chiếu nhãn eval | Phát hiện lệch quy ước ID; đổi theo gold/eval |
| Rework | Quá nhiều box thừa ở xe nhỏ ở xa | Áp tiêu chí bỏ xe quá nhỏ/mơ hồ, rà lại ảnh lệch nhiều | F1 0.70 → 0.79 |
| Rework vòng cuối | Bỏ thêm box làm mất cả box đúng | Dừng ở bản tốt nhất | F1 0.7947, thiếu 0.005 so với ngưỡng |
| Colab | Tên lớp hiển thị theo `data.yaml`, lệch với nhãn thật | Ghi chú quy đổi khi phân tích | Đã giải thích ở mục 5 |

## 7. Tự đánh giá theo rubric

- Hoàn thành và định dạng YOLO: đạt (50 file, checker đạt).
- Đầy đủ vật thể / độ bám box: mean IoU 0.855; recall 0.82 — còn sót một số xe ở ảnh đông.
- Phân lớp nhất quán: còn nhầm van/car; có lệch quy ước ID ban đầu, đã sửa.
- Mini guideline: có định nghĩa lớp, quy tắc box, 4 ca mơ hồ.
- Gold-set QC: F1 0.7947 (chưa đạt 0.80), mean IoU đạt; có nhiều vòng rework được giải thích ở mục 4.
- Pipeline YOLO: Colab chạy được, giữ nguyên split, có metrics và ảnh eval.

## 8. Công cụ AI và tài liệu tham khảo

- Claude (Anthropic): hỗ trợ đọc tài liệu đề bài, chạy `check_yolo_labels.py` và `evaluate_annotations.py`, viết script đổi ID lớp, tổng hợp số liệu theo ảnh và soạn nháp báo cáo này. Box và quyết định gán nhãn do mình tự làm trong công cụ annotation; nội dung báo cáo đã được mình đọc lại và chỉnh sửa.
- Công cụ gán nhãn: CVAT online (app.cvat.ai), export định dạng Ultralytics YOLO Detection. Ảnh `drive_001`–`drive_037` mình tự gán tay. Từ `drive_038` đến `drive_050`, mình dùng tính năng tự động gán nhãn bằng model YOLOv7 có sẵn trong CVAT, xen kẽ với tự gán thêm xe bị sót, sửa lớp/vị trí box và xóa box sai; mọi box tự động đều được rà lại bằng mắt.
- Không dùng nguồn dữ liệu ngoài repo.
