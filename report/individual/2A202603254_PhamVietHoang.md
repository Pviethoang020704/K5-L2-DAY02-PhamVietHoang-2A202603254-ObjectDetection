# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- **Họ tên:** Phạm Việt Hoàng
- **MSSV:** 2A202603254
- **Email:** phamviethoang.dev@gmail.com
- **Lớp:** C403
- **Link repo bài làm:** https://github.com/Pviethoang020704/K5-L2-DAY02-PhamVietHoang-2A202603254-ObjectDetection
- **Link Colab đã chạy:** https://colab.research.google.com/drive/1dssPllP8ZZc5jbwQX_9Sv5XvhBbnAOsw?usp=sharing

## 2. Schema và mini guideline

File hướng dẫn: [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md).

| ID | Lớp | Định nghĩa và điểm phân biệt |
|---|---|---|
| 0 | `car` | Sedan, SUV, taxi, xe bán tải; xe bán tải không xếp vào `truck` |
| 1 | `truck` | Xe tải hàng, xe đầu kéo |
| 2 | `bus` | Xe buýt và minibus chở khách |
| 3 | `van` | Xe van, minivan chở người hoặc hàng |

Mỗi phương tiện hợp lệ tương ứng một bounding box, ôm sát **phần nhìn thấy**; vẫn gán xe đỗ, xe bị che/cắt khung nếu đủ cơ sở nhận diện, không suy đoán phần nằm ngoài ảnh. Không gán xe máy, xe đạp, người đi bộ, ảnh quảng cáo hoặc phản chiếu không phải phương tiện thật trong cảnh.

Ba trường hợp đã được nêu trong mini guideline cá nhân (đề nghị đối chiếu lại nguyên văn file gốc trước khi nộp):

1. `drive_003.jpg`: xe khách bị che khuất một phần; xử lý theo phần thân xe nhìn thấy.
2. `drive_044.jpg`: tình huống khó phân biệt `van` và `car`; so sánh hình dáng, khoang xe và đối chiếu schema.
3. `drive_019.jpg`: tình huống khó phân biệt `bus` và `van`; cân nhắc đặc trưng phương tiện chở khách, không tự đoán khi không chắc.

Với trường hợp thiếu bằng chứng để phân loại, ghi rõ ảnh và xin giảng viên làm rõ thay vì tự sửa schema.

## 3. Annotation và self-QC

- **Công cụ:** CVAT chạy cục bộ bằng Docker, xuất YOLO Detection.
- **Số ảnh train / số file nhãn:** 50 / 50.
- **Lệnh kiểm tra:** `py tools/check_yolo_labels.py`.
- **Kết quả thực tế:** `ĐẠT cấu trúc: 50/50 file nhãn hợp lệ. Chờ ground truth để chấm chất lượng.`
- **Kiểm tra đối chiếu trên Colab:** `OK: 50 train + 10 eval; eval không đi vào train.`

Checker xác nhận số lượng, basename và tính hợp lệ hình học/định dạng, không tự khẳng định độ bao phủ đối tượng hay chất lượng phân lớp.

**Danh sách ít nhất 10 ảnh đã trực tiếp rà bằng mắt:** 
    drive_001.jpg
    drive_002.jpg
    drive_003.jpg
    drive_004.jpg
    drive_005.jpg
    drive_006.jpg
    drive_007.jpg
    drive_008.jpg
    drive_009.jpg
    drive_010.jpg
**Lỗi phát hiện khi rà và cách sửa:** 
    -   khó phân biệt một vài bus và van: sửa lại nhãn của bus và van
    -   box dài hơn kích thước xe: thu hẹp khoảng cách box sát với viền xe
**Commit khóa nhãn ban đầu:** `63e3d53` — `CP4: Initial 50 YOLO annotations and guideline`. Các commit tiếp theo được liệt kê: `4493995 update guideline_mini`, `cd3b462 update data`; notebook đã clone tại `cd3b462`.

## 4. Gold QC và rework

Đánh giá nhãn với ngưỡng ghép **IoU ≥ 0.50**, ghép box cùng lớp một-một. Dữ liệu nhận được:

