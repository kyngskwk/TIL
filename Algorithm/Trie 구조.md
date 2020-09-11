# Trie 구조

### Trie란?

> 원래  Trie는 탐색을 뜻하는 retrieval에서 중간 글자인 trie에서 유래되었다. Tree의 일종으로 Prefix tree라고 부르는 사람도 있다고 한다. Prefix로 보면 좀 더 직관적으로 이해가 가능하다. 예를 들면, IP주소를 routing table에 저장할 때 에도 앞에 오는 bit들 부터 정해놓고 routing을 해주는 것과 비슷하다고 볼 수 있다.

![99FC77495E05EF9A25](https://user-images.githubusercontent.com/60081217/92939829-3bd91200-f489-11ea-891b-e1db21e93f64.png)



### Trie구조 왜 사용할까?

- Trie구조는 **탐색 속도가 빠르다**
- 문자열의 길이를 m이라고 할 때, 탐색의 시간복잡도를 계산해보면 O(m)이 되므로 탐색에서는 아주 빠르다는 것을 알 수 있다.
- 물론 각 노드에 자식들을 모두 저장(python으로 구현하면 딕셔너리 형태)저장하기 때문에 사용하는 공간의 크기가 크다는 단점이 있기는 하다.
- 하지만 어떤 문자열을 접두어로 이용하여 찾을 때는 매우 유용하다.



### Python으로 Trie 구현하기

```python
_end = '_end_' 
def make_trie(words): 
	root = dict() 
	for word in words: 
		current_dict = root 
		for letter in word: 
			if _end in current_dict: 
				return False 
			current_dict = current_dict.setdefault(letter, {}) 
		current_dict[_end] = _end 
	return True
```