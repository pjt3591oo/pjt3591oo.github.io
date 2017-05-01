---
layout: post
title:  "파일 리더기 만들기 - 사용 모듈 정리, pdf와 hwp 구조"
date:   2017-05-01 15:02:05 +0900
categories: node.js
tags: [programming, node.js, hwpml, pdf, docx, rtf, hwp]
published: true
count: 125
descript: 'docx, hwp, pdf 파일'
writer: 박정태
---

# pdf -> html 변환

가장 무난하다.

기본적으로 pdf는 htm와 비슷한 형태의 구조를 가지고 있다.

크게 **header**, **body**, **xref table**, **trailer**의 구조로 되어있다.

pdf는 환경에 상관없이 표현을 하기 위한 목적을 가지고 있는 파일이다. 이런 이유 때문에 무난히 진행이 된 것 같다.

`pdf2htmlex`와 `pdftohtmljs`라는 모듈을 이용을 했다.

```javascript
var pdftohtml = require('pdftohtmljs');
var converter = new pdftohtml('test.pdf', "sample.html");

converter.convert('ipad').then(function() {
    console.log("Success");
}).catch(function(err) {
    console.error("Conversion error: " + err);
});
```

이미지나, text같은 것들이 거의 100%로 변환이 된다.

# docx -> html 변환

docx파일을 html파일로 변환을 할 때는 style 적용과 한글이 깨지는 문제가 있다.

텍스트들을 전부 잘 읽기는 하는데 스타일 정보를 제대로 가져오지 못하기 때문에 좀 애매하다

- Headings.
- Lists.
- Customisable mapping from your own docx styles to HTML. For instance, you could convert WarningHeading to h1.warning by providing an appropriate style mapping.
- Tables. The formatting of the table itself, such as borders, is currently ignored, but the formatting of the text is treated the same as in the rest of the document.
- Footnotes and endnotes.
- Images.
- Bold, italics, underlines, strikethrough, superscript and subscript.
- Links.
- Line breaks.
- Text boxes. The contents of the text box are treated as a separate paragraph that appears after the paragraph containing the text box.
- Comments.

위 내용에 한에서만 스타일 변경이 가능은 한 것 같은데 생각보다 잘 되지 않는다...

`mammoth`라는 모듈을 이용했다.

```javascript
var mammoth = require("mammoth");
var options = {
    styleMap: [
        // "u => em",
        // "p[style-name='Section Title'] => h1:fresh",
        // "p[style-name='Subsection Title'] => h2:fresh"
    ],
    includeDefaultStyleMap: false
};

mammoth.convertToHtml({path: './reader/test.docx'}, {}).then(result=>{
    let text = result.value;
    let message = result.messages;
    console.log(text);
    console.log(message);
})
```

# hwp -> html 변환

우선 hwp를 완전히 html로 변환하지 못했다.

하지만 Microsoft에서 hwp의 ml을 공개를 해서 hwp를 hwpml로 변환을 하였다.

