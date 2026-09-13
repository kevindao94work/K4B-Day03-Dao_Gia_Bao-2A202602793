# 📊 BÁO CÁO THU HOẠCH NGHIỆM THU BÀI LAB 3 (BƯỚC 3 — SUBMISSION ARTIFACT)

> **Họ và Tên Học viên:** Đào Gia Bảo
>
> **Mã Sinh Viên / Mã Học viên:** 2A202602793
>
> **Chủ đề Lựa chọn:** Trợ lý Học vụ VinUni — tra cứu hồ sơ, GPA và đặt lịch tư vấn với cố vấn (lĩnh vực Giáo dục, gợi ý 1.1)

---

## 1. BẢNG CHẤM ĐIỂM AGENTIC FIT SCORING MATRIX (ĐÁNH GIÁ CHỦ ĐỀ)

| Tiêu chí Đánh giá | Mức độ (1 - 5) | Giải trình chi tiết lý do chọn điểm |
| :--- | :---: | :--- |
| **1. Multi-step Reasoning** | 4 / 5 | Với yêu cầu tư vấn, Agent cần xác định mã sinh viên và thời gian hẹn, tra cứu hồ sơ để lấy đúng cố vấn, dùng kết quả đó đặt lịch rồi tổng hợp xác nhận. Các bước phụ thuộc nhau, nhưng quy trình còn ngắn và chưa cần lập kế hoạch phức tạp. |
| **2. Tool Interaction** | 4 / 5 | Agent cần gọi academic_query để lấy dữ liệu học vụ và schedule_appointment để tạo lịch qua MCP Server; chỉ trả lời bằng văn bản không hoàn thành được yêu cầu. Phạm vi lab gồm hai công cụ với dữ liệu mô phỏng, chưa tích hợp hệ thống học vụ hoặc lịch thật. |
| **3. Dynamic Decision** | 4 / 5 | Agent chọn trả lời trực tiếp, tra cứu hoặc đặt lịch theo yêu cầu. Sau tra cứu thành công, Agent lấy tên cố vấn từ dữ liệu trả về để đặt lịch; nếu nhận NOT_FOUND thì thông báo không tìm thấy và đề nghị kiểm tra mã, không tự bịa hồ sơ hay tiếp tục đặt lịch. |
| **4. Long Horizon Goal** | 3 / 5 | Agent phải giữ mục tiêu đặt lịch tư vấn cùng mã sinh viên, thời gian và cố vấn xuyên suốt các lượt gọi công cụ cho đến khi nhận kết quả đặt lịch. Tuy nhiên, nhiệm vụ hoàn tất trong một phiên ngắn, chưa có theo dõi nhiều ngày hay quản lý kế hoạch dài hạn. |
| **TỔNG ĐIỂM AGENTIC FIT** | **15 / 20** | **15 > 12:** Bài toán phù hợp triển khai Agentic System vì cần phối hợp tra cứu và hành động, đồng thời chọn bước tiếp theo dựa trên kết quả công cụ. |

---

## 2. TRÍCH XUẤT KẾT QUẢ WATERFALL TRACE LOG (SAU KHI CHẠY TEST SUITE TRÊN API THẬT)

Đã chạy `python src/app.py --all` với **OpenAIProvider**, model **gpt-4o-mini**, sử dụng `OPENAI_API_KEY` đã cấu hình trong `.env`. Log lần nghiệm thu không có cảnh báo API hoặc fallback sang Mock. Backend học vụ và đặt lịch vẫn là dữ liệu mô phỏng của lab; phần quyết định và kết luận dùng LLM API thật.

Trace đầy đủ tại [trace_waterfall.json](trace_waterfall.json), gồm **11 sự kiện: 6 TOOL_EXECUTION và 5 FINAL_ANSWER**. Mỗi sự kiện có `step`, `query`, `action_type`, `latency_ms`; sự kiện công cụ có thêm `tool_name`, `arguments`, `observation`, còn kết luận có `thought`, `output`. Số bước bắt đầu lại từ 1 theo từng câu hỏi. `latency_ms` của bước gọi công cụ gồm thời gian gọi LLM và thực thi công cụ.

Trích nguyên chuỗi **TC04: tra cứu hồ sơ → lấy cố vấn → đặt lịch → kết luận** từ trace của lần nghiệm thu:

