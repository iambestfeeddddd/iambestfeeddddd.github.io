---
title: "ViT_vs_CNN"
date: 2023-03-10T00:00-00:00
last_modified_at: 2023-03-10T00:00-00:00
categories:
  - learning
  - deep learning
permalink: /benchmark_i2i_problem/
classes: wide
excerpt: Image-to-image problem
---
# Image-to-image translation:
I2I là 1 task trong CV khi người ta cố dạy cho máy tính biết cách biến đổi từ ảnh đầu vào để được 1 ảnh đầu ra đảm bảo những ràng được quy định (các ràng buộc này có thể là text, là style của ảnh, ...). 2 yếu tố quyết định chính của bài toán này là **ảnh đầu vào** và **ràng buộc**. 

Bạn có thể tìm thấy một số cách đánh giá cho bài toán này nhưng hiện tại thì các phương pháp feature base như **IS, FID** đang được tin dùng. Khái quát thì 2 metric này đều dựa trên CNN base nhưng có 1 nhược điểm là chúng đều bị bias vào tập ImageNet (do các model sử dụng trong 2 pp này đều được train trên này). Với mình đây là 1 vài thiếu xót lớn như:
1. Vì image-to-image đã mở rộng sang 1 bài toán trên rất nhiều class và việc bị giới hạn trong 1 tập dataset chỉ 1000 class đã khiến cho việc extract feature trở nên bị yếu đi. 
2. Tính chất của CNN rất hiệu quả trong việc học được các đặc trưng toàn cục. Điều này sẽ rất hiệu quả trong việc giúp CNN phân loại và extract các đặc trưng toàn của của cả 1 class. Từ đó phân loại và tính toán được khoảng cách giữa 2 ảnh được thay đổi về class. 
3. Nhưng với những case mà ảnh bị thay đổi ở điểm nhỏ và ngoại cảnh thì việc extract feature như này còn hiệu quả không? Mình tin là không!
4. Nhìn lại 1 vòng các mạng liên quan đến chủ đề I2I thì yeah, chúng ta có 1 ứng cử viên rất mạnh là ViT. 1 mạng khai thác feature của ảnh nhưng theo cách rất khác. Mục tiêu của ViT là để tiếp cận những đặc trưng cục bộ của ảnh. Điều này đã được chứng minh là giúp ViT mạnh hơn trong các bài toán classification khó (khi cần phân loại các đối tượng trong cùng class - đặc trưng được dùng để phân loại là đặc trưng cục bộ).
# Một số lập luận:
     Vậy tại sao chúng ta cần lại quan tâm đến đặc trưng cục bộ?
Mình có 1 số lý do như sau:
1. Quan sát lại paper pix2pix ta dễ dàng nhận ra ở module Discriminartor tác giả sử dụng PatchGAN. Một cơ chế tính loss trên từng vùng thay vì tính tổng thể. Và vâng, cơ chế này rất giống ViT.
2. Như ở bài toán phân loại mình đã nói ở trên, đâu là điểm quyết định sự khác nhau của mỗi đối tượng trong cùng 1 class chung là chó. Vâng, nó là đặc trưng cục bộ như màu sắc, hình thái, ... Một cách dễ dàng để hình dung thì những đặc trưng cục bộ này có thể coi như Sytle còn những đặc trưng toàn cục nói về 1 đối tượng là Coor. 
3. Dễ thấy thì CNN sẽ không fit được với nhu cầu tìm kiếm và trích xuất đặc trưng cục bộ này gòi. 
# Chúng ta cần gì ở một thang đo cho bài toán Image-to-image:
  Nhìn vào định nghĩa bài toán thì mình có thể khẳng định ngay đây là 1 bài toán chúng ta cần phải có 1 độ đo quan tâm đến: **input image, yêu cầu, output image**. Tức là chúng ta cần phải tính được khoảng cách của 2 ảnh trên những yếu tố được cung cấp từ yêu cầu. Và mình nghĩ đến việc benchmark bằng CLIP - một model kết nối image-text và dựa vào ý tưởng **embed information from image and text to embedding space**. Với mình thì ý tưởng này khá giống với ý tưởng của Stable Diffution khi cố gắng ép về latent space nhưng có thêm text encoder. 
# Điều gì còn bỏ ngỏ:
    - CLIP là 1 model mạnh nhưng không ai chứng minh nó là thứ toàn diện (tốt tuyệt đối). Vậy? làm sao để chứng minh việc benchmark này tốt? 
    - Làm sao để ta biết liệu có phải CLIP đã tối ưu hay do chúng ta đang lấy số lượng áp đảo chất lượng?
    - Liệu có 1 cách nào đó để ta đạt tới trạng thái embedding cả image và text nơi mà thông tin được cô đọng như 1 latent space cho cả ảnh và text?
  

