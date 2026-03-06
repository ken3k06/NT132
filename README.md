# NT132

## MongoDB

MongoDB là một cơ sở dữ liệu mã nguồn mở và là cơ sở dữ liệu NoSQL

Vậy NoSQL là gì? 

- NoSQL là 1 dạng CSDL mã nguồn mở và được viết tắt bởi None-relational SQL.
- NoSQL được phát triển trên js framework với kiểu dữ liệu là JSON và dạng dữ liệu theo kiểu key và value
- NoSQL ra đời như là một mảnh vá cho những khuyết điểm và thiếu sót cũng như hạn chế của mô hình dữ liệu quan hệ RDBMS về tốc độ, tính năng, khả năng mở rộng
- NoSQL có thể dễ dàng mở rộng dữ liệu mà không lo tới những vấn đề như tạo khóa ngoại, khóa chính, kiểm tra ràng buộc .v.v…
- NoSQL bỏ qua tính toàn vẹn của dữ liệu và transaction để đối lấy hiệu suất nhanh và khả năng mở rộng.

Vì MongoDB là cơ sở dữ liệu NoSQL nên nó cũng là một cơ sở dữ liệu hướng tài liệu (document), các dữ liệu được lưu trữ trong document theo kiểu JSON thay vì dạng bảng như CSDL quan hệ nên tốc độ truy vấn sẽ nhanh.

Một số đặc tính nổi bật của MongoDB: 
- Cấu trúc dữ liệu linh hoạt, không ràng buộc cứng các field dữ liệu từ đầu
- Dễ dàng mở rộng theo chiều ngang 
- Hỗ trợ query nhanh vì đã lược bỏ bớt các mối quan hệ ràng buộc cũng như cơ chế caching
- Hỗ trợ bộ index xử lí đa dạng cho các nhau cầu khác nhau. 

Tiếp theo ta sẽ tìm hiểu cách triển khai MongoDB bằng Docker.

