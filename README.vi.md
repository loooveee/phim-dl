# phim-dl

[English](README.md) · **Tiếng Việt**

> [!WARNING]
> **Dự án chỉ phục vụ mục đích học tập.** Dự án cho thấy cách HLS streaming, giải mã luồng phát và
> tải song song hoạt động. Không dùng để đăng lại, chia sẻ hay bán phim.

Tải phim lẻ và phim bộ từ **phimkey.com** và các trang clone (**phimsea.com**, **phimfix.com**,
**xemphim.in**, ...) về máy dưới dạng file **MP4**, kèm phụ đề.

- Dán link là tải: phim lẻ ra một file, phim bộ tải **toàn bộ các tập của mọi mùa**
- Giữ nguyên chất lượng gốc (thường là 1080p, có âm thanh 5.1 nếu phim có), không nén lại
- **Phụ đề** lưu cạnh video (`.vi.srt`, `.en.srt`, ... mỗi ngôn ngữ lấy bản tốt nhất)
- Xem **chất lượng, dung lượng và bitrate** trên từng server trước khi tải (`--info`)
- Tải nhanh bằng **aria2c** (không có thì dùng bộ tải có sẵn)
- Đoạn nào tải hỏng thì tự tải lại; server nào lỗi thì tự chuyển sang server khác
- Thanh tiến độ trực tiếp: mỗi tập một thanh, thêm một dòng tổng, có tốc độ và thời gian còn lại
- Tắt giữa chừng thì chạy lại đúng lệnh cũ để tải tiếp

Các trang này chỉ xem được khi có tài khoản, nên bạn cần một tài khoản để dùng phim-dl. Mỗi trang có
tài khoản riêng: hãy dùng tài khoản của đúng trang trong link.

## Cài đặt

