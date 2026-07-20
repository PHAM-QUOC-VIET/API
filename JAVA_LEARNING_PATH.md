# 🚀 Lộ Trình Học API với Java - Từ Cơ Bản Đến Nâng Cao

## 📚 Mục Lục
1. [Cơ Bản](#cơ-bản)
2. [Hướng Dẫn Chi Tiết](#hướng-dẫn-chi-tiết)
3. [Ví Dụ Code Cụ Thể](#ví-dụ-code-cụ-thể)
4. [Bài Tập Thực Hành](#bài-tập-thực-hành)
5. [Cách Kiểm Thử API](#cách-kiểm-thử-api)

---

## 📖 Cơ Bản

### API là gì?
**API (Application Programming Interface)** là một tập hợp các quy tắc và giao thức cho phép các ứng dụng giao tiếp với nhau. Trong Java, bạn sẽ làm việc với HTTP APIs sử dụng REST (Representational State Transfer).

### Các HTTP Methods cơ bản:
- **GET**: Lấy dữ liệu từ server
- **POST**: Gửi dữ liệu đến server
- **PUT**: Cập nhật toàn bộ tài nguyên
- **DELETE**: Xóa tài nguyên
- **PATCH**: Cập nhật một phần tài nguyên

### HTTP Status Codes quan trọng:
- **200 OK**: Yêu cầu thành công
- **201 Created**: Tài nguyên được tạo thành công
- **400 Bad Request**: Yêu cầu không hợp lệ
- **401 Unauthorized**: Không được phép
- **404 Not Found**: Không tìm thấy tài nguyên
- **500 Internal Server Error**: Lỗi server

---

## 🎓 Hướng Dẫn Chi Tiết

### Giai Đoạn 1: Thiết Lập Môi Trường (Tuần 1)

#### Yêu Cầu Hệ Thống:
- Java Development Kit (JDK) 11 hoặc cao hơn
- Maven hoặc Gradle
- IDE: IntelliJ IDEA hoặc Eclipse
- Postman (để test API)

#### Cài Đặt Maven Dependencies:
```xml
<dependencies>
    <!-- HTTP Client -->
    <dependency>
        <groupId>org.apache.httpcomponents.client5</groupId>
        <artifactId>httpclient5</artifactId>
        <version>5.2.1</version>
    </dependency>
    
    <!-- JSON Processing -->
    <dependency>
        <groupId>com.google.code.gson</groupId>
        <artifactId>gson</artifactId>
        <version>2.10.1</version>
    </dependency>
    
    <!-- Logging -->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
        <version>2.0.5</version>
    </dependency>
</dependencies>
```

---

### Giai Đoạn 2: Gọi API Cơ Bản (Tuần 2-3)

#### Hiểu GET Request:
- GET request được sử dụng để lấy dữ liệu
- Không có body trong request
- Tham số được truyền qua URL query string

#### Hiểu POST Request:
- POST được sử dụng để tạo tài nguyên mới
- Dữ liệu được gửi trong request body
- Thường trả về 201 Created

#### Xử Lý JSON:
- JSON là định dạng phổ biến nhất để trao đổi dữ liệu
- Sử dụng Gson để parse/convert JSON

---

### Giai Đoạn 3: Error Handling & Best Practices (Tuần 4-5)

#### Xử Lý Lỗi:
- Kiểm tra status code
- Try-catch để bắt exceptions
- Log lỗi chi tiết

#### Best Practices:
- Sử dụng constants cho URLs
- Tách business logic khỏi HTTP logic
- Sử dụng Configuration files
- Implement retry logic
- Validate input trước khi gửi

---

## 💻 Ví Dụ Code Cụ Thể

### Ví Dụ 1: GET Request Đơn Giản

```java
import org.apache.hc.client5.http.classic.methods.HttpGet;
import org.apache.hc.client5.http.impl.classic.CloseableHttpClient;
import org.apache.hc.client5.http.impl.classic.HttpClients;
import org.apache.hc.core5.http.ClassicHttpResponse;
import java.io.IOException;
import java.util.Scanner;

public class SimpleGetExample {
    
    public static void main(String[] args) {
        String apiUrl = "https://jsonplaceholder.typicode.com/posts/1";
        getRequest(apiUrl);
    }
    
    public static void getRequest(String url) {
        try (CloseableHttpClient httpClient = HttpClients.createDefault()) {
            HttpGet request = new HttpGet(url);
            request.setHeader("Accept", "application/json");
            
            ClassicHttpResponse response = httpClient.executeOpen(null, request, null);
            
            // Kiểm tra status code
            int statusCode = response.getCode();
            System.out.println("Status Code: " + statusCode);
            
            if (statusCode == 200) {
                String responseBody = new Scanner(
                    response.getEntity().getContent()
                ).useDelimiter("\\A").next();
                System.out.println("Response: " + responseBody);
            } else {
                System.out.println("Error: " + statusCode);
            }
            
        } catch (IOException e) {
            System.err.println("Error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

**Giải Thích:**
- Tạo HttpGet object với URL
- Set header Accept = application/json
- Execute request
- Kiểm tra status code
- Đọc response body

---

### Ví Dụ 2: POST Request với JSON Body

```java
import org.apache.hc.client5.http.classic.methods.HttpPost;
import org.apache.hc.client5.http.impl.classic.CloseableHttpClient;
import org.apache.hc.client5.http.impl.classic.HttpClients;
import org.apache.hc.core5.http.ClassicHttpResponse;
import org.apache.hc.core5.http.ContentType;
import org.apache.hc.core5.http.io.entity.StringEntity;
import com.google.gson.Gson;
import com.google.gson.JsonObject;

public class PostRequestExample {
    
    public static void main(String[] args) {
        String apiUrl = "https://jsonplaceholder.typicode.com/posts";
        postRequest(apiUrl);
    }
    
    public static void postRequest(String url) {
        try (CloseableHttpClient httpClient = HttpClients.createDefault()) {
            HttpPost request = new HttpPost(url);
            
            // Tạo JSON body
            JsonObject jsonBody = new JsonObject();
            jsonBody.addProperty("title", "Bài viết mới");
            jsonBody.addProperty("body", "Đây là nội dung bài viết");
            jsonBody.addProperty("userId", 1);
            
            // Set headers
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Accept", "application/json");
            
            // Set body
            StringEntity entity = new StringEntity(
                jsonBody.toString(), 
                ContentType.APPLICATION_JSON
            );
            request.setEntity(entity);
            
            // Execute
            ClassicHttpResponse response = httpClient.executeOpen(null, request, null);
            
            int statusCode = response.getCode();
            System.out.println("Status Code: " + statusCode);
            
            if (statusCode == 201) {
                String responseBody = new Scanner(
                    response.getEntity().getContent()
                ).useDelimiter("\\A").next();
                System.out.println("Created: " + responseBody);
            } else {
                System.out.println("Error: " + statusCode);
            }
            
        } catch (IOException e) {
            System.err.println("Error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

**Giải Thích:**
- Tạo JsonObject để chứa dữ liệu
- Set Content-Type header
- Set StringEntity với JSON body
- POST lên server
- Xử lý response

---

### Ví Dụ 3: Tạo API Client Class (Best Practice)

```java
import org.apache.hc.client5.http.classic.methods.*;
import org.apache.hc.client5.http.impl.classic.CloseableHttpClient;
import org.apache.hc.client5.http.impl.classic.HttpClients;
import org.apache.hc.core5.http.ClassicHttpResponse;
import org.apache.hc.core5.http.ContentType;
import org.apache.hc.core5.http.io.entity.StringEntity;
import com.google.gson.JsonObject;
import java.io.IOException;
import java.util.Scanner;

public class ApiClient {
    
    private static final String BASE_URL = "https://jsonplaceholder.typicode.com";
    private CloseableHttpClient httpClient;
    
    public ApiClient() {
        this.httpClient = HttpClients.createDefault();
    }
    
    // GET request
    public String get(String endpoint) throws IOException {
        String url = BASE_URL + endpoint;
        HttpGet request = new HttpGet(url);
        request.setHeader("Accept", "application/json");
        
        ClassicHttpResponse response = httpClient.executeOpen(null, request, null);
        int statusCode = response.getCode();
        
        if (statusCode != 200) {
            throw new IOException("GET failed with status: " + statusCode);
        }
        
        return readResponse(response);
    }
    
    // POST request
    public String post(String endpoint, JsonObject body) throws IOException {
        String url = BASE_URL + endpoint;
        HttpPost request = new HttpPost(url);
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Accept", "application/json");
        
        StringEntity entity = new StringEntity(
            body.toString(), 
            ContentType.APPLICATION_JSON
        );
        request.setEntity(entity);
        
        ClassicHttpResponse response = httpClient.executeOpen(null, request, null);
        int statusCode = response.getCode();
        
        if (statusCode != 201 && statusCode != 200) {
            throw new IOException("POST failed with status: " + statusCode);
        }
        
        return readResponse(response);
    }
    
    // PUT request
    public String put(String endpoint, JsonObject body) throws IOException {
        String url = BASE_URL + endpoint;
        HttpPut request = new HttpPut(url);
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Accept", "application/json");
        
        StringEntity entity = new StringEntity(
            body.toString(), 
            ContentType.APPLICATION_JSON
        );
        request.setEntity(entity);
        
        ClassicHttpResponse response = httpClient.executeOpen(null, request, null);
        int statusCode = response.getCode();
        
        if (statusCode != 200) {
            throw new IOException("PUT failed with status: " + statusCode);
        }
        
        return readResponse(response);
    }
    
    // DELETE request
    public void delete(String endpoint) throws IOException {
        String url = BASE_URL + endpoint;
        HttpDelete request = new HttpDelete(url);
        
        ClassicHttpResponse response = httpClient.executeOpen(null, request, null);
        int statusCode = response.getCode();
        
        if (statusCode != 200 && statusCode != 204) {
            throw new IOException("DELETE failed with status: " + statusCode);
        }
    }
    
    // Helper method để đọc response
    private String readResponse(ClassicHttpResponse response) throws IOException {
        return new Scanner(
            response.getEntity().getContent()
        ).useDelimiter("\\A").next();
    }
    
    public void close() throws IOException {
        httpClient.close();
    }
}
```

---

### Ví Dụ 4: Sử Dụng API Client

```java
import com.google.gson.JsonObject;

public class ApiClientUsageExample {
    
    public static void main(String[] args) {
        ApiClient client = new ApiClient();
        
        try {
            // GET request
            String posts = client.get("/posts/1");
            System.out.println("GET /posts/1: " + posts);
            
            // POST request
            JsonObject newPost = new JsonObject();
            newPost.addProperty("title", "Bài viết test");
            newPost.addProperty("body", "Nội dung test");
            newPost.addProperty("userId", 1);
            
            String created = client.post("/posts", newPost);
            System.out.println("POST /posts: " + created);
            
            // PUT request
            JsonObject updatedPost = new JsonObject();
            updatedPost.addProperty("title", "Bài viết cập nhật");
            updatedPost.addProperty("body", "Nội dung cập nhật");
            updatedPost.addProperty("userId", 1);
            updatedPost.addProperty("id", 1);
            
            String updated = client.put("/posts/1", updatedPost);
            System.out.println("PUT /posts/1: " + updated);
            
            // DELETE request
            client.delete("/posts/1");
            System.out.println("DELETE /posts/1: Success");
            
        } catch (Exception e) {
            System.err.println("Error: " + e.getMessage());
            e.printStackTrace();
        } finally {
            try {
                client.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

---

## 🏋️ Bài Tập Thực Hành

### Bài Tập 1: Lấy danh sách người dùng (Mức độ: Dễ)

**Yêu cầu:**
- Sử dụng API: `https://jsonplaceholder.typicode.com/users`
- Lấy danh sách tất cả người dùng
- In ra ID, name, email của mỗi người dùng

**Hướng dẫn:**
```java
// 1. Tạo GET request đến /users
// 2. Parse JSON response thành array
// 3. Lặp qua từng user và in thông tin
```

---

### Bài Tập 2: Tạo bài viết mới (Mức độ: Trung bình)

**Yêu cầu:**
- Tạo POST request đến `https://jsonplaceholder.typicode.com/posts`
- Gửi dữ liệu: title, body, userId
- In ra ID của bài viết được tạo
- Kiểm tra status code

**Hướng dẫn:**
```java
// 1. Tạo JsonObject với title, body, userId
// 2. POST đến /posts
// 3. Check status = 201
// 4. Parse response và in ID
```

---

### Bài Tập 3: Cập nhật bài viết (Mức độ: Trung bình)

**Yêu cầu:**
- Cập nhật bài viết ID 1 với title mới
- Sử dụng PUT request
- Kiểm tra response

**Hướng dẫn:**
```java
// 1. Tạo JsonObject với dữ liệu mới
// 2. PUT đến /posts/1
// 3. Hiển thị dữ liệu cập nhật
```

---

### Bài Tập 4: Xóa bài viết (Mức độ: Dễ)

**Yêu cầu:**
- Xóa bài viết ID 1
- Kiểm tra status code = 200
- In ra thông báo thành công

---

### Bài Tập 5: Xây dựng ứng dụng quản lý Todo (Mức độ: Nâng cao)

**Yêu cầu:**
- Lấy danh sách todos từ `/todos`
- Thêm todo mới
- Cập nhật todo
- Xóa todo
- Implement error handling và logging

**Gợi ý:**
```java
public class TodoManager {
    private ApiClient client;
    
    // Lấy tất cả todos
    public List<Todo> getAllTodos() { }
    
    // Lấy todo theo ID
    public Todo getTodoById(int id) { }
    
    // Tạo todo mới
    public Todo createTodo(String title, int userId) { }
    
    // Cập nhật todo
    public Todo updateTodo(int id, String title) { }
    
    // Xóa todo
    public void deleteTodo(int id) { }
}
```

---

## 🧪 Cách Kiểm Thử API

### 1. Kiểm Thử bằng Postman (GUI)

**Các bước:**
1. Tải Postman: https://www.postman.com/downloads/
2. Tạo request mới
3. Chọn HTTP method (GET, POST, v.v.)
4. Nhập URL
5. Nếu POST/PUT: vào tab Body > Raw > chọn JSON
6. Nhập JSON payload
7. Click Send

**Ví dụ POST request:**
```
Method: POST
URL: https://jsonplaceholder.typicode.com/posts
Headers: Content-Type: application/json
Body: {
  "title": "Test title",
  "body": "Test body",
  "userId": 1
}
```

---

### 2. Kiểm Thử bằng cURL (Terminal)

```bash
# GET request
curl https://jsonplaceholder.typicode.com/posts/1

# POST request
curl -X POST https://jsonplaceholder.typicode.com/posts \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Test title",
    "body": "Test body",
    "userId": 1
  }'

# PUT request
curl -X PUT https://jsonplaceholder.typicode.com/posts/1 \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Updated title",
    "body": "Updated body",
    "userId": 1
  }'

# DELETE request
curl -X DELETE https://jsonplaceholder.typicode.com/posts/1
```

---

### 3. Unit Test với JUnit

```java
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class ApiClientTest {
    
    private ApiClient apiClient;
    
    @BeforeEach
    public void setUp() {
        apiClient = new ApiClient();
    }
    
    @Test
    public void testGetRequest_Success() throws Exception {
        String response = apiClient.get("/posts/1");
        assertNotNull(response);
        assertTrue(response.contains("id"));
    }
    
    @Test
    public void testPostRequest_Success() throws Exception {
        JsonObject body = new JsonObject();
        body.addProperty("title", "Test");
        body.addProperty("body", "Test body");
        body.addProperty("userId", 1);
        
        String response = apiClient.post("/posts", body);
        assertNotNull(response);
        assertTrue(response.contains("id"));
    }
    
    @Test
    public void testDeleteRequest_Success() throws Exception {
        assertDoesNotThrow(() -> apiClient.delete("/posts/1"));
    }
}
```

---

### 4. Checklist Kiểm Thử

- [ ] Kiểm tra status code (200, 201, 400, 404, 500)
- [ ] Kiểm tra response body có đúng dữ liệu
- [ ] Kiểm tra response headers
- [ ] Kiểm tra lỗi khi request không hợp lệ
- [ ] Kiểm tra lỗi khi server không phản hồi
- [ ] Kiểm tra timeout
- [ ] Kiểm tra error handling

---

## 📝 Tóm Tắt

**Các kiến thức chính:**
1. ✅ HTTP Methods cơ bản (GET, POST, PUT, DELETE)
2. ✅ JSON parsing và xử lý
3. ✅ Error handling
4. ✅ Tạo API client reusable
5. ✅ Testing API

**Tiếp theo:**
- Học về Authentication (API Keys, JWT)
- Rate limiting
- Async API calls
- WebSocket communication

Happy Learning! 🎉
