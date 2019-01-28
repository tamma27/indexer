#

## 1. Description

``` text
lập trình trên Golang
    -> tốc độ cao
    -> dễ triển khai

lưu trực tiếp lên file, RAM và cache
    -> tốn Ram
    -> chạy nhanh

ps: dự án cá nhân chưa public
```

---

## 2. Target

``` text
Phiên bản hiện tại dùng cho các doanh nghiệp vừa và nhỏ.
Lưu trữ sản phẩm bài viết khoảng 10.000 đến 50.000 sản phẩm

Xử lí trên ngôn ngủ GoLang nên tốc độ rất nhanh (realtime)
Search theo inverted index, đáp ứng tốc độ tìm kiếm theo thời gian thực

```

## 3. Install

``` sh
# install Go dep
export GOBIN="$GOPATH/bin"
curl https://raw.githubusercontent.com/golang/dep/master/install.sh | sh

# move project
cd $GOPATH/src/gitlab.com/calla-group/indexer

# install all dependency
dep ensure

# run
go run main.go

```

## 4. API

### 4.1. Debug

> `Note: Only works on Debug mode`

| Description | Method | API | Query |
| ---------------- | -------- | ------------------- | ------------ |
| USERS | GET | /v1/debug/Users | - |
| ITEMS | GET | /v1/debug/Items | - |
| DESCRIPTS | GET | /v1/debug/Descripts | - |
| GALLERYS | GET | /v1/debug/Gallerys | - |
| COUNT | GET | /v1/debug/Count | - |
| ORDERS | GET | /v1/debug/Orders | - |
| RAM CAPACITY | GET | /v1/debug/Sysinfo | - |

``` sh
# Items quick view
curl -X GET http://localhost:8080/v1/debug/Items

# Descripts detail infomation (outside Items)
curl -X GET http://localhost:8080/v1/debug/Descripts

# Gallerys list items
# Dictionary of item by type, sort type
# Format: [type]:[sort]: []ids
curl -X GET http://localhost:8080/v1/debug/Gallerys

.
.
.
etc
```

### 4.2. Login

| Description | Method | API | Attach |
| ---------------- | -------- | ------------------- | ------------ |
| LOGIN | POST | /v1/login/ | { email: yourMail@gmail.com , pass: SHA-2(your pass) } |

``` sh
curl -v -X POST \
  http://localhost:8080/v1/login/ \
  -H 'content-type: application/json' \
  -H 'accept: application/json' \
  -d '{ email: " yourMail@gmail.com ", pass: SHA-2(your pass) }'
```

