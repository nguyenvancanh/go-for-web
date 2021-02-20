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

**3. Accept connections**

Việc cuối cùng chúng ta cần làm để hoàn thiện HTTP server đó là khai báo cổng để có thể lắng nghe các sự kiện đến từ request. Đương nhiên, Go cũng có một máy chủ HTTP có sẵn, chúng ta có thể start dễ dàng và nhanh chóng. Sau khi start, bạn có thể truy cập vào ứng dụng bằng trình duyệt của mình

```
http.ListenAndServe(":80", nil)
```

**4. Full code**

```
package main

import (
    "fmt"
    "net/http"
)

func main() {
    http.HandleFunc("/", func (w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Welcome to my website!")
    })

    fs := http.FileServer(http.Dir("static/"))
    http.Handle("/static/", http.StripPrefix("/static/", fs))

    http.ListenAndServe(":80", nil)
}
```

## Routing

Package _net/http_ cung cấp cho chúng ta khá đầy đủ các hàm xử lý với giao thức HTTP. Chỉ có 1 điểm mà nó chưa có hay chưa thực sự tốt đó là định tuyết các request phức tạp. Chúng ta cũng không cần phải quá lo lắng, khi có một package rất phổ biến cho vấn đề này, được biết đến rộng rãi trong cộng đồng Go. Ví dụ sau sẽ giúp các bạn hiểu rõ hơn về cách dùng package _gorilla/mux_ để tạo route cho ứn dụng web của mình. 

**1. Cài đặt **

Để cài đặt, bạn cần chạy lệnh sau: 

```
go get -u github.com/gorilla/mux
```

**2. Create new router**

Đầu tiên, hay khai báo một request router mới. Nó là bộ đính tuyết chính cho ứng dụng web của bạn và sau đó sẽ được chuyển dưới dạng tham số tới máy chủ. Nó sẽ nhận tất cả các kết nối HTTP và chuyển nó cho các hàm handle request mà bạn đã khai báo. Tạo mới bằng lênh sau: 

```
r := mux.NewRouter()
```

**3. Khai báo handle request**

Một khi bạn đã có router, bạn có thể khai báo một hàm handle reqeust cho nó một cách dễ dàng. Khác biệt duy nhất đó là việc gọi http.HandleFunc(...) thành gọi HandleFunc trên router bạn đã định nghĩa

**4. URL parametter**

Điểm mạnh nhất của package _gorilla/mux_ là khả năng lấy các thành phần của URL. Ví dụ, đây là 1 URL trong ứng dụng của bạn

```
/books/go-programming-blueprint/page/10
```

URL trên có 2 tham số động đó là

- Tên sách (go-programming-blueprint)
- số trang (page 10 )

Khi đó, bạn cần khai báo router như sau để có thế dynamic các tham số được truyền vào

```
r.HandleFunc("/books/{title}/page/{page}", func(w http.ResponseWriter, r *http.Request) {
    // get the book
    // navigate to the page
})
```

Điều cuối cùng là package này cũng cung cấp cho chung ta phương thức _mux.Vars(r)_ giúp chúng ta dễ dàng lấy các tham số từ router ra hơn

```
func(w http.ResponseWriter, r *http.Request) {
    vars := mux.Vars(r)
    vars["title"] // the book title slug
    vars["page"] // the page
}
```

**5. Full code**

```
package main

import (
    "fmt"
    "net/http"

    "github.com/gorilla/mux"
)

func main() {
    r := mux.NewRouter()

    r.HandleFunc("/books/{title}/page/{page}", func(w http.ResponseWriter, r *http.Request) {
        vars := mux.Vars(r)
        title := vars["title"]
        page := vars["page"]

        fmt.Fprintf(w, "You've requested the book: %s on page %s\n", title, page)
    })

    http.ListenAndServe(":80", r)
}
```

**6. Một số ưu điểm khác **

- Method:

```
r.HandleFunc("/books/{title}", CreateBook).Methods("POST")
r.HandleFunc("/books/{title}", ReadBook).Methods("GET")
r.HandleFunc("/books/{title}", UpdateBook).Methods("PUT")
r.HandleFunc("/books/{title}", DeleteBook).Methods("DELETE")
```

- Hostname & Subdomains

```
r.HandleFunc("/books/{title}", BookHandler).Host("www.mybookstore.com")
```
- Schemes

```
r.HandleFunc("/secure", SecureHandler).Schemes("https")
r.HandleFunc("/insecure", InsecureHandler).Schemes("http")
```

- Path Prefixes & Subrouters

```
bookrouter := r.PathPrefix("/books").Subrouter()
bookrouter.HandleFunc("/", AllBooks)
bookrouter.HandleFunc("/{title}", GetBook)
```
