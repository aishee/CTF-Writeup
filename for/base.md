Xem mấy bài forensic thì mình thấy bài "for" là bài hay nhất. Nên vậy mình sẽ chỉ writeup bài này.
Dữ kiện được cho

```
Someone broadcasts something. Dare you find it? boobooboo.pcapng 
https://en.wikipedia.org/wiki/Eddystone_Lighthouse
```

Một file pcap, và một wiki về Eddystone, google thì ta biết đây là một giao thức, và thêm một dữ 
kiện được lấy từ repository của google về nó: https://github.com/google/eddystone

Ta thử dùng wireshark load file pcap được cho lên xem thử.

![alt](https://s2.anh.im/2016/11/07/rsz_screenshot_-_07112016_-_0933322176e.png)

Thông tin đã được load nên nhưng có vẻ nó nhìn không được đẹp lắm thì phải, hãy thử duyệt qua vài
dòng xem thử nào:

Lướt vài dòng và mình đã thấy điều đáng chú ý: **User encapsulation not handled: DLT=147, check your Preferences->Protocols->DLT_USER**
Thử search với Bing hoặc Google ta tìm được đây là traffic kiểu **btle**, chúng ta nên tùy chỉnh lại cho Wireshark hiển thị đúng định dạng để có thể
dễ dàng phân tích.

![Imgur](http://i.imgur.com/vpappAo.png)

Lướt vài dòng và ta thấy 1 vài packet có chứa "SVATT"

Kết hợp với hint đề bên trên về giao thức Eddystone => định dạng Eddystone-URL (broadcast 1 địa chỉ URL) => FLAG gần như chắc chắn năm ở đây mà thôi.
Tham khảo wiki của google về giao thức trên ta => byte đầu tiên của gói data broadcast sẽ được bắt đầu với 0x10
Lần lượt decode (0x10-0xbb-0x02-...) ra ta được kết quả là "http://9t.com/AzGiHoOJ"
Một link rút gọn, thử curl đến link này xem nào: 

![alt](https://s2.anh.im/2016/11/07/rsz_screenshot_-_07112016_-_100607fc4bc.png)

=> Link cần tìm đã lộ mặt: https://2016.svattt.org/7541e0246119c829b7dc1987b2390dd0

Acess và view-source lên => FLAG = SVATTT{n0_id3a_wh4t_i5_b33rcon}
