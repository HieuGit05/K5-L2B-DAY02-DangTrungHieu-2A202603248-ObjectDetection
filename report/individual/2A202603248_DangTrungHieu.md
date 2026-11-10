# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Đặng Trung Hiếu
- MSSV: 2A202603248
- Email: ladykiller12.2005@gmail.com
- Lớp: L2B
- Link repo bài làm: https://github.com/HieuGit05/K5-L2B-DAY02-DangTrungHieu-2A202603248-ObjectDetection
- Link Colab đã chạy: https://colab.research.google.com/drive/1OikW1Csp77DNSdP2E5oLisxAvcrx62cW#scrollTo=M3MS_eJ8Uqx4

## 2. Schema và mini guideline

Chi tiết định nghĩa lớp và quy tắc box nằm trong [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Schema: `car=0, truck=1, bus=2, van=3`.

Ba ca mơ hồ đã quyết định:

| Ảnh | Tình huống | Quyết định | Lý do theo schema |
|---|---|---|---|
| `drive_029` | Xe buýt bị mất một phần ở đầu xe | Gán `bus` | Nóc cao hơn `car`/`van`, nhiều cửa sổ |
| `drive_013` | Xe con bị hàng cây che | Gán `car` | Kích thước nhỏ, chở được 4 người |
| `drive_023` | Chỉ thấy phần thân một bên của xe | Gán `bus` | Xe lớn, nhiều cửa sổ |

Cách áp dụng schema: phân biệt lớp theo kiểu thân xe và chức năng (xe buýt chở nhiều hành khách, nhiều cửa sổ; `van` thân hộp, nóc cao, nhỏ hơn xe buýt; `truck` có cabin và thùng hàng riêng). Box bám phần nhìn thấy được, không đoán phần bị che.

Phần cần giảng viên làm rõ: nhãn gold và nhãn eval dường như dùng ID lớp khác với cách đặt tên trong schema (xe buýt trong ảnh có ID `van`). Dữ kiện ở mục 4 và mục 5. 

## 3. Annotation và self-QC

- Số file nhãn train: 50 (đủ, cùng basename với 50 ảnh train; cell kiểm tra trong notebook in `OK: 50 train + 10 eval`).
- Kết quả checker: `python3 tools/check_yolo_labels.py` cho kết quả `ĐẠT cấu trúc: 50/50 file nhãn hợp lệ`.
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: `drive_013`, `drive_023`, `drive_029` (ba ca mơ hồ trong mini guideline), `drive_009`, `drive_015`, `drive_018`, `drive_024`, `drive_026`, `drive_027`, `drive_041`.
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: khi đối chiếu với kết quả chấm gold, các ảnh này có nhiều chênh lệch nhất:
  - `drive_041`, `drive_027`, `drive_024`, `drive_009`, `drive_026`: nhiều gold box không có box nào của tôi trùng vị trí (lần lượt 19, 18, 17, 16 và 11 FN), chủ yếu là xe nhỏ ở xa hoặc xe trong cụm xe dày đặc.
  - `drive_018`: gold có 2 box, tôi gán 7 box, không box nào khớp (0 match).
  - `drive_015`: gold có 12 box, tôi chỉ gán 6 box (4 box khớp), tức bỏ sót khoảng một nửa số xe.
  - `drive_029`, `drive_041`: xe buýt lớn trùng vị trí với gold nhưng khác ID lớp (tôi gán `bus`, gold ghi `van`), xem mục 4.
  - Cách sửa: chưa sửa nhãn (chưa rework), vì cần giảng viên xác nhận khác biệt ID lớp trước; các lỗi bỏ sót xe nhỏ sẽ sửa nếu rework.
- Commit khóa nhãn ban đầu: commit `57131a7` ("Add files via upload", 10/11/2026 00:13 +0700) là commit đầu tiên đưa `dataset/labels/train/` lên GitHub; các commit trước đó chỉ sửa tài liệu và `GUIDELINE_MINI.md`. 

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 392 | Chưa rework |
| FP | 315 | Chưa rework |
| FN | 261 | Chưa rework |
| Precision | 0.5545 | Chưa rework |
| Recall | 0.6003 | Chưa rework |
| F1 | 0.5765 | Chưa rework |
| mean_iou_of_matches | 0.839 | Chưa rework |
| passed | false | Chưa rework |

Kết quả nằm trong `outputs/annotation_evaluation.json` (ngưỡng IoU 0.50, yêu cầu F1 ≥ 0.80 và mean IoU ≥ 0.70). Bộ nhãn trên GitHub cho cùng kết quả khi chạy lại `tools/evaluate_annotations.py`, nên chưa có thay đổi nhãn nào sau lần chấm này. Mean IoU đạt yêu cầu; F1 chưa đạt.

Phân tích (số liệu tổng hợp từ lần chấm lại, không trích nhãn gold):

- Chỉ lớp `car` có TP (392). Số nhãn gold: car 533, van 54, truck 46, bus 20. Số nhãn của tôi: car 551, bus 74, van 61, truck 21. Recall của `car` ≈ 0.735, precision ≈ 0.711. Ba lớp còn lại có TP = 0.
- Trong 261 FN, có 121 gold box trùng vị trí (IoU ≥ 0.5) với box của tôi nhưng khác lớp; 140 FN còn lại không có box nào của tôi trùng vị trí. Trong 315 FP, có 120 box trùng vị trí với một gold box khác lớp; 195 FP còn lại không trùng gold box nào.
- Các cặp khác lớp (gold → nhãn của tôi): van → bus 50, truck → van 32, bus → truck 20, car → van 10, truck → car 7, truck → bus 2. Ba cặp lớn nhất tạo thành một hoán vị vòng: ID tôi gán cho xe buýt tương ứng với ID `van` của gold, ID tôi gán cho `van` tương ứng với `truck` của gold, ID tôi gán cho `truck` tương ứng với `bus` của gold.
- Đã mở lại hai ảnh `drive_041` và `drive_029` để đối chiếu: các xe buýt lớn có box trùng vị trí giữa nhãn của tôi và gold, nhưng gold gán ID `van` còn tôi gán `bus`. Vì vậy phần lớn lỗi lớp là khác quy ước ID/lớp, chưa chứng minh được là tôi nhận diện sai loại xe.
- FN/FP còn lại tập trung ở vật thể nhỏ (diện tích box trung vị khoảng 0.8% ảnh với FN và 0.57% với FP), phù hợp với khó khăn khi gán xe ở xa. Chỉ từ FP/FN chưa kết luận được nguyên nhân từng ca (bỏ sót, box lỏng, hay gold khác quy ước); 
- Chẩn đoán tham khảo, không phải kết quả chính thức: nếu áp dụng phép hoán vị ID nói trên khi chấm, F1 ≈ 0.725 và mean IoU ≈ 0.846, vẫn dưới 0.80. Phần còn lại do box nhỏ bị bỏ sót hoặc thừa.

Rework: chưa thực hiện. Theo quy định, không sửa gold và không tự đổi schema; cần báo giảng viên về khác biệt ID lớp trước khi sửa nhãn. 

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản: Google Colab, GPU Tesla T4 (14 913 MiB), Ultralytics 8.4.208, Torch 2.14.1+cu130, Python 3.13.16. Train 50 epoch hết khoảng 0.021 giờ.
- Tham số thay đổi so với notebook: không đổi tham số train (`yolo26n.pt`, `epochs=50`, `imgsz=640`, `batch=-1`, `seed=20260909`). Chỉ điền `REPO_URL` và thêm một cell mount Google Drive.
- Best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`. Log cho thấy mAP50-95 tốt nhất (0.200) ở epoch 1, các epoch cuối chỉ khoảng 0.158.
- Nhãn dùng để train là bộ nhãn trên GitHub chưa qua rework (chưa đạt gate gold F1 ở mục 4).

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.4091 |
| Recall | 0.2339 |
| mAP50 | 0.2707 |
| mAP50–95 | 0.1996 |

Theo lớp (113 đối tượng):

| Lớp | Số đối tượng | P | R | mAP50 | mAP50-95 |
|---|---:|---:|---:|---:|---:|
| car | 94 | 0.836 | 0.652 | 0.740 | 0.524 |
| truck | 5 | 0.663 | 0.200 | 0.240 | 0.206 |
| bus | 2 | 0 | 0 | 0.014 | 0.005 |
| van | 12 | 0.136 | 0.083 | 0.089 | 0.064 |

Ảnh và chỉ số đánh giá (giải nén từ `day2_colab_outputs.zip`):

- Ảnh eval kèm box dự đoán: [drive_051](assets/eval_predictions/visuals/drive_051.jpg), [drive_052](assets/eval_predictions/visuals/drive_052.jpg), [drive_053](assets/eval_predictions/visuals/drive_053.jpg), [drive_058](assets/eval_predictions/visuals/drive_058.jpg)
- Chỉ số: `assets/yolo26_runs/vehicle_eval/` (confusion matrix, PR curve), `assets/yolo26_runs/vehicle_annotation/results.csv`

Trả lời bốn câu hỏi cuối notebook:

**1. Lớp nào recall thấp nhất? Ảnh nào minh họa?**
`bus` có recall thấp nhất (R = 0, P = 0), tiếp theo là `van` (R ≈ 0.083) và `truck` (R = 0.2). Ảnh minh họa: `drive_051`, nhãn eval có một `bus` nhỏ ở phía trên bên phải (khoảng 70×129 px, 2.2% diện tích), ban đêm và bị cột điện che một phần; model dự đoán ảnh này là 8 `car` và 1 `van`, không có `bus`. Lưu ý `bus` chỉ có 2 đối tượng và `truck` chỉ có 5 trong eval, nên con số rất nhạy với từng đối tượng.

**2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?**
Cần kiểm xem box có khít với vật thể không: lệch tâm, sai kích thước, hoặc quy ước vẽ box không nhất quán (box sát phần nhìn thấy hay bao cả phần bị che). Vật thể nhỏ đặc biệt nhạy, vì lệch vài pixel đã làm IoU giảm mạnh; box `car` trong train có diện tích trung vị khoảng 0.59% ảnh (~49×49 px ở 640×640). Trong kết quả này chỉ `car` có dạng đó (mAP50 0.740 → mAP50-95 0.524); ba lớp còn lại mAP50 vốn đã thấp nên vấn đề chính là phát hiện và phân loại, không phải độ khít box. Mean IoU của các box khớp khi chấm nhãn là 0.839, nên chưa có dấu hiệu box lỏng hệ thống ở nhãn train.

**3. Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ?**
Lỗi `bus`/`van`/`truck` liên quan tới quy ước lớp. Dữ kiện: (a) trong eval, các xe buýt lớn có nhãn `van`: `drive_052` (13.6% diện tích), `drive_053` (hai xe buýt) và `drive_058` (xe buýt vàng và các xe buýt cùng làn), trong khi nhãn `bus` của eval chỉ nằm ở hai xe nhỏ ở xa (`drive_051`, `drive_058`); (b) model dự đoán 8 box `bus` (052×1, 053×3, 056×1, 058×2, 060×1) nên precision `bus` = 0 và recall `van` thấp; (c) mini guideline của tôi định nghĩa `bus` là xe nhiều hành khách, nhiều cửa sổ và train có 74 nhãn `bus`, nên model học theo quy ước đó; (d) khi chấm nhãn train, mẫu hình khác lớp tương tự xuất hiện (mục 4). Đây là khác biệt quy ước giữa nhãn của tôi và nhãn gold/eval, cần giảng viên xác nhận; tôi không sửa nhãn eval. Ngoài ra, cả 5 nhãn `truck` trong eval đều nhỏ (0.1–1.0% diện tích) và train chỉ có 21 nhãn `truck`, nên model hầu như không học được lớp này; `drive_051` còn có một xe sedan đỏ gần camera bị dự đoán `van 0.99` trong khi nhãn eval không có `van`.

**4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation?**
Nếu train trên eval, model có thể ghi nhớ ảnh và nhãn (dễ xảy ra với 50 ảnh), khiến mAP không còn phản ánh khả năng tổng quát hóa; đồng thời quy định bài cấm đưa eval vào train. Trong notebook, 10 ảnh eval đóng vai trò validation: được đánh giá sau mỗi epoch và dùng để chọn `best.pt`, nên không phải test set độc lập và con số hơi lạc quan. Với 10 ảnh, các chỉ số chỉ dùng để minh họa vòng lặp dữ liệu, không dùng làm benchmark.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Gán nhãn 50 ảnh | Khó quyết định xe chồng lấn nhưng vẫn phân biệt được, và xe ở xa hoặc mờ | Áp dụng quy tắc trong mini guideline (chỉ gán khi xác định tương đối chắc chắn; box theo phần nhìn thấy) | Còn nhiều FN/FP ở box nhỏ (mục 4). |
| Gold QC | F1 = 0.5765 (chưa đạt); `bus`/`van`/`truck` có TP = 0 | Chạy lại evaluator và so sánh trực quan `drive_041`, `drive_029` | Phát hiện khác biệt ID lớp so với gold; chưa rework, cần giảng viên xác nhận |
| Train/eval YOLO | `bus` P = R = 0; `best.pt` ở epoch 1 | Đối chiếu dự đoán với nhãn eval (mục 5) | Giữ nguyên split eval; không sửa nhãn eval |

## 7. Tự đánh giá theo rubric

Đối chiếu [RUBRIC.md](../../RUBRIC.md); đây là nhận xét theo bằng chứng, không tự chấm điểm:

- Hoàn thành và định dạng YOLO: có 50 file, checker đạt 50/50.
- Đầy đủ vật thể: chưa đầy đủ ở vật thể nhỏ/xa; recall chung 0.6003 và 140 gold box không có box nào của tôi trùng vị trí.
- Độ bám sát box: mean IoU của các box khớp 0.839 (≥ 0.70); còn 195 box thừa không khớp gold.
- Phân lớp nhất quán: so với gold chỉ `car` có TP; nguyên nhân chính là khác quy ước ID lớp (mục 4), chưa được giảng viên xác nhận.
- Mini guideline: có định nghĩa bốn lớp, quy tắc box và ba ca mơ hồ có lý do.
- Gold QC: chưa đạt (F1 0.5765 < 0.80; mean IoU đạt); chưa có vòng rework.
- Pipeline YOLO và phân tích: Colab đã chạy, không dùng eval để train, có metrics và trả lời bốn câu hỏi.
- Chưa kiểm được: danh sách 10 ảnh rà bằng mắt, phản hồi của giảng viên về ID lớp.

## 8. Công cụ AI và tài liệu tham khảo

- Công cụ AI: Claude (Anthropic). Dùng để giải thích các khái niệm, tính thống kê lỗi tổng hợp.
- Nguồn trong repo: README, GUIDE, RULES, RUBRIC, SUBMISSION, CHECKPOINTS của bài.

