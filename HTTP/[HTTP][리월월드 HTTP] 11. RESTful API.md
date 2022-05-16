* REST API와 PRC의 차이 
> 설계 철학의 차이 
  * REST API(Representational State Transfer)
  * - 자원(resource)의 표현(representation) 에 의한 상태 전달을 의미한다.
  * - HTTP URI(Uniform Resource Identifier)를 통해 자원(Resource)을 명시 
  *   -> HTTP Method(POST, GET, PUT, DELETE)를 통해 해당 자원에 대한 CRUD Operation을 적용

  * PRC 
  * - URL은 하나로, 요청할 때 서비스 이름과 메서드 이름을 전달해 '무엇을 할 것인가' 지정

* HATEOAS
> Hypermedia As The Engine Of Application REST의 일부인, 인터페이스의 일반화를 사양으로 만든 것
> 리소스간의 정보와 조작링크를 응답에 포함 시켜 자기 서술력을 향상 

* REST-ish (REST 스타일)
> REST를 지향하지만, 완전히 RESTful하게 됐는지 자신이 없을때는 REST-ish API라고 부를수도..

* 스테이터스 코드(STATUS CODE)
> 상세한 오류내용이나 서버의 상황을 알려주는 상태코드

- 1xx : 성공이나 실패가 결정되기 이전 상태
e.g. 100 Countinue : 송신 전 헤더만 보내서 허가를 구하러온 클라이언트 서버가 접수되었다.
e.g. 101 Switching protocols : HTTP 이외의 프로토콜로 변경을 서버가 접수 했을때 반환

- 2xx : 정상종료
e.g. 200 OK
e.g. 201 Created : 작성 성공. POST 메서드에 대한 반환
e.g. 204 No Content : DELETE 메서드에 대한 반환
e.g. 206 Partial Content 다운로드 중단 및 재개에 사용됨 

- 3xx : 리디렉트 

- 4xx : 클라이언트 오류
e.g. 400 Bad Request : 클라이언트 요청에 문자가 있을때 반환. url 파싱오류 등 
e.g. 401 Unauthorized : 인증이 필요하여 반환
e.g. 403 Forbidden : 금지된 리소스에 액세스 했을때 반환
e.g. 404 Not Found : 리소스를 찾을 수 없을때 반환
e.g. 408 Request Timeout : 요청이 기대한 대로 오지 않을때 (시간초과)
e.g. 414 URI Too Long : URL 길이가 서버 허용치를 넘을때 반환. RFC 7231에서 추가 
e.g. 429 Too Many Request : 대량의 요청을 보냈을 때 오류로서 반환된다. 
                            구글 초당 10회 제한 API를 제한이 넘는 빈도로 호출시 가능 

- 5xx : 서버 오류
e.g. 500 Internal Server Error : 서버 내부 오류
e.g. 502 Bad Gateway : 서비스에 이르는 동안 게이트웨이나 프록시 서버가 요청을 거부한 경우, AWS의 로드 밸런서가 대상 서버와 통신에 실패한 경우 발생
e.g. 503 Service Unavailable : 서버가 부팅안되거나 프록시 서버와 로드벨런서가 반환
e.g. 504 Gateway Timeout : 서비스에 도달하는 동안, 게이트웨이나 프록시 서버가 최종 목적지 서버에 도달할 수 없어 타임아웃됬을때 반환

* REST API 액세스시 주의할점 
- 타임아웃 - 클라이언트에서 세션 종료, 마감, 취소 로직이 구현되어 있어야 한다. 
- 액세스 수 제한 - Rate Limit Exceeded 잦은 호출을 막기위해 설정되어 있다.