| Chỉ số | Kết quả từ JSON | Sau sửa nếu có |
|---|---:|---:|
| TP | 523 | Chưa có lần đánh giá riêng |
| FP | 107 | Chưa có lần đánh giá riêng |
| FN | 130 | Chưa có lần đánh giá riêng |
| Precision | 0.8302 | Chưa có lần đánh giá riêng |
| Recall | 0.8009 | Chưa có lần đánh giá riêng |
| F1 | **0.8153** | Chưa có lần đánh giá riêng |
| mean_iou_of_matches | **0.9553** | Chưa có lần đánh giá riêng |
| passed | **true** | Chưa có lần đánh giá riêng |

Kết quả **đạt cả hai ngưỡng**: F1 ≥ 0.80 và mean IoU ≥ 0.70. File cần có trong repo: [outputs/annotation_evaluation.json](../../outputs/annotation_evaluation.json).

**Nhận xét:** Chất lượng hình học của các box được ghép khớp rất tốt (mean IoU 0.9553), nhưng vẫn còn 107 FP và 130 FN. Chưa thể xác định nguyên nhân cụ thể từ số FP/FN tổng hợp: có thể do bỏ sót, box thừa, sai class hoặc box không đạt điều kiện ghép.

**Những ảnh đáng kiểm tra lại dựa trên `per_image`:**

- `drive_026`: 39 gold boxes, 29 student boxes, 19 matches (20 gold box không ghép).
- `drive_024`: 30 gold boxes, 19 student boxes, 11 matches (19 gold box không ghép).
- `drive_009`: 21 gold boxes, 26 student boxes, 7 matches (14 gold box không ghép, 19 student box không ghép).
- `drive_028`: 3 gold boxes, 9 student boxes, 2 matches (7 student box không ghép).
- `drive_019`: 27 gold boxes, 25 student boxes, 17 matches (10 gold box không ghép).

Các chỉ số trên là **gợi ý kiểm tra**, không khẳng định những lỗi cụ thể hay việc rework đã diễn ra.

**Lịch sử rework:** Không thực hiện rework vì lần đánh giá đầu đạt ngưỡng

## 5. Kết quả YOLO

**Môi trường huấn luyện (theo notebook đã chạy):**

