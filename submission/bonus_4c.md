# Bonus 4C

Cùng seed=0, 40 epoch, imgsz=640, batch=16; val gồm 53 ảnh gốc và 53 ảnh lật gương với keypoint hoán đổi giải phẫu.

- flip_idx giải phẫu: pose mAP50-95 gốc=0.4573, gương=0.4388; box mAP gốc=0.9303, gương=0.9184.
- flip_idx đồng nhất: pose mAP50-95 gốc=0.4169, gương=0.2977; box mAP gốc=0.9036, gương=0.8939.

Val gốc chỉ có hổ quay phải nên mAP pose trên tập đó có thể che lỗi trái/phải trên ảnh gương; box mAP không chấm danh tính keypoint nên càng không phát hiện lỗi này. So sánh pose mAP trên tập gương có nhãn đúng làm lộ độ suy giảm do quy ước flip_idx. Cần báo riêng từng hướng, kích thước và mức che khuất, thêm ảnh quay trái thật, và kiểm tra nhầm cặp trái/phải thay vì chỉ gộp một mAP.