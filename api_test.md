## Repository

[https://github.com/lewis810k/gori](https://github.com/lewis810k/gori)

## API Base

`https://address.com/api`

## API 목록
- Obtain Token
- Member
	- [Signup](#signup)
	- [Login](#login)
	- [Facebook Login](#facebook-login)
	- [Logout](#logout)
	- [UserDetail](#userdetail)
- Talent
	- [Talent List](#talent-list)
	- [Talent Detail Retrieve All](#talent-detail-retrieve-all) 
	- [Talent Detail Retrieve Fragments](#talent-detail-retrieve-fragments)
  		- [Talent Detail Short Retreive](#talent-detail-short-retrieve)
  		- [Talent Location Retreive](#talent-location-retrieve)
  		- [Talent Curriculum Retreive](#talent-curriculum-retrieve)
  		- [Talent Class Image Retreive](#talent-class-image-retrieve)

> **Talent Detail Retrieve All**  
> - 하나의 수업 정보에 대한 모든 세부정보 retrieve
  
> **Talent Retrieve Detail Fragments**  
> - 하나의 수업 정보의 세부정보를 여러 api로 세분화하여 retrieve

## Obtain Token

### URL

`/api/member/token-auth/`

### Method

`POST`

### URL Params

None

### Data Params

#### 1. 일반유저

key|Description|Type
---|---|---
**username**|회원가입하는 사용자 이메일|String
**password**|패스워드|String

#### 2. 페이스북 유저
 
key|Value
---|---
**access_token**|Token Key Value

### Success Response
- Code: 201
- Content

Token Key Value

```python
{
  "key": "3a9fcdcf85afbf783ad5ffed3a3966dc07314acd"
}
```

### Error Response
- Code: 400
	- Reason
		- 필수항목 누락
		- 정보 불일치
	- Content

```python
{
  "username": [
    "이 항목을 채워주십시오."
  ]
}
```

```python
{
  "non_field_errors": [
    "제공된 인증데이터(credentials)로는 로그인할 수 없습니다."
  ]
}
```

---

## Signup

### URL

`/api/member/signup/`

### Method

`POST`

### URL Params

None

### Data Params

key|Description|Type
---|---|---
**username**|회원가입하는 사용자 이메일|String
**password1**|패스워드|String
**password2**|패스워드 확인용|String
**name**|사용자 이름|String

### Success Response
- Code: 201
- Content

Token Key Value

```python
{
  "key": "3a9fcdcf85afbf783ad5ffed3a3966dc07314acd"
}
```

### Error Response
- Code: 400
	- Reason
		- 필수항목 누락
		- username 중복
		- password 불일치
	- Content
	 
```python
{
  "username": [
    "해당 사용자 이름은 이미 존재합니다."
  ]
}
```

```python
{
  "non_field_errors": [
    "비밀번호가 일치하지 않습니다."
  ]
}
```

```python
{
  "password2": [
    "이 항목을 채워주십시오."
  ]
}
```

name 필드에 대한 에러메시지 커스터마이징 필요.

---

## Login

### URL

`/api/member/login/`

### Method

`GET`

### URL Params

key|Description|Type
---|---|---
**username**|사용자 이메일|String
**password**|패스워드|String

### Data Params

None

### Success Response
- Code: 200
- Content

Token Key Value

```python
{
  "key": "36ddf1824a5c7aaca5977bbe65962566a17fb86f"
}
```

### Error Response
- Code: 400
	- Reason: 인증 실패
	- Content

```python
{
  "non_field_errors": [
    "제공된 인증데이터(credentials)로는 로그인할 수 없습니다."
  ]
}
```

---

## Logout

> Authenticate Required

### URL

`/api/member/logout/`

### Method

`POST`

### Header
Key|Value
---|---
Authorization|Token [Token Key Value]

### Url Params

None

### Data Params

None

### Success Response
- Code: 200
- Content

```python
{
  "detail": "Successfully logged out."
}
```

### Error Response
rest-auth에서 자동적으로 Error에 대한 처리를 하지 않기 때문에 커스터마이징 필요

---

## Facebook Login

### URL

`/api/member/fb_login/`

### Method

`POST`

### URL Params

None

### Data Params

Key|Value
---|---
access_token|Token Key Value

### Success Response

- Code: 200
- Content

Token Key Value

```python
{
  "key": "b8a2ce3996515dfe26982cdbfdb90f59a0c2d653"
}
```

### Error Response
- Code: 400
	- Reason
		- 필수항목 누락
		- 잘못된 토큰 값
	- Content

```python
{
  "non_field_errors": [
    "Incorrect input. access_token is required."
  ]
}
```

```python
{
  "non_field_errors": [
    "Incorrect value"
  ]
}
```

## Talent List
### URL

`/talent/list/`

### Method

`GET`

### URL Params

None

### Data Params

None

### Success Response

- Code: 200
- Content

```json
{
  "next": "http://localhost:8000/api/talent/list/?cursor=cD0yMDE3LTAzLTMxKzA4JTNBNTglM0EwMS43NzA5NzYlMkIwMCUzQTAw",
  "previous": null,
  "results": [
    {
      "pk": 1,
      "tutor": {
        "pk": 2,
        "user_id": "admin",
        "name": "",
        "nickname": "",
        "is_verified": false,
        "profile_image": null,
        "cellphone": ""
      },
      "title": "파이썬 장고",
      "category_name": "컴퓨터",
      "type_name": "그룹 수업",
      "cover_image": "https://projectgori.s3.amazonaws.com/media/talent/cover_image/article.jpg",
      "price_per_hour": 1,
      "hours_per_class": 1,
      "number_of_class": 1,
      "is_soldout": false,
      "created_date": "2017-03-31T08:51:55.257701Z",
      "review_count": 0,
      "locations": [
        "강남",
        "종로"
      ]
    },
    {
      "pk": 2,
      "tutor": {
        "pk": 2,
        "user_id": "admin",
        "name": "",
        "nickname": "",
        "is_verified": false,
        "profile_image": null,
        "cellphone": ""
      },
      "title": "영화보자",
      "category_name": "미술 / 음악",
      "type_name": "원데이 수업",
      "cover_image": "https://projectgori.s3.amazonaws.com/media/talent/cover_image/beuxnVcF.jpg",
      "price_per_hour": 1,
      "hours_per_class": 1,
      "number_of_class": 1,
      "is_soldout": false,
      "created_date": "2017-03-31T08:58:01.770976Z",
      "review_count": 0,
      "locations": [
        "사당"
      ]
    }
  ]
}
```

## Talent Detail Retrieve All

### URL

`/talent/detail-all/<talent_pk>/`

### Method

`GET`

### Header

None

### URL Params

None

### Data Params

None

### Success Response

- Code: 200
- Content

```Json

  "tutor": {
    "pk": 89,
    "user_id": "test_api@gmail.com",
    "name": "lewis",
    "nickname": "",
    "is_verified": true,
    "profile_image": null,
    "cellphone": ""
  },
  "title": "바리스타 마스터 코스",
  "category": "HOB",
  "category_name": "이색취미",
  "type": 1,
  "cover_image": "https://projectgori.s3.amazonaws.com/media/talent/cover_image/article_rEC7reP.jpg",
  "tutor_info": "바빈스키",
  "class_info": "바빈스키가 직접 알려줌",
  "video1": "https://www.youtube.com/watch?v=t8YXut6_56c",
  "video2": "",
  "location": [
    {
      "region": "신촌",
      "specific_location": "협의 후 결정",
      "day": "월",
      "time": "12-14",
      "extra_fee": "N",
      "extra_fee_amount": ""
    },
    {
      "region": "사당",
      "specific_location": "협의 후 결정",
      "day": "화",
      "time": "14-16",
      "extra_fee": "N",
      "extra_fee_amount": ""
    }
  ],
  "price_per_hour": 10,
  "hours_per_class": 2,
  "number_of_class": 8,
  "is_soldout": false,
  "class_image": [
    {
      "image": "https://projectgori.s3.amazonaws.com/media/talent/extra_images/moon4.jpg"
    }
  ],
  "curriculum": [
    {
      "information": "원두 로스팅",
      "image": "https://projectgori.s3.amazonaws.com/media/talent/curriculum/moon3.jpg"
    },
    {
      "information": "내리기",
      "image": "https://projectgori.s3.amazonaws.com/media/talent/curriculum/moon1.jpg"
    }
  ]
}
```

### Error Response

- Code: 404
  - Reason: Invalid Talent pk
  - Content

```Json
{
  "detail": "찾을 수 없습니다."
}
```

## Talent Detail Retrieve Fragments

## Talent Detail Short Retrieve

### URL

`/talent/detail/<talent_pk>/`

### Method

`GET`

### Header

None

### URL Params

None

### Data Params

None

### Success Response

- Code: 200
- Content

```Json
{
  "tutor": {
    "pk": 89,
    "user_id": "test_api@gmail.com",
    "name": "lewis",
    "nickname": "",
    "is_verified": true,
    "profile_image": null,
    "cellphone": ""
  },
  "title": "바리스타 마스터 코스",
  "category_name": "이색취미",
  "type": "그룹 수업",
  "cover_image": "https://projectgori.s3.amazonaws.com/media/talent/cover_image/article_rEC7reP.jpg",
  "tutor_info": "바빈스키",
  "class_info": "바빈스키가 직접 알려줌",
  "video1": "https://www.youtube.com/watch?v=t8YXut6_56c",
  "video2": "",
  "price_per_hour": 10,
  "hours_per_class": 2,
  "number_of_class": 8,
  "is_soldout": false
}
```

### Error Response

- Code: 404
  - Reason: Invalid Talent pk
  - Content

```Json
{
  "detail": "찾을 수 없습니다."
}
```

## Talent Location Retrieve

### URL

`/talent/detail/<talent_pk>/location/`

### Method

`GET`

### Header

None

### URL Params

None

### Data Params

None

### Success Response

- Code: 200
- Content

```Json
{
  "id": 27,
  "title": "신나게 배우는 영어 회화",
  "category": "외국어",
  "type": "그룹 수업",
  "locations": [
    {
      "region": "강남",
      "specific_location": "협의 후 결정",
      "registered_student": [
        3,
        8
      ],
      "day": "토",
      "time": "12:40-13:40",
      "extra_fee": "Y",
      "extra_fee_amount": "스터디 룸 비용"
    }
  ]
}
```

### Error Response

- Code: 404
  - Reason: Invalid Talent pk
  - Content

```Json
{
  "detail": "찾을 수 없습니다."
}
```

## Talent Curriculum Retrieve

### URL

`/talent/detail/<talent_pk>/curriculum/`

### Method

`GET`

### Header

None

### URL Params

None

### Data Params

None

### Success Response

- Code: 200
- Content

```Json
{
  "id": 3,
  "title": "바리스타 마스터 코스",
  "category": "이색취미",
  "type": "그룹 수업",
  "curriculum": [
    {
      "information": "원두 로스팅",
      "image": "https://projectgori.s3.amazonaws.com/media/talent/curriculum/moon3.jpg"
    },
    {
      "information": "내리기",
      "image": "https://projectgori.s3.amazonaws.com/media/talent/curriculum/moon1.jpg"
    }
  ]
}
```

### Error Response

- Code: 404
  - Reason: Invalid Talent pk
  - Content

```Json
{
  "detail": "찾을 수 없습니다."
}
```

## Talent Class Image Retrieve

### URL

`/talent/detail/<talent_pk>/class-image/`

### Method

`GET`

### Header

None

### URL Params

None

### Data Params

None

### Success Response

- Code: 200
- Content

```Json
{
  "id": 3,
  "title": "바리스타 마스터 코스",
  "category": "이색취미",
  "type": "그룹 수업",
  "class_image": [
    {
      "image": "https://projectgori.s3.amazonaws.com/media/talent/extra_images/moon4.jpg"
    },
    {
      "image": "https://projectgori.s3.amazonaws.com/media/talent/extra_images/moon5.jpg"
    }
  ]
}
```

### Error Response

- Code: 404
  - Reason: Invalid Talent pk
  - Content

```Json
{
  "detail": "찾을 수 없습니다."
}
```

