# Axios 실습

> axios 라이브러리를 활용해서 버튼을 누르면 강아지 사진을 1개씩 가져오는 코드를 작성해보자.



- dog.ceo의 dog-api를 사용한다.
- 랜덤 강아지 사진 : https://dog.ceo/dog-api/ → https://dog.ceo/api/breeds/image/random

<img width="647" alt="dog_api" src="https://user-images.githubusercontent.com/60081217/85919052-0d449580-b8a3-11ea-8d74-fc760bae1a4c.png">



- 우리는 결국 이런 json의 message key 값의 value가 필요함
- 콘솔에서 구조 확인하고 필요한 key값으로 접근한다.

<img width="664" alt="console" src="https://user-images.githubusercontent.com/60081217/85919050-0b7ad200-b8a3-11ea-86b8-d2316e34c212.png">



### 실습 코드

```html
<!--실습 코드-->
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>exercise</title>
	<!--먼저 CDN 가져와야 한다.-->
  <script src="<https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js>"></script>
</head>
<body>
  <h1>Dog Image(s)</h1>
  <hr>

  <h2>강아지</h2>  
  <button id="dog">강아지</button>
  <div class="dogs"></div>

  <script>
	<!--글자 구분 때문에 아래에 기입-->
  </script>
</body>
</html>
```



### 함수 이름을 지정하는 방법

```jsx
// 여기에 코드를 작성하시오.
// dog api에 요청을 보내서 이미지 주소를 받아 이미지 태그를 만드는 콜백 함수
const getDogImage = function () {
  axios.get('<https://dog.ceo/api/breeds/image/random>')
    .then(response => {
      const URL = response.data.message
      // <img> 태그 생성
      const dogImage = document.createElement('img')
      // <img> 태그의 속성 값 넣기
      dogImage.src = URL
      dogImage.style.height = '300px'
      // 완성된 <img>를 특정 div 안에 넣기
      document.querySelector('.dogs').append(dogImage)
    })
    
    .catch(error => {
      console.log(error)
    })
}

const dogButton = document.querySelector('#dog')
dogButton.addEventListener('click', getDogImage)
```



### 익명의 함수를 이벤트 리스너 안에 넣는 방법

```jsx
// 이벤트 리스너 - 익명함수
const dogButton = document.querySelector('#dog')
dogButton.addEventListener('click', function() {
  axios.get('<https://dog.ceo/api/breeds/image/random>')
    .then(response => {
      const URL = response.data.message
      // <img> 태그 생성
      const dogImage = document.createElement('img')
      // <img> 태그의 속성 값 넣기
      dogImage.src = URL
      dogImage.style.height = '300px'
      // 완성된 <img>를 특정 div 안에 넣기
      document.querySelector('.dogs').append(dogImage)
    })
    
    .catch(error => {
      console.log(error)
    })
})
```

