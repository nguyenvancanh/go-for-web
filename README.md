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

Nếu bạn chỉ có một hàm xử lý handle như tr
