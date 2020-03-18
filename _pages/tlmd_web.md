---
layout: post
title: "Sprint boot + Mybatis + H2 + Thymeleaf + W3.css"
permalink: /tlmd_web
sitemap:
  priority: 1.0
  changefreq: 'monthly'
  lastmod: '2020-03-18T09:00:00-00:00'
---

스프링부트 기반의 샘플 프로젝트를 구현하고자 한다.

Sprint boot + Mybatis + H2 + Thymeleaf + W3.css 을 사용하여 구현을 시작한다.

JSP 파일에서 JSTL을 이용하는데 익숙하지만 타임리프를 맛보기 정도로 습득하여 적용하고 테스트 해보니 
쉽게 개념을 이해할 수 있고 이삼일 정도면 기본적으로 사용하는데 문제가 없어 보이는 반면에 
JSTL 보다는 많은 기능을 제공한다고 생각된다.

특히, 자바스크립트 지원은 아주 훌륭하고 원하던 기능이다.

## 관련 링크

* [Official Apache Maven documentation](https://maven.apache.org/guides/index.html)
* [Spring Boot Maven Plugin Reference Guide](https://docs.spring.io/spring-boot/docs/2.2.4.RELEASE/maven-plugin/)
* [Spring Boot DevTools](https://docs.spring.io/spring-boot/docs/2.2.4.RELEASE/reference/htmlsingle/#using-boot-devtools)
* [Spring Web](https://docs.spring.io/spring-boot/docs/2.2.4.RELEASE/reference/htmlsingle/#boot-features-developing-web-applications)
* [Spring Thymeleaf](https://docs.spring.io/spring-boot/docs/2.2.4.RELEASE/reference/htmlsingle/#boot-features-spring-mvc-template-engines)
* [Thymeleaf Docs.](https://www.thymeleaf.org/documentation.html)


## Thymeleaf Javascript inlining

타임리프는 자바스크립트 내에서도 개발에 편리하고 개발시간을 단축할 수 있는 훌륭한 역할을 한다고 판단된다.

로컬에서 작업하던 소스 파일을 웹브라우저로 직업 오픈하여도 자바스크립트가 실행하는데 문제없도록 타임리프 태그를 
숨길 수 있는 방법을 제공하는 것이 아주 매력적이다.

* [타임리프 자바스크립트 사용 샘플](https://ttallaemideul.github.io/20200318/thymeleaf-js-001)

