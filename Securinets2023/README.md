{%hackmd @themes/dracula %}
# Forensics #
## The Betrayers ##

**Files:**
1. **quals-2023.s01** 2GB
2. **quals-2023.s02** 1.45GB

**Description:**

*An IT company provides a critical service to customers. One day, the service is attacked by DDoS. The Attack is originated from within the company's internal network, and that each compromised host was sending a large number of requests to the server.*

*The attack occurred shortly after the company installed a new patch to one of its software programs. Every new version should be hosted by a special web server with DNS record in the private network but this time the new version is hosted by the Technical Director server!!!*

*The Technical Director is in trouble and he said that is not the attacker!*

*The strange thing is that the program is deleted directly after the blue team figure out the attack!*

*We need to get a release from that version and figure out how the attack occur!*

*The binary expect to do DDoS attack on specifc IP. In this challenge we changed the IP and the rate of send requests*

*Author : Raf²*

**Note: Hướng đi của mình là unintended và nếu bạn muốn đọc thêm về hướng intended của tác giả thì tham khảo [writeup chính thức của tác giả](https://github.com/4n6nk8s/quals-2023-write-ups/tree/main/The-Betrayers)**

Bài này cung cấp cho chúng ta 2 tập tin ``EnCase image`` được tạo bởi ``FTK Imager``

![](https://hackmd.io/_uploads/HJ2dxATi3.png)

Như mọi khi, mình có thể dùng 1 trong 2 công cụ đắc lực để phân tích các tập tin disk image là ``FTK Imager`` hoặc ``Autopsy``. Và vì đọc cái description làm mình không biết đi hướng nào nên mình sử dụng ``Autopsy`` để phân tích.

Description có mô tả về 1 cuộc tấn công DDOS từ trong nội bộ công ty (đúng như tên bài - Những kẻ phản bội) và xảy ra sau khi công ty đã cài đặt bản vá mới cho một trong những chương trình phần mềm của họ, ta có thể đặt ra giả thuyết rằng kẻ tấn công đã lợi dụng việc cập nhập phần mềm để phát tán botnet và gây ra DDOS. Okay, vậy là mã độc, tới đây hướng đi dễ dàng hơn rồi.

Để tìm kiếm dấu vết của một chương trình độc hại nào đấy được thực thi thì mình thường check event log. Tuy nhiên trong ``quals-2023.s01`` không có folder ``winevt`` nên mình sẽ chuyển sang xem PowerShell history để tìm kiếm manh mối.

Mình mở tập tin ``Console_History.txt`` nằm trong đường dẫn quen thuộc.

![](https://hackmd.io/_uploads/HJ50mRTsh.png)

Trong này chỉ có các lệnh cài đặt và kiểm tra ``wsl``, ngoài ra không còn gì hết. Ta có thể suy đoán kẻ tấn công sử dụng ``wsl`` để triển khai các hoạt động của mình.

Theo mặc định thì ``wsl`` file system nằm ở trong ``ext4.vhdx`` ở đường dẫn

``%USERPROFILE%\AppData\Local\Packages\CanonicalGroupLimited. Ubuntu20. 04onWindows_79rhkp1fndgsc\LocalState``

Mình extract ``ext4.vhdx`` ra.

![](https://hackmd.io/_uploads/SkZnBCpon.png)

Tuy nhiên lúc đang làm bài này thì chả biết do lỗi gì, mình không load vào FTK Imager được (lúc viết wu này thì load được chả hiểu sao) nên mình đã giải nén ra để phân tích các tập tin bên trong.

Đầu tiên là mình check bash trước xem những lệnh nào đã được nhập 

```
$ cat .bash_history
udo vim /etc/wsl.conf
sudo apt install docker.io
sudo systemctl status docker
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.17.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
kind
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl
rm kubectl
vim k-config.yaml
kind create cluster -n quals --config=k-config.yaml
docker images
kubectl cluster-info --context kind-quals
kubectl get nodes
sudo mkdir /etc/kubernetes/enc
sudo vim /etc/kubernetes/enc/enc.yaml
kubectl get nodes
USER=XXXXXXXXXXXXXXXXXXXX
PASSWORD=XXXXXXXXXXXXXXXXXXX
SERVER=XXXXXXXXXXXXXXXXXX
kubectl create secret docker-registry regcred --docker-server=$SERVER --docker-username=$USER --docker-password=$PASSWORD
kubectl get secrets
vim deploy.yaml
kubectl apply -f deploy.yaml
kubectl get pods
kubectl expose deployment my-app --name=my-nodeport-service --type=NodePort --port=80 --target-port=80 --node-port=30999 --dry-run=client -o yaml > service.yaml
kubectl apply -f service.yaml
kubectl get services
cd
ls
ls -lha
vim .bash_history
exit
```

Mình bị choáng ngợp khi lần đầu nhìn thấy đống này vì mình không có kinh nghiệm về kubenetes nên khi nhìn thấy đống này mình có hơi nản :v.

Nhưng mà nếu phân tích từ từ thì ta có thể hiểu được.

```
udo vim /etc/wsl.conf
sudo apt install docker.io
sudo systemctl status docker
```

Khúc này thì attacker sửa đổi cấu hình của wsl và tải về ``docker.io``

```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.17.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
kind
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl
```

Ở đoạn này thì attacker tải về ``kind`` và ``kubectl``
https://kind.sigs.k8s.io/docs/user/quick-start/
https://kubernetes.io/vi/docs/tasks/tools/install-kubectl/

Attacker sửa đổi cấu hình cho ``kind``, sau đó liệt kê các docker images có trong máy.

```
vim k-config.yaml
kind create cluster -n quals --config=k-config.yaml
docker images
```

Attacker pull image chứa botnet từ private registry rồi sau đó triển khai trong Kubenetes.
https://4n6nk8s.tech/2022/09/08/Kubernetes/private-docker-k8s/

```
kubectl cluster-info --context kind-quals
kubectl get nodes
sudo mkdir /etc/kubernetes/enc
sudo vim /etc/kubernetes/enc/enc.yaml
kubectl get nodes
USER=XXXXXXXXXXXXXXXXXXXX
PASSWORD=XXXXXXXXXXXXXXXXXXX
SERVER=XXXXXXXXXXXXXXXXXX
kubectl create secret docker-registry regcred --docker-server=$SERVER --docker-username=$USER --docker-password=$PASSWORD
kubectl get secrets
vim deploy.yaml
kubectl apply -f deploy.yaml
kubectl get pods
kubectl expose deployment my-app --name=my-nodeport-service --type=NodePort --port=80 --target-port=80 --node-port=30999 --dry-run=client -o yaml > service.yaml
kubectl apply -f service.yaml
kubectl get services
```

Nội dung của ``deploy.yaml``, ta có thể thấy image tên là ``kubersex``. 

![](https://hackmd.io/_uploads/B1kGGms33.png)

Vậy giờ chúng ta cần tìm kiếm và pull image từ private registry đấy, sau đó xem cách mà cuộc tấn công diễn ra.

Trước tiên thì ta cần phải biết USER, PASSWORD và SERVER chứa private registry đấy. 

Về Username thì dựa vào blog bên trên ta có thể giả sử nó là ``medrafk8s``

https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

Theo như bài viết từ trang chủ của Kubernetes thì thông tin về authorization token nằm trong tập tin ``docker/config.json`` tuy nhiên nó đã bị xoá.

Theo hướng intended của tác giả thì tác giả phục hồi ``etcd``. Mình lại làm theo 1 hướng khác, mình sử dụng công cụ tối thượng của người chơi Forensics. Đúng vậy, là Strings - Grep và search trong HxD.

![](https://hackmd.io/_uploads/rkrD3Xi32.png)

Vậy là mình đã có được nội dung của docker/config.json

```
dockerconfigjson¬{"auths":{"docker.io":{"username":"medrafk8s","password":"dckr_pat_MLkRjYtjdk7dwT80W_dx3VTuac8","auth":"bWVkcmFmazhzOmRja3JfcGF0X01Ma1JqWXRqZGs3ZHdUODBXX2R4M1ZUdWFjOA=="}}}
```
```
USERNAME: medrafk8s
PASSWORD: dckr_pat_MLkRjYtjdk7dwT80W_dx3VTuac8
SERVER: docker.io
```

Đã có đầy đủ thông tin, tiến hành kéo image về thôi.

Trước tiên là cài đặt ``docker.io``, phiên bản docker này khác docker image nên nếu không cài đặt thì sẽ không login được.

Nhập lệnh ``docker login docker.io -u medrafk8s -p dckr_pat_MLkRjYtjdk7dwT80W_dx3VTuac8`` để đăng nhập, các lần đăng nhập sau docker sẽ tự động trích xuất credentials từ ``config.json`` để đăng nhập.

Sau đó pull image với lệnh ``docker pull medrafk8s/kubersex``

Sử dụng công cụ ``dive`` để check layer content của docker image này. 

![](https://hackmd.io/_uploads/S10G2Nj33.png)

Dựa vào các lệnh này ta có thể chắc chắn đây là Docker image chạy Nginx webserver.

![](https://hackmd.io/_uploads/BkBKA4o23.png)


Tiếp theo chạy docker image với lệnh ``docker run medrafk8s/kubersex`` ta có thể thấy image này tự động thực thi các tác vụ.

![](https://hackmd.io/_uploads/SyrA1Nj23.png)

Mình nhập thêm option ``-it`` để tương tác trực tiếp với các file bên trong docker image. Vì đây là web app nên ta có thể tìm kiếm ở đường dẫn ``/usr/share/nginx/html``.

Mình thấy có 1 tập tin tên là ``app``, mình có chạy thử thì thấy nó yêu cầu nhập ``Serial-Key``. Nhưng theo lịch sử thì ``Serial-Key.txt`` đã bị xoá

![](https://hackmd.io/_uploads/H1tc_Nj23.png)

![](https://hackmd.io/_uploads/r1ASJSj2n.png)

Ta có thể retrieve ``Serial-Key.txt`` nằm trong layer bằng cách tìm kiếm tập tin này trong ``overlay2``.

![](https://hackmd.io/_uploads/B1WHgBj2h.png)

Vậy là ta đã có serial key. Việc tiếp theo là chạy web app và dùng strace để trace process của nó. Ta có thể thấy flag nằm trong request.

![](https://hackmd.io/_uploads/r1lb-rsh3.png)

References:
https://www.login-securite.com/2021/10/29/sthackwriteup-forensic-docker-layer/







