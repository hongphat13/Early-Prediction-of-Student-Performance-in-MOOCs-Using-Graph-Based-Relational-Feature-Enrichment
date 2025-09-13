<img src="assets\cloud-img-01.png" width="100%">

1. **Lưu trữ: Tải các dữ liệu lên S3**  
   Bước 1: Tạo một S3 Bucket trên AWS

   Truy cập Console của S3:

   - Vào S3 Console → chọn Create bucket.
   - Cấu hình bucket:
     - Bucket name: mooc
     - Giữ nguyên các thiết lập mặc định khác. Key: workshop Value: AnalyticsOnAWS
   - Chọn: Create bucket

   Bước 2: Tạo thư mục trong bucket

- Mở bucket
- Chọn: Create folder
- Tên thư mục: data
- Chọn Create folder  
  Bước 3: Tạo thư mục con trong thư mục data
- Mở thư mục: data
- Chọn: Create folder
- Tên thư mục: reference_data
- Chọn Create folder  
  Bước 4: Tải các folder tương ứng với dữ liệu train lên
- Trong giao diện S3 Dashboard, mở thư mục reference_data.
- Chọn: Upload
- Chọn Add files và tải lên các file lên.
- Chọn Upload (góc dưới bên trái).  
  Ngoài data, nhóm tạo thêm 3 folder khác
- Data-quality: lưu các số liệu về chất lượng dữ liệu.
- Query_results: lưu các truy vấn trong warehouse
- Tools: lưu các model và scaler để phụ vụ việc dự đoán

<img src="assets\cloud-img-02.png" width="100%">

Trong folder data có các file:

- Reference_data: chưa các phase1, phase2, … chưa các thống tin của user để dự đoán.
- Video: thông tin xem video của user.
- Exercise: thông tin về làm bài tập của user.
- Comment: thông tin về bình luận của user.
- Reply: thông tin về trả lời của user.
- Course_info: thông tin về khóa học.
- Mốt số dữ liệu thêm: các dữ liệu đầy đủ của phase1, phase2, điểm thành phần của user,... hoặc các file đã được xử lý.

2. **Catalog cho dữ liệu**
1. **Tạo IAM có các quyền cho AWS Glue**

   Bước 1: Truy cập IAM Console

- Truy cập vào IAM Console từ giao diện quản lý AWS.
- Trong menu bên trái, chọn Roles (Vai trò).
- Nhấn nút Create role (Tạo vai trò).

  Bước 2: Chọn loại trusted entity

- Ở mục Trusted entity type, chọn:
  - AWS service (Dịch vụ AWS)
- Ở phần Use case, chọn:
  - Glue (đây là dịch vụ sẽ sử dụng vai trò này)
- Nhấn Next (Tiếp theo)

  Bước 3: Gán quyền (Attach permissions policies)

- Trong ô tìm kiếm, tìm kiếm và chọn các chính sách sau:
  - AmazonS3FullAccess – cấp quyền truy cập đầy đủ vào S3.
  - AWSGlueServiceRole – cấp quyền AWS Glue cần thiết.
  - Đảm bảo chọn đúng hai chính sách này.
- Nhấn Next (Tiếp theo)

  Bước 4: Gắn thẻ (Tùy chọn)

- Thêm các thẻ để dễ quản lý.
- Ví dụ: Key: workshop; Value: AnalyticsOnAWS
- Nhấn Next (Tiếp theo)

  Bước 5: Đặt tên và tạo vai trò

- Nhập tên vai trò: AnalyticsworkshopGlueRole
- Xác nhận lại rằng chỉ có 2 chính sách được gán.
- Nhấn Create role (Tạo vai trò)

<img src="assets\cloud-img-03.png" width="100%">

2. **Các bước tạo AWS Glue Crawler để quét dữ liệu từ S3**

   Bước 1: Truy cập Glue Console

- Truy cập vào AWS Glue Console
- Ở thanh bên trái, chọn Crawlers
- Nhấn nút Create crawler (Tạo crawler)

<img src="assets\cloud-img-04.png" width="100%">

Bước 2: Thông tin về crawler

- Crawler name (Tên crawler): AnalyticsworkshopCrawler
- (Tùy chọn) Thêm Tags (Nhãn): Key: workshop, Value: AnalyticsOnAWS
- Nhấn Next (Tiếp theo)

  Bước 3: Thêm nguồn dữ liệu (Data source)

