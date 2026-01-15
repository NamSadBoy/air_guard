#📊 Air Quality Timeseries — PM2.5 Forecasting & AQI Alerts

#(Supervised + Semi-Supervised Learning)

1️⃣ So sánh Accuracy & F1-macro

Baseline vs Self-training vs Co-training

🔢 Kết quả tổng quan (τ = 0.9)

<img width="1422" height="121" alt="image" src="https://github.com/user-attachments/assets/52ee4f92-9538-4ba5-a47c-d3e5f898850a" />
<img width="989" height="390" alt="image" src="https://github.com/user-attachments/assets/ab7bc25f-d836-4f59-8e76-a8ac356ca499" />


| Phương pháp       | Accuracy   | F1-macro |
| ----------------- | ---------- | -------- |
| **Self-training** | ≈ **0.59** | **0.53** |
| **Co-training**   | ≈ 0.53     | 0.40     |

#🔍Phân tích

Self-training đạt hiệu quả tốt nhất

Accuracy và F1-macro đều cao hơn Co-training

F1-macro cao cho thấy mô hình phân biệt tốt hơn giữa các lớp AQI, không chỉ dự đoán đúng lớp chiếm đa số

Co-training kém hơn đáng kể

F1-macro thấp (~0.40) → mô hình dự đoán lệch lớp, đặc biệt với các mức AQI hiếm (Unhealthy, Hazardous)

#🧠 Giải thích nguyên nhân

Dữ liệu AQI không thực sự thỏa mãn giả định “hai view độc lập” (điều kiện quan trọng của co-training)

Các biến môi trường (PM10, PM2.5, NO2, SO2, thời gian…) tương quan cao
→ Co-training dễ lan truyền pseudo-label sai

#📌 Kết luận phần này

Trong bài toán AQI classification, self-training phù hợp và ổn định hơn co-training.

2️⃣ Động lực học của Semi-Supervised Learning

(Pseudo-labels theo vòng lặp)

🔁 Self-training dynamics

Quan sát từ biểu đồ:
<img width="789" height="390" alt="image" src="https://github.com/user-attachments/assets/c8cae423-5d55-4bd3-811d-458bfca81a30" />


Số pseudo-labels tăng mạnh ở các vòng đầu (iteration 1–3)

F1-macro validation:

Ban đầu tăng

Sau đó giảm dần và dao động

📉 Điều này cho thấy:

Pseudo-labels ban đầu có chất lượng tốt

Nhưng về sau, mô hình bắt đầu “tự tin sai”, dẫn đến:

Nhiễu tích lũy

Hiệu năng không tăng thêm

📌 Insight quan trọng

Self-training có “điểm ngọt” (sweet spot).
Chạy quá nhiều vòng không đảm bảo cải thiện mô hình.

🔁 Co-training dynamics

Quan sát từ biểu đồ:
<img width="790" height="390" alt="image" src="https://github.com/user-attachments/assets/968e24ff-787f-4dbc-b3eb-11aa694d12f6" />


Số pseudo-labels gần như cố định (~500 / vòng)

F1-macro:

Dao động nhẹ

Không có xu hướng cải thiện rõ ràng

📉 Giải thích:

Hai mô hình con không bổ sung đủ thông tin cho nhau

Pseudo-labels mới không mang tính cải thiện

📌 Kết luận phần này

Self-training tận dụng được dữ liệu chưa gán nhãn tốt hơn co-training trong bối cảnh AQI.

3️⃣ Phân tích AQI Alerts theo trạm + Time Series

🚨 Top trạm có số cảnh báo cao
🔵 Self-training
<img width="989" height="390" alt="image" src="https://github.com/user-attachments/assets/3e6dcc4c-2a9f-437a-8898-8dd27e1436ec" />

| Trạm         | Alert count | Alert rate |
| ------------ | ----------- | ---------- |
| Aotizhongxin | 685         | ~49%       |
| Changping    | 643         | ~46%       |
| Dingling     | 561         | ~40%       |
| Dongsi       | 445         | **~56%**   |

🟠 Co-training
<img width="989" height="390" alt="image" src="https://github.com/user-attachments/assets/653dc335-53fa-40b9-9b1d-73f259048c7a" />

| Trạm         | Alert count | Alert rate |
| ------------ | ----------- | ---------- |
| Aotizhongxin | 657         | ~47%       |
| Changping    | 479         | ~34%       |
| Dingling     | 468         | ~33%       |
| Dongsi       | 386         | ~48%       |

🔍 Nhận xét

Aotizhongxin & Changping luôn nằm trong top

Phù hợp thực tế: trạm nội đô → ô nhiễm cao

Self-training phát hiện nhiều cảnh báo hơn

Nhạy hơn với các mức AQI xấu

Co-training bảo thủ hơn

Ít cảnh báo hơn nhưng có nguy cơ bỏ sót

📌 Trade-off

Self-training: recall cao (phát hiện sớm)

Co-training: precision cao hơn nhưng thiếu nhạy

📈 Time series AQI alerts (ví dụ 1 trạm)
Self-training

Dự đoán AQI:

Dao động linh hoạt giữa Moderate → Unhealthy → Hazardous

Cảnh báo xuất hiện sớm và liên tục trong các đợt ô nhiễm

Co-training

AQI dự đoán:

Tập trung nhiều ở Moderate / Unhealthy

Ít cảnh báo hơn, phản ứng chậm hơn với spike PM2.5

📌 Ý nghĩa thực tế

Với hệ thống cảnh báo sức khỏe cộng đồng, self-training phù hợp hơn vì ưu tiên phát hiện sớm.

🎯 Tổng kết chung

Semi-supervised learning giúp cải thiện AQI classification khi nhãn bị thiếu nhiều

Self-training là lựa chọn tốt nhất trong bài toán này:

Hiệu năng cao hơn

Pseudo-label dynamics hợp lý

Cảnh báo AQI nhạy và thực tế hơn

Co-training không phù hợp do dữ liệu không có hai view độc lập rõ ràng


