---
title: "Blog 2"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---

# Physics on AWS: Tối ưu hiệu suất tuabin gió bằng OpenFAST trong digital twin

Khi tìm hiểu bài viết “Physics on AWS: Optimizing wind turbine performance using OpenFAST in a digital twin”, điểm mình thấy đáng chú ý nhất là cách AWS kết hợp dữ liệu IoT thời gian thực với mô phỏng vật lý để xây dựng một digital twin cho tuabin gió.

Thay vì chỉ giám sát thiết bị bằng dữ liệu cảm biến, kiến trúc này còn dùng OpenFAST — một công cụ mô phỏng tuabin gió mã nguồn mở của National Renewable Energy Laboratory, hay NREL — để chạy các mô phỏng theo nhu cầu. Kết quả mô phỏng được so sánh với dữ liệu vận hành thực tế, từ đó hỗ trợ phát hiện bất thường và cải thiện hiệu suất vận hành của wind farm.

**Vì sao cần digital twin?**

- Digital twin trong bài viết này được hiểu là một kiến trúc kết nối tuabin gió vật lý với AWS Cloud thông qua IoT devices để theo dõi hiệu suất, đồng thời bổ sung tri thức từ các mô phỏng theo nhu cầu.

- Mục tiêu của digital twin là so sánh dữ liệu thực tế từ tuabin với kết quả mô phỏng từ OpenFAST. Khi có sai lệch hoặc bất thường, hệ thống có thể hỗ trợ đánh giá nguyên nhân, kiểm tra tác động của thay đổi controller và đưa ra quyết định điều chỉnh vận hành.

- OpenFAST là một aeroelastic simulator thường được dùng trong thiết kế và chứng nhận tuabin gió theo các tiêu chuẩn như IEC 61400-1 và IEC 61400-3. Tuy nhiên, trong thực tế vận hành, các điều kiện môi trường có thể thay đổi theo cách không được tính hết trong giai đoạn thiết kế ban đầu. Ví dụ, turbulence intensity cao hơn dự kiến có thể làm tăng tốc độ suy giảm của thiết bị.

- Vì vậy, dùng lại chính công cụ mô phỏng trong thiết kế để kiểm tra thay đổi controller trước khi triển khai ra thiết bị thật là một hướng tiếp cận có giá trị. Nó giúp operator đánh giá xem một thay đổi có thể giảm hư hại tích lũy hay không trước khi áp dụng vào hệ thống vận hành.
Tổng quan kiến trúc digital twin

Kiến trúc trong bài gốc kết hợp dữ liệu thực tế từ tuabin gió với kết quả mô phỏng để hỗ trợ cập nhật controller software. Luồng xử lý chính gồm các bước sau:
1. Cảm biến của tuabin gió được kết nối tới AWS Cloud thông qua AWS IoT Core.
2. Một IoT rule chuyển tiếp dữ liệu cảm biến vào Amazon Timestream, cơ sở dữ liệu chuyên dụng cho time-series data.
3. Một AWS Lambda chạy theo lịch để truy vấn dữ liệu trong Timestream và phát hiện bất thường trong chuỗi thời gian.
4. Khi phát hiện bất thường, Amazon SNS gửi thông báo, đồng thời Lambda preprocessor chuẩn bị input files cho mô phỏng OpenFAST.
5. Các mô phỏng được chạy theo nhu cầu. Phiên bản OpenFAST mới nhất được pull từ Amazon Elastic Container Registry, hay Amazon ECR.
6. Mô phỏng được dispatch thông qua RESTful API và chạy bằng AWS Fargate.
7. Kết quả mô phỏng được upload lên Amazon S3.
8. Time-series data từ mô phỏng được xử lý bằng AWS Lambda. Tại bước này, hệ thống đưa ra quyết định có cập nhật controller software hay không dựa trên bất thường đã phát hiện.
9. Lambda post-processor khởi tạo quá trình cập nhật controller software cho tuabin gió. Thông tin cập nhật được truyền về tuabin thông qua AWS IoT Core.
10. Kết quả được trực quan hóa bằng Amazon Managed Grafana.

Một ví dụ về bất thường ở bước phát hiện là controller overspeed alarm. Có thể dùng rule-based anomaly detection đơn giản để phát hiện khi giá trị vượt ngưỡng. Ngoài ra, có thể dùng các phương pháp anomaly detection nâng cao hơn bằng machine learning thông qua Amazon SageMaker.

Các thành phần chính của kiến trúc

Kiến trúc digital twin thành bốn nhóm nội dung quan trọng:
· Event-driven architecture workflow.
· Thực thi OpenFAST thông qua RESTful API và containers.
· Chạy nhiều mô phỏng OpenFAST đồng thời bằng Application Load Balancer và Amazon EC2 Auto Scaling.
· Trực quan hóa tài sản vật lý và kết quả mô phỏng bằng Amazon Managed Grafana.

**Event-driven architecture**

