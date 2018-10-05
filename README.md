# mithril

[Source](https://mithril.js.org/simple-application.html "Permalink to Simple application - Mithril.js")

# Ứng dụng đơn giản - Mithril.js

Hãy phát triển 1 ứng dụng đơn giản gồm 1 vài khía cạnh lớn của Single Page Applications (SPA)

Một ví dụ chạy tương tác có thể được thấy ở đây  [flems: Simple Application][1]

Đầu tiên hãy tạo 1 điểm bắt đầu cho ứng dụng. Tạo 1 file `index.html`:
```html
<!doctype html>
<html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <title>My Application</title>
    </head>
    <body>
        <script src="bin/app.js"></script>
    </body>
</html>
```

Dòng <! Doctype html> cho biết đây là một tài liệu HTML 5. Thẻ meta charset cho biết mã hóa của tài liệu và thẻ meta Viewport chỉ ra cách các trình duyệt di động nên mở rộng quy mô trang. Thẻ title chứa văn bản được hiển thị trên tab trình duyệt cho ứng dụng này và thẻ script cho biết đường dẫn đến tệp Javascript điều khiển ứng dụng là gì.

Chúng tôi có thể tạo toàn bộ ứng dụng trong một tệp Javascript duy nhất, nhưng làm như vậy sẽ khiến việc điều hướng codebase trở nên khó khăn hơn. Thay vào đó, hãy chia mã thành các mô-đun và lắp ráp các mô-đun này thành một gói bin / app.js.

Có nhiều cách để thiết lập 1 công cụ  bundler, nhưng hầu hết đều được phân phối thông qua NPM. Trong thực tế, hầu hết các thư viện và công cụ JS hiện đại đều được cung cấp bằng cách này, bao gồm cả Mithril. NPM là Node.js Package Manager. Để tải NPM, [install Node.js][2]; NPM sẽ được tự động cài theo đó. Khi bạn đã cài Node.js và NPM, mở command line và chạy dòng lệnh sau: 

 ``  
    npm init -y
`` 

Nếu NPM được cài đặt đúng, 1 file `package.json` sẽ được tạo ra. File này sẽ chứa 1 file  meta-description xương sống của dự án. Hãy chỉnh sửa thông tin dự án và tác giả trong tệp này.



Để cài đặt Mithril, làm theo các hướng dẫn trong trang  [installation][3] . Khi bạn có bộ khung project với Mithril đã được cài đặt, chúng ta đã sẵn sàng tạo 1 ứng dụng.

Hãy bắt đầu bằng việc tạo 1 module để lưu trạng thái của chúng ta. Hãy tạo 1 file đặt là :
`src/models/User.js`
    
    
    // src/models/User.js
    var User = {
        list: []
    }
    
    module.exports = User
    
Giờ hãy thêm code để tải 1 vài dữ liệu từ 1 server. Để giao tiếp với 1 server, chúng ta có thể sử dụng tiện ích XHR của mithril. Đầu tiên chúng ta sẽ thêm Mithril vào module:
    
    
    // src/models/User.js
    var m = require("mithril")
    
    var User = {
        list: []
    }
    
    module.exports = User
    
Tiếp theo chúng ta tạo 1 hàm sẽ  bắn ra 1 lời gọi XHR. Gọi hàm này là `loadList`
    
    
    // src/models/User.js
    var m = require("mithril")
    
    var User = {
        list: [],
        loadList: function() {
            // TODO: make XHR call
        }
    }
    
    module.exports = User
    

Sau đó chúng ta có thể thêm 1 lời gọi `m.request` để tạo 1 request XHR, Với bài hướng dẫn này, chúng ta sẽ tạo những lời gọi XHR đến API [REM][4],một API REST giả lập được thiết kế để tạo mẫu nhanh..API này trả về 1 danh sách các người dùng từ endpoint  `GET https://rem-rest-api.herokuapp.com/api/users`. Hãy sử dụng `m.request`  để tạo 1 request XHR và lưu dữ liệu với response của endpoint đó.
    
    
    // src/models/User.js
    var m = require("mithril")
    
    var User = {
        list: [],
        loadList: function() {
            return m.request({
                method: "GET",
                url: "https://rem-rest-api.herokuapp.com/api/users",
                withCredentials: true,
            })
            .then(function(result) {
                User.list = result.data
            })
        },
    }
    
    module.exports = User
    
Tùy chọn `method` là 1  [HTTP method][5]. Để lấy dữ liệu từ server mà không bị ảnh hưởng bởi các ảnh hưởng phụ trên server, chúng ta cần sử dụng phương thức `GET`. `url` là địa chỉ của  endpoint aPI. `withCredentials: true` chỉ định rằng chúng ta đang sử dụng cookies(đây là 1 yêu cầu với REM API)

Lời gọi `m.request` trả về 1 Promise sẽ reoslve dữ liệu từ endpoint. Mặc định , Mithril coi body của response HTTP ở định dạng JSON và tự động chuyển nó về dạng đối tượng hoặc mảng JS. Sau đó callback `.then` sẽ chạy khi XHR request hoàn thành. Trong trường hợp này, callback gán mảng `result.data` vào `User.list`

Chú ý rằng chúng ta chúng có 1 câu `return` trong `loadList`. Đây là 1 việc làm tốt khi làm việc với Promises, điều này cho phép chúng ta đăng ký nhiều callback để chạy sau khi request XHR hoàn thành.

Mô hình đơn giản này có 2 thành phần: User.list ( 1 mảng các đối tượng người dùng), và User.loadList ( 1 phương thức lấy User.list với dữ liệu server)


* * *

Giờ, chúng ta sẽ tạo 1 module view để chúng ta có thể hiển thị dữ liệu từ module model User.

Tạo 1 file gọi là `src/views/UserList.js`. Đầu tiên, hãy thêm Mithril và model của chúng ta, vì thế chúng ta sẽ cần cả hai:
    
    
    // src/views/UserList.js
    var m = require("mithril")
    var User = require("../models/User")
    

Giờ, chúng ta sẽ tạo 1 thành phần Mithril. 1 thành phần chỉ đơn giản là 1 đối tượng có 1 phương thức `view`:
    
    
    // src/views/UserList.js
    var m = require("mithril")
    var User = require("../models/User")
    
    module.exports = {
        view: function() {
            // TODO add code here
        }
    }
    

Mặc định, các view Mithril được mô tả là sử dụng [hyperscript][6].  Hyperscript cung cấp cú pháp vắn tắt có thể rút gọn 1 cách tự nhiên hơn HTML với các tags phức tạp, và ngoài ra, vì cú pháp của nó chỉ đơn thuần là JS, nó có thể hỗ trợ rất nhiều hệ sinh thái công cụ JS: ví dụ  [Babel][7], [JSX][8] (inline-HTML syntax extension), [eslint][9] (linting), [uglifyjs][10] (minification), [istanbul][11] (code coverage), [flow][12] (static type analysis), etc.

Hãy sử dụng hyperscript Mithril để tạo ra danh sách các item. Hyperscript  cách chuẩn nhất để viết các view Mithril, nhưng [JSX là 1 cách thay thế cũng rất thông dụng mà bạn có thể tìm hiểu][8] khi bạn cảm thấy dễ chịu với những thứ căn bản:
    
    
    // src/views/UserList.js
    var m = require("mithril")
    var User = require("../models/User")
    
    module.exports = {
        view: function() {
            return m(".user-list")
        }
    }
    
String  `".user-list"` string là 1 CSS selector, và hiển nhiên,  `.user-list` đại diện cho 1 class. KHi 1 tag không được chỉ định, `div` sẽ được chọn làm mặc định. Vì vậy, view này sẽ tương đương với.
 <div class="user-list"></div>.

Giờ hãy sử dụng danh sách người dùng từ model chúng ta đã tạo trước đó ('User.list') để loop tự động qua dữ liệu:
    
    
    // src/views/UserList.js
    var m = require("mithril")
    var User = require("../models/User")
    
    module.exports = {
        view: function() {
            return m(".user-list", User.list.map(function(user) {
                return m(".user-list-item", user.firstName + " " + user.lastName)
            }))
        }
    }
    

Vì  `User.list` là 1 mảng Js, và vì các view hyperscript cũng chỉ là JS, chúng ta có thể lặp qua mảng bằng cách sử dụng phương thức .map Lệnh này sẽ tạo 1 mảng các vnodes đại diện cho danh sách các thẻ `div`, mỗi phần tử chứa tên của 1 người dùng.

Dĩ nhiên, vấn đề là chúng ta không bao giờ gọi hàm `User.loadList`. Vì thế `User.loadList` vẫn là 1 mảng rỗng, và vì thế view này sẽ render ra 1 trang trắng. Vì chúng ta muốn `User.loadList` sẽ được gọi khi chúng ta render thành phần này, chũng ta sẽ tận dụng lợi thế của thành phần  [lifecycle methods][13]:
    
    
    // src/views/UserList.js
    var m = require("mithril")
    var User = require("../models/User")
    
    module.exports = {
        oninit: User.loadList,
        view: function() {
            return m(".user-list", User.list.map(function(user) {
                return m(".user-list-item", user.firstName + " " + user.lastName)
            }))
        }
    }
    

Chú ý rằng chúng ta thêm 1 phương thức `oninit` vào thành phần,  quan hệ với `User.loadList`.Điều này có nghĩa là khi thành phần được khởi tạo, User.loadList sẽ được gọi, dẫn tới 1 XHR request. KHi server trả về 1 response, `User.list` sẽ được gán dữ liệu.

Và cũng chú ý rằng chúng ta không thực hiện `oninit: User.loadList()` (với dấu ngoặc đơn ở cuối.) Sự khác biệt ở đây là `oninit: User.loadList()`  gọi hàm 1 lần và ngay lập tức, nhưng `oninit: User.loadList()`  chỉ được gọi khi thành phần render. Đây là 1 điều khác biệt quan trọng và 1 bẫy phổ biến với những dev mới làm quen JS: gọi hàm ngay lập tức nghĩa là XHR request sẽ được bắn ra ngay khi source code được thực thi, ngay cả khi thành phần không bao giờ được render. Cũng như là khi thành phần không bao giờ được tạo ( mặc  dù điều hướng qua lại trong ứng dụng), hàm sẽ không được gọi lại như mong đợi.

* * *

Hãy render view từ file đầu vào `src/index.js` chúng ta tạo trước đó:
    
    
    // src/index.js
    var m = require("mithril")
    
    var UserList = require("./views/UserList")
    
    m.mount(document.body, UserList)
    
Lời gọi `m.mount` sẽ render các thành phần cụ thể (`UserList`) vào 1 DOM element (`document.body`), xóa bất kỳ Dom nào tồn tại trước đó. Mở 1 file HTML trong 1 trình duyệt giờ sẽ hiển thị 1 danh sách tên mọi người.

* * *

Giờ đây danh sách giống plain hơn vì chúng ta không định nghĩa bất kỳ style nào
Có rất nhiều thỏa thuận và các thư viện giống nhau giúp cho việc tổ chức style cho ứng dụng ngày nay. 1 vài trong số chúng, như [Boostrap][14] quyết định một tập hợp cụ thể của các cấu trúc HTML và tên class có ý nghĩa, trong đó có xu hướng cung cấp sự thiếu hụt nhận thức thấp, nhưng nhược điểm của việc tùy biến làm cho khó khăn hơn. Những người khác, như Tachyons cung cấp một số lượng lớn các tên class tự mô tả, nguyên tử với chi phí tự đặt tên cho các class không mang tính ngữ nghĩa. “CSS-in-JS” là một loại hệ thống CSS đang phát triển phổ biến, về cơ bản bao gồm CSS phạm vi thông qua công cụ chuyển biên. Các thư viện CSS-in-JS đạt được khả năng bảo trì bằng cách giảm kích thước của không gian vấn đề, nhưng đến với chi phí có độ phức tạp cao.

Dù cho quy ước / thư viện CSS bạn chọn là gì, nguyên tắc chung là tránh các khía cạnh xếp tầng của CSS. Để giữ cho hướng dẫn này đơn giản, chúng tôi sẽ chỉ sử dụng CSS đơn giản với tên lớp quá rõ ràng để các kiểu tự cung cấp nguyên tử của Tachyons và sự va chạm tên lớp không được thông qua độ dài của tên lớp. Plain CSS  có thể đủ cho các dự án có độ phức tạp thấp (ví dụ: 3 đến 6 tháng của thời gian triển khai ban đầu và một vài giai đoạn dự án).

Để thêm kiểu, trước tiên, hãy tạo tệp có tên styles.css và đưa nó vào tệp index.html:
```html
<!doctype html>
<html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />
        <title>My Application</title>
        <link href="styles.css" rel="stylesheet" />
    </head>
    <body>
        <script src="bin/app.js"></script>
    </body>
</html>
```

Giờ chúng ta có thể style thành phần `UserList` :
    
    
    .user-list {list-style:none;margin:0 0 10px;padding:0;}
    .user-list-item {background:#fafafa;border:1px solid #ddd;color:#333;display:block;margin:0 0 1px;padding:8px 15px;text-decoration:none;}
    .user-list-item:hover {text-decoration:underline;}
    

 CSS ở trên được viết bằng cách sử dụng một quy ước giữ tất cả các kiểu cho một quy tắc trong một dòng, theo thứ tự bảng chữ cái. Quy ước này được thiết kế để tận dụng tối đa bất động sản màn hình và dễ dàng quét các bộ chọn CSS (vì chúng luôn ở bên trái) và nhóm logic của chúng, và nó thực thi các quy tắc CSS có thể dự đoán và thống nhất cho mỗi bộ chọn .

Rõ ràng bạn có thể sử dụng bất kỳ khoảng cách / iquy ước thụt đầu dòng bạn thích. Ví dụ trên chỉ là một minh họa của một quy ước không phổ biến rộng rãi có các lý do mạnh mẽ đằng sau nó, nhưng đi lệch khỏi các quy ước khoảng cách định hướng làm mịn hơn

giờ reload lại cửa sổ trình duyệt sẻ hiển thị vài 1 phần tử có style


* * *

Hãy thêm Router vào ứng dụng của chúng tôi.

Router có nghĩa là liên kết màn hình với một URL duy nhất, để tạo khả năng chuyển từ một "trang" sang một trang khác. Mithril được thiết kế cho các ứng dụng một trang, do đó, các "trang" này không nhất thiết phải là các tệp HTML khác nhau theo nghĩa truyền thống của từ đó. Thay vào đó,Router trong các ứng dụng trang đơn giữ lại cùng một tệp HTML trong suốt vòng đời của nó, nhưng thay đổi trạng thái của ứng dụng thông qua Javascript.Router phía máy khách có lợi ích của việc tránh nhấp nháy màn hình trống giữa các lần chuyển trang và có thể giảm lượng dữ liệu được gửi xuống từ máy chủ khi được sử dụng kết hợp với kiến trúc hướng dịch vụ web (tức là ứng dụng tải xuống dữ liệu dưới dạng JSON thay vì tải xuống các đoạn kết xuất trước của HTML chi tiết).

Chúng ta có thể thêm Router bằng cách thay đổi lời gọi m.mount thành lời gọi m.route:
    
    
    // src/index.js
    var m = require("mithril")
    
    var UserList = require("./views/UserList")
    
    m.route(document.body, "/list", {
        "/list": UserList
    })
    

Lời gọi m.route chỉ định rằng ứng dụng sẽ được hiển thị thành document.body. Đối số "/ list" là tuyến mặc định. Điều đó có nghĩa là người dùng sẽ được chuyển hướng đến tuyến đường đó nếu họ đến một tuyến đường không tồn tại. Đối tượng {"/ list": UserList} khai báo một bản đồ các tuyến hiện có và các thành phần mà mỗi tuyến đường xử lý.

Làm mới trang trong trình duyệt giờ đây sẽ thêm #! / List vào URL để cho biết rằng Router đang hoạt động. Do tuyến đường đó hiển thị UserList, chúng ta vẫn sẽ thấy danh sách những người trên màn hình như trước đây.

Các đoạn mã #! được gọi là băm và là chuỗi thường được sử dụng để triển khai Router phía máy khách. Có thể định cấu hình chuỗi này qua m.route.prefix. Một số cấu hình yêu cầu hỗ trợ thay đổi phía máy chủ, vì vậy chúng tôi sẽ tiếp tục sử dụng hashbang cho phần còn lại của hướng dẫn này.

* * *

Hãy thêm một Router khác vào ứng dụng của chúng ta để chỉnh sửa người dùng. Trước tiên, hãy tạo mô-đun có tên  `views/UserForm.js`
    
    
    // src/views/UserForm.js
    
    module.exports = {
        view: function() {
            // TODO implement view
        }
    }
    

Sau đó, chúng ta có thể `require` mô-đun mới này từ `src/index.js`
    
    
    // src/index.js
    var m = require("mithril")
    
    var UserList = require("./views/UserList")
    var UserForm = require("./views/UserForm")
    
    m.route(document.body, "/list", {
        "/list": UserList
    })
    

Và cuối cùng, chúng ta có thể tạo một Router tham chiếu đến nó:
    
    
    // src/index.js
    var m = require("mithril")
    
    var UserList = require("./views/UserList")
    var UserForm = require("./views/UserForm")
    
    m.route(document.body, "/list", {
        "/list": UserList,
        "/edit/:id": UserForm,
    })
    

Lưu ý rằng Router mới có một: id trong đó. Đây là một tham số route; bạn có thể nghĩ về nó như một thẻ wild, route / edit / 1 sẽ phân giải thành UserForm với id là "1". / edit / 2 cũng sẽ phân giải thành UserForm, nhưng với id là "2". Và cứ thế.

Hãy triển khai thành phần UserForm để nó có thể phản hồi các tham số Router đó:
    
    
    // src/views/UserForm.js
    var m = require("mithril")
    
    module.exports = {
        view: function() {
            return m("form", [
                m("label.label", "First name"),
                m("input.input[type=text][placeholder=First name]"),
                m("label.label", "Last name"),
                m("input.input[placeholder=Last name]"),
                m("button.button[type=button]", "Save"),
            ])
        }
    }
    

và hãy thêm 1 vài  styles vào `styles.css`:
    
    
    /* styles.css */
    body,.input,.button {font:normal 16px Verdana;margin:0;}
    
    .user-list {list-style:none;margin:0 0 10px;padding:0;}
    .user-list-item {background:#fafafa;border:1px solid #ddd;color:#333;display:block;margin:0 0 1px;padding:8px 15px;text-decoration:none;}
    .user-list-item:hover {text-decoration:underline;}
    
    .label {display:block;margin:0 0 5px;}
    .input {border:1px solid #ddd;border-radius:3px;box-sizing:border-box;display:block;margin:0 0 10px;padding:10px 15px;width:100%;}
    .button {background:#eee;border:1px solid #ddd;border-radius:3px;color:#333;display:inline-block;margin:0 0 10px;padding:10px 15px;text-decoration:none;}
    .button:hover {background:#e8e8e8;}
    

Ngay bây giờ, thành phần này không làm gì khi trả lời các sự kiện của người dùng. Hãy thêm một số mã vào Mô hình người dùng của chúng ta trong src / models / User.js. Đây là  code hiện tại:
    
    
    // src/models/User.js
    var m = require("mithril")
    
    var User = {
        list: [],
        loadList: function() {
            return m.request({
                method: "GET",
                url: "https://rem-rest-api.herokuapp.com/api/users",
                withCredentials: true,
            })
            .then(function(result) {
                User.list = result.data
            })
        },
    }
    
    module.exports = User
    

Hãy thêm code để cho phép chúng ta tải một người dùng
    
    
    // src/models/User.js
    var m = require("mithril")
    
    var User = {
        list: [],
        loadList: function() {
            return m.request({
                method: "GET",
                url: "https://rem-rest-api.herokuapp.com/api/users",
                withCredentials: true,
            })
            .then(function(result) {
                User.list = result.data
            })
        },
    
        current: {},
        load: function(id) {
            return m.request({
                method: "GET",
                url: "https://rem-rest-api.herokuapp.com/api/users/" + id,
                withCredentials: true,
            })
            .then(function(result) {
                User.current = result
            })
        }
    }
    
    module.exports = User
    

Lưu ý rằng chúng ta đã thêm một thuộc tính User.current và một phương thức User.load (id) để điền thuộc tính đó. Bây giờ chúng ta có thể điền vào khung nhìn UserForm bằng cách sử dụng phương thức mới này:
    
    
    // src/views/UserForm.js
    var m = require("mithril")
    var User = require("../models/User")
    
    module.exports = {
        oninit: function(vnode) {User.load(vnode.attrs.id)},
        view: function() {
            return m("form", [
                m("label.label", "First name"),
                m("input.input[type=text][placeholder=First name]", {value: User.current.firstName}),
                m("label.label", "Last name"),
                m("input.input[placeholder=Last name]", {value: User.current.lastName}),
                m("button.button[type=button]", "Save"),
            ])
        }
    }
    

Tương tự như thành phần UserList, oninit gọi User.load (). Hãy nhớ rằng chúng tôi đã có thông số Router được gọi là: id trên "/ edit /: id": tuyến đường UserForm? Tham số Router trở thành thuộc tính của vnodes của thành phần UserForm, do đó Router đến "/ edit / 1 " sẽ làm cho vnode.attrs.id có giá trị là "1".

Bây giờ, hãy sửa đổi chế độ xem Danh sách người dùng để chúng tôi có thể điều hướng từ đó đến UserForm:
    
    
    // src/views/UserList.js
    var m = require("mithril")
    var User = require("../models/User")
    
    module.exports = {
        oninit: User.loadList,
        view: function() {
            return m(".user-list", User.list.map(function(user) {
                return m("a.user-list-item", {href: "/edit/" + user.id, oncreate: m.route.link}, user.firstName + " " + user.lastName)
            }))
        }
    }
    

Ở đây chúng ta đã thay đổi .user-list-item thành a.user-list-item. Chúng ta đã thêm một href tham chiếu Router mà chúng ta muốn và cuối cùng chúng ta đã thêm oncreate: m.route.link. Điều này làm cho liên kết hoạt động giống như một liên kết Router(trái ngược với việc chỉ hoạt động giống như một liên kết thông thường). Điều này có nghĩa là việc nhấp vào liên kết sẽ thay đổi một phần của URL xuất hiện sau hashbang #! (do đó thay đổi Router mà không dỡ tải trang HTML hiện tại)

Nếu bạn làm mới trang trong trình duyệt, bây giờ bạn có thể nhấp vào một người và được đưa đến một biểu mẫu. Bạn cũng có thể nhấn nút quay lại trong trình duyệt để quay lại từ biểu mẫu tới danh sách mọi người.

* * *

Bản thân biểu mẫu vẫn không lưu khi bạn nhấn "Lưu". Hãy làm cho biểu mẫu này hoạt động:
    
    
    // src/views/UserForm.js
    var m = require("mithril")
    var User = require("../models/User")
    
    module.exports = {
        oninit: function(vnode) {User.load(vnode.attrs.id)},
        view: function() {
            return m("form", {
                    onsubmit: function(e) {
                        e.preventDefault()
                        User.save()
                    }
                }, [
                m("label.label", "First name"),
                m("input.input[type=text][placeholder=First name]", {
                    oninput: m.withAttr("value", function(value) {User.current.firstName = value}),
                    value: User.current.firstName
                }),
                m("label.label", "Last name"),
                m("input.input[placeholder=Last name]", {
                    oninput: m.withAttr("value", function(value) {User.current.lastName = value}),
                    value: User.current.lastName
                }),
                m("button.button[type=submit]", "Save"),
            ])
        }
    }
    

Chúng ta đã thêm các sự kiện oninput vào cả hai đầu vào, đặt thuộc tính User.current.firstName và User.current.lastName khi người dùng nhập.

Ngoài ra, chúng ta đã tuyên bố rằng phương thức User.save nên được gọi khi nhấn nút “Lưu”. Hãy thực hiện phương thức đó:
    
    
    // src/models/User.js
    var m = require("mithril")
    
    var User = {
        list: [],
        loadList: function() {
            return m.request({
                method: "GET",
                url: "https://rem-rest-api.herokuapp.com/api/users",
                withCredentials: true,
            })
            .then(function(result) {
                User.list = result.data
            })
        },
    
        current: {},
        load: function(id) {
            return m.request({
                method: "GET",
                url: "https://rem-rest-api.herokuapp.com/api/users/" + id,
                withCredentials: true,
            })
            .then(function(result) {
                User.current = result
            })
        },
    
        save: function() {
            return m.request({
                method: "PUT",
                url: "https://rem-rest-api.herokuapp.com/api/users/" + User.current.id,
                data: User.current,
                withCredentials: true,
            })
        }
    }
    
    module.exports = User
    

Trong phương thức lưu ở dưới cùng, chúng ta đã sử dụng phương pháp PUT HTTP để chỉ ra rằng chúng ta đang upserting dữ liệu đến máy chủ.

Bây giờ hãy thử chỉnh sửa tên của người dùng trong ứng dụng. Khi bạn lưu thay đổi, bạn sẽ có thể thấy thay đổi được phản ánh trong danh sách người dùng.



* * *

Hiện tại, chúng ta chỉ có thể điều hướng trở lại danh sách người dùng qua nút quay lại trình duyệt. Lý tưởng nhất, chúng ta muốn có một trình đơn - hoặc tổng quát hơn, một bố cục nơi chúng ta có thể đặt các yếu tố giao diện người dùng toàn cục

Hãy tạo 1 file `src/views/Layout.js`:
    
    
    // src/views/Layout.js
    var m = require("mithril")
    
    module.exports = {
        view: function(vnode) {
            return m("main.layout", [
                m("nav.menu", [
                    m("a[href='https://mithril.js.org/list']", {oncreate: m.route.link}, "Users")
                ]),
                m("section", vnode.children)
            ])
        }
    }
    

Thành phần này khá đơn giản, nó có một liên kết đến danh sách người dùng. Tương tự như những gì chúng ta đã làm cho các liên kết / chỉnh sửa, liên kết này sử dụng m.route.link để kích hoạt hành vi định tuyến trong liên kết.

Lưu ý rằng cũng có một phần tử với vnode.children là child vnode là một tham chiếu đến vnode đại diện cho một thể hiện của thành phần Layout (tức là vnode được trả về bởi một cuộc gọi m (Layout)). Vì vậy, vnode.children đề cập đến bất kỳ child nào của vnode đó.

Hãy thêm một số style:
    
    
    /* styles.css */
    body,.input,.button {font:normal 16px Verdana;margin:0;}
    
    .layout {margin:10px auto;max-width:1000px;}
    .menu {margin:0 0 30px;}
    
    .user-list {list-style:none;margin:0 0 10px;padding:0;}
    .user-list-item {background:#fafafa;border:1px solid #ddd;color:#333;display:block;margin:0 0 1px;padding:8px 15px;text-decoration:none;}
    .user-list-item:hover {text-decoration:underline;}
    
    .label {display:block;margin:0 0 5px;}
    .input {border:1px solid #ddd;border-radius:3px;box-sizing:border-box;display:block;margin:0 0 10px;padding:10px 15px;width:100%;}
    .button {background:#eee;border:1px solid #ddd;border-radius:3px;color:#333;display:inline-block;margin:0 0 10px;padding:10px 15px;text-decoration:none;}
    .button:hover {background:#e8e8e8;}
    

Hãy thay đổi router trong `src/index.js` để thêm layout vào file mix:
    
    
    // src/index.js
    var m = require("mithril")
    
    var UserList = require("./views/UserList")
    var UserForm = require("./views/UserForm")
    var Layout = require("./views/Layout")
    
    m.route(document.body, "/list", {
        "/list": {
            render: function() {
                return m(Layout, m(UserList))
            }
        },
        "/edit/:id": {
            render: function(vnode) {
                return m(Layout, m(UserForm, vnode.attrs))
            }
        },
    })
    

Chúng ta thay thế mỗi thành phần bằng một RouteResolver (về cơ bản, một đối tượng có phương thức render). Các phương thức render có thể được viết theo cách tương tự như các khung nhìn thành phần thông thường, bằng cách lồng các lời gọi m ().

Điều thú vị cần lưu ý là cách các thành phần có thể được sử dụng thay vì một chuỗi chọn trong một lời gọi m (). Ở đây, trong route / list, chúng ta có m (Layout, m (UserList)). Điều này có nghĩa là có một vnode gốc đại diện cho một thể hiện của Layout, trong đó có một Usern vnode là con duy nhất của nó.

Trong route / edit /: id, cũng có một đối số vnode mang các tham số route vào thành phần UserForm. Vì vậy, nếu URL là / edit / 1, thì vnode.attrs trong trường hợp này là {id: 1}, và m này (UserForm, vnode.attrs) tương đương với m (UserForm, {id: 1}). Mã JSX tương đương sẽ là ``.

Làm mới trang trong trình duyệt và bây giờ bạn sẽ thấy điều hướng toàn cục trên mọi trang trong ứng dụng.

* * *


[1]: https://flems.io/#0=N4IgzgpgNhDGAuEAmIBcICGAHLA6AVmCADQgBmAljEagNqgB2GAthGiAKqQBOAsgPZJoBIqVj8GiSewBuGbgAIuERQF4FwADoMFuhVAph4qBbQC6xbXv38MSADKHjCsgFcGCChIAUASg1W1nrcEPCu3DrMuCEAjq4QRt5aOkGprPAAFoImmiAA4gCiACq5limp1uFQOSAZ8PBYYKgA9M0hzAC0IUYd2BS4GSr8ANau2HjizM19za48YKWBFXoA7hSZAMIhQpIUGFBNCvDc8WXLAL6+S0G4mRAM3m4e8F4P3a5Q8P7Jy9bK3LgDEYFOp3p9cEgMPAMNdrJdrucytdYOEQpITMBEdcoLYkCYnp4fBQkN9YcFQuFItEIHEEvAkmS0qEsniFLlCiUSIyglUanUGk1Wu0unTelh+oNuCMxjhcJNpuLZvNmrkFABqBTEs6-XRrTbbe4vfaHY6nbnw8o3O4PAkvHxgr4BS3Lf5y1GGkEKB3mq6WrEMa5gDAyCD49yEh6k53ksIRBRRWLxRI-HXx5nZNkgAAKHE52p1vMz-MaLTaEE63XgYolQ1G4zl-CmMzmKjAKpA6qUPDd3DR8FwWu51kh0JMrpRvcN+d+eoyW2Qhr2BxMpog07hvrh2nOIERjBYbHQ-0cRhEJBA4kkhtk8i7KhP8E9Kd0EgoDHWY+7OLsD+nMgoEArGGzyvH4TrLCEsaRN4uS4C23AdEC8ClHeAJIbgzDYI84Z2g88FRqmXoUnGzAwZgcE8IhTgdOs5YocAGQhGQNTNMg6ztp28EDkgxAKBIsAhFCobxtE-CuIggJvsMiIKFxlDcEYAByB6dqqqoalxUAYEpB6bhUlx6bo5zbruxD7qw7D-AAYvw3BRIQ56XlI8A3oo1m2cwT7XK+77OLaoEyAwggQN8rrfkg3iBcFuBQscYDcb4-rWP+gHARGYHPkEkGUvGZFkB59FDhUEhgK4ABGzAfi4OGgSF4GERUEC4FgIQhpIAAiEBkBgHz0oZDV-N2QYhn4RWpMZ0bjbxtBjbluRaWVwgLdAKG5FZFAKY+TCsLkvjrhUpG5G+WDiQODAnfAtDwAAnlgECqIgAAe8BmLQWBabAEBZFAQjcKo62bQo20QGYhWTb8PkXSYUSzgAgvU3BkXIUDxCh-k+Mj8Shd2E59rg8k6awnqYxAlz7TqJOfioPZ4wT8DKTt4MbuTQSHSAy1QICGCLVAq0gPY2lbQeu0s9YbPHadEuXe9GCfd9v2qALwLA6DJD1QNkPidDuBwwjSP7Kjavow8JPY9TuOGlzhMQMTBuk3ts3JXbVMAhbkhW-TwtM3oZOzWzZXifAEi4AH9QSFdt33aVFXrKrvG5AAysGEAi9yZj9RNO57iAwPsAL11if2DliBIzmuQo+eF15lopUB1UgRjQVCARFTZSRZGYW+XMF+JKEzd7uhs0wMgYfcrh947ehszCauZQNjFdSYADkzRIUvosQx4gmINrUriU1BgMMMk9GfHnDzLts3pxvg9kZAEYoVFQhyhkVBIGi-XWOnCImdnufoPWYuF5S7XnQAmQuTUWpdQoI9bwS8ADES9fTgP3t4JA-AUSsHdmVQQ10z6rycGDawuQCFGFyBibkaJfppVwhlWabdoKV3ErxUix4nC+E-j7BE04SFsXgM0VAxJyHqyyvcah9d0pPzqnPVuxFGEYB7vAFh3h3J2V4lImKCMwAcPNNw7cvhTLmUPCAOUYBRDAKvNIdAOCkB4LOhdYgIdA4SA0PldEQU7L7AUAARgAGxYEegoAAaioSETAADcmFuAAHM3wmAAEwAAYAkTW0N3KuwAomxIYKgbxyTAk9SDpEjAj0OhrCQJkXJiTqkBPCRNUeDBXAaCyXExJCg2kAGZ8l1O0Gk+CVFgTACQh0Iw10YCoCCgwCAxSYmtPaT47pWA7BIDfNE1AiSekMAoioAZVZaKeWAGVWWwxol7wYHieB3UrkYHCTg7g1DvEBIUGAfgBgkAKHgUgL54TxA4m4KgeBHSgXhJWWAGW11UBlRxLAYYMzsnrPmY8x64SllfNWagAAHE87xABWWpT0qxCHENwKErwJkSGmfU-pwz9moCyCGRQwACUdCJbZUlEhUDuF+ofSlvStkcw0KC8FkLoWwpaTktpbS8XIvqVLDQdyHlPJeW8j5XykC3Nsr9LodgKBzFQB02pODSlgAoAAL3RQqnZRqQWGGFVCjBYr5DwslQs2pqKVkMDWXk7F0rwnlMqXkxJABSTZTiw46EOcc05YlzkAogPGjV9yVC5KVa84kqrvmWoQiSlZeqDXIt+bZAFQKOk2rBVpCFb4eUdHtTCuFcy2neuRe69FTafG+uZaykluFyVTNDaHIOOT6UqHlVGs5FyIAYsnZOupu4LDsykjQegOcDzsEqpkbgVBzxVHYMWQUsxzonIbFMddjEqAAAFvG4Cvb45op7N2cyATdO67AHLnDMOcIAA
[2]: https://nodejs.org/en/
[3]: https://mithril.js.org/installation.html
[4]: http://rem-rest-api.herokuapp.com/
[5]: https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods
[6]: https://mithril.js.org/hyperscript.html
[7]: https://mithril.js.org/es6.html
[8]: https://mithril.js.org/jsx.html
[9]: http://eslint.org/
[10]: https://github.com/mishoo/UglifyJS2
[11]: https://github.com/gotwarlost/istanbul
[12]: https://flowtype.org/
[13]: https://mithril.js.org/lifecycle-methods.html
[14]: http://getbootstrap.com/
[15]: http://tachyons.io/
[16]: https://mithril.js.org/route.html#mrouteprefix
[17]: https://mithril.js.org/route.html#routeresolver
[18]: https://mithril.js.org/examples.html
[19]: https://gitter.im/MithrilJS/mithril.js

  