1. Cài [Python 3.10 trở lên](https://www.python.org/downloads/)
   (trên Windows, tích ô **Add python.exe to PATH** ở màn hình đầu tiên của bộ cài)
2. Tải dự án này: **Code** → **Download ZIP**, rồi giải nén
3. Mở cửa sổ lệnh trong thư mục vừa giải nén (Windows: bấm vào thanh địa chỉ của thư mục,
   gõ `cmd` rồi Enter), sau đó chạy:
   ```bash
   python -m pip install -r requirements.txt
   ```
4. Đặt `aria2c.exe` cạnh file `phim-dl.py` (nên làm, tải nhanh hơn)
   - aria2c: https://github.com/aria2/aria2/releases (file zip `win-64bit`, lấy `aria2c.exe` ra)
   - ffmpeg đã được cài ở bước 3. Bạn cũng có thể đặt `ffmpeg.exe` của mình cạnh script.

## Đăng nhập

Chỉ cần đăng nhập một lần; phiên đăng nhập được lưu trong `session.txt` cạnh script:

```bash
python phim-dl.py --login
```

Lệnh sẽ hỏi email và mật khẩu. Mật khẩu chỉ được gửi tới trang phim và không bao giờ được lưu lại.

Lệnh trên đăng nhập phimkey.com. Với trang clone thì thêm `--site`. Có thể đăng nhập nhiều trang
cùng lúc; mỗi link sẽ dùng phiên của đúng tên miền đó:

```bash
python phim-dl.py --login --site phimsea.com
python phim-dl.py --site phimsea.com          # xem đang đăng nhập tài khoản nào ở trang đó
```

Trang chỉ cho một tài khoản đăng nhập ở **một nơi cùng lúc**, nên đăng nhập ở đây có thể làm bạn
bị đăng xuất trên trình duyệt (và ngược lại). Muốn dùng chung phiên với trình duyệt thì xuất cookie
của trang phim ra file `cookies.txt` bằng một tiện ích trình duyệt (ví dụ "Get cookies.txt LOCALLY")
rồi chạy:

```bash
python phim-dl.py --cookies cookies.txt                     # phimkey.com
python phim-dl.py --cookies cookies.txt --site phimsea.com  # trang clone
```

Giữ kín `session.txt` và `cookies.txt`: ai có chúng đều dùng được tài khoản của bạn.
`python phim-dl.py --logout` (thêm `--site` với trang clone) sẽ xóa phiên đã lưu của trang đó.

## Cách dùng

```bash
python phim-dl.py "https://phimkey.com/title/the-odyssey~Vk8k"
```

| Lệnh | Tác dụng |
|---|---|
| `python phim-dl.py <link>` | Tải phim lẻ, hoặc toàn bộ tập của phim bộ |
| `python phim-dl.py "https://phimsea.com/title/..."` | Link của các trang clone dùng y hệt |
| `python phim-dl.py <link> --info` | Chỉ xem chất lượng, dung lượng và bitrate trên từng server |
| `python phim-dl.py <link> -S 2` | Chỉ tải mùa 2 của phim bộ (`1-2` = mùa 1 và 2) |
| `python phim-dl.py <link> -e 1-5,8` | Chỉ tải tập 1 đến 5 và tập 8 của mỗi mùa (`10-` = từ tập 10 tới hết) |
| `python phim-dl.py <link> --list` | Chỉ liệt kê các mùa và tập |
| `python phim-dl.py <link> --subs all` | Phụ đề mọi ngôn ngữ (mặc định `vi,en`, `none` = không tải phụ đề) |
| `python phim-dl.py <link> -s 2` | Ưu tiên server 2 (mặc định server 1) |
| `python phim-dl.py <link> -o D:\Phim` | Lưu vào thư mục khác |
| `python phim-dl.py <link> -j 3` | Tải 3 tập cùng lúc (mặc định 2) |
| `python phim-dl.py <link> --lang vi` | Hiện thông báo bằng tiếng Việt |

Dùng được cả link trang phim (`/title/...`) lẫn link trang xem (`/watch/...`);
link trang xem của một tập thì chỉ tải đúng tập đó.

File được lưu trong `downloads/<tên phim> (<năm>)/`, đặt tên kiểu `Ten.Phim.2026.mp4` với phim lẻ
và `Ten.Phim.S01E01.mp4` với một tập phim bộ. Phụ đề có cùng tên kèm ngôn ngữ:
`Ten.Phim.2026.vi.srt`, nên trình phát video tự nhận.

Xem trước khi tải:

```
  Server   Chất lượng          Dung lượng      Bitrate     Thời lượng  Máy chủ
  ────────────────────────────────────────────────────────────────────────────────
  1        1080p (1920x1080)   ~4.8 GB         3.9 Mbps    2:52:35     t1.kingcdn.xyz  ← mặc định dùng server này
  2        1080p (1920x1080)   ~4.5 GB         3.7 Mbps    2:52:35     f3.kingcdn.xyz
  3        1080p (1920x1080)   ~4.5 GB         3.7 Mbps    2:52:35     f3.kingcdn.xyz
```

Trong lúc tải:

```
  ⠹ S01E01     ━━━━━━━━━━━━━━━━━╸━━━━━━━━━━━━    58.3%    392.9 MB / 673.3 MB     17.4 MB/s   ETA   00:16
  ⠼ S01E02     ━━━━━━╸━━━━━━━━━━━━━━━━━━━━━━━    21.0%    141.1 MB / 674.2 MB     17.1 MB/s   ETA   00:31
  ──────────────────────────────────────────────────────────────────────────────────────────────────────
  Σ Tổng 1/4   ━━━━━━━━━━━━━╸━━━━━━━━━━━━━━━━    44.8%      1.2 GB / 2.7 GB       34.4 MB/s   ETA   00:44
```

Thông báo mặc định bằng **tiếng Anh** (tự chuyển tiếng Việt nếu máy bạn đặt ngôn ngữ tiếng Việt).
Dùng `--lang vi` hoặc `--lang en` để chọn. Dùng `--no-color` (hoặc đặt `NO_COLOR=1`) để in chữ trơn.

Chạy `python phim-dl.py -h` để xem tất cả tùy chọn.

## Tải lỗi

Tập nào không tải được sẽ hiện màu đỏ kèm lý do, và được ghi vào **`failed.txt`** trong thư mục phim
(tập, link và lý do). Chạy lại đúng lệnh cũ để thử lại: tập đã xong sẽ được bỏ qua, tập dở dang tải
tiếp từ chỗ đã dừng. `failed.txt` tự xóa khi không còn tập nào lỗi.

## Gặp lỗi?

- **`python` is not recognized:** cài lại Python và tích ô **Add python.exe to PATH**
- **"Thiếu thư viện":** chạy lại `python -m pip install -r requirements.txt`
- **"Bạn chưa đăng nhập" / "Phiên đăng nhập đã hết hạn":** chạy lại `python phim-dl.py --login`
  (hoặc xuất lại cookie). Lỗi này cũng xảy ra khi bạn vừa đăng nhập ở nơi khác.
- **"Tài khoản này hiện không xem phim được":** kiểm tra gói tài khoản trên trang phim
- **Tải chậm:** đặt `aria2c.exe` cạnh `phim-dl.py`. Máy chủ video chậm trên mỗi kết nối, nên script tải
  nhiều đoạn cùng lúc (32 đoạn với aria2c). Có thể thử `-n 48`, đổi server (`-s 2`), hoặc `-j 3` với
  phim bộ. Tốc độ mạng của bạn là giới hạn cao nhất
- **Ctrl+C:** dừng ngay. Chạy lại đúng lệnh cũ để tải tiếp

## Đóng góp

Rất hoan nghênh báo lỗi và pull request. Những thay đổi nhằm vượt captcha, tường phí hay các lớp
bảo vệ khác sẽ không được nhận.

- **Báo lỗi:** mở một [issue](https://github.com/loooveee/phim-dl/issues) kèm lệnh bạn đã chạy,
  nội dung lỗi (hoặc `failed.txt`), hệ điều hành và `python --version`. Đừng bao giờ dán
  `session.txt`, cookie hay mật khẩu.
- **Gửi bản sửa:** fork repo, tạo nhánh mới, commit, push lên bản fork, rồi mở pull request.
  Ghi rõ bạn đã sửa gì, vì sao, và đã kiểm tra thế nào.
- **Quy tắc:**
  - Giữ mọi thứ trong `phim-dl.py`. Docstring ở đầu file có sơ đồ code.
  - Viết mọi thông báo bằng cả hai ngôn ngữ: `t("English", "Tiếng Việt")`.
  - Dùng các hàm màu (`green()`, `red()`...) thay cho mã ANSI thô.

## Miễn trừ trách nhiệm

**Dự án chỉ phục vụ mục đích học tập.** Bạn tự chịu trách nhiệm về cách sử dụng.
Hãy tôn trọng bản quyền và điều khoản sử dụng của trang web.

Dự án không liên quan tới phimkey.com hay các trang clone của nó. Tác giả không chịu trách nhiệm cho bất kỳ hành vi lạm dụng nào.

## Giấy phép

MIT © loooveee
