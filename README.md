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
  <w:body>
    <!-- content -->
  </w:body>
</w:document>
```

b. Paragraph (Text Container)

```xml
<w:p> <!-- Paragraph -->

  <w:pPr> <!-- Paragraph properties -->

    <w:pStyle w:val="Heading1"/>
    <!-- w:val="Heading1"
        Meaning: Applies Word built-in Heading 1 style
        Effect: Appears in Navigation Pane as main heading -->

    <w:outlineLvl w:val="0"/>
    <!-- w:val="0"
        Meaning: Outline level 0
        Effect: Top-level heading in Navigation Pane -->

    <w:jc w:val="center"/>
    <!-- w:val="center"
        Meaning: Paragraph alignment
        Possible values: left | right | center | both (justified) -->

  </w:pPr>
    <w:r> <!-- Text run -->

      <w:rPr> <!-- Run (text) properties -->
  
        <w:b/>
        <!-- Bold text -->
  
        <w:u w:val="single"/>
        <!-- w:val="single"
            Meaning: Single underline
            Other values: double | none -->
  
        <w:color w:val="2D6CC0"/>
        <!-- w:val="2D6CC0"
            Meaning: Hex RGB color (blue tone) -->
  
        <w:sz w:val="24"/>
        <!-- w:val="24"
            Meaning: Font size
            Note: Value = half-points × 2 → 24 = 12pt -->
  
        <w:rFonts w:ascii="Arial" w:hAnsi="Arial"/>
        <!-- w:ascii / w:hAnsi
            Meaning: Font family for ASCII and ANSI text -->
  
    </w:rPr>
  
    <w:t>Sample Heading Text</w:t>
    <!-- Actual visible text -->

  </w:r>

</w:p>
```

c. Table Structure

```xml
<w:tbl> <!-- Table -->
  <w:tr> <!-- Table row -->
    <w:tc> <!-- Table cell -->
      <w:p>
        <w:pPr>
          <w:jc w:val="right"/>
          <!-- Right-align text inside this cell -->
        </w:pPr>
        <w:r>
          <w:t>Right Aligned Cell</w:t>
        </w:r>
      </w:p>
    </w:tc>
  </w:tr>
</w:tbl>
```

## 🔗 Adapting XML into ABAP Code

Below is the simple XML implemented into ABAP code.

```abap
DATA: lv_xml_1        TYPE string,
      lv_xml_2        TYPE string,
      lv_xml_3        TYPE string,
      lv_xml          TYPE string,
      lo_zip          TYPE REF TO cl_abap_zip,
      lv_docx_xstring TYPE xstring,
      lv_filename     TYPE string,
      lt_solix        TYPE solix_tab.


DATA lv_example TYPE string VALUE '{lv_example}'.
DATA lv_control TYPE string VALUE '{lv_control}'.
DATA lv_datum   TYPE string VALUE '{lv_datum}'.

lv_xml_1 = ''                         " start empty
&&  '<?xml version="1.0" encoding="UTF-8" standalone="yes"?>'
&&  '<w:document xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main">'
&&  '<w:body>'

" ---- Centered multi-line header ----
&&  '<w:p>'
&&  '  <w:pPr><w:jc w:val="right"/></w:pPr>'
&&  '  <w:r>'
&&  '    <w:rPr>'
&&  '      <w:b/><w:sz w:val="24"/>'
&&  '      <w:rFonts w:ascii="Arial" w:hAnsi="Arial" w:eastAsia="Arial" w:cs="Arial"/>'
&&  '    </w:rPr>'
&&  '    <w:t>To:' && '&#160;' && lv_example && '<w:br/>In Istanbul, Turkiye</w:t>'
&&  '  </w:r>'
&&  '  <w:r><w:br/></w:r>'
&&  '</w:p>'"

&&  '<w:p>'
&&  '  <w:pPr><w:jc w:val="left"/></w:pPr>'
&&  '  <w:r>'
&&  '    <w:rPr>'
&&  '      <w:sz w:val="24"/>'
&&  '      <w:rFonts w:ascii="Arial" w:hAnsi="Arial" w:eastAsia="Arial" w:cs="Arial"/>'
&&  '    </w:rPr>'
&&  '    <w:t>' && 'Date:' && '&#160;' && lv_datum && '<w:br/>' && 'Ref: №:' && '&#160;' && lv_example && '</w:t>'
&&  '  </w:r>'
&&  '</w:p>'"

"To Whom it may Concern
&&  '<w:p>'
&&  '  <w:pPr>'
&&  '    <w:pStyle w:val="Heading1"/>'
&&  '    <w:outlineLvl w:val="0"/>'
&&  '    <w:jc w:val="center"/>'
&&  '  </w:pPr>'
&&  '  <w:r>'
&&  '    <w:rPr>'
&&  '      <w:color w:val="2D6CC0"/>'
&&  '      <w:b/>'
&&  '      <w:sz w:val="24"/>'
&&  '      <w:rFonts w:ascii="Arial" w:hAnsi="Arial"/>'
&&  '    </w:rPr>'
&&  '    <w:t>To Whom It May Concern</w:t>'
&&  '  </w:r>'
&&  '</w:p>'

"Another Paragraph
&&  '<w:p>'
&&  '  <w:pPr><w:jc w:val="both"/></w:pPr>'
&&  '  <w:r>'
&&  '    <w:rPr>'
&&  '      <w:sz w:val="24"/>'
&&  '      <w:rFonts w:ascii="Arial" w:hAnsi="Arial" w:eastAsia="Arial" w:cs="Arial"/>'
&&  '    </w:rPr>'
&&  '    <w:t>' && lv_example && ' current monthly gross salary is' && '&#160;' && lv_example
&&  ' TRY (taxes included)'.

