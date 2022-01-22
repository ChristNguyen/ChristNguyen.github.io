-- title: "**Giới thiệu Apache NiF**

categories:

-   Big data
-   Data Engineering

Với các bạn đang phải thực hiện các công việc như: crawl dữ liệu trên các website, lấy log của các hệ thống ứng dụng hay lấy dữ liệu từ server FTP, DBMS, Kafka để xử lý và đưa vào một hệ thống lưu trữ tập trung (ví dụ như HDFS), … chắc hẳn đã từng phải đối mặt với một trong số vấn đề như:

-   Làm sao để đảm bảo tốc độ lấy được dữ liệu từ nguồn về nơi lưu trữ đáp ứng được các tác vụ phía sau.
-   Làm sao để xây dựng phần xử lý cho nhiều định dạng khác nhau của dữ liệu và nguồn dữ liệu.
-   Hay phải đảm bảo an toàn dữ liệu, không để bị mất, sai lệch hay bị lặp dữ liệu.

Sau khi được giới thiệu và tự tìm hiểu thì mình thấy một công cụ khá hiệu quả để thực hiện các công việc trên, đó là Apache NiFi. Nếu các bạn đã từng đọc qua bài viết về kiến trúc hệ thống dữ liệu lớn [(1)](https://christnguyen.ml/Ki%E1%BA%BFn-tr%C3%BAc-d%E1%BB%AF-li%E1%BB%87u-l%E1%BB%9Bn-(Big-Data-Architecture)/) đã được đăng trong group, thì có thể hiểu rằng NiFi là một công cụ thuộc nhóm thu thập dữ liệu (Data Ingestion), giữ vai trò thu thập, xử lý và chuyển dữ liệu từ các nguồn dữ liệu (Data Source) thành phần lưu trữ dữ liệu (Data Storage) hoặc tới thành phần thu thập dữ liệu thời gian thực (Real-Time Message Ingestion).

Apache Nifi là gì?

Apache NiFi là một phần mềm open-source viết bằng Java, được tạo ra để tự động hóa luồng dữ liệu giữa các hệ thống phần mềm với nhau. Nó được xây dựng từ năm 2006 dựa trên phần mềm “NiagaraFiles” phát triển bởi anh NSA, sau đó được chuyển sang open-source vào năm 2014 (Nguồn Wiki).

![](https://scontent.fhan15-1.fna.fbcdn.net/v/t1.6435-9/89472028_2483041638577239_9015106792044101632_n.jpg?_nc_cat=106&ccb=1-5&_nc_sid=8ecba9&_nc_ohc=aVNka6AtiNYAX9AH2jP&_nc_ht=scontent.fhan15-1.fna&oh=00_AT_Kpxo982NnkXpoFHi715bRe1bjOhGRzCfF9Acicl5c4A&oe=6210BEFF)

  

NiFi được biết đến với khả năng xây dựng luồng chuyển dữ liệu tự động giữa các hệ thống. Đặc biết là hỗ trợ rất nhiều kiểu nguồn và đích khác nhau như:

-   Các loại RDBMS: Oracle, MySql, Postgre, …
-   Các loại DB NoSQL: Mongo, HBase, Cassandra, …
-   Từ các nguồn web như: HTTP, web-socket
-   Lấy hoặc đẩy dữ liệu streaming vào Kafka
-   Hay là từ: FTP, log

Ngoài việc rút ra và đẩy vào dữ liệu thì NiFi còn các chức năng như routing dữ liệu theo thuộc tính và nội dung, xử lý dữ liệu như: lọc, chỉnh sửa, thêm bớt nội dung của dữ liệu trước khi đưa đến nơi lưu trữ.

![](https://scontent.fhan15-1.fna.fbcdn.net/v/t1.6435-9/89435999_2483042021910534_394336108680314880_n.jpg?_nc_cat=102&ccb=1-5&_nc_sid=8ecba9&_nc_ohc=DOn6k5iL3wkAX-WsEsF&tn=xiO5Ov5pkJDvLtMO&_nc_ht=scontent.fhan15-1.fna&oh=00_AT_9yFvKoFi76CRikP3Rv0XghuZvt-olRuEZEkeZ3tPb6A&oe=6212A734)

(Nguồn Medium)
Apache Nifi có gì hay?

Ba nhóm tính năng nổi bật của Nifi bao gồm khả năng quản lý luồng dữ liệu; việc sử dụng, vận hành một cách dễ dàng; và khả năng mở rộng. Khi lựa chọn có dùng Nifi cho tầng thu thập dữ liệu (Data Ingestion) hay không, các bạn có thể tham khảo các tính năng này để quyết định sử dụng cho dự án của mình:

Thứ nhất là về khả năng quản lý luồng dữ liệu:

-   Đảm bảo an toàn: Mỗi đơn vị dữ liệu trong luồng của bạn sẽ được biểu diễn bởi một Object có tên là FlowFile. Nó sẽ ghi lại tất cả các thông tin về dữ liệu trong luồng như đang được xử lý bởi khối nào, đang được chuyển đi đâu, … Lịch sử xử lý của một FlowFile lại được lưu trữ trong Provenance Repo để chúng ta truy vết. Kết hợp với cơ chế Copy-on-Write, NiFi lưu trữ lại dữ liệu tại từng bước trong luồng trước khi xử lý, giúp ta dễ dàng chạy lại dữ liệu.
-   Data Buffering: tính năng này giúp giải quyết vấn đề tốc độ ăn chậm hơn tốc độ nhả giữa hai hệ thống khác nhau. Nó hoạt động dựa theo cơ chế Queue giữa hai khối xử lý trong luồng. Dữ liệu này sẽ được giữ trên RAM, nhưng nếu nó vượt qua ngưỡng mình cài thì dữ liệu sẽ được đưa xuống ổ cứng.
-   Thiết lập độ ưu tiên: trong một số trường hợp mà ta sẽ cần xử lý dữ liệu này trước khi xử lý những thằng khác. Ví dụ như log có nhãn error chẳng hạn, thường thì ta muốn nó có thể được xử lý ngay lập tức trước khi xử những thằng warning.
-   Hỗ trợ đánh đổi giữa tốc độ và khả năng chịu lỗi: Có những luồng dữ liệu ta cần đảm bảo tuyệt đối về tính toàn vẹn và an toàn của dữ liệu chấp nhận độ trễ cao. Và có những luồng ta lại cần chuyển được dữ liệu tới đích trong thời gian ngắn nhất có thể. NiFi sẽ hỗ trợ bạn cài đặt để cân bằng giữa hai yếu tố này.

Thứ hai là về vấn đề sử dụng có dễ dàng hay không:

![](https://scontent.fhan15-1.fna.fbcdn.net/v/t1.6435-9/89774839_2483042428577160_2588228450271625216_n.jpg?_nc_cat=105&ccb=1-5&_nc_sid=8ecba9&_nc_ohc=Q_9iTPyOPoIAX9-ODv9&tn=xiO5Ov5pkJDvLtMO&_nc_ht=scontent.fhan15-1.fna&oh=00_AT_NbcaYcH2ZnmG61WzeQv4kaPK1reQKBGWsvm26v74c7A&oe=621350FF)

  

-   Việc tạo ra một luồng dữ liệu của bạn sẽ được thực hiện hoàn toàn trên giao diện WEB, và bằng vài thao tắc kéo thả bạn sẽ nhanh chóng tạo được một luồng đơn giản.
-   Tính tái sử dụng cũng được hỗ trợ, bạn có thể tạo ra một template chứa một luồng cơ bản để sử lại khi cần.
-   Theo dõi trực quan lịch sử xử lý của dữ liệu khi cần kiểm tra lỗi.
-   Chạy lại được cả dữ liệu tại từng bước xử lý
-   Bạn sẽ dễ dàng lập trình được một thành phần xử lý, điều khiển, report hay UI trong NiFi khi cần. Ví dụ như một khối encode hoặc decode dữ liệu chẳng hạn.

![](https://scontent.fhan15-1.fna.fbcdn.net/v/t1.6435-9/89049676_2483042615243808_4230800480302792704_n.jpg?_nc_cat=102&ccb=1-5&_nc_sid=8ecba9&_nc_ohc=mqi1B2VHEkgAX-Wk638&_nc_ht=scontent.fhan15-1.fna&oh=00_AT-QzUDWHgFY9SDIBBqXF1M4fd3osXWtVc97--Q4kn_baA&oe=62124BBA)

  

Cuối cùng, cần nhắc tới một tính năng quan trọng của các ứng dụng trong các hệ thống phân tán là khả năng mở rộng theo chiều ngang (thêm server vào cụm ấy). Nếu một luồng dữ liệu trên trên một server NiFi có thể xử lý được 100MB/s , nhưng yêu cầu thực tế lại lên đến 500MB/s thì các bạn có thể cài đặt một cụm gồm nhiều server để xử lý dữ liệu một cách song song mà không cần nâng cấu hình của server.

Những ai đang sử dụng Apache NiFi?

Với các ưu điểm, tính năng ở trên, Apache Nifi đang được sử dụng ngày càng nhiều trong các technology stack của các hàng lớn như Cloudera, Looker, …

-   [Micron](https://www.micron.com/?fbclid=IwAR380Ej5ybroaR-3wS-FQRm572wcUe5IZ4nmfJ6-tiXwr515gK2AzKMtfZw): là một công ty trong ngành công nghiệp sản xuất chất bán dẫn. Họ sử dụng NiFi để thu thập dữ liệu sản xuất trên toàn thế giới của mình và đưa về các kho dữ liệu tập trung. Dữ liệu này được khai thác và đưa cho cái nhìn toàn diện về hoạt động sản xuất. Đối với các luồng dữ liệu yêu cầu nặng về khả năng xử lý, giao thức NiFi site-to-site được sử dụng để cung cấp dữ liệu liên tục đến các job Spark trên cụm Hadoop. Họ cũng sử dụng Rest API của NiFi để tự động hóa việc tạo các luồng và quản lý chúng.
-   [Macquarie Telecom Group:](https://macquarietelecomgroup.com/?fbclid=IwAR0OQQNqter1Z4COv_sRiLDY17gOBl58skaVyroYx8imG3hqyrPxfSezN7k) hoạt động trong lĩnh vực cloud và viễn thông. Công ty này sử dụng NiFi để chuyển hàng triệu bản ghi về các sự kiện giữa các data center, đồng thời biến đổi và làm giàu cho dữ liệu.
-   [Dovestech](https://www.dovestech.com/?fbclid=IwAR1iALtqNvpepzXCSqIYkIdc-d8aZ2KycvlV3URUGRDYj1eSEDPziV5-AYs): là một công ty an ninh mạng. Họ sử dụng NiFi để làm giàu và chuẩn hóa hàng triệu bản ghi liên quan đến an ninh mạng rồi đưa vào cơ sở dữ liệu tập trung, phục vụ cho sản phẩm trực quan hóa an ninh mạng – [ThreatPop](http://www.dovestech.com/threatpop/?fbclid=IwAR11LBLkF2VNVoF7AEvjc7zoq2tG5oX31kOahHprxadht56xOv5oXbVLjlU).
-   [Looker](https://looker.com/?fbclid=IwAR1TysL-i5-YaqXSXm-KUT-uLBM-hRL4Xgd13oKMrCNDxLoTJugWwIF_Obs): công ty cung cấp các phần mềm SaaS và phân tích. Hiện nay, mọi luồng dữ liệu mới của họ đều xây dựng bằng NiFi. Các luồng dữ liệu cũ cũng đang được chuyển dần sang sử dụng NiFi. Công ty này triển khai các cụm NiFi để thu thập, biến đổi và chuyển dữ liệu lên các nền tảng khác nhau như: Google big query, Amazon Redshift và Amazon S3.

Các bạn có thể xem thêm các công ty đã sử dụng NiFi ở đây [(2)](https://nifi.apache.org/powered-by-nifi.html?fbclid=IwAR3wsXZ_H9-pvm5sLuoVublODe6Wb4atlWCbZwWx4LFUUOISNXmZAU9F-os)

Ngoài Apache Nifi, còn có các lựa chọn nào khác?

Đối với thành phần thu thập dữ liệu trong một hệ thống Big Data, còn có thể có các framework khác, các bạn có thể tùy thuộc vào từng bài toán cụ thể mà chọn công cụ phù hợp. Tuy nhiên, qua nghiên cứu và triển khai thực tế, mình thấy Nifi dễ dàng cài đặt, sử dụng cũng như quản trị, khả năng mở rộng của Nifi cũng đáp ứng được yêu cầu hệ thống Big Data của mình.

-   Apache Sqoop: công cụ dùng trên command line, được sử dụng để chuyển dữ liệu 2 chiều giữa Hadoop và RDBMS.
-   Apache Flume: là một công cụ dùng để đẩy dữ liệu một chiều từ một số nguồn dữ liệu lên nơi lưu trữ như Hadoop, Hbase, ... Flume giải quyết tốt dữ liệu real time với thông lượng cao và độ trễ thấp. Tuy nhiên, luồng dữ liệu được cấu hình trên file properties trong java, tương đối phức tạp. Ngoài ra, sự đảm bảo về dữ liệu của Flume cũng yếu hơn so với các hệ thống khác (xảy ra hiện tượng duplicate dữ liệu [(2)](https://www.oreilly.com/library/view/data-lake-for/9781787281349/1be49776-3294-44ee-850d-aa5f2f6eb5cf.xhtml?fbclid=IwAR2VyEkcqbVwJJZKqxG69rryDFhtzGuBBJ26Q-C2zK8jbIiPKWuc7Y4ZgRk)) do ưu tiên về thời gian xử lý.
-   Apache Flink: một framework xử lý dữ liệu trực tuyến trong môi trường phân tán. Nó có tốc độ xử lý, hiệu năng và tính sẵn sàng cao. Nó hoạt đông rất hiệu quả với các ứng dụng phân tích dữ liệu trực tuyến (ví dụ như live report, phát hiện bất thường,…). Tuy nhiên khả năng thu thập dữ liệu từ nhiều nguồn khác nhau không được đa dạng như NiFi. Vì vậy, ta nên kết hợp khả năng thu thập dữ liệu của NiFi với sức mạnh xử lý những tính toán phức tạp, tốc độ cao của Flink.

Ở trên mình đã giới thiệu một chút về công cụ Apache NiFi, rất mong được nghe chia sẻ của các bạn về kinh nghiệm sử dụng NiFi hoặc các công cụ khác.
