##User Manager — Spring Boot CRUD
학습용 사용자 관리 웹 애플리케이션입니다. 기존 Spring MVC + Hibernate 프로젝트를 Spring Boot로 마이그레이션하여 클래식 CRUD 시나리오를 최신 Spring Boot 스택으로 구현했습니다. (Habsida 교육 과정 과제)
설명
사용자 목록 조회, 등록, 수정, 삭제를 지원하는 관리자 화면이며 서버 측 검증을 적용했습니다.
이전 버전인 Spring MVC + Hibernate 프로젝트에서 마이그레이션하여, 최종 버전은 Spring Boot 자동 설정, 수동 DAO 대신 Spring Data JPA, 외부 배포 대신 내장 Tomcat을 사용합니다.
스택
Spring Boot 4.0.6 — 자동 설정, 내장 Tomcat, 의존성 관리
Spring Data JPA — CRUD를 자동 제공하는 리포지토리
Hibernate 7 + Jakarta Persistence — ORM
Hibernate Validator 9 — 서버 측 검증 (Bean Validation 3)
Thymeleaf 3 — 서버 사이드 렌더링
MySQL 8 — 데이터베이스
Maven — 빌드
Java 17
아키텍처
```
Controller (@Controller)
        ↓
Service (@Service @Transactional)   ← 비즈니스 로직, 트랜잭션
        ↓
Repository (extends JpaRepository)  ← 자동 생성되는 CRUD
        ↓
Entity (@Entity)                    ← 데이터 모델
        ↓
MySQL
```
이전 버전과 달리 DAO 계층을 완전히 제거했습니다. `JpaRepository`가 `save` / `findAll` / `findById` / `deleteById` 구현을 제공합니다.
Endpoints
Method	URL	설명
GET	`/`	`/users`로 redirect
GET	`/users`	사용자 목록
GET	`/users/new`	등록 폼
POST	`/users`	사용자 등록
GET	`/users/{id}/edit`	수정 폼
POST	`/users/{id}`	수정 내용 저장
POST	`/users/{id}/delete`	사용자 삭제
검증
서버 측 Bean Validation (`@Valid` + `BindingResult`):
이름 / 성 — 필수, 1~50자, 문자만 허용 (러시아어 또는 영어)
나이 — 필수, 1~150
클라이언트 측에서는 Thymeleaf 템플릿의 HTML5 속성(`pattern`, `required`)을 추가로 사용합니다.
실행
MySQL 8을 설치하고 데이터베이스를 생성합니다.
```sql
   CREATE DATABASE mvc_hibernate_db;
```
`src/main/resources/application.properties`에 본인의 비밀번호를 입력합니다.
```properties
   spring.datasource.password=YOUR_PASSWORD
```
애플리케이션을 실행합니다.
```bash
   mvn spring-boot:run
```
또는 IntelliJ IDEA에서 `UserManagerApplication` 클래스의 Run 버튼으로 실행합니다.
브라우저에서 http://localhost:8080/users 로 접속합니다.
최초 실행 시 Hibernate가 `users` 테이블을 자동 생성합니다 (`spring.jpa.hibernate.ddl-auto=update`).
프로젝트 구조
```
src/main/
├── java/io/slava/usermanager/
│   ├── controller/        — HTTP 엔드포인트
│   ├── service/           — 비즈니스 로직, 트랜잭션
│   ├── repository/        — Spring Data JPA 리포지토리
│   ├── model/             — JPA 엔티티
│   └── UserManagerApplication.java
└── resources/
    ├── application.properties
    └── templates/         — Thymeleaf 템플릿
```
Spring MVC + Hibernate에서 변경된 점
이전	이후
`WAR` 빌드, 외부 Tomcat 배포	`JAR` + 내장 Tomcat
수동 Java Config (`AppConfig`, `WebConfig`, `AppInit`)	`@SpringBootApplication` + `application.properties`
Hibernate 5.6 + `javax.persistence`	Hibernate 7 + `jakarta.persistence`
`UserDaoImpl` (`EntityManager` 직접 사용)	`UserRepository extends JpaRepository`
`DataSource`, `EntityManagerFactory`, `TransactionManager` 수동 설정	Spring Boot 자동 설정
`@Autowired` 필드 주입	생성자 주입
발전 과정
단계	저장소	내용
1	mvc-hibernate	Spring MVC + Hibernate CRUD
2	spring-boot-task (현재)	Spring Boot 마이그레이션, Spring Data JPA
3	spring-security-task	Spring Security 인증/인가
4	bootstrap-task	Bootstrap 5 UI
5	rest-api-task	REST API + JavaScript
