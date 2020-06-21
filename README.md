## Upload and Resize image with Nodejs Exxpress

Nếu bạn là một lập trình viên, đặc biệt là lập trình wed thì mỗi trang web của bạn không ít thì nhiều cũng sẽ có chức năng upload imag. Có thể là upload ảnh avatar cho user, có thể là upload ảnh thumb, ... Nói tóm lại là việc upload image không còn quá xa lạ với tất cả chúng ta. Trong bài viết này, tôi xin giới thiệu với mọi người cách upload image bằng NodeJs và resize image bằng thư viện _sharp_

# Step 1. Tạo mới node project

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
# Step 2: Create file server.js

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

# Step 3: Config EJS template

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

# Step 4: Config Express router
Frameword Express đã cung cấp sẵn cho chúng ta module router để tạo và quản lý các router của project, để thao tác, hãy tạo file router.js

```
// router.js

const express = require('express');
const app = express();
const router = express.Router();

router.get('/', async function (req, res) {
  await res.render('index');
});
module.exports = router;
```
Sau đó, trong file server.js chúng ta cần required file router mà mình đã định nghĩa

```
// server.js

const express = require('express');
const bodyParser = require('body-parser');

const app = express();
const port = process.env.PORT || 3000;
const router = require('./router');

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended: true}));

app.use(express.static('public'));
app.set('view engine', 'ejs');

app.use('/upload', router);

app.listen(port, function () {
  console.log('Server is running on PORT',port);
});
```
Để kiểm tra bạn hãy vào đường dẫn [http://localhost:3000/upload](http://localhost:3000/upload)

# Step 5; Create form

Trong file index.ejs thêm đoạn code sau

```
// index.ejs

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>EJS Engine</title>
  </head>
  <body>
      <p>Image Upload</p>
      <form method="post" action="upload/post" enctype="multipart/form-data">
        <input type="file" name="image" /><br /><br />
        <button type="submit" name="upload">Upload</button>
      </form>
  </body>
</html>
```
Bây giờ, trên giao diện bạn đã có thể chọn được 1 image để upload

# Step 6: Create file upload middleware

Với việc cài đặt thư viện multer, bạn có thể dễ dàng tạo middleware cho project của mình. Trước tiên hãy tạo 1 file uploadMiddleware.js và thêm vào đoạn code

```
// uploadMiddleware.js

const multer = require('multer');

const upload = multer({
  limits: {
    fileSize: 4 * 1024 * 1024,
  }
});

module.exports = upload
```
Cập nhật file router.js để có thẻ sử dụng được file middleware

```
// router.js

const express = require('express');
const app = express();
const router = express.Router();
const upload = require('./uploadMiddleware');

router.get('/', async function (req, res) {
  await res.render('index');
});

router.post('/post', upload.single('image'), async function (req, res) {
  await console.log('post');
});

module.exports = router;
```
Nguyên tắc hoạt động như sau, khi bạn thực hiện thao tác upload thì method post sẽ được thực thi. và trong cửa sổ console thì bạn sẽ thấy chữ post được in ra, Việc tiếp theo chúng ta cần làm là resize image vừa được gửi lên

# Step 7: Resize imaga

Đây là lúc mà thư viện sharp phát huy, tạo file resize.js với nội dung sau:

```
// Resize.js

const sharp = require('sharp');
const uuidv4 = require('uuid/v4');
const path = require('path');

class Resize {
  constructor(folder) {
    this.folder = folder;
  }
  async save(buffer) {
    const filename = Resize.filename();
    const filepath = this.filepath(filename);

    await sharp(buffer)
      .resize(300, 300, {
        fit: sharp.fit.inside,
        withoutEnlargement: true
      })
      .toFile(filepath);
    
    return filename;
  }
  static filename() {
    return `${uuidv4()}.png`;
  }
  filepath(filename) {
    return path.resolve(`${this.folder}/${filename}`)
  }
}
module.exports = Resize;
```
Trong file này, chúng ta đã tạo ra một class với tên là Resize.js và chấp nhận một argument truyền vào là path folder để chứa nhưng file đã upload. Bạn cần tạo thư mục images trong thư mục public

Sau đó, định nghĩa 1 static function là filename, nhiệm vụ của nó là sinh random 1 tên cho file của bạn. Cuối cùng là method save, trong đó có filepath, filename, và việc gọi thư viện sharp để resize, cuối cùng là lưu file vào path đã được định nghĩa

# Step 8: Save image in the file system

Cập nhật file router như sau:
```
// router.js

const express = require('express');
const app = express();
const path = require('path');

const router = express.Router();
const upload = require('./uploadMiddleware');
const Resize = require('./Resize');

router.get('/', async function (req, res) {
  await res.render('index');
});

router.post('/post', upload.single('image'), async function (req, res) {
  const imagePath = path.join(__dirname, '/public/images');
  const fileUpload = new Resize(imagePath);
  if (!req.file) {
    res.status(401).json({error: 'Please provide an image'});
  }
  const filename = await fileUpload.save(req.file.buffer);
  return res.status(200).json({ name: filename });
});

module.exports = router;
```

Cuối cùng, là test lạ project của bạn nhé. Chúc các bạn thành công. 

