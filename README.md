# DNS anycast

Mục đích của bài LAB là tạo ra một mô hình DNS anycast sử dụng Docker.

Bài LAB sử dụng 6 networks và 8 containers. Các containers được base trên ubuntu server 16.04 bao gồm (4 routers, 2 DNS servers và 2 máy client )

Cả 2 máy chủ DNS servers đều sử dụng IP anycast. Trên mỗi DNS server quản lý một bản ghi A và bản ghi A đó trỏ đến 2 IP khác nhau

## Mô hình 

<img src="https://i.imgur.com/JP6qSsr.png">

Quy hoạch IP 

<img src="https://i.imgur.com/1yXd2Wy.png">

## Cài đặt 

```
git clone https://github.com/MinhKMA/anycast.git
cd anycast
./build-images
./setup
```

## Trên client 1 

- Truy cập vào container 

    ```
    docker exec -it client1 /bin/bash
    ```

    + Thực hiện lệnh

        ```
        dig @192.168.0.10 minhkma.local
        ```
    
    + Kết quả trả về:

        + Bản ghi A của `minhkma.local` có giá trị là `1.2.0.5` do DNS1 server quản lý

            ```
            [root@client1 anycast]# docker exec -it client1 /bin/bash
            root@f5ae9a8b7769:/# dig @192.168.0.10 minhkma.local

            ; <<>> DiG 9.10.3-P4-Ubuntu <<>> @192.168.0.10 minhkma.local
            ; (1 server found)
            ;; global options: +cmd
            ;; Got answer:
            ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 31620
            ;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 2

            ;; OPT PSEUDOSECTION:
            ; EDNS: version: 0, flags:; udp: 4096
            ;; QUESTION SECTION:
            ;minhkma.local.			IN	A

            ;; ANSWER SECTION:
            minhkma.local.		86400	IN	A	1.2.0.5

            ;; AUTHORITY SECTION:
            minhkma.local.		86400	IN	NS	ns1.minhkma.local.

            ;; ADDITIONAL SECTION:
            ns1.minhkma.local.	86400	IN	A	10.1.1.10

            ;; Query time: 0 msec
            ;; SERVER: 192.168.0.10#53(192.168.0.10)
            ;; WHEN: Wed May 01 08:16:30 UTC 2019
            ;; MSG SIZE  rcvd: 92
            ``` 

## Trên client2

- Truy cập vào container 

    ```
    docker exec -it client2 /bin/bash
    ```

    + Thực hiện lệnh

        ```
        dig @192.168.0.10 minhkma.local
        ```
    
    + Kết quả trả về:

        + Bản ghi A của `minhkma.local` có giá trị là `1.9.9.7` do DNS2 server quản lý

            ```
            [root@client1 anycast]# docker exec -it client2 /bin/bash
            root@e682d9515c47:/# dig @192.168.0.10 minhkma.local

            ; <<>> DiG 9.10.3-P4-Ubuntu <<>> @192.168.0.10 minhkma.local
            ; (1 server found)
            ;; global options: +cmd
            ;; Got answer:
            ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 8249
            ;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 2

            ;; OPT PSEUDOSECTION:
            ; EDNS: version: 0, flags:; udp: 4096
            ;; QUESTION SECTION:
            ;minhkma.local.			IN	A

            ;; ANSWER SECTION:
            minhkma.local.		86400	IN	A	1.9.9.7

            ;; AUTHORITY SECTION:
            minhkma.local.		86400	IN	NS	ns1.minhkma.local.

            ;; ADDITIONAL SECTION:
            ns1.minhkma.local.	86400	IN	A	10.1.3.10

            ;; Query time: 0 msec
            ;; SERVER: 192.168.0.10#53(192.168.0.10)
            ;; WHEN: Wed May 01 08:16:52 UTC 2019
            ;; MSG SIZE  rcvd: 92
            ```

Như vậy qua đó ta có thể thấy cùng truy vấn bản ghi ở DNS server có IP là `192.168.0.10` lại cho ra hai kết quả khác nhau 

## Trên DNS1 server 

Để kiểm chứng lại sự hoạt động của mô hình LAB. Tôi tắt dịch vụ `quagga` trên một trong hai máy chủ DNS. Sau đó kiểm chứng kết quả lúc này và ban đầu của máy client1 

- DNS1

    ```
    docker exec -it dns1 /bin/bash
    root@202f34a72f08:/# service quagga stop
    ```

- Client1 

    ```
    root@f5ae9a8b7769:/# dig @192.168.0.10 minhkma.local

    ; <<>> DiG 9.10.3-P4-Ubuntu <<>> @192.168.0.10 minhkma.local
    ; (1 server found)
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 2250
    ;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 2

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 4096
    ;; QUESTION SECTION:
    ;minhkma.local.			IN	A

    ;; ANSWER SECTION:
    minhkma.local.		86400	IN	A	1.9.9.7

    ;; AUTHORITY SECTION:
    minhkma.local.		86400	IN	NS	ns1.minhkma.local.

    ;; ADDITIONAL SECTION:
    ns1.minhkma.local.	86400	IN	A	10.1.3.10

    ;; Query time: 5 msec
    ;; SERVER: 192.168.0.10#53(192.168.0.10)
    ;; WHEN: Wed May 01 09:07:41 UTC 2019
    ;; MSG SIZE  rcvd: 92
    ```

    ***WOOO! Ban đầu giá trị bản ghi là 1.2.0.5 cơ mà....***

### Bài lab của tôi kết thúc ở đây. Rất mong được sự góp ý của độc giả.
