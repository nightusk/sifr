---
title: "VBScript"
date: 2021-09-30T22:30:03+09:00
draft: false
---

# VBScriptからExcelを操作する
```vb
Sub Main()
    'Launch excel
    Dim app: Set app = CreateObject("Excel.Application")
    'app.Visible = True 'Uncomment this line if you would like to see what happens.

    'Open a book
    Dim wb: Set wb = app.Workbooks.Open("C:\path\to\the_book.xlsx", False, False)

    While wb.Worksheets.Count < 5
        wb.Worksheets.Add , wb.Worksheets(wb.Worksheets.Count)
    Wend
    'Select a sheet
    Dim ws: Set ws = wb.Worksheets("Sheet2")

    'Write text to a cell
    Dim rng: Set rng = ws.Range("C4")
    rng.Value = "Hello World"

    'Save and close the book
    wb.Close True

    'Quit excel
    app.Quit
End Sub

Main
```
