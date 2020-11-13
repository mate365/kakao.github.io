---
layout: post
title: "간단한 .Net 앱을 배포해 보면서 둘러본 Azure"
author: youngbin han
date: 2020-11-03 11:00
tags: [Azure, Microsoft Azure, Azure DevOps, App Service]
---

이번에 고객사가 기획중인 서비스에 RESTful API 백엔드가 필요하게 되어 백엔드 개발과 Azure 에서 배포와 운영을 어떻게 할지 등을 계획하여 진행하게 되었는데, 이를 통해 간단한 .Net 백엔드를 만들어 Azure 에 배포해 보면서 웹앱 배포와 관련된 제품을 사용해 보았다.
백엔드는 .Net 기반으로 개발하기로 했는데, 기획안 중, 키오스크 기획 부분에 키넥트 장비와 C# 기반 Unity 프로그램이 들어가는 점, 
프로젝트에 같이 참여하는 같은 시기 입사한 경력자 분이 .Net에 정통한 경력자 분 이신것도 한 몫 한 것 같다.
결론부터 말하자면, 프로젝트는 제대로 시작 하기도 전에 고객사의 사정으로(?) 틀어졌다. 
그래도 한달 가까운 시간동안 처음 접해보는 .Net 도 깊게는 다뤄보진 못했지만, 간단한 RESTful API 백엔드도 만들어 봤고 Azure 에 배포 해보면서 써본 제품도 다양하니, 그냥 잊고 지나가기 보단 글로 한번 정리해 보는 것도 좋을 것 같아 글을 써 보게 되었다.

# 그냥 데이터만 간단히 쌓아주는 백엔드
라고 하지만, 그렇게 간단할리가 없다. 아래 그림을 한번 보자.
![](/files/blog/2020-11-03/somethingmissing0.png)
뭔가 허전하지 않은가? 어떻게 앱이 MySQL DB 와 바로 통신을? 중간에 백엔드가 있어야 할 것 같지 않은가?
그 간단(할 것 같은) .Net 백엔드를 만들어 Azure 에 배포 하는것이 미션 이였다. 위 그림을 보면 그 다음도 조금은 예상이 가능하겠지만, 
백엔드에 대해서도 어떤 기능이 필요한 지 정의가 부족했다. 고객사와의 소통을 통해 필요한 기능을 정의하는 것이 먼저였다.

고객사와 메일로 소통을 통해 요구사항을 구체화 했고, 동시에 어떤 도구를 활용해서 어떻게 만들어 배포할지 논의했다.
Azure 제품 중에서는 Front door, API Management, App Service, Database for MySQL 를 이용하여 배포와 운영을 하고
개발 과정에서 버전 관리와 CI/CD, 문서화 등은 Azure DevOps 를 활용하기로 했다. 아직 어떤 기능을 개발해야 할 지 확정된 것은 아니기 때문에,
백엔드 개발에 사용할 .Net Core 와 Azure 제품을 사용해 보면서 일정이 시작되면 어떻게 진행할지 검토하는 시간을 가졌다.

.Net 기반 백엔드 개발에는 .Net 기반 백엔드 개발에 많이 사용하는 ASP.NET Core, DB ORM 프레임워크인 EF Core를 검토했고,
추가적으로 개발 일정이 시작 되었을 때 미리 설계한 API 명세로 코드를 자동 생성하여 빠르게 진행할 수 있게 Swagger Codegen,
그리고 Swagger UI로 API 문서를 자동 생성하는 ASP.NET 미들웨어인 Swashbuckle 까지 사용해 보는 시간을 가졌다.

# ASP.NET Core 백엔드 만들기
개인적으로 Node.js, Python(Flask), Go(Gin) 으로 간단히 RESTful API 백엔드를 만들어 본 경험이 있고. 최근 들어서야 Java와 Spring을 사용해 보기 시작했는데, Spring은 앞에서 언급한 3가지 프레임워크와는 다르게 시작부터 간단하진 않았다. 전에는 그냥 라우팅 간단히 설정하고 각 라우팅별로 함수 연결해서 각 함수에서 처리할 내용을 넣으면 간단히 시작이 가능하고, 필요하면 미들웨어 작성해서 끼워넣어주거나, 상황에 따라 적절한 패키지를 활용하여 조합했었다. Spring은 듣던대로 대규모 시스템에 많이 사용해서 그런지, 처음 생성된 프로젝트 코드부터 구조가 잡혀있고 말로만 듣던 제어 반전(Inversion of Control), 종속성 주입(Dependency Injection) 을 주로 활용하는 프레임워크여서 그런지 익숙해지기 쉽진 않았다. 써보면서 느낀 점은 확실히 규모 큰 프로젝트 할 수록, 유지보수 하기엔 좋을 것 같은 느낌을 받았다. 

그리고 이제 ASP.NET Core. 이걸 처음 코드를 보면서 느낌 점은 Spring Framework 쓰는것과 비슷한 느낌 이라는 것이다. Spring 처럼 제어 반전과 종속성 주입을 집중접으로 활용하는 모습. 
Java 에서는 Annotation 에 해당하는 C#의 특성(Attribute) 를 클래스나 함수에 붙여서 API 컨트롤러나 데이터베이스 모델로 설정하는 것 등. 많은 점이 닯은 모습이였다. 
아래 간단한 RESTful API Controller 예제 코드를 비교해 보면, 괄호 쓰는 방법 같은 코딩 스타일이나 약간의 문법 차이만 제외하면 비슷한 구조임을 알 수 있다. 
ASP.NET 과 Spring 이 비슷한 면이 많아서 그런지, Spring 에 익숙해 지기 좀 어려웠던 것 처럼 .Net 에 익숙해지기도 쉽지는 않았다.

