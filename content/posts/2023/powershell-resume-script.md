---
title: "Powershell Resume Script"
tags: ["powershell", "email", "resume", "snippet"]
date: 2023-02-14T21:12:41-05:00
draft: false
---

Scripts to generate company email specific CV/Resume PDF(s).

This is useful for job application email filtering.

`work.email@my_domain.com` will be `work.company_name@my_domain.com`.

The `mailto` link for the email will also be updated accordingly.

{{< tabs "create_pdf" >}}
{{< tab "CreatePDF.ps1" >}}

Creates a PDF by prompted input text in user `Desktop`.

```sh
$TextFile = Read-Host -Prompt 'File name'
$TextFile = $TextFile.ToLower().Replace(' ', '')

$Word = New-Object -ComObject Word.Application
$Word.Visible = $false
$word.DisplayAlerts = "wdAlertsNone"

$Filename = "\path\to\folder\my_resume.docx"
$NewFileName = $Filename.Replace(".docx","")
$Document = $Word.Documents.open($Filename)
$objSelection = $Document.Selection
"Processing file: {0}" -f $Document.FullName
$DesktopPath = [Environment]::GetFolderPath("Desktop")

$FindText = "email@"

$MatchCase = $false
$MatchWholeWorld = $true
$MatchWildcards = $false
$MatchSoundsLike = $false
$MatchAllWordForms = $false
$Forward = $false
$Wrap = 1
$Format = $false
$Replace = 1

# Replace email with company name
$ReplaceText = $FindText -Replace "email",$TextFile
$Document.Content.Find.Execute($FindText, $MatchCase, $MatchWholeWorld, $MatchWildcards, $MatchSoundsLike, $MatchAllWordForms, $Forward, $Wrap, $Format, $ReplaceText, $Replace)

# Replace mailto hyperlink
$Document.Hyperlinks | ForEach-Object {
  if ($_.Address -like "*email@*") {
    $NewAddress = $_.Address -Replace "email",$TextFile
    $_.Address = $NewAddress
    # Copies new email to clipboard
    $NewAddress = $NewAddress -Replace "mailto:", "" | Set-Clipboard
  }
}

# Export as pdf
$Pdf = $DesktopPath + "`\" + $Document.Name.Replace(".docx", "") + "`-$TextFile.pdf"
$Document.ExportAsFixedFormat($Pdf,17)
"Exported document as {0} `r`n" -f $Pdf

# Quit Word without saving
$Document.Close($false)
$Word.Quit()
```

{{< /tab >}}
{{< tab "CreatePDFs.ps1" >}}

Create multiple PDFs based on text file in .docx file directory.

```sh
$Word = New-Object -ComObject word.application
$Word.Visible = $False

$Filename = "\path\to\folder\my_resume.docx"
$NewFileName = $Filename.Replace(".docx","")
$TextFile = "\path\to\folder\sites.txt"
$Document = $Word.Documents.open($Filename)
$objSelection = $Document.Selection
"Processing file: {0}" -f $Document.FullName

$TextInfo = (Get-Culture).TextInfo

$FindText = "email@"

$MatchCase = $false
$MatchWholeWorld = $true
$MatchWildcards = $false
$MatchSoundsLike = $false
$MatchAllWordForms = $false
$Forward = $false
$Wrap = 1
$Format = $false
$Replace = 1

foreach ($line in [System.IO.File]::ReadLines($TextFile)) {
  $line = $line.ToLower().Replace(' ', '')
  $ReplaceText = $FindText -Replace "email",$line
  $Document.Content.Find.Execute($FindText, $MatchCase, $MatchWholeWorld, $MatchWildcards, $MatchSoundsLike, $MatchAllWordForms, $Forward, $Wrap, $Format, $ReplaceText, $Replace)
  $Document.Hyperlinks | ForEach-Object {
    if ($_.Address -like "*email@*") {
      $NewAddress = $_.Address -Replace "email",$line
      $_.Address = $NewAddress
    }
  }
  $Pdf = $NewFileName + "`-$line.pdf"
  "Saving document {0} as PDF {1}" -f $Document.Fullname,$Pdf
  $Document.ExportAsFixedFormat($Pdf,17)
  "Completed processing {0} `r`n" -f $Document.Fullname
}

$Document.Close($false)
$Word.Quit()
```

{{< /tab >}}
{{< tab "sites.txt" >}}

Example `.txt` file content for `CreatePDFs.ps1`

```text
linkedin
indeed
glassdoor

```

{{< /tab >}}
{{< /tabs >}}
