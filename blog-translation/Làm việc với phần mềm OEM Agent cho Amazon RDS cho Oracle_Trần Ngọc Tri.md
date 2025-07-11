# Làm việc với phần mềm OEM Agent cho Amazon RDS cho Oracle

> **📖 Bài viết gốc**: [Working with OEM Agent software for Amazon RDS for Oracle](https://aws.amazon.com/blogs/database/working-with-oem-agent-software-for-amazon-rds-for-oracle/)
> **👤 Tác giả**: Nipun Ravi và Ibrahim Emara - Database Specialist Solutions Architects at AWS
> **📅 Ngày xuất bản**: 04 April 2025
> **🌐 Nguồn**: AWS Database Blog
> **👨‍💻 Người dịch**: Trần Ngọc Tri - FCJ Intern
> **📅 Ngày dịch**: 01 July 2025
> **⏱️ Thời gian đọc**: 25 phút

---

## 📋 Tóm tắt

Bài viết này khám phá các tình huống quan trọng ảnh hưởng đến OEM Agents trên Amazon RDS cho Oracle Database instances và nêu bật các cân nhắc thiết yếu cho người dùng. Tác giả trình bày chi tiết về cấu hình OEM Management Agent, tác động của các hoạt động như minor version upgrade, agent upgrade, Multi-AZ failover, renaming instance và restore từ backup. Bài viết cũng so sánh với các tùy chọn monitoring khác như CloudWatch, Enhanced Monitoring và Performance Insights để giúp người dùng đưa ra quyết định phù hợp với môi trường của họ.

**🎯 Đối tượng đọc**: Database Administrators, Oracle DBAs, Infrastructure Engineers
**📊 Độ khó**: Advanced (300)
**🏷️ Tags**: Oracle Database, RDS, OEM Agent, Database Monitoring, Enterprise Manager

---

## 📚 Mục lục

- [Giới thiệu](#giới-thiệu)
- [Cấu hình OEM Management Agent trên Amazon RDS cho Oracle instance](#cấu-hình-oem-management-agent-trên-amazon-rds-cho-oracle-instance)
- [Amazon RDS cho Oracle minor version upgrade](#amazon-rds-cho-oracle-minor-version-upgrade)
- [Oracle Management Agent upgrade](#oracle-management-agent-upgrade)
- [Amazon RDS cho Oracle multi-AZ failure](#amazon-rds-cho-oracle-multi-az-failure)
- [Renaming Amazon RDS cho Oracle instance](#renaming-amazon-rds-cho-oracle-instance)
- [Restore Amazon RDS cho Oracle instance từ automated backups và snapshots](#restore-amazon-rds-cho-oracle-instance-từ-automated-backups-và-snapshots)
- [Các tùy chọn monitoring khác](#các-tùy-chọn-monitoring-khác)
- [Kết luận](#kết-luận)
- [Glossary - Thuật ngữ](#glossary---thuật-ngữ)
- [Tài liệu tham khảo](#tài-liệu-tham-khảo)

---

## Giới thiệu

Amazon Relational Database Service (Amazon RDS) cho Oracle là một dịch vụ database được quản lý hoàn toàn nhằm đơn giản hóa việc setup, vận hành và scaling các Oracle database trong cloud. Dịch vụ này giảm bớt nhu cầu về các tác vụ database administration tốn thời gian bằng cách xử lý provisioning, backups, software patching, monitoring và hardware scaling. Với Amazon RDS cho Oracle, bạn có thể tập trung vào application development và innovation trong khi Amazon Web Services (AWS) quản lý underlying infrastructure.

Oracle Enterprise Manager (OEM) là một comprehensive management tool được thiết kế để monitor và quản lý Oracle databases và applications. Nó cung cấp một unified interface cho system administrators và database professionals, cho phép họ streamline operations, optimize performance và monitor health của IT environments. Với các tính năng như real-time monitoring, automated diagnostics và performance tuning, OEM empowers các tổ chức để enhance productivity và reduce downtime.

Oracle Management Agent (OEM Agent) trong Amazon RDS cho Oracle là một component cho phép monitoring và management của Oracle databases chạy trên RDS instances. OEM Agent là một lightweight process chạy trên Amazon RDS cho Oracle instance và communicate với Oracle Enterprise Manager Cloud Control, là một centralized management console được cung cấp bởi Oracle.

Amazon RDS cho Oracle hỗ trợ các phiên bản cụ thể của OEM Agent. Amazon gần đây đã phát hành OEM Management Agent version 13.5.0.0.v2 cho Amazon RDS cho Oracle databases. OEM Agent version 13.5.0.0.v2 yêu cầu Oracle Management Server (OMS) version 13.5.0.23 ở mức tối thiểu.

## Cấu hình OEM Management Agent trên Amazon RDS cho Oracle instance

OEM bao gồm một số key components: Management Server, xử lý dữ liệu và cung cấp user interface; Management Repository, nơi configuration và performance data được stored; và OEM Agents được installed trên target databases, collect metrics và gửi chúng về Management Server. Kiến trúc này cho phép real-time monitoring, alerting và reporting về database performance, resource usage và security.

Với Amazon RDS cho Oracle, bạn cần enable OEM Agent option trong một custom options group để include OEM Agent. Tạo custom options group nếu nó không tồn tại và associate nó với Amazon RDS cho Oracle instance của bạn. Nhớ cũng properly configure OMS details, bao gồm hostname, port và security group settings. Có proper network connectivity giữa OMS và RDS instance cũng rất quan trọng để OEM Agent function correctly.

Sau khi bạn đã enabled OEM Agent option, RDS instance của bạn sẽ enter modifying state trong quá trình agent installation. Sau khi installation complete và instance returns về available state, OEM Agent sẽ được enabled và ready for use với Oracle Enterprise Manager setup của bạn.

Bảng sau đây hiển thị các tình huống khác nhau và tác động của chúng lên OEM Agent cho Amazon RDS cho Oracle. Behavior tương tự cho OEM Agents trên cả non-multitenant và multitenant Amazon RDS cho Oracle databases.

| Tình huống                                                       | RDS minor version upgrade | OEM Management agent version upgrade                                                                               | Multi-AZ failure   | Rename của RDS instance        | Restore của RDS instance sử dụng PITR hoặc snapshots |
| ------------------------------------------------------------------ | ------------------------- | ------------------------------------------------------------------------------------------------------------------ | ------------------ | ------------------------------- | -------------------------------------------------------- |
| **OEM Agent version 13.4.0.9.v1 với OMS version 13.4.0.19** | Không tác động        | Đến 13.5.0.0.v1 – Historical metrics data bị mất`<br>`Đến 13.5.0.0.v2 – Historical metrics data bị mất | Không tác động | Add RDS instance như new agent | Không tác động                                       |
| **OEM Agent version 13.4.0.9.v1 với OMS version 13.5.0.23** | Không tác động        | Đến 13.5.0.0.v1 – Historical metrics data bị mất`<br>`Đến 13.5.0.0.v2 – Historical metrics data bị mất | Không tác động | Add RDS instance như new agent | Historical metrics data bị mất                         |
| **OEM Agent version 13.5.0.0.v1 với OMS version 13.5.0.23** | Không tác động        | Đến 13.5.0.0.v2 – Không tác động                                                                            | Không tác động | Add RDS instance như new agent | Historical metrics data bị mất                         |
| **OEM Agent version 13.5.0.0.v2 với OMS version 13.5.0.23** | Không tác động        | Không áp dụng                                                                                                   | Không tác động | Add RDS instance như new agent | Không tác động                                       |

## Amazon RDS cho Oracle minor version upgrade

Minor version upgrade áp dụng Oracle Database patch set update (PSU) hoặc Release Update (RU) cho major engine version. Upgrade bao gồm upgrading cả database binaries và database dictionary. Minor version upgrades không impact binaries hoặc configuration files của OEM Management Agent, và agent functionality sẽ không bị impacted bởi những upgrades này.

## Oracle Management Agent upgrade

Tùy thuộc vào current và target OEM Agent version trên Amazon RDS cho Oracle, bạn có thể observe different behavior khi performing upgrade.

### OEM Agent upgrade từ version 13.4.0.9.v1 đến 13.5.0.0.v1 hoặc 13.5.0.0.v2

Khi upgrading OEM Agent version từ 13.4.0.9.v1 đến 13.5.0.0.x, Amazon RDS cho Oracle sẽ remove old 13.4.0.9.v1 OEM Agent từ custom options group associated với RDS instance và add new 13.5.0.0.x OEM Agent. Tuy nhiên, điều này sẽ result in plugin version mismatch cho database targets vì new 13.5 OEM Agent có different plugin so với old 13.4 OEM agent.

Trying to install 13.4 plugin trên 13.5 OEM Agent cũng sẽ fail và bạn sẽ cần re-add database targets đến new 13.5 OEM Agent. Điều này có thể lead đến việc bạn losing historical performance metrics associated với những targets đó.

Để preserve historical performance data, bạn có thể choose install new OMS instance và configure tất cả targets với new 13.5 OEM Agents trong khi keeping old OMS running như standalone historical data repository. Để optimize costs, bạn cũng có thể consider scaling down Amazon Elastic Compute Cloud (Amazon EC2) instance types cho cả old OMS và Oracle Management Repository (OMR).

### OEM Agent upgrade từ version 13.5.0.0.v1 đến 13.5.0.0.v2

Để upgrade OEM Agent version 13.5.0.0.v1 đến 13.5.0.0.v2, bạn cần remove OEM Agent option 13.5.0.0.v1 từ custom options group associated với RDS instance và add 13.5.0.0.v2 OEM Agent option instead. Sau khi OEM Agent option đã được changed cho Amazon RDS cho Oracle instance, OMS sẽ continue communicate với new OEM Agent và historical performance data sẽ vẫn available cho Amazon cho Oracle instance và target database.

## Amazon RDS cho Oracle multi-AZ failure

Trong Multi-AZ deployment, RDS automatically tạo primary database (DB) instance và synchronously replicates data đến instance trong different Availability Zone (AZ). Khi nó detects failure, RDS automatically fails over đến standby instance mà không cần manual intervention.

Khi bạn provision OEM Agent trên primary instance trong Multi-AZ deployment, cả binaries và configuration files sẽ được written đến storage của secondary instance như part của continuous data replication happens giữa hai instances. Amazon RDS cho Oracle sử dụng RDS instance endpoint để configure host name của OEM Agent, và endpoint này sẽ không change trong case của failover đến secondary instance.

Kết quả là, OEM Agent sẽ continue run sau Multi-AZ failover mà không cần reconfigure. Tình huống này có thể được tested bằng cách rebooting RDS instance với choosing failover option.

## Renaming Amazon RDS cho Oracle instance

Đôi khi bạn có thể cần rename Amazon RDS cho Oracle instances của mình. Có thể có situation mà bạn đang trying clone lower environment databases từ production. Renaming RDS instance sẽ interrupt communication giữa instance và OMS. Điều này là vì renaming instance changes hostname, nên OMS không thể reestablish contact với OEM Agent.

Để resolve điều này, bạn cần modify instance để associate nó với default options group, sau đó modify lại để associate với custom options group. Điều này triggers creation của new OEM Agent trong OMS matching changed instance name.

Tại điểm này, bạn phải manually add database target đến newly created OEM Agent này.

Renaming RDS instance disrupts OMS-OEM Agent communication, necessitating new OEM Agent registration. Process này results in loss của historical performance metrics associated với previous OEM Agent. Bạn nên factor điều này vì nó có thể impact ability để access previously collected data.

Đáng chú ý là sau khi renaming instance, nếu bạn change nó back đến original name, OMS có thể reestablish communication với OEM Agent và historical performance metrics không bị lost.

## Restore Amazon RDS cho Oracle instance từ automated backups và snapshots

Đôi khi bạn có thể cần restore Amazon RDS cho Oracle instance đến previous point in time hoặc từ manual snapshot. Trong những scenarios này, bạn cần delete hoặc rename original instance trước khi proceeding với restore.

Deleting hoặc renaming original instance breaks communication giữa instance đó và associated OEM Agent target trong OMS, rendering OEM Agent unreachable.

Sau khi restore complete, OEM Agent sẽ automatically enabled trên new instance, miễn là bạn đã used same options group được configured cho original instance.

Tuy nhiên, behavior từ điểm này forward depends on OEM Agent version:

- **Với OEM Agent version 13.4.0.9.v1 chạy trên OMS version 13.4.0.19**: Resynchronization từ OMS sẽ allow new OEM Agent trên restored instance reestablish communication với already registered OEM Agent trong OMS.
- **Với OEM Agent version 13.4.0.9.v1 chạy trên OMS version 13.5.0.23**: Communication sẽ không reestablish ngay cả sau resynchronization.
- **Với OEM Agent version 13.5.0.0.v1**: Communication sẽ không reestablish ngay cả sau resynchronization.
- **Với OEM Agent version 13.5.0.0.v2**: Resynchronization từ OMS sẽ allow new OEM Agent trên restored instance reestablish communication với already registered OEM Agent trong OMS.

Key challenge ở đây là restoring RDS instance requires deleting original, breaks OMS integration. Bạn cần aware về differing behaviors giữa OEM Agent versions khi comes to reestablishing connection sau khi restore complete.

## Các tùy chọn monitoring khác

Ngoài OEM, AWS cung cấp other monitoring mechanisms cho Amazon RDS cho Oracle databases:

### 1. Amazon CloudWatch

Amazon CloudWatch cung cấp several built-in metrics cho RDS instances, như CPU utilization, database connections và free storage space. Bạn có thể set up Amazon CloudWatch alarms để notify khi specific metrics exceed defined thresholds. CloudWatch Logs có thể được used để collect và analyze database logs, bao gồm error logs, audit logs và slow query logs.

### 2. Enhanced Monitoring

Enhanced monitoring cung cấp additional metrics cho RDS instances, bao gồm disk I/O, CPU và memory utilization ở operating system level. Những metrics này có thể help identify performance bottlenecks và potential issues.

### 3. Amazon RDS Performance Insights

Performance Insights là database performance tuning và monitoring feature cung cấp detailed view của load trên database instances. Nó captures metrics như SQL statements, wait events và OS processes, và presents chúng trong straightforward format.

Performance Insights offers rolling 7 days của performance data history at no charge. Điều này đủ time để troubleshoot wide variety của issues. Để biết pricing details around retention của performance data cho more than 7 days trong Performance Insights, tham khảo Performance Insights pricing.

## Kết luận

Trong bài viết này chúng tôi đã explored critical scenarios affecting OEM Agents trên Amazon RDS cho Oracle Database instances. Oracle Enterprise Manager offers robust mechanism cho monitoring Oracle Databases và other applications. Tuy nhiên, nếu bạn anticipate frequent changes trong environment, như renaming RDS instance hoặc restoring RDS instance, điều này có thể render OEM Agent unreachable và require bạn reregister OEM Agent như new trong OMS.

Để avoid scenario như vậy, bạn có thể opt cho alternative options như Performance Insights available với RDS instance như primary mechanism để monitor Oracle databases. Điều này có thể provide more stable và reliable monitoring solution, đặc biệt trong environments prone to frequent infrastructure changes.

---

## 📖 Glossary - Thuật ngữ

| English                             | Tiếng Việt                  | Định nghĩa                                                                         |
| ----------------------------------- | ----------------------------- | ------------------------------------------------------------------------------------- |
| Oracle Enterprise Manager (OEM)     | Oracle Enterprise Manager     | Comprehensive management tool của Oracle để monitor và manage databases           |
| Oracle Management Agent (OEM Agent) | Oracle Management Agent       | Component chạy trên database instance để collect metrics và communicate với OMS |
| Oracle Management Server (OMS)      | Oracle Management Server      | Centralized server xử lý data và cung cấp management interface                    |
| Management Repository (OMR)         | Management Repository         | Database repository lưu trữ configuration và performance data                      |
| Amazon RDS for Oracle               | Amazon RDS cho Oracle         | Fully managed Oracle database service của AWS                                        |
| Multi-AZ Deployment                 | Triển khai Multi-AZ          | High availability deployment across multiple Availability Zones                       |
| Point-in-Time Recovery (PITR)       | Khôi phục theo thời điểm | Khả năng restore database đến một thời điểm cụ thể                          |
| Custom Options Group                | Nhóm tùy chọn tùy chỉnh  | Configuration group cho RDS instance với custom settings                             |
| Minor Version Upgrade               | Nâng cấp phiên bản nhỏ   | Upgrade với patch set hoặc release update                                           |
| Performance Insights                | Performance Insights          | AWS service để analyze database performance                                         |
| Enhanced Monitoring                 | Enhanced Monitoring           | Detailed OS-level monitoring cho RDS instances                                        |
| CloudWatch                          | CloudWatch                    | AWS monitoring và logging service                                                    |
| Patch Set Update (PSU)              | Cập nhật bộ vá            | Collection của bug fixes cho Oracle Database                                         |
| Release Update (RU)                 | Cập nhật phát hành        | Newer patching model thay thế PSU                                                    |
| Availability Zone (AZ)              | Vùng khả dụng              | Isolated location trong AWS region                                                    |
| Failover                            | Chuyển đổi dự phòng      | Automatic switch to standby instance khi primary fails                                |
| Database Target                     | Target database               | Database instance được monitored bởi OEM                                          |
| Plugin Version Mismatch             | Lỗi không khớp plugin      | Incompatibility giữa agent và target database plugins                               |
| Historical Metrics Data             | Dữ liệu metrics lịch sử   | Performance data collected over time                                                  |
| Resynchronization                   | Đồng bộ hóa lại          | Process để reestablish communication giữa components                               |

## 🔗 Tài liệu tham khảo

### Tài liệu gốc

- [Working with OEM Agent software for Amazon RDS for Oracle](https://aws.amazon.com/blogs/database/working-with-oem-agent-software-for-amazon-rds-for-oracle/): Bài viết gốc
- [Monitor Amazon RDS for Oracle instances using Oracle Enterprise Manager](https://aws.amazon.com/blogs/database/monitor-amazon-rds-for-oracle-instances-using-oracle-enterprise-manager/): Hướng dẫn monitoring với OEM
- [Oracle Enterprise Manager Cloud Control](https://docs.oracle.com/en/enterprise-manager/index.html): Oracle documentation

### Tài liệu AWS RDS cho Oracle

- [Amazon RDS for Oracle User Guide](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Oracle.html): Hướng dẫn sử dụng RDS Oracle
- [Oracle Management Agent for Enterprise Manager Cloud Control](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Oracle.Options.OEMAgent.html): OEM Agent options
- [Working with Option Groups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithOptionGroups.html): Quản lý option groups

### Monitoring và Performance

- [Amazon RDS Performance Insights](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PerfInsights.html): Performance Insights documentation
- [Enhanced Monitoring](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_Monitoring.OS.Enabling.html): Enhanced monitoring setup
- [Amazon CloudWatch for RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/monitoring-cloudwatch.html): CloudWatch monitoring
- [Performance Insights Pricing](https://aws.amazon.com/rds/performance-insights/pricing/): Chi phí Performance Insights

### Oracle Database Resources

- [Oracle Database Versions](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Oracle.Concepts.database-versions.html): Supported Oracle versions
- [Automated Backups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_ManagingAutomatedBackups.html): Backup management
- [Multi-AZ Deployments](https://aws.amazon.com/rds/features/multi-az/): High availability features
- [Oracle Read Replicas](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/oracle-read-replicas.html): Read replica setup

### AWS Services

- [Amazon EC2](https://aws.amazon.com/ec2/): Compute service cho OMS hosting
- [AWS Database Migration Service](https://aws.amazon.com/dms/): Database migration tools
- [AWS Schema Conversion Tool](https://aws.amazon.com/sct/): Schema conversion utilities

---

## 💬 Ghi chú của người dịch

### Challenges trong quá trình dịch

- **Technical Oracle Terminology**: Nhiều thuật ngữ Oracle Database cần được dịch chính xác để tránh hiểu nhầm về technical concepts
- **Version-Specific Behavior**: Các behavior khác nhau giữa versions cần được trình bày rõ ràng
- **Database Administration Context**: Cần hiểu sâu về DBA workflows để dịch chính xác

### Insights gained

- **OEM Architecture**: Hiểu sâu hơn về Oracle Enterprise Manager architecture và integration với AWS RDS
- **RDS Operations Impact**: Nắm được cách các RDS operations ảnh hưởng đến monitoring tools
- **Monitoring Strategy**: Học được về trade-offs giữa OEM và native AWS monitoring solutions

### Lưu ý về implementation

Khi làm việc với OEM Agent trên RDS Oracle, cần chú ý:

- **Version Compatibility**: Đảm bảo compatibility giữa OEM Agent và OMS versions
- **Network Configuration**: Proper security group và network setup cho OMS communication
- **Change Management**: Plan carefully cho infrastructure changes để avoid monitoring disruption
- **Data Retention**: Consider historical data loss khi upgrading hoặc making changes
- **Alternative Monitoring**: Evaluate AWS-native monitoring solutions cho stability

### Recommendations

Cho production environments:

- **Use Performance Insights** cho stable monitoring solution
- **Plan OEM upgrades carefully** để minimize historical data loss
- **Test failover scenarios** để ensure monitoring continuity
- **Document procedures** cho agent re-registration
- **Consider hybrid approach** với multiple monitoring tools

---

## 🤝 Đóng góp và Feedback

Bài dịch này được thực hiện trong khuôn khổ **FCJ Internship Program**.

**📧 Liên hệ**: tritrantnt@gmail.com
**💬 Feedback**: Mọi góp ý để cải thiện chất lượng dịch thuật xin gửi về email trên
**🔄 Updates**: Bài dịch sẽ được cập nhật dựa trên feedback từ cộng đồng

---

*© 2025 - Bản dịch thuộc về Trần Ngọc Tri. Vui lòng credit khi sử dụng.*
