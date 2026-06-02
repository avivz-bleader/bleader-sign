# BLeader Sign — מערכת חתימות אלקטרוניות

מערכת חתימה אלקטרונית בחינם, מבוססת GitHub Pages + Supabase.

## קבצים

| קובץ | תיאור |
|------|-------|
| `index.html` | לוח הבקרה שלך (מנהל) |
| `sign.html` | דף החתימה לעובדים (הלינק שתשלח) |

---

## הגדרה בפעם הראשונה

### שלב 1 — Supabase (בסיס נתונים ואחסון)

1. גש ל-[supabase.com](https://supabase.com) וצור חשבון חינמי
2. צור פרויקט חדש (שמור את הסיסמה)
3. לך ל-**SQL Editor** והרץ את הקוד הבא:

```sql
create table documents (
  id uuid default gen_random_uuid() primary key,
  title text not null,
  description text,
  pdf_url text,
  created_at timestamptz default now()
);

create table signatures (
  id uuid default gen_random_uuid() primary key,
  document_id uuid references documents(id),
  signer_name text not null,
  signature_data text not null,
  signed_at timestamptz default now()
);

-- הרשאות גישה ציבורית
alter table documents enable row level security;
alter table signatures enable row level security;
create policy "public read docs" on documents for select using (true);
create policy "public insert sigs" on signatures for insert with check (true);
create policy "public read sigs" on signatures for select using (true);
```

4. לך ל-**Storage** → צור bucket בשם `documents` ← סמן **Public**
5. לך ל-**Settings → API** → העתק:
   - `Project URL`
   - `anon public` key

---

### שלב 2 — GitHub Pages (אחסון הקוד)

1. גש ל-[github.com](https://github.com) וצור חשבון חינמי
2. צור repository חדש (לדוגמה: `bleader-sign`)
3. העלה את שני הקבצים: `index.html` ו-`sign.html`
4. לך ל-**Settings → Pages** → בחר **Deploy from branch: main**
5. הכתובת שלך תהיה: `https://USERNAME.github.io/bleader-sign/`

---

### שלב 3 — חיבור Supabase למערכת

1. פתח את `index.html` ב-`https://USERNAME.github.io/bleader-sign/`
2. לך ל-**הגדרות** (בסרגל הצד)
3. הדבק את ה-URL וה-Key שהעתקת
4. לחץ **שמור** — זה נשמר בדפדפן

> ✅ מעכשיו גם `sign.html` יעבוד כי שני הדפים חולקים את אותו localStorage

---

## שימוש יומיומי

### להוספת מסמך חדש:
1. לך ל-**מסמך חדש**
2. העלה PDF
3. הזן שם ותיאור
4. לחץ **צור מסמך** — תקבל לינק
5. שלח את הלינק לקבוצת WhatsApp

### העובד רואה:
1. מזין שם מלא
2. קורא את ה-PDF
3. חותם עם האצבע/עכבר
4. לוחץ אשר — הושלם!

### לך:
- לוח בקרה מציג מי חתם בזמן אמת
- הורדת PDF חתום לכל עובד

---

## עלויות

| שירות | מגבלת חינמי |
|-------|------------|
| GitHub Pages | ללא הגבלה |
| Supabase DB | 500MB, 50,000 שורות/חודש |
| Supabase Storage | 1GB קבצים |

**לרוב ארגונים קטנים — חינמי לנצח.**

---

## שאלות נפוצות

**האם החתימה משפטית תקפה?**  
חתימה אלקטרונית בישראל תקפה לפי חוק החתימה האלקטרונית, תשס"א-2001. לצרכים משפטיים מורכבים מומלץ להוסיף אימות זהות.

**האם הנתונים מאובטחים?**  
כל הנתונים נשמרים ב-Supabase שלך בלבד. GitHub Pages מציג רק קוד סטטי.

**כמה עובדים יכולים לחתום?**  
ללא הגבלה — כל אחד שיש לו את הלינק.
