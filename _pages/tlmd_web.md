---
layout: post
title: "Sprint boot + Mybatis + H2 + Thymeleaf + W3.css"
permalink: /tlmd_web
sitemap:
  priority: 1.0
  changefreq: 'monthly'
  lastmod: '2020-04-18T09:00:00-00:00'
---

스프링부트 기반의 샘플 프로젝트를 구현하고자 한다.

Sprint boot + Mybatis + H2 + Thymeleaf + W3.css 을 사용하여 구현을 시작한다.

JSP 파일에서 JSTL을 이용하는데 익숙하지만 타임리프를 맛보기 정도로 습득하여 적용하고 테스트 해보니 
쉽게 개념을 이해할 수 있고 이삼일 정도면 기본적으로 사용하는데 문제가 없어 보이는 반면에 
JSTL 보다는 많은 기능을 제공한다고 생각된다.

특히, 자바스크립트 지원은 아주 훌륭하고 원하던 기능이다.

## Thymeleaf Javascript inlining

타임리프는 자바스크립트 내에서도 개발에 편리하고 개발시간을 단축할 수 있는 훌륭한 역할을 한다고 판단된다.

로컬에서 작업하던 소스 파일을 웹브라우저로 직접 오픈하여도 자바스크립트가 실행되는데 문제없도록 타임리프 태그를 
숨길 수 있는 방법을 제공하는 것이 아주 매력적이다.

* [타임리프 자바스크립트 사용 샘플](https://ttallaemideul.github.io/20200318/thymeleaf-js-001)

## Extending Thymeleaf dialects

타임리프 dialect를 확장하여 커스텀 속성, 태그를 만드는 방법이다.

* [dialect 확장하기](https://ttallaemideul.github.io/20200416/thymeleaf-extend-001)
* [dialect 확장 더 많이... 태그에 class 추가 하기](https://ttallaemideul.github.io/20200417/thymeleaf-extend-002)

## Thymeleaf 기본 문법

* [타임리프 URL 링크 걸기](https://ttallaemideul.github.io/20200418/thymeleaf-url-001)

## H2 디비 연동과 다중 데이터베이스 연결 지원.

메모리 디비가 지원되는 H2 데이터베이스를 연동하고 쿼리 실행 결과를 타임리프로 화면에 표시하는 샘플이다.

* [H2 디비 연동 샘플](https://ttallaemideul.github.io/20200411/h2-001)

## 관련 링크

* [Official Apache Maven documentation](https://maven.apache.org/guides/index.html)
* [Spring Boot Maven Plugin Reference Guide](https://docs.spring.io/spring-boot/docs/2.2.4.RELEASE/maven-plugin/)
* [Spring Boot DevTools](https://docs.spring.io/spring-boot/docs/2.2.4.RELEASE/reference/htmlsingle/#using-boot-devtools)
* [Spring Web](https://docs.spring.io/spring-boot/docs/2.2.4.RELEASE/reference/htmlsingle/#boot-features-developing-web-applications)
* [Spring Thymeleaf](https://docs.spring.io/spring-boot/docs/2.2.4.RELEASE/reference/htmlsingle/#boot-features-spring-mvc-template-engines)
* [Thymeleaf Docs.](https://www.thymeleaf.org/documentation.html)
* [MyBatis homepage](https://mybatis.org/mybatis-3/)
* [MyBatis-Spring](https://mybatis.org/spring/)
* [H2 Database Engine](https://www.h2database.com/)
* [W3.CSS homepage](https://www.w3schools.com/w3css/)