```java
// Java, Spring
@RestController
@RequestMapping("/student")
public class ThirdController {

    // MyBatis Mapper
    @Autowired StudentMapper studentMapper;
    
    @GetMapping("/get", @RequestParam("id") int id)
    public Student test2(Model model){
        Student student = studentMapper.findById(id);
        return student;
    }

    @PostMapping("/new")
    public Student test2(Model model, Student student){
        studentMapper.insert(student);
        return student;
    }

    @PutMapping("/update", @RequestParam("id") int id)
    public String test2(Model model, Student student){
        studentMapper.update(student);
        return "updated";
    }

    @DeleteMapping("/delete")
    public String test2(Model model, @RequestParam("id") int id){
        studentMapper.delete(id);
        return "deleted";
    }
}
```
```cs
// C#, ASP.NET
namespace StudentApi.Controllers
{
    [Route("[controller]")]
    [ApiController]
    public class StudentsController : ControllerBase
    {
        // EF Core Database context
        private readonly StudentContext _context;

        public StudentsController(StudentContext context)
        {
            _context = context;
        }

        [HttpGet("/get/{id}")]
        public async Task<ActionResult<Student>> GetById(long id)
        {
            var product = await _context.Students.FindAsync(id);
            if (product == null)
            {
                return NotFound();
            }
            return product;
        }

        // POST action
        [HttpPost("/new")]
        [Consumes(MediaTypeNames.Application.Json)]
        public async Task<ActionResult<Student>> Create(Student student)
        {
            _context.Students.Add(student);
            await _context.SaveChangesAsync();
            return CreatedAtAction(nameof(GetById), new { id = student.Id }, student);
        }

        // PUT action
        [HttpPut("/update/{id}")]
        [Consumes(MediaTypeNames.Application.Json)]
        public async Task<ActionResult<Student>> Update(long id, Student student)
        {
            if (id != student.Id)
            {
                return BadRequest();
            }
            _context.Entry(student).State = EntityState.Modified;
            await _context.SaveChangesAsync();
            return NoContent();
        }

        // DELETE action
        [HttpDelete("/delete/{id}")]
        public async Task<IActionResult> Delete(long id)
        {
            var product = await _context.Students.FindAsync(id);
            if (product == null)
            {
                return NotFound();
            }
            _context.Students.Remove(product);
            await _context.SaveChangesAsync();
            return NoContent();
        }
    }
}
```
# Open API 명세로 코드를, 코드에서 다시 API 문서 생성하기.
고객사와 논의를 통해 API 명세가 확정되면 이를 기반으로, 빠르게 구현을 시작하기 위해 Open API 명세에서 코드를 자동으로 생성하는 도구와.
작성된 코드에 연동하면 API 문서를 자동으로 생성하는 도구도 같이 검토했다. 그렇게 검토한 것이 Swagger Codegen과 Swagger UI 이다.
Swagger UI는 API 문서 생성 자동화를 위해 이미 많이 사용하는 경우가 많아 익숙할 것이다. Swagger Codegen은? 이름에서 유추할 수 있듯. 
Open API 명세(혹은 Swagger API 명세) 에서 RESTful API 코드를 자동으로 생성하는 도구이다. 

다양한 프로그래밍 언어와 웹 프레임워크 코드 생성을 지원하고, 준비된 API 명세 파일을 넣어서 실행하면, API 명세에 정의된 대로 서버측 코드를 생성해준다. 
생성된 코드에는 API를 호출 해 볼수 있는 정도로만 구현 되어 있고, 실제 API가 할 동작과 나머지 다른 기능(DB 관련 기능, 메일전송, 인증)을 개발자가 구현해 주면 된다. 
Spring 이나 ASP.NET 같은 프레임워크는 프로젝트 초기화와 구성부터 대략적인 API 컨트롤러 함수 작성 해 두는 데 까지 비교적 복잡한 편이다 보니 시간도 좀 필요한 편인데, 
(요세는 그래도 Spring 쪽은 Spring Boot 가 나오고, .Net 은 .Net CLI 로 스캐폴드가 가능해서 많이 간단해졌다) 
Swagger Codegen 에 미리 작성한 API 명세를 넣고 실행하여 코드를 생성함으로써, 이 과정 전체를 건너뛸 수 있다.