`return` [Person](#54-person)

### 4.3. Upload Item

> *Admin's [Token](#token) is required!*

| Description | Method | API | Attach |
| ---------------- | -------- | ------------------- | ------------ |
| Upload Item | POST | /v1/item/ | [ItemFull](#51-itemfull) |
| Edit Item | PUT | /v1/item/ | [ItemFull](#51-itemfull) |
| Delete Item | DELETE | /v1/item/ | { ID: itemID } |

### 4.4. Upload Image

> *Admin's [Token](#token) is required!*

| Description | Method | API | Attach | Return |
| - | - | - | - | - |
| Upload Image | POST | /v1/images/ | - | Image's URL |
| Delete Image | DELETE | /v1/images/ | { url: Image's URL } | - |

### 4.5. Get Item

| Description | Method | API | Query |
| ---------------- | -------- | ------------------- | ------------ |
| Get Item | GET | /v1/item/:idItem | - |

``` sh
// ex: ber1ea8549efkoar89og is itemID

curl -v -X GET \
  http://localhost:8080/v1/item/ber1ea8549efkoar89og \
  -H 'content-type: application/json' \
  -H 'accept: application/json'
```

`return` [ItemFull](#51-itemfull)

### 4.6. Search

*Key search need encoded*

| Description | Method | API | Query |
| ---------------- | -------- | ------------------- | ------------ |
| Search Items | GET | /v1/search/:search | { limit: int, page: int } |

``` sh
curl -v -X GET \
  http://localhost:8080/v1/search/your_key_search?limit=100&page=1 \
  -H 'content-type: application/json' \
  -H 'accept: application/json'
```

### 4.7. Hashtag

*Search items by hastag*  
*Key Hashtag need encoded*

| Description | Method | API | Query |
| ---------------- | -------- | ------------------- | ------------ |
| Hashtag Items | GET | /v1/hashtag/:hashtag | { limit: int, page: int } |

``` sh
curl -v -X GET \
  http://localhost:8080/v1/hashtag/your_key_hashtag?limit=100&page=1 \
  -H 'content-type: application/json' \
  -H 'accept: application/json'
```

`return` Array[] [Item](#53-item)

### 4.8. Gallery Item

| Description | Method | API | Query |
| ---------------- | -------- | ------------------- | ------------ |
| Get Items on a Type | GET | /v1/gallery/:type | { limit: int, page: int, sort: string } |

*Type: javascript, golang, php, etc*  
*Type spec: alltypes, recently*  
*Sort: date, price, data-reverse, price-reverse*

``` sh
# Get items from all types
curl -v -X GET \
  http://localhost:8080/v1/gallery/alltypes?limit=100&sort=date \
  -H 'content-type: application/json' \
  -H 'accept: application/json'

# Get items on javascript type
curl -v -X GET \
  http://localhost:8080/v1/gallery/javascript?limit=100&page=1&sort=date \
  -H 'content-type: application/json' \
  -H 'accept: application/json'
```

`return` Array[] [Item](#53-item)

### 4.9. User

| No | Description | Method | API | Required | Return |
| - | - | - | - | - | - |
| 1 | Admin information | GET | /v1/admin/info/ | [Token](#token) | [Person](#54-person) |
| 2 | User information | GET | /v1/user/info/:userId | - | [Person](#54-person) |
| 3 | Register user | POST | /v1/user/info/ | - | [Person](#54-person) |
| 4 | Edit user | PUT | /v1/user/info/ | [Token](#token) | [Person](#54-person) |
| 5 | Send Email Active | PUT | /v1/email/active/:userId | [Token](#token) | - |
| 6 | Active user | PUT | /v1/user/active/:userId | - | - |

### 4.10. Order

| No | Description | Method | API | Required | Return |
| - | - | - | - | - | - |
| 1 | Send Order | POST | /v1/orders/ | User's [Token](#token) | - |
| 2 | Get Order | GET | /v1/orders/ | User's [Token](#token) | [] Order |
| 2 | Get All Order | GET | /v1/orders/all/ | Admin's [Token](#token) | [] Order |
| 2 | Edit status Order | PUT | /v1/orders/:orderId/status/:status | Admin's [Token](#token) | - |

## 5. Struct

### 5.1. ItemFull

``` go
// Full infomation of a Item

type ItemFull struct {
  ID        string    `json:"id"`         // Auto create when Upload Item
  Timestamp int32     `json:"timestamp"`  // Auto create when Upload Item
  Name      string    `json:"name"      binding:"required"`
  Types     []string  `json:"types"     binding:"required"`   // A type of items (ex: Javascript, Php)
  Descript  string    `json:"descript"  binding:"required"`
  User      string    `json:"user"      binding:"required"`
  Avatar    string    `json:"avatar"`
  Price     int       `json:"price"`
  Info      string    `json:"info"`         // summary info of descript
  Imgs      []string  `json:"imgs"`
  Branchs   []string  `json:"branchs"`      // A branch of type (ex: Vuejs, Reactjs, cakePhp)
  Hashtags  []string  `json:"hashtags"`     // Faster than Search function
  SubItems  []SubItem `json:"subitems"`
  Expired   bool      `json:"expired"`
}
```

#### ItemFull Example

``` json
{
  "id": "ber1ea8549efkoar89og",
  "idroot": "ber1ea8549efkoar89og",
  "timestamp": 1538660137,
  "name": "Brexit: PM meets Euro leaders",
  "types": ["News"],
  "branchs": [],
  "price": 0,
  "avatar": "https://myphamnhatcalla.com/image/2018-10-04/img.jpg",
  "imgs": [
    "https://myphamnhatcalla.com/image/img_1....",
    "https://myphamnhatcalla.com/image/img_2....",
  ],
  "hashtags": ["news", "brexit"],
  "info": "European leaders will be discussing...",
  "descript": "",
  "user": "Admin",
  "expired": false
}
```

### 5.2. SubItem

``` go
// Should be used to sell other colors or sizes

type SubItem struct {
  ID     string   `json:"id"`
  Type   string   `json:"type"` // color or size
  Name   string   `json:"name"`
  Price  int      `json:"price""`
  Avatar string   `json:"avatar"`
  Imgs   []string `json:"imgs"`
}
```

### 5.3. Item

``` go
// Short information of ItemFull

type Item struct {
  ID        string   `json:"id"`
  Timestamp int32    `json:"timestamp"`
  Name      string   `json:"name" binding:"required"`
  Types     []string `json:"types" binding:"required"`
  Avatar    string   `json:"avatar"`
  Branchs   []string `json:"branchs"`
  Price     int      `json:"price"`
  User      string   `json:"user"`
  Expired   bool     `json:"expired"`
}
```

### 5.4. Person

``` go
// Person contains User informations

type Person struct {
  ID      string `json:"id"`        // Auto create when register
  Email   string `json:"email"`
  Phone   string `json:"phone"`
  Pass    string `json:"pass"`      // Should be used Encrypt (SHA256, MD5, etc)
  Name    string `json:"name"`
  Type    string `json:"type"`      // is admin, facebook, google, normal, etc
  Address string `json:"address"`
  Token   string `json:"token"`
  Active  bool   `json:"active"`    // is activated
}
```

### ***Token***

*[Login](#42-login) can get your Token (User or Admin)*  
*or*  
*[User information #2](#49-user) can only get user's Token*

## Configs

### Configs reduce

``` go
// TimeoutUpdate 1 seconds
var TimeoutUpdate = 1 * seconds

// TimeoutGc 2 seconds
var TimeoutGc = 2 * seconds

// TimeoutSlowUpdate 1 tiếng
var TimeoutSlowUpdate = 60 * minute

// SizeItems sản phẩm
// lớn hơn không cập nhật tiếp
// lấy theo thời gian gần
var SizeItems = 50000

// SizeGallery sản phẩm mỗi type-sort
// lớn hơn không cập nhật tiếp
// lấy theo thời gian gần
var SizeGallery = 10000

// SizeCache sản phẩm mỗi key
// lớn hơn không cập nhật tiếp
// caches: lưu ids của key tìm kiếm
var SizeCache = 1000

// SizeQuery giới hạn của limit trên search, hashtag
var SizeQuery = 200

// SizeQueryDefault default limit of search, hashtag, gallery
var SizeQueryDefault = "100"
```

### Configs User

``` go
const (
  // User tên của chủ hàng
  User = "myphamnhatcalla"
  // Host máy chủ
  Host = "https://myphamnhatcalla.com/"
  // Port cua indexer
  Port = "8080"
)
```

### Configs Gallery

``` go
/*
 *  Option:
 *    + Full setting { "price", "date" }
 *    + Auto created { "price-reverse", "date-reverse" }
 */
var Sorts = []string{ "price", "date" }

// Types kiểu hàng hóa
var Types = []string{ "javascript", "Nuxtjs" }
```

### Configs Cors

``` go
var AllowOrigins = []string{ "http://127.0.0.1:3000" }
```

## Notes

### 1. đăng kí trang web mới theo các bước

``` text
Client:
  thay đổi config
  thay đổi fanpage
  đổi file index.html

Server:
  config menu
  config ảnh
  config email
  config admin
  config cors

  tat bao mat cho gmail:
  https://myaccount.google.com/lesssecureapps

  tạo app_id facebook

```

### 2. Recommender

``` Recommender System
LDA (Latent Dirichlet Allocation)
  https://github.com/patrikeh/go-topics

Document Similarity
  ...

Reference
http://www.jamesbowman.me/post/semantic-analysis-of-webpages-with-machine-learning-in-go/
```

## Version Tag

``` text
tag: v1.1
  +) advanced search(FilterSideBySide): các từ tìm kiếm phải đứng cạnh nhau
  +) thêm IDRoot: fix lỗi editItem thì thay đổi ID, mất fb-comment
  +) editItem ID thay đổi bằng cách thêm _1, _2 ... vào. tối đa TimesCheckItem lần

  fix nhẹ:
  +) fix Expired item
  +) Order number chuyển sang float

```

``` text
tag: v1.2
  +) xóa IDRoot
  +) thêm subitems lưu trữ thông tin item khác màu or size
  +) sử dụng go dep
```