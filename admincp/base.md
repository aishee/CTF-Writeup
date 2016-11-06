Tiếp tục 1 bài WEB nữa, với số điểm là 200 :D có vẻ khó hơn đây

```
http://128.199.128.238:31333

Hint 1: server.py
can you figure out wut is going on then hack it ?
```

Ta thử access vào link được cung cấp xem thử nào

```bash
Traceback (most recent call last):
  File "/home/admincp/server.py", line 30, in <module>
    cipher = buffer.split('GET /login/')[1]
IndexError: list index out of range
```

Oh, một lỗi quen thuộc nếu ai đã từng làm việc với python, :)) tôi đoán là họ đã sử dụng Framework Flask. 
Ta thử access với link: http://128.199.128.238/login/helloyou

Một exception nữa:

```bash
Your credential: helloyou Traceback (most recent call last): File "/home/admincp/server.py", line 42, in login = json.loads(AES.new(KEY, AES.MODE_OFB, IV).decrypt(cipher.decode('hex'))) 
File "/usr/lib/python2.7/encodings/hex_codec.py", line 42, in hex_decode output = binascii.a2b_hex(input) TypeError: Non-hexadecimal digit found
```

Để ý kỹ: login = json.loads(AES.new(KEY, AES.MODE_OFB, IV).decrypt(cipher.decode('hex'))) , Oimeoi, phát hiện ra rằng đây là một bài crypto thì đúng hơn là web chứ, lâu lâu không đọc về crypto, 
ngẫm nghĩ mãi không ra, lướt lại mấy trang wiki về crypto, chợt lóe lên một tia hy vọng >>
It involves feeding the successive output blocks from the underlying block cipher back to it. These feedback blocks provide string of bits to feed the encryption algorithm which act as the key-stream generator as in case of CFB mode.

The key stream generated is XOR-ed with the plaintext blocks. The OFB mode requires an IV as the initial random n-bit input block. The IV need not be secret.
Link tham khao: https://www.tutorialspoint.com/cryptography/block_cipher_modes_of_operation.htm

=))) ra rồi, ra rồi.
=> để có thể bypass thì ta phải tìm một input hợp lệ dạng cipher sao cho plaintext = decryption(ciphertext) để có thể parse vào hàm json.loads()

Hiện tại với cách mà tôi có thể nghĩ ra nhanh nhất là phải brute force, để chúng ta có thể tìm input của nó.
Sử dụng 1 script mà tôi đã viết cách đây khá lâu để exploit. exploit.py
Sau 1 hồi brute ta đã tìm được input = "admin"
Output = e3c1f6d242e66611f709e97304acb7f4

Sử dụng module requests của python ta có kết quả sau:

![text](https://s2.anh.im/2016/11/07/ezgif.com-crop3f32a2.png)

=> FLAG = SVATTT{sorry_this_aint_totally_cryptography_using_crypto_w1sely_btw}

=>>> Một bài crypto đáng ghét -----_------
