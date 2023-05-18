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
    
  - Khai báo XML (XML declaration) là phân biệt kiểu chữ và phải bắt đầu với “<?xml>” ở đây “xml” viết ở dạng chữ thường.
  - Nếu tài liệu chứa khai báo XML, thì nó phải là lệnh đầu tiên của tài liệu XML.  
  - Khai báo XML phải là lệnh đầu tiên của tài liệu XML.  
  - Một HTTP Protocol có thể ghi đè giá trị của encoding mà bạn đặt trong khai báo XML.  

**Tags và Elements**

Một XML file được cấu thành bởi một số phần tử XML (Element), còn được gọi là XML-node hoặc XML-tags. Tên các phần tử XML được bao trong các dấu < > như sau:

<element>

**Qui tắc cú pháp (Syntax Rules) cho các thẻ và phần tử**

  - Cú pháp phần tử: Mỗi phần tử XML cần được bao trong hoặc với phần tử bắt đầu hoặc kết thúc như sau:

        <element>….</element>
        Hoặc đơn giản theo cách:
        <element/>

  - Lồng các phần tử: Một phần tử XML có thể chứa nhiều phần tử XML khác như là con của nó, nhưng các phần tử con này phải không đè lên nhau, ví dụ: Một thẻ đóng của một phần tử phải có cùng tên như thẻ mở kết nối với nó.  

        Ví dụ sau minh họa các thẻ lồng nhau sai cú pháp:

        <?xml version="1.0"?>
        <contact-info>
        <company>TopDev
        <contact-info>
        </company>

        Ví dụ sau minh họa các thẻ lồng nhau đúng cú pháp:

        <?xml version="1.0"?>
        <contact-info>
        <company>TopDev</company>
        <contact-info>

  - Phần tử gốc (Root Element): Một tài liệu XML có thể chỉ có một phần tử gốc. Ví dụ sau minh họa một tài liệu XML sai cú pháp, bởi vì cả hai phần tử x và y xuất hiện ở cấp cao nhất mà không phải là một phần tử gốc.

        <x>...</x>
        <y>...</y>
          
        Còn đây là ví dụ về đúng cú pháp:

        <root>
           <x>...</x>
          <y>...</y>
        </root>
          
  - Phân biệt kiểu chữ: Tên của các phần tử XML là phân biệt kiểu chữ. Nghĩa là tên của thẻ mở và thẻ đóng phải cùng kiểu.
          
          Ví dụ, <contact-info> là khác với <Contact-Info>.
          
**Thuộc tính (Attributes)**
          
Một thuộc tính xác định thuộc tính cho phần tử, sử dụng một cặp tên/giá trị. Một phần tử XML có thể có một hoặc nhiều thuộc tính. Ví dụ:
          
    <a href="https://topdev.vn/">TopDev</a>
          
Ở đây href là tên thuộc tính và https://topdev.vn/ là giá trị thuộc tính.  
          
**Qui tắc cú pháp cho thuộc tính trong XML**
          
  - Tên thuộc tính trong XML là phân biệt kiểu chữ (không giống như HTML). Tức là, HREF và href là hai thuộc tính khác nhau trong XML.  
  - Cùng một thuộc tính không thể có hai giá trị trong một cú pháp. Ví dụ sau là sai cú pháp bởi vì thuộc tính b được xác định hai lần:  
          
      <a b="x" c="y" b="z">....</a>
          
  - Tên thuộc tính được định nghĩa không có sự trích dẫn, trong khi giá trị thuộc tính phải luôn luôn trong các dấu trích dẫn. Ví dụ sau là sai cú pháp:  
         
      <a b=x>....</a>
          
  - Trong ví dụ này, giá trị thuộc tính không được định nghĩa trong các dấu trích dẫn.
          
**Text trong XML**
          
  - Tên của phần tử XML và thuộc tính XML là phân biệt kiểu chữ, nghĩa là tên của phần tử mở và phần tử đóng phải ở được viết cùng kiểu.
  - Để tránh các vấn đề về mã hóa ký tự, tất cả XML file nên được lưu ở dạng Unicode UTF-8 hoặc UTF-16.  
  - Các ký tự whitespace như khoảng trắng, tab và ngắt dòng giữa các phần tử XML và giữa các thuộc tính XML sẽ bị bỏ qua.  
  - Một số ký tự được dành riêng trong cú pháp XML. Vì thế, chúng không thể được sử dụng một cách trực tiếp. Để sử dụng chúng, một số thực thể thay thế được sử dụng, các thực thể này được liệt kê trong bảng dưới:
          
![image](https://github.com/elliSzAt/XML-external-entity-XXE-injection/assets/125866921/b0475c13-fbda-475c-b60b-83bd4f82ec09)

# XML DTD
