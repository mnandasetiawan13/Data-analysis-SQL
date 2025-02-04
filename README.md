# INTRODUCTION
Ini adalah langkah pertama saya untuk terjun ke dunia data analyst.Saya memfokuskan diri untuk menjadi seorang data analis dengan mengembangkan sebuah proyek yang mengedepankan analisa tentang beberapa role di dunia data yang memiliki permintaan dan pendapatan yang tinggi diikuti dengan skill yang sedang banyak dibutuhkan 

# BACKGROUND
Karena permintaan talent data analyst yang sangat tinggi,maka project ini dilahirkan untuk menganalisa skill apa saja yang dibutuhkan dan role apa saja yang memiliki pendapatan tinggi.Proyek ini juga ditujukan untuk mengetahui keselarasan skill dan pendapatan yang akan didapatkan oleh data analyst secara optimal.

Sumber data proyek ini saya dapatkan dari online course https://www.lukebarousse.com/sql yang memiliki data seperti data pekerjaan,pendapatan,lokasi,skill yang dibutuhkan,dan lain sebagainya

Beberapa Studi kasus yang saya analisa dengan data di atas pada kasus ini antara lain :

1. Role apa yang memiliki pendapatan tertinggi dalam pekerjaan data analyst?
2. Apa saja skill yang dibutuhkan untuk role ini?
3. Skill apa saja yang memiliki permintaan tertinggi?
4. Skill apa saja yang memiliki peluang gaji yang lebih tinggi?
5. Apa saja skill yang perlu dipelajari secara optimal?

# Tools 
Dalam project ini saya menggunakan beberapa tools untuk melakukan analisa dan pengolahan data,antara lain:

- SQL : Digunakan untuk membangun database dan mengeksekusi query analisa
- PostgreSQL : Digunakan sebagai Database management system
- VS Code : Digunakan untuk keperluan penulisan dan eksekusi query
- Git&Github : Sebagai version control dan sarana berbagi studi kasus dan script SQL serta memungkinkan kolaborasi antar sesama pengguna

# The Analysis
Setiap studi kasus akan memuat kasus yang berbeda disertai dengan query untuk penyelesaian permasalahan 
### 1. Role apa yang memiliki pendapatan tertinggi dalam pekerjaan data analyst?

Untuk melakukakn analisa ini,saya melakukan filtrasi data dengan memfokuskan pada role dan annual year salary,lokasi, dan pekerjaan yang dapat dilakukan secara remote.Kemudian diimplementasikan dengan query :
```sql
SELECT
job_id,
job_title,
job_location,
job_schedule_type,
salary_year_avg,
job_posted_date::DATE,
name as comapany_name

FROM job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id=company_dim.company_id
WHERE job_title_short = 'Data Analyst' AND
job_location = 'Anywhere' AND
salary_year_avg IS NOT NULL
ORDER BY
salary_year_avg DESC limit 10
```
Berikut hasil analisa distribusi data pekerjaan data analyst berdasarkan data di atas :

- **Sebaran distribusi data :** pada field data analyst pada peringkat 10 besar tersebar antara $184.000 sampai dengan $650.000 dengan potensi yang sangat besar
- **Sebaran Karyawan:** Beberapa perusahaan besar seperti META,smartassets dan AT&T memiliki rata-rata gaji yang lebih tinggi dibandingkan dengan perusahaan lain
- **Keberagaman role pekerjaan :** Sebaran role pekerjaan dalam field data analyst memiliki keberagaman yang tinggi seperti data analyst sampai dengan ERM data analyst

Sebaran distribusi:
![distribusi_data] (assets\query 1 - distribusi.PNG)

Rata-rata Gaji :
![rata-rata] (assets\query 1 - rata-rata.PNG)

### 2. Apa saja skill yang dibutuhkan untuk role ini?
Untuk mendapatkan pekerjaan dengan gaji yang tinggi,maka diperlukan juga skill yang mumpuni.Skill tersebut dapat digambarkan dengan query di bawah ini :
```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        salary_year_avg,
        job_posted_date::DATE,
    name as comapany_name

    FROM job_postings_fact
    LEFT JOIN 
        company_dim 
        ON job_postings_fact.company_id=company_dim.company_id
    WHERE 
        job_title_short = 'Data Analyst' AND
        job_location = 'Anywhere' AND
        salary_year_avg IS NOT NULL
    ORDER BY
        salary_year_avg DESC limit 10
)
SELECT top_paying_jobs.* ,skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY salary_year_avg DESC limit 10;
```
![skill_need] (assets\query 2 - top 25.PNG)

berdasarkan hasil di atas,kita mendapatkan hasil :

- SQL memimpin dengan 8 
- Python dengan 7
- Tableau menjadi tools visualisasi dengan gaji tertinggi ke tiga dalam field data analysis

### 3. Skill Dengan Permintaan Tertinggi
Dalam menganalisa pekerjaan dengan gaji tinggi,saya juga menghubungkan skill apa dengan permintaan tertinggi unutuk mengakomodir pekerjaan dengan gaji tinggi.
``` sql
SELECT skills,
COUNT(skills_job_dim.job_id) AS "Demand Count"
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst' AND job_work_from_home = TRUE
GROUP BY 
    skills
ORDER BY "Demand Count"  DESC
LIMIT 10;
```
Hasil :
- SQL dan excel memiliki permintaan tertinggi untuk skill data analysis
- Tools visualisasi dan pemrograman menempati urutan selanjutnya.

![skill_top] (assets\query 3.PNG)

