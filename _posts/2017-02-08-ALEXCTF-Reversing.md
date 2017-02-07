---
layout: post
title: "ALEXCTF Reversing Writeup"
description: "ALEXCTF Writeup"
data: "2017-02-08"
tags: ["CTF", "Writeup"]
share: true
---

# RE1: Gifted(50)

```sh
wget https://ctf.oddcoder.com/files/8b0078584e1bc0527f131d15ea29361d/gifted
chmod 776 gifted
strings gifted
```

다운 받아서 strings를 돌리면 그 중에 있다.

### strings

strings는 바이너리 안에 들어있는 printable한 바이트 배열을 인식해서 모조리 추출해준다. 주로 C, C++언어로 만들어진 프로그램 에서는 하드 코딩된(소스에 들어있는) 문자열들이 나오게 된다.

#  RE2: C++ is awesome(100)

#### They say C++ is complex, prove them wrong!

```sh
wget https://ctf.oddcoder.com/files/2248b2ff8732c1c9b4ebdd6cc687957d/re2
```

필자의 경우 라이브러리 문제로 실행이 안되었다. 아마 분명 같은 문제로 실행이 안되는 환경이 상당히 있을 것이라 생각되는데, 리눅스를 최신버전으로 업데이트 하는 것으로 해결했다.

```python
q = [0x24, 0x36, 0x65, 0x27, 0x26, 0x2d, 0x01, 0x03, 0x00, 0x0d, 0x56, 0x01,
0x03, 0x65, 0x03, 0x2d, 0x16, 0x02, 0x15, 0x03, 0x65, 0x00, 0x29, 0x44,
0x44, 0x01, 0x44, 0x2B]

s = '\x4C\x33\x74\x5F\x4D\x45\x5F\x54\x33\x6C\x6C\x5F\x59\x30\x75\x5F\x53\x30\x6D\x33\x74\x68\x31\x6E\x67\x5F\x31\x6D\x70\x30\x72\x74\x61\x6E\x74\x5F\x41\x5F\x7B\x46\x4C\x34\x47\x7D\x5F\x57\x30\x6E\x74\x5F\x62\x33\x5F\x33\x58\x34\x63\x74\x6C\x79\x5F\x74\x68\x34\x74\x5F\x33\x34\x35\x79\x5F\x74\x30\x5F\x63\x34\x70\x74\x75\x72\x33\x5F\x48\x30\x77\x65\x76\x33\x72\x5F\x31\x54\x5F\x77\x31\x6C\x6C\x5F\x62\x33\x5F\x43\x30\x30\x6C\x5F\x31\x46\x5F\x59\x30\x75\x5F\x67\x30\x74\x5F\x31\x74\x00\x42\x65\x74\x74\x65\x72\x20\x6C\x75\x63\x6B\x20\x6E\x65\x78\x74\x20\x74\x69\x6D\x65\x0A\x00\x00\x59\x6F\x75\x20\x73\x68\x6F\x75\x6C\x64\x20\x68\x61\x76\x65\x20\x74\x68\x65\x20\x66\x6C\x61\x67\x20\x62\x79\x20\x6E\x6F\x77\x0A\x00\x55\x73\x61\x67\x65\x3A\x20\x00\x20\x66\x6C\x61\x67\x0A\x00\x01\x1B\x03\x3B\x74\x00\x00\x00\x0D\x00\x00\x00\x68\xFA\xFF\xFF\xC0\x00\x00\x00\x48\xFB\xFF\xFF\x90\x00\x00\x00\x3E\xFC\xFF\xFF\xE8\x00\x00\x00\x5B\xFC\xFF\xFF\x08'
print 's: ',s

r = ''.join([s[c] for c in q])

print 'r: ', r
print 'len:',len(r)
```

위는 문제풀이 도중 작성한 스크립트이다. IDA나 GDB로 분석해보면 하드코딩된 문자열 중에(앞서 말한 strings명령어로도 볼 수 있다.) 실제로 쓰이지 않는 이상한 문자열이 들어있다.

참가자를 조롱하는 듯한 문자열이지만 아마 이것이 쓰일 거라고 생각해 분석해보면, 그 문자열의 특정 순서에 있는 문자들을 조합해서 flag가 완성된다.

스크립트에서 s는 참조된 바이트어레이(조롱하는 문자열 ㅂㄷㅂㄷ)이고 q는 분석으로 얻어낸 각 문자의 인덱스이다. 이것을 조합하면 FLAG를 얻을 수 있다.

