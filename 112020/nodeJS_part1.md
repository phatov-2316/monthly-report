## Giới thiệu

Node.js chỉ là một môi trường. Không có một server HTTP mặc định, hoặc bất kỳ server nào cho vấn đề đó. Đó là lợi thế để  có thể xây dựng được ứng dụng web có tốc độ thực thi cao. Một script quản lý tất cả truyền thông với các client. Điều này giúp giảm đáng kể số lượng tài nguyên sử dụng bởi ứng dụng đó. Ví dụ, đây là đoạn code cho một ứng dụng đơn giản bằng Node.js:

```
var i, a, b, c, max;

max = 1000000000;

var d = Date.now();

for (i = 0; i < max; i++) {
    a = 1234 + 5678 + i;
    b = 1234 * 5678 + i;
    c = 1234 / 2 + i;
}

console.log(Date.now() - d);
``` 
**Module**

Node.js sử dụng một kiến trúc module để đơn giản hóa việc tạo ra các ứng dụng phức tạp. Các module hơi giống các thư viện trong ngôn ngữ C. Mỗi module chứa một tập các chức năng liên quan đến "chủ đề" của module đó. Ví dụ, module http chứa các function xác định liên quan đến HTTP. Node.js cung cấp một số module lõi để truy cập các file trên hệ thống file, tạo ra các server HTTP và TCP/UDP, và thực thi những chức năng hữu ích khác...

Để gọi sử dụng một module, chỉ cần gọi function require():

`var http = require('http');`

Function require() đó sẽ trả về tham chiếu đến module xác định. Trong trường hợp của đoạn code trên là một tham chiếu đến module http được lưu trữ trong biến http.

Trong đoạn code ở trên, truyền vào tên của một module tới function require() (tên module chính là "http"́). Node sẽ tiến hành tìm kiếm folder có tên là node_modules nằm trong thư mục chứa source (thư mục này từ đâu có thì sẽ ở part sau sẽ đi sâu hơn :D), và tìm kiếm module "http" nằm trong folder đó. Nếu Node không tìm thấy folder node_modules (hoặc module http trong đó), thì nó sẽ tìm kiếm thông qua module cache toàn cục. Ngoài ra cũng có thể require trực tiếp file module bằng cách truyền một đường dẫn tương đối hoặc tuyệt đối, kiểu như thế này:

`var myModule = require('./myModule.js');`

Các module được đóng gói thành những phần code riêng biệt. Code trong một module hầu hết ở trạng thái private - nghĩa là các function và biến được định nghĩa trong chúng chỉ có thể được truy cập từ bên trong module đó mà thôi. Tuy nhiên vẫn có thể phơi bày (expose) ra các function hoặc biến được sử dụng bên ngoài của module đó. Để làm được điều này, dùng đối tượng "exports" và gọi các phương thức hay thuộc tính của nó với đoạn code mà bạn muốn cung cấp ra ngoài. 

Ví dụ:

```
var PI = Math.PI;

exports.area = function (r) {
  return PI * r * r;
};

exports.circumference = function (r) {
  return 2 * PI * r;
};
```

Đoạn code trên tạo ra một biến PI mà chỉ có thể được truy cập bởi code trong module đó; nó không thể bị truy cập từ phía bên ngoài. Tiếp theo, hai function được tạo ra trên đối tượng exports. Những function này có thể truy cập từ bên ngoài của module đó bởi vì chúng được định nghĩa trên đối tượng exports. Kết quả là, PI hoàn toàn được bảo về khỏi sự can thiệp từ bên ngoài. 

**Global Scope (Phạm vi toàn cục)**

Node là một môi trường JavaScript chạy trên engine Google's V8 JavaScript. Vì vậy, nên tuân theo những chuẩn tốt nhất khi sử dụng để phát triển trên client-side. Nên tránh việc đặt bất cứ thứ gì vào trong một phạm vi toàn cục (global scope) mà phải tùy trường hợp sử dụng. Biến toàn cục trong Node được tạo thông qua một Global Object.

Ví dụ: 

```
global.globalVariable = 1;

global.globalFunction = function () { ... };
```

Ngoài ra, còn một cách nữa để tạo biến global, đó là không cần khai báo kiểu dữ liệu (var, let...) phía trước tên biến.

Ví dụ:

```
globalVariable = 1;

globalFunction = function () { ... };
```
Tuy nhiên, cách này không khuyến khích dùng trong khi code.

## Cài đặt NodeJS

**Đối với Windows:** Truy cập trang web nodejs.org để tải bộ cài về và cài đặt theo hướng dẫn. 

