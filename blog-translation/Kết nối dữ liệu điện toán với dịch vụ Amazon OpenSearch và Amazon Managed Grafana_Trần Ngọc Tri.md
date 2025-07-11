# Kết nối dữ liệu điện toán với dịch vụ Amazon OpenSearch và Amazon Managed Grafana

> **📖 Bài viết gốc**: [Correlate telemetry data with Amazon OpenSearch Service and Amazon Managed Grafana](https://aws.amazon.com/blogs/big-data/correlate-telemetry-data-with-amazon-opensearch-service-and-amazon-managed-grafana/)
> **👤 Tác giả**: Balaji Mohan, Muthu Pitchaimani, và Senthil Ramasamy
> **📅 Ngày xuất bản**: 03 April 2025
> **🌐 Nguồn**: AWS Big Data Blog
> **👨‍💻 Người dịch**: Trần Ngọc Tri - FCJ Intern
> **📅 Ngày dịch**: 01 July 2025
> **⏱️ Thời gian đọc**: 30 phút

---

## 📋 Tóm tắt

Bài viết này hướng dẫn cách sử dụng Amazon OpenSearch Service và Amazon Managed Grafana để kết nối và phân tích các tín hiệu observability khác nhau như logs, traces và metrics. Tác giả trình bày một giải pháp tham khảo để xây dựng hệ thống monitoring có thể mở rộng quy mô cho các ứng dụng enterprise, giúp giảm Mean Time to Resolution (MTTR) thông qua việc phân tích nguyên nhân gốc hiệu quả hơn. Giải pháp sử dụng OpenTelemetry Collector, Amazon EKS, và các dịch vụ AWS managed để tạo ra một kiến trúc observability toàn diện.

**🎯 Đối tượng đọc**: DevOps Engineers, Platform Engineers, Site Reliability Engineers
**📊 Độ khó**: Intermediate (200)
**🏷️ Tags**: OpenSearch, Grafana, Observability, Telemetry, Monitoring, OpenTelemetry, EKS

---

## 📚 Mục lục

- [Giới thiệu](#giới-thiệu)
- [Tổng quan giải pháp](#tổng-quan-giải-pháp)
- [Yêu cầu tiên quyết](#yêu-cầu-tiên-quyết)
- [Tạo pipeline OpenSearch Ingestion cho logs và traces](#tạo-pipeline-opensearch-ingestion-cho-logs-và-traces)
- [Cài đặt ứng dụng OpenTelemetry demo trên Amazon EKS](#cài-đặt-ứng-dụng-opentelemetry-demo-trên-amazon-eks)
- [Cấu hình OpenTelemetry Collector exporter](#cấu-hình-opentelemetry-collector-exporter)
- [Cấu hình Amazon Managed Grafana](#cấu-hình-amazon-managed-grafana)
- [Tạo correlations trong Amazon Managed Grafana](#tạo-correlations-trong-amazon-managed-grafana)
- [Xác thực kết quả](#xác-thực-kết-quả)
- [Dọn dẹp](#dọn-dẹp)
- [Kết luận](#kết-luận)
- [Glossary - Thuật ngữ](#glossary---thuật-ngữ)
- [Tài liệu tham khảo](#tài-liệu-tham-khảo)

---

## Giới thiệu

Việc troubleshooting một ứng dụng enterprise lớn, phức tạp và phân tán bao gồm những thách thức như tracing các request qua nhiều service, xác định performance bottleneck trên stack, và hiểu các cascading failure giữa các dependent service. Khách hàng thường cần làm việc với dữ liệu cô lập để xác định nguyên nhân cốt lõi của vấn đề. Bằng cách kết nối các tín hiệu khác nhau như logs, traces, metrics và các chỉ số hiệu suất khác, bạn có thể có được cái nhìn sâu sắc có giá trị về nguyên nhân gây ra vấn đề, ở đâu và tại sao.

Amazon OpenSearch Service là một dịch vụ được quản lý để triển khai, vận hành và tìm kiếm dữ liệu ở quy mô lớn trong AWS. Amazon Managed Grafana là một dịch vụ data visualization bảo mật để truy vấn dữ liệu operational từ nhiều nguồn, bao gồm OpenSearch Service.

Trong bài viết này, chúng tôi sẽ chỉ cho bạn cách sử dụng các dịch vụ này để kết nối các tín hiệu observability khác nhau nhằm cải thiện root cause analysis, từ đó giảm Mean Time to Resolution (MTTR). Chúng tôi cũng cung cấp một giải pháp tham khảo có thể được sử dụng ở quy mô lớn cho việc monitoring chủ động các ứng dụng enterprise để tránh vấn đề trước khi chúng xảy ra.

## Tổng quan giải pháp

Sơ đồ sau đây cho thấy kiến trúc giải pháp để thu thập và kết nối các tín hiệu telemetry enterprise khác nhau ở quy mô lớn.

Ở trung tâm của kiến trúc này là các ứng dụng được cấu tạo từ microservices (được đại diện bởi các hộp màu cam) chạy trên Amazon Elastic Kubernetes Service (Amazon EKS). Các microservices này chứa instrumentation phát ra dữ liệu telemetry dưới dạng metrics, logs và traces. Dữ liệu này được xuất vào OpenTelemetry Collector, đóng vai trò như một gateway trung tâm vendor agnostic để thu thập dữ liệu này một cách thống nhất.

Trong bài viết này, chúng tôi sử dụng một ứng dụng OpenTelemetry demo như một ứng dụng enterprise mẫu. Các khách hàng enterprise lớn thường tách dữ liệu observability signal của họ thành các store khác nhau để có khả năng mở rộng, fault isolation, access control và dễ dàng vận hành.

Để hỗ trợ các chức năng này, chúng tôi khuyến nghị và sử dụng Amazon OpenSearch Ingestion cho một data pipeline serverless, có thể mở rộng và được quản lý hoàn toàn. Chúng tôi tách dữ liệu log và trace và gửi chúng đến các OpenSearch Service domain riêng biệt. Giải pháp cũng gửi dữ liệu metrics đến Amazon Managed Service for Prometheus.

Chúng tôi sử dụng Amazon Managed Grafana như một platform data visualization và analytics để truy vấn và visualize dữ liệu này. Chúng tôi cũng chỉ ra cách sử dụng correlations như một công cụ có giá trị để có được insights từ các tín hiệu này được trải rộng trên các data store khác nhau.

## Yêu cầu tiên quyết

Hoàn thành các bước tiên quyết sau:

1. **Provision và configure Amazon Managed Prometheus workspace** để nhận metrics từ OpenTelemetry Collector.
2. **Tạo hai OpenSearch Service domain chuyên dụng** (hoặc sử dụng các domain hiện có) để ingest logs và traces từ OpenTelemetry Collector.
3. **Tạo Amazon Managed Grafana workspace** và configure data source để kết nối với Amazon Managed Prometheus và OpenSearch Service.
4. **Set up EKS cluster** để deploy applications và OpenTelemetry Collector.

## Tạo pipeline OpenSearch Ingestion cho logs và traces

Trước khi thiết lập các ingestion pipeline, bạn cần tạo các AWS Identity and Access Management (IAM) policies và roles cần thiết. Quá trình này bao gồm việc tạo hai policies cho domain và OSIS access, sau đó tạo một pipeline role sử dụng các policies này.

### Tạo policy cho ingestion

Hoàn thành các bước sau để tạo IAM policy:

1. Mở **IAM console**.
2. Chọn **Policies** trong navigation pane, sau đó chọn **Create policy**.
3. Trên tab **JSON**, nhập policy sau vào editor:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "es:DescribeDomain",
            "Resource": "arn:aws:es:*:{accountId}:domain/*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "es:ESHttpGet", 
                "es:HttpHead", 
                "es:HttpDelete", 
                "es:HttpPatch", 
                "es:HttpPost", 
                "es:HttpPut"
            ],
            "Resource": "arn:aws:es:us-east-1:{accountId}:domain/otel-traces"
        },
        {
            "Effect": "Allow",
            "Action": [
                "es:ESHttpGet", 
                "es:HttpHead", 
                "es:HttpDelete", 
                "es:HttpPatch", 
                "es:HttpPost", 
                "es:HttpPut"
            ],
            "Resource": "arn:aws:es:us-east-1:{accountId}:domain/otel-logs"
        }
    ]
}

// Thay thế {accountId} bằng account ID của bạn
```

4. Chọn **Next**, chọn **Next** lần nữa, và đặt tên policy là `domain-policy`.
5. Chọn **Create policy**.
6. Tạo một policy khác với tên `osis-policy` và sử dụng JSON sau:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "osis:Ingest",
            "Resource": "arn:aws:osis:us-east-1:{accountId}:pipeline/osi-pipeline-otellogs"
        },
        {
            "Effect": "Allow",
            "Action": "osis:Ingest",
            "Resource": "arn:aws:osis:us-east-1:{accountId}:pipeline/osi-pipeline-oteltraces"
        }
    ]
}
// Thay thế {accountId} bằng account ID của bạn
```

### Tạo pipeline role

Hoàn thành các bước sau để tạo pipeline role:

1. Trên IAM console, chọn **Roles** trong navigation pane, sau đó chọn **Create role**.
2. Chọn **Custom trust policy** và nhập policy sau vào editor:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": [
                    "eks.amazonaws.com",
                    "osis-pipelines.amazonaws.com"
                ],
                "AWS": "{nodegroup_arn}"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}

// Thay thế {nodegroup_arn} bằng ARN nodegroup của bạn
```

3. Chọn **Next**, sau đó tìm kiếm và chọn các policies `osis-policy` và `domain-policy` bạn vừa tạo.
4. Chọn **Next** và đặt tên role là `PipelineRole`.
5. Chọn **Create role**.

### Cho phép access cho pipeline role trong OpenSearch Service domains

Để enable access cho pipeline role trong OpenSearch Service domains, hoàn thành các bước sau:

1. Mở **OpenSearch Service console**.
2. Chọn domain của bạn (logs hoặc traces).
3. Chọn **OpenSearch Dashboards URL**.
4. Đăng nhập với credentials của bạn.

Sau đó, hoàn thành các bước sau cho mỗi OpenSearch Service domain (logs và traces domains):

5. Trong OpenSearch Dashboards, đi đến **Security**.
6. Chọn **Roles** và sau đó **all_access**.

> **Lưu ý bảo mật**: Thủ tục này sử dụng role `all_access` chỉ cho mục đích demo. Điều này cấp full administrative privileges cho pipeline role, vi phạm nguyên tắc least privilege và có thể gây rủi ro bảo mật. Cho production environments, bạn nên tạo custom role với minimal permissions cần thiết cho data ingestion.

7. Chọn **Mapped users** và sau đó **Managed mapping**.
8. Trên trang **Map user**, dưới **Backend roles**, cập nhật backend role với Amazon Resource Name (ARN) cho role `PipelineRole`.
9. Chọn **Map**.

### Tạo pipeline cho logs

Hoàn thành các bước sau để tạo pipeline cho logs:

1. Mở **OpenSearch Service console**.
2. Chọn **Ingestion pipelines**.
3. Chọn **Create pipeline**.
4. Định nghĩa pipeline configuration bằng cách nhập như sau:

```yaml
version: "2"
otel-logs-pipeline:
  source:
    otel_logs_source:
      path: "/v1/logs"
  sink:
    - opensearch:
        hosts: ["{OpenSearch_domain_endpoint}"]
        aws:
          sts_role_arn: "arn:aws:iam::{accountId}:role/osi-pipeline-role"
          region: "us-east-1"
          serverless: false
        index: "observability-otel-logs%{yyyy-MM-dd}"

# Để lấy các giá trị cho placeholders:
# 1. {OpenSearch_domain_endpoint}: Bạn có thể tìm domain endpoint bằng cách 
#    navigate đến Amazon Managed OpenSearch managed clusters trong AWS Management Console
# Sau khi có các giá trị cần thiết, thay thế placeholders trong configuration 
# bằng các giá trị thực tế.
```

### Tạo pipeline cho traces

Hoàn thành các bước sau để tạo pipeline cho traces:

1. Mở **OpenSearch Service console**.
2. Chọn **Ingestion pipelines**.
3. Chọn **Create pipeline**.
4. Định nghĩa pipeline configuration bằng cách nhập như sau:

```yaml
version: "2"
entry-pipeline:
  source:
    otel_trace_source:
      path: "/v1/traces"
  processor:
    - trace_peer_forwarder:
  sink:
    - pipeline:
        name: "span-pipeline"
    - pipeline:
        name: "service-map-pipeline"

span-pipeline:
  source:
    pipeline:
      name: "entry-pipeline"
  processor:
    - otel_traces:
  sink:
    - opensearch:
        index_type: "trace-analytics-raw"
        hosts: ["{OpenSearch_domain_endpoint}"]
        aws:               
          sts_role_arn: "arn:aws:iam::{accountId}:role/osi-pipeline-role"
          region: "us-east-1"

service-map-pipeline:
  source:
    pipeline:
      name: "entry-pipeline"
  processor:
    - service_map:
  sink:
    - opensearch:
        index_type: "trace-analytics-service-map"
        hosts: ["{OpenSearch_domain_endpoint}"]
        aws:               
          sts_role_arn: "arn:aws:iam::{accountId}:role/osi-pipeline-role"
          region: "us-east-1"

# Để lấy các giá trị cho placeholders:
# 1. {OpenSearch_domain_endpoint}: Domain endpoint
# 2. {accountId}: AWS account ID của bạn
# Sau khi có các giá trị cần thiết, thay thế placeholders bằng giá trị thực tế.
```

## Cài đặt ứng dụng OpenTelemetry demo trên Amazon EKS

Sử dụng EKS cluster bạn đã setup trước đó cùng với AWS CloudShell hoặc công cụ khác để hoàn thành các bước này:

1. Mở **AWS Management Console**.
2. Chọn CloudShell icon ở top navigation bar, hoặc đi trực tiếp đến **CloudShell console**.
3. Đợi shell environment khởi tạo—nó đi kèm với các AWS Command Line Interface (AWS CLI) tools phổ biến được pre-install.

Bây giờ bạn có thể hoàn thành các bước sau để cài đặt application:

4. Clone **OpenTelemetry Demo** repository:

```bash
git clone https://github.com/aws-samples/sample-correlation-opensearch-repository
```

5. Navigate đến Kubernetes directory:

```bash
cd deployment_files
```

6. Deploy demo application sử dụng kubectl apply:

```bash
kubectl apply -f .
```

7. Sử dụng load balancer để expose frontend service để bạn có thể reach source application web URL:

```bash
kubectl expose deployment opentelemetry-demo-frontendproxy --type=LoadBalancer --name=frontendproxy
```

8. Sau khi bạn đã deploy application, truy cập frontend application sử dụng load balancer trên port 8080. Sử dụng browser để visit `http://<LoadBalancerIP>:8080/` để mở source application cho OpenTelemetry.

Bằng cách tuân theo các bước này, bạn có thể cài đặt và truy cập demo applications thành công trên EKS cluster của mình.

## Cấu hình OpenTelemetry Collector exporter

OpenTelemetry Collector là một tool quản lý việc receiving, processing và exporting telemetry data từ application của bạn đến target repository.

Trong bước này, chúng tôi gửi logs và traces đến OpenSearch Service và metrics đến Amazon Managed Prometheus. OpenTelemetry Collector cũng hoạt động với các data repository phổ biến như Jaeger và nhiều platform open source và commercial khác.

Hoàn thành các bước sau:

1. Mở otel-collector-config ConfigMap trong editor ưa thích của bạn:

```bash
kubectl edit configmap opentelemetry-demo-otelcol -n otel-demo
```

2. Cập nhật exporters section với configuration sau (cung cấp Amazon Managed Service for Prometheus endpoint và OpenSearch Service log ingestion URLs phù hợp):

```yaml
exporters:
     logging: {}
     otlphttp/logs:
       logs_endpoint: "<AWS_OPENSEARCH_LOG_INGESTION_URL>/v1/logs"
       auth:
         authenticator: sigv4auth
       compression: none
     otlphttp/traces:
       traces_endpoint: "<AWS_OPENSEARCH_TRACE_INGESTION_URL>/v1/traces"
       auth:
         authenticator: sigv4auth
       compression: none
     prometheusremotewrite:
        endpoint: "<AWS_MANAGED_PROMETHEUS_ENDPOINT>"
        auth:
          authenticator: sigv4auth 
```

3. Locate extensions section và cập nhật IAM role ARN trong sigv4auth configuration:

```yaml
sigv4auth:
        assume_role:
            arn: "arn:aws:iam::{accountId}:role/osi-pipeline-role"
            sts_region: "us-east-1"
        region: "us-east-1"
        service: "osis"
# {accountId}: thay thế accountID bằng account id của bạn
```

4. Sau khi cập nhật ConfigMap, restart OpenTelemetry Collector deployment:

```bash
kubectl rollout restart deployment opentelemetry-demo-otelcol -n otel-demo
```

Với những thay đổi này, OpenTelemetry Collector sẽ gửi trace data đến OpenSearch Service domain, metrics data đến AWS Managed Service for Prometheus endpoint, và log data đến OpenSearch Service domain.

## Cấu hình Amazon Managed Grafana

Trước khi bạn có thể visualize logs và traces của mình, bạn cần configure OpenSearch Service như một data source trong Amazon Managed Grafana workspace. Configuration này được thực hiện thông qua Amazon Managed Grafana console.

### Cấu hình OpenSearch Service data source

Hoàn thành các bước sau để configure OpenSearch Service data source:

1. Mở **Amazon Managed Grafana console**.
2. Chọn workspace của bạn và chọn workspace URL để truy cập Grafana instance.
3. Đăng nhập vào Amazon Managed Grafana instance.
4. Từ side menu, chọn configuration (gear) icon.
5. Trên Configuration menu, chọn **Data Sources**.
6. Chọn **Add data source**.
7. Trên trang **Add data source**, chọn **OpenSearch Service** từ danh sách available data sources.
8. Trong trường **Name**, nhập tên mô tả cho data source.
9. Trong trường **URL**, nhập URL (OpenSearch Service domain endpoint) của OpenSearch Service domain, bao gồm protocol và port number.
10. Nếu OpenSearch cluster được configure với authentication, cung cấp credentials cần thiết trong trường **User** và **Password**.
11. Nếu bạn muốn sử dụng index pattern cụ thể cho data source, bạn có thể specify nó trong trường **Index name** (Ví dụ: `logstash-*`).
12. Điều chỉnh bất kỳ setting nào khác cần thiết, như **Time field name** và **Time interval**.
13. Chọn **Save & Test** để verify connection đến OpenSearch cluster.

Nếu test thành công, bạn sẽ thấy green notification với message "Data source is working."

14. Chọn **Save** để lưu data source configuration.
15. Lặp lại các bước tương tự cho OpenSearch logs và traces domains.

### Cấu hình Prometheus data source

Hoàn thành các bước sau để configure Prometheus data source:

1. Mở **Amazon Managed Grafana console**.
2. Chọn workspace của bạn và chọn workspace URL để truy cập Grafana instance.
3. Đăng nhập vào Amazon Managed Grafana instance.
4. Từ side menu, chọn configuration (gear) icon.
5. Trên Configuration menu, chọn **Data Sources**.
6. Chọn **Add data source**.
7. Trên trang **Add data source**, chọn **Amazon Managed Prometheus** từ danh sách available data sources.
8. Trong trường **Name**, nhập tên mô tả cho data source.
9. Các trường **AWS Auth Provider** và **Default Region** sẽ được tự động populate dựa trên Amazon Managed Grafana workspace configuration.
10. Trong trường **Workspace**, nhập ID hoặc alias của Amazon Managed Prometheus workspace.
11. Chọn **Save & Test** để verify connection đến Amazon Managed Prometheus workspace.

Nếu test thành công, bạn sẽ thấy green notification với message "Data source is working."

12. Chọn **Save** để lưu data source configuration.

## Tạo correlations trong Amazon Managed Grafana

Để establish connections giữa logs và traces data, bạn cần setup data correlations trong Amazon Managed Grafana. Điều này cho phép bạn navigate seamlessly giữa related logs và traces. Tuân theo các bước này trong Amazon Managed Grafana workspace:

1. Mở **Amazon Managed Grafana console**.
2. Chọn workspace của bạn và chọn workspace URL để truy cập Grafana instance.
3. Trong Amazon Managed Grafana portal, trên **Administration** menu, chọn **Plugins and Data**, và chọn **Correlation**.
4. Trên trang **Set up the target for the correlation**, dưới **Target**, chọn traces data source của bạn (OpenSearch Service, ví dụ `otel-traces`) từ dropdown list và define query sẽ execute khi link được followed. Bạn có thể sử dụng variables để query specific field values. Ví dụ: `traceId: ${__value.raw}`.
5. Trên trang **Set up the target for the correlation**, chọn log data source từ dropdown list, và nhập field name sẽ được linked hoặc correlated với traces data source trong OpenSearch Service data source. Ví dụ: `traceID`.
6. Chọn **Save** để complete correlation configuration.
7. Lặp lại các bước để tạo correlation giữa metrics trên Prometheus với logs trong OpenSearch Service.

## Xác thực kết quả

Trong Amazon Managed Grafana, sử dụng Prometheus data source, locate desired instance cho correlation. Instance ID sẽ được hiển thị như một link. Follow link để mở corresponding log details trong panel bên phải của page.

Với logs to traces correlation được configure, bạn có thể truy cập trace information trực tiếp từ logs page. Chọn traces trên log details panel để view corresponding trace data.

Screenshot sau minh họa node graph visualization cho thấy correlation flow: instance metrics to logs to traces.

## Dọn dẹp

Remove infrastructure cho solution này khi không sử dụng để tránh phát sinh chi phí không cần thiết.

## Kết luận

Trong bài viết này, chúng tôi đã chỉ ra cách sử dụng correlation như một công cụ hữu ích để có được insight vào observability data được stored trong các store khác nhau.

Việc tách logs và traces thành dedicated domains cung cấp các lợi ích sau:

- **Better resource allocation** và scaling dựa trên các workload pattern khác nhau
- **Independent performance optimization** cho mỗi loại data
- **Simplified cost tracking** và management
- **Enhanced security control** với separate access policies

Bạn có thể sử dụng solution này như một reference để xây dựng scalable observability solution cho enterprise của mình để detect, investigate và remediate problems nhanh hơn. Khả năng này, khi được sử dụng cùng với next-generation artificial intelligence và machine learning (AI/ML), giúp không chỉ proactively react mà còn predict và prevent problems trước khi chúng xảy ra.

---

## 📖 Glossary - Thuật ngữ

| English                        | Tiếng Việt                             | Định nghĩa                                                                               |
| ------------------------------ | ---------------------------------------- | ------------------------------------------------------------------------------------------- |
| Telemetry Data                 | Dữ liệu điện toán                   | Dữ liệu được thu thập tự động từ hệ thống để monitor performance và behavior |
| Observability                  | Khả năng quan sát                     | Khả năng hiểu trạng thái nội bộ của hệ thống thông qua dữ liệu bên ngoài     |
| Correlation                    | Kết nối/Tương quan                   | Quá trình liên kết các dữ liệu khác nhau để tìm ra mối quan hệ                 |
| Mean Time to Resolution (MTTR) | Thời gian trung bình để giải quyết | Metric đo thời gian trung bình để resolve một incident                                |
| Root Cause Analysis            | Phân tích nguyên nhân gốc           | Quá trình xác định nguyên nhân cốt lõi của vấn đề                              |
| OpenTelemetry                  | OpenTelemetry                            | Framework mã nguồn mở để collect, process và export telemetry data                    |
| Traces                         | Traces                                   | Ghi lại journey của request qua distributed system                                        |
| Metrics                        | Metrics                                  | Các measurement định lượng về system performance                                      |
| Logs                           | Logs                                     | Structured hoặc unstructured text records của events                                      |
| Ingestion                      | Ingestion                                | Quá trình thu thập và import dữ liệu vào hệ thống                                  |
| Pipeline                       | Pipeline                                 | Chuỗi các bước xử lý dữ liệu từ source đến destination                           |
| Data Source                    | Nguồn dữ liệu                         | Nơi cung cấp dữ liệu cho visualization tools                                            |
| Visualization                  | Trực quan hóa                          | Hiển thị dữ liệu dưới dạng charts, graphs và dashboards                             |
| Microservices                  | Microservices                            | Architectural pattern chia application thành small services                                |
| Instrumentation                | Instrumentation                          | Code được thêm vào application để collect telemetry data                             |
| Service Map                    | Service Map                              | Visual representation của dependencies giữa services                                      |

## 🔗 Tài liệu tham khảo

### Tài liệu gốc

- [Correlate telemetry data with Amazon OpenSearch Service and Amazon Managed Grafana](https://aws.amazon.com/blogs/big-data/correlate-telemetry-data-with-amazon-opensearch-service-and-amazon-managed-grafana/): Bài viết gốc
- [OpenTelemetry Demo](https://opentelemetry.io/docs/demo/): Demo application được sử dụng trong bài viết
- [OpenTelemetry Collector](https://opentelemetry.io/docs/concepts/components/#collector): Documentation về OpenTelemetry Collector

### Tài liệu tiếng Việt

- [Amazon OpenSearch Service Documentation](https://docs.aws.amazon.com/opensearch-service/): Hướng dẫn sử dụng OpenSearch Service
- [Amazon Managed Grafana User Guide](https://docs.aws.amazon.com/grafana/latest/userguide/): Hướng dẫn sử dụng Managed Grafana
- [Amazon EKS User Guide](https://docs.aws.amazon.com/eks/latest/userguide/): Hướng dẫn sử dụng EKS

### Tools và Services

- [Amazon OpenSearch Service](https://aws.amazon.com/opensearch-service/): Managed search và analytics service
- [Amazon Managed Grafana](https://aws.amazon.com/grafana/): Managed data visualization service
- [Amazon Managed Service for Prometheus](https://aws.amazon.com/prometheus/): Managed monitoring service
- [Amazon EKS](https://aws.amazon.com/eks/): Managed Kubernetes service
- [AWS CloudShell](https://aws.amazon.com/cloudshell/): Browser-based shell environment

### Observability và Monitoring

- [AWS Observability](https://aws.amazon.com/products/management-and-governance/use-cases/monitoring-and-observability/): AWS observability solutions
- [OpenTelemetry](https://opentelemetry.io/): Observability framework
- [Grafana Correlations](https://docs.aws.amazon.com/grafana/latest/userguide/v10-explore-correlations.html): Hướng dẫn về correlations trong Grafana

---

## 💬 Ghi chú của người dịch

### Challenges trong quá trình dịch

- **Technical Terminology**: Nhiều thuật ngữ observability và monitoring cần giữ nguyên tiếng Anh để tránh hiểu nhầm (OpenTelemetry, traces, metrics, logs)
- **Configuration Details**: Các configuration YAML và JSON cần được giữ nguyên format và syntax
- **AWS Service Names**: Tên các dịch vụ AWS được giữ nguyên để đảm bảo tính chính xác

### Insights gained

- **Observability Architecture**: Hiểu sâu hơn về cách xây dựng hệ thống observability comprehensive với AWS services
- **Data Correlation**: Nắm được tầm quan trọng của việc correlate different telemetry signals để improve troubleshooting
- **Scalable Monitoring**: Học được best practices để build scalable monitoring solution cho enterprise applications

### Lưu ý về implementation

Bài viết này cung cấp hướng dẫn chi tiết để implement observability solution, tuy nhiên cần lưu ý:

- **Security Considerations**: Production environments cần implement proper IAM roles với least privilege principles
- **Cost Optimization**: Monitor và optimize chi phí cho OpenSearch domains và data retention policies
- **Performance Tuning**: Fine-tune ingestion pipelines và query performance dựa trên workload patterns
- **Monitoring Scale**: Adjust resource allocation dựa trên volume của telemetry data

---

## 🤝 Đóng góp và Feedback

Bài dịch này được thực hiện trong khuôn khổ **FCJ Internship Program**.

**📧 Liên hệ**: tritrantnt@gmail.com
**💬 Feedback**: Mọi góp ý để cải thiện chất lượng dịch thuật xin gửi về email trên
**🔄 Updates**: Bài dịch sẽ được cập nhật dựa trên feedback từ cộng đồng

---

*© 2025 - Bản dịch thuộc về Trần Ngọc Tri. Vui lòng credit khi sử dụng.*
