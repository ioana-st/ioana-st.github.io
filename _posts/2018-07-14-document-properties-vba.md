---
layout: single
title: "VBA: Batch Changing Document Properties"
date: 2018-07-14 16:00:28 +0300
classes: wide
categories: vba
---
After a rebranding, the names of the two products we documented changed, so we had to update the properties of a few hundred Word documents. In our case, the product name is saved as a custom property. We also wanted to update the titles of the documents, by concatenating several document properties.

![Word Document Properties]({{ "/assets/images/document_properties.png" | relative_url }})

This VBA macro processes all the files in a given folder and, for each of them, updates the product name (based on the old one) and then sets the title to `[Product Name] [Version] [Module]` (for example, `New Product Name 7.2 Cache Servers`).

```vba
Sub BatchProcess()
  Dim strFolder As String, strFile As String, wdDoc As Document, product As String, version As String, title As String, newtitle As String
  strFolder = "D:\technical_guides\" 'replace the path with your own folder; the macro processes all the .docx files in this folder
  strFile = Dir(strFolder & "\*.docx", vbNormal)
  While strFile <> ""
    Set wdDoc = Documents.Open(FileName:=strFolder & "\" & strFile, AddToRecentFiles:=False, Visible:=False)
    With wdDoc
      If wdDoc.CustomDocumentProperties("ProductName").Value = "Alain Software" Then
        wdDoc.CustomDocumentProperties("ProductName").Value = "Jake App" 'if the old product name was Alain Software, replace it with Jake App
      Else
        If wdDoc.CustomDocumentProperties("ProductName").Value = "Cuthbert Co." Then
          wdDoc.CustomDocumentProperties("ProductName").Value = "Eddie App"
        End If
      End If
      product = wdDoc.CustomDocumentProperties("ProductName").Value 'saves the value of the ProductName property in a variable
      version = wdDoc.CustomDocumentProperties("PoweredBy").Value 'saves the value of the PoweredBy property in a variable
      title = wdDoc.CustomDocumentProperties("GuideName").Value 'saves the value of the GuideName property in a variable
      newtitle = product + " " + version + " " + title 'creates the new title by adding the above values
      wdDoc.BuiltInDocumentProperties("Title") = newtitle 'sets the Title property to the new title created above
      wdDoc.Fields.update 'updates all the fields to take into account the new property
      wdDoc.Save 'saves the document
    End With
    strFile = Dir() 'moves to the next file in the folder
  Wend
End Sub
```
 

If you want to simply update the product name for all the documents in a given folder, replace the entire `If` with:

```vba
wdDoc.CustomDocumentProperties("ProductName").Value = "Your New Product Name"
```