**Đối với Linux:** Mở Terminal và chạy những lệnh command line:

```
sudo apt-get update

sudo apt-get install node
```
Hoặc
```
sudo aptitude update

sudo aptitude install node
```

**Cài đặt những module mới**

Node.js có một trình quản lý gói (package manager), được gọi là Node Package Manager (NPM). Nó tự động được cài cùng Node.js, và sử dụng NPM để cài đặt những module mới. Để cài đặt một module, mở terminal/ command line lên, điều hướng tới folder mong muốn và chạy lệnh sau đây:

`
npm install module_name
`

Không quan trọng đang chạy hệ điều hành nào, dòng lệnh phía trên sẽ cài đặt module xác định tại ví trí module_name. Folder "node_modules" chứa các module nói ở phần trên cũng được tạo ra từ đây.

## Ứng dụng

Tạo ra một file, đặt tên nó là hello.js, và gõ vào dòng code sau:

`
console.log('Hello World!');
`

Bây giờ thử chạy script đó. Mở terminal/command line lên, điều hướng tới folder chứa file hello.js, và chạy dòng lệnh sau:

`node hello.js`

Kết quả trả về sẽ là "Hello World!" hiển thị trên console.

**HTTP Server**

Trước khi áp dụng HTTP module để tạo server. Cần nắm được 1 số lưu ý cơ bản.

Hàm **response.writeHead()** dùng để thiết lập các tham số của header khi gửi kết quả về cho client, cú pháp của nó như sau:

`response.writeHead(statusCode, headerObject);`

Trong đó statusCode là mã code gồm 3 chữ số, đây là mã số quyết định trạng thái của request.

```
100 Continue

101 Switching Protocols

2xx Success

200 Ok

201 Created
...

// Và còn nhiều status khác nữa...
```

Hàm **response.end()** sẽ kết thúc process và trả response về cho phía client.

Khi đã nắm được những cấu trúc của nó rồi, thử viết đoạn code bên dưới và chạy trên browser để quan sát kết quả:

```
// Gọi module http từ node_modules
var http = require("http");

// Tạo server.
http.createServer(function (request, response) {
	// Hàm response.writeHead dùng để thiết lập các tham số của header khi gửi kết quả về cho client
        // 200 là HTTP status code
        // Tham số thứ 2 là một object định nghĩa nội dung của Response Headers
        response.writeHead(200, {
            'Content-Type': 'text/plain',
	    'signature' : 'phatov',
            'company' : 'sun-asterisk'
        });

        // Gửi data về client
        response.end('Hello HTTP!');
}).listen(8080, function() {
    // Lắng nghe cổng 8080 và trả nội dung log trên console.
    console.log('Connected Successfully!');
});

```

Lưu đoạn code này lại và đặt tên file là **test_http.js** và chạy lệnh:

`node test_http.js`

Lúc này sẽ thấy gì **Connected Successfully!** trên terminal, tiếp tục mở browser lên, truy cập vào đường dẫn:

`http://localhost:8080/`

Kết quả nhận được trên browser sẽ là **Hello HTTP!**.

**Handling URL Parameters**

```
var http = require("http");
var url = require("url");

// Tạo server.
http.createServer(function (request, response) {
	// Tạo một biến params để nhận các params được truyền từ phía client.
	// url.parse sẽ chuyển URL request thành 1 objects và có thuộc tính là "query", chính là nơi truy xuất các tham số từ client.
	var params = url.parse(request.url, true).query; 

        response.writeHead(200, {
            'Content-Type': 'text/plain',
	    'signature' : 'phatov',
            'company' : 'sun-asterisk'
        });
        // Gửi data về client, đổ params_test nhận được từ phía client ra sau đoạn text bên dưới
        response.end('Your param got on url is: ' + params['param_test']);

}).listen(8080, function() {
    console.log('Connected Successfully!');
});

```

Lưu đoạn code này lại và đặt tên file là **test_params.js** và chạy lệnh:

`node test_params.js`

Lúc này sẽ thấy gì **Connected Successfully!** trên terminal, tiếp tục mở browser lên, truy cập vào đường dẫn:

`http://localhost:8080/`

Kết quả nhận được trên browser sẽ là **Your param got on url is: undefined**.

Tại thanh URL thay đổi đường dẫn:

`http://localhost:8080/?param_test=abcxyz`

Kết quả nhận được trên browser sẽ là **Your param got on url is: abcxyz**.

##### _Kết thúc part 1. Part 2 sẽ tìm hiểu về cách kết nối cơ sở dữ liệu MySQL và đọc file, ghi file trên NodeJS._
