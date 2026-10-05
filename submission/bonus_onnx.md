# Bonus: ONNX và latency CPU

Hai graph export từ cùng yolo26n.pt, FP32, batch=1, đầu vào cố định 640×640 trên bus.jpg, iou=0.7. Đo 30 lần sau 3 warm-up cho từng cấu hình; Results.speed tách preprocess/inference/postprocess, không bao gồm đọc file và khởi tạo session.

| Head | conf | preprocess ms | inference ms | postprocess ms | tổng ms | box |
|---|---:|---:|---:|---:|---:|---:|
| one-to-many + NMS | 0.25 | 6.74 | 98.49 | 1.65 | 106.88 | 5 |
| one-to-many + NMS | 0.001 | 4.65 | 72.32 | 2.03 | 79.0 | 186 |
| one-to-one NMS-free | 0.25 | 4.82 | 70.88 | 0.44 | 76.14 | 5 |
| one-to-one NMS-free | 0.001 | 4.7 | 68.84 | 0.46 | 74.0 | 177 |
Conf=0.25: postprocess 1.65 → 0.44 ms; tổng 106.88 → 76.14 ms (-28.8%). Nếu tổng tăng dù hậu xử lý giảm, inference đang chi phối; không được kết luận NMS-free luôn nhanh hơn trên CPU này.
Conf=0.001: postprocess 2.03 → 0.46 ms; tổng 79.00 → 74.00 ms (-6.3%). Nếu tổng tăng dù hậu xử lý giảm, inference đang chi phối; không được kết luận NMS-free luôn nhanh hơn trên CPU này.

Providers: ['CPUExecutionProvider']; ONNX Runtime 1.30.0; Ultralytics 8.4.171. Outputs: {'yolo26n_one2many.onnx': [[1, 84, 8400]], 'yolo26n_one2one.onnx': [[1, 300, 6]]}.

Mặc định export trả raw one-to-many và host chạy NMS; nms=False export head one-to-one nên không có bước loại box bằng IoU. Conf là ngưỡng lọc lúc predict, không đóng cố định vào graph; cả hai đo trên cùng ảnh/kích thước/device. Đối chiếu postprocess để tách chi phí NMS, tổng để đánh giá latency pipeline; nhiễu đo và chênh lệch head khiến mức tăng tốc trên một ảnh không đại diện mọi cảnh hay CPU/NPU.

Nguồn API: https://docs.ultralytics.com/guides/end2end-detection/