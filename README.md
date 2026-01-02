# Creating Word Documents via XML (XML with ABAP)

**Author:** Mustafa Gedikli  
**Date:** 14.12.2025

---

Mustafa Gedikli

14.12.2025

## 📌 Purpose of This Document

This document describes the approach for creating Microsoft Word documents dynamically using WordprocessingML (XML) in ABAP. It explains how document structure, formatting, headings, tables, and styles can be generated directly via XML without relying on Word templates, macros, SmartForms, or Adobe Forms. The goal is to provide a flexible and maintainable method for producing fully formatted Word documents in SAP on-premise systems.

The main idea here is to create an XML code and put it into an ABAP variable as a value. Then utilize this variable in whichever case required. In our instance, it is used in GUI_DOWNLOAD function in order to download a Word file.

## 🏷 Common XML Tags

There are too many tags and since they can be found on the internet, only common ones are shared below.

a. Document Skeleton

```xml
<w:document>
```

```xml
<w:body>
```

```xml
<!-- content -->
```

```xml
</w:body>
```

```xml
</w:document>
```

b. Paragraph (Text Container)

```xml
<w:p> <!-- Paragraph -->
```

```xml
<w:pPr> <!-- Paragraph properties -->
```

```xml
<w:pStyle w:val="Heading1"/>
```

```xml
<!-- w:val="Heading1"
```

Meaning: Applies Word built-in Heading 1 style

Effect: Appears in Navigation Pane as main heading -->

```xml
<w:outlineLvl w:val="0"/>
```

```xml
<!-- w:val="0"
```

Meaning: Outline level 0

Effect: Top-level heading in Navigation Pane -->

```xml
<w:jc w:val="center"/>
```

```xml
<!-- w:val="center"
```

Meaning: Paragraph alignment

Possible values: left | right | center | both (justified) -->

```xml
</w:pPr>
```

```xml
<w:r> <!-- Text run -->
```

```xml
<w:rPr> <!-- Run (text) properties -->
```

```xml
<w:b/>
```

```xml
<!-- Bold text -->
```

```xml
<w:u w:val="single"/>
```

```xml
<!-- w:val="single"
```

Meaning: Single underline

Other values: double | none -->

```xml
<w:color w:val="2D6CC0"/>
```

```xml
<!-- w:val="2D6CC0"
```

Meaning: Hex RGB color (blue tone) -->

```xml
<w:sz w:val="24"/>
```

```xml
<!-- w:val="24"
```

Meaning: Font size

Note: Value = half-points × 2 → 24 = 12pt -->

```xml
<w:rFonts w:ascii="Arial" w:hAnsi="Arial"/>
```

```xml
<!-- w:ascii / w:hAnsi
```

Meaning: Font family for ASCII and ANSI text -->

```xml
</w:rPr>
```

```xml
<w:t>Sample Heading Text</w:t>
```

```xml
<!-- Actual visible text -->
```

```xml
</w:r>
```

```xml
</w:p>
```

c. Table Structure

```xml
<w:tbl> <!-- Table -->
```

```xml
<w:tr> <!-- Table row -->
```

```xml
<w:tc> <!-- Table cell -->
```

```xml
<w:p>
```

```xml
<w:pPr>
```

```xml
<w:jc w:val="right"/>
```

```xml
<!-- Right-align text inside this cell -->
```

```xml
</w:pPr>
```

```xml
<w:r>
```

```xml
<w:t>Right Aligned Cell</w:t>
```

```xml
</w:r>
```

```xml
</w:p>
```

```xml
</w:tc>
```

```xml
</w:tr>
```

```xml
</w:tbl>
```

## 🔗 Adapting XML into ABAP Code

Below is the simple XML implemented into ABAP code.

```abap
DATA: lv_xml_1        TYPE string,
```

lv_xml_2        TYPE string,

lv_xml_3        TYPE string,

lv_xml          TYPE string,

lo_zip          TYPE REF TO cl_abap_zip,

lv_docx_xstring TYPE xstring,

lv_filename     TYPE string,

lt_solix        TYPE solix_tab.

```abap
DATA lv_example TYPE string VALUE '{lv_example}'.
```

```abap
DATA lv_control TYPE string VALUE '{lv_control}'.
```

```abap
DATA lv_datum   TYPE string VALUE '{lv_datum}'.
```

lv_xml_1 = ''                         " start empty

```abap
&&  '<?xml version="1.0" encoding="UTF-8" standalone="yes"?>'
```

