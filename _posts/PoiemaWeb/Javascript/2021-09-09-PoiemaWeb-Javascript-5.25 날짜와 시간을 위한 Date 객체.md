---
title: "[개발자 블로그] 5.25 날짜와 시간을 위한 Date 객체" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2021-09-09 +0800
categories: [PoiemaWeb, Javascript] # categories는 최대 2개까지 가능
tags: [poiemaweb, javascript, es6] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

Date 객체는 날짜와 시간(년, 월, 일, 시, 분, 초, 밀리초(천분의 1초(millisecond, ms)))을 위한 메소드를 제공하는 빌트인 객체이면서 생성자 함수이다.

Date 생성자 함수로 생성한 Date 객체는 내부적으로 숫자값을 갖는다. 이 값은 1970년 1월 1일 00:00(UTC)을 기점으로 현재 시간까지의 밀리초를 나타낸다.

UTC(협정 세계시: Coordinated Universal Time)는 GMT(그리니치 평균시: Greenwich Mean Time)로 불리기도 하는데 UTC와 GMT는 초의 소숫점 단위에서만 차이가 나기 때문에 일상에서는 혼용되어 사용된다. 기술적인 표기에서는 UTC가 사용된다.

KST(Korea Standard Time)는 UTC/GMT에 9시간을 더한 시간이다. 즉, KST는 UTC/GMT보다 9시간이 빠르다. 예를 들어, UTC 00:00 AM은 KST 09:00 AM이다.

현재의 날짜와 시간은 자바스크립트 코드가 동작한 시스템의 시계에 의해 결정된다. 시스템 시계의 설정(timezone, 시간)에 따라 서로 다른 값을 가질 수 있다.

# [1.1 new Date()](https://poiemaweb.com/js-date#11-new-date)
인수를 전달하지 않으면 현재 날짜와 시간을 가지는 인스턴스를 반환한다.

~~~
const date = new Date();
console.log(date); // Thu May 16 2019 17:16:13 GMT+0900 (한국 표준시)
~~~

# [1.2 new Date(milliseconds)](https://poiemaweb.com/js-date#12-new-datemilliseconds)
인수로 숫자 타입의 밀리초를 전달하면 1970년 1월 1일 00:00(UTC)을 기점으로 인수로 전달된 밀리초만큼 경과한 날짜와 시간을 가지는 인스턴스를 반환한다.

~~~
// KST(Korea Standard Time)는 GMT(그리니치 평균시: Greenwich Mean Time)에 9시간을 더한 시간이다.
let date = new Date(0);
console.log(date); // Thu Jan 01 1970 09:00:00 GMT+0900 (한국 표준시)

// 86400000ms는 1day를 의미한다.
// 1s = 1,000ms
// 1m = 60s * 1,000ms = 60,000ms
// 1h = 60m * 60,000ms = 3,600,000ms
// 1d = 24h * 3,600,000ms = 86,400,000ms
date = new Date(86400000);
console.log(date); // FFri Jan 02 1970 09:00:00 GMT+0900 (한국 표준시)
~~~

# [1.3 new Date(dateString)](https://poiemaweb.com/js-date#13-new-datedatestring)
인수로 날짜와 시간을 나타내는 문자열을 전달하면 지정된 날짜와 시간을 가지는 인스턴스를 반환한다. 이때 인수로 전달한 문자열은 Date.parse 메소드에 의해 해석 가능한 형식이어야 한다.

~~~
let date = new Date('May 16, 2019 17:22:10');
console.log(date); // Thu May 16 2019 17:22:10 GMT+0900 (한국 표준시)

date = new Date('2019/05/16/17:22:10');
console.log(date); // Thu May 16 2019 17:22:10 GMT+0900 (한국 표준시)
~~~

# [1.4 new Date(year, month[, day, hour, minute, second, millisecond])](https://poiemaweb.com/js-date#14-new-dateyear-month-day-hour-minute-second-millisecond)
인수로 년, 월, 일, 시, 분, 초, 밀리초를 의미하는 숫자를 전달하면 지정된 날짜와 시간을 가지는 인스턴스를 반환한다. 이때 년, 월은 반드시 지정하여야 한다. 지정하지 않은 옵션 정보는 0 또는 1으로 초기화된다.

인수는 다음과 같다.

|인수|내용|
|-|-|
|year|1900년 이후의 년|
|month|월을 나타내는 0~11까지의 정수(주의: 0부터 시작, 0 = 1월)|
|day|일을 나타내는 1 ~ 31까지의 정수|
|hour|시를 나타내는 0 ~ 23까지의 정수|
|minute|분을 나타내는 0 ~ 59까지의 정수|
|second|초를 나타내는 0 ~ 59까지의 정수|
|millisecond|밀리초를 나타내는 0 ~ 999까지의 정수|

년, 월을 지정하지 않은 경우 1970년 1월 1일 00:00(UTC)을 가지는 인스턴스를 반환한다.