- Chọn Add a data source
- Data source: chọn S3
- Trong mục Location of S3 data, chọn:
- In this account
- Nhập đường dẫn S3: s3://mooccube-datalake/data/
- Giữ nguyên thiết lập mặc định: Chọn Crawl all sub-folders
- Chọn Add an S3 data source (Thêm nguồn dữ liệu)

  Bước 4: Chọn nguồn dữ liệu vừa thêm

- Chọn nguồn dữ liệu bạn vừa tạo dưới mục Data Sources
- Nhấn Next

  Bước 5: Gán IAM Role

- Trong phần IAM Role, chọn: Existing IAM role
- Sau đó chọn AnalyticsworkshopGlueRole
- Giữ nguyên các thiết lập còn lại
- Nhấn Next

  Bước 6: Cấu hình output – Tạo database

- Trong phần Output configuration, chọn: Add database
- Trong cửa sổ mới hiện ra, tạo:
- Tên database: analyticsworkshopdb
- Nhấn Create database
- Đóng cửa sổ vừa tạo và quay lại cửa sổ trước

<img src="assets\cloud-img-05.png" width="100%">

Bước 7: Chọn database và lịch chạy

- Nhấn biểu tượng làm mới (refresh) bên cạnh Target database
- Chọn database: analyticsworkshopdb
- Crawler schedule: chọn On demand
- Nhấn Next

  Bước 8: Xem lại & tạo crawler

- Kiểm tra lại tất cả thông tin cấu hình
- Nhấn Create crawler

  Bước 9: Chạy crawler

- Sau khi thấy thông báo:
- The following Crawler has been created: “AnalyticsworkshopCrawler”
- Nhấn Run crawler để chạy crawler lần đầu tiên
- Đợi vài phút để crawler hoàn tất quét dữ liệu

<img src="assets\cloud-img-06.png" width="100%">

<img src="assets\cloud-img-07.png" width="100%">

3. **Xác minh bảng vừa tạo trong Glue Catalog**

   Bước 1: Truy cập Glue Data Catalog

- Truy cập vào Glue Console
- Chọn Databases → chọn database có tên: analyticsworkshopdb
- Trong database này, chọn mục Tables

  Bước 2: Kiểm tra bảng raw

- Chọn bảng raw trong danh sách
- Truy vấn dữ liệu với Amazon Athena

  Bước 3: Mở Amazon Athena. Truy cập vào Athena Console

  Bước 4: Cấu hình nơi lưu kết quả truy vấn (nếu cần)

- Nếu xuất hiện cảnh báo màu xanh lá, chọn “Edit settings”
- Trong phần Query result location, chọn: s3://mooccube-datalake/query_results/
- Nhấn Save

  Bước 5: Xem bảng và xem trước dữ liệu

- Chọn tab Editor
- Trong bảng bên trái (Database), chọn:
- analyticsworkshopdb → chọn bảng raw
- Nhấn vào biểu tượng ba chấm dọc (⋮) bên cạnh bảng raw → chọn Preview Table
- Xem kết quả hiển thị

  Bước 6: Chạy truy vấn SQL

- Trong khung Query Editor, dán đoạn truy vấn sau: SELECT * FROM "analyticsworkshopdb"."course_info" limit 10;
- Nhấn Run để chạy truy vấn và xem kết quả phân tích dữ liệu.

3. **Tính chất lượng dữ liệu**  
   Bước 1: Trong AWS Glue Database, chọn database là analyticsworkshopdb

<img src="assets\cloud-img-08.png" width="100%">

<img src="assets\cloud-img-09.png" width="100%">

Bước 2: Chọn bảng cần tính chất lượng dữ liệu

<img src="assets\cloud-img-10.png" width="100%">  
Bước 3: Thêm tập các quality rules

<img src="assets\cloud-img-11.png" width="100%">

<img src="assets\cloud-img-12.png" width="100%">

<img src="assets\cloud-img-13.png" width="100%">

(các tiêu chí quality mà AWS Glue gợi ý)  
Bước 4: Chạy và xem kết quả

<img src="assets\cloud-img-14.png" width="100%">

<img src="assets\cloud-img-15.png" width="100%">

