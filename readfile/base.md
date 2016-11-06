Mở đầu bài Web là một câu 150 điểm với tựa đề READFILE với vài dữ kiện:
URL: http://readfile.svattt.org:8888/web100.php
Source: http://readfile.svattt.org:8888/web100.php.bak

Hừm, ta thử truy cập vào URL xem có gì nào:

```
Dare to read flag.php???
Click me
Something's missing!!
```

Một câu thách thức bạn dạm đọc file flag.php =))) tại sao không chứ, click vào link thử xem nào

```

Dare to read flag.php???
Click me
```
Một cái gì đó là lạ, thử view-source xem thử có gì nào:

```bash
view-source:readfile.svattt.org:8888/web100.php?filename=test.php&timestamp=13371337&sig=d7a52c3ed325ef19
```

![alt text](https://s2.anh.im/2016/11/07/ezgif.com-crop15e024.png)

=> FLAG sẽ thay thế "nothing" khi tham số request trên URL thỏa mã điều kiện.


Ta sẽ phân tích tiếp đoạn source được cung cấp, 

```php
    function checksig($filename, $timestamp, $sig, $secretkey)
    {
        $realsig = substr(md5($filename.$timestamp.$secretkey),0,16);
        if ($sig == $realsig)
        {
            $filename = './'.str_replace('/','',$filename);
            readfile($filename);
            die(0);
        }
        echo "Invalid Signature!";
    }

```
Chúng ta sẽ chỉ quan tâm 2 dữ kiện: 

```php
$realsig = substr(md5($filename.$timestamp.$secretkey),0,16);
```

và 

```php
if ($sig == $realsig)
```
$sig là chữ ký mà chúng ta gửi lên server, còn $realsig là một con số được tính theo công thức bên trên. Server sẽ so sánh 2 giá trị $sig và $realsig để quyết định sẽ cung cấp cho chúng ta 1 giá trị nào đó hay không.
Ở trên chúng ta để ý một điều là toán tử "==" được sử dụng để so sánh,  chúng ta sẽ thử khai thác nó. Khi sử dụng toán tử này (==), sự so sánh sẽ được thực hiện dựa trên các k hoạt động của các toán hạng. Nếu các toán hạng là chuỗi hoặc null, chúng được so sánh nnhư kiểu strings, nếu một trong hai là kiê Boolean, nó được chuyển đổi thành các giá trị Boolean và so sánh, và nếu không nó sẽ được chuyển đổi sang số và so sánh.
 Tham Khảo http://php.net/manual/en/language.operators.comparison.php

Lấy một ví dụ với "0e11223344" == "0",sẽ trả về True. Trường hợp này, php sẽ biên dich là những con số không phải là chuỗi, phía bên trái, "0e11223344", php coi nó như là một ký hiệu khoa học, và sẽ hiểu là "0*10^11223344=0"
Vế bên phải cũng sẽ đươc hiểu là số 0, php sẽ so sánh 2 số và kết quả trả về là True. Kể cả ta có đảo 2 vế thì kết quả nhận được vẫn là True.
=> $sig = $realsig = 0
=> ta sẽ có các tham số sau: filename = flag.php
$sig = 0
Oh, vậy như thế là gần đủ để hoàn thành, còn thiếu 1 tham số timestamp, nhận thấy không còn dữ kiện nào giá trị =))) => cách tối ưu mà tôi chọn là fuzzy, và kết quả nhận được là $timestamp=862

=> http://readfile.svattt.org:8888/web100.php?filename=flag.php&timestamp=862&sig=0

![alt text](https://s1.anh.im/2016/11/07/ezgif.com-crop2ba186.png)

=> Access và view-source lên => FLAG = SVATTT{N0_m0r3_h4sh_3xtens10n_4tt4ck}