![re2_1](/images/alex_re2_1.png)

위에서 v7은 for문 안에서 우리가 입력한 플래그의 각 문자열이 된다. v13은 0에서 부터 1씩 점점 증가하게 된다.

off_6020A0은 조롱하는 문자열이 있는 곳이며, dword_6020C0은 그 문자열에서 참조할 인덱스가 차례대로 들어있는 곳이다.

# RE3: Catalyst System(150)

#### CEO of catalyst systems decided to build his own log in system from scratch, he thought that it is so safe that no one can fool around with him!

``` sh
wget https://ctf.oddcoder.com/files/6802e8faa75f51dbdd32b1d8163ac862/catalyst
```

아이디와 패스워드를 요구하는데,  맞추면 플래그를 주는 프로그램이다. 참고로 플래그 자체가 아이디와 패스워드를 이용해서 계산되는 구조이기 때문에 아이디와 패스워드 없이 플래그를 바이너리에서 뽑아내는 행위는 불가능하다.

우선 실행시키면 "Loading...."이 뜨면서 우리를 기다리게 한다. 필자는 IDA로 열어서 sleep을 호출하는 부분을 nop패치했다.

분석해보면, 아이디는 12바이트이다. 이 12바이트는 3개의 정수(4바이트 * 3)로 부터 얻어낼 수 있다. 3개의 정수는 3항 연립방정식을 풀면 얻을 수 있다.

![re3_1](/images/alex_re3_1.png)

$x+y+z=1550207830$

$3x+y+3z=12465522610$

$yz=3651346623716053780$

이므로

$x = 1635017059, y = 1953724780, z = 1868915551$

```python
from struct import pack
print ''.join(pack('I',val) for val in [1635017059,1953724780,1868915551])
```

```sh
catalyst_ceo
```

필자는 Wolframalpha의 힘을빌려 풀었다. 그리고 아이디는 catalyst_ceo가 된다.

이제 비밀번호를 알아내자

![re3_2](/images/alex_re3_2.png)

a1은 아까 알아냈던 아이디이다. 즉, 아까 알아낸 3개의 정수를 더한 값을 rand의 시드로 주고 연속적으로 랜덤값을 추출한 후에 어떤 값들을 더하고 나서, 아이디때의 경우와 같이 숫자들을 스트링으로 바꾸어 주면 비밀번호가 나올 것이다. 우선, C의 rand()함수를 써야 하므로 간단한 C코딩을 하자

```C
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char** argv)
{
        if(argc < 2) {
                return 0;
        }
        int seed = atoi(argv[1]);
        srand(seed);
        for(int i=0;i<10;++i) {
                printf("%d\n", rand());
        }
        return 0;
}
```

첫번째 인자를 랜덤 시드로 주고 10개의 랜덤 값을 차례대로 뽑아주는 프로그램이다.

```python
username = 'catalyst_ceo'
a = 1635017059
b = 1953724780
c = 1868915551

seed = a+b+c

print 'seed: ', seed

import os
from struct import pack, unpack

rands = os.popen('./calc ' + str(seed)).read().split('\n')[:-1]
rands = map(lambda x: int(x), rands)
pOffs = [0x55eb052a, 0xef76c39, 0xcc1e2d64, 0xc7b6c6f5,
0x26941bfa, 638382323, 282381039, -966334428, -58112612, 605226810]
print rands
pl = []
for i in range(10) :
        val = rands[i] + pOffs[i]
        val = val & 0xffffffff
        pl.append(pack('I',val))
password = ''.join(pl)

print 'u: ', username
print 'p: ', password
```

위는 프로그램을 분석해서 얻어낸, 랜덤으로 나온 값들에 차례대로 더할 값들을 알아내어 만든 스크립트이다.

방금 C로 만든 calc라는 프로그램으로 10개의 랜덤값을 알아내고, 거기에 바이너리에서 추출한 10개의 값을 각각 더해서 스트링으로 변환하면 

```
u:  catalyst_ceo
p:  sLSVpQ4vK3cGWyW86AiZhggwLHBjmx9CRspVGggj
```

위와 같은 결과가 나온다.

# RE4: unVM me(250)

#### If I tell you what version of python I used .. where is the fun in that?

```sh
wget https://ctf.oddcoder.com/files/20f1959c34dae4fc68fe2ed0c2b0acf1/unvm_me.pyc
```

