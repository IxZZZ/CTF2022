# hide

Bài này là một file thực thi ELF linux và đã được pack bởi một loại custom packer nào đó, mục đích của chúng ta là phải unpack chương trình và tìm được flag

Cách unpack cơ bản nhất là debug =))))

Nhìn vào start của chương trình thì khá phức tạp và có các hàm khá tương tự với upx unpack nên mình không để ở đây, tuy nhiên khi inspect String Windows thì ta có một chuỗi khá thú vị -> `Enter the password:`

![image](https://user-images.githubusercontent.com/31529599/169077259-4647352c-3a18-4c6a-9c25-9cbf9d9d62a1.png)

đặt break point để trace read write của chuỗi này để debug, ngay khi gặp trace breakpoint đầu tiên thi to có thể sử dụng chức năng reanalyze của IDA để chương trình tự phân tích lại, và xem các hàm mới -> hàm mà chương trình đọc vào input và check flag là ở địa 0x401D99

```
void sub_401D99()
{
  __int64 encrypted[32]; // [rsp+10h] [rbp-240h] BYREF
  _BYTE int_64[16]; // [rsp+110h] [rbp-140h] BYREF
  __int64 key[4]; // [rsp+120h] [rbp-130h] BYREF
  __int64 v3[33]; // [rsp+140h] [rbp-110h] BYREF
  unsigned __int64 v4; // [rsp+248h] [rbp-8h]

  v4 = __readfsqword(0x28u);
  v3[0] = 0LL;
  v3[1] = 0LL;
  v3[2] = 0LL;
  v3[3] = 0LL;
  v3[4] = 0LL;
  v3[5] = 0LL;
  v3[6] = 0LL;
  v3[7] = 0LL;
  v3[8] = 0LL;
  v3[9] = 0LL;
  v3[10] = 0LL;
  v3[11] = 0LL;
  v3[12] = 0LL;
  v3[13] = 0LL;
  v3[14] = 0LL;
  v3[15] = 0LL;
  v3[16] = 0LL;
  v3[17] = 0LL;
  v3[18] = 0LL;
  v3[19] = 0LL;
  v3[20] = 0LL;
  v3[21] = 0LL;
  v3[22] = 0LL;
  v3[23] = 0LL;
  v3[24] = 0LL;
  v3[25] = 0LL;
  v3[26] = 0LL;
  v3[27] = 0LL;
  v3[28] = 0LL;
  v3[29] = 0LL;
  v3[30] = 0LL;
  v3[31] = 0LL;
  key[0] = 0xEE790C0100EA3E61LL;
  key[1] = 0x81777D85F0AE73FBLL;
  key[2] = 0xD708613B0700001FLL;
  key[3] = 0xF4DF1409A310982DLL;
  encrypted[30] = 0x3A9ACAE2EC75FD06LL;
  encrypted[31] = 0x6E30DB404445C837LL;
  print((__int64)aEnterThePasswo_0);
  scanf(v3, 17LL, off_4C66D8);
  *((_BYTE *)v3 + sub_401180() - 1) = 0;
  maybe_expandkey((__int64)key, (__int64)encrypted, 256);
  encryption((char *)v3, int_64, (__int64)encrypted, 256);
  if ( (unsigned int)compare() )
    sub_41D260(aFail);
  else
    sub_41D260(aCongrat);
  if ( __readfsqword(0x28u) != v4 )
    sub_459480();
}
```

Nhìn vào chương trình ta thấy có một số hằng số lạ, sử dụng find crypto constant plugin của ida để xem xét chương trình có sử dụng các loại encryption phổ biến hay ko 

![image](https://user-images.githubusercontent.com/31529599/169088843-c0f4d5fe-6d69-4f0b-abdb-120f02a22153.png)

Và kết quả đúng như ta mong đợi, chương trình có lẽ đang sử dụng AES encryption 

Ở trên mình đã phân tích và đổi tên để dễ hiểu hơn cơ bản chương trình nhập vào flag sau đó sử dụng AES 256 với key hardcode để encrypt sau đó so sánh với chuỗi constant được lưu trong chương trình

Vậy ở đây ta chỉ cần decrypt chuỗi constant được so sánh và nhận được flag:

encrypted: [0x6,0xfd,0x74,0xec,0xe2,0xca,0x9a,0x3a,0x37,0xc8,0x45,0x44,0x40,0xdb]

key: 613eea00010c79eefb73aef0857d77811f0000073b6108d72d9810a30914dff4

vì ở đây chương trình không có sài iv cho nên mode sẽ là ECB và đặt biệt các hằng số truyền vào các hàm là 256 nên encrytion cuối cùng sẽ là AES-ECB-256

![image](https://user-images.githubusercontent.com/31529599/169091706-de77d544-4e7e-4f0f-a841-4d31f7ed8b53.png)

Sử dụng tool online để decrypt và ta được flag: HCMUS-CTF{BestPacker_Ever}



