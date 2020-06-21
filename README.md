## Upload and Resize image with Nodejs Exxpress

Nếu bạn là một lập trình viên, đặc biệt là lập trình wed thì mỗi trang web của bạn không ít thì nhiều cũng sẽ có chức năng upload imag. Có thể là upload ảnh avatar cho user, có thể là upload ảnh thumb, ... Nói tóm lại là việc upload image không còn quá xa lạ với tất cả chúng ta. Trong bài viết này, tôi xin giới thiệu với mọi người cách upload image bằng NodeJs và resize image bằng thư viện _sharp_

#1. Tạo mới node project

```
mkdir nodeimageupload
cd nodeimageupload
```

Sau khi tạo được thư mục project, thì bạn hãy tạo file packae.json trong thư mục này và chạy câu lệnh sau:

```
npm init -y
```

Tiếp theo, cài đặt thêm thư viện để phục vụ cho các bước tiếp theo

- express: NodeJs framework
- ejs: là teamplate engine
- body-parse, multer: giúp xử lý multipart/form-data
- uuid: để tạo một tên random
- sharp: là thư viện resize ảnh cần thiết

```
npm install express body-parser ejs uuid multer sharp --save
```
Đương nhiên, để node có thể tự reload mỗi khi chúng ta edit code, thì bạn cần cài thêm thư viện nodedemon vào nữa

```
npm install nodemon --save-dev
```

Bây giờ, hãy mở thư mục project bằng chương trình edit ưa thích của bạn, mở file package.json và thêm vào dòng code

```
"scripts": {
    "start": "nodemon server.js"
}
```
#2. Step 2: Create file server.js

```
// server.js

const express = require('express');
const app = express();
const port = process.env.PORT || 3000;

app.listen(port, function () {
  console.log('Server is running on PORT',port);
});
```
Tiếp theo, vào terminal và chạy dòng lệnh

```
npm start
```
Sau khi câu lệnh trên được chạy, thì bạn có thể vào link localhost:3000 để kiểm tra web của mình

#3. Step 3: Config EJS template

Là một trang web, thì luôn luôn yêu cầu phải có css và js, và thường những file css và js chúng ta sẽ thiết kế và để chúng trong thư mục public. Vì vậy, hãy tạo thư mục public trong thư mục project của mình và sửa file server.js như sau

```
// server.js

app.use(express.static('public'));
```
Tiếp theo là khai báo để project của chúng ta có thể sử dụng được ejs thư viện

```
app.set('view engine', 'ejs');
```
Nhưng file html sẽ được đặt trong thư mục víews. Tạo thư mục views cùng level với thư mục public và trong đó tạo file index.ejs với nội dung sau:

```
<!-- index.ejs -->

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>EJS Engine</title>
  </head>
  <body>
    <p>Node Express Image Upload and Resize Example</p>
  </body>
</html>
```
Tương tư, tạo file index.html trong thư mục public

```
<!-- index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
   <meta charset="UTF-8">
   <meta name="viewport" content="width=He, initial-scale=1.0">
   <meta http-equiv="X-UA-Compatible" content="ie=edge">
   <title>Node Express Image Upload Example</title>
</head>
<body>
   <p>
     Node Express Image Upload and Resize Example
   </p>
</body>
</html>
```

