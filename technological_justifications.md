
---

## Technology Choices & Justifications

### Full Stack at a Glance

| Layer | Choice | Version |
|-------|--------|---------|
| Frontend framework | React (CRA) | 18.2 |
| Styling | Tailwind CSS + shadcn/ui | 3.4 |
| Routing | React Router | 6 |
| Backend framework | Spring Boot | 3.5.11 |
| Language (backend) | Java | 25 |
| ORM | Spring Data JPA (Hibernate) | N/A |
| Database | MySQL | 8+ |
| Auth | JWT (jjwt 0.11) | N/A |
| Email | Spring Mail (SMTP) | N/A |
| Build tools | Maven (backend), CRA (frontend) | N/A |

---

### Q1. Why Spring Boot over Node.js / Express?

| Factor | Spring Boot (chosen) | Node.js / Express |
|--------|---------------------|-------------------|
| **Type safety** | Java is statically typed, catching bugs at compile time. Critical for a healthcare platform where malformed data (wrong patient ID type, null appointment fields) can cause real harm. | JavaScript is dynamically typed; runtime errors are more likely without extra tooling (TypeScript). |
| **Built-in ecosystem** | Spring Boot starters give us JPA, Mail, Security, Validation, and DevTools out of the box, one dependency line each. No need to evaluate, install, or glue together separate npm packages. | Express is minimalist by design. Every feature (ORM, email, auth) requires finding, vetting, and integrating a third-party library (Sequelize, Nodemailer, Passport, etc.). |
| **JPA / Hibernate** | With `@Entity` annotations our Java models auto-generate and migrate database tables (`ddl-auto=update`). Repository interfaces like `findByEmail` are auto-implemented, with zero SQL written by hand. | ORMs like Sequelize/TypeORM exist but require more manual configuration and don't match the convention-over-configuration depth of Spring Data JPA. |
| **Security** | `spring-boot-starter-security` gives password hashing, CORS config, and a security filter chain with minimal code. JWT integration is well-documented and battle-tested. | Doable with Passport.js/bcrypt/jsonwebtoken but requires assembling multiple packages and middleware wiring. |
| **Email sending** | `spring-boot-starter-mail` + `JavaMailSender` works natively with MIME/HTML emails, attachments, and SMTP config in `application.properties`. | Nodemailer works well too, but it's another third-party dependency. |
| **Concurrency model** | Java's thread-per-request model (Tomcat) is straightforward for our CRUD endpoints. No callback hell, no need for async/await discipline everywhere. | Single-threaded event loop is great for I/O-heavy apps (chat, streaming). Our app is database-CRUD-heavy with synchronous request-response, so Node.js's strength isn't leveraged. |
| **Academic alignment** | Java and OOP are core to our IIIT Lucknow curriculum (DSA, OOP, DBMS labs). The team has the deepest experience in Java. | Would require the team to learn a new server-side paradigm, increasing delivery risk. |

**Bottom line:** Spring Boot gives us a full-featured, type-safe, convention-driven backend where we write business logic instead of wiring libraries together. For a data-driven healthcare platform with structured models, relational queries, email, and auth, it's the natural fit.

---

### Q2. Why Spring Boot over plain Spring Framework?

| Factor | Spring Boot (chosen) | Vanilla Spring |
|--------|---------------------|----------------|
| **Configuration** | Auto-configuration detects the MySQL driver on classpath and configures the DataSource automatically. We only set the URL, username, and password. | Requires explicit `@Bean` definitions for DataSource, EntityManagerFactory, TransactionManager, DispatcherServlet, etc. Hundreds of lines of XML or Java config. |
| **Embedded server** | Ships with embedded Tomcat. `./mvnw spring-boot:run` starts the app with no external server install needed. | Requires packaging as a WAR, deploying to an external Tomcat/Jetty, and managing server configuration separately. |
| **Starter POMs** | `spring-boot-starter-web`, `spring-boot-starter-data-jpa`, `spring-boot-starter-mail` each pull in all transitive dependencies at compatible versions, with no version conflicts. | You manually pick and version-manage spring-web, spring-orm, hibernate-core, jackson, etc. Dependency conflicts are common. |
| **DevTools** | Hot reload during development (`spring-boot-devtools`). | Not available. Manual restart on every change. |
| **Production-readiness** | Actuator endpoints, externalized config (`application.properties`), and profile support are all built in. | All must be manually set up. |

**Bottom line:** Vanilla Spring is the foundation; Spring Boot is the opinionated layer that eliminates boilerplate. For a student project with time constraints, Boot saves dozens of hours of configuration work.

---

### Q3. Why Spring Boot over Django (Python)?

