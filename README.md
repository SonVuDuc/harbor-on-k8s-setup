# Hướng dẫn cài đặt Harbor trên Rancher
### Nội dung
1. Yêu cầu 
2. Cài đặt Rancher
3. Tạo cluster k8s
4. Cài đặt Harbor

## 1. Yêu cầu 

Danh sách server:
  + 1 Node Rancher
  + 1 Node Master
  + 1 Node Worker

Cần 3 VPS hoặc VM
  + HĐH: Ubuntu server 18.04
  + Mỗi node đều được cài đặt Docker
  

| Server  | Hostname   |  Role  |IP Address     |
| ------- |:----------:|:-----: |--------------:|
| 1       | Rancher    | Rancher| 192.168.41.1  |
| 2       | Master     | Master | 192.168.41.128|
| 3       | Worker1    | Worker | 192.168.41.130|


## 2. Cài đặt Rancher

SSH đến node Rancher và chạy lệnh sau
```
docker run -d --restart=unless-stopped \
    -p 80:80 -p 443:443 \
    -v /opt/rancher:/var/lib/rancher \
    rancher/rancher:latest
```
Khi quá trình cài đặt hoàn tất, truy cập vào Rancher thông qua địa chỉ https://192.168.41.1
Hệ thống sẽ yêu cầu set password cho tài khoản admin. Điền password và đăng nhập vào Rancher

![Screenshot from 2020-08-06 00-01-15](https://user-images.githubusercontent.com/32956424/89441881-0cdbcc00-d778-11ea-9389-f41fff8a59f2.png)

Sau khi đăng nhập vào Rancher sẽ có giao diện như sau

![Screenshot from 2020-08-06 00-07-30](https://user-images.githubusercontent.com/32956424/89442316-d81c4480-d778-11ea-816e-1fdf4c56518a.png)

## 3. Tạo cluster k8s

Chọn **Add Cluster**, click **From Existing Node**
![Screenshot from 2020-08-06 00-08-20](https://user-images.githubusercontent.com/32956424/89442420-0568f280-d779-11ea-8eca-3b0df99e58a1.png)

Đặt tên cho Cluster vào click **Next**

Tùy chọn role cho từng node muốn thêm vào cluster bằng cách tick vào ô tương ứng.

![Screenshot from 2020-08-06 00-11-06](https://user-images.githubusercontent.com/32956424/89442625-5547b980-d779-11ea-81f8-87955cb3bce8.png)

Với node Master, chọn **etcd** và **Control Plane**. Copy command, SSH đến node Master và chạy lệnh.

Với node Worker, chọn **Worker**. Copy command, SSH đến node Worker và chạy lệnh.

Cluster khi tạo xong sẽ như này

![Screenshot from 2020-08-06 00-34-51](https://user-images.githubusercontent.com/32956424/89444933-ad33ef80-d77c-11ea-8ec7-65892a857586.png)


## 4. Cài đặt Harbor

Chọn **Global** -> **Tên Cluster** -> **Default**. 

![Screenshot from 2020-08-06 00-35-34](https://user-images.githubusercontent.com/32956424/89445000-c63ca080-d77c-11ea-96fd-381367f5de9a.png)

Chọn **App** -> **Laucnh**. Tìm app Harbor 

![Screenshot from 2020-08-06 00-37-51](https://user-images.githubusercontent.com/32956424/89445247-14ea3a80-d77d-11ea-8ada-03d56ffd5bef.png)

Tùy chỉnh password cho admin Harbor và chọn **Launch**

![Screenshot from 2020-08-06 00-38-44](https://user-images.githubusercontent.com/32956424/89445360-3f3bf800-d77d-11ea-9229-12bf5b774875.png)












