# homework1_SW
명령어 정리, 실습 for README.md


# 명령어 
## Ⅰ쉘 스크립트 관련
### getopt 명령어 & getopts 명령어
**1. 역사**

  원래 **getopt**에는 몇가지 문제가 있었다. 
  인수의 공백이나 쉘 메타 문자를 처리 할 수 없었고, 오류 메세지 출력을 비활성화 하는 기능이 없었다.
  
  **getopts**는 1986년 *Unix SVR3*와 함께 제공되는 *Bourne shell*에서 처음 도입되었다. 
  셸 자체 변수를 사용하여 현재 및 인수 위치, 
  *POTIND* 와 *OPTARG*의 위치를 추적하고 셸 변수에 옵션 이름을 반환하였다.
  *Bourne Shell* 초기 버전에는 **getopts**가 없었다.
  
  1995년, **getopts**는 *Single UNIX Specification version 1 / X/Open Portability Guidelines Issue 4dp* 포함되었다. 그 결과, **getopts**는 지금 *Bourne shell, KornShell, Almquist shell, Bash* 그리고 *Zsh*을 포함한 쉘에서 사용가능하다.
  
  **getopts** 명령어는 *IBM i* 운영체제로 포트되었다.
 
   **getopt**의 현대적인 사용법은 *util-linux* 의 향상된 구현으로 인해 부분적으로 활성화 되었다.
   BSD기반으로 한 **getopt** 버전은 **getopt** 대한 두 가지 불만 사항을 수정했을 뿐만 아니라 *GNU*스타일의 긴 옵션과 옵션에 대한 선택적 인수를 구문 분석하는 기능을 도입했다. 그러나 다양한 *BSD* 배포판은 여전히 이전 구현을 고수했다.
 
  
**2.향상**

  * **다양한 getopts**
  
    2004년 봄 (Solaris 10 beta 개발), **getopt()** 에 대한 libc 구현이 long 옵션을 지원하도록 향상되었다. 결과적으로, 새로운 기능은 *Bourne Shell* 의 기본 제공 명령에서도 사용할 수 있었다.  이것은 *long aliases*을 지정 하는 *optstring*의 괄호로 묶인 접미사에 의해 트리거된다.
    

*KornShell* 과 *Zsh*는 모두 long 인수에 대한 확장을 가지고 있다. *KornShell* 은 *Solaris*에서와 같이 정의되고, *Zsh*는 별도의 *zparseopts* 명령을 통해 구현된다.
*KornShell* 은 - 대신 + 으로 시작하는 옵션에 대해 optstring확장을 추가로 구현한다.


  * **Linux getopt**

  **getopts**에 대한 대안은 **getop**의 향상된 Linux 버전(external command line program)이다. 
  
  **getopt**의 Linux 고급 버전은 **getopts**의 추가 안전성과 고급 기능들이 있다. 예를 들어, *--help* 등의 long option name을 지원한다. 그리고 옵션이 모든 피연산자 앞에 나타날 필요가 없다. 예를 들어, *command operand1 operand2 -a operand3 -b*. 이것은 **getopt**의 Linux 확장 버전에서는 허용되지만 , **getopts**는 작동되지 않는다.
  또한, tcsh와 POSIX 같은 쉘과 선택적 인수를 위해 이스케이트 메타문자를 지원한다. 


**3. 비교**

|Feature\Program|POSIX getopts|Solaris getopts|Unix/BSD getopt|Linux getopt|
|--------|-------|--------|-------|-------|
|쉬운 parsing을 위한 분할 옵션|예|예|예|예|
|오류 메시지 억제 허용|예|예|아니요|예|
|공백 및 메타 문자로 안전|예|예|아니요|예|
|피연산자를 옵션과 혼합할 수 있습니다.|아니요|예|아니요|예|
|긴 옵션 지원|Emulation|예|아니요|예|
|선택적 인수|오류|오류|아니요|예|

***Emulation: 모방*


