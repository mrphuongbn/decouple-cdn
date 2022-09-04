# decouple-cdn
CDN Nâng cao - Tách biệt origin để tránh phụ thuộc vào một nhà cung cấp

Ở bài viết này chúng ta sẽ không bàn về CDN là gì, cách dùng như thế nào. Về góc độ production, chỉ sử dụng một nhà cung cấp cho một dịch vụ chính là một SPF – single point of failure. Mặc dù các nhà cung cấp CDN luôn cam kết được hỗ trợ chống tấn công DDoS ở tầng này, nhưng vẫn tiềm ẩn rủi ro bị tấn công/lỗi hệ thống bị lỗi kết nối một phần hoặc toàn bộ.  
Luồng CDN mà mọi người thường thấy:

![alt text](https://github.com/mrphuongbn/decouple-cdn/blob/main/normal-cdn.png?raw=true)
