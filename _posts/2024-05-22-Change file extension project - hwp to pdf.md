---
layout: single
title:  "Change file extension project - hwp to pdf"
categories: Project
tag: []
author_profile: true
toc: true
toc_label: 목차
toc_icon: "fas fa-list"

---

<br/>





# ◆바이너리 데이터

hwp 파일을 pdf파일로 변경하는 코드로 구현하려고 했다.

프론트는 react로 구현하고 백엔드는 srping boot로 진행하려고 한다.

하지만 파일을 주고받을 때에는 바이너리 데이터로 변경해서 주고 받아야 된다고 한다.(용량이 크기 때문에 효율적인 파일 처리를 위해)

<br>

그래서 react 파일의 용량을 체크하고 spring으로 넘어가면 hwp파일을 pdf파일로 변경한뒤 pdf파일을 바이너리 데이터로 변경하여 react로 넘겨줄려고 한다.

<br>

#### react에서 바이너리 데이터로 받는방법은 여러가지가 있다.

1. base64 인코딩하여 전송=> 데이터 크기가 크지않을 때 효과적인 방법 ( js-base64라이브러리 사용 )
2. ArrayBuffer=> 바이너리 데이터를 효율적으로 다룰 수 있는 자료구조로써 이후 blob객체로 변환하여 사용할 수 있다.

<br>

spring에서 ArrayBuffer로 넘어가기 때문에 react받아서 blob객체로 변환 시켜서 다운 받을 수 있도록 구현하는게 맞다고 생각했다.

**ArrayBuffer로 받아 base64 문자열로 변환해도 blob 객체로 변환**해야 되기 때문에 더 간결한 방법인 2번째를 사용하기로 함..

*나는 인코딩 디코딩이 파일마다 다르게 사용되기 때문에 중간에 base64를 활용하여 파일에 맞게 변경해주어야 된다고 생각했지만... 애초에 spring에서 해당 파일로 변경하여 바이너리데이터로 전송해주기 때문에 이를 파일로 다운로드 받을 수 있도록 하는게 옮은 방식이란걸 깨달음.... 흠*

<br>

> 바이너리 데이터가 무엇인가..???
>
> 0과1의 시퀀스로 구성된 이진 형식으로 표현된 데이터를 말합니다. 컴퓨터가 내부적으로 데이터를 저장하고 처리하는 방식에 더 가까운 보다 간결하고 낮은 수준의 표현이기 때문에 컴퓨터에서 정보를 효율적으로 저장하가 처리하는데 자주 사용됩니다.

<br>





# ◆라이브러리

 hwp파일을 pdf로 변경해야되는데 react에서 spring으로 파일을 multipiles을 활용해서 전달을 하면 이를 spring에서 hwp파일을 읽고 pdf로변환시켜줘야 하는데 라이브러리를 찾아서 활용해보고자 했다.

<br>

#### 라이브러리

1. Aspose.Words for Java 

   ==> 상용 라이선스가 필요 ... 나는 돈이 없기 때문에 패스

2. Apache PDFBox 

   ==> pdf파일 생성하는 라이브러리 오픈소스 활용할수 있다

3. Apache.poi

   ==>ms office파일 포맷을 처리한다.

4. hwplib

​	==> 오픈 소스 라이브러리,  apache-2.0까지는 무료로 사용할 수 있다.

<br>



문제발생!!

처음에는 Apach poi를 사용하고자 했는데 오류가 발생함...

```
org.apache.poi.openxml4j.exceptions.OLE2NotOfficeXmlFileException: The supplied data appears to be in the OLE2 Format. You are calling the part of POI that deals with OOXML (Office Open XML) Documents. You need to call a different part of POI to process this data (eg HSSF instead of XSSF)
```

==>이는 Apache POI 라이브러리에서 발생한 오류로, 입력 데이터가 OLE2 형식이지만 OOXML(Office Open XML) 형식을 처리하려고 했기 때문에 발생

<br>



**POI 라이브러리는 주로 MS Office 문서 형식(doc, xls, ppt 등)을 처리**

**HWP 파일 처리 라이브러리로는 Argos HWP Parser, hwplib 등**

**결론은 hwplib라이브러리로 hwp파일의 내용을 읽어오고 pdfBox을 활용하여 pdf파일을 생성하여 hwp파일 내의 내용을 옮겨줄것이댜!**

<br>



**해당 파일을 multifile로 받아서 db에 저장을 하지않고 바로 반환하고 싶은게 이번 프로젝트에 원하는 방식이다..**

하지만 대부분 구글링 했을 때 파일을 db에저장한 상태에서 파일을 불러오는 방식을 통해 해당 파일의 경로를 쉽게 가져올수 있는 방식밖에 없었다... 

그래서 **File 객체의 .createTempFile() function을 활용**하여 임시파일을 생성하고 바로 사용이 끝나면 삭제처리하는 방식을 선택하였다..(임시 파일을 생성함으로써 파일의 경로를 취득할 수 있게됨)

<br>



hwp파일을 pdf로 변환을 성공했다.. 이제 운영체제와 한글의 인코딩, 사진과 표까지 변경가능하도록 수정을 해볼것이다!!!

<br>



pdfbox 라이브러리 :<a href="https://www.tutorialspoint.com/pdfbox/pdfbox_adding_text.htm">www.tutorialspoint.com/pdfbox/pdfbox_adding_text.htm<a>

hwp파일 구조 : <a href="https://github.com/neolord0/hwplib/wiki">https://github.com/neolord0/hwplib/wiki<a>

hwplib : <a href="https://github.com/neolord0/hwplib">https://github.com/neolord0/hwplib<a>
