## Phần 2 - Đọc/Ghi file & Kết nối cơ sở dữ liệu MySQL

## Đọc và ghi file

Để quản lý các file trong Node, chúng ta sử dụng module fs (một module lõi). Chúng ta đọc và ghi file sử dụng các phương thức fs.readFile() và fs.writeFile() tương ứng. Follow đoạn code ở dưới đây:

```javascript
// Gọi module http từ node_modules
var http = require("http");
// Gọi mysql module
var fs = require("fs");

// Tạo server.
http.createServer(function (request, response) {
    request.on("end", function () {
        // Tiến hành đọc file
        fs.readFile("test.txt", 'utf-8', function (error, data) {
            // Write headers. (đã giải thích ở part 1)
            response.writeHead(200, {
            'Content-Type': 'text/plain'
            });
            // Tăng số đếm
            data = parseInt(data) + 1;
            // Ghi vào file
            fs.writeFile('test.txt', data);
            // Kết thúc và trả về response messenge
            response.end('This page was refreshed ' + data + ' times!');
        });
    });
}).listen(8080);
```
``Lưu ý: Node.js có một trình quản lý gói được gọi là Node Package Manager (NPM). Nó được cài đặt tự động cùng với Node.js``

Lưu đoạn code trên lại trong file tên là files.js. Trước khi chạy đoạn script này, hãy tạo ra một file có tên là test.txt trong cùng thư mục chứa file files.js.

Đoạn code trên minh họa cách sử dụng các phương thức fs.readFile() và fs.writeFile(). Mỗi lần server nhận một request, đoạn script đó đọc một con số từ file, tăng giá trị số lên một đơn vị, và ghi con số mới xuống file. Phương thức fs.readFile() chấp nhận 3 đối số: tên của file cần đọc, kiểu mã hóa mong muốn, và một callback function.

Việc ghi tới một file, ít nhất là trong trường hợp này, còn đơn giản hơn nhiều. Chúng ta không cần đợi bất kỳ kết quả nào, mặc dù nên thêm phần kiểm tra lỗi trong ứng dụng thực tế. Phương thức fs.writeFile() chấp nhận tên file và data như là các đối số. Nó cũng chấp nhận các đối số thứ 3 và 4 (cả hai đều là tùy chọn) để xác định kiểu mã hóa và hàm callback, tương ứng.

Bây giờ hãy chạy đoạn script trên bằng dòng lệnh sau:

```
node files.js
```

Mở nó trong trình duyệt tại địa chỉ http://localhost:8080 (8080: Là port đã listen ở cuối đoạn code bên trên nhé) và nhấn refresh một vài lần. Lúc này có thể nghĩ rằng có một lỗi trong đoạn code đó bởi vì nó dường như con số đếm tăng một lần 2 đơn vị. Đây không phải là một lỗi đâu nhé, bởi vì bản chất mỗi lần request URL này, thì có tới 2 request được gửi tới server. Request đầu tiên thì được tạo tự động bởi trình duyệt, nó request favicon.ico, và dĩ nhiên, cái request thứ hai là cho URL http://localhost:8080 => nó tăng 2 số cho 1 lần request.

Bây giờ chỉ cần thêm điều kiện vào để server hiểu mình chỉ cần đếm số lần request URL thôi. Đây là đoạn code được sửa lại:

```javascript
// Gọi module http từ node_modules
var http = require("http");
// Gọi mysql module
var fs = require("fs");

// Tạo server.
http.createServer(function (request, response) {
    // Attach listener on end event.
    request.on('end', function () {
        // Check request của user chứ ko phải request tự động của browser
        if (request.url == '/') {
            // Tiến hành đọc file
            fs.readFile('test.txt', 'utf-8', function (error, data) {
                // Write headers. (đã giải thích ở part 1)
                response.writeHead(200, {
                    'Content-Type': 'text/plain'
                });
                // Tăng số đếm
                data = parseInt(data) + 1;
                // Ghi vào file
                fs.writeFile('test.txt', data);
                // Kết thúc và trả về response messenge
                response.end('This page was refreshed ' + data + ' times!');
            });
        } else {
            // Trả về 404 => không tìm thấy file yêu cầu
            response.writeHead(404);
            // Không trả messenge mà chỉ kết thúc thôi
            response.end();
        }
    });
}).listen(8080); 
```

