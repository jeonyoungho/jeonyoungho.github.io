---
title: "[Webpack] Source Map" # post의 layout이 기본적으로 post로 설정되어있어서 Front Matter에 따로 layout변수를 만들어 주지 않아도 됨
date: 2022-04-05 +0800
categories: [Frontend, Environment] # categories는 최대 2개까지 가능
tags: [npm, yarn] # TAG는 반드시 소문자로 이루어져야함, 0~무한개까지 지정 가능
toc: true # Table Of Content(TOC) 옵션, 기본적으로 포스트의 오른쪽 패널에 위치
comments: true # 댓글 유무 지정
# image: /assets/img/test.png # Preview image
# pin: true # 홈페이지 메인화면에 특정 게시물 고정
---

# 소스맵(Source Map) 이란?
웹팩은 흔히 여러 자바스크립트 파일을 하나로 합쳐주는 번들의 역할을 한다. 그리고 보통 서버에 배포할때 성능 최적화를 위해 HTML, CSS, JS와 같은 웹 자원들을 합축하게 된다. 그런데 만약 압축하여 배포한 파일에서 에러가 난다면 어떻게 디버깅할 수 있을까?

<b>바로 소스 맵을 이용해 배포용 파일의 특정 부분이 원본 소스의 어떤 부분인지 확인하는 것이다. 이러한 편의성을 제공하는 것이 소스 맵입니다.</b>

# 소스 맵 설정하기
웹팩에서 소스 맵을 설정하는 방법은 아래와 같습니다.

```javascript
// webpack.config.js
module.exports = {
  devtool: 'cheap-eval-source-map'
}
```

devtool 속성을 추가하고 소스 맵 설정 옵션 중 하나를 선택해 지정해주면 된다.

