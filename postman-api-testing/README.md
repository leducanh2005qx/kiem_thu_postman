# Báo Cáo Kiểm Thử API với Postman

**Tác giả:** Lê Đình Đức Anh  
**Môn học / học phần:** Kiểm thử phần mềm (Software Testing)  
**Chủ đề:** Kiểm thử API RESTful và tự động hóa bằng Postman  
**Tham khảo:** [Postman Learning Center](https://learning.postman.com/docs/), [JSONPlaceholder](https://jsonplaceholder.typicode.com/), [Postman Collection v2.1 schema](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)

## I. Giới Thiệu Chung

### Mục tiêu

- Thực hành gửi và kiểm thử các thao tác CRUD trên API bằng Postman.
- Quản lý URL cơ sở bằng Environment Variable để tái sử dụng cấu hình.
- Viết assertion JavaScript để tự động xác nhận status code, thời gian phản hồi và dữ liệu trả về.
- Xuất collection và environment thành JSON để có thể import, chia sẻ và chạy lại.

### Khái niệm RESTful API

RESTful API cung cấp tài nguyên thông qua các endpoint HTTP và sử dụng các phương thức như `GET`, `POST`, `PUT`, `DELETE` để đọc, tạo, cập nhật và xóa dữ liệu. Phản hồi thường được biểu diễn bằng JSON; mã trạng thái HTTP cho biết kết quả xử lý yêu cầu.

### API mục tiêu

Bài thực hành sử dụng [JSONPlaceholder](https://jsonplaceholder.typicode.com), một API giả lập miễn phí. Các endpoint được kiểm thử là `/users/1` và `/users`. Đây là dịch vụ mô phỏng: yêu cầu tạo, cập nhật hoặc xóa có thể trả về kết quả thành công nhưng không lưu thay đổi lâu dài trên máy chủ.

## II. Các Bước Triển Khai & Kết Quả Minh Họa

### 1. Tạo Workspace & Collection

Tạo workspace trong Postman, sau đó import `postman_collection.json` ở thư mục này. Collection **User Management API Testing** gồm bốn request CRUD và các test script đi kèm.

![Workspace và Collection](images/01_workspace_collection.png)

### 2. Cấu hình Environment Variables

Import `postman_environment.json`, chọn environment **JSONPlaceholder_Env** và xác nhận biến `base_url` có giá trị `https://jsonplaceholder.typicode.com`. Các request sử dụng `{{base_url}}` để không phải lặp lại URL máy chủ.

![Environment Variables](images/02_environment_variables.png)

### 3. Chi tiết 4 requests CRUD

#### GET - Lấy thông tin người dùng

**Request:** `GET {{base_url}}/users/1`

Test script:

```javascript
pm.test('Status code is 200', function () {
    pm.response.to.have.status(200);
});

pm.test('Response time is less than 500ms', function () {
    pm.expect(pm.response.responseTime).to.be.below(500);
});

pm.test('Response has email and user id is 1', function () {
    const user = pm.response.json();
    pm.expect(user).to.have.property('email');
    pm.expect(user.id).to.eql(1);
});
```

Các assertion xác nhận status `200`, thời gian phản hồi dưới `500ms`, response có thuộc tính `email` và `id` bằng `1`.

![GET request](images/03_get_request.png)

#### POST - Tạo người dùng

**Request:** `POST {{base_url}}/users`  
**Header:** `Content-Type: application/json`

Body:

```json
{
  "name": "Le Dinh Duc Anh",
  "username": "ducanh",
  "email": "ducanh@example.com"
}
```

Test script:

```javascript
pm.test('Status code is 201', function () {
    pm.response.to.have.status(201);
});

pm.test('Created user has the expected name', function () {
    const user = pm.response.json();
    pm.expect(user.name).to.eql('Le Dinh Duc Anh');
});
```

![POST request](images/04_post_request.png)

#### PUT - Cập nhật người dùng

**Request:** `PUT {{base_url}}/users/1`  
**Header:** `Content-Type: application/json`

Body:

```json
{
  "id": 1,
  "name": "Le Dinh Duc Anh (Updated)",
  "email": "ducanh.updated@example.com"
}
```

Test script:

```javascript
pm.test('Status code is 200', function () {
    pm.response.to.have.status(200);
});

pm.test('Updated user has the expected name', function () {
    const user = pm.response.json();
    pm.expect(user.name).to.eql('Le Dinh Duc Anh (Updated)');
});
```

![PUT request](images/05_put_request.png)

#### DELETE - Xóa người dùng

**Request:** `DELETE {{base_url}}/users/1`

Test script:

```javascript
pm.test('Status code is 200', function () {
    pm.response.to.have.status(200);
});
```

![DELETE request](images/06_delete_request.png)

### 4. Collection Runner & Kết quả kiểm thử tự động

Trong Postman, chọn collection **User Management API Testing**, chọn environment **JSONPlaceholder_Env**, rồi chạy bằng **Run**. Collection Runner thực thi tuần tự bốn request và tổng hợp trạng thái từng assertion. Lưu ảnh chụp màn hình kết quả chạy tại đường dẫn bên dưới để hoàn thiện báo cáo.

![Collection Runner và kết quả kiểm thử](images/07_test_runner.png)

### Khởi tạo repository GitHub

Từ thư mục `postman-api-testing`, chạy các lệnh sau. Thay `<YOUR_GITHUB_REPOSITORY_URL>` bằng URL repository GitHub đã tạo:

```sh
git init
git add .
git commit -m "feat: complete postman testing report, collection configs, and docs"
git branch -M main
git remote add origin <YOUR_GITHUB_REPOSITORY_URL>
git push -u origin main
```

Nếu `origin` đã được cấu hình, cập nhật URL trước khi push:

```sh
git remote set-url origin <YOUR_GITHUB_REPOSITORY_URL>
git push -u origin main
```

## III. Đánh Giá & Kết Luận

Bài thực hành giúp củng cố cách thiết kế request theo phương thức HTTP, sử dụng biến môi trường để cấu hình endpoint và viết assertion với Postman Sandbox. Collection và environment dạng JSON giúp bộ kiểm thử có thể tái sử dụng, chia sẻ và chạy tự động bằng Collection Runner.

Assertion tự động kiểm tra kết quả nhất quán sau mỗi lần chạy, giảm thao tác đối chiếu thủ công và phát hiện hồi quy nhanh hơn. Kiểm tra bằng mắt vẫn hữu ích khi đánh giá nội dung hoặc trải nghiệm không được assertion bao phủ; do đó hai cách bổ trợ cho nhau. Với API giả lập như JSONPlaceholder, kết quả xác nhận contract của endpoint trong lần gọi, không chứng minh dữ liệu đã được lưu bền vững.
