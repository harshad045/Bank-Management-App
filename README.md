(cd "$(git rev-parse --show-toplevel)" && git apply --3way <<'EOF'
diff --git a/README.md b/README.md
--- a/README.md
+++ b/README.md
@@ -1,1 +1,104 @@
-# Bank-Management-App
+## Bank Management App (Java Servlets + JSP)
+
+A simple banking web application built with Java Servlets, JSP, and JDBC (MySQL). It supports user registration/approval, account management, transactions, transfers, passbook, and fixed deposits with admin and user dashboards.
+
+### Tech Stack
+- **Backend**: Java 8+ Servlets (`javax.servlet`), JSP, JSTL
+- **Database**: MySQL 8+
+- **JDBC Driver**: `mysql-connector-j 8.0.x`
+- **Templating**: JSP + JSTL
+- **PDF**: iText (packaged jar)
+- **App Type**: Traditional WAR-less servlet app using `@WebServlet` annotations
+
+### Project Layout
+```
+src/main/java/com/tss
+  ├─ controller/   # Servlets (annotated with @WebServlet)
+  ├─ service/      # Business logic
+  ├─ dao/          # Data access via JDBC
+  ├─ model,dto/    # Entities and DTOs
+  └─ database/     # DB connection helper (DBConnection)
+src/main/webapp
+  ├─ *.jsp         # Views
+  └─ WEB-INF/lib   # Provided jars (JSTL, MySQL driver, iText)
+database.sql       # Schema + seed data
+```
+
+### Prerequisites
+- Java 8+ (JDK)
+- Tomcat 9/10 (or any Servlet 4+ compatible container)
+- MySQL 8+
+
+### Database Setup
+1) Create and seed the database:
+```sql
+SOURCE /path/to/database.sql;
+```
+This creates a database named `bank_management` and seeds admins, users, accounts, transactions, FD tables, and config.
+
+2) Configure DB credentials in `DBConnection`:
+```java
+// src/main/java/com/tss/database/DBConnection.java
+private static final String URL = "jdbc:mysql://localhost:3306/bank_management";
+private static final String USERNAME = "root";     // change
+private static final String PASSWORD = "<your-pass>"; // change
+```
+
+### Running the App
+Because this project does not include a Maven/Gradle build file, deploy it as a standard Java webapp:
+
+- Option A (IDE): Import as a Java Web project, set Tomcat as the server, mark `src/main/webapp` as Web Resource Directory, and run on Tomcat.
+- Option B (Manual): Package the classes into `WEB-INF/classes` and ensure jars are under `WEB-INF/lib`, then deploy the folder to Tomcat as an exploded webapp.
+
+Suggested Tomcat context path: `/bank` (or root `/`). After deployment, open:
+```
+http://localhost:8080/bank/login
+```
+
+### Default Credentials (seeded)
+- **Super Admin**: `superadmin` / `admin123`
+- **Admin**: `admin1` / `pass123`, `admin2` / `pass456`
+- **Users**: `john_doe` / `john123`, `jane_smith` / `jane123` (Approved)
+
+Note: Passwords are stored in plain text for demo purposes only.
+
+### Key Endpoints (Servlet mappings)
+- Auth: `/login`, `/logout`, `/forgot-password`
+- Registration: `/register/step1`, `/register/step2`
+- User: `/user/*`, `/user/passbook`, `/user/transaction`, `/user/transfer`, `/user/analysis`, `/user/my-fds`, `/user/complaints`
+- Admin: `/admin/*`, `/admin/manage-users`, `/admin/manage-fds`, `/admin/fd-pending`, `/admin/transaction-report`, `/admin/approve`, `/admin/analysis`, `/admin/complaints`
+- Profile: `/user-profile`
+
+JSP views live under `src/main/webapp`. Most pages are linked from the user/admin dashboards after login.
+
+### Dependencies (bundled under WEB-INF/lib)
+- `mysql-connector-j-8.0.x.jar`
+- `jstl-1.2.jar`
+- `itextpdf-5.5.13.3-*.jar`
+
+If deploying to a fresh Tomcat instance, ensure these jars are present in the app's `WEB-INF/lib`.
+
+### Environment Configuration
+- Set `DBConnection.java` to match your MySQL host, port, database, username, and password.
+- Ensure MySQL timezone configs are compatible (e.g., add `?serverTimezone=UTC` to JDBC URL if needed).
+
+### Build Notes
+- No `pom.xml` or `build.gradle` provided. If you prefer Maven:
+  - Create a `pom.xml` with Servlet API (provided), JSTL, MySQL Connector, iText; configure `maven-war-plugin` and set `src/main/webapp` as web resources.
+  - Then run `mvn clean package` and deploy the WAR to Tomcat.
+
+### Security Notice
+This project is for learning/demo:
+- Plain-text passwords in DB (do not use in production)
+- Minimal validation and error handling
+- No CSRF protection
+- Basic session management
+
+### Troubleshooting
+- HTTP 404 after deploy: verify context path and visit `/login`.
+- ClassNotFoundException for MySQL driver: check `WEB-INF/lib/mysql-connector-j-*.jar` presence.
+- Access denied for DB user: confirm `DBConnection` credentials and grants.
+- Timezone/SSL JDBC warnings: append `?serverTimezone=UTC&useSSL=false` to JDBC URL.
+
+### License
+MIT (or your preferred license)
EOF
)
