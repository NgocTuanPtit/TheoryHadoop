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
- **Checkpointing** lưu RDD vào đĩa cứng để các tiến  trình khác có thể sử dụng lại RDD này làm partition trung gian thay vì tính toán lại RDD từ các nguồn dữ liệu gốc.

- **Accumulaotor** đối khi chúng ta muốn sử dụng các biến bên ngoài của chương trình driver trong trasformation. Điều này hoàn toàn có thể, thông thường *các bản sao của biến* sẽ được gửi từ driver node đến worker node. Nhưng những thay đổi trên biến này không được tập hợp lại trên driver node. **Accumulator** sẽ biến tập hợp các giá trị thay đổi của biến từ worker nodes trở vè driver node - cập nhật lại giá trị cho biến - giống như là biến toàn cục =))
- **Broadcast** được gửi từ driver node với cơ chế giống Bittorrent cho worker node - các task ở worker node không thể thay đổi giá trị biến - giống như là hằng số vậy đó :v
- **Shuffle** là bược thực hiện song song với bước redude, tức là sau khi **map** thực hiện xong công việc của mình, output của **map** được đặt rải rác trên các **cluster** khác nhau nên **shuffle** sẽ làm nhiệm vụ thu thập các cặp khóa và giá trị do 
**map** sinh ra mà có cùng khóa để chuyển qua cho **reduce** thực hiện tiếp công việc của nó.
-**Sort** là một cách để sắp xếp RDD bằng cách chuyển một bao đóng lấy một tuple làm đầu vào và sắp xếp RDD.
-**Persistence** lưu trữ lại giá trị tính toán của RDD để sau này gọi action trên RDD sẽ không mất thời gian tính toán lại từ đầu.

---
-**Spark Streaming** là một phần mở rộng của Spark API cho phép xử lý luồng dữ liệu trực tiếp có khả năng mở rộng, thông lượng cao, có khả năng chịu lỗi. Dữ liệu có thể được hấp thụ từ nhiều nguồn khác nhau như Kafka, Flumem kinesis, or TCP sockets và có thể được xử lý bới các thuật toán phức tạp được thể hiện với các chức năng  cao cấp như *map, reduce, join và windown*. Về cơ bản Spark Streaming sẽ nhận dữ liệu từ các nguồn Streaming data (được gọi là **receiver data**). Sau đó nó chia nhỏ cái receiver data đó ra và chứa trong các **batch data**, mỗi **batch data** được lưu trữ trong một RDD, các RDD này sau đó sẽ được xử lý bởi SpajDD mới. **Một dãy các RDD thì Spark gọi đó là DStream**. Các RDD được tạo ra dựa trên các khoảng thời gian được cấu hình trong ứng dụng Spark Streaming, *nó đóng vai trò định mức tần số dữ liệu sẽ được sử lý bới ứng dụng.*



**NOTE**
{ Data frame là bộ dữ liệu phân tán tổ chức thành các cột được đặt tên. Tương đương với một bảng trong cơ sở dữ liệu quan hệ, nhưng với sự tối ưu hóa và phong phú hơn.}



https://codingpearls.com/big-data/apache-spark/viet-ung-dung-word-count-tren-spark-bang-scala-su-dung-intellij-idea-community.html

https://docs.google.com/document/d/1FH20GlPcs9H0SJt7wsnacUwZEvcHi_NpxJ7TQbCM3Vg/edit#

https://www.javatpoint.com/mapreduce-word-count-example