- Event-driven architecture giúp các service giao tiếp bất đồng bộ và giảm phụ thuộc trực tiếp vào nhau. Workflow được kích hoạt tự động khi có event xảy ra. Event có thể là một cảnh báo đang hoạt động từ tuabin, hoặc một output file của OpenFAST được upload lên Amazon S3.
- Điểm hay của cách tiếp cận này là số lượng tuabin được giám sát có thể tăng từ một lên 100 hoặc nhiều hơn mà không cần cấp phát tài nguyên thủ công theo từng tuabin.
- AWS Lambda hỗ trợ scale nhanh để tăng số lượng tác vụ xử lý mô phỏng OpenFAST. Trong khi đó, AWS Fargate giúp chạy container mà không cần quản lý hạ tầng compute bên dưới.
- Về mặt vận hành, kiến trúc event-driven giúp đội kỹ thuật tự động hóa các bước như chuẩn bị input files, dispatch mô phỏng và post-process kết quả mà không phải tự provision tài nguyên thủ công.

**Containerization cho OpenFAST**

- OpenFAST được đóng gói dưới dạng container. Containerization cho phép triển khai ứng dụng cùng với các thư viện cần thiết để chạy mô phỏng.
- Trong kiến trúc này, Docker được dùng để tạo container image chứa OpenFAST executable. FastAPI cũng được đưa vào container để mô phỏng có thể được dispatch thông qua RESTful API.

**RESTful API cho OpenFAST với các nhóm lệnh chính:**
- PUT /execute: chạy mô phỏng với initial conditions.
- POST /upload_to_s3: upload kết quả mô phỏng lên Amazon S3.
- GET /status: kiểm tra trạng thái mô phỏng.
- DELETE /simulation: xóa kết quả mô phỏng.

Cách đóng gói này giúp engineering teams có thể sử dụng một phiên bản OpenFAST phù hợp với tuabin gió đang vận hành mà không cần cấu hình thủ công lại môi trường chạy.

**Load balancing và auto scaling**

Kiến trúc sử dụng Amazon EC2 Auto Scaling và Application Load Balancer, hay ALB, để xử lý nhu cầu mô phỏng thay đổi theo thời gian và cho phép chạy nhiều OpenFAST simulations đồng thời.
EC2 Auto Scaling có nhiệm vụ điều chỉnh số lượng OpenFAST containers dựa trên số lượng simulation requests. Khi nhu cầu tăng, hệ thống có thể scale out; khi nhu cầu giảm, hệ thống có thể scale in để tránh tài nguyên nhàn rỗi.
Khi kết hợp với ALB, các simulation requests được phân phối đều tới các OpenFAST containers. Thiết kế này giúp duy trì hiệu năng và tăng tính sẵn sàng khi nhiều mô phỏng được chạy đồng thời.

**Data visualization**

- Amazon Timestream được dùng để thu thập và lưu trữ metrics thời gian thực từ tuabin gió vật lý. Các metrics này có thể bao gồm rotor speed, generator power, generator speed, generator torque hoặc wind turbine control system alarms.
Một điểm đáng chú ý của Timestream là scheduled queries. Tính năng này có thể chạy các tác vụ tự động định kỳ, ví dụ tính tốc độ gió trung bình trong 10 phút hoặc theo dõi các tuabin có controller alarms.
- Nhờ đó, operations teams có thể xem dữ liệu chi tiết theo thời gian thực hoặc truy vấn dữ liệu lịch sử bằng SQL.
- Amazon Managed Grafana được kết nối với dữ liệu vận hành trong Timestream và kết quả mô phỏng OpenFAST lưu trong Amazon S3. Điều này giúp so sánh dữ liệu mô phỏng với dữ liệu thực tế, đồng thời quan sát phản ứng của các thành phần được mô phỏng.
- Với engineering teams, Grafana đóng vai trò như một cửa sổ quan sát: họ có thể xem mô phỏng phản ứng thế nào với thay đổi controller, sau đó kiểm tra liệu tuabin vật lý có phản hồi đúng như kỳ vọng hay không.

**Ý nghĩa của kiến trúc**

Điểm quan trọng của kiến trúc này là nó không chỉ dừng ở việc giám sát tuabin gió. Hệ thống còn tạo ra một vòng lặp giữa:
- dữ liệu cảm biến từ thiết bị thật,
- phát hiện bất thường,
- mô phỏng vật lý bằng OpenFAST,
- phân tích kết quả mô phỏng,
- và cập nhật controller software nếu cần.

Cách tiếp cận này giúp operator đưa ra quyết định dựa trên dữ liệu gần thời gian thực, đồng thời tận dụng các mô hình vật lý để nâng cao độ chính xác trong phân tích.

Thay vì chỉ phản ứng sau khi hư hại đã xảy ra, digital twin có thể hỗ trợ nhận diện nguồn gốc cảnh báo và áp dụng chiến lược vận hành nhằm hạn chế hao mòn quá mức trước khi thiệt hại trở nên nghiêm trọng.

![Ảnh minh hoa](/images/3.jpg)

Link bài viết tham khảo: https://aws.amazon.com/vi/blogs/architecture/physics-on-aws-optimizing-wind-turbine-performance-using-openfast-in-a-digital-twin/