```diff
@@ Web - Client Challenge @@
```



## XSS - Stored 2 [50 Pointes]

- Challenge cung cấp một trang web như sau

  ![1](F:\VCS_practice\rootme\img\1.jpg)

- Có 2 ô input, mọi nội dung ta nhập vào khi post lên server sẽ được *admin* kiểm tra

  ![1-1](F:\VCS_practice\rootme\img\1-1.jpg)

- Tới đây, ta thử nhập các payload  reflect xss để kiểm tra trang web. Kết quả là script của ta đã không được thực thi do server đã mã hóa html các ký tự đặc biệt

  ![1-2](F:\VCS_practice\rootme\img\1-2.jpg)

- Inject payload xss như trên không được, ta thử đi tìm vị trí khác. 

- Kiểm tra thêm, ta thấy ta cũng đang kiểm soát được giá trị của cookie ***status***. Thử thay đổi giá trị của nó, ta thấy trong các lần post tiếp theo, trạng thái ban đầu được thay đổi từ ***invite*** sang giá trị mà ta inject

  ![1-3](F:\VCS_practice\rootme\img\1-3.jpg)

- Vậy là xác nhận được ta có thể thay đổi giá trị này, thử XSS với nó, quan sát kết quả ta thấy

  ![1-6](F:\VCS_practice\rootme\img\1-6.jpg)

- Đọc source code ta thấy cần bybass chỗ này với ký tự ```">``` ta có thể thực thi script ở đây![1-7](F:\VCS_practice\rootme\img\1-7.jpg)

- Sửa lại payload cho chỗ này như sau để có thể lấy được cookie 

  ![1-8](F:\VCS_practice\rootme\img\1-8.jpg)

  ![1-9](F:\VCS_practice\rootme\img\1-9.jpg)

- Kết quả

  ![1-5](F:\VCS_practice\rootme\img\1-5.jpg)

- Thay thế cookie của admin vào, sau đó vào trang của admin (có đường dẫn phía trên), ta thu được flag ```E5HKEGyCXQVsYaehaqeJs0AfV``` 

  ![1-4](F:\VCS_practice\rootme\img\1-4.jpg)