**4. Option string 과 $OPTIND이용 예**
  
  ![getopts개념](https://user-images.githubusercontent.com/94296757/142472068-16a55660-a336-4511-b22c-cd15d555e2b0.png)

  
  **getopt**은 **getopts builtin** 명령과 비슷한데 /usr/bin/getopt 에 위치한 외부 명령이다.
  이 명령은 기본적으로 short, long 옵션을 모두 지원한다. 옵션 인수를 가질 경우 **":"** 문자를 사용하는 것은 getopts builtin 명령과 동일하다.
  
  
  ```
#!/bin/bash

set -- -abc hello world

echo $OPTIND

getopts abc opt "$@"
echo $opt, $OPTIND

getopts abc opt #args부분을 생략하면  default값은 "$@"
echo $opt, $OPTIND

getopts abc opt
echo $opt, $OPTIND

shift $(( OPTIND - 1 ))
echo "$@"
```

<출력 결과>

![getopts1](https://user-images.githubusercontent.com/94296757/142471448-a27af50e-9ccf-4f5c-bedf-23dd28da9b31.GIF)

  
  **5.getopts이용 예** 

*loop문 이용해 처리*
```
#!/bin/bash

while getopts "a:bc" opt; do
  case $opt in
    a)
      echo >&2 "-a was triggered!, OPTARG: $OPTARG"
      ;;
    b)
      echo >&2 "-b was triggered!"
      ;;
    c)
      echo >&2 "-c was triggered!"
      ;;
  esac
done

shift $(( OPTIND - 1 ))
echo "$@"
```

![getopts2](https://user-images.githubusercontent.com/94296757/142473208-eec44634-bb32-44a4-b547-821bdb5385ad.GIF)




  
**6. getopt 이용 예**
  
  ```   
  # short 옵션 지정은 -o 옵션으로 한다.
  # ':'에 따라 옵션 -a는 옵션 인수는 갖는다.
  getopt -o a:bc 
  
  
 # long 옵션 지정은 -l 옵션으로 하고 옵션명은 ',' 로 구분한다.
# ':' 에 따라서 옵션 --path 와 --name 은 옵션 인수를 갖는다.
# 명령 라인에서 옵션 인수 사용은 "--name foo" 또는 "--name=foo" 두 가지 모두 가능하다.
getopt -l help,path:,name: 

# 명령 마지막에는 -- 와 함께 "$@" 를 붙인다.
getopt -o a:bc -l help,path:,name: -- "$@"
  ```
  
  *위를 토대로 bash 파일을 작성해봄 ↓*
  
  * getopt
   
    **↓설정하지 않은 옵션이 사용되거나 옵션 인수가 빠질 경우 오류메시지를 출력해준다.↓**
  
   ```   
#!/bin/bash
options=$( getopt -o a:bc -l help,path:,name: -- "$@" )
echo "$options"
   ```
  
  <출력결과> 
    
 ![getopt1](https://user-images.githubusercontent.com/94296757/142468122-922896d6-c55d-4161-b17a-a475f625acef.GIF)

   **↓getopt 명령의 특징은 사용자가 입력한 옵션들을 case 문에서 사용하기 좋게 정렬해준다.↓**

```
#!/bin/bash

options=$( getopt -o a:bc -l help,path:,name: -- "$@" )
echo "$options"
```

  <출력결과> 
  
  ![getopt2](https://user-images.githubusercontent.com/94296757/142469079-cbb4a306-a5a7-4a75-b5e1-92b47819a120.GIF)


<출력 결과 설명>

첫번째 명령 
1. -a123 옵션이 -a '123' 로 분리.
2. -bc 옵션이 -b -c 로 분리.
3.  3. 옵션에 해당하지 않는 hello.c 는 '--' 뒤로 이동
    
두번째 명령    
1. --path=/usr/bin 옵션이 --path '/usr/bin' 로 분리
2.'--' 는 항상 끝부분에 붙는다.   
    
세번째 명령 

1.'--' ( end of options ) 처리도 해준다.   
    
 
 
 
 * getopt builtin
  
```
#!/bin/bash

while getopts "a:bc" opt; do
  case $opt in
    a)
      echo >&2 "-a was triggered!, OPTARG: $OPTARG"
      ;;
    b)
      echo >&2 "-b was triggered!"
      ;;
    c)
      echo >&2 "-c was triggered!"
      ;;
  esac
done

shift $(( OPTIND - 1 ))
echo ------------------
echo "$@"

```

<출력 결과>

![getopts_buil1](https://user-images.githubusercontent.com/94296757/142470907-64b3acc8-20d9-4230-9dbf-c3925316f167.GIF)




## Ⅱ리눅스 명령어 관련
### ⅰ)sed 명령어


### ⅱ)awk 명령어




# 2. 번호 없는 리스트 작성
* 리스트1
  - 리스트2
    + 리스트3
    
# 3. 번호 있는 리스트 작성
1. 리스트1
2. 리스트2
3. 리스트3 

# 4. 이텔릭체(기울어진 글씨) 작성
*텍스트*

# 5. 굵은 글씨 작성
**텍스트**

# 6. 인용
> 인용1

> 인용2
>> 인용안의 인용

# 7. 수평선 넣기

---
  
# 8. 링크 달기
(1) 인라인 링크  

[블로그 주소](https://lsh424.tistory.com/)

(2) 참조 링크  

[블로그 주소][blog]

[blog]: https://lsh424.tistory.com/

# 9. 이미지 추가하기
![이탈리아 포지타노](https://user-images.githubusercontent.com/31477658/85016059-f962aa80-b1a3-11ea-8c91-dacba2666b78.jpeg)

### 이미지 사이즈 조절
<img src="https://user-images.githubusercontent.com/31477658/85016059-f962aa80-b1a3-11ea-8c91-dacba2666b78.jpeg"  width="700" height="370">

### 이미지 파일로 추가하기
<img src="Capri_Island.jpeg" width="700">

# 10. 코드블럭 추가하기

```swift
public struct CGSize {
  public var width: CGFloat
  public var heigth: CGFloat
  ...
}
```

# etc

**텍스트 굵게**  
~~텍스트 취소선~~

### [개행]  

스페이스바를 통한 문장개행  
스페이스바를 통한 문장개행  

br태그를 사용한 문장개행
<br>
<br>
br태그를 사용한 문장개행


### [체크박스]

다음과 같이 체크박스를 표현 할 수 있습니다. 
* [x] 체크박스
* [ ] 빈 체크박스
* [ ] 빈 체크박스

### [이모지 넣기]
❤️💜💙🤍

### [표 넣기]
|왼쪽 정렬|가운데 정렬|오른쪽 정렬| 
|:---|:---:|---:| 
|내용1|내용2|내용3| 
|내용1|내용2|내용3| 

<br>

### 정리내용
[정리 내용 보기](https://lsh424.tistory.com/37)
