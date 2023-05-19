Description: This lab has a “Check stock” feature that parses XML input but does not display the result. You can detect the blind XXE vulnerability by triggering out-of-band interactions with an external domain. To solve the lab, use an external entity to make the XML parser issue a DNS lookup and HTTP request to Burp Collaborator

Lổ hổng vẫn nằm ở tính năng Check stock. Nhưng đầu vào XML không được trả về ở response. Ta phải sử dụng blind XXE out-of-band. Nhưng challenge này chi cho phép sử dụng Burp Collaborator nên mình mất kha khá thời gian để cài cái Burp Pro Crack.

Về lab này thì để solve được thì ta chỉ cần khiến server tạo request đến server burp collaborator của ta là được nên request sẽ là như sau:

![image](https://github.com/elliSzAt/XML-external-entity-XXE-injection/assets/125866921/0793e338-0f14-47bc-a0b3-fcb557ef92e5)

Sau đó vào phần ``Collab`` nhấn vào nút ``Poll now`` là ta đã hoàn thành lab này.