```abap
&&  '<w:document xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main">'
```

```abap
&&  '<w:body>'
```

" ---- Centered multi-line header ----

```abap
&&  '<w:p>'
```

```abap
&&  '  <w:pPr><w:jc w:val="right"/></w:pPr>'
```

```abap
&&  '  <w:r>'
```

```abap
&&  '    <w:rPr>'
```

```abap
&&  '      <w:b/><w:sz w:val="24"/>'
```

```abap
&&  '      <w:rFonts w:ascii="Arial" w:hAnsi="Arial" w:eastAsia="Arial" w:cs="Arial"/>'
```

```abap
&&  '    </w:rPr>'
```

```abap
&&  '    <w:t>To:' && '&#160;' && lv_example && '<w:br/>In Istanbul, Turkiye</w:t>'
```

```abap
&&  '  </w:r>'
```

```abap
&&  '  <w:r><w:br/></w:r>'
```

```abap
&&  '</w:p>'"
```

```abap
&&  '<w:p>'
```

```abap
&&  '  <w:pPr><w:jc w:val="left"/></w:pPr>'
```

```abap
&&  '  <w:r>'
```

```abap
&&  '    <w:rPr>'
```

```abap
&&  '      <w:sz w:val="24"/>'
```

```abap
&&  '      <w:rFonts w:ascii="Arial" w:hAnsi="Arial" w:eastAsia="Arial" w:cs="Arial"/>'
```

```abap
&&  '    </w:rPr>'
```

```abap
&&  '    <w:t>' && 'Date:' && '&#160;' && lv_datum && '<w:br/>' && 'Ref: №:' && '&#160;' && lv_example && '</w:t>'
```

```abap
&&  '  </w:r>'
```

```abap
&&  '</w:p>'"
```

"To Whom it may Concern

```abap
&&  '<w:p>'
```

```abap
&&  '  <w:pPr>'
```

```abap
&&  '    <w:pStyle w:val="Heading1"/>'
```

```abap
&&  '    <w:outlineLvl w:val="0"/>'
```

```abap
&&  '    <w:jc w:val="center"/>'
```

```abap
&&  '  </w:pPr>'
```

```abap
&&  '  <w:r>'
```

```abap
&&  '    <w:rPr>'
```

```abap
&&  '      <w:color w:val="2D6CC0"/>'
```

```abap
&&  '      <w:b/>'
```

```abap
&&  '      <w:sz w:val="24"/>'
```

```abap
&&  '      <w:rFonts w:ascii="Arial" w:hAnsi="Arial"/>'
```

```abap
&&  '    </w:rPr>'
```

```abap
&&  '    <w:t>To Whom It May Concern</w:t>'
```

```abap
&&  '  </w:r>'
```

```abap
&&  '</w:p>'
```

"Another Paragraph

```abap
&&  '<w:p>'
```

```abap
&&  '  <w:pPr><w:jc w:val="both"/></w:pPr>'
```

```abap
&&  '  <w:r>'
```

```abap
&&  '    <w:rPr>'
```

```abap
&&  '      <w:sz w:val="24"/>'
```

```abap
&&  '      <w:rFonts w:ascii="Arial" w:hAnsi="Arial" w:eastAsia="Arial" w:cs="Arial"/>'
```

```abap
&&  '    </w:rPr>'
```

```abap
&&  '    <w:t>' && lv_example && ' current monthly gross salary is' && '&#160;' && lv_example
```

```abap
&&  ' TRY (taxes included)'.
```

IF lv_control IS NOT INITIAL.

TRANSLATE lv_example TO LOWER CASE.

lv_xml_2 =

' and' && '&#160;' && lv_example && ' TRY is transferred to' && '&#160;' && lv_example

```abap
&& ' company-provided life endowment program account'.
```

ENDIF.

lv_xml_3 =

'.</w:t>'

```abap
&&  '  </w:r>'
```

```abap
&&  '</w:p>'
```

```abap
&&  '<w:p>'
```

```abap
&&  '  <w:pPr><w:jc w:val="both"/></w:pPr>'
```

```abap
&&  '  <w:r>'
```

```abap
&&  '    <w:rPr>'
```

```abap
&&  '      <w:sz w:val="24"/>'
```

```abap
&&  '      <w:rFonts w:ascii="Arial" w:hAnsi="Arial" w:eastAsia="Arial" w:cs="Arial"/>'
```

