# Ghi chú phân tích · Day 04

Họ và tên: Duong Manh Hung
MSSV: 2A202603431

## 1. Case được chọn

- ID case: A5
- Câu trả lời của agent: Khẳng định không có incident GitHub Actions nào trong năm 2025.
- Điểm ở lần chạy 1: 45/100

## 2. Bằng chứng trong trace

Dán đoạn trace chứng minh lỗi và nêu nguyên nhân có thể xảy ra.

```text
Tool get_status trả về dữ liệu trong 50 incident gần nhất và note:
"Chỉ tìm theo chuỗi trong 50 incident gần nhất; không tìm thấy không chứng minh
không có sự cố."
Final answer lại suy diễn thành "không có incident nào ... trong năm 2025".
```

Nguyên nhân có thể xảy ra: model ưu tiên trả lời trực tiếp câu hỏi lịch sử nhưng
không giữ invariant về phạm vi tìm kiếm của tool.

## 3. Thay đổi đã thực hiện

- Sửa gì, ở đâu: cập nhật `SYSTEM_PROMPT` trong `agent.py` để nhấn mạnh rằng
  không tìm thấy trong 50 incident gần nhất không chứng minh không tồn tại và
  không được tuyên bố danh sách đầy đủ theo năm.
- Vì sao chọn thay đổi này: đây là lỗi trực tiếp thể hiện trong trace và không
  cần sửa snapshot, fixtures hoặc bộ chấm.

## 4. So sánh hai lần chạy

| Chỉ số | `eval_before.json` | `eval_after.json` |
|---|---|---|
| Điểm tổng | 75.68 | 77.5 |
| Điểm case đã chọn | 45 | 75 |
| Số model calls | 52 | 47 |
| Token, chi phí nếu có | 48,726 tokens | 40,254 tokens |

- Lỗi đã giải quyết: A5 không còn khẳng định tuyệt đối rằng không có incident
  trong năm 2025; câu trả lời đã nêu giới hạn 50 incident gần nhất và không thể
  cung cấp danh sách đầy đủ.
- Lỗi còn lại hoặc mới xuất hiện: điểm từng case dao động do judge/model không
  xác định; H1, H2 và H6 vẫn cần thêm bằng chứng hoặc diễn giải tốt hơn.

## 5. Giới hạn

Điểm judge là ước lượng của một LLM và có thể dao động. Snapshot chỉ phản ánh dữ
liệu tại thời điểm ghi nhận, không đại diện cho trạng thái hiện tại. Mỗi phiên
bản chỉ chạy một lần nên chưa chứng minh độ ổn định.

## Bonus

Eval bonus lần đầu đạt 11.17/15 (10/10 case được chấm). Trace cho thấy B4 vượt
giới hạn retry Vercel và B6 không đọc tiếp incident details sau status. Đã bổ sung
quy tắc retry tối đa 3 lần, pagination và lọc dữ liệu nhiễu trong
`bonus/tools.py`; điểm bonus sau thay đổi được ghi trong `eval_bonus.json`.
