# go-for-web

Ngày nay **Golang** không còn là ngôn ngữ lập trình quá xa lạ đối với các nhà lập trình viên trên thế giới. Nhưng đa phần trong số chúng ta mới chỉ biết đến **Golang** là ngôn ngữ lập trình chỉ mạnh trên server mà ít biết rằng **Golang** cũng vô cùng mạnh mẽ khi được dùng để lập trình ứng dụng web. Trong bài viết này, tôi xin giới thiệu tới quý bạn đọc, giúp bạn đọc hiểu được những khái niệm cơ bản nhất khi sử dụng **Golang** để lập trình một ứng dụng web. 

Mục tiêu của bài viết :

- Cũng cấp nhưng khái niệm, đoạn mã dễ hiểu nhất để phát triển ứng dụng web bằng GO
- Hướng dẫn và đưa ra một số ví dụ cho những bạn nào mới lập trình với GO
- Là nguồn tài liệu cho các lập trình tìm kiếm những đoạn mã chuẩn cũng như các hướng dẫn nếu cần thiết

Để bắt đầu, hãy cùng xem ví dụ về "Hello world"

## Hello world

**1. Khai báo một Request Handle**

Trước hết, tạo một function nhận tất cả các request HTTP đến từ trình duyệt, HTTP client, hay các yêu cầu API. Hàm đó được khai báo như sau

```
func (w http.ResponseWriter, r *http.Request)
```

Hàm này có 2 tham số truyền vào, 

- http.ResponseWriter: nơi lưu giá trị response text/html vào đó
- http.Request: Bao gồm tất cả thông tin có trong HTTP request. Ví dụ như URL, hay các thông tin của header

Một hàm handle request tới HTTP server đơn giản có thể được viết như sau:

```
http.HandleFunc("/", func (w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello, you've requested: %s\n", r.URL.Path)
})
```

**2. Listen HTTP connections**

Nếu bạn chỉ có một hàm xử lý handle như trong mục 1, thì bạn không thể nhận bất ký kết nối nào từ bên ngoài. HTTP server cần phải lắng nghe trên 1 port để chuyển các kết nối tới hàm handle request. HTTP thường lấy port 80 làm mặc định. Đoạn mã sau sẽ khởi động máy chủ HTTP mặc định của Go, và sử dụng cổng 80 để chuyển các request tới hàm xử lý

```
http.ListenAndServe(":80", nil)

```

**3. Full code**

```
package main

import (
    "fmt"
    "net/http"
)

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hello, you've requested: %s\n", r.URL.Path)
    })

    http.ListenAndServe(":80", nil)
}

```

Giờ thì bạn hãy thử truy cập vào link [http://localhost/](http://localhost/) trên trình duyệt của mình nhé. 

## HTTP Server

Phần này sẽ giới thiệu cho các bạn cách để trạo một HTTP Server cơ bản nhất với GO. Một HTTP server cơ bản sẽ có những nhiệm vụ sau: 

- Xử lý yêu cầu động bộ : Xử lý các yêu cầu tới từ người sử dụng trình duyệt (đăng nhập, upload hình ảnh )
- Cung cấp nội dung tĩnh : Cung câp css, javascript, hình ảnh để tạo trải nghiệm cho người dùng 
- Chấp nhận kết nối : Máy chủ HTTP cần phải lắng nghe trên một cổng cụ thể để có thể chập nhận kết nối từ internet

**1. Xử lý yêu cầu động bộ**

Package _net/http_ bao gồm tất cả những điều cần thiết để chấp nhận yêu cầu cũng như xử lý chúng một cách linh hoạt. Chúng ta có thể khai báo một handle mới với function _http.HandleFunc_. Tham số đầu tiên là một đường dẫn, tham số thứ 2 là một hàm để thực thi. Với ví dụ dưới đây, khi một người dùng truy cập vào 1 website, họ có thể nhìn thấy được đoạn text chào đón nồng nhiệt 

```
http.HandleFunc("/", func (w http.ResponseWriter, r *http.Request) {
    fmt.Fprint(w, "Welcome to my website!")
})
```

Để xử dụng linh hoạt, _http.Request_ bao gồm tất cả thông tin liên quan tới request và các tham số của nó. Bạn có thể lấy các tham số của method GET với phướng thức _r.URL.Query().Get("token")_ hay POST parame (được submit từ HTML form) _r.FormValue("email").

**2. Cung cấp nội dung tĩnh**

Để cung cấp các file tĩnh như JS, CSS, image chúng ta sử dụng _http.FileServer_ và truyền vào một đường dẫn url. Tất nhiên, nó cần được biết file của bạn đang được lưu trữ ở đâu. Chúng ta có thể dùng cách sau:

```
fs := http.FileServer(http.Dir("static/"))
```

Khi máy chủ được cài đặt, chúng ta chỉ cần trỏ 1 đường dẫn url vào đó. Lưu ý rằng, để phân biệt được file một cách chính xác, chúng ta cần loại bỏ một phần của url

```
http.Handle("/static/", http.StripPrefix("/static/", fs))
```

