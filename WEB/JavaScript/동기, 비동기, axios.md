### 동기, 비동기 :  기다려주지 않는 자바스크립트에서 해당 일이 끝나면 실행 시켜줄 것이라는 약속

### 동기식 처리 모델

- 직렬적으로 task를 수행
- 태스크는 순차적으로 실행되며 어떤 작업이 수행중이면 다음 작업은 대기
- 예를들면 서버에서 데이터를 가져와서 화면에 표시하는 작업을 수행할 때, 서버에 데이터를 요청하고 데이터 응답될 때까지 이후 태스크들은 모두 작업 중단 (blocking)

```python
from time import sleep

def sleep_3():
    sleep(3)
    print('잘잤다!') // 2

print('잔다') // 1
sleep_3()
print('학교가자') //3

// 잔다 -> 잘잤다 -> 학교가자
```

### 비동기식 처리 모델

- 3G 네이버 로딩 → 동시다발적으로 로딩된다. 동기식이면 네이버 로고→ 검색창 → 순서대로 로딩이 됨
- 병렬적으로 태스크 수행
- 태스크가 종료되지 않은 상태라 하더라도 대기하지 않고 다음 태스크를 실행
- 예를 들면 서버에서 데이터를 가져와서 화면에 표시하는 작업을 수행할 때, 서버에 데이터를 요청한 이후 서버로부터 데이터가 응답 될 때까지 대기하지 않고 즉시 다음 태스크를 수행
- 자바스크립트의 대부분의 DOM 이벤트와 Timer함수, Ajax 요청은 비동기식 처리 모델로 동작한다.

```jsx
function sleep() {
    console.log('잘잤다') // 3
}

console.log('잔다') // 1
setTimeout(sleep, 3000) 
console.log('학교가자') // 2

// 잔다 -> 학교가자 -> 잘잤다
```

<img width="921" alt="동기,비동기" src="https://user-images.githubusercontent.com/60081217/85918776-9d351000-b8a0-11ea-8d32-030e99286aa7.png">



- 틱(Tick) : stack과 queue를 감시하다가 stack이 비는 순간 callback queue에서 대기하고 잇는 콜백 함수를 스택에 밀어 넣는다.

```jsx
function cb1() {
    console.log('cb1') // 3
}

console.log('Hello') // 1
setTimeout(cb1, 3000) // 3초뒤에 실행 될 것이라는 의미 nope, 3초 뒤에 콜백큐에 추가된다
console.log('byebye') // 2
```

- **3초뒤에 실행 될 것이라는 의미 nope, 3초 뒤에 콜백큐에 추가된다**
- 콜스택에 들어가고 →  콘솔에 출력 ( hello )
- 콜스택에 타이머 들어가고 → 웹 api에 넘겨주고 → 사라짐 ( setTimeout )
- 콜스택에 바이바이 들어가고 → 콜솔에 출력 ( byebye )
- 웹 api → 콜백 큐 → 이벤트 루프 : 스택이 비엇니? 콜백큐 → 콜 스택 → 콘솔 출력하고 위에서 부터 빠짐
- **settimeout은 몇 초 뒤에 실행된다가 아님...  콜백에 추가 되냐 의 문제임**
- 그래서 `setTimeout` 이 0초라도 바로 콜백큐에 들어가는 의미이기 때문에 → 다른 거 다 실행되고 나서 콜백큐에서 나오는 것..! 그래서 결국 **잔다 → 학교가자 → 잘갔다**



- 콜백 지옥(callback hell) :
- → 방지하기 위한 새로운 문법 : axios
- axios CDN을 공식 문서로 가져온다 : Using jsDelivr CDN
- https://github.com/axios/axios

```html
<!--axios 문법 정리 -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">    
    <title>Document</title>
    <script src="<https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js>"></script>
</head>
<body>
    <h1>axios 문법</h1>
    <script>
      axios.get('<https://koreanjson.com/posts/1>')
      .then(response => {
          console.log(response)
          // .을 이용해서 접근 가능
          return response.data
      })
      .then(data => {
          console.log(data)
          // 데이터의 컨텐트가 필요함
          return data.content
      })

      // 해당 작업이 실패하면 catch가 실행됨
      .catch(error => {
          console.log(error)
      })
    </script>
</body>
</html>

```



- 정상처리

  <img width="673" alt="normal1" src="https://user-images.githubusercontent.com/60081217/85918841-1fbdcf80-b8a1-11ea-87fb-d7dbf8c244a0.png">

- 에러 뜰 때

  <img width="672" alt="error" src="https://user-images.githubusercontent.com/60081217/85918842-21879300-b8a1-11ea-9657-71e8a55652fc.png">

  