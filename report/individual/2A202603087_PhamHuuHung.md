# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Phạm Hữu Hùng
- MSSV: 2A202603087
- Email: hwngt1412@gmail.com
- Lớp: E402
- Link repo bài làm: [https://github.com/hungtun/K5-L2A-DAY02-PhamHuuHung-2A202603087-ObjectDetection](https://github.com/hungtun/K5-L2A-DAY02-PhamHuuHung-2A202603087-ObjectDetection)
- Link Colab đã chạy: [https://colab.research.google.com/drive/1_9wHq5catOnv2n6PUl5Zx_SUq1q7oYZx?usp=sharing](https://colab.research.google.com/drive/1_9wHq5catOnv2n6PUl5Zx_SUq1q7oYZx?usp=sharing)

## 2. Schema và mini guideline

Chi tiết định nghĩa lớp và quy tắc gán nhãn xem tại [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md).

- **Cách áp dụng schema 4 lớp (`car`, `truck`, `bus`, `van`)**: Phân lớp theo loại phương tiện vật lý (không phụ thuộc trạng thái dừng/đỗ hay đang di chuyển). Mỗi xe hợp lệ được vẽ một bounding box 2D riêng ôm sát phần thân xe, bánh xe và khối hàng hóa gắn cố định đang nhìn thấy; không lấy dư mặt đường hoặc bóng đổ. Với xe bị che khuất một phần hoặc bị cắt bởi rìa ảnh, chỉ giữ nhãn khi phần lộ ra đủ nhận diện chắc chắn lớp và kéo cạnh box bám đúng phần nhìn thấy / kẹp sát mép khung hình. Loại trừ toàn bộ người đi bộ, xe đạp, xe máy, xe ba gác, biển báo và các đốm xe quá nhỏ/nhòe sát đường chân trời.
- **Tóm tắt các ca mơ hồ đã quyết định trong [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md)**:
  1. **`drive_002`** *(đoàn xe rất nhỏ, nhòe sát chân trời gần cột đèn giao thông)*: Quyết định **không gán** đám đốm xe li ti bị dính chùm ở quá xa; chỉ gán 19 xe ở tiền cảnh và trung cảnh đủ rõ hình khối để tránh tự đoán lớp giữa `car` và `van`.
  2. **`drive_008`** *(ngã tư đông xe có xe buýt khớp nối che nửa thân các ô tô phía sau, microvan xen lẫn SUV, xe máy và mẩu nhỏ đầu xe ở góc dưới phải)*: Gán riêng từng box bám phần lộ ra của các `car` bị xe buýt che, phân tách microvan thân hộp (`van`) khỏi SUV có ca-pô (`car`), bỏ qua xe máy (ngoài schema) và mẩu xe bị cắt quá nhỏ ở góc dưới phải.
  3. **`drive_034`** *(xe cứu hộ giao thông có cần kéo đi cạnh xe ba gác có thùng/mái che dưới trời mưa)*: Gán `truck` cho xe cứu hộ giao thông (đóng trên khung gầm tải, cabin tách rời sàn/cần kéo) và **không gán** toàn bộ xe ba gác có mái che, xe đạp thồ, xe máy.
  4. **`drive_042` & `drive_047`** *(xe tải chở bao hàng cao quá nóc che xe buýt 2 tầng, xe buýt/minivan bị rìa ảnh cắt)*: Box `truck` bao trọn cả phần bao hàng cố định trên thùng; các xe bị cắt ở mép dưới (`drive_042`) hoặc mép trái (`drive_047`) vẫn lộ rõ đặc trưng lớp nên giữ nhãn và kẹp cạnh chạm sát biên ảnh.
- **Phần cần giảng viên làm rõ thêm**:
  1. Quy ước đối với **dãy ô tô đỗ dọc lề đường/làn phụ tách biệt** (như hàng xe đỗ lề trái ở `drive_007`, `drive_018`, `drive_050`, `drive_052`): theo hướng dẫn *"Xe đang đỗ vẫn gán lớp theo loại xe"* thì cần gán, nhưng trong nhãn chuẩn gốc UA-DETRAC các vùng lề đường này thuộc vùng loại trừ (`ignore region`).
  2. Ngưỡng kích thước tối thiểu cụ thể (theo pixel hoặc tỷ lệ ảnh) cho đoàn xe nối đuôi ở làn xa (`drive_002`, `drive_005`, `drive_041`) và quy ước ranh giới giữa MPV/minivan cỡ nhỏ với `car`/SUV.

## 3. Annotation và self-QC

- Số file nhãn train: `50` file `.txt` trong `dataset/labels/train/` (`drive_001.txt` đến `drive_050.txt`), tổng cộng `716` bounding boxes.
- Kết quả checker và lỗi đã xử lý: Chạy `python3 tools/check_yolo_labels.py` đạt **`ĐẠT cấu trúc: 50/50 file nhãn hợp lệ`** (khớp 100% basename với 50 ảnh train, đúng 5 cột `<class_id> <x_center> <y_center> <width> <height>`, tọa độ chuẩn hóa trong `[0, 1]`, `width/height > 0`, các box sát mép đã được kẹp biên nên không có lỗi vượt khung ảnh).
- Danh sách ít nhất 10 ảnh đã rà bằng mắt: `drive_001`, `drive_002`, `drive_007`, `drive_008`, `drive_013`, `drive_018`, `drive_034`, `drive_042`, `drive_047`, `drive_050`.
- Lỗi bỏ sót/box/class phát hiện khi rà và cách sửa: Kiểm tra độ khít 4 cạnh để loại bỏ phần bóng đổ dưới gầm xe (`drive_013`, `drive_034`); kiểm tra tách rời từng box độc lập khi hai xe chồng lấp (`drive_008`, `drive_042`); kẹp sát biên `0.0` / `1.0` cho các xe bị cắt khung mà vẫn rõ loại xe (`drive_042`, `drive_047`); rà loại bỏ phương tiện 2–3 bánh có mái che dễ nhầm với xe nhỏ (`drive_034`) và lọc các đốm xe quá mờ ở chân trời (`drive_002`).
- Commit khóa nhãn ban đầu: `837f9a7` (`837f9a77fb829aa5d12d597f43e0a7305142e99a` — *"add label train"*).

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 470 | Không rework |
| FP | 246 | Không rework |
| FN | 183 | Không rework |
| Precision | 0.6564 | Không rework |
| Recall | 0.7198 | Không rework |
| F1 | 0.6866 | Không rework |
| mean_iou_of_matches | 0.8717 | Không rework |
| passed | false | Không rework |

File kết quả chấm chi tiết trong repo: [outputs/annotation_evaluation.json](../../outputs/annotation_evaluation.json).

- **Tình trạng rework**: **Chưa thực hiện vòng rework** sau khi nhận `gold_train_labels/` — giữ nguyên bộ nhãn đã khóa ở commit `837f9a7` để bảo toàn lịch sử đối chiếu trung thực và khớp chính xác với phiên huấn luyện trên Google Colab.
- **Phân tích nguyên nhân thực tế khi mở ảnh đối chiếu với nhãn gold** (không suy diễn chỉ từ con số FP/FN):
  1. **Chất lượng hình học box rất sát (`mean_iou_of_matches = 0.8717`)**: Vượt xa ngưỡng yêu cầu `0.70` và đạt mức **Xuất sắc** (`>= 0.80` theo rubric), cho thấy khi hai bên cùng gán một phương tiện và thống nhất lớp thì khung bao của học viên bám rất khít thân xe.
  2. **Nguyên nhân sinh `FP = 246`**: Mở trực tiếp các ảnh có chênh lệch lớn như `drive_007` (`student=14, gold=13, matches=6`), `drive_018` (`student=10, gold=2, matches=1`), `drive_050` (`student=30, gold=18, matches=13`) cho thấy học viên đã gán đầy đủ dãy ô tô đỗ dọc lề đường bên trái / làn phụ (ví dụ 6 xe đỗ ở `drive_007`, 8 xe đỗ ở `drive_018`), trong khi bộ `gold_train_labels` không gán các vùng đỗ xe ven đường này (tạo ra `137` box `car` không có box tương ứng trong gold). Bên cạnh đó có `47` box khớp rất chặt vị trí với gold (`IoU` từ `0.73` đến `0.94`, tiêu biểu 35 ca giữa lớp ID `1` và ID `0` tại `drive_007`, `drive_016`, `drive_026`, `drive_047`, `drive_050`), nhưng do `evaluate_annotations.py` chỉ ghép cặp cùng `class_id` nên mỗi ca lệch lớp bị tính đồng thời **1 FP và 1 FN**.
  3. **Nguyên nhân sinh `FN = 183`**: Mở các ảnh `drive_005` (`gold=24, student=16`), `drive_026` (`gold=39, student=28`), `drive_041` (`gold=24, student=12`) cho thấy ngoài 47 ca lệch mã lớp nêu trên, phần FN còn lại tập trung ở hàng xe rất nhỏ phía xa gần đỉnh ảnh (`y ≈ 0.04–0.16`, kích thước chỉ khoảng `2%–4%` chiều ngang ảnh) mà học viên chủ động bỏ qua theo quy tắc không đoán vật thể quá nhỏ/mờ trong mini guideline.

## 5. Kết quả YOLO

- Runtime, GPU/CPU, phiên bản Ultralytics và Torch: Google Colab GPU **Tesla T4 (`CUDA:0`, 14913 MiB)**, Python `3.13.16`, **Ultralytics `8.4.208`**, **PyTorch `2.14.1+cu130`**.
- Các tham số thay đổi so với notebook nếu có và lý do: **Không thay đổi tham số** — giữ nguyên toàn bộ cấu hình gốc của notebook (`model='yolo26n.pt'`, `epochs=50`, `imgsz=640`, `batch=-1`, `seed=20260909`, `conf=0.25` khi xuất ảnh dự đoán, giữ nguyên `data.yaml` và split `50 train / 10 eval`, chỉ điền `REPO_URL` trỏ tới repo cá nhân ở commit `837f9a7`).
- Đường dẫn best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.5093 |
| Recall | 0.5286 |
| mAP50 | 0.4853 |
| mAP50–95 | 0.3378 |

*(Chi tiết theo từng lớp từ phiên `best_model.val` trên 10 ảnh `eval` — tổng 113 instances: `car` [94]: P=`0.645`, R=`0.810`, mAP50=`0.772`, mAP50–95=`0.512`; `truck` [5]: P=`0.208`, R=`0.400`, mAP50=`0.314`, mAP50–95=`0.259`; `bus` [2]: P=`0.693`, R=`0.500`, mAP50=`0.495`, mAP50–95=`0.396`; `van` [12]: P=`0.492`, R=`0.404`, mAP50=`0.361`, mAP50–95=`0.184`.)*

- **Đường dẫn kết quả đã giải nén cạnh báo cáo**:
  - Thư mục 10 ảnh `eval` kèm box dự đoán: [assets/eval_predictions/visuals/](assets/eval_predictions/visuals/) (gồm [drive_051.jpg](assets/eval_predictions/visuals/drive_051.jpg), [drive_052.jpg](assets/eval_predictions/visuals/drive_052.jpg), [drive_053.jpg](assets/eval_predictions/visuals/drive_053.jpg), [drive_054.jpg](assets/eval_predictions/visuals/drive_054.jpg), [drive_055.jpg](assets/eval_predictions/visuals/drive_055.jpg), [drive_056.jpg](assets/eval_predictions/visuals/drive_056.jpg), [drive_057.jpg](assets/eval_predictions/visuals/drive_057.jpg), [drive_058.jpg](assets/eval_predictions/visuals/drive_058.jpg), [drive_059.jpg](assets/eval_predictions/visuals/drive_059.jpg), [drive_060.jpg](assets/eval_predictions/visuals/drive_060.jpg)).
  - Log huấn luyện theo epoch và chỉ số đánh giá model: [assets/yolo26_runs/vehicle_annotation/results.csv](assets/yolo26_runs/vehicle_annotation/results.csv) và thư mục [assets/yolo26_runs/vehicle_eval/](assets/yolo26_runs/vehicle_eval/) (ma trận nhầm lẫn [confusion_matrix.png](assets/yolo26_runs/vehicle_eval/confusion_matrix.png), đường cong PR [BoxPR_curve.png](assets/yolo26_runs/vehicle_eval/BoxPR_curve.png)).
- **Nhận xét trên ảnh và vật thể cụ thể**:
  - Trên ảnh [drive_052.jpg](assets/eval_predictions/visuals/drive_052.jpg): Nhãn chuẩn `eval` chỉ có `8` vật thể trên trục đường chính (`6 car`, `1 truck` nhỏ ở rất xa góc trên phải tọa độ `(0.73, 0.04)`, `1 van` ở trung tâm `(0.43, 0.53)`), nhưng model dự đoán `17 cars` (và `0 truck`, `0 van`). Lý do trực tiếp là model học từ nhãn train của học viên nên phát hiện thêm cả hàng ô tô con đang đỗ dọc vỉa hè/làn phụ bên trái phía sau trạm chờ xe buýt, đồng thời bỏ sót chiếc xe nhỏ li ti ở cuối làn đường bên phải.
  - Trên ảnh [drive_056.jpg](assets/eval_predictions/visuals/drive_056.jpg): Model dự đoán `24 cars, 3 trucks` so với nhãn `eval` (`15 car, 1 truck, 1 van`) — bắt tốt toàn bộ sedan đang lưu thông trên mặt đường chính nhưng tiếp tục bắt thêm dãy xe đỗ bên lề trái và dự đoán nhầm xe gầm cao thân vuông (như chiếc Jeep màu đỏ sẫm ở tiền cảnh góc dưới phải) sang `truck`.

Trả lời bốn câu hỏi cuối notebook:

1. **Lớp nào recall thấp nhất? Ảnh nào minh họa?**
   - Lớp có Recall thấp nhất trên tập `eval` là **`truck`** với **Recall = `0.400`** (chỉ có 5 thực thể trên 5 ảnh; đứng sát ngay sau là `van` với **Recall = `0.404`** trên 12 thực thể, thấp hơn nhiều so với `car` đạt **Recall = `0.810`** trên 94 thực thể).
   - Ảnh minh họa cụ thể: [drive_052.jpg](assets/eval_predictions/visuals/drive_052.jpg) (nhãn `eval` có 1 vật thể lớp `truck` ở rất xa phía trên làn phải `(0.73, 0.04)` kích thước `0.03 × 0.05` nhưng ở ngưỡng `conf=0.25` model chỉ dự đoán `17 cars`, bỏ sót hoàn toàn) và [drive_053.jpg](assets/eval_predictions/visuals/drive_053.jpg) (nhãn `eval` có 1 `truck` tại `(0.28, 0.40)` ở làn bên trái nhưng model chỉ dự đoán `10 cars, 1 bus, 1 van`). Nguyên nhân đến từ sự mất cân bằng lớp rất mạnh trong dữ liệu huấn luyện (50 ảnh train có `509` box lớp `0` nhưng rất ít mẫu cho các lớp còn lại) kết hợp kích thước vật thể ở xa quá nhỏ.

2. **Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?**
   - Khoảng cách lớn giữa `mAP50` (`0.4853`) và `mAP50–95` (`0.3378`) — đặc biệt ở lớp `van` giảm từ `0.361` xuống `0.184` và `car` giảm từ `0.772` xuống `0.512` — phản ánh việc model định vị đúng vùng có xe (vượt ngưỡng dễ `IoU >= 0.50`) nhưng sai lệch biên đáng kể ở các ngưỡng khắt khe `IoU = 0.75–0.95`.
   - Khi kiểm tra lại nhãn và dự đoán cần rà soát: (1) **độ ôm sát (tightness)** của 4 cạnh box — có lấy thừa mặt đường/bóng đổ dưới gầm hoặc cắt hụt phần mũi/đuôi xe không; (2) **quy tắc vẽ phần nhìn thấy khi xe bị che khuất hoặc cắt khung** — có sự không đồng nhất giữa việc chỉ bao phần lộ ra so với ước lượng cả phần bị che hay phần hàng hóa chất trên xe không; (3) **độ nhạy IoU trên các xe nhỏ ở xa** (với box rộng ~15–25 px, lệch chỉ vài pixel đã làm IoU rớt mạnh dưới 0.70).

3. **Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ nhận xét?**
   - **Nhầm lẫn ranh giới giữa `car` và `truck`/`van` (SUV, MPV/minivan, xe bán tải)**: Dữ kiện từ Gold QC trên 50 ảnh train cho thấy có **35 trường hợp box khớp vị trí rất cao (`IoU = 0.76–0.94`)** nhưng học viên gán ID `1` trong khi gold gán ID `0` (`car`), ví dụ tại `drive_007`, `drive_016`, `drive_026`, `drive_047`. Hệ quả lan sang model: trên tập `eval`, lớp `truck` (ID `1`) chỉ đạt **Precision = `0.208`** (dự đoán thừa `3 trucks` trên [drive_056.jpg](assets/eval_predictions/visuals/drive_056.jpg) khi nhãn chỉ có 1, và dự đoán `3 trucks` trên [drive_058.jpg](assets/eval_predictions/visuals/drive_058.jpg) khi nhãn có 0).
   - **Khác biệt quy ước gán xe đỗ lề đường giữa guideline và nhãn chuẩn**: Thực hiện theo `GUIDE.md` (*"Xe đang đỗ vẫn gán lớp theo loại xe"*), học viên đã gán nhất quán hàng xe đỗ dọc vỉa hè trái (`drive_007`, `drive_018`, `drive_050`), tạo `137` FP lớp `car` ở Gold QC và khiến model cũng bắt toàn bộ hàng xe đỗ lề trái trên tập `eval` ([drive_052.jpg](assets/eval_predictions/visuals/drive_052.jpg) dự đoán `17 cars` so với `6 car` trong nhãn `eval`), kéo Precision của `car` xuống `0.645`. Ngoài ra, khi kiểm tra chéo tọa độ nhãn chuẩn trên ảnh thực tế (`drive_042`, `drive_052`, `drive_053`), nhận thấy mã số lớp gốc của bộ dữ liệu UA-DETRAC và tên lớp trong `data.yaml` có sự hoán vị ở các ID `1, 2, 3` (học viên giữ nguyên không sửa `data.yaml` hay `eval` đúng quy định).

4. **Vì sao không dùng 10 ảnh eval để train? Vai trò validation của chúng trong notebook là gì?**
   - **Không dùng 10 ảnh `eval` để train** vì sẽ gây **rò rỉ dữ liệu (data leakage)**: với tập dữ liệu rất nhỏ (50 ảnh train), nếu đưa 10 ảnh `eval` vào huấn luyện thì model sẽ ghi nhớ trực tiếp bối cảnh và vật thể của chính tập đánh giá, làm các chỉ số Precision/Recall/mAP tăng ảo và không còn phản ánh năng lực tổng quát hóa trên dữ liệu mới (đồng thời vi phạm điều kiện bắt buộc của học phần).
   - **Vai trò validation trong notebook**: Trong `data.yaml`, `val` trỏ tới `images/eval` (10 ảnh) để đánh giá sau mỗi epoch trong quá trình `model.train(...)` và làm tiêu chí chọn ra trọng số tốt nhất `best.pt`. Do `best.pt` được chọn dựa trên điểm số của chính 10 ảnh này, tập `eval` thực chất đóng vai trò **validation set** để chọn model chứ **không phải một test set độc lập hoàn toàn (blind test set)**.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| CP1–CP3: Gán nhãn & Self-QC 50 ảnh train | Nhiều ảnh có đoàn xe rất nhỏ nối đuôi ở xa (`drive_002`, `drive_005`), xe bị xe buýt che một nửa (`drive_008`, `drive_042`) và xe ba gác có mái che dễ nhầm ô tô nhỏ (`drive_034`). | Thiết lập quy tắc cụ thể trong `GUIDELINE_MINI.md`, chạy `tools/check_yolo_labels.py` và kiểm tra trực quan lại 10 ảnh đại diện trước khi khóa nhãn. | Đạt `50/50` cấu trúc YOLO hợp lệ, khóa và push bộ nhãn gốc lên GitHub tại commit `837f9a7`. |
| CP5: Đối chiếu Gold QC (`evaluate_annotations.py`) | Chỉ số hình học đạt cao (`mean_iou_of_matches = 0.8717 >= 0.70`) nhưng `F1 = 0.6866 < 0.80` (`passed = false`, `FP = 246`, `FN = 183`). | Mở trực tiếp các ảnh lệch nhiều (`drive_005`, `drive_007`, `drive_018`, `drive_026`, `drive_050`) để kiểm tra tọa độ thay vì chỉ nhìn số FP/FN; xác định do gán thêm dãy xe đỗ lề đường ngoài ROI của gold, lọc xe nhỏ ở xa và 35 ca lệch ranh giới lớp `0/1`. | Đã xuất `outputs/annotation_evaluation.json` và ghi nhận đầy đủ phân tích; chưa kịp thực hiện vòng rework sửa lại bộ nhãn trước thời hạn nộp. |
| CP6: Huấn luyện & đóng gói kết quả Colab | Trên tập 50 ảnh lệch lớp mạnh (`car` chiếm đa số), Recall của `truck` (`0.400`) và `van` (`0.404`) thấp; file kết quả nén `day2_colab_outputs.zip` sinh ra trên máy ảo Colab cần đưa về repo sạch `.pt`. | Chạy trọn vẹn pipeline `yolo26n.pt` (50 epochs, GPU Tesla T4) từ commit `837f9a7`, lưu metrics thật và kiểm tra `.gitignore` chặn tự động `*.pt`. | Đã có link Colab, notebook có output và đầy đủ metrics; cần giải nén 2 thư mục `eval_predictions/` và `yolo26_runs/` từ `day2_colab_outputs.zip` vào `report/individual/assets/` trước khi push chốt bài. |

## 7. Tự đánh giá theo rubric

Đối chiếu thực tế bài làm với các tiêu chí trong [RUBRIC.md](../../RUBRIC.md):

- **Hoàn thành và định dạng YOLO (10 điểm)**: **Đã hoàn thành** — đủ 50 file `.txt` trong `dataset/labels/train/` trùng basename với ảnh, đúng chuẩn 5 cột normalized và 4 class ID, vượt qua `tools/check_yolo_labels.py` (`50/50`).
- **Đầy đủ vật thể (20 điểm)**: **Đạt một phần khá** — đã gán `716` box bao phủ đầy đủ phương tiện ở tiền/trung cảnh, kể cả xe bị che khuất và cắt khung (`Recall = 0.7198`), nhưng còn bỏ sót các xe rất nhỏ ở làn xa (`FN = 183`).
- **Độ bám sát và hình học box (20 điểm)**: **Đạt tốt** — `mean_iou_of_matches = 0.8717` (vượt ngưỡng Xuất sắc `>= 0.80`), các box ôm sát phần nhìn thấy của xe và không gộp chung nhiều xe.
- **Phân lớp nhất quán (15 điểm)**: **Đạt một phần** — tuân thủ nghiêm việc không gán người đi bộ, xe máy, xe đạp, xe ba gác; tuy nhiên còn 35 ca lệch ranh giới giữa lớp `1` và `0` cũng như chênh lệch quy ước gán dãy xe đỗ ven đường so với gold.
- **Mini guideline và quyết định mơ hồ (10 điểm)**: **Đã hoàn thành** — [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md) có đầy đủ định nghĩa 4 lớp, điểm phân biệt, 4 quy tắc vẽ box và 5 ca mơ hồ thực tế kèm lý do theo schema.
- **Gold-set annotation QC (15 điểm)**: **Đã có kết quả chấm và phân tích chi tiết, còn thiếu bước rework đạt ngưỡng F1** — đã xuất [outputs/annotation_evaluation.json](../../outputs/annotation_evaluation.json) (`mean_iou = 0.8717` đạt `>= 0.70`, `F1 = 0.6866 < 0.80`), đã mổ xẻ nguyên nhân trên từng nhóm ảnh cụ thể nhưng chưa thực hiện sửa nhãn sau khóa.
- **Pipeline YOLO và phân tích (10 điểm)**: **Đã chạy hoàn chỉnh trên Colab và phân tích đầy đủ** — giữ nguyên split `50 train / 10 eval` (không dùng `eval` để train), có link Colab chạy thật, bảng metrics và câu trả lời chi tiết cho 4 câu hỏi cuối notebook (cần đảm bảo đính kèm đủ 2 thư mục ảnh/log trong `report/individual/assets/` khi push).

## 8. Công cụ AI và tài liệu tham khảo

- **Công cụ AI đã sử dụng**: Sử dụng trợ lý AI (Google Antigravity / Gemini) ở bước cuối để hỗ trợ kiểm tra danh mục file nộp theo `SUBMISSION.md`, tổng hợp log kết quả từ notebook Colab đã chạy và biên tập diễn đạt báo cáo rõ ràng theo đúng mẫu quy định.
- **Phần học viên tự thực hiện và tự kiểm tra**: Tự thực hiện bộ nhãn 50 ảnh `dataset/labels/train/` và khóa tại commit `837f9a7`; tự chạy `tools/check_yolo_labels.py` và `tools/evaluate_annotations.py` với `gold_train_labels`; tự cấu hình và chạy huấn luyện/đánh giá `yolo26n.pt` trên Google Colab cá nhân; tự kiểm tra đối chiếu từng con số (`TP`, `FP`, `FN`, `F1`, `mean_iou_of_matches`, `Precision`, `Recall`, `mAP50`, `mAP50–95`) và tên ảnh minh họa khớp hoàn toàn với kết quả chạy thực tế.
- **Tài liệu tham khảo**:
  1. Tài liệu hướng dẫn và quy định của bài thực hành: `README.md`, `RULES.md`, `CHECKPOINTS.md`, `GUIDE.md`, `RUBRIC.md`, `SUBMISSION.md`.
  2. Tài liệu chính thức Ultralytics YOLO: [https://docs.ultralytics.com](https://docs.ultralytics.com).
  3. Bộ dữ liệu cảnh giao thông gốc UA-DETRAC (phát hành theo giấy phép CC-BY-4.0, bản biên tập phục vụ học phần).
