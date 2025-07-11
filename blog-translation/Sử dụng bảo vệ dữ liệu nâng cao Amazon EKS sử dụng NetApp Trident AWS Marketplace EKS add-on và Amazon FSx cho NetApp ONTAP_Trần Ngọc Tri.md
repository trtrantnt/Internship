# Triển khai bảo vệ dữ liệu nâng cao Amazon EKS sử dụng NetApp Trident AWS Marketplace EKS add-on và Amazon FSx cho NetApp ONTAP

> **📖 Bài viết gốc**: [Deploy Amazon EKS advanced data protection using NetApp Trident AWS Marketplace EKS add-on and Amazon FSx for NetApp ONTAP](https://aws.amazon.com/blogs/awsmarketplace/deploy-amazon-eks-advanced-data-protection-using-netapp-trident-aws-marketplace-eks-add-on-and-amazon-fsx-for-netapp-ontap/)  
> **👤 Tác giả**: Eric Yuen, Michael Shaul, và Bhavin Shah  
> **📅 Ngày xuất bản**: 04/04/2025  
> **🌐 Nguồn**: AWS Marketplace Blog  
> **👨‍💻 Người dịch**: Trần Ngọc Tri - FCJ Intern  
> **📅 Ngày dịch**: 11/07/2025  
> **⏱️ Thời gian đọc**: 30 phút

---

## 📋 Tóm tắt

Bài viết này hướng dẫn chi tiết cách triển khai giải pháp bảo vệ dữ liệu nâng cao cho Amazon EKS sử dụng NetApp Trident thông qua AWS Marketplace EKS add-on kết hợp với Amazon FSx cho NetApp ONTAP. Giải pháp cung cấp khả năng sao chép dữ liệu giữa các EKS cluster để phục hồi thảm họa (DR), tạo snapshot tức thì, và khôi phục dữ liệu tại chỗ. Nội dung bao gồm từ thiết lập môi trường, cấu hình Trident, tạo Trident Mirror Relationship (TMR), đến test failover và failback giữa primary và DR cluster.

**🎯 Đối tượng đọc**: DevOps Engineers, Solutions Architects, Platform Engineers  
**📊 Độ khó**: Intermediate (200)  
**🏷️ Tags**: Amazon EKS, NetApp Trident, FSx ONTAP, Disaster Recovery, Data Protection

---

## 📚 Mục lục

- [Giới thiệu](#giới-thiệu)
- [Prerequisites](#prerequisites)
- [Tổng quan giải pháp](#tổng-quan-giải-pháp)
- [Walkthrough triển khai](#walkthrough-triển-khai)
  - [Cài đặt Trident EKS add-on](#cài-đặt-trident-eks-add-on-trên-cả-primary-và-dr-eks-cluster)
  - [Cấu hình Trident](#cấu-hình-trident-trên-cả-primary-và-dr-eks-cluster)
  - [Tạo source PVC và TMR](#tạo-source-pvc-và-tmr-replication-relationship-trên-primary-eks-cluster)
  - [Tạo TMR và destination PVC](#tạo-tmr-và-destination-pvc-tương-ứng-trên-dr-eks-cluster)
- [Test failover thủ công](#test-failover-thủ-công-từ-dr-eks-cluster)
- [Cleanup](#cleanup)
- [Kết luận](#kết-luận)
- [Glossary - Thuật ngữ](#glossary---thuật-ngữ)
- [Tài liệu tham khảo](#tài-liệu-tham-khảo)

---

## Giới thiệu

NetApp Trident cho Amazon FSx cho NetApp ONTAP có sẵn dưới dạng [AWS Marketplace](https://aws.amazon.com/marketplace/pp/prodview-mptzckr6mggia) add-on cho Amazon EKS. Giải pháp này đơn giản hóa quá trình đăng ký và triển khai bằng cách tự động hóa việc cài đặt và cấu hình ban đầu của NetApp Trident trên các Amazon EKS cluster.

Khi sử dụng [NetApp Trident](https://docs.aws.amazon.com/eks/latest/userguide/fsx-ontap.html) cùng với [FSx cho ONTAP](https://aws.amazon.com/fsx/netapp-ontap/), nó không chỉ cung cấp storage [Container Storage Interface (CSI)](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html) cơ bản, mà còn các tính năng quản lý dữ liệu nâng cao như mirroring PersistentVolumeClaim (PVC) giữa các Amazon EKS cluster để [phục hồi thảm họa (DR)](https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html), tạo snapshot tức thì để bảo vệ dữ liệu, và khôi phục snapshot tại chỗ.

Vì các tính năng này có sẵn dưới dạng Kubernetes custom resource, bạn có thể quản lý chúng thông qua các công cụ Amazon EKS và tích hợp chúng vào quy trình GitOps như một phần của quản lý vòng đời ứng dụng để đơn giản hóa triển khai.

Bài viết này chỉ cho bạn cách triển khai Trident sử dụng AWS Marketplace add-on, thiết lập sao chép dữ liệu giữa hai Amazon EKS cluster với hai FSx cho ONTAP file system, và test failover.

## Prerequisites

Các điều kiện tiên quyết sau đây cần thiết để thiết lập môi trường demo này:

1. [Tạo hai EKS cluster](https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html) trong cùng hoặc khác AWS Region làm primary và DR cluster.
2. Kích hoạt cả hai Amazon EKS cluster cho AWS Identity and Access Management (IAM) [OpenID Connect (OIDC) provider](https://docs.aws.amazon.com/eks/latest/userguide/enable-iam-roles-for-service-accounts.html) để cấu hình IAM role cho service account.
3. [Tạo hai FSx cho ONTAP file system](https://docs.aws.amazon.com/fsx/latest/ONTAPGuide/getting-started.html) làm primary và DR.
4. [Tạo NetApp SnapMirror peering](https://docs.aws.amazon.com/fsx/latest/ONTAPGuide/migrating-fsx-ontap-snapmirror.html#cluster-peering) giữa primary và DR FSx cho NetApp file system và storage virtual machine (SVM).
5. [Cài đặt eksctl](https://eksctl.io/installation/) và cấu hình local host để truy cập cả hai EKS cluster.
6. Thiết lập biến môi trường (như hình 1) với tên Primary và DR EKS cluster:

![Shell script demonstrating AWS EKS high availability multi-cluster setup](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure1.png)
*Hình 1: Shell script demo thiết lập multi-cluster EKS high availability*

7. Các [quyền AWS Marketplace](https://docs.aws.amazon.com/marketplace/latest/buyerguide/buyer-security-iam-awsmanpol.html#security-iam-awsmanpol-awsmarketplacemanagesubscriptions) sau đây được yêu cầu:

```json
"aws-marketplace:ViewSubscriptions",
"aws-marketplace:Subscribe", 
"aws-marketplace:Unsubscribe"
```

8. [Sử dụng lệnh sau](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-secret-sh) (như hình 2) để tạo các secret khác nhau sử dụng [AWS Secrets Manager](https://aws.amazon.com/secrets-manager/) cho mỗi FSx cho ONTAP SVM:

![AWS secret creation script defining Trident storage credentials](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure2.png)
*Hình 2: AWS CLI command tạo secret trong AWS Secrets Manager*

## Tổng quan giải pháp

Giải pháp bao gồm bốn bước cấp cao (như hình 3):

![AWS high availability architecture depicting EKS clusters, Trident CSI, and FSx storage replication](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure3.png)
*Hình 3: Amazon EKS PVC replication sử dụng NetApp Trident và Amazon FSx cho NetApp ONTAP*

1. Cài đặt Trident EKS add-on trên cả primary và DR Amazon EKS cluster
2. Cấu hình Trident trên cả hai EKS cluster
3. Tạo source PVC và [Trident Mirror Relationship (TMR)](https://docs.netapp.com/us-en/astra-control-center/use-acp/acp-volume-replicate.html) replication relationship trên primary EKS cluster
4. Tạo TMR và destination PVC tương ứng trên DR EKS cluster

## Walkthrough triển khai

### Cài đặt Trident EKS add-on trên cả primary và DR Amazon EKS cluster

#### 1. Tạo IAM policy

Sử dụng template sau (như hình 4) để [tạo file policy.json](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-policy-json), thiết lập các tham số cho IAM policy cần thiết:

![AWS IAM policy document specifying FSx management actions and Secrets Manager retrieval permissions](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure4.png)
*Hình 4: policy.json chứa quyền AWS IAM cho FSx operations và Secrets Manager access*

[Chạy lệnh sau](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-policy-sh) (như hình 5) để tạo policy:

![AWS IAM policy creation command defining NetApp Trident access](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure5.png)
*Hình 5: AWS CLI command tạo AWS IAM policy cho Trident CSI driver integration*

#### 2. Tạo IAM role

[Chạy lệnh sau](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-role-sh) (như hình 6) để tạo IAM role cho service account với tên role tùy chỉnh. Trong ví dụ này, đó là `AmazonEKS_FSxN_CSI_DriverRole`:

![eksctl create command establishing IAM service account with Amazon FSx permissions](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure6.png)
*Hình 6: Command tạo AWS IAM service account sử dụng eksctl*

#### 3. Cài đặt Trident add-on trên cả hai EKS cluster

[Tạo file add-on.json](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-add-on-json) (như hình 7) để capture các tham số thiết lập add-on. Cập nhật clusterName với tên primary và DR EKS cluster của bạn. Cập nhật serviceAccountRoleArn với [Amazon Resource Name (ARN)](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference-arns.html) của role, được tạo ở bước trước. Trong ví dụ này, ARN là `arn:aws:iam::<account_ID>:role/AmazonEKS_FSXN_CSI_DriverRole`. Cập nhật configurationValues với cùng ARN này.

![add-on.json configuration file linking NetApp Trident operator to EKS FSx driver role](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure7.png)
*Hình 7: add-on.json hiển thị cluster setting cho Trident operator add-on với service account role specification*

[Chạy lệnh sau](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-add-on-sh) (như hình 8) để cài đặt Trident trên cả primary và DR Amazon EKS cluster:

![AWS EKS add-on creation command with JSON input file reference](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure8.png)
*Hình 8: AWS CLI command để tạo EKS add-on sử dụng add-on.json như configuration file*

Xác minh trạng thái triển khai Trident add-on bằng cách kiểm tra phiên bản hiện tại thông qua [eksctl command](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-get-add-on-sh) (như hình 9). Thực hiện bước này trên cả primary và DR EKS cluster, thay thế primary/DR-Cluster bằng tên cluster tương ứng.

![eksctl command retrieving NetApp Trident operator addon for Primary/DR cluster](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure9.png)
*Hình 9: eksctl command get Trident operator add-on cho cluster được chỉ định*

Bạn nên mong đợi output như hình 10:

![Trident operator add-on output](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure10.png)
*Hình 10: Output của Trident operator add-on*

### Cấu hình Trident trên cả primary và DR EKS cluster

#### 1. Cấu hình cả hai FSx cho ONTAP file system

Tạo [backend-config.yaml](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-backend-config-yaml) riêng biệt sử dụng SVM credential (username và password) được lưu trữ trong [AWS Secrets Manager](https://aws.amazon.com/secrets-manager/), như đã đề cập trong prerequisite thứ bảy. Thực hiện điều này cho cả primary và DR EKS cluster và FSx cho ONTAP file system tương ứng.

Trong file YAML ví dụ sau (như hình 11), bạn cần cập nhật cả `fsxFileSystemID`, `<fs-xxxxxxxxxx>` và `<region>` với file system ID và [AWS Region](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/) của bạn.

![TridentBackendConfig YAML defining ONTAP NAS storage with AWS credential](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure11.png)
*Hình 11: backend-config.yaml định nghĩa Trident backend storage configuration sử dụng ONTAP driver*

[Chạy các lệnh sau](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-backend-config-sh) (như hình 12) trên cả hai EKS cluster:

![kubectl commands switching contexts and creating backend config across EKS clusters](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure12.png)
*Hình 12: kubectl command tạo backend configuration sử dụng backend-config.yaml cho cả primary và DR cluster*

#### 2. Cấu hình Kubernetes StorageClass object cho cả primary và DR EKS cluster

[Sử dụng storageclass.yaml ví dụ sau](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-storageclass-yaml) (như hình 13), tạo file YAML và chạy command từ cả primary và DR EKS cluster:

![StorageClass YAML defining Trident CSI storage with volume expansion and reclaim policies](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure13.png)
*Hình 13: storageclass.yaml định nghĩa StorageClass sử dụng NetApp Trident provisioner cho FSx ONTAP*

[Chạy các lệnh sau](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-storageclass-sh) (như hình 14):

![kubectl commands creating StorageClass in primary and DR EKS clusters](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure14.png)
*Hình 14: kubectl command tạo StorageClass configuration sử dụng storageclass.yaml*

### Tạo source PVC và TMR replication relationship trên primary Amazon EKS cluster

#### 1. Tạo PVC

[Trên primary EKS cluster, tạo PVC bằng file YAML ví dụ sau](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-pvc-yaml) (như hình 15). Bạn có thể thay đổi tên PVC và storage size để đáp ứng yêu cầu của mình:

![Complete Kubernetes PersistentVolumeClaim configuration showing storage specs](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure15.png)
*Hình 15: pvc.yaml định nghĩa PVC với configuration cơ bản bao gồm storage size, ReadWriteMany access và basic-csi storage class*

[Chạy các lệnh sau](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-pvc-sh) (như hình 16) để chạy PVC creation:

![kubectl commands to set context, create PVC from YAML, and list PVCs](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure16.png)
*Hình 16: kubectl command tạo PVC từ pvc.yaml, và retrieve PVC information*

Kiểm tra PVC status trên primary EKS cluster, sau đây là output mong đợi (như hình 17):

![PVC information from previous kubectl commands](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure17.png)
*Hình 17: PVC information*

#### 2. Tạo TMR

Sau khi PVC được tạo trên primary EKS cluster, sử dụng ví dụ sau để [tạo mirrorsource.yaml](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-mirrorsource-yaml) (như hình 18) file cho TMR giữa primary và DR PVC.

![NetApp Trident YAML defining mirror relationship with promoted state](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure18.png)
*Hình 18: mirrorsource.yaml định nghĩa TridentMirrorRelationship resource với promoted state*

Sau khi bạn có file mirrorsource.yaml sẵn sàng, [chạy các lệnh sau](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-mirrorsource-sh) (như hình 19):

![kubectl commands showing Trident mirror source deployment with context switching](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure19.png)
*Hình 19: kubectl command cấu hình Trident mirror relationship từ mirrorsource.yaml*

Kiểm tra TMR trên primary Amazon EKS cluster. Sau đây là output mong đợi (như hình 20):

![TMR in promoted state](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure20.png)
*Hình 20: TMR trong promoted state từ primary EKS cluster*

Lấy FSx cho ONTAP local volume handle cho PVC của bạn từ primary Amazon EKS cluster bằng [cách chạy lệnh sau](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-gettmr-sh) (như hình 21).

![kubectl command extracting local volume handle from PVC using custom jsonpath format](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure21.png)
*Hình 21: kubectl command sử dụng jsonpath để extract local volume handle*

Output sẽ trông như ví dụ sau, với FSx cho ONTAP SVM name và volume ID của bạn. Ghi lại các giá trị này vì bạn sẽ sử dụng chúng trong bước tiếp theo.

```
[pvc-storage, <fsxn_svm>:<volume_id>]
```

### Tạo TMR và destination PVC tương ứng trên DR Amazon EKS cluster

#### 1. Tạo TMR trên DR Amazon EKS cluster

Tạo TMR trên DR EKS cluster [sử dụng mirrordest.yaml manifest](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-mirrordest-yaml) (như hình 22). Đảm bảo bạn cập nhật tất cả localPVCName (như pvc-storage, từ bước trước) và remoteVolumeHandle, như ví dụ sau:

```
<fsxn_svm>:<volume_id>
```

![NetApp Trident mirrordest.yaml configuration showing established PVC storage mapping](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure22.png)
*Hình 22: mirrordest.yaml thiết lập storage mirroring relationship*

[Chạy các lệnh sau](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-mirrordest-sh) (như hình 23) để tạo TMR trên DR Amazon EKS cluster:

![kubectl commands create mirror destination](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure23.png)
*Hình 23: kubectl command tạo mirror destination sử dụng mirrordest.yaml*

Bạn sẽ nhận được output mong đợi sau (như hình 24):

![TMR created successfully](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure24.png)
*Hình 24: TMR được tạo thành công*

#### 2. Tạo PVC trên DR Amazon EKS cluster và bắt đầu data replication

Bước cuối cùng là [tạo PVC trên DR EKS cluster](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-pvcdest-yaml) và bắt đầu replication. Đảm bảo bạn cập nhật TridentMirrorRelationship metadata annotation với tên TMR được thiết lập trong ví dụ trước. Trong ví dụ sau (như hình 25), nó được đặt thành pvc-storage.

![pvcdest.yaml defining PVC with ReadWriteMany access, Trident mirror settings](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure25.png)
*Hình 25: pvcdest.yaml định nghĩa storage claim với ReadWriteMany access*

Bây giờ bạn có thể [chạy các lệnh sau](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-pvcdest-sh) (như hình 26) để tạo PVC:

![kubectl commands set context, create PVC, and verify mirror status](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure26.png)
*Hình 26: kubectl command set context, tạo PVC, và verify mirror status*

Tại thời điểm này, TMR sẽ được thiết lập sau khi destination PVC được tạo. Bạn có thể kiểm tra TMR status bằng cách so sánh với output mong đợi này (như hình 27):

![TMR in established state](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure27.png)
*Hình 27: TMR trong established state*

Thiết lập và cấu hình Amazon EKS environment hiện đã hoàn thành. Đợi vài phút để replication bắt kịp và hoàn thành, sau đó bạn sẽ có một cặp Amazon EKS PVC hoàn toàn sẵn sàng cho DR.

Trong giai đoạn tiếp theo, thực hiện thủ công trigger failover để mô phỏng kịch bản DR cho mục đích testing.

## Test failover thủ công từ DR Amazon EKS cluster

Chạy các bước sau để mô phỏng quy trình DR trong Primary EKS cluster của bạn và chuyển cả application và data vào DR cluster.

Sử dụng test environment của bạn, bạn có thể dừng data replication và promote PVC trên DR EKS cluster thành ReadWrite và mountable. [Sử dụng mirrordestdr.yaml manifest ví dụ sau](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-mirrordestdr-yaml) (như hình 28), bạn có thể thay đổi TMR từ "established" thành "promoted", đây là điều bạn cần để biến DR Amazon EKS cluster thành primary.

![mirrordestdr.yaml defining promoted state for Trident mirror relationship in disaster recovery](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure28.png)
*Hình 28: mirrordestdr.yaml khai báo TMR với promoted state*

[Chạy lệnh sau](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-mirrordestdr-sh) (như hình 29) để trigger failover:

![kubectl command triggers a cluster failover](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure29.png)
*Hình 29: kubectl command trigger cluster failover*

**Lưu ý**: Quan trọng là phải validate rằng state của TMR thay đổi thành "promoted". Đôi khi status có thể là "promoting" thay vì vì failover mất thời gian. Điều này bình thường; đợi vài phút và thử lại nếu cần thiết.

Để lấy state của TMR, sử dụng lệnh sau:

```bash
kubectl get tmr
```

Sau đây là output mong đợi (như hình 30):

![TMR in promoted state](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure30.png)
*Hình 30: TMR trong promoted state*

Chúc mừng! Bạn đã thiết lập thành công DR cho Amazon EKS cluster của mình và validate disaster recovery bằng cách chuyển đổi sang DR cluster.

Tại thời điểm này, DR EKS cluster của bạn đang trong production và có thể host production workload của bạn. Một khi primary EKS cluster và FSx cho ONTAP file system của bạn sẵn sàng hoạt động, bạn có thể thực hiện bước replication theo thứ tự ngược lại để fail back về original primary site.

## Cleanup

Để remove test environment, chạy [các lệnh sau](https://gist.github.com/mickeysh/f9d7f6971615f845b855869a39be56e9#file-cleanup-sh) (như hình 31):

![kubectl commands to remove the test environment](https://d2908q01vomqb2.cloudfront.net/77de68daecd823babbb58edb1c8e14d7106e83bb/2025/04/01/Figure31.png)
*Hình 31: kubectl command remove test environment*

## Kết luận

Amazon EKS với NetApp Trident và FSx cho ONTAP cung cấp khả năng bảo vệ dữ liệu và phục hồi thảm họa mạnh mẽ cho containerized workload trên AWS. AWS Marketplace add-on đơn giản hóa triển khai, cho phép DevOps engineer thiết lập và quản lý environment của họ sử dụng các công cụ Amazon EKS quen thuộc.

Bài viết này đã demo cách triển khai Trident và cấu hình Trident Mirror Relationship giữa hai Amazon EKS cluster sử dụng AWS CLI, eksctl, và kubectl. Để bắt đầu, truy cập [NetApp Trident](https://aws.amazon.com/marketplace/pp/prodview-mptzckr6mggia) trong AWS Marketplace. Để xem Trident documentation, truy cập [Trident documentation repository](https://docs.netapp.com/us-en/trident/).

---

## 📖 Glossary - Thuật ngữ

| English | Tiếng Việt | Định nghĩa |
|---------|------------|------------|
| Amazon EKS | Amazon EKS | Dịch vụ Kubernetes được quản lý của AWS |
| NetApp Trident | NetApp Trident | CSI driver cho NetApp storage trong Kubernetes |
| FSx for ONTAP | FSx cho ONTAP | Dịch vụ file system được quản lý dựa trên NetApp ONTAP |
| Disaster Recovery (DR) | Phục hồi thảm họa | Quá trình khôi phục hệ thống sau sự cố |
| PersistentVolumeClaim (PVC) | PersistentVolumeClaim | Resource Kubernetes để yêu cầu storage |
| Trident Mirror Relationship (TMR) | Quan hệ mirror Trident | Cơ chế sao chép dữ liệu giữa các PVC |
| Container Storage Interface (CSI) | Giao diện lưu trữ container | Chuẩn API cho storage trong Kubernetes |
| Storage Virtual Machine (SVM) | Máy ảo lưu trữ | Đơn vị logic trong NetApp ONTAP |
| SnapMirror | SnapMirror | Công nghệ sao chép dữ liệu của NetApp |
| IAM Role | IAM Role | Vai trò xác thực và phân quyền AWS |
| OIDC Provider | OIDC Provider | Nhà cung cấp xác thực OpenID Connect |
| StorageClass | StorageClass | Định nghĩa loại storage trong Kubernetes |
| Backend Configuration | Cấu hình backend | Thiết lập kết nối đến storage system |
| Failover | Failover | Chuyển đổi sang hệ thống dự phòng |
| Failback | Failback | Chuyển đổi về hệ thống chính |
| GitOps | GitOps | Phương pháp triển khai sử dụng Git |
| Add-on | Add-on | Thành phần mở rộng cho EKS |
| Marketplace | Marketplace | Chợ ứng dụng AWS |
| Snapshot | Snapshot | Bản sao tức thì của dữ liệu |
| ReadWriteMany | ReadWriteMany | Chế độ truy cập đọc/ghi từ nhiều node |
| JSON Path | JSON Path | Cú pháp truy vấn dữ liệu JSON |
| Kubectl | Kubectl | Công cụ dòng lệnh Kubernetes |
| Eksctl | Eksctl | Công cụ quản lý EKS cluster |

## 🔗 Tài liệu tham khảo

### Tài liệu gốc
- [Deploy Amazon EKS advanced data protection using NetApp Trident](https://aws.amazon.com/blogs/awsmarketplace/deploy-amazon-eks-advanced-data-protection-using-netapp-trident-aws-marketplace-eks-add-on-and-amazon-fsx-for-netapp-ontap/): Bài viết gốc
- [NetApp Trident AWS Marketplace](https://aws.amazon.com/marketplace/pp/prodview-mptzckr6mggia): Sản phẩm trên AWS Marketplace
- [Author Profiles](https://aws.amazon.com/blogs/awsmarketplace/): Thông tin tác giả

### Tài liệu tiếng Việt
- [AWS EKS Documentation](https://docs.aws.amazon.com/eks/): Tài liệu EKS chính thức
- [AWS FSx Documentation](https://docs.aws.amazon.com/fsx/): Tài liệu FSx chính thức
- [AWS IAM Documentation](https://docs.aws.amazon.com/iam/): Tài liệu IAM chính thức

### Tools và Services
- [Amazon EKS](https://aws.amazon.com/eks/): Managed Kubernetes service
- [Amazon FSx for NetApp ONTAP](https://aws.amazon.com/fsx/netapp-ontap/): Managed file system
- [NetApp Trident](https://docs.netapp.com/us-en/trident/): CSI driver documentation
- [AWS Secrets Manager](https://aws.amazon.com/secrets-manager/): Secrets management service
- [AWS Marketplace](https://aws.amazon.com/marketplace/): Cloud software marketplace
- [Kubernetes CSI](https://kubernetes-csi.github.io/): Container Storage Interface
- [eksctl](https://eksctl.io/): EKS cluster management tool
- [kubectl](https://kubernetes.io/docs/reference/kubectl/): Kubernetes CLI
- [AWS CLI](https://aws.amazon.com/cli/): AWS command line interface
- [NetApp ONTAP](https://www.netapp.com/data-management/ontap-data-management-software/): Data management platform

---

## 💬 Ghi chú của người dịch

### Challenges trong quá trình dịch
- **Technical Terms**: Nhiều thuật ngữ chuyên ngành như "Trident Mirror Relationship", "SnapMirror" chưa có bản dịch chuẩn, đã giữ nguyên và giải thích
- **Complex Workflow**: Quy trình triển khai phức tạp với nhiều bước, cần duy trì tính chính xác của từng command
- **NetApp Terminology**: Thuật ngữ NetApp ONTAP và Trident cần được giữ nguyên để đảm bảo tính nhất quán

### Insights gained
- **Technical Learning**: Hiểu sâu về disaster recovery cho Kubernetes và storage replication
- **Enterprise Storage**: Nắm bắt được cách tích hợp enterprise storage với cloud-native platform
- **AWS Integration**: Học cách các third-party solution tích hợp với AWS Marketplace

---

## 🤝 Đóng góp và Feedback

Bài dịch này được thực hiện trong khuôn khổ **FCJ Internship Program**.

**📧 Liên hệ**: tritrantnt@gmail.com  
**💬 Feedback**: Mọi góp ý để cải thiện chất lượng dịch thuật xin gửi về email trên  
**🔄 Updates**: Bài dịch sẽ được cập nhật dựa trên feedback từ cộng đồng

---

*© 2025 - Bản dịch thuộc về Trần Ngọc Tri. Vui lòng credit khi sử dụng.*
