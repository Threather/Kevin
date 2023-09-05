Sub Egets()
    Dim ws As Worksheet
    Dim colNum1 As Long
    Dim colNum2 As Long
    Dim colNum3 As Long
    Dim colNum4 As Long
    Dim colNum5 As Long
    Dim newWB As Workbook
    Dim newWS As Worksheet

    Dim yesterday As Date
    yesterday = DateAdd("d", -1, Date)

    Set newWB = Workbooks.Add

    For Each ws In ThisWorkbook.Worksheets

        Set newWS = newWB.Sheets.Add(After:=newWB.Sheets(newWB.Sheets.Count))
        newWS.Name = "Successful Payment Gateway"



        newWS.Rows(1).Insert
        newWS.Columns("A").Delete
        newWS.Range("A1:X1").Merge
        newWS.Range("A1").Value = "Daily Successful Payment Gateway as of " & Format(Date, "mmm-yyyy")
        newWS.Range("A1").Font.Bold = True
        newWS.Range("A1").HorizontalAlignment = xlCenter
        newWS.Range("A1").VerticalAlignment = xlCenter
        newWS.Range("A1").HorizontalAlignment = xlCenter
        Debug.Print "Value of A1: " & newWS.Range("A1").Value
        newWS.Range("A1").Font.Size = 24
        newWS.Range("A1").RowHeight = 59.25
        newWS.Shapes.AddPicture "Y:\Kevin\Nara\Image\Image.jpg", msoFalse, msoTrue, 0, 0, 2.24 * 72, 0.8 * 72
        With newWS.Shapes(1)
            .ScaleHeight 1.06, msoFalse
            .ScaleWidth 0.98, msoFalse
        End With

        colNum1 = Application.Match("Status", ws.Rows(1), 0)
        colNum2 = Application.Match("Type", ws.Rows(1), 0)
        colNum3 = Application.Match("Service", ws.Rows(1), 0)
        colNum4 = Application.Match("Description", ws.Rows(1), 0)
        colNum5 = Application.Match("Date", ws.Rows(1), 0)
        colNum6 = Application.Match("Terminal", ws.Rows(1), 0)

        ws.UsedRange.AutoFilter field:=colNum1, Criteria1:=Array("Completed", "Partial refund"), Operator:=xlFilterValues
        ws.UsedRange.AutoFilter field:=colNum2, Criteria1:="PAYMENT_TO_MERCHANT"
        ws.UsedRange.AutoFilter field:=colNum3, Criteria1:="Payment amount"
        ws.UsedRange.AutoFilter field:=colNum4, Criteria1:="<>*Bonus amount pay*"
        ws.UsedRange.AutoFilter field:=colNum6, Criteria1:="e-gets"

        Dim startDate As Date
        If Weekday(yesterday) = 1 Then
            startDate = DateAdd("d", -2, yesterday)
        Else
            startDate = yesterday
        End If

        ws.UsedRange.AutoFilter field:=colNum5, Criteria1:=">=" & Format(startDate, "m/d/yyyy"), Operator:=xlAnd, Criteria2:="<=" & Format(yesterday, "m/d/yyyy")

        ws.UsedRange.SpecialCells(xlCellTypeVisible).Copy newWS.Range("A2")
        
        With newWS.Range("A2").CurrentRegion
    .Font.Name = "Natosan"
    .Font.Size = 9
End With
Dim cell As Range
For Each cell In newWS.Range("A2").CurrentRegion.Rows(2).Cells
    If cell.Value <> "" Then
        cell.Interior.Color = RGB(255, 0, 0)
        cell.Font.Color = RGB(255, 255, 255)
        cell.Font.Bold = True
    End If