| Factor | Spring Boot (chosen) | Django |
|--------|---------------------|--------|
| **Type safety** | Java catches type errors at compile time. An `@Entity` with `private long id` is enforced by the compiler. | Python is dynamically typed. Django models are validated at runtime only. |
| **Performance** | JVM is significantly faster than CPython for CPU-bound operations. JIT compilation optimises hot paths. | Django/Python is slower for computation-heavy logic. Adequate for simple CRUD but doesn't scale as well under load. |
| **ORM flexibility** | JPA/Hibernate supports complex mappings, lazy loading, criteria queries, and native SQL. Spring Data auto-generates repository methods from method names. | Django ORM is excellent for simple queries but less flexible for complex joins and database-specific features without dropping to raw SQL. |
| **REST API style** | Spring `@RestController` with `@GetMapping` / `@PostMapping` is explicit and annotation-driven. Very readable. | Django REST Framework (DRF) is powerful but adds another layer of serializers, viewsets, and throttling config on top of Django. |
| **JWT auth** | We use `jjwt` library directly, giving full control over token creation, claims, and expiry. | Django typically uses `djangorestframework-simplejwt`. Works fine, but it's another third-party bolt-on. |
| **Team skill** | The team knows Java deeply from university coursework. | Python is familiar but server-side Django was new to most team members. |

**Bottom line:** Django is a great framework, but for a team proficient in Java building a structured healthcare API with strict data types and relational integrity, Spring Boot provides stronger compile-time guarantees and better performance.

---

### Q4. Why MySQL Over PostgreSQL?

| Factor | MySQL (chosen) | PostgreSQL |
|--------|---------------|------------|
| **Simplicity & ease of setup** | Easier to install, configure, and administer, especially for developers new to databases. Lower operational complexity. | More configuration options, which also means more ways to misconfigure things. Steeper learning curve. |
| **Performance on simple queries** | Faster out of the box for straightforward read-heavy workloads (SELECT, INSERT at scale). Powers massive platforms like Facebook and Twitter. | Optimized more for correctness than raw speed. Can be slower on simple OLTP workloads without tuning. |
| **Wider hosting support** | Supported on virtually every shared hosting provider, cPanel, and cloud platform. Near-universal compatibility. | PostgreSQL hosting is less ubiquitous, especially on budget/shared hosting tiers. |
| **Replication & read scaling** | MySQL's replication is battle-tested, simple to set up, and widely documented. Master-replica setups are straightforward. | Replication is more complex to configure correctly, covering logical vs. physical replication, replication slots, and WAL settings. |
| **Tooling & ecosystem maturity** | phpMyAdmin, MySQL Workbench, and a massive community of tutorials, Stack Overflow answers, and documentation. | pgAdmin is less polished. Community is smaller, with fewer beginner-friendly resources. |
| **Hibernate / Spring Boot defaults** | MySQL is often the assumed default in Spring Boot tutorials and starter projects, leading to fewer surprises when following guides. | Dialect mismatches and schema generation quirks can catch developers off guard. |
| **Familiarity in academia & bootcamps** | MySQL is still the most commonly taught database in university courses and bootcamps, so team ramp-up is faster. | PostgreSQL is gaining ground but MySQL remains the default teaching database. |

**Bottom line:** For a student team that needs fast onboarding, broad hosting compatibility, and a simple operational model, MySQL is the pragmatic choice. PostgreSQL's advanced features are compelling but largely unnecessary until the product matures. YAGNI applies.

---

### Q5. Why PostgreSQL over MongoDB?

| Factor | PostgreSQL (chosen) | MongoDB |
|--------|-------------------|---------|
| **Data model** | MedLink's data is inherently relational. Users have Appointments, Hospitals belong to Locations, and Contacts reference Users. These are table relationships with foreign keys, not loose documents. | MongoDB excels at document-oriented, schema-less data. But our entities (UserModel, PatientInfo, HospitalModel) have fixed, well-defined schemas. |
| **ACID transactions** | Full ACID compliance is critical for healthcare. An appointment must either be fully saved or not at all, with no partial writes. | MongoDB supports multi-document transactions (since 4.0), but they're slower and more complex than PostgreSQL's native transaction support. |
| **JPA / Hibernate support** | Spring Data JPA works natively with relational databases. `@Entity`, `@Table`, `@Column` map directly to relational tables. Auto-generated queries, pagination, and schema migration all work out of the box. | Spring Data MongoDB exists but uses a different programming model (`@Document`, `MongoRepository`). JPA annotations don't apply. Switching later would require rewriting all models and repositories. |
| **Query power** | SQL supports complex JOINs, aggregations, GROUP BY, and subqueries. Finding all appointments for a user at hospitals in a specific city is a single SQL query. | MongoDB's aggregation pipeline can do this but is more verbose and less intuitive for relational queries. |
| **Schema enforcement** | DDL constraints (`NOT NULL`, `UNIQUE`, foreign keys) are enforced at the database level, acting as a safety net below the application layer. | Schema validation exists in MongoDB but is opt-in and less mature. No foreign keys. |
| **Healthcare domain fit** | Healthcare data is structured by nature. Patient records, appointment slots, hospital beds, and contact forms all have fixed fields. Relational databases are the industry standard for healthcare (HL7, FHIR). | Document databases suit content management, IoT, and analytics pipelines better than structured medical records. |

