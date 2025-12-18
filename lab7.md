BÁO CÁO THỰC HÀNH: PHÂN TÍCH CÚ PHÁP PHỤ THUỘC (DEPENDENCY PARSING)
Môn học: Xử lý ngôn ngữ tự nhiên (NLP) Công cụ sử dụng: Thư viện spaCy, Python Nội dung: Thực hiện các bài tập tự luyện về trích xuất thông tin dựa trên cây cú pháp phụ thuộc.

1. Mục tiêu và Chuẩn bị
Mục tiêu của bài thực hành là vận dụng kiến thức về Dependency Parsing để giải quyết các bài toán trích xuất thành phần câu tự động mà không cần viết luật thủ công phức tạp.

Cài đặt môi trường:

Cài đặt thư viện: pip install -U spacy.

Tải mô hình ngôn ngữ tiếng Anh: python -m spacy download en_core_web_md.

2. Nội dung thực hiện
Bài 1: Tìm động từ chính của câu (Main Verb)
Yêu cầu: Viết hàm find_main_verb(doc) để tìm token đóng vai trò là gốc (ROOT) của cây cú pháp.

Giải thuật:

Duyệt qua từng token trong văn bản.

Kiểm tra thuộc tính token.dep_. Nếu giá trị là "ROOT", đó chính là động từ chính của câu.

Kết quả thực nghiệm:

Câu đầu vào: "The boy is running in the park."

Code triển khai:

Python

def find_main_verb(doc):
    for token in doc:
        if token.dep_ == "ROOT":
            return token
    return None
Kết quả: running.

Nhận xét: Hàm đã xác định chính xác "running" là động từ chính điều khiển toàn bộ câu.

Bài 2: Trích xuất các cụm danh từ (Noun Chunks)
Yêu cầu: Tự viết hàm trích xuất cụm danh từ (thay vì dùng doc.noun_chunks có sẵn) bằng cách kết hợp danh từ với các từ bổ nghĩa cho nó.

Giải thuật:

Viết hàm phụ extract_noun_chunk(token): Với một token danh từ, tìm các từ con (children) có quan hệ phụ thuộc là det (mạo từ), amod (tính từ bổ nghĩa), hoặc compound (danh từ ghép) để ghép thành cụm.

Viết hàm chính custom_noun_chunks(doc): Duyệt qua văn bản, nếu gặp token có từ loại NOUN, gọi hàm phụ để trích xuất cụm tương ứng.

Kết quả thực nghiệm:

Câu đầu vào: "The big fluffy cat chased a small mouse."

Code triển khai:

Python

def extract_noun_chunk(token):
    modifiers = [child.text for child in token.children if child.dep_ in ("det", "amod", "compound")]
    return " ".join(modifiers + [token.text]) # Giả lập logic join

def custom_noun_chunks(doc):
    chunks = []
    for token in doc:
        if token.pos_ == "NOUN":
            chunks.append(extract_noun_chunk(token))
    return chunks
Kết quả:

'The big fluffy cat' (Danh từ chính: cat)

'a small mouse' (Danh từ chính: mouse)

Nguồn:

Bài 3: Tìm đường đi ngắn nhất tới gốc (Path to Root)
Yêu cầu: Viết hàm get_path_to_root(token) để truy vết từ một từ bất kỳ lên đến đỉnh của cây cú pháp.

Giải thuật:

Bắt đầu từ token đầu vào.

Sử dụng vòng lặp while để di chuyển ngược lên token cha (token.head) chừng nào quan hệ phụ thuộc chưa phải là "ROOT".

Lưu lại các token trên đường đi.

Code triển khai:

Python

def get_path_to_root(token):
    path = [token]
    while token.dep_ != "ROOT":
        token = token.head
        path.append(token)
    return path
(Dựa trên logic code được thể hiện trong file Lap7.pdf)

3. Kết luận
Qua bài thực hành, chúng ta đã thấy được sức mạnh của việc sử dụng cây cú pháp phụ thuộc:

Dễ dàng xác định thành phần cốt lõi của câu (Main Verb).

Trích xuất thông tin phức tạp (Noun Chunks) dựa trên mối quan hệ ngữ pháp thay vì chỉ dựa vào vị trí từ.

Hiểu được cấu trúc phân cấp của câu thông qua việc truy vết đường đi tới Root.