## Controller Test

~~~java

@Controller
public class HomeController {
	
	private static final Logger logger = LoggerFactory.getLogger(HomeController.class);
	
	@RequestMapping( value = "/", method = RequestMethod.GET, 
			   produces ="application/json;charset=utf-8")
	public String home(Locale locale, Model model) {
		logger.info("Welcome home! The client locale is {}.", locale);
		Date date = new Date();
		DateFormat dateFormat 
			= DateFormat.getDateTimeInstance(DateFormat.LONG, DateFormat.LONG, locale);
		String formattedDate = dateFormat.format(date);
		model.addAttribute("serverTime", formattedDate );
		return "home";
	}
	
	//void 리턴 타입
	@RequestMapping("/doA")
	public void doA() {
		logger.info("/doA called...");
	}
	
	//String 리턴 타입
	@RequestMapping("/doC")
	public String doC(@ModelAttribute("msg") String msg) {
		logger.info("/doC called");
		return "result";
	}
	
	//String 리턴 타입
	@RequestMapping("/doD")
	public String doD(Model model) {
		ProductVO product = new ProductVO("desktop", 10000);
		logger.info("/doD called");
		model.addAttribute(product);
		return "product_detail";
	}
	
	//doF를 리다이렉트
	@RequestMapping("/doE")
	public String doE(RedirectAttributes redirectAttributes) {
		logger.info("/doE called and redirect to /doF");
		redirectAttributes.addAttribute("msg","this is the message with redirected");
		
		return "redirect:/doF";
	}
	
	@RequestMapping("/doF")
	public void doF(@ModelAttribute String msg) {
		logger.info("/doF called" + msg);
	}
	
	//json데이터를 생성하는 경우
	@RequestMapping("/doJson")
	@ResponseBody
	public ProductVO doJson() {
		ProductVO productVO = new ProductVO("laptop",300000);
		return productVO;
	}
}
~~~

~~~java
package com.web.test;

import static org.springframework.test.web.client.match.MockRestRequestMatchers.content;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.model;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.redirectedUrl;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

import javax.inject.Inject;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.test.context.web.WebAppConfiguration;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.ResultMatcher;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;

@RunWith(SpringJUnit4ClassRunner.class)
@WebAppConfiguration
@ContextConfiguration(locations = "file:src/main/webapp/WEB-INF/spring/appServlet/servlet-context.xml")
public class HomeControllerTest {
	
	private static final Logger logger = LoggerFactory.getLogger(HomeControllerTest.class);
	
	@Autowired
	private WebApplicationContext context;
	
	private MockMvc mockMvc;
	
	@Before
	public void setup() {
		this.mockMvc = MockMvcBuilders.webAppContextSetup(this.context).build();
		logger.info("setup!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!");
	}
	
	/*
	@Test
	public void test() throws Exception {
		this.mockMvc
		//GET 방식으로 "/" URL을 호출한다
		.perform(get("/"))
		//처리내용을 출력한다
		.andDo(print())
		//status 값이 정상인 경우를 기대하고 만든 체이닝 메소드 중 일부입니다.
		.andExpect(status().isOk())	
		//model 속성에서 "serverTime"이 존재하는지 검증한다
		.andExpect(model().attributeDoesNotExist("serverTime"))
		//model 속성에서 "serverTime"의 값이 원하는 값으로 세팅되어 있는지 검증한다.
		.andExpect(model().attribute("serverTime", "22"))
		//contentType을 검증합니다.
		.andExpect((ResultMatcher) content().contentType("application/json;charset=utf-8"));
	}
	
	@Test
	public void testDoA() throws Exception {
		mockMvc.perform(MockMvcRequestBuilders.get("/doA"))
		.andDo(print())
		.andExpect(status().isOk());
	}
	
	@Test
	public void testDoC() throws Exception {
		mockMvc.perform(MockMvcRequestBuilders.get("/doC?msg=world"))
		.andDo(print())
		.andExpect(status().isOk());
	}
	
	@Test
	public void testDoD() throws Exception {
		mockMvc.perform(MockMvcRequestBuilders.get("/doD"))
		.andDo(print())
		.andExpect(status().isOk())
		.andExpect(model().attributeExists("productVO"));
	}
	
	@Test
	public void testDoE() throws Exception {
		mockMvc.perform(MockMvcRequestBuilders.get("/doE"))
		.andDo(print())
		.andExpect(status().is3xxRedirection())
		.andExpect(redirectedUrl("doF?msg=this+is+the+message+with+redirected"));
	} */
	
	@Test
	public void testDoJson() throws Exception {
		mockMvc.perform(MockMvcRequestBuilders.get("/doJson"))
		.andDo(print())
		.andExpect(status().isOk())
		.andExpect((ResultMatcher) content().contentType("application/json;charset=utf-8"));
	}
}
~~~

### @RunWith(SpringJUnit4ClassRunner.class)
  - @RunWith는 JUnit 프레임워크의 테스트 실행 방법을 확장할 때 사용하는 애노테이션.
  - SpringJUnit4ClassRunner라는 JUnit용 테스트 컨텍스트 프레임워크 확장 클래스를 지정  
    -> JUnit이 테스트 진행하는 중에 테스트가 사용할 애플리케이션 컨텍스트를 만들과 관리하는 작업을 진행해줌.
  - @RunWith에 Runner클래스를 설정하면 JUnit에 내장된 Runner대신 그 클래스를 실행한다. 
    여기서는 스프링 테스트를 위해서 SpringJUnit4ClassRunner라는 Runner 클래스를 설정해 준 것이다.

### @WebAppConfiguration
  - 프로젝트의 `web.xml`이 아닌 가상의 `web.xml`을 사용하겠다는 의미이다.

### @ContextConfiguration
  - 자동으로 만들어줄 애플리케이션 컨텍스트의 설정파일위치를 지정한 것이다.
  - 스프링의 JUnit 확장기능은 테스트가 실행되기 전에 딱 한 번만 애플리케이션 컨텍스트를 만들어두고, 
    테스트 오브젝트가 만들어질 때마다 특별한 방법을 이용해 애플리케이션 컨텍스트 자신을 테스트 오브젝트의 
    특정 필드에 주입해주는 것이다.
  - 한 클래스내에 여러개의 테스트가 있더라도 어플리케이션 컨텍스트를 초기 한번만 로딩하여 사용하기 때문에,
    여러개의 테스트가 있더라도 처음 테스트만 조금 느리고 그 뒤의 테스트들은 빠르다.

### @Before
  - 단위테스트 시작 전 공통적으로 초기화되는 코드

### @After
  - 단위테스트 종료 후 공통적으로 적용될 로직

### @Test
  - 테스트 기본 단위

#### 테스트 코드를 작성하고 테스트하는 것의 장정

1. 웹페이지를 테스트하려면 매번 입력항목을 입력해서 제대로 동작하는지 확인하는데, 여러번 웹페이지를 입력하는 것보다 테스트 코드를 통해 처리하는 것이 개발 시간을 단축할 수 있다.
2. JSP 등에서 발생하는 에러를 해결하는 과정에서 매번 WAS에 만들어진 Controller 코드를 수정해서 배포하는 작업은 많은 시간을 소모한다.
3. Controller에서 결과 데이터만을 확인할 수 있기 때문에 문제 발생시 원인을 파악할 수 있는 시간이 절약된다.