4. **Chuyển đổi dữ liệu**  
   Biến đổi dữ liệu với AWS Glue (interactive sessions)  
   Sử dụng AWS Glue Studio và Jupyter Notebook thông qua Glue Interactive Sessions để xử lý dữ liệu và lưu kết quả vào một thư mục mới trong S3 (tầng transformed).  
   AWS Glue (interactive sessions) Interactive sessions cho phép phát triển các tác vụ Glue một cách tương tác, chạy từng bước và xem kết quả ngay lập tức.
   **Chuẩn bị IAM Policies và Roles**  
   Bước 1: Tạo IAM Policy

- Truy cập IAM Console → chọn Policies → nhấn Create policy
- Chuyển sang tab JSON và dán nội dung sau:  
  {  
   "Version": "2012-10-17",  
   "Statement": [
  {
  "Effect": "Allow",
  "Action": "iam:PassRole",
  "Resource": "arn:aws:iam::<AWS account ID>:role/Analyticsworkshop-GlueISRole"
  }
  ]  
  }  
  Thay <AWS account ID> bằng ID tài khoản AWS account hiện tại.
- Nhấn Next: Tags (tùy chọn: thêm Tag: workshop: AnalyticsOnAWS)
- Nhấn Next: Review
- Policy name: AWSGlueInteractiveSessionPassRolePolicy
- (Tùy chọn) Mô tả: Cho phép notebook Glue sử dụng IAM role trong interactive session.
- Nhấn Create Policy  
  Bước 2: Tạo IAM Role cho Glue Interactive Sessions
- Trở lại IAM Console → chọn Roles → nhấn Create role
- Chọn Glue làm dịch vụ sử dụng role → nhấn Next
- Gán các policy sau:
  - AWSGlueServiceRole
  - AwsGlueSessionUserRestrictedNotebookPolicy
  - AWSGlueInteractiveSessionPassRolePolicy
  - AmazonS3FullAccess
- Nhấn Next
- Tên Role: Analyticsworkshop-GlueISRole
- (Tùy chọn) Thêm tag: workshop: AnalyticsOnAWS
- Nhấn Create role
- Sử dụng Jupyter Notebook trong AWS Glue cho ETL tương tác

<img src="assets\cloud-img-16.png" width="100%">  
Bước 3: Tạo Glue Job với Jupyter Notebook

- Truy cập Glue Studio → chọn tab Jobs
- Chọn Jupyter Notebook
- Chọn Upload and edit an existing notebook
- Nhấn Choose file → chọn file analytics-workshop-glueis-notebook.ipynb đã có
- Nhấn Create

<img src="assets\cloud-img-17.png" width="100%">

<img src="assets\cloud-img-18.png" width="100%">

Bước 4: Cấu hình notebook

- Job name: AnalyticsOnAWS-GlueIS
- IAM Role: Analyticsworkshop-GlueISRole
- Kernel: giữ mặc định là Spark
- Nhấn Start notebook  
  Bước 5: Thực hiện ETL
- Sau khi notebook khởi động, làm theo hướng dẫn trong từng ô của notebook
- Thực hiện các bước để xử lý dữ liệu từ S3 và lưu kết quả

<img src="assets\cloud-img-19.png" width="100%">

**Kiểm tra dữ liệu đã xử lý**

- Vào S3 Console → mở bucket moooccube-datalake
- Truy cập thư mục: data/addtion/preproccessed-data
- Đảm bảo rằng có các file .parquet được tạo trong thư mục này.
- Phân tích dữ liệu đã xử lý với Athena: Truy vấn dữ liệu đã chuyển đổi bằng Amazon Athena

5. **Phân tích với Amazon Athena**  
   Cho đến thời điểm này, đã lưu trữ nhiều tập dữ liệu trong Amazon S3 và lập chỉ mục (index) chúng thông qua AWS Glue Data Catalog. Giờ đây, với Amazon Athena, chúng ta có thể khám phá dữ liệu bằng cách sử dụng truy vấn SQL tiêu chuẩn.  
   **Khám phá dữ liệu đã chuyển đổi với Athena**  
   Bước 1: Đăng nhập Amazon Athena

- Truy cập Athena Console tại AWS.
- Nếu Athena yêu cầu tạo một bucket để lưu kết quả truy vấn:
  - Vào S3 Console, tạo một bucket mới, ví dụ: query-results
  - Quay lại Athena Console, chọn Settings (góc phải trên cùng).
  - Trong phần Query result location, nhập đường dẫn đến bucket mới: s3://query-results/
  - Nhấn Save

