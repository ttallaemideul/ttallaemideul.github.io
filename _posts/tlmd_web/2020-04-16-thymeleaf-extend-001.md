---
layout: post
title: "Creating Thymeleaf Custom Tags"
categories: [springboot, thymeleaf, custom]
tags: [springboot, thymeleaf, custom]
---

# 타임리프 커스텀 태그 만들기

타임리프 커스텀 태그는 생성되는 태크에 속성을 부여하고 생성 전/후 처리, 표준 표현식에 대응되는 부분 등으로 구성되는 dialect를 생성하여 구현할 수 있다.

[참조사이트](https://www.thymeleaf.org/doc/articles/sayhelloextendingthymeleaf5minutes.html)

## 속성 처리기: 화면 표시 담당

`AbstractAttributeTagProcessor` 클래스를 확장하여 화면에 태그 표시를 담당하는 처리기를 작성한다.

```java
public class SayToAttrTagProcessor extends AbstractAttributeTagProcessor {

	private static final String ATTR_NAME = "sayto";
	private static final int PRECEDENCE = 10000;
	protected SayToAttrTagProcessor(String dialectPrefix) {
		super(
				TemplateMode.HTML		// HTML 모드로 설정
				, dialectPrefix						// 접두사
				,null										// 태그명 미설정. 적용가능 태그명에 제약이 없다.
				,false										// 태그명에 접두사를 적용하지 않는다.
				,ATTR_NAME						// 적용될 속성명
				,true										// 속성명에 dialect 접두사를 사용한다.
				,PRECEDENCE					//  dialect 들 내에서 적용 우선권.
				,true										// 적용한 속성명은 처리 후 삭제한다.
		);
	}

	@Override
	protected void doProcess(ITemplateContext context, IProcessableElementTag tag, AttributeName attributeName,
			String attributeValue, IElementTagStructureHandler structureHandler) {
		structureHandler.setBody("Hello, " + HtmlEscape.escapeHtml5(attributeValue) + "!", false);
	}
}
```

[SayToAttrTagProcessor ](https://github.com/ttallaemideul/springboot/blob/master/tlmd_web/src/main/java/io/github/ttallaemideul/sample/thymeleaf/SayToAttrTagProcessor.java)

## dialect 클래스 생성

dialect 클래스를 생성하여 타임리프에서 새롭게 만든 커스텀 태그를 사용할 수 있도록 해준다.
사용할 접두사는 `hello` 이고, 속성명은 `sayto`가 되어 화면에서 호출시에는 `hello:sayto`와 같이 사용한다.

```java
public class HelloDialect extends AbstractProcessorDialect {
	private static final String PREFIX = "hello";
	
	public HelloDialect() {
		super("Hello Dialect" // dialect name
				, PREFIX// 접두사. 속성 사용방법: hello:*
				, 1000 // dialect 우선순위
				);
	}

	/**
	 * dialect 처리기를 초기화 한다.
	 */
	@Override
	public Set<IProcessor> getProcessors(String dialectPrefix) {
		final Set<IProcessor> processors = new HashSet<>();
		processors.add(new SayToAttrTagProcessor(dialectPrefix));
		return processors;
	}

}
```

[HelloDialect ](https://github.com/ttallaemideul/springboot/blob/master/tlmd_web/src/main/java/io/github/ttallaemideul/sample/thymeleaf/HelloDialect.java)

## MVC 설정에 신규 dialect 추가

어플리케이션이 로드되어 타임리프 설정이 진행될 때 신규 dialect를 추가해준다.

```java
@Bean
public SpringResourceTemplateResolver templateResolver() {
	SpringResourceTemplateResolver templateResolver = new SpringResourceTemplateResolver();
	templateResolver.setPrefix("classpath:templates/");
	templateResolver.setSuffix(".html");
	templateResolver.setTemplateMode(TemplateMode.HTML);
	templateResolver.setCharacterEncoding("UTF-8");
	// TODO: Template cache is true by default. Set to false if you want
	// templates to be automatically updated when modified.
	templateResolver.setCacheable(true);
	return templateResolver;
}

@Bean
public SpringTemplateEngine templateEngine() {
	SpringTemplateEngine templateEngine = new SpringTemplateEngine();
	templateEngine.setEnableSpringELCompiler(true); // Compiled SpringEL should speed up executions
	templateEngine.setTemplateResolver(templateResolver());
	templateEngine.addDialect(new HelloDialect());
	return templateEngine;
}

@Bean
public ThymeleafViewResolver viewResolver() {
	ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
	viewResolver.setTemplateEngine(templateEngine());
	viewResolver.setCharacterEncoding("UTF-8");
	viewResolver.setOrder(0);
	return viewResolver;
}
```

[WebMvcConfig ](https://github.com/ttallaemideul/springboot/blob/master/tlmd_web/src/main/java/io/github/ttallaemideul/config/WebMvcConfig.java)

## 화면에서 신규 dialect 사용

`hello` 접두사가 html 편집기에서 작동되도록 추가한다.

```html
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      xmlns:hello="http://hello"
      layout:decorator="~{layouts/layout_base}">
```
신규 태그는 아래와 같이 사용한다.

```html
<p hello:sayto="커스텀 dialect">Hi ya!</p>
```

생성된 태그 결과는 다음과 같다.

```html
<p>Hello, 커스텀 dialect!</p>
```
	
## 샘플 코드

아래 샘플 프로젝트에서 이 문서에서 설명한 소스를 확인할 수 있다.
프로젝트를 받아서 직접 빌드하여 실행하고 결과를 확인해보면 도움이 될 것이다.

[github 샘플 프로젝트](https://github.com/ttallaemideul/springboot/tree/master/tlmd_web)