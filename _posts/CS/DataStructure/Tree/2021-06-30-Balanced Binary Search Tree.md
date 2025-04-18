---
title: "[DataStructure] Balanced Binary Search Tree(균형 이진탐색 트리)"
date: 2021-06-30 +0800
categories: [DataStructure, Tree]
tags: [datastructure, tree, balanced-binary-search-tree]
toc: true
comments: true
---

# Balanced Binary Search Tree(균형 이진탐색 트리)
- 이진 탐색트리에 새로운 노드가 삽입이 되면 부모의 노드보다 작은 값은 왼쪽으로 큰 값은 오른쪽으로 추가하면서 서브트리가 계속 구성되어진다.
- 이진탐색트리의 치명적인 단점은 자료가 많아질수록 트리의 높이(height)가 커지기 때문에 검색에 불리해지고 최악의 경우 (worst case) 특정 노드를 탐색하는데 log(n) 이 소요될수도 있다.<br>

- 예를들어 지금까지 배운 방식대로 1 , 2, 3, 4 를 이진트리에 추가한다면 아래의 그림과 같이 된다.<br>
<img width="674" alt="스크린샷 2021-04-22 오후 4 45 38" src="https://user-images.githubusercontent.com/44339530/115676051-2c5c6880-a38a-11eb-8b9e-f3e1b208d616.png"><br>

- 위의 그림에서 4를 찾는다하면 4번의 탐색이 필요하게 된다.
- 이렇게 이진 탐색트리가 한쪽으로 치우쳐저 있는 트리를 skewed binary search tree(편향 이진 탐색 트리) 라고 부르는데 성능면에서 최악의 경우(worst case)인 것이다.
- 그래서 최악의 경우를 피하기 위해 또는 계속 추가되는 노드로 인해 높이가 점점 커지는것을 좀 더 막을수 있는 방법이 무엇일까 ? 고민하였고 나오게된 것이 균형 이진 탐색 트리(Balanced Binary Search Tree) 인 것이다.
- <b>균형 이진 탐색 트리란 노드의 삽입과 삭제가 일어나는 경우에 자동으로 그 높이를 작게 유지하도록 되어 있다.</b>
- 아무리 노드의 빈번한 추가 삭제가 아무리 많이 있어도 높이가 얼마 이상되지 않도록 제약하는 특성을 갖게 되어 성능상 매우 유리하다.<br>

<img width="879" alt="스크린샷 2021-04-22 오후 4 49 23" src="https://user-images.githubusercontent.com/44339530/115676587-b1e01880-a38a-11eb-9f79-cc2ab891371f.png"><br>

- (a)처럼 균형이 맞지 않는 트리의 경우 루트에서 특정 노드로 갈 때 평균 3.27회의 노드 접근이 필요하다. 하지만 (b)처럼 트리의 높이 균형을 맞춘다면 이동 비용이 3.0으로 감소된다.
- 이런 균형 이진 탐색트리에는 여러가지가 있는데 대표적으로 <b>AVL 트리 , 레드-블랙트리(Red-Black Tree) , B 트리, B+ 트리, B* 트리</b> 들이 있다. 각 균형탐색트리마다 트리 높이의 균형을 잡기 위한 알고리즘들이 조금씩 다르다.

# 균형 이진 탐색트리의 종류
- [red-black 트리 & AVL 트리](https://github.com/jeonyoungho/TIL/blob/master/DataStructure/red-black%20%ED%8A%B8%EB%A6%AC%20%26%20AVL%20%ED%8A%B8%EB%A6%AC.md)

#### 출처
- [https://jackpot53.tistory.com/17](https://jackpot53.tistory.com/17)