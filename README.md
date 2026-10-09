# BÁO CÁO BÀI TẬP LAB 7: KIỂM THỬ API VỚI POSTMAN

* **Môn học:** Kiểm thử phần mềm / Software Testing
* **Họ và tên sinh viên:** Đặng Đắc Tú
* **Mã sinh viên:** 23010619
* **Lớp:** CNTT7-k17
* **Link GitHub Repo:** https://github.com/DagTool/postman

---

## 1. Mục tiêu bài lab
- Hiểu kiến trúc RESTful API và các phương thức HTTP cơ bản: `GET`, `POST`, `PUT`, `DELETE`.
- Thành thạo việc sử dụng công cụ **Postman** để kiểm thử thủ công và tự động hóa kiểm thử API.
- Biết cách quản lý Request theo **Collection** và **Environment**.
- Viết các đoạn mã kiểm thử tự động (**Test Scripts / Assertions**) để xác minh mã trạng thái (Status code), thời gian phản hồi (Response time) và dữ liệu trả về (Body response).
- Chạy tự động toàn bộ kịch bản kiểm thử bằng **Postman Collection Runner**.

---

## 2. Công cụ và API sử dụng
- **Công cụ:** Postman (Desktop App / Web)
- **Hệ thống API mẫu:** 
  - `ReqRes API`: `https://reqres.in/` (hoặc `JSONPlaceholder`: `https://jsonplaceholder.typicode.com`)
  - Chức năng kiểm thử: Quản lý thông tin User / Sản phẩm (CRUD)

---

## 3. Các kịch bản kiểm thử (Test Cases)

| STT | Phương thức | API Endpoint | Mục đích kiểm thử | Mã phản hồi mong đợi | Test Script (Assertion) |
|-----|-------------|--------------|-------------------|----------------------|-------------------------|
| 1 | `GET` | `/api/users?page=2` | Lấy danh sách người dùng | `200 OK` | Status 200, kiểm tra danh sách có phần tử |
| 2 | `GET` | `/api/users/2` | Lấy chi tiết 1 người dùng theo ID | `200 OK` | Status 200, kiểm tra email và first_name |
| 3 | `POST` | `/api/users` | Tạo mới người dùng | `201 Created` | Status 201, kiểm tra có id và createdAt |
| 4 | `PUT` | `/api/users/2` | Cập nhật thông tin người dùng | `200 OK` | Status 200, kiểm tra updatedAt |
| 5 | `DELETE` | `/api/users/2` | Xóa người dùng | `204 No Content` | Status 204 |
| 6 | `GET` | `/api/users/23` | Kiểm tra người dùng không tồn tại | `404 Not Found` | Status 404 |

---

## 4. Kết quả thực hiện chi tiết

### 4.1. Kịch bản 1: Lấy danh sách người dùng (GET)
* **Endpoint:** `GET https://reqres.in/api/users?page=2`
* **Test script:**
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Response contains page info", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.page).to.eql(2);
    pm.expect(jsonData.data.length).to.be.above(0);
});
```
* **Hình ảnh kết quả:**
*(Chụp màn hình Postman hiển thị Params, Status 200, Response Body và tab Test Results: PASS)*
![GET List Users](![alt text](image.png))

---

### 4.2. Kịch bản 2: Lấy thông tin chi tiết người dùng (GET by ID)
* **Endpoint:** `GET https://reqres.in/api/users/2`
* **Test script:**
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("User ID and email are correct", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.data.id).to.eql(2);
    pm.expect(jsonData.data.email).to.be.a("string");
});
```
* **Hình ảnh kết quả:**
![GET User By ID](![alt text](image-1.png))

---

### 4.3. Kịch bản 3: Tạo mới người dùng (POST)
* **Endpoint:** `POST https://reqres.in/api/users`
* **Request Body (JSON):**
```json
{
    "name": "Nguyen Van A",
    "job": "QA Engineer"
}
```
* **Test script:**
```javascript
pm.test("Status code is 201 Created", function () {
    pm.response.to.have.status(201);
});

pm.test("Verify created name and job", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.name).to.eql("Nguyen Van A");
    pm.expect(jsonData.job).to.eql("QA Engineer");
    pm.expect(jsonData).to.have.property("id");
});
```
* **Hình ảnh kết quả:**
![POST Create User](![alt text](image-3.png))

---

### 4.4. Kịch bản 4: Cập nhật thông tin (PUT)
* **Endpoint:** `PUT https://reqres.in/api/users/2`
* **Request Body (JSON):**
```json
{
    "name": "Nguyen Van A Updated",
    "job": "Senior QA Engineer"
}
```
* **Test script:**
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Verify updated response", function () {
    var jsonData = pm.response.json();
    pm.expect(jsonData.name).to.eql("Nguyen Van A Updated");
    pm.expect(jsonData).to.have.property("updatedAt");
});
```
* **Hình ảnh kết quả:**
![PUT Update User](![alt text](image-4.png))

---

### 4.5. Kịch bản 5: Xóa người dùng (DELETE)
* **Endpoint:** `DELETE https://reqres.in/api/users/2`
* **Test script:**
```javascript
pm.test("Status code is 204 No Content", function () {
    pm.response.to.have.status(204);
});
```
* **Hình ảnh kết quả:**
![DELETE User](![alt text](image-5.png))

---

### 4.6. Kịch bản 6: Kiểm tra lỗi khi ID không tồn tại (Negative Test)
* **Endpoint:** `GET https://reqres.in/api/users/23`
* **Test script:**
```javascript
pm.test("Status code is 404 Not Found", function () {
    pm.response.to.have.status(404);
});
```
* **Hình ảnh kết quả:**
![GET 404 Not Found](![alt text](image-6.png))

---

### 4.7. Chạy tự động Collection Runner
* **Mô tả:** Sử dụng tính năng Collection Runner của Postman để thực thi tự động toàn bộ 6 kịch bản trên.
* **Thời gian phản hồi:** Tất cả request chạy thành công không có lỗi.
* **Hình ảnh kết quả Collection Runner:**
*(Chụp màn hình Runner summary hiển thị toàn bộ Tests PASS màu xanh lá)*
![Collection Runner](screenshots/07_collection_runner.png)

---

## 5. File đính kèm trong Repo
1. `README.md`: Báo cáo chi tiết quá trình kiểm thử và kết quả.
2. `screenshots/`: Thư mục chứa các ảnh chụp màn hình minh chứng kết quả kiểm thử.
3. `Lab7_Postman_Collection.json`: Toàn bộ Postman Collection được export từ Postman (có thể import trực tiếp vào Postman để kiểm tra lại).

---

## 6. Kết luận & Đánh giá
- Nắm vững quy trình kiểm thử API RESTful từ khâu phân tích Endpoint, gửi Request đến kiểm tra Response.
- Sử dụng thành thạo Test Scripts bằng JavaScript trong Postman để tự động hóa việc assert kết quả.
- Tận dụng Postman Runner để giảm thời gian kiểm thử hồi quy (Regression Testing).
