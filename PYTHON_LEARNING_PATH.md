# 🚀 Lộ Trình Học API với Python - Từ Cơ Bản Đến Nâng Cao

## 📚 Mục Lục
1. [Cơ Bản](#cơ-bản)
2. [Hướng Dẫn Chi Tiết](#hướng-dẫn-chi-tiết)
3. [Ví Dụ Code Cụ Thể](#ví-dụ-code-cụ-thể)
4. [Bài Tập Thực Hành](#bài-tập-thực-hành)
5. [Cách Kiểm Thử API](#cách-kiểm-thử-api)

---

## 📖 Cơ Bản

### API là gì?
**API (Application Programming Interface)** là một tập hợp các quy tắc và giao thức cho phép các ứng dụng giao tiếp với nhau. Trong Python, bạn sẽ làm việc với HTTP APIs sử dụng REST (Representational State Transfer).

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
- Python 3.7 hoặc cao hơn
- pip (package manager)
- Text Editor hoặc IDE: VS Code, PyCharm
- Postman (để test API)

#### Cài Đặt Required Libraries:

```bash
# Cài đặt requests library
pip install requests

# Cài đặt requests-json-schema để validate JSON
pip install requests-json-schema

# Cài đặt python-dotenv để quản lý environment variables
pip install python-dotenv

# Cài đặt pytest để viết unit tests
pip install pytest

# Cài đặt pytest-mock để mock API calls
pip install pytest-mock
```

#### File requirements.txt:
```txt
requests==2.31.0
python-dotenv==1.0.0
pytest==7.4.0
pytest-mock==3.11.1
```

#### Cài từ requirements.txt:
```bash
pip install -r requirements.txt
```

---

### Giai Đoạn 2: Gọi API Cơ Bản (Tuần 2-3)

#### Hiểu GET Request:
- GET request được sử dụng để lấy dữ liệu
- Không có body trong request
- Tham số được truyền qua URL query string
- Response là JSON, CSV, HTML, v.v.

#### Hiểu POST Request:
- POST được sử dụng để tạo tài nguyên mới
- Dữ liệu được gửi trong request body dưới dạng JSON
- Thường trả về 201 Created
- Có thể gửi dữ liệu form encoded hoặc JSON

#### Xử Lý JSON:
- Python có built-in `json` module
- Requests library tự động parse JSON
- Sử dụng `.json()` method để chuyển đổi

---

### Giai Đoạn 3: Error Handling & Best Practices (Tuần 4-5)

#### Xử Lý Lỗi:
- Kiểm tra status code bằng `response.status_code`
- Sử dụng `try-except` để bắt exceptions
- `raise_for_status()` để tự động raise exception nếu status != 2xx
- Log lỗi chi tiết

#### Best Practices:
- Sử dụng constants/config cho URLs
- Tách business logic khỏi HTTP logic
- Sử dụng environment variables cho sensitive data
- Implement retry logic với exponential backoff
- Validate input trước khi gửi
- Set timeout cho requests

---

## 💻 Ví Dụ Code Cụ Thể

### Ví Dụ 1: GET Request Đơn Giản

```python
import requests

def simple_get_request():
    """Lấy dữ liệu từ API đơn giản"""
    
    url = "https://jsonplaceholder.typicode.com/posts/1"
    
    try:
        # Gửi GET request
        response = requests.get(url)
        
        # Kiểm tra status code
        print(f"Status Code: {response.status_code}")
        
        # Nếu thành công (200)
        if response.status_code == 200:
            # Parse JSON response
            data = response.json()
            print(f"Response: {data}")
        else:
            print(f"Error: {response.status_code}")
            
    except requests.exceptions.RequestException as e:
        print(f"Error: {e}")

if __name__ == "__main__":
    simple_get_request()
```

**Giải Thích:**
- `requests.get()` gửi GET request
- `response.status_code` lấy HTTP status code
- `response.json()` tự động parse JSON

---

### Ví Dụ 2: POST Request với JSON Body

```python
import requests
import json

def post_request():
    """Tạo bài viết mới bằng POST request"""
    
    url = "https://jsonplaceholder.typicode.com/posts"
    
    # Dữ liệu muốn gửi
    payload = {
        "title": "Bài viết mới",
        "body": "Đây là nội dung bài viết",
        "userId": 1
    }
    
    # Headers
    headers = {
        "Content-Type": "application/json",
        "Accept": "application/json"
    }
    
    try:
        # Gửi POST request
        response = requests.post(url, json=payload, headers=headers)
        
        print(f"Status Code: {response.status_code}")
        
        # Nếu tạo thành công (201)
        if response.status_code == 201:
            data = response.json()
            print(f"Created: {data}")
            print(f"ID của bài viết mới: {data.get('id')}")
        else:
            print(f"Error: {response.status_code}")
            
    except requests.exceptions.RequestException as e:
        print(f"Error: {e}")

if __name__ == "__main__":
    post_request()
```

**Giải Thích:**
- `requests.post()` gửi POST request
- `json=payload` tự động convert dict thành JSON
- `json=` tự động set Content-Type header

---

### Ví Dụ 3: GET Request với Query Parameters

```python
import requests

def get_with_params():
    """GET request với query parameters"""
    
    url = "https://jsonplaceholder.typicode.com/posts"
    
    # Query parameters
    params = {
        "userId": 1,
        "_limit": 5  # Giới hạn 5 posts
    }
    
    try:
        response = requests.get(url, params=params)
        
        print(f"Status Code: {response.status_code}")
        print(f"Actual URL: {response.url}")  # In URL thực tế
        
        if response.status_code == 200:
            posts = response.json()
            print(f"Số bài viết: {len(posts)}")
            for post in posts:
                print(f"  - ID: {post['id']}, Title: {post['title']}")
        else:
            print(f"Error: {response.status_code}")
            
    except requests.exceptions.RequestException as e:
        print(f"Error: {e}")

if __name__ == "__main__":
    get_with_params()
```

**Giải Thích:**
- `params` dictionary được tự động convert thành query string
- `response.url` để xem URL thực tế được gửi

---

### Ví Dụ 4: PUT Request (Cập Nhật)

```python
import requests

def put_request():
    """Cập nhật bài viết bằng PUT"""
    
    url = "https://jsonplaceholder.typicode.com/posts/1"
    
    payload = {
        "id": 1,
        "title": "Bài viết cập nhật",
        "body": "Nội dung cập nhật",
        "userId": 1
    }
    
    headers = {"Content-Type": "application/json"}
    
    try:
        response = requests.put(url, json=payload, headers=headers)
        
        print(f"Status Code: {response.status_code}")
        
        if response.status_code == 200:
            data = response.json()
            print(f"Updated: {data}")
        else:
            print(f"Error: {response.status_code}")
            
    except requests.exceptions.RequestException as e:
        print(f"Error: {e}")

if __name__ == "__main__":
    put_request()
```

---

### Ví Dụ 5: DELETE Request

```python
import requests

def delete_request():
    """Xóa bài viết bằng DELETE"""
    
    url = "https://jsonplaceholder.typicode.com/posts/1"
    
    try:
        response = requests.delete(url)
        
        print(f"Status Code: {response.status_code}")
        
        if response.status_code == 200:
            print("Bài viết đã được xóa thành công")
        else:
            print(f"Error: {response.status_code}")
            
    except requests.exceptions.RequestException as e:
        print(f"Error: {e}")

if __name__ == "__main__":
    delete_request()
```

---

### Ví Dụ 6: API Client Class (Best Practice)

```python
import requests
import logging
from typing import Dict, Any, Optional
import time

# Cấu hình logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

class ApiClient:
    """
    Client để gọi API với error handling và best practices
    """
    
    def __init__(self, base_url: str, timeout: int = 10):
        """
        Khởi tạo API client
        
        Args:
            base_url: URL cơ sở của API (ví dụ: https://api.example.com)
            timeout: Timeout cho mỗi request (giây)
        """
        self.base_url = base_url
        self.timeout = timeout
        self.session = requests.Session()
    
    def _build_url(self, endpoint: str) -> str:
        """Xây dựng full URL từ endpoint"""
        return f"{self.base_url}{endpoint}"
    
    def _handle_response(self, response: requests.Response, expected_status: int = 200) -> Dict[str, Any]:
        """
        Xử lý response từ API
        
        Args:
            response: Response object từ requests
            expected_status: Expected HTTP status code
            
        Returns:
            JSON data từ response
            
        Raises:
            requests.exceptions.HTTPError: Nếu status code không mong muốn
        """
        logger.info(f"Response Status: {response.status_code}")
        
        if response.status_code != expected_status:
            logger.error(f"Expected {expected_status}, got {response.status_code}")
            response.raise_for_status()
        
        try:
            return response.json()
        except ValueError:
            logger.warning("Response không phải JSON")
            return {"text": response.text}
    
    def get(self, endpoint: str, params: Optional[Dict] = None) -> Dict[str, Any]:
        """GET request"""
        url = self._build_url(endpoint)
        logger.info(f"GET {url}")
        
        try:
            response = self.session.get(url, params=params, timeout=self.timeout)
            return self._handle_response(response, expected_status=200)
        except requests.exceptions.RequestException as e:
            logger.error(f"GET request failed: {e}")
            raise
    
    def post(self, endpoint: str, json_data: Dict[str, Any]) -> Dict[str, Any]:
        """POST request"""
        url = self._build_url(endpoint)
        logger.info(f"POST {url}")
        
        try:
            response = self.session.post(url, json=json_data, timeout=self.timeout)
            return self._handle_response(response, expected_status=201)
        except requests.exceptions.RequestException as e:
            logger.error(f"POST request failed: {e}")
            raise
    
    def put(self, endpoint: str, json_data: Dict[str, Any]) -> Dict[str, Any]:
        """PUT request"""
        url = self._build_url(endpoint)
        logger.info(f"PUT {url}")
        
        try:
            response = self.session.put(url, json=json_data, timeout=self.timeout)
            return self._handle_response(response, expected_status=200)
        except requests.exceptions.RequestException as e:
            logger.error(f"PUT request failed: {e}")
            raise
    
    def delete(self, endpoint: str) -> bool:
        """DELETE request"""
        url = self._build_url(endpoint)
        logger.info(f"DELETE {url}")
        
        try:
            response = self.session.delete(url, timeout=self.timeout)
            if response.status_code not in [200, 204]:
                response.raise_for_status()
            return True
        except requests.exceptions.RequestException as e:
            logger.error(f"DELETE request failed: {e}")
            raise
    
    def close(self):
        """Đóng session"""
        self.session.close()
    
    def __enter__(self):
        """Context manager - enter"""
        return self
    
    def __exit__(self, exc_type, exc_val, exc_tb):
        """Context manager - exit"""
        self.close()

# Ví dụ sử dụng
if __name__ == "__main__":
    with ApiClient("https://jsonplaceholder.typicode.com") as client:
        
        # GET
        post = client.get("/posts/1")
        print(f"GET: {post}\n")
        
        # POST
        new_post = {
            "title": "Test",
            "body": "Test body",
            "userId": 1
        }
        created = client.post("/posts", new_post)
        print(f"POST: {created}\n")
        
        # PUT
        updated_post = {
            "title": "Updated",
            "body": "Updated body",
            "userId": 1,
            "id": 1
        }
        updated = client.put("/posts/1", updated_post)
        print(f"PUT: {updated}\n")
        
        # DELETE
        client.delete("/posts/1")
        print("DELETE: Success\n")
```

---

### Ví Dụ 7: Retry Logic với Exponential Backoff

```python
import requests
import time
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

def create_session_with_retries(retries: int = 3, backoff_factor: float = 0.5):
    """
    Tạo requests session với automatic retry
    
    Args:
        retries: Số lần retry
        backoff_factor: Backoff factor cho exponential backoff
    """
    session = requests.Session()
    
    # Cấu hình retry strategy
    retry_strategy = Retry(
        total=retries,
        backoff_factor=backoff_factor,
        status_forcelist=[429, 500, 502, 503, 504],
        allowed_methods=["GET", "POST", "PUT", "DELETE"]
    )
    
    # Mount HTTPAdapter với retry strategy
    adapter = HTTPAdapter(max_retries=retry_strategy)
    session.mount("https://", adapter)
    session.mount("http://", adapter)
    
    return session

# Ví dụ sử dụng
if __name__ == "__main__":
    session = create_session_with_retries()
    
    try:
        response = session.get("https://jsonplaceholder.typicode.com/posts/1")
        print(f"Status: {response.status_code}")
        print(f"Data: {response.json()}")
    finally:
        session.close()
```

---

## 🏋️ Bài Tập Thực Hành

### Bài Tập 1: Lấy danh sách người dùng (Mức độ: Dễ)

**Yêu cầu:**
- Sử dụng API: `https://jsonplaceholder.typicode.com/users`
- Lấy danh sách tất cả người dùng
- In ra ID, name, email của mỗi người dùng

**Gợi ý Code:**
```python
import requests

def get_users():
    url = "https://jsonplaceholder.typicode.com/users"
    response = requests.get(url)
    
    if response.status_code == 200:
        users = response.json()
        for user in users:
            print(f"ID: {user['id']}, Name: {user['name']}, Email: {user['email']}")
    else:
        print(f"Error: {response.status_code}")

if __name__ == "__main__":
    get_users()
```

---

### Bài Tập 2: Tạo bài viết mới (Mức độ: Trung bình)

**Yêu cầu:**
- Tạo POST request đến `https://jsonplaceholder.typicode.com/posts`
- Gửi dữ liệu: title, body, userId
- In ra ID của bài viết được tạo
- Kiểm tra status code

**Gợi ý Code:**
```python
import requests

def create_post():
    url = "https://jsonplaceholder.typicode.com/posts"
    payload = {
        "title": "My First Post",
        "body": "This is my first post",
        "userId": 1
    }
    
    response = requests.post(url, json=payload)
    
    if response.status_code == 201:
        data = response.json()
        print(f"Post created with ID: {data['id']}")
    else:
        print(f"Error: {response.status_code}")

if __name__ == "__main__":
    create_post()
```

---

### Bài Tập 3: Cập nhật bài viết (Mức độ: Trung bình)

**Yêu cầu:**
- Cập nhật bài viết ID 1 với title mới
- Sử dụng PUT request
- Kiểm tra response

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
```python
class TodoManager:
    def __init__(self, base_url: str):
        self.base_url = base_url
    
    def get_all_todos(self):
        """Lấy tất cả todos"""
        pass
    
    def get_todo(self, todo_id: int):
        """Lấy todo theo ID"""
        pass
    
    def create_todo(self, title: str, user_id: int):
        """Tạo todo mới"""
        pass
    
    def update_todo(self, todo_id: int, title: str, completed: bool):
        """Cập nhật todo"""
        pass
    
    def delete_todo(self, todo_id: int):
        """Xóa todo"""
        pass
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

### 3. Unit Test với Pytest

**File: test_api_client.py**

```python
import pytest
import requests
from unittest.mock import patch, MagicMock
from api_client import ApiClient

class TestApiClient:
    
    @pytest.fixture
    def client(self):
        """Fixture để tạo API client"""
        return ApiClient("https://jsonplaceholder.typicode.com")
    
    def test_get_success(self, client):
        """Test GET request thành công"""
        response = client.get("/posts/1")
        assert response is not None
        assert "id" in response
    
    @patch('requests.Session.get')
    def test_get_with_mock(self, mock_get, client):
        """Test GET request với mock"""
        mock_response = MagicMock()
        mock_response.status_code = 200
        mock_response.json.return_value = {"id": 1, "title": "Test"}
        mock_get.return_value = mock_response
        
        response = client.get("/posts/1")
        assert response["id"] == 1
        assert response["title"] == "Test"
    
    def test_post_success(self, client):
        """Test POST request thành công"""
        payload = {
            "title": "Test",
            "body": "Test body",
            "userId": 1
        }
        response = client.post("/posts", payload)
        assert response is not None
        assert "id" in response
    
    def test_delete_success(self, client):
        """Test DELETE request thành công"""
        result = client.delete("/posts/1")
        assert result is True
    
    def test_invalid_endpoint(self, client):
        """Test request đến endpoint không tồn tại"""
        with pytest.raises(requests.exceptions.HTTPError):
            client.get("/invalid/endpoint")
```

**Chạy test:**
```bash
# Chạy tất cả tests
pytest test_api_client.py

# Chạy test cụ thể
pytest test_api_client.py::TestApiClient::test_get_success

# Chạy với verbose output
pytest test_api_client.py -v

# Chạy với coverage report
pytest test_api_client.py --cov=api_client
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
- [ ] Kiểm tra retry logic

---

### 5. Test Performance

```python
import time
import requests

def test_api_performance():
    """Test API response time"""
    url = "https://jsonplaceholder.typicode.com/posts/1"
    
    start_time = time.time()
    response = requests.get(url)
    end_time = time.time()
    
    response_time = end_time - start_time
    print(f"Response time: {response_time:.2f} seconds")
    
    # Assert response time < 1 second
    assert response_time < 1.0, f"Response time too slow: {response_time}s"

if __name__ == "__main__":
    test_api_performance()
```

---

## 📝 Tóm Tắt

**Các kiến thức chính:**
1. ✅ HTTP Methods cơ bản (GET, POST, PUT, DELETE)
2. ✅ JSON parsing và xử lý
3. ✅ Error handling và logging
4. ✅ Tạo reusable API client
5. ✅ Unit testing với pytest
6. ✅ Retry logic

**Tiếp theo:**
- Học về Authentication (API Keys, JWT)
- Rate limiting
- Async API calls với `aiohttp`
- WebSocket communication
- GraphQL APIs

---

## 🔗 Resources Bổ Sung

- Requests Documentation: https://docs.python-requests.org/
- JSON API Guide: https://jsonapi.org/
- RESTful API Best Practices: https://restfulapi.net/
- JSONPlaceholder (Test API): https://jsonplaceholder.typicode.com/

Happy Learning! 🎉
