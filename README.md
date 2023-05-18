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

**DTD là gì**
            
-    1 DTD là 1 Document Type Definition.
-    1 DTD định nghĩa cấu trúc và các yếu tố và thuộc tính hợp pháp của 1 tài liệu XML.
            
**Tại sao phải sử dụng DTD**
            
Với 1 DTD, các nhóm người độc lập có thể đồng ý về một DTD tiêu chuẩn để trao đổi dữ liệu. 1 ứng dụng có thể sử dụng 1 DTD để xác thực dữ liệu XML có hợp lệ hay không.
            
**Khai báo 1 Internal DTD**
            
Nếu DTD được khai báo bên trong file XML, nó phải được gói bên trong <!DOCTYPE>
            
**XML document with an internal DTD**
            
```
<?xml version="1.0"?>
<!DOCTYPE note [
  <!ELEMENT note    (to,from,heading,body)>
  <!ELEMENT to      (#PCDATA)>
  <!ELEMENT from    (#PCDATA)>
  <!ELEMENT heading (#PCDATA)>
  <!ELEMENT body    (#PCDATA)>
]>
<note>
<to>Tove</to>
<from>Jani</from>
<heading>Reminder</heading>
<body>Don't forget me this weekend!</body>
</note> 
```
DTD phía trên có thể được hiểu như sau:
            
-    !DOCTYPE xác định rằng phần tử root của tài liệu này là note.
-    !ELEMENT note xác định rằng phần tử note phải chứa bốn phần tử: “to, from, header, body”.
-    !ELEMENT to/from/heading/body xác định phần tử to/from/heading/body thuộc loại “#PCDATA”.
            
**Khai báo 1 External DTD**
            
Nếu DTD được khai báo trong 1 file ngoài, <!DOCTYPE> phải bao gồm reference đến DTD file.
            
Tài liệu XML với reference đến 1 External DTD:
            
```
<?xml version="1.0"?>
<!DOCTYPE note SYSTEM "note.dtd">
<note>
  <to>Tove</to>
  <from>Jani</from>
  <heading>Reminder</heading>
  <body>Don't forget me this weekend!</body>
</note>
```
            
Và đây là file note.dtd
            
```<!ELEMENT note (to,from,heading,body)>
<!ELEMENT to (#PCDATA)>
<!ELEMENT from (#PCDATA)>
<!ELEMENT heading (#PCDATA)>
<!ELEMENT body (#PCDATA)>
```
            
**DTD – XML Building Blocks**
            
Các khối xây dựng của các tài liệu XML và HTML là element(Phần tử)
            
**The Building Blocks of XML Documents**
            
Nhìn từ quan điểm DTD, tất cả các tài liệu XML được tạo thành bởi các building block sau:
            
**Elements(Phần tử)**
            
Ví dụ ở HTML thì phần tử là “body” và “table”, trong XML thì phần tử có thể là “note” hoặc “message”. Các phần tử có thể chứa text, các phàn tử khác, hoặc rỗng. Ví dụ của phần tử HTML rỗng là “hr”, “br” và “img”
Ví dụ:
            
```
<body>some text</body>
 
<message>some text</message>
```
            
**Attributes(Thuộc tính)**
            
Thuộc tính cung cấp thông tin thêm về phần tử
Thược tính luôn được đặt bên trong tag mở đầu của phần tử. Thuộc tính luôn ở dạng cặp name/value. Ví dụ:
            
 ```
<img src=”computer.gif” />
```
            
**Entities(Thực thể)**
            
1 vài kí tự có ý nghĩa đặc biệt trong XML, ví dụ ``<`` định nghĩa sự mở đầu của 1 thẻ XML. Các thực thể sau được định nghĩa trước trong XML.
                                                     
