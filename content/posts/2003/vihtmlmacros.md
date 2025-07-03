---
title: "HTML Macros for vi"
date: 2003-09-08T19:06:37-06:00
toc: false
images:
categories:
  - tech
tags: 
  - vi
  - html
---

```
" HTML Macros
"
"	These are bar far the best set of VI HTML 
"	macros that I have used.  To my knowledge
"	there is no more of an extensive list as this.
"	
"	However Doug Renze who created them does not seem
"	to exist anymore.  I can't located him to see if
"	he has updated them or anything, so I will leave
"	his cudos here, and mirror these macros on my site.
"
"	Doug Renze
"	http://www.avalon.net/~drenze/
"	mailto:drenze@avalon.net
"
"	Typically you will put this file into your
"	home directory like so: ~/.exrc
"
"	Current location:
"	https://jasonmurray.org/
"
"
" General Markup Tags
"
"	Comment Tag
map! ;cm <!--  -->Bhi
"
"	A HREF	Anchor Hyperlink	HTML 2.0
map! ;ah <A HREF=""></A>?"
i
"
"	A NAME	Named Anchor		HTML 2.0
map! ;an <A NAME=""></A>?"
i
"
"	ABBREV	Abbreviation		HTML 3.0
"map! ;ab <ABBREV></ABBREV>bhhi
"
"	ACRONYM				HTML 3.0
"map! ;ac <ACRONYM></ACRONYM>bhhi
"
"	ADDRESS				HTML 2.0
map! ;ad <ADDRESS></ADDRESS>bhhi
"
"	AU	Author			HTML 3.0
"map! ;au <AU></AU>bhhi
"
"	B	Boldfaced Text		HTML 2.0
"map! ;bo <B></B>hhhi
"
"	BANNER				HTML 3.0
"map! ;ba <BANNER></BANNER>bhhi
"
"	BASE				HTML 2.0	HEADER
map! ;bh <BASE HREF="">hi
"
"	BASEFONT			NETSCAPE
"map! ;bf <BASEFONT SIZE=>i
"
"	BIG				HTML 3.0
"map! ;bi <BIG></BIG>bhhi
"
"	BLOCKQUOTE			HTML 2.0
map! ;bl <BLOCKQUOTE>
</BLOCKQUOTE>O
"
"	BODY				HTML 2.0
map! ;bd <BODY>
</BODY>O
"
"	BQ	Blockquote		HTML 3.0
"map! ;bq <BQ></BQ>bhhi
"
"	BR	Line break		HTML 2.0
map! ;br <BR>
"
"	CAPTION				HTML 3.0
"map! ;ca <CAPTION></CAPTION>bhhi
"
"	CENTER				NETSCAPE
map! ;ce <CENTER></CENTER>bhhi
"
"	CITE				HTML 2.0
map! ;ci <CITE></CITE>bhhi
"
"	CODE				HTML 2.0
map! ;co <CODE></CODE>bhhi
"
"	CREDIT				HTML 3.0
"map! ;cr <CREDIT></CREDIT>bhhi
"
"	DEFINITION LIST COMPONENTS	HTML 2.0
"		DL	Definition List
"		DT	Definition Term
"		DD	Definition Body
map! ;dl <DL>
</DL>O	
map! ;dt <DT>
map! ;dd <DD>
"
"	DEL	Deleted Text		HTML 3.0
"map! ;de <DEL></DEL>bhhi
"
"	DFN	Defining Instance	HTML 3.0
"map! ;df <DFN></DFN>bhhi
"
"	DIR	Directory List		HTML 3.0
"map! ;di <DIR>
</DIR>O	
"
"	DIV	Document Division	HTML 3.0
"map! ;dv <DIV></DIV>bhhi
"
"	EM	Emphasize		HTML 2.0
map! ;em <EM></EM>bhhi
"
"	FIG	Figure			HTML 3.0
"map! ;fi <FIG SRC=""></FIG>?"
i
"
"	FN	Footnote		HTML 3.0
"map! ;fn <FN></FN>bhhi
"
"	FONT				NETSCAPE
"map! ;fo <FONT SIZE=></FONT>bhhhi
"
"	HEADERS, LEVELS 1-6		HTML 2.0
map! ;h1 <H1></H1>bhhi
map! ;h2 <H2></H2>bhhi
map! ;h3 <H3 ALIGN=CENTER></H3>bhhi
map! ;h4 <H4 ALIGN=CENTER></H4>bhhi
map! ;h5 <H5 ALIGN=CENTER></H5>bhhi
map! ;h6 <H6 ALIGN=CENTER></H6>bhhi
"
"	HEAD				HTML 2.0
map! ;he <HEAD>
</HEAD>O
"
"	HR	Horizontal Rule		HTML 2.0 W/NETSCAPISM
map! ;hr <HR WIDTH=100%>
"
"	HTML				HTML 3.0
map! ;ht <HTML>
</HTML>O
"
"	I	Italicized Text		HTML 2.0
"map! ;it <I></I>hhhi
"
"	IMG	Image			HTML 2.0
map! ;im <IMG SRC="" ALT="">Bhhi
"
"	INS	Inserted Text		HTML 3.0
"map! ;in <INS></INS>bhhi
"
"	ISINDEX	Identifies Index	HTML 2.0
map! ;ii <ISINDEX>
"
"	KBD	Keyboard Text		HTML 2.0
map! ;kb <KBD></KBD>bhhi
"
"	LANG	Language Context	HTML 3.0
"map! ;la <LANG LANG=""></LANG>?"
i
"
"	LI	List Item		HTML 2.0
map! ;li <LI>
"
"	LINK				HTML 2.0	HEADER
map! ;lk <LINK HREF="">hi
"
"	LH	List Header		HTML 2.0
map! ;lh <LH></LH>bhhi
"
"	MENU				HTML 2.0
map! ;mu <MENU>
</MENU>O	
"
"	META	Meta Information	HTML 2.0	HEADER
map! ;me <META NAME="" CONTENT="">Bhhi
"
"	NOBR	No Break		NETSCAPE
"map! ;nb <NOBR></NOBR>bhhi
"	NOTE				HTML 3.0
"map! ;no <NOTE></NOTE>bhhi
"
"	OL	Ordered List		HTML 3.0
map! ;ol <OL>
</OL>O	
"
"	OVERLAY	Overlay Image		HTML 3.0
"map! ;ov <OVERLAY SRC="">hi
"
"	P	Paragraph		HTML 3.0
map! ;pp <P></P>hhhi
"
"	PRE	Preformatted Text	HTML 2.0
map! ;pr <PRE>
</PRE>O
"
"	Q	Quote			HTML 3.0
"map! ;qu <Q></Q>hhhi
"
"	RANGE				HTML 3.0
"map! ;ra <RANGE FROM= UNTIL=>Bhi
"
"	S	Strikethrough		HTML 3.0
"map! ;sk <S></S>hhhi
"	SAMP	Sample Text		HTML 2.0
map! ;sa <SAMP></SAMP>bhhi
"	SMALL	Small Text		HTML 3.0
"map! ;sm <SMALL></SMALL>bhhi
"	SPOT				HTML 3.0
"map! ;sp <SPOT ID=>i
"
"	STRONG				HTML 2.0
map! ;st <STRONG></STRONG>bhhi
"	STYLE				HTML 3.0
"map! ;sn <STYLE NOTATION="">
</STYLE>k/"
a
"	SUB	Subscript		HTML 3.0
"map! ;sb <SUB></SUB>bhhi
"	SUP	Superscript		HTML 3.0
"map! ;sp <SUP></SUP>bhhi
"
"	TAB				HTML 3.0
"map! ;ta <TAB>
"	TITLE				HTML 2.0	HEADER
map! ;ti <TITLE></TITLE>bhhi
"	TT	Teletype Text		HTML 2.0
"map! ;tt <TT></TT>bhhi
"
"	U	Underlined Text		HTML 2.0
"map! ;uu <U></U>hhhi
"	UL	Unordered List		HTML 2.0
map! ;ul <UL>
</UL>O	
"
"	V	Variable		HTML 3.0
"map! ;vv <V></V>hhhi
"
"	WBR	Word Break		NETSCAPE
"map! ;wb <WBR>
" 	Special Characters
map! ;& &amp;
map! ;K &copy;
map! ;" &quot;
map! ;< &lt;
map! ;> &gt;
```