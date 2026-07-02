# Ứng dụng Dự đoán biến PD bằng Logistic Regression

## 1. Mô tả

Ứng dụng Streamlit tái hiện quy trình huấn luyện mô hình trong notebook `Untitled.ipynb`:

- **Thuật toán**: Logistic Regression (scikit-learn), bài toán **phân loại nhị phân**.
- **Biến mục tiêu (y)**: `PD` (giá trị 0/1).
- **Biến đầu vào (X)**: 24 biến khảo sát dạng thang điểm (Likert 1–5), gồm các nhóm:
  `TC1–TC5`, `NL1–NL4`, `DK1–DK5`, `V1–V6`, `TS1–TS4`.
- **Tiền xử lý**: notebook gốc không dùng scaler/encoder — các biến đầu vào là số nguyên
  được đưa thẳng vào mô hình, ứng dụng tái hiện đúng như vậy.
- **Chia tập dữ liệu**: `train_test_split` với `test_size` và `random_state` có thể chỉnh ở
  sidebar (mặc định lần lượt là `0.2` và `23`, đúng giá trị notebook đã dùng).
- **Siêu tham số Logistic Regression**: notebook dùng cấu hình mặc định của scikit-learn
  (`LogisticRegression()` không truyền tham số). Ứng dụng bổ sung các widget để người dùng
  tinh chỉnh `C`, `max_iter`, `penalty`, `solver` với giá trị mặc định là mặc định của
  scikit-learn (`C=1.0`, `penalty="l2"`, `solver="lbfgs"`, `max_iter=1000`) — đây là phần
  ứng dụng thêm để tăng tính tương tác, không có trong notebook gốc.

## 2. Cài đặt

```bash
pip install -r requirements.txt
```

## 3. Chạy ứng dụng

```bash
streamlit run app.py
```

Khuyến nghị dùng phiên bản Streamlit mới (>= 1.38) để tương thích tốt nhất với các thành
phần layout hiện đại (`st.container(height=...)`, `st.tabs`, v.v.).

## 4. Cấu trúc file dữ liệu đầu vào

File CSV tải lên cần chứa tối thiểu các cột sau (giống cấu trúc file mẫu `5c.csv`):

- 24 cột biến đầu vào: `TC1, TC2, TC3, TC4, TC5, NL1, NL2, NL3, NL4, DK1, DK2, DK3, DK4, DK5, V1, V2, V3, V4, V5, V6, TS1, TS2, TS3, TS4` (giá trị số nguyên, ví dụ thang điểm 1–5).
- Cột biến mục tiêu: `PD` (giá trị 0 hoặc 1).

Các cột khác trong file gốc (ví dụ `Dấu thời gian`, `NN`) không được dùng làm biến đầu vào
cho mô hình, vì notebook gốc không sử dụng các cột này khi huấn luyện.

## 5. Mô tả các tab

| Tab | Nội dung |
|---|---|
| **Tổng quan dữ liệu** | Kích thước dữ liệu (số dòng, số cột, dung lượng file), xem dữ liệu thô, thống kê mô tả cho các biến X và y. |
| **Trực quan hóa dữ liệu** | Biểu đồ phân phối biến mục tiêu `PD`, kèm biểu đồ cho các biến đầu vào do người dùng chọn (mặc định 3 biến đầu tiên) do có nhiều hơn 4 biến đầu vào. |
| **Kết quả huấn luyện & kiểm định mô hình** | Accuracy, Precision, Recall, F1-score, ROC-AUC, ma trận nhầm lẫn, đường cong ROC và classification report chi tiết trên tập kiểm tra. Chỉ hiển thị sau khi bấm nút huấn luyện ở sidebar. |
| **Sử dụng mô hình** | Hai chế độ: (1) Nhập trực tiếp giá trị từng biến qua form để dự báo một trường hợp; (2) Tải lên file CSV có đúng cấu trúc 24 biến đầu vào để dự báo hàng loạt và tải kết quả về dưới dạng CSV. |

## 6. Ghi chú

- Mô hình chỉ được huấn luyện lại khi người dùng bấm nút **"🚀 Huấn luyện mô hình"** ở
  sidebar. Kết quả huấn luyện được lưu trong `st.session_state` để các tab khác dùng lại mà
  không cần huấn luyện lại khi chuyển tab.
- Vì tập biến đầu vào là thang điểm rời rạc (1–5) với ít giá trị duy nhất, ứng dụng tự động
  chọn biểu đồ dạng cột (bar theo `value_counts`) thay vì histogram liên tục.
- Do notebook gốc không xác thực mô hình trên nhãn thực cho phần "sử dụng mô hình" mới
  (dữ liệu chưa có nhãn), ứng dụng không tự tính lại các chỉ số kiểm định cho dữ liệu mới,
  chỉ đưa ra kết quả dự đoán và xác suất theo lớp.