~~~
// 월을 나타내는 4는 5월을 의미한다.
// 2019/5/1/00:00:00:00
let date = new Date(2019, 4);
console.log(date); // Wed May 01 2019 00:00:00 GMT+0900 (한국 표준시)

// 월을 나타내는 4는 5월을 의미한다.
// 2019/5/16/17:24:30:00
date = new Date(2019, 4, 16, 17, 24, 30, 0);
console.log(date); // Thu May 16 2019 17:24:30 GMT+0900 (한국 표준시)

// 가독성이 훨씬 좋다.
date = new Date('2019/5/16/17:24:30:10');
console.log(date); // Thu May 16 2019 17:24:30 GMT+0900 (한국 표준시)
~~~

# [1.5 Date 생성자 함수를 new 연산자없이 호출](https://poiemaweb.com/js-date#15-date-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98%EB%A5%BC-new-%EC%97%B0%EC%82%B0%EC%9E%90%EC%97%86%EC%9D%B4-%ED%98%B8%EC%B6%9C)
Date 생성자 함수를 new 연산자없이 호출하면 인스턴스를 반환하지 않고 결과값을 문자열로 반환한다.

~~~
let date = Date();
console.log(typeof date, date); // string Thu May 16 2019 17:33:03 GMT+0900 (한국 표준시)
~~~

# [2. Date 메소드](https://poiemaweb.com/js-date#2-date-%EB%A9%94%EC%86%8C%EB%93%9C)

# [2.1 Date.now](https://poiemaweb.com/js-date#21-datenow)
1970년 1월 1일 00:00:00(UTC)을 기점으로 현재 시간까지 경과한 밀리초를 숫자로 반환한다.

~~~
const now = Date.now();
console.log(now);
~~~

# [2.2 Date.parse](https://poiemaweb.com/js-date#22-dateparse)
1970년 1월 1일 00:00:00(UTC)을 기점으로 인수로 전달된 지정 시간(new Date(dateString)의 인수와 동일한 형식)까지의 밀리초를 숫자로 반환한다.

~~~
let d = Date.parse('Jan 2, 1970 00:00:00 UTC'); // UTC
console.log(d); // 86400000

d = Date.parse('Jan 2, 1970 09:00:00'); // KST
console.log(d); // 86400000

d = Date.parse('1970/01/02/09:00:00'); // KST
console.log(d); // 86400000
~~~

# [2.3 Date.UTC](https://poiemaweb.com/js-date#23-dateutc)
1970년 1월 1일 00:00:00(UTC)을 기점으로 인수로 전달된 지정 시간까지의 밀리초를 숫자로 반환한다.

Date.UTC 메소드는 `new Date(year, month[, day, hour, minute, second, millisecond])`와 같은 형식의 인수를 사용해야 한다. Date.UTC 메소드의 인수는 local time(KST)가 아닌 UTC로 인식된다.

~~~
let d = Date.UTC(1970, 0, 2);
console.log(d); // 86400000

d = Date.UTC('1970/1/2');
console.log(d); // NaN
~~~

<b>month는 월을 의미하는 0~11까지의 정수이다. 0부터 시작하므로 주의가 필요하다.</b>

# [2.4 Date.prototype.getFullYear](https://poiemaweb.com/js-date#24-dateprototypegetfullyear)
년도를 나타내는 4자리 숫자를 반환한다.

~~~
const today = new Date();
const year = today.getFullYear();

console.log(today); // Thu May 16 2019 17:39:30 GMT+0900 (한국 표준시)
console.log(year);  // 2019
~~~

# [2.5 Date.prototype.setFullYear](https://poiemaweb.com/js-date#25-dateprototypesetfullyear)
년도를 나타내는 4자리 숫자를 설정한다. 년도 이외 월, 일도 설정할 수 있다.

~~~
dateObj.setFullYear(year[, month[, day]])
~~~

~~~
const today = new Date();

// 년도 지정
today.setFullYear(2000);

let year = today.getFullYear();
console.log(today); // Tue May 16 2000 17:42:40 GMT+0900 (한국 표준시)
console.log(year);  // 2000

// 년도 지정
today.setFullYear(1900, 0, 1);

year = today.getFullYear();
console.log(today); // Mon Jan 01 1900 17:42:40 GMT+0827 (한국 표준시)
console.log(year);  // 1900
~~~