IF lv_control IS NOT INITIAL.
  TRANSLATE lv_example TO LOWER CASE.
  lv_xml_2 =
  ' and' && '&#160;' && lv_example && ' TRY is transferred to' && '&#160;' && lv_example
  && ' company-provided life endowment program account'.
ENDIF.

lv_xml_3 =
'.</w:t>'
&&  '  </w:r>'
&&  '</w:p>'
&&  '<w:p>'
&&  '  <w:pPr><w:jc w:val="both"/></w:pPr>'
&&  '  <w:r>'
&&  '    <w:rPr>'
&&  '      <w:sz w:val="24"/>'
&&  '      <w:rFonts w:ascii="Arial" w:hAnsi="Arial" w:eastAsia="Arial" w:cs="Arial"/>'
&&  '    </w:rPr>'
&& '<w:br/><w:t>'


&&  '    Employee’s all costs related to this business trip including travel, accommodation, insurance,'
&&  ' living expenses, as well as repatriation if any, will be covered by the company.' && '<w:br/>'
&& '<w:br/>'
&&  'Your kind assistance in granting visa to the a forementioned employee will be much appreciated.'
&& '<w:br/>'


&& '<w:br/>Should you require any further information, please do not hesitate to contact the undersigned.'
&&  '</w:t>'
&&  '  </w:r>'
&&  '</w:p>'


" ---- Section properties (required by Word) ----
&&  '<w:sectPr/>'
&&  '</w:body>'
&&  '</w:document>'.

CONCATENATE lv_xml_1 lv_xml_2 lv_xml_3 INTO lv_xml.

"--- 2. ZIP it as word/document.xml
CREATE OBJECT lo_zip.
CALL METHOD lo_zip->add
  EXPORTING
    name    = 'word/document.xml'
    content = cl_abap_conv_codepage=>create_out( )->convert( lv_xml ).

"--- 3. Add minimal _rels/.rels and [Content_Types].xml
lo_zip->add( name = '_rels/.rels'
  content = cl_abap_conv_codepage=>create_out( )->convert(
  '<?xml version="1.0" encoding="UTF-8"?>'
  && '<Relationships xmlns="http://schemas.openxmlformats.org/package/2006/relationships">'
  && '<Relationship Id="rId1" Type="http://schemas.openxmlformats.org/officeDocument/2006/relationships/officeDocument"'
  && ' Target="word/document.xml"/>'
  && '</Relationships>'
  ) ).

lo_zip->add( name = '[Content_Types].xml'
  content = cl_abap_conv_codepage=>create_out( )->convert(
  '<?xml version="1.0" encoding="UTF-8"?>'
  && '<Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">'
  && '<Default Extension="xml" ContentType="application/xml"/>'
  && '<Override PartName="/word/document.xml" ContentType="application/vnd.openxmlformats-officedocument.wordprocessingml.document.main+xml"/>'
  && '</Types>'
  ) ).

"--- 4. Get final DOCX as XSTRING
lv_docx_xstring = lo_zip->save( ).

"--- 5. Convert XSTRING to SOLIX_TAB for GUI_DOWNLOAD
lt_solix = cl_bcs_convert=>xstring_to_solix( lv_docx_xstring ).

"--- 6. Set filename
CONCATENATE 'C:\Users\Public\Downloads\Doc' '.docx' INTO lv_filename.

"--- 7. Download DOCX
CALL FUNCTION 'GUI_DOWNLOAD'
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
```
## ⚠️ Troubleshooting

Even if no syntax error has been received within ABAP editor, you may still not be able to display your word document after downloading. This is because the xml is broken and you do not have necessary syntax check for XML.

Most common errors caused by wrong tag structure. Therefore, it is important to check your tag tree.
One convenient way is to use an XML renderer online. This may show you the location of first structure break.

<img width="1004" height="533" alt="image" src="https://github.com/user-attachments/assets/e23a6afc-480b-4b2f-9523-804c8f84e04b" />

Simply select all XML, copy and paste it to the renderer.

<img width="1004" height="531" alt="image" src="https://github.com/user-attachments/assets/214dc251-4d3b-4deb-953f-93f33e25dfc3" />

After beautifying, it is going to be much easier to examine.

<img width="1004" height="387" alt="image" src="https://github.com/user-attachments/assets/057499b7-6a26-43af-8feb-683a55224039" />

In our example, the appearance should be this:

<img width="1004" height="533" alt="image" src="https://github.com/user-attachments/assets/58e0cefd-1e9e-4b3e-90b2-d21d91a50232" />

One common mistake is if one your variables value consists of symbol ‘&’, then XML considers it as a special character and tries to render accordingly. And it may not be seen in the rendered version.

One way to solve this is changing character ‘&’ with ‘&amp;’.
```abap
REPLACE ALL OCCURRENCES OF '&' IN lv_example WITH '&amp;'.
```
After downloading word, it can be changed to a zip file simply by editing name extension from “docx“ to “zip“.

<img width="1004" height="147" alt="image" src="https://github.com/user-attachments/assets/b0328d07-a469-478b-8482-8237f5271b6f" />

Inside, document.xml can be found.

<img width="1004" height="160" alt="image" src="https://github.com/user-attachments/assets/a44a667c-aaf7-4eed-b082-8fa6927c51ba" />

Clicking it will display the XML structure with its values.

<img width="888" height="723" alt="image" src="https://github.com/user-attachments/assets/18f1e7f3-0cee-4c68-ab81-4edc3f7b2ece" />
