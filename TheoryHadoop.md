# TheoryWithHadoop
### 1. Các khái niệm 
---
- Spark cho phép xử lý dữ liệu lớn phân tán m cách hiệu quả và nhanh chóng. Spark có tốc độ xử lý nhanh gấp 100 lần so với Hadoop Mapreduce khi được cache trên bộ nhớ và nhanh hơn gấp 10 lần 10 nếu được cache trên đĩa.
- Điểm nổi bật của Spark là các tập dữ liệu phân tán có khả năng phục hồi dữ liệu khi xyar ra lỗi đó là RDD, có thể lưu tạm thời trên bộ nhớ RAM, có khả năng chịu lỗi cao và tính toán song song, cung cấp cơ chế cache trên memory.
- **RDD** là một tập các phần tử không thể thay đổi được phân tán. Phần tử ở đây là có thể là các đối tượng trong java, Scala, Python, hay do người dùng định nghĩa, RDD được chia thành nhiều Partition để dễ dàng lưu trữ, nhằm tính toán phân tán trên các nodes.
- RDD hỗ trợ 2 kiểu hoạt động: *transformations* và *actions*
- **Ban đầu RDD được tạo qua Transformation chỉ như một metadata chỉ dẫn cách tính toán dữ liệu nên RDD đó. Dữ liệu trên RDD chỉ thực sự được tính toán khi gọi Action. Thao tác loading dữ liệu cũng là Lazy Evaluation. Spark dùng Lazy Evaluation để tối ưu tính toán. Bằng cách nhóm các thao tác lại, xây dựng lược đồ tính toán.**
- Actions : trả về kết quả hoặc lưu trữ dữ liệu trên (HDFS,Amazon s3,Memory)
- Việc xử lý lặp xủa Hadoop Mapreduce được thực hiện như một chuỗi các công việc nối tiếp nhau mà ở đó các kết quả trung gian phải viết đến HDFS và sau đó chúng phải được đọc lại để làm đầu vào cho công việc kế tiếp. Trong khi đó , Spark sẽ đọc dữ liệu đầu vào từ HDFS, thực hiện một loạt các hành động lặp đi lặp lại đối với dữ liệu dạng RDD và sau cùng mới viết đến HDFS.
=> Spark nhanh hơn Hadoop Mapreduce.
- **ReduceByKey** sẽ kết hợp kết quả trên mỗi patition trước khi shuffle dữ liệu (đọc tất cả các patition và gộp các value có cùng key sau đó gộp dữ liệu cùng key vào một patition và tính toán kết quả).  Shuffle operation là một phepd toán phức tạp, liên quan đến disk I/O và network I/O.
- **GroupByKey** đọc từ tất cả các partiton để tìm các key giống nhau và gộp chúng vào cùng một partiotion và tính toán kết quả
- **Collect** trả về tất cả các phần tử của tập dữ liệu như là một mảng ở driver Program. Hàm này hữu ích sau khi lọc hoặc các thao tác khác mà trả về tập dữ liệu con đủ nhỏ. Collect sẽ copy tất cả element của tập dữ liệu vào driver program và sẽ dễ bị outOfMemoryError. Nên *filter* dữ liêu trước hoặc có thể sử dụng take() để chắc chắn số element được trả về.
- **Filter** trả về 1 RDD mới bằng cách chọn những phần tử đàu vào mà hàm **func** trả về giá trị **true**
- **map(func)** trả về 1 RDD bằng cách truyền mỗi phần tử đầu vào qua hàm **func**
- **flatMap(func)** tương tự như map nhưng khác ở chỗ, mỗi phần tử đầu vào qua faltmap sẽ trả về 0 hoặc nhiều phần tử đầu ra.
- **mapPartition(func)** tương tự như map nhưng chạy riêng biệt trên mỗi vùng RDD, hàm **func** phải có dang *Iterator[T] => Iterator[U]* khi chạy RDD kiểu T*
- **saveAstextFile** ghi các phần tử của tập dữ liệu như 1 file text(hoặc 1 têp các file text) lên 1 thư mục trong hệ thống local, HDFS hoặc hệ thống hỗ trợ Hadoop bấtkỳ.
- **Join** khi gọi tập dữ liệu có kiểu (k, v) và (k, w) nó trả về 1 cặp mới (k, (v,w)) nghĩa là nối 2 phần tử có cùng key.
- **Cogroup** khi gọi tập dữ liệu có kiểu (k , v) và (k, w) nó sẽ trả về 1 tập dữ liệu (k,seq(v),seq(w))
- **Union** trả về 1 RDD mới là hợp của tập dữ liệu phần tử đầu vào và phần tử của đối
- **take(n)** trả về một mảng gồm n phần tử đầu tiên của tập dự liệu.
- **Count** trả về số phần tử của tập dữ liệu


**NOTE**
{ Data frame là bộ dữ liệu phân tán tổ chức thành các cột được đặt tên. Tương đương với một bảng trong cơ sở dữ liệu quan hệ, nhưng với sự tối ưu hóa và phong phú hơn.}