Image chính thức của MongoDB trên Docker Hub là [mongo](https://hub.docker.com/_/mongo)

Tải bản mới nhất:
```
docker pull mongo:latest
```
Chạy MongoDB container

```
sudo docker run -d --name mongodb -p 27017:27017 mongo:latest
```

Kiểm tra xem container có đang chạy không bằng:
```
sudo docker ps
```
```
❯ sudo docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                                             NAMES
cde338c2600a   mongo:latest   "docker-entrypoint.s…"   21 seconds ago   Up 20 seconds   0.0.0.0:27017->27017/tcp, [::]:27017->27017/tcp   mongodb
```

Xem chi tiết hơn tại [đây](https://www.mongodb.com/resources/products/compatibilities/docker)



Phần này mình chỉ demo một số tính năng của Docker nên sẽ pull latest image, nhưng trên thực tế thì nên chọn một phiên bản cố định để thống nhất môi trường phát triển và hơn nữa là có document đầy đủ để tiện theo dõi hơn. 

Vào mongosh bên trong mongodb container:
```
sudo docker exec -it mongodb mongosh
```
Kiểm tra thử bằng lệnh:
```
test> db.version()
8.2.5
test> 
```

Một số thuật ngữ trong MongoDB: 


- Document: Một bản ghi thuộc một collection được gọi là một document. Các document lần lượt bao gồm các trường tên và giá trị. 


- _id: là trường bắt buộc có trong mỗi document. Trường _id đại diện cho một giá trị duy nhất trong document MongoDB. Trường _id có thể được hiểu như là khóa chính trong document, còn document có thể được xem như là một hàng trong bảng của CSDL quan hệ. 

- Collection: là nhóm của nhiều document trong MongoDB. Collection có thể được hiểu là một abrng tương ứng trong cơ sở dữ liệu quan hệ. Collection nằm trong một cơ sở dữ liệu duy nhất. Các collection không phải định nghĩa các cột, các hàng hay kiểu dữ liệu trước. 

- Cursor: là một con trỏ đến tập kết quả của một truy vấn. Máy khác có thể lặp qua một con trỏ để lấy kết quả

Ví dụ khi ta chạy:
```
db.users.find()
```
thì kết quả trả về sẽ là một con trỏ đến tập kết quả của truy vấn này. Ta có thể lặp qua con trỏ này để lấy từng document trong tập kết quả. Vì sao lại cần có cursor? Giả sử trong trường hợp collection có tới 10 triệu document thì việc MongoDB trả 1 lần toàn bộ 10 triệu records sẽ khiến RAM quá tả. 

- Database: nơi chứa các collection, giống với CSDL RDMS chúng chứa các bảng. Mỗi DB có một tập tin riêng lưu trữ trên bộ nhớ vật lí. Một máy chủ MongoDB có thể chứa nhiều DB.

- Field: là một cặp name-value trong một document. Một document có thể có không hoặc nhiều trường. Các trường giống các cột ở cơ sở dữ liệu quan hệ. 
- JSON: Là viết tắt của JS Object Notation. 

- Index: là một CTDL đặ cbiệt giúp tăng tốc độ truy vấn bằng cách cho phép MongoDB tìm dữ liệu nhanh hơn thay vì phải quét toàn bộ collection. 

Một số thứ về JSON:

### JSON 



JSON là viết tắt cho [Javascript Object Notation](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Scripting/JSON) là một loại dữ liệu dựa trên chuẩn syntax của Javascript Object. 
Ví dụ:
```
{"username" : "duccorp",
"school": "uit",
"github": "ken3k06"
...}
```

JSON có thể được lưu vào file, có phần mở rộng là `.json` ,hoặc có thể dùng lưu lưu vào một record trong CSDL, v.v... Nó có định dạng đơn giản, bao gồm 2 phần chính đó là key và value, ngoài ra nó cũng dễ dàng sử dụng và truy vấn hơn XML rất nhiều. Thực tế, dữ liệu JSON hợp lệ có thể ở hai định dạng khác nhau: 
- Đầu tiên là dạng tập hợp các cặp key-value được bao bởi một cặp dấu ngoặc nhọn `{...}`. Như ví dụ ở trên
- Dạng thứ hai là tập hợp danh sách có thứ tự các cặp key-value được phân tách bởi dấu phẩy và được bao bởi một cặp dấu ngoặc vuông `[...]`
Ví dụ:
```
[
	{
		"username" : "duccorp", 
		"school" : "uit",
		"github" : "ken3k06"
	},
	{
		"username" : "dangminhtu", 
		"school" : "uit",
		"github" : "little"
	},
	{
		"username" : "nguyentrongnhan",
		"school" : "uit", 
		"github" : "nhantablop"
	}
]
```


Ta nên sử dụng JSON trong tình huống nào? 
Trong trường hợp ta muốn lưu trữ dữ liệu đơn thuần, tức là dưới dạng metadata ở phía server. Chuỗi JSON sẽ được lưu vào database và sau đó khi cần thì dữ liệu sẽ được giải mã. Và ngoài ra cũng còn nhiều ứng dụng khác nhưng mình xin dành cho các bài sau để nói sâu hơn về chủ đề này.
Xem thêm tại đây: https://datatracker.ietf.org/doc/html/rfc8259

Trong Python có sẵn module `json` để ta làm việc với kiểu dữ liệu này
Để chuyển các dữ liệu trong Python sang kiểu dữ liệu JSON thì ta dùng `json.dumps` hoặc `json.dump`
```python
>>> import json
>>> data = {"username" : "duccorp", "option" : "get_flag"}
>>> json.dumps(data)
'{"username": "duccorp", "option": "get_flag"}'
>>>
```

Về sự khác nhau giữa hai methods này thì mọi người đọc doc của python để biết thêm chi tiết: [Doc](https://docs.python.org/3/library/json.html)

Tương tự ta cũng sẽ có `json.load()` và `json.loads()`
```python
>>> d = json.dumps(data).encode()
>>> e = json.loads(d.decode())
>>> type(e)
<class 'dict'>
>>> e
{'username': 'duccorp', 'option': 'get_flag'}
>>> d = json.dumps(data)
>>> type(d)
<class 'str'>
>>>
```


### Làm quen với MongoDB
Doc của MongoDB: https://www.mongodb.com/docs/manual/


Đầu tiên trong mongosh, để kiểm tra các db hiện có:
```
test> show dbs
admin   40.00 KiB
config  60.00 KiB
local   40.00 KiB
```

Để tạo một db mới, xài lệnh `use + db_name`.

```
local> use shop
switched to db shop
shop> show dbs
admin   40.00 KiB
config  60.00 KiB
local   40.00 KiB
shop> 
```
db shop vẫn chưa được tạo do bên trong nó vẫn chưa có dữ liệu. Nhưng trước đó thì mình cần tạo một volume để lưu trữ dữ liệu của container này cho các lần làm việc sau. 

```
sudo docker stop mongodb && sudo docker rm mongodb 
sudo docker volume create mongo-data
```

Chạy lại container với volume mới tạo:
```
sudo docker run -d --name mongodb -p 27017:27017 -v mongo-data:/data/db mongo:latest
```

Tạo collection và thêm document vào collection: 
```
shop> db.createCollection('users');
{ ok: 1 }
shop> db.users.insert({name:'Duc', gender:'male'}); 
DeprecationWarning: Collection.insert() is deprecated. Use insertOne, insertMany, or bulkWrite.
{
  acknowledged: true,
  insertedIds: { '0': ObjectId('69aa391023aa66e9208563b1') }
}
shop> db.users.insertOne({name:'Tu', gender:'male'}); 
{
  acknowledged: true,
  insertedId: ObjectId('69aa392223aa66e9208563b2')
}
shop> 
```
insert sẽ có 3 lệnh chính là `insertOne`, `insertMany` và `bulkWrite`. 

Xem các collection trong db hiện tại:
```
show collections
```
Xem toàn bộ dữ liệu trong collection `users`:
```
shop> db.users.find()
[
  {
    _id: ObjectId('69aa391023aa66e9208563b1'),
    name: 'Duc',
    gender: 'male'
  },
  {
    _id: ObjectId('69aa392223aa66e9208563b2'),
    name: 'Tu',
    gender: 'male'
  },
  {
    _id: ObjectId('69aa3a0e23aa66e9208563b3'),
    name: 'Nhan',
    gender: 'male'
  },
  {
    _id: ObjectId('69aa3a0e23aa66e9208563b4'),
    name: 'Dung',
    gender: 'male'
  }
]
shop> 
```
Hoặc format đẹp hơn: 
```
db.users.find().pretty()
```

Tìm document theo điều kiện:
```
db.users.find({name:'Duc'}) 
```

Cập nhật 1 document hoặc thêm field vào bằng lệnh `updateOne,updateMany`.

```
}
shop> db.users.updateOne({name:'Duc'}, {$set: {age: 24}}) 
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 1,
  upsertedCount: 0
}
shop> db.users.find({name:'Duc'})
[
  {
    _id: ObjectId('69aa391023aa66e9208563b1'),
    name: 'Duc',
    gender: 'male',
    age: 24
  }
]
shop> 
```
lúc đầu không có field `age` trong document của Duc, sau khi chạy lệnh update thì field này đã được thêm vào và có giá trị là 24.

Trong trường hợp có nhiều users cùng tên thì ta có thể dùng `updateMany` để cập nhật tất cả các document này cùng lúc hoặc update theo `ObjectId`. 

Kiểm tra lại volume để xem dữ liệu đã được cập nhật chưa: 

Có thể dùng lệnh sau để kiểm tra vị trí lưu volume: 
```
sudo docker inspect mongodb
```

```
        "Mounts": [
            {
                "Type": "volume",
                "Name": "mongo-data",
                "Source": "/var/lib/docker/volumes/mongo-data/_data",
                "Destination": "/data/db",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            },
```

Xem dữ liệu:
```
sudo ls /var/lib/docker/volumes/mongo-data/_data
```

```
collection-216d347a-98fc-4a2b-ac5e-d58832aa3a80.wt  index-47403e43-b296-4411-b087-551d4b072589.wt  journal          _tmp               WiredTiger.wt
collection-82d45b8c-6e12-49b4-b42e-ebe504e559da.wt  index-5053806c-baed-410c-addc-de47f2ab9d36.wt  _mdb_catalog.wt  WiredTiger
collection-d60b55d8-28d3-420b-a1d6-fb96d6a1a988.wt  index-63775c2b-0fb9-42b5-b35e-05f6c3430e77.wt  mongod.lock      WiredTigerHS.wt
collection-f1854e35-c8f7-4e03-b8d5-d4918a1b1f59.wt  index-a2824842-8f7c-493e-801d-0c6f8877a562.wt  sizeStorer.wt    WiredTiger.lock
diagnostic.data                                     index-d579bc7f-67ee-40c2-b66b-274baafff5e0.wt  storage.bson     WiredTiger.turtle
```
Vậy là đã có dữ liệu được lưu vào rồi. 

Thử tắt và bật lại container để kiểm tra dữ liệu có được lưu lại hay không: 
```
sudo docker stop mongodb && sudo docker start mongodb
```

Tiếp theo mình sẽ thử connect tới MongoDB thông qua python. 
