# NF-UQ NIDS v3 - CSV to Parquet Merge

Tự động chuyển đổi file CSV lớn sang định dạng Parquet, merge dữ liệu, và xử lý theo batch cho môi trường Kaggle.

## 📊 Mô tả

Repository này cung cấp giải pháp tối ưu hóa bộ nhớ cho các dataset lớn trên Kaggle:

- **4 Datasets NIDS:**
  - NF-UNSW-NB15-v3 (CSV)
  - NF-ToN-IoT-v3 (CSV)
  - NF-CICIDS2018-v3 (CSV)
  - NF-BoT-IoT-v3 (CSV)

## 🎯 Tính năng

✅ **Chuyển đổi CSV → Parquet**
- Đọc từng batch 100K dòng để tiết kiệm RAM
- Nén Snappy (giảm ~60-70% dung lượng)
- Theo dõi RAM real-time

✅ **Merge Datasets**
- Gộp các chunks thành 1 file/dataset
- Tối ưu hóa bộ nhớ với garbage collection
- Output cuối < 19.2GB

✅ **Batch Processing**
- Load dữ liệu theo batch
- Hỗ trợ single dataset hoặc all datasets kết hợp
- Memory efficient

## ⚙️ Constraints Kaggle

| Tài nguyên | Giới hạn | Status |
|-----------|---------|--------|
| RAM | 30GB | ✅ Supported |
| Output | 19.2GB | ✅ Optimized |
| Batch Size | 100K rows | ⚙️ Configurable |
| Compression | Snappy | ⚙️ Adjustable |

## 🚀 Cách sử dụng

### 1. Import vào Kaggle Notebook

```bash
# Sao chép file .ipynb vào Kaggle
# Hoặc nhập từ GitHub:
```

### 2. Chuẩn bị Datasets

Link 4 datasets trong Kaggle Input:
```
/kaggle/input/datasets/seyhed/nf-unsw-nb15-v3/NF-UNSW-NB15-v3.csv
/kaggle/input/datasets/seyhed/nf-ton-iot-v3/NF-ToN-IoT-v3.csv
/kaggle/input/datasets/seyhed/nf-cicids2018-v3/NF-CICIDS2018-v3.csv
/kaggle/input/datasets/seyhed/nf-bot-iot-v3/NF-BoT-IoT-v3.csv
```

### 3. Chạy Notebook

**Cell 1: Chuyển đổi & Merge**
```python
# Tự động convert CSV → Parquet
# Merge từng dataset
# Output: /kaggle/working/merged_data/
```

**Cell 2: Single Dataset Processing**
```python
for batch_df in load_data_in_batches(parquet_path):
    # Xử lý batch_df
    pass
```

**Cell 3: All Datasets Combined**
```python
for batch_df in load_all_datasets_in_batches(merged_files):
    # Xử lý dữ liệu từ tất cả datasets
    pass
```

**Cell 4: Summary**
```python
# Kiểm tra dung lượng và memory usage
```

## 📁 Cấu trúc Output

```
/kaggle/working/
└── merged_data/
    ├── nf-unsw-nb15-v3_merged.parquet
    ├── nf-ton-iot-v3_merged.parquet
    ├── nf-cicids2018-v3_merged.parquet
    └── nf-bot-iot-v3_merged.parquet
```

## 💡 Code Examples

### Load Single File
```python
df = pd.read_parquet('/kaggle/working/merged_data/nf-unsw-nb15-v3_merged.parquet')
```

### Process in Batches
```python
def load_data_in_batches(parquet_path, batch_size=100000):
    df = pd.read_parquet(parquet_path)
    for i in range(0, len(df), batch_size):
        yield df.iloc[i:i+batch_size].reset_index(drop=True)

for batch in load_data_in_batches(path):
    # Your processing logic
    pass
```

### Monitor Memory
```python
from psutil import Process
process = Process(os.getpid())
memory_gb = process.memory_info().rss / (1024 ** 3)
print(f"Memory usage: {memory_gb:.2f}GB")
```

## 📊 Performance

| Metric | Value |
|--------|-------|
| **Compression Ratio** | ~60-70% |
| **Batch Processing** | 100K rows/batch |
| **Max Memory** | < 30GB |
| **Output Size** | < 19.2GB |
| **I/O Speed** | Fast (Parquet optimized) |

## 🔧 Optimization Tips

1. **Adjust Batch Size** - Tùy theo yêu cầu computation:
   - Heavy computation: 50K rows
   - I/O operations: 200K rows
   - Default: 100K rows

2. **Monitor Memory** - Sử dụng `get_memory_usage()` function

3. **Garbage Collection** - Auto cleanup sau mỗi batch

4. **Compression** - Snappy cân bằng tốc độ vs nén

## 📝 Notebook Content

`convert_to_parquet_batch.ipynb`:
- Cell 1: CSV → Parquet conversion + Merge
- Cell 2: Single dataset batch processing
- Cell 3: All datasets combined batch processing
- Cell 4: Summary & usage statistics

## 🛠️ Requirements

```python
pandas>=1.0.0
pyarrow>=10.0.0
psutil>=5.0.0
```

## 💾 Output Sizes (Estimated)

Dựa trên dữ liệu gốc:
- NF-UNSW-NB15-v3: ~3-4GB (parquet)
- NF-ToN-IoT-v3: ~2-3GB (parquet)
- NF-CICIDS2018-v3: ~4-5GB (parquet)
- NF-BoT-IoT-v3: ~2-3GB (parquet)

**Total: ~11-15GB** (dưới 19.2GB limit)

## ⚡ Quick Start

```bash
# 1. Copy notebook vào Kaggle
# 2. Link 4 datasets trong Input
# 3. Run Cell 1 (chuyển đổi)
# 4. Run Cell 2/3 (xử lý)
# 5. Check summary (Cell 4)
```

## 🔗 Resources

- [Kaggle Datasets](https://kaggle.com/datasets)
- [Parquet Format](https://parquet.apache.org/)
- [PyArrow Documentation](https://arrow.apache.org/docs/python/)
- [Pandas Documentation](https://pandas.pydata.org/)

## 📄 License

MIT

## 👨‍💻 Author

- **User**: @nnhuwz03
- **Repository**: [nf-uq-nids-v3-merge](https://github.com/nnhuwz03/nf-uq-nids-v3-merge)

## ❓ FAQ

**Q: Tại sao dùng Parquet thay vì CSV?**
- A: Parquet nén tốt hơn (60-70% nhỏ hơn), đọc nhanh hơn, hỗ trợ schema

**Q: Batch size bao nhiêu là tốt?**
- A: 100K rows là mặc định tốt, điều chỉnh tuỳ vào memory available

**Q: Có thể xử lý all datasets cùng lúc không?**
- A: Có, sử dụng `load_all_datasets_in_batches()` function

**Q: Kích thước output cuối cùng?**
- A: ~11-15GB (dưới 19.2GB limit của Kaggle)

---

**Last Updated**: 2026-05-06
