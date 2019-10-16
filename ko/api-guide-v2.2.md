## Notification > SMS > API v2.2 Guide

## v2.2 API 소개

### v2.1과 달라진 사항
1. 템플릿으로 발송 요청 시 요청 파라미터의 우선순위가 높아지도록 수정되었습니다.
   - 예시) 템플릿으로 발송 요청 시 요청 파라미터에 제목, 본문, 발신번호, 첨부파일이 포함된 경우 템플릿에 저장된 데이터를 사용되지 않도록 수정
2. 문자 발송 시 제목/본문에 대한 유효성 검사가 강화되었습니다.
    * 아래와 같이 문자 발송 시 제목/본문 길이 제한이 강화됩니다.
    * SMS(본문: 최대 255자), LMS/MMS(제목: 최대 120자, 본문: 최대 4000자)



### [API 도메인]

|환경|	도메인|
|---|---|
|Real|	https://api-sms.cloud.toast.com|

<span id="precautions"></span>
### [주의 사항]
* 지원하는 문자 길이는 아래와 같습니다.
* 최대 지원 글자 수는 저장 기준이며 문자 잘림을 방지하기 위해서 표준 규격으로 작성해주세요.
* 인코딩은 EUC-KR 기준으로 발송되며 지원하지 않는 이모티콘은 발송에 실패합니다.

| 분류  | 최대 지원 | 표준 규격 |
| --- | --- | --- |
| SMS 본문 | 255자 | 90바이트(한글 45자, 영문 90자) |
| MMS 제목 | 120자 | 40바이트(한글 20자, 영문 40자) |
| MMS 본문 | 4,000자 | 2,000바이트(한글 1,000자, 영문 2,000자) |

## 단문 SMS

### 단문 SMS 발송

#### 요청

[URL]