# 그 이외의 날짜와 시간을 지정하고 반환하는 메소드
- [2.6 Date.prototype.getMonth](https://poiemaweb.com/js-date#26-dateprototypegetmonth)
- [2.7 Date.prototype.setMonth](https://poiemaweb.com/js-date#27-dateprototypesetmonth)
- [2.8 Date.prototype.getDate](https://poiemaweb.com/js-date#28-dateprototypegetdate)
- [2.9 Date.prototype.setDate](https://poiemaweb.com/js-date#29-dateprototypesetdate)
- [2.10 Date.prototype.getDay](https://poiemaweb.com/js-date#210-dateprototypegetday)
- [2.11 Date.prototype.getHours](https://poiemaweb.com/js-date#211-dateprototypegethours)
- [2.12 Date.prototype.setHours](https://poiemaweb.com/js-date#212-dateprototypesethours)
- [2.13 Date.prototype.getMinutes](https://poiemaweb.com/js-date#213-dateprototypegetminutes)
- [2.14 Date.prototype.setMinutes](https://poiemaweb.com/js-date#214-dateprototypesetminutes)
- [2.15 Date.prototype.getSeconds](https://poiemaweb.com/js-date#215-dateprototypegetseconds)
- [2.16 Date.prototype.setSeconds](https://poiemaweb.com/js-date#216-dateprototypesetseconds)
- [2.17 Date.prototype.getMilliseconds](https://poiemaweb.com/js-date#217-dateprototypegetmilliseconds)
- [2.18 Date.prototype.setMilliseconds](https://poiemaweb.com/js-date#218-dateprototypesetmilliseconds)

# [2.19 Date.prototype.getTime](https://poiemaweb.com/js-date#219-dateprototypegettime)
`1970년 1월 1일 00:00:00(UTC)를 기점으로` 현재 시간까지 경과된 밀리초를 반환한다.

~~~
const today = new Date();
const time = today.getTime();

console.log(today); // Thu May 16 2019 17:56:08 GMT+0900 (한국 표준시)
console.log(time);  // 1557996968335
~~~

# [2.20 Date.prototype.setTime](https://poiemaweb.com/js-date#220-dateprototypesettime)
`1970년 1월 1일 00:00:00(UTC)를 기점으로` 현재 시간까지 경과된 밀리초를 설정한다.

~~~
dateObj.setTime(time)
~~~

~~~
const today = new Date();

// 1970년 1월 1일 00:00:00(UTC)를 기점으로 현재 시간까지 경과된 밀리초 지정
today.setTime(86400000); // 86400000 === 1day

const time = today.getTime();
console.log(today); // Fri Jan 02 1970 09:00:00 GMT+0900 (한국 표준시)
console.log(time);  // 86400000
~~~

# [2.21 Date.prototype.getTimezoneOffset](https://poiemaweb.com/js-date#221-dateprototypegettimezoneoffset)
UTC와 지정 로케일(Locale) 시간과의 차이를 분단위로 반환한다.

~~~
const today = new Date();
const x = today.getTimezoneOffset() / 60; // -9

console.log(today); // Thu May 16 2019 17:58:13 GMT+0900 (한국 표준시)
console.log(x);     // -9
~~~

KST(Korea Standard Time)는 UTC에 9시간을 더한 시간이다. 즉, UTC = KST - 9h이다.

# [2.22 Date.prototype.toDateString](https://poiemaweb.com/js-date#222-dateprototypetodatestring)
사람이 읽을 수 있는 형식의 문자열로 `날짜를 반환`한다.

~~~
const d = new Date('2019/5/16/18:30');

console.log(d.toString());     // Thu May 16 2019 18:30:00 GMT+0900 (한국 표준시)
console.log(d.toDateString()); // Thu May 16 2019
~~~

# [2.23 Date.prototype.toTimeString](https://poiemaweb.com/js-date#223-dateprototypetotimestring)
사람이 읽을 수 있는 형식의 문자열로 `시간을 반환`한다.

~~~
const d = new Date('2019/5/16/18:30');

console.log(d.toString());     // Thu May 16 2019 18:30:00 GMT+0900 (한국 표준시)
console.log(d.toTimeString()); // 18:30:00 GMT+0900 (한국 표준시)
~~~

# [3. Date Example](https://poiemaweb.com/js-date#3-date-example)
현재 날짜와 시간을 초단위로 반복 출력하는 예제이다.

~~~
(function printNow() {
  const today = new Date();

  const dayNames = ['(일요일)', '(월요일)', '(화요일)', '(수요일)', '(목요일)', '(금요일)', '(토요일)'];
  // getDay: 해당 요일(0 ~ 6)를 나타내는 정수를 반환한다.
  const day = dayNames[today.getDay()];

  const year = today.getFullYear();
  const month = today.getMonth() + 1;
  const date = today.getDate();
  let hour = today.getHours();
  let minute = today.getMinutes();
  let second = today.getSeconds();
  const ampm = hour >= 12 ? 'PM' : 'AM';

  // 12시간제로 변경
  hour %= 12;
  hour = hour || 12; // 0 => 12

  // 10미만인 분과 초를 2자리로 변경
  minute = minute < 10 ? '0' + minute : minute;
  second = second < 10 ? '0' + second : second;

  const now = `${year}년 ${month}월 ${date}일 ${day} ${hour}:${minute}:${second} ${ampm}`;

  console.log(now);
  setTimeout(printNow, 1000);
}());
~~~

#### 출처
- [https://poiemaweb.com/js-date](https://poiemaweb.com/js-date)