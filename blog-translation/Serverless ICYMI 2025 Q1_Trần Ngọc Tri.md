# Tóm tắt Serverless ICYMI Quý 1/2025 - Cập nhật các dịch vụ Serverless AWS mới nhất

> **📖 Bài viết gốc**: [Serverless ICYMI 2025 Q1](https://aws.amazon.com/blogs/compute/serverless-icymi-2025-q1/)  
> **👤 Tác giả**: Julian Wood - AWS Developer Advocate  
> **📅 Ngày xuất bản**: 07/04/2025  
> **🌐 Nguồn**: AWS Compute Blog  
> **👨‍💻 Người dịch**: Trần Ngọc Tri - FCJ Intern  
> **📅 Ngày dịch**: 11/07/2025  
> **⏱️ Thời gian đọc**: 25 phút

---

## 📋 Tóm tắt

Bài viết này là phiên bản thứ 28 của bản tóm tắt quý AWS Serverless ICYMI (in case you missed it), tập hợp các thông tin quan trọng nhất trong quý 1 năm 2025. Nội dung bao gồm các cập nhật mới về AWS Step Functions với Workflow Studio trong VS Code, AWS Lambda với khả năng xử lý hàng tỷ invocation, Amazon Bedrock với multi-agent collaboration, Amazon Q Developer CLI agent, và nhiều tính năng mới của các dịch vụ serverless khác như AppSync, SNS, EventBridge. Đây là tài liệu tham khảo toàn diện cho developers và architects muốn cập nhật xu hướng công nghệ serverless mới nhất.

**🎯 Đối tượng đọc**: Developers, Solutions Architects, DevOps Engineers  
**📊 Độ khó**: Intermediate  
**🏷️ Tags**: AWS, Serverless, Step Functions, Lambda, Bedrock, Q Developer

---

## 📚 Mục lục

- [AWS Step Functions](#aws-step-functions)
- [Developer Tools](#developer-tools)
- [AWS Lambda](#aws-lambda)
- [Tính năng Amazon Bedrock](#tính-năng-amazon-bedrock)
- [Model Amazon Bedrock mới](#model-amazon-bedrock-mới)
- [Amazon Q Developer](#amazon-q-developer)
- [AWS AppSync](#aws-appsync)
- [Amazon SNS](#amazon-sns)
- [Amazon EventBridge](#amazon-eventbridge)
- [AWS Amplify](#aws-amplify)
- [Bài viết blog Serverless compute](#bài-viết-blog-serverless-compute)
- [Serverless Office Hours weekly livestream](#serverless-office-hours-weekly-livestream)
- [Glossary - Thuật ngữ](#glossary---thuật-ngữ)
- [Tài liệu tham khảo](#tài-liệu-tham-khảo)

---

Chào mừng bạn đến với phiên bản thứ 28 của bản tóm tắt quý AWS Serverless ICYMI (in case you missed it). Vào cuối mỗi quý, chúng tôi chia sẻ những sản phẩm mới được ra mắt gần đây nhất, các cải tiến tính năng, bài viết blog, video, livestream và những điều thú vị khác mà bạn có thể đã bỏ lỡ!

Trong trường hợp bạn bỏ lỡ bản ICYMI cuối cùng của chúng tôi, hãy xem những gì đã xảy ra trong quý 4 năm 2024 tại [đây](https://aws.amazon.com/blogs/compute/serverless-icymi-q4-2024/).

![Lịch Serverless Quý 1/2025](https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2025/04/03/Serverless-calendar-Q1-2025.png)
*Lịch Serverless Quý 1/2025*

## AWS Step Functions

Đội ngũ [AWS Step Functions](https://aws.amazon.com/step-functions/) tiếp tục cải thiện trải nghiệm nhà phát triển. Workflow Studio hiện đã có sẵn trong Visual Studio Code (VS Code) thông qua extension [AWS Toolkit](https://aws.amazon.com/visualstudiocode/).

![AWS Step Functions trong IDE](https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2025/04/03/AWS-Step-Functions-in-IDE.jpeg)
*AWS Step Functions trong IDE*

Bây giờ bạn có thể thiết kế, kiểm thử và triển khai các workflow Step Functions mà không cần rời khỏi IDE của mình. Extension này cung cấp giao diện kéo-thả với tất cả các khả năng quen thuộc của Workflow Studio, giúp việc xây dựng state machine cục bộ trở nên dễ dàng hơn.

Để bắt đầu, hãy cài đặt [AWS Toolkit cho Visual Studio Code](https://aws.amazon.com/visualstudiocode/) và tham khảo [hướng dẫn người dùng](https://docs.aws.amazon.com/toolkit-for-vscode/latest/userguide/stepfunctions-workflowstudio.html) về tích hợp Workflow Studio.

Step Functions private integrations giờ đây cho phép bạn tích hợp các ứng dụng một cách liền mạch qua mạng riêng, cơ sở hạ tầng on-premises và các nền tảng cloud. Tìm hiểu thêm trong [bài viết blog](https://aws.amazon.com/blogs/compute/simplifying-private-api-integrations-with-amazon-eventbridge-and-aws-step-functions-2/) và [video giải thích](https://www.youtube.com/watch?v=gVO5YNVDg-k).

Step Functions giờ đây [tích hợp với 36 dịch vụ AWS khác](https://aws.amazon.com/about-aws/whats-new/2025/01/aws-step-functions-integration-36-services-user-messaging/) hỗ trợ khả năng nhắn tin người dùng. Bạn có thể điều phối thông báo thông qua [Amazon SNS](https://aws.amazon.com/sns/), [Amazon SQS](https://aws.amazon.com/sqs/), [Amazon EventBridge](https://aws.amazon.com/eventbridge/), [Amazon Pinpoint](https://aws.amazon.com/pinpoint/), v.v., tất cả đều sử dụng các tích hợp được tối ưu hóa mà bạn đã quen thuộc.

Step Functions đã [tăng quota mặc định](https://aws.amazon.com/about-aws/whats-new/2025/02/aws-step-functions-100-000-state-machines-activities-account/) cho state machine và activity từ 10.000 lên 100.000 trên mỗi tài khoản AWS. Việc tăng gấp mười lần này có nghĩa là bạn có thể tạo nhiều workflow hơn để tự động hóa các quy trình kinh doanh mà không lo lắng về việc đạt đến giới hạn quota.

[Distributed Map](https://docs.aws.amazon.com/step-functions/latest/dg/state-map-distributed.html) đang mở rộng khả năng bằng cách thêm hỗ trợ cho định dạng [JSON Lines (JSONL)](https://jsonlines.org/). JSONL, một định dạng văn bản hiệu quả cao, lưu trữ dữ liệu có cấu trúc dưới dạng các đối tượng JSON riêng lẻ được phân tách bằng dòng mới, đặc biệt phù hợp để xử lý các tập dữ liệu lớn.

![AWS Step Functions Distributed Map](https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2025/04/03/AWS-Step-Functions-Distributed-Map.png)
*AWS Step Functions Distributed Map*

Distributed Map cũng có thể xử lý dữ liệu từ [phạm vi rộng hơn các định dạng file phân cách](https://aws.amazon.com/about-aws/whats-new/2025/02/aws-step-functions-data-source-output-option-distributed-map/) được lưu trữ trong [Amazon S3](https://aws.amazon.com/s3/) và cung cấp các phép biến đổi đầu ra mới để kiểm soát định dạng kết quả tốt hơn.

## Developer Tools

[Serverless Land patterns](https://serverlessland.com/patterns) giờ đây có sẵn [trực tiếp trong VS Code](https://aws.amazon.com/about-aws/whats-new/2025/03/ready-to-use-serverless-land-patterns-vs-code-ide/).

![Serverless Land Patterns trong IDE](https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2025/04/03/Serverless-Land-Patterns-in-IDE.png)

Bạn không còn cần chuyển đổi giữa IDE và các tài nguyên bên ngoài khi xây dựng kiến trúc serverless. Duyệt, tìm kiếm và triển khai các pattern serverless được xây dựng sẵn trực tiếp trong VS Code.

![Ví dụ Serverless Pattern](https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2025/04/03/Example-Serverless-Pattern.png)
*Ví dụ Serverless Pattern*

## AWS Lambda

Tìm hiểu cách [AWS Lambda](https://aws.amazon.com/lambda/) xử lý hàng tỷ lần gọi.

![AWS Lambda asynchronous invocations](https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2025/04/03/AWS-Lambda-asynchronous-invocations.png)
*AWS Lambda asynchronous invocations*

[Bài viết blog](https://aws.amazon.com/blogs/compute/handling-billions-of-invocations-best-practices-from-aws-lambda/) này cung cấp các khuyến nghị và insights để triển khai các ứng dụng phân tán cao dựa trên kinh nghiệm của đội ngũ dịch vụ Lambda trong việc xây dựng hệ thống xử lý sự kiện bất đồng bộ mạnh mẽ. Nó đi sâu vào những thách thức bạn có thể gặp phải, các kỹ thuật giải pháp và best practice để xử lý noisy neighbor.

Một [video](https://www.youtube.com/watch?v=rhBOuJqzABY) mới hướng dẫn cách sử dụng [trải nghiệm IDE cục bộ được cải tiến](https://aws.amazon.com/blogs/compute/introducing-an-enhanced-local-ide-experience-for-aws-lambda-developers/) cho các nhà phát triển Lambda.

Extension VS Code cho Lambda giờ đây hỗ trợ [live tailing của CloudWatch Logs trực tiếp trong IDE](https://aws.amazon.com/about-aws/whats-new/2025/03/aws-lambda-cloudwatch-logs-live-tail-vs-code-ide/) tiếp theo sau hỗ trợ trước đó cho [Live Tail trong Lambda console](https://aws.amazon.com/blogs/compute/simplifying-lambda-function-development-using-cloudwatch-logs-live-tail-and-metrics-insights/). Xem log theo thời gian thực khi các function của bạn thực thi, làm cho debugging và troubleshooting hiệu quả hơn bao giờ hết.

Bây giờ bạn có thể [kích hoạt Application Performance Monitoring (APM)](https://aws.amazon.com/blogs/aws/track-performance-of-serverless-applications-built-using-aws-lambda-with-application-signals/) cho Java và .NET runtime bằng [Amazon CloudWatch Application Signals](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Application-Monitoring-Sections.html).

![Amazon CloudWatch Application Signals cho Java và .NET AWS Lambda runtime](https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2025/04/03/Amazon-CloudWatch-Application-Signals-for-Java-and-.NET-AWS-Lambda-runtimes-.png)
*Amazon CloudWatch Application Signals cho Java và .NET AWS Lambda runtime*

Điều này cung cấp khả năng hiển thị sâu sắc về hiệu suất function của bạn, bao gồm tracing cấp method, memory profiling và phát hiện bất thường tự động.

## Tính năng Amazon Bedrock

[Multi-agent collaboration](https://aws.amazon.com/about-aws/whats-new/2025/03/amazon-bedrock-multi-agent-collaboration/) hiện có sẵn trong Bedrock dưới dạng preview, cho phép bạn tạo hệ thống nơi nhiều AI agent làm việc cùng nhau để giải quyết các vấn đề phức tạp. Các agent có thể chuyên môn trong các lĩnh vực khác nhau, chia sẻ ngữ cảnh và phối hợp hành động để đạt được mục tiêu mà một agent đơn lẻ khó có thể thực hiện.

[RAG evaluation](https://aws.amazon.com/about-aws/whats-new/2025/03/amazon-bedrock-rag-evaluation-generally-available/) giờ đây đã general available. Điều này cung cấp metrics để đánh giá và cải thiện pipeline retrieval augmented generation của bạn. GraphRAG cho Bedrock Knowledge Bases [giờ đây đã general available](https://aws.amazon.com/about-aws/whats-new/2025/03/amazon-bedrock-knowledge-bases-graphrag-generally-available/), cho phép bạn tăng cường retrieval với ngữ cảnh dựa trên đồ thị.

[Amazon Bedrock Flows](https://aws.amazon.com/bedrock/flows/) giờ đây hỗ trợ [multi-turn conversation](https://aws.amazon.com/blogs/machine-learning/introducing-multi-turn-conversation-with-an-agent-node-for-amazon-bedrock-flows-preview/), cho phép bạn xây dựng các ứng dụng AI động duy trì ngữ cảnh qua nhiều tương tác của người dùng. [Bedrock data automation](https://aws.amazon.com/about-aws/whats-new/2025/03/amazon-bedrock-data-automation-generally-available/) giờ đây đã general available, tinh gọn quá trình chuẩn bị, thu thập và duy trì dữ liệu cho các ứng dụng GenAI của bạn.

Bedrock giờ đây cung cấp khả năng [LLM-as-a-judge](https://aws.amazon.com/about-aws/whats-new/2025/03/amazon-bedrock-model-evaluation-llm-as-a-judge/) để đánh giá model, cung cấp đánh giá tự động về đầu ra model mà không cần người đánh giá. So sánh các model khác nhau hoặc chiến lược prompt với các tiêu chí cụ thể của bạn ở quy mô lớn.

[Khả năng của Bedrock giờ đây được tích hợp vào](https://aws.amazon.com/about-aws/whats-new/2025/03/amazon-bedrocks-capabilities-amazon-sagemaker-unified-studio/) [Amazon SageMaker Unified Studio](https://aws.amazon.com/sagemaker/unified-studio/), tạo ra trải nghiệm liền mạch cho các nhà thực hành machine learning muốn kết hợp foundation model vào workflow của họ. Truy cập các model Bedrock, fine-tuning và đánh giá trực tiếp từ SageMaker.

[Amazon Nova](https://aws.amazon.com/ai/generative-ai/nova/) là thế hệ mới của foundation model tiên tiến cung cấp trí thông minh tiên phong và hiệu suất giá-hiệu suất dẫn đầu ngành. Nova đã mở rộng khả năng sử dụng công cụ và converse API, giúp các nhà phát triển dễ dàng xây dựng AI assistant có thể sử dụng công cụ bên ngoài để hoàn thành nhiệm vụ.

[Amazon Bedrock Guardrails](https://aws.amazon.com/bedrock/guardrails/) image content filter giờ đây đã general available. Định nghĩa và thực thi ranh giới cho các ứng dụng AI của bạn với điều khiển cho cả nội dung văn bản và hình ảnh, đảm bảo đầu ra phù hợp với chính sách của tổ chức bạn.

Bedrock Knowledge Bases giờ đây [hỗ trợ sử dụng cluster OpenSearch hiện có của bạn](https://aws.amazon.com/about-aws/whats-new/2025/03/amazon-bedrock-knowledge-bases-opensearch-cluster-vector-storage/) làm backend lưu trữ vector. Tích hợp này cho phép bạn tận dụng đầu tư vào OpenSearch trong khi hưởng lợi từ khả năng RAG được quản lý của Bedrock.

## Model Amazon Bedrock mới

• [Claude 3.7 Sonnet hybrid reasoning](https://aws.amazon.com/blogs/aws/anthropics-claude-3-7-sonnet-the-first-hybrid-reasoning-model-is-now-available-in-amazon-bedrock/) của Anthropic cho phép bạn chuyển đổi giữa chế độ suy nghĩ tiêu chuẩn và mở rộng. Ở chế độ tiêu chuẩn, nó hoạt động như phiên bản nâng cấp của Claude 3.5 Sonnet. Trong khi ở chế độ suy nghĩ mở rộng, nó sử dụng tự phản ánh để đạt được kết quả cải thiện qua nhiều loại nhiệm vụ.

• [DeepSeek R1](https://aws.amazon.com/blogs/aws/deepseek-r1-now-available-as-a-fully-managed-serverless-model-in-amazon-bedrock/), một model tiên tiến chuyên về nghiên cứu và lý luận khoa học xuất sắc trong các nhiệm vụ giải quyết vấn đề phức tạp và tạo nội dung kỹ thuật.

• Model [Cohere Embed 3](https://aws.amazon.com/about-aws/whats-new/2025/01/amazon-bedrock-multimodal-cohere-embed-3-multilingual-english/) giờ đây có sẵn trong cả phiên bản đa ngôn ngữ và tiếng Anh cụ thể. Các model embedding này hỗ trợ văn bản và hình ảnh, cung cấp biểu diễn chính xác hơn cho nội dung đa phương tiện và cải thiện ứng dụng retrieval augmented generation (RAG).

• [Ray2](https://aws.amazon.com/about-aws/whats-new/2025/01/luma-ais-ray2-visual-ai-model-amazon-bedrock/), model AI thị giác mới của Luma AI có khả năng tạo ra hình ảnh thực tế với chuyển động mượt mà, tự nhiên. Bạn có thể sử dụng nó để hiểu hình ảnh, tái tạo cảnh 3D và tạo nội dung thị giác, mở ra những khả năng mới cho các ứng dụng immersive và thị giác.

• Bedrock giờ đây hỗ trợ [fine-tuning các model Llama 3.2 mới nhất của Meta](https://aws.amazon.com/about-aws/whats-new/2025/03/metas-llama-3-2-models-fine-tuning-amazon-bedrock/). Các model nâng cấp này cung cấp hiệu suất cải thiện qua reasoning, coding và các nhiệm vụ đa ngôn ngữ trong khi hiệu quả hơn với tài nguyên tính toán.

## Amazon Q Developer

[Amazon Q Developer](https://aws.amazon.com/q/developer/transform/) giờ đây [có sẵn dưới dạng CLI agent](https://aws.amazon.com/blogs/devops/introducing-the-enhanced-command-line-interface-in-amazon-q-developer/?trk=a59f8d3f-589a-44bf-8569-17c313bd4e50&sc_channel=el), mang phát triển hỗ trợ AI đến command line. Nhận khuyến nghị theo ngữ cảnh, tạo lệnh shell và giải quyết vấn đề coding mà không cần rời khỏi terminal.

![Amazon Q CLI](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/03/13/cli-1-scaffold-animated.gif)
*Amazon Q CLI*

[Amazon Q Developer transformation](https://aws.amazon.com/q/developer/transform/#Java) giờ đây hỗ trợ nâng cấp các ứng dụng Java sử dụng Maven lên Java 21. Nó cung cấp gợi ý code cải tiến, refactoring và khuyến nghị tối ưu hóa cho các ứng dụng sử dụng tính năng Java mới nhất, như virtual thread và pattern matching.

## AWS AppSync

[AWS AppSync](https://aws.amazon.com/appsync/) Events giờ đây hỗ trợ [event publishing cho WebSocket API](https://aws.amazon.com/blogs/mobile/building-real-time-apps-with-aws-appsync-events-websocket-publishing/), cho phép chức năng publish-subscribe thời gian thực. Tính năng này giúp xây dựng ứng dụng cần cập nhật tức thì dễ dàng hơn, như ứng dụng chat, công cụ cộng tác và dashboard thời gian thực.

![AWS AppSync Events](https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2025/04/03/AWS-AppSync-Events.png)
*AWS AppSync Events*

Có các [construct L2 AWS Cloud Development Kit](https://aws.amazon.com/cdk/) (AWS CDK) mới [cho AppSync WebSocket API](https://aws.amazon.com/about-aws/whats-new/2025/02/aws-appsync-cdk-l2-simplify-websocket-apis/). Chúng giúp định nghĩa và triển khai API thời gian thực đơn giản hơn bằng infrastructure as code. Các construct cấp cao này xử lý chi tiết về kết nối WebSocket, authorization và pattern messaging.

## Amazon SNS

Amazon SNS giờ đây hỗ trợ [high throughput mode](https://aws.amazon.com/about-aws/whats-new/2025/01/high-throughput-mode-amazon-sns-fifo-topics/) cho SNS FIFO topic, với throughput mặc định khớp với SNS standard topic. Khi bạn kích hoạt high-throughput mode, SNS FIFO topic sẽ duy trì thứ tự trong message group, trong khi giảm phạm vi de-duplication xuống cấp message-group.

## Amazon EventBridge

[Amazon EventBridge](https://aws.amazon.com/eventbridge/) giờ đây hỗ trợ [giao hàng trực tiếp đến target qua tài khoản AWS](https://aws.amazon.com/blogs/compute/introducing-cross-account-targets-for-amazon-eventbridge-event-buses/), đơn giản hóa kiến trúc đa tài khoản. Điều này giảm độ trễ và cải thiện độ tin cậy khi định tuyến sự kiện giữa các tài khoản trong tổ chức của bạn.

![Amazon EventBridge cross account](https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2025/04/03/Amazon-EventBridge-cross-account.png)
*Amazon EventBridge cross account*

EventBridge console giờ đây có tính năng [event source discovery](https://aws.amazon.com/about-aws/whats-new/2025/01/amazon-eventbridge-event-source-discovery-console/), giúp tìm và hình dung các event source có sẵn trong môi trường AWS của bạn dễ dàng hơn. Công cụ này giúp bạn xác định các event producer tiềm năng và hiểu event schema mà chúng phát ra.

## AWS Amplify

[AWS Amplify](https://aws.amazon.com/amplify/) giờ đây cung cấp [TypeScript data client](https://aws.amazon.com/about-aws/whats-new/2025/01/aws-amplify-typescript-data-client-server-side-lambda/) được tối ưu hóa cho Lambda function phía server, cung cấp truy cập type-safe đến nguồn dữ liệu của bạn. Client này giảm độ phức tạp code và cải thiện độ tin cậy khi làm việc với database và API trong môi trường server.

## Bài viết blog Serverless compute

### Tháng 1

• [Serverless ICYMI Q4 2024](https://aws.amazon.com/blogs/compute/serverless-icymi-q4-2024/)
• [Introducing cross-account targets for Amazon EventBridge Event Buses](https://aws.amazon.com/blogs/compute/introducing-cross-account-targets-for-amazon-eventbridge-event-buses/)

### Tháng 2

• [Introducing JSONL support with Step Functions Distributed Map](https://aws.amazon.com/blogs/compute/introducing-jsonl-support-with-step-functions-distributed-map/)

### Tháng 3

• [Introducing an enhanced local IDE experience for AWS Step Functions](https://aws.amazon.com/blogs/compute/introducing-an-enhanced-local-ide-experience-for-aws-step-functions/)
• [Optimizing network footprint in serverless applications](https://aws.amazon.com/blogs/compute/optimizing-network-footprint-in-serverless-applications/)
• [Simplifying private API integrations with Amazon EventBridge and AWS Step Functions](https://aws.amazon.com/blogs/compute/simplifying-private-api-integrations-with-amazon-eventbridge-and-aws-step-functions-2/)

## Serverless Office Hours weekly livestream

### February

• 18/2 – [What's new in Serverless for 2025](https://www.youtube.com/watch?v=_aiKWxv9p3o)
• 25/2 – [AWS Step Functions: What's new](https://www.youtube.com/watch?v=MKGjsFHtX30)

### March

• 4/3 – [Scaling Apache Kafka processing](https://www.youtube.com/watch?v=iDffuu733v0&sttick=0)
• 11/3 – [Local AWS step functions dev](https://www.youtube.com/watch?v=B4J6Gsnkqmg)
• 18/3 – [New private API integrations](https://www.youtube.com/watch?v=LqhnA_MrOm4)
• 25/3 – [Data processing with AWS Step Functions](https://www.youtube.com/live/fVo4rJJuZlM?feature=shared)

## Vẫn đang tìm kiếm thêm?

[Trang chủ Serverless](http://aws.amazon.com/serverless) có thêm thông tin. [Trang tài nguyên Lambda](https://aws.amazon.com/lambda/resources/?aws-lambda-resources-blog.sort-by=item.additionalFields.createdDate&aws-lambda-resources-blog.sort-order=desc) chứa các case study, webinar, whitepaper, câu chuyện khách hàng, kiến trúc tham khảo và thậm chí nhiều tutorial Getting Started hơn.

Bạn cũng có thể theo dõi các thành viên của đội ngũ Developer Advocacy làm việc về Serverless để xem tin tức mới nhất, theo dõi cuộc trò chuyện và tương tác với đội ngũ.

• Eric Johnson: [@edjgeek](https://twitter.com/edjgeek)
• Julian Wood: [@julian_wood](https://twitter.com/julian_wood)
• Marcia Villalba: [@mavi888uy](https://twitter.com/mavi888uy/)
• Gunnar Grosch: [@GunnarGrosch](https://x.com/GunnarGrosch)
• Cobus Bernard: [@cobusbernard](https://x.com/cobusbernard/)
• Darko Mesaros: [@darko.rup12.net](https://bsky.app/profile/darko.rup12.net)
• James Ward: [@jamesward](https://x.com/jamesward)
• Marcelo Palladino: [https://www.linkedin.com/in/mfpalladino](https://www.linkedin.com/in/mfpalladino)
• Salih Gueler: [@salihgueler](https://x.com/salihgueler)
• Romain Jourdan: [@rjourdan_net](https://x.com/rjourdan_net)
• Sebastien Stormacq: [@sebsto](https://x.com/sebsto)
• Vinicius Senger: [@siliconvini](https://x.com/siliconvini)

Và cuối cùng, hãy truy cập [Serverless Land](http://serverlessland.com/) cho tất cả nhu cầu serverless của bạn.

## 📖 Glossary - Thuật ngữ

| English | Tiếng Việt | Định nghĩa |
|---------|------------|------------|
| Serverless | Serverless | Mô hình điện toán không cần quản lý server, thanh toán theo sử dụng |
| Step Functions | Step Functions | Dịch vụ điều phối workflow của AWS |
| Workflow Studio | Workflow Studio | Công cụ thiết kế workflow trực quan |
| State Machine | State Machine | Máy trạng thái để điều phối các bước xử lý |
| Distributed Map | Distributed Map | Chức năng xử lý song song phân tán |
| JSONL | JSON Lines | Định dạng dữ liệu với mỗi dòng là một JSON object |
| Private Integration | Tích hợp riêng | Kết nối qua mạng riêng không qua internet |
| Multi-agent Collaboration | Cộng tác đa agent | Nhiều AI agent làm việc cùng nhau |
| RAG Evaluation | Đánh giá RAG | Đánh giá pipeline Retrieval Augmented Generation |
| GraphRAG | GraphRAG | RAG sử dụng cấu trúc đồ thị |
| LLM-as-a-judge | LLM làm giám khảo | Sử dụng LLM để đánh giá output |
| Foundation Model | Model nền tảng | Model AI được huấn luyện trên dữ liệu lớn |
| Fine-tuning | Tinh chỉnh | Điều chỉnh model cho nhiệm vụ cụ thể |
| Live Tail | Live Tail | Xem log theo thời gian thực |
| Application Performance Monitoring | Giám sát hiệu suất ứng dụng | APM theo dõi hiệu suất chi tiết |
| Event-driven Architecture | Kiến trúc hướng sự kiện | Kiến trúc dựa trên sự kiện |
| WebSocket API | WebSocket API | API cho kết nối thời gian thực |
| High Throughput Mode | Chế độ thông lượng cao | Chế độ xử lý với tốc độ cao |
| Cross-account | Liên tài khoản | Kết nối giữa các tài khoản AWS |
| TypeScript Data Client | TypeScript Data Client | Client dữ liệu có type safety |
| CLI Agent | CLI Agent | Agent dòng lệnh |
| Infrastructure as Code | Hạ tầng dưới dạng code | IaC quản lý hạ tầng bằng code |

## 🔗 Tài liệu tham khảo

### Tài liệu gốc
- [Serverless ICYMI 2025 Q1](https://aws.amazon.com/blogs/compute/serverless-icymi-2025-q1/): Bài viết gốc
- [Julian Wood Profile](https://aws.amazon.com/developer/community/heroes/julian-wood/): Thông tin tác giả
- [AWS Compute Blog](https://aws.amazon.com/blogs/compute/): Blog chính thức AWS Compute

### Tài liệu tiếng Việt
- [AWS Documentation VN](https://docs.aws.amazon.com/vi_vn/): Tài liệu AWS tiếng Việt
- [AWS Lambda Resources](https://aws.amazon.com/lambda/resources/): Tài nguyên học tập Lambda
- [Serverless Land](https://serverlessland.com/): Cộng đồng Serverless

### Tools và Services
- [AWS Step Functions](https://aws.amazon.com/step-functions/): Dịch vụ điều phối workflow
- [AWS Lambda](https://aws.amazon.com/lambda/): Dịch vụ compute serverless
- [Amazon Bedrock](https://aws.amazon.com/bedrock/): Nền tảng AI/ML managed
- [Amazon Q Developer](https://aws.amazon.com/q/developer/): AI coding assistant
- [AWS Toolkit for VS Code](https://aws.amazon.com/visualstudiocode/): Extension AWS cho VS Code
- [AWS AppSync](https://aws.amazon.com/appsync/): Managed GraphQL service
- [Amazon EventBridge](https://aws.amazon.com/eventbridge/): Event bus service
- [Amazon SNS](https://aws.amazon.com/sns/): Notification service
- [AWS Amplify](https://aws.amazon.com/amplify/): Full-stack development platform
- [Amazon CloudWatch](https://aws.amazon.com/cloudwatch/): Monitoring và observability

---

## 💬 Ghi chú của người dịch

### Challenges trong quá trình dịch
- **Technical Terms**: Một số thuật ngữ mới như "multi-agent collaboration", "GraphRAG" chưa có bản dịch chuẩn, đã giữ nguyên tiếng Anh và giải thích định nghĩa
- **Cultural Context**: Adapt nội dung cho developers Việt Nam, đặc biệt về ecosystem và công cụ phát triển
- **Complex Concepts**: Giải thích các khái niệm AI/ML phức tạp một cách dễ hiểu cho độc giả Việt

### Insights gained
- **Technical Learning**: Hiểu sâu hơn về xu hướng serverless và AI/ML integration trong AWS
- **Language Skills**: Phát triển kỹ năng dịch thuật technical content, đặc biệt về AI/ML terminology
- **Industry Knowledge**: Nắm bắt được roadmap phát triển của AWS serverless ecosystem

---

## 🤝 Đóng góp và Feedback

Bài dịch này được thực hiện trong khuôn khổ **FCJ Internship Program**.

**📧 Liên hệ**: tritrantnt@gmail.com  
**💬 Feedback**: Mọi góp ý để cải thiện chất lượng dịch thuật xin gửi về email trên  
**🔄 Updates**: Bài dịch sẽ được cập nhật dựa trên feedback từ cộng đồng

---

*© 2025 - Bản dịch thuộc về Trần Ngọc Tri. Vui lòng credit khi sử dụng.*