<img src="assets\cloud-img-20.png" width="100%">

Bước 2: Truy vấn dữ liệu

- Vì Athena sử dụng Glue Data Catalog, nên các bảng từ Glue đều có thể thấy trong Athena.
- Ở thanh bên trái, chọn Cơ sở dữ liệu analyticsworkshopdb
- Dán và chạy truy vấn sau:

  SELECT name, video, assignment, exam

  FROM course_info;

- Kết quả: Truy vấn này trả về danh sách nghệ sĩ được nghe nhiều nhất.

<img src="assets\cloud-img-21.png" width="100%">

6. **Kết nối AWS lambda (tạo API) và Athena, S3**  
   Tạo một Hàm Lambda thực hiện truy vấn trên Amazon Athena để lấy ra thông tin khóa học, từ dữ liệu đã xử lý và lưu trữ trên Amazon S3.  
   **Tạo Lambda Function**  
   Bước 1: Truy cập Lambda Console

- Mở Lambda Dashboard từ AWS Console.  
  Lưu ý: Đảm bảo vùng (Region) được chọn là US East (N. Virginia) (us-east-1).  
  Bước 2: Tạo hàm mới
- Chọn Create function (Nếu là lần đầu dùng Lambda, có thể bạn cần nhấn “Get Started”)
- Chọn Author from scratch
- Trong phần Basic Information:
- Function name: getCoures
- Runtime: Python 3.12
- Trong mục Permissions, mở rộng phần "Choose or create an execution role", đảm bảo chọn Create a new role with basic Lambda permissions.
- Nhấn Create function

<img src="assets\cloud-img-22.png" width="100%">

**Viết mã cho Lambda**  
Lambda sẽ sử dụng **boto3** để truy vấn Athena. Boto3 là SDK Python chính thức của AWS.  
Tài liệu: https://boto3.amazonaws.com/v1/documentation/api/latest/index.html  
Tài liệu về Athena client: https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/athena.html  
Bước 3: Thay thế mã mặc định

- Kéo xuống phần Function code, thay thế nội dung mặc định trong lambda_function.py bằng đoạn mã Python được cung cấp (bạn có thể chèn đoạn mã sau nếu cần).

<img src="assets\cloud-img-23.png" width="100%">

- Thiết lập cấu hình
  - Memory: giữ nguyên 128 MB
  - Timeout: đổi thành 10 giây
- Tags (tuỳ chọn): workshop: AnalyticsOnAWS
- Nhấn Save

<img src="assets\cloud-img-24.png" width="100%">

<img src="assets\cloud-img-25.png" width="100%">

**Cấp quyền IAM cho Lambda**  
Bước 4: Thêm IAM Policies

- Nhấn vào tab Permissions trong Lambda Console.
- Click vào Role name trong phần Execution Role để mở trang IAM.
- Chọn Add permissions > Attach policies
- Tìm và gắn các chính sách sau:
  - AmazonS3FullAccess
  - AmazonAthenaFullAccess
- Nhấn Attach policies rồi đóng tab IAM.

  <img src="assets\cloud-img-26.png" width="100%">

  <img src="assets\cloud-img-27.png" width="100%">

  **Cấu hình và kiểm tra hàm Lambda**

  Bước 5: Kiểm tra hàm

- Nhấn Deploy để triển khai hàm với mã mới.
- Nhấn Test (góc trên bên phải).
- Trong cửa sổ bật lên:
- Event name: getCourse
- Template: để mặc định là Hello World
- Nhấn Save
- Nhấn lại Test để chạy thử.
- Kết quả JSON dưới mục Execution result.

  <img src="assets\cloud-img-28.png" width="100%">

  <img src="assets\cloud-img-29.png" width="100%">

  <img src="assets\cloud-img-30.png" width="100%">

7.  **BI dữ liệu trên QuickSight**

Bước 1: Tạo tài khoản quicksight

Bước 2: Chọn dataset muốn BI

<img src="assets\cloud-img-31.png" width="100%">

Điểm số bài tập theo phân phối từng trường

<img src="assets\cloud-img-32.png" width="100%">

Phân bố điểm của từng nhãn qua các trường
