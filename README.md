# Bài 9: Kỹ thuật RAG Grounding Dập tắt Ảo giác Hợp đồng Thu mua



## PHẦN 1: PHÂN TÍCH NGUYÊN NHÂN ẢO GIÁC (HALLUCINATION)

Khi nhân viên hỏi ChatGPT mà không kèm tài liệu, AI hoạt động giống như một học sinh đang làm bài thi "đóng sách".

Cơ chế dự đoán (Next-token prediction): AI không có khả năng "truy cập" vào tủ hồ sơ nội bộ của MartX. Thay vào đó, nó tự động suy đoán câu trả lời dựa trên xác suất thống kê các từ ngữ thường đi chung với nhau từ nguồn dữ liệu khổng lồ trên Internet.

Sự tự tin sai lệch: Trên Internet, các hợp đồng cung ứng phổ biến thường có mức phạt nặng (như 20% hay đình chỉ 30 ngày). Do đó, AI tự lắp ghép các thông tin này lại và tạo ra một câu trả lời nghe cực kỳ trôi chảy, hợp lý nhưng lại sai hoàn toàn so với quy định nội bộ của MartX.

Để khắc phục, ta cần dùng RAG Grounding (Cung cấp ngữ cảnh tham chiếu) – tức là đưa cho AI một bài thi "mở sách", yêu cầu nó chỉ được chép phạt đúng từ cuốn sách đó.

## PHẦN 2: CẤU TRÚC RAG PROMPT HOÀN CHỈNH

Dưới đây là cấu trúc Prompt chuẩn áp dụng kỹ thuật RAG Grounding và khóa an toàn Negative Constraint:

[1. Văn bản hợp đồng chuẩn - Dữ liệu nền]
Dưới đây là trích đoạn quy định hợp đồng của MartX:
"Trong trường hợp nhà cung cấp giao mặt hàng rau củ quả bị dập nát với tỷ lệ trên 10%, nhà cung cấp sẽ bị phạt 5% giá trị của lô hàng bị dập nát đó, đồng thời bắt buộc phải yêu cầu đổi trả, giao lại hàng mới trong vòng 24 giờ."

[2. Câu hỏi nghiệp vụ]
Dựa vào văn bản trên, hãy cho biết: Nhà cung cấp giao rau bị dập nát trên 10% thì bị phạt bao nhiêu và có yêu cầu gì đi kèm?

[3. Negative Constraint - Khóa an toàn chống ảo giác]
QUAN TRỌNG: Chỉ được phép sử dụng thông tin từ "Văn bản hợp đồng chuẩn" được cung cấp ở trên để trả lời. Nếu thông tin hỏi không có trong văn bản, tuyệt đối phải trả lời nguyên văn là: "Không có dữ liệu trong tài liệu". Tuyệt đối không tự suy đoán, không bịa đặt, và không sử dụng kiến thức bên ngoài Internet để trả lời.
