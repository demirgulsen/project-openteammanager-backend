# Open Team Manager

<div align="center">

[![GitHub](https://img.shields.io/badge/GitHub-Bilgisayar-Kavramlari-Toplulugu-181717?style=flat-square&logo=github)](https://github.com/Bilgisayar-Kavramlari-Toplulugu/project-openteammanager-backend)
[![License: MIT]([https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square](https://img.shields.io/badge/Next.js-15-black)](LICENSE)
[![Nextjs]([https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square])](Nextjs)
[![FastAPI]([https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square])](FastAPI)



**Part of [Ekip yönetimini basitleştir, verimliliği artır!](docs/Project-Definition.md)**

</div>

---

<details open>
<summary><strong>🇹🇷 Türkçe</strong></summary>

<br>

> **ÖNEMLİ:** Bu repository **Ekip yönetimini basitleştir, verimliliği artır!** projesinin bir parçasıdır. Proje hakkında detaylı bilgi için [`docs/Project-Definition.md`](docs/Project-Definition.md) dosyasına bakın.

## 📖 Hakkında

<!-- Topluluk projelerini, görevleri ve ekip üyelerini takip eden açık kaynaklı proje yönetim sistemi.
Open Team Manager, toplulukların gönüllü projelerini yönetmek için kullandığı açık kaynaklı bir platformdur. Proje koordinatörleri görev açar, üyeler katılım isteği gönderir, görevler takip edilir.

Temel özellikler:

📋 Proje ve görev yönetimi (Kanban board)
👥 Topluluk üye dizini ve profil sayfaları
🔔 Gerçek zamanlı bildirimler (WebSocket)
📊 Sprint raporları ve istatistikler
🔐 JWT tabanlı kimlik doğrulama

Teknoloji Stack'i

-->

## 🚀 Kurulum

### Gereksinimler

- Frontend:       Next.js 15, TypeScript, Tailwind CSS, shadcn/ui
- BackendPython:  3.12, FastAPI, SQLAlchemy, Celery
- Veritabanı:     PostgreSQL 16, Redis 7
- Altyapı:        Docker, GitHub Actions



### Başlangıç

```bash
# Repoyu Klonla (Projeyi kendi bilgisayarına indir)
git clone https://github.com/Bilgisayar-Kavramlari-Toplulugu/project-openteammanager-backend.git
cd project-openteammanager-backend

# Ortam Değişkenlerini Ayarla
cp backend/.env.example backend/.env
cp frontend/.env.example frontend/.env.local

# Docker ile Başlat
docker compose up --build
docker compose exec backend alembic upgrade head

# Tamamlanınca servislerin durumunu kontrol et
docker compose ps

# örnek çıktı:
backend    running   :8000
frontend   running   :3000
db         running   :5432

# Veritabanını Hazırla - migration çalıştır (kod içindeki tablo tanımlarını okuyup veritabanına uygular)
docker compose exec backend alembic upgrade head

# Çalıştığını Doğrula
# frontend : http://localhost:3000
# backend : http://localhost:8000
# API endpoint'leri : http://localhost:8000/docs

```

## 💻 Kullanım
##### Her çalışma oturumunda servisleri başlatman yeterli:

docker compose up       # Tüm servisleri başlat
docker compose up -d    # Yalnızca arka planda başlat
docker compose down     # Bitince durdur
docker compose logs -f backend    # backend loglarını canlı izle
docker compose logs -f frontend   # frontend loglarını canlı izle

```bash
# Uygulamayı çalıştırma komutunu buraya ekleyin
```

## 📁 Proje Yapısı

```
project-openteammanager-backend/
backend/
├── app/
│   ├── main.py                  # FastAPI uygulama girişi
│   ├── config.py                # Pydantic Settings (env)
│   ├── database.py              # Async SQLAlchemy engine
│   ├── dependencies.py          # DI: get_db, get_current_user
│   ├── models/                  # SQLAlchemy ORM modelleri
│   │   ├── user.py
│   │   ├── project.py
│   │   ├── task.py
│   │   └── ...
│   ├── schemas/                 # Pydantic request/response
│   │   ├── user.py
│   │   ├── project.py
│   │   └── ...
│   ├── routers/                 # API endpoint grupları
│   │   ├── auth.py              # /api/v1/auth/*
│   │   ├── projects.py          # /api/v1/projects/*
│   │   ├── tasks.py             # /api/v1/tasks/*
│   │   ├── members.py
│   │   └── reports.py
│   ├── services/                # İş mantığı katmanı
│   │   ├── auth_service.py
│   │   ├── task_service.py
│   │   └── report_service.py
│   ├── core/
│   │   ├── security.py          # JWT, bcrypt
│   │   ├── permissions.py       # Yetki kontrolü
│   │   └── websocket.py         # WS bağlantı yönetimi
│   └── workers/
│       ├── celery_app.py
│       └── tasks.py             # Arka plan görevleri
├── migrations/                  # Alembic
├── tests/        # Testler
├── docs/         # Dokümantasyon
├── requirements.txt
├── docker-compose.yml
├── .env.example
└── README.md

```
## Teknik Mimari 

```bash
+----------------------------------------------------------+
|                      Kullanici                           |
|              Tarayici (Chrome, Firefox...)               |
+-------------------------+--------------------------------+
                          | HTTP / WebSocket
+-------------------------v--------------------------------+
|              Next.js 15  (Frontend)                     |
|   Kullanicinin gordugu her sey burada render edilir      |
|   App Router / TypeScript / Tailwind / shadcn/ui         |
|              http://localhost:3000                       |
+-------------------------+--------------------------------+
                          | REST API / WebSocket
+-------------------------v--------------------------------+
|              FastAPI  (Backend)                         |
|   İş mantigi, kimlik dogrulama, veri islemleri burada   |
|         SQLAlchemy / Pydantic / Celery                  |
|              http://localhost:8000                       |
+--------+------------------------------+-----------------+
         |                              |
+--------v---------+         +----------v------+
|   PostgreSQL     |         |     Redis       |
| Ana veritabani   |         | Onbellekleme +  |
| Tum veriler      |         | is kuyrugu      |
|      :5432       |         |      :6379      |
+------------------+         +--------+--------+
                                       |
                              +--------v--------+
                              |  Celery Worker  |
                              | Arka plan isleri|
                              | (email, rapor)  |
                              +-----------------+

```

## 🧪 Test

```bash
# Testleri çalıştır
docker compose exec backend pytest

# Tek dosya
docker compose exec backend pytest tests/test_tasks.py

# Tek test
docker compose exec backend pytest tests/test_tasks.py::test_create_task

# Coverage raporu
docker compose exec backend pytest --cov=app --cov-report=term-missing

# Frontend
docker compose exec frontend npm run test
docker compose exec frontend npm run test -- --watch

# Backend
docker compose exec backend ruff check .
docker compose exec backend ruff check --fix .   # otomatik düzelt
docker compose exec backend ruff format .        # biçimlendir

# Frontend
docker compose exec frontend npm run lint
docker compose exec frontend npm run lint -- --fix
docker compose exec frontend npm run type-check

```

## 🤝 Katkıda Bulunma

Her seviyeden katkı memnuniyetle karşılanır
  — kod, dokümantasyon, hata raporu, tasarım önerisi.

1. CONTRIBUTING.md dosyasını oku
2. good first issue etiketli bir görev seç → Issues
3. Branch aç, kodunu yaz, PR gönder

Katkıda bulunmak için lütfen [`CONTRIBUTING.md`](.github/CONTRIBUTING.md) dosyasını inceleyin.

## 📚 Dokümantasyon

- [Proje Tanımı](docs/Project-Definition.md)
- [Mimari Genel Bakış](docs/Architecture-Overview.md)
- [Geliştirme Akışı](docs/Development-Workflow.md)

## 📄 Lisans

Bu proje MIT Lisansı ile lisanslanmıştır - detaylar için [LICENSE](LICENSE) dosyasına bakın.

---

**Proje Lideri:** [@hakanceran64](https://github.com/hakanceran64)

</details>

<details>
<summary><strong>🇬🇧 English</strong></summary>

<br>

> **IMPORTANT:** This repository is part of **Ekip yönetimini basitleştir, verimliliği artır!** project. See [`docs/Project-Definition.md`](docs/Project-Definition.md) for details.

## 📖 About

<!-- Describe what this repository does -->

## 🚀 Installation

### Requirements

- List required tools here

### Getting Started

```bash
git clone https://github.com/Bilgisayar-Kavramlari-Toplulugu/project-openteammanager-backend.git
cd project-openteammanager-backend

# Add installation steps here
```

## 💻 Usage

```bash
# Add command to run the application
```

## 📁 Project Structure

```
project-openteammanager-backend/
├── src/          # Source code
├── tests/        # Tests
├── docs/         # Documentation
└── README.md     # This file
```

## 🧪 Testing

```bash
# Add test commands here
```

## 🤝 Contributing

Please see [`CONTRIBUTING.md`](.github/CONTRIBUTING.md) for contribution guidelines.

## 📚 Documentation

- [Project Definition](docs/Project-Definition.md)
- [Architecture Overview](docs/Architecture-Overview.md)
- [Development Workflow](docs/Development-Workflow.md)

## 📄 License

This project is licensed under the MIT License - see [LICENSE](LICENSE) file for details.

---

**Project Lead:** [@hakanceran64](https://github.com/hakanceran64)

</details>
