---
layout: post
title: "Extending Thymeleaf dialect : processing attribute value"
categories: [springboot, thymeleaf, custom]
tags: [springboot, thymeleaf, custom]
---

# thymeleaf dialect 확장하여 태그 속성값을 변환하여 출력하기

타임리프 속성의 값으로 입력된 값을 처리하여 원하는 출력으로 변환하고자 한다.

`tlmd` dialect를 만들고 `ox` 속성을 추가한다.

속성에 입력한 값이 `true`에 해당하는 값(`0`이 아닌 숫자, `false`가 아닌 문자열 등) 이면 `●`을 출력하고,
`false`에 해당하는 값(숫자 `0`, `false`, `null` 등) 이면 `○`을 출력한다.

```html
<td tlmd:ox="${item.user.active_yn}">active_yn</td>
```

위와 같이 코딩을 하면 `${item.user.active_yn}`이 `1` 문자열을 가지고 있기 때문에 아래와 같이 화면에 출력된다.

```html
<td>●</td>
```

[타임리프 참조사이트](https://www.thymeleaf.org/doc/articles/sayhelloagainextendingthymeleafevenmore5minutes.html)

## custom dialect 클래스

`AbstractProcessorDialect`을 확장하여 `TlmdDialect` 클래스를 생성한다.

```java
public class TlmdDialect extends AbstractProcessorDialect {
	private static final String PREFIX = "tlmd";
	
	public TlmdDialect() {
		super("Ttallaemideul Dialect" // dialect name
				, PREFIX	// 접두사. 속성 사용방법: tlmd:*
				, 1000 		// dialect 우선순위
				);
	}

	/**
	 * dialect 처리기를 초기화 한다.
	 */
	@Override
	public Set<IProcessor> getProcessors(String dialectPrefix) {
		final Set<IProcessor> processors = new HashSet<>();
		processors.add(new OxAttrTagProcessor(dialectPrefix));
		return processors;
	}

}
```

[TlmdDialect.java](https://github.com/ttallaemideul/springboot/blob/master/tlmd_web/src/main/java/io/github/ttallaemideul/thymeleaf/TlmdDialect.java)

## 속성태그 처리기 클래스

`AbstractAttributeTagProcessor`을 확장하여 `OxAttrTagProcessor` 클래스를 생성한다.

오버라이드한 `doProcess` 함수에서 속성값(`attributeValue`)을 파싱 처리하여 값을 `attrVal` 획득한다.

`attrVal` 값이 `true`에 해당하면 `●`을 태그의 바디에 설정하고 아니면 `○`을 설정한다.

```java
@Override
protected void doProcess(ITemplateContext context, IProcessableElementTag tag, AttributeName attributeName,
		String attributeValue, IElementTagStructureHandler structureHandler) {
	// 속성값을 타임리프 표준 표현식으로 파싱한 후 표현식을 처리한다.
	final IEngineConfiguration configuration = context.getConfiguration();
	final IStandardExpressionParser parser = StandardExpressions.getExpressionParser(configuration);
	final IStandardExpression expression = parser.parseExpression(context, attributeValue);
	final Object attrVal = expression.execute(context);
	// 문자열이 true에 해당하는지 판단.
	if (UtilString.isTrue(attrVal)) {
		structureHandler.setBody(YES, false);
	} else {
		structureHandler.setBody(NO, false);
	}
}
```

`isTrue()` 함수는 파라미터의 값이 `true`에 해당하는 경우 `true`를 리턴한다.
`true`에 해당하는 문자열은 `0`이 아닌 숫자, `false`, `null`, `undefined` 문자열이 아닌 경우이다.

[OxAttrTagProcessor.java](https://github.com/ttallaemideul/springboot/blob/master/tlmd_web/src/main/java/io/github/ttallaemideul/thymeleaf/OxAttrTagProcessor.java)

## dialect 등록

MVC 설정시에 등록한 템플릿 엔진에 생성한 dialect 클래스 `TlmdDialect`를 등록한다.

```java
@Bean
public SpringTemplateEngine templateEngine() {
	SpringTemplateEngine templateEngine = new SpringTemplateEngine();
	templateEngine.setEnableSpringELCompiler(true); // Compiled SpringEL should speed up executions
	templateEngine.setTemplateResolver(templateResolver());
	templateEngine.addDialect(layoutDialect());	// 레이아웃 관리
	templateEngine.addDialect(new HelloDialect()); // 샘플 dialect
	templateEngine.addDialect(new TlmdDialect()); // tlmd dialect
	return templateEngine;
}
```

[WebMvcConfig.java](https://github.com/ttallaemideul/springboot/blob/master/tlmd_web/src/main/java/io/github/ttallaemideul/config/WebMvcConfig.java)

## dialect 사용

웹페이지에 `tlmd` dialect를 사용함을 명시한다.

```xml
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      xmlns:tlmd="https://ttallaemideul.github.io"
      layout:decorator="~{layouts/layout_base}">
```

`tlmd:ox` 태그를 적용한다.

```html
<tr th:each="item : ${userList}">
  <td th:text="${item.user.login_id}">login_id</td>
  <td th:text="${item.user.user_name}">user_name</td>
  <td tlmd:ox="${item.user.active_yn}">active_yn</td>
  <td tlmd:ox="${item.user.expired_yn}">expired_yn</td>
  <td tlmd:ox="${item.user.locked_yn}">locked_yn</td>
  <td tlmd:ox="${item.user.pwd_expired_yn}">pwd_expired_yn</td>
  <td th:text="${item.user.reg_dt}">reg_dt</td>
  <td th:text="${item.user.upd_dt}">upd_dt</td>
  <td>
  	<span th:each="role : ${item.roles}">
  		[[${role.roleNm}]]<small th:text="|(${role.desc})|"></small><br>
  	</span>
  </td>
</tr>
```

[admin_main.html](https://github.com/ttallaemideul/springboot/blob/master/tlmd_web/src/main/resources/templates/web/admin/admin_main.html)

## 화면 랜더링 결과

최종적으로 화면에 나타나는 결과물이다.
`정상` 컬럼은 데이터베이스에 값이 `1`이 저장되어 있기 때문에 `true`에 해당하여 `●`로 표시된다.

![tlmd:ox 적용 결과](/assets/tlmd_web/tlmd_web_002.PNG)

## `class`를 변경하기

태그 본문의 문자열을 처리하는 것에 추가적으로 태그 속성 중에 `class`를 변경하도록 하자.

`w3-css`를 사용하고 있기 때문에 `w3-color` 클래스를 이용할 것 이다.

[W3.CSS Colors](https://www.w3schools.com/w3css/w3css_colors.asp)

`●`를 표시할 때는 `w3-text-blue` 클래스를 이용하고 `○`를 표시할 때는 `w3-text-gray` 클래스를 이용한다.

`OxAttrTagProcessor` 클래스의 `doProcess()` 함수에서 `tag` 객체로 부터 `class` 속성을 얻어 그 값을 가져온다.
이 값에 html 태그의 기존 class 값이 들어있다.

```java
final IAttribute cls = tag.getAttribute(CLS);
String oldCls = null;
if (cls != null) {
	oldCls = cls.getValue();
}
```

태그의 바디를 설정할 때 `structureHandler.setAttribute()` 함수로 `class`를 설정하도록 한다.
이 때, 기존 클래스 `oldCls`가 존재하면 뒤에 추가하도록 한다.

최종적으로 완료된 `OxAttrTagProcessor.doProcess()` 함수이다.

```java
@Override
protected void doProcess(ITemplateContext context, IProcessableElementTag tag, AttributeName attributeName,
		String attributeValue, IElementTagStructureHandler structureHandler) {
	// 속성값을 타임리프 표준 표현식으로 파싱한 후 표현식을 처리한다.
	final IEngineConfiguration configuration = context.getConfiguration();
	final IStandardExpressionParser parser = StandardExpressions.getExpressionParser(configuration);
	final IStandardExpression expression = parser.parseExpression(context, attributeValue);
	final Object attrVal = expression.execute(context);
	final IAttribute cls = tag.getAttribute(CLS);
	String oldCls = null;
	if (cls != null) {
		oldCls = cls.getValue(); // 기존 class 값.
	}
	// 문자열이 true에 해당하는지 판단.
	if (UtilString.isTrue(attrVal)) {
		if (oldCls != null) {
			// 기존 클래스에 추가
			structureHandler.setAttribute(CLS, oldCls + " " + CLS_YES);
		} else {
			structureHandler.setAttribute(CLS, CLS_YES);
		}
		structureHandler.setBody(YES, false);
	} else {
		if (oldCls != null) {
			structureHandler.setAttribute(CLS, oldCls + " " + CLS_NO);
		} else {
			structureHandler.setAttribute(CLS, CLS_NO);
		}
		structureHandler.setBody(NO, false);
	}
}
```

화면은 이제 아래 이미지와 같이 클래스가 적용되어 표시된다.

![class가 적용된 화면](/assets/tlmd_web/tlmd_web_003.PNG)

생성된 html 코드를 보면 아래와 같이 경우에 따라 클래스가 추가되었고, 
기존 `test` 클래스가 태그에 있었던 첫 번째 `td` 태그에는 `test` 클래스 뒤에 추가되었다.

```html
 <td class="test w3-text-blue">●</td>
  <td class="w3-text-gray">○</td>
  <td class="w3-text-gray">○</td>
  <td class="w3-text-gray">○</td>
```

[타임리프 참조사이트](https://github.com/thymeleaf/thymeleafexamples-extrathyme)

## 샘플 코드

아래 샘플 프로젝트에서 이 문서에서 설명한 소스를 확인할 수 있다.
프로젝트를 받아서 직접 빌드하여 실행하고 결과를 확인해보면 도움이 될 것이다.

[github 샘플 프로젝트](https://github.com/ttallaemideul/springboot/tree/master/tlmd_web)
