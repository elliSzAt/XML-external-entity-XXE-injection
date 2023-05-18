# XML-external-entity-XXE-injection

XML external entity injection hay còn gọi là XXE injection, là 1 lổ hổng web cho phép hacker can thiệp vào quá trình xử lý dữ liệu XML. Nó thường cho phép hacker xem có file trên server và tương tác với bất kì hệ thống hackend mà ứng dụng có thể tương tác. Trong 1 số tuwrowfng hợp, hacker có thể leo thang XXE để xâm phạm server bên dưới hoặc hệ thống backend khác, bằng cách tận dụng XXE để thực hiện SSRF.  

Trước khi đi vào tìm hiểu lỗ hổng này, trước hết ta sẽ tìm hiểu về XML.  

# XML

Trong lập trình ứng dụng web, XML được sử dụng nhiều nhất là xây dựng các API Service. Các API sẽ trả kết quả về dạng XML hoặc JSON để các hệ thống khác có thể nói nói chuyện với nhau được. Hiện nay tuy JSON được sử dụng phổ biến hơn, nhưng XML cũng vẫn đang được dùng bởi nhiều hệ thống lớn.  

**XML** là viết tắt của **Extensible Markup Language** là ngôn ngữ đánh dấu giống như HTML. XML có chức năng truyền dữ liệu và mô tả nhiều loại dữ liệu khác nhau. Tác dụng chính của XML là đơn giản hóa việc chia sẻ dữ liệu giữa các nền tảng và các hệ thống được kết nối thông qua mạng Internet.  

XML dùng để cấu trúc, lưu trữ và trong trao đổi dữ liệu giữa các ứng dụng và lưu trữ dữ liệu. Ví dụ khi ta xây dựng một ứng dụng bằng Php và một ứng dụng bằng Java thì hai ngôn ngữ này không thể hiểu nhau, vì vậy ta sẽ sử dụng XML để trao đổi dữ liệu. Chính vì vậy, XML có tác dụng rất lớn trong việc chia sẻ, trao đổi dữ liệu giữa các hệ thống.  

# XML không làm gì cả

Dưới đây là 1 ví dụ về XML:  

    <?xml version="1.0" encoding="UTF-8"?>
    <ShopOrder>
      <Order>
       // không gian tên cus
       <cus:Customer xmlns:cus="https://freetuts.net/customer">
           <cus:Title>ElliSzAt</cus:Title>
           <cus:Address>KMA</cus:Address>
       </cus:Customer>
      </Order>
    </ShopOrder>

XML trên là sự mô tả: 

  - Tên khách hàng.  
  - Địa chỉ khách hàng.  

Ta có thể nói là XML không làm gì cả, nó chỉ là 1 mớ thông tin nằm trong các tag.  

# So sánh XML và HTML

**XML** và **HTML** giống nhau đều là các thẻ (tag).  

**XML**

  - Do người dùng định nghĩa.  
  - Được thiết kế để chuyển tải và lưu trữ dữ liệu, tập trung vào "what data are".  

**HTML**  

  - Được định nghĩa trước và người dùng phải tuân thủ.  
  - Được thiết kế để hiển thị dữ liệu, tập trung vào "how data look".  

# XML dùng để làm gì? 

  - XML được dùng trong nhiều khía cạnh của web development.  

  - XML thường được sử dụng để tách biệt data khỏi phần trình bày.  

**XML tách biệt data khỏi phần trình bày**  

  - XML không chứa bất kì thông tin nào về cách data được hiển thị. Dữ liệu XML giống nhau có thể được sử dụng trong nhiều trường hợp trình bày khác nhau. Bởi vì điều này, với XML có sự tách biệt hoàn toàn giữa dữ liệu và bản trình bày.

**XML thường bổ sung cho HTML**

  - Trong nhiều ứng dụng HTML, XML được sử dụng để lưu trữ và vận chuyển thông tin, trong khi HTML được sử dụng để format và hiển thị data đó.  

**XML tách biệt data khỏi HTML**

  - Khi hiển thị data trong HTML ta không cần phải edit HTML khi data thay đổi. Với XML, data được lưu trữ trong 1 file XML tách biệt.

# Ưu và nhược điểm của XML

**Ưu điểm**  

  - Ưu điểm lớn nhất của XML là sự độc lập. XML được sử dụng để mô tả dữ liệu dưới dạng text, nên hầu hết các phần mềm hay các chương trình bình thường đều có thể đọc được chúng.  
  - XML có thể đọc và phân tích nguồn dữ liệu khá dễ dàng nên nó được sử dụng với mục đích chính là trao đổi dữ liệu giữa các chương trình, các hệ thống khác nhau. XML được sử dụng cho Remote Procedure Calls nhằm phục vụ cho các dịch vụ của website.  

**Nhược điểm**  

  - Tỷ lệ sai sót khi sử dụng XML để truyền dữ liệu khoảng từ 5-7%. Con số này tuy không quá cao, nhưng trên thực tế, người ta vẫn cần cân nhắc trước khi sử dụng nó để trao đổi thông tin.  

# XML tree  

Các tài liệu XML tạo thành một cấu trúc cây bắt đầu từ “root(gốc)” và các nhánh đến “leaf(lá)”.  

![image](https://github.com/elliSzAt/XML-external-entity-XXE-injection/assets/125866921/8c35700e-8879-4fe1-b8a5-80fbdb2631cd)

**Ví dụ về 1 tài liệu XML**

    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <Order>
      <OrderDate>2002-6-14</OrderDate>
      <Customer>Helen Mooney</Customer>
      <Item>
        <ProductID>1</ProductID>
        <Quantity>2</Quantity>
      </Item>
      <Item>
        <ProductID>4</ProductID>
        <Quantity>1</Quantity>
      </Item>
    </Order>
    
**XML Tree Structure**

Tài liệu XML được dựng thành ở dạng “cây phần tử”.  

1 câu XML bắt đầu từ phần tử root và các nhánh từ root đến các phần tử con.  

    <root>
      <child>
        <subchild>.....</subchild>
      </child>
    </root>
    
# Cú pháp của XML

Một XML đầy đủ:

    <?xml version="1.0"?>
    <contact-info>
    <name>Tanmay Patil</name>
    <company>TutorialsPoint</company>
    <phone>(011) 123-4567</phone>
    </contact-info>
    
Trong ví dụ trên có 2 loại thông tin:

  - Thông tin đánh dấu, như <name> và <company>.  
  - Text, hoặc dữ liệu là ký tự, như Tanmey.  
  
Sơ đồ sau minh họa qui tắc cú pháp để viết các kiểu đánh dấu và text khác nhau trong một tài liệu XML.  
    
![image](https://github.com/elliSzAt/XML-external-entity-XXE-injection/assets/125866921/e29c1eaf-4006-4fc0-8d58-339c470464e1)

**Khai báo XML (XML Declaration)**
    
Tài liệu XML có thể tùy ý có một phần khai báo XML. Nó được viết như sau:  

<?xml version="1.0" encoding="UTF-8"?>  

Ở đây, version là phiên bản XML và encoding xác định mã hóa ký tự được sử dụng trong tài liệu.  
    
**Các qui tắc cú pháp để khai báo XML**  
    
Khai báo XML (XML declaration) là phân biệt kiểu chữ và phải bắt đầu với “<?xml>” ở đây “xml” viết ở dạng chữ thường.