pyc파일을 준다. 인터넷에서 python decompiler를 다운받아, 그 안에 pyc파일을 넣으면 원래의 py파일로 복원해 준다.

```python
# Embedded file name: unvm_me.py
import md5
md5s = [174282896860968005525213562254350376167L,
 137092044126081477479435678296496849608L,
 126300127609096051658061491018211963916L,
 314989972419727999226545215739316729360L,
 256525866025901597224592941642385934114L,
 115141138810151571209618282728408211053L,
 8705973470942652577929336993839061582L,
 256697681645515528548061291580728800189L,
 39818552652170274340851144295913091599L,
 65313561977812018046200997898904313350L,
 230909080238053318105407334248228870753L,
 196125799557195268866757688147870815374L,
 74874145132345503095307276614727915885L]
print 'Can you turn me back to python ? ...'
flag = raw_input('well as you wish.. what is the flag: ')
if len(flag) > 69:
    print 'nice try'
    exit()
if len(flag) % 5 != 0:
    print 'nice try'
    exit()
for i in range(0, len(flag), 5):
    s = flag[i:i + 5]
    if int('0x' + md5.new(s).hexdigest(), 16) != md5s[i / 5]:
        print 'nice try'
        exit()

print 'Congratz now you have the flag'
```

복원된 py파일의 내용은 이렇다.

맨 위의 숫자 나열의 숫자들을 각각 MD5 decrypt하기만 하면 Flag가 나온다.

MD5 decrypt도 구글링 하면 아주 많이 나온다. MD5는 레인보우 테이블(무차별 공격을 미리 해놓고 잘 저장해 놓은 사전파일)이 꽤 많이 만들어져있기 때문에 짧은 문자의 Cipher는 왠만하면 모두 복호화 할 수 있다. 위 숫자들의 경우 모두 복호화하면 5자리밖에 안되는 문자열이 나온다.

# RE5: packed movement(350)

#### Being said that move instruction is enough to build a complete computer, anyway move on while you can.

```sh
wget https://ctf.oddcoder.com/files/d822c114fcd8bfdcfc26e40414361fb2/move
```

```sh
chmod 776 move
strings move | grep UPX
```

strings 명령어로 확인해 보면 UPX라는 문자열이 보이는 데, 그것은 이 프로그램이 UPX로 패킹되있음을 의미한다. UPX로 프로그램을 패킹하게 되면, 프로그램을 압축해서 저장하고, 프로그램을 실행시킬 때는 그 압축된 것을 압축해제 하는 코드가 실행되도록 만든다. 하지만 압축되있는 프로그램을 눈으로 보고 분석할 수는 없으므로 언패킹을 해야 한다. UPX는 언패킹이 아주 쉽다.

```sh
upx -d move
```

위 명령어 한 줄이면 언패킹이 된다. upx가 없으면 다운받길.

gdb로 열면 코드가 죄다 mov밖에 없다. 문제 설명에도 나와있듯이 mov는 튜링 완전하다. 즉, jmp나 cmp, add, sub, 등등 모두 mov명령어만 있어도 대체할 수 있다는 뜻이다. 자세한 원리는 잘 모르겠지만 이 프로그램은 그렇게 기계어 레벨에서도 사람이 잘 알수없도록 난독화되어있다.

그래서 분석을 포기한다! (~~난 인간을 그만두겠다! 죠...읍..으..ㅂ!~~)

분명 이렇게 분석이 어려운데 FLAG가 인코딩이나 암호화까지 되어있지 않으리라 굳게 믿고,

ALEXCTF{ 의 A를 생으로 찾자. A는 0x41이다. L은 0x4c이고...

```sh
objdump -d move | grep 0x41 # A
objdump -d move | grep 0x4c # L
objdump -d move | grep 0x45 # E
objdump -d move | grep 0x58 # X
objdump -d move | grep 0x43 # C
objdump -d move | grep 0x54 # T
objdump -d move | grep 0x46 # F
objdump -d move | grep 0x7b # {
```

찾아보면 전부 나온다...

``` sh
80493db:	c7 05 68 20 06 08 41 	movl   $0x41,0x8062068
```

대략 이런 결과가 한줄 씩 나올 것인데 , 0x8062068이라는 숫자는 항상 나온다.

```sh
objdump -d move | grep 0x8062068
```

그러면 ALEXCTF{ 뒤의 나머지 글자들을 모두 알 수 있다.