- Nền tảng: Google Colab, GPU **NVIDIA Tesla T4** (CUDA:0; 14913 MiB hiển thị trong log).
- Python: **3.13.16**.
- Ultralytics: **8.4.208**.
- PyTorch: **2.14.1+cu130**.
- Mô hình khởi tạo: **`yolo26n.pt`**.
- Train: **50 ảnh**, 50 epochs, `imgsz=640`, `batch=-1` (auto), `seed=20260909`, `device=0`, `plots=True`.
- Eval/validation: **10 ảnh**, **113 đối tượng**, đúng split `images/eval` / `labels/eval`.
- Thay đổi so với notebook gốc: thay `https://github.com/Pviethoang020704/K5-L2-DAY02-PhamVietHoang-2A202603254-ObjectDetection.git` bằng fork cá nhân; 
- Checkpoint tốt nhất trong phiên Colab: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt`
- Notebook clone commit: `cd3b462 update data`.

**Kết quả tổng thể trên 10 ảnh eval (`best.pt`):**

| Chỉ số | Giá trị |
|---|---:|
| Precision | **0.4925** |
| Recall | **0.3822** |
| mAP50 | **0.4744** |
| mAP50–95 | **0.3374** |

**Chi tiết theo lớp** (giá trị hiển thị trong log validation, làm tròn):

| Lớp | Số đối tượng eval | Precision | Recall | mAP50 | mAP50–95 |
|---|---:|---:|---:|---:|---:|
| car | 94 | 0.797 | 0.795 | 0.876 | 0.612 |
| truck | 5 | 0.268 | 0.400 | 0.451 | 0.331 |
| bus | 2 | 0.000 | **0.000** | 0.0179 | 0.0125 |
| van | 12 | 0.904 | 0.333 | 0.553 | 0.394 |

Kết quả phản ánh năng lực nhận diện chưa đồng đều giữa các lớp; `car` trội hơn đáng kể, trong khi các lớp ít mẫu có Recall thấp. Tập eval rất nhỏ, đặc biệt chỉ 2 đối tượng `bus`, nên kết luận về hiệu năng lớp cần thận trọng. Kết quả model thấp không tự phủ định chất lượng annotation đã đạt ngưỡng CP5.

**Tài liệu minh chứng sau khi giải nén ZIP và chép đúng thư mục:**

- [Ảnh dự đoán trên 10 ảnh eval](assets/eval_predictions/visuals/)
- [Log và đồ thị huấn luyện](assets/yolo26_runs/vehicle_annotation/)
- [Confusion matrix, PR curve và đồ thị đánh giá](assets/yolo26_runs/vehicle_eval/)
- [Ảnh `drive_053.jpg`](assets/eval_predictions/visuals/drive_053.jpg)
- [Ảnh `drive_058.jpg`](assets/eval_predictions/visuals/drive_058.jpg)

### Phân tích ảnh eval cụ thể

Quan sát ảnh dự đoán `drive_053.jpg`: một xe buýt lớn màu xanh-trắng ở phần dưới bên phải được mô hình vẽ box `bus` với confidence khoảng **0.44**. Ảnh còn có các phương tiện được vẽ nhãn `car` và `van`. Một số box trùm lên nhau hoặc có độ bám chưa thật chặt theo quan sát hình vẽ. Do chưa trực tiếp đối chiếu nhãn chuẩn riêng của ảnh này, ví dụ chỉ xác nhận **mô hình có đưa ra dự đoán lớp bus**, không khẳng định dự đoán đó đúng với ground truth.

Trong `drive_058.jpg`, một số phương tiện cỡ lớn được mô hình dự đoán là `bus` với confidence khoảng 0.26–0.35. Ngưỡng confidence thấp và Recall `bus=0` trong báo cáo validation cho thấy không thể dựa vào việc nhìn thấy nhãn `bus` trên ảnh prediction để kết luận mô hình nhận diện đúng lớp này. Cần so sánh trực tiếp với nhãn eval.

### Trả lời bốn câu hỏi cuối notebook

**1. Lớp nào có Recall thấp nhất? Ảnh nào minh họa?**

`bus` có Recall thấp nhất, **0.000**, trên 2 đối tượng thuộc lớp này trong tập eval. `drive_053.jpg` và `drive_058.jpg` minh họa các tình huống xe buýt hoặc phương tiện kích thước lớn được model gán nhãn bus nhưng cần đối chiếu ground truth; kết quả validation xác nhận model không ghép đúng đối tượng bus nào ở tiêu chí đánh giá. Mẫu eval của bus quá ít nên kết quả có độ bất định cao.

**2. Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm tra điều gì trên box?**

Kiểm tra độ sát mép xe, box lệch tâm, box rộng quá mức, box thiếu phần thân xe và box trùm nền/xe bên cạnh. Một dự đoán có thể được tính đúng tại IoU 0.50 nhưng không đạt các ngưỡng nghiêm ngặt hơn. Trong bài này mAP50 = 0.4744 và mAP50–95 = 0.3374; chênh lệch cho thấy độ chính xác vị trí vẫn là một yếu tố đáng kiểm tra, nhưng không đủ để kết luận đây là nguyên nhân duy nhất.

**3. Có lỗi class nào liên quan guideline? Dữ kiện nào hỗ trợ?**

Các ranh giới `bus`–`van` và `car`–`van` là những tình huống đã được nêu trong mini guideline (`drive_019` và `drive_044`). Trên eval, Recall `bus=0.000` và `van=0.333`, trong khi `car=0.795`, cho thấy các lớp ít mẫu còn yếu. Quan sát `drive_053.jpg` cũng thấy mô hình đưa ra nhãn `bus` và `van` cho các phương tiện trong cùng cảnh. Đây là **căn cứ để ưu tiên kiểm tra sự phân biệt lớp**, không chứng minh trực tiếp rằng guideline đã gây lỗi; cần xem ground truth, confusion matrix và từng trường hợp cụ thể để kết luận.

**4. Vì sao không dùng 10 ảnh eval để train? Vai trò validation là gì?**

Nếu đưa eval vào train sẽ gây rò rỉ dữ liệu, khiến việc đánh giá trên chính các ảnh đó quá lạc quan và không còn phản ánh khả năng tổng quát hóa trên ảnh chưa dùng cập nhật trọng số. Trong notebook này, 10 ảnh eval được dùng làm validation qua các epoch và chọn checkpoint `best.pt`; vì vậy chúng **không phải một test set độc lập**. Cần giữ nguyên split đã quy định để kết quả có thể đối chiếu.

## 6. Khó khăn và cách xử lý

| Bước | Vấn đề hoặc điểm cần kiểm tra | Cách xử lý đã có bằng chứng | Trạng thái |
|---|---|---|---|
| Chuẩn bị | Cần môi trường gán nhãn | Dùng CVAT chạy với Docker | Đã có nhãn được export |
| CP1 | Ranh giới lớp `car`/`van`/`bus` mơ hồ | Ghi ba ca mơ hồ trong guideline | Đã có nội dung guideline được trao đổi |
| CP3 | Kiểm tra đúng số lượng/định dạng | Chạy `check_yolo_labels.py` | Đạt 50/50 |
| CP4 | Lưu phiên bản ban đầu | Commit `63e3d53` | Có commit khóa nhãn |
| CP5 | Cần so sánh chất lượng với gold | Chạy evaluator tại IoU 0.5 | F1 0.8153, mean IoU 0.9553, passed=true |
| CP6 | Tập nhỏ và lệch lớp | Giữ split, train YOLO26n, phân tích metric theo lớp | Đã train 50 epoch, có 10 ảnh prediction |

Lỗi hệ thống/runtime bổ sung:Không ghi nhận lỗi runtime ảnh hưởng kết quả

## 7. Tự đánh giá theo rubric

| Tiêu chí | Minh chứng / mức độ xác nhận |
|---|---|
| 50 file và định dạng YOLO | Checker đạt 50/50 |
| Đầy đủ vật thể | QC có TP=523, FN=130; còn nhiều đối tượng chưa ghép |
| Độ bám sát box | mean IoU trên matches đạt 0.9553; đây chỉ là trung bình của các box ghép thành công |
| Phân lớp nhất quán | Có guideline và kiểm tra gold; còn FP/FN nên chưa khẳng định hoàn hảo |
| Mini guideline | Có bốn lớp và ba ca mơ hồ |
| Gold QC | F1=0.8153; mean IoU=0.9553; passed=true |
| Pipeline YOLO | Train 50 epoch; val trên 10 ảnh; đã có metrics và ảnh prediction |

**Tự đánh giá:** Các bước chính có kết quả thực tế và vượt qua cổng kiểm tra chất lượng nhãn. Những phần cần xác nhận để báo cáo hoàn chỉnh về mặt quy trình gồm danh sách tối thiểu 10 ảnh đã tự rà bằng mắt và tình trạng rework thực tế. Không tự chấm điểm tuyệt đối hay coi metric model thấp là bằng chứng annotation sai.

## 8. Công cụ AI và tài liệu tham khảo

**Công cụ sử dụng:** CVAT (annotation), Docker (môi trường CVAT), Python (checker/evaluator), Git/GitHub (quản lý phiên bản), Google Colab (train/eval YOLO), Ultralytics (YOLO26n),

**Phạm vi sử dụng ChatGPT:** hỗ trợ hướng dẫn các bước, phân tích JSON CP5 và log/metrics CP6, tổng hợp thành văn bản báo cáo. Chất lượng nhãn và kết quả train được dẫn từ file đã tạo trong quy trình bài làm; 

**Tài liệu:** `RULES.md`, `CHECKPOINTS.md`, `GUIDE.md`, `GUIDELINE_MINI.md`, `RUBRIC.md`, `SUBMISSION.md`, notebook `day2_annotation_to_yolo.ipynb` của repo VinUni AI20k; tài liệu công cụ CVAT 
