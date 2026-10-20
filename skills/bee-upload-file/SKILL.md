---
name: bee-upload-file
description: Upload một file (CSV, Excel, JSON, TXT, XML, Parquet, Avro...) từ máy người dùng vào một thư mục org file trên Bee Intelligence. Dùng khi người dùng muốn đưa/tải/upload file lên một thư mục dữ liệu (data source, org files) của Bee Intelligence.
---

# Bee Intelligence - Upload file vào thư mục

File phải được xử lý bằng script của plugin trước khi upload (chuyển Excel sang CSV, chọn sheet, chuyển mã TCVN3, thêm số dòng), giống hệt khi upload trên web. Không bao giờ tự PUT file gốc hay tự chuyển đổi file.

Tên tool của MCP `bee-intelligence` có tiền tố theo site (ví dụ `acme_get_organization_file_detail`). Dưới đây tool được gọi bằng phần đuôi tên; dùng tool có tên kết thúc bằng phần đó.

Script: `${CLAUDE_PLUGIN_ROOT}/scripts/upload-org-file.mjs`. Mỗi lệnh in đúng một dòng JSON: kết quả ra stdout, lỗi ra stderr dạng `{"error": ...}` và exit code 1.

## Chuẩn bị

1. Kiểm tra runtime: `node --version` (cần 20 trở lên), nếu không có thì `bun --version`. Nếu không có cả hai, dừng và hướng dẫn người dùng cài Node.js 20+ (https://nodejs.org). Gọi runtime tìm được là `<runtime>` ở dưới.
2. Cần hai thứ từ người dùng; thiếu cái nào thì hỏi, không đoán:
   - **Đường dẫn file** trên máy.
   - **Link thư mục** trên web Bee Intelligence, dạng `.../data-sources/org-files?id=<uuid>`. Lấy `folder_id` = tham số `id` của link. Không có tool liệt kê thư mục, nên chỉ dùng link người dùng đưa.

## Các bước

1. **Xem thư mục**: gọi `get_organization_file_detail(file_id = folder_id)`.
   - `is_folder` là false: báo đây là một file, xin link một thư mục.
   - `keep_original` là true: thư mục này lưu file nguyên gốc, còn script luôn chuyển đổi file. Dừng, không gọi các bước sau; bảo người dùng upload file đó trên web Bee Intelligence.
   - Không tìm thấy / không có quyền xem: báo người dùng; nếu thư mục thuộc công ty khác, gợi ý chuyển phạm vi đăng nhập bằng `switch_login_scope` rồi thử lại.
2. **Xử lý file**:
   ```
   <runtime> "${CLAUDE_PLUGIN_ROOT}/scripts/upload-org-file.mjs" prepare "<đường dẫn file>" [--target-sheet "<target_sheet_name>"] [--merge-all-sheets]
   ```
   - Thêm `--target-sheet` chỉ khi `target_sheet_name` khác null, và `--merge-all-sheets` chỉ khi `merge_all_sheets` là true, đúng theo kết quả bước 1. Không tự chọn sheet.
   - Kết quả: `{"path", "file_name", "file_size", "notices"}`. Ghi nhớ `path`, `file_name`, `file_size`.
   - Có `notices` thì báo lại cho người dùng (ví dụ: đã chuyển chữ TCVN3 sang Unicode, hoặc cảnh báo bảng mã cũ không hỗ trợ có thể làm lỗi chữ tiếng Việt).
   - Lỗi (định dạng không cho phép, quá 100 MB, file rỗng, chuyển đổi thất bại): báo nguyên thông báo lỗi và dừng.
   - Nếu đã truyền `--target-sheet` mà lỗi là "File is empty.", thì thường là file không có sheet đúng tên đó (phân biệt hoa thường, dấu cách). Báo người dùng thư mục yêu cầu sheet `<target_sheet_name>` và nhờ họ kiểm tra tên sheet trong file; không tự đổi tên sheet hay bỏ `--target-sheet`.
3. **Xác nhận**: hỏi người dùng xác nhận trước khi upload, nêu rõ tên file sẽ lưu (`file_name`), kích thước, và thư mục đích (`path` từ bước 1). Chỉ tiếp tục khi người dùng đồng ý.
4. **Lấy URL upload**: gọi `get_organization_file_upload_url(folder_id, file_name, file_size)` với đúng `file_name`, `file_size` của bước 2. Nhận `upload_id`, `url`, `expires_in`.
   - Lỗi 400 (định dạng/kích thước), 422 (thư mục không hợp lệ), 403 (không có quyền upload): báo lỗi và dừng.
5. **Upload**:
   ```
   <runtime> "${CLAUDE_PLUGIN_ROOT}/scripts/upload-org-file.mjs" put "<path>" "<url>"
   ```
   - Thành công: `{"ok": true}`.
   - Lỗi (403 do URL hết hạn sau khoảng 20 phút, hoặc mã khác): quay lại bước 4 lấy URL mới rồi chạy lại `put` với cùng `path`. Không chạy lại `prepare`.
6. **Hoàn tất**: gọi `complete_organization_file_upload(upload_id, description?)`. Chỉ truyền `description` nếu người dùng đưa. Kết quả có `file_id`: báo người dùng file đã vào thư mục.

## Lỗi ở bước hoàn tất

| Lỗi | Làm gì |
|---|---|
| 422 "File has not been uploaded." | Chạy lại `put` (bước 5), rồi hoàn tất lại. |
| 422 "Upload not found or expired." | Nếu file này đã nhận được `file_id` thì dừng (đã xong). Nếu chưa, quay lại bước 4 một lần duy nhất. |
| 409 "This upload is already being processed." | Không thử lại; báo người dùng kiểm tra thư mục sau. |
| Lỗi khác | Báo nguyên thông báo lỗi; file có thể đã được tạo trong thư mục, như khi upload trên web. |

## Quy tắc

- `url` là thông tin bí mật dùng một lần: không hiển thị cho người dùng, không ghi vào file, memory hay `bee_config`; chỉ truyền vào lệnh `put`.
- Mỗi lần chỉ upload một file. Nhiều file thì lặp lại các bước cho từng file, xác nhận từng file.
- Nếu không có các tool `get_organization_file_detail`, `get_organization_file_upload_url`, `complete_organization_file_upload`, báo người dùng tài khoản hiện tại không có quyền xem hoặc upload nguồn dữ liệu; không tìm cách khác.
