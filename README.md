# ThreadTest

Extremely simple demo of multithreading in twinBASIC

Normally, when you call MsgBox, code execution stops until you respond to it. In this demo, you'll see the following line continues and updates the label, before we decide to manually wait for it.

(Feb 24th 2023: Updated to use a TextBox instead of a label due to a rendering issue with lightweight controls not updating immediately; UI improvements)

(Nov 9th 2022: Updated for x64/bugfixes)

---

This is just a simple proof-of-concept showing you can safely multithread in twinBASIC via API without relying on the elaborate hacks VB6 requires to call `CreateThread`:

```
    Private Declare PtrSafe Function GetCurrentThreadId Lib "kernel32" () As Long

    Private Declare PtrSafe Function CreateThread Lib "kernel32" ( _
                            ByRef lpThreadAttributes As Any, _
                            ByVal dwStackSize As Long, _
                            ByVal lpStartAddress As LongPtr, _
                            ByRef lpParameter As Any, _
                            ByVal dwCreationFlags As Long, _
                            ByRef lpThreadId As Long) As LongPtr

    Private Declare PtrSafe Function WaitForSingleObject Lib "kernel32" ( _
                            ByVal hHandle As LongPtr, _
                            ByVal dwMilliseconds As Long) As Long

 
    
    Private Const INFINITE = -1&
    
    Private Sub Command1_Click() Handles Command1.Click
        Dim lTID As Long
        Dim lCurTID As Long
        Dim hThreadNew As LongPtr
        lCurTID = GetCurrentThreadId()
        hThreadNew = CreateThread(ByVal 0, 0, AddressOf TestThread, ByVal 0, 0, lTID)
        Text1.Text = "Thread " & lCurTID & " is waiting on thread " & lTID
        Dim hr As Long
        hr = WaitForSingleObject(hThreadNew, 30000&) 'Wait 30s as a default. You can use INFINITE instead if you never want to time out.
        Text1.Text = "Wait end code " & CStr(hr)
    End Sub

    Public Sub TestThread()
        MsgBox "Hello thread"
    End Sub
```