Bây giờ lại chạy câu lệnh này và quan sát kết quả:

```
node files.js
```
## Truy cập cơ sở dữ liệu MySQL

Hầu hết các công nghệ server-side truyền thống có một phương tiện built-in để kết nối và truy vấn cơ sở dữ liệu. Với Node.js, phải cài đặt một thư viện để thực hiện công việc đó. Hiện tại trong bài này sẽ sử dụng một thư viện ổn định và khá dễ dùng là **node_mysql**. Tên đầy đủ của module này là mysql@2.0.0-alpha2 (mọi thứ phía sau chữ @ là con số phiên bản). Mở console lên, điều hướng tới thư mục nơi lưu trữ những script của mình, và chạy dòng lệnh sau:

```
npm install mysql@2.0.0-alpha2
```

Lệnh trên sẽ tải về và cài đặt module mysql, và nó cũng tạo ra folder tên là node_modules trong thư mục hiện tại. Bây giờ hãy xem làm cách nào có thể sử dụng nó trong code của mình, xem đoạn code ví dụ sau:

```javascript
var http = require('http');
var mysql = require("mysql"); 
     
// Khởi tạo kết nối, thiết lập các thông số cho DB
var connection = mysql.createConnection({ 
    user: "root", 
    password: "", 
    database: "db_name"
}); 

// Tạo server
http.createServer(function (request, response) {
    request.on('end', function () { 
        // Thực hiện câu lệnh truy vấn
        connection.query('SELECT * FROM your_table;', function (error, rows, fields) { 
            response.writeHead(200, { 
                'Content-Type': 'x-application/json' 
            }); 
            // Trả response bằng JSON
            // Biến rows chính là kết quả của câu truy vấn trên (có thể hiểu 1 row là 1 record trên DB) 
            response.end(JSON.stringify(rows)); 
        }); 
    }); 
}).listen(8080);
```
Việc truy vấn cơ sở dữ liệu bằng thư viện này rất dễ, chỉ đơn giản nhập vào chuỗi truy vấn và một hàm callback. Trong một ứng dụng thực tế, nên kiểm tra xem liệu có lỗi xảy ra hay không (tham số **error** sẽ không là _undefined_ nếu có lỗi xuất hiện) và gửi mã response phụ thuộc vào thành công hay thất bại của truy vấn đó. Ngoài ra cũng chú ý việc thiết lập **Content-Type** thành **x-application/json**, đây là kiểu MIME hợp lệ cho JSON. Tham số **rows** chứa kết quả của truy vấn đó, và chỉ việc convert dữ liệu trong **rows** thành một cấu trúc JSON bằng cách sử dụng phương thức **JSON.stringify()**.

Lưu file này thành tên mysql.js, và chạy nó (nếu đã cài đặt MySQL) bằng dòng lệnh sau:

```
node mysql.js
```

Điều hướng tới địa chỉ http://localhost:8080 trên trình duyệt, và sẽ thấy một cửa sổ hiện lện nhắc tải về file có định dạng JSON.

##Kết luận

**_Mỗi function trong Node.js là bất đồng bộ (asynchronous)._**


Node.js yêu cầu thêm nhiều công việc phụ trợ, nhưng kết quả đạt được là một ứng dụng tốc độ nhanh và mạnh mẽ thì cũng đáng đồng tiền bát gạo :D. Nếu không muốn làm mọi thứ ở những mức thấp nhất, thì luôn có thể chọn lấy một vài framework, như là Express, để khiến công việc phát triển ứng dụng trở nên đơn giản hơn.

Node.js là một công nghệ đầy hứa hẹn và là một lựa chọn hoàn hảo cho các ứng dụng có mức tải cao (high load). Nó đã được chứng minh bởi các tổ chức lớn như Microsoft, eBay, và Yahoo. Nếu chưa chắc chắn về việc hosting ứng dụng hoặc website của mình, luôn có thể sử dụng một VPS giá rẻ hoặc rất nhiều dịch vụ cloud-based như Microsoft Azure và Amazon EC2. Cả hai dịch vụ này đều cung cấp những môi trường có khả năng mở rộng với một mức giá chấp nhận được.

**KẾT THÚC 2 PHẦN VỀ NHỮNG THAO TÁC CƠ BẢN ĐỂ NHẬP MÔN NODE JS.**
