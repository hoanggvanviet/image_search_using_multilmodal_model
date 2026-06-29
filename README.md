# 🔍 Flickr30k Image Search với CLIP + FAISS

Hệ thống tìm kiếm ảnh ngữ nghĩa (semantic image search) sử dụng model CLIP để tạo embedding và FAISS để lưu trữ và tìm kiếm vector. Hỗ trợ tìm kiếm bằng cả **text** lẫn **ảnh**.

---

## 📁 Cấu trúc thư mục

```
project/
├── data/
│   └── flickr30k_images/        # Chứa ~30.000 ảnh .jpg
├── main.ipynb # Notebook chính
├── index.faiss                  # Vector index (tự sinh sau bước 3)
├── image_files.txt              # Danh sách đường dẫn ảnh (tự sinh sau bước 3)
└── README.md
└── requirements.md
```

---

## ⚙️ Cài đặt

**Yêu cầu:** Python 3.8+

```bash
pip install sentence-transformers faiss-cpu pillow matplotlib numpy
```

> Nếu có GPU, thay `faiss-cpu` bằng `faiss-gpu` để tăng tốc độ tìm kiếm.

---

## 📊 Dữ liệu

Tải bộ dữ liệu [Flickr30k](https://www.kaggle.com/datasets/hsankesara/flickr-image-dataset) và đặt toàn bộ ảnh `.jpg` vào thư mục `data/flickr30k_images/`.

---

## 🚀 Hướng dẫn chạy

Mở file `main.ipynb` trong PyCharm và chạy lần lượt các cell:

| Cell | Mô tả |
|------|-------|
| **Cell 0** | Import thư viện |
| **Cell 1** | Hiển thị 5 ảnh ngẫu nhiên từ dataset |
| **Cell 2** | Tạo embedding cho toàn bộ ảnh bằng CLIP (`clip-ViT-B-32`) |
| **Cell 3** | Xây dựng FAISS index và lưu ra file |
| **Cell 4** | *(Tùy chọn)* Load lại index đã lưu, bỏ qua bước 2 & 3 |
| **Cell 5** | Định nghĩa hàm `search_image()` |
| **Cell 6** | Định nghĩa hàm `visualize_results()` |
| **Cell 7** | Tìm kiếm bằng **text query** |
| **Cell 8** | Tìm kiếm bằng **image query** |

> **Lưu ý:** Cell 2 (tạo embedding) sẽ mất nhiều thời gian với 30K ảnh. Sau khi chạy xong một lần, dùng Cell 4 để load lại index thay vì tạo lại từ đầu.

---

## 💡 Cách sử dụng

**Tìm kiếm bằng text:**
```python
query = "a yellow balloon"
query, retrieved_image_files = search_image(query, model, index, image_files)
visualize_results(query, retrieved_image_files)
```

**Tìm kiếm bằng ảnh:**
```python
query_image_path = "data/flickr30k_images/1234567.jpg"
query, retrieved_image_files = search_image(query_image_path, model, index, image_files)
visualize_results(query, retrieved_image_files)
```

Tham số `top_k` trong `search_image()` điều chỉnh số lượng kết quả trả về (mặc định là 5).

---

## 🧠 Kiến trúc hệ thống

```
Text / Image Query
       │
       ▼
  CLIP (clip-ViT-B-32)          ← encode query thành vector 512 chiều
       │
       ▼
  FAISS IndexFlatIP              ← tìm kiếm Inner Product (cosine similarity)
       │
       ▼
  Top-K ảnh tương đồng nhất
```

- **CLIP** (`clip-ViT-B-32`): Model đa phương thức của OpenAI, encode cả text và ảnh vào cùng một không gian vector, cho phép so sánh trực tiếp giữa text và ảnh.
- **FAISS** (`IndexFlatIP`): Tìm kiếm vector theo Inner Product (tương đương cosine similarity với vector đã normalize), hiệu quả với tập dữ liệu lớn.

---

## 📦 Thư viện sử dụng

| Thư viện | Mục đích |
|----------|---------|
| `sentence-transformers` | Load và chạy model CLIP |
| `faiss-cpu` | Vector database, tìm kiếm similarity |
| `Pillow` | Đọc và xử lý ảnh |
| `numpy` | Xử lý mảng vector |
| `matplotlib` | Hiển thị kết quả |
