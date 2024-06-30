## Intro

에러코드를 도입하기 전에 리팩토링을 먼저 끝내고, 커스텀을 진행했다.

커스텀 하기 앞서, `Restdocs`의 코드 분석이나, 사전에 알아야할 내용은 [Restdocs 코드 뜯어보기](https://github.com/gomudayya/DevelopNote/blob/main/Spring/Restdocs/Restdocs%20%EC%BD%94%EB%93%9C%20%EB%9C%AF%EC%96%B4%EB%B3%B4%EA%B8%B0.md) 에서 정리하였다.

그래서 이번 글은 구체적으로 어떻게 커스텀을 했는지에 대한 내용이다.

## Snippet 파일 정의하기

`test/resources/org/springframework/restdocs/templates/` 하위 경로에 all-error-codes.snippet.snippet 파일을 만들었다.

이 파일은, 스니펫의 양식을 정의하는 파일인데 머스타치 문법으로 되어있다.

사실 조금 후회하는것은 머스타치보다는 타임리프를 이용해서 커스텀하면 어땠을까 하는 점이다.

(Restdocs에서 템플릿엔진도 바꿀 수 있는것 같음)

왜냐하면 아래 보다시피 머스타치 문법이 워낙 가독성이 떨어지는거 같아서.....

```
{{#allErrorCodes}}

=== {{errorType}}

|====
|에러코드|HTTP 상태코드|설명

{{#errorCodes}}
|{{#tableCellContent}}{{code}}{{/tableCellContent}}
|{{#tableCellContent}}{{httpStatus}}{{/tableCellContent}}
|{{#tableCellContent}}{{description}}{{/tableCellContent}}
{{/errorCodes}}

|====

{{/allErrorCodes}}
```

- `{{변수이름}}` : 머스타치에서는 템플릿엔진의 변수를 이런식으로 처리한다.

- `{{#allErrorCodes}} ... {{/#allErrorCodes}}` 전체에 대한 반복 블록이다.

  - 머스타치는 `{{#key}}` `{{/key}}` 구문을 이용해서 반복문을 정의한다.
 
- `==`, `===` 이것들은 머스타치에서 제목형식을 나타내는 것이다 `=`의 갯수가 작을수록 대제목이다.

  - HTML로 치면 `=` `==` `===` 를 H1 , H2 , H3 로 생각하면 된다.
    
- `(|==== ... |====)` 표에대한 Block을 나타낸다.

<br>

결국 이게 어떤Snippet인지 결과본을 올려보면

![image](https://github.com/gomudayya/DevelopNote/assets/129571789/c6c2d956-3210-44a4-a672-e5d17bd649a8)

요런 형태이다!

에러타입별로 에러코드를 분류하는것이 더 깔끔한 API문서를 만들 수 있을거라 판단해서, 이런식으로 스니펫을 구성해보았다.

![image](https://github.com/gomudayya/DevelopNote/assets/129571789/a6cca542-bd23-42b5-b6ba-66f71e714998)

## 문서화를 위한 컨트롤러/테스트코드 작성하기

Restdocs는 컨트롤러 계층의 테스트코드가 있어야만 문서화를 할 수 있다.

따라서 껍데기만 있는 컨트롤러와, 껍데기만 있는 테스트코드를 테스트 패키지에 작성하였다. 

내가 하려고했던 것은 어떤 API의 Request와 Response를 토대로 문서화하는 것이 아니라

에러코드를 문서화하는 것이기 때문에, 그냥 껍데기만 있는 형태로 만드는 것이 더 괜찮다고 판단했다.

- 컨트롤러 껍데기

![image](https://github.com/gomudayya/DevelopNote/assets/129571789/3885c5df-2652-4b0e-a871-fc77ff91267c)

- 테스트코드 껍데기

  - `AllErrorCodesSnippet.buildSnippet()` 에 대한것은 아래에서 설명.
![image](https://github.com/gomudayya/DevelopNote/assets/129571789/de1b7205-6630-48e5-9ad8-4fcf9c7e9952)



