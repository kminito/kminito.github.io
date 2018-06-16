---
layout: post
title: "Excel VBA - 파일 첨부하여 메일 자동 발송"
category: vba
---

### 목표
```
특정 PDF 파일을 지정된 폴더로 복사 후 메일에 첨부하여 아웃룩으로 자동 발송하는 매크로 만들기
```
제가 실제 업무에서 이용하고 있는 매크로입니다.  

VBA로 만든 기능은 **pdf 파일 형식의 구매주문서(PO)를 복사하여 회사 네트워크
 폴더에 저장 후, 메일에 첨부하여 담당자에게 발송** 하는 것입니다. 전체 코드는 맨 아래에 있습니다.  





#### 규칙
- 아래 규칙은 제가 만든 매크로에만 해당 되는 것들입니다.
<pre><code>
<b> 규칙</b>
  1. D3 셀에 PDF 파일 이름을 입력하며, 파일 이름은 반드시 '날짜+"PO"+PO번호 형식이 되어야 함'
    - 파일 이름에서 PO 번호만 추출하여 메일 제목 및 내용에 들어가도록 해놨기 때문임.
  2. D5 셀에 수신자의 메일 주소를 입력하며, 여러명일 경우 세미콜론(;)으로 구분
  3. D9 셀은 대상 PDF 파일을 복사하여 붙여넣을 주소가 들어감
  4. 대상 PDF 파일은 엑셀 매크로 파일과 같은 폴더에 있어야 함.

</code></pre>
![엑셀 스크린샷]({{ site.url }}/assets/VBA/vba_pdf_mail_1.jpg)



___
___


## 순서
#### 1) PDF 파일의 주소를 불러오기
 아래 코드를 통해, 엑셀 파일와 같은 위치에 있는 PDF 파일의 주소를 불러와 `Current_Path` 변수에 넣습니다. PDF 파일의 이름은 엑셀 시트의 D3 셀에 직접 입력이 되어야 하며, D3 셀에 입력된 값에 따라 D6, D7 셀의 내용도 함께 바뀌도록 셀에 수식이 작성되어 있습니다.

  ```python
Dim Current_Path As String
Dim File_Name As String

Current_Path = ThisWorkbook.Path        # 현재 엑셀 파일의 위치를 Current_Path 변수에 넣음
File_Name = Cells(3, 4).Value & ".pdf"  # D3셀의 값에 '.pdf'를 붙여 File_Name에 넣음
''If Len(Cells(3, 4).Value) <> 20 Then  # D3셀의 글자수 확인하여 20글자가 아닐 경우 에러 실행
''    GoTo ErrorMessage'
''End If

''On Error GoTo ErrorMessage

  ```

#### 2) PDF 파일을 복사하기  
`FileCopy` 는 파일을 복사하는 함수입니다. `FileCopy source, destination`과 같은 형식으로 사용이 됩니다. 아래 코드는 `Star` 변수에 할당된 주소의 파일을 `Desti` 변수에 할당된 주소 및 파일명으로 복사합니다.

```
Dim Star As String
Dim Desti As String

Star = ThisWorkbook.Path & "\" & File_Name  # 복사될 파일의 원본 위치+파일명을 Star에 넣음
Desti = Cells(9, 4).Value & "\" & File_Name # 파일이 복사 및 저장될 위치 + 파일명을 Desti에 넣음

FileCopy Star, Desti    # 파일 복사 실행

MsgBox "파일 복사 완료" & Chr(10) & Desti    # 파일이 복사된 위치 표시
```



#### 2) PDF 파일을 첨부하여 메일 발송하기
원본 PDF 파일을 아웃룩 메일에 첨부하여, D5 셀에 입력된 수신자로
메일을 발송하는 코드입니다.

  ```python
If MsgBox("메일을 전달하겠습니까?", vbQuestion + vbYesNo, "자동 발송") = vbYes Then
                                              # 메일 보낼 것인지 물음
    Dim olApp As Outlook.Application
    Dim olMail As MailItem
    Dim sAttFile As String

    Set olApp = New Outlook.Application           
    Set olMail = olApp.CreateItem(olMailItem)         

        With olMail                           # 작성할 메일의 내용을 지정
            .To = Cells(5, 4).Value           # 메일 수신자 : D5 셀 값
            .Subject = Cells(6, 4).Value      # 메일 제목 : D6 셀 값

            .Attachments.Add Star             # 첨부파일 : Star 변수에 저장된 위치의 파일
            .Body = Cells(7, 4).Value         # 메일 내용 : D7 셀 값
            .Send                             # 메일 발송
        End With

        MsgBox ("메일 발송 완료")

Exit Sub
ErrorMessage:
    MsgBox ("파일 이름 오류 - 다시 시도하세요")

End If
End Sub

''코드 끝!
  ```



<pre><code>
<b> 확인 사항</b>
  1. 원본 파일은 삭제되지 않음 - FileCopy 함수를 썼기 때문.
  2. 아웃룩이 켜져있지 않을 때에도 자동으로 '보낼 편지함'에 저장되고 이후에 발송되나,
      네트워크 문제 등으로 발송되지 않을 경우가 있으니 가끔 보낸 편지함을 확인해볼 것.
  3. 오류 발생시 주소에 '\' 이 빠져있거나 혹은 두 개가 있는지 확인해볼 것. if문 걸어서
      체크할 수 있으나 그냥 직접 확인하는 게 나을 것 같아 추가하지 않음.

</code></pre>
___
___


## 전체 코드  
 - 매크로 파일을 첨부하고 싶으나 지금 뭔가 컴퓨터에 문제가 있어 VBA가 실행이 안 됨. 조만간 할게용

  ```python
Option Explicit

Sub Copy_Icebox()

Dim Current_Path As String
Dim File_Name As String
Dim Star As String
Dim Desti As String

Current_Path = ThisWorkbook.Path
File_Name = Cells(3, 4).Value & ".pdf"
If Len(Cells(3, 4).Value) <> 20 Then
    GoTo ErrorMessage
End If

On Error GoTo ErrorMessage


Star = ThisWorkbook.Path & "\" & File_Name
Desti = Cells(9, 4).Value & "\" & File_Name

FileCopy Star, Desti

MsgBox "파일 복사 완료" & Chr(10) & Desti


If MsgBox("메일을 전달하겠습니까?", vbQuestion + vbYesNo, "자동 발송") = vbYes Then

    Dim olApp As Outlook.Application
    Dim olMail As MailItem
    Dim sAttFile As String

    Set olApp = New Outlook.Application
    Set olMail = olApp.CreateItem(olMailItem)

        With olMail
            .To = Cells(5, 4).Value
            .Subject = Cells(6, 4).Value

            .Attachments.Add Star
            .Body = Cells(7, 4).Value
            .Send

        End With
        Set olMail = Nothing
        Set olApp = Nothing

        MsgBox ("메일 발송 완료")

Exit Sub

ErrorMessage:
    MsgBox ("파일 이름 오류 - 다시 시도하세요")
End If
End Sub

  ```
