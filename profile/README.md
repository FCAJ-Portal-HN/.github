<div align="center">

# First Cloud AI Journey — Hanoi

**FCAJ University Portal** — nền tảng hợp nhất cho chương trình FCAJ: quản lý sinh viên, đặt chỗ làm việc, check-in tại toà nhà, tuyển dụng, phát đồng phục và bảng xếp hạng thời gian thực.

[![Portal](https://img.shields.io/badge/Portal-awsfcaj.com-232F3E?style=flat-square&logo=amazonaws&logoColor=FF9900)](https://awsfcaj.com)
![Node](https://img.shields.io/badge/Node.js-20%2B-339933?style=flat-square&logo=nodedotjs&logoColor=white)
![React](https://img.shields.io/badge/React-Vite-61DAFB?style=flat-square&logo=react&logoColor=black)
![Postgres](https://img.shields.io/badge/PostgreSQL-16-4169E1?style=flat-square&logo=postgresql&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=flat-square&logo=docker&logoColor=white)

</div>

---

## Chúng tôi làm gì

FCAJ University Portal số hoá hoạt động thường ngày của chương trình đào tạo FCAJ. Năm ứng dụng frontend chuyên biệt cùng làm việc với một backend duy nhất nắm toàn bộ business logic, dữ liệu và điều phối real-time.

| | Năng lực |
| :--- | :--- |
| **Đặt chỗ linh hoạt** | Sinh viên giữ chỗ theo ngày, tầng và ca làm việc, có giới hạn sức chứa từng tầng. |
| **Tự động phê duyệt theo rule** | Quản trị viên định nghĩa rule để hệ thống tự duyệt hoặc từ chối yêu cầu đặt chỗ. |
| **Điểm danh tại toà nhà** | Ứng dụng trạm gọn nhẹ cho nhân sự toà nhà check-in / check-out sinh viên. |
| **Gamification** | Điểm tích luỹ từ việc lên office và tham gia thuyết trình, đẩy trực tiếp lên leaderboard. |
| **Luồng tuyển dụng** | HR đăng tin, sinh viên gửi CV, reviewer đánh giá hồ sơ trực tuyến. |
| **Hậu cần đồng phục** | Sinh viên đặt đồng phục, thanh toán qua VietQR và tải lên biên lai chuyển khoản. |

---

## Kiến trúc

Hệ thống theo mô hình client-server. Request/response đi qua REST trên HTTP; các thay đổi trạng thái cần đến client ngay lập tức được broadcast qua WebSocket (Socket.IO).

```mermaid
graph TD
    subgraph Frontends [Frontend - React, Vite, Tailwind CSS]
        Admin["Admin Portal"]
        Client["Student Portal"]
        Checkin["Check-in Station"]
        Leaderboard["Live Leaderboard"]
        Uniform["Uniform Ordering"]
    end

    subgraph Backend [REST API and WebSocket server]
        API["FCAJ-Uni-Backend - Express.js"]
        SocketServer["Socket.IO server"]
        AutoApprove["Auto-approval engine"]
        CronService["node-cron scheduler"]
    end

    subgraph Infra [Infrastructure and external services]
        DB[("PostgreSQL via Prisma ORM")]
        S3["AWS S3 - CVs, receipts"]
        SMTP["SMTP mail service"]
    end

    Admin -->|REST and WebSocket| API
    Client -->|REST and WebSocket| API
    Checkin -->|REST and WebSocket| API
    Leaderboard -->|REST and WebSocket| API
    Uniform -->|REST and WebSocket| API

    API <--> SocketServer
    API --> AutoApprove
    API --> CronService

    API <-->|Prisma Client| DB
    API <-->|AWS SDK v3| S3
    API -->|Nodemailer| SMTP

    SocketServer -.->|Notifications| Client
    SocketServer -.->|Attendance updates| Checkin
    SocketServer -.->|Point updates| Leaderboard
```

---

## Repositories

Toàn bộ repo của tổ chức đều ở chế độ private. Mỗi ứng dụng là một Git submodule của repo orchestration `FCAJ-Portal`.

| Repository | Vai trò | Stack |
| :--- | :--- | :--- |
| [`FCAJ-Portal`](https://github.com/FCAJ-Portal-HN/FCAJ-Portal) | Monorepo gốc: submodules, compose files, script vận hành | Shell, PowerShell, Docker |
| [`FCAJ-Uni-Backend`](https://github.com/FCAJ-Portal-HN/FCAJ-Uni-Backend) | REST API, WebSocket, auto-approval, mail, storage | Node.js, Express, Prisma, Socket.IO |
| [`FCAJ-Uni-Admin`](https://github.com/FCAJ-Portal-HN/FCAJ-Uni-Admin) | Console vận hành cho super admin, HR, reviewer | React, Vite, Recharts, FullCalendar |
| [`FCAJ-Uni-Client`](https://github.com/FCAJ-Portal-HN/FCAJ-Uni-Client) | Cổng sinh viên: đăng ký, đặt chỗ, CV, hồ sơ | React, Vite, i18n |
| [`FCAJ-Uni-Checkin`](https://github.com/FCAJ-Portal-HN/FCAJ-Uni-Checkin) | Trạm check-in cho nhân sự toà nhà | React, Vite |
| [`FCAJ-Uni-Leaderboard`](https://github.com/FCAJ-Portal-HN/FCAJ-Uni-Leaderboard) | Bảng xếp hạng real-time | React, Vite, Socket.IO |
| [`FCAJ-Uni-Uniform`](https://github.com/FCAJ-Portal-HN/FCAJ-Uni-Uniform) | Đặt đồng phục và đối chiếu thanh toán VietQR | React, Vite |
| [`FCAJ-Uni-Docs`](https://github.com/FCAJ-Portal-HN/FCAJ-Uni-Docs) | Tài liệu sản phẩm và vận hành | HTML, Markdown |

---

## Bắt đầu

```bash
git clone --recurse-submodules https://github.com/FCAJ-Portal-HN/FCAJ-Portal.git
cd FCAJ-Portal
chmod +x run-projects.sh stop-projects.sh fcaj-dev-db.sh
docker compose -f docker-compose.dev-db.yml up -d
./run-projects.sh
```

Hướng dẫn đầy đủ về biến môi trường, khởi tạo database, port phát triển và triển khai production nằm trong `README.md` của repo [`FCAJ-Portal`](https://github.com/FCAJ-Portal-HN/FCAJ-Portal).

---

## Tài liệu

- `docs/adr` — architecture decision records
- `docs/wiki` — runbook vận hành và ghi chú tính năng
- [`FCAJ-Uni-Docs`](https://github.com/FCAJ-Portal-HN/FCAJ-Uni-Docs) — tài liệu cấp sản phẩm
- `fcaj-uni-s3/RULES.md` — quy ước bucket và đặt tên object

---

<div align="center">

Hệ thống được phát triển riêng cho **First Cloud AI Journey (FCAJ)**.
Việc sao chép, phân phối lại hoặc công bố mã nguồn ra ngoài tổ chức cần chấp thuận bằng văn bản từ ban quản lý FCAJ.

</div>
