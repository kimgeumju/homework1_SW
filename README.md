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

>loop문 이용해 처리
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
   
   >↓설정하지 않은 옵션이 사용되거나 옵션 인수가 빠질 경우 오류메시지를 출력해준다.↓
  
   ```   
#!/bin/bash
options=$( getopt -o a:bc -l help,path:,name: -- "$@" )
echo "$options"
   ```
  
  <출력결과> 
    
 ![getopt1](https://user-images.githubusercontent.com/94296757/142468122-922896d6-c55d-4161-b17a-a475f625acef.GIF)

   >↓getopt 명령의 특징은 사용자가 입력한 옵션들을 case 문에서 사용하기 좋게 정렬해준다.↓

```
#!/bin/bash

options=$( getopt -o a:bc -l help,path:,name: -- "$@" )
echo "$options"
```

  <출력결과> 
  
  ![getopt2](https://user-images.githubusercontent.com/94296757/142469079-cbb4a306-a5a7-4a75-b5e1-92b47819a120.GIF)


<출력 결과 설명>

>첫번째 명령 
>> 1. -a123 옵션이 -a '123' 로 분리.
>> 2. -bc 옵션이 -b -c 로 분리.
>> 3. 옵션에 해당하지 않는 hello.c 는 '--' 뒤로 이동
    
>두번째 명령    
>> --path=/usr/bin 옵션이 --path '/usr/bin' 로 분리
>>'--' 는 항상 끝부분에 붙는다.   
    
>세번째 명령 
>>'--' ( end of options ) 처리도 해준다.   
    
 
 
 
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

**1. 소개**

**sed**(stream editor)는 유닉스에서 텍스트를 분해하거나 변환하기 위한 프로그램이다. sed는 벨 연구소의 Lee E. McMahon이 1973년부터 1974년까지 개발하였고, 현재 유닉스 등의 여러가지 운영 체제에서 사용 가능하다.


|sed| |
|---|---|
|paradigm| scripting|
|Designed| Lee E.McMahon|
|첫 등장 |1974 ; 47 years ago|
|구현 언어 | C |
|웹 사이트 | [GNU sed](https://www.gnu.org/software/sed/) |
|Influenced | ed |
|Incluenced|Perl, AWK|

**2. 역사**

유닉스 7 버전이 처음 등장한, sed는 데이터 파일의 명령 줄 처리를 위해 개발된 초기 유닉스 명령어들 가운데 하나이다. 대중적인 *grep* 명령어의 뒤를 자연스럽게 이을 정도로 발전하였다. 원래는 치환을 목적으로 한 grep(g/re/p)의 상이형인 *"g/re/s"*이었다. 개별 명령어를 위해 추가적인 특수 목적의 프로그램들이 등장할 것으로 예견하면서 맥마흔은 범용 목적의 라인 지향 스트림 편집기를 작성하였으며, 이것이 sed로 되었다.

**3. sed 개념**

**SED(Stream Editor)**
**SED는 Stream Editor의 약자로 sed라는 명령어로 원본 텍스트 파일을 편집하는 유용한 명령어이다.** 리눅스의 editor라하면 vi 편집기가 대표적인데, 여러분들이 vi편집기로 편집할때는 vi filename의 명령을 이용해서 파일을 열고 난 이후에 각종 vi 명령어를 통해서 이리 저리 움직여 추가, 삭제, 변경 등의 편집을 하게 된다. 그리고 작업이 다 끝나게 되면 저장 후 나가게 된다. 이때 원래의 파일을 변경하여 저장하기 때문에 원본은 변경된다. 그리고 vi는 실시간 저장할 수가 있다. 여기서 sed는 vi 편집기와는 다르게 이런 차이점이 있다.


1. sed와 vi가 다른 점은 sed는 명령어 형태로 편집이 되며 vi처럼 실시간 편집이 아니다.

2. **원본을 건드리지 않고 편집**하기 때문에 작업이 완료되었어도 기본적으로 원본에는 전혀 영향이 없다.*(단, sed옵션에서 -i 옵션을 지정한다면 원본을 바꾸게 된다.)* 그래서 내부적으로 특수한 저장 공간인 버퍼를 사용한다. 두 가지 버퍼는 **패턴 버퍼**(패턴 스페이스라고도 합니다)와 **홀드 버퍼**(홀드 스페이스라고도 합니다)입니다.

 

아래의 그림을 보면 sed는 **InputStream**으로 파일의 내용을 가져온다. 그리고 난 후에 패턴 버퍼에 그 내용을 담고 있으며 데이터의 변형과 추가를 위해 다시 임시 버퍼를 사용하게 되는데, 그게 **홀드 버퍼**이다. 그리고 작업이 전부 완료되었다면 패턴 버퍼에 그 내용이 담기는데, 그 내용을 **OutputStream**으로 보내주게 되면 비로소 원하는 결과가 출력되는것이다. 기본적으로 OutputStream은 콜솔화면인 stdout이다. 우리는 그냥 sed가 내부적으로 2개의 버퍼를 가지고 있다고 보면 된다. 



### ⅱ)awk 명령어

## Ⅲ참고 사이트

[Getopts](https://en.wikipedia.org/wiki/Getopts "Getopts")
[Getopts2](https://mug896.github.io/bash-shell/getopts.html)
[sed](https://en.wikipedia.org/wiki/Sed)
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