```abap
&&  '    </w:rPr>'
```

```abap
&& '<w:br/><w:t>'
```

```abap
&&  '    Employee’s all costs related to this business trip including travel, accommodation, insurance,'
```

```abap
&&  ' living expenses, as well as repatriation if any, will be covered by the company.' && '<w:br/>'
```

```abap
&& '<w:br/>'
```

```abap
&&  'Your kind assistance in granting visa to the a forementioned employee will be much appreciated.'
```

```abap
&& '<w:br/>'
```

```abap
&& '<w:br/>Should you require any further information, please do not hesitate to contact the undersigned.'
```

```abap
&&  '</w:t>'
```

```abap
&&  '  </w:r>'
```

```abap
&&  '</w:p>'
```

" ---- Section properties (required by Word) ----

```abap
&&  '<w:sectPr/>'
```

```abap
&&  '</w:body>'
```

```abap
&&  '</w:document>'.
```

CONCATENATE lv_xml_1 lv_xml_2 lv_xml_3 INTO lv_xml.

"--- 2. ZIP it as word/document.xml

CREATE OBJECT lo_zip.

```abap
CALL METHOD lo_zip->add
```

EXPORTING

name    = 'word/document.xml'

content = cl_abap_conv_codepage=>create_out( )->convert( lv_xml ).

"--- 3. Add minimal _rels/.rels and [Content_Types].xml

lo_zip->add( name = '_rels/.rels'

content = cl_abap_conv_codepage=>create_out( )->convert(

'<?xml version="1.0" encoding="UTF-8"?>'

```abap
&& '<Relationships xmlns="http://schemas.openxmlformats.org/package/2006/relationships">'
```

```abap
&& '<Relationship Id="rId1" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/officeDocument"'
```

```abap
&& ' Target="word/document.xml"/>'
```

```abap
&& '</Relationships>'
```

) ).

lo_zip->add( name = '[Content_Types].xml'

content = cl_abap_conv_codepage=>create_out( )->convert(

'<?xml version="1.0" encoding="UTF-8"?>'

```abap
&& '<Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">'
```

```abap
&& '<Default Extension="xml" ContentType="application/xml"/>'
```

```abap
&& '<Override PartName="/word/document.xml" ContentType="application/vnd.openxmlformats-officedocument.wordprocessingml.document.main+xml"/>'
```

```abap
&& '</Types>'
```

) ).

"--- 4. Get final DOCX as XSTRING

lv_docx_xstring = lo_zip->save( ).

"--- 5. Convert XSTRING to SOLIX_TAB for GUI_DOWNLOAD

lt_solix = cl_bcs_convert=>xstring_to_solix( lv_docx_xstring ).

"--- 6. Set filename

CONCATENATE 'C:\Users\Public\Downloads\Doc' '.docx' INTO lv_filename.

"--- 7. Download DOCX

```abap
CALL FUNCTION 'GUI_DOWNLOAD'
```

EXPORTING

filename                = lv_filename

filetype                = 'BIN'

bin_filesize            = xstrlen( lv_docx_xstring )

TABLES

data_tab                = lt_solix

EXCEPTIONS

file_write_error        = 1

no_batch                = 2

gui_refuse_filetransfer = 3

invalid_type            = 4

no_authority            = 5

unknown_error           = 6

OTHERS                  = 7.

IF sy-subrc <> 0.

MESSAGE 'Error downloading DOCX' TYPE 'E'.

ENDIF.

## ⚠️ Troubleshooting

Even if no syntax error has been received within ABAP editor, you may still not be able to display your word document after downloading. This is because the xml is broken and you do not have necessary syntax check for XML.

Most common errors caused by wrong tag structure. Therefore, it is important to check your tag tree.
One convenient way is to use an XML renderer online. This may show you the location of first structure break.

Simply select all XML, copy and paste it to the renderer.

After beautifying, it is going to be much easier to examine.

In our example, the appearance should be this:

One common mistake is if one your variables value consists of symbol ‘&’, then XML considers it as a special character and tries to render accordingly. And it may not be seen in the rendered version.

One way to solve this is changing character ‘&’ with ‘&amp;’.

REPLACE ALL OCCURRENCES OF '&' IN lv_example WITH '&amp;'.

After downloading word, it can be changed to a zip file simply by editing name extension from “docx“ to “zip“.

Inside, document.xml can be found.

Clicking it will display the XML structure with its values.