Next cell
newWS.Range("A1").Font.Size = 20
        
        
        ' Delete column A below row 1 after copying data from original file.
        newWS.Range("A2", newWS.Cells(newWS.Rows.Count, "A")).Delete

        Dim lastRow As Long
        lastRow = newWS.Cells(newWS.Rows.Count, 1).End(xlUp).Row

      newWS.Range("A" & lastRow + 5).Value = "Total Transaction"
      newWS.Range("B" & lastRow + 5).Formula = "=COUNTIFS(M:M,""<>"",M:M,""<>Description"",M:M,""<>Bonus amount income"",M:M,""<>merchant order return"")"

      newWS.Range("A" & lastRow + 6).Value = "Total amount USD"
      newWS.Range("B" & lastRow + 6).Formula = "=SUMIFS(H:H,I:I,""USD"",E:E,""Payment amount"",L:L,""Completed"")+VLOOKUP(""Total Amount After Partial Refund"",A:B,2,FALSE)"


      newWS.Range("A" & lastRow + 7).Value = "Total BONUS_USD"
      newWS.Range("B" & lastRow + 7).Formula = "=SUMIFS(H:H, I:I, ""BONUS_USD"", E:E, ""Payment amount"")"

      newWS.Range("A" & lastRow + 8).Value = "Total Amount Payment"
      newWS.Range("B" & lastRow + 8).Formula = "=SUMIFS(H:H,E:E,""Payment amount"",L:L,""Completed"")+VLOOKUP(""Total Amount After Partial Refund"",A:B,2,FALSE)"

      newWS.Range("A" & lastRow + 9).Value = "Total MDR Amount"
      newWS.Range("B" & lastRow + 9).Formula = "=" & newWS.Range("B" & lastRow + 8).Address & "*0.65%"
      newWS.Range("B" & lastRow + 9).NumberFormat = "0.00"

     newWS.Range("A" & lastRow + 10).Value = "Total Amount Cross Day refund"
      newWS.Range("B" & lastRow + 10).Formula = "=SUMIFS(B:B, D:D, ""Cross Day refund"", C:C, ""Completed"")"

      newWS.Range("A" & lastRow + 11).Value = "Total Amount In Day refund"
      newWS.Range("B" & lastRow + 11).Formula = "=SUMIFS(B:B, D:D, ""In day refund"", C:C, ""Completed"")"

     newWS.Range("A" & lastRow + 12).Value = "Total Amount After Partial Refund"
     newWS.Range("B" & lastRow + 12).Formula = "=VLOOKUP(""Total Amount after Partial refund"",H:I,2,FALSE)"
     newWS.Range("A" & lastRow + 13).Value = "Total Settle Amount"
     newWS.Range("B" & lastRow + 13).Formula = "=" & newWS.Range("B" & lastRow + 8).Address & "-" & newWS.Range("B" & lastRow + 9).Address & "-" & newWS.Range("B" & lastRow + 10).Address & "-" & newWS.Range("B" & lastRow + 11).Address

    Dim refWB As Workbook
Set refWB = Workbooks.Open("Y:\Kevin\Nara\Reference file\ref.xlsx")
Dim refWS As Worksheet
Set refWS = refWB.Sheets(1)

Dim i As Long
For i = 2 To lastRow
    If newWS.Cells(i, 12).Value = "Partial refund" Then
        Dim searchVal As Long
        searchVal = newWS.Cells(i, 1).Value

        Dim j As Long
        For j = 1 To refWS.UsedRange.Rows.Count
    ' Check if the value in column 14 is equal to searchVal, if the value in column D is not equal to "MARKETING", and if the value in column K is not equal to "Partial refund"
    If refWS.Cells(j, 14).Value = searchVal And refWS.Cells(j, "D").Value <> "MARKETING" And refWS.Cells(j, "K").Value <> "Partial refund" Then
        ' Update cells in the new worksheet
        newWS.Cells(lastRow + 20, 1).Value = refWS.Cells(j, 4).Value
        newWS.Cells(lastRow + 20, 2).Formula = "=IF(AND(COUNTIF(A:A,F" & lastRow + 20 & "),COUNTIF(J:J,DATE(LEFT(C" & lastRow + 20 & ",4),MID(C" & lastRow + 20 & ",6,2),MID(C" & lastRow + 20 & ",9,2)))),""In Day refund"",""Cross Day refund"")"
        newWS.Cells(lastRow + 20, 3).Value = refWS.Cells(j, 10).Value
        newWS.Cells(lastRow + 20, 4).Value = refWS.Cells(j, 8).Value
        newWS.Cells(lastRow + 20, 5).Value = refWS.Cells(j, 11).Value
        newWS.Cells(lastRow + 20, 6).Value = refWS.Cells(j, 14).Value
        newWS.Cells(lastRow + 20, 7).Formula = "=VLOOKUP(F" & lastRow + 20 & ",A:X,8,FALSE)"
        newWS.Cells(lastRow + 20, 8).Formula = "=SUMIF(A" & lastRow + 20 & ",""PAYMENT_TO_MERCHANT"",D" & lastRow + 20 & ")"
        newWS.Cells(lastRow + 20, 9).Formula = "=IF(H" & lastRow + 20 & "=0,"""",G" & lastRow + 20 & "-H" & lastRow + 20 & ")"
        
        ' Increment lastRow by one
        lastRow = lastRow + 1
    End If
Next j
    End If
Next i

refWB.Close False


        
        newWS.Cells(lastRow + 35, 8).Value = "Total Amount after Partial refund"
        newWS.Cells(lastRow + 35, 9).Formula = "=SUM(I" & lastRow + 15 & ":I" & lastRow + 34 & ")"
       For i = lastRow + 1 To newWS.Cells(newWS.Rows.Count, 1).End(xlUp).Row
    For j = 1 To newWS.Cells(i, newWS.Columns.Count).End(xlToLeft).Column
        If newWS.Cells(i, j).Value <> "" Then
            newWS.Cells(i, j).Interior.Color = RGB(255, 0, 0)
            newWS.Cells(i, j).Font.Color = RGB(255, 255, 255)
            newWS.Cells(i, j).Font.Bold = True
        End If
    Next j
Next i

    Next ws

    Application.DisplayAlerts = False
    newWB.Sheets(1).Delete
    Application.DisplayAlerts = True

    Dim path As String
path = "Y:\Kevin\Nara\Online Merchant\E-Gets\"
Filename = path & "Daily Successful Online Payment Gateway Transaction on " & Format(Date, "dd mmmm yyyy") & ".xlsx"
newWB.SaveAs Filename

End Sub