### 4. Rata-Rata Gaji Berdasarkan Skill
Setiap skill memiliki rata-rata gaji yang berbeda.Ada beberapa skill yang memiliki rataan gaji yang lebih tinggi dibanding skill yang lain.
```sql
SELECT skills,
ROUND(AVG(salary_year_avg),0) AS "AVG Salary"
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst' 
AND salary_year_avg IS NOT NULL
AND job_work_from_home = TRUE
GROUP BY 
    skills
ORDER BY "AVG Salary"  DESC
LIMIT 25;
```
![skill_salaries] (assets\query 4.PNG)
 
Dari analisa di atas,saya mendapatkan hasil :
- **Permintaan Tinggi untuk Keterampilan Big Data & ML**: Gaji teratas diraih oleh analis yang ahli dalam teknologi big data (PySpark, Couchbase), alat pembelajaran mesin (DataRobot, Jupyter), dan pustaka Python (Pandas, NumPy), yang mencerminkan penilaian tinggi industri terhadap kemampuan pemrosesan data dan pemodelan prediktif.
- **Skill Pengembangan & Penerapan Perangkat Lunak**: Pengetahuan dalam alat pengembangan dan penerapan (GitLab, Kubernetes, Airflow) menunjukkan adanya peluang menguntungkan antara analisis data dan rekayasa, dengan keunggulan pada keterampilan yang memfasilitasi otomatisasi dan manajemen alur data yang efisien.
**Skill Komputasi Cloud**: Keakraban dengan alat rekayasa data dan cloud (Elasticsearch, Databricks, GCP) menggarisbawahi semakin pentingnya lingkungan analitik berbasis cloud, yang menunjukkan bahwa kemahiran cloud secara signifikan meningkatkan potensi penghasilan dalam analitik data. 
### 5 Skills Yang Optimal Untuk Dipelajari
Dengan menggabungkan hsil analisa dari data permintaan dan gaji, kueri ini bertujuan untuk menentukan skill yang banyak diminati dan memiliki gaji tinggi, sehingga menawarkan fokus strategis untuk pengembangan skill.
```sql
SELECT 
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = True 
GROUP BY
    skills_dim.skill_id
HAVING
    COUNT(skills_job_dim.job_id) > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25;
```

![skill_optimal] (assets\query 5.PNG)

- **Bahasa Pemrograman yang Sangat Diminati**: Python dan R menonjol karena permintaannya yang tinggi, dengan jumlah permintaan masing-masing 236 dan 148. Meskipun permintaannya tinggi, gaji rata-rata mereka sekitar $101.397 untuk Python dan $100.499 untuk R, yang menunjukkan bahwa kemahiran dalam bahasa-bahasa ini sangat dihargai tetapi juga tersedia secara luas.
- **Alat dan Teknologi Cloud**: Keterampilan dalam teknologi khusus seperti Snowflake, Azure, AWS, dan BigQuery menunjukkan permintaan yang signifikan dengan gaji rata-rata yang relatif tinggi, yang menunjukkan semakin pentingnya platform cloud dan teknologi big data dalam analisis data.
- **Tools Visualisasi dan Kecerdasan Bisnis**: Tableau dan Looker, dengan jumlah permintaan masing-masing 230 dan 49, dan gaji rata-rata sekitar $99.288 dan $103.795, menyoroti peran penting visualisasi data dan kecerdasan bisnis dalam memperoleh wawasan yang dapat ditindaklanjuti dari data.
- **Teknologi - Basis Data**: Permintaan akan keterampilan dalam basis data tradisional dan NoSQL (Oracle, SQL Server, NoSQL) dengan gaji rata-rata berkisar antara $97.786 hingga $104.534, mencerminkan kebutuhan abadi akan keahlian penyimpanan, pengambilan, dan pengelolaan data.

### Apa Yang Saya Pelajari
- **Membuat Struktur Query Kompleks Untuk Menyelesaikan Masalah** : Masalah yang terlihat kompleks dapat diselesaikan dengan query terstruktur dan secara deskriptif dapat mengakomodir analisa data yang diperlukan
- **Data Aggregation** : Penggunaan perintah agregasi seperti AVG(),COUNT() dikombinasikan dengan GROUP BY dapat sangat membantu dalam analisa data
- **Analisa Masalah** : mengidentifikasi masalah dan menyelesaikan dengan query terstruktur dan hasil yang presisi

### Kesimpulan

Dari analisis tersebut, muncul beberapa hasil analisa umum:

- Role Analis Data Bergaji Tinggi: Pekerjaan dengan gaji tertinggi untuk analis data yang memungkinkan kerja jarak jauh menawarkan berbagai macam gaji, yang tertinggi adalah $650.000!
- Skill untuk Pekerjaan Bergaji Tinggi: Pekerjaan analis data bergaji tinggi memerlukan kemahiran tingkat lanjut dalam SQL, yang menunjukkan bahwa ini adalah keterampilan penting untuk mendapatkan gaji tinggi.
- Skill yang Paling Banyak Diminati: SQL juga merupakan keterampilan yang paling diminati di pasar kerja analis data, sehingga membuatnya penting bagi para pencari kerja.
- Skill dengan Gaji Lebih Tinggi: Keterampilan khusus, seperti SVN dan Solidity, dikaitkan dengan gaji rata-rata tertinggi, yang menunjukkan premi pada keahlian khusus.
- Skill Optimal untuk Nilai Pasar Kerja: SQL memimpin dalam permintaan dan menawarkan gaji rata-rata yang tinggi, memposisikannya sebagai salah satu keterampilan paling optimal bagi analis data untuk dipelajari guna memaksimalkan nilai pasar mereka.
