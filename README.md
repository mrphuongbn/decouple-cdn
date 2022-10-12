# Phân tách CDN - Giảm chi phí data transfer - Giảm độ trễ cho production

Lưu ý: Ở bài viết này chúng ta sẽ không bàn về CDN là gì, cách dùng như thế nào. 
Bài viết phù hợp với các sản phẩm có tải lớn, nhiều cấu hình origin đang chạy trên AWS, cân nhắc nhiều nhà cung cấp local CDN vì vấn đề chi phí và độ trễ.


Luồng CDN mà mọi người thường thấy:

![alt text](https://github.com/mrphuongbn/decouple-cdn/blob/main/normal-cdn-aws.png?raw=true)

# Thực trạng
- Về góc độ production, chỉ sử dụng một nhà cung cấp cho một dịch vụ chính là một SPF – single point of failure. 
- Mặc dù các nhà cung cấp CDN luôn cam kết được hỗ trợ chống tấn công DDoS ở tầng này, nhưng vẫn tiềm ẩn rủi ro bị tấn công/lỗi hệ thống bị lỗi kết nối một phần hoặc toàn bộ.
- Với nhiều origin như media files trên S3, xử lý ảnh thumb ở cụm compute, css,...  đòi hỏi mỗi lần cấu hình trên một nhà cung cấp CDN rất phức tạp.
- Hơn nữa, độ trễ từ mỗi nhà cung cấp tới origin khác nhau, thường là đi internet với độ trễ cao. Đặc biệt lúc đứt cáp thì càng thảm hoạ.
- Thực tế mình còn gặp tình huống cụm CDN của một Việt Nam local brand bị tèo nhiều lần một tháng, dẫn tới lỗi phía end-user và đồng thời phải gọi lại origin khiến chi phí data transfer vụt lên cao từ vài chục đến vài trăm dollar, thậm chí hơn nữa. (1TB ~ 1000$ từ S3 Singapore về Việt Nam)


# Cân nhắc về chi phí
1. Data transfer = Cloudfront bandwidth là 0,120 USD/GB cho 10TB đầu.(Khoảng 2700 VNĐ, tính theo giá 23000VNĐ/Dollar)
=> Để data chạy thẳng từ origin tới end-user tốn chi phí ngang nhau nhưng độ trễ thấp hơn.

2. AWS Cloudfront dùng trên 10TB thì đâu đó làm việc với sale sẽ giảm về $0.085/GB (Khoảng 1955 VNĐ/GB).

3. Chi phí trên 1GB băng thông với các local brand ở Việt Nam chỉ khoảng 100-300 VNĐ/GB.

4. Chi phí xoá cache, chi phí phòng chống tấn công với AWS ghi rất cụ thể, nhưng local brand lại thường free.

# Tiêu chí chọn một CDN Provider
- Hạ tầng đủ lớn, sức chịu tải cao, phân tán gần end-user của sản phẩm không?
- Có những hỗ trợ về chống tấn công DDoS không?
- SLA cho service là bao nhiêu?
- Lúc lỗi thì có kênh hỗ trợ 24/7 có Hotline, chat nào?
- Chi phí cạnh tranh (Đa phần ở Việt Nam thì đều giá tốt, chênh nhau không nhiều nên chi phí là yếu tố tính cuối cùng)

# Giải pháp

Giải pháp tách
![alt text](https://github.com/mrphuongbn/decouple-cdn/blob/main/cdn-solution.png?raw=true)
Sau khi tách, chúng ta dùng endpoint của cloudfront làm source cho mọi nhà cung cấp CDN sẽ mang lại lợi ích:
- Đổi nhà cung cấp một cách đơn giản chỉ bằng một endpoint.
- Latency từ PoP của AWS tới S3 thì đương nhiên nhà siêu thấp (Chạy Backbone của AWS) mà nó lại ở ngay HN và HCM. Sau đó từ PoP tới các PoP của local CDN lại càng chả phải nghĩ.
- Giảm rất nhiều chi phí CDN và data transfer out của S3. (Đương nhiên là vẫn còn ít chi phí từ Cloudfront tới local CDN provider)
- Được support xử lý tấn công tốt hơn từ local CDN provider.
- Trường hợp xấu nhất là tất cả các local CDN provider kém thì chỉ cần CNAME trực tiếp domain về cloudfront mà không phải điều chỉnh quá nhiều.

---------
Trên đây là góc nhìn cá nhân mình, mong nhận được đóng góp để cải thiện hơn.
Trân trọng.
