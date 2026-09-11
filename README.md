# SDA_TITHE_MS
# SDA Tithe Management System

A web-based tithe payment, receipting, and reporting system for Seventh Day
Adventist churches in Rwanda, built with **Java, JSF (Jakarta Faces), and
Hibernate ORM**, developed in **IntelliJ IDEA 2026.2.1**.

This is the Phase 1 practical implementation for Assignment 3: full CRUD
(Create, Read, Update, Delete) on the **Member** and **TithePayment**
entities, with three types of validation and three types of CSS.

## Features implemented

- Public home page with a "Pay Tithe" call to action and a Bible verse (`index.xhtml`)
- Member management: register, list/search, edit, delete
- Tithe payment: pay, list, edit, delete
- Automatic receipt generation (name, amount in figures + words, date, church, receipt number), viewable and downloadable/printable
- Weekly / monthly / yearly tithe reports per church (or all churches), downloadable/printable
- Validation:
  1. Standard JSF validators (`required`, `f:validateLength`, `f:validateRegex`)
  2. Custom JSF validator (`AmountValidator`, registered as `amountValidator`)
  3. Bean Validation (JSR 380) annotations directly on the `Member` and `TithePayment` entities
- CSS:
  1. **Internal** CSS — `<style>` block inside `member/memberForm.xhtml`
  2. **External** CSS — `resources/css/styles.css`, linked via `<h:outputStylesheet>` in `WEB-INF/template.xhtml`
  3. **Inline** CSS — `style="..."` attributes on the home page button and the payment list amount column

## Project structure

```
sda-tithe-management-system/
├── pom.xml
├── README.md
└── src/main/
    ├── java/com/sda/tithe/
    │   ├── entity/        Church, Member, TithePayment (JPA + Bean Validation)
    │   ├── dao/            GenericDao, ChurchDao, MemberDao, TithePaymentDao (Hibernate)
    │   ├── bean/           HomeBean, MemberBean, TithePaymentBean, ReceiptBean (JSF/CDI)
    │   ├── validator/      AmountValidator (custom JSF validator)
    │   └── util/           HibernateUtil, DataSeedListener, NumberToWordsConverter
    ├── resources/
    │   └── hibernate.cfg.xml
    └── webapp/
        ├── index.xhtml               (home page)
        ├── member/memberList.xhtml   (Read + entry points)
        ├── member/memberForm.xhtml   (Create/Update, internal CSS)
        ├── payment/paymentList.xhtml (Read + entry points, inline CSS)
        ├── payment/paymentForm.xhtml (Create/Update, all 3 validation types)
        ├── payment/receipt.xhtml     (downloadable receipt)
        ├── payment/reports.xhtml     (weekly/monthly/yearly reports)
        ├── resources/css/styles.css  (external CSS)
        └── WEB-INF/
            ├── template.xhtml, web.xml, beans.xml
```

## Prerequisites

- **JDK 17+**
- **IntelliJ IDEA 2026.2.1** (Ultimate edition recommended, for Jakarta EE/web app support)
- **Apache Tomcat 10.1+** or any Jakarta EE 10 compatible server (e.g., WildFly). Tomcat 10.1 is a "Faces-capable" servlet-only container — you must add the Mojarra JSF jars to `WEB-INF/lib` if you deploy to plain Tomcat (Maven already pulls these as WAR dependencies).
- **MySQL 8+** running locally

## Setup in IntelliJ IDEA

1. Open IntelliJ IDEA → **File → Open** → select the `sda-tithe-management-system` folder.
2. IntelliJ will detect the `pom.xml` and prompt to load it as a Maven project — accept.
3. Create the database: open MySQL and run:
   ```sql
   CREATE DATABASE IF NOT EXISTS sda_tithe_db;
   ```
   (Or let Hibernate create it automatically — the JDBC URL in `hibernate.cfg.xml` already includes `createDatabaseIfNotExist=true`.)
4. Update the database username/password in `src/main/resources/hibernate.cfg.xml` to match your local MySQL setup.
5. **Run → Edit Configurations → + → Tomcat Server → Local**, point it at your installed Tomcat, and add the project artifact (`sda-tithe-management-system:war exploded`) to "Deployment".
6. Run the configuration. On first startup, `DataSeedListener` automatically inserts 5 sample Rwandan SDA churches so the Member/Payment forms have data to select from.
7. Visit `http://localhost:8080/sda-tithe-management-system/` — you should see the home page with the "Pay Tithe" button and a Bible verse.

## Typical workflow to test CRUD

1. Go to **Members → + Add New Member**, register a member (pick one of the seeded churches).
2. Go to **Tithe Payments → + Record New Tithe Payment**, select that member and a church, enter an amount, date, and method.
3. Click **Receipt** on the payment list to view the generated receipt (name, amount in figures and words, date) — use "Download Receipt" to print/save as PDF.
4. Go to **Reports**, pick Weekly/Monthly/Yearly and a church (or all churches), click **Generate Report**, then **Download Report**.
5. Try **Edit** and **Delete** on both Members and Tithe Payments to see full CRUD in action.
6. Try submitting the payment form with an empty amount, a negative amount, and a huge amount (e.g. 999999999) to see all three validation layers fire.

## Publishing to GitHub

```bash
cd sda-tithe-management-system
git init
git add .
git commit -m "Initial commit: SDA Tithe Management System (JSF + Hibernate CRUD)"
git branch -M main
git remote add origin https://github.com/<your-username>/sda-tithe-management-system.git
git push -u origin main
```
Make sure the repository visibility is set to **Public** so it can be linked in your documentation.

## Recording the demo video (Google Vids)

The assignment requires a 5–10 minute screen + camera recording covering:
1. A short project proposal explanation (problem, scope, what the system does).
2. A live walkthrough of the JSF + Hibernate CRUD implementation: register a member, pay tithe, view/download a receipt, generate a report, edit and delete a record.

Steps:
1. Open **Google Vids**, start a new recording, enable both **screen share** and **camera**.
2. Run through the workflow above while narrating.
3. Stop recording, let it process, then click **Share** and set access to "Anyone with the link can view".
4. Copy the share link into the "Project Links" section at the top of the documentation file.

## Notes / possible extensions

- Real Mobile Money / bank API integration for payments (currently a payment-method field only).
- A dedicated `Church` CRUD screen (currently seeded and only referenced by Member/TithePayment).
- Role-based login (Member vs Tithe Manager) with Jakarta Security.
- PDF export using a library like OpenPDF instead of the browser's print-to-PDF.