![](https://hackmd.io/_uploads/B1pVT8QBh.png)

Thực thể được sử dụng để định nghĩa shortcut đến 1 số kí tự đặc biệt. Thực thể có thể được khai báo bên trong hoặc bên ngoài.
                                                       
**Khai báo thực thể bên trong:**
                                                       
Cú pháp <!ENTITY entity-name “entity-value”>

Ví dụ: 

```DTD Example:
<!ENTITY writer "Donald Duck.">
<!ENTITY copyright "Copyright W3Schools.">
 
XML example:
<author>&writer;&copyright;</author>
```

Note: 1 thực thể có 3 phần, “&”, name và “;”

**Khai báo thực thể bên ngoài**

Cú pháp: <!ENTiTY entity-name SYSTEM “URL/URI”>

Ví dụ:

```
DTD Example:
 
<!ENTITY writer SYSTEM "https://www.w3schools.com/entities.dtd">
<!ENTITY copyright SYSTEM "https://www.w3schools.com/entities.dtd">
 
XML example:
 
<author>&writer;&copyright;</author>
```

XML cho phép custom entities được định nghĩa bên trong DTD. Ví dụ:

<!DOCTYPE foo [ <!ENTITY myentity "my entity value" > ]>

Định nghĩa này có nghĩa là mọi cách sử dụng tham chiếu thực thể &myentity; trong tài liệu XML sẽ được thay thế bằng giá trị đã xác định: “my entity value”..

# XML external entities là gì?

Các thực thể bên ngoài XML là một loại thực thể tùy chỉnh có định nghĩa nằm bên ngoài DTD nơi chúng được khai báo.
Khai báo của một thực thể bên ngoài sử dụng từ khóa SYSTEM và phải chỉ định một URL mà từ đó giá trị của thực thể sẽ được tải. Ví dụ:
<!DOCTYPE foo [ <!ENTITY ext SYSTEM "http://normal-website.com" > ]>

URL có thể sử dụng giao thức file:// và do đó các thực thể bên ngoài có thể được tải từ tệp. Ví dụ:
<!DOCTYPE foo [ <!ENTITY ext SYSTEM "file:///path/to/file" > ]>

Các thực thể bên ngoài XML cung cấp phương tiện chính để phát sinh các cuộc tấn công XXE

# Làm thế nào các lổ hổng XXE có thể phát sinh?

1 số ứng dụng sử dụng XML format để truyền data giữa trình duyệt và server. Các ứng dụng thực hiện điều này hầu như luôn sử dụng thư viện chuẩn hoặc API nền tảng để xử lý dữ liệu XML trên server. Lổ hổng XXR phát sinh bởi vì XML XML chứa nhiều tính năng nguy hiểm tiềm ẩn khác nhau và trình phân tích cú pháp tiêu chuẩn hỗ trợ các tính năng này ngay cả khi chúng không được ứng dụng sử dụng bình thường.

XXE là một loại thực thể XML tùy chỉnh có các giá trị đã xác định được tải từ bên ngoài DTD mà chúng được khai báo. Các thực thể bên ngoài đặc biệt thú vị từ góc độ bảo mật vì chúng cho phép một thực thể được xác định dựa trên nội dung của đường dẫn tệp hoặc URL.

# Các loại hình tấn công XXE

Có nhiều loại hình tấn công XXE:

-    Khai thác XXE để đọc file: Khi 1 external entity được định nghĩa bao gồm nội dung của 1 file và trả về trong response.
-    Khai thác XXE để thực hiện SSRF: khi 1 external entity được định nghĩa dựa trên 1 URL đến hệ thống backend.
-    Khai thác blind XXE đọc data out-of-band, khi dữ liệu nhạy crm được truyền từ server đến hệ thống mà hacker điều khiển.
-    Blind XXE đọc data thông qua thông báo error, hacker có thể trigger 1 thông báo error có chứa dữ liệu nhạy cảm.

# Khai thác XXE để đọc file

Để thực hiện một cuộc tấn công chèn XXE truy xuất một tệp tùy ý từ hệ thống tệp của máy chủ, bạn cần sửa đổi XML đã gửi theo hai cách:

-    Giới thiệu (hoặc chỉnh sửa) phần tử DOCTYPE xác định một thực thể bên ngoài chứa đường dẫn đến tệp.
-    Chỉnh sửa giá trị dữ liệu trong XML được trả về trong phản hồi của ứng dụng, để sử dụng thực thể bên ngoài đã xác định.

Ví dụ: 1 ứng dụng shopping check giá của sản phẩm bằng cách submit 1 XML đến server.

```
<?xml version="1.0" encoding="UTF-8"?>
<stockCheck><productId>381</productId></stockCheck>
```

Ứng dụng này không thực hiện bất kì biện pháp phòng chống XXE nào, vì vậy ta có thể exploit XXE để đọc file /etc/passwd bằng cách dùng payload:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<stockCheck><productId>&xxe;</productId></stockCheck>
```

Payload XXE này sẽ định nghĩa 1 thực thể &xxe; có giá trị là nội dung của file /etc/passwd và sử dung thực thể trong giá trị của productId. Response:

```
Invalid product ID: root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
```

# Exploiting XXE to perform SSRF attacks

Ngoài việc truy xuất dữ liệu nhạy cảm, tác động chính khác của các cuộc tấn công XXE là chúng có thể được sử dụng để thực hiện giả mạo yêu cầu phía máy chủ (SSRF). Đây là một lỗ hổng nghiêm trọng tiềm ẩn trong đó ứng dụng phía máy chủ có thể được tạo ra để thực hiện các yêu cầu HTTP tới bất kỳ URL nào mà máy chủ có thể truy cập.

Để khai thác lỗ hổng XXE để thực hiện tấn công SSRF, bạn cần xác định một thực thể XML bên ngoài bằng cách sử dụng URL mà bạn muốn nhắm mục tiêu và sử dụng thực thể đã xác định trong một giá trị dữ liệu. Nếu bạn có thể sử dụng thực thể đã xác định trong một giá trị dữ liệu được trả về trong phản hồi của ứng dụng, thì bạn sẽ có thể xem phản hồi từ URL trong phản hồi của ứng dụng và do đó có được tương tác hai chiều với hệ thống back-end. Nếu không, bạn sẽ chỉ có thể thực hiện các cuộc tấn công SSRF mù (vẫn có thể gây ra hậu quả nghiêm trọng).

Ví dụ:
```
 <!DOCTYPE foo [ <!ENTITY xxe SYSTEM “http://internal.vulnerable-website.com/”&gt; ]>
```