**Bottom line:** Our data is relational, structured, and needs integrity guarantees. PostgreSQL is the right tool; MongoDB would force us to simulate relational behavior in a non-relational database.

---

### Q6. Why React over Angular / Vue / plain HTML-JS?

| Factor | React (chosen) | Angular | Vue | Vanilla JS |
|--------|---------------|---------|-----|------------|
| **Component model** | Functional components + hooks are lightweight and easy to reason about. Each UI section (HeroSection, AppointmentForm, PatientDashboard) is a self-contained component. | Full framework with modules, services, and dependency injection, meaning more ceremony per component. Overkill for our scale. | Similar component model to React but smaller ecosystem. | No component model. Code becomes unmanageable as the app grows. |
| **Ecosystem** | Massive: React Router, shadcn/ui, Radix UI, Tailwind integrations, lucide-react icons. Everything we need has a React binding. | Good ecosystem but Angular-specific libraries are fewer. Material UI is the main option. | Growing ecosystem but smaller than React's. | No ecosystem. Build everything from scratch. |
| **Learning curve** | Moderate: JSX is intuitive once understood. Hooks (`useState`, `useEffect`, `useContext`) cover 90% of needs. | Steep: TypeScript mandatory, RxJS observables, decorators, modules, services, zones. | Easy to learn but less employable as a skill. | Easy initially, unscalable quickly. |
| **Tailwind + shadcn/ui** | First-class support. shadcn/ui is built for React. Our entire UI uses its component primitives (Button, Card, Select, Accordion, DropdownMenu). | Tailwind works but shadcn/ui doesn't support Angular. A different component library would be needed. | Tailwind works, but shadcn/ui has limited Vue support. | Tailwind works, but would need manual JS for interactive components. |
| **Job market / academic relevance** | React is the most widely used frontend framework globally (Stack Overflow, npm trends). Most relevant for placements and internships. | Used in enterprise. Less common in startups and student projects. | Popular but third after React and Angular. | Not a framework. |
| **SPA routing** | React Router v6 provides declarative routes, nested layouts, and lazy loading with `React.lazy` and `Suspense`. | Built-in router but configuration-heavy. | Vue Router works well. | Manual `window.location` handling. |

**Bottom line:** React's component model, massive ecosystem, Tailwind/shadcn integration, and job-market relevance make it the clear choice for our frontend.

---

### Q7. Why JWT for authentication over sessions / OAuth?

| Factor | JWT (chosen) | Server-side sessions | OAuth 2.0 / OpenID |
|--------|-------------|---------------------|---------------------|
| **Stateless backend** | JWT is self-contained: the token carries the user's ID and email. The backend doesn't need to look up a session store on every request. Simpler architecture for a student project. | Requires a session store (in-memory, Redis, or database). Adds infrastructure complexity. | Requires an external identity provider (Google, Auth0). Adds external dependency and OAuth flow complexity. |
| **Frontend integration** | Token stored in `localStorage`, sent as `Authorization: Bearer <token>`. Simple to implement in `fetch()` calls. | Requires cookies with `httpOnly`, `secure`, `SameSite` flags. CORS cookie handling is tricky with separate frontend/backend origins. | More complex, involving redirect flows, token exchange, and refresh tokens. |
| **Our scale** | For a beta student project with a few hundred users, JWT's simplicity outweighs the marginal security benefits of sessions. | Better suited for large-scale apps where token revocation matters. | Overkill for our user base. No need for "Sign in with Google" yet. |
| **Email OTP verification** | Our flow is: register, receive OTP via email, verify, then get JWT. This is a custom auth flow that fits naturally with JWT issuance. | Would also work with sessions, but JWT lets us return a token immediately upon OTP verification. | Doesn't apply to our custom OTP flow. |

**Note:** JWT has tradeoffs (cannot revoke individual tokens without a blacklist, `localStorage` is accessible to XSS). For a beta student project, this is an acceptable tradeoff. A production system would add token refresh, `httpOnly` cookies, and a revocation list.

---

### Q8. Why Lombok in the backend?

Lombok eliminates boilerplate. Instead of writing 50+ lines of getters, setters, constructors, `toString()`, and `equals()/hashCode()` for each entity, we annotate with `@Data`, `@NoArgsConstructor`, `@AllArgsConstructor`. In a project with 4 entity classes (UserModel, HospitalModel, PatientInfo, ContactModel), Lombok saves approximately 200 lines of repetitive code and keeps models readable.

---

### Q9. Why Maven over Gradle?

Maven's XML-based `pom.xml` is verbose but explicit and well-understood. The Spring Initializr default is Maven, most Spring Boot tutorials use Maven, and the team had prior Maven experience from coursework. Gradle is faster for large multi-module builds, but for a single-module project like MedLink, the build speed difference is negligible. We chose familiarity and ecosystem alignment over marginal performance.
