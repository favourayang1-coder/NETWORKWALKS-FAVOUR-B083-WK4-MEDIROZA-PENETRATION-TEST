# Evidence — `mediroza_db_backup_2019.sql`

**Source:** `https://medirozahospital.com/old/mediroza_db_backup_2019.sql`
**Discovered via:** Directory listing enabled on `/old/` (path disclosed in `robots.txt`, flagged by `gobuster`)
**File size:** ~7 KB
**Last Modified (server):** 2026-09-04

This file is a plaintext MySQL dump containing at least two tables. Table structures are reproduced below; full row-level data (including PII such as national ID numbers) is preserved only in the original screenshots under `../screenshots/staff-exposed-data.png` and `../screenshots/shareholders-exposed-data.png`, and is intentionally not fully re-transcribed here to avoid duplicating raw PII across multiple files in this write-up.

## Table: `staff`

```sql
CREATE TABLE `staff` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `full_name` varchar(120) NOT NULL,
  `job_title` varchar(120) NOT NULL,
  `department` varchar(80) NOT NULL,
  `email` varchar(120) NOT NULL,
  `phone` varchar(20) NOT NULL,
  `national_id` varchar(20) NOT NULL,
  `monthly_salary_zar` int(11) NOT NULL,
  `date_joined` date NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

- **Row count:** 30 employees
- **Fields exposed per employee:** full name, job title, department, work email, phone number, **national ID number**, **monthly salary (ZAR)**, date joined
- **Notable row:** `id=9`, `Jameel Malik`, `IT Systems Administrator`, `IT` — matches the `j.malik` author metadata found embedded in `patient_report_3.pdf` (see M3 in the main [README](../README.md)), linking the metadata anomaly directly to a real staff account.
- **Salary range observed:** ~R26,000/month (Pharmacy Assistant) to ~R160,000/month (Medical Director)

## Table: `shareholders`

```sql
CREATE TABLE `shareholders` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `shareholder_name` varchar(120) NOT NULL,
  `share_percent` decimal(5,2) NOT NULL,
  `shares_held` int(11) NOT NULL,
  `share_class` varchar(20) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

- **Row count:** 10 shareholders
- **Fields exposed per shareholder:** name, ownership percentage, number of shares held, share class (Ordinary / Preferential)
- See the full table in the main [README](../README.md#from-a-username-to-a-full-database-backup).

## Why this matters

A single unauthenticated HTTP request to a legacy, indexable directory exposed:
- Full PII and compensation data for every employee at the hospital
- The complete ownership/cap table of the hospital itself

This is a **critical confidentiality breach** with legal/regulatory implications (PII and financial data exposure) well beyond the original scope of "patient lab reports."