```json
[
  {
    "step": 1,
    "query": "Tôi là sinh viên SV2026002 và chưa biết cố vấn học tập của mình là ai. Hãy tra cứu hồ sơ học vụ, cho tôi biết GPA hiện tại, rồi đặt lịch tư vấn với đúng cố vấn của tôi vào lúc 09:00 ngày 16/09/2026.",
    "action_type": "TOOL_EXECUTION",
    "tool_name": "academic_query",
    "arguments": {
      "student_id": "SV2026002"
    },
    "observation": {
      "status": "SUCCESS",
      "student_id": "SV2026002",
      "data": {
        "full_name": "Trần Thị Bình",
        "class": "AI-K4",
        "gpa": 3.6,
        "email": "binh.tt@vinuni.edu.vn",
        "status": "Đang học",
        "advisor": "TS. Lê Thị B"
      }
    },
    "latency_ms": 1132.68
  },
  {
    "step": 2,
    "query": "Tôi là sinh viên SV2026002 và chưa biết cố vấn học tập của mình là ai. Hãy tra cứu hồ sơ học vụ, cho tôi biết GPA hiện tại, rồi đặt lịch tư vấn với đúng cố vấn của tôi vào lúc 09:00 ngày 16/09/2026.",
    "action_type": "TOOL_EXECUTION",
    "tool_name": "schedule_appointment",
    "arguments": {
      "student_id": "SV2026002",
      "datetime_str": "09:00 16/09/2026",
      "advisor_name": "TS. Lê Thị B"
    },
    "observation": {
      "status": "SUCCESS",
      "booking_id": "BK-SV2026002-99",
      "student_id": "SV2026002",
      "datetime": "09:00 16/09/2026",
      "advisor": "TS. Lê Thị B",
      "message": "Đặt lịch thành công cho sinh viên SV2026002 với TS. Lê Thị B vào lúc 09:00 16/09/2026."
    },
    "latency_ms": 1356.17
  },
  {
    "step": 3,
    "query": "Tôi là sinh viên SV2026002 và chưa biết cố vấn học tập của mình là ai. Hãy tra cứu hồ sơ học vụ, cho tôi biết GPA hiện tại, rồi đặt lịch tư vấn với đúng cố vấn của tôi vào lúc 09:00 ngày 16/09/2026.",
    "action_type": "FINAL_ANSWER",
    "thought": "OpenAI phản hồi trực tiếp bằng văn bản (không cần gọi công cụ).",
    "output": "Hồ sơ học vụ của bạn SV2026002 như sau:\n- **Họ và tên:** Trần Thị Bình\n- **Lớp:** AI-K4\n- **GPA hiện tại:** 3.6\n- **Trạng thái:** Đang học\n- **Cố vấn học tập:** TS. Lê Thị B\n\nBạn đã đặt lịch tư vấn với cố vấn học tập TS. Lê Thị B thành công vào lúc **09:00 ngày 16/09/2026**. \n\nThông tin đặt lịch:\n- **Mã sinh viên:** SV2026002\n- **Thời gian hẹn:** 09:00 16/09/2026\n- **Tên cố vấn:** TS. Lê Thị B\n- **Mã đặt lịch:** BK-SV2026002-99\n\nNếu cần thêm thông tin, hãy cho tôi biết nhé!",
    "latency_ms": 1985.82
  }
]
```

---

## 3. TỔNG KẾT KẾT QUẢ NGHIỆM THU & NỘP BÀI

- [x] Đã xác nhận Agent kết nối LLM API thật qua OpenAIProvider, model gpt-4o-mini.
- **Tổng số Test Cases đã chạy thành công:** **5 / 5** theo đối chiếu hành vi, tham số công cụ, Observation và câu trả lời cuối của lần nghiệm thu.
- **Số lượt gọi Tool qua MCP Server chính xác:** **6 lượt** (4 academic_query, 2 schedule_appointment). Trong đó 5 kết quả SUCCESS và 1 NOT_FOUND đúng kỳ vọng của TC05.
- **Kết quả đẩy Repo nộp bài:** [ ] Chưa Commit và Push trong bước nghiệm thu này.

| Test Case | Kết quả | Bằng chứng đối chiếu |
| :--- | :---: | :--- |
| TC01 | Đạt | Trả lời văn bản trực tiếp, không gọi công cụ. Chỉ đánh giá hành vi điều tuyến; chưa xác minh nội dung với quy chế VinUni chính thức vì System Prompt không chứa văn bản quy chế. |
| TC02 | Đạt | Gọi academic_query với SV2026001; kết luận đúng hồ sơ Nguyễn Văn An, GPA 3.85 và cố vấn từ Observation. |
| TC03 | Đạt | Tra cứu thêm SV2026001 rồi đặt lịch đúng 14:00 15/09/2026 với PGS.TS Nguyễn Văn A; kết luận giữ nguyên BK-SV2026001-99. Ca này không quy định cấm tra cứu trước khi đặt lịch. |
| TC04 | Đạt | Tra cứu SV2026002 trước, lấy TS. Lê Thị B từ kết quả rồi đặt lịch 09:00 16/09/2026; kết luận có GPA 3.6 và BK-SV2026002-99. |
| TC05 | Đạt | Tra cứu SV9999999 nhận NOT_FOUND; đề nghị kiểm tra lại mã, không bịa hồ sơ và không đặt lịch. |

**Kiểm thử Interactive CLI:** Đã chạy `python src/app.py --interactive`, nhập câu hỏi tra cứu SV2026001, yêu cầu đặt lịch cho SV2026002 với TS. Lê Thị B lúc 09:00 16/09/2026, rồi nhập `exit`. Hai yêu cầu có kết luận đúng dữ liệu và phiên thoát thành công (exit code 0). Đây là hai yêu cầu độc lập có đủ thông tin; chưa đánh giá khả năng nhớ ngữ cảnh giữa các câu hỏi. Số lượt công cụ interactive không cộng vào 6 lượt nghiệm thu ở trên. Sau kiểm thử interactive, đã khôi phục trace đầy đủ của bộ 5 ca vào trace_waterfall.json.

**Sửa lỗi phát hiện khi nghiệm thu:** Lần chạy đầu TC03 rút gọn sai mã đặt lịch thành BK-SV2026001. Đã bổ sung quy tắc trong src/prompts.py yêu cầu sao chép đầy đủ mã định danh và các trường xác nhận lịch, đồng thời yêu cầu kiểm tra lại mã khi NOT_FOUND; sau đó chạy lại toàn bộ 5 ca. Các số liệu và đoạn trích trên lấy từ lần chạy sau sửa lỗi.

**Log kiểm chứng:** [Chạy toàn bộ test suite](eval_suite.log) và [Interactive CLI](eval_interactive.log). Dòng “Đã thực thi 5/5” của CLI chỉ đếm ca đã chạy; kết quả Đạt trong bảng được đối chiếu riêng với trace và nội dung phản hồi, không suy ra chỉ từ bộ đếm này.

---

> ✅ **HOÀN TẤT NỘP BÀI:** Sao chép đường link GitHub Repository cá nhân của bạn và dán vào ô nộp bài trên hệ thống LMS VLearn để hoàn tất Bài Lab 3!