# 소스 맵 설정 옵션
위에서 정의한 소스 맵 설정 옵션 이외에도 많은 옵션들이 있다. 자세한 옵션 속성과 비교는 다음 [링크](https://webpack.js.org/configuration/devtool/#devtool)로 확인하자.

# 소스 맵은 어떻게 동작할까?
webpack 을 통해서 번들링된 bundle.js 파일 끝에 보면 아래와 같은 주석이 있음을 확인할 수 있다.

```
//# sourceMappingURL=<url>
```

<b>이 특별한 주석을 브라우저가 읽고 Source Map 이 저장한 파일 찾아, 그 파일을 바탕으로 원래 코드와 번들된 코드를 연결한다. [webpack Devtool 문서](https://webpack.js.org/configuration/devtool/)를 보면 source map 을 만드는 여러 방법이 나열되어 있는데, 각 옵션이 다 위 Source Map 주석을 어떻게 표현할지와 연관되는 옵션이다.</b>

## source-map
가장 기본적인 옵션이다. map 파일을 만들고 `url` 에 파일 경로를 추가합니다.

```javascript
//# sourceMappingURL=bundle.js.map
```

```javascript
{"version":3,"sources":["webpack:///./example.coffee"],"names":[],"mappings":";;;;;;;;;AAEU;;;AAAA;;AACV,OACE;EAAA,MAAQ,IAAI,CAAC,IAAb;EACA,QAAQ,MADR;EAEA,MAAQ,SAAC,CAAD;WAAO,IAAI,OAAO,CAAP;EAAX;AAFR,EAFQ;;;AAOV,OAAO,SAAC,MAAD,KAAS,OAAT;SACL,MAAM,MAAN,EAAc,OAAd;AADK","file":"./bundle-source-map.js","sourcesContent":["# Taken from http://coffeescript.org/\n\n# Objects:\nmath =\n  root:   Math.sqrt\n  square: square\n  cube:   (x) -> x * square x\n\n# Splats:\nrace = (winner, runners...) ->\n  print winner, runners\n"],"sourceRoot":""}
```

# eval 옵션
eval 이 붙는 source map 옵션은 JavaScript 의 함수인 eval() 을 사용하여 source map 을 만든다. eval 함수로 각 모듈을 따로 실행시키기 때문에 수정된 모듈만 재빌드해서 빠르지만 <b>정확한 소스 코드 위치를 매핑하지 못하는 경우가 종종있다.</b>

```javascript
eval("// Taken from http://coffeescript.org/\n\n// Objects:\nvar math, race;\n\nmath = {\n root: Math.sqrt,\n square: square,\n cube: function(x) {\n return x * square(x);\n }\n};\n\n// Splats:\nrace = function(winner, ...runners) {\n return print(winner, runners);\n};\n//# sourceURL=[module]\n//# sourceMappingURL=data:application/json;charset=utf-8;base64,eyJ2ZXJzaW9uIjozLCJzb3VyY2VSb290IjoiIiwic291cmNlcyI6WyJ3ZWJwYWNrOi8vLy4vZXhhbXBsZS5jb2ZmZWU/MjQxNiJdLCJuYW1lcyI6W10sIm1hcHBpbmdzIjoiQUFFVTs7O0FBQUEsSUFBQSxJQUFBLEVBQUE7O0FBQ1YsSUFBQSxHQUNFO0VBQUEsSUFBQSxFQUFRLElBQUksQ0FBQyxJQUFiO0VBQ0EsTUFBQSxFQUFRLE1BRFI7RUFFQSxJQUFBLEVBQVEsUUFBQSxDQUFDLENBQUQsQ0FBQTtXQUFPLENBQUEsR0FBSSxNQUFBLENBQU8sQ0FBUDtFQUFYO0FBRlIsRUFGUTs7O0FBT1YsSUFBQSxHQUFPLFFBQUEsQ0FBQyxNQUFELEVBQUEsR0FBUyxPQUFULENBQUE7U0FDTCxLQUFBLENBQU0sTUFBTixFQUFjLE9BQWQ7QUFESyIsInNvdXJjZXNDb250ZW50IjpbIiMgVGFrZW4gZnJvbSBodHRwOi8vY29mZmVlc2NyaXB0Lm9yZy9cblxuIyBPYmplY3RzOlxubWF0aCA9XG4gIHJvb3Q6ICAgTWF0aC5zcXJ0XG4gIHNxdWFyZTogc3F1YXJlXG4gIGN1YmU6ICAgKHgpIC0+IHggKiBzcXVhcmUgeFxuXG4jIFNwbGF0czpcbnJhY2UgPSAod2lubmVyLCBydW5uZXJzLi4uKSAtPlxuICBwcmludCB3aW5uZXIsIHJ1bm5lcnNcbiJdLCJmaWxlIjoiMC5qcyJ9\n//# sourceURL=webpack-internal:///0\n");
```

## inline 옵션
map 파일을 만들지 않고 주석에 파일을 data URL 로 적습니다. Source Map 이 독립된 파일로 존재하지 않고 bundle.js 파일 내에 포함되게 됩니다.

```javascript
//# sourceMappingURL=data:application/json;charset=utf-8;base64,eyJ2ZXJzaW9uIjozLCJzb3VyY2VzIjpbIndlYnBhY2s6Ly8vLi9leGFtcGxlLmNvZmZlZSJdLCJuYW1lcyI6W10sIm1hcHBpbmdzIjoiOzs7Ozs7Ozs7QUFFVTs7O0FBQUE7O0FBQ1YsT0FDRTtFQUFBLE1BQVEsSUFBSSxDQUFDLElBQWI7RUFDQSxRQUFRLE1BRFI7RUFFQSxNQUFRLFNBQUMsQ0FBRDtXQUFPLElBQUksT0FBTyxDQUFQO0VBQVg7QUFGUixFQUZROzs7QUFPVixPQUFPLFNBQUMsTUFBRCxLQUFTLE9BQVQ7U0FDTCxNQUFNLE1BQU4sRUFBYyxPQUFkO0FBREsiLCJmaWxlIjoiLi9idW5kbGUtaW5saW5lLXNvdXJjZS1tYXAuanMiLCJzb3VyY2VzQ29udGVudCI6WyIjIFRha2VuIGZyb20gaHR0cDovL2NvZmZlZXNjcmlwdC5vcmcvXG5cbiMgT2JqZWN0czpcbm1hdGggPVxuICByb290OiAgIE1hdGguc3FydFxuICBzcXVhcmU6IHNxdWFyZVxuICBjdWJlOiAgICh4KSAtPiB4ICogc3F1YXJlIHhcblxuIyBTcGxhdHM6XG5yYWNlID0gKHdpbm5lciwgcnVubmVycy4uLikgLT5cbiAgcHJpbnQgd2lubmVyLCBydW5uZXJzXG4iXSwic291cmNlUm9vdCI6IiJ9
```

## cheap 옵션
<b>cheap 옵션은 라인 넘버만 매핑하고 라인에서 몇 번째 글자인지는 매핑하지 않습니다. 그래서 빠르게 빌드가 가능한 반면 정확한 매핑은 포기하는 옵션입니다.</b>

# Source Map 이 소스코드를 찾는 원리
웹팩에 source map을 설정한 뒤 빌드를 하면 bundle.js.map 파일이 생성된다.

- bundle.js.map

```json
{ 
    version : 3, 
    file: "bundle.js",
    sourceRoot : "", 
    sources: ["a.js", "b.js"], 
    names: ["src", "maps", "are", "fun"],
    mappings: "AAgBC,SAAQ,CAAEA" 
}
```

bundle.js.map 파일은 json 포맷으로 만들어진다.
`sources` 는 bundle.js 를 만드는데 활용된 소스 코드 파일 목록이다.
`names` 는 소스 코드의 모든 변수와 함수 이름이 기록된다.
`mappings` 가 실제 코드와 매핑할 수 있도록 하는 데이터이며 Base64 VLQ 로 인코딩되어 기록됩니다.

> **Note**: Base64 VLQ 와 관련된 자세한 내용은 [이 글](https://www.html5rocks.com/en/tutorials/developertools/sourcemaps/#toc-base64vlq) 을 참고하자.

<b>따라서 디버깅하다가 매핑이 비활성화되어 있거나 매핑이 잘못된 경우 map 파일에서 sources 에 소스 파일이 포함되어 있는지 그리고 디버깅하려는 변수 또는 함수가 names 에 포함되어 있는지 확인해보고 잘못되어 있으면 다시 빌드해서 확인해보면 된다.</b>

`mappings` 가 잘못되었는지는 `Mozilla` 에서 제공하는 [base64 VLQ 디코더](https://github.com/mozilla/source-map/blob/master/lib/base64-vlq.js)로 디코딩하여 확인하면 된다. 만약 `mappings` 가 잘못되었다면 `webpack.config.js` 에서 `devtool 옵션`을 `source-map` 으로 변경해보고 시도해보길 추천한다.

#### 출처
- [https://joshua1988.github.io/webpack-guide/devtools/source-map.html#%EC%86%8C%EC%8A%A4-%EB%A7%B5](https://joshua1988.github.io/webpack-guide/devtools/source-map.html#%EC%86%8C%EC%8A%A4-%EB%A7%B5)
- [김예건님의 포스팅 https://ibocon.tistory.com/269](https://ibocon.tistory.com/269)
- [https://velog.io/@seeker1207/SourceMap%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8Cwith-webPack-devtool](https://velog.io/@seeker1207/SourceMap%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8Cwith-webPack-devtool)