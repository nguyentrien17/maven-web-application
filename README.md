# maven-web-application

Ứng dụng web Java (Spring MVC, đóng gói WAR) dùng để thực hành quy trình CI/CD với Maven, Jenkins, SonarQube, Docker, Kubernetes và Ansible. Mã gốc từ Mithun Technologies.

## Công nghệ

- Java 8, Maven, Spring 5.1.2, Servlet API
- Đóng gói: `war`
- Chạy trên Tomcat (Docker image `tomcat:8.0.20-jre8`)

## Cấu trúc

```
├── src/main/java/com/mt/services/EmployeeService.java  # REST endpoint /employee/getEmployeeDetails
├── src/main/webapp/        # home.jsp, web.xml, mt-servlet.xml
├── pom.xml
├── Dockerfile              # build image Tomcat chứa file WAR
├── docker-compose.yml      # chạy container, map cổng 9090 -> 8080
├── appdeploy.yaml, mavenwebappdeployment.yaml  # manifest Kubernetes
├── .github/workflows/maven.yml                 # GitHub Actions (JDK 11, mvn package)
└── Jenkinsfile*, Jenkinspipeline, Jenkinsparalleljob, JenkinswithAnsible  # các pipeline Jenkins
```

## Build và chạy

Yêu cầu: JDK 8+, Maven 3.x.

```bash
mvn clean package
```

File WAR nằm ở `target/maven-web-application.war`. Chép vào thư mục `webapps/` của Tomcat, hoặc dùng Docker:

```bash
docker build -t maven-web-application .
docker run -p 9090:8080 maven-web-application
```

Truy cập:

- Trang chủ: `http://localhost:9090/maven-web-application/`
- API: `http://localhost:9090/maven-web-application/employee/getEmployeeDetails`

## CI/CD

- **GitHub Actions** (`.github/workflows/maven.yml`): build bằng Maven khi push lên `master` và khi mở pull request vào `master`/`develop`.
- **Jenkins**: các pipeline gồm checkout, build, phân tích SonarQube, đẩy artifact lên Artifactory và triển khai WAR lên Tomcat qua SSH. Mỗi file `Jenkinsfile*` là một biến thể theo từng khóa học.

## Lưu ý bảo mật

`pom.xml` đang chứa địa chỉ và thông tin đăng nhập SonarQube viết cứng (`sonar.login`, `sonar.password`). Đây chỉ là mẫu thực hành. Không dùng cho môi trường thật, hãy chuyển sang biến môi trường hoặc credential của Jenkins.
