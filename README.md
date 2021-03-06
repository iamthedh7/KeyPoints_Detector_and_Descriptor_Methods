<!-- Title -->
<h1 align="center"><b>KEYPOINTS DETECTORS & DESCRIPTOR METHODS</b></h1>
<h1 align="center"><b>~~~~~~~</b></h1>

<!-- Title -->
<h1 align="center"><b>A/ Keypoints Detectors Only</b></h1>

	BÀI NGHIÊN CỨU NÀY BAO GỒM:
	
		KEYPOINTS DETECTORS ONLY: HARRIS, FAST, STAR

		KEYPOINTS DESCRIPTORS:

		    •	FLOATING-POINTS DESCRIPTORS
					SIFT (having own keypoints detector)
					SURF (having own keypoints detector)

		    •	BINARY DESCRIPTORS
					BRIEF (using others keypoints detector)
					ORB   (having own keypoints detector)
					BRISK (having own keypoints detector)
					FREAK (using others keypoints detector)


## 1. HARRIS 

Harris là một thuật toán phát hiện góc sử dụng trong Thị giác máy tính được giới thiệu lần đầu vào 1988. Kể từ đó, nó đã được cải tiến và áp dụng trong nhiều thuật toán để tiền xử lý hình ảnh cho các ứng dụng tiếp theo. Cùng tìm hiểu xem nó hoạt động như thế nào ?

Hình bên trái là ảnh gốc, giữa là ảnh biểu thị cường độ Gradient theo chiều x, tiếp theo là biểu thị cường độ Gradient theo chiều y, cuối cùng là biểu thị cường độ Gradient theo cả chiều x và y.

