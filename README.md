# Google Maps reviews Sentiment analysis - AEON MALL Long Biên

Phân tích dữ liệu từ ~7.000 đánh giá công khai của khách hàng tại AEON MALL Long Biên trên Google Maps nhằm tìm ra các thời điểm cần cải thiện dịch vụ, đặc điểm nhóm người đánh giá và thái độ thực tế của khách hàng.

* **Slide báo cáo chi tiết:** [Xem file PDF tại đây](./report/NLP_Project_2_Slides.pdf)
* **Notebook xử lý và chạy mô hình:** [Xem code Python tại đây](./notebooks/sentiment_analysis_visobert.ipynb)

---

### 1. Dữ liệu và Quy trình xử lý
* **Thu thập dữ liệu:** 6,895 đánh giá thu thập từ Google Maps bằng công cụ Apify Google Maps Reviews Scraper (bao gồm số sao, nội dung review, trạng thái Local Guide, số lượng ảnh đính kèm, ngày giờ đăng).
* **Preprocessing:**
  * Chuyển đổi emoji phổ biến sang token cảm xúc (`<EMO_POS>`, `<EMO_NEG>`).
  * Chuẩn hóa từ viết tắt, teencode tiếng Việt (ví dụ: `ko` -> `không`, `dc` -> `được`, `nhìu` -> `nhiều`).
  * Xử lý kéo dài ký tự (de-elongation), đưa về chữ thường và lọc bỏ các review quá ngắn không mang ý nghĩa.
  * Dùng `langdetect` lọc giữ lại **4,199 review tiếng Việt** (loại bỏ 900 review ngoại ngữ) để đưa vào mô hình NLP.

---

### 2. So sánh mô hình Sentiment Analysis
* **TextBlob (Baseline):** Không phù hợp với tiếng Việt. Trên tập 4,199 review, mô hình gắn nhãn trung tính (`polarity = 0`) tới 3,930 câu (93.6%), gần như không nhận diện được sắc thái cảm xúc.
* **5CD-ViSoBERT (`5CD-AI/Vietnamese-Sentiment-visobert` trên Hugging Face):** Khắc phục được nhược điểm trên. Kết quả phân loại khớp sát với phân bổ điểm sao thực tế:
  * **Tích cực (POS):** 3,365 reviews (80.1%)
  * **Tiêu cực (NEG):** 621 reviews (14.8%)
  * **Trung tính (NEU):** 213 reviews (5.1%)
  * **Độ tin cậy mô hình:** 3,950/4,199 dự đoán (94.1%) đạt mức độ chắc chắn cao (`score > 0.8`).

---

### 3. Các Insight chính
* **Phân bổ theo thời gian:** Điểm đánh giá trung bình đạt mức cao nhất vào giữa tuần (Thứ 4 đến Thứ 6: 4.57–4.58 sao) và khung giờ tối (18h–22h: 4.61 sao). Ngược lại, điểm số tụt xuống thấp nhất vào Chủ Nhật (4.46 sao) do lượng khách quá tải cuối tuần làm giảm chất lượng trải nghiệm.
* **Đặc điểm nhóm người đánh giá:** Nhóm *Trustworthy* (100+ reviews, gần 100% là Local Guides) có tiêu chuẩn đánh giá khắt khe hơn (trung bình 4.50 sao so với 4.61 sao của nhóm Casual/Active) nhưng đáng tin cậy nhất với tỷ lệ đính kèm ảnh thực tế cao nhất (~25%). Nhóm *Newbie* (0–4 reviews) có điểm trung bình thấp nhất (4.28 sao), thường chỉ để lại đánh giá khi gặp trải nghiệm xấu.
* **Độ dài review và cảm xúc:** Khách hàng không hài lòng có xu hướng viết dài và chi tiết hơn hẳn để phàn nàn (nhóm review dài có 332 đánh giá tiêu cực, cao gấp 2.7 lần nhóm review ngắn với 123 đánh giá). Các review ngắn đa phần mang tính xã giao và tích cực.
---

> **Lưu ý về dữ liệu:** Do bộ dữ liệu bị imbalance nặng (hơn 80% là đánh giá tích cực), nếu chỉ nhìn vào điểm số trung bình tổng thể sẽ dễ bị nhiễu bởi positivity bias và bỏ sót các vấn đề vận hành. Thay vì phân tích dàn trải, hướng khai thác hiệu quả hơn là tập trung lọc sâu vào nhóm thiểu số các review tiêu cực (đặc biệt là nhóm review dài và các đánh giá vào Chủ Nhật) để tìm ra đúng điểm nghẽn dịch vụ cần cải thiện. Đồng thời, sự mất cân bằng này cũng là lý do dự án ưu tiên dùng pre-trained model `5CD-ViSoBERT` thay vì tự train từ đầu để tránh tình trạng model học lệch về nhóm tích cực.
