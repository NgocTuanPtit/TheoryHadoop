# TheoryWithHadoop
### 1. Khái niệm lazy của rdd
---
- Spark cho phép xử lý dữ liệu lớn phân tán m cách hiệu quả và nhanh chóng. Spark có tốc độ xử lý nhanh gấp 100 lần so với Hadoop Mapreduce khi được cache trên bộ nhớ và nhanh hơn gấp 10 lần 10 nếu được cache trên đĩa.
- Điểm nổi bật của Spark là các tập dữ liệu phân tán có khả năng phục hồi RDD, có thể lưu tạm thời trên bộ nhớ RAM, có khả năng chịu lỗi cao và tính toán song song.
- RDD hỗ trợ 2 kiểu hoạt động: *transformations và actions*
- Transfrmationso là một thao tác **lazy** - **có nghĩa là thao t này sẽ không thực hiện ngay lập tức mà chỉ ghi nhớ các bước thực hiện lại. Thao tác này chỉ được thực hiện khi trong quá trình thực hiện có Actions được gọi thì công việc tính toán của Transformation mới được diễn ra.**
- Việc xử lý lặp xủa Hadoop Mapreduce được thực hiện như một chuỗi các công việc nối tiếp nhau mà ở đó các kết quả trung gian phải viết đến HDFS và sau đó chúng phải được đọc lại để làm đầu vào cho công việc kế tiếp. Trong khi đó , Spark sẽ đọc dữ liệu đầu vào từ HDFS, thực hiện một loạt các hành động lặp đi lặp lại đối với dữ liệu dạng RDD và sau cùng mới viết đến HDFS.
=> Spark nhanh hơn Hadoop Mapreduce.