![image](https://user-images.githubusercontent.com/81065789/148052662-081da42b-0686-48bd-9072-3db08f1df2a7.png)

Để xác định được một điểm là Corner (góc), chúng ta sẽ lấy tổng các giá trị Gradient theo chiều x và theo chiều y trong vùng đó:

![image](https://user-images.githubusercontent.com/81065789/148052875-50da8d89-8968-4e6e-b222-de27cdbb6fc9.png)

Nếu cả 2 giá trị này đều đủ lớn, thì chúng ta có thể định nghĩa vùng đó là một góc. Quá trình này được thực hiện cho mọi pixel của hình ảnh. Phương phá này hoạt động vì các vùng được gọi là góc sẽ có số lượng lớn Gradient theo chiều ngang và Gradient theo chiều dọc.

Để mở rộng phương thức này sang các góc tùy ý, trước tiên chúng ta cần tính toán biểu diễn cường độ Gradient của hình ảnh, và sau đó sử dụng cường độ Gradient này để xây dựng lên một ma trận M:

![image](https://user-images.githubusercontent.com/81065789/148053166-04e534d7-f447-4f39-899e-b14ebd7bffc7.png)

Sau khi ma trận M được định nghĩa, chúng ta sẽ sử dụng công thức sau để tính giá trị điểm số biểu thị mức độ của các góc.

![image](https://user-images.githubusercontent.com/81065789/148053429-2dcdf575-42b3-4160-8b9f-7f5659e8badf.png)

* det(M)=λ1λ2

* trace(M)=λ1+λ2

* Với λ1 and λ2 là 2 trị riêng (eigenvalues) của ma trận M.

Sau đó, chúng ta có thể xem xét điều kiện sau:

      - Nếu |R| nhỏ thì đây là một khu vực phẳng, có nghĩa là không phải keypoint

      - Nếu R < 0 thì đây là một cạnh, và đây cũng không phải keypoint

      - Nếu |R| lớn thì đây chính là một keypoint
      
Với Harris, ngay cả khi hình ảnh được xoay, chúng ta vẫn có thể tìm thấy các góc giống nhau. Đó là điều hiển nhiên vì các góc trong hình ảnh xoay cũng vẫn là các góc trong hình ảnh ban đầu, hai hình sau minh họa cho điều này:

![1](https://user-images.githubusercontent.com/81065789/148065886-43e17743-85bb-4073-adf7-b7a7b75e25e7.jpg)
![2](https://user-images.githubusercontent.com/81065789/148065896-b450c892-39ea-40b7-aa8b-8c0f9efecfd1.jpg)

## 2. FAST

FAST keypoints detector được giới thiệu đầu tiên vào 2006, được sử dụng để phát hiện các góc trong hình ảnh, được triển khai trong thư viện OpenCV và được áp dụng nhiều nhất cho các ứng dụng thời gian thực hoặc các thiết bị bị hạn chế tài nguyên, nơi không có nhiều thời gian tính toán hoặc sức mạnh để sử dụng các kĩ thuật dò tìm keypoints tiên tiến hơn. 

Cụ thể: FAST không tính và lưu trữ ma trận descriptor cho hình ảnh như thuật toán keypoints detector trong SIFT và SURF, nên không tốn dung lượng lưu trữ ma trận này, phù hợp cho bộ nhớ của các thiết bị di dộng.

Ý tưởng của FAST là để một pixel được coi là một góc phải có _**ít nhất n**_ các pixel liền kề dọc theo chu vi hình tròn có bán kính r, _**tất cả**_ đều sáng hơn hoặc tối hơn pixel trung tâm bởi một ngưỡng t nào đó.

Hãy cùng xem một ví dụ:

![3](https://user-images.githubusercontent.com/81065789/148164855-90fbdaba-9e20-4b7a-89bc-851a779ed2bb.png)

Ở đây, chúng ta muốn xem xét liệu pixel trung tâm có nên được coi là một điểm then chốt hay không? Ví dụ: pixel trung tâm = 32, để pixel này được coi là một keypoint, nó phải có n = 12 pixel liền kề dọc theo đường biên của vòng tròn có giá trị đều lớn hơn 32 + t hoặc nhỏ hơn 32 - t. Giả sử rằng t = 16 cho ví dụ này.

Như chúng ta có thể thấy, chỉ có 8 pixel liền kề tối hơn (được đánh dấu bằng hình chữ nhật màu xanh lá cây, tất cả các pixel khác là hình chữ nhật màu đỏ) so với pixel trung tâm - do đó, pixel trung tâm không phải là keypoint.

Một trường hợp khác:

![4](https://user-images.githubusercontent.com/81065789/148164868-c0765261-0227-40cd-af8a-6d43c9330469.png)

Chúng ta thấy có 14 pixel liền kề nhau và sáng hơn pixel trung tâm. Vì thế, pixel trung tâm này được coi là 1 keypoint.

Chúng ta có thể thấy được là thuật toán bên trên tuy rất đơn giản, nhưng lại được sử dụng rất nhiều trong các ứng dụng Computer Vision hằng ngày cũng như các ứng dụng real-time, đòi hỏi tốc độ tính toán cao. Nhược điểm là nó không hiệu quả với mức độ nhiễu cao và có phụ thuộc vào một giá trị ngưỡng.

Hình ảnh qua thuật toán FAST cho kết quả như sau:

![FAST_nonmaxSuppression](https://user-images.githubusercontent.com/81065789/148171386-21dac5d3-80d4-4b27-9957-63e4de482c0f.jpg)

## 3. STAR

Star Feature Detector có nguồn gốc từ máy dò CenSurE. Trong khi CenSurE sử dụng các đa giác (như hình vuông, hình lục giác và hình bát giác) như một sự thay thế dễ tính toán hơn cho hình tròn, thì Star sử dụng hình tròn tạo bởi 2 hình vuông chồng lên nhau: 1 thẳng đứng và 1 xoay 45 độ. Các đa giác này có thể được xem như là đa giác với đường viền dày. 

![STAR](https://user-images.githubusercontent.com/81065789/152470088-6c5517ee-3985-4ad0-ac74-c176c9b8fc3d.jpg)


<!-- Title -->
<h1 align="center"><b></b></h1>
<h1 align="center"><b>B/ Keypoints Descriptors</b></h1>

# I. Floating-point Descriptors:

## 1. SIFT (NON-FREE trước kia, nhưng available ở 2021)

Với Harris Keypoints Detector, hình ảnh xoay cho ra kết quả tập keypoints y hệt ảnh gốc ban đầu. Nhưng còn đối với phép Scaling thì sao? Ở tỉ lệ này có 1 góc nhưng ở tỷ lệ khác có còn là 1 góc nữa hay không? 

Ví dụ, hãy kiểm tra một hình ảnh đơn giản bên dưới:

![image](https://user-images.githubusercontent.com/81065789/148063926-e5373a0f-92ec-49f0-877e-c70016563d80.png)

Ảnh ban đầu cho ta một góc, nhưng ở tỉ lệ lớn hơn, nó lại có nhiều hơn là một góc. Đó là lí do SIFT Keypoints Detector ra đời (1999)!

Với SIFT, 2 hình với 2 tỉ lệ khác nhau sẽ cho ra 1 tập keypoints khác nhau:

917 keypoints: ![SIFTbig](https://user-images.githubusercontent.com/81065789/148076055-997693f2-80da-4c3d-9220-e764f42b8de8.jpg)

263 keypoints: ![SIFTsmall](https://user-images.githubusercontent.com/81065789/148076069-2b8f9c13-8924-404a-a8a8-c0077d65c433.jpg)

Chúng ta dùng SIFT để phát hiện và mô tả keypoints nhưng tốc độ của nó tương đối chậm vì sử dụng vector **128 chiều** cho bộ mô tả, người ta cần một phiên bản nâng cấp hơn, vì thế, vào năm 2006, SURF ra đời, cải thiện tốc độ cho SIFT.

(use Euclide distance to matching)

## 2. SURF (NON-FREE ở 2021)

SURF bổ sung rất nhiều tính năng để cải thiện tốc độ trong từng bước thực hiện, do đó nó nhanh hơn SIFT. Bộ mô tả của SURF sử dụng 1 vector **64 chiều** thay vì 128 như SIFT. Phân tích cho thấy nó nhanh hơn _3 lần_ so với SIFT trong khi hiệu suất tương đương với SIFT. SURF xử lý tốt hình ảnh bị mờ và xoay, nhưng không tốt trong việc xử lý thay đổi điểm nhìn và thay đổi độ sáng.

Vì nó không miễn phí trong OpenCV tại thời điểm 2021 nên tôi không thể đánh giá hiệu suất và trình bày kết quả ở đây được. :(

(use Euclide distance to matching)

# II. Binary Descriptors:

- Bộ mô tả nhị phân bao gồm 4 phần:

+ Sampling pattern (Lấy mẫu): chọn ra các pixel trong khu vực xung quanh keypoint, mỗi Binary Descriptor có một phương pháp để chọn ra các pixel này;

+ Sampling pairs (Ghép cặp): các pixel chọn được từ bước #1 sẽ được ghép thành N cặp để so sánh với nhau;

+ Orientation compensation: bước này nhằm đảm bảo rằng Binary Feature Vector không bị phụ thuộc vào góc quay của đối tượng trong Image Patch (rotation invariance);

+ Binary feature vector construction (Xây dựng vector đặc trưng): với N cặp pixel có được ở bước #2, chúng ta lần lượt so sánh cường độ của hai pixel v_1, v_2 trong từng cặp (hình 1), từ đó xây dựng vector đặc trưng N-dims (hình 2).

Hình 1: ![image](https://user-images.githubusercontent.com/81065789/152501837-5be3ab16-b32a-41dd-8a04-4a184e4a80f4.png)

Hình 2: ![Screenshot 2022-02-04 160749](https://user-images.githubusercontent.com/81065789/152502033-0be08f39-ba7c-4b23-ab07-d61e8f3271bc.png)

Khi Matching, việc so sánh giữa hai "Vector đặc trưng nhị phân" cũng rất đơn giản, chúng ta sẽ sử dụng Hamming Distance để tính toán sự khác biệt giữa hai vector (hình 3).

Hình 3: ![image](https://user-images.githubusercontent.com/81065789/152502129-7caf737f-76b2-4c77-942b-ead8f3455a48.png)

_Note: Image path (bản vá hình ảnh)_

![image](https://user-images.githubusercontent.com/81065789/152671918-a2f30787-b3a1-4893-a487-887a8ccc6cfb.png)

## 1. BRIEF

Như chúng ta đã biết, SIFT sử dụng số dấu phẩy động nên cần sử dụng vector 128 chiều cho bộ mô tả, tương đương 512 bytes. Tương tự, SURF cũng chiếm tối thiểu 256 bytes (cho vector 64 chiều). Việc tạo một vector như vậy cho hàng nghìn đặc trưng sẽ tốn rất nhiều bộ nhớ, điều này không khả thi đối với các ứng dụng hạn chế tài nguyên, đặc biệt là đối với các hệ thống nhúng. Bộ nhớ càng lớn thì thời gian matching càng lâu.

Chúng ta có thể sử dụng các phương pháp giảm chiều dữ liệu (PCA, LDA,...) hay các phương pháp băm LSH để chuyển đổi bộ mô tả SIFT từ dạng dấu phẩy động sang dạng chuỗi nhị phân. Các chuỗi nhị phân này được sử dụng để so khớp các đặc trưng bằng cách sử dụng khoảng cách Hamming, điều này giúp tăng tốc độ tốt hơn. Nhưng với cách này, trước tiên chúng ta cần tìm các bộ mô tả, sau đó áp dụng các phương pháp băm, và điều này không giải quyết được vấn đề ban đầu của chúng ta về bộ nhớ.

BRIEF đi vào hình ảnh ngay và luôn. Nó cung cấp một lối tắt để tìm các chuỗi nhị phân trực tiếp mà không cần tìm bộ mô tả. Nôm na, BRIEF hoạt động như sau:

_Giả sử đã xác định được K keypoints. Đầu tiên, thuật toán sẽ chọn ra i pixels trong khu vực xung quanh từng keypoint đã xác định. Tiếp theo, các chuỗi pixel của từng keypoint chọn được từ bước #1 sẽ được ghép thành N cặp để so sánh với nhau. Sau đó, giai đoạn bù định hướng sẽ diễn ra. Và rồi, với N cặp pixel có được ở bước #2, chúng ta lần lượt so sánh cường độ của hai pixel trong từng cặp (hình 1), từ đó xây dựng vector đặc trưng N-dims (hình 2)._

BRIEF descriptor thực hiện sampling pattern một cách ngẫu nhiên với 'Gaussian distribution', nghĩa là các pixel càng gần keypoint sẽ có xác suất được lựa chọn cao hơn các pixel ở xa. Từ các pixel này chúng ta chọn ra N cặp để so sánh và xây dựng Vector đặc trưng.

Một điểm quan trọng là BRIEF là một bộ mô tả đặc trưng, nó không cung cấp bất kỳ phương pháp nào để tìm các đặc trưng. Vì vậy, chúng ta sẽ phải sử dụng bất kỳ công cụ dò tính năng nào khác như SIFT, SURF, v.v. Các bài báo khuyến nghị sử dụng STAR (CenSurE) là một công cụ dò nhanh và BRIEF hoạt động tốt hơn một chút so với các cách khác.

Và không phải cái gì nhanh thì đều tốt :). BRIEF không cho độ chính xác cao bằng SIFT và SURF. Cần cân nhắc khi lựa chọn bộ mô tả đặc trưng phù hợp cho bối cảnh bài toán.

Theo tiêu chuẩn, vector đặc trưng của từng keypoint của ORB descriptor kích thước 256-dims, được lưu trữ ở 32 bytes.

(use Hamming distance to matching)

## 2. ORB

Thuật toán này được đưa ra bởi **Ethan Rublee, Vincent Rabaud, Kurt Konolige và Gary R. Bradski** trong bài báo của họ: "ORB: Một giải pháp thay thế hiệu quả cho SIFT hoặc SURF" vào năm 2011. Như tiêu đề đã nói, nó là một giải pháp thay thế tốt cho SIFT và SURF trong tính toán chi phí, hiệu suất phù hợp cho matching và chủ yếu là vấn đề bằng sáng chế, trong khi SIFT và SURF đã được cấp bằng sáng chế và bạn phải trả tiền cho họ để sử dụng nó. Nhưng ORB thì không!

ORB cũng sử dụng FAST để detect ra các keypoints sau đó dùng phép đo góc Harris để tìm N điểm cao nhất trong số đó và dùng BRIEF để mô tả các đặc trưng. Tuy nhiên, FAST và BRIEF được nhóm tác giả tinh chỉnh lại để khắc phục nhược điểm không bất biến với những thay đổi xoay của phiên bản nguyên bản.

ORB descriptor thực hiện sampling pattern với một thuật toán Machine Learning, chúng ta sẽ không đi sâu tìm hiểu về thuật toán này.

Với orientation angle, chúng ta có thể xoay các image patch về một góc đồng nhất, nhờ đó mà các image patch có góc quay khác nhau vẫn được xử lý tương tự nhau.

Bài báo cho biết ORB nhanh hơn nhiều so với SURF và SIFT và bộ mô tả ORB hoạt động tốt hơn SURF. ORB là một lựa chọn tốt trong các thiết bị tiêu thụ điện năng thấp để ghép ảnh panorama,... 

Theo tiêu chuẩn, vector đặc trưng của từng keypoint của ORB descriptor kích thước 256-dims, được lưu trong 32 bytes.

(use Hamming distance to matching)

## 3. BRISK

BRIEF hoặc ORB đều chọn các pixel xung quanh keypoint một cách ngẫu nhiên, nhưng với BRISK thì xác định trước. Các pixel được lấy mẫu nằm trên các vòng đồng tâm. Đối với mỗi điểm lấy mẫu (keypoint), một bản vá nhỏ được xem xét xung quanh nó. Trước khi bắt đầu thuật toán, bản vá được làm mịn bằng cách làm mịn Gaussian. Vòng tròn màu đỏ trong hình dưới minh họa kích thước của độ lệch chuẩn của bộ lọc Gaussian được áp dụng cho mỗi điểm lấy mẫu.

![brisk_pattern](https://user-images.githubusercontent.com/81065789/152670741-2b144001-700b-4649-8b9b-e7b1a9730e13.png)

Có hai loại cặp được sử dụng để lấy mẫu: cặp ngắn và cặp dài. Các cặp ngắn là những cặp có khoảng cách dưới ngưỡng 'max_distance' đã đặt trong khi các cặp dài có khoảng cách trên 'min_distance', với **min_distance > max_distance**. Các cặp dài được sử dụng để định hướng và các cặp ngắn được sử dụng để tính toán bộ mô tả bằng cách so sánh các cường độ.

Ở giai đoạn bù định hướng, thuật toán sẽ sử dụng đạo hàm cục bộ giữa các cặp dài để tính toán hướng. Điều này cho biết góc của các keypoints. Bây giờ, chúng ta chỉ cần xoay các cặp ngắn theo góc đó để giúp bộ mô tả trở nên bất biến hơn để xoay.

Một số nghiên cứu và thực nghiệm đã đưa ra nhận xét như sau:

* BRIEF vượt trội hơn BRISK (và ORB) trong các thay đổi về trắc quang - làm mờ, thay đổi độ sáng và nén JPEG.

* BRISK vượt trội hơn một chút so với BRIEF về thay đổi góc nhìn, nhưng về tổng thể thì hoạt động tương tự như ORB.

Theo tiêu chuẩn, vector đặc trưng của BRISK descriptor có kích thước 512-dims, được lưu trong 64 bytes.

(use Hamming distance to matching)

## 4. FREAK

FREAK descriptor rất đặc biệt vì nó mô phỏng cấu trúc tế bào của võng mạc để thực hiện sampling pattern.

![image](https://user-images.githubusercontent.com/81065789/152671970-cf7b95f0-5613-4f82-99b7-7a2b8a5f6b50.png)

Theo đó, các tế bào trên võng mạc được phân bố dày đặc hơn về phía trung tâm. FREAK descriptor thực hiện sampling pattern với mẫu hình sau:

![image](https://user-images.githubusercontent.com/81065789/152671978-f4c4ba2b-391e-4f76-be39-0a1529a64085.png)

Chúng ta có thể thấy rằng tại khu vực xung quanh keypoint, các đường tròn chồng lấn nhau được xây dựng và tập trung dày đặc hơn về phía trung tâm với kích thước nhỏ dần.

FREAK áp dụng một thuật toán Machine Learning (tương tự như ORB descriptor) để thực hiện sampling pairs và thu được 512 cặp pixels.

Để xác định góc định hướng của image patch, FREAK descriptor tính toán các giá trị dựa trên 45 cặp pixel đối xứng.

![image](https://user-images.githubusercontent.com/81065789/152672047-a1526774-728f-44b8-90aa-7d49633aa60f.png)

Sau cùng, cường độ của các pixel được so sánh và xây dựng thành Vector đặc trưng.

FREAK chỉ là một bộ mô tả đặc trưng, nên phải kết hợp với một bộ dò keypoints khác. Ở trong code phần này đã chọn STAR làm bộ dò điểm chính cho FREAK.

Theo tiêu chuẩn, vector đặc trưng của BRISK descriptor có kích thước 512-dims, được lưu trong 64 bytes.

(use Hamming distance to matching)

      Trong 4 bộ descriptor đã tìm hiểu ở trên thì FREAK cho kết quả tốt nhất, tiếp theo là BRISK và ORB (và BRIEF). Trên thực tế, chúng ta sẽ cần thử nghiệm trên từng descriptor để chọn ra descriptor phù hợp nhất bộ dữ liệu đang có.
      
Code thực hiện của từng thuật toán trình bày ở trên đều có phần Matching được commented ở sau cùng, và phần kết quả của các Keypoint detectors được lưu lại trong thư mục 'result', hình ảnh sử dụng trong code được lưu ở thư mục 'images'.

<h1 align="center"><b>--------</b></h1>

<!-- Footer -->
<p align='center'>Copyright © 2021 - Duong Hai Nguyen, Thanh Trung Nguyen</p>