```
POST  /sms/v2.2/appKeys/{appKey}/sender/sms
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Request body]

```
{
   "templateId":"TemplateId",
   "body":"본문",
   "sendNo":"15446859",
   "requestDate":"2018-08-10 10:00",
   "senderGroupingKey":"SenderGroupingKey",
   "recipientList":[
      {
         "recipientNo":"01000000000",
         "countryCode":"82",
         "internationalRecipientNo":"821000000000",
         "templateParameter":{
            "key":"value"
         },
         "recipientGroupingKey":"recipientGroupingKey"
      }
   ],
   "userId":"UserId"
}
```

|값|	타입| 최대 길이 |	필수|	설명|
|---|---|---|---|---|
|templateId|	String | 50 |	X|	발송 템플릿 ID|
|body|	String| 표준: 90바이트, 최대: 255자 (EUC-KR 기준) [[주의사항](./api-guide/#precautions)] |	O|	본문 내용|
|sendNo|	String| 13 |	O|	발신 번호|
|requestDate| String| - | X | 예약 일시(yyyy-MM-dd HH:mm)|
|senderGroupingKey| String| 100 | X | 발신자 그룹키 |
|recipientList[].recipientNo| String| 20 |	O|	수신 번호<br/>countryCode와 조합하여 사용 가능<br/>최대 1,000명|
|recipientList[].countryCode|	String| 8 |	X|	국가 번호 [기본값: 82(한국)] |
|recipientList[].internationalRecipientNo| String|  20 | X| 국가 번호가 포함된 수신 번호<br/>예)821012345678<br/>recipientNo가 있을 경우 이 값은 무시된다.<br/>|
|recipientList[].templateParameter|	Object|  - |	X|	템플릿 파라미터(템플릿 ID 입력 시)|
|recipientList[].templateParameter.{key}| String| - |	X|	치환 키(##key##)|
|recipientList[].templateParameter.{value}|	Object| - |	X|	치환 키에 매핑되는 Value값|
|recipientList[].recipientGroupingKey| String| 100 | X | 수신자 그룹키 |
|userId|	String|	100 | X | 발송 구분자 ex)admin,system |

#### 응답

```
{
   "header":{
      "isSuccessful":true,
      "resultCode":0,
      "resultMessage":"SUCCESS"
   },
   "body":{
      "data":{
         "requestId":"20180810100630ReZQ6KZzAH0",
         "statusCode":"2",
         "senderGroupingKey":"SenderGroupingKey",
         "sendResultList":[
            {
               "recipientNo":"01000000000",
               "resultCode":0,
               "resultMessage":"SUCCESS",
               "recipientSeq":1,
               "recipientGroupingKey":"RecipientGroupingKey"
            }
         ]
      }
   }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.data.requestId|	String|	요청 ID|
|body.data.statusCode|	String|	요청 상태 코드(1:요청 중, 2:요청 완료, 3:요청 실패)|
|body.data.senderGroupingKey|	String|	발신자 그룹키|
|body.data.sendResultList[].recipientNo| String | 수신 번호|
|body.data.sendResultList[].resultCode| Integer | 결과 코드|
|body.data.sendResultList[].resultMessage| String | 결과 메시지|
|body.data.sendResultList[].recipientSeq| Integer | 수신자 시퀀스(mtPr)|
|body.data.sendResultList[].recipientGroupingKey| String | 수신자 그룹키|

#### 단문 SMS 발송 예제(일반 국내 수신 번호)

| Http metho | URL |
| - | - |
| POST | https://api-sms.cloud.toast.com/sms/v2.2/appKeys/{appKey}/sender/sms|

[Request body]
```
{
   "body":"본문",
   "sendNo":"15446859",
   "senderGroupingKey":"SenderGroupingKey",
   "recipientList":[
      {
         "recipientNo":"01000000000",
         "recipientGroupingKey":"RecipientGroupingKey"
      },
      {
         "recipientNo":"01000000001",
         "recipientGroupingKey":"RecipientGroupingKey2"
      }
   ]
}
```

[Response]
```
{
   "header":{
      "isSuccessful":true,
      "resultCode":0,
      "resultMessage":"SUCCESS"
   },
   "body":{
      "data":{
         "requestId":"20180810100630ReZQ6KZzAH0",
         "statusCode":"2",
         "senderGroupingKey":"SenderGroupingKey",
         "sendResultList":[
            {
               "recipientNo":"01000000000",
               "resultCode":0,
               "resultMessage":"SUCCESS",
               "recipientSeq":1,
               "recipientGroupingKey":"RecipientGroupingKey"
            },
            {
               "recipientNo":"01000000001",
               "resultCode":0,
               "resultMessage":"SUCCESS",
               "recipientSeq":2,
               "recipientGroupingKey":"RecipientGroupingKey2"
            }
         ]
      }
   }
}
```

[curl]
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" https://api-sms.cloud.toast.com/sms/v2.2/appKeys/{appKey}/sender/sms -d '{"body": "{본문 내용}","sendNo": "15446859","senderGroupingKey":"SenderGroupingKey","recipientList":[{"recipientNo": "01000000000","recipientGroupingKey":"RecipientGroupingKey"},{"recipientNo": "01000000002","recipientGroupingKey":"RecipientGroupingKey2"}]}'
```

#### 단문 SMS 발송 예제(국가 코드가 포함된 수신 번호)

| Http metho | URL |
| - | - |
| POST | https://api-sms.cloud.toast.com/sms/v2.2/appKeys/{appKey}/sender/sms|


[Request body]
```
{
   "body":"본문",
   "sendNo":"15446859",
   "senderGroupingKey":"SenderGroupingKey",
   "recipientList":[
      {
         "internationalRecipientNo":"821000000000",
         "recipientGroupingKey":"RecipientGroupingKey"
      }
   ],
   "userId":""
}
```

[Response]
```
{
   "header":{
      "isSuccessful":true,
      "resultCode":0,
      "resultMessage":"SUCCESS"
   },
   "body":{
      "data":{
         "requestId":"20180810100630ReZQ6KZzAH0",
         "statusCode":"2",
         "senderGroupingKey":"SenderGroupingKey",
         "sendResultList":[
            {
               "recipientNo":"01000000000",
               "resultCode":0,
               "resultMessage":"SUCCESS",
               "recipientSeq":1,
               "recipientGroupingKey":"RecipientGroupingKey"
            }
         ]
      }
   }
}
```

[curl]
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" https://api-sms.cloud.toast.com/sms/v2.2/appKeys/{appKey}/sender/sms -d '{"body": "본문","sendNo": "15446859","recipientList": [{"internationalRecipientNo": "821000000000"}]}'
```

### 단문 SMS 발송리스트 조회

#### 요청

[URL]

```
GET  /sms/v2.2/appKeys/{appKey}/sender/sms
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Query parameter]
* requestId 또는 startRequestDate + endRequestDate 또는 startCreateDate + endCreateDate는 필수입니다.
* 등록 날짜/발송 날짜를 동시에 조회하는 경우, 발송 날짜는 무시됩니다.

|값|	타입|	최대 길이 | 필수|	설명|
|---|---|---|---|---|
|requestId|	String| 25 |	필수 |	요청 ID|
|startRequestDate|	String| - |	필수 |	발송 날짜 시작값(yyyy-MM-dd HH:mm:ss)|
|endRequestDate|	String| - |	필수 |	발송 날짜 종료값(yyyy-MM-dd HH:mm:ss)|
|startCreateDate|	String| - |	필수 |	등록 날짜 시작값(yyyy-MM-dd HH:mm:ss)|
|endCreateDate|	String| - |	필수 |	등록 날짜 종료값(yyyy-MM-dd HH:mm:ss)|
|startResultDate|	String| - |	옵션|	수신 날짜 시작값(yyyy-MM-dd HH:mm:ss)|
|endResultDate|	String| - |	옵션|	수신 날짜 종료값(yyyy-MM-dd HH:mm:ss)|
|sendNo|	String| 13 |	옵션|	발신 번호|
|recipientNo|	String| 20 |	옵션|	수신 번호|
|templateId|	String| 50 |	옵션|	템플릿 번호|
|msgStatus|	String| 1 |	옵션| 메시지 상태 코드(0: 실패, 1: 요청, 2: 처리 중, 3:성공, 4:예약취소, 5:중복실패) |
|resultCode|	String| 10 |	옵션|	수신 결과 코드 [[조회 코드표](./error-code/#_2)]|
|subResultCode|	String| 10 |	옵션|	수신 결과 상세 코드 [[조회 코드표](./error-code/#_3)]|
|senderGroupingKey|	String| 100 |	옵션|	발송자 그룹키|
|recipientGroupingKey|	String| 100 |	옵션|	수신자 그룹키|
|pageNum|	Integer| - |	옵션|	페이지 번호(기본값 : 1)|
|pageSize|	Integer| 1000 |	옵션|	조회 수(기본값 : 15)|

#### 응답

```
{
   "header":{
      "resultCode":0,
      "resultMessage":"SUCCESS",
      "isSuccessful":true
   },
   "body":{
      "pageNum":1,
      "pageSize":15,
      "totalCount":1,
      "data":[
         {
            "requestId":"20180810100630ReZQ6KZzAH0",
            "requestDate":"2018-08-10 10:06:30.0",
            "resultDate":"2018-08-10 10:06:42.0",
            "templateId":"TemplateId",
            "templateName":"템플릿명",
            "categoryId":0,
            "categoryName":"카테고리명",
            "body":"단문 테스트",
            "sendNo":"15446859",
            "countryCode":"82",
            "recipientNo":"01000000000",
            "msgStatus":"3",
            "msgStatusName":"성공",
            "resultCode":"1000",
            "resultCodeName":"성공",
            "telecomCode":10001,
            "telecomCodeName":"SKT",
            "mtPr":"1",
            "sendType":"0",
            "userId":"tester",
            "adYn":"N",
            "resultMessage": "",
            "senderGroupingKey":"SenderGroupingKey",
            "recipientGroupingKey":"RecipientGroupingKey"
         }
      ]
   }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.pageNum|	Integer|	현재 페이지 번호|
|body.pageSize|	Integer|	조회된 데이터 수|
|body.totalCount|	Integer|	총 데이터 수|
|body.data[].requestId|	String|	요청 ID|
|body.data[].requestDate|	String|	발신 일시|
|body.data[].resultDate|	String|	수신 일시|
|body.data[].templateId|	String|	템플릿 ID|
|body.data[].templateName|	String|	템플릿명|
|body.data[].categoryId|	String|	카테고리 ID|
|body.data[].categoryName|	String|	카테고리명|
|body.data[].body|	String|	본문 내용|
|body.data[].sendNo|	String|	발신 번호|
|body.data[].countryCode|	String|	국가 번호|
|body.data[].recipientNo|	String|	수신 번호|
|body.data[].msgStatus|	String|	메시지 상태 코드|
|body.data[].msgStatusName|	String|	메시지 상태 코드명|
|body.data[].resultCode|	String|	수신 결과 코드 [[수신 결과 코드표](./error-code/#emma-v3)]|
|body.data[].resultCodeName|	String|	수신 결과 코드명|
|body.data[].telecomCode|	Integer|	통신사 코드|
|body.data[].telecomCodeName|	String|	통신사명|
|body.data[].mtPr|	Integer|	발송 상세 ID(상세 조회 시 필수)|
|body.data[].sendType|	String|	발송 유형(0:Sms, 1:Lms/Mms, 2:Auth)|
|body.data[].userId|	String|	발송 요청 ID|
|body.data[].adYn|	String|	광고 여부|
|body.data[].senderGroupingKey|	String|	발신자 그룹키|
|body.data[].recipientGroupingKey|	String|	수신자 그룹키|

### 단문 SMS 발송 단일 조회

#### 요청

[URL]

```
GET  /sms/v2.2/appKeys/{appKey}/sender/sms/{requestId}
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|
|requestId|	String|	요청 ID|

[Query parameter]

|값|	타입|	필수|	설명|
|---|---|---|---|
|mtPr|	Integer|	필수|	발송 상세 ID|

#### 응답

```
{
   "header":{
      "resultCode":0,
      "resultMessage":"SUCCESS",
      "isSuccessful":true
   },
   "body":{
      "data":{
         "requestId":"20180810100630ReZQ6KZzAH0",
         "requestDate":"2018-08-10 10:06:30.0",
         "resultDate":"2018-08-10 10:06:42.0",
         "templateId":"TemplateId",
         "templateName":"템플릿명",
         "categoryId":0,
         "categoryName":"카테고리명",
         "body":"본문",
         "sendNo":"15446859",
         "countryCode":"82",
         "recipientNo":"01000000000",
         "msgStatus":"3",
         "msgStatusName":"성공",
         "resultCode":"1000",
         "resultCodeName":"성공",
         "telecomCode":10001,
         "telecomCodeName":"SKT",
         "mtPr":"1",
         "sendType":"0",
         "userId":"tester",
         "adYn":"N",
         "resultMessage": "",
         "senderGroupingKey":"SenderGroupingKey",
         "recipientGroupingKey":"RecipientGroupingKey"
      }
   }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.data.requestId|	String|	요청 ID|
|body.data.requestDate|	String|	발신 일시|
|body.data.resultDate|	String|	수신 일시|
|body.data.templateId|	String|	템플릿 ID|
|body.data.templateName|	String|	템플릿명|
|body.data.categoryId|	String|	카테고리 ID|
|body.data.categoryName|	String|	카테고리명|
|body.data.body|	String|	본문 내용|
|body.data.sendNo|	String|	발신 번호|
|body.data.countryCode|	String|	국가 번호|
|body.data.recipientNo|	String|	수신 번호|
|body.data.msgStatus|	String|	메시지 상태 코드|
|body.data.msgStatusName|	String|	메시지 상태 코드명|
|body.data.resultCode|	String|	수신 결과 코드 [[수신 결과 코드표](./error-code/#emma-v3)]|
|body.data.resultCodeName|	String|	수신 결과 코드명|
|body.data.telecomCode|	Integer|	통신사 코드|
|body.data.telecomCodeName|	String|	통신사명|
|body.data.mtPr|	Integer|	발송 상세 ID(상세 조회 시 필수)|
|body.data.sendType|	String|	발송 유형(0:Sms, 1:Lms/Mms, 2:Auth)|
|body.data.userId|	String|	발송 요청 ID|
|body.data.adYn|	String|	광고 여부|
|body.data.senderGroupingKey|	String|	발신자 그룹키|
|body.data.recipientGroupingKey|	String|	수신자 그룹키|

## 장문 MMS

### 장문 MMS 발송(첨부 파일 미포함)
※ MMS는 해외발송이 불가능합니다.

#### 요청

[URL]

```
POST  /sms/v2.2/appKeys/{appKey}/sender/mms
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Request body]

```
{
   "templateId":"TemplateId",
   "title":"Title",
   "body":"Body",
   "sendNo":"15446859",
   "requestDate":"2018-08-10 10:00",
   "senderGroupingKey":"SenderGroupingKey",
   "recipientList":[
      {
         "recipientNo":"01000000000",
         "countryCode":"82",
         "internationalRecipientNo":"821000000000",
         "templateParameter":{
            "key":"value"
         },
         "recipientGroupingKey":"recipientGroupingKey"
      }
   ],
   "userId":"UserId"
}
```

|값|	타입| 최대 길이 |	필수|	설명|
|---|---|---|---|---|
|templateId|	String| 50 |	X|	발송 템플릿 ID|
|title|	String| 120 |	O|	제목|
|body|	String| 4000|	O|	본문 |
|sendNo|	String| 13 |	O|	발신 번호|
|requestDate| String| - | X | 예약 일시(yyyy-MM-dd HH:mm)|
|senderGroupingKey| String| 100 | X | 발신자 그룹키 |
|recipientList[].recipientNo| String| 20 |	O|	수신 번호<br/>countryCode와 조합하여 사용 가능|
|recipientList[].countryCode| String| 8 |	X|	국가 번호 [기본값: 82(한국)]<br/>MMS는 해외 발송 불가 |
|recipientList[].internationalRecipientNo| String| 20 | X| 국가 번호가 포함된 수신 번호<br/>예)821012345678<br/>recipientNo가 있을 경우 이 값은 무시된다.<br/>|
|recipientList[].templateParameter|	Object| - |	X|	템플릿 파라미터(템플릿 ID 입력 시)|
|recipientList[].templateParameter.{key}|	String| - |	X|	치환 키(##key##)|
|recipientList[].templateParameter.{value}|	Object| - |	X|	치환 키에 매핑되는 Value값|
|recipientList[].recipientGroupingKey| String| 1000 | X | 수신자 그룹키 |
|userId|	String| 100 |	X | 발송 구분자 ex)admin,system |

#### 응답

```
{
   "header":{
      "isSuccessful":true,
      "resultCode":0,
      "resultMessage":"SUCCESS"
   },
   "body":{
      "data":{
         "requestId":"20180810100630ReZQ6KZzAH0",
         "statusCode":"2",
         "senderGroupingKey":"SenderGroupingKey",
         "sendResultList":[
            {
               "recipientNo":"01000000000",
               "resultCode":0,
               "resultMessage":"SUCCESS",
               "recipientSeq":1,
               "recipientGroupingKey":"RecipientGroupingKey"
            }
         ]
      }
   }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.data.requestId|	String|	요청 ID|
|body.data.statusCode|	String|	요청 상태 코드(1:요청 중, 2:요청 완료, 3:요청 실패)|
|body.data.senderGroupingKey|	String|	발신자 그룹키|
|body.data.sendResultList[].recipientNo| String | 수신 번호|
|body.data.sendResultList[].resultCode| Integer | 결과 코드|
|body.data.sendResultList[].resultMessage| String | 결과 메시지|
|body.data.sendResultList[].recipientSeq| Integer | 수신자 시퀀스(mtPr)|
|body.data.sendResultList[].recipientGroupingKey| String | 수신자 그룹키|

#### 장문 MMS 발송 예제

| Http metho | URL |
| - | - |
| POST | https://api-sms.cloud.toast.com/sms/v2.2/appKeys/{appKey}/sender/mms|

[Request body]
```
{
   "title": "제목",
   "body":"본문",
   "sendNo":"15446859",
   "senderGroupingKey":"SenderGroupingKey",
   "recipientList":[
      {
         "recipientNo":"01000000000",
         "recipientGroupingKey":"RecipientGroupingKey"
      },
      {
         "recipientNo":"01000000001",
         "recipientGroupingKey":"RecipientGroupingKey2"
      }
   ]
}
```

[Response]
```
{
   "header":{
      "isSuccessful":true,
      "resultCode":0,
      "resultMessage":"SUCCESS"
   },
   "body":{
      "data":{
         "requestId":"20180810100630ReZQ6KZzAH0",
         "statusCode":"2",
         "senderGroupingKey":"SenderGroupingKey",
         "sendResultList":[
            {
               "recipientNo":"01000000000",
               "resultCode":0,
               "resultMessage":"SUCCESS",
               "recipientSeq":1,
               "recipientGroupingKey":"RecipientGroupingKey"
            },
            {
               "recipientNo":"01000000001",
               "resultCode":0,
               "resultMessage":"SUCCESS",
               "recipientSeq":2,
               "recipientGroupingKey":"RecipientGroupingKey2"
            }
         ]
      }
   }
}
```

[curl]
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" https://api-sms.cloud.toast.com/sms/v2.2/appKeys/{appKey}/sender/mms -d '{"title": "{제목}","body": "{본문 내용}","sendNo": "{발신 번호}","recipientList": [{"recipientNo": "{수신 번호}","templateParameter": { }}],"userId": ""}'
```

### 장문 MMS 발송(첨부 파일 포함)

#### 첨부 파일 발송 예제

| Http method | URL |
| - | - |
| POST | https://api-sms.cloud.toast.com/sms/v2.2/appKeys/{appKey}/sender/mms |

[Request body]
```
{
    "title": "제목",
    "body": "본문",
    "sendNo": "15446859",
    "senderGroupingKey": "SenderGrouping",
    "attachFileIdList": [0],
    "recipientList": [{
        "recipientNo": "01010000000",
        "recipientGroupingKey":"RecipientGroupingKey"
    }]
}
```

[Response]
```
{
  "header": {
    "isSuccessful": true,
    "resultCode": 0,
    "resultMessage": "SUCCESS"
  },
  "body": {
    "data": {
      "requestId": "1-201607-424631-1",
      "statusCode": "2",
      "senderGroupingKey": "SenderGrouping",
      "sendResultList" : [
          {
              "recipientNo" : {수신 번호},
              "resultCode" :  0,
              "resultMessage" : "SUCCESS"
              "recipientSeq": 1,
              "recipientGroupingKey":"RecipientGroupingKey"
          }
      ]
    }
  }
}
```

##### Description
- 첨부 파일(필드명: attachFileIdList)을 포함한 장문 MMS 발송을 위해서는 사전에 첨부 파일 업로드가 진행되어야 합니다.<br>
- [[첨부 파일 업로드](./api-guide/#binaryUpload)]</a> 가이드를 참고하시기 바랍니다.
- 첨부 이미지 제한 사항
    - 지원 코덱 : jpg
    - 첨부 이미지 개수 : 3개 이하
    - 첨부 이미지 사이즈 : 300K 이하
    - 첨부 이미지 해상도 : 1000 x 1000 이하

### 장문 MMS 발송리스트 조회

#### 요청

[URL]

```
GET  /sms/v2.2/appKeys/{appKey}/sender/mms
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Query parameter]
* requestId 또는 startRequestDate + endRequestDate 또는 startCreateDate + endCreateDate는 필수입니다.
* 등록 날짜/발송 날짜를 동시에 조회하는 경우, 발송 날짜는 무시됩니다.

|값|	타입| 최대 길이 |	필수|	설명|
|---|---|---|---|---|
|requestId|	String| 25 |	필수 |	요청 ID|
|startRequestDate|	String| - |	필수 |	발송 날짜 시작값(yyyy-MM-dd HH:mm:ss)|
|endRequestDate|	String| - |	필수 |	발송 날짜 종료값(yyyy-MM-dd HH:mm:ss)|
|startCreateDate|	String| - |	필수 |	등록 날짜 시작값(yyyy-MM-dd HH:mm:ss)|
|endCreateDate|	String| - |	필수 |	등록 날짜 종료값(yyyy-MM-dd HH:mm:ss)|
|startResultDate|	String| - |	옵션|	수신 날짜 시작값(yyyy-MM-dd HH:mm:ss)|
|endResultDate|	String| - |	옵션|	수신 날짜 종료값(yyyy-MM-dd HH:mm:ss)|
|sendNo|	String| 13 |	옵션|	발신 번호|
|recipientNo|	String| 20 |	옵션|	수신 번호|
|templateId|	String| 50 |	옵션|	템플릿 번호|
|msgStatus|	String| 1 |	옵션| 메시지 상태 코드(0: 실패, 1: 요청, 2: 처리 중, 3:성공, 4:예약취소, 5:중복실패) |
|resultCode|	String| 10 |	옵션|	수신 결과 코드 [[조회 코드표](./error-code/#_2)]|
|subResultCode|	String| 10 |	옵션|	수신 결과 상세 코드 [[조회 코드표](./error-code/#_3)]|
|senderGroupingKey|	String| 100 |	옵션|	발송자 그룹키|
|recipientGroupingKey|	String| 100 |	옵션|	수신자 그룹키|
|pageNum|	Integer| - |	옵션|	페이지 번호(기본값 : 1)|
|pageSize|	Integer| 1000 |	옵션|	조회 수(기본값 : 15)|

#### 응답

```
{
  "header":{
    "resultCode":0,
    "resultMessage":"SUCCESS",
    "isSuccessful":true
  },
  "body":{
    "pageNum":1,
    "pageSize":15,
    "totalCount":1,
    "data":[
      {
        "requestId":"20180810100630ReZQ6KZzAH0",
        "requestDate":"2018-08-10 10:06:30.0",
        "resultDate":"2018-08-10 10:06:42.0",
        "templateId":"TemplateId",
        "templateName":"템플릿명",
        "categoryId":0,
        "categoryName":"카테고리명",
        "title":"제목",
        "body":"본문",
        "sendNo":"15446859",
        "countryCode":"82",
        "recipientNo":"01000000000",
        "msgStatus":"3",
        "msgStatusName":"성공",
        "resultCode":"1000",
        "resultCodeName":"성공",
        "telecomCode":10001,
        "telecomCodeName":"SKT",
        "mtPr":"1",
        "sendType":"0",
        "userId":"tester",
        "adYn":"N",
        "attachFileList":[
          {
            "fileId":0,
            "filePath":"26606/toast-mt-2018-02-08/1639/107303/",
            "fileName":"small_img.jpg"
          }
        ],
        "resultMessage":"",
        "senderGroupingKey":"SenderGroupingKey",
        "recipientGroupingKey":"RecipientGroupingKey"
      }
    ]
  }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body|	Object|	본문 영역|
|body.pageNum|	Integer|	현재 페이지 번호|
|body.pageSize|	Integer|	조회된 데이터 수|
|body.totalCount|	Integer|	총 데이터 수|
|body.data[].requestId|	String|	요청 ID|
|body.data[].requestDate|	String|	발신 일시|
|body.data[].resultDate|	String|	수신 일시|
|body.data[].templateId|	String|	템플릿 ID|
|body.data[].templateName|	String|	템플릿명|
|body.data[].categoryId|	String|	카테고리 ID|
|body.data[].categoryName|	String|	카테고리명|
|body.data[].body|	String|	본문 내용|
|body.data[].sendNo|	String|	발신 번호|
|body.data[].countryCode|	String|	국가 번호|
|body.data[].recipientNo|	String|	수신 번호|
|body.data[].msgStatus|	String|	메시지 상태 코드|
|body.data[].msgStatusName|	String|	메시지 상태 코드명|
|body.data[].resultCode|	String|	수신 결과 코드 [[수신 결과 코드표](./error-code/#emma-v3)]|
|body.data[].resultCodeName|	String|	수신 결과 코드명|
|body.data[].telecomCode|	Integer|	통신사 코드|
|body.data[].telecomCodeName|	String|	통신사명|
|body.data[].mtPr|	Integer|	발송 상세 ID(상세 조회 시 필수)|
|body.data[].sendType|	String|	발송 유형(0:Sms, 1:Lms/Mms, 2:Auth)|
|body.data[].userId|	String|	발송 요청 ID|
|body.data[].adYn|	String|	광고 여부|
|body.data[].attachFileList[].fileId|	Integer|	파일 ID|
|body.data[].attachFileList[].filePath|	String|	파일 저장경로(내부용)|
|body.data[].attachFileList[].fileName|	String|	파일명|
|body.data[].senderGroupingKey|	String|	발신자 그룹키|
|body.data[].recipientGroupingKey|	String|	수신자 그룹키|


### 장문 MMS 발송 단일 조회

#### 요청

[URL]

```
GET  /sms/v2.2/appKeys/{appKey}/sender/mms/{requestId}
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|
|requestId|	String|	요청 ID|

[Query parameter]

|값|	타입|	필수|	설명|
|---|---|---|---|
|mtPr|	Integer|	필수|	발송 상세 ID|

#### 응답

```
{
  "header":{
    "resultCode":0,
    "resultMessage":"SUCCESS",
    "isSuccessful":true
  },
  "body":{
    "data":{
      "requestId":"20180810100630ReZQ6KZzAH0",
      "requestDate":"2018-08-10 10:06:30.0",
      "resultDate":"2018-08-10 10:06:42.0",
      "templateId":"TemplateId",
      "templateName":"템플릿명",
      "categoryId":0,
      "categoryName":"카테고리명",
      "title":"제목",
      "body":"본문",
      "sendNo":"15446859",
      "countryCode":"82",
      "recipientNo":"01000000000",
      "msgStatus":"3",
      "msgStatusName":"성공",
      "resultCode":"1000",
      "resultCodeName":"성공",
      "telecomCode":10001,
      "telecomCodeName":"SKT",
      "mtPr":"1",
      "sendType":"0",
      "userId":"tester",
      "adYn":"N",
      "attachFileList":[
        {
          "fileId":0,
          "filePath":"26606/toast-mt-2018-02-08/1639/107303/",
          "fileName":"small_img.jpg"
        }
      ],
      "resultMessage":"",
      "senderGroupingKey":"SenderGroupingKey",
      "recipientGroupingKey":"RecipientGroupingKey"
    }
  }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body|	Object|	본문 영역|
|body.pageNum|	Integer|	현재 페이지 번호|
|body.pageSize|	Integer|	조회된 데이터 수|
|body.totalCount|	Integer|	총 데이터 수|
|body.data[].requestId|	String|	요청 ID|
|body.data[].requestDate|	String|	발신 일시|
|body.data[].resultDate|	String|	수신 일시|
|body.data[].templateId|	String|	템플릿 ID|
|body.data[].templateName|	String|	템플릿명|
|body.data[].categoryId|	String|	카테고리 ID|
|body.data[].categoryName|	String|	카테고리명|
|body.data[].body|	String|	본문 내용|
|body.data[].sendNo|	String|	발신 번호|
|body.data[].countryCode|	String|	국가 번호|
|body.data[].recipientNo|	String|	수신 번호|
|body.data[].msgStatus|	String|	메시지 상태 코드|
|body.data[].msgStatusName|	String|	메시지 상태 코드명|
|body.data[].resultCode|	String|	수신 결과 코드 [[수신 결과 코드표](./error-code/#emma-v3)]|
|body.data[].resultCodeName|	String|	수신 결과 코드명|
|body.data[].telecomCode|	Integer|	통신사 코드|
|body.data[].telecomCodeName|	String|	통신사명|
|body.data[].mtPr|	Integer|	발송 상세 ID(상세 조회 시 필수)|
|body.data[].sendType|	String|	발송 유형(0:Sms, 1:Lms/Mms, 2:Auth)|
|body.data[].userId|	String|	발송 요청 ID|
|body.data[].adYn|	String|	광고 여부|
|body.data[].attachFileList[].fileId|	Integer|	파일 ID|
|body.data[].attachFileList[].filePath|	String|	파일 저장경로(내부용)|
|body.data[].attachFileList[].fileName|	String|	파일명|
|body.data[].senderGroupingKey|	String|	발신자 그룹키|
|body.data[].recipientGroupingKey|	String|	수신자 그룹키|

## 인증용 SMS(긴급)

### 인증용 SMS 발송

#### 요청

[URL]

```
POST  /sms/v2.2/appKeys/{appKey}/sender/auth/sms
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Request body]

```
{
   "templateId":"TemplateId",
   "body":"본문",
   "sendNo":"15446859",
   "requestDate":"2018-08-10 10:00",
   "senderGroupingKey":"SenderGroupingKey",
   "recipientList":[
      {
         "recipientNo":"01000000000",
         "countryCode":"82",
         "internationalRecipientNo":"821000000000",
         "templateParameter":{
            "key":"value"
         },
         "recipientGroupingKey":"recipientGroupingKey"
      }
   ],
   "userId":"UserId"
}
```

|값|	타입| 최대 길이 |	필수|	설명|
|---|---|---|---|---|
|templateId|	String| 50 |	X|	발송 템플릿 ID|
|body|	String| 표준: 90바이트, 최대: 255자 (EUC-KR 기준) [[주의사항](./api-guide/#precautions)] |	O|	본문 내용|
|sendNo|	String| 13 |	O|	발신 번호|
|requestDate| String| - | X | 예약 일시(yyyy-MM-dd HH:mm)|
|senderGroupingKey| String| 100 | X | 발신자 그룹키 |
|recipientList[].recipientNo|	String| 20 |	O|	수신 번호<br/>countryCode와 조합하여 사용 가능|
|recipientList[].countryCode|	String| 8 |	X|	국가 번호 [기본값: 82(한국)] |
|recipientList[].internationalRecipientNo| String| 20 | X| 국가 번호가 포함된 수신 번호<br/>예)821012345678<br/>recipientNo가 있을 경우 이 값은 무시<br/>|
|recipientList[].templateParameter|	Object| - |	X|	템플릿 파라미터(템플릿 ID 입력 시)|
|recipientList[].templateParameter.{key}| String| - |	X|	치환 키(##key##)|
|recipientList[].templateParameter.{value}| Object| - |	X|	치환 키에 매핑되는 Value값|
|recipientList[].recipientGroupingKey| String| 100 | X | 수신자 그룹키 |
|userId|	String| 100 |	X | 발송 구분자 ex)admin,system |


#### 응답
```
{
   "header":{
      "isSuccessful":true,
      "resultCode":0,
      "resultMessage":"SUCCESS"
   },
   "body":{
      "data":{
         "requestId":"20180810100630ReZQ6KZzAH0",
         "statusCode":"2",
         "senderGroupingKey":"SenderGroupingKey",
         "sendResultList":[
            {
               "recipientNo":"01000000000",
               "resultCode":0,
               "resultMessage":"SUCCESS",
               "recipientSeq":1,
               "recipientGroupingKey":"RecipientGroupingKey"
            }
         ]
      }
   }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.data.requestId|	String|	요청 ID|
|body.data.statusCode|	String|	요청 상태 코드(1:요청 중, 2:요청 완료, 3:요청 실패)|
|body.data.senderGroupingKey|	String|	발신자 그룹키|
|body.data.sendResultList[].recipientNo| String | 수신 번호|
|body.data.sendResultList[].resultCode| Integer | 결과 코드|
|body.data.sendResultList[].resultMessage| String | 결과 메시지|
|body.data.sendResultList[].recipientSeq| Integer | 수신자 시퀀스(mtPr)|
|body.data.sendResultList[].recipientGroupingKey| String | 수신자 그룹키|

#### 예제

| Http metho | URL |
| - | - |
| POST | https://api-sms.cloud.toast.com/sms/v2.2/appKeys/{appKey}/sender/auth/sms|

[Request body]
```
{
   "body":"본문",
   "sendNo":"15446859",
   "senderGroupingKey":"SenderGroupingKey",
   "recipientList":[
      {
         "recipientNo":"01000000000",
         "recipientGroupingKey":"RecipientGroupingKey"
      },
      {
         "recipientNo":"01000000001",
         "recipientGroupingKey":"RecipientGroupingKey2"
      }
   ]
}
```

[Response]
```
{
   "header":{
      "isSuccessful":true,
      "resultCode":0,
      "resultMessage":"SUCCESS"
   },
   "body":{
      "data":{
         "requestId":"20180810100630ReZQ6KZzAH0",
         "statusCode":"2",
         "senderGroupingKey":"SenderGroupingKey",
         "sendResultList":[
            {
               "recipientNo":"01000000000",
               "resultCode":0,
               "resultMessage":"SUCCESS",
               "recipientSeq":1,
               "recipientGroupingKey":"RecipientGroupingKey"
            },
            {
               "recipientNo":"01000000001",
               "resultCode":0,
               "resultMessage":"SUCCESS",
               "recipientSeq":2,
               "recipientGroupingKey":"RecipientGroupingKey2"
            }
         ]
      }
   }
}
```

[curl]
```
curl -X POST -H "Content-Type: application/json;charset=UTF-8" https://api-sms.cloud.toast.com/sms/v2.2/appKeys/{appKey}/sender/auth/sms -d '{"body": "{본문 내용}","sendNo": "{발신 번호}","recipientList":[{"recipientNo": "{수신 번호}","templateParameter": { }}],"userId": ""}'
```

### 인증용 SMS 발송리스트 조회

#### 요청

[URL]

```
GET  /sms/v2.2/appKeys/{appKey}/sender/auth/sms
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|----|---|
|appKey|	String|	고유의 앱키|

[Query parameter]
* requestId 또는 startRequestDate + endRequestDate 또는 startCreateDate + endCreateDate는 필수입니다.
* 등록 날짜/발송 날짜를 동시에 조회하는 경우, 발송 날짜는 무시됩니다.

|값|	타입|	최대 길이 | 필수|	설명|
|---|---|---|---|---|
|requestId|	String| 25 |	필수 |	요청 ID|
|startRequestDate|	String| - |	필수 |	발송 날짜 시작값(yyyy-MM-dd HH:mm:ss)|
|endRequestDate|	String| - |	필수 |	발송 날짜 종료값(yyyy-MM-dd HH:mm:ss)|
|startCreateDate|	String| - |	필수 |	등록 날짜 시작값(yyyy-MM-dd HH:mm:ss)|
|endCreateDate|	String| - |	필수 |	등록 날짜 종료값(yyyy-MM-dd HH:mm:ss)|
|startResultDate|	String| - |	옵션|	수신 날짜 시작값(yyyy-MM-dd HH:mm:ss)|
|endResultDate|	String| - |	옵션|	수신 날짜 종료값(yyyy-MM-dd HH:mm:ss)|
|sendNo|	String| 13 |	옵션|	발신 번호|
|recipientNo|	String| 20 |	옵션|	수신 번호|
|templateId|	String| 50 |	옵션|	템플릿 번호|
|msgStatus|	String| 1 |	옵션| 메시지 상태 코드(0: 실패, 1: 요청, 2: 처리 중, 3:성공, 4:예약취소, 5:중복실패) |
|resultCode|	String| 10 |	옵션|	수신 결과 코드 [[조회 코드표](./error-code/#_2)]|
|subResultCode|	String| 10 |	옵션|	수신 결과 상세 코드 [[조회 코드표](./error-code/#_3)]|
|senderGroupingKey|	String| 100 |	옵션|	발송자 그룹키|
|recipientGroupingKey|	String| 100 |	옵션|	수신자 그룹키|
|pageNum|	Integer| - |	옵션|	페이지 번호(기본값 : 1)|
|pageSize|	Integer| 1000 |	옵션|	조회 수(기본값 : 15)|

#### 응답

```
{
   "header":{
      "resultCode":0,
      "resultMessage":"SUCCESS",
      "isSuccessful":true
   },
   "body":{
      "pageNum":1,
      "pageSize":15,
      "totalCount":1,
      "data":[
         {
            "requestId":"20180810100630ReZQ6KZzAH0",
            "requestDate":"2018-08-10 10:06:30.0",
            "resultDate":"2018-08-10 10:06:42.0",
            "templateId":"TemplateId",
            "templateName":"템플릿명",
            "categoryId":0,
            "categoryName":"카테고리명",
            "body":"단문 테스트",
            "sendNo":"15446859",
            "countryCode":"82",
            "recipientNo":"01000000000",
            "msgStatus":"3",
            "msgStatusName":"성공",
            "resultCode":"1000",
            "resultCodeName":"성공",
            "telecomCode":10001,
            "telecomCodeName":"SKT",
            "mtPr":"1",
            "sendType":"0",
            "userId":"tester",
            "adYn":"N",
            "resultMessage": "",
            "senderGroupingKey":"SenderGroupingKey",
            "recipientGroupingKey":"RecipientGroupingKey"
         }
      ]
   }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.pageNum|	Integer|	현재 페이지 번호|
|body.pageSize|	Integer|	조회된 데이터 수|
|body.totalCount|	Integer|	총 데이터 수|
|body.data[].requestId|	String|	요청 ID|
|body.data[].requestDate|	String|	발신 일시|
|body.data[].resultDate|	String|	수신 일시|
|body.data[].templateId|	String|	템플릿 ID|
|body.data[].templateName|	String|	템플릿명|
|body.data[].categoryId|	String|	카테고리 ID|
|body.data[].categoryName|	String|	카테고리명|
|body.data[].body|	String|	본문 내용|
|body.data[].sendNo|	String|	발신 번호|
|body.data[].countryCode|	String|	국가 번호|
|body.data[].recipientNo|	String|	수신 번호|
|body.data[].msgStatus|	String|	메시지 상태 코드|
|body.data[].msgStatusName|	String|	메시지 상태 코드명|
|body.data[].resultCode|	String|	수신 결과 코드 [[수신 결과 코드표](./error-code/#emma-v3)]|
|body.data[].resultCodeName|	String|	수신 결과 코드명|
|body.data[].telecomCode|	Integer|	통신사 코드|
|body.data[].telecomCodeName|	String|	통신사명|
|body.data[].mtPr|	Integer|	발송 상세 ID(상세 조회 시 필수)|
|body.data[].sendType|	String|	발송 유형(0:Sms, 1:Lms/Mms, 2:Auth)|
|body.data[].userId|	String|	발송 요청 ID|
|body.data[].adYn|	String|	광고 여부|
|body.data[].senderGroupingKey|	String|	발신자 그룹키|
|body.data[].recipientGroupingKey|	String|	수신자 그룹키|

### 인증용 SMS 발송 단일 조회

#### 요청

[URL]

```
GET  /sms/v2.2/appKeys/{appKey}/sender/auth/sms/{requestId}
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|
|requestId|	String|	요청 ID|

[Query parameter]

|값|	타입|	필수|	설명|
|---|----|---|---|
|mtPr|	Integer|	필수|	발송 상세 ID|

#### 응답

```
{
   "header":{
      "resultCode":0,
      "resultMessage":"SUCCESS",
      "isSuccessful":true
   },
   "body":{
      "data":{
         "requestId":"20180810100630ReZQ6KZzAH0",
         "requestDate":"2018-08-10 10:06:30.0",
         "resultDate":"2018-08-10 10:06:42.0",
         "templateId":"TemplateId",
         "templateName":"템플릿명",
         "categoryId":0,
         "categoryName":"카테고리명",
         "body":"본문",
         "sendNo":"15446859",
         "countryCode":"82",
         "recipientNo":"01000000000",
         "msgStatus":"3",
         "msgStatusName":"성공",
         "resultCode":"1000",
         "resultCodeName":"성공",
         "telecomCode":10001,
         "telecomCodeName":"SKT",
         "mtPr":"1",
         "sendType":"0",
         "userId":"tester",
         "adYn":"N",
         "resultMessage": "",
         "senderGroupingKey":"SenderGroupingKey",
         "recipientGroupingKey":"RecipientGroupingKey"
      }
   }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.data.requestId|	String|	요청 ID|
|body.data.requestDate|	String|	발신 일시|
|body.data.resultDate|	String|	수신 일시|
|body.data.templateId|	String|	템플릿 ID|
|body.data.templateName|	String|	템플릿명|
|body.data.categoryId|	String|	카테고리 ID|
|body.data.categoryName|	String|	카테고리명|
|body.data.body|	String|	본문 내용|
|body.data.sendNo|	String|	발신 번호|
|body.data.countryCode|	String|	국가 번호|
|body.data.recipientNo|	String|	수신 번호|
|body.data.msgStatus|	String|	메시지 상태 코드|
|body.data.msgStatusName|	String|	메시지 상태 코드명|
|body.data.resultCode|	String|	수신 결과 코드 [[수신 결과 코드표](./error-code/#emma-v3)]|
|body.data.resultCodeName|	String|	수신 결과 코드명|
|body.data.telecomCode|	Integer|	통신사 코드|
|body.data.telecomCodeName|	String|	통신사명|
|body.data.mtPr|	Integer|	발송 상세 ID(상세 조회 시 필수)|
|body.data.sendType|	String|	발송 유형(0:Sms, 1:Lms/Mms, 2:Auth)|
|body.data.userId|	String|	발송 요청 ID|
|body.data.adYn|	String|	광고 여부|
|body.data.senderGroupingKey|	String|	발신자 그룹키|
|body.data.recipientGroupingKey|	String|	수신자 그룹키|

## 광고 문자
### 광고성 SMS 발송

#### 요청

[URL]

```
POST  /sms/v2.2/appKeys/{appKey}/sender/ad-sms
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Request Body]
위에 SMS 발송과 동일.
[[Request Body 참고](./api-guide/#sms_2)]

<span style="color:red">단, 본문에 아래 문구가 필수로 들어가야 합니다.</span>
080 번호는 콘솔의 **080 수신 거부 설정** 탭에서 확인할 수 있습니다.
```
(광고)

[무료 수신 거부]080XXXXXXX
```


### 광고성 MMS 발송
※ MMS는 해외발송이 불가능합니다.

#### 요청
[URL]

```
POST  /sms/v2.2/appKeys/{appKey}/sender/ad-mms
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Request Body]
위에 MMS 발송과 동일.
[[Request Body 참고](./api-guide/#mms_1)]

<span style="color:red">단, 본문에 아래 문구가 필수로 들어가야 합니다.</span>
080 번호는 콘솔의 **080 수신 거부 설정** 탭에서 확인할 수 있습니다.
```
(광고)

[무료 수신 거부]080XXXXXXX
```



## 결과 업데이트 기준 메시지 조회
* 해당 API는 메시지 발송 결과 업데이트 시간 기준으로 조회됩니다.
* 단말기 발송 결과를 서비스에서 가져가 사용하시는 경우 이 API를 사용해주세요.

### 메시지 조회

#### 요청

[URL]

```
GET /sms/v2.2/appKeys/{appKey}/message-results?startUpdateDate={startUpdateDate}&endUpdateDate={endUpdateDate}&messageType={messageType}&pageNum={pageNum}&pageSize={pageSize}
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Query parameter]

|값|	타입|	필수|	설명|
|---|---|---|---|
|startUpdateDate|	String|	필수|	결과 업데이트 조회 시작 시간 <br/>yyyy-MM-dd HH:mm:ss|
|endUpdateDate|	String|	필수|	결과 업데이트 조회 종료 시간 <br/>yyyy-MM-dd HH:mm:ss|
|messageType|	String|	옵션|	메시지 타입 (SMS/LMS/MMS/AUTH)|
| pageNum | Integer | 옵션 | 페이지 번호(기본값:1) |
| pageSize | Integer | 옵션 |조회 수(기본값:15) |

#### 응답
```
{
  "header":{
    "isSuccessful":true,
    "resultCode":0,
    "resultMessage":"Success."
  },
  "body":{
    "pageNum":1,
    "pageSize":15,
    "totalCount":1,
    "data":[
        {
          "messageType":"SMS",
          "requestId":"",
          "recipientSeq":0,
          "resultCode":"1000",
          "resultCodeName":"성공",
          "requestDate":"2018-10-04 16:16:00.0",
          "resultDate":"2018-10-04 16:17:10.0",
          "updateDate":"2018-10-04 16:17:15.0",
          "telecomCode": "10003",
          "telecomCodeName": "LGU",
          "senderGroupingKey":"senderGroupingKey",
          "recipientGroupingKey":"recipientGroupingKey"
        }
    ]
  }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.data.resultUpdateList[].messageType|String| 메시지 타입(SMS/LMS/MMS/AUTH)|
|body.data.resultUpdateList[].requestId | String | 요청 ID |
|body.data.resultUpdateList[].recipientSeq | Integer | 수신자 시퀀스 |
|body.data.resultUpdateList[].resultCode | String | 결과 코드 |
|body.data.resultUpdateList[].resultCodeName | String | 결과 코드명 |
|body.data.resultUpdateList[].requestDate | String | 요청 일시(yyyy-MM-dd HH:mm:ss.S) |
|body.data.resultUpdateList[].resultDate | String | 수신 일시(yyyy-MM-dd HH:mm:ss.S) |
|body.data.resultUpdateList[].updateDate | String | 결과 업데이트 일시(yyyy-MM-dd HH:mm:ss.S) |
|body.data.resultUpdateList[].telecomCode | String | 통신사 코드 |
|body.data.resultUpdateList[].telecomCodeName | String | 통신사 코드명 |
|body.data.resultUpdateList[].senderGroupingKey | String | 발신자 그룹 키 |
|body.data.resultUpdateList[].recipientGroupingKey | String | 수신자 그룹 키 |


## 태그 발송

### 태그 SMS 발송

#### 요청

[URL]

```
POST /sms/v2.2/appKeys/{appKey}/tag-sender/sms
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Request body]

```
{
    "body":"본문",
    "sendNo":"15446859",
    "requestDate":"2018-03-22 10:00",
    "templateId":"TemplateId",
    "templateParameter" : {
      "key" : "value"
    },
    "tagExpression":[
        "tag1",
        "AND",
        "tag2"
     ],
    "userId":"user_id",
    "adYn":"N",
    "autoSendYn":"N"
}
```

|값|	타입|	최대 길이 | 필수|	설명|
|---|---|---|---|---|
|body|	String| 표준: 90바이트, 최대: 255자 (EUC-KR 기준) [[주의사항](./api-guide/#precautions)] |	O|	본문 내용|
| sendNo | String | 13 | O | 발신 번호 |
| requestDate| String| - | X | 예약 일시(yyyy-MM-dd HH:mm)|
| templateId | String | 50 | X | 템플릿 ID |
| templateParameter | Map<String, String> | - | X | 템플릿 파라미터 |
| tagExpression | List<String> | - | O | 태그 표현식<br/>ex) ["tagA","AND","tabB"] |
| userId | String | 100 | X | 요청한 유저 ID |
| adYn | String | 1 | X | 광고 여부(기본값: N) |
| autoSendYn | String | 1 | X | 자동 발송(즉시 발송) 여부 (기본값: Y) |


#### 응답
```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "body": {
        "data": {
            "requestId": "201808130914102Y94ABMNF30"
        }
    }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.data.requestId|	String|	요청 ID|

### 태그 LMS 발송
※ LMS는 해외발송이 불가능합니다.

#### 요청

[URL]

```
POST  /sms/v2.2/appKeys/{appKey}/tag-sender/mms
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Request body]
```
{
    "body":"본문",
    "sendNo":"15446859",
    "requestDate":"2018-03-22 10:00",
    "templateId":"TemplateId",
    "templateParameter" : {
        "key" : "value"
    },
    "attachFileIdList" : [
     1,
     2,
     3
    ],
    "tagExpression":[
        "tag1",
        "AND",
        "tag2"
     ],
    "userId":"user_id",
    "adYn":"N",
    "autoSendYn":"N"
}
```

|값|	타입|	최대 길이 | 필수|	설명|
|---|---|---|---|---|
| title | String | 120 | O | 문자 제목 |
| body | String | 4000 | O | 문자 내용 |
| sendNo | String | 13 | O | 발신 번호 |
| requestDate| String| - | X | 예약 일시(yyyy-MM-dd HH:mm)|
| templateId | String | 50 | X | 템플릿 ID |
| templateParameter | Map<String, String> | - | X | 템플릿 파라미터 |
| tagExpression | List<String> | - | O | 태그 표현식<br/>ex) ["tagA","AND","tabB"] |
| attachFileIdList | List<Integer> | - | X | 첨부 파일 ID(fileId) |
| userId | String | 100 | X | 요청한 유저 ID |
| adYn | String | 1 | X | 광고 여부(기본값: N) |
| autoSendYn | String | 1 | X | 자동 발송(즉시 발송) 여부 (기본 Y) |


#### 응답
```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "body": {
        "data": {
            "requestId": "201808130914102Y94ABMNF30"
        }
    }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.data.requestId|	String|	요청 ID|


### 태그 발송 리스트 조회

#### 요청

[URL]

```
GET /sms/v2.2/appKeys/{appKey}/tag-sender
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Query parameter]
* requestId 또는 startRequestDate + endRequestDate 또는 startCreateDate + endCreateDate는 필수입니다.

|값|	타입| 최대 길이 |	필수|	설명|
|---|---|---|---|---|
| appKey | String| - | O | 앱키 |
| sendType | required, String | 1 | O | 발송 유형<br>SMS : "0",<br>MMS : "1" |
| requestId | String | - | O | 요청 ID |
| startRequestDate | String | - | O | 발송 날짜 시작 |
| endRequestDate | String | - | O | 발송 날짜 종료 |
| startCreateDate |	String| - |	O |	등록 날짜 시작 |
| endCreateDate |	String| - |	O |	등록 날짜 종료 |
| statusCode | String | 10 | X | 발송 상태 코드<br>WAIT : "MAS00"<br>READY : "MAS01"<br>SENDREADY : "MAS09"<br>SENDWAIT : "MAS10"<br>SENDING : "MAS11"<br>COMPLETE : "MAS19"<br>CANCEL : "MAS91"<br>FAIL : "MAS99" |
| pageNum | optional, Integer | - | X | 페이지 번호 |
| pageSize | optional, Integer | 1000 | X | 조회 수 |

#### 응답
```
{
    "header" : {
    "isSuccessful" :  true,
    "resultCode" :  0,
    "resultMessage" :  "."
    },
    "body":{
        "pageNum":0,
        "pageSize":0,
        "totalCount":0,
        "data" :[{
                "requestId": "20171220141558eonMsyDI6P0",
                "requestIp": "127.0.0.1",
                "sendType": "0",
                "templateId": "TemplateId",
                "templateName": "템플릿명",
                "masterStatusCode": "READY",
                "sendNo": "15446859",
                "requestDate": "2017-12-20 14:15:58",
                "tagExpression": [
                    "Kb6BjCY1"
                ],
                "title": "제목",
                "body": "본문",
                "adYn": "N",
                "autoSendYn": "Y",
                "sendErrorCount": 0,
                "createUser": "CreateUser",
                "createDate": "2017-12-20 14:15:58.0",
                "updateUser": "UpdateUser",
                "updateDate": "2017-12-20 14:15:58.0"
            }
        ]
    }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.data[].requestId | String | 요청 ID |
|body.data[].requestIp | String | 요청 IP |
|body.data[].requestDate | String | 요청 시간 |
|body.data[].tagSendStatus | String | 태그 발송 상태 |
|body.data[].tagExpression[] | List<String> | 태그 표현식 |
|body.data[].templateId | String | 템플릿 ID |
|body.data[].templateName | String | 템플릿명 |
|body.data[].senderName | String | 발신자명 |
|body.data[].senderMail | String | 발신자 주소 |
|body.data[].title | String | 제목 |
|body.data[].body | String | 내용 |
|body.data[].adYn | String | 광고 여부 |
|body.data[].autoSendYn | String | 자동 발송 여부 |
|body.data[].sendErrorCount | Integer | 에러 수신자 건수 |
|body.data[].createUser | String | 생성자 |
|body.data[].createDate | String | 생성 일시 |
|body.data[].updateUser | String | 수정한 사용자 |
|body.data[].updateDate | String | 수정 날짜 |


### 태그 발송 수신자 리스트 조회

#### 요청

[URL]

```
GET /sms/v2.2/appKeys/{appKey}/tag-sender/{requestId}
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|
| requestId | String | 요청 ID |

[Query parameter]
* requestId 또는 startRequestDate + endRequestDate는 필수입니다.

|값|	타입| 최대 길이 |	필수|	설명|
|---|---|---|---|---|
| recipientNum | String | 20 | X | 수신자 번호 |
| startRequestDate | String | - | O | 발송 요청 시작 날짜 |
| endRequestDate | String | - | O | 발송 요청 종료 날짜 |
| startResultDate | String | - | X | 수신 시작 날짜 |
| endResultDate | String | - | X | 수신 종료 날짜 |
| msgStatusName | String | 10 |  X | 메시지 상태 코드<br/> - READY:준비<br/> - SENDING:발송 요청 중<br/> - COMPLETED : 발송요청 완료<br/> - FAILED : 발송 실패  |
| resultCode | String | 10 | X | 수신 결과 코드 |
| pageNum | Integer | - | X | 페이지 번호 |
| pageSize | Integer | 1000 | X | 조회 수 |

#### 응답
```
{
    "header" : {
    "isSuccessful" :  true,
    "resultCode" :  0,
    "resultMessage" :  "."
    },
    "body":{
        "pageNum":0,
        "pageSize":0,
        "totalCount":0,
        "data" :[
        {
            "requestId": "20180813022044Jps2xJ1qsv0",
            "recipientSeq": 99,
            "countryCode": "82",
            "recipientNo": "01000000000",
            "requestDate": "2018-08-13 02:20:44.0",
            "msgStatus": "3",
            "msgStatusName": "COMPLETED",
            "resultCode": "3015",
            "receiveDate": "2018-08-13 02:20:53.0",
            "createDate": "2018-08-13 02:20:46.0",
            "updateDate": "2018-08-13 02:27:00.0"
        }
        ]
    }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.data.requestId | String | 요청 ID |
|body.data.recipientSeq | Integer | 수신자 시퀀스 |
|body.data.countryCode | String | 수신자 국가코드 |
|body.data.recipientNo | String | 수신자 번호 |
|body.data.requestDate | String | 요청 일시 |
|body.data.msgStatus | String | 메시지 상태 코드 |
|body.data.msgStatusName | String | 메시지 상태 코드명 |
|body.data.resultCode | String | 수신 결과 코드[[수신 결과 코드표](./error-code/#emma-v3)] |
|body.data.receiveDate | String | 수신 일시 |
|body.data.createDate | String | 등록 일시 |
|body.data.updateDate | String | 수정 날짜 |



### 태그 발송 수신자 리스트 상세 조회

#### 요청

[URL]

```
GET /sms/v2.2/appKeys/{appKey}/tag-sender/{requestId}/{recipientSeq}
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|
| requestId | String | 요청 ID |
| recipientSeq | String | 시퀀스 |

[Request body]

```
X
```

#### 응답
```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "body": {
        "data": {
            "requestId": "20180813022044Jps2xJ1qsv0",
            "recipientSeq": 14,
            "sendType": "0",
            "messageType": "SMS",
            "templateId": "TemplateId",
            "templateName": "템플릿명",
            "sendNo": "15446859",
            "recipientNum": "01000000000",
            "countryCode": "82",
            "title": "제목",
            "body": "본문",
            "requestDate": "2018-08-13 02:20:44.0",
            "msgStatusName": "COMPLETED",
            "msgStatus": "3",
            "resultCode": "3015",
            "receiveDate": "2018-08-13 02:20:48.0",
            "attachFileList": []
        }
    }
}
```

|값|	타입|	설명|
|---|---|---|
|header|	Object|	헤더 영역|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.data.requestId | String | 요청 ID |
|body.data.recipientSeq | Integer | 수신자 시퀀스 |
|body.data.sendType | String | 발송 유형 |
|body.data.messageType | String | 메시지 타입 |
|body.data.templateId | String | 템플릿 ID |
|body.data.templateName | String | 템플릿명 |
|body.data.sendNo | String | 발신 번호 |
|body.data.title | String | 제목 |
|body.data.body | String | 내용 |
|body.data.recipientNum | String | 수신자 번호 |
|body.data.requestDate | String | 요청 일시 |
|body.data.msgStatusName | String | 메시지 상태 이름 |
|body.data.resultCode | String | 수신 결과 코드[[수신 결과 코드표](./error-code/#emma-v3)] |
|body.data.receiveDate | String | 수신 일시 |
|body.data.attachFileList[].filePath | String | 첨부 파일 - 경로 |
|body.data.attachFileList[].fileName | String | 첨부 파일 - 파일명 |
|body.data.attachFileList[].fileSize | Long | 첨부 파일 - 사이즈 |
|body.data.attachFileList[].fileSequence | Integer | 첨부 파일 - 파일 번호 |
|body.data.attachFileList[].createDate | String | 첨부 파일 - 생성 일시 |
|body.data.attachFileList[].updateDate | String | 첨부 파일 - 수정 날짜 |


<span id="binaryUpload"></span>
## 첨부 파일

### 첨부 파일 업로드

[URL]

```
POST  /sms/v2.2/appKeys/{appKey}/attachfile/binaryUpload
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Request body]

```
{
    "fileName": "attachment.jpg",
    "createUser": "CreateUser",
    // "fileBody": [0,10,16]
    "fileBody": "{byte[] -> Base64 인코딩한 값}"
}
```

|값|	타입| 최대 길이 |	필수|	설명|
|---|----|---|----|---|
|fileName|	String|	45 | 필수|	파일 이름(확장자는 jpg, jpeg(소문자)만 가능)|
|fileBody|	Byte[]| 300K |	필수| 파일 byte[]를 Base64로 인코딩한 값.<br/>* 또는 바이트 배열 값|
|createUser|	String|	100 | 필수|	파일 업로드 사용자 정보|

#### 응답

```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "body": {
        "data": {
            "fileId": 0,
            "fileName": "attachment.jpg",
            "filePath": "0/toast-mt-2018-08-10/1609/178576"
        }
    }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.data.fileId|	Integer|	파일 ID|
|body.data.fileName|	String|	파일명|
|body.data.filePath|	String|	첨부 파일 기본 경로 <br/> (https://domain/attachFile/filePath/fileName)|




#### 첨부 파일 업로드 예제

| Http method | URL |
| - | - |
| POST | https://api-sms.cloud.toast.com/sms/v2.2/appKeys/{appKey}/attachfile/binaryUpload |

[Request body]
```
{
  "fileName": "attachement.jpg",
  "createUser": "CreateUser",
  "fileBody": "/9j/4AAQSkZJRgABAQEAYABgAAD/4RDSRXhpZgAATU0AKgAAAAgABAE7AAIAAAAESkdHAIdpAAQAAAABAAAISpydAAEAAAAIAAAQwuocAAcAAAgMAAAAPgAAAAAc6gAAAAgAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAFkAMAAgAAABQAABCYkAQAAgAAABQAABCskpEAAgAAAAM1MgAAkpIAAgAAAAM1MgAA6hwABwAACAwAAAiMAAAAABzqAAAACAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAMjAxODowMjowOCAxNjoyOTo0MQAyMDE4OjAyOjA4IDE2OjI5OjQxAAAASgBHAEcAAAD/4QsWaHR0cDovL25zLmFkb2JlLmNvbS94YXAvMS4wLwA8P3hwYWNrZXQgYmVnaW49J++7vycgaWQ9J1c1TTBNcENlaGlIenJlU3pOVGN6a2M5ZCc/Pg0KPHg6eG1wbWV0YSB4bWxuczp4PSJhZG9iZTpuczptZXRhLyI+PHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj48cmRmOkRlc2NyaXB0aW9uIHJkZjphYm91dD0idXVpZDpmYWY1YmRkNS1iYTNkLTExZGEtYWQzMS1kMzNkNzUxODJmMWIiIHhtbG5zOmRjPSJodHRwOi8vcHVybC5vcmcvZGMvZWxlbWVudHMvMS4xLyIvPjxyZGY6RGVzY3JpcHRpb24gcmRmOmFib3V0PSJ1dWlkOmZhZjViZGQ1LWJhM2QtMTFkYS1hZDMxLWQzM2Q3NTE4MmYxYiIgeG1sbnM6eG1wPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvIj48eG1wOkNyZWF0ZURhdGU+MjAxOC0wMi0wOFQxNjoyOTo0MS41MTc8L3htcDpDcmVhdGVEYXRlPjwvcmRmOkRlc2NyaXB0aW9uPjxyZGY6RGVzY3JpcHRpb24gcmRmOmFib3V0PSJ1dWlkOmZhZjViZGQ1LWJhM2QtMTFkYS1hZDMxLWQzM2Q3NTE4MmYxYiIgeG1sbnM6ZGM9Imh0dHA6Ly9wdXJsLm9yZy9kYy9lbGVtZW50cy8xLjEvIj48ZGM6Y3JlYXRvcj48cmRmOlNlcSB4bWxuczpyZGY9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkvMDIvMjItcmRmLXN5bnRheC1ucyMiPjxyZGY6bGk+SkdHPC9yZGY6bGk+PC9yZGY6U2VxPg0KCQkJPC9kYzpjcmVhdG9yPjwvcmRmOkRlc2NyaXB0aW9uPjwvcmRmOlJERj48L3g6eG1wbWV0YT4NCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIAogICAgICAgICAgICAgICAgICAgICAgICAgICAgPD94cGFja2V0IGVuZD0ndyc/Pv/bAEMABwUFBgUEBwYFBggHBwgKEQsKCQkKFQ8QDBEYFRoZGBUYFxseJyEbHSUdFxgiLiIlKCkrLCsaIC8zLyoyJyorKv/bAEMBBwgICgkKFAsLFCocGBwqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKioqKv/AABEIABoAHQMBIgACEQEDEQH/xAAfAAABBQEBAQEBAQAAAAAAAAAAAQIDBAUGBwgJCgv/xAC1EAACAQMDAgQDBQUEBAAAAX0BAgMABBEFEiExQQYTUWEHInEUMoGRoQgjQrHBFVLR8CQzYnKCCQoWFxgZGiUmJygpKjQ1Njc4OTpDREVGR0hJSlNUVVZXWFlaY2RlZmdoaWpzdHV2d3h5eoOEhYaHiImKkpOUlZaXmJmaoqOkpaanqKmqsrO0tba3uLm6wsPExcbHyMnK0tPU1dbX2Nna4eLj5OXm5+jp6vHy8/T19vf4+fr/xAAfAQADAQEBAQEBAQEBAAAAAAAAAQIDBAUGBwgJCgv/xAC1EQACAQIEBAMEBwUEBAABAncAAQIDEQQFITEGEkFRB2FxEyIygQgUQpGhscEJIzNS8BVictEKFiQ04SXxFxgZGiYnKCkqNTY3ODk6Q0RFRkdISUpTVFVWV1hZWmNkZWZnaGlqc3R1dnd4eXqCg4SFhoeIiYqSk5SVlpeYmZqio6Slpqeoqaqys7S1tre4ubrCw8TFxsfIycrS09TV1tfY2dri4+Tl5ufo6ery8/T19vf4+fr/2gAMAwEAAhEDEQA/APD6K6e88IxWemTamdQaSx+zW8trJ5GDPJLkGMjd8u0pKCcn7nTmq/jDTbHSvH2qadao1vY2940aqmZGRAe24/MQPU8+tVs7ev4f8OT0uYFFdvrmhWmo/Ee30wXVpp9rcWlvIs0FmIAQbZXX90ZCPMbgY34LHqM1zes6bbaXq81mHv18rAK3tiLeZWxyGj3tt/M5HpTWrsGyv/Wpb1bXIp/DekaLYzXEsFj5kztcRhP3shGVUBmyq44ORksxwM1JrfiyLXtSa/u/DmkpdSXHnzyRPdDz/VWBmIAPfaFPoRXO0UgOk1Pxemq6vb3914d0jMMAgeFftBjmRYxGgbMpYFVUYKlTnqTWfruuz69dQSTW8FrFa26W1vb24bZFGucKC7Mx5JOWYnn0wKy6KAP/2Q=="
}
```

[Response]
```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "body": {
        "data": {
            "fileId": 0,
            "fileName": "attachment.jpg",
            "filePath": "0/toast-mt-2018-08-10/1609/178576"
        }
    }
}
```

## 카테고리

### 카테고리 등록

#### 요청

[URL]

```
POST  /sms/v2.2/appKeys/{appKey}/categories
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Request body]

```
{
   "categoryParentId" : 0,
   "categoryName" : "",
   "categoryDesc" : "",
   "useYn" : "",
   "createUser" : ""
}
```

|값|	타입|	최대 길이 | 필수|	설명|
|---|---|---|---|---|
| categoryParentId |	Integer|	- | 옵션 | 부모 카테고리 ID [기본값: 최상위 카테고리]  |
| categoryName | String | 50 | 필수 | 카테고리 ID |
| categoryDesc |	String| 100 |	옵션 |	카테고리명|
| useYn |	String| 1 |	필수| 사용 여부(Y/N)|
| createUser |	String| 100 | 옵션| 등록한 사용자|

##### Description
- categoryParentId 값이 비어있는 경우, 최상위 카테고리 바로 아래에 등록됩니다.


#### 응답

```
{  
   "header":{  
      "isSuccessful":true,
      "resultCode":0,
      "resultMessage":"SUCCESS"
   },
   "body":{  
      "data":{  
         "categoryId":0,
         "categoryParentId":0,
         "depth":0,
         "sort" :0,
         "categoryName":"",
         "categoryDesc":"",
         "useYn":"",
         "createUser":""
      }
   }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.data[].categoryId|	Integer|	카테고리 ID|
|body.data[].categoryParentId|	Integer|	부모 카테고리 ID|
|body.data[].depth|	Integer|	카테고리 깊이|
|body.data[].sort|	Integer|	카테고리 정렬 순서|
|body.data[].categoryName|	String|	카테고리명|
|body.data[].categorycategoryDescame|	String|	카테고리 설명|
|body.data[].useYn|	String|	사용 여부|
|body.data[].createUser|	String|	등록한 사용자|

### 카테고리 리스트 조회

#### 요청

[URL]

```
GET  /sms/v2.2/appKeys/{appKey}/categories
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Query parameter]

|값|	타입|	최대 길이 | 필수|	설명|
|---|---|---|---|---|
|pageNum|	Integer| - |	옵션|	페이지 번호(기본값 : 1)|
|pageSize|	Integer| 1000 |	옵션|	조회 수(기본값 : 15)|

#### 응답

```
{  
   "header":{  
      "isSuccessful":true,
      "resultCode":0,
      "resultMessage":"SUCCESS"
   },
   "body":{  
      "pageNum":1,
      "pageSize":15,
      "totalCount":1,
      "data":[  
         {  
            "categoryId":137612,
            "categoryParentId":0,
            "depth":0,
            "sort" :0,
            "categoryName":"카테고리",
            "categoryDesc":"최상위 카테고리",
            "useYn":"Y",
            "createDate":"2018-04-17 15:39:56.0",
            "createUser":"bb076dc0-ef5e-11e7-9ede-005056ac7022",
            "updateDate":"2018-04-17 15:39:56.0",
            "updateUser":"bb076dc0-ef5e-11e7-9ede-005056ac7022"
         }
      ]
   }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.pageNum|	Integer|	현재 페이지 번호|
|body.pageSize|	Integer|	조회된 데이터 수|
|body.totalCount|	Integer|	총 데이터 수|
|body.data[].categoryId|	Integer|	카테고리 ID|
|body.data[].categoryParentId|	Integer|	부모 카테고리 ID|
|body.data[].depth|	Integer|	카테고리 깊이|
|body.data[].sort|	Integer|	카테고리 정렬 순서|
|body.data[].categoryName|	String|	카테고리명|
|body.data[].categorycategoryDescame|	String|	카테고리 설명|
|body.data[].useYn|	String|	사용 여부|
|body.data[].createDate|	String|	등록 날짜|
|body.data[].createUser|	String|	등록한 사용자|
|body.data[].updateDate|	String|	수정 날짜|
|body.data[].updateUser|	String|	수정한 사용자|

### 카테고리 단건 조회

#### 요청

[URL]

```
GET  /sms/v2.2/appKeys/{appKey}/categories/{categoryId}
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|
|categoryId|	String|	카테고리 ID|

#### 응답

```
{  
   "header":{  
      "isSuccessful":true,
      "resultCode":0,
      "resultMessage":"SUCCESS"
   },
   "body":{  
      "data":[  
         {  
            "categoryId":137612,
            "categoryParentId":0,
            "depth":0,
            "sort":0,
            "categoryName":"카테고리",
            "categoryDesc":"최상위 카테고리",
            "useYn":"Y",
            "createDate":"2018-04-17 15:39:56.0",
            "createUser":"bb076dc0-ef5e-11e7-9ede-005056ac7022",
            "updateDate":"2018-04-17 15:39:56.0",
            "updateUser":"bb076dc0-ef5e-11e7-9ede-005056ac7022"
         }
      ]
   }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.data[].categoryId|	Integer|	카테고리 ID|
|body.data[].categoryParentId|	Integer|	부모 카테고리 ID|
|body.data[].depth|	Integer|	카테고리 깊이|
|body.data[].sort|	Integer|	카테고리 정렬 순서|
|body.data[].categoryName|	String|	카테고리명|
|body.data[].categorycategoryDescame|	String|	카테고리 설명|
|body.data[].useYn|	String|	사용 여부|
|body.data[].createDate|	String|	등록 날짜|
|body.data[].createUser|	String|	등록한 사용자|
|body.data[].updateDate|	String|	수정 날짜|
|body.data[].updateUser|	String|	수정한 사용자|


### 카테고리 수정

#### 요청

[URL]

```
PUT  /sms/v2.2/appKeys/{appKey}/categories/{categoryId}
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|
|categoryId|	String|	카테고리 ID|

[Request body]

```
{
   "categoryName" : "",
   "categoryDesc" : "",
   "useYn" : "",
   "updateUser" : ""
}
```

|값|	타입|	최대 길이 | 필수|	설명|
|---|---|---|---|---|
| categoryName | String | 50 | 필수 | 카테고리 ID |
| categoryDesc |	String| 100 |	옵션 |	카테고리명|
| useYn |	String| 1 |	필수| 사용 여부(Y/N)|
| updateUser |	String| 100 |	옵션| 수정한 사용자|


#### 응답

```
{
   "header" : {
      "isSuccessful" : true,
      "resultCode" : "",
      "resultMessage" : ""
   }
}
```

### 카테고리 삭제

#### 요청

[URL]

```
DELETE  /sms/v2.2/appKeys/{appKey}/categories/{categoryId}
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|
|categoryId|	String|	카테고리 ID|

#### 응답

```
{
   "header" : {
      "isSuccessful" : true,
      "resultCode" : "",
      "resultMessage" : ""
   }
}
```




## 템플릿

### 템플릿 등록

#### 요청

[URL]

```
POST  /sms/v2.2/appKeys/{appKey}/templates
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Request body]

```
{
   "categoryId" : 0,
   "templateId" : "",
   "templateName" : "",
   "templateDesc" : "",
   "sendNo" : "",
   "sendType" : "",
   "title" : "",
   "body" : "",
   "useYn" : "",
   "attachFileIdList" : [
      0,
      1
   ]
}
```

|값|	타입|	최대 길이 | 필수|	설명|
|---|---|---|---|---|
| categoryId |	Integer|	- | 필수 |	카테고리 ID |
| templateId | String | 50 | 필수 | 템플릿 ID |
| templateName |	String| 50 |	필수 |	템플릿명|
| templateDesc |	String| 100 |	옵션 |	템플릿 설명|
| sendNo | String | 13 | 필수 | 발신 번호 |
| sendType | String| 1| 필수| 발송 유형(0:Sms, 1:Lms/Mms) |
| title | String | 120 | 옵션 | 문자 제목(발송 유형이 Lms/MmS인 경우 필수) |
| body | String | 4000 | 필수 | 문자 내용 |
| useYn |	String| 1 |	필수|	사용 여부(Y/N)|
| attachFileIdList | List<Integer> | - | X | 첨부 파일 ID(fileId) |


#### 응답

```
{
   "header" : {
      "isSuccessful" : true,
      "resultCode" : "",
      "resultMessage" : ""
   }
}
```


#### 템플릿 등록 예시

| Http method | URL |
| - | - |
| POST | https://api-sms.cloud.toast.com/sms/v2.2/appKeys/{appKey}/templates |

[Request body]
```
{
   "categoryId" : 199376 ,
   "templateId" : "TemplateId",
   "templateName" : "템플릿 발송 예시",
   "templateDesc" : "템플릿 발송 예시",
   "sendNo" : "01012341234",
   "sendType" : "1",
   "title" : "example",
   "body" : "일반 발송 테스트용 입니다.\r\n##key1## 님 안녕하세요.\r\n##key2## 입니다.",
   "useYn" : "Y",
   "attachFileIdList" : [
     123123,
     456456
   ]
}
```

[Response]
```
{
   "header" : {
      "isSuccessful" : true,
      "resultCode" : "",
      "resultMessage" : ""
   }
}
```



##### Description
- 첨부 파일(필드명: attachFileIdList)을 포함한 템플릿 등록은 사전에 첨부 파일 업로드가 진행되어야 합니다.<br>
- [[첨부 파일 업로드](./api-guide/#binaryUpload)]</a> 가이드를 참고하시기 바랍니다.
- 첨부 이미지 제한 사항
    - 지원 코덱 : jpg
    - 첨부 이미지 개수 : 3개 이하
    - 첨부 이미지 사이즈 : 300K 이하
    - 첨부 이미지 해상도 : 1000 x 1000 이하


### 템플릿 발송(본문 수정이 필요 없는 경우)

|Http method| 종류 | URL|
| - | - | - |
| POST | SMS | https://api-sms.cloud.toast.com/sms/v2.2/appKeys/{appKey}/sender/sms |
| POST | MMS | https://api-sms.cloud.toast.com/sms/v2.2/appKeys/{appKey}/sender/mms |

Request URL은 템플릿 등록시 선택한 발송 유형으로 선택하여 발송합니다.

**Request parameter body 안의 값이 공란이면 해당 templateId의 body 내용으로 치환합니다.**

[Request body] 치환하고자 하는 키와 값을 key,value로 대입.

```
{
    "templateId": "TemplateId",
    "senderGroupingKey": "SenderGroupingKey",
    "recipientList": [{
        "recipientNo": "01000000000",
        "templateParameter": {
          "key1" : "Toast Cloud",
          "key2" : "SMS"
        },
        "recipientGroupingKey": "RecipientGroupingKey"
    }]
}
```

[Response]
```
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "body": {
        "data": {
            "requestId": "20180813095534I4VcVuPBpd0",
            "statusCode": "2",
            "senderGroupingKey": "SenderGroupingKey",
            "sendResultList": [
                {
                    "recipientNo": "01000000000",
                    "resultCode": 0,
                    "resultMessage": "SUCCESS",
                    "recipientSeq": 1,
                    "recipientGroupingKey": "RecipientGroupingKey"
                }
            ]
        }
    }
}
```

![[그림 1] 템플릿 발송 성공](http://static.toastoven.net/prod_sms/img_27.png)

### 템플릿 발송(본문 수정이 필요한 경우)

#### 템플릿 발송 예제

|Http method| 종류 | URL|
| - | - | - |
| POST | SMS | https://api-sms.cloud.toast.com/sms/v2.2/appKeys/{appKey}/sender/sms |
| POST | MMS | https://api-sms.cloud.toast.com/sms/v2.2/appKeys/{appKey}/sender/mms |


Request URL은 템플릿 등록 시 선택한 발송 유형으로 선택하여 발송합니다.

**템플릿 ID와 Request parameter body 안의 값이 있을 경우, 발신 번호와 발신 내용이 템플릿 내용으로 치환되지 않습니다.**

단, 템플릿 ID를 입력했을 경우, 해당 템플릿으로 조회할 수 있습니다.

위와 같은 경우는 템플릿을 조회한 뒤, 템플릿의 내용을 수정하고 싶을 때 사용할 수 있습니다.

[Request body]

```
{
    "templateId": "TemplateId",
    "body":"본문",
    "sendNo":"15446859",
    "senderGroupingKey": "SenderGroupingKey",
    "recipientList": [{
        "recipientNo": "01000000000",
        "templateParameter": {
          "key1" : "Toast Cloud",
          "key2" : "SMS"
        },
        "recipientGroupingKey": "RecipientGroupingKey"
    }]
}
```

[Response]
```
{
    "header": {
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "body": {
        "data": {
            "requestId": "20180813095534I4VcVuPBpd0",
            "statusCode": "2",
            "senderGroupingKey": "SenderGroupingKey",
            "sendResultList": [
                {
                    "recipientNo": "01000000000",
                    "resultCode": 0,
                    "resultMessage": "SUCCESS",
                    "recipientSeq": 1,
                    "recipientGroupingKey": "RecipientGroupingKey"
                }
            ]
        }
    }
}
```


### 템플릿 리스트 조회

#### 요청

[URL]

```
GET  /sms/v2.2/appKeys/{appKey}/templates
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Query parameter]

|값|	타입|	필수|	설명|
|---|---|---|---|
|categoryId|	Integer|	옵션|	카테고리 ID|
|useYn|	String|	옵션|	사용 여부(Y/N)|

#### 응답

```
{
    "header": {
        "isSuccessful": Boolean,
        "resultCode": Integer,
        "resultMessage": String
    },
    "body": {
        "pageNum": Integer,
        "pageSize": Integer,
        "totalCount": Integer,
        "data": [{
            "templateId": "TemplateId",
            "serviceId": 0,
            "categoryId": 0,
            "categoryName": "카테고리명",
            "sort": 0,
            "templateName": "템플릿명",
            "templateDesc": "템플릿 설명",
            "useYn": "Y",
            "priority": "S",
            "sendNo": ""15446859String"",
            "sendType": "0",
            "sendTypeName": "SMS 발송",
            "title": "제목",
            "body": "본문",
            "attachFileYn": "N",
            "delYn": "N",
            "createDate": "2018-01-28 17:50:55.0,
            "createUser": "CreateUser",
            "updateDate": "2018-01-28 17:50:55.0",
            "updateUser": "UpdateUser",
            "attachFileList": [{
                "fileId": 0,
                "serviceId": 0,
                "attachType": 0,
                "templateId": "TemplateId",
                "filePath": "26606/toast-mt-2018-01-29/1427/105316",
                "fileName": "attachment.jpg",
                "fileSize": 0,
                "createDate": "2018-01-28 17:50:55.0",
                "createUser": "CreateUser"
            }]
        }]
    }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.pageNum|	Integer|	현재 페이지 번호|
|body.pageSize|	Integer|	조회된 데이터 수|
|body.totalCount|	Integer|	총 데이터 수|
|body.data[].templateId|	String|	템플릿 ID|
|body.data[].serviceId|	Integer|	서비스 ID(내부용, 미사용값)|
|body.data[].categoryId|	Integer|	카테고리 ID|
|body.data[].categoryName|	String|	카테고리명|
|body.data[].sort|	Integer|	정렬값|
|body.data[].templateName|	String|	템플릿명|
|body.data[].templateDesc|	String|	템플릿 설명|
|body.data[].useYn|	String|	사용 여부|
|body.data[].priority|	String|	우선순위값(미사용값)|
|body.data[].sendNo|	String|	발신 번호|
|body.data[].sendType|	String|	발송 유형(0:Sms, 1:Lms/Mms, 2:Auth)|
|body.data[].sendTypeName|	String|	발송 유형명|
|body.data[].title|	String|	제목|
|body.data[].body|	String|	본문 내용|
|body.data[].attachFileYn|	String|	첨부 파일 여부(Y/N)|
|body.data[].delYn |	String|	삭제여부(Y/N), 현재 상태 표기용으로만 사용|
|body.data[].createDate|	String|	등록 날짜|
|body.data[].createUser|	String|	등록한 사용자|
|body.data[].updateDate|	String|	수정 날짜|
|body.data[].updateUser|	String|	수정한 사용자|
|body.data[].attachFileList[].fileId|	Integer|	첨부 파일 ID|
|body.data[].attachFileList[].serviceId|	Integer|	서비스 ID(내부용, 미사용값)|
|body.data[].attachFileList[].attachType|	Integer|	첨부 파일 업로드 타입(0:임시,1:업로드,2:템플릿)|
|body.data[].attachFileList[].templateId|	String|	템플릿 ID|
|body.data[].attachFileList[].filePath|	String|	첨부 파일 경로|
|body.data[].attachFileList[].fileName|	String|	첨부 파일명|
|body.data[].attachFileList[].fileSize|  Integer| 파일 사이즈|
|body.data[].attachFileList[].createDate|	String|	첨부 파일 등록 날짜|
|body.data[].attachFileList[].createUser|	String|	첨부 파일 등록 사용자|


### 템플릿 단일 조회

#### 요청

[URL]

```
GET  /sms/v2.2/appKeys/{appKey}/templates/{templateId}
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|
|templateId|	String|	템플릿 ID|

#### 응답

```
{
    "header": {
        "isSuccessful": Boolean,
        "resultCode": Integer,
        "resultMessage": String
    },
    "body": {
        "pageNum": Integer,
        "pageSize": Integer,
        "totalCount": Integer,
        "data": {
            "templateId": "TemplateId",
            "serviceId": 0,
            "categoryId": 0,
            "categoryName": "카테고리명",
            "sort": 0,
            "templateName": "템플릿명",
            "templateDesc": "템플릿 설명",
            "useYn": "Y",
            "priority": "S",
            "sendNo": ""15446859String"",
            "sendType": "0",
            "sendTypeName": "SMS 발송",
            "title": "제목",
            "body": "본문",
            "attachFileYn": "N",
            "delYn": "N",
            "createDate": "2018-01-28 17:50:55.0,
            "createUser": "CreateUser",
            "updateDate": "2018-01-28 17:50:55.0",
            "updateUser": "UpdateUser",
            "attachFileList": [{
                "fileId": 0,
                "serviceId": 0,
                "attachType": 0,
                "templateId": "TemplateId",
                "filePath": "26606/toast-mt-2018-01-29/1427/105316",
                "fileName": "attachment.jpg",
                "fileSize": 0,
                "createDate": "2018-01-28 17:50:55.0",
                "createUser": "CreateUser"
            }]
        }
    }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.pageNum|	Integer|	현재 페이지 번호|
|body.pageSize|	Integer|	조회된 데이터 수|
|body.totalCount|	Integer|	총 데이터 수|
|body.data.templateId|	String|	템플릿 ID|
|body.data.serviceId|	Integer|	서비스 ID(내부용, 미사용값)|
|body.data.categoryId|	Integer|	카테고리 ID|
|body.data.categoryName|	String|	카테고리명|
|body.data.sort|	Integer|	정렬값|
|body.data.templateName|	String|	템플릿명|
|body.data.templateDesc|	String|	템플릿 설명|
|body.data.useYn|	String|	사용 여부|
|body.data.priority|	String|	우선순위값(미사용값)|
|body.data.sendNo|	String|	발신 번호|
|body.data.sendType|	String|	발송 유형(0:Sms, 1:Lms/Mms, 2:Auth)|
|body.data.sendTypeName|	String|	발송 유형명|
|body.data.title|	String|	제목|
|body.data.body|	String|	본문 내용|
|body.data.attachFileYn|	String|	첨부 파일 여부(Y/N)|
|body.data.delYn |	String|	삭제여부(Y/N), 현재 상태 표기용으로만 사용|
|body.data.createDate|	String|	등록 날짜|
|body.data.createUser|	String|	등록한 사용자|
|body.data.updateDate|	String|	수정 날짜|
|body.data.updateUser|	String|	수정한 사용자|
|body.data.attachFileList[].fileId|	Integer|	첨부 파일 ID|
|body.data.attachFileList[].serviceId|	Integer|	서비스 ID(내부용, 미사용값)|
|body.data.attachFileList[].attachType|	Integer|	첨부 파일 업로드 타입(0:임시,1:업로드,2:템플릿)|
|body.data.attachFileList[].templateId|	String|	템플릿 ID|
|body.data.attachFileList[].filePath|	String|	첨부 파일 경로|
|body.data.attachFileList[].fileName|	String|	첨부 파일명|
|body.data.attachFileList[].fileSize|  Integer| 파일 사이즈|
|body.data.attachFileList[].createDate|	String|	첨부 파일 등록 날짜|
|body.data.attachFileList[].createUser|	String|	첨부 파일 등록 사용자|


### 템플릿 수정

#### 요청

[URL]

```
PUT  /sms/v2.2/appKeys/{appKey}/templates/{templateId}
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Request body]

```
{
   "templateName" : "",
   "templateDesc" : "",
   "sendNo" : "",
   "sendType" : "",
   "title" : "",
   "body" : "",
   "useYn" : "",
   "attachFileIdList" : [
      0,
      1
   ]
}
```

|값|	타입|	최대 길이 | 필수|	설명|
|---|---|---|---|---|
| templateName |	String| 50 |	필수 |	템플릿명|
| templateDesc |	String| 100 |	옵션 |	템플릿 설명|
| sendNo | String | 13 | 필수 | 발신 번호 |
| sendType | String| 1| 필수| 발송 유형(0:Sms, 1:Lms/Mms) |
| title | String | 120 | 옵션 | 문자 제목(발송 유형이 Lms/MmS인 경우 필수) |
| body | String | 4000 | 필수 | 문자 내용 |
| useYn |	String| 1 |	필수|	사용 여부(Y/N)|
| attachFileIdList | List<Integer> | - | X | 첨부 파일 ID(fileId) |


#### 응답

```
{
   "header" : {
      "isSuccessful" : true,
      "resultCode" : "",
      "resultMessage" : ""
   }
}
```

### 템플릿 삭제

#### 요청

[URL]

```
DELETE  /sms/v2.2/appKeys/{appKey}/templates/{templateId}
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|
|templateId|	String|	템플릿 ID|

#### 응답

```
{
   "header" : {
      "isSuccessful" : true,
      "resultCode" : "",
      "resultMessage" : ""
   }
}
```


## 080 수신 거부 서비스

### 수신 거부 대상자 조회

#### 요청

[URL]

```
GET  /sms/v2.2/appKeys/{appKey}/blockservice/recipients
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Query parameter]

|값|	타입|	최대 길이 | 필수|	설명|
|---|---|---|---|---|
|unsubscribeNo|	String| 25 |	필수 |	080 수신 거부 번호 |
|startRequestDate|	String| - |	옵션 |	수신 거부 요청 시작값(yyyy-MM-dd HH:mm:ss)|
|endRequestDate|	String| - |	옵션 |	수신 거부 요청 종료값(yyyy-MM-dd HH:mm:ss)|
|pageNum|	Integer| - |	옵션|	페이지 번호(기본값 : 1)|
|pageSize|	Integer| 1000 |	옵션|	조회 수(기본값 : 15)|

#### 응답
```
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "body": {
        "pageNum": 1,
        "pageSize": 15,
        "totalCount": 1,
        "data": [
            {
              "unsubscribeNo": "0808888888",
              "recipientNo": "01000000000",
              "requestDate": "2018-05-14 17:07:29.0"
            }
        ]
    }
}
```

### 수신 거부 대상자 삭제

#### 요청

[URL]

```
DELETE  /sms/v2.2/appKeys/{appKey}/blockservice/recipients/removes?unsubscribeNo={unsubscribeNo}&updateUser={updateUser}&recipientNoList={recipientNo},{recipientNo}
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Query parameter]

|값|	타입|	최대 길이 | 필수|	설명|
|---|---|---|---|---|
|unsubscribeNo|	String| 20 |	필수 |	080 수신 거부 번호 |
|updateUser|	String|	100 | 필수 |	수신 거부 삭제자|
|recipientNo|	String|	20 | 필수 |	삭제할 수신 거부 번호|

#### 응답
```
{
    "header": {
        "isSuccessful": boolean,
        "resultCode": Integer,
        "resultMessage": String
    },
    "body": null
}
```

## 발신 번호

### 발신 번호 등록 요청

#### 요청

[URL]

```
POST /sms/v2.2/appKeys/{appKey}/reqeusts/sendNos|
Content-Type: application/json;charset=UTF-8
```


[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Request Body]
```
{
  "sendNos" : [
    String,
    String
  ],
  "fileIds" : [
    Integer
  ],
  "comment" :  String
}

```

|값|	타입|	최대 길이 | 필수|	설명|
|---|---|---|---|---|
| sendNos[] |	List<String> | - | 필수 |	발신 번호|
| fileIds[] |	List<Integer> | - | 옵션 | 업로드한 서류의 파일 아이디|
| comment | String | 4000 | 옵션 | 발신번호 승인자에게 남길 말  |

#### 응답
```
{
  "header" : {
    "isSuccessful" :  Boolean,
    "resultCode" :  Integer,
    "resultMessage" :  String
  }
}
```

### 발신 번호 서류 업로드

#### 요청

[URL]
```
POST /sms/v2.2/appKeys/{appKey}/requests/attachFiles/authDocuments
Content-Type : multipart/form-data;
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Request Body]

|값|타입|설명|
|---|---|---|
| attachFile | MultiPartFile | MultiPartFile로 받을 수 있는 파일 데이터 |


#### 응답
```
{
  "header" : {
    "isSuccessful" :  Boolean,
    "resultCode" :  Integer,
    "resultMessage" :  String
    },
    "file" : {
      "fileId" :  Integer,
      "fileName" : String,
      "filePath" : String
    }
  }
```

### 발신 번호 인증 요청 내역 조회 API

#### 요청

[URL]

|Http method|	URI|
|---|---|
|GET|	/sms/v2.2/appKeys/{appKey}/requests/sendNos?sendNo={sendNo}&status={status}&pageNum={pageNum}&pageSize={pageSize}|

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Query parameter]

|값|	타입|	설명|
|---|---|---|
| sendNo|String | 등록 요청한 발신 번호 |
|status|	String|	서류 인증 상태<br/>- SRS01	발신 번호 등록 요청<br/>- SRS02	심사중<br/>- SRS03	등록 완료<br/>- SRS04	등록 불가<br/>- SRS05	휴대폰 인증 대기<br/>- SRS06	휴대폰 인증 실패<br/>- SRS07	수동 등록 완료|
|pageNum|	Integer| 페이지 번호(기본값 : 1)|
|pageSize|	Integer| 조회 수(기본값 : 15)|

#### 응답
```
{
  "header":{
    "isSuccessful":true,
    "resultCode":0,
    "resultMessage":"SUCCESS"
  },
  "body":{
    "pageNum":1,
    "pageSize":15,
    "totalCount":1,
    "data":[
      {
        "authType":"{SMS_AUTH|DOCUMENT_AUTH|MANUAL_REGIST_AUTH}",
        "sendNos":[
          "01000000000",
          "01011111111"
        ],
        "comment":"{comment}",
        "fileIds":null,
        "status":"{REGIST_REQUEST|EXAMINE|COMPLETE|REJECT|CERTIFYING|CERTIFY_FAILED|MANUAL_REGIST}",
        "createDate":"2018-08-14 16:34:54",
        "updateDate":"2018-08-14 16:34:54",
        "confirmDate":"2018-08-14 16:34:54"
      }
    ]
  }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.pageNum|	Integer|	현재 페이지 번호|
|body.pageSize|	Integer|	조회된 데이터 수|
|body.totalCount|	Integer|	총 데이터 수|
|body.data[].authType|	String|	요청 인증 타입<br/>- SMS_AUTH:SMS인증<br/>- DOCUMENT_AUTH:서류인증<br/>- REGIST_AUTH:수동인증|
|body.data[].sendNos[]|	List<String>|	등록요청 발신 번호 리스트|
|body.data[].comment|	String|	커멘트 항목|
|body.data[].fileIds[]|	List<Integer>|	서류 인증 시 업로드한 파일 ID(내부용)|
|body.data[].status|	String|	요청상태<br/>- REGIST_REQUEST(SRS01)<br/>- EXAMINE(SRS02)<br/>- COMPLETE(SRS03)<br/>- REJECT(SRS04)<br/>- CERTIFYING(SRS05)<br/>- CERTIFY_FAILED(SRS06)<br/>- MANUAL_REGIST(SRS07)|
|body.data[].createDate|	String| 등록 일시	|
|body.data[].updateDate|	String| 수정 날짜	|
|body.data[].confirmDate|	String| 승인/거절 일시	|



### 등록된 발신 번호 리스트 조회 API

#### 요청

[URL]

|Http method|	URI|
|---|---|
|GET|	/sms/v2.2/appKeys/{appKey}/sendNos?sendNo={sendNo}&useYn={useYn}&blockYn={blockYn}&pageNum={pageNum}&pageSize={pageSize}

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Query parameter]

|값|	타입|	설명|
|---|---|---|
| sendNo | String | 발신 번호 |
| useYn | String | 사용 여부 |
| blockYn | String | 차단 여부 |
|pageNum|	Integer| 페이지 번호(기본값 : 1)|
|pageSize|	Integer| 조회 수(기본값 : 15)|

#### 응답
```
{
    "header" : {
        "isSuccessful" :  true,
        "resultCode" :  0,
        "resultMessage" :  ""
    },
    "body" : {
        "pageNum" :  0,
        "pageSize" :  0,
        "totalCount" :  0,
        "data" : [
        {
            "serviceId" :  0,
            "sendNo" :  "",
            "useYn" :  "",
            "blockYn" :  "",
            "blockReason" :  "",
            "createDate" :  "",
            "createUser" :  "",
            "updateDate" :  "",
            "updateUser" :  ""
        }
        ]
    }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.pageNum|	Integer|	페이지 번호|
|body.pageSize|	Integer|	조회된 데이터 수|
|body.totalCount|	Integer|	총 데이터 수|
|body.data[].serviceId | Integer | 서비스 ID |
|body.data[].sendNo | String | 발신 번호 |
|body.data[].useYn | String | 사용 여부 |
|body.data[].blockYn | String | 차단여부 |
|body.data[].blockReason | String | 차단 사유 |
|body.data[].createDate | String | 생성 일시 |
|body.data[].createUser | String | 생성자 |
|body.data[].updateDate | String | 수정 날짜 |
|body.data[].updateUser | String | 수정한 사용자 |

## 통계 조회

### 통합 통계 조회

#### 요청

[URL]

|Http method|	URI|
|---|---|
|GET|	/sms/v2.2/appKeys/{appKey}}/statistics/view?searchType={searchType}&from={from}&to={to}&messageTypes={messageType}&contentTypes={contentType}&templateId={templateId}|

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Query parameter]

|값|	타입|	최대 길이 | 필수 |설명|
|---|---|---|---|---|
| searchType | String | 10 | O | 통계 구분<br/>DATE:날짜별, TIME:시간별, DAY:요일별 |
| from | String |  - |O | 통계 조회 시작 날짜<br/>yyyy-MM-dd HH:mm |
| to | String | - | O | 통계 조회 종료 날짜<br/>yyyy-MM-dd HH:mm |
| messageType | String | 10 |  X |  메시지 타입<br/>SMS:단문, LMS:장문, MMS:첨부 파일, AUTH:인증용 |
| contentType | String | 10 |  X |  콘텐츠 타입<br/>NORMAL: 일반, AD: 광고 |
| templateId | String | 50 |  X |  템플릿 ID |

#### 응답
```
{
    "header" : {
        "isSuccessful" : true,
        "resultCode" : 0,
        "resultMessage" : "SUCCESS""
    },
    "body" : {
        "data" :
        [
          {
            "divisionName" : "2018-06-01",
            "statisticsView" :
            {
              "requestedCount" : 10,
              "succeedCount" : 10,
              "failedCount" : 0,
              "pendingCount" : 0,
              "succeedRate" : "100.00",
              "failedRate" : "0.00",
              "pendingRate" : "0.00"
            }
          }
        ]
    }
}
```
|값| 타입|설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.data[].divisionName | String | 표시이름<br/>날짜, 시간, 요일 |
|body.data[].statisticsView | Object | |
|body.data[].requestedCount | Integer | 요청 개수 |
|body.data[].succeedCount | Integer | 성공 개수 |
|body.data[].failedCount | Integer | 실패 개수 |
|body.data[].pendingCount | Integer | 발송 중 개수 |
|body.data[].succeedRate | String | 성공 비율 |
|body.data[].failedRate | String | 실패 비율 |
|body.data[].pendingRate | String | 발송 중 비율 |

## 예약 발송

### 예약 발송 목록 조회

#### 요청

[URL]

```
GET /sms/v2.2/appKeys/{appKey}/reservations
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Query parameter]

|값|	타입|	최대 길이 | 필수|	설명|
|---|---|---|---|---|
|sendType| String| 1 |  옵션 | 발송 유형<br/>(0:SMS, 1:LMS/MMS, 2:AUTH) |
|requestId|	String| 25 |	옵션 |	요청 ID|
|startRequestDate|	String| - |	옵션 |	발송 날짜 시작값(yyyy-MM-dd HH:mm:ss)|
|endRequestDate|	String| - |	옵션 |	발송 날짜 종료값(yyyy-MM-dd HH:mm:ss)|
|startCreateDate|	String| - |	옵션 |	등록 날짜 시작값(yyyy-MM-dd HH:mm:ss)|
|endCreateDate|	String| - |	옵션 |	등록 날짜 종료값(yyyy-MM-dd HH:mm:ss)|
|sendNo|	String| 13 | 옵션|	발신 번호|
|recipientNo|	String| 20 |	옵션|	수신 번호|
|templateId|	String| 50 |	옵션|	템플릿 번호|
|messageStatus|	String| 10 |	옵션|	메시지 상태<br/>(RESERVED:예약 대기, SENDING:발송 중, COMPLETED:발송 완료, FAILED:발송 실패,CANCEL:예약 취소,DUPLICATED:중복 발송)|
|pageNum|	Integer| - |	옵션|	페이지 번호(기본값 : 1)|
|pageSize|	Integer| 1000 |	옵션|	조회 수(기본값 : 15)|

#### 응답

```
{
  "header":{
    "resultCode":0,
    "resultMessage":"success",
    "isSuccessful":true
  },
  "body":{
    "pageNum":1,
    "pageSize":15,
    "totalCount":15,
    "data":[
      {
        "requestId":"{요청 ID}",
        "recipientSeq":1,
        "requestDate":"{예약 날짜}",
        "sendNo":"{발신 번호}",
        "recipientNo":"{수신 번호}",
        "countryCode":"{국가코드}",
        "sendType":"{발송 유형}",
        "messageType":"{메시지타입}",
        "adYn":"{광고 여부}",
        "templateId":"{템플릿 ID}",
        "templateParameter":"{템플릿 파라미터}",
        "templateName":"{템플릿 명}",
        "title":"{제목}",
        "body":"{내용}",
        "messageStatus":"{메시지 상태}",
        "createUser":"{등록한 사용자}",
        "createDate":"{등록 일시}",
        "updateDate":"{수정 날짜}"
      }
    ]
  }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.pageNum|	Integer|	현재 페이지 번호|
|body.pageSize|	Integer|	조회된 데이터 수|
|body.totalCount|	Integer|	총 데이터 수|
|body.data[].requestId|	String|	요청 ID|
|body.data[].recipientSeq|	Integer|	수신자 시퀀스|
|body.data[].requestDate|	String|	발신 일시|
|body.data[].sendNo|	String|	발신 번호|
|body.data[].recipientNo|	String|	수신 번호|
|body.data[].countryCode|	String|	국가 번호|
|body.data[].sendType|	String|	발송 유형(0:Sms, 1:Lms/Mms, 2:Auth)|
|body.data[].messageType|	String|	메시지타입<br/>(SMS,LMS,MMS,AUTH)|
|body.data[].adYn|	String|	광고 여부|
|body.data[].templateId|	String|	템플릿 ID|
|body.data[].templateParameter|	String(json)|	템플릿 파라미터|
|body.data[].templateName|	String|	템플릿명|
|body.data[].title|	String|	제목|
|body.data[].body|	String|	본문 내용|
|body.data[].messageStatus|	String|	메시지 상태<br/>(RESERVED:예약 대기,SENDING:발송 중,COMPLETED:발송 완료,FAILED:발송 실패,CANCEL:예약 취소,DUPLICATED:중복 발송)|
|body.data[].createUser|	String|	등록한 사용자|
|body.data[].createDate|	String|	등록 날짜|
|body.data[].updateDate|	String|	수정 날짜|

### 예약 발송 상세 조회

#### 요청

[URL]

```
GET /sms/v2.2/appKeys/{appKey}/reservations/{requestId}/{recipientSeq}
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|
|requestId|	String|	요청 ID|
|recipientSeq|	Integer|	수신자 시퀀스|

#### 응답

```
{
  "header":{
    "resultCode":0,
    "resultMessage":"success",
    "isSuccessful":true
  },
  "body":{
    "data":{
      "requestId":"{요청 ID}",
      "recipientSeq":1,
      "requestDate":"{예약 날짜}",
      "sendNo":"{발신 번호}",
      "recipientNo":"{수신 번호}",
      "countryCode":"{국가코드}",
      "sendType":"{발송 유형}",
      "messageType":"{메시지타입}",
      "adYn":"{광고 여부}",
      "templateId":"{템플릿 ID}",
      "templateParameter":"{템플릿 파라미터}",
      "templateName":"{템플릿 명}",
      "title":"{제목}",
      "body":"{내용}",
      "messageStatus":"{메시지 상태}",
      "createUser":"{등록한 사용자}",
      "createDate":"{등록 일시}",
      "updateDate":"{수정 날짜}",
      "attachFileList":[
        {
          "fileId":0,
          "filePath":"26606/toast-mt-2018-02-07/1555/105887/",
          "fileName":"file_attach_test.jpg"
        }
      ]
    }
  }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.pageNum|	Integer|	현재 페이지 번호|
|body.pageSize|	Integer|	조회된 데이터 수|
|body.totalCount|	Integer|	총 데이터 수|
|body.data.requestId|	String|	요청 ID|
|body.data.recipientSeq|	Integer|	수신자 시퀀스|
|body.data.requestDate|	String|	발신 일시|
|body.data.sendNo|	String|	발신 번호|
|body.data.recipientNo|	String|	수신 번호|
|body.data.countryCode|	String|	국가 번호|
|body.data.sendType|	String|	발송 유형(0:Sms, 1:Lms/Mms, 2:Auth)|
|body.data.messageType|	String|	메시지타입<br/>(SMS,LMS,MMS,AUTH)|
|body.data.adYn|	String|	광고 여부|
|body.data.templateId|	String|	템플릿 ID|
|body.data.templateParameter|	String(json)|	템플릿 파라미터|
|body.data.templateName|	String|	템플릿명|
|body.data.title|	String|	제목|
|body.data.body|	String|	본문 내용|
|body.data.messageStatus|	String|	메시지 상태<br/>(RESERVED:예약 대기,SENDING:발송 중,COMPLETED:발송 완료,FAILED:발송 실패,CANCEL:예약 취소,DUPLICATED:중복 발송)|
|body.data.createUser|	String|	등록한 사용자|
|body.data.createDate|	String|	등록 날짜|
|body.data.attachFileList[].fileId|	Integer|	파일 ID|
|body.data.attachFileList[].filePath|	String|	파일경로(내부용)|
|body.data.attachFileList[].fileName|	String|	파일명|


### 예약 발송 취소

#### 요청

[URL]

```
PUT /sms/v2.2/appKeys/{appKey}/reservations/cancel
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Request body]

```
{
  "reservationList":[
    {
      "requestId":"{requestId}",
      "recipientSeq":1
    }
  ],
  "updateUser":"{updateUser}"
}
```

|값|	타입|	최대 길이 | 필수|	설명|
|---|---|---|---|---|
|reservationList[].requestId| String| 25 | O | 요청 ID|
|reservationList[].recipientSeq| Integer| - | O | 수신자 시퀀스|
|updateUser| String| 100 | O | 취소 요청자|

#### 응답

```
{
  "header":{
    "resultCode":0,
    "resultMessage":"success",
    "isSuccessful":true
  },
  "body":{
    "data":{
      "requestedCount":1,
      "canceledCount":1
    }
  }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.data.requestedCount|	Integer|	취소 요청 건수|
|body.data.canceledCount|	Integer|	취소 성공 건수|

## 발송 결과 파일 다운로드

### 조회 파일 생성 요청

#### 요청

[URL]

```
POST /sms/v2.2/appKeys/{appKey}/sender/download-reservations
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Request body]
* requestId 또는 startRequestDate + endRequestDate 또는 startCreateDate + endCreateDate는 필수입니다.
* 등록 날짜/발송 날짜를 동시에 조회하는 경우, 발송 날짜는 무시됩니다.

```
{
  "sendType":"1",
  "requestId":"20190601100630ReZQ6KZzAH0",
  "startRequestDate":"2019-06-01 00:00:00",
  "endRequestDate":"2019-06-08 00:00:00",
  "startCreateDate":"2019-06-01 00:00:00",
  "endCreateDate":"2019-06-08 00:00:00",
  "startResultDate":"2019-06-01 00:00:00",
  "endResultDate":"2019-06-08 00:00:00",
  "sendNo":"15446859",
  "recipientNo":"01000000000",
  "templateId":"TemplateId",
  "msgStatus":"3",
  "resultCode":"MTR2",
  "subResultCode":"MTR2_3",
  "senderGroupingKey":"{발송자 그룹 키}",
  "recipientGroupingKey":"{수신자 그룹 키}",
  "isIncludeTitleAndBody":true
}
```

|값|	타입|	최대 길이 | 필수|	설명|
|---|---|---|---|---|
|sendType| String| 1| 필수| 발송 유형(0:Sms, 1:Lms/Mms, 2:Auth) |
|requestId|	String| 25 |	필수 |	요청 ID|
|startRequestDate|	String| - |	필수 |	발송 날짜 시작값(yyyy-MM-dd HH:mm:ss)|
|endRequestDate|	String| - |	필수 |	발송 날짜 종룟값(yyyy-MM-dd HH:mm:ss)|
|startCreateDate|	String| - |	필수 |	등록 날짜 시작값(yyyy-MM-dd HH:mm:ss)|
|endCreateDate|	String| - |	필수 |	등록 날짜 종룟값(yyyy-MM-dd HH:mm:ss)|
|startResultDate|	String| - |	옵션|	수신 날짜 시작값(yyyy-MM-dd HH:mm:ss)|
|endResultDate|	String| - |	옵션|	수신 날짜 종룟값(yyyy-MM-dd HH:mm:ss)|
|sendNo|	String| 13 |	옵션|	발신 번호|
|recipientNo|	String| 20 |	옵션|	수신 번호|
|templateId|	String| 50 |	옵션|	템플릿 번호|
|msgStatus|	String| 1 |	옵션| 메시지 상태 코드(0: 실패, 1: 요청, 2: 처리 중, 3:성공, 4:예약취소, 5:중복실패) |
|resultCode|	String| 10 |	옵션|	수신 결과 코드 [[조회 코드표](./error-code/#_2)]|
|subResultCode|	String| 10 |	옵션|	수신 결과 상세 코드 [[조회 코드표](./error-code/#_3)]|
|senderGroupingKey|	String| 100 |	옵션|	발송자 그룹 키|
|recipientGroupingKey|	String| 100 |	옵션|	수신자 그룹 키|
|isIncludeTitleAndBody | Boolean | - | 옵션 | 제목, 본문 포함 여부 |

#### 응답

```
{
  "header":{
    "isSuccessful":true,
    "resultCode":0,
    "resultMessage":"SUCCESS"
  },
  "body":{
    "data":{
      "donwloadId":"20190610100630ReZQ6KZzAH0",
      "downloadType":"NORMAL",
      "fileType":"CSV",
      "downloadStatusCode":"COMPLETED",
      "expiredDate":"2019-07-09 10:06:00.0"
    }
  }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.data.donwloadId|	String|	다운로드 ID|
|body.data.downloadType|	String|	다운로드 유형<br/>- BLOCK: 수신거부<br/>- NORMAL: 일반 발송<br/>- MASS: 대량 발송<br/>- TAG: 태그 발송|
|body.data.fileType|	String|	파일 타입(현재 csv만 지원)|
|body.data.downloadStatusCode|	String|	파일 생성 상태<br/>- READY: 생성 준비<br/>- MAKING: 생성 중<br/>- COMPLETED: 생성 완료<br/>- FAILED: 생성 실패<br/>- EXPIRED: 다운로드 기간 만료|
|body.data.expiredDate|	String|	다운로드 기간 만료 일시|\


### 발송 결과 파일 생성 요청 내역 조회

#### 요청

[URL]

```
GET /sms/v2.2/appKeys/{appKey}/download-reservations
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|

[Query parameter]

|값|	타입| 최대 길이 |	필수|	설명|
|---|---|---|---|---|
|downloadId|	String|	25 | 옵션 | 다운로드 아이디|
|downloadStatusCode|	String| 10 | 옵션 |	다운로드 파일 상태 코드|
|pageNum|	Integer|	- | 옵션 | 페이지 번호(기본값: 1)|
|pageSize|	Integer|	1000 | 옵션 | 조회 수(기본값: 15)|

#### 응답

```json
{
  "header":{
    "isSuccessful":true,
    "resultCode":0,
    "resultMessage":"SUCCESS"
  },
  "body":{
    "totalCount":0,
    "data":[
      {
        "downloadId":"",
        "downloadType":"",
        "fileType":"",
        "parameter":"",
        "size":0,
        "downloadStatusCode":"",
        "resultMessage":"",
        "expiredDate":"",
        "createUser":"",
        "createDate":"",
        "updateDate":""
      }
    ]
  }
}
```

|값|	타입|	설명|
|---|---|---|
|header.isSuccessful|	Boolean|	성공 여부|
|header.resultCode|	Integer|	실패 코드|
|header.resultMessage|	String|	실패 메시지|
|body.totalCount| Integer | 전체 건수|
|body.data[].downloadId| String | 다운로드 ID |
|body.data[].downloadType| String | 다운로드 유형<br/>- BLOCK: 수신 거부<br/>- NORMAL: 일반 발송<br/>- MASS: 대량 발송<br/>- TAG: 태그 발송 |
|body.data[].fileType| String | 파일 타입 |
|body.data[].parameter| String | 요청 파라미터 |
|body.data[].size| Integer | 조회 데이터 크기 |
|body.data[].downloadStatusCode| String | 파일 생성 상태<br/>- READY: 생성 준비<br/>- MAKING: 생성 중<br/>- COMPLETED: 생성 완료<br/>- FAILED: 생성 실패<br/>- EXPIRED: 다운로드 기간 만료 |
|body.data[].resultMessage| String | 결과 메시지(실패 시 응답) |
|body.data[].expiredDate| String | 파일 만료 일시 |
|body.data[].createUser| String | 파일 생성 요청자 |
|body.data[].createDate| String | 파일 생성 요청 일시 |
|body.data[].updateDate| String | 파일 생성 완료, 실패 일시 |


### 발송 결과 파일 다운로드 요청

#### 요청

[URL]

```
GET /sms/v2.2/appKeys/{appKey}/download-reservations/{downloadId}/download
Content-Type: application/json;charset=UTF-8
```

[Path parameter]

|값|	타입|	설명|
|---|---|---|
|appKey|	String|	고유의 앱키|
|downloadId| String | 다운로드 ID|

#### 응답

```
file byte
```