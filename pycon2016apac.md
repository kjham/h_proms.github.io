---
layout: page
title: 코딩배틀 가위바위보 in 파이콘 2016 APAC
image: /files/covers/python-in-ex-machina.jpg
permalink: /pycon2016apac/
tags: [events]
---

[CODING BATTLE 가위바위보! - 못다한 이야기](http://tech.kakao.com/2016/08/19/gawibawibo/) 참조

---

> ## 둘째날(8/14) 경기 결과
>
> * 1위 : qkqnrpa
> * 2위 : johnjanghochoi
> * 3위 : bak723 &amp; bage79

---

> ## 첫째날(8/13) 경기 결과
>
> * 1위 : k239507
> * 2위 : hulk
> * 3위 : eclipse1725 &amp; hahanbyul

---

## 참가 자격

* [파이콘 2016 APAC](https://www.pycon.kr/2016apac) 참석자 누구나 가능합니다.
* 주의: [Kakao Corp.](http://kakaocorp.com)의 임직원도 참여하실 수 있으나, 경품은 받으실 수 없습니다.
* **8/14변경** 어제(8/13) 참가자도 다시 참가하실 수 있지만, 동일한 상품을 중복으로 받을 수 없습니다.


## 참여 방법

아래의 요구사항을 만족하는 **파이썬 소스코드**를 [opensource@kakaocorp.com](mailto:opensource@kakaocorp.com)에게 메일로 보내주세요.

* 메일 제목: `pycon2016apac`
* 첨부 파일: `player.py` 또는 `player.py`를 포함하는 `player.zip`
* 신청 마감: 파이콘 2016 APAC 행사 양일 **오후 2시**
* 참가 인원: 선착순 **100명**
* 결과 발표: 파이콘 2016 APAC 행사 양일 **오후 5시**(카카오 부스에서 Live!)

> <img src="http://item.kakaocdn.net/do/-26p06+UqCd0OAgiRHNZwHaq4FJCveCBKCNZV-bZscw_/477c52636630bc15b2890bde099cba0a1667fc7b08261b4c493670baa83d5cb9" class="pull-right" />
> **21세기에 왠 메일?**
>
> 애초 계획은 클라우드 서버에 올려서 서버-to-서버 RESTful API를 게임을 진행하는 방식이었지만,
> 행사장의 극도로 불안정한 네트웍을 고려하여, 로컬에서 개발하고 동작할 수 있는 지금의 방식으로 변경하였습니다.
>
> 저도 급하게 바꾸느라 죽을 맛이에요 ㅠㅠ


## 게임 규칙

* **각 라운드별로 정해진 회수**의 가위-바위-보에서 점수를 많이 획득한 플레이어가 승리합니다.
 * 예선전: 100회
 * 본선 토너먼트(32강, 16강, 8강): 1000회
 * 준결승: 5000회
 * 결승: 10000회
* 승리는 **3점**, 무승부는 **1점**, 패배는 **0점** 입니다.(주의: 무승부에 점수가 있음!)
* 승자는 **총점** &gt; **다승** &gt; **참가접수시간** 순으로 결정됩니다.
* 예선전은 **풀 리그(full league)** 방식으로 진행됩니다. 즉, 각 참가자는 모든 다른 참가자와 대결합니다.
* 예선전 결과 상위 **32명**이 본선 토너먼트로 진출합니다.(참자가 숫자에 따라 본선 토너먼트 시작 인원이 달라질 수 있습니다)
* 본선은 **토너먼트(tournament)** 방식으로 진행됩니다. 최고 득점자와 최저 득점자가 대결합니다.
* 16강 - 8강 - 준결승 - 결승을 거쳐 우승자가 나올때 까지 게임이 계속됩니다.
* 주의: **제출한 코드**에서 발생하는 오류는 **패배**로 처리됩니다.


## 시상 내역

* 우승: **큰** 라이언
* 준우승:  캔들 워머
* 3위, 4위: **작은** 라이언
* 예선전 참가자 전원: 카카오프렌즈 목베개
* **8/14변경** 어제(8/13) 참가했더라도 오늘 참가 가능
* **8/14변경** 어제 수령한 상품과 동일한 상품/기념품 수령 불가(다른 상품은 가능)
 - positive: 어제는 32강 오늘은 3위, 어제는 3위 오늘은 우승... 등
 - negative: 어제는 32강 오늘은 8강, 어제는 4위 오늘은 3위... 등


## 요구사항

1. `파이썬3`을 사용하여, `player.py` 파일을 작성합니다.
1. `상대 플레이어의 직전까지의 경기 기록`을 전달받아
1. 가위(`gawi`), 바위(`bawi`), 보(`bo`) 중의 하나를 리턴하는
1. 함수 `show_me_the_hand`를 작성하세요.
1. **8/14변경** 확장 패키지 사용 불가!
1. **8/14변경** `random` 패키지, `os.urandom()` 을 포함한 기본 파이썬 패키지에 포함된 random 사용 불가!
1. **8/14변경** 이 홈페이지의 예제와 동일한 코드를 제출하면 실격!

```
경기기록 ::= [ (가위바위보, 승무패), ...]
가위바위보 ::= 'gawi' | 'bawi' | 'bo' ('gawi'=가위, 'bawi'=바위, 'bo'=보)
승무패 ::= 1 | 0 | -1 (1=승리, 0=무승, -1=패배)
```

### 예제1: 가위, 바위, 보를 **랜덤**으로 내는 플레이어

* **8/14변경** 오늘 이렇게 내면 실격!!!

```python
from random import choice

def show_me_the_hand(records):
    # 상대방이 지금까지 뭘 냈던 랜덤으로 선택
    return choice(['gawi', 'bawi', 'bo'])
```

#### 예제2: 최초 한번은 **랜덤**으로, 이후부터는 상대방이 낸 걸 **따라**내는 플레이어

* **8/14변경** 오늘 이렇게 내면 실격!!!

```python
from random import choice

def show_me_the_hand(records):
    # 최초 한번은 랜덤...
    if len(records) == 0:
        return choice(['gawi', 'bawi', 'bo'])
    # 이후에는 상대방이 낸 걸 따라내는 플레이어
    return records[0][0]
```

#### 예제3: 항상 `주먹`만 내는 플레이어

```python
def show_me_the_hand(records):
    # 나는 주먹만 내!!!
    return 'bawi'
```

#### 예제4: `가위`, `바위`, `보`를 돌아가면서 내는 플레이어

```python
hands = ['gawi', 'bawi', 'bo']
my_hand = 0

def show_me_the_hand(records):
    my_hand = (my_hand + 1) % 3
    return hands[my_hand]
```


#### 테스트 방법(pseudo code):

* 이 코드는 참가자의 이해를 돕기위한 pseudo code입니다.
* **8/14변경** 변경된 부분에 주의하세요!

```python
import player1
import player2

r1 = []
r2 = []
for i in range(1000):
    h1 = player1.show_me_the_hand(r2)
    h2 = player2.show_me_the_hand(r1)
    if h1 == h2:
        print('match %d of 1000: tie' % i)
        r = 0
    elif (h1 == 'gawi' and h2 == 'bo') or (h1 == 'bawi' and h2 == 'gawi') or (h1 == 'bo' and h2 == 'bawi'):
        print('match %d of 1000: p1 win' % i)
        r = 1
    else:
        print('match %d of 1000: p2 win' % i)
        r = -1
    r1.insert(0, (h1, r))  # NOTE: 어제와 달라졌습니다!!!
    r2.insert(0, (h2, -r))  # NOTE: 어제와 달라졌습니다!!!
    # blah blah ...
```

---

[CODING BATTLE 가위바위보! - 못다한 이야기](http://tech.kakao.com/2016/08/19/gawibawibo/) 참조
