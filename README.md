
# 📘 RavenDB Academy Database Demo

Демонстраційний проєкт бази даних **AcademyDB**, перенесений із SQL Server до **RavenDB**.  

---

## 🧩 Структура колекцій

| Колекція | Опис |
|-----------|------|
| `Users` | Користувачі системи (admin, student, teacher) з вкладеними підтипами `student`, `teacher` |
| `Courses` | Курси з мовою, рівнем, ціною та викладачами |
| `Modules` | Модулі курсів із порядковим номером |
| `Lessons` | Уроки, прив’язані до модулів |
| `Tags` | Тематичні теги для уроків |
| `Enrollments` | Записи студентів на курси |
| `Certificates` | Сертифікати для завершених записів |
| `Reviews` | Відгуки студентів про курси |
| `LessonGrades` | Оцінки уроків (зв’язок student–lesson–teacher) |

---

## ⚙️ Використані технології

- **RavenDB 7.x (Windows)**
- **RQL** (Raven Query Language)
- **RavenDB Studio** для управління документами та запитами

---

## 🧠 Приклади RQL-запитів

### Отримати всіх студентів

```rql
from Users
where role = 'student'
```
<img width="841" height="449" alt="image" src="https://github.com/user-attachments/assets/2fa754fe-b6f9-4937-b0c5-b78b9b617a1e" />

Всі викладачі з їх біографією та досвідом

```rql
from Users
where role = 'teacher'
select name, email, teacher
```
<img width="866" height="471" alt="image" src="https://github.com/user-attachments/assets/fefeb691-79d2-40dc-9891-fe50feb398a4" />

🔹 Вчителі з досвідом понад 5 років
```rql
from Users
where role = 'teacher' and teacher.experience >= 5
```
<img width="1003" height="502" alt="image" src="https://github.com/user-attachments/assets/57257591-914c-4c21-936f-8022c12cd51d" />

🔹 Студенти, записані на курс
```rql
from Enrollments
where courseId = 'courses/1'
include userId
```
<img width="1013" height="478" alt="image" src="https://github.com/user-attachments/assets/f51b5df1-7eed-42dd-9e7b-d2b2acd3f0ff" />

🔹 Активні або завершені курси певного користувача
```rql
from Enrollments
where userId = 'users/1' and status in ('active', 'completed')
order by enrolledAt desc
```
<img width="1063" height="404" alt="image" src="https://github.com/user-attachments/assets/2b9edae3-6939-459f-b49c-4cf1351b5d1a" />

🔹 Оцінки уроків певного студента
```rql
from LessonGrades
where studentId = 'users/4'
order by gradedAt desc
```
<img width="1073" height="399" alt="image" src="https://github.com/user-attachments/assets/d7d3eae4-0720-45e8-8ef1-cea7eafd66ac" />

🔹 Псевдо-JOIN (load) між Enrollment → User → Course
```rql
from Enrollments as e
load e.userId as u, e.courseId as c
select {
    userName: u.name,
    courseTitle: c.title,
    status: e.status,
    enrolledAt: e.enrolledAt
}
```
<img width="1060" height="571" alt="image" src="https://github.com/user-attachments/assets/7049b545-7832-4e18-9f7b-5784608c2cca" />

🔹 Відгуки по курсу
```rql
from Reviews
where courseId = 'courses/1'
select userId
```
<img width="1023" height="454" alt="image" src="https://github.com/user-attachments/assets/a2d20ad1-6bf1-4068-8820-309a6d3f7233" />

🔹 Пошук курсу за частиною назви
```rql
from Courses
where search(title, 'програм*')
```
<img width="1047" height="475" alt="image" src="https://github.com/user-attachments/assets/3b9767bf-f9e4-4158-b1d3-aa6c977963f1" />

---

## Quick start

### Option A — Docker (recommended)

1. Install Docker Desktop.
2. Start RavenDB:

```bash
docker compose up -d
```
This maps RavenDB to **http://127.0.0.1:8080** with TCP **38888**.

3. Open Studio in the browser → **http://127.0.0.1:8080**.
4. Create database **`festival_db`** (Databases → New Database).

### Option B — Windows zip

If you use the Windows zip (`run.ps1`):
```
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\run.ps1
```
Open **http://127.0.0.1:8080** and create database **`festival_db`**.

---

## Import the sample data

### Import via Studio

Studio → **Settings → Import Database** → *Full Database Export* → choose the dump `.ravendbdump` → **Import**.

- If the dump is not in the repo, get it from Releases (or link provided by the author).
- Alternative: use **Smuggler/rvn** CLI (optional).

---
