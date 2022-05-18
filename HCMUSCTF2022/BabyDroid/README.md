# Baybydroid

Đây là một bài android viết bằng java, sử đụng jadx-gui để decompile apk về lại source code java

Ở đây ta thấy chương trình có một số activity

![image](https://user-images.githubusercontent.com/31529599/169072828-13d1e18c-674e-4cb5-a8f3-e0646d405cfd.png)

Trong activity main thì chương trình sẽ lấy input và gọi hàm checkFlag ở activity FlagValidator để check flag

![image](https://user-images.githubusercontent.com/31529599/169073052-696f7be5-b3a3-4bc6-b891-a6b6ccdbed47.png)

Hàm check flag

![image](https://user-images.githubusercontent.com/31529599/169073449-fa64f091-c1b4-49cd-897a-a4e866e6c619.png)

Ở hàm check flag, để flag chính xác ta cần pass 2 điều kiện, một của câu lệnh if và còn lại là của hàm math regex

Chuyển câu if về đơn giản hơn:

![image](https://user-images.githubusercontent.com/31529599/169074130-bc9eaaab-a7d4-454c-ad91-4dcdd1dc5890.png)

Ta có được flag cơ bản như sau:

`HCMUS-CTF{THIS_IS_A_really_bASIC_REV}`

tuy nhiên bên trong dấu `{}` thì tất cả các ký tự ở dạng in hoa hoặc thường đều pass điều kiện if

Phân tích regex để lấy được chính xác flag:

![image](https://user-images.githubusercontent.com/31529599/169074599-48a07738-93e6-4efd-a068-2b74ef84294d.png)

Nhìn vào hàm này thì ta biết được đoạn regex sẽ như sau:
`[A-Z_][a-z_][A-Z_][a-z_][A-Z_][a-z_][A-Z_][a-z_][A-Z_][a-z_][A-Z_][a-z_][A-Z_][a-z_][A-Z_][a-z_][A-Z_][a-z_][A-Z_][a-z_][A-Z_][a-z_][A-Z_][a-z_][A-Z_][a-z_]`

Match đoạn này vào đoạn bên trong cặp `{}`

Ta được flag: `HCMUS-CTF{ThIs_iS_A_ReAlLy_bAsIc_rEv}`
