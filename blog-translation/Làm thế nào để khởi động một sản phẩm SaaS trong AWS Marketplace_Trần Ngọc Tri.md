# Làm thế nào để khởi động một sản phẩm SaaS trong AWS Marketplace

> **📖 Bài viết gốc**: [How to launch a SaaS product in AWS Marketplace](https://aws.amazon.com/blogs/awsmarketplace/how-to-launch-a-saas-product-in-aws-marketplace/)
> **👤 Tác giả**: Pawan Kumar - Technical Account Manager at Amazon Web Services
> **📅 Ngày xuất bản**: 04 April 2025
> **🌐 Nguồn**: AWS Marketplace Blog
> **👨‍💻 Người dịch**: Trần Ngọc Tri - FCJ Intern
> **📅 Ngày dịch**: 01 July 2025
> **⏱️ Thời gian đọc**: 20 phút

---

## 📋 Tóm tắt

Bài viết này cung cấp hướng dẫn chi tiết self-serve để xuất bản sản phẩm SaaS trong AWS Marketplace. Tác giả trình bày quy trình 4 bước từ việc publish sản phẩm ở trạng thái limited, thực hiện các bước integration cần thiết, testing và cuối cùng là publish sản phẩm ra public. Đây là hướng dẫn foundational dành cho sellers, independent software vendors (ISVs) và consulting partners (CPs) muốn đưa sản phẩm SaaS của họ lên AWS Marketplace để tiếp cận với hàng triệu khách hàng AWS trên toàn thế giới.

**🎯 Đối tượng đọc**: Software Vendors, SaaS Providers, AWS Partners, Product Managers
**📊 Độ khó**: Foundational (100)
**🏷️ Tags**: AWS Marketplace, SaaS, Software Publishing, Self-Service, ISV

---

## 📚 Mục lục

- [Giới thiệu](#giới-thiệu)
- [Yêu cầu tiên quyết](#yêu-cầu-tiên-quyết)
- [Tổng quan giải pháp](#tổng-quan-giải-pháp)
- [Giải pháp](#giải-pháp)
  - [Bước 1: Publish sản phẩm SaaS ở trạng thái limited](#bước-1-publish-sản-phẩm-saas-ở-trạng-thái-limited)
  - [Bước 2: Các bước integration cần thiết](#bước-2-các-bước-integration-cần-thiết)
  - [Bước 3: Testing và contract cancellation](#bước-3-testing-và-contract-cancellation)
  - [Bước 4: Publish sản phẩm ra public](#bước-4-publish-sản-phẩm-ra-public)
- [Kết luận và bước tiếp theo](#kết-luận-và-bước-tiếp-theo)
- [Glossary - Thuật ngữ](#glossary---thuật-ngữ)
- [Tài liệu tham khảo](#tài-liệu-tham-khảo)

---

## Giới thiệu

AWS Marketplace sellers, independent software vendors (ISVs) và consulting partners (CPs) có thể khởi động các sản phẩm software-as-a-service (SaaS) trong AWS Marketplace.

Bài viết này cung cấp các bước self-serve chi tiết để publish một sản phẩm SaaS ở trạng thái limited, bao gồm các bước integration cần thiết để publish sản phẩm ra trạng thái public, và bao gồm hướng dẫn để publish sản phẩm ra trạng thái public.

## Yêu cầu tiên quyết

Để khởi động một sản phẩm SaaS trong AWS Marketplace, bạn phải hoàn thành các yêu cầu tiên quyết sau:

- **Bạn phải có access vào AWS Marketplace console.**
- **Bạn phải được đăng ký đầy đủ với AWS Marketplace như một seller.** Để biết chi tiết về cách đăng ký thành công, tham khảo [Checklist to successfully registering as a seller in AWS Marketplace](https://aws.amazon.com/blogs/awsmarketplace/checklist-successfully-registering-seller-aws-marketplace/).

## Tổng quan giải pháp

Giải pháp được cấu tạo từ các bước high-level sau:

1. **Publish một sản phẩm SaaS ở trạng thái limited sử dụng self-service**
2. **Các bước integration cần thiết để publish sản phẩm ra trạng thái public**
3. **Testing và contract cancellation**
4. **Các bước để publish sản phẩm ra trạng thái public**

## Giải pháp

Để khởi động một sản phẩm SaaS trong AWS Marketplace, hãy tuân theo các bước trong các phần tiếp theo.

### Bước 1: Publish sản phẩm SaaS ở trạng thái limited sử dụng self-service

1. **Đăng nhập vào AWS account của bạn.**
2. **Truy cập AWS Marketplace Management Portal.**
3. **Navigate đến tab Products và chọn SaaS từ dropdown menu,** như được hiển thị trong screenshot sau.

   ![Screenshot of AWS Marketplace Portal Product Types Menu](https://d2908q01vomqb2.cloudfront.net/761f22b2c1593d0bb87e0b606f990ba4974706de/2025/02/07/Picture1-3-1024x398.png)

   *Hình 1 – AWS Marketplace Portal Product Types Menu*
4. **Verify rằng application của bạn chạy trên AWS.** [Bắt đầu từ ngày 1 tháng 5 năm 2025, việc chạy application trên AWS trở thành tùy chọn](https://aws.amazon.com/blogs/awsmarketplace/aws-marketplace-announces-upcoming-expansion-to-saas-product-catalog/). Bạn cũng có thể thêm các tag tùy chọn để hỗ trợ tag-based authorization.

   **Cung cấp product title.** Chọn **Generate product ID and product code,** như được hiển thị trong screenshot sau. Điều này sẽ tạo ra một product ID và product code duy nhất trên các sản phẩm AWS Marketplace.

   ![Screenshot related to generating product ID and product code for SaaS product types](https://d2908q01vomqb2.cloudfront.net/761f22b2c1593d0bb87e0b606f990ba4974706de/2025/02/07/Picture2-1-963x1024.png)

   *Hình 2 – Generate product ID and product code for SaaS product types*
5. **Cung cấp một Amazon Simple Storage Service (Amazon S3) URL có thể truy cập công khai cho product logo.** Cung cấp mô tả ngắn và dài cho sản phẩm của bạn.
6. **Tùy thuộc vào use case của sản phẩm, chọn tối đa ba (3) categories và tối đa mười lăm (15) keywords cụ thể cho sản phẩm của bạn,** như được hiển thị trong screenshot sau.

   ![Screenshot of AWS Marketplace Product offering categories and keywords selection for SaaS product types](https://d2908q01vomqb2.cloudfront.net/761f22b2c1593d0bb87e0b606f990ba4974706de/2025/02/07/Picture3-999x1024.png)

   *Hình 3 – Categories and keywords selection for SaaS product types*
7. **Cung cấp fulfillment URL.** Fulfillment URL là nơi AWS Marketplace website redirect subscribers. Điều này được thiết kế cho software access hoặc subscriber registration.
8. **Chọn một pricing model và cung cấp các chi tiết cụ thể cho nó.** Để biết chi tiết về việc chọn pricing model tốt nhất cho product use case, tham khảo [SaaS product pricing in AWS Marketplace](https://docs.aws.amazon.com/marketplace/latest/userguide/saas-pricing-models.html). Screenshot sau hiển thị màn hình Configure product pricing.

   ![Screenshot of Pricing models for SaaS product types](https://d2908q01vomqb2.cloudfront.net/761f22b2c1593d0bb87e0b606f990ba4974706de/2025/02/07/Picture4-1-1024x479.png)

   *Hình 4 – Pricing models for SaaS product types*
9. **Bạn có thể set một refund policy cho sản phẩm cụ thể của mình.**
10. **Bạn có thể set một end user license agreement (EULA).** Đây là nơi bạn set một agreement giữa sellers và buyers. Có hai tùy chọn: Standard Contract for AWS Marketplace (SCMP) và custom EULA (là một custom agreement giữa sellers và buyers).
11. **Offer availability xác định nơi bạn muốn publish sản phẩm của mình.** Bạn có thể chọn từ ba tùy chọn cho offer availability, như được hiển thị trong screenshot sau:

    - a. All countries
    - b. All countries with exclusions
    - c. Allowlisted countries only

    ![Screenshot of Offer availability for SaaS product types](https://d2908q01vomqb2.cloudfront.net/761f22b2c1593d0bb87e0b606f990ba4974706de/2025/02/07/Picture5-1-1024x667.png)

    *Hình 5 – Configure offer availability for SaaS product types*
12. **Bạn có thể thêm nhiều AWS account ID sẽ có access vào sản phẩm published của bạn.** Sau đó chọn **Submit,** như được hiển thị trong screenshot sau.

    ![Screenshot related to configuring allowlist accounts for SaaS product types](https://d2908q01vomqb2.cloudfront.net/761f22b2c1593d0bb87e0b606f990ba4974706de/2025/02/07/Picture6-1-1024x610.png)

    *Hình 6 – Configure allowlist – Optional for SaaS product types*
13. **Bạn có thể track các product publishing request trong phần Request log** như được hiển thị trong screenshot sau.

    ![Screenshot of tracking the product publishing request for SaaS product types](https://d2908q01vomqb2.cloudfront.net/761f22b2c1593d0bb87e0b606f990ba4974706de/2025/02/07/Picture7-1-1024x206.png)

    *Hình 7 – Tracking the product publishing request for SaaS product types*
14. **Sản phẩm này sẽ được published vào AWS Marketplace với trạng thái limited,** bao gồm các chi tiết như Product ARN, Metering Service SNS topic ARN, và Entitlement Service SNS topic ARN.

### Bước 2: Các bước integration cần thiết để publish sản phẩm ra trạng thái public

Các bước integration khác nhau dựa trên pricing type của sản phẩm. Để biết chi tiết về các bước integration cho mỗi pricing type, tham khảo như sau:

- **[Subscription integration](https://docs.aws.amazon.com/marketplace/latest/userguide/saas-integrate-subscription.html)**
- **[Contract integration](https://docs.aws.amazon.com/marketplace/latest/userguide/saas-integrate-contract.html)**
- **[Contract with pay-as-you-go integration](https://docs.aws.amazon.com/marketplace/latest/userguide/saas-integrate-contract-with-pay.html)**

Sản phẩm của bạn phải tuân thủ [Product usage guidelines](https://docs.aws.amazon.com/marketplace/latest/userguide/saas-guidelines.html#saas-product-usage).

### Bước 3: Testing và contract cancellation

**Subscribe vào SaaS product listing của bạn và đánh giá customer experience.** Để biết chi tiết về cách test, tham khảo [Successfully testing your SaaS listing in AWS Marketplace](https://aws.amazon.com/blogs/awsmarketplace/successfully-testing-your-saas-listing-in-aws-marketplace/).

**Cancel test subscription của bạn trước khi tiến hành bước tiếp theo.** Nếu bạn cần hỗ trợ, liên hệ [Marketplace Seller Operations team](https://aws.amazon.com/marketplace/management/contact-us/) bằng cách submit một support ticket.

### Bước 4: Publish sản phẩm ra trạng thái public

Khi bạn đã sẵn sàng, tuân theo các bước 1–3 của Bước 1 và chọn sản phẩm cụ thể của bạn. Để publish sản phẩm ra trạng thái public, tuân theo các bước sau:

1. **Trên dropdown menu Request changes, chọn Update product visibility,** như được hiển thị trong screenshot sau.

   ![AWS Marketplace seller interface with dropdown menu for product updates, pricing, fulfillment, and public offer settings](https://d2908q01vomqb2.cloudfront.net/761f22b2c1593d0bb87e0b606f990ba4974706de/2025/04/02/Figure8.png)

   *Hình 8 – Update product visibility for SaaS product types*
2. **Trên màn hình Update product visibility, chọn một tùy chọn để specify ai có thể view sản phẩm của bạn trong AWS Marketplace.** Bạn có thể chọn Public, Limited, hoặc Restricted.

   ![Screenshot of update product visibility for SaaS product types](https://d2908q01vomqb2.cloudfront.net/761f22b2c1593d0bb87e0b606f990ba4974706de/2025/02/07/Picture9-3.png)

   *Hình 9 – Update product visibility for SaaS product types*

## Kết luận và bước tiếp theo

Sau khi đọc bài viết này, bạn sẽ có hiểu biết rõ ràng về cách khởi động một sản phẩm SaaS trong AWS Marketplace một cách độc lập. Ngay cả khi họ chưa sẵn sàng với chi tiết sản phẩm SaaS thực tế của họ, sellers, ISVs và CPs sẽ có thể khởi động một sample SaaS product ở trạng thái limited sử dụng thông tin được cung cấp.

Việc publish sản phẩm SaaS trên AWS Marketplace mở ra cơ hội tiếp cận hàng triệu khách hàng AWS trên toàn thế giới và tận dụng hệ sinh thái đối tác rộng lớn của AWS. Điều quan trọng là đảm bảo sản phẩm của bạn tuân thủ các guidelines và best practices để có trải nghiệm khách hàng tốt nhất.

---

## 📖 Glossary - Thuật ngữ

| English                                      | Tiếng Việt                                 | Định nghĩa                                                                      |
| -------------------------------------------- | -------------------------------------------- | ---------------------------------------------------------------------------------- |
| Software-as-a-Service (SaaS)                 | Phần mềm dưới dạng dịch vụ            | Mô hình cung cấp phần mềm qua internet như một dịch vụ                    |
| Independent Software Vendor (ISV)            | Nhà cung cấp phần mềm độc lập         | Công ty phát triển và bán phần mềm độc lập                               |
| Consulting Partner (CP)                      | Đối tác tư vấn                          | Đối tác cung cấp dịch vụ tư vấn và implementation                         |
| AWS Marketplace                              | AWS Marketplace                              | Nền tảng mua bán phần mềm và dịch vụ của AWS                              |
| Self-Service                                 | Tự phục vụ                                | Khả năng thực hiện các tác vụ mà không cần hỗ trợ trực tiếp          |
| Limited State                                | Trạng thái giới hạn                      | Trạng thái sản phẩm chỉ có thể truy cập bởi một số account nhất định |
| Public State                                 | Trạng thái công khai                      | Trạng thái sản phẩm có thể được truy cập bởi tất cả khách hàng      |
| Product ID                                   | ID sản phẩm                                | Identifier duy nhất cho sản phẩm trong AWS Marketplace                          |
| Product Code                                 | Mã sản phẩm                               | Mã duy nhất được sử dụng cho billing và metering                           |
| Fulfillment URL                              | URL hoàn thành                             | URL mà khách hàng được redirect đến sau khi subscribe                      |
| Pricing Model                                | Mô hình định giá                        | Cách thức tính phí cho sản phẩm (subscription, contract, pay-as-you-go)      |
| End User License Agreement (EULA)            | Thỏa thuận giấy phép người dùng cuối | Hợp đồng pháp lý giữa vendor và end user                                    |
| Standard Contract for AWS Marketplace (SCMP) | Hợp đồng chuẩn cho AWS Marketplace       | Template hợp đồng được AWS cung cấp sẵn                                    |
| Offer Availability                           | Khả năng cung cấp                         | Xác định các quốc gia/region nào có thể truy cập sản phẩm               |
| Allowlist                                    | Danh sách cho phép                         | Danh sách các account được phép truy cập sản phẩm                         |
| Integration                                  | Tích hợp                                   | Quá trình kết nối sản phẩm với AWS Marketplace APIs                         |
| Subscription Integration                     | Tích hợp subscription                      | Integration cho mô hình pricing định kỳ                                       |
| Contract Integration                         | Tích hợp contract                          | Integration cho mô hình pricing theo hợp đồng                                 |
| Metering Service                             | Dịch vụ đo lường                        | Service theo dõi usage cho billing                                                |
| Entitlement Service                          | Dịch vụ quyền hạn                        | Service quản lý quyền truy cập của customer                                   |

## 🔗 Tài liệu tham khảo

### Tài liệu gốc

- [How to launch a SaaS product in AWS Marketplace](https://aws.amazon.com/blogs/awsmarketplace/how-to-launch-a-saas-product-in-aws-marketplace/): Bài viết gốc
- [AWS Marketplace Management Portal](https://aws.amazon.com/marketplace/management/homepage): Portal quản lý seller
- [Checklist to successfully registering as a seller in AWS Marketplace](https://aws.amazon.com/blogs/awsmarketplace/checklist-successfully-registering-seller-aws-marketplace/): Hướng dẫn đăng ký seller

### Tài liệu tiếng Việt

- [AWS Marketplace Seller Guide](https://docs.aws.amazon.com/marketplace/latest/userguide/): Hướng dẫn cho seller
- [SaaS Product Pricing Models](https://docs.aws.amazon.com/marketplace/latest/userguide/saas-pricing-models.html): Các mô hình định giá SaaS
- [AWS Marketplace Documentation](https://docs.aws.amazon.com/marketplace/index.html): Tài liệu tổng hợp

### Integration Resources

- [Subscription Integration](https://docs.aws.amazon.com/marketplace/latest/userguide/saas-integrate-subscription.html): Hướng dẫn tích hợp subscription
- [Contract Integration](https://docs.aws.amazon.com/marketplace/latest/userguide/saas-integrate-contract.html): Hướng dẫn tích hợp contract
- [Contract with Pay-as-you-go Integration](https://docs.aws.amazon.com/marketplace/latest/userguide/saas-integrate-contract-with-pay.html): Hướng dẫn tích hợp hybrid pricing
- [Product Usage Guidelines](https://docs.aws.amazon.com/marketplace/latest/userguide/saas-guidelines.html#saas-product-usage): Guidelines sử dụng sản phẩm

### Testing và Support

- [Successfully testing your SaaS listing in AWS Marketplace](https://aws.amazon.com/blogs/awsmarketplace/successfully-testing-your-saas-listing-in-aws-marketplace/): Hướng dẫn testing
- [Marketplace Seller Operations team](https://aws.amazon.com/marketplace/management/contact-us/): Support team cho seller
- [AWS Marketplace Resources Hub](https://aws.amazon.com/mp/resources-hub/): Tài nguyên học tập

### Business Resources

- [AWS Marketplace Solutions](https://aws.amazon.com/marketplace/solutions): Use cases và success stories
- [Customer Success Stories](https://aws.amazon.com/marketplace/solutions/awsmp-all-case-studies): Câu chuyện thành công
- [Sell in AWS Marketplace](https://aws.amazon.com/marketplace/management/tour/): Hướng dẫn bán hàng

---

## 💬 Ghi chú của người dịch

### Challenges trong quá trình dịch

- **Business Terminology**: Nhiều thuật ngữ business và legal cần được dịch chính xác để tránh hiểu nhầm về quy trình
- **Technical Process**: Các bước kỹ thuật cần được trình bày rõ ràng để người đọc có thể thực hiện được
- **UI Elements**: Tên các buttons, menu và interface elements được giữ nguyên tiếng Anh cho dễ đối chiếu

### Insights gained

- **AWS Marketplace Ecosystem**: Hiểu sâu hơn về hệ sinh thái AWS Marketplace và cơ hội kinh doanh cho ISV
- **SaaS Business Model**: Nắm được các mô hình pricing và strategies cho SaaS products
- **Self-Service Platform**: Học được về power của self-service platforms trong việc democratize market access

### Lưu ý về implementation

Khi thực hiện publish SaaS product trên AWS Marketplace, cần chú ý:

- **Legal Compliance**: Đảm bảo tuân thủ các quy định pháp lý và EULA requirements
- **Integration Testing**: Test kỹ lưỡng các integration với AWS Marketplace APIs
- **Customer Experience**: Focus vào customer experience từ discovery đến onboarding
- **Pricing Strategy**: Cân nhắc kỹ pricing model phù hợp với target market
- **Support Readiness**: Chuẩn bị support resources cho customers sau khi go-live

### Business Impact

AWS Marketplace cung cấp:

- **Global Reach**: Tiếp cận millions of AWS customers worldwide
- **Trusted Platform**: Leverage AWS brand trust và security
- **Simplified Procurement**: Streamlined purchasing process cho enterprise customers
- **Co-sell Opportunities**: Partnership opportunities với AWS sales teams

---

## 🤝 Đóng góp và Feedback

s
Bài dịch này được thực hiện trong khuôn khổ **FCJ Internship Program**.

**📧 Liên hệ**: tritrantnt@gmail.com
**💬 Feedback**: Mọi góp ý để cải thiện chất lượng dịch thuật xin gửi về email trên
**🔄 Updates**: Bài dịch sẽ được cập nhật dựa trên feedback từ cộng đồng

---

*© 2025 - Bản dịch thuộc về Trần Ngọc Tri. Vui lòng credit khi sử dụng.*