```xml
<?xml version="1.0" encoding="UTF-8" standalone="no" ?>
<HWPML Version="2.8" SubVersion="8.0.0.0" Style="embed"><HEAD SecCnt="1"><DOCSUMMARY><TITLE>hello world</TITLE><AUTHOR>누군가</AUTHOR><DATE>2017년 5월 1일 월요일 오전 9:38:44</DATE></DOCSUMMARY><DOCSETTING><BEGINNUMBER Page="1" Footnote="1" Endnote="1" Picture="1" Table="1" Equation="1"/><CARETPOS List="0" Para="5" Pos="0"/></DOCSETTING><MAPPINGTABLE><FACENAMELIST><FONTFACE Lang="Hangul" Count="2"><FONT Id="0" Type="ttf" Name="함초롬돋움"><TYPEINFO FamilyType="2" SerifStyle="3" Weight="5" Proportion="4" Contrast="0" StrokeVariation="1" ArmStyle="1" Letterform="1" Midline="1" XHeight="1"/></FONT><FONT Id="1" Type="ttf" Name="함초롬바탕"><TYPEINFO FamilyType="2" SerifStyle="3" Weight="5" Proportion="4" Contrast="0" StrokeVariation="1" ArmStyle="1" Letterform="1" Midline="1" XHeight="1"/></FONT></FONTFACE><FONTFACE Lang="Latin" Count="2"><FONT Id="0" Type="ttf" Name="함초롬돋움"><TYPEINFO FamilyType="2" SerifStyle="3" Weight="5" Proportion="4" Contrast="0" StrokeVariation="1" ArmStyle="1" Letterform="1" Midline="1" XHeight="1"/></FONT><FONT Id="1" Type="ttf" Name="함초롬바탕"><TYPEINFO FamilyType="2" SerifStyle="3" Weight="5" Proportion="4" Contrast="0" StrokeVariation="1" ArmStyle="1" Letterform="1" Midline="1" XHeight="1"/></FONT></FONTFACE><FONTFACE Lang="Hanja" Count="2"><FONT Id="0" Type="ttf" Name="함초롬돋움"><TYPEINFO FamilyType="2" SerifStyle="3" Weight="5" Proportion="4" Contrast="0" StrokeVariation="1" ArmStyle="1" Letterform="1" Midline="1" XHeight="1"/></FONT><FONT Id="1" Type="ttf" Name="함초롬바탕"><TYPEINFO FamilyType="2" SerifStyle="3" Weight="5" Proportion="4" Contrast="0" StrokeVariation="1" ArmStyle="1" Letterform="1" Midline="1" XHeight="1"/></FONT></FONTFACE><FONTFACE Lang="Japanese" Count="2"><FONT Id="0" Type="ttf" Name="함초롬돋움"><TYPEINFO FamilyType="2" SerifStyle="3" Weight="5" Proportion="4" Contrast="0" StrokeVariation="1" ArmStyle="1" Letterform="1" Midline="1" XHeight="1"/></FONT><FONT Id="1" Type="ttf" Name="함초롬바탕"><TYPEINFO FamilyType="2" SerifStyle="3" Weight="5" Proportion="4" Contrast="0" StrokeVariation="1" ArmStyle="1" Letterform="1" Midline="1" XHeight="1"/></FONT></FONTFACE><FONTFACE Lang="Other" Count="2"><FONT Id="0" Type="ttf" Name="함초롬돋움"><TYPEINFO FamilyType="2" SerifStyle="3" Weight="5" Proportion="4" Contrast="0" StrokeVariation="1" ArmStyle="1" Letterform="1" Midline="1" XHeight="1"/></FONT><FONT Id="1" Type="ttf" Name="함초롬바탕"><TYPEINFO FamilyType="2" SerifStyle="3" Weight="5" Proportion="4" Contrast="0" StrokeVariation="1" ArmStyle="1" Letterform="1" Midline="1" XHeight="1"/></FONT></FONTFACE><FONTFACE Lang="Symbol" Count="2"><FONT Id="0" Type="ttf" Name="함초롬돋움"><TYPEINFO FamilyType="2" SerifStyle="3" Weight="5" Proportion="4" Contrast="0" StrokeVariation="1" ArmStyle="1" Letterform="1" Midline="1" XHeight="1"/></FONT><FONT Id="1" Type="ttf" Name="함초롬바탕"><TYPEINFO FamilyType="2" SerifStyle="3" Weight="5" Proportion="4" Contrast="0" StrokeVariation="1" ArmStyle="1" Letterform="1" Midline="1" XHeight="1"/></FONT></FONTFACE><FONTFACE Lang="User" Count="2"><FONT Id="0" Type="ttf" Name="함초롬돋움"><TYPEINFO FamilyType="2" SerifStyle="3" Weight="5" Proportion="4" Contrast="0" StrokeVariation="1" ArmStyle="1" Letterform="1" Midline="1" XHeight="1"/></FONT><FONT Id="1" Type="ttf" Name="함초롬바탕"><TYPEINFO FamilyType="2" SerifStyle="3" Weight="5" Proportion="4" Contrast="0" StrokeVariation="1" ArmStyle="1" Letterform="1" Midline="1" XHeight="1"/></FONT></FONTFACE></FACENAMELIST><BORDERFILLLIST Count="1"><BORDERFILL Id="1" ThreeD="false" Shadow="false" Slash="0" BackSlash="0" CrookedSlash="0" CounterSlash="0" CounterBackSlash="0" BreakCellSeparateLine="0" CenterLine="0"><LEFTBORDER Type="None" Width="0.1mm" Color="0"/><RIGHTBORDER Type="None" Width="0.1mm" Color="0"/><TOPBORDER Type="None" Width="0.1mm" Color="0"/><BOTTOMBORDER Type="None" Width="0.1mm" Color="0"/><DIAGONAL Type="Solid" Width="0.1mm" Color="0"/><FILLBRUSH><WINDOWBRUSH FaceColor="4294967295" HatchColor="0" HatchStyle="-1" Alpha="0"/></FILLBRUSH></BORDERFILL></BORDERFILLLIST><CHARSHAPELIST Count="8"><CHARSHAPE Id="0" Height="1000" TextColor="0" ShadeColor="4294967295" UseFontSpace="false" UseKerning="false" SymMark="0" BorderFillId="1"><FONTID Hangul="1" Latin="1" Hanja="1" Japanese="1" Other="1" Symbol="1" User="1"/><RATIO Hangul="100" Latin="100" Hanja="100" Japanese="100" Other="100" Symbol="100" User="100"/><CHARSPACING Hangul="0" Latin="0" Hanja="0" Japanese="0" Other="0" Symbol="0" User="0"/><RELSIZE Hangul="100" Latin="100" Hanja="100" Japanese="100" Other="100" Symbol="100" User="100"/><CHAROFFSET Hangul="0" Latin="0" Hanja="0" Japanese="0" Other="0" Symbol="0" User="0"/></CHARSHAPE><CHARSHAPE Id="1" Height="1000" TextColor="0" ShadeColor="4294967295" UseFontSpace="false" UseKerning="false" SymMark="0" BorderFillId="1"><FONTID Hangul="0" Latin="0" Hanja="0" Japanese="0" Other="0" Symbol="0" User="0"/><RATIO Hangul="100" Latin="100" Hanja="100" Japa>daf</CHAR></TEXT></P></SECTION></BODY></HWPML>
```

해당 마크업 파일에는 스타일 정보, 내용 등이 들어있기 때문에 해당 파일을 파싱을 하면 가능하다.

일단 pyhwp라는 라이브러리가 있긴한데 이게 python특정 버전에서만 작동을 하는 것 같다. python3.x로 포팅을 시켜볼려고 했는데 잘 안되는......

다음으로 java를 이용해서 파싱을 해볼까 한다. 다행이 node bridge로 java연동이 되는 것 같다.

`node-hwp`라는 모듈을 사용하였다.

```javascript
var hwp = require("./node-hwp");
var fs = require('fs');
hwp.open('helo.hwp', function(err, doc){
	console.log(doc.toHML());
	// fs.writeFile('test1.html', doc.toHML());
});
```

위 코드를 실행시키면 node-hwp가 없어서 에러가 뜰 것이다. 해당 모듈이 npm 받아지지 않아서 직접 git clone을 했다.

```bash
$ git clone https://github.com/123jimin/node-hwp.git
```