아래와 같은 Open API 3.0 명세 예제에서 코드를 자동 생성해 보자. Student 라는 데이터를 만들고, 읽고, 고치고, 지우는 간단한 API 명세이다. 
![](/files/blog/2020-11-03/apidoc.png)
```yaml
openapi: 3.0.1
info:
  title: StudentApi
  version: '1.0'
paths:
  "/Students":
    get:
      tags:
      - Students
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                type: array
                items:
                  "$ref": "#/components/schemas/Student"
    post:
      tags:
      - Students
      requestBody:
        content:
          application/json:
            schema:
              "$ref": "#/components/schemas/Student"
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                "$ref": "#/components/schemas/Student"
  "/Students/{id}":
    get:
      tags:
      - Students
      parameters:
      - name: id
        in: path
        required: true
        schema:
          type: integer
          format: int64
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                "$ref": "#/components/schemas/Student"
    put:
      tags:
      - Students
      parameters:
      - name: id
        in: path
        required: true
        schema:
          type: integer
          format: int64
      requestBody:
        content:
          application/json:
            schema:
              "$ref": "#/components/schemas/Student"
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema:
                "$ref": "#/components/schemas/Student"
    delete:
      tags:
      - Students
      parameters:
      - name: id
        in: path
        required: true
        schema:
          type: integer
          format: int64
      responses:
        '200':
          description: Success
components:
  schemas:
    StudentStatus:
      enum:
      - 0
      - 1
      - 2
      type: integer
      format: int32
    Student:
      required:
      - major
      - name
      type: object
      properties:
        id:
          type: integer
          format: int64
        name:
          type: string
        major:
          type: string
        status:
          "$ref": "#/components/schemas/StudentStatus"
      additionalProperties: false

```
아래 명령을 실행하여, Swagger Codegen을 받고 코드를 자동으로 생성하자. 실행하려면, Java가 필요하다. 
`studentapi` 디렉토리에, [명령 실행 시 버전 옵션을 넣지 않았으므로, 글 작성 시점 기준 .Net Core 3.1 코드가 생성된다.](https://github.com/swagger-api/swagger-codegen-generators/blob/b4261015ab25bf7c206e7e87b9f5e1c0ff1efb17/src/main/java/io/swagger/codegen/v3/generators/dotnet/AspNetCoreServerCodegen.java#L33)

```bash
# Wget 으로 Swagger Codegen *.jar 파일 받기
wget https://repo1.maven.org/maven2/io/swagger/codegen/v3/swagger-codegen-cli/3.0.23/swagger-codegen-cli-3.0.23.jar -O swagger-codegen-cli.jar

mkdir studentapi

# Swagger Codegen 으로 서버 코드 생성
# -i : API 명세 파일 또는 URL 지정
# -l : ASP.NET Core 코드를 생성하도록 지정
# -o : 지정 디렉토리에 생성된 코드 저장
java -jar swagger-codegen-cli.jar generate \
  -i spec.yml -l aspnetcore -o studentapi 
```

![](/files/blog/2020-11-03/codegenfiles.png)   
그러면 위와 같은 프로젝트와 코드가 생성된다. 이 글에서 코드를 다 보여주긴 어려우니, `StudentsApi.cs` 컨트롤러 파일만 한번 보자.

```cs
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Mvc;
using Swashbuckle.AspNetCore.Annotations;
using Swashbuckle.AspNetCore.SwaggerGen;
using Newtonsoft.Json;
using System.ComponentModel.DataAnnotations;
using IO.Swagger.Attributes;

using Microsoft.AspNetCore.Authorization;
using IO.Swagger.Models;
namespace IO.Swagger.Controllers
{ 
    /// <summary>
    /// 
    /// </summary>
    [ApiController]
    public class StudentsApiController : ControllerBase
    { 
        /// <summary>
        /// 
        /// </summary>
        /// <response code="200">Success</response>
        [HttpGet]
        [Route("/Students")]
        [ValidateModelState]
        [SwaggerOperation("StudentsGet")]
        [SwaggerResponse(statusCode: 200, type: typeof(List<Student>), description: "Success")]
        public virtual IActionResult StudentsGet()
        { 
            //TODO: Uncomment the next line to return response 200 or use other options such as return this.NotFound(), return this.BadRequest(..), ...
            // return StatusCode(200, default(List<Student>));
            string exampleJson = null;
            exampleJson = "[ {\n  \"major\" : \"major\",\n  \"name\" : \"name\",\n  \"id\" : 0,\n  \"status\" : 6\n}, {\n  \"major\" : \"major\",\n  \"name\" : \"name\",\n  \"id\" : 0,\n  \"status\" : 6\n} ]";
            
                        var example = exampleJson != null
                        ? JsonConvert.DeserializeObject<List<Student>>(exampleJson)
                        : default(List<Student>);            //TODO: Change the data returned
            return new ObjectResult(example);
        }

        /// <summary>
        /// 
        /// </summary>
        /// <param name="id"></param>
        /// <response code="200">Success</response>
        [HttpDelete]
        [Route("/Students/{id}")]
        [ValidateModelState]
        [SwaggerOperation("StudentsIdDelete")]
        public virtual IActionResult StudentsIdDelete([FromRoute][Required]long? id)
        { 
            //TODO: Uncomment the next line to return response 200 or use other options such as return this.NotFound(), return this.BadRequest(..), ...
            // return StatusCode(200);

            throw new NotImplementedException();
        }

        /// <summary>
        /// 
        /// </summary>
        /// <param name="id"></param>
        /// <response code="200">Success</response>
        [HttpGet]
        [Route("/Students/{id}")]
        [ValidateModelState]
        [SwaggerOperation("StudentsIdGet")]
        [SwaggerResponse(statusCode: 200, type: typeof(Student), description: "Success")]
        public virtual IActionResult StudentsIdGet([FromRoute][Required]long? id)
        { 
            //TODO: Uncomment the next line to return response 200 or use other options such as return this.NotFound(), return this.BadRequest(..), ...
            // return StatusCode(200, default(Student));
            string exampleJson = null;
            exampleJson = "{\n  \"major\" : \"major\",\n  \"name\" : \"name\",\n  \"id\" : 0,\n  \"status\" : 6\n}";
            
                        var example = exampleJson != null
                        ? JsonConvert.DeserializeObject<Student>(exampleJson)
                        : default(Student);            //TODO: Change the data returned
            return new ObjectResult(example);
        }

        /// <summary>
        /// 
        /// </summary>
        /// <param name="id"></param>
        /// <param name="body"></param>
        /// <response code="200">Success</response>
        [HttpPut]
        [Route("/Students/{id}")]
        [ValidateModelState]
        [SwaggerOperation("StudentsIdPut")]
        [SwaggerResponse(statusCode: 200, type: typeof(Student), description: "Success")]
        public virtual IActionResult StudentsIdPut([FromRoute][Required]long? id, [FromBody]Student body)
        { 
            //TODO: Uncomment the next line to return response 200 or use other options such as return this.NotFound(), return this.BadRequest(..), ...
            // return StatusCode(200, default(Student));
            string exampleJson = null;
            exampleJson = "{\n  \"major\" : \"major\",\n  \"name\" : \"name\",\n  \"id\" : 0,\n  \"status\" : 6\n}";
            
                        var example = exampleJson != null
                        ? JsonConvert.DeserializeObject<Student>(exampleJson)
                        : default(Student);            //TODO: Change the data returned
            return new ObjectResult(example);
        }

        /// <summary>
        /// 
        /// </summary>
        /// <param name="body"></param>
        /// <response code="200">Success</response>
        [HttpPost]
        [Route("/Students")]
        [ValidateModelState]
        [SwaggerOperation("StudentsPost")]
        [SwaggerResponse(statusCode: 200, type: typeof(Student), description: "Success")]
        public virtual IActionResult StudentsPost([FromBody]Student body)
        { 
            //TODO: Uncomment the next line to return response 200 or use other options such as return this.NotFound(), return this.BadRequest(..), ...
            // return StatusCode(200, default(Student));
            string exampleJson = null;
            exampleJson = "{\n  \"major\" : \"major\",\n  \"name\" : \"name\",\n  \"id\" : 0,\n  \"status\" : 6\n}";
            
                        var example = exampleJson != null
                        ? JsonConvert.DeserializeObject<Student>(exampleJson)
                        : default(Student);            //TODO: Change the data returned
            return new ObjectResult(example);
        }
    }
}

```

이 글의 초반에 보여준 예제 컨트롤러 코드와 같은 형태의 코드가 생성된 것을 볼 수 있다. 다만, 앞서 예기한 것 처럼 내부에 로직이 구현되어 있지는 않고, 
테스트 해 볼 수 있도록 샘플 데이터만 반환 하도록 되어 있다. 추가적으로 각 함수마다 Swagger 를 통한 API 문서화를 위해 각 함수마다 주석과 함께 Swashbuckle이 제공하는 각종 속성이 붙어있는 것을 볼 수 있다. 
이렇게 미리 구현된 각 메소드 내부에 추후 로직을 구현하는 방식으로 초반에 시간을 아낄 수 있다.

위 코드의 각 메소드에 Swashbuckle이 제공하는 특성이 붙은걸 보면 짐작할 수 있겠지만, Swagger Codegen으로 생성한 코드에는 이미 Swashbuckle까지 설정이 되어 있다.
프로그램 실행 시 각종 서비스와 플러그인 등을 구성하는 `Startup.cs` 의 `ConfigureServices()`, `Configure()` 를 보면 이를 확인할 수 있다.

```cs
...
public void ConfigureServices(IServiceCollection services)
{
    ...
    services
        .AddSwaggerGen(c =>
        {
            c.SwaggerDoc("1.0", new OpenApiInfo
            {
                Version = "1.0",
                Title = "StudentApi",
                Description = "StudentApi (ASP.NET Core 3.1)",
                Contact = new OpenApiContact()
                {
                   Name = "Swagger Codegen Contributors",
                   Url = new Uri("https://github.com/swagger-api/swagger-codegen"),
                   Email = ""
                },
                TermsOfService = new Uri("")
            });
            c.CustomSchemaIds(type => type.FullName);
            c.IncludeXmlComments($"{AppContext.BaseDirectory}{Path.DirectorySeparatorChar}{_hostingEnv.ApplicationName}.xml");
            // Include DataAnnotation attributes on Controller Action parameters as Swagger validation rules (e.g required, pattern, ..)
            // Use [ValidateModelState] on Actions to actually validate it in C# as well!
            c.OperationFilter<GeneratePathParamsValidationFilter>();
        });
}
public void Configure(IApplicationBuilder app, IWebHostEnvironment env, ILoggerFactory loggerFactory)
{
    ...
    app.UseSwagger();
    app.UseSwaggerUI(c =>
    {
        //TODO: Either use the SwaggerGen generated Swagger contract (generated from C# classes)
        c.SwaggerEndpoint("/swagger/1.0/swagger.json", "StudentApi");
        //TODO: Or alternatively use the original Swagger contract that's included in the static files
        // c.SwaggerEndpoint("/swagger-original.json", "StudentApi Original");
    });
    ...
}
...
```

# EF Core ORM 코드도 자동으로 생성하기
데이터를 쌓으려면 당연히 DB도 필요하고, ORM이나 Query Mapper, DB 드라이버도 필요하다. .Net 쪽에서는 주로 EF Core라는 ORM을 많이 사용해서,
이를 사용하는 방향으로 검토했다. EF Core 의 경우에는 이미 DB 테이블 스키마마 만들어져 있는 DB 서버에 접속해서 이를 C# 코드로 리버스 엔지니어링 하는 기능이 있다.
마치 Swagger Codegen 이 하는 역할과 비슷한 기능이다. 그래서 검토한 방안은 같이 프로젝트 하는 .Net 뿐만 아니라 데이터베이스까지 잘 다루시는 정우님께서 MySQL 서버에 스키마를 짜서 올려두면, 
이를 .Net CLI 의 EF Core 관련 기능으로 리버스 엔지니어링 해서 C# 코드로 변환하는 것이다. 그러면 EF Core 의 쿼리 함수로 처리 가능한 부분은 컨트롤러 부분에서 호출해서 사용하고, 
이걸로 부족할 정도로 복잡한 쿼리는 정우님께서 SQL 질의나 프로시저로 처리해 주시는 방법으로 하기로 했다.

위에서 다룬 Student API 에 사용할 테이블을 하나 작업한다 가정하고 예를 들어 설명해 보자. 먼저 MySQL DB 서버가 하나 필요하다.
테스트용 Azure Database for MySQL 서버를 하나 배포해서 사용해 보자. Azure Portal에 접속하여 Azure Database for MySQL 서버를 검색한 후, 새로 생성 화면을 들어가면.
아래 화면이 바로 나오진 않고, 단일 서버와 유연한 서버(미리보기) 선택지가 나온다. 유연한 서버는 아직 미리보기 단계여서 SLA 등 보장이 없으니 단일 서버를 선택하자.

![](/files/blog/2020-11-03/azuremysql.png)

그리고 목적에 맞게 리소스 그룹, 백업 여부, 위치, 버전, 성능 등을 설정해 주자. 필자의 경우 DB를 테스트 용도로만 잠깐 쓸 용도여서 위와 같이 설정하였다.
만약 프로덕션으로 사용할 경우, 백업 설정을 해 주고 컴퓨팅+스토리지 선택지는 기본이 아닌 범용이나 메모리 최적화 선택지를 선택하는 것이 적합할 것이다.

![](/files/blog/2020-11-03/dbfirewall.png)

DB에 접속하기 위해 DB 서버 방화벽 설정을 해서 사용중인 컴퓨터가 접속 할 수 있게 허용해 줘야 한다. 이는 생성한 DB서버 화면의 `연결 보안` 으로 들어가서 IP를 허용하도록 설정할 수 있다.
여기서 화면 상단에 `현재 클라이언트 IP주소 추가(x.x.x.x)` 를 클릭하여 현재 Azure Portal 에 접속중인 본인의 PC IP 추가가 가능하다. 클릭하여 추가한 뒤 저장한다.
이제 DB 에 접속하여 간단한 DB와 테이블을 하나 생성하자. 먼저 `개요`로 들어가 연결 정보를 확인한다. 그리고 이를 이용해 DB서버에 접속한다. 
여기서는 MySQL CLI 클라이언트로 접속했지만, MySQL Workbench, QueryPie 등으로 접속해서 사용해도 좋다. 만약 접속에 사용할 관리자 암호를 분실했다면, `개요`화면의 `암호 재설정` 버튼으로 초기화 하면 된다.

![](/files/blog/2020-11-03/dbinfo.png)

```bash
# mysql -h <서버 이름> -u <서버 관리자 로그인 이름> -p
mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```
이제 아래 명령으로 데이터베이스를 하나 생성하고, `students`테이블도 하나 생성하자.
```sql
CREATE DATABASE student;
USE student;
CREATE TABLE students(
    id serial PRIMARY KEY,
    name VARCHAR(50),
    major VARCHAR(50),
    status INTEGER
);
```

프로젝트는 앞서 Swagger Codegen 으로 생성한 프로젝트를 활용해 보자. 먼저 해당 프로젝트에 패키지를 추가하고, EF Core 관련 도구도 설치하자.
아래 명령은 .Net CLI 에서 사용 가능한  EF Core 툴을 설치하고, 프로젝트에 DB설계 도구와 EF Core용 MySQL 드라이버를 설치한다. 
이 글에서는 [Pomelo Foundation 의 것](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)으로 설치 했는데, [Oracle의 MySQL 팀이 제공하는 EF Core 용 공식 드라이버](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore)도 있다. 다만 오라클 제공 드라이버는 라이센스가 GPL 이여서 파생 프로젝트에 GPL을 사용해야 할 수도 있어(보통 예외 조항이 있는 경우가 많다.), MIT 라이센스를 사용하는 Pomelo Foundation 것으로 일단 편하게 선택했다.
```bash
dotnet tool install --global dotnet-ef
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Pomelo.EntityFrameworkCore.MySql
```

그리고 아래 .Net CLI 명령으로 스캐폴드 한다. 그러면 자동으로 DB 서버에서 리버스 엔지니어링 하여 EF Core API 를 사용하는 C# 코드가 생성된다.
아래 명령어 대로 하면, Models 아래에 `DatabaseContext.cs`와 `Students.cs` 파일이 생성된다
```bash
# dotnet ef dbcontext scaffold "DB 연결 문자열" "Pomelo.EntityFrameworkCore.MySql" -c <컨텍스트 이름> --output-dir <코드 생성할 디렉토리(상대경로)>
# dotnet ef dbcontext scaffold "Server=<서버 이름>; Port=3306; Database=<DB이름>; Uid=<로그인 이름>; Pwd=<로그인 암호>; SslMode=Preferred;" "Pomelo.EntityFrameworkCore.MySql" -c DatabaseContext --output-dir Models
dotnet ef dbcontext scaffold "Server=mydemoserver.mysql.database.azure.com; Port=3306; Database=student; Uid=myadmin@mydemoserver; Pwd={your_password}; SslMode=Preferred;" "Pomelo.EntityFrameworkCore.MySql" -c DatabaseContext --output-dir Models
```
생성된 파일 중 `Students.cs` 만 열어 보자면, 아래와 같이 생성되어 있음을 확인할 수 있다.
```cs
using System;
using System.Collections.Generic;

namespace IO.Swagger.Models
{
    public partial class Student
    {
        public ulong Id { get; set; }
        public string Name { get; set; }
        public string Major { get; set; }
        public int? Status { get; set; }
    }
}

```

`DatabaseContext.cs`는 열어보면, `OnConfiguring`메소드에 앞서 명령행에서 같이 입력한 연결 문자열이 그대로 들어가 있는 것을 볼 수 있을 것이다. 당연하게도 저 상태로 배포하는 것이 아니라 설정 파일이나 환경 변수로 분리해야 한다. ASP.NET 의 경우 `appsettings.json` 에 DB 연결 문자열 등 설정값을 저장한다.

```cs
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            if (!optionsBuilder.IsConfigured)
            {
#warning To protect potentially sensitive information in your connection string, you should move it out of source code. See http://go.microsoft.com/fwlink/?LinkId=723263 for guidance on storing connection strings.
                optionsBuilder.UseMySql("server=mydemoserver.mysql.database.azure.com;database=student;user=myadmin@mydemoserver;password=password;", x => x.ServerVersion("8.0.15-mysql"));
            }
        }
```
`appsettings.json` 에 `ConnectionString` 을 추가하고 그 아래에 `DatabaseContext` 항목을 넣어 그 값으로 연결 문자열을 넣어준다.
```json
{
  "ConnectionStrings": {
    "DatabaseContext": "server=mydemoserver.mysql.database.azure.com;database=student;user=myadmin@mydemoserver;password=password;"
  },
  ...
}
```
그리고 `Startup.cs`의 `ConfigureServices()` 메소드에 DB 컨텍스트를 종속성 주입으로 설정해 준다. 이제 `DatabaseContext.cs`의 `OnConfiguring()`메소드는 더이상 필요하지 않으니, 통째로 지워준다.
```cs
...
public void ConfigureServices(IServiceCollection services)
{
    ... 
    services.AddDbContext<DatabaseContext>(options =>
         options.UseMySql(
             Configuration.GetConnectionString("DatabaseContext"),
             mySqlOptions => mySqlOptions
                     .ServerVersion(new Version(8, 0, 15), ServerType.MySql)));
    ...
}
...
```
이제 DB 설정과 컨트롤러 코드 틀이 준비 되었으니. 실제 데이터를 넣고, 조회하고, 수정하고, 삭제하는 작업을 컨트롤러단에서 구현하자. 이 글의 초반에 나온 코드를 참고하여 작성하면 된다.
생성자에서 `Startup.cs` 에서 설정한 DB Context 를 주입받도록 생성자를 작성하고, 주입받은 DB Context 로 질의를 하거나 쓰기 작업을 해서 데이터를 반환하도록 작성하면 된다.
아래 예시에서는 DB Context 주입받는 생성자와 `GET /Students/{id}` API 구현한 예제만 넣어 보았다.
```cs
namespace IO.Swagger.Controllers
{ 
    // EF Core Database context
    private readonly DatabaseContext _context;
    public StudentsController(DatabaseContext context)
    {
        _context = context;
    }
    /// <summary>
    /// 
    /// </summary>
    [ApiController]
    public class StudentsApiController : ControllerBase
    { 
        ...
        /// <summary>
        /// 
        /// </summary>
        /// <param name="id"></param>
        /// <response code="200">Success</response>
        [HttpGet]
        [Route("/Students/{id}")]
        [ValidateModelState]
        [SwaggerOperation("StudentsIdGet")]
        [SwaggerResponse(statusCode: 200, type: typeof(Student), description: "Success")]
        public virtual IActionResult StudentsIdGet([FromRoute][Required]long? id)
        {
            var student = await _context.Students.FindAsync(id);
            if (student == null)
            {
                return NotFound();
            }
            return student; 
        }
        ...
    }
    ...
}
```

# 배포
자. 이제 드디어 배포를 해 보자. 프로젝트 소스코드는 회사에서 Azure DevOps 를 사용해서, Azure DevOps 가 제공하는 Azure Repos 로 관리한다.
그리고 배포에는 Azure App Service 를 사용하기로 했는데, 둘 다 Azure 서비스 여서 마우스 클릭 몇번으로 배포 연동이 가능하다.
[Azure DevOps 에 대한 소개는 영진님의 글에 잘 나와있어서, 이걸 참고하면 좋고.](/2020/07/10/AzureDevopsOnAKS1) [Azure Repos 에 소스코드 커밋 올리는건 GitHub등 다른 플랫폼과 방법 유사하니 생략하겠다.](https://docs.microsoft.com/ko-kr/azure/devops/repos/git/create-new-repo)
여기선 바로 Azure App Service 하나 생성하고, Azure Repos 에 소스코드가 올라와 있다고 가정하고 배포 연동을 해 보자.
![](/files/blog/2020-11-03/newappservice.png)

Azure Portal 검색창에서 `App Services` 를 검색하고 들어간 다음, `추가`를 클릭하면 위와 같은 화면이 나온다. 리소스 그룹과 이름을 적절히 지정하고, 
게시는 `코드`로(`Docker 컨테이너`는 여기서 `Dockerfile`를 작성하거나 컨테이너 이미지를 따로 빌드 하지 않았으니 해당사항이 아니다.), 런타임 스택은 우리가 올릴 Swagger Codegen 생성물 기반 코드가 .Net Core 3.1 기준으로 생성 되었으니, `.Net Core 3.1 (LTS)` 항목으로 선택한다. 그리고 앱 서비스 플랜(요금제) 와 서비스 사양도 선택한다. 여기서는 테스트 용도로만 사용할 목적이여서 D1 으로 선택했지만, 실제 서비스로 사용하려면 S1 이상으로 사용해야 한다. 그래야 자동 크기 조정(Autoscale), 앱 백업, 테스트 환경 별도 배포를 위한 스테이징 슬롯 등. 프로덕션 환경 앱 배포를 위한 기능을 사용할 수 있다.

![](/files/blog/2020-11-03/s1spec.png)
> 위 사진은 S1 요금제가 제공하는 하드웨어 사양과 기능을 보여준다.

App Service 리소스를 생성했다면, 앞서 생성한 Azure Database for MySQL 과의 연결 설정을 먼저 하자. 앞에서 Azure MySQL 에 컴퓨터에서 접속하기 위해 방화벽 설정을 했듯, 이번에는 App Service 에 대해 방화벽 설정을 해야 한다. App Service 리소스의 IP 를 허용해 주면 되는데, 이 IP는 App Service 리소스의 `설정 -> 속성` 화면에 들어가면 쉼표로 구분된 `아웃바운드 IP주소`가 있다. 여기 나온 IP 를 복사해서 Azure Database for MySQL 리소스 연결보안 화면의 방화벽 규칙에 추가하면 된다.
![](/files/blog/2020-11-03/appsvcoutbound.png)
![](/files/blog/2020-11-03/dbfirewall2.png)

그리고 DB 연결 문자열을 설정하자. 리모트 저장소(여기서는 Azure Repos)에는 연결 문자열 같은 암호나 인증키 등이 포함된 데이터를 커밋하면 안 되므로, 앞서 `appsettings.json` 에 분리한 연결 문자열이 있는 `DatabaseConnection` 의 값을 지워서 비워두고. 이 값을 App Service 리소스의 구성으로 옮길 것이다. 아래 그림처럼 App Service 리소스의 `구성 -> 연결 문자열 -> 새 연결 문자열`로 들어간다.
여기에 이름은 `DatabaseConnection` 앞서 `appsettings.json` 에 있던 연결 문자열 값에 대한 Key 이다. App Service 의 경우 .Net 앱 배포시 연결 문자열로 `appsettings.json`의 `ConnectionStrings` 아래에 있는 키로 설정하면, [App Service 에서 설정한 값이 `appsettings.json` 에 정의한 값을 덮어쓰게 된다.](https://docs.microsoft.com/ko-kr/azure/app-service/configure-common#configure-connection-strings)
![](/files/blog/2020-11-03/connstring.png)

필요한 준비를 다 했으니, 이번에는 Azure Repos 에서 자동으로 배포되도록 설정하자. App Service 화면에서 `배포 센터`로 들어가면 다양한 방식의 배포 옵션을 확인할 수 있다. Azure Repos 뿐만 아니라, 많이 사용하는 GitHub와 Bitbucket 저장소와 연동해서 배포도 가능하며. 로컬 Git 저장소에서 푸시하여 배포하는 방법도 지원한다. 여기서 Azure Repos를 선택하자.

![](/files/blog/2020-11-03/deploycenter.png)

빌드 공급자는 Azure Pipelines 를 선택하자. Azure Pipelines 는 Azure DevOps 에 통합되어 있어서, 각 Azure DevOps 프로젝트에서 파이프라인 작업 현황을 확인할 수 있다. 나중에 필요하면 파이프라인을 수정해서 단위 테스트를 자동화 하거나, 정적 분석 등의 과정을 추가할 수 있다.

![](/files/blog/2020-11-03/buildsrc.png)

커밋이 올라오면 자동을 배포할 저장소와 저장소의 브랜치(분기) 를 설정해 준다. 그리고 설정을 마무리 하면, 자동으로 배포 파이프라인 구축되어 작동하기 시작한다.

![](/files/blog/2020-11-03/buildcfg.png)

Azure DevOps 에서 배포 설정한 프로젝트의 Pipelines 로 이동하면, 방금 앞에서 배포 설정으로 구성된 파이프라인이 작동하고 있는것을 확인할 수 있다.

![](/files/blog/2020-11-03/azpipeline.png)

![](/files/blog/2020-11-03/azpipejobs.png)

파이프라인 항목으로 들어가서, 수정 화면으로 들어가 보자. 요즘 많은 사람들이 사용하는 GitHub Actions 이 YAML 파일 편집 화면을 보여주는 것과 다르게 GUI 편집 화면이 나오는 데, 이는 클래식 인터페이스로 파이프라인을 구성하는 방식이다. [Azure Pipelines 의 경우 사진처럼 블럭을 끌어나 배치해서 파이프라인을 구성하는 클래식 인터페이스를 사용하는 방법이 있고, GitHub Actions나 기존에 많이 사용하던 Travis CI 처럼 YAML파일로 작성하여 구성하는 두가지 방법이 있다.](https://docs.microsoft.com/ko-kr/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
![](/files/blog/2020-11-03/editpipeline.png)

이제 배포도 다 완료 하였으니, 실제 작동하는 것을 확인하자. 앞에서 Swashbuckle을 연동한 것을 기억하는가? API 문서 자동 생성 연동과 그 문서를 볼 URL 까지 설정했기 때문에 바로 볼 수 있다. 
`내_웹사이트_주소/swagger` 로 들어가서 확인해 보자. App Service 로 배포한 앱의 웹 주소는 App Service 리소스 개요 화면에서 확인 가능하다. 예를 들어 `testapi.azurewebsites.net` 면 `testapi.azurewebsites.net/swagger`로 들어가자. Swagger UI 로 생성된 API 문서를 확인할 수 있다.
![](/files/blog/2020-11-03/swagger.png)

각 API 항목의 `Try it out` 버튼으로 바로 API 테스트 해보는 것도 가능하다.

![](/files/blog/2020-11-03/swaggeritem.png)

# 인증기능 구현하기
이제 여기까지 했으면 다 했다 싶지만... 그게 아니였다. 고객사에서 전달받은 수정된 서비스 사용 시나리오 문서를 보면서 이상한 점이 몇가지 더 있었는데, 
그 중 하나가 기본적인 인증 기능(가입, 로그인, 로그아웃, 계정 복구)에 대한 설명이 없는 것이였다. 그래서 중간에 컨퍼런스 콜을 통해 이 점을 문의했다. 
회원 데이터가 이미 DB에 저장되어 있다고 가정하고 인증 기능을 넣지 말아야 하는지, 아니면 인증 기능까지 추가로 구현해야 하는지. 
그 결과는... 고객서에서 인증 기능까지 구현해 달라고 한다. (역시 쉽게 끝날리가 없지...)

인증 기능도 직접 다 구현하기 보단, 빠르게 기능을 넣기 위해 이미 존재하는 모듈을 활용하려 했는데, 그러기 위해 [ASP.NET Identity](https://docs.microsoft.com/ko-kr/aspnet/core/security/authentication/identity?view=aspnetcore-5.0&tabs=visual-studio)를 검토했다. ASP.NET Identity 는 기본적인 쿠키 기반 인증 뿐만 아니라, IdentityServer4 기반 OpenID 인증, Azure AD 기반 인증, Azure AD B2C 기반의 소셜 미디어 계정(Facebook, Twitter 등) 인증, App Service 간편 인증 등. 정말 다양한 옵션을 제공한다. 여기서는 사용자 정보를 EF Core 를 통해 저장하는 방식의 기본적인 쿠키 기반 인증만 간단히 다뤄보겠다.

앞에서 작업하던 프로젝트에 몇가지 패키지를 추가하자.
```bash 
dotnet add package Microsoft.AspNetCore.Identity
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
```

`Statup.cs` 에서 종속성 주입으로 몇가지 설정을 해야 한다. 먼저 `ConfigureServices()` 에 [`AddDefaultIdentity()` 메소드를 호출하여 쿠키 기반 인증 서비스를 설정한다.](https://docs.microsoft.com/ko-kr/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-5.0#definition) 
```cs
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddDefaultIdentity<IdentityUser>()
        .AddEntityFrameworkStores<DatabaseContext>(); // 앞서 만들어 둔 DbContext 연동
    ...
}
```

추가로 각종 인증 정책(암호 정책, 계정 잠금 정책, 사용자 정책 등)을 아래와 같이 설정할 수 있다.  [이 문서](https://docs.microsoft.com/ko-kr/dotnet/api/microsoft.aspnetcore.identity.identityoptions?view=aspnetcore-5.0) 를 참고해서 원하는 인증 정책을 찾아 설정할 수 있다.
```cs
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddDefaultIdentity<IdentityUser>()
        .AddEntityFrameworkStores<DatabaseContext>(); // 앞서 만들어 둔 DbContext 연동
    ...
    services.Configure<IdentityOptions>(options =>
    {
        // Password settings.
        options.Password.RequireDigit = true;
        options.Password.RequireLowercase = true;
        options.Password.RequireNonAlphanumeric = true;
        options.Password.RequireUppercase = true;
        options.Password.RequiredLength = 6;
        options.Password.RequiredUniqueChars = 1;

        // Lockout settings.
        options.Lockout.DefaultLockoutTimeSpan = TimeSpan.FromMinutes(5);
        options.Lockout.MaxFailedAccessAttempts = 5;
        options.Lockout.AllowedForNewUsers = true;

        // User settings.
        options.User.AllowedUserNameCharacters =
        "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-._@+";
        options.User.RequireUniqueEmail = false;
    });
    ...
}
```
쿠키 기반 인증이니, 쿠키 보안 정책도 설정해 주자. 쿠키 인증 정책 옵션은 [이 문서](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions?view=aspnetcore-1.1) 를 참고하면 된다.
```cs
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.ConfigureApplicationCookie(options =>
    {
        // Cookie settings
        options.Cookie.HttpOnly = true; // 클라이언트 측 JS 에서 쿠키 접근 불허 여부
        options.ExpireTimeSpan = TimeSpan.FromMinutes(5); // 쿠키 유효 시간 설정

        // options.LoginPath = "/Identity/Account/Login"; // 401 오류시 리다이렉트할 경로
        // options.AccessDeniedPath = "/Identity/Account/AccessDenied"; // 403 오류시 리다이렉트할 경로
        options.SlidingExpiration = true; // 쿠키 자동 갱신 설정
    });
    ...
}
```

이번에는 `Configure()` 메소드에 `UseAuthentication()`과 `UseAuthorization()`를 호출해서 인증(Authentication) 및 허가(Authorization) 미들웨어를 설정해 준다.
```cs
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
  ...
  app.UseAuthentication();
  app.UseAuthorization();
  ...
}
```

기존 `DatabaseContext.cs` 를 수정하여 ASP.NET Identity 와 연동하자. 기존 `DbContext`를상속 하던 클래스를 `IdentityDbContext`를 상속하도록 수정한다.
```cs
// 기존
namespace IO.Swagger.Models
{
    public partial class DatabaseContext : DbContext
    {
      ...
    }
}
// 수정
namespace IO.Swagger.Models
{
    public partial class DatabaseContext : IdentityDbContext
    {
      ...
    }
}
```

기본적인 설정은 되었고, 이제 회원가입, 로그인, 로그아웃, 계정 복구 등 각종 인증 관련 API 를 구현해 보자. 인증을 위한 컨트롤러를 구현해서 작업할 것이다.
먼저 아래 코드 처럼, 로그인 관리에 필요한 `SignInManager`, 사용자 관리에 필요한 `UserManager` 를 종속성 주입으로 주입받는다.
```cs
namespace IO.Swagger.Controllers
{

    [ApiController]
    [Route("Auth")]
    public class AuthController : ControllerBase
    {
        private readonly UserManager<IdentityUser> _userManager;
        private readonly SignInManager<IdentityUser> _signInManager;
        private readonly ILogger<AuthController> _logger;

        public AuthController(SignInManager<IdentityUser> signInManager,
           ILogger<AuthController> logger,
            UserManager<IdentityUser> userManager)
        {
            _userManager = userManager;
            _signInManager = signInManager;
            _logger = logger;
        }
    }
}
```

이제 API 함수 하나 만들고, 필요한 데이터를 URL 파라메터나 요청 Body 로 받아서 `SignInManager`의 메소드나 `UserManager`의 메소드를 호출해 주면 된다. 
회원 가입 API 를 예제로 코드를 작성해 보자. 우선 회원가입 데이터 양식을 DTO 코드로 만들어 정의하자. [`DataAnnotation` 특성을 이용하여 유효성 검사를 할 수 있다.](https://docs.microsoft.com/ko-kr/dotnet/api/system.componentmodel.dataannotations?view=net-5.0)
```cs
namespace IO.Swagger.Models.Dto
{
    public class SignUpDto
    {
        [Required]
        public string Username { get; set; }
        [Required]
        [StringLength(100, ErrorMessage = "The {0} must be at least {2} and at max {1} characters long.", MinimumLength = 6)]
        public string Password { get; set; }
        [Required]
        [StringLength(100, ErrorMessage = "The {0} must be at least {2} and at max {1} characters long.", MinimumLength = 6)]
        public string PasswordConfirm { get; set; }
        [Required]
        [EmailAddress]
        public string Email { get; set; }
    }
}
```
그리고 이러한 형태의 데이터를 받아 처리하는 API 를 구현하자. 회원가입은 [`UserManager`의 `CreateAsync()`를 호출하면 된다.](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync?view=aspnetcore-5.0#definition) 그리고 해당 메소드가 반환한 객체의 `Succeeded` 값이 `true` 인지 확인하여 성공 여부를 확인한다.
```cs
...
public class AuthController : ControllerBase
{
  ...
  [HttpPost("signup")]
  public async Task<IActionResult> SignUp(SignUpDto signUpForm)
  {
      var user = new IdentityUser { UserName = signUpForm.Username, Email = signUpForm.Email };
      var result = await _userManager.CreateAsync(user, signUpForm.Password);
      if (result.Succeeded)
      {
          return Ok();
      }
      return BadRequest();
  }
  ...
}
...
```
# 이메일 발송

회원 가입이 성공이면, 계정 이메일을 인증하는 메일을 발송해야 한다. ASP.NET Identity 가 메일 발송에 필요한 인증 토큰 정도는 생성해 주지만, 메일까지 전송해 주지는 않는다.
그래서 메일 전송 모듈도 하나 따로 붙여줘야 한다. 그리고 메일 발송서비스도 하나 필요하다. 여기서는 SendGrid와 FluentEmail로 이를 구성해 보겠다.
SendGrid는 Azure 제공 서비스는 아니지만, [Azure Marketplace 에서 SendGrid 계정을 생성하여 Azure 리소스로 관리가 가능하다.](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)
SendGrid 계정을 생성하려면, 다른 리소스와 마찬가지로 Azure Portal 통합 검색창에 *SendGrid* 검색하면 바로 나온다. 들어가서 계정 하나 생성하자. 사진처럼 가입 폼이 나오는데, 적절히 입력해 주면 된다.
무료 요금제는 월 25000건이라는 많은 사용량을 제공하긴 하지만, 메일 발송 전용 IP를 제공하지는 않는다. 무료 요금제의 공유IP 로 메일을 발송한다.

중요한 내용의 메일을 꼭 수신자가 수신해야 하는 경우, 유료 요금제 중 전용 IP를 할당해 주는 요금제를 사용하는 것이 좋다.
공유 IP 는 이름 그대로 나만 전송에 사용하는 것이 아니라, 다른 사용자도 메일 발송에 사용하기 때문이다. 물론 SendGrid 의 경우 스팸 전송을 사전에 차단 하겠지만,
만약 다른 사용자가 해당 공유 IP 로 스팸메일을 대량 발송해서 IP주소에 대한 평판(Reputation)이 떨어졌다면, 이메일 서비스 제공자의 메일 서버가 해당 IP 주소를 차단할 수도 있기 때문이다.
메일 서버에서 IP를 차단 하면, 수신자의 스팸함에조차 들어가지 못하기 때문에, 수신자는 발신자가 메일 발송했다는 사실 조차 모른다.
그래서 정말 중요한 메일을 전송 하는 경우, 특히 마케팅 메일 등 다른 목적의 메일도 전송하는 경우, 전용 IP를 제공하는 요금제를 선택하여 용도에 따라 전용 IP 를 두고 사용하는 것이 좋다.
보통 메일 발송 에이전트를 직접 구축해 관리하면 메일 발송 수를 서서히 늘려가던가 하는 방식으로 IP주소 평판을 직접 관리해야 하지만, SendGrid 의 경우 이를 대신 관리해 주기 때문에 편리하다.

![](/files/blog/2020-11-03/newsendgrid.png)

계정을 만들었다면, 해당 SendGrid Account 리소스 화면의 `Manage` 버튼을 누르면 로그인 된 SendGrid 관리 화면이 나온다. 
먼저 Sender Identity 인증 및 등록을 통해 메일 발송에 사용할 도메인과 주소를 등록해야 한다. Domain Authentication 으로 도메인 인증을 하고, Single Sender Verification 으로 전송에 사용할 이메일 주소를 만들고 발송자 정보를 인증한다. Sender Authentication 화면은 Settings -> Sender Authentication 에 있다.

![](/files/blog/2020-11-03/senderauth.png)

먼저 `Authehnticate Your Domain` 을 클릭하여, 도메인 등록과 인증을 먼저 진행하자. 아래 사진과 같은 화면이 나오는데, 본인이 사용하는 DNS 제공자를 선택하자. 본인이 사용하는 DNS 제공자가 없다면 `Other Host`를 선택 후 진행한다.
![](/files/blog/2020-11-03/choosedns.png)

사용할 최상위 도메인(TLD)를 입력한다.

![](/files/blog/2020-11-03/enterdomain.png)

다음 화면에서는 DNS 에 입력해야 할 레코드 정보가 나온다. 나와있는 데로 DNS 레코드를 등록하면 된다. 아래 사진의 경우 CNAME 레코드 3개를 등록하면 된다.
![](/files/blog/2020-11-03/regkeys.png)
> SendGrid 에서 보여주는 등록 DNS 레코드 정보
![](/files/blog/2020-11-03/dnssetup.png)
> Netlify DNS 에 레코드 등록한